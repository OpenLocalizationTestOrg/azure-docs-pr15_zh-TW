<properties
    pageTitle="新增與成品 VM 中 Azure DevTest 實驗室實驗室 |Microsoft Azure"
    description="瞭解如何在 Azure DevTest 實驗室中新增的成品 VM"
    services="devtest-lab,virtual-machines"
    documentationCenter="na"
    authors="tomarcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="devtest-lab"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/30/2016"
    ms.author="tarcher"/>

# <a name="add-a-vm-with-artifacts-to-a-lab-in-azure-devtest-labs"></a>新增與成品 VM 中 Azure DevTest 實驗室實驗室

> [AZURE.VIDEO how-to-create-vms-with-artifacts-in-a-devtest-lab]

您可以建立 VM 中從*基本*的[自訂圖像](./devtest-lab-create-template.md)、[公式](./devtest-lab-manage-formulas.md)或[服務商場圖像](./devtest-lab-configure-marketplace-images.md)。

DevTest 實驗室*成品*可讓您指定建立 VM 時執行的*動作*。 

成品動作可以執行程序，例如執行執行艦隊命令，以及安裝軟體的 Windows PowerShell 指令碼。 

成品*參數*可讓您自訂的成品為特定的案例。

本文將示範如何建立 VM 與成品測試環境中。

## <a name="add-a-vm-with-artifacts"></a>新增與成品 VM

1. [Azure 入口網站](http://go.microsoft.com/fwlink/p/?LinkID=525040)登入。

1. 選取**更多服務**]，然後從清單中選取 [ **DevTest 實驗室**。

1. 實驗室清單中，選取您要在其中建立 VM 實驗室。  

1. 在實驗室**概觀**刀，選取 [ **+ 虛擬機器**。  
    ![新增 VM] 按鈕](./media/devtest-lab-add-vm-with-artifacts/devtestlab-home-blade-add-vm.png)

1. 在 [**選擇基底**刀中，選取基底 vm。

1. 在**虛擬機器**刀中，輸入新的虛擬機器的名稱**虛擬機器名稱**] 文字方塊中。

    ![實驗室 VM 刀](./media/devtest-lab-add-vm-with-artifacts/devtestlab-lab-vm-blade.png)

1. 輸入要授與虛擬機器上的系統管理員權限的**使用者名稱**。  

1. 如果您想要使用儲存在您*私人的儲存區*中的密碼，選取 [**使用我的私人存放區中的密碼**]，然後指定關鍵值對應到您的密碼 （密碼）。 否則，只要密碼欄位中輸入文字標示為**輸入值**。
 
1. 選取**虛擬機器大小**，然後選取其中一個預先定義的項目指定處理器核心、 RAM 大小，並建立 VM 硬碟大小。

1. 選取**虛擬網路**，然後選取所要的虛擬網路。

1. 選取**子網路**，然後選取 [子網路。

1. 如果實驗室原則設定為允許選取子網路的公用 IP 位址，指定您是否要選取 [**是**] 或 [**否**]，為公用的 IP 位址。 否則，此選項會停用，並選取為 [**否]**。 

1. 選取**的成品**-從清單中的成品-選取，並設定您想要新增至基底圖像的成品。 
**附註︰**如果您是新 DevTest 實驗室或設定的成品，直接跳到 [[新增現有的成品 vm](#add-an-existing-artifact-to-a-vm) ] 區段，然後在完成後，返回 [以下。

1. 如果您想要檢視或複製 Azure 資源管理員範本，直接跳到[儲存 Azure 資源管理員範本](#save-arm-template)] 區段中，並傳回以下完成。

1. 選取 [**建立**]，將指定的 VM 新增至實驗室。

1. 實驗室刀顯示 VM 建立; 的狀態第一次為**建立**，然後為 VM 後，**執行**已經啟動。

1. 移至 [[下一步](#next-steps)] 區段。 

## <a name="add-an-existing-artifact-to-a-vm"></a>將現有的成品新增至 VM

建立時 VM，您可以新增現有的成品。 每個實驗室包含從公用 DevTest 實驗室成品存放庫，以及您建立及新增至您自己的成品存放庫的成品的成品。
若要瞭解如何建立成品，請參閱本文，[瞭解如何製作您自己的成品用於 DevTest 實驗室](devtest-lab-artifact-author.md)。

1. 在**虛擬機器**刀中，選取 [**成品**]。 

1. 在**新增的成品**刀中，選取所要的成品。  

    ![新增成品刀](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifact-blade.png)

1. 輸入所需的參數值] 和 [任何您需要的選擇性參數。  

1. 選取 [**新增**]，以新增成品並返回 [**新增成品**刀]。

1. 接著繼續新增所需的您 VM 的成品。

1. 一旦您新增您的成品，就可以[變更成品的執行順序](#change-the-order-in-which-artifacts-are-run)。 您也可以前往回[檢視或修改的成品](#view-or-modify-an-artifact)。

## <a name="change-the-order-in-which-artifacts-are-run"></a>變更在其中執行的成品的順序

根據預設，成品的動作會執行中的 vm 加入的順序。 下列步驟說明如何變更在其中執行成品的順序。

1. 在**新增的成品**刀頂端，選取連結表示已新增的 vm 的成品的數目。

    ![新增至 VM 的成品的數字](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)

1. 若要指定在其中執行成品的順序，請將成品拖放到想要的順序。 **附註︰**如果您有無法拖曳成品，請確定您正在拖曳從左側的成品。 

1. 選取**[確定]**完成。  

## <a name="view-or-modify-an-artifact"></a>檢視或修改成品

下列步驟說明如何檢視或修改的成品參數︰

1. 在**新增的成品**刀頂端，選取連結表示已新增的 vm 的成品的數目。

    ![新增至 VM 的成品的數字](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)

1. 在**選取的成品**刀中，選取您想要檢視或編輯的成品。  

1. 在**新增的成品**防禦，以進行任何必要的變更，然後選取**[確定**] 關閉**新增成品**刀。

1. 選取**[確定**] 關閉**選取的成品**刀。

## <a name="save-azure-resource-manager-template"></a>儲存 Azure 資源管理員範本

Azure 資源管理員範本提供用宣告方式定義重複的部署。 下列步驟說明如何建立 vm 儲存 Azure 資源管理員範本。
儲存之後，您可以使用部署[新 Vm 使用 Azure PowerShell](../azure-resource-manager/resource-group-overview.md#template-deployment)的 Azure 資源管理員範本。

1. 在**虛擬機器**刀中，選取 [**檢視 ARM 範本**]。

1. 在**檢視 Azure 資源管理員範本刀**中，選取範本文字。

1. 將選取的文字複製到剪貼簿。

1. 選取**[確定**] 關閉**檢視 Azure 資源管理員範本刀**。

1. 開啟文字編輯器。

1. 貼上剪貼簿中的範本文字。

1. 儲存檔案以供日後使用。

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>後續步驟

- 只要 VM 建立之後，您可以連線至 VM VM 刀上選取**連線**。
- 瞭解如何[建立自訂您的 DevTest 實驗室 VM 成品](devtest-lab-artifact-author.md)。
- 探索[DevTest 實驗室 ARM 快速入門的範本藝廊](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates)