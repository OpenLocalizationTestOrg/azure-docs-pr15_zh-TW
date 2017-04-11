<properties 
    pageTitle="DocumentDB 程式設計︰ 預存程序、 資料庫引動程序，以及 Udf |Microsoft Azure" 
    description="瞭解如何使用 DocumentDB 寫入 [JavaScript 中的 [預存程序、 資料庫引動程序及使用者定義函數 (Udf)。 取得資料庫 programing 秘訣及其他內容。" 
    keywords="資料庫引動程序、 預存程序、 預存程序、 資料庫程式、 預存程序、 documentdb、 azure、 Microsoft azure"
    services="documentdb" 
    documentationCenter="" 
    authors="aliuy" 
    manager="jhubbard" 
    editor="mimig"/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/14/2016" 
    ms.author="andrl"/>

# <a name="documentdb-server-side-programming-stored-procedures-database-triggers-and-udfs"></a>DocumentDB 伺服器端程式設計︰ 預存程序、 資料庫引動程序，以及 Udf

瞭解如何 Azure DocumentDB 語言整合，交易執行 JavaScript 可讓開發人員撰寫 JavaScript 原本**預存程序**、**引動程序**及**使用者定義函數 (Udf)** 。 這個選項可讓您撰寫資料庫可以傳送及直接在資料庫儲存空間的磁碟分割區上執行的程式應用程式邏輯 

我們建議您快速入門觀看以下影片，其中為 Andrew Liu 提供 DocumentDB 的伺服器端資料庫程式設計模型的簡短簡介。 

> [AZURE.VIDEO azure-demo-a-quick-intro-to-azure-documentdbs-server-side-javascript]

然後，回到本文中，您會在此瞭解下列問題的答案︰  

- 如何寫入的預存程序、 觸發程序或使用 JavaScript UDF？
- 如何 DocumentDB 保證 ACID？
- 如何交易 DocumentDB 中運作？
- 什麼是預先觸發程序後觸發程序和如何撰寫一個？
- 如何註冊，使用 HTTP 執行預存程序、 觸發程序或 UDF RESTful 的方式？
- DocumentDB Sdk 有哪些可用來建立和執行預存程序、 引動程序，以及 Udf 嗎？

## <a name="introduction-to-stored-procedure-and-udf-programming"></a>預存程序和 UDF 程式設計簡介

這種方法的*「 JavaScript 現代日 T SQL 」*釋放應用程式開發人員類型系統不符和物件關聯對應技術複雜性。 也都有許多優點建立豐富的應用程式您可以利用的內建︰  

-   **程序的邏輯︰**JavaScript 為高層級程式設計語言，提供豐富且熟悉的介面表達商務邏輯。 您可以執行作業往資料的複雜的序列。

-   **最小的異動︰**DocumentDB 保證，在單一預存程序執行的資料庫作業或原子觸發程序。 這個選項可讓應用程式，讓所有的成功或都不成功合併在一個批次的相關的作業。 

