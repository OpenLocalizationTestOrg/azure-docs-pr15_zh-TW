



如需 VM 代理程式，以及如何運作支援 VM 延伸模組的詳細資訊，請參閱[VM 代理程式和 VM 擴充功能概觀](../articles/virtual-machines/virtual-machines-windows-classic-manage-extensions.md)。

##<a name="azure-vm-extensions"></a>Azure VM 副檔名

VM 副檔名實作大部分的要徑的功能，您想要使用您的 Vm，包括基本功能，例如重設密碼，設定 RDP，及眾多其他人。 新的副檔名新增任何時間，因為可能您 Vm 支援 Azure 中的功能數目會持續增加。 根據預設，當您建立您 VM 圖像庫，包括**IaaSDiagnostics** (目前 Windows Vm 只)、 **VMAccess**，與**BGInfo** (也目前 Windows 僅) 安裝數個基本 VM 副檔名。 不過，並非所有擴充功能 Linux 和 Windows 上實作在任何指定的時間，因為固定的功能更新及新的副檔名。

##<a name="connectivity-and-basic-management"></a>連線與基本的管理

下列的副檔名是啟用、 重新啟用，或停用與您的 Vm 基本的連線之後會建立, 與執行的關鍵。

|VM 副檔名|功能描述|詳細資訊
|---|---|---|
|VMAccessAgent (Windows)|建立、 更新和重設使用者的資訊和 RDP 連線設定。|[Windows](../articles/virtual-machines/virtual-machines-windows-classic-extensions-customscript.md)
|VMAccessForLinux (Linux)|建立、 更新和重設使用者的資訊和 SSH 連線設定。|[Linux](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess)

##<a name="deployment-and-configuration-management"></a>部署及管理設定

下列擴充功能支援不同類型的部署及設定管理案例和功能。 另請參閱 VM 作業及管理] 區段下方。

