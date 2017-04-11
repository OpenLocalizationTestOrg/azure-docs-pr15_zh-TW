<properties 
    pageTitle="教學課程︰ 針對輸入同步處理設定 Workday |Microsoft Azure" 
    description="瞭解如何使用 Azure Active Directory 中的連入同步處理，來啟用單一登入、 自動化佈建和更多 ！" 
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
    ms.date="04/06/2016" 
    ms.author="jeedes" />

#<a name="tutorial-configuring-workday-for-inbound-synchronization"></a>教學課程︰ 設定工作日的連入同步處理
>[AZURE.NOTE]Azure Active Directory (AD) 進階版適用於中國的客戶使用 Azure AD 的全球執行個體。    
Azure AD 進階版目前不支援在中國的 21Vianet 所營運的 Microsoft Azure 服務。    

本教學課程中的目標是以顯示您必須先在 Workday 與 Microsoft Azure AD 從 Workday 的人員匯入至 Microsoft Azure AD 中執行的步驟。    
 本教學課程中所述的案例假設您已經有下列項目︰  

-   有效的 Azure 訂閱  
-   租用戶中 Workday  

本教學課程中所述的案例是由下列建置組塊所組成︰  

1.  啟用整合應用程式的工作日  
2.  建立整合系統使用者  
3.  建立安全性群組  
4.  指派整合系統使用者的安全性群組  
5.  設定安全性群組選項  
6.  啟動安全性原則變更  
7.  在 Microsoft Azure AD 設定的使用者匯入  

##<a name="enabling-the-application-integration-for-workday"></a>啟用整合應用程式的工作日

本節的目標是大綱如何啟用的 Salesforce 整合應用程式。    

###<a name="to-enable-the-application-integration-for-workday-perform-the-following-steps"></a>若要啟用整合應用程式的工作日，請執行下列步驟︰

1.  在 Azure 管理入口網站，在左側的功能窗格中，按一下 [ **Active Directory**]。    

    ![Active Directory](./media/active-directory-saas-inbound-synchronization-tutorial/IC700993.png "Active Directory")  

2.  從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。    

3.  若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [在上方的功能表中的 [**應用程式**]。    

    ![應用程式](./media/active-directory-saas-inbound-synchronization-tutorial/IC700994.png "應用程式")  

4.  若要開啟**應用程式組件庫**，按一下 [**新增應用程式**]，再**新增我的組織使用的應用程式**。    

    ![您想要做什麼？](./media/active-directory-saas-inbound-synchronization-tutorial/IC700995.png "您想要做什麼？")  

5.  在**搜尋] 方塊**中，輸入**Workday**。    

    ![workday](./media/active-directory-saas-inbound-synchronization-tutorial/IC701021.png "workday")  

6.  在 [結果] 窗格中，選取**工作日**，，然後按一下要新增應用程式**完成**。    

    ![workday](./media/active-directory-saas-inbound-synchronization-tutorial/IC701022.png "workday")  

##<a name="creating-an-integration-system-user"></a>建立整合系統使用者

1.  **Workday 工作**中，在 [搜尋] 方塊中輸入 [**建立使用者**，然後按一下連結，**建立整合系統的使用者**。     

    ![建立使用者](./media/active-directory-saas-inbound-synchronization-tutorial/IC750979.png "建立使用者")  

2.  完成建立整合系統使用者工作，為新的整合系統使用者提供使用者名稱和密碼。  將在下一個登入] 選項，需要的新密碼保留未選取，因為此使用者會被登入以程式設計方式。    
    讓它的預設值為 0，會防止使用者的工作階段逾時提前工作階段逾時分鐘數。    

    ![建立整合系統的使用者](./media/active-directory-saas-inbound-synchronization-tutorial/IC750980.png "建立整合系統的使用者")  

##<a name="creating-a-security-group"></a>建立安全性群組

本教學課程中所述的案例中，您需要建立不受限制的整合系統安全性群組，並為其指派給使用者。    

1.  輸入 [搜尋] 方塊中建立安全性群組，然後按一下連結，建立安全性群組。     

    ![CreateSecurity 群組](./media/active-directory-saas-inbound-synchronization-tutorial/IC750981.png "CreateSecurity 群組")  

2.  完成建立安全性群組的工作。  選取 [整合系統安全性群組，受限制地從類型的 Tenanted 安全性群組] 下拉式清單中，若要建立或修改的群組成員會明確加入。     

    ![CreateSecurity 群組](./media/active-directory-saas-inbound-synchronization-tutorial/IC750982.png "CreateSecurity 群組")  

##<a name="assigning-the-integration-system-user-to-the-security-group"></a>指派整合系統使用者的安全性群組

1.  在 [搜尋] 方塊中輸入編輯安全性群組，然後按一下連結，**編輯安全性群組**。     

    ![編輯安全性群組](./media/active-directory-saas-inbound-synchronization-tutorial/IC750983.png "編輯安全性群組")  

2.  搜尋，並選取新的整合安全性群組名稱    

    ![編輯安全性群組](./media/active-directory-saas-inbound-synchronization-tutorial/IC750984.png "編輯安全性群組")  

3.  新增新的整合系統使用者至新的安全性群組。       

    ![系統安全性群組](./media/active-directory-saas-inbound-synchronization-tutorial/IC750985.png "系統安全性群組")  

##<a name="configuring-security-group-options"></a>設定安全性群組選項

