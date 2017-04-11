<properties 
    pageTitle="教學課程︰ Azure Active Directory 整合 IdeaScale |Microsoft Azure" 
    description="瞭解如何使用 IdeaScale 與 Azure Active Directory 啟用單一登入、 自動化佈建和更多 ！" 
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
    ms.date="09/29/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-ideascale"></a>教學課程︰ 使用 IdeaScale 的 Azure Active Directory 整合
  
本教學課程中的目標是以顯示 Azure 和 IdeaScale 的整合。  
本教學課程中所述的案例假設您已經有下列項目︰

-   有效的 Azure 訂閱
-   IdeaScale 單一登入啟用訂閱
  
完成後本教學課程中，您指派給 IdeaScale Azure AD 使用者將能夠[存取面板簡介](active-directory-saas-access-panel-introduction.md)在應用程式中的單一登。
  
本教學課程中所述的案例是由下列建置組塊所組成︰

1.  啟用 IdeaScale 應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  將使用者指派

![案例](./media/active-directory-saas-ideascale-tutorial/IC790838.png "案例")
##<a name="enabling-the-application-integration-for-ideascale"></a>啟用 IdeaScale 應用程式整合
  
本節的目標是大綱如何啟用 IdeaScale 的整合應用程式。

###<a name="to-enable-the-application-integration-for-ideascale-perform-the-following-steps"></a>若要啟用的 IdeaScale 整合應用程式，請執行下列步驟︰

1.  Azure 傳統入口網站中，在左側的功能窗格中，按一下 [ **Active Directory**]。

    ![Active Directory](./media/active-directory-saas-ideascale-tutorial/IC700993.png "Active Directory")

