<properties
    pageTitle="分析平台︰ Apache 大量資料流分析比較 |Microsoft Azure"
    description="取得使用串流分析 Apache 大量比較選擇雲端分析平台的指引。 了解功能與相異處。"
    keywords="分析平台、 分析平台、 雲端分析平台大量比較"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="09/26/2016"
    ms.author="jeffstok"/>

# <a name="help-choosing-a-streaming-analytics-platform-apache-storm-comparison-to-azure-stream-analytics"></a>協助選擇串流的分析平台︰ Apache 大量比較 Azure 資料流狀況分析

取得使用 Azure 資料流分析此 Apache 大量比較選擇雲端分析平台的指引。 了解與為受管理的服務上 Azure HDInsight，因此您可以選擇正確的方案，您的商務用 Apache 大量的資料流分析的值，建議使用案例。

同時分析平台提供 PaaS 解決方案的優點，有幾個主要的不同功能的區別它們。 功能，以及這些服務限制下面是可協助您開啟您要達成目標方案。

## <a name="storm-comparison-to-stream-analytics-general-features"></a>大量資料流分析的比較︰ 一般功能 ##
<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Azure 資料流狀況分析</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>在 HDInsight Apache 大量</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>開啟來源</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Azure 資料流分析不是 Microsoft 專屬提供。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
是的 Apache 大量是 Apache 授權技術。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Microsoft 支援</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
[是] </p>
            </td>
            <td width="246" valign="top">
                <p>
[是] </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>硬體需求</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
不有任何硬體需求。 Azure 資料流分析是 Azure 服務。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
不有任何硬體需求。 Apache 大量是 Azure 服務。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>最上層的單位</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
與 Azure 資料流分析客戶部署，並監控串流的工作。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
HDInsight 客戶 Apache 大量部署，並監控整個叢集，可以主控大量的多個工作，以及其他工作負載 （內含批次）。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>價格</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
資料流分析的價格音量處理的資料和數量串流 （每小時工作執行）。
                </p>
                <p>
                    <a href="http://azure.microsoft.com/en-us/pricing/details/stream-analytics/">進一步價格資訊可以在這裡找到。</a>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
為上 HDInsight Apache 大量，購買單位叢集為基礎，而負責根據叢集正在執行，獨立的部署工作的時間。
                </p>
                <p>
                    <a href="http://azure.microsoft.com/en-us/pricing/details/hdinsight/">進一步價格資訊可以在這裡找到。</a>
                </p>
            </td>
        </tr>
    </tbody>
</table>
##在每個分析平台上撰寫##
<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Azure 資料流狀況分析</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>在 HDInsight Apache 大量</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>功能︰ SQL DSL</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
是的方便使用的 SQL 語言支援使用。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
否，使用者必須撰寫程式碼中 Java C#，或使用戟 Api。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>暫時運算子的功能︰</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
預設工作支援視窗彙總數量和暫時連接。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
必須由使用者實作暫時運算子。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>開發體驗</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
互動式撰寫及偵錯體驗透過 Azure 入口網站上的範例資料。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
開發、 偵錯和監視體驗是透過 Visual Studio.NET 使用者︰ 時如需 Java 和其他語言開發人員的體驗可能會使用他們所選擇的 IDE 提供。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>偵錯支援</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
資料流分析提供基本的工作狀態和作業記錄的方式的偵錯，但目前不提供彈性，該怎麼辦/如何充分會包含在記錄中 ie︰ 詳細資訊模式。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
詳細的記錄可供偵錯。 有兩種方法可以運用的記錄檔給使用者，透過 visual Studio 或使用者可以 RDP 到叢集存取記錄。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>UDF 的支援 （使用者定義函數）</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
目前沒有 Udf 支援。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Udf 可以撰寫 C#、 Java 或您選擇的語言。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>可延伸-自訂程式碼</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
有不支援的資料流分析中的可延伸程式碼。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
是的是可用性]，以在大量撰寫自訂程式碼 C#、 Java 或其他支援的語言。
                </p>
            </td>
        </tr>
    </tbody>
