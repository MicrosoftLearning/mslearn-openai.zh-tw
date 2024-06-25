---
lab:
  title: 實作如何搭配 Azure OpenAI 服務使用檢索增強生成 (RAG)
---

# 實作搭配 Azure OpenAI 服務使用擷取擴增世代 (RAG)

Azure OpenAI 服務可讓您搭配基礎 LLM 的智慧使用自己的資料。 您可將模型限制為，只針對相關主題使用您的資料，或將它與預先訓練模型的結果混合。

在此練習的案例中，您將擔任服務於 Margie's Travel Agency 軟體開發人員的角色。 您將探索如何使用生成式 AI，讓編碼工作更容易且更有效率。 練習中使用的技術可以套用至其他程式碼檔案、程序設計語言和使用案例。

此練習大約需要 **20** 分鐘。

## 佈建 Azure OpenAI 資源

如果您還未擁有，請在 Azure 訂用帳戶中佈建 Azure OpenAI 資源。

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

3. 等候部署完成。 然後移至 Azure 入口網站中已部署的 Azure OpenAI 資源。

## 部署模型

Azure OpenAI 提供名為 **Azure OpenAI Studio** 的 Web 入口網站，可讓您用來部署、管理及探索模型。 您將使用 Azure OpenAI Studio 來部署模型，以開始探索 Azure OpenAI。

1. 在 Azure OpenAI 資源的 [概觀]**** 頁面上，使用 [移至 Azure OpenAI Studio]**** 按鈕在新瀏覽器索引標籤中開啟 Azure OpenAI Studio。
2. 在 Azure OpenAI Studio 的 [部署]**** 頁面上，檢視現有的模型部署。 如果您還未擁有，請使用下列設定建立 **gpt-35-turbo-16k** 模型的新部署：
    - **模型**：gpt-35-turbo-16k * (必須是此模型才能使用此練習中的功能)*
    - **模型版本**：自動更新為預設值
    - **部署名稱**：*您選擇的唯一名稱。您稍後會在實驗室中使用此名稱。*
    - **進階選項**
        - **內容篩選**：預設
        - **部署類型**：標準
        - **每分鐘權杖速率限制**：5K\*
        - **啟用動態配額**：啟用

    > \* 每分鐘 5,000 個權杖的速率限制已足以完成此練習，同時還有剩餘容量可讓其他人使用相同的訂用帳戶。

## 不新增您自己的資料，只觀察一般聊天行為

將 Azure OpenAI 連線到您的資料之前，我們先觀察基本模型如何在沒有任何基礎資料的情況下回應查詢。

1. 在位於 `https://oai.azure.com` 的 **Azure OpenAI Studio** 中，於 [遊樂場]**** 區段中，選取 [聊天]**** 頁面。 [聊天]**** 遊樂場頁面包含三個主要區段：
    - **設定** - 用來設定模型回應的內容。
    - **聊天工作階段** - 用於提交聊天訊息和檢視回應。
    - **組態** - 用來設定模型部署的設定。
2. 在 [組態]**** 區段中，確定已選取您的模型部署。
3. 在 [設定]** **區域中，選取預設系統訊息範本，設定聊天工作階段的內容。 預設的系統訊息為，*您是可協助人員尋找資訊的 AI 助理*。
4. 在 [聊天工作階段]**** 中，提交下列查詢，然後檢閱回應：

    ```prompt
    I'd like to take a trip to New York. Where should I stay?
    ```

    ```prompt
    What are some facts about New York?
    ```

    嘗試將加入基礎資料，關於旅遊和其他地點住宿場所的類似問題，例如倫敦或舊金山。 您可能會得到關於區域或街區的完整回應，以及關於城市的一些一般事實。

## 在聊天遊樂場連結您的資料

現在，您將為名為 *Margie's Travel* 的虛構旅行社新增一些資料。 然後，您會看到在使用 Margie Travel 的摺頁冊作為基本資料時，Azure OpenAI 模型如何回應。

1. 在新的瀏覽器索引標籤中，從 `https://aka.ms/own-data-brochures` 下載摺頁冊資料的封存。 將摺頁冊解壓縮到您電腦的資料夾。
1. 在 Azure OpenAI Studio 的 [聊天]** **遊樂場，於 [設定]** **區段中，選取 [新增您的資料]****。
1. 選取 [新增資料來源]****，然後選擇 [上傳檔案]****。
1. 您必須建立儲存體帳戶和 Azure AI 搜尋服務資源。 在儲存體資源的下拉式清單中，選取 [建立新的 Azure Blob 儲存體資源]****，然後以下列設定建立儲存體帳戶。 任何未指定的項目都會保留預設值。

    - **訂用帳戶**：您的 Azure 訂用帳戶**
    - **資源群組**：*選取與您 Azure OpenAI 資源相同的資源群組*
    - **儲存體帳戶名稱**：*輸入唯一名稱*
    - **區域**：*選取與您 Azure OpenAI 資源相同的區域*
    - **備援**：[本地備援儲存體 (LRS)]

