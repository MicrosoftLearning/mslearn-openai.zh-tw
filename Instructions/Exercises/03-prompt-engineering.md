---
lab:
  title: 在您的應用程式中利用提示工程
---

# 在您的應用程式中利用提示工程

使用 Azure OpenAI 服務時，開發人員如何塑造提示，會大幅影響生成式 AI 模型的回應方式。 Azure OpenAI 模型能夠在要求清晰簡潔的情況下，量身打造並格式化內容。 在此練習中，您將了解類似內容的不同提示，如何協助塑造 AI 模型的回應，以更符合您的需求。

在此練習的案例中，您將擔任負責野生生物行銷活動軟體開發人員的角色。 您正在探索如何使用生成式 AI 改善廣告電子郵件，並且將可能適用於您的小組的文章分類。 練習中使用的提示工程技術，同樣適用於各種使用案例。

此練習大約需要 **30** 分鐘。

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

Azure 提供網頁入口網站，命名為 **Azure AI Foundry入口網站**，可用來部署、管理並探索模型。 使用 Azure AI Foundry 入口網站來部署模型，即可開始探索 Azure OpenAI。

> **備註**：當使用 Azure AI Foundry 入口網站時，可能會顯示建議您要執行工作的訊息方塊。 您可以關閉這些訊息，並依照本練習中的步驟進行。

1. 在 Azure 入口網站，請到 Azure OpenAI 資源的**概觀**頁面，將畫面向下捲動到**入門**區段，然後選取按鈕，即可前往 **AI Foundry 入口網站** (之前稱為 AI Studio)。
1. 在 Azure AI Foundry 入口網站的左側窗格，選取**部署**頁面，檢視現有的模型部署。 如果您還未擁有，請使用下列設定建立 **gpt-35-turbo-16k** 模型的新部署：
    - **部署名稱**：*您選擇的唯一名稱*
    - **模型**：gpt-35-turbo-16k *(如果無法取得 16k 模型，請選擇 gpt-35-turbo)*
    - **模型版本**：*使用預設版本*
    - **部署類型**：標準
    - **每分鐘權杖速率限制**：5K\*
    - **內容篩選**：預設
    - **啟用動態配額**：停用

    > \* 每分鐘 5,000 個權杖的速率限制已足以完成此練習，同時還有剩餘容量可讓其他人使用相同的訂用帳戶。

## 探索提示工程技術

一開始，我們先在聊天遊樂場，探索一些提示工程技術。

1. 在 **[遊樂場]** 區段中，選取 **[聊天]** 頁面。 [聊天]**** 遊樂場頁面包含一列按鈕及兩個主要面板 (可能由右至左水平排列或由上至下垂直排列，依螢幕解析度而定)：
    - ****[設定] - 用來選取您的部署、定義系統訊息，以及設定要與您的部署互動的參數。
    - **聊天工作階段** - 用於提交聊天訊息和檢視回應。
2. 在 [部署]** **下，確定已選取 gpt-35-turbo-16k 模型部署。
1. 檢閱預設的**系統訊息**，內容應該是 [您是可協助人員尋找資訊的 AI 助理]**。
4. 在 [聊天工作階段]**** 中，提交下列查詢：

    ```prompt
    What kind of article is this?
    ---
    Severe drought likely in California
    
    Millions of California residents are bracing for less water and dry lawns as drought threatens to leave a large swath of the region with a growing water shortage.
    
    In a remarkable indication of drought severity, officials in Southern California have declared a first-of-its-kind action limiting outdoor water use to one day a week for nearly 8 million residents.
    
    Much remains to be determined about how daily life will change as people adjust to a drier normal. But officials are warning the situation is dire and could lead to even more severe limits later in the year.
    ```

    回應會提供文章的說明。 不過，假設您想要更具體的文章分類格式。

5. 在 [設定]** **區段中，將系統訊息變更為 `You are a news aggregator that categorizes news articles.`

6. 在新的系統訊息下方，選取 [新增區段]**** 按鈕，然後選擇 [範例]****。 然後，新增下列範例。

    **使用者**：
    
    ```prompt
    What kind of article is this?
    ---
    New York Baseballers Wins Big Against Chicago
    
    New York Baseballers mounted a big 5-0 shutout against the Chicago Cyclones last night, solidifying their win with a 3 run homerun late in the bottom of the 7th inning.
    
    Pitcher Mario Rogers threw 96 pitches with only two hits for New York, marking his best performance this year.
    
    The Chicago Cyclones' two hits came in the 2nd and the 5th innings but were unable to get the runner home to score.
    ```
    
    **助理：**
    
    ```prompt
    Sports
      ```

