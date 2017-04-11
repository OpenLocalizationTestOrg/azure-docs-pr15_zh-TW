<properties
    pageTitle="如何使用 Android 行動應用程式的用戶端程式庫"
    description="如何使用 Android 的用戶端 SDK Azure Mobile 應用程式。"
    services="app-service\mobile"
    documentationCenter="android"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="yuaxu"/>


# <a name="how-to-use-the-android-client-library-for-mobile-apps"></a>如何使用 Android 的用戶端文件庫的行動應用程式

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

本指南為您示範如何使用 Android 的用戶端 SDK 的行動應用程式，例如實作常見的案例、:

- 查詢資料 （插入、 更新和刪除）。
- 驗證。
- 處理錯誤。
- 自訂用戶端。 

也會深度探討常見最行動應用程式中使用的用戶端程式碼。

本指南著重於用戶端 Android SDK。  若要進一步瞭解伺服器端 Sdk 的行動應用程式，請參閱[使用.NET 後端 SDK] [10]或[如何使用 Node.js 後端 SDK][11]。

## <a name="reference-documentation"></a>參照文件

您可以找到[Javadocs API 參考][12]上 GitHub Android 的用戶端文件庫。

## <a name="supported-platforms"></a>支援的平台

Azure 行動應用程式在 Android SDK 支援 API 層級 19 到 24 (透過 Nougat KitKat)。  

「 伺服器流程 「 驗證用於呈現的使用者介面的網頁檢視。 如果裝置無法呈現網頁檢視使用者介面，然後驗證的其他方法所需產品的範圍之外。  此 SDK 不適合用於監看型態或同樣在受限制的裝置。

## <a name="setup-and-prerequisites"></a>設定與先決條件

完成[Mobile 應用程式快速入門](app-service-mobile-android-get-started.md)教學課程。  這項工作可確保符合所有的必要條件開發 Azure 行動應用程式。  快速入門也可協助您設定您的帳戶，並建立您的第一個 Mobile 應用程式後端。

如果您決定不要完成快速入門教學課程，請完成下列工作︰

