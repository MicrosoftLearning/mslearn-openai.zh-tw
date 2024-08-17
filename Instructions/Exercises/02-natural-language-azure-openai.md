---
lab:
  title: 在應用程式使用 Azure OpenAI SDK
---

# 在應用程式使用 Azure OpenAI API

透過 Azure OpenAI 服務，開發人員可以建立以了解自然人類語言見長的聊天機器人、語言模型和其他應用程式。 Azure OpenAI 讓您存取預先訓練的 AI 模型，以及配合應用程式特定需求自訂和微調這些模型的 API 和工具套件。 在本練習中，您將了解如何在 Azure OpenAI 中部署模型，以及如何在自己的應用程式使用它。

在此練習的案例中，您將擔任負責實作應用程式的軟體開發人員角色，該應用程式可使用生成式 AI 協助提供健行建議。 練習中使用的技術，可以應用在任何想使用 Azure OpenAI API 的應用程式。

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

Azure 提供名為 **Azure AI Studio** 的網頁型入口網站，可讓您用來部署、管理及探索模型。 使用 Azure AI Studio 來部署模型，即可開始探索 Azure OpenAI。

> **注意**：使用 Azure AI Studio 時，可能會顯示建議您要執行之工作的訊息方塊。 您可以關閉這些訊息，並依照本練習中的步驟進行。

1. 在 Azure 入口網站 中，於 Azure OpenAI 資源的 [概觀]**** 頁面上，向下捲動至 [開始使用]**** 區段，然後選取按鈕以移至 **AI Studio**。
1. 在 Azure AI Studio 的左側窗格中，選取 [部署]**** 頁面，然後檢視現有的模型部署。 如果您還未擁有，請使用下列設定建立 **gpt-35-turbo-16k** 模型的新部署：
    - **部署名稱**：*您選擇的唯一名稱*
    - **模型**：gpt-35-turbo-16k *(如果無法取得 16k 模型，請選擇 gpt-35-turbo)*
    - **模型版本**：*使用預設版本*
    - **部署類型**：標準
    - **每分鐘權杖速率限制**：5K\*
    - **內容篩選**：預設
    - **啟用動態配額**：已停用

    > \* 每分鐘 5,000 個權杖的速率限制已足以完成此練習，同時還有剩餘容量可讓其他人使用相同的訂用帳戶。

## 準備在 Visual Studio Code 中開發應用程式

您將使用 Visual Studio Code 開發 Azure OpenAI 應用程式。 您應用程式的程式碼檔案已在 GitHub 存放庫中提供。

> **秘訣**：如果您已複製 **mslearn-openai** 存放庫，請在 Visual Studio Code 中開啟它。 否則，請遵循下列步驟將其複製到您的開發環境。

1. 啟動 Visual Studio Code。
2. 開啟選擇區 (SHIFT+CTRL+P) 並執行 **Git：複製 ** 命令，將 `https://github.com/MicrosoftLearning/mslearn-openai` 存放庫複製到本機資料夾 (哪個資料夾無關緊要)。
3. 複製存放庫後，請在 Visual Studio Code 中開啟此資料夾。

    > **注意**：如果 Visual Studio Code 顯示快顯訊息，提示您信任您所開啟的程式碼，請按一下快顯項目中的 [是，我信任作者]**** 選項。

4. 等候其他檔案安裝以支援存放庫中的 C# 程式碼專案。

    > **注意**：如果系統提示您新增必要的資產來組建和偵錯，請選取 [現在不要]****。

## 設定您的應用程式

已提供 C# 和 Python 的應用程式。 這兩個應用程式都有相同的功能。 首先，您將完成應用程式的一些重要部分，以開始使用您的 Azure OpenAI 資源。

1. 在 Visual Studio Code 的 [總管]** **窗格中，瀏覽至 **Labfiles/02-azure-openai-api** 資料夾，然後根據您的使用語言展開 **CSharp** 或 **Python** 資料夾。 每個資料夾都包含應用程式的特定語言檔案，您將在其中整合 Azure OpenAI 功能。
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
5. 儲存組態檔。

## 新增程式碼以使用 Azure OpenAI 服務

現在您已準備好使用 Azure OpenAI SDK 來取用已部署的模型。

