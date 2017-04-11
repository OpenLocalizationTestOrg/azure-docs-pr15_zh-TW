<properties
   pageTitle="CSV 檔案格式的 Azure Active Directory B2B 共同作業預覽 |Microsoft Azure"
   description="Azure Active Directory B2B 啟用選擇性地存取您公司的應用程式的商業夥伴支援您跨公司的關聯"
   services="active-directory"
   documentationCenter=""
   authors="viv-liu"
   manager="cliffdi"
   editor=""
   tags=""/>

<tags
   ms.service="active-directory"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="identity"
   ms.date="05/09/2016"
   ms.author="viviali"/>

# <a name="azure-ad-b2b-collaboration-preview-csv-file-format"></a>Azure AD B2B 共同作業預覽︰ CSV 檔案格式

Azure AD B2B 共同作業的 preview 版本需要 CSV 檔案，指定透過 Azure AD 入口網站上傳的合作夥伴使用者資訊。 CSV 檔案包含必要的標籤下，並視選用的功能變數。 修改樣本 CSV 檔案 （如下所示），而不變更標籤的第一列的拼字。

>[AZURE.NOTE] 第一列的標籤 （例如電子郵件、 顯示名稱，等等），則需要成功剖析 CSV 檔案。 拼字必須符合下列指定的欄位。 這些標籤找出在其下的內容。 不需要的選用欄位，可以從 CSV 檔案移除其標籤。 整欄可以保留空白。

## <a name="required-fields-br"></a>必要的欄位︰ <br/>
**電子郵件︰**邀請使用者的電子郵件地址。 <br/>
**DisplayName:**邀請使用者 （通常是，第一個和最後一個名稱） 的顯示名稱。<br/>


## <a name="optional-fields-br"></a>選用的功能變數︰ <br/>

**InvitationText:**自訂邀請電子郵件文字後商標應用程式，並在贖回連結。<br/>
**InvitedToApplications:**若要指派給使用者的公司應用程式 AppIDs。 AppIDs，則可電話會在 PowerShell 中除外`Get-MsolServicePrincipal | fl DisplayName, AppPrincipalId`<br/>
**InvitedToGroups:**若要新增使用者至群組的 ObjectIDs。 ObjectIDs，則可電話會在 PowerShell 中除外`Get-MsolGroup | fl DisplayName, ObjectId`<br/>
**InviteRedirectURL:**邀請的使用者引導接受邀請之後的 URL。 應該是公司特定 URL （例如[*contoso.my.salesforce.com*](http://contoso.my.salesforce.com/))。 如果未指定選擇性的欄位，他們可以瀏覽至您所選的企業應用程式的應用程式存取面板會導向受邀請的使用者。 應用程式存取面板 URL 是表單的`https://account.activedirectory.windowsazure.com/applications/default.aspx?tenantId=<TenantID>`。<br/>
**CcEmailAddress**︰ 電子郵件地址複製郵件的邀請。 如果使用 CcEmailAddress 欄位，則此邀請無法用於電子郵件驗證的使用者或建立租用戶。<br/>
**語言︰**邀請電子郵件與贖回的體驗，使用 「 en 」 （英文） 時未指定的預設語言。 其他 10 的支援的語言的程式碼會︰<br/>
1. de︰ 德文<br/>
2. es︰ 西班牙文<br/>
3. 這個方法︰ 法文<br/>
4. 它︰ 義大利文<br/>
5. ja︰ 日文<br/>
6. 弩︰ 韓文<br/>
7. pt 巴西︰ 葡萄牙文 （巴西）<br/>
8. ru︰ 俄文<br/>
9. 曆法 HANS︰ 簡體中文<br/>
10. 曆法 HANT︰ 繁體中文<br/>

## <a name="sample-csv-file"></a>範例 CSV 檔案
以下是範例 CSV 您可以修改。

>[AZURE.NOTE] 複製並貼上此到 [記事本]，使用副檔名為 「.csv 」 將其儲存。 然後在 Excel 中編輯此。 它會將標籤的第一列的資料表結構化。

> 在 Excel 中新增更多選用的功能變數，藉由指定標籤，並填入其下則是資料行。

```
Email,DisplayName,InvitationText,InviteRedirectUrl,InvitedToApplications,InvitedToGroups,CcEmailAddress,Language
wharp@contoso.com,Walter Harp,Hi Walter! I hope you’ve been doing well.,,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,,you@yourcompany.com,en
jsmith@contoso.com,Jeff Smith,Hi Jeff! I hope you’ve been doing well.,,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,,you@yourcompany.com,en
bsmith@contoso.com,Ben Smith,Hi Ben! I hope you’ve been doing well.,,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,,you@yourcompany.com,en

```

## <a name="related-articles"></a>相關的文章
瀏覽其他文章 Azure AD B2B 共同作業

- [什麼是 Azure AD B2B 共同作業？](active-directory-b2b-what-is-azure-ad-b2b.md)
- [運作方式](active-directory-b2b-how-it-works.md)
- [詳細逐步解說](active-directory-b2b-detailed-walkthrough.md)
- [外部使用者 token 格式](active-directory-b2b-references-external-user-token-format.md)
- [變更外部使用者物件屬性](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [目前的預覽限制](active-directory-b2b-current-preview-limitations.md)
- [Azure Active Directory 中的應用程式管理文件索引](active-directory-apps-index.md)
