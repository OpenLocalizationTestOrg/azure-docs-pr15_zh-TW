<properties
   pageTitle="使用 Azure Active Directory B2B 共同作業預覽的詳細逐步解說 |Microsoft Azure"
   description="Azure Active Directory B2B 共同作業啟用選擇性地存取您公司的應用程式的商業夥伴支援您跨公司的關聯"
   services="active-directory"
   documentationCenter=""
   authors="viv-liu"
   manager="cliffdi"
   editor=""
   tags=""/>

<tags
   ms.service="active-directory"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="identity"
   ms.date="05/09/2016"
   ms.author="viviali"/>

# <a name="azure-ad-b2b-collaboration-preview-detailed-walkthrough"></a>Azure AD B2B 共同作業預覽︰ 詳細逐步解說

此逐步解說說明如何使用 Azure AD B2B 共同作業。 Contoso IT 系統管理員，我們想要共用的應用程式與三個合作夥伴公司的員工。 無的合作夥伴公司需要 Azure AD。

- 從簡單的合作夥伴組織 Alice
- 百勝，從中型合作夥伴組織，需要存取應用程式的一組
- Carol，從複雜的合作夥伴組織，需要存取一組的應用程式，以及在 Contoso] 群組中的成員資格

邀請會傳送至協力廠商的使用者後，我們可以設定這些 Azure AD 授與存取應用程式，並透過 Azure 入口網站的群組的成員資格。 現在就讓我們來新增 Alice 啟動。

## <a name="adding-alice-to-the-contoso-directory"></a>新增 Alice 到 Contoso 目錄
1. 使用標題建立.csv 檔案，如下圖所示，填入只 Alice 的**電子郵件**、**顯示名稱**及**InviteContactUsUrl**。 **顯示名稱**為的名稱會出現在 [邀請]，並且也 Contoso Azure AD 目錄中顯示的名稱。 **InviteContactUsUrl**是連絡 Contoso Alice 的方式。 在下列範例中，InviteContactUsUrl 指定 Contoso LinkedIn 設定檔。 請務必拼字指定[CSV 檔案格式參照](active-directory-b2b-references-csv-file-format.md)中的相同的.csv 檔案的第一列中的標籤。  
![範例 CSV 檔案的 Alice](./media/active-directory-b2b-detailed-walkthrough/AliceCSV.png)

2. 在 Azure 入口網站中，將使用者新增到 Contoso 目錄 (Active Directory > Contoso > 使用者 > 新增使用者)。 在 「 使用者類型] 下拉式清單中，選取 「 合作夥伴公司中的使用者]。 上傳的.csv 檔案。 請確定.csv 檔案已關閉之前上傳。  
![CSV 檔案上傳的 Alice](./media/active-directory-b2b-detailed-walkthrough/AliceUpload.png)

3. Alice 現在是以外部使用者 Contoso Azure AD 目錄中表示。  
![Alice 會列於 Azure AD](./media/active-directory-b2b-detailed-walkthrough/AliceInAD.png)

4. Alice 會收到下列電子郵件。  
![Alice 邀請電子郵件](./media/active-directory-b2b-detailed-walkthrough/AliceEmail.png)

5. Alice 按一下連結，然後她會提示您接受邀請，並使用其公司認證登入]。 如果 Alice 不是 Azure AD 目錄中，Alice 會提示您登入。  
![註冊後 Alice 邀請](./media/active-directory-b2b-detailed-walkthrough/AliceSignUp.png)

6. Alice 會重新導向至應用程式存取面板，空白，直到她授與存取應用程式。  
![Alice 存取面板](./media/active-directory-b2b-detailed-walkthrough/AliceAccessPanel.png)

此程序可讓 B2B 共同作業的最簡單的表單。 以 Contoso Azure AD 目錄中的使用者，Alice 可以授與存取應用程式與透過 Azure 入口網站的群組。 現在讓我們來新增百勝，需要存取 Moodle 和 Salesforce 應用程式。

## <a name="adding-bob-to-the-contoso-directory-and-granting-access-to-apps"></a>將百勝新增至 Contoso 目錄，並授與存取應用程式
1. 若要尋找的 Moodle 應用程式識別碼與 Salesforce 安裝使用 Windows PowerShell 的 Azure AD 模組。 可以使用 cmdlet 擷取識別碼︰`Get-MsolServicePrincipal | fl DisplayName, AppPrincipalId`如此會開啟 Contoso 和其 AppPrincialIds 中所有可用的應用程式的清單。  
![百勝擷取識別碼](./media/active-directory-b2b-detailed-walkthrough/BobPowerShell.png)

