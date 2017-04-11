<properties
    pageTitle="教學課程-快速入門 Azure 批次 Python 用戶端 |Microsoft Azure"
    description="瞭解 Azure 批次，以及如何開發批次服務使用簡單的案例的基本概念"
    services="batch"
    documentationCenter="python"
    authors="mmacy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="batch"
    ms.devlang="python"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.workload="big-compute"
    ms.date="09/27/2016"
    ms.author="marsma"/>

# <a name="get-started-with-the-azure-batch-python-client"></a>開始使用 Azure 批次 Python 用戶端

> [AZURE.SELECTOR]
- [.NET](batch-dotnet-get-started.md)
- [Python](batch-python-tutorial.md)

瞭解基本概念[Azure]批次[azure_batch]和[批次 Python] [py_azure_sdk]用戶端我們討論 Python 所撰寫的小批次應用程式。 我們看看如何兩個範例指令碼使用處理 Linux 虛擬機器在雲端，及他們與互動的方式[Azure 儲存體](./../storage/storage-introduction.md)檔案臨時與擷取平行工作負載的批次服務。 您會瞭解常見的批次應用程式工作流程和基底瞭解批次的主要元件，例如工作、 工作、 集區，並計算節點。

![批次解決方案工作流程 (basic)][11]<br/>

## <a name="prerequisites"></a>必要條件

本文假設您已熟悉 Python 和熟悉 Linux。 也會假設您無法滿足下列指定 Azure 及批次，儲存空間服務帳戶建立需求。

### <a name="accounts"></a>帳戶

