# 用户自定义命令使用指南

## 概述

BMAD-METHOD 支持通过 `customize.yaml` 文件为 agent 添加自定义命令（menu items）。这些自定义命令会在编译时自动合并到 agent 中，**不会被覆盖**。

## 工作原理

### 1. 文件位置

自定义配置文件位于：
```
{bmad_folder}/_cfg/agents/{module}-{agentName}.customize.yaml
```

例如：
- `bmad/_cfg/agents/bmm-pm.customize.yaml` - 为 bmm 模块的 pm agent 添加自定义
- `bmad/_cfg/agents/core-dev.customize.yaml` - 为 core 模块的 dev agent 添加自定义

### 2. 自动创建

在首次安装或编译 agent 时，如果 customize 文件不存在，系统会**自动创建模板文件**，但**不会覆盖已存在的文件**。

### 3. 合并机制

在编译 agent 时（`rebuildAgentFiles` 或 `processAgentFiles`）：
1. 读取源 agent YAML 文件（`src/modules/{module}/agents/{name}.agent.yaml`）
2. 检查是否存在对应的 customize 文件
3. 如果存在，将 customize 文件中的内容**合并**到 agent 中
4. 对于 `menu` 字段，自定义命令会**追加**到原有命令后面

## 使用方法

### 步骤 1：找到 customize 文件

在 `{bmad_folder}/_cfg/agents/` 目录下找到对应的 customize 文件，例如：
```bash
.bmad/_cfg/agents/bmm-pm.customize.yaml
```

如果文件不存在，可以手动创建，或者运行一次编译让它自动创建模板。

### 步骤 2：编辑 customize 文件

打开 customize 文件，添加自定义命令到 `menu` 字段：

```yaml
# 自定义 Persona（可选）
persona:
  role: "高级产品经理"
  identity: "我是你的产品管理专家"

# 自定义 Agent 元数据（可选）
agent:
  metadata:
    name: "产品经理"

# 自定义命令（menu items）- 这是重点！
menu:
  - trigger: my-custom-command
    description: "执行我的自定义命令"
    exec: "{project-root}/{bmad_folder}/my-scripts/custom-task.xml"
  
  - trigger: another-command
    description: "另一个自定义命令"
    workflow: "{project-root}/{bmad_folder}/my-workflows/custom-workflow.yaml"

# 自定义关键操作（可选）
critical_actions:
  - "执行某些关键操作"

# 自定义记忆（可选）
memories:
  - "记住某些重要信息"

# 自定义提示（可选）
prompts:
  - id: custom-prompt
    content: "自定义提示内容"
```

### 步骤 3：重新编译

运行编译命令：
```bash
bmad compile
```

或者使用 CLI：
```bash
bmad install --action compile
```

编译后，自定义命令会被合并到 agent 中。

## 自定义命令格式

### 基本格式

```yaml
menu:
  - trigger: command-name        # 命令触发器（必需）
    description: "命令描述"      # 命令描述（必需）
    # 以下属性可选，根据命令类型选择：
    workflow: "path/to/workflow.yaml"    # 执行工作流
    exec: "path/to/script.xml"            # 执行脚本
    tmpl: "path/to/template.md"          # 使用模板
    data: "path/to/data.json"             # 使用数据文件
    action: "#prompt-id"                  # 执行 prompt
    validate-workflow: "path/to/workflow.yaml"  # 验证工作流
```

### 命令类型示例

#### 1. 工作流命令

```yaml
menu:
  - trigger: deploy
    description: "部署应用"
    workflow: "{project-root}/{bmad_folder}/my-workflows/deploy.yaml"
```

#### 2. 执行脚本命令

```yaml
menu:
  - trigger: backup
    description: "备份数据"
    exec: "{project-root}/{bmad_folder}/my-scripts/backup.xml"
```

#### 3. 使用模板命令

```yaml
menu:
  - trigger: generate-report
    description: "生成报告"
    tmpl: "{project-root}/{bmad_folder}/templates/report.md"
```

#### 4. 使用数据文件命令

```yaml
menu:
  - trigger: process-data
    description: "处理数据"
    data: "{project-root}/{bmad_folder}/data/input.json"
```

#### 5. 执行 Prompt 命令

```yaml
menu:
  - trigger: custom-action
    description: "执行自定义操作"
    action: "#my-custom-prompt"
```

