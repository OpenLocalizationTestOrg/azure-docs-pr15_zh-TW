<properties
   pageTitle="如何叫用服務布料的轉印圖樣服務的資料遺失 |Microsoft Azure"
   description="說明如何使用資料遺失 api"
   services="service-fabric"
   documentationCenter=".net"
   authors="LMWF"
   manager="rsinha"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/19/2016"
   ms.author="lemai"/>
   
# <a name="how-to-invoke-data-loss-on-services"></a>如何叫用服務的資料遺失

>[AZURE.WARNING] 這份文件會說明如何在您的服務，會造成資料遺失，應該謹慎使用。

## <a name="introduction"></a>簡介
您可以叫用在您的服務布料的轉印圖樣服務呼叫 StartPartitionDataLossAsync() 的磁碟分割的資料遺失。  此 api 會使用錯誤插入和分析服務可執行的工作，導致資料遺失條件。

## <a name="using-the-fault-injection-and-analysis-service"></a>使用錯誤插入和分析服務

錯誤插入及分析服務目前支援下列 Api 下列圖表中。  右側的圖表會顯示對應的 PowerShell 指令程式。  請參閱 msdn 上的文件的詳細資訊，在每一個的每個 API。

|           C# API                    |         PowerShell Cmdlet                      |
|-------------------------------------|-----------------------------------------------:|
|[StartPartitionDataLossAsync] [dl]   |[開始 ServiceFabricPartitionDataLoss] [psdl]   |
|[StartPartitionQuorumLossAsync] [ql] |[開始 ServiceFabricPartitionQuorumLoss] [psql] |
|[StartPartitionRestartAsync] [rp]    |[開始 ServiceFabricPartitionRestart] [psrp]    |

## <a name="conceptual-overview-of-running-a-command"></a>執行命令的概念的概觀

錯誤插入及分析服務使用 [非同步模型位置開始一個 API 的命令，稱為 「 開始 」 API 的此文件，然後檢查使用 「 GetProgress 」 API，直到達到終端機的狀態，或您取消此命令的進度。
若要開始命令，請連絡 「 開始 」 API 的相對應的 API。  此 API 傳回時的錯誤插入及分析服務已接受邀請。  不過，它不表示目前有執行命令，或甚至它尚未啟動。  若要檢查] 命令的進度，請打電話給 「 GetProgress 」 對應至先前稱為 「 開始 」 API 的 API。  「 GetProgress 」 API 會傳回指出其狀態屬性內命令的目前狀態的物件。  永遠直到會執行命令︰

