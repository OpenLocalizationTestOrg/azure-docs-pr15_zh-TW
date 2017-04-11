<properties
    pageTitle="Azure AD Connect︰ 錯誤疑難排解同步處理期間 |Microsoft Azure"
    description="說明如何疑難排解同步處理的 Azure AD Connect 發生錯誤。"
    services="active-directory"
    documentationCenter=""
    authors="karavar"
    manager="samueld"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="vakarand"/>

# <a name="troubleshooting-errors-during-synchronization"></a>疑難排解同步處理期間的錯誤
識別資料從 Windows Server Active Directory (AD DS) 同步處理到 Azure Active Directory (Azure AD) 時，可能會發生錯誤。 本文提供的一些可能會導致這些錯誤及可能的方式來修正錯誤的案例的同步處理錯誤的不同類型的概觀。 本文包含的常見錯誤類型，並可能不會包含所有可能的錯誤。

 本文假設讀者人員都熟悉的基本[設計 Azure AD 和 Azure AD 概念連線](active-directory-aadconnect-design-concepts.md)。

與最新版 Azure AD Connect\(年 8 月 2016年或更高\)，同步處理錯誤的報表中使用為[Azure 入口網站](https://aka.ms/aadconnecthealth)Azure AD 連線狀況的同步處理的一部分。


啟動 2016 年 9 月 1 日的所有*新*Azure Active Directory 租用戶的預設會啟用[Azure Active Directory 複製屬性恢復](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md)功能。 此功能會自動啟用現有的租用戶中即將來臨的月數。

Azure AD Connect 執行會保留在同步處理的目錄 3 種作業︰ 匯入與匯出同步處理。 錯誤可能需要進行所有作業中的位置。 本文主要著重於錯誤匯出至 Azure AD 期間。

## <a name="errors-during-export-to-azure-ad"></a>在 [匯出至 Azure AD 時的錯誤
追蹤節說明不同類型的 Azure AD 使用 Azure AD 連接器的匯出作業期間會發生的同步處理錯誤。 此連接器可識別名稱格式的 「 contoso。*onmicrosoft.com*」。
匯出至 Azure AD 期間發生的錯誤，以表示作業\(新增、 更新、 刪除等\)嘗試 Azure AD Connect\(同步處理引擎\)上失敗的 Azure Active Directory。

![匯出錯誤概觀](.\media\active-directory-aadconnect-troubleshoot-sync-errors\Export_Errors_Overview_01.png)

## <a name="data-mismatch-errors"></a>資料不相符的錯誤
### <a name="invalidsoftmatch"></a>InvalidSoftMatch
#### <a name="description"></a>描述
- Azure AD 連線時\(同步處理引擎\)指示新增或更新物件的 Azure Active Directory、 Azure AD 符合傳入 Azure AD 中使用物件的**immutableId**屬性**sourceAnchor**屬性的物件。 此相符項目稱為**硬相符**。
- 當 Azure AD**找不到**符合**immutableId**任何物件屬性與**sourceAnchor**屬性的內送的物件，然後才提供新物件，它會回使用 ProxyAddresses 和 UserPrincipalName 屬性找到相符項目。 此相符項目稱為**柔相符**。 柔邊符合被為了符合正在同步處理期間新增/更新代表相同的實體 （使用者、 群組） 內部部署的新物件已經存在 Azure AD （也就取得資料來源中 Azure AD） 中的物件。
- 硬符合找不到任何符合的物件**和**柔符合會找出相符的物件，但該物件的不同值為*immutableId*比內送的物件*SourceAnchor*，建議相符的物件已同步處理與內部部署 Active Directory 中的另一個物件時，會發生**InvalidSoftMatch**錯誤。

換句話說，柔邊的相符項工作的順序，物件柔比對不應該*immutableId*任何值。 如果*immutableId*與任何物件設定值會失敗硬相符，但滿足柔符合準則的作業會導致 InvalidSoftMatch 同步處理錯誤。

Azure Active Directory 結構描述不允許兩個或多個物件具備下列屬性的值相同。 \(不的詳細清單。\)

- ProxyAddresses
- UserPrincipalName
- onPremisesSecurityIdentifier
- ObjectId

>[AZURE.NOTE] [Azure AD 屬性複製屬性恢復](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md)功能也正在首展做為預設行為的 Azure Active Directory。  這將會減少看到 Azure AD Connect （以及其他同步處理用戶端） 的同步處理錯誤的處理重複的 ProxyAddresses 和 UserPrincipalName 屬性存在於上部署 AD 環境中的方式可以在建立 Azure AD 更有彈性。 此功能無法修正重複錯誤。 因此資料仍需要修正。 不過，可讓佈建的新的物件，否則封鎖從 Azure AD 中佈建因為重複的值。 這也會減少同步處理錯誤的形式傳回至同步處理用戶端的數目。
如果您的租用戶啟用此功能，您不會看到的過程中的新物件佈建 InvalidSoftMatch 同步處理錯誤。


#### <a name="example-scenarios-for-invalidsoftmatch"></a>範例案例的 InvalidSoftMatch
1. 內部部署 Active Directory 中有相同值的 ProxyAddresses 屬性的兩個或多個物件。 僅有一個快速提供 Azure AD。
2. 內部部署 Active Directory 中有相同的 userPrincipalName 值的兩個或多個物件。 僅有一個快速提供 Azure AD。
3. 新增一個物件上的部署具有相同值的 ProxyAddresses 屬性的 Active Directory 的 Azure Active Directory 中現有的物件。 Azure Active Directory 中並未取得提供加入內部部署的物件。
4. 新增一個物件中內部部署 Active Directory 具有相同值的 userPrincipalName 屬性的 Azure Active Directory 中的帳戶。 Azure Active Directory 中並未取得提供物件。
5. 同步處理的帳戶已移動樹系從 A 到樹系 B Azure AD Connect （同步處理引擎） 來計算 SourceAnchor 使用 objectguid 資訊屬性。 在樹系移動之後，SourceAnchor 的值會不同。 新的物件 （從樹系 B) 無法在 Azure AD 同步處理現有的物件。
6. 同步處理您使用的是不小心刪除物件從內部部署 Active Directory 和新的物件建立 Active Directory 中相同的實體 （例如使用者） 而不會刪除 Azure Active Directory 中的帳戶。 新帳戶，就無法同步處理與現有的 Azure AD 物件。
7. Azure AD Connect 已解除安裝並重新安裝。 重新安裝時，為 SourceAnchor 選擇不同的屬性。 先前已同步處理的所有物件會都停止同步處理，InvalidSoftMatch 錯誤。

