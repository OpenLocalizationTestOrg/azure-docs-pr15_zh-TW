<properties
    pageTitle="教學課程︰ Azure Active Directory 整合 Optimizely |Microsoft Azure"
    description="瞭解如何設定單一登入 Azure Active Directory 和 Optimizely 之間。"
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
    ms.date="09/11/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-optimizely"></a>教學課程︰ 使用 Optimizely 的 Azure Active Directory 整合

在本教學課程中，您可以瞭解如何將 Optimizely 整合與 Azure Active Directory (Azure AD)。

Azure AD 與整合 Optimizely 為您提供下列優點︰

- 您可以控制可存取 Optimizely Azure AD
- 您可以讓使用者能自動取得登入 Optimizely （單一登入） 至其 Azure AD 帳戶
- 您可以管理您的帳戶，在一個中央位置-Azure 傳統入口網站

如果您想要知道 Azure AD SaaS 應用程式整合相關的更多詳細資料，請參閱[什麼是應用程式存取和單一登入與 Azure Active Directory](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件

若要設定 Optimizely Azure AD 整合，您需要下列項目︰

- Azure AD 訂閱
- 啟用訂閱**Optimizely**單一正負號


> [AZURE.NOTE] 若要測試此教學課程中的步驟，我們不建議使用生產環境。


若要測試此教學課程中的步驟，您應該先按照這些建議︰

- 您應該使用生產環境，除非這是必要的。
- 如果您沒有安裝 Azure AD 試用環境，您可以在一個月試用[這裡](https://azure.microsoft.com/pricing/free-trial/)取得。


## <a name="scenario-description"></a>案例說明
在本教學課程中，您測試 Azure AD 單一登入的測試環境中。 本教學課程中所述的案例是由兩個主要的建置組塊所組成︰

1. 從圖庫新增 Optimizely
2. 設定及測試 Azure AD 單一登入


## <a name="adding-optimizely-from-the-gallery"></a>從圖庫新增 Optimizely
若要設定 Optimizely 整合 Azure AD，必須將 Optimizely 從圖庫新增至您的受管理的 SaaS 應用程式清單。

**若要從圖庫新增 Optimizely，請執行下列步驟︰**

1. 在**Azure 傳統入口網站**，在左側的功能窗格中，按一下 [ **Active Directory**]。 

    ![Active Directory][1]

2. 從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3. 若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [在上方的功能表中的 [**應用程式**]。

    ![應用程式][2]

4. 按一下 [**新增**頁面的底部。

    ![應用程式][3]

5. 在 [**您想要做什麼**] 對話方塊中，按一下 [**新增應用程式，從圖庫**。

    ![應用程式][4]

6. 在 [搜尋] 方塊中，輸入**Optimizely**。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_01.png)

7. 在 [結果] 窗格中，選取**Optimizely**，，然後按一下要新增應用程式**完成**。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>設定及測試 Azure AD 單一登入
在此區段中，您可以設定並使用 Optimizely 根據稱為 「 許承恩 」 的測試使用者測試 Azure AD 單一登入。

單一登入搭配使用，Azure AD 需要知道什麼對應中的使用者 Optimizely 使用者 Azure AD。 也就是說，必須建立連結和之間的關聯 Azure AD 使用者 Optimizely 中相關的使用者。
所指派**的使用者名稱**的值為 Optimizely**使用者名稱**的值 Azure AD 中建立此連結關聯。

若要設定及測試 Azure AD 單一登入與 Optimizely，必須完成下列建置組塊︰

1. **[Azure AD 設定單一登入](#configuring-azure-ad-single-single-sign-on)**-若要讓使用者能使用這項功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)**-Azure AD 單一登入與許承恩測試。
4. **[建立 Optimizely 測試使用者](#creating-an-optimizely-test-user)**-連結至 Azure AD 的表示她的 Optimizely 中有許承恩的對應。
5. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)**-啟用許承恩使用 Azure AD 單一登入。
5. **[測試單一登入](#testing-single-sign-on)**-驗證是否設定運作。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD 單一登入設定

本節的目標是 Azure AD 單一登入 Azure 傳統入口網站中啟用並 Optimizely 應用程式中設定單一登入。

Optimizely 應用程式預期的 SAML 判斷提示，以包含名稱為 [電子郵件] 的屬性。 [電子郵件] 的值應該取得經過 Azure AD Optimizely 辨識電子郵件。 請設定此應用程式的 [電子郵件] 宣告。 您可以在**「 Atrributes 」**索引標籤上的應用程式管理這些屬性的值。 以下螢幕擷取畫面會顯示這個範例。 


![設定單一登入](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_03.png) 


**若要使用 Optimizely 設定 Azure AD 單一登入，請執行下列步驟︰**

1. 在 Azure 的傳統入口網站中**Optimizely**應用程式整合在頁面上的功能表頂端，按一下 [**屬性**]。
     
    ![設定單一登入][5]

2. 在 [SAML 權杖屬性] 對話方塊中，加上的 [電子郵件] 屬性。

    。 按一下 [開啟 [**新增使用者屬性**] 對話方塊的 [**新增使用者屬性**]。 
    
    ![設定單一登入](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_05.png)

    b。 在 [**屬性名稱**] 文字方塊中，輸入屬性名稱 [電子郵件]。

    c。 **屬性值**] 清單中，選取屬性值 」 userprincipalname 」 或任何值，包含 Azure AD 辨識電子郵件和 Optimizely。

    d。 按一下 [**完成**]。
3. 在頂端的功能表，按一下 [**快速啟動**]。

    ![設定單一登入][6]
4. 在 [傳統] 入口網站， **Optimizely**應用程式整合在頁面上，按一下 [**設定單一登入**以開啟 [**設定單一登入**] 對話方塊。

    ![設定單一登入][7] 

5. 在**您要如何登入 Optimizely 的使用者**] 頁面上，選取**Azure AD 單一登入**，然後按 [**下一步**。
    
    ![設定單一登入](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_06.png)

6. **設定應用程式設定**] 對話方塊在頁面上，執行下列步驟︰ 

    ![設定單一登入](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_07.png)


    。 在 [**登入 URL** ] 文字方塊中輸入︰`https://app.optimizely.net/contoso`

    b。 在 [**識別碼**] 文字方塊中輸入︰`urn:auth0:optimizely:contoso`

    c。 按一下 [**下一步**]。 


    > [AZURE.NOTE] **登入 URL**以及**識別碼**值是只的版面配置區的實際值。 您可以稍後在本教學課程中找到要求的相關指示 Optimizely 的實際值。

7. 在**設定單一登入 Optimizely 在**頁面上，執行下列步驟︰

    ![設定單一登入](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_08.png)

    。 按一下 [**下載憑證**]，然後儲存您的電腦上的檔案。

    b。 複製**單一登入服務的 URL**。

8. 若要取得 SSO 應用程式的設定，請連絡您的 Optimizely 帳戶管理員，並提供下列資訊︰

    - 您已下載的憑證 
    - 單一登入服務 URL
 
    在您的電子郵件回應，Optimizely 可讓您登入會 URL (SP 啟動 SSO) 與識別碼服務提供者實體值。

9. 回到**設定應用程式設定**] 對話方塊頁面，然後執行下列步驟︰

    ![設定單一登入](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_07.png)

    。 在 [**登入 URL** ] 文字方塊中，輸入 Optimizely 所提供的**預存程序啟動 SSO URL** 。

    b。 在 [**識別碼**] 文字方塊中，輸入 Optimizely 所提供的**服務提供者實體識別碼**。

    c。 按一下 [**下一步**]。

10. 在**設定單一登入 Optimizely 在**頁面上，執行下列步驟︰
    
    ![Azure AD 單一登入][10]

    。 選取單一登入設定確認。

    b。 按一下 [**下一步**]。

11. 在**單一登入確認**] 頁面上，按一下 [**完成**]。  
    
    ![Azure AD 單一登入][11]

12. 在不同的瀏覽器視窗中，登入 Optimizely 應用程式。
13. 按一下您的帳戶名稱右上角，然後**帳戶設定**]。

    ![Azure AD 單一登入](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_09.png)

14. 在 [帳戶] 索引標籤中，核取方塊下單一登入**概觀**一節中**啟用 SSO** 。

    ![Azure AD 單一登入](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_10.png)

### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
在此區段中，您可以建立在傳統入口網站中名為許承恩測試使用者。
在 [使用者] 清單中，選取**許承恩**。

![建立 Azure AD 使用者][20]

**若要建立的測試使用者 Azure AD 中，執行下列步驟︰**

1. 在**Azure 傳統入口網站**，在左側的功能窗格中，按一下 [ **Active Directory**]。
    
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-optimizely-tutorial/create_aaduser_09.png) 

2. 從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3. 若要顯示的使用者清單頂端的功能表中，按一下 [**使用者**]。
    
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-optimizely-tutorial/create_aaduser_03.png) 

4. 若要開啟 [**新增使用者**] 對話方塊底部的工具列中，按一下 [**新增使用者**]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-optimizely-tutorial/create_aaduser_04.png) 

