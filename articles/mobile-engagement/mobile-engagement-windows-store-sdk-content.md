<properties 
    pageTitle="Windows 通用應用程式 SDK 內容" 
    description="Azure 行動互動的瞭解 Windows 通用應用程式 SDK 的內容"                    
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede" 
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-windows-store" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="windows-universal-apps-sdk-content"></a>Windows 通用應用程式 SDK 內容

這份文件清單，並說明部署 SDK 應用程式中的內容。

##<a name="the-resources-folder"></a>`/Resources`資料夾

這個資料夾包含行動互動需要的所有資源。 您也可以自訂，以符合您的應用程式。

- `EngagementConfiguration.xml`: 行動互動的設定檔中，這是您可以在此自訂行動互動設定 （行動互動的連接字串，報告當機...）。

### <a name="html-folder"></a>/html 資料夾

- `EngagementNotification.html`: `Notification` Web 應用程式中橫幅檢視 html 設計。

- `EngagementAnnouncement.html`: `Announcement` Web app 中插入檢視的檢視 html 設計。

### <a name="images-folder"></a>/images 資料夾

- `EngagementIconNotification.png`︰ 在左邊的通知，顯示的品牌圖示取代您的品牌圖示來此項目。

- `EngagementIconOk.png`:`Ok`達到內容頁面的 [巨集指令或驗證] 按鈕的圖示。

- `EngagementIconNOK.png`:`NOK`達到內容頁面的 [驗證] 按鈕會停用時所使用的圖示。
 
- `EngagementIconClose.png`:`Close`達到通知和解除] 按鈕的內容的圖示。

### <a name="overlay-folder"></a>/overlay 資料夾

- `EngagementPageOverlay.cs`︰ 負責新增互動覆疊頁面連絡其孩子的應用程式使用者介面。
  