2. 建立包含百勝的電子郵件與顯示名稱、 **InviteAppID**、 **InviteAppResources**，以及 InviteContactUsUrl.csv 檔案。 填入**InviteAppResources** AppPrincipalIds 的 Moodle 與 Salesforce 找到 PowerShell，並以空格分隔。 填入**InviteAppId**與相同 AppPrincipalId 的 Moodle 品牌電子郵件並登入具有 Moodle 標誌的頁面。  
![百勝的範例 CSV 檔案](./media/active-directory-b2b-detailed-walkthrough/BobCSV.png)

3. 它已完成的 Alice 一樣上, 傳透過 Azure 入口網站的.csv 檔案。 百勝現在 Contoso Azure AD 目錄中的外部使用者。

4. 百勝會收到下列電子郵件。  
![百勝的邀請電子郵件](./media/active-directory-b2b-detailed-walkthrough/BobEmail.png)

5. 百勝按一下連結，並會提示您接受邀請。 他登入之後，他會導向至存取面板，並可以使用 Moodle 和 Salesforce。  
![百勝存取面板](./media/active-directory-b2b-detailed-walkthrough/BobAccessPanel.png)

我們會將 Carol 接下來，需要存取應用程式及 Contoso 目錄中的群組的成員資格的人員。

## <a name="adding-carol-to-the-contoso-directory-granting-access-to-apps-and-giving-group-membership"></a>加入 Carol Contoso 目錄、 授與存取應用程式，並授與群組成員資格

1. 若要尋找應用程式識別碼與群組 Id Contoso 內安裝使用 Windows PowerShell 的 Azure AD 模組。
 - 擷取 AppPrincipalId 使用 cmdlet `Get-MsolServicePrincipal | fl DisplayName, AppPrincipalId`、 相同先百勝
 - 擷取群組使用 cmdlet ObjectId `Get-MsolGroup | fl DisplayName, ObjectId`。 如此會開啟一份 Contoso 和其 ObjectIds 中的所有群組。 也可以與 Azure 入口網站中的群組的 [屬性] 索引標籤中的物件識別碼擷取群組識別碼。  
![Carol 擷取識別碼和群組](./media/active-directory-b2b-detailed-walkthrough/CarolPowerShell.png)

2. 建立.csv 檔案，填入 Carol 的電子郵件、 顯示名稱、 InviteAppID、 InviteAppResources、 **InviteGroupResources**及 InviteContactUsUrl。 群組 MyGroup1 和外部，以空格分隔的 ObjectIds 會填入**InviteGroupResources** 。  
![Carol 的範例 CSV 檔案](./media/active-directory-b2b-detailed-walkthrough/CarolCSV.png)

3. 上傳透過 Azure 入口網站的.csv 檔案。

4. Carol 是 Contoso 目錄中的使用者同時也是 MyGroup1] 和 [外部，群組的成員 Azure 入口網站中所見。  
![Carol Azure AD 中列出的群組](./media/active-directory-b2b-detailed-walkthrough/CarolGroup.png)

5. 收件者會收到包含連結接受邀請電子郵件。 她登入後，她會重新導向至有權存取 Moodle 和 Salesforce 應用程式存取面板。  

這就是從 Azure AD B2B 共同合作夥伴企業中新增使用者。 此逐步解說說明如何新增使用者 Alice 及百勝，並 Carol Contoso 目錄使用三個不同的.csv 檔案。 此程序可以讓您更容易成單一檔案壓縮個別的.csv 檔案。  
![範例 CSV 檔案用於 Alice 與百勝，收件者](./media/active-directory-b2b-detailed-walkthrough/CombinedCSV.png)

## <a name="related-articles"></a>相關的文章
瀏覽其他文章 Azure AD B2B 共同作業︰

- [什麼是 Azure AD B2B 共同作業？](active-directory-b2b-what-is-azure-ad-b2b.md)
- [運作方式](active-directory-b2b-how-it-works.md)
- [CSV 檔案格式的參照](active-directory-b2b-references-csv-file-format.md)
- [外部使用者 token 格式](active-directory-b2b-references-external-user-token-format.md)
- [變更外部使用者物件屬性](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [目前的預覽限制](active-directory-b2b-current-preview-limitations.md)
- [Azure Active Directory 中的應用程式管理文件索引](active-directory-apps-index.md)
