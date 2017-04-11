<properties
    pageTitle="教學課程︰ 使用 Amazon Web 服務 (AWS) 的 Azure Active Directory 整合 |Microsoft Azure"
    description="瞭解如何使用 Azure Active Directory 中的 Amazon Web 服務 (AWS)，來啟用單一登入、 自動化佈建和更多 ！"
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
    ms.date="09/01/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-amazon-web-service-aws"></a>教學課程︰ Azure Active Directory 整合與 Amazon Web 服務 (AWS)

本教學課程中的目標是以顯示您如何與 Azure Active Directory (Azure AD) 整合 Amazon Web 服務 (AWS)。  
Azure AD 與整合 Amazon Web 服務 (AWS) 為您提供下列優點︰ 

- 您可以控制可存取 Amazon Web 服務 (AWS) Azure AD 
- 您可以使用其 Azure AD 帳戶啟用使用者會自動取得登入至 Amazon Web 服務 (AWS) （單一登入）
- 您可以管理您的帳戶，在一個中央位置-Azure 傳統入口網站

如果您想要知道 Azure AD SaaS 應用程式整合相關的更多詳細資料，請參閱[什麼是應用程式存取和單一登入與 Azure Active Directory](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件 

若要設定 Azure AD 整合與 Amazon Web 服務 (AWS)，您需要下列項目︰

- Azure AD 訂閱
- 啟用訂閱 Amazon Web 服務 (AWS) 單一正負號


> [AZURE.NOTE] 若要測試此教學課程中的步驟，我們不建議使用生產環境。


若要測試此教學課程中的步驟，您應該先按照這些建議︰

- 您應該使用生產環境，除非這是必要的。
- 如果您沒有安裝 Azure AD 試用環境，您可以在一個月試用[這裡](https://azure.microsoft.com/pricing/free-trial/)取得。 

 
## <a name="scenario-description"></a>案例說明
本教學課程中的目的是讓您測試環境中測試 Azure AD 單一登入。  
本教學課程中所述的案例是由三個主要的建置組塊所組成︰

1. 從圖庫新增 Amazon Web 服務 (AWS) 
2. 設定及測試 Azure AD 單一登入


## <a name="adding-amazon-web-service-aws-from-the-gallery"></a>從圖庫新增 Amazon Web 服務 (AWS)
若要設定成 Azure AD 整合 Amazon Web 服務 (AWS)，您需要從庫的 Amazon Web 服務 (AWS) 新增至您的受管理的 SaaS 應用程式清單。

### <a name="to-add-amazon-web-service-aws-from-the-gallery-perform-the-following-steps"></a>若要從圖庫新增 Amazon Web 服務 (AWS)，請執行下列步驟︰

1. 在**Azure 傳統入口網站**，在左側的功能窗格中，按一下 [ **Active Directory**]。 

    ![Active Directory][1] 

2. 從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3. 若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [在上方的功能表中的 [**應用程式**]。 
   
    ![應用程式][2]

4. 按一下 [**新增**頁面的底部。 
   
    ![應用程式][3]

5. 在 [**您想要做什麼**] 對話方塊中，按一下 [**新增應用程式，從圖庫**。 
   
    ![應用程式][4]

6. 在 [搜尋] 方塊中，輸入**Amazon Web 服務 (AWS)**。
   
    ![應用程式][5]

7. 在 [結果] 窗格中，選取**Amazon Web 服務 (AWS)**，，然後按一下要新增應用程式**完成**。

    ![應用程式][6]



##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>設定及測試 Azure AD 單一登入
本節的目標是以顯示您如何設定和測試 Azure AD 單一登入根據稱為 「 許承恩 」 的測試使用者 Amazon Web 服務 (AWS)。

單一登入搭配使用，Azure AD 需要知道什麼是 Azure AD 中的使用者對應使用者 Amazon Web 服務 (AWS)。 也就是說，必須建立連結和之間的關聯 Azure AD 使用者相關的使用者在 Amazon Web 服務 (AWS)。  
所指派**的使用者名稱**的值為**使用者名稱**Amazon Web 服務 (AWS) 中的值 Azure AD 中建立此連結關聯。
 
若要設定並測試 Azure AD 單一登入與 Amazon Web 服務 (AWS)，您需要完成下列建置組塊︰

1. **[設定 Azure AD 單一單一登入](#configuring-azure-ad-single-single-sign-on)**-若要讓使用者能使用這項功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)**-Azure AD 單一登入與許承恩測試。
4. **[建立 Amazon Web 服務 (AWS) 測試使用者](#creating-a-halogen-software-test-user)**-將對應的許承恩中 Amazon Web 服務 (AWS) Azure AD 表示她的連結。
5. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)**-啟用許承恩使用 Azure AD 單一登入。
5. **[測試單一登入](#testing-single-sign-on)**-驗證是否設定運作。

### <a name="configuring-azure-ad-single-single-sign-on"></a>Azure AD 單一單一登入設定

本節的目標是 Azure AD 單一登入 Azure 傳統入口網站中啟用並 Amazon Web 服務 (AWS) 應用程式中設定單一登入。  
Amazon Web 服務 (AWS) 應用程式的期待 SAML 判斷提示以特定格式會要求您將自訂屬性對應至**saml 權杖屬性**設定。 以下螢幕擷取畫面會顯示這個範例。


![設定單一登入][27]

**若要設定 Azure AD 單一登入與 Amazon Web 服務 (AWS) 中，執行下列步驟︰**

1. 在 [Azure 傳統入口網站， **Amazon Web 服務 (AWS)**應用程式整合在頁面上，按一下 [**設定單一登入**以開啟 [**設定單一登入**] 對話方塊。

    ![設定單一登入][7]

2. 在**您要如何登入 Amazon Web 服務 (AWS) 的使用者**] 頁面上，選取**Azure AD 單一登入**，然後按 [**下一步**。

    ![設定單一登入][8]

3. **設定應用程式設定**] 對話方塊在頁面上，按一下 [下一步]。 

    ![設定應用程式設定][9]
 
4. 在**設定單一登入在 Amazon Web 服務 (AWS)**頁面上，按一下**下載的中繼資料**]，然後儲存在本機電腦上的中繼資料檔案。

    ![設定單一登入][10]

5. 在不同的瀏覽器視窗中，登入至您 Amazon Web 服務 (AWS) 的公司網站管理員。

6. 按一下 [**主控台首頁**。

    ![設定單一登入][11]

7. 按一下 [**身分識別與管理存取**]。 

    ![設定單一登入][12]

8. 按一下 [**身分識別提供者**，，然後按一下 [**建立提供者**。 

    ![設定單一登入][13]

9. **設定提供者**] 對話方塊在頁面上，執行下列步驟︰ 

    ![設定單一登入][14]

     。 為**提供者類型**，選取 [ **SAML**]。

     b。 在 [**提供者名稱**] 文字方塊中，輸入 [提供者名稱 (例如︰ *WAAD*)。

     c。 若要將檔案上傳已下載的中繼資料，請按一下 [**選擇 [檔案**]。

     d。 按一下 [**下一步**]。


10. 在 [**驗證提供者資訊**] 對話方塊] 頁面中，按一下 [**建立**]。 

    ![設定單一登入][15]

11. 按一下 [**角色**]，然後按一下 [**建立新的角色**。 

    ![設定單一登入][16]

12. 在 [**設定的角色名稱**] 對話方塊中，執行下列步驟︰ 

    ![設定單一登入][17]

    。 在 [**角色名稱**] 文字方塊中，輸入角色名稱 (例如︰ *TestUser*)。

    b。 按一下 [**下一步**]。

13. 在 [**選取角色類型**] 對話方塊中，執行下列步驟︰ 

    ![設定單一登入][18]

    。 選取 [**身分識別提供者存取角色**]。

    b。 在 [**授與 Web 單一登入 (WebSSO) 存取 SAML 提供者**] 區段中，按一下 [**選取**]。


14. 在 [**建立信任**] 對話方塊中，執行下列步驟︰  

    ![設定單一登入][19]
     
     。 為 SAML 供應商]，選取您建立 previousley SAML 提供者 (例如︰ *WAAD*) 

     b。 按一下 [**下一步**]。


15. 在 [**確認角色信任**] 對話方塊中，按一下 [**下一步**]。 

    ![設定單一登入][32]


16. 在 [**附加原則**] 對話方塊中，按一下 [**下一步**]。  

    ![設定單一登入][33]


17. 在 [**校閱**] 對話方塊中，執行下列步驟︰   

    ![設定單一登入][34]

     。 複製 [**角色 ARN**值。

     b。 複製**受信任的實體**ARN 值。

     c。 按一下 [**建立角色**。 

18. 在 Azure 傳統的入口網站中，選取單一登入設定確認，然後再按 [**下一步**。

    ![什麼是 Azure AD Connect][20]

19. **單一登入確認**頁面上，按一下 [**完成**，關閉 [**設定單一登入**] 對話方塊。

    ![什麼是 Azure AD Connect][22]


20. 在頂端的功能表，按一下 [**屬性**開啟 [ **SAML 權杖屬性**] 對話方塊。 

    ![設定單一登入][21]

21. 按一下 [**新增使用者屬性**]。 

    ![設定單一登入][23]

22. 在 [新增使用者屬性] 對話方塊中，執行下列步驟。 

    ![設定單一登入][24] 

    。 在 [**屬性名稱**] 文字方塊中，輸入**https://aws.amazon.com/SAML/Attributes/Role**。

    b。 在 [**屬性值**] 文字方塊中，輸入**[角色 ARN 值]，[信任的實體 ARN 值]**。

    >[AZURE.TIP] 以下是當您建立您的角色時，您是複製 [校閱] 對話方塊中的值。 

    c。 按一下 [**完成**，關閉 [**新增使用者屬性**] 對話方塊。

23. 按一下 [**新增使用者屬性**]。 

    ![設定單一登入][23]


24. 在 [新增使用者屬性] 對話方塊中，執行下列步驟。 

    ![設定單一登入][25]


     。 在 [**屬性名稱**] 文字方塊中，輸入**https://aws.amazon.com/SAML/Attributes/RoleSessionName**。

     b。 在 [**屬性值**] 文字方塊中，輸入或從下拉式清單中選取**user.userprincipalname** 。
     
    ![設定單一登入][35]
    

     c。 按一下 [**完成**，關閉 [**新增使用者屬性**] 對話方塊。


25. 按一下 [**套用變更**]。 

    ![設定單一登入][26]





### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

本節的目標是稱為許承恩 Azure 傳統入口網站中建立的測試使用者。

![建立 Azure AD 測試使用者](./media/active-directory-saas-amazon-web-service/create_aaduser_01.png)

**若要建立的測試使用者 Azure AD 中，執行下列步驟︰**

1. 在**Azure 傳統入口網站**，在左側的功能窗格中，按一下 [ **Active Directory**]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-amazon-web-service/create_aaduser_02.png) 

2. 從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3. 若要顯示的使用者清單頂端的功能表中，按一下 [**使用者**]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-amazon-web-service/create_aaduser_03.png) 
 
4. 若要開啟 [**新增使用者**] 對話方塊底部的工具列中，按一下 [**新增使用者**]。 

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-amazon-web-service/create_aaduser_04.png) 

5. 在 [**告訴我們瞭解此使用者**的對話方塊頁面，請執行下列步驟︰ 

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-amazon-web-service/create_aaduser_05.png) 

  1. 為使用者類型，選取您組織中的 [新的使用者]。
  2. 在 [使用者名稱]**文字方塊**中，輸入**BrittaSimon**。
  3. 按一下 [下一步]。

6.  **使用者設定檔**] 對話方塊在頁面上，執行下列步驟︰ 

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-amazon-web-service/create_aaduser_06.png) 

    。 在 [**名字**] 文字方塊中輸入**許**。  

    b。 在 [**名字姓氏**txtbox，類型，**承恩**。
  
    c。 在 [**顯示名稱**] 文字方塊中，輸入**許承恩**。

    d。 在 [**角色**] 清單中選取 [**使用者**]。
  
    e。 按一下 [**下一步**]。

7. 在 [**取得暫時密碼**] 對話方塊頁面上，按一下 [**建立**]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-amazon-web-service/create_aaduser_07.png) 
 
8. **取得暫時密碼**] 對話方塊在頁面上，執行下列步驟︰

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-amazon-web-service/create_aaduser_08.png) 

    。 寫下的值的**新密碼**。
  
    b。 按一下 [**完成**]。   
  
 
### <a name="creating-a-amazon-web-service-aws-test-user"></a>建立 Amazon Web 服務 (AWS) 測試使用者

本節的目標是建立稱為許承恩 Amazon Web 服務 (AWS) 中的使用者。

### <a name="to-create-a-user-called-britta-simon-in-amazon-web-service-aws-perform-the-following-steps"></a>若要建立稱為許承恩 Amazon Web 服務 (AWS) 中的使用者，請執行下列步驟︰

1. 以系統管理員身分登入您**Amazon Web 服務 (AWS)**的公司網站。

2. 按一下 [**主控台常用**圖示。 

    ![設定單一登入][11]

3. 按一下 [身分識別和存取管理。 

    ![設定單一登入][28]

4. 在儀表板，按一下 [使用者]，然後再按一下 [建立新的使用者。 

    ![設定單一登入][29]

5. 在 [建立使用者] 對話方塊中，執行下列步驟︰ 

    ![設定單一登入][30]

     。 **輸入使用者名稱**文字方塊中輸入 Brita 承恩的使用者名稱 (userprincipalname) 中 Azure AD。

     b。 按一下 [**建立**]。




### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

本節的目標是啟用許承恩 Azure 單一登入使用其存取授予 Amazon Web 服務 (AWS)。

![為使用者指派][31]

**若要指定 CloudPassage 許承恩，請執行下列步驟︰**

1. Azure 傳統的入口網站中，若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [**應用程式**頂端的功能表中。

    ![為使用者指派][26]

2. 在應用程式清單中，選取 [ **Amazon Web 服務 (AWS)**]。

    ![為使用者指派][27]

1. 在頂端的功能表，按一下 [**使用者**]。

    ![為使用者指派][25]

1. 在 [使用者] 清單中，選取**許承恩**。

2. 在底部工具列中，按一下 [**指派**]。

    ![為使用者指派][29]

### <a name="testing-single-sign-on"></a>測試單一登入

本節的目標是測試 Azure AD 單一登入設定使用 Access 面板。  
當您按一下 [Access] 面板中的 [Amazon Web 服務 (AWS)] 方塊時，您應該取得自動登入 Amazon Web 服務 (AWS) 應用程式。


## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程的清單](active-directory-saas-tutorial-list.md)
* [什麼是應用程式存取和單一登入與 Azure Active Directory？](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-amazon-web-service/tutorial_general_01.png
[2]: ./media/active-directory-saas-amazon-web-service/tutorial_general_02.png
[3]: ./media/active-directory-saas-amazon-web-service/tutorial_general_03.png
[4]: ./media/active-directory-saas-amazon-web-service/tutorial_general_04.png
[5]: ./media/active-directory-saas-amazon-web-service/ic795019.png
[6]: ./media/active-directory-saas-amazon-web-service/ic795020.png
[7]: ./media/active-directory-saas-amazon-web-service/ic795027.png
[8]: ./media/active-directory-saas-amazon-web-service/ic795028.png
[9]: ./media/active-directory-saas-amazon-web-service/capture23.png
[10]: ./media/active-directory-saas-amazon-web-service/capture24.png
[11]: ./media/active-directory-saas-amazon-web-service/ic795031.png
[12]: ./media/active-directory-saas-amazon-web-service/ic795032.png
[13]: ./media/active-directory-saas-amazon-web-service/ic795033.png
[14]: ./media/active-directory-saas-amazon-web-service/ic795034.png
[15]: ./media/active-directory-saas-amazon-web-service/ic795035.png
[16]: ./media/active-directory-saas-amazon-web-service/ic795022.png
[17]: ./media/active-directory-saas-amazon-web-service/ic795023.png
[18]: ./media/active-directory-saas-amazon-web-service/ic795024.png
[19]: ./media/active-directory-saas-amazon-web-service/ic795025.png
[20]: ./media/active-directory-saas-amazon-web-service/ic7950351.png
[21]: ./media/active-directory-saas-amazon-web-service/tutorial_general_80.png
[22]: ./media/active-directory-saas-amazon-web-service/ic7950352.png
[23]: ./media/active-directory-saas-amazon-web-service/tutorial_general_81.png
[24]: ./media/active-directory-saas-amazon-web-service/ic7950353.png
[25]: ./media/active-directory-saas-amazon-web-service/tutorial_general_15.png
[26]: ./media/active-directory-saas-amazon-web-service/tutorial_general_18.png
[27]: ./media/active-directory-saas-amazon-web-service/ic7950357.png
[28]: ./media/active-directory-saas-amazon-web-service/ic7950321.png
[29]: ./media/active-directory-saas-amazon-web-service/tutorial_general_16.png
[30]: ./media/active-directory-saas-amazon-web-service/ic795038.png
[31]: ./media/active-directory-saas-amazon-web-service/tutorial_general_17.png
[32]: ./media/active-directory-saas-amazon-web-service/ic7950251.png
[33]: ./media/active-directory-saas-amazon-web-service/ic7950252.png
[34]: ./media/active-directory-saas-amazon-web-service/ic7950253.png
[35]: ./media/active-directory-saas-amazon-web-service/user_attributes_01.png






















