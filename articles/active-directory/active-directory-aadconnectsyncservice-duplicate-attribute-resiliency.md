<properties
    pageTitle="身分識別同步處理和重複屬性恢復 |Microsoft Azure"
    description="如何使用 Azure AD Connect 的目錄同步處理期間處理的 UPN 或 ProxyAddress 衝突物件的新的行為。"
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/29/2016"
    ms.author="markusvi"/>



# <a name="identity-synchronization-and-duplicate-attribute-resiliency"></a>身分識別同步處理和重複屬性恢復功能
重複屬性恢復是將會排除人事所造成的**UserPrincipalName**和**ProxyAddress**衝突時執行的 Microsoft 的同步處理工具的 Azure Active Directory 中的功能。

這兩種屬性，通常才能在指定的 Azure Active Directory 租用戶中的所有**使用者**、**群組**或**連絡人**物件是唯一的。

> [AZURE.NOTE] 只有使用者可以有 Upn。

此功能可讓新行為是雲端部分的同步處理管道的郵件，因此無從驗證，包括 Azure AD Connect、 DirSync 和 MIM + 連接器任何 Microsoft 同步處理產品的相關的用戶端。 一般的字詞同步處理用戶端] 文件中用來代表任何一項產品。

## <a name="current-behavior"></a>目前的行為
如果嘗試佈建 UPN 或 ProxyAddress 值違反此唯一性限制式，新的物件，Azure Active Directory 封鎖建立該物件。 同樣地，如果物件的非唯一的 UPN 或 ProxyAddress 更新，更新將會失敗。 佈建嘗試或更新重試一次同步處理用戶端在每個匯出循環圖，並持續失敗衝突解決之前。 在每一次嘗試時產生錯誤報告電子郵件並同步處理用戶端記錄錯誤。

## <a name="behavior-with-duplicate-attribute-resiliency"></a>重複屬性恢復與行為
而不是完全無法佈建或重複的屬性更新物件，Azure Active Directory 「 隔離 」 違反唯一性限制式重複屬性。 如果這個屬性需佈建，UserPrincipalName，例如服務會指派的版面配置區的值。 這些暫時值的格式  
「***<OriginalPrefix>+<4DigitNumber>@<InitialTenantDomain>。 onmicrosoft.com***」。  
如果不需要的例如**ProxyAddress**屬性，Azure Active Directory 只要隔離衝突屬性，然後繼續進行物件建立或更新。

在隔離屬性，有關衝突的資訊會傳送相同舊的行為使用中的錯誤報告電子郵件中。 不過，此資訊只會出現錯誤報告中一次，隔離發生情況時，它不會繼續會記錄在未來的電子郵件。 此外，因為此物件匯出成功，同步處理用戶端不會記錄錯誤並不會重新建立 / 更新在後續的同步處理循環的作業。

支援這種行為的新屬性已新增的使用者、 群組和連絡人物件類別︰  
**DirSyncProvisioningErrors**

這是用來儲存違反唯一性限制式應該則會新增至正常衝突屬性的多重值的屬性。 在執行每小時，若要尋找重複的屬性衝突都已解決，自動將問題屬性移除隔離的 Azure Active Directory 中已啟用背景計時器工作。

### <a name="enabling-duplicate-attribute-resiliency"></a>啟用重複屬性恢復功能
重複屬性恢復會在所有的 Azure Active Directory 租用戶是新的預設行為。 它會出現在適用於所有承租人啟用第一次在 2016 年 8 月 22 日或更新版本的同步處理的預設。 啟用同步處理此日期之前的租用戶會有批次中啟用的功能。 此範圍會先年 9 月 2016年中，以及電子郵件通知將會傳送至每個租用戶的技術通知連絡人與特定的日期時將會啟用的功能。

複製屬性恢復功能開啟後，無法停用。

若要查看您的租用戶是否啟用的功能，您可以執行下載 Azure Active Directory PowerShell 模組的最新版本，並執行︰

`Get-MsolDirSyncFeatures -Feature DuplicateUPNResiliency`

`Get-MsolDirSyncFeatures -Feature DuplicateProxyAddressResiliency`

如果您想要前就已為您的租用戶主動啟用的功能，您可以執行下載 Azure Active Directory PowerShell 模組的最新版本，並執行︰

`Set-MsolDirSyncFeature -Feature DuplicateUPNResiliency -Enable $true`

`Set-MsolDirSyncFeature -Feature DuplicateProxyAddressResiliency -Enable $true`

## <a name="identifying-objects-with-dirsyncprovisioningerrors"></a>識別 DirSyncProvisioningErrors 的物件
有目前兩種方法可以找出重複的屬性衝突，Azure Active Directory PowerShell 和 Office 365 管理入口網站的這些錯誤的物件。 有可延伸至其他入口網站根據報告在未來的計劃。

### <a name="azure-active-directory-powershell"></a>PowerShell 的 azure Active Directory
本主題中的 PowerShell 指令程式，如下列情況成立時︰

- 所有的下列 cmdlet，會區分大小寫。
- **– ErrorCategory PropertyConflict**都必須包含在內。 目前沒有其他類型的**ErrorCategory**，但這可能會在未來延伸。

首先，並以開始執行**連線 MsolService**租用戶系統管理員輸入認證。

請使用下列 cmdlet 和運算子來檢視錯誤以不同方式︰

