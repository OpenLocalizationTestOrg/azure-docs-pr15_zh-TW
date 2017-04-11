<properties
    pageTitle="在雲端服務中設定自訂網域名稱 |Microsoft Azure"
    description="瞭解如何將公開 Azure 應用程式或在自訂網域上網際網路資料來設定 DNS 設定。  這些範例使用 Azure 入口網站。"
    services="cloud-services"
    documentationCenter=".net"
    authors="Thraka"
    manager="timlt"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="adegeo"/>

# <a name="configuring-a-custom-domain-name-for-an-azure-cloud-service"></a>設定 Azure 雲端服務的自訂網域名稱

> [AZURE.SELECTOR]
- [Azure 入口網站](cloud-services-custom-domain-name-portal.md)
- [Azure 傳統入口網站](cloud-services-custom-domain-name.md)

當您建立雲端服務時，則 Azure 會將其指派給**cloudapp.net**的子網域。 例如，如果您的雲端服務名稱為 「 contoso 」，您的使用者會無法存取您的應用程式的 URL，例如 http://contoso.cloudapp.net 上。 Azure 也會指派虛擬的 IP 位址。

不過，您也可以在自己的網域名稱，例如**contoso.com**公開您的應用程式。 本文說明如何保留或設定雲端服務 web 角色的自訂網域名稱。

