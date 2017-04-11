<properties
    pageTitle="教學課程︰ Azure Active Directory 整合 Novatus |Microsoft Azure"
    description="瞭解如何設定單一登入 Azure Active Directory 和 LearnUpon 之間。"
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
    ms.date="08/15/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-learnupon"></a>教學課程︰ 使用 LearnUpon 的 Azure Active Directory 整合

本教學課程中的目標是以顯示您如何將整合 LearnUpon 與 Azure Active Directory (Azure AD)。  
Azure AD 與整合 LearnUpon 為您提供下列優點︰

- 您可以控制可存取 LearnUpon Azure AD
- 您可以讓使用者能自動取得登入 LearnUpon （單一登入） 至其 Azure AD 帳戶
- 您可以管理您的帳戶，在一個中央位置-傳統的 Azure Active Directory 


如果您想要知道 Azure AD SaaS 應用程式整合相關的更多詳細資料，請參閱[什麼是應用程式存取和單一登入與 Azure Active Directory](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件

若要設定 LearnUpon Azure AD 整合，您需要下列項目︰

- Azure AD 訂閱
- 啟用訂閱 LearnUpon 單一正負號


> [AZURE.NOTE] 若要測試此教學課程中的步驟，我們不建議使用生產環境。


若要測試此教學課程中的步驟，您應該先按照這些建議︰

- 您應該使用生產環境，除非這是必要的。
- 如果您沒有安裝 Azure AD 試用環境，您可以在一個月試用[這裡](https://azure.microsoft.com/pricing/free-trial/)取得。


## <a name="scenario-description"></a>案例說明
本教學課程中的目的是讓您測試環境中測試 Azure AD 單一登入。  
本教學課程中所述的案例是由兩個主要的建置組塊所組成︰

1. 從圖庫新增 LearnUpon
2. 設定及測試 Azure AD 單一登入


## <a name="adding-learnupon-from-the-gallery"></a>從圖庫新增 LearnUpon
若要設定 LearnUpon 整合 Azure AD，您需要從庫新增 LearnUpon 到受管理的 SaaS 應用程式的清單。

**若要從圖庫新增 LearnUpon，請執行下列步驟︰**

1. 在**Azure 傳統入口網站**，在左側的功能窗格中，按一下 [ **Active Directory**]。 

    ![Active Directory][1]

2. 從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3. 若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [在上方的功能表中的 [**應用程式**]。

    ![應用程式][2]

4. 按一下 [**新增**頁面的底部。

    ![應用程式][3]

5. 在 [**您想要做什麼**] 對話方塊中，按一下 [**新增應用程式，從圖庫**。

    ![應用程式][4]

6. 在 [搜尋] 方塊中，輸入**LearnUpon**。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_01.png)

7. 在 [結果] 窗格中，選取**LearnUpon**，，然後按一下要新增應用程式**完成**。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>設定及測試 Azure AD 單一登入
本節的目標是以顯示您如何設定和使用 LearnUpon 根據稱為 「 許承恩 」 的測試使用者測試 Azure AD 單一登入。

單一登入搭配使用，Azure AD 需要知道什麼對應中的使用者 LearnUpon Azure AD 中的使用者。 也就是說，必須建立連結和之間的關聯 Azure AD 使用者 LearnUpon 中相關的使用者。  
所指派**的使用者名稱**的值為 LearnUpon**使用者名稱**的值 Azure AD 中建立此連結關聯。

若要設定及測試 Azure AD 單一登入與 LearnUpon，必須完成下列建置組塊︰

1. **[Azure AD 設定單一登入](#configuring-azure-ad-single-single-sign-on)**-若要讓使用者能使用這項功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)**-Azure AD 單一登入與許承恩測試。
4. **[建立 LearnUpon 測試使用者](#creating-a-learnupon-test-user)**-連結至 Azure AD 的表示她的 LearnUpon 中有許承恩的對應。
5. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)**-啟用許承恩使用 Azure AD 單一登入。
5. **[測試單一登入](#testing-single-sign-on)**-驗證是否設定運作。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD 單一登入設定

本節的目標是 Azure AD 單一登入 Azure 傳統入口網站中啟用並 LearnUpon 應用程式中設定單一登入。



**若要使用 LearnUpon 設定 Azure AD 單一登入，請執行下列步驟︰**

1. 在 Azure 的傳統入口網站， **LearnUpon**應用程式整合在頁面上，按一下 [**設定單一登入**以開啟 [**設定單一登入**] 對話方塊。

    ![設定單一登入][6] 

2. 在**您要如何登入 LearnUpon 的使用者**] 頁面上，選取**Azure AD 單一登入**，然後按 [**下一步**。

    ![設定單一登入](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_03.png) 

3. **設定應用程式設定**] 對話方塊在頁面上，執行下列步驟:。

    ![設定單一登入](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_04.png) 


    。 在 [**回覆 URL** ] 文字方塊中輸入的判斷提示消費者服務 URL 使用下列模式︰`https://\<companyname\>.learnupon.com/saml/consumer`

    b。 按一下 [**下一步**]。 


4. 在**設定單一登入 LearnUpon 在**頁面上，執行下列步驟︰

    ![設定單一登入](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_05.png) 

    。 按一下 [**下載憑證**]，然後儲存您的電腦上的檔案。 我們需要此憑證和中繼資料 Url （實體識別碼、 SSO 登入 URL 和登出 URL） 設定 SSO LearnUpon 側。

    b。 按一下 [**下一步**]。




1. 開啟另一個瀏覽器執行個體並登入到 LearnUpon 以系統管理員帳戶。 

1. 按一下 [**設定**] 索引標籤。

    ![設定單一登入](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_06.png) 


1. **單一登入-SAML**，按一下，然後按一下 [**一般設定**]，以設定 SAML 設定。

    ![設定單一登入](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_07.png) 


5. 在 [**一般設定**] 區段中，執行下列步驟︰

    ![設定單一登入](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_08.png) 

    。 選取 [**啟用**]。

    b。 為**版本**，請選取 [ **2.0**]。

    c。 **略過條件**，為選取 [**否**]。

    d。 在 [ **SAML 權杖張貼參數名稱**] 文字方塊中，要求文章參數 SAML 消費者 url 名稱上述的類型會包含 SAML 宣告，來驗證並驗證-例如**SAMLResponse**。

    e。 在 [**姓名識別碼格式**] 文字方塊中，輸入值，指出您 SAML 判斷提示中的使用者識別碼 （電子郵件地址） 所在-例如**urn: oasis︰ 名稱︰ tc: SAML:1.1:nameid-格式︰ 電子郵件地址**。

    f。 在 [**識別提供者位置**文字方塊中輸入值，表示位置只要按您上傳的圖示，從 Azure 傳統的入口網站登入畫面上，使用者就會傳送到。

    g.in Azure 傳統的入口網站中，複製**單一 Sign-Out 服務的 URL**，，然後將其貼入**登出 URL** textbos。

    h。 按一下 [**管理手指列印**，，然後上傳您的下載憑證根手指列印。 


1. 按一下 [**使用者設定**]，然後執行下列步驟︰

    ![設定單一登入](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_11.png) 

    。 在**第一個名稱識別碼格式**] 文字方塊中輸入 [告訴我們您 SAML 判斷提示中的使用者名字所在-例如的值︰ **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/ givenname**。

    b。 在**最後一個名稱識別碼格式**] 文字方塊中輸入的值，告訴我們您 SAML 判斷提示中的使用者 [姓氏] 所在的例如︰ **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/ 姓氏**。


6. 在 Azure 傳統的入口網站中，選取單一登入設定確認，然後再按 [**下一步**。

    ![Azure AD 單一登入][10]

7. 在**單一登入確認**] 頁面上，按一下 [**完成**]。  

    ![Azure AD 單一登入][11]




### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
本節的目標是稱為許承恩 Azure 傳統入口網站中建立的測試使用者。

![建立 Azure AD 使用者][20]

**若要建立的測試使用者 Azure AD 中，執行下列步驟︰**

1. 在**Azure 傳統入口網站**，在左側的功能窗格中，按一下 [ **Active Directory**]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-learnupon-tutorial/create_aaduser_09.png) 

2. 從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3. 若要顯示的使用者清單頂端的功能表中，按一下 [**使用者**]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-learnupon-tutorial/create_aaduser_03.png) 

4. 若要開啟 [**新增使用者**] 對話方塊底部的工具列中，按一下 [**新增使用者**]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-learnupon-tutorial/create_aaduser_04.png) 

5. 在 [**告訴我們瞭解此使用者**的對話方塊頁面，請執行下列步驟︰

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-learnupon-tutorial/create_aaduser_05.png) 

    。 為使用者類型，選取您組織中的 [新的使用者]。

    b。 在 [使用者名稱]**文字方塊**中，輸入**BrittaSimon**。

    c。 按一下 [**下一步**]。

6.  **使用者設定檔**] 對話方塊在頁面上，執行下列步驟︰

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-learnupon-tutorial/create_aaduser_06.png) 

    。 在 [**名字**] 文字方塊中輸入**許**。  

    b。 在 [**姓氏**] 文字方塊中輸入，**承恩**。

    c。 在 [**顯示名稱**] 文字方塊中，輸入**許承恩**。

    d。 在 [**角色**] 清單中選取 [**使用者**]。

    e。 按一下 [**下一步**]。

7. 在 [**取得暫時密碼**] 對話方塊頁面上，按一下 [**建立**]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-learnupon-tutorial/create_aaduser_07.png) 

8. **取得暫時密碼**] 對話方塊在頁面上，執行下列步驟︰

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-learnupon-tutorial/create_aaduser_08.png) 

    。 寫下的值的**新密碼**。

    b。 按一下 [**完成**]。   



