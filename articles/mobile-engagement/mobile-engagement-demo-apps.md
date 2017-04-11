<properties
    pageTitle="Azure 行動互動示範應用程式 |Microsoft Azure"
    description="說明下載位置、 如何使用，以及使用 Azure 行動互動示範應用程式的優點"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/10/2016"
    ms.author="piyushjo" />

# <a name="azure-mobile-engagement-demo-app"></a>Azure 行動互動示範應用程式

我們已發佈**iOS**、 **Android**、 和**Windows**平台可協助您尋找實用的資源，並深入瞭解行動互動的 Azure 行動互動示範應用程式。

應用程式可協助您︰

- 輕鬆地找到實用連結行動互動和之類的資源影片、 文件、 的支援論壇，移至提高功能要求的位置。
- 支援的行動電話的行動應用程式的互動的體驗範例通知。
- 使用參考實作學習如何將您自己的應用程式實作行動互動。 您可以瞭解如何︰

    - 收集分析資料。
    - 進階的類型，例如*全螢幕插入*或*快顯*通知案例的實作。
    - 實作問卷調查和輪詢的回覆。
    - 實作無推入的資料與推入案例。   

## <a name="app-installation"></a>應用程式安裝
此應用程式有下列應用程式存放區中︰

- **Windows 通用示範應用程式**︰

    - 下載[Windows 應用程式儲存](https://www.microsoft.com/en-us/store/apps/azure-mobile-engagement/9nblggh4qmh2)在應用程式。
    - 為 Windows 10 通用的應用程式開發應用程式。 在[GitHub](https://github.com/Azure/azure-mobile-engagement-app-windows)上使用的程式碼。

- **iOS 示範應用程式**︰

    - 下載在[Apple 儲存](https://itunes.apple.com/us/app/azure%20mobile%20engagement/id1105090090)應用程式。
    - Ios 快速開發應用程式。 在[GitHub](https://github.com/Azure/azure-mobile-engagement-app-ios)上使用的程式碼。

- **Android 的示範應用程式**︰

    - 下載在[Google Play 儲存](https://play.google.com/store/apps/details?id=com.microsoft.azure.engagement)應用程式。
    - 在[GitHub](https://github.com/Azure/azure-mobile-engagement-app-android)上使用的程式碼。

![Windows 通用示範應用程式][1]

![iOS 示範應用程式][2]
![Android 示範應用程式][3]


## <a name="usage"></a>使用方式

您可以透過下列方式來使用此應用程式︰

**下載您的應用程式的裝置上的應用程式儲存 （提供更舊版本） 的連結︰**

>[AZURE.IMPORTANT] 您不需要的 Azure 帳戶或連線到後端的 [應用程式的需求。 應用程式的獨立地運作。

- 您已在裝置上的應用程式之後，您可以瀏覽若要尋找有關行動互動實用的資源的 [左側] 功能表中的連結。
- 我們已將[服務的 RSS 摘要](https://aka.ms/azmerssfeed)加到此應用程式，讓您隨時更新相關最新的產品更新。
- 您也可以瀏覽遇到的每個平台支援的行動互動通知類型的範例通知案例。 這些通知可以遇到本機-是的您可以按一下以顯示您通知體驗，也就是相同的行動互動平台從傳送通知的螢幕上的按鈕。

![在 Windows 版的應用程式] 功能表][4]

![IOS 版應用程式功能表][5]
![Android 適用的應用程式] 功能表][6]

**下載的程式碼 （提供更舊版本） GitHub 連結︰**

- 下載的程式碼後，請在個別的開發環境-XCode iOS，Android Studio for Android 中和 Visual Studio for Windows 版中開啟。
- 下一步應該先按照我們[SDK 整合的基本步驟](mobile-engagement-windows-store-dotnet-get-started.md)，讓您能夠將此應用程式連線到它自己的行動互動後端執行個體。
    - 您需要設定應用程式中的 [連線字串。
    - 您也需要設定您的應用程式的推入通知平台。
- 您會注意到應用程式本身進行檢測與行動互動。 因此，您可以開啟應用程式之後，連線到後端，您可以看到 [**監視器**] 索引標籤上的 [使用者工作階段、 活動、 活動和等等。
- 您也可以傳送通知給此應用程式中，從您自己的行動互動執行個體，而不是使用本機的通知。
    - 以下您也可以使用應用程式中的 [**取得裝置識別碼**功能表項目，以新增您的裝置測試裝置。 這可讓您的裝置識別碼與您再為測試裝置以註冊您的平台後端執行個體。

    ![在 Windows 裝置識別碼][7]

    ![在 iOS 裝置識別碼][8]
    ![Android 的裝置識別碼][9]

## <a name="key-features-of-the-demo-app"></a>重要功能的示範應用程式

- 如先前所述，使用此應用程式，您必須索引鍵的所有資源行動互動的手中。 在左功能表上，您可以前往的連結。

- 您可能遇到每個平台的出應用程式的通知。 為**只通知**，位置按一下通知即可開啟應用程式的原生螢幕 （藉由使用**深層連結**）-或**網頁的通知**，您可以在其中傳送從行動互動後端時發出通知顯示額外的 HTML 內容，則可以傳送這些通知。

    ![出應用程式通知][29]

- 在 iOS，您必須關閉應用程式，請參閱出應用程式或系統的推播通知。 您可以查看新增**動作按鈕**時，會新增*意見反應*及*共用*此出應用程式通知 （讓使用者可以採取動作右，從本身的通知） 類似以下的實作。

    ![在 iOS 上出應用程式通知][11] ![在 iOS 上出應用程式通知顯示][14]

- 在 Android 上的支援選項要新增多行文字 （**顯示較大的文字**） 或通知圖像 （**一窺全貌**） 的通知，以及**動作] 按鈕**（如下圖所支援 iOS）。

    ![在 Android 上出應用程式通知][12] ![在 Android 上出應用程式通知顯示][15]

- 在 Windows 10 中，您可以看到通知] 在電腦上的外觀。 此通知也會出現在 Windows 10**通知中心**。 有不支援在 Windows SDK 中新增**動作按鈕**。

    ![在 Windows 上的應用程式出通知][10] ![在 Windows 上的應用程式出顯示][13]

- 您可以體驗每個平台的預設 「 在應用程式 」 通知。 這是第一次顯示的**通知**視窗是兩個步驟體驗。 當您按一下它時，將其開啟完整的畫面**通知**，如以下的螢幕擷取畫面顯示。 此通知的內容是來自您的行動互動後端執行個體。 SDK 有通知] 及 [宣告的範本。 您可以輕鬆地自訂，如下所示的我們的標誌和色彩此示範應用程式。  

    ![在 Windows 上的應用程式通知][16]

    ![在 iOS 上的應用程式通知][17]  ![在 Android 上的應用程式通知][18]

    **iOS** **Android**

- 您也可以使用 [**類別**] 功能，行動互動的自訂此預設的體驗。 示範應用程式，我們已示範兩個常見的方法可以變更通知的體驗。 請注意，[類別] 功能尚未支援 Windows SDK 中。

    **全螢幕插入︰**

    ![在 [應用程式通知-插入類別][30]

    ![在 iOS 上插入類別][21]  ![在 Android 上插入類別][22]

    **快顯通知︰**

    ![在 [應用程式通知-快顯的類別][31]

    ![在 iOS 上的快顯通知][19]   ![在 Android 上的快顯通知][20]

**iOS** **Android**

- 行動互動也支援特殊的類型的一部分**投票**的應用程式通知。 這個選項可讓您快速問卷調查傳送給您區段的應用程式的使用者。 您可以新增問題以及每個問題，如以下的螢幕擷取畫面所示的選項。 此然後取得顯示為應用程式使用者的應用程式的通知。   

    ![投票通知][32]

    ![在 Windows 上的問卷][26]

    ![在 iOS 上問卷][27]   ![在 Android 上的問卷][28]

**iOS** **Android**

- 行動互動也支援傳送**無資料推播**通知]。 使用這些通知，您可以傳送資料服務 （例如 JSON 資料在下列範例），您可以處理您的應用程式，並採取行動。 例如，如何我們要變更的項目選擇性地使用資料推入通知。

    ![資料推入通知][33]

    ![在 Windows 上的資料推入通知][23]

    ![在 iOS 上的資料推入通知][24]  ![在 Android 上的資料推入通知][25]

**iOS** **Android**

> [AZURE.NOTE] 按一下任何範例通知畫面上的 [**如何傳送這些通知行動互動平台上的指示，請按一下這裡**，您可以檢視任何這些通知的詳細逐步的指示。


[1]: ./media/mobile-engagement-demo-apps/home-windows.png
[2]: ./media/mobile-engagement-demo-apps/home-ios.png
[3]: ./media/mobile-engagement-demo-apps/home-android.png
[4]: ./media/mobile-engagement-demo-apps/menu-windows.png
[5]: ./media/mobile-engagement-demo-apps/menu-ios.png
[6]: ./media/mobile-engagement-demo-apps/menu-android.png
[7]: ./media/mobile-engagement-demo-apps/device-id-windows.png
[8]: ./media/mobile-engagement-demo-apps/device-id-ios.png
[9]: ./media/mobile-engagement-demo-apps/device-id-android.png
[10]: ./media/mobile-engagement-demo-apps/out-of-app-windows.png
[11]: ./media/mobile-engagement-demo-apps/out-of-app-ios.png
[12]: ./media/mobile-engagement-demo-apps/out-of-app-android.png
[13]: ./media/mobile-engagement-demo-apps/out-of-app-display-windows.png
[14]: ./media/mobile-engagement-demo-apps/out-of-app-display-ios.png
[15]: ./media/mobile-engagement-demo-apps/out-of-app-display-android.png
[16]: ./media/mobile-engagement-demo-apps/in-app-windows.png
[17]: ./media/mobile-engagement-demo-apps/in-app-ios.png
[18]: ./media/mobile-engagement-demo-apps/in-app-android.png
[19]: ./media/mobile-engagement-demo-apps/pop-up-ios.png
[20]: ./media/mobile-engagement-demo-apps/pop-up-android.png
[21]: ./media/mobile-engagement-demo-apps/interstitial-ios.png
[22]: ./media/mobile-engagement-demo-apps/interstitial-android.png
[23]: ./media/mobile-engagement-demo-apps/data-push-windows.png
[24]: ./media/mobile-engagement-demo-apps/data-push-ios.png
[25]: ./media/mobile-engagement-demo-apps/data-push-android.png
[26]: ./media/mobile-engagement-demo-apps/survey-windows.png
[27]: ./media/mobile-engagement-demo-apps/survey-ios.png
[28]: ./media/mobile-engagement-demo-apps/survey-android.png
[29]: ./media/mobile-engagement-demo-apps/out-of-app.png
[30]: ./media/mobile-engagement-demo-apps/in-app-interstitial.png
[31]: ./media/mobile-engagement-demo-apps/in-app-pop-up.png
[32]: ./media/mobile-engagement-demo-apps/notification-poll.png
[33]: ./media/mobile-engagement-demo-apps/notification-data-push.png
