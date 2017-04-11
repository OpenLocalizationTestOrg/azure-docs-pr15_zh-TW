<properties
    pageTitle="教學課程︰ 使用呈現檔案的 Azure Active Directory 整合 |Microsoft Azure"
    description="瞭解如何設定單一登入之間 Azure Active Directory 和呈現的檔案。"
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
    ms.date="09/29/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-flatter-files"></a>教學課程︰ 使用呈現檔案的 Azure Active Directory 整合

本教學課程中的目標是以顯示您如何與 Azure Active Directory (Azure AD) 整合呈現的檔案。  
Azure AD 與整合呈現的檔案可以提供下列優點︰ 

- 您可以控制可存取呈現檔案 Azure AD 
- 您可以使用其 Azure AD 帳戶啟用使用者會自動取得登入呈現檔案 （單一登入）
- 您可以管理您的帳戶，在一個中央位置的 Azure Active Directory 傳統入口網站

如果您想要知道 Azure AD SaaS 應用程式整合相關的更多詳細資料，請參閱[什麼是應用程式存取和單一登入與 Azure Active Directory](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件 

若要設定 Azure AD 整合呈現的檔案，您需要下列項目︰

- Azure AD 訂閱
- 呈現檔案單一登啟用的訂閱


> [AZURE.NOTE] 若要測試此教學課程中的步驟，我們不建議使用生產環境。


若要測試此教學課程中的步驟，您應該先按照這些建議︰

- 您應該使用生產環境，除非這是必要的。
- 如果您沒有安裝 Azure AD 試用環境，您可以在一個月試用[這裡](https://azure.microsoft.com/pricing/free-trial/)取得。 

 
## <a name="scenario-description"></a>案例說明
本教學課程中的目的是讓您測試環境中測試 Azure AD 單一登入。  
本教學課程中所述的案例是由兩個主要的建置組塊所組成︰

1. 從圖庫新增呈現的檔案 
2. 設定及測試 Azure AD 單一登入


## <a name="adding-flatter-files-from-the-gallery"></a>從圖庫新增呈現的檔案
若要設定成 Azure AD 整合呈現的檔案，必須將呈現的檔案從圖庫新增至您的受管理的 SaaS 應用程式清單。

**若要從圖庫新增呈現的檔案，請執行下列步驟︰**

1. 在**Azure 傳統入口網站**，在左側的功能窗格中，按一下 [ **Active Directory**]。 

    ![Active Directory][1]

2. 從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3. 若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [在上方的功能表中的 [**應用程式**]。

    ![應用程式][2]

4. 按一下 [**新增**頁面的底部。

    ![應用程式][3]

5. 在 [**您想要做什麼**] 對話方塊中，按一下 [**新增應用程式，從圖庫**。

    ![應用程式][4]

6. 在 [搜尋] 方塊中，輸入**呈現的檔案**。


    ![建立 Azure AD 測試使用者](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_01.png)

7. 在 [結果] 窗格中，選取**呈現的檔案**，，然後按一下 [新增應用程式**完成**。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_500.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>設定及測試 Azure AD 單一登入
本節的目標是以顯示您如何設定和測試 Azure AD 單一登入呈現根據稱為 「 許承恩 」 的測試使用者的檔案。

單一登入搭配使用，Azure AD 需要知道什麼是 Azure AD 中的使用者對應使用者呈現的檔案中。 也就是說，必須建立連結和之間的關聯 Azure AD 使用者呈現的檔案中相關的使用者。  
所指派的**使用者名稱**值在 Azure AD 為呈現檔案**的使用者名稱**的值建立此連結關聯。
 
若要設定和測試 Azure AD 單一登入呈現的檔案，必須完成下列建置組塊︰

1. **[Azure AD 設定單一登入](#configuring-azure-ad-single-single-sign-on)**-若要讓使用者能使用這項功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)**-Azure AD 單一登入與許承恩測試。
4. **[建立呈現的檔案測試使用者](#creating-a-halogen-software-test-user)**-呈現之檔案中的已連結至她的 Azure AD 表示有許承恩的對應。
5. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)**-啟用許承恩使用 Azure AD 單一登入。
5. **[測試單一登入](#testing-single-sign-on)**-驗證是否設定運作。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD 單一登入設定

本節的目標是 Azure AD 單一登入 Azure AD 傳統入口網站中啟用和設定單一登入應用程式呈現的檔案。 此程序的一部分，您所需建立 64 基本編碼的憑證檔案。 如果您不熟悉這個程序，請參閱[如何將轉換成文字檔的二進位憑證](http://youtu.be/PlgrzUZ-Y1o)。

若要設定單一登入呈現的檔案，必須已註冊的網域。 如果您沒有已註冊的網域尚未時，連絡人呈現檔案支援小組透過[support@flatterfiles.com](mailto:support@flatterfiles.com)。  



**若要呈現的檔案，以設定 Azure AD 單一登入，請執行下列步驟︰**

1. Azure AD 傳統入口網站中，在**呈現檔案**應用程式整合頁面上，按一下 [**設定單一登入**以開啟 [**設定單一登入**對話方塊]。

    ![設定單一登入][6] 

2. 在**您要如何登入呈現檔案的使用者**] 頁面上，選取**Azure AD 單一登入**，然後按 [**下一步**。
 
    ![設定單一登入](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_02.png) 

3. 在**設定應用程式設定**] 對話方塊頁面，按一下 [**下一步**]。

    ![設定單一登入](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_03.png) 

    > [AZURE.NOTE] 呈現檔案的所有客戶使用相同的 SSO 登入 URL: [https://www.flatterfiles.com/site/login/sso/](https://www.flatterfiles.com/site/login/sso/)。
.
 
 
4. **設定單一登入呈現的檔案，**在頁面上，執行下列步驟︰

    ![設定單一登入](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_04.png)  

    。 按一下 [**下載憑證**]，然後儲存您的電腦上的檔案。

    b。 按一下 [**下一步**]。


1. 登入以系統管理員身分呈現檔案應用程式。

2. 按一下 [儀表板]。 

    ![設定單一登入](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_05.png)  



2. 按一下 [**設定**]，然後在 [**公司**] 索引標籤上執行下列步驟︰ 

    ![設定單一登入](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_06.png)  

    。 選取 [**使用 SAML 2.0 進行驗證**。

    b。 按一下 [**設定 SAML**。



2. 在 [ **SAML 設定**] 對話方塊中，執行下列步驟︰ 

    ![設定單一登入](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_08.png)  

    。 在 [網域] 文字方塊中，輸入您已註冊的網域。

    > [AZURE.NOTE] 如果您沒有已註冊的網域尚未時，連絡人呈現檔案支援小組透過[support@flatterfiles.com](mailto:support@flatterfiles.com)。
    
    b。 在 Azure 傳統入口網站中的，在單一設定登入呈現的檔案] 對話方塊中，copt 在單一登入服務的 URL，然後再貼到 [身分識別提供者 URL] 文字方塊。

    c。  建立**64 基本編碼**檔案從您下載的憑證。  

    >[AZURE.TIP] 如需詳細資訊，請參閱[如何轉換成文字檔的二進位憑證](http://youtu.be/PlgrzUZ-Y1o)

    d。  在記事本中開啟您 64 基本編碼的憑證、 將其內容複製到剪貼簿，並貼到**FlatterFiles 身分識別提供者的憑證**文字方塊。

    e。 按一下 [**更新**]。

6. Azure AD 傳統入口網站中，在選取的單一登入設定確認，然後再按 [**下一步**。 

    ![Azure AD 單一登入][10]

7. 在**單一登入確認**] 頁面上，按一下 [**完成**]。  

    ![Azure AD 單一登入][11]




### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
本節的目標是稱為許承恩 Azure 傳統入口網站中建立的測試使用者。

![建立 Azure AD 使用者][20]

**若要建立的測試使用者 Azure AD 中，執行下列步驟︰**

1. 在**Azure 傳統入口網站**，在左側的功能窗格中，按一下 [ **Active Directory**]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_09.png) 

2. 從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3. 若要顯示的使用者清單頂端的功能表中，按一下 [**使用者**]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_03.png) 
 
4. 若要開啟 [**新增使用者**] 對話方塊底部的工具列中，按一下 [**新增使用者**]。 

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_04.png) 

5. 在 [**告訴我們瞭解此使用者**的對話方塊頁面，請執行下列步驟︰ 

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_05.png)  

    。 為使用者類型，選取您組織中的 [新的使用者]。

    b。 在 [使用者名稱]**文字方塊**中，輸入**BrittaSimon**。

    c。 按一下 [**下一步**]。

6.  **使用者設定檔**] 對話方塊在頁面上，執行下列步驟︰ 

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_06.png) 
 
    。 在 [**名字**] 文字方塊中輸入**許**。  

    b。 在 [**姓氏**] 文字方塊中輸入，**承恩**。

    c。 在 [**顯示名稱**] 文字方塊中，輸入**許承恩**。

    d。 在 [**角色**] 清單中選取 [**使用者**]。
    e。 按一下 [**下一步**]。

7. 在 [**取得暫時密碼**] 對話方塊頁面上，按一下 [**建立**]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_07.png) 
 
8. **取得暫時密碼**] 對話方塊在頁面上，執行下列步驟︰

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_08.png) 
  
    。 寫下的值的**新密碼**。

    b。 按一下 [**完成**]。   

  
 
### <a name="creating-a-flatter-files-test-user"></a>建立呈現檔案測試使用者

本節的目標是建立稱為許承恩呈現檔案中的使用者。

**若要建立稱為許承恩呈現檔案中的使用者，請執行下列步驟︰**

1. 以系統管理員身分登入您**呈現檔案**的公司網站。

2. 在左側功能窗格] 中按一下**設定**]，然後按一下 [使用者**] 索引標籤**。

    ![Cfreate 呈現檔案使用者](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_09.png)

3. 按一下 [**新增使用者**]。 

4. 在 [**新增使用者**] 對話方塊中，執行下列步驟︰

    ![Cfreate 呈現檔案使用者](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_10.png)

    。 在 [**名字**] 文字方塊中輸入**許**。

    b。 在 [**姓氏**] 文字方塊中輸入**承恩**。 

    c。 在 [**電子郵件地址**] 文字方塊中，輸入許的 Azure 傳統入口網站中的電子郵件地址。

    d。 按一下 [**送出**]。   


### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

本節的目標是啟用許承恩 Azure 單一登入使用其存取權授與呈現的檔案。

![為使用者指派][200] 

**若要指定許承恩呈現的檔案，請執行下列步驟︰**

1. Azure 傳統的入口網站中，若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [**應用程式**頂端的功能表中。

    ![為使用者指派][201] 

2. 在應用程式清單中，選取 [**呈現的檔案**。

    ![為使用者指派](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_11.png) 

1. 在頂端的功能表，按一下 [**使用者**]。

    ![為使用者指派][203] 

1. 在 [使用者] 清單中，選取**許承恩**。

2. 在底部工具列中，按一下 [**指派**]。

    ![為使用者指派][205]



### <a name="testing-single-sign-on"></a>測試單一登入

本節的目標是測試 Azure AD 單一登入設定使用 Access 面板。  
當您按一下 [Access] 面板中的 [呈現的檔案] 方塊時，您應該取得自動登入應用程式呈現的檔案。


## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程的清單](active-directory-saas-tutorial-list.md)
* [什麼是應用程式存取和單一登入與 Azure Active Directory？](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_205.png






