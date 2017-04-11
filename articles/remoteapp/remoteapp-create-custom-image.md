<properties
    pageTitle="如何建立自訂的範本圖像的 Azure RemoteApp |Microsoft Azure"
    description="瞭解如何建立自訂的範本圖像的 Azure RemoteApp。 您可以使用此範本與混合式部署或雲端的集合。"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016" 
    ms.author="elizapo"/>

# <a name="how-to-create-a-custom-template-image-for-azure-remoteapp"></a>如何建立自訂的範本圖像的 Azure RemoteApp

> [AZURE.IMPORTANT]
> Azure RemoteApp 會被停用。 閱讀[公告](https://go.microsoft.com/fwlink/?linkid=821148)如需詳細資訊。

Azure RemoteApp 使用 Windows Server 2012 R2 範本圖像裝載所有您想要與您的使用者共用的程式。 若要建立自訂的 RemoteApp 範本圖像，您可以開始現有的圖像，或建立新的範本。 


> [AZURE.TIP] 您知道您可以從 Azure VM 來建立圖像嗎？ 則為 true 的本文，而且剪下向下的時間量，匯入圖像的時間。 請參閱步驟[以下](remoteapp-image-on-azurevm.md)。

用於 Azure RemoteApp 可以上傳圖像的需求如下︰


- 圖像大小應該 Mb 的倍數。 如果您嘗試要上傳圖像的不是倍數，就無法上傳。
- 圖像大小必須 127 GB 或更小。
- 它必須 VHD 檔案 （VHDX 檔案 [HYPER-V 虛擬硬碟] 目前不支援）。
- VHD 不能產生 2 虛擬機器。
- VHD 可以是固定大小或動態擴充。 動態擴充 VHD 建議，因為超過固定大小 VHD 檔案上傳至 Azure 時間。
- 必須使用母片開機記錄 (MBR) 分割樣式初始化磁碟。 不支援 GUID 分割表格 (GPT) 磁碟分割樣式。
- VHD 必須包含單一的 Windows Server 2012 R2 安裝。 它可以包含多個區，但只有一個包含的 Windows 安裝。
- 必須安裝遠端桌面工作階段 host （主機) (RDSH) 角色和 「 桌面體驗 」 功能。
- 遠端桌面連線代理人角色必須*不*會安裝。
- 加密檔案系統 (EFS) 必須先停用。
- 圖像必須使用參數 SYSPREPed **/oobe / 一般化 /shutdown** （請勿使用**/mode:vm**參數）。
- 上傳您從快照鏈結 VHD 不受支援。


**開始之前**

您需要執行下列動作，才能建立服務︰

