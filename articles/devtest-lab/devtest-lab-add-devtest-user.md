<properties
    pageTitle="新增 [Azure DevTest 實驗室中的 [擁有人和使用者 |Microsoft Azure"
    description="在使用 [Azure 入口網站] 或 [PowerShell 的 Azure DevTest 實驗室新增擁有人和使用者"
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
    ms.date="09/12/2016"
    ms.author="tarcher"/>

# <a name="add-owners-and-users-in-azure-devtest-labs"></a>新增 [Azure DevTest 實驗室中的 [擁有人和使用者

> [AZURE.VIDEO how-to-set-security-in-your-devtest-lab]

存取在 Azure DevTest 實驗室會控制[Azure Role-Based 存取控制 (RBAC)](../active-directory/role-based-access-control-what-is.md)。 使用 RBAC，您可以將之責任中您的小組為您授與存取地執行工作的使用者所需的量的*角色*。 三個這些 RBAC 角色為*擁有者*、 *DevTest 實驗室使用者*及*參與者*。 在本文中，您將瞭解可在每個三個主要 RBAC 角色執行什麼動作。 在這裡，您可以瞭解如何將使用者新增至實驗-透過入口網站及透過 PowerShell 指令碼，以及如何將使用者新增訂閱層級。

## <a name="actions-that-can-be-performed-in-each-role"></a>可以在每一個角色中執行的動作

有三個主要的角色，您可以指派使用者︰

- 擁有者
- DevTest 實驗室使用者
- 參與者

下表說明中每一個角色的使用者可以執行的動作︰

| **可執行此角色的使用者動作** | **DevTest 實驗室使用者**            | **擁有者** | **參與者** |
|---|---|---|---|
| **實驗室工作**                          |                              |       |             |
| 將使用者新增至實驗室                     | 無                           | [是]   | 無          |
| 更新成本設定                   | 無                           | [是]   | [是]         |
| **VM 的基本工作**                      |                              |       |             |
| 新增和移除自訂的圖像           | 無                           | [是]   | [是]         |
| 新增、 更新及刪除公式       | [是]                          | [是]   | [是]         |
| Whitelist Azure Marketplace 圖像     | 無                           | [是]   | [是]         |
| **VM 工作**                           |                              |       |             |
| 建立 Vm                             | [是]                          | [是]   | [是]         |
| 啟動、 停止及刪除 Vm            | 使用者所建立的 Vm | [是]   | [是]         |
| 更新 VM 原則                     | 無                           | [是]   | [是]         |
| 新增或移除資料磁碟從 Vm      | 使用者所建立的 Vm | [是]   | [是]         |
| **成品工作**                     |                              |       |             |
| 新增和移除的成品存放庫   | 無                           | [是]   | [是]         |
| 套用的成品                        | [是]                          | [是]   | [是]         |

> [AZURE.NOTE] 當使用者建立 VM 時，該使用者會自動指派到建立 VM 的**擁有者**角色。

## <a name="add-an-owner-or-user-at-the-lab-level"></a>新增實驗室層級的擁有者或使用者

