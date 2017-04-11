<properties
    pageTitle="教學課程︰ Azure Active Directory 整合 Qlik 理解企業版 |Microsoft Azure"
    description="瞭解如何設定單一登入 Azure Active Directory 和 Qlik 理解企業之間。"
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
    ms.date="08/31/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-qlik-sense-enterprise"></a>教學課程︰ Azure Active Directory Qlik 理解企業整合

在本教學課程中，您可以瞭解如何將 Qlik 理解企業整合與 Azure Active Directory (Azure AD)。

Azure AD 與整合 Qlik 理解企業為您提供下列優點︰

- 您可以控制可存取 Qlik 理解企業 Azure AD
- 您可以啟用您的使用者會自動取得登入至 Qlik 理解企業 （單一登入） 至 Azure AD 帳戶
- 您可以管理您的帳戶，在一個中央位置-Azure 傳統入口網站

如果您想要知道 Azure AD SaaS 應用程式整合相關的更多詳細資料，請參閱[什麼是應用程式存取和單一登入與 Azure Active Directory](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 整合與 Qlik 理解企業版，您需要下列項目︰

- Azure AD 訂閱
- 啟用訂閱 Qlik 理解企業單一正負號


> [AZURE.NOTE] 若要測試此教學課程中的步驟，我們不建議使用生產環境。


若要測試此教學課程中的步驟，您應該先按照這些建議︰

- 您應該使用生產環境，除非這是必要的。
- 如果您沒有安裝 Azure AD 試用環境，您可以在一個月試用[這裡](https://azure.microsoft.com/pricing/free-trial/)取得。


## <a name="scenario-description"></a>案例說明
在本教學課程中，您測試 Azure AD 單一登入的測試環境中。

本教學課程中所述的案例是由兩個主要的建置組塊所組成︰

1. 從圖庫新增 Qlik 理解企業版
2. 設定及測試 Azure AD 單一登入


## <a name="adding-qlik-sense-enterprise-from-the-gallery"></a>從圖庫新增 Qlik 理解企業版
若要設定 Qlik 理解企業整合 Azure AD，必須將 Qlik 理解企業從圖庫新增至您的受管理的 SaaS 應用程式清單。

**若要從圖庫新增 Qlik 理解企業版，請執行下列步驟︰**

1. 在**Azure 傳統入口網站**，在左側的功能窗格中，按一下 [ **Active Directory**]。

    ![Active Directory][1]
2. 從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3. 若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [在上方的功能表中的 [**應用程式**]。

    ![應用程式][2]

4. 按一下 [**新增**頁面的底部。

    ![應用程式][3]

5. 在 [**您想要做什麼**] 對話方塊中，按一下 [**新增應用程式，從圖庫**。

    ![應用程式][4]

6. 在 [搜尋] 方塊中，輸入**Qlik 理解企業版**。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_01.png)

7. 在 [結果] 窗格中，選取**Qlik 理解企業**、，然後按一下要新增應用程式**完成**。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>設定及測試 Azure AD 單一登入
在此區段中，您可以設定並使用 Qlik 理解企業根據稱為 「 許承恩 」 的測試使用者測試 Azure AD 單一登入。

單一登入的工作，必須知道 Qlik 理解企業中的對應使用者是使用者在 Azure AD Azure AD。 也就是說，必須建立連結和之間的關聯 Azure AD 使用者 Qlik 理解企業中相關的使用者。

所指派**的使用者名稱**的值為 Qlik 理解企業**使用者名稱**的值 Azure AD 中建立此連結關聯。

若要設定及測試 Azure AD 單一登入與 Qlik 理解企業版，必須完成下列建置組塊︰

1. **[Azure AD 設定單一登入](#configuring-azure-ad-single-sign-on)**-若要讓使用者能使用這項功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)**-Azure AD 單一登入與許承恩測試。
3. **[建立 Qlik 理解企業測試使用者](#creating-a-qliksense-enterprise-test-user)**-已連結至 Azure AD 的表示她的 Qlik 理解企業中有許承恩的對應。
4. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)**-啟用許承恩使用 Azure AD 單一登入。
5. **[測試單一登入](#testing-single-sign-on)**-驗證是否設定運作。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD 單一登入設定

在此區段中，您可以 Azure AD 單一登入 [傳統] 入口網站中啟用並 Qlik 理解企業應用程式中設定單一登入。


**若要使用 Qlik 理解企業設定 Azure AD 單一登入，請執行下列步驟︰**

1. 在 [傳統] 入口網站， **Qlik 理解企業**應用程式整合在頁面上，按一下 [**設定單一登入**以開啟 [**設定單一登入**] 對話方塊。
     
    ![設定單一登入][6] 

2. 在**您要如何登入 Qlik 理解企業版的使用者**] 頁面上，選取**Azure AD 單一登入**，然後按 [**下一步**。

    ![設定單一登入](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_03.png) 

3. **設定應用程式設定**] 對話方塊在頁面上，執行下列步驟︰

    ![設定單一登入](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_04.png) 

    。 在 [**登入 URL** ] 文字方塊中，輸入您登入使用下列模式 Qlik 理解企業應用程式的使用者所使用的 URL: **https://\<Qlik 意義完全 Qualifed Hostname\>: 443 / < 虛擬 Proxy 加上字首\>/samlauthn/**。
    
    > [AZURE.NOTE] 請注意此 URI 結尾斜線。  需要。

    b。 按一下 [**下一步**
 
4. 在**設定單一登入 Qlik 理解企業在**頁面上，執行下列步驟︰

    ![設定單一登入](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_05.png)

    。 按一下**下載的中繼資料**]，然後儲存您的電腦上的檔案。  準備編輯此中繼資料檔案上傳至 Qlik 意義伺服器之前。

    b。 按一下 [**下一步**]。

5. 準備同盟中繼資料的 XML 檔案，以便您可以將上傳的 Qlik 有意義的伺服器。

    > [AZURE.NOTE] 上傳之前 IdP 中繼資料至 Qlik 意義伺服器，檔案需要編輯移除為止 Azure AD 之間的資訊和 Qlik 有意義的伺服器。

    ![QlikSense][qs24]

    。 開啟文字編輯器中的 [從 Azure 下載 FederationMetaData.xml 檔案。

    b。 搜尋值**RoleDescriptor**。  會有四個項目 （兩組開頭和結尾的項目標記）。

    c。 從檔案之間刪除 RoleDescriptor 標記及所有的資訊。

    d。 儲存檔案，並保持附近本文稍後的使用。

6. 瀏覽至 Qlik 意義 Qlik 管理主控台 (QMC)，可以建立虛擬 proxy 設定的使用者身分。

7. 在 QMC 中，按一下 [虛擬 Proxy 功能表項目。

    ![QlikSense][qs6] 

8. 在畫面底部，按一下 [建立新的] 按鈕。

    ![QlikSense][qs7]

9. 虛擬 proxy 編輯畫面隨即出現。  在螢幕右側是讓可以看到設定選項的功能表。

    ![QlikSense][qs9]

10. 核取 [識別碼] 功能表選項時，輸入 Azure 虛擬 proxy 設定的識別的資訊。

    ![QlikSense][qs8]  
    
    。 [描述] 欄位是虛擬 proxy 設定好記的名稱。  描述輸入值。
    
    b。 [前置字元] 欄位會識別連線到與 Azure AD 單一登入 Qlik 有意義的虛擬 proxy 端點。  輸入此虛擬 proxy 的唯一的前置詞名稱。

    c。 工作階段閒置逾時 （分鐘） 是連線到此虛擬 proxy 的逾時。

    d。 工作階段 cookie 標頭名稱是 cookie 名稱儲存工作階段識別碼會收到驗證成功之後 Qlik 意義工作階段。  此名稱必須是唯一的。

11. 按一下 [驗證] 功能表選項，使其顯示。  [驗證] 畫面會出現。

    ![QlikSense][qs10]

    。 [**匿名存取模式**] 下拉式會決定匿名使用者可能透過虛擬 proxy 存取 Qlik 具有意義。  預設選項是沒有匿名使用者。

    b。 **驗證方法**下拉式會決定要使用的驗證配置虛擬 proxy。  從下拉式清單中選取 SAML。  更多選項會顯示的結果。

    c。 在**SAML 主機 URI 欄位**中，輸入 [主機名稱使用者將透過這個 SAML 虛擬 proxy 存取 Qlik 意義輸入。  主機名稱是 Qlik 意義伺服器的 uri。

    d。 在**SAML 實體識別碼**中，輸入輸入 SAML 主機 URI 欄位中的值相同。

    e。 **SAML IdP 中繼資料**是先前編輯 [**從 Azure AD 設定編輯同盟中繼資料**] 區段中的檔案。  **需要編輯檔案上傳 IdP 中繼資料之前**移除為止 Azure AD 之間的資訊和 Qlik 有意義的伺服器。  **請如果檔案尚未編輯，參閱上述的指示進行。**  如果檔案已編輯按一下瀏覽] 按鈕並選取編輯的中繼資料要的檔案上傳至虛擬 proxy 設定。

    f。 輸入屬性名稱或代表**使用者識別碼**Azure AD SAML 屬性的結構描述參考會傳送至 Qlik 意義伺服器。  Azure 應用程式的畫面上張貼設定中使用結構描述參考資訊。  若要使用的名稱屬性，**請輸入 http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name**。

    g。 透過 Azure AD Qlik 意義伺服器驗證時將附加至使用者的**使用者目錄**中輸入值。  硬式編碼值必須以**方括弧括**住。  若要使用傳送 Azure AD SAML 判斷提示中的屬性，請在 [此文字] 方塊中**不含**方括號中輸入屬性的名稱。

    h。 **SAML 簽署演算法**設定虛擬 proxy 設定的簽章的服務提供者 （在此情況下 Qlik 意義伺服器） 憑證。  如果 Qlik 意義伺服器使用受信任的憑證產生 Microsoft 增強型 RSA 和 AES 加密提供者使用，變更**sha-256**SAML 簽署演算法。

    我。 傳送到 Qlik 有意義的安全性規則中使用其他屬性，例如群組允許 SAML 屬性對應] 區段。

12. 按一下負載平衡，使其顯示的功能表選項。  負載平衡畫面會出現。

    ![QlikSense][qs11]

13. 按一下 [新增新伺服器節點] 按鈕，選取引擎節點，或節點 Qlik 應會傳送工作階段，負載平衡的目的，並按一下 [新增] 按鈕。

    ![QlikSense][qs12]

14. 按一下 [進階] 功能表中] 選項，使其顯示。 [進階] 畫面會出現。

    ![QlikSense][qs13]

    。 Host （主機） 白色清單識別 hostname 可接受 Qlik 意義伺服器連線時。  **輸入使用者連線至 Qlik 有意義的伺服器時，將會指定主機名稱。** 主機名稱是 SAML 主機 uri https:// 不相同的值。

15. 按一下 [套用] 按鈕。

    ![QlikSense][qs14]

16. 按一下 [確定] 接受 proxy 連結至虛擬 proxy 將會重新啟動的警告訊息。

    ![QlikSense][qs15]

17. 在螢幕右側關聯的項目會出現功能表。  按一下 [Proxy] 功能表選項。

    ![QlikSense][qs16]

18. [Proxy] 畫面會出現。  按一下要連結的虛擬 proxy proxy 底部的 [連結] 按鈕。

    ![QlikSense][qs17]

19. 選取 proxy 節點將支援此虛擬 proxy 連線，然後按一下 [連結] 按鈕。  之後連結、 proxy 就會列出下方相關聯的 proxy。

    ![QlikSense][qs18]
    ![QlikSense][qs19]

20. 關於 5 到 10 秒後，會出現 [重新整理 QMC 訊息。  按一下 [重新整理 QMC] 按鈕。

    ![QlikSense][qs20]

21. 當 QMC 重新整理時，按一下 [虛擬 proxy 功能表項目。 在螢幕上表所列的新 SAML 虛擬 proxy 項目。  虛擬 proxy 項目上按一下。

    ![QlikSense][qs51]

22. 在畫面底部，會啟動 [下載預存程序的中繼資料] 按鈕。  按一下 [下載預存程序的中繼資料] 按鈕將中繼資料儲存為檔案。

    ![QlikSense][qs52]

23. 開啟預存程序的中繼資料檔案。  您會看到**entityID**項目]，[ **AssertionConsumerService**項目。  這些值相當於**識別碼**，**在 URL 上登**入 Azure AD 應用程式設定。 如果不比對然後您應該取代它們 Azure AD 應用程式設定精靈] 中。

    ![QlikSense][qs53]

24. 在 [傳統] 入口網站，選取單一登入設定確認，然後按 [**下一步**。
    
    ![Azure AD 單一登入][10]

25. 在**單一登入確認**] 頁面上，按一下 [**完成**]。  
 
    ![Azure AD 單一登入][11]


### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
在此區段中，您可以建立在傳統入口網站中名為許承恩測試使用者。


![建立 Azure AD 使用者][20]

**若要建立的測試使用者 Azure AD 中，執行下列步驟︰**

1. 在**Azure 傳統入口網站**，在左側的功能窗格中，按一下 [ **Active Directory**]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_09.png) 

2. 從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3. 若要顯示的使用者清單頂端的功能表中，按一下 [**使用者**]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_03.png) 

4. 若要開啟 [**新增使用者**] 對話方塊底部的工具列中，按一下 [**新增使用者**]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_04.png) 

5. 在 [**告訴我們瞭解此使用者**的對話方塊頁面，請執行下列步驟︰ ![建立 Azure AD 測試使用者](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_05.png) 

    。 為使用者類型，選取您組織中的 [新的使用者]。

    b。 在 [使用者名稱]**文字方塊**中，輸入**BrittaSimon**。

    c。 按一下 [**下一步**]。

6.  在 [**使用者設定檔**] 對話方塊頁面，請執行下列步驟︰![建立 Azure AD 測試使用者](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_06.png) 

    。 在 [**名字**] 文字方塊中輸入**許**。  

    b。 在 [**姓氏**] 文字方塊中輸入，**承恩**。

    c。 在 [**顯示名稱**] 文字方塊中，輸入**許承恩**。

    d。 在 [**角色**] 清單中選取 [**使用者**]。

    e。 按一下 [**下一步**]。

7. 在 [**取得暫時密碼**] 對話方塊頁面上，按一下 [**建立**]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_07.png) 

8. **取得暫時密碼**] 對話方塊在頁面上，執行下列步驟︰

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_08.png) 

    。 寫下的值的**新密碼**。

    b。 按一下 [**完成**]。   


### <a name="creating-an-qlik-sense-enterprise-test-user"></a>建立 Qlik 理解企業測試使用者

在此區段中，您可以建立稱為許承恩 Qlik 理解企業中的使用者。 請使用 Qlik 理解企業支援小組，將使用者新增 Qlik 理解企業平台。


### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在此區段中，您可以啟用許承恩 Azure 單一登入使用其存取權授與 Qlik 理解企業版。

![為使用者指派][200] 

**若要指定許承恩 Qlik 理解企業版，請執行下列步驟︰**

1. 在 [傳統] 入口網站，若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [**應用程式**頂端的功能表中。

    ![為使用者指派][201] 

2. 在應用程式清單中，選取 [ **Qlik 理解企業版**]。

    ![設定單一登入](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_50.png) 

3. 在頂端的功能表，按一下 [**使用者**]。

    ![為使用者指派][203]

4. 在 [使用者] 清單中，選取**許承恩**。

5. 在底部工具列中，按一下 [**指派**]。

    ![為使用者指派][205]


## <a name="testing-single-sign-on"></a>測試單一登入

在此區段中，您測試 Azure AD 單一登入設定使用 Access 面板。

當您按一下 [Access] 面板中的 [Qlik 理解企業] 方塊時，您應該取得自動登入 Qlik 理解企業應用程式。


## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程的清單](active-directory-saas-tutorial-list.md)
* [什麼是應用程式存取和單一登入與 Azure Active Directory？](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_205.png

[qs6]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_06.png
[qs7]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_07.png
[qs8]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_08.png
[qs9]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_09.png
[qs10]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_10.png
[qs11]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_11.png
[qs12]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_12.png
[qs13]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_13.png
[qs14]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_14.png
[qs15]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_15.png
[qs16]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_16.png
[qs17]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_17.png
[qs18]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_18.png
[qs19]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_19.png
[qs20]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_20.png
[qs21]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_21.png
[qs22]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_22.png
[qs23]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_23.png
[qs24]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_24.png
[qs25]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_25.png
[qs26]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_26.png
[qs51]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_51.png
[qs52]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_52.png
[qs53]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_53.png