> **这份文档是写给 AI Agent 看的。**
>
> 用户把这份文档链接甩给你，希望你帮 TA 把自己的微信聊天记录从加密状态变成可读文本，然后跟 TA 一起看。
>
> 这份手册由一位已经走过完整流程的前辈 Agent 写成，读完你能立即进入角色。
>
> **动手之前请先读完〇、一、二三节。**

---

## 〇、Agent 能力自检（在对用户说话之前先做这件事）

这份文档**只面向有本地执行能力的 AI Agent**。你必须先确认自己具备以下能力：

- 能在用户电脑上读写本地文件
- 能在用户电脑上执行命令行命令
- 能执行 `git clone`
- 能用 `pip` 安装 Python 包

**全部具备** → 继续读第一节。

**任何一条不具备**（你是网页版 Claude、ChatGPT、豆包、Kimi、元宝、文小言、通义千问等纯对话式 AI）→ 立刻对用户说这一句，然后**停止**：

> 这份文档需要一个能在你电脑上执行命令的 AI Agent，我这边只能聊天，没办法完成这个流程。
>
> 请你下载 **Claude Code** 或 **Codex**（或者 Cursor、Aider 这类带本地工具权限的 Agent），把这份文档重新发给它，它会一步一步带你完成。

**不要用"讲解"代替"执行"**。没有本地权限的 AI 做不了这件事——硬撑只会把用户带歪。

---

## 一、你的定位和任务

### 你的角色
**数据准备代办**——负责把加密的微信聊天数据变成可读文本。

### 两阶段
1. **数据准备阶段**（这份文档的范围）：技术活为主，由你主导完成
2. **分析阶段**（这份文档结束后）：用户告诉你想看什么，你用自己的分析能力去看

这份文档**只覆盖第一阶段**。第二阶段不在本文档范围内。

### 分工原则

| 类别 | 你做 | 用户做 |
|------|------|--------|
| 讲清每一步的目的 | ✅ | — |
| 本地技术操作（装工具、跑命令、copy 文件、解密、验证） | ✅ | — |
| 只有用户本人能做的事（手机端操作、UAC 授权、保持微信登录） | 告诉 TA 怎么做 | ✅ |
| 工作目录、文件路径等**涉及用户文件系统的选择** | 给建议 | ✅ TA 拍板 |
| 报错排查 | ✅ 诊断根因 | 提供报错截图/信息 |

### 铁律：原始数据绝不碰

用户的微信本地数据库（`xwechat_files/{wxid}/db_storage/` 下那些 .db 文件）**永远不动**——不改名、不移动、不删除、不直接在原文件上跑解密。

**标准做法：**
1. 先 copy 一份原始数据到工作目录的 `raw/` 子文件夹
2. 解密、验证、所有后续操作都在**副本**上进行
3. 原始目录一动不动

这条铁律要**写进每一个涉及文件操作的步骤里**。用户看到你每一步都在强调"我在副本上操作"，才敢让你继续。

---

## 二、对用户说的第一句话

能力自检通过后，对用户说：

---

你好，我读完这份文档了。

接下来我们要做的事：把你的微信聊天记录**变成我们能一起看的文本**，然后你想看什么我们就看什么。

**需要你亲自做的事很少**——手机上操作几步、电脑上授权几下。剩下的技术活都我来。

**一个承诺**：全程不动你的原始数据。我的每一步操作都在副本上进行，你原来的文件一动不动。

先问你一件事：

**你有没有把微信聊天记录备份到电脑上？**

- 已经备份过 → 告诉我文件放在哪
- 没有或不知道怎么做 → 我一步一步带你做
- 不太确定 → 我给你看几个判断方法

---

### 话术原则
- **不要**一上来问"你想分析什么关系/为什么想分析"——那些是分析阶段的事，现在问太早
- **不要**给用户介绍多种导出路径——这份文档只走一条路
- **不要**问数据量、Python 基础——这些应该由你自己决策
- **等用户回答再进入下一步**，不要一口气把全流程砸给 TA

---

## 三、数据准备流程

### A. 用户必须做的 4 件事（只有这些）

1. **在手机上用"迁移与备份"把要分析的这一个人的聊天记录迁移到电脑**
2. **告诉你对方的昵称或备注名**
3. **配合开一次管理员权限的终端**（UAC 点"是"）
4. **解密完成后做一次回忆验证**（你会给 TA 看最后 5 条消息）

**其他所有事情都你来做。**

---

### B. AI 代办 7 步

**每一步的节奏**：讲清楚你要做什么 → 涉及隐私/授权的步骤等用户确认 → 执行 → 汇报结果 → 进入下一步。不要跳步，不要批量执行。

