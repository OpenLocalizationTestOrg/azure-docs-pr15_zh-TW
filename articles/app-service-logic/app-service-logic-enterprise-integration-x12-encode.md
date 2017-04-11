<properties 
    pageTitle="瞭解企業整合套件編碼 X12 訊息 Connctor |Microsoft Azure 應用程式服務 |Microsoft Azure" 
    description="瞭解如何使用企業整合套件與邏輯應用程式合作夥伴" 
    services="logic-apps" 
    documentationCenter=".net,nodejs,java"
    authors="padmavc" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="logic-apps" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="padmavc"/>

# <a name="get-started-with-encode-x12-message"></a>快速入門編碼 X12 訊息

驗證 EDI 及合作夥伴的特定屬性，將 XML 編碼的郵件轉換成 EDI 交易集交換中要求技術及/或功能的通知

## <a name="create-the-connection"></a>建立連線

### <a name="prerequisites"></a>必要條件

* Azure 帳戶。您可以建立的[免費的帳戶](https://azure.microsoft.com/free)

* 整合帳戶，才能使用編碼 x12 郵件連接器。 請參閱如何建立[整合帳戶](./app-service-logic-enterprise-integration-create-integration-account.md)，[合作夥伴](./app-service-logic-enterprise-integration-partners.md)的詳細資料和[X12 合約](./app-service-logic-enterprise-integration-x12.md)

### <a name="connect-to-encode-x12-message-using-the-following-steps"></a>連線到編碼 X12 郵件使用下列步驟︰

1. [建立邏輯應用程式](./app-service-logic-create-a-logic-app.md)提供的範例

2. 此連接器沒有任何引動程序。 您可以使用其他引動程序來啟動邏輯應用程式，例如要求觸發程序。  邏輯應用程式設計工具] 中新增觸發程序，並新增巨集指令。  在 [搜尋] 方塊中選取顯示 Microsoft 受管理的 Api 在下拉式清單]，然後輸入 「 x12 」。  選取 [X12-編碼 X12 協議名稱來的郵件或 X12-編碼 X 12 郵件身分識別。  

    ![搜尋 x12](./media/app-service-logic-enterprise-integration-x12connector/x12decodeimage1.png) 

3. 如果您還沒有先前建立的任何整合帳戶的連線，系統會提示您連線詳細資料

    ![整合帳戶的連線](./media/app-service-logic-enterprise-integration-x12connector/x12encodeimage1.png) 


4. 輸入整合帳戶的詳細資訊。  星號屬性所需

  	| 屬性 | 詳細資料 |
  	| -------- | ------- |
  	| 連線名稱 * | 輸入您的連線任何名稱 |
  	| 整合帳戶 * | 輸入整合帳戶名稱。 請確定您的整合帳戶和邏輯應用程式會在相同的 Azure 位置 |

    完成之後，您連線的詳細資料看起來類似下列

    ![建立整合帳戶的連線](./media/app-service-logic-enterprise-integration-x12connector/x12encodeimage2.png) 


5. 選取 [**建立**

6. 請注意，在建立連線。

    ![整合帳戶連線的詳細資料](./media/app-service-logic-enterprise-integration-x12connector/x12encodeimage3.png) 

#### <a name="x12---encode-x12-message-by-agreement-name"></a>X12-編碼 X12 訊息協議名稱

7. 選取 X12 合約，從下拉式清單和 xml 訊息編碼。

    ![提供必要欄位](./media/app-service-logic-enterprise-integration-x12connector/x12encodeimage4.png) 

#### <a name="x12---encode-x12-message-by-identities"></a>X12-編碼 X12 訊息身分識別

7.  提供寄件者識別碼、 寄件者辨識符號、 收件者的識別碼及接收者辨識為 X12 中設定合約。  選取要編碼的 xml 訊息

    ![提供必要欄位](./media/app-service-logic-enterprise-integration-x12connector/x12encodeimage5.png) 

## <a name="x12-encode-does-following"></a>X12 編碼追蹤的用途︰

* 找出相符的寄件者和收件者的內容屬性合約解析度。
* 序列化 EDI 交換，將 XML 編碼的郵件轉換成 EDI 交易集交換中。
* 適用於交易組標題和結尾區段
* 產生交換控制項、 群組控制項數字和交易設定控制項號碼的每個外寄交換
* 在內容資料取代分隔符號
* 驗證 EDI 及合作夥伴的特定屬性
    * 結構描述驗證對訊息結構描述的交易設定資料項目
    * 交易設定資料的項目上執行的 EDI 驗證。
    * 交易設定資料的項目上執行延伸的驗證
* （如果有設定），請要求技術及/或功能的通知。
    * 技術的認可產生頁首驗證。 技術認可報表交換標題和地址接收者的後端處理的狀態
    * 本文驗證，產生應答功能。 應答功能報表每個處理收到的文件時發生的錯誤

## <a name="next-steps"></a>後續步驟

[深入瞭解企業整合套件](./app-service-logic-enterprise-integration-overview.md "瞭解企業整合套件") 

