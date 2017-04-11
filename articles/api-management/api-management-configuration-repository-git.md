<properties 
    pageTitle="如何儲存及設定使用給您 API 管理服務設定" 
    description="瞭解如何儲存及設定使用給您 API 管理服務設定。" 
    services="api-management" 
    documentationCenter="" 
    authors="steved0x" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>


# <a name="how-to-save-and-configure-your-api-management-service-configuration-using-git"></a>如何儲存及設定使用給您 API 管理服務設定

>[AZURE.IMPORTANT] 給設定 API 管理目前是在預覽中。 功能完成，但將預覽，因為我們積極希望此功能的意見反應。 有可能我們可能會讓新客戶意見反應，來回應變更，因此我們建議您不是根據生產環境中使用的功能。 如果您有任何意見反應或問題，請讓我們知道， `apimgmt@microsoft.com`。

每個 API 管理服務執行個體維護設定資料庫包含設定與服務執行個體的中繼資料的相關資訊。 變更可以服務執行個體所做的變更 publisher 入口網站中的設定，或使用 PowerShell cmdlet，或進行 REST API 通話。 除了這些方法，您也可以管理您的服務執行個體設定使用給，例如啟用服務管理案例︰

-   設定版本設定-下載並儲存您的服務設定的其他版本
-   大量設定變更-變更您的服務設定您的本機存放庫中的多個部分並與單一作業整合至伺服器的變更
-   熟悉給 toolchain 和工作流程-使用給工具與您已熟悉的工作流程

下圖顯示不同的方式來設定您的 API 管理服務執行個體的概觀。

![給設定][api-management-git-configure]

當您變更您使用 publisher 入口網站、 PowerShell cmdlet 或 REST API 的服務，表示您正在管理您的設定資料庫使用服務`https://{name}.management.azure-api.net`端點，如下圖所示右側的圖表。 圖表的左側說明如何管理使用給您的服務設定，並為您的服務給存放庫位於`https://{name}.scm.azure-api.net`。

下列步驟提供管理使用給您 API 管理服務執行個體的概觀。

1.  啟用給存取您的服務
2.  將您的服務設定資料庫儲存至您就可以給存放庫
3.  複製到本機電腦給 repo
4.  傳送最新的 repo 下您的本機電腦，並返回您 repo 的認可與推入變更
5.  將您的服務設定資料庫部署您 repo 的變更

本文將說明如何啟用並使用給管理您的服務設定，並提供的檔案和資料夾，就可以給存放庫中的參照。

## <a name="to-enable-git-access"></a>若要啟用給存取

您可以快速檢視給圖示右上角的 publisher 入口網站檢視給設定的狀態。 在此範例中，就可以給存取尚未尚未啟用。

![給狀態][api-management-git-icon-enable]

若要檢視並設定您就可以給設定的設定，您可以按一下就可以給] 圖示，或按一下 [**安全性**] 功能表並瀏覽至 [**設定存放庫**] 索引標籤。

![啟用給][api-management-enable-git]

