# THUQX AutoOps for OpenClaw 0.5

**真正可落地的「内容生产 → 多平台运营」自动化**（无需平台 API）  
基于 **Chrome DevTools Protocol（CDP）** 控制浏览器，完成：内容生成、平台适配、顺序运营与稳定性自检。

清华大学新媒体研究中心 · 智能传播工具链 · *version 0.5*

---

## 你能得到什么

- **一条命令四平台顺序运营**：Twitter / 微博 / 小红书 / 微信公众号（草稿）
- **同一主题，多平台不同风格文案**：避免“一段话到处贴”
- **无需平台 API / Token**：只要你在 Chrome 里登录过，脚本就能模拟真实操作
- **面向 SPA 的稳定性策略**：顺序执行避免焦点争抢，内置 CDP 自检与自动拉起 Chrome

---

## 支持平台（Ops）

| 平台 | 脚本 | 结果 |
|------|------|------|
| Twitter / X | `twitter/cdp_tweet.py` | 发推（Ops） |
| 微博 | `weibo/cdp_weibo_ops.py` | 发微博（Ops） |
| 小红书 | `xiaohongshu/cdp_xhs_ops.py` | 长文笔记（写长文 → 一键排版 → 下一步 → 平台侧提交） |
| 微信公众号 | `wechat/cdp_wechat_ops.py` | 保存草稿（不群发） |

---

## 一键运营（推荐）

```bash
bash scripts/run_social_ops_v5.sh "AI认知债务"
```

- **可选**：`THUQX_PLATFORM_PAUSE=3`（平台间间隔秒数，默认 2；网络慢/SPA 重时更稳）
- **兼容**：`scripts/run_social_publish_v5.sh` 会自动转调到 `run_social_ops_v5.sh`

顺序（为了稳定，**必须顺序**）：

```
generate_content.py 生成四平台 JSON
  → Twitter Ops
  → 微博 Ops
  → 小红书 Ops
  → 微信公众号（草稿 Ops）
```

---

## 快速开始

### 1) 安装依赖

```bash
pip3 install websocket-client
```

### 2) 启动 Chrome CDP（新版 Chrome 必看）

新版 Chrome 要求 CDP 使用 **非默认 profile**，并允许 DevTools WebSocket 来源：

```bash
open -na "Google Chrome" --args \
  --remote-debugging-port=9222 \
  --user-data-dir="$HOME/chrome-cdp-profile" \
  --remote-allow-origins="*" \
  "https://x.com" \
  "https://weibo.com" \
  "https://creator.xiaohongshu.com/publish/publish?source=official&from=menu&target=article" \
  "https://mp.weixin.qq.com"
```

然后在这四个标签页里完成登录。  
如果你直接跑 `run_social_ops_v5.sh`，当 CDP 不可用时脚本也会尝试自动拉起 Chrome（macOS / Linux）。

---

## 单平台 Ops

```bash
python3 twitter/cdp_tweet.py "推文内容"
python3 weibo/cdp_weibo_ops.py "微博正文"
python3 xiaohongshu/cdp_xhs_ops.py "标题" "正文"
python3 wechat/cdp_wechat_ops.py "标题" "正文"
```

---

## 项目结构

```
├── README.md
├── OPENCLAW.md
├── scripts/
│   ├── _thuqx_cdp_common.sh
│   ├── generate_content.py
│   ├── generate_social_content_v4.py
│   ├── run_social_ops_v5.sh           # 四平台一键顺序运营（推荐）
│   └── run_social_publish_v5.sh       # 兼容入口
├── twitter/
│   ├── cdp_tweet.py
│   └── tweet.sh
├── weibo/
│   ├── cdp_weibo_ops.py
│   ├── run_weibo_ops.sh
│   └── run_weibo_publish.sh           # 兼容入口
├── xiaohongshu/
│   └── cdp_xhs_ops.py
└── wechat/
    └── cdp_wechat_ops.py
```

---

## 稳定性与常见问题

### 为什么不并行？

四个平台如果并行，会在同一个 Chrome 实例上争抢：
- Tab 激活
- 输入焦点（`Input.insertText`/React 编辑器状态）

所以本项目默认 **顺序 Ops**，并提供 `THUQX_PLATFORM_PAUSE` 做缓冲。

### 小红书为什么要“一键排版 → 下一步”？

长文流程中，编辑器页底部常见的是“一键排版”，进入模板页后才出现“下一步”，再进入设置页才出现平台侧“发布/提交”按钮。脚本按这个真实路径执行，并避免误点侧边栏的“发布笔记”导航。

### Twitter 发帖按钮 disabled？

Twitter 编辑器常是 React/Draft.js，单纯 `Input.insertText` 可能不触发状态更新；脚本使用 `document.execCommand('insertText')` 作为主要策略以启用按钮。

---

## OpenClaw 集成

OpenClaw 的 skill 映射与一键命令见 `OPENCLAW.md` 与 `openclaw/zeelin-social-autopublisher/SKILL.md`。

---

## License

MIT

---

*THUQX AutoOps 0.5 · 清华大学新媒体研究中心*
