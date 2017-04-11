<properties
   pageTitle="在 [作業管理套件 (OMS) 中的應用程式相依性監視器 (ADM) |Microsoft Azure"
   description="應用程式相依性監視器 (ADM) 是一種作業管理套件 (OMS) 解決方案，自動探索 Windows 和 Linux 系統上的應用程式元件和地圖服務之間的通訊。  本文提供您的環境中部署 ADM 與它在許多情況下使用詳細資料。"
   services="operations-management-suite"
   documentationCenter=""
   authors="daseidma"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/28/2016"
   ms.author="daseidma;bwren" />

# <a name="using-application-dependency-monitor-solution-in-operations-management-suite-oms"></a>使用應用程式相依性監視器解決方案中作業管理套件 (OMS)
![管理通知] 圖示](media/operations-management-suite-application-dependency-monitor/icon.png) 應用程式相依性監視器 (ADM) 自動探索 Windows 和 Linux 系統上的應用程式元件和地圖服務之間的通訊。 其可讓您檢視您的伺服器，當您將這些視為互連式傳送重要的服務的系統。  應用程式相依性監視器顯示伺服器，處理程序，之間的連線，並在沒有設定任何 TCP 連接的架構的連接埠所需代理程式的安裝以外。

本文將說明使用應用程式相依性監視器的詳細資料。  設定 ADM 和登入代理程式的資訊，請參閱[設定應用程式相依性監視器解決方案中作業管理套件 (OMS)](operations-management-suite-application-dependency-monitor-configure.md)

