<properties
    pageTitle="Azure Active Directory B2C︰ 可延伸原則架構 |Microsoft Azure"
    description="主題可延伸原則架構中的 Azure Active Directory B2C 及如何建立各種原則類型"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/24/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-extensible-policy-framework"></a>Azure Active Directory B2C︰ 可延伸原則架構

## <a name="the-basics"></a>基本概念

可延伸原則架構的 Azure Active Directory (Azure AD) B2C 是服務的核心強度。 原則完整描述消費者身分識別體驗，例如註冊，登入或設定檔編輯。 例如，註冊原則可讓您設定下列設定來控制行為︰

- 帳戶類型 （例如 Facebook 社交帳戶或本機帳戶，例如電子郵件地址） 的使用者皆可使用應用程式的登入。
- 若要在註冊期間收集從消費者的屬性 （例如，名字、 郵遞區號，與鞋子大小）。
- 使用多重因素驗證。
- 外觀與風格的所有註冊的頁面。
- （此為宣告權杖資訊清單） 的資訊的應用程式接收何時原則執行完成。

您可以在 [您的租用戶中建立不同類型的多個原則，並視需要在您的應用程式中使用它們。 原則可重複使用的應用程式。 這個選項可讓開發人員定義和修改的最小的消費者身分識別體驗或不變更其程式碼。

原則可供使用透過簡單的開發人員介面。 您的應用程式使用標準 HTTP 驗證要求 （傳送邀請中的原則參數） 原則觸發程序，並會接收為回應的自訂的權杖。 比方說，只之間的差異要求叫用註冊原則，而那些叫用登入原則是原則名稱"p"的查詢字串參數中使用︰

```

https://login.microsoftonline.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siup                                       // Your sign-up policy

```

```

https://login.microsoftonline.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siin                                       // Your sign-in policy

```

如需原則架構的詳細資訊，請參閱此[部落格文章](http://blogs.technet.com/b/ad/archive/2015/11/02/a-look-inside-azuread-b2c-with-kim-cameron.aspx)。

## <a name="create-a-sign-up-policy"></a>建立註冊原則

若要啟用註冊您的應用程式，您必須建立註冊原則。 此原則說明將使用者皆經過期間註冊體驗及應用程式將會收到成功的登記的權杖內容。

1. [請遵循下列步驟，瀏覽至 Azure 入口網站上 B2C 功能刀](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)。
2. 按一下 [**註冊原則**]。
3. 按一下 [ **+ 新增**刀頂端]。
4. **名稱**會決定您的應用程式所使用的註冊原則名稱。 例如，輸入 「 SiUp 」。
5. 按一下 [**身分識別提供者**，然後選取 [」 電子郵件註冊]。 或者，您也可以選取社交身分識別提供者，如果已設定。 按一下**[確定]**。
6. 按一下 [**註冊屬性**]。 您在這裡選擇您想要從消費者收集註冊期間的屬性。 例如，選取 [國家/地區 」、 「 顯示名稱 」 和 「 郵遞區號 」。 按一下**[確定]**。
7. 按一下 [**應用程式宣告**]。 您在這裡選擇您想要傳回的權杖傳送回註冊體驗︰ 成功之後，應用程式中的宣告。 例如，選取 [顯示名稱 」、 「 身分識別提供者 」、 「 郵遞區號 」，「 使用者有新的 」 和 「 使用者的物件識別碼 」。
8. 按一下 [**建立**]。 請注意，只要建立的原則會顯示為 「**B2C_1_SiUp**」 ( **B2C\_1\_**就會自動新增片段) 中**註冊原則**刀。
9. 按一下 「**B2C_1_SiUp**」 開啟原則。
10. 選取 「 Contoso B2C 應用程式 」**應用程式**] 下拉式清單中的，`https://localhost:44321/`中**回覆 URL / 重新導向 URI**下拉式清單。
11. 按一下 [**立即執行**。 隨後便會開啟新的瀏覽器索引標籤，並透過的註冊您的應用程式的消費者體驗，您可以執行。

    > [AZURE.NOTE]
    它會使用建立原則和更新為分鐘才會生效。

## <a name="create-a-sign-in-policy"></a>建立登入原則

若要啟用登入您的應用程式，您必須建立登入原則。 此原則說明使用者皆會在登入及內容的應用程式將會收到權杖順利登集來穿過的體驗。

1. [請遵循下列步驟，瀏覽至 Azure 入口網站上 B2C 功能刀](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)。
2. 按一下 [**登入原則**]。
3. 按一下 [ **+ 新增**刀頂端]。
4. **名稱**會決定您的應用程式所使用的登入名稱。 例如，輸入 「 SiIn 」。
5. 按一下 [**身分識別提供者**，然後選取 「 本機帳戶登入]。 或者，您也可以選取社交身分識別提供者，如果已設定。 按一下**[確定]**。
6. 按一下 [**應用程式宣告**]。 您在這裡選擇您想要傳回的權杖傳送回成功的登入體驗之後，應用程式中的宣告。 例如，選取 [顯示名稱 」、 「 身分識別提供者 」、 「 郵遞區號 」 和 「 使用者的物件識別碼 」。 按一下**[確定]**。
7. 按一下 [**建立**]。 請注意，只要建立的原則會顯示為 「**B2C_1_SiIn**」 ( **B2C\_1\_**就會自動新增片段)**登入原則**刀中。
8. 按一下 「**B2C_1_SiIn**」 開啟原則。
9. 選取 「 Contoso B2C 應用程式 」**應用程式**] 下拉式清單中的，`https://localhost:44321/`中**回覆 URL / 重新導向 URI**下拉式清單。
10. 按一下 [**立即執行**。 隨後便會開啟新的瀏覽器索引標籤，並透過消費者一次登入您的應用程式，您可以執行。

    > [AZURE.NOTE]
    它會使用建立原則和更新為分鐘才會生效。

## <a name="create-a-sign-up-or-sign-in-policy"></a>建立註冊或登入原則

此原則控點以單一設定兩個消費者註冊與登入體驗。 使用者皆會導致根據內容右路徑 （註冊或登入）。 同時也會說明應用程式將會收到成功的登記或登增益集時的權杖的內容。  註冊或登入原則的程式碼範例是[使用以下](active-directory-b2c-devquickstarts-web-dotnet-susi.md)。

1. [請遵循下列步驟，瀏覽至 Azure 入口網站上 B2C 功能刀](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)。
2. 按一下 [**註冊或登入原則**]。
3. 按一下 [ **+ 新增**刀頂端]。
4. **名稱**會決定您的應用程式所使用的註冊原則名稱。 例如，輸入 「 SiUpIn 」。
5. 按一下 [**身分識別提供者**，然後選取 [」 電子郵件註冊]。 或者，您也可以選取社交身分識別提供者，如果已設定。 按一下**[確定]**。
6. 按一下 [**註冊屬性**]。 您在這裡選擇您想要從消費者收集註冊期間的屬性。 例如，選取 [國家/地區 」、 「 顯示名稱 」 和 「 郵遞區號 」。 按一下**[確定]**。
7. 按一下 [**應用程式宣告**]。 您在這裡選擇您想要傳回的權杖傳送回成功註冊或登入體驗之後，應用程式中的宣告。 例如，選取 [顯示名稱 」、 「 身分識別提供者 」、 「 郵遞區號 」，「 使用者有新的 」 和 「 使用者的物件識別碼 」。
8. 按一下 [**建立**]。 請注意，只要建立的原則會顯示為 「**B2C_1_SiUpIn**」 ( **B2C\_1\_**就會自動新增片段) 中**註冊或登入原則**刀。
9. 按一下 「**B2C_1_SiUpIn**」 開啟原則。
10. 選取 「 Contoso B2C 應用程式 」**應用程式**] 下拉式清單中的，`https://localhost:44321/`中**回覆 URL / 重新導向 URI**下拉式清單。
11. 按一下 [**立即執行**。 隨後便會開啟新的瀏覽器索引標籤，，您可以執行設定透過註冊或登入的消費者經驗。

    > [AZURE.NOTE]
    它會使用建立原則和更新為分鐘才會生效。

## <a name="create-a-profile-editing-policy"></a>建立編輯原則設定檔

若要啟用編輯您的應用程式的設定檔，您必須建立編輯原則設定檔。 此原則說明將使用者皆經過編輯設定檔及應用程式將會收到成功地完成的權杖內容期間體驗。

1. [請遵循下列步驟，瀏覽至 Azure 入口網站上 B2C 功能刀](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)。
2. 按一下 [**設定檔編輯原則**]。
3. 按一下 [ **+ 新增**刀頂端]。
4. **名稱**會決定編輯原則名稱應用程式所使用的設定檔。 例如，輸入 「 SiPe 」。
5. 按一下 [**身分識別提供者**，選取 [電子郵件地址 」。 或者，您也可以選取社交身分識別提供者，如果已設定。 按一下**[確定]**。
6. 按一下 [**設定檔屬性**]。 您在這裡選擇消費者可以檢視及編輯的屬性。 例如，選取 [國家/地區 」、 「 顯示名稱 」 和 「 郵遞區號 」。 按一下**[確定]**。
7. 按一下 [**應用程式宣告**]。 您在這裡選擇您想要傳回的權杖傳送回編輯經驗成功設定檔之後，應用程式中的宣告。 例如，選取 [顯示名稱 」 和 「 郵遞區號 」。
8. 按一下 [**建立**]。 請注意，只要建立的原則會顯示為 「**B2C_1_SiPe**」 ( **B2C\_1\_**片段就會自動新增) 中**的設定檔編輯原則**刀。
9. 按一下 「**B2C_1_SiPe**」 開啟原則。
10. 選取 「 Contoso B2C 應用程式 」**應用程式**] 下拉式清單中的，`https://localhost:44321/`中**回覆 URL / 重新導向 URI**下拉式清單。
11. 按一下 [**立即執行**。 隨即會開啟新的瀏覽器索引標籤，然後您可以透過編輯您的應用程式中的消費者體驗的設定檔中執行。

    > [AZURE.NOTE]
    它會使用建立原則和更新為分鐘才會生效。
    
## <a name="create-a-password-reset-policy"></a>建立密碼重設原則

若要啟用更細緻的密碼重設您的應用程式，您必須建立密碼重設原則。 附註的整個租用戶的密碼重設所指定的選項[以下](active-directory-b2c-reference-sspr.md)仍適用於登入原則。 此原則說明將使用者皆經過期間重設密碼及應用程式將會收到成功地完成的權杖內容體驗。

1. [請遵循下列步驟，瀏覽至 Azure 入口網站上 B2C 功能刀](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)。
2. 按一下 [**重設密碼原則**]。
3. 按一下 [ **+ 新增**刀頂端]。
4. **名稱**會決定密碼重設您的應用程式所使用的原則名稱。 例如，輸入 「 SSPR 」。
5. 按一下 [**身分識別提供者**，然後選取 [重設密碼使用電子郵件地址]。 按一下**[確定]**。
6. 按一下 [**應用程式宣告**]。 您在這裡選擇您想要傳送至您的應用程式後成功密碼重設體驗的權杖中傳回宣告。 例如，選取 「 使用者的物件識別碼 」。
7. 按一下 [**建立**]。 請注意，只要建立的原則會顯示為 「**B2C_1_SSPR**」 ( **B2C\_1\_**就會自動新增片段) 中**重設密碼原則**刀。
8. 按一下 「**B2C_1_SSPR**」 開啟原則。
9. 選取 「 Contoso B2C 應用程式 」**應用程式**] 下拉式清單中的，`https://localhost:44321/`中**回覆 URL / 重新導向 URI**下拉式清單。
10. 按一下 [**立即執行**。 隨即會開啟新的瀏覽器索引標籤，然後您可以透過密碼重設消費者體驗應用程式中執行。

    > [AZURE.NOTE]
    它會使用建立原則和更新為分鐘才會生效。

## <a name="additional-resources"></a>其他資源

- [權杖、 工作階段和單一登入設定](active-directory-b2c-token-session-sso.md)。
