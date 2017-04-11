<properties
   pageTitle="建立工作或學校的身分識別中 AAD |Microsoft Azure"
   description="瞭解如何建立與 Linux 虛擬機器搭配使用的 Azure Active Directory 中的公司或學校的身分識別。"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="squillace"
   manager="timlt"
   editor=""
   tags="azure-service-management,azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="08/23/2016"
   ms.author="rasquill"/>

# <a name="creating-a-work-or-school-identity-in-azure-active-directory-to-use-with-linux-vms"></a>若要使用 Linux Vm 的 Azure Active Directory 中建立的公司或學校的身分識別

如果您建立個人的 Azure 帳戶或個人版的 MSDN 訂閱及建立 Azure 帳戶利用 MSDN Azure 貸項總計，您會用來建立的*Microsoft 帳戶*身分識別。 許多很棒的功能的 Azure-[資源群組範本](../azure-resource-manager/resource-group-overview.md)是其中一個範例--需要搭配使用的公司或學校帳戶 （身分識別管理的 Azure Active Directory）。 您可以遵循下列指示來建立新的公司或學校帳戶，因為所幸，其中一個最佳的項目，瞭解您的個人 Azure 帳戶是它隨附預設 Azure Active Directory 網域時，若要建立新的公司或學校帳戶，您可以使用 Azure 功能需要它，您可以使用。

不過，最近的變更，即可管理您的訂閱任何類型的 Azure 帳戶使用`azure login`描述的互動式登入方法[以下](../xplat-cli-connect.md)。 您可以使用該機制，或您可以依照請依照下列指示進行。 您也可以[建立公司或學校與 Windows Vm 搭配使用的 Azure Active Directory 中的身分識別](virtual-machines-windows-create-aad-work-id.md)。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

[AZURE.INCLUDE [virtual-machines-common-create-aad-work-id](../../includes/virtual-machines-common-create-aad-work-id.md)]
