<properties
    pageTitle="Azure 資源群組專案部署指令碼概觀 |Microsoft Azure"
    description="說明 Azure 資源群組部署專案中的 PowerShell 指令碼的運作方式。"
    services="visual-studio-online"
    documentationCenter="na"
    authors="tfitzmac"
    manager="timlt"
    editor="" />

 <tags
    ms.service="azure-resource-manager"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="07/26/2016"
    ms.author="tomfitz" />

# <a name="overview-of-the-azure-resource-group-project-deployment-script"></a>Azure 資源群組專案部署指令碼的概觀

Azure 資源群組部署專案階段，並將檔案和其他成品部署 Azure 的說明。 當您在 Visual Studio 建立 Azure 資源管理員部署專案時，稱為**部署 AzureResourceGroup.ps1** PowerShell 指令碼會新增到專案。 本主題提供這個指令碼功能，以及如何執行內和 Visual Studio 以外的詳細資料。

## <a name="what-does-the-script-do"></a>指令碼做什麼？

部署 AzureResourceGroup.ps1 指令碼並部署工作流程重要的兩個項目。

- 上傳的任何檔案或範本部署所需的成品
- 部署範本

指令碼的第一個部分上傳的檔案及部署，成品和指令碼中的最後一個 cmdlet 實際部署範本。 例如，如果虛擬機器需要使用指令碼來設定，部署指令碼第一次安全地上傳設定指令碼 Azure 儲存體帳戶。 這樣就可以使用至 Azure 資源管理員期間佈建設定虛擬機器。

並非所有範本部署需要都有額外需要上傳的成品，因為評估稱為*uploadArtifacts*的切換參數。 如果需要上傳的任何成品，請設定呼叫指令碼*uploadArtifacts*切換。 請注意的主要範本檔案及參數檔案不需要上傳。 其他檔案、 設定指令碼，例如巢狀結構的部署範本，需要上傳應用程式的檔案。

## <a name="detailed-script-description"></a>詳細的指令碼說明

下列是部署 AzureResourceGroup.ps1 Azure PowerShell 指令碼執行中的哪些選取區段的描述。

>[AZURE.NOTE] 此說明 1.0 版的部署 AzureResourceGroup.ps1 指令碼。