在此步驟中，您授與新安全性群組的權限] 物件受到下列網域安全性原則取得與放入作業︰  

-   外部帳戶佈建  
-   背景工作資料︰ 公用工作報表  
-   背景工作資料︰ 所有位置  
-   專業人員的資訊工作者資料︰ 目前  
-   在背景工作設定檔的工作者資料︰ 商務標題  

&nbsp;  

1.  在 [搜尋] 方塊中輸入網域安全性原則，然後按一下連結，也就是網域安全性原則功能區上的。     

    ![網域安全性原則](./media/active-directory-saas-inbound-synchronization-tutorial/IC750986.png "網域安全性原則")  

2.  搜尋系統，並選取 [系統運作的區域。  按一下 [] 按鈕上標示，[確定]。     

    ![網域安全性原則](./media/active-directory-saas-inbound-synchronization-tutorial/IC750987.png "網域安全性原則")  

3.  在清單中的 [系統] 功能區域的安全性原則，展開安全性管理並選取網域安全性原則，外部帳戶佈建。     

    ![網域安全性原則](./media/active-directory-saas-inbound-synchronization-tutorial/IC750988.png "網域安全性原則")  

4.  按一下 [編輯權限] 按鈕，然後在 [編輯權限] 畫面中，將新的安全性群組新增至安全性群組，以取得和放入的整合權限的清單。     

    ![編輯權限](./media/active-directory-saas-inbound-synchronization-tutorial/IC750989.png "編輯權限")  

5.  重複上述步驟 1，返回 [選取功能區，畫面並這次請搜尋人員，選取 [Staffing 功能] 區域中，按一下 [標示為，[確定] 按鈕。    

    ![網域安全性原則](./media/active-directory-saas-inbound-synchronization-tutorial/IC750990.png "網域安全性原則")  

6.  在清單中的 Staffing 功能區域的安全性原則，展開 [背景工作資料︰ Staffing，然後重複步驟 4 上述每個剩餘安全性原則︰    

    -   背景工作資料︰ 公用背景工作報表  
    -   背景工作資料︰ 所有位置  
    -   專業人員的資訊工作者資料︰ 目前  
    -   在背景工作設定檔的工作者資料︰ 商務標題    

    ![網域安全性原則](./media/active-directory-saas-inbound-synchronization-tutorial/IC750991.png "網域安全性原則")  

##<a name="activating-security-policy-changes"></a>啟動安全性原則變更

1.  輸入啟動 [搜尋] 方塊中，然後按一下 [連結，啟動擱置的安全性原則變更。    

    ![啟動](./media/active-directory-saas-inbound-synchronization-tutorial/IC750992.png "啟動")  

2.   輸入註解稽核作業，然後按一下 [] 按鈕上標示，[確定]，開始啟動擱置的安全性原則變更工作。      

    ![啟動擱置的安全性](./media/active-directory-saas-inbound-synchronization-tutorial/IC750993.png "啟動擱置的安全性")  

3.  完成的下一個畫面上的工作，請核取標示為 [確認並按一下 [標示為，[確定] 按鈕上的核取方塊。     

    ![啟動擱置的安全性](./media/active-directory-saas-inbound-synchronization-tutorial/IC750994.png "啟動擱置的安全性")  

##<a name="configuring-user-import-in-microsoft-azure-ad"></a>在 Microsoft Azure AD 設定的使用者匯入

本節的目標是大綱如何設定 Microsoft Azure AD 從 Workday 匯入的人員。    

###<a name="to-configure-user-import-in-microsoft-azure-ad-perform-the-following-steps"></a>若要設定使用者匯入 Microsoft Azure AD 中，執行下列步驟︰

1.  **Workday**應用程式整合在頁面上，按一下 [開啟 [**設定佈建**] 對話方塊的 [**設定使用者匯入**。    

2.  在**設定及管理認證**] 頁面上，執行下列步驟，然後再按 [下一步︰    

    ![設定和管理員認證](./media/active-directory-saas-inbound-synchronization-tutorial/IC750995.png "設定和管理員認證")    

    1.  在 [ **Workday 管理員使用者名稱**] 文字方塊中，輸入您[建立的整合系統的使用者](https://msdn.microsoft.com/library/azure/Dn762434.aspx#BKMK_CreateUser)] 區段中所建立的使用者名稱。    
    2.  在 [ **Workday 管理員密碼**] 文字方塊中，輸入您在[建立整合系統的使用者](https://msdn.microsoft.com/library/azure/Dn762434.aspx#BKMK_CreateUser)] 區段中建立使用者的密碼。    
    3.  在 [ **Workday 租用戶 URL** ] 文字方塊中輸入 URL 或 Workday 租用戶。    

3.  在**[測試連線]**頁面上，按一下 [**開始測試**確認連線，然後按 [**下一步**。    

    ![測試連線](./media/active-directory-saas-inbound-synchronization-tutorial/IC750996.png "測試連線")  

4.  在**提供選項**頁面上，按一下 [**下一步**]。    

    ![佈建選項](./media/active-directory-saas-inbound-synchronization-tutorial/IC750997.png "佈建選項")  

5.  在 [**開始佈建**] 對話方塊中，按一下 [**完成**]。    

    ![啟動佈建](./media/active-directory-saas-inbound-synchronization-tutorial/IC750998.png "啟動佈建")  

您現在可以移至 [**使用者**] 區段，並檢查是否已匯入您的工作日使用者。    