#### 6. 验证工作流命令

```yaml
menu:
  - trigger: validate
    description: "验证配置"
    validate-workflow: "{project-root}/{bmad_folder}/workflows/validate.yaml"
```

## 完整示例

### 示例 1：为 PM Agent 添加自定义命令

文件：`.bmad/_cfg/agents/bmm-pm.customize.yaml`

```yaml
# 自定义命令
menu:
  - trigger: create-prd
    description: "创建产品需求文档"
    workflow: "{project-root}/{bmad_folder}/my-workflows/create-prd.yaml"
  
  - trigger: review-feature
    description: "评审功能设计"
    exec: "{project-root}/{bmad_folder}/my-scripts/review-feature.xml"
  
  - trigger: generate-roadmap
    description: "生成产品路线图"
    tmpl: "{project-root}/{bmad_folder}/templates/roadmap.md"
```

### 示例 2：为 Dev Agent 添加自定义命令

文件：`.bmad/_cfg/agents/core-dev.customize.yaml`

```yaml
# 自定义 Persona
persona:
  role: "高级开发工程师"
  communication_style: "技术导向，注重代码质量"

# 自定义命令
menu:
  - trigger: code-review
    description: "代码审查"
    workflow: "{project-root}/{bmad_folder}/workflows/code-review.yaml"
  
  - trigger: run-tests
    description: "运行测试套件"
    exec: "{project-root}/{bmad_folder}/scripts/run-tests.xml"
```

## 注意事项

### 1. 文件不会被覆盖

- ✅ **已存在的 customize 文件不会被覆盖**
- ✅ 自定义命令会**追加**到原有命令后面
- ✅ 可以安全地编辑 customize 文件

### 2. 路径占位符

在 customize 文件中可以使用以下占位符：
- `{project-root}` - 项目根目录
- `{bmad_folder}` - BMAD 安装文件夹名称（如 `.bmad` 或 `bmad`）

### 3. 命令合并方式

- **menu 命令**：自定义命令会**追加**到原有命令列表后面
- **persona**：自定义值会**覆盖**原有值（如果提供了非空值）
- **critical_actions**：自定义操作会**追加**到原有操作后面
- **memories**：自定义记忆会**追加**到原有记忆后面
- **prompts**：自定义提示会**追加**到原有提示后面

### 4. 空值处理

- 空字符串 `""` 会被忽略
- `null` 值会被忽略
- 空数组 `[]` 会被忽略（对于 persona 字段）

### 5. 编译时机

自定义命令会在以下时机被合并：
- 首次安装时
- 执行 `compile` 命令时
- 执行 `quick update` 时

## 验证自定义命令

编译后，可以检查生成的 agent 文件（`.md` 文件）来验证自定义命令是否已添加：

```bash
# 查看编译后的 agent 文件
cat .bmad/bmm/agents/pm.md | grep -A 5 "menu"
```

或者直接使用 agent，查看菜单中是否包含你的自定义命令。

## 故障排除

### 问题 1：自定义命令没有出现

**检查**：
1. customize 文件是否存在且路径正确
2. YAML 格式是否正确
3. 是否重新编译了 agent

**解决**：
```bash
# 重新编译
bmad compile
```

### 问题 2：自定义命令被覆盖

**原因**：这不应该发生。如果发生了，可能是：
1. customize 文件被意外删除
2. 使用了错误的文件路径

**解决**：
1. 检查 customize 文件是否存在
2. 确认文件路径格式：`{module}-{agentName}.customize.yaml`

### 问题 3：YAML 格式错误

**检查**：
```bash
# 验证 YAML 格式
yamllint .bmad/_cfg/agents/bmm-pm.customize.yaml
```

**解决**：
- 确保缩进使用空格（不是 Tab）
- 确保 YAML 语法正确
- 检查引号匹配

## 总结

通过 `customize.yaml` 文件，你可以：
- ✅ 为任何 agent 添加自定义命令
- ✅ 自定义 agent 的 persona、记忆、提示等
- ✅ 安全地保留自定义内容（不会被覆盖）
- ✅ 在编译时自动合并到 agent 中

这是 BMAD-METHOD 提供的强大自定义功能，让你可以根据项目需求定制 agent 的行为！

