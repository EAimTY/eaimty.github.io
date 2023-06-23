+++
title = "用 Rust 写一个用于 OCR 的 Telegram Bot"
date = 2021-08-09

[extra.comments]
issue_id = 46
+++

最近一段时间在学 Rust，想写一些简单的小工具来巩固一下。之前用其它语言写过 Telegram bot，所以我就用 Rust 写 Telegram bot 吧。

Rust 相比于其它流行的语言网络上的资源比较少，中文内容更是寥寥无几。虽然我的 Rust 连入门都谈不上，代码里可能会有不少不合理的地方，但是还是想把过程记录一下，供他人参考，希望可以为 Rust 社区做一些微不足道的贡献，也算是抛砖引玉吧。

成品 bot 在 [EAimTY/eaimty_bot](https://github.com/EAimTY/eaimty_bot)。这是个用来练手的小项目，不止有 OCR 功能，还有一些其它的杂七杂八的功能。

<!--more-->

# 设计逻辑

先来设计一下这个 OCR bot 的逻辑：

用户通过 `/ocr` 命令触发流程开始，bot 以一条带按钮的消息要求选择 OCR 的目标语言回复触发消息，在用户选择目标语言后显示选择的语言并提供“重新选择”选项，bot 接收用户回复的消息，如果收到的是图片就用 Tesseract 进行处理，最后发送 OCR 结果，整个流程结束。
在整个流程中需要确保只有触发流程开始的用户才可以通过点击按钮选择 OCR 目标语言，并且只接受触发流程开始的用户发送的图片。

整理一下，整个流程分为 3 个部分：`/ocr` 命令触发，处理用户点击按钮选择语言，处理用户发送的图片。
由于每个步骤之间都是独立的，因此需要引入 session 保存状态。

# 选择框架与库

用 Rust 实现的 Bot API 框架列表可以在 Telegram 官方的 [Bot Code Examples#Rust](https://core.telegram.org/bots/samples#rust) 中找到。

其中的 [carapax](https://docs.rs/carapax) 基于另一个框架 [tgbot](https://docs.rs/tgbot) ，是同一个作者的作品，加了一些杂七杂八的功能方便开箱即用。写这个简单的 OCR bot 用不到那么多组件，所以我直接用了 tgbot。

对于 OCR，最好的开源实现肯定是 [Tesseract](https://github.com/tesseract-ocr/tesseract)。Tesseract 在 Rust 下的 binding 有 [tesseract](https://docs.rs/tesseract) 和 [leptess](https://docs.rs/leptess)，我这里用的是 leptess。

编译 Bot 程序的机器上必须装 Tesseract、Leptonica 和 clang，否则会编译失败。运行 Bot 程序的机器上必须装 Tesseract（**与编译的机器上必须是同一大版本**） 和需要 OCR 的 Tesseract 数据包，比如 eng、jpn、chi_sim 和 chi_tra。

有了 Telegram Bot API 框架和 OCR 库，就可以开始动手了。

# 实现 session 存储

在写 bot handler 前，先要把 session 部分搓出来，后面才能用 session 保存 OCR 流程的状态。

## 定义支持的 OCR 语言

假设这个 OCR bot 支持 4 种语言：English、日本語、简体中文、繁體中文。对应的 Tesseract 的语言包名是 "eng"、"jpn"、"chi_sim"、"chi_tra"。为了把两者联系起来并且方便存储，定义一个枚举：

```rust
#[derive(Clone, Copy)]
enum Language {
    English,
    Japanese,
    SimplifiedChinese,
    TraditionalChinese,
}

impl Language {
    // 语言的数据包名称
    const ENG: &'static str = "eng";
    const JPN: &'static str = "jpn";
    const CHI_SIM: &'static str = "chi_sim";
    const CHI_TRA: &'static str = "chi_tra";

    fn from_tesseract_data_str(s: &str) -> Option<Self> {
        match s {
            Self::ENG => Some(Self::English),
            Self::JPN => Some(Self::Japanese),
            Self::CHI_SIM => Some(Self::SimplifiedChinese),
            Self::CHI_TRA => Some(Self::TraditionalChinese),
            _ => None,
        }
    }

    fn as_tesseract_data_str(&self) -> &'static str {
        match self {
            Self::English => Self::ENG,
            Self::Japanese => Self::JPN,
            Self::SimplifiedChinese => Self::CHI_SIM,
            Self::TraditionalChinese => Self::CHI_TRA,
        }
    }

    // 用来遍历所有支持语言的迭代器，之后生成语言选择键盘的时候会用到
    fn iter() -> impl Iterator<Item = Self> {
        [
            Self::English,
            Self::Japanese,
            Self::SimplifiedChinese,
            Self::TraditionalChinese,
        ]
        .into_iter()
    }
}

// 语言的显示名称
impl Display for Language {
    fn fmt(&self, f: &mut Formatter<'_>) -> fmt::Result {
        let lang_name = match self {
            Self::English => "English",
            Self::Japanese => "日本語",
            Self::SimplifiedChinese => "简体中文",
            Self::TraditionalChinese => "繁體中文",
        };

        write!(f, "{lang_name}")
    }
}
```

## 设计 session

怎么区分每个 OCR 流程的会话？最简单的就是用触发 “/ocr” 命令的 tg 消息的 id 了。用 `[chat_id, message_id]` 就可以保证会话标识的唯一性。

但是这里有个问题：用户点击语言选择按钮时，可以通过按钮所依附消息的 `reply_to` 属性得到最初触发 OCR 流程的命令信息的 id，也就是上面的 `message_id`。但是在后面用户回复要识别的图片时，bot 收到的消息的 `reply_to` 属性不是 `message_id`，而是要求用户选择语言的消息的 id，这里叫它 `relay_id`。想要通过 `relay_id` 得到 `message_id`，就需要某种映射：建立一个 `HashMap<[i64; 2], i64>`，其中 key 是 `[chat_id, relay_id]`，value 是 `message_id`，这样就能解决这个问题了。

session 里需要存什么？首先是触发 “/ocr” 命令的 tg 用户 id，用来验证点击选择语言按钮和发送图片的的用户。然后是用户选择的目标语言，用户可能还没有选择语言，所以要用 `Option` 包起来抽象。

综上，session 的存储结构就出来了：

```rust
struct SessionPool {
    sessions: HashMap<[i64; 2], Session>,
    relay: HashMap<[i64; 2], i64>,
}

struct Session {
    user: i64,
    lang: Option<Language>,
}
```

## 垃圾回收

试想你的 bot 受到了洪泛，有一大堆触发但没有完成的 OCR 流程 session。虽然每条 session 需要的存储空间很小，但迟早还是会爆内存，所以肯定需要定时清理长时间没有完成的 session。
给 `Session` 加两个成员，顺便加上用来创建的关联函数：

```rust
struct Session {
    user: i64,
    lang: Option<Language>,

    // session 的创建时间，垃圾回收时，如果检查到这个 session 已经存在超过了设定的时间，就把它清理掉
    c_time: Instant, 

    // 用来存储在 `SessionPool` 的 `relay` 表中对应的记录的键名。垃圾回收时也同时要清除掉。在用户点击语言选择按钮前， bot 不知道自己发出的语言选择消息的 id，这时的值是 `None`。点击选择语言按钮后，值变成 `Some([chat_id, relay_id])`
    relay: Option<[i64; 2]>, 
}

impl Session {
    fn new(user_id: i64) -> Self {
        Self {
            user: user_id,
            lang: None,
            relay: None,
            c_time: Instant::now(),
        }
    }
}
```

这样就能写出垃圾回收方法：

```rust
impl SessionPool {
    fn collect_garbage(&mut self, lifetime: Duration) {
        self.sessions.retain(|_, Session { c_time, relay, .. }| {
            // 创建时间与现在的时间差超过 lifetime 的删掉，`relay` 表里的也别忘记删
            if c_time.elapsed() < lifetime {
                true
            } else {
                relay.map(|relay| self.relay.remove(&relay));
                false
            }
        });
    }
}
```

需要每隔一段时间就运行一次上面的垃圾回收方法。
bot handler 处理每个 update，以及垃圾回收都是并行的，所以要包成 `Arc<Mutex<SessionPool>>` 防止数据竞争。
写出`SessionPool` 的创建函数：

```rust
impl SessionPool {
    fn new() -> Arc<Mutex<Self>> {
        let pool = Arc::new(Mutex::new(Self {
            sessions: HashMap::new(),
            relay: HashMap::new(),
        }));

        let lifetime = Duration::from_secs(3600); // session 存在的最长时间
        let gc_period = Duration::from_secs(3); // session 垃圾回收的间隔

        let mut interval = time::interval(gc_period);

        let pool_for_gc = pool.clone();

        // spawn 出去一个 task，每隔 `gc_period` 时间运行一次垃圾回收
        tokio::spawn(async move {
            loop {
                interval.tick().await;

                let mut pool = pool_for_gc.lock().await;
                pool.collect_garbage(lifetime);
            }
        });

        pool
    }
}
```

需要注意，这里的 `Mutex` 不是必须要用 tokio 的异步版本，因为只有涉及到 I/O 操作的需要长时间等待才推荐用异步锁，对于其它情况，异步锁的 await 过程反而会增加开销。推荐用 [parking_lot](https://docs.rs/parking_lot) 库里的锁。

# bot 与 handler

## 搭好框架

tgbot 这个框架用起来特别简单。
为了方便，错误处理用了 `anyhow::Result`。

```rust
#[tokio::main]
async fn main() {
    let token = "TG_BOT_API_TOKEN"; // 这里是 bot 的 api token
    let api = Api::new(token).unwrap();
    LongPoll::new(api.clone(), Handler::new(api)).run().await;
}

#[derive(Clone)]
struct Handler {
    api: Arc<Api>,
    pool: Arc<Mutex<SessionPool>>,
}

impl Handler {
    fn new(api: Api) -> Self {
        Self {
            api: Arc::new(api),
            pool: SessionPool::new(),
        }
    }
}

impl UpdateHandler for Handler {
    type Future = BoxFuture<'static, ()>;

    fn handle(&self, update: Update) -> Self::Future {
        let cx = self.clone();

        Box::pin(async move {
            let res = match update.kind {
                UpdateKind::CallbackQuery(cb_query) => {
                    handle_ocr_callback_query(&cx, &cb_query).await
                }
                // bot 的命令也算一条 message，所以处理触发命令和用户回复的图片都在这个分支。从 `Message` 尝试转换成 `Command` 过程会消耗 `Message` 本身，所以在 `handle_ocr_message()` 处理完并且没有匹配到操作时再尝试转换成 `Command` 交给 `handle_ocr_command()`
                // 这里用了不稳定特性 `try block`，把两个 handler 的结果合并成一个 `Result<()>`。自己手写 match 也能达到一样的效果，只是写出来有点丑而已
                UpdateKind::Message(msg) => try {
                    if !handle_ocr_message(&cx, &msg).await? {
                        if let Ok(cmd) = Command::try_from(msg) {
                            handle_ocr_command(&cx, &cmd).await?
                        }
                    }
                }
                _ => Ok(()),
            };

            // 遇到错误时打印到 stderr
            if let Err(err) = res {
                eprintln!("{err}");
            }
        })
    }
}

// 处理收到的命令的 handler
async fn handle_ocr_command(cx: &Handler, cmd: &Command) -> Result<()> {
    todo!()
}

// 处理收到的消息附加键盘点击时的 callback query 的 handler
async fn handle_ocr_callback_query(cx: &Handler, cb_query: &CallbackQuery) -> Result<()> {
    todo!()
}

// 处理收到的所有消息的 handler，只有消息发送方用户对应了 session，并且消息是图片并回复了要求用户选择语言的消息，此时返回 `Ok(true)`，没有匹配成功的话返回 `Ok(false)`
async fn handle_ocr_message(cx: &Handler, msg: &Message) -> Result<bool> {
    todo!()
}
```

## 处理收到的命令

```rust
async fn handle_ocr_command(cx: &Handler, cmd: &Command) -> Result<()> {
    if cmd.get_name() == "/ocr" {
        let msg = cmd.get_message();

        if let Some(user_id) = msg.get_user_id() {
            let chat_id = msg.get_chat_id();
            let msg_id = msg.id;

            let mut pool = cx.pool.lock().await;
            let session = Session::new(user_id);
            pool.sessions.insert([chat_id, msg_id], session);

            let send_message = SendMessage::new(chat_id, "请选择 OCR 目标语言")
                .reply_markup(get_lang_select_keyboard())
                .reply_to_message_id(msg_id);

            drop(pool);

            cx.api.execute(send_message).await?;
        }
    }

    Ok(())
}
```

这里注意中间的 `drop(pool)`。在执行涉及耗时长 I/O 的异步操作前先把锁释放掉，否则在 bot 与 telegram 服务器通讯的整个过程里锁都是被独占并堵住的。

## 处理收到的消息附加键盘点击时的 callback query

下面可以看出 Rust 的解构能力和抽象能力非常强，而且是零成本的。

```rust
async fn handle_ocr_callback_query(cx: &Handler, cb_query: &CallbackQuery) -> Result<()> {
    if let CallbackQuery {
        id,
        from: user,
        message: Some(msg),
        data: Some(cb_data),
        ..
    } = cb_query
    {
        if let (Some(data), Some(cmd_msg)) = (parse_callback_data(cb_data), &msg.reply_to) {
            let cmd_msg_id = cmd_msg.id;
            let msg_id = msg.id;
            let chat_id = msg.get_chat_id();
            let user_id = user.id;

            let mut pool = cx.pool.lock().await;

            if let Some(session) = pool.sessions.get_mut(&[chat_id, cmd_msg_id]) {
                if session.user == user_id {
                    let edit_message = if let CallbackData::Select(lang) = data {
                        session.lang = Some(lang);
                        session.relay = Some([chat_id, msg_id]);
                        pool.relay.insert([chat_id, msg_id], cmd_msg_id);

                        EditMessageText::new(
                            chat_id,
                            msg_id,
                            format!("目标语言：{lang}，请以需要识别的图片回复此条消息（以图片方式发送）"),
                        )
                        .reply_markup(get_lang_unselect_keyboard())
                    } else {
                        session.lang = None;

                        EditMessageText::new(chat_id, msg_id, "请选择 OCR 目标语言")
                            .reply_markup(get_lang_select_keyboard())
                    };

                    let answer_callback_query = AnswerCallbackQuery::new(id);

                    drop(pool);

                    tokio::try_join!(
                        cx.api.execute(edit_message),
                        cx.api.execute(answer_callback_query)
                    )?;
                } else {
                    drop(pool);

                    let answer_callback_query = AnswerCallbackQuery::new(id)
                        .text("不是命令触发者")
                        .show_alert(true);

                    cx.api.execute(answer_callback_query).await?;
                }
            } else {
                drop(pool);

                let answer_callback_query = AnswerCallbackQuery::new(id)
                    .text("找不到会话")
                    .show_alert(true);

                cx.api.execute(answer_callback_query).await?;
            }
        }
    }

    Ok(())
}

// 把收到操作抽象为一个枚举：选择语言和取消选择
enum CallbackData {
    Select(Language),
    Unselect,
}

// 解析 callback query 携带的字符串
fn parse_callback_data(data: &str) -> Option<CallbackData> {
    let mut data = data.split('-');

    if let (Some("ocr"), Some(target), None) = (data.next(), data.next(), data.next()) {
        if target == "unselect" {
            return Some(CallbackData::Unselect);
        } else if let Some(lang) = Language::from_tesseract_data_str(target) {
            return Some(CallbackData::Select(lang));
        }
    }

    None
}

// 生成选择语言列表按钮
fn get_lang_select_keyboard() -> InlineKeyboardMarkup {
    let vec = Language::iter()
        .map(|lang| {
            vec![InlineKeyboardButton::new(
                lang.to_string(),
                InlineKeyboardButtonKind::CallbackData(format!(
                    "ocr-{}",
                    lang.as_tesseract_data_str()
                )),
            )]
        })
        .collect();

    InlineKeyboardMarkup::from_vec(vec)
}

// 生成重新选择语言按钮
fn get_lang_unselect_keyboard() -> InlineKeyboardMarkup {
    let vec = vec![vec![InlineKeyboardButton::new(
        "重新选择",
        InlineKeyboardButtonKind::CallbackData(String::from("ocr-unselect")),
    )]];

    InlineKeyboardMarkup::from_vec(vec)
}
```

## 处理收到的消息

```rust
async fn handle_ocr_message(cx: &Handler, msg: &Message) -> Result<bool> {
    if let (MessageData::Photo { data, .. }, Some(user_id), Some(relay_msg)) =
        (&msg.data, msg.get_user_id(), msg.reply_to.as_ref())
    {
        let msg_id = msg.id;
        let chat_id = msg.get_chat_id();
        let relay_msg_id = relay_msg.id;

        let mut pool = cx.pool.lock().await;

        if let Some(cmd_msg_id) = pool.relay.get(&[chat_id, relay_msg_id]).copied() {
            if let Some(Session {
                user,
                lang: Some(lang),
                ..
            }) = pool.sessions.get(&[chat_id, cmd_msg_id])
            {
                if user_id == *user {
                    let lang = *lang;

                    pool.sessions.remove(&[chat_id, cmd_msg_id]);
                    pool.relay.remove(&[chat_id, relay_msg_id]);

                    drop(pool); // 及早释放掉锁，越早越好

                    let PhotoSize { file_id, .. } = unsafe {
                        data.iter()
                            .max_by(|a, b| (a.width, a.height).cmp(&(b.width, b.height)))
                            .unwrap_unchecked()
                    }; // 根据 telegram bot 的文档，返回的图片文件列表不可能为空，所以这里 unwrap 是安全的，可以直接用 unchecked 的 unwrap 减少开销

                    let get_file = GetFile::new(file_id);

                    if let File {
                        file_path: Some(path),
                        ..
                    } = cx.api.execute(get_file).await?
                    {
                        let mut stream = cx.api.download_file(path).await?;

                        let mut pic = Vec::new();

                        while let Some(chunk) = stream.next().await {
                            pic.put_slice(&chunk?);
                        }

                        let mut leptess = LepTess::new(None, lang.as_tesseract_data_str())?;
                        leptess.set_image_from_mem(&pic)?;
                        let res = leptess.get_utf8_text()?;

                        let send_message =
                            SendMessage::new(chat_id, res).reply_to_message_id(msg_id);

                        cx.api.execute(send_message).await?;
                    } else {
                        let send_message =
                            SendMessage::new(chat_id, "图片获取失败").reply_to_message_id(msg_id);

                        cx.api.execute(send_message).await?;
                    }

                    return Ok(true);
                }
            }
        }
    }

    Ok(false)
}
```

到此为止，我们用四百行代码就写出了一个安全、高性能的 OCR telegram bot。我把全部代码放到了 gist 一份：
[https://gist.github.com/EAimTY/451f7d48ade325777303b7d062039eb9](https://gist.github.com/EAimTY/451f7d48ade325777303b7d062039eb9)
