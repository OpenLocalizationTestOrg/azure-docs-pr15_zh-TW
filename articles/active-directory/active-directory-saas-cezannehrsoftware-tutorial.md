<properties
    pageTitle="教學課程︰ 使用 Cezanne HR 軟體的 Azure Active Directory 整合 |Microsoft Azure"
    description="瞭解如何設定單一登入 Azure Active Directory 和 Cezanne HR 軟體之間。"
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
    ms.date="10/26/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-cezanne-hr-software"></a>教學課程︰ 使用 Cezanne HR 軟體的 Azure Active Directory 整合

本教學課程中的目標是以顯示您如何與 Azure Active Directory (Azure AD) 整合 Cezanne HR 軟體。

Azure AD 與整合 Cezanne HR 軟體為您提供下列優點︰

- 您可以控制可存取 Cezanne HR 軟體 Azure AD
- 您可以使用其 Azure AD 帳戶啟用使用者會自動取得登入 Cezanne HR 軟體 （單一登入）
- 您可以管理您的帳戶，在一個中央位置-Azure 傳統入口網站

如果您想要知道 Azure AD SaaS 應用程式整合相關的更多詳細資料，請參閱[什麼是應用程式存取和單一登入與 Azure Active Directory](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 整合 Cezanne HR 軟體，您需要下列項目︰

- Azure AD 訂閱
- 啟用訂閱 Cezanne HR 軟體單一正負號


> [AZURE.NOTE] 若要測試此教學課程中的步驟，我們不建議使用生產環境。


若要測試此教學課程中的步驟，您應該先按照這些建議︰

- 您應該使用生產環境，除非這是必要的。
- 如果您沒有安裝 Azure AD 試用環境，您可以在一個月試用[這裡](https://azure.microsoft.com/pricing/free-trial/)取得。


## <a name="scenario-description"></a>案例說明
本教學課程中的目的是讓您測試環境中測試 Azure AD 單一登入。

本教學課程中所述的案例是由兩個主要的建置組塊所組成︰

1. 從圖庫新增 Cezanne HR 軟體
2. 設定及測試 Azure AD 單一登入


## <a name="adding-cezanne-hr-software-from-the-gallery"></a>從圖庫新增 Cezanne HR 軟體
若要設定 Cezanne HR 軟體整合 Azure AD，必須將 Cezanne HR 軟體從圖庫新增至您的受管理的 SaaS 應用程式清單。

**若要從圖庫新增 Cezanne HR 軟體，請執行下列步驟︰**

1. 在**Azure 傳統入口網站**，在左側的功能窗格中，按一下 [ **Active Directory**]。 

    ![Active Directory][1]

2. 從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3. 若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [在上方的功能表中的 [**應用程式**]。
    
    ![應用程式][2]

4. 按一下 [**新增**頁面的底部。
    
    ![應用程式][3]

5. 在 [**您想要做什麼**] 對話方塊中，按一下 [**新增應用程式，從圖庫**。

    ![應用程式][4]

6. 在 [搜尋] 方塊中，輸入**Cezanne HR 軟體**。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_01.png)

7. 在 [結果] 面板中，選取**Cezanne HR 軟體**，，然後按一下要新增應用程式**完成**。

    ![在圖庫中選取應用程式](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_0001.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>設定及測試 Azure AD 單一登入
本節的目標是以顯示您如何設定和測試 Azure AD 單一登入，以根據稱為 「 許承恩 」 的測試使用者 Cezanne HR 軟體。

單一登入搭配使用，Azure AD 需要知道什麼對應中的使用者 Cezanne HR 軟體 Azure AD 中的使用者。 也就是說，必須建立連結和之間的關聯 Azure AD 使用者 Cezanne HR 軟體中相關的使用者。

所指派**的使用者名稱**的值為 Cezanne HR 軟體**的使用者名稱**的值 Azure AD 中建立此連結關聯。

若要設定及測試 Azure AD 單一登入以 Cezanne HR 軟體，必須完成下列建置組塊︰

1. **[Azure AD 設定單一登入](#configuring-azure-ad-single-single-sign-on)**-若要讓使用者能使用這項功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)**-Azure AD 單一登入與許承恩測試。
3. **[建立 Cezanne HR 軟體測試使用者](#creating-a-cezanne-hr-software-test-user)**-有許承恩的對應中的已連結至她的 Azure AD 表示 Cezanne HR 軟體。
4. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)**-啟用許承恩使用 Azure AD 單一登入。
5. **[測試單一登入](#testing-single-sign-on)**-驗證是否設定運作。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD 單一登入設定

在此區段中，您可以 Azure AD 單一登入 [傳統] 入口網站中啟用並 Cezanne HR 軟體應用程式中設定單一登入。

**若要以 Cezanne HR 軟體設定 Azure AD 單一登入，請執行下列步驟︰**

1. 在 [傳統] 入口網站， **Cezanne HR 軟體**應用程式整合在頁面上，按一下 [**設定單一登入**以開啟 [**設定單一登入**] 對話方塊。
     
    ![設定單一登入][6] 

2. 在**您要如何登入 Cezanne HR 軟體的使用者**] 頁面上，選取**Azure AD 單一登入**，然後按 [**下一步**。
    
    ![設定單一登入](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_03.png)

3. **設定應用程式設定**] 對話方塊在頁面上，執行下列步驟，然後按一下 [**下一步**︰

    ![設定單一登入](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_04.png)

    。 在 [**登入 URL** ] 文字方塊中，輸入 [使用下列模式的 URL: `https://w3.cezanneondemand.com/cezannehr/-/<tenant id>`。

    b。 在 [**回覆 URL** ] 文字方塊中，輸入 [使用下列模式的 URL: `https://w3.cezanneondemand.com:443/CezanneOnDemand/-/<tenant id>/Saml/samlp`。

    c。 按一下 [**下一步**

    > [AZURE.NOTE] 請注意，您必須更新這些值的實際登入 URL 和回覆 URL。 若要取得這些值，請連絡 Cezanne HR 軟體支援小組透過<mailto:info@cezannehr.com>。

4. 在 [**設定單一登入 Cezanne HR 軟體**] 頁面中，執行下列步驟，然後按一下 [**下一步**︰

    ![設定單一登入](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_05.png)

    。 按一下 [**下載憑證**]，然後儲存您的電腦上的檔案。

    b。 按一下 [**下一步**]。

5. 在不同的網頁瀏覽器視窗中，登入至您的 Cezanne HR 軟體租用戶系統管理員身分。

6. 在左側的功能窗格中，按一下 [**系統設定**]。 移至 [**安全性設定**。 然後瀏覽至**單一登入設定**。

    ![設定單一登入應用程式方](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_000.png)

7. 在**允許使用者使用下列的單一登入 (SSO) 服務登入**面板] 核取 [ **SAML 2.0**方塊，然後選取它旁邊的 [**進階設定**] 選項。

    ![設定單一登入應用程式方](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_001.png)

8. 按一下 [**新增]**按鈕。

    ![設定單一登入應用程式方](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_002.png)

9. 請執行下列步驟**SAML 2.0 身分識別提供者**一節。

    ![設定單一登入應用程式方](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_003.png)

    。 輸入您的身分識別提供者的名稱為 [**顯示名稱**。

    b。 在**實體識別碼**文字方塊，請從 Azure AD 應用程式設定精靈將**實體 ID**的值。

    c。 變更**SAML 繫結**的 「 張貼 」。

    d。 在 [**安全性 Token 服務端點**文字方塊會將**單一登入服務 URL**的值從 Azure AD 應用程式設定精靈。

    e。 輸入 「 http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name 」 的**使用者識別碼屬性名稱**。

    f。 按一下 [**上傳**] 圖示可上傳從 Azure AD 下載的憑證]。

    g。 按一下 [**確定**] 按鈕。 

10. 按一下 [**儲存**] 按鈕。

    ![設定單一登入應用程式方](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_004.png)

11. 在 [傳統] 入口網站，選取單一登入設定確認，然後按 [**下一步**。
    
    ![Azure AD 單一登入][10]

12. 在**單一登入確認**] 頁面上，按一下 [**完成**]。  
    
    ![Azure AD 單一登入][11]



### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
本節的目標是稱為許承恩傳統入口網站中建立一個測試使用者。

![建立 Azure AD 使用者][20]

**若要建立的測試使用者 Azure AD 中，執行下列步驟︰**

1. 在**Azure 傳統入口網站**，在左側的功能窗格中，按一下 [ **Active Directory**]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_09.png)

2. 從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3. 若要顯示的使用者清單頂端的功能表中，按一下 [**使用者**]。
    
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_03.png)

4. 若要開啟 [**新增使用者**] 對話方塊底部的工具列中，按一下 [**新增使用者**]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_04.png)

5. 在 [**告訴我們瞭解此使用者**的對話方塊頁面，請執行下列步驟︰

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_05.png)

    。 為使用者類型，選取您組織中的 [新的使用者]。

    b。 在 [使用者名稱]**文字方塊**中，輸入**BrittaSimon**。

    c。 按一下 [**下一步**]。

6.  **使用者設定檔**] 對話方塊在頁面上，執行下列步驟︰
    
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_06.png)

    。 在 [**名字**] 文字方塊中輸入**許**。  

    b。 在 [**姓氏**] 文字方塊中輸入**承恩**。

    c。 在 [**顯示名稱**] 文字方塊中，輸入**許承恩**。

    d。 在 [**角色**] 清單中選取 [**使用者**]。

    e。 按一下 [**下一步**]。

7. 在 [**取得暫時密碼**] 對話方塊頁面上，按一下 [**建立**]。
    
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_07.png)

8. **取得暫時密碼**] 對話方塊在頁面上，執行下列步驟︰
    
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_08.png)

    。 寫下的值的**新密碼**。

    b。 按一下 [**完成**]。   



### <a name="creating-a-cezanne-hr-software-test-user"></a>建立 Cezanne HR 軟體測試使用者

若要啟用 Azure AD 使用者登入 Cezanne HR 軟體，他們必須提供 Cezanne HR 軟體。 若是 Cezanne HR 軟體，佈建是手動的工作。

####<a name="to-provision-a-user-account-perform-the-following-steps"></a>佈建使用者帳戶，請執行下列步驟︰

1.  請以系統管理員身分登入您 Cezanne HR 軟體公司的網站。

2.  在左側的功能窗格中，按一下 [**系統設定**]。 移至**管理使用者**。 然後瀏覽至 [**新增使用者**。

    ![新的使用者](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_005.png "新的使用者")

3.  在**個人詳細資料**] 區段中，執行下列步驟︰

    ![新的使用者](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_006.png "新的使用者")

    。 將**內部的使用者**設定為 [關閉]。

    b。 在 [**名字**] 文字方塊中輸入**許**。  

    c。 在 [**姓氏**] 文字方塊中輸入**承恩**。

    d。 在 [**電子郵件**] 文字方塊中，輸入許承恩帳戶的電子郵件地址。

4.  在 [**帳戶資訊**] 區段中，執行下列步驟︰

    ![新的使用者](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_007.png "新的使用者")

    。 在 [**使用者名稱**] 文字方塊中，輸入許承恩的電子郵件地址。

    b。 在 [**密碼**] 文字方塊中，輸入許承恩帳戶的密碼。

    c。 選取**HR 專業****安全性**角色。

    d。 按一下**[確定]**。

5. 瀏覽至**單一登入**] 索引標籤，然後選取 [ **SAML 2.0 識別碼**] 區域中的 [**新增]** 。

    ![使用者](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_008.png "使用者")

6. 選擇 [您的身分識別提供者提供**身分識別提供者**，並在文字方塊中的**使用者識別碼**，請輸入許承恩帳戶的電子郵件地址。

    ![使用者](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_009.png "使用者")
    
7. 按一下 [**儲存**] 按鈕。

    ![使用者](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_010.png "使用者")


### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

本節的目標是啟用許承恩 Azure 單一登入使用其存取權授與 Cezanne HR 軟體。
    
![為使用者指派][200]

**若要指定許承恩 Cezanne HR 軟體，請執行下列步驟︰**

1. 在 [傳統] 入口網站，若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [**應用程式**頂端的功能表中。
    
    ![為使用者指派][201]

2. 在應用程式清單中，選取 [ **Cezanne HR 軟體**]。
    
    ![設定單一登入](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_50.png)

3. 在頂端的功能表，按一下 [**使用者**]。
    
    ![為使用者指派][203]

4. 在 [使用者] 清單中，選取**許承恩**。

5. 在底部工具列中，按一下 [**指派**]。
    
    ![為使用者指派][205]

### <a name="testing-single-sign-on"></a>測試單一登入

本節的目標是測試 Azure AD 單一登入設定使用 Access 面板。
 
當您按一下 [Access] 面板中的 [Cezanne HR 軟體] 方塊時，您應該取得自動登入 Cezanne HR 軟體應用程式。

## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程的清單](active-directory-saas-tutorial-list.md)
* [什麼是應用程式存取和單一登入與 Azure Active Directory？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_205.png
