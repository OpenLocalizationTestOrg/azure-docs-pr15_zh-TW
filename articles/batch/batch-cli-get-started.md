<properties
   pageTitle="開始使用 Azure 批次 CLI |Microsoft Azure"
   description="批次命令的快速簡介以取得 Azure CLI 管理 Azure 批次服務資源"
   services="batch"
   documentationCenter=""
   authors="mmacy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="batch"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="multiple"
   ms.workload="big-compute"
   ms.date="09/30/2016"
   ms.author="marsma"/>

# <a name="get-started-with-azure-batch-cli"></a>Azure 批次 CLI 快速入門

跨平台 Azure 命令列介面 (Azure CLI) 可讓您管理您的批次帳戶和資源，例如集區、 工作和 Linux、 Mac 和 Windows 命令 shell 中的工作。 Azure CLI 中，您可以執行與指令碼的許多您以批次 Api、 Azure 入口網站和批次 PowerShell cmdlet 執行相同的工作。

本文根據 Azure CLI 版本 0.10.5。

## <a name="prerequisites"></a>必要條件

* [安裝 Azure CLI](../xplat-cli-install.md)

* [Azure CLI 連線至您訂閱的 Azure](../xplat-cli-connect.md)

* 切換至 [**資源管理員模式**︰`azure config mode arm`

>[AZURE.TIP] 我們建議您更新您的 Azure CLI 安裝常見問題以利用其服務更新與增強功能。

## <a name="command-help"></a>命令說明

您也可以將 Azure CLI 中顯示的每個命令的說明文字`-h`為命令後的唯一選項。 例如︰

* 若要取得說明`azure`命令，請輸入︰`azure -h`
* 若要在 CLI 中取得所有批次命令的清單，請使用︰`azure batch -h`
* 若要取得建立批次帳戶的說明，請輸入︰`azure batch account create -h`

如有疑問，請使用`-h`命令列選項可在任何 Azure CLI] 命令上取得協助。

## <a name="create-a-batch-account"></a>建立批次帳戶

使用方法︰

    azure batch account create [options] <name>

範例︰

    azure batch account create --location "West US"  --resource-group "resgroup001" "batchaccount001"

使用指定的參數建立新的批次帳戶。 您必須指定一個位置，資源] 群組中和帳戶名稱。 如果您還沒有資源群組，建立一個執行`azure group create`，並指定 Azure 區域 （例如 「 西部適用於美國） 」 的其中一個`--location`選項。 例如︰

    azure group create --name "resgroup001" --location "West US"

> [AZURE.NOTE] 批次帳戶名稱中必須是唯一 Azure 帳戶會建立的區域。 它可能包含小寫英數字元，並必須是 3 24 個字元的長度。 您無法使用特殊字元，例如`-`或`_`批次帳戶名稱] 中。

### <a name="linked-storage-account-autostorage"></a>連結的儲存空間帳戶 (autostorage)

（選擇性） 您可以批次帳戶連結**一般用途**儲存帳戶，您所建立。 批次的[應用程式套件](batch-application-packages.md)」 功能會使用 blob 儲存體中連結的一般用途儲存帳戶[批次檔案慣例.NET](batch-task-output.md)文件庫一樣。 這些選用的功能將協助您部署批次工作執行時，應用程式和保存他們所產生的資料。

若要連結至新的批次帳戶現有 Azure 儲存體帳戶，您所建立，指定`--autostorage-account-id`選項。 這個選項要求儲存帳戶的完整的資源識別碼。

首先，顯示您儲存的帳戶的詳細資料︰

    azure storage account show --resource-group "resgroup001" "storageaccount001"

然後使用的**Url**值`--autostorage-account-id`選項。 [Url] 的值開頭 「 / 訂閱 / 」，並包含您的訂閱 ID 和資源的路徑，儲存帳戶︰

    azure batch account create --location "West US"  --resource-group "resgroup001" --autostorage-account-id "/subscriptions/8ffffff8-4444-4444-bfbf-8ffffff84444/resourceGroups/resgroup001/providers/Microsoft.Storage/storageAccounts/storageaccount001" "batchaccount001"