1. 在 [總管]** **窗格中，於 **CSharp** 或 **Python** 資料夾中，開啟使用者慣用的介面語言的程式碼檔案，並以程式碼取代註解***新增 Azure OpenAI 套件***，新增 Azure OpenAI SDK 程式庫：

    **C#**：Program.cs

    ```csharp
    // Add Azure OpenAI package
    using Azure.AI.OpenAI;
    ```
    
    **Python**：test-openai-model.py
    
    ```python
    # Add Azure OpenAI package
    from openai import AzureOpenAI
    ```

1. 在您語言的應用程式程式碼中，以下列程式碼取代註解***初始化 Azure OpenAI 用戶端***，將用戶端初始化並定義我們的系統訊息。

    **C#**：Program.cs

    ```csharp
    // Initialize the Azure OpenAI client
    OpenAIClient client = new OpenAIClient(new Uri(oaiEndpoint), new AzureKeyCredential(oaiKey));
    
    // System message to provide context to the model
    string systemMessage = "I am a hiking enthusiast named Forest who helps people discover hikes in their area. If no area is specified, I will default to near Rainier National Park. I will then provide three suggestions for nearby hikes that vary in length. I will also share an interesting fact about the local nature on the hikes when making a recommendation.";
    ```

    **Python**：test-openai-model.py

    ```python
    # Initialize the Azure OpenAI client
    client = AzureOpenAI(
            azure_endpoint = azure_oai_endpoint, 
            api_key=azure_oai_key,  
            api_version="2024-02-15-preview"
            )
    
    # Create a system message
    system_message = """I am a hiking enthusiast named Forest who helps people discover hikes in their area. 
        If no area is specified, I will default to near Rainier National Park. 
        I will then provide three suggestions for nearby hikes that vary in length. 
        I will also share an interesting fact about the local nature on the hikes when making a recommendation.
        """
    ```

1. 以建置要求所需的程式碼取代註解***新增程式碼以傳送要求***；指定模型的各種參數，例如 `messages` 和 `temperature`。

    **C#**：Program.cs

    ```csharp
    // Add code to send request...
    // Build completion options object
    ChatCompletionsOptions chatCompletionsOptions = new ChatCompletionsOptions()
    {
        Messages =
        {
            new ChatRequestSystemMessage(systemMessage),
            new ChatRequestUserMessage(inputText),
        },
        MaxTokens = 400,
        Temperature = 0.7f,
        DeploymentName = oaiDeploymentName
    };

    // Send request to Azure OpenAI model
    ChatCompletions response = client.GetChatCompletions(chatCompletionsOptions);

    // Print the response
    string completion = response.Choices[0].Message.Content;
    Console.WriteLine("Response: " + completion + "\n");
    ```

    **Python**：test-openai-model.py

    ```python
    # Add code to send request...
    # Send request to Azure OpenAI model
    response = client.chat.completions.create(
        model=azure_oai_deployment,
        temperature=0.7,
        max_tokens=400,
        messages=[
            {"role": "system", "content": system_message},
            {"role": "user", "content": input_text}
        ]
    )
    generated_text = response.choices[0].message.content

    # Print the response
    print("Response: " + generated_text + "\n")
    ```

1. 將變更儲存至您的程式碼檔案。

## 測試您的應用程式

現在您的應用程式設定好了，請執行程式，將要求傳送至您的模型，並觀察回應。

1. 在互動式終端窗格中，確定資料夾內容是您慣用語言的資料夾。 然後輸入下列命令來執行應用程式。

    - **C#**：`dotnet run`
    - **Python**：`python test-openai-model.py`

    > **秘訣**：您可以使用終端工具列中的**最大化面板大小** (**^**) 圖示來查看更多的主控台文字。

1. 出現提示時，輸入文字 `What hike should I do near Rainier?`。
1. 請觀察輸出，留意回應會遵循您新增至*訊息*陣列之系統訊息提供的指導方針。
1. 提供提示 `Where should I hike near Boise? I'm looking for something of easy difficulty, between 2 to 3 miles, with moderate elevation gain.` 並觀察輸出。
1. 在使用者慣用的介面語言的程式碼檔案中，將要求中的*溫度*參數值變更為 **1.0**，並儲存檔案。
1. 使用上述提示再次執行應用程式，並觀察輸出。

提高溫度通常會導致回應因隨機性增加而有所不同，即使提供的文字相同也一樣。 您可以執行程式數次，了解輸出可能有何變化。 使用相同的輸入，嘗試針對溫度使用不同的值。

## 維護交談記錄

在大部分的真實世界應用中，參考交談先前部分的能力，可讓您與 AI 代理程式進行更真實的互動。 Azure OpenAI API 的設計為無狀態，但是在提示提供交談記錄，即可讓 AI 模型參考過去的訊息。

1. 再次執行應用程式，並提供提示 `Where is a good hike near Boise?`。
1. 觀察輸出，然後提示 `How difficult is the second hike you suggested?`。
1. 模型的回應可能表示，無法理解您參考的健行。 若要修正此問題，我們可以讓模型有過去的交談訊息可供參考。
1. 在應用程式中，我們必須將先前的提示和回應，新增至我們要傳送的未來提示。 在**系統訊息**定義下方，新增下列程式碼。

    **C#**：Program.cs

    ```csharp
    // Initialize messages list
    var messagesList = new List<ChatRequestMessage>()
    {
        new ChatRequestSystemMessage(systemMessage),
    };
    ```

    **Python**：test-openai-model.py

    ```python
    # Initialize messages array
    messages_array = [{"role": "system", "content": system_message}]
    ```

1. 在註解 [新增程式碼以傳送要求]*** ***下方，使用下列程式碼取代從註解到 **while** 迴圈結尾的所有程式碼，然後儲存檔案。 程式碼多半相同，但現在使用訊息陣列來儲存交談記錄。

    **C#**：Program.cs

    ```csharp
    // Add code to send request...
    // Build completion options object
    messagesList.Add(new ChatRequestUserMessage(inputText));

    ChatCompletionsOptions chatCompletionsOptions = new ChatCompletionsOptions()
    {
        MaxTokens = 1200,
        Temperature = 0.7f,
        DeploymentName = oaiDeploymentName
    };

    // Add messages to the completion options
    foreach (ChatRequestMessage chatMessage in messagesList)
    {
        chatCompletionsOptions.Messages.Add(chatMessage);
    }

    // Send request to Azure OpenAI model
    ChatCompletions response = client.GetChatCompletions(chatCompletionsOptions);

    // Return the response
    string completion = response.Choices[0].Message.Content;

    // Add generated text to messages list
    messagesList.Add(new ChatRequestAssistantMessage(completion));

    Console.WriteLine("Response: " + completion + "\n");
    ```

    **Python**：test-openai-model.py

    ```python
    # Add code to send request...
    # Send request to Azure OpenAI model
    messages_array.append({"role": "user", "content": input_text})

    response = client.chat.completions.create(
        model=azure_oai_deployment,
        temperature=0.7,
        max_tokens=1200,
        messages=messages_array
    )
    generated_text = response.choices[0].message.content
    # Add generated text to messages array
    messages_array.append({"role": "assistant", "content": generated_text})

    # Print generated text
    print("Summary: " + generated_text + "\n")
    ```

1. 儲存檔案。 在您新增的程式碼中，請注意，我們現在會將先前的輸入和回應附加至提示陣列，讓模型能夠理解交談的記錄。
1. 在終端視窗中，輸入下列命令以執行應用程式。

    - **C#**：`dotnet run`
    - **Python**：`python test-openai-model.py`

1. 再次執行應用程式，並提供提示 `Where is a good hike near Boise?`。
1. 觀察輸出，然後提示 `How difficult is the second hike you suggested?`。
1. 您可能會收到關於模型建議之第二次健行的回應，而且提供的對話更為務實。 您可以詢問參考先前答案的額外後續追蹤問題，而且每次記錄都會為模型提供回答問題的上下文。

    > **秘訣**：權杖計數只會設定為 1200，因此如果交談持續太久，應用程式就會耗盡可用的權杖，導致提示不完整。 實際使用時，將記錄的長度限制為最新的輸入和回應，有助於控制所需的權杖數目。

## 清理

當您完成 Azure OpenAI 資源時，請記得刪除 **Azure 入口網站** (位於 `https://portal.azure.com`) 中的部署或整個資源。