#### <a name="example-case"></a>範例案例︰
1. **百勝 Smith**是 contoso.com 的 Azure Active Directory 中從內部部署**Active Directory 同步處理的使用者
2. 百勝 Smith **UserPrincipalName**設為**bobs@contoso.com**。
3. **」 abcdefghijklmnopqrstuv = = 」**已**immutableId**百勝 Smith Azure Active Directory 中的計算方式使用百勝 Smith **objectguid 資訊**從開啟的 Azure AD Connect **SourceAnchor**部署 Active Directory。
4. 百勝也具有追蹤**proxyAddresses**屬性的值︰
    - smtp:bobs@contoso.com
    - smtp:bob.smith@contoso.com
    - **smtp:bob@contoso.com**
5.  新的使用者，**百勝 taylor 的相片**，會新增至 [開啟部署 Active Directory。
6. 百勝 Taylor **UserPrincipalName**設為**bobt@contoso.com**。
7. **」 abcdefghijkl0123456789 = = 」 「** **sourceAnchor** Azure AD Connect 使用百勝 Taylor **objectguid 資訊**從上由計算部署 Active Directory。 百勝 Taylor 物件有未同步處理到 Azure Active Directory 尚未。
8. 百勝 Taylor 具有下列的值 proxyAddresses 屬性
    - smtp:bobt@contoso.com
    - smtp:bob.taylor@contoso.com
    - **smtp:bob@contoso.com**
9. 同步處理期間 Azure AD Connect 會辨識的內部部署 Active Directory 中的百勝 Taylor，並要求 Azure AD 以相同的變更。
10. Azure AD 將第一次執行辛苦完成的相符項目。 也就是說，它會搜尋如果有任何物件的 immutableId 等於 」 abcdefghijkl0123456789 = = 」。 Azure AD 中的任何其他物件會不有該 immutableId 時，將會失敗硬相符項目。
11. Azure AD 然後會嘗試柔相符項目百勝 taylor 的相片。 也就是說，它會搜尋如果等於三個值，包括使用 proxyAddresses 任何物件smtp:bob@contoso.com
12. Azure AD] 會尋找符合的柔符合準則的百勝 Smith 的物件。 但這個物件的值為 immutableId = 」 abcdefghijklmnopqrstuv = = 」。 表示這個物件是從另一個物件內部部署 Active Directory 同步處理。 因此，Azure AD 無法柔比對這些物件，會導致**InvalidSoftMatch**同步處理錯誤。

