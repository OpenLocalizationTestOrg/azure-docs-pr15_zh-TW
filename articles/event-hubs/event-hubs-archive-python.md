<properties
    pageTitle="Azure 事件集線器封存逐步解說 |Microsoft Azure"
    description="會使用 Azure Python SDK，示範使用事件集線器封存功能的範例。"
    services="event-hubs"
    documentationCenter=""
    authors="djrosanova"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="darosa;sethm"/>

# <a name="event-hubs-archive-walkthrough-python"></a>事件集線器封存逐步解說︰ Python

事件集線器封存是事件集線器，可讓您自動在您的事件中心內，您所選擇的 Azure Blob 儲存體帳戶進行資料流資料的新功能。 如此可讓您輕鬆執行批次處理串流的即時資料。 本文將說明如何使用 Python 事件集線器封存。 如需有關事件集線器封存的詳細資訊，請參閱[概觀文件](event-hubs-archive-overview.md)。

此範例會使用 Azure Python SDK，示範使用封存功能。 Sender.py 會傳送到事件模擬環境遙測 JSON 格式。 [事件] 中心設定為使用封存功能來撰寫此資料至 blob 分批的儲存空間。 Archivereader.py 然後讀取這些二進位大型物件建立附加檔案，每個裝置，並將.csv 檔案的資料。

什麼就會完成

