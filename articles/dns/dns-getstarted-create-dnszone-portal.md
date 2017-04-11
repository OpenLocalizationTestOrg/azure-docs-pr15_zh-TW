<properties
   pageTitle="如何建立及管理 DNS 區域 Azure 入口網站中的 |Microsoft Azure"
   description="瞭解如何建立 DNS 區域 Azure dns。 這是建立和管理您的第一個 DNS 並開始裝載您的 DNS 網域使用 Azure 入口網站的逐步指南。"
   services="dns"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/16/2016"
   ms.author="sewhee"/>

# <a name="create-a-dns-zone-in-the-azure-portal"></a>Azure 入口網站中建立 DNS 區域


> [AZURE.SELECTOR]
- [Azure 入口網站](dns-getstarted-create-dnszone-portal.md)
- [PowerShell](dns-getstarted-create-dnszone.md)
- [Azure CLI](dns-getstarted-create-dnszone-cli.md)



本文會引導您完成建立 DNS 區域使用 Azure 入口網站的步驟。 您也可以建立使用 PowerShell 或 CLI DNS 區域。

[AZURE.INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]


### <a name="about-tags-for-azure-dns"></a>如需 Azure dns 的標籤


標記的清單名稱 / 值組與使用由 Azure 資源管理員標籤資源的帳單或群組的用途。 如需標記的詳細資訊，請參閱[使用標籤以組織 Azure 資源](../resource-group-using-tags.md)的文件。

您可以使用您的 DNS 區域的**設定**刀 Azure 入口網站中新增標籤。


## <a name="create-a-dns-zone"></a>建立 DNS 區域

1. 登入 Azure 入口網站

2. 按一下中心] 功能表上，按一下**新增 > 網路 >** ，然後按一下以開啟 [DNS zone 刀**DNS 區域**。

    ![DNS 區域](./media/dns-getstarted-create-dnszone-portal/openzone650.png)

3. 在 [ **DNS 區域**刀中，按一下 [底部的 [**建立**]。 這會開啟**建立 DNS 區域**刀。

    ![建立區域](./media/dns-getstarted-create-dnszone-portal/newzone250.png)

4. 在**建立 DNS 區域**刀，命名您的 DNS 區域。 例如， *contoso.com*。 請參閱上述一節中[瞭解 DNS 區域名稱](#names)。

5. 接下來，指定您想要使用的 [資源] 群組。 您可以建立新的 [資源] 群組中，或選取 [已經存在的。

6. 從 [**位置**] 下拉式清單中，指定 [資源] 群組的位置。 請注意，這項設定指的是 [資源] 群組中，不是 DNS 區域的位置的位置。 實際的 DNS 區域資源自動 「 全域管理員 」，而不是您可以 （或需要） 指定在入口網站。

7. 您可以將如果您想要輕鬆找出您新增的區域，您的儀表板上選取 [**固定至儀表板**核取方塊。 然後按一下 [**建立**。

    ![釘選至儀表板](./media/dns-getstarted-create-dnszone-portal/pindashboard150.png)

8. 按一下 [建立後，您會看到您正在設定儀表板上的新區域。

    ![建立](./media/dns-getstarted-create-dnszone-portal/creating150.png)

9. 已建立新的區域，就會開啟您的新區域刀儀表板上。


## <a name="view-records"></a>檢視記錄

建立 DNS 區域時，也會建立下列記錄︰

- 「 開始的授權 」 (SOA) 記錄。 SOA 則會出現在每個 DNS 區域的根目錄。
- 授權的名稱伺服器 (NS) 記錄。 這些顯示的名稱伺服器所裝載的區域。 Azure DNS 使用集區的名稱伺服器，以及不同的名稱伺服器可能會指派給 Azure DNS 中的不同區域。 如需詳細資訊，請參閱[代理人 Azure dns 網域](dns-domain-delegation.md)。

您可以檢視從 Azure 入口網站的記錄

1. 從您的**DNS 區域**刀，按一下以開啟 [**設定刀**DNS 區域的**所有設定**。

    ![區域](./media/dns-getstarted-create-dnszone-portal/viewzonens500.png)


2. 在基本功能窗格] 的下方，您可以查看設定 DNS 區域中的記錄。


    ![區域](./media/dns-getstarted-create-dnszone-portal/viewzone500.png)

## <a name="test"></a>測試

您可以使用 DNS 工具，例如 nslookup、 挖掘或[解決 DnsName PowerShell cmdlet](https://technet.microsoft.com/library/jj590781.aspx)來測試您的 DNS 區域。

如果您還沒有委派您的網域以使用新的 Azure DNS 區域，您必須將 DNS 查詢直接到其中一個名稱伺服器，您的區域。 在所列的 NS 記錄，指定您區域的名稱伺服器`Get-AzureRmDnsRecordSet`上方。 請確定替代正確的值，您的區域將以下的指令。

    nslookup
    > set type=SOA
    > server ns1-01.azure-dns.com
    > contoso.com

    Server: ns1-01.azure-dns.com
    Address:  208.76.47.1

    contoso.com
            primary name server = ns1-01.azure-dns.com
            responsible mail addr = msnhst.microsoft.com
            serial  = 1
            refresh = 900 (15 mins)
            retry   = 300 (5 mins)
            expire  = 604800 (7 days)
            default TTL = 300 (5 mins)



## <a name="delete-a-dns-zone"></a>刪除 DNS 區域

您可以直接從入口網站中刪除 DNS 區域。 在刪除之前 Azure DNS 區域，您會需要刪除所有記錄集，除了 NS 和 SOA 區域根目錄的記錄建立區域時自動建立。

1. 找出您想要刪除的區域**DNS 區域**刀，然後按一下 [刀頂端的 [**刪除**]。

2. 會出現訊息，讓您知道您必須先刪除所有記錄集，除了 NS 和 SOA 自動建立的記錄。 如果您已刪除您記錄集，請按一下 [**是**]。 請注意，從入口網站中刪除 DNS 區域時，不會刪除相關聯的 DNS 區域的 [資源] 群組。


## <a name="next-steps"></a>後續步驟

建立 DNS 區域之後, 建立[記錄集和記錄](dns-getstarted-create-recordset-portal.md)啟動解決您的網際網路網域的名稱。
