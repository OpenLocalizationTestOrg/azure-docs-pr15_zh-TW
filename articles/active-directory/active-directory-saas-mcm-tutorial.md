<properties 
    pageTitle="教學課程︰ Azure Active Directory 整合 MCM |Microsoft Azure" 
    description="瞭解如何使用 MCM 與 Azure Active Directory 啟用單一登入、 自動化佈建和更多 ！" 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="08/30/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-mcm"></a>教學課程︰ 使用 MCM 的 Azure Active Directory 整合
  
本教學課程中的目標是以顯示您如何將整合 MCM 與 Azure Active Directory (Azure AD)。

Azure AD 與整合 MCM 為您提供下列優點︰

- 您可以控制可存取 MCM Azure AD
- 您可以讓使用者能自動取得登入 MCM （單一登入） 至其 Azure AD 帳戶
- 您可以管理您的帳戶，在一個中央位置-Azure 傳統入口網站

如果您想要知道 Azure AD SaaS 應用程式整合相關的更多詳細資料，請參閱[什麼是應用程式存取和單一登入與 Azure Active Directory](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件

若要設定 MCM Azure AD 整合，您需要下列項目︰

- 有效的 Azure 訂閱
- 啟用訂閱 MCM 單一正負號


> [AZURE.NOTE] 若要測試此教學課程中的步驟，我們不建議使用生產環境。


若要測試此教學課程中的步驟，您應該先按照這些建議︰

- 您應該使用生產環境，除非這是必要的。
- 如果您沒有安裝 Azure AD 試用環境，您可以在一個月試用[這裡](https://azure.microsoft.com/pricing/free-trial/)取得。

## <a name="scenario-description"></a>案例說明
本教學課程中的目的是讓您測試環境中測試 Azure AD 單一登入。

本教學課程中所述的案例是由兩個主要的建置組塊所組成︰

1. 從圖庫新增 MCM
2. 設定及測試 Azure AD 單一登入

## <a name="adding-mcm-from-the-gallery"></a>從圖庫新增 MCM
若要設定 MCM 整合 Azure AD，您需要從圖庫新增 MCM 受管理的 SaaS 應用程式清單。

**若要從圖庫新增 MCM，請執行下列步驟︰**

1.  Azure 傳統入口網站中，在左側的功能窗格中，按一下 [ **Active Directory**]。

    ![Active Directory](./media/active-directory-saas-mcm-tutorial/tutorial_general_01.png "Active Directory")

2.  從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [在上方的功能表中的 [**應用程式**]。

    ![應用程式](./media/active-directory-saas-mcm-tutorial/tutorial_general_02.png "應用程式")

4.  按一下 [**新增**頁面的底部。

    ![新增應用程式](./media/active-directory-saas-mcm-tutorial/tutorial_general_03.png "新增應用程式")

5.  在 [**您想要做什麼**] 對話方塊中，按一下 [**新增應用程式，從圖庫**。

    ![新增 gallerry 應用程式](./media/active-directory-saas-mcm-tutorial/tutorial_general_04.png "新增 gallerry 應用程式")

6.  在**搜尋] 方塊**中，輸入**MCM**。

    ![應用程式組件庫](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_01.png "應用程式組件庫")

7.  在 [結果] 窗格中，選取**MCM**，，然後按一下要新增應用程式**完成**。

    ![MCM](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_001.png "MCM")

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>設定及測試 Azure AD 單一登入
本節的目標是以顯示您如何設定和使用 MCM 根據稱為 「 許承恩 」 的測試使用者測試 Azure AD 單一登入。

單一登入搭配使用，Azure AD 需要知道什麼對應中的使用者 MCM Azure AD 中的使用者。 也就是說，必須建立連結和之間的關聯 Azure AD 使用者 MCM 中相關的使用者。

所指派**的使用者名稱**的值為 MCM**使用者名稱**的值 Azure AD 中建立此連結關聯。

若要設定及測試 Azure AD 單一登入與 MCM，必須完成下列建置組塊︰

1. **[Azure AD 設定單一登入](#configuring-azure-ad-single-single-sign-on)**-若要讓使用者能使用這項功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)**-Azure AD 單一登入與許承恩測試。
3. **[建立 MCM 測試使用者](#creating-a-mcm-test-user)**-連結至 Azure AD 的表示她的 MCM 中有許承恩的對應。
4. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)**-啟用許承恩使用 Azure AD 單一登入。
5. **[測試單一登入](#testing-single-sign-on)**-驗證是否設定運作。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD 單一登入設定
  
在此區段中，您可以 Azure AD 單一登入 [傳統] 入口網站中啟用並 MCM 應用程式中設定單一登入。

**若要使用 MCM 設定 Azure AD 單一登入，請執行下列步驟︰**

1.  在 Azure 的傳統入口網站， **MCM**應用程式整合在頁面上，按一下 [**設定單一登入**以開啟 [**設定單一登入**] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-mcm-tutorial/tutorial_general_05.png "設定單一登入")

2.  在**您要如何登入 MCM 的使用者**] 頁面上，選取**Microsoft Azure AD 單一登入**，然後按 [**下一步**。

    ![Microsoft Azure AD 單一登入](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_03.png "Microsoft Azure AD 單一登入")

3.  設定應用程式設定] 對話方塊在頁面上，執行下列步驟︰

    ![設定應用程式 URL](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_04.png "設定應用程式 URL")

    。 在 [**登入 URL** ] 文字方塊中，輸入︰ `https://myaba.co.uk/client-access/<company name>/saml.php`。
    
    b。 按一下 [**下一步**

4.  在**設定單一登入 MCM 在**頁面上，按一下**下載的中繼資料**]，然後儲存您的電腦上的憑證檔案。

    ![設定單一登入](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_05.png "設定單一登入")

5. 若要取得 SSO 應用程式的設定，請連絡 MCM 支援小組。 附加下載的中繼資料檔案並與他們設定 SSO MCM 小組共用檔案。

6.  在 [傳統] 入口網站，選取單一登入設定確認，然後按 [**下一步**。

    ![設定單一登入](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_06.png "設定單一登入")

7. 在**單一登入確認**] 頁面上，按一下 [**完成**]。

    ![設定單一登入](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_07.png "設定單一登入")


### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

本節的目標是稱為許承恩傳統入口網站中建立一個測試使用者。

![建立 Azure AD 測試使用者](./media/active-directory-saas-mcm-tutorial/create_aaduser_00.png)

**若要建立的測試使用者 Azure AD 中，執行下列步驟︰**

1. 在**Azure 傳統入口網站**，在左側的功能窗格中，按一下 [ **Active Directory**]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-mcm-tutorial/create_aaduser_01.png)

2. 從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3. 若要顯示的使用者清單頂端的功能表中，按一下 [**使用者**]。
    
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-mcm-tutorial/create_aaduser_02.png)

4. 若要開啟 [**新增使用者**] 對話方塊底部的工具列中，按一下 [**新增使用者**]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-mcm-tutorial/create_aaduser_03.png)

5. 在 [**告訴我們瞭解此使用者**的對話方塊頁面，請執行下列步驟︰

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-mcm-tutorial/create_aaduser_04.png)

    。 為使用者類型，選取您組織中的 [新的使用者]。

    b。 在 [使用者名稱]**文字方塊**中，輸入**BrittaSimon**。

    c。 按一下 [**下一步**]。

6.  **使用者設定檔**] 對話方塊在頁面上，執行下列步驟︰
    
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-mcm-tutorial/create_aaduser_05.png)

    。 在 [**名字**] 文字方塊中輸入**許**。  

    b。 在 [**姓氏**] 文字方塊中輸入，**承恩**。

    c。 在 [**顯示名稱**] 文字方塊中，輸入**許承恩**。

    d。 在 [**角色**] 清單中選取 [**使用者**]。

    e。 按一下 [**下一步**]。