1.  順利完成。  如果您 「 GetProgress 」 上呼叫它在此情況下，將完成進度物件的狀態。
2.  遇到嚴重錯誤。  如果您 「 GetProgress 」 上呼叫它在此情況下，會發生錯誤進度物件的狀態
3.  您取消透過[CancelTestCommandAsync]  [ cancel] API 或[停止 ServiceFabricTestCommand]  [ cancelps] PowerShell 指令程式。  如果您 「 GetProgress 」 上呼叫它在此情況下，進度物件的狀態會是 [已取消] 或 [ForceCancelled，根據的 API 的引數。  請參閱[CancelTestCommandAsync]的文件 [cancel]如需詳細資訊。


## <a name="details-of-running-a-command"></a>執行命令的詳細資料

若要啟動命令，呼叫開始 API 預期的引數。  所有開始 Api 有一個名為 operationId 的 Guid 引數。  您應該追踨的 operationId 引數，因為它用來追蹤進度的這個命令。  這必須傳遞到 「 GetProgress 」 API 以追蹤進度的命令。  OperationId 必須是唯一的。

在成功呼叫開始 API 之後, GetProgress API 應該呼叫循環等到完成後傳回的進度物件的 [狀態] 屬性。  所有[FabricTransientException 的] [fte]和 OperationCanceledException 的應該重試。
當命令達到終端機的狀態 （[已完成]、 [Faulted 或 [取消） 時，傳回的進度物件的 [結果] 屬性中有其他資訊。  如果完成狀態，則 Result.SelectedPartition.PartitionId 會包含已選取的磁碟分割識別碼。  Result.Exception 將為 null。  如果發生錯誤狀態，Result.Exception 會有錯誤插入原因，並分析服務發生錯誤的命令。  Result.SelectedPartition.PartitionId 會有已選取的磁碟分割識別碼。  在某些情況下，命令可能不有即可遠選擇資料分割。  在此情況下，PartitionId 為 0。  如果已取消] 狀態，Result.Exception 將為 null。  Faulted 大小寫，例如 Result.SelectedPartition.PartitionId 磁碟分割識別碼的選擇，但是如果命令有不即可遠這麼做，則為 0。  請也請參閱下面範例。

下列範例會示範如何開始，然後檢查] 命令，會導致在特定的磁碟分割的資料遺失的進度。

```csharp
    static async Task PerformDataLossSample()
    {
        // Create a unique operation id for the command below
        Guid operationId = Guid.NewGuid();

        // Note: Use the appropriate overload for your configuration
        FabricClient fabricClient = new FabricClient();

        // The name of the target service
        Uri targetServiceName = new Uri("fabric:/MyService");

        // The id of the target partition inside the target service
        Guid targetPartitionId = new Guid("00000000-0000-0000-0000-000002233445");

        PartitionSelector partitionSelector = PartitionSelector.PartitionIdOf(targetServiceName, targetPartitionId);

        // Start the command.  Retry OperationCanceledException and all FabricTransientException's.  Note when StartPartitionDataLossAsync completes
        // successfully it only means the Fault Injection and Analysis Service has saved the intent to perform this work.  It does not say anything about the progress
        // of the command.
        while (true)
        {
            try
            {
                await fabricClient.TestManager.StartPartitionDataLossAsync(operationId, partitionSelector, DataLossMode.FullDataLoss).ConfigureAwait(false);
                break;
            }
            catch (OperationCanceledException)
            {
            }
            catch (FabricTransientException)
            {
            }

            await Task.Delay(TimeSpan.FromSeconds(1.0d)).ConfigureAwait(false);
        }

        PartitionDataLossProgress progress = null;

        // Poll the progress using GetPartitionDataLossProgressAsync until it is either Completed or Faulted.  In this example, we're assuming
        // the command won't be cancelled.        

        while (true)
        {
            try
            {
                progress = await fabricClient.TestManager.GetPartitionDataLossProgressAsync(operationId).ConfigureAwait(false);
            }
            catch (OperationCanceledException)
            {
                continue;
            }
            catch (FabricTransientException)
            {
                continue;
            }

            if (progress.State == TestCommandProgressState.Completed)
            {
                Console.WriteLine("Command '{0}' completed successfully", operationId);

                // In a terminal state .Result.SelectedPartition.PartitionId will have the chosen partition
                Console.WriteLine("  Printing selected partition='{0}'", progress.Result.SelectedPartition.PartitionId);
                break;
            }
            else if (progress.State == TestCommandProgressState.Faulted)
            {
                // If State is Faulted, the progress object's Result property's Exception property will have the reason why.
                Console.WriteLine("Command '{0}' failed with '{1}'", operationId, progress.Result.Exception);
                break;
            }
            else
            {
                Console.WriteLine("Command '{0}' is currently Running", operationId);
            }

            await Task.Delay(TimeSpan.FromSeconds(5.0d)).ConfigureAwait(false);
        }
    }
```

下列範例會示範如何使用 PartitionSelector 選擇隨機指定服務的磁碟分割︰

```csharp
    static async Task PerformDataLossUseSelectorSample()
    {
        // Create a unique operation id for the command below
        Guid operationId = Guid.NewGuid();

        // Note: Use the appropriate overload for your configuration
        FabricClient fabricClient = new FabricClient();

        // The name of the target service
        Uri targetServiceName = new Uri("fabric:/SampleService ");

        // Use a PartitionSelector that will have the Fault Injection and Analysis Service choose a random partition of “targetServiceName”
        PartitionSelector partitionSelector = PartitionSelector.RandomOf(targetServiceName);

        // Start the command.  Retry OperationCanceledException and all FabricTransientException's.  Note when StartPartitionDataLossAsync completes
        // successfully it only means the Fault Injection and Analysis Service has saved the intent to perform this work.  It does not say anything about the progress
        // of the command.
        while (true)
        {
            try
            {
                await fabricClient.TestManager.StartPartitionDataLossAsync(operationId, partitionSelector, DataLossMode.FullDataLoss).ConfigureAwait(false);
                break;
            }
            catch (OperationCanceledException)
            {
            }
            catch (FabricTransientException)
            {
            }
            catch (Exception e)
            {
                Console.WriteLine("Unexpected exception '{0}'", e);
                throw;
            }

            await Task.Delay(TimeSpan.FromSeconds(1.0d)).ConfigureAwait(false);
        }

        PartitionDataLossProgress progress = null;

        // Poll the progress using GetPartitionDataLossProgressAsync until it is either Completed or Faulted.  In this example, we're assuming
        // the command won't be cancelled.

        while (true)
        {
            try
            {
                progress = await fabricClient.TestManager.GetPartitionDataLossProgressAsync(operationId).ConfigureAwait(false);
            }
            catch (OperationCanceledException)
            {
                continue;
            }
            catch (FabricTransientException)
            {
                continue;
            }

            if (progress.State == TestCommandProgressState.Completed)
            {
                Console.WriteLine("Command '{0}' completed successfully", operationId);

                Console.WriteLine("Printing progress.Result:");
                Console.WriteLine("  Printing selected partition='{0}'", progress.Result.SelectedPartition.PartitionId);

                break;
            }
            else if (progress.State == TestCommandProgressState.Faulted)
            {
                // If State is Faulted, the progress object's Result property's Exception property will have the reason why.
                Console.WriteLine("Command '{0}' failed with '{1}', SelectedPartition {2}", operationId, progress.Result.Exception, progress.Result.SelectedPartition);
                break;
            }
            else
            {
                Console.WriteLine("Command '{0}' is currently Running", operationId);
            }

            await Task.Delay(TimeSpan.FromSeconds(1.0d)).ConfigureAwait(false);
        }
    }
```

## <a name="history-and-truncation"></a>歷程記錄與無條件捨去

命令達到終端機的狀態後，它的中繼資料將會保留在錯誤插入及分析服務一段時間之前會以節省空間。  使用 operationId 的命令，將其移除後時，稱為 「 GetProgress 」，則會傳回與 KeyNotFound 錯誤碼 FabricException。

[dl]: https://msdn.microsoft.com/library/azure/mt693569.aspx
[ql]: https://msdn.microsoft.com/library/azure/mt693558.aspx
[rp]: https://msdn.microsoft.com/library/azure/mt645056.aspx
[psdl]: https://msdn.microsoft.com/library/mt697573.aspx
[psql]: https://msdn.microsoft.com/library/mt697557.aspx
[psrp]: https://msdn.microsoft.com/library/mt697560.aspx
[cancel]: https://msdn.microsoft.com/library/azure/mt668910.aspx
[cancelps]: https://msdn.microsoft.com/library/mt697566.aspx
[fte]: https://msdn.microsoft.com/library/azure/system.fabric.fabrictransientexception.aspx