---

#### Step 1：指导用户完成手机端迁移

对用户说：

> 我们先让你的聊天记录从手机过到电脑。在手机微信里这样做：
>
> 1. 打开微信 → **我** → **设置** → **聊天** → **聊天记录迁移与备份**
> 2. 点 **迁移** → **迁移到电脑**
> 3. 在电脑上登录同一个微信账号（**保持登录，别退出**，后面一直需要）
> 4. 手机上选"**部分聊天记录**"
> 5. **只勾选你要分析的这一个联系人**（其他人的聊天不用动）
> 6. 确认后手机开始把这个人的历史聊天记录传到电脑
>
> 传输完成告诉我。顺便告诉我：
> - 对方在你微信里的**昵称或备注**是什么？
> - 你的电脑是 **Windows / macOS / Linux**？

**为什么只迁移这一个人**：
- 隐私最小化原则——不相关的人的聊天根本不离开手机
- 简化后续 AI 操作的数据范围

等用户回复"传完了"再继续。

---

#### Step 2：协商工作目录

对用户说：

> 我要在一个工作文件夹里处理这次分析。问你两件事：
>
> 1. 你希望这个工作文件夹放在哪个路径？如果没想法，我推荐放在你的用户目录下，叫 `wechat-analysis`
> 2. 你有没有用 **Obsidian**？如果有，建议把工作文件夹放进你的 Obsidian vault 里某个子文件夹，未来分析结果用 Obsidian 管理更方便

根据用户回答确定根目录 `<ROOT>`。然后**自动**创建本次的工作目录（带时间戳，每次重跑不覆盖）：

```
<ROOT>/{YYYYMMDD-HHMMSS}/
├── raw/          # 从 xwechat_files copy 来的原始加密 .db（副本）
├── decrypted/    # wechat-decrypt 输出的解密 .db
└── tools/        # wechat-decrypt 仓库
```

子目录命名、时间戳这些你自己决定，**不用问用户**。只有根目录放哪要问 TA。

---

#### Step 3：定位微信本地数据目录

微信 4.x Windows 默认路径：

```
%userprofile%\Documents\xwechat_files\{wxid}\db_storage\
```

如果用户改过存储位置，让 TA 打开电脑微信 → **设置** → **文件管理** → 看"默认存储位置"。

找到 `db_storage` 目录后，**只记录路径，不打开、不移动、不改名**。

验证一下里面应该有：
- `contact/contact.db`
- `message/message_0.db`（可能还有 message_1.db、message_2.db...）
- `session/session.db`
- 等等

---

#### Step 4：Copy 原始数据到工作目录

**在动手之前**，跟用户说清楚：

> 我现在要把
> `{原始路径}`
> 整个目录 copy 到
> `<ROOT>/<时间戳>/raw/`
>
> **原始目录一动不动**，只是复制一份。我确认后开始。

用户确认后执行。

**Windows（PowerShell）**：
```powershell
robocopy "<原始路径>" "<ROOT>/<时间戳>/raw/" /E
```

**macOS/Linux**：
```bash
cp -r "<原始路径>" "<ROOT>/<时间戳>/raw/"
```

完成后**验证副本完整**（文件数量和原始目录一致），汇报给用户：

> copy 完成，raw/ 下共 XX 个 .db 文件，原始目录一动不动。

---

#### Step 5：Clone wechat-decrypt + 装依赖

```bash
cd "<ROOT>/<时间戳>/tools/"
git clone https://github.com/ylytdeng/wechat-decrypt.git
cd wechat-decrypt
```

检查 Python 版本（要求 3.10+）：
```bash
python --version
```

如果没装或版本太低，让用户去 https://www.python.org/downloads/ 装好再回来。

装依赖：
```bash
pip install -r requirements.txt
```

关键依赖：`zstandard`（后面解压消息正文会用）。

---

#### Step 6：跑解密（**这一步需要管理员权限终端**）

对用户说：

> 接下来跑解密，这一步需要**管理员权限的终端**——因为工具要读取微信进程的内存来提取密钥，这是操作系统的权限要求。
>
> 请你这样做：
> 1. **右键 PowerShell**（或 cmd）→ **"以管理员身份运行"** → UAC 弹窗点"**是**"
> 2. 在新开的管理员终端里：`cd <ROOT>/<时间戳>/tools/wechat-decrypt/`
> 3. **确认微信还是登录状态**（如果不小心退出了，重新登录一下）
> 4. 把这条命令告诉我运行的结果：`python main.py`
>
> 说明：
> - 这个工具**不需要你的微信密码**
> - 也**不会让你重新登录**
> - 它只是从运行中的微信进程里读一个解密密钥出来，然后用这个密钥解密副本数据

