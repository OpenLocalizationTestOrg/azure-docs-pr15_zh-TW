<properties 
    pageTitle="教學課程︰ Azure Active Directory 整合 ClickTime |Microsoft Azure" 
    description="瞭解如何使用 ClickTime 與 Azure Active Directory 啟用單一登入，自動化佈建和更多 ！" 
    services="active-directory" 
    authors="jeevansd"
    documentationCenter="na" 
    manager="femila" />
<tags
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="08/16/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-clicktime"></a>教學課程︰ 使用 ClickTime 的 Azure Active Directory 整合

在本教學課程中，您可以瞭解如何將 ClickTime 整合與 Azure Active Directory (Azure AD)。

Azure AD 與整合 ClickTime 為您提供下列優點︰

- 您可以控制可存取 ClickTime Azure AD
- 您可以讓使用者能自動取得登入 ClickTime （單一登入） 至其 Azure AD 帳戶
- 您可以管理您的帳戶，在一個中央位置-Azure 傳統入口網站

如果您想要知道 Azure AD SaaS 應用程式整合相關的更多詳細資料，請參閱[什麼是應用程式存取和單一登入與 Azure Active Directory](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件

若要設定 ClickTime Azure AD 整合，您需要下列項目︰

- Azure AD 訂閱
- 啟用訂閱 ClickTime 單一正負號


> [AZURE.NOTE] 若要測試此教學課程中的步驟，我們不建議使用生產環境。


若要測試此教學課程中的步驟，您應該先按照這些建議︰

- 您應該使用生產環境，除非這是必要的。
- 如果您沒有安裝 Azure AD 試用環境，您可以在一個月試用[這裡](https://azure.microsoft.com/pricing/free-trial/)取得。


## <a name="scenario-description"></a>案例說明
在本教學課程中，您測試 Azure AD 單一登入的測試環境中。

本教學課程中所述的案例是由兩個主要的建置組塊所組成︰

1. 從圖庫新增 ClickTime
2. 設定及測試 Azure AD 單一登入

##<a name="adding-clicktime-from-the-gallery"></a>從圖庫新增 ClickTime

本節的目標是大綱如何啟用 ClickTime 的整合應用程式。

###<a name="to-enable-the-application-integration-for-clicktime-perform-the-following-steps"></a>若要啟用的 ClickTime 整合應用程式，請執行下列步驟︰

1.  Azure 傳統入口網站中，在左側的功能窗格中，按一下 [ **Active Directory**]。

    ![Active Directory](./media/active-directory-saas-clicktime-tutorial/tic700993.png "Active Directory")

2.  從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [在上方的功能表中的 [**應用程式**]。

    ![應用程式](./media/active-directory-saas-clicktime-tutorial/tic700994.png "應用程式")

4.  按一下 [**新增**頁面的底部。

    ![新增應用程式](./media/active-directory-saas-clicktime-tutorial/tic749321.png "新增應用程式")

5.  在 [**您想要做什麼**] 對話方塊中，按一下 [**新增應用程式，從圖庫**。

    ![新增 gallerry 應用程式](./media/active-directory-saas-clicktime-tutorial/tic749322.png "新增 gallerry 應用程式")

6.  在**搜尋] 方塊**中，輸入**ClickTime**。

    ![應用程式組件庫](./media/active-directory-saas-clicktime-tutorial/tic777275.png "應用程式組件庫")

7.  在 [結果] 窗格中，選取**ClickTime**，，然後按一下要新增應用程式**完成**。

    ![ClickTime](./media/active-directory-saas-clicktime-tutorial/tic777276.png "ClickTime")

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>設定及測試 Azure AD 單一登入
在此區段中，您可以設定並使用 ClickTime 根據稱為 「 許承恩 」 的測試使用者測試 Azure AD 單一登入。

單一登入搭配使用，Azure AD 需要知道什麼對應中的使用者 ClickTime 使用者 Azure AD。 也就是說，必須建立連結和之間的關聯 Azure AD 使用者 ClickTime 中相關的使用者。

所指派**的使用者名稱**的值為 ClickTime**使用者名稱**的值 Azure AD 中建立此連結關聯。

若要設定及測試 Azure AD 單一登入與 ClickTime，必須完成下列建置組塊︰

1. **[Azure AD 設定單一登入](#configuring-azure-ad-single-sign-on)**-若要讓使用者能使用這項功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)**-Azure AD 單一登入與許承恩測試。
3. **[建立 ClickTime 測試使用者](#creating-a-clicktime-test-user)**-連結至 Azure AD 的表示她的 ClickTime 中有許承恩的對應。
4. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)**-啟用許承恩使用 Azure AD 單一登入。
5. **[測試單一登入](#testing-single-sign-on)**-驗證是否設定運作。


### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD 單一登入設定

本節的目標是大綱如何啟用使用者進行驗證其帳戶中使用根據 SAML 通訊協定的同盟 Azure AD ClickTime。  


>[AZURE.IMPORTANT] 才能設定單一登入您的 ClickTime 租用戶，您需要先連絡 ClickTime 技術支援，以取得啟用此功能。

**若要使用 ClickTime 設定 Azure AD 單一登入，請執行下列步驟︰**

1.  在 Azure 的傳統入口網站， **ClickTime**應用程式整合在頁面上，按一下 [**設定單一登入**以開啟 [**設定單一登入**] 對話方塊。

    ![啟用單一登入](./media/active-directory-saas-clicktime-tutorial/tic777277.png "啟用單一登入")

2.  在**您要如何登入 ClickTime 的使用者**] 頁面上，選取**Microsoft Azure AD 單一登入**，然後按 [**下一步**。

    ![設定單一登入](./media/active-directory-saas-clicktime-tutorial/tic777278.png "設定單一登入")

3. **設定應用程式設定**] 對話方塊在頁面上，執行下列步驟︰

    ![設定單一登入](./media/active-directory-saas-clicktime-tutorial/tic777286.png) 

    。 在 [ **IdentifierL** ] 文字方塊中，輸入 [使用下列模式的 URL: **https://app.clicktime.com/sp/**
    
    b。 在 [**回覆 URL** ] 文字方塊中，輸入 [使用下列模式的 URL: **https://app.clicktime.com/Login/**

    c。 按一下 [**下一步**

4.  在**設定單一登入 ClickTime 在**頁面上，若要下載您的憑證，按一下 [**下載憑證**] 並儲存在您的電腦上的憑證檔案。

    ![設定單一登入](./media/active-directory-saas-clicktime-tutorial/tic777279.png "設定單一登入")

4.  在不同的網頁瀏覽器視窗中，以系統管理員身分登入您 ClickTime 公司的網站。

5.  在工具列在頂端，按一下 [**喜好設定**]，然後按一下**安全性設定**。

6.  在**單一登入喜好設定**組態] 區段中，執行下列步驟︰

    ![安全性設定](./media/active-directory-saas-clicktime-tutorial/tic777280.png "安全性設定")

    。  選取 [**允許**登入**Azure AD**搭配使用單一登入 (SSO)。
    
    b。  Azure 傳統入口網站中，**設定單一登入，ClickTime** ] 對話方塊在頁面上，複製 [**單一登入服務 URL**的值，，然後再貼到**身分識別提供者端點**文字方塊。

    c。  開啟 64 基本編碼的憑證在**「 記事本 」**、 複製內容，然後再貼到**X.509 憑證**文字方塊。
    
    d。  按一下 [**儲存**]。

7.  在 Azure 傳統的入口網站中，選取單一登入設定確認，然後再按一下**完成**關閉 [**設定單一登入**] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-clicktime-tutorial/tic777281.png "設定單一登入")

##<a name="configuring-user-provisioning"></a>設定使用者佈建

若要啟用 Azure AD 使用者登入 ClickTime，他們必須佈建到 ClickTime。  
若是 ClickTime，佈建是手動的工作。

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>佈建使用者帳戶，請執行下列步驟︰

1.  登入您的**ClickTime**租用戶。

2.  在頂端上的工具列，按一下 [**公司**]，然後按一下**人員**。

    ![人員](./media/active-directory-saas-clicktime-tutorial/tic777282.png "人員")

3.  按一下 [**新增**]。

    ![新增人員](./media/active-directory-saas-clicktime-tutorial/tic777283.png "新增人員")

4.  在 [新增連絡人] 區段中，執行下列步驟︰

    ![人員](./media/active-directory-saas-clicktime-tutorial/tic777284.png "人員")

    。  在 [**電子郵件地址**] 文字方塊中，輸入您的 Azure AD 帳戶的電子郵件地址。
    
    b。  在 [**完整名稱**] 文字方塊中，輸入您 Azure AD 帳戶的名稱。  

    >[AZURE.NOTE] 如果您想要您可以設定新的 [人員] 物件的其他屬性。

    c。  按一下 [**儲存**]。

>[AZURE.NOTE] 您可以使用任何其他 ClickTime 使用者帳戶建立工具或 Api 提供 ClickTime 佈建 Azure AD 使用者帳戶。

### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在此區段中，您可以啟用許承恩 Azure 單一登入使用其存取權授與 ClickTime。

![為使用者指派][200]

若要測試您的設定，您需要授與 Azure AD 使用者您想要允許使用您的應用程式存取分派給他們。

**若要指定 ClickTime 許承恩，請執行下列步驟**

1. 在 [傳統] 入口網站，若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [**應用程式**頂端的功能表中。

    ![為使用者指派][201] 

2. 在應用程式清單中，選取 [ **ClickTime**]。

    ![設定單一登入](./media/active-directory-saas-clicktime-tutorial/tutorial_clicktime_50.png) 

3. 在頂端的功能表，按一下 [**使用者**]。

    ![為使用者指派][203]

4. 在 [使用者] 清單中，選取**許承恩**。

5. 在底部工具列中，按一下 [**指派**]。

    ![為使用者指派][205]

## <a name="testing-single-sign-on"></a>測試單一登入
在此區段中，您測試 Azure AD 單一登入設定使用 Access 面板。

當您按一下 [Access] 面板中的 [ClickTime] 方塊時，您應該取得自動登入 ClickTime 應用程式。


## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程的清單](active-directory-saas-tutorial-list.md)
* [什麼是應用程式存取和單一登入與 Azure Active Directory？](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[200]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_203.png
[205]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_205.png