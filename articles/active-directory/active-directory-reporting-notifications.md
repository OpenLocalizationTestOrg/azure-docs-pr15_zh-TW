<properties
    pageTitle="Azure Active Directory 報告通知"
    description="如何使用報告可疑的通知 Azure Active Directory 集。"
    services="active-directory"
    documentationCenter=""
    authors="dhanyahk"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="03/07/2016"
    ms.author="dhanyahk"/>

# <a name="azure-active-directory-reporting-notifications"></a>Azure Active Directory 報告通知

## <a name="what-reports-generate-email-notifications"></a>哪些報表產生電子郵件通知

此時，[不規則登入] 活動報表引動程序電子郵件通知。

## <a name="what-is-an-irregular-sign-in"></a>什麼是 「 不規則登入 」？

不規則登增益集是已由我們電腦學習演算法，根據結合異常登入位置與裝置的 「 無法旅遊 」 條件。 這可能表示駭客具有已嘗試使用此帳戶登入。

## <a name="who-receives-the-email-notifications"></a>誰會收到電子郵件通知？

電子郵件會傳送給所有的全域管理員被指派 Active Directory 進階版授權。 若要確保它已傳送，我們將其傳送給管理員替代電子郵件地址以及。 管理員應包含aad-alerts-noreply@mail.windowsazure.com在其安全的寄件者清單，讓他們未接到的電子郵件中。

## <a name="how-often-are-these-emails-sent"></a>傳送這些電子郵件的頻率為何？

如果 10 新不規則登入活動發生前 30 天，或傳送的最後一個電子郵件，因為以小於，會傳送電子郵件。

## <a name="how-do-i-access-the-report-mentioned-in-the-email"></a>我要如何存取電子郵件中所提及的報表？

當您按一下連結時，會被重新導向至 Azure 傳統入口網站中的 [報表] 頁面。 若要存取報表，您必須同時︰

- 系統管理員或 Azure 訂閱的共同管理員

- 在目錄中，全域管理員，並指派 Active Directory 進階版授權。 如需詳細資訊，請參閱[Azure Active Directory 版本](active-directory-editions.md)。

## <a name="can-i-turn-off-these-emails"></a>我可以關閉這些電子郵件嗎？

是，若要關閉通知相關的異常登增益集 Azure 傳統入口網站中，按一下 [**設定**]，然後選取 [**通知**] 區段底下的 [**停用**。

## <a name="whats-next"></a>下一步是什麼
- 好奇哪些安全性、 稽核及活動報表可供使用？ 請參閱[Azure AD 安全性、 稽核及活動報表](active-directory-view-access-usage-reports.md)
- [Azure Active Directory 進階版快速入門](active-directory-get-started-premium.md)
- [新增公司商標至登入並存取面板頁面](active-directory-add-company-branding.md)
