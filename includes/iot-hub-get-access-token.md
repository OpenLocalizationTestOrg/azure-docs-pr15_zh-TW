## <a name="obtain-an-azure-resource-manager-token"></a>取得 Azure 資源管理員 token

Azure Active Directory 必須驗證您使用 Azure 資源管理員的資源執行的所有工作。 以下所示的範例使用密碼驗證的其他方式看到[驗證 Azure 資源管理員要求][lnk-authenticate-arm]。

1. 將下列程式碼新增至 Program.cs 擷取使用的應用程式識別碼和密碼的 Azure AD 的權杖將**主要**方法。

    ```
    var authContext = new AuthenticationContext(string.Format  
      ("https://login.windows.net/{0}", tenantId));
    var credential = new ClientCredential(applicationId, password);
    AuthenticationResult token = authContext.AcquireTokenAsync
      ("https://management.core.windows.net/", credential).Result;
    
    if (token == null)
    {
      Console.WriteLine("Failed to obtain the token");
      return;
    }
    ```

2. 建立**主要**方法的結尾加入下列程式碼會使用權杖**ResourceManagementClient**物件：

    ```
    var creds = new TokenCredentials(token.AccessToken);
    var client = new ResourceManagementClient(creds);
    client.SubscriptionId = subscriptionId;
    ```

3. 建立或取得的參照，您使用的資源群組：

    ```
    var rgResponse = client.ResourceGroups.CreateOrUpdate(rgName,
        new ResourceGroup("East US"));
    if (rgResponse.Properties.ProvisioningState != "Succeeded")
    {
      Console.WriteLine("Problem creating resource group");
      return;
    }
    ```

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx