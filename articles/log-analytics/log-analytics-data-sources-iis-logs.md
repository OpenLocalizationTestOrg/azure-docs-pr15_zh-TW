<properties
   pageTitle="IIS 登入記錄檔分析 |Microsoft Azure"
   description="網際網路服務 (IIS) 儲存記錄檔，可以藉由使用記錄分析收集使用者活動。  本文將說明如何設定 IIS 記錄的集合及他們 OMS 存放庫中建立的記錄的詳細資料。"
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

# <a name="iis-logs-in-log-analytics"></a>IIS 登入記錄檔狀況分析
網際網路服務 (IIS) 儲存記錄檔，可以藉由使用記錄分析收集使用者活動。  

![IIS 記錄檔](media/log-analytics-data-sources-iis-logs/overview.png)

## <a name="configuring-iis-logs"></a>設定 IIS 記錄
記錄檔分析收集 IIS，所以您必須[設定 IIS 記錄](https://technet.microsoft.com/library/hh831775.aspx)所建立的記錄檔中的項目。

記錄檔分析只支援 IIS 記錄檔 W3C 格式儲存，而且不支援自訂欄位] 或 [IIS 進階記錄。  
記錄檔分析並不會收集 NCSA 或 IIS 原生格式的記錄。

設定記錄分析，從[[資料] 功能表中記錄分析設定](log-analytics-data-sources.md#configuring-data-sources)IIS 記錄檔。  有不需要的設定不是選取 [**收集 W3C 格式 IIS 記錄檔**。

我們建議您，當您啟用 IIS 記錄集合時，您應該設定 IIS 記錄變換設定每個伺服器上。


## <a name="data-collection"></a>資料收集

記錄檔分析收集 IIS 記錄項目從每個連線的來源約每 15 分鐘。  代理程式就會從每個事件記錄檔中記錄的位置。  如果代理程式離線，然後記錄分析收集事件從上次停止的地方，即使代理程式離線時，所建立的事件。


## <a name="iis-log-record-properties"></a>IIS 記錄記錄內容

IIS 記錄**W3CIISLog**類型，並在下表中包含的內容︰

| 屬性 | 描述 |
|:--|:--|
| 電腦 | 事件收集從電腦的名稱。 |
| cIP | 用戶端 IP 位址。 |
| csMethod | 取得或文章等要求的方法。 |
| csReferer | 網站使用者追蹤從目前網站的連結。 |
| csUserAgent | 在瀏覽器的用戶端類型。 |
| csUserName | 已驗證的使用者存取伺服器的名稱。 匿名使用者被以連字號。 |
| csUriStem | 要求，例如網頁的目標。 |
| csUriQuery | 如果有的話，，查詢用戶端已嘗試執行。 |
| ManagementGroupName | Operations Manager 代理程式的 [管理] 群組的名稱。  其他代理程式 」，這是 AOI-\<工作區識別碼\> |
| RemoteIPCountry | 用戶端 IP 位址的國家/地區。 |
| RemoteIPLatitude | 緯度的用戶端 IP 位址。 |
| RemoteIPLongitude | 經度的用戶端 IP 位址。 |
| scStatus | HTTP 狀態碼。 |
| scSubStatus | 子狀態，出現錯誤碼。 |
| scWin32Status | Windows 狀態碼。 |
| sIP | 網頁伺服器 IP 位址。 |
| SourceSystem  | OpsMgr |
| 運動項目 | 在用戶端連線至伺服器的連接埠。 |
| sSiteName | IIS 網站的名稱。 |
| TimeGenerated | 日期及時間記錄項目。 |
| TimeTaken | 若要處理此要求以毫秒為單位的時間長度。 |

## <a name="log-searches-with-iis-logs"></a>使用 IIS 記錄的記錄檔搜尋

下表提供擷取 IIS 記錄的記錄查詢中不同的範例。

| 查詢 | 描述 |
|:--|:--|
| 輸入 = IISLog | 所有 IIS 登都入記錄。 |
| 輸入 = IISLog EventLevelName = 錯誤 | 所有的 Windows 事件與錯誤嚴重性。 |
| 輸入 = W3CIISLog & #124;測量 cIP count （） | 計算的 IIS 記錄項目用戶端 IP 位址。 |
| 輸入 = W3CIISLog csHost = 」 www.contoso.com 「 & #124;測量 csUriStem count （） | 計算的 IIS 的記錄項目 url 主機 www.contoso.com。 |
| 輸入 = W3CIISLog & #124;測量 Sum(csBytes) 的電腦與 #124;頂端 500000| 每個 IIS 電腦所收到的總位元組數。 |

## <a name="next-steps"></a>後續步驟

- 設定記錄分析，收集分析的其他[資料來源](log-analytics-data-sources.md)。
- 深入了解[記錄搜尋](log-analytics-log-searches.md)，以分析的資料來源與解決方案從收集的資料。
- 設定記錄分析主動時通知您在 IIS 記錄檔中找到的重要條件的通知。
