<properties
    pageTitle="教學課程︰ Azure Active Directory 整合 ServiceNow 與 ServiceNow Express |Microsoft Azure"
    description="瞭解如何設定單一登入 Azure Active Directory 和 ServiceNow 和 ServiceNow Express 之間。"
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
    ms.date="10/27/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-servicenow-and-servicenow-express"></a>教學課程︰ Azure Active Directory 整合 ServiceNow 與 ServiceNow Express。


在本教學課程中，您可以瞭解如何與 Azure Active Directory (Azure AD) 整合 ServiceNow 和 ServiceNow Express。

Azure AD 整合 ServiceNow 和 ServiceNow Express 為您提供下列優點︰

- 您可以控制可存取 ServiceNow 和 ServiceNow Express Azure AD
- 您可以讓使用者能自動取得登入，ServiceNow 和 ServiceNow Express （單一登入） Azure AD 帳戶
- 您可以管理您的帳戶，在一個中央位置-Azure 傳統入口網站

如果您想要知道 Azure AD SaaS 應用程式整合相關的更多詳細資料，請參閱[什麼是應用程式存取和單一登入與 Azure Active Directory](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 整合 ServiceNow 與 ServiceNow Express，您需要下列項目︰

- Azure AD 訂閱
- ServiceNow、 執行個體或租用戶的 ServiceNow，加拿大版本或更新版本
- ServiceNow 快速，ServiceNow Express，赫爾辛基版本的執行個體或更新版本
- ServiceNow 租用戶必須啟用[多個提供者單一登入外掛程式](http://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0)。 這可以提交服務要求在<https://hi.service-now.com/> 


> [AZURE.NOTE] 若要測試此教學課程中的步驟，我們不建議使用生產環境。


若要測試此教學課程中的步驟，您應該先按照這些建議︰

- 您應該使用生產環境，除非這是必要的。
- 如果您沒有安裝 Azure AD 試用環境，您可以在一個月試用[這裡](https://azure.microsoft.com/pricing/free-trial/)取得。


## <a name="scenario-description"></a>案例說明
在本教學課程中，您測試 Azure AD 單一登入的測試環境中。 本教學課程中所述的案例是由兩個主要的建置組塊所組成︰

1. 從圖庫新增 ServiceNow
2. 設定及測試 Azure AD 單一登入的 ServiceNow 或 ServiceNow Express


## <a name="adding-servicenow-from-the-gallery"></a>從圖庫新增 ServiceNow
若要設定的 ServiceNow 或 ServiceNow Express 整合到 Azure AD，您需要從庫新增 ServiceNow 到受管理的 SaaS 應用程式的清單。 

**若要從圖庫新增 ServiceNow，請執行下列步驟︰**

1. 在**Azure 傳統入口網站**，在左側的功能窗格中，按一下 [ **Active Directory**]。 

    ![Active Directory][1]

2. 從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3. 若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [在上方的功能表中的 [**應用程式**]。

    ![應用程式][2]

4. 按一下 [**新增**頁面的底部。

    ![應用程式][3]

5. 在 [**您想要做什麼**] 對話方塊中，按一下 [**新增應用程式，從圖庫**。

    ![應用程式][4]

6. 在 [搜尋] 方塊中，輸入**ServiceNow**。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_01.png)

7. 在 [結果] 窗格中，選取**ServiceNow**，，然後按一下要新增應用程式**完成**。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>設定及測試 Azure AD 單一登入
在此區段中，您設定和測試 Azure AD 單一登入有 ServiceNow 或 ServiceNow Express 根據測試使用者稱為 「 許承恩 」。

單一登入搭配使用，Azure AD 需要知道什麼對應中的使用者 ServiceNow 使用者 Azure AD。 也就是說，必須建立連結和之間的關聯 Azure AD 使用者 ServiceNow 中相關的使用者。
所指派**的使用者名稱**的值為 ServiceNow**使用者名稱**的值 Azure AD 中建立此連結關聯。 若要設定及測試 Azure AD 單一登入與 ServiceNow，必須完成下列建置組塊︰

1. **[Azure AD 設定單一登入的 ServiceNow](#configuring-azure-ad-single-sign-on-for-servicenow)** -若要讓使用者能使用這項功能。
2. **[Azure AD 設定單一登入 ServiceNow 快速](#configuring-azure-ad-single-sign-on-for-servicenow-express)**-若要讓使用者能使用這項功能。
3. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)**-Azure AD 單一登入與許承恩測試。
4. **[建立 ServiceNow 測試使用者](#creating-a-servicenow-test-user)**-連結至 Azure AD 的表示她的 ServiceNow 中有許承恩的對應。
5. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)**-啟用許承恩使用 Azure AD 單一登入。
6. **[測試單一登入](#testing-single-sign-on)**-驗證是否設定運作。

> [AZURE.NOTE] 如果您想要設定 ServiceNow 省略步驟 2。 同樣地，如果您想要設定 ServiceNow Express 省略步驟 1。

### <a name="configuring-azure-ad-single-sign-on-for-servicenow"></a>設定 Azure AD 單一登入 ServiceNow

1.  Azure AD 傳統入口網站中，在**ServiceNow**應用程式整合頁面上，按一下 [**設定單一登入**以開啟 [**設定單一登入**對話方塊]。

    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/IC749323.png "設定單一登入")

2.  在**您要如何登入 ServiceNow 的使用者**] 頁面上，選取**Microsoft Azure AD 單一登入**，然後按 [**下一步**。

    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/IC749324.png "設定單一登入")

3.  在 [**設定應用程式設定**] 頁面上執行下列步驟︰

    ![設定應用程式 URL](./media/active-directory-saas-servicenow-tutorial/IC769497.png "設定應用程式 URL")

    。 在 [ **ServiceNow 登入 URL** ] 文字方塊中，輸入您登入您追蹤的圖樣的 ServiceNow 應用程式使用者所使用的 URL: `https://<instance-name>.service-now.com`。

    b。 在 [**識別碼**] 文字方塊中，輸入您用於您的使用者登入您追蹤的圖樣的 ServiceNow 應用程式的 URL: `https://<instance-name>.service-now.com`。

    c。 按一下 [**下一步**

4.  若要自動設定以 SAML 為基礎的驗證 ServiceNow Azure AD，請在**自動設定單一登入**表單中輸入您的 ServiceNow 執行個體名稱、 管理員的使用者名稱和管理員密碼，然後按一下 [*設定*]。 請注意，提供管理員的使用者名稱必須**security_admin**角色指派此工作的 ServiceNow。 否則，若要手動設定 ServiceNow Azure AD 作為 SAML 身分識別提供者，請按一下 [**手動設定單一登入的應用程式**，請按一下 [**下一步**，完成下列步驟。

    ![設定應用程式 URL](./media/active-directory-saas-servicenow-tutorial/IC7694971.png "設定應用程式 URL")



5.  在**設定單一登入 ServiceNow 在**頁面上，按一下 [**下載憑證**，儲存在本機電腦上的憑證檔案。

    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/IC749325.png "設定單一登入")

1. 登入以系統管理員身分 ServiceNow 應用程式。
2. 下一個步驟，即可啟動_整合-多個提供者單一登入安裝程式_外掛程式︰

    。 在左側功能窗格] 移至**系統定義**] 區段，然後按一下 [**增益集**。

    ![設定應用程式 URL](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_03.png "啟動外掛程式")
    
    b。 搜尋_整合-多個提供者單一登入安裝程式_]。

    ![設定應用程式 URL](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_04.png "啟動外掛程式")

    c。 選取 [增益集]。 Rigth 按一下，然後選取 [**啟動或升級**。
    
    d。 按一下 [**啟動**] 按鈕。

2. 在左側功能窗格] 中按一下 [**內容**]。  

    ![設定應用程式 URL](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_06.png "設定應用程式 URL")


3. 在**多個提供者 SSO 屬性**] 對話方塊中，執行下列步驟︰

    ![設定應用程式 URL](./media/active-directory-saas-servicenow-tutorial/IC7694981.png "設定應用程式 URL")

    。 為**啟用多個提供者 SSO**，請選取 [**是**]。

    b。 為**啟用偵錯記錄有多個提供者 SSO 整合**，請選取 [**是**]。

    c。 在**[使用者] 欄位的表格的...** ] 文字方塊中輸入**使用者名稱**。

    d。 按一下 [**儲存**]。



1. 按一下 [在左側功能窗格] 中的 [ **x509 憑證**]。

    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_05.png "設定單一登入")


1. 在 [ **X.509 憑證**] 對話方塊中，[**新增**]。

    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/IC7694974.png "設定單一登入")


1. 在 [ **X.509 憑證**] 對話方塊中，執行下列步驟︰

    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/IC7694975.png "設定單一登入")

    。 按一下 [**新增**]。

    b。 在 [**名稱**] 文字方塊中，輸入您設定的名稱 (例如︰ **TestSAML2.0**)。

    c。 選取 [**作用中**]。

    d。 [**格式**] 中，選取**PEM**。

    e。 為**類型**，選取 [**允許儲存憑證**]。
    
    f。 在記事本中開啟您 Base64 編碼的憑證、 將其內容複製到剪貼簿，並貼到**PEM 憑證**文字方塊。

    g。 按一下 [**更新**]。


1. 在左側功能窗格] 中按一下 [**身分識別提供者**。

    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_07.png "設定單一登入")

1. 在 [**身分識別提供者**] 對話方塊中，按一下 [**新增**︰

    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/IC7694977.png "設定單一登入")


1. 在 [**身分識別提供者**] 對話方塊中，按一下 [ **SAML2 Update1？**:

    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/IC7694978.png "設定單一登入")


1. 在 [SAML2 Update1 屬性] 對話方塊中，執行下列步驟︰

    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/IC7694982.png "設定單一登入")


    。 在 [**名稱**] 文字方塊中，輸入您設定的名稱 (例如︰ **SAML 2.0**)。

    b。 在 [**使用者] 欄位**文字方塊、 輸入**電子郵件**或**user_id**，依據欄位用來識別您 ServiceNow 部署中的使用者。 
    
    > [AZURE.NOTE] 您可以唯一識別項在 SAML 權杖發出 Azure AD 使用者識別碼 （使用者主體名稱） 或電子郵件地址，移至 configue Azure AD **ServiceNow > 屬性 > 單一登入**的 Azure 傳統入口網站，並將所要的欄位對應至**nameidentifier**屬性] 區段。 Azure AD （例如使用者主體名稱） 中儲存的選取的屬性的值必須符合儲存 ServiceNow 中的 [輸入] 欄位 (例如 user_id) 的值

    c。 在 Azure AD 傳統入口網站中的**身分識別提供者識別碼**值，複製，然後再貼到 [**身分識別提供者 URL** ] 文字方塊。

    d。 在 Azure AD 傳統入口網站中**驗證要求 URL**值，複製，然後再貼到**身分識別提供者 AuthnRequest**文字方塊。

    e。 Azure AD 傳統入口網站中，在**單一 Sign-Out 服務 URL**值，複製，然後再貼到**身分識別提供者 SingleLogoutRequest**文字方塊。

    f。 在 [ **ServiceNow 首頁**] 文字方塊中，輸入您 ServiceNow 的執行個體首頁的 URL。

    > [AZURE.NOTE] ServiceNow 執行個體首頁是**ServieNow 租用戶 URL**及**/navpage.do**串連 (例如︰`https://fabrikam.service-now.com/navpage.do`)。
 

    g。 在 [**實體識別碼 / 發行者**] 文字方塊中，輸入您的 ServiceNow 租用戶的 URL。

    h。 在 [**對象 URL** ] 文字方塊中，輸入您 ServiceNow 租用戶的 URL。 

    我。 在 [**通訊協定繫結 IDP SingleLogoutRequest** ] 文字方塊中，輸入**urn: oasis︰ 名稱︰ tc: SAML:2.0:bindings:HTTP-重新導向**。

    j。 在 [NameID 原則] 文字方塊中，輸入**urn: oasis︰ 名稱︰ tc: SAML:1.1:nameid-格式︰ 未指定**。

    k。 取消選取 [**建立 AuthnContextClass**。

    l。 在**AuthnContextClassRef 方法**中，輸入`http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password`。 這只需要如果您是雲端只組織。 如果您使用內部部署 ADFS 或 MFA 驗證您應該未設定此值。 

    m。 在 [**時鐘扭曲**的文字方塊中輸入**60**。

    n。 為**單一登入指令碼**，請選取 [ **MultiSSO_SAML2_Update1**]。

    o。 為**x509 憑證**，選取您在上一個步驟中所建立的憑證。

    p。 按一下 [**送出**]。 



6. 在 Azure AD 傳統入口網站中，選取單一登入設定確認，然後再按 [**下一步**。 

    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/IC7694990.png "設定單一登入")

7. 在**單一登入確認**] 頁面上，按一下 [**完成**]。
 
    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/IC7694991.png "設定單一登入")

