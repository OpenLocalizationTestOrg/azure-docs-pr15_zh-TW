
<properties
    pageTitle="清單的連接埠和 Url whitelist 的 Azure RemoteApp 部署客戶虛擬網路中 |Microsoft Azure"
    description="了解哪些連接埠和 Url 您將需要透過 Azure RemoteApp 通訊設定。"
    services="remoteapp"
    documentationCenter=""
    authors="mghosh1616"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/16/2016"
    ms.author="elizapo" />



# <a name="list-of-ports-and-urls-to-permit-access-for-azure-remoteapp-deployed-in-customer-virtual-network"></a>允許的 Azure RemoteApp 部署的 access 客戶虛擬網路中的 [連接埠和 Url 的清單 

> [AZURE.IMPORTANT]
> Azure RemoteApp 會被停用。 閱讀[公告](https://go.microsoft.com/fwlink/?linkid=821148)如需詳細資訊。

下列適用於 Azure RemoteApp 雲端或混合式集合如果您將其部署虛擬網路 (VNET)。 如需有關虛擬網路的詳細資訊，請閱讀[虛擬網路概觀](../virtual-network/virtual-networks-overview.md)。 如果您已建立網路安全性群組 (NSG) 限制流量您為 Azure RemoteApp 選擇的虛擬網路資源，請確定下列可存取和使用允許透過虛擬網路上的安全性原則。 如需有關網路安全性群組的詳細資訊，請閱讀[網路安全性群組是什麼？(NSG)](../virtual-network/virtual-networks-nsg.md)。

##  <a name="azure-remoteapp-subnet-needs-access-to-these-endpoints-and-urls"></a>Azure RemoteApp 子網路需要存取這些結束點和 Url: 
*   *。 servicebus.windows.net
*    *。 servicebus.net
*    https://*.remoteapp.windwsazure.com  
*    https://www.remoteapp.windowsazure.com 
*    https://*remoteapp.windowsazure.com  
*    https://*.core.windows.net  
*    輸出︰ TCP: 443 TCP: 10101 10175 
*    可省略 – UDP: 10201 10275  
 
## <a name="azure-remoteapp-clients-need-access-to-these-endpoints-and-urls"></a>Azure RemoteApp 用戶端需要存取這些結束點和 Url: 

用戶端我表示桌上型電腦裝置可供使用連線至部署 Azure RemoteApp 集合中的應用程式的等等。

-  https://telemetry.remoteapp.windowsazure.com  
-  https://*.remoteapp.windowsazure.com （可省略 UDP 連接埠是這個地址） 
-  https://login.windows.net  
-  https://login.microsoftonline.com  
-  https://www.remoteapp.windowsazure.com 
-  https://*.core.windows.net  
-  輸出︰ TCP: 443  
-  選用-UDP: 3391 