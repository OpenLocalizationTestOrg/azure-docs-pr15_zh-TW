<properties
    pageTitle="教學課程︰ Azure Active Directory 整合 Evidence.com |Microsoft Azure"
    description="瞭解如何設定單一登入 Azure Active Directory 和 Evidence.com 之間。"
    services="active-directory"
    documentationCenter=""
    authors="asmalser-msft"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/23/2016"
    ms.author="asmalser"/>


# <a name="tutorial-azure-active-directory-integration-with-evidencecom"></a>教學課程︰ 使用 Evidence.com 的 Azure Active Directory 整合

本教學課程中的目標是要如何設定 [單一登入 Azure Active Directory (AAD) 和 Evidence.com 之間。 本教學課程中所述的案例假設您已經有下列項目︰
    
* 有效的 Microsoft Azure 訂閱
* 單一登入 Evidence.com 訂閱啟用 (電子郵件earlyaccess@evidence.com如果未啟用以 SAML 為基礎單一登入)

完成本教學課程後，對象您已指派 Evidence.com 存取 AAD 使用者會無法單一的登入使用 AAD 存取畫面的應用程式。

## <a name="add-evidencecom-to-your-directory"></a>新增 Evidence.com 至您的目錄

本節說明如何新增 Evidence.com 為 Azure Active Directory 中整合式應用程式。

**若要啟用證據的整合應用程式︰**

1.  在[Azure 傳統入口網站](https://manage.windowsazure.com)，在左側的功能窗格中，按一下 [ **Active Directory**]。

2.  從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [在上方的功能表中的 [**應用程式**]。

4.  若要開啟應用程式庫，按一下 [**新增**]，然後再按一下 [**新增應用程式，從圖庫**。

5.  在 [搜尋] 方塊中，輸入**Evidence.com**。

6.  在 [結果] 窗格中，選取**Evidence.com**，，然後按一下要新增應用程式**完成**。


## <a name="configuring-single-sign-on"></a>設定單一登入

本節說明如何讓使用者以 Evidence.com 的 Azure Active Directory，使用 SAML 通訊協定所根據的同盟其帳戶驗證方法。

**若要設定單一登入，請執行下列步驟︰**

1.  新增後 Evidence.com Azure 傳統入口網站中，按一下 [**設定單一登入**]。 
 
2.  在下一個畫面中，選取**Azure AD 單一登入**，然後按 [**下一步**。

3.  在 [設定應用程式 URL] 畫面中，輸入的 URL 會將您 Evidence.com 租用戶的 URL 登入使用者 (範例︰ https://yourtenant.evidence.com，然後按 [**下一步**。 

4.  按一下 [**下載憑證**] 連結，並將其儲存至本機磁碟。 這個憑證和中繼資料 Url (實體識別碼、 SSO 登入 URL 及登出 URL） 將用於設定 SSO Evidence.com 網站上。 

5.  在另一個網頁瀏覽器視窗中，登入您 Evidence.com 租用戶系統管理員身分，並瀏覽至 [**管理**] 索引標籤
      
6.  按一下 [在**公司的單一登入**
 
7.  選取 [**以 SAML 基礎單一登入**
 
8.  **單一登入**與**單一登出**顯示在 Azure 傳統入口網站和 Evidence.com 的對應欄位的值，複製**發行者 URL**。

9.  開啟憑證下載在步驟 4 使用 Notepad.exe 等文字編輯器中，複製並貼上內容到**安全性憑證**] 方塊。 

10. 設定儲存 Evidence.com。
 
11. 在 Azure 傳統的入口網站中，核取**您已設定單一登入如上所述的確認**。 檢查這可讓目前的憑證，若要開始使用此應用程式] 核取方塊。
 
12. 在單一登入確認] 頁面上，按一下 [**完成**]。  


## <a name="creating-an-evidencecom-test-user"></a>建立 Evidence.com 測試使用者

Azure AD 使用者可以登入，他們必須提供存取內 Evidence.com 應用程式。 本節說明如何建立 Azure AD Evidence.com 內的使用者帳戶

**佈建 Evidence.com 中的使用者帳戶︰**

1.  在網頁瀏覽器視窗中，以系統管理員身分登入您 Evidence.com 公司的網站。

2.  瀏覽至 [**管理**] 索引標籤。

3.  按一下 [**新增使用者**]。

4.  按一下 [**新增**] 按鈕。

5.  新增使用者的**電子郵件地址**必須符合使用者的名稱，在您想要授與存取權的 Azure AD。 如果使用者名稱和電子郵件地址不是您組織中的值相同，您可以使用**Evidence.com > 屬性 > 單一登入**Azure 傳統的入口網站，以變更為電子郵件地址傳送至 Evidence.com nameidenitifer 一節。


## <a name="assigning-users-to-evidencecom"></a>將使用者指派至 Evidence.com

能夠 AAD 要讓使用者能夠看到 Evidence.com 其存取面板上，他們必須被指派內 Azure 傳統入口網站的存取權。

**將使用者指派給 Evidence.com:**

1.  在 [快速入門] 頁面的 Evidence.com Azure 傳統入口網站中，按一下 [**指派 Evidence.com 使用者**。
 
2.  在 [**顯示**] 功能表中，選取您想要 Evidence.com，以指定使用者或群組，然後按一下核取記號按鈕。
 
3.  在 [**使用者**] 清單中選取使用者群組至您想要指派 Evidence.com 的人員。
 
4.  在頁尾中，按一下 [**指派**] 按鈕。

