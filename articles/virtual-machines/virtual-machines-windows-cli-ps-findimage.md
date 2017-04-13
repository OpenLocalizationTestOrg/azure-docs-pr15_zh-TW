<properties
   pageTitle="瀏覽並選取 [Windows VM 圖像 |Microsoft Azure"
   description="瞭解如何決定 publisher、 提供的服務，以及 SKU 的圖像時建立 Windows 虛擬機器資源管理員部署模型。"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="squillace"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"
   />

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure"
   ms.date="08/23/2016"
   ms.author="rasquill"/>

# <a name="navigate-and-select-windows-virtual-machine-images-in-azure-with-powershell-or-the-cli"></a>瀏覽並選取 [使用 PowerShell 或 CLI Azure 中的 [Windows 虛擬機器圖像

本主題說明如何尋找 VM 圖像發行者、 優惠、 sku 及版本可能會在部署每個位置。 若要提供範例，一些常用的 Windows VM 圖像是︰

## <a name="table-of-commonly-used-windows-images"></a>常用的 Windows 圖像的表格


| PublisherName                        | 優惠                                 | Sku                         |
|:---------------------------------|:-------------------------------------------|:---------------------------------|:--------------------|
| MicrosoftDynamicsNAV             | DynamicsNAV                                | 2015                             |
| MicrosoftSharePoint              | MicrosoftSharePointServer                  | 2013                             |
| MicrosoftSQLServer               | SQL2014 WS2012R2                           | 企業-最佳化-為-DW      |
| MicrosoftSQLServer               | SQL2014 WS2012R2                           | 企業-最佳化-為-OLTP    |
| MicrosoftWindowsServer           | WindowsServer                              | 2012 R2-資料中心                  |
| MicrosoftWindowsServer           | WindowsServer                              | 2012 資料中心               |
| MicrosoftWindowsServer           | WindowsServer                              | 2008 R2 SP1 |
| MicrosoftWindowsServer           | WindowsServer                              | Windows Server-Technical Preview |
| MicrosoftWindowsServerEssentials | WindowsServerEssentials                    | WindowsServerEssentials          |
| MicrosoftWindowsServerHPCPack    | WindowsServerHPCPack                       | 2012R2                           |


[AZURE.INCLUDE [virtual-machines-common-cli-ps-findimage](../../includes/virtual-machines-common-cli-ps-findimage.md)]