|VM 副檔名|功能描述|詳細資訊|
|---|---|---|
|**MSEnterpriseApplication**|實作 Windows 系統管理中心所支援的功能。|[系統管理中心 2012 R2 虛擬機器角色](http://social.technet.microsoft.com/wiki/contents/articles/18274.system-center-2012-r2-virtual-machine-role-authoring-guide-resource-extension-package.aspx)|
|**Octopus 部署**（DSC 副檔名為基礎）|支援自動化部署到開發、 測試和生產環境 ASP.NET web 應用程式和 Windows 服務。|[快速入門 Octopus 部署](http://docs.octopusdeploy.com/display/OD/Getting%20started)|
|**Visual Studio 版本管理員**（DSC 副檔名為基礎）|支援 Visual Studio 連續部署。|[自動化版本管理的部署](https://msdn.microsoft.com/Library/vs/alm/Release/overview)|
|**CentosChefClient**|||
|**ChefClient**|在 Windows 上建立主廚用戶端。 （也可以使用 DSC 副檔名下方。）|[主廚和 Microsoft Azure](https://www.getchef.com/solutions/azure/)|
|**LinuxChefClient**|||
|**DockerExtension**|安裝支援遠端 Docker 命令的 Docker 精靈。|[如何使用 Docker 虛擬機器副檔名](../articles/virtual-machines/virtual-machines-linux-dockerextension.md)如需詳細資訊，請參閱[Docker VM 副檔名使用者指南](https://github.com/Azure/azure-docker-extension/blob/master/README.md)|
|**DSC**|PowerShell DSC （所需的狀態設定） 副檔名。|[Azure PowerShell DSC （所需的狀態設定） 副檔名](http://blogs.msdn.com/b/powershell/archive/2014/08/07/introducing-the-azure-powershell-dsc-desired-state-configuration-extension.aspx)|
|**PuppetEnterpriseAgent**|實作傀儡企業版的功能。 |[在 Azure 傀儡](http://puppetlabs.com/solutions/microsoft)|
|**CustomScriptExtension**(Windows)**CustomScriptForLinux**(Linux)|隨時呼叫 VM 上的自訂指令碼︰ 啟動或存留期間。|[自訂指令碼副檔名](../articles/virtual-machines/virtual-machines-windows-classic-extensions-customscript.md) | [Linux](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript)|
|**AzureCATExtensionHandler**|使用診斷收集**IaaSDiagnostics** ，例如[Azure 儲存分析指標](https://msdn.microsoft.com/library/azure/hh343270.aspx)的一些其他資料來源的資料，並將它轉換成適當的 SAP 主機控制程序來彙總資料集|[Azure 增強型的 SAP 監控](https://azure.microsoft.com/blog/2014/06/04/azure-enhanced-monitoring-for-sap/)|

##<a name="security-and-protection"></a>安全性並保護

本節中的擴充提供您 Azure Vm 重要的安全性功能。

|VM 副檔名|功能描述|詳細資訊|
|---|---|---|
|**CloudLinkSecureVMWindowsAgent**|Microsoft Azure 客戶提供加密虛擬機器資料多租用戶共用基礎結構和完整的加密資料加密金鑰 Azure 儲存基礎結構上的控制項上的功能。|[保護 Microsoft Azure 虛擬機器充分使用原生 OS BitLocker 加密](http://www.cloudlinktech.com/azure)|
|**McAfeeEndpointSecurity**|保護您 VM 惡意軟體。|[McAfee](https://www.mcafeeasap.com/MarketingContent/default.aspx)|
|**TrendMicroDSA**|啟用提供侵入偵測並防止、 防火牆、 反惡意程式碼、 web 評價、 記錄檢查和完整性監控 TrendMicro 的深安全性平台支援。|[如何安裝和設定趨勢微型深層安全性 Azure VM 上的服務](../articles/virtual-machines/virtual-machines-windows-classic-install-trend.md)|
|**PortalProtectExtension**|針對您的 Microsoft SharePoint 環境威脅守衛。|[保護您的 SharePoint 部署上 Azure](http://blog.trendmicro.com/securing-sharepoint-deployment-azure/)|
|**IaaSAntimalware**|Microsoft 反惡意程式碼 Azure 雲端服務和虛擬機器是有助於識別和移除的病毒、 間諜軟體及其他惡意軟體，已知惡意或有害軟體嘗試自行安裝，或在您的系統上執行時，可以設定通知即時保護功能。|[反惡意程式碼 Azure 雲端服務和虛擬機器](../articles/azure-security-antimalware.md)|
|**SymantecEndpointProtection**|Symantec 端點保護 12.1.4 實體和虛擬系統中啟用安全性和效能。|[如何安裝和設定 Azure VM Symantec 端點保護](../articles/virtual-machines/virtual-machines-windows-classic-install-symantec.md)

##<a name="vm-operations-and-management"></a>VM 作業與管理

支援一般作業管理功能和行為。 另請參閱上部署及設定管理] 區段的上方。

|**VM 副檔名**|功能描述|詳細資訊|
|---|---|---|
|**AzureVmLogCollector**|您可以使用**AzureVMLogCollector**副檔名視您的記錄檔的一次性集合 （從 web 角色及工作角色） 的一或多個雲端服務 Vm 並傳輸 Azure 儲存體帳戶 – 所有不必遠端登入任何 Vm 收集的檔案。 |[AzureLogCollector 副檔名](../articles/virtual-machines/virtual-machines-windows-log-collector-extension.md)|
|**IaaSDiagnostics**|啟用已停用，並設定 Azure 診斷與也由**AzureCATExtensionHandler**用來支援 SAP 監視。|[Microsoft Azure 虛擬機器監控 Azure 診斷副檔名](https://azure.microsoft.com/blog/2014/09/02/windows-azure-virtual-machine-monitoring-with-wad-extension/)|
|**OSPatchingForLinux**|可讓 Azure VM 管理員自動化 VM OS 更新自訂的設定。 您可以使用 OSPatching 副檔名為您的虛擬機器，包括設定 OS 更新︰ 指定頻率，並安裝作業系統修補程式，指定什麼修補程式，來安裝及設定重開機行為之後更新|[OS 修補副檔名部落格文章](https://azure.microsoft.com/blog/2014/10/23/automate-linux-vm-os-updates-using-ospatching-extension/)。 另請參閱讀我檔案與來源上 GitHub 在[OS 修補副檔名](https://github.com/Azure/azure-linux-extensions)。|

##<a name="developing-and-debugging"></a>開發及偵錯

這些 VM 延伸此處列出的完整性，他們的 Visual Studio 相關功能提供支援，並不是直接使用。

|VM 副檔名|功能描述|詳細資訊|
|---|---|---|
|**VS14CTPDebugger**|支援與使用 Azure SDK 2.4 從遠端偵錯|[遠端 Visual Studio 中偵錯](https://msdn.microsoft.com/library/y7f5zaaa.aspx)|
|**VS2013Debugger**|支援與使用 Azure SDK 2.4 從遠端偵錯||
|**VS2012Debugger**|支援與使用 Azure SDK 2.4 從遠端偵錯||
|**RemoteDebugVS14CTP**|支援與使用 Azure SDK 2.3 從遠端偵錯||
|**RemoteDebugVS2013**|支援與使用 Azure SDK 2.3 從遠端偵錯||
|**RemoteDebugVS2012**|支援與使用 Azure SDK 2.3 從遠端偵錯||
|**WebDeployForVSDevTest**|安裝和設定 IIS 和 Web 部署在 Windows Server 上。 移除或停用它不受支援。|

##<a name="miscellaneous-features"></a>其他功能

這些擴充功能的其他可能很有用的 VM 功能提供支援。

|VM 副檔名|功能描述|詳細資訊|
|---|---|---|
|**BGInfo**|使用 RDP 時，會顯示在桌面上的實用的伺服器資訊合併的圖片。|[BGInfo 副檔名](https://msdn.microsoft.com/library/mt589195.aspx)|
|**HpcVmDrivers**|安裝、 設定和維護遠端直接記憶體存取 (RDMA) 網路上裝置驅動程式 A8 或執行 Windows Server 2012 R2 或 Windows Server 2012 A9 VM 的大小。 A8 或 A9 Vm 執行平行 MPI 應用程式時，使用 RDMA 網路，則可讓群組直條圖。|[關於 A8、 A9、 A10 及 A11 運算密集執行個體](../articles/virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md)

