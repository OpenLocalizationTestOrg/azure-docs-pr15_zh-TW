<properties
    pageTitle="如何使用診斷 Azure 虛擬機器中 |Microsoft Azure"
    description="使用 Azure 診斷收集資料從 Azure 虛擬機器偵錯、 衡量效能、 監控、 流量分析及其他功能。"
    services="virtual-machines"
    documentationCenter=".net"
    authors="rboucher"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="virtual-machines"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="02/20/2016"
    ms.author="robb"/>



# <a name="enabling-diagnostics-in-azure-virtual-machines"></a>啟用診斷 Azure 虛擬機器中

Azure 診斷背景，請參閱[Azure 診斷程式概觀](azure-diagnostics.md)。

## <a name="how-to-enable-diagnostics-in-a-virtual-machine"></a>若要啟用診斷虛擬機器中的方式

此逐步說明如何從遠端安裝開發電腦從 Azure 虛擬機器診斷。 您也會學習如何實作該 Azure 虛擬機器上執行，並會發出使用.NET [EventSource 類別][]的遙測資料的應用程式。 Azure 診斷用來收集遙測，並將其儲存在 Azure 儲存體帳戶中。

### <a name="pre-requisites"></a>必要條件
此逐步假設您有 Azure 訂閱並使用 Azure SDK Visual Studio 2013。 如果您沒有 Azure 的訂閱，您可以註冊[免費試用版][]。 請務必[安裝及設定 PowerShell 的 Azure 0.8.7 版本或更新版本][]。

