<properties
   pageTitle="雲端服務角色回收的常見原因 |Microsoft Azure"
   description="突然回收雲端服務角色可能會導致嚴重停機時間。 以下是一些常見的問題導致回收，可協助您減少停機時間的角色。"
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
   ms.date="09/02/2016"
   ms.author="v-six" />

# <a name="common-issues-that-cause-roles-to-recycle"></a>常見的問題導致回收的角色

本文討論部署問題的常見原因的部分，並提供可協助您解決這些問題的疑難排解提示。 角色執行個體無法啟動，或將其循環初始化、 忙碌，和停止狀態時，應用程式有問題的指示。

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-runtime-dependencies"></a>遺漏 runtime 相依性

如果您的應用程式中的角色依賴任何不屬於.NET Framework 或 Azure 的組件受管理的文件庫，您必須明確該組應用程式套件。 請記住其他 Microsoft 架構並不適用於預設 Azure。 如果您的角色依賴這類架構，您必須將這些組件新增至應用程式套件。

您建立及封裝應用程式之前，請檢查下列各項︰

- 如果使用 Visual studio，請確定不屬於 Azure SDK 或.NET Framework 您專案中每個參考的組件的 [**複製到本機**] 屬性設為**True** 。

- 請確定 web.config 檔案不會參考編譯項目中任何未使用的組件。

- **建立巨集指令**的每個.cshtml 檔案] 設定為**內容**。 這可確保檔案會在套件中正確顯示，並讓其他參照的檔案，才會出現在套件中。

## <a name="assembly-targets-wrong-platform"></a>組件目標錯誤的平台

Azure 是 64 位元環境。 因此，Azure 不會使用 32 位元目標編譯的.NET 組件。

## <a name="role-throws-unhandled-exceptions-while-initializing-or-stopping"></a>角色擲回初始化或停止時的處理的例外狀況

會擲回[RoleEntryPoint]類別，包括[OnStart]、 [OnStop]，以及[執行]的方法的方法的任何例外是處理的例外狀況。 如果處理的例外狀況發生在其中一個方法，將資源角色。 如果重複回收角色，它可能會擲處理的例外狀況每次嘗試啟動。

## <a name="role-returns-from-run-method"></a>傳回從執行方法的角色

[執行]的方法被要執行。 如果您的程式碼覆寫[執行]的方法，它應該無限期休眠。 如果[執行]的方法傳回，角色回收。

## <a name="incorrect-diagnosticsconnectionstring-setting"></a>不正確的 DiagnosticsConnectionString 設定

如果應用程式會使用 Azure 診斷，您的服務設定檔必須指定`DiagnosticsConnectionString`設定的設定。 這項設定應 Azure 中指定 HTTPS 連線至您儲存的帳戶。

若要確保您`DiagnosticsConnectionString`設定是否正確，您將應用程式套件部署至 Azure 之前，請確認下列動作︰  

- `DiagnosticsConnectionString`設定有效的儲存空間帳戶 Azure 中的點。  
  根據預設，此設定重點模擬的儲存帳戶，以便在部署您的應用程式套件之前，您必須明確地變更這項設定。 如果您無法變更這項設定，例外狀況的角色執行個體嘗試啟動診斷監視器時。 這可能會導致無限期回收的角色執行個體。

- 以下列[格式](../storage/storage-configure-connection-string.md)指定連線字串。 （通訊協定必須指定為 HTTPS）。取代您儲存帳戶和*MyAccountKey*的名稱與您便捷鍵*MyAccountName* :    

        DefaultEndpointsProtocol=https;AccountName=MyAccountName;AccountKey=MyAccountKey

  如果您使用 Microsoft Visual Studio Azure 工具來開發應用程式，您可以使用將此值設定的[屬性頁面](https://msdn.microsoft.com/library/ee405486)。

## <a name="exported-certificate-does-not-include-private-key"></a>匯出的憑證不包括私密金鑰

若要執行下 SSL web 角色，您必須先確定您匯出的管理的憑證包含私密金鑰。 如果您使用*Windows 憑證管理員*匯出憑證，請務必選取 [**是**]，**匯出私密金鑰**的選項。 憑證必須匯出 PFX 格式，這是目前支援的唯一格式。

## <a name="next-steps"></a>後續步驟

檢視更多[疑難排解文章](https://azure.microsoft.com/documentation/articles/?tag=top-support-issue&product=cloud-services)雲端服務。

檢視更多回收案例在[Kevin Williamson 部落格系列](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx)的角色。

[RoleEntryPoint]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx
[OnStart]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx
[OnStop]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstop.aspx
[執行]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx
