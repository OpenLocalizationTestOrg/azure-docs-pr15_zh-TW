<properties 
pageTitle="處理雲端服務的生命週期事件 |Microsoft Azure" 
description="瞭解如何使用雲端服務角色的生命週期方法.net" 
services="cloud-services" 
documentationCenter=".net" 
authors="Thraka" 
manager="timlt" 
editor=""/>
<tags 
ms.service="cloud-services" 
ms.workload="tbd" 
ms.tgt_pltfrm="na" 
ms.devlang="na" 
ms.topic="article" 
ms.date="09/06/2016" 
ms.author="adegeo"/>

# <a name="customize-the-lifecycle-of-a-web-or-worker-role-in-net"></a>自訂網頁或背景工作中的角色的.NET 週期

當您建立工作者角色時，您會擴充[RoleEntryPoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx)類別提供可讓您可供您覆寫方法回應生命週期的事件。 Web 角色，您必須使用它來回應生命週期的事件，是選擇性的此課程。

## <a name="extend-the-roleentrypoint-class"></a>擴充 RoleEntryPoint 類別

[RoleEntryPoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx)類別包含 Azure 呼叫的方法時，**啟動**、**執行**，或**停止**網頁或背景工作的角色。 您可以選擇覆寫這些方法來管理角色初始化、 角色關機順序或該角色的執行緒。 

在擴充時**RoleEntryPoint**，您應該注意下列問題的方法︰

-   [OnStart](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx)和[OnStop](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstop.aspx)方法會傳回一個 boolean 值，，就可以從這些方法會傳回**false** 。

     如果您的程式碼會傳回**false**，角色程序會意外終止，而不執行任何關閉順序，您可能需要在的位置。 一般而言，您應避免從**OnStart**方法傳回**false** 。
     
-   任何遺漏的例外**RoleEntryPoint**方法的超載中被視為處理的例外狀況。

     如果發生例外狀況內生命週期兩種方法之一，Azure 會引發[UnhandledException](https://msdn.microsoft.com/library/system.appdomain.unhandledexception.aspx)事件，，然後結束程序。 您的角色已離線之後，它將會重新 Azure 的啟動。 處理的例外狀況發生時，就不會引發[停止](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.stopping.aspx)事件，而不會呼叫**OnStop**方法。

如果您的角色無法啟動，或為回收之間初始化忙碌，和停止狀態，您的程式碼可能會擲回內的其中一個每次重新啟動角色生命週期事件處理的例外狀況。 在此情況下，使用[UnhandledException](https://msdn.microsoft.com/library/system.appdomain.unhandledexception.aspx)事件來決定例外狀況的原因，以及適當的處理方式。 您的角色也可能會起[執行](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)方法，會導致重新啟動的角色。 如需有關部署狀態的詳細資訊，請參閱[一般問題的原因角色與資源](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md)。

> [AZURE.NOTE] 如果您使用**Microsoft Visual Studio Azure 工具**開發應用程式，角色專案範本會自動延伸**RoleEntryPoint**類別的*WebRole.cs*和*WorkerRole.cs*檔案。

## <a name="onstart-method"></a>OnStart 方法

當您的角色執行個體上線，Azure，稱為**OnStart**方法。 OnStart 程式碼執行時，角色執行個體標示為**忙碌**，而且沒有外部的流量會導向至其負載平衡器以。 您可以覆寫此方法可執行初始化工作，例如實作事件處理常式和啟動[Azure 診斷](cloud-services-how-to-monitor.md)。

如果**OnStart** ，則傳回**true**，成功初始化執行個體，然後 Azure 呼叫**RoleEntryPoint.Run**方法。 如果**OnStart**會傳回**false**，角色結束，而不執行任何計劃的關機順序。

下列範例會示範如何覆寫**OnStart**方法。 這個方法設定，並啟動診斷監視器的角色執行個體啟動，並設定記錄資料傳送到儲存的帳戶︰

```csharp
public override bool OnStart()
{
    var config = DiagnosticMonitor.GetDefaultInitialConfiguration();

    config.DiagnosticInfrastructureLogs.ScheduledTransferLogLevelFilter = LogLevel.Error;
    config.DiagnosticInfrastructureLogs.ScheduledTransferPeriod = TimeSpan.FromMinutes(5);

    DiagnosticMonitor.Start("DiagnosticsConnectionString", config);

    return true;
}
```

## <a name="onstop-method"></a>OnStop 方法

角色執行個體已離線，Azure 之後和結束程序之前，稱為**OnStop**方法。 您可以覆寫此方法呼叫徹底關閉您的角色執行個體所需的程式碼。

> [AZURE.IMPORTANT] **OnStop**方法中執行的程式碼具有有限的時間完成時，要求使用者啟動的關機以外的原因。 此時間之後，結束，因此您必須確定**OnStop**方法可以快速地執行程式碼的程序，或可容許不在執行中完成。 之後，**停止**事件會引發呼叫**OnStop**方法。


## <a name="run-method"></a>執行的方法

您可以覆寫實作長執行緒角色執行個體的**執行**方式。

覆寫**執行**方法不是必要;預設實作開始永遠休眠執行緒。 如果您不要覆寫的**執行**方式，您的程式碼應該無限期封鎖。 如果**執行**的方法傳回，角色就會自動適當地回收;換句話說，Azure 引發**停止**事件，然後呼叫**OnStop**方法，讓之前角色離線時，可能會執行您關閉順序。


### <a name="implementing-the-aspnet-lifecycle-methods-for-a-web-role"></a>實作 ASP.NET 生命週期的方法網頁角色

您可以使用 ASP.NET 生命週期的方法，除了**RoleEntryPoint**類別，提供管理初始化和 web 角色的關機順序。 如果您移轉 Azure 現有的 ASP.NET 應用程式時，這可能是用於相容性用途。 ASP.NET 生命週期方法是從呼叫內**RoleEntryPoint**方法。 **應用程式\_開始** **RoleEntryPoint.OnStart**方法完成後呼叫方法。 **應用程式\_結束**方法稱為呼叫**RoleEntryPoint.OnStop**方法之前。

## <a name="next-steps"></a>後續步驟
瞭解如何[建立雲端服務套件](cloud-services-model-and-package.md)。