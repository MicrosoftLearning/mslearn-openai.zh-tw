---
lab:
  title: 使用 Azure OpenAI 服務產生並改善程式碼
  status: stale
---

# 使用 Azure OpenAI 服務產生並改善程式碼

Azure OpenAI 服務模型可以使用自然語言提示為您產生程式碼、修正已完成程式碼中的 Bug，以及提供程式碼註解。 這些模型也可以說明和簡化現有的程式碼，以協助您了解其用途，以及如何加以改善。

在此練習的案例中，您將扮演軟體開發人員的角色，以探索如何使用生成式 AI，讓編碼工作更容易且更有效率。 練習中使用的技術可以套用至其他程式碼檔案、程序設計語言和使用案例。

此練習大約需要 **25** 分鐘。

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

    > \* Azure OpenAI 資源受到區域配額的限制。 列出的區域包括本練習中使用的模型類型的預設配額。 在與其他使用者共用訂用帳戶的情況下，隨機選擇區域可以降低單一區域達到其配額限制的風險。 在練習稍後達到配額限制時，您可能需要在不同的區域中建立另一個資源。需要在不同的區域中建立另一個資源。

3. 等候部署完成。 然後移至 Azure 入口網站中已部署的 Azure OpenAI 資源。

## 部署模型

Azure 提供網頁入口網站，命名為 **Azure AI Foundry入口網站**，可用來部署、管理並探索模型。 使用 Azure AI Foundry 入口網站來部署模型，即可開始探索 Azure OpenAI。

> **備註**：當使用 Azure AI Foundry 入口網站時，可能會顯示建議您要執行工作的訊息方塊。 您可以關閉這些訊息，並依照本練習中的步驟進行。

1. 在 Azure 入口網站，請到 Azure OpenAI 資源的**概觀**頁面，將畫面向下捲動到**入門**區段，然後選取按鈕，即可前往 **AI Foundry 入口網站** (之前稱為 AI Studio)。
1. 在 Azure AI Foundry 入口網站的左側窗格，選取**部署**頁面，檢視現有的模型部署。 如果您還未擁有，請使用下列設定，為**gpt-4o**模型建立的新部署：
    - **部署名稱**：*您選擇的唯一名稱*
    - **模型**：gpt-4o
    - **模型版本**：*使用預設版本*
    - **部署類型**：標準
    - **每分鐘權杖速率限制**：5K\*
    - **內容篩選**：預設
    - **啟用動態配額**：停用

    > \* 每分鐘 5,000 個權杖的速率限制已足以完成此練習，同時還有剩餘容量可讓其他人使用相同的訂用帳戶。

## 在聊天遊樂場中產生程式碼

在應用程式中使用之前，請先觀察 Azure OpenAI 如何在聊天遊樂場中產生和說明程式碼。

1. 在 **[遊樂場]** 區段中，選取 **[聊天]** 頁面。 [聊天]**** 遊樂場頁面包含一列按鈕及兩個主要面板 (可能由右至左水平排列或由上至下垂直排列，依螢幕解析度而定)：
    - ****[設定] - 用來選取您的部署、定義系統訊息，以及設定要與您的部署互動的參數。
    - **聊天工作階段** - 用於提交聊天訊息和檢視回應。
1. 在 [部署]**** 下，確定已選取模型部署。
1. 在 [系統訊息]**** 區域中，將系統訊息設定為：`You are a programming assistant helping write code`，然後套用變更。
1. 在 [聊天工作階段]**** 中，提交下列查詢：

    ```prompt
    Write a function in python that takes a character and a string as input, and returns how many times the character appears in the string
    ```

    模型可能會以函式回應，並說明函式的用途，以及如何呼叫函式。

1. 接下來，傳送提示 `Do the same thing, but this time write it in C#`。

    模型的回應可能與第一次非常類似，但這次是以 C# 編碼。 您可以針對選擇的不同語言再次詢問，或可完成不同工作的函式，例如反轉輸入字串。

1. 接下來，讓我們來探索使用 AI 來了解程式碼。 以使用者訊息的形式提交下列提示。

    ```prompt
    What does the following function do?  
    ---  
    def multiply(a, b):  
        result = 0  
        negative = False  
        if a < 0 and b > 0:  
            a = -a  
            negative = True  
        elif a > 0 and b < 0:  
            b = -b  
            negative = True  
        elif a < 0 and b < 0:  
            a = -a  
            b = -b  
        while b > 0:  
            result += a  
            b -= 1      
        if negative:  
            return -result  
        else:  
            return result  
    ```

    模型應該描述函式的作用，也就是使用迴圈將兩個數字相乘。

1. 提交提示 `Can you simplify the function?`。

    模型應該撰寫函式的更簡單版本。

1. 提交提示：`Add some comments to the function.`

    模型會將註解新增至程式碼。

## 準備在 Visual Studio Code 中開發應用程式

現在讓我們來探索如何建置使用 Azure OpenAI 服務來產生程式碼的自訂應用程式。 您將使用 Visual Studio Code 開發應用程式。 您應用程式的程式碼檔案已在 GitHub 存放庫中提供。

> **秘訣**：如果您已複製 **mslearn-openai** 存放庫，請在 Visual Studio Code 中開啟它。 否則，請遵循下列步驟將其複製到您的開發環境。

1. 啟動 Visual Studio Code。
2. 開啟命令選擇區（SHIFT+CTRL+P 或 **View** > **命令選擇區...**），並執行 **Git: 複製** 命令，將`https://github.com/MicrosoftLearning/mslearn-openai` 存放庫複製到本機資料夾（哪個資料夾都無所謂）。
3. 複製存放庫後，請在 Visual Studio Code 中開啟此資料夾。

    > **注意**：如果 Visual Studio Code 顯示快顯訊息，提示您信任您所開啟的程式碼，請按一下快顯項目中的 [是，我信任作者]**** 選項。

