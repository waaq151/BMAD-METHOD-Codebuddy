# 同步上游仓库更新指南

## 概述

当你 fork 的项目有更新时，需要将原始仓库（upstream）的更改同步到你的 fork。

## 原始仓库信息

- **原始仓库**: `https://github.com/bmad-code-org/BMAD-METHOD`
- **你的 Fork**: `https://github.com/waaq151/BMAD-METHOD-Codebuddy`

## 同步步骤

### 方法一：使用命令行（推荐）

#### 1. 添加 upstream 远程仓库

```bash
git remote add upstream https://github.com/bmad-code-org/BMAD-METHOD.git
```

如果已经添加过，可以跳过这一步。检查是否已添加：
```bash
git remote -v
```

#### 2. 获取 upstream 的更新

```bash
git fetch upstream
```

#### 3. 切换到主分支（通常是 main）

```bash
git checkout main
```

#### 4. 合并 upstream 的更改

有两种方式：

**方式 A：Merge（保留完整历史）**
```bash
git merge upstream/main
```

**方式 B：Rebase（线性历史，更干净）**
```bash
git rebase upstream/main
```

#### 5. 解决冲突（如果有）

如果有冲突，Git 会提示你。解决冲突后：
```bash
# 如果使用 merge
git add .
git commit

# 如果使用 rebase
git add .
git rebase --continue
```

#### 6. 推送到你的 fork

```bash
git push origin main
```

### 方法二：使用 GitHub Web 界面

1. 访问你的 fork 页面：`https://github.com/waaq151/BMAD-METHOD-Codebuddy`
2. 点击 "Sync fork" 按钮（如果有）
3. 或者点击 "Fetch upstream" → "Fetch and merge"

## 处理本地未提交的更改

在同步之前，建议先处理本地未提交的更改：

### 选项 1：提交更改

```bash
git add .
git commit -m "你的提交信息"
```

### 选项 2：暂存更改（Stash）

```bash
# 暂存更改
git stash

# 同步完成后恢复
git stash pop
```

### 选项 3：创建新分支保存更改

```bash
# 创建新分支保存当前更改
git checkout -b my-changes

# 提交更改
git add .
git commit -m "保存我的更改"

# 切换回 main 分支
git checkout main

# 同步 upstream
# ... 执行同步步骤 ...

# 如果需要，可以合并你的更改
git merge my-changes
```

## 完整同步脚本

```bash
#!/bin/bash
# 同步上游仓库的完整流程

# 1. 检查是否有未提交的更改
if [[ -n $(git status -s) ]]; then
    echo "⚠️  检测到未提交的更改"
    echo "请先提交或暂存更改："
    echo "  git add . && git commit -m '你的提交信息'"
    echo "  或"
    echo "  git stash"
    exit 1
fi

# 2. 添加 upstream（如果不存在）
if ! git remote | grep -q upstream; then
    echo "添加 upstream 远程仓库..."
    git remote add upstream https://github.com/bmad-code-org/BMAD-METHOD.git
fi

# 3. 获取更新
echo "获取 upstream 更新..."
git fetch upstream

# 4. 切换到 main 分支
echo "切换到 main 分支..."
git checkout main

# 5. 合并更新
echo "合并 upstream/main..."
git merge upstream/main

# 6. 推送到 origin
echo "推送到 origin..."
git push origin main

echo "✅ 同步完成！"
```

## 常见问题

### Q1: 如何查看 upstream 和 origin 的差异？

```bash
# 查看本地和 upstream 的差异
git fetch upstream
git diff main upstream/main

# 查看本地和 origin 的差异
git diff main origin/main
```

### Q2: 如何只同步特定分支？

```bash
# 假设要同步 develop 分支
git fetch upstream
git checkout develop
git merge upstream/develop
git push origin develop
```

### Q3: 如何更新 upstream 的 URL？

```bash
# 查看当前 upstream URL
git remote get-url upstream

# 更新 upstream URL
git remote set-url upstream https://github.com/bmad-code-org/BMAD-METHOD.git
```

### Q4: 如何移除 upstream？

```bash
git remote remove upstream
```

### Q5: 同步后如何查看更改？

```bash
# 查看最近的提交
git log --oneline -10

# 查看与 upstream 的差异
git log main..upstream/main --oneline
```

## 最佳实践

1. **定期同步**：建议每周或每次开始工作前同步一次
2. **先提交本地更改**：同步前先提交或暂存本地更改
3. **使用 merge 而非 rebase**：如果与其他人协作，merge 更安全
4. **测试同步后的代码**：同步后运行测试确保一切正常
5. **查看更改日志**：同步后查看 CHANGELOG.md 了解更新内容

## 自动化同步

可以设置 GitHub Actions 自动同步，但需要配置权限。对于个人 fork，手动同步更简单安全。