執行您已 undestand 哪些 CNAME 和 A 記錄？ [過去 explaination 跳轉](#add-a-cname-record-for-your-custom-domain)。

> [AZURE.NOTE]
> 這項工作的程序會套用至 Azure 雲端服務。 如需應用程式服務，請參閱[此](../app-service-web/web-sites-custom-domain-name.md)。 儲存帳戶，請參閱[此](../storage/storage-custom-domain-name.md)。

<p/>

> [AZURE.TIP]
> 更快速-上手使用新的 Azure[逐步引導](http://support.microsoft.com/kb/2990804)！  它會建立關聯的自訂網域名稱與安全通訊 (SSL) Azure 雲端服務的 Azure 網站貼齊。

## <a name="understand-cname-and-a-records"></a>了解 CNAME 和 A 記錄

CNAME （或別名記錄） 和記錄這兩個可讓您建立網域名稱與特定的伺服器之間的關聯 （或在此情況下，服務） 不過其運作方式會不同。 還有一些特定的考量事項與 Azure 雲端服務，您應該先決定要使用哪考慮使用記錄時。

### <a name="cname-or-alias-record"></a>CNAME 或別名記錄

CNAME 記錄會將*特定*的網域，例如**contoso.com**或**www.contoso.com**，對應至標準的網域名稱。 在這個案例中，是標準的網域名稱**[myapp].cloudapp.net**網域名稱。 您 Azure 裝載於應用程式。 CNAME 建立後，建立一個別名**[myapp].cloudapp.net**。 CNAME 項目會解析為的 IP 位址您**[myapp].cloudapp.net**自動服務，因此如果雲端服務的 IP 位址變更，您不必採取任何動作。

> [AZURE.NOTE]
> 一些網域註冊機構只允許您將對應的子網域時使用 CNAME 記錄，例如 www.contoso.com，並不根名稱，例如 contoso.com。 如需有關 CNAME 記錄的詳細資訊，請參閱提供您的註冊機構、[維基百科上的項目 CNAME 記錄](http://en.wikipedia.org/wiki/CNAME_record)時或[IETF 網域名稱-實作與規格](http://tools.ietf.org/html/rfc1035)文件的文件。

### <a name="a-record"></a>記錄

例如*A*記錄對應的網域，例如**contoso.com**或**www.contoso.com**，*或萬用字元網域* ** \*。 contoso.com**，IP 位址。 若是 Azure 雲端服務，服務的虛擬 IP。 因此 A 記錄上一筆 CNAME 記錄的主要優點是，您可以讓項目，例如使用萬用字元， \* **。 contoso.com**，其會處理多個子網域，例如**mail.contoso.com**、 **login.contoso.com**或**www.contso.com**的要求。

> [AZURE.NOTE]
> A 記錄] 已對應至的靜態 IP 位址，因為其無法自動解析變更您的雲端服務的 IP 位址。 您的雲端服務所用的 IP 位址已配置第一次您部署至空白的位置 （生產或臨時。）如果您刪除此位置的部署，Azure 釋放的 IP 位址，然後到位置的任何未來部署指定新的 IP 位址。
>
> 在此便利地，臨時和生產部署或執行就地升級現有的部署的之間交換時，會保存指定的部署位置 （生產或執行） 的 IP 位址。 如需有關如何執行這些動作的詳細資訊，請參閱[如何管理雲端服務](cloud-services-how-to-manage.md)。


## <a name="add-a-cname-record-for-your-custom-domain"></a>新增您的自訂網域的 CNAME 記錄

若要建立 CNAME 記錄，您必須新增新的項目 DNS 資料表中的自訂網域使用您的註冊機構提供的工具。 每個註冊機構有類似，但稍有不同的方法，指定一筆 CNAME 記錄，但是相同的概念。

1. 使用其中一個方法來尋找**。 cloudapp.net**指派給您的雲端服務的網域名稱。

    * 登入[Azure 入口網站]，選取您的雲端服務，查看 [**基本資訊**] 區段，然後找到 [的**網站 URL**項目。

        ![顯示網站 URL 的快速檢視區段][csurl]
            
        **OR**
  
    * 安裝並設定[Powershell 的 Azure](../powershell-install-configure.md)，然後使用下列命令︰

        ```powershell
        Get-AzureDeployment -ServiceName yourservicename | Select Url
        ```
    
    儲存] 當您將需要建立一筆 CNAME 記錄時，所傳回的任何一種方法，將 URL 中使用的網域名稱。

1.  登入您 DNS 註冊機構的網站，然後移至 [管理 DNS 的頁面。 尋找連結或標示為 「**網域名稱**、 **DNS**或**名稱伺服器管理**網站的區域。

2.  現在尋找您可以在此選取或輸入 CNAME 的。 您可能必須選取的記錄類型從下拉向下或移至 [進階的設定] 頁面。 您應該看這幾個字**CNAME**，**別名**或**子網域**。

3.  您也必須提供的 CNAME，例如**www**網域或子網域別名，如果您想要建立**www.customdomain.com**別名。 如果您想要建立根網域別名，它可能會列為 '**@**' 在您的註冊機構 DNS 工具] 中的符號。

4. 然後，您必須提供標準主機名稱，在本例中是您的應用程式**cloudapp.net**網域。

例如，下列 CNAME 記錄轉所有流量從**www.contoso.com** **contoso.cloudapp.net**，部署的應用程式的自訂網域名稱︰

| Alias （別名）/主機名稱子網域 | 標準的網域     |
| ------------------------- | -------------------- |
| www                       | contoso.cloudapp.net |

> [AZURE.NOTE]
**Www.contoso.com**的訪客不會看到 true 主機 (contoso.cloudapp.net)，因此轉寄程序是看不到使用者。

> 上述範例只適用於流量**www**子網域。 由於您無法使用萬用字元的 CNAME 記錄，您必須建立每個網域/子網域的一筆的 CNAME。 如果您想要直接從子網域，例如*。 contoso.com，對 cloudapp.net 地址，您可以設定* *URL 重新導向**或* *URL 轉寄** 項目，在您的 DNS 設定，或建立 A 記錄。


## <a name="add-an-a-record-for-your-custom-domain"></a>新增 A 記錄以您的自訂網域

若要建立 A 記錄，您必須先找到您的雲端服務的虛擬 IP 位址。 使用您的註冊機構提供的工具，然後在您的自訂網域的 DNS 表格中新增新的項目。 每個註冊機構有類似，但稍有不同的方法，指定 A 記錄，但是相同的概念。

1. 使用下列方法之一來取得您的雲端服務的 IP 位址。

    * 登入至[Azure 入口網站]中，選取您的雲端服務，查看 [**基本資訊**] 區段，然後找到的**公用 IP 位址**項目。

        ![快速檢視顯示 VIP] 區段][vip]

        **OR**

    * 安裝並設定[Powershell 的 Azure](../powershell-install-configure.md)，然後使用下列命令︰

        ```powershell
        get-azurevm -servicename yourservicename | get-azureendpoint -VM {$_.VM} | select Vip
        ```
    
    當您將需要建立 A 記錄時，請儲存的 IP 位址、。

1.  登入您 DNS 註冊機構的網站，然後移至 [管理 DNS 的頁面。 尋找連結或標示為 「**網域名稱**、 **DNS**或**名稱伺服器管理**網站的區域。

2.  您可以在此選取或輸入一筆記錄，現在找到。 您可能必須選取的記錄類型從下拉向下或移至 [進階的設定] 頁面。

3. 選取或輸入網域或會使用此 A 記錄的子網域。 如果您想要建立**www.customdomain.com**別名，，例如，選取 [ **www** ]。 如果您想要建立的所有的子網域為萬用字元項目，請輸入 「__*__」。 這會包含所有的子網域，例如**mail.customdomain.com**、 **login.customdomain.com**及**www.customdomain.com**。

    如果您想要建立 A 記錄以根網域，可能會列出為 「**@**「 在您的註冊機構 DNS 工具] 中的符號。

4. 提供的欄位中輸入您的雲端服務的 IP 位址。 這將關聯雲端服務部署的 IP 位址的 A 記錄中使用的網域項目。

例如，下列記錄**contoso.com**所有流量應用都轉寄**137.135.70.239**，部署的應用程式的 IP 位址︰

| 主機名稱子網域 | IP 位址     |
| ------------------- | -------------- |
| @                   | 137.135.70.239 |


此範例會示範如何建立 A 記錄以根網域。 如果您想要建立萬用字元項目，包含所有的子網域，您可以輸入 「__*__' 為子網域。

>[AZURE.WARNING]
>Azure 中的 IP 位址是預設的動態。 您可能會想要使用[保留的 IP 位址](../virtual-network/virtual-networks-reserved-public-ip.md)，以確保您的 IP 位址不會變更。

## <a name="next-steps"></a>後續步驟

* [如何管理雲端服務](cloud-services-how-to-manage.md)
* [如何將自訂網域名稱對應 CDN 內容](../cdn/cdn-map-content-to-custom-domain.md)
* [一般設定您的雲端服務](cloud-services-how-to-configure-portal.md)。
* 瞭解如何[部署雲端服務](cloud-services-how-to-create-deploy-portal.md)。
* 設定[ssl 憑證](cloud-services-configure-ssl-certificate-portal.md)。

[Expose Your Application on a Custom Domain]: #access-app
[Add a CNAME Record for Your Custom Domain]: #add-cname
[Expose Your Data on a Custom Domain]: #access-data
[VIP swaps]: cloud-services-how-to-manage-portal.md#how-to-swap-deployments-to-promote-a-staged-deployment-to-production
[Create a CNAME record that associates the subdomain with the storage account]: #create-cname
[Azure 入口網站]: https://portal.azure.com
[vip]: ./media/cloud-services-custom-domain-name-portal/csvip.png
[csurl]: ./media/cloud-services-custom-domain-name-portal/csurl.png
 