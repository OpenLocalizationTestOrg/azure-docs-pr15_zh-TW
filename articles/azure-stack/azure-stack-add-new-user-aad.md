<properties
    pageTitle="Azure Active Directory 中新增新的 Azure 堆疊租用戶帳戶 |Microsoft Azure"
    description="部署 Microsoft Azure 堆疊 POC 之後, 必須建立至少有一個租用戶的使用者帳戶，讓您可以瀏覽租用戶入口網站。"
    services="azure-stack"
    documentationCenter=""
    authors="ErikjeMS"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="erikje"/>

# <a name="add-a-new-azure-stack-tenant-account-in-azure-active-directory"></a>Azure Active Directory 中新增新的 Azure 堆疊租用戶帳戶

[部署 Azure 堆疊 POC](azure-stack-run-powershell-script.md)之後, 您需要的租用戶的使用者帳戶，讓您可以瀏覽租用戶入口網站並測試您的提供和計劃。 您可以使用[Azure 入口網站](#create-an-azure-stack-tenant-account-using-the-azure-portal)，或使用[PowerShell](#create-an-azure-stack-tenant-account-using-powershell)來建立的租用戶帳戶。

## <a name="create-an-azure-stack-tenant-account-using-the-azure-portal"></a>建立 Azure 堆疊租用戶帳戶使用 Azure 入口網站

您必須使用 Azure 入口網站的 Azure 訂閱。

1. 登入[Azure](http://manage.windowsazure.com)。

2.  Microsoft Azure 左側的導覽列中，按一下 [ **Active Directory**]。

3.  在 [目錄] 清單中，按一下您想要使用的 Azure 堆疊的目錄或建立新的項目。

4.  在此目錄] 頁面上，按一下 [**使用者**]。

5.  按一下 [**新增使用者**]。

6.  在 [**新增使用者**精靈] 中**的使用者類型**] 清單中，選擇**您組織中的新使用者**。

7.  在 [**使用者名稱**] 方塊中，輸入使用者的名稱。

8.  在 [**@**方塊中，選擇適當的項目。

9.  按一下下一步的箭號。

10.  在精靈的 [**使用者設定檔**] 頁面中，輸入**名字**、**姓氏**及**顯示名稱**。

11. 在 [**角色**] 清單中，選擇 [**使用者**]。

12. 按一下下一步的箭號。

13. 在 [**取得暫時密碼**] 頁面上，按一下 [**建立**]。

14. 複製的**新密碼**。

15. 使用新的帳戶登入 Microsoft Azure。 變更當畫面提示您的密碼。

16. 登入`https://portal.azurestack.local`若要查看的租用戶入口網站的新帳戶。

## <a name="create-an-azure-stack-tenant-account-using-powershell"></a>建立使用 PowerShell 的 Azure 堆疊租用戶帳戶

如果您沒有安裝 Azure 的訂閱，您無法使用 Azure 入口網站，若要新增的租用戶的使用者帳戶。 在此情況下，您可以改用 Azure Active Directory 模組的 Windows PowerShell。

> [AZURE.NOTE] 如果您部署 Azure 堆疊 PoC 使用 Microsoft 帳戶 (Live ID)，您就無法使用 AAD PowerShell 來建立租用戶帳戶。 

1.  安裝[Microsoft Online Services 登入小幫手適用於 IT 專業人員 RTW](https://www.microsoft.com/en-us/download/details.aspx?id=41950)。

2.  安裝[Azure Active Directory 的 Windows PowerShell 模組 （64 位元版本）](http://go.microsoft.com/fwlink/p/?linkid=236297) ，並將其開啟。

3.  執行下列 cmdlet:




    ```powershell
    # Provide the AAD credential you use to deploy Azure Stack PoC
   
            $msolcred = get-credential
    
    # Add a tenant account "Tenant Admin <username>@<yourdomainname>" with the initial password "<password>".
    
            connect-msolservice -credential $msolcred
            $user = new-msoluser -DisplayName "Tenant Admin" -UserPrincipalName <username>@<yourdomainname> -Password <password>
            Add-MsolRoleMember -RoleName "Company Administrator" -RoleMemberType User -RoleMemberObjectId $user.ObjectId
    
    ```

4.  使用新的帳戶登入 Microsoft Azure。 變更當畫面提示您的密碼。

5.  登入`https://portal.azurestack.local`若要查看的租用戶入口網站的新帳戶。



