<properties
    pageTitle="Azure 批次集區中的 Linux 節點 |Microsoft Azure"
    description="瞭解如何處理 Linux 虛擬機器中 Azure 批次的集區上您平行計算負載。"
    services="batch"
    documentationCenter="python"
    authors="mmacy"
    manager="timlt"
    editor="" />

<tags
    ms.service="batch"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="vm-linux"
    ms.workload="na"
    ms.date="09/08/2016"
    ms.author="marsma" />

# <a name="provision-linux-compute-nodes-in-azure-batch-pools"></a>佈建 Linux 運算節點 Azure 批次集區中

您可以使用 Azure 批次 Linux 和 Windows 虛擬機器上執行平行運算的工作量。 本文詳細說明如何建立資料庫的 Linux 運算節點批次服務中使用兩個[批次 Python] [py_batch_package]和[批次.NET] [api_net]用戶端文件庫。

> [AZURE.NOTE] [應用程式套件](batch-application-packages.md)目前不支援在 Linux 運算節點。

## <a name="virtual-machine-configuration"></a>虛擬機器設定

當您建立的運算節點集區批次時，您有兩個選項可選取節點大小和作業系統︰ 雲端服務設定和虛擬機器設定。

**雲端服務設定**提供 Windows 節點*只*計算。 可用作業系統會列於[Azure 來賓作業系統版本和 SDK 相容性矩陣](../cloud-services/cloud-services-guestos-update-matrix.md)和[Cloud Services 的大小](../cloud-services/cloud-services-sizes-specs.md)，會列出可用的計算節點大小。 當您建立包含 Azure Cloud Services 節點的資料庫時，必須先指定節點大小和其 「 OS 家庭，」 都可以找到先前所述的文件中。 針對 Windows 的計算節點，最常使用雲端服務。

**虛擬機器設定**提供運算節點 Linux 和 Windows 的圖像。 使用計算節點大小會列出[中 Azure 虛擬機器大小](../virtual-machines/virtual-machines-linux-sizes.md)(Linux) 和[中 Azure 虛擬機器大小](../virtual-machines/virtual-machines-windows-sizes.md)(Windows) 中。 當您建立包含虛擬機器設定節點的資料庫時，您必須指定節點、 虛擬機器圖像參考及批次節點代理程式安裝在節點的 SKU 的大小。

### <a name="virtual-machine-image-reference"></a>虛擬機器影像參照

批次服務使用[虛擬機器縮放] 設定](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)，提供 Linux 運算節點。 [Azure Marketplace]提供這些虛擬機器的作業系統圖像[vm_marketplace]。 當您設定的虛擬機器圖像參照時，您可以指定服務商場虛擬機器圖像的屬性。 當您建立的虛擬機器圖像參照時，會需要下列屬性︰

| **圖像參考屬性** | **範例** |
| ----------------- | ------------------------ |
| Publisher         | 標準                |
| 優惠             | UbuntuServer             |
| SKU               | 14.04.4-LTS              |
| 版本           | 最新                   |

