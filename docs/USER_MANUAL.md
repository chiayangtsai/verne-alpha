# User Manual

## 1. Introduction

Welcome to OpenHands, a platform for software development agents powered by AI. OpenHands agents can do anything a human developer can: modify code, run commands, browse the web, call APIs, and even copy code snippets from StackOverflow.

OpenHands is designed to assist with a wide range of software engineering tasks. It excels at "greenfield" projects (starting from scratch), adding new functionality to existing codebases, refactoring code in manageable chunks, and assisting with bug fixes.

Key features of the OpenHands interface include:

-   **Chat Panel**: Displays the interactive conversation between you and the OpenHands agent. This is where you provide instructions and receive feedback.
-   **Changes**: Shows a diff of file modifications made by the agent during its execution.
-   **VS Code**: An embedded VS Code editor instance, allowing you to browse the agent's working directory, view, and modify files. You can also use it to upload and download files.
-   **Terminal**: Provides a terminal interface within the agent's sandboxed environment, enabling both the agent and you to run commands.
-   **Jupyter**: Displays any Python commands executed by the agent within a Jupyter notebook interface. This is particularly useful for data visualization tasks.
-   **App**: If the agent starts a web server (e.g., a Flask or Node.js app), this tab will display the running application, allowing you to interact with it directly.
-   **Browser**: A non-interactive browser window that OpenHands uses to navigate websites and gather information from the web.

This manual will guide you through the process of setting up and effectively using OpenHands for your software development needs.

## 2. Getting Started

This section covers the initial setup required to get OpenHands running on your local system.

### System Requirements

- MacOS with Docker Desktop support
- Linux
- Windows with WSL and Docker Desktop support

A system with a modern processor and at least **4GB RAM** is recommended for a smooth experience.

### Prerequisites

Before you can run OpenHands, ensure that Docker Desktop is installed and correctly configured for your operating system. OpenHands runs its agent in a sandboxed Docker environment for security and consistency.

**System-Specific Docker Setup:**

