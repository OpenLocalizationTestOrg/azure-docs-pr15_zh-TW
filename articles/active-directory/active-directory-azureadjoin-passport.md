<properties
    pageTitle="驗證身分識別，不需要密碼透過 Microsoft 密碼 |Microsoft Azure"
    description="部署 Microsoft 密碼提供 Microsoft 密碼及其他資訊的概觀。"
    services="active-directory"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor=""
    tags="azure-classic-portal"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="femila"/>

# <a name="authenticating-identities-without-passwords-through-microsoft-passport"></a>不需要密碼透過 Microsoft 密碼驗證身分識別

目前的密碼了解單獨使用的驗證方法都無法足以讓使用者更安全。 使用者重複使用及忘記的密碼。 密碼會 breachable，phishable 容易事情，及猜得到。 他們也可以使用容易記住及容易攻擊"[傳遞雜湊](https://technet.microsoft.com/dn785092.aspx)」。

## <a name="about-microsoft-passport"></a>瞭解 Microsoft 密碼
Microsoft 密碼是私人/公開金鑰或組織和客戶的超出密碼的憑證驗證方法。 此表單的驗證依賴可以取代密碼且難遭侵害等、 竊取的行為，與網路釣魚組認證。

 Microsoft 密碼可讓使用者以 Microsoft 帳戶、 Windows Server Active Directory 帳戶、 Microsoft Azure Active Directory (Azure AD) 帳戶或非 Microsoft 服務的支援快速識別線上 (FIDO) 驗證進行驗證。 在 Microsoft 密碼註冊期間初始的雙步驟驗證之後, Microsoft 密碼設定使用者的裝置上並使用者設定的手勢，可以在 Windows Hello 或 PIN。 使用者提供來驗證其身分手勢。 Windows 然後使用 Microsoft 密碼使用者進行驗證，並協助他們存取受保護的資源和服務。

私密金鑰可僅透過 PIN、 統計或遠端裝置上登入裝置的使用者使用智慧卡登像等 「 使用者筆勢 」。 這項資訊會連結至憑證或對稱的金鑰。 私密金鑰是硬體 attested 如果裝置有受信任的平台模組 (TPM) 晶片。 私密金鑰永遠不會離開裝置。

公開金鑰註冊 Azure Active Directory 與 Windows Server Active Directory （的內部部署）。 身分識別提供者 (IDPs) 驗證使用者將使用者的公開金鑰對應至 [私人] 鍵，並提供登入資訊透過一次密碼 (OTP)、 PhoneFactor 或不同的通知機制。

## <a name="why-enterprises-should-adopt-microsoft-passport"></a>為什麼企業應採用 Microsoft 密碼

啟用 Microsoft 密碼，企業可以進行資源更安全的︰

* 設定 Microsoft 密碼硬體慣用的選項。 這表示 TPM 1.2 或 TPM 2.0 可用時，會產生金鑰。 無法使用 TPM 時，軟體會產生金鑰。

* 定義的 PIN，長度與複雜度和您的組織是否啟用認識使用方式。

* 設定 Microsoft 密碼，以使用憑證型信任支援智慧卡類似的情況。

## <a name="how-microsoft-passport-works"></a>Microsoft 密碼的運作方式
1. 硬體上產生金鑰 TPM 或軟體。 許多裝置有內建的 TPM 晶片保護硬體來加密金鑰整合裝置。 TPM 1.2 或 TPM 2.0 會產生金鑰或從產生金鑰所建立的憑證。

2. TPM 驗證這些硬體繫結按鍵。

3. 單一解除鎖定筆勢解除鎖定的裝置。 此動作可讓多個資源的存取權如果裝置是網域或 Azure AD 加入。

## <a name="how-the-microsoft-passport-lifecycle-works"></a>Microsoft 密碼生命週期的運作方式

![Microsoft 密碼生命週期](./media/active-directory-azureadjoin/active-directory-azureadjoin-microsoft-passport.png)

上述的圖表可圖解私人/公開金鑰組與身分識別提供者驗證。 以下詳細說明這些步驟︰

1. 使用者證明透過多個內建的校訂方法 （手勢，實體智慧卡，多重因素驗證） 的身分識別和傳送此資訊來身分識別提供者 (IDP) 等 Azure Active Directory 或者內部部署 Active Directory。

2. 裝置然後建立索引鍵、 驗證鍵、 採用此按鍵公用部分，將它附加站陳述式、 登入，並傳送給登錄機碼 IDP。

4. 一旦 IDP 登錄機碼公用部分，IDP 挑戰登入與索引鍵的私人部分的裝置。

5. 然後 IDP 會驗證並問題驗證權杖，可讓使用者和裝置存取受保護的資源。 IDPs 可以撰寫跨平台應用程式，或使用瀏覽器支援 （透過 JavaScript/Webcrypto Api) 建立及使用 Microsoft 密碼憑證的使用者。

## <a name="the-deployment-requirements-for-microsoft-passport"></a>Microsoft 密碼部署需求
### <a name="at-the-enterprise-level"></a>企業層級

* 企業有 Azure 的訂閱。

### <a name="at-the-user-level"></a>在使用者層級

* 使用者的電腦執行 Windows 10 專業版或企業版。

如需詳細的部署指示，請參閱[啟用 Microsoft 密碼在組織中的工作](active-directory-azureadjoin-passport-deployment.md)。


## <a name="additional-information"></a>其他資訊

* [企業版的 Windows 10︰ 裝置用於工作的方法](active-directory-azureadjoin-windows10-devices-overview.md)
* [延伸至 Windows 10 裝置，透過 Azure [Active Directory 加入雲端功能](active-directory-azureadjoin-user-upgrade.md)
* [瞭解有關使用案例的 Azure AD 加入](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [加入網域的裝置連接到 Windows 10 體驗 Azure AD](active-directory-azureadjoin-devices-group-policy.md)
* [設定 Azure AD 加入](active-directory-azureadjoin-setup.md)
