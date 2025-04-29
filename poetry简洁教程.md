

# 📦 Poetry 终极指南

## 1️⃣ 安装 Poetry

```bash
# Linux/macOS
curl -sSL https://install.python-poetry.org | python3 -

# Windows (PowerShell)
(Invoke-WebRequest -Uri https://install.python-poetry.org -UseBasicParsing).Content | python -
```

验证安装：

```bash
poetry --version
```

---

## 2️⃣ 创建新项目

```bash
# 创建全新项目（自动生成目录结构）
poetry new my-project

# 已有项目初始化
cd existing-project
poetry init  # 交互式创建pyproject.toml
```

---

## 3️⃣ 依赖管理

```bash
# 添加依赖
poetry add requests
poetry add pytest --group dev  # 开发依赖

# 安装全部依赖
poetry install

# 更新依赖
poetry update
```

示例 `pyproject.toml` 片段：

```toml
[tool.poetry.dependencies]
python = "^3.8"
requests = { version = "^2.28", extras = ["security"] }

[tool.poetry.group.dev.dependencies]
pytest = "^7.2"
```

---

## 4️⃣ 虚拟环境管理

```bash
# 自动创建虚拟环境（默认）
poetry install

# 在虚拟环境中执行单条命令
poetry run python script.py

# 查看虚拟环境路径
poetry env info
```

---

## 5️⃣ 项目打包与发布

```bash
# 构建包
poetry build

# 发布到PyPI
poetry publish


# 使用私有仓库
poetry config repositories.my-repo https://example.com/simple
poetry publish -r my-repo
```

---

## 6️⃣ 进阶技巧

```bash
# 查看已安装python版本列表
poetry python list
# 安装特定版本python
poetry python install 3.12

# 导出requirements.txt
poetry export -f requirements.txt --output requirements.txt

# 检查依赖安全性
poetry check

# 显示依赖树
poetry show --tree
```

---

## 7️⃣ 常用配置

```bash
# 关闭自动创建虚拟环境
poetry config virtualenvs.create false

# 指定Python版本
poetry env use /full/path/to/python
```

---

## 🚀 工作流示例

```bash
# 典型开发流程
poetry new awesome-project
cd awesome-project
poetry add pandas numpy
poetry add --group dev pytest
code .
poetry run pytest tests/
poetry build
```

---

## 💡 常见问题

**Q：如何更新所有依赖？**

```bash
poetry update --latest
```

**Q：如何处理依赖冲突？**
手动修改 `pyproject.toml` 后运行：

```bash
poetry lock --no-update
poetry install
```

---

掌握这些命令后，你可以高效管理Python项目的依赖和打包流程。Poetry的自动版本解析和lock文件机制能有效避免"dependency hell"问题！🎯
