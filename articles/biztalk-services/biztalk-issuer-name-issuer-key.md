<properties 
    pageTitle="發行者的名稱和 BizTalk 服務中的發行者鍵 |Microsoft Azure" 
    description="瞭解如何擷取發行者名稱和發行者鍵服務匯流排或存取控制 (ACS) 中 BizTalk 服務。 MABS WABS" 
    services="biztalk-services" 
    documentationCenter="" 
    authors="MandiOhlinger" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="biztalk-services" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="mandia"/>




# <a name="biztalk-services-issuer-name-and-issuer-key"></a>Biztalk︰ 發行者名稱和發行者鍵

Azure BizTalk 服務使用服務匯流排發行者名稱和發行者鍵，以及存取控制發行者的名稱和發行者鍵。 特別是︰

任務 | 哪些發行者名稱和發行者索引鍵
--- | ---
部署從 Visual Studio 應用程式 | 存取控制簽發者名稱和簽發者索引鍵
設定 Azure BizTalk Services 入口網站 | 存取控制發行者的名稱和發行者索引鍵
使用 Visual Studio 中介面卡 Biztalk 建立 LOB 轉送 | 服務匯流排發行者的名稱和發行者鍵

本主題列出的步驟來擷取 [簽發者名稱] 和 [簽發者鍵。 

## <a name="access-control-issuer-name-and-issuer-key"></a>存取控制發行者的名稱和發行者索引鍵
[Access 控制項簽發者名稱] 和 [簽發者鍵會使用下列步驟︰

- 在 Visual Studio 建立 Azure BizTalk 服務應用程式︰ 若要順利將 BizTalk 服務應用程式在 Visual Studio 中的部署至 Azure 中，輸入 [存取控制簽發者名稱] 和 [簽發者鍵。 
- Azure BizTalk Services 入口網站︰ 當您建立 BizTalk 服務，並開啟 BizTalk Services 入口網站時，您的 Access 控制項發行者的名稱及發行者金鑰自動登錄您部署與相同的存取控制值。

### <a name="to-copy-and-paste-the-access-control-issuer-name-and-issuer-key"></a>複製並貼上 [存取控制簽發者名稱] 和 [簽發者索引鍵

1. [Azure 傳統入口網站](http://go.microsoft.com/fwlink/p/?LinkID=213885)登入。
2. 在左側的功能窗格中，選取 [ **BizTalk 服務**]。
3. 選取您 BizTalk 服務。 
4. 選取 [工作列中的 [**連線資訊**]。 Access 控制項的命名空間，預設的發行者 （發行者名稱） 及預設的金鑰 （發行者） 會列出與可以會複製和貼上。  

摘要總結︰  
發行者名稱 = 預設發行者  
發行者鍵 = 預設索引鍵


您也可以選取**開啟 ACS 管理入口網站**以取得 Access 控制項的值︰

1. [Azure 傳統入口網站](http://go.microsoft.com/fwlink/p/?LinkID=213885)登入。
2. 在左側的功能窗格中，選取 [ **BizTalk 服務**]。
3. 選取您 BizTalk 服務。
4. 選取 [連接資訊] 按鈕，然後選取 [**開啟 ACS 管理入口網站**。
5. 在入口網站在**服務設定**] 下，選取**服務身分識別**。 隨後便會顯示您的服務身分識別，這是您存取控制簽發者名稱] 的值。 選取您服務身分識別] 連結，請參閱 [密碼]，這是您發行者索引鍵的值。 複製其值。<br/><br/>
例如，在**服務身分識別**，您會看到 「 擁有者 」。 「 擁有者 」 是您存取控制簽發者的名稱。 當您按 「 擁有者 」 連結時，您會看到**密碼**。 當您按一下 「 密碼 」 連結時，您會看到的值。 此密碼值是您存取 Control 簽發者鍵。  

摘要總結︰  
發行者名稱 = 服務身分識別的名稱  
發行者鍵 = 密碼值

在左側的功能窗格中，您也可以選取要擷取的存取控制值的**Active Directory** 。 

> [AZURE.IMPORTANT]建立 Access 控制項的命名空間時使用**Active Directory**服務身分識別**無法**自動建立。 當您佈建 BizTalk 服務，Access 控制項的命名空間，服務身分識別名為 「 擁有者 」 （簽發者的名稱） 的密碼 （簽發者按鍵），然後對稱金鑰會自動建立。<br /> 
[如何︰ 使用 ACS 管理服務設定服務身分識別](http://go.microsoft.com/fwlink/p/?LinkID=303942)上存取控制服務身分識別提供的更多資訊。


## <a name="service-bus-issuer-name-and-issuer-key"></a>服務匯流排簽發者名稱和簽發者鍵
服務匯流排發行者的名稱和發行者鍵會使用 BizTalk 介面卡服務。 Biztalk 專案 Visual Studio 中，您可以使用 Biztalk 介面卡連線到內部部署的線條商務 (LOB) 系統。 若要連線，您可以建立 LOB 轉送，並輸入 LOB 系統的詳細資訊。 這麼一來，您也輸入 [服務匯流排簽發者名稱] 和 [簽發者鍵。

### <a name="to-retrieve-the-service-bus-issuer-name-and-issuer-key"></a>若要擷取服務匯流排發行者的名稱和發行者鍵

1. [Azure 傳統入口網站](http://go.microsoft.com/fwlink/p/?LinkID=213885)登入。
2. 在左側的功能窗格中，選取 [**服務匯流排**]。
3. 選取您的命名空間。 任務列中，選取 [**連線資訊**。 隨後便會顯示**預設的發行者**（發行者名稱） 和**預設鍵**（發行者按鍵）。 複製其值。  

摘要總結︰  
發行者名稱 = 預設發行者  
發行者鍵 = 預設索引鍵

## <a name="next"></a>下一步
其他 Azure Biztalk 主題︰

-  [安裝 Azure Biztalk SDK](http://go.microsoft.com/fwlink/p/?LinkID=241589)<br/>
-  [教學課程︰ Azure BizTalk 服務](http://go.microsoft.com/fwlink/p/?LinkID=236944)<br/>
-  [我要如何開始使用 Azure BizTalk 服務 SDK](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
-  [Azure BizTalk 服務](http://go.microsoft.com/fwlink/p/?LinkID=303664)<br/>


## <a name="see-also"></a>另請參閱
-  [如何︰ 使用 ACS 管理服務設定服務身分識別](http://go.microsoft.com/fwlink/p/?LinkID=303942)<br/>
- [Biztalk︰ 開發人員、 基本、 標準和進階版圖表](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
- [Biztalk︰ 佈建使用 Azure 傳統入口網站](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
- [Biztalk︰ 佈建狀態圖表](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
- [Biztalk︰ 儀表板]、 監視器和縮放比例] 索引標籤](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
- [Biztalk︰ 備份與還原](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
- [Biztalk︰ 節流](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>
 
