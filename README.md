# Apex WebSocket 调试台

该页面复用客户端正式认证流程，并从 `/v1/client/websocket/config` 获取服务端签发的 WebSocket 短期 Token。

## 启动

```bash
cd tools/wsdebug
python3 -m http.server 18080
```

浏览器访问：

```text
http://127.0.0.1:18080/debug.html
```

## 功能

- 使用一次性 RSA-OAEP-256 公钥加密账号密码；
- 登录后加载当前玩家信息；
- 登录 Access Token 到期前自动刷新，并接受 Refresh Token 轮换；
- 从服务端获取正确的 WebSocket URL、namespace 和 platform group；
- WebSocket Token 到期前自动重新获取并重建连接；
- 自动心跳、断线重连和消息日志；
- 可在全部日志和仅业务消息之间切换，复制日志时仅复制当前可见内容；
- 一键复制当前可见的调试日志；
- 将心跳发送数与响应数并排展示，业务消息单独统计且不包含 `ping/pong`；
- 高亮 `SUPER_MARQUEE_TRIGGERED` 消息；
- 使用 `LOGIN_TYPE_REGISTER` 创建并在浏览器中保存固定测试账号池；
- 使用账号池建立最多 100 条独立用户 WebSocket 连接；
- 批量连接独立续期 Access/Refresh/WS Token，并统计最新业务消息的连接覆盖率；
- 顶层以 Tab 切换“单个调试”和“批量调试”，两种工作区互不占用页面高度；
- 批量调试内部再以 Tab 切换账号池管理和批量连接，Tab 标题实时显示账号数与在线进度。

## 批量连接

1. 先填写环境作用域和账号前缀。统一测试密码默认为 `Aa123456`，也可在创建前修改。
2. 点击“创建/补齐账号池”。已在当前浏览器保存的账号会跳过，同名账号会尝试使用当前密码登录校验。
3. 账号池就绪后点击“批量登录并连接”。每个账号只使用一个固定 Device ID 建立一条连接。

账号池按 `tenant/platform/agent-line/region` 隔离，并以明文存在浏览器 `localStorage`。可以导出 JSON 供其他人导入，文件中包含测试账号密码，不得用于生产环境或提交到代码仓库。

单账号登录的密码、Access Token、Refresh Token 和客户端请求签名密钥只保存在当前页面内存中。签名密钥不会写入 `localStorage`、账号池或导出文件，页面刷新后需要重新输入。环境 ID 和固定测试账号池会保存在浏览器 `localStorage`，方便重复调试。

本页面只能用于测试环境，不得部署到生产环境或公开网络。
