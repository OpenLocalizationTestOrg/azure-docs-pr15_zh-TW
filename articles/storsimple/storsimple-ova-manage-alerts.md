<properties 
   pageTitle="檢視及管理 StorSimple 虛擬陣列通知 |Microsoft Azure"
   description="說明 StorSimple 虛擬陣列通知條件及的重要性，以及如何使用 StorSimple 管理員服務來管理提醒。"
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="06/07/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-view-and-manage-alerts-for-the-storsimple-virtual-array"></a>使用 StorSimple 管理員服務來檢視及管理通知 StorSimple 虛擬陣列

## <a name="overview"></a>概觀

StorSimple 管理員服務中的 [**通知**] 索引標籤可讓您檢閱及清除即時為基礎的 StorSimple 虛擬陣列相關通知。 此索引標籤中，您可以集中監視您 StorSimple 虛擬陣列 （也稱為 StorSimple 內部部署的虛擬裝置） 和整體的 Microsoft Azure StorSimple 解決方案健康狀況的問題。

本教學課程說明如何設定提醒通知提醒的常見條件、 提醒的重要性層級，以及如何檢視及追蹤通知。 此外，其包含提醒的快速參考資料表，讓您快速找出特定的提醒，以及適當的回應。

![通知頁面](./media/storsimple-ova-manage-alerts/alerts1.png)

## <a name="configure-alert-settings"></a>設定提醒設定

您可以選擇是否要使用的每一個 StorSimple 虛擬裝置通知條件的電子郵件通知。 此外，您也可以在**其他電子郵件收件者**] 方塊中，並以分號分隔輸入其電子郵件地址，以識別其他通知收件者。

>[AZURE.NOTE] 您可以輸入每一個虛擬裝置 20 電子郵件地址的最大值。

