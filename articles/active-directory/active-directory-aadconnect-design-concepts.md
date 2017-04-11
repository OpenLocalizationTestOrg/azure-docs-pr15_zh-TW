<properties
   pageTitle="Azure AD Connect︰ 設計概念 |Microsoft Azure"
   description="本主題詳細說明某些實作設計區域"
   services="active-directory"
   documentationCenter=""
   authors="billmath"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.custom = "azure-ad-connect"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="Identity"
   ms.date="09/13/2016"
   ms.author="billmath"/>

# <a name="azure-ad-connect-design-concepts"></a>Azure AD Connect︰ 設計概念
本主題旨在說明必須考慮的 Azure AD Connect 實作設計期間的區域。 本主題是深度剖析的特定部分，以及其他主題簡要說明這些概念。

## <a name="sourceanchor"></a>sourceAnchor
SourceAnchor 屬性的定義*屬性的物件存留期間不變*。 可以唯一識別為同一個物件內部部署和 Azure AD 物件。 也稱為**immutableId** ，可以交換使用兩個名稱。

Word 不變的 「 無法變更 」、 本主題，請務必。 由於已設定後，就無法變更此屬性的值，請務必挑選設計支援您的狀況。

在下列情況下使用屬性︰

- 當新的同步處理引擎伺服器是內建，或重建損毀修復狀況之後時，這個屬性會連結物件內部部署與 Azure AD 中現有的物件。
- 如果您已同步處理的身分識別模型移動從雲端專用的身分識別，這個屬性會允許 「 辛苦完成的相符項目 」 現有物件中 Azure AD 與內部部署的物件。
- 如果您使用同盟，則與**userPrincipalName**這個屬性會使用宣告中唯一識別使用者。

與相關的使用者，本主題只需 sourceAnchor 說話。 相同的規則套用至所有的物件類型，但僅適用於使用者是此問題通常是考量。

### <a name="selecting-a-good-sourceanchor-attribute"></a>選取 [建議的 sourceAnchor 屬性
屬性值必須遵循下列規則︰

- 長度不超過 60 個字元
    - 字元無法 a-z、 A 至 Z 或 0-9 編碼與計算為 3 個字元
- 不包含特殊字元︰ & # 頁，共 92; ！ # $ % & * + / = ? ^ & #96;{ } |~ < > （) ';: , [ ] " @ _
- 必須是全域唯一
- 必須是字串、 整數或二進位
- 不根據使用者的名稱，這些變更
- 應不區分大小寫，並避免可能因大小寫的值
- 建立物件時應該逐一指派

如果選取的 sourceAnchor 不是字串類型，然後 Azure AD 連線 Base64Encode 屬性值，以確保沒有特殊字元會出現。 如果您是使用 ADFS 與其他同盟伺服器，請確定您也可以 Base64Encode 屬性的伺服器。

SourceAnchor 屬性是區分大小寫。 「 JohnDoe 」 的值不是 「 johndoe 」 相同。 但您不需要兩個不同的物件的大小寫的差異。

如果您有單一樹系內部，然後將您應該使用的屬性是**objectguid 資訊**。 這也是用 Azure AD Connect 中使用快速設定時的屬性，同時使用 DirSync 的屬性。

如果您有多個並不會移動使用者之間樹系和網域， **objectguid 資訊**就會是很好的屬性，甚至在此情況下使用。

如果您的使用者之間移動樹系及網域]，然後您必須在移動與使用者尋找並不會變更，或可以移動的屬性。 建議的方法是介紹模擬的屬性。 可能適合看起來像 GUID 無法保留內容的屬性。 物件建立時，新的 GUID，建立並戳記使用者。 在建立根據**objectguid 資訊**此值，並更新選取的屬性中新增同步處理引擎伺服器，可以建立自訂的同步處理規則。 當您移動物件時，請務必也將複製這個值的內容。

選擇現有的屬性，您知道不會變更為另一種解決方案。 常用的屬性包括**員工識別碼]**。 如果您認為包含字母的屬性，請確定有大小寫 （大寫和小寫） 沒有機會可以變更屬性的值。 不應使用的不正確屬性會包含這些屬性的使用者名稱。 在結婚或離婚，必須名稱若要變更，不允許此屬性。 這也是其中一個原因為何無法甚至可以在 Azure AD Connect 安裝精靈中選取**userPrincipalName**、**郵件**、 等**targetAddress**屬性。 這些屬性中也包含@-character,不允許在 sourceAnchor。