2.  從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [在上方的功能表中的 [**應用程式**]。

    ![應用程式](./media/active-directory-saas-ideascale-tutorial/IC700994.png "應用程式")

4.  按一下 [**新增**頁面的底部。

    ![新增應用程式](./media/active-directory-saas-ideascale-tutorial/IC749321.png "新增應用程式")

5.  在 [**您想要做什麼**] 對話方塊中，按一下 [**新增應用程式，從圖庫**。

    ![新增 gallerry 應用程式](./media/active-directory-saas-ideascale-tutorial/IC749322.png "新增 gallerry 應用程式")

6.  在**搜尋] 方塊**中，輸入**IdeaScale**。

    ![應用程式組件庫](./media/active-directory-saas-ideascale-tutorial/IC790841.png "應用程式組件庫")

7.  在 [結果] 窗格中，選取**IdeaScale**，，然後按一下要新增應用程式**完成**。

    ![IdeaScale](./media/active-directory-saas-ideascale-tutorial/IC790842.png "IdeaScale")
##<a name="configuring-single-sign-on"></a>設定單一登入
  
本節的目標是大綱如何啟用使用者進行驗證其帳戶中使用根據 SAML 通訊協定的同盟 Azure AD IdeaScale。  
設定單一登入的 IdeaScale 需要您從憑證擷取指紋值。  
如果您不熟悉這個程序，請參閱[如何擷取的憑證指紋值](http://youtu.be/YKQF266SAxI)。

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要設定單一登入，請執行下列步驟︰

1.  在 Azure 的傳統入口網站， **IdeaScale**應用程式整合在頁面上，按一下 [**設定單一登入**以開啟 [**設定單一登入**] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-ideascale-tutorial/IC790843.png "設定單一登入")

2.  在**您要如何登入 IdeaScale 的使用者**] 頁面上，選取**Microsoft Azure AD 單一登入**，然後按 [**下一步**。

    ![設定單一登入](./media/active-directory-saas-ideascale-tutorial/IC790844.png "設定單一登入")

3.  **設定應用程式 URL**在頁面上， **IdeaScale 登入 URL** ] 文字方塊中，輸入您的使用者用來登入 IdeaScale 應用程式的 URL (例如: 「*https://company.IdeaScale.com*」)，然後按一下 [**下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-ideascale-tutorial/IC790845.png "設定應用程式 URL")

4.  在**設定單一登入 IdeaScale 在**頁面上，若要下載您的中繼資料，按一下**下載的中繼資料**]，然後儲存在本機電腦上的中繼資料檔案。

    ![設定單一登入](./media/active-directory-saas-ideascale-tutorial/IC790846.png "設定單一登入")

5.  在不同的網頁瀏覽器視窗中，以系統管理員身分登入您 IdeaScale 公司的網站。

6.  移至 [**社群設定]**。

    ![社群設定](./media/active-directory-saas-ideascale-tutorial/IC790847.png "社群設定")

7.  移至 [**安全性\>單一試用設定**。

    ![單一試用設定](./media/active-directory-saas-ideascale-tutorial/IC790848.png "單一試用設定")

8.  為**單一試用的類型**，選取 [ **SAML 2.0**]。

    ![單一試用類型](./media/active-directory-saas-ideascale-tutorial/IC790849.png "單一試用類型")

9.  在**單一試用設定**] 對話方塊中，執行下列步驟︰

    ![單一試用設定](./media/active-directory-saas-ideascale-tutorial/IC790850.png "單一試用設定")

    1.  Azure 傳統入口網站，在 [**設定單一登入在 IdeaScale**對話方塊] 頁面中的**實體識別碼**值，複製，然後再貼到 [ **SAML IdP 實體 ID** ] 文字方塊。
    2.  複製下載的中繼資料檔案的內容，然後將其貼入**SAML IdP 中繼資料**文字方塊。
    3.  在 Azure 的傳統入口網站中**設定單一登入，IdeaScale** ] 對話方塊在頁面上，複製 [**遠端登出 URL**的值，，然後再貼到 [**登出成功 URL** ] 文字方塊。
    4.  按一下 [**儲存變更**]。

10. 在 Azure 傳統的入口網站中，選取單一登入設定確認，然後再按一下**完成**關閉 [**設定單一登入**] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-ideascale-tutorial/IC790851.png "設定單一登入")
##<a name="configuring-user-provisioning"></a>設定使用者佈建
  
若要啟用 Azure AD 使用者登入 IdeaScale，他們必須佈建到 IdeaScale。  
若是 IdeaScale，佈建是手動的工作。

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>若要設定使用者佈建，執行下列步驟︰

1.  以系統管理員身分登入您**IdeaScale**公司的網站。

2.  移至 [**社群設定]**。

    ![社群設定](./media/active-directory-saas-ideascale-tutorial/IC790847.png "社群設定")

3.  移至 [**基本設定\>成員管理**。

4.  按一下 [**新增成員**]。

    ![成員管理](./media/active-directory-saas-ideascale-tutorial/IC790852.png "成員管理")

5.  在 [新增成員] 區段中，執行下列步驟︰

    ![加入新成員](./media/active-directory-saas-ideascale-tutorial/IC790853.png "加入新成員")

    1.  在 [**電子郵件地址**] 文字方塊中，輸入您想要佈建有效 AAD 帳戶的電子郵件地址。
    2.  按一下 [**儲存變更**]。

    >[AZURE.NOTE] Azure Active Directory 帳戶擁有者會收到確認帳戶，再會變成作用中連結的電子郵件。

>[AZURE.NOTE] 您可以使用任何其他 IdeaScale 使用者帳戶建立工具或 Api 提供 IdeaScale 佈建 AAD 使用者帳戶。

##<a name="assigning-users"></a>將使用者指派
  
若要測試您的設定，您需要授與 Azure AD 使用者您想要允許使用您的應用程式存取分派給他們。

###<a name="to-assign-users-to-ideascale-perform-the-following-steps"></a>若要指定 IdeaScale 使用者，請執行下列步驟︰

1.  在 Azure 傳統入口網站中建立測試帳戶。

2.  **IdeaScale**應用程式整合在頁面上，按一下 [**指派給使用者**。

    ![為使用者指派](./media/active-directory-saas-ideascale-tutorial/IC790854.png "為使用者指派")

3.  選取您測試的使用者、**指派**，請按一下 [，然後按一下**[是]**以確認您的工作分派。

    ![[是]](./media/active-directory-saas-ideascale-tutorial/IC767830.png "[是]")
  
如果您想要測試您的單一登入設定，開啟 [存取面板。 如需存取畫面的詳細資訊，請參閱[簡介存取面板](active-directory-saas-access-panel-introduction.md)。