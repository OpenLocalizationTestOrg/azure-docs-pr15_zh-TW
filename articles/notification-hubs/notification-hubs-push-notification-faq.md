<properties
    pageTitle="Azure 通知集線器-常見問題集 (Faq)"
    description="在設計/實作上通知集線器方案的常見問題集"
    services="notification-hubs"
    documentationCenter="mobile"
    authors="ysxu"
    manager="erikre"
    keywords="推播通知、 推入通知，iOS 推入通知、 android 推入通知，ios 推入、 android 推入"
    editor="" />

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="yuaxu" />

#<a name="push-notifications-with-azure-notification-hubs---frequently-asked-questions"></a>推播通知與 Azure 通知集線器-常見問題集

##<a name="general"></a>一般
###<a name="1---what-is-the-price-model-for-notification-hubs"></a>1.什麼是通知集線器價格模型？
通知集線器提供三層︰

* **免費**-取得最多有 1 百萬個推入每個月的訂閱。
* **基本**-取得 10 百萬推入每個月比較基準，配額等比級數選項的訂閱。
* **標準**-取得每個訂閱月比較基準 10 百萬推入的配額與增加 [選項]，再加上 rtf 遙測功能。

可以在[通知集線器價格]] 頁面上找到最新詳細資料。 價格建立訂閱層級，並讓不論多少 Azure 訂閱中建立的命名空間或通知集線器根據推入通知 initiations 數目。

提供**免費**層與 SLA 保證開發用途。 雖然這一層可能會想要探索的推入通知，透過 Azure 通知集線器功能的絕佳起點，它可能無法中大型應用程式的最佳選擇。

**基本** & **標準**層會提供下列重要功能生產用法啟用*僅適用於標準層*︰

- *Rtf 遙測*-通知集線器優惠的數字的匯出遙測資料，以及推播通知註冊資訊供離線檢視及分析功能。
- *多 tenancy* -理想，如果您要建立使用通知集線器支援多個租用戶的行動應用程式。 這個選項可讓您在應用程式的通知中心命名空間層級設定推入通知服務 (PNS) 認證，然後您可以將提供其個別集線器下這個常見的命名空間租用戶。 保留 [傳送及接收推入通知，從通知集線器 SA 按鍵隔離確保非跨租用戶重疊每個租用戶時，這可讓輕鬆進行的維修作業。
- *排定的推入*-可讓您排程推入通知，將後續佇列並送出。
- *大量匯入*-可讓您匯入大量登錄。

###<a name="2---what-is-the-notification-hubs-sla"></a>2.什麼是通知集線器 SLA？
**基本**和**標準**通知集線器層我們保證至少 99.9%的時間，經妥善設定的應用程式會無法傳送推入通知，或執行註冊管理作業提供的支援層內部署通知中心解答。 若要深入瞭解我們 SLA，請造訪[通知集線器 SLA]頁面。

> [AZURE.NOTE] 由於通知集線器外部的平台提供者提供的推入通知的裝置而定，有的平台通知服務與裝置之間以任何 SLA 保證。

###<a name="3---which-customers-are-using-notification-hubs"></a>3.哪些客戶使用的通知集線器？
我們有大量的客戶通知集線器使用下列幾個主要檔案︰

* Sochi 2014 – 100 s 感興趣的群組，3 + 百萬裝置、 分派 2 週 150 + 百萬通知。 [CaseStudy-Sochi]
* Skanska- [CaseStudy-Skanska]
* 西雅圖時間- [CaseStudy-西雅圖時間]
* Mural.ly- [CaseStudy-Mural.ly]
* 7Digital- [CaseStudy-7Digital]
* Bing 應用程式 – 的裝置，數百萬 10s 傳送 3 百萬通知一天。

###<a name="4-how-do-i-upgrade-or-downgrade-my-notification-hubs-to-change-my-service-tier"></a>4.如何升級或降級我通知集線器，若要變更我的服務層級？
移至[Azure 傳統入口網站]，按一下服務匯流排，按一下您的命名空間然後通知中心。 在 [縮放比例] 索引標籤中，您可以變更通知集線器層。

![](./media/notification-hubs-faq/notification-hubs-classic-portal-scale.png)

##<a name="design--development"></a>設計和開發
###<a name="1---which-server-side-platforms-do-you-support"></a>1.您所支援的伺服器端平台？
我們提供 Sdk 和[完整的範例].NET、 Java、 PHP、 Python、 Node.js 讓應用程式後端可設定與通訊通知集線器使用任何這些平台。 通知集線器 Api 根據其餘介面，您可以選擇直接與這些是如果您不想要新增額外的相依性。 可以在[NH-REST Api] ] 頁面上找到更多詳細資料。

