<properties 
    pageTitle="分析計算模擬器中本機雲端服務 |Microsoft Azure" 
    services="cloud-services"
    description="使用 Visual Studio 分析工具調查雲端服務中的效能問題" 
    documentationCenter=""
    authors="TomArcher" 
    manager="douge" 
    editor=""
    tags="" 
    />

<tags 
    ms.service="cloud-services" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="multiple" 
    ms.topic="article" 
    ms.date="07/30/2016" 
    ms.author="tarcher"/>

# <a name="testing-the-performance-of-a-cloud-service-locally-in-the-azure-compute-emulator-using-the-visual-studio-profiler"></a>使用 Visual Studio 分析工具 Azure 計算模擬器中本機測試雲端服務的效能

各種不同的工具和技術可供測試雲端服務的效能。
當您以 Azure 發佈雲端服務時，您可以有 Visual Studio 收集分析資料，然後為[剖析 Azure 應用程式]所述，分析[1]。
您也可以使用診斷追蹤各種不同的效能計數器[使用效能計數器 Azure 中的]所述[2]。
您也可以在設定檔本機計算模擬器中的您應用程式，再將其部署到雲端。

本文涵蓋分析，可在模擬器本機完成的 CPU 取樣的方法。 CPU 取樣是不是非常侵入剖析的方法。 指定的取樣間隔分析工具會呼叫堆疊的快照。 資料收集一段時間，並顯示在報表中。 這個剖析方法可能會指出位置計算資源的應用程式中大部分的 CPU 工作完成。  這樣您就可以將焦點放在 「 熱門路徑 」 應用程式會花費最多時間的位置。



## <a name="1-configure-visual-studio-for-profiling"></a>1︰ 設定 Visual Studio 的設定檔

首先，有幾個分析時可能會很有幫助的 Visual Studio 設定選項。 若要有意義的分析報表，您需要符號 （.pdb 檔案） 應用程式及也系統文件庫的符號。 您需要，請確定您參照可用符號伺服器。 若要這麼做，在 Visual Studio 中的 [**工具**] 功能表上，選擇 [**選項**]，然後選擇 [**偵錯**，然後**符號**。 請確定 Microsoft 符號伺服器會列出**符號檔案 (.pdb) 位置**] 下。  您也可以參考 http://referencesource.microsoft.com/symbols，可能會有其他符號檔案。

![符號選項][4]

如有需要，您可以簡化分析工具會產生設定只是我的程式碼的報表。 只要我程式碼啟用，讓文件庫和.NET Framework 完全內部通話會從報表隱藏的簡化函數呼叫堆疊。 在 [**工具**] 功能表上選擇 [**選項**]。 展開 [**效能工具**節點，然後選擇 [**一般**。 **啟用只是我的程式碼分析工具報表**，請選取核取方塊。

![只要我的程式碼選項][17]

使用現有的專案，或使用新的專案，您可以使用下列指示進行。  如果您建立新的專案時的嘗試如下所述的技巧，選擇 [C# **Azure 雲端服務**專案，並選取**網頁角色**及**工作角色**。

![Azure 雲端服務專案角色][5]

例如的目的，將一些程式碼新增至您的專案，以取得大量時間，並示範一些明顯的效能問題。 例如，新增下列程式碼工作者角色專案︰

    public class Concatenator
    {
        public static string Concatenate(int number)
        {
            int count;
            string s = "";
            for (count = 0; count < number; count++)
            {
                s += "\n" + count.ToString();
            }
            return s;
        }
    }

將此程式碼的來電 RunAsync 類別中的方法工作者角色的 RoleEntryPoint 衍生。 （略過]，在警告執行同步的方法）。

        private async Task RunAsync(CancellationToken cancellationToken)
        {
            // TODO: Replace the following with your own logic.
            while (!cancellationToken.IsCancellationRequested)
            {
                Trace.TraceInformation("Working");
                Concatenator.Concatenate(10000);
            }
        }

建立並執行您的雲端服務本機但不偵錯 (Ctrl + F5)，含有**發行**方案設定。 這可確保所有檔案和資料夾會建立本機，執行應用程式，並確保所有模擬器會都啟動。 從工作列啟動計算模擬器使用者介面，以驗證您的工作者角色正在執行。

## <a name="2-attach-to-a-process"></a>2︰ 將附加至程序

而不是由自 Visual Studio 2010 IDE 分析應用程式，您必須執行的程序附加分析工具。 

若要將分析工具附加至程序，請按一下 [**分析**] 功能表中，選擇 [**分析工具**和**附加/中斷連結**]。

![附加的設定檔選項][6]

針對工作者角色，找到 WaWorkerHost.exe 程序。

![WaWorkerHost 程序][7]

如果您的專案資料夾位於網路磁碟機，分析工具會要求您提供儲存分析報表的其他位置。

 您也可以將 WaIISHost.exe 附加至 web 角色。
如果您的應用程式中有多個背景工作角色程序，您需要使用在 processID 對其進行區分。 您可以存取的程序的物件，以程式設計方式查詢，其中 processID。 例如，如果您將此程式碼新增至 RoleEntryPoint 衍生的類別角色中的 [執行] 方法時，您可以查看計算模擬器 UI 知道連線至何種程序中的登入。

    var process = System.Diagnostics.Process.GetCurrentProcess();
    var message = String.Format("Process ID: {0}", process.Id);
    Trace.WriteLine(message, "Information");

若要檢視的記錄，開始在計算模擬器 ui。

![開始計算模擬器 UI][8]

開啟背景工作角色記錄主控台視窗計算模擬器 UI 主控台視窗標題列上的。 您可以看到記錄檔中的程序識別碼。

![檢視程序識別碼][9]

您已附加，一個執行中的步驟應用程式的使用者介面 （如有需要） 重現案例。

當您想要停止分析時，選擇 [**停止分析**] 連結。

![停止分析選項][10]

## <a name="3-view-performance-reports"></a>3︰ 檢視效能報告

會顯示您的應用程式的效能報告。

此時，分析工具會停止執行、.vsp 檔案]，以儲存資料，並顯示報表，其中顯示的資料分析。

![分析報表][11]


如果您看到 String.wstrcpy 熱路徑中的，按一下剛才我碼來變更檢視，以顯示 [只有使用者程式碼。  如果您看到 String.Concat，請嘗試按 [顯示所有的程式碼] 按鈕。

您應該會看到 Concatenate 方法和 String.Concat 佔用大部分的執行時間。

![報表的分析][12]

如果您在本文中新增的字串串連程式碼，您應該會看到這個的工作清單中的警告。 您可能也會看到有過多金額回收，這是因為的字串，來建立並處置數目的警告。

![效能警告][14]

## <a name="4-make-changes-and-compare-performance"></a>4︰ 變更，比較效能

您也可以比較之前和之後變更程式碼的效能。  停止執行的程序，並編輯程式碼，取代 StringBuilder 使用中的字串串連作業︰

    public static string Concatenate(int number)
    {
        int count;
        System.Text.StringBuilder builder = new System.Text.StringBuilder("");
        for (count = 0; count < number; count++)
        {
             builder.Append("\n" + count.ToString());
        }
        return builder.ToString();
    }

執行其他的效能執行，然後比較效能。 在效能總管] 中，如果在執行位於相同的工作階段，您可以只選取兩個報表、 開啟捷徑功能表，然後選擇**比較效能報告**。 如果您想要比較與另一個效能工作階段中執行，請開啟 [**分析**] 功能表，然後選擇**比較效能報告**。 在出現的對話方塊中，指定兩個檔案。

![比較效能報告] 選項][15]