擁有者和使用者可以新增實驗室層級透過 Azure 入口網站。 這包含外部使用者以正確的[Microsoft 帳戶 (MSA)](devtest-lab-faq.md#what-is-a-microsoft-account)。
下列步驟會引導您將擁有者或使用者新增至在 Azure DevTest 實驗室實驗室的程序︰

1. [Azure 入口網站](http://go.microsoft.com/fwlink/p/?LinkID=525040)登入。

1. 選取**更多服務**]，然後從清單中選取 [ **DevTest 實驗室**。

1. 從實驗室清單中，選取所要的實驗]。

1. 在實驗室刀，選取 [**設定**]。 

1. 在**設定**刀中，選取 [**使用者**]。

1. 在**使用者**刀中，選取 [ **+ 新增]**。

    ![新增使用者](./media/devtest-lab-add-devtest-user/devtest-users-blade.png)

1. 在**選取的角色**刀，選取所要的角色。 [] 區段中[，可以在每一個角色中執行的動作](#actions-that-can-be-performed-in-each-role)會列出的擁有者、 DevTest 使用者和參與者角色的使用者可以執行各種動作。

1. 在 [**新增使用者**刀中，輸入電子郵件地址或您想要新增在您指定的角色的使用者名稱。 如果找不到使用者，則錯誤訊息說明問題。 如果找到使用者，則該使用者已列出或選取。 

1. 選取 [**選取**]。

1. 選取**[確定**] 關閉**新增存取**刀]。

1. 當您返回**使用者**刀時，已新增使用者]。  

## <a name="add-an-external-user-to-a-lab-using-powershell"></a>將外部使用者新增至實驗室使用 PowerShell

除了 Azure 入口網站中新增使用者，您可以使用 PowerShell 指令碼測試環境中新增外部使用者。 在下列範例中，只要修改**值，以變更**註解下的參數值。
您可以擷取`subscriptionId`， `labResourceGroup`，及`labName`實驗室刀 Azure 入口網站中的值。

> [AZURE.NOTE]
> 範例指令碼假設指定的使用者已以來賓身分新增到 Active Directory 中，並不符合上述情況將會失敗。 若要新增使用者不在 Active Directory 中實驗室，使用 Azure 入口網站中的區段中，[新增擁有者] 或 [實驗室層級的使用者](#add-an-owner-or-user-at-the-lab-level)所示的角色指派給使用者。   

    # Add an external user in DevTest Labs user role to a lab
    # Ensure that guest users can be added to the Azure Active directory:
    # https://azure.microsoft.com/en-us/documentation/articles/active-directory-create-users/#set-guest-user-access-policies

    # Values to change
    $subscriptionId = "<Enter Azure subscription ID here>"
    $labResourceGroup = "<Enter lab's resource name here>"
    $labName = "<Enter lab name here>"
    $userDisplayName = "<Enter user's display name here>"

    # Log into your Azure account
    Login-AzureRmAccount
    
    # Select the Azure subscription that contains the lab. 
    # This step is optional if you have only one subscription.
    Select-AzureRmSubscription -SubscriptionId $subscriptionId
    
    # Retrieve the user object
    $adObject = Get-AzureRmADUser -SearchString $userDisplayName
    
    # Create the role assignment. 
    $labId = ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)
    New-AzureRmRoleAssignment -ObjectId $adObject.Id -RoleDefinitionName 'DevTest Labs User' -Scope $labId

## <a name="add-an-owner-or-user-at-the-subscription-level"></a>新增訂閱層級的擁有者或使用者

Azure 權限的上層範圍傳播到 Azure 中的子範圍。 因此，包含實驗室 Azure 訂閱的擁有者就會自動這些實驗室的擁有者。 他們也擁有 Vm 及建立實驗室的使用者和 Azure DevTest 實驗室服務的其他資源。 

您可以透過[Azure 入口網站](http://go.microsoft.com/fwlink/p/?LinkID=525040)中的實驗刀實驗室新增額外的擁有者。 不過，管理加入的擁有者範圍是更窄比訂閱擁有者的範圍。 新增的擁有者，例如沒有完整存取權的部分所建立的 DevTest 實驗室服務訂閱中的資源。 

若要新增至 Azure 訂閱的擁有者，請遵循下列步驟︰

1. [Azure 入口網站](http://go.microsoft.com/fwlink/p/?LinkID=525040)登入。

1. 選取**更多服務**]，然後從清單中選取 [**訂閱]** 。

1. 選取您要的訂閱]。

1. 選取的**Access**圖示。 

    ![Access 使用者](./media/devtest-lab-add-devtest-user/access-users.png)

1. 在**使用者**刀中，選取 [**新增**]。

    ![新增使用者](./media/devtest-lab-add-devtest-user/devtest-users-blade.png)

1. 在**選取的角色**刀中，選取 [**擁有者**]。

1. 在 [**新增使用者**刀中，輸入電子郵件地址或您想要新增為 [擁有者的使用者名稱。 如果找不到使用者，您會收到錯誤訊息，說明問題。 如果找到使用者時，該使用者會列在**使用者**的 [文字] 方塊中。

1. 選取位於的使用者名稱。

1. 選取 [**選取**]。

1. 選取**[確定**] 關閉**新增存取**刀]。

1. 當您返回**使用者**刀時，使用者已新增為 [擁有者]。 此使用者現在已在此訂閱]，建立任何實驗室的擁有者，因此可以執行擁有者工作。 

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]
