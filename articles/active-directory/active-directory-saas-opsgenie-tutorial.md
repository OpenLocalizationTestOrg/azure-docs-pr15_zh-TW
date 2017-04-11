<properties
    pageTitle="教學課程︰ Azure Active Directory 整合 OpsGenie |Microsoft Azure"
    description="瞭解如何設定單一登入 Azure Active Directory 和 OpsGenie 之間。"
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
    ms.date="10/07/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-opsgenie"></a>教學課程︰ 使用 OpsGenie 的 Azure Active Directory 整合

本教學課程中的目標是以顯示您如何將整合 OpsGenie 與 Azure Active Directory (Azure AD)。

Azure AD 與整合 OpsGenie 為您提供下列優點︰

- 您可以控制可存取 OpsGenie Azure AD
- 您可以讓使用者能自動取得登入 OpsGenie （單一登入） 至其 Azure AD 帳戶
- 您可以管理您的帳戶，在一個中央位置-Azure 傳統入口網站

如果您想要知道 Azure AD SaaS 應用程式整合相關的更多詳細資料，請參閱[什麼是應用程式存取和單一登入與 Azure Active Directory](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件

若要設定 OpsGenie Azure AD 整合，您需要下列項目︰

- Azure AD 訂閱
- OpsGenie 單一登入啟用訂閱


> [AZURE.NOTE] 若要測試此教學課程中的步驟，我們不建議使用生產環境。


若要測試此教學課程中的步驟，您應該先按照這些建議︰

- 您應該使用生產環境，除非這是必要的。
- 如果您沒有安裝 Azure AD 試用環境，您可以在一個月試用[這裡](https://azure.microsoft.com/pricing/free-trial/)取得。


## <a name="scenario-description"></a>案例說明
本教學課程中的目的是讓您測試環境中測試 Azure AD 單一登入。 

本教學課程中所述的案例是由兩個主要的建置組塊所組成︰

1. 從圖庫新增 OpsGenie
2. 設定及測試 Azure AD 單一登入


## <a name="adding-opsgenie-from-the-gallery"></a>從圖庫新增 OpsGenie
若要設定 OpsGenie 整合 Azure AD，必須將 OpsGenie 從圖庫新增至您的受管理的 SaaS 應用程式清單。

**若要從圖庫新增 OpsGenie，請執行下列步驟︰**

1. 在**Azure 傳統入口網站**，在左側的功能窗格中，按一下 [ **Active Directory**]。 

    ![Active Directory][1]

2. 從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3. 若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [在上方的功能表中的 [**應用程式**]。

    ![應用程式][2]

4. 按一下 [**新增**頁面的底部。

    ![應用程式][3]

5. 在 [**您想要做什麼**] 對話方塊中，按一下 [**新增應用程式，從圖庫**。

    ![應用程式][4]

6. 在 [搜尋] 方塊中，輸入**OpsGenie**。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_01.png)

7. 在 [結果] 窗格中，選取**OpsGenie**，，然後按一下要新增應用程式**完成**。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_02.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>設定及測試 Azure AD 單一登入
本節的目標是以顯示您如何設定和使用 OpsGenie 根據稱為 「 許承恩 」 的測試使用者測試 Azure AD 單一登入。

單一登入搭配使用，Azure AD 需要使用者瞭解 Azure AD OpsGenie 對應使用者。 也就是說，必須建立連結和之間的關聯 Azure AD 使用者 OpsGenie 中相關的使用者。

所指派**的使用者名稱**的值為 OpsGenie**使用者名稱**的值 Azure AD 中建立此連結關聯。

若要設定及測試 Azure AD 單一登入與 OpsGenie，必須完成下列建置組塊︰

1. **[Azure AD 設定單一登入](#configuring-azure-ad-single-single-sign-on)**-若要讓使用者能使用這項功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)**-Azure AD 單一登入與許承恩測試。
4. **[建立 OpsGenie 測試使用者](#creating-a-opsgenie-test-user)**-連結至 Azure AD 的表示她的 OpsGenie 中有許承恩的對應。
5. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)**-啟用許承恩使用 Azure AD 單一登入。
5. **[測試單一登入](#testing-single-sign-on)**-驗證是否設定運作。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD 單一登入設定

本節的目標是 Azure AD 單一登入 Azure 傳統入口網站中啟用並 OpsGenie 應用程式中設定單一登入。



**若要使用 OpsGenie 設定 Azure AD 單一登入，請執行下列步驟︰**

1. 在 Azure 的傳統入口網站， **OpsGenie**應用程式整合在頁面上，按一下 [**設定單一登入**以開啟 [**設定單一登入**] 對話方塊。

    ![設定單一登入][6] 

2. 在**您要如何登入 OpsGenie 的使用者**] 頁面上，選取**Azure AD 單一登入**，然後按 [**下一步**。

    ![設定單一登入](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_03.png) 

3. **設定應用程式設定**] 對話方塊在頁面上，執行下列步驟︰

    ![設定單一登入](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_04.png) 


    。 在 [登入 URL] 文字方塊中，輸入您登入您的 OpsGenie 應用程式，使用下列模式的使用者所使用的 URL: **「 https://app.opsgenie.com/auth/login 」**。

    > [AZURE.NOTE] 請如果您需要登入 URL，連絡您的[OpsGenie 支援小組](mailto:support@opsgenie.com)。

    b。 按一下 [**下一步**]。


4. 在**設定單一登入 OpsGenie 在**頁面上，執行下列步驟︰

    ![設定單一登入](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_05.png) 

    。 按一下 [**下載憑證**]，並儲存您的電腦上的檔案。 我們需要此憑證和中繼資料 Url （實體識別碼、 SSO 登入 URL 和登出 URL） 設定 SSO OpsGenie 方。

    b。 按一下 [**下一步**]。


5. 開啟另一個瀏覽器執行個體，然後再登入以系統管理員身分 OpsGenie。

6. 按一下 [**設定**]，然後按一下 [**單一登入**] 索引標籤。
 
    ![OpsGenie 單一登入](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_06.png) 

7. 若要啟用 SSO，選取 [**啟用]**。

    ![OpsGenie 設定](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_07.png) 
   
8. 在 [**提供者**] 區段中，按一下 [ **Azure Active Directory** ] 索引標籤。

    ![OpsGenie 設定](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_08.png) 
    
9. Azure Active Directory] 對話方塊在頁面上，執行下列步驟︰
 
    ![OpsGenie 設定](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_09.png) 

    。 Azure 傳統入口網站中，**設定單一登入，OpsGenie** ] 對話方塊在頁面上，複製 [**單一登入服務的 URL**的值，，然後再貼到 [ **SAML 2.0 結束點**] 文字方塊。

    b。 建立 64 基本編碼的檔案從您下載的憑證。      
    
    > [AZURE.NOTE] 如需詳細資訊，請參閱[如何將轉換成文字檔的二進位憑證](https://www.youtube.com/watch?v=PlgrzUZ-Y1o&feature=youtu.be)。

    c。 在記事本中開啟您 64 基本編碼的憑證，將其內容複製到剪貼簿，，然後將其貼入**X.500 憑證**文字方塊。

    d。 按一下 [**儲存變更**]。


6. 在 Azure 傳統的入口網站中，選取單一登入設定確認，然後再按 [**下一步**。

    ![Azure AD 單一登入][10]

7. 在**單一登入確認**] 頁面上，按一下 [**完成**]。  

    ![Azure AD 單一登入][11]




### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
本節的目標是稱為許承恩 Azure 傳統入口網站中建立的測試使用者。



![建立 Azure AD 使用者][20]

**若要建立的測試使用者 Azure AD 中，執行下列步驟︰**

1. 在**Azure 傳統入口網站**，在左側的功能窗格中，按一下 [ **Active Directory**]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_09.png) 

2. 從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3. 若要顯示的使用者清單頂端的功能表中，按一下 [**使用者**]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_03.png) 

4. 若要開啟 [**新增使用者**] 對話方塊底部的工具列中，按一下 [**新增使用者**]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_04.png) 

5. 在 [**告訴我們瞭解此使用者**的對話方塊頁面，請執行下列步驟︰

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_05.png) 

    。 為使用者類型，選取您組織中的 [新的使用者]。

    b。 在 [使用者名稱]**文字方塊**中，輸入**BrittaSimon**。

    c。 按一下 [**下一步**]。

6.  **使用者設定檔**] 對話方塊在頁面上，執行下列步驟︰

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_06.png) 

    。 在 [**名字**] 文字方塊中輸入**許**。  

    b。 在 [**姓氏**] 文字方塊中輸入，**承恩**。

    c。 在 [**顯示名稱**] 文字方塊中，輸入**許承恩**。

    d。 在 [**角色**] 清單中選取 [**使用者**]。

    e。 按一下 [**下一步**]。

7. 在 [**取得暫時密碼**] 對話方塊頁面上，按一下 [**建立**]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_07.png) 