#### <a name="how-to-fix-invalidsoftmatch-error"></a>如何修正 InvalidSoftMatch 錯誤
InvalidSoftMatch 錯誤的常見原因是兩個物件具有不同 SourceAnchor \(immutableId\)有 ProxyAddresses 及/或 UserPrincipalName 屬性，這些上 Azure AD 柔符合程序時使用相同的值。 若要修正不正確的軟體相符項目

1.  找出重複的 proxyAddresses、 userPrincipalName 或導致錯誤的其他屬性值。 也會識別這兩個\(或多個\)物件涉及衝突。 [Azure AD 連線狀況的同步處理](https://aka.ms/aadchsyncerrors)產生的報表可以協助您找出兩個物件。
2. 找出哪個物件應該繼續有重複的值，並不應該哪一個物件。
3. 不應該該值物件中移除重複的值。 請注意，請變更目錄物件源自位置中。 在某些情況下，您可能需要刪除其中一個衝突的物件。
4. 如果您在上的部署 AD 中進行變更，讓 Azure AD Connect 同步處理變更。

請注意會更新，每隔 30 分鐘內 Azure AD 連線狀況的同步處理的同步處理錯誤報告，且包含從最新的同步處理嘗試錯誤。

>[AZURE.NOTE] ImmutableId，根據預設，不應該變更物件的存留期間。 如果 Azure AD Connect 的設定不是使用一些記住從上述清單的案例，最後 Azure AD Connect 位置計算不同值，表示在相同的 AD 物件 SourceAnchor 的情況下都有您想要繼續使用現有 Azure AD 物件的實體 （相同的使用者/群組連絡人等）。

#### <a name="related-articles"></a>相關的文章
- [重複或無效的屬性妨礙 Office 365 中的目錄同步處理](https://support.microsoft.com/en-us/kb/2647098)

### <a name="objecttypemismatch"></a>ObjectTypeMismatch
#### <a name="description"></a>描述
當 Azure AD 嘗試軟比對兩個物件時，有可能的兩個不同的 「 物件物件類型] （例如使用者、 群組、 連絡人等） 有相同的值，用來執行柔符合的屬性。 Azure AD 中不允許重複這些屬性，為作業可能會導致 「 ObjectTypeMismatch 」 同步處理錯誤。

#### <a name="example-scenarios-for-objecttypemismatch-error"></a>範例案例 ObjectTypeMismatch 錯誤
- 郵件功能的安全性群組皆會建立在 Office 365。 系統會新增內部部署 （的未同步處理至 Azure AD 尚未） AD 具有相同值的 ProxyAddresses 屬性和 Office 365 群組的新使用者或連絡人。

#### <a name="example-case"></a>範例案例

1. 在 Office 365 中建立新郵件功能的安全性群組稅部門的 it 管理員，並提供為電子郵件地址tax@contoso.com。 這會指派 ProxyAddresses 屬性的值這個群組**smtp:tax@contoso.com**
2. 新使用者加入 Contoso.com 和內部部署與 proxyAddress 為使用者建立帳戶**smtp:tax@contoso.com**
3. Azure AD Connect 會同步處理新的使用者帳戶，就會收到 「 ObjectTypeMismatch 」 錯誤。

#### <a name="how-to-fix-objecttypemismatch-error"></a>如何修正 ObjectTypeMismatch 錯誤
ObjectTypeMismatch 錯誤的常見原因是類型的不同 （使用者、 群組、 連絡人等） 的兩個物件都有相同值的 ProxyAddresses 屬性。 若要修正 ObjectTypeMismatch:

1.  找出重複的 proxyAddresses （或其他屬性），檢視造成的錯誤值。 也會識別這兩個\(或多個\)物件涉及衝突。 [Azure AD 連線狀況的同步處理](https://aka.ms/aadchsyncerrors)產生的報表可以協助您找出兩個物件。
2. 找出哪個物件應該繼續有重複的值，並不應該哪一個物件。
3. 不應該該值物件中移除重複的值。 請注意，請變更目錄物件源自位置中。 在某些情況下，您可能需要刪除其中一個衝突的物件。
4. 如果您在上的部署 AD 中進行變更，讓 Azure AD Connect 同步處理變更。 Azure AD 連線狀況同步處理的內的同步處理錯誤報告取得更新，每隔 30 分鐘，且包含從最新的同步處理嘗試錯誤。


## <a name="duplicate-attributes"></a>重複的屬性
### <a name="attributevaluemustbeunique"></a>AttributeValueMustBeUnique
#### <a name="description"></a>描述
Azure Active Directory 結構描述不允許兩個或多個物件具備下列屬性的值相同。 這是強制 Azure AD 中的每個物件在指定的執行個體有以下屬性的唯一值。

- ProxyAddresses
- UserPrincipalName

如果 Azure AD Connect 嘗試新增新的物件，或針對上述屬性值的已指派給 Azure Active Directory 中的另一個物件以更新現有的物件，操作會導致 「 AttributeValueMustBeUnique 」 同步處理錯誤。
#### <a name="possible-scenarios"></a>可能的案例︰
1. 重複的值會指派到的已同步處理的物件，與同步處理的另一個物件的衝突。

#### <a name="example-case"></a>範例案例︰
1. **百勝 Smith**是 contoso.com 的 Azure Active Directory 中從內部部署 Active Directory 同步處理的使用者
2. 內部部署的百勝 Smith **UserPrincipalName**設為**bobs@contoso.com**。
3. 百勝也具有追蹤**proxyAddresses**屬性的值︰
    - smtp:bobs@contoso.com
    - smtp:bob.smith@contoso.com
    - **smtp:bob@contoso.com**
4. 新的使用者，**百勝 taylor 的相片**，會新增至 [開啟部署 Active Directory。
5. 百勝 Taylor **UserPrincipalName**設為**bobt@contoso.com**。
6. **百勝 Taylor**有**ProxyAddresses**屬性 i 的下列值。 smtp:bobt@contoso.comii。 smtp:bob.taylor@contoso.com
7. 百勝 Taylor 的物件是與同步處理 Azure AD 成功。
8. 系統管理員決定要百勝 Taylor **ProxyAddresses**屬性更新下列值︰ 我。 **smtp:bob@contoso.com**
9. Azure AD 將嘗試更新百勝 Taylor 的物件中 Azure AD 上方的值，但該作業會失敗為的 ProxyAddresses 值已指派給百勝 Smith 結果 」 AttributeValueMustBeUnique 」 錯誤。

#### <a name="how-to-fix-attributevaluemustbeunique-error"></a>如何修正 AttributeValueMustBeUnique 錯誤
AttributeValueMustBeUnique 錯誤的常見原因是兩個物件具有不同 SourceAnchor \(immutableId\)有相同值的 ProxyAddresses 及/或 UserPrincipalName 屬性。 若要修正 AttributeValueMustBeUnique 錯誤

1.  找出重複的 proxyAddresses、 userPrincipalName 或導致錯誤的其他屬性值。 也會識別這兩個\(或多個\)物件涉及衝突。 [Azure AD 連線狀況的同步處理](https://aka.ms/aadchsyncerrors)產生的報表可以協助您找出兩個物件。
2. 找出哪個物件應該繼續有重複的值，並不應該哪一個物件。
3. 不應該該值物件中移除重複的值。 請注意，請變更目錄物件源自位置中。 在某些情況下，您可能需要刪除其中一個衝突的物件。
4. 如果您在上的部署 AD 中進行變更，讓 Azure AD Connect 同步處理錯誤，以取得固定的變更。

#### <a name="related-articles"></a>相關的文章
-[重複或無效的屬性妨礙 Office 365 中的目錄同步處理](https://support.microsoft.com/en-us/kb/2647098)


## <a name="data-validation-failures"></a>資料驗證失敗
### <a name="identitydatavalidationfailed"></a>IdentityDataValidationFailed
#### <a name="description"></a>描述
Azure Active Directory 會強制執行各種限制再讓該資料才能寫入到目錄本身的資料。 這是為了確保使用者最適合使用這項資料而定的應用程式時可能體驗。
#### <a name="scenarios"></a>案例
。 UserPrincipalName 屬性值有無效/不支援的字元。
b。 UserPrincipalName 屬性不符合所需的格式。
#### <a name="how-to-fix-identitydatavalidationfailed-error"></a>如何修正 IdentityDataValidationFailed 錯誤

。 請確定 userPrincipalName 屬性具有支援字元和所需的格式。

#### <a name="related-articles"></a>相關的文章
- [準備透過目錄同步處理到 Office 365 的使用者佈建]( https://support.office.com/en-us/article/Prepare-to-provision-users-through-directory-synchronization-to-Office-365-01920974-9e6f-4331-a370-13aea4e82b3e)


### <a name="datavalidationfailed"></a>DataValidationFailed
#### <a name="description"></a>描述
這是特定的大小寫的**「 DataValidationFailed 」**同步處理錯誤時的使用者的 UserPrincipalName 尾碼從一個聯盟網域變更為另一個聯盟網域。

#### <a name="scenarios"></a>案例
同步處理的使用者，UserPrincipalName 尾碼從一個同盟網域變更為內部部署的另一個同盟網域。 例如， *UserPrincipalName =bob@contoso.com*已變更為*UserPrincipalName = bob@fabrikam.com *。

#### <a name="example"></a>範例
1. 以 UserPrincipalName 使用 Active Directory 中的新使用者加入百勝 Smith，Contoso.com 的帳戶bob@contoso.com
2. 百勝移動 Contoso.com 稱為 Fabrikam.com 不同部門和其 UserPrincipalName 變更為bob@fabrikam.com
3. 同時 contoso.com 和 fabrikam.com 網域是 Azure Active Directory 與同盟的網域。
4. 百勝的 userPrincipalName 並不會更新，並產生 「 DataValidationFailed 」 同步處理錯誤。

#### <a name="how-to-fix"></a>如何修正問題
如果從更新使用者的 UserPrincipalName 尾碼bob@ **contoso.com**至bob@ **fabrikam.com** **contoso.com**和**fabrikam.com**都在哪裡**同盟的網域**，然後依照下列步驟來修正同步處理錯誤

1. 在 [從 Azure AD 更新使用者的 UserPrincipalNamebob@contoso.com至bob@contoso.onmicrosoft.com。 使用 PowerShell 的 Azure AD 模組，您可以使用下列 PowerShell 命令︰`Set-MsolUserPrincipalName -UserPrincipalName bob@contoso.com -NewUserPrincipalName bob@contoso.onmicrosoft.com`

2. 允許下次同步處理週期嘗試同步處理。 同步處理的時間會成功，它會更新到 UserPrincipalName 百勝bob@fabrikam.com如預期般。


## <a name="largeobject"></a>LargeObject
### <a name="description"></a>描述
當屬性超過允許的大小限制、 長度限制或 Azure Active Directory 結構描述所設定的計數限制時，同步處理作業會導致**LargeObject**或**ExceededAllowedLength**同步處理錯誤。 此錯誤通常會發生的下列屬性

- userCertificate
- thumbnailPhoto
- proxyAddresses

### <a name="possible-scenarios"></a>可能的案例
1. 百勝的 userCertificate 屬性儲存太多指派給百勝的憑證。 這些可能包含舊版的過期憑證。
2. 設定 Active Directory 中的百勝的 thmubnailPhoto 太大而無法同步處理 Azure AD 中。
3. 在 ProxyAddresses 屬性 Active Directory 中的自動母體時您使用的是指定的物件 > 500 ProxyAddresses。

### <a name="how-to-fix"></a>如何修正問題

1. 確定允許的限制範圍內造成錯誤的屬性。

## <a name="related-links"></a>相關的連結
- [在 Active Directory 系統管理中心中，找出 Active Directory 物件](https://technet.microsoft.com/library/dd560661.aspx)
- [如何查詢的使用 Azure Active Directory PowerShell 物件的 Azure Active Directory](https://msdn.microsoft.com/library/azure/jj151815.aspx)
