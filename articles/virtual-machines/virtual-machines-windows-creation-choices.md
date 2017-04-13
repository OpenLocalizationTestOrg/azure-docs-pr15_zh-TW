<properties
    pageTitle="若要建立 Windows VM 的不同方式 |Microsoft Azure"
    description="列出的不同方式來建立 Windows 虛擬機器與資源管理員。"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows"
    ms.workload="infrastructure-services"
    ms.date="09/27/2016"
    ms.author="cynthn"/>

# <a name="different-ways-to-create-a-windows-virtual-machine-with-resource-manager"></a>若要建立 Windows 虛擬機器與資源管理員不同的方式

Azure 提供不同的方式建立虛擬機器，因為虛擬機器很適合不同的使用者和用途。 這表示您需要一些選擇虛擬機器及如何建立。 本文可讓您的摘要，這些選項和連結的指示。

## <a name="azure-portal"></a>Azure 入口網站

使用 Azure 入口網站是要嘗試虛擬機器，一個簡單的方法，尤其是如果您剛開始使用 Azure。 

[建立虛擬機器執行 Windows 使用入口網站](virtual-machines-windows-hero-tutorial.md)

## <a name="template"></a>範本

虛擬機器需要資源的組合 (例如可用性集和儲存的帳戶)。 而不是部署及管理各個資源分開，您可以建立的部署和佈建新的所有資源在單一、 協調作業 Azure 資源管理員範本。

- [資源管理員從範本建立 Windows 虛擬機器](virtual-machines-windows-ps-template.md)


## <a name="azure-powershell"></a>Azure PowerShell

如果您偏好使用的命令殼層，您可以使用 PowerShell 的 Azure。

- [建立使用 PowerShell 的 Windows VM](virtual-machines-windows-ps-create.md)


## <a name="visual-studio"></a>Visual Studio

使用 Visual Studio 建立、 管理及 Visual Studio 和 Azure SDK 部署 Vm Azure 工具。

[Visual Studio azure 工具](https://www.visualstudio.com/features/azure-tools-vs)

