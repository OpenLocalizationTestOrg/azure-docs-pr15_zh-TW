<properties
    pageTitle="管理與 Azure Active Directory 的應用程式 |Microsoft Azure"
    description="此文件整合與您的內部部署、 雲端 SaaS 應用程式的 Azure Active Directory 的優點。"
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

   <tags
      ms.service="active-directory"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="identity"
      ms.date="10/10/2016"
      ms.author="markvi"/>

# <a name="managing-applications-with-azure-active-directory"></a>管理應用程式與 Azure Active Directory

除了的實際工作流程或內容，企業會有兩個的所有應用程式的基本需求︰

1. 若要提高生產力，應用程式應該是輕鬆地探索並存取

2. 若要啟用安全性與管理，組織必須控制項與監督誰可以和實際存取每個應用程式

世界雲端應用程式的最佳方式如下使用 「*誰可以執行的動作*」 的控制項的身分識別。

在電腦術語中︰

- *誰*是稱為*身分識別*管理的使用者和群組

- *什麼*是稱為*存取管理*– 受保護的資源的存取權的管理

這兩個元件一起即為*身分識別與 Access 管理 (IAM)*， [Gartner](http://www.gartner.com/it-glossary/identity-and-access-management-iam)群組所定義 」*安全性分項可存取正確的資源在右側的向每個人的正確的原因時間*」。

好，因此問題是什麼？ 如果 IAM 是*不受管理*的整合解決方案使用一個位置︰

- 身分識別的系統管理員必須個別建立並更新所有應用程式中的使用者帳戶分別多餘及時間的活動。

- 使用者會有記多個的認證以存取所需使用應用程式。 如此一來，使用者似乎經常犯寫下其密碼，或使用其他密碼管理解決方案會介紹其他資料安全性風險。

- 重複、 耗時活動減少次使用者和系統管理員正在處理增加您企業的下一行的商業活動。

因此，什麼通常可以避免組織採用整合式的 IAM 解決方案？

- 最技術的解決方案根據需要部署及調整以自己的應用程式的每一個組織的軟體平台。

- 雲端應用程式通常採用較高的速度比 IT 組織可以使用現有的 IAM 解決方案整合。

- 安全性和監視工具需要額外的自訂及達成全面涵蓋所有內容 E2E 案例的整合。

## <a name="azure-active-directory-integrated-with-applications"></a>Azure Active Directory 整合到應用程式

Azure Active Directory 是 Microsoft 的服務 (IDaaS) 解決方案的全面涵蓋所有內容身分識別的︰

- 啟用 IAM 為雲端服務 

- 提供集中的 access 管理單一登入 (SSO)，及報表 

- 在應用程式庫中，包括 Salesforce、 Google 應用程式、 方塊、 Concur，等[數以千計的應用程式](https://azure.microsoft.com/marketplace/active-directory/)的支援整合式的存取管理。 


Azure Active Directory 所有應用程式發佈您的合作夥伴與客戶 （企業或消費者） 有相同的身分識別和存取管理功能。<br> 這可讓您大幅降低您操作的成本。

如果您需要實作尚未列在 [應用程式] 圖庫中的應用程式？ 雖然這是更耗時超過設定 SSO 應用程式從應用程式庫，Azure AD 提供可協助您完成設定精靈。

Azure AD 的值超出 「 只要 」 雲端應用程式。 您也可以使用其與內部部署的應用程式提供的安全遠端存取。 透過安全遠端存取時，您就可以排除需要 Vpn 或其他傳統遠端存取管理實作。

所有應用程式提供集中的 access 管理和單一登入 (SSO)，Azure AD 會提供主要資料的安全性和生產力問題的解決方案。

- 使用者可以存取多個應用程式使用一個號上完成的產生或公司作業活動收入讓更多時間。

- 身分識別的系統管理員可以管理 access 應用程式在同一個位置。

使用者與貴公司的優點是明顯的。 讓我們來看深入瞭解優點身分識別管理員及組織。

## <a name="integrated-application-benefits"></a>整合式應用程式的優點

SSO 程序有兩個步驟︰

- 驗證]，確認使用者的身分識別的程序。

- 授權、 啟用] 或 [封鎖的存取權與存取原則資源決策。

使用管理應用程式，並啟用 SSO Azure AD︰ 時

- 驗證會對使用者的內部部署 (例如 AD) 或 Azure AD 帳戶進行。

- 授權執行 Azure AD 工作分派與保護原則確保一致的使用者體驗，以及讓您將工作分派、 位置及 MFA 條件，請在任何應用程式，不論其內部的功能。

瞭解在目標應用程式的准許授權的方式，根據 Azure AD 整合應用程式是如何而異。

- **預先整合服務提供者的應用程式**如 Office 365 和 Azure，這些是內建直接在 Azure AD 和其完整的身分識別和存取管理功能依賴應用程式。 透過目錄資訊和權杖發行啟用存取這些應用程式。

- **預先整合 Microsoft 和自訂應用程式的應用程式**這些是依賴內部應用程式目錄和 Azure AD 亦可以運作的獨立雲端應用程式。 這些應用程式的功能會啟用發行應用程式的認證對應到應用程式帳戶。 應用程式的功能，根據同盟權杖或使用者名稱和密碼先前已佈建應用程式中的帳戶，可能需要認證。

- **內部部署的應用程式**應用程式發佈到 Azure AD 應用程式 proxy 主要啟用存取內部部署的應用程式。 這些應用程式依賴中央部署像 Windows Server Active Directory 的目錄。 觸發 proxy 會啟用存取這些應用程式時接受要求登入內部部署，傳送給使用者的應用程式的內容。

例如，如果使用者加入您的組織，您需要為主要登入作業 Azure AD 中建立使用者帳戶。 如果此使用者需要存取受管理的應用程式，例如 Salesforce，您也需要 Salesforce 中建立此使用者的帳戶，並將其連結至 Azure 帳戶，以進行工作的 SSO。 當使用者離開您的組織時，建議您刪除 Azure AD 帳戶，並在 IAM 中的所有對應帳戶都儲存使用者有權存取的應用程式。

## <a name="access-detection"></a>Access 偵測

在新的企業，IT 部門並通常不知道所有的雲端應用程式所使用。 雲端應用程式探索搭配，Azure AD 提供偵測這些應用程式的解決方案。

## <a name="account-management"></a>帳戶管理

一般而言，管理在不同的應用程式中的帳戶是由手動程序 IT 或支援組織中個人。 Azure AD 完全自動化帳戶管理所有的服務提供者整合應用程式以及預先整合 Microsoft 支援自動化的使用者佈建或 SAML JIT 那些應用程式。

## <a name="automated-user-provisioning"></a>佈建自動化的使用者

部分應用程式提供自動化介面，建立及移除 （或停用） 的帳戶。 如果提供者提供這類介面，它會利用 Azure AD。 這可減少您操作的成本，因為系統管理工作會自動執行，並可改善您的環境的安全性，因為它將會減少未經授權存取的機率。

## <a name="access-management"></a>管理存取

您可以使用 Azure AD 管理存取權給使用個別的應用程式或導向工作分派的規則。 您也可以委派存取 [管理]，以確保最佳監督並減少技術服務負擔組織中正確的人員。

## <a name="on-premises-applications"></a>內部部署的應用程式

內建應用程式中 proxy 可讓您發佈使用者結果您內部部署的應用程式一致存取現代雲端應用程式與優點體驗從 Azure AD 監控、 報告和安全性功能。

## <a name="reporting-and-monitoring"></a>報告及監視

Azure AD 提供預先整合報告及監視等功能，可讓您知道可存取應用程式，與他們實際上使用。

## <a name="related-capabilities"></a>相關的功能

您可以使用 Azure AD 來保護精細存取原則與 MFA 預先整合式應用程式。 若要瞭解關於 Azure MFA 的詳細資訊請參閱[Azure MFA](https://azure.microsoft.com/services/multi-factor-authentication/)。

## <a name="getting-started"></a>快速入門

若要開始與 Azure AD 整合應用程式，看看的[整合 Azure Active Directory 與應用程式快速入門指南](active-directory-integrating-applications-getting-started.md)。

## <a name="see-also"></a>另請參閱

[Azure Active Directory 中的應用程式管理文件索引](active-directory-apps-index.md)
