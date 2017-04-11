<properties
   pageTitle="整合 Azure Active Directory 與應用程式快速入門指南 | Microsoft Azure"
   description="本文是發生入門的指南的整合內部部署的應用程式，以及和雲端應用程式的 Azure Active Directory (AD)。"
   services="active-directory"
   documentationCenter=""
   authors="ihenkel"
   manager="femila"
   editor=""/>

   <tags
      ms.service="active-directory"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="identity"
      ms.date="02/09/2016"
      ms.author="inhenk"/>

# <a name="integrating-azure-active-directory-with-applications-getting-started-guide"></a>整合 Azure Active Directory 與應用程式快速入門指南
## <a name="overview"></a>概觀
本主題被用來讓您藍圖的整合應用程式與 Azure Active Directory (AD)。 每個以下各節會包含簡短摘要更詳細的主題，因此您可以找出此取得入門指南中的哪些部分是與您相關。  按照每個主旨不動產更深入的連結。

## <a name="before-you-begin-take-inventory"></a>在開始之前，請採取庫存
您在 [跳到整合應用程式與 Azure AD 之前，務必知道您身在何處，以及您要移的位置。  下列問題被為了幫助您認為 Azure AD 應用程式整合專案資訊。

### <a name="application-inventory"></a>應用程式的庫存
- 在哪裡所有應用程式？ 他們擁有者是誰？
- 哪一類的驗證您的應用程式需要？
- 誰需要存取哪些應用程式？
- 您想要部署新的應用程式？
  - 將您建立內部，並將其部署 Azure 計算執行個體上嗎？
  - 您可以使用 Azure 應用程式庫中提供的其中一個？

### <a name="user-and-group-inventory"></a>使用者和群組庫存
- 您的使用者帳戶所在的位置？
 - 內部部署的 Active Directory
 - Azure AD
 - 您擁有自己的另一個應用程式資料庫中
 - 在 unsanctioned 應用程式
 - 上述所有
- 哪些權限和角色指派個別使用者目前有？ 您是否要檢閱其存取權，或者根本確認您的使用者存取和角色指派適當現在？
- 會在您的內部部署 Active Directory 中已經建立群組？
 - 如何組織群組？
 - 群組成員是誰？
 - 哪些權限/角色指派群組目前有？
- 您是否需要先清理使用者/群組資料庫整合之前？  （這是很重要問題。 回收中查看回收。）

### <a name="access-management-inventory"></a>Access 管理庫存
- 您如何目前管理使用者存取應用程式？ 需要變更嗎？  您是否考量管理存取權，例如使用[RBAC](role-based-access-control-configure.md) ，例如的其他方法？
- 誰需要存取什麼內容？

也許您沒有安裝的問題的答案預付，但就好。  本指南可協助您回答的問題，然後進行一些決策。

## <a name="prerequisites"></a>必要條件
- Azure 的訂閱和 Azure Active Directory 目錄。  如果您還沒有 Azure 的訂閱，您可以嘗試 Azure 30 天免費的。 [試試看 ！](https://azure.microsoft.com/trial/get-started-active-directory/)

## <a name="application-integration-with-azure-ad"></a>Azure AD 應用程式整合
### <a name="finding-unsanctioned-cloud-applications-with-cloud-app-discovery"></a>尋找 unsanctioned 雲端應用程式探索雲端應用程式
如上所述，有可能尚未由您的組織到目前為止已受管理的應用程式。  庫存程序的一部分，可能是找出 unsanctioned 的雲端應用程式。 請參閱[尋找雲端應用程式探索 unsanctioned 的雲端應用程式](active-directory-cloudappdiscovery-whatis.md)。

### <a name="authentication-types"></a>驗證類型
每個應用程式可能會有不同的驗證需求。 Azure AD，與簽章憑證可以搭配使用 SAML 2.0、 WS 同盟，或 OpenID 連線通訊協定以及密碼單一登入應用程式。 如需有關應用程式[的同盟單一登入 Azure Active Directory 中的管理憑證](active-directory-sso-certs.md)和[密碼以單一登](active-directory-appssoaccess-whatis.md)，請參閱 Azure AD 搭配使用的驗證類型。

### <a name="enabling-sso-with-azure-ad-app-proxy"></a>啟用 SSO 與 Azure AD 應用程式 Proxy
使用 Microsoft Azure AD 應用程式 Proxy]，您可以存取您私人的網路內安全地，位於，從任何位置，並在任何裝置上的應用程式。 您的環境中安裝的應用程式 proxy 連接器之後，就可以輕鬆地以設定 Azure AD。

### <a name="integrating-applications-with-azure-ad"></a>Azure AD 與整合應用程式
下列文章討論的不同方式應用程式整合 Azure AD，並提供一些指引。

- [決定使用哪個 Active Directory](active-directory-administer.md)
- [Azure 應用程式庫中使用應用程式](active-directory-appssoaccess-whatis.md)
- [整合 SaaS 應用程式教學課程清單](active-directory-saas-tutorial-list.md)

## <a name="managing-access-to-applications"></a>管理應用程式的功能
下列文章說明他們有 Azure AD 使用 Azure AD 連接器與 Azure AD 整合後，您可以管理 access 應用程式的方法。

- [管理存取應用程式使用 Azure AD](active-directory-managing-access-to-apps.md)
- [Azure AD 連接器自動化](active-directory-saas-app-provisioning.md)
- [將使用者指派至應用程式](active-directory-applications-guiding-developers-assigning-users.md)
- [群組指派的應用程式](active-directory-applications-guiding-developers-assigning-groups.md)
- [共用帳戶](active-directory-sharing-accounts.md)

## <a name="integrating-custom-applications"></a>整合自訂應用程式
如果您正在撰寫新的應用程式，並且想要強大 Azure AD 協助開發人員，請參閱[Guiding 開發人員](active-directory-applications-guiding-developers-for-lob-applications.md)。

如果您想要新增至 Azure 應用程式庫自訂應用程式，請參閱[Azure AD 自助 SAML 設定的 「 將您自己的應用程式 」](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx)。

## <a name="see-also"></a>另請參閱

- [Azure Active Directory 中的應用程式管理文件索引](active-directory-apps-index.md)
