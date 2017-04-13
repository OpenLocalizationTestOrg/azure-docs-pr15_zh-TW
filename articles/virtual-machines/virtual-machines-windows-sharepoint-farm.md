<properties
    pageTitle="建立 SharePoint 伺服器陣列 |Microsoft Azure"
    description="快速建立新的 SharePoint 2013 或 SharePoint 2016 伺服器陣列中 Azure。"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="JoeDavies-MSFT"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/30/2016"
    ms.author="josephd"/>

# <a name="create-sharepoint-server-farms"></a>建立 SharePoint 伺服器陣列

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]傳統的模型。

## <a name="sharepoint-2013-farms"></a>SharePoint 2013 伺服器陣列

使用 Microsoft Azure 入口網站市集]，您可以快速建立預先設定的 SharePoint Server 2013 伺服器陣列。 這可以您節省大量時間時所需的裝置/測試環境基本] 或 [高可用性的 SharePoint 伺服器陣列，或如果您正在評估 SharePoint Server 2013 的共同作業解決方案為您的組織。

> [AZURE.NOTE] 已移除**SharePoint 伺服器陣列**中的之項目的 Azure Marketplace Azure 入口網站。 已被取代的**SharePoint 2013 非 HA 陣列**與**SharePoint 2013 HA 伺服器陣列**的項目。

基本的 SharePoint 伺服器陣列包含在此組態中的三個虛擬機器。

![sharepointfarm](./media/virtual-machines-windows-sharepoint-farm/Non-HAFarm.png)

您可以使用此伺服器陣列設定簡化的安裝 SharePoint 應用程式開發或 SharePoint 2013 的第一次評估。

若要建立基本 （三個伺服器） SharePoint 伺服器陣列︰

1. 按一下[這裡](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-nonha/)。
2. 按一下 [**部署**]。
3. 在**SharePoint 2013 非 HA 陣列**] 窗格中，按一下 [**建立**]。
4. 指定設定上的 [**建立 SharePoint 2013 非 HA 陣列**窗格] 的步驟，然後按一下 [**建立**。

在此設定的九個虛擬機器包含高可用性 SharePoint 伺服器陣列。

![sharepointfarm](./media/virtual-machines-windows-sharepoint-farm/HAFarm.png)

若要測試的 SharePoint 伺服器陣列的較高的用戶端載入、 外部的 SharePoint 網站的可用性和 SQL Server AlwaysOn 可用性群組，您可以使用此伺服器陣列設定。 您也可以使用此設定高可用性環境中的 SharePoint 應用程式開發。

若要建立高可用性 (九 server) SharePoint 伺服器陣列︰

1. 按一下[這裡](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-ha/)。
2. 按一下 [**部署**]。
3. 在**SharePoint 2013 HA 伺服器陣列**] 窗格中，按一下 [**建立**]。
4. 指定設定上的 [**建立 SharePoint 2013 HA 伺服器陣列**] 窗格中，7 個步驟，然後按一下 [**建立**。

> [AZURE.NOTE] 包含 Azure 免費試用版，則無法建立**SharePoint 2013 非 HA 陣列**或**SharePoint 2013 HA 伺服器陣列**。

Azure 入口網站建立兩個這些伺服器陣列的具網際網路 web 目前狀態的雲端專用虛擬網路中。 沒有網站-VPN 或 ExpressRoute 連線至您組織的網路。

> [AZURE.NOTE] 當您建立基本或高可用性 SharePoint 伺服器陣列使用 Azure 入口網站，您無法指定現有的資源群組。 若要解決此限制，請使用 PowerShell 的 Azure 建立這些伺服器陣列。 如需詳細資訊，請參閱[使用 PowerShell 的 Azure 建立 SharePoint 2013 開發/測試伺服器陣列](https://technet.microsoft.com/library/mt743093.aspx#powershell)。

## <a name="sharepoint-2016-farms"></a>SharePoint 2016 伺服器陣列

請參閱[本文](https://technet.microsoft.com/library/mt723354.aspx)的指示建立下列單一伺服器 SharePoint Server 2016 伺服器陣列。

![sharepointfarm](./media/virtual-machines-windows-sharepoint-farm/SP2016Farm.png)

## <a name="managing-the-sharepoint-farms"></a>管理 SharePoint 伺服器陣列

您可以管理這些透過遠端桌面連線的伺服器陣列的伺服器。 如需詳細資訊，請參閱[登入虛擬機器](virtual-machines-windows-hero-tutorial.md#log-on-to-the-virtual-machine)。

從中央管理 SharePoint 網站上，您可以設定 「 我的網站、 SharePoint 應用程式，以及其他功能。 如需詳細資訊，請參閱[設定 SharePoint](http://technet.microsoft.com/library/ee836142.aspx)。

## <a name="next-steps"></a>後續步驟

- 探索 Azure 基礎結構服務中的其他[SharePoint 設定](https://technet.microsoft.com/library/dn635309.aspx)。
