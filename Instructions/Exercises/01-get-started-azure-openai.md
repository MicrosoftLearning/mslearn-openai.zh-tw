---
lab:
  title: 開始使用 Azure OpenAI 服務
---

# 開始使用 Azure OpenAI 服務

Azure OpenAI 服務會將這些由 OpenAI 開發的生成式 AI 模型帶入 Azure 平台，讓您開發功能強大的 AI 解決方案，以受益於 Azure 雲端平台所提供服務的安全性、可擴縮性和整合。 在本練習中，您會了解如何透過將服務佈建為 Azure 資源，以及使用 Azure AI Foundry 來部署和探索生成式 AI 模型，以開始使用 Azure OpenAI。

在本練習的案例中，您會執行已負責實作 AI 代理程式的軟體開發人員角色，該代理程式可以使用生成式 AI 來協助行銷組織提升其觸及客戶和廣告新產品的有效性。 練習中所使用的技術可以套用至組織想要使用生成式 AI 模型來協助員工更有效率且更具生產力的任何案例。

本練習大約需要 **30** 分鐘的時間。

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

## 使用 [聊天遊樂場]

由於您已部署模型，即可根據自然語言提示將模型用於產生回應。 Azure AI Foundry 入口網站中的 [聊天]** 遊樂場可提供適用於 GPT 3.5 和更新版模型的聊天機器人介面。

> **注意：***[聊天]* 遊樂場會使用 *ChatCompletions* API，而不是 *[完成]* 遊樂場所使用的舊版 *Completions* API。 提供 [完成] 遊樂場是為了與舊版模型相容。

1. 在 **[遊樂場]** 區段中，選取 **[聊天]** 頁面。 [聊天]**** 遊樂場頁面包含一列按鈕及兩個主要面板 (可能由右至左水平排列或由上至下垂直排列，依螢幕解析度而定)：
    - ****[設定] - 用來選取您的部署、定義系統訊息，以及設定要與您的部署互動的參數。
    - **聊天工作階段** - 用於提交聊天訊息和檢視回應。
1. 在 [部署]** **下，確定已選取 gpt-35-turbo-16k 模型部署。
1. 檢閱預設的**系統訊息**，該訊息應為「您是 AI 助理，可協助人員尋找資訊」**。 系統訊息會包括在提交給模型的提示中，並會提供模型回應的內容；設定關於 AI 代理程式應如何根據模型與使用者互動的預期。
1. 在 **[聊天工作階段]** 面板中，輸入使用者查詢 `How can I use generative AI to help me market a new product?`

    > **注意**：您可能會收到 API 部署尚未就緒的回應。 如果是這樣，請等候幾分鐘後再試一次。

1. 檢閱回應，注意模型已產生與提示的查詢相關並有共識的自然語言答案。
1. 輸入使用者查詢 `What skills do I need if I want to develop a solution to accomplish this?`。
1. 檢閱回應，注意聊天工作階段已保留交談內容 (因此「此」會解譯為行銷的生成式 AI 解決方案)。 此內容化會藉由在每次後續提示提交中包括最近交談記錄來實現，因此針對第二個查詢傳送給模型的提示已包括原始查詢和回應，以及新的使用者輸入。
1. 在 **[聊天工作階段]** 工具列中，請選取 **[清除聊天]**，並確認您想要重新啟動聊天工作階段。
1. 輸入查詢 `Can you help me find resources to learn those skills?` 並檢閱回應，這應該是有效的自然語言答案，但由於先前的聊天記錄已遺失，因此答案可能是關於尋找一般技能資源，而不是與建置生成式 AI 行銷解決方案所需的特定技能。

## 實驗系統訊息、提示和少量範例

到目前為止，您已根據預設系統訊息與模型進行了聊天交談。 您可以自訂系統設定，以更充分掌控模型所產生的回應種類。

1. 在主要工具列中，選取 [提示範例]****，然後使用 [行銷撰寫小幫手]**** 提示範本。
1. 檢閱新的系統訊息，其中會描述 AI 代理程式應該如何使用模型來提供回應。
1. 在 **[聊天工作階段]** 面板中，輸入使用者查詢 `Create an advertisement for a new scrubbing brush`。
1. 檢閱回應，其中應該會包括硬毛刷的廣告文案。 文案內容可能相當廣泛並富有創意。

    在真實案例中，行銷專業人員可能已經知道硬毛刷產品的名稱，以及一些關於應該在廣告中強調的重要功能的想法。 若要從生成式 AI 模型取得最實用的結果，使用者需要設計提示以盡可能包括最多的相關資訊。

1. 輸入提示 `Revise the advertisement for a scrubbing brush named "Scrubadub 2000", which is made of carbon fiber and reduces cleaning times by half compared to ordinary scrubbing brushes`。
1. 檢閱回應，其中應該考慮您已提供的關於硬毛刷產品的其他資訊。

    回應現在應該更實用，但若要更充分掌控模型的輸出，您可以提供一或多個回應應該根據的*少量*範例。