1. [查看所有](#see-all)

2. [依內容類型](#by-property-type)

3. [衝突的值](#by-conflicting-value)

4. [使用搜尋字串](#using-a-string-search)

5. [排序](#sorted)

6. [在受限制的數量或所有](#in-a-limited-quantity-or-all)


#### <a name="see-all"></a>查看所有
連線之後，若要查看一般清單屬性佈建錯誤租用戶中的執行︰

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict`

這會產生下列結果︰  
 ![取得 MsolDirSyncProvisioningError](./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/1.png "Get-MsolDirSyncProvisioningError")  


#### <a name="by-property-type"></a>依內容類型
若要查看依內容類型的錯誤，加入**-PropertyName**旗標與**UserPrincipalName**或**ProxyAddresses**引數︰

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName UserPrincipalName`

或

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName ProxyAddresses`

#### <a name="by-conflicting-value"></a>衝突的值
若要查看錯誤的相關的特定屬性新增 （**-PropertyName**時，必須使用以及新增這個旗標） **-PropertyValue**標幟︰

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyValue User@domain.com -PropertyName UserPrincipalName`


#### <a name="using-a-string-search"></a>使用搜尋字串
若要執行主要的字串搜尋，請使用**-SearchString**標幟。 這可以單獨使用從所有上述標幟，但**-ErrorCategory PropertyConflict**，永遠所需︰

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -SearchString User`

#### <a name="in-a-limited-quantity-or-all"></a>在受限制的數量或所有
1. **MaxResults<Int>**可以用來限制查詢，以特定數字的值。

2. **所有**可確保所有結果都擷取大量的錯誤存在的大小寫。

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -MaxResults 5`

## <a name="office-365-admin-portal"></a>Office 365 管理入口網站

您可以在 Office 365 系統管理中心中檢視的目錄同步處理錯誤。 Office 365 入口網站中的報告只顯示具有這些錯誤的**使用者**物件。 它不會顯示**群組**和**連絡人**之間的衝突的資訊。


![作用中的使用者](./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/1234.png "作用中的使用者")

如需如何在 Office 365 系統管理中心中檢視的目錄同步處理錯誤的指示，請參閱[在 Office 365 中的識別目錄同步處理錯誤](https://support.office.com/en-us/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067)。


### <a name="identity-synchronization-error-report"></a>身分識別同步處理錯誤報告
當有衝突的重複屬性的物件處理通知會包含在標準的身分識別同步處理錯誤報告電子郵件傳送給技術通知此新行為與連絡人的租用戶。 不過，就是這種行為重要變更。 在過去的經驗，重複屬性衝突的資訊會包含在每一個後續的錯誤報告鍵，直到已解決衝突。 使用此新行為] 中，指定衝突的錯誤通知就只會出現一次-衝突的屬性會被隔離次。

以下是範例電子郵件通知 ProxyAddress 衝突的外觀︰  
    ![作用中的使用者](./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/6.png "Active Users")  

## <a name="resolving-conflicts"></a>解決衝突
重複屬性錯誤已過去的處理的方式不應該不同策略與解析度策略這些錯誤的疑難排解。 唯一的差別計時器工作掃掠，透過自動新增至適當的物件問題的屬性，解決衝突後服務一邊的租用戶。

下列文章概述各種疑難排解並解決︰[有重複或無效的屬性妨礙 Office 365 中的目錄同步處理](https://support.microsoft.com/kb/2647098)。

## <a name="known-issues"></a>已知的問題
無這些已知問題會導致資料遺失或降低服務。 其中幾個美感、 其他人會導致標準 」*前恢復*」 重複屬性錯誤狀況，而不是隔離衝突的屬性，以及另一個會造成需要額外手動修正向上特定錯誤。

**核心行為︰**

1. 物件的特定屬性設定持續收到匯出錯誤，而不是重複被隔離的屬性。  
例如︰

    。 建立新使用者的 UPN 與 AD 中**Joe@contoso.com**和 ProxyAddress**smtp:Joe@contoso.com**

    b。 此物件的屬性與現有的群組，ProxyAddress 在哪裡變更產生衝突**SMTP:Joe@contoso.com**。

    c。 在 [匯出]， **ProxyAddress 衝突**錯誤狀況而不是由隔離衝突屬性。 當被前恢復功能，在每一個後續的同步處理循環圖中，會重試操作。

2. 如果兩個群組都有相同的 SMTP 位址建立內部部署，失敗進行佈建在第一次嘗試使用標準重複**ProxyAddress**錯誤。 不過，重複的值是正確的隔離在下次同步處理循環。

**Office 入口網站的報表**︰

1. 詳細的錯誤訊息的 UPN 衝突集中的兩個物件都相同。 這表示，他們兩者都有其 UPN 變更 / 隔離，這些項目實際上有任何資料變更時。

2. 詳細的錯誤訊息的 UPN 衝突的使用者顯示的使用者已變更/隔離其 UPN 錯誤的顯示名稱。 例如︰

    。 **使用者**會先使用**UPN = User@contoso.com **。

    b。 **B 使用者**嘗試同步處理設定與下一步**UPN = User@contoso.com **。

    c。 **B 的使用者**UPN 變更為**User1234@contoso.onmicrosoft.com**和**User@contoso.com**會新增至**DirSyncProvisioningErrors**。

    d。 錯誤訊息，**使用者**b 應該指出**使用者的**已有**User@contoso.com**為 UPN，但它會顯示**使用者 B**自己的顯示名稱。



**身分識別同步處理錯誤報告**︰

如需如何解決這個問題的*步驟*連結不正確︰  
    ![作用中的使用者](./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/6.png "Active Users")  

它指向[https://aka.ms/duplicateattributeresiliency](https://aka.ms/duplicateattributeresiliency)。


## <a name="see-also"></a>另請參閱

- [Azure AD Connect 同步處理](active-directory-aadconnectsync-whatis.md)

- [整合您的內部部署識別與 Azure Active Directory](active-directory-aadconnect.md)

- [找出在 Office 365 中的目錄同步處理錯誤](https://support.office.com/en-us/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067)
