<properties
    pageTitle="教學課程︰ Azure Active Directory 整合 Kiteworks |Microsoft Azure"
    description="瞭解如何設定單一登入 Azure Active Directory 和 Kiteworks 之間。"
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
    ms.date="10/20/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-kiteworks"></a>教學課程︰ 使用 Kiteworks 的 Azure Active Directory 整合


本教學課程中的目標是以顯示您如何將整合 Kiteworks 與 Azure Active Directory (Azure AD)。  
Azure AD 與整合 Kiteworks 為您提供下列優點︰ 

- 您可以控制可存取 Kiteworks Azure AD 
- 您可以讓使用者能自動取得登入 Kiteworks （單一登入） 至其 Azure AD 帳戶
- 您可以管理您的帳戶，在一個中央位置的 Azure Active Directory 

如果您想要知道 Azure AD SaaS 應用程式整合相關的更多詳細資料，請參閱[什麼是應用程式存取和單一登入與 Azure Active Directory](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件 

若要設定 Kiteworks Azure AD 整合，您需要下列項目︰

- Azure AD 訂閱
- 啟用訂閱 Kiteworks 單一正負號


> [AZURE.NOTE] 若要測試此教學課程中的步驟，我們不建議使用生產環境。


若要測試此教學課程中的步驟，您應該先按照這些建議︰

- 您應該使用生產環境，除非這是必要的。
- 如果您沒有安裝 Azure AD 試用環境，您可以在一個月試用[這裡](https://azure.microsoft.com/pricing/free-trial/)取得。 

 
## <a name="scenario-description"></a>案例說明
本教學課程中的目的是讓您測試環境中測試 Azure AD 單一登入。  
本教學課程中所述的案例是由兩個主要的建置組塊所組成︰

1. 從圖庫新增 Kiteworks 
2. 設定及測試 Azure AD 單一登入


## <a name="adding-kiteworks-from-the-gallery"></a>從圖庫新增 Kiteworks
若要設定 Kiteworks 整合 Azure AD，必須將 Kiteworks 從圖庫新增至您的受管理的 SaaS 應用程式清單。

**若要從圖庫新增 Kiteworks，請執行下列步驟︰**

1. 在**Azure 傳統入口網站**，在左側的功能窗格中，按一下 [ **Active Directory**]。 

    ![Active Directory][1]

2. 從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3. 若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [在上方的功能表中的 [**應用程式**]。

    ![應用程式][2]

4. 按一下 [**新增**頁面的底部。
 
    ![應用程式][3]

5. 在 [**您想要做什麼**] 對話方塊中，按一下 [**新增應用程式，從圖庫**。

    ![應用程式][4]

6. 在 [搜尋] 方塊中，輸入**Kiteworks**。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-kiteworks-tutorial/tutorial_kiteworks_01.png)

7. 在 [結果] 窗格中，選取**Kiteworks**，，然後按一下要新增應用程式**完成**。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-kiteworks-tutorial/tutorial_kiteworks_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>設定及測試 Azure AD 單一登入
本節的目標是以顯示您如何設定和使用 Kiteworks 根據稱為 「 許承恩 」 的測試使用者測試 Azure AD 單一登入。

單一登入搭配使用，Azure AD 需要知道什麼對應中的使用者 Kiteworks Azure AD 中的使用者。 也就是說，必須建立連結和之間的關聯 Azure AD 使用者 Kiteworks 中相關的使用者。  
所指派**的使用者名稱**的值為 Kiteworks**使用者名稱**的值 Azure AD 中建立此連結關聯。
 
若要設定及測試 Azure AD 單一登入與 Kiteworks，必須完成下列建置組塊︰

1. **[Azure AD 設定單一登入](#configuring-azure-ad-single-single-sign-on)**-若要讓使用者能使用這項功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)**-Azure AD 單一登入與許承恩測試。
4. **[建立 Kiteworks 測試使用者](#creating-a-kiteworks-test-user)**-連結至 Azure AD 的表示她的 Kiteworks 中有許承恩的對應。
5. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)**-啟用許承恩使用 Azure AD 單一登入。
5. **[測試單一登入](#testing-single-sign-on)**-驗證是否設定運作。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD 單一登入設定

本節的目標是 Azure AD 單一登入 Azure 傳統入口網站中啟用並 Kiteworks 應用程式中設定單一登入。 此程序的一部分，您所需建立 64 基本編碼的憑證檔案。 如果您不熟悉這個程序，請參閱[如何將轉換成文字檔的二進位憑證](http://youtu.be/PlgrzUZ-Y1o)。

若要設定單一登入的 Kiteworks，必須已註冊的網域。 如果您沒有已註冊的網域，請連絡您 Kiteworks 的支援小組。  



**若要使用 Kiteworks 設定 Azure AD 單一登入，請執行下列步驟︰**

1. 在 Azure 的傳統入口網站， **Kiteworks**應用程式整合在頁面上，按一下 [**設定單一登入**以開啟 [**設定單一登入**] 對話方塊。

    ![設定單一登入][6] 

2. 在**您要如何登入 Kiteworks 的使用者**] 頁面上，選取**Azure AD 單一登入**，然後按 [**下一步**。

    ![設定單一登入](./media/active-directory-saas-kiteworks-tutorial/tutorial_kiteworks_03.png) 

3. **設定應用程式設定**] 對話方塊在頁面上，執行下列步驟:。

    ![設定單一登入](./media/active-directory-saas-kiteworks-tutorial/tutorial_kiteworks_04.png) 


    。 在 [**登入 URL** ] 文字方塊中，輸入您登入您的 Kiteworks 應用程式的使用者所使用的 URL (例如︰ *https://fabrikam.kiteworks.com/*)。

    b。 按一下 [**下一步**]。
 
 
4. 在**設定單一登入 Kiteworks 在**頁面上，執行下列步驟︰

    ![設定單一登入](./media/active-directory-saas-kiteworks-tutorial/tutorial_kiteworks_05.png) 

    。 按一下 [**下載憑證**]，然後儲存您的電腦上的檔案。

    b。 按一下 [**下一步**]。


1. 以系統管理員身分登入您 Kiteworks 公司的網站。

1. 在頂端上工具列中，按一下 [**設定**]。

    ![設定單一登入](./media/active-directory-saas-kiteworks-tutorial/tutorial_kiteworks_06.png) 


1. 在 [**驗證與授權**] 區段中，按一下 [ **SSO 設定**]。 

    ![設定單一登入](./media/active-directory-saas-kiteworks-tutorial/tutorial_kiteworks_07.png) 


1. 在 [SSO 設定] 頁面上執行下列步驟︰

    ![設定單一登入](./media/active-directory-saas-kiteworks-tutorial/tutorial_kiteworks_09.png) 

    。 選取 [**透過 SSO 進行驗證**。

    b。 選取 [**啟動 AuthnRequest**]。

    c。 Azure 傳統入口網站，在 [**設定單一登入在 Kiteworks**對話方塊] 頁面中的**實體識別碼**值，複製，然後再貼到 [ **IDP 實體 ID** ] 文字方塊。 

    d。 Azure 傳統入口網站，在 [**設定單一登入在 Kiteworks**對話方塊] 頁面中的**單一登入服務 URL**值，複製，然後再貼到**單一登入服務 URL** ] 文字方塊。

    e。 在 Azure 的傳統入口網站中**設定單一登入，Kiteworks** ] 對話方塊在頁面上，將**單一 Sign-Out 服務 URL**值，複製，然後再貼到**單一登出服務 URL** ] 文字方塊。

    f。 在記事本中開啟您下載的憑證與複製內容，，然後將其貼入**RSA 公用金鑰憑證**文字方塊。 

    g。 按一下 [**儲存**]。


6. 在 Azure 傳統的入口網站中，選取單一登入設定確認，然後再按 [**下一步**。 

    ![Azure AD 單一登入][10]

7. 在**單一登入確認**] 頁面上，按一下 [**完成**]。  

    ![Azure AD 單一登入][11]




### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
本節的目標是稱為許承恩 Azure 傳統入口網站中建立的測試使用者。

![建立 Azure AD 使用者][20]

**若要建立的測試使用者 Azure AD 中，執行下列步驟︰**

1. 在**Azure 傳統入口網站**，在左側的功能窗格中，按一下 [ **Active Directory**]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-kiteworks-tutorial/create_aaduser_09.png)  

2. 從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3. 若要顯示的使用者清單頂端的功能表中，按一下 [**使用者**]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-kiteworks-tutorial/create_aaduser_03.png) 
 
4. 若要開啟 [**新增使用者**] 對話方塊底部的工具列中，按一下 [**新增使用者**]。 

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-kiteworks-tutorial/create_aaduser_04.png) 

5. 在 [**告訴我們瞭解此使用者**的對話方塊頁面，請執行下列步驟︰ 

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-kiteworks-tutorial/create_aaduser_05.png)  

    。 為使用者類型，選取您組織中的 [新的使用者]。

    b。 在 [使用者名稱]**文字方塊**中，輸入**BrittaSimon**。

    c。 按一下 [**下一步**]。

6.  **使用者設定檔**] 對話方塊在頁面上，執行下列步驟︰ 

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-kiteworks-tutorial/create_aaduser_06.png) 
 
    。 在 [**名字**] 文字方塊中輸入**許**。  

    b。 在 [**姓氏**] 文字方塊中輸入，**承恩**。

    c。 在 [**顯示名稱**] 文字方塊中，輸入**許承恩**。

    d。 在 [**角色**] 清單中選取 [**使用者**]。
    e。 按一下 [**下一步**]。

7. 在 [**取得暫時密碼**] 對話方塊頁面上，按一下 [**建立**]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-kiteworks-tutorial/create_aaduser_07.png) 
 
