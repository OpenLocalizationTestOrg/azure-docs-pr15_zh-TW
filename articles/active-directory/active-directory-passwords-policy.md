<properties
    pageTitle="密碼原則和 Azure Active Directory 中的限制 |Microsoft Azure"
    description="將說明適用於密碼的 Azure Active Directory，包括允許的字元、 [長度] 和 [到期原則"
  services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="curtand"/>


# <a name="password-policies-and-restrictions-in-azure-active-directory"></a>密碼原則和 Azure Active Directory 中的限制

本文將說明密碼原則和儲存 Azure AD 目錄中的使用者帳戶相關聯的複雜性需求。

> [AZURE.IMPORTANT] **以下是您，因為您無法登入方面的問題嗎？** 如果是這樣，[下列是您可以變更與重設您自己的密碼](active-directory-passwords-update-your-own-password.md)。

## <a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>套用到所有的使用者帳戶的 UserPrincipalName 原則

需要登入 Azure AD 驗證系統的每個使用者帳戶必須使用該帳戶相關聯的專屬使用者主要名稱 (UPN) 屬性值。 下列表格外框的原則套用至這兩個內部部署的 Active Directory 取得資料來源的使用者帳戶 （同步處理到雲端） 以及雲端專用的使用者帳戶。

|   屬性           |     UserPrincipalName 需求  |
|   ----------------------- |   ----------------------- |
|  允許的字元    |  <ul> <li>A – Z</li> <li>-z </li><li>0-9</li> <li> . - \_ ! \# ^ \~</li></ul> |
|  不允許的字元  | <ul> <li>任何'@'不分隔網域的使用者名稱的字元。</li> <li>不能包含句號字元 」。 「 前面'@'符號</li></ul> |
| 長度限制  |       <ul> <li>總長度不得超過 113 字元</li><li>前的 64 個字元‘@’符號</li><li>48 個字元後的‘@’符號</li></ul>

## <a name="password-policies-that-apply-only-to-cloud-user-accounts"></a>只會套用到雲端的使用者帳戶的密碼原則

下表說明可套用至所建立和 Azure AD 中管理使用者帳戶的可用的密碼原則設定。

|  屬性       |    需求          |
|   ----------------------- |   ----------------------- |
|  允許的字元   |   <ul><li>A – Z</li><li>-z </li><li>0-9</li> <li>@# $ % ^ & * - _ ! + = [{} & #124;\ : ‘ , . ? / ` ~ “ ( ) ;</li></ul> |
|  不允許的字元   |       <ul><li>Unicode 字元</li><li>空格</li><li> **僅限強式密碼**︰ 不能包含點字元 」。 「 前面'@'符號</li></ul> |
|   密碼限制 | <ul><li>8 個字元的最小與最大的 16 個字元</li><li>**僅限強式密碼**︰ 需要 3，不在 4 個動作︰<ul><li>小寫字母的字元</li><li>大寫</li><li>數字 (0-9)</li><li>符號 （請參閱上述密碼限制）</li></ul></li></ul> |
| 密碼到期工期      | <ul><li>預設值︰ **90**天 </li><li>值為可使用 [從 Azure Active Directory 模組的 Windows PowerShell 設定 MsolPasswordPolicy cmdlet 設定。</li></ul> |
| 密碼到期的通知 |  <ul><li>預設值︰ **14**天 （之前密碼過期）</li><li>值為可使用設定 MsolPasswordPolicy cmdlet 設定。</li></ul> |
| 密碼到期 |  <ul><li>預設值︰ **false**天 （表示該密碼到期已啟用） </li><li>您可以使用設定 MsolUser 指令程式的個別使用者帳戶的設定值。 </li></ul> |
|  密碼歷程記錄  | 無法再使用最後的密碼。 |
|  密碼歷程記錄持續時間 | 不限次數 |
|  帳戶鎖定 | 失敗之後 10 失敗登入 （錯誤密碼），請用一分鐘鎖定使用者。 進一步不正確的登入嘗試會鎖定增加工期的使用者。 |


## <a name="next-steps"></a>後續步驟

* **以下是您，因為您無法登入方面的問題嗎？** 如果是這樣，[下列是您可以變更與重設您自己的密碼](active-directory-passwords-update-your-own-password.md)。
* [從任何位置管理您的密碼](active-directory-passwords.md)
* [密碼管理的運作方式](active-directory-passwords-how-it-works.md)
* [密碼管理快速入門](active-directory-passwords-getting-started.md)
* [自訂密碼管理](active-directory-passwords-customize.md)
* [密碼管理的最佳做法](active-directory-passwords-best-practices.md)
* [如何使用密碼管理報表的操作深入資訊](active-directory-passwords-get-insights.md)
* [密碼管理常見問題集](active-directory-passwords-faq.md)
* [疑難排解密碼管理](active-directory-passwords-troubleshoot.md)
* [深入瞭解](active-directory-passwords-learn-more.md)
