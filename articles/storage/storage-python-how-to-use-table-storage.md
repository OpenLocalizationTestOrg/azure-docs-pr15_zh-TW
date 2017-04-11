<properties
    pageTitle="如何使用從 Python 資料表儲存體 |Microsoft Azure"
    description="結構化的資料儲存在雲端使用 Azure 資料表儲存體，NoSQL 資料存放區。"
    services="storage"
    documentationCenter="python"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>


# <a name="how-to-use-table-storage-from-python"></a>如何使用從 Python 資料表儲存體

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>概觀

本指南為您示範如何使用 Azure 資料表儲存服務執行常見的案例。 範例 Python 撰寫，並使用[Microsoft Azure 儲存體 SDK python]。 涵蓋的案例包含建立及刪除表格，除了在插入文字與查詢表格中的項目。

[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-table"></a>建立表格

**TableService**物件可讓您搭配資料表服務。 下列程式碼會建立**TableService**物件。 新增您要在其中任何 Python 檔案以程式控制方式存取 Azure 儲存體上方附近的程式碼︰

    from azure.storage.table import TableService, Entity

下列程式碼會使用儲存體帳戶名稱和帳戶金鑰，以建立**TableService**物件。  使用您的帳戶名稱和鍵取代 「 我的帳戶 」 和 「 mykey 」。

    table_service = TableService(account_name='myaccount', account_key='mykey')

    table_service.create_table('tasktable')

## <a name="add-an-entity-to-a-table"></a>實體新增至資料表

若要新增的實體，先建立字典或定義您的實體屬性名稱和值的實體。 請注意，針對每個實體，您必須指定**PartitionKey**和**RowKey**。 這些是您的實體的唯一識別碼。 您可以查詢速度將比您可以查詢您的其他內容使用這些值。 系統會使用**PartitionKey**自動許多儲存空間節點上發佈表格項目。
有相同的**PartitionKey**的項目會儲存在相同的節點。 **RowKey**是實體所屬的磁碟分割內的唯一識別碼。

若要新增至表格的實體，傳遞到字典物件**插入\_實體**方法。

    task = {'PartitionKey': 'tasksSeattle', 'RowKey': '1', 'description' : 'Take out the trash', 'priority' : 200}
    table_service.insert_entity('tasktable', task)

您也可以將傳遞的**實體**類別的執行個體**插入\_實體**方法。

    task = Entity()
    task.PartitionKey = 'tasksSeattle'
    task.RowKey = '2'
    task.description = 'Wash the car'
    task.priority = 100
    table_service.insert_entity('tasktable', task)

## <a name="update-an-entity"></a>更新實體

將此程式碼會顯示如何更新的版本以取代現有的實體的舊版本。

    task = {'PartitionKey': 'tasksSeattle', 'RowKey': '1', 'description' : 'Take out the garbage', 'priority' : 250}
    table_service.update_entity('tasktable', 'tasksSeattle', '1', task, content_type='application/atom+xml')

如果要更新的實體不存在，然後更新作業會失敗。 如果您想要儲存的實體，不論是否存在之前，請使用**插入\_或\_replace_entity**。
在下列範例中，第一個呼叫將會取代現有的實體。 第二個通話會插入新的實體，因為沒有指定**PartitionKey**實體和**RowKey**存在於資料表。

    task = {'PartitionKey': 'tasksSeattle', 'RowKey': '1', 'description' : 'Take out the garbage again', 'priority' : 250}
    table_service.insert_or_replace_entity('tasktable', 'tasksSeattle', '1', task, content_type='application/atom+xml')

    task = {'PartitionKey': 'tasksSeattle', 'RowKey': '3', 'description' : 'Buy detergent', 'priority' : 300}
    table_service.insert_or_replace_entity('tasktable', 'tasksSeattle', '1', task, content_type='application/atom+xml')

## <a name="change-a-group-of-entities"></a>變更實體的群組

有時讓提交合作以確保原子處理伺服器批次中的多個作業。 若要完成的工作，您可以使用**TableBatch**類別。 當您想要送出批次時，您呼叫**認可\_批次**。 請注意，所有項目才能變更以批次時，必須在相同的磁碟分割。 下面這個範例將兩個實體相加以批次。

    from azure.storage.table import TableBatch
    batch = TableBatch()
    task10 = {'PartitionKey': 'tasksSeattle', 'RowKey': '10', 'description' : 'Go grocery shopping', 'priority' : 400}
    task11 = {'PartitionKey': 'tasksSeattle', 'RowKey': '11', 'description' : 'Clean the bathroom', 'priority' : 100}
    batch.insert_entity(task10)
    batch.insert_entity(task11)
    table_service.commit_batch('tasktable', batch)

批次也可協助程式管理員語法︰

    task12 = {'PartitionKey': 'tasksSeattle', 'RowKey': '12', 'description' : 'Go grocery shopping', 'priority' : 400}
    task13 = {'PartitionKey': 'tasksSeattle', 'RowKey': '13', 'description' : 'Clean the bathroom', 'priority' : 100}

    with table_service.batch('tasktable') as batch:
        batch.insert_entity(task12)
        batch.insert_entity(task13)


## <a name="query-for-an-entity"></a>實體的查詢

若要查詢的實體表格中，請使用**取得\_實體**藉由傳遞**PartitionKey**和**RowKey**的方法。

    task = table_service.get_entity('tasktable', 'tasksSeattle', '1')
    print(task.description)
    print(task.priority)

## <a name="query-a-set-of-entities"></a>查詢的實體一組

此範例會找出所有的任務，在西雅圖根據**PartitionKey**。

    tasks = table_service.query_entities('tasktable', filter="PartitionKey eq 'tasksSeattle'")
    for task in tasks:
        print(task.description)
        print(task.priority)

## <a name="query-a-subset-of-entity-properties"></a>查詢實體屬性的子集

查詢的資料表可以取得一些屬性的實體。
這項技巧，稱為*預測*，減少頻寬，以及改善查詢效能，特別是大型的項目。 使用**選取**參數，並將您想要顯示用戶端屬性的名稱。

下列程式碼中的查詢會傳回資料表中的實體的描述。

[AZURE.NOTE] 下列程式碼片段運作只針對雲端儲存服務。 不支援儲存模擬器。

    tasks = table_service.query_entities('tasktable', filter="PartitionKey eq 'tasksSeattle'", select='description')
    for task in tasks:
        print(task.description)

## <a name="delete-an-entity"></a>刪除實體

您可以刪除實體使用它的磁碟分割和資料列識別碼。

    table_service.delete_entity('tasktable', 'tasksSeattle', '1')

## <a name="delete-a-table"></a>刪除表格

下列程式碼會刪除一個資料表儲存體帳戶。

    table_service.delete_table('tasktable')

## <a name="next-steps"></a>後續步驟

現在，您學到的資料表儲存體基本概念，請遵循這些連結，瞭解更多。

- [Python 開發人員中心](/develop/python/)
- [Azure 儲存服務 REST API](http://msdn.microsoft.com/library/azure/dd179355)
- [Azure 儲存體小組部落格]
- [Microsoft Azure 儲存體 python SDK]

[Azure 儲存體小組部落格]: http://blogs.msdn.com/b/windowsazurestorage/
[Microsoft Azure 儲存體 python SDK]: https://github.com/Azure/azure-storage-python
