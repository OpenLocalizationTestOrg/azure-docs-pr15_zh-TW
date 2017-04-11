<properties
    pageTitle="如何使用的服務管理 API (Python)-功能指南"
    description="瞭解如何以程式設計方式執行 Python 常見的服務管理工作。"
    services="cloud-services"
    documentationCenter="python"
    authors="lmazuel"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="lmazuel"/>

# <a name="how-to-use-service-management-from-python"></a>如何使用 Python 服務管理

> [AZURE.NOTE] 服務管理 API 會以新資源管理的 API，目前可在預覽版本中被取代。  使用新的資源管理 API 從 Python，請參閱詳細資料的[Azure 資源管理文件](http://azure-sdk-for-python.readthedocs.org/)。

本指南為您示範如何以程式設計方式從 Python 執行一般的服務管理工作。 [Azure SDK python](https://github.com/Azure/azure-sdk-for-python) **ServiceManagementService**類別支援許多的服務管理相關功能[Azure 傳統入口網站]中所提供的程式設計存取[management-portal]（例如**建立、 更新和刪除雲端服務、 部署，資料管理服務與虛擬機器**）。 這項功能可以用來建立需要以程式設計方式存取服務管理應用程式。

## <a name="WhatIs"></a>服務管理功能
服務管理 API 提供許多的服務管理功能，可透過[Azure 傳統入口網站]以程式設計方式存取[management-portal]。 Azure SDK python 可讓您管理您的雲端服務和儲存的帳戶。

若要使用的服務管理 API，您需要[建立 Azure 帳戶](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="Concepts"></a>概念
Azure SDK python 換行[Azure 服務管理 API][svc-mgmt-rest-api]，這是 REST API。 所有的 API 作業執行是透過 SSL 且互相使用 X.509 v3 憑證驗證。 管理服務可能會從內執行 Azure，或在網際網路上直接從可以 HTTPS 要求傳送及接收 HTTPS 回應的任何應用程式的服務存取。

## <a name="Installation"></a>安裝

本文所述的所有功能都都可以在`azure-servicemanagement-legacy`套件，您可以使用 pip 對其進行安裝。 如需詳細資訊 （例如，如果您不熟悉 Python），請參閱這篇文章︰[安裝 Python 和 Azure SDK](../python-how-to-install.md)

## <a name="Connect"></a>如何︰ 連線至服務管理
若要連線至服務管理端點，您需要您 Azure 訂閱識別碼和有效管理憑證。 您可以取得您的訂閱識別碼，透過[Azure 傳統入口網站][management-portal]。

> [AZURE.NOTE] 自 Python v0.8.0 Azure SDK，您就可以使用 Windows 上執行時，使用 OpenSSL 建立的憑證。  需要 Python 2.7.4 或更新版本。 我們建議您的使用者，而不是.pfx，使用 OpenSSL，自.pfx 憑證可能會在未來的支援。

### <a name="management-certificates-on-windowsmaclinux-openssl"></a>在 Windows/Mac/Linux (OpenSSL) 上的管理憑證
若要建立您的管理憑證，您可以使用[OpenSSL](http://www.openssl.org/) 。  您需要建立兩個憑證，一個用於伺服器 (`.cer`檔案)，另一個用戶端 (`.pem`檔案)。 若要建立`.pem`檔案，請執行︰

    openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem

若要建立`.cer`憑證，請執行︰

    openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer

如需更多關於 Azure 憑證的詳細資訊，請參閱[Azure 雲端服務的憑證概觀](./cloud-services-certs-create.md)。 如 OpenSSL 參數的完整描述，請參閱[http://www.openssl.org/docs/apps/openssl.html](http://www.openssl.org/docs/apps/openssl.html)的文件。

建立這些檔案之後，您需要上傳`.cer`檔案，以透過[Azure 傳統入口網站]的 [設定] 索引標籤的 [上傳 」 動作 Azure[management-portal]，您需要記下您用來儲存及`.pem`檔案。

取得您的訂閱 ID 之後，建立的憑證上, 傳`.cer`檔案至 Azure，您可以連線至 Azure 管理端點傳遞訂閱 id 和其路徑`.pem` **ServiceManagementService**檔案︰

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = '<path_to_.pem_certificate>'

    sms = ServiceManagementService(subscription_id, certificate_path)

在上述範例中，`sms`是**ServiceManagementService**物件。 **ServiceManagementService**類別是用來管理 Azure 服務的主要類別。

### <a name="management-certificates-on-windows-makecert"></a>在 Windows (MakeCert) 上的管理憑證

您可以建立管理自我簽署的憑證，您的電腦使用`makecert.exe`。  開啟**Visual Studio 命令提示字元**系統**管理員**的身分並使用以下命令，以您想要使用的憑證名稱取代*AzureCertificate* 。

    makecert -sky exchange -r -n "CN=AzureCertificate" -pe -a sha1 -len 2048 -ss My "AzureCertificate.cer"

命令會建立`.cer`檔案，然後將它安裝的**個人**憑證存放區。 如需詳細資訊，請參閱[Azure 雲端服務的憑證概觀](./cloud-services-certs-create.md)。

您建立的憑證之後，您需要上傳`.cer`檔案，以透過[Azure 傳統入口網站]的 [設定] 索引標籤的 [上傳 」 動作 Azure[management-portal]。

取得您的訂閱 ID 之後，建立的憑證上, 傳`.cer`檔案至 Azure，您可以連線至 Azure 管理結束點，在您的**個人**憑證存放區中的訂閱識別碼] 和 [憑證的位置傳遞給**ServiceManagementService** （一次，取代*AzureCertificate*與您的憑證的名稱）︰

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = 'CURRENT_USER\\my\\AzureCertificate'

    sms = ServiceManagementService(subscription_id, certificate_path)

在上述範例中，`sms`是**ServiceManagementService**物件。 **ServiceManagementService**類別是用來管理 Azure 服務的主要類別。

## <a name="ListAvailableLocations"></a>如何︰ 清單可用的位置

若要在清單中所使用的主機服務的位置，使用**清單\_位置**方法︰

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_locations()
    for location in result:
        print(location.name)

當您建立的雲端服務或儲存服務時必須提供的正確位置。 **清單\_位置**方法一律會傳回目前可用的位置的最新清單。 本文撰寫時，可用的位置是︰

- 西歐
- 北美歐洲
- 東南亞
- 中式地址
- 美國中部
- 北美美國中部
- 美國中部南美洲
- 西美制]。
- 設定適用於美國
- 日本東亞
- 日本西部
- 巴西南部
- 澳大利亞東亞
- 澳大利亞 Southeast

## <a name="CreateCloudService"></a>如何︰ 建立雲端服務

當您建立的應用程式，並在 Azure 中執行時，[程式碼] 和 [組態一起稱為 Azure[雲端服務][]（也稱為*代管服務*於舊版 Azure）。 **建立\_裝載\_服務**方法可讓您建立新的代管的服務提供在代管的服務名稱 （必須是唯一的 Azure）、 標籤 （自動編碼 base64）、 描述及位置。

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myhostedservice'
    label = 'myhostedservice'
    desc = 'my hosted service'
    location = 'West US'

    sms.create_hosted_service(name, label, desc, location)

您可以列出所有裝載的服務與您訂閱的**清單\_裝載\_服務**方法︰

    result = sms.list_hosted_services()

    for hosted_service in result:
        print('Service name: ' + hosted_service.service_name)
        print('Management URL: ' + hosted_service.url)
        print('Location: ' + hosted_service.hosted_service_properties.location)
        print('')

如果您想要取得特定的代管服務的相關資訊，您可以執行代管的服務名稱，以傳遞**取得\_裝載\_服務\_屬性**方法︰

    hosted_service = sms.get_hosted_service_properties('myhostedservice')

    print('Service name: ' + hosted_service.service_name)
    print('Management URL: ' + hosted_service.url)
    print('Location: ' + hosted_service.hosted_service_properties.location)

建立雲端服務之後，您可以將程式碼部署到與服務**建立\_部署**方法。

## <a name="DeleteCloudService"></a>如何︰ 刪除雲端服務

您可以藉由傳送服務名稱，即可刪除雲端服務**刪除\_裝載\_服務**方法︰

    sms.delete_hosted_service('myhostedservice')

您可以刪除服務之前，必須先刪除所有部署的服務。 (請參閱[如何︰ 刪除部署](#DeleteDeployment)如需詳細資訊。)

## <a name="DeleteDeployment"></a>如何︰ 刪除部署

若要刪除的部署，請使用**刪除\_部署**方法。 下列範例會示範如何刪除命名的部署`v1`。

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment('myhostedservice', 'v1')

## <a name="CreateStorageService"></a>如何︰ 建立儲存服務

[儲存服務](../storage/storage-create-storage-account.md)可讓您存取 Azure [Blob](../storage/storage-python-how-to-use-blob-storage.md)、[表格](../storage/storage-python-how-to-use-table-storage.md)和[佇列](../storage/storage-python-how-to-use-queue-storage.md)。 若要建立儲存服務，您需要的名稱服務 （3 和 24 小寫字母的字元之間和 Azure 中唯一）、 描述、 標籤 （最多 100 個字元自動編碼 base64） 及位置。 下列範例會示範如何建立儲存服務指定一個位置。

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'mystorageaccount'
    label = 'mystorageaccount'
    location = 'West US'
    desc = 'My storage account description.'

    result = sms.create_storage_account(name, desc, label, location=location)

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

附註在上述範例中的狀態**建立\_儲存\_帳戶**作業可擷取傳遞傳回的結果**建立\_儲存空間\_帳戶**至**取得\_作業\_狀態**方法。  

您可以在您儲存的帳戶和與內容列出**清單\_儲存\_帳戶**方法︰

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_storage_accounts()
    for account in result:
        print('Service name: ' + account.service_name)
        print('Location: ' + account.storage_service_properties.location)
        print('')

## <a name="DeleteStorageService"></a>如何︰ 刪除儲存服務

您可以藉由傳送儲存服務名稱刪除儲存服務**刪除\_儲存\_帳戶**方法。 刪除儲存服務會刪除所有服務 （二進位大型物件、 表格和佇列） 中所儲存的資料。

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_storage_account('mystorageaccount')

## <a name="ListOperatingSystems"></a>如何︰ 清單可用作業系統

若要在清單中的主機服務可使用的作業系統，使用**清單\_作業系統\_系統**方法︰

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_operating_systems()

    for os in result:
        print('OS: ' + os.label)
        print('Family: ' + os.family_label)
        print('Active: ' + str(os.is_active))

或者，您可以使用**清單\_作業系統\_系統\_系列**群組系列作業系統的方法︰

    result = sms.list_operating_system_families()

    for family in result:
        print('Family: ' + family.label)
        for os in family.operating_systems:
            if os.is_active:
                print('OS: ' + os.label)
                print('Version: ' + os.version)
        print('')

## <a name="CreateVMImage"></a>如何︰ 建立作業系統圖像

若要新增作業系統圖像至圖像存放庫**新增\_os\_圖像**方法︰

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'mycentos'
    label = 'mycentos'
    os = 'Linux' # Linux or Windows
    media_link = 'url_to_storage_blob_for_source_image_vhd'

    result = sms.add_os_image(label, media_link, name, os)

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

若要在清單中所使用的作業系統圖像，使用**清單\_os\_圖像**方法。 包括所有的平台圖像和使用者圖像︰

    result = sms.list_os_images()

    for image in result:
        print('Name: ' + image.name)
        print('Label: ' + image.label)
        print('OS: ' + image.os)
        print('Category: ' + image.category)
        print('Description: ' + image.description)
        print('Location: ' + image.location)
        print('Media link: ' + image.media_link)
        print('')

## <a name="DeleteVMImage"></a>如何︰ 刪除作業系統圖像

若要刪除的使用者圖像，請使用**刪除\_os\_圖像**方法︰

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.delete_os_image('mycentos')

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

## <a name="CreateVM"></a>如何︰ 建立虛擬機器

若要建立虛擬機器，您必須先建立[雲端服務](#CreateCloudService)。  然後建立虛擬機器部署使用**建立\_虛擬\_機器\_部署**方法︰

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myvm'
    location = 'West US'

    #Set the location
    sms.create_hosted_service(service_name=name,
        label=name,
        location=location)

    # Name of an os image as returned by list_os_images
    image_name = 'OpenLogic__OpenLogic-CentOS-62-20120531-en-us-30GB.vhd'

    # Destination storage account container/blob where the VM disk
    # will be created
    media_link = 'url_to_target_storage_blob_for_vm_hd'

    # Linux VM configuration, you can use WindowsConfigurationSet
    # for a Windows VM instead
    linux_config = LinuxConfigurationSet('myhostname', 'myuser', 'mypassword', True)

    os_hd = OSVirtualHardDisk(image_name, media_link)

    sms.create_virtual_machine_deployment(service_name=name,
        deployment_name=name,
        deployment_slot='production',
        label=name,
        role_name=name,
        system_config=linux_config,
        os_virtual_hard_disk=os_hd,
        role_size='Small')

## <a name="DeleteVM"></a>如何︰ 刪除虛擬機器

若要刪除虛擬機器，您第一次刪除部署使用**刪除\_部署**方法︰

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment(service_name='myvm',
        deployment_name='myvm')

雲端服務可以再刪除使用**刪除\_裝載\_服務**方法︰

    sms.delete_hosted_service(service_name='myvm')

##<a name="how-to-create-a-virtual-machine-from-a-captured-virtual-machine-image"></a>如何︰ 從所擷取的虛擬機器圖像建立虛擬機器

若要擷取的 VM 圖像，請您第一次呼叫**擷取\_vm\_圖像**方法︰

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    # replace the below three parameters with actual values
    hosted_service_name = 'hs1'
    deployment_name = 'dep1'
    vm_name = 'vm1'

    image_name = vm_name + 'image'
    image = CaptureRoleAsVMImage    ('Specialized',
        image_name,
        image_name + 'label',
        image_name + 'description',
        'english',
        'mygroup')

    result = sms.capture_vm_image(
            hosted_service_name,
            deployment_name,
            vm_name,
            image
        )

接下來，若要確保您已成功擷取圖像，請使用**清單\_vm\_圖像**api，並確定您的圖像便會出現在結果中︰

    images = sms.list_vm_images()

若要最後建立虛擬機器使用擷取的圖像，請使用**建立\_虛擬\_機器\_部署**方法之前，但這次傳入 vm_image_name 改為

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myvm'
    location = 'West US'

    #Set the location
    sms.create_hosted_service(service_name=name,
        label=name,
        location=location)

    sms.create_virtual_machine_deployment(service_name=name,
        deployment_name=name,
        deployment_slot='production',
        label=name,
        role_name=name,
        system_config=linux_config,
        os_virtual_hard_disk=None,
        role_size='Small',
        vm_image_name = image_name)

若要進一步瞭解如何擷取 Linux 虛擬機器，請參閱[如何擷取 Linux 虛擬機器。](../virtual-machines/virtual-machines-linux-classic-capture-image.md)

若要進一步瞭解如何擷取 Windows 虛擬機器，請參閱[如何擷取 Windows 虛擬機器。](../virtual-machines/virtual-machines-windows-classic-capture-image.md)

## <a name="What's Next"></a>接下來的步驟

現在，您學到的服務管理基本概念，您可以存取[完整的 API Azure Python SDK 的參考文件](http://azure-sdk-for-python.readthedocs.org/)，然後執行複雜的工作，輕鬆地來管理您的 python 應用程式。

如需詳細資訊，請參閱[Python 開發人員中心](/develop/python/)。

[What is Service Management]: #WhatIs
[Concepts]: #Concepts
[How to: Connect to service management]: #Connect
[How to: List available locations]: #ListAvailableLocations
[How to: Create a cloud service]: #CreateCloudService
[How to: Delete a cloud service]: #DeleteCloudService
[How to: Create a deployment]: #CreateDeployment
[How to: Update a deployment]: #UpdateDeployment
[How to: Move deployments between staging and production]: #MoveDeployments
[How to: Delete a deployment]: #DeleteDeployment
[How to: Create a storage service]: #CreateStorageService
[How to: Delete a storage service]: #DeleteStorageService
[How to: List available operating systems]: #ListOperatingSystems
[How to: Create an operating system image]: #CreateVMImage
[How to: Delete an operating system image]: #DeleteVMImage
[How to: Create a virtual machine]: #CreateVM
[How to: Delete a virtual machine]: #DeleteVM
[Next Steps]: #NextSteps
[management-portal]: https://manage.windowsazure.com/
[svc-mgmt-rest-api]: http://msdn.microsoft.com/library/windowsazure/ee460799.aspx


[雲端服務]:/services/cloud-services/

