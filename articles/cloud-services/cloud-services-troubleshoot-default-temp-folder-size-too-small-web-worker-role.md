<properties
   pageTitle="預設 TEMP 資料夾大小是太小角色的 |Microsoft Azure"
   description="雲端服務角色有一段有限的暫存資料夾的空間。 本文提供如何避免空間不足的一些建議。"
   services="cloud-services"
   documentationCenter=""
   authors="simonxjx"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>
<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="10/12/2016"
   ms.author="v-six" />

# <a name="default-temp-folder-size-is-too-small-on-a-cloud-service-webworker-role"></a>預設 TEMP 資料夾大小是太小雲端服務 web/工作者角色

預設的暫時目錄的雲端服務工作者或 web 角色具有大小上限為 100 MB、 可能會變得完整在某個時間點。 本文將說明如何避免附贈暫存目錄的空間。

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-do-i-run-out-of-space"></a>為何要執行空間？

標準的 Windows 環境變數 TEMP 和 TMP 可在應用程式中執行的程式碼。 同時 TEMP 和 TMP 指向單一目錄，具有大小上限為 100 MB。 儲存此目錄中的任何資料並不會保留在雲端服務; 的週期如果回收雲端服務中的角色執行個體，請，清除目錄。

## <a name="suggestion-to-fix-the-problem"></a>若要修正此問題的建議

實作下列替代方案的其中一個︰

- 設定本機存放區的資源，並直接，而不是使用 TEMP 或 TMP 存取。 若要從應用程式中執行的程式碼存取本機存放區資源，請連絡[RoleEnvironment.GetLocalResource](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.getlocalresource.aspx)方法。 

- 設定儲存本機資源，並指向 TEMP 和 TMP 目錄指向本機存放區資源的路徑。 修改應該執行[RoleEntryPoint.OnStart](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx)方法內。

下列範例會示範如何在 OnStart 方法修改目標目錄 TEMP 和 TMP 從︰


```csharp
using System;
using Microsoft.WindowsAzure.ServiceRuntime;

namespace WorkerRole1
{
    public class WorkerRole : RoleEntryPoint
    {
        public override bool OnStart()
        {
            // The local resource declaration must have been added to the
            // service definition file for the role named WorkerRole1:
            //
            // <LocalResources>
            //    <LocalStorage name="CustomTempLocalStore"
            //                  cleanOnRoleRecycle="false"
            //                  sizeInMB="1024" />
            // </LocalResources>

            string customTempLocalResourcePath =
            RoleEnvironment.GetLocalResource("CustomTempLocalStore").RootPath;
            Environment.SetEnvironmentVariable("TMP", customTempLocalResourcePath);
            Environment.SetEnvironmentVariable("TEMP", customTempLocalResourcePath);

            // The rest of your startup code goes here…

            return base.OnStart();
        }
    }
}
```

## <a name="next-steps"></a>後續步驟

請閱讀說明[如何增加 Azure Web 角色 ASP.NET 暫存資料夾大小](http://blogs.msdn.com/b/kwill/archive/2011/07/18/how-to-increase-the-size-of-the-windows-azure-web-role-asp-net-temporary-folder.aspx)的部落格。

檢視更多[疑難排解文章](/?tag=top-support-issue&product=cloud-services)雲端服務。

若要瞭解如何使用 Azure PaaS 電腦診斷資料，即可疑難排解在雲端服務角色問題，請檢視[Kevin Williamson 部落格系列](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx)。