> [AZURE.TIP] 您可以進一步瞭解這些屬性，以及如何在清單中[瀏覽選取 Linux 虛擬機器中與圖像 CLI 或 PowerShell 的 Azure](../virtual-machines/virtual-machines-linux-cli-ps-findimage.md)服務商場圖像。 請注意，並非所有服務商場圖像目前相容於批次。 如需詳細資訊，請參閱[節點代理程式 SKU](#node-agent-sku)。

### <a name="node-agent-sku"></a>節點代理程式 SKU

批次節點代理程式是程式集區中每個節點上執行，並提供命令控制項之間的介面節點和批次服務。 有不同的實作稱為不同作業系統的 Sku 的節點代理程式。 基本上，當您建立虛擬機器設定，您第一次指定虛擬機器圖像參照，然後指定節點代理程式安裝上的圖像。 一般而言，每個節點代理程式 SKU 都包含多個虛擬機器圖像相容。 以下是一些節點代理程式 Sku 的範例︰

* batch.node.ubuntu 14.04
* batch.node.centos 7
* batch.node.windows amd64

> [AZURE.IMPORTANT] 並非所有虛擬機器中的影像可用服務商場都是與目前提供批次節點代理程式的相容。 您必須使用批次 Sdk 清單可用的節點代理程式 Sku 與彼此相容的虛擬機器圖像。 請參閱本文稍後的詳細資訊[清單的虛擬機器圖像](#list-of-virtual-machine-images)。

## <a name="create-a-linux-pool-batch-python"></a>建立 Linux 資料庫︰ 批次 Python

下列程式碼片段示範如何使用[Microsoft Azure 批次用戶端程式庫 python] [py_batch_package]建立 Ubuntu Server 的集區運算節點。 批次 Python 模組的參考文件，請參閱[azure.batch 套件][py_batch_docs]讀取文件。

此程式碼片段建立[ImageReference] [py_imagereference]明確地指定每個屬性 （發行者，提供的服務，SKU，版本）。 在實際執行程式碼，不過，我們建議您使用[list_node_agent_skus] [py_list_skus]方法來決定，然後選取 [從可用圖像和節點代理程式 SKU 組合在執行階段。

```python
# Import the required modules from the
# Azure Batch Client Library for Python
import azure.batch.batch_service_client as batch
import azure.batch.batch_auth as batchauth
import azure.batch.models as batchmodels

# Specify Batch account credentials
account = "<batch-account-name>"
key = "<batch-account-key>"
batch_url = "<batch-account-url>"

# Pool settings
pool_id = "LinuxNodesSamplePoolPython"
vm_size = "STANDARD_A1"
node_count = 1

# Initialize the Batch client
creds = batchauth.SharedKeyCredentials(account, key)
config = batch.BatchServiceClientConfiguration(creds, base_url = batch_url)
client = batch.BatchServiceClient(config)

# Create the unbound pool
new_pool = batchmodels.PoolAddParameter(id = pool_id, vm_size = vm_size)
new_pool.target_dedicated = node_count

# Configure the start task for the pool
start_task = batchmodels.StartTask()
start_task.run_elevated = True
start_task.command_line = "printenv AZ_BATCH_NODE_STARTUP_DIR"
new_pool.start_task = start_task

# Create an ImageReference which specifies the Marketplace
# virtual machine image to install on the nodes.
ir = batchmodels.ImageReference(
    publisher = "Canonical",
    offer = "UbuntuServer",
    sku = "14.04.2-LTS",
    version = "latest")

# Create the VirtualMachineConfiguration, specifying
# the VM image reference and the Batch node agent to
# be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference = ir,
    node_agent_sku_id = "batch.node.ubuntu 14.04")

# Assign the virtual machine configuration to the pool
new_pool.virtual_machine_configuration = vmc

# Create pool in the Batch service
client.pool.add(new_pool)
```

如先前所述，我們建議您，而非建立[ImageReference] [py_imagereference]明確，您可以使用[list_node_agent_skus] [py_list_skus]以動態方式選取從目前支援的節點代理程式/Marketplace 圖像組合的方法。 下列 Python 程式碼片段會顯示這個方法的使用方式。

```python
# Get the list of node agents from the Batch service
nodeagents = client.account.list_node_agent_skus()

# Obtain the desired node agent
ubuntu1404agent = next(agent for agent in nodeagents if "ubuntu 14.04" in agent.id)

# Pick the first image reference from the list of verified references
ir = ubuntu1404agent.verified_image_references[0]

# Create the VirtualMachineConfiguration, specifying the VM image
# reference and the Batch node agent to be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference = ir,
    node_agent_sku_id = ubuntu1404agent.id)
```

## <a name="create-a-linux-pool-batch-net"></a>建立 Linux 資料庫︰ 批次.NET

下列程式碼片段示範如何使用[批次.NET] [nuget_batch_net]建立 Ubuntu Server 的集區運算節點的用戶端文件庫。 您可以找到[批次.NET 參考文件][ api_net] MSDN 上。

下列程式碼片段使用[PoolOperations][net_pool_ops]。[ListNodeAgentSkus][net_list_skus]支援服務商場圖像和節點代理程式 SKU 組合，從清單中目前選取的方法。 這項技巧建議，因為可能會隨時變更支援的組合的清單。 大多數情況下，加入受支援的組合。

```csharp
// Pool settings
const string poolId = "LinuxNodesSamplePoolDotNet";
const string vmSize = "STANDARD_A1";
const int nodeCount = 1;

// Obtain a collection of all available node agent SKUs.
// This allows us to select from a list of supported
// VM image/node agent combinations.
List<NodeAgentSku> nodeAgentSkus =
    batchClient.PoolOperations.ListNodeAgentSkus().ToList();

// Define a delegate specifying properties of the VM image
// that we wish to use.
Func<ImageReference, bool> isUbuntu1404 = imageRef =>
    imageRef.Publisher == "Canonical" &&
    imageRef.Offer == "UbuntuServer" &&
    imageRef.SkuId.Contains("14.04");

// Obtain the first node agent SKU in the collection that matches
// Ubuntu Server 14.04. Note that there are one or more image
// references associated with this node agent SKU.
NodeAgentSku ubuntuAgentSku = nodeAgentSkus.First(sku =>
    sku.VerifiedImageReferences.Any(isUbuntu1404));

// Select an ImageReference from those available for node agent.
ImageReference imageReference =
    ubuntuAgentSku.VerifiedImageReferences.First(isUbuntu1404);

// Create the VirtualMachineConfiguration for use when actually
// creating the pool
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(
        imageReference: imageReference,
        nodeAgentSkuId: ubuntuAgentSku.Id);

// Create the unbound pool object using the VirtualMachineConfiguration
// created above
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    virtualMachineSize: vmSize,
    virtualMachineConfiguration: virtualMachineConfiguration,
    targetDedicated: nodeCount);

// Commit the pool to the Batch service
pool.Commit();
```

雖然先前的程式碼片段使用[PoolOperations][net_pool_ops]。[ListNodeAgentSkus][net_list_skus]方法，以動態清單，然後選取 [從支援 （建議使用） 的圖像和節點代理程式 SKU 組合，您也可以設定[ImageReference] [net_imagereference]明確︰

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    skuId: "14.04.2-LTS",
    version: "latest");
```

## <a name="list-of-virtual-machine-images"></a>虛擬機器圖像的清單

下表列出上次更新這份文件時相容可用批次節點代理程式的服務商場虛擬機器圖像。 請務必注意此清單不明確，因為圖像和節點代理程式可能會在新增或移除任何時間。 我們建議您批次應用程式與服務一律使用[list_node_agent_skus] [ py_list_skus] (Python) 和[ListNodeAgentSkus] [ net_list_skus] (批次.NET) 決定，然後選取 [從目前使用的 sku 皆可。

> [AZURE.WARNING] 下列清單可以隨時變更。 清單，然後選取相容的虛擬機器和節點代理程式 Sku 執行您工作時，一律使用批次 Api 提供的**清單節點代理程式 SKU**方法。

| **Publisher** | **優惠** | **圖像 SKU** | **版本** | **節點代理程式 SKU 識別碼** |
| ------- | ------- | ------- | ------- | ------- |
| 標準 | UbuntuServer | 14.04.0-LTS | 最新 | batch.node.ubuntu 14.04 |
| 標準 | UbuntuServer | 14.04.1-LTS | 最新 | batch.node.ubuntu 14.04 |
| 標準 | UbuntuServer | 14.04.2-LTS | 最新 | batch.node.ubuntu 14.04 |
| 標準 | UbuntuServer | 14.04.3-LTS | 最新 | batch.node.ubuntu 14.04 |
| 標準 | UbuntuServer | 14.04.4-LTS | 最新 | batch.node.ubuntu 14.04 |
| 標準 | UbuntuServer | 14.04.5-LTS | 最新 | batch.node.ubuntu 14.04 |
| 標準 | UbuntuServer | 16.04.0-LTS | 最新 | batch.node.ubuntu 16.04 |
| Credativ | Debian | 8 | 最新 | batch.node.debian 8 |
| OpenLogic | CentOS | 7.0 | 最新 | batch.node.centos 7 |
| OpenLogic | CentOS | 7.1 | 最新 | batch.node.centos 7 |
| OpenLogic | CentOS HPC | 7.1 | 最新 | batch.node.centos 7 |
| OpenLogic | CentOS | 7.2 | 最新 | batch.node.centos 7 |
| Oracle | Oracle Linux | 7.0 | 最新 | batch.node.centos 7 |
| SUSE | openSUSE | 13.2 | 最新 | batch.node.opensuse 13.2 |
| SUSE | openSUSE 閏 | 42.1 | 最新 | batch.node.opensuse 42.1 |
| SUSE | SLES HPC | 12 | 最新 | batch.node.opensuse 42.1 |
| SUSE | SLES | 12 SP1 | 最新 | batch.node.opensuse 42.1 |
| microsoft 廣告 | 標準資料-科學 vm | 標準資料-科學 vm | 最新 | batch.node.windows amd64 |
| microsoft 廣告 | linux 資料-科學 vm | linuxdsvm | 最新 | batch.node.centos 7 |
| MicrosoftWindowsServer | WindowsServer | 2008 R2 SP1 | 最新 | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012 資料中心 | 最新 | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012 R2-資料中心 | 最新 | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | Windows Server-Technical Preview | 最新 | batch.node.windows amd64 |

## <a name="connect-to-linux-nodes"></a>連線至 Linux 節點

開發期間或疑難排解，您可能會發現需要登入您的資料庫中的節點。 不同於 Windows 運算節點，您無法連線至 Linux 節點使用遠端桌面通訊協定 (RDP)。 不過，批次服務可讓遠端連線的每個節點 SSH 存取。

下列 Python 程式碼片段建立使用者在資料庫中，這是必要遠端連線的每個節點。 接著，它會列印每個節點的安全殼層 (SSH) 連線資訊。

```python
import datetime
import getpass
import azure.batch.batch_service_client as batch
import azure.batch.batch_auth as batchauth
import azure.batch.models as batchmodels

# Specify your own account credentials
batch_account_name = ''
batch_account_key = ''
batch_account_url = ''

# Specify the ID of an existing pool containing Linux nodes
# currently in the 'idle' state
pool_id = ''

# Specify the username and prompt for a password
username = 'linuxuser'
password = getpass.getpass()

# Create a BatchClient
credentials = batchauth.SharedKeyCredentials(
    batch_account_name,
    batch_account_key
)
batch_client = batch.BatchServiceClient(
        credentials,
        base_url=batch_account_url
)

# Create the user that will be added to each node in the pool
user = batchmodels.ComputeNodeUser(username)
user.password = password
user.is_admin = True
user.expiry_time = \
    (datetime.datetime.today() + datetime.timedelta(days=30)).isoformat()

# Get the list of nodes in the pool
nodes = batch_client.compute_node.list(pool_id)

# Add the user to each node in the pool and print
# the connection information for the node
for node in nodes:
    # Add the user to the node
    batch_client.compute_node.add_user(pool_id, node.id, user)

    # Obtain SSH login information for the node
    login = batch_client.compute_node.get_remote_login_settings(pool_id,
                                                                node.id)

    # Print the connection info for the node
    print("{0} | {1} | {2} | {3}".format(node.id,
                                         node.state,
                                         login.remote_login_ip_address,
                                         login.remote_login_port))
```

以下是包含四個 Linux 節點集區的上一個程式碼的輸出範例︰

```
Password:
tvm-1219235766_1-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50000
tvm-1219235766_2-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50003
tvm-1219235766_3-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50002
tvm-1219235766_4-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50001
```

請注意，而不是密碼，您可以指定 SSH 公開金鑰節點上建立使用者時。 這由[ComputeNodeUser]上使用**ssh_public_key**參數 Python sdk，您可以在[py_computenodeuser]。 在.NET 中，這是使用[ComputeNodeUser][net_computenodeuser]。[SshPublicKey][net_ssh_key]屬性。

## <a name="pricing"></a>價格

Azure 雲端服務和 Azure 虛擬機器技術內建 azure 批次。 批次服務本身提供免費，這表示您負責僅適用於計算資源批次方案取用。 當您選擇**雲端服務設定**時，您需要付費根據[Cloud Services 價格][cloud_services_pricing]結構。 當您選擇**虛擬機器設定**時，您需要付費根據的[虛擬機器價格][vm_pricing]結構。

## <a name="next-steps"></a>後續步驟

### <a name="batch-python-tutorial"></a>批次 Python 教學課程

如何使用批次，使用 Python 更深入的教學課程，請參閱[開始使用 Azure 批次 Python 用戶端](batch-python-tutorial.md)。 其小幫手][程式碼範例][github_samples_pyclient]包含協助函數， `get_vm_config_for_distro`，顯示其他技巧，以取得虛擬機器設定。

### <a name="batch-python-code-samples"></a>批次 Python 程式碼範例

請查看其他[Python 程式碼範例][ github_samples_py] [azure-批次-範例][ github_samples] GitHub 的存放庫，告訴您如何執行一般批次作業，例如資料庫、 工作，以及建立工作的多個指令碼。 [讀我檔案][github_py_readme]的上述 Python 範例具有有關如何安裝所需的封裝的詳細資料。

### <a name="batch-forum"></a>批次論壇

[Azure 批次論壇][forum]是 MSDN 上的 [討論批次並提出問題有關服務的絕佳起點。 閱讀很有幫助 「 stickied 」 文章與張貼您的問題，您建立批次方案時，所出現。

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[cloud_services_pricing]: https://azure.microsoft.com/pricing/details/cloud-services/
[forum]: https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=azurebatch
[github_py_readme]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/README.md
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_py]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[github_samples_pyclient]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/article_samples/python_tutorial_client.py
[portal]: https://portal.azure.com
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_computenodeuser]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenodeuser.aspx
[net_imagereference]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.imagereference.aspx
[net_list_skus]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listnodeagentskus.aspx
[net_pool_ops]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.aspx
[net_ssh_key]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenodeuser.sshpublickey.aspx
[nuget_batch_net]: https://www.nuget.org/packages/Azure.Batch/
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[py_account_ops]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations
[py_azure_sdk]: https://pypi.python.org/pypi/azure
[py_batch_docs]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.html
[py_batch_package]: https://pypi.python.org/pypi/azure-batch
[py_computenodeuser]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.models.html#azure.batch.models.ComputeNodeUser
[py_imagereference]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.models.html#azure.batch.models.ImageReference
[py_list_skus]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations.list_node_agent_skus
[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/
[vm_pricing]: https://azure.microsoft.com/pricing/details/virtual-machines/