>[AZURE.NOTE]應用程式相依性監視器目前正在私人的預覽。  您可以要求存取[https://aka.ms/getadm](https://aka.ms/getadm)ADM 私人預覽。
>
>私人預覽，在所有 OMS 帳戶都有 ADM.無限制的存取  ADM 節點免費提供，但會計量付費 AdmComputer_CL 和 AdmProcess_CL 類型的記錄檔分析資料，例如任何其他方案。
>
>ADM 輸入公用預覽之後，會使用免費與付費的充分並分析 OMS 價格計劃中的客戶。  免費層帳戶會限制為 5 ADM 節點。  如果您正在參與私人的預覽，無法註冊 OMS 價格計劃中 ADM 進入公用預覽時，會在該時間停用 ADM。 


## <a name="use-cases-make-your-it-processes-dependency-aware"></a>使用案例︰ 讓您的 IT 處理注意的相依性

### <a name="discovery"></a>探索
ADM 建置自動常見參照地圖的相依性您的伺服器，處理程序，以及第 3 廠商服務。  它探索及對應所有的 TCP 相依性，識別意外連線，遠端您而定，第 3 廠商系統與傳統深色的區域，例如 DNS 和 AD 網路的相依性。  ADM 探索失敗的網路連線的受管理的系統正在嘗試進行，協助您找出可能的伺服器設定錯誤、 服務中斷和網路問題。

### <a name="incident-management"></a>事件管理
ADM 可協助避免問題隔離的猜測顯示系統連接的方式，並會影響其他。  除了連線失敗，找出設定錯誤的負載平衡器、 意外或過多負載重要的服務，並劣等用戶端，例如 [開發人員機器交談生產系統，幫助連線的用戶端的相關資訊。  整合式工作流程與 OMS 變更追蹤也可讓您查看是否在後端的電腦上變更事件或服務說明根本原因的事件。

### <a name="migration-assurance"></a>移轉保證
ADM 可讓您規劃、 加速，及驗證 Azure 移轉時，確定不會保留下來，並有沒有意外中斷。  您可以發現需要同時移轉與評估系統設定與容量、 識別執行系統仍提供服務的使用者，或為候選解除委任，而不移轉的所有交互系統。  移動完成之後，您可以檢查在用戶端載入與驗證連線測試系統和客戶的身分識別。  如果您子網路規劃與防火牆定義發生問題，ADM 地圖的連線失敗會指向您需要連線的系統。

### <a name="business-continuity"></a>業務連續性
如果您使用的 Azure 網站復原，且需要定義您的應用程式的環境，ADM 的復原順序的說明功能可以自動顯示如何系統依賴彼此確保修復計劃是可靠的。  選擇要徑伺服器，並檢視它的用戶端，您可以找出應該修復只之後的要徑伺服器還原且可用的前端系統。  相反地，您可以藉由查看重要的伺服器端相依性，識別焦點系統就會還原之前，必須先復原這些系統。

### <a name="patch-management"></a>更新程式管理
ADM 可顯示您的其他小組和伺服器取決於您的服務，因此您可以進行預先之前通知您需要您的系統修補增進 OMS 系統更新評估使用。  ADM 也可增進補充 OMS 中的顯示您的服務是否提供正確的連線會修正的項目，並重新啟動之後。 


## <a name="mapping-overview"></a>對應概觀
ADM 代理程式收集要安裝之位置的伺服器上所有的 TCP 連接處理程序的相關資訊，以及每個程序的傳入和傳出連線的詳細資料。  使用 ADM 解決方案左側的 [電腦] 清單，可以呈現相依性所選的時間範圍內選取 ADM 代理程式的電腦。  電腦相依性將焦點放在特定的電腦上的對應，並顯示所有電腦直接 TCP 用戶端或電腦的伺服器。

![ADM 概觀](media/operations-management-suite-application-dependency-monitor/adm-overview.png)

若要顯示作用中的網路連線與執行的程序期間所選的時間範圍地圖的擴充機器。  ADM 代理程式的遠端電腦展開時顯示程序的詳細資料，則會顯示與焦點電腦通訊這些程序。  在左側的處理程序連線至表示無代理程式的前端電腦連線到焦點電腦的計數。  如果焦點電腦已連線到後端代理程式後, 端表示中的對應，顯示其 IPv4 位址的節點，並顯示個別的連接埠與通訊焦點電腦與服務會展開節點。

根據預設，ADM 地圖會顯示最後 10 分鐘的時間的相依性的資訊。  使用時間控制項左上角，地圖可以查詢的歷史時間範圍，最多到一小時整個，以顯示如何相依性尋找過去，例如在事件過程或之前發生變更。    ADM 資料會儲存在付費的工作區，30 天的和免費的工作區中 7 天。

![選取的電腦屬性與電腦地圖](media/operations-management-suite-application-dependency-monitor/machine-map.png)

## <a name="failed-connections"></a>連線失敗
連線失敗時，會顯示 ADM 地圖的程序與電腦上，使用紅色虛線顯示如果用戶端系統失敗達到程序或連接埠。  如果系統嘗試連線失敗的項目從任何使用部署 ADM 代理程式的系統報告連線失敗。  ADM 測量這觀察建立連線失敗的 TCP 通訊端。  這可能是由於防火牆來說，用戶端或伺服器，或遠端服務，無法使用的設定錯誤。 

![連線失敗](media/operations-management-suite-application-dependency-monitor/failed-connections.png)

了解連線失敗可以協助疑難排解移轉驗證安全性分析與整體架構的瞭解程度。  有時候無法連線，但它們通常指向直接的問題，例如容錯移轉環境突然變得連線，...，無法對談雲端移轉後的.or 兩個應用程式層。  在上方的圖片，IIS 和 WebSphere 同時執行的，但無法連線。 

## <a name="computer-and-process-properties"></a>電腦和程序屬性
瀏覽時 ADM 地圖，您可以選取機器和程序，以取得其內容的相關的其他內容。  機器提供 DNS 名稱、 IPv4 位址、 CPU 和記憶體容量、 VM 類型、 作業系統的版本，最後一個重新啟動時間和 Id，其 OMS 和 ADM 代理程式的相關資訊。

從作業系統中繼資料執行程序，包括程序名稱、 程序描述、 使用者名稱和網域 （Windows)、 公司名稱、 產品名稱、 產品版本、 工作目錄、 命令列中，與程序開始時間收集程序的詳細資料。

![程序屬性](media/operations-management-suite-application-dependency-monitor/process-properties.png)

處理程序摘要] 面板提供該程序的連線，包括其繫結的連接埠、 輸入與輸出連線的其他資訊以及失敗的連線。 

![處理程序摘要](media/operations-management-suite-application-dependency-monitor/process-summary.png)

## <a name="oms-change-tracking-integration"></a>OMS 的追蹤修訂整合
兩種解決方案會啟用及設定 OMS 工作區中時，會自動與追蹤修訂的 ADM 的整合。

