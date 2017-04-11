<properties
    pageTitle="設定使用 Azure Active Directory 裝置註冊內部部署條件存取權限 |Microsoft Azure"
    description="若要啟用條件存取內部部署的應用程式在 Windows Server 2012 R2 使用 Active Directory Federation 服務 (AD FS) 的逐步指南。"
    services="active-directory"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="femila"/>


# <a name="setting-up-on-premises-conditional-access-using-azure-active-directory-device-registration"></a>設定使用 Azure Active Directory 裝置註冊內部部署條件存取權限

個人可以至您的組織要求使用者的工作位置加入至 Azure Active Directory 裝置註冊服務其裝置的已知會標示的使用者擁有的裝置。 以下是啟用條件存取內部部署的應用程式在 Windows Server 2012 R2 使用 Active Directory Federation 服務 (AD FS) 的逐步指南。

> [AZURE.NOTE]
> Office 365 授權或 Azure AD 進階版授權時所使用的裝置註冊 Azure Active Directory 裝置註冊服務條件存取原則中。 這包含內部部署資源 Active Directory Federation Services (AD FS) 強制執行原則。

如需內部部署的條件存取案例的詳細資訊，請參閱[加入工作地點，從任何裝置 SSO 和順暢第二個因素驗證跨公司應用程式](https://technet.microsoft.com/library/dn280945.aspx)。

這些功能可供購買 Azure Active Directory 進階版授權的客戶。

<a name="supported-devices"></a>支援的裝置
-------------------------------------------------------------------------
* Windows 7 加入網域的裝置。
* Windows 8.1 個人和網域，連接裝置。
* iOS 6 及更新版本，用於 Safari 瀏覽器
* Android 4.0 或更新版本，針對 Samsung GS3 或上方 Samsung 註 2 電話或平板電腦。


<a name="scenario-prerequisites"></a>案例的先決條件
------------------------------------------------------------------------
* Office 365 訂閱或 Azure Active Directory 進階版
* Azure Active Directory 租用戶
* Windows Server Active Directory (Windows Server 2008 或更新)
* 在 Windows Server 2012 R2 中更新的結構描述
* Azure Active Directory 進階版的授權
* Windows Server 2012 R2 Federation Services，Azure ad SSO 設定
* Windows Server 2012 R2 Web 應用程式 Proxy Microsoft Azure Active Directory 連線 (Azure AD Connect)。 [下載 Azure AD Connect](http://www.microsoft.com/en-us/download/details.aspx?id=47594)。
* 驗證的網域。

<a name="known-issues-in-this-release"></a>在這個版本中的已知的問題
-------------------------------------------------------------------------------
* 裝置根據條件存取原則需要從 Azure Active Directory Active Directory 裝置物件回寫功能。 可能需要長達 3 小時到 Active Directory 是回寫功能的裝置物件
* iOS 7 裝置一律會提示使用者選取期間用戶端憑證驗證的憑證。
* IOS8 之前 iOS 8.3，無法使用某些版本。

## <a name="scenario-assumptions"></a>案例假設
此案例假設您有包含 Azure AD 租用戶和內部部署的 Active Directory 混合式環境。 這些租用戶應該使用 Azure AD Connect 連線和驗證的網域與 AD FS 的 SSO。 下列檢查清單，可協助您設定您的環境上述階段。

<a name="checklist-prerequisites-for-conditional-access-scenario"></a>檢查清單︰ 先決條件條件存取的案例
--------------------------------------------------------------
將您的內部部署 Active Directory 連線到您 Azure AD 租用戶。

## <a name="configure-azure-active-directory-device-registration-service"></a>設定 Azure Active Directory 裝置註冊服務
使用本指南部署，並為您的組織設定 Azure Active Directory 裝置註冊服務。

本指南假設您已設定 Windows Server Active Directory 和 Microsoft Azure Active Directory 訂閱。 請參閱上述的先決條件。

若要使用您的 Azure Active Directory 租用戶部署 Azure Active Directory 裝置註冊服務，完成順序中的下列檢查清單中的工作。 當參照連結會帶您到概念性主題時，返回這份檢查清單之後您要檢閱的概念性主題，好讓您可以進行這份檢查清單中的剩餘工作。 某些工作會包含案例驗證步驟可協助您確認順利完成此步驟。

## <a name="part-1-enable-azure-active-directory-device-registration"></a>第 1 部分︰ 啟用 Azure Active Directory 裝置註冊

請遵循下列啟用和設定 Azure Active Directory 裝置註冊服務的檢查清單。

| 任務                                                                                                                                                                                                                                                                                                                                                                                             | 參照                                                       |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| 啟用裝置註冊 Azure Active Directory 租用戶允許加入工作地點的裝置。 根據預設，多重因素驗證未啟用服務。 不過，註冊裝置時，會建議多重因素驗證。 啟用之前多重因素驗證 ADRS 中的，確定 AD FS 設定多重因素驗證提供者。 | [啟用 Azure Active Directory 裝置註冊](active-directory-conditional-access-device-registration-overview.md)               |
| 裝置會發現您 Azure Active Directory 裝置註冊服務尋找已知的 DNS 記錄。 讓裝置可能會發現您 Azure Active Directory 裝置註冊服務，您必須設定您的公司 DNS。                                                                                                                                                   | [設定 Azure Active Directory 裝置註冊探索。](active-directory-conditional-access-device-registration-overview.md) |

##<a name="part-2-deploy-and-configure-windows-server-2012-r2-active-directory-federation-services-and-set-up-a-federation-relationship-with-azure-ad"></a>第 2 部分︰ 部署及設定 Windows Server 2012 R2 Active Directory Federation Services，並設定 Azure AD 的同盟關係

| 任務                                                                                                                                                                                                                                                                                                                                                                                             | 參照                                                       |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| 部署 Active Directory 網域服務與 Windows Server 2012 R2 結構描述延伸的網域。 您不需要任何網域控制站升級至 Windows Server 2012 R2。 結構描述升級是唯一的需求。 | [升級您的 Active Directory 網域服務結構描述](#upgrade-your-active-directory-domain-services-schema)               |
| 裝置會發現您 Azure Active Directory 裝置註冊服務尋找已知的 DNS 記錄。 您必須先設定您公司的 DNS，以便裝置可能會發現您 Azure Active Directory 裝置註冊服務。                                                                                                                                                   | [準備 Active Directory 支援裝置](#prepare-your-active-directory-to-support-devices) |


##<a name="part-3-enable-device-writeback-in-azure-ad"></a>Azure AD 的第 3 部分︰ 啟用裝置回寫

| 任務                                                                                                                                                                                                                                                                                                                                                                                             | 參照                                                       |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| 完成 Azure AD Connect 啟用裝置回寫第 2 的部分。 完成時，會傳回這本指南。 | [啟用裝置回寫 Azure AD Connect](#upgrade-your-active-directory-domain-services-schema)               |


##<a name="optional-part-4-enable-multi-factor-authentication"></a>[選擇性]第 4 部分︰ 啟用多重因素驗證

強烈建議您其中一個選項多重因素驗證設定。 如果您想要需要 MFA，請參閱[選擇多重因素安全性的解決方案](../multi-factor-authentication/multi-factor-authentication-get-started.md)。 其包含描述每個方案，並協助您設定您所選擇的解決方案的連結。

## <a name="part-5-verification"></a>第 5 部分︰ 驗證

部署現已完成。 您現在可以嘗試某些案例。 請遵循下列連結，可嘗試使用的服務，以及熟悉功能


| 任務                                                                                                                                                                                                                         | 參照                                                                       |
|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------|
| 加入使用 Azure Active Directory 裝置註冊您工作地點的某些裝置。 您可以加入 iOS、 Windows 和 Android 裝置                                                                                         | [加入使用 Azure Active Directory 裝置註冊您工作地點的裝置](#join-devices-to-your-workplace-using-azure-active-directory-device-registration) |
| 您可以檢視並啟用或停用註冊的裝置使用系統管理員] 入口網站。 在這項任務中，您將檢視使用系統管理員中心某些註冊的裝置。                                                        | [Azure Active Directory 裝置註冊概觀](active-directory-conditional-access-device-registration-overview.md)                             |
| 確認裝置物件會寫入回從 Azure Active Directory 到 Windows Server Active Directory。                                                                                                                  | [確認已註冊的裝置到 Active Directory 是回寫功能](#verify-registered-devices-are-written-back-to-active-directory)                  |
| 現在，使用者可以註冊其裝置，您可以建立應用程式存取原則允許註冊的裝置的 AD FS 中。 在此工作中，您將建立的應用程式存取規則] 和 [自訂存取被拒絕的訊息 | [建立應用程式存取原則和自訂拒絕存取的訊息](#create-an-application-access-policy-and-custom-access-denied-message)            |



## <a name="integrate-azure-active-directory-with-on-premises-active-directory"></a>整合內部部署的 Active Directory 中的 Azure Active Directory
這將可協助您與您內部部署的 active directory，使用 Azure AD Connect 整合 Azure AD 租用戶。 雖然 Azure 傳統入口網站提供的步驟，請記任何的下特殊列在本節中的指示。

1.  Azure 傳統入口網站使用的是 Azure AD 全域管理員帳戶登入。
2.  在左窗格中，選取 [ **Active Directory**]。
3.  在 [**目錄**] 索引標籤中，選取 [目錄]。
4.  選取 [**目錄整合**] 索引標籤。
5.  **部署及管理**] 區段下，請依照下列步驟 1 到 3，與您的內部部署目錄整合 Azure Active Directory。
  1.    將網域新增。
  2.    安裝及執行 Azure AD Connect︰ 安裝 Azure AD Connect 使用下列指示，也就是[Azure AD Connect 自訂安裝](./connect/active-directory-aadconnect-get-started-custom.md)。
  3. 驗證與管理目錄同步處理。 單一登入指示，可在此步驟中。

  > [AZURE.NOTE]
  > 設定與 AD FS 同盟，上方連結的文件中所述。 您不需要設定任何預覽功能。


## <a name="upgrade-your-active-directory-domain-services-schema"></a>升級您的 Active Directory 網域服務結構描述

> [AZURE.NOTE]
> 升級您的 Active Directory 結構描述無法還原。 建議您先執行此測試環境中。

1. 登入您的網域控制站擁有企業系統管理員和結構描述管理員權限的帳戶。
2. 將**[媒體] \support\adprep**目錄和子目錄複製到其中一個 Active Directory 網域控制站。
3. [媒體] 在哪裡 Windows Server 2012 R2 安裝媒體的路徑。
4. 在命令提示字元中，瀏覽至 adprep 目錄，並執行︰ **adprep.exe /forestprep**。 請依照畫面上的指示完成結構描述升級。

## <a name="prepare-your-active-directory-to-support-devices"></a>準備您的 Active Directory 支援的裝置

>[AZURE.NOTE] 這是您準備 Active Directory 樹系支援的裝置必須執行一次作業。 您必須具有企業系統管理員權限登入，您的 Active Directory 樹系必須完成此程序的 Windows Server 2012 R2 結構描述。


##<a name="prepare-your-active-directory-forest-to-support-devices"></a>準備 Active Directory 樹系支援的裝置

> [AZURE.NOTE]
>這是您準備 Active Directory 樹系支援的裝置必須執行一次作業。 您必須具有企業系統管理員權限登入，您的 Active Directory 樹系必須完成此程序的 Windows Server 2012 R2 結構描述。

### <a name="prepare-your-active-directory-forest"></a>準備 Active Directory 樹系

1.  在您同盟伺服器上，開啟 [Windows PowerShell 命令視窗並鍵入︰ 初始化 ADDeviceRegistration
2.  出現提示時的**ServiceAccountName**，輸入您選取作為服務帳戶，為 AD FS 服務帳戶的名稱。 如果這是 gMSA 帳戶，請**domain\accountname$**格式中輸入的帳戶。 網域帳戶，請使用格式**domain\accountname**。



### <a name="enable-device-authentication-in-ad-fs"></a>啟用裝置驗證 AD FS 中

1. 在您同盟伺服器上，開啟 AD FS 管理主控台，然後瀏覽至**AD FS** > **驗證原則**。
2. 選取 [**編輯...全域主要驗證** 從 [**動作**] 窗格中。
3. 核取 [**啟用裝置驗證**，然後按一下**[確定]**。
4. 根據預設，AD FS 會定期從 Active Directory 移除未使用的裝置。 使用 Azure Active Directory 裝置註冊，以便可以 Azure 中管理裝置時，您必須停用這項工作。


### <a name="disable-unused-device-cleanup"></a>停用未使用的裝置清理
1. 在您同盟伺服器上，開啟 [Windows PowerShell 命令視窗並鍵入︰ 設定 AdfsDeviceRegistration MaximumInactiveDays 0

### <a name="prepare-azure-ad-connect-for-device-writeback"></a>Azure AD Connect 準備裝置回寫

1.  完成第 1 部分︰ 準備 Azure AD 連線。


## <a name="join-devices-to-your-workplace-using-azure-active-directory-device-registration"></a>加入使用 Azure Active Directory 裝置註冊您工作地點的裝置

### <a name="join-an-ios-device-using-azure-active-directory-device-registration"></a>加入 iOS 裝置使用 Azure Active Directory 裝置註冊

Azure Active Directory 裝置註冊使用 iOS 裝置 Over-the-Air 設定檔註冊程序。 此程序開頭為使用者設定檔註冊 url 使用 Safari 網頁瀏覽器連線]。 URL 格式如下所示︰

    https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/"yourdomainname"

位置`yourdomainname`是您已設定與 Azure Active Directory 網域名稱。 例如，如果您的網域名稱是 contoso.com，就會 URL:

    https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/contoso.com

有許多不同的方式來傳達給您的使用者將此 URL。 其中一個建議的方法是在 access 中自訂應用程式發佈此 URL 拒絕 AD FS 中的郵件。 這涵蓋在即將來臨] 區段中︰[建立應用程式存取原則和自訂拒絕存取的訊息](#create-an-application-access-policy-and-custom-access-denied-message)。

###<a name="join-a-windows-81-device-using-azure-active-directory-device-registration"></a>加入使用 Azure Active Directory 裝置註冊 Windows 8.1 裝置

1. Windows 8.1 裝置上瀏覽至 [**電腦設定** > **網路** > **地點**。
2. UPN 格式輸入您的使用者名稱。 例如， dan@contoso.com.。
3. 選取 [**加入**]。
4. 出現提示時，使用登入您的認證。 裝置現在已加入。

###<a name="join-a-windows-7-device-using-azure-active-directory-device-registration"></a>加入使用 Azure Active Directory 裝置註冊 Windows 7 裝置
若要註冊 Windows 7 網域加入部署裝置註冊軟體套件所需的裝置。 軟體套件稱為地點加入的 Windows 7，而且可供下載[Microsoft 連線網站](https://connect.microsoft.com/site1164)。 瞭解如何使用封裝可在[Windows 7 網域設定自動裝置註冊加入裝置](active-directory-conditional-access-automatic-device-registration-windows7.md)的相關指示。

### <a name="join-an-android-device-using-azure-active-directory-device-registration"></a>加入 Android 裝置使用 Azure Active Directory 裝置註冊

[Azure 驗證 Android 主題](active-directory-conditional-access-azure-authenticator-app.md)包含如何在 Android 裝置上安裝 Azure 驗證應用程式，並新增公司帳戶的相關指示。 在 Android 裝置上已成功建立公司帳戶時，該裝置是加入組織的地點。

## <a name="verify-registered-devices-are-written-back-to-active-directory"></a>確認已註冊的裝置到 Active Directory 是回寫功能
您可以檢視，並確認您的裝置物件有已寫入回到您的 Active Directory 使用 LDP.exe 或 Adsi。 同時，可使用 Active Directory 系統管理員的工具。

根據預設，從 Azure Active Directory 回寫功能的裝置物件將置於與 AD FS 陣列相同的網域。

    CN=RegisteredDevices,defaultNamingContext

## <a name="create-an-application-access-policy-and-custom-access-denied-message"></a>建立應用程式存取原則和自訂拒絕存取的訊息
請考慮下列案例︰ 您建立的應用程式中 AD FS Relying 廠商信任，並設定發行授權規則，允許註冊的裝置。 現在已註冊的裝置可存取應用程式。 若要輕鬆存取應用程式使用者，您可以設定自訂拒絕存取的訊息，其中包含如何加入他們的裝置上的指示進行。 現在您的使用者會有順暢的方式，才能存取應用程式註冊其裝置。

下列步驟說明如何實作這種情況。

>[AZURE.NOTE]
本節假設您有已設定依賴廠商信任 AD FS 中的應用程式。

1. 開啟 [AD FS MMC 工具，然後瀏覽至 AD FS > 信任關係 > 依賴廠商信任。
2. 找出這個新存取規則會套用在其應用程式。 以滑鼠右鍵按一下 [應用程式，然後選取 [編輯宣告規則...
3. 選取 [**發行授權規則**] 索引標籤，然後選取 [**新增規則...**
4. 從 [**宣告規則**範本下拉式清單中，選取 [**允許] 或 [拒絕使用者根據連入宣告**。 選取 [**下一步**]。
5. 在 [宣告規則名稱︰ 欄位類型︰**允許從註冊裝置存取**
6. 從 [內送宣告類型︰ 下拉式清單，請選取 [**已註冊的使用者**。
7. 在 [內送宣告值︰ 欄位中，輸入: **，則為 true**
8. 選取 [**允許使用者使用此連入宣告的存取權**] 選項按鈕。
9. 選取 [**完成**]，然後選取 [**套用]**。
10. 移除任何其他寬鬆比您剛剛建立的規則的規則。 例如，移除預設的**所有使用者的 [允許存取**規則。

應用程式現在使用者來自他們在哪裡註冊及加入工作地點的裝置時，只允許存取設定。 更多進階的存取原則，請參閱[管理與多重因素存取控制的風險](https://technet.microsoft.com/library/dn280949.aspx)。

接下來，您將在應用程式的設定自訂的錯誤訊息。 錯誤訊息，可讓使用者知道他們必須其裝置加入至工作場所之前使用者可以存取應用程式。 您可以建立自訂的應用程式拒絕存取訊息使用自訂的 HTML 和 Windows PowerShell。

在您同盟伺服器上，開啟 Windows PowerShell 命令視窗，然後輸入下列命令。 取代您的系統的特定的項目] 命令的部分︰

    Set-AdfsRelyingPartyWebContent -Name "relying party trust name" -ErrorPageAuthorizationErrorMessage
您可以存取此應用程式之前，您必須註冊您的裝置。

**如果您使用的 iOS 裝置，請選取這個連結，加入您的裝置**︰

    a href='https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/yourdomain.com

加入您工作地點的 iOS 裝置。


**如果您使用 Windows 8.1 的裝置**，您可以加入您的裝置，移至 [**電腦設定** > **網路** > **地點**。


「**依賴廠商信任名稱**」 在哪裡 AD FS 中您的應用程式依賴廠商信任物件的名稱。
**Yourdomain.com**是您已設定與 Azure Active Directory 網域名稱。 例如，contoso.com。
請務必一併移除您傳遞**設定 AdfsRelyingPartyWebContent** cmdlet 的 html 內容的任何換行 （如果有的話）。


現在當使用者存取您的應用程式從裝置無法註冊的 Azure Active Directory 裝置註冊服務時，他們會收到看起來就像螢幕擷取畫面下方的頁面。

![Screeshot 的使用者還沒有向他們的裝置註冊 Azure AD 時出現錯誤](./media/active-directory-conditional-access/error-azureDRS-device-not-registered.gif)

##<a name="related-articles"></a>相關的文章

- [Azure Active Directory 中的應用程式管理文件索引](active-directory-apps-index.md)
