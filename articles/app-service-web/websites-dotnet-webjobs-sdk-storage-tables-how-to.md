<properties 
    pageTitle="如何使用 WebJobs SDK Azure 資料表儲存體" 
    description="瞭解如何使用 WebJobs SDK Azure 資料表儲存體。 建立資料表，將項目新增至資料表，並閱讀現有的資料表。" 
    services="app-service\web, storage" 
    documentationCenter=".net" 
    authors="tdykstra" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="06/01/2016" 
    ms.author="tdykstra"/>

# <a name="how-to-use-azure-table-storage-with-the-webjobs-sdk"></a>如何使用 WebJobs SDK Azure 資料表儲存體

## <a name="overview"></a>概觀

本指南可提供 C# 程式碼範例，顯示如何讀取和寫入 Azure 儲存資料表使用[WebJobs SDK](websites-dotnet-webjobs-sdk.md)版 1.x。

本指南假設您已經知道[如何建立 WebJob 專案在 Visual Studio 中，指向您儲存的帳戶的連線字串](websites-dotnet-webjobs-sdk-get-started.md)或[多個儲存的帳戶](https://github.com/Azure/azure-webjobs-sdk/blob/master/test/Microsoft.Azure.WebJobs.Host.EndToEndTests/MultipleStorageAccountsEndToEndTests.cs)。
        
程式碼片段顯示部分`Table`[稱為以手動方式](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#manual)，也就是不是使用其中一個觸發程序屬性的函數中使用的屬性。 

## <a id="ingress"></a>如何將項目新增至表格

若要新增至資料表的項目，請使用`Table`屬性與`ICollector<T>`或`IAsyncCollector<T>`參數位置`T`指定您想要新增的實體架構。 屬性建構函式參數的字串指定的資料表名稱。 

下列程式碼範例將`Person`項目，名為*輸入*的資料表。

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

通常是類型您使用`ICollector`衍生自`TableEntity`或實作`ITableEntity`，但不含至。 下列其中一`Person`類別工作顯示在前面的程式碼`Ingress`方法。

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

如果您想要直接使用 API Azure 儲存空間，您可以新增`CloudStorageAccount`參數的方法簽章。

## <a id="monitor"></a>即時監視

資料輸入函數通常程序發掘大量資料，因為 WebJobs SDK 儀表板提供監控的即時資料。 [**引動記錄**] 區段會告訴您仍是否正在執行的函數。

![輸入執行的函數](./media/websites-dotnet-webjobs-sdk-storage-tables-how-to/ingressrunning.png)

**引動詳細資料**頁面報表函數的進度 （數字的實體撰寫） 時，它正在執行，並讓您有機會中止它。 

![輸入執行的函數](./media/websites-dotnet-webjobs-sdk-storage-tables-how-to/ingressprogress.png)

此函數完成時，**引動詳細資料**頁面報表撰寫的資料列數目。

![完成的輸入函數](./media/websites-dotnet-webjobs-sdk-storage-tables-how-to/ingresssuccess.png)

## <a id="multiple"></a>如何以讀取表格中的多個項目

若要閱讀表格，使用`Table`屬性與`IQueryable<T>`參數，輸入`T`衍生自`TableEntity`或實作`ITableEntity`。

下列程式碼範例讀取並記錄的所有資料列`Ingress`表格︰
 
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

### <a id="readone"></a>瞭解如何從資料表中讀取單一項目

有`Table`含有兩個其他參數，可讓您指定的分割索引鍵和資料列識別碼，當您要繫結至單一資料表實體屬性建構函式。

下列程式碼範例讀取的表格列`Person`實體數值為根據的磁碟分割索引鍵和列按鍵佇列中郵件中收到︰  

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


`Person`在此範例中的類別沒有實作`ITableEntity`。

## <a id="storageapi"></a>如何使用直接使用的表格儲存 API

您也可以使用`Table`屬性與`CloudTable`取得更多的彈性，使用使用表格中的物件。

下列程式碼範例用途`CloudTable`將單一項目新增至 [*輸入*表格的物件。 
 
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

如需有關如何使用`CloudTable`物件，請參閱[如何使用.NET 從資料表儲存體](../storage/storage-dotnet-how-to-use-tables.md)。 

## <a id="queues"></a>相關的主題覆蓋佇列使用方法文章

如需如何處理資料表處理觸發佇列中的訊息，或針對 WebJobs SDK 案例表格處理，請參閱[如何使用 Azure 佇列中的儲存空間 WebJobs SDK 與](websites-dotnet-webjobs-sdk-storage-queues-how-to.md)非特定資訊。 

下列文件中涵蓋下列主題︰

* 非同步函數
* 多個執行個體
* 正常的關閉
* 使用函數的內文中的 WebJobs SDK 屬性
* 在 [程式碼中設定 SDK 連線字串
* 設定值 WebJobs SDK 建構函式參數程式碼
* 手動觸發函數
* 寫入記錄檔

## <a id="nextsteps"></a>後續步驟

本指南已提供程式碼範例，顯示如何處理 Azure 資料表所使用的常見的案例。 如需有關如何使用 Azure WebJobs 和 WebJobs SDK 的詳細資訊，請參閱[Azure WebJobs 建議資源](http://go.microsoft.com/fwlink/?linkid=390226)。
 
