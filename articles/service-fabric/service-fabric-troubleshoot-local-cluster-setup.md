<properties
   pageTitle="您的本機服務布料的轉印圖樣叢集安裝疑難排解 |Microsoft Azure"
   description="本文涵蓋建議的疑難排解本機開發叢集一組"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/08/2016"
   ms.author="seanmck"/>

# <a name="troubleshoot-your-local-development-cluster-setup"></a>您的本機開發叢集安裝疑難排解

如果與本機 Azure 服務布料的轉印圖樣開發叢集互動時遇到問題，請檢閱下列可能解決方案的建議。

## <a name="cluster-setup-failures"></a>叢集設定失敗

### <a name="cannot-clean-up-service-fabric-logs"></a>無法清理服務布料的轉印圖樣記錄

#### <a name="problem"></a>問題

在執行 DevClusterSetup 指令碼時，您會看到如下錯誤︰

    Cannot clean up C:\SfDevCluster\Log fully as references are likely being held to items in it. Please remove those and run this script again.
    At line:1 char:1 + .\DevClusterSetup.ps1
    + ~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : NotSpecified: (:) [Write-Error], WriteErrorException
    + FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException,DevClusterSetup.ps1


#### <a name="solution"></a>解決方案

關閉目前的 PowerShell 視窗，並開啟新的 PowerShell 視窗以系統管理員。 您現在應該可以順利執行指令碼。

## <a name="cluster-connection-failures"></a>叢集連線失敗

### <a name="service-fabric-powershell-cmdlets-are-not-recognized-in-azure-powershell"></a>在 PowerShell 的 Azure 中無法辨識服務布料的轉印圖樣 PowerShell cmdlet

#### <a name="problem"></a>問題

如果您嘗試執行任何服務布料的轉印圖樣 PowerShell cmdlet，例如`Connect-ServiceFabricCluster`PowerShell 的 Azure 在視窗中，會失敗，指出無法辨識的 cmdlet。 原因是 PowerShell 的 Azure 使用 32 位元版本的 Windows PowerShell （即使是在 64 位元作業系統的版本），而在 64 位元環境中僅適用於服務布料的轉印圖樣 cmdlet。

#### <a name="solution"></a>解決方案

一律直接從 Windows PowerShell 中執行服務布料的轉印圖樣 cmdlet。

>[AZURE.NOTE] 最新版的 PowerShell 的 Azure 並不會建立一個特殊的捷徑，讓這不會再出現。

### <a name="type-initialization-exception"></a>輸入初始化例外狀況

#### <a name="problem"></a>問題

您連線到 PowerShell 中叢集，當您看到錯誤 TypeInitializationException System.Fabric.Common.AppTrace。

#### <a name="solution"></a>解決方案

在安裝期間未正確設定您的 path 變數。 請登入 Windows，並再次登入。 這會完全重新整理您的路徑。

### <a name="cluster-connection-fails-with-object-is-closed"></a>使用 「 關閉物件 」 的叢集連線時失敗

#### <a name="problem"></a>問題

連線 ServiceFabricCluster 呼叫會發生錯誤，就像這樣︰

    Connect-ServiceFabricCluster : The object is closed.
    At line:1 char:1
    + Connect-ServiceFabricCluster
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : InvalidOperation: (:) [Connect-ServiceFabricCluster], FabricObjectClosedException
    + FullyQualifiedErrorId : CreateClusterConnectionErrorId,Microsoft.ServiceFabric.Powershell.ConnectCluster

#### <a name="solution"></a>解決方案

關閉目前的 PowerShell 視窗，並開啟新的 PowerShell 視窗以系統管理員。 您現在應該可以成功連線。

### <a name="fabric-connection-denied-exception"></a>布料的轉印圖樣拒絕連線的例外狀況

#### <a name="problem"></a>問題

從 Visual Studio 偵錯時，您收到 FabricConnectionDeniedException 錯誤。

#### <a name="solution"></a>解決方案

當您嘗試要嘗試啟動的服務主機處理程序以手動方式，而不允許您啟動的服務布料的轉印圖樣執行階段時，通常會發生此錯誤。

請確定您沒有設定為您的方案中的啟動專案任何服務專案。 服務布料的轉印圖樣應用程式專案應設定為啟動專案。

>[AZURE.TIP] 如果您在追蹤設定，您的本機叢集開始異常行為，您可以重設使用本機叢集管理員系統匣應用程式。 將移除現有的叢集，並將新的設定。 請注意，所有部署應用程式相關的資料將會移除。

## <a name="next-steps"></a>後續步驟

- [瞭解及疑難排解叢集與系統的狀況報告](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
- [以視覺化方式呈現叢集服務布料的轉印圖樣檔案總管](service-fabric-visualizing-your-cluster.md)