### <a name="configuring-azure-ad-single-sign-on-for-servicenow-express"></a>ServiceNow 快速設定 Azure AD 單一登入

1.  Azure AD 傳統入口網站中，在**ServiceNow**應用程式整合頁面上，按一下 [**設定單一登入**以開啟 [**設定單一登入**對話方塊]。

    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/IC749323.png "設定單一登入")

2.  在**您要如何登入 ServiceNow 的使用者**] 頁面上，選取**Microsoft Azure AD 單一登入**，然後按 [**下一步**。

    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/IC749324.png "設定單一登入")

3.  在 [**設定應用程式設定**] 頁面上執行下列步驟︰

    ![設定應用程式 URL](./media/active-directory-saas-servicenow-tutorial/IC769497.png "設定應用程式 URL")

    。 在 [ **ServiceNow 登入 URL** ] 文字方塊中，輸入您登入您追蹤的圖樣的 ServiceNow 應用程式使用者所使用的 URL: `https://<instance-name>.service-now.com`。

    b。 在 [**簽發者 URL** ] 文字方塊中，輸入您用於您的使用者登入您追蹤的圖樣的 ServiceNow 應用程式的 URL `https://<instance-name>.service-now.com`。

    c。 按一下 [**下一步**

4.  按一下 [**手動設定單一登入的應用程式**，然後按一下 [**下一步**，請完成下列步驟。

    ![設定應用程式 URL](./media/active-directory-saas-servicenow-tutorial/IC7694971.png "設定應用程式 URL")

5.  在**設定單一登入 ServiceNow 在**頁面上，按一下 [**下載憑證**，儲存在本機電腦上的憑證檔案，然後按 [**下一步**。

    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/IC749325.png "設定單一登入")

6. 登入以系統管理員身分 ServiceNow Express 應用程式。

7. 在左側功能窗格] 中按一下**單一登入**。  

    ![設定應用程式 URL](./media/active-directory-saas-servicenow-tutorial/ic7694980ex.png "設定應用程式 URL")


8. 在**單一登入**] 對話方塊中，按一下右上方的 [設定] 圖示，並設定下列屬性︰

    ![設定應用程式 URL](./media/active-directory-saas-servicenow-tutorial/ic7694981ex.png "設定應用程式 URL")

    。 切換**啟用多個提供者 SSO**至右側。

    b。 切換**啟用偵錯記錄的多個的提供者 SSO 整合**至右側。

    c。 在**[使用者] 欄位的表格的...** ] 文字方塊中輸入**使用者名稱**。


9. 在**單一登入**] 對話方塊中，按一下 [**新增新的憑證**]。

    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/ic7694973ex.png "設定單一登入")



