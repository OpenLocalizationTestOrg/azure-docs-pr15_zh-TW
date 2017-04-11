<properties 
   pageTitle="管理存取控制記錄 StorSimple 虛擬陣列 |Microsoft Azure"
   description="說明如何管理來決定哪些主機可以連線至 StorSimple 虛擬陣列上區的 access 控制項記錄 (ACRs)。"
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
   ms.date="05/03/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-manage-access-control-records-for-the-storsimple-virtual-array"></a>使用 StorSimple 管理員服務來管理 StorSimple 虛擬陣列存取控制記錄 

## <a name="overview"></a>概觀

Access 控制項記錄 (ACRs) 可讓您指定哪些主機可以連線至 StorSimple 虛擬陣列 （也稱為 StorSimple 內部部署虛擬裝置） 上區。 ACRs 設定為特定的音量，並包含主機 iSCSI 完整名稱 (IQNs)。 當主機嘗試連線到大量時，裝置檢查相關聯的 IQN 名稱，該大量 ACR 並如果沒有符合項目，然後建立連線。 在 [**設定**] 頁面上的 [**存取控制記錄**] 區段會顯示與相對應的 IQNs 主機的所有 access 控制項筆記錄。

本教學課程說明下列常見項 ACR 相關的工作︰

- 取得 IQN
- 新增記錄 access 控制項 
- 編輯記錄 access 控制項 
- 刪除存取控制記錄 

> [AZURE.IMPORTANT] 
> 
> - 指派時 ACR 音量，請注意，音量不同時存取多個非叢集主機因為這可能會損毀音量。 
> - 當 ACR 刪除區，請確定對應主機不存取音量因為刪除可能會導致讀寫中斷。

## <a name="get-the-iqn"></a>取得 IQN

執行下列步驟，以取得 Windows 主機正在執行 Windows Server 2012 的 IQN。

[AZURE.INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]

## <a name="add-an-acr"></a>新增 ACR

您可以使用 [StorSimple 管理員服務**設定**] 頁面來新增 ACRs。 一般而言，您會建立一個 ACR 與一個大量的關聯。

ACR 關聯大量的相關資訊，請移至[新增音量](storsimple-ova-deploy3-iscsi-setup.md#step-3-add-a-volume)。

>[AZURE.IMPORTANT] 
> 
>指派時 ACR 音量，請注意，音量不同時存取多個非叢集主機因為這可能會損毀音量。
 
執行下列步驟，以新增 ACR。

#### <a name="to-add-an-acr"></a>若要新增 ACR

1. 在服務登陸頁面上，選取您的服務，按兩下服務名稱，然後按一下 [**設定**] 索引標籤。

    ![組態] 索引標籤](./media/storsimple-ova-manage-acrs/acr1.png)

2. 在 [**存取控制記錄**] 底下表格式清單中，提供一個**名稱**為您 ACR。

3. 下**iSCSI 啟動器名稱**，提供您的 Windows 主機 IQN 名稱。 

4. 按一下 [**儲存**在頁面底部的儲存新建立的 ACR。 您會看到下列的確認訊息。

    ![確認訊息](./media/storsimple-ova-manage-acrs/acr2.png)

5. 按一下 [檢查] 圖示 ![檢查圖示](./media/storsimple-ova-manage-acrs/check-icon.png). 表格式清單將會更新以反映這種加入。

## <a name="edit-an-acr"></a>編輯 ACR

若要編輯 ACRs Azure 傳統入口網站在使用 [**設定**] 頁面。 

> [AZURE.NOTE] 您應該修改只在目前不是使用中的 ACRs。 若要編輯目前正在使用大量相關聯 ACR，您應該先離線音量。

執行下列步驟，以編輯 ACR。

#### <a name="to-edit-an-acr"></a>若要編輯 ACR

1. 在服務登陸頁面上，選取您的服務，按兩下服務名稱，然後按一下 [**設定**] 索引標籤。

2. 在 access 控制項記錄表格式清單中，將游標停留在您想要修改 ACR。

3. 新的名稱及/或 IQN 提供 ACR。

4. 按一下 [**儲存**在頁面底部的儲存已修改的 ACR。 您會看到一則確認訊息。 

5. 按一下 [檢查] 圖示 ![檢查圖示](./media/storsimple-ova-manage-acrs/check-icon.png). 表格式清單將會更新以反映這項變更。

## <a name="delete-an-access-control-record"></a>刪除 access 控制項的記錄

若要刪除 ACRs Azure 傳統入口網站在使用 [**設定**] 頁面。 

> [AZURE.NOTE] 
> 
> - 您應該刪除只在目前不是使用中的 ACRs。 若要刪除目前正在使用大量相關聯 ACR，您應該先離線音量。
> - 當 ACR 刪除區，請確定對應主機不存取音量因為刪除可能會導致讀寫中斷。

執行下列步驟，以刪除 access 控制項的記錄。

#### <a name="to-delete-an-access-control-record"></a>若要刪除的 access 控制項記錄

1. 在服務登陸頁面上，選取您的服務，按兩下服務名稱，然後按一下 [**設定**] 索引標籤。

2. 在表格式的 access 控制項的記錄 (ACRs) 清單中，將游標停留在您想要刪除 ACR。

3. 刪除圖示 (**x**) 會出現在非常右邊的欄中，選取 ACR 的。 按一下 [ **x** ] 圖示，若要刪除 ACR。 您會看到下列的確認訊息。

    ![確認訊息](./media/storsimple-ova-manage-acrs/acr3.png)

5. 按一下 [檢查] 圖示 ![檢查圖示](./media/storsimple-ova-manage-acrs/check-icon.png). 表格式清單將會更新以反映刪除。

## <a name="next-steps"></a>後續步驟

- 進一步瞭解[新增區及設定 ACRs](storsimple-ova-deploy3-iscsi-setup.md#step-3-add-a-volume)。
