<properties
    pageTitle="疑難排解 Azure 診斷程式"
    description="疑難排解問題時使用 Azure 雲端服務，虛擬機器中的 Azure 診斷和 "
    services="multiple"
    documentationCenter=".net"
    authors="rboucher"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="robb"/>


# <a name="azure-diagnostics-troubleshooting"></a>Azure 診斷疑難排解
疑難排解使用 Azure 診斷程式的相關資訊。 如需有關 Azure 診斷的詳細資訊，請參閱[Azure 診斷概觀](azure-diagnostics.md#cloud-services)。

## <a name="azure-diagnostics-is-not-starting"></a>Azure 診斷無法啟動

診斷包含兩個元件︰ 來賓代理程式外掛程式和監視代理程式。 您可以檢查為何無法啟動診斷的詳細資訊的記錄檔**DiagnosticsPluginLauncher.log**和**DiagnosticsPlugin.log** 。  
  
在雲端服務的角色，來賓代理程式外掛程式的記錄檔位於︰ 

``` 
C:\Logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.6.3.0\ 
``` 

Azure 虛擬機器，在記錄檔，以來賓代理程式外掛程式位於︰ 

``` 
C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\1.6.3.0\Logs\ 
``` 
 
最後一行的記錄檔所包含的結束。  

``` 
DiagnosticsPluginLauncher.exe Information: 0 : [4/16/2016 6:24:15 AM] DiagnosticPlugin exited with code 0 
``` 

外掛程式會傳回下列結束代碼︰

結束程式碼|描述
---|---
0|成功。
-1|一般錯誤。
-2|無法載入 rcf 檔案。<p>這是如果來賓代理程式外掛程式啟動器手動啟動，不正確，VM 上應該只會發生內部錯誤。
-3|無法載入診斷設定檔。<p><p>解決方案︰ 這是無法傳遞結構描述驗證的設定檔的結果。 解決方法是提供符合的設定檔與結構描述。
-4|監控代理程式診斷的另一個執行個體已經在使用本機資源目錄。<p><p>解決方案︰ **LocalResourceDirectory**指定不同的值。
-6|嘗試啟動診斷無效的命令列使用來賓代理程式外掛程式啟動器。<p><p>這是如果來賓代理程式外掛程式啟動器手動啟動，不正確，VM 上應該只會發生內部錯誤。
-10|診斷外掛程式結束，處理的例外狀況。
-11|來賓代理程式無法建立負責啟動，並監控監控代理程式的程序。<p><p>解決方案︰ 確認系統資源可啟動新的程序。<p>
-101|不正確的引數呼叫診斷外掛程式時。<p><p>這是如果來賓代理程式外掛程式啟動器手動啟動，不正確，VM 上應該只會發生內部錯誤。
-102|外掛程式程序，無法將初始化本身。<p><p>解決方案︰ 確認系統資源可啟動新的程序。
-103|外掛程式程序，無法將初始化本身。 特別是無法建立記錄器物件。<p><p>解決方案︰ 確認系統資源可啟動新的程序。
-104|無法載入來賓代理程式所提供的 rcf 檔案。<p><p>這是如果來賓代理程式外掛程式啟動器手動啟動，不正確，VM 上應該只會發生內部錯誤。
-105|診斷外掛程式無法開啟診斷設定檔。<p><p>這是如果診斷外掛程式手動啟動，不正確，VM 上應該只會發生內部錯誤。
-106|無法讀取診斷設定檔。<p><p>解決方案︰ 這是無法傳遞結構描述驗證的設定檔的結果。 解決辦法是使提供符合的設定檔與結構描述。 您可以尋找已傳送診斷副檔名 VM 上資料夾*%SystemDrive%\WindowsAzure\Config*中的 XML。 開啟適當的 XML 檔案和搜尋**Microsoft.Azure.Diagnostics**，然後**xmlCfg**欄位。 資料是 base64 編碼，因此您需要查看已由診斷載入 XML[解碼它](http://www.bing.com/search?q=base64+decoder)。<p>
-107|監控代理程式的資源目錄傳遞不正確。<p><p>這是如果監控代理程式手動啟動，不正確，VM 上應該只會發生內部錯誤。</p>
-108    |無法將診斷設定檔案轉換成監控代理程式的設定檔。<p><p>這是如果診斷外掛程式手動叫用的不正確的設定檔應該只會發生內部錯誤。
-110|一般診斷程式設定錯誤。<p><p>這是如果診斷外掛程式手動叫用的不正確的設定檔應該只會發生內部錯誤。
-111|無法啟動監控代理程式。<p><p>解決方案︰ 確認足夠的 「 系統 」 資源可供使用。
-112|一般錯誤


## <a name="diagnostics-data-is-not-logged-to-azure-storage"></a>診斷資料是並未登入 Azure 儲存體
Azure 診斷儲存 Azure 儲存體中的所有資料。

遺漏事件資料的最常見的原因是正確的已定義的儲存空間帳戶資訊。

解決辦法︰ 更正診斷設定檔案，然後重新安裝診斷。
如果問題持續發生，然後偵錯時，您可能必須重新安裝診斷副檔名後進一步尋找透過任何監控代理程式錯誤。 事件資料上傳您儲存的帳戶之前就會儲存於 LocalResourceDirectory。

雲端服務角色 LocalResourceDirectory 是︰

    C:\Resources\Directory\<CloudServiceDeploymentID>.<RoleName>.DiagnosticStore\WAD<DiagnosticsMajorandMinorVersion>\Tables

虛擬機器 LocalResourceDirectory 是︰

    C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD<DiagnosticsMajorandMinorVersion>\Tables

如果沒有 [LocalResourceDirectory] 資料夾中的檔案，監控代理程式無法啟動。 這通常被因為不正確的設定檔中，應該 CommandExecution.log 中報告的事件。

如果監控代理程式順利收集事件資料，您會看到您的設定檔中定義每項事件.tsf 檔案。 監控代理程式中的檔案 MaEventTable.tsf 記錄其錯誤。 若要檢查此檔案的內容中，您可以使用 tabel2csv 應用程式，將.tsf 檔案轉換為逗點分隔的 values(.csv) 檔案︰

在雲端服務角色︰

    %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<DiagnosticsVersion>\Monitor\x64\table2csv maeventtable.tsf

在雲端服務角色*%系統磁碟機 %*通常是 d

虛擬機器中︰

    C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\Monitor\x64\table2csv maeventtable.tsf

上述的命令會產生記錄檔*maeventtable.csv*，您可以開啟，並檢查錯誤訊息。    


## <a name="diagnostics-data-tables-not-found"></a>診斷資料找不到的表格
使用下列程式碼命名 Azure Azure 診斷資料的儲存空間中的資料表︰

        if (String.IsNullOrEmpty(eventDestination)) {
            if (e == "DefaultEvents")
                tableName = "WADDefault" + MD5(provider);
            else
                tableName = "WADEvent" + MD5(provider) + eventId;
        }
        else
            tableName = "WAD" + eventDestination;

以下是範例︰

        <EtwEventSourceProviderConfiguration provider=”prov1”>
          <Event id=”1” />
          <Event id=”2” eventDestination=”dest1” />
          <DefaultEvents />
        </EtwEventSourceProviderConfiguration>
        <EtwEventSourceProviderConfiguration provider=”prov2”>
          <DefaultEvents eventDestination=”dest2” />
        </EtwEventSourceProviderConfiguration>

就會產生 4 的表格︰

事件|資料表名稱
---|---
提供者 = 「 prov1 」&lt;事件識別碼 = 「 1 」 /&gt;|WADEvent + MD5("prov1") + 「 1 」
提供者 = 「 prov1 」&lt;事件識別碼 = 「 2 」 eventDestination = 「 dest1 」 /&gt;|WADdest1
提供者 = 「 prov1 」 &lt;DefaultEvents /&gt;|WADDefault+MD5("prov1")
提供者 = 「 prov2 」 &lt;DefaultEvents eventDestination = 「 dest2 」 /&gt;|WADdest2
