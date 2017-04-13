<properties 
   pageTitle="疑難排解入口網站的網路安全性群組-|Microsoft Azure"
   description="瞭解如何使用 [Azure 入口網站 Azure 資源管理員部署模型中的疑難排解網路安全性群組。"
   services="virtual-network"
   documentationCenter="na"
   authors="AnithaAdusumilli"
   manager="narayan"
   editor=""
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/23/2016"
   ms.author="anithaa" />

# <a name="troubleshoot-network-security-groups-using-the-azure-portal"></a>疑難排解使用 Azure 入口網站的網路安全性群組

> [AZURE.SELECTOR]
- [Azure 入口網站](virtual-network-nsg-troubleshoot-portal.md)
- [PowerShell](virtual-network-nsg-troubleshoot-powershell.md)

如果您設定您的虛擬機器 (VM) 網路安全性群組 (NSGs)，會發生 VM 連線發生問題，本文會提供概觀以協助進一步疑難排解 NSGs 的診斷功能。

NSGs 可讓您控制類型的流量與登出您的虛擬機器 (Vm) 的流量。 NSGs 可以套用至子網路中 Azure 虛擬網路 (VNet)、 網路介面 (NIC)，或兩者。 有效的規則套用至 NIC 會彙總的規則套用至 NIC NSGs 存在於和已連接到子網路。 這些 NSGs 過規則有時可以互相衝突，並會影響 VM 的網路連線。  

當您 VM Nic 上套用，您可以從您的 NSGs 檢視所有有效的安全性規則。 本文說明如何疑難排解 VM Azure 資源管理員部署模型中使用這些規則的連線發生問題。 如果您不熟悉 VNet 和 NSG 的概念，閱讀文章[虛擬網路](virtual-networks-overview.md)和[網路安全性群組](virtual-networks-nsg.md)概觀。

## <a name="using-effective-security-rules-to-troubleshoot-vm-traffic-flow"></a>若要疑難排解 VM 流量使用有效的安全性規則

遵循此案例是常見的連線問題的範例︰

名為*VM1* VM 是名為*Subnet1*中名為*WestUS VNet1*VNet 子網路的一部分。 嘗試透過 TCP 連接埠 3389 使用 RDP vm 連線失敗。 NSGs 會套用在 NIC *VM1 NIC1*和子網路*Subnet1*。 TCP 連接埠 3389 流量導向允許 NSG 相關聯的網路介面*VM1 NIC1*，不過 TCP 偵測 （ping） 至 VM1 的連接埠 3389 失敗。

雖然這個範例使用 TCP 連接埠 3389，下列步驟可用來決定傳入和傳出連線失敗，透過任何連接埠。

### <a name="view-effective-security-rules-for-a-virtual-machine"></a>檢視有效的安全性規則虛擬機器

完成下列步驟，以疑難排解 NSGs vm:

您可以在 NIC，從 VM 本身上檢視有效的安全性規則的完整清單。 您也可以新增、 修改及 NIC 和子網路 NSG 規則刪除有效規則刀中，如果您有權限，才能執行這些作業。

1. 在 https://portal.azure.com Azure 入口網站登入。
2. 按一下 [**更多服務**]，然後按一下**虛擬機器**中出現的清單。
3. 選取 VM 疑難排解從出現的清單，並 VM 刀選項顯示。
4. 按一下 [**診斷和解決問題**]，然後選取 [常見的問題。 例如，**無法連線至我的 Windows VM**已選取。 

    ![](./media/virtual-network-nsg-troubleshoot-portal/image1.png)

5. 步驟顯示問題] 之下，如下圖所示︰ 

    ![](./media/virtual-network-nsg-troubleshoot-portal/image2.png)

    按一下清單中的建議步驟*有效的安全性群組規則*]。