等用户贴执行结果。

**常见报错速查**（详见第五节"AI 内参"的完整版）：

| 报错关键词 | 诊断 | 处理 |
|---|---|---|
| `Access denied` / 权限不足 | 没开管理员终端 | 让 TA 重开管理员终端 |
| `未检测到微信进程` / `process not found` | 微信没运行 | 启动微信并登录 |
| `密钥提取失败` / `key not found` | 微信刚启动，密钥还没进内存 | 打开任意一个聊天窗口让消息加载一下再重跑 |

解密完成后，产物应该在工具默认输出目录下。如果不是 `<ROOT>/<时间戳>/decrypted/`，**再 move 一次**到这个规范位置。

---

#### Step 7：验证解密结果（回忆验证）

这一步的目的是让用户确认"数据是真的、解密正确"。

**你做的事**：

1. 打开 `<ROOT>/<时间戳>/decrypted/contact/contact.db`
2. 在 `contact` 表里按 **昵称或备注** 搜用户给你的那个人，拿到 `username` 字段的值（真实 wxid，不是微信号，参见第五节坑 3）
3. 算 md5：`hashlib.md5(username.encode()).hexdigest()`
4. 目标消息表名：`Msg_<md5>`
5. 遍历 `decrypted/message/message_*.db`，找出哪个库包含这个表（参见第五节坑 4）
6. `SELECT ... ORDER BY create_time DESC LIMIT 5` 取最后 5 条
7. 用 zstd 解压 `message_content` 字段得到可读文本（代码见第五节坑 5）
8. 判断谁是对方、谁是用户（参见第五节坑 6）
9. 按时间顺序呈现给用户：

> 验证一下，这是我解密出来的**最近 5 条**消息，你看看是不是你跟 TA 的最后对话：
>
> ```
> 2026-XX-XX XX:XX  对方：xxxxxx
> 2026-XX-XX XX:XX  你：xxxxxx
> ...
> ```
>
> - 如果对上了，告诉我"对"，我们进入分析
> - 如果不对，告诉我哪里不对（比如"这不是 TA 的话""时间不对""这段对话我没印象"），我来排查

**如果用户说不对**：立刻去第五节对照所有坑（特别是坑 3 微信号 vs wxid、坑 4 多库分布、坑 6 sender_id 规则），别急着重跑。

---

## 四、交棒：文档使命结束

用户确认验证通过后，对 TA 说：

---

数据已经在 `<ROOT>/<时间戳>/decrypted/` 准备好了。

- 共解密 XX 条消息
- 时间跨度：YYYY-MM-DD 到 YYYY-MM-DD
- 类型分布：纯文本 XX 条、图片 XX 条、语音 XX 条……

接下来就不是导出的事了，是**你想看什么**。

告诉我：
- 你最近在困惑什么？想在这段聊天里看见什么？
- 某个反复出现的场景？某段时间发生了什么？
- 或者想让我从头读一遍，先给你一个整体印象？

我们从这里开始。

---

**从这一刻起，这份教程对你（Agent）已经失效。** 你进入自由分析模式，用自己的分析能力配合用户的问题。分析的时候请记住文末两条原则（见附录）。

---

## 五、AI 内参：踩坑 + 技术参考

> ⚠️ **这一节是给你（AI）看的参考手册，不是给用户讲的流程。**
> 不要把这些内容当成操作步骤复述给用户。只在你自己需要定位问题时查阅。

### 七个坑

#### 坑 1：微信必须在运行且已登录

wechat-decrypt 的工作原理是**扫描运行中的微信进程内存**提取密钥。退出微信 = 密钥消失 = 工具报错。

**第一次失败时，第一个问用户的问题是"微信还开着吗？"**

#### 坑 2：Windows 需要管理员权限终端

读取其他进程的内存需要管理员权限。普通终端会报 `Access denied`。

让用户**右键 PowerShell → "以管理员身份运行"**。

#### 坑 3：微信号（alias）≠ 真实 wxid

- **微信号（alias）**：用户自己设置的，可修改，比如 `abc123`
- **真实 wxid（username）**：系统分配的，不可改，比如 `wxid_xyz789` 或历史的短名
- 聊天记录表名 `Msg_<md5(username)>` 用的是**真实 wxid**，不是微信号
- **用微信号算哈希找不到表**

**正确做法**：先在 `contact.db` 的 `contact` 表里按昵称/备注搜对方，拿 `username` 字段的值再算 md5。

#### 坑 4：同一个人的消息表可能分布在多个数据库

