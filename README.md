# Codex Skills 集合

这是一个面向国内开发者的 Codex Skill 集合仓库。每个 Skill 单独放在 `skills/<skill-name>/` 目录中；以后新增 Skill 时，在本仓库增加子目录并推送即可，无需再为每个 Skill 新建公开仓库。

## 当前收录

- [`codex-macos-gh-tls`](skills/codex-macos-gh-tls/)：排查和修复 macOS 上 Codex 环境中的 GitHub CLI（`gh`）TLS/证书错误。

## 安装

先克隆一次集合仓库，再把需要使用的 Skill 目录链接到 Codex 的个人 Skill 目录：

```sh
git clone https://github.com/4646with/codex-skills.git "$HOME/codex-skills"
SKILLS_DIR="${CODEX_HOME:-$HOME/.codex}/skills"
mkdir -p "$SKILLS_DIR"
ln -s "$HOME/codex-skills/skills/codex-macos-gh-tls" "$SKILLS_DIR/codex-macos-gh-tls"
```

如果仓库已克隆，更新时只需在仓库目录运行 `git pull`。新增 Skill 后，为它再建立一个同样的目录链接；不需要重新克隆仓库。

## 新增 Skill

1. 在 `skills/` 下创建以 Skill 名称命名的目录，例如 `skills/my-skill/`。
2. 在其中添加 `SKILL.md`，并按需添加 `agents/openai.yaml`、`references/`、`scripts/` 或 `assets/`。
3. 将该 Skill 加入上方“当前收录”列表，检查内容中没有个人路径、令牌或其他私密信息。
4. 提交并推送到本仓库的 `main` 分支。

每个 Skill 都应能独立安装和使用；仓库根目录只放集合说明和管理文件。