1.  宣告 Azure 資源管理員部署專案所需的參數。 某些參數有建立專案時設定的預設值。 您可以變更這些指令碼中的預設值，或新增不同的參數值，才能執行指令碼。

    ```
    Param(
      [string] [Parameter(Mandatory=$true)] $ResourceGroupLocation,
      [string] $ResourceGroupName = 'AzureResourceGroup1',
      [switch] $UploadArtifacts,
      [string] $StorageAccountName,
      [string] $StorageAccountResourceGroupName,
      [string] $StorageContainerName = $ResourceGroupName.ToLowerInvariant() + '-stageartifacts',
      [string] $TemplateFile = '..\Templates\azuredeploy.json',
      [string] $TemplateParametersFile = '..\Templates\azuredeploy.parameters.json',
      [string] $ArtifactStagingDirectory = '..\bin\Debug\staging',
      [string] $AzCopyPath = '..\Tools\AzCopy.exe',
      [string] $DSCSourceFolder = '..\DSC'
    )
    ```

  	|參數|描述|
  	|---|---|
  	|$ResourceGroupLocation|地區或資料中心的 [資源] 群組中，例如**西美國**或**中式地址**的位置。|
  	|$ResourceGroupName|Azure 資源群組的名稱。|
  	|$UploadArtifacts|二進位值，指出是否需要上傳至 Azure 從系統的成品。|
  	|$StorageAccountName|您上傳您的成品所在位置的 Azure 儲存體帳戶名稱。|
  	|$StorageAccountResourceGroupName|包含的儲存空間帳戶 Azure 資源群組的名稱。|
  	|$StorageContainerName|使用上傳的成品存放容器的名稱。|
  	|$TemplateFile|部署檔案路徑 (`<app name>.json`) Azure 資源群組專案中。|
  	|$TemplateParametersFile|參數檔案路徑 (`<app name>.parameters.json`) Azure 資源群組專案中。|
  	|$ArtifactStagingDirectory|在系統成品在本機上傳所在位置，包括 PowerShell 指令碼根資料夾路徑。 此路徑可以是絕對或相對於指令碼位置。|
  	|$AzCopyPath|位置 AzCopy.exe 工具複製其.zip 檔案，包括 PowerShell 指令碼根資料夾路徑。 此路徑可以是絕對或相對於指令碼位置。|
  	|$DSCSourceFolder|DSC （所需的狀態設定） 的來源資料夾，包括 PowerShell 指令碼根資料夾的路徑。 此路徑可以是絕對或相對於指令碼位置。 若適用，如需詳細資訊，請參閱[介紹副檔名為 Azure PowerShell DSC （所需的狀態設定）](http://blogs.msdn.com/b/powershell/archive/2014/08/07/introducing-the-azure-powershell-dsc-desired-state-configuration-extension.aspx)。|

1.  請檢查是否需要上傳至 Azure 成品。 如果沒有，直接跳到步驟 11。 否則，請執行下列步驟。

1.  相對路徑的任何變數轉換成絕對的路徑。 例如，例如變更的路徑`..\Tools\AzCopy.exe`至`C:\YourFolder\Tools\AzCopy.exe`。 此外，初始化變數*ArtifactsLocationName*和*ArtifactsLocationSasTokenName*為 null。 *ArtifactsLocation* *SaSToken*可能會與範本參數。 如果參數檔案中的讀取後，其值是 null，指令碼會產生的值。

    Azure 工具使用的參數值*_artifactsLocation*及*_artifactsLocationSasToken*範本中管理成品。 如果 PowerShell 指令碼會找出參數的名稱，但不是提供的參數值，指令碼上傳的成品，並傳回這些參數的適當的值。 然後透過 cmdlet 傳遞`@OptionsParameters`。

  	|變數|描述|
  	|---|---|
  	|ArtifactsLocationName|Azure 成品的所在位置的路徑。|
  	|ArtifactsLocationSasTokenName|用的指令碼來驗證服務匯流排 SA （共用 Access 簽章） 權杖的名稱。 如需詳細資訊，請參閱[共用 Access 簽章驗證服務匯流排](service-bus-shared-access-signature-authentication.md)。|

    ```
    if ($UploadArtifacts) {
    # Convert relative paths to absolute paths if needed
    $AzCopyPath = [System.IO.Path]::Combine($PSScriptRoot, $AzCopyPath)
    $ArtifactStagingDirectory = [System.IO.Path]::Combine($PSScriptRoot, $ArtifactStagingDirectory)
    $DSCSourceFolder = [System.IO.Path]::Combine($PSScriptRoot, $DSCSourceFolder)

    Set-Variable ArtifactsLocationName '_artifactsLocation' -Option ReadOnly
    Set-Variable ArtifactsLocationSasTokenName '_artifactsLocationSasToken' -Option ReadOnly

    $OptionalParameters.Add($ArtifactsLocationName, $null)
    $OptionalParameters.Add($ArtifactsLocationSasTokenName, $null)
    ```

1.  此區段檢查是否<app name>。 （又稱為 「 參數檔案 」） 的 parameters.json 檔案具有名為**參數**的上層節點 (在`else`區塊)。 否則，它有沒有父節點。 [格式為可接受的。
    
    ```
    if ($JsonParameters -eq $null) {
            $JsonParameters = $JsonContent
        }
        else {
            $JsonParameters = $JsonContent.parameters
        }
    ```

1.  逐一查看 JSON 參數的集合。 如果*_artifactsLocation*或*_artifactsLocationSasToken*已指派的參數值，然後設定變數*$OptionalParameters*這些值。 如此可避免指令碼不小心覆寫任何所提供的參數值。

    ```
    $JsonParameters | Get-Member -Type NoteProperty | ForEach-Object {
        $ParameterValue = $JsonParameters | Select-Object -ExpandProperty $_.Name

        if ($_.Name -eq $ArtifactsLocationName -or $_.Name -eq $ArtifactsLocationSasTokenName) {
            $OptionalParameters[$_.Name] = $ParameterValue.value
        }
    }
    ```

1.  用來儲存以供部署成品儲存帳戶資源，取得的儲存空間的 [帳戶金鑰和內容。

    ```
    $StorageAccountKey = (Get-AzureRMStorageAccountKey -ResourceGroupName $StorageAccountResourceGroupName -Name $StorageAccountName).Key1

    $StorageAccountContext = (Get-AzureRmStorageAccount -ResourceGroupName $StorageAccountResourceGroupName -Name $StorageAccountName).Context
    ```

1.  如果您使用 PowerShell DSC 若要設定的虛擬機器，DSC 需要在單一 zip 檔案的成品。 因此，建立 DSC 設定.zip 封存檔案。 若要這麼做，請檢查 $DSCSourceFolder 是否存在。 如果 DSC 設定已存在，移除它，然後建立稱為 dsc.zip 新壓縮的檔案。

    ```
    # Create DSC configuration archive
    if (Test-Path $DSCSourceFolder) {
    Add-Type -Assembly System.IO.Compression.FileSystem
        $ArchiveFile = Join-Path $ArtifactStagingDirectory "dsc.zip"
        Remove-Item -Path $ArchiveFile -ErrorAction SilentlyContinue
        [System.IO.Compression.ZipFile]::CreateFromDirectory($DSCSourceFolder, $ArchiveFile)
    }
    ```

1.  如果沒有路徑 Azure 成品提供參數檔案中，設定上傳的成品時使用的 PowerShell 指令碼的路徑。 若要這麼做，建立路徑使用儲存帳戶的端點路徑加上儲存容器名稱的組合。 此新的路徑，然後更新參數檔案。

    ```
    # Generate the value for artifacts location if it is not provided in the parameter file
    $ArtifactsLocation = $OptionalParameters[$ArtifactsLocationName]
    if ($ArtifactsLocation -eq $null) {
        $ArtifactsLocation = $StorageAccountContext.BlobEndPoint + $StorageContainerName
        $OptionalParameters[$ArtifactsLocationName] = $ArtifactsLocation
    }
    ```

1.  若要將您的本機存放區拖放路徑，任何檔案複製到您的線上 Azure 儲存體帳戶使用**AzCopy**公用程式 （包括中 Azure 資源群組部署專案中的 [**工具**] 資料夾）。 如果這個步驟失敗，請結束指令碼，因為部署可能不是成功，而不必要的成品。

    ```
    # Use AzCopy to copy files from the local storage drop path to the storage account container
    & $AzCopyPath """$ArtifactStagingDirectory""", $ArtifactsLocation, "/DestKey:$StorageAccountKey", "/S", "/Y", "/Z:$env:LocalAppData\Microsoft\Azure\AzCopy\$ResourceGroupName"
    if ($LASTEXITCODE -ne 0) { return }
    ```

1.  如果成品位置 SA 權杖不提供參數檔案中，建立一個以暫時唯讀存取線上存放容器。 然後，該 SA 將權杖傳遞至命令行為 「 optionalParameter 」。 請注意，任何命令行傳遞的參數會優先於參數檔案中所提供的值。

    ```
    # Generate the value for artifacts location SAS token if it is not provided in the parameter file
    $ArtifactsLocationSasToken = $OptionalParameters[$ArtifactsLocationSasTokenName]
    if ($ArtifactsLocationSasToken -eq $null) {
       # Create a SAS token for the storage container - this gives temporary read-only access to the container
       $ArtifactsLocationSasToken = New-AzureStorageContainerSASToken -Container $StorageContainerName -Context $StorageAccountContext -Permission r -ExpiryTime (Get-Date).AddHours(4)
       $ArtifactsLocationSasToken = ConvertTo-SecureString $ArtifactsLocationSasToken -AsPlainText -Force
       $OptionalParameters[$ArtifactsLocationSasTokenName] = $ArtifactsLocationSasToken
    }
    ```

1.  如果尚未不存在，核取 [將無法從成功部署的任何驗證錯誤的範本和參數檔，請建立資源群組。

    ```
    # Create or update the resource group using the specified template file and template parameters file
    New-AzureRMResourceGroup -Name $ResourceGroupName -Location $ResourceGroupLocation -Verbose -Force -ErrorAction Stop

    Test-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile $TemplateFile -TemplateParameterFile $TemplateParametersFile @OptionalParameters -ErrorAction Stop
    ```

1. 最後，部署範本。 將此程式碼會建立部署使用時間戳記的唯一名稱。

    ```
    New-AzureRMResourceGroupDeployment -Name ((Get-ChildItem $TemplateFile).BaseName + '-' + ((Get-Date).ToUniversalTime()).ToString('MMdd-HHmm')) `
        -ResourceGroupName $ResourceGroupName `
        -TemplateFile $TemplateFile `
        -TemplateParameterFile $TemplateParametersFile `
        @OptionalParameters `
        -Force -Verbose
    ```

## <a name="deploy-the-resource-group"></a>部署資源群組

### <a name="to-deploy-the-resource-group-in-visual-studio"></a>若要部署 Visual Studio 中的 [資源] 群組

1. Azure 資源群組專案的快顯功能表，選擇 [**部署** > **新部署**。

    ![][0]

1. 在 [**部署至 [資源群組**] 對話方塊中，[部署至，或選擇**下拉式清單方塊中，選擇現有的資源群組&lt;建立新...&gt;** 若要建立新的資源群組。

    ![][1]

1. 如果出現提示，請在**建立資源群組**] 對話方塊中，輸入資源群組名稱和位置，然後選擇 [**建立**] 按鈕。

    ![][2]

1. 選擇 [**編輯參數]**按鈕，檢視**編輯參數**] 對話方塊，然後輸入 [任何遺漏的參數值。

    ![][3]

    >[AZURE.NOTE] 如果任何必要的參數會需要的值，這個對話方塊就會自動出現在部署。

    ![][4]

1. 當您完成輸入參數值，選擇 [**儲存**] 按鈕，然後選擇 [**部署**] 按鈕。

    部署指令碼 (部署 AzureResourceGroup.ps1) 執行，且您的範本，以及任何成品，部署至 Azure。

### <a name="to-deploy-the-resource-group-by-using-powershell"></a>使用 PowerShell 部署資源群組

如果您想要執行指令碼，而不使用 Visual Studio 中部署] 命令，UI，指令碼快顯功能表上，選擇 [ **PowerShell ise [以系統中開啟**]。

![][5]


## <a name="command-deployment-examples"></a>命令部署範例

### <a name="deploy-using-default-values"></a>部署使用預設值

此範例會示範如何執行指令碼使用預設的參數值。 （位置參數沒有預設值，因此您需要提供一個。）

`.\Deploy-AzureResourceGroup.ps1 -ResourceGroupLocation eastus`

### <a name="deploy-overriding-the-default-values"></a>部署覆寫預設值

此範例會示範如何執行指令碼部署範本參數及參數值與預設值不同的檔案。

```
.\Deploy-AzureResourceGroup.ps1 -ResourceGroupLocation eastus –TemplateFile ..\templates\AnotherTemplate.json –TemplateParametersFile ..\templates\AnotherTemplate.parameters.json
```

### <a name="deploy-using-uploadartifacts-for-staging"></a>部署 UploadArtifacts 用臨時

此範例顯示上傳成品，從 [發行] 資料夾，並部署非預設範本中的指令碼的執行方式。

```
.\Deploy-AzureResourceGroup.ps1 -StorageAccountName 'mystorage' -StorageAccountResourceGroupName 'Default-Storage-EastUS' -ResourceGroupName 'myResourceGroup' -ResourceGroupLocation 'eastus' -TemplateFile '..\templates\windowsvirtualmachine.json' -TemplateParametersFile '..\templates\windowsvirtualmachine.parameters.json' -UploadArtifacts -ArtifactStagingDirectory ..\bin\release\staging
```

此範例會示範如何在 Visual Studio Online 中的 PowerShell 的 Azure 任務執行指令碼。

```
$(Build.StagingDirectory)/AzureResourceGroup1/Scripts/Deploy-AzureResourceGroup.ps1 -StorageAccountName 'mystorage' -StorageAccountResourceGroupName 'Default-Storage-EastUS' -ResourceGroupName 'myResourceGroup' -ResourceGroupLocation 'eastus' -TemplateFile '..\templates\windowsvirtualmachine.json' -TemplateParametersFile '..\templates\windowsvirtualmachine.parameters.json' -UploadArtifacts -ArtifactStagingDirectory $(Build.StagingDirectory)
```

## <a name="next-steps"></a>後續步驟
閱讀[Azure 資源管理員的概觀](azure-resource-manager/resource-group-overview.md)，瞭解更多關於 Azure 資源管理員。

更多範例使用 Azure 資源群組專案的詳細資訊，請參閱[部署及管理 Azure 資源](https://github.com/Microsoft/HealthClinic.biz/wiki/Deploy-and-manage-Azure-resources)從[HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) 2015 連線[示範](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/)。 從 HealthClinic.biz 示範更多的快速入門，請參閱[Azure 開發人員工具快速入門](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts)。

[0]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy1c.png
[1]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy2bc.png
[2]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy3bc.png
[3]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy4bc.png
[4]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy5c.png
[5]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy6c.png
