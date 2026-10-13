# Codex macOS `gh` TLS 故障排查 Skill

这个 Skill 用于排查和修复 macOS 上 Codex 环境中的 GitHub CLI（`gh`）TLS/证书错误，尤其适用于 `gh` 在 Codex 里报证书错误、但在普通终端中可以正常运行的情况。

## 安装

将仓库克隆到 Codex 的 Skill 目录：

```sh
git clone https://github.com/4646with/codex-macos-gh-tls.git "${CODEX_HOME:-$HOME/.codex}/skills/codex-macos-gh-tls"
```

如果没有设置 `CODEX_HOME`，默认目录是 `~/.codex/skills/codex-macos-gh-tls`。

## 使用

在 Codex 中输入 `$codex-macos-gh-tls` 调用，也可以直接描述 macOS 上 `gh` 的 TLS 或证书报错，让 Codex 自动匹配这个 Skill。

排查步骤、安全边界和修复说明见 [`SKILL.md`](SKILL.md)。
