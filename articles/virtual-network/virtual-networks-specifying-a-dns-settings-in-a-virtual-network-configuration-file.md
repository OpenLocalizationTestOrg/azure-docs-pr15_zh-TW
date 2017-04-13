<properties 
   pageTitle="指定虛擬網路設定檔中的 [DNS 設定 |Microsoft Azure"
   description="如何使用傳統的部署模型中的虛擬網路設定檔的虛擬網路變更 DNS 伺服器設定"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" 
   tags="azure-service-management" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/23/2016"
   ms.author="jdial" /> 


# <a name="specifying-dns-settings-in-a-virtual-network-configuration-file"></a>指定虛擬網路設定檔中的 [DNS 設定

網路設定檔有兩個項目，指定網域名稱系統 (DNS) 設定，您可以使用︰ **DnsServers**和**DnsServerRef**。 您可以新增 DNS 伺服器的清單指定其 IP 位址和參考**DnsServers**項目名稱。 然後可用**DnsServerRef**項目來指定不同的網路網站虛擬網路中使用哪一個 DNS 伺服器的項目從 DnsServers 的項目。

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]本文涵蓋傳統部署模型。

網路設定檔可能會包含下列項目。 標題的每個項目已連結至提供的項目值設定的其他資訊的頁面。

>[AZURE.IMPORTANT] 如需如何設定網路設定檔資訊，請參閱[設定虛擬網路使用網路的設定檔](virtual-networks-using-network-configuration-file.md)。 有關網路設定檔中所包含的每個項目，請參閱[Azure 虛擬網路設定結構描述](https://msdn.microsoft.com/library/azure/jj157100.aspx)。

[Dns 項目](http://go.microsoft.com/fwlink/?LinkId=248093)

    <Dns>
      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>
    </Dns>

>[AZURE.WARNING] **DnsServer**項目中的**名稱**屬性用於**DnsServerRef**項目只能作為參照。 它不代表為 DNS 伺服器主機名稱。 每個**DnsServer**屬性值必須是唯一的整個 Microsoft Azure 訂閱

[虛擬網路網站元素](http://go.microsoft.com/fwlink/?LinkId=248093)

    <DnsServersRef>
      <DnsServerRef name="ID1" />
      <DnsServerRef name="ID2" />
      <DnsServerRef name="ID3" />
    </DnsServersRef>

>[AZURE.NOTE] 如果要指定虛擬網路網站元素的這項設定，它必須先前定義的 DNS 項目中。 虛擬網路網站元素中 DnsServerRef*名稱*必須參照 DnsServer*名稱*DNS 項目中指定名稱] 的值。

## <a name="next-steps"></a>後續步驟

- 了解[Azure 虛擬網路組態結構描述](http://go.microsoft.com/fwlink/?LinkId=248093)。
- 了解[Azure 服務設定結構描述](https://msdn.microsoft.com/library/windowsazure/ee758710)。
- [設定虛擬網路使用網路設定檔](virtual-networks-using-network-configuration-file.md)。