- [登入](https://azure.microsoft.com/services/remoteapp/)RemoteApp。
- 用來作為 RemoteApp 服務帳戶的 Active Directory 中建立使用者帳戶。 限制此帳戶的權限，使其只可以加入機器網域。 如需詳細資訊，請參閱[設定 Azure Active Directory RemoteApp 版](remoteapp-ad.md)。
- 收集您內部網路的相關資訊︰ IP 位址資訊和 VPN 裝置詳細資料。
- 安裝[Azure PowerShell](../powershell-install-configure.md)模組。
- 收集您想要授與存取權的使用者的相關資訊。 這可以是 Microsoft 帳戶資訊或使用者的 Active Directory 工作帳戶資訊。



## <a name="create-a-template-image"></a>建立的範本圖像 ##

若要從頭開始建立新的範本圖像的高層級的步驟如下︰

1.  找出的 Windows Server 2012 R2 更新 DVD 或 ISO 圖像。
2.  建立 VHD 檔案。
4.  安裝 Windows Server 2012 R2。
5.  安裝遠端桌面工作階段 host （主機) (RDSH) 角色及 「 桌面體驗 」 功能。
6.  安裝應用程式所需的其他功能。
7.  安裝並設定您的應用程式。 若要簡化共用應用程式，新增任何應用程式或您想要共用的圖像，特別是在 **%systemdrive%\ProgramData\Microsoft\Windows\Start Menu\Programs 中的 [**開始**] 功能表的程式。
8.  執行任何其他應用程式所需的 Windows 設定。
9.  停用加密檔案系統 (EFS)。
10. **REQUIRED:**移至 [Windows Update，並安裝所有的重要更新。
9.  SYSPREP 圖像。

建立新的圖像的詳細的步驟如下︰

1.  找出的 Windows Server 2012 R2 更新 DVD 或 ISO 圖像。
2.  使用磁碟管理建立 VHD 檔案。
    1.  啟動磁碟管理 (diskmgmt.msc)。
    2.  建立動態擴充 VHD 40 GB 或更多的大小。 （估計所需的 Windows，您的應用程式] 及自訂的間距的量。 Windows Server RDSH 角色與安裝桌面體驗 」 功能會要求的空間 10 GB）。
        1.  按一下 [**動作 > 建立 VHD**。
        2.  指定位置、 大小和 VHD 格式。 選取 [**動態展開**，然後再按一下**[確定**]。

            這樣會執行幾秒鐘。 VHD 建立完成後，您應該會看到新的磁碟不需要任何代號及在 「 未初始化 「 磁碟管理主控台的狀態。

        - 磁碟 （不未配置空格），以滑鼠右鍵按一下，然後按一下 [**初始化磁碟**。 選取**MBR** （主開機記錄） 的磁碟分割樣式，為，然後按一下**[確定]**。
        - 建立新的音量︰ 未配置的空格中，以滑鼠右鍵按一下，然後按一下 [**新增簡單的音量**。 您可以接受預設值在精靈中，但請確定您指派磁碟機代號為了避免當您上傳的範本圖像時發生潛在問題。
        - 磁碟空間，以滑鼠右鍵按一下，然後按一下 [**中斷連結 VHD**。





1. 安裝 Windows Server 2012 R2:
    1. 建立新的虛擬機器。 使用新的虛擬機器精靈中 HYPER-V 管理員] 或 [用戶端 HYPER-V。
        1. 在 [指定產生器] 頁面上選擇 [**產生 1**]。
        2. 在連線虛擬硬碟頁面上，選取 [**使用現有的虛擬硬碟**]，瀏覽至您在上一個步驟中建立的 VHD。
        2. 在 [安裝選項] 頁面上選取**安裝作業系統從 CD/DVD_ROM 開機**，，然後選取您的 Windows Server 2012 R2 安裝媒體的位置。
        3. 選擇所需安裝 Windows 和您的應用程式精靈中的其他選項。 完成精靈。
    2.  精靈完成後，請編輯的虛擬機器設定進行任何其他變更所需安裝 Windows 和您的程式，例如虛擬的處理器數目，並再按一下**[確定]**。
    4.  連線至虛擬機器，並安裝 Windows Server 2012 R2。
1. 安裝遠端桌面工作階段 host （主機) (RDSH) 角色及 「 桌面體驗 」 功能︰
    1. 啟動伺服器管理員]。
    2. 在 [歡迎] 畫面上，或從 [**管理**] 功能表，請按一下 [**新增角色和功能**]。
    3. 在您開始前] 頁面上，按一下 [**下一步**]。
    4. 選取**角色或功能的安裝**]，然後按一下 [**下一步**。
    5. 從清單中，選取 [本機電腦，然後按一下 [**下一步**。
    6. 選取 [**遠端桌面服務**]，然後按一下 [**下一步**。
    7. 展開**的使用者介面和基礎結構**，並選取 [**桌面體驗**。
    8. 按一下 [**新增功能**]，然後按一下 [**下一步**。
    9. 在 [遠端桌面服務] 頁面上，按一下 [**下一步**]。
    10. 按一下 [**遠端桌面工作階段主機**]。
    11. 按一下 [**新增功能**]，然後按一下 [**下一步**。
    12. 在確認安裝選項] 頁面上，選取 [**自動如有必要，請重新啟動目的伺服器**，然後再按一下**[是]**重新啟動警告。
    13. 按一下 [**安裝**]。 將重新啟動電腦。
1.  安裝應用程式，例如.NET Framework 3.5 所需的其他功能。 若要安裝的功能，執行 [新增角色及功能精靈。
7.  安裝和設定的程式與您想要透過 RemoteApp 發佈的應用程式。

>[AZURE.IMPORTANT]
>
>安裝 RDSH 角色之前安裝應用程式，以確保圖像上傳到 RemoteApp 之前，會發現任何應用程式的相容性問題。
>
>請確定您的應用程式 （**.lnk**檔案） 的捷徑出現在所有使用者 (%systemdrive%\ProgramData\Microsoft\Windows\Start Menu\Programs) 的 [**開始**] 功能表中。 也請確定您在**[開始**] 功能表中看到的圖示是您希望使用者看見。 如果沒有的話，將其變更。 (沒有*已*新增至 [開始] 應用程式] 功能表中，但可讓您更容易地在 RemoteApp 發佈應用程式。 否則，您需要提供應用程式的安裝路徑，當您發佈應用程式。）


8.  執行任何其他應用程式所需的 Windows 設定。
9.  停用加密檔案系統 (EFS)。 在提高權限的命令] 視窗中，執行下列命令︰

        Fsutil behavior set disableencryption 1

    或者，您可以設定，或在登錄中新增下列 DWORD 值︰

        HKLM\System\CurrentControlSet\Control\FileSystem\NtfsDisableEncryption = 1
9.  如果您要建立 Azure 虛擬機器內圖像，重新命名**\%windir%\Panther\Unattend.xml**檔案，這會封鎖從工作稍後使用上傳指令碼。 變更此檔案的名稱來 Unattend.old，使您仍然可以檔案，您需要還原您的部署。
10. 移至 [Windows Update，並安裝所有的重要更新。 您可能需要執行 Windows Update 多次以取得所有的更新。 （有時候您安裝更新，及該更新本身需要的更新）。
10. SYSPREP 圖像。 提高權限的命令提示字元中，執行下列命令︰

    **C:\Windows\System32\sysprep\sysprep.exe / 一般化 /oobe /shutdown**

    **附註︰**雖然這是虛擬機器，不要使用**/mode:vm**上手的 SYSPREP 命令。


## <a name="next-steps"></a>後續步驟 ##
現在您擁有自訂的範本圖像時，您需要上傳至 RemoteApp 集合的圖像。 若要建立您的集合中使用下列文章中的資訊︰


- [如何建立混合式集合 RemoteApp](remoteapp-create-hybrid-deployment.md)
- [如何建立 RemoteApp 雲端集合](remoteapp-create-cloud-deployment.md)
 