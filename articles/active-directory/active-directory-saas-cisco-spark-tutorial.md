<properties
    pageTitle="教學課程︰ Azure Active Directory 整合 Cisco 火花 |Microsoft Azure"
    description="瞭解如何設定單一登入 Azure Active Directory 和 Cisco 火花之間。"
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


# <a name="tutorial-azure-active-directory-integration-with-cisco-spark"></a>教學課程︰ Cisco 火花 Azure Active Directory 整合

在本教學課程中，您可以瞭解如何將 Cisco 火花整合與 Azure Active Directory (Azure AD)。

Azure AD 與整合 Cisco 火花為您提供下列優點︰

- 您可以控制可存取 Cisco 火花 Azure AD
- 您可以使用其 Azure AD 帳戶啟用自動取得登入 Cisco 火花 （單一登入） 至使用者
- 您可以管理您的帳戶，在一個中央位置-Azure 傳統入口網站

如果您想要知道 Azure AD SaaS 應用程式整合相關的更多詳細資料，請參閱[什麼是應用程式存取和單一登入與 Azure Active Directory](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件

若要設定 Cisco 火花 Azure AD 整合，您需要下列項目︰

- Azure AD 訂閱
- 啟用訂閱**Cisco 火花**單一正負號


> [AZURE.NOTE] 若要測試此教學課程中的步驟，我們不建議使用生產環境。


若要測試此教學課程中的步驟，您應該先按照這些建議︰

- 您應該使用生產環境，除非這是必要的。
- 如果您沒有安裝 Azure AD 試用環境，您可以在一個月試用[這裡](https://azure.microsoft.com/pricing/free-trial/)取得。


## <a name="scenario-description"></a>案例說明
在本教學課程中，您測試 Azure AD 單一登入的測試環境中。 本教學課程中所述的案例是由兩個主要的建置組塊所組成︰

1. 從圖庫新增 Cisco 火花
2. 設定及測試 Azure AD 單一登入


## <a name="adding-cisco-spark-from-the-gallery"></a>從圖庫新增 Cisco 火花
若要設定 Cisco 火花整合 Azure AD，必須將 Cisco 火花從圖庫新增至您的受管理的 SaaS 應用程式清單。

**若要從圖庫新增 Cisco 火花，請執行下列步驟︰**

1. 在**Azure 傳統入口網站**，在左側的功能窗格中，按一下 [ **Active Directory**]。 

    ![Active Directory][1]

2. 從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3. 若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [在上方的功能表中的 [**應用程式**]。

    ![應用程式][2]

4. 按一下 [**新增**頁面的底部。

    ![應用程式][3]

5. 在 [**您想要做什麼**] 對話方塊中，按一下 [**新增應用程式，從圖庫**。

    ![應用程式][4]

6. 在 [搜尋] 方塊中，輸入**Cisco 火花**。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_01.png)

7. 在 [結果] 窗格中，選取**Cisco 火花**，，然後按一下要新增應用程式**完成**。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>設定及測試 Azure AD 單一登入
在此區段中，您可以設定並使用根據稱為 「 許承恩 」 的測試使用者 Cisco 火花測試 Azure AD 單一登入。

單一登入搭配使用，Azure AD 需要知道什麼對應中的使用者 Cisco 火花使用者 Azure AD。 也就是說，必須建立連結和之間的關聯 Azure AD 使用者 Cisco 火花中相關的使用者。
所指派**的使用者名稱**的值為 Cisco 火花**使用者名稱**的值 Azure AD 中建立此連結關聯。 若要設定及測試 Azure AD 單一登入與 Cisco 火花，必須完成下列建置組塊︰

1. **[Azure AD 設定單一登入](#configuring-azure-ad-single-single-sign-on)**-若要讓使用者能使用這項功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)**-Azure AD 單一登入與許承恩測試。
4. **[建立 Cisco 火花測試使用者](#creating-a-cisco-spark-test-user)**-連結至她的 Azure AD 表示 Cisco 火花中有許承恩的對應。
5. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)**-啟用許承恩使用 Azure AD 單一登入。
5. **[測試單一登入](#testing-single-sign-on)**-驗證是否設定運作。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD 單一登入設定

本節的目標是 Azure AD 單一登入 Azure 傳統入口網站中啟用並 Cisco 火花應用程式中設定單一登入。

Cisco 火花應用程式預期的 SAML 判斷提示，以包含特定的屬性。 請將此應用程式的下列屬性的設定。 您可以在**「 Atrributes 」**索引標籤上的應用程式管理這些屬性的值。 以下螢幕擷取畫面會顯示這個範例。

![設定單一登入](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_03.png) 

**若要使用 Cisco 火花設定 Azure AD 單一登入，請執行下列步驟︰**

1. 在 Azure 的傳統入口網站中**Cisco 火花**應用程式整合在頁面上的功能表頂端，按一下 [**屬性**]。
     
    ![設定單一登入][5]


2. 在 [ **SAML 權杖屬性**] 對話方塊中，執行下列步驟︰

    。 按一下 [開啟 [**新增使用者 Attribure** ] 對話方塊的 [**新增使用者屬性**]。

    ![設定單一登入](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_05.png)
    
    b。 在 [**屬性名稱**] 文字方塊中，輸入**uid**。
    
    c。 從**屬性值**] 清單中，選取 [ **user.userprincipal**]。
    
    d。 按一下 [**完成**]。 然後，**套用變更**頁面的底部。

3. 在頂端的功能表，按一下 [**快速啟動**]。

    ![設定單一登入][6]

4. 在 [傳統] 入口網站， **Cisco 火花**應用程式整合在頁面上，按一下 [**設定單一登入**以開啟 [**設定單一登入**] 對話方塊。

    ![設定單一登入][7] 

5. 在**您要如何登入 Cisco 火花的使用者**] 頁面上，選取**Azure AD 單一登入**，然後按 [**下一步**。
    
    ![設定單一登入](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_06.png)

6. **設定應用程式設定**] 對話方塊在頁面上，執行下列步驟︰

    ![設定單一登入](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_07.png)


    。 在 [登入 URL] 文字方塊中，輸入 [使用下列模式的 URL: `https://web.ciscospark.com/#/signin`。

    b。 按一下 [**下一步**]。


7. 在**設定單一登入 Cisco 火花在**頁面上，按一下 [**下載中繼資料**，然後儲存您的電腦上的檔案。

    ![設定單一登入](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_09.png)

8. 使用您的完整的系統管理員認證登入[Cisco 雲端共同作業管理](https://admin.ciscospark.com/)。
9. 選取 [**設定**]，然後在 [**驗證**] 區段中，按一下 [**修改**]。

    ![設定單一登入](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_10.png)

10. 選取**整合 3rd 廠商身分識別提供者。（進階）**然後移至下一個畫面。
11. 按一下**下載的中繼資料檔案**，然後儲存您的電腦上的檔案。
12. 在 [**匯入 Idp 中繼資料**] 頁面上 [拖放 Azure AD 中繼資料檔案拖曳至頁面或使用，找出並 Azure AD 中繼資料檔案上傳的檔案瀏覽器選項。 然後，選取 [**需要憑證簽署的憑證授權單位在 [中繼資料 （更安全）** ，然後按一下 [**下一步**。 

    ![設定單一登入](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_11.png)

13. 選取**測試 SSO 連線**，以及新的瀏覽器索引標籤開啟時，驗證與 Azure AD 登入。
14. 返回 [ **Cisco 雲端共同作業管理**瀏覽器] 索引標籤。 如果測試成功，請選取**順利完成這項測試。啟用單一登入選項**，按一下 [**下一步**。

7. 在 Azure AD 傳統入口網站中，選取單一登入設定確認，然後再按 [**下一步**。
    
    ![Azure AD 單一登入][10]

8. 在**單一登入確認**] 頁面上，按一下 [**完成**]。  
    
    ![Azure AD 單一登入][11]

### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
在此區段中，您可以建立在傳統入口網站中名為許承恩測試使用者。

![建立 Azure AD 使用者][20]

**若要建立的測試使用者 Azure AD 中，執行下列步驟︰**

1. 在**Azure 傳統入口網站**，在左側的功能窗格中，按一下 [ **Active Directory**]。
    
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_09.png) 

2. 從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3. 若要顯示的使用者清單頂端的功能表中，按一下 [**使用者**]。
    
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_03.png) 

4. 若要開啟 [**新增使用者**] 對話方塊底部的工具列中，按一下 [**新增使用者**]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_04.png) 

5. 在 [**告訴我們瞭解此使用者**的對話方塊頁面，請執行下列步驟︰
 
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_05.png) 

    。 為使用者類型，選取您組織中的 [新的使用者]。

    b。 在 [使用者名稱]**文字方塊**中，輸入**BrittaSimon**。

    c。 按一下 [**下一步**]。

6.  **使用者設定檔**] 對話方塊在頁面上，執行下列步驟︰

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_06.png) 

    。 在 [**名字**] 文字方塊中輸入**許**。  

    b。 在 [**姓氏**] 文字方塊中輸入，**承恩**。

    c。 在 [**顯示名稱**] 文字方塊中，輸入**許承恩**。

    d。 在 [**角色**] 清單中選取 [**使用者**]。

    e。 按一下 [**下一步**]。

7. 在 [**取得暫時密碼**] 對話方塊頁面上，按一下 [**建立**]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_07.png) 

8. **取得暫時密碼**] 對話方塊在頁面上，執行下列步驟︰

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_08.png) 

    。 寫下的值的**新密碼**。

    b。 按一下 [**完成**]。   



### <a name="creating-a-cisco-spark-test-user"></a>建立 Cisco 火花測試使用者

在此區段中，您可以建立 Cisco 火花中名為許承恩使用者。 在此區段中，您可以建立 Cisco 火花中名為許承恩使用者。

1. 移至您的完整的系統管理員認證[Cisco 雲端共同作業管理](https://admin.ciscospark.com/)。
2. 按一下**使用者**，然後**管理使用者**。

    ![設定單一登入](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_12.png) 

3. **管理使用者**在視窗中，選取 [**手動新增或修改使用者**，然後按一下 [**下一步**。
4. 選取 [**名稱和電子郵件地址**。 然後，填寫文字方塊時，請依照下列︰

    ![設定單一登入](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_13.png) 

    。 在 [**名字**] 文字方塊中輸入 [**許**

    b。 在 [**姓氏**] 文字方塊中輸入 [**承恩**

    c。 在 [**電子郵件地址**] 文字方塊中輸入**britta.simon@contoso.com**

5. 按一下加號以新增許承恩。 然後，請按一下 [**下一步**]。
6. 在 [**使用者的 [新增服務**] 視窗中，按一下 [**儲存**，然後**完成**]。

### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在此區段中，您可以啟用 Azure 單一登入使用其存取權授與 Cisco 火花許承恩。

![為使用者指派][200] 

**若要指定 Cisco 火花許承恩，請執行下列步驟︰**

1. 在 [傳統] 入口網站，若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [**應用程式**頂端的功能表中。

    ![為使用者指派][201] 

2. 在應用程式清單中，選取 [ **Cisco 火花**]。

    ![設定單一登入](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_14.png) 

1. 在頂端的功能表，按一下 [**使用者**]。

    ![為使用者指派][203] 

1. 在 [所有使用者] 清單中，選取**許承恩**。

2. 在底部工具列中，按一下 [**指派**]。

    ![為使用者指派][205]


### <a name="testing-single-sign-on"></a>測試單一登入

本節的目標是測試 Azure AD 單一登入設定使用 Access 面板。

當您按一下 [Access] 面板中的 [Cisco 火花] 方塊時，您應該取得自動登入 Cisco 火花應用程式。

## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程的清單](active-directory-saas-tutorial-list.md)
* [什麼是應用程式存取和單一登入與 Azure Active Directory？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_205.png
