<properties 
   pageTitle="加速虛擬機器-入口網站的網路 |Microsoft Azure"
   description="瞭解如何使用 [Azure 入口網站 Azure 虛擬機器中設定 [加速網路。"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/26/2016"
   ms.author="jdial" />

# <a name="accelerated-networking-for-a-virtual-machine"></a>虛擬機器加速的網路

> [AZURE.SELECTOR]
- [Azure 入口網站](virtual-network-accelerated-networking-portal.md)
- [PowerShell](virtual-network-accelerated-networking-powershell.md)

加速的網路啟用單一根 I/O 虛擬化 (SR IOV) 虛擬機器 (VM)，大幅改善網路效能。 此高效能路徑會略過主機減少延遲、 抖動，以及用於最重要的網路負載支援 VM 類型的 CPU 使用率資料路徑。 本文說明如何設定在 Azure 資源管理員部署模型中的 [加速網路使用 Azure 入口網站。 您也可以建立 VM 具有加速網路使用 PowerShell 的 Azure。 若要瞭解作法，按一下 [這份文件頂端的 [PowerShell] 方塊。

下圖顯示兩個虛擬機器 (VM) 與不包含加速網路之間的通訊︰

![比較](./media/virtual-network-accelerated-networking-portal/image1.png)

沒有加速網路主機和虛擬切換，必須通過與 VM 登出的所有網路流量。 虛擬切換提供所有的原則強制，例如網路安全性群組、 存取控制清單、 隔離與其他網路虛擬化服務網路流量。 若要深入瞭解，請閱讀[HYPER-V 網路虛擬化和虛擬切換](https://technet.microsoft.com/library/jj945275.aspx)文章。

含加速網路網路流量到達網路介面卡 (NIC)，然後再轉寄給 VM。 虛擬切換適用於不加速網路的所有網路原則卸載，並在 [硬體套用。 套用原則的硬體可直接至維持套用主應用程式中的所有原則，略過主機和虛擬切換，VM 轉寄網路流量 NIC。

在已啟用 VM 只適用於加速網路的優點。 為了獲得最佳的結果，很適合用來啟用此功能在兩個以上的 Vm 連線到相同的 VNet。 當通訊過 VNets 或內部部署連線，此功能會有整體的延遲的影響最小。

[AZURE.INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

##<a name="benefits"></a>優點

- **較低的延遲較高每一封包秒 (.pps):**移除資料路徑的虛擬切換原則處理主機中移除封包花費的時間，並增加可以處理 VM 內的封包數目。
- **精簡抖動︰**虛擬切換處理取決於所要套用原則的數量和執行作業的 cpu 的工作量。 卸載原則強制執行的硬體會移除該變化提供封包直接 VM，移除主應用程式 VM 通訊所有軟體中斷和內容切換。
- **縮小內容的 CPU 使用率︰**略過主機虛擬切換引導較少的 CPU 使用率處理網路流量。

## <a name="limitations"></a>限制

使用此功能時，有下列限制︰
 
- **網路介面建立︰**僅限啟用新的網路介面加速的網路。  它不能啟用現有的網路介面上。
- **VM 建立︰**網路介面加速的網路啟用僅可附加 VM VM 建立時。 網路介面無法附加至現有的 VM。
- **區域︰**在 [美國中部西部和西部歐洲 Azure 區域只提供。 將會在未來展開的地區設定。
- **支援的作業系統︰**Microsoft Windows Server 2012 R2 和 Windows Server 2016 Technical Preview 5。 將推出加入 Linux 與 Windows Server 2012 支援。
- **虛擬記憶體大小︰**Standard_D15_v2 和 Standard_DS15_v2 都是唯一的支援 VM 執行個體大小。 如需詳細資訊，請參閱[Windows VM 大小](../virtual-machines/virtual-machines-windows-sizes.md)文件。 將會在未來展開支援 VM 執行個體大小的設定。

將本項變更這些限制，透過[Azure 虛擬網路更新](https://azure.microsoft.com/updates/accelerated-networking-in-preview)頁面。

## <a name="create-a-windows-vm-with-accelerated-networking"></a>建立 Windows VM 加速網路

1. 藉由傳送電子郵件使用您的訂閱識別碼與用途[加速網路訂閱](mailto:axnpreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e)註冊預覽。 您收到電子郵件通知您已在預覽將已接受之後，請完成其餘的步驟，直到。
2. 在 http://portal.azure.com Azure 入口網站登入。
3. 完成選取下列選項[建立 Windows VM](../virtual-machines/virtual-machines-windows-hero-tutorial.md)文件中的步驟，以建立 VM:
    - 選取 [限制] 區段的本文中所列的作業系統。
    - 選取 [限制] 區段的本文中所列的位置 （地區）。
    - 選取 [限制] 區段的本文中所列的虛擬記憶體大小。 如果其中一個支援大小未列出，請按一下 [**選擇大小**刀選取大小從展開清單中的 [**檢視所有**]。
    - 在**設定**刀中，按一下 [*啟用***加速網路**，如下圖所示︰

        ![設定](./media/virtual-network-accelerated-networking-portal/image3.png)

    >[AZURE.NOTE] 加速網路] 選項時，才會顯示，如果您已︰
    >
    >- 接受到預覽
    >- 選取支援的作業系統、 位置及 VM 大小限制一節的本文所述。

5. VM 建立後，下載[加速網路驅動程式](https://gallery.technet.microsoft.com/Azure-Accelerated-471b5d84)，請連線並登入 VM，並執行 VM 內部驅動程式安裝程式。
6. 以滑鼠右鍵按一下 [Windows] 按鈕，然後按一下**[裝置管理員**]。 驗證的**Mellanox ConnectX 3 虛擬函數乙太網路介面卡**會出現在 [**網路**] 選項中展開時，如下圖所示︰

    ![[裝置管理員](./media/virtual-network-accelerated-networking-portal/image2.png)