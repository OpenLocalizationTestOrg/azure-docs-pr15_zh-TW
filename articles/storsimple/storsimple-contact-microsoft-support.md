<properties 
   pageTitle="連絡 Microsoft 支援服務 |Microsoft Azure"
   description="瞭解如何建立支援要求並開始 StorSimple 裝置上的支援工作階段。"
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/21/2016"
   ms.author="alkohli" />

# <a name="contact-microsoft-support"></a>連絡 Microsoft 支援服務

如果您的 Microsoft Azure StorSimple 解決方案發生任何問題，您可以建立服務要求的技術支援。 使用您的支援工程師線上工作階段，您可能也需要 StorSimple 裝置上啟動支援工作階段。 本文會引導您完成︰

- 如何建立支援要求。
- 如何在 Windows PowerShell 介面 StorSimple 裝置的啟動支援工作階段。

建立支援要求之前，請檢閱[StorSimple 8000 數列支援 Sla 和資訊](https://msdn.microsoft.com/library/mt433077.aspx)。

## <a name="create-a-support-request"></a>建立支援要求

執行下列步驟，以建立支援要求︰

#### <a name="to-create-a-support-request"></a>若要建立的支援要求

1. 在[Azure 傳統入口網站](https://manage.windowsazure.com/)，在右上角，按一下您的帳戶名稱，然後按一下**連絡 Microsoft 支援人員**。

    ![透過 ManagementPortal 連絡人 MS 支援](./media/storsimple-contact-microsoft-support/Ibiza1.png)

2. 您會被重新導向至新的 Azure 入口網站 (portal.azure.com)。 按一下 [**新增支援要求**的磚。

    ![連絡人 MS 支援透過新的入口網站](./media/storsimple-contact-microsoft-support/Ibiza2.png)

    [**新增支援要求**] 窗格會出現在螢幕右側。 

    ![新支援要求] 窗格](./media/storsimple-contact-microsoft-support/Ibiza3a.png)

3. 在 [**基本功能**] 對話方塊中完成下列步驟︰                                
    1. 從 [**問題類型**] 下拉式清單中，選取 [**技術**]。
    2. 從下拉式清單中選取**訂閱**。
    3. 從 [**服務**] 下拉式清單中，選取 [ **StorSimple**]。 
    4. 從下拉式清單中選取一個**支援計劃**。 您需要付費的支援計劃，以技術支援。

4. 按一下 [**下一步**]。 [**問題**] 對話方塊隨即出現。

    ![新支援要求] 窗格](./media/storsimple-contact-microsoft-support/Ibiza5a.png) 

5. 在 [**問題**] 對話方塊中完成下列步驟︰

    1.  從下拉式清單中選取**重要性**層級。
    2.  從下拉式清單中選取**問題類型**。
    3.  從下拉式清單中選取**類別**。 
    4.  在 [**詳細資料**] 方塊中，簡要描述您的問題。
    5.  在 [**時間範圍**] 方塊中，表示日期、 時間及對應到您的問題最新項目的時區。
    6.  **上傳檔案**，請按一下 [資料夾] 圖示，以瀏覽至您的支援套件。
    7.  選取 [**共用的診斷資訊**] 核取方塊。

6. 按一下 [**下一步**]。 [**連絡人資訊**] 對話方塊隨即出現。

    ![新支援要求] 窗格](./media/storsimple-contact-microsoft-support/Ibiza6a.png) 

7. 輸入您的連絡人資訊，然後選取連絡方式 （手機或電子郵件）。 

8. 選取 [**儲存連絡人變更未來支援要求的**核取方塊。

9. 按一下 [**建立**]。

您已經提交您的要求之後，支援工程師會與您連絡越快越，繼續使用您的要求。

## <a name="start-a-support-session-in-windows-powershell-for-storsimple"></a>在 Windows PowerShell 中啟動 StorSimple 支援工作階段

若要使用 StorSimple 裝置可能會遇到任何問題進行疑難排解，您需要加入與 Microsoft 技術支援小組。 Microsoft 支援服務可能需要登入您的裝置使用支援的工作階段。 

執行下列步驟，以啟動支援工作階段︰

#### <a name="to-start-a-support-session"></a>若要開始在支援工作階段

1. 直接使用循序主控台或從遠端電腦 telnet 工作階段，請存取裝置。 若要這麼做，請遵循[使用 PuTTY 連線至裝置循序主控台](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console)中的步驟。

2. 在開啟的工作階段，請按**Enter**鍵以取得命令提示字元。

3. 在循序主控台] 功能表中選取選項 1，**登入的完整功能**。

4. 出現提示時，輸入下列密碼︰ 

    `Password1`

5. 出現提示時，輸入以下命令︰

    `Enable-HcsSupportAccess`

6. 加密的字串會提供給您。 複製這個字串到 「 記事本 」 等文字編輯器。

7. 儲存此字串，並以電子郵件訊息傳送給 Microsoft 支援人員。 

> [AZURE.IMPORTANT] 您可以藉由執行停用支援存取`Disable-HcsSupportAccess`。 StorSimple 裝置也會停用支援存取 8 小時後啟動工作階段。 最好變更 StorSimple 裝置認證後起始支援工作階段。
