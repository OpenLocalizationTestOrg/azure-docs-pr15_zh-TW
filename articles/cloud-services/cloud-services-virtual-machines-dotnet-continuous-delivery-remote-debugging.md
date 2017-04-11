<properties
    pageTitle="啟用遠端偵錯連續傳遞 |Microsoft Azure"
    description="瞭解如何啟用遠端偵錯時要部署至此 Azure 使用連續的傳送"
    services="cloud-services"
    documentationCenter=".net"
    authors="TomArcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="tarcher"/>

# <a name="enable-remote-debugging-when-using-continuous-delivery-to-publish-to-azure"></a>啟用遠端偵錯時發佈至 Azure 使用連續的傳送

您可以啟用遠端偵錯中 Azure 虛擬機器雲端服務的發佈至 Azure 中，執行下列步驟使用[連續傳遞](cloud-services-dotnet-continuous-delivery.md)時。

## <a name="enabling-remote-debugging-for-cloud-services"></a>啟用遠端偵錯的雲端服務

1. 建立代理程式上設定的初始環境 Azure[命令列建立 Azure](http://msdn.microsoft.com/library/hh535755.aspx)中所述。
2. 因為需要套件偵錯執行階段 (msvsmon.exe)，請安裝[的 Visual Studio 2015 遠端工具](http://www.microsoft.com/en-us/download/details.aspx?id=48155)（或[Microsoft Visual Studio 2013 更新 5 的遠端工具](https://www.microsoft.com/en-us/download/details.aspx?id=48156)如果您使用 Visual Studio 2013）。 或者，您可以從已安裝的 Visual Studio 系統複製偵錯二進位檔案。
3. [Azure 雲端服務的憑證概觀](cloud-services-certs-create.md)中所述，建立憑證。 保留.pfx 與 RDP 憑證指紋上, 傳至目標雲端服務的憑證。
4. 建立並啟用遠端偵錯與封裝 MSBuild 命令列中使用下列選項。 （取代實際的路徑，您的系統與專案檔案的角度帶有括號的項目）。

        msbuild /TARGET:PUBLISH /PROPERTY:Configuration=Debug;EnableRemoteDebugger=true;VSX64RemoteDebuggerPath="<remote tools path>";RemoteDebuggerConnectorCertificateThumbprint="<thumbprint of the certificate added to the cloud service>";RemoteDebuggerConnectorVersion="2.7" "<path to your VS solution file>"

    `VSX64RemoteDebuggerPath`資料夾的路徑 Visual studio 包含 msvsmon.exe 遠端工具] 中。
    `RemoteDebuggerConnectorVersion`為您的雲端服務中的 Azure SDK 版本。 它也應該相符 Visual Studio 安裝的版本。

5. 使用上一個步驟所產生的封裝並.cscfg 檔案發佈至目標雲端服務。
6. 將憑證 （.pfx 檔） 匯入具有與 Azure SDK 的 Visual Studio.net 安裝的電腦。 請確定您匯入至`CurrentUser\My`憑證存放區，否則附加至 Visual Studio 中偵錯工具將會失敗。

## <a name="enabling-remote-debugging-for-virtual-machines"></a>啟用遠端偵錯的虛擬機器

1. 建立 Azure 虛擬機器。 請參閱[建立執行 Windows Server 的虛擬機器](../virtual-machines/virtual-machines-windows-hero-tutorial.md)或[建立及管理 Visual Studio 中 Azure 虛擬機器](../virtual-machines/virtual-machines-windows-classic-manage-visual-studio.md)。
2. 在[Azure 傳統的入口網站頁面](http://go.microsoft.com/fwlink/p/?LinkID=269851)上，檢視以查看虛擬機器**RDP 憑證指紋**的虛擬機器中的儀表板。 使用此值的`ServerThumbprint`延伸設定中的值。
3. [Azure 雲端服務的憑證概觀](cloud-services-certs-create.md)中所述，建立用戶端憑證 （保留.pfx 及 RDP 憑證指紋）。
4. 安裝 Powershell 的 Azure (版本 0.7.4 或更新版本)[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md)中所述。
5. 執行下列指令碼啟用 RemoteDebug 副檔名。 使用您自己的圖片，例如您的訂閱名稱、 服務名稱及指紋取代的路徑和個人的資料。

    >[AZURE.NOTE] Visual Studio 2015 設定這個指令碼。 如果您使用 Visual Studio 2013，修改`$referenceName`和`$extensionName`指派下列使用`RemoteDebugVS2013`(而不是`RemoteDebugVS2015`)。

    <pre>
   新增 AzureAccount

    選取 AzureSubscription 「 我的 Microsoft 訂閱 」

    $vm = 取得 AzureVM ServiceName 「 mytestvm1 」-名稱 「 mytestvm1 」

    $endpoints = @( ,@{Name="RDConnVS2013"; PublicPort = 30400;PrivatePort = 30398} ,@{Name="RDFwdrVS2013"; PublicPort = 31400;PrivatePort = 31398})  

    foreach ($endpoint 中 $endpoints) {新增 AzureEndpoint VM $vm-命名 $endpoint。命名-通訊協定 tcp-PublicPort $endpoint。PublicPort LocalPort $endpoint。PrivatePort}

    $referenceName = 「 Microsoft.VisualStudio.WindowsAzure.RemoteDebug.RemoteDebugVS2015 」 $publisher = 「 Microsoft.VisualStudio.WindowsAzure.RemoteDebug 」 $extensionName = 「 RemoteDebugVS2015 」 $version = 「 1.* 」 $publicConfiguration ="<PublicConfig>< Connector.Enabled > true < /Connector.Enabled ><ClientThumbprint>56D7D1B25B472268E332F7FC0C87286458BFB6B2</ClientThumbprint><ServerThumbprint>E7DCB00CB916C468CC3228261D6E4EE45C8ED3C6</ServerThumbprint><ConnectorPort>30398</ConnectorPort><ForwarderPort>31398</ForwarderPort></PublicConfig>」

    $vm |設定 AzureVMExtension `
    -ReferenceName $referenceName ` 
    -Publisher $publisher `
    -ExtensionName $extensionName ` 
    -版本 $version '-PublicConfiguration $publicConfiguration

    foreach ($extension $vm 中。VM。ResourceExtensionReferences) {如果 (($extension.參照名稱 eq $referenceName) `
    -and ($extension.Publisher -eq $publisher) ` 
    -和 ($extension。命名-eq $extensionName） '-和 ($extension。版本-eq $version)) {$extension。ResourceExtensionParameterValues [0]。索引鍵 = 'config.txt' 符號}}

    $vm |更新 AzureVM </pre>

6. 將憑證 (.pfx) 匯入具有與 Azure SDK 的 Visual Studio.net 安裝的電腦。