若要啟用給存取，請核取 [**啟用給存取**核取方塊。

隨後會儲存變更，並顯示確認訊息。 請注意，就可以給圖示已變更色彩，表示已啟用給存取並的狀態訊息現在會指出的那里未儲存的變更儲存機制。 這是因為 API 管理服務設定資料庫尚未儲存到儲存機制。

![啟用給][api-management-git-enabled]

>[AZURE.IMPORTANT] 任何不在內容會儲存在存放庫，並會保留在其歷程記錄，直到您所定義的機密停用，並重新啟用給存取。 內容提供管理常數字串值，包括機密，所有的 API 設定及原則，，因此您不必將它們儲存在您的原則陳述式中直接安全的地方。 如需詳細資訊，請參閱[如何使用中 Azure API 管理原則的屬性](api-management-howto-properties.md)。

啟用或停用給使用 REST API 的存取權的資訊，請參閱[啟用或停用給使用 REST API 的存取權](https://msdn.microsoft.com/library/dn781420.aspx#EnableGit)。

## <a name="to-save-the-service-configuration-to-the-git-repository"></a>若要儲存的服務設定到給儲存機制

複製存放庫之前的第一個步驟是將儲存至存放庫的目前狀態的服務設定。 按一下 [**儲存到儲存機制的設定**。

![儲存設定][api-management-save-configuration]

在確認畫面上進行任何所需的變更，然後按一下 [**確定]**儲存]。

![儲存設定][api-management-save-configuration-confirm]

一段時間後儲存設定，並將存放庫的設定狀態會顯示，包括日期及時間的最後一個設定以及上次同步處理服務設定和儲存機制之間。

![設定狀態][api-management-configuration-status]

設定儲存到儲存機制之後，可以複製。

執行此作業使用 REST API 的詳細資訊，請參閱[認可使用 REST API 的設定快照](https://msdn.microsoft.com/library/dn781420.aspx#CommitSnapshot)。

## <a name="to-clone-the-repository-to-your-local-machine"></a>若要複製到本機電腦存放庫

若要複製之儲存機制，需要 URL 您存放庫、 使用者名稱和密碼。 使用者名稱和 URL 會顯示靠近頂端的 [**設定存放庫**] 索引標籤。

![給複製][api-management-configuration-git-clone]

密碼會產生底部的 [**設定存放庫**] 索引標籤。

![產生密碼][api-management-generate-password]

若要產生密碼**到期**的設定為所要的到期日期和時間，必須先確定，再按一下 [**產生的權杖**。

![密碼][api-management-password]

>[AZURE.IMPORTANT] 記下密碼。 當您離開此頁面將不會再顯示密碼。

下列範例使用從[Windows 版給](http://www.git-scm.com/downloads)給艦隊工具，但您可以使用熟悉的任何給工具。

開啟您就可以給工具中所需的資料夾，並執行下列命令以複製到本機電腦，使用命令 publisher 入口網站提供給存放庫。

    git clone https://bugbashdev4.scm.azure-api.net/ 

提供使用者名稱和密碼出現提示時。

如果您收到任何錯誤，請試著修改您`git clone`] 命令，包括使用者名稱和密碼，如下列範例所示。

    git clone https://username:password@bugbashdev4.scm.azure-api.net/

如果這可錯誤，請嘗試 URL 編碼密碼的部分] 命令。 若要執行此動作的其中一個快速方法是開啟 Visual Studio 中，然後在**即時運算] 視窗**中執行下列命令。 若要開啟**即時運算] 視窗**，Visual Studio 中開啟任何方案或專案 （或建立新的空白主控台應用程式），然後選擇 [ **Windows****即時運算****偵錯**] 功能表。

    ?System.NetWebUtility.UrlEncode("password from publisher portal")

[密碼] 使用編碼及您的使用者名稱和存放庫位置來建構給] 命令。

    git clone https://username:url encoded password@bugbashdev4.scm.azure-api.net/

