<!-- status: dispatched host:local agent:gpt56-sol worktree:.worktrees/002-extension-broker branch:ticket/002-extension-broker at:2026-09-28T15:25:14+08:00 -->
<!-- route:
 phase: execution
 local: gpt56-sol
 server: manual
-->
<!-- released: -->

# [002] LiveAgent Extension Broker 与只读状态事件

## 📌 Spec 引用

来源：`.workflow/specs/liveagent-plugin-hub-phase1.md` 的“Broker/API 边界”“配对与 Token”“连接状态”“自动重连”章节。

## 🎯 任务

定义并实现 `liveagent.extension/v1` 的最小 Broker/API，使独立 Plugin Hub 可以通过一次性配对码和本地 Token 读取当前 Agent 状态、订阅只读状态事件，并在 LiveAgent 离线后自动恢复连接。

本 ticket 只实现协议、授权和状态事件边界；不实现 Plugin Hub UI，也不实现宠物动画。

## ✅ 验收标准

1. Broker 暴露版本化的 Extension API，并支持开始配对、确认配对、Token 使用和 Token 撤销。
2. 正确的一次性配对码可以换取本机 Token；错误、过期、重复使用或已撤销的配对码不能换取 Token。
3. 未配对客户端、错误 Token 和已撤销 Token 不能读取 Agent 状态或订阅事件。
4. 已授权客户端只能使用 `agent.state.read` 和 `agent.events.subscribe` 两项能力；其他方法返回稳定的权限错误。
5. `agent.state.read` 返回六类状态之一：`idle`、`working`、`waiting`、`success`、`error`、`offline`。
6. 连接成功后先返回当前状态快照，再发送后续 `agent/state-changed` 事件；事件至少包含类型、状态和时间戳。
7. LiveAgent 连接断开时，客户端能观察到 `offline` 或等价的连接状态，并能重新订阅。
8. Token 不出现在普通日志、插件包、远程同步内容和错误消息中；撤销后已有连接被拒绝或关闭。
9. Broker 的传输实现可以替换，但必须限制为本机连接，并为配对失败、Token 失效、权限拒绝和断线返回可诊断错误。

## 🧪 测试 seam

- 使用 Fake Agent State Provider 驱动状态快照和事件序列。
- 使用 Fake Token Store 测试签发、过期、撤销和重启后恢复。
- 使用协议级客户端测试正常请求、权限拒绝、非法状态和断线重连。
- 使用 Fake Transport，使测试不绑定 Named Pipe、loopback HTTP 或具体 IPC 实现。
- 断线测试验证“快照先于事件”以及重复订阅不会产生重复事件。

## 🚧 阻塞

- 依赖：无（可与 [001] 并行）

## 🧭 路由

- Phase：`execution`
- 档位：`关键`
- 本机：`gpt56-sol`
- 服务器：`manual`
- 理由：涉及本机授权、Token 撤销、权限边界和事件一致性，属于安全与状态一致性关键档；本机 Sol 的 security/backend/debugging 能力最适合。服务器当前没有登记 Agent，只能由人工或当前会话执行。

## 🌿 工作树

- 目录：`.worktrees/002-extension-broker/`
- 分支：`ticket/002-extension-broker`

## 🧩 上下文

Broker 是 LiveAgent 与外部扩展之间的稳定边界，不能让 Plugin Hub 直接访问 LiveAgent 内部数据库、React 状态或 Tauri 内部命令。第一版只读，不提供控制任务、读取对话、读取工具结果、访问文件、Shell 或网络的能力。

具体 IPC 方式不在本 ticket 中预先锁死；先让协议和 Fake Transport 可测试，再选 Windows 实现。
