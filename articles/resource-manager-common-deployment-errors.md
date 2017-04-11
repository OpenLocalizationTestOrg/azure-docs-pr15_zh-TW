<properties
   pageTitle="疑難排解常見 Azure 部署錯誤 |Microsoft Azure"
   description="說明如何使用 Azure 資源管理員 Azure 部署資源時，解決常見錯誤。"
   services="azure-resource-manager"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"
   keywords="部署錯誤，azure 部署中，將 azure 部署"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="tomfitz"/>

# <a name="troubleshoot-common-azure-deployment-errors-with-azure-resource-manager"></a>疑難排解常見 Azure 部署錯誤與 Azure 資源管理員

本主題說明如何解決一些常見您可能會遇到的 Azure 部署錯誤。 如果您需要錯在哪裡搭配您的部署的詳細資訊，請先參閱[檢視部署作業](resource-manager-troubleshoot-deployments-portal.md)，然後回到協助您解決錯誤的本文。 本主題中的區段列出您看到錯誤碼。

## <a name="invalid-template"></a>無效的範本

此錯誤可能會造成從數種不同類型的錯誤。 

### <a name="syntax-error"></a>語法錯誤

如果您收到錯誤訊息，指出範本無法驗證，您可能必須在範本中的語法問題。 

    Code=InvalidTemplate 
    Message=Deployment template validation failed

此錯誤是很容易因為範本運算式可以錯綜複雜。 例如，下列儲存體帳戶名稱工作分派包含一組括弧括住、 三個函數、 三組括號括住的、 一組單引號括住及一個屬性︰

    "name": "[concat('storage', uniqueString(resourceGroup().id))]",

如果您不提供相符的語法，範本便會產生不同於您的值。

當您收到此類型的錯誤時，請仔細檢閱運算式語法。 請考慮使用 JSON 編輯器[Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)或[Visual Studio 程式碼](resource-manager-vs-code.md)，它可以告訴您語法錯誤等。 

### <a name="incorrect-segment-lengths"></a>不正確的區段長度

不正確的格式的資源名稱時，就會發生另一個不正確的範本錯誤。

    Code=InvalidTemplate
    Message=Deployment template validation failed: 'The template resource {resource-name}' 
    for type {resource-type} has incorrect segment lengths.

根層級的資源必須擁有一個較少的區段中比中的資源類型的名稱。 每個區段是以斜線區分。 在下列範例中，類型有兩個區段，而且名稱有一個區段中，讓它是**有效的名稱**。

    {
      "type": "Microsoft.Web/serverfarms",
      "name": "myHostingPlanName",
      ...
    }

但下一個範例**不是有效的名稱**，因為它數相同的區段類型。

    {
      "type": "Microsoft.Web/serverfarms",
      "name": "appPlan/myHostingPlanName",
      ...
    }

子資源的名稱與類型有數相同的區段。 這個數字的區段的意義，因為的完整名稱及子類型包含的上層名稱與類型。 因此，完整的名稱仍會有一個較少區段比完整的類型。 

    "resources": [
        {
            "type": "Microsoft.KeyVault/vaults",
            "name": "contosokeyvault",
            ...
            "resources": [
                {
                    "type": "secrets",
                    "name": "appPassword",
                    ...
                }
            ]
        }
    ]

取得正確的區段可能有點難度會套用在資源提供者的資源管理員類型。 例如，套用至網站的資源鎖定所需要的四個線段的類型。 因此，名稱為三個區段︰

    {
        "type": "Microsoft.Web/sites/providers/locks",
        "name": "[concat(variables('siteName'),'/Microsoft.Authorization/MySiteLock')]",
        ...
    }

### <a name="copy-index-is-not-expected"></a>不應該複製索引

當您**複製**項目套用至不支援此元素的範本組件，就會發生此**InvalidTemplate**錯誤。 您可以只適用於資源類型進行複製項目。 您無法將複製套用至內的資源類型的屬性。 例如，您將複製套用至虛擬機器，但您無法將它套用至虛擬機器 OS 磁碟。 在某些情況下，您可以將子資源轉換成父項資源來建立複製循環播放。 如需有關使用複製的詳細資訊，請參閱[建立資源 Azure 資源管理員] 中的多個執行個體](resource-group-create-multiple.md)。

### <a name="parameter-is-not-valid"></a>無效的參數

如果範本指定允許的值參數，且您提供的不是這些值的其中一個值，您會收到下列錯誤訊息︰

    Code=InvalidTemplate; 
    Message=Deployment template validation failed: 'The provided value {parameter vaule} 
    for the template parameter {parameter name} is not valid. The parameter value is not 
    part of the allowed value(s)

