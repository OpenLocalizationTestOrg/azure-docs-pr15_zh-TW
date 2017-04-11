<properties
    pageTitle="無法登入 Azure 訂閱 |Microsoft Azure"
    description="說明如何一些常見的 Azure 訂閱登入問題進行疑難排解。"
    services=""
    documentationCenter=""
    authors="genlin"
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
    ms.date="10/25/2016"
    ms.author="genli"/>

# <a name="i-cant-sign-in-to-manage-my-azure-subscription"></a>我無法登入管理 Azure 訂閱

本文會引導您完成一些最常見的方法來解決登入問題。

## <a name="page-hangs-in-the-loading-status"></a>在 [載入狀態中的頁面當掉

如果您的網際網路瀏覽器頁面當機，請按照下列步驟，直到您可以前往[Azure 入口網站](https://portal.azure.com)。

-   重新整理頁面。
-   使用不同的網際網路瀏覽器。
-   如果您使用的 Microsoft Internet Explorer，瀏覽至 Azure 入口網站使用 InPrivate 瀏覽模式。 

    答︰  按一下 [**工具]** ![工具] 按鈕](./media/billing-cannot-login-subscription/Toolsbutton.png) > **安全** > **InPrivate 瀏覽**。

    B。  [Azure 入口網站](https://portal.azure.com)中，瀏覽]，然後登入入口網站。

## <a name="error-message-no-subscriptions-found"></a>錯誤訊息 「 找不到訂閱 」

如果您的帳戶沒有足夠的權限，您可能會看到 「**找不到訂閱**」 錯誤訊息。 只有帳戶管理員能連線至[帳戶中心](https://account.windowsazure.com/)，不是 [服務系統管理員 (SA)] 或 [共同管理員 (CA)。

**情況 1︰ 在[Azure 入口網站](https://portal.azure.com)中收到錯誤訊息**

若要解決此問題，請[新增共同管理員或擁有者角色](billing-add-change-azure-subscription-administrator.md)的帳戶。

**案例 2︰ 在[Azure 帳戶中心](https://account.windowsazure.com/Subscriptions)收到錯誤訊息**

請檢查您所使用的帳戶是否帳戶管理員。 若要確認帳戶管理員是誰，請遵循下列步驟︰

1.  [Azure 入口網站](https://portal.azure.com)登入。
2.  在中樞] 功能表中，選取 [**訂閱**]。
3.  選取您想要檢查的訂閱，然後選取 [**設定**。
4.  選取 [**內容**]。 訂閱的帳戶管理員會顯示在 [**帳戶管理員**] 方塊。

## <a name="you-are-automatically-signed-in-as-a-different-user"></a>您會自動登入，以不同的使用者

如果您在網際網路瀏覽器中使用多個使用者帳戶，就可能發生此問題。

若要解決此問題，請嘗試下列方法之一︰

-   清除快取，然後刪除網際網路 cookie]。 在 Internet Explorer 中，按一下 [**工具**![工具] 按鈕](./media/billing-cannot-login-subscription/Toolsbutton.png) > **網際網路選項** > **刪除**。 請確定已核取方塊暫存檔案、 cookie、 密碼和瀏覽歷程記錄，然後按一下 [刪除項目。

-   重設 Internet Explorer 設定，以還原您所做的任何個人設定。 按一下 [**工具]** ![工具] 按鈕](./media/billing-cannot-login-subscription/Toolsbutton.png)> **網際網路選項** > **進階**> 選取 [**刪除個人設定**] 方塊 >**重設**。

-   瀏覽至 [InPrivate 瀏覽模式的 Azure 入口網站。 按一下 [**工具]** ![工具] 按鈕](./media/billing-cannot-login-subscription/Toolsbutton.png) > **安全** > **InPrivate 瀏覽**。

## <a name="need-help-contact-support"></a>需要協助嗎？ 連絡支援人員。 

如果您仍然需要說明，請[連絡支援部門](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以取得您快速解決的問題。 