1. 建立儲存體帳戶資源時，請返回 Azure OpenAI Studio，然後選取使用下列設定 [建立新的 Azure AI 搜尋服務資源]****。 任何未指定的項目都會保留預設值。

    - **訂用帳戶**：您的 Azure 訂用帳戶**
    - **資源群組**：*選取與您 Azure OpenAI 資源相同的資源群組*
    - **服務名稱**：*輸入唯一名稱*
    - **位置**：*選取與您 Azure OpenAI 資源相同的位置*
    - **定價層**：基本

1. 等搜尋資源部署完畢，接著切換回 Azure AI Studio。
1. 在 [新增資料]** **中，為資料來源輸入下列值，然後選取 [下一步]****。

    - **選取資料來源**：上傳檔案
    - **訂用帳戶**：您的 Azure 訂用帳戶
    - **選取 Azure Blob 儲存體資源**：*使用 [重新整理]** **按鈕重新填入清單，然後選擇您建立的儲存體資源*
        - 出現提示時開啟 CORS
    - **選取 Azure AI 搜尋服務資源**：*使用 [重新整理]** **按鈕重新填入清單，然後選擇您建立的搜尋資源*
    - **輸入索引名稱**：`margiestravel`
    - **將向量搜尋新增至此搜尋資源**：未核取
    - **本人確認連線到 Azure AI 搜尋服務帳戶時，會使用我的帳戶**：已核取

1. 在 [上傳檔案]** **頁面，上傳您下載的 PDF，然後選取 [下一步]****。
1. 在 [資料管理]** **頁面上，從下拉式清單中選取 [關鍵字]** **搜尋類型，然後選取 [下一步]****。
1. 在 [檢閱並完成]** **頁面上，選取 [儲存並關閉]****，以新增您的資料。 這可能需要幾分鐘的時間，在此期間，視窗必須保持開啟。 完成後，您會看到 [設定]** ** 一節中指定的資料來源、搜尋資源和索引。

    > **秘訣**：有時候，新搜尋索引與 Azure OpenAI Studio 之間的連線時間太久。 如果您已等候幾分鐘，但仍未連線，請在 Azure 入口網站檢查您的 AI 搜尋資源。 如果您看到已完成的索引，可以在 Azure OpenAI Studio 中斷資料連線，然後指定 Azure AI 搜尋服務資料來源，並選取新的索引，重新新增它。

## 與以您的資料為基礎的模型聊天

既然您已新增資料，請提出與您先前詢問過的相同問題，然後查看回應有何不同。

```prompt
I'd like to take a trip to New York. Where should I stay?
```

```prompt
What are some facts about New York?
```

您這次會看到極為不同的回應，其中包含特定旅館的具體細節和 Margie's Travel 的簡短陳述，以及提供之資訊的來源參考。 如果您開啟回應中所列的 PDF 參考，會看到與模型提供之資料相同的旅館。

請嘗試詢問它關於基礎資料包含之其他城市的問題，亦即杜拜、拉斯維加斯、倫敦和舊金山。

> **注意**：**新增您的資料**仍是預覽版，這項功能未必始終可以符合期望的方式運作，例如為基礎資料未包含的城市提供不正確的參考。

## 將您的應用程式連結到自己的資料

接著，我們來探索如何連結您的應用程式，以使用您自己的資料。

### 準備在 Visual Studio Code 中開發應用程式

現在來探索，在使用 Azure OpenAI 服務 SDK 的應用程式中，使用自己的資料的情況。 您將使用 Visual Studio Code 開發應用程式。 您應用程式的程式碼檔案已在 GitHub 存放庫中提供。

> **秘訣**：如果您已複製 **mslearn-openai** 存放庫，請在 Visual Studio 程式碼中開啟它。 否則，請遵循下列步驟將其複製到您的開發環境。

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
    - 您為模型部署指定的**部署名稱** (可在 Azure OpenAI Studio 的 [部署]**** 頁面中取得)。
    - 搜尋服務的端點 (Azure 入口網站中搜尋資源概觀頁面的 **URL** 值)。
    - 搜尋資源的 **金鑰** (可在 Azure 入口網站中搜尋資源的 [金鑰]** **頁面找到 - 您可以使用其中一個系統管理金鑰)
    - 搜尋索引的名稱 (應該是 `margiestravel`)。
1. 儲存組態檔。

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

完成 Azure OpenAI 資源時，請記得在 `https://portal.azure.com` 刪除 **Azure 入口網站**中的資源。 此外，請務必包含儲存體帳戶和搜尋資源，因為它們可能產生相對龐大的成本。
