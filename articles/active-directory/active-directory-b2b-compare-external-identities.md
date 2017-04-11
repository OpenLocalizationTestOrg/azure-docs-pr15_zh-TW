<properties
   pageTitle="比較功能來管理外部使用 Azure Active Directory 的身分識別 |Microsoft Azure"
   description="比較 Azure Active Directory B2B 共同作業與 B2C，多租用戶的應用程式支援的外部的身分識別的驗證和授權"
   services="active-directory"
   documentationCenter="" 
   authors="arvindsuthar"
   manager="cliffdi"
   editor=""
   tags=""/>

<tags
   ms.service="active-directory"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="identity"
   ms.date="02/24/2016"
   ms.author="asuthar"/>

# <a name="comparing-capabilities-for-managing-external-identities-using-azure-active-directory"></a>比較功能來管理外部使用 Azure Active Directory 的身分識別

管理行動裝置的人力存取 SaaS 應用程式，除了 Azure Active Directory (Azure AD) 可協助您的組織和業務合作夥伴共用資源，並傳送至企業和消費者應用程式。

## <a name="developing-applications-for-businesses"></a>開發適用於企業的應用程式

您提供的服務或應用程式，例如薪資服務，企業版？ Azure AD 提供身分識別的平台可讓您建立的組織已設定 Azure AD 部署 Office 365 或其他企業版服務的一部分，數百萬完美地整合應用程式。

**範例︰**提供給經銷商提供醫療耗材和醫療產業資訊系統。 他們所需欄位醫療作法和需要的客戶管理自己的身分識別的分析應用程式。 此公司選擇 Azure AD 識別平台為其練習管理應用程式中，提供 Azure AD 身分識別符號客戶，設定必要時。 如需詳細資訊，請參閱[Azure Active Directory 開發人員指南](active-directory-developers-guide.md)。

## <a name="enabling-business-partner-access-to-your-corporate-resources"></a>啟用商業夥伴存取您的企業資源

您有業務合作夥伴或其他公司外部的人員需要存取您的企業資源，例如 SharePoint 網站或 ERP 系統？ Azure AD 可讓系統管理員授與外部使用者 （可能或可能不存在於 Azure AD） 單一登入公司的應用程式的功能。 當使用者失去存取權，而您組織內控制存取原則離開合作夥伴組織，這樣就會提高安全性。 當您不需要管理目錄或每個夥伴同盟關係外部的合作夥伴，這也可簡化管理。

**範例︰**影像的公司提供零售商影像服務的相片，然後列印 kiosk 的最大零售網路的運作方式。 選擇 Azure AD 啟用數以千計的使用者在他們零售商業夥伴下載最新的合作夥伴行銷及重新排列相片處理從公司的外部網路的供應商提供使用自己的認證。 如需詳細資訊，請參閱[Azure AD B2B 共同作業](active-directory-b2b-what-is-azure-ad-b2b.md)。

## <a name="developing-applications-for-consumers"></a>開發消費者用的應用程式

您需要安全地效益發佈線上應用程式，例如零售商店置於最上層的消費者嗎？ Azure AD 提供啟用社交以及使用者名稱與密碼登入的平台建立品牌的自助註冊，與自助密碼重設您的應用程式的消費者用。 這個動作會增加方便時觀看您的消費者用時減少負載開發人員。

**範例︰**\#1 運動而直接加入與其 450 百萬風扇所需的世界。 若要這麼做，他們內建行動應用程式使用 Azure AD 使用者驗證及設定檔儲存空間。 風扇取得簡化的註冊及登入 Facebook，例如社交帳戶使用或者他們可以使用傳統的使用者名稱密碼獲得順暢的使用體驗 iOS、 Android 和 Windows 手機。 建立建置 Azure AD 平台大幅降低自訂程式碼，而的商標和減輕顧慮安全性、 資料破壞、 和延展性提供而自訂。 如需詳細資訊，請參閱[Azure AD B2C](https://azure.microsoft.com/documentation/services/active-directory-b2c/)。

## <a name="comparison-of-azure-ad-capabilities"></a>Azure AD 功能的比較

每個已本文中所討論的案例是 Azure AD 內解決功能。 此表格應能助您釐清哪些功能是最︰

| **請考慮這項產品...**       | [Azure AD 租用戶多 SaaS 應用程式](active-directory-developers-guide.md)    | [Azure AD B2B 共同作業](active-directory-b2b-what-is-azure-ad-b2b.md)        | [Azure AD B2C](https://azure.microsoft.com/documentation/services/active-directory-b2c/)                |
|-----------------------|-------------------------|----------------------------|------------------------|
| **如果我需要提供...** | 企業版服務 | 合作夥伴存取我的應用程式  | 客戶服務 |
| **與我類似...**  | Pharma 經銷商      | 處理公司            | 運動而       |
| **部署的應用程式...**  | 練習管理     | 供應商外部網路          | 足球風扇            |
| **針對選取目標...**        | 看醫生的辦公室        | 已核准的商業夥伴 | 任何人只要有電子郵件      |
| **無障礙時...**      | 客戶管理員同意 | 我管理員邀請           | 消費者註冊      |
