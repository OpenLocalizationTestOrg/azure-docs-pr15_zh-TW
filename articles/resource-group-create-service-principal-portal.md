<properties
   pageTitle="建立服務主要入口網站中 |Microsoft Azure"
   description="說明如何建立新的 Active Directory 應用程式與服務的主要的也使用角色型存取控制 Azure 資源管理員中用來管理資源的存取權。"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/07/2016"
   ms.author="tomfitz"/>

# <a name="use-portal-to-create-active-directory-application-and-service-principal-that-can-access-resources"></a>若要建立 Active Directory 應用程式與服務的主要可以存取資源的使用入口網站

> [AZURE.SELECTOR]
- [PowerShell](resource-group-authenticate-service-principal.md)
- [Azure CLI](resource-group-authenticate-service-principal-cli.md)
- [入口網站](resource-group-create-service-principal-portal.md)


當您需要存取或修改資源的應用程式時，您必須設定 [Active Directory (AD) 應用程式，並為其指定必要的權限。 本主題說明如何執行這些步驟，透過入口網站。 目前，您必須使用 [傳統] 入口網站來建立新的 Active Directory 應用程式，然後切換至 Azure 入口網站指派角色給應用程式。 

> [AZURE.NOTE] 本主題中的步驟只適用於使用**傳統的入口網站**建立 AD 應用程式時。 **如果您使用 Azure 入口網站建立 AD 應用程式時，這些步驟會失敗。** 
>
> 您可能會發現容易設定 AD 應用程式和服務本金透過[PowerShell](resource-group-authenticate-service-principal.md)或[Azure CLI](resource-group-authenticate-service-principal-cli.md)，尤其是如果您想要使用的憑證驗證。 本主題不會顯示如何使用的憑證。

Active Directory 概念的說明，請參閱[應用程式與服務主要物件](./active-directory/active-directory-application-objects.md)。 如需有關 Active Directory 驗證的詳細資訊，請參閱[Azure ad 驗證案例](./active-directory/active-directory-authentication-scenarios.md)。

整合 Azure 中的應用程式，來管理資源的詳細步驟，請參閱[開發人員指南和 Azure 資源管理員 API 的授權](resource-manager-api-authentication.md)。

## <a name="create-an-active-directory-application"></a>建立 Active Directory 應用程式

1. 登入您的 Azure 帳戶透過[傳統入口網站](https://manage.windowsazure.com/)。 

2. 請確定您知道您的訂閱預設 Active Directory。 您可以只授與您的訂閱相同的目錄中的應用程式的存取權。 選取 [**設定**]，並尋找您的訂閱相關聯的目錄名稱。  如需詳細資訊，請參閱[如何 Azure 訂閱 Azure Active Directory 與相關聯](./active-directory/active-directory-how-subscriptions-associated-directory.md)。
   
     ![尋找預設目錄](./media/resource-group-create-service-principal-portal/show-default-directory.png)

2. 從左窗格中選取 [ **Active Directory** ]。

     ![選取 [Active Directory](./media/resource-group-create-service-principal-portal/active-directory.png)
     
3. 選取您想要建立應用程式的使用 Active Directory。 如果您有一個以上的 Active Directory，建立您的訂閱的預設目錄中的應用程式。   

     ![選擇目錄](./media/resource-group-create-service-principal-portal/active-directory-details.png)
     
3. 若要檢視應用程式目錄中，選取 [**應用程式**。

     ![檢視應用程式](./media/resource-group-create-service-principal-portal/view-applications.png)

4. 如果您尚未建立應用程式中的目錄之前，您應該會看到類似下列圖像。 選取 [**新增應用程式**

     ![新增應用程式](./media/resource-group-create-service-principal-portal/create-application.png)

     或者，按一下 [**新增**]，請在下方窗格中。

     ![新增](./media/resource-group-create-service-principal-portal/add-icon.png)

5. 選取您想要建立的應用程式類型。 在此教學課程中，選取 [**新增組織內部開發的應用程式**。 

     ![新的應用程式](./media/resource-group-create-service-principal-portal/what-do-you-want-to-do.png)

6. 提供應用程式的名稱，然後選取您想要建立的應用程式類型。 在此教學課程中，建立**WEB 應用程式和/或 WEB API** ，然後按一下 [下一步] 按鈕。 如果您選取**原始的用戶端應用程式**，本文的其餘的步驟會不符合您的體驗。

     ![名稱應用程式](./media/resource-group-create-service-principal-portal/tell-us-about-your-application.png)

7. 填入您的應用程式的內容。 **登開啟 URL**，提供 URI 描述您的應用程式的網站。 不會驗證存在的網站。 **應用程式識別碼 URI**，提供識別您的應用程式的 URI。

     ![應用程式屬性](./media/resource-group-create-service-principal-portal/app-properties.png)

您已經建立您的應用程式。

## <a name="get-client-id-and-authentication-key"></a>取得用戶端識別碼和驗證金鑰

以程式設計方式登入，您會需要應用程式的識別碼。 如果 [它自己的認證] 底下，執行應用程式，您也需要驗證金鑰。

1. 選取 [**設定**] 索引標籤來設定您的應用程式的密碼。

     ![設定應用程式](./media/resource-group-create-service-principal-portal/application-configure.png)

2. 將複製的 [**用戶端識別碼**。
  
     ![用戶端識別碼](./media/resource-group-create-service-principal-portal/client-id.png)

3. 如果 [它自己的認證] 底下，執行應用程式，向下**鍵**捲動，然後選取 [您想要您的密碼是有效的時間長度。

     ![索引鍵](./media/resource-group-create-service-principal-portal/create-key.png)

4. 選取 [**儲存**]，建立您的金鑰。

     ![儲存](./media/resource-group-create-service-principal-portal/save-icon.png)

     會顯示 [儲存] 鍵，您可以將其複製。 您無法擷取金鑰更新版本，因此現在複製。

     ![儲存索引鍵](./media/resource-group-create-service-principal-portal/save-key.png)

## <a name="get-tenant-id"></a>取得租用戶識別碼

以程式設計方式登入，必須先通過驗證要求您的租用戶識別碼。 如需 Web 應用程式與 Web API 應用程式，您可以選取在畫面底部的**檢視結束點**，並擷取下圖所示的識別碼擷取的租用戶識別碼。  

   ![租用戶識別碼](./media/resource-group-create-service-principal-portal/save-tenant.png)

您也可以擷取透過 PowerShell 的租用戶識別碼︰

    Get-AzureRmSubscription

或 Azure CLI:

    azure account show --json

## <a name="set-delegated-permissions"></a>設定委派的權限

如果您的應用程式存取代表登入使用者的資源，您必須授與您的應用程式存取其他應用程式的委派權限。 您授與此 access 中的 [**設定**] 索引標籤的 [**其他應用程式的權限**] 區段。 根據預設，委派的權限已啟用的 Azure Active Directory。 [離開此保持不變的委派權限。

1. 選取 [**新增應用程式**]。

2. 從清單中，選取 [ **Windows Azure 服務管理 API**]。 然後，選取 [完成] 圖示。

      ![選取應用程式](./media/resource-group-create-service-principal-portal/select-app.png)

3. 在下拉式清單中的委派權限，選取 [**為組織的 Access Azure 服務管理**]。

      ![選取權限](./media/resource-group-create-service-principal-portal/select-permissions.png)

4. 儲存變更。

## <a name="assign-application-to-role"></a>指派給角色的應用程式

如果您的應用程式正在執行 [它自己的認證] 底下，您必須指派給角色的應用程式。 決定哪些角色代表應用程式的正確權限。 若要進一步瞭解可用的角色，請參閱[RBAC︰ 內建於角色](./active-directory/role-based-access-built-in-roles.md)。 

若要將角色指派給應用程式，您必須正確的權限。 具體來說，您必須`Microsoft.Authorization/*/Write`授與透過[使用者存取系統管理員](./active-directory/role-based-access-built-in-roles.md#user-access-administrator)角色的[擁有者](./active-directory/role-based-access-built-in-roles.md#owner)角色的存取權。 參與者角色並沒有正確的存取權。

您可以設定範圍層級的訂閱，資源] 群組中或資源。 權限繼承較低層級的範圍。 例如，新增資源群組讀取者角色的應用程式，表示它可以讀取資源群組和它所包含任何資源。

1. 若要指派給角色的應用程式，請從 [傳統] 入口網站切換至[Azure 入口網站](https://portal.azure.com)。

1. 核取您，請確定您可以將服務主要指派給角色的權限。 選取 [**我的權限**，您的帳戶。

    ![選取 [我的權限](./media/resource-group-create-service-principal-portal/my-permissions.png)

1. 檢視您的帳戶指派的權限。 如先前所述，您必須屬於的擁有者或使用者存取系統管理員角色，或針對 Microsoft.Authorization 授與寫入存取權的自訂的角色。 下圖顯示帳戶的訂閱，沒有足夠的權限指派給角色的應用程式的參與者角色指派。

    ![顯示我的權限](./media/resource-group-create-service-principal-portal/show-permissions.png)

     如果您沒有正確的權限授與存取權的應用程式，您必須是您訂閱的系統管理員的使用者存取系統管理員角色，將您的要求或要求的系統管理員授與存取應用程式。

1. 瀏覽至您想要指派的應用程式的範圍的層級。 若要指定在訂閱的範圍內的角色，選取 [**訂閱**]。

     ![選取 [訂閱]](./media/resource-group-create-service-principal-portal/select-subscription.png)

     選取特定的訂閱，若要指派的應用程式。

     ![選取 [工作分派的訂閱](./media/resource-group-create-service-principal-portal/select-one-subscription.png)

     選取右上角的**Access**圖示。

     ![選取 [存取](./media/resource-group-create-service-principal-portal/select-access.png)
     
     或者，若要指定在資源群組範圍的角色，瀏覽至 [資源群組]。 從 [資源群組刀中，選取 [**存取控制**]。

     ![選取 [使用者]](./media/resource-group-create-service-principal-portal/select-users.png)

     下列步驟也適用於任何範圍。

2. 選取 [**新增**]。

     ![選取 [新增](./media/resource-group-create-service-principal-portal/select-add.png)

3. 選取 [**讀取者**角色 （或您想要指派的應用程式的任意角色）。

     ![選取角色](./media/resource-group-create-service-principal-portal/select-role.png)

4. 當您第一次看到您可以新增至角色的使用者清單時，您就不會看到應用程式。 您只會看到群組和使用者。

     ![顯示使用者](./media/resource-group-create-service-principal-portal/show-users.png)

5. 若要尋找您的應用程式，您必須搜尋。 開始輸入您的應用程式的名稱，然後變更可用選項的清單。 當您看到清單中，請選取 [應用程式]。

     ![指派給角色](./media/resource-group-create-service-principal-portal/assign-to-role.png)

6. 選取 [**好的**完成指派角色]。 您現在應該會看到您在清單中的 [指派給角色的 [資源] 群組的用途的應用程式。


如需有關如何指定使用者和角色透過入口網站應用程式的詳細資訊，請參閱[使用角色指派管理 Azure 訂閱資源的存取權](role-based-access-control-configure.md#manage-access-using-the-azure-management-portal)。

## <a name="sample-applications"></a>範例應用程式

下列範例應用程式顯示如何服務主要身分登入。

**.NET**

- [部署 SSH 啟用 VM.net 範本](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-template-deployment/)
- [管理 Azure 資源和.NET 資源群組](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-resources-and-groups/)

**Java**

- [快速入門-部署使用 Azure 資源管理員範本-資源 java](https://azure.microsoft.com/documentation/samples/resources-java-deploy-using-arm-template/)
- [快速入門-管理資源群組-資源 java](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group//)

**Python**

- [部署 SSH 啟用 VM Python 中的範本](https://azure.microsoft.com/documentation/samples/resource-manager-python-template-deployment/)
- [管理 Azure 資源與資源群組 Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/)

**Node.js**

- [部署 SSH 啟用 VM Node.js 中的範本](https://azure.microsoft.com/documentation/samples/resource-manager-node-template-deployment/)
- [管理 Azure 資源與資源群組 Node.js](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/)

**[注音標示**

- [部署 SSH 啟用 VM 與 [注音標示的範本](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-template-deployment/)
- [管理 Azure 資源與資源群組並列文字](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)


## <a name="next-steps"></a>後續步驟

- 若要瞭解指定安全性原則，請參閱[Azure 角色型存取控制](./active-directory/role-based-access-control-configure.md)。  
- 這些步驟的示範短片，請參閱[Azure 與 Azure Active Directory 資源的 [啟用以程式設計方式管理](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Enabling-Programmatic-Management-of-an-Azure-Resource-with-Azure-Active-Directory)。

