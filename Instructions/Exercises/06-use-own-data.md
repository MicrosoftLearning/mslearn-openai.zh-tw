---
lab:
  title: 實作如何搭配 Azure OpenAI 服務使用檢索增強生成 (RAG)
---

# 實作搭配 Azure OpenAI 服務使用擷取擴增世代 (RAG)

Azure OpenAI 服務可讓您搭配基礎 LLM 的智慧使用自己的資料。 您可將模型限制為，只針對相關主題使用您的資料，或將它與預先訓練模型的結果混合。

在此練習的案例中，您將擔任服務於 Margie's Travel Agency 軟體開發人員的角色。 您將探索如何使用 Azure AI 搜尋服務來編製自己的資料索引，並將其與 Azure OpenAI 搭配使用，以增強提示。

此練習大約需要 **30** 分鐘。

## 佈建 Azure 資源

若要完成此練習，您將需要：

- Azure OpenAI 資源。
- Azure AI 搜尋服務資源。
- Azure 儲存體帳戶資源。

1. 登入 **Azure 入口網站**，網址為：`https://portal.azure.com`。
2. 使用下列設定建立 **Azure OpenAI** 資源：
    - **訂用帳戶**：*選取已核准存取 Azure OpenAI 服務的 Azure 訂用帳戶*
    - **資源群組**：*選擇或建立資源群組*
    - **區域**：*從以下任一區域中**隨機**選擇*\*
        - 澳大利亞東部
        - 加拿大東部
        - 美國東部
        - 美國東部 2
        - 法國中部
        - 日本東部
        - 美國中北部
        - 瑞典中部
        - 瑞士北部
        - 英國南部
    - **名稱**：*您選擇的唯一名稱*
    - **定價層**:標準 S0

    > \* Azure OpenAI 資源受到區域配額的限制。 列出的區域包括本練習中使用的模型類型的預設配額。 在與其他使用者共用訂用帳戶的情況下，隨機選擇區域可以降低單一區域達到其配額限制的風險。 如果練習稍後達到配額限制，您可能需要在不同的區域中建立另一個資源。

3. 佈建 Azure OpenAI 資源時，請使用下列設定建立 **Azure AI 搜尋服務**資源：
    - **訂用帳戶**：*您已佈建 Azure OpenAI 資源的訂用帳戶*
    - **資源群組**：*您已佈建 Azure OpenAI 資源的資源群組*
    - **服務名稱**：*您選擇的唯一名稱*
    - **位置**：*您已佈建 Azure OpenAI 資源的區域*
    - **定價層**：基本
4. 佈建 Azure AI 搜尋服務資源時，請使用下列設定建立**儲存體帳戶**資源：
    - **訂用帳戶**：*您已佈建 Azure OpenAI 資源的訂用帳戶*
    - **資源群組**：*您已佈建 Azure OpenAI 資源的資源群組*
    - **儲存體帳戶名稱**：*您選擇的唯一名稱*。
    - **區域**：*您已佈建 Azure OpenAI 資源的區域*
    - **效能**：標準
    - **備援**：本地備援儲存體 (LRS)
