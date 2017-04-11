<properties
   pageTitle="在服務布料的轉印圖樣叢集產生混亂 |Microsoft Azure"
   description="使用錯誤插入和叢集分析服務 Api 叢集管理混亂。"
   services="service-fabric"
   documentationCenter=".net"
   authors="motanv"
   manager="rsinha"
   editor="toddabel"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/19/2016"
   ms.author="motanv"/>

# <a name="induce-controlled-chaos-in-service-fabric-clusters"></a>產生服務布料的轉印圖樣叢集控制的混亂
大型分散式的系統雲端基礎架構原本就不可靠等。 Azure 服務布料的轉印圖樣讓開發人員撰寫可靠的服務不可靠的基礎結構的上方。 若要撰寫強大的服務，開發人員需要能夠便會針對例如不可靠的基礎結構，以測試其服務的穩定性錯誤。

錯誤插入及叢集分析服務 （也稱為故障分析服務） 可讓開發人員便會以測試服務故障動作。 不過，目標模擬的錯誤能協助您只為止。 若要測試進一步，您可以使用混亂。

混亂模擬連續、 交錯錯誤 （正常和優雅） 整個叢集透過長時間。 您設定混亂工資率] 和 [錯誤類型之後，您可以啟動或停止透過 C# Api 或 PowerShell 叢集和您的服務中產生錯誤。

執行混亂時，它會產生不同的事件擷取執行在目前的狀態。 例如，ExecutingFaultsEvent 包含執行該反覆項目中的所有錯誤。 ValidationFailedEvent 包含找不到期間叢集驗證失敗的詳細資料。 您可以叫用 GetChaosReportAsync API 混亂執行報表。

## <a name="faults-induced-in-chaos"></a>在混亂產生的錯誤
混亂整個服務布料的轉印圖樣叢集產生錯誤並將幾個小時月或年會看到的錯誤。 高故障率交錯錯誤的組合會找出否則未接的角落案例。 這個混亂練習會導致大幅改善服務的程式碼品質。

混亂引發錯誤從下列類別︰

 - 重新啟動節點
 - 重新啟動已部署程式碼套件
 - 移除複本
 - 重新啟動複本
 - 移動主要複本 （設定）
 - 移動次要複本 （設定）

在多個反覆運算中，執行混亂。 每次所組成的錯誤和指定期間內的叢集驗證。 您可以設定以穩定叢集和驗證成功所花費的時間。 如果無法找到叢集驗證混亂產生，並持續 ValidationFailedEvent UTC 時間戳記與失敗詳細資料。

例如，請考慮設定為執行一個小時，最大值的三個同時錯誤的混亂的執行個體。 混亂引發三個錯誤，然後驗證叢集健康狀況。 它會重複上一個步驟，直到它是明確地透過 StopChaosAsync API 停止或一小時傳遞。 如果叢集會變成不佳的任何反覆運算 （也就是它不會不穩定設定的時間內），混亂會產生 ValidationFailedEvent。 項目有任何錯誤，可能需要進一步調查，則會指出此事件。

在目前的表單中，混亂會引發安全的錯誤。 這表示，在沒有外部錯誤時，仲裁遺失或資料遺失不會發生。

## <a name="important-configuration-options"></a>重要的設定選項
 - **TimeToRun**︰ 各項總時間的混亂就會執行，才能成功完成。 執行完畢 TimeToRun 期間透過 StopChaos API 之前，您可以停止混亂。
 - **MaxClusterStabilizationTimeout**︰ 的最大的前一次檢查其變得健全叢集等待的時間量。 這個等待是減少叢集上的載入時，會復原。 檢查項目執行如下︰
    - 如果是叢集狀況
    - 如果是服務健康狀況
    - 如果目標複本設定大小的方法是服務磁碟分割
    - 沒有 InBuild 複本存在
 - **MaxConcurrentFaults**︰ 同時在每次所產生的錯誤的最大數目。 為較高的數字、 更積極混亂。 這會更複雜的容錯移轉和轉場效果組合。 混亂保證，沒有外部錯誤時，沒有仲裁遺失或資料遺失，無論高值，此設定。
 - **EnableMoveReplicaFaults**︰ 啟用或停用的錯誤導致移動主要或次要複本。 預設會停用這些錯誤。
 - **WaitTimeBetweenIterations**︰ 之後的錯誤和對應的驗證等候高次數，也就是之間的時間量。
 - **WaitTimeBetweenFaults**︰ 兩個連續的錯誤，在反覆運算之間等待的時間量。

## <a name="how-to-run-chaos"></a>如何執行混亂
**C# 中︰**

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using System.Fabric;

using System.Diagnostics;
using System.Fabric.Chaos.DataStructures;

class Program
{
    private class ChaosEventComparer : IEqualityComparer<ChaosEvent>
    {
        public bool Equals(ChaosEvent x, ChaosEvent y)
        {
            return x.TimeStampUtc.Equals(y.TimeStampUtc);
        }

        public int GetHashCode(ChaosEvent obj)
        {
            return obj.TimeStampUtc.GetHashCode();
        }
    }

    static void Main(string[] args)
    {
        var clusterConnectionString = "localhost:19000";
        using (var client = new FabricClient(clusterConnectionString))
        {
            var startTimeUtc = DateTime.UtcNow;
            var stabilizationTimeout = TimeSpan.FromSeconds(30.0);
            var timeToRun = TimeSpan.FromMinutes(60.0);
            var maxConcurrentFaults = 3;

            var parameters = new ChaosParameters(
                stabilizationTimeout,
                maxConcurrentFaults,
                true, /* EnableMoveReplicaFault */
                timeToRun);

            try
            {
                client.TestManager.StartChaosAsync(parameters).GetAwaiter().GetResult();
            }
            catch (FabricChaosAlreadyRunningException)
            {
                Console.WriteLine("An instance of Chaos is already running in the cluster.");
            }

            var filter = new ChaosReportFilter(startTimeUtc, DateTime.MaxValue);

            var eventSet = new HashSet<ChaosEvent>(new ChaosEventComparer());

            while (true)
            {
                var report = client.TestManager.GetChaosReportAsync(filter).GetAwaiter().GetResult();

                foreach (var chaosEvent in report.History)
                {
                    if (eventSet.add(chaosEvent))
                    {
                        Console.WriteLine(chaosEvent);
                    }
                }

                // When Chaos stops, a StoppedEvent is created.
                // If a StoppedEvent is found, exit the loop.
                var lastEvent = report.History.LastOrDefault();

                if (lastEvent is StoppedEvent)
                {
                    break;
                }

                Task.Delay(TimeSpan.FromSeconds(1.0)).GetAwaiter().GetResult();
            }
        }
    }
}
```
**PowerShell:**

```powershell
$connection = "localhost:19000"
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Connect-ServiceFabricCluster $connection

$events = @{}
$now = [System.DateTime]::UtcNow

Start-ServiceFabricChaos -TimeToRunMinute $timeToRun -MaxConcurrentFaults $concurrentFaults -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec

while($true)
{
    $stopped = $false
    $report = Get-ServiceFabricChaosReport -StartTimeUtc $now -EndTimeUtc ([System.DateTime]::MaxValue)

    foreach ($e in $report.History) {

        if(-Not ($events.Contains($e.TimeStampUtc.Ticks)))
        {
            $events.Add($e.TimeStampUtc.Ticks, $e)
            if($e -is [System.Fabric.Chaos.DataStructures.ValidationFailedEvent])
            {
                Write-Host -BackgroundColor White -ForegroundColor Red $e
            }
            else
            {
                if($e -is [System.Fabric.Chaos.DataStructures.StoppedEvent])
                {
                    $stopped = $true
                }

                Write-Host $e
            }
        }
    }

    if($stopped -eq $true)
    {
        break
    }

    Start-Sleep -Seconds 1
}

Stop-ServiceFabricChaos
```
