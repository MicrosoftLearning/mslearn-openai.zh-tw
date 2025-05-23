---
lab:
  title: 使用 Azure OpenAI 服務，開發應用程式
  status: new
---

# 使用 Azure OpenAI 服務，開發應用程式

選用 Azure OpenAI 服務，透過使用 REST API 或語言特定 SDK，開發人員就可以建立專攻能理解自然人類語言的聊天機器人、語言模型和其他應用程式。 使用語言模型運作時，開發人員如何塑造提示，會大幅影響到生成式 AI 模型的回應方式。 Azure OpenAI 模型能夠在要求清晰簡潔的情況下，量身打造並格式化內容。 在本練習中，您將能了解類似內容的不同提示，都是如何協助塑造 AI 模型回應，更能符合您的需求。

您將在本練習案例中，擔任負責野生生物行銷廣告活動軟體開發人員的角色。 您正在探索如何使用生成式 AI 改善廣告電子郵件，並且將可能適用於您的小組的文章分類。 練習中使用的提示工程技術，同樣適用於各種使用案例。

此練習大約需要 **30** 分鐘。

## 複製本課程的存放庫

如果您尚未完成此步驟，您必須複製本課程的程式碼存放庫：

1. 啟動 Visual Studio Code。
2. 開啟命令選擇區（SHIFT+CTRL+P 或 **View** > **命令選擇區...**），並執行 **Git: 複製** 命令，將`https://github.com/MicrosoftLearning/mslearn-openai` 存放庫複製到本機資料夾（哪個資料夾都無所謂）。
3. 複製存放庫後，請在 Visual Studio Code 中開啟此資料夾。
4. 等候其他檔案安裝以支援存放庫中的 C# 程式碼專案。

    > **注意**：如果系統提示您新增必要的資產來組建和偵錯，請選取 [現在不要]****。

## 佈建 Azure OpenAI 資源

如果您還未擁有，請在 Azure 訂用帳戶中佈建 Azure OpenAI 資源。

1. 登入 **Azure 入口網站**，網址為：`https://portal.azure.com`。

1. 使用下列設定建立 **Azure OpenAI** 資源：
    - **訂用帳戶**：*選取已核准存取 Azure OpenAI 服務的 Azure 訂用帳戶*
    - **資源群組**：*選擇或建立資源群組*
    - **區域**：*從以下任一區域中**隨機**選擇*\*
        - 美國東部
        - 美國東部 2
        - 美國中北部
        - 美國中南部
        - 瑞典中部
        - 美國西部
        - 美國西部 3
    - **名稱**：*您選擇的唯一名稱*
    - **定價層**:標準 S0

    > \* Azure OpenAI 資源受到區域配額的限制。 列出的區域包括本練習中使用的模型類型的預設配額。 在與其他使用者共用訂用帳戶的情況下，隨機選擇區域可以降低單一區域達到其配額限制的風險。 如果練習稍後達到配額限制，您可能需要在不同的區域中建立另一個資源。

1. 等候部署完成。 然後移至 Azure 入口網站中已部署的 Azure OpenAI 資源。

## 部署模型

下一頁，您將可從 Cloud Shell 部署 Azure OpenAI 模型資源。

1. 使用頁面上方搜尋欄右側的 [\>_]**** 按鈕，就能從 Azure 入口網站哪邊，建立新的 Cloud Shell，選擇 ***Bash*** 環境。 Cloud Shell 會在 Azure 入口網站底部的窗格顯示命令列介面。

    > **備註**：如果您之前就已建立使用 *Bash* 環境的 Cloud Shell，請將原先的設定切換成 ***PowerShell***。

1. 只要參考本範例，就能用自己的資源值，取代下列變數：

    ```dotnetcli
    az cognitiveservices account deployment create \
       -g <your_resource_group> \
       -n <your_OpenAI_service> \
       --deployment-name gpt-4o \
       --model-name gpt-4o \
       --model-version 2024-05-13 \
       --model-format OpenAI \
       --sku-name "Standard" \
       --sku-capacity 5
    ```

> **備註**：SKU 容量是以每分鐘數千個權杖來衡量標準。 每分鐘 5,000 個權杖的速率限制，就可以完成本練習，同時為其他使用相同訂閱的人，留下一點容量。

## 設定您的應用程式

已提供 C# 和 Python 的應用程式，而且兩個應用程式都具有相同的功能。 首先，您將完成應用程式的一些重要部分，才能以非同步 API 呼叫使用 Azure OpenAI 資源。

