# <a name="using-cdn-for-azure"></a>Cdn 到底用 Azure

Azure 內容傳遞網路 」 (CDN) 提供開發人員全域快取二進位大型物件及計算執行個體，在 [美國、 歐洲、 亞洲、 澳大利亞和南美洲實體節點的靜態內容傳遞高頻寬內容的解決方案。 Cdn 到底節點位置目前的清單，請參閱[Azure CDN 節點的位置]。

這項工作包括下列步驟︰

* [步驟 1︰ 建立儲存帳戶](#Step1)
* [步驟 2︰ 建立新的 CDN 端點，為您儲存的帳戶](#Step2)
* [步驟 3︰ 存取您的 CDN 內容](#Step3)
* [步驟 4︰ 移除您的 CDN 內容](#Step4)

使用 CDN 快取 Azure 資料的優點包括︰

-   更佳的效能和使用者體驗的一般使用者的內容來源，不是，且用來使用許多 '網際網路往返 」 所需載入內容的應用程式
-   大型分散式更有效地處理瞬間完成的效能高負載，比方說，例如產品啟動事件的開頭

現有的 CDN 客戶現在可以使用 Azure CDN [Azure 傳統入口網站]中。 Cdn 到底是附加元件功能至您的訂閱，而且有不同的[帳單的方案]。

<a id="Step1"> </a>
<h2>步驟 1︰ 建立儲存帳戶</h2>

使用下列程序建立新的儲存空間帳戶 Azure 訂閱。 儲存帳戶可讓 Azure 儲存服務存取。 儲存帳戶代表最高層級的存取 Azure 儲存服務元件的命名空間︰ Blob 服務、 佇列中服務以及表格服務。 如需有關 Azure 儲存服務的詳細資訊，請參閱[使用 Azure 儲存服務](http://msdn.microsoft.com/library/azure/gg433040.aspx)。

若要建立的儲存空間帳戶，您必須是服務管理員或相關聯的訂閱的共同管理員。

> [AZURE.NOTE] 有關如何使用 Azure 服務管理 API 來執行這項作業，請參閱[建立儲存帳戶](http://msdn.microsoft.com/library/windowsazure/hh264518.aspx)參照主題。

**若要建立的 Azure 訂閱的儲存空間帳戶**

1.  登入[Azure 傳統入口網站]。
2.  在右下角，按一下 [**新增**]。 在 [**新增**] 對話方塊中選取 [**資料服務**]，然後按一下 [**儲存空間**，然後 [**快速建立**。

    **建立儲存帳戶**] 對話方塊隨即出現。

    ![建立儲存帳戶][create-new-storage-account]

4. 在 [ **URL** ] 欄位中，輸入的子網域名稱。 這個項目可以包含 3 24 小寫字母和數字。

    此值會變成主機名稱中使用地址的訂閱 Blob、 佇列中或表格資源 URI。 若要解決容器資源 Blob 服務中的，您想使用 URI 以下列格式，其中*&lt;StorageAccountLabel&gt;*指的是您在**輸入 URL**輸入值︰

    http://*&lt;StorageAcountLabel&gt;*.blob.core.windows.net/*&lt;mycontainer&gt; *

    **重要︰**[URL] 標籤表單儲存帳戶 URI 的子網域，而且必須是唯一 Azure 中的所有主控服務之間。

    以程式控制方式存取此帳戶時，則此值也會用在入口網站，或此儲存帳戶的名稱。

5.  從 [**地區/相關性群組**] 下拉式清單中，選取地區或相關性] 群組中的儲存空間帳戶。 如果您想要在相同的資料中心，與您所使用的其他 Windows Azure 服務儲存服務，請選取 [相關性] 群組中，而不是區域]。 這可以改善效能，並沒有費用所產生的出口。  

    **附註︰**若要建立相關性] 群組中，開啟的 [管理] 入口網站的 [**設定**] 區域，按一下**相關性群組**] 下，，然後按一下**新增相關性群組**] 或 [**新增**。 您也可以建立及管理使用 Windows Azure 服務管理 API 的相關性群組。 如需詳細資訊，請參閱 [作業相關性群組]。

6. 從 [**訂閱**] 下拉式清單中，選取 [訂閱的儲存空間帳戶會使用]。
7.  按一下 [**建立儲存帳戶**]。 建立儲存帳戶的程序可能需要幾分鐘才能完成。
8.  若要驗證的儲存空間帳戶已成功建立，請確認該帳戶會出現在所列的**儲存空間**的**線上**狀態為項目。

<a id="Step2"> </a>
<h2>步驟 2︰ 建立新的 CDN 端點，為您儲存的帳戶</h2>

一旦您啟用 CDN 存取儲存的帳戶或代管服務時，可公開使用的所有物件都是 CDN 邊緣快取的。 如果您修改目前快取中 CDN 的物件，新的內容會才可透過 CDN CDN 在的快取的內容存留時間週期到期時，會重新整理其內容。

**若要建立新的 CDN 端點，為您儲存的帳戶**

1. 在[Azure 傳統入口網站]，在功能窗格] 中按一下 [ **CDN**]。

2. 在功能區 [**新增**]。 在 [**新增**] 對話方塊中，選取 [**應用程式服務**，然後**CDN**，然後**快速建立**]。

3. 在**原始網域**] 下拉式清單中，選取您在前一節的可用儲存帳戶從清單中建立的儲存空間帳戶。 

4. 按一下 [**建立**] 按鈕，以建立新的結束點。

5. 端點建立後，會顯示清單中的端點的訂閱。 [清單] 檢視會顯示用來存取快取的內容，以及 origin 網域的 URL。 

    原點網域是從 CDN 快取內容的位置。 原點網域可以是儲存帳戶或雲端服務;儲存帳戶用於此範例的目的。 儲存的內容會根據您所指定的快取控制項設定或快取的網路] 的預設 heuristics 邊緣伺服器快取。 


    > [AZURE.NOTE] 建立端點設定會立即無法使用。可以需要 60 分鐘的時間，才會傳播到 CDN 網路註冊。 嘗試立即使用 CDN 的網域名稱的使用者可能會收到狀態碼 400 （錯誤的要求），直到該內容會透過 CDN 提供使用。

<a id="Step3"> </a>
<h2>步驟 3︰ 存取內容的 CDN</h2> 

若要存取 CDN 快取的內容，請使用在入口網站提供的 CDN URL。 快取的 blob 的地址會類似下列動作︰

http://<*CDNNamespace*\>.vo.msecnd.net/ <*myPublicContainer*\>/<*BlobName*\>

<a id="Step4"> </a>
<h2>步驟 4︰ 移除 CDN 的內容</h2>

如果您不再想快取物件中 Azure 內容傳遞網路 (CDN)，您可以採取下列步驟︰

-   為 Azure blob，您可以刪除 blob 從公用容器。
-   您可以進行容器私人，而非 public。 如需詳細資訊，請參閱[限制存取容器及二進位大型物件](https://azure.microsoft.com/documentation/articles/storage-manage-access-to-resources/#restrict-access-to-containers-and-blobs)。
-   您可以停用或刪除 CDN 端點，使用 [管理入口網站。
-   您可以修改您的代管的服務不再回應要求的物件。

已快取中 CDN 物件仍會維持快取到物件的存留時間週期到期為止。 當的存留時間週期到期時，CDN 會檢查是否 CDN 端點仍然有效，而且仍以匿名方式可存取物件。 如果尚未選取，然後物件將不再快取。

Azure 管理入口網站上目前不支援可立即清除的內容。 請如果您需要立即清除的內容，連絡[支援部門 Azure](https://azure.microsoft.com/support/options/) 。 

## <a name="additional-resources"></a>其他資源

-   [如何建立 Azure 中的相關性群組]
-   [如何︰ 管理 Azure 訂閱的儲存空間帳戶]
-   [關於服務管理的 API]
-   [如何將自訂網域名稱對應 CDN 內容]

  [Create Storage Account]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/
  [Azure CDN 節點位置]: http://msdn.microsoft.com/library/windowsazure/gg680302.aspx
  [Azure 傳統入口網站]: https://manage.windowsazure.com/
  [帳單寄送的計劃]: /pricing/calculator/?scenario=full
  [如何建立 Azure 中的相關性群組]: http://msdn.microsoft.com/library/azure/ee460798.aspx
  [Overview of the Azure CDN]: http://msdn.microsoft.com/library/windowsazure/ff919703.aspx
  [關於服務管理的 API]: http://msdn.microsoft.com/library/windowsazure/ee460807.aspx
  [如何將自訂網域名稱對應 CDN 內容]: http://msdn.microsoft.com/library/windowsazure/gg680307.aspx


[create-new-storage-account]: ./media/cdn/CDN_CreateNewStorageAcct.png
[Previous Management Portal]: ../../Shared/Media/previous-portal.png
