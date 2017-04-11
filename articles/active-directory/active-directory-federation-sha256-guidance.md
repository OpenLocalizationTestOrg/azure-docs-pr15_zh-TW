<properties
    pageTitle="變更簽名雜湊演算法的 Office 365 回覆廠商信任 |Microsoft Azure"
    description="此頁面變更 SHA 演算法的同盟信任搭配 Office 365 提供的方針"
    keywords="SHA1 SHA256、 O365，同盟 aadconnect、 adfs、 ad fs、 變更 sha 同盟信任，依賴廠商信任"
    services="active-directory"
    documentationCenter=""
    authors="anandyadavmsft"
    manager="samueld"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/01/2016"
    ms.author="anandy"/>

# <a name="change-signature-hash-algorithm-for-office-365-replying-party-trust"></a>變更回覆廠商信任的 Office 365 的簽章雜湊演算法

## <a name="overview"></a>概觀

Azure Active Directory Federation Services (AD FS) 簽署其權杖到 Microsoft Azure Active Directory 以確保他們無法遭竄改。 此簽名可以根據 SHA1 或 SHA256。 Azure Active Directory 現在支援與 SHA256 演算法，登入的權杖，我們建議您設定的最高的安全性層級的權杖簽署演算法 SHA256。 本文將說明若要設定更安全 SHA256 層級的權杖簽署演算法所需的步驟。

## <a name="change-the-token-signing-algorithm"></a>變更的權杖簽署演算法

設定簽章演算法的其中一個下列兩個程序之後，AD FS 會簽署權杖依賴 SHA256 廠商信任的 Office 365。 您不需要變更任何額外的設定，這項變更會造成任何影響上存取 Office 365 或其他 Azure AD 應用程式的能力。

### <a name="ad-fs-management-console"></a>AD FS 管理主控台

1. 開啟 [AD FS 管理主控台主要 AD FS 伺服器上。
2. 展開 [AD FS 節點並按一下 [**依賴廠商信任**。
3. 以滑鼠右鍵按一下您 Office 365/Azure 依賴廠商信任，然後選取 [**內容]**。
4. 選取 [**進階**] 索引標籤，然後選取 [安全雜湊演算法 SHA256。
5. 按一下**[確定]**。

![SHA256 簽署演算法-MMC](./media/active-directory-aadconnectfed-sha256guidance/mmc.png)

### <a name="ad-fs-powershell-cmdlets"></a>AD FS PowerShell cmdlet

1. 在任何 AD FS 伺服器上，開啟 [系統管理員權限下的 PowerShell。
2. 使用**設定 AdfsRelyingPartyTrust** cmdlet 設定安全雜湊演算法。

 <code>Set-AdfsRelyingPartyTrust -TargetName 'Microsoft Office 365 Identity Platform' -SignatureAlgorithm 'http://www.w3.org/2001/04/xmldsig-more#rsa-sha256'</code>

## <a name="also-read"></a>也讀取

* [修復 Office 365 信任 Azure AD Connect](./active-directory-aadconnect-federation-management.md#repairing-the-trust)