###<a name="2---which-client-platforms-do-you-support"></a>2.您所支援的用戶端平台？
我們支援[Apple iOS](notification-hubs-ios-apple-push-notification-apns-get-started.md)、 [Android](notification-hubs-android-push-notification-google-gcm-get-started.md)、[通用 Windows](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)、 [Windows Phone](notification-hubs-windows-mobile-push-notifications-mpns.md)、 [Kindle](notification-hubs-kindle-amazon-adm-push-notification.md)、 [Android 中國 （透過 Baidu)](notification-hubs-baidu-china-android-notifications-get-started.md)、 Xamarin 傳送推入通知 ([iOS](xamarin-notification-hubs-ios-push-notification-apns-get-started.md) & [Android](xamarin-notification-hubs-push-notifications-android-gcm.md))， [Chrome 應用程式](notification-hubs-chrome-push-notifications-get-started.md)和[Safari](https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSafari)平台。 快速入門處理傳送推入通知，這些平台上的教學課程的完整清單，請造訪我們[NH-快速入門教學課程]的網頁。

###<a name="3---do-you-support-smsemailweb-notifications"></a>3.您是否支援網頁版 SMS/電子郵件通知？
通知集線器是主要是設計用來傳送通知給使用上述的平台的行動應用程式。 我們尚不提供這項功能可以傳送電子郵件或簡訊通知。不過協力廠商平台可提供這些功能可整合以及通知集線器使用[Azure Mobile 應用程式]傳送原生推入通知。

通知集線器也不會提供在瀏覽器推入通知傳送服務--現成。 客戶可以選擇實作這使用 SignalR 支援的伺服器端平台的上方。 如果您想要傳送通知給在 Chrome 沙箱的瀏覽器應用程式，請參閱[Chrome 應用程式教學課程]。

###<a name="4---what-is-the-relation-between-azure-mobile-apps-and-azure-notification-hubs-and-when-do-i-use-what"></a>4.什麼是 Azure 行動應用程式與 Azure 通知集線器之間的關聯，以及何時使用什麼？
如果您有現有的行動應用程式後端，且您只想要新增的功能傳送推入通知，您可以使用 Azure 通知集線器。 如果您想要設定您的行動應用程式後端從頭您應該考慮使用 Azure 行動應用程式。 Azure 行動應用程式會自動佈建新的通知中心，您就能輕鬆地傳送行動應用程式後端的推播通知。 價格 Azure Mobile 應用程式的通知中心包含基底費用，且只支付時，您可以試試包含推入。 在[應用程式服務價格]] 頁面上有更多詳細資料，加上。

###<a name="5---how-many-devices-can-i-support-if-i-send-push-notifications-via-notification-hubs"></a>5.如果我將傳送通知集線器透過推入通知，我有支援多少裝置可以？
請參閱支援的裝置數目的詳細資料的[通知集線器價格]頁面。

某些情況下，如果您需要支援多個 10000000 註冊裝置，請直接[與我們連絡](https://azure.microsoft.com/overview/contact-us/)，我們將協助您調整您的方案。

###<a name="6---how-many-push-notifications-can-i-send-out"></a>6.多少推入通知可以傳送？
根據所選的層 Azure 會自動縮放根據通過系統的通知數目。

>[AZURE.NOTE] 整體使用成本可以向上根據提供的推入通知數目。 請確定您是知道大綱在[通知集線器價格]] 頁面上的現有層限制。

現有客戶使用通知集線器每天傳送數百萬推播通知。 您沒有任何特別來調整您的推入通知連絡，只要您使用的 Azure 通知集線器。

###<a name="7---how-long-does-it-take-for-sent-push-notifications-to-reach-my-device"></a>7。 如何時間的傳送推播通知達到裝置？
Azure 通知集線器就無法處理至少**有 1 百萬個推入通知傳送到一分鐘**在標準模式中使用的案例的內送的載入很一致及不 spikey 性質的位置。 這個速度可能會有所不同數標記、 性質的內送的傳送和其他外部因素。

估計的傳遞期間服務可計算每個個別的推入通知傳送服務根據註冊的標籤標籤運算式的平台和傳送郵件的目標。 以下是推入通知服務 (PNS)，傳送通知給裝置的責任。

PNS 不保證任何 SLA 來傳送通知。不過，通常是大部分的推入通知傳遞到目標裝置 （通常限制內的 10 分鐘的時間） 在幾分鐘內傳送給我們的平台的時間。 可能有幾個範數可能需要更多的時間。

>[AZURE.NOTE] Azure 通知集線器有一個原則卸除其無法傳遞至 PNS 30 分鐘任何推入通知的位置。 此延遲也可能會發生的原因，最常 PNS 節流設定您的應用程式。

