<properties
    pageTitle="建立 VM 可用性組 |Microsoft Azure"
    description="瞭解如何建立虛擬機器使用 Azure 入口網站或使用資源管理員部署模型的 PowerShell 設定可用性。"
    keywords="顯示狀態設定"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>
<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="cynthn"/>


# <a name="create-an-availability-set"></a>建立可用性設定 

使用入口網站，如果您想要加入的可用性您 VM 設定時，您需要建立第一次設定的可用性。

如需有關建立和使用可用性集的詳細資訊，請參閱[管理虛擬機器中的可用性](virtual-machines-windows-manage-availability.md)。


## <a name="use-the-portal-to-create-an-availability-set-before-creating-your-vms"></a>若要建立設定再建立您的 Vm 可用性使用入口網站

1. 在 [中心] 功能表中，按一下 [**瀏覽**並選取**可用性設定**。

2. 在**顯示狀態設定為刀**，按一下 [**新增**。

    ![螢幕擷取畫面顯示 [新增] 按鈕，建立新的顯示狀態的設定。](./media/virtual-machines-windows-create-availability-set/add-availability-set.png)

3. 在**建立可用性設定**刀，完成您設定的資訊。

    ![必須先建立可用性集輸入的螢幕擷取畫面顯示資訊。](./media/virtual-machines-windows-create-availability-set/create-availability-set.png)

    - **名稱**-名稱都必須是 1 80 個字元的數字、 字母、 週期、 底線和虛線組成。 字母或數字，則必須是第一個字元。 字母、 數字或底線，必須使用的最後一個字元。
    - **故障網域**-故障網域定義共用常見 power 來源和網路切換參數的虛擬機器中的群組。 根據預設，Vm 分隔各最多三個故障網域，且可以變更為介於 1 到 3。
    - **更新網域**的網域會指派預設，這五個更新可供設定，介於 1 到 20。 更新網域表示虛擬機器和可以重新開機，同時的基礎實體硬體的群組。 例如，如果我們指定五個更新的網域，設定五個以上的虛擬機器中單一可用性設定時，第六個虛擬機器會放入第一個的虛擬機器中第二個虛擬機器中，為相同的 「 ud 」 七相同的更新網域等等。 重新啟動的順序可能不連續的但只有一個更新的網域就會重新開機，一次。
    - **訂閱**-選取 [使用如果您有多個訂閱。
    - **資源群組**-按一下箭號，然後向下選取資源群組中的，從下拉式清單選取現有的資源群組。 您也可以輸入名稱，以建立新的資源群組。 名稱可以包含下列字元︰ 字母、 數字、 週期、 虛線、 底線和左或右括號。 名稱無法句點結尾。 Vm 可用性] 群組中的所有需要建立的顯示狀態設定為相同的 [資源] 群組中。
    - **位置**-選取一個位置，從下拉式清單。

4. 當您完成輸入資訊，請按一下 [**建立**]。 只要可用性群組建立之後，您可以在清單中看到它之後您重新整理入口網站。

## <a name="use-the-portal-to-create-a-virtual-machine-and-an-availability-set-at-the-same-time"></a>若要建立虛擬機器與同時設定可用性使用入口網站

如果您要建立新的 VM 使用入口網站，您也可以建立新的顯示狀態，當您在設定建立第一個 VM vm 設定。

![螢幕擷取畫面顯示 [建立新的顯示狀態，您建立 VM 時設定的程序。](./media/virtual-machines-windows-create-availability-set/new-vm-avail-set.png)


## <a name="add-a-new-vm-to-an-existing-availability-set"></a>將新的 VM 新增至現有的顯示狀態設定

對於每個其他 VM 您建立的屬於應該在設定，請確定您建立相同的**資源群組**中，，然後選取 [現有的顯示狀態，在步驟 3 中設定。 

![螢幕擷取畫面顯示如何選取現有的顯示狀態設定為使用您 VM。](./media/virtual-machines-windows-create-availability-set/add-vm-to-set.png)



## <a name="use-powershell-to-create-an-availability-set"></a>使用 PowerShell 來建立可用性設定

此範例會建立在**西美國**位置設定**RMResGroup**資源群組中的可用性。 這需要建立會在設定的第一個 VM 之前，先完成。

    New-AzureRmAvailabilitySet -ResourceGroupName "RMResGroup" -Name "AvailabilitySet03" -Location "West US"
    
如需詳細資訊，請參閱[新增 AzureRmAvailabilitySet](https://msdn.microsoft.com/library/mt619453.aspx)。


## <a name="troubleshooting"></a>疑難排解

- 當您建立 VM，如果您想要的顯示狀態設定不在下拉式清單，在入口網站時可能會在建立不同的資源群組中。 如果您不知道您的顯示狀態的資源群組設定中心] 功能表，請按一下 [瀏覽 > 可用性設定以查看您是否有空組，而清單資源所屬的群組。


## <a name="next-steps"></a>後續步驟

新增額外的[資料磁碟](virtual-machines-windows-attach-disk-portal.md)您 vm 新增額外的儲存空間。