5. 在 [**告訴我們瞭解此使用者**的對話方塊頁面，請執行下列步驟︰
 
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-optimizely-tutorial/create_aaduser_05.png) 

    。 為使用者類型，選取您組織中的 [新的使用者]。

    b。 在 [使用者名稱]**文字方塊**中，輸入**BrittaSimon**。

    c。 按一下 [**下一步**]。

6.  **使用者設定檔**] 對話方塊在頁面上，執行下列步驟︰

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-optimizely-tutorial/create_aaduser_06.png) 

    。 在 [**名字**] 文字方塊中輸入**許**。  

    b。 在 [**姓氏**] 文字方塊中輸入，**承恩**。

    c。 在 [**顯示名稱**] 文字方塊中，輸入**許承恩**。

    d。 在 [**角色**] 清單中選取 [**使用者**]。

    e。 按一下 [**下一步**]。

7. 在 [**取得暫時密碼**] 對話方塊頁面上，按一下 [**建立**]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-optimizely-tutorial/create_aaduser_07.png) 

8. **取得暫時密碼**] 對話方塊在頁面上，執行下列步驟︰

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-optimizely-tutorial/create_aaduser_08.png) 

    。 寫下的值的**新密碼**。

    b。 按一下 [**完成**]。   



### <a name="creating-an-optimizely-test-user"></a>建立 Optimizely 測試使用者