1. 在 Visual Studio Code 的 **[總管]** 窗格中，瀏覽至 **[Labfiles/04-code-generation]** 資料夾，然後根據您的語言偏好，展開 **[CSharp]** 或 **[Python]** 資料夾。 每個資料夾都包含應用程式的特定語言檔案，您將在其中整合 Azure OpenAI 功能。
2. 以滑鼠右鍵按一下包含程式碼檔案的 [CSharp]**** 或 [Python]**** 資料夾，然後開啟整合式終端。 然後，針對您的語言喜好設定執行適當的命令，以安裝 Azure OpenAI SDK 套件：

    **C#：**

    ```powershell
    dotnet add package Azure.AI.OpenAI --version 2.1.0
    ```

    **Python**：

    ```powershell
    pip install openai==1.65.2
    ```

3. 在 [總管]**** 窗格的 **CSharp** 或 **Python** 資料夾中，開啟使用者慣用的介面語言的設定檔

    - **C#**：appsettings.json
    - **Python**：.env

4. 更新設定值以包含：
    - 您所建立 Azure OpenAI 資源的**端點**和**金鑰** (可在 Azure 入口網站中 Azure OpenAI 資源的 [金鑰和端點]**** 頁面上取得)
    - 您為模型部署指定的**部署名稱**。
5. 儲存組態檔。

## 新增程式碼以使用 Azure OpenAI 服務

現在您已準備好使用 Azure OpenAI SDK 來取用已部署的模型。

1. 在 [總管]** **窗格中，於 **CSharp** 或 **Python** 資料夾中，開啟使用者慣用的介面語言的程式碼檔案，並以程式碼取代註解***新增 Azure OpenAI 套件***，新增 Azure OpenAI SDK 程式庫：

    **C#**：Program.cs

    ```csharp
    // Add Azure OpenAI packages
    using Azure.AI.OpenAI;
    using OpenAI.Chat;
    ```

    **Python**：application.py

    ```python
    # Add Azure OpenAI package
    from openai import AsyncAzureOpenAI
    ```

2. 在程式碼檔案中，尋找註解***設定 Azure OpenAI 用戶端***，並新增程式碼，以設定 Azure OpenAI 用戶端：

    **C#**：Program.cs

    ```csharp
    // Configure the Azure OpenAI client
    AzureOpenAIClient azureClient = new (new Uri(oaiEndpoint), new ApiKeyCredential(oaiKey));
    ChatClient chatClient = azureClient.GetChatClient(oaiDeploymentName);
    ```

    **Python**：application.py

    ```python
    # Configure the Azure OpenAI client
    client = AsyncAzureOpenAI(
        azure_endpoint = azure_oai_endpoint, 
        api_key=azure_oai_key,  
        api_version="2024-02-15-preview"
    )
    ```

3. 在呼叫 Azure OpenAI 模型的函式中，到註解下方***取得來自 Azure OpenAI 的回應***，新增程式碼，以便進行格式化，然後將要求傳送到模型。

    **C#**：Program.cs

    ```csharp
    // Get response from Azure OpenAI
    ChatCompletionOptions chatCompletionOptions = new ChatCompletionOptions()
    {
        Temperature = 0.7f,
        MaxOutputTokenCount = 800
    };

    ChatCompletion completion = chatClient.CompleteChat(
        [
            new SystemChatMessage(systemMessage),
            new UserChatMessage(userMessage)
        ],
        chatCompletionOptions
    );

    Console.WriteLine($"{completion.Role}: {completion.Content[0].Text}");
    ```

    **Python**：application.py

    ```python
    # Get response from Azure OpenAI
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
    - **Python**：`python application.py`

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
    Include a list of the current animals we have at our rescue after the signature, in the form of a table. These animals include elephants, zebras, gorillas, lizards, and jackrabbits.
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
    Include a list of the current animals we have at our rescue after the signature, in the form of a table. These animals include elephants, zebras, gorillas, lizards, and jackrabbits.
    ```

1. 觀察輸出。 此時您可能會看到類似格式的電子郵件，但語調比較輕鬆隨性。 您甚至可能會看到內容包含笑話！

## 使用基礎設置內容，維護聊天記錄

