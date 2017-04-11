<properties
    pageTitle="教學課程︰ Azure Active Directory 整合 Ceridian Dayforce HCM |Microsoft Azure"
    description="瞭解如何設定單一登入 Azure Active Directory 和 Ceridian Dayforce HCM 之間。"
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
    ms.date="09/01/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-ceridian-dayforce-hcm"></a>教學課程︰ 使用 Ceridian Dayforce HCM 的 Azure Active Directory 整合

本教學課程中的目標是以顯示您如何將整合 Ceridian Dayforce HCM 與 Azure Active Directory (Azure AD)。  
Azure AD 與整合 Ceridian Dayforce HCM 為您提供下列優點︰

- 您可以控制可存取 Ceridian Dayforce HCM Azure AD
- 您可以使用其 Azure AD 帳戶啟用自動取得登入 Ceridian Dayforce HCM （單一登入） 至使用者
- 您可以管理您的帳戶，在一個中央位置-Azure 傳統入口網站


如果您想要知道 Azure AD SaaS 應用程式整合相關的更多詳細資料，請參閱[什麼是應用程式存取和單一登入與 Azure Active Directory](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件

若要設定 Ceridian Dayforce HCM Azure AD 整合，您需要下列項目︰

- Azure AD 訂閱
- 啟用訂閱 Ceridian Dayforce HCM 單一正負號


> [AZURE.NOTE] 若要測試此教學課程中的步驟，我們不建議使用生產環境。


若要測試此教學課程中的步驟，您應該先按照這些建議︰

- 您應該使用生產環境，除非這是必要的。
- 如果您沒有安裝 Azure AD 試用環境，您可以在一個月試用[這裡](https://azure.microsoft.com/pricing/free-trial/)取得。


## <a name="scenario-description"></a>案例說明
本教學課程中的目的是讓您測試環境中測試 Azure AD 單一登入。  
本教學課程中所述的案例是由兩個主要的建置組塊所組成︰

1. 從圖庫新增 Ceridian Dayforce HCM
2. 設定及測試 Azure AD 單一登入


## <a name="adding-ceridian-dayforce-hcm-from-the-gallery"></a>從圖庫新增 Ceridian Dayforce HCM
若要設定 Ceridian Dayforce HCM 整合 Azure AD，必須將 Ceridian Dayforce HCM 從圖庫新增至您的受管理的 SaaS 應用程式清單。

**若要從圖庫新增 Ceridian Dayforce HCM，請執行下列步驟︰**

1. 在**Azure 傳統入口網站**，在左側的功能窗格中，按一下 [ **Active Directory**]。 

    ![Active Directory][1]

2. 從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3. 若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [在上方的功能表中的 [**應用程式**]。

    ![應用程式][2]

4. 按一下 [**新增**頁面的底部。

    ![應用程式][3]

5. 在 [**您想要做什麼**] 對話方塊中，按一下 [**新增應用程式，從圖庫**。

    ![應用程式][4]

6. 在 [搜尋] 方塊中，輸入**Ceridian Dayforce HCM**。

    ![應用程式](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_01.png)

7. 在 [結果] 窗格中，選取**Ceridian Dayforce HCM**，，然後按一下要新增應用程式**完成**。

    ![應用程式](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_07.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>設定及測試 Azure AD 單一登入
本節的目標是以顯示您如何設定和使用根據稱為 「 許承恩 」 的測試使用者 Ceridian Dayforce HCM 測試 Azure AD 單一登入。

單一登入搭配使用，Azure AD 需要知道什麼對應中的使用者 Ceridian Dayforce HCM Azure AD 中的使用者。 也就是說，必須建立連結和之間的關聯 Azure AD 使用者 Ceridian Dayforce HCM 中相關的使用者。

若要設定及測試 Azure AD 單一登入與 Ceridian Dayforce HCM，必須完成下列建置組塊︰

1. **[Azure AD 設定單一登入](#configuring-azure-ad-single-sign-on)**-若要讓使用者能使用這項功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)**-Azure AD 單一登入與許承恩測試。
4. **[建立 Ceridian Dayforce HCM 測試使用者](#creating-a-ceridian-dayforce-hcm-test-user)**-有對應的許承恩 Ceridian Dayforce HCM Azure AD 表示她的連結。
5. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)**-啟用許承恩使用 Azure AD 單一登入。
5. **[測試單一登入](#testing-single-sign-on)**-驗證是否設定運作。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD 單一登入設定

本節的目標是 Azure AD 單一登入 Azure 傳統入口網站中啟用並 Ceridian Dayforce HCM 應用程式中設定單一登入。

Ceridian Dayforce HCM 應用程式的期待 SAML 判斷提示以特定格式。 請使用 Dayforce HCM 小組第一次以識別正確的使用者識別碼。 Microsoft 建議使用做為使用者識別碼的**「 名稱 」**屬性。 您可以管理**「 Atrribute 」** ] 對話方塊的這個屬性的值。 以下螢幕擷取畫面會顯示這個範例。 

![設定單一登入](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_02.png) 

**若要使用 Ceridian Dayforce HCM 設定 Azure AD 單一登入，請執行下列步驟︰**




1. 在 Azure 的傳統入口網站中**Ceridian Dayforce HCM**應用程式整合在頁面上的功能表頂端，按一下 [**屬性**]。

    ![設定單一登入](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_81.png) 


1. 在屬性**saml 權杖屬性**] 清單中，選取名稱的屬性，然後再按一下 [**編輯**。

    ![設定單一登入](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_06.png) 


1. 在 [**編輯使用者屬性**] 對話方塊中，執行下列步驟︰
 
    。 **屬性值**清單中，選取您想要使用的您實作使用者屬性。  
    例如，如果您想要的員工作為專屬使用者識別碼，ExtensionAttribute2 中儲存屬性值，然後選取**user.extensionattribute2**。 

    b。 按一下 [**完成**]。  
    



1. 在頂端的功能表，按一下 [**快速啟動**]。

    ![設定單一登入](./media/active-directory-saas-hightail-tutorial/tutorial_general_83.png)  





1. 在 Azure 的傳統入口網站中**Ceridian Dayforce HCM**應用程式整合在頁面上，按一下 [**設定單一登入**。

    ![設定單一登入][6] 

2. 在**您要如何登入 Ceridian Dayforce HCM 的使用者**] 頁面上，選取**Azure AD 單一登入**，然後按 [**下一步**。

    ![設定單一登入](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_03.png) 

3. **設定應用程式設定**] 對話方塊在頁面上，執行下列步驟:。

    ![設定單一登入](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_04.png) 


    。 在 [**登入 URL** ] 文字方塊中，輸入您登入您的 Ceridian Dayforce HCM 應用程式的使用者所使用的 URL。 生產環境中，使用下列 URL 格式︰`https://sso.dayforcehcm.com/DayforcehcmNamespace`

    為避免混淆，取代 DayforcehcmNamespace 與您的環境，或您公司的識別碼; 命名空間例如︰`https://sso.dayforcehcm.com/contoso`
    
    測試環境中，使用下列 URL 格式︰`https://ssotest.dayforcehcm.com/DayforcehcmNamespace` 

    b。 在 [**回覆 URL** ] 文字方塊中輸入的 URL Azure AD 用於張貼回覆。  
    生產環境中，使用︰`https://ncpingfederate.dayforcehcm.com/sp/ACS.saml2`  
    測試環境中，使用︰`https://fs-test.dayforcehcm.com/sp/ACS.saml2`  
   

4. 在**設定單一登入 Ceridian Dayforce HCM 在**頁面上，執行下列步驟︰

    ![設定單一登入](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_05.png) 

    。 按一下 [**下載憑證**]，然後儲存您的電腦上的檔案。

    b。 按一下 [**下一步**]。


5. 若要取得 SSO 應用程式的設定，請連絡 Ceridian Dayforce HCM 支援小組透過電子郵件，並向他們提供下列︰

    - 下載的憑證檔案
    - **發行者 URL**
    - **SAML SSO URL** 
    - **單一登出服務 URL** 


6. 在 Azure 傳統的入口網站中，選取單一登入設定確認，然後再按 [**下一步**。

    ![Azure AD 單一登入][10]

7. 在**單一登入確認**] 頁面上，按一下 [**完成**]。  

    ![Azure AD 單一登入][11]



### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
本節的目標是稱為許承恩 Azure 傳統入口網站中建立的測試使用者。

![建立 Azure AD 使用者][20]

**若要建立的測試使用者 Azure AD 中，執行下列步驟︰**

1. 在**Azure 傳統入口網站**，在左側的功能窗格中，按一下 [ **Active Directory**]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-ceridiandayforcehcm-tutorial/create_aaduser_09.png) 

2. 從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3. 若要顯示的使用者清單頂端的功能表中，按一下 [**使用者**]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-ceridiandayforcehcm-tutorial/create_aaduser_03.png) 

4. 若要開啟 [**新增使用者**] 對話方塊底部的工具列中，按一下 [**新增使用者**]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-ceridiandayforcehcm-tutorial/create_aaduser_04.png) 

5. 在 [**告訴我們瞭解此使用者**的對話方塊頁面，請執行下列步驟︰

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-ceridiandayforcehcm-tutorial/create_aaduser_05.png) 

    。 為使用者類型，選取您組織中的 [新的使用者]。

    b。 在 [使用者名稱]**文字方塊**中，輸入**BrittaSimon**。

    c。 按一下 [**下一步**]。

6.  **使用者設定檔**] 對話方塊在頁面上，執行下列步驟︰

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-ceridiandayforcehcm-tutorial/create_aaduser_06.png) 

    。 在 [**名字**] 文字方塊中輸入**許**。  

    b。 在 [**姓氏**] 文字方塊中輸入，**承恩**。

    c。 在 [**顯示名稱**] 文字方塊中，輸入**許承恩**。

    d。 在 [**角色**] 清單中選取 [**使用者**]。

    e。 按一下 [**下一步**]。

7. 在 [**取得暫時密碼**] 對話方塊頁面上，按一下 [**建立**]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-ceridiandayforcehcm-tutorial/create_aaduser_07.png) 

8. **取得暫時密碼**] 對話方塊在頁面上，執行下列步驟︰

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-ceridiandayforcehcm-tutorial/create_aaduser_08.png) 

    。 寫下的值的**新密碼**。

    b。 按一下 [**完成**]。   



