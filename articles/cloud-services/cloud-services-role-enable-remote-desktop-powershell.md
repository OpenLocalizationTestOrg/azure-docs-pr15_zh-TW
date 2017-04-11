<properties
pageTitle="使用 PowerShell 的 Azure 雲端服務中的角色啟用遠端桌面連線"
description="如何設定以允許遠端桌面連線使用 PowerShell 的 azure 雲端服務應用程式"
services="cloud-services"
documentationCenter=""
authors="thraka"
manager="timlt"
editor=""/>
<tags
ms.service="cloud-services"
ms.workload="tbd"
ms.tgt_pltfrm="na"
ms.devlang="na"
ms.topic="article"
ms.date="08/05/2016"
ms.author="adegeo"/>

# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-using-powershell"></a>使用 PowerShell 的 Azure 雲端服務中的角色啟用遠端桌面連線

>[AZURE.SELECTOR]
- [Azure 傳統入口網站](cloud-services-role-enable-remote-desktop.md)
- [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
- [Visual Studio](../vs-azure-tools-remote-desktop-roles.md)


遠端桌面可讓您存取執行 Azure 中的角色的桌面。 您可以使用遠端桌面連線疑難排解，並在執行中時，診斷應用程式的問題。

本文將說明如何啟用遠端桌面使用 PowerShell 您雲端服務的角色。 本文所需的必要條件，請參閱[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md) 。 PowerShell 使用遠端桌面擴充功能，讓應用程式部署之後，您可以啟用遠端桌面。


## <a name="configure-remote-desktop-from-powershell"></a>設定 PowerShell 從遠端桌面

[設定 AzureServiceRemoteDesktopExtension](https://msdn.microsoft.com/library/azure/dn495117.aspx)指令程式可讓您指定的角色遠端桌面或雲端服務部署的各種角色。 Cmdlet 可讓您透過*認證*參數接受 PSCredential 物件的遠端桌面使用者指定的使用者名稱和密碼。

如果您使用 PowerShell 互動的方式，您可以輕鬆地設定 PSCredential 物件呼叫[取得認證](https://technet.microsoft.com/library/hh849815.aspx)指令程式。

```
$remoteusercredentials = Get-Credential
```

這個命令會顯示對話方塊，讓您針對遠端使用者輸入使用者名稱和密碼，以安全的方式。

由於 PowerShell 有助於在自動化案例中，您也可以設定**PSCredential**物件不需要使用者互動的方式。 首先，您必須設定 [安全密碼。 您開始指定純文字密碼將其轉換為使用[ConvertTo SecureString](https://technet.microsoft.com/library/hh849818.aspx)安全字串。 接下來，您需要將這個安全字串轉換成使用[ConvertFrom SecureString](https://technet.microsoft.com/library/hh849814.aspx)加密標準字串。 現在您可以將此加密的標準字串儲存[設定內容](https://technet.microsoft.com/library/ee176959.aspx)檔案。

您也可以建立安全密碼檔案，以便您沒有在密碼] 輸入每次。 此外，安全密碼檔案是更多純文字檔案。 您可以使用下列 PowerShell 來建立安全密碼檔案︰

```
ConvertTo-SecureString -String "Password123" -AsPlainText -Force | ConvertFrom-SecureString | Set-Content "password.txt"
```

>[AZURE.IMPORTANT] 當設定密碼，請確定您符合[複雜度需求](https://technet.microsoft.com/library/cc786468.aspx)。

若要建立的認證物件從安全密碼檔案，您必須閱讀檔案內容，並將它們轉換回使用[ConvertTo SecureString](https://technet.microsoft.com/library/hh849818.aspx)安全字串。

[設定 AzureServiceRemoteDesktopExtension](https://msdn.microsoft.com/library/azure/dn495117.aspx)指令程式也會接受*到期*的參數，指定的使用者帳戶到期**日期時間**。 例如，您可以設定為過期幾天，從目前的日期和時間的帳戶。

這個 PowerShell 範例會為您示範如何設定在雲端服務的遠端桌面擴充功能︰

```
$servicename = "cloudservice"
$username = "RemoteDesktopUser"
$securepassword = Get-Content -Path "password.txt" | ConvertTo-SecureString
$expiry = $(Get-Date).AddDays(1)
$credential = New-Object System.Management.Automation.PSCredential $username,$securepassword
Set-AzureServiceRemoteDesktopExtension -ServiceName $servicename -Credential $credential -Expiration $expiry
```
您也可以選擇可以指定您想要啟用遠端桌面的角色與部署位置。 如果未指定下列參數，cmdlet 可讓遠端桌面**生產**部署位置中所有的角色。

遠端桌面副檔名是在部署與相關聯。 如果您建立新的部署的服務，您都必須啟用該部署遠端桌面。 如果您隨時想要有啟用遠端桌面時，您應該考慮的 PowerShell 指令碼整合您的部署工作流程。


## <a name="remote-desktop-into-a-role-instance"></a>遠端桌面的角色執行個體
[取得 AzureRemoteDesktopFile](https://msdn.microsoft.com/library/azure/dn495261.aspx)指令程式會用於遠端桌面到雲端服務的特定角色執行個體。 您可以使用*本機路徑*參數下載至本機 RDP 檔案。 或者，您可以使用*啟動*參數直接啟動存取雲端服務角色執行個體的 [遠端桌面連線] 對話方塊。

```
Get-AzureRemoteDesktopFile -ServiceName $servicename -Name "WorkerRole1_IN_0" -Launch
```


## <a name="check-if-remote-desktop-extension-is-enabled-on-a-service"></a>檢查遠端桌面擴充功能是否已啟用服務
[取得 AzureServiceRemoteDesktopExtension](https://msdn.microsoft.com/library/azure/dn495261.aspx)指令程式會顯示的遠端桌面已啟用或停用在服務部署。 指令程式會傳回遠端桌面使用者] 和 [啟用遠端桌面擴充的角色的使用者名稱。 根據預設，此時部署位置上，讓您選擇要改用的暫存的位置。

```
Get-AzureServiceRemoteDesktopExtension -ServiceName $servicename
```

## <a name="remove-remote-desktop-extension-from-a-service"></a>遠端桌面擴充移除服務
如果您已經啟用遠端桌面副檔名為在部署中，且需要更新遠端桌面設定時，第一次移除副檔名。 並使用新的設定，再次啟用此。 例如，如果您想要設定新的遠端使用者帳戶或帳戶密碼過期。 在現有的部署啟用遠端桌面副檔名所需執行此動作。 針對新的部署，您只可以直接套用副檔名。

若要移除部署遠端桌面副檔名，您可以使用[移除 AzureServiceRemoteDesktopExtension](https://msdn.microsoft.com/library/azure/dn495280.aspx)指令程式。 您也可以選擇可以指定您要移除遠端桌面擴充的角色與部署位置。

```
Remove-AzureServiceRemoteDesktopExtension -ServiceName $servicename -UninstallConfiguration
```

>[AZURE.NOTE] 若要完全移除延伸設定，您應致電*移除*cmdlet 和**UninstallConfiguration**參數。
>
>**UninstallConfiguration**參數解除安裝任何延伸設定套用至服務。 每個延伸設定是與相關聯的服務設定。 因此不**UninstallConfiguration**解除延伸設定<mark>部署</mark>的關聯，請撥打*移除*cmdlet，有效地移除副檔名。 不過，延伸設定仍會保留與服務相關聯。



## <a name="additional-resources"></a>其他資源

[如何設定 Cloud Services](cloud-services-how-to-configure.md)
