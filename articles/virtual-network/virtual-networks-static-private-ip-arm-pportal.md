<properties 
   pageTitle="如何設定靜態私人 ip 位址手臂模式使用 Azure 入口網站中 |Microsoft Azure"
   description="了解私人 IPs (DIPs)，以及如何管理其 ARM 模式使用 Azure 入口網站中"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/04/2016"
   ms.author="jdial" />

# <a name="how-to-set-a-static-private-ip-address-in-the-azure-portal"></a>如何設定 Azure 入口網站中的私人的靜態 IP 位址

[AZURE.INCLUDE [virtual-networks-static-private-ip-selectors-arm-include](../../includes/virtual-networks-static-private-ip-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]本文涵蓋資源管理員部署模型。 您也可以[管理靜態私人傳統部署模型中的 IP 位址](virtual-networks-static-private-ip-classic-pportal.md)。

[AZURE.INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

下列範例步驟預期已經建立一個簡單環境。 如果您想要執行這份文件中所顯示的步驟，先建立測試環境中[建立 vnet](virtual-networks-create-vnet-arm-pportal.md)所述。

## <a name="how-to-create-a-vm-for-testing-static-private-ip-addresses"></a>如何建立 VM 以進行測試私人的靜態 IP 位址

使用 Azure 入口網站，您無法在資源管理員部署模式 VM 建立期間設定私人的靜態 IP 位址。 您必須先建立 VM，轉設定為靜態其私人 IP。

若要建立 VM 中名為 「 *DNS01*名為*TestVNet*VNet *FrontEnd*子網路，請遵循下列步驟。

1. 從瀏覽器中，瀏覽至 http://portal.azure.com，如果有需要，請登入您的 Azure 帳戶。
2. 按一下 [**新增** > **計算** > **Windows Server 2012 R2 資料中心**，通知的**選取部署模型**] 清單中已顯示 [**資源管理員**]，然後按一下 [**建立**] 下, 圖所示。

    ![Azure 入口網站中建立 VM](./media/virtual-networks-static-ip-arm-pportal/figure01.png)

3. 中**的基本概念**刀中，輸入要建立 (案例中的*DNS01* ，) VM 的本機系統管理員帳戶及密碼下, 圖所示。

    ![基本概念刀](./media/virtual-networks-static-ip-arm-pportal/figure02.png)

4. 請確定已選取的**位置**是*美國中部*，然後按一下 [**資源群組**] 底下的 [**選取現有**然後再按一下 [**資源群組**]，然後按一下*TestRG*，，然後按一下**[確定]**。

    ![基本概念刀](./media/virtual-networks-static-ip-arm-pportal/figure03.png)

5. 在 [**選擇大小**刀中，選取**A1 標準**，，然後按一下**選取**。

    ![選擇大小刀](./media/virtual-networks-static-ip-arm-pportal/figure04.png) 

6. 在**設定**防禦，以確定下列屬性設定會以下列的值來設定，然後按一下**[確定]**。

    -**儲存帳戶**︰ *vnetstorage*
    - **網路**︰ *TestVNet*
    - **子網路**︰*主選單*

    ![選擇大小刀](./media/virtual-networks-static-ip-arm-pportal/figure05.png)  

7. 在**摘要**刀中，按一下**[確定]**。 請注意儀表板中顯示方塊下方。

    ![Azure 入口網站中建立 VM](./media/virtual-networks-static-ip-arm-pportal/figure06.png)

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>如何擷取 vm 靜態私人 IP 位址資訊

若要檢視使用上述步驟建立 vm 靜態私用的 IP 位址資訊，請執行下列步驟。

1. 從 Azure Azure 入口網站中，按一下 [**全部瀏覽** > **虛擬機器** > **DNS01** > **所有設定** > **網路介面**，然後按一下 [僅限網路介面上的所列。

    ![部署 VM 磚](./media/virtual-networks-static-ip-arm-pportal/figure07.png)

2. 在**網路介面**刀中，按一下 [**所有設定** > **IP 位址**，並注意 [**工作分派**和**IP 位址**] 值。

    ![部署 VM 磚](./media/virtual-networks-static-ip-arm-pportal/figure08.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>如何將私人的靜態 IP 位址新增至現有的 VM
若要新增 VM 使用上述步驟建立私人的靜態 IP 位址，請遵循下列步驟︰

1. 從**IP 位址**防禦如上所示的情況下，按一下 [**工作分派**] 下的 [**靜態**]。
2. 輸入*192.168.1.101* **IP 位址**，然後再按一下 [**儲存**。

    ![Azure 入口網站中建立 VM](./media/virtual-networks-static-ip-arm-pportal/figure09.png)

>[AZURE.NOTE] 如果之後按一下 [**儲存**您會注意到**動態**還是設定的工作分派]，表示您輸入的 IP 位址已在使用。 請嘗試不同的 IP 位址。

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>如何從 VM 移除私人的靜態 IP 位址
若要移除先前所建立的 VM 私人的靜態 IP 位址，請遵循下列步驟。
    
1. 從 [ **IP 位址**防禦如上所示的情況下，按一下 [**工作分派**] 底下的 [**動態**，然後按一下**儲存**。

## <a name="next-steps"></a>後續步驟

- 深入了解[保留的公用 IP](virtual-networks-reserved-public-ip.md)位址。
- 瞭解[執行個體層級的公用 IP (ILPIP)](virtual-networks-instance-level-public-ip.md)地址。
- [保留的 IP REST Api](https://msdn.microsoft.com/library/azure/dn722420.aspx)，請參閱。