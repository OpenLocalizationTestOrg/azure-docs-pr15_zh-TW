<properties
    pageTitle="教學課程︰ Azure Active Directory 整合 QuickHelp |Microsoft Azure"
    description="瞭解如何設定單一登入 Azure Active Directory 和 QuickHelp 之間。"
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


# <a name="tutorial-azure-active-directory-integration-with-quickhelp"></a>教學課程︰ 使用 QuickHelp 的 Azure Active Directory 整合

本教學課程中的目標是以顯示您如何將整合 QuickHelp 與 Azure Active Directory (Azure AD)。  
Azure AD 與整合 QuickHelp 為您提供下列優點︰ 

- 您可以控制可存取 QuickHelp Azure AD 
- 您可以讓使用者能自動取得登入 QuickHelp （單一登入） 至其 Azure AD 帳戶
- 您可以管理您的帳戶，在一個中央位置-Azure 傳統入口網站

如果您想要知道 Azure AD SaaS 應用程式整合相關的更多詳細資料，請參閱[什麼是應用程式存取和單一登入與 Azure Active Directory](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件 

若要設定 QuickHelp Azure AD 整合，您需要下列項目︰

- Azure AD 訂閱
- 啟用訂閱 QuickHelp 單一正負號


> [AZURE.NOTE] 若要測試此教學課程中的步驟，我們不建議使用生產環境。


若要測試此教學課程中的步驟，您應該先按照這些建議︰

- 您應該使用生產環境，除非這是必要的。
- 如果您沒有安裝 Azure AD 試用環境，您可以在一個月試用[這裡](https://azure.microsoft.com/pricing/free-trial/)取得。 

 
## <a name="scenario-description"></a>案例說明
本教學課程中的目的是讓您測試環境中測試 Azure AD 單一登入。  
本教學課程中所述的案例是由兩個主要的建置組塊所組成︰

1. 從圖庫新增 QuickHelp 
2. 設定及測試 Azure AD 單一登入


## <a name="adding-quickhelp-from-the-gallery"></a>從圖庫新增 QuickHelp
若要設定 QuickHelp 整合 Azure AD，必須將 QuickHelp 從圖庫新增至您的受管理的 SaaS 應用程式清單。

**若要從圖庫新增 QuickHelp，請執行下列步驟︰**

1. 在**Azure 傳統入口網站**，在左側的功能窗格中，按一下 [ **Active Directory**]。 

    ![Active Directory][1]

2. 從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3. 若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [在上方的功能表中的 [**應用程式**]。

    ![應用程式][2]

4. 按一下 [**新增**頁面的底部。

    ![應用程式][3]

5. 在 [**您想要做什麼**] 對話方塊中，按一下 [**新增應用程式，從圖庫**。

    ![應用程式][4]

6. 在 [搜尋] 方塊中，輸入**QuickHelp**。

    ![應用程式][5]

7. 在 [結果] 窗格中，選取**QuickHelp**，，然後按一下要新增應用程式**完成**。

    ![應用程式][500]


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>設定及測試 Azure AD 單一登入
本節的目標是以顯示您如何設定和使用 QuickHelp 根據稱為 「 許承恩 」 的測試使用者測試 Azure AD 單一登入。


若要設定及測試 Azure AD 單一登入與 QuickHelp，必須完成下列建置組塊︰

1. **[Azure AD 設定單一登入](#configuring-azure-ad-single-single-sign-on)**-若要讓使用者能使用這項功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)**-Azure AD 單一登入與許承恩測試。
4. **[建立 QuickHelp 測試使用者](#creating-a-quickhelp-test-user)**-連結至 Azure AD 的表示她的 QuickHelp 中有許承恩的對應。
5. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)**-啟用許承恩使用 Azure AD 單一登入。
5. **[測試單一登入](#testing-single-sign-on)**-驗證是否設定運作。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD 單一登入設定

本節的目標是 Azure AD 單一登入 Azure 傳統入口網站中啟用並 QuickHelp 應用程式中設定單一登入。


**若要使用 QuickHelp 設定 Azure AD 單一登入，請執行下列步驟︰**

1. 在 Azure 的傳統入口網站， **QuickHelp**應用程式整合在頁面上，按一下 [**設定單一登入**以開啟 [**設定單一登入**] 對話方塊。

    ![設定單一登入][6] 

2. 在**您要如何登入 QuickHelp 的使用者**] 頁面上，選取**Azure AD 單一登入**，然後按 [**下一步**。

    ![Azure AD 單一登入][7] 

3. **設定應用程式設定**] 對話方塊在頁面上，執行下列步驟︰

    ![設定應用程式設定][8] 
 
     。 在 [**登入 URL** ] 文字方塊中，輸入您登入您的 QuickHelp 網站的使用者所使用的 URL (例如︰* https://quickhelp.com/bsiazure/*)。

     > [AZURE.NOTE] 請如果您不知道值的 URL 登入，連絡您 QuickHelp 的支援小組。

     b。 按一下 [**下一步**]。

 
4. 在**設定單一登入 QuickHelp 在**頁面上，執行下列步驟︰ 按一下 [**下載中繼資料**，並儲存在本機電腦上的中繼資料檔案。

    ![什麼是 Azure AD Connect][9] 



1. 登入至您的 QuickHelp 公司網站管理員。

2. 在頂端的功能表，按一下 [**管理員**]。

    ![設定單一登入][21]


1. 在 [ **QuickHelp 管理員**] 功能表中，按一下 [**設定**]。

    ![設定單一登入][22]

1. 按一下 [**驗證設定**。

1. 在 [**驗證設定**] 頁面上執行下列步驟

    ![設定單一登入][23]

    。 為**SSO 類型**，選取 [ **WSFederation**]。

    b。 結束要上傳下載 Azure 中繼資料檔案，請按一下 [**瀏覽]**，瀏覽至檔案，然後按一下 [**上傳的中繼資料**。

    c。 在 [**電子郵件**] 文字方塊中，輸入**http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**。

    d。 在 [**名字**] 文字方塊中**輸入 http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**。

    e。 在 [**姓氏**] 文字方塊中**輸入 http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**

    f。 在**動作列**中，按一下 [**儲存**]。







6. 在 Azure 傳統的入口網站中，選取單一登入設定確認，然後再按 [**下一步**。 

    ![什麼是 Azure AD Connect][10]

7. 在**單一登入確認**] 頁面上，按一下 [**完成**]。  

    ![什麼是 Azure AD Connect][11]




### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
本節的目標是稱為許承恩 Azure 傳統入口網站中建立的測試使用者。  
在 [使用者] 清單中，選取**許承恩**。

![建立 Azure AD 使用者][20]

**若要建立的測試使用者 Azure AD 中，執行下列步驟︰**

1. 在**Azure 傳統入口網站**，在左側的功能窗格中，按一下 [ **Active Directory**]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_02.png) 

2. 從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3. 若要顯示的使用者清單頂端的功能表中，按一下 [**使用者**]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_03.png) 
 
4. 若要開啟 [**新增使用者**] 對話方塊底部的工具列中，按一下 [**新增使用者**]。 

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_04.png) 

5. 在 [**告訴我們瞭解此使用者**的對話方塊頁面，請執行下列步驟︰ 
 
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_05.png) 

    。 為使用者類型，選取您組織中的 [新的使用者]。

    b。 在 [使用者名稱]**文字方塊**中，輸入**BrittaSimon**。

    c。 按一下 [**下一步**]。

6.  **使用者設定檔**] 對話方塊在頁面上，執行下列步驟︰ 

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_06.png) 
 
    。 在 [**名字**] 文字方塊中輸入**許**。  

    b。 在 [**姓氏**] 文字方塊中輸入，**承恩**。

    c。 在 [**顯示名稱**] 文字方塊中，輸入**許承恩**。

    d。 在 [**角色**] 清單中選取 [**使用者**]。
    e。 按一下 [**下一步**]。

7. 在 [**取得暫時密碼**] 對話方塊頁面上，按一下 [**建立**]。

![建立 Azure AD 測試使用者](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_07.png) 
 
8. **取得暫時密碼**] 對話方塊在頁面上，執行下列步驟︰

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_08.png) 
  
    。 寫下的值的**新密碼**。

    b。 按一下 [**完成**]。   

  
 
### <a name="creating-a-quickhelp-test-user"></a>建立 QuickHelp 測試使用者

本節的目標是以建立 QuickHelp 中名為許承恩使用者。
單一登入搭配使用，Azure AD 需要知道什麼對應中的使用者 QuickHelp Azure AD 中的使用者。 也就是說，必須建立連結和之間的關聯 Azure AD 使用者 QuickHelp 中相關的使用者。

QuickHelp 支援佈建只的時間。 這表示，如果需要，使用者帳戶會自動建立在 QuickHelp，帳戶已連結至 Azure AD 帳戶。

沒有動作項目為您在此區段中。


### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

本節的目標是啟用許承恩 Azure 單一登入使用其存取權授與 QuickHelp。

![為使用者指派][200] 

**若要指定 QuickHelp 許承恩，請執行下列步驟︰**

1. Azure 傳統的入口網站中，若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [**應用程式**頂端的功能表中。

    ![為使用者指派][201] 

2. 在應用程式清單中，選取 [ **QuickHelp**]。

    ![為使用者指派][202] 

1. 在頂端的功能表，按一下 [**使用者**]。

    ![為使用者指派][203] 

1. 在 [使用者] 清單中，選取**許承恩**。

2. 在底部工具列中，按一下 [**指派**]。

    ![為使用者指派][205]



### <a name="testing-single-sign-on"></a>測試單一登入

本節的目標是測試 Azure AD 單一登入設定使用 Access 面板。  
當您按一下 [Access] 面板中的 [QuickHelp] 方塊時，您應該取得自動登入 QuickHelp 應用程式。


## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程的清單](active-directory-saas-tutorial-list.md)
* [什麼是應用程式存取和單一登入與 Azure Active Directory？](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_01.png
[500]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_14.png


[6]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_02.png
[8]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_03.png
[9]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_04.png
[10]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_100.png
[21]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_05.png
[22]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_06.png
[23]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_07.png
[24]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_08.png
[25]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_09.png
[26]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_10.png
[27]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_11.png
[28]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_12.png

[200]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_13.png
[203]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_205.png


[400]: ./media/active-directory-saas-QuickHelp-tutorial/tutorial_QuickHelp_400.png
[401]: ./media/active-directory-saas-QuickHelp-tutorial/tutorial_QuickHelp_401.png
[402]: ./media/active-directory-saas-QuickHelp-tutorial/tutorial_QuickHelp_402.png




