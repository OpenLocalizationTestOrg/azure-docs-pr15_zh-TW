<properties
    pageTitle="設定適用於 Windows 8.1 加入網域裝置自動裝置註冊 |Microsoft Azure"
    description=" 設定適用於 Windows 8.1 網域裝置自動註冊 Azure AD 的群組原則的步驟。 "
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
    ms.author="Markvi"/>

# <a name="configure-automatic-device-registration-for-windows-81-domain-joined-devices"></a>設定適用於 Windows 8.1 加入網域裝置自動裝置註冊

您可以使用 Active Directory 群組原則設定自動註冊 Azure AD 您 Windows 8.1 加入網域裝置。 若要設定群組原則，您必須至少有一個網域連接 Windows Server 2012 R2 或 Windows 8.1 電腦上安裝的群組原則管理功能。 一旦您的網域啟用此群組原則，登入電腦任何網域使用者會自動及自動登錄與裝置物件中 Azure AD。 Azure AD 的實體裝置的每個已註冊的使用者中會有一個裝置物件。請務必閱讀，並完成來自自動裝置註冊使用 Azure Active Directory for Windows Domain-Joined 裝置的必要條件。

>[AZURE.NOTE]
 如何設定自動裝置註冊的最新指示，請參閱[如何設定 Windows 網域的自動註冊加入與 Azure Active Directory 的裝置](active-directory-conditional-access-automatic-device-registration-setup.md)。



## <a name="configure-the-group-policy-for-your-windows-81-domain-joined-devices"></a>設定您的 Windows 8.1 加入網域裝置的群組原則

1. 開啟 [伺服器管理員]，然後瀏覽至 [**工具** > **群組原則管理**。
2. 從群組原則管理] 中，瀏覽至對應到您想要啟用**自動加入的工作地點**的網域的 [網域] 節點。
3. 以滑鼠右鍵按一下**群組原則物件**，然後選取 [**新增**]。 提供給您群組原則物件的名稱，例如，[**自動加入的地點**。 按一下**[確定]**。
4. 以滑鼠右鍵按一下新的群組原則物件，然後選取 [**編輯**]。
5. 瀏覽至 [**電腦設定** > **原則** > **系統管理範本** > **Windows 元件** > **地點加入**。
6. 以滑鼠右鍵按一下自動加入工作地點的用戶端電腦，然後選取 [**編輯**]。
7. 選取 [啟用] 選項按鈕，然後按一下 [套用]。 按一下**[確定]**。
8. 您現在可能會將群組原則物件連結到您所選擇的位置。 若要讓所有加入網域 Windows 8.1 裝置在您組織的此原則，連結到網域的群組原則。

## <a name="unregistering-your-windows-81-domain-joined-devices"></a>取消您的 Windows 8.1 網域註冊加入裝置

您可以選擇註冊加入網域 Windows 8.1 裝置執行下列動作︰ 修改前一節中所建立的工作地點加入的群組原則設定。 設定自動地點加入用戶端電腦原則已停用。 這會防止新裝置會自動加入工作地點。

移除現有加入網域 Windows 8.1 電腦註冊以下列其中一個下列兩個選項︰

* 選項 1︰**取消註冊 Windows 8.1 網域加入使用電腦上設定的裝置**
  1. 在 Windows 8.1 裝置上，瀏覽至 [**電腦設定** > **網路** > **地點**
  2. 選取 [**離開**]。
必須為每個網域使用者已登入電腦，並已自動加入工作地點的重複此程序。

* 選項 2︰ 移除註冊使用指令碼的 Windows 8.1 的網域連接的裝置
    1. 開啟 Windows 8.1 電腦上的命令提示字元，然後執行下列命令︰` %SystemRoot%\System32\AutoWorkplace.exe leave`
   
到電腦，必須簽署的每個網域使用者的內容中執行此命令。

##<a name="event-viewer--errors-for-windows-81-domain-joined-devices"></a>加入裝置事件檢視器與錯誤的 Windows 8.1 的網域。

在 Windows 8.1 的電腦上的 Windows 事件記錄檔會顯示與裝置註冊相關訊息。 您可以找到郵件成功及失敗的事件。 

事件記錄檔找在事件檢視器應用程式與服務**記錄檔**] 下的 > **Microsoft** > **Windows > 地點加入**。

##<a name="additional-details"></a>其他詳細資料

群組原則可讓使用者的內容中執行，在使用者登入觸發系統上排程任務。 任務會自動使用者和裝置以註冊 Azure AD 的登入完成後。 排定任務位於工作排程器程式庫，在**Microsoft**Windows 8.1 裝置 > **Windows** > **地點加入**。 任務會執行，並登錄任何及所有的 Active Directory 使用者的登入。 

## <a name="additional-topics"></a>其他主題
- [Azure Active Directory 裝置註冊概觀](active-directory-conditional-access-device-registration-overview.md)
- [自動裝置註冊 Azure Active Directory Windows 10 版網域的裝置](active-directory-conditional-access-automatic-device-registration.md)
- [設定適用於 Windows 7 加入網域裝置自動裝置註冊](active-directory-conditional-access-automatic-device-registration-windows7.md)

