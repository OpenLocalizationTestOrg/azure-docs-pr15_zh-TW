<properties
    pageTitle="Azure AD Connect 同步處理︰ 了解宣告式佈建運算式 |Microsoft Azure"
    description="說明宣告式佈建的運算式。"
    services="active-directory"
    documentationCenter=""
    authors="andkjell"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/31/2016"
    ms.author="markusvi;andkjell"/>


# <a name="azure-ad-connect-sync-understanding-declarative-provisioning-expressions"></a>Azure AD Connect 同步處理︰ 了解宣告式佈建運算式
宣告式佈建在 Forefront 身分識別 Manager 2010 根據 azure AD Connect 同步處理。 其可讓您實作完成的身分識別整合商務邏輯而不需要使用撰寫編譯程式碼。

宣告式佈建不可或缺的一部分是屬性流程中使用的運算式語言。 使用的語言是 Microsoft® 第® Visual Basic for Applications (VBA) 的子集合。 在 Microsoft Office 中使用此語言和使用者體驗的 VBScript 也會將它。 宣告式佈建運算式語言只使用函數，而且不會結構化的語言。 沒有方法或陳述式。 快速程式流程改為是巢狀函數。

如需詳細資訊，請參閱[歡迎使用 Visual Basic 的 Office 2013 應用程式語言參考](https://msdn.microsoft.com/library/gg264383.aspx)。

屬性是強型別。 函數只接受正確類型的屬性。 這也是區分大小寫。 函數名稱和屬性名稱都必須有適當的大小寫，或錯誤會擲回 expression.error。

## <a name="language-definitions-and-identifiers"></a>語言定義和識別項

- 函數具有名字，後面接著以括弧括住的引數︰ 上 （引數 1，引數 N）。
- 方括號來識別屬性: [attributeName]
- 參數以百分比符號識別: %parametername%
- 字串常數以引號括住:，例如 「 Contoso 」 (附註︰ 必須使用一般引號 」 取代 「 」 和不智慧引號 」 」)
- 數字的值表示沒有報價，應該是小數位數。 前置字元的十六進位值為 & h 例如，98052，與 h f f
- 布林值表示常數︰ True、 False。
- 內建的常數和常值會使用他們的名稱來表示︰ null 值，CRLF，IgnoreThisFlow

### <a name="functions"></a>函數
宣告式佈建啟用轉換屬性值可能會使用許多函數。 這些函數可巢狀，以便從一個函數的結果會傳遞給另一個函數。

`Function1(Function2(Function3()))`

[函數參考](active-directory-aadconnectsync-functions-reference.md)中找函數的完整清單。

### <a name="parameters"></a>參數
參數被定義連接器或使用 PowerShell 以系統管理員。 參數通常包含有不同系統的值，例如使用者的網域名稱位於。 在屬性流可這些參數。

Active Directory 連接器的輸入同步處理規則提供下列參數︰

| 參數名稱 | 註解 |
| --- | --- |
| Domain.Netbios | 目前正在匯入的網域，例如 FABRIKAMSALES 的 Netbios 格式 |
| Domain.FQDN | 目前正在匯入的網域，例如 sales.fabrikam.com 的 FQDN 格式 |
| Domain.LDAP | 目前正在匯入的網域，例如 DC LDAP 格式 = 銷售額，亦即 = fabrikam，亦即 = com |
| Forest.Netbios | 目前正在匯入的樹系名稱，例如 FABRIKAMCORP Netbios 格式 |
| Forest.FQDN | 目前正在匯入，樹系名稱，例如 fabrikam.com 的 FQDN 格式 |
| Forest.LDAP | LDAP 名稱的格式樹系目前正在匯入，例如 DC = fabrikam，亦即 = com |

系統會提供下列參數，可用來取得目前執行的連接器識別碼︰  
`Connector.ID`

以下是網域的填入 metaverse 屬性網域搭配 netbios 使用者所在名稱的範例︰  
`domain` <- `%Domain.Netbios%`

### <a name="operators"></a>運算子
可以使用以下運算子︰

- **比較**︰ <，< =、 <>、 =、 > > =
- **數學**: +、-， \*、-
- **字串**︰ & （串連）
- **邏輯**︰ & & （及） 個管線（或者）
- **評估順序**: （)

運算子由左至右評估，和具有相同的評估優先順序。 就是\*（加成） 不會評估前-（減號）。 2\*(5 + 3) 不是 2 相同\*5 + 3。 括號 （） 用來變更時從左到右評估順序不適當的評估順序。

## <a name="multi-valued-attributes"></a>多重值的屬性
函數可以操作單一值及多重值屬性。 多重值屬性，函數會在每個值的運作方式與適用於每個值相同的功能。

例如︰  
`Trim([proxyAddresses])`執行 proxyAddress 屬性中的每個值的修整。  
`Word([proxyAddresses],1,"@") & "@contoso.com"`為每個值與@-sign,取代網域搭配@contoso.com。  
`IIF(InStr([proxyAddresses],"SIP:")=1,NULL,[proxyAddresses])`尋找 SIP 位址，並將它移除的值。

## <a name="next-steps"></a>後續步驟

- 了解更多關於設定模型中[的瞭解程度宣告式佈建](active-directory-aadconnectsync-understanding-declarative-provisioning.md)的資訊。
- 請參閱如何宣告式佈建是使用的出方塊了解[的預設設定](active-directory-aadconnectsync-understanding-default-configuration.md)。
- 瞭解如何變更實際使用宣告式佈建如何[進行的變更為預設設定](active-directory-aadconnectsync-change-the-configuration.md)。

**概觀主題**

- [Azure AD Connect 同步處理︰ 了解並自訂同步處理](active-directory-aadconnectsync-whatis.md)
- [整合您的內部部署識別與 Azure Active Directory](active-directory-aadconnect.md)

**參考主題**

- [Azure AD Connect 同步處理︰ 函數參考](active-directory-aadconnectsync-functions-reference.md)