6. **取得有效的安全性規則**刀隨即出現，如下圖所示︰

    ![](./media/virtual-network-nsg-troubleshoot-portal/image3.png)

    請注意下列各節的圖片︰

    - **範圍︰**步驟 3 中，設定*VM1*，VM 選取。
    - **網路介面︰***VM1 NIC1*為選取狀態。 VM 可以有多個網路介面 (NIC)。 每個 NIC 可以有唯一有效的安全性規則。 疑難排解時，您可能需要的每個 NIC 檢視有效的安全性的規則
    - **相關聯 NSGs:**可以套用 NSGs NIC 和子 NIC 已連線至網路。 在圖片，NIC 和子網路連線到已套用 NSG。 您可以按一下 NSG 名稱直接修改 NSGs 中的規則。
    - **VM1 nsg] 索引標籤︰**在圖片中顯示的規則清單適用於套用至在 NIC NSG Azure NSG 建立時，會建立多個預設的規則。 您無法移除預設的規則，但您可以使用規則的優先順序覆寫它們。 若要瞭解關於預設規則的詳細資訊，請閱讀[NSG 概觀](virtual-networks-nsg.md#default-rules)文章。
    - **目的資料行︰**當其他人有地址前置詞的某些規則可以有 [] 欄中的文字。 文字是套用的安全性規則建立時的預設標籤的名稱。 標記是系統提供代表多個前置詞的識別碼。 選取標記，例如*AllowInternetOutBound*，規則清單中的**地址前置詞**刀的首碼。
    - **下載︰**規則清單可以很長。 您可以下載離線分析的規則的.csv 檔案，只要按一下 [**下載**並儲存檔案。
    - **AllowRDP**輸入的規則︰ 此規則可讓 VM RDP 連線。
7. 按一下 [ **Subnet1 NSG** ] 索引標籤，來檢視從套用至子網路，NSG 有效的規則，如下圖所示︰ 

    ![](./media/virtual-network-nsg-troubleshoot-portal/image4.png)

    請注意*denyRDP* **輸入**規則。 輸入的規則套用至子網路會評估前規則套用至 [網路介面。 因為拒絕規則會套用在子網路，要求連線至 TCP 3389 失敗，因為儲存格內允許規則在 nic 永遠不會進行評估。 

    *DenyRDP*規則是 RDP 連線失敗的原因的原因。 移除它應該會解決問題。

    >[AZURE.NOTE]如果在 NIC 相關聯的 VM 不在執行狀態，或 NSGs 尚未已經套用到 NIC 或子網路，則會不顯示任何規則。

8. 若要編輯 NSG 規則，請按一下*Subnet1 NSG* **相關聯的 NSGs** ] 區段中。
   這會開啟**Subnet1 NSG**刀。 您可以直接**連入的安全性規則**上的 [編輯規則。

    ![](./media/virtual-network-nsg-troubleshoot-portal/image7.png)

9. 移除*denyRDP*輸入的規則**Subnet1 NSG**中，加入*allowRDP*規則，有效的規則清單看起來像下圖︰

    ![](./media/virtual-network-nsg-troubleshoot-portal/image8.png)

    確認 [TCP 連接埠 3389 是開啟的 vm RDP 連線，或使用 PsPing 工具來開啟]。 您可以進一步瞭解 PsPing 閱讀的[PsPing 下載頁面](https://technet.microsoft.com/sysinternals/psping.aspx)。

### <a name="view-effective-security-rules-for-a-network-interface"></a>檢視有效的安全性規則網路介面

如果您 VM 交通流量的特定 NIC 的影響，您可以檢視完整的有效的規則清單從網路介面內容 NIC 完成下列步驟︰

1. 在 https://portal.azure.com Azure 入口網站登入。
2. 按一下 [**更多服務**]，然後按一下出現的清單中**的網路介面**。
3. 選取網路介面。 在下圖中，已選取名為*VM1 NIC1* NIC。

    ![](./media/virtual-network-nsg-troubleshoot-portal/image5.png)

    請注意，**範圍**設定為選取的網路介面。 若要進一步瞭解顯示的其他資訊，請閱讀本文的**疑難排解 NSGs vm**一節的步驟 6。

    >[AZURE.NOTE] 從網路介面移除 NSG 時，子網路 NSG 仍然有效指定 NIC 上 在此情況下，輸出只會顯示子網路 NSG 的規則。 規則只會出現在 NIC 附加到 VM。

4. 您可以直接編輯的 NSGs NIC 和子網路相關聯的規則。 若要瞭解作法，請閱讀步驟 8 的**虛擬機器檢視有效的安全性規則**區段的 [這份文件。

## <a name="view-effective-security-rules-for-a-network-security-group-nsg"></a>檢視有效的安全性規則網路安全性群組 (NSG)

修改時 NSG 規則，您可能會想要檢閱特定 VM 新增規則的影響。 您可以檢視完整的有效的安全性規則清單的所有 Nic 指定的 NSG 套用至]，而不需要切換指定 NSG 刀的內容。 若要疑難排解 NSG 內有效的規則，請完成下列步驟︰

1. 在 https://portal.azure.com Azure 入口網站登入。
2. 按一下 [**更多服務**]，然後按一下出現的清單中**的網路安全性群組**。
3. 選取 [NSG]。 在下圖中，選取名稱為 VM1 nsg NSG。

    ![](./media/virtual-network-nsg-troubleshoot-portal/image6.png)

    請注意下列各節的前一張圖片︰

    - **範圍︰**設定選取 NSG。
    - **虛擬機器︰**當 NSG 套用至子網路時，它會套用至附加至子網路連線的所有 Vm 的所有網路介面。 此清單會顯示此 NSG 套用至所有 Vm。 您可以從清單中選取任何 VM。

    >[AZURE.NOTE] 如果 NSG 套用至空白子網路時，就不會列出 Vm。 如果不是與 VM 相關聯的 NIC 套用 NSG，則這些 Nic 也不會列在。 
    - **網路介面︰**VM 可以有多個網路介面。 您可以選取附加至所選 VM 網路介面。
    - **AssociatedNSGs:**在任何時候 NIC 可以有兩個有效 NSGs，套用至 NIC 和子網路另一個。 如果在 NIC 有有效的子網路 NSG VM1 nsg，以選取範圍，雖然輸出會顯示兩個 NSGs。
4. 您可以直接編輯的 NSGs NIC 或子網路相關聯的規則。 若要瞭解作法，請閱讀步驟 8 的**虛擬機器檢視有效的安全性規則**區段的 [這份文件。

若要進一步瞭解顯示的其他資訊，請閱讀本文的**虛擬機器檢視有效的安全性規則**一節的步驟 6。

>[AZURE.NOTE] 雖然子網路和 NIC 各有只有一個套用 NSG，NSG 可以多個 Nic 和多個子網路相關聯。

## <a name="considerations"></a>考量

疑難排解連線問題時，請考慮下列幾點︰

- 預設 NSG 規則會封鎖從網際網路輸入的存取權，並只允許 VNet 連入流量。 若要從網際網路]，允許輸入的存取，視需要應該明確新增規則。
- 如果沒有 NSG 安全性規則造成 VM 的網路連線失敗，問題可能會到期日︰
    - 執行 VM 的作業系統內的防火牆軟體
    - 設定虛擬就或內部部署的傳輸路由。 網際網路流量可以重新導向至內部部署透過強制通道。 從網際網路 RDP/SSH 連線至您 VM 可能不適用於這項設定，取決於內部部署網路硬體如何處理此流量。 請參閱[疑難排解路由](virtual-network-routes-troubleshoot-powershell.md)，瞭解如何診斷傳送的問題，可能會索性流量傳送與 VM 登出。 
- 如果您有 peered VNets，根據預設，VIRTUAL_NETWORK 標記會自動展開至包含 peered VNets 前置字元]。 您可以檢視這些前置詞**ExpandedAddressPrefix**清單中，與 VNet 等連線相關的任何問題進行疑難排解。 
- 有效的安全性規則只會顯示是否有相關聯的 VM NIC 和或子網路 NSG。 
- 如果不有任何 NIC 相關聯的 NSGs 或子網路，而且您已指派給您 VM 的公用 IP 位址，會開啟傳入和傳出存取所有的連接埠。 如果 VM 的公用 IP 位址，強烈建議將 NSGs 套用至 NIC 或子網路。