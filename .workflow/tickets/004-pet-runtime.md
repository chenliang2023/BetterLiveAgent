<!-- status: todo -->
<!-- route:
 phase: execution
 local: antigravity
 server: manual
-->
<!-- released: -->

# [004] 声明式月薪喵宠物运行时

## 📌 Spec 引用

来源：`.workflow/specs/liveagent-plugin-hub-phase1.md` 的“插件包与 Manifest”“Broker/API 边界”“Plugin Hub 桌面行为”“连接状态”“宠物渲染测试”章节；依赖 [001]、[002] 和 [003]。

## 🎯 任务

把已启用的 `resource.plugin` 资源和 Broker 状态事件接入 Plugin Hub 的桌宠运行时：月薪喵读取状态映射并渲染对应动画，LiveAgent 断线时显示 `offline`，后台自动重连后恢复最新状态。

本 ticket 不执行插件代码，也不扩展 Broker 权限。

## ✅ 验收标准

1. 启用包含六类资源映射的月薪喵插件后，Plugin Hub 能加载资源并显示桌宠。
2. `idle`、`working`、`waiting`、`success`、`error`、`offline` 六类状态都能映射到对应资源或安全占位表现。
3. 状态变化事件按顺序驱动动画切换；连接建立后先应用状态快照，再处理后续事件。
4. LiveAgent 断开或 Token 失效时，桌宠保持显示并进入 `offline`，Hub UI 不阻塞或崩溃。
5. Hub 自动重连成功后重新订阅事件，并以最新快照纠正桌宠状态。
6. 禁用插件会停止动画、关闭或隐藏桌宠窗口、取消事件订阅并释放相关资源。
7. 桌宠只能获得状态枚举和必要的时间信息；测试中不能观察到对话正文、工具参数、工具结果或 Token。
8. 月薪喵资源包不包含、也不会触发任何脚本、子进程、网络请求或 LiveAgent 控制操作。

## 🧪 测试 seam

- 使用 Fake Broker 推送确定性状态序列：`idle → working → waiting → success → idle`。
- 使用 Fake Asset Loader 测试完整资源、缺失资源、非法路径和占位状态。
- 使用 Fake Clock/Retry Policy 验证断线、重连和状态快照优先级。
- 使用窗口适配层验证状态渲染不会阻塞管理界面。
- 使用权限审计断言验证运行时没有调用白名单之外的 Broker 方法。

## 🚧 阻塞

- 依赖：[001] resource.plugin 包校验与生命周期（资源和状态映射来源）
- 依赖：[002] LiveAgent Extension Broker 与只读状态事件（状态和事件来源）
- 依赖：[003] Plugin Hub 管理应用（窗口和应用壳）

## 🧭 路由

- Phase：`execution`
- 档位：`常规`
- 本机：`antigravity`
- 服务器：`manual`
- 理由：主要难点是桌面窗口、动画状态映射、断线体验和交互细节；Antigravity 的 frontend/ui/interaction 能力最对口。服务器当前没有登记 Agent，只能由人工或当前会话执行。

## 🌿 工作树

- 目录：`.worktrees/004-pet-runtime/`
- 分支：`ticket/004-pet-runtime`

## 🧩 上下文

月薪喵是个人使用的第一款资源插件。运行时只消费 [001] 产出的已校验资源和 [002] 的只读状态事件；不要让宠物插件绕过 Hub 直接连接 LiveAgent。重连策略需要可替换，具体退避数值可在实现时选择，但必须避免快速无限重试。