電腦摘要面板會指出是否追蹤修訂的事件發生選取電腦上的所選的時間範圍。

![電腦摘要面板](media/operations-management-suite-application-dependency-monitor/machine-summary.png)

電腦變更 [追蹤] 面板會顯示所有修訂]，最新的第一個，以及如需詳細資料的記錄檔搜尋向下切入的連結清單。
![電腦變更追蹤面板](media/operations-management-suite-application-dependency-monitor/machine-change-tracking.png)

下列是向下設定變更事件的檢視選取 [**顯示記錄分析**之後。
![設定變更事件](media/operations-management-suite-application-dependency-monitor/configuration-change-event.png)


## <a name="log-analytics-records"></a>記錄狀況分析
使用[搜尋](../log-analytics/log-analytics-log-searches.md)中記錄分析 ADM 的電腦與程序庫存資料。  這會套用到包括移轉規劃、 容量分析、 探索與臨機操作效能疑難排解的案例。 

每小時的每個唯一的電腦系統會產生一筆記錄，除了記錄的程序時產生程序或電腦啟動，或已在-boarded 至 ADM.  這些記錄會包含下列表格中的內容。 

有內部產生可用來找出獨特的程序和電腦的內容︰

- PersistentKey_s 唯一所定義之程序設定，例如命令列] 和 [使用者識別碼。  都是唯一的特定的電腦，但可以電腦間重複。
- ProcessId_s 和 ComputerId_s 是全域唯一 ADM 模型中。



### <a name="admcomputercl-records"></a>AdmComputer_CL 記錄
記錄類型的**AdmComputer_CL**有伺服器 ADM 代理程式的庫存資料。  這些記錄下表中包含的內容。  

| 屬性 | 描述 |
|:--|:--|
| 類型 | *AdmComputer_CL* |
| SourceSystem | *OpsManager* |
| ComputerName_s | 在 Windows 或 Linux 電腦名稱 |
| CPUSpeed_d | 在 [MHz CPU 速度 |
| DnsNames_s | 此電腦的所有 DNS 名稱的清單 |
| IPv4s_s | 在這台電腦所使用的所有 IPv4 位址的清單 |
| IPv6s_s | 所有的 IPv6 位址，在這台電腦所使用的清單。  （ADM 識別 IPv6 位址的但找不到 IPv6 相依性）。 |
| Is64Bit_b | true 或 false 根據 OS 類型 |
| MachineId_s | 內部 GUID，唯一整個 OMS 區  |
| OperatingSystemFamily_s | 在 Windows 或 Linux |
| OperatingSystemVersion_s | 作業系統版本的長字串 |
| TimeGenerated | 日期及時間的已建立的記錄。 |
| TotalCPUs_d | CPU 核心數目 |
| TotalPhysicalMemory_d | 記憶體容量 (mb) |
| VirtualMachine_b | true 或 false 根據 OS 為 VM 來賓 |
| VirtualMachineID_g | HYPER-V VM 識別碼 |
| VirtualMachineName_g | HYPER-V VM 名稱 |
| VirtualMachineType_s | Hyperv Vmware、 Xen、 Kvm、 Ldom、 Lpar、 Virtualpc |


### <a name="admprocesscl-type-records"></a>AdmProcess_CL 類型的記錄 
記錄類型的**AdmProcess_CL**有 TCP 連接的處理程序的清單資料與 ADM 代理程式的伺服器上。  這些記錄下表中包含的內容。

| 屬性 | 描述 |
|:--|:--|
| 類型 | *AdmProcess_CL* |
| SourceSystem | *OpsManager* |
| CommandLine_s | 程序的完整命令列 |
| CompanyName_s | （從 Windows PE 或 Linux RPM） 的公司名稱 |
| Description_s | （從 Windows PE 或 Linux RPM） 長的程序描述 |
| FileVersion_s | 可執行檔的版本 （從 Windows PE，僅在 Windows) |
| FirstPid_d | OS 處理程序識別碼 |
| InternalName_s | 可執行檔內部名稱 （從 Windows PE，僅在 Windows) |
| MachineId_s | 內部 GUID 唯一整個 OMS 區  |
| Name_s | 程序可執行檔名稱 |
| Path_s | 檔案系統路徑的程序可執行檔 |
| PersistentKey_s | 內部 GUID 唯一這台電腦 |
| PoolId_d | 彙總以類似的命令列的程序的內部 ID。 |
| ProcessId_s | 內部 GUID 唯一整個 OMS 區  |
| ProductName_s | 產品名稱的字串 （從 Windows PE 或 Linux RPM） |
| ProductVersion_s | 產品版本字串 （從 Windows PE 或 Linux RPM） |
| StartTime_t | 在本機電腦時鐘的程序開始時間 |
| TimeGenerated | 日期及時間的已建立的記錄。 |
| UserDomain_s | 網域的程序擁有者 (僅限 Windows) |
| UserName_s | 程序擁有者 (僅限 Windows) 的名稱 |
| WorkingDirectory_s | 程序工作目錄 |


