# AGENTS.md

## 目标

这份仓库只维护 GitHub Issue Forms 的 YAML 模板，不再维护同名 Markdown 镜像。

## 模板获取规则

1. 不要用 `gh repo view <repo> --json issueTemplates` 作为模板总入口。
   - 这个字段当前只稳定返回 Markdown issue template。
   - YAML issue forms 需要直接读 `.github/ISSUE_TEMPLATE` 目录。
2. 先列出 YAML 模板，再逐个读取内容。
   - 列出文件：
     ```bash
     gh api repos/ricsy/.github/contents/.github/ISSUE_TEMPLATE --jq '.[] | select(.name | endswith(".yml")) | .path'
     ```
   - 读取单个文件：
     ```bash
     gh api repos/ricsy/.github/contents/.github/ISSUE_TEMPLATE/<file>.yml --jq '.content' | base64 -d
     ```
   - 批量读取：
     ```bash
     for f in $(gh api repos/ricsy/.github/contents/.github/ISSUE_TEMPLATE --jq '.[] | select(.name | endswith(".yml")) | .path'); do
       echo "=== $f ==="
       gh api "repos/ricsy/.github/contents/$f" --jq '.content' | base64 -d
       echo
     done
     ```

## 已确认的坑

1. YAML 和 Markdown 不要维护同名模板。
   - 同一个 `name` 会触发 GitHub 的重复校验。
   - 如果必须并存，必须显式区分展示名，但这会增加重复维护成本。
2. YAML 模板的 `name` 不能太短。
   - GitHub 会校验最小长度，过短会直接报错。
3. `gh repo view --json issueTemplates` 不能当作 YAML 的完整来源。
   - 需要直接读 `.github` 仓库的 contents API。

## 约定

1. 新增或修改 issue 模板时，默认只改 YAML。
2. 不再为了 `gh repo view` 额外创建 Markdown 副本。
3. 如果 agent 需要模板清单，优先读 `.github/ISSUE_TEMPLATE` 目录，而不是读仓库的 `issueTemplates` 字段。
