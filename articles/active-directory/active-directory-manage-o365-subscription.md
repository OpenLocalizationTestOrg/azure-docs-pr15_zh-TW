<properties
   pageTitle="管理您的 Office 365 訂閱 Azure 中的目錄 |Microsoft Azure"
   description="管理 Azure Active Directory 和 Azure 傳統入口網站使用的 Office 365 訂閱目錄"
   services="active-directory"
   documentationCenter=""
   authors="curtand"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/23/2016"
   ms.author="curtand"/>

# <a name="manage-the-directory-for-your-office-365-subscription-in-azure"></a>管理您的 Office 365 訂閱 Azure 中的目錄

本文將說明如何管理 Office 365 訂閱，使用 [Azure 傳統入口網站所建立的目錄。 您必須是服務管理員或 Azure 訂閱的共同管理員 Azure 傳統入口網站登入。 如果您還沒有 Azure 的訂閱，您可以註冊[免費試用 30 天](https://azure.microsoft.com/trial/get-started-active-directory/)今天，並部署底下 5 分鐘，您的第一個雲端方案，使用此連結。 請務必使用您用於登入 Office 365 的公司或學校帳戶。

完成 Azure 訂閱之後，您可以登入 Azure 傳統入口網站，以及存取 Azure 服務。 按一下 [管理相同的目錄來驗證您的 Office 365 使用者的 Active Directory 副檔名。

如果您已經 Azure 的訂閱，管理額外的目錄的程序也是乾淨的簡單。 例如，Michael Smith 可能會有 Office 365 訂閱 Contoso.com。 他也是他註冊使用其 Microsoft 帳戶，Azure 訂閱msmith@hotmail.com。 在此情況下，他會管理兩個目錄。

  訂閱 |  Office 365  |  Azure
  -------------- | ------------- | -------------------------------
  顯示名稱 |  Contoso  |     預設的 Azure Active Directory (Azure AD) 目錄
  網域名稱  |  contoso.com  | msmithhotmail.onmicrosoft.com

他想要管理 Contoso 目錄中的使用者身分識別，當他登入 Azure 使用其 Microsoft 帳戶，使其可啟用 Azure AD 功能，例如多重要素驗證。 下圖可能有助於說明程序。

![若要管理兩個獨立目錄的圖表](./media/active-directory-manage-o365-subscription/AAD_O365_03.png)

在此情況下，則兩個目錄是獨立的每個。

## <a name="to-manage-two-independent-directories"></a>若要管理兩個獨立的目錄
用來管理兩個目錄時他登入 Azure 為 Michael Smith 順序msmith@hotmail.com,他必須完成下列步驟︰

> [AZURE.NOTE]
> 使用者以 Microsoft 帳戶登入時，才可完成這些步驟。 如果使用者已登入公司或學校帳戶，可以**使用現有的目錄**會無法使用。 公司或學校帳戶驗證只能由其主目錄 （也就是儲存的工作或學校帳戶，和目錄企業或學校擁有）。

1.  登入[Azure 傳統入口網站](https://manage.windowsazure.com)以msmith@hotmail.com。
2.  按一下 [**新增** > **應用程式服務** > **Active Directory** > **目錄** > **建立自訂**。
3.  按一下 [使用現有的目錄，然後選取**我已經準備好現在已登出**核取方塊。
4.  以全域管理員 Contoso.onmicrosoft.com 的登入 Azure 傳統入口網站 (例如， msmith@contoso.com)。
5.  當畫面提示您**Contoso 目錄使用 Azure？**，按一下 [**繼續**]。
6.  按一下 [**立即登出**]。
7.  Azure 傳統入口網站登入msmith@hotmail.com。 Contoso 目錄及預設的目錄會出現在 Active Directory 副檔名。

完成這些步驟後, msmith@hotmail.com Contoso 目錄中的全域管理員。

## <a name="to-administer-resources-as-the-global-admin"></a>身為全域管理員管理資源
現在讓我們假設陳為何需要管理網站與資料庫 Azure 訂閱相關聯的資源msmith@hotmail.com。 晨怡也可以這麼做之前，Michael Smith 必須完成這些額外的步驟︰

1.  [Azure 傳統入口網站](https://manage.windowsazure.com)使用 Azure 訂閱服務系統管理員帳戶登入 (在此範例中， msmith@hotmail.com)。
2.  將訂閱轉移到 Contoso 目錄︰ 按一下 [**設定** > **訂閱**> 選取 [訂閱] >**編輯目錄**> 選取**Contoso (Contoso.com)**。 傳輸、 任何工作或學校的一部分移除訂閱的共同管理員的帳戶。
3.  新增訂閱的共同管理員陳華︰ 按一下 [**設定** > **管理員**> 選取 [訂閱] >**新增**> 類型**JohnDoe@Contoso.com**。

## <a name="next-steps"></a>後續步驟
如需有關訂閱與目錄之間的關係的詳細資訊，請參閱[如何訂閱的目錄與相關聯](active-directory-how-subscriptions-associated-directory.md)。
