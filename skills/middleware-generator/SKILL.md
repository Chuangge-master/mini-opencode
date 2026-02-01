---
name: "middleware-generator"
description: "为 mini-OpenCode 添加新中间件的标准流程。当需要添加新中间件（如总结、限流、审计等）时调用此技能。"
---

# mini-OpenCode 中间件添加指南

本 Skill 总结了为 mini-OpenCode 添加可配置中间件的标准流程，旨在确保功能实现的规范性与一致性。

## 适用场景
当需要为 mini-OpenCode Agent 添加新的功能拦截或增强层（中间件）时，请遵循此指南。例如：对话总结、输入过滤、Token 统计、审计日志等。

## 标准流程

### 1. 调研与设计
- **功能调研**：使用 `SearchDocsByLangChain` 查找 LangChain/LangGraph 原生支持的中间件或相关实现模式。
- **配置设计**：在 `config.yaml` 中设计合理的配置结构。推荐遵循以下层级：
  ```yaml
  middlewares:
    enabled:
      - middleware_name
    configs:
      middleware_name:
        param1: value
        param2: value
  ```

### 2. 目录结构规范
所有中间件代码应位于 `src/mini_opencode/middlewares/` 目录下：
- `src/mini_opencode/middlewares/__init__.py`: 导出中间件加载函数。
- `src/mini_opencode/middlewares/<middleware_name>.py`: 实现中间件的初始化逻辑。

### 3. 代码实现步骤
- **初始化逻辑**：在 `<middleware_name>.py` 中编写 `get_<middleware_name>_middleware()` 函数，负责从配置中读取参数并实例化中间件。
- **Agent 集成**：修改 `src/mini_opencode/agents/coding_agent.py`，在 `create_coding_agent` 函数中调用加载函数，并将其添加到 `create_agent` 的 `middleware` 列表中。
- **提示词增强**（可选）：如果中间件影响对话上下文（如总结），需同步更新 `src/mini_opencode/prompts/templates/coding_agent.md`，向模型解释相关行为。

### 4. 验证与文档同步
- **功能验证**：编写临时测试脚本（如 `test_middleware.py`）验证 Agent 是否能正常加载中间件且不崩溃。
- **文档更新**：
  - 更新 `AGENTS.md`：在架构概述和配置说明中加入新中间件的信息。
  - 更新 `README.md` & `README.zh-CN.md`：在特性列表和项目结构中加入相关内容。

## 注意事项
- **配置读取**：使用 `mini_opencode.config.get_config_section` 统一读取配置。
- **健壮性**：中间件加载函数应处理配置缺失的情况，默认返回 `None` 或使用合理的默认值。
- **代码风格**：遵循 Python 3.12+ 规范，强制使用类型提示和 Google 风格的文档字符串。
