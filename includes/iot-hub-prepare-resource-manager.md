## <a name="prepare-to-authenticate-azure-resource-manager-requests"></a>若要驗證 Azure 資源管理員要求準備

您必須驗證您在使用[Azure 資源管理員]「 資源執行的所有作業[lnk-authenticate-arm]與 Azure Active Directory (AD)。 進行此設定最簡單的方法是使用 PowerShell 或 Azure CLI。

您應該安裝[Azure PowerShell 1.0] [lnk-powershell-install]或稍後再繼續。

下列步驟顯示如何使用 PowerShell AD 應用程式的密碼驗證設定。 您可以執行這些命令在標準 PowerShell 工作階段。

1. 使用下列命令在 Azure 訂閱登入：

    ```
    Login-AzureRmAccount
    ```

2. 記下您**TenantId**和**SubscriptionId**。 需要加以更新版本。

3. 建立新的 Azure Active Directory 應用程式使用下列命令，並取代預留位置：

    - **{顯示名稱}:** **MySampleApp**等應用程式的顯示名稱
    - **{首頁 URL}:** **http://mysampleapp/home**例如應用程式的 [首頁] 頁面的 URL。 不需要此 URL 以指向實際的應用程式。
    - **{應用程式識別碼}:**例如**http://mysampleapp**的唯一識別碼。 不需要此 URL 以指向實際的應用程式。
    - **{密碼}:**您要與您的應用程式搭配使用來驗證的密碼。

    ```
    New-AzureRmADApplication -DisplayName {Display name} -HomePage {Home page URL} -IdentifierUris {Application identifier} -Password {Password}
    ```
    
4. 記下您建立應用程式**ApplicationId** 。 您將需要此更新版本。

5. 建立新的服務主要使用下列命令， **{MyApplicationId}**取代**ApplicationId**上一個步驟：

    ```
    New-AzureRmADServicePrincipal -ApplicationId {MyApplicationId}
    ```
    
6. 使用下列命令，以在**ApplicationId**取代**{MyApplicationId}**角色指派的安裝程式。

    ```
    New-AzureRmRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName {MyApplicationId}
    ```
    
現在您已經完成建立可讓您以驗證從您的自訂 C# 應用程式的 Azure AD 應用程式。 您將需要下列值稍後在本教學課程：

- TenantId
- SubscriptionId
- ApplicationId
- 密碼

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx
[lnk-powershell-install]: ../articles/powershell-install-configure.md