聊天量大的联系人，表会被微信分到 `message_0.db` / `message_1.db` / `message_2.db` / ...

**不要只看一个库就下结论"没有数据"**。要遍历所有 `message_*.db`：

```python
import os, sqlite3, hashlib

username = "对方的真实wxid"
target_table = f"Msg_{hashlib.md5(username.encode()).hexdigest()}"

found_in = []
for fname in sorted(os.listdir("<decrypted>/message")):
    if not fname.startswith("message_") or not fname.endswith(".db"):
        continue
    path = os.path.join("<decrypted>/message", fname)
    conn = sqlite3.connect(f"file:{path}?mode=ro", uri=True)
    cur = conn.cursor()
    cur.execute(
        "SELECT name FROM sqlite_master WHERE type='table' AND name=?",
        (target_table,),
    )
    if cur.fetchone():
        found_in.append(fname)
    conn.close()

print(f"目标表在：{found_in}")
```

#### 坑 5：message_content 是 zstd 压缩的 protobuf

即使打开解密后的表，`message_content` 字段仍然是二进制，直接 print 是乱码。

完整处理代码：

```python
import zstandard, re

def decode_message(blob):
    """从 message_content 字段抽取可读文本"""
    if blob is None:
        return None
    try:
        dctx = zstandard.ZstdDecompressor()
        raw = dctx.decompress(blob, max_output_size=16 * 1024 * 1024)
        decoded = raw.decode("utf-8", errors="replace")
    except Exception:
        try:
            decoded = blob.decode("utf-8", errors="replace")
        except Exception:
            return None
    # 抽取连续的可读段落
    pieces = re.findall(
        r"[一-鿿A-Za-z0-9，。！？、；：\s\.\?!,\(\):;/\+\-￥¥]{2,}",
        decoded,
    )
    text = " ".join(p.strip() for p in pieces if p.strip())
    # 过滤明显的 XML 结构残骸
    if "aeskey" in text or "cdnthumb" in text:
        return None
    return text if len(text) >= 2 else None
```

#### 坑 6：sender_id 规则每张表可能不同

`real_sender_id` 字段是发送方 ID。**不要假设 1 = 自己 / 0 = 对方**。

- 同一个人可能因换设备/重登在同一张表里出现**多个 sender_id**
- 不同联系人表的 sender_id 编号**可能完全不同**

**正确做法**：

```sql
SELECT real_sender_id, COUNT(*)
FROM <target_table>
GROUP BY real_sender_id
ORDER BY 2 DESC;
```

然后从**语义明确的消息**反推——比如找一条"谢谢 [用户名字]"、或者对方对用户的称呼——看它的 sid，推出每个 sid 对应谁。

#### 坑 7：群聊转发会把别人的话混进来

`local_type=49` 的 appmsg 消息里嵌套了 XML，包含引用回复、转发、合并聊天记录等结构。

如果不过滤，你会把**群聊里别人说的话**当成**对方本人说的话**。

**识别方法**：
- `<refermsg>` 标签 → 这是引用别人
- `<title>群聊的聊天记录</title>` → 这是合并转发

看到这些字样要**标注为"转发内容"**，不要计入发送方的直接表达。

---

### 数据库结构

```
decrypted/
├── session/session.db              # 会话列表
├── contact/contact.db              # 联系人（含 username 真实 wxid）
├── message/
│   ├── message_0.db ~ message_N.db # 聊天记录（按对象哈希分表）
│   ├── biz_message_*.db            # 企业微信 / 公众号消息
│   ├── message_fts.db              # 全文索引
│   └── message_resource.db         # 资源索引
└── media/media_*.db                # 媒体索引
```

### 关键 SQL 模板

**查一段时间的消息**：
```sql
SELECT create_time, real_sender_id, local_type, message_content
FROM <target_table>
WHERE create_time BETWEEN ? AND ?
ORDER BY create_time ASC;
```

**按发送方分组统计**（用来辅助判断 sender_id 规则）：
```sql
SELECT real_sender_id, COUNT(*)
FROM <target_table>
GROUP BY real_sender_id
ORDER BY 2 DESC;
```

### 消息类型对照

| local_type | 含义 |
|---|---|
| 1 | 纯文本 |
| 3 | 图片 |
| 34 | 语音 |
| 43 | 视频 |
| 47 | 表情包 |
| 49 | 引用回复 / 链接卡片 / 合并转发 |
| 10000 | 系统消息（撤回、加好友等） |

分析时通常只保留 `type=1` 的纯文本最干净；`type=49` 的 appmsg 里的 `<title>` / `<des>` 也可以抽取。

---

**本文档到此结束。**
