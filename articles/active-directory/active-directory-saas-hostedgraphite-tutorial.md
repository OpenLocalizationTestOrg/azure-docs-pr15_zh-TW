<properties
    pageTitle="教學課程︰ Azure Active Directory 整合裝載 Graphite |Microsoft Azure"
    description="瞭解如何設定單一登入 Azure Active Directory 和裝載 Graphite 之間。"
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
    ms.date="10/18/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-hosted-graphite"></a>教學課程︰ 使用裝載於 Graphite 的 Azure Active Directory 整合

本教學課程中的目標是以顯示您如何將整合裝載 Graphite 與 Azure Active Directory (Azure AD)。

Azure AD 與整合裝載 Graphite 為您提供下列優點︰

- 您可以控制可存取裝載 Graphite Azure AD
- 您可以使用其 Azure AD 帳戶啟用自動取得登入裝載的 Graphite （單一登入） 至使用者
- 您可以管理您的帳戶，在一個中央位置-Azure 傳統入口網站

如果您想要知道 Azure AD SaaS 應用程式整合相關的更多詳細資料，請參閱[什麼是應用程式存取和單一登入與 Azure Active Directory](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件

若要設定裝載於 Graphite Azure AD 整合，您需要下列項目︰

- Azure AD 訂閱
- 裝載於 Graphite 單一登啟用的訂閱


> [AZURE.NOTE] 若要測試此教學課程中的步驟，我們不建議使用生產環境。


若要測試此教學課程中的步驟，您應該先按照這些建議︰

- 您應該使用生產環境，除非這是必要的。
- 如果您沒有安裝 Azure AD 試用環境，您可以在一個月試用[這裡](https://azure.microsoft.com/pricing/free-trial/)取得。


## <a name="scenario-description"></a>案例說明
本教學課程中的目的是讓您測試環境中測試 Azure AD 單一登入。

本教學課程中所述的案例是由兩個主要的建置組塊所組成︰

1. 從圖庫新增裝載於 Graphite
2. 設定及測試 Azure AD 單一登入


## <a name="adding-hosted-graphite-from-the-gallery"></a>從圖庫新增裝載於 Graphite
若要設定裝載於 Graphite 整合 Azure AD，必須將裝載於 Graphite 從圖庫新增至您的受管理的 SaaS 應用程式清單。

**若要從圖庫新增裝載於 Graphite，請執行下列步驟︰**

1. 在**Azure 傳統入口網站**，在左側的功能窗格中，按一下 [ **Active Directory**]。 

    ![Active Directory][1]

2. 從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3. 若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [在上方的功能表中的 [**應用程式**]。
    
    ![應用程式][2]

4. 按一下 [**新增**頁面的底部。
    
    ![應用程式][3]

5. 在 [**您想要做什麼**] 對話方塊中，按一下 [**新增應用程式，從圖庫**。

    ![應用程式][4]

6. 在 [搜尋] 方塊中，輸入**主控 Graphite**。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_01.png)

7. 在 [結果] 面板中，選取**裝載於 Graphite**，，然後按一下要新增應用程式**完成**。

    ![在圖庫中選取應用程式](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_0001.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>設定及測試 Azure AD 單一登入
本節的目標是以顯示您如何設定和使用裝載 Graphite 根據稱為 「 許承恩 」 的測試使用者測試 Azure AD 單一登入。

單一登入搭配使用，Azure AD 需要知道什麼對應中的使用者裝載 Graphite Azure AD 中的使用者。 也就是說，必須建立連結和之間的關聯 Azure AD 使用者裝載於 Graphite 中相關的使用者。

所指派的**使用者名稱**值在 Azure AD 為裝載 Graphite**使用者名稱**的值建立此連結關聯。

若要設定及測試 Azure AD 單一登入與裝載於 Graphite，必須完成下列建置組塊︰

1. **[Azure AD 設定單一登入](#configuring-azure-ad-single-single-sign-on)**-若要讓使用者能使用這項功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)**-Azure AD 單一登入與許承恩測試。
3. **[建立裝載的 Graphite 測試使用者](#creating-a-hosted-graphite-test-user)**-連結至她的 Azure AD 表示裝載於 Graphite 中有許承恩的對應。
4. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)**-啟用許承恩使用 Azure AD 單一登入。
5. **[測試單一登入](#testing-single-sign-on)**-驗證是否設定運作。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD 單一登入設定

在此區段中，您可以啟用 Azure AD 單一登入 [傳統] 入口網站中，並裝載於 Graphite 應用程式中設定單一登入。

**若要使用裝載 Graphite 設定 Azure AD 單一登入，請執行下列步驟︰**

1. 在 [傳統] 入口網站**裝載於 Graphite**應用程式整合在頁面上，按一下 [**設定單一登入**以開啟 [**設定單一登入**] 對話方塊。
     
    ![設定單一登入][6] 

2. 在**您要如何登入裝載於 Graphite 的使用者**] 頁面上，選取**Azure AD 單一登入**，然後按 [**下一步**。
    
    ![設定單一登入](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_03.png)

3. **設定應用程式設定**] 對話方塊在頁面上，如果您想要將應用程式設定**IDP 發起的租用戶模式**中，執行下列步驟，然後按一下 [**下一步**︰

    ![設定單一登入](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_04.png)

    。 在 [**識別碼**] 文字方塊中輸入 URL，請使用下列模式︰`https://www.hostedgraphite.com/metadata/<user id>`

    b。 在 [**回覆 URL** ] 文字方塊中，輸入 URL，請使用下列模式︰`https://www.hostedgraphite.com/complete/saml/<user id>`

    c。 按一下 [**下一步**

4. 如果您想要設定**預存程序發起的租用戶模式**中的應用程式，在 [**設定應用程式設定**] 對話方塊的頁面上，然後按一下**[顯示進階設定 （可省略） 」**的然後輸入的**登入 URL**並按一下 [**下一步**。

    ![設定單一登入](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_10.png)

    。 在 [**登入 URL** ] 文字方塊中，輸入 URL，請使用下列模式︰`https://www.hostedgraphite.com/login/saml/<user id>/`

    b。 按一下 [**下一步**

    > [AZURE.NOTE] 請注意，這些不是真正的值。 您必須更新這些值的實際的登入 URL、 識別碼和回覆 URL。 若要取得這些值，您可以移至 Access]-> [您的應用程式方 SAML 設定，或連絡裝載 Graphite。

5. 在 [**設定單一登入裝載 Graphite 在**頁面上，執行下列步驟，然後按一下 [**下一步**︰

    ![設定單一登入](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_05.png)

    。 按一下 [**下載憑證**]，然後儲存您的電腦上的檔案。

    b。 按一下 [**下一步**]。

6. 登入至您裝載於 Graphite 租用戶，以系統管理員。

7. 移至**SAML 設定] 頁面**中資訊看板 (**Access]-> [SAML 設定**)。

    ![設定單一登入應用程式方](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_000.png)

8. 請確認這些 Url 符合您在步驟 3 中的設定。

    ![設定單一登入應用程式方](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_001.png)

9. 複製**發行者 URL**及**SAML SSO URL** Azure AD**實體或發行者識別碼**，並在主控 Graphite **SSO 登入 URL** 。

    ![設定單一登入應用程式方](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_002.png)

    ![設定單一登入應用程式方](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_003.png)

9. 選取 「**唯讀**」 作為**預設使用者角色**。

    ![設定單一登入應用程式方](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_004.png)

10. 複製下載的憑證檔案的內容，然後將其貼入**X.509 憑證**文字方塊。

     ![設定單一登入應用程式方](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_005.png)

11. 按一下 [**儲存**] 按鈕。

12. 在 [傳統] 入口網站，選取單一登入設定確認，然後按 [**下一步**。
    
    ![Azure AD 單一登入][10]

13. 在**單一登入確認**] 頁面上，按一下 [**完成**]。  
    
    ![Azure AD 單一登入][11]



### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
本節的目標是稱為許承恩傳統入口網站中建立一個測試使用者。

![建立 Azure AD 使用者][20]

**若要建立的測試使用者 Azure AD 中，執行下列步驟︰**

1. 在**Azure 傳統入口網站**，在左側的功能窗格中，按一下 [ **Active Directory**]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-hostedgraphite-tutorial/create_aaduser_09.png)

2. 從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3. 若要顯示的使用者清單頂端的功能表中，按一下 [**使用者**]。
    
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-hostedgraphite-tutorial/create_aaduser_03.png)

4. 若要開啟 [**新增使用者**] 對話方塊底部的工具列中，按一下 [**新增使用者**]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-hostedgraphite-tutorial/create_aaduser_04.png)

5. 在 [**告訴我們瞭解此使用者**的對話方塊頁面，請執行下列步驟︰

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-hostedgraphite-tutorial/create_aaduser_05.png)

    。 為使用者類型，選取您組織中的 [新的使用者]。

    b。 在 [使用者名稱]**文字方塊**中，輸入**BrittaSimon**。

    c。 按一下 [**下一步**]。

6.  **使用者設定檔**] 對話方塊在頁面上，執行下列步驟︰
    
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-hostedgraphite-tutorial/create_aaduser_06.png)

    。 在 [**名字**] 文字方塊中輸入**許**。  

    b。 在 [**姓氏**] 文字方塊中輸入，**承恩**。

    c。 在 [**顯示名稱**] 文字方塊中，輸入**許承恩**。

    d。 在 [**角色**] 清單中選取 [**使用者**]。

    e。 按一下 [**下一步**]。

7. 在 [**取得暫時密碼**] 對話方塊頁面上，按一下 [**建立**]。
    
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-hostedgraphite-tutorial/create_aaduser_07.png)

8. **取得暫時密碼**] 對話方塊在頁面上，執行下列步驟︰
    
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-hostedgraphite-tutorial/create_aaduser_08.png)

    。 寫下的值的**新密碼**。

    b。 按一下 [**完成**]。   



### <a name="creating-a-hosted-graphite-test-user"></a>建立裝載於 Graphite 測試使用者

本節的目標是以建立裝載於 Graphite 中名為許承恩使用者。 主控的 Graphite 支援只時間佈建，這是預設啟用。

沒有動作項目為您在此區段中。 在嘗試存取裝載 Graphite 如果尚不存在，將會建立新的使用者。

> [AZURE.NOTE] 若要手動建立的使用者，您需要連絡裝載 Graphite 支援小組透過<mailto:help@hostedgraphite.com>。


### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

本節的目標是啟用 Azure 單一登入使用其存取權授與裝載 Graphite 許承恩。
    
![為使用者指派][200]

**若要指定裝載 Graphite 許承恩，請執行下列步驟︰**

1. 在 [傳統] 入口網站，若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [**應用程式**頂端的功能表中。
    
    ![為使用者指派][201]

2. 在應用程式清單中，選取 [**裝載 Graphite**]。
    
    ![設定單一登入](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_50.png)

1. 在頂端的功能表，按一下 [**使用者**]。
    
    ![為使用者指派][203]

1. 在 [使用者] 清單中，選取**許承恩**。

2. 在底部工具列中，按一下 [**指派**]。
    
    ![為使用者指派][205]



### <a name="testing-single-sign-on"></a>測試單一登入

本節的目標是測試 Azure AD 單一登入設定使用 Access 面板。
 
當您按一下 [Access] 面板中的 [裝載 Graphite] 方塊時，您應該取得自動登入裝載 Graphite 應用程式。


## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程的清單](active-directory-saas-tutorial-list.md)
* [什麼是應用程式存取和單一登入與 Azure Active Directory？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_205.png