### <a name="changing-the-sourceanchor-attribute"></a>變更 sourceAnchor 屬性
Azure AD 中所建立的物件及身分識別已同步處理之後，就無法變更 sourceAnchor 屬性值。

因此，下列限制︰ Azure AD Connect

- 初始安裝期間僅可設定 sourceAnchor 屬性。 如果您重新執行 [安裝] 精靈，此選項會是唯讀的。 如果您要變更這項設定，然後您必須解除安裝並重新安裝。
- 如果您安裝另一部 Azure AD Connect 伺服器時，您必須先選取先前用的同一個 sourceAnchor 屬性。 如果您先前已經使用 DirSync，移至 Azure AD Connect，您必須使用**objectguid 資訊**因為這是用來 DirSync 的屬性。
- 如果 sourceAnchor] 的值後變更物件已匯出至 Azure AD，然後 Azure AD Connect 同步處理擲回一個錯誤，而且不允許任何其他變更上之前已修正問題的物件和 sourceAnchor 變更回來源目錄。

## <a name="azure-ad-sign-in"></a>Azure AD 登入
同時使用 Azure AD 整合內部部署目錄，請務必瞭解如何同步處理設定會影響方式使用者驗證。 Azure AD 使用 userPrincipalName (UPN) 的使用者進行驗證。 不過，當您同步處理您的使用者，您必須選擇謹慎使用值的 userPrincipalName 屬性。

### <a name="choosing-the-attribute-for-userprincipalname"></a>選擇 userPrincipalName 的屬性
當您選取之提供屬性應該確認 UPN 用於 Azure 的項目中的值

- 屬性值符合 UPN 語法 (RFC 822)，它應該的格式username@domain
- Azure AD 中值的後置字元符合其中一個驗證的自訂網域

在 [快速設定] 屬性的假定的選擇是 userPrincipalName。 如果 userPrincipalName 屬性不包含值要 Azure 登入您的使用者，然後您必須選擇**自訂安裝**。

### <a name="custom-domain-state-and-upn"></a>自訂網域的狀態和 UPN
請務必確保 UPN 尾碼驗證的網域。

John 是為 contoso.com 的使用者。 您想要使用的內部部署 UPN Johnjohn@contoso.com使用者同步處理至您的 Azure AD 目錄 contoso.onmicrosoft.com 之後，登入至 Azure。 若要這麼做，您需要新增及驗證 contoso.com 為自訂網域，才能開始同步處理使用者 Azure AD 中。 如果 John，例如 contoso.com 的 UPN 尾碼 Azure AD 中不相符驗證的網域，然後 Azure AD 取代的 UPN 尾碼 contoso.onmicrosoft.com。

### <a name="non-routable-on-premises-domains-and-upn-for-azure-ad"></a>非舉例來說內部部署網域和 UPN Azure AD
在某些組織有非路由的網域，例如 contoso.local 或簡單單張標籤網域，例如 contoso。 您無法在 Azure AD 驗證的非可路由的網域。 Azure AD Connect 在 Azure AD 可以同步處理驗證網域。 當您建立 Azure AD 目錄時，它會建立成為預設網域，例如 contoso.onmicrosoft.com 您 Azure ad 路由網域。 因此，您需要驗證其他任何路由網域，在這種情況下，以防您不想要同步處理到預設的 onmicrosoft.com 網域。

取得上新增及驗證網域的詳細資訊，請閱讀[將您的自訂網域名稱至 Azure Active Directory](active-directory-add-domain.md) 。

如果您執行非路由網域環境中，且想妥當之後，警告您從繼續進行快速設定，則會偵測 azure AD Connect。 如果您正在操作的非路由網域，則可能的使用者的 UPN 也有非路由式的尾碼。 例如，如果您執行的下 contoso.local，Azure AD Connect 會建議您可以使用自訂設定，而不是使用快速設定。 使用自訂設定，也可以指定應該 UPN 為用來登入 Azure 之後使用者已同步處理至 Azure AD 屬性。

## <a name="next-steps"></a>後續步驟
進一步瞭解[整合您的內部部署識別與 Azure Active Directory](active-directory-aadconnect.md)。