10. 在 [ **X.509 憑證**] 對話方塊中，執行下列步驟︰

    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/IC7694975.png "設定單一登入")

    。 在 [**名稱**] 文字方塊中，輸入您設定的名稱 (例如︰ **TestSAML2.0**)。

    b。 選取 [**作用中**]。

    c。 [**格式**] 中，選取**PEM**。

    d。 為**類型**，選取 [**允許儲存憑證**]。

    e。 建立 Base64 編碼檔案從您下載的憑證。
   
    > [AZURE.NOTE] 如需詳細資訊，請參閱[如何將轉換成文字檔的二進位憑證](http://youtu.be/PlgrzUZ-Y1o)。
    
    f。 在記事本中開啟您 Base64 編碼的憑證、 將其內容複製到剪貼簿，並貼到**PEM 憑證**文字方塊。

    g。 按一下 [**更新**]。


11. 在 [**單一登入**] 對話方塊中，按一下 [**新增新 IdP**]。

    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/ic7694976ex.png "設定單一登入")


12. 在 [**新增新的身分識別提供者**對話方塊的 [**設定身分識別提供者**，請執行下列步驟︰

    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/ic7694982ex.png "設定單一登入")


    。 在 [**名稱**] 文字方塊中，輸入您設定的名稱 (例如︰ **SAML 2.0**)。

    b。 在 Azure AD 傳統入口網站中的**身分識別提供者識別碼**值，複製，然後再貼到 [**身分識別提供者 URL** ] 文字方塊。

    c。 在 Azure AD 傳統入口網站中**驗證要求 URL**值，複製，然後再貼到**身分識別提供者 AuthnRequest**文字方塊。

    d。 Azure AD 傳統入口網站中，在**單一 Sign-Out 服務 URL**值，複製，然後再貼到**身分識別提供者 SingleLogoutRequest**文字方塊。

    e。 為**身分識別提供者的憑證**，選取 [上一個步驟中建立的憑證]。


13. 按一下 [**進階設定**]，並在**其他的身分識別提供者屬性**] 底下執行下列步驟︰

    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/ic7694983ex.png "設定單一登入")

    。 在 [**通訊協定繫結 IDP SingleLogoutRequest** ] 文字方塊中，輸入**urn: oasis︰ 名稱︰ tc: SAML:2.0:bindings:HTTP-重新導向**。

    b。 在 [ **NameID 原則**] 文字方塊中，輸入**urn: oasis︰ 名稱︰ tc: SAML:1.1:nameid-格式︰ 未指定**。    

    c。 在**AuthnContextClassRef 方法**中，輸入**http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password**。
    
    d。 取消選取 [**建立 AuthnContextClass**。

14. 在 [**其他服務提供者屬性**] 中，執行下列步驟︰

    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/ic7694984ex.png "設定單一登入")

    。 在 [ **ServiceNow 首頁**] 文字方塊中，輸入您 ServiceNow 的執行個體首頁的 URL。

    > [AZURE.NOTE] ServiceNow 執行個體首頁是**ServieNow 租用戶 URL**及**/navpage.do**串連 (例如︰ `https://fabrikam.service-now.com/navpage.do`)。

    b。 在 [**實體識別碼 / 發行者**] 文字方塊中，輸入您的 ServiceNow 租用戶的 URL。

    c。 在 [**對象 URI** ] 文字方塊中，輸入您 ServiceNow 租用戶的 URL。 

    d。 在 [**時鐘扭曲**的文字方塊中輸入**60**。

    e。 在 [**使用者] 欄位**文字方塊、 輸入**電子郵件**或**user_id**，依據欄位用來識別您 ServiceNow 部署中的使用者。
    
    > [AZURE.NOTE] 您可以唯一識別項在 SAML 權杖發出 Azure AD 使用者識別碼 （使用者主體名稱） 或電子郵件地址，移至 configue Azure AD **ServiceNow > 屬性 > 單一登入**的 Azure 傳統入口網站，並將所要的欄位對應至**nameidentifier**屬性] 區段。 Azure AD （例如使用者主體名稱） 中儲存的選取的屬性的值必須符合儲存 ServiceNow 中的 [輸入] 欄位 (例如 user_id) 的值

    f。 按一下 [**儲存**]。 