1. 在 [系統訊息]**** 文字方塊中，展開 [新增區段]**** 的下拉式清單，然後選取 [範例]****。 然後在指定的方塊中輸入下列訊息和回應：

    **使用者**：
    
    ```prompt
    Write an advertisement for the lightweight "Ultramop" mop, which uses patented absorbent materials to clean floors.
    ```
    
    **小幫手**：
    
    ```prompt
    Welcome to the future of cleaning!
    
    The Ultramop makes light work of even the dirtiest of floors. Thanks to its patented absorbent materials, it ensures a brilliant shine. Just look at these features:
    - Lightweight construction, making it easy to use.
    - High absorbency, enabling you to apply lots of clean soapy water to the floor.
    - Great low price.
    
    Check out this and other products on our website at www.contoso.com.
    ```

1. 使用 **[套用變更]** 按鈕來儲存範例並啟動新的工作階段。
1. 在 **[聊天工作階段]** 區段中，輸入使用者查詢 `Create an advertisement for the Scrubadub 2000 - a new scrubbing brush made of carbon fiber that reduces cleaning time by half`。
1. 檢閱回應，這應該是“Scrubadub 2000”的新廣告，該廣告以系統設定中提供的“Ultramop”範例為藍本。

## 實驗參數

您已探索系統訊息、範例和提示如何協助精簡模型所傳回的回應。 您也可以使用參數來控制模型行為。

1. 在 **[設定]** 面板中，選取 **[參數]** 索引標籤並設定下列參數值：
    - **最大回應**：1000
    - **溫度**：1

1. 在 **[聊天工作階段]** 區段中，使用 **[清除聊天]** 按鈕來重設聊天工作階段。 然後輸入使用者查詢 `Create an advertisement for a cleaning sponge` 並檢閱回應。 產生的廣告文案應該包括最多 1000 個文字標記，並包括一些創意元素，例如，模型可能已發明海綿的產品名稱，並對其功能提出了一些聲明。
1. 使用 **[清除聊天]** 按鈕以再次重設聊天工作階段，然後重新輸入與之前相同的查詢 (`Create an advertisement for a cleaning sponge`) 並檢閱回應。 回應可能與先前的回應不同。
1. 在 **[設定]** 面板中的 **[參數]** 索引標籤上，將 **[溫度]** 參數值變更為 0。
1. 在 **[聊天工作階段]** 區段中，使用 **[清除聊天]** 按鈕以再次重設聊天工作階段，然後重新輸入與之前相同的查詢 (`Create an advertisement for a cleaning sponge`) 並檢閱回應。 這次，回應可能不太有創意。
1. 使用 **[清除聊天]** 按鈕以再一次重設聊天工作階段，然後重新輸入與之前相同的查詢 (`Create an advertisement for a cleaning sponge`) 並檢閱回應；這應該與先前的回應非常類似 (如果不是完全相同)。

    **[溫度]** 參數可控制模型在產生回應時富有創意的程度。 低值會產生一致的回應，幾乎沒有隨機變化，而高值則鼓勵模型對其輸出新增創意元素；這可能會影響回應的正確性和現實性。

## 將您的模型部署至 Web 應用程式

既然您已探索 Azure AI Foundry 遊樂場中生成式 AI 模型的一些功能，您可以部署 Azure Web 應用程式，以提供基本的 AI 代理程式介面，讓使用者可以透過此介面與模型聊天。

> **注意**：對於某些使用者，由於 Studio 範本中的錯誤 (bug)，所以無法部署至 Web 應用程式。 如果是這種情況，請略過本節。

1. 在 **[聊天]** 遊樂場頁面右上方的 **[部署至]** 功能表中，選取 **[新的 Web 應用程式]**。
1. 在 **[部署至 Web 應用程式]** 對話方塊中，使用下列設定建立新的 Web 應用程式：
    - **名稱**︰*唯一的名稱*
    - **訂用帳戶**：您的 Azure 訂用帳戶**
    - **資源群組**：*您已佈建 Azure OpenAI 資源的資源群組*
    - **位置**：*您已佈建 Azure OpenAI 資源的區域*
    - **定價方案**：免費 (F) - *若此不可用，則選取 [基本 (B1)]*
    - **在 Web 應用程式中啟用聊天記錄**：<u>未</u>選取
    - **我承認 Web 應用程式會使用我的帳戶**：已選取
1. 部署新的 Web 應用程式並等待部署完成 (可能需要 10 分鐘左右的時間)
1. 成功部署 Web 應用程式之後，請使用 **[聊天]** 遊樂場頁面右上方的按鈕來啟動 Web 應用程式。 應用程式可能需要幾分鐘的時間才能啟動。 如果出現提示，請接受權限要求。
1. 在 Web 應用程式中，輸入下列聊天訊息：

    ```prompt
    Write an advertisement for the new "WonderWipe" cloth that attracts dust particulates and can be used to clean any household surface.
    ```

1. 檢閱回應。

    > **注意**：您已將*模型*部署至 Web 應用程式，但此部署不包括您在遊樂場中設定的系統設定和參數；因此回應可能不會反映您在遊樂場中指定的範例。 在實際案例中，您可能會將邏輯新增至應用程式以修改提示，使其包括您想要產生之回應種類的適當內容資料。 此自訂種類已超出此入門層級練習的範圍，但您可以在其他練習和產品文件中了解提示工程技術和 Azure OpenAI API。

1. 當您在 Web 應用程式中完成模型實驗後，請關閉瀏覽器中的 Web 應用程式索引標籤，以返回 Azure AI Foundry 入口網站。

## 清理

當您完成 Azure OpenAI 資源時，請記得刪除 **Azure 入口網站** (位於 `https://portal.azure.com`) 中的部署或整個資源。
