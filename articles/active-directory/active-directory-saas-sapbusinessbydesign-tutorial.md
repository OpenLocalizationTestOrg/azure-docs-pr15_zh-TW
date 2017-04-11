<properties
    pageTitle="教學課程︰ 使用 SAP 商務 ByDesign 的 Azure Active Directory 整合 |Microsoft Azure"
    description="瞭解如何設定單一登入 Azure Active Directory 和 SAP 商務 ByDesign 之間。"
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
    ms.date="09/09/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-sap-business-bydesign"></a>教學課程︰ 使用 SAP 商務 ByDesign 的 Azure Active Directory 整合

在本教學課程中，您可以瞭解如何將 SAP 商務 ByDesign 整合與 Azure Active Directory (Azure AD)。

Azure AD 與整合 SAP 商務 ByDesign 為您提供下列優點︰

- 您可以控制可存取 SAP 商務 ByDesign Azure AD
- 您可以使用其 Azure AD 帳戶啟用自動取得登入 SAP 商務 ByDesign （單一登入） 至使用者
- 您可以管理您的帳戶，在一個中央位置-Azure 傳統入口網站

如果您想要知道 Azure AD SaaS 應用程式整合相關的更多詳細資料，請參閱[什麼是應用程式存取和單一登入與 Azure Active Directory](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件

若要設定的 SAP 商務 ByDesign 的 Azure AD 整合，您需要下列項目︰

- Azure AD 訂閱
- SAP 商務 ByDesign 單一登啟用的訂閱


> [AZURE.NOTE] 若要測試此教學課程中的步驟，我們不建議使用生產環境。


若要測試此教學課程中的步驟，您應該先按照這些建議︰

- 您應該使用生產環境，除非這是必要的。
- 如果您沒有安裝 Azure AD 試用環境，您可以在一個月試用[這裡](https://azure.microsoft.com/pricing/free-trial/)取得。


## <a name="scenario-description"></a>案例說明
在本教學課程中，您測試 Azure AD 單一登入的測試環境中。

本教學課程中所述的案例是由兩個主要的建置組塊所組成︰

1. 從圖庫新增 SAP 商務 ByDesign
2. 設定及測試 Azure AD 單一登入


## <a name="adding-sap-business-bydesign-from-the-gallery"></a>從圖庫新增 SAP 商務 ByDesign
若要設定的 SAP 商務 ByDesign 整合到 Azure AD，您需要從庫新增 SAP 商務 ByDesign 到受管理的 SaaS 應用程式的清單。

**若要從圖庫新增 SAP 商務 ByDesign，請執行下列步驟︰**

1. 在**Azure 傳統入口網站**，在左側的功能窗格中，按一下 [ **Active Directory**]。

    ![Active Directory][1]

2. 從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。


3. 若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [在上方的功能表中的 [**應用程式**]。

    ![應用程式][2]

4. 按一下 [**新增**頁面的底部。

    ![應用程式][3]

5. 在 [**您想要做什麼**] 對話方塊中，按一下 [**新增應用程式，從圖庫**。

    ![應用程式][4]

6. 在 [搜尋] 方塊中，輸入**SAP 商務 ByDesign**。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_01.png)

7. 在 [結果] 窗格中，選取**SAP 商務 ByDesign**，，然後按一下要新增應用程式**完成**。

    ![Active Directory](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_02.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>設定及測試 Azure AD 單一登入
在此區段中，您可以設定並使用 SAP 商務 ByDesign 根據稱為 「 許承恩 」 的測試使用者測試 Azure AD 單一登入。

單一登入搭配使用，Azure AD 需要知道什麼對應中的使用者 SAP 商務 ByDesign 使用者 Azure AD。 也就是說，必須建立連結和之間的關聯 Azure AD 使用者 SAP 商務 ByDesign 中相關的使用者。

所指派**的使用者名稱**的值為 SAP 商務 ByDesign**使用者名稱**的值 Azure AD 中建立此連結關聯。

若要設定及測試 Azure AD 單一登入與 SAP 商務 ByDesign，必須完成下列建置組塊︰

1. **[Azure AD 設定單一登入](#configuring-azure-ad-single-sign-on)**-若要讓使用者能使用這項功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)**-Azure AD 單一登入與許承恩測試。
3. **[建立 SAP 商務 ByDesign 測試使用者](#creating-an-sap-business-bydesign-test-user)**-連結至 Azure AD 的表示她的 SAP 商務 ByDesign 中有許承恩的對應。
4. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)**-啟用許承恩使用 Azure AD 單一登入。
5. **[測試單一登入](#testing-single-sign-on)**-驗證是否設定運作。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD 單一登入設定

在此區段中，您可以 Azure AD 單一登入 [傳統] 入口網站中啟用，並在 SAP 商務 ByDesign 應用程式中設定單一登入。

SAP 商務 ByDesign 應用程式預期 SAML 判斷提示，以特定格式。 請設定此應用程式的下列宣告。 您可以在**「 Atrribute 」**索引標籤上的應用程式管理這些屬性的值。 以下螢幕擷取畫面會顯示這個範例。 


**若要使用 SAP 商務 ByDesign 設定 Azure AD 單一登入，請執行下列步驟︰**


1. 在 Azure 的傳統入口網站中**SAP 商務 ByDesign**應用程式整合在頁面上的功能表頂端，按一下 [**屬性**]。

    ![設定單一登入](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_80.png) 


2. 在屬性 SAML 權杖屬性] 清單中，選取 nameidentifier 的屬性，然後再按一下 [**編輯**。

    ![設定單一登入](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_84.png) 

3. 在 [編輯使用者屬性] 對話方塊中，執行下列步驟︰

    ![設定單一登入](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_85.png) 

    。 屬性值清單中，選取 [ **ExtractMailPrefix()** now

    b。 從郵件清單中，選取您想要使用的您實作使用者屬性。 
    例如，如果您想要的員工作為專屬使用者識別碼，ExtensionAttribute2 中儲存屬性值，然後選取**user.extensionattribute2**。 

    c。 按一下 [**完成**]。 
    

4. 在 [傳統] 入口網站， **SAP 商務 ByDesign**應用程式整合在頁面上，按一下 [**設定單一登入**以開啟 [**設定單一登入**] 對話方塊。
     
    ![設定單一登入][6] 

5. 在**您要如何登入 SAP 商務 ByDesign 的使用者**] 頁面上，選取**Azure AD 單一登入**，然後按 [**下一步**。

    ![設定單一登入](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_03.png) 

6. **設定應用程式設定**] 對話方塊在頁面上，執行下列步驟︰

    ![設定單一登入](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_04.png) 

    。 在 [**登入 URL** ] 文字方塊中，輸入您登入您使用下列模式的 SAP 商務 ByDesign 應用程式的使用者所使用的 URL:`https://<servername>.sapbydesign.com`
    
    b。 按一下 [**下一步**
 
7. 在**設定單一登入 SAP 商務 ByDesign 在**頁面上，執行下列步驟︰

    ![設定單一登入](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_05.png)

    。 按一下**下載的中繼資料**]，然後儲存您的電腦上的檔案。

    b。 按一下 [**下一步**]。


8. 若要取得 SSO 應用程式的設定，請執行下列步驟︰

    。 登入您的 SAP 商務 ByDesign 入口網站，以系統管理員權限。

    b。 瀏覽至**應用程式和使用者管理一般工作**，然後按一下 [**身分識別提供者**] 索引標籤。

    c。 按一下 [**新的身分識別提供者**，然後選取 [從 Azure 傳統入口網站所下載的中繼資料 XML 檔案。 匯入中繼資料，系統會自動上傳的必要的簽章憑證和加密憑證。

    ![設定單一登入](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_54.png)

    d。 若要**判斷提示消費者服務 URL**到 SAML 邀請，請選取 [**包含判斷提示消費者服務 URL**]。

    e。 按一下 [**啟動單一登入**]。

    f。 儲存變更。

    g。 按一下 [**我的系統**] 索引標籤。

    ![設定單一登入](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_52.png)

    h。 複製**SSO URL** ，並將它貼到 [ **Azure AD 登入 [URL** ] 文字方塊。

    ![設定單一登入](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_53.png)

    我。 指定是否員工可以手動選擇之間登入的使用者識別碼及密碼或 SSO 選取**手動身分識別提供者的選取範圍**。

    j。 在 [ **SSO URL** ] 區段中，指定應由系統的登入的員工的 URL。 
    在 URL 傳送給員工下拉式清單中，您可以選擇下列選項︰
    
    **非 SSO URL**
 
    系統會傳送給員工標準系統 URL。 員工無法登入使用 SSO，且必須使用密碼或改為憑證。

    **SSO URL** 

    系統會傳送給員工 SSO URL。 員工可以登入使用 SSO。 驗證要求重新導向到 IdP。

    **自動選取範圍**
 
    如果 SSO 不是作用中時，系統會傳送給員工標準系統 URL。 如果 SSO 是作用中時，系統會檢查員工是否具有密碼。 如果使用密碼，同時 SSO URL] 與 [非 SSO URL 會傳送給員工。 不過，如果員工沒有密碼，SSO URL 會傳送給員工。

    k。 儲存變更。

9. 在 [傳統] 入口網站，選取單一登入設定確認，然後按 [**下一步**。
    
    ![Azure AD 單一登入][10]

10. 在**單一登入確認**] 頁面上，按一下 [**完成**]。  
 
    ![Azure AD 單一登入][11]


### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
在此區段中，您可以建立在傳統入口網站中名為許承恩測試使用者。


![建立 Azure AD 使用者][20]

**若要建立的測試使用者 Azure AD 中，執行下列步驟︰**

1. 在**Azure 傳統入口網站**，在左側的功能窗格中，按一下 [ **Active Directory**]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_09.png) 

2. 從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3. 若要顯示的使用者清單頂端的功能表中，按一下 [**使用者**]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_03.png) 

4. 若要開啟 [**新增使用者**] 對話方塊底部的工具列中，按一下 [**新增使用者**]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_04.png) 

5. 在 [**告訴我們瞭解此使用者**的對話方塊頁面，請執行下列步驟︰
    
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_05.png) 

    。 為使用者類型，選取您組織中的 [新的使用者]。

    b。 在 [使用者名稱]**文字方塊**中，輸入**BrittaSimon**。

    c。 按一下 [**下一步**]。

6.  **使用者設定檔**] 對話方塊在頁面上，執行下列步驟︰
    
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_06.png) 

    。 在 [**名字**] 文字方塊中輸入**許**。  

    b。 在 [**姓氏**] 文字方塊中輸入，**承恩**。

    c。 在 [**顯示名稱**] 文字方塊中，輸入**許承恩**。

    d。 在 [**角色**] 清單中選取 [**使用者**]。

    e。 按一下 [**下一步**]。

7. 在 [**取得暫時密碼**] 對話方塊頁面上，按一下 [**建立**]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_07.png) 

8. **取得暫時密碼**] 對話方塊在頁面上，執行下列步驟︰

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_08.png) 

    。 寫下的值的**新密碼**。

    b。 按一下 [**完成**]。   



### <a name="creating-an-sap-business-bydesign-test-user"></a>建立 SAP 商務 ByDesign 測試使用者

在此區段中，您可以建立 SAP 商務 ByDesign 中名為許承恩使用者。 請使用 SAP 商務 ByDesign 將使用者新增 SAP 商務 ByDesign 平台的支援小組。 

> [AZURE.NOTE] 請務必 NameID 值應與使用者名稱] 欄位中的 SAP 商務 ByDesign 平台。

### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在此區段中，您可以啟用 Azure 單一登入使用其存取權授與 SAP 商務 ByDesign 許承恩。

![為使用者指派][200] 

**若要指定 SAP 商務 ByDesign 許承恩，請執行下列步驟︰**

1. 在 [傳統] 入口網站，若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [**應用程式**頂端的功能表中。

    ![為使用者指派][201] 

2. 在應用程式清單中，選取 [ **SAP 商務 ByDesign**]。

    ![設定單一登入](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_50.png) 

3. 在頂端的功能表，按一下 [**使用者**]。

    ![為使用者指派][203]

4. 在 [使用者] 清單中，選取**許承恩**。

5. 在底部工具列中，按一下 [**指派**]。

    ![為使用者指派][205]


### <a name="testing-single-sign-on"></a>測試單一登入

在此區段中，您測試 Azure AD 單一登入設定使用 Access 面板。

當您按一下 [Access] 面板中的 [SAP 商務 ByDesign] 方塊時，您應該取得自動登入 SAP 商務 ByDesign 應用程式。


## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程的清單](active-directory-saas-tutorial-list.md)
* [什麼是應用程式存取和單一登入與 Azure Active Directory？](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_205.png
