<properties
    pageTitle="Azure Active Directory 存取方面的問題的疑難排解 |Microsoft Azure"
    description="瞭解您可以採取以解決與您組織的線上資源的存取問題的步驟。"
    services="active-directory"
    keywords="裝置型條件存取，裝置註冊啟用裝置註冊裝置註冊與 MDM"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/23/2016"
    ms.author="markvi"/>


# <a name="troubleshooting-for-azure-active-directory-access-issues"></a>Azure Active Directory 存取方面的問題的疑難排解

您嘗試存取組織的 SharePoint Online 內部網路，您收到 「 拒絕存取 」 錯誤訊息。 您做什麼？

本文涵蓋補救步驟可協助您解決與您組織的線上資源的存取問題。

來協助解決 Azure Active Directory (Azure AD) 存取問題，請移至 [] 區段，包括您的裝置平台，請參閱︰

-   在 Windows 裝置
-   在 iOS 裝置 (核取推出尋求 Iphone 與 ipad 上的返回。)
-   在 android 裝置 (回來推出尋求 Android 電話和平板。)

## <a name="access-from-a-windows-device"></a>從 Windows 裝置存取

如果您的裝置執行其中一項動作的平台，尋找下一節，當您嘗試存取應用程式或服務會顯示錯誤訊息︰

- 在 Windows 10
- Windows 8.1
- Windows 8
- Windows 7
- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2

### <a name="device-is-not-registered"></a>未註冊裝置

如果您的裝置未註冊 Azure AD 與裝置原則受保護的應用程式，您可能會看到頁面會顯示下列錯誤訊息︰

![未註冊裝置的 「 您無法取得那里從這裡開始 」 訊息](./media/active-directory-conditional-access-device-remediation/01.png "案例")

如果網域到 Active Directory 中組織您的裝置，請嘗試︰

1.  請確認您登入 windows 使用您的公司帳戶 （您的 Active Directory 帳戶）。
2.  連線至公司網路透過虛擬私人網路 (VPN) 或 DirectAccess。
3.  您連線之後，請按下 Windows 標誌鍵 + L 鍵，鎖定您的 Windows 工作階段。
4.  輸入您的公司帳戶認證，若要解除鎖定您的 Windows 工作階段。
5.  等候一分鐘，然後再試一次存取應用程式或服務。
6.  如果您看到同一個頁面，按一下 [**更多詳細資料**] 連結，然後請連絡您的系統管理員的詳細資料。

如果您的裝置未設定網域，並執行 Windows 10，您會有兩個選項︰

- 執行 Azure AD 加入
- 新增公司或學校帳戶至 Windows

如需這些選項有何不同的資訊，請參閱[使用 Windows 10 裝置，在您工作地點](active-directory-azureadjoin-windows10-devices.md)。

若要執行 Azure AD 加入，請執行下列步驟的平台裝置上執行。 （azure AD 加入並不提供 Windows 手機上）。

**Windows 10 的週年紀念日更新**

1.  開啟 [**設定**] 應用程式]。
2.  按一下 [**帳戶**] > **存取工作或學校**。
3.  按一下 [**連線**]。
4.  按一下 [**加入至 Azure AD 此裝置**]。
5.  驗證您的組織，請提供多重因素驗證，如果出現提示，然後遵循的步驟，會顯示。
6.  登出，，然後使用您的公司帳戶登入。
7.  再次嘗試存取應用程式。


**Windows 10 的年 11 月 2015年更新**

1.  開啟 [**設定**] 應用程式]。
2.  按一下 [**系統** > **有關**。
3.  按一下 [**加入 Azure AD**]。
4.  驗證您的組織，請提供多重因素驗證，如果出現提示，然後遵循的步驟，會顯示。
5.  登出，，然後使用您的公司帳戶 （您 Azure AD 帳戶） 登入。
6.  再次嘗試存取應用程式。

若要新增公司或學校帳戶，請執行下列步驟︰

**Windows 10 週年紀念日更新**

1.  開啟 [**設定**] 應用程式]。
2.  按一下 [**帳戶**] > **存取工作或學校**。
3.  按一下 [**連線**]。
4.  驗證您的組織，請提供多重因素驗證，如果出現提示，然後遵循的步驟，會顯示。
5.  再次嘗試存取應用程式。


**Windows 10 的年 11 月 2015年更新**

1.  開啟 [**設定**] 應用程式]。
2.  按一下 [**帳戶**] > **您的帳戶**。
3.  按一下 [**新增工作或學校帳戶**]。
4.  驗證您的組織，請提供多重因素驗證，如果出現提示，然後遵循的步驟，會顯示。
5.  再次嘗試存取應用程式。

如果您的裝置未設定網域，並執行 Windows 8.1 中，若要執行工作地點加入，在 Microsoft Intune 註冊，請執行下列步驟︰

1.  開啟 [**電腦設定**]。
2.  按一下 [**網路** > **地點**。
3.  按一下 [**加入**]。
4.  驗證您的組織，請提供多重因素驗證，如果出現提示，然後遵循的步驟，會顯示。
5.  按一下 [**開啟**]。
6.  再次嘗試存取應用程式。


### <a name="browser-is-not-supported"></a>不支援的瀏覽器

您可能會被拒絕存取您嘗試使用以下瀏覽器存取應用程式或服務︰

- Chrome、 Firefox 或 Microsoft Edge 或在 Windows 10 或 Windows Server 2016 中的 Microsoft Internet Explorer 以外的任何其他瀏覽器
- 在 Windows 8.1、 Windows 7、 Windows Server 2012 R2、 Windows Server 2012 或 Windows Server 2008 R2 的 Firefox

您會看到錯誤頁面看起來像這樣︰

![不支援的瀏覽器的 「 您無法取得那里從這裡開始 」 訊息](./media/active-directory-conditional-access-device-remediation/02.png "案例")

僅限補救是使用您的裝置平台版的應用程式支援的瀏覽器。

## <a name="next-steps"></a>後續步驟

[Azure Active Directory 條件的存取](active-directory-conditional-access.md)
