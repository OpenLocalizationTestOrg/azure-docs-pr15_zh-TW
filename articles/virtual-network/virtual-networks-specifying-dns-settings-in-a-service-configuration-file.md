<properties 
   pageTitle="指定 [服務設定檔中的 [DNS 設定 |Microsoft Azure"
   description="指定用於虛擬網路的服務設定檔的自訂 DNS 設定"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/24/2016"
   ms.author="jdial" />

# <a name="specifying-dns-settings-in-a-service-configuration-file"></a>指定 [服務設定檔中的 [DNS 設定

## <a name="dns-elements"></a>DNS 項目

服務設定檔案可能包含 DnsServers 項目清單的服務會使用網域名稱系統 (DNS) 伺服器 IPv4 位址。 服務設定檔中的設定優先於網路設定檔中的設定。 如需詳細資訊，請參閱[Azure 服務設定結構描述 (.cscfg 檔案)](https://msdn.microsoft.com/library/azure/ee758710.aspx)。

**NetworkConfiguration 項目**

      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>

>[AZURE.WARNING] **DnsServer**項目中的**名稱**屬性只作為參照的名稱。 它不代表為 DNS 伺服器主機名稱。 在整個 Microsoft Azure 訂閱，每個**DnsServer**屬性值必須是唯一的。

## <a name="see-also"></a>另請參閱

[Azure 服務設定結構描述 (.cscfg)](https://msdn.microsoft.com/library/windowsazure/ee758710)

[Azure 虛擬網路組態結構描述](http://go.microsoft.com/fwlink/?LinkId=248093)

[設定使用網路設定檔的虛擬網路](http://go.microsoft.com/fwlink/?LinkId=248094)

[在 [管理] 入口網站中的虛擬網路設定相關](http://go.microsoft.com/fwlink/?LinkId=248092)

