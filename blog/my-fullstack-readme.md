---
title: "My Fullstack Readme"
date: 2026-06-21
---

**Project Overview**

- **Root files**: [README.md](vscode-file://vscode-app/c:/Users/23855/AppData/Local/Programs/Microsoft%20VS%20Code/resources/app/out/vs/code/electron-browser/workbench/workbench.html): 项目说明与运行/开发指南. [pyproject.toml](vscode-file://vscode-app/c:/Users/23855/AppData/Local/Programs/Microsoft%20VS%20Code/resources/app/out/vs/code/electron-browser/workbench/workbench.html): Python 项目元信息与依赖管理.
- **[app](vscode-file://vscode-app/c:/Users/23855/AppData/Local/Programs/Microsoft%20VS%20Code/resources/app/out/vs/code/electron-browser/workbench/workbench.html)**: 后端应用主目录（主要代码入口与业务逻辑）。
    - **[main.py](vscode-file://vscode-app/c:/Users/23855/AppData/Local/Programs/Microsoft%20VS%20Code/resources/app/out/vs/code/electron-browser/workbench/workbench.html)**: 应用入口（ASGI/FastAPI 实例、包含 router mount 点或启动配置）。
    - **`backend_pre_start.py` / `tests_pre_start.py`**: 在启动或测试前运行的准备脚本（如等数据库就绪、创建表等）。
    - **`initial_data.py`**: 可选的种子数据脚本，用于初始化数据库示例数据。
    - **`models.py`**: SQLModel（或 ORM）数据模型定义。
    - **`crud.py`**: 与数据库交互的 Create/Read/Update/Delete 操作封装。
    - **`utils.py`**: 通用帮助函数与工具。
- **[api](vscode-file://vscode-app/c:/Users/23855/AppData/Local/Programs/Microsoft%20VS%20Code/resources/app/out/vs/code/electron-browser/workbench/workbench.html)**: 路由与 API 层。
    - **`api/main.py`** 与 [main.py](vscode-file://vscode-app/c:/Users/23855/AppData/Local/Programs/Microsoft%20VS%20Code/resources/app/out/vs/code/electron-browser/workbench/workbench.html) 共同负责路由注册（通常 [main.py](vscode-file://vscode-app/c:/Users/23855/AppData/Local/Programs/Microsoft%20VS%20Code/resources/app/out/vs/code/electron-browser/workbench/workbench.html) 引入并挂载 `api` 下的路由）。
    - **`api/routes/`**: 具体路由模块：`items.py`, `login.py`, `private.py`, `users.py`, `utils.py`（负责端点与请求/响应处理、依赖注入等）。
- **[core](vscode-file://vscode-app/c:/Users/23855/AppData/Local/Programs/Microsoft%20VS%20Code/resources/app/out/vs/code/electron-browser/workbench/workbench.html)**: 核心基础设施与配置。
    - **`core/config.py`**: 配置（环境变量、设置对象）。
    - **`core/db.py`**: 数据库引擎、会话、连接初始化（并可能包含开发时 `create_all` 的注释切换）。
    - **`core/security.py`**: 密码哈希、JWT 签名/校验等安全相关逻辑。
- **[email-templates](vscode-file://vscode-app/c:/Users/23855/AppData/Local/Programs/Microsoft%20VS%20Code/resources/app/out/vs/code/electron-browser/workbench/workbench.html)**: 邮件模板源文件（MJML）与构建后的 HTML（`src/` vs `build/`）。
- **`alembic/`**: 数据库迁移配置与历史（`env.py`, `versions/` 下各迁移脚本）。
- **[scripts](vscode-file://vscode-app/c:/Users/23855/AppData/Local/Programs/Microsoft%20VS%20Code/resources/app/out/vs/code/electron-browser/workbench/workbench.html)**: 启动/测试/格式化脚本（`test.sh`, `prestart.sh`, `tests-start.sh` 等）。
- **[tests](vscode-file://vscode-app/c:/Users/23855/AppData/Local/Programs/Microsoft%20VS%20Code/resources/app/out/vs/code/electron-browser/workbench/workbench.html)**: Pytest 测试套件（`api/`, `crud/`, `utils/` 的单元/集成测试）。

**主要模块职责（摘要）**

- **`models.py`**: 建表字段、关系、索引与模型校验规则。
- **`crud.py`**: 抽象数据库操作（避免在路由里写原生 SQL/Session 逻辑）。
- **[main.py](vscode-file://vscode-app/c:/Users/23855/AppData/Local/Programs/Microsoft%20VS%20Code/resources/app/out/vs/code/electron-browser/workbench/workbench.html)**: 构建 FastAPI 应用，包含中间件、异常处理、路由挂载与事件（startup/shutdown）。
- **`api/routes/*`**: 每个文件提供一组相关路由（例如 `users.py` 管理用户注册/查询，`login.py` 管理认证 token）。
- **`core/config.py`**: 把环境设置集中，供应用与测试读取（数据库 URL、密钥、邮件等）。
- **`core/db.py`**: 创建 SQLModel/SQLAlchemy engine 与 session，提供 `get_db` 依赖。
- **`core/security.py`**: 密码哈希、JWT 生成/解析、权限校验 helper。
- **`alembic/`**: 自动生成迁移、升级数据库；`versions/` 保存迁移历史。
- **`backend_pre_start.py` / [prestart.sh](vscode-file://vscode-app/c:/Users/23855/AppData/Local/Programs/Microsoft%20VS%20Code/resources/app/out/vs/code/electron-browser/workbench/workbench.html)**: 启动前工作流（例如先执行 `alembic upgrade head`），容器/CI 会调用。
- **[tests](vscode-file://vscode-app/c:/Users/23855/AppData/Local/Programs/Microsoft%20VS%20Code/resources/app/out/vs/code/electron-browser/workbench/workbench.html)**: 包含测试夹具（`conftest.py`），通过 `pytest` 运行。

# 如何启动（开发与测试）

- 本地（使用 `uv` 管理依赖/环境）
    - 安装依赖并同步环境：
```
uv sync
```

- 激活虚拟环境（Windows PowerShell）：
```
.\.venv\Scripts\Activate.ps1
.venv\Scripts\activate
```

## 后端：

- 在激活环境后运行：
	在backend目录下执行：
```
fastapi run --reload app/main.py
# 或显式 uvicorn（如果可用）
uvicorn app.main:app --reload
```
（api文档链接：http://localhost:8000/docs）


## 前端：
frontend目录下：
pnpm install和pnpm dev也启动起来了：http://localhost:5173/login


# 该看这里了

- 使用 Docker Compose（推荐和 README 描述一致）
    - 启动开发堆栈（`compose.override.yml` 在开发时常被启用）：
```
docker compose up --build
# 或项目提供的便捷命令（如 README 中的 watch）
docker compose watch
```

- 进入后端容器交互式 shell：
```
docker compose exec backend bash
```

- 在容器内运行开发服务器（如果你保持容器为“闲置”模式再手动运行）：
```
fastapi run --reload app/main.py
```

**运行测试**

- 在本地（激活虚拟环境后）：
```
pytest -q
```


- 在容器中（项目脚本封装好环境检查）：
```
docker compose exec backend bash scripts/tests-start.sh
# 传递 pytest 参数示例
docker compose exec backend bash scripts/tests-start.sh -x
```

- 项目也提供 [test.sh](vscode-file://vscode-app/c:/Users/23855/AppData/Local/Programs/Microsoft%20VS%20Code/resources/app/out/vs/code/electron-browser/workbench/workbench.html)（Bash 脚本），在类 Unix 环境运行：
```
bash ./scripts/test.sh
```

**数据库迁移**

- 生成新迁移（在容器或本地环境，确保能导入 `app.models`）：
```
alembic revision --autogenerate -m "描述变更"
```

- 应用迁移：
```
alembic upgrade head
```

- 注意：[prestart.sh](vscode-file://vscode-app/c:/Users/23855/AppData/Local/Programs/Microsoft%20VS%20Code/resources/app/out/vs/code/electron-browser/workbench/workbench.html) 通常会调用 `alembic upgrade head`，容器启动流程里可能已包含此步骤。

**常见快速排查建议**

- 如果启动时报错“无法连接数据库”：先确认 `.env` 或配置中的数据库 URL，并确保依赖的服务（Postgres 等）已启动（在 Compose 中检查）。
- 迁移相关问题：检查 `alembic/env.py` 是否正确导入 `app.models`，以及 `versions/` 目录是否为预期状态。
- 依赖问题：使用 `uv sync` 后确认 `python --version` 与虚拟环境一致，编辑器指向 `backend/.venv/...` 的解释器。
- 如果容器在保存文件后因为语法错误退出：用 `docker compose watch` + `docker compose exec backend bash` 进入容器并手动运行 `fastapi run --reload` 来更快修复。