### <a name="creating-a-ceridian-dayforce-hcm-test-user"></a>建立 Ceridian Dayforce HCM 測試使用者

本節的目標是以建立 Ceridian Dayforce HCM 中名為許承恩使用者。 

請使用 Ceridian Dayforce HCM 的支援小組，為使用者新增至您在 Ceridian Dayforce HCM 應用程式。 





### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

本節的目標是啟用許承恩 Azure 單一登入使用其存取權授與 Ceridian Dayforce HCM。

![為使用者指派][200] 

**若要指定 Ceridian Dayforce HCM 許承恩，請執行下列步驟︰**

1. Azure 傳統的入口網站中，若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [**應用程式**頂端的功能表中。

    ![為使用者指派][201] 

2. 在應用程式清單中，選取 [ **Ceridian Dayforce HCM**]。

    ![設定單一登入](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_50.png) 

1. 在頂端的功能表，按一下 [**使用者**]。

    ![為使用者指派][203] 

1. 在 [使用者] 清單中，選取**許承恩**。

2. 在底部工具列中，按一下 [**指派**]。

    ![為使用者指派][205]



### <a name="testing-single-sign-on"></a>測試單一登入

本節的目標是測試 Azure AD 單一登入設定使用 Access 面板。  
當您按一下 [Access] 面板中的 [Ceridian Dayforce HCM] 方塊時，您應該取得自動登入 Ceridian Dayforce HCM 應用程式。


## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程的清單](active-directory-saas-tutorial-list.md)
* [什麼是應用程式存取和單一登入與 Azure Active Directory？](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_205.png
