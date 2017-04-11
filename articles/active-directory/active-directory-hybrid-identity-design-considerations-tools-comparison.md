<properties
    pageTitle="混合式身分識別︰ 目錄整合工具比較 |Microsoft Azure"
    description="此為頁面提供全面涵蓋所有內容的資料表，比較的各種的目錄整合工具，可用於目錄整合。"
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
    ms.topic="get-started-article"
    ms.date="08/08/2016"
    ms.author="billmath"/>

# <a name="hybrid-identity-directory-integration-tools-comparison"></a>混合式身分識別目錄整合工具比較

年目錄整合工具有變，而且發展出的。  這份文件是以協助提供這些工具的合併的檢視與所提供的每一個功能的比較。

<!-- The hardcoded link is a workaround for campaign ids not working in acom links-->

>[AZURE.NOTE] Azure AD Connect 併入的元件和先前以 Dirsync 和 AAD Sync 發行的功能。 這些工具不會再個別發行和所有未來的改良功能將會包含在更新 Azure AD Connect，讓您隨時知道可在何處取得最新功能。
>
>DirSync 和 Azure AD Sync 已遭取代。 可以在[這裡](active-directory-aadconnect-dirsync-deprecated.md)找到更多資訊。


下列按鍵用於每個資料表。

● 現在 = 可用  
這個方法 = 未來版本  
前置處理 = 公用預覽  

## <a name="on-premises-to-cloud-synchronization"></a>內部部署雲端同步處理

| 功能  | Azure Active Directory 連線  | Azure Active Directory 同步處理服務 (AAD Sync) | Azure Active Directory 同步處理工具 (DirSync)| Forefront 身分識別 Manager 2010 R2 (FIM) |Microsoft 身分識別管理員 2016 (MIM)|
| :-------- |:--------:|:--------:|:--------:|:--------:|:--------:
| 連線到單一內部部署 AD 樹系 | ● | ● | ● | ● |● |
| 連線至多個內部部署 AD 樹系 |●  | ● |  | ● |● |
| 連線至多個內部部署 Exchange 組織 | ● |  |  |  | |
| 連接到內部部署的單一 LDAP 目錄 | 這個方法 |  |  | ● |● |
| 連線至多個內部部署 LDAP 目錄 |這個方法  |  |  | ● |● |
| 連線到內部部署 AD 與內部部署 LDAP 目錄 |這個方法  |  |  | ● |● |
| 連線到自訂系統 （也就是 SQL Oracle、 MySQL、 等等。） | 這個方法 |  |  | ● |● |
| 同步處理客戶定義屬性 （目錄擴充功能） | ● |  |  |  |  |
|連線到內部部署 HR (亦即 SAP，Oracle eBusiness，PeopleSoft)| 這個方法 |  |  | ● |● |
|支援 FIM 同步處理規則和連接器佈建到內部部署系統。|  |  |  | ● |● |

## <a name="cloud-to-on-premises-synchronization"></a>內部部署同步處理至雲端

| 功能  | Azure Active Directory 連線  | Azure Active Directory 同步處理服務 | Azure Active Directory 同步處理工具 (DirSync) | Forefront 識別管理員 2010 R2 (FIM) |Microsoft 身分識別管理員 2016 (MIM)|
| :-------- |:--------:|:--------:|:--------:|:--------:|:--------:
| 回寫的裝置 | ● |  | ● |  ||
| 屬性回寫 （適用於 Exchange 混合式部署） | ● | ● | ● | ● |● |
| 回寫的使用者和群組物件 |  ●|  | |  ||
| 回寫的密碼 （從自助密碼重設 (SSPR) 和變更密碼） |  ● | ● |  |  ||



## <a name="authentication-feature-support"></a>驗證所支援功能

| 功能  | Azure Active Directory 連線 | Azure Active Directory 同步處理服務 | Azure Active Directory 同步處理工具 (DirSync) | Forefront 身分識別 Manager 2010 R2 (FIM) |Microsoft 身分識別管理員 2016 (MIM)|
| :-------- |:--------:|:--------:|:--------:|:--------:|:--------:
| 針對單一密碼同步處理內部部署 AD 樹系 | ● | ● | ● |  ||
| 密碼同步處理多個內部部署 AD 樹系 |  ●| ● |  |  ||
| 單一登入同盟功能 | ● | ● | ● | ● |● |
| 回寫的密碼 （從 SSPR 和密碼變更） |●  | ● |  |  ||



## <a name="set-up-and-installation"></a>安裝和設定

| 功能  | Azure Active Directory 連線  | Azure Active Directory 同步處理服務 | Azure Active Directory 同步處理工具 (DirSync) | Microsoft 身分識別管理員 2016 (MIM) |
| :-------- |:--------:|:--------:|:--------:|:--------:
| 支援網域控制站的安裝 | ● | ● | ● |  |
| 支援使用 SQL Express 安裝 | ● | ● | ● |  |
| 從 DirSync 輕鬆升級 |● |  |  |  |
| 管理員 UX 的本地化 Windows Server 語言 | ● | ● | ● |  |
|已進行本地化的使用者 UX Windows Server 語言| |  |  |● |
| Windows Server 2008 及 Windows Server 2008 R2 的支援 | ● 同步，不的同盟| ● | ●  | ● |
| Windows Server 2012 和 Windows Server 2012 R2 的支援 | ● | ● | ● | ● |

## <a name="filtering-and-configuration"></a>篩選及設定

功能  | Azure Active Directory 連線 | Azure Active Directory 同步處理服務 | Azure Active Directory 同步處理工具 (DirSync) | Forefront 身分識別 Manager 2010 R2 (FIM)| Microsoft 身分識別管理員 2016 (MIM)
:-------- |:--------:|:--------:|:--------:|:--------:|:--------:|
在網域和組織單位篩選 | ● | ● | ● | ●  | ●
篩選物件的屬性值 | ● | ● | ● | ●| ●
允許最基本的屬性，同步處理 (MinSync) | ● | ● |  ||
允許的屬性套用不同的服務範本 |●  | ● |  ||
允許移除流向 AD 從 Azure AD 屬性 | ● | ● |  |  |
讓進階的自訂的屬性 | ● | ● |  | ●  | ●

## <a name="next-steps"></a>後續步驟
進一步瞭解[整合您的內部部署識別與 Azure Active Directory](active-directory-aadconnect.md)。
