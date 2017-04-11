<properties 
    pageTitle="開始使用 Azure 搜尋管理 REST API |Microsoft Azure |裝載的雲端搜尋服務" 
    description="管理您裝載的雲端管理 REST API Azure 搜尋服務" 
    services="search" 
    documentationCenter="" 
    authors="HeidiSteen" 
    manager="jhubbard" 
    editor=""/>

<tags 
    ms.service="search" 
    ms.devlang="rest-api" 
    ms.workload="search" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.date="10/17/2016" 
    ms.author="heidist"/>

# <a name="get-started-with-azure-search-management-rest-api"></a>Azure 搜尋管理 REST API 快速入門
> [AZURE.SELECTOR]
- [入口網站](search-manage.md)
- [PowerShell](search-manage-powershell.md)
- [REST API](search-get-started-management-api.md)

Azure 搜尋其餘管理 API 是以程式設計方式的其他方法來執行入口網站中的 [系統管理工作。 服務管理作業包括建立或刪除服務、 縮放服務，以及管理鍵。 本教學課程隨附示範服務管理 API 範例用戶端應用程式。 也包含設定步驟，才能執行您的本機的開發環境中的範例。

若要完成此教學課程中，您將需要︰

- Visual Studio 2012 或 2013
- 範例用戶端應用程式下載

在完成教學課程，兩個服務會佈建︰ Azure 搜尋和 Azure Active Directory (AD)。 此外，您將建立的 Azure 中建立您的用戶端應用程式與資源管理員結束點之間的信任的 AD 應用程式。

您必須完成本教學課程 Azure 帳戶。


##<a name="download-the-sample-application"></a>下載的範例應用程式

本教學課程為基礎撰寫 C#，您可以編輯並在 Visual Studio 2012 或 2013年中執行的 Windows 主控台應用程式

您可以在[Azure 搜尋.NET 管理 API 示範](https://github.com/Azure-Samples/search-dotnet-management-api/)Github 上尋找用戶端應用程式。


##<a name="configure-the-application"></a>設定應用程式

您可以執行的範例應用程式之前，您必須先啟用驗證，以便能接受邀請傳送至資源管理員端點的用戶端應用程式。 驗證需求產生與[Azure 資源管理員](https://msdn.microsoft.com/library/azure/dn790568.aspx)，也就是透過 API，包括搜尋服務管理相關要求所有入口網站相關作業的基礎。 Azure 搜尋服務管理 API 只要副檔名的 Azure 資源管理員中，而因此繼承相依性。  

Azure 資源管理員需要為其身分識別提供者的 Azure Active Directory 服務。 

若要取得存取權杖，可讓達到資源管理員的要求，用戶端應用程式會包括程式碼區段的通話 Active Directory。 程式碼區段中，加上必要的步驟，使用程式碼區段中，借用從這篇文章︰[驗證 Azure 資源管理員的要求](http://msdn.microsoft.com/library/azure/dn790557.aspx)。

您可以遵循上述的連結中的指示，或使用此文件中的步驟，如果您想要瀏覽逐步教學課程。

在此區段中，您將會執行下列工作︰

1. 建立 AD 服務
1. 建立 AD 應用程式
1. 將 AD 應用程式設定註冊您下載的範例用戶端應用程式的詳細資料
1. 載入範例用戶端應用程式中的值，將會用來取得其要求的授權

> [AZURE.NOTE] 下列連結提供背景用 Azure Active Directory 驗證用戶端資源管理員的要求︰ [Azure 資源管理員](http://msdn.microsoft.com/library/azure/dn790568.aspx)與[Azure Active Directory](http://msdn.microsoft.com/library/azure/jj673460.aspx)[驗證 Azure 資源管理員的要求](http://msdn.microsoft.com/library/azure/dn790557.aspx)。

###<a name="create-an-active-directory-service"></a>建立 Active Directory 服務

1. [Azure 入口網站](https://manage.windowsazure.com)登入。

2. 向下捲動左側的導覽窗格，然後按一下 [ **Active Directory**。

4. 按一下 [**新增**] 以開啟 [**應用程式服務** | **Active Directory**。 在此步驟中，您會建立新的 Active Directory 服務。 這項服務會裝載 AD 應用程式，您會定義從現在的幾個步驟。 建立新的服務，可協助隔離教學課程中，您可能已經主控 Azure 中的其他應用程式。

5. 按一下 [**目錄** | **建立自訂**。

6. 輸入服務名稱、 網域和地理位置。 網域必須是唯一的。 按一下建立服務的核取記號。

     ![][5]

###<a name="create-a-new-ad-application-for-this-service"></a>建立新的 AD 應用程式，這項服務

1. 選取您剛剛建立的 「 SearchTutorial 」 Active Directory 服務。

2. 在頂端的功能表中，按一下 [**應用程式**。 
 
3. 按一下 [**新增應用程式**]。 AD 應用程式儲存會將它做為身分識別提供者的用戶端應用程式的相關資訊。  
 
4. 選擇 [**新增組織內部開發的應用程式**。 這個選項提供登錄設定未發佈至應用程式庫的應用程式。 用戶端應用程式不是應用程式庫的一部分，因為這會很適合以下在本教學課程。

     ![][6]
 
5. 輸入名稱，例如 「 Azure-搜尋-管理員 」。

6. 應用程式類型，請選擇 [**原生的用戶端應用程式**]。 這是正確的範例應用程式。發生為 Windows 用戶端 （主控台） 應用程式，不是 web 應用程式。

     ![][7]
 
7. 在重新導向 URI，輸入 「 http://localhost/Azure-Search-Manager-App 」。 這樣的 Azure Active Directory 的 URI 會重新導向回應 OAuth 2.0 的授權要求使用者代理程式。 值不需要是實際的端點，但必須是有效的 URI。 

    本教學課程中的目的，值可以是任何項目，但您輸入的項目會變成範例應用程式中的系統管理連線所需的資訊。 
 
7. 按一下 [核取記號，以建立 Active Directory 應用程式]。 在左側的功能窗格中，您應該會看到 「 Azure-搜尋-管理員-應用程式]。

###<a name="configure-the-ad-application"></a>設定 AD 應用程式
 
9. 按一下 [AD 應用程式時，「 Azure-搜尋-管理員-應用程式 」，您剛剛建立的。 您應該會看到它列在左側的功能窗格中。

10. 按一下頂端的功能表中的 [**設定**]。
 
11. 權限下捲動並選取**Azure 管理 API**。 您可以在此步驟中，指定 API （在此例中 Azure 資源管理員 API），用戶端應用程式所需的存取權，以及存取層級需要。

12. 在 [委派的權限]，按一下 [下拉式清單並選取**存取 Azure 服務管理 (預覽**)。
 
     ![][8]
 
13. 儲存變更。 

請在 [應用程式設定] 頁面保持開啟。 在下一個步驟中，您將值，複製此頁面，並輸入他們的範例應用程式。

###<a name="load-the-sample-application-program-with-registration-and-subscription-values"></a>載入範例應用程式，以註冊和訂閱的值

在此區段中，您要編輯的方案在 Visual Studio 中，替代從入口網站取得有效的值。
Program.cs 頂端附近，顯示您要新增的值︰

        private const string TenantId = "<your tenant id>";
        private const string ClientId = "<your client id>";
        private const string SubscriptionId = "<your subscription id>";
        private static readonly Uri RedirectUrl = new Uri("<your redirect url>");

如果您還沒有有[下載 Github 範例應用程式](https://github.com/Azure-Samples/search-dotnet-management-api/)，您必須將其這個步驟。

1. Visual Studio 中開啟**ManagementAPI.sln** 。

2. 開啟 Program.cs。

3. 提供`ClientId`。 在廣告應用程式設定] 頁面保持開啟上述步驟中複製入口網站中的 [AD 應用程式設定] 頁面中的用戶端識別碼與貼入 Program.cs。

4. 提供`RedirectUrl`。 複製重新導向 URI 相同入口網站頁面上，並將它貼到 Program.cs。

    ![][9]

5. 提供`TenantID.` 
    - 返回 Active Directory |SearchTutorial （服務）。 
    - 按一下 [頂端列中的 [**應用程式**]。 
    - 按一下 [**檢視端點**底部的頁面]。 
    - 複製 OAUTH 2.0 授權端點清單的底部。 
    - 端點貼上 TenantID，修整的租用戶識別碼以外的所有 URI 參數值

    指定 「 https://login.windows.net/55e324c7-1656-4afe-8dc3-43efcd4ffa50/oauth2/authorize?api-version=1.0 」，刪除 「 55e324c7-1656-4afe-8dc3-43efcd4ffa50 」 以外的所有項目。

    ![][10]

6. 提供`SubscriptionID`。
    - 移至主要入口網站頁面。
    - 按一下左側的功能窗格底部的**設定**]。
    - 從 [訂閱] 索引標籤中，複製訂閱 ID，然後將它貼到 Program.cs。

7. 儲存，然後建置解決方案。


##<a name="explore-the-application"></a>瀏覽應用程式

新增第一個方法呼叫中斷點，好讓您可以逐步執行程式。 按**F5**執行應用程式，然後按下**F11**逐步執行程式碼。

範例應用程式所建立的現有 Azure 訂閱的免費 Azure 搜尋服務。 如果您的訂閱已經有可用的服務，就無法範例應用程式。 允許只有一個免費搜尋服務，每個訂閱。

1. 從方案總管中開啟 Program.cs，然後移至 [主要 (字串 [void) 函數。 
 
3. 請注意**ExecuteArmRequest**會用於執行對 Azure 資源管理員端點，要求`https://management.azure.com/subscriptions`指定`subscriptionID`。 這個方法在整個程式用於使用 Azure 資源管理員 API 或搜尋管理 API 執行作業。

3. 要求至 Azure 資源管理員必須驗證，並授權。 這是使用由**ExecuteArmRequest**方法，從[驗證 Azure 資源管理員要求](http://msdn.microsoft.com/library/azure/dn790557.aspx)借呼叫**GetAuthorizationHeader**方法。 請注意， **GetAuthorizationHeader**通話`https://management.core.windows.net`取得存取權杖。

4. 系統提示您登入的使用者名稱和密碼是有效，您的訂閱。

5. 接下來，新的 Azure 搜尋服務已註冊 Azure 資源管理員提供者。 同樣地，這是**ExecuteArmRequest**用的方法，這次，建立您的訂閱，透過 Azure 上搜尋服務`providers/Microsoft.Search/register`。 

6. 程式的其餘部分使用[Azure 搜尋管理 REST API](http://msdn.microsoft.com/library/dn832684.aspx)。 請注意，`api-version`此 API 是 Azure 資源管理員 api 版本不同。 例如，`/listAdminKeys?api-version=2014-07-31-Preview`顯示`api-version`Azure 搜尋管理 REST API。

    下一系列的作業擷取服務定義您剛建立管理員 api 金鑰、 重新產生及擷取鍵、 變更複本和分割，以及最後刪除服務。

    變更服務複本或磁碟分割計數時, 預期會失敗這個動作，如果您使用免費的版本。 只有標準版可以讓使用額外的磁碟分割的複本。

    刪除服務是最後一個作業。

##<a name="next-steps"></a>後續步驟

後無法完成此教學課程，可能會想要進一步瞭解服務管理或 Active Directory 服務使用的驗證︰

- 深入瞭解如何使用 Active Directory 整合的用戶端應用程式。 請參閱[整合 Azure Active Directory 中的應用程式](http://msdn.microsoft.com/library/azure/dn151122.aspx)。
- 深入瞭解其他服務管理中的作業 Azure。 請參閱[管理您的服務](http://msdn.microsoft.com/library/azure/dn578292.aspx)。

<!--Anchors-->
[Download the sample application]: #Download
[Configure the application]: #config
[Explore the application]: #explore
[Next Steps]: #next-steps

<!--Image references-->
[5]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-Service.PNG
[6]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-App.PNG
[7]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-App-prop.PNG
[8]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-ConfigPermissions.PNG
[9]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-ConfigPage.PNG
[10]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-OAuthEndpoint.PNG

<!--Link references-->
[Manage your search solution in Microsoft Azure]: search-manage.md
[Azure Search development workflow]: search-workflow.md
[Create your first azure search solution]: search-create-first-solution.md
[Create a geospatial search app using Azure Search]: search-create-geospatial.md


 