一旦複製存放庫您可以檢視，並在您的本機檔案系統中使用它。 如需詳細資訊，請參閱[的本機給存放庫的檔案與資料夾結構參照](#file-and-folder-structure-reference-of-local-git-repository)。

## <a name="to-update-your-local-repository-with-the-most-current-service-instance-configuration"></a>若要更新的最新的服務執行個體設定您的本機存放庫

如果您在 publisher 入口網站的 API 管理服務執行個體中進行變更，或使用 REST API，您必須儲存這些變更到儲存機制之前，您可以使用最新的變更來更新您的本機存放庫。 若要這麼做，請按一下 [**儲存到儲存機制設定**publisher 入口網站中的 [**設定存放庫**] 索引標籤上，然後發出下列命令，在您的本機存放庫中。

    git pull

後再繼續執行`git pull`確定您在資料夾中的 [您的本機存放庫。 如果您已經完成`git clone`命令，然後您必須變更目錄您 repo 執行類似下列命令。

    cd bugbashdev4.scm.azure-api.net/

## <a name="to-push-changes-from-your-local-repo-to-the-server-repo"></a>若要從您的本機 repo 的變更推入伺服器 repo

若要從您的本機存放庫的變更推入伺服器存放庫中，您必須確認您的變更，然後再傳送至伺服器存放庫。 若要確認您的變更，開啟您就可以給命令工具、 切換至您的本機存放庫，目錄並提交下列命令。

    git add --all
    git commit -m "Description of your changes"

若要推所有的認可到伺服器，請執行下列命令。

    git push

## <a name="to-deploy-any-service-configuration-changes-to-the-api-management-service-instance"></a>若要部署 API 管理服務執行個體的任何服務設定變更

本機變更已確認並推入至伺服器存放庫內之後，可以將其部署 API 管理的服務執行個體。

![部署][api-management-configuration-deploy]

執行此作業使用 REST API 的詳細資訊，請參閱[使用 REST API 設定資料庫的部署給變更](https://msdn.microsoft.com/library/dn781420.aspx#DeployChanges)。

## <a name="file-and-folder-structure-reference-of-local-git-repository"></a>本機給存放庫的檔案與資料夾結構參照

檔案與資料夾本機給存放庫包含的服務執行個體的設定資訊。

| 項目                       | 描述                                                                                |
|-------------------------   |--------------------------------------------------------------------------------------------|
| 根 api 管理資料夾 | 包含服務執行個體的最上層的設定                                  |
| api 資料夾                | 包含服務執行個體中的 api 設定                            |
| 群組] 資料夾              | 包含服務執行個體中的群組的設定                          |
| 原則] 資料夾            | 包含服務執行個體中的原則                                              |
| portalStyles 資料夾        | 包含的服務執行個體中的開發人員入口網站自訂 |
| 產品] 資料夾            | 包含產品服務執行個體中的設定                        |
| [範本] 資料夾           | 包含的服務執行個體中的電子郵件範本                 |

每個資料夾可包含一或多個檔案，以及在某些情況下一個或多個資料夾，例如每個 API、 產品，或群組的資料夾。 每個資料夾中的檔案是特定的資料夾名稱描述的實體類型。

| 檔案類型 | 用途                                                                |
|-----------|------------------------------------------------------------------------|
| json      | 個別的實體的設定資訊                  |
| html      | 實體，通常會顯示在開發人員入口網站中的相關說明 |
| xml       | 原則陳述式                                                      |
| css       | 開發人員入口網站的自訂樣式表                        |

這些檔案您可以建立、 刪除、 編輯及管理您的本機檔案系統，並變更部署返回您的 API 管理服務執行個體。

>[AZURE.NOTE] 下列項目不在給存放庫中，而且無法使用給設定。
>
>-    使用者
>-    訂閱
>-    屬性
>-    開發人員入口網站的實體以外的樣式

### <a name="root-api-management-folder"></a>根 api 管理資料夾

根`api-management`資料夾包含`configuration.json`包含最上層資訊服務執行個體以下列格式的檔案。

    {
      "settings": {
        "RegistrationEnabled": "True",
        "UserRegistrationTerms": null,
        "UserRegistrationTermsEnabled": "False",
        "UserRegistrationTermsConsentRequired": "False",
        "DelegationEnabled": "False",
        "DelegationUrl": "",
        "DelegatedSubscriptionEnabled": "False",
        "DelegationValidationKey": ""
      },
      "$ref-policy": "api-management/policies/global.xml"
    }

前四個設定 (`RegistrationEnabled`， `UserRegistrationTerms`， `UserRegistrationTermsEnabled`，及`UserRegistrationTermsConsentRequired`) 對應至 [**安全性**] 區段中的 [**身分識別**] 索引標籤上的下列設定。

| 設定身分識別                     | 對應至                                               |
|--------------------------------------|-------------------------------------------------------|
| RegistrationEnabled                  | **重新導向匿名使用者登入頁面**] 核取方塊 |
| UserRegistrationTerms                | **在 [使用者註冊使用規定**文字方塊               |
| UserRegistrationTermsEnabled         | **顯示在註冊] 頁面上的使用規定**] 核取方塊         |
| UserRegistrationTermsConsentRequired | **需要同意**] 核取方塊                          |

![設定身分識別][api-management-identity-settings]

下一步四種設定 (`DelegationEnabled`， `DelegationUrl`， `DelegatedSubscriptionEnabled`，及`DelegationValidationKey`) 對應至 [**安全性**] 區段中的 [**委派**] 索引標籤上的下列設定。

| 委派設定           | 對應至                                    |
|------------------------------|--------------------------------------------|
| DelegationEnabled            | **登入與註冊的代理人**] 核取方塊    |
| DelegationUrl                | **委派端點 URL** ] 文字方塊        |
| DelegatedSubscriptionEnabled | **代理人產品訂閱**] 核取方塊 |
| DelegationValidationKey      | **代理人驗證金鑰**文字方塊        |

![委派設定][api-management-delegation-settings]

最後的設定， `$ref-policy`，對應至全域原則陳述式檔案的服務執行個體。

### <a name="apis-folder"></a>api 資料夾

`apis`資料夾的每個 API，其中包含下列項目服務執行個體中包含的資料夾。

