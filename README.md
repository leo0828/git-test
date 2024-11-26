# git-test

---

## git commit 提交规范

feat：新功能（feature）

fix：修复 Bug

docs：文档（documentation）修改

style：代码格式（不影响代码运行的改动，例如空格、格式化等）

refactor：重构代码（即没有新增功能或修复 Bug）

perf：优化性能

test：添加或修改测试

chore：构建过程或辅助工具的变动，不涉及源代码

ci：CI 配置文件和脚本的修改

build：构建系统或外部依赖的变更（如升级 npm 包）

## git 分支开发规范

### 分支类型

主分支（main）：只包含稳定的发布版本。

开发分支（dev）：所有新功能和修复在此进行开发。

功能分支（feat/）：在 dev 分支上创建，用于开发新功能。

发布分支（release/）：当准备发布新版本时，从 dev 创建发布分支，进行最后的测试和优化。

热修复分支（hotfix/）：从 main 创建用于修复线上紧急 Bug，修复后合并回 main 和 dev。

### Git Flow 工作流

1. 从 dev 创建 feat/xxx 分支开发新功能。

```sh
# 在dev分支上创建功能分支
git checkout -b feat/xxx
```

2. 功能开发完成后，合并到 dev。

```sh
# 切换到dev分支
git checkout dev

# 合并feat/xxx分支到dev分支
git merge feat/xxx --no-ff
```

3. 准备发布时，从 dev 创建 release/xxx 分支，进行测试与修复。

```sh
# 在dev分支上创建release分支
git checkout -b release/1.0.0
```

4. 测试完成后，合并 release/xxx 到 main 和 dev。

```sh
# 将release分支合并到 main 和 dev 分支
git checkout main
git merge release/1.0.0 --no-ff
git checkout dev
git merge release/1.0.0 --no-ff
```

5. 如果生产环境出现问题，从 main 创建 hotfix/xxx 分支进行修复，修复完成后合并到 main 和 dev。

```sh
# 从 main 创建 hotfix 分支
git checkout -b hotfix/xxx main

# 修复完成后合并回 main 和 dev
git checkout main
git merge hotfix/xxx --no-ff
git checkout dev
git merge hotfix/xxx --no-ff
```

### 在 Git Flow 工作流中使用 Rebase

#### 1. 在 feat 分支中保持更新

当你在 feat 分支上开发时，dev 分支可能会有更新。为了让你的 feat 分支始终与最新的 dev 保持同步，可以使用 git rebase。

```sh
# 确保在 feat 分支上
git checkout feat/your-feature

# 从 dev 拉取最新更新
git pull origin dev

# 变基
git rebase dev

# 解决冲突（如果有），然后继续提交修改
git add <file>
git rebase --continue

# 将变基后的分支推送到远程（强制推送）
git push origin feat/your-feature --force
```

#### 2. 合并 feat 分支到 dev

当 feat 分支开发完成后，需要将其合并到 develop。在合并之前，可以使用 git rebase 清理提交历史.

```sh
# 切换到 feat 分支
git checkout feat/your-feature

# 将 feat 分支变基到最新的 dev
git rebase dev

# 切换到 dev 分支并合并
git checkout dev
git merge --no-ff feat/your-feature

#删除 feat 分支
git branch -d feat/your-feature
```

#### 3. 修复 hotfix 分支

在 Git Flow 中，hotfix 分支从 main 创建，紧急修复完成后需要合并回 main 和 develop。在此过程中，可以使用 git rebase 保持提交历史整洁。

```sh
# 切换到 hotfix 分支
git checkout hotfix/urgent-fix

# 变基到最新的 main
git rebase main

# 合并回 main 和 dev
git checkout main
git merge --no-ff hotfix/urgent-fix
git checkout dev
git merge --no-ff hotfix/urgent-fix

# 删除 hotfix 分支
git branch -d hotfix/urgent-fix
```

#### 4. 使用 rebase 合并 release 分支

在 Git Flow 中，release 分支通常用于准备稳定版本。如果需要合并到 main 和 develop，也可以使用 git rebase 确保提交历史干净。

```sh
# 切换到 release 分支
git checkout release/1.0.0

# 变基到最新的 dev
git rebase dev

# 合并回 main 和 dev
git checkout main
git merge --no-ff release/1.0.0
git checkout dev
git merge --no-ff release/1.0.0

# 删除 release 分支
git branch -d release/1.0.0
```

### Rebase 使用注意事项

1. 避免对公共分支（如 main、dev）直接使用 rebase。

- rebase 会重写提交历史，可能导致团队其他成员的分支与远程历史冲突。

2. 解决冲突时需谨慎：

- 在解决冲突后重新运行 git rebase --continue，确保变基操作完成。

3. 在多人协作时，推送变基后的分支时需要使用 --force：

- 例如：git push --force。

4. 保持本地和远程一致：

- 在完成 rebase 后，一定要确保远程分支的更新以避免冲突。