-   **效能︰**JSON 實際上] 已對應至 Javascript 語言類型系統，而且也會儲存在 DocumentDB 的基本單位可讓您像 JSON 緩衝資料庫及使其可以視需要執行的程式碼中的文件的麻煩具體化最佳化的數字。 還有許多傳送至資料庫的商務邏輯相關聯的效能優點︰
    -   批次處理 – 開發人員可以群組作業，例如隨後便會插入並送出大量。 成本的網路流量延遲和儲存區的資源來建立個別交易大幅降低。 
    -   預先編譯 – DocumentDB 先行編譯預存程序、 引動程序及使用者定義函數 (Udf)，以避免每 JavaScript 編譯成本。 建置位元組程式碼的程序的邏輯的費用需要大量的計算的最小值。
    -   排序 – 許多作業需要端效果 （「 觸發程序 」） 可能會包含 [執行一或多個次要儲存區作業。 除了完整性，這是更具效能時移到伺服器。 
-   **封裝︰**預存程序可以用於群組在同一個位置中的商務邏輯。 這會有兩個優點︰
    -   它會將抽象層原始資料，讓資料設計師發展其個別資料的應用程式的頂端。 資料結構描述的情況，因為可能需要確定在應用程式中，如果有直接處理資料的脆弱假設時，這是特別的優點。  
    -   此抽象可以讓企業簡化指令碼的 access 來保護其資料。  

建立及執行的資料庫引動程序、 預存程序和自訂查詢運算子支援[REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx)、 [DocumentDB Studio](https://github.com/mingaliu/DocumentDBStudio/releases)，並在許多包括.NET、 Node.js 和 JavaScript 的平台的[用戶端 Sdk](documentdb-sdk-dotnet.md) 。

**本教學課程中使用[與問承諾 Node.js SDK](http://azure.github.io/azure-documentdb-node-q/)**以描繪出語法及使用方式的預存程序與引動程序，Udf。   

## <a name="stored-procedures"></a>預存程序

### <a name="example-write-a-simple-stored-procedure"></a>範例︰ 撰寫簡單的預存程序 
現在就讓我們開始使用簡單的預存程序會傳回"Hello World 」 回應。

    var helloWorldStoredProc = {
        id: "helloWorld",
        body: function () {
            var context = getContext();
            var response = context.getResponse();
    
            response.setBody("Hello, World");
        }
    }


預存程序登錄每個集合，並在上的文件及附件出現在該集合操作。 下列程式碼片段示範如何註冊集合 helloWorld 預存程序。 

    // register the stored procedure
    var createdStoredProcedure;
    client.createStoredProcedureAsync('dbs/testdb/colls/testColl', helloWorldStoredProc)
        .then(function (response) {
            createdStoredProcedure = response.resource;
            console.log("Successfully created stored procedure");
        }, function (error) {
            console.log("Error", error);
        });


一旦註冊預存程序之後，我們執行該集合，並讀取回在用戶端的結果。 

    // execute the stored procedure
    client.executeStoredProcedureAsync('dbs/testdb/colls/testColl/sprocs/helloWorld')
        .then(function (response) {
            console.log(response.result); // "Hello, World"
        }, function (err) {
            console.log("Error", error);
        });


物件提供存取所有 DocumentDB 儲存空間，可以執行的作業，以及存取要求與回應物件。 在此案例中，我們使用回應物件來設定用戶端傳送回應的本文。 如需詳細資訊，請參閱[DocumentDB JavaScript 伺服器 SDK 文件](http://azure.github.io/azure-documentdb-js-server/)。  

讓我們在此範例中展開，並新增更多資料庫預存程序的相關的功能。 預存程序可以建立、 更新、 讀取、 查詢及刪除文件和集合內的附件。    

### <a name="example-write-a-stored-procedure-to-create-a-document"></a>範例︰ 撰寫預存程序建立的文件 
下一步的程式碼片段示範如何使用內容物件互動 DocumentDB 資源。

    var createDocumentStoredProc = {
        id: "createMyDocument",
        body: function createMyDocument(documentToCreate) {
            var context = getContext();
            var collection = context.getCollection();

            var accepted = collection.createDocument(collection.getSelfLink(),
                  documentToCreate,
                  function (err, documentCreated) {
                      if (err) throw new Error('Error' + err.message);
                      context.getResponse().setBody(documentCreated.id)
                  });
            if (!accepted) return;
        }
    }


此預存的程序會帶領為輸入 documentToCreate，才能在目前的集合中建立文件的本文。 所有這類作業非同步而 JavaScript 函數回撥而定。 回撥函數有兩個參數，一個作業失敗的大小寫的錯誤物件，一個用於建立的物件。 內回撥，使用者可以處理的例外狀況或發生錯誤。 未提供回撥發生錯誤，以防 DocumentDB 執行階段擲回一個錯誤。   

上述範例中回撥會擲回錯誤作業失敗。 否則，它會將建立的文件的識別碼為用戶端回應的本文。 以下是此預存程序以輸入參數的執行方式。

    // register the stored procedure
    client.createStoredProcedureAsync('dbs/testdb/colls/testColl', createDocumentStoredProc)
        .then(function (response) {
            var createdStoredProcedure = response.resource;
    
            // run stored procedure to create a document
            var docToCreate = {
                id: "DocFromSproc",
                book: "The Hitchhiker’s Guide to the Galaxy",
                author: "Douglas Adams"
            };
    
            return client.executeStoredProcedureAsync('dbs/testdb/colls/testColl/sprocs/createMyDocument',
                  docToCreate);
        }, function (error) {
            console.log("Error", error);
        })
    .then(function (response) {
        console.log(response); // "DocFromSproc"
    }, function (error) {
        console.log("Error", error);
    });

    
請注意此預存程序可以修改文件主體的陣列，做為輸入並都在同一個預存程序執行，而不是個別的每一個建立的多個網路要求中建立。 這可以用於有效大量匯入程式實作 DocumentDB （討論稍後在本教學課程）。   

說明範例會示範如何使用預存程序。 我們會說明引動程序及使用者定義函數 (Udf)，稍後的教學課程。

## <a name="database-program-transactions"></a>資料庫程式交易
一般的資料庫中的交易可定義為一系列作為單一邏輯的工時單位所執行的作業。 每次交易提供**ACID 保證**。 ACID 是已知的縮寫，代表四個屬性-完整性、 一致性、 隔離及持續性。  

完整性，確保所有交易內所完成的工時會被都視為一個單位其中任一全部都是 [已確認] 或 [無。 一致性確保資料為一律內部依然跨交易。 沒有兩個交易干擾 – 通常，，大多數的商業系統提供可用的多個隔離層級根據應用程式的需求，可確保隔離。 持續性可確保一定會出現交付資料庫中的任何變更。   

在 DocumentDB，JavaScript 被裝載在相同的記憶體空間的資料庫。 因此，預存程序中所做的要求，然後執行相同的資料庫工作階段範圍中的引動程序。 這可讓 DocumentDB 保證 ACID 單一預存程序/觸發程序的一部分的所有作業。 請考慮下列預存程序定義︰

    // JavaScript source code
    var exchangeItemsSproc = {
        name: "exchangeItems",
        body: function (playerId1, playerId2) {
            var context = getContext();
            var collection = context.getCollection();
            var response = context.getResponse();
    
            var player1Document, player2Document;
    
            // query for players
            var filterQuery = 'SELECT * FROM Players p where p.id  = "' + playerId1 + '"';
            var accept = collection.queryDocuments(collection.getSelfLink(), filterQuery, {},
                function (err, documents, responseOptions) {
                    if (err) throw new Error("Error" + err.message);
    
                    if (documents.length != 1) throw "Unable to find both names";
                    player1Document = documents[0];
    
                    var filterQuery2 = 'SELECT * FROM Players p where p.id = "' + playerId2 + '"';
                    var accept2 = collection.queryDocuments(collection.getSelfLink(), filterQuery2, {},
                        function (err2, documents2, responseOptions2) {
                            if (err2) throw new Error("Error" + err2.message);
                            if (documents2.length != 1) throw "Unable to find both names";
                            player2Document = documents2[0];
                            swapItems(player1Document, player2Document);
                            return;
                        });
                    if (!accept2) throw "Unable to read player details, abort ";
                });
    
            if (!accept) throw "Unable to read player details, abort ";
    
            // swap the two players’ items
            function swapItems(player1, player2) {
                var player1ItemSave = player1.item;
                player1.item = player2.item;
                player2.item = player1ItemSave;
    
                var accept = collection.replaceDocument(player1._self, player1,
                    function (err, docReplaced) {
                        if (err) throw "Unable to update player 1, abort ";
    
                        var accept2 = collection.replaceDocument(player2._self, player2,
                            function (err2, docReplaced2) {
                                if (err) throw "Unable to update player 2, abort"
                            });
    
                        if (!accept2) throw "Unable to update player 2, abort";
                    });
    
                if (!accept) throw "Unable to update player 1, abort";
            }
        }
    }
    
    // register the stored procedure in Node.js client
    client.createStoredProcedureAsync(collection._self, exchangeItemsSproc)
        .then(function (response) {
            var createdStoredProcedure = response.resource;
        }
    );

此預存程序會使用貿易兩個人在單一作業之間的項目遊戲應用程式。 閱讀做為引數中每個對應至播放程式識別碼傳遞的兩個文件嘗試預存程序。 如果找不到兩個播放程式文件，預存程序會更新文件以交換他們的項目。 如果過程發生任何錯誤，就會擲回隱含中止交易 JavaScript 例外狀況。

如果預存程序的集合註冊針對單一資料分割集合，就交易的範圍是集合內的所有 docuemnts。 如果已分割的集合，接著預存程序來執行單一磁碟分割索引鍵的交易範圍。 每個預存程序執行然後必須包含對應至交易必須下執行的範圍的磁碟分割索引鍵的值。 如需詳細資訊，請參閱[DocumentDB 分割](documentdb-partition-data.md)。

### <a name="commit-and-rollback"></a>認可和復原
交易已經深層和自然地整合 DocumentDB 的 JavaScript 程式設計模型。 JavaScript 函數，在所有作業自動換都行單一的交易下。 JavaScript 完成沒有任何例外，資料庫的作業已確認。 作用中的 「 開始交易 」 和 「 認可交易 」 陳述式關聯式資料庫中會隱含 DocumentDB 中。  
 
如果有任何指令碼從傳送的例外狀況，DocumentDB 的 JavaScript 執行階段會復原整個交易。 先前範例所示，擲回例外狀況相當有效 DocumentDB 中的 [復原交易 」。
 
### <a name="data-consistency"></a>資料的一致性
預存程序與觸發程序一律會在 DocumentDB 集合的主要複本上執行。 如此一來，可確保內從讀取預存程序提供強式一致性。 使用使用者定義的函數查詢都能執行的主要或任何次要複本，但我們確保選擇適當的複本以符合要求的一致性層級。

## <a name="bounded-execution"></a>受限制的執行
所有 DocumentDB 作業必須都完成中指定的伺服器要求逾時持續時間。 這項限制式也適用於 JavaScript 函數 （預存程序、 引動程序和使用者定義函數）。 如果運算未完成的時間限制，被復原交易。 JavaScript 函數必須完成時間限制] 或 [實作有效的基礎接續模型，以批次繼續執行。  

以簡化開發的預存程序及處理時間限制的觸發程序，集合物件 （適用於建立、 讀取、 取代和刪除文件和附件的） 下的所有函數會都傳回一個 Boolean 值，表示該作業才會完成。 如果此值為 false，則表示時間限制即將到期，而且必須總結程序執行。  作業佇列的之前的第一個未經的儲存作業並保證完成如果預存程序完成時間，而不佇列任何要求。  

JavaScript 函數也會在資源消耗限制。 DocumentDB 保留處理量，每個集合根據能夠資料庫帳戶的大小。 處理量的 CPU、 記憶體和 IO 消耗稱為要求單位或 RUs 標準化單位表示。 JavaScript 函數可以使用向上 RUs 一段時間，內有很多，且可能會收到工資率限制如果達到集合的限制。 若要確保原始的資料庫作業時，可能也隔離資源大量的預存程序。  

### <a name="example-bulk-importing-data-into-a-database-program"></a>範例︰ 大量將資料庫程式匯入資料
以下是寫入大量匯入的文件集合中的預存程序的範例。 請注意 [預存程序如何處理界限的執行，請核取此布林值從 createDocument，傳回的值，然後使用 [追蹤並繼續進行跨批次的 [插入每個引動預存程序中的文件的計數。

    function bulkImport(docs) {
        var collection = getContext().getCollection();
        var collectionLink = collection.getSelfLink();
    
        // The count of imported docs, also used as current doc index.
        var count = 0;
    
        // Validate input.
        if (!docs) throw new Error("The array is undefined or null.");
    
        var docsLength = docs.length;
        if (docsLength == 0) {
            getContext().getResponse().setBody(0);
        }
    
        // Call the create API to create a document.
        tryCreate(docs[count], callback);
    
        // Note that there are 2 exit conditions:
        // 1) The createDocument request was not accepted. 
        //    In this case the callback will not be called, we just call setBody and we are done.
        // 2) The callback was called docs.length times.
        //    In this case all documents were created and we don’t need to call tryCreate anymore. Just call setBody and we are done.
        function tryCreate(doc, callback) {
            var isAccepted = collection.createDocument(collectionLink, doc, callback);
    
            // If the request was accepted, callback will be called.
            // Otherwise report current count back to the client, 
            // which will call the script again with remaining set of docs.
            if (!isAccepted) getContext().getResponse().setBody(count);
        }
    
        // This is called when collection.createDocument is done in order to process the result.
        function callback(err, doc, options) {
            if (err) throw err;
    
            // One more document has been inserted, increment the count.
            count++;
    
            if (count >= docsLength) {
                // If we created all documents, we are done. Just set the response.
                getContext().getResponse().setBody(count);
            } else {
                // Create next document.
                tryCreate(docs[count], callback);
            }
        }
    }

## <a id="trigger"></a>資料庫引動程序
### <a name="database-pre-triggers"></a>資料庫測試引動程序
DocumentDB 提供引動程序所執行或觸發的文件上的作業。 例如，您可以指定測試觸發程序，當您建立的文件 – 建立文件之前，會執行此測試觸發程序。 以下是如何使用預先引動程序驗證所建立的文件的屬性的範例︰

    var validateDocumentContentsTrigger = {
        name: "validateDocumentContents",
        body: function validate() {
            var context = getContext();
            var request = context.getRequest();
    
            // document to be created in the current operation
            var documentToCreate = request.getBody();
    
            // validate properties
            if (!("timestamp" in documentToCreate)) {
                var ts = new Date();
                documentToCreate["my timestamp"] = ts.getTime();
            }
    
            // update the document that will be created
            request.setBody(documentToCreate);
        },
        triggerType: TriggerType.Pre,
        triggerOperation: TriggerOperation.Create
    }


與對應的 Node.js 用戶端註冊程式碼觸發程序︰

    // register pre-trigger
    client.createTriggerAsync(collection.self, validateDocumentContentsTrigger)
        .then(function (response) {
            console.log("Created", response.resource);
            var docToCreate = {
                id: "DocWithTrigger",
                event: "Error",
                source: "Network outage"
            };
    
            // run trigger while creating above document 
            var options = { preTriggerInclude: "validateDocumentContents" };
    
            return client.createDocumentAsync(collection.self,
                  docToCreate, options);
        }, function (error) {
            console.log("Error", error);
        })
    .then(function (response) {
        console.log(response.resource); // document with timestamp property added
    }, function (error) {
        console.log("Error", error);
    });


測試引動程序不能有任何輸入的參數。 要求物件可用來管理與操作相關聯的要求訊息。 在這裡，測試觸發程序正在執行建立的文件，請使用，並要求郵件內文包含 JSON 格式建立的文件。   

當已登錄引動程序時，使用者可以指定其可在執行的作業。 使用 TriggerOperation.Create，這表示下列不允許所建立此觸發程序。

    var options = { preTriggerInclude: "validateDocumentContents" };
    
    client.replaceDocumentAsync(docToReplace.self,
                  newDocBody, options)
    .then(function (response) {
        console.log(response.resource);
    }, function (error) {
        console.log("Error", error);
    });
    
    // Fails, can’t use a create trigger in a replace operation

### <a name="database-post-triggers"></a>資料庫後引動程序
後的觸發程序，例如測試引動程序，與文件上的作業相關聯，而且不需要任何輸入的參數。 他們執行**之後**完成作業，並回覆郵件的傳送至用戶端存取。   

下列範例顯示動作後引動程序︰

    var updateMetadataTrigger = {
        name: "updateMetadata",
        body: function updateMetadata() {
            var context = getContext();
            var collection = context.getCollection();
            var response = context.getResponse();
    
            // document that was created
            var createdDocument = response.getBody();
    
            // query for metadata document
            var filterQuery = 'SELECT * FROM root r WHERE r.id = "_metadata"';
            var accept = collection.queryDocuments(collection.getSelfLink(), filterQuery,
                updateMetadataCallback);
            if(!accept) throw "Unable to update metadata, abort";
     
            function updateMetadataCallback(err, documents, responseOptions) {
                if(err) throw new Error("Error" + err.message);
                         if(documents.length != 1) throw 'Unable to find metadata document';
                         
                         var metadataDocument = documents[0];
                         
                         // update metadata
                         metadataDocument.createdDocuments += 1;
                         metadataDocument.createdNames += " " + createdDocument.id;
                         var accept = collection.replaceDocument(metadataDocument._self,
                               metadataDocument, function(err, docReplaced) {
                                      if(err) throw "Unable to update metadata, abort";
                               });
                         if(!accept) throw "Unable to update metadata, abort";
                         return;                    
            }                                                                                           
        },
        triggerType: TriggerType.Post,
        triggerOperation: TriggerOperation.All
    }


下列範例所示，可以註冊觸發程序。

    // register post-trigger
    client.createTriggerAsync('dbs/testdb/colls/testColl', updateMetadataTrigger)
        .then(function(createdTrigger) { 
            var docToCreate = { 
                name: "artist_profile_1023",
                artist: "The Band",
                albums: ["Hellujah", "Rotators", "Spinning Top"]
            };
    
            // run trigger while creating above document 
            var options = { postTriggerInclude: "updateMetadata" };
        
            return client.createDocumentAsync(collection.self,
                  docToCreate, options);
        }, function(error) {
            console.log("Error" , error);
        })
    .then(function(response) {
        console.log(response.resource); 
    }, function(error) {
        console.log("Error" , error);
    });


此觸發程序查詢中繼資料文件，並將其更新新建立的文件的詳細資料。  

注意的項目是**交易**中 DocumentDB 觸發程序執行。 此後的觸發程序執行做為與原始文件建立相同的交易的一部分。 因此，如果我們會引發例外狀況從後的觸發程序 （例如如果我們無法更新中繼資料文件），整個交易將會失敗，而且復原。 將會建立一個文件，並會傳回例外狀況。  

##<a id="udf"></a>使用者定義函數
使用者定義函數 (Udf) 用來延伸 DocumentDB SQL 查詢語言文法及實作自訂商務邏輯。 他們只能從呼叫內查詢。 他們無法存取內容物件，會用來作為計算專用 JavaScript。 因此，Udf 可以 DocumentDB 服務的次要複本上執行。  
 
下列範例會建立計算收入稅根據的各種收入括弧括住，UDF，然後再使用查詢內的若要尋找支付稅金超過 $20000 的所有人。

    var taxUdf = {
        name: "tax",
        body: function tax(income) {
    
            if(income == undefined) 
                throw 'no input';
    
            if (income < 1000) 
                return income * 0.1;
            else if (income < 10000) 
                return income * 0.2;
            else
                return income * 0.4;
        }
    }


UDF 就可用於如下列範例中的查詢︰

    // register UDF
    client.createUserDefinedFunctionAsync('dbs/testdb/colls/testColl', taxUdf)
        .then(function(response) { 
            console.log("Created", response.resource);
    
            var query = 'SELECT * FROM TaxPayers t WHERE udf.tax(t.income) > 20000'; 
            return client.queryDocuments('dbs/testdb/colls/testColl',
                   query).toArrayAsync();
        }, function(error) {
            console.log("Error" , error);
        })
    .then(function(response) {
        var documents = response.feed;
        console.log(response.resource); 
    }, function(error) {
        console.log("Error" , error);
    });

## <a name="javascript-language-integrated-query-api"></a>JavaScript 語言整合查詢 API
除了發行使用 DocumentDB 的 SQL 文法查詢，伺服器端 SDK 可讓您執行最佳化使用 fluent JavaScript 介面不必 SQL 瞭解的查詢。 通話 JavaScript 查詢 API 可讓您以程式設計方式建立查詢傳送到 chainable 函數的述詞函數語法熟悉 ECMAScript5 的陣列 built-ins 和 lodash 等的常用 JavaScript 文件庫。 查詢會剖析 JavaScript 執行階段執行有效率地使用 DocumentDB 的索引。

> [AZURE.NOTE]`__` （雙底線） 是以別名`getContext().getCollection()`。
> <br/>
> 換句話說，您可以使用`__`或`getContext().getCollection()`JavaScript 查詢 API 的存取。

支援的功能包括︰
<ul>
<li>
<b>chain()...。值 ([回撥] [，選項])</b>
<ul>
<li>
必須結束鏈結的呼叫開頭為 value （）。
</li>
</ul>
</li>
<li>
<b>篩選 (predicateFunction [，選項] [，回撥])</b>
<ul>
<li>
篩選輸入使用述詞函數會傳回 true/false，才能將結果集篩選內部/外部輸入文件。 與 round 類似 WHERE 子句中。
</li>
</ul>
</li>
<li>
<b>地圖 (transformationFunction [，選項] [，回撥])</b>
<ul>
<li>
適用於指定轉換函數會將每個輸入的項目對應到 JavaScript 物件或值的預測。 與 round 類似 SELECT 子句中。
</li>
</ul>
</li>
<li>
<b>pluck ([propertyName] [，選項] [，回撥])</b>
<ul>
<li>
這是對應的每個輸入的項目算起的單一屬性值的快速鍵。
</li>
</ul>
</li>
<li>
<b>簡維 ([isShallow] [，選項] [，回撥])</b>
<ul>
<li>
結合，以及簡維陣列中各輸入項目從一個陣列。 與 round 類似 linq SelectMany。
</li>
</ul>
</li>
<li>
<b>sortBy ([述詞] [，選項] [，回撥])</b>
<ul>
<li>
排序輸入文件資料流以遞增順序使用指定的述詞中的文件產生一組新的文件。 與 round 類似 ORDER BY 子句中。
</li>
</ul>
</li>
<li>
<b>sortByDescending ([述詞] [，選項] [，回撥])</b>
<ul>
<li>
排序在輸入文件中的資料流遞減順序使用指定的述詞中的文件產生一組新的文件。 與 round 類似 x DESC ORDER BY 子句在 SQL 中。
</li>
</ul>
</li>
</ul>


包含內述詞及/或選取器函數時，下列 JavaScript 建構取得自動最佳化，可以直接在 DocumentDB 索引上執行︰

* 簡單的運算子: = +-* / %|^ &amp; == != === !=== &lt; &gt; &lt;= &gt;= ||&amp;&amp; &lt;&lt; &gt;&gt; &gt;&gt;&gt;! ~
* 串連，包括物件常值: {}
* var 與傳回

下列 JavaScript 架構，就不取得適合 DocumentDB 索引︰

* 控制流程 (例如如果時)
* 函數呼叫

如需詳細資訊，請參閱我們的[伺服器端 JSDocs](http://azure.github.io/azure-documentdb-js-server/)。

### <a name="example-write-a-stored-procedure-using-the-javascript-query-api"></a>範例︰ 撰寫查詢 JavaScript API 的預存程序

下列程式碼範例是如何使用 JavaScript 查詢 API，預存程序的內容中的範例。 插入文件，請輸入參數，由預存程序，並更新中繼資料文件、 使用`__.filter()`minSize、 maxSize，與 totalSize 根據輸入文件的 [大小] 屬性的方法。

    /**
     * Insert actual doc and update metadata doc: minSize, maxSize, totalSize based on doc.size.
     */
    function insertDocumentAndUpdateMetadata(doc) {
      // HTTP error codes sent to our callback funciton by DocDB server.
      var ErrorCode = {
        RETRY_WITH: 449,
      }

      var isAccepted = __.createDocument(__.getSelfLink(), doc, {}, function(err, doc, options) {
        if (err) throw err;

        // Check the doc (ignore docs with invalid/zero size and metaDoc itself) and call updateMetadata.
        if (!doc.isMetadata && doc.size > 0) {
          // Get the meta document. We keep it in the same collection. it's the only doc that has .isMetadata = true.
          var result = __.filter(function(x) {
            return x.isMetadata === true
          }, function(err, feed, options) {
            if (err) throw err;

            // We assume that metadata doc was pre-created and must exist when this script is called.
            if (!feed || !feed.length) throw new Error("Failed to find the metadata document.");

            // The metadata document.
            var metaDoc = feed[0];

            // Update metaDoc.minSize:
            // for 1st document use doc.Size, for all the rest see if it's less than last min.
            if (metaDoc.minSize == 0) metaDoc.minSize = doc.size;
            else metaDoc.minSize = Math.min(metaDoc.minSize, doc.size);

            // Update metaDoc.maxSize.
            metaDoc.maxSize = Math.max(metaDoc.maxSize, doc.size);

            // Update metaDoc.totalSize.
            metaDoc.totalSize += doc.size;

            // Update/replace the metadata document in the store.
            var isAccepted = __.replaceDocument(metaDoc._self, metaDoc, function(err) {
              if (err) throw err;
              // Note: in case concurrent updates causes conflict with ErrorCode.RETRY_WITH, we can't read the meta again 
              //       and update again because due to Snapshot isolation we will read same exact version (we are in same transaction).
              //       We have to take care of that on the client side.
            });
            if (!isAccepted) throw new Error("replaceDocument(metaDoc) returned false.");
          });
          if (!result.isAccepted) throw new Error("filter for metaDoc returned false.");
        }
      });
      if (!isAccepted) throw new Error("createDocument(actual doc) returned false.");
    }

## <a name="sql-to-javascript-cheat-sheet"></a>Javascript 速查表 SQL
下表提供各種不同的 SQL 查詢及相對應的 JavaScript 查詢。

為 SQL 查詢的文件屬性索引鍵 (例如`doc.id`) 會區分大小寫。

<br/>
<table border="1" width="100%">
<colgroup>
<col span="1" style="width: 40%;">
<col span="1" style="width: 40%;">
<col span="1" style="width: 20%;">
</colgroup>
<tbody>
<tr>
<th>SQL</th>
<th>JavaScript 查詢 API</th>
<th>詳細資料</th>
</tr>
<tr>
<td>
<pre>
選取 [* 從文件
</pre>
</td>
<td>
<pre>
__.map(function(doc) {傳回的文件;)。
</pre>
</td>
<td>在所有文件 （分頁與接續權杖） 為結果是。</td>
</tr>
<tr>
<td>
<pre>
選取 docs.id、 docs.message 為訊息，docs.actions 從文件
</pre>
</td>
<td>
<pre>
__.map(function(doc) {傳回 {識別碼︰ doc.id、 訊息︰ doc.message、 動作︰ doc.actions};})。
</pre>
</td>
<td>專案識別碼、 訊息 （的別名訊息），以及將所有文件中的動作。</td>
</tr>
<tr>
<td>
<pre>
選取 [* 文件從何處 docs.id="X998_Y998 」
</pre>
</td>
<td>
<pre>
__.filter(function(doc) {傳回 doc.id === 「 X998_Y998 」;})。
</pre>
</td>
<td>查詢述詞的文件︰ id = 「 X998_Y998 」。</td>
</tr>
<tr>
<td>
<pre>
選取 [* 從文件位置 ARRAY_CONTAINS(docs.標記，123）
</pre>
</td>
<td>
<pre>
__.filter(function(x) {傳回 x.Tags & & x.Tags.indexOf(123) >-1，})。
</pre>
</td>
<td>查詢，其中的標籤屬性和標記文件是陣列，其中包含 [123] 的值。</td>
</tr>
<tr>
<td>
<pre>
選取 docs.id、 docs.message 為訊息寄文件的位置 docs.id="X998_Y998 」
</pre>
</td>
<td>
<pre>
__.chain().filter(function(doc) {傳回 doc.id === 「 X998_Y998 」;}).map(function(doc) {傳回 {識別碼︰ doc.id、 訊息︰ doc.message};}).value();
</pre>
</td>
<td>查詢述詞，與文件識別碼 = 「 X998_Y998 」，然後專案的編號及郵件 （訊息的別名）。</td>
</tr>
<tr>
<td>
<pre>
選取 [值的文件加入標籤中的文件的標記。ORDER BY docs._ts 標籤
</pre>
</td>
<td>
<pre>
__.chain().filter(function(doc) {傳回文件。標籤 & & Array.isArray （文件。標記）。}）.sortBy(function(doc) {傳回 doc._ts;}).pluck("Tags").flatten().value()
</pre>
</td>
<td>文件，必須陣列屬性，標籤篩選和排序 _ts 時間戳記系統屬性，然後專案所產生的文件 + 簡維標籤陣列。</td>
</tr>
</tbody>
</table>

## <a name="runtime-support"></a>執行階段支援
[DocumentDB JavaScript 伺服器端 SDK](http://azure.github.io/azure-documentdb-js-server/)的最大的主要 JavaScript 語言功能，為，[例如 ECMA 262](http://www.ecma-international.org/publications/standards/Ecma-262.htm)標準化提供支援。

### <a name="security"></a>安全性
JavaScript 預存程序且引動程序沙箱化，好讓一指令碼的效果不要洩露到其他不透過快照交易隔離層級資料庫。 執行階段環境集區式，但在每次執行之後已清除的內容。 因此保證為彼此安全的任何非預期的側邊效果。

### <a name="pre-compilation"></a>預先編譯
預存程序、 引動程序和 Udf 是隱含先行位元組程式碼格式以一次的每個指令碼引動避免編譯成本。 這樣的預存程序叫用快速，而且有低所需。

## <a name="client-sdk-support"></a>用戶端 SDK 支援
除了[Node.js](documentdb-sdk-node.md)的用戶端，DocumentDB 支援[.NET](documentdb-sdk-dotnet.md)、 [Java](documentdb-sdk-java.md)、 [JavaScript](http://azure.github.io/azure-documentdb-js/)] 和[Python Sdk](documentdb-sdk-python.md)。 預存程序、 引動程序 Udf 可以建立並使用任何這些 Sdk 以及執行。 下列範例會示範如何建立和執行使用.NET 用戶端的預存程序。 請注意.NET 類型傳遞至 json 預存程序及讀出的方式。

    var markAntiquesSproc = new StoredProcedure
    {
        Id = "ValidateDocumentAge",
        Body = @"
                function(docToCreate, antiqueYear) {
                    var collection = getContext().getCollection();    
                    var response = getContext().getResponse();    
    
                    if(docToCreate.Year != undefined && docToCreate.Year < antiqueYear){
                        docToCreate.antique = true;
                    }
    
                    collection.createDocument(collection.getSelfLink(), docToCreate, {}, 
                        function(err, docCreated, options) { 
                            if(err) throw new Error('Error while creating document: ' + err.message);                              
                            if(options.maxCollectionSizeInMb == 0) throw 'max collection size not found'; 
                            response.setBody(docCreated);
                    });
            }"
    };
    
    // register stored procedure
    StoredProcedure createdStoredProcedure = await client.CreateStoredProcedureAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"), markAntiquesSproc);
    dynamic document = new Document() { Id = "Borges_112" };
    document.Title = "Aleph";
    document.Year = 1949;
    
    // execute stored procedure
    Document createdDocument = await client.ExecuteStoredProcedureAsync<Document>(UriFactory.CreateStoredProcedureUri("db", "coll", "sproc"), document, 1920);


此範例會示範如何使用[.NET SDK](https://msdn.microsoft.com/library/azure/dn948556.aspx)建立測試觸發程序，並啟用的觸發程序建立的文件。 

    Trigger preTrigger = new Trigger()
    {
        Id = "CapitalizeName",
        Body = @"function() {
            var item = getContext().getRequest().getBody();
            item.id = item.id.toUpperCase();
            getContext().getRequest().setBody(item);
        }",
        TriggerOperation = TriggerOperation.Create,
        TriggerType = TriggerType.Pre
    };
    
    Document createdItem = await client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"), new Document { Id = "documentdb" },
        new RequestOptions
        {
            PreTriggerInclude = new List<string> { "CapitalizeName" },
        });


然後，下列範例會示範如何建立使用者定義函數 (UDF)，並將其用於[DocumentDB SQL 查詢](documentdb-sql-query.md)。

    UserDefinedFunction function = new UserDefinedFunction()
    {
        Id = "LOWER",
        Body = @"function(input) 
        {
            return input.toLowerCase();
        }"
    };
    
    foreach (Book book in client.CreateDocumentQuery(UriFactory.CreateDocumentCollectionUri("db", "coll"),
        "SELECT * FROM Books b WHERE udf.LOWER(b.Title) = 'war and peace'"))
    {
        Console.WriteLine("Read {0} from query", book);
    }

## <a name="rest-api"></a>REST API
RESTful 的方式，就可以執行所有 DocumentDB 作業。 預存程序、 引動程序及使用者定義函數註冊集合下使用 HTTP 文章。 以下是如何註冊預存程序的範例︰

    POST https://<url>/sprocs/ HTTP/1.1
    authorization: <<auth>>
    x-ms-date: Thu, 07 Aug 2014 03:43:10 GMT
    
    
    var x = {
      "name": "createAndAddProperty",
      "body": function (docToCreate, addedPropertyName, addedPropertyValue) {
                var collectionManager = getContext().getCollection();
                collectionManager.createDocument(
                    collectionManager.getSelfLink(),
                    docToCreate,
                    function(err, docCreated) {
                      if(err) throw new Error('Error:  ' + err.message);
                      docCreated[addedPropertyName] = addedPropertyValue;
                      getContext().getResponse().setBody(docCreated);
                    });
            }
    }


本文包含預存程序可以建立與執行文章要求針對 URI 好處/藉由對 testdb/colls/testColl/預登錄預存程序。 觸發程序和 Udf 註冊同樣分別發出針對 /triggers 和 /udfs 文章。
此預存程序可以，然後執行發行文章要求針對其資源的連結︰

    POST https://<url>/sprocs/<sproc> HTTP/1.1
    authorization: <<auth>>
    x-ms-date: Thu, 07 Aug 2014 03:43:20 GMT
    
    
    [ { "name": "TestDocument", "book": "Autumn of the Patriarch"}, "Price", 200 ]


在這裡，預存程序的輸入會傳送邀請內文中。 請注意，輸入會傳送以 JSON 陣列的輸入參數。 預存程序所需的第一個輸入的時間做為回應主體的文件。 我們會收到的回應如下所示︰

    HTTP/1.1 200 OK
     
    { 
      name: 'TestDocument',
      book: ‘Autumn of the Patriarch’,
      id: ‘V7tQANV3rAkDAAAAAAAAAA==‘,
      ts: 1407830727,
      self: ‘dbs/V7tQAA==/colls/V7tQANV3rAk=/docs/V7tQANV3rAkDAAAAAAAAAA==/’,
      etag: ‘6c006596-0000-0000-0000-53e9cac70000’,
      attachments: ‘attachments/’,
      Price: 200
    }


無法直接執行引動程序，與不同的預存程序。 請改為執行的作業上的文件的一部分。 我們可以指定執行要求使用 HTTP 標頭的觸發程序。 以下是建立文件的邀請。

    POST https://<url>/docs/ HTTP/1.1
    authorization: <<auth>>
    x-ms-date: Thu, 07 Aug 2014 03:43:10 GMT
    x-ms-documentdb-pre-trigger-include: validateDocumentContents 
    x-ms-documentdb-post-trigger-include: bookCreationPostTrigger
    
    
    {
       "name": "newDocument",
       “title”: “The Wizard of Oz”,
       “author”: “Frank Baum”,
       “pages”: 92
    }


以下 x-ms-documentdb-pre-trigger-include 標頭中指定測試觸發程序執行要求。 因此，任何後續引動程序會提供 x-ms-documentdb-post-trigger-include 標題中。 請注意，同時測試並指定要求您可以指定後續引動程序。

## <a name="sample-code"></a>程式碼範例

您可以在我們[Github 存放庫](https://github.com/Azure/azure-documentdb-js-server/tree/master/samples)，以找到更多的伺服器端程式碼範例 （包括[大量刪除](https://github.com/Azure/azure-documentdb-js-server/tree/master/samples/stored-procedures/bulkDelete.js)]，並[更新](https://github.com/Azure/azure-documentdb-js-server/tree/master/samples/stored-procedures/update.js)）。

想要共用您實用的預存程序？ 請傳送給我們提取要求 ！ 

## <a name="next-steps"></a>後續步驟

一旦您有一個或多個預存程序、 引動程序，並建立使用者定義函數，您可以載入，並使用指令碼總管 Azure 入口網站中檢視。 如需詳細資訊，請參閱[檢視預存程序、 引動程序，以及使用 [DocumentDB 指令碼總管] 中的使用者定義函數](documentdb-view-scripts.md)。

您可能也有用下列參照和資源，若要進一步瞭解 DocumentDB 伺服器端程式設計路徑中︰

- [Azure DocumentDB Sdk](https://msdn.microsoft.com/library/azure/dn781482.aspx)
- [DocumentDB Studio](https://github.com/mingaliu/DocumentDBStudio/releases)
- [JSON](http://www.json.org/) 
- [JavaScript ECMA 262](http://www.ecma-international.org/publications/standards/Ecma-262.htm)
- [JavaScript – JSON 類型系統](http://www.json.org/js.html) 
- [安全與可攜式資料庫擴充功能](http://dl.acm.org/citation.cfm?id=276339) 
- [服務導向資料庫結構](http://dl.acm.org/citation.cfm?id=1066267&coll=Portal&dl=GUIDE) 
- [裝載於 Microsoft SQL server.NET 執行階段](http://dl.acm.org/citation.cfm?id=1007669)
