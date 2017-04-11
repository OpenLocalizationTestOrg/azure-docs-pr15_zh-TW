<properties
    pageTitle="授與使用者權限特定實驗室原則 |Microsoft Azure"
    description="瞭解如何授與使用者權限，以根據每位使用者的需求 DevTest 實驗室中的特定實驗室原則"
    services="devtest-lab,virtual-machines,visual-studio-online"
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
    ms.date="08/25/2016"
    ms.author="tarcher"/>

# <a name="grant-user-permissions-to-specific-lab-policies"></a>授與特定實驗室原則的使用者權限

## <a name="overview"></a>概觀

本文說明如何使用 PowerShell 來授與特定實驗室原則的使用者權限。 如此一來，套用權限，可根據每位使用者的需求。 例如，您可能要授與特定使用者能夠變更 VM 原則設定，但不是成本原則。

## <a name="policies-as-resources"></a>為資源的原則

[Azure 角色型存取控制](../active-directory/role-based-access-control-configure.md)本文所述，RBAC 可讓資源的 Azure 精密存取管理。 使用 RBAC，可以將之責任內 DevOps 小組，並只量的存取權授與使用者所需執行的工作。

在 DevTest 實驗室原則會是啟用 RBAC 動作**Microsoft.DevTestLab/labs/policySets/policies/**的資源類型。 每個實驗室原則為資源在原則的資源類型，並可以指派為 RBAC 角色範圍。

例如，若要授與使用者讀/寫權限可**允許 VM 大小**原則，您想要建立自訂的角色，可以使用**Microsoft.DevTestLab/labs/policySets/policies/** *動作]，然後將適當的使用者指派給此範圍中的自訂角色* *Microsoft.DevTestLab/labs/policySets/policies/AllowedVmSizesInLab**。

若要進一步瞭解自訂 RBAC 角色，請參閱[自訂角色存取控制](../active-directory/role-based-access-control-custom-roles.md)。

##<a name="creating-a-lab-custom-role-using-powershell"></a>建立使用 PowerShell 實驗室自訂角色
若要開始設定，必須先閱讀下列文章說明如何安裝和設定 Azure PowerShell cmdlet: [https://azure.microsoft.com/blog/azps-1-0-pre](https://azure.microsoft.com/blog/azps-1-0-pre)。

一旦您已設定好 Azure PowerShell cmdlet，您可以執行下列工作︰

- 資源提供者的清單中的所有作業/動作
- 清單中的特定角色的動作︰
- 建立自訂的角色

下列 PowerShell 指令碼說明如何執行這些工作的範例︰

    ‘List all the operations/actions for a resource provider.
    Get-AzureRmProviderOperation -OperationSearchString "Microsoft.DevTestLab/*"

    ‘List actions in a particular role.
    (Get-AzureRmRoleDefinition "DevTest Labs User").Actions

    ‘Create custom role.
    $policyRoleDef = (Get-AzureRmRoleDefinition "DevTest Labs User")
    $policyRoleDef.Id = $null
    $policyRoleDef.Name = "Policy Contributor"
    $policyRoleDef.IsCustom = $true
    $policyRoleDef.AssignableScopes.Clear()
    $policyRoleDef.AssignableScopes.Add("/subscriptions/<SubscriptionID> ")
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/policySets/policies/*")
    $policyRoleDef = (New-AzureRmRoleDefinition -Role $policyRoleDef)

##<a name="assigning-permissions-to-a-user-for-a-specific-policy-using-custom-roles"></a>指派權限給使用者使用特定的原則，使用自訂的角色
一旦您定義了您自訂的角色，您可以將其指派給使用者。 若要自訂的角色指派給使用者，您必須先取得**ObjectId**代表該使用者。 若要這樣做，請使用**取得 AzureRmADUser**指令程式。

在下列範例中， **ObjectId** *SomeUser*使用者的是 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3。

    PS C:\>Get-AzureRmADUser -SearchString "SomeUser"

    DisplayName                    Type                           ObjectId
    -----------                    ----                           --------
    someuser@hotmail.com                                          05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3

**ObjectId**使用者及自訂角色名稱之後，您可以指定該角色的使用者**新增 AzureRmRoleAssignment**指令程式︰

    PS C:\>New-AzureRmRoleAssignment -ObjectId 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3 -RoleDefinitionName "Policy Contributor" -Scope /subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.DevTestLab/labs/<LabName>/policySets/policies/AllowedVmSizesInLab

在上一個範例中，會使用**AllowedVmSizesInLab**原則。 您可以使用下列任何一原則︰

- MaxVmsAllowedPerUser
- MaxVmsAllowedPerLab
- AllowedVmSizesInLab
- LabVmsShutdown

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>後續步驟

一次您已授予使用者權限特定實驗室原則，這裡有一些考量的下一個步驟︰

- [安全存取實驗室](devtest-lab-add-devtest-user.md)。

- [設定實驗室原則](devtest-lab-set-lab-policy.md)。

- [建立實驗室範本](devtest-lab-create-template.md)。

- [建立自訂的成品，為您的 Vm](devtest-lab-artifact-author.md)。

- [新增與實驗室的成品 VM](devtest-lab-add-vm-with-artifacts.md)。
