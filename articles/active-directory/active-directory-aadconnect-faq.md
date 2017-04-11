<properties
    pageTitle="Azure AD 連線︰ 常見問題集 |Microsoft Azure"
    description="此頁面具有的常見問題集 Azure AD Connect。"
    services="active-directory"
    documentationCenter=""
    authors="billmath"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/08/2016"
    ms.author="billmath"/>

# <a name="azure-ad-connect-faq"></a>Azure AD Connect 常見問題集

## <a name="general-installation"></a>一般的安裝
**問︰ 如果就能安裝使用 Azure AD 全域管理員已啟用 2FA 嗎？**  
從年 2 月 2016年組建，這是受支援。

**問︰ 有安裝 Azure AD Connect 自動的方式嗎？**  
僅支援安裝 Azure AD Connect 使用安裝精靈。 不支援自動並無安裝。

**問︰ 我有樹系無法連線到一個網域。如何安裝 Azure AD Connect？**  
從年 2 月 2016年組建，這是受支援。

**問︰ AD DS 健康情況代理程式運作的伺服器核心？**  
[是]。 安裝後代理程式，您可以完成註冊程序使用下列 PowerShell commandlet: 

`Register-AzureADConnectHealthADDSAgent -Credentials $cred`

## <a name="network"></a>網路
**問︰ 我有防火牆、 網路的裝置，或其他項目限制的最大時間連線可以保留我的網路上開啟。多久我用戶端側逾時閾值時應該使用 Azure AD Connect？**  
所有的網路軟體、 實體裝置，或其他項目限制連線可以保持開啟的最大時間應該使用臨界值的最小 5 分鐘 （300 秒） 安裝 Azure AD Connect 用戶端的伺服器和 Azure Active Directory 之間的連線。 這也適用於所有先前發行的 Microsoft 身分識別同步處理工具。

**問︰ 是 SLDs （單一標籤網域） 支援？**  
否，Azure AD 連線不支援內部部署樹系/網域使用 SLDs。

**問︰ 「 點 」 NetBios 名為支援嗎？**  
否，Azure AD 連線不支援內部部署樹系/網域 NetBios 名稱其中包含一段 」。 」 的名稱。

## <a name="federation"></a>同盟
**問︰ 我怎麼做如果我收到一封電子郵件的要求我更新我的 Office 365 憑證**  
使用[更新的憑證](active-directory-aadconnect-o365-certs.md)主題如何更新憑證所述的指南。

**問︰ 我有 「 自動更新信賴 」 設定 O365 信賴。有自動簽章憑證我權杖會變換時採取任何動作嗎？**  
使用指南的文章[更新的憑證](active-directory-aadconnect-o365-certs.md)。

## <a name="environment"></a>環境
**問︰ 它支援 Azure AD Connect 安裝之後，重新命名伺服器？**  
[否]。 變更的伺服器名稱會造成無法連線至 SQL 資料庫同步處理引擎和服務不能開始。

## <a name="identity-data"></a>身分識別的資料
**問︰ UPN (userPrincipalName) 中的屬性 Azure AD 不符的內部部署 UPN-為何？**  
請參閱下列文章︰

- [在 Office 365、 Azure 或 Intune 的使用者名稱不相符的內部部署 UPN 或其他登入 ID](https://support.microsoft.com/en-us/kb/2523192)
- [變更未同步處理的 Azure Active Directory 同步處理工具之後變更使用者帳戶的 UPN，若要使用不同的同盟的網域](https://support.microsoft.com/en-us/kb/2669550)

您也可以設定允許更新 userPrincipalName [Azure AD Connect 同步處理服務功能](active-directory-aadconnectsyncservice-features.md)所述的同步處理引擎 Azure AD。

## <a name="custom-configuration"></a>自訂的設定
**問︰ 位置記錄的 Azure AD Connect 的 PowerShell 指令程式？**  
記錄在此網站上的指令程式，但不支援在 Azure AD Connect 中找到其他 PowerShell cmdlet 客戶使用。

**問︰ 我可以在使用 「 伺服器匯出伺服器匯入 」 中找到*的同步處理服務管理員*之間 servers? * * 移動設定  
[否]。 這個選項會將不會擷取所有設定的設定，並不應使用。 您應該改用精靈第二個伺服器上建立基本的設定和使用同步處理規則編輯器產生伺服器之間移動的任何自訂規則的 PowerShell 指令碼。 請參閱[將自訂的設定，從作用暫存的伺服器](active-directory-aadconnect-upgrade-previous-version.md#move-custom-configuration-from-active-to-staging-server)。

## <a name="troubleshooting"></a>疑難排解
**問︰ 如何能 Azure AD Connect 的說明？**

[搜尋 Microsoft 知識庫文 (KB)](https://www.microsoft.com/en-us/Search/result.aspx?q=azure%20active%20directory%20connect&form=mssupport)

- 搜尋 Microsoft 知識庫 (KB) 的技術支援 Azure AD Connect 相關的常見符號修正問題解決方案。

[Microsoft Azure Active Directory 論壇](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)

- 您可以搜尋及瀏覽的技術問題和社群解答或提出您自己的問題，即可[在這裡](https://social.msdn.microsoft.com/Forums/azure/en-US/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required)。

[Azure AD Connect 客戶支援](https://manage.windowsazure.com/?getsupport=true)

- 若要取得支援透過 Azure 入口網站中使用此連結。
