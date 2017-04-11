<properties 
   pageTitle="在記錄檔分析系統郵件 |Microsoft Azure"
   description="系統會是常見 Linux 的事件記錄通訊協定。   本文將說明如何設定系統郵件記錄分析及詳細資料，他們 OMS 存放庫中建立的記錄。"
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
   ms.date="09/06/2016"
   ms.author="bwren" />


# <a name="syslog-data-sources-in-log-analytics"></a>系統記錄分析的資料來源

系統會是常見 Linux 的事件記錄通訊協定。  應用程式會傳送可能會儲存在本機電腦上或傳送給系統行程的訊息。  安裝 Linux OMS 代理程式時，它就會設定本機系統精靈，將郵件轉寄至代理程式。  代理程式傳送郵件給記錄分析 OMS 存放庫中建立相對應的記錄的位置。  

> [AZURE.NOTE]記錄檔分析支援集合 rsyslog 或系統 ng 所傳送的郵件。 版本 5 紅色角色企業 Linux CentOS，與 Oracle Linux 版本 (sysklog) 上的預設系統精靈不支援系統事件集合。 若要從這些散佈此版本收集系統資料，應該安裝和設定取代 sysklog [rsyslog 精靈](http://rsyslog.com)。

![系統集合](media/log-analytics-data-sources-syslog/overview.png)


## <a name="configuring-syslog"></a>設定系統
Linux OMS 代理人只會收集事件的設備，而且在其設定中指定的重要性。  您可以設定系統，透過 OMS 入口網站或管理您的 Linux 代理程式上的設定檔。


### <a name="configure-syslog-in-the-oms-portal"></a>設定系統 OMS 入口網站中

設定系統從[記錄分析設定] 中的 [資料] 功能表](log-analytics-data-sources.md#configuring-data-sources)。  此設定會傳送給每個 Linux 代理程式的設定檔。

您可以新增的新功能輸入其名稱，然後按一下**+**。  每個功能，將會收集只有含所選的重要性的郵件。  核取您要收集的特定功能的重要性。  您無法提供任何其他準則來篩選郵件。

![設定系統](media/log-analytics-data-sources-syslog/configure.png)


根據預設，所有設定變更自動推都入所有代理程式。  如果您想要在每個 Linux 代理程式手動設定系統，然後取消核取 [*套用至我的 Linux 電腦設定] 下方*的方塊。


### <a name="configure-syslog-on-linux-agent"></a>設定系統上 Linux 代理程式

當[Linux 用戶端上已安裝的 OMS 代理程式](log-analytics-linux-agents.md)，請安裝定義的功能和嚴重性收集的郵件預設系統設定檔。  您可以修改此檔案，以變更設定。  設定檔案已安裝的用戶端系統精靈而有所不同。

> [AZURE.NOTE] 如果您編輯系統設定，您必須重新啟動系統精靈，變更才會生效。

#### <a name="rsyslog"></a>rsyslog

Rsyslog 的設定檔是位於**/etc/rsyslog.d/95-omsagent.conf**。  預設內容如下所示。  這會收集系統的郵件來自本機代理程式的層級的警告或更高的所有功能。

    kern.warning       @127.0.0.1:25224
    user.warning       @127.0.0.1:25224
    daemon.warning     @127.0.0.1:25224
    auth.warning       @127.0.0.1:25224
    syslog.warning     @127.0.0.1:25224
    uucp.warning       @127.0.0.1:25224
    authpriv.warning   @127.0.0.1:25224
    ftp.warning        @127.0.0.1:25224
    cron.warning       @127.0.0.1:25224
    local0.warning     @127.0.0.1:25224
    local1.warning     @127.0.0.1:25224
    local2.warning     @127.0.0.1:25224
    local3.warning     @127.0.0.1:25224
    local4.warning     @127.0.0.1:25224
    local5.warning     @127.0.0.1:25224
    local6.warning     @127.0.0.1:25224
    local7.warning     @127.0.0.1:25224

您可以移除設備移除的設定檔 > 一節。  您可以限制藉由使用修改的功能的項目收集的特定功能的重要性。  例如，若要限制郵件使用者設備嚴重錯誤或更高您想修改下列設定檔的線條︰

    user.error  @127.0.0.1:25224


#### <a name="syslog-ng"></a>系統 ng

Rsyslog 的設定檔是在**/etc/syslog-ng/syslog-ng.conf**的位置。  預設內容如下所示。  這會收集所有的功能及所有的重要性傳送來自本機代理程式的系統訊息。   

    #
    # Warnings (except iptables) in one file:
    #
    destination warn { file("/var/log/warn" fsync(yes)); };
    log { source(src); filter(f_warn); destination(warn); };
    
    #OMS_Destination
    destination d_oms { udp("127.0.0.1" port(25224)); };

    #OMS_facility = auth
    filter f_auth_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(auth); };
    log { source(src); filter(f_auth_oms); destination(d_oms); };

    #OMS_facility = authpriv
    filter f_authpriv_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(authpriv); };
    log { source(src); filter(f_authpriv_oms); destination(d_oms); };

    #OMS_facility = cron
    filter f_cron_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(cron); };
    log { source(src); filter(f_cron_oms); destination(d_oms); };

    #OMS_facility = daemon
    filter f_daemon_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(daemon); };
    log { source(src); filter(f_daemon_oms); destination(d_oms); };

    #OMS_facility = kern
    filter f_kern_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(kern); };
    log { source(src); filter(f_kern_oms); destination(d_oms); };
    
    #OMS_facility = local0
    filter f_local0_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(local0); };
    log { source(src); filter(f_local0_oms); destination(d_oms); };
    
    #OMS_facility = local1
    filter f_local1_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(local1); };
    log { source(src); filter(f_local1_oms); destination(d_oms); };
    
    #OMS_facility = mail
    filter f_mail_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(mail); };
    log { source(src); filter(f_mail_oms); destination(d_oms); };
    
    #OMS_facility = syslog
    filter f_syslog_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(syslog); };
    log { source(src); filter(f_syslog_oms); destination(d_oms); };
    
    #OMS_facility = user
    filter f_user_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(user); };
    log { source(src); filter(f_user_oms); destination(d_oms); };