15. 在 Azure AD 傳統入口網站中，選取單一登入設定確認，然後再按 [**下一步**。 

    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/IC7694990.png "設定單一登入")

16. 在**單一登入確認**] 頁面上，按一下 [**完成**]。
 
    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/IC7694991.png "設定單一登入")

##<a name="configuring-user-provisioning"></a>設定使用者佈建
  
本節的目標是大綱如何啟用使用者佈建 Active Directory 使用者帳戶，以便 ServiceNow。


### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>若要設定使用者佈建，執行下列步驟︰

1. Azure 管理傳統入口網站中，在**ServiceNow**應用程式整合頁面上，按一下 [**設定使用者佈建**]。 

    ![使用者佈建](./media/active-directory-saas-servicenow-tutorial/IC769498.png "使用者佈建")


2. **輸入您的 ServiceNow 認證，以啟用自動使用者佈建**在頁面上，提供下列設定的設定︰ 設定使用者佈建 

     。 在 [ **ServiceNow 執行個體名稱**] 文字方塊中，輸入 ServiceNow 執行個體名稱。

     b。 在 [ **ServiceNow 管理員的使用者名稱**] 文字方塊中輸入 ServiceNow 管理員帳戶的名稱。

     c。 在 [ **ServiceNow 管理員密碼**] 文字方塊中，輸入此帳戶的密碼。

     d。 按一下 [**驗證**以驗證您的設定]。

     e。 按一下 [**下一步**] 按鈕以開啟 [**下一步**] 頁面。

     f。 如果您要提供此應用程式的所有使用者，請選取 「 都**自動佈都建到這個應用程式目錄中的所有使用者帳戶**]。 

    ![後續步驟](./media/active-directory-saas-servicenow-tutorial/IC698804.png "後續步驟")

     g。 在 [**下一步**] 頁面上，按一下 [**完成**，儲存您的設定]。

### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
在此區段中，您可以建立在傳統入口網站中名為許承恩測試使用者。

![建立 Azure AD 使用者][20]

**若要建立的測試使用者 Azure AD 中，執行下列步驟︰**

1. 在**Azure 傳統入口網站**，在左側的功能窗格中，按一下 [ **Active Directory**]。
    
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-servicenow-tutorial/create_aaduser_09.png) 

2. 從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3. 若要顯示的使用者清單頂端的功能表中，按一下 [**使用者**]。
    
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-servicenow-tutorial/create_aaduser_03.png) 

4. 若要開啟 [**新增使用者**] 對話方塊底部的工具列中，按一下 [**新增使用者**]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-servicenow-tutorial/create_aaduser_04.png) 

5. 在 [**告訴我們瞭解此使用者**的對話方塊頁面，請執行下列步驟︰
 
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-servicenow-tutorial/create_aaduser_05.png) 

    。 為使用者類型，選取您組織中的 [新的使用者]。

    b。 在 [使用者名稱]**文字方塊**中，輸入**BrittaSimon**。

    c。 按一下 [**下一步**]。

6.  **使用者設定檔**] 對話方塊在頁面上，執行下列步驟︰

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-servicenow-tutorial/create_aaduser_06.png) 

    。 在 [**名字**] 文字方塊中輸入**許**。  

    b。 在 [**姓氏**] 文字方塊中輸入，**承恩**。

    c。 在 [**顯示名稱**] 文字方塊中，輸入**許承恩**。

    d。 在 [**角色**] 清單中選取 [**使用者**]。

    e。 按一下 [**下一步**]。

