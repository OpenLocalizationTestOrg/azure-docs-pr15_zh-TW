<properties
    pageTitle="疑難排解 Azure CDN 端點傳回 404 的狀態 |Microsoft Azure"
    description="疑難排解與 Azure CDN 端點 404 回應代碼。"
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
    
# <a name="troubleshooting-cdn-endpoints-returning-404-statuses"></a>疑難排解傳回 404 狀態的 CDN 端點

本文可協助您疑難排解問題與[CDN 端點](cdn-create-new-endpoint.md)傳回 404 錯誤。

如果您需要更多協助，這份文件中的任一點時，您也可以連絡 Azure 專家[MSDN Azure 和堆疊溢位論壇](https://azure.microsoft.com/support/forums/)上。 或者，您也可以檔案 Azure 支援事件。 移至 [ [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後按一下 [**取得**支援。

## <a name="symptom"></a>徵狀

您已經建立 CDN 設定檔和 [結束點，但似乎 CDN 在您的內容。  使用者嘗試存取您的內容，透過 CDN URL 收到 HTTP 404 狀態碼。 

## <a name="cause"></a>原因

有多個可能的原因，包括︰

- 檔案的來源不會看到的 CDN
- 端點︰ 設定錯誤，導致錯誤的位置中呈現的外觀 CDN
- Host （主機） 已拒絕 CDN 主機頁首
- 端點尚未有時間，才會傳播整個 CDN

## <a name="troubleshooting-steps"></a>疑難排解步驟

> [AZURE.IMPORTANT] 建立 CDN 端點之後，立即無法可供使用，如下所花的時間，才會傳播到 CDN 註冊。  <b>Azure CDN Akamai 從</b>設定檔，傳播通常會完成一分鐘內。  <b>Azure CDN Verizon 從</b>設定檔，傳播通常會 90 分鐘內完成，但在某些情況下可能需要花久。  如果您完成這份文件中的步驟，您仍然收到 404 回應，請考慮等候檢查一次開啟支援票證前幾個小時。

### <a name="check-the-origin-file"></a>核取來源檔案

首先，我們應該確認我們所需快取檔案會提供我們原點和公開存取。  若要執行這項作業最快速的方法是在私人或 Incognito 工作階段中開啟瀏覽器，並直接瀏覽至檔案。  只輸入或 URL 貼到 [位址] 方塊，請參閱是否，導致您預期的檔案。  例如，我要使用的檔案中 Azure 儲存體帳戶，請在存取擁有`https://cdndocdemo.blob.core.windows.net/publicblob/lorem.txt`。  如您所見，其已成功傳遞測試。

![成功 ！](./media/cdn-troubleshoot-endpoint/cdn-origin-file.png)

> [AZURE.WARNING] 雖然這是最快速簡單的方式來確認您的檔案是可公開使用，您組織中的某些網路設定可以讓您圖例，此檔案，就可公開使用，則事實上，只顯示您的網路的使用者 （即使其會裝載於 Azure）。  如果您有外部的瀏覽器，從您可以測試，例如行動裝置的未連線到您組織的網路或在 Azure 虛擬機器那就是最佳。

### <a name="check-the-origin-settings"></a>核取 [origin] 設定

現在，我們已驗證的檔案是在網際網路上公開可用，我們應該驗證我們原始設定。  在[Azure 入口網站](https://portal.azure.com)，瀏覽至您的 CDN 設定檔，然後按一下您正在進行疑難排解的端點。  在產生的**端點**刀中，按一下 [來源]。  

![結束點刀與反白顯示的原點](./media/cdn-troubleshoot-endpoint/cdn-endpoint.png)

**原點**刀隨即出現。 

![原點刀](./media/cdn-troubleshoot-endpoint/cdn-origin-settings.png)

#### <a name="origin-type-and-hostname"></a>來源類型與主機名稱

確認 [**來源類型**正確，並確認**原點主機名稱**。  在我的範例， `https://cdndocdemo.blob.core.windows.net/publicblob/lorem.txt`，主機名稱的部分 URL 是`cdndocdemo.blob.core.windows.net`。  您可以看到螢幕擷取畫面，這是正確。  Azure 儲存體、 Web 應用程式，及雲端服務來源，[ **Origin 主機名稱**] 欄位是下拉式清單中，我們不需擔心其拼字正確。  不過，如果您使用的自訂的來源，它是*絕對的要徑*您主機名稱拼字正確 ！

#### <a name="http-and-https-ports"></a>HTTP 和 HTTPS 的連接埠

若要查看此處的其他項目，而且您**HTTP** **HTTPS 連接埠**。  在大部分情況下，80 和 443 都正確，並且您需要任何變更。  不過，如果來源伺服器不同的連接埠接聽的需要以下表示。  如果您不確定，只要查看您的來源檔案的 URL。  HTTP 和 HTTPS 的相關規格，指定為預設值的連接埠 80 和 443。 在 [我的 URL， `https://cdndocdemo.blob.core.windows.net/publicblob/lorem.txt`，不指定連接埠，因此 443 的預設值，並在我的設定正確無誤。  

不過，在您進行測試較舊版本的來源檔案是假設 URL `http://www.contoso.com:8080/file.txt`。  請注意`:8080`結尾的 「 主機名稱 」 區段。  其目的是告知瀏覽器使用的連接埠`8080`連線至網頁伺服器， `www.contoso.com`，因此您需要**HTTP 連接埠**] 欄位中輸入 8080。  請務必請注意下列連接埠設定只會影響到哪一個連接埠端點使用從來源擷取資訊。

> [AZURE.NOTE] **從 Akamai azure CDN**端點不允許來源的完整 TCP 連接埠範圍。  不允許的來源連接埠的清單，請參閱[Azure CDN 從 Akamai 允許的來源連接埠](https://msdn.microsoft.com/library/mt757337.aspx)。  
  
### <a name="check-the-endpoint-settings"></a>核取 [結束點] 設定

回到上**端點**刀中，按一下 [**設定**] 按鈕。

![結束點刀與反白顯示的 [設定] 按鈕](./media/cdn-troubleshoot-endpoint/cdn-endpoint-configure-button.png)

端點的**設定**刀隨即出現。

![設定刀](./media/cdn-troubleshoot-endpoint/cdn-configure.png)

#### <a name="protocols"></a>通訊協定

**通訊協定**，請確認已選取 [在用戶端的通訊協定。  用戶端使用相同的通訊協定會用來存取原點，因此請務必有正確設定前一節中的來源連接埠的項目。  端點只接聽預設 HTTP 和 HTTPS 的連接埠 （80 和 443），不論原始連接埠。

現在就讓我們返回使用我們假設範例`http://www.contoso.com:8080/file.txt`。  您會記住，當 Contoso 指定`8080`為其 HTTP 連接埠，但我們也假設其指定`44300`為其 HTTPS 連接埠。  如果他們建立名為端點`contoso`，其 CDN 端點主機名稱應`contoso.azureedge.net`。  要求`http://contoso.azureedge.net/file.txt`是 HTTP 要求，，因此端點會使用連接埠 8080 HTTP 擷取來源。  在 HTTPS 上的安全要求`https://contoso.azureedge.net/file.txt`，就會使用 HTTPS 連接埠 44300 端點時 retriving 從來源檔案。

#### <a name="origin-host-header"></a>原始主機標頭

**原始主機標頭**是傳送給每個要求原點的主機頁首值。  在大部分情況下，這應該是我們先前驗證**原點主機名稱**相同。  在此欄位中不正確的值通常不會導致出現 404 的狀態，但可能會造成其他 4xx 的狀態，根據原點的預期。

#### <a name="origin-path"></a>原始路徑

最後，我們應該確認我們**原點路徑**。  依預設這是空白項目。  如果您想要縮小您想要在 CDN 提供的原始裝載的資源的範圍，您應該僅使用此欄位。  

例如，在我的端點，我想要所有資源我儲存的帳戶]，才能使用，所以我空白**原點路徑**。  這表示的要求`https://cdndocdemo.azureedge.net/publicblob/lorem.txt`產生的連線，從 [我的端點，`cdndocdemo.core.windows.net`的要求`/publicblob/lorem.txt`。  同樣地，要求`https://cdndocdemo.azureedge.net/donotcache/status.png`產生端點要求`/donotcache/status.png`從來源。

但如果我不想使用的所有路徑上我的原點的 CDN？  假設我只想要的方式公開`publicblob`的路徑。  我**原點路徑**] 欄位中輸入*/publicblob* ，，便會插入*/publicblob*原點進行每個要求之前的端點。  這表示的要求`https://cdndocdemo.azureedge.net/publicblob/lorem.txt`實際現在會取得邀請部分的 URL， `/publicblob/lorem.txt`，與附加`/publicblob`開頭。 這會導致要求`/publicblob/publicblob/lorem.txt`從來源。  如果該路徑仍無法解決實際的檔案，原點會傳回 404 的狀態。  實際上是正確的 URL 來擷取 lorem.txt 在此範例中`https://cdndocdemo.azureedge.net/lorem.txt`。  請注意，我們不加*/publicblob*路徑，因為 URL 的要求部分`/lorem.txt`並新增端點`/publicblob`、 導致`/publicblob/lorem.txt`要求傳遞給原點。
