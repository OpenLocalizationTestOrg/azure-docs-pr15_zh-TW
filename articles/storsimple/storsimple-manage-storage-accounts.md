<properties 
   pageTitle="管理 StorSimple 儲存帳戶 |Microsoft Azure"
   description="說明如何使用 StorSimple 管理員設定] 頁面來新增、 編輯、 刪除或旋轉儲存帳戶的安全性索引鍵。"
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="04/29/2016"
   ms.author="v-sharos" />

# <a name="use-the-storsimple-manager-service-to-manage-your-storage-account"></a>使用 StorSimple 管理員服務來管理您儲存的帳戶

## <a name="overview"></a>概觀

[**設定**] 頁面會顯示您可以建立 StorSimple 管理員服務中的所有全域服務參數。 下列參數可套用至連線到該服務中的所有裝置，而且包括︰

- 儲存帳戶 
- 頻寬範本 
- Access 控制項記錄 

本教學課程會說明如何使用 [**設定**] 頁面來新增、 編輯或刪除儲存的帳戶，或旋轉儲存帳戶的安全性索引鍵。

 ![設定頁面](./media/storsimple-manage-storage-accounts/HCS_ConfigureService.png)  

儲存帳戶包含裝置用來存取您儲存的帳戶在雲端服務提供者的認證。 Microsoft Azure 儲存體帳戶，這些是認證，例如客戶名稱] 和 [主要便捷鍵。 

在 [**設定**] 頁面上所有的儲存空間帳戶所建立的付款的訂閱會顯示表格的格式，包含下列資訊︰

- **名稱**– 指派給帳戶時所建立的唯一名稱。
- **啟用 SSL** – 是否 SSL 已啟用，而裝置至雲端通訊是透過安全通道。
- **使用由**– 區使用的儲存空間帳戶的數目。

可以在 [**設定**] 頁面執行的儲存空間帳戶相關的常見工作如下︰

- 新增儲存空間帳戶 
- 編輯儲存帳戶 
- 刪除儲存帳戶 
- 儲存帳戶的重要的旋轉角度 

## <a name="types-of-storage-accounts"></a>儲存帳戶類型

有三種類型的儲存空間帳戶可使用 StorSimple 裝置。

- **自動產生的儲存空間帳戶**-第一次建立服務時，顧名思義，會自動產生此類型的儲存空間的帳戶。 若要進一步瞭解如何建立此儲存帳戶，請參閱[步驟 1︰ 建立新的服務](storsimple-deployment-walkthrough-u1.md#step-1-create-a-new-service)在[部署您的內部部署 StorSimple 裝置](storsimple-deployment-walkthrough.md)。 
- **服務訂閱中的儲存空間帳戶**– 這些是 Azure 儲存體帳戶與服務的同一份訂閱相關聯。 若要進一步瞭解如何使用這些儲存空間，會建立帳戶，請參閱[關於 Azure 儲存體帳戶](../storage/storage-create-storage-account.md)。 
- **外部服務訂閱的儲存空間帳戶**– 這些是 Azure 儲存體帳戶不與您的服務相關聯，可能已存在之前服務的建立。

## <a name="add-a-storage-account"></a>新增儲存空間帳戶

您可以新增儲存空間帳戶提供的唯一易記的名稱，以及存取認證所連結的儲存空間帳戶 （指定的雲端服務提供者）。 您也可以選擇的啟用安全通訊端層 (SSL) 模式，來建立您的裝置與雲端之間的網路通訊安全通道。

您可以指定的雲端服務提供者建立多個帳戶。 不過，請注意，建立儲存帳戶之後，您無法變更雲端服務提供者。

時儲存儲存帳戶時，則服務會與您的雲端服務提供者通訊。 這次，將會驗證認證，您所提供的 access 材料。 只有當驗證成功，會建立儲存帳戶。 如果驗證失敗，請將顯示適當的錯誤訊息。

StorSimple 也支援 Azure 入口網站中建立的資源管理員儲存帳戶。 資源管理員儲存帳戶會不顯示在選取的下拉式清單中會顯示嘗試建立大量容器，只有在 Azure 傳統入口網站中建立儲存的帳戶時。 資源管理員儲存帳戶必須使用以新增儲存空間帳戶，如下所述的程序來新增。

> [AZURE.NOTE] 新增儲存空間帳戶的程序會根據您使用的 StorSimple 軟體版本。 請確定依照正確的程序，針對您 StorSimple 版本。


[AZURE.INCLUDE [add-a-storage-account-update1](../../includes/storsimple-configure-new-storage-account-u1.md)]

[AZURE.INCLUDE [add-a-storage-account](../../includes/storsimple-configure-new-storage-account.md)]

## <a name="edit-a-storage-account"></a>編輯儲存帳戶

您可以編輯的儲存空間帳戶所使用的大量容器。 如果您編輯目前正在使用的儲存空間帳戶，才能修改唯一的欄位是儲存帳戶的便捷鍵。 您可以提供新的儲存空間便捷鍵，並儲存更新的設定。

#### <a name="to-edit-a-storage-account"></a>若要編輯的儲存空間帳戶

1. 在服務登陸頁面上，選取您的服務、 按兩下服務名稱，然後再按一下 [**設定**。

2. 按一下 [**新增/編輯儲存帳戶**]。

3. 在 [**新增/編輯儲存帳戶**] 對話方塊中︰

  1. 在下拉式清單中的**儲存空間帳戶**，選擇您想要修改現有的帳戶。 也可能包括服務的第一次建立時自動產生的儲存空間帳戶。
  2. 如有需要，您可以修改**啟用 SSL 模式**選取範圍。
  3. 您可以選擇旋轉您儲存帳戶便捷鍵。 如需有關如何執行主要的旋轉，請參閱[的儲存空間帳戶金鑰旋轉](#key-rotation-of-storage-accounts)。
  4. 按一下核取圖示![核取圖示](./media/storsimple-manage-storage-accounts/HCS_CheckIcon.png)儲存設定。 在 [**設定**] 頁面上，就會更新設定。 按一下 [儲存新的更新設定的 [**儲存**]。

     ![編輯儲存帳戶](./media/storsimple-manage-storage-accounts/HCs_AddEditStorageAccount.png)
  
## <a name="delete-a-storage-account"></a>刪除儲存帳戶

> [AZURE.IMPORTANT] 如果無法使用大量容器，您可以刪除儲存帳戶。 如果儲存帳戶由大量容器，第一次刪除大量容器，然後刪除 [關聯的儲存帳戶。

#### <a name="to-delete-a-storage-account"></a>若要刪除的儲存空間帳戶

1. 在 StorSimple 管理員服務登陸頁面上，選取您的服務、 按兩下服務名稱，然後再按一下 [**設定**。

2. 在 [儲存帳戶的 [表格式] 清單中，將游標停留在您想要刪除的帳戶。

3. (**X**) 上的 [刪除] 圖示會出現在該儲存帳戶的極大右邊的欄。 按一下 [刪除的認證的 [ **x** ] 圖示。

4. 當畫面提示您確認，按一下 [ **]**若要繼續刪除。 表格式清單將會更新以反映所做的變更。

## <a name="key-rotation-of-storage-accounts"></a>儲存帳戶的重要的旋轉角度

基於安全性理由，索引鍵的旋轉，通常會在資料中心。 

> [AZURE.NOTE] 下列按鍵旋轉資訊和旋轉程序適用於僅適用於 Microsoft Azure 儲存體帳戶。 如果您使用的另一個雲端服務提供者，您可以管理儲存帳戶金鑰，透過該提供者的儀表板。
 
每個 Microsoft Azure 訂閱可以有一或多個相關聯的儲存空間的帳戶。 存取這些帳戶是由儲存的每個帳戶的訂閱和存取索引鍵控制。 

當您建立的儲存空間帳戶時，Microsoft Azure 會產生存取儲存帳戶時，會用於驗證的兩個 512 位元儲存便捷鍵。 有兩個儲存便捷鍵可讓您重新產生金鑰不會中斷您儲存的服務或該服務的存取權。 目前正在使用的索引鍵是*主索引*鍵和備份金鑰稱為*次要*索引鍵。 當您的 Microsoft Azure StorSimple 裝置存取您的雲端儲存服務提供者必須提供下列兩個機碼。

## <a name="what-is-key-rotation"></a>什麼是金鑰旋轉？

一般而言，應用程式使用其中一個按鍵存取您的資料。 在一段時間之後，您可以讓您切換至 [使用第二個機的應用程式。 您切換至第二個機碼應用程式之後，您可以淘汰的第一個鍵，並產生新的金鑰。 使用兩個機碼這種方式不需產生任何停機時間讓您的應用程式資料的存取權。

儲存帳戶金鑰一律會儲存在 [加密的表單中的服務。 不過，這些可以重設透過 StorSimple 管理員服務。 服務可以在同一份訂閱，包括建立第一個 StorSimple 管理員服務服務時所產生的帳戶中的儲存空間服務，以及預設儲存空間建立帳戶中取得主索引鍵和次要索引鍵的所有儲存的帳戶。 StorSimple 管理員服務服務會自動從 Azure 傳統入口網站取得這些按鍵，然後將它們儲存在 [加密的方式。

## <a name="rotation-workflow"></a>旋轉工作流程

Microsoft Azure 系統管理員可以重新產生，或直接存取 （透過 Microsoft Azure 儲存體服務） 的儲存空間帳戶變更主要或次要鍵。 StorSimple 管理員服務無法自動看到這項變更。

若要通知 StorSimple 管理員服務的變更，您會需要存取 StorSimple 管理員服務，存取儲存帳戶，然後同步處理 （根據哪一個變更） 的主要或次要鍵。 服務取得的最新的索引鍵、 加密金鑰，然後傳送至裝置的加密的金鑰。

#### <a name="to-synchronize-keys-for-storage-accounts-in-the-same-subscription-as-the-service-azure-only"></a>若要同步處理同一份訂閱服務 (僅限 Azure) 中的儲存空間帳戶的按鍵

1. 在 [**服務**] 頁面上，按一下 [**設定**] 索引標籤。

2. 按一下 [**新增/編輯儲存帳戶**]。

3. 在對話方塊中，執行下列動作︰

  1. 選取您要同步處理的鍵儲存帳戶。 顯示時，會加密儲存帳戶金鑰。
  2. 在 StorSimple 管理員服務中，您需要更新先前已經變更 Microsoft Azure 儲存體服務中的索引鍵。 如果變更主要便捷鍵 （重新產生），請按一下 [**同步處理的主索引鍵**]。 如果已變更的第二個機碼，請按一下 [**同步處理次要鍵**]。

    ![同步處理索引鍵](./media/storsimple-manage-storage-accounts/HCS_KeyRotationStorageAccountSameSubscriptionAsService.png)

#### <a name="to-synchronize-keys-for-storage-accounts-outside-of-the-service-subscription"></a>若要同步處理的外部服務訂閱的儲存空間帳戶按鍵

1. 在 [**服務**] 頁面上，按一下 [**設定**] 索引標籤。

2. 按一下 [**新增/編輯儲存帳戶**]。

3. 在對話方塊中，執行下列動作︰

  1. 選取的儲存空間帳戶與您想要更新便捷鍵。
  2. 您需要更新 StorSimple 管理員服務中的儲存空間便捷鍵。 在此情況下，您可以看到儲存便捷鍵。 **儲存帳戶便捷鍵**y] 方塊中輸入新的金鑰。 
  3. 儲存變更。 您儲存帳戶便捷鍵現在應該會更新。

## <a name="next-steps"></a>後續步驟

- 進一步瞭解[StorSimple 安全性](storsimple-security.md)。
- 瞭解更多關於[使用 StorSimple 管理員服務來管理您的 StorSimple 裝置](storsimple-manager-service-administration.md)。