8. **取得暫時密碼**] 對話方塊在頁面上，執行下列步驟︰

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-kiteworks-tutorial/create_aaduser_08.png) 
  
    。 寫下的值的**新密碼**。

    b。 按一下 [**完成**]。   

  
 
### <a name="creating-a-kiteworks-test-user"></a>建立 Kiteworks 測試使用者

本節的目標是以建立 Kiteworks 中名為許承恩使用者。
Kiteworks 支援只時間佈建，這是預設啟用。

沒有動作項目為您在此區段中。
在嘗試存取 Kitewors 如果尚不存在，將會建立新的使用者。

> [AZURE.NOTE] 若要手動建立的使用者，您需要連絡 Kiteworks 支援小組。


### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

本節的目標是啟用許承恩 Azure 單一登入使用其存取權授與 Kiteworks。

![為使用者指派][200] 

**若要指定 Kiteworks 許承恩，請執行下列步驟︰**

1. Azure 傳統的入口網站中，若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [**應用程式**頂端的功能表中。

    ![為使用者指派][201] 

2. 在應用程式清單中，選取 [ **Kiteworks**]。

    ![設定單一登入](./media/active-directory-saas-kiteworks-tutorial/tutorial_kiteworks_50.png) 

1. 在頂端的功能表，按一下 [**使用者**]。

    ![為使用者指派][203] 

1. 在 [使用者] 清單中，選取**許承恩**。

2. 在底部工具列中，按一下 [**指派**]。

    ![為使用者指派][205]



### <a name="testing-single-sign-on"></a>測試單一登入

本節的目標是測試 Azure AD 單一登入設定使用 Access 面板。  
當您按一下 [Access] 面板中的 [Kiteworks] 方塊時，您應該取得自動登入 Kiteworks 應用程式。


## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程的清單](active-directory-saas-tutorial-list.md)
* [什麼是應用程式存取和單一登入與 Azure Active Directory？](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_205.png