*   **MacOS**:
    1.  Install [Docker Desktop on Mac](https://docs.docker.com/desktop/install/mac-install/).
    2.  Open Docker Desktop, navigate to `Settings > Advanced`, and ensure the option `Allow the default Docker socket to be used` is enabled. This allows OpenHands to communicate with the Docker daemon.

*   **Linux**:
    1.  Install [Docker Desktop on Linux](https://docs.docker.com/desktop/install/linux/). Ensure your user has permissions to manage Docker containers (usually by adding your user to the `docker` group).

*   **Windows**:
    1.  Install [WSL (Windows Subsystem for Linux)](https://learn.microsoft.com/en-us/windows/wsl/install). OpenHands is best supported on Windows via WSL 2.
    2.  In PowerShell, run `wsl --version` and confirm that your `Default Version` is `2`. If not, you may need to set WSL 2 as the default (`wsl --set-default-version 2`) or convert your existing WSL distribution.
    3.  Install [Docker Desktop on Windows](https://docs.docker.com/desktop/install/windows-install/).
    4.  During or after installation, configure Docker Desktop to use the WSL 2 based engine. This is typically found in `Settings > General`.
    5.  In Docker Desktop settings, navigate to `Resources > WSL Integration` and ensure that integration with your default WSL distro (or the specific distro you intend to use) is enabled.

### Start the OpenHands Application
Once Docker is set up, you can start the OpenHands application. The easiest way to do this is by using the provided Docker command:

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

Once the Docker container is running, OpenHands will be accessible at [http://localhost:3000](http://localhost:3000) in your web browser.

### Initial Setup

After launching OpenHands for the first time, you **must** configure a Large Language Model (LLM) for the agent to use:
1.  Select an `LLM Provider` from the available options (e.g., Anthropic, OpenAI).
2.  Choose a specific `LLM Model` from the selected provider (e.g., `anthropic/claude-sonnet-4-20250514` is a recommended model).
3.  Enter your `API Key` for the chosen LLM provider.

This configuration can be performed using the initial settings pop-up that appears on first launch, or at any time by clicking the `Settings` button (gear icon) in the OpenHands user interface.

**Getting an API Key:**

*   **Anthropic (Claude):**
    1.  [Create an Anthropic account](https://console.anthropic.com/).
    2.  [Generate an API key](https://console.anthropic.com/settings/keys).
    3.  [Set up billing](https://console.anthropic.com/settings/billing).
*   **OpenAI:**
    1.  [Create an OpenAI account](https://platform.openai.com/).
    2.  [Generate an API key](https://platform.openai.com/api-keys).
    3.  [Set up billing](https://platform.openai.com/account/billing/overview).

Once these steps are completed, you're ready to start using OpenHands!

## 3. Basic Usage

This section provides examples of how you can interact with OpenHands to perform common software development tasks. The key is to provide clear, specific prompts to the agent.

### "Hello World" Example

Start with a simple task to familiarize yourself with the agent's workflow:
> Write a bash script `hello.sh` that prints "hello world!"

OpenHands will attempt to write the script, set the necessary permissions, and execute it to verify the output. You can then iterate on this:
> Modify `hello.sh` so that it accepts a name as the first argument, but defaults to "world" if no argument is provided.

### Building From Scratch

OpenHands excels at "greenfield" projects where it can define the structure from the beginning. Be specific about your requirements and the technologies you want to use. For example:
> Build a frontend-only TODO app in React. All application state should be stored in `localStorage`.

Once the basic structure is in place, you can ask for refinements:
> Allow adding an optional due date to each TODO task.

It's good practice to version control your work. You can ask OpenHands to do this:
> Commit the current changes with the message "Add due date functionality" and push them to a new Git branch called `feature/due-dates`.

### Adding New Code to an Existing Project

OpenHands can integrate new features or components into an existing codebase. For example:
> Add a GitHub Action workflow that lints the Python code in this repository using Flake8.

For more complex additions, providing context about the existing codebase is crucial for better results:
> In the file `./backend/api/routes.js`, add a new GET route `/api/tasks` that retrieves and returns a list of all tasks from the database. Assume a PostgreSQL database and use the `pg` library for database interaction.

### Code Refactoring

You can use OpenHands to refactor code in manageable portions. Instead of asking for a complete overhaul, focus on specific improvements. For instance:
> Rename all single-letter variable names in the Go file `./app.go` to be more descriptive, while ensuring all tests still pass.

> In the PHP file `widget.php`, the function `build_and_deploy_widgets` is too long. Split it into two separate functions: `build_widgets` and `deploy_widgets`, maintaining the original functionality.

### Bug Fixing

OpenHands can assist in diagnosing and fixing bugs. It's most effective when you can provide context about the bug, including error messages or where in the code you suspect the issue lies. For example:
> The `/subscribe` API endpoint is incorrectly rejecting email addresses with `.io` domains. The relevant code is in `routes/subscribe.js`. Please fix this validation logic.

Test-driven development (TDD) can be very effective here. You can ask OpenHands to first write a failing test that reproduces the bug:
> The `hello` function in my Python script (`main.py`) crashes when given an empty string as input. Write a pytest test case in `tests/test_main.py` that demonstrates this bug. Then, modify the `hello` function to correctly handle empty strings (e.g., by returning an error or a default value) so that the new test passes.

### CLI Mode

OpenHands also offers an interactive Command-Line Interface (CLI).

**Running with Docker:**
1. Set environment variables: `SANDBOX_VOLUMES`, `LLM_MODEL`, `LLM_API_KEY`.
2. Run the Docker command:
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
       --name openhands-app-$(date +%Y%m%d%H%M%S) \
       docker.all-hands.dev/all-hands-ai/openhands:0.39 \
       python -m openhands.cli.main
   ```

**Interactive CLI Commands:**
- `/help`: Show available commands.
- `/exit`: Exit the application.
- `/init`: Initialize a new repository for agent exploration.
- `/status`: Show conversation details and usage metrics.
- `/new`: Start a new conversation.
- `/settings`: View and modify LLM/agent settings.
- `/resume`: Resume the agent if paused (Ctrl-P to pause).

For more detailed tips on how to write effective prompts, refer to the [Prompting Best Practices](https://docs.all-hands.dev/modules/usage/prompting/prompting-best-practices) documentation.
---

## 4. Common Use Cases

OpenHands is a versatile tool that can assist with a wide array of software engineering tasks. This section highlights some common scenarios where OpenHands can be particularly helpful by automating or augmenting developer workflows.

*   **Developing new features:** Start from scratch and build new applications or modules. For example, "Build a Python Flask backend for a blog with endpoints for creating, reading, and deleting posts."
*   **Writing scripts:** Automate tasks by generating scripts in various languages. For instance, "Write a Python script to organize files in a directory by their extension."
*   **Adding unit tests:** Improve code quality by asking OpenHands to write tests. Example: "Write a unit test for the `calculate_total` function in `billing.py`."
*   **Debugging code:** Provide OpenHands with error messages or descriptions of unexpected behavior to get help in identifying and fixing bugs. "The `user_login` function in `auth.py` is throwing a `NullPointerException` when the username is not found. Fix this."
*   **Refactoring code:** Improve code structure and readability. "Refactor the `process_data` function in `utils.py` to be more modular."
*   **Learning new technologies:** Ask OpenHands to generate examples or explain concepts. "Show me an example of how to make an API call in Go."
*   **Generating boilerplate code:** Speed up development by having OpenHands generate common code structures. "Create a basic HTML and CSS structure for a responsive webpage with a header, sidebar, and content area."
*   **Integrating APIs:** Ask OpenHands to write code to interact with third-party APIs. "Write a Javascript function to fetch user data from the GitHub API."

Remember to break down complex tasks into smaller, more manageable steps. Providing clear, specific prompts and relevant context will significantly improve the quality of the results you get from OpenHands.

## 5. Advanced Topics

This section delves into more advanced configuration options and operational modes for OpenHands, allowing for greater customization and flexibility.

OpenHands offers a range of configuration options to customize its behavior. These are primarily managed via environment variables passed to the Docker container or, for development setups, through a `config.toml` file.

**Key Configuration Areas (using environment variable names):**

*   **Core Configuration (`CORE_*`):**
    *   `MAX_ITERATIONS`: Maximum number of iterations an agent task can run.
    *   `DEFAULT_AGENT`: Specifies the default agent class to use (e.g., `CodeActAgent`).
*   **LLM Configuration (`LLM_*`):**
    *   `LLM_MODEL`: The specific LLM to use (e.g., `anthropic/claude-sonnet-4-20250514`).
    *   `LLM_API_KEY`: Your API key for the chosen LLM provider.
    *   `LLM_BASE_URL`: Custom base URL for LLM API, useful for proxies or self-hosted models.
    *   `LLM_TEMPERATURE`: Controls the randomness of the LLM's output.
    *   `LLM_NUM_RETRIES`: Number of times to retry a failing LLM API call.
*   **Agent Configuration (`AGENT_*`):**
    *   `AGENT_ENABLE_BROWSING`: Allows the agent to use a browser tool for web searches.
    *   `AGENT_DISABLED_MICROAGENTS`: A comma-separated list of micro-agents to disable.
*   **Sandbox Configuration (`SANDBOX_*`):**
    *   `SANDBOX_BASE_CONTAINER_IMAGE`: The base Docker image used for the agent's sandbox environment.
    *   `SANDBOX_TIMEOUT`: Timeout in seconds for operations within the sandbox.
    *   `SANDBOX_VOLUMES`: (As discussed below) Mounts local directories into the sandbox (e.g., `/path/host:/path/container:rw`).
*   **Security Configuration (`SECURITY_*`):**
    *   `SECURITY_CONFIRMATION_MODE`: If set to `true`, requires user confirmation for potentially impactful actions (like running commands or writing files).

For a comprehensive list of all available options, their default values, and detailed descriptions, consult the `config.template.toml` file (available on [GitHub](https://github.com/All-Hands-AI/OpenHands/blob/main/config.template.toml)) in the OpenHands repository, or refer to the official [Configuration Options documentation](https://docs.all-hands.dev/modules/usage/configuration-options).

### Connecting to Your Local Filesystem

You can allow OpenHands to access and modify files on your local system by mounting directories into its Docker container. This is particularly useful for working on existing projects.

To do this, use the `SANDBOX_VOLUMES` environment variable when running the Docker container. The format is `host_path:container_path[:mode]`.

**Example:**
```bash
export SANDBOX_VOLUMES="/path/to/your/local/project:/workspace:rw"

# Then include it in your docker run command:
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
*   `/path/to/your/local/project` is the directory on your host machine.
*   `/workspace` is the corresponding path inside the Docker container where the agent will access these files. It's recommended to use `/workspace` as the agent's default working directory.
*   `:rw` signifies read-write access. Use `:ro` for read-only access if preferred.
*   The `-e SANDBOX_USER_ID=$(id -u)` argument ensures that files created by the agent in the mounted volume have the correct user permissions, matching your host user.

**Caution:** Granting file system access means the agent can modify or delete files in the mounted directory. Always ensure you are mounting directories from trusted sources and understand the scope of access provided.

### Headless Mode

OpenHands can be run in "headless mode," which allows you to interact with it through a command-line interface without launching the web UI. This is useful for scripting, automation, or running OpenHands in environments where a web browser is not available.

**With Docker:**
1.  Set environment variables: `SANDBOX_VOLUMES`, `LLM_MODEL`, `LLM_API_KEY`.
2.  Run:
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
        --name openhands-app-headless-$(date +%Y%m%d%H%M%S) \
        docker.all-hands.dev/all-hands-ai/openhands:0.39 \
        python -m openhands.core.main -t "your task description here"
    ```

For more details on command-line arguments and options in headless mode, consult the official [Headless Mode documentation](https://docs.all-hands.dev/modules/usage/how-to/headless-mode).

### LLM Backends

OpenHands utilizes LiteLLM to connect to a wide variety of Large Language Models (LLMs). This provides flexibility in choosing the model that best suits your needs.

**Model Recommendations:**
*   `anthropic/claude-sonnet-4-20250514` (recommended)
*   `openai/o4-mini`
*   `gemini/gemini-2.5-pro`

You can configure the LLM provider, model, API key, and other parameters through the OpenHands UI (in `Settings`) or by setting environment variables (e.g., `LLM_MODEL`, `LLM_API_KEY`, `LLM_BASE_URL`).

For specific guides on configuring different LLM providers (such as Azure, Google, or local LLMs), please refer to the [LLMs documentation](https://docs.all-hands.dev/modules/usage/llms).

## 6. Troubleshooting

This section addresses some common issues that users might encounter while setting up or using OpenHands, along with recommended solutions.

*   **Error: `Launch docker client failed. Please make sure you have installed docker and started docker desktop/daemon.`**
    *   **Solution:**
        *   Verify that Docker is running. You can do this by opening a terminal and typing `docker ps`. If Docker is running, this command should list active containers without errors.
        *   If you are using Docker Desktop, navigate to `Settings > Advanced` and ensure the `Allow the default Docker socket to be used` option is enabled.
        *   In some Docker Desktop configurations, you might also need to enable `Settings > Resources > Network > Enable host networking`.
        *   If the issue persists, try restarting Docker Desktop. As a last resort, consider reinstalling Docker Desktop.

*   **Error: `Permission Denied` or `PermissionError` when OpenHands tries to access files or directories (e.g., `~/.openhands-state` or mounted workspaces).**
    *   **Solution:**
        *   This often happens if the `~/.openhands-state` directory was created with root privileges. You can fix this by changing its ownership: `sudo chown -R $(whoami):$(whoami) ~/.openhands-state`.
        *   Alternatively, you can grant write permissions more broadly: `sudo chmod -R 777 ~/.openhands-state`. However, changing ownership is generally preferred for security.
        *   If you don't need to preserve previous OpenHands settings, you can simply delete the directory: `sudo rm -rf ~/.openhands-state`. OpenHands will recreate it with the correct permissions on its next run.
        *   If you are mounting a local directory using `SANDBOX_VOLUMES`, ensure that the user running the Docker command (your host user) has appropriate read and write permissions for the specified host path. Also, ensure the `SANDBOX_USER_ID=$(id -u)` flag is used in your `docker run` command.

*   **Issue: The VS Code tab within the OpenHands UI shows a "Forbidden" error or fails to load when accessing OpenHands from a non-localhost URL (e.g., via a LAN IP address).**
    *   **Solution:**
        *   This typically occurs because the embedded VS Code instance runs on a separate, randomly assigned high port that might not be exposed or accessible over the network.
        *   To resolve this, you need to specify a fixed port for VS Code and ensure it's exposed in your Docker command.
            1.  Set the `SANDBOX_VSCODE_PORT` environment variable to a specific port number (e.g., `41234`).
            2.  Expose this same port in your `docker run` command using the `-p` flag (e.g., `-p 41234:41234`).
            Your modified `docker run` command would include:
            ```bash
            # Example snippet:
            # docker run ... -e SANDBOX_VSCODE_PORT=41234 -p 41234:41234 ...
            ```
        *   If you are running OpenHands using the development workflow (e.g., with `poetry run`), you can set this port in your `config.toml` file under the `[sandbox]` section: `vscode_port = 41234`.

For more detailed troubleshooting steps and a broader list of potential issues and their solutions, please consult the official [Troubleshooting Guide](https://docs.all-hands.dev/modules/usage/troubleshooting).

## 7. Contributing to OpenHands

OpenHands is a vibrant, community-driven project. We enthusiastically welcome and appreciate contributions of all kinds. Whether you're a developer, a researcher, or simply passionate about the intersection of AI and software engineering, there are numerous ways to get involved and make a meaningful impact.

### How Can I Contribute?
*   **Use OpenHands and report issues:** If you find bugs or have feature requests, please create an issue on GitHub.
*   **Send feedback:** Use the thumbs-up/thumbs-down buttons after each session to help us improve.
*   **Improve the codebase:** Submit Pull Requests for bug fixes, new features, or documentation improvements. Check for "good first issues" on GitHub.
*   **UI/UX:** Help improve the application's look and feel. For major changes, please discuss in an issue or on Slack first.
*   **Agent improvements:** Modify prompts or agent behavior. Changes to core agents like `CodeActAgent` require evaluation (e.g., using SWE-bench).
*   **Add new agents or runtimes.**
*   **Write tests:** Add unit or integration tests.

### Sending Pull Requests
1.  Fork the repository.
2.  Create a new branch for your changes.
3.  Make your changes and commit them with a descriptive message.
4.  Push your branch to your fork.
5.  Open a Pull Request against the main OpenHands repository.

**PR Title Format:**
Start with a prefix like `feat:`, `fix:`, `docs:`, `refactor:`, etc. (e.g., `feat(frontend): Add new button`).

### Community
*   **Slack:** Join the [OpenHands Slack workspace](https://join.slack.com/t/openhands-ai/shared_invite/zt-34zm4j0gj-Qz5kRHoca8DFCbqXPS~f_A) for discussions on research, architecture, and development.
*   **Discord:** Join the [community-run Discord server](https://discord.gg/ESHStjSjD4).
*   **GitHub Issues:** Check out existing issues or create new ones.

For more detailed information on how to contribute, including coding standards, the development environment setup, our pull request process, and community guidelines, please refer to the `CONTRIBUTING.md` and `COMMUNITY.md` files available in the root of the [OpenHands GitHub repository](https://github.com/All-Hands-AI/OpenHands). We look forward to your contributions!
---
