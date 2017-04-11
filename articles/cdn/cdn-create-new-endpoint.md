<properties
     pageTitle="使用 Azure CDN |Microsoft Azure"
     description="本主題說明如何啟用 Azure 內容傳遞網路 (CDN)。 教學課程會引導您完成建立新的 CDN 設定檔和結束點。"
     services="cdn"
     documentationCenter=""
     authors="camsoper"
     manager="erikre"
     editor=""/>
<tags
     ms.service="cdn"
     ms.workload="media"
     ms.tgt_pltfrm="na"
     ms.devlang="na"
     ms.topic="get-started-article"
     ms.date="07/28/2016" 
     ms.author="casoper"/>

# <a name="using-azure-cdn"></a>使用 Azure 的 CDN  

本主題會引導啟用 Azure CDN 藉由建立新的 CDN 設定檔及結束點。

>[AZURE.IMPORTANT] 簡介 CDN 的運作方式，以及一份功能，請參閱[CDN 概觀](./cdn-overview.md)。

## <a name="create-a-new-cdn-profile"></a>建立新的 CDN 設定檔

Cdn 到底設定檔是 CDN 端點的集合。  每個設定檔含有一或多個 CDN 結束點。  您可能會想要組織 CDN 端點 internet 的網域、 web 應用程式，或一些其他準則中使用多個設定檔。

> [AZURE.NOTE] 根據預設，單一 Azure 訂閱只限八個 CDN 設定檔。 每個 CDN 設定檔僅限於十 CDN 結束點。
>
> Cdn 到底價格會套用在 CDN 的設定檔層級。 如果您想要使用的 Azure CDN 價格層級，則需要多個 CDN 設定檔。

[AZURE.INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]

## <a name="create-a-new-cdn-endpoint"></a>建立新的 CDN 端點

**若要建立新的 CDN 端點**

1. 在[Azure 入口網站](https://portal.azure.com)，瀏覽至您的 CDN 設定檔。  您可能會有釘選它到上一個步驟中的儀表板。  如果您不是，您可以找到它，按一下 [**瀏覽]**，然後**CDN 設定檔**]，按一下您要新增端點，可設定檔。

    Cdn 到底設定檔刀隨即出現。

    ![Cdn 到底設定檔][cdn-profile-settings]

2. 按一下 [**新增結束點**] 按鈕。

    ![新增結束點] 按鈕][cdn-new-endpoint-button]

    **新增端點**刀隨即出現。

    ![新增端點刀][cdn-add-endpoint]

3. 此 CDN 端點，請輸入 [**名稱**]。  這個名稱會用來存取您的網域的快取的資源`<endpointname>.azureedge.net`。

4. 在 [**來源類型**] 下拉式清單中，選取您的來源類型。  Azure 儲存體帳戶的**儲存空間**，**雲端服務**Azure 雲端服務， **Web 應用程式**Azure Web 應用程式，或**自訂原點**選取任何其他公開存取網頁伺服器原點 （裝載 Azure 或其他位置）。

    ![Cdn 到底來源類型](./media/cdn-create-new-endpoint/cdn-origin-type.png)
        
5. 在 [ **Origin 主機名稱**] 下拉式清單中，選取或輸入您的來源網域。  下拉式清單會列出您在步驟 4 中指定之類型的所有可用的來源。  如果您選取作為**原點輸入**您的*自訂來源*，您會輸入您的自訂原始網域。

6. 在**原始路徑**] 文字方塊中，輸入您想要快取，或將保留空白，可讓您在步驟 5 中指定的網域在任何資源快取的資源的路徑。

7. 在**原始主機標頭**中，輸入您想要傳送的每個要求 CDN 主機標頭或保留預設值。

    > [AZURE.WARNING] 某些類型的來源，例如 Azure 儲存體和 Web 應用程式，需要以符合原始網域的主機標頭。 除非您需要不同的網域的主機標頭的來源，您應該保留預設值]。

8. 如需**通訊協定**與**來源連接埠**，指定用來存取您的資源位於原點的連接埠與通訊協定。  您必須選取至少有一個通訊協定 （HTTP 或 HTTPS）。
    
    > [AZURE.NOTE] **來源連接埠**只會影響什麼連接埠結束點用途，從來源擷取資訊。  端點本身時，才會使用預設 HTTP 結束用戶端和 HTTPS 連接埠 （80 和 443），不論**原始連接埠**。  
    >
    > **從 Akamai azure CDN**端點不允許來源的完整 TCP 連接埠範圍。  不允許的來源連接埠的清單，請參閱[Azure CDN 從 Akamai 允許的來源連接埠](https://msdn.microsoft.com/library/mt757337.aspx)。  
    >
    > 使用 HTTPS 的內容存取 CDN 具有下列的限制式︰
    > 
    > - 您必須使用所提供的 CDN 的 SSL 憑證。 協力廠商憑證不受支援。
    > - 您必須使用所提供 CDN 網域 (`<endpointname>.azureedge.net`) 存取 HTTPS 內容。 HTTPS 沒有支援的自訂網域名稱 (CNAMEs) 由於 CDN 這次支援自訂的憑證。

9. 按一下 [**新增**] 按鈕，以建立新的結束點。

10. 端點建立之後，它會出現在清單中的設定檔的結束點。 [清單] 檢視會顯示用來存取快取的內容，以及原始網域的 URL。

    ![Cdn 到底端點][cdn-endpoint-success]

    > [AZURE.IMPORTANT] 端點會立即無法為可供使用，如下所花的時間，才會傳播到 CDN 註冊。  <b>Azure CDN Akamai 從</b>設定檔，傳播通常會一分鐘內完成。  <b>Azure CDN Verizon 從</b>設定檔，傳播通常會 90 分鐘內完成，但在某些情況下可能需要花久。
    >    
    > 嘗試使用 CDN 網域名稱之前結束點設定已傳播到 Pop 的使用者會收到 HTTP 404 回應代碼。  由於您建立您的結束點，而您仍然收到 404 回應已幾小時的時間，請參閱[疑難排解 CDN 端點傳回 404 狀態](cdn-troubleshoot-endpoint.md)。


##<a name="see-also"></a>另請參閱
- [控制要求與查詢字串的快取行為](cdn-query-string.md)
- [如何將自訂網域名稱對應 CDN 內容](cdn-map-content-to-custom-domain.md)
- [Azure CDN 端點上預先載入資產](cdn-preload-endpoint.md)
- [清除 Azure CDN 端點](cdn-purge-endpoint.md)
- [疑難排解傳回 404 狀態的 CDN 端點](cdn-troubleshoot-endpoint.md)

[cdn-profile-settings]: ./media/cdn-create-new-endpoint/cdn-profile-settings.png
[cdn-new-endpoint-button]: ./media/cdn-create-new-endpoint/cdn-new-endpoint-button.png
[cdn-add-endpoint]: ./media/cdn-create-new-endpoint/cdn-add-endpoint.png
[cdn-endpoint-success]: ./media/cdn-create-new-endpoint/cdn-endpoint-success.png
