# WXPush - 微信消息推送服务 (EdgeOne Pages / Cloudflare Workers 兼容)

这是一个轻量级的微信公众号模板消息推送服务，提供简单的 HTTP API，通过 GET/POST 请求即可向微信用户发送模板消息。

## ✨ 特性

- 完全免费
- 支持多用户 (`userid` 用 `|` 分隔)
- 支持 GET / POST / Webhook
- 支持临时覆盖 AppID / Secret / 模板ID / 跳转链接
- 支持内置皮肤跳转页（`classic` / `night` / `hacker`）
- 可部署到 EdgeOne Pages（推荐）或 Cloudflare Workers

## 🚀 EdgeOne 标准部署

### 1. 项目标准结构

```text
.
|- edgeone.json
|- functions/
|- skins/
|- README.md
```

### 2. `edgeone.json`（本项目已提供）

本项目为无构建静态+函数混合项目，使用以下最小配置：

```json
{
  "outputDirectory": ".",
  "nodeVersion": "22"
}
```

### 3. 一键部署按钮

将以下 Markdown 放到你的 README：

```md
[![使用 EdgeOne Pages 部署](https://cdnstatic.tencentcs.com/edgeone/pages/deploy.svg)](https://console.cloud.tencent.com/edgeone/pages/new?repository-url=https%3A%2F%2Fgithub.com%2Fshisheng820%2FWXPush-edgeone&project-name=wxpush-edgeone&output-directory=.&env=API_TOKEN%2CWX_APPID%2CWX_SECRET%2CWX_USERID%2CWX_TEMPLATE_ID%2CWX_BASE_URL%2CWX_SKIN&env-description=%E8%AF%B7%E5%A1%AB%E5%86%99%E5%BE%AE%E4%BF%A1%E6%8E%A8%E9%80%81%E7%9B%B8%E5%85%B3%E7%8E%AF%E5%A2%83%E5%8F%98%E9%87%8F)
```

支持常用参数：

- `repository-url`
- `project-name`
- `install-command`
- `build-command`
- `output-directory`
- `root-directory`
- `env`
- `env-description`
- `env-link`

参数值请先做 URL 编码（`encodeURIComponent`）。

## ⚙️ 环境变量

必填环境变量：

- `API_TOKEN`
- `WX_APPID`
- `WX_SECRET`
- `WX_USERID`
- `WX_TEMPLATE_ID`

可选环境变量：

- `WX_BASE_URL`
- `WX_SKIN`（默认 `classic`）

## ⚙️ API 使用方法

### 请求地址

```text
https://<你的域名>/wxsend
```

注意：

- `https://<你的域名>/<token>` 是测试页面入口，不是 API 地址。
- 实际 API 固定走 `/wxsend`。

### 请求参数

| 参数名 | 类型 | 是否必填 | 描述 |
|---|---|---|---|
| `token` | String | 是 | 访问令牌，对应 `API_TOKEN`。GET 常用 query；POST 可用 body 或 `Authorization` 头。 |
| `title` | String | 是 | 消息标题。 |
| `content` | String | 是 | 消息内容。 |
| `appid` | String | 否 | 临时覆盖默认微信 AppID。 |
| `secret` | String | 否 | 临时覆盖默认微信 AppSecret。 |
| `userid` | String | 否 | 临时覆盖默认接收用户 OpenID，多用户用 `|` 分隔。 |
| `template_id` | String | 否 | 临时覆盖默认模板 ID。 |
| `base_url` | String | 否 | 临时覆盖默认跳转 URL。 |
| `skin` | String | 否 | 内置皮肤，支持 `classic`/`night`/`hacker`。未传 `base_url` 时用于生成跳转页。 |

### GET 示例

基础推送：

```text
https://<你的域名>/wxsend?title=服务器通知&content=服务已于北京时间%2022:00%20重启&token=your_secret_token
```

临时覆盖用户：

```text
https://<你的域名>/wxsend?title=私人提醒&content=记得带钥匙&token=your_secret_token&userid=temporary_openid_here
```

### POST / Webhook 示例

请求头：

```json
{
  "Authorization": "your_secret_token",
  "Content-Type": "application/json"
}
```

请求体：

```json
{
  "title": "Webhook 通知",
  "content": "这是一个通过 POST 请求发送的 Webhook 消息。"
}
```

cURL：

```bash
curl -X POST \
  "https://<你的域名>/wxsend" \
  -H "Authorization: your_secret_token" \
  -H "Content-Type: application/json" \
  -d '{
    "title": "来自 cURL 的消息",
    "content": "自动化任务已完成。"
  }'
```

## ✅ 响应示例

成功（HTTP 200）：

```json
{
  "msg": "Successfully sent messages to 1 user(s). First response: ok",
  "skin": "classic",
  "jump_url": "https://example.com/page"
}
```

参数缺失（HTTP 400，已改为精确报错）：

```json
{
  "msg": "Missing required parameters: token"
}
```

```json
{
  "msg": "Missing required parameters: title, content"
}
```

鉴权失败（HTTP 403）：

```json
{
  "msg": "Invalid token"
}
```

## 📚 参考

- EdgeOne LLM 文档索引：<https://docs.edgeone.site/llms.txt>
- EdgeOne `edgeone.json` 说明：<https://pages.edgeone.ai/document/edgeone-json>

## 🤝 贡献

欢迎提交 Issue / PR。

## 📜 许可证

MIT

## 致谢

本项目最初基于以下开源项目演进而来，在此致谢：

- <https://github.com/frankiejun/wxpush>
- <https://github.com/hezhizheng/go-wxpush>