雙核取允許的值，在範本，並提供在部署期間。

## <a name="not-found-or-resource-not-found"></a>找不到 （或找不到資源）

當您的範本包含無法解決資源的名稱時，您會收到錯誤類似︰

    Code=NotFound;
    Message=Cannot find ServerFarm with name exampleplan.

如果您正嘗試部署遺失資源範本中的，核取您要新增相依性。 資源管理員藉由建立資源平行，盡可能最佳化部署。 如果某個資源必須部署另一個資源之後，您需要在範本中使用**dependsOn**項目，建立其他的資源的相依性。 例如，在部署 web 應用程式時，必須已經存在的應用程式服務方案。 如果您不指定 web 應用程式，而定的應用程式服務方案，資源管理員會同時建立兩個資源。 您收到錯誤訊息指出的應用程式服務方案資源找不到，因為它尚不存在當您嘗試在 web 應用程式上設定屬性時。 您可以設定 web 應用程式中的相依性，以防止這個錯誤。

    {
      "apiVersion": "2015-08-01",
      "type": "Microsoft.Web/sites",
      ...
      "dependsOn": [
        "[variables('hostingPlanName')]"
      ],
      ...
    }
 
當資源存在於比部署至不同的資源群組中，也會看到這個錯誤。 在此情況下，使用[預設函數](resource-group-template-functions.md#resourceid)來取得完整的資源名稱。

    "properties": {
        "name": "[parameters('siteName')]",
        "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
    } 

如果您嘗試使用[參照](resource-group-template-functions.md#referenc)或[listKeys](resource-group-template-functions.md#listkeys)函數與資源無法解析，您會收到下列錯誤︰

    Code=ResourceNotFound;
    Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource 
    group {resource group name} was not found.

尋找包含**參考**函數的運算式。 檢查參數值正確無誤。

## <a name="storage-account-already-exists-or-already-taken"></a>儲存帳戶已存在 （或已經存在）

儲存帳戶，您必須提供的資源，透過 Azure 都是唯一的名稱。 如果您不提供一個唯一的名稱，您會收到錯誤，例如︰

    Code=StorageAccountAlreadyTaken 
    Message=The storage account named mystorage is already taken.

您可以建立唯一的名稱來造句[uniqueString](resource-group-template-functions.md#uniquestring)函數的結果與您命名慣例。

    "name": "[concat('contosostorage', uniqueString(resourceGroup().id))]",
    "type": "Microsoft.Storage/storageAccounts",

如果您在您的訂閱，組織中部署使用現有的儲存空間帳戶的相同名稱的儲存空間帳戶，但提供不同的位置，您會收到的錯誤訊息的儲存空間帳戶已存在在不同的位置。 刪除現有的儲存空間帳戶，或提供現有的儲存空間帳戶的相同位置。

## <a name="account-name-invalid"></a>不正確的帳戶名稱

當您嘗試提供儲存體帳戶名稱，其中包含禁止字元時，您會看到**AccountNameInvalid**錯誤。 儲存體帳戶名稱必須介於 3 和 24 個字元，並使用數字和只有英文小寫字母。

## <a name="no-registered-provider-found"></a>找到沒有註冊提供者

在部署資源時，您可能會收到下列錯誤與訊息︰

    Code: NoRegisteredProviderFound
    Message: No registered resource provider found for location {ocation} 
    and API version {api-version} for type {resource-type}.

您會收到此錯誤的三個原因︰

1. 資源類型不支援的位置
2. 不支援的資源類型的 API 版本
3. 資源提供者尚未註冊您的訂閱

錯誤訊息，應該能支援的位置和 API 版本的建議。 您可以變更您的範本，其中一個建議的值。 大部分提供者會自動以 Azure 入口網站或您使用的命令列介面註冊但非全部。 如果您沒有使用特定的資源提供者之前，您可能需要註冊的提供者。 您可以瞭解更多關於透過 PowerShell 或 Azure CLI 資源提供者。

### <a name="powershell"></a>PowerShell

若要查看您註冊的狀態，請使用**取得 AzureRmResourceProvider**。

    Get-AzureRmResourceProvider -ListAvailable

若要註冊提供者，請使用**Register AzureRmResourceProvider**並提供您想要註冊資源提供者的名稱。

    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Cdn

若要取得特定類型的資源支援的位置，請使用︰

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations

若要取得特定類型的資源所支援的 API 版本，請使用︰

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions

### <a name="azure-cli"></a>Azure CLI

若要查看是否已登錄提供者，請使用`azure provider list`] 命令。

    azure provider list

若要註冊資源提供者，請使用`azure provider register`命令，以及指定登錄*命名空間*。

    azure provider register Microsoft.Cdn

若要為資源提供者，請參閱支援的位置和 API 版本，請使用︰

    azure provider show -n Microsoft.Compute --json > compute.json

## <a name="operation-not-allowed"></a>不允許作業

部署超過配額] 中，這可能是每個資源群組、 訂閱、 帳戶及其他範圍時，您可能會有問題。 例如，您的訂閱可能被設定為限制核心區域的數量。 如果您嘗試部署允許數量的更多核心的虛擬機器，您會收到錯誤訊息指出已超出配額。
完成的配額資訊，請參閱[Azure 訂閱及服務限制，配額和限制式](azure-subscription-service-limits.md)。

若要檢查您的訂閱配額核心，您可以使用`azure vm list-usage`Azure CLI 中的命令。 下列範例說明免費的試用帳戶的核心配額是 4:

    azure vm list-usage

傳回的︰

    info:    Executing command vm list-usage
    Location: westus
    data:    Name   Unit   CurrentValue  Limit
    data:    -----  -----  ------------  -----
    data:    Cores  Count  0             4
    info:    vm list-usage command OK

如果您部署建立四個以上的核心西美國地區的範本，您會收到如下所示的部署錯誤︰

    Code=OperationNotAllowed
    Message=Operation results in exceeding quota limits of Core. 
    Maximum allowed: 4, Current in use: 4, Additional requested: 2.

或在 PowerShell 中，您可以使用**取得 AzureRmVMUsage**指令程式。

    Get-AzureRmVMUsage

傳回的︰

    ...
    CurrentValue : 0
    Limit        : 4
    Name         : {
                     "value": "cores",
                     "localizedValue": "Total Regional Cores"
                   }
    Unit         : null
    ...

在下列情況下，您應該前往入口網站，並將您想要部署區域的配額的支援問題的檔案。

> [AZURE.NOTE] 請記住，資源群組，每個個別的區域，不適用於整個訂閱的配額。 如果您需要部署中西美國 30 核心，必須在 [美國西 30 資源管理員核心要求。 如果您需要部署中的任何的區域中，您有權存取的 30 核心，您應該要求所有區域的 30 資源管理員核心。

## <a name="invalid-content-link"></a>無效的內容連結

當您收到錯誤訊息︰

    Code=InvalidContentLink
    Message=Unable to download deployment content from ...

您最有可能嘗試將無法使用巢狀範本的連結。 點兩下核取您所提供的巢狀範本的 URI。 如果儲存帳戶檔案，請確定 URI 存取。 您可能需要將 SA 權杖傳遞。 如需詳細資訊，請參閱[使用連結的範本與 Azure 資源管理員](resource-group-linked-templates.md)。

## <a name="authorization-failed"></a>授權失敗

您可能會收到錯誤部署時，因為帳戶或服務本金嘗試部署資源沒有存取執行這些動作。 Azure Active Directory 可讓您或您控制的身分識別的系統管理員可以存取哪些資源好精準的程度。 例如，如果您的帳戶已指派給讀者角色，您無法建立資源。 在此情況下，您會看到錯誤訊息，指出授權失敗。

如需有關角色型存取控制的詳細資訊，請參閱[Azure Role-Based Access 控制項](./active-directory/role-based-access-control-configure.md)。

除了角色型存取控制您的部署動作可能會受限於原則訂閱。 透過原則，管理員可以強制執行部署訂閱中的所有資源的慣例。 例如，系統管理員可以要求特定標籤值提供的資源類型。 如果您不符合原則需求，您會收到錯誤部署期間。 如需有關原則的詳細資訊，請參閱[使用原則管理資源，並控制存取權](resource-manager-policy.md)。

## <a name="troubleshooting-virtual-machines"></a>疑難排解虛擬機器

| 錯誤 | 文件 |
| -------- | ----------- |
| 自訂指令碼副檔名錯誤 | [Windows VM 副檔名失敗](./virtual-machines/virtual-machines-windows-extensions-troubleshoot.md)<br />或<br />[Linux VM 副檔名失敗](./virtual-machines/virtual-machines-linux-extensions-troubleshoot.md) |
| 佈建錯誤的 OS 圖像 | [新的 Windows VM 錯誤](./virtual-machines/virtual-machines-windows-troubleshoot-deployment-new-vm.md)<br />或<br />[新的 Linux VM 錯誤](./virtual-machines/virtual-machines-linux-troubleshoot-deployment-new-vm.md ) |
| 配置失敗 | [Windows VM 配置失敗](./virtual-machines/virtual-machines-windows-allocation-failure.md)<br />或<br />[Linux VM 配置失敗](./virtual-machines/virtual-machines-linux-allocation-failure.md) |
| 當您嘗試連線時安全命令介面 (SSH) 錯誤 | [安全的命令介面連線，Linux vm](./virtual-machines/virtual-machines-linux-troubleshoot-ssh-connection.md) |
| 連線到 VM 上執行應用程式的錯誤 | [在 Windows VM 上執行應用程式](./virtual-machines/virtual-machines-windows-troubleshoot-app-connection.md)<br />或<br />[Linux VM 上執行應用程式](./virtual-machines/virtual-machines-linux-troubleshoot-app-connection.md) |
| 遠端桌面連線錯誤 | [Windows VM 遠端桌面連線](./virtual-machines/virtual-machines-windows-troubleshoot-rdp-connection.md) |
| 解決重新部署連線錯誤 | [部署至新的 Azure 節點的虛擬機器](./virtual-machines/virtual-machines-windows-redeploy-to-new-node.md) |
| 雲端服務的錯誤 | [雲端服務部署問題](./cloud-services/cloud-services-troubleshoot-deployment-problems.md) |

## <a name="troubleshooting-other-services"></a>疑難排解其他服務

下表不 Azure 疑難排解主題的完整清單。 不過，它著重於部署或設定資源相關的問題。 如果您需要協助與資源的執行時間問題的疑難排解，請參閱 Azure 服務的文件。

| 服務 | 文件 |
| -------- | -------- |
| 自動化 | [Azure 自動化中的常見錯誤的疑難排解秘訣](./automation/automation-troubleshooting-automation-errors.md) |
| Azure 堆疊 | [Microsoft Azure 堆疊疑難排解](./azure-stack/azure-stack-troubleshooting.md) |
| Azure 堆疊 | [Web 應用程式和 Azure 堆疊](./azure-stack/azure-stack-webapps-troubleshoot-known-issues.md ) |
| 資料工廠 | [資料工廠問題進行疑難排解](./data-factory/data-factory-troubleshoot.md) |
| 服務布料的轉印圖樣 | [當您部署上 Azure 服務布料的轉印圖樣的服務疑難排解常見的問題](./service-fabric/service-fabric-diagnostics-troubleshoot-common-scenarios.md) |
| 網站復原 | [監控和疑難排解虛擬機器及實體伺服器的保護](./site-recovery/site-recovery-monitoring-and-troubleshooting.md) |
| 儲存空間 | [監控、 診斷，及疑難排解 Microsoft Azure 儲存體](./storage/storage-monitoring-diagnosing-troubleshooting.md) |
| StorSimple | [StorSimple 裝置部署問題進行疑難排解](./storsimple/storsimple-troubleshoot-deployment.md) |
| SQL 資料庫 | [疑難排解連線問題到 Azure SQL 資料庫](./sql-database/sql-database-troubleshoot-common-connection-issues.md) |
| SQL Data Warehouse | [疑難排解 Azure SQL Data Warehouse](./sql-data-warehouse/sql-data-warehouse-troubleshoot.md) |

## <a name="understand-when-a-deployment-is-ready"></a>瞭解當部署的準備

Azure 資源管理員報告成功部署時所有提供者回收部署成功。 不過，這則訊息並不代表資源群組是 「 使用中和您的使用者準備好。 」 例如，在部署可能需要下載升級、 等待的非範本的資源，或安裝複雜的指令碼。 資源管理員不知道當這些工作完成，因為他們不提供者所追蹤的活動。 在下列情況下，可能之前已可供實際使用的資源。 如此一來，您應該會部署狀態成功後，才能使用您的部署一些時間。

您可以防止 Azure 報告部署成功，不過，您可以建立自訂指令碼自訂範本。 指令碼知道如何監控系統整備整個部署，並傳回成功，使用者可互動整個部署時，才。 如果您想要確定您的副檔名是來執行，請使用**dependsOn**屬性在範本中的最後一個。

## <a name="next-steps"></a>後續步驟

- 若要進一步瞭解稽核動作，請參閱[稽核作業與資源管理員](resource-group-audit.md)。
- 若要瞭解動作，以決定錯誤部署時，請參閱[檢視部署作業](resource-manager-troubleshoot-deployments-portal.md)。
