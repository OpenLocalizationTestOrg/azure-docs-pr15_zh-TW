<properties 
   pageTitle="變更 StorSimple 虛擬裝置管理員密碼 |Microsoft Azure"
   description="說明如何使用 Azure 傳統入口網站或 StorSimple 虛擬陣列網頁 UI 變更裝置系統管理員的密碼。"
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
   ms.workload="TBD"
   ms.date="06/17/2016"
   ms.author="alkohli" />

# <a name="change-the-storsimple-virtual-array-device-administrator-password"></a>變更 StorSimple 虛擬陣列裝置系統管理員的密碼

## <a name="overview"></a>概觀

當您使用 Windows PowerShell 介面存取 StorSimple 虛擬裝置時，會要求您輸入 [裝置管理員密碼。 StorSimple 裝置是第一次佈建後，開始使用時的預設密碼是*密碼 1*。 如需資料的安全性，預設密碼到期第一次登入，您必須變更密碼。

若要變更次生產環境中部署裝置之後的裝置系統管理員的密碼，您也可以使用本機網頁 UI 或 Azure 傳統入口網站。 本文說明這些程序。

## <a name="use-the-azure-classic-portal-to-change-the-password"></a>若要變更密碼使用 Azure 傳統入口網站

執行下列步驟，以變更裝置系統管理員的密碼透過 Azure 傳統入口網站。

#### <a name="to-change-the-device-administrator-password-via-the-azure-classic-portal"></a>若要變更裝置系統管理員的密碼透過 Azure 傳統入口網站

1. 在入口網站中，按一下 [**裝置** > **設定**您的裝置。

2. 捲動至 [**裝置管理員密碼**] 區段。 提供 8 包含 15 個字元的系統管理員的密碼。 密碼必須大寫、 小寫字母、 數字及特殊字元的組合。

3. 確認密碼。

4. 按一下 [在頁面底部的 [**儲存**]。

現在應該更新裝置系統管理員的密碼。 您可以使用 [修改的密碼存取本機裝置。

## <a name="use-the-storsimple-virtual-array-web-ui-to-change-the-password"></a>使用 StorSimple 虛擬陣列 web 變更密碼的使用者介面

執行下列步驟，以變更在本機的 web UI 裝置系統管理員的密碼。

#### <a name="to-change-the-device-administrator-password-via-the-local-web-ui"></a>若要變更裝置系統管理員的密碼透過本機網站使用者介面

1. 在本機的 web UI，按一下 [**進行的維修作業** > 您裝置的 [**變更密碼**。

    ![變更密碼 1](./media/storsimple-ova-change-device-admin-password/image40.png)

2. 輸入**目前的密碼**。

3. 輸入**新密碼**。 密碼必須至少 8 個字元。 它必須包含 3 / 4 / 下列︰ 大寫、 小寫字母、 數字及特殊字元。

    請注意，您的密碼無法最後 24 個密碼相同。

3. 重新輸入密碼以進行確認。

    ![變更 password2](./media/storsimple-ova-change-device-admin-password/image41.png)

4. 在頁面底部，按一下 [**套用**]。 就會套用的新密碼。 如果變更密碼不成功，您會看到下列錯誤。

    ![密碼錯誤](./media/storsimple-ova-change-device-admin-password/image42.png)

    成功更新密碼後，您會收到通知。 修改的密碼然後可用來存取本機裝置。

## <a name="next-steps"></a>後續步驟

深入瞭解[管理 StorSimple 虛擬陣列](storsimple-ova-web-ui-admin.md)。
