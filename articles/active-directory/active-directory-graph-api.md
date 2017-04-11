<properties
   pageTitle="Azure Active Directory 圖表 API |Microsoft Azure"
   description="圖形 API 可透過 REST API 端點 Azure AD 以程式設計方式存取概觀 」 和 「 快速入門指南。"
   services="active-directory"
   documentationCenter=""
   authors="PatAltimore"
   manager="mbaldwin"
   editor="mbaldwin" />
<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/16/2016"
   ms.author="mbaldwin" />

# <a name="azure-active-directory-graph-api"></a>Azure Active Directory 圖表 API

> [AZURE.IMPORTANT] Azure AD 圖形 API 功能也可以透過[Microsoft Graph](https://graph.microsoft.io/)，包含其他 Microsoft 服務，例如 Outlook、 OneDrive、 OneNote、 規劃和 Office graph 下方，可透過單一端點和單一存取權杖存取 Api 整合 API。

Azure Active Directory 圖表 API 提供透過 REST API 端點 Azure AD 以程式設計方式存取。 應用程式可以使用圖形 API 來執行建立、 讀取、 更新及刪除 (CRUD) 作業目錄資料和物件。 例如，圖形 API 支援下列使用者物件的共同作業︰

- 在目錄中建立新的使用者

- 取得使用者的詳細的屬性，例如其群組

- 更新使用者的屬性，例如其位置和電話號碼，或變更使用者的密碼

- 檢查使用者的角色型存取的群組成員資格

- 停用使用者帳戶，或完全將其刪除

除了使用者物件中，您可以執行類似作業群組和應用程式等其他物件。 若要在目錄呼叫圖形 API，應用程式必須使用 Azure AD 登錄，設定為允許目錄的存取權。 這通常是透過使用者或管理員同意流程達成。

若要開始使用 Azure Active Directory 圖表 API，請參閱[圖形 API 快速入門指南](active-directory-graph-api-quickstart.md)，或檢視[互動式圖表 API 參照文件](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)。


## <a name="features"></a>功能

圖形 API 提供下列功能︰

- **REST API 結束點**︰ 圖形 API 是 RESTful 服務組成使用標準 HTTP 要求存取的結束點。 圖形 API 邀請及回應支援 XML 或 Javascript 物件標記法 (JSON) 的內容類型。 如需詳細資訊，請參閱[Azure AD 圖形 REST API 參考](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)。

- **Azure AD 驗證**︰ 每個圖形 API 邀請必須經過驗證附加 JSON 網頁 Token (JWT) 中要求的授權標頭。 取得此 token 加到 Azure AD token 端點提出要求，並提供正確的認證。 您可以使用 OAuth 2.0 用戶端認證流程或驗證碼授與延伸至取得權杖撥打該圖表。 如需的資訊，[在 Azure AD OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx)。

- **角色為基礎的授權 (RBAC)**: 安全性群組用來在圖形 API 執行 RBAC。 例如，如果您想要確定使用者是否有權存取特定的資源，應用程式可以呼叫[核取 [群組成員資格 （錯誤）](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#FunctionsandactionsongroupsCheckmembershipinaspecificgrouptransitive)作業，傳回 true 或 false。

- **差異的查詢**︰ 如果您想要檢查是否有變更，而不必頻繁查詢圖形 API 的兩個時間週期之間的目錄中，您可以進行的差異查詢要求。 此類型的邀請會傳回只將先前差異查詢要求與目前的要求之間所做的變更。 如需詳細資訊，請參閱[Azure AD 圖形 API 差異查詢](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query)。

- **目錄副檔名**︰ 如果您開發的需求來讀取或寫入目錄物件的唯一內容的應用程式，您可以註冊，並使用圖形 API 使用副檔名值。 例如，如果您的應用程式需要為每位使用者的 Skype ID 屬性，您可以在目錄中註冊新屬性和將會出現在每個使用者的物件。 如需詳細資訊，請參閱[Azure AD 圖形 API 目錄結構描述副檔名](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions)。

- **依權限範圍 Secured**: AAD 圖形 API 公開啟用安全/同意 AAD 資料存取及支援的用戶端應用程式類型，其中包括各種不同的權限範圍︰
 - 相關的使用者介面，其中會提供委派的 access 透過授權的資料登入使用者 （委派）
  - 使用應用程式-定義角色型存取控制等服務/精靈用戶端 （應用程式角色）

    委派和應用程式角色的權限範圍代表所公開之圖形 API 」 權限，並可透過應用程式的登錄權限[Azure 傳統入口網站的功能](https://manage.windowsazure.com)的用戶端應用程式要求。 用戶端可以檢查權限範圍授與給他們檢查委派的權限存取權杖中收到的範圍 (「 scp 」) 宣告及宣告的應用程式角色的權限的角色 （「 角色 」）。 進一步瞭解[Azure AD 圖形 API 權限的範圍](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes)。


## <a name="scenarios"></a>案例

圖形 API 可讓許多應用程式案例。 下列情況是最常見的︰

- **商務線條 （單一租用戶） 應用程式**︰ 在此案例中，企業開發人員適合擁有 Office 365 訂閱的組織。 開發人員正在建立與執行工作的 Azure AD 互動的 web 應用程式，例如將授權指派給使用者。 這項工作需要圖形 API，存取權，因此單一租用戶中 Azure AD 應用程式，並設定的開發人員登錄讀取和寫入圖形 API 的權限。 然後應用程式設定為使用自己的認證或目前登入使用者取得撥打圖形 API 權杖。

- **為服務應用程式 （多租用戶） 的軟體**︰ 在此案例中，獨立軟體廠商 (ISV) 開發使用者管理功能提供使用 Azure AD 其他組織的裝載的多租用戶 web 應用程式。 這些功能需要存取目錄物件和應用程式，因此需要呼叫圖形 API。 開發人員 Azure AD 中登錄應用程式設定為需要讀取和寫入的圖形 API，權限，讓其他組織可以同意其目錄中使用應用程式，然後啟用外部存取。 其他組織中的使用者驗證的第一次應用程式，其會顯示具有權限的應用程式要求同意] 對話方塊。  這些要求使用者的目錄中的圖形 API 的權限授與同意然後讓應用程式。 如需有關同意架構的詳細資訊，請參閱[同意架構的概觀](active-directory-integrating-applications.md)。

## <a name="see-also"></a>另請參閱

[Azure AD 圖形 API 快速入門指南](active-directory-graph-api-quickstart.md)

[AD 圖形的其餘部分文件](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)

[Azure Active Directory 開發人員指南](active-directory-developers-guide.md)
