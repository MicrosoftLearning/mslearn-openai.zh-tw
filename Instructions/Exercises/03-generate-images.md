---
lab:
  title: 使用 DALL-E 模型產生影像
---

# 使用 DALL-E 模型產生影像

Azure OpenAI 服務包含名為 DALL-E 的影像產生模型。 您可以使用此模型提交描述所需影像的自然語言提示，而模型會根據您提供的描述產生原始影像。

在此練習中，您會使用 DALL-E 版本 3 模型來根據自然語言提示產生影像。

此練習大約需要 **25** 分鐘。

## 佈建 Azure OpenAI 資源

您必須先在 Azure 訂用帳戶中佈建 Azure OpenAI 資源，才能使用 Azure OpenAI 模型來產生影像。 資源必須位於支援 DALL-E 模型的區域中。

1. 登入 **Azure 入口網站**，網址為：`https://portal.azure.com`。
1. 使用下列設定建立 **Azure OpenAI** 資源：
    - **訂用帳戶**：*選取已核准存取 Azure OpenAI 服務 (包括 DALL-E) 的 Azure 訂用帳戶*
    - **資源群組**：*選擇或建立資源群組*
    - **區域**：*從**美國東部**、**澳洲東部**或**瑞典中部***\*隨意選擇
    - **名稱**：*您選擇的唯一名稱*
    - **定價層**:標準 S0

    > \* DALL-E 3 模型只適用於**美國東部**、**瑞典中部區域**和**瑞典中部**的 Azure OpenAI 服務資源。

1. 等候部署完成。 然後移至 Azure 入口網站中已部署的 Azure OpenAI 資源。

## 部署模型

接著，您就可以從 CLI，建立 **dalle3**模型的部署。 在 Azure 入口網站中，從上方功能表列選取 **Cloud Shell** 圖示，確定已將終端機設定為 **Bash**。 使用自己的資料值，參考此範例，取代取代下列變數：

```dotnetcli
az cognitiveservices account deployment create \
   -g *your resource group* \
   -n *your Open AI resource* \
   --deployment-name dall-e-3 \
   --model-name dall-e-3 \
   --model-version 3.0  \
   --model-format OpenAI \
   --sku-name "Standard" \
   --sku-capacity 1
```

    > \* Sku-capacity is measured in thousands of tokens per minute. A rate limit of 1,000 tokens per minute is more than adequate to complete this exercise while leaving capacity for other people using the same subscription.


## 使用 REST API 來產生影像

Azure OpenAI 服務提供 REST API，可讓您用來提交內容產生的提示，包括 DALL-E 模型所產生的影像。

### 準備在 Visual Studio Code 中開發應用程式

現在讓我們來探索如何建置使用 Azure OpenAI 服務的自訂應用程式來產生影像。 您將使用 Visual Studio Code 開發應用程式。 您應用程式的程式碼檔案已在 GitHub 存放庫中提供。

> **秘訣**：如果您已複製 **mslearn-openai** 存放庫，請在 Visual Studio Code 中開啟它。 否則，請遵循下列步驟將其複製到您的開發環境。

1. 啟動 Visual Studio Code。
2. 開啟選擇區 (SHIFT+CTRL+P) 並執行 **Git：複製 ** 命令，將 `https://github.com/MicrosoftLearning/mslearn-openai` 存放庫複製到本機資料夾 (哪個資料夾無關緊要)。
3. 複製存放庫後，請在 Visual Studio Code 中開啟此資料夾。

    > **注意**：如果 Visual Studio Code 顯示快顯訊息，提示您信任您所開啟的程式碼，請按一下快顯項目中的 [是，我信任作者]**** 選項。

4. 等候其他檔案安裝以支援存放庫中的 C# 程式碼專案。

    > **注意**：如果系統提示您新增必要的資產來組建和偵錯，請選取 [現在不要]****。

### 設定您的應用程式

已提供 C# 和 Python 的應用程式。 這兩個應用程式都有相同的功能。 首先，您會將 Azure OpenAI 資源的端點和金鑰新增至應用程式的組態檔。

1. 在 Visual Studio Code 的 **[總管]** 窗格中，瀏覽至 **[Labfiles/03-image-generation]** 資料夾，然後根據您的使用語言，展開 **[CSharp]** 或 **[Python]** 資料夾。 每個資料夾都包含應用程式的特定語言檔案，您將在其中整合 Azure OpenAI 功能。
2. 在 [總管]**** 窗格中的 [CSharp]**** 或 [Python]**** 資料夾中，開啟您慣用語言的組態檔

    - **C#**：appsettings.json
    - **Python**：.env
    
3. 更新組態值以包含您所建立之 Azure OpenAI 資源的**端點**和**金鑰** (可在 Azure 入口網站中 Azure OpenAI 資源的 [金鑰和端點]**** 頁面上取得)。
4. 儲存組態檔。

### 檢視應用程式程式碼

現在您已準備好探索用來呼叫 REST API 並產生影像的程式碼。

1. 在 [總管]**** 窗格中，選取應用程式的主要程式碼檔案：

    - C#: `Program.cs`
    - Python： `generate-image.py`

2. 檢閱檔案所包含的程序碼，注意如下主要功能：
    - 程式碼會向服務的端點 (包括標頭中服務的金鑰) 提出 HTTPS 要求，。 這兩個值都是從組態檔取得。
    - 要求包含一些參數，包括影像應根據的提示、要產生影像的數量以及已產生影像的大小。
    - 回應包括 DALL-E 模型從使用者提供的提示中推斷以使其更具描述性的已修改提示，以及已產生影像的 URL。
    
    > **重要**：如果您將部署命名為建議的 *dalle3* 以外的任何名稱，則必須更新程式碼才能使用部署的名稱。

### 執行應用程式

現在您已檢閱程式碼，是時候來執行程式碼並產生一些影像。

1. 以滑鼠右鍵按一下包含程式碼檔案的 [CSharp]**** 或 [Python]**** 資料夾，然後開啟整合式終端。 然後輸入適當命令來執行您的應用程式：

   **C#**
   ```
   dotnet run
   ```
   
   **Python**
   ```
   pip install requests
   python generate-image.py
   ```

3. 出現提示時，請輸入影像的描述。 例如*長頸鹿放風箏*。

4. 等候影像產生 - 終端窗格中會顯示超連結。 然後選取超連結以開啟新的瀏覽器分頁，並檢閱產生的影像。

   > **提示**：如果應用程式未傳回回應，請稍後一分鐘然後再試一次。 新部署的資源最多可能需要 5 分鐘的時間才能使用。

5. 關閉包含產生影像的瀏覽器分頁，然後使用不同的提示重新執行應用程式以產生新影像。

## 清理

當您完成 Azure OpenAI 資源時，請記得在 `https://portal.azure.com` 刪除 **Azure 入口網站**中的資源。
