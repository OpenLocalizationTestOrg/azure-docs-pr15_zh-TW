<properties
   pageTitle="啟用或停用 Azure VM 監控"
   description="說明如何啟用或停用 Azure VM 監控"
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="kmouss"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="02/08/2016"
   ms.author="kmouss"/>
   
# <a name="enable-or-disable-azure-vm-monitoring"></a>啟用或停用 Azure VM 監控

本節說明如何啟用或停用監控執行 Azure 虛擬機器上。 依預設監視已啟用 Azure 虛擬機器上如果部署從[Azure 入口網站](https://portal.azure.com)，監控圖形所提供的預設 1 分鐘。 您可以啟用或停用監視使用入口網站或 Azure 命令列介面 Mac 版、 Linux，以及 Windows (Azure CLI)。 

## <a name="enable--disable-monitoring-through-the-azure-portal"></a>啟用/停用透過 Azure 入口網站的監視
 
您可以啟用監視您 Azure VM，在 1 一刻期間提供您的執行個體的相關資料。 （套用儲存變更）。 然後使用 vm 在入口網站的圖形，或透過 API 診斷的詳細的資料。 根據預設，Azure 入口網站可讓您可以監視，但您可以將其關閉如下所述。 您可以啟用監控 VM 正在執行，或在停止狀態時。

- 開啟[https://portal.azure.com](https://portal.azure.com) Azure**在入口網站**

- 在左側導覽畫面中，按一下 [虛擬機器]。

- 在清單虛擬機器中，選取 [執行中或停止執行個體。 虛擬機器 blad 隨即會開啟。

- 按一下 [所有設定]。

- 按一下 [診斷]。

- 若要開啟或關閉，請變更狀態。 您也可以挑選此刀中的層級的監控您想要啟用虛擬機器的詳細資料。

[Azure.Note] 診斷上切換是預設值，當您建立新的虛擬機器

![啟用/停用透過 Azure 入口網站的監視。][1]


## <a name="enable--disable-monitoring-with-azure-cli"></a>啟用/停用與 Azure CLI 監控
 
若要啟用監視 Azure VM。

- 建立一個名為 PrivateConfig.json 例如，下列內容檔案。
        {「 storageAccountName 」: 「 接收資料儲存區帳戶 」，「 storageAccountKey 」: 「 的帳戶金鑰 」}
- 執行下列 Azure CLI 命令。

        azure vm extension set myvm LinuxDiagnostic Microsoft.OSTCExtensions 2.0 --private-config-path PrivateConfig.json

[Azure.Note] 您可以變更從 2.0 版本時提供的新版本。 

如更多關於設定的詳細資料，監控指標和範例，請造訪文件-* *[使用 Linux 診斷副檔名監視器 Linux VM 的效能和診斷資料](virtual-machines-linux-classic-diagnostic-extension.md)。

<!--Image references-->
[1]: ./media/virtual-machines-linux-vm-monitoring/portal-enable-disable.png
 