-   `apis\<api name>\configuration.json`-這是 API 的設定，包含後端服務 URL] 與 [作業的相關資訊。 這是相同的資訊如果您要撥打[取得特定的 API](https://msdn.microsoft.com/library/azure/dn781423.aspx#GetAPI)含有要傳回`export=true`中`application/json`格式。
-   `apis\<api name>\api.description.html`-這是 API 的描述，而且對應到`description` [API 實體](https://msdn.microsoft.com/library/azure/dn781423.aspx#EntityProperties)屬性。
-   `apis\<api name>\operations\`-這個資料夾包含`<operation name>.description.html`對應至 API 中作業的檔案。 每個檔案包含單一對應到 API 中作業的描述`description`[作業實體](https://msdn.microsoft.com/library/azure/dn781423.aspx#OperationProperties)REST API 的屬性。

### <a name="groups-folder"></a>群組] 資料夾

`groups`資料夾包含的服務執行個體中已定義之每個群組的資料夾。

-   `groups\<group name>\configuration.json`-這是群組的設定。 這是您為數列呼叫[取得特定群組](https://msdn.microsoft.com/library/azure/dn776329.aspx#GetGroup)作業時要傳回的相同資訊。
-   `groups\<group name>\description.html`-這是群組的描述，而且對應到`description`[群組實體](https://msdn.microsoft.com/library/azure/dn776329.aspx#EntityProperties)屬性。

### <a name="policies-folder"></a>原則] 資料夾

`policies`資料夾包含您的服務執行個體的原則陳述式。

-   `policies\global.xml`-包含在您的服務執行個體的全域範圍定義的原則。
-   `policies\apis\<api name>\`-如果您有任何 API 範圍定義的原則，均在此資料夾中。
-   `policies\apis\<api name>\<operation name>\`資料夾-如果您有任何作業範圍定義的原則，均在此資料夾中`<operation name>.xml`對應至每個作業的原則陳述式的檔案。
-   `policies\products\`-如果您有任何在產品範圍中定義的原則，均在此資料夾中，其中包含`<product name>.xml`對應至每個產品的原則陳述式的檔案。

### <a name="portalstyles-folder"></a>portalStyles 資料夾

`portalStyles`資料夾包含服務執行個體的開發人員入口網站自訂的設定和樣式工作表。

-   `portalStyles\configuration.json`-包含開發人員入口網站所使用的樣式表的名稱
-   `portalStyles\<style name>.css`為每個`<style name>.css`檔案包含開發人員入口網站的樣式 (`Preview.css`和`Production.css`預設)。

### <a name="products-folder"></a>產品] 資料夾

`products`資料夾包含每個產品的服務執行個體中定義的資料夾。

-   `products\<product name>\configuration.json`-這是針對產品的設定。 這是您為數列呼叫[取得特定產品](https://msdn.microsoft.com/library/azure/dn776336.aspx#GetProduct)作業時要傳回的相同資訊。
-   `products\<product name>\product.description.html`-這是產品的說明，而且對應到`description`REST API 中的[產品實體](https://msdn.microsoft.com/library/azure/dn776336.aspx#Product)的屬性。

### <a name="templates"></a>範本

`templates`資料夾包含設定[電子郵件範本](api-management-howto-configure-notifications.md)的服務執行個體。

-   `<template name>\configuration.json`-這是電子郵件範本的設定。
-   `<template name>\body.html`-這是電子郵件範本的本文。

## <a name="next-steps"></a>後續步驟

如要管理您的服務執行個體的其他方法的詳細資訊，請參閱︰

-   管理您的服務執行個體，使用下列 PowerShell cmdlet
    -   [服務部署 PowerShell cmdlet 參考](https://msdn.microsoft.com/library/azure/mt619282.aspx)
    -   [服務管理 PowerShell cmdlet 參考](https://msdn.microsoft.com/library/azure/mt613507.aspx)
-   管理您的服務執行個體，在 publisher 入口網站
    -   [管理您的第一個 API](api-management-get-started.md)
-   管理您的服務執行個體，使用 REST API
    -   [API 管理 REST API 參照](https://msdn.microsoft.com/library/azure/dn776326.aspx)

## <a name="watch-a-video-overview"></a>觀看視訊的概觀

> [AZURE.VIDEO configuration-over-git]

[api-management-enable-git]: ./media/api-management-configuration-repository-git/api-management-enable-git.png
[api-management-git-enabled]: ./media/api-management-configuration-repository-git/api-management-git-enabled.png
[api-management-save-configuration]: ./media/api-management-configuration-repository-git/api-management-save-configuration.png
[api-management-save-configuration-confirm]: ./media/api-management-configuration-repository-git/api-management-save-configuration-confirm.png
[api-management-configuration-status]: ./media/api-management-configuration-repository-git/api-management-configuration-status.png
[api-management-configuration-git-clone]: ./media/api-management-configuration-repository-git/api-management-configuration-git-clone.png
[api-management-generate-password]: ./media/api-management-configuration-repository-git/api-management-generate-password.png
[api-management-password]: ./media/api-management-configuration-repository-git/api-management-password.png
[api-management-git-configure]: ./media/api-management-configuration-repository-git/api-management-git-configure.png
[api-management-configuration-deploy]: ./media/api-management-configuration-repository-git/api-management-configuration-deploy.png
[api-management-identity-settings]: ./media/api-management-configuration-repository-git/api-management-identity-settings.png
[api-management-delegation-settings]: ./media/api-management-configuration-repository-git/api-management-delegation-settings.png
[api-management-git-icon-enable]: ./media/api-management-configuration-repository-git/api-management-git-icon-enable.png




