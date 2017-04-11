<properties 
   pageTitle="記錄檔分析的資料來源 |Microsoft Azure"
   description="資料來源定義與代理程式和其他記錄分析收集連線來源的資料。  本文將說明記錄分析如何使用資料來源，說明如何進行設定的詳細資料，並提供可用的不同資料來源摘要的概念。"
   services="log-analytics"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="log-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/18/2016"
   ms.author="bwren" />

# <a name="data-sources-in-log-analytics"></a>記錄檔分析的資料來源

記錄檔分析收集資料來源的資料連線 OMS 工作區中，並將它儲存 OMS 存放庫。  會從各收集的資料是由您設定的資料來源定義。  OMS 存放庫中的資料會儲存成一組記錄中。  每個資料來源建立有自己的內容集每一種類型的特定類型的記錄。

![登入分析資料收集](./media/log-analytics-data-sources/overview.png)

資料來源會不同於 OMS 解決方案也收集資料來源的連線，並在 OMS 存放庫中建立記錄。  解決方案可以從方案庫新增至您的工作區，通常會提供 OMS 入口網站中的其他分析工具。  

## <a name="summary-of-data-sources"></a>資料來源摘要

下表列出目前可在記錄檔分析的資料來源。  每個有提供為資料來源的詳細資料的另一個文件的連結。

| 資料來源 | 事件類型 | 描述 |
|:--|:--|:--|
| [自訂的記錄](log-analytics-data-sources-custom-logs.md) | \<LogName\>_CL | 包含記錄資訊的 Windows 或 Linux 代理程式上的文字檔案。 |
| [Windows 事件記錄檔](log-analytics-data-sources-windows-events.md) | 事件 | 在 Windows 電腦上，從事件記錄檔收集事件。 |
| [Windows 效能計數器](log-analytics-data-sources-performance-counters.md) | 效能 | 效能計數器收集從 Windows 電腦。 |
| [Linux 效能計數器](log-analytics-data-sources-performance-counters.md) | 效能 | 效能計數器收集從 Linux 電腦。 |
| [IIS 記錄檔](log-analytics-data-sources-iis-logs.md) | W3CIISLog | Internet Information Services 記錄 W3C 格式。 |
| [系統](log-analytics-data-sources-syslog.md) | 系統 | 在 Windows 或 Linux 電腦的系統事件。 |

## <a name="configuring-data-sources"></a>設定資料來源

您在記錄檔分析**設定**中設定資料來源，從 [**資料**] 功能表。  任何設定已傳送給您 OMS 工作區中的所有連線來源。  目前您無法將任何代理程式排除這項設定。

![設定 Windows 事件](./media/log-analytics-data-sources/configure-events.png)

2. OMS 主控台中選取 [**設定**] 方塊。
3. 選取 [**資料**]。
4. 按一下要設定的資料來源。
5. 請詳細資料的上述資料表中每個資料來源的文件的連結，在他們的設定。

## <a name="data-collection"></a>資料收集

資料來源組態將傳送至在幾分鐘內直接連接到 OMS 的代理程式。  指定的資料收集代理程式，而直接傳送到記錄分析在每個資料來源的特定的時間間隔。  請參閱下列詳細資料的每個資料來源的文件。

連線的管理群組中的是系統管理中心的作業管理員 (SCOM) 代理程式，資料來源組態管理套件轉換成數而傳送至 [管理] 群組中每隔 5 分鐘預設。  代理程式下載像其他任何的管理套件，並會收集指定的資料。 根據資料來源的資料將會是傳送給管理伺服器轉記錄狀況分析資料，或代理程式將會傳送給記錄分析資料不透過管理伺服器。 請參閱[資料集合詳細資料，OMS 功能的方案，以及](log-analytics-add-solutions.md#data-collection-details-for-oms-features-and-solutions)如需詳細資訊。  您可以閱讀連接是 SCOM 和 OMS 和修改頻率的詳細資料的設定它在[系統管理中心 Operations Manager 設定整合](log-analytics-om-agents.md)。

## <a name="log-analytics-records"></a>記錄狀況分析

收集記錄分析的所有資料會都儲存於 OMS 存放庫中，為記錄。  藉由使用不同的資料來源收集的記錄會有自己的內容，並由其**類型**] 屬性。  在每一個記錄類型，請參閱針對各個資料來源的文件和解決方案，如需詳細資訊。


## <a name="next-steps"></a>後續步驟

- 深入了解[解決方案](log-analytics-add-solutions.md)，將功能新增至記錄分析及也將 OMS 存放庫收集資料。
- 深入了解[記錄搜尋](log-analytics-log-searches.md)，以分析的資料來源與解決方案從收集的資料。  
- 設定[通知](log-analytics-alerts.md)，主動時通知您從資料來源與解決方案收集的重要資料。
