<properties
    pageTitle="教學課程︰ 使用 Halogen 軟體的 Azure Active Directory 整合"
    description="瞭解如何設定單一登入 Azure Active Directory 和 Halogen 軟體之間。"
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
    ms.date="10/10/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-halogen-software"></a>教學課程︰ 使用 Halogen 軟體的 Azure Active Directory 整合

本教學課程中的目標是以顯示您如何與 Azure Active Directory (Azure AD) 整合 Halogen 軟體。

Azure AD 與整合 Halogen 軟體為您提供下列優點︰ 

- 您可以控制可存取 Halogen 軟體 Azure AD 
- 您可以使用其 Azure AD 帳戶啟用使用者會自動取得登入 Halogen 軟體 （單一登入）
- 您可以管理您的帳戶，在一個中央位置-Azure 傳統入口網站

如果您想要知道 Azure AD SaaS 應用程式整合相關的更多詳細資料，請參閱[什麼是應用程式存取和單一登入與 Azure Active Directory](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件 

若要設定 Azure AD 整合 Halogen 軟體，您需要下列項目︰

- Azure AD 訂閱
- 啟用訂閱 Halogen 軟體單一正負號


> [AZURE.NOTE] 若要測試此教學課程中的步驟，我們不建議使用生產環境。


若要測試此教學課程中的步驟，您應該先按照這些建議︰

- 您應該使用生產環境，除非這是必要的。
- 如果您沒有安裝 Azure AD 試用環境，您可以在一個月試用[這裡](https://azure.microsoft.com/pricing/free-trial/)取得。 

 
## <a name="scenario-description"></a>案例說明
本教學課程中的目的是讓您測試環境中測試 Azure AD 單一登入。 

本教學課程中所述的案例是由兩個主要的建置組塊所組成︰

1. 從圖庫新增 Halogen 軟體 
2. 設定及測試 Azure AD 單一登入


## <a name="adding-halogen-software-from-the-gallery"></a>從圖庫新增 Halogen 軟體
若要設定 Halogen 軟體整合 Azure AD，您需要從庫新增 Halogen 軟體到受管理的 SaaS 應用程式的清單。

**若要從圖庫新增 Halogen 軟體，請執行下列步驟︰**

1. 在**Azure 傳統入口網站**，在左側的功能窗格中，按一下 [ **Active Directory**]。 

    ![Active Directory][1]

2. 從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3. 若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [在上方的功能表中的 [**應用程式**]。

    ![應用程式][2]

4. 按一下 [**新增**頁面的底部。 

    ![應用程式][3]

5. 在 [**您想要做什麼**] 對話方塊中，按一下 [**新增應用程式，從圖庫**。

    ![應用程式][4]

6. 在 [搜尋] 方塊中，輸入**halogen 軟體**。

    ![應用程式][5]

7. 在 [結果] 窗格中，選取**Halogen 軟體**，然後按一下**完成**新增應用程式。



##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>設定及測試 Azure AD 單一登入
本節的目標是以顯示您如何設定和測試 Azure AD 單一登入，以根據稱為 「 許承恩 」 的測試使用者 Halogen 軟體。

單一登入搭配使用，Azure AD 需要知道什麼對應中的使用者 Halogen 軟體 Azure AD 中的使用者。 也就是說，必須建立連結和之間的關聯 Azure AD 使用者 Halogen 軟體中相關的使用者。

所指派**的使用者名稱**的值為 Halogen 軟體**的使用者名稱**的值 Azure AD 中建立此連結關聯。
 
若要設定及測試 Azure AD 單一登入以 Halogen 軟體，必須完成下列建置組塊︰

1. **[設定 Azure AD 單一單一登入](#configuring-azure-ad-single-single-sign-on)**-若要讓使用者能使用這項功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)**-Azure AD 單一登入與許承恩測試。
4. **[建立 Halogen 軟體測試使用者](#creating-a-halogen-software-test-user)**-有許承恩的對應中的已連結至她的 Azure AD 表示 Halogen 軟體。
5. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)**-啟用許承恩使用 Azure AD 單一登入。
5. **[測試單一登入](#testing-single-sign-on)**-驗證是否設定運作。

### <a name="configuring-azure-ad-single-single-sign-on"></a>Azure AD 單一單一登入設定

本節的目標是 Azure AD 單一登入 Azure 傳統入口網站中啟用並 Halogen 軟體應用程式中設定單一登入。


**若要以 Halogen 軟體設定 Azure AD 單一登入，請執行下列步驟︰**

1. 在 Azure 的傳統入口網站， **Halogen 軟體**應用程式整合在頁面上，按一下 [**設定單一登入**以開啟 [**設定單一登入**] 對話方塊。

    ![設定單一登入][8]

2. 在**您要如何登入 Halogen 軟體的使用者**] 頁面上，選取**Azure AD 單一登入**，然後按 [**下一步**。

    ![Azure AD 單一登入][9]

3. **設定應用程式設定**] 對話方塊在頁面上，執行下列步驟︰ ![設定應用程式設定][10]
 
     。 在 [**登入 URL** ] 文字方塊中，輸入您用來使用下列模式 Halogen 軟體應用程式登入您的使用者的 URL: *https://global.hgncloud.com/fabrikam/welcome.jsp*

     b。 按一下 [**下一步**]。
 
4. 在**設定單一登入 Halogen 軟體在**頁面上，按一下**下載中繼資料**]，然後儲存在本機電腦上的中繼資料檔案。
    
    ![什麼是 Azure AD Connect][11]

5. 在不同的瀏覽器視窗中，登入以系統管理員身分**Halogen 軟體**應用程式。

6. 按一下 [**選項**] 索引標籤。 

    ![什麼是 Azure AD Connect][12]


7. 在左側的功能窗格中，按一下 [ **SAML 設定**]。 

    ![什麼是 Azure AD Connect][13]

8. 在 [ **SAML 設定**] 頁面上執行下列步驟︰ ![什麼是 Azure AD Connect][14]

    。 **唯一識別碼**] 選取**NameID**。

    b。 **唯一識別碼對應至**]，選取 [**使用者名稱**。

    c。 若要將檔案上傳已下載的中繼資料，按一下 [**瀏覽]**以選取檔案，然後**上傳的檔案**。

    d。 若要測試設定，請按一下 [**執行測試**]。 

    > [AZURE.NOTE] 您必須等候訊息 」*SAML 測試已完成。請關閉此視窗*」。 然後關閉開啟的瀏覽器視窗。 如果測試完成後，才會啟用**啟用 SAML** ] 核取方塊。

    e。 選取 [**啟用 SAML**。
    
    f。 按一下 [**儲存變更**]。 


9. 在 Azure 傳統的入口網站中，選取單一登入設定確認，然後再按一下**完成**關閉 [**設定單一登入**] 對話方塊。 

    ![什麼是 Azure AD Connect][15]

10. 在**單一登入確認**] 頁面上，按一下 [**完成**]。  

    ![什麼是 Azure AD Connect][16]




### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
本節的目標是稱為許承恩 Azure 傳統入口網站中建立的測試使用者。

**若要建立的測試使用者 Azure AD 中，執行下列步驟︰**

1. 在**Azure 傳統入口網站**，在左側的功能窗格中，按一下 [ **Active Directory**]。

    ![什麼是 Azure AD Connect][100] 

2. 從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3. 若要顯示的使用者清單頂端的功能表中，按一下 [**使用者**]。

    ![什麼是 Azure AD Connect][101] 

4. 若要開啟 [**新增使用者**] 對話方塊底部的工具列中，按一下 [**新增使用者**]。 

    ![什麼是 Azure AD Connect][102] 

5. 在 [**告訴我們瞭解此使用者**的對話方塊頁面，請執行下列步驟︰

    ![什麼是 Azure AD Connect][103] 
 
    。 **輸入的使用者**，選取**您組織中的新使用者**。

    b。 在 [使用者名稱]**文字方塊**中，輸入**BrittaSimon**。

    c。 按一下 [下一步]。

6.  **使用者設定檔**] 對話方塊在頁面上，執行下列步驟︰ 

    ![什麼是 Azure AD Connect][104] 

    。 在 [**名字**] 文字方塊中輸入**許**。  

    b。 在 [**名字姓氏**txtbox，類型，**承恩**。

    c。 在 [**顯示名稱**] 文字方塊中，輸入**許承恩**。

    d。 在 [**角色**] 清單中選取 [**使用者**]。

    e。 按一下 [**下一步**]。

7. 在 [**取得暫時密碼**] 對話方塊頁面上，按一下 [**建立**]。

    ![什麼是 Azure AD Connect][105]  

8. **取得暫時密碼**] 對話方塊在頁面上，執行下列步驟︰

    ![什麼是 Azure AD Connect][106]   

    。 寫下的值的**新密碼**。
    b。 按一下 [**完成**]。   
  
 
### <a name="creating-a-halogen-software-test-user"></a>建立 Halogen 軟體測試使用者

本節的目標是建立稱為許承恩 Halogen 軟體中的使用者。

**若要建立稱為許承恩 Halogen 軟體中的使用者，請執行下列步驟︰**

1. **Halogen 軟體**應用程式以系統管理員身分登入。

2. 按一下 [**使用者中心**] 索引標籤，然後按一下 [**建立使用者**。

    ![什麼是 Azure AD Connect][300]  

3. **新的使用者**] 對話方塊在頁面上，執行下列步驟︰

    ![什麼是 Azure AD Connect][301]

    。 在 [**名字**] 文字方塊中輸入**許**。 
  
    b。 在 [**姓氏**] 文字方塊中輸入**承恩**。
  
    c。 在 [**使用者名稱**] 文字方塊中，輸入**Brita 承恩 Azure 傳統入口網站中的使用者名稱**。
  
    d。 在 [**密碼**] 文字方塊中，輸入許密碼。
  
    e。 按一下 [**儲存**]。


### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

本節的目標是啟用許承恩 Azure 單一登入使用其存取權授與 Halogen 軟體。

![什麼是 Azure AD Connect][200]

**若要指定許承恩 Halogen 軟體，請執行下列步驟︰**

1. Azure 傳統的入口網站中，若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [**應用程式**頂端的功能表中。

    ![什麼是 Azure AD Connect][201]

2. 在應用程式清單中，選取 [ **Halogen 軟體**]。

    ![什麼是 Azure AD Connect][202]

1. 在頂端的功能表，按一下 [**使用者**]。

    ![什麼是 Azure AD Connect][203]

1. 在 [使用者] 清單中，選取**許承恩**。

    ![什麼是 Azure AD Connect][204]

2. 在底部工具列中，按一下 [**指派**]。

    ![什麼是 Azure AD Connect][205]



### <a name="testing-single-sign-on"></a>測試單一登入

本節的目標是測試 Azure AD 單一登入設定使用 Access 面板。

當您按一下 [Access] 面板中的 [Halogen 軟體] 方塊時，您應該取得自動登入 Halogen 軟體應用程式。


## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程的清單](active-directory-saas-tutorial-list.md)
* [什麼是應用程式存取和單一登入與 Azure Active Directory？](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_01.png
[2]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_02.png
[3]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_03.png
[4]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_04.png
[5]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_05.png
[6]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_06.png
[7]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_07.png
[8]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_08.png
[9]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_09.png
[10]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_10.png
[11]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_11.png
[12]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_12.png
[13]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_13.png
[14]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_14.png
[15]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_15.png
[16]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_16.png
[100]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_100.png 
[101]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_101.png 
[102]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_102.png 
[103]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_103.png 
[104]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_104.png 
[105]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_105.png 
[106]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_106.png 
[200]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_200.png 
[201]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_201.png 
[202]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_202.png
[203]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_203.png
[204]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_204.png
[205]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_205.png
[300]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_300.png
[301]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_301.png