啟用虛擬裝置的相關的電子郵件通知之後，通知清單中的成員會收到電子郵件訊息每次發生時的重要警示。 會從傳送郵件*storsimple-alerts-noreply@mail.windowsazure.com*，並會描述通知的條件。 收件者可以按一下 [**取消訂閱**，若要從電子郵件通知清單中移除本身。

#### <a name="to-enable-email-notification-of-alerts-for-a-virtual-device"></a>若要啟用的虛擬裝置的相關提醒的電子郵件通知

1. 移至 [**裝置** > 的虛擬裝置的**設定**。 移至 [**設定通知**] 區段。

    ![通知設定](./media/storsimple-ova-manage-alerts/alerts2.png)

2. 在 [**通知設定**] 設定︰

    1. 在 [**傳送電子郵件通知**] 欄位中，選取 [**是**]。

    2. 如果您想要讓服務系統管理員，所有的共同管理員會收到通知的通知，請在 [**電子郵件服務的系統管理員**] 欄位中，選取**[是]** 。

    3. 在 [**其他電子郵件收件者**] 欄位中，輸入電子郵件地址的所有其他收件者應收到通知的通知。 在格式中輸入名稱*someone@somewhere.com*。 以分號區隔電子郵件地址。 您可以設定的每個虛擬裝置 20 電子郵件地址。 

        ![提醒通知設定](./media/storsimple-ova-manage-alerts/alerts3.png)

3. 在頁面底部，按一下 [**儲存**] 儲存您的設定。

4. 若要傳送測試電子郵件通知，請按一下 [**傳送測試電子郵件**] 旁的箭號圖示。 它會轉送測試通知 StorSimple 管理員服務會顯示的狀態訊息。 

5. 當出現下列訊息時，請按一下**[確定]**。 

    ![通知測試傳送通知電子郵件](./media/storsimple-ova-manage-alerts/alerts4.png)

    >[AZURE.NOTE] 如果無法傳送測試通知訊息，StorSimple 管理員服務會顯示適當的訊息。 按一下**[確定]**，稍候幾分鐘，然後再試一次傳送您的測試通知訊息。 

    測試通知訊息會類似下列。

    ![通知測試電子郵件範例](./media/storsimple-ova-manage-alerts/alerts5.png)

## <a name="common-alert-conditions"></a>常見的通知條件

您 StorSimple 虛擬陣列中各種不同的條件的回應產生提醒。 以下是最常見的通知的條件類型︰

- **連線問題**– 困難傳送資料時，會發生下列通知。 通訊問題發生時的資料傳輸 Azure 儲存體帳戶或因為缺少的虛擬裝置與 StorSimple 管理員服務之間的連線。 通訊問題有難修正，因為有這麼多點失敗。 您應該一律先確認網路連線和存取網際網路是可用再繼續執行更多進階的疑難排解。 連接埠與防火牆設定的相關資訊，請移至[StorSimple 虛擬陣列系統需求](storsimple-ova-system-requirements.md)。 疑難排解說明，請移至 [[測試連線 cmdlet 的疑難排解](storsimple-troubleshoot-deployment.md)。

- **效能問題**– 這些通知會造成您的系統不最佳方式，例如時負荷中執行時。

此外，您可能會看到通知相關的安全性、 更新或工作失敗。

## <a name="alert-severity-levels"></a>通知的重要性層級

通知有不同的重要性層級，根據含有提醒的情況下會影響，需要使用提醒的回應。 重要性層級是︰

- **要徑**– 此通知是系統的會影響您的成功效能的條件來回應。 若要確保服務不會中斷 StorSimple 需要動作。

- **警告**– 這種情況可能會變成要徑無法解決。 您應該調查這種情況，並進行任何所需清除問題的動作。

- **資訊**– 此通知包含會有幫助追蹤和管理您的系統中的資訊。

## <a name="view-and-track-alerts"></a>檢視及追蹤通知

StorSimple 管理員服務儀表板提供讓您快速檢視在您虛擬在裝置上，排列重要性層級的通知的電話號碼。

![通知儀表板](./media/storsimple-ova-manage-alerts/alerts6.png)

按一下重要性層級會開啟 [**通知**] 索引標籤。 結果包含相符的重要性層級的警示。

![通知提醒類型範圍的報表](./media/storsimple-ova-manage-alerts/alerts7.png)

按一下清單中的提醒您提供的其他詳細資料警示，包括報告的提醒，最後一次的裝置，以及建議的動作，以解決提醒通知的次數。

如果您需要將資訊傳送給 Microsoft 技術支援，您可以複製通知的詳細資料至文字檔案。 您已追蹤建議，並解決通知條件內部之後，請選取提醒**通知**] 索引標籤中，按一下 [**清除**清除的警示。 若要清除的多個提醒，選取每個通知，按一下 [**通知**] 欄中，以外的任何資料行，然後在您選取 [清除所有通知後，按一下 [**清除**。 請注意一些通知會自動清除當問題已解決，或系統的新資訊來更新通知。

當您按一下 [**清除**] 時，您會有機會，在提供的註解提醒及您原本以解決此問題的步驟。 

![提醒的註解](./media/storsimple-ova-manage-alerts/clear-alert.png)

按一下 [檢查] 圖示 ![檢查圖示](./media/storsimple-ova-manage-alerts/check-icon.png) 若要儲存您的註解。

系統會清除某些事件，如果另一個事件觸發新的資訊。 在此情況下，您會看到下列訊息。

![清除提醒的郵件](./media/storsimple-ova-manage-alerts/alerts8.png)

## <a name="sort-and-review-alerts"></a>排序及檢閱通知

[**通知**] 索引標籤可以顯示最大 250 通知]。 如果您有超過該數字的提醒，並非所有通知會都顯示在預設檢視。 您可以結合來自訂的通知會顯示下列欄位︰

- **狀態**-您可以顯示**作用中**或**已清除**的通知。 作用中的通知將會仍觸發您系統上時清除的提醒已手動清除 [以系統管理員，或以程式設計方式關閉，因為系統會以新資訊更新通知的條件。

- **嚴重性**– 您可以顯示所有的重要性層級 （要徑、 警告、 資訊） 或只是特定的重要性，例如僅要徑通知的通知。

- **來源**– 您可以顯示所有來源的通知或限制來自該服務或一個或所有的虛擬裝置的通知。

- **時間範圍**-藉由指定**從**] 和 [**到**] 日期和時間戳記，您可以查看通知您感興趣的時段內。

## <a name="alerts-quick-reference"></a>通知的快速參考

下表列出一些您可能會遇到，以及其他資訊和建議的位置使用 Microsoft Azure StorSimple 通知。 StorSimple 虛擬裝置通知分為下列類別︰

- [雲端連線通知](#cloud-connectivity-alerts)

- [設定通知](#configuration-alerts)

- [工作失敗通知](#job-failure-alerts)

- [效能通知](#performance-alerts)

- [安全性警告](#security-alerts)

- [更新通知](#update-alerts)

### <a name="cloud-connectivity-alerts"></a>雲端連線通知

|提醒的文字|事件|詳細資訊 / 建議的動作|
|:---|:---|:---|
|裝置*<device name>*未連線至雲端。|命名的裝置無法連線至雲端。 |無法連線至雲端。 這可能是因為下列其中一項︰<ul><li>可能會在您的裝置上的網路設定問題。</li><li>可能會有問題的儲存空間帳戶認證。</li></ul>疑難排解連線問題的詳細資訊，請移至[本機網頁 UI](storsimple-ova-web-ui-admin.md)的裝置。|


### <a name="configuration-alerts"></a>設定通知

|提醒的文字|事件|詳細資訊 / 建議的動作|
|:---|:---|:---|
|內部部署的虛擬裝置設定不受支援。|效能變慢。|目前的設定可能會導致效能降低。 請確定您的伺服器符合最低組態需求。 如需詳細資訊，請前往[StorSimple 虛擬陣列需求](storsimple-ova-system-requirements.md)。 
|您所提供的磁碟空間不足 <*裝置名稱*>。|磁碟空間警告。|您正在使用低能夠的磁碟空間。 若要釋放空間，請考慮將負載移至另一個磁碟區或共用，或刪除資料。

### <a name="job-failure-alerts"></a>工作失敗通知

|提醒的文字|事件|詳細資訊 / 建議的動作|
|:---|:---|:---|
|無法完成 <*裝置名稱*> 的備份。|備份工作失敗。|無法建立備份。 請考慮下列其中一項︰<ul><li>連線問題可能防止備份已順利完成作業。 請確定沒有連線問題。 疑難排解連線問題的詳細資訊，請移至[本機網頁 UI](storsimple-ova-web-ui-admin.md)虛擬裝置。</li><li>您已達到可用儲存空間限制。 若要釋放空間，請考慮刪除不再需要的備份。</li></ul> 解決問題，請清除提醒，再試。|
|還原 <*裝置名稱*> 的無法完成。|還原工作失敗。|無法從備份還原。 請考慮下列其中一項︰<ul><li>備份清單可能不是有效的。 重新整理清單，確認它仍然有效。</li><li>連線問題可能防止還原作業成功完成。 請確定沒有連線問題。</li><li>您已達到可用儲存空間限制。 若要釋放空間，請考慮刪除不再需要的備份。</li></ul>解決問題，請清除提醒，再試一次還原作業。|
|無法完成 <*裝置名稱*> 的複本。|複製工作失敗。|無法建立複本。 請考慮下列其中一項︰<ul><li>備份清單可能不是有效的。 重新整理清單，確認它仍然有效。</li><li>連線問題可能防止複製操作無法完成。 請確定沒有連線問題。</li><li>您已達到可用儲存空間限制。 若要釋放空間，請考慮刪除不再需要的備份。</li></ul>解決問題，請清除提醒，再試。|

### <a name="performance-alerts"></a>效能通知

|提醒的文字|事件|詳細資訊 / 建議的動作|
|:---|:---|:---|
|發生無法預期中的資料傳輸的延遲。|資料變得很慢傳輸。|當您在超出延展性的目標儲存服務，就會發生節流錯誤。 儲存服務會以確保沒有單一用戶端或租用戶，可以使用犧牲其他人的服務。 疑難排解 Azure 儲存體帳戶的詳細資訊，請移至[監視器，診斷，及疑難排解 Microsoft Azure 儲存體](storage-monitoring-diagnosing-troubleshooting.md)。
|您正在使用低 <*裝置名稱*> 保留本機磁碟空間。|回應時間。|10%的 <*裝置名稱*> 能夠大小總計的保留本機裝置上，而且您目前正在使用低保留的空間。 <*裝置名稱*> 工作量產生更高變換工資率，或您可能會有最近移轉大量的資料。 這可能會導致效能降低。 請考慮採取下列動作，以解決這個問題的其中一個︰<ul><li>增加此裝置的雲端頻寬。</li><li>減少或移動到另一個磁碟區或共用的工作量。</li></ul>

### <a name="security-alerts"></a>安全性警告

|提醒的文字|事件|詳細資訊 / 建議的動作|
|:---|:---|:---|
|<*裝置名稱*> 密碼，便會到期 <*數字*> 天數。|密碼警告。| 在您的密碼到期 < 數字 < 天。 請考慮變更您的密碼。 如需詳細資訊，請前往[變更 StorSimple 虛擬陣列裝置系統管理員的密碼](storsimple-ova-change-device-admin-password.md)。

### <a name="update-alerts"></a>更新通知

|提醒的文字|事件|詳細資訊 / 建議的動作|
|:---|:---|:---|
|新的更新可供您的裝置。|更新 StorSimple 虛擬陣列可供使用。|您可以**進行的維修作業**] 頁面中安裝新更新。|
|無法掃描 <*裝置名稱*> 上的新更新。|更新失敗。 |安裝新更新時，發生錯誤。 您可以手動安裝更新。 如果問題持續發生，請連絡[Microsoft 支援服務](storsimple-contact-microsoft-support.md)。|


## <a name="next-steps"></a>後續步驟

- [深入了解 StorSimple 虛擬陣列](storsimple-ova-overview.md)。


