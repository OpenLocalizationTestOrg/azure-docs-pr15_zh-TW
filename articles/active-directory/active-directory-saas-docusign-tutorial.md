<properties
    pageTitle="教學課程︰ Azure Active Directory 整合 DocuSign |Microsoft Azure"
    description="瞭解如何設定單一登入 Azure Active Directory 和 DocuSign 之間。"
    services="active-directory"
    documentationCenter=""
    authors="jeevansd"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/16/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-docusign"></a>教學課程︰ 使用 DocuSign 的 Azure Active Directory 整合

本教學課程中的目標是以顯示 Azure 和 DocuSign 的整合。
本教學課程中所述的案例假設您已經有下列項目︰

- 有效的 Azure 訂閱
- 在 [DocuSign 租用戶



本教學課程中所述的案例是由下列建置組塊所組成︰

1. [啟用 DocuSign 應用程式整合](#enabling-the-application-integration-for-docusign) 


2. [設定單一登入](#configuring-single-sign-on) 


3. [設定帳戶佈建](#configuring-account-provisioning) 


4. [將使用者指派](#assigning-users) 

    ![設定單一登入][0]
 

## <a name="enabling-the-application-integration-for-docusign"></a>啟用 DocuSign 應用程式整合

本節的目標是大綱如何啟用 DocuSign 的整合應用程式。

### <a name="to-enable-the-application-integration-for-docusign-perform-the-following-steps"></a>若要啟用的 DocuSign 整合應用程式，請執行下列步驟︰

1. Azure 傳統入口網站中，在左側的功能窗格中，按一下 [ **Active Directory**]。

    ![設定單一登入][1]

2. 從 [目錄] 清單中，選取您要啟用目錄整合的目錄。

3. 若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [在上方的功能表中的 [**應用程式**]。

    ![設定單一登入][2]

4. 按一下 [**新增**頁面的底部。

    ![應用程式][3]

5. 在要方式] 對話方塊中，按一下 [**新增應用程式，從圖庫**。

    ![設定單一登入][4]


6. 在 [搜尋] 方塊中，輸入**DocuSign**。

    ![設定單一登入][5]

7. 在 [結果] 窗格中，選取**DocuSign**，，然後按一下要新增應用程式**完成**。

    ![設定單一登入][6]


## <a name="configuring-single-sign-on"></a>設定單一登入

本節的目標是大綱如何啟用使用者進行驗證其帳戶中使用根據 SAML 通訊協定的同盟 Azure AD DocuSign。


### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要設定單一登入，請執行下列步驟︰

1. 在 [Azure 傳統入口網站**整合 DocuSign 應用程式**在頁面上，按一下 [**設定單一登入**開啟 [設定單一登入] 對話方塊。

    ![設定單一登入][7]

2. 在**您要如何登入 DocuSign 的使用者**] 頁面上，選取**Microsoft Azure AD 單一登入**，然後按 [下一步]。

    ![設定單一登入][8]

3. 在 [**設定應用程式設定**] 頁面上執行下列步驟︰

    ![設定單一登入][61]

    。 在 [**登入 URL** ] 文字方塊中，輸入`https://account.docusign.com/*`。  

    b。 在 [**識別碼**] 文字方塊中，輸入`https://account.docusign.com/*`。  
   
    c。 按一下 [**下一步**]。 


    > [AZURE.TIP] 登入 URL] 與 [識別碼值是只版面配置區。 本主題稍後的覆蓋如何擷取您的環境的實際值的相關指示。
 

4. 在**設定單一登入 DocuSign 在**頁面上，按一下**下載憑證**]，然後儲存在本機電腦上的憑證檔案。

    ![設定單一登入][10]


5. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入您**DocuSign 管理入口網站**。


6. 在左側導覽功能表中，按一下 [**網域**]。

    ![設定單一登入][51]

7. 在右窗格中，按一下 [**宣告網域**。

    ![設定單一登入][52]

8. 在 [**網域宣告**] 對話方塊的 [**網域名稱**] 文字方塊中，輸入您的公司網域，然後再按一下**宣告**。 請確定您 [驗證網域，而且的狀態是 [作用中。

    ![設定單一登入][53]

9. 按一下左側功能表中的 [**身分識別提供者**  

    ![設定單一登入][54]

10. 在右窗格中，按一下 [**新增身分識別提供者**。 
    
    ![設定單一登入][55]

11. 在 [**身分識別提供者設定**] 頁面上執行下列步驟︰

    ![設定單一登入][56]


    。 在 [**名稱**] 文字方塊中，輸入您的設定的唯一名稱。 請不要使用空格。

    b。 在 Azure 傳統的入口網站中，複製發行者的 URL，然後再貼到 [**身分識別提供者的發行者**] 文字方塊。

    c。 在 Azure 傳統的入口網站中，複製**遠端登入 URL**，然後再貼到 [**身分識別提供者登入 URL** ] 文字方塊。

    d。 在 Azure 傳統的入口網站中，複製**遠端登出 URL**，然後再貼到 [**身分識別提供者登出 URL** ] 文字方塊。

    e。 選取**登驗證要求**。

    f。 為**傳送驗證要求**，選取 [**文章**]。

    g。 為**傳送登出要求**，請選取 [**文章**]。 


12. 在 [**自訂屬性對應**] 區段中，選擇您想要與 Azure AD 宣告對應的欄位。 在此範例中，會將**電子郵件地址**宣告對應**http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**的值。 這是預設的宣告名稱從 Azure AD 的電子郵件宣告。 

    > [AZURE.NOTE] 若要對應 Docusign 使用者對應從 Azure AD 使用者使用適當的**使用者識別碼**。 選取適當的欄位，然後輸入適當的值，根據您的組織設定]。

    ![設定單一登入][57]

13. 在 [**身分識別提供者的憑證**] 區段中，按一下 [**新增憑證**]，然後上傳您已從 Azure AD 傳統 portal 下載的憑證。   

    ![設定單一登入][58]

14. 按一下 [**儲存**]。

15. 在 [**身分識別提供者**] 區段中，按一下 [**動作**]，然後按一下**端點**。   

    ![設定單一登入][59]



10. Azure 傳統入口網站，回到 [**設定應用程式設定**] 頁面。 

16. 在**管理入口網站上 DocuSign**，在 [**檢視 SAML 2.0 結束點**] 區段中，下列步驟執行︰

    ![設定單一登入][60]

    。 複製**服務提供者發行者的 URL**，然後再貼到 Azure 傳統入口網站上的 [**識別碼**] 文字方塊。

    b。 **服務提供者登入 URL**中，複製並貼到 Azure 傳統入口網站上的 [**登入 URL** ] 文字方塊。

    c。  按一下 [**關閉**]  


10. 在 Azure 傳統的入口網站中，按一下 [**下一步**]。 


15. 在 Azure 傳統的入口網站中，選取**單一登入設定確認**，然後再按 [**下一步**。

    ![應用程式][14]

10. 在**單一登入確認**] 頁面上，按一下 [**完成**]。

    ![應用程式][15]
 

## <a name="configuring-account-provisioning"></a>設定帳戶佈建

本節的目標是大綱如何啟用使用者佈建 Active Directory 使用者帳戶，以便 DocuSign。

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>若要設定使用者佈建，執行下列步驟︰

1. 在**Azure 傳統入口網站**，在**DocuSign 整合應用程式**頁面上，按一下 [開啟 [設定使用者佈建] 對話方塊的 [**設定帳戶佈建**]。

    ![設定帳戶佈建][30]

2. 在 [**設定及管理認證**] 頁面上若要啟用自動佈建的使用者，有足夠的權限，提供 DocuSign 帳戶認證，然後按一下 [**下一步**。 

    ![設定帳戶佈建][31]

3. 在 [**測試連線**] 對話方塊中，按一下 [**開始測試**]，然後在成功測試]，按一下 [**下一步**。

    ![設定帳戶佈建][32]

3. 在 [**確認**] 頁面上，按一下 [**完成**]。

    ![設定帳戶佈建][33]
 

## <a name="assigning-users"></a>將使用者指派

若要測試您的設定，您需要授與 Azure AD 使用者想要允許使用您的應用程式存取分派給他們。

### <a name="to-assign-users-to-docusign-perform-the-following-steps"></a>若要指定 DocuSign 使用者，請執行下列步驟︰

1. 在**Azure 傳統入口網站**中，建立測試帳戶。

2. 在**整合 DocuSign 應用程式**] 頁面上，按一下 [**指派給使用者**。

    ![將使用者指派][40]
 

3. 選取您測試的使用者、**指派**，請按一下 [，然後按一下**[是]**以確認您的工作分派。

    ![將使用者指派][41]


您現在應該等待 10 分鐘的時間，並確認帳戶已同步處理至 DocuSign。

驗證，首先，您可以檢查佈建狀態]，即可在 Azure 傳統入口網站上的 [DocuSign 應用程式整合] 頁面上 D 中的儀表板。

![將使用者指派][42]

以相關的狀態，表示順利完成的使用者佈建循環圖︰

![將使用者指派][43]


如果您想要測試您的單一登入設定，開啟 [存取面板。

如需存取畫面的詳細資訊，請參閱簡介存取面板。


## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程的清單](active-directory-saas-tutorial-list.md)
* [什麼是應用程式存取和單一登入與 Azure Active Directory？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[0]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_00.png
[1]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_01.png
[6]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_02.png
[7]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_03.png
[8]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_04.png
[9]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_05.png
[10]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_06.png

[14]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_10.png
[15]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_11.png

[30]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_400.png
[31]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_12.png
[32]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_13.png
[33]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_14.png



[40]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_15.png
[41]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_16.png
[42]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_17.png
[43]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_18.png

[51]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_28.png
[61]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_29.png