8. **取得暫時密碼**] 對話方塊在頁面上，執行下列步驟︰

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_08.png) 

    。 寫下的值的**新密碼**。

    b。 按一下 [**完成**]。   



### <a name="creating-a-opsgenie-test-user"></a>建立 OpsGenie 測試使用者

本節的目標是以建立 OpsGenie 中名為許承恩使用者。 

1.  在網頁瀏覽器視窗中，請登入您的系統管理員 OpsGenie 租用戶。

2.  瀏覽至 [使用者] 清單中，按一下左窗格中的**使用者**。
   
    ![OpsGenie 設定](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_10.png) 

3.  按一下 [**新增使用者**]。

3.  在 [**新增使用者**] 對話方塊中，執行下列步驟︰

    ![OpsGenie 設定](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_11.png) 

    。 在 [**電子郵件**] 文字方塊中，輸入許的 Azure Active Directory 中的電子郵件地址。

    b。 在 [**完整名稱**] 文字方塊中，輸入**許承恩**。

    c。 按一下 [**儲存**]。 

許會收到電子郵件設定其設定檔的相關指示。


### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

本節的目標是啟用許承恩 Azure 單一登入使用其存取權授與 OpsGenie。

![為使用者指派][200] 

**若要指定 OpsGenie 許承恩，請執行下列步驟︰**

1. Azure 傳統的入口網站中，若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [**應用程式**頂端的功能表中。
 
    ![為使用者指派][201] 

2. 在應用程式清單中，選取 [ **OpsGenie**]。

    ![設定單一登入](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_50.png) 

1. 在頂端的功能表，按一下 [**使用者**]。

    ![為使用者指派][203] 

1. 在 [使用者] 清單中，選取**許承恩**。

2. 在底部工具列中，按一下 [**指派**]。

    ![為使用者指派][205]



### <a name="testing-single-sign-on"></a>測試單一登入

本節的目標是測試 Azure AD 單一登入設定使用 Access 面板。

當您按一下 [Access] 面板中的 [OpsGenie] 方塊時，您應該取得自動登入 OpsGenie 應用程式。


## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程的清單](active-directory-saas-tutorial-list.md)
* [什麼是應用程式存取和單一登入與 Azure Active Directory？](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_205.png
