---
lab:
  title: 使用 AI 產生影像
  description: 瞭解如何使用 DALL-E OpenAI 模型產生影像。
  status: new
---

# 使用 AI 產生影像

在本練習中，您將使用 OpenAI DALL-E 生成式 AI 模型來產生影像。 您將使用 Azure AI Foundry 和 Azure OpenAI 服務來開發應用程式。

本練習大約需要 **30** 分鐘的時間。

## 建立 Azure AI Foundry 專案

讓我們從建立 Azure AI Foundry 專案開始。

1. 在網頁瀏覽器中，開啟 [Azure AI Foundry 入口網站](https://ai.azure.com) 於`https://ai.azure.com` 並使用您的 Azure 認證登入。 關閉首次登入時開啟的所有提示或快速啟動窗格，如有必要，使用左上角的 **Azure AI Foundry** 標誌瀏覽到首頁，首頁類似於下圖：

    ![Azure AI Foundry 入口網站螢幕擷取畫面。](../media/ai-foundry-home.png)

1. 在首頁中，選取 **+ 建立專案**。
1. 在**建立專案**精靈中，輸入合適的專案名稱（例如，`my-ai-project`），然後檢閱將自動建立以支援您的專案的 Azure 資源。
1. 選取**自訂**，然後為您的中樞指定下列設定：
    - **中樞名稱**： *唯一名稱 - 例如 `my-ai-hub`*
    - **訂用帳戶**：您的 Azure 訂用帳戶**
    - **資源群組**：*建立一個具有唯一名稱的新資源群組（例如，`my-ai-resources`），或選取一個現有資源群組*
    - **位置**：選取 [協助我選擇]****，然後在 [位置協助程式] 視窗中選取 [dalle]****，然後使用建議的區域\*
    - **連接 Azure AI 服務或 Azure OpenAI**：*使用適當的名稱建立新的 AI 服務資源（例如 `my-ai-services`）或使用現有資源*
    - **連接 Azure AI 搜尋服務**：跳過連接

    > \*Azure OpenAI 資源在租用戶等級受到區域配額的限制。 如果練習稍後達到配額限制，您可能需要在不同的區域中建立另一個資源。

1. 選取**下一步**並檢閱您的設定。 然後選取**建立**並等待該流程完成。
1. 建立專案後，關閉顯示的所有提示並檢閱 Azure AI Foundry 入口網站中的專案頁面，該頁面應類似於下圖：

    ![Azure AI Foundry 入口網站中 Azure AI 專案詳細資料的螢幕螢幕擷取畫面。](../media/ai-foundry-project.png)

## 部署 DALL-E 模型

現在您已準備好部署 DALL-E 模型以支援影像產生。

1. 在 Azure AI Foundry 專案頁面右上角的工具列中，使用**預覽功能**圖示啟用 **將模型部署到 Azure AI 模型推斷服務**功能。
1. 在專案左側窗格中的 [我的資產]**** 區段中，選取 [模型 + 端點]**** 頁面。
1. 在 [模型 + 端點]**** 頁面中，於 [模型部署]**** 索引標籤的 [+ 部署模型]**** 功能表中，選取 [部署基本模型]****。
1. 在清單中搜尋 **dall-e-3** 模型，然後選取並確認。
1. 如果出現提示，請同意授權合約，然後透過在部署詳細資料中選取 [自訂]**** 來使用以下設定部署模型：
    - **部署名稱**：*模型部署的唯一名稱 - 例如 `dall-e-3`（記住您指定的名稱，稍後會需要它*）
    - **部署類型**：標準
    - **部署詳細資料**： *使用預設設定*
1. 等待部署配置狀態為 [完成]****。

## 在遊樂場中測試模型。

在建立用戶端應用程式之前，讓我們在遊樂場中測試 DALL-E 模型。

1. 在您部署的 DALL-E 模型頁面中，選取 **[在遊樂場中開啟]**（或在 [遊樂場]**** 頁面中，開啟 [影像遊樂場]****。
1. 確定已選取您的 DALL-E 模型部署。 然後，在 [提示]** 方塊**中，輸入提示，例如 `Create an image of an robot eating spaghetti`。
1. 在遊樂場中檢閱產生的影像：

    ![影像遊樂場的螢幕擷取畫面，其中包含產生的影像。](../media/images-playground.png)

1. 輸入後續提示，例如 `Show the robot in a restaurant` 並檢閱產生的影像。
1. 繼續使用新的提示進行測試，以精簡影像，直到您滿意為止。

## 建立用戶端應用程式

該模型似乎在遊樂場中發揮作用。 現在您可以使用 Azure OpenAI SDK 在用戶端應用程式中使用。

> **提示**：您可以選擇使用 Python 或 Microsoft C# 開發解決方案。 請遵循您所選語言的相應章節中的說明進行操作。

### 準備應用程式組態

1. 在 Azure AI Foundry 入口網站中，檢視專案的**概觀**頁面。
1. 在 [專案詳細資料]**** 區域中，記下 [專案連接字串]****。 您將使用此連接字串連線到用戶端應用程式中的專案。
1. 開啟一個新的瀏覽器索引標籤（保持 Azure AI Foundry 入口網站在現有索引標籤中開啟）。 然後在新索引標籤中，瀏覽到 `https://portal.azure.com` 的 [Azure 入口網站](https://portal.azure.com)；如果出現提示，請使用您的 Azure 認證登入。
1. 使用頁面頂部搜尋欄右側的 **[\>_]** 按鈕在 Azure 入口網站中建立一個新的 Cloud Shell，並選擇 ***PowerShell*** 環境。 Cloud Shell 會在 Azure 入口網站底部的窗格顯示命令列介面。

    > **注意**：如果您之前建立了使用 *Bash* 環境的 Cloud Shell，請將其切換到 ***PowerShell***。

1. 在 Cloud Shell 工具列中，在**設定**功能表中，選擇**轉到經典版本**（這是使用程式碼編輯器所必需的）。

    > **提示**：當您將命令貼到 Cloud Shell 中時，輸出可能會佔用大量的螢幕緩衝區。 您可以透過輸入 `cls` 命令來清除螢幕，以便更輕鬆地專注於每個工作。

1. 在 PowerShell 窗格中，輸入以下命令來複製此練習的 GitHub 存放庫：

    ```
    rm -r mslearn-openai -f
    git clone https://github.com/microsoftlearning/mslearn-openai mslearn-openai
    ```

> **注意**：現在按照您選擇的程式設計語言的步驟進行操作。

1. 複製存放庫之後，瀏覽至包含應用程式碼檔案的資料夾：  

    **Python**

    ```
   cd mslearn-openai/Labfiles/03-image-generation/Python
    ```

    **C#**

    ```
   cd mslearn-openai/Labfiles/03-image-generation/CSharp
    ```

1. 在 Cloud Shell 命令列窗格中，輸入下列命令來安裝您將使用的程式庫：

    **Python**

    ```
   pip install python-dotenv azure-identity azure-ai-projects openai requests
    ```

    *您可以忽略 PIP 版本和本機路徑的相關錯誤*

    **C#**

    ```
   dotnet add package Azure.Identity
   dotnet add package Azure.AI.Projects --prerelease
   dotnet add package Azure.AI.OpenAI
    ```

1. 輸入以下命令，編輯已提供的設定檔：

    **Python**

    ```
   code .env
    ```

    **C#**

    ```
   code appsettings.json
    ```

    程式碼編輯器中會開啟檔案。

1. 在程式碼檔案中，將 **your_project_endpoint** 預留位置替換為您的專案的連接字串（從 Azure AI Foundry 入口網站中的專案 [概觀]**** 頁面複製），並將 **your_model_deployment** 預留位置替換為您指派至 dall-e-3 模型部署的名稱。
1. 取代預留位置後，使用 **CTRL+S** 命令儲存變更，然後使用 **CTRL+Q** 命令關閉程式碼編輯器，同時保持 Cloud Shell 命令列開啟。

### 撰寫程式碼以連線至您的專案，並與模型聊天

> **提示**：新增程式碼時，請確保保持正確的縮排。

1. 輸入以下命令，編輯已提供的\程式碼檔案：

    **Python**

    ```
   code dalle-client.py
    ```

    **C#**

    ```
   code Program.cs
    ```

1. 在程式碼檔案中，記下檔案頂端新增的現有語句，以匯入必要的 SDK 命名空間。 然後，在註解 [新增引用]**** 下，新增以下程式碼來引用之前安裝的庫中的命名空間：

    **Python**

    ```
   from dotenv import load_dotenv
   from azure.identity import DefaultAzureCredential
   from azure.ai.projects import AIProjectClient
   from openai import AzureOpenAI
   import requests
    ```

    **C#**

    ```
   using Azure.Identity;
   using Azure.AI.Projects;
   using Azure.AI.OpenAI;
   using OpenAI.Images;
    ```

1. 在 **main** 函式中，在註解 [取得組態設定]**** 下，請注意程式碼會載入您在設定檔中定義的專案連接字串和模型部署名稱值。
1. 在註解 [初始化項目用戶端]**** 下，新增以下程式碼，使用您目前登入的 Azure 認證連接到您的 Azure AI Foundry 專案：

    **Python**

    ```
   project_client = AIProjectClient.from_connection_string(
        conn_str=project_connection,
        credential=DefaultAzureCredential())
    ```

    **C#**

    ```
   var projectClient = new AIProjectClient(project_connection,
                        new DefaultAzureCredential());
    ```

1. 在註解 [取得 OpenAI 用戶端]**** 下，新增下列程式碼以建立用戶端物件來與模型聊天：

    **Python**

    ```
   openai_client = project_client.inference.get_azure_openai_client(api_version="2024-06-01")

    ```

    **C#**

    ```
   ConnectionResponse connection = projectClient.GetConnectionsClient().GetDefaultConnection(ConnectionType.AzureOpenAI, withCredential: true);

   var connectionProperties = connection.Properties as ConnectionPropertiesApiKeyAuth;

   AzureOpenAIClient openAIClient = new(
        new Uri(connectionProperties.Target),
        new AzureKeyCredential(connectionProperties.Credentials.Key));

   ImageClient openAIimageClient = openAIClient.GetImageClient(model_deployment);

    ```

1. 請注意，程式碼包含迴圈，可讓使用者輸入提示，直到他們輸入 "quit" 為止。 然後在執行迴圈區段的註解 [產生影像]**** 下，新增下列程式碼以提交提示，並從您的模型擷取所產生影像的 URL：

    **Python**

    ```python
   result = openai_client.images.generate(
        model=model_deployment,
        prompt=input_text,
        n=1
    )

    json_response = json.loads(result.model_dump_json())
    image_url = json_response["data"][0]["url"] 
    ```

    **C#**

    ```
   var imageGeneration = await openAIimageClient.GenerateImageAsync(
            input_text,
            new ImageGenerationOptions()
            {
                Size = GeneratedImageSize.W1024xH1024
            }
   );
   imageUrl= imageGeneration.Value.ImageUri;
    ```

1. 請注意，**main** 函式其餘部分的程式碼將影像 URL 和檔案名稱傳遞給提供的函式，該函式下載產生的影像並將其儲存為 .png 檔案。

1. 使用 **CTRL+S** 命令儲存對程式碼檔案的變更，然後使用 **CTRL+Q** 命令關閉程式碼編輯器，同時保持雲端 shell 命令列開啟。

### 執行用戶端應用程式

1. 在 Cloud Shell 命令列窗格中，輸入下列命令來執行應用程式：

    **Python**

    ```
   python dalle-client.py
    ```

    **C#**

    ```
   dotnet run
    ```

1. 出現提示時，請輸入影像的要求，例如 `Create an image of a robot eating pizza`。 片刻之後，應用程式應確認影像已儲存。
1. 嘗試更多提示。 完成後，輸入 `quit` 可結束程式。

    > **注意**：在這個簡單的應用程式中，我們還沒有實現保留交談記錄的邏輯；因此模型會將每個提示視為一個新要求，而沒有前一個提示的上下文。

1. 若要下載和檢視您的應用程式產生的影像，請在 Cloud Shell 窗格的工具列中使用 [上傳/下載檔案] **** 按鈕下載檔案，然後開啟它。 若要下載檔案，請在下載介面填寫其檔案路徑；例如：

    **Python**

    /home/*user*`/mslearn-openai/Labfiles/03-image-generation/Python/images/image_1.png`

    **C#**

    /home/*user*`/mslearn-openai/Labfiles/03-image-generation/CSharp/images/image_1.png`

## 摘要

在本練習中，您使用 Azure AI Foundry 和 Azure OpenAI SDK 建立了一個用戶端應用程式，該應用程式使用 DALL-E 模型來產生影像。

## 清理

如果您已完成 DALL-E 探索，您應該刪除在本練習中建立的資源，以避免產生不必要的 Azure 成本。

1. 返回包含 Azure 入口網站的瀏覽器索引標籤 (或在新的瀏覽器索引標籤中重新開啟位於`https://portal.azure.com`的 [Azure 入口網站](https://portal.azure.com))，並檢視您在其中部署本練習所用資源的資源群組內容。
1. 在工具列上，選取 [刪除資源群組]****。
1. 輸入資源群組名稱並確認您想要將其刪除。
