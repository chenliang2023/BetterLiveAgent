<!-- status: todo -->
<!-- route:
 phase: execution
 local: antigravity
 server: manual
-->
<!-- released: -->

# [003] Plugin Hub 管理应用

## 📌 Spec 引用

来源：`.workflow/specs/liveagent-plugin-hub-phase1.md` 的“解决方案”“插件来源与安装流程”“插件生命周期”“Plugin Hub 桌面行为”“Profile 预留”章节；依赖 [001] 的包管理 API。

## 🎯 任务

在 `apps/plugin-hub` 中实现独立 Windows Plugin Hub 的管理界面和应用壳，让用户可以导入插件、查看 Manifest 信息、安装、启用、禁用、卸载并看到生命周期状态；管理界面不修改 LiveAgent 主 UI。

本 ticket 可以使用 Fake Broker 展示连接状态，不负责完整状态事件集成和宠物动画。

## ✅ 验收标准

1. Plugin Hub 可以独立启动，不需要 LiveAgent 主窗口提供 UI 入口。
2. 用户可以从本地目录、ZIP、GitHub 或 URL 发起导入，并看到校验中、失败或成功结果。
3. 插件列表显示名称、ID、版本、kind、来源、兼容性、权限和当前生命周期状态。
4. 安装确认清楚显示插件来源、权限和“未签名/个人来源”提示；取消不会安装。
5. 安装后的插件默认显示为 `installed-disabled`，UI 不提供隐式自动启用。
6. 用户可以显式启用、禁用和卸载插件；卸载前必须先禁用；每个操作都有成功或可读失败反馈。
7. 插件包损坏、Host API 不兼容或运行失败时，列表仍可打开，并显示原因和可恢复动作。
8. Hub 具备透明、无边框、可拖动、置顶的桌宠窗口基础能力，以及显示/隐藏、打开管理界面和退出的托盘菜单入口。
9. 管理界面不直接解析安装目录、不直接连接 LiveAgent 内部 API，而是调用 [001] 的管理接口和后续 Broker 客户端接口。

## 🧪 测试 seam

- 使用 Fake Plugin Repository 测试列表、安装、启用、禁用、卸载和失败状态。
- 使用 Fake Broker 测试未配对、已连接、离线和 Token 失效的 UI 状态，不绑定真实 LiveAgent。
- 使用桌面端自动化或可替换 Window Adapter 验证透明、拖动、置顶和托盘菜单行为。
- 用用户操作级测试验证安装默认禁用、卸载前禁用和错误提示。

## 🚧 阻塞

- 依赖：[001] resource.plugin 包校验与生命周期（必须先完成）

## 🧭 路由

- Phase：`execution`
- 档位：`常规`
- 本机：`antigravity`
- 服务器：`manual`
- 理由：核心难点是独立桌面应用、窗口行为和管理交互；Antigravity 的 frontend/ui/styling/interaction 能力最对口。服务器当前没有登记 Agent，只能由人工或当前会话执行。

## 🌿 工作树

- 目录：`.worktrees/003-plugin-hub-manager/`
- 分支：`ticket/003-plugin-hub-manager`

## 🧩 上下文

Plugin Hub 是独立应用，不在 LiveAgent React 树中注入第三方组件。第一版不做公共 Marketplace、多 Profile 编辑、升级、回滚和代码插件。管理 UI 只消费稳定的包管理接口，不把文件系统和权限判断散落到页面组件中。