###<a name="8---is-there-any-latency-guarantee"></a>8.有任何延遲保證嗎？
由於推入通知 （傳送外部、 特定推入通知服務） 的性質，也不會延遲保證。 一般而言，大部分的推入通知傳送幾分鐘內。

###<a name="9---what-are-the-considerations-i-need-to-take-into-account-when-designing-a-solution-with-namespaces-and-notification-hubs"></a>9。 我需要考慮設計與命名空間和通知集線器解決方案時的考量是什麼？

####<a name="mobile-appenvironment"></a>行動裝置的應用程式環境

* 應該行動應用程式，每個環境每一個通知中心。
* 在多租用戶案例中，每個租用戶都應具備個別的中心。
* 這可能會導致問題時傳送通知時，您必須永遠不會共用相同的通知中心之間測試和生產環境。 例如 Apple 提供沙箱和生產推播端點與各有不同的認證。
* 根據預設，您可以傳送測試通知您已註冊的裝置，透過 Azure 入口網站或 Visual Studio 中 Azure 整合的元件。 臨界值會設定為 10 註冊資料庫中隨機選取的裝置。

>[AZURE.NOTE] 如果中樞已設定原本 Apple 沙箱憑證，然後重新設定為使用 Apple 生產憑證舊的裝置權杖會變成無效使用新的憑證，並會導致失敗的推入。 最好分隔您生產並測試環境不同環境中使用不同的集線器。

####<a name="pns-credentials"></a>PNS 認證

當行動應用程式已註冊的平台的開發人員入口網站 （例如 Apple 或 Google 等） 然後會出現應用程式識別碼與安全性權杖應用程式後端需要提供的平台推入通知服務能夠推入通知傳送至 [裝置]。 在表單中的憑證 （例如 Apple iOS 或 Windows Phone 版） 或 （Google Android、 Windows 等） 的安全性鍵可這些安全性權杖需要通知集線器設定。 這通常是完成的通知中心層級，但也可以在命名空間層級多租用戶情境中完成。

####<a name="namespaces"></a>命名空間

命名空間可用於部署分組。  也可代表所有通知集線器適用於所有承租人的相同的應用程式，在多租用戶的案例。

####<a name="geo-distribution"></a>地理通訊群組

地理分配不一定要徑推入通知案例中。 這是要注意各種推入通知服務 （例如 APNS、 GCM 等），最後傳送至裝置的推入通知，不均勻分佈其中。

如果您有使用世界各地的應用程式，您可以建立多個集線器利用通知集線器服務的可用性各地不同 Azure 區域中的不同命名空間。

>[AZURE.NOTE] 這會增加周圍登錄，特別是管理成本，讓這不是真正的建議，您必須明確需要時僅完成。

###<a name="10--should-i-do-registrations-from-the-app-backend-or-directly-through-client-devices"></a>10.我該如何登錄應用程式後端，或直接透過用戶端裝置？
當您只需要建立註冊之前的用戶端驗證，或您有必須建立或修改的部分應用程式邏輯為基礎的應用程式後端的標籤，登錄應用程式後端很有用。 如需詳細資訊，您可以瞭解更多的[後端註冊指引]與[後端註冊指引-2]的頁面。

###<a name="11--what-is-the-push-notification-delivery-security-model"></a>11。 推入通知傳送安全性模型是什麼？
Azure 通知集線器使用[共用 Access 簽章 (SA)](../storage/storage-dotnet-shared-access-signature-part-1.md)為基礎的安全性模型。 您可以使用 SA 權杖，根命名空間層級或細微通知集線器層級。 這些 SA 權杖可以與其他授權規則例如傳送郵件的權限，請設定聆聽通知權限等等。更多詳細資料，可在[NH 安全性模型]文件中。

###<a name="12--how-should-i-handle-sensitive-payload-in-the-push-notifications"></a>12。 如何處理推入通知中的機密內容？
所有通知將都傳送至目標裝置的平台的推入通知服務 (PNS)。 當寄件者會傳送通知給 Azure 通知集線器我們處理程序，並通知為了分別 PNS。

所有連線寄件者至 Azure 通知集線器再 PNS 都使用 HTTPS。

>[AZURE.NOTE] Azure 通知集線器不會以任何方式記錄郵件的內容。

傳送的機密內容不過建議安全推播圖樣的寄件者提供 「 偵測 （ping） 」 通知訊息且識別項裝置不需要區分裝載及裝置上的應用程式會收到此內容的位置，則可以呼叫安全 API 直接擷取訊息詳細資料。 如何實作模式上述輔助線會提供[NH-安全推播教學課程]頁面。

##<a name="operations"></a>作業
###<a name="1---what-is-the-disaster-recovery-dr-story"></a>1.了損毀復原 (DR)？
我們提供的中繼資料損毀修復涵蓋我們結束 （通知中心名稱、 連接字串及其他重要資訊）。 觸發 DR 情況時，進行之註冊資料就會是將會遺失這些通知集線器基礎結構的**只區段**。 您必須執行重新將此資料填入新的中心後復原的解決方案。

