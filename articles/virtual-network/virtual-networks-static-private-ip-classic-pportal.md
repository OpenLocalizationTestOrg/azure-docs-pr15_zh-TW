<properties 
   pageTitle="如何使用 [Azure 入口網站的標準模式中設定靜態私人 IP |Microsoft Azure"
   description="了解靜態私人 IPs，以及如何管理其使用 Azure 入口網站的標準模式] 中"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-service-management"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/04/2016"
   ms.author="jdial" />

# <a name="how-to-set-a-static-private-ip-address-classic-in-the-azure-portal"></a>如何設定私人的靜態 IP 位址 （傳統） Azure 入口網站中

[AZURE.INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[AZURE.INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]本文涵蓋傳統部署模型。 您也可以[管理資源管理員部署模型的靜態私用的 IP 位址](virtual-networks-static-private-ip-arm-pportal.md)。

[AZURE.INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

下列範例步驟預期已經建立一個簡單環境。 如果您想要執行這份文件中所顯示的步驟，先建立測試環境中[建立 vnet](virtual-networks-create-vnet-classic-pportal.md)所述。

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>如何建立 VM 時，指定私人的靜態 IP 位址
若要建立 VM 中名為 「 *DNS01*名為 [固定的*192.168.1.101*私人 ip *TestVNet* VNet *FrontEnd*子網路，請遵循下列步驟︰

1. 從瀏覽器中，瀏覽至 http://portal.azure.com，如果有需要，請登入您的 Azure 帳戶。
2. 按一下 [**新增** > **計算** > **Windows Server 2012 R2 資料中心**、**選取部署模型**] 清單中已顯示 [**傳統**，，然後按一下 [**建立**的通知。

    ![Azure 入口網站中建立 VM](./media/virtual-networks-static-ip-classic-pportal/figure01.png)

3. 在**建立 VM**刀中，輸入名稱的建立 (案例中的*DNS01* ，) VM 本機系統管理員帳戶與密碼。

    ![Azure 入口網站中建立 VM](./media/virtual-networks-static-ip-classic-pportal/figure02.png)

4. 按一下 [**選擇性設定** > **網路** > **虛擬網路**，然後按一下 [ **TestVNet**。 如果**TestVNet**無法使用，請確定您正在使用的*美國中部*位置所建立的本文開頭所述的測試環境。

    ![Azure 入口網站中建立 VM](./media/virtual-networks-static-ip-classic-pportal/figure03.png)

5. 在**網路**防禦，以確認目前選取的子網路*FrontEnd*，然後按一下 [ **IP 位址**、 **IP 位址工作分派**] 下按一下 [**靜態**，，然後輸入*192.168.1.101* **IP**位址如下所示。

    ![Azure 入口網站中建立 VM](./media/virtual-networks-static-ip-classic-pportal/figure04.png)   

6. **IP 位址**刀中，按一下**[確定]** ，然後在**網路**刀中，按一下**[確定]** ，**選擇性 config**刀中按一下**[確定]** 。
7. 在**建立 VM**刀中，按一下 [**建立**。 請注意儀表板中顯示方塊下方。

    ![Azure 入口網站中建立 VM](./media/virtual-networks-static-ip-classic-pportal/figure05.png)

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>如何擷取 vm 靜態私人 IP 位址資訊

若要檢視使用上述步驟建立 vm 靜態私用的 IP 位址資訊，請執行下列步驟。

1. 從 Azure Azure 入口網站中，按一下 [**全部瀏覽** > **（傳統） 的虛擬機器** > **DNS01** > **所有設定** > **IP 位址**和通知的 IP 位址指派及 IP 位址如下所示。

    ![Azure 入口網站中建立 VM](./media/virtual-networks-static-ip-classic-pportal/figure06.png)

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>如何從 VM 移除私人的靜態 IP 位址
若要移除先前所建立的 VM 私人的靜態 IP 位址，請遵循下列步驟。
    
1. 從**IP 位址**防禦如上所示的情況下，按一下**動態**右邊的**IP 位址的指派**，然後按一下 [**儲存**]，然後按一下**[是]**。

    ![Azure 入口網站中建立 VM](./media/virtual-networks-static-ip-classic-pportal/figure07.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>如何將私人的靜態 IP 位址新增至現有的 VM
若要新增 VM 使用上述步驟建立私人的靜態 IP 位址，請遵循下列步驟︰

1. 從**IP 位址**防禦如上所示的情況下，按一下 [**靜態** **IP 位址指派**右側]。
2. 輸入*192.168.1.101* **IP 位址**，然後按一下 [**儲存**]，然後按一下**[是]**。

## <a name="next-steps"></a>後續步驟

- 深入了解[保留的公用 IP](virtual-networks-reserved-public-ip.md)位址。
- 瞭解[執行個體層級的公用 IP (ILPIP)](virtual-networks-instance-level-public-ip.md)地址。
- [保留的 IP REST Api](https://msdn.microsoft.com/library/azure/dn722420.aspx)，請參閱。