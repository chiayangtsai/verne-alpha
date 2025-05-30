# 使用者手冊

## 1. 簡介

歡迎使用 OpenHands，這是一個由人工智能（AI）驅動的軟體開發智能體（Agent）平台。OpenHands 智能體能夠執行人類開發人員的各項任務：修改程式碼、執行指令、瀏覽網頁、呼叫 API，甚至從 StackOverflow 複製貼上程式碼片段。

OpenHands 旨在協助處理各類軟體工程任務。它尤其擅長「綠地」專案（即從零開始的新專案）、為現有程式碼庫添加新功能、將程式碼重構為更易管理的程式碼區塊，以及輔助修復程式錯誤（Bug）。

OpenHands 介面的主要特性包括：

-   **聊天面板 (Chat Panel)**：顯示您與 OpenHands 智能體之間的即時對話。您可在此處發出指令並接收回饋。
-   **變更記錄 (Changes)**：展示智能體在執行任務期間對檔案所做修改的差異 (Diff) 比較。
-   **VS Code 編輯器 (VS Code)**：內建一個 VS Code 編輯器實例，方便您瀏覽智能體的工作目錄、檢視及修改檔案，同時也支援檔案的上傳與下載。
-   **終端機 (Terminal)**：在智能體的沙箱 (Sandbox) 環境中提供一個命令列終端機介面，允許智能體和使用者執行指令。
-   **Jupyter 筆記本 (Jupyter)**：在 Jupyter Notebook 使用者介面中展示智能體執行過的所有 Python 指令，這對資料視覺化等任務尤為實用。
-   **應用程式 (App)**：若智能體啟動了 Web 伺服器（例如 Flask 或 Node.js 應用程式），此分頁將展示正在執行的應用程式，方便您直接進行互動測試。
-   **瀏覽器 (Browser)**：一個非互動式瀏覽器視窗，OpenHands 智能體可利用它存取網站並從網際網路收集資訊。

本手冊旨在引導您順利完成 OpenHands 的安裝設定，並高效地將其應用於您的軟體開發工作中。

## 2. 開始入門

本節涵蓋在您的本機系統上執行 OpenHands 所需的初始設定。

### 系統需求

-   MacOS（須支援 Docker Desktop）
-   Linux
-   Windows（須支援 WSL 和 Docker Desktop）

為確保流暢執行，我們推薦您的系統配備現代處理器以及至少 **4GB 記憶體**。

### 先決條件

在執行 OpenHands 之前，請務必確保您的作業系統已安裝並正確設定 Docker Desktop。OpenHands 會在沙箱化 (sandboxed) 的 Docker 環境中執行其智能體，以此保障系統的安全性和執行的一致性。

**各作業系統的 Docker 特定設定：**

