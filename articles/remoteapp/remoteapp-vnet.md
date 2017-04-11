
<properties
    pageTitle="驗證使用 Azure RemoteApp Azure VNET |Microsoft Azure"
    description="瞭解如何以確定您 Azure VNET 已準備好使用 Azure RemoteApp"
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



# <a name="validate-the-azure-vnet-to-use-with-azure-remoteapp"></a>驗證 Azure VNET 與 Azure RemoteApp 搭配使用

> [AZURE.IMPORTANT]
> Azure RemoteApp 會被停用。 閱讀[公告](https://go.microsoft.com/fwlink/?linkid=821148)如需詳細資訊。

Azure VNET 使用 Azure RemoteApp 之前，您可能會想要驗證 VNET。 這可協助避免發生問題的連線。

若要驗證您的 Azure VNET，執行下列動作︰

1. 建立您想要使用 Azure RemoteApp Azure VNET 的子網路內 Azure 虛擬機器。

2. 連線到該 VM 管理入口網站中使用 [**連線**] 選項。
3. 加入虛擬機器至您想要使用 Azure RemoteApp 相同的網域。 如果您要建立連線到您的內部網路的混合式集合，加入虛擬機器至您的本機網域。

如果這是成功，Azure VNET 可使用 RemoteApp。

如需端對端混合式集合工作流程的詳細資訊，請參閱下列文章︰

- [如何規劃 Azure RemoteApp 虛擬網路](remoteapp-planvnet.md)
- [建立混合式集合](remoteapp-create-hybrid-deployment.md)
- [Azure RemoteApp 集合部署至 Azure 虛擬網路 （含 ExpressRoute 支援）](http://blogs.msdn.com/b/rds/archive/2015/04/23/deploy-azure-remoteapp-collection-to-your-azure-virtual-network-with-support-for-expressroute.aspx)