1. 針對最後一個反覆運算，我們會偏離產生電子郵件的工作，探索*基礎設置內容*，同時維護聊天記錄。 這裡會提供一些簡單的系統訊息，同時變更應用程式，以便提供基礎設置內容，作為聊天記錄的開始。 然後，應用程式會附加使用者輸入，並從基礎內容擷取資訊，以回應使用者提示。
1. 開啟檔案 `grounding.txt`，並簡短閱讀您要插入的基礎內容。
1. 透過應用程式，緊接著留言 ***[將訊息清單初始化]*** 之後，還有在任何現有程式碼之前，新增以下程式碼片段，以便從`grounding.txt`讀取文字，並使用基礎設置內容，將聊天記錄初始化。

    **C#**：Program.cs

    ```csharp
    // Initialize messages list
    Console.WriteLine("\nAdding grounding context from grounding.txt");
    string groundingText = System.IO.File.ReadAllText("grounding.txt");
    var messagesList = new List<ChatMessage>()
    {
        new UserChatMessage(groundingText),
    };
    ```

    **Python**：application.py

    ```python
    # Initialize messages array
    print("\nAdding grounding context from grounding.txt")
    grounding_text = open(file="grounding.txt", encoding="utf8").read().strip()
    messages_array = [{"role": "user", "content": grounding_text}]
    ```

1. 請在留言 ***[格式化並將請求傳送到模型]*** 下方，把從留言到 **[while]** 迴圈末尾的程式碼用以下程式碼取代。 程式碼多半相同，但現在使用訊息陣列，方便將請求傳送至模型。

    **C#**：Program.cs
   
    ```csharp
    // Format and send the request to the model
    messagesList.Add(new SystemChatMessage(systemMessage));
    messagesList.Add(new UserChatMessage(userMessage));
    GetResponseFromOpenAI(messagesList);
    ```

    **Python**：application.py

    ```python
    # Format and send the request to the model
    messages_array.append({"role": "system", "content": system_text})
    messages_array.append({"role": "user", "content": user_text})
    await call_openai_model(messages=messages_array, 
        model=azure_oai_deployment, 
        client=client
    )
    ```

1. 在留言下方 ***定義從 Azure OpenAI 端點取得回應的函式***，會下列程式代碼取代函式宣告，以便在`GetResponseFromOpenAI` C# 或`call_openai_model` Python 的通話函式時，使用聊天記錄清單。

    **C#**：Program.cs
   
    ```csharp
    // Define the function that gets the response from Azure OpenAI endpoint
    private static void GetResponseFromOpenAI(List<ChatMessage> messagesList)
    ```

    **Python**：application.py

    ```python
    # Define the function that will get the response from Azure OpenAI endpoint
    async def call_openai_model(messages, model, client):
    ```
    
1. 最後，請在 ***[從 Azure OpenAI 取得回應]*** 下方取代所有程式碼。 程式碼多半相同，但現在使用訊息陣列來儲存交談記錄。

    **C#**：Program.cs
   
    ```csharp
    // Get response from Azure OpenAI
    ChatCompletionOptions chatCompletionOptions = new ChatCompletionOptions()
    {
        Temperature = 0.7f,
        MaxOutputTokenCount = 800
    };

    ChatCompletion completion = chatClient.CompleteChat(
        messagesList,
        chatCompletionOptions
    );

    Console.WriteLine($"{completion.Role}: {completion.Content[0].Text}");
    messagesList.Add(new AssistantChatMessage(completion.Content[0].Text));
    ```

    **Python**：application.py

    ```python
    # Get response from Azure OpenAI
    print("\nSending request to Azure OpenAI model...\n")

    # Call the Azure OpenAI model
    response = await client.chat.completions.create(
        model=model,
        messages=messages,
        temperature=0.7,
        max_tokens=800
    )   

    print("Response:\n" + response.choices[0].message.content + "\n")
    messages.append({"role": "assistant", "content": response.choices[0].message.content})
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

   請注意，模型使用基礎設置文字資訊，回答您的問題。

1. 在不更改系統訊息的情況下，輸入以下使用者訊息提示：

    **使用者訊息：**

    ```prompt
    How can they interact with it at Contoso?
    ```

    請注意，此模型會將「他們」識別為孩子，將「它」識別為孩子最喜歡的動物，原因是現在它可以存取您在聊天記錄中的上一題問題。
   
## 清理

當您完成 Azure OpenAI 資源時，請記得刪除 **Azure 入口網站** (位於 `https://portal.azure.com`) 中的部署或整個資源。
