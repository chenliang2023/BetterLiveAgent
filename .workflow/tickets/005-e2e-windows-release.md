<!-- status: todo -->
<!-- route:
 phase: execution
 local: gpt56-luna
 server: manual
-->
<!-- released: -->

# [005] Windows 端到端闭环与交付验证

## 📌 Spec 引用

来源：`.workflow/specs/liveagent-plugin-hub-phase1.md` 的“主验收演示”“兼容与更新”“测试决策”“范围外”章节；依赖 [001]、[002]、[003] 和 [004]。

## 🎯 任务

把 Plugin Hub、resource.plugin 包管理、Extension Broker 和月薪喵运行时组合成一个可在 Windows 上演示的闭环，并产出可重复执行的构建、测试和验收流程。

完整流程必须是：导入月薪喵 → Manifest 校验 → 安装但默认禁用 → 用户启用 → 显示桌宠 → 配对 LiveAgent → 状态驱动动画 → 断线进入 offline → 自动重连 → 禁用并卸载。

## ✅ 验收标准

1. 在干净 Windows 环境中可以构建并启动独立 Plugin Hub，不要求修改或 Fork LiveAgent 主 UI。
2. 使用固定的月薪喵 fixture 完成从本地/ZIP 导入到安装、启用和桌宠显示的完整流程。
3. 使用测试 Broker 或真实本机 Broker 完成一次性配对、Token 连接、状态快照和事件驱动动画。
4. 人为断开 Broker 后，桌宠显示 `offline`，Plugin Hub 仍可操作，并在 Broker 恢复后自动重连。
5. 用户可以禁用并卸载月薪喵；重启 Plugin Hub 后不会残留旧窗口、订阅、临时目录或 Token 日志。
6. 非法 Manifest、路径穿越包、权限越界包、Token 失效和不兼容 Host API 都有稳定的失败表现，不会导致 Hub 崩溃。
7. 自动化测试覆盖五个 ticket 的关键外部行为，并能在本地重复执行。
8. 交付文档说明构建命令、测试命令、测试 fixture、已知限制和未实现的 `trusted.code.plugin` 能力。

## 🧪 测试 seam

- 端到端测试使用可控的 fixture source、Fake/Local Broker 和确定性状态序列。
- Windows 打包测试与业务协议测试分开：协议和生命周期可在非 GUI 环境运行，窗口/托盘测试在 Windows 执行。
- 使用临时用户数据目录验证安装、卸载和重启恢复，不污染开发者真实目录。
- 对完整主路径和四类失败路径做冒烟测试：非法包、拒绝配对、Token 撤销、Broker 离线。

## 🚧 阻塞

- 依赖：[001] resource.plugin 包校验与生命周期（必须先完成）
- 依赖：[002] LiveAgent Extension Broker 与只读状态事件（必须先完成）
- 依赖：[003] Plugin Hub 管理应用（必须先完成）
- 依赖：[004] 声明式月薪喵宠物运行时（必须先完成）

## 🧭 路由

- Phase：`execution`
- 档位：`复杂`
- 本机：`gpt56-luna`
- 服务器：`manual`
- 理由：这是跨包管理、授权协议、桌面窗口和状态事件的集成闭环，属于复杂档；Luna 是本机主力且覆盖 architecture/backend/frontend/test，适合做全局整合和回归。服务器当前没有登记 Agent，只能由人工或当前会话执行。

## 🌿 工作树

- 目录：`.worktrees/005-e2e-windows-release/`
- 分支：`ticket/005-e2e-windows-release`

## 🧩 上下文

Phase 1 不包含公共 Marketplace、任意代码插件、MCP、自动化、升级/回滚和多 Profile 编辑。验收应证明资源插件闭环稳定，而不是提前实现 Phase 2 的 `trusted.code.plugin`。