7. 在 [**取得暫時密碼**] 對話方塊頁面上，按一下 [**建立**]。
    
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-mcm-tutorial/create_aaduser_06.png)

8. **取得暫時密碼**] 對話方塊在頁面上，執行下列步驟︰
    
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-mcm-tutorial/create_aaduser_07.png)

    。 寫下的值的**新密碼**。

    b。 按一下 [**完成**]。   

###<a name="creating-a-mcm-test-user"></a>建立 MCM 測試使用者
  
在此區段中，您可以建立 MCM 中名為許承恩使用者。 請使用 MCM 將使用者新增 MCM 平台的支援小組。

>[AZURE.NOTE]您可以使用任何其他 MCM 使用者帳戶建立工具或 Api 提供 MCM 佈建 AAD 使用者帳戶。


###<a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者
  
本節的目標是啟用 Azure 單一登入使用其存取權授與 MCM 許承恩。
    
![為使用者指派](./media/active-directory-saas-mcm-tutorial/assign_aaduser_00.png "為使用者指派")

**若要指定 MCM 許承恩，請執行下列步驟︰**

1. 在 [傳統] 入口網站，若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [**應用程式**頂端的功能表中。
    
    ![為使用者指派](./media/active-directory-saas-mcm-tutorial/assign_aaduser_01.png "為使用者指派")

2. 在應用程式清單中，選取 [ **MCM**]。
    
    ![設定單一登入](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_08.png)

1. 在頂端的功能表，按一下 [**使用者**]。
    
    ![為使用者指派](./media/active-directory-saas-mcm-tutorial/assign_aaduser_02.png "為使用者指派")

1. 在 [使用者] 清單中，選取**許承恩**。

2. 在底部工具列中，按一下 [**指派**]。
    
    ![為使用者指派](./media/active-directory-saas-mcm-tutorial/assign_aaduser_03.png "為使用者指派")


### <a name="testing-single-sign-on"></a>測試單一登入

本節的目標是測試 Azure AD 單一登入設定使用 Access 面板。
 
當您按一下 [Access] 面板中的 [MCM] 方塊時，您應該取得自動登入 MCM 應用程式。


## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程的清單](active-directory-saas-tutorial-list.md)
* [什麼是應用程式存取和單一登入與 Azure Active Directory？](active-directory-appssoaccess-whatis.md)