<properties
    pageTitle="儲存帳戶整合 CDN |Microsoft Azure"
    description="瞭解如何使用 Azure 內容傳遞網路 」 (CDN) 進行高頻寬內容快取 blob 從 Azure 儲存體。"
    services="cdn"
    documentationCenter=""
    authors="camsoper"
    manager="erikre"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/28/2016"
    ms.author="casoper"/>


# <a name="integrate-a-storage-account-with-cdn"></a>儲存帳戶整合 CDN

從 Azure 儲存時，可以啟用 CDN 快取內容。 它會提供開發人員全域快取二進位大型物件及計算執行個體，在 [美國、 歐洲、 亞洲、 澳大利亞和南美洲實體節點的靜態內容傳遞高頻寬內容的解決方案。


## <a name="step-1-create-a-storage-account"></a>步驟 1︰ 建立儲存帳戶

使用下列程序建立新的儲存空間帳戶 Azure 訂閱。 儲存帳戶可讓 Azure 儲存服務存取。 儲存帳戶代表最高層級的存取每個 Azure 儲存服務元件的命名空間︰ Blob 服務、 佇列中服務以及表格服務。 如需詳細資訊，請參閱[Microsoft Azure 儲存體簡介](../storage/storage-introduction.md)。

若要建立的儲存空間帳戶，您必須是服務管理員或相關聯的訂閱的共同管理員。

> [AZURE.NOTE] 有數種方法，可用來建立儲存帳戶，包括 Azure 入口網站和 Powershell。  在此教學課程中，我們將使用 Azure 入口網站。  

**若要建立的 Azure 訂閱的儲存空間帳戶**

1.  [Azure 入口網站](https://portal.azure.com)登入。
2.  在右上角，選取 [**新增**]。 在 [**新增**] 對話方塊中選取**資料 + 的儲存空間**，然後按一下 [**儲存帳戶。**

    **建立儲存帳戶**刀隨即出現。

    ![建立儲存帳戶][create-new-storage-account]

4. 在 [**名稱**] 欄位中輸入的子網域名稱。 3-24 小寫字母和數字，可以包含此項目。

    此值會變成主機名稱中使用地址的訂閱 Blob、 佇列中或表格資源 URI。 地址容器資源 Blob 服務中的，您可以使用 URI 以下列格式，其中*&lt;StorageAccountLabel&gt;*指的是您在**輸入 URL**輸入值︰

    http://*&lt;StorageAcountLabel&gt;*.blob.core.windows.net/*&lt;mycontainer&gt; *

    **重要︰**[URL] 標籤表單儲存帳戶 URI 的子網域，而且必須是唯一 Azure 中的所有主控服務之間。

    以程式控制方式存取此帳戶時，則此值也會用在入口網站，或此儲存帳戶的名稱。

5. 您可以保留預設值的**部署模型**、**帳戶類型**、**效能**及**複寫**。 

6. 選取**訂閱**的儲存空間帳戶使用。

7. 選取或建立**資源群組**]。  如需有關資源群組的詳細資訊，請參閱[Azure 資源管理員的概觀](azure-resource-manager/resource-group-overview.md#resource-groups)。

8. 選取您儲存的帳戶的位置。

8. 按一下 [**建立**]。 建立儲存帳戶的程序可能需要幾分鐘才能完成。


## <a name="step-2-create-a-new-cdn-profile"></a>步驟 2︰ 建立新的 CDN 設定檔

Cdn 到底設定檔是 CDN 端點的集合。  每個設定檔含有一或多個 CDN 結束點。  您可能會想要組織 CDN 端點 internet 的網域、 web 應用程式，或一些其他準則中使用多個設定檔。

> [AZURE.TIP] 如果您已經有您想要使用此教學課程中的 CDN 設定檔，繼續進行[步驟 3](#step-3-create-a-new-cdn-endpoint)。

[AZURE.INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]

## <a name="step-3-create-a-new-cdn-endpoint"></a>步驟 3︰ 建立新的 CDN 端點

**若要建立新的 CDN 端點，為您儲存的帳戶**

1. 在[管理入口網站 Azure](https://portal.azure.com)，瀏覽至您的 CDN 設定檔。  您可能會有釘選它到上一個步驟中的儀表板。  如果您不是，您可以找到它，按一下 [**瀏覽]**，然後**CDN 設定檔**]，按一下您要新增端點，可設定檔。

    Cdn 到底設定檔刀隨即出現。

    ![Cdn 到底設定檔][cdn-profile-settings]

2. 按一下 [**新增結束點**] 按鈕。

    ![新增結束點] 按鈕][cdn-new-endpoint-button]

    **新增端點**刀隨即出現。

    ![新增端點刀][cdn-add-endpoint]

3. 此 CDN 端點，請輸入 [**名稱**]。  這個名稱會用來存取您的網域的快取的資源`<endpointname>.azureedge.net`。

4. 在 [**來源類型**] 下拉式清單中，選取 [*儲存*]。  

5. 在 [ **Origin 主機名稱**] 下拉式清單中，選取您儲存的帳戶。

6. 您可以保留預設值的**來源路徑**與**原始主機標頭**，**原始通訊協定/連接埠**。  您必須指定至少有一個通訊協定 （HTTP 或 HTTPS）。

    > [AZURE.NOTE] 此設定可讓您的公開可見容器儲存帳戶的 CDN 中的快取中的所有。  如果您想要範圍限制為單一容器，請使用**原始路徑**。  請注意容器必須可見度設為公開。

7. 按一下 [**新增**] 按鈕，以建立新的結束點。

8. 端點建立之後，它會出現在清單中的設定檔的結束點。 [清單] 檢視會顯示用來存取快取的內容，以及原始網域的 URL。

    ![Cdn 到底端點][cdn-endpoint-success]

    > [AZURE.NOTE] 端點會立即無法為可供使用。  可以需要 90 分鐘的時間，才會傳播到 CDN 網路註冊。 嘗試立即使用 CDN 的網域名稱的使用者可能會收到狀態碼 404，直到該內容會透過 CDN 提供使用。


## <a name="step-4-access-cdn-content"></a>步驟 4︰ 存取內容的 CDN

若要存取 CDN 快取的內容，請使用在入口網站提供的 CDN URL。 快取 blob 的地址會類似下列動作︰

http://<*EndpointName*\>.azureedge.net/ <*myPublicContainer*\>/<*BlobName*\>

> [AZURE.NOTE] 一旦您啟用 CDN 存取儲存的帳戶或代管服務時，可公開使用的所有物件都是 CDN 邊緣快取的。 如果您修改目前快取中 CDN 的物件，新的內容會才可透過 CDN CDN 在的快取的內容存留時間週期到期時，會重新整理其內容。

## <a name="step-5-remove-content-from-the-cdn"></a>步驟 5: 的 CDN 移除內容

如果您不再想快取物件中 Azure 內容傳遞網路 (CDN)，您可以採取下列步驟︰

-   您可以進行容器私人，而非 public。 如需詳細資訊，請參閱[管理匿名的讀取權限容器及二進位大型物件](../storage/storage-manage-access-to-resources.md)。
-   您可以停用或刪除 CDN 端點，使用 [管理入口網站。
-   您可以修改您的代管的服務不再回應要求的物件。

已快取中 CDN 物件仍會維持快取直到物件的存留時間週期到期或清除結束點。 當的存留時間週期到期時，CDN 會檢查是否 CDN 端點仍然有效，而且仍以匿名方式可存取物件。 如果尚未選取，然後物件將不再快取。


## <a name="additional-resources"></a>其他資源

-   [如何將自訂網域名稱對應 CDN 內容](cdn-map-content-to-custom-domain.md)

[create-new-storage-account]: ./media/cdn-create-a-storage-account-with-cdn/CDN_CreateNewStorageAcct.png

[cdn-profile-settings]: ./media/cdn-create-a-storage-account-with-cdn/cdn-profile-settings.png
[cdn-new-endpoint-button]: ./media/cdn-create-a-storage-account-with-cdn/cdn-new-endpoint-button.png
[cdn-add-endpoint]: ./media/cdn-create-a-storage-account-with-cdn/cdn-add-endpoint.png
[cdn-endpoint-success]: ./media/cdn-create-a-storage-account-with-cdn/cdn-endpoint-success.png
