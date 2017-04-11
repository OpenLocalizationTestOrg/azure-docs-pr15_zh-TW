<properties 
   pageTitle="Azure 應用程式服務中建立交易的合作夥伴合約 |Microsoft Azure" 
   description="建立交易合作夥伴合約" 
   services="logic-apps" 
   documentationCenter=".net,nodejs,java" 
   authors="rajram" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
    ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="08/23/2016"
   ms.author="rajram"/>

# <a name="creating-a-trading-partner-agreement"></a>建立交易的合作夥伴合約   

[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]

交易夥伴就是參與 B2B （企業對企業） 通訊。 當兩個夥伴建立關聯時，這被指*合約*。 定義為基礎進行通訊兩個合作夥伴想及通訊協定或傳輸特定。 各種 B2B 通訊協定和 Azure 應用程式服務支援傳輸包括︰

- AS2 （是否適陳述式 2）
- EDIFACT （美國國家/電子資料交換管理、 商務與傳輸 (取消/EDIFACT)）
- X12 (ASC X12)

### <a name="biztalk-api-apps-that-support-b2b-scenarios"></a>支援 B2B 案例 BizTalk API 應用程式
下列 API 應用程式啟用 Azure 入口網站中使用 rtf 化和直覺化的體驗這些功能︰


## <a name="biztalk-trading-partner-management-tpm"></a>BizTalk 交易夥伴管理 (TPM)
- 建立和管理的合作夥伴、 設定檔與身分識別
- 儲存和管理的 EDI 結構描述
- 儲存和管理的憑證 （用於 AS2 通訊協定）
- 建立和管理 AS2 合約
- 建立和管理 EDIFACT 協議 （包括批次處理傳送側）
- 建立和管理 X12 協議 （包括批次處理傳送側）

![][1]


## <a name="as2-connector"></a>AS2 連接器
- 執行 AS2 合約相關 TPM API 應用程式執行個體中的定義
- 疑難排解的表面 AS2 處理/追蹤資訊


## <a name="biztalk-edifact"></a>BizTalk EDIFACT
- 執行 EDIFACT 合約相關 TPM API 應用程式執行個體中的定義
- 疑難排解的表面 EDIFACT 處理/追蹤資訊
- 中所定義 EDIFACT 協定相關 TPM API 應用程式執行個體中提供批次 （開始及停止） 的狀態的管理


## <a name="biztalk-x12"></a>BizTalk X12
- 執行 X12 合約相關 TPM API 應用程式執行個體中的定義 
- 呈現 X12 處理追蹤的疑難排解資訊
- 提供的批次 （開始及停止） 的狀態管理 X12 中所定義協定相關 TPM API 應用程式執行個體中

為先前陳述 AS2 X 12 和 EDIFACT API 應用程式要求函數 TPM API 應用程式，如預期般。


## <a name="getting-started"></a>快速入門
若要建立交易合作夥伴合約︰

1. 建立**BizTalk 交易夥伴管理**連接器的執行個體。 需要空白 SQL 資料庫函數。 之前開始，請務必有空白資料庫可用，以及可供使用。
2. 上傳結構描述和所需的合約的憑證。 請執行下列動作瀏覽 TPM 執行個體建立和逐步執行 「 結構描述 」 及/或 「 憑證 」 的組件
3. 瀏覽至所建立的 TPM 執行個體，然後在**合作夥伴**組件中的步驟
4. 建立所需的合作夥伴。 也編輯適當的設定檔，並新增必要的身分識別
5. 現在若要建立合約使用**合約**組件。 當您建立合約時，您必須先選取會使用的通訊協定。 剩餘的設定選項會根據您所選取的通訊協定。

![][2]

![][3]

<!--Image references-->
[1]: ./media/app-service-logic-create-a-trading-partner-agreement/TPMResourceView.png
[2]: ./media/app-service-logic-create-a-trading-partner-agreement/ProtocolSelection.png
[3]: ./media/app-service-logic-create-a-trading-partner-agreement/X12AgreementCreation.png
 
