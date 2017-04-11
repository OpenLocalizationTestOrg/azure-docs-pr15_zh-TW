<properties 
   pageTitle="部署 StorSimple 管理員服務 |Microsoft Azure"
   description="說明如何建立及刪除 StorSimple 管理員服務中 Azure 傳統入口網站，並說明如何管理服務登錄機碼。"
   services="storsimple"
   documentationCenter=""
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/24/2016"
   ms.author="v-sharos" />

# <a name="deploy-the-storsimple-manager-service"></a>部署 StorSimple 管理員服務

## <a name="overview"></a>概觀

StorSimple 管理員服務 Microsoft Azure 中執行，並且連線到多個 StorSimple 裝置。 建立服務之後，您可以從 Microsoft Azure 傳統入口網站瀏覽器中執行管理裝置使用它。 這個選項可讓您監視從單一且集中位置，藉此最小化系統管理負擔 StorSimple 管理員服務連線的所有裝置。

StorSimple 管理員登陸頁面會列出您可以使用來管理您 StorSimple 存放裝置的所有 StorSimple 管理員服務。 為每個 StorSimple 管理員服務]，[StorSimple 管理員] 頁面上呈現下列資訊︰

- **名稱**– 建立時，您 StorSimple 管理員服務已指定的名稱。 建立服務之後，就無法變更服務名稱。

- **狀態**-可以是**作用中**、**建立**、 或**線上**服務的狀態。

- **位置**– 部署 StorSimple 裝置的地理位置。

- **訂閱**– 您的服務與相關聯的計費訂閱。

可透過 [StorSimple 管理員] 頁面來執行一般工作包括︰

- 建立服務
- 刪除服務
- 取得服務登錄機碼
- 重新產生服務登錄機碼

本教學課程說明如何執行每項工作。

## <a name="create-a-service"></a>建立服務

使用 [**快速建立**選項來建立 StorSimple 管理員服務，如果您想要部署 StorSimple 裝置。 若要建立服務，您必須具備︰

- Enterprise 合約訂閱
- 使用中的 Microsoft Azure 儲存體帳戶
- 用於存取管理帳單資訊

您也可以選擇產生預設儲存帳戶，當您建立的服務。

單一服務可以管理多個裝置。 不過，在裝置無法橫跨多個服務。 大型企業可以有多個服務執行個體來使用不同的訂閱，組織或甚至部署位置。 請注意，您需要的個別執行個體 StorSimple 管理員服務，來管理 StorSimple 8000 數列裝置和 StorSimple 虛擬陣列。

執行下列步驟來建立的服務。

[AZURE.INCLUDE [storsimple-create-new-service](../../includes/storsimple-create-new-service.md)]

## <a name="delete-a-service"></a>刪除服務

刪除服務之前，請確定沒有連線的裝置使用它。 在使用服務時，停用連線的裝置。 停用] 作業會伺服器裝置和服務，之間的連線，但保留在雲端的裝置資料。 

[AZURE.IMPORTANT] 刪除服務之後，就無法回復作業。 使用服務的任何裝置將必須工廠重設之前搭配其他服務。 在此案例中，將會遺失裝置，以及設定] 區域的資料。

執行下列步驟，以刪除服務。

### <a name="to-delete-a-service"></a>若要刪除服務

1. 在 [ **StorSimple 管理員服務**] 頁面中，選取您想要刪除的服務。

1. 按一下 [在頁面底部的 [**刪除**]。

1. 在確認通知，請按一下**[是]** 。 可能需要幾分鐘的時間刪除服務。

## <a name="get-the-service-registration-key"></a>取得服務登錄機碼

您已成功建立服務之後，您必須登錄 StorSimple 裝置的服務。 若要註冊您的第一個 StorSimple 裝置，您必須服務登錄機碼。 若要向其他裝置註冊現有 StorSimple 服務，您需要的登錄機碼和服務資料加密金鑰 （產生的第一個裝置上註冊期間）。 如需有關服務資料加密金鑰的詳細資訊，請參閱[StorSimple 安全性](storsimple-security.md)。 您可以存取**登錄機碼**，在 [**服務**] 頁面上，以取得登錄機碼。

執行下列步驟，以取得服務登錄機碼。

[AZURE.INCLUDE [storsimple-get-service-registration-key](../../includes/storsimple-get-service-registration-key.md)]

將服務登錄機碼保留在安全的位置。 您會需要此鍵，以及服務的資料加密金鑰，以向其他裝置註冊此服務。 取得服務登錄機碼之後，您必須設定您的裝置透過 Windows PowerShell StorSimple 介面。

如需如何使用此登錄機碼的詳細資訊，請參閱[步驟 3︰ 設定，並透過 Windows PowerShell 的裝置註冊 StorSimple](storsimple-deployment-walkthrough.md#step-2-configure-and-register-the-device-through-windows-powershell-for-storsimple)。

## <a name="regenerate-the-service-registration-key"></a>重新產生服務登錄機碼

您必須重新產生服務登錄機碼，如果您所需執行重要的旋轉或清單的服務系統管理員已變更。 當您重新產生金鑰時，新的索引鍵只適用於註冊後續的裝置。 已註冊的裝置是受此程序。

執行下列步驟，以重新產生服務登錄機碼。

### <a name="to-regenerate-the-service-registration-key"></a>若要重新產生服務登錄機碼

1. 在 [ **StorSimple 管理員服務**] 頁面上，按一下 [**登錄機碼**。

1. 在 [**服務登錄機碼**] 對話方塊中，按一下 [**重新產生**]。

1. 您會看到一則確認訊息。 按一下**[確定]**以繼續重新產生]。

1. 新的服務登錄機碼會出現。

1. 複製此鍵，並將其儲存任何新的裝置註冊此服務。

1. 按一下 [檢查] 圖示 ![檢查圖示](./media/storsimple-manage-service/HCS_CheckIcon.png) 若要關閉此對話方塊。


## <a name="next-steps"></a>後續步驟

- 進一步瞭解[StorSimple 部署程序](storsimple-deployment-walkthrough.md)。

- 深入瞭解[管理您 StorSimple 儲存的帳戶](storsimple-manage-storage-accounts.md)。

- 進一步瞭解如何[使用 StorSimple 管理員服務來管理您的 StorSimple 裝置](storsimple-manager-service-administration.md)。

 
