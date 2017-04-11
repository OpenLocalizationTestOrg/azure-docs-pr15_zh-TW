<properties
   pageTitle="匯入及匯出網域區域檔案使用 CLI Azure dns |Microsoft Azure"
   description="瞭解如何匯入及匯出 Azure DNS 使用 Azure CLI DNS 區域檔案"
   services="dns"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/16/2016"
   ms.author="sewhee"/>

# <a name="import-and-export-a-dns-zone-file-using-the-azure-cli"></a>匯入及匯出 Azure CLI DNS 區域檔案


本文會引導您執行匯入及匯出 DNS 區域檔案使用 Azure CLI Azure dns 的方式。

## <a name="introduction-to-dns-zone-migration"></a>DNS zone 移轉簡介

DNS 區域檔案已包含區域中的每個網域名稱系統 (DNS) 記錄的詳細資料的文字檔案。 看來標準的格式，使其適用於轉接 DNS 系統之間的 DNS 記錄。 使用區域檔案可快速，可靠，好方便您進或移出 Azure DNS 傳輸 DNS 區域。

Azure DNS 支援匯入及匯出區域檔案使用 Azure 的命令列介面 (CLI)。 Azure CLI 可跨平台的命令列工具，用來管理 Azure 服務。 使用 Windows、 Mac 和 Linux 平台，從[Azure 下載頁面上](https://azure.microsoft.com/downloads/)的。 跨平台支援是特別重要匯入及匯出區域檔案，因為最常見的名稱伺服器軟體，[繫結](https://www.isc.org/downloads/bind/)，通常是在 Linux 上執行。

## <a name="obtain-your-existing-dns-zone-file"></a>取得您現有的 DNS 區域檔案

DNS 區域檔案匯入 Azure DNS 之前，您必須以取得區域檔案的複本。 此檔案的來源，取決於 DNS 區域目前裝載於何處。

- 如果您的 DNS 區域裝載合作夥伴服務 （例如網域註冊機構、 專用的 DNS 主機服務提供者或替代雲端提供者），該服務應提供能夠下載 DNS 區域檔案。

- 如果您的 DNS 區域裝載在 Windows 的 DNS，區域檔案的預設資料夾是**%systemroot%\system32\dns**。 每個區域檔案的完整路徑也會顯示在 DNS 服務管理主控台中的 [**一般**] 索引標籤上。

- 如果使用繫結裝載您的 DNS 區域，繫結的設定檔**named.conf**中指定每個區域的區域檔案的位置。

**使用 GoDaddy 的區域檔案**<BR>
從 GoDaddy 下載的區域檔案有稍微非標準的格式。 您要修正這個問題，您將這些區域檔案匯入 Azure DNS 之前。 中的每個 DNS 記錄 RData DNS 名稱已指定為完整名稱，但是沒有終止 」。 」這表示它們會被解譯相對名稱為其他 DNS 系統。 您要編輯的區域檔案，新增 [終止 」。 「 成之前將 Azure DNS 匯入他們的名稱。

## <a name="import-a-dns-zone-file-into-azure-dns"></a>DNS 區域檔案匯入 Azure DNS


匯入的區域檔案會建立新的區域中 Azure DNS 如果不存在。 如果已經存在的區域，必須與現有記錄集合併區域檔案中的記錄集。

### <a name="merge-behavior"></a>合併行為

- 根據預設，會將合併用於現有及新記錄的設定。 合併記錄集內的相同記錄是取消重複項目。

- 或者，藉由指定`--force`選項，匯入程序將會取代現有的新記錄集記錄集。 沒有相對應的記錄設定匯入的區域檔案中的現有記錄集不會移除。

- 合併記錄集時，會使用存留時間 (TTL) 已經存在的記錄集。 當`--force`是使用，會使用 [TTL] 的新記錄的設定。

- 授權單位 (SOA) 參數的開頭 (除了`host`) 一律取自匯入的區域檔案，不論是否`--force`使用。 同樣地，在區域 apex 設定名稱伺服器記錄 TTL 一律會從匯入的區域檔案。

- 匯入的 CNAME 記錄將不會取代現有的 CNAME 記錄具有相同名稱除非`--force`指定參數。

- CNAME 記錄與另一筆記錄的相同名稱，但不同類型 （無論這現有或新） 之間的衝突時，會保留現有的記錄。 這是獨立的使用`--force`。

### <a name="additional-information-about-importing"></a>匯入的其他資訊

下列備忘稿提供其他相關區域的技術詳細資料匯入程序。

- `$TTL`指示詞，而且它受支援。 當沒有`$TTL`指示詞，不明確的 TTL 的記錄會匯入的設定為預設 TTL 為 3600 秒。 當兩筆記錄相同的記錄設定中指定不同的悄悄時，則會使用較低值。

- `$ORIGIN`指示詞，而且它受支援。 時`$ORIGIN`設定，用的預設值是依指定命令列上的區域名稱 (加上結束 「。 」)。

- `$INCLUDE`和`$GENERATE`指示詞不受支援。

- 支援這些記錄類型︰ A、 AAAA、 CNAME、 MX，NS、 SOA、 SRV，以及 TXT。

- 建立區域時，SOA 記錄會自動建立 Azure dns。 當您匯入的區域檔案時，所有 SOA 參數都來自區域檔案*除了*`host`參數。 這個參數會使用 Azure DNS 所提供的值。 這是因為此參數必須參照 Azure DNS 所提供的主要名稱伺服器。

- 在區域 apex 設定名稱伺服器記錄也會建立自動 Azure dns 時建立區域。 此記錄集的 TTL 已匯入。 這些記錄包含 Azure DNS 所提供的名稱伺服器名稱。 匯入的區域檔案中所包含的值，不會覆寫記錄資料。

- 在公用預覽，Azure DNS 支援單一字串 TXT 記錄。 將串連 multistring TXT 記錄，並將其被截斷為 255 個字元。

### <a name="cli-format-and-values"></a>CLI 格式和值


若要匯入 DNS 區域 Azure CLI 命令的格式為︰<BR>`azure network dns zone import [options] <resource group> <zone name> <zone file name>`

值︰

- `<resource group>`是在 Azure DNS 區域的 [資源] 群組的名稱。
- `<zone name>`是區域的名稱。
- `<zone file name>`為要匯入區域檔案路徑/名稱。

如果 [資源] 群組中的區域，此名稱不存在，它會為您建立。 如果區域已存在，會與現有記錄集合併匯入的記錄集。 若要覆寫現有記錄集，請使用`--force`選項。

若要確認的區域檔案格式，而不在其上匯入，請使用`--parse-only`選項。

### <a name="step-1-import-a-zone-file"></a>步驟 1。 匯入的區域檔案

匯入的區域檔案的區域**contoso.com**。

1. 使用 Azure CLI 登入您的 Azure 訂閱。

        azure login

2. 選取您要建立新的 DNS 區域的訂閱。

        azure account set <subscription name>

3. Azure DNS 是 Azure 資源管理員服務，Azure CLI 必須資源管理員模式之間切換。

        azure config mode arm

4. 您使用 Azure DNS 服務之前，您必須註冊您的訂閱，以使用 Microsoft.Network 資源提供者。 （此為每個訂閱的一次性作業）。

        azure provider register Microsoft.Network

5. 如果您沒有，您也需要建立資源管理員資源群組。

        azure group create myresourcegroup westeurope

6. 若要從匯入區域**contoso.com**檔案**contoso.com.txt**至新的 DNS 區域，在 [資源群組**myresourcegroup**中，執行命令`azure network dns zone import`。<BR>這個命令會載入區域檔案，和剖析它。 命令會執行一系列的命令來建立區域 Azure DNS 服務及所有記錄的區域設定。 命令也會報告進度主控台在視窗中，以及任何錯誤或警告。 因為數列建立記錄集，可能需要幾分鐘，才能匯入大型區域檔案。

        azure network dns zone import myresourcegroup contoso.com contoso.com.txt



### <a name="step-2-verify-the-zone"></a>步驟 2。 請確認該區域

若要匯入檔案之後，請確認 DNS 區域，您可以使用以下兩種方法其中一個︰

- 您可以使用下列 Azure CLI 命令列出記錄。

        azure network dns record-set list myresourcegroup contoso.com

- 您可以使用 PowerShell 指令程式來列出記錄`Get-AzureRmDnsRecordSet`。

- 您可以使用`nslookup`驗證記錄名稱解析。 因為還沒有委派的區域，您必須明確地指定正確的 Azure DNS 名稱伺服器。 下列範例會示範如何擷取指派至] 區域的名稱伺服器名稱。 IT 也會顯示如何使用查詢的 「 www 」 記錄`nslookup`。

        C:\>azure network dns record-set show myresourcegroup contoso.com @ NS
        info:Executing command network dns record-set show
        + Looking up the DNS Record Set "@" of type "NS"
        data:Id: /subscriptions/…/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/NS/@
        data:Name: @
        data:Type: Microsoft.Network/dnszones/NS
        data:Location: global
        data:TTL : 3600
        data:NS records
        data:Name server domain name : ns1-01.azure-dns.com
        data:Name server domain name : ns2-01.azure-dns.net
        data:Name server domain name : ns3-01.azure-dns.org
        data:Name server domain name : ns4-01.azure-dns.info
        data:
        info:network dns record-set show command OK

        C:\> nslookup www.contoso.com ns1-01.azure-dns.com

        Server: ns1-01.azure-dns.com
        Address:  40.90.4.1

        Name:www.contoso.com
        Addresses:  134.170.185.46
        134.170.188.221

### <a name="step-3-update-dns-delegation"></a>步驟 3。 更新 DNS 委派

您已驗證的區域已匯入正確之後，您需要更新 DNS 委派指向 Azure DNS 名稱伺服器。 如需詳細資訊，請參閱[更新 DNS 委派](dns-domain-delegation.md)的文件。

## <a name="export-a-dns-zone-file-from-azure-dns"></a>從 Azure DNS 匯出 DNS 區域檔案

若要匯入 DNS 區域 Azure CLI 命令的格式為︰<BR>`azure network dns zone export [options] <resource group> <zone name> <zone file name>`

值︰

- `<resource group>`是在 Azure DNS 區域的 [資源] 群組的名稱。
- `<zone name>`是區域的名稱。
- `<zone file name>`是要匯出的區域檔案路徑/名稱。

為區域匯入]，與您第一次需要登入，選擇您的訂閱，並設定 Azure CLI 使用資源管理員模式。

### <a name="to-export-a-zone-file"></a>若要匯出的區域檔案


1. 使用 Azure CLI 登入您的 Azure 訂閱。

        azure login

2. 選取您要建立新的 DNS 區域的訂閱。

        azure account set <subscription name>

3. Azure DNS 是 Azure 資源管理員專用服務。 Azure CLI 必須切換為資源管理員模式。

        azure config mode arm

4. 若要將現有 Azure DNS zone **contoso.com**資源群組**myresourcegroup**中匯出檔案**contoso.com.txt** （在目前的資料夾），請執行`azure network dns zone export`。 這個命令會呼叫列舉區域中的記錄集，並將結果匯出為相容於繫結的區域檔案 Azure DNS 服務。

        azure network dns zone export myresourcegroup contoso.com contoso.com.txt

