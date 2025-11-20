# Fork 项目支持 npx 安装指南

本指南说明如何让你的 fork 项目支持类似 `npx bmad-method@alpha install` 的安装方式。

## 方式一：从 GitHub 直接安装（最简单，推荐）

**无需发布到 npm，直接使用 GitHub 仓库**

### 使用方法

用户可以通过以下方式安装你的 fork：

```bash
# 安装最新版本（main 分支）
npx github:waaq151/BMAD-METHOD-Codebuddy install

# 安装特定分支
npx github:waaq151/BMAD-METHOD-Codebuddy#main install

# 安装特定标签/版本
npx github:waaq151/BMAD-METHOD-Codebuddy#v1.0.0 install
```

### 优点
- ✅ 无需 npm 账号
- ✅ 无需发布流程
- ✅ 直接使用 GitHub 代码
- ✅ 支持分支和标签

### 缺点
- ❌ 命令较长
- ❌ 需要完整的 GitHub 路径

---

## 方式二：发布到 npm（最接近原项目体验）

**需要 npm 账号，但使用体验最好**

### 步骤 1：修改 package.json

需要修改包名以避免与原始包冲突：

```json
{
  "name": "@waaq151/bmad-method-codebuddy",  // 或 "bmad-method-codebuddy"
  "version": "6.0.0-alpha.10",
  "repository": {
    "type": "git",
    "url": "git+https://github.com/waaq151/BMAD-METHOD-Codebuddy.git"
  }
}
```

### 步骤 2：创建 npm 账号

1. 访问 [npmjs.com](https://www.npmjs.com)
2. 注册账号
3. 验证邮箱

### 步骤 3：登录 npm

```bash
npm login
```

### 步骤 4：发布到 npm

```bash
# 发布公开包
npm publish --access public

# 或者发布为 scoped package（如果使用 @waaq151/ 前缀）
npm publish
```

### 步骤 5：使用方式

发布后，用户可以通过以下方式安装：

```bash
# 安装最新版本
npx @waaq151/bmad-method-codebuddy install

# 或（如果包名是 bmad-method-codebuddy）
npx bmad-method-codebuddy install

# 安装特定版本
npx @waaq151/bmad-method-codebuddy@6.0.0-alpha.10 install
```

### 更新版本

每次更新后需要：

1. 更新 `package.json` 中的版本号
2. 提交并推送到 GitHub
3. 运行 `npm publish` 发布新版本

---

## 方式三：使用 GitHub Packages（npm registry 的替代方案）

**使用 GitHub 的包管理服务**

### 步骤 1：创建 .npmrc 文件

在项目根目录创建 `.npmrc`：

```
@waaq151:registry=https://npm.pkg.github.com
```

### 步骤 2：修改 package.json

```json
{
  "name": "@waaq151/bmad-method-codebuddy",
  "publishConfig": {
    "registry": "https://npm.pkg.github.com"
  }
}
```

### 步骤 3：配置 GitHub Token

```bash
# 创建 GitHub Personal Access Token (需要 packages:write 权限)
# 然后配置 npm
npm config set //npm.pkg.github.com/:_authToken YOUR_GITHUB_TOKEN
```

### 步骤 4：发布

```bash
npm publish
```

### 步骤 5：使用方式

用户需要先配置 registry：

```bash
# 用户需要先配置
echo "@waaq151:registry=https://npm.pkg.github.com" >> ~/.npmrc

# 然后安装
npx @waaq151/bmad-method-codebuddy install
```

---

## 推荐方案对比

| 方案 | 难度 | 用户体验 | 维护成本 | 推荐度 |
|------|------|----------|----------|--------|
| GitHub 直接安装 | ⭐ 简单 | ⭐⭐⭐ 一般 | ⭐⭐⭐ 低 | ⭐⭐⭐⭐⭐ |
| 发布到 npm | ⭐⭐⭐ 中等 | ⭐⭐⭐⭐⭐ 最好 | ⭐⭐ 中等 | ⭐⭐⭐⭐ |
| GitHub Packages | ⭐⭐ 中等 | ⭐⭐ 较差 | ⭐⭐ 中等 | ⭐⭐ |

---

## 当前项目配置检查

你的项目已经配置好了 `bin` 字段，这意味着：

```json
"bin": {
  "bmad": "tools/bmad-npx-wrapper.js",
  "bmad-method": "tools/bmad-npx-wrapper.js"
}
```

这个配置已经正确，无论使用哪种方式，npx 都会正确执行这些命令。

---

## 快速开始（推荐：方式一）

如果你想立即使用，最简单的方式是：

1. **确保代码已推送到 GitHub**
   ```bash
   git push origin main
   ```

2. **告诉用户使用以下命令**
   ```bash
   npx github:waaq151/BMAD-METHOD-Codebuddy install
   ```

就这么简单！无需任何额外配置。

---

## 注意事项

1. **包名冲突**：如果选择发布到 npm，确保包名不与现有包冲突
2. **版本管理**：建议使用语义化版本（semver）
3. **README 更新**：更新 README.md 中的安装说明
4. **权限设置**：如果使用 GitHub Packages，确保仓库是公开的或用户有访问权限

---

## 测试安装

发布后，可以在新目录测试：

```bash
# 创建测试目录
mkdir test-install
cd test-install

# 测试安装（根据你选择的方式）
npx github:waaq151/BMAD-METHOD-Codebuddy install
# 或
npx @waaq151/bmad-method-codebuddy install
```

