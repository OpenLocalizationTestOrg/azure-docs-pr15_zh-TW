<properties
 pageTitle="了解並在 HDInsight WebHCat 錯誤的解決方法"
 description="瞭解如何以瞭解常見錯誤所傳回的 WebHCat 上 HDInsight，以及如何解決方法。"
 services="hdinsight"
 documentationCenter=""
 authors="Blackmist"
 manager="jhubbard"
 editor="cgronlun"
 tags="azure-portal"/>

<tags
 ms.service="hdinsight"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="big-data"
 ms.date="09/27/2016"
 ms.author="larryfr"/>

#<a name="understand-and-resolve-errors-received-from-webhcat-templeton-on-hdinsight"></a>了解並從 WebHCat (Templeton，) 收到錯誤的解決方法在 HDInsight

使用 HDInsight，使用 WebHCat （先前稱為 Templeton，） 時，您可能會收到錯誤。 這份文件提供的指導方針常見錯誤-發生原因，以及您可以解決方法中執行的動作。

##<a name="what-is-webhcat"></a>什麼是 WebHCat？

[WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat)是 REST API Hadoop 的[HCatalog](https://cwiki.apache.org/confluence/display/Hive/HCatalog)、 表格和儲存空間管理圖層。 WebHCat 上 HDInsight 叢集，預設會啟用，並提交工作，而不必登入叢集取得工作狀態等各種工具會使用。

##<a name="modifying-configuration"></a>修改設定

> [AZURE.IMPORTANT] 數個列在這份文件中的錯誤發生，因為已超過設定的最大值。 時的解決步驟提及，您可以變更的值，您必須執行變更使用下列其中一項︰

* 若為**Windows**叢集︰ 將值設定叢集建立期間使用指令碼的巨集指令。 如需詳細資訊，請參閱[開發指令碼動作](hdinsight-hadoop-script-actions.md)。

* **Linux**叢集︰ 使用 Ambari （網頁或 REST API） 修改值。 如需詳細資訊，請參閱[管理 HDInsight 使用 Ambari](hdinsight-hadoop-manage-ambari.md)

###<a name="default-configuration"></a>預設設定

以下是可以影響 WebHCat 效能，或如果超過這些值，會導致錯誤的預設設定值︰

| 設定 | 功能 | 預設值 |
| ------- | ------------ | ------------- |
| [yarn.scheduler.capacity.maximum 應用程式][maximum-applications] | 可同時作用中的工作的最大的數字 （擱置或執行） | 10000 |
| [templeton.exec.max 程序][max-procs] | 可以同時提供服務的要求數目上限 | 20 |
| [ms-年齡 mapreduce.jobhistory.max-][max-age-ms] | 將保留工作歷程記錄的天數 | 7 天 |

##<a name="too-many-requests"></a>太多要求

**HTTP 狀態碼**︰ 429

| 原因 | 解決方法 |
| ----- | ---------- |
| 您已超過最大值的一個要求由 WebHCat 每分鐘 （預設值 20） | 您的工作量，以確保，您不會送出多個同時的要求的數目上限百分比減少或增加同時的要求限制，可以修改`templeton.exec.max-procs`。 如需詳細資訊，請參閱[修改設定](#modifying-configuration) |

##<a name="server-unavailable"></a>伺服器無法使用

**HTTP 狀態碼**︰ 503

| 原因 | 解決方法 |
| ---------------- | ------------------- |
| 這通常是在叢集主要和次要 HeadNode 之間的容錯移轉 | 請等候兩分鐘，然後再試一 |

##<a name="bad-request-content-could-not-find-job"></a>錯誤的要求，內容︰ 找不到工作

**HTTP 狀態碼**︰ 400

| 原因 | 解決方法 |
| ---------------- | ------------------- |
| 工作詳細資料已經清除工作歷程記錄的清潔器 | 工作歷程記錄的預設保留期限是 7 天。 這可以變更修改`mapreduce.jobhistory.max-age-ms`。 如需詳細資訊，請參閱[修改設定](#modifying-configuration) |
| 由於容錯移轉已經刪除工作 | 再試一次工作送出兩分鐘 |
| 使用不正確的工作識別碼 | 檢查是否正確的作業識別碼 |

##<a name="bad-gateway"></a>不正確的閘道

**HTTP 狀態碼**︰ 502

| 原因 | 解決方法 |
| ---------------- | ------------------- |
| 發生內部回收在 WebHCat 程序 | 等到完成，或重新啟動 WebHCat 服務回收 |
| 從 ResourceManager 服務回應等候逾時。 這會使用中應用程式數目進入設定的最大值 （預設值 10000） | 等待目前執行的工作，以完成，或增加一個工作限制修改`yarn.scheduler.capacity.maximum-applications`。 如需詳細資訊，請參閱[修改設定](#modifying-configuration)  |
| 當您嘗試擷取透過[取得 /jobs](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference+Jobs)來電時的所有工作時`Fields`設定為`*` | 執行擷取*所有*工作詳細資料，請改為使用`jobid`擷取工作只大於特定工作識別碼的詳細資料。 或者，不要使用`Fields` |
| 在 HeadNode 容錯移轉 WebHCat 服務已關閉 | 等待兩分鐘，然後再試 |
| 有多個 500 透過 WebHCat 送出的擱置中工作 | 等待目前擱置中的工作完成前送出更多作業 |

[maximum-applications]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.3/bk_system-admin-guide/content/setting_application_limits.html
[max-procs]: https://hive.apache.org/javadocs/hcat-r0.5.0/configuration.html
[max-age-ms]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.6.0/ds_Hadoop/hadoop-mapreduce-client/hadoop-mapreduce-client-core/mapred-default.xml
 