- **Azure 帳戶**︰ 如果您還沒有[建立的免費 Azure 帳戶]Azure 訂閱[azure_free_account]。
- **批次帳戶**︰ 當您有 Azure 訂閱，[建立 Azure 批次帳戶](batch-account-create-portal.md)。
- **儲存帳戶**︰ 請參閱[關於 Azure 儲存體帳戶](../storage/storage-create-storage-account.md)中[建立儲存的帳戶](../storage/storage-create-storage-account.md#create-a-storage-account)。

### <a name="code-sample"></a>程式碼範例

Python 教學課程[的程式碼範例][github_article_samples]其中許多的批次的程式碼範例中找到[azure-批次-範例][ github_samples] GitHub 上的儲存機制。 您可以按一下 [下載所有範例**複製或都下載 > 都下載 ZIP**存放庫的 [首頁] 頁面，或按一下[azure 批次-範例 master.zip] [github_samples_zip]直接都下載連結。 一旦您已擷取 ZIP 檔案的內容，在中找到本教學課程中的兩個指令碼`article_samples`目錄︰

`/azure-batch-samples/Python/Batch/article_samples/python_tutorial_client.py`<br/>
`/azure-batch-samples/Python/Batch/article_samples/python_tutorial_task.py`

### <a name="python-environment"></a>Python 環境

若要在本機工作站執行*python_tutorial_client.py*範例指令碼，您需要**Python 解譯**與版本**2.7**或**3.3 +**相容。 指令碼的 Linux 和 Windows 上測試。

### <a name="cryptography-dependencies"></a>加密相依性

您必須安裝的相依性[加密][crypto]文件庫，所需`azure-batch`和`azure-storage`Python 套件。 執行下列作業適合您的平台，其中一項或參照[加密安裝][crypto_install]詳細資料，如需詳細資訊︰

* Ubuntu

    `apt-get update && apt-get install -y build-essential libssl-dev libffi-dev libpython-dev python-dev`

* CentOS

    `yum update && yum install -y gcc openssl-dev libffi-devel python-devel`

* SLES/OpenSUSE

    `zypper ref && zypper -n in libopenssl-dev libffi48-devel python-devel`

* Windows

    `pip install cryptography`

>[AZURE.NOTE] 如果安裝 Python 3.3 + Linux 上，使用 Python 相依性 python3 對應項目。 例如，在 Ubuntu:`apt-get update && apt-get install -y build-essential libssl-dev libffi-dev libpython3-dev python3-dev`

### <a name="azure-packages"></a>Azure 套件

接下來，請安裝**Azure 批次**和**Azure 儲存體**Python 套件。 您可以在這裡找到*requirements.txt* **pip**與︰

`/azure-batch-samples/Python/Batch/requirements.txt`

安裝批次，儲存空間的封裝**pip**命令的問題︰

`pip install -r requirements.txt`

或者，您可以安裝[azure 批次][pypi_batch]和[azure 儲存體][ pypi_storage] Python 手動套件︰

`pip install azure-batch`<br/>
`pip install azure-storage`

> [AZURE.TIP] 您可能需要您使用的命令加上字首`sudo`如果您使用未具權限的帳戶。 例如， `sudo pip install -r requirements.txt`。 如需有關如何安裝 Python 套件的詳細資訊，請參閱[安裝套件][ pypi_install] readthedocs.io 上。

## <a name="batch-python-tutorial-code-sample"></a>批次 Python 教學課程的程式碼範例

批次 Python 教學課程的程式碼範例包含兩個 Python 指令碼和一些資料檔案。

- **python_tutorial_client.py**︰ 運算節點 （虛擬機器） 上執行平行的工作量批次，儲存空間服務進行互動。 在本機工作站上執行， *python_tutorial_client.py*指令碼。

- **python_tutorial_task.py**︰ 在執行的指令碼計算中執行的實際工時 Azure 節點。 在範例中， *python_tutorial_task.py*剖析從 Azure 儲存空間 （輸入檔案） 下載的檔案中的文字。 然後，它會包含出現在輸入的檔案中的前三個單字的清單的文字檔案 （輸出檔案）。 建立輸出檔案之後， *python_tutorial_task.py*上傳的檔案至 Azure 儲存體。 這樣便可供下載到您工作站上執行的用戶端指令碼。 批次服務中的多個運算節點平行在執行*python_tutorial_task.py*指令碼。

- **./data/taskdata\*.txt**︰ 以下三個文字檔案提供輸入運算節點執行的工作。

下圖顯示執行的用戶端與任務的指令碼的主索引作業。 這項基本的工作流程是一般的許多會以批次來建立的計算解決方案。 時，不會示範每個批次服務中使用的功能，幾乎批次案例包含此工作流程的部分。

![工作流程範例批次][8]<br/>

[**步驟 1。**](#step-1-create-storage-containers) 建立**容器**中 Azure Blob 儲存體。<br/>
[**步驟 2。**](#step-2-upload-task-script-and-data-files) 上傳工作指令碼，並輸入檔案至容器。<br/>
[**步驟 3。**](#step-3-create-batch-pool) 建立批次**資料庫**。<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**3a。** 集區**StartTask**節點下載任務指令碼 (python_tutorial_task.py)，他們加入集區。<br/>
[**步驟 4。**](#step-4-create-batch-job) 建立批次**工作**。<br/>
[**步驟 5。**](#step-5-add-tasks-to-job) 將**任務**新增至工作。<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**5a。** 若要在節點上執行任務。<br/>
    &nbsp;&nbsp;&nbsp;&nbsp;**5b。** 每個任務從 Azure 儲存空間，下載輸入的資料，然後開始執行。<br/>
[**步驟 6。**](#step-6-monitor-tasks) 監視工作。<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**6a。** 工作完成後，其上傳其輸出資料至 Azure 儲存體。<br/>
[**步驟 7。**](#step-7-download-task-output) 下載儲存工作成果。

所述，不是每個批次解決方案會執行這些確切的步驟，以及可能包含許多其他，但此範例會示範批次解決方案中找到的一般程序。

## <a name="prepare-client-script"></a>準備用戶端指令碼

執行範例之前，請將*python_tutorial_client.py*您批次，儲存空間的帳戶認證。 如果您尚未執行此動作您最愛的編輯器中開啟檔案，然後更新下列幾行與您的認證。

```python
# Update the Batch and Storage account credential strings below with the values
# unique to your accounts. These are used when constructing connection strings
# for the Batch and Storage client objects.

# Batch account credentials
batch_account_name = "";
batch_account_key  = "";
batch_account_url  = "";

# Storage account credentials
storage_account_name = "";
storage_account_key  = "";
```

您可以在[Azure 入口網站]尋找您批次，儲存空間的帳戶認證中的每個服務帳戶刀[azure_portal]:

![批次入口網站中的認證][9]
![入口網站中的儲存空間認證][10]<br/>

在下列區段中，我們分析指令碼處理工作負載批次服務中的步驟。 我們建議您參照定期編輯器中的指令碼時您處理的文件其餘部分。

瀏覽至**python_tutorial_client.py**開始步驟 1 中的下列行︰

```python
if __name__ == '__main__':
```

## <a name="step-1-create-storage-containers"></a>步驟 1︰ 建立存放容器

![Azure 儲存體中建立容器][1]
<br/>

批次包含內建支援互動 Azure 儲存體。 在您儲存帳戶的容器會提供批次帳戶中執行的工作所需的檔案。 容器也會提供儲存工作產生的輸出資料的位置。 首先*python_tutorial_client.py*指令碼會執行的是[Azure Blob 儲存體](../storage/storage-introduction.md#blob-storage)中建立三個容器︰

- **應用程式**︰ 此容器會儲存執行的工作， *python_tutorial_task.py*Python 指令碼。
- **輸入**︰ 工作會下載至安裝程序*輸入*容器的資料檔案。
- **成果**︰ 輸入的檔案處理工作完成，他們會將結果上載至*輸出*容器。

以互動的儲存空間帳戶，並建立容器，我們使用[azure 儲存體][pypi_storage]套件建立[BlockBlobService] [py_blockblobservice]物件-「 blob 用戶端]。 然後，我們會建立三個容器中使用 blob 用戶端的儲存空間帳戶。

```python
 # Create the blob client, for use in obtaining references to
 # blob storage containers and uploading files to containers.
 blob_client = azureblob.BlockBlobService(
     account_name=_STORAGE_ACCOUNT_NAME,
     account_key=_STORAGE_ACCOUNT_KEY)

 # Use the blob client to create the containers in Azure Storage if they
 # don't yet exist.
 app_container_name = 'application'
 input_container_name = 'input'
 output_container_name = 'output'
 blob_client.create_container(app_container_name, fail_on_exist=False)
 blob_client.create_container(input_container_name, fail_on_exist=False)
 blob_client.create_container(output_container_name, fail_on_exist=False)
```

只要容器建立之後，應用程式現在可以上傳的工作會使用檔案。

> [AZURE.TIP] [如何使用 Azure Blob 儲存體，從 Python](../storage/storage-python-how-to-use-blob-storage.md)提供使用 Azure 儲存體容器和 blob 的概觀。 當您以批次開始使用應該會讀取清單頂端附近。

## <a name="step-2-upload-task-script-and-data-files"></a>步驟 2︰ 上傳工作的指令碼和資料檔案

![上傳至容器的任務應用程式及輸入 （資料） 檔案][2]
<br/>

在檔案上傳作業， *python_tutorial_client.py*先定義**應用程式**，並**輸入**檔案路徑的集合存在於本機電腦上。 然後將其上傳這些檔案的容器的上一個步驟所建立。

```python
 # Paths to the task script. This script will be executed by the tasks that
 # run on the compute nodes.
 application_file_paths = [os.path.realpath('python_tutorial_task.py')]

 # The collection of data files that are to be processed by the tasks.
 input_file_paths = [os.path.realpath('./data/taskdata1.txt'),
                     os.path.realpath('./data/taskdata2.txt'),
                     os.path.realpath('./data/taskdata3.txt')]

 # Upload the application script to Azure Storage. This is the script that
 # will process the data files, and is executed by each of the tasks on the
 # compute nodes.
 application_files = [
     upload_file_to_container(blob_client, app_container_name, file_path)
     for file_path in application_file_paths]

 # Upload the data files. This is the data that will be processed by each of
 # the tasks executed on the compute nodes in the pool.
 input_files = [
     upload_file_to_container(blob_client, input_container_name, file_path)
     for file_path in input_file_paths]
```

使用清單理解`upload_file_to_container`函數呼叫的集合和兩個[ResourceFile]中每個檔案[py_resource_file]集合已填妥。 `upload_file_to_container`下方會出現函數︰

```
def upload_file_to_container(block_blob_client, container_name, file_path):
    """
    Uploads a local file to an Azure Blob storage container.

    :param block_blob_client: A blob service client.
    :type block_blob_client: `azure.storage.blob.BlockBlobService`
    :param str container_name: The name of the Azure Blob storage container.
    :param str file_path: The local path to the file.
    :rtype: `azure.batch.models.ResourceFile`
    :return: A ResourceFile initialized with a SAS URL appropriate for Batch
    tasks.
    """
    blob_name = os.path.basename(file_path)

    print('Uploading file {} to container [{}]...'.format(file_path,
                                                          container_name))

    block_blob_client.create_blob_from_path(container_name,
                                            blob_name,
                                            file_path)

    sas_token = block_blob_client.generate_blob_shared_access_signature(
        container_name,
        blob_name,
        permission=azureblob.BlobPermissions.READ,
        expiry=datetime.datetime.utcnow() + datetime.timedelta(hours=2))

    sas_url = block_blob_client.make_blob_url(container_name,
                                              blob_name,
                                              sas_token=sas_token)

    return batchmodels.ResourceFile(file_path=blob_name,
                                    blob_source=sas_url)
```

### <a name="resourcefiles"></a>ResourceFiles

[ResourceFile] [py_resource_file]提供批次中之前執行工作時，會下載至運算節點的 Azure 儲存體檔案 url 中的工作。 [ResourceFile][py_resource_file]。**blob_source**屬性存在於 Azure 儲存體中，指定檔案的完整 URL。 URL 也可能會包含共用的 access 簽章 (SA)，提供安全存取檔案。 大部分的任務類型批次中包含*ResourceFiles*屬性時，包括︰

- [CloudTask][py_task]
- [StartTask][py_starttask]
- [JobPreparationTask][py_jobpreptask]
- [JobReleaseTask][py_jobreltask]

此範例中不使用 JobPreparationTask 或 JobReleaseTask 任務類型，但您可以閱讀更多關於中[執行作業準備及完成工作 Azure 批次運算節點](batch-job-prep-release.md)。

### <a name="shared-access-signature-sas"></a>共用的 access 簽章 (SA)

共用的 access 簽章會提供安全存取容器和 Azure 儲存體中的二進位大型物件的字串。 *Python_tutorial_client.py*指令碼會使用這兩個 blob 與容器共用 access 簽章]，並示範如何取得這些共用的 access 簽章字串從儲存服務。

- **Blob 共用 access 簽章**︰ 集區 StartTask 用途 blob 共用的 access 簽章，將其從儲存空間下載任務指令碼，並輸入資料檔時 （請參閱[步驟 3](#step-3-create-batch-pool) ）。 `upload_file_to_container`函數*python_tutorial_client.py*中的包含的程式碼會在每個 blob 共用的 access 簽章。 它是藉由撥[BlockBlobService.make_blob_url] [py_make_blob_url]儲存模組中。

- **容器的共用的存取簽章**︰ 為每個任務的完成運算節點的工作，將其上傳的輸出檔案*輸出*中容器 Azure 儲存體。 若要這麼做， *python_tutorial_task.py*會使用容器共用的 access 簽章所提供的容器寫入存取權。 `get_container_sas_token`中*python_tutorial_client.py*函數會取得容器的共用的 access 簽名]，然後傳遞給任務的命令列引數。 步驟 5，[新增工作至工作](#step-5-add-tasks-to-job)，討論容器 SA 的使用方式。

> [AZURE.TIP] 核取 [共用的 access 簽章，兩個部分系列[第 1 部分︰ 了解 SA 模型](../storage/storage-dotnet-shared-access-signature-part-1.md)和[第 2 部分︰ 建立及使用 SA Blob 服務](../storage/storage-dotnet-shared-access-signature-part-2.md)，若要進一步瞭解提供安全存取您儲存的帳戶中的資料。

## <a name="step-3-create-batch-pool"></a>步驟 3︰ 建立批次資料庫

![建立批次資料庫][3]
<br/>

批次**集區**是在其批次執行工作的工作的運算節點 （虛擬機器） 的集合。

它上傳至儲存帳戶的任務指令碼和資料檔案之後， *python_tutorial_client.py*與批次服務會使用啟動批次 Python 模組。 若要這麼做，請[BatchServiceClient] [py_batchserviceclient]建立︰

```python
 # Create a Batch service client. We'll now be interacting with the Batch
 # service in addition to Storage.
 credentials = batchauth.SharedKeyCredentials(_BATCH_ACCOUNT_NAME,
                                              _BATCH_ACCOUNT_KEY)

 batch_client = batch.BatchServiceClient(
     credentials,
     base_url=_BATCH_ACCOUNT_URL)
```

接下來，請在通話批次帳戶中建立運算節點的集區`create_pool`。

```python
def create_pool(batch_service_client, pool_id,
                resource_files, publisher, offer, sku):
    """
    Creates a pool of compute nodes with the specified OS settings.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str pool_id: An ID for the new pool.
    :param list resource_files: A collection of resource files for the pool's
    start task.
    :param str publisher: Marketplace image publisher
    :param str offer: Marketplace image offer
    :param str sku: Marketplace image sku
    """
    print('Creating pool [{}]...'.format(pool_id))

    # Create a new pool of Linux compute nodes using an Azure Virtual Machines
    # Marketplace image. For more information about creating pools of Linux
    # nodes, see:
    # https://azure.microsoft.com/documentation/articles/batch-linux-nodes/

    # Specify the commands for the pool's start task. The start task is run
    # on each node as it joins the pool, and when it's rebooted or re-imaged.
    # We use the start task to prep the node for running our task script.
    task_commands = [
        # Copy the python_tutorial_task.py script to the "shared" directory
        # that all tasks that run on the node have access to.
        'cp -r $AZ_BATCH_TASK_WORKING_DIR/* $AZ_BATCH_NODE_SHARED_DIR',
        # Install pip and the dependencies for cryptography
        'apt-get update',
        'apt-get -y install python-pip',
        'apt-get -y install build-essential libssl-dev libffi-dev python-dev',
        # Install the azure-storage module so that the task script can access
        # Azure Blob storage
        'pip install azure-storage']

    # Get the node agent SKU and image reference for the virtual machine
    # configuration.
    # For more information about the virtual machine configuration, see:
    # https://azure.microsoft.com/documentation/articles/batch-linux-nodes/
    sku_to_use, image_ref_to_use = \
        common.helpers.select_latest_verified_vm_image_with_node_agent_sku(
            batch_service_client, publisher, offer, sku)

    new_pool = batch.models.PoolAddParameter(
        id=pool_id,
        virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
            image_reference=image_ref_to_use,
            node_agent_sku_id=sku_to_use),
        vm_size=_POOL_VM_SIZE,
        target_dedicated=_POOL_NODE_COUNT,
        start_task=batch.models.StartTask(
            command_line=
            common.helpers.wrap_commands_in_shell('linux', task_commands),
            run_elevated=True,
            wait_for_success=True,
            resource_files=resource_files),
        )

    try:
        batch_service_client.pool.add(new_pool)
    except batchmodels.batch_error.BatchErrorException as err:
        print_batch_exception(err)
        raise
```

當您建立資料庫時，您定義[PoolAddParameter] [ py_pooladdparam] ，指定集區的數個屬性︰

- **識別碼**的資料庫 (*id* -必要)<p/>就跟批次的大部分項目，將新的資料庫必須您批次帳戶內的唯一識別碼。 使用其識別碼，此集區是指您的程式碼並識別 Azure[入口網站]中的資料庫的方法是[azure_portal]。

- **運算節點的數字**(*target_dedicated* -必要)<p/>這個屬性會指定多少 Vm 應該部署集區中。 請務必注意批次的所有帳戶都有限制的**核心**（以及，運算節點） 數批次帳戶預設**配額**。 您可以在[配額和 Azure 批次服務限制](batch-quota-limit.md)找到的預設配額和指示[增加配額](batch-quota-limit.md#increase-a-quota)（例如核心批次帳戶中的最大的數字）。 如果您發現要求 「 為何無法我資料庫達到多個 X 節點？ 」 原因可能是此核心配額。

- **作業系統**節點 (*virtual_machine_configuration* **或** *cloud_service_configuration* -必要)<p/>在*python_tutorial_client.py*，我們建立使用[VirtualMachineConfiguration]Linux 節點的集區[py_vm_config]。 `select_latest_verified_vm_image_with_node_agent_sku`函數中`common.helpers`簡化使用[Azure 虛擬機器 Marketplace] [vm_marketplace]圖像。 如需有關使用的服務商場圖像，請參閱[佈建 Linux 運算 Azure 批次集區中的節點](batch-linux-nodes.md)。

- **運算節點的大小**(*vm_size* -必要)<p/>由於指定 Linux 節點的我們[VirtualMachineConfiguration][py_vm_config]，我們指定虛擬記憶體大小 (`STANDARD_A1`在這個範例中) 從[中 Azure 虛擬機器的大小](../virtual-machines/virtual-machines-linux-sizes.md)。 同樣地，請參閱[佈建 Linux 運算 Azure 批次集區中的節點](batch-linux-nodes.md)如需詳細資訊。

- **啟動工作**(*start_task* -不需要)<p/>沿著與上方實體節點屬性，您也可以指定[StartTask] [py_starttask]集區 （不需要）。 StartTask 執行每個節點的節點加入集區，以及每次重新啟動節點。 StartTask 功能的準備工作，例如安裝執行工作的應用程式的執行運算節點尤其有用。<p/>在此範例應用程式，StartTask 複製下載從儲存空間 （這指定使用 StartTask **resource_files**屬性） 的*工作目錄*StartTask*共用*目錄的節點上執行的所有任務可以都存取的檔案。 將複製基本上，`python_tutorial_task.py`每個節點上共用的目錄節點加入集區，使的節點上執行任何工作時可以存取的地方。

您可能會發現通話`wrap_commands_in_shell`協助函數。 此函數會採用不同的命令的集合，然後建立單一的命令列適用於工作的命令列] 屬性。

上述的程式碼片段顯著也是使用兩種環境中的變數 StartTask **command_line**屬性︰`AZ_BATCH_TASK_WORKING_DIR`和`AZ_BATCH_NODE_SHARED_DIR`。 批次集區中的每個運算節點會自動設定的特定批次的幾個環境變數。 任何處理程序所執行的工作有權存取這些環境變數。

> [AZURE.TIP] 若要瞭解更多有關環境變數，可在運算節點中批次資料庫，以及工作使用目錄的詳細資訊，請參閱[Azure 批次功能概觀](batch-api-basics.md)中的**任務的環境設定**] 和 [**檔案及目錄**。

## <a name="step-4-create-batch-job"></a>步驟 4︰ 建立批次工作

![建立批次工作][4]<br/>

批次**工作**的工作，集合，而相關聯的運算節點集區。 在 [相關聯的集區運算節點上，執行中工作的工作。

您可以使用不僅適用於組織及追蹤工作的相關的工作量，也可以限制特定的限制式，例如 [最大的執行階段工作 （以及由副檔名，其工作） 的工作和工作相對於批次帳戶中其他工作的優先順序。 在此範例中，不過，工作是只與步驟 3 中所建立的集區相關聯。 不設定任何其他屬性。

批次的所有工作都會與特定的資料庫相關聯。 此關聯會指出哪些節點執行的工作的工作。 您可以指定集區使用[PoolInformation] [py_poolinfo]屬性時，程式碼片段之以下所示。

```python
def create_job(batch_service_client, job_id, pool_id):
    """
    Creates a job with the specified ID, associated with the specified pool.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str job_id: The ID for the job.
    :param str pool_id: The ID for the pool.
    """
    print('Creating job [{}]...'.format(job_id))

    job = batch.models.JobAddParameter(
        job_id,
        batch.models.PoolInformation(pool_id=pool_id))

    try:
        batch_service_client.job.add(job)
    except batchmodels.batch_error.BatchErrorException as err:
        print_batch_exception(err)
        raise
```

現在，已建立工作，工作會新增至執行工作。

## <a name="step-5-add-tasks-to-job"></a>步驟 5︰ 將任務新增至工作

![將任務新增至工作][5]<br/>
*（1） 的工作會新增至工作、 （2) 任務的排程節點上執行及 （3） 工作下載處理的資料檔案*

批次**工作**是在運算節點執行工作的個別單位。 任務具有命令列，並執行指令碼或您指定的命令列中的可執行檔。

實際執行的工作，都必須工作加入工作。 每個[CloudTask] [py_task]命令列] 屬性與[ResourceFiles] [ py_resource_file] （如同使用集區 StartTask） 的節點之前自動執行的命令列下載的工作。 在範例中，每項工作程序只能有一個檔案。 因此，其 ResourceFiles 集合包含單一項目。

```python
def add_tasks(batch_service_client, job_id, input_files,
              output_container_name, output_container_sas_token):
    """
    Adds a task for each input file in the collection to the specified job.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str job_id: The ID of the job to which to add the tasks.
    :param list input_files: A collection of input files. One task will be
     created for each input file.
    :param output_container_name: The ID of an Azure Blob storage container to
    which the tasks will upload their results.
    :param output_container_sas_token: A SAS token granting write access to
    the specified Azure Blob storage container.
    """

    print('Adding {} tasks to job [{}]...'.format(len(input_files), job_id))

    tasks = list()

    for input_file in input_files:

        command = ['python $AZ_BATCH_NODE_SHARED_DIR/python_tutorial_task.py '
                   '--filepath {} --numwords {} --storageaccount {} '
                   '--storagecontainer {} --sastoken "{}"'.format(
                    input_file.file_path,
                    '3',
                    _STORAGE_ACCOUNT_NAME,
                    output_container_name,
                    output_container_sas_token)]

        tasks.append(batch.models.TaskAddParameter(
                'topNtask{}'.format(input_files.index(input_file)),
                wrap_commands_in_shell('linux', command),
                resource_files=[input_file]
                )
        )

    batch_service_client.task.add_collection(job_id, tasks)
```

> [AZURE.IMPORTANT] 當他們例如存取環境變數`$AZ_BATCH_NODE_SHARED_DIR`或執行應用程式中的節點找不到`PATH`，任務命令列必須叫用命令介面，例如與`/bin/sh -c MyTaskApplication $MY_ENV_VAR`。 這項需求不需要，如果您的工作執行應用程式中的節點`PATH`並不會參考任何環境變數。

內`for`上述的程式碼片段循環播放，您可以看到工作的命令列建構傳遞給*python_tutorial_task.py*五個命令列引數︰

1. **檔案路徑**︰ 這是本機檔案路徑為存在於節點。 ResourceFile 中的物件時`upload_file_to_container`建立在上述步驟 2] 中的檔案名稱用於此屬性 ( `file_path` ResourceFile 建構函式的參數)。 這表示檔案可以集中*python_tutorial_task.py*節點上相同的目錄。

2. **numwords**︰ 上方*N*文字應該寫入輸出檔案。

3. **storageaccount**︰ 擁有工作輸出應該上傳的容器儲存體帳戶名稱。

4. **storagecontainer**︰ 輸出檔案應該上傳存放容器的名稱。

5. **sastoken**︰ 共用的 access 的簽章 (SA) 提供**輸出**中容器 Azure 儲存體寫入存取權。 *Python_tutorial_task.py*指令碼會使用此共用的 access 簽章時建立其 BlockBlobService 參照。 這不需要便捷鍵儲存帳戶的情況下提供的容器寫入存取權。

```python
# NOTE: Taken from python_tutorial_task.py

# Create the blob client using the container's SAS token.
# This allows us to create a client that provides write
# access only to the container.
blob_client = azureblob.BlockBlobService(account_name=args.storageaccount,
                                         sas_token=args.sastoken)
```

## <a name="step-6-monitor-tasks"></a>步驟 6︰ 監控工作

![監視工作][6]<br/>
*(1) 的指令碼監視工作的工作完成狀態和工作 （2) 將結果資料上傳至 Azure 儲存體*

當工作會新增至工作時，他們會自動佇列與排程與工作相關聯的資料庫中的計算節點上執行。 根據您指定的設定，批次所有任務佇列、 排程、 重試，與其他任務管理責任會為您處理。

有許多種監控執行工作。 `wait_for_tasks_to_complete`中*python_tutorial_client.py*函數提供的監控簡單範例工作的特定狀態，在此情況下，[完成][py_taskstate]狀態。

```python
def wait_for_tasks_to_complete(batch_service_client, job_id, timeout):
    """
    Returns when all tasks in the specified job reach the Completed state.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str job_id: The id of the job whose tasks should be to monitored.
    :param timedelta timeout: The duration to wait for task completion. If all
    tasks in the specified job do not reach Completed state within this time
    period, an exception will be raised.
    """
    timeout_expiration = datetime.datetime.now() + timeout

    print("Monitoring all tasks for 'Completed' state, timeout in {}..."
          .format(timeout), end='')

    while datetime.datetime.now() < timeout_expiration:
        print('.', end='')
        sys.stdout.flush()
        tasks = batch_service_client.task.list(job_id)

        incomplete_tasks = [task for task in tasks if
                            task.state != batchmodels.TaskState.completed]
        if not incomplete_tasks:
            print()
            return True
        else:
            time.sleep(1)

    print()
    raise RuntimeError("ERROR: Tasks did not reach 'Completed' state within "
                       "timeout period of " + str(timeout))
```

## <a name="step-7-download-task-output"></a>步驟 7︰ 下載工作輸出

![從儲存下載工作輸出][7]<br/>

現在，完成工作時，可以從 Azure 儲存體下載工作的輸出。 這是呼叫`download_blobs_from_container` *python_tutorial_client.py*中︰

```python
def download_blobs_from_container(block_blob_client,
                                  container_name, directory_path):
    """
    Downloads all blobs from the specified Azure Blob storage container.

    :param block_blob_client: A blob service client.
    :type block_blob_client: `azure.storage.blob.BlockBlobService`
    :param container_name: The Azure Blob storage container from which to
     download files.
    :param directory_path: The local directory to which to download the files.
    """
    print('Downloading all files from container [{}]...'.format(
        container_name))

    container_blobs = block_blob_client.list_blobs(container_name)

    for blob in container_blobs.items:
        destination_file_path = os.path.join(directory_path, blob.name)

        block_blob_client.get_blob_to_path(container_name,
                                           blob.name,
                                           destination_file_path)

        print('  Downloaded blob [{}] from container [{}] to {}'.format(
            blob.name,
            container_name,
            destination_file_path))

    print('  Download complete!')
```

> [AZURE.NOTE] 通話`download_blobs_from_container` *python_tutorial_client.py*中指定應該下載至您的主目錄的檔案。 可以自由修改此輸出位置。

## <a name="step-8-delete-containers"></a>步驟 8︰ 刪除容器

您會位於 Azure 儲存體中的資料，因為一律是建議您先移除不再需要您批次工作的任何二進位大型物件。 在*python_tutorial_client.py*，這是與三個的來電至[BlockBlobService.delete_container][py_delete_container]:

```
# Clean up storage resources
print('Deleting containers...')
blob_client.delete_container(app_container_name)
blob_client.delete_container(input_container_name)
blob_client.delete_container(output_container_name)
```

## <a name="step-9-delete-the-job-and-the-pool"></a>步驟 9︰ 刪除的工作及資料庫

在最後一個步驟中，會提示您要刪除的工作及*python_tutorial_client.py*指令碼所建立的資料庫。 雖然您不會工作和工作，您**要為運算節點。 因此，建議您只會依需要配置節點。 刪除未使用的資料庫可以進行的維修作業程序的一部分。

BatchServiceClient [JobOperations] [py_job]和[PoolOperations] [py_pool]兩者都有相對應的刪除方法，如果您確認刪除稱為︰

```python
# Clean up Batch resources (if the user so chooses).
if query_yes_no('Delete job?') == 'yes':
    batch_client.job.delete(_JOB_ID)

if query_yes_no('Delete pool?') == 'yes':
    batch_client.pool.delete(_POOL_ID)
```

> [AZURE.IMPORTANT] 請記住，您會計算資源-刪除未使用的資料庫會最小化的成本。 此外，請注意，刪除資料庫刪除該資料庫，內的所有運算節點節點上的任何資料集區刪除之後將無法復原。

## <a name="run-the-sample-script"></a>執行範例指令碼

當您執行的*python_tutorial_client.py*指令碼的教學課程的[程式碼範例][github_article_samples]，主控台輸出會類似下列。 會出現在暫停`Monitoring all tasks for 'Completed' state, timeout in 0:20:00...`時建立資料庫的運算節點，開始與集區的 [開始] 任務中的命令會執行。 使用[Azure 入口網站][azure_portal]監控您的資料庫，計算節點、 工作和工作期間和後執行。 使用[Azure 入口網站][azure_portal]或[Microsoft Azure 儲存檔案總管][storage_explorer]若要檢視應用程式所建立的儲存空間資源 （容器和二進位大型物件）。

>[AZURE.TIP] 執行*python_tutorial_client.py*指令碼中的`azure-batch-samples/Python/Batch/article_samples`目錄。 它所使用的相對路徑`common.helpers`模組匯入]，因此您可能會看見`ImportError: No module named 'common'`如果您不在執行此目錄內的指令碼。

一般的執行時間是**5-7 分鐘左右**，當您執行預設設定的範例。

```
Sample start: 2016-05-20 22:47:10

Uploading file /home/user/py_tutorial/python_tutorial_task.py to container [application]...
Uploading file /home/user/py_tutorial/data/taskdata1.txt to container [input]...
Uploading file /home/user/py_tutorial/data/taskdata2.txt to container [input]...
Uploading file /home/user/py_tutorial/data/taskdata3.txt to container [input]...
Creating pool [PythonTutorialPool]...
Creating job [PythonTutorialJob]...
Adding 3 tasks to job [PythonTutorialJob]...
Monitoring all tasks for 'Completed' state, timeout in 0:20:00..........................................................................
  Success! All tasks reached the 'Completed' state within the specified timeout period.
Downloading all files from container [output]...
  Downloaded blob [taskdata1_OUTPUT.txt] from container [output] to /home/user/taskdata1_OUTPUT.txt
  Downloaded blob [taskdata2_OUTPUT.txt] from container [output] to /home/user/taskdata2_OUTPUT.txt
  Downloaded blob [taskdata3_OUTPUT.txt] from container [output] to /home/user/taskdata3_OUTPUT.txt
  Download complete!
Deleting containers...

Sample end: 2016-05-20 22:53:12
Elapsed time: 0:06:02

Delete job? [Y/n]
Delete pool? [Y/n]

Press ENTER to exit...
```

## <a name="next-steps"></a>後續步驟

可以自由變更*python_tutorial_client.py*和*python_tutorial_task.py*若要嘗試使用不同的計算案例。 例如，請嘗試執行延遲加*python_tutorial_task.py*模擬長時間執行工作，並監控其在入口網站。 請嘗試新增更多的工作或調整運算節點的數字。 加入邏輯檢查，並允許使用現有的集區到速度執行時間。

現在，您已經熟悉批次解決方案的基本工作流程，就深入批次服務的其他功能。

- 檢閱建議，如果您是新服務的[概觀的 Azure 批次功能](batch-api-basics.md)文件。
- 在**您採取進階的開發**[批次學習路徑]的其他批次開發文章上開始[batch_learning_path]。
- 請參閱處理中[TopNWords]批次的 「 前 n 個字 「 工作負載的不同實作[github_topnwords]範例。

[azure_batch]: https://azure.microsoft.com/services/batch/
[azure_free_account]: https://azure.microsoft.com/free/
[azure_portal]: https://portal.azure.com
[batch_learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[blog_linux]: http://blogs.technet.com/b/windowshpc/archive/2016/03/30/introducing-linux-support-on-azure-batch.aspx
[crypto]: https://cryptography.io/en/latest/
[crypto_install]: https://cryptography.io/en/latest/installation/
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_zip]: https://github.com/Azure/azure-batch-samples/archive/master.zip
[github_topnwords]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords
[github_article_samples]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch/article_samples

[nuget_packagemgr]: https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c
[nuget_restore]: https://docs.nuget.org/consume/package-restore/msbuild-integrated#enabling-package-restore-during-build

[py_account_ops]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations
[py_azure_sdk]: https://pypi.python.org/pypi/azure
[py_batch_docs]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.html
[py_batch_package]: https://pypi.python.org/pypi/azure-batch
[py_batchserviceclient]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.html#azure.batch.BatchServiceClient
[py_blockblobservice]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.blockblobservice.html#azure.storage.blob.blockblobservice.BlockBlobService
[py_cloudtask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.CloudTask
[py_computenodeuser]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.models.html#azure.batch.models.ComputeNodeUser
[py_cs_config]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.CloudServiceConfiguration
[py_delete_container]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.delete_container
[py_gen_blob_sas]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.generate_blob_shared_access_signature
[py_gen_container_sas]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.generate_container_shared_access_signature
[py_image_ref]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.ImageReference
[py_imagereference]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.models.html#azure.batch.models.ImageReference
[py_job]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.operations.html#azure.batch.operations.JobOperations
[py_jobpreptask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.JobPreparationTask
[py_jobreltask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.JobReleaseTask
[py_list_skus]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations.list_node_agent_skus
[py_make_blob_url]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.make_blob_url
[py_pool]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.operations.html#azure.batch.operations.PoolOperations
[py_pooladdparam]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.PoolAddParameter
[py_poolinfo]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.PoolInformation
[py_resource_file]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.ResourceFile
[py_samples_github]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch/
[py_starttask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.StartTask
[py_starttask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.StartTask
[py_task]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.CloudTask
[py_taskstate]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.TaskState
[py_vm_config]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.VirtualMachineConfiguration
[pypi_batch]: https://pypi.python.org/pypi/azure-batch
[pypi_storage]: https://pypi.python.org/pypi/azure-storage

[pypi_install]: http://python-packaging-user-guide.readthedocs.io/en/latest/installing/
[storage_explorer]: http://storageexplorer.com/
[visual_studio]: https://www.visualstudio.com/products/vs-2015-product-editions
[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/

[1]: ./media/batch-python-tutorial/batch_workflow_01_sm.png "Azure 儲存體中建立容器"
[2]: ./media/batch-python-tutorial/batch_workflow_02_sm.png "上傳至容器的任務應用程式及輸入 （資料） 檔案"
[3]: ./media/batch-python-tutorial/batch_workflow_03_sm.png "建立批次資料庫"
[4]: ./media/batch-python-tutorial/batch_workflow_04_sm.png "建立批次工作"
[5]: ./media/batch-python-tutorial/batch_workflow_05_sm.png "將任務新增至工作"
[6]: ./media/batch-python-tutorial/batch_workflow_06_sm.png "監視工作"
[7]: ./media/batch-python-tutorial/batch_workflow_07_sm.png "從儲存下載工作輸出"
[8]: ./media/batch-python-tutorial/batch_workflow_sm.png "批次解決方案工作流程 （完整的圖表）"
[9]: ./media/batch-python-tutorial/credentials_batch_sm.png "批次認證，在入口網站"
[10]: ./media/batch-python-tutorial/credentials_storage_sm.png "在入口網站中的儲存空間認證"
[11]: ./media/batch-python-tutorial/batch_workflow_minimal_sm.png "批次解決方案工作流程 （最小圖表）"