### <a name="creating-a-learnupon-test-user"></a>建立 LearnUpon 測試使用者

本節的目標是以建立 LearnUpon 中名為許承恩使用者。 LearnUpon 支援只時間佈建，這是預設啟用。

沒有動作項目為您在此區段中。 在嘗試存取 LearnUpon 如果尚不存在，將會建立新的使用者。 [設定 Azure AD 單一登入](#configuring-azure-ad-single-single-sign-on)。

> [AZURE.NOTE] 若要手動建立的使用者，您需要連絡 LearnUpon 支援小組。


### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

本節的目標是啟用許承恩 Azure 單一登入使用其存取權授與 LearnUpon。

![為使用者指派][200] 

**若要指定 LearnUpon 許承恩，請執行下列步驟︰**

1. Azure 傳統的入口網站中，若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [**應用程式**頂端的功能表中。

    ![為使用者指派][201] 

2. 在應用程式清單中，選取 [ **LearnUpon**]。

    ![設定單一登入](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_50.png) 

1. 在頂端的功能表，按一下 [**使用者**]。

    ![為使用者指派][203] 

1. 在 [使用者] 清單中，選取**許承恩**。

2. 在底部工具列中，按一下 [**指派**]。

    ![為使用者指派][205]



### <a name="testing-single-sign-on"></a>測試單一登入

本節的目標是測試 Azure AD 單一登入設定使用 Access 面板。  
當您按一下 [Access] 面板中的 [LearnUpon] 方塊時，您應該取得自動登入 LearnUpon 應用程式。


## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程的清單](active-directory-saas-tutorial-list.md)
* [什麼是應用程式存取和單一登入與 Azure Active Directory？](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_205.png