- *Step1* -建立不同的資料中心中的次要的通知中心。 您可以先將此即時建立，DR 事件的時間，或您可以建立一個開始移。 它不會使許多不同的通知中心佈建不相當快速的程序，數秒順序，因此您選擇哪個選項。 無法從開頭會讓您從影響您管理的功能，因此強烈建議您選擇的 DR 事件。

- *遵循步驟 2* -Hydrate 從主要的通知中心註冊使用第二個通知中心。 不建議使用嘗試維持在兩個集線器登錄並嘗試將保留這些即時同步登錄因為-通常的 PNS 端卻沒有找到也由於固有之集中趨勢估計的登錄到期。 通知集線器他們清除我們收到關於過期或無效的登錄的 PNS 意見反應。  

若要使用的應用程式後端的其中一個建議︰

- 維護其結尾的登錄指定的設定，讓它可以執行大量插入到若 DR 次要通知中心

**OR**

- 取得一般傾印從主要的中心當成備份註冊的然後會大量插入到第二個 NH。

>[AZURE.NOTE] 在標準層可用的登錄匯出/匯入功能說明[登錄匯出/匯入]文件中。

如果您沒有安裝後端，然後在任何目標裝置上啟動應用程式時，它們會執行，新的註冊在次要的通知中心內，而且最後次要通知中心會發生註冊所有作用中的裝置。

缺點是會當裝置位置的應用程式沒有開啟設定不會收到通知的時間範圍。

###<a name="2---is-there-any-audit-log-capability"></a>2.是否有任何稽核記錄功能？
所有通知集線器管理作業都移至作業記錄檔中[Azure 傳統入口網站]公開。

##<a name="monitoring--troubleshooting"></a>監控與疑難排解
###<a name="1---what-troubleshooting-capabilities-are-available"></a>1.哪些疑難排解功能可使用？
Azure 通知集線器提供數種功能，執行一般的疑難排解，尤其是在周圍放置通知最常見的情況。 請參閱中的詳細資料我們[NH-疑難排解]白皮書。

###<a name="2---what-telemetry-features-are-available"></a>2.遙測功能可使用？
[Azure 傳統入口網站]中檢視遙測資料的 azure 通知集線器啟用。 可用的標準的詳細資訊，可在[NH-指標]] 頁面上。

>[AZURE.NOTE] 成功通知僅有意義的推入通知傳遞給外部的推入通知服務 （例如的 Apple APNS、 GCM 的 Google 等）。 這是最 PNS 傳遞到目標裝置的通知。 一般而言，PNS 不會揭露傳遞給第三方的指標。  

我們也會提供這項功能可以匯出遙測資料以程式設計方式 （在**標準**層）。 請參閱[NH-指標範例]，如需詳細資訊。

[Azure 傳統入口網站]: https://manage.windowsazure.com
[通知集線器價格]: http://azure.microsoft.com/pricing/details/notification-hubs/
[通知集線器 SLA]: http://azure.microsoft.com/support/legal/sla/
[CaseStudy-Sochi]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=7942
[CaseStudy-Skanska]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=5847
[CaseStudy-西雅圖時間]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=8354
[CaseStudy-Mural.ly]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=11592
[CaseStudy-7Digital]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=3684
[NH-REST Api]: https://msdn.microsoft.com/library/azure/dn530746.aspx
[NH-開始使用教學課程]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
[Chrome 應用程式教學課程]: http://azure.microsoft.com/documentation/articles/notification-hubs-chrome-get-started/
[Mobile Services Pricing]: http://azure.microsoft.com/pricing/details/mobile-services/
[後端註冊指南]: https://msdn.microsoft.com/library/azure/dn743807.aspx
[後端註冊指引-2]: https://msdn.microsoft.com/library/azure/dn530747.aspx
[NH 安全性模型]: https://msdn.microsoft.com/library/azure/dn495373.aspx
[NH-安全推入教學課程]: http://azure.microsoft.com/documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/
[NH-疑難排解]: http://azure.microsoft.com/documentation/articles/notification-hubs-diagnosing/
[NH-指標]: https://msdn.microsoft.com/library/dn458822.aspx
[NH-指標範例]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/FetchNHTelemetryInExcel
[登錄匯出/匯入]: https://msdn.microsoft.com/library/dn790624.aspx
[Azure Portal]: https://portal.azure.com
[完成的範例]: https://github.com/Azure/azure-notificationhubs-samples
[Azure 行動應用程式]: https://azure.microsoft.com/en-us/services/app-service/mobile/
[應用程式服務價格]: https://azure.microsoft.com/en-us/pricing/details/app-service/
