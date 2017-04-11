<properties
   pageTitle="使用 PowerShell 建立雲端服務容器 |Microsoft Azure"
   description="本文說明如何使用 PowerShell 建立雲端服務容器。 容器裝載網頁和背景工作的角色。"
   services="cloud-services"
   documentationCenter=".net"
   authors="cawaMS"
   manager="timlt"
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="powershell"
   ms.workload="na"
   ms.date="07/29/2016"
   ms.author="cawa"/>

# <a name="use-an-azure-powershell-command-to-create-an-empty-cloud-service-container"></a>使用 PowerShell 的 Azure 命令來建立空白的雲端服務容器
本文說明如何快速建立使用 PowerShell 的 Azure cmdlet 雲端服務容器。 請遵循下列步驟︰

1. 從 [ [PowerShell 的 Azure 下載](http://aka.ms/webpi-azps)頁面安裝 Microsoft Azure PowerShell 指令程式。
2. 開啟 [PowerShell 命令提示字元]。
3. 使用[新增 AzureAccount](https://msdn.microsoft.com/library/dn495128.aspx)登入。

    > [AZURE.NOTE] 在安裝 Azure PowerShell cmdlet 和連線至您訂閱的 Azure 進一步的指示，請參閱[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md)。

4. 您可以使用 [**新增 AzureService** cmdlet 來建立空白的 Azure 雲端服務容器。

    ```
    New-AzureService [-ServiceName] <String> [-AffinityGroup] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
    New-AzureService [-ServiceName] <String> [-Location] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
```

5. 請遵循此範例中叫用 cmdlet:
```
New-AzureService -ServiceName "mytestcloudservice" -Location "Central US" -Label "mytestcloudservice"
```

如需有關如何建立 Azure 雲端服務的詳細資訊，請執行︰
```
Get-help New-AzureService
```

### <a name="next-steps"></a>後續步驟

 * 若要管理雲端服務部署，請參閱[取得 AzureService](https://msdn.microsoft.com/library/azure/dn495131.aspx)、[移除 AzureService](https://msdn.microsoft.com/library/azure/dn495120.aspx)，以及[設定 AzureService](https://msdn.microsoft.com/library/azure/dn495242.aspx)命令。 如需進一步瞭解，您可能也參照至[如何設定雲端服務](cloud-services-how-to-configure.md)。

 * 若要將您的雲端服務專案發佈至 Azure 中，請參閱[Azure 中的雲端服務的連續傳遞](cloud-services-dotnet-continuous-delivery.md) **PublishCloudService.ps1**程式碼範例。
