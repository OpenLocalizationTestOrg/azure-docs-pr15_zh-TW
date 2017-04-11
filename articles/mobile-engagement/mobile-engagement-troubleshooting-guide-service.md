<properties 
   pageTitle="疑難排解指南-服務 azure 行動互動" 
   description="疑難排解指南 Azure 行動互動" 
   services="mobile-engagement" 
   documentationCenter="" 
   authors="piyushjo" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="mobile-multiple"
   ms.workload="mobile" 
   ms.date="08/19/2016"
   ms.author="piyushjo"/>

# <a name="troubleshooting-guide-for-service-issues"></a>服務問題的疑難排解指南

以下是可能的問題，您可能會遇到的 Azure 行動互動的執行方式。

## <a name="service-outages"></a>服務中斷

### <a name="issue"></a>問題
- 顯示為會因 Azure 行動互動服務中斷的問題。

### <a name="causes"></a>原因
- 顯示為會因 Azure 行動互動服務中斷的問題可能因數種不同的問題︰
    - 原本顯示 [Azure 行動互動的所有系統的隔離的問題
    - 伺服器中斷 （不一定會顯示在伺服器狀態） 所造成的已知的問題︰
    - 排程延遲、 目標錯誤、 徽章更新問題收集，使用 [推入停駐點的統計資料停駐點，API 的停止運作，新的應用程式使用者無法建立或，DNS 錯誤與逾時錯誤 UI、 API 或應用程式中的裝置上。
    - 雲端相依性中斷[Azure 服務狀態](http://status.azure.com/)
    - 推入通知服務 (PNS) 相依性中斷
    - App Store 中斷

1) 若要測試是否為系統問題，您可以測試從不同的同一個函數
   
   - Azure 行動互動整合應用程式
   - 測試裝置
   - 測試裝置作業系統版本
   - 行銷活動
   - 系統管理員的使用者帳戶
   - 在瀏覽器 （IE，Firefox、 Chrome、 等）
   - 電腦

2) 若要測試如果 UI 或 API，只會影響問題︰

   - 測試從 Azure 行動互動 UI 和 Azure 行動互動 API 相同的功能。

3) 若要測試如果問題是與您的行動電話網路︰

   - 測試連線到網際網路透過 WIFI 和時透過 3g 行動電話網路連線時。
   - 確認您的防火牆不會封鎖的任何 Azure 行動互動 IP 位址或連接埠。

4) 若要測試如果問題是與您的裝置︰

   - 測試您的裝置是否能夠使用另一個 Azure 行動互動整合式應用程式連線至 Azure 行動互動。
   - 您可以從您可以在 Azure 行動互動使用者介面中看到的電話產生的事件、 工作和當機的測試。 
   - 測試您可以從 Azure 行動互動 UI 傳送推入通知，至您的裝置根據其裝置識別碼。 

5) 若要測試如果問題是與您的應用程式︰

   - 安裝並測試您的應用程式，而不是從實體裝置模擬器︰
   
6) 若要測試如果問題是 OS 升級至使用者需要解決 SDK 升級的裝置︰

   - 測試您的應用程式在不同版本的作業系統不同的裝置上。
   - 確認您使用的 SDK 的最新版本。
 
## <a name="connectivity-and-incorrect-information-issues"></a>連線與不正確的資訊問題

### <a name="issue"></a>問題
- 登入 Azure 行動互動 UI 的問題。
- Azure 行動互動 API 連線錯誤。
- 上傳透過裝置 API 的應用程式資訊標記的問題。
- 從 Azure 行動互動下載記錄檔] 或 [匯出的資料的問題。
- 不正確的資訊，顯示在 Azure 行動互動 ui。
- Azure 行動互動記錄中所顯示的資訊不正確。

### <a name="causes"></a>原因
* 確認您的使用者帳戶有足夠的權限，才能執行工作。
* 確認問題不是一部電腦或您的區域網路隔離。
* 確認 Azure 行動互動服務沒有提報中斷。
* 確認您的應用程式資訊標籤檔案追蹤所有這些規則︰
    - 使用僅 UTF8 字元組 （ANSI 字元集不支援時）。
    - 使用逗號 「，」 作為分隔符號字元 (您可以開啟服務要求來要求變更.csv 分隔符號字元逗號 「，」 到另一個字元，例如以分號 」; 」)。
    - 使用的所有小寫，則為 True 的值，"true"和"false"。
    - 使用小於 35 MB 的最大的檔案大小的檔案。
 
