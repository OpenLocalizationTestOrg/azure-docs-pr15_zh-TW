<properties 
   pageTitle="變更您 StorSimple 密碼 |Microsoft Azure" 
   description="說明如何使用 StorSimple 管理員服務，來變更您 StorSimple 快照管理員和裝置系統管理員的密碼。" 
   services="storsimple" 
   documentationCenter="NA" 
   authors="alkohli" 
   manager="carmonm" 
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD" 
   ms.date="08/17/2016"
   ms.author="alkohli"/>

# <a name="use-the-storsimple-manager-service-to-change-your-storsimple-passwords"></a>若要變更您 StorSimple 密碼使用 StorSimple 管理員服務

## <a name="overview"></a>概觀 

Azure 傳統的入口網站**設定**] 頁面包含您可以重新設定由 StorSimple 管理員服務管理 StorSimple 裝置的所有裝置參數。 本教學課程會說明如何使用 [**設定**] 頁面，以及在變更您的裝置系統管理員或 StorSimple 快照管理員密碼。

## <a name="change-the-device-administrator-password"></a>變更裝置系統管理員的密碼

當您使用 Windows PowerShell 介面存取 StorSimple 裝置時，會要求您輸入 [裝置管理員密碼。 第一個 StorSimple 裝置註冊服務，這個介面的預設密碼是*密碼 1*。 如需資料的安全性，您需要變更這個註冊程序結尾的密碼。 您無法結束註冊程序，而不變更密碼。 如需詳細資訊，請參閱[步驟 3︰ 設定，並透過 Windows PowerShell 的裝置註冊 StorSimple](storsimple-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple)。

然後可以變更密碼的第一次設定透過 Windows PowerShell 介面註冊期間透過 Azure 傳統入口網站。 執行下列步驟，以變更裝置系統管理員的密碼。

#### <a name="to-change-the-device-administrator-password"></a>若要變更裝置系統管理員的密碼

1. 在 [傳統] 入口網站，按一下 [**裝置** > **設定**您的裝置。

2. 捲動至 [**裝置管理員密碼**] 區段。 提供 8 包含 15 個字元的系統管理員的密碼。 密碼必須是 3 或更多的大寫、 小寫字母、 數字及特殊字元的組合。

3. 確認密碼。

4. 按一下 [在頁面底部的 [**儲存**]。

現在應該更新裝置系統管理員的密碼。 存取 Windows PowerShell 介面，您可以使用這個已修改的密碼。

## <a name="change-the-storsimple-snapshot-manager-password"></a>變更 StorSimple 快照管理員密碼

StorSimple 快照管理員軟體位於您的 Windows 主機，並可讓系統管理員來管理 StorSimple 裝置在表單中的本機的備份與雲端快照集。

在設定裝置 StorSimple 快照管理員中的商務連絡人時，系統會提示提供裝置的 IP 位址和密碼以驗證您的儲存裝置。 透過 Windows PowerShell 介面第一次設定此密碼。 如需詳細資訊，請參閱[步驟 3︰ 設定，並透過 Windows PowerShell 的裝置註冊 StorSimple](storsimple-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple)。

然後可以透過 [傳統] 入口網站變更密碼的第一次在註冊時設定透過 Windows PowerShell 介面。 執行下列步驟，以變更 StorSimple 快照管理員的密碼。

#### <a name="to-change-the-storsimple-snapshot-manager-password"></a>若要變更的 StorSimple 快照管理員密碼

1. 在 [傳統] 入口網站，按一下 [**裝置** > **設定**您的裝置。

2. 向下**StorSimple 快照集管理員**] 區段中捲動。 輸入密碼 14 或 15 個字元。 請確認密碼包含 3 或更多的大寫、 小寫字母、 數字及特殊字元的組合。

3. 確認密碼。

4. 按一下 [在頁面底部的 [**儲存**]。

現在應該更新 StorSimple 快照管理員密碼。
 

## <a name="next-steps"></a>後續步驟

- 進一步瞭解[StorSimple 安全性](storsimple-security.md)。

- 進一步瞭解[修改您的裝置設定](storsimple-modify-device-config.md)。

- 瞭解更多關於[使用 StorSimple 管理員服務來管理您的 StorSimple 裝置](storsimple-manager-service-administration.md)。