7. 使用下列文字新增另一個範例。

    **使用者**：
    
    ```prompt
    Categorize this article:
    ---
    Joyous moments at the Oscars
    
    The Oscars this past week where quite something!
    
    Though a certain scandal might have stolen the show, this year's Academy Awards were full of moments that filled us with joy and even moved us to tears.
    These actors and actresses delivered some truly emotional performances, along with some great laughs, to get us through the winter.
    
    From Robin Kline's history-making win to a full performance by none other than Casey Jensen herself, don't miss tomorrows rerun of all the festivities.
    ```
    
    **小幫手**：
    
    ```prompt
    Entertainment
    ```

8. 使用 [設定] 區段頂端的 **** [套用變更****] 按鈕來儲存變更。

9. 在 [聊天工作階段]**** 區段中，重新提交下列提示：

    ```prompt
    What kind of article is this?
    ---
    Severe drought likely in California
    
    Millions of California residents are bracing for less water and dry lawns as drought threatens to leave a large swath of the region with a growing water shortage.
    
    In a remarkable indication of drought severity, officials in Southern California have declared a first-of-its-kind action limiting outdoor water use to one day a week for nearly 8 million residents.
    
    Much remains to be determined about how daily life will change as people adjust to a drier normal. But officials are warning the situation is dire and could lead to even more severe limits later in the year.
    ```

    較具體的系統訊息結合一些預期查詢和回應的範例，會為結果產生一致的格式。

10. 將系統訊息變更回預設範本，內容應該是 `You are an AI assistant that helps people find information.` 而且沒有範例。 接著套用變更。

11. 在 [聊天工作階段]**** 區段中，提交下列提示：

    ```prompt
    # 1. Create a list of animals
    # 2. Create a list of whimsical names for those animals
    # 3. Combine them randomly into a list of 25 animal and name pairs
    ```

    模型可能會回應符合提示的答案，並分割為編號清單。 這是適當的回應，但假設您其實是想讓模型撰寫 Python 程式，執行您所述的工作呢？

12. 將系統訊息變更為 `You are a coding assistant helping write python code.` 並套用變更。
13. 將下列提示重新提交至模型：

    ```
    # 1. Create a list of animals
    # 2. Create a list of whimsical names for those animals
    # 3. Combine them randomly into a list of 25 animal and name pairs
    ```

    模型應該會正確回應，提供執行註解要求之動作的 Python 程式碼。

## 準備在 Visual Studio Code 中開發應用程式

現在來探索，在使用 Azure OpenAI 服務 SDK 的應用程式中，使用提示工程的案例。 您將使用 Visual Studio Code 開發應用程式。 您應用程式的程式碼檔案已在 GitHub 存放庫中提供。

> **秘訣**：如果您已複製 **mslearn-openai** 存放庫，請在 Visual Studio Code 中開啟它。 否則，請遵循下列步驟將其複製到您的開發環境。

1. 啟動 Visual Studio Code。
2. 開啟選擇區 (SHIFT+CTRL+P) 並執行 **Git：複製 ** 命令，將 `https://github.com/MicrosoftLearning/mslearn-openai` 存放庫複製到本機資料夾 (哪個資料夾無關緊要)。
3. 複製存放庫後，請在 Visual Studio Code 中開啟此資料夾。

    > **注意**：如果 Visual Studio Code 顯示快顯訊息，提示您信任您所開啟的程式碼，請按一下快顯項目中的 [是，我信任作者]**** 選項。

4. 等候其他檔案安裝以支援存放庫中的 C# 程式碼專案。

    > **注意**：如果系統提示您新增必要的資產來組建和偵錯，請選取 [現在不要]****。

## 設定您的應用程式

已提供 C# 和 Python 的應用程式，而且兩個應用程式都具有相同的功能。 首先，您將完成應用程式的一些重要部分，才能以非同步 API 呼叫使用 Azure OpenAI 資源。