5. 在 Azure 訂用帳戶中成功部署這三個資源之後，請在 Azure 入口網站 中檢閱這些資源，並收集下列資訊 (稍後在練習中需要此資訊)：
    - 您所建立 Azure OpenAI 資源的**端點**和**金鑰** (可在 Azure 入口網站中 Azure OpenAI 資源的 [金鑰和端點]**** 頁面上取得)
    - Azure AI 搜尋服務的端點 (Azure 入口網站中 Azure AI 搜尋資源概觀頁面的 **URL** 值)。
    - Azure AI 搜尋資源的**主要系統管理金鑰** (可在 Azure 入口網站中 Azure AI 搜尋服務資源的 [金鑰]**** 頁面中取得。

## 上傳您的資料

您即將使用自己的資料，以生成式 AI 模型作為您使用提示的基礎。 在此練習中，資料包含來自 *Margies Travel* 虛構公司的大量旅遊摺頁冊。

1. 在新的瀏覽器索引標籤中，從 `https://aka.ms/own-data-brochures` 下載摺頁冊資料的封存。 將摺頁冊解壓縮到您電腦的資料夾。
1. 在 Azure 入口網站中，瀏覽至儲存體帳戶，然後檢視 [儲存體瀏覽器]**** 頁面。
1. 選取 [Blob 容器]****，然後新增名為 `margies-travel` 的新容器。
1. 選取 **margies-travel** 容器，然後將您先前擷取的 .pdf 摺頁冊上傳至 Blob 容器的根資料夾。

## 部署 AI 模型

您即將在此練習中使用兩個 AI 模型：

- 文字內嵌模型，其可將摺頁冊中的文字 *向量化*，以便有效率地編製索引，用於作為提示基礎。
- GPT 模型，應用程式可使用該模型，針對以資料為依據基礎的提示產生回應。

若要部署這些模型，請使用 AI Studio。

1. 在 Azure 入口網站中，瀏覽至 Azure OpenAI 資源。 接著，使用連結來開啟 ** Azure AI Studio**中的資源。
1. 在 Azure AI Studio 的 [部署]**** 頁面上，檢視現有的模型部署。 然後，使用下列設定建立 **text-embedding-ada-002** 模型的新基本模型部署：
    - **部署名稱**：text-embedding-ada-002
    - **模型版本**：*預設版本*
    - **部署類型**：標準
    - **每分鐘權杖速率限制**：5K\*
    - **內容篩選**：預設
    - **啟用動態配額**：啟用
1. 部署文字內嵌模型之後，返回 [部署]**** 頁面，然後使用下列設定建立 ** gpt-35-turbo-16k** 模型的新部署：
    - **部署名稱**：gpt-35-turbo-16k
    - **模型**：gpt-35-turbo-16k *(如果無法取得 16k 模型，請選擇 gpt-35-turbo)*
    - **模型版本**：*預設版本*
    - **部署類型**：標準
    - **每分鐘權杖速率限制**：5K\*
    - **內容篩選**：預設
    - **啟用動態配額**：啟用

    > \* 每分鐘 5,000 個權杖的速率限制已足以完成此練習，同時還有剩餘容量可讓其他人使用相同的訂用帳戶。

## 建立索引

為了能在提示中輕鬆使用自己的資料，請使用 Azure AI 搜尋服務對其編製索引。 使用先前在編製索引流程期間部署的文字內嵌模型，來將文字資料*向量化* (這會導致索引中的每個文字語彙基元都以數值向量表示，使其能與生成式 AI 模型呈現文字的方式相容)

1. 在 Azure 入口網站中，瀏覽至 Azure AI 搜尋服務資源。
1. 在 [概觀]**** 頁面上，選取 [匯入並向量化資料]****。
1. 在 [設定資料連線]**** 頁面中，選取 [Azure Blob 儲存體]****，然後使用下列設定來設定資料來源：
    - **訂用帳戶**：您已佈建儲存體帳戶的 Azure 訂用帳戶。
    - **儲存體帳戶**：您先前建立的儲存體帳戶。
    - **Blob 容器**：margies-travel
    - **Blob 資料夾**：*保留空白*
    - **啟用刪除追蹤**：未選取
    - **使用受控識別驗證**：未選取
1. 在 [將文字向量化] **** 頁面上，選取下列設定：
    - **種類**：Azure OpenAI
    - **訂用帳戶**：您已佈建 Azure OpenAI 服務的 Azure 訂用帳戶。
    - **Azure OpenAI 服務**：Azure OpenAI 服務資源
    - **模型部署**：text-embedding-ada-002
    - **驗證類型**：API 金鑰
    - **我確認連線到 Azure OpenAI 服務帳戶會對我的帳戶產生額外費用**：已選取
1. 在下一個頁面上，請<u>不要</u>選取 toptions，來將影像向量化或使用 AI 技能擷取資料。
1. 在下一個頁面上，啟用語意排名，然後排程索引器執行一次。
1. 在最後一個頁面上，將 [物件名稱前置詞]**** 設定為 `margies-index`，然後建立索引。

## 準備在 Visual Studio Code 中開發應用程式

現在來探索，在使用 Azure OpenAI 服務 SDK 的應用程式中，使用自己的資料的情況。 您將使用 Visual Studio Code 開發應用程式。 您應用程式的程式碼檔案已在 GitHub 存放庫中提供。

> **秘訣**：如果您已複製 **mslearn-openai** 存放庫，請在 Visual Studio Code 中開啟它。 否則，請遵循下列步驟將其複製到您的開發環境。

1. 啟動 Visual Studio Code。
2. 開啟選擇區 (SHIFT+CTRL+P) 並執行 **Git：複製 ** 命令，將 `https://github.com/MicrosoftLearning/mslearn-openai` 存放庫複製到本機資料夾 (哪個資料夾無關緊要)。
3. 複製存放庫後，請在 Visual Studio Code 中開啟此資料夾。

    > **注意**：如果 Visual Studio Code 顯示快顯訊息，提示您信任您所開啟的程式碼，請按一下快顯項目中的 [是，我信任作者]**** 選項。

4. 等候其他檔案安裝以支援存放庫中的 C# 程式碼專案。

    > **注意**：如果系統提示您新增必要的資產來組建和偵錯，請選取 [現在不要]****。

## 設定您的應用程式

已提供 C# 和 Python 的應用程式，而且兩個應用程式都具有相同的功能。 首先，您將完成應用程式的一些重要部分，以開始使用您的 Azure OpenAI 資源。

1. 在 Visual Studio Code 的 [總管]**** 窗格中，瀏覽至 **Labfiles/06-use-own-data** 資料夾，然後根據您的使用語言展開 **CSharp** 或 **Python** 資料夾。 每個資料夾都包含應用程式的特定語言檔案，您將在其中整合 Azure OpenAI 功能。
2. 以滑鼠右鍵按一下包含程式碼檔案的 [CSharp]**** 或 [Python]**** 資料夾，然後開啟整合式終端。 然後，針對您的語言喜好設定執行適當的命令，以安裝 Azure OpenAI SDK 套件：

    **C#：**

    ```
    dotnet add package Azure.AI.OpenAI --version 1.0.0-beta.14
    ```

    **Python**：

    ```
    pip install openai==1.13.3
    ```

3. 在 [總管]**** 窗格的 **CSharp** 或 **Python** 資料夾中，開啟使用者慣用的介面語言的設定檔

    - **C#**：appsettings.json
    - **Python**：.env
    
4. 更新設定值以包含：
    - 您所建立 Azure OpenAI 資源的**端點**和**金鑰** (可在 Azure 入口網站中 Azure OpenAI 資源的 [金鑰和端點]**** 頁面上取得)
    - 您為模型部署指定的**部署名稱** (可在 Azure AI Studio 的 [部署]**** 頁面中取得)。
    - 搜尋服務的端點 (Azure 入口網站中搜尋資源概觀頁面的 **URL** 值)。
    - 搜尋資源的 **金鑰** (可在 Azure 入口網站中搜尋資源的 [金鑰]** **頁面找到 - 您可以使用其中一個系統管理金鑰)
    - 搜尋索引的名稱 (應該是 `margies-index`)。
5. 儲存組態檔。

### 新增程式碼以使用 Azure OpenAI 服務

現在您已準備好使用 Azure OpenAI SDK 來取用已部署的模型。

1. 在 [總管]** **窗格中，於 **CSharp** 或 **Python** 資料夾中，開啟使用者慣用的介面語言的程式碼檔案，並以程式碼取代註解***設定資料來源***，新增 Azure OpenAI SDK 程式庫：

    **C#**：ownData.cs

    ```csharp
    // Configure your data source
    AzureSearchChatExtensionConfiguration ownDataConfig = new()
    {
            SearchEndpoint = new Uri(azureSearchEndpoint),
            Authentication = new OnYourDataApiKeyAuthenticationOptions(azureSearchKey),
            IndexName = azureSearchIndex
    };
    ```

    **Python**：ownData.py

    ```python
    # Configure your data source
    extension_config = dict(dataSources = [  
            { 
                "type": "AzureCognitiveSearch", 
                "parameters": { 
                    "endpoint":azure_search_endpoint, 
                    "key": azure_search_key, 
                    "indexName": azure_search_index,
                }
            }]
        )
    ```

2. 檢閱其餘程式碼，留意在用來提供資料來源設定相關資訊之要求本文中使用的*延伸項目*。

3. 將變更儲存至程式碼檔案。

## 執行您的應用程式

現在您的應用程式設定好了，請執行程式，將要求傳送至您的模型，並觀察回應。 您會發現，提示內容是不同選項之間的唯一差異，每項要求的所有其他參數 (例如權杖計數和溫度) 都保持不變。

1. 在互動式終端窗格中，確定資料夾內容是您慣用語言的資料夾。 然後輸入下列命令來執行應用程式。

    - **C#**：`dotnet run`
    - **Python**：`python ownData.py`

    > **秘訣**：您可以使用終端工具列中的**最大化面板大小** (**^**) 圖示來查看更多的主控台文字。

2. 檢閱提示 `Tell me about London` 的回應，其中應該包含答案，以及用來建立提示基礎之資料的詳細資料，這些是從搜尋服務取得的資料。

    > **秘訣**：如果您要從搜尋索引查看引文，請將程式碼檔案頂端附近的變數***顯示引文***設定為 **true**。

## 清理

當您完成使用 Azure OpenAI 資源時，請記得在 `https://portal.azure.com` 刪除 **Azure 入口網站**中的資源。 此外，請務必包含儲存體帳戶和搜尋資源，因為它們可能產生相對龐大的成本。