您可以移除設備移除的設定檔 > 一節。  您可以限制藉由將其從清單移除收集的特定功能的重要性。  例如，若要限制只提醒和重要郵件的使用者功能，您想要修改下列設定檔區段︰

    #OMS_facility = user
    filter f_user_oms { level(alert,crit) and facility(user); };
    log { source(src); filter(f_user_oms); destination(d_oms); };


### <a name="changing-the-syslog-port"></a>變更系統連接埠

OMS 代理人接聽連接埠 25224 在本機的用戶端系統訊息。  您可以變更此連接埠位於**/etc/opt/microsoft/omsagent/conf/omsagent.conf**OMS 代理程式的設定檔新增下一節。  **連接埠**項目中的 25224 換成您想要的連接埠號碼。  請注意，您也需要修改傳送郵件給此連接埠的系統精靈的設定檔。

    <source>
      type syslog
      port 25224
      bind 127.0.0.1
      protocol_type udp
      tag oms.syslog
    </source>


## <a name="data-collection"></a>資料收集

OMS 代理人接聽連接埠 25224 在本機的用戶端系統訊息。 設定檔案系統精靈轉寄傳送到此位置他們所收集記錄分析的連接埠的應用程式的系統訊息。


## <a name="syslog-record-properties"></a>系統記錄內容

系統記錄**系統**類型，並在下表中包含的內容。

| 屬性 | 描述 |
|:--|:--|
| 電腦 | 從收集事件的電腦。 |
| 功能 | 定義系統產生該訊息的一部分。 |
| HostIP | 傳送郵件系統的 IP 位址。  |
| 主機名稱 | 傳送郵件系統的名稱。 |
| 嚴重性層級 | 事件的重要性層級。 |
| SyslogMessage | 訊息的文字。 |
| ProcessID | 產生郵件的程序的識別碼。 |
| EventTime | 日期及時間所產生的事件。



## <a name="log-queries-with-syslog-records"></a>系統記錄的記錄查詢

下表提供擷取系統記錄的記錄查詢中不同的範例。

| 查詢 | 描述 |
|:--|:--|
| 輸入 = 系統 | 所有 Syslogs。 |
| 輸入 = 系統嚴重性層級 = 錯誤 | 使用錯誤的嚴重性的所有系統記錄。 |
| 輸入 = 系統 & #124;電腦的量值 count （) | 電腦所計算的系統記錄。 |
| 輸入 = 系統 & #124;測量 count （） 的功能 | 計算的系統記錄的功能。 |

## <a name="next-steps"></a>後續步驟

- 深入了解[記錄搜尋](log-analytics-log-searches.md)，以分析的資料來源與解決方案從收集的資料。 
- 使用[自訂欄位](log-analytics-custom-fields.md)至個別功能變數剖析系統記錄的資料。
- [設定 Linux 代理程式](log-analytics-linux-agents.md)收集其他類型的資料。 