*   **MacOS**：
    1.  安裝 [適用於 Mac 的 Docker Desktop](https://docs.docker.com/desktop/install/mac-install/)。
    2.  開啟 Docker Desktop，前往 `Settings > Advanced`（設定 > 進階）分頁，確認已勾選 `Allow the default Docker socket to be used`（允許使用預設 Docker 通訊端）選項。此設定確保 OpenHands 能與 Docker 守護进程（Daemon）正常通訊。

*   **Linux**：
    1.  安裝 [適用於 Linux 的 Docker Desktop](https://docs.docker.com/desktop/install/linux/)。請確保您的使用者帳戶擁有管理 Docker 容器的權限（通常是將使用者加入 `docker` 使用者群組）。

*   **Windows**：
    1.  安裝 [WSL (Windows Subsystem for Linux)](https://learn.microsoft.com/en-us/windows/wsl/install)。OpenHands 在 Windows 平台上主要透過 WSL 2 提供支援。
    2.  在 PowerShell 中執行 `wsl --version` 指令，確認 `Default Version`（預設版本）是否為 `2`。若非如此，您可能需要將 WSL 2 設為預設版本（執行 `wsl --set-default-version 2`）或轉換您已有的 WSL 發行版。
    3.  安裝 [適用於 Windows 的 Docker Desktop](https://docs.docker.com/desktop/install/windows-install/)。
    4.  安裝期間或安裝完成後，請設定 Docker Desktop 以採用基於 WSL 2 的引擎。此選項通常位於 `Settings > General`（設定 > 一般）中。
    5.  在 Docker Desktop 的設定中，找到 `Resources > WSL Integration`（資源 > WSL 整合）部分，確保已啟用與您的預設 WSL 發行版（或您計劃使用的特定發行版）的整合。

### 啟動 OpenHands 應用程式
完成 Docker 的設定後，您便可以啟動 OpenHands 應用程式。最簡便的方式是採用我們提供的 Docker 指令：

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

Docker 容器成功執行後，您即可在本機 Web 瀏覽器中透過存取 [http://localhost:3000](http://localhost:3000) 來使用 OpenHands。

### 初始化設定

首次啟動 OpenHands 時，您 **必須** 為智能體設定一個大型語言模型 (LLM)，以便其正常運作：
1.  從可選清單中選擇一個 `LLM Provider`（LLM 供應商），例如 Anthropic、OpenAI 等。
2.  從該供應商處選擇一個特定的 `LLM Model`（LLM 模型），例如推薦的 `anthropic/claude-sonnet-4-20250514`。
3.  輸入您所選 LLM 供應商的 `API Key`（API 金鑰）。

您可以在首次啟動時彈出的設定視窗中完成此項設定，或在任何時候透過點擊 OpenHands 使用者介面中的 `Settings`（設定）按鈕（通常為一個齒輪圖示）來進行調整。

**取得 API 金鑰：**

*   **Anthropic (Claude):**
    1.  [建立一個 Anthropic 帳戶](https://console.anthropic.com/)。
    2.  [產生一個 API 金鑰](https://console.anthropic.com/settings/keys)。
    3.  [設定帳單資訊](https://console.anthropic.com/settings/billing)。
*   **OpenAI:**
    1.  [建立一個 OpenAI 帳戶](https://platform.openai.com/)。
    2.  [產生一個 API 金鑰](https://platform.openai.com/api-keys)。
    3.  [設定帳單資訊](https://platform.openai.com/account/billing/overview)。

完成這些步驟後，您就可以開始使用 OpenHands 了！

## 3. 基本用法

本小節列舉了若干範例，旨在示範如何與 OpenHands 智能體互動以完成常見的軟體開發工作。核心要點在於向智能體下達清晰且特定的指令。

### 「Hello World」入門範例

不妨從一個簡單的任務開始，以便快速熟悉智能體的工作模式：
> 請編寫一個名為 `hello.sh` 的 bash 指令稿，該指令稿能夠列印輸出 "hello world!"。

OpenHands 將嘗試建立此指令稿，為其賦予必要的執行權限，並執行以檢驗輸出結果。隨後，您可以基於此成果進行迭代優化：
> 請修改 `hello.sh` 指令稿，使其能接受一個名稱作為第一個命令列參數；如果未提供參數，則預設輸出 "world"。

### 從零開始建置新專案

OpenHands 在「綠地」專案（即無歷史遺留程式碼的專案）中表現出色，因為它能夠從頭規劃並搭建專案架構。此時，您需要明確您的需求以及期望採用的技術堆疊。例如：
> 請使用 React 技術堆疊建置一個純前端的待辦事項（TODO）應用程式。應用的所有狀態資料均應儲存在瀏覽器的 `localStorage` 中。

基礎框架搭建完畢後，您可以進一步提出優化需求：
> 請為每個待辦事項任務增加一個可選的截止日期功能。

在開發過程中，勤於版本控制是一個良好的習慣。您可以指示 OpenHands 來完成這項工作：
> 請將目前的所有程式碼變更提交，提交訊息為 "Add due date functionality"，並將這些變更推送至一個名為 `feature/due-dates` 的新 Git 分支。

### 向現有專案加入新程式碼

OpenHands 能夠將新的特性或程式碼模組無縫整合到已有的程式碼庫中。例如：
> 請為本專案加入一個 GitHub Action 工作流程，該流程需使用 Flake8 工具對倉庫中的 Python 程式碼執行 Lint 檢查。

若要進行更複雜的程式碼加入，提供充足的現有程式碼庫上下文資訊，將有助於智能體產生更理想的結果：
> 請在 `./backend/api/routes.js` 檔案中新增一個 GET 路由 `/api/tasks`。此路由需能從資料庫中擷取並傳回所有任務的列表。假設專案後端採用 PostgreSQL 資料庫，並使用 `pg` 套件進行資料庫操作。

### 程式碼重構

您可以利用 OpenHands 對程式碼進行分階段、模組化的重構。相較於一次性提出大規模的修改需求，聚焦於特定的改進點会更为有效。例如：
> 請將 Go 語言檔案 `./app.go` 中的所有單字母變數名替換為更具描述性的名稱，並確保此變更不会导致任何现有测试失败。

> PHP 檔案 `widget.php` 内的 `build_and_deploy_widgets` 函數邏輯過於冗長。請将其拆分为 `build_widgets` 和 `deploy_widgets` 兩個獨立的函數，同時保持原有的核心功能不變。

### Bug 修復 (Bug Fixing)

OpenHands 能夠協助您診斷並修復程式碼中的 Bug。若您能提供關於 Bug 的詳細上下文資訊，例如錯誤訊息、相關的程式碼片段或您對問題根源的初步判斷，智能體的修復效果會更佳。例如：
> `/subscribe` API 端點目前錯誤地拒絕了使用 `.io` 頂級域名的電子郵件地址。相關的業務邏輯程式碼位于 `routes/subscribe.js` 檔案中。請修復此驗證缺陷。

在此類場景下，採用測試驅動開發（TDD）的模式會非常高效。您可以先要求 OpenHands 編寫一個能夠重現該 Bug 的失敗測試案例：
> 我在 Python 指令稿 (`main.py`) 中定義的 `hello` 函數，當接收到空字串作為輸入時會引發程式崩潰。請先在 `tests/test_main.py` 檔案中編寫一個 pytest 測試案例来準確地重現這個 Bug。隨後，請修改 `hello` 函數的實作，使其能夠妥善處理空字串輸入（例如，傳回一個特定的錯誤提示或一個預設的預設值），并确保新编写的测试案例能够顺利通过。

### 命令列界面 (CLI) 模式

OpenHands 同時提供了一個互動式的命令列界面（CLI），方便開發者在終端機環境中使用。

**透過 Docker 執行 CLI：**
1.  設定必要的環境變數：`SANDBOX_VOLUMES`、`LLM_MODEL`、`LLM_API_KEY`。
2.  執行以下 Docker 指令：
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

**CLI 互動指令：**
-   `/help`：顯示所有可用的互動指令及其說明。
-   `/exit`：退出 CLI 應用程式。
-   `/init`：為智能體初始化一個新的程式碼倉庫以供探索。
-   `/status`：顯示目前會話的詳細資訊及用量統計。
-   `/new`：開啟一個全新的會話。
-   `/settings`：檢視並修改目前的 LLM 及智能體相關設定。
-   `/resume`：若智能體處於暫停狀態，使用此指令可使其恢復執行（在智能體執行時可按 `Ctrl-P` 暫停）。

若需瞭解更多關於如何編寫高效指令以獲得理想輸出的技巧，請参閱[提示工程最佳實務](https://docs.all-hands.dev/modules/usage/prompting/prompting-best-practices)文件。
---

## 4. 常見應用場景

OpenHands 是一款功能強大的多用途工具，能夠輔助開發人員完成各類軟體工程相關的任務。本節將重點介紹若干 OpenHands 在自動化或增強開發者工作流方面表現尤為出色的典型應用場景。

*   **開發新功能：** 從全新的專案開始，逐步建置應用程式或模組。例如：「請為我的部落格網站建置一個基於 Python Flask 框架的後端服務，要求包含用於建立、讀取和刪除博文的 API 端點。」
*   **編寫自動化指令稿：** 透過產生不同程式語言的指令稿，實現常見任務的自動化。例如：「請編寫一個 Python 指令稿，用於根據副檔名來整理指定目錄下的所有檔案。」
*   **加入單元測試：** 指示 OpenHands 為現有程式碼編寫單元測試，以提升程式碼品質與強健性。例如：「請為 `billing.py` 檔案中的 `calculate_total` 函數編寫相應的單元測試案例。」
*   **除錯程式碼：** 向 OpenHands 提供詳細的錯誤訊息或程式非預期行為的描述，輔助定位並修復 Bug。例如：「`auth.py` 檔案中的 `user_login` 函數，在使用者不存在時會拋出 `NullPointerException` 例外。請修復此問題。」
*   **程式碼重構：** 改進現有程式碼的結構，提升其可讀性與可維護性。例如：「请重构 `utils.py` 檔案中的 `process_data` 函數，使其邏輯更加模組化。」
*   **學習新技術：** 要求 OpenHands 產生特定技術的程式碼範例或解釋相關概念。例如：「請給我展示一個在 Go 語言中如何發起 API 呼叫的程式碼範例。」
*   **產生樣板程式碼：** 利用 OpenHands 快速產生常見的程式碼結構，從而加速專案啟動與開發進程。例如：「請為包含頁首、側邊欄和主內容區域的響應式網頁，產生基礎的 HTML 和 CSS 結構程式碼。」
*   **整合第三方 API：** 指示 OpenHands 編寫與外部服務 API 對接的程式碼。例如：「請編寫一個 JavaScript 函數，用於從 GitHub API 獲取指定使用者的資訊。」

請謹記，將複雜任務拆解為一系列更小、更易於管理的步驟，並向 OpenHands 提供清晰、特定的指令以及相關的上下文資訊，這將顯著提升最終輸出結果的品質。

## 5. 進階功能與設定

本小節將深入探討 OpenHands 提供的一些進階設定選項与特殊操作模式，旨在賦予使用者更大程度的自訂能力和使用上的靈活性。

OpenHands 提供了一系列豐富的設定選項，允許使用者根據自身需求自訂其行為。這些設定主要透過兩種方式進行管理：一是透過傳遞給 Docker 容器的環境變數；二是在開發環境中，透過編輯 `config.toml` 檔案來實現。

**主要設定參數（以環境變數形式列出）：**

*   **核心設定 (`CORE_*` 前綴)：**
    *   `MAX_ITERATIONS`：設定智能體執行單個任務時允許的最大迭代次數。
    *   `DEFAULT_AGENT`：指定系統預設使用的智能體類別（例如 `CodeActAgent`）。
*   **LLM 設定 (`LLM_*` 前綴)：**
    *   `LLM_MODEL`：指定所用的大型語言模型（例如 `anthropic/claude-sonnet-4-20250514`）。
    *   `LLM_API_KEY`：您選用的大型語言模型供應商的 API 金鑰。
    *   `LLM_BASE_URL`：大型語言模型 API 的自訂基礎 URL，適用於設定代理伺服器或自託管模型。
    *   `LLM_TEMPERATURE`：調節大型語言模型輸出結果的隨機性程度。
    *   `LLM_NUM_RETRIES`：設定在大型語言模型 API 呼叫失敗時的自動重試次數。
*   **智能體設定 (`AGENT_*` 前綴)：**
    *   `AGENT_ENABLE_BROWSING`：授權智能體運用瀏覽器工具執行網頁搜尋任務。
    *   `AGENT_DISABLED_MICROAGENTS`：以逗號分隔的列表，用於指定需要停用的微型智能體。
*   **沙箱設定 (`SANDBOX_*` 前綴)：**
    *   `SANDBOX_BASE_CONTAINER_IMAGE`：作為智能體沙箱執行環境的基礎 Docker 映像檔。
    *   `SANDBOX_TIMEOUT`：設定沙箱內部操作的逾時時限（單位：秒）。
    *   `SANDBOX_VOLUMES`：（詳見下文）用於將主機本機目錄掛載至沙箱內部（例如 `/host/path:/container/path:rw`）。
*   **安全設定 (`SECURITY_*` 前綴)：**
    *   `SECURITY_CONFIRMATION_MODE`：若設為 `true`，則在執行可能產生影響的操作（如執行指令、寫入檔案等）前，系統會請求使用者確認。

若需獲取所有可用設定選項的完整清單、各自的預設值以及詳盡的功能說明，請參閱 OpenHands 專案程式碼庫中的 `config.template.toml` 檔案（可從 [GitHub](https://github.com/All-Hands-AI/OpenHands/blob/main/config.template.toml) 取得），或直接存取官方的[設定選項文件](https://docs.all-hands.dev/modules/usage/configuration-options)。

### 連接到您的本機檔案系統

您可以授權 OpenHands 存取並修改您本機電腦上的檔案，具體方法是將本機目錄掛載到其執行的 Docker 容器內部。此功能對於在現有專案上开展工作尤为实用。

為此，您需要在啟動 Docker 容器時設定 `SANDBOX_VOLUMES` 環境變數。其標準格式為 `主機路徑:容器內路徑[:模式]`。

**範例：**
```bash
export SANDBOX_VOLUMES="/path/to/your/local/project:/workspace:rw"

# 然後將其包含在您的 docker run 指令中：
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
*   `/path/to/your/local/project` 指向您主機上的實際目錄路徑。
*   `/workspace` 是該目錄在 Docker 容器內部對映的路徑，智能體將透過此路徑存取相關檔案。推薦將 `/workspace` 作為智能體的預設工作目錄。
*   `:rw` (read-write) 代表讀寫存取模式；若僅需唯讀權限，可選用 `:ro` (read-only)。
*   透過 `-e SANDBOX_USER_ID=$(id -u)` 參數，可以确保智能體在掛載磁碟區内建立的檔案擁有與您主機使用者相符的正確權限。

**敬請注意：** 授予檔案系統存取權限，即意味著智能體有能力修改甚至刪除所掛載目錄中的檔案。因此，請務必僅掛載來自可信任來源的目錄，並充分理解所賦予的存取權限範圍。

### 無頭模式

OpenHands 支援在「無頭模式」(Headless Mode) 下執行，允許使用者透過命令列介面直接與其互動，而無需啟動圖形化的 Web 使用者介面。此模式非常適用於自動化指令稿、批次處理任務，或在不便使用 Web 瀏覽器的環境中執行 OpenHands。

**透過 Docker 執行無頭模式：**
1.  預設必要的環境變數：`SANDBOX_VOLUMES`、`LLM_MODEL`、`LLM_API_KEY`。
2.  執行以下指令：
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
        python -m openhands.core.main -t "此處填寫您的任務描述"
    ```

關於無頭模式下可用的命令列參數及其他設定選項的更多詳情，請參閱官方的[無頭模式文件](https://docs.all-hands.dev/modules/usage/how-to/headless-mode)。

### LLM 後端

OpenHands 透過整合 LiteLLM 函式庫，得以支援連接至眾多不同類型的大型語言模型 (LLM)。這種設計賦予了使用者在選用最能滿足其特定需求的模型時，極大的靈活性。

**推薦模型列表：**
*   `anthropic/claude-sonnet-4-20250514` (官方推薦)
*   `openai/o4-mini`
*   `gemini/gemini-2.5-pro`

您可以在 OpenHands 的圖形使用者界面（位於 `Settings`（設定）區域）或設定相應的環境變數（如 `LLM_MODEL`、`LLM_API_KEY`、`LLM_BASE_URL` 等），來便捷地設定 LLM 供應商、指定模型、填入 API 金鑰以及調整其他相關參數。

若需獲取針對不同 LLM 供應商（例如 Azure、Google 或本機部署的 LLM）的詳細設定指南，敬請參閱我們的 [LLM 設定文件](https://docs.all-hands.dev/modules/usage/llms)。

## 6. 故障排除

本節歸納了使用者在安裝、設定或使用 OpenHands 過程中可能遇到的若干常見問題，並針對性地提供了推薦的解決步驟。

*   **錯誤提示：`Launch docker client failed. Please make sure you have installed docker and started docker desktop/daemon.` (Docker 客戶端啟動失敗。請確認您已正確安裝 Docker 並已啟動 Docker Desktop 或 Docker 守護行程。) **
    *   **解決步驟：**
        *   首先，請確認 Docker 服務是否已在您的系統中正常執行。您可以在終端機執行 `docker ps` 指令進行檢查。若 Docker 執行正常，該指令會列出目前活動的容器，且不應報錯。
        *   若您正在使用 Docker Desktop，請開啟其設定介面，前往 `Settings > Advanced`（設定 > 進階）分頁，並確保已勾選 `Allow the default Docker socket to be used`（允許使用預設 Docker 通訊端）選項。
        *   在部分 Docker Desktop 設定下，您可能還需啟用 `Settings > Resources > Network > Enable host networking`（設定 > 資源 > 網路 > 啟用主機網路）選項。
        *   若上述檢查調整後問題依舊，請嘗試重新啟動 Docker Desktop。如重啟無效，作為最終解決方案，可考慮重裝 Docker Desktop。

*   **錯誤提示：`Permission Denied` (權限不足) 或 `PermissionError` (權限錯誤)，通常发生在 OpenHands 尝试存取特定档案或目录（例如 `~/.openhands-state` 目录或已掛載的工作区）时。**
    *   **解決步驟：**
        *   此類問題往往是由于 `~/.openhands-state` 目錄在建立時被賦予了 root 使用者權限。您可以透過變更該目錄的所有權来解決：`sudo chown -R $(whoami):$(whoami) ~/.openhands-state`。
        *   作為替代方案，您也可以更寬泛地授予寫入權限：`sudo chmod -R 777 ~/.openhands-state`。但從安全角度出发，通常更推薦採用變更所有權的方式。
        *   若您無需保留 OpenHands 先前的設定資訊，直接刪除該目錄也是一個可行的選擇：`sudo rm -rf ~/.openhands-state`。OpenHands 會在下次啟動時以正確的權限自動重新建立此目錄。
        *   若您透過 `SANDBOX_VOLUMES` 參數掛載了本機目錄，请务必确保执行 Docker 指令的使用者（即您的主機使用者）对所指定的主机路径拥有充分的读写權限。同时，请确认在 `docker run` 指令中已正确加入 `SANDBOX_USER_ID=$(id -u)` 參數。

*   **現象描述：当透过非 `localhost` 的 URL（例如，区域网路内的 IP 位址）存取 OpenHands 时，其使用者界面中的 VS Code 编辑器分页显示「Forbidden」（禁止存取）错误，或无法正常加载。**
    *   **解決步驟：**
        *   此現象通常是由于 OpenHands 内嵌的 VS Code 實例执行在一个独立的、随机分配的高位连接埠上，而该连接埠可能并未对外暴露或无法经由目前网路路径存取。
        *   为解决此问题，您需要为 VS Code 指定一个固定的连接埠，并在执行 Docker 指令时确保该连接埠已被正确对映。
            1.  設定 `SANDBOX_VSCODE_PORT` 環境變數，為其分配一個明確的連接埠號（例如 `41234`）。
            2.  在您的 `docker run` 指令中，使用 `-p` 參數來對映此連接埠（例如 `-p 41234:41234`）。
            经过修改的 `docker run` 指令应包含如下片段：
            ```bash
            # 指令範例片段：
            # docker run ... -e SANDBOX_VSCODE_PORT=41234 -p 41234:41234 ...
            ```
        *   若您采用的是开发模式工作流（例如，透过 `poetry run` 启动 OpenHands），则可以直接在专案根目录下的 `config.toml` 档案中的 `[sandbox]` 設定区域设定此连接埠：`vscode_port = 41234`。

若需获取更为详尽的故障排除步骤，以及更广泛的潜在问题及其对应解决方案的清单，敬请参阅我们的官方[故障排除指南](https://docs.all-hands.dev/modules/usage/troubleshooting)。

## 7. 為 OpenHands 做貢獻

OpenHands 是一個充滿活力的、由社群共同驅動的開源專案。我們熱忱歡迎並由衷感謝來自各方的任何形式的貢獻。無論您是經驗豐富的開發者、專注的研究人員，抑或是對人工智能與軟體工程的融合充滿熱情的愛好者，都有眾多途徑可以參與到專案中來，並發揮您獨特且有價值的作用。

### 如何參與貢獻？
*   **試用 OpenHands 並提交問題回饋：** 若您在使用過程中发现任何程式错误（Bug）或有新的功能建议，欢迎随时在我们的 GitHub Issue 页面提交。
*   **提供使用回饋：** 每次与智能体互动会话结束后，透过点击「赞」或「踩」按钮来分享您的体验，这将帮助我们持续优化产品。
*   **改進程式碼庫：** 透過提交拉取請求（Pull Request）的方式，您可以直接参与修复已知 Bug、实现新功能或完善现有文件。我们也为初次贡献者准备了标记为 “good first issues” 的任务。
*   **優化使用者介面/使用者體驗 (UI/UX)：** 协助我们改进应用的外观设计与操作体验。若涉及较大范围的改动，建议先透过 Issue 或 Slack 管道与社群进行讨论。
*   **提升智能體能力：** 参与修改和優化智能体的提示（Prompt）设计或其核心行为逻辑。请注意，对 `CodeActAgent` 这类核心智能体的改动，通常需要配合如 SWE-bench 等基准测试进行评估。
*   **開發新的智能體或執行環境。**
*   **編寫與完善測試案例：** 积极参与单元测试和整合测试的编写工作。

### 提交拉取請求 (Pull Request) 的流程
1.  首先，Fork OpenHands 的主程式碼倉庫。
2.  基於您 Fork 的倉庫，為您的修改内容建立一個新的分支。
3.  完成程式碼修改後，撰寫清晰、规范的提交訊息（Commit Message）并提交。
4.  将您建立的分支推送到您 Fork 的遠端倉庫。
5.  最后，从您 Fork 倉庫的分支向 OpenHands 主倉庫的相应分支发起一个拉取请求。

**拉取請求標題規範：**
请确保您的 PR 标题以标准字首开头，如 `feat:`（新功能）、`fix:`（Bug修復）、`docs:`（文件相关）、`refactor:`（程式碼重構）等（例如：`feat(frontend): Add new login button`）。

### 社群交流
*   **Slack：** 加入我们的 [OpenHands Slack 工作區](https://join.slack.com/t/openhands-ai/shared_invite/zt-34zm4j0gj-Qz5kRHoca8DFCbqXPS~f_A)，这里是讨论专案研究、技术架构与未来开发方向的主要平台。
*   **Discord：** 您也可以加入由社群成员營運的 [Discord 伺服器](https://discord.gg/ESHStjSjD4)参与交流。
*   **GitHub Issues：** 关注专案中已有的 Issue，或建立新的 Issue 来追踪问题和讨论方案。

若需瞭解關於如何為 OpenHands 專案貢獻力量的更為詳盡的資訊——包括我們的編碼標準、開發環境搭建指南、我們的拉取請求流程以及社群行為準則等——敬请参阅位於 [OpenHands GitHub 程式碼倉庫](https://github.com/All-Hands-AI/OpenHands)根目錄下的 `CONTRIBUTING.md` 和 `COMMUNITY.md` 檔案。我們熱切期待您的加入与贡献！
---

[end of docs/USER_MANUAL.zh_TW.md]
