<properties
    pageTitle="更新管理解決方案中 OMS |Microsoft Azure"
    description="本文是可協助您瞭解如何使用此方案管理您的 Windows 和 Linux 電腦上的更新。"
    services="operations-management-suite"
    documentationCenter=""
    authors="MGoedtel"
    manager="jwhit"
    editor=""
    />
<tags
    ms.service="operations-management-suite"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/14/2016"
    ms.author="magoedte"/>

# <a name="update-management-solution-in-omsmediaoms-solution-update-managementupdate-management-solution-iconpng-update-management-solution-in-oms"></a>![更新 OMS 中的管理解決方案](./media/oms-solution-update-management/update-management-solution-icon.png) 更新 OMS 中的管理解決方案

更新管理解決方案中 OMS 可讓您管理您的 Windows 和 Linux 電腦上的更新。  您可以快速評估代理程式的所有電腦上可用的更新的狀態，並啟動安裝必要的更新的伺服器的程序。 

## <a name="prerequisites"></a>必要條件

-   必須與 Windows Server 更新 > 伺服器進行通訊，或有權存取 Microsoft Update 設定 Windows 代理程式。  

    >[AZURE.NOTE] Windows 代理程式無法管理同時系統管理中心設定管理員。  
  
-   更新存放庫 Linux 代理程式必須有權存取。  Linux OMS 代理人可以從[GitHub](https://github.com/microsoft/oms-agent-for-linux)下載。 

## <a name="configuration"></a>設定

執行下列步驟，以新增至您 OMS 工作區的 [更新管理方案，並新增 Linux 代理程式。  Windows 代理程式會自動加入的任何其他設定。

1.  新增到使用中[新增 OMS 解決方案](../log-analytics/log-analytics-add-solutions.md)從方案庫所述的程序 OMS 工作區更新管理解決方案。  
2.  在 OMS 入口網站中，選取 [**設定**]，然後按一下 [**連線的來源**。  請注意**工作區識別碼**以及**主索引鍵**或**第二個機碼**。
3.  為每個 Linux 電腦，請執行下列步驟。

    。  藉由執行下列命令 Linux 安裝最新版的 OMS 代理程式。  取代<Workspace ID>的工作區識別碼和<Key>與主要或次要鍵。

        cd ~
        wget https://github.com/Microsoft/OMS-Agent-for-Linux/releases/download/v1.2.0-75/omsagent-1.2.0-75.universal.x64.sh
        sudo bash omsagent-1.2.0-75.universal.x64.sh --upgrade -w <Workspace ID> -s <Key>

     b。 若要移除代理程式，請執行下列命令。

        sudo bash omsagent-1.2.0-75.universal.x64.sh --purge

## <a name="management-packs"></a>管理組件

如果您的系統管理中心 Operations Manager 管理群組連線到您 OMS 工作區，然後下列管理組件將會安裝 Operations Manager 中時新增此方案。 沒有任何設定或的必要這些管理組件進行的維修作業。 

-   Microsoft 系統管理中心顧問更新評估智慧套件 (Microsoft.IntelligencePacks.UpdateAssessment)
-   Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
-   更新部署 MP

如需有關解決方案管理套件的更新方式的詳細資訊，請參閱[連線至記錄分析的 Operations Manager](../log-analytics/log-analytics-om-agents.md)。

## <a name="data-collection"></a>資料收集

### <a name="supported-agents"></a>支援的代理程式

下表說明此方案所支援的連線的來源。

連線的來源 | 支援 | 描述|
----------|----------|----------|
Windows 代理程式 | [是] | 解決方案會從 Windows 代理程式的 [系統更新的相關資訊，並啟動安裝必要的更新。|
Linux 代理程式 | [是] | 方案會從 Linux 代理程式的系統更新的相關資訊。|
作業管理員管理群組 | [是] | 方案會從連線的管理群組中的代理程式的系統更新的相關資訊。<br>不需要直接從 Operations Manager 代理程式連線到記錄分析。 資料是從管理群組轉寄至 OMS 存放庫。|
Azure 儲存體帳戶 | 無 | Azure 儲存不包含系統更新的相關資訊。|  

### <a name="collection-frequency"></a>集合頻率

針對每個受管理的 Windows 電腦，掃描會執行兩次每日。  安裝更新後，會將其資訊更新 15 分鐘內。  

每個 managed Linux 電腦中，執行每 3 小時掃描。  

## <a name="using-the-solution"></a>使用解決方案

當您更新管理解決方案新增到 OMS 工作區時，[**更新管理**] 方塊便會新增至您 OMS 儀表板。 此圖磚會顯示目前需要系統更新您的環境中的計數與電腦數目的圖形表示。<br><br>
![更新管理摘要磚](media/oms-solution-update-management/update-management-summary-tile.png)  

## <a name="viewing-update-assessments"></a>檢視更新評估

按一下 [**更新管理**] 方塊，以開啟 [**更新管理**儀表板上。 儀表板包含下列表格中的欄。 每個資料行列出符合指定的範圍和時間範圍內的資料行的條件最多 10 個項目。 您可以執行記錄搜尋傳回的所有記錄，即可**查看所有**資料行的底部，或按一下資料行標頭。

資料行 | 描述|
----------|----------|
**遺漏更新的電腦** ||
要徑] 或 [安全性更新 | 清單的頂端的十個電腦的遺失更新依據更新數目就會遺失。 按一下 [電腦名稱來執行記錄搜尋傳回所有更新記錄該電腦上。|
要徑或大於 30 天的安全性更新| 識別數量的電腦的遺失的要徑或分組的時間長度，因為更新已發佈的安全性更新。 按一下其中一個項目執行記錄搜尋傳回所有遺失和重要更新。|
**必要遺失的更新**||
要徑] 或 [安全性更新 | 列出分類的更新電腦的遺失電腦遺漏的更新，在類別中的數字排序。 按一下 [執行傳回所有更新記錄的分類記錄搜尋分類]。|
**更新部署**||
更新部署 | 目前排程的更新部署及直到下一個排程執行期間的數目。  按一下磚來檢視排程，目前正在執行，並完成的更新或排程新的部署。|  
<br>  
![更新管理摘要儀表板](./media/oms-solution-update-management/update-management-deployment-dashboard.png)<br>  
<br>
![更新管理儀表板電腦檢視](./media/oms-solution-update-management/update-management-assessment-computer-view.png)<br>  
<br>
![更新管理儀表板套件檢視](./media/oms-solution-update-management/update-management-assessment-package-view.png)<br>  

## <a name="installing-updates"></a>安裝更新

後更新已評估過的所有您的環境中的 Windows 電腦，您可以需要建立*更新部署*安裝的更新。  部署更新是必要的更新，一或多個 Windows 電腦上的排程的安裝。  您指定的日期和時間的部署，除了在電腦或電腦應該包括在內的群組。  

由中 Azure 自動化 runbooks 安裝的更新。  您目前無法檢視這些 runbooks，及他們不需要任何設定。  建立更新部署時，它會建立排程中的啟動主更新 runbook 包含電腦上指定的時間。  此主 runbook 上執行安裝必要的更新的每個 Windows 代理程式啟動子 runbook。  

### <a name="viewing-update-deployments"></a>檢視更新部署

按一下 [**更新部署**磚，若要檢視的現有更新部署清單。  他們的狀態分組 –**排程**、**執行**和**已完成]**。<br><br> ![更新部署排程] 頁面](./media/oms-solution-update-management/update-updatedeployment-schedule-page.png)<br>  

下表說明顯示每個更新部署的屬性。

屬性 | 描述|
----------|----------|
名稱 | 更新部署的名稱。|
排程 | 排程類型。  *OneTime*目前是唯一可能的值。|
開始時間|日期及更新部署排定開始的時間。|
持續時間 | 更新部署可以執行的分鐘數。  如果未安裝此期間內的所有更新，剩餘的更新必須等到下一個更新部署。|
伺服器 | 受更新部署電腦數目。|
狀態 | 更新部署的目前狀態。<br><br>可能的值為︰<br>-尚未啟動<br>執行<br>-完成|  

按一下 [更新部署，檢視包含下列表格中的資料行的詳細資料畫面。  如果尚未啟動更新部署，不會填入這些資料行。<br>

資料行 | 描述|
----------|----------|
**電腦結果**||
已順利完成 | 依狀態清單更新部署內的電腦數目。  按一下 [狀態] 可執行傳回所有更新記錄的狀態更新的部署記錄搜尋。|
電腦安裝狀態| 列出的電腦參與更新部署] 和 [已成功安裝的更新的百分比的。 按一下其中一個項目執行記錄搜尋傳回所有遺失和重要更新。|
**更新執行個體的結果**||
執行個體安裝狀態 | 列出分類的更新電腦的遺失電腦遺漏的更新，在類別中的數字排序。 按一下 [執行記錄搜尋所有更新記錄該電腦所傳回的電腦]。|  
<br><br> ![更新部署結果的概觀](./media/oms-solution-update-management/update-la-updaterunresults-page.png)

### <a name="creating-an-update-deployment"></a>建立部署更新

按一下 [開啟**新的更新部署**] 頁面的螢幕頂端的 [**新增**] 按鈕，以建立新的更新部署。  您必須提供下列表格中的屬性值。

屬性 | 描述|
----------|----------|
名稱 | 若要識別更新部署的唯一名稱。|
時區 | 若要使用的開始時間的時區。|
開始時間 | 日期及時間開始更新部署。|
持續時間 | 更新部署可以執行的分鐘數。  如果未安裝此期間內的所有更新，剩餘的更新必須等到下一個更新部署。|
電腦 | 電腦或包含在更新部署電腦群組的名稱。  從下拉式清單中選取一或多個項目。|
<br><br> ![新的更新部署頁面](./media/oms-solution-update-management/update-newupdaterun-page.png)

### <a name="time-range"></a>時間範圍

根據預設，更新管理方案中分析資料的範圍是從產生最後 1 天內的所有連線的管理群組。 

若要變更資料的時間範圍，請選取儀表板頂端的 [**基礎的資料**。 您可以選取 [建立或更新過去 7 天，1 天或 6 小時內的記錄。 或者，您可以指定自訂日期範圍、 選取**自訂**。<br><br> ![自訂的時間範圍] 選項](./media/oms-solution-update-management/update-la-time-range-scope-databasedon.png)  

## <a name="log-analytics-records"></a>記錄狀況分析

更新管理解決方案 OMS 存放庫中建立兩種類型的記錄。

### <a name="update-records"></a>更新記錄

安裝或需要在每一部電腦上的每個更新是自動建立的記錄類型的**更新**。 更新記錄下表中包含的內容。

屬性 | 描述|
----------|----------|
類型 | *更新*|
SourceSystem | 核准安裝更新的來源。<br>可能的值為︰<br>Microsoft Update<br>Windows 更新<br>-SCCM<br>-（從套件管理員讀取） Linux 伺服器|
[已核准] | 指定是否已安裝的核准的更新。<br> 針對 Linux 伺服器這是目前為選用修補程式不由管理 OMS。|
在 Windows 版的分類 | 更新的分類。<br>可能的值為︰<br>應用程式<br>重要任務更新<br>定義更新<br>功能套件<br>安全性更新<br>服務套件<br>更新彙總套件<br>-更新|
Linux 分類 | Cassification 的更新。<br>可能的值為︰<br>重要任務更新<br>安全性更新<br>-其他更新|
電腦 | 電腦的名稱。|
InstallTimeAvailable | 指定是否安裝時提供其他代理程式的安裝相同的更新。|
InstallTimePredictionSeconds | 根據其他安裝相同的更新的代理程式的秒數的估計的安裝時間。|
編號 | 說明更新的知識庫文件的識別碼。|
ManagementGroupName | 是 SCOM 代理程式的 [管理] 群組的名稱。  其他代理程式 」，這是 AOI-<workspace ID>。|
MSRCBulletinID | 描述更新 Microsoft 安全性公告的識別碼。|
MSRCSeverity | Microsoft 安全性公告的嚴重性。<br>可能的值為︰<br>重要任務<br>重要<br>-中等|
選擇性 | 指定是否為選用的更新。|
產品 | 更新版的產品的名稱。  按一下 [**檢視**]，在瀏覽器中開啟文件]。|
PackageSeverity | 固定在此更新，報告 Linux distro 廠商弱點的嚴重性。 | 
PublishDate | 日期及時間的已安裝更新。|
RebootBehavior | 指定更新是否強制重新啟動電腦。<br>可能的值為︰<br>-canrequestreboot<br>-neverreboots|
RevisionNumber | 修訂版編號的更新。|
SourceComputerId | GUID，可唯一識別的電腦。|
TimeGenerated | 日期及時間上次更新記錄。|
標題 | 更新的標題。|
UpdateID | GUID 識別更新。|
UpdateState | 指定是否在這台電腦上安裝更新。<br>可能的值為︰<br>安裝-在這台電腦上安裝更新。<br>需要-更新未安裝，而且需要這台電腦上。|  

<br>
當您執行任何記錄搜尋，傳回記錄類型的**更新**時，您可以選取 [**更新**] 檢視會顯示一組並排摘要搜尋傳回的更新。 您可以按一下**遺失與套用的更新**中的項目與磚來設定範圍的更新該設定檢視的**必要與選用的更新**。 選取要傳回的個別記錄的**清單**或**表格**的檢視。<br> 

![記錄類型的更新後使用記錄搜尋更新檢視](./media/oms-solution-update-management/update-la-view-updates.png)  

在**表格**檢視中，您可以按一下**編號**任何記錄以在瀏覽器開啟篇知識庫文章。 這個選項可讓您快速瞭解特定更新的詳細資料。<br> 

![並排記錄類型的更新的記錄檔搜尋表格檢視](./media/oms-solution-update-management/update-la-view-table.png)

在 [**清單**] 檢視中，您可以按一下以開啟篇知識庫文章編號] 旁的 [**檢視**] 連結。<br>

![並排記錄類型的更新的記錄檔搜尋清單檢視](./media/oms-solution-update-management/update-la-view-list.png)


###<a name="updatesummary-records"></a>UpdateSummary 記錄

為每個 Windows 代理程式電腦建立含**UpdateSummary**類型的記錄。 掃描電腦每次更新時，會更新這筆記錄。 **UpdateSummary**記錄下表中包含的內容。

屬性 | 描述|
----------|----------|
類型 | UpdateSummary|
SourceSystem | OpsManager |
電腦 | 電腦的名稱。|
CriticalUpdatesMissing | 遺漏的電腦上的重大更新的數目。|
ManagementGroupName | 是 SCOM 代理程式的 [管理] 群組的名稱。 其他代理程式 」，這是 AOI-<workspace ID>。|
NETRuntimeVersion | 在電腦上安裝的.NET 執行階段的版本。|
OldestMissingSecurityUpdateBucket | 發佈分類的時間，因為從最舊遺失的安全性更新這台電腦上的連結。<br>可能的值為︰<br>-舊版<br>-180 天之內<br>-150 天之內<br>-120 天之內<br>-90 天之內<br>-60 天之內<br>至移 30 天<br>-最近|
OldestMissingSecurityUpdateInDays | 發佈天數從最舊遺失的安全性更新這台電腦上。|
OsVersion | 作業系統的電腦上安裝的版本。|
OtherUpdatesMissing | 遺漏的電腦上的其他更新數目。|
SecurityUpdatesMissing | 遺漏的電腦上的安全性更新數目。|
SourceComputerId | GUID，可唯一識別的電腦。|
TimeGenerated | 日期及時間上次更新記錄。|
TotalUpdatesMissing |遺漏的電腦上的更新總數。|
WindowsUpdateAgentVersion | 在電腦上的 Windows Update 代理程式的版本號碼。|
WindowsUpdateSetting | 如何安裝在電腦的重要更新] 設定。<br>可能的值為︰<br>-停用<br>-在安裝前通知<br>-已排程的安裝|
WSUSServer | 如果電腦設定為使用其中一個的 URL 的 WSUS 伺服器。|  

## <a name="sample-log-searches"></a>範例記錄搜尋

下表提供範例記錄搜尋此方案所收集到的更新記錄。 

查詢 | 描述|
----------|----------|
所有電腦的遺失的更新 | 輸入 = 更新 UpdateState = 需要選擇性 = false & #124;選取電腦、 標題、 編號、 分類、 UpdateSeverity、 PublishedDate|
遺漏更新電腦 「 COMPUTER01.contoso.com 」 （取代為您自己的電腦名稱） | 輸入 = 更新 UpdateState = 需要選擇性 = false 電腦 = 」 COMPUTER01.contoso.com 「 & #124;選取電腦、 標題、 編號、 產品、 UpdateSeverity、 PublishedDate|
所有的電腦，遺失要徑或安全性更新 | 輸入 = 更新 UpdateState = 需要選擇性 = false (分類 = 」 安全性更新 」 或分類 = 「 重大更新 」)|
所需的手動套用更新電腦的要徑或安全性更新 | 類型 = 更新 UpdateState = 需要選擇性 = false (分類 = 」 安全性更新 」 或分類 = 「 重大更新 」) 的電腦在 {類型 = UpdateSummary WindowsUpdateSetting = 手動 & #124;不同的電腦} & #124;不同的編號|
有遺失的要徑的電腦或安全性的錯誤事件所需的更新 | 類型 = 事件 EventLevelName = 錯誤的電腦在 {類型 = 更新 (分類 = 」 安全性更新 」 或分類 = 「 重大更新 」) UpdateState = 需要選擇性 = false & #124;不同的電腦}|
遺失更新彙總套件的所有電腦 | 輸入 = 更新選擇性 = false 分類 = 」 更新彙總套件 」 UpdateState = 需要 & #124;選取電腦、 標題、 編號、 分類、 UpdateSeverity、 PublishedDate|
所有電腦之間的不同遺失更新 | 輸入 = 更新 UpdateState = 需要選擇性 = false & #124;不同的標題|
WSUS 電腦成員資格 | 輸入 = UpdateSummary & #124;以 WSUSServer 量值 count （)|
自動更新設定 | 輸入 = UpdateSummary & #124;以 WindowsUpdateSetting 量值 count （)|
停用自動更新的電腦 | 輸入 = UpdateSummary WindowsUpdateSetting = 手動|  
有可用的套件更新的所有 Linux 電腦的清單 | 輸入 = 更新與 OSType = Linux 和 UpdateState ！ = 」 不需要 「 & #124;電腦的量值 count （)|
地址的要徑或安全性漏洞，必須提供的套件更新的所有 Linux 電腦的清單 | 類型 = 更新與 OSType = Linux 和 UpdateState ！ = 「 不需要 」 及 (分類 = 「 重大更新 」 或分類 = 」 安全性更新 」) & #124;電腦的量值 count （)|
有可用的更新的所有套件的清單 | 輸入 = 更新與 OSType = Linux 和 UpdateState ！ = 「 不需要 」|
要徑或安全性的弱點 「 地址有可用的更新的所有套件的清單 | 類型 = 更新與 OSType = Linux 和 UpdateState ！ = 「 不需要 」 及 (分類 = 「 重大更新 」 或分類 = 」 安全性更新 」)|
使用任何可用的更新的所有 「 Ubuntu 」 電腦的清單 | 輸入 = 更新與 OSType = Linux 和 OSName = Ubuntu & #124;電腦的量值 count （)|

## <a name="next-steps"></a>後續步驟

- 使用[記錄分析](../log-analytics/log-analytics-log-searches.md)中的記錄檔的搜尋，來檢視詳細的更新資料。

- [建立您自己的儀表板](../log-analytics/log-analytics-dashboards.md)顯示更新法規遵循的受管理的電腦。

- [建立提醒](../log-analytics/log-analytics-alerts.md)的重大更新偵測到為從電腦或電腦的遺失時已停用的自動更新。  




