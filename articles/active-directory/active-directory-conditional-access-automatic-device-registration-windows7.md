<properties
    pageTitle="# 設定適用於 Windows 7 加入網域裝置自動裝置註冊 |Microsoft Azure"
    description="步驟設定您的 Windows 7 網域加入自動註冊 Azure AD 的裝置。 並將裝置註冊軟體套件部署至您的 Windows 7 網域的步驟加入裝置使用軟體發佈系統等系統管理中心設定管理員。"
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
    ms.date="09/21/2016"
    ms.author="MarkVi"/>

# <a name="configure-automatic-device-registration-for-windows-7-domain-joined-devices"></a>設定適用於 Windows 7 加入網域裝置自動裝置註冊

身為 IT 管理員，您可以設定 Windows 7 加入網域裝置自動註冊 Azure AD。 若要這樣做，您必須將裝置註冊軟體套件部署到您在 Windows 7 的網域，連接裝置使用軟體發佈系統等系統管理中心設定管理員。 請務必閱讀，並完成自動裝置註冊 Azure Active Directory 的 Windows 網域的裝置中所列的先決條件。

>[AZURE.NOTE]
 如何設定自動裝置註冊的最新指示，請參閱[如何設定 Windows 網域的自動註冊加入與 Azure Active Directory 的裝置](active-directory-conditional-access-automatic-device-registration-setup.md)。

##<a name="installing-the-device-registration-software-package-on-windows-7-domain-joined-devices"></a>在 Windows 7 中安裝裝置註冊軟體套件加入裝置網域

在 Windows 7 的裝置註冊位於為[可下載 MSI 套件](https://connect.microsoft.com/site1164)。 連接到 Active Directory 網域的 Windows 7 電腦上必須安裝套件。 您應該部署套件使用軟體發佈系統等系統管理中心設定管理員。 MSI 套件支援標準無訊息的安裝選項使用 /quiet 參數。
使用[Microsoft 連線網站](https://connect.microsoft.com/site1164)下載的軟體套件。 您可以在這裡選擇並下載工作地點加入的 Windows 7。

![](./media/active-directory-conditional-access/device-registration-process-windows7.gif)

## <a name="workplace-join-with-azure-active-directory"></a>工作地點加入與 Azure Active Directory
適用於 Windows 7 加入網域裝置的裝置註冊不需要或包含使用者介面。 安裝在電腦上，登入電腦任何網域使用者會自動與火車登錄與裝置物件中 Azure AD。 Azure AD 的實體裝置的每個已註冊的使用者中會有一個裝置物件。

安裝程式之使用者的內容中執行，並在使用者登入觸發系統建立排程工作。 工作火車註冊的使用者，有 Azure AD 之後使用者符號中的裝置已完成。
排程的任務，請參閱**Microsoft**下工作排程器程式庫 > **地點加入**。
任務會執行，並登錄任何及所有的 Active Directory 使用者的登入電腦。
下圖會列出自動裝置註冊的逐步程序。

![](./media/active-directory-conditional-access/automatic-device-registration-windows7.png)

1. 使用者 （資訊工作者） 登入 Windows 7 用戶端電腦上使用 Active Directory 網域認證。
1. 執行工作地點加入排程的任務。
1. 使用者會自動驗證與 AD FS 使用 Windows 整合式驗證。
1. Windows 7 電腦上已註冊的使用者，Azure AD 中。
1. Azure AD 中建立的裝置物件和憑證。 物件代表user@device。
1. 將工作地點加入憑證儲存在電腦上。

## <a name="unregistering-your-windows-7-domain-joined-devices"></a>取消您在 Windows 7 的網域註冊加入裝置

您可以選擇註冊加入網域 Windows 7 裝置執行下列動作︰ 解除安裝地點加入軟體套件，從您的 Windows 7 網域加入使用軟體發佈系統等系統管理中心設定管理員的裝置。

然後，開啟命令提示字元在 Windows 7 電腦上，並執行下列命令以移除註冊裝置︰

    %ProgramFiles%\Microsoft Workplace Join\AutoWorkplace.exe /leave

>[AZURE.NOTE]
>到電腦，必須簽署的每個網域使用者的內容中執行此命令。
事件檢視器和錯誤的 Windows 7 中加入網域的裝置。

在 Windows 7 電腦上的 Windows 事件記錄檔會顯示與地點加入相關訊息。 您可以找到郵件的成功及失敗加入工作地點的事件。 事件記錄檔找在事件檢視器應用程式與服務記錄檔] 下的 > Microsoft 工作場所加入。

## <a name="additional-topics"></a>其他主題

- [Azure Active Directory 裝置註冊概觀](active-directory-conditional-access-device-registration-overview.md)
- [使用 Azure Active Directory for Windows Domain-Joined 裝置自動裝置註冊](active-directory-conditional-access-automatic-device-registration.md)
- [設定適用於 Windows 8.1 加入網域裝置自動裝置註冊](active-directory-conditional-access-automatic-device-registration-windows-8-1.md)
- [自動裝置註冊 Azure Active Directory Windows 10 版網域的裝置](active-directory-azureadjoin-devices-group-policy.md)
