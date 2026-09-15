# Codex 插件：DWS multi skills

本 fork 将上游 `skills/multi/` 作为一个 Codex 插件发布。这是 HaidongPang
维护的社区打包，不代表钉钉或 OpenAI 官方发布。

## 安装

前置条件：支持 `codex plugin` 的 Codex，以及已安装、已登录且与 skills
版本兼容的 `dws` CLI。插件本身不安装 CLI、不登录账号，也不启动 MCP 服务。

```bash
codex plugin marketplace add HaidongPang/dingtalk-workspace-cli --ref main
codex plugin add dws@haidong-dws
codex plugin list --marketplace haidong-dws --json
```

安装后新建 Codex 任务，直接描述钉钉需求。插件使用
`dws:` 命名空间，当前包含 14 个产品及共享 skills。

若已安装独立的 `dingtalk-*` 或 mono `dws` skill，应在核对来源并备份后
停用旧副本，避免重复发现。此插件不会自动修改或删除旧 skills；
`dws upgrade` 也可能重新安装上游独立 skills，升级后需检查重复项。

## 目录与加载

插件根目录就是仓库根目录，清单直接指定：

```json
"skills": "./skills/multi/"
```

不复制、不移动、不重写 multi 内容，产品之间的相对引用以及 `scripts/`
均保持上游布局。mono 保留在仓库中供原有 CLI 使用，不作为插件 skill 加载。
脚本仍由 Agent 按产品 skill 的指令调用；脚本中的 `dws` 必须能从 PATH 找到。

插件统一管理安装和启停，内部仍为 multi：Codex 可以看到各 skill 的名称和
描述，并在任务需要时读取正文。这不会把 14 个 skill 的描述压缩成一个入口。

## 更新与验证

维护者在 `main` 分支合并上游更新，核对 multi 与 CLI
兼容性，并更新插件版本。用户随后刷新 marketplace 并重新安装：

```bash
codex plugin marketplace upgrade haidong-dws
codex plugin add dws@haidong-dws
```

新版本应验证 Codex 实际发现的 skills 与 `skills/multi/*/SKILL.md` 完全一致，
且不包含 mono `dws`；安装缓存中的脚本和参考文件应与源目录一致。

初始版本已在 Codex Desktop `0.154.0-alpha.6.2` 的隔离配置中完成安装和
skill 发现检查。这里只验证插件加载，不代表已对钉钉线上业务操作做回归测试。

## 从旧插件名称迁移

旧标识 `dingtalk-workspace-cli@haidong-dws` 已更名为 `dws@haidong-dws`。
更新 marketplace 后安装新插件，再卸载旧插件，避免两个命名空间同时加载：

```bash
codex plugin marketplace upgrade haidong-dws
codex plugin add dws@haidong-dws
codex plugin remove dingtalk-workspace-cli@haidong-dws
```

显示名称为 **DWS**。图标取自钉钉 macOS 应用的 `AppIcon.icns`，
转换为 PNG；钉钉标识权利归其权利人所有，不表示本社区插件获得官方背书。