1. 在 Visual Studio Code 的 [總管]**** 窗格中，瀏覽至 **Labfiles/03-prompt-engineering**資料夾，然後根據您的使用語言展開 **CSharp** 或 **Python** 資料夾。 每個資料夾都包含應用程式的特定語言檔案，您將在其中整合 Azure OpenAI 功能。
2. 以滑鼠右鍵按一下包含程式碼檔案的 [CSharp]**** 或 [Python]**** 資料夾，然後開啟整合式終端。 然後，針對您的語言喜好設定執行適當的命令，以安裝 Azure OpenAI SDK 套件：

    **C#：**

    ```
    dotnet add package Azure.AI.OpenAI --version 1.0.0-beta.14
    ```

    **Python**：

    ```
    pip install openai==1.55.3
    ```

3. 在 [總管]**** 窗格的 **CSharp** 或 **Python** 資料夾中，開啟使用者慣用的介面語言的設定檔

    - **C#**：appsettings.json
    - **Python**：.env
    
4. 更新設定值以包含：
    - 您所建立 Azure OpenAI 資源的**端點**和**金鑰** (可在 Azure 入口網站中 Azure OpenAI 資源的 [金鑰和端點]**** 頁面上取得)
    - 針對模型部署，您可以指定的**部署名稱** (可在 Azure AI Foundry 入口網站的 **[部署]** 頁面取得)。
5. 儲存組態檔。

## 新增程式碼以使用 Azure OpenAI 服務

現在您已準備好使用 Azure OpenAI SDK 來取用已部署的模型。

1. 在 [總管]** **窗格中，於 **CSharp** 或 **Python** 資料夾中，開啟使用者慣用的介面語言的程式碼檔案，並以程式碼取代註解***新增 Azure OpenAI 套件***，新增 Azure OpenAI SDK 程式庫：

    **C#**：Program.cs

    ```csharp
    // Add Azure OpenAI package
    using Azure.AI.OpenAI;
    ```

    **Python**：prompt-engineering.py

    ```python
    # Add Azure OpenAI package
    from openai import AsyncAzureOpenAI
    ```

2. 在程式碼檔案中，尋找註解***設定 Azure OpenAI 用戶端***，並新增程式碼，以設定 Azure OpenAI 用戶端：

    **C#**：Program.cs

    ```csharp
    // Configure the Azure OpenAI client
    OpenAIClient client = new OpenAIClient(new Uri(oaiEndpoint), new AzureKeyCredential(oaiKey));
    ```

    **Python**：prompt-engineering.py

    ```python
    # Configure the Azure OpenAI client
    client = AsyncAzureOpenAI(
        azure_endpoint = azure_oai_endpoint, 
        api_key=azure_oai_key,  
        api_version="2024-02-15-preview"
        )
    ```

3. 在呼叫 Azure OpenAI 模型的函式中，於註解***格式化並將要求傳送至模型***底下，新增程式碼以格式化，然後將要求傳送至模型。

    **C#**：Program.cs

    ```csharp
    // Format and send the request to the model
    var chatCompletionsOptions = new ChatCompletionsOptions()
    {
        Messages =
        {
            new ChatRequestSystemMessage(systemMessage),
            new ChatRequestUserMessage(userMessage)
        },
        Temperature = 0.7f,
        MaxTokens = 800,
        DeploymentName = oaiDeploymentName
    };
    
    // Get response from Azure OpenAI
    Response<ChatCompletions> response = await client.GetChatCompletionsAsync(chatCompletionsOptions);
    ```

    **Python**：prompt-engineering.py

    ```python
    # Format and send the request to the model
    messages =[
        {"role": "system", "content": system_message},
        {"role": "user", "content": user_message},
    ]
    
    print("\nSending request to Azure OpenAI model...\n")

    # Call the Azure OpenAI model
    response = await client.chat.completions.create(
        model=model,
        messages=messages,
        temperature=0.7,
        max_tokens=800
    )
    ```

4. 將變更儲存至程式碼檔案。

## 執行您的應用程式

現在您的應用程式設定好了，請執行程式，將要求傳送至您的模型，並觀察回應。 您會發現，提示內容是不同選項之間的唯一差異，每項要求的所有其他參數 (例如權杖計數和溫度) 都保持不變。

1. 在使用者慣用的介面語言的資料夾中，在 Visual Studio Code 開啟 `system.txt`。 針對每個反覆項目，您將在此檔案輸入**系統訊息**，並且儲存檔案。 每個反覆項目都會先暫停，讓您變更系統訊息。
1. 在互動式終端窗格中，確定資料夾內容是您慣用語言的資料夾。 然後輸入下列命令來執行應用程式。

    - **C#**：`dotnet run`
    - **Python**：`python prompt-engineering.py`

    > **秘訣**：您可以使用終端工具列中的**最大化面板大小** (**^**) 圖示來查看更多的主控台文字。

