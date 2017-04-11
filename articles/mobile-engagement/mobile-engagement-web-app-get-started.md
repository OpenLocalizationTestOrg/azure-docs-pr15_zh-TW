<properties
    pageTitle="快速入門 Azure 行動互動的 Web 應用程式 |Microsoft Azure"
    description="瞭解如何使用 Web 應用程式的分析及推入通知 Azure 行動互動。"
    services="mobile-engagement"
    documentationCenter="Mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="js"
    ms.topic="hero-article"
    ms.date="06/01/2016"
    ms.author="piyushjo" />

# <a name="get-started-with-azure-mobile-engagement-for-web-apps"></a>快速入門 Azure 行動互動的 Web 應用程式

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

本主題說明如何使用 Azure 行動互動，若要瞭解您的 Web 應用程式使用方式。

本教學課程的需求如下︰

+ Visual Studio 2015 或您選擇的任何其他編輯器
+ [Web SDK](http://aka.ms/P7b453) 

此網頁 SDK 預覽中僅支援分析，此時，並不支援傳送瀏覽器或應用程式中的推播通知。 

> [AZURE.NOTE] 若要完成此教學課程中，您必須使用中的 Azure 帳戶。 如果您沒有帳戶，您可以建立的免費的試用帳戶在幾分鐘。 如需詳細資訊，請參閱[Azure 免費試用版](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-web-app-get-started)。

##<a name="setup-mobile-engagement-for-your-web-app"></a>設定行動 Web 應用程式的互動

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

##<a id="connecting-app"></a>將您的應用程式連線到行動互動後端

本教學課程中會顯示 「 基本整合，「 這是最小收集資料所需的設定。

我們會示範整合，雖然您可以遵循的步驟，也建立 Visual Studio 以外的任何 web 應用程式的 Visual studio 建立基本的 web 應用程式。 

###<a name="create-a-new-web-app"></a>建立新 Web 應用程式

下列步驟假設使用 Visual Studio 2015，雖然步驟會在舊版的 Visual Studio 中類似。 

1. 啟動 [Visual Studio 中，並在 [**常用**] 畫面中，選取 [**新的專案**。

2. 在快顯功能表中，選取 [ **Web** -> **ASP.Net Web 應用程式**。 填滿]，在應用程式**名稱**、**位置**及**方案名稱**，，然後按一下**[確定]**。

3. 在**選取範本**快顯功能表中，選取**空白** **ASP.Net 4.5 範本**] 底下，按一下**[確定]**。 

現在您已建立新 Web 應用程式的專案讓我們會整合 Azure 行動互動 Web SDK。

###<a name="connect-your-app-to-mobile-engagement-backend"></a>將您的應用程式連線到行動互動後端

1. 建立新的資料夾，稱為**javascript**方案中，將 Web SDK JS 檔案**azure engagement.js** 。 

2. 新增下列程式碼此 javascript 資料夾中名為**main.js**檔案。 請務必一併更新連線字串。 此`azureEngagement`物件會用來存取 Web SDK 的方法。 

        var azureEngagement = {
            debug: true,
            connectionString: 'xxxxx'
        };

    ![Visual Studio js 檔案][1]

##<a name="enable-real-time-monitoring"></a>啟用即時監視

若要開始傳送資料，並確保作用中使用者，請您必須行動互動後端傳送至少有一個活動。 在 web 應用程式的內容中活動是網頁。 

1. 建立新頁面，您的方案中名為**home.html** ，並將其設為開始頁面，web 應用程式。 
2. 包含兩個 javascripts 我們新增之前在此頁面中加入下列本文標籤中。 

        <script type="text/javascript" src="javascript/main.js"></script>
        <script type="text/javascript" src="javascript/azure-engagement.js"></script>

3. 更新撥打 EngagementAgent 的主體標記`startActivity`方法
        
        <body onload="engagement.agent.startActivity('Home')">

4. 以下是您**home.html**的外觀
        
        <html>
        <head>
            ...
        </head>
        <body onload="engagement.agent.startActivity('Home')">
            <script type="text/javascript" src="javascript/main.js"></script>
            <script type="text/javascript" src="javascript/azure-engagement.js"></script>
        </body>
        </html>

##<a name="connect-app-with-real-time-monitoring"></a>使用即時監視連線應用程式

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

![][2]

##<a name="extend-analytics"></a>延伸狀況分析

以下是所有方法目前提供 Web SDK，您可以使用狀況分析︰

1. 活動/網頁︰

        engagement.agent.startActivity(name);
        engagement.agent.endActivity();

2. 事件
        
        engagement.agent.sendEvent(name, extras);

3. 錯誤

        engagement.agent.sendError(name, extras);

4. 工作

        engagement.agent.startJob(name);
        engagement.agent.endJob(name);

<!-- Images. -->
[1]: ./media/mobile-engagement-web-app-get-started/visual-studio-solution-js.png
[2]: ./media/mobile-engagement-web-app-get-started/session.png

