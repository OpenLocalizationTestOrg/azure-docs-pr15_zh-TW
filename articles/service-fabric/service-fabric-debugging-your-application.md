<properties
   pageTitle="偵錯 Visual Studio 中的應用程式 |Microsoft Azure"
   description="開發和偵錯時，其在 Visual Studio 中的本機開發叢集上以改善可靠性和效能的服務。"
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/21/2016"
   ms.author="vturecek;mikhegn"/>

# <a name="debug-your-service-fabric-application-by-using-visual-studio"></a>使用 Visual Studio 偵錯應用程式服務布料的轉印圖樣

## <a name="debug-a-local-service-fabric-application"></a>偵錯本機的服務布料的轉印圖樣應用程式

您可以節省時間和金錢部署和偵錯 Azure 服務布料的轉印圖樣應用程式中的本機電腦開發叢集。 Visual Studio 可以部署到本機叢集應用程式，並自動連線到您的應用程式的所有執行個體的 [偵錯工具。

1. 首先本機開發叢集[設定您的服務布料的轉印圖樣的開發環境](service-fabric-get-started.md)中的步驟進行。

2. 按**F5** ，或按一下 [**偵錯** > **啟動偵錯**。

    ![啟動偵錯應用程式][startdebugging]

3. 設定中斷點代碼與透過應用程式的步驟，即可**偵錯**] 功能表中的命令。

    > [AZURE.NOTE] Visual Studio 附加至您的應用程式的所有例項。 當您正在逐步執行程式碼時，並產生同時工作階段中的多個程序可能會取得叫用中斷點。 請嘗試這些叫用，讓每一個中斷點上執行緒識別碼條件或使用的診斷事件之後，停用中斷點。

4. **診斷事件**會自動開啟，讓您可以即時檢視診斷事件。

    ![即時檢視診斷事件][diagnosticevents]

5. 您也可以在雲端的檔案總管中開啟 [**診斷事件**] 視窗。  在**服務布料的轉印圖樣**，以滑鼠右鍵按一下任何節點，選擇 [**檢視串流追蹤**。

    ![開啟 [診斷事件] 視窗][viewdiagnosticevents]

    如果您想要篩選您追蹤特定的服務或應用程式，即可啟用串流追蹤特定的服務或應用程式上。

6. 診斷事件能看到自動產生的**ServiceEventSource.cs**檔案中，稱為 [從應用程式碼。

    ```csharp
    ServiceEventSource.Current.ServiceMessage(this, "My ServiceMessage with a parameter {0}", result.Value.ToString());
    ```

7. [**診斷事件**] 視窗中支援的篩選、 暫停，及透過檢查即時事件。  篩選器是簡單的字串搜尋事件訊息，包括其內容。

    ![篩選、 暫停並繼續或檢查即時事件][diagnosticeventsactions]

8. 偵錯服務，就像偵錯時，任何其他應用程式。 您通常會設定中斷點透過 Visual Studio 輕鬆偵錯。 即使可靠的集合複製跨多個節點，他們仍實作 IEnumerable。 這表示，您可以使用 [結果] 檢視中 Visual Studio 偵錯時若要查看您已儲存內。 只要任意處程式碼中設定中斷點。

    ![啟動偵錯應用程式][breakpoint]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="debug-a-remote-service-fabric-application"></a>偵錯遠端服務布料的轉印圖樣應用程式

如果在 Azure 服務布料的轉印圖樣叢集上執行的服務布料的轉印圖樣應用程式，也可以從遠端偵錯這些項目，直接從 Visual Studio。

> [AZURE.NOTE] 此功能需要[服務布料的轉印圖樣 SDK 2.0](http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015)與[.NET 2.9 Azure SDK](https://azure.microsoft.com/downloads/)。    

<!-- -->
> [AZURE.WARNING] 遠端偵錯是用來開發/測試案例，而非生產環境中使用因為上執行應用程式的影響。

1. 瀏覽至您的叢集在**雲端的檔案總管**中，以滑鼠右鍵按一下，然後選擇 [**啟用偵錯**

    ![啟用遠端偵錯][enableremotedebugging]

    這會開始啟用遠端偵錯副檔名為您叢集節點，以及必要的網路設定的程序。

2. 叢集節點在**雲端的檔案總管**中，以滑鼠右鍵按一下，然後選擇 [**附加偵錯工具**

    ![附加偵錯工具][attachdebugger]

3. 在 [**附加至 [處理程序**] 對話方塊中，選擇您想要偵錯，此程的序，按一下 [**附加**]

    ![選擇 [處理程序][chooseprocess]

    您想要附加至]，此程序的名稱等服務的專案的組件名稱的名稱。

    偵錯工具會附加到所有的節點執行程序。
    - 您將在偵錯無服務的大小寫，在所有執行個體上所有的節點會是服務的偵錯工作階段的一部分。
    - 如果您有狀態的服務，任何分割的主要複本會使用中，因此攔截偵錯工具。 如果主要複本移動偵錯工作階段期間，該複本的處理仍可偵錯工作階段的一部分。
    - 為了只攔截相關的磁碟分割或指定服務的執行個體，您可以使用條件中斷點只中斷特定的磁碟分割或執行個體。

    ![條件中斷點][conditionalbreakpoint]

    > [AZURE.NOTE] 目前我們不支援偵錯服務布料的轉印圖樣叢集多個執行個體的相同的服務可執行檔的名稱。

4. 當您完成偵錯時，您的應用程式時，您可以停用遠端偵錯副檔名，以滑鼠右鍵按一下 [在**雲端的檔案總管**中叢集，並選擇 [**停用偵錯**

    ![停用遠端偵錯][disableremotedebugging]

## <a name="streaming-traces-from-a-remote-cluster-node"></a>從遠端叢集節點串流追蹤

您也是可以串流追蹤直接從 Visual studio 遠端叢集節點。 此功能可讓您將資料流 ETW 追蹤事件服務布料的轉印圖樣叢集節點中，直接在 Visual Studio 中所產生。

> [AZURE.NOTE] 此功能需要[服務布料的轉印圖樣 SDK 2.0](http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015)與[.NET 2.9 Azure SDK](https://azure.microsoft.com/downloads/)。

<!-- -->
> [AZURE.WARNING]串流追蹤是用來開發/測試案例，而非生產環境中使用因為上執行應用程式的影響。
> 在生產案例中，您應該能轉接使用 Azure 診斷程式的事件。

1. 瀏覽至您的叢集在**雲端的檔案總管**中，以滑鼠右鍵按一下，然後選擇 [**啟用串流追蹤**

    ![啟用遠端串流追蹤][enablestreamingtraces]

    這會開始啟用串流追蹤延伸您叢集節點，以及必要的網路設定的程序。

2. 展開**節點**中的項目**雲端的檔案總管**]，以滑鼠右鍵按一下您想要將串流的追蹤，然後選擇 [**檢視串流追蹤**的節點

    ![遠端串流追蹤] 檢視][viewremotestreamingtraces]

    重複步驟 2 的任何數量的節點，當您想要查看的追蹤。 在固定視窗中，會顯示每個節點資料流。

    現在，您就可以查看服務布料的轉印圖樣和您的服務所發出的追蹤。 如果您想要篩選，僅顯示特定應用程式的事件，在 [名稱] 的應用程式，在篩選中直接輸入。

    ![檢視資料流追蹤][viewingstreamingtraces]

4. 在您完成串流追蹤從叢集，您可以停用遠端串流追蹤，以滑鼠右鍵按一下叢集在**雲端的檔案總管**中，選擇 [**停用串流追蹤**

    ![停用遠端串流追蹤][disablestreamingtraces]

## <a name="next-steps"></a>後續步驟

- [測試服務布料的轉印圖樣服務](service-fabric-testability-overview.md)。
- [管理您的服務布料的轉印圖樣應用程式，在 Visual Studio 中](service-fabric-manage-application-in-visual-studio.md)。

<!--Image references-->
[startdebugging]: ./media/service-fabric-debugging-your-application/startdebugging.png
[diagnosticevents]: ./media/service-fabric-debugging-your-application/diagnosticevents.png
[viewdiagnosticevents]: ./media/service-fabric-debugging-your-application/viewdiagnosticevents.png
[diagnosticeventsactions]: ./media/service-fabric-debugging-your-application/diagnosticeventsactions.png
[breakpoint]: ./media/service-fabric-debugging-your-application/breakpoint.png
[enableremotedebugging]: ./media/service-fabric-debugging-your-application/enableremotedebugging.png
[attachdebugger]: ./media/service-fabric-debugging-your-application/attachdebugger.png
[chooseprocess]: ./media/service-fabric-debugging-your-application/chooseprocess.png
[conditionalbreakpoint]: ./media/service-fabric-debugging-your-application/conditionalbreakpoint.png
[disableremotedebugging]: ./media/service-fabric-debugging-your-application/disableremotedebugging.png
[enablestreamingtraces]: ./media/service-fabric-debugging-your-application/enablestreamingtraces.png
[viewingstreamingtraces]: ./media/service-fabric-debugging-your-application/viewingstreamingtraces.png
[viewremotestreamingtraces]: ./media/service-fabric-debugging-your-application/viewremotestreamingtraces.png
[disablestreamingtraces]: ./media/service-fabric-debugging-your-application/disablestreamingtraces.png
