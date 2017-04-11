<properties
    pageTitle="Azure AD Connect 同步處理︰ 了解使用者及連絡人 |Microsoft Azure"
    description="說明使用者和 Azure AD Connect 同步處理的連絡人。"
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
    ms.date="10/10/2016"
    ms.author="markusvi;andkjell"/>


# <a name="azure-ad-connect-sync-understanding-users-and-contacts"></a>Azure AD Connect 同步處理︰ 了解使用者及連絡人

有數種不同的原因為何會有多個 Active Directory 和有數種不同的部署拓撲。 常見的模型合併與擷取後加上的帳戶資源部署及 GAL sync'ed 樹系。 但即使有純粹模型，混合式部署模型常見以及。 Azure AD Connect 同步處理中的預設設定不會假設任何特定的模型，但根據如何使用者比對所選的安裝指南中，您可以觀察不同的行為。

本主題中，我們會在特定拓撲中的預設設定的行為方式。 我們會設定和同步處理規則編輯器可用來查看設定。

有幾個一般的規則，假設設定︰

- 無論順序，我們匯入的來源作用中的目錄，最後的結果應該保持不變。
- 使用中的帳戶一律會提供登入資訊，包括**userPrincipalName**和**sourceAnchor**。
- 如果找不到沒有作用中的帳戶是一個連結的信箱，除非 userPrincipalName 和 sourceAnchor，會提供停用的帳戶。
- 帳戶與連結的信箱不會使用 userPrincipalName 和 sourceAnchor。 它會引用本身的使用中的帳戶，則會找到更新版本。
- 連絡人的物件可能會佈建 Azure ad 為連絡人，或以的使用者。 您不知道直到在處理完所有來源 Active Directory 樹系。

## <a name="contacts"></a>連絡人

代表不同樹系使用者的連絡人是常見之後合併與擷取 GALSync 解決方案橋兩個或多個 Exchange 樹系的位置。 連絡人物件永遠加入從連接器空間 metaverse 使用郵件屬性。 如果已經有連絡人的物件或使用相同的郵件地址的使用者物件，物件會結合在一起。 這設定規則中**中從 AD – 連絡人加入**。 也有規則命名**中從 AD – 連絡人常見**到 metaverse 屬性流程屬性**sourceObjectType**與固定的**連絡人**。 此規則有極低的優先順序，如果使用者物件加入 metaverse 相同的物件，然後規則**中從 AD – 使用者常見**會提供此屬性的值使用者。 此規則，這個屬性會有值，如果已經加入沒有使用者的連絡人及值使用者如果已經找到至少有一位使用者。

提供 Azure AD 物件，如果 metaverse 屬性**sourceObjectType**設定為 [**連絡****外出 AAD – 連絡人加入**輸出規則時，會建立一個連絡人的物件。 如果**使用者**設定此屬性，然後規則**外出 AAD – 使用者加入**會建立一個使用者物件改為。
有可能的物件就會升級來自連絡人的使用者更多來源作用中的目錄會匯入及同步處理。

例如，GALSync 拓撲中我們會尋找連絡人物件的每個人中第二個樹系我們匯入第一個樹系時。 這會階段 AAD 連接器中新增連絡人的物件。 當我們稍後匯入並同步處理的第二個樹系時，我們會找出實際的使用者，並加入現有的 metaverse 物件。 然後，我們會刪除連絡人物件中 AAD 並改為建立新的使用者物件。

如果您有拓撲位置的使用者，表示為連絡人，請確認您選取要符合郵件屬性安裝指南中的使用者。 如果您選擇其他選項，您就會有順序相依設定。 連絡人的物件會永遠加入郵件屬性，但在安裝指南選取這個選項，使用者物件只會在郵件屬性上加入。 您無法再最後兩個不同的物件，以相同的郵件屬性斷如果使用者物件之前匯入連絡人的物件。 在 [匯出至 Azure AD，會擲回錯誤。 這種行為的設計，而不正確的資料或的拓撲不正確識別出在安裝期間，則表示。

## <a name="disabled-accounts"></a>停用的帳戶

停用的帳戶，也會同步處理至 Azure AD。 停用的帳戶是常見代表 Exchange，例如會議室中的資源。 例外狀況是使用者與連結的信箱。如先前所述，這些會永遠不會佈建 Azure ad 帳戶。

假設是，如果找不到停用的使用者帳戶，然後我們將找不到另一個使用中的帳戶更新版本，物件佈建到 Azure AD userPrincipalName 與 sourceAnchor 找到。 萬一作用中的另一個帳戶會加入至相同的 metaverse 物件，然後將使用其 userPrincipalName 和 sourceAnchor。

## <a name="changing-sourceanchor"></a>變更 sourceAnchor

具有已物件匯出 Azure AD，然後再變更 sourceAnchor 不允許時。 當已匯出物件 metaverse 屬性**cloudSourceAnchor**會設定接受 Azure AD **sourceAnchor**值。 如果**sourceAnchor**變更時，不符合**cloudSourceAnchor****外出 AAD – 使用者加入**規則，將會擲回**sourceAnchor 屬性已變更**的錯誤。 在此情況下，設定或資料必須修正相同 sourceAnchor 為簡報斷再次之前物件可以一次同步處理。

## <a name="additional-resources"></a>其他資源

* [Azure AD 連線同步處理︰ 自訂同步處理選項](active-directory-aadconnectsync-whatis.md)
* [整合您的內部部署識別與 Azure Active Directory](active-directory-aadconnect.md)
