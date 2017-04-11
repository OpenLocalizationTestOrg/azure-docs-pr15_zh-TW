<properties 
   pageTitle="管理 StorSimple 儲存帳戶 |Microsoft Azure"
   description="說明如何使用 [StorSimple 管理員設定] 頁面來新增、 編輯、 刪除，或旋轉 StorSimple 虛擬陣列相關聯的儲存空間帳戶的安全性索引鍵。"
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
   ms.date="09/29/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-manage-storage-accounts-for-storsimple-virtual-array"></a>使用 StorSimple 管理員服務來管理 StorSimple 虛擬陣列的儲存空間帳戶

## <a name="overview"></a>概觀

[**設定**] 頁面顯示您可以建立 StorSimple 管理員服務中的全域服務參數。 下列參數可套用至連線到該服務中的所有裝置，而且包括︰

- 儲存帳戶 
- Access 控制項記錄 

本教學課程會說明如何使用 [**設定**] 頁面新增、 編輯或刪除儲存的帳戶，在 StorSimple 虛擬陣列。 本教學課程中的資訊僅適用於執行年 3 月 2016 GA 發行軟體 StorSimple 虛擬陣列。

 ![設定頁面](./media/storsimple-ova-manage-storage-accounts/configure_service_page.png)  

儲存帳戶包含裝置用來存取您儲存的帳戶在雲端服務提供者的認證。 Microsoft Azure 儲存體帳戶，這些是認證，例如客戶名稱] 和 [主要便捷鍵。 

在 [**設定**] 頁面上所有的儲存空間帳戶所建立的付款的訂閱會顯示表格的格式，包含下列資訊︰

- **名稱**– 指派給帳戶時所建立的唯一名稱。
- **啟用 SSL** – 是否 SSL 已啟用，而裝置至雲端通訊是透過安全通道。

可以在 [**設定**] 頁面執行的儲存空間帳戶相關的常見工作如下︰

- 新增儲存空間帳戶 
- 編輯儲存帳戶 
- 刪除儲存帳戶 


## <a name="types-of-storage-accounts"></a>儲存帳戶類型

有三種類型的儲存空間帳戶可使用 StorSimple 裝置。

- **自動產生的儲存空間帳戶**-第一次建立服務時，顧名思義，會自動產生此類型的儲存空間的帳戶。 若要進一步瞭解如何建立此儲存帳戶，請參閱[建立新的服務](storsimple-ova-manage-service.md#create-a-service)。 
- **服務訂閱中的儲存空間帳戶**– 這些是 Azure 儲存體帳戶與服務的同一份訂閱相關聯。 若要進一步瞭解如何使用這些儲存空間，會建立帳戶，請參閱[關於 Azure 儲存體帳戶](../storage/storage-create-storage-account.md)。 
- **外部服務訂閱的儲存空間帳戶**– 這些是 Azure 儲存體帳戶不與您的服務相關聯，可能已存在之前服務的建立。

每個 StorSimple 虛擬陣列會建立在相關聯的儲存帳戶 （含前置詞 hcs) 容器。 此容器具有您裝置的所有雲端資料。 不要刪除此容器存取透過 Azure 儲存體服務，這個動作會造成資料遺失。

## <a name="add-a-storage-account"></a>新增儲存空間帳戶

您可以新增儲存空間帳戶 StorSimple 管理員服務設定，提供唯一的易記名稱和所連結的儲存空間帳戶存取認證。 您也可以選擇的啟用安全通訊端層 (SSL) 模式，來建立您的裝置與雲端之間的網路通訊安全通道。

您可以指定的雲端服務提供者建立多個帳戶。 時儲存儲存帳戶時，則服務會與您的雲端服務提供者通訊。 這次，將會驗證認證，您所提供的 access 材料。 只有當驗證成功，會建立儲存帳戶。 如果驗證失敗，請將顯示適當的錯誤訊息。

StorSimple 也支援 Azure 入口網站中建立的資源管理員儲存帳戶。 資源管理員儲存帳戶會不會顯示在選取範圍，只會顯示在 Azure 傳統入口網站中建立的帳戶的儲存空間的下拉式清單中。 資源管理員儲存帳戶必須使用以新增儲存空間帳戶，如下所述的程序來新增。

下面會新增 Azure 傳統儲存帳戶的程序。

[AZURE.INCLUDE [add-a-storage-account](../../includes/storsimple-ova-configure-new-storage-account.md)]

## <a name="edit-a-storage-account"></a>編輯儲存帳戶

您可以編輯您的裝置所使用的儲存空間帳戶。 如果您編輯目前正在使用的儲存空間帳戶，才能修改欄位有便捷鍵及 SSL 模式儲存帳戶。 您可以提供新的儲存空間便捷鍵或修改**啟用 SSL 模式**選取範圍，並儲存更新的設定。

#### <a name="to-edit-a-storage-account"></a>若要編輯的儲存空間帳戶

1. 在服務登陸頁面上，選取您的服務、 按兩下服務名稱，然後再按一下 [**設定**。

2. 按一下 [**新增/編輯儲存帳戶**]。

3. 在 [**新增/編輯儲存帳戶**] 對話方塊中︰

  1. 在下拉式清單中的**儲存空間帳戶**，選擇您想要修改現有的帳戶。 
  2. 如有需要，您可以修改**啟用 SSL 模式**選取範圍。
  3. 您可以選擇重新產生您儲存帳戶便捷鍵。 如需詳細資訊，請參閱[重新產生儲存帳戶金鑰](storage-create-storage-account.md#manage-your-storage-access-keys)。 提供新的儲存空間帳戶金鑰。 Azure 儲存體帳戶，這是主要便捷鍵。 
  4. 按一下核取圖示![核取圖示](./media/storsimple-ova-manage-storage-accounts/checkicon.png)儲存設定。 在 [**設定**] 頁面上，就會更新設定。 
  5. 在頁面底部，按一下 [**儲存**] 儲存新的更新設定。 

     ![編輯儲存帳戶](./media/storsimple-ova-manage-storage-accounts/modifyexistingstorageaccount.png)
  
## <a name="delete-a-storage-account"></a>刪除儲存帳戶

> [AZURE.IMPORTANT] 只有不是使用，您可以刪除儲存的帳戶。 使用中儲存帳戶時，您會收到通知。

#### <a name="to-delete-a-storage-account"></a>若要刪除的儲存空間帳戶

1. 在 StorSimple 管理員服務登陸頁面上，選取您的服務、 按兩下服務名稱，然後再按一下 [**設定**。

2. 在 [儲存帳戶的 [表格式] 清單中，將游標停留在您想要刪除的帳戶。

3. (**X**) 上的 [刪除] 圖示會顯示在該儲存帳戶的極大的右欄中。 按一下 [刪除的認證的 [ **x** ] 圖示。

4. 當畫面提示您確認，按一下 [ **]**若要繼續刪除。 表格式清單將會更新以反映所做的變更。

5. 在頁面底部，按一下 [**儲存**] 儲存新的更新設定。


## <a name="next-steps"></a>後續步驟

- 瞭解如何[管理您的 StorSimple 虛擬陣列](storsimple-ova-web-ui-admin.md)。
