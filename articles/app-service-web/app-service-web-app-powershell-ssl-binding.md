<properties
    pageTitle="使用 PowerShell 的 SSL 憑證繫結"
    description="瞭解如何將 SSL 憑證繫結至您的 web 應用程式，使用 PowerShell。"
    services="app-service\web"
    documentationCenter=""
    authors="ahmedelnably"
    manager="stefsch"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="01/13/2016"
    ms.author="ahmedelnably"/>

# <a name="azure-app-service-ssl-certificate-binding-using-powershell"></a>Azure 應用程式服務的 SSL 憑證繫結使用 PowerShell #

版的 Microsoft Azure PowerShell 版本 1.1.0 新指令程式已新增的想讓使用者能夠將現有或新的 SSL 憑證繫結到現有的 Web 應用程式。

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

若要深入瞭解使用 Azure 資源管理員基礎 Azure PowerShell cmdlet 來管理您的 Web 應用程式會檢查[Azure 資源管理員基礎 Azure Web 應用程式的 PowerShell 命令](app-service-web-app-azure-resource-manager-powershell.md)

## <a name="uploading-and-binding-a-new-ssl-certificate"></a>上傳並繫結新的 SSL 憑證 ##

案例︰ 使用者想要繫結至其中一個他的 web 應用程式的 SSL 憑證。

了解包含 web 應用程式、 web 應用程式的名稱、 在使用者電腦上的憑證，以及自訂主機名稱密碼的憑證.pfx 檔案路徑的資源群組名稱，我們可以使用下列 PowerShell 命令建立該 SSL 繫結︰

    New-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -CertificateFilePath PathToPfxFile -CertificatePassword PlainTextPwd -Name www.contoso.com

請注意，再新增 SSL 繫結至 web 應用程式，您必須已經設定主機名稱 （自訂的網域）。 如果未設定主機名稱，然後您會看到的錯誤時執行新增 AzureRmWebAppSSLBinding 不存在 」 主機名稱]。 您可以直接從入口網站，或使用 PowerShell 的 Azure 中新增主機名稱。 若要執行新增 AzureRmWebAppSSLBinding 之前，先設定主機名稱可以下列 PowerShell 程式碼片段。   
  
    $webApp = Get-AzureRmWebApp -Name mytestapp -ResourceGroupName myresourcegroup  
    $hostNames = $webApp.HostNames  
    $HostNames.Add("www.contoso.com")  
    Set-AzureRmWebApp -Name mytestapp -ResourceGroupName myresourcegroup -HostNames $HostNames   
  
請務必瞭解設定 AzureRmWebApp 指令程式會覆寫 hostname web 應用程式。 因此上述 PowerShell 程式碼片段附加至現有的主機名稱清單的 web 應用程式。  

## <a name="uploading-and-binding-an-existing-ssl-certificate"></a>上傳並繫結現有的 SSL 憑證 ##

案例︰ 使用者想要繫結至其中一個他的 web 應用程式的先前上傳的 SSL 憑證。

我們可以取得憑證已上傳到特定的資源群組使用下列命令清單

    Get-AzureRmWebAppCertificate -ResourceGroupName myresourcegroup

請注意，憑證是本機的特定位置和資源群組，需要重新上傳憑證，如果已設定的 web 應用程式中的其他位置，而資源群組的其他使用者所需的憑證 

了解所包含的 web 應用程式、 web 應用程式的名稱、 憑證指紋，以及自訂主機名稱的資源群組名稱，我們可以使用下列 PowerShell 命令建立該 SSL 繫結︰

    New-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -Thumbprint <certificate thumbprint> -Name www.contoso.com

## <a name="deleting-an-existing-ssl-binding"></a>刪除現有的 SSL 繫結  ##

使用者想要刪除現有的 SSL 繫結案例:。

了解所包含的 web 應用程式、 web 應用程式的名稱，以及自訂主機名稱的資源群組名稱，我們可以使用下列 PowerShell 命令移除該 SSL 繫結︰

    Remove-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -Name www.contoso.com

請注意，如果移除的 SSL 繫結所使用的憑證的位置，預設憑證會刪除，如果使用者想要保留他可以使用 [DeleteCertificate 選項來保留憑證的憑證的最後一個繫結

    Remove-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -Name www.contoso.com -DeleteCertificate $false

### <a name="references"></a>參照 ###
- [Azure 資源管理員基礎 Azure Web 應用程式的 PowerShell 命令](app-service-web-app-azure-resource-manager-powershell.md)
- [應用程式服務環境簡介](app-service-app-service-environment-intro.md)
- [使用 Azure PowerShell 的 Azure 資源管理員](../powershell-azure-resource-manager.md)