</table>
##資料來源與輸出##
<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Azure 資料流狀況分析</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>在 HDInsight Apache 大量</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                 <strong>輸入的資料來源</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>支援的輸入的來源是 Azure 事件集線器和 Azure Blob。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
有可用的事件集線器、 服務匯流排、 Kafka 等的連接器。透過 [自訂程式碼，可能會實作不受支援的連接器。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>輸入資料格式</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
支援的輸入的格式為 Avro、 JSON、 CSV。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
透過 [自訂程式碼，可能會實作任何格式。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>輸出</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
串流工作可能會有多個輸出。 支援的輸出︰ Azure 事件集線器、 Azure Blob 儲存體、 Azure 資料表、 Azure SQL 資料庫及。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
支援拓撲中的許多輸出，每個輸出可能會有下游處理的自訂邏輯。 預設工作大量包括連接器中、 Azure 事件集線器、 Azure Blob 儲存、 Azure DocumentDB、 SQL 和 HBase。 透過 [自訂程式碼，可能會實作不受支援的連接器。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>資料編碼格式</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
資料流分析需要利用 utf-8 資料格式。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
可透過自訂程式碼執行編碼格式的任何資料。
                </p>
            </td>
        </tr>
    </tbody>
</table>
##管理及作業##
<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Azure 資料流狀況分析</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>在 HDInsight Apache 大量</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>工作部署模型</strong>
                </p>
                <p>
                    - <strong>Azure 入口網站</strong>
                </p>
                <p>
                    - <strong>Visual Studio</strong>
                </p>
                <p>
                    - <strong>PowerShell</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
部署是透過 Azure 入口網站與 PowerShell REST Api 實作。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Depolyment 是透過 Azure 入口網站與 PowerShell、 Visual Studio REST Api 實作。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>監控 （統計資料、 計數器）</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
監控是透過 Azure 入口網站和 REST Api 實作。
                </p>
                <p>
使用者也可以設定 Azure 通知。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
監控是透過大量 UI 和 REST Api 實作。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>延展性</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
每個工作串流單位的數目。 每個串流單位處理進位到 1 MB/s。 依預設 50 個單位的最大值。 若要增加限制通話。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
HDI 大量叢集的節點數目。 沒有限制的節點 （上方您 Azure 配額所定義的上限）。 若要增加限制通話。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>資料處理限制</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
使用者可以調整向上或向下串流增加處理資料或最佳化成本的單位數量。
                </p>
                <p>
不按比例縮放最多為 1 GB/s </p>
            </td>
            <td width="246" valign="top">
                <p>
使用者可以調整向上或向下叢集大小以符合需求。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>停止/履歷表</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
停止與繼續從停止的最後一個位置。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
停止與繼續最後一次從放置停止根據浮水印。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>服務與架構的更新</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
自動修正不中斷的情況。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
自動修正不中斷的情況。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>透過高度與保證 SLA 提供服務的業務連續性</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
99.9%的存留時間的 SLA </p>
                <p>
自動復原功能從失敗 </p>
                <p>
設定狀態暫時運算子的復原是內建項目。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
99.9%的大量叢集的存留時間的 SLA。 Apache 大量是故障容錯串流平台，但是是以確保其串流工作執行不受干擾的客戶的責任。
                </p>
            </td>
        </tr>
    </tbody>
</table>
##進階的功能##
<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Azure 資料流狀況分析</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>在 HDInsight Apache 大量</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>最遲抵達和順序事件處理</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
若要重新排列的內建可設定原則放事件，或調整事件的時間。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
使用者必須實作邏輯來處理這種情況。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>參考資料</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
從 Azure Blob 最大的大小為 100 MB 的記憶體內查閱快取可用的參考資料。 參考資料重新整理會管理服務。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
沒有資料的大小限制。 適用於 HBase 與 DocumentDB、 SQL Server Azure 連接器。 透過 [自訂程式碼，可能會實作不受支援的連接器。
                </p>
                <p>
必須由自訂程式碼處理的參考資料重新整理。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>與電腦學習整合</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
設定發佈為函數 Azure 電腦學習模型期間 ASA 工作建立<a href="http://blogs.msdn.com/b/streamanalytics/archive/2015/05/24/real-time-scoring-of-streaming-data-using-machine-learning-models.aspx">（私人預覽版本）</a>。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
透過大量螺栓使用。
                </p>
            </td>
        </tr>
    </tbody>
</table>
