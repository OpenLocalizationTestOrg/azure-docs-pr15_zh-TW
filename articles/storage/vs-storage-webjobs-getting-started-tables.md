<properties
    pageTitle="快速入門 Azure 儲存和 Visual Studio 連線服務 （WebJob 專案）"
    description="如何連線到儲存帳戶使用 Visual Studio 連接服務之後，在 Visual Studio 中 Azure WebJobs 專案中使用 Azure 資料表儲存體快速入門"
    services="storage"
    documentationCenter=""
    authors="TomArcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="storage"
    ms.workload="web"
    ms.tgt_pltfrm="vs-getting-started"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/18/2016"
    ms.author="tarcher"/>

# <a name="getting-started-with-azure-storage-azure-webjob-projects"></a>快速入門 Azure 儲存空間 （Azure WebJob 專案）

[AZURE.INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>概觀

本文提供 C# 程式碼顯示的範例顯示如何使用 Azure WebJobs SDK 版本 1.x Azure 資料表儲存體服務。 程式碼範例使用[WebJobs SDK](../app-service-web/websites-dotnet-webjobs-sdk.md)版本 1.x。

Azure 資料表儲存服務可讓您儲存大量的結構化資料。 服務已接受經過驗證的來電內部和外部 Azure 雲端 NoSQL 資料存放區。 Azure 資料表非常適合儲存結構化、 非關聯式資料。  如需詳細資訊，請參閱[快速入門使用.NET Azure 資料表儲存體](storage-dotnet-how-to-use-tables.md#create-a-table)。

部分程式碼片段顯示稱為以手動方式，也就是不是使用其中一個觸發程序屬性的函數中用的**表格**屬性。

## <a name="how-to-add-entities-to-a-table"></a>如何將項目新增至表格

若要新增至資料表的項目，請使用 [**資料表**屬性**ICollector<T>**或**IAsyncCollector<T>**參數**T**指定您想要新增的實體架構的位置。 屬性建構函式參數的字串指定的資料表名稱。

下列程式碼範例會將**人員**的實體*輸入*的資料表。

        [NoAutomaticTrigger]
        public static void IngressDemo(
            [Table("Ingress")] ICollector<Person> tableBinding)
        {
            for (int i = 0; i < 100000; i++)
            {
                tableBinding.Add(
                    new Person() {
                        PartitionKey = "Test",
                        RowKey = i.ToString(),
                        Name = "Name" }
                    );
            }
        }

您使用的**ICollector**類型通常是衍生自**TableEntity**或實作**ITableEntity**，但並不一定。 其中一項下列**人員**類別使用上述**輸入**方法] 所示的程式碼。

        public class Person : TableEntity
        {
            public string Name { get; set; }
        }

        public class Person
        {
            public string PartitionKey { get; set; }
            public string RowKey { get; set; }
            public string Name { get; set; }
        }

如果您想要直接使用 API Azure 儲存空間，您可以新增**CloudStorageAccount**參數的方法簽章。

## <a name="real-time-monitoring"></a>即時監視

資料輸入函數通常程序發掘大量資料，因為 WebJobs SDK 儀表板提供監控的即時資料。 [**引動記錄**] 區段會告訴您仍是否正在執行的函數。

![輸入執行的函數](./media/vs-storage-webjobs-getting-started-tables/ingressrunning.png)

**引動詳細資料**頁面報表函數的進度 （數字的實體撰寫） 時，它正在執行，並讓您有機會中止它。

![輸入執行的函數](./media/vs-storage-webjobs-getting-started-tables/ingressprogress.png)

此函數完成時，**引動詳細資料**頁面報表撰寫的資料列數目。

![完成的輸入函數](./media/vs-storage-webjobs-getting-started-tables/ingresssuccess.png)

## <a name="how-to-read-multiple-entities-from-a-table"></a>如何以讀取表格中的多個項目

若要閱讀表格，使用 [**資料表**屬性與**IQueryable<T>**參數類型**T**衍生自**TableEntity**或實作**ITableEntity**的位置。

下列程式碼範例讀取，並**輸入**表格的記錄的所有資料列︰

        public static void ReadTable(
            [Table("Ingress")] IQueryable<Person> tableBinding,
            TextWriter logger)
        {
            var query = from p in tableBinding select p;
            foreach (Person person in query)
            {
                logger.WriteLine("PK:{0}, RK:{1}, Name:{2}",
                    person.PartitionKey, person.RowKey, person.Name);
            }
        }

### <a name="how-to-read-a-single-entity-from-a-table"></a>瞭解如何從資料表中讀取單一項目

有兩個額外的參數，可讓您指定的分割索引鍵和資料列識別碼，當您要繫結至單一資料表實體的**資料表**屬性建構函式。

下列程式碼範例會顯示表格列的值為基礎的磁碟分割索引鍵和列鍵佇列中郵件中收到**人員**實體如下︰  

        public static void ReadTableEntity(
            [QueueTrigger("inputqueue")] Person personInQueue,
            [Table("persontable","{PartitionKey}", "{RowKey}")] Person personInTable,
            TextWriter logger)
        {
            if (personInTable == null)
            {
                logger.WriteLine("Person not found: PK:{0}, RK:{1}",
                        personInQueue.PartitionKey, personInQueue.RowKey);
            }
            else
            {
                logger.WriteLine("Person found: PK:{0}, RK:{1}, Name:{2}",
                        personInTable.PartitionKey, personInTable.RowKey, personInTable.Name);
            }
        }


在此範例中的**人員**類別沒有實作**ITableEntity**。

## <a name="how-to-use-the-net-storage-api-directly-to-work-with-a-table"></a>如何使用直接使用的表格儲存 API

您也可以使用**表格**屬性與**CloudTable**物件，取得更多的彈性，使用使用表格中。

下列程式碼範例使用**CloudTable**物件將單一項目新增至 [*輸入*] 資料表。

        public static void UseStorageAPI(
            [Table("Ingress")] CloudTable tableBinding,
            TextWriter logger)
        {
            var person = new Person()
                {
                    PartitionKey = "Test",
                    RowKey = "100",
                    Name = "Name"
                };
            TableOperation insertOperation = TableOperation.Insert(person);
            tableBinding.Execute(insertOperation);
        }

如需有關如何使用**CloudTable**物件的詳細資訊，請參閱[快速入門使用.NET Azure 資料表儲存體](storage-dotnet-how-to-use-tables.md)。

## <a name="related-topics-covered-by-the-queues-how-to-article"></a>相關的主題覆蓋佇列使用方法文章

如需如何處理資料表處理觸發佇列中的訊息，或針對 WebJobs SDK 案例表格處理，請參閱[快速入門 Azure 佇列中儲存和 Visual Studio 已連線的服務] （WebJob 專案）](vs-storage-webjobs-getting-started-queues.md)不是特定資訊。



## <a name="next-steps"></a>後續步驟

本文提供程式碼範例，顯示如何處理 Azure 資料表所使用的常見的案例。 如需有關如何使用 Azure WebJobs 和 WebJobs SDK 的詳細資訊，請參閱[Azure WebJobs 文件的資源](http://go.microsoft.com/fwlink/?linkid=390226)。