## <a name="delete-a-batch-account"></a>刪除批次帳戶

使用方法︰

    azure batch account delete [options] <name>

範例︰

    azure batch account delete --resource-group "resgroup001" "batchaccount001"

刪除特定批次帳戶。 出現提示時，確認您要移除帳戶 （移除帳戶可能需要一些時間才能完成）。

## <a name="manage-account-access-keys"></a>管理帳戶便捷鍵

您需要[建立](#create-and-modify-batch-resources)及修改資源便捷鍵批次帳戶中。

### <a name="list-access-keys"></a>清單便捷鍵

使用方法︰

    azure batch account keys list [options] <name>

範例︰

    azure batch account keys list --resource-group "resgroup001" "batchaccount001"

列出指定批次帳戶的帳戶金鑰。

### <a name="generate-a-new-access-key"></a>產生新的便捷鍵

使用方法︰

    azure batch account keys renew [options] --<primary|secondary> <name>

範例︰

    azure batch account keys renew --resource-group "resgroup001" --primary "batchaccount001"

重新產生指定批次帳戶的指定的帳戶金鑰。

## <a name="create-and-modify-batch-resources"></a>建立及修改批次資源

您可以使用 Azure CLI 來建立、 讀取、 更新和刪除 (CRUD) 批次資源集區，如、 計算節點、 工作和工作。 這些 CRUD 作業需要批次帳戶名稱、 便捷鍵，以及結束點。 您可以指定的`-a`， `-k`，及`-u`的選項或設定[環境變數](#credential-environment-variables)CLI 使用自動 （如果填入）。

### <a name="credential-environment-variables"></a>認證環境變數

您可以設定`AZURE_BATCH_ACCOUNT`， `AZURE_BATCH_ACCESS_KEY`，及`AZURE_BATCH_ENDPOINT`環境變數，而不是指定`-a`， `-k`，及`-u`您執行的每個命令的命令列上的選項。 批次 CLI 會使用這些變數 (如果設定)，好讓您可以略過`-a`， `-k`，及`-u`選項。 本文的其餘部分假設使用這些環境變數。

>[AZURE.TIP] 清單與索引鍵`azure batch account keys list`，並顯示帳戶的端點`azure batch account show`。

### <a name="json-files"></a>JSON 檔案

當您建立批次資源集區等工作時，您可以指定 JSON 檔案包含新的資源設定，而不是傳遞的參數為命令列選項。 例如︰

`azure batch pool create my_batch_pool.json`

您可以執行許多資源建立作業使用只命令列選項，某些功能需要 JSON 格式的檔案，包含資源詳細資料。 例如，您必須使用 JSON 檔案，如果您想要指定開始任務的資源檔案。

若要尋找所需建立資源 JSON，請參閱[批次 REST API 參考][ rest_api] MSDN 上的文件。 每個 [新增*資源類型*] 主題包含範例 JSON 建立的資源，您可以使用 JSON 檔案做為範本。 例如，JSON 資料庫建立的請參閱[增益集區帳戶][rest_add_pool]。

>[AZURE.NOTE] 如果您在建立資源時，您可以指定 JSON 檔案，會忽略您指定該資源的命令列的其他所有參數。

## <a name="create-a-pool"></a>建立資料庫

使用方法︰

    azure batch pool create [options] [json-file]

範例 （虛擬機器設定）︰

    azure batch pool create --id "pool001" --target-dedicated 1 --vm-size "STANDARD_A1" --image-publisher "Canonical" --image-offer "UbuntuServer" --image-sku "14.04.2-LTS" --node-agent-id "batch.node.ubuntu 14.04"

範例 （雲端服務設定）︰

    azure batch pool create --id "pool002" --target-dedicated 1 --vm-size "small" --os-family "4"

批次服務中建立運算節點集的區。

[批次功能概觀](batch-api-basics.md#pool)所述，當您在 [程式集區中選取 [節點作業系統有兩個選項︰**虛擬機器設定**和**雲端服務設定**。 使用`--image-*`建立虛擬機器設定資料庫的選項和`--os-family`建立雲端服務設定集區。 您無法指定兩者`--os-family`和`--image-*`選項。

您可以指定資料庫[應用程式套件](batch-application-packages.md)」 和 「[啟動工作](batch-api-basics.md#start-task)的命令列。 若要指定 [開始] 任務的資源檔案，不過，您必須改為使用[JSON 檔案](#json-files)。

刪除與集區︰

    azure batch pool delete [pool-id]

>[AZURE.TIP] 檢查[清單的虛擬機器圖像](batch-linux-nodes.md#list-of-virtual-machine-images)的值適用於`--image-*`選項。

## <a name="create-a-job"></a>建立工作

使用方法︰

    azure batch job create [options] [json-file]

範例︰

    azure batch job create --id "job001" --pool-id "pool001"

新增工作至批次帳戶並指定要在其執行的工作集區。

刪除工作︰

    azure batch job delete [job-id]

## <a name="list-pools-jobs-tasks-and-other-resources"></a>清單資料庫、 工作、 工作及其他資源

每個批次的資源類型支援`list`的查詢批次帳戶，並列出資源，該類型的命令。 例如，您也可以在您的帳戶和中工作的工作列出集區︰

    azure batch pool list
    azure batch task list --job-id "job001"

### <a name="listing-resources-efficiently"></a>有效率地列出資源

更快速地查詢，您可以指定的**選取**]、 [**篩選**]，並**展開**子句選項`list`作業。 若要限制傳回的資料量批次服務中使用這些選項。 所有的篩選，就會發生伺服器端，因為您感興趣的資料交叉線。 使用這些子句將儲存頻寬 （因此時間） 當您執行的作業清單。

例如，這會傳回其識別碼開頭 「 renderTask 」 的資料庫︰

    azure batch task list --job-id "job001" --filter-clause "startswith(id, 'renderTask')"

批次 CLI 支援批次服務支援所有三個子句︰

* `--select-clause [select-clause]`傳回每個實體屬性的子集
* `--filter-clause [filter-clause]`傳回符合指定的 OData 運算式的實體
* `--expand-clause [expand-clause]`取得單一基礎其餘呼叫的實體的資訊。 展開子句只支援`stats`這一次的屬性。

三個子句-及執行的清單查詢的詳細資訊，請參閱[有效率地查詢 Azure 批次服務](batch-efficient-list-queries.md)。

## <a name="application-package-management"></a>應用程式套件管理

應用程式套件提供簡化部署應用程式到您的資料庫中的計算節點。 Azure CLI 中，您可以將應用程式套件上傳、 管理套件版本和刪除套件。

若要建立新的應用程式，並新增套件版本︰

**建立**應用程式︰

    azure batch application create "resgroup001" "batchaccount001" "MyTaskApplication"

**新增**應用程式套件︰

    azure batch application package create "resgroup001" "batchaccount001" "MyTaskApplication" "1.10-beta3" package001.zip

**啟動**套件︰

    azure batch application package activate "resgroup001" "batchaccount001" "MyTaskApplication" "1.10-beta3" zip

設定應用程式的**預設版本**︰

    azure batch application set "resgroup001" "batchaccount001" "MyTaskApplication" --default-version "1.10-beta3"

### <a name="deploy-an-application-package"></a>部署應用程式套件

當您建立新的資料庫時，您可以指定一個或多個應用程式套件，以供部署。 當您在資料庫建立時指定套件時，將其部署至每個節點為節點的連接集區。 當您重新啟動或重新建立影像節點時也部署套件。

指定`--app-package-ref`當建立要將應用程式套件部署至資料庫的節點，他們加入集區資料庫] 選項。 `--app-package-ref`選項可接受的部署至運算節點的應用程式識別碼以分號分隔清單。

    azure batch pool create --pool-id "pool001" --target-dedicated 1 --vm-size "small" --os-family "4" --app-package-ref "MyTaskApplication"

當您使用的命令列選項來建立資料庫時，您目前無法指定*要部署至運算節點，例如 「 1.10-beta3 」 應用程式套件的版本*。 因此，您必須先指定與應用程式的預設版本`azure batch application set [options] --default-version <version-id>`建立資料庫之前 （請參閱前一節）。 不過，如果您將使用[JSON 檔案](#json-files)，而不是命令列選項，當您建立資料庫時，您可以指定集區的封裝版本。

您可以[使用 Azure 批次應用程式套件的應用程式部署](batch-application-packages.md)尋找應用程式套件的詳細資訊。

>[AZURE.IMPORTANT] 您必須[連結 Azure 儲存體帳戶](#linked-storage-account-autostorage)至您要使用應用程式套件的批次帳戶。

### <a name="update-a-pools-application-packages"></a>更新資料庫的應用程式套件

若要指派給現有的資料庫應用程式的更新，請`azure batch pool set`命令與`--app-package-ref`選項︰

    azure batch pool set --pool-id "pool001" --app-package-ref "MyTaskApplication2"

若要部署新的應用程式套件，來計算已在現有的資料庫中的節點，您必須重新啟動，或重新這些節點︰

    azure batch node reboot --pool-id "pool001" --node-id "tvm-3105992504_1-20160930t164509z"

>[AZURE.TIP] 您可以使用，取得清單中的節點資料庫，以及其節點識別碼， `azure batch node list`。

請記住，您必須已經設定應用程式使用預設之前版本部署 (`azure batch application set [options] --default-version <version-id>`)。

## <a name="troubleshooting-tips"></a>疑難排解提示

本節是為您提供疑難排解 Azure CLI 問題時使用的資源。 它不一定能解決所有的問題，但它可以協助您縮小原因，並指向要說明的資源。

* 使用`-h`取得**說明文字**的任何 CLI 命令

* 使用`-v`和`-vv`顯示命令輸出**詳細資訊**。`-vv` 「 額外 」 的詳細資訊，並且會顯示實際的其餘部分邀請和回應。 這些選項是很方便的顯示完整的錯誤輸出。

* 您可以檢視與**為 JSON 命令輸出**`--json`選項。 例如， `azure batch pool show "pool001" --json` JSON 格式顯示 pool001 的屬性。 您可以然後複製並修改中使用此輸出`--json-file`（請參閱本文稍早的[JSON 檔案](#json-files)）。

* [批次 MSDN 上的論壇][batch_forum]很大的幫助的資源，而密切監控以批次小組成員。 請確定有張貼您的問題，如果您遇到問題，或想要使用特定的作業的說明。

* Azure cli 目前支援不是每個批次資源的操作。 例如，您無法目前指定資料庫時，只封裝識別碼應用程式套件*版本* 在這種情況下，您可能需要提供`--json-file`命令，而不是使用命令列選項。 請務必保持最新 CLI 版本回答未來的增強功能。

## <a name="next-steps"></a>後續步驟

*  若要瞭解如何使用此功能，可用於管理及部署您批次運算節點執行應用程式的[應用程式部署 Azure 批次應用程式套件](batch-application-packages.md)，請參閱。

* 請參閱[有效率地查詢批次服務](batch-efficient-list-queries.md)更多關於減少的項目數和批次查詢傳回的資訊類型。

[batch_forum]: https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=azurebatch
[github_readme]: https://github.com/Azure/azure-xplat-cli/blob/dev/README.md
[rest_api]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx