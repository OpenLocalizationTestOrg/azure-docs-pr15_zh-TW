<properties
    pageTitle="MyDriving Azure IoT 範例︰ 快速入門 |Microsoft Azure"
    description="開始使用應用程式的完整示範如何使用 Microsoft Azure，包括串流分析、 電腦學習及事件集線器設計 IoT 系統。"
    services=""
    documentationCenter=".net"
    suite=""
    authors="harikmenon"
    manager="douge"/>

<tags
    ms.service="multiple"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="03/25/2016"
    ms.author="harikm"/>

# <a name="mydriving-iot-system-quick-start"></a>MyDriving IoT 系統︰ 快速入門

MyDriving 是不是系統示範的設計及實作的一般[網際網路的項目](iot-suite-overview.md)(IoT) 方案中，從裝置收集遙測程序在雲端，該資料與適用於電腦學習提供彈性的回應。 示範使用從您的行動電話及會從您的汽車控制系統資訊介面卡的資料來記錄您汽車旅遊相關資料。 使用這項資料來提供意見反應您與其他使用者的行駛樣式。

實際的 MyDriving 用途可以讓您開始建立您自己的 IoT 解決方案。 但之前，現在就讓我們協助您開始使用 MyDriving 應用程式本身，我們測試使用者小組成員。 這可讓您的應用程式，然後為消費者，其背後系統體驗之前您深入架構。 它也會向您介紹 HockeyApp，製作出酷炫的管理 alpha 和 beta 分配，您的應用程式的方法可以測試使用者。

## <a name="use-the-mobile-experience"></a>使用行動體驗

如果您在 Android、 iOS 或在 Windows 10 裝置，您可以使用 MyDriving 應用程式。

### <a name="android-and-windows-10-mobile-installation"></a>Android 與 Windows 10 行動裝置版安裝

在您的裝置︰

1.  允許開發應用程式︰

    -   Android︰ 在 [**設定** > **安全性**]，允許**不明**來源的相關應用程式。

    -   Windows 10︰ 在 [**設定** > **更新** > **的開發人員**，設定**開發人員模式**。

2.  加入我們的 beta 測試小組，註冊或登入， [HockeyApp](https://rink.hockeyapp.net)。 HockeyApp 讓您更容易散佈最早的版本類似，若要測試使用者應用程式。

    如果您使用的 Windows 10，請使用 [Edge 瀏覽器。

    如果您建立 2016年出席者，使用您在使用 Microsoft 按鈕的其中一個註冊的會議，同一個 Microsoft 帳戶電子郵件登入。 您已是 HockeyApp 與登入。

    ![HockeyApp 登入畫面](./media/iot-solution-get-started/image1.png)

3.  下載並安裝應用程式從這裡開始︰

    -   [Android](http://rink.io/spMyDrivingAndroid)

    -   [在 Windows 10](http://rink.io/spMyDrivingUWP)

    有兩個項目。 在 [**受信任的人員**安裝的憑證。 然後安裝應用程式。

*啟動應用程式在 Windows 10 行動裝置上的任何問題嗎？* 更新或兩個前，可能是您的電話。 請確定您已取得最新的更新，或安裝︰

 - [Microsoft.NET.Native.Framework.1.2.appx](https://download.hockeyapp.net/packages/win10/Microsoft.NET.Native.Framework.1.2.appx) 

 - [Microsoft.NET.Native.Runtime.1.1.appx](https://download.hockeyapp.net/packages/win10/Microsoft.NET.Native.Runtime.1.1.appx) 

 - [Microsoft.VCLibs.ARM.14.00.appx](https://download.hockeyapp.net/packages/win10/Microsoft.VCLibs.ARM.14.00.appx)


### <a name="ios-installation"></a>iOS 安裝

如果您參加建立 2016年，請下載應用程式 HockeyApp 我們測試小組成員︰

1.  IOS 裝置上登入[HockeyApp](https://rink.hockeyapp.net)。
    使用其中一個 Microsoft 登入] 按鈕，並使用您註冊與會議的相同 Microsoft 帳戶電子郵件登入。 （不使用的電子郵件和密碼的欄位）。

    ![HockeyApp 登入畫面](./media/iot-solution-get-started/image1.png)

2.  在 HockeyApp 儀表板中，選取 MyDriving 及下載它。

3.  授權從 HockeyApp beta 版︰

    。 移至 [**設定** > **一般** > **設定檔及裝置管理。**

    b。 信任**元場地 GmbH**憑證。

如果您未參加建立 2016年，您可以建立並部署您自己的應用程式︰

1.   下載的程式碼[從 GitHub]。

2.   建立並部署使用[Xamarin]。

[MyDriving 參考指南](http://aka.ms/mydrivingdocs)中尋找更多詳細資料。

## <a name="get-an-obd-adapter-optional"></a>取得 OBD 介面卡 （選用）

這是使這真實的網際網路的項目系統的組件 ！ 您可以使用的應用程式沒有，但更多變化的實的項目，並他們不耗費資源。

板診斷 (OBD) 是賽車車庫用來調整您車和診斷奇數雜音和警告燈的功能。 除非您汽車的絕佳 antiquity，您會發現端位置木屋給毀，通常是前 flap 底下儀表板中。 正確的連接器，您可以取得引擎的效能的指標並讓特定的調整。 您可以從主要位置廉價地購買 OBD 連接器。 連線至您的手機上的應用程式使用 Bluetooth 或需使用 Wi-fi]。

在此案例中，我們將賽車連線至雲端。 直接從 OBD 連線到您的電話，但我們的應用程式為轉送的運作。 您的汽車遙測傳送至 MyDriving IoT] 中心內，直接處理登入您的道路往返以及評估您行駛樣式。

若要連接 OBD 裝置︰

1.  檢查您的車有 OBD 通訊端。

2.  取得 OBD 介面卡︰

    -   如果您使用的在 Android 或 Windows 手機，您需要將藍牙啟用 OBD II 介面卡。 我們使用[BAFX 產品 34t5 Bluetooth OBDII 掃描的工具]。

    -   如果您使用的 iOS 手機，您需要有 Wi-fi 才能啟用 OBD 介面卡。 我們使用[ScanTool OBDLink MX 需使用 Wi-fi: OBD 介面卡/診斷掃描器]。

3.  依照您 OBD 介面卡連線至您的手機隨附的指示。 請注意下列動作︰

    -   將藍牙介面卡必須搭配手機，在 [**設定**] 頁面上。

    -   Wi-fi 介面卡必須擁有範圍 192.168.xxx.xxx 中的地址。

4.  如果您有數種汽車時，您可以將個別的介面卡取得每一個 （最多三個）。

如果您沒有安裝 OBD 介面卡，應用程式將仍傳送位置和速度資料從手機的 GPS 收件者的後端，並會詢問您是否要模擬 OBD。

您可以瞭解更多選項] 區段中 2.1，「 IoT 裝置，「 [MyDriving 參考指南](http://aka.ms/mydrivingdocs)中建立您自己的 OBD 裝置及相關應用程式如何使用 OBD 介面卡的資料。

## <a name="use-the-app"></a>使用應用程式

啟動應用程式。 有逐步引導您完成其運作方式的初始快速入門。

### <a name="track-your-trips"></a>追蹤您存取

點選 [記錄] 按鈕 （在畫面底部的大紅色圓圈） 開始旅行，然後再次點選 [結束。

![[記錄] 按鈕的追蹤出差行程井然有序的圖例](./media/iot-solution-get-started/image2.png)

每次啟動旅行，如果不有任何 OBD 裝置，請將會要求您是否您想要使用模擬器。

在出差結束時，點選 [停止] 按鈕，且您收到摘要。

![旅行摘要的範例](./media/iot-solution-get-started/image3.png)

### <a name="review-your-trips"></a>檢閱您存取

![過去的旅行的範例](./media/iot-solution-get-started/image4.png)

### <a name="review-your-profile"></a>檢閱您的設定檔

![推動樣式設定檔的範例](./media/iot-solution-get-started/image5.png)

## <a name="send-us-your-test-feedback"></a>將您測試的意見反應傳送給我們

因為我們建立以協助啟動 MyDriving IoT 系統，我們確實想要瞭解您的想法其運作方式。 讓我們知道︰

- 遇到困難或問題。

- 有延伸點，讓更多適用於您的狀況。

- 您找到更有效率的方法完成特定需求。

- 您有任何其他建議改善 MyDriving] 或 [此文件。

MyDriving 應用程式本身，您可以使用內建的 HockeyApp 意見反應機制︰ 在 iOS 和 Android，只要搖動功能，讓您的電話，或使用**意見反應**] 功能表命令。 這會自動附加螢幕擷取畫面，，讓我們知道您正在談論什麼。 並 HockeyApp 如果有任何可惜當機，收集到他們的意見告訴我們當機記錄。 您也可以提供透過[HockeyApp 入口網站]的意見反應。

您也可以檔案[GitHub 上的有問題]，或離開註解下方 (短-我們 edition)。

期待聽力您 ！

## <a name="next-steps"></a>後續步驟

-   瀏覽[MyDriving 參考指南](http://aka.ms/mydrivingdocs)瞭解我們如何已設計和建置整個 MyDriving 系統。

-   [建立並部署您自己的系統](iot-solution-build-system.md)使用我們 Azure 資源管理員指令碼。 [MyDriving 參考指南](http://aka.ms/mydrivingdocs)也會引導您就能在此讓大多數的自訂的區域。

  [從 GitHub]: https://github.com/Azure-Samples/MyDriving
  [使用 Xamarin]: https://developer.xamarin.com/guides/ios/getting_started/installation/
  [BAFX 產品 34t5 Bluetooth OBDII 掃描工具]: http://www.amazon.com/gp/product/B005NLQAHS
  [ScanTool OBDLink MX 需使用 Wi-fi: OBD 介面卡/診斷掃描器]: http://www.amazon.com/gp/product/B00OCYXTYY/ref=s9_simh_gw_g263_i1_r?pf_rd_m=ATVPDKIKX0DER&pf_rd_s=desktop-2&pf_rd_r=1MWRMKXK4KK9VYMJ44MP
  [HockeyApp 入口網站]: https://rink.hockeyapp.org
  [在 GitHub 時發生問題]: https://github.com/Azure-Samples/MyDriving/issues