7. 在 [**取得暫時密碼**] 對話方塊頁面上，按一下 [**建立**]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-servicenow-tutorial/create_aaduser_07.png) 

8. **取得暫時密碼**] 對話方塊在頁面上，執行下列步驟︰

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-servicenow-tutorial/create_aaduser_08.png) 

    。 寫下的值的**新密碼**。

    b。 按一下 [**完成**]。   


### <a name="creating-a-servicenow-test-user"></a>建立 ServiceNow 測試使用者

在此區段中，您可以建立 ServiceNow 中名為許承恩使用者。 在此區段中，您可以建立 ServiceNow 中名為許承恩使用者。 如果您不知道如何 ServiceNow 或 ServiceNow Express 帳戶中新增使用者，請連絡 ServiceNow 支援小組。

### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在此區段中，您可以啟用許承恩 Azure 單一登入使用其存取權授與 ServiceNow。

![為使用者指派][200] 

**若要指定 ServiceNow 許承恩，請執行下列步驟︰**

1. 在 [傳統] 入口網站，若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [**應用程式**頂端的功能表中。

    ![為使用者指派][201] 

2. 在應用程式清單中，選取 [ **ServiceNow**]。

    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_10.png) 

1. 在頂端的功能表，按一下 [**使用者**]。

    ![為使用者指派][203] 

1. 在 [所有使用者] 清單中，選取**許承恩**。

2. 在底部工具列中，按一下 [**指派**]。

    ![為使用者指派][205]


### <a name="testing-single-sign-on"></a>測試單一登入

本節的目標是測試 Azure AD 單一登入設定使用 Access 面板。

當您按一下 [Access] 面板中的 [ServiceNow] 方塊時，您應該取得自動登入 ServiceNow 應用程式。

## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程的清單](active-directory-saas-tutorial-list.md)
* [什麼是應用程式存取和單一登入與 Azure Active Directory？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-servicenow-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_205.png
