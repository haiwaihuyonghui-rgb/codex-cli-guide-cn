# OpenAI Codex CLI 国内使用指南（2026）

**直接回答：** Codex CLI 是 OpenAI 官方开源的本地编程助手，在终端里运行。它不单独收费，用 ChatGPT 账号登录（Plus、Pro、Business、Edu、Enterprise 套餐都包含），额度算在会员里；也可以用 API Key 登录，按 API 价格另算。

**安装：** macOS / Linux 运行 `curl -fsSL https://chatgpt.com/codex/install.sh | sh`，也可以用 `npm install -g @openai/codex`；装好后运行 `codex`，选择 **Sign in with ChatGPT**。

**额度：** Plus（$20/月）的 Codex 额度按每 5 小时计算，适合每周几次专注的编程；每天重度使用选 Pro（$100 / $200 / $500），目前没有 5 小时限制。额度不够时可以另外购买 credits。

> 更新日期：2026-10-17。安装命令来自 openai/codex 仓库 README，登录和多账号切换来自 OpenAI 官方 Authentication、Environment variables 文档（2026-10-16 核对），额度和价格来自 OpenAI 官方定价页（2026-10-13 核对），以官方最新说明为准；本仓库非官方，与 OpenAI 没有隶属关系。

## 目录

- [使用前先读：关于地区](#使用前先读关于地区)
- [Codex 包含在哪些套餐里](#codex-包含在哪些套餐里)
- [安装](#安装)
- [用 ChatGPT 账号登录](#用-chatgpt-账号登录)
- [多账号切换（官方方法）](#多账号切换官方方法)
- [上手建议](#上手建议)
- [Plus 和 Pro 的额度差别](#plus-和-pro-的额度差别)
- [点数 credits 说明](#点数-credits-说明)
- [常见报错与解决](#常见报错与解决)
- [和 Claude Code 怎么选](#和-claude-code-怎么选)
- [国内开通参考](#国内开通参考)
- [官方文档](#官方文档)

## 使用前先读：关于地区

OpenAI 官方对 ChatGPT 和 API 都有支持的国家和地区列表，具体以官方页面为准。本指南只整理官方的安装、登录、套餐和报错说明，不提供绕过地区限制的方法；请在符合 OpenAI 服务条款的前提下使用。

## Codex 包含在哪些套餐里

Codex 没有单独的月卡，跟着 ChatGPT 的订阅走。按官方定价页：

| 套餐 | 官方价 | Codex 使用情况 |
|---|---|---|
| 免费版 | $0 | 轻量任务，体验用 |
| Go | $8/月 | 轻量任务 |
| Plus | $20/月 | 额度按每 5 小时估算，可能另有每周限制 |
| Pro | $100 / $200 / $500 每月 | 目前没有 5 小时限制 |
| Business | 每人每月 $20（年付）/ $25（月付） | 标准席位的估算和 Plus 相同 |

Codex 有几种用法，共用同一份额度：终端里的命令行（本文重点）、编辑器插件（[IDE 文档](https://developers.openai.com/codex/ide)）、桌面应用（运行 `codex app`）、网页版（chatgpt.com/codex）。

## 安装

官方 README 提供了多种安装方式，任选一种。

**macOS / Linux：**

```shell
curl -fsSL https://chatgpt.com/codex/install.sh | sh
```

**Windows（PowerShell）：**

```shell
powershell -ExecutionPolicy ByPass -c "irm https://chatgpt.com/codex/install.ps1 | iex"
```

**npm：**

```shell
npm install -g @openai/codex
```

**Homebrew：**

```shell
brew install --cask codex
```

也可以到 [GitHub Releases](https://github.com/openai/codex/releases/latest) 下载对应平台的压缩包（例如 macOS Apple 芯片是 `codex-aarch64-apple-darwin.tar.gz`，Linux x86_64 是 `codex-x86_64-unknown-linux-musl.tar.gz`），解压后把文件改名为 `codex`。

独立安装脚本默认从 `https://releases.openai.com/codex` 下载。如果这个地址下载失败，官方说明可以设置环境变量，让它改从 GitHub Releases 下载：

```shell
curl -fsSL https://chatgpt.com/codex/install.sh | CODEX_INSTALLER_USE_RELEASES_OPENAI_COM=false sh
```

装好后，进入项目目录运行：

```shell
codex
```

更新时，官方 CLI 文档给的命令和安装相同，再运行一次安装脚本即可；用 npm 或 Homebrew 装的，用对应的包管理器升级。

**Windows 用户：** Codex 可以在 Windows 上通过 Windows 沙盒原生运行，也可以装在 WSL2 里。需要 Linux 工具链、仓库本来就在 WSL2 里时，选 WSL2：

```powershell
wsl --install
```

```powershell
wsl
```

然后在 WSL 里运行上面的 Linux 安装命令和 `codex`。官方建议把仓库放在 Linux 家目录（例如 `~/code/my-app`），放在 `/mnt/c/...` 下会更慢，还可能遇到符号链接和权限问题。从 Codex 0.115 起，WSL1 不再支持，请使用 WSL2。

## 用 ChatGPT 账号登录

1. 运行 `codex`，选择 **Sign in with ChatGPT**。
2. 浏览器弹出授权页，登录你开了会员的 ChatGPT 账号并确认。
3. 回到终端就可以开始对话。

官方推荐用 ChatGPT 账号登录，这样 Codex 算在你的 Plus、Pro 等套餐里。也可以用 API Key 登录，但需要额外设置，用量按标准 API 价格计费，并且依赖 ChatGPT 工作区或云服务的功能会受限，个人写代码通常用会员账号更划算。

**登录相关命令：**

- `codex login status`：查看当前登录方式。
- `codex logout`：清除已保存的登录信息。
- 在没有浏览器的远程机器上，官方首选设备码登录：`codex login --device-auth`（测试版，需要先在 ChatGPT 的安全设置里开启设备码登录），然后在任意浏览器里打开提示的链接，输入一次性代码。
- 登录信息缓存在 `~/.codex/auth.json` 或系统凭据库里，包含访问令牌，请当作密码保管，不要发给别人或提交到仓库。

## 多账号切换（官方方法）

Codex 没有官方的"账号切换器"按钮，也不需要装第三方软件。按 OpenAI 官方文档，切换账号有两种办法：偶尔换号用"退出再登录"；经常换号用 `CODEX_HOME` 给每个账号单独一个目录。

**方法一：退出再登录（偶尔换号）**

macOS / Linux 终端、Windows PowerShell 都一样：

```shell
codex logout
codex login
codex login status
```

`codex logout` 清除当前保存的登录信息；`codex login` 会打开浏览器，登录另一个 ChatGPT 账号即可；`codex login status` 查看当前登录方式。如果浏览器里还登着旧账号，先在 chatgpt.com 退出旧账号，再重新运行 `codex login`。

注意：官方说明命令行和编辑器插件共用同一份登录信息，在一边退出，另一边下次启动也要重新登录。

**方法二：用 `CODEX_HOME` 给每个账号一个目录（经常换号）**

官方环境变量文档写明：`CODEX_HOME` 是 Codex 存放配置、登录信息、日志、会话记录的根目录，默认 `~/.codex`；设置它时，目录必须事先存在。默认设置下登录信息保存在这个目录的 `auth.json` 里，所以不同目录就是不同账号，互不覆盖。

macOS / Linux：

```shell
mkdir -p "$HOME/.codex-a" "$HOME/.codex-b"
CODEX_HOME="$HOME/.codex-a" codex login
CODEX_HOME="$HOME/.codex-b" codex login

# 设置 codex-a / codex-b 快捷命令（用 bash 的把三处 ~/.zshrc 换成 ~/.bashrc）
echo 'alias codex-a="CODEX_HOME=$HOME/.codex-a codex"' >> ~/.zshrc
echo 'alias codex-b="CODEX_HOME=$HOME/.codex-b codex"' >> ~/.zshrc
source ~/.zshrc
```

之后输入 `codex-a` 就是 A 账号，`codex-b` 就是 B 账号；直接输入 `codex` 仍然用默认的 `~/.codex`。

Windows PowerShell：

```powershell
New-Item -ItemType Directory -Force "$HOME\.codex-a", "$HOME\.codex-b"
$env:CODEX_HOME = "$HOME\.codex-a"; codex login
$env:CODEX_HOME = "$HOME\.codex-b"; codex login
```

PowerShell 里的 `$env:CODEX_HOME` 只对当前窗口有效：开两个窗口，分别设置成 `.codex-a` 和 `.codex-b`，再各自运行 `codex`，就是两个账号。想要 `codex-a` / `codex-b` 快捷命令，运行 `if (!(Test-Path $PROFILE)) { New-Item -ItemType File -Force $PROFILE }; notepad $PROFILE` 打开 PowerShell 配置文件（没有会自动新建），粘贴下面两行后保存、重开 PowerShell（如果提示禁止运行脚本，就用上面每个窗口设置一次的办法）：

```powershell
function codex-a { $old = $env:CODEX_HOME; $env:CODEX_HOME = "$HOME\.codex-a"; try { codex @args } finally { $env:CODEX_HOME = $old } }
function codex-b { $old = $env:CODEX_HOME; $env:CODEX_HOME = "$HOME\.codex-b"; try { codex @args } finally { $env:CODEX_HOME = $old } }
```

几点说明：

- 每个目录是独立的一套配置和会话记录。想沿用原来的设置，只复制 `~/.codex/config.toml` 过去，**不要复制 `auth.json`**。
- 官方配置项 `cli_auth_credentials_store` 默认是 `file`（存到各目录的 `auth.json`）；如果改成了 `keyring` 或 `auto`（存进系统凭据库），多账号的表现以官方文档为准。
- API Key 账号：`printenv OPENAI_API_KEY | codex login --with-api-key`（PowerShell 写成 `$env:OPENAI_API_KEY | codex login --with-api-key`），按标准 API 价格计费、不用会员额度，可以同样放进一个单独的目录。
- 桌面 App 和编辑器插件：在个人资料菜单里选 **Log out**，再在未登录界面重新登录另一个账号。

**安全提醒**

- 官方文档原话："treat `~/.codex/auth.json` like a password: it contains access tokens"。不要把它提交到仓库、贴到工单里，或在聊天里发给别人。
- 网上有一些第三方"Codex 账号切换器"桌面软件，不是 OpenAI 官方发布的。这类软件要切换账号，就需要读取或替换你的 `auth.json`；只提供安装包、看不到源代码的，请谨慎。
- 网上名为"瑶瑶Codex账号切换器"（lucky-yaoyao-codex-release）的软件**不是本仓库作者发布的，和瑶瑶没有关系**。瑶瑶没有发布过任何桌面软件或账号切换器。
- 额度用完了换号接着用：用自己名下的不同账号（比如个人账号和公司工作区账号）是官方工具支持的正常用法；但 OpenAI 使用条款不允许绕过速率限制或其他使用限制，专门注册多个账号轮流躲额度，可能被认定违反条款，以 OpenAI 最新条款为准。

更详细的图文说明和常见问题：[Codex 怎么切换多个账号？官方多账号切换方法](https://kw.yaoyao.chat/guide/codex-duozhanghao.html)

## 上手建议

在 Codex 里常用的斜杠命令（以官方 CLI 文档为准）：

| 命令 | 作用 |
|---|---|
| `/init` | 创建 `AGENTS.md`，写给 Codex 看的项目说明 |
| `/status` | 查看当前会话配置 |
| `/permissions` | 选择 Codex 可以做什么，控制改文件、跑命令前是否先问你 |
| `/model` | 选择模型和推理强度 |
| `/review` | 审查改动、找问题 |

三个建议：先用 git 提交一次当前代码，Codex 改坏了可以退回；任务说得具体，例如"把登录页的报错提示改成中文"比"优化一下登录"更省额度；每次改动都看一眼它列出的文件，确认没问题再提交。

## Plus 和 Pro 的额度差别

官方没有给固定条数，只给了估算。下面是官方定价页（2026-10-13 核对）里 Plus 每 5 小时在本地能发的 Codex 消息估算，模型名沿用页面上的写法：

| 模型 | Plus 每 5 小时本地消息（估算） |
|---|---|
| GPT-6 Astra | 5–45 条 |
| GPT-6.1 Sol | 15–160 条 |
| GPT-6 Sol | 15–150 条 |
| GPT-6 Luna | 350–3,000 条 |

要点：

- 这是估算，不是固定上限；实际能发多少取决于任务大小、项目复杂度和模型。
- 简单改动用轻量模型，难题再换强模型，同样的 5 小时能发的消息多得多。
- 本地消息和云端任务共用同一份额度。
- Plus 之外可能还有每周限制，官方没有给具体数字。
- Pro（$100 / $200 / $500）目前没有 5 小时限制，仍可能有每周限制，官方可能随时调整。
- 免费版和 Go 面向轻量任务；每周几次专注写代码选 Plus，每天重度使用选 Pro。

## 点数 credits 说明

Plus 和 Pro 用到上限后，可以购买额外的 ChatGPT credits（点数）继续用，不需要先升级套餐。

- OpenAI 官方账号在 2025 年 10 月公布过：可以在 Codex 用量面板里按 1,000 点 $40 的档位购买，并且 Codex 总是先用完套餐自带的额度，再扣 credits。
- 官方定价页现在写的是"点数的购买价格取决于你的套餐或协议"，所以当前价格以你在 ChatGPT 里看到的为准，上面 1,000 点约 $40 只作参考。
- 购买和查看余额：在 ChatGPT 的 Settings 里找 Usage，或在 Codex 应用的 Usage & Billing 里。
- 如果你每周都要额外买点数，把这部分钱加起来和 Pro 的价格比一比，往往直接升级 Pro 更划算。

## 常见报错与解决

| 情况 | 多半是什么原因 | 怎么办 |
|---|---|---|
| 浏览器授权页打不开或一直转圈 | 网络连不上 OpenAI 的登录服务 | 先确认 chatgpt.com 能正常打开，网络稳定后重试；远程机器用 `codex login --device-auth` |
| 登录后提示要升级套餐 | 登录的账号没有会员，或登录了另一个邮箱 | `codex login status` 查看，`codex logout` 后重新用开了会员的账号登录 |
| 写到一半提示额度用完 | Plus 的 5 小时额度用完了 | 等窗口恢复；用 `/model` 换轻量模型；长期不够考虑购买 credits 或升级 Pro |
| 发现被扣了 API 费用 | 登录时选成了 API Key | `codex logout`，重新选 Sign in with ChatGPT，额度才算进会员 |
| 安装脚本下载失败 | 默认下载地址当前不可用 | 设置 `CODEX_INSTALLER_USE_RELEASES_OPENAI_COM=false` 改从 GitHub Releases 下载，或用 npm、Homebrew 安装 |
| WSL 里编辑器找不到 Codex | WSL 里没装或不在 PATH 里 | 在 WSL 里运行 `which codex`，没有就在 WSL 里重装 |
| WSL 里大仓库很慢 | 仓库放在 `/mnt/c/...` 下 | 把仓库移到 Linux 家目录；再运行 `wsl --update` 和 `wsl --shutdown` |
| 命令行和桌面应用版本不一致 | 两边版本不同 | 分别运行 `codex --version` 对比后升级 |
| 一直在等，没有输出 | 可能在等你确认权限 | 看终端是否停在审批提示上，`/permissions` 查看当前设置 |

## 和 Claude Code 怎么选

| 对比项 | OpenAI Codex CLI | Claude Code |
|---|---|---|
| 订阅 | ChatGPT Plus / Pro 等 | Claude Pro / Max |
| 官方入门价 | $20/月（Plus） | $20/月（Pro） |
| 额度窗口 | Plus 按 5 小时计算，Pro 目前没有 5 小时限制 | 5 小时窗口 + 每周上限 |
| 启动命令 | `codex` | `claude` |
| 登录 | Sign in with ChatGPT | 浏览器登录 Claude 账号 |

两个都是在终端里让 AI 读代码、改代码、跑命令的工具，很多人两个都用，拿不准就各试一个月。Claude Code 的安装和报错见 [Claude Code 国内使用指南](https://github.com/haiwaihuyonghui-rgb/claude-code-guide-cn)。

## 国内开通参考

官方主要支持信用卡等海外付款方式，国内银行卡和支付宝付不了。Codex 跟着 ChatGPT 账号走：哪个账号开了 Plus / Pro，用它登录 Codex 就有会员额度。国内怎么开通、各档额度和价格怎么选，见作者整理的教程：[Codex 国内怎么用](https://kw.yaoyao.chat/guide/codex.html)、[Codex 多少钱](https://kw.yaoyao.chat/guide/codex-price.html)。

## 官方文档

- [openai/codex 仓库 README](https://github.com/openai/codex)
- [Codex 文档](https://developers.openai.com/codex)
- [Codex IDE 集成](https://developers.openai.com/codex/ide)
- [Codex 登录与认证（Authentication）](https://learn.chatgpt.com/docs/auth)
- [Codex 环境变量（含 CODEX_HOME）](https://learn.chatgpt.com/docs/config-file/environment-variables)

---

整理：瑶瑶ai训练师（AI 内容创作者），[官网](https://www.yaoyao.chat/)。本仓库为非官方教程整理，与 OpenAI 没有隶属关系，品牌名仅用于说明。内容采用 CC-BY-4.0 许可，转载请注明出处。
