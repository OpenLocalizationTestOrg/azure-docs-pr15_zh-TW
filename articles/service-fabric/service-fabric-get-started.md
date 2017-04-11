<properties
   pageTitle="設定您的開發環境 |Microsoft Azure"
   description="安裝執行階段、 sdk，您可以及工具，並建立本機開發叢集。 完成此安裝之後，您就可以開始建立應用程式項目。"
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/26/2016"
   ms.author="ryanwi"/>

# <a name="prepare-your-development-environment"></a>開發環境準備作業

> [AZURE.SELECTOR]
-[Windows](service-fabric-get-started.md)
- [Linux](service-fabric-get-started-linux.md)
- [OSX](service-fabric-get-started-mac.md)

 若要建立並執行[Azure 服務布料的轉印圖樣應用程式][1]在您的部署電腦上安裝執行階段、 sdk，您可以及工具。 您也需要啟用 SDK 中包含的 Windows PowerShell 指令碼的執行。

## <a name="prerequisites"></a>必要條件
### <a name="supported-operating-system-versions"></a>支援的作業系統版本
以下作業系統版本支援的開發︰

- Windows 7
- Windows 8/Windows 8.1
- Windows Server 2012 R2
- 在 Windows 10

>[AZURE.NOTE] Windows 7 中只會包含預設的 Windows PowerShell 2.0。 服務布料的轉印圖樣 PowerShell cmdlet 需要 PowerShell 3.0 或更新版本。 您可以[下載 Windows PowerShell 5.0] [powershell5-download]從 Microsoft 下載中心。

## <a name="install-the-runtime-sdk-and-tools"></a>安裝執行階段、 sdk，您可以和工具

Web 平台安裝程式提供服務布料的轉印圖樣開發的兩個的設定︰

- [（需要 Visual Studio 2015 更新 2 或更新版本） 的 Visual Studio 2015 安裝的服務布料的轉印圖樣執行階段、 sdk，您可以與工具][full-bundle-vs2015]
- [安裝的服務布料的轉印圖樣執行階段及只 SDK （沒有 Visual Studio 工具）][core-sdk]

## <a name="enable-powershell-script-execution"></a>啟用 PowerShell 指令碼執行

服務布料的轉印圖樣會使用 Windows PowerShell 指令碼，建立本機開發叢集，及部署從 Visual Studio 應用程式。 根據預設，Windows 會封鎖執行這些指令碼。 若要啟用這些，您必須修改您的 PowerShell 執行原則。 開啟以系統管理員的 PowerShell，並輸入以下命令︰

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```

## <a name="next-steps"></a>後續步驟
現在您已完成設定您的開發環境，開始建立和執行應用程式。

- [在 Visual Studio 建立您的第一個服務布料的轉印圖樣應用程式](service-fabric-create-your-first-application-in-visual-studio.md)
- [瞭解如何部署及管理您的本機叢集上的應用程式](service-fabric-get-started-with-a-local-cluster.md)
- [深入瞭解程式設計的模型︰ 可靠的服務與可靠的動作項目](service-fabric-choose-framework.md)
- [請參閱在 GitHub 服務布料的轉印圖樣程式碼範例](https://aka.ms/servicefabricsamples)
- [使用服務布料的轉印圖樣總管視覺化叢集](service-fabric-visualizing-your-cluster.md)
- [遵循服務布料的轉印圖樣學習路徑，以取得平台的主要簡介](https://azure.microsoft.com/documentation/learning-paths/service-fabric/)

[1]: http://azure.microsoft.com/en-us/campaigns/service-fabric/ "服務布料的轉印圖樣行銷活動頁面"
[2]: http://go.microsoft.com/fwlink/?LinkId=517106 "與 RC 存在"
[full-bundle-vs2015]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015 "與 2015 WebPI 連結"
[full-bundle-dev15]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-Dev15 "Dev15 WebPI 連結"
[core-sdk]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK "核心 SDK WebPI 連結"
[powershell5-download]:https://www.microsoft.com/en-us/download/details.aspx?id=50395
