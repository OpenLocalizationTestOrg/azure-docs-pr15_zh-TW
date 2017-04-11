<properties
   pageTitle="Azure 入口網站中建立 VM FQDN |Microsoft Azure"
   description="瞭解如何建立完整網域名稱或 FQDN 資源管理員的虛擬機器中 Azure 入口網站。"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="08/23/2016"
   ms.author="iainfou"/>

# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal"></a>建立在 Azure 入口網站的完整網域名稱
當您使用的資源管理員部署模型[Azure 入口網站](https://portal.azure.com)中建立虛擬機器 (VM) 時，會自動建立虛擬機器的公用 IP 資源。 您可以使用這個 IP 位址從遠端存取 VM。 雖然入口網站不會建立的[完整網域名稱](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)，或 FQDN，根據預設，您可以新增其中一個，一旦建立 VM。 本文將示範步驟來建立 DNS 名稱或 FQDN。

[AZURE.INCLUDE [virtual-machines-common-portal-create-fqdn](../../includes/virtual-machines-common-portal-create-fqdn.md)]

您現在可以連線遠端使用這個 DNS 名稱例如 vm `ssh adminuser@testdnslabel.centralus.cloudapp.azure.com`。

## <a name="next-steps"></a>後續步驟
現在您 VM 有的公用 IP 和 DNS 的名稱，您可以部署一般應用程式架構] 或 [服務 nginx MongoDB，Docker，例如等。

您也可以閱讀更多關於[使用資源管理員](../azure-resource-manager/resource-group-overview.md)建置 Azure 部署的秘訣。