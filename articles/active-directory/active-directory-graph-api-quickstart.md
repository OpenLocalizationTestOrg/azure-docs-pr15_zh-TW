<properties
   pageTitle="Azure AD 圖表 API 的快速入門 |Microsoft Aure"
   description="Azure Active Directory 圖表 API 提供透過 OData REST API 端點 Azure AD 以程式設計方式存取。 應用程式可以使用圖形 API 來執行建立、 讀取、 更新及刪除 (CRUD) 作業目錄資料和物件。"
   services="active-directory"
   documentationCenter="n/a"
   authors="PatAltimore"
   manager="mbaldwin"
   editor=""
   tags=""/>


   <tags
      ms.service="active-directory"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="identity"
      ms.date="09/16/2016"
      ms.author="patricka"/>

# <a name="quickstart-for-the-azure-ad-graph-api"></a>Azure AD 圖表 API 的快速入門

Azure Active Directory (AD) 圖形 API 提供透過 OData REST API 端點 Azure AD 以程式設計方式存取。 應用程式可以使用圖形 API 來執行建立、 讀取、 更新及刪除 (CRUD) 作業目錄資料和物件。 例如，您可以使用圖形 API 建立新的使用者、 檢視或更新使用者的內容、 變更使用者的密碼，核取的角色型存取群組成員資格，停用或刪除的使用者。 如需有關的圖形 API 功能和應用程式案例的詳細資訊，請參閱[Azure AD 圖形 API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)和[Azure AD 圖形 API 先決條件](https://msdn.microsoft.com/library/hh974476.aspx)。 

> [AZURE.IMPORTANT] Azure AD 圖形 API 功能也可以透過[Microsoft Graph](https://graph.microsoft.io/)，包含其他 Microsoft 服務，例如 Outlook、 OneDrive、 OneNote、 規劃和 Office graph 下方，可透過單一端點和單一存取權杖存取 Api 整合 API。

## <a name="how-to-construct-a-graph-api-url"></a>如何建構圖形 API URL

在圖形 API，才能存取目錄資料和物件 （也就是說，資源或實體） 要執行 CRUD 作業，您可以使用根據開啟資料 (OData) 通訊協定的 Url。 包含四個主要部分圖形 API 中使用的 Url︰ 服務根、 租用戶識別碼、 資源路徑和查詢字串選項︰ `https://graph.windows.net/{tenant-identifier}/{resource-path}?[query-parameters]`。 請將下列 URL 範例︰ `https://graph.windows.net/contoso.com/groups?api-version=1.6`。

- **服務根目錄**︰ Azure AD 圖形 API，服務根目錄都是 https://graph.windows.net。
- **租用戶識別碼**︰ 可以是驗證 （已註冊） 的網域名稱，在 contoso.com 上述範例。 它也可以是租用戶物件識別碼 」 myorganiztion 」 或 「 我 」 別名。 如需詳細資訊，請參閱[處理的項目和作業圖形 API 中的）。](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-operations-overview)
- **資源路徑**︰ URL 的本節會指出資源互動 （使用者、 群組、 特定的使用者，或特定群組等等。）在上述範例中，則最上層 「 群組 」 資源設定的地址。 您也可以例如位址特定的實體 」 使用者 / {objectId} 」 或 「 使用者/userPrincipalName 」。
- **查詢參數**︰ 嗎？ 分隔從 [查詢參數] 區段的 [資源路徑] 區段。 圖形 API 中的所有邀請上需要 「 api 版本 」 查詢參數。 圖形 API 也支援下列 OData 查詢選項︰ **$filter**、 **$orderby**、 **$展開**、 **$top**及**$format**。 目前不支援下列查詢選項︰ **$count** **$inlinecount**，與**$skip**。 如需詳細資訊，請參閱[支援的查詢、 篩選和 Azure AD 圖形 API 中的 [分頁選項](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options)。

## <a name="graph-api-versions"></a>圖形 API 版本

在 「 api 版本 」 查詢參數指定圖形 API 邀請的版本。 1.5 及更新版本的版本，您可以使用數字的版本的值。api 版本 = 1.6。 舊版，您可以使用日期字串符合格式 YYYY MM DD;例如的 api 版本 = 2013年-11-08。 預覽功能，使用 [字串 「 beta 」;例如的 api 版本 = beta。 如需有關圖形 API 版本之間的差異的詳細資訊，請參閱[Azure AD 圖形 API 版本設定](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-versioning)。

## <a name="graph-api-metadata"></a>API 的圖表 」 中繼資料

傳回圖形 API 中繼資料檔案，新增 「 $metadata 」 區段後的 URL 的範例中的租用戶識別項，將下列 URL 會傳回中繼資料的圖形檔案總管所使用的示範公司︰ `https://graph.windows.net/GraphDir1.OnMicrosoft.com/$metadata?api-version=1.6`。 若要查看中繼資料的網頁瀏覽器的網址列中，您可以輸入此 URL。 傳回的 CSDL 中繼資料文件將說明實體及複雜的類型、 其屬性和函數和動作所公開之圖形 API 您要求的版本。 省略 api 版本參數會傳回的最新版本的中繼資料。

## <a name="common-queries"></a>常見的查詢

[Azure AD 圖形 API 一般查詢](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options#CommonQueries)會列出可搭配 Azure AD 圖表，包括可用來存取您的目錄中的最上層資源的查詢和查詢，以在您的目錄中執行作業的一般查詢。

例如，`https://graph.windows.net/contoso.com/tenantDetails?api-version=1.6`傳回公司目錄 contoso.com 的資訊。

或者`https://graph.windows.net/contoso.com/users?api-version=1.6`列出所有使用者物件為目錄 contoso.com。

## <a name="using-the-graph-explorer"></a>使用圖形檔案總管

您可以使用 [圖形總管] 中的 Azure AD 圖形 API 來查詢目錄資料，建立您的應用程式。

> [AZURE.IMPORTANT] [圖形總管] 中不支援撰寫或刪除目錄中的資料。 您只能執行 Azure AD 目錄圖形檔案總管上的讀取的作業。

下列是是否您是要瀏覽至 [圖形總管] 中，選取 [使用示範公司]，輸入，您會看到的輸出`https://graph.windows.net/GraphDir1.OnMicrosoft.com/users?api-version=1.6`示範目錄中顯示所有的使用者︰

![Azure AD 圖形 api 總管](./media/active-directory-graph-api-quickstart/graph_explorer.png)

**載入圖形總管**︰ 若要載入工具，瀏覽至[https://graphexplorer.cloudapp.net/](https://graphexplorer.cloudapp.net/)。 按一下 [**使用示範公司**的範例租用戶中執行資料圖形總管]。 您不需要使用示範公司認證。 或者，您也可以按一下 [**登入**並使用 Azure AD 帳戶認證登入您的租用戶對執行 [圖形總管]。 如果您對您自己的租用戶執行圖形檔案總管，您或您的系統管理員必須同意期間登入。 如果您有 Office 365 訂閱，您會自動有 Azure AD 租用戶。 您使用 Office 365 登入認證，事實上，Azure AD 帳戶，以及您可以使用這些認證圖形檔案總管。

**執行查詢**︰ 若要執行查詢時，在邀請的 [文字] 方塊中輸入您的查詢和按一下**取得**或按一下 [**輸入**金鑰]。 結果會顯示在 [回應] 方塊中。 例如，`https://graph.windows.net/graphdir1.onmicrosoft.com /groups?api-version=1.6`會列出示範目錄中的所有群組物件。

請注意下列功能和限制圖形總管] 的︰
- 資源集的自動完成功能。 若要查看此，按一下 [**使用示範公司**]，然後按一下邀請文字方塊 （公司 URL 出現的位置）。 您可以選取資源，從下拉式清單設定。

- 支援 「 我 」 和 「 myorganization 「 處理別名。 例如，您可以使用`https://graph.windows.net/me?api-version=1.6`使用者物件中的 [登入使用者或`https://graph.windows.net/myorganization/users?api-version=1.6`傳回目前的目錄中的所有使用者。 請注意，使用 「 我 」 別名會傳回錯誤的示範公司因為沒有登入使用者提出的要求。

- 回應標頭] 區段中。 這可協助執行查詢時所發生的問題進行疑難排解。

- 展開與摺疊功能回應 JSON 檢視器。

- 顯示縮圖相片不支援。

## <a name="using-fiddler-to-write-to-the-directory"></a>使用 Fiddler 寫入的目錄

此快速入門指南的目的，您可以使用 Fiddler Web 偵錯工具才能練習執行 「 撰寫 」 的作業，針對您 Azure AD 目錄。 如需詳細資訊，並安裝 Fiddler，請參閱[http://www.telerik.com/fiddler](http://www.telerik.com/fiddler)。

在下面的範例中，您會使用 Fiddler Web 偵錯工具，來建立新的安全性群組 'MyTestGroup' Azure AD 目錄中。

**取得存取權杖**︰ 若要存取 Azure AD 圖形，用戶端，才能順利第一次驗證 Azure AD。 如需詳細資訊，請參閱[Azure ad 驗證案例](active-directory-authentication-scenarios.md)。

**撰寫及執行查詢**︰ 完成下列步驟。

1. 開啟 Fiddler Web 偵錯工具並切換到 [**編輯器**] 索引標籤。
2. 由於您想要建立新的 [安全性] 群組中，選取 [從下拉式功能表的 HTTP 方法為 [**文章**]。 群組物件上作業與權限的相關資訊，請參閱[Azure AD 圖形 REST API 參照](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)中的[群組](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#GroupEntity)。
3. 在**文章**旁欄位中，輸入下列要求 URL 中︰ `https://graph.windows.net/mytenantdomain/groups?api-version=1.6`。

    > [AZURE.NOTE] 您必須以替代 mytenantdomain Azure AD 目錄的網域名稱。

4. 在文章下拉式的正下方欄位中，輸入以下資料︰

    ```
Host: graph.windows.net
Authorization: your access token
Content-Type: application/json
```

    > [AZURE.NOTE] 取代您&lt;您存取權杖&gt;以存取權杖 Azure AD 目錄。

5. 在 [**邀請內文**] 欄位中，輸入以下資料︰

    ```
        {
            "displayName":"MyTestGroup",
            "mailNickname":"MyTestGroup",
            "mailEnabled":"false",
            "securityEnabled": true
        }
```

    如需有關如何建立群組的詳細資訊，請參閱[建立群組](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#CreateGroup)。

Azure AD 項目和所公開圖表類型的資訊及可以使用圖表的 [在其執行的作業的相關資訊，請參閱[Azure AD 圖形 REST API 參考](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)。

## <a name="next-steps"></a>後續步驟

- 深入瞭解[Azure AD 圖形 API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)
- 深入瞭解[Azure AD 圖形 API 權限範圍](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes)
