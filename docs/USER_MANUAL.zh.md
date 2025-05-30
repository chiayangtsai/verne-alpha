# 用户手册

## 1. 引言

欢迎使用 OpenHands，这是一个由人工智能（AI）驱动的软件开发智能体（Agent）平台。OpenHands 智能体能够执行人类开发人员的各项任务：修改代码、运行命令、浏览网页、调用 API，甚至从 StackOverflow 复制粘贴代码片段。

OpenHands 旨在协助处理各类软件工程任务。它尤其擅长“绿地”项目（即从零开始的新项目）、为现有代码库添加新功能、将代码重构为更易管理的代码块，以及辅助修复程序错误（Bug）。

OpenHands 界面的主要特性包括：

-   **聊天面板 (Chat Panel)**：显示您与 OpenHands 智能体之间的实时对话。您可在此处发出指令并接收反馈。
-   **变更记录 (Changes)**：展示智能体在执行任务期间对文件所做修改的差异（Diff）对比。
-   **VS Code 编辑器 (VS Code)**：内置一个 VS Code 编辑器实例，方便您浏览智能体的工作目录、查看及修改文件，同时也支持文件的上传与下载。
-   **终端 (Terminal)**：在智能体的沙盒（Sandbox）环境中提供一个命令行终端界面，允许智能体和用户执行命令。
-   **Jupyter 笔记本 (Jupyter)**：在 Jupyter Notebook 用户界面中展示智能体执行过的所有 Python 命令，这对数据可视化等任务尤为实用。
-   **应用程序 (App)**：若智能体启动了 Web 服务器（例如 Flask 或 Node.js 应用），此选项卡将展示正在运行的应用程序，方便您直接进行交互测试。
-   **浏览器 (Browser)**：一个非交互式浏览器窗口，OpenHands 智能体可利用它访问网站并从互联网收集信息。

本手册旨在引导您顺利完成 OpenHands 的安装配置，并高效地将其应用于您的软件开发工作中。

## 2. 开始入门

本节涵盖在您的本地系统上运行 OpenHands 所需的初始设置。

### 系统要求

-   MacOS（须支持 Docker Desktop）
-   Linux
-   Windows（须支持 WSL 和 Docker Desktop）

为确保流畅运行，我们推荐您的系统配备现代处理器以及至少 **4GB 内存**。

### 先决条件

在运行 OpenHands 之前，请务必确保您的操作系统已安装并正确配置 Docker Desktop。OpenHands 会在沙盒化的 Docker 环境中运行其智能体，以此保障系统的安全性和运行的一致性。

**各操作系统的 Docker 具体设置：**

