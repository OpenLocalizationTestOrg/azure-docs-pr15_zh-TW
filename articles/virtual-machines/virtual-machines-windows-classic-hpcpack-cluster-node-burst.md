<properties
 pageTitle="為 HPC 套件叢集新增尖峰節點 |Microsoft Azure"
 description="瞭解如何新增雲端服務中執行的工作角色執行個體以展開 HPC 套件中的叢集點播 Azure"
 services="virtual-machines-windows"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="10/14/2016"
 ms.author="danlep"/>

# <a name="add-on-demand-burst-nodes-to-an-hpc-pack-cluster-in-azure"></a>為 HPC 套件叢集 Azure 中新增視 「 突發 」 節點



如果您設定[Microsoft HPC 套件](https://technet.microsoft.com/library/cc514029)叢集 Azure 中時，您可以快速縮放叢集容量向上或向下，而不維護預先設定的運算節點 Vm 一組的方式。 本文將示範如何新增視 」 尖峰 」 的節點 （工作者角色執行個體雲端服務中執行） 做為標頭節點 Azure 中的計算資源。 

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

![尖峰節點][burst]

本文中的步驟可協助您快速新增至雲端 HPC 套件標頭節點 VM 測試或的概念部署的 Azure 節點。 高層級的步驟是 「 傳送至 Azure 」 的步驟相同新增雲端計算為內部部署 HPC 套件叢集的容量。 教學課程，請參閱[設定混合式運算叢集與 Microsoft HPC 套件](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)。 詳細的指導方針及生產部署的考量，請參閱[傳送至 Microsoft HPC pack Azure](https://technet.microsoft.com/library/gg481749.aspx)。


## <a name="prerequisites"></a>必要條件

* **Azure VM 部署 HPC 套件標頭節點**-您可以使用獨立的標頭節點 VM 或另一個則是較大的叢集的一部分。 若要建立獨立的標頭節點，請參閱[部署 Azure VM HPC 套件不對節點](virtual-machines-windows-hpcpack-cluster-headnode.md)。 自動化 HPC 套件叢集部署選項，請參閱[建立及管理 Windows HPC 叢集 Microsoft HPC pack Azure 中的選項](virtual-machines-windows-hpcpack-cluster-options.md)。

    >[AZURE.TIP] 如果您使用[HPC 套件 IaaS 部署指令碼](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md)來建立叢集 Azure 中時，您就可以自動部署中包含 Azure 突發節點。 請參閱在文件中的範例。

* **Azure 訂閱**-新增 Azure 節點，您可以選擇同一份訂閱使用部署主節點 VM，或不同的訂閱 （或訂閱）。

* **核心配額**-您可能需要增加的配額的核心，尤其是如果您選擇部署多核心大小的數個 Azure 節點。 若要增加配額] 中，[開啟線上客戶支援要求](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)免費。

## <a name="step-1-create-a-cloud-service-and-a-storage-account-for-the-azure-nodes"></a>步驟 1︰ 建立雲端服務和 Azure 節點儲存帳戶

使用 [Azure 傳統入口網站] 或 [對等工具來設定部署您 Azure 節點所需的下列資源︰

* 在新的 Azure 雲端服務
* 新的 Azure 儲存體帳戶

>[AZURE.NOTE] 不重複使用現有的雲端服務中您的訂閱。 

**考量**

* 設定每個 Azure 節點範本您計劃要建立另一個雲端的服務。 不過，您可以使用相同的儲存空間帳戶的多個節點範本。

* 我們建議您在相同的 Azure 區域中尋找雲端服務，以供部署儲存帳戶。




## <a name="step-2-configure-an-azure-management-certificate"></a>步驟 2︰ 設定 Azure 管理憑證

若要新增 Azure 節點做為計算的資源，您需要在主節點及上傳的項目，對應到 Azure 部署所用的訂閱憑證管理憑證。

此案例中，您可以選擇**預設 HPC Azure 管理憑證**的 HPC 套件安裝並設定自動上的標頭的節點。 這個憑證以進行測試用途且的概念部署適合。 若要使用這個憑證上, 傳檔案 C:\Program Files\Microsoft HPC 套件 2012\Bin\hpccert.cer 從主節點 VM 訂閱。 若要上傳[Azure 傳統入口網站](https://manage.windowsazure.com)中的憑證，請按一下 [**設定** > **管理憑證**。

若要設定管理憑證的其他選項，請參閱[設定 Azure 尖峰部署 Azure 管理認證的案例](http://technet.microsoft.com/library/gg481759.aspx)。

## <a name="step-3-deploy-azure-nodes-to-the-cluster"></a>步驟 3︰ 叢集部署 Azure 節點



新增及啟動 Azure 節點在這個案例中的步驟通常是與內部部署前端節點的步驟相同。 如需詳細資訊，請參閱下列各節中[的 Microsoft HPC 套件部署 Azure 節點的步驟](https://technet.microsoft.com/library/gg481758.aspx)︰

* 建立可 Azure 節點的範本

* 新增到 Windows HPC 叢集的 Azure 節點

* 開始 （提供） Azure 節點

您新增及啟動節點之後，他們的年齡供您用來執行叢集工作。

如果部署 Azure 節點時，您就會發生問題，請參閱[疑難排解部署的 Azure 節點與 Microsoft HPC 套件](http://technet.microsoft.com/library/jj159097.aspx)。

## <a name="next-steps"></a>後續步驟

* 若要使用尖峰節點運算密集執行個體大小，請參閱[關於 H 數列和運算密集的數列 Vm](virtual-machines-windows-a8-a9-a10-a11-specs.md)的考量。

* 如果您想要自動放大或縮小根據叢集工作負載的 Azure 電腦資源，請參閱[自動變大和縮小 HPC 套件叢集 Azure 計算資源](virtual-machines-windows-classic-hpcpack-cluster-node-autogrowshrink.md)。

<!--Image references-->
[burst]: ./media/virtual-machines-windows-classic-hpcpack-cluster-node-burst/burst.png
