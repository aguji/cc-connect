# DeepSeek + 微信个人号 配置指南

> 把 DeepSeek 模型接入微信，在微信里使唤 AI 写代码。

---

## 1. 安装

```bash
# 先装 Node.js：https://nodejs.org（LTS 版本）

npm install -g cc-connect
npm install -g @anthropic-ai/claude-code
```

---

## 2. 微信扫码接入

```bash
cc-connect weixin setup --project my-project
```

终端出现二维码后，用手机微信扫码确认。token 会自动写入配置文件。

---

## 3. 配置 DeepSeek

配置文件路径：`~/.cc-connect/config.toml`（Windows：`C:\Users\你的用户名\.cc-connect\config.toml`）

扫码后配置已有微信部分，补上 DeepSeek 配置即可。**完整配置如下：**

```toml
[log]
level = "info"

[[projects]]
name = "my-project"

[projects.agent]
type = "claudecode"

[projects.agent.options]
work_dir = "/path/to/your/project"    # 你的项目路径
mode = "acceptEdits"                  # 编辑文件自动确认，省事

# ---- DeepSeek（以 SiliconFlow 为例，注册 https://cloud.siliconflow.cn 拿 key）----
[[projects.agent.providers]]
name = "deepseek"
api_key = "sk-你的key"
base_url = "https://api.siliconflow.cn/v1"
model = "deepseek-ai/DeepSeek-V3"
thinking = "disabled"

# ---- 微信（weixin setup 自动写入，不用改）----
[[projects.platforms]]
type = "weixin"

[projects.platforms.options]
token = "自动写入的token"
```

> **DeepSeek 官方 API 也行**：把 `base_url` 换成 `https://api.deepseek.com/v1`，`model` 换成 `deepseek-chat`，`api_key` 用 DeepSeek 官方的 key。

---

## 4. 启动

```bash
cc-connect
```

看到 `cc-connect is running` 后，用微信给机器人发一条消息（任意内容，先建立连接），然后发：

```
/provider switch deepseek
```

搞定，现在微信里聊天用的就是 DeepSeek 了。

---

## 5. 常用命令

| 命令 | 作用 |
|------|------|
| `/new` | 新对话 |
| `/stop` | 停止当前任务 |
| `/mode acceptEdits` | 少点确认弹窗 |
| `/status` | 看当前状态 |
| `/provider` | 切换模型 |
| `/compress` | 上下文太长时压缩 |

---

## 6. 设为开机自启

```bash
cc-connect daemon install --config ~/.cc-connect/config.toml
```

执行一次就行。之后每次开机登录 Windows，cc-connect 会自动在后台启动，不需要再手动操作。

> 手动管理：`cc-connect daemon start` / `stop` / `restart` / `status`

---

## 常见问题

**Q：微信发消息没反应？** 启动后第一次必须先发一条消息建立关联。如果还不行，检查 `config.toml` 中 `allow_from` 是不是限制了你的 ID。

**Q：DeepSeek 不生效？** 确认发了 `/provider switch deepseek`，确认 API key 正确且有余额。

**Q：回复很慢？** DeepSeek 免费版有速率限制，换付费版或用 SiliconFlow 中转。

**Q：想换模型？** `/provider switch` 切到其他 provider，支持配置多个随时切换。


