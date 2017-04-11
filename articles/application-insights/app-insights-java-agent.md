<properties 
    pageTitle="監視相依性、 例外狀況和 Java web 應用程式中的執行時間" 
    description="延伸 Java 網站與應用程式的深入見解的監控" 
    services="application-insights" 
    documentationCenter="java"
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/24/2016" 
    ms.author="awills"/>
 
# <a name="monitor-dependencies-exceptions-and-execution-times-in-java-web-apps"></a>監視相依性、 例外狀況和 Java web 應用程式中的執行時間

*應用程式的深入見解位於預覽。*

如果您有[檢測 Java web 應用程式的應用程式的深入見解][java]，您可以使用 Java 代理程式，取得更深入的深入見解，不進行任何的程式碼變更︰


* **相依性︰**相關的其他元件，包括您的應用程式進行通話的資料︰
 * 透過 HttpClient OkHttp，與 RestTemplate （春天） 進行的**其他來電**。
 * 透過 Jedis 用戶端進行**redis**的通話。 如果通話時間長於 10s，代理程式也會擷取通話引數。
 * **[JDBC 通話](http://docs.oracle.com/javase/7/docs/technotes/guides/jdbc/)**-MySQL、 SQL Server、 PostgreSQL、 SQLite、 Oracle 資料庫或 Apache Derby DB。 「 executeBatch 」 支援。 如需 MySQL 與 PostgreSQL，通話時間長於 10s，如果代理程式報表查詢計劃。 
* **攔截例外狀況︰**處理您的程式碼的例外狀況相關的資料。
* **方法執行時間︰**若要執行特定方法的時間相關的資料。

若要使用 Java 代理程式，則安裝在伺服器上。 您的 web 應用程式必須檢測與[應用程式的深入見解 Java SDK][java]。

## <a name="install-the-application-insights-agent-for-java"></a>安裝 java 應用程式的深入見解代理程式

1. 在電腦上執行 Java 伺服器，[下載代理程式](https://aka.ms/aijavasdk)。
2. 編輯應用程式伺服器啟動指令碼，以及新增下列 JVM:

    `javaagent:`*代理程式 JAR 檔案的完整路徑*

    例如，在 Tomcat Linux 電腦上︰

    `export JAVA_OPTS="$JAVA_OPTS -javaagent:<full path to agent JAR file>"`


3. 重新啟動應用程式伺服器。

## <a name="configure-the-agent"></a>設定代理程式

建立一個名為檔案`AI-Agent.xml`並將其放在 [代理程式 JAR 檔案的相同資料夾中。

設定內容的 xml 檔案。 編輯您想要的下列範例包含或省略功能。 

```XML

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsightsAgent>
      <Instrumentation>
        
        <!-- Collect remote dependency data -->
        <BuiltIn enabled="true">
           <!-- Disable Redis or alter threshold call duration above which arguments are sent.
               Defaults: enabled, 10000 ms -->
           <Jedis enabled="true" thresholdInMS="1000"/>
           
           <!-- Set SQL query duration above which query plan is reported (MySQL, PostgreSQL). Default is 10000 ms. -->
           <MaxStatementQueryLimitInMS>1000</MaxStatementQueryLimitInMS>
        </BuiltIn>

        <!-- Collect data about caught exceptions 
             and method execution times -->

        <Class name="com.myCompany.MyClass">
           <Method name="methodOne" 
               reportCaughtExceptions="true"
               reportExecutionTime="true"
               />

           <!-- Report on the particular signature
                void methodTwo(String, int) -->
           <Method name="methodTwo"
              reportExecutionTime="true"
              signature="(Ljava/lang/String;I)V" />
        </Class>
        
      </Instrumentation>
    </ApplicationInsightsAgent>

```

您必須啟用報表的例外狀況] 和 [方法預存時間的個別的方法。

根據預設，`reportExecutionTime`為真，則與`reportCaughtExceptions`為 false。

## <a name="view-the-data"></a>檢視資料

在 [應用程式的深入見解資源彙總遠端相依性和方法的執行時間會出現在[[效能磚][metrics]。 

若要搜尋的相依性、 例外狀況，以及方法報表個別執行個體，請開啟 [[搜尋][diagnostic]。 

[Diagnosing 相依性的問題-瞭解詳細資訊](app-insights-dependencies.md#diagnosis)。



## <a name="questions-problems"></a>問題嗎？ 時遇到問題？

* 沒有資料？ [設定防火牆例外狀況](app-insights-ip-addresses.md)
* [疑難排解 Java](app-insights-java-troubleshoot.md)



<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiexceptions]: app-insights-api-custom-events-metrics.md#track-exception
[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md
[usage]: app-insights-web-track-usage.md

 
