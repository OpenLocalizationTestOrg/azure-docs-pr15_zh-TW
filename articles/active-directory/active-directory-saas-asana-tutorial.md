<properties
    pageTitle="教學課程︰ Azure Active Directory 整合 Asana |Microsoft Azure"
    description="瞭解如何設定單一登入 Azure Active Directory 和 Asana 之間。"
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
    ms.date="10/24/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-asana"></a>教學課程︰ 使用 Asana 的 Azure Active Directory 整合

在本教學課程中，您可以瞭解如何將 Asana 整合與 Azure Active Directory (Azure AD)。

Azure AD 與整合 Asana 為您提供下列優點︰

- 您可以控制可存取 Asana Azure AD
- 您可以讓使用者能自動取得登入 Asana （單一登入） 至其 Azure AD 帳戶
- 您可以管理您的帳戶，在一個中央位置-Azure 傳統入口網站

如果您想要知道 Azure AD SaaS 應用程式整合相關的更多詳細資料，請參閱[什麼是應用程式存取和單一登入與 Azure Active Directory](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件

若要設定 Asana Azure AD 整合，您需要下列項目︰

- Azure AD 訂閱
- 啟用訂閱**Asana**單一正負號


> [AZURE.NOTE] 若要測試此教學課程中的步驟，我們不建議使用生產環境。


若要測試此教學課程中的步驟，您應該先按照這些建議︰

- 您應該使用生產環境，除非這是必要的。
- 如果您沒有安裝 Azure AD 試用環境，您可以在一個月試用[這裡](https://azure.microsoft.com/pricing/free-trial/)取得。


## <a name="scenario-description"></a>案例說明
在本教學課程中，您測試 Azure AD 單一登入的測試環境中。 本教學課程中所述的案例是由兩個主要的建置組塊所組成︰

1. 從圖庫新增 Asana
2. 設定及測試 Azure AD 單一登入


## <a name="adding-asana-from-the-gallery"></a>從圖庫新增 Asana
若要設定 Asana 整合 Azure AD，必須將 Asana 從圖庫新增至您的受管理的 SaaS 應用程式清單。

**若要從圖庫新增 Asana，請執行下列步驟︰**

1. 在**Azure 傳統入口網站**，在左側的功能窗格中，按一下 [ **Active Directory**]。 

    ![Active Directory][1]

2. 從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3. 若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [在上方的功能表中的 [**應用程式**]。

    ![應用程式][2]

4. 按一下 [**新增**頁面的底部。

    ![應用程式][3]

5. 在 [**您想要做什麼**] 對話方塊中，按一下 [**新增應用程式，從圖庫**。

    ![應用程式][4]

6. 在 [搜尋] 方塊中，輸入**Asana**。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-asana-tutorial/tutorial_asana_01.png)

7. 在 [結果] 窗格中，選取**Asana**，，然後按一下要新增應用程式**完成**。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-asana-tutorial/tutorial_asana_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>設定及測試 Azure AD 單一登入
在此區段中，您可以設定並使用 Asana 根據稱為 「 許承恩 」 的測試使用者測試 Azure AD 單一登入。

單一登入搭配使用，Azure AD 需要知道什麼對應中的使用者 Asana 使用者 Azure AD。 也就是說，必須建立連結和之間的關聯 Azure AD 使用者 Asana 中相關的使用者。
所指派**的使用者名稱**的值為 Asana**使用者名稱**的值 Azure AD 中建立此連結關聯。

若要設定及測試 Azure AD 單一登入與 Asana，必須完成下列建置組塊︰

1. **[Azure AD 設定單一登入](#configuring-azure-ad-single-single-sign-on)**-若要讓使用者能使用這項功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)**-Azure AD 單一登入與許承恩測試。
4. **[建立 Asana 測試使用者](#creating-an-Asana-test-user)**-連結至 Azure AD 的表示她的 Asana 中有許承恩的對應。
5. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)**-啟用許承恩使用 Azure AD 單一登入。
5. **[測試單一登入](#testing-single-sign-on)**-驗證是否設定運作。

### <a name="configuring-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

本節的目標是 Azure AD 單一登入 Azure 傳統入口網站中啟用並 Asana 應用程式中設定單一登入。

**若要使用 Asana 設定 Azure AD 單一登入，請執行下列步驟︰**

1. 在頂端的功能表，按一下 [**快速啟動**]。

    ![設定單一登入][6]
2. 在 [傳統] 入口網站， **Asana**應用程式整合在頁面上，按一下 [**設定單一登入**以開啟 [**設定單一登入**] 對話方塊。

    ![設定單一登入][7] 

3. 在**您要如何登入 Asana 的使用者**] 頁面上，選取**Azure AD 單一登入**，然後按 [**下一步**。
    
    ![設定單一登入](./media/active-directory-saas-asana-tutorial/tutorial_asana_06.png)

4. **設定應用程式設定**] 對話方塊在頁面上，執行下列步驟︰ 

    ![設定單一登入](./media/active-directory-saas-asana-tutorial/tutorial_asana_07.png)


    。 在 [登入 URL] 文字方塊中輸入 URL，請使用下列模式︰`https://app.asana.com`

    c。 按一下 [**下一步**]。

5. 在**設定單一登入 Asana 在**頁面上，按一下 [**下載憑證**，然後儲存您的電腦上的檔案。 此外，SAML SSO url 複製的值。
    
    ![設定單一登入](./media/active-directory-saas-asana-tutorial/tutorial_asana_08.png)

8. 憑證，以滑鼠右鍵按一下，然後開啟 [記事本] 或您慣用的文字編輯器憑證檔案。 將複製的開始和結束憑證標題之間的內容。 這是您在 Asana 中用來設定 SSO 的 X.509 憑證。

6. 在不同的瀏覽器視窗中，登入 Asana 應用程式。 若要設定 SSO Asana 中，即可在螢幕的右上角的工作區名稱存取工作區設定。 然後按一下 [上**\<您的工作區名稱\>設定**。 

    ![設定單一登入](./media/active-directory-saas-asana-tutorial/tutorial_asana_09.png)

7. 在 [**組織設定**] 視窗中，按一下 [**管理**]。 然後按一下 [**成員必須登入，透過 SAML**啟用 SSO 設定]。 執行下列步驟︰

    ![設定單一登入](./media/active-directory-saas-asana-tutorial/tutorial_asana_10.png)

    。 在**登入頁面的 URL** texbox 中，貼上 URL 從 Azure AD 上的 [SAML 登。

    b。 在 [ **X.509 憑證**] 文字方塊中貼上您是從 Azure AD 複製 X.509 憑證。

9. 按一下 [**儲存**]。 如果您需要進一步協助，請移至[Asana 指南 SSO 設定](https://asana.com/guide/help/premium/authentication#gl-saml)。

7. 移至 Azure AD 中的**設定單一登入在 Asana**頁面，選取 [單一登入設定確認，然後按一下 [**下一步**。
    
    ![Azure AD 單一登入][10]

8. 在**單一登入確認**] 頁面上，按一下 [**完成**]。  
    
    ![Azure AD 單一登入][11]


### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
在此區段中，您可以建立在傳統入口網站中名為許承恩測試使用者。

![建立 Azure AD 使用者][20]

**若要建立的測試使用者 Azure AD 中，執行下列步驟︰**

1. 在**Azure 傳統入口網站**，在左側的功能窗格中，按一下 [ **Active Directory**]。
    
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-asana-tutorial/create_aaduser_09.png) 

2. 從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3. 若要顯示的使用者清單頂端的功能表中，按一下 [**使用者**]。
    
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-asana-tutorial/create_aaduser_03.png) 

4. 若要開啟 [**新增使用者**] 對話方塊底部的工具列中，按一下 [**新增使用者**]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-asana-tutorial/create_aaduser_04.png) 

5. 在 [**告訴我們瞭解此使用者**的對話方塊頁面，請執行下列步驟︰
 
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-asana-tutorial/create_aaduser_05.png) 

    。 為使用者類型，選取您組織中的 [新的使用者]。

    b。 在 [使用者名稱]**文字方塊**中，輸入**BrittaSimon**。

    c。 按一下 [**下一步**]。

6.  **使用者設定檔**] 對話方塊在頁面上，執行下列步驟︰

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-asana-tutorial/create_aaduser_06.png) 

    。 在 [**名字**] 文字方塊中輸入**許**。  

    b。 在 [**姓氏**] 文字方塊中輸入，**承恩**。

    c。 在 [**顯示名稱**] 文字方塊中，輸入**許承恩**。

    d。 在 [**角色**] 清單中選取 [**使用者**]。

    e。 按一下 [**下一步**]。

7. 在 [**取得暫時密碼**] 對話方塊頁面上，按一下 [**建立**]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-asana-tutorial/create_aaduser_07.png) 

8. **取得暫時密碼**] 對話方塊在頁面上，執行下列步驟︰

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-asana-tutorial/create_aaduser_08.png) 

    。 寫下的值的**新密碼**。

    b。 按一下 [**完成**]。   



### <a name="creating-an-asana-test-user"></a>建立 Asana 測試使用者

在此區段中，您可以建立 Asana 中名為許承恩使用者。

1. 在**Asana**，移至**小組**區段在 [左] 面板。 按一下加號按鈕。 

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-asana-tutorial/tutorial_asana_12.png) 

2. 輸入電子郵件britta.simon@contoso.com中文字] 方塊，然後選取**[邀請**。
3. 按一下 [**傳送邀請**。 新的使用者會收到電子郵件，將其電子郵件帳戶。 需要先建立及驗證的帳戶。


### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在此區段中，您可以啟用許承恩 Azure 單一登入使用其存取權授與 Asana。

![為使用者指派][200] 

**若要指定 Asana 許承恩，請執行下列步驟︰**

1. 在 [傳統] 入口網站，若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [**應用程式**頂端的功能表中。

    ![為使用者指派][201] 

2. 在應用程式清單中，選取 [ **Asana**]。

    ![設定單一登入](./media/active-directory-saas-asana-tutorial/tutorial_asana_50.png) 

1. 在頂端的功能表，按一下 [**使用者**]。

    ![為使用者指派][203] 

1. 在 [所有使用者] 清單中，選取**許承恩**。

2. 在底部工具列中，按一下 [**指派**]。

    ![為使用者指派][205]


### <a name="testing-single-sign-on"></a>測試單一登入

本節的目標是以測試您 Azure AD 單一登入。

移至 Asana 登入頁面。 在電子郵件地址] 文字方塊插入電子郵件地址britta.simon@contoso.com。 離開 [密碼] 文字方塊中空白，然後按一下 [**登入**。 您會被重新導向至 Azure AD 登入頁面。 完成您的 Azure AD 認證。 現在，您會在 Asana 上登入。

## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程的清單](active-directory-saas-tutorial-list.md)
* [什麼是應用程式存取和單一登入與 Azure Active Directory？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-asana-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-asana-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-asana-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-asana-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-asana-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-asana-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-asana-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-asana-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-asana-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-asana-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-asana-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-asana-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-asana-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-asana-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-asana-tutorial/tutorial_general_205.png