*   **MacOS**：
    1.  安装 [适用于 Mac 的 Docker Desktop](https://docs.docker.com/desktop/install/mac-install/)。
    2.  打开 Docker Desktop，访问 `Settings > Advanced`（设置 > 高级）选项卡，确认已勾选 `Allow the default Docker socket to be used`（允许使用默认 Docker 套接字）选项。此设置确保 OpenHands 能与 Docker 守护进程（Daemon）正常通信。

*   **Linux**：
    1.  安装 [适用于 Linux 的 Docker Desktop](https://docs.docker.com/desktop/install/linux/)。请确保您的用户账户拥有管理 Docker 容器的权限（通常是将用户添加到 `docker` 用户组）。

*   **Windows**：
    1.  安装 [WSL (Windows Subsystem for Linux)](https://learn.microsoft.com/en-us/windows/wsl/install)。OpenHands 在 Windows 平台上主要通过 WSL 2 提供支持。
    2.  在 PowerShell 中执行 `wsl --version` 命令，确认 `Default Version`（默认版本）是否为 `2`。若非如此，您可能需要将 WSL 2 设为默认版本（执行 `wsl --set-default-version 2`）或转换您已有的 WSL 发行版。
    3.  安装 [适用于 Windows 的 Docker Desktop](https://docs.docker.com/desktop/install/windows-install/)。
    4.  安装期间或安装完成后，请配置 Docker Desktop 以采用基于 WSL 2 的引擎。此选项通常位于 `Settings > General`（设置 > 常规）中。
    5.  在 Docker Desktop 的设置中，找到 `Resources > WSL Integration`（资源 > WSL 集成）部分，确保已启用与您的默认 WSL 发行版（或您计划使用的特定发行版）的集成。

### 启动 OpenHands 应用程序
完成 Docker 的配置后，您便可以启动 OpenHands 应用程序。最简便的方式是采用我们提供的 Docker 命令：

```bash
docker pull docker.all-hands.dev/all-hands-ai/runtime:0.39-nikolaik

docker run -it --rm --pull=always \
    -e SANDBOX_RUNTIME_CONTAINER_IMAGE=docker.all-hands.dev/all-hands-ai/runtime:0.39-nikolaik \
    -e LOG_ALL_EVENTS=true \
    -v /var/run/docker.sock:/var/run/docker.sock \
    -v ~/.openhands-state:/.openhands-state \
    -p 3000:3000 \
    --add-host host.docker.internal:host-gateway \
    --name openhands-app \
    docker.all-hands.dev/all-hands-ai/openhands:0.39
```

Docker 容器成功运行后，您即可在本地 Web 浏览器中通过访问 [http://localhost:3000](http://localhost:3000) 来使用 OpenHands。

### 初始化配置

首次启动 OpenHands 时，您 **必须** 为智能体配置一个大型语言模型 (LLM)，以便其正常工作：
1.  从可选列表中选择一个 `LLM Provider`（LLM 提供商），例如 Anthropic、OpenAI 等。
2.  从该提供商处选择一个具体的 `LLM Model`（LLM 模型），例如推荐的 `anthropic/claude-sonnet-4-20250514`。
3.  输入您所选 LLM 提供商的 `API Key`（API 密钥）。

您可以在首次启动时弹出的设置窗口中完成此项配置，或在任何时候通过点击 OpenHands 用户界面中的 `Settings`（设置）按钮（通常为一个齿轮图标）来进行调整。

**获取 API 密钥：**

*   **Anthropic (Claude):**
    1.  [创建一个 Anthropic 帐户](https://console.anthropic.com/)。
    2.  [生成一个 API 密钥](https://console.anthropic.com/settings/keys)。
    3.  [设置账单信息](https://console.anthropic.com/settings/billing)。
*   **OpenAI:**
    1.  [创建一个 OpenAI 帐户](https://platform.openai.com/)。
    2.  [生成一个 API 密钥](https://platform.openai.com/api-keys)。
    3.  [设置账单信息](https://platform.openai.com/account/billing/overview)。

完成这些步骤后，您就可以开始使用 OpenHands 了！

## 3. 基本用法

本小节列举了若干示例，旨在演示如何与 OpenHands 智能体互动以完成常见的软件开发工作。核心要点在于向智能体下达清晰且具体的指令。

### “Hello World”入门示例

不妨从一个简单的任务开始，以便快速熟悉智能体的工作模式：
> 请编写一个名为 `hello.sh` 的 bash 脚本，该脚本能够打印输出 "hello world!"。

OpenHands 将尝试创建此脚本，为其赋予必要的执行权限，并运行以检验输出结果。随后，您可以基于此成果进行迭代优化：
> 请修改 `hello.sh` 脚本，使其能接受一个名字作为第一个命令行参数；如果未提供参数，则默认输出 "world"。

### 从零开始构建新项目

OpenHands 在“绿地”项目（即无历史遗留代码的项目）中表现出色，因为它能够从头规划并搭建项目结构。此时，您需要明确您的需求以及期望采用的技术栈。例如：
> 请使用 React 技术栈构建一个纯前端的待办事项（TODO）应用程序。应用的所有状态数据均应存储在浏览器的 `localStorage` 中。

基础框架搭建完毕后，您可以进一步提出优化需求：
> 请为每个待办事项任务增加一个可选的截止日期功能。

在开发过程中，勤于版本控制是一个良好的习惯。您可以指示 OpenHands 来完成这项工作：
> 请将当前的所有代码更改提交，提交信息为 "Add due date functionality"，并将这些更改推送至一个名为 `feature/due-dates` 的新 Git 分支。

### 向现有项目添加新代码

OpenHands 能够将新的特性或代码模块无缝集成到已有的代码库中。例如：
> 请为本项目添加一个 GitHub Action 工作流程，该流程需使用 Flake8 工具对仓库中的 Python 代码执行 Lint 检查。

若要进行更复杂的代码添加，提供充足的现有代码库上下文信息，将有助于智能体生成更理想的结果：
> 请在 `./backend/api/routes.js` 文件中新增一个 GET 路由 `/api/tasks`。此路由需能从数据库中检索并返回所有任务的列表。假设项目后端采用 PostgreSQL 数据库，并使用 `pg` 库进行数据库操作。

### 代码重构

您可以利用 OpenHands 对代码进行分阶段、模块化的重构。相较于一次性提出大规模的修改需求，聚焦于具体的改进点会更为有效。例如：
> 请将 Go 语言文件 `./app.go` 中的所有单字母变量名替换为更具描述性的名称，并确保此更改不会导致任何现有测试失败。

> PHP 文件 `widget.php` 内的 `build_and_deploy_widgets` 函数逻辑过于冗长。请将其拆分为 `build_widgets` 和 `deploy_widgets` 两个独立的函数，同时保持原有的核心功能不变。

### Bug 修复 (Bug Fixing)

OpenHands 能够协助您诊断并修复代码中的 Bug。若您能提供关于 Bug 的详细上下文信息，例如错误消息、相关的代码片段或您对问题根源的初步判断，智能体的修复效果会更佳。例如：
> `/subscribe` API 端点目前错误地拒绝了使用 `.io` 顶级域名的电子邮件地址。相关的业务逻辑代码位于 `routes/subscribe.js` 文件中。请修复此验证缺陷。

在此类场景下，采用测试驱动开发（TDD）的模式会非常高效。您可以先要求 OpenHands 编写一个能够复现该 Bug 的失败测试用例：
> 我在 Python 脚本 (`main.py`) 中定义的 `hello` 函数，当接收到空字符串作为输入时会引发程序崩溃。请先在 `tests/test_main.py` 文件中编写一个 pytest 测试用例来准确地复现这个 Bug。随后，请修改 `hello` 函数的实现，使其能够妥善处理空字符串输入（例如，返回一个特定的错误提示或一个预设的默认值），并确保新编写的测试用例能够顺利通过。

### 命令行界面 (CLI) 模式

OpenHands 同时提供了一个交互式的命令行界面（CLI），方便开发者在终端环境中使用。

**通过 Docker 运行 CLI：**
1.  配置必要的环境变量：`SANDBOX_VOLUMES`、`LLM_MODEL`、`LLM_API_KEY`。
2.  执行以下 Docker 命令：
    ```bash
    docker run -it \
        --pull=always \
        -e SANDBOX_RUNTIME_CONTAINER_IMAGE=docker.all-hands.dev/all-hands-ai/runtime:0.39-nikolaik \
        -e SANDBOX_USER_ID=$(id -u) \
        -e SANDBOX_VOLUMES=$SANDBOX_VOLUMES \
        -e LLM_API_KEY=$LLM_API_KEY \
        -e LLM_MODEL=$LLM_MODEL \
        -v /var/run/docker.sock:/var/run/docker.sock \
        -v ~/.openhands-state:/.openhands-state \
        --add-host host.docker.internal:host-gateway \
        --name openhands-cli-$(date +%Y%m%d%H%M%S) \
        docker.all-hands.dev/all-hands-ai/openhands:0.39 \
        python -m openhands.cli.main
    ```

**CLI 交互命令：**
-   `/help`：显示所有可用的交互命令及其说明。
-   `/exit`：退出 CLI 应用程序。
-   `/init`：为智能体初始化一个新的代码仓库以供探索。
-   `/status`：显示当前会话的详细信息及用量统计。
-   `/new`：开启一个全新的会话。
-   `/settings`：查看并修改当前的 LLM 及智能体相关设置。
-   `/resume`：若智能体处于暂停状态，使用此命令可使其恢复运行（在智能体运行时可按 `Ctrl-P` 暂停）。

若需了解更多关于如何编写高效指令以获得理想输出的技巧，请参阅[提示工程最佳实践](https://docs.all-hands.dev/modules/usage/prompting/prompting-best-practices)文档。
---

## 4. 常见应用场景

OpenHands 是一款功能强大的多用途工具，能够辅助开发人员完成各类软件工程相关的任务。本节将重点介绍若干 OpenHands 在自动化或增强开发者工作流方面表现尤为出色的典型应用场景。

*   **开发新功能：** 从全新的项目开始，逐步构建应用程序或模块。例如：“请为我的博客网站构建一个基于 Python Flask 框架的后端服务，要求包含用于创建、读取和删除博文的 API 端点。”
*   **编写自动化脚本：** 通过生成不同编程语言的脚本，实现常见任务的自动化。例如：“请编写一个 Python 脚本，用于根据文件扩展名来整理指定目录下的所有文件。”
*   **添加单元测试：** 指示 OpenHands 为现有代码编写单元测试，以提升代码质量与健壮性。例如：“请为 `billing.py` 文件中的 `calculate_total` 函数编写相应的单元测试用例。”
*   **调试代码：** 向 OpenHands 提供详细的错误信息或程序非预期行为的描述，辅助定位并修复 Bug。例如：“`auth.py` 文件中的 `user_login` 函数，在用户不存在时会抛出 `NullPointerException` 异常。请修复此问题。”
*   **代码重构：** 改进现有代码的结构，提升其可读性与可维护性。例如：“请重构 `utils.py` 文件中的 `process_data` 函数，使其逻辑更加模块化。”
*   **学习新技术：** 要求 OpenHands 生成特定技术的代码示例或解释相关概念。例如：“请给我展示一个在 Go 语言中如何发起 API 调用的代码示例。”
*   **生成样板代码：** 利用 OpenHands 快速生成常见的代码结构，从而加速项目启动与开发进程。例如：“请为包含页眉、侧边栏和主内容区域的响应式网页，生成基础的 HTML 和 CSS 结构代码。”
*   **集成第三方 API：** 指示 OpenHands 编写与外部服务 API 对接的代码。例如：“请编写一个 JavaScript 函数，用于从 GitHub API 获取指定用户的信息。”

请谨记，将复杂任务拆解为一系列更小、更易于管理步骤，并向 OpenHands 提供清晰、具体的指令以及必要的上下文信息，这将显著提升最终输出结果的质量。

## 5. 高级功能与配置

本小节将深入探讨 OpenHands 提供的一些高级配置选项与特殊操作模式，旨在赋予用户更大程度的自定义能力和使用上的灵活性。

OpenHands 提供了一系列丰富的配置选项，允许用户根据自身需求定制其行为。这些配置主要通过两种方式进行管理：一是通过传递给 Docker 容器的环境变量；二是在开发环境中，通过编辑 `config.toml` 文件来实现。

**主要配置参数（以环境变量形式列出）：**

*   **核心配置 (`CORE_*` 前缀)：**
    *   `MAX_ITERATIONS`：设定智能体执行单个任务时允许的最大迭代次数。
    *   `DEFAULT_AGENT`：指定系统默认使用的智能体类别（例如 `CodeActAgent`）。
*   **LLM 配置 (`LLM_*` 前缀)：**
    *   `LLM_MODEL`：指定所用的大型语言模型（例如 `anthropic/claude-sonnet-4-20250514`）。
    *   `LLM_API_KEY`：您选用的大型语言模型提供商的 API 密钥。
    *   `LLM_BASE_URL`：大型语言模型 API 的自定义基础 URL，适用于配置代理服务器或自托管模型。
    *   `LLM_TEMPERATURE`：调节大型语言模型输出结果的随机性程度。
    *   `LLM_NUM_RETRIES`：设定在大型语言模型 API 调用失败时的自动重试次数。
*   **智能体配置 (`AGENT_*` 前缀)：**
    *   `AGENT_ENABLE_BROWSING`：授权智能体运用浏览器工具执行网页搜索任务。
    *   `AGENT_DISABLED_MICROAGENTS`：以逗号分隔的列表，用于指定需要禁用的微型智能体。
*   **沙盒配置 (`SANDBOX_*` 前缀)：**
    *   `SANDBOX_BASE_CONTAINER_IMAGE`：作为智能体沙盒运行环境的基础 Docker 镜像。
    *   `SANDBOX_TIMEOUT`：设定沙盒内部操作的超时时限（单位：秒）。
    *   `SANDBOX_VOLUMES`：（详见下文）用于将宿主机本地目录挂载至沙盒内部（例如 `/host/path:/container/path:rw`）。
*   **安全配置 (`SECURITY_*` 前缀)：**
    *   `SECURITY_CONFIRMATION_MODE`：若设为 `true`，则在执行可能产生影响的操作（如执行命令、写入文件等）前，系统会请求用户确认。

若需获取所有可用配置选项的完整清单、各自的默认值以及详尽的功能说明，请参阅 OpenHands 项目代码库中的 `config.template.toml` 文件（可从 [GitHub](https://github.com/All-Hands-AI/OpenHands/blob/main/config.template.toml) 获取），或直接访问官方的[配置选项文档](https://docs.all-hands.dev/modules/usage/configuration-options)。

### 连接到您的本地文件系统

您可以授权 OpenHands 访问并修改您本地计算机上的文件，具体方法是将本地目录挂载到其运行的 Docker 容器内部。此功能对于在现有项目上开展工作尤为实用。

为此，您需要在启动 Docker 容器时配置 `SANDBOX_VOLUMES` 环境变量。其标准格式为 `宿主机路径:容器内路径[:模式]`。

**示例：**
```bash
export SANDBOX_VOLUMES="/path/to/your/local/project:/workspace:rw"

# 然后将其包含在您的 docker run 命令中：
docker run -it --rm --pull=always \
    -e SANDBOX_RUNTIME_CONTAINER_IMAGE=docker.all-hands.dev/all-hands-ai/runtime:0.39-nikolaik \
    -e LOG_ALL_EVENTS=true \
    -v /var/run/docker.sock:/var/run/docker.sock \
    -v ~/.openhands-state:/.openhands-state \
    -p 3000:3000 \
    --add-host host.docker.internal:host-gateway \
    -e SANDBOX_USER_ID=$(id -u) \
    -e SANDBOX_VOLUMES=$SANDBOX_VOLUMES \
    --name openhands-app \
    docker.all-hands.dev/all-hands-ai/openhands:0.39
```
*   `/path/to/your/local/project` 指向您宿主机上的实际目录路径。
*   `/workspace` 是该目录在 Docker 容器内部映射的路径，智能体将通过此路径访问相关文件。推荐将 `/workspace` 作为智能体的默认工作目录。
*   `:rw` (read-write) 代表读写访问模式；若仅需只读权限，可选用 `:ro` (read-only)。
*   通过 `-e SANDBOX_USER_ID=$(id -u)` 参数，可以确保智能体在挂载卷内创建的文件拥有与您宿主机用户相匹配的正确权限。

**敬请注意：** 授予文件系统访问权限，即意味着智能体有能力修改甚至删除所挂载目录中的文件。因此，请务必仅挂载来自可信任来源的目录，并充分理解所赋予的访问权限范围。

### 无头模式

OpenHands 支持在“无头模式”（Headless Mode）下运行，允许用户通过命令行界面直接与其交互，而无需启动图形化的 Web 用户界面。此模式非常适用于自动化脚本、批处理任务，或在不便使用 Web 浏览器的环境中运行 OpenHands。

**通过 Docker 运行无头模式：**
1.  预设必要的环境变量：`SANDBOX_VOLUMES`、`LLM_MODEL`、`LLM_API_KEY`。
2.  执行以下命令：
    ```bash
    docker run -it \
        --pull=always \
        -e SANDBOX_RUNTIME_CONTAINER_IMAGE=docker.all-hands.dev/all-hands-ai/runtime:0.39-nikolaik \
        -e SANDBOX_USER_ID=$(id -u) \
        -e SANDBOX_VOLUMES=$SANDBOX_VOLUMES \
        -e LLM_API_KEY=$LLM_API_KEY \
        -e LLM_MODEL=$LLM_MODEL \
        -e LOG_ALL_EVENTS=true \
        -v /var/run/docker.sock:/var/run/docker.sock \
        -v ~/.openhands-state:/.openhands-state \
        --add-host host.docker.internal:host-gateway \
        --name openhands-headless-$(date +%Y%m%d%H%M%S) \
        docker.all-hands.dev/all-hands-ai/openhands:0.39 \
        python -m openhands.core.main -t "此处填写您的任务描述"
    ```

关于无头模式下可用的命令行参数及其他配置选项的更多详情，请参阅官方的[无头模式文档](https://docs.all-hands.dev/modules/usage/how-to/headless-mode)。

### LLM 后端

OpenHands 通过集成 LiteLLM 库，得以支持连接至众多不同类型的大型语言模型 (LLM)。这种设计赋予了用户在选用最能满足其特定需求的模型时，极大的灵活性。

**推荐模型列表：**
*   `anthropic/claude-sonnet-4-20250514` (官方推荐)
*   `openai/o4-mini`
*   `gemini/gemini-2.5-pro`

您可以通过 OpenHands 的图形用户界面（位于 `Settings`（设置）区域）或设定相应的环境变量（如 `LLM_MODEL`、`LLM_API_KEY`、`LLM_BASE_URL` 等），来便捷地配置 LLM 提供商、指定模型、填入 API 密钥以及调整其他相关参数。

若需获取针对不同 LLM 提供商（例如 Azure、Google 或本地部署的 LLM）的详细配置指南，敬请参阅我们的 [LLM 配置文档](https://docs.all-hands.dev/modules/usage/llms)。

## 6. 故障排除

本节归纳了用户在安装、配置或使用 OpenHands 过程中可能遇到的若干常见问题，并针对性地提供了推荐的解决步骤。

*   **报错提示：`Launch docker client failed. Please make sure you have installed docker and started docker desktop/daemon.` (Docker 客户端启动失败。请确认您已正确安装 Docker 并已启动 Docker Desktop 或 Docker 守护进程。) **
    *   **解决步骤：**
        *   首先，请确认 Docker 服务是否已在您的系统中正常运行。您可以在终端执行 `docker ps` 命令进行检查。若 Docker 运行正常，该命令会列出当前活动的容器，且不应报错。
        *   若您正在使用 Docker Desktop，请打开其设置界面，访问 `Settings > Advanced`（设置 > 高级）选项卡，并确保已勾选 `Allow the default Docker socket to be used`（允许使用默认 Docker 套接字）选项。
        *   在部分 Docker Desktop 配置下，您可能还需启用 `Settings > Resources > Network > Enable host networking`（设置 > 资源 > 网络 > 启用主机网络）选项。
        *   若上述检查调整后问题依旧，请尝试重启 Docker Desktop。如重启无效，作为最终解决方案，可考虑重装 Docker Desktop。

*   **报错提示：`Permission Denied` (权限不足) 或 `PermissionError` (权限错误)，通常发生在 OpenHands 尝试访问特定文件或目录（例如 `~/.openhands-state` 目录或已挂载的工作区）时。**
    *   **解决步骤：**
        *   此类问题往往是由于 `~/.openhands-state` 目录在创建时被赋予了 root 用户权限。您可以通过变更该目录的所有权来解决：`sudo chown -R $(whoami):$(whoami) ~/.openhands-state`。
        *   作为替代方案，您也可以更宽泛地授予写入权限：`sudo chmod -R 777 ~/.openhands-state`。但从安全角度出发，通常更推荐采用变更所有权的方式。
        *   若您无需保留 OpenHands 先前的设置信息，直接删除该目录也是一个可行的选择：`sudo rm -rf ~/.openhands-state`。OpenHands 会在下次启动时以正确的权限自动重新创建此目录。
        *   若您通过 `SANDBOX_VOLUMES` 参数挂载了本地目录，请务必确保执行 Docker 命令的用户（即您的宿主机用户）对所指定的主机路径拥有充分的读写权限。同时，请确认在 `docker run` 命令中已正确添加 `SANDBOX_USER_ID=$(id -u)` 参数。

*   **现象描述：当通过非 `localhost` 的 URL（例如，局域网内的 IP 地址）访问 OpenHands 时，其用户界面中的 VS Code 编辑器选项卡显示“Forbidden”（禁止访问）错误，或无法正常加载。**
    *   **解决步骤：**
        *   此现象通常是由于 OpenHands 内嵌的 VS Code 实例运行在一个独立的、随机分配的高位端口上，而该端口可能并未对外暴露或无法经由当前网络路径访问。
        *   为解决此问题，您需要为 VS Code 指定一个固定的端口，并在执行 Docker 命令时确保该端口已被正确映射。
            1.  设置 `SANDBOX_VSCODE_PORT` 环境变量，为其分配一个明确的端口号（例如 `41234`）。
            2.  在您的 `docker run` 命令中，使用 `-p` 参数来映射此端口（例如 `-p 41234:41234`）。
            经过修改的 `docker run` 命令应包含如下片段：
            ```bash
            # 命令示例片段：
            # docker run ... -e SANDBOX_VSCODE_PORT=41234 -p 41234:41234 ...
            ```
        *   若您采用的是开发模式工作流（例如，通过 `poetry run` 启动 OpenHands），则可以直接在项目根目录下的 `config.toml` 文件中的 `[sandbox]` 配置区域设定此端口：`vscode_port = 41234`。

若需获取更为详尽的故障排查步骤，以及更广泛的潜在问题及其对应解决方案的清单，敬请参阅我们的官方[故障排除指南](https://docs.all-hands.dev/modules/usage/troubleshooting)。

## 7. 为 OpenHands 做贡献

OpenHands 是一个充满活力的、由社区共同驱动的开源项目。我们热忱欢迎并由衷感谢来自各方的任何形式的贡献。无论您是经验丰富的开发者、专注的研究人员，抑或是对人工智能与软件工程的融合充满热情的爱好者，都有众多途径可以参与到项目中来，并发挥您独特且有价值的作用。

### 如何参与贡献？
*   **试用 OpenHands 并提交问题反馈：** 若您在使用过程中发现任何程序错误（Bug）或有新的功能建议，欢迎随时在我们的 GitHub Issue 页面提交。
*   **提供使用反馈：** 每次与智能体交互会话结束后，通过点击“赞”或“踩”按钮来分享您的体验，这将帮助我们持续优化产品。
*   **改进代码库：** 通过提交拉取请求（Pull Request）的方式，您可以直接参与修复已知 Bug、实现新功能或完善现有文档。我们也为初次贡献者准备了标记为 “good first issues” 的任务。
*   **优化用户界面/用户体验 (UI/UX)：** 协助我们改进应用的外观设计与操作体验。若涉及较大范围的改动，建议先通过 Issue 或 Slack 渠道与社区进行讨论。
*   **提升智能体能力：** 参与修改和优化智能体的提示（Prompt）设计或其核心行为逻辑。请注意，对 `CodeActAgent` 这类核心智能体的改动，通常需要配合如 SWE-bench 等基准测试进行评估。
*   **开发新的智能体或运行时环境。**
*   **编写与完善测试用例：** 积极参与单元测试和集成测试的编写工作。

### 提交拉取请求 (Pull Request) 的流程
1.  首先，Fork OpenHands 的主代码仓库。
2.  基于您 Fork 的仓库，为您的修改内容创建一个新的分支。
3.  完成代码修改后，撰写清晰、规范的提交信息（Commit Message）并提交。
4.  将您创建的分支推送到您 Fork 的远程仓库。
5.  最后，从您 Fork 仓库的分支向 OpenHands 主仓库的相应分支发起一个拉取请求。

**拉取请求标题规范：**
请确保您的 PR 标题以标准前缀开头，如 `feat:`（新功能）、`fix:`（Bug修复）、`docs:`（文档相关）、`refactor:`（代码重构）等（例如：`feat(frontend): Add new login button`）。

### 社区交流
*   **Slack：** 加入我们的 [OpenHands Slack 工作区](https://join.slack.com/t/openhands-ai/shared_invite/zt-34zm4j0gj-Qz5kRHoca8DFCbqXPS~f_A)，这里是讨论项目研究、技术架构与未来开发方向的主要平台。
*   **Discord：** 您也可以加入由社区成员运营的 [Discord 服务器](https://discord.gg/ESHStjSjD4)参与交流。
*   **GitHub Issues：** 关注项目中已有的 Issue，或创建新的 Issue 来追踪问题和讨论方案。

若需了解关于如何为 OpenHands 项目贡献力量的更为详尽的信息——包括我们的编码规范、开发环境搭建指南、拉取请求处理流程以及社区行为准则等——敬请参阅位于 [OpenHands GitHub 代码库](https://github.com/All-Hands-AI/OpenHands)根目录下的 `CONTRIBUTING.md` 和 `COMMUNITY.md` 文件。我们热切期待您的加入与贡献！
---
