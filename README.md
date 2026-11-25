# 聊天参谋（纯 iPhone 版）

一个**不需要电脑、不需要同网段、不需要装 App** 的聊天参谋：把微信聊天内容贴进来，DeepSeek 给你一段分析和 3 条可以直接复制的回复。

- 加在 iPhone 主屏幕，点图标就进，全屏无地址栏
- 支持多个联系人，随时切换，可单独删除
- 支持区分「我说的」和「对方说的」，也支持整段粘贴让 AI 自己判断
- 每个联系人的记录存在手机本地，分析时会自动带上
- 不满意可以写要求重新生成（「再温柔点」「更短」「别太主动」）

## 它怎么工作

页面是**纯静态**的，直接在你的手机浏览器里调 DeepSeek 开放接口：

```
https://api.deepseek.com/chat/completions
```

DeepSeek 官方接口允许浏览器直连（CORS 已实测开放），所以中间不需要任何服务器。
你的 API Key 只存在这台手机的浏览器里，不会上传到任何地方——除了直接发给 DeepSeek。

## 部署到 GitHub Pages（拿到固定地址）

### 方式一：自己在网页上操作（最稳，不碰凭据）

1. GitHub 新建一个**公开**仓库，名字随意，比如 `chat-coach`
2. 把本目录这几样上传到仓库根目录：
   `index.html`、`manifest.json`、`icon-180.png`、`icon-192.png`、`icon-512.png`
3. 仓库 → Settings → Pages → Source 选 `Deploy from a branch` → Branch 选 `main`、目录选 `/ (root)` → Save
4. 等 1–2 分钟，地址是 `https://<你的用户名>.github.io/chat-coach/`

### 方式二：用 GitHub API 代传

需要一个 Personal Access Token：

- classic token：勾 `repo`
- fine-grained token：`Contents` 读写、`Pages` 读写、`Administration` 读写（要建仓库的话）

把 token 写到本地文件（**别贴在对话里**），例如 `D:\dsh-plugins\_data\gh_token.txt`，
然后让 DSH 用脚本建仓库、传文件、开 Pages。用完记得去 GitHub 撤销这个 token。

## 第一次使用

1. 用 Safari 打开部署好的地址
2. 点右上角 ⚙，填：
   - API 地址：`https://api.deepseek.com`（默认已填）
   - API Key：`sk-...`（在 platform.deepseek.com 创建）
   - 模型：`deepseek-chat`（想更会分析可以换 `deepseek-reasoner`，但慢）
3. 点右上角「分享」→「添加到主屏幕」，以后点图标进入

## 添加聊天的三种方式

| 按钮 | 用途 |
|---|---|
| **对方说的** | 把框里的内容记成 TA 发的（一行一条） |
| **我说的** | 记成你发的 |
| **整段粘贴** | 一大段不区分角色的聊天记录，交给 AI 判断谁说的 |

角色标对之后，分析质量会明显更好——模型能看出是谁在推进、谁在回避。

## 数据存在哪

**存在手机本地**（浏览器 localStorage），不上传任何服务器。

为什么不留电脑上：手机和电脑不同网段，要让电脑存就得开隧道 + 电脑常开，
而你随时可能在微信里需要它。放手机里意味着在任何网络下都能用。

风险与对策：

- iOS 在极端情况下可能清理网站数据（存储紧张、长期不访问）→ 定期用 ⚙ 里的**导出备份**
- 换手机不会自动迁移 → 换机前导出，新机导入
- 导出的 JSON 包含 API Key，别乱发

## 已知限制

- **接口必须支持 CORS**。DeepSeek 官方支持；如果你换别家中转发现「连不上接口」，多半是它不允许浏览器直连
- 需要联网（调 API）；离线时只能看已有记录
- 一次分析大约消耗 1500 输入 + 400 输出 token，按 DeepSeek 价格约几厘到一分钱
- 页面不含任何统计、广告、第三方脚本

## 换设备 / 重装

导出备份 → 在新设备打开同一地址 → ⚙ → 导入备份。地址不变，所以主屏幕图标不用重建。

## 文件说明

| 文件 | 作用 |
|---|---|
| `index.html` | 全部功能（单文件，内联样式和脚本） |
| `manifest.json` | PWA 清单，主屏幕图标、全屏打开 |
| `icon-180/192/512.png` | 主屏幕图标 |
| `_test.mjs` | 本地端到端测试（起假接口验证逻辑，不需要真实 Key） |
| `_make_icons.py` | 图标生成脚本 |