1. 針對第一個反覆項目，輸入下列提示：

    **系統訊息**

    ```prompt
    You are an AI assistant
    ```

    **使用者訊息：**

    ```prompt
    Write an intro for a new wildlife Rescue
    ```

1. 觀察輸出。 AI 模型可能產生不錯的野生生物救援一般簡介。
1. 接下來，輸入下列提示，以指定回應的格式：

    **系統訊息**

    ```prompt
    You are an AI assistant helping to write emails
    ```

    **使用者訊息：**

    ```prompt
    Write a promotional email for a new wildlife rescue, including the following: 
    - Rescue name is Contoso 
    - It specializes in elephants 
    - Call for donations to be given at our website
    ```

    > **提示**：您可能會在 VM 中發現自動輸入不適用於多行提示。 如果這是您的問題，請複製整個提示，然後將它貼到 Visual Studio Code。

1. 觀察輸出。 此時，您可能會看到包含特定動物的電子郵件格式，以及捐款呼籲。
1. 接下來，輸入下列提示，額外指定內容：

    **系統訊息**

    ```prompt
    You are an AI assistant helping to write emails
    ```

    **使用者訊息：**

    ```prompt
    Write a promotional email for a new wildlife rescue, including the following: 
    - Rescue name is Contoso 
    - It specializes in elephants, as well as zebras and giraffes 
    - Call for donations to be given at our website 
    \n Include a list of the current animals we have at our rescue after the signature, in the form of a table. These animals include elephants, zebras, gorillas, lizards, and jackrabbits.
    ```

1. 觀察輸出，查看電子郵件如何根據您的清晰指示變更。
1. 接下來，輸入下列提示，我們將在其中新增有關語調的詳細資料至系統訊息：

    **系統訊息**

    ```prompt
    You are an AI assistant that helps write promotional emails to generate interest in a new business. Your tone is light, chit-chat oriented and you always include at least two jokes.
    ```

    **使用者訊息：**

    ```prompt
    Write a promotional email for a new wildlife rescue, including the following: 
    - Rescue name is Contoso 
    - It specializes in elephants, as well as zebras and giraffes 
    - Call for donations to be given at our website 
    \n Include a list of the current animals we have at our rescue after the signature, in the form of a table. These animals include elephants, zebras, gorillas, lizards, and jackrabbits.
    ```

1. 觀察輸出。 此時您可能會看到類似格式的電子郵件，但語調比較輕鬆隨性。 您甚至可能會看到內容包含笑話！
1. 針對最後一個反覆項目，我們會偏離產生電子郵件的工作，探索*基礎內容*。 在這裡，您會提供簡單的系統訊息，並變更應用程式，在使用者提示開頭提供基礎內容。 然後，應用程式會附加使用者輸入，並從基礎內容擷取資訊，以回應使用者提示。
1. 開啟檔案 `grounding.txt`，並簡短閱讀您要插入的基礎內容。
1. 在註解 ***格式之後的應用程式中，將要求傳送至模型***，並在任何現有的程式碼之前，新增下列程式碼片段，從 `grounding.txt` 讀取文字，以基礎內容增強使用者提示。

    **C#**：Program.cs

    ```csharp
    // Format and send the request to the model
    Console.WriteLine("\nAdding grounding context from grounding.txt");
    string groundingText = System.IO.File.ReadAllText("grounding.txt");
    userMessage = groundingText + userMessage;
    ```

    **Python**：prompt-engineering.py

    ```python
    # Format and send the request to the model
    print("\nAdding grounding context from grounding.txt")
    grounding_text = open(file="grounding.txt", encoding="utf8").read().strip()
    user_message = grounding_text + user_message
    ```

1. 儲存檔案並重新執行應用程式。
1. 輸入下列提示 (**系統訊息**仍在輸入並儲存於 `system.txt` 中)。

    **系統訊息**

    ```prompt
    You're an AI assistant who helps people find information. You'll provide answers from the text provided in the prompt, and respond concisely.
    ```

    **使用者訊息：**

    ```prompt
    What animal is the favorite of children at Contoso?
    ```

> **秘訣**：如果您想要查看 Azure OpenAI 的完整回應，則可以將 **printFullResponse** 變數設定為 `True`，然後重新執行應用程式。

## 清理

當您完成 Azure OpenAI 資源時，請記得刪除 **Azure 入口網站** (位於 `https://portal.azure.com`) 中的部署或整個資源。