### <a name="step-1-create-a-virtual-machine"></a>步驟 1︰ 建立虛擬機器
1.  開發在電腦上，啟動 [Visual Studio 2013]。
2.  Visual Studio**伺服器總管**] 中展開**Azure**，以滑鼠右鍵按一下**虛擬機器**，然後選取 [**建立虛擬機器**。
3.  在 [**選擇訂閱**對話方塊中選取 Azure 訂閱，然後按一下 [**下一步**]。
4.  選取 [**選取虛擬機器圖像**] 對話方塊中的**Windows Server 2012 R2 資料中心，2014 年 11 月**，然後按一下 [**下一步**]。
5.  在**基本設定的虛擬機器**中，設定虛擬機器名稱以 「 wadexample 」。 設定您的系統管理員使用者名稱和密碼，然後按一下 [**下一步**]。
6.  在**雲端服務設定**] 對話方塊中建立新的雲端服務，名為 「 wadexampleVM 」。 建立新的儲存體帳戶名稱為 「 wadexample 」，然後按一下 [**下一步**。
7.  按一下 [**建立**]。

### <a name="step-2-create-your-application"></a>步驟 2︰ 建立您的應用程式
1.  開發在電腦上，啟動 [Visual Studio 2013]。
2.  建立新 Visual C# 主控台應用程式為目標.NET Framework 4.5。 專案 「 WadExampleVM 」 的名稱。
    ![CloudServices_diag_new_project](./media/virtual-machines-dotnet-diagnostics/NewProject.png)
3.  下列程式碼取代 Program.cs 的內容。 類別**SampleEventSourceWriter**實作四筆記錄的方法︰ **SendEnums**、 **MessageMethod**、 **SetOther**和**HighFreq**。 第一個參數 WriteEvent 方法定義個別事件的識別碼。 執行方法實作無限迴圈呼叫每個記錄方法**SampleEventSourceWriter**類別中實作每 10 秒。

        using System;
        using System.Diagnostics;
        using System.Diagnostics.Tracing;
        using System.Threading;

        namespace WadExampleVM
        {
        sealed class SampleEventSourceWriter : EventSource
        {
            public static SampleEventSourceWriter Log = new SampleEventSourceWriter();
            public void SendEnums(MyColor color, MyFlags flags) { if (IsEnabled())  WriteEvent(1, (int)color, (int)flags); }// Cast enums to int for efficient logging.
            public void MessageMethod(string Message) { if (IsEnabled())  WriteEvent(2, Message); }
            public void SetOther(bool flag, int myInt) { if (IsEnabled())  WriteEvent(3, flag, myInt); }
            public void HighFreq(int value) { if (IsEnabled()) WriteEvent(4, value); }

        }

        enum MyColor
        {
            Red,
            Blue,
            Green
        }

        [Flags]
        enum MyFlags
        {
            Flag1 = 1,
            Flag2 = 2,
            Flag3 = 4
        }

        class Program
        {
        static void Main(string[] args)
        {
            Trace.TraceInformation("My application entry point called");

            int value = 0;

            while (true)
            {
                Thread.Sleep(10000);
                Trace.TraceInformation("Working");

                // Emit several events every time we go through the loop
                for (int i = 0; i < 6; i++)
                {
                    SampleEventSourceWriter.Log.SendEnums(MyColor.Blue, MyFlags.Flag2 | MyFlags.Flag3);
                }

                for (int i = 0; i < 3; i++)
                {
                    SampleEventSourceWriter.Log.MessageMethod("This is a message.");
                    SampleEventSourceWriter.Log.SetOther(true, 123456789);
                }

                if (value == int.MaxValue) value = 0;
                SampleEventSourceWriter.Log.HighFreq(value++);
            }

        }
        }
        }


4.  儲存檔案，然後選取 [建立您的程式碼的 [**建置**] 功能表的 [**建立的解決方案**。


### <a name="step-3-deploy-your-application"></a>步驟 3︰ 將應用程式部署
1.  以滑鼠右鍵按一下**WadExampleVM**專案**總管**] 中，然後選擇 [**在檔案總管中開啟資料夾**]。
2.  瀏覽至*bin\Debug*資料夾，然後複製所有檔案 (WadExampleVM.*)
3.  在**伺服器總管**虛擬機器上以滑鼠右鍵按一下，然後選擇 [**使用遠端桌面連線**。
4.  連線到 VM 後建立名為 WadExampleVM 和 [貼上您的應用程式檔案到資料夾的資料夾。
5.  啟動應用程式 WadExampleVM.exe。 您應該會看到空白主控台視窗。

### <a name="step-4-create-your-diagnostics-configuration-and-install-the-extension"></a>步驟 4︰ 建立您的診斷設定，並安裝副檔名
1.  下載到電腦開發公用設定檔案結構描述定義藉由執行下列動作的 PowerShell 命令︰

        (Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfig.xsd'

2.  在沒有開啟專案的專案您已經開啟，或在 Visual Studio 中執行個體中的 Visual Studio 中開啟新的 XML 檔案。 在 Visual Studio 中，選取 [**新增** -> **新項目** ->  **Visual C# 項目** -> **資料** -> **XML 檔案**。 「 WadExample.xml 」 的檔案名稱
3.  WadConfig.xsd 關聯設定檔。 請確定 [WadExample.xml 編輯器] 視窗是使用中視窗。 按下**F4**以開啟 [**屬性**] 視窗。 按一下 [**屬性**] 視窗中的 [**結構**] 屬性。 按一下 [ **...** 在 [**結構**] 屬性。 按一下 [**新增...** ] 按鈕，瀏覽至儲存 XSD 檔案的位置並選取檔案 WadConfig.xsd。 按一下**[確定]**。
4.  使用下列 XML 取代 WadExample.xml 設定檔的內容，然後儲存檔案。 此設定檔定義收集的幾個效能計數器︰ 的 cpu 和記憶體使用量的。 然後設定定義四個事件對應至 SampleEventSourceWriter 類別中的方法。

```
        <?xml version="1.0" encoding="utf-8"?>
        <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
            <WadCfg>
                <DiagnosticMonitorConfiguration overallQuotaInMB="25000">
                <PerformanceCounters scheduledTransferPeriod="PT1M">
                    <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />
                    <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT1M" unit="bytes"/>
                    </PerformanceCounters>
                    <EtwProviders>
                        <EtwEventSourceProviderConfiguration provider="SampleEventSourceWriter" scheduledTransferPeriod="PT5M">
                            <Event id="1" eventDestination="EnumsTable"/>
                            <Event id="2" eventDestination="MessageTable"/>
                            <Event id="3" eventDestination="SetOtherTable"/>
                            <Event id="4" eventDestination="HighFreqTable"/>
                            <DefaultEvents eventDestination="DefaultTable" />
                        </EtwEventSourceProviderConfiguration>
                    </EtwProviders>
                </DiagnosticMonitorConfiguration>
            </WadCfg>
        </PublicConfig>
```

### <a name="step-5-remotely-install-diagnostics-on-your-azure-virtual-machine"></a>步驟 5︰ 從遠端安裝診斷 Azure 虛擬機器上
管理上 VM 診斷 PowerShell 指令程式︰ 設定 AzureVMDiagnosticsExtension、 取得 AzureVMDiagnosticsExtension 及移除 AzureVMDiagnosticsExtension。

1.  開發人員在電腦上，開啟 PowerShell 的 Azure。
2.  執行遠端安裝您 VM (取代*StorageAccountKey*以儲存的帳戶金鑰 wadexamplevm 儲存帳戶) 中的 [診斷指令碼︰

        $storage_name = "wadexamplevm"
        $key = "<StorageAccountKey>"
        $config_path="c:\users\<user>\documents\visual studio 2013\Projects\WadExampleVM\WadExampleVM\WadExample.xml"
        $service_name="wadexamplevm"
        $vm_name="WadExample"
        $storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
        $VM1 = Get-AzureVM -ServiceName $service_name -Name $vm_name
        $VM2 = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $config_path -Version "1.*" -VM $VM1 -StorageContext $storageContext
        $VM3 = Update-AzureVM -ServiceName $service_name -Name $vm_name -VM $VM2.VM


### <a name="step-6-look-at-your-telemetry-data"></a>步驟 6︰ 查看遙測資料
在 Visual Studio**伺服器總管**] 中瀏覽至 wadexample 儲存帳戶。 執行 VM 約 5 分鐘之後，您應該會看到**WADEnumsTable**、 **WADHighFreqTable**、 **WADMessageTable**、 **WADPerformanceCountersTable**和**WADSetOtherTable**的資料表。 若要檢視已經收集遙測的資料表上按兩下。

![CloudServices_diag_wadexamplevm_tables](./media/virtual-machines-dotnet-diagnostics/WadExampleVMTables.png)

## <a name="configuration-file-schema"></a>設定檔案結構描述

診斷設定檔定義所使用的診斷代理程式啟動時初始化診斷設定的設定值。 請參閱[最新的結構描述參考](https://msdn.microsoft.com/library/azure/mt634524.aspx)有效的值和範例。

## <a name="troubleshooting"></a>疑難排解

如需詳細資訊，請參閱[疑難排解 Azure 診斷程式](azure-diagnostics-troubleshooting.md)。


## <a name="next-steps"></a>後續步驟
若要變更您收集的資料的[虛擬機器的清單，請參閱相關 Azure 診斷文章](azure-diagnostics.md#virtual-machines-using-azure-diagnostics)疑難排解問題或進一步瞭解診斷一般。


[EventSource 類別]: http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx

[Debugging an Azure Application]: http://msdn.microsoft.com/library/windowsazure/ee405479.aspx   
[Collect Logging Data by Using Azure Diagnostics]: http://msdn.microsoft.com/library/windowsazure/gg433048.aspx
[免費試用版]: http://azure.microsoft.com/pricing/free-trial/
[安裝和設定 PowerShell 的 Azure 0.8.7 版本或更新版本]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/
