<properties
    pageTitle="建立非互動式驗證.NET HDInsight applciations |Microsoft Azure"
    description="瞭解如何建立非互動式驗證.NET HDInsight 應用程式。"
    editor="cgronlun"
    manager="jhubbard"
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/02/2016"
    ms.author="jgao"/>

# <a name="create-non-interactive-authentication-net-hdinsight-applications"></a>建立非互動式驗證.NET HDInsight 應用程式

您可以執行.NET Azure HDInsight 應用程式在應用程式本身的身分識別 （非互動式） 或 （互動式） 應用程式的登入使用者的身分識別下。 互動式應用程式的範例，請參閱[使用 HDInsight.NET SDK 的送出登錄區/豬/Sqoop 工作](hdinsight-submit-hadoop-jobs-programmatically.md#submit-hivepigsqoop-jobs-using-hdinsight-net-sdk)。 本文將示範如何建立非互動式驗證.NET 應用程式連線至 Azure HDInsight 並提交登錄區工作。

從.NET 應用程式，您將需要︰

- 您 Azure 訂閱的租用戶識別碼
- Azure 目錄應用程式的用戶端識別碼
- Azure 目錄應用程式密碼金鑰。  

主要的程序包括以下步驟︰

2. 建立 Azure 目錄應用程式。
2. 將角色指派至 AD 應用程式。
3. 開發用戶端應用程式。


##<a name="prerequisites"></a>必要條件

- HDInsight 叢集。 您可以建立一個使用中的[快速入門教學課程中](hdinsight-hadoop-linux-tutorial-get-started.md#create-cluster)的指示進行。 




## <a name="create-azure-directory-application"></a>建立 Azure 目錄應用程式 
當您建立的 Active Directory 應用程式時，它會建立應用程式及服務本金。 您可以執行應用程式的身分識別的應用程式。

目前，您必須使用 Azure 傳統入口網站，來建立新的 Active Directory 應用程式。 這項功能將會新增至新版本 Azure 入口網站。 您也可以執行下列步驟，透過 PowerShell 的 Azure 或 Azure CLI。 如需有關如何使用 PowerShell 或 CLI 本金服務的詳細資訊，請參閱[驗證服務本金與 Azure 資源管理員](../resource-group-authenticate-service-principal.md)。

**若要建立 Azure 目錄應用程式**

1.  [Azure 傳統入口網站]( https://manage.windowsazure.com/)登入。
2.  從左窗格中選取 [ **Active Directory** ]。

    ![Azure 傳統的入口網站 active directory](.\media\hdinsight-create-non-interactive-authentication-dotnet-application\active-directory.png)
    
3.  選取您想要用來建立新的應用程式目錄]。 應該就能現有項目。
4.  按一下 [從現有的應用程式] 清單頂端的 [**應用程式**]。
5.  按一下 [從上至下加入新的應用程式的 [**新增**]。
6.  輸入**名稱**，選取 [ **Web 應用程式及/或 Web API**，，再按一下 [**下一步**。

    ![新的 azure active directory 應用程式](.\media\hdinsight-create-non-interactive-authentication-dotnet-application\hdinsight-add-ad-application.png)

7.  輸入**登入 URL** ] 與 [**應用程式識別碼 URI**。 **登開啟 URL**，提供 URI 描述您的應用程式的網站。 不會驗證存在的網站。 應用程式識別碼 uri，提供 URI，識別您的應用程式。 然後按一下 [**完成**]。
花一些時間才能建立應用程式。  應用程式建立後，入口網站會顯示您的新的應用程式快速 Glace 的頁面。 不關閉入口網站。 

    ![新的 azure active directory 應用程式屬性](.\media\hdinsight-create-non-interactive-authentication-dotnet-application\hdinsight-add-ad-application-properties.png)

**若要取得應用程式用戶端識別碼和密碼的金鑰**

1.  新建立的 AD 應用程式頁面上，按一下 [從上方的功能表的 [**設定**]。
2.  請**用戶端識別碼**的複本。 您必須將其.NET 應用程式中。
3.  底下**鍵**，按一下 [**選取期間**] 下拉式清單，然後選取**1 年**或**2 的年數**。 您儲存設定之前，不會顯示的值。
4.  按一下 [頁面底端的 [**儲存**]。 私密金鑰出現時，複製的金鑰。 您必須將其.NET 應用程式中。

##<a name="assign-ad-application-to-role"></a>指派給角色的廣告應用程式

您必須指派[角色](../active-directory/role-based-access-built-in-roles.md)執行動作的權限授與應用程式。 您可以設定範圍層級的訂閱，資源] 群組中或資源。 權限繼承較低層級的範圍 （例如，新增應用程式讀取者角色資源群組來表示它可以讀取資源群組和它所包含任何資源）。 在本教學課程中，您會在資源群組層級設定範圍。  Azure 傳統入口網站不支援資源群組，因此此組件會有 Azure 入口網站中執行。 

**若要新增至 AD 應用程式的擁有者角色**

1.  [Azure 入口網站](https://portal.azure.com)登入。
2.  按一下 [從左窗格的 [**資源群組**]。
3.  按一下包含您會在其中執行稍後在本教學課程登錄區查詢 HDInsight 叢集資源群組]。 如果有太多的資源群組，您可以使用篩選器。
4.  按一下 [從叢集刀 [**存取**]。

    ![雲端和雷電圖示 = 快速入門](./media/hdinsight-hadoop-create-linux-cluster-portal/quickstart.png)
5.  按一下 [從**使用者**刀的 [**新增**]。
6.  請依照您在最後一個程序中建立的指令新增至 AD 應用程式的**擁有者**角色。 當您順利完成它時，您應該看到的擁有者角色的使用者刀中所列的應用程式。


##<a name="develop-hdinsight-client-application"></a>開發 HDInsight 用戶端應用程式

建立 C#.net 主控台應用程式[中 HDInsight 的送出 Hadoop 工作](hdinsight-submit-hadoop-jobs-programmatically.md#submit-hivepigsqoop-jobs-using-hdinsight-net-sdk)中的指示執行。 然後取代 GetTokenCloudCredentials 方法下列動作︰

    public static TokenCloudCredentials GetTokenCloudCredentials(string tenantId, string clientId, SecureString secretKey)
    {
        var authFactory = new AuthenticationFactory();

        var account = new AzureAccount { Type = AzureAccount.AccountType.ServicePrincipal, Id = clientId };

        var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];

        var accessToken =
            authFactory.Authenticate(account, env, tenantId, secretKey, ShowDialog.Never)
                .AccessToken;

        return new TokenCloudCredentials(accessToken);
    }

若要擷取的租用戶識別碼透過 PowerShell:

    Get-AzureRmSubscription

或 Azure CLI:

    azure account show --json

      
## <a name="see-also"></a>另請參閱

- [提交 Hadoop HDInsight 中的工作](hdinsight-submit-hadoop-jobs-programmatically.md)
- [建立 Active Directory 應用程式與服務主要使用入口網站](../resource-group-create-service-principal-portal.md)
- [驗證服務主要與 Azure 資源管理員](../resource-group-authenticate-service-principal.md)
- [Azure 角色型存取控制](../active-directory/role-based-access-control-configure.md)