4. 等候其他檔案安裝以支援存放庫中的 C# 程式碼專案。

    > **注意**：如果系統提示您新增必要的資產來組建和偵錯，請選取 [現在不要]****。

## 設定您的應用程式

已同時提供 C# 和 Python 的應用程式，以及您將用於測試摘要的範例文字檔案。 這兩個應用程式都有相同的功能。 首先，您將完成應用程式的一些重要部分，以開始使用您的 Azure OpenAI 資源。

1. 在 Visual Studio Code 的 [總管]**** 窗格中，瀏覽至 [Labfiles/04-code-generation]**** 資料夾，然後根據您的語言偏好設定展開 [CSharp]**** 或 [Python]**** 資料夾。 每個資料夾都包含應用程式的特定語言檔案，您將在其中整合 Azure OpenAI 功能。
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
    - 針對模型部署，您可以指定的**部署名稱** (可在 Azure AI Foundry 入口網站的 **[部署]** 頁面取得)。
5. 儲存組態檔。

## 新增程式碼以使用您的 Azure OpenAI 服務模型

現在您已準備好使用 Azure OpenAI SDK 來取用已部署的模型。

1. 在 [總管]**** 窗格中的 [CSharp]**** 或 [Python]**** 資料夾中，開啟您慣用語言的程式碼檔案。 在呼叫 Azure OpenAI 模型的函式中，於註解***格式化並將要求傳送至模型***底下，新增程式碼以格式化，然後將要求傳送至模型。

    **C#**：Program.cs

    ```csharp
    // Format and send the request to the model
    var chatCompletionsOptions = new ChatCompletionOptions()
    {
        Temperature = 0.7f,
        MaxOutputTokenCount = 800
    };
    
    // Get response from Azure OpenAI
    ChatCompletion response = await chatClient.CompleteChatAsync(
        [
            new SystemChatMessage(systemPrompt),
            new UserChatMessage(userPrompt),
        ],
        chatCompletionsOptions);
    ```

    **Python**：code-generation.py

    ```python
    # Format and send the request to the model
    messages =[
        {"role": "system", "content": system_message},
        {"role": "user", "content": user_message},
    ]
    
    # Call the Azure OpenAI model
    response = client.chat.completions.create(
        model=model,
        messages=messages,
        temperature=0.7,
        max_tokens=1000
    )
    ```

1. 將變更儲存至程式碼檔案。

## 執行應用程式

現在您的應用程式已設定好，請執行它以嘗試為每個使用案例產生程式碼。 使用案例會在應用程式中編號，且可以依任何順序執行。

> **注意**：某些使用者可能會在呼叫模型太頻繁時遇到速率限制。 如果您遇到有關權杖速率限制的錯誤，請等候一分鐘，然後再試一次。

1. 在 [總管]**** 窗格中，展開 [Labfiles/04-code-generation/sample-code]**** 資料夾，然後針對您的語言檢閱函式和 *go-fish* 應用程式。 這些檔案將用於應用程式中的工作。
2. 在互動式終端窗格中，確定資料夾內容是您慣用語言的資料夾。 然後輸入下列命令來執行應用程式。

    - **C#**：`dotnet run`
    - **Python**：`python code-generation.py`

    > **秘訣**：您可以使用終端工具列中的**最大化面板大小** (**^**) 圖示來查看更多的主控台文字。

3. 選擇選項 [1]**** 以將註解新增至您的程式碼，然後輸入下列提示。 請注意，這些工作可能需要幾秒鐘的時間來回應。

    ```prompt
    Add comments to the following function. Return only the commented code.\n---\n
    ```

    結果會放入 **result/app.txt**。 開啟該檔案，並將其與 **sample-code** 中的函式檔案進行比較。

4. 接下來，選擇選項 [2]**** 來撰寫該相同函式的單元測試，並輸入下列提示。

    ```prompt
    Write four unit tests for the following function.\n---\n
    ```

    結果會取代 **result/app.txt** 中的內容，並詳細說明該函式的四個單元測試。

5. 接下來，選擇選項 [3]**** 來修正應用程式中播放 Go Fish 的 Bug。 輸入下列提示。

    ```prompt
    Fix the code below for an app to play Go Fish with the user. Return only the corrected code.\n---\n
    ```

    結果會取代 **result/app.txt** 中的內容，且應該有非常類似的程式碼，並修正一些內容。

    - **C#**：修正會在第 30 行和 59 行進行
    - **Python**：修正會在第 18 行和 31 行進行

    如果您以 Azure OpenAI 中的回應取代包含 Bug 的行，則可以執行 **sample-code** 中的 Go Fish 應用程式。 如果您在沒有修正的情況下執行，應用程式將無法正常運作。

    > **注意**：請務必注意，即使此 Go Fish 應用程式的程式碼已針對某些語法進行修正，但並不是遊戲的嚴格精確表示法。 如果您仔細觀察，在繪製卡片時沒有檢查牌組是否空白，未於玩家得到配對時從玩家手中移除配對，以及一些其他需要了解卡片遊戲才能發現的錯誤。 這是一個很好的範例，說明實用的生成式 AI 模型如何協助產生程式碼，但無法信任為正確且需要由開發人員確認。

    如果您想要查看 Azure OpenAI 的完整回應，則可以將 **printFullResponse** 變數設定為 `True`，然後重新執行應用程式。

## 清理

當您完成 Azure OpenAI 資源時，請記得刪除 **Azure 入口網站** (位於 `https://portal.azure.com`) 中的部署或整個資源。