1.  建立 Azure Blob 儲存體帳戶與 blob 容器中，使用 [Azure 入口網站

2.  建立事件中樞的命名空間，使用 [Azure 入口網站

3.  建立事件中心使用封存功能已啟用，使用 Azure 入口網站

4.  傳送資料至 [事件] 中心內的 Python 指令碼

5.  從封存讀取檔案並進行處理與另一個 Python 指令碼

必要條件

1.  Python 2.7.x

2.  Azure 的訂閱

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="create-an-azure-storage-account"></a>建立 Azure 儲存體帳戶

1.  [Azure 入口網站][]登入。

2.  在入口網站的左的導覽窗格中，按一下 [新增]，然後按一下 [資料 + 儲存空間，，然後按一下 [儲存帳戶。

3.  完成儲存帳戶刀中的欄位，然後按一下 [**建立**]。

    ![][1]

4.  您會看到**已成功部署**郵件中，按一下 [新的儲存空間帳戶，並在**基礎**刀後按一下 [**二進位大型物件**]。 **Blob 服務**刀開啟時，按一下 [ **+ 容器**頂端]。 命名容器**封存**中，然後關閉**Blob 服務**刀。

5.  按一下左側的刀**便捷鍵**，然後複製的儲存體帳戶名稱和**key1**的值。 將這些值儲存到記事本或其他暫存的位置。

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

## <a name="create-a-python-script-to-send-events-to-your-event-hub"></a>建立事件傳送到您的事件集線器 Python 指令碼

1.  開啟您最愛的 Python 編輯器，例如[Visual Studio 程式碼][]。

2.  建立稱為**sender.py**指令碼。 這個指令碼會傳送給您的事件中樞的 200 事件。 他們的年齡中 JSON 傳送的簡單環境讀取。

3.  下列程式碼貼入 sender.py:

    ```
    import uuid
    import datetime
    import random
    import json
    from azure.servicebus import ServiceBusService
    
    sbs = ServiceBusService(service_namespace='INSERT YOUR NAMESPACE NAME', shared_access_key_name='RootManageSharedAccessKey', shared_access_key_value='INSERT YOUR KEY')
    devices = []
    for x in range(0, 10):
        devices.append(str(uuid.uuid4()))
    
    for y in range(0,20):
        for dev in devices:
            reading = {'id': dev, 'timestamp': str(datetime.datetime.utcnow()), 'uv': random.random(), 'temperature': random.randint(70, 100), 'humidity': random.randint(70, 100)}
            s = json.dumps(reading)
            sbs.send\_event('myhub', s)
        print y
    ```
4.  更新以使用您的命名空間名稱和索引鍵值時所建立的事件集線器命名空間取得上述的程式碼。

## <a name="create-a-python-script-to-read-your-archive-files"></a>建立 Python 指令碼閱讀封存檔案

1.  填寫刀，然後按一下 [**建立**]。

2.  建立稱為**archivereader.py**指令碼。 這個指令碼會閱讀封存檔案，並建立的檔案，每個裝置撰寫僅適用於該裝置資料。

3.  下列程式碼貼入 archivereader.py:

    ```
    import os
    import string
    import json
    import avro.schema
    from avro.datafile import DataFileReader, DataFileWriter
    from avro.io import DatumReader, DatumWriter
    from azure.storage.blob import BlockBlobService
    
    def processBlob(filename):
        reader = DataFileReader(open(filename, 'rb'), DatumReader())
        dict = {}
        for reading in reader:
            parsed\_json = json.loads(reading["Body"])
            if not 'id' in parsed\_json:
                return
            if not dict.has\_key(parsed\_json['id']):
            list = []
            dict[parsed\_json['id']] = list
        else:
            list = dict[parsed\_json['id']]
            list.append(parsed\_json)
        reader.close()
        for device in dict.keys():
            deviceFile = open(device + '.csv', "a")
            for r in dict[device]:
                deviceFile.write(", ".join([str(r[x]) for x in r.keys()])+'\\n')

    def startProcessing(accountName, key, container):
        print 'Processor started using path: ' + os.getcwd()
        block\_blob\_service = BlockBlobService(account\_name=accountName, account\_key=key)
        generator = block\_blob\_service.list\_blobs(container)
        for blob in generator:
            if blob.properties.content\_length != 0:
                print('Downloaded a non empty blob: ' + blob.name)
                cleanName = string.replace(blob.name, '/', '\_')
                block\_blob\_service.get\_blob\_to\_path(container, blob.name, cleanName)
                processBlob(cleanName)
                os.remove(cleanName)
            block\_blob\_service.delete\_blob(container, blob.name)
    startProcessing('YOUR STORAGE ACCOUNT NAME', 'YOUR KEY', 'archive')
    ```

4.  請務必通話中貼上儲存體帳戶名稱和鍵適當的值`startProcessing`。

## <a name="run-the-scripts"></a>執行指令碼

1.  開啟命令提示字元中路徑，包含 Python，並執行下列命令來安裝 Python 必要條件套件︰

    ```
    pip install azure-storage
    pip install azure-servicebus
    pip install avro
    ```
  
    如果您有舊版的任一 azure 儲存體或 azure 您可能需要使用**-升級**選項

    您可能也需要執行下列動作 （不需要在大多數系統上）︰

    ```
    pip install cryptography
    ```

2.  變更您的目錄，任何您儲存 sender.py 和 archivereader.py，並執行此命令時︰

    ```
    start python sender.py
    ```
    
    此舉會啟動新 Python 程序來執行寄件者。

3. 現在請等候幾分鐘的執行封存。 將您的原始命令視窗，然後輸入下列命令︰

    ```
    python archivereader.py
    ```

這個封存處理器下載所有 blob 儲存體帳戶/容器中使用本機的目錄。 它會處理任何不是空白，並為.csv 檔案中寫入本機目錄的結果。

## <a name="next-steps"></a>後續步驟

您可以進一步瞭解事件集線器瀏覽下列連結︰

- [封存事件集線器概觀][]
- 完成[範例應用程式的使用事件集線器][]。
- [延展事件處理事件集線器][]樣本。
- [事件集線器概觀][]
 

[Azure 入口網站]: https://portal.azure.com/
[封存事件集線器概觀]: event-hubs-archive-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]: https://azure.microsoft.com/en-us/documentation/articles/storage-create-storage-account/
[Visual Studio 程式碼]: https://code.visualstudio.com/
[事件集線器概觀]: event-hubs-overview.md
[使用事件集線器範例應用程式]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[不按比例縮放出事件處理事件集線器]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
