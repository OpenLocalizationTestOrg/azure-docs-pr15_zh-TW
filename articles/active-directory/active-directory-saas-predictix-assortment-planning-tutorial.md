<properties
    pageTitle="教學課程︰ Azure Active Directory 整合 Predictix 各種不同的 「 規劃 |Microsoft Azure"
    description="瞭解如何設定單一登入 Azure Active Directory 和 Predictix 各種不同的 「 規劃之間。"
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
    ms.date="10/14/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-predictix-assortment-planning"></a>教學課程︰ Azure Active Directory 整合 Predictix 各種不同的 「 規劃

在本教學課程中，您可以瞭解如何將整合 Predictix 各種不同的 「 規劃 」，與 Azure Active Directory (Azure AD)。

Azure AD 整合 Predictix 各種不同的 「 規劃為您提供下列優點︰

- 您可以控制可存取 Predictix 各種不同的 「 規劃 Azure AD
- 您可以使用其 Azure AD 帳戶啟用您自動取得登入 Predictix 規劃 （單一登入） 的各種不同的使用者
- 您可以管理您的帳戶，在一個中央位置-Azure 傳統入口網站

如果您想要知道 Azure AD SaaS 應用程式整合相關的更多詳細資料，請參閱[什麼是應用程式存取和單一登入與 Azure Active Directory](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 整合 Predictix 各種不同的 「 規劃，您需要下列項目︰

- Azure AD 訂閱
- Predictix 各種不同的 「 規劃單一登啟用的訂閱


> [AZURE.NOTE] 若要測試此教學課程中的步驟，我們不建議使用生產環境。


若要測試此教學課程中的步驟，您應該先按照這些建議︰

- 您應該使用生產環境，除非這是必要的。
- 如果您沒有安裝 Azure AD 試用環境，您可以在一個月試用[這裡](https://azure.microsoft.com/pricing/free-trial/)取得。


## <a name="scenario-description"></a>案例說明
在本教學課程中，您測試 Microsoft Azure AD 單一登入的測試環境中。

本教學課程中所述的案例是由兩個主要的建置組塊所組成︰

1. 從圖庫新增 [Predictix 各種不同的 「 規劃
2. 設定及測試 Microsoft Azure AD 單一登入


## <a name="adding-predictix-assortment-planning-from-the-gallery"></a>從圖庫新增 [Predictix 各種不同的 「 規劃
若要設定的 Predictix 各種不同的 「 規劃整合到 Azure AD，您需要 Predictix 各種不同的 「 規劃從庫新增至您的受管理的 SaaS 應用程式清單。

**若要新增 Predictix 各種不同的 「 規劃圖庫，執行下列步驟︰**

1. 在**Azure 傳統入口網站**，在左側的功能窗格中，按一下 [ **Active Directory**]。

    ![Active Directory][1]
2. 從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3. 若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [在上方的功能表中的 [**應用程式**]。

    ![應用程式][2]

4. 按一下 [**新增**頁面的底部。

    ![應用程式][3]

5. 在 [**您想要做什麼**] 對話方塊中，按一下 [**新增應用程式，從圖庫**。

    ![應用程式][4]

6. 在 [搜尋] 方塊中，輸入**Predictix 各種不同的 「 規劃**。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_predictixassortmentplanning_01.png)

7. 在 [結果] 窗格中，選取**Predictix 各種不同的 「 規劃**，然後按一下要新增應用程式**完成**。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_predictixassortmentplanning_02.png)


##  <a name="configuring-and-testing-microsoft-azure-ad-single-sign-on"></a>設定及測試 Microsoft Azure AD 單一登入
在此區段中，您可以設定並測試的 Microsoft Azure AD 單一登入有 Predictix 各種不同的 「 規劃根據 [測試使用者稱為 「 許承恩 」。

單一登入的工作，必須知道對應使用者 Predictix 各種不同的 「 規劃功能使用者 Azure AD Azure AD。 也就是說，必須建立連結和之間的關聯 Azure AD 使用者 Predictix 各種不同的 「 規劃中相關的使用者。

所指派**的使用者名稱**的值為計劃 Predictix 各種不同**的使用者名稱**的值 Azure AD 中建立此連結關聯。

若要設定及測試 Microsoft Azure AD 單一登入有 Predictix 各種不同的 「 規劃，必須完成下列建置組塊︰

1. **[設定 Microsoft Azure AD 單一登入](#configuring-azure-ad-single-sign-on)**-若要讓使用者能使用這項功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)**-Microsoft Azure AD 單一登入與許承恩測試。
3. **[建立 Predictix 各種不同的 「 規劃測試使用者](#creating-a-predictix-price-reporting-test-user)**-將對應的許承恩 Predictix 各種不同規劃 Azure AD 表示她的連結。
4. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)**-啟用許承恩使用 Microsoft Azure AD 單一登入。
5. **[測試單一登入](#testing-single-sign-on)**-驗證是否設定運作。

### <a name="configuring-microsoft-azure-ad-single-sign-on"></a>設定 Microsoft Azure AD 單一登入

在此區段中，您將 Microsoft Azure AD 單一登入 [傳統] 入口網站中啟用並 Predictix 各種不同的 「 規劃應用程式中設定單一登入。


**若要進行 Predictix 各種不同的 「 規劃，以設定 Microsoft Azure AD 單一登入，請執行下列步驟︰**

1. 在 [傳統] 入口網站， **Predictix 各種不同的 「 規劃**應用程式整合在頁面上，按一下 [**設定單一登入**以開啟 [**設定單一登入**] 對話方塊。
     
    ![設定單一登入][6] 

2. 在**您要如何登入 Predictix 各種不同的 「 規劃的使用者**] 頁面上，選取**Microsoft Azure AD 單一登入**，然後按 [**下一步**。

    ![設定單一登入](./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_predictixassortmentplanning_03.png) 

3. **設定應用程式設定**] 對話方塊在頁面上，執行下列步驟︰

    ![設定單一登入](./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_predictixassortmentplanning_04.png) 

    。 在 [**登入 URL** ] 文字方塊中，輸入您登入您計劃 Predictix 各種不同的應用程式使用下列模式的使用者所使用的 URL: **https://\<公司名稱價格\>.ap.predictix.com/sso/request**。
    
    b。 按一下 [**下一步**
 
4. 在**設定單一登入 Predictix 各種不同的 「 規劃在**頁面上，執行下列步驟︰

    ![設定單一登入](./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_predictixassortmentplanning_05.png)

    。 按一下 [**下載憑證**]，然後儲存您的電腦上的檔案。

    b。 按一下 [**下一步**]。


5. 若要取得 SSO 應用程式的設定，連絡 Predictix 各種不同的 「 規劃支援小組，並向他們提供下列動作︰

    • 下載憑證

    •**實體識別碼**

    • **SAML SSO URL**

    •**單一登出服務 URL**

6. 在 [傳統] 入口網站，選取單一登入設定確認，然後按 [**下一步**。
    
    ![Azure AD 單一登入][10]

7. 在**單一登入確認**] 頁面上，按一下 [**完成**]。  
 
    ![Azure AD 單一登入][11]


### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
在此區段中，您可以建立在傳統入口網站中名為許承恩測試使用者。


![建立 Azure AD 使用者][20]

**若要建立的測試使用者 Azure AD 中，執行下列步驟︰**

1. 在**Azure 傳統入口網站**，在左側的功能窗格中，按一下 [ **Active Directory**]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-predictix-assortment-planning-tutorial/create_aaduser_09.png) 

2. 從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3. 若要顯示的使用者清單頂端的功能表中，按一下 [**使用者**]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-predictix-assortment-planning-tutorial/create_aaduser_03.png) 

4. 若要開啟 [**新增使用者**] 對話方塊底部的工具列中，按一下 [**新增使用者**]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-predictix-assortment-planning-tutorial/create_aaduser_04.png) 

5. 在 [**告訴我們瞭解此使用者**的對話方塊頁面，請執行下列步驟︰ ![建立 Azure AD 測試使用者](./media/active-directory-saas-predictix-assortment-planning-tutorial/create_aaduser_05.png) 

    。 為使用者類型，選取您組織中的 [新的使用者]。

    b。 在 [使用者名稱]**文字方塊**中，輸入**BrittaSimon**。

    c。 按一下 [**下一步**]。

6.  在 [**使用者設定檔**] 對話方塊頁面，請執行下列步驟︰![建立 Azure AD 測試使用者](./media/active-directory-saas-predictix-assortment-planning-tutorial/create_aaduser_06.png) 

    。 在 [**名字**] 文字方塊中輸入**許**。  

    b。 在 [**姓氏**] 文字方塊中輸入，**承恩**。

    c。 在 [**顯示名稱**] 文字方塊中，輸入**許承恩**。

    d。 在 [**角色**] 清單中選取 [**使用者**]。

    e。 按一下 [**下一步**]。

7. 在 [**取得暫時密碼**] 對話方塊頁面上，按一下 [**建立**]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-predictix-assortment-planning-tutorial/create_aaduser_07.png) 

8. **取得暫時密碼**] 對話方塊在頁面上，執行下列步驟︰

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-predictix-assortment-planning-tutorial/create_aaduser_08.png) 

    。 寫下的值的**新密碼**。

    b。 按一下 [**完成**]。   



### <a name="creating-an-predictix-assortment-planning-test-user"></a>建立 Predictix 各種不同的 「 規劃測試使用者

在此區段中，您可以建立稱為許承恩 Predictix 各種不同的 「 規劃中的使用者。 請使用 Predictix 各種不同的 「 規劃在 Predictix 各種不同的 「 規劃平台新增使用者的支援小組。


### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在此區段中，您可以啟用許承恩 Azure 單一登入使用授與其存取權 Predictix 各種不同的 「 規劃。

![為使用者指派][200] 

**若要指定許承恩 Predictix 各種不同的 「 規劃，執行下列步驟︰**

1. 在 [傳統] 入口網站，若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [**應用程式**頂端的功能表中。

    ![為使用者指派][201] 

2. 在應用程式清單中，選取 [ **Predictix 各種不同的 「 規劃**]。

    ![設定單一登入](./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_predictixassortmentplanning_50.png) 

3. 在頂端的功能表，按一下 [**使用者**]。

    ![為使用者指派][203]

4. 在 [使用者] 清單中，選取**許承恩**。

5. 在底部工具列中，按一下 [**指派**]。

    ![為使用者指派][205]


### <a name="testing-single-sign-on"></a>測試單一登入

在此區段中，您可以測試使用 Access 面板單一登入設定 Microsoft Azure AD。

當您按一下 [Access] 面板中的 [Predictix 各種不同的 「 規劃] 方塊時，您應該取得自動登入您計劃 Predictix 各種不同的應用程式。


## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程的清單](active-directory-saas-tutorial-list.md)
* [什麼是應用程式存取和單一登入與 Azure Active Directory？](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_205.png
