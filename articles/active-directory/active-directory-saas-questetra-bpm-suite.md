<properties
    pageTitle="教學課程︰ 使用 Questetra BPM 套件的 Azure Active Directory 整合 |Microsoft Aure"
    description="瞭解如何設定單一登入 Azure Active Directory 和 Questetra BPM 套件之間。"
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
    ms.date="10/28/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-questetra-bpm-suite"></a>教學課程︰ 使用 Questetra BPM 套件的 Azure Active Directory 整合

本教學課程中的目標是以顯示您如何與 Azure Active Directory (Azure AD) 整合 Questetra BPM 套件。  
Azure AD 與整合 Questetra BPM 套件為您提供下列優點︰ 

- 您可以控制可存取 Questetra BPM 套件 Azure AD 
- 您可以使用其 Azure AD 帳戶啟用使用者會自動取得登入 Questetra BPM 套件 （單一登入）
- 您可以管理您的帳戶，在一個中央位置-Azure 傳統入口網站

如果您想要知道 Azure AD SaaS 應用程式整合相關的更多詳細資料，請參閱[什麼是應用程式存取和單一登入與 Azure Active Directory](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件 

若要設定 Azure AD 整合與 Questetra BPM 套件，您需要下列項目︰

- Azure AD 訂閱
- 啟用訂閱[Questetra BPM 套件](https://senbon-imadegawa-988.questetra.net/)單一正負號


> [AZURE.NOTE] 若要測試此教學課程中的步驟，我們不建議使用生產環境。


若要測試此教學課程中的步驟，您應該先按照這些建議︰

- 您應該使用生產環境，除非這是必要的。
- 如果您沒有安裝 Azure AD 試用環境，您可以在一個月試用[這裡](https://azure.microsoft.com/pricing/free-trial/)取得。 

 
## <a name="scenario-description"></a>案例說明
本教學課程中的目的是讓您測試環境中測試 Azure AD 單一登入。  
本教學課程中所述的案例是由三個主要的建置組塊所組成︰

1. 從圖庫新增 Questetra BPM 套件 
2. 設定及測試 Azure AD 單一登入


## <a name="adding-questetra-bpm-suite-from-the-gallery"></a>從圖庫新增 Questetra BPM 套件
若要設定 Questetra BPM 套件整合 Azure AD，必須將 Questetra BPM 套件從圖庫新增至您的受管理的 SaaS 應用程式清單。

**若要從圖庫新增 Questetra BPM 套件，請執行下列步驟︰**

1. 在**Azure 傳統入口網站**，在左側的功能窗格中，按一下 [ **Active Directory**]。 

    ![Active Directory][1]

2. 從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3. 若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [在上方的功能表中的 [**應用程式**]。

    ![應用程式][2]

4. 按一下 [**新增**頁面的底部。

    ![應用程式][3]

5. 在 [**您想要做什麼**] 對話方塊中，按一下 [**新增應用程式，從圖庫**。

    ![應用程式][4]

6. 在 [搜尋] 方塊中，輸入**Questetra BPM 套件**。

    ![應用程式][5]

7. 在 [結果] 窗格中，選取**Questetra BPM 套件**，，然後按一下要新增應用程式**完成**。



##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>設定及測試 Azure AD 單一登入
本節的目標是以顯示您如何設定和使用根據稱為 「 許承恩 」 的測試使用者 Questetra BPM 套件測試 Azure AD 單一登入。

單一登入搭配使用，Azure AD 需要知道什麼對應使用者在 Questetra BPM 套件中 Azure AD 中的使用者。 也就是說，必須建立連結和之間的關聯 Azure AD 使用者 Questetra BPM 組件中相關的使用者。  
所指派的**使用者名稱**值在 Azure AD 為 Questetra BPM 套件**的使用者名稱**的值建立此連結關聯。
 
若要設定及測試 Azure AD 單一登入與 Questetra BPM 套件，必須完成下列建置組塊︰

1. **[Azure AD 設定單一登入](#configuring-azure-ad-single-single-sign-on)**-若要讓使用者能使用這項功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)**-Azure AD 單一登入與許承恩測試。
4. **[建立 Questetra BPM 套件測試使用者](#creating-a-questetra-bpm-suite-test-user)**-連結至 Azure AD 的表示她的 Questetra BPM 套件中有許承恩的對應。
5. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)**-啟用許承恩使用 Azure AD 單一登入。
5. **[測試單一登入](#testing-single-sign-on)**-驗證是否設定運作。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD 單一登入設定

本節的目標是 Azure AD 單一登入 Azure 傳統入口網站中啟用並 Questetra BPM 套件應用程式中設定單一登入。

**若要設定 Azure AD 單一登入與 Questetra BPM 套件，請執行下列步驟︰**

1. 在 Azure 的傳統入口網站， **Questetra BPM 套件**應用程式整合在頁面上，按一下 [**設定單一登入**以開啟 [**設定單一登入**] 對話方塊。

    ![設定單一登入][8]

2. 在**您要如何登入 Questetra BPM 套件的使用者**] 頁面上，選取**Azure AD 單一登入**，然後按 [**下一步**。

    ![Azure AD 單一登入][9]


3. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入您**Questetra BPM 套件**公司的網站。

4. 在頂端的功能表，按一下 [**系統設定**]。 

    ![Azure AD 單一登入][10]

5. 若要開啟 [ **SingleSignOnSAML** ] 頁面，按一下 [ **SSO (SAML)**]。 

    ![Azure AD 單一登入][11]


6. 在 Azure 傳統入口網站中的 [**設定應用程式設定**] 對話方塊在頁面上，執行下列步驟︰ 

    ![設定應用程式設定][13]
 
    。 在您**Questetra BPM 套件**公司網站、 預存程序資訊] 區段中，複製**ACS URL**，，然後再貼到 [**登入 URL** ] 文字方塊。

    b。 在您**Questetra BPM 套件**公司網站、 預存程序資訊] 區段中，複製**實體識別碼**，，然後再貼到 [**簽發者 URL** ] 文字方塊。

    c。 在您**Questetra BPM 套件**公司網站、 預存程序資訊] 區段中，複製**ACS URL**，，然後再貼到 [**回覆 URL** ] 文字方塊。

    d。 按一下 [**下一步**]。

 
7. 在**設定單一登入 Questetra BPM 套件在**頁面上，按一下**下載憑證**]，然後儲存在本機電腦上的憑證檔案。

    ![設定單一登入][14]


8. 在您**Questetra BPM 套件**公司網站，請執行下列步驟︰ 

    ![設定單一登入][15]

    。 選取 [**啟用單一登入**]。
     
    b。 在 Azure 傳統的入口網站中，複製**簽發者 URL**的值，然後再貼到 [**實體 ID** ] 文字方塊。

    c。 在 Azure 傳統的入口網站中，複製 [**單一登入服務 URL**的值，然後再貼到 [**登入頁面的 URL** ] 文字方塊。

    d。 在 Azure 傳統的入口網站中，複製 [**單一 Sign-Out 服務 URL**的值，然後再貼到 [**教具借出頁面的 URL** ] 文字方塊。

    e。 在 [ **NameID 格式**] 文字方塊中，輸入**urn: oasis︰ 名稱︰ tc: SAML:1.1:nameid-格式︰ 電子郵件地址**。


    f。 建立 64 基本編碼的檔案從您下載的憑證。 

    >[AZURE.TIP] 如需詳細資訊，請參閱[如何將轉換成文字檔的二進位憑證](http://youtu.be/PlgrzUZ-Y1o)。

    g。 在記事本中開啟您 64 基本編碼的憑證，將其內容複製到剪貼簿，，然後將其貼入**驗證憑證**文字方塊。 

    h。 按一下 [**儲存**]。


9. 在 Azure 傳統的入口網站中，選取單一登入設定確認，然後再按 [**下一步**。 

    ![什麼是 Azure AD Connect][17]


10. 在**單一登入確認**] 頁面上，按一下 [**完成**]。  

    ![什麼是 Azure AD Connect][18]




### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
本節的目標是稱為許承恩 Azure 傳統入口網站中建立的測試使用者。

**若要建立的測試使用者 Azure AD 中，執行下列步驟︰**

1. 在**Azure 傳統入口網站**，在左側的功能窗格中，按一下 [ **Active Directory**]。

    ![建立 Azure AD 測試使用者][100] 

2. 從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3. 若要顯示的使用者清單頂端的功能表中，按一下 [**使用者**]。

    ![建立 Azure AD 測試使用者][101] 

4. 若要開啟 [**新增使用者**] 對話方塊底部的工具列中，按一下 [**新增使用者**]。 

    ![建立 Azure AD 測試使用者][102] 

5. 在 [**告訴我們瞭解此使用者**的對話方塊頁面，請執行下列步驟︰

    ![建立 Azure AD 測試使用者][103]
 
    。 **輸入的使用者**，選取**您組織中的新使用者**。
  
    b。 在 [使用者名稱]**文字方塊**中，輸入**BrittaSimon**。

    c。 按一下 [下一步]。

6.  **使用者設定檔**] 對話方塊在頁面上，執行下列步驟︰ 

    ![建立 Azure AD 測試使用者][104] 
  
    。 在 [**名字**] 文字方塊中輸入**許**。 
 
    b。 在 [**姓氏**] 文字方塊中輸入，**承恩**。

    c。 在 [**顯示名稱**] 文字方塊中，輸入**許承恩**。

    d。 在 [**角色**] 清單中選取 [**使用者**]。

    e。 按一下 [**下一步**]。

7. 在 [**取得暫時密碼**] 對話方塊頁面上，按一下 [**建立**]。

    ![建立 Azure AD 測試使用者][105]  

8. **取得暫時密碼**] 對話方塊在頁面上，執行下列步驟︰

    ![建立 Azure AD 測試使用者][106]   
  
    。 寫下的值的**新密碼**。
  
    b。 按一下 [**完成**]。   
  
 
### <a name="creating-a-questetra-bpm-suite-test-user"></a>建立 Questetra BPM 套件測試使用者

本節的目標是建立稱為許承恩 Questetra BPM 組件中的使用者。

**若要建立稱為許承恩 Questetra BPM 組件中的使用者，請執行下列步驟︰**

1.  登入至您 Questetra BPM 套件的公司網站以系統管理員。
2.  移至 [**系統設定 > 使用者清單 > 新使用者**。 
3.  在 [新增使用者] 對話方塊中，執行下列步驟︰ 

    ![建立測試使用者][300] 

    。 在 [**名稱**] 文字方塊中，輸入許的使用者名稱中 Azure AD。

    b。 在 [**電子郵件**] 文字方塊中，輸入許的使用者名稱中 Azure AD。

    c。 在 [**密碼**] 文字方塊中輸入密碼。

4.  按一下 [**新增使用者**]。



### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

本節的目標是啟用許承恩 Azure 單一登入使用其存取權授與 Questetra BPM 套件。

![什麼是 Azure AD Connect][200]

**若要指定許承恩 Questetra BPM 套件，請執行下列步驟︰**

1. Azure 傳統的入口網站中，若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [**應用程式**頂端的功能表中。

    ![什麼是 Azure AD Connect][201]

2. 在應用程式清單中，選取 [ **Questetra BPM 套件**]。

    ![什麼是 Azure AD Connect][205]

1. 在頂端的功能表，按一下 [**使用者**]。

    ![什麼是 Azure AD Connect][202]

1. 在 [使用者] 清單中，選取**許承恩**。

    ![什麼是 Azure AD Connect][203]

2. 在底部工具列中，按一下 [**指派**]。

    ![什麼是 Azure AD Connect][204]



### <a name="testing-single-sign-on"></a>測試單一登入

本節的目標是測試 Azure AD 單一登入設定使用 Access 面板。  
當您按一下 [Access] 面板中的 [Questetra BPM 套件] 方塊時，您應該取得自動登入 Questetra BPM 套件應用程式。


## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程的清單](active-directory-saas-tutorial-list.md)
* [什麼是應用程式存取和單一登入與 Azure Active Directory？](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_01.png
[2]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_02.png
[3]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_03.png
[4]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_04.png
[5]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_01.png


[8]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_06.png
[9]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_02.png
[10]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_03.png
[11]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_04.png
[12]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_05.png
[13]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_06.png
[14]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_07.png
[15]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_08.png
[16]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_09.png
[17]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_10.png
[18]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_08.png


[100]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_09.png 
[101]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_10.png 
[102]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_11.png 
[103]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_12.png 
[104]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_13.png 
[105]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_14.png 
[106]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_15.png 


[200]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_16.png 
[201]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_17.png 
[202]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_18.png
[203]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_19.png
[204]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_20.png
[205]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_12.png

[300]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_11.png 