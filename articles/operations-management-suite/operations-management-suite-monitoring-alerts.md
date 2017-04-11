<properties 
   pageTitle="通知中的 「 監控產品管理 |Microsoft Azure"
   description="通知，指出需要系統管理員的注意的一些問題。  本文說明如何建立及管理是系統管理中心的作業管理員 (SCOM) 和記錄分析通知的差異，並提供充分使用混合式提醒管理策略的兩個產品的最佳作法。" 
   services="operations-management-suite"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/06/2016"
   ms.author="bwren" />

# <a name="managing-alerts-with-microsoft-monitoring"></a>管理與 Microsoft 監控的通知 

通知，指出需要系統管理員的注意的一些問題。  有不同和之間的差異是系統管理中心的作業管理員 (SCOM) 記錄分析作業管理套件 (OMS) 如何建立通知、 如何他們管理和分析，及如何通知您已偵測到的要徑的問題。

## <a name="alerts-in-operations-manager"></a>Operations Manager 的警示
以個別規則或監視器，表示特定問題，就會產生中是 SCOM 警示。  監視器規則可能會產生通知，表示與管理物件的健康情況不直接相關某些重要問題時，它會進入錯誤狀態時，可以產生提醒。  管理套件包含各種不同的工作流程所建立的應用程式或服務所管理的提醒。  設定新的管理套件的程序的一部分調整，以確保您沒有收到太多通知您不認為重要的問題。

![是 SCOM 通知檢視](media/operations-management-suite-monitoring-alerts/scom-alert-view.png)

是 SCOM 提供完成的通知管理有這些工作以解決此問題時可以變更由系統管理員工作狀態的警示。  當問題已解決時，系統管理員設定關閉提醒的時間，它不會再出現在檢視中顯示 [作用中的通知。  監視器傳回健全狀態時，就可以自動解決通知所產生的監視器。

![通知的狀態](media/operations-management-suite-monitoring-alerts/scom-alert-status.png)

## <a name="alerts-in-log-analytics"></a>記錄檔分析的警示
定期自動執行的記錄查詢從建立相關記錄分析的通知。  您可以從任何記錄查詢建立提醒的規則。  如果查詢傳回的結果符合您指定的準則，則會建立提醒。  這可能是如果偵測到特定事件時，就會建立提醒的特定查詢，或者您可以使用多個一般查詢來尋找特定應用程式相關的任何錯誤事件。

記錄檔分析通知會寫入到為事件 OMS 存放庫，並且可以使用記錄查詢擷取。  沒有等是 SCOM 事件狀態，好讓您可以指示解決問題時。

![OMS 通知](media/operations-management-suite-monitoring-alerts/oms-alert.png)

是 SCOM 記錄分析使用做為資料來源時，是 SCOM 通知會寫入到 OMS 存放庫中，為應用程式建立和修改。  

![是 SCOM 通知](media/operations-management-suite-monitoring-alerts/scom-alert.png)

[通知管理解決方案](http://technet.microsoft.com/library/mt484092.aspx)提供作用中的通知的摘要，並以擷取不同的幾個常見查詢設定的提醒。  可讓您更有效率分析報表的是 SCOM 比您的通知。  您可以向下切入摘要的詳細資料，並建立臨機查詢來擷取組不同的通知。

![通知管理解決方案](media/operations-management-suite-monitoring-alerts/alert-management.png)

## <a name="notifications"></a>通知
通知中是 SCOM 傳送給您的郵件或文字以回應符合特定準則的通知。  您可以建立不同的通知訂閱具有不同根據這類準則監視物件、 警示的嚴重性、 偵測到的問題種通知的人員] 或 [一天的時間。

幾個訂閱可用於實作大量管理套件的策略完成的通知。

![是 SCOM 通知](media/operations-management-suite-monitoring-alerts/alerts-overview-scom.png)

記錄檔分析會通知您透過郵件通知已經建立的每個[通知規則](http://technet.microsoft.com/library/mt614775.aspx)上設定電子郵件通知動作。  它沒有是 SCOM 的能力以單一規則的多個提醒訂閱。  您也需要建立提醒的規則，因為 OMS 不提供任何預先設定。

![登入分析通知](media/operations-management-suite-monitoring-alerts/alerts-overview-oms.png)

您無法完全管理記錄分析是 SCOM 警示透過之後您僅可以修改這些作業主控台中檢視。  為記錄分析適合通知管理透過程序的沒有提供了解單獨使用的是 SCOM 的分析工具。

## <a name="alert-remediation"></a>通知補救
[補救](http://technet.microsoft.com/library/mt614775.aspx)指的是以自動修正通知可由問題。
  
是 SCOM 可讓您執行的監視器，輸入不健全的狀態的診斷和修復。  發生這種情況同時建立提醒的監視器。  診斷和修復通常是為指令碼代理程式上執行的實作。  診斷嘗試收集偵測到問題的相關詳細資訊，但是復原嘗試修正問題。

記錄分析，可讓您啟動[Azure 自動化 runbook](https://azure.microsoft.com/documentation/services/automation/)或通話 webhook 記錄分析警示。  Runbooks 可以包含在 PowerShell 中執行複雜的邏輯。  指令碼 Azure 中執行，並從雲端存取任何 Azure 資源或外部資源。  Azure 自動化會在您的本機資料中心，在伺服器上執行 runbooks 能力，但是啟動 runbook 記錄分析通知來回應時，並未目前提供這項功能。

復原中是 SCOM 和 runbooks OMS 中的都可以包含 PowerShell 指令碼，但復原難建立及管理，因為他們必須包含在管理套件。  Runbooks 會儲存在 Azure 自動化撰寫、 測試和管理 runbooks 提供的功能。

如果您使用是 SCOM 做為資料來源記錄分析，您可以建立使用記錄查詢來擷取是 SCOM 通知 OMS 存放庫中儲存記錄檔分析提醒。  這會讓您執行 Azure 自動化 runbook 是 SCOM 警示。  當然，runbook 會 Azure 中執行，因為這不是可行的策略復原的內部部署問題。

## <a name="next-steps"></a>後續步驟

- 瞭解[在系統管理中心的作業管理員 (是 SCOM) 通知](https://technet.microsoft.com/library/hh212913.aspx)的詳細資料。