在此區段中，您可以建立 Optimizely 中名為許承恩使用者。

1. 在 [首頁] 頁面上選取 [**共同作業者**] 索引標籤
2. 按一下 [**新 Collaborator**將新的 collaborator 加入至專案。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-optimizely-tutorial/create_aaduser_10.png)

3.  填入 [電子郵件地址，並指派這些角色。 按一下 [**邀請**]。


    ![建立 Azure AD 測試使用者](./media/active-directory-saas-optimizely-tutorial/create_aaduser_11.png)

4. 他們會收到電子郵件邀請。 使用電子郵件地址。 使用者將能夠登入 Optimizely。


### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在此區段中，您可以啟用許承恩 Azure 單一登入使用其存取權授與 Optimizely。

![為使用者指派][200] 

**若要指定 Optimizely 許承恩，請執行下列步驟︰**

1. 在 [傳統] 入口網站，若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [**應用程式**頂端的功能表中。

    ![為使用者指派][201] 

2. 在應用程式清單中，選取 [ **Optimizely**]。

    ![設定單一登入](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_50.png) 

1. 在頂端的功能表，按一下 [**使用者**]。

    ![為使用者指派][203] 

1. 在 [所有使用者] 清單中，選取**許承恩**。

2. 在底部工具列中，按一下 [**指派**]。

    ![為使用者指派][205]


### <a name="testing-single-sign-on"></a>測試單一登入

本節的目標是測試 Azure AD 單一登入設定使用 Access 面板。

當您按一下 [Access] 面板中的 [Optimizely] 方塊時，您應該取得自動登入 Optimizely 應用程式。

## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程的清單](active-directory-saas-tutorial-list.md)
* [什麼是應用程式存取和單一登入與 Azure Active Directory？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-optimizely-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_205.png
