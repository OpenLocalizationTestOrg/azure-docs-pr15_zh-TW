<properties
    pageTitle="如何將結構描述版本 2015年-08-01-preview 移轉邏輯應用程式 |Microsoft Azure 應用程式服務"
    description="您可以輕鬆地移轉邏輯應用程式，為最新的結構描述版本。 請遵循下列步驟。"
    services="logic-apps"
    documentationCenter=""
    authors="MSFTMAN"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/23/2016"
    ms.author="deonhe"/>

# <a name="how-to-migrate-logic-apps-to-schema-version-2015-08-01-preview"></a>如何將結構描述版本 2015年-08-01-preview 移轉邏輯應用程式

若要將您現有的邏輯應用程式移至新的結構描述中，執行下列動作︰  
1. 開啟 Azure 入口網站中的邏輯應用程式  
2. 按一下 [更新結構描述︰

 ![API 圖示][step1]   
[更新結構描述] 頁面會顯示，並提供提供新的結構描述中的改良功能的詳細資訊的文件的連結︰ ![API 圖示][step2]

>[AZURE.NOTE] 當您選取**更新結構描述**時，我們會自動執行移轉步驟，並為您提供的程式碼輸出。 您可以使用此更新您的定義，不過，確保遵循良好的編碼方式，例如下方的**最佳作法**一節中所列。

## <a name="best-practices-when-migrating-your-logic-apps-to-the-latest-schema-version"></a>移轉您的邏輯應用程式的最新的結構描述版本時的最佳作法︰  

- 將移轉的指令碼複製到新的邏輯應用程式-不會覆寫的舊的其中一個鍵，直到您完成您的測試並確認移轉應用程式如預期般運作。
- 您邏輯應用程式**之前**生產環境中測試
- 移轉完成後，請啟動更新您的邏輯應用程式，讓您可以使用[受管理的 Api](./apis-list.md) 。 例如，您可以開始使用 Dropbox v2，您會使用 DropBox v1 做。


## <a name="whats-next"></a>下一步是什麼
-  [瞭解如何手動移轉邏輯應用程式](../app-service-logic/app-service-logic-schema-2015-08-01.md)


<!--Icon references-->
[step1]: ./media/connectors-schema-migration/migrateschema1.png
[step2]: ./media/connectors-schema-migration/migrateschema2.png






