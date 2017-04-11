
<properties
    pageTitle="縮放資訊中 Azure RemoteApp VNET |Microsoft Azure"
    description="瞭解執行 VNET Azure RemoteApp 的 IP 位址需求"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />



# <a name="sizing-information-for-a-vnet-in-azure-remoteapp"></a>縮放 VNET Azure RemoteApp 中的資訊

> [AZURE.IMPORTANT]
> Azure RemoteApp 會被停用。 閱讀[公告](https://go.microsoft.com/fwlink/?linkid=821148)如需詳細資訊。

當您使用 Azure RemoteApp 虛擬網路 (VNET) 時，RemoteApp 會使用子網路中的 IP 位址。 根據您 RemoteApp 服務的小數位數，必須先確定您子網路有足夠的可用 RemoteApp 虛擬機器的 IP 位址。 時本縮放指南並非完美指定如何 RemoteApp 動態微調向上及向下集合內的虛擬機器尾端旋轉，就能協助您估計您子網路的範圍。 這是因為後 RemoteApp 服務位於 VNET，您無法增加子網路大小，而不移除 RemoteApp 特別重要。

您想要執行最大容量每個 RemoteApp 集合，您必須有可用的 100 IP 位址。 例如，如果您在標準的計劃中有一個 RemoteApp 集合，而您想要讓最大的 500 使用者，您應該集合的 100 的 IP 位址。 同樣地，您需要 100 的 IP 位址 RemoteApp 集合在基本計劃中有 800 使用者。 如果您打算有較少的使用者 （小於最大值），您可以減少需要每個集合的 IP 位址。 最小的子網路大小需求是 30 IP 位址 （/ 27）。

請查看下列資訊，請確定您 VNET 是設定，而且運作卻︰

- [將個人的 VNET 移轉到 Azure VNET](remoteapp-migratevnet.md)
- [驗證 Azure VNET 與 Azure RemoteApp 搭配使用](remoteapp-vnet.md)
