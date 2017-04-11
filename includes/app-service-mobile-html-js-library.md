##<a name="create-client"></a>建立用戶端連線

建立用戶端連線建立`WindowsAzure.MobileServiceClient`物件。  取代`appUrl`與您的行動應用程式的 URL。

```
var client = WindowsAzure.MobileServiceClient(appUrl);
```

##<a name="table-reference"></a>使用表格

Access 或更新的資料，建立後端表格的參照。 取代`tableName`的表格名稱

```
var table = client.getTable(tableName);
```

表格參照之後，您可以使用其他資料表的情況︰

* [查詢的資料表](#querying)
  * [篩選資料](#table-filter)
  * [瀏覽資料分頁](#table-paging)
  * [排序資料](#sorting-data)
* [插入資料](#inserting)
* [修改資料](#modifying)
* [刪除資料](#deleting)

###<a name="querying"></a>如何︰ 查詢表格參照

表格參照之後，您可以使用它來查詢在伺服器上的資料。  查詢所做的 「 LINQ 喜歡 」 的語言。
若要傳回資料表的所有資料，請使用下列步驟︰

```
/**
 * Process the results that are received by a call to table.read()
 *
 * @param {Object} results the results as a pseudo-array
 * @param {int} results.length the length of the results array
 * @param {Object} results[] the individual results
 */
function success(results) {
   var numItemsRead = results.length;

   for (var i = 0 ; i < results.length ; i++) {
       var row = results[i];
       // Each row is an object - the properties are the columns
   }
}

function failure(error) {
    throw new Error('Error loading data: ', error);
}

table
    .read()
    .then(success, failure);
```

成功函數稱為結果。   不要使用`for (var i in results)`的成功函數時，會反覆運算結果中所包含的資訊時其他查詢函數 (例如`.includeTotalCount()`) 使用。

如需查詢語法的詳細資訊，請參閱 [查詢物件文件]。

####<a name="table-filter"></a>在伺服器上的篩選資料

您可以使用`where`在表格參照子句︰

```
table
    .where({ userId: user.userId, complete: false })
    .read()
    .then(success, failure);
```

您也可以使用篩選物件的函數。  在此情況下`this`變數指派給目前正在進行篩選的物件。  以下是功能相當於前一個範例︰

```
function filterByUserId(currentUserId) {
    return this.userId === currentUserId && this.complete === false;
}

table
    .where(filterByUserId, user.userId)
    .read()
    .then(success, failure);
```

####<a name="table-paging"></a>瀏覽資料分頁

利用 take() 和 skip() 方法。  例如，如果您想要將資料表分割為 100 個資料列記錄︰

```
var totalCount = 0, pages = 0;

// Step 1 - get the total number of records
table.includeTotalCount().take(0).read(function (results) {
    totalCount = results.totalCount;
    pages = Math.floor(totalCount/100) + 1;
    loadPage(0);
}, failure);

function loadPage(pageNum) {
    let skip = pageNum * 100;
    table.skip(skip).take(100).read(function (results) {
        for (var i = 0 ; i < results.length ; i++) {
            var row = results[i];
            // Process each row
        }
    }
}
```

`.includeTotalCount()`方法來新增 totalCount 欄位至結果的物件。  使用不分頁時要傳回的記錄的總數填滿 totalCount 欄位。

您可以然後使用頁面變數及部分使用者介面按鈕提供頁面清單;使用 loadPage() 載入每個頁面的新記錄。  您應該執行的快取以速度的存取權已載入的記錄。


####<a name="sorting-data"></a>如何︰ 傳回資料排序

使用.orderBy() 或.orderByDescending() 查詢方法︰

```
table
    .orderBy('name')
    .read()
    .then(success, failure);
```

如需查詢物件的詳細資訊，請參閱 [查詢物件文件]。

###<a name="inserting"></a>如何︰ 插入資料

建立具有適當的日期 JavaScript 物件和非同步呼叫 table.insert():

```
var newItem = {
    name: 'My Name',
    signupDate: new Date()
};

table
    .insert(newItem)
    .done(function (insertedItem) {
        var id = insertedItem.id;
    }, failure);
```

在成功插入，插入的項目會傳回這些額外的欄位所需的同步處理作業。  您應該稍後更新此資訊來更新您自己的快取。

請注意 Azure 行動應用程式 Node.js 伺服器 SDK 的支援開發用途動態結構描述。
動態結構描述，如果表格的結構描述會更新即時，讓您新增欄至表格，只要在插入或更新的作業。  我們建議您在移動至生產環境的應用程式之前關閉動態結構描述。

###<a name="modifying"></a>如何︰ 修改資料

.Insert() 方法類似，您應該建立更新物件，然後呼叫.update()。  更新物件必須包含要更新的記錄 ID-閱讀記錄時，或撥打.insert() 時，這取得。

```
var updateItem = {
    id: '7163bc7a-70b2-4dde-98e9-8818969611bd',
    name: 'My New Name'
};

table
    .update(updateItem)
    .done(function (updatedItem) {
        // You can now update your cached copy
    }, failure);
```

###<a name="deleting"></a>如何︰ 刪除資料

呼叫.del() 方法若要刪除一筆記錄。  在物件參照傳遞識別碼︰

```
table
    .del({ id: '7163bc7a-70b2-4dde-98e9-8818969611bd' })
    .done(function () {
        // Record is now deleted - update your cache
    }, failure);
```