- [建立行動應用程式後端][13]與您的 Android 應用程式搭配使用。
- 在 Android Studio[更新 Gradle 建立檔案](#gradle-build)。
- [啟用網際網路權限](#enable-internet)。

###<a name="gradle-build"></a>更新 Gradle 建立檔案

變更兩個**build.gradle**檔案︰

1. 將此程式碼新增至 [ *buildscript*標籤內的*專案*層級**build.gradle**檔案︰

        buildscript {
            repositories {
                jcenter()
            }
        }

2. 將此程式碼新增至 [*相依性*標籤內的*模組應用程式*層級**build.gradle**檔案︰

        compile 'com.microsoft.azure:azure-mobile-android:3.1.0'

    目前的最新版本是 3.1.0。 支援的版本會列出[以下][14]。

###<a name="enable-internet"></a>啟用網際網路權限

若要存取 Azure，您的應用程式必須啟用的網際網路權限。 如果尚未啟用，請將下列程式碼新增至**AndroidManifest.xml**檔案︰

    <uses-permission android:name="android.permission.INTERNET" />

## <a name="the-basics-deep-dive"></a>基本概念深度剖析

本節將說明適用於使用 Azure 行動應用程式快速入門應用程式中的程式碼的部分。  

###<a name="data-object"></a>定義用戶端的資料類別

若要存取 SQL Azure 資料表的資料，定義對應的用戶端資料類別中的行動應用程式後端的資料表。 本主題中的範例假設**ToDoItem**，有下列資料行的資料表︰

- 識別碼
- 文字
- 完成

對應輸入用戶端物件︰

    public class ToDoItem {
        private String id;
        private String text;
        private Boolean complete;
    }

程式碼位於稱為**ToDoItem.java**檔案中。

如果您的 SQL Azure 資料表包含多個資料行，您可以新增此類別的相對應的欄位。  例如，如果 DTO （資料傳輸物件） 有整數優先順序資料行，然後您可以加入此欄位，以及其 getter 和設定器的方法︰

    private Integer priority;

    /**
    * Returns the item priority
    */
    public Integer getPriority() {
        return mPriority;
    }
    
    /**
    * Sets the item priority
    *
    * @param priority
    *            priority to set
    */
    public final void setPriority(Integer priority) {
        mPriority = priority;
    }

若要瞭解如何在您的行動應用程式後端中建立其他的資料表，請參閱[如何︰ 定義表格控制器][ 15] （.NET 後端） 或[定義資料表使用動態結構描述][ 16] （Node.js 後端）。 針對 Node.js 後端，您也可以使用**簡單的表格**設定[Azure 入口網站]中。

###<a name="create-client"></a>如何︰ 建立用戶端內容

將此程式碼會建立用來存取您的行動應用程式後端**MobileServiceClient**物件。 程式碼會`onCreate`**活動**類別中*AndroidManifest.xml*指定為**主要**巨集指令和**啟動器**類別的方法。 快速入門程式碼，它會進入**ToDoActivity.java**檔案中。

        MobileServiceClient mClient = new MobileServiceClient(
            "MobileAppUrl", // Replace with the Site URL
            this)

在這段程式碼，取代`MobileAppUrl`與您的行動應用程式後端的 URL，其中，請參閱[Azure 入口網站]中為您的行動應用程式後端刀。 用來編譯程式碼的這一行，您也需要新增下列**匯入**陳述式︰

    import com.microsoft.windowsazure.mobileservices.*;

###<a name="instantiating"></a>如何︰ 建立表格參照

查詢或修改資料後端最簡單的方法是使用*所輸入的程式設計模型*、 Java 強的語言。 此模型提供順暢 JSON 序列化和還原序列化使用[gson] [3]後端 Azure SQL 傳送用戶端物件和資料表之間的資料時的文件庫。

若要存取表格，請先建立[MobileServiceTable] [8]物件，則可電話**不**上[MobileServiceClient]方法[9]。  這個方法有兩個多載︰

    public class MobileServiceClient {
        public <E> MobileServiceTable<E> getTable(Class<E> clazz);
        public <E> MobileServiceTable<E> getTable(String name, Class<E> clazz);
    }

下列程式碼， **mClient**是 MobileServiceClient 物件的參照。  使用第一個超量的狀況位置的類別名稱及表格名稱相同，而其中一個中使用快速入門︰

    MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable(ToDoItem.class);

不同的類別名稱的資料表名稱時，會使用第二個超量的狀況︰ 第一個參數是資料表名稱。

    MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);

###<a name="binding"></a>如何︰ 資料繫結至使用者介面

資料繫結包含三個元件︰

- 資料來源
- 畫面版面配置
- 緊密整合兩個卡。

在我們的範例中，我們將資料傳回行動應用程式 SQL Azure 資料表**ToDoItem**一個陣列。 此活動是資料應用程式的常見模式。  資料庫查詢通常會傳回用戶端取得在清單或陣列中的資料列集合。 在此範例中，陣列是資料來源。

程式碼指定在裝置定義所顯示的資料檢視的畫面版面配置。  兩個介面卡，這在這個程式碼是副檔名與繫結的**ArrayAdapter&lt;ToDoItem&gt;**類別。

#### <a name="layout"></a>如何︰ 定義版面配置

由數個的 XML 程式碼片段定義版面配置。 指定現有的版面配置，下列程式碼，代表我們想要填入我們 server 資料的**清單檢視**。

    <ListView
        android:id="@+id/listViewToDo"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        tools:listitem="@layout/row_list_to_do" >
    </ListView>

在前面的程式碼， *listitem*屬性會指定個別資料列在清單中的版面配置的識別碼。 將此程式碼指定核取方塊，以及其相關聯的文字，然後在清單中的每個項目會一次產生。 此版面配置並不會顯示 [**識別碼**] 欄位中，並更複雜的版面配置會指定其他欄位在顯示]。 將此程式碼是**row_list_to_do.xml**檔案中。

    <?xml version="1.0" encoding="utf-8"?>
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="horizontal">
        <CheckBox
            android:id="@+id/checkToDoItem"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="@string/checkbox_text" />
    </LinearLayout>


#### <a name="adapter"></a>如何︰ 定義介面卡

因為我們檢視的資料來源是陣列**ToDoItem**，我們子類別從我們介面卡**ArrayAdapter&lt;ToDoItem&gt;**類別。 此子類別產生檢視的使用**row_list_to_do**版面配置的每個**ToDoItem** 。

在我們的程式碼中，我們定義的延伸下列類別**ArrayAdapter&lt;E&gt;**類別︰

    public class ToDoItemAdapter extends ArrayAdapter<ToDoItem> {

覆寫介面卡**getView**方法。 例如︰

    @Override
    public View getView(int position, View convertView, ViewGroup parent) {
        View row = convertView;

        final ToDoItem currentItem = getItem(position);

        if (row == null) {
            LayoutInflater inflater = ((Activity) mContext).getLayoutInflater();
            row = inflater.inflate(R.layout.row_list_to_do, parent, false);
        }

        row.setTag(currentItem);

        final CheckBox checkBox = (CheckBox) row.findViewById(R.id.checkToDoItem);
        checkBox.setText(currentItem.getText());
        checkBox.setChecked(false);
        checkBox.setEnabled(true);

        checkBox.setOnClickListener(new View.OnClickListener() {

            @Override
            public void onClick(View arg0) {
                if (checkBox.isChecked()) {
                    checkBox.setEnabled(false);
                    if (mContext instanceof ToDoActivity) {
                        ToDoActivity activity = (ToDoActivity) mContext;
                        activity.checkItem(currentItem);
                    }
                }
            }
        });


        return row;
    }

我們建立課程此執行的個體我們活動，如下所示︰

    ToDoItemAdapter mAdapter;
    mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);

ToDoItemAdapter 建構函式的第二個參數是版面配置的參照。 現在，我們可以產生**清單檢視**，並將介面卡指派給**清單檢視**。

    ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
    listViewToDo.setAdapter(mAdapter);

### <a name="api"></a>API 結構

行動應用程式表格作業和自訂 API 呼叫是非同步。 使用與有關查詢、 插入、 更新和刪除非同步方法在[未來]和[AsyncTask]物件。 使用 future 可執行多個背景執行緒作業，而不需要處理多個巢狀的回撥變得更容易。

檢閱**ToDoActivity.java**專案中的檔案 Android 的快速入門從[Azure 入口網站]的範例。

#### <a name="use-adapter"></a>如何︰ 使用介面卡

您已準備好要使用資料繫結。 下列程式碼會顯示如何在資料表中的項目，並傳回的項目使用填滿本機介面卡。

    public void showAll(View view) {
        AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final List<ToDoItem> results = mToDoTable.execute().get();
                    runOnUiThread(new Runnable() {

                        @Override
                        public void run() {
                            mAdapter.clear();
                            for (ToDoItem item : results) {
                                mAdapter.add(item);
                            }
                        }
                    });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        };
        runAsyncTask(task);
    }

呼叫介面卡的隨時在您修改**ToDoItem**資料表。 完成修改筆的為基礎，因為您處理，而不是集合的單一資料列。 當您插入項目時，呼叫**新增**介面卡;當刪除呼叫**移除**方法。

##<a name="querying"></a>如何︰ 查詢您的行動應用程式後端的資料

本節說明如何發佈查詢 Mobile 應用程式後端，包括下列工作︰

- [傳回所有項目]
- [傳回資料的篩選]
- [傳回資料的排序]
- [在頁面中傳回的資料]
- [選取特定的資料行]
- [串連查詢方法](#chaining)

### <a name="showAll"></a>如何︰ 從資料表傳回的所有項目

下列查詢會傳回**ToDoItem**資料表中的所有項目。

    List<ToDoItem> results = mToDoTable.execute().get();

*結果*變數會傳回設定為清單查詢的結果。

### <a name="filtering"></a>如何︰ 篩選傳回的資料

執行下列動作查詢傳回的所有項目**ToDoItem**資料表中其中**完成**等於**false**。

    List<ToDoItem> result = mToDoTable.where()
                                .field("complete").eq(false)
                                .execute().get();

**mToDoTable**是我們先前所建立的行動訊息服務資料表的參考。

定義使用在表格參照的**位置**呼叫篩選。 **Where**方法後面**欄位**方法，後面接著指定邏輯述詞的方法。 可能的述詞方法包括**eq** （等於）、 **ne** （不等於）、 **gt** （大於）、 **ge** （大於或等於）、 **lt** （小於）， **le** （小於或等於）。 這些方法可讓您比較數字] 及 [字串欄位以特定的值。

您可以篩選日期。 以下兩種方法可讓您比較之日期的組件的整個日期欄位︰**年**、**月**、**天**、**小時**、**分鐘**和**第二個**。 下列範例會篩選的*到期日*的等於 2013年的項目。

    mToDoTable.where().year("due").eq(2013).execute().get();

以下兩種方法的字串欄位支援更複雜的篩選︰ **startsWith**、 **endsWith**、 **concat 函數**、**子字串**、 **indexOf**、**取代**、 **toLower**、 **toUpper**、**剪輯**，並**長度**。 下列範例會使用 「 PRI0。 」*文字*欄的開始位置的資料表資料列篩選

    mToDoTable.where().startsWith("text", "PRI0").execute().get();

數字欄位都支援下列運算子方法︰**新增**、**子**、 **mul**、 **div**、 **mod**、**樓面**、 **ceiling**，及**四捨五入**。 下列範例會篩選表格列位置的**工期**為偶數。

    mToDoTable.where().field("duration").mod(2).eq(0).execute().get();

您可以結合述詞與這些邏輯的方法: **，**或****與**不**。 下列範例會結合兩個一個範例。

    mToDoTable.where().year("due").eq(2013).and().startsWith("text", "PRI0")
                .execute().get();

分組和巢狀嵌入邏輯運算子︰

    mToDoTable.where()
                .year("due").eq(2013)
                    .and
                (startsWith("text", "PRI0").or().field("duration").gt(10))
                .execute().get();

更詳細的討論區及篩選的範例，請參閱[探索的 Android 的用戶端查詢的模型](http://hashtagfail.com/post/46493261719/mobile-services-android-querying)。

### <a name="sorting"></a>如何︰ 排序傳回的資料

下列程式碼會傳回的**ToDoItems**在資料表中的所有項目以遞增順序排列的 [*文字*] 欄位。 *mToDoTable*是您先前建立的後端資料表的參照︰

    mToDoTable.orderBy("text", QueryOrder.Ascending).execute().get();

[**排序方式**] 方法的第一個參數是欄位的等於要排序名稱的字串。 第二個參數使用**QueryOrder**列舉指定是否要遞增或遞減順序排序。  如果您要篩選的使用***位置***的方法， ***where***方法必須***orderBy***方法之前叫用。

### <a name="paging"></a>如何︰ 在頁面中傳回的資料

第一個範例會示範如何從表格中選取前五個項目。 查詢會傳回**ToDoItems**目錄中的項目。 **mToDoTable**是您先前建立的後端資料表的參照︰

    List<ToDoItem> result = mToDoTable.top(5).execute().get();


以下是略過的前五個項目，並傳回下一個五個的查詢︰

    mToDoTable.skip(5).top(5).execute().get();

### <a name="selecting"></a>如何︰ 選取特定的資料行

下列程式碼會說明如何從目錄**ToDoItems**，傳回的所有項目，但只會顯示**完成**] 和 [**文字**欄位。 **mToDoTable**是我們先前所建立的後端資料表的參考。

    List<ToDoItemNarrow> result = mToDoTable.select("complete", "text").execute().get();

選取函數的參數是您想要傳回的資料表的資料行字串名稱。

**選取**方法需要追蹤方法，例如**排序方式**及**位置**。 它可以後面的分頁方法，例如**頂端**。

### <a name="chaining"></a>如何︰ 串連查詢方法

可以串連查詢後端的資料表中所使用的方法。 鏈結查詢方法可讓您選取特定的資料行的篩選的資料列排序及分頁。 您可以建立更複雜的邏輯篩選。
每個查詢方法會傳回查詢物件。 若要結束的方法，並實際執行查詢，呼叫**執行**方法。 例如︰

    mToDoTable.where()
        .year("due").eq(2013)
        .and().startsWith("text", "PRI0")
        .or().field("duration").gt(10)
        .orderBy(duration, QueryOrder.Ascending)
        .select("id", "complete", "text", "duration")
        .top(20)
        .execute().get();

鏈結的查詢方法的順序必須，如下所示︰

1. 篩選 （**位置**） 的方法。
2. 排序 (**orderBy**) 的方法。
3. （**選取**） 的選取範圍的方法。
4. 分頁 （**略過**和**頂端**） 方法。

##<a name="inserting"></a>如何︰ 插入後端的資料

產生*ToDoItem*類別的執行個體，並設定其內容。

    ToDoItem item = new ToDoItem();
    item.text = "Test Program";
    item.complete = false;

若要插入的物件，然後使用**insert()** :

    ToDoItem entity = mToDoTable.insert(item).get();

傳回的實體相符項目插入 [後端] 資料表中的資料包含 [識別碼] 和 [後端上設定的其他值。

行動應用程式的資料表都需要主索引鍵資料行名稱為**識別碼**。 根據預設，此欄是一個字串。 [識別碼] 欄的預設值是 GUID。  您可以提供其他唯一值，例如電子郵件地址] 或 [使用者名稱。 當插入記錄未提供的字串識別碼值時後, 端會產生新的 GUID。

字串識別碼值會提供下列優點︰

+ 可能會產生識別碼，而不需要往返資料庫。
+ 記錄是合併來自不同資料表或資料庫的工作變得更容易。
+ 識別碼值更有效地整合應用程式的邏輯。

字串識別碼值是離線同步處理支援**REQUIRED** 。

##<a name="updating"></a>如何︰ 在行動應用程式中更新資料

若要更新表格中的資料，將新的物件傳遞**update()**方法。

    mToDoTable.update(item).get();

在此範例中，*項目*是一列於 [ *ToDoItem* ] 資料表已有一些變更的參照。
使用相同的**識別碼**列會更新。

##<a name="deleting"></a>如何︰ 刪除行動應用程式中的資料

下列程式碼會顯示如何刪除資料表中的資料，以指定的資料物件。

    mToDoTable.delete(item);

您也可以藉由指定 [要刪除的資料列**識別碼**] 欄位中刪除項目。

    String myRowId = "2FA404AB-E458-44CD-BC1B-3BC847EF0902";
    mToDoTable.delete(myRowId);

##<a name="lookup"></a>如何︰ 尋找特定項目

尋找有特定的**識別碼**] 欄位的項目以**Lookup**方法︰

    ToDoItem result = mToDoTable
                        .lookUp("0380BAFB-BCFF-443C-B7D5-30199F730335")
                        .get();

##<a name="untyped"></a>如何︰ 使用的不具型資料

不具型別程式模型可讓您精確控制 JSON 序列化。  有一些常見的案例，您可能會想要使用的不具型別程式模型。 例如，如果您的後端資料表包含多欄，您只需要參照的資料行的子集。  輸入的模型需要您定義您的資料類別中的所有行動應用程式資料表的資料行。  

大部分的 API 呼叫來存取資料輸入的程式設計通話類似。 主要差異是，不具型別模型中您叫用**MobileServiceJsonTable**物件，而不是**MobileServiceTable**物件上的方法。

### <a name="json_instance"></a>如何︰ 建立不具型別表格的執行個體

類似輸入模型，就可啟動快速表格參照，但在此情況下是**MobileServicesJsonTable**物件。 取得參照，則可電話**不**在用戶端的執行個體的方法︰

    private MobileServiceJsonTable mJsonToDoTable;
    //...
    mJsonToDoTable = mClient.getTable("ToDoItem");

一旦您建立的**MobileServiceJsonTable**執行個體，幾乎所有具有相同的 API 提供輸入程式設計模型。 在某些情況下，方法會採用不具型別的參數，而不是輸入參數。

### <a name="json_insert"></a>如何︰ 在不具型別表格中插入

下列程式碼會顯示如何插入。 第一個步驟是，建立[JsonObject][1]，這是[gson] [3]文件庫。

    JsonObject jsonItem = new JsonObject();
    jsonItem.addProperty("text", "Wake up");
    jsonItem.addProperty("complete", false);

然後，使用**insert()**不具型別的物件插入表格。

    mJsonToDoTable.insert(jsonItem).get();

如果您需要取得插入的物件的識別碼，請使用**getAsJsonPrimitive()**方法。

    jsonItem.getAsJsonPrimitive("id").getAsInt());

### <a name="json_delete"></a>如何︰ 從不具型別資料表刪除

下列程式碼會顯示如何刪除執行個體，如此一來， **JsonObject** *插入*先前範例中所建立的同一個執行個體。 程式碼會與輸入的大小寫，相同，但的方法有不同的簽名，因為它會參考**JsonObject**。

         mToDoTable.delete(jsonItem);

您也可以直接使用其識別碼來刪除執行個體︰

         mToDoTable.delete(ID);

### <a name="json_get"></a>如何︰ 從不具型別表格傳回的所有資料列

下列程式碼會顯示如何擷取整個表格。 由於您使用 JSON 表格，您可以選擇性地擷取部分一個資料表的資料行。

    public void showAllUntyped(View view) {
        new AsyncTask<Void, Void, Void>() {
            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final JsonElement result = mJsonToDoTable.execute().get();
                    final JsonArray results = result.getAsJsonArray();
                    runOnUiThread(new Runnable() {

                        @Override
                        public void run() {
                            mAdapter.clear();
                            for (JsonElement item : results) {
                                String ID = item.getAsJsonObject().getAsJsonPrimitive("id").getAsString();
                                String mText = item.getAsJsonObject().getAsJsonPrimitive("text").getAsString();
                                Boolean mComplete = item.getAsJsonObject().getAsJsonPrimitive("complete").getAsBoolean();
                                ToDoItem mToDoItem = new ToDoItem();
                                mToDoItem.setId(ID);
                                mToDoItem.setText(mText);
                                mToDoItem.setComplete(mComplete);
                                mAdapter.add(mToDoItem);
                            }
                        }
                    });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        }.execute();
    }

篩選的同一組，篩選和分頁可供輸入模型的方法可供不具型別模型。

##<a name="custom-api"></a>如何︰ 呼叫自訂的 API

自訂的 API 可讓您定義自訂的端點，公開伺服器功能無法對應至 [插入、 更新、 刪除，或讀取作業。 您可以藉由使用自訂的 API，讓更多的控制訊息，包括讀取和設定 HTTP 郵件標題以及定義 JSON 以外的郵件本文格式。

從 Android 的用戶端，呼叫**invokeApi**方法呼叫自訂的 API 結束點。 下列範例會示範如何呼叫名為**completeAll**，它會傳回一個名為**MarkAllResult**的集合類別的 API 端點。

    public void completeItem(View view) {

        ListenableFuture<MarkAllResult> result = mClient.invokeApi( "completeAll", MarkAllResult.class );

            Futures.addCallback(result, new FutureCallback<MarkAllResult>() {
                @Override
                public void onFailure(Throwable exc) {
                    createAndShowDialog((Exception) exc, "Error");
                }

                @Override
                public void onSuccess(MarkAllResult result) {
                    createAndShowDialog(result.getCount() + " item(s) marked as complete.", "Completed Items");
                    refreshItemsFromTable();
                }
            });
        }

在用戶端，傳送文章要求到新的自訂 API 呼叫**invokeApi**方法。 自訂 API 所傳回的結果會顯示在 [郵件] 對話方塊中，為任何錯誤。 其他版本的**invokeApi**可讓您選擇性地傳送邀請內文中的物件、 指定 HTTP 的方法，並傳送要求的查詢參數。 不具型的版本**invokeApi**也提供。

##<a name="authentication"></a>如何︰ 將驗證新增至您的應用程式

教學課程已經詳細的說明如何新增這些功能。

應用程式服務支援[驗證應用程式使用者](app-service-mobile-android-get-started-users.md)使用各種不同的外部的身分識別提供者︰ Facebook、 Google、 Microsoft 帳戶、 Twitter 和 Azure Active Directory。 您可以設定權限限制為特定作業已驗證的使用者存取的表格。 您也可以使用已驗證的使用者身分的識別，在您的後端實作授權規則。

兩個驗證流程支援︰**伺服器**流程和**用戶端**流程。 伺服器流程提供最簡單的驗證體驗，它依賴的身分識別提供者網頁介面。  沒有其他 Sdk，才能實作伺服器流程驗證。 伺服器流程驗證並不提供深度整合到行動裝置，並只建議，為證明概念的案例。

用戶端流程允許更深入的整合與裝置的特定功能，例如 [單一登入身分識別提供者所提供的 Sdk 必須。  例如，您可以將 Facebook SDK 整合行動應用程式。  行動用戶端交換到 Facebook 應用程式，並確認您登入之前交換回到您的行動應用程式。

若要啟用您的應用程式中的驗證需要四個步驟︰

- 與身分識別提供者註冊您的應用程式進行驗證。
- 設定您的應用程式服務後端。
- 限制已驗證的使用者，只在應用程式服務後端的資料表權限。
- 加入您的應用程式中的驗證碼。

您可以設定權限限制為特定作業已驗證的使用者存取的表格。 您也可以使用驗證的使用者的 SID 修改要求。  如需詳細資訊，請檢閱[快速入門驗證]與伺服器 SDK 如何文件。

### <a name="caching"></a>如何︰ 驗證碼新增至您的應用程式

下列程式碼會開始使用 Google 提供者伺服器流程登入程序︰

    MobileServiceUser user = mClient.login(MobileServiceAuthenticationProvider.Google);

取得使用**getUserId**方法**MobileServiceUser**的登入的使用者識別碼。 如需如何使用 Future 呼叫非同步的登入 Api 的範例，請參閱[開始使用驗證]。

### <a name="caching"></a>如何︰ 快取驗證的權杖

快取驗證的權杖會要求您儲存在使用者識別碼和驗證權杖本機裝置上。 下次啟動應用程式，您檢查快取中，如果這些值存在，您可以略過登入程序，並 rehydrate 此資料的用戶端。 不過這項資料機密，而應儲存加密安全，以免電話會竊取。

您可以看到[快取驗證權杖]一節中的快取驗證權杖方式完成範例[7]。

當您嘗試使用過期的權杖時，您會收到*401 未經授權*的回應。 您可以處理使用篩選的驗證錯誤。  篩選 intercept 應用程式服務後端要求。 篩選程式碼測試 401 的回應，觸發登入程序，並再繼續執行產生 401 的要求。 

## <a name="adal"></a>如何︰ 驗證與 Active Directory 驗證文件庫的使用者

您可以使用 Active Directory 驗證文件庫 (ADAL) 登入應用程式使用 Azure Active Directory 的使用者。 使用用戶端流程登入，通常會比使用`loginAsync()`方法時，提供更多原生的 UX 風格，並讓其他自訂作業。

1. 依照[如何設定 Active Directory 登入應用程式服務](app-service-mobile-how-to-configure-active-directory-authentication.md)教學課程中設定您的行動應用程式後端的 AAD 登入。 請確定完成註冊的原生的用戶端應用程式的選用步驟。

2. 安裝 ADAL 修改 build.gradle 檔案包含定義如下︰

```
repositories {
    mavenCentral()
    flatDir {
        dirs 'libs'
    }
    maven {
        url "YourLocalMavenRepoPath\\.m2\\repository"
    }
}
packagingOptions {
    exclude 'META-INF/MSFTSIG.RSA'
    exclude 'META-INF/MSFTSIG.SF'
}
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile('com.microsoft.aad:adal:1.1.1') {
        exclude group: 'com.android.support'
    } // Recent version is 1.1.1
    compile 'com.android.support:support-v4:23.0.0'
}
```

3. 將下列程式碼新增至您的應用程式中，然後再進行下列取代項目︰

* **插入授權這裡**取代為您佈建應用程式的租用戶的名稱。 格式應該是 https://login.windows.net/contoso.onmicrosoft.com。 此值可以複製您在 [Azure 傳統入口網站] 的 Azure Active Directory 中的 [網域] 索引標籤。

* **插入資源識別碼-以下**換成您的行動應用程式後端的用戶端識別碼。 您可以從 [**進階]**索引標籤底下**Azure Active Directory 設定**入口網站中，以取得的用戶端識別碼。

* **插入用戶端識別碼-以下**換成您複製的原生的用戶端應用程式的用戶端識別碼。

* 取代您的網站_/.auth/login/done_端點，請使用 HTTPS 配置**插入重新導向-URI 這裡**。 此值應該類似_https://contoso.azurewebsites.net/.auth/login/done_。

        private AuthenticationContext mContext;

        private void authenticate() {
            String authority = "INSERT-AUTHORITY-HERE";
            String resourceId = "INSERT-RESOURCE-ID-HERE";
            String clientId = "INSERT-CLIENT-ID-HERE";
            String redirectUri = "INSERT-REDIRECT-URI-HERE";
            try {
                mContext = new AuthenticationContext(this, authority, true);
                mContext.acquireToken(this, resourceId, clientId, redirectUri, PromptBehavior.Auto, "", callback);
            } catch (Exception exc) {
                exc.printStackTrace();
            }
        }

        private AuthenticationCallback<AuthenticationResult> callback = new AuthenticationCallback<AuthenticationResult>() {
            @Override
            public void onError(Exception exc) {
                if (exc instanceof AuthenticationException) {
                    Log.d(TAG, "Cancelled");
                } else {
                    Log.d(TAG, "Authentication error:" + exc.getMessage());
                }
            }

            @Override
            public void onSuccess(AuthenticationResult result) {
                if (result == null || result.getAccessToken() == null
                        || result.getAccessToken().isEmpty()) {
                    Log.d(TAG, "Token is empty");
                } else {
                    try {
                        JSONObject payload = new JSONObject();
                        payload.put("access_token", result.getAccessToken());
                        ListenableFuture<MobileServiceUser> mLogin = mClient.login("aad", payload.toString());
                        Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
                            @Override
                            public void onFailure(Throwable exc) {
                                exc.printStackTrace();
                            }
                            @Override
                            public void onSuccess(MobileServiceUser user) {
                                Log.d(TAG, "Login Complete");
                            }
                        });
                    }
                    catch (Exception exc){
                        Log.d(TAG, "Authentication error:" + exc.getMessage());
                    }
                }
            }
        };

        @Override
        protected void onActivityResult(int requestCode, int resultCode, Intent data) {
            super.onActivityResult(requestCode, resultCode, data);
            if (mContext != null) {
                mContext.onActivityResult(requestCode, resultCode, data);
            }
        }

## <a name="how-to-add-push-notification-to-your-app"></a>如何︰ 新增至您的應用程式的 [推入通知

您可以[閱讀適合][6]說明如何 Microsoft Azure 通知集線器支援各種不同的推播通知。  在[本教學課程][5]，每次即會插入一筆記錄，將會傳送至所有裝置的推入通知。

## <a name="how-to-add-offline-sync-to-your-app"></a>如何︰ 新增至您的應用程式的 [離線同步處理

快速入門教學課程包含實作離線同步處理的程式碼。 尋找加上註解的程式碼︰

    // Offline Sync

藉由 uncommenting 下列幾行程式碼中，您可以執行離線同步處理，然後您可以將類似的程式碼新增至其他行動應用程式的程式碼。

##<a name="customizing"></a>如何︰ 自訂用戶端

有幾種方式可以自訂的用戶端的預設行為。

### <a name="headers"></a>如何︰ 自訂要求標頭

設定將自訂的 HTTP 標頭新增至每一條**ServiceFilter** :

    private class CustomHeaderFilter implements ServiceFilter {

        @Override
        public ListenableFuture<ServiceFilterResponse> handleRequest(
                    ServiceFilterRequest request,
                    NextServiceFilterCallback next) {

            runOnUiThread(new Runnable() {

                @Override
                public void run() {
                    request.addHeader("My-Header", "Value");                    }
            });

            SettableFuture<ServiceFilterResponse> result = SettableFuture.create();
            try {
                ServiceFilterResponse response = next.onNext(request).get();
                result.set(response);
            } catch (Exception exc) {
                result.setException(exc);
            }
        }

### <a name="serialization"></a>如何︰ 自訂序列化

用戶端假設上所有符合完全在用戶端中定義資料物件後端的資料表名稱、 資料行名稱及資料類型。 可以有任何數字的原因為何可能不符合伺服器與用戶端名稱。 在您的案例中，您可以執行下列類型的自訂項目︰

- 應用程式服務資料表中使用的資料行名稱都不符合您使用的在用戶端的名稱。
- 使用具有它對應至用戶端中的類別名稱不同的應用程式服務資料表。
- 開啟 [自動屬性大寫]。
- 新增至物件的複雜的屬性。

### <a name="columns"></a>如何︰ 將不同的用戶端和伺服器名稱對應

假設 Java 用戶端程式碼會使用標準 Java 樣式名稱**ToDoItem**物件屬性，例如下列屬性︰

- mId
- mText
- mComplete
- mDuration

在伺服器上**ToDoItem**資料表的資料行名稱相符的 JSON 名稱將序列化用戶端名稱。 下列程式碼會使用[gson] [3]內容加上註解的文件庫︰

    @com.google.gson.annotations.SerializedName("text")
    private String mText;

    @com.google.gson.annotations.SerializedName("id")
    private int mId;

    @com.google.gson.annotations.SerializedName("complete")
    private boolean mComplete;

    @com.google.gson.annotations.SerializedName("duration")
    private String mDuration;

### <a name="table"></a>如何︰ 對應的用戶端和後端之間的不同的資料表名稱

用戶端資料表名稱對應到不同的行動服務表格名稱，以覆寫[getTable()] [4]方法︰

    mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);

### <a name="conversions"></a>如何︰ 自動化資料行名稱對應

您可以指定使用[gson]套用至每個資料行轉換策略[3]API。 Android 的用戶端文件庫使用[gson] [3]序列化 JSON 資料 Java 物件，再傳送資料至 Azure 應用程式服務的詳細說明。  下列程式碼會使用**setFieldNamingStrategy()**方法，若要設定的策略。 此範例會刪除初始字元 ("m")，然後均和下一個字元，每個欄位名稱。 例如，它會變成 」 mId 」 「 識別碼 」。

    client.setGsonBuilder(
        MobileServiceClient
        .createMobileServiceGsonBuilder()
        .setFieldNamingStrategy(new FieldNamingStrategy() {
            public String translateName(Field field) {
                String name = field.getName();
                return Character.toLowerCase(name.charAt(1))
                    + name.substring(2);
                }
            });

使用**MobileServiceClient**之前，必須執行這個程式碼。

### <a name="complex"></a>如何︰ 儲存成表格的物件或陣列的屬性

到目前為止，我們序列化範例有涉及基本的類型，例如整數] 和 [字串。  將 JSON 輕鬆序列化基本類型。  如果我們想要新增的複雜的物件，無法自動序列化 JSON，我們需要提供 JSON 序列化方法。  若要查看如何提供自訂 JSON 序列化範例，請檢閱 [部落格文章[使用 gson 文件庫，在 Android 行動服務的用戶端中的自訂序列化][2]。

<!-- Anchors. -->

[What is Mobile Services]: #what-is
[Concepts]: #concepts
[How to: Create the Mobile Services client]: #create-client
[How to: Create a table reference]: #instantiating
[The API structure]: #api
[How to: Query data from a mobile service]: #querying
[傳回所有項目]: #showAll
[傳回資料的篩選]: #filtering
[傳回資料的排序]: #sorting
[在頁面中傳回的資料]: #paging
[選取特定的資料行]: #selecting
[How to: Concatenate query methods]: #chaining
[How to: Bind data to the user interface]: #binding
[How to: Define the layout]: #layout
[How to: Define the adapter]: #adapter
[How to: Use the adapter]: #use-adapter
[How to: Insert data into a mobile service]: #inserting
[How to: update data in a mobile service]: #updating
[How to: Delete data in a mobile service]: #deleting
[How to: Look up a specific item]: #lookup
[How to: Work with untyped data]: #untyped
[How to: Authenticate users]: #authentication
[Cache authentication tokens]: #caching
[How to: Handle errors]: #errors
[How to: Design unit tests]: #tests
[How to: Customize the client]: #customizing
[Customize request headers]: #headers
[Customize serialization]: #serialization
[Next Steps]: #next-steps
[Setup and Prerequisites]: #setup

<!-- Images. -->

<!-- URLs. -->
[Get started with Azure Mobile Apps]: app-service-mobile-android-get-started.md
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[Mobile Services SDK for Android]: http://go.microsoft.com/fwlink/p/?LinkID=717033
[Azure 入口網站]: https://portal.azure.com
[快速入門驗證]: app-service-mobile-android-get-started-users.md
[1]: http://google-gson.googlecode.com/svn/trunk/gson/docs/javadocs/com/google/gson/JsonObject.html
[2]: http://hashtagfail.com/post/44606137082/mobile-services-android-serialization-gson
[3]: http://go.microsoft.com/fwlink/p/?LinkId=290801
[4]: http://go.microsoft.com/fwlink/p/?LinkId=296840
[5]: app-service-mobile-android-get-started-push.md
[6]: ../notification-hubs/notification-hubs-push-notification-overview.md#integration-with-app-service-mobile-apps
[7]: app-service-mobile-android-get-started-users.md#cache-tokens
[8]: http://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/table/MobileServiceTable.html
[9]: http://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html
[10]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[11]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[12]: http://azure.github.io/azure-mobile-apps-android-client/
[13]: app-service-mobile-android-get-started.md#create-a-new-azure-mobile-app-backend
[14]: http://go.microsoft.com/fwlink/p/?LinkID=717034
[15]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#how-to-define-a-table-controller
[16]: app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations
[未來]: http://developer.android.com/reference/java/util/concurrent/Future.html
[AsyncTask]: http://developer.android.com/reference/android/os/AsyncTask.html