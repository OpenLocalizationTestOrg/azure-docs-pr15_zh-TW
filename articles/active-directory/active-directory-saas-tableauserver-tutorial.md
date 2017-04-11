<properties
    pageTitle="教學課程︰ 與 Tableau 伺服器的 Azure Active Directory 整合 |Microsoft Azure"
    description="瞭解如何設定單一登入 Azure Active Directory 和 Tableau Server 之間。"
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


# <a name="tutorial-azure-active-directory-integration-with-tableau-server"></a>教學課程︰ 與 Tableau 伺服器的 Azure Active Directory 整合

本教學課程中的目標是以顯示您要與 Azure Active Directory (Azure AD) 整合 Tableau 伺服器的方式。

Azure AD 與整合 Tableau 伺服器可以提供下列優點︰

- 您可以控制可存取 Tableau Server Azure AD
- 您可以使用其 Azure AD 帳戶啟用使用者會自動取得登入 Tableau 伺服器 （單一登入）
- 您可以管理您的帳戶，在一個中央位置-Azure 傳統入口網站

如果您想要知道 Azure AD SaaS 應用程式整合相關的更多詳細資料，請參閱[什麼是應用程式存取和單一登入與 Azure Active Directory](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 整合與 Tableau 伺服器，您需要下列項目︰

- Azure AD 訂閱
- 啟用訂閱 Tableau 伺服器單一正負號


> [AZURE.NOTE] 若要測試此教學課程中的步驟，我們不建議使用生產環境。


若要測試此教學課程中的步驟，您應該先按照這些建議︰

- 您應該使用生產環境，除非這是必要的。
- 如果您沒有安裝 Azure AD 試用環境，您可以在一個月試用[這裡](https://azure.microsoft.com/pricing/free-trial/)取得。


## <a name="scenario-description"></a>案例說明
本教學課程中的目的是讓您測試環境中測試 Azure AD 單一登入。 

本教學課程中所述的案例是由兩個主要的建置組塊所組成︰

1. 從圖庫新增 Tableau 伺服器
2. 設定及測試 Azure AD 單一登入


## <a name="adding-tableau-server-from-the-gallery"></a>從圖庫新增 Tableau 伺服器
若要設定 Tableau Server 整合 Azure AD，您需要將 Tableau 伺服器從圖庫新增至您的受管理的 SaaS 應用程式清單。

**若要從圖庫新增 Tableau 伺服器，請執行下列步驟︰**

1. 在**Azure 傳統入口網站**，在左側的功能窗格中，按一下 [ **Active Directory**]。 
 
    ![Active Directory][1]

2. 從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3. 若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [在上方的功能表中的 [**應用程式**]。

    ![應用程式][2]

4. 按一下 [**新增**頁面的底部。

    ![應用程式][3]

5. 在 [**您想要做什麼**] 對話方塊中，按一下 [**新增應用程式，從圖庫**。

    ![應用程式][4]

6. 在 [搜尋] 方塊中，輸入**Tableau 伺服器**。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_01.png)

7. 在 [結果] 窗格中，選取**Tableau 伺服器**]，然後按一下要新增應用程式**完成**。

    ![在圖庫中選取應用程式](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>設定及測試 Azure AD 單一登入
本節的目標是以顯示您如何設定和測試 Azure AD 單一登入與 Tableau 伺服器根據測試使用者稱為 「 許承恩 」。

單一登入搭配使用，Azure AD 需要知道什麼對應使用者 Tableau server Azure AD 中的使用者。 也就是說，必須建立連結和之間的關聯 Azure AD 使用者 Tableau 伺服器中相關的使用者。

所指派**的使用者名稱**的值為 Tableau 伺服器**的使用者名稱**的值 Azure AD 中建立此連結關聯。

若要設定及測試 Azure AD 單一登入與 Tableau 伺服器，必須完成下列建置組塊︰

1. **[Azure AD 設定單一登入](#configuring-azure-ad-single-single-sign-on)**-若要讓使用者能使用這項功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)**-Azure AD 單一登入與許承恩測試。
4. **[建立 Tableau 伺服器測試使用者](#creating-a-tableauserver-test-user)**-將對應的許承恩 Tableau server Azure AD 表示她的連結。
5. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)**-啟用許承恩使用 Azure AD 單一登入。
5. **[測試單一登入](#testing-single-sign-on)**-驗證是否設定運作。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD 單一登入設定

本節的目標是 Azure AD 單一登入 Azure 傳統入口網站中啟用並 Tableau 伺服器應用程式中設定單一登入。

Tableau 伺服器應用程式以特定格式預期 SAML 判斷提示。 以下螢幕擷取畫面會顯示這個範例。 

![設定單一登入](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_51.png) 

**若要設定 Azure AD 單一登入與 Tableau 伺服器，請執行下列步驟︰**


1. Azure 傳統入口網站， **Tableau 伺服器**應用程式整合在頁面上，在上方的功能表中按一下 [**屬性**]。

    ![設定單一登入](./media/active-directory-saas-tableauserver-tutorial/tutorial_general_81.png) 


1. 在 [ **SAML 權杖屬性**] 對話方塊中，執行下列步驟︰

    

    。 按一下 [開啟 [**新增使用者 Attribure** ] 對話方塊的 [**新增使用者屬性**]。

    ![設定單一登入](./media/active-directory-saas-tableauserver-tutorial/tutorial_general_82.png) 


    b。 在 [ **Attrubute 名稱**] 文字方塊中，輸入**使用者名稱**。

    c。 從**屬性值**清單中，selsect **user.displayname**。

    d。 按一下 [**完成**]。  
    



1. 在頂端的功能表，按一下 [**快速啟動**]。

    ![設定單一登入](./media/active-directory-saas-tableauserver-tutorial/tutorial_general_83.png)  








1. 按一下 [**設定單一登入**以開啟 [**設定單一登入**] 對話方塊。

    ![設定單一登入][6] 



2. 在**您要如何登入 Tableau 伺服器的使用者**] 頁面上，選取**Azure AD 單一登入**，然後按 [**下一步**。

    ![設定單一登入](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_03.png) 


3. **設定應用程式設定**] 對話方塊在頁面上，執行下列步驟，然後按一下 [**下一步**︰

    ![設定單一登入](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_04.png) 



    。 在 [**登入 URL** ] 文字方塊中，輸入 Tableau 伺服器的 URL。 

    b。 在 [識別碼] 方塊中複製 

    c。 按一下 [**下一步**


4. 在 [**設定單一登入 Tableau 伺服器**] 頁面中，執行下列步驟，然後按一下 [**下一步**︰

    ![設定單一登入](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_05.png) 


    。 按一下**下載的中繼資料**]，然後儲存您的電腦上的檔案。

    b。 按一下 [**下一步**]。


6. 若要取得 SSO 應用程式的設定，必須先登入您的系統管理員 Tableau 伺服器租用戶。

    。 在 Tableau Server 組態] 中，按一下 [ **SAML** ] 索引標籤。

    ![設定單一登入](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_001.png) 


    b。 選取**使用 SAML 單一登入**] 核取的方塊。

    c。 找出同盟中繼資料檔案從 Azure 傳統入口網站，下載，然後上傳**SAML Idp 中繼資料檔案**中。

    d。 Tableau 伺服器傳回的 URL，Tableau 伺服器的使用者會存取，例如 http://tableau_server 的 URL。 建議您不要使用 http://localhost。 不支援使用斜線 (例如，http://tableau_server/) 的 URL。 複製**Tableau 伺服器傳回的 URL** ，並將其貼至 Azure AD**登入 URL** ] 文字方塊中，在步驟 3 中所示

    e。 SAML 實體 ID-的實體識別碼唯一識別 Tableau Server 安裝，以 IdP。 您可以輸入 Tableau 伺服器 URL 一次在這裡，如果您想要的話，但沒有為您 Tableau 伺服器的 URL。 複製**SAML 實體識別碼**，並將其貼至 Azure AD**識別碼**] 文字方塊中，在步驟 3 中所示。

    f。 按一下**匯出中繼資料檔案**，然後在文字編輯器應用程式中開啟。 找出含有 Http 文章判斷提示消費者服務 URL 索引 0 並複製 URL。 立即將其貼至 Azure AD**回覆 URL** ] 文字方塊在步驟 3 中所示。 

    g。 按一下**[確定**] 按鈕，在 [Tableau 伺服器 Configiuration 頁面。

    > [AZURE.NOTE] 如果您需要協助 Tableau 伺服器上設定 SAML 然後，請參閱本文[設定 SAML](http://onlinehelp.tableau.com/current/server/en-us/config_saml.htm) 

6. 在 Azure 傳統的入口網站中，選取單一登入設定確認，然後再按 [**下一步**。

    ![Azure AD 單一登入][10]

7. 在**單一登入確認**] 頁面上，按一下 [**完成**]。 
 
    ![Azure AD 單一登入][11]


### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
本節的目標是稱為許承恩 Azure 傳統入口網站中建立的測試使用者。

在 [使用者] 清單中，選取**許承恩**。

![建立 Azure AD 使用者][20]

**若要建立的測試使用者 Azure AD 中，執行下列步驟︰**

1. 在**Azure 傳統入口網站**，在左側的功能窗格中，按一下 [ **Active Directory**]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_09.png) 

2. 從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3. 若要顯示的使用者清單頂端的功能表中，按一下 [**使用者**]。
 
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_03.png) 


4. 若要開啟 [**新增使用者**] 對話方塊底部的工具列中，按一下 [**新增使用者**]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_04.png)

5. 在 [**告訴我們瞭解此使用者**的對話方塊頁面，請執行下列步驟︰

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_05.png) 

    。 **輸入的使用者**，選取**您組織中的新使用者**。

    b。 在 [**使用者名稱**] 文字方塊中，輸入**BrittaSimon**。

    c。 按一下 [**下一步**]。

6.  **使用者設定檔**] 對話方塊在頁面上，執行下列步驟︰

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_06.png) 

    。 在 [**名字**] 文字方塊中輸入**許**。  

    b。 在 [**姓氏**] 文字方塊中輸入，**承恩**。

    c。 在 [**顯示名稱**] 文字方塊中，輸入**許承恩**。

    d。 在 [**角色**] 清單中選取 [**使用者**]。

    e。 按一下 [**下一步**]。

7. 在 [**取得暫時密碼**] 對話方塊頁面上，按一下 [**建立**]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_07.png) 


8. **取得暫時密碼**] 對話方塊在頁面上，執行下列步驟︰
 
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_08.png) 

    。 寫下的值的**新密碼**。

    b。 按一下 [**完成**]。   



