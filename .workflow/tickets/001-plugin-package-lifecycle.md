<!-- status: dispatched host:local agent:gpt56-sol worktree:.worktrees/001-plugin-package-lifecycle branch:ticket/001-plugin-package-lifecycle at:2026-09-28T15:25:14+08:00 -->
<!-- route:
 phase: execution
 local: gpt56-sol
 server: manual
-->
<!-- released: -->

# [001] resource.plugin 包校验与生命周期

## 📌 Spec 引用

来源：`.workflow/specs/liveagent-plugin-hub-phase1.md` 的“插件类型分层”“插件包与 Manifest”“插件来源与安装流程”“插件生命周期”“安全测试”章节。

## 🎯 任务

实现 `resource.plugin` 的包读取、Manifest 校验、来源导入、原子安装和生命周期状态管理，使 Plugin Hub 能安全地把本地目录、ZIP、GitHub/URL 来源转换成一个**已安装但默认禁用**的插件。

本 ticket 不执行插件代码，不连接 LiveAgent Broker，不负责宠物窗口渲染。

## ✅ 验收标准

1. 本地目录和 ZIP 可以被读取；GitHub/URL 输入可以被解析为受支持的目录或 ZIP，无法解析时返回可读错误。
2. 合法 Manifest 能通过校验，并识别 `schema`、`id`、`name`、`version`、`kind`、`hostApi`、`permissions` 和 `entry`。
3. `kind` 不是 `resource.plugin`、Host API 不兼容、权限不在白名单或缺少资源入口时，导入被拒绝。
4. `..`、绝对路径、符号链接逃逸和解压到包外的路径都被拒绝。
5. 安装前先写入临时目录；校验或用户取消时不留下半成品；安装成功后原子进入正式安装目录。
6. 安装成功后的状态是 `installed-disabled`，不会执行代码、连接 Broker、启动进程或创建事件订阅。
7. 生命周期至少支持 `not-installed`、`validating`、`installed-disabled`、`enabled`、`failed`、`disabled`、`uninstalled`，非法跳转会被拒绝。
8. Hub 重启后能恢复已安装插件、版本、来源和启用状态；插件包损坏时进入可诊断的 `failed` 或 `invalid` 状态，不影响其他插件。
9. 安装、校验、启用、禁用和卸载的错误都能返回稳定的错误类别，供 UI 和端到端测试使用。

## 🧪 测试 seam

- 使用内存文件系统或临时目录测试目录/ZIP 导入、原子安装和清理。
- 使用固定的 Manifest fixture 覆盖合法包、缺字段、未知权限、不兼容版本和路径穿越。
- 使用 Fake Source Resolver 测试 GitHub/URL 解析成功和失败。
- 使用生命周期状态机的公开命令测试合法/非法迁移，不测试内部类名。
- 测试失败安装不会污染正式安装目录。

## 🚧 阻塞

- 依赖：无（可立即开始）

## 🧭 路由

- Phase：`execution`
- 档位：`关键`
- 本机：`gpt56-sol`
- 服务器：`manual`
- 理由：涉及插件包信任边界、路径穿越、原子安装和生命周期一致性，属于安全敏感的关键档；本机 Sol 的 security/debugging 能力最对口。服务器当前没有登记 Agent，只能由人工或当前会话执行。

## 🌿 工作树

- 目录：`.worktrees/001-plugin-package-lifecycle/`
- 分支：`ticket/001-plugin-package-lifecycle`

## 🧩 上下文

第一版只允许声明式 `resource.plugin`，不允许执行 JavaScript、TypeScript、Rust、PowerShell 或其他脚本。安装与启用必须分离，安装后默认禁用。未来可能增加 `trusted.code.plugin`，但本 ticket 不实现，也不能为未来代码插件预留可执行入口。

状态和错误应提供给后续 Plugin Hub UI 使用；不要让 UI 直接读安装目录或自行解析 Manifest。
