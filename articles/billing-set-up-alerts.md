<properties
    pageTitle="設定帳單通知您的 Microsoft Azure 訂閱 |Microsoft Azure"
    description="說明如何您可以設定提醒 Azure 帳單上，您可以避免帳單意外。"
    services=""
    documentationCenter=""
    authors="vikdesai"
    manager="mbaldwin"
    editor=""
    tags="billing"
    />

<tags
    ms.service="billing"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/18/2016"
    ms.author="vikdesai"/>

# <a name="set-up-billing-alerts-for-your-microsoft-azure-subscriptions"></a>設定您的 Microsoft Azure 訂閱的帳單的提醒

您擔心您開銷多少 Azure 訂閱的每個月嗎？ 如果您是 Azure 訂閱的帳戶管理員，您可以使用 Azure 帳單通知服務來建立自訂協助您的帳單通知監控和管理帳單的活動，以便您 Azure 帳戶。

這項服務是預覽服務，就必須執行的第一個項目為其設定為登。 請造訪 Azure 帳戶管理入口網站，才能執行這項功能中的[[預覽功能] 頁面](https://account.windowsazure.com/PreviewFeatures)。

## <a name="set-the-alert-threshold-and-email-recipients"></a>設定通知臨界值和電子郵件收件者

您收到的帳單的服務已開啟的電子郵件確認您的訂閱之後，請造訪帳戶入口網站中的[[訂閱] 頁面](https://account.windowsazure.com/Subscriptions)。 按一下您想要監視的訂閱，然後按一下 [**通知]**。

![][Image1]

接下來，按一下 [**新增提醒**以建立您的第一個-您可以設定的每個訂閱，不同的閥值與最每個警示的兩個電子郵件收件者的五個帳單通知總計。

![][Image2]

當您新增提醒時，您唯一的名稱、 選擇耗費的閥值，然後選擇將會傳送通知電子郵件地址。 設定時閥值，您可以選擇**提醒的**清單中的**總帳單**或**貨幣信用卡**。 帳單寄送的總計，訂閱花費超過臨界值時，會傳送通知。 貨幣正在等待信用，貨幣貸項總計放到限制以下時，會傳送通知。 貨幣貸項總計通常會套用至免費試用版和 MSDN 帳戶相關聯的訂閱。

![][Image3]

Azure 支援任何電子郵件地址，但無法驗證電子郵件地址運作正常，因此，再次檢查拼字錯誤。

## <a name="check-on-your-alerts"></a>查看您的通知

設定通知後，帳戶中心會列出它們，並顯示多少其他您可以設定。 每個警示，您會看到日期和時間寄，無論它通知總帳單或貨幣信用卡，然後設定限制。 日期及時間格式 24 小時制國際標準時間 (UTC)，而日期是 yyyy-mm-dd 格式。 按一下警示加以編輯，清單中的加號或按一下資源回收筒可以將其刪除。

[Image1]: ./media/azure-billing-set-up-alerts/billingalert1.png
[Image2]: ./media/azure-billing-set-up-alerts/billingalert2.png
[Image3]: ./media/azure-billing-set-up-alerts/billingalerts3.png