### <a name="creating-a-tableau-server-test-user"></a>建立 Tableau 伺服器的測試使用者

本節的目標是建立稱為許承恩 Tableau Server 中的使用者。 您需要佈建 Tableau 伺服器中的所有使用者。 另請注意之使用者的使用者名稱應符合您設定的**使用者名稱**的 Azure AD 自訂屬性中的值。 有正確對應整合應該使用[設定 Azure AD 單一登入](#configuring-azure-ad-single-single-sign-on)。

> [AZURE.NOTE] 如果您需要手動建立使用者時，必須與您組織中的 Tableau 伺服器管理員連絡。


### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

本節的目標是啟用許承恩 Azure 單一登入使用其存取權授與 Tableau 伺服器。

![為使用者指派][200] 

**若要指定許承恩 Tableau 伺服器，請執行下列步驟︰**

1. Azure 傳統的入口網站中，若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [**應用程式**頂端的功能表中。
 
    ![為使用者指派][201] 

2. 在應用程式清單中，選取 [ **Tableau 伺服器**]。

    ![設定單一登入](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_50.png) 


1. 在頂端的功能表，按一下 [**使用者**]。

    ![為使用者指派][203]

1. 在 [使用者] 清單中，選取**許承恩**。

2. 在底部工具列中，按一下 [**指派**]。

![為使用者指派][205]



### <a name="testing-single-sign-on"></a>測試單一登入

本節的目標是測試 Azure AD 單一登入設定使用 Access 面板。

當您按一下 [Access] 面板中的 [Tableau 伺服器] 方塊時，您應該取得自動登入 Tableau 伺服器應用程式。


## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程的清單](active-directory-saas-tutorial-list.md)
* [什麼是應用程式存取和單一登入與 Azure Active Directory？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_205.png