## <a name="sample-log-searches"></a>範例記錄搜尋

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>列出所有受管理的電腦的實體記憶體容量。 
輸入 = AdmComputer_CL |選取 TotalPhysicalMemory_d，ComputerName_s |Dedup ComputerName_s

![ADM 查詢範例](media/operations-management-suite-application-dependency-monitor/adm-example-01.png)

### <a name="list-computer-name-dns-ip-and-os-version"></a>清單的電腦名稱，DNS 與 IP，OS 版本。
輸入 = AdmComputer_CL |選取 ComputerName_s、 OperatingSystemVersion_s、 DnsNames_s IPv4s_s |dedup ComputerName_s

![ADM 查詢範例](media/operations-management-suite-application-dependency-monitor/adm-example-02.png)

### <a name="find-all-processes-with-sql-in-the-command-line"></a>命令列中尋找所有的處理程序，使用 「 sql 」
輸入 = AdmProcess_CL CommandLine_s = \*sql\* |dedup ProcessId_s

![ADM 查詢範例](media/operations-management-suite-application-dependency-monitor/adm-example-03.png)

### <a name="after-viewing-event-data-for-given-process-use-its-machine-id-to-retrieve-the-computers-name"></a>指定檢視事件資料之後處理程序，請使用其電腦識別碼來擷取電腦的名稱
輸入 = AdmComputer_CL 「 m!m-9bb187fa-e522-5f73-66d2-211164dc4e2b 」 |相異 ComputerName_s

![ADM 查詢範例](media/operations-management-suite-application-dependency-monitor/adm-example-04.png)

### <a name="list-all-computers-running-sql"></a>列出所有的電腦執行 SQL
輸入 = AdmComputer_CL MachineId_s 在 {類型 = AdmProcess_CL \*sql\* |相異 MachineId_s} |相異 ComputerName_s

![ADM 查詢範例](media/operations-management-suite-application-dependency-monitor/adm-example-05.png)

### <a name="list-of-all-unique-product-versions-of-curl-in-my-datacenter"></a>在 [我的資料中心捲曲所有唯一的產品版本的清單
輸入 = AdmProcess_CL Name_s = 捲曲 |相異 ProductVersion_s

![ADM 查詢範例](media/operations-management-suite-application-dependency-monitor/adm-example-06.png)

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>建立電腦群組的所有執行 CentOS 的電腦

![ADM 查詢範例](media/operations-management-suite-application-dependency-monitor/adm-example-07.png)



## <a name="diagnostic-and-usage-data"></a>診斷及使用方式的資料
Microsoft 會自動收集您使用的應用程式相依性監視器服務使用情況和效能資料。 Microsoft 會使用這項資料來提供，以及改善品質、 安全性和應用程式相依性監視器服務的完整性。 資料包含您的軟體等作業系統版本設定的相關資訊，也包含 IP 位址、 DNS 名稱，以及工作站名稱，以提供正確且有效的疑難排解功能。 我們不會收集名稱、 地址或其他連絡人的資訊。

如需有關資料收集與使用方式的詳細資訊，請參閱[Microsoft Online Services 隱私權聲明](hhttps://go.microsoft.com/fwlink/?LinkId=512132)。



## <a name="next-steps"></a>後續步驟
- 深入瞭解[登入的搜尋](../log-analytics/log-analytics-log-searches.md] in Log Analytics to retrieve data collected by Application Dependency Monitor.)