報表反白顯示兩個執行之間的差異。

![比較報表][16]

恭喜您 ！ 您已卡住入門分析工具。

## <a name="troubleshooting"></a>疑難排解

- 請確定您的設定檔的發行版本，並沒有偵錯的情況下啟動。

- 如果 [附加/中斷連結] 選項未啟用在分析工具] 功能表上，執行效能精靈。

- 使用計算模擬器 UI 若要檢視您的應用程式的狀態。 

- 如果您有在模擬器中啟動應用程式的問題，或附加分析工具，關閉向下計算模擬器，並重新啟動它。 如果這樣無法解決問題，請嘗試重新啟動。 如果您使用計算模擬器暫停並移除執行部署時，會發生此問題。

- 如果您已使用任何的 [分析] 命令從命令列，尤其是通用的設定，請確定已經呼叫 VSPerfClrEnv /globaloff 和 VsPerfMon.exe 已關閉。

- 如果在取樣，您會看到訊息 「 PRF0025︰ 已收集任何資料，] 核取 [您要附加的程序有 CPU 活動。 不會進行計算的任何工作的應用程式可能會產生任何範例資料。  您也可處理序結束任何取樣前。 若要查看的角色，您的設定檔的執行方式不會結束的核取。

## <a name="next-steps"></a>後續步驟

操作 Azure 的二進位檔案在模擬器中不支援 Visual Studio 分析工具，但如果您想要測試的記憶體配置，分析時，您可以選擇該選項。 您也可以選擇並行分析，這可協助您判斷執行緒是否浪費時間競爭鎖定，或層互動分析，可協助您追蹤效能問題時互動之間的應用程式，最常之間資料層和工作者角色層。  您可以檢視您的應用程式會產生資料庫查詢，並使用分析資料以改善您使用的資料庫。 層互動設定檔的相關資訊，請參閱部落格文章[逐步解說︰ 使用 Visual Studio 小組系統 2010年中的層互動分析工具][3]。



[1]: http://msdn.microsoft.com/library/azure/hh369930.aspx
[2]: http://msdn.microsoft.com/library/azure/hh411542.aspx
[3]: http://blogs.msdn.com/b/habibh/archive/2009/06/30/walkthrough-using-the-tier-interaction-profiler-in-visual-studio-team-system-2010.aspx
[4]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally09.png
[5]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally10.png
[6]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally02.png
[7]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally05.png
[8]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally010.png
[9]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally07.png
[10]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally06.png
[11]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally03.png
[12]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally011.png
[14]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally04.png 
[15]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally013.png
[16]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally012.png
[17]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally08.png
 