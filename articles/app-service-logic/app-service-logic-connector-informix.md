<properties
   pageTitle="Microsoft Azure 應用程式服務中使用 Informix 連接器 |Microsoft Azure"
   description="如何使用 Informix 連接器邏輯應用程式引動程序與動作"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="gplarsen"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/31/2016"
   ms.author="plarsen"/>

# <a name="informix-connector"></a>Informix 連接器
>[AZURE.NOTE] 此版本，請參閱適用於邏輯應用程式 2014年-12-01-預覽架構版本。

Informix Microsoft 連接器是透過 Azure 應用程式服務的應用程式連線到 IBM Informix 資料庫中儲存的資源的 API 應用程式。 連接器包含 Microsoft 客戶 TCP/IP 網路連線，包括 Azure 混合式連線到內部部署 Informix 伺服器使用 Azure 服務匯流排轉送之間連線到遠端 Informix 伺服器電腦。 連接器支援下列的資料庫作業︰

- 閱讀使用選取的資料列
- 若要閱讀來選取的計算，後面接著選取的資料列的投票
- 新增一列或多個 （大量） 列，使用插入
- 變更一列或多個 （大量） 列，使用更新
- 移除一列或多個 （大量） 列，使用 [刪除]
- 已讀取] 即可變更資料列使用後面接著更新位置目前的游標選取游標
- 若要移除的資料列使用選取的游標，後面接著更新位置目前的游標讀取
- 輸入與輸出參數的執行程序，傳回的值，結果集，請使用通話
- 自訂命令和複合索引作業，使用 [選取]，[插入]，更新、 刪除

## <a name="triggers-and-actions"></a>觸發程序及動作
連接器支援下列邏輯應用程式引動程序及動作︰

觸發程序 | 動作
--- | ---
<ul><li>投票資料</li></ul> | <ul><li>大量插入</li><li>插入</li><li>大量更新</li><li>更新</li><li>通話</li><li>大量刪除</li><li>刪除</li><li>選取</li><li>條件的更新</li><li>張貼到 EntitySet</li><li>條件的刪除</li><li>選取單一項目</li><li>刪除</li><li>若要 EntitySet 了</li><li>自訂命令</li><li>複合索引的作業</li></ul>


## <a name="create-the-informix-connector"></a>建立 Informix 連接器
您可以在下列範例想定義中的邏輯應用程式，或從 Azure Marketplace，連接器︰  

1. 在 Azure startboard 中，選取 [ **Marketplace**]。
2. 在**所有項目**刀，在 [**搜尋所有項目**] 方塊中，輸入**informix** ，然後按一下 enter 鍵。
3. 在搜尋所有項目結果] 窗格中，選取**Informix 連接器**。
4. Informix 連接器描述刀中，選取 [**建立**。
5. 在 Informix 連接器套件刀中，輸入名稱 (例如 「 InformixConnectorNewOrders 」)、 應用程式服務規劃及其他屬性。
6. 選取 [**封裝設定**]，然後輸入下列封裝設定。

    名稱 | 所需 |  描述
--- | --- | ---
連接字串 | [是] | Informix 用戶端連線字串 (例如，「 網路位址 = 伺服器名稱。網路連接埠 = 9089;使用者識別碼 = 的使用者名稱。密碼 = 密碼; 初始目錄 = nwind; 預設的結構描述 = informix 」)。
表格 | [是] | 逗點分隔清單的資料表、 檢視和別名必要 OData 作業，並產生 swagger 範例 (例如 「 NEWORDERS 」) 的文件的名稱。
程序 | [是] | 逗點分隔的程序與函數名稱清單 (例如 「 SPORDERID 」)。
OnPremise | 無 | 部署內部部署使用 Azure 服務匯流排轉送。
ServiceBusConnectionString | 無 | Azure 服務匯流排轉送連接字串。
PollToCheckData | 無 | 若要使用的邏輯應用程式觸發程序選取 [計數陳述式 (例如 「 選取 COUNT (\*) 從 NEWORDERS 位置 SHIPDATE IS NULL 」)。
PollToReadData | 無 | 若要使用的邏輯應用程式觸發程序的 SELECT 陳述式 (例如 「 選取\*從 NEWORDERS SHIPDATE 哪裡 NULL FOR 更新 」)。
PollToAlterData | 無 | 更新或刪除陳述式，若要使用的邏輯應用程式觸發程序 (例如 「 更新 NEWORDERS 設定 SHIPDATE = 目前日期在目前的&lt;游標&gt;」)。

7. 選取**[確定**]，然後再選取 [**建立**]。
8. 完成時，看起來類似下列封裝設定︰  
![][1]


## <a name="logic-app-with-informix-connector-action-to-add-data"></a>Informix 連接器動作，將資料新增邏輯應用程式 ##
您可以定義邏輯應用程式巨集指令新增至使用實體 OData 作業 API 插入或文章 Informix 資料表的資料。 例如，您可以插入新的客戶訂單記錄，處理識別資料行，以定義表格插入 SQL 陳述式傳回識別值或邏輯應用程式受影響的資料列 (選取 ORDID 的最後一個資料表 (插入到 NEWORDERS （CUSTID 送貨、 SHIPADDR、 送貨、 SHIPREG、 SHIPZIP） 值 (？，？，？，？，？，？)))。

> [AZURE.TIP] Informix 連線 「*張貼到 EntitySet*」 會傳回身分識別資料行的值，並 「*API 插入*「 傳回受影響的資料列

1. 在 [Azure startboard 中，選取 [ **+** （加號） **Web + Mobile**，然後**邏輯應用程式**。
2. 輸入名稱 (例如 「 NewOrdersInformix 」)、 應用程式服務規劃、 其他內容，然後選取 [**建立**。
3. 在 Azure startboard 中，選取邏輯您剛剛建立的應用程式、**設定**] 及 [**觸發程序和動作**。
4. 在引動程序及動作刀中，選取 [**從頭開始建立**邏輯應用程式範本。
5. 在 API 應用程式] 面板中，選取 [**週期]**，設定頻率和間隔，然後**核取記號**。
6. 在 [API 應用程式] 面板中，選取**Informix 連接器**，展開 [作業] 清單，選取 [**插入 NEWORDER**。
7. 展開 [參數] 清單，輸入下列值︰  

    名稱 | 值
--- | --- 
CUSTID | 10042
SHIPID | 10000
送貨 | 延遲 K Kountry 存放區 
SHIPADDR | 12 管弦樂團享用
送貨 | Walla Walla 
SHIPREG | 「 台北 」
SHIPZIP | 99362 

8. 選取**核取記號**以儲存動作設定] 及 [**儲存**]。
9. 設定應如下所示︰  
![][3]  
10. 在 [**作業**底下**所有執行**清單中選取的第一列的項目 （最近執行）。 
11. 在**執行的邏輯應用程式**刀中，選取 [**動作**項目**informixconnectorneworders**]。
12. 在**邏輯應用程式動作**刀中，選取**輸入] 連結**。 Informix 連接器使用參數化的插入陳述式 [處理程序輸入項目。
13. 在**邏輯應用程式動作**刀中，選取**輸出連結**。 輸入應如下所示︰  
![][4]

#### <a name="what-you-need-to-know"></a>您需要知道什麼

- 構成邏輯應用程式動作名稱時，連接器就會截斷 Informix 資料表名稱。 例如，**插入 NEWORDERS**作業會取至整數插入**NEWORDER**。
- 儲存之後邏輯應用程式**引動程序及動作**，邏輯應用程式會處理作業。 可能會有延遲邏輯之前的秒數 （例如 3-5 秒） 的數字的應用程式進行處理作業。 或者，您可以按一下 [**立即執行**處理作業。
- Informix 連接器定義 EntitySet 成員屬性，包括是否成員對應，並有預設的 Informix 資料行或產生的欄 （例如識別）。 邏輯應用程式顯示 EntitySet 成員識別碼名稱上，以表示 Informix 欄所需的值] 旁的紅色星號。 您不應該 ORDID 成員，其會對應到 Informix 身分識別的資料行中輸入值。 您可能會輸入的其他選用成員 （項目、 ORDDATE、 REQDATE、 SHIPID、 運費、 SHIPCTRY），其會對應到 Informix 使用預設值的資料行的值。 
- 傳回邏輯應用程式 Informix 連接器上張貼到 EntitySet 衍生自 DRDA SQLDARD （SQL 資料區域回覆資料） 包含身分識別的資料行的值，在準備插入 SQL 陳述式的回應。 Informix 伺服器不會傳回使用預設值的資料行插入的值。  


## <a name="logic-app-with-informix-connector-action-to-add-bulk-data"></a>若要新增大量資料 Informix 連接器動作邏輯應用程式 ##
您可以定義邏輯應用程式巨集指令新增至使用 API 大量插入作業的 Informix 資料表的資料。 例如，您可以插入兩個新客戶訂單記錄，處理插入 SQL 陳述式使用陣列的身分識別] 欄中，以定義資料表資料列的值傳回邏輯應用程式受影響的資料列 (選取 ORDID 的最後一個資料表 (插入到 NEWORDERS （CUSTID 送貨、 SHIPADDR、 送貨、 SHIPREG、 SHIPZIP） 值 (？，？，？，？，？，？)))。

1. 在 [Azure startboard 中，選取 [ **+** （加號） **Web + Mobile**，然後**邏輯應用程式**。
2. 輸入名稱 (例如 「 NewOrdersBulkInformix 」)、 應用程式服務規劃、 其他內容，然後選取 [**建立**。
3. 在 Azure startboard 中，選取邏輯您剛剛建立的應用程式、**設定**] 及 [**觸發程序和動作**。
4. 在引動程序及動作刀中，選取 [**從頭開始建立**邏輯應用程式範本。
5. 在 API 應用程式] 面板中，選取 [**週期]**，設定頻率和間隔，然後**核取記號**。
6. 在 [API 應用程式] 面板中，選取**Informix 連接器**，展開選取**大量插入新**的 [作業] 清單。
7. 以陣列方式輸入**的資料列**的值。 例如，複製並貼上下列動作︰  

    ```
    [{"custid":10081,"shipid":10000,"shipname":"Trail's Head Gourmet Provisioners","shipaddr":"722 DaVinci Blvd.","shipcity":"Kirkland","shipreg":"WA","shipzip":"98034"},{"custid":10088,"shipid":10000,"shipname":"White Clover Markets","shipaddr":"305 14th Ave. S. Suite 3B","shipcity":"Seattle","shipreg":"WA","shipzip":"98128","shipctry":"USA"}]
    ```
        
8. 選取**核取記號**以儲存動作設定] 及 [**儲存**]。 設定應如下所示︰  
![][6]

9. 在下**作業**清單**所有執行**中，按一下第一列的項目 （最近執行）。
10. 在**執行的邏輯應用程式**刀中，按一下的**動作**項目。
11. 在 [**邏輯應用程式動作**刀中，**輸入連結**。 輸出應如下所示︰  
[][7]
12. 在**邏輯應用程式動作**刀中，按一下**輸出連結**。 輸出應如下所示︰  
![][8]

#### <a name="what-you-need-to-know"></a>您需要知道什麼

- 構成邏輯應用程式動作名稱時，連接器就會截斷 Informix 資料表名稱。 例如，**大量插入 NEWORDERS**作業會取至整數**大量**插入新增。
- Informix 資料庫可能會區分大小寫成資料表和資料行的名稱。 例如，大量插入作業陣列資料行名稱可能需要在小寫 (「 custid 」)，而不是大寫 (「 CUSTID 」) 中指定。
- 省略身分識別欄 (例如 ORDID)、 空值的資料行 （例如 [出貨日期） 及預設值 （例如 ORDDATE REQDATE、 SHIPID、 運費、 SHIPCTRY） 的資料行，Informix 資料庫會產生的值。
- 藉由指定 「 今天 」 和 「 明天 」，Informix 連接器產生 「 目前日期 」 及 「 目前日期 + 1 天 」 函數 (例如 REQDATE)。 


## <a name="logic-app-with-informix-connector-trigger-to-read-alter-or-delete-data"></a>Informix 連接器引動程序來讀取、 變更或刪除資料的邏輯應用程式 ##
您可以定義邏輯應用程式觸發程序投票及讀取使用 API 投票資料複合索引作業 Informix 表格中的資料。 比方說，您可以閱讀一或多個新客戶訂單記錄，傳回邏輯應用程式的記錄。 Informix 連線應用程式套件設定應如下所示︰

    應用程式設定 | 值
--- | --- | ---
PollToCheckData | 選取 COUNT (\*) 從 NEWORDERS SHIPDATE 哪裡 NULL
PollToReadData | 選取 [\*從 NEWORDERS SHIPDATE 哪裡 NULL 更新
PollToAlterData | <no value specified>


此外，您可以定義邏輯應用程式觸發程序投票、 讀取及變更使用的 API 投票資料複合索引作業 Informix 表格中的資料。 例如，您可以閱讀一或多個新客戶訂單的記錄更新資料列的值，傳回所選 （之前更新） 的記錄邏輯應用程式。 Informix 連線應用程式套件設定應如下所示︰

    應用程式設定 | 值
--- | --- | ---
PollToCheckData | 選取 COUNT (\*) 從 NEWORDERS SHIPDATE 哪裡 NULL
PollToReadData | 選取 [\*從 NEWORDERS SHIPDATE 哪裡 NULL 更新
PollToAlterData | 更新 NEWORDERS 設定 SHIPDATE = 目前的日期在目前的&lt;游標&gt;


此外，您可以定義邏輯應用程式觸發程序投票、 讀取及移除 Informix 表格，使用 API 投票資料複合索引操作的資料。 例如，您可以閱讀一或多個新客戶訂單記錄，刪除的列，傳回所選 （之前 delete) 的記錄邏輯應用程式。 Informix 連線應用程式套件設定應如下所示︰

    應用程式設定 | 值
--- | --- | ---
PollToCheckData | 選取 COUNT (\*) 從 NEWORDERS SHIPDATE 哪裡 NULL
PollToReadData | 選取 [\*從 NEWORDERS SHIPDATE 哪裡 NULL 更新
PollToAlterData | 刪除 NEWORDERS 位置目前的&lt;游標&gt;

在此範例中，邏輯應用程式會投票、 讀取、 更新及重新讀取 Informix 表格中的資料。

1. 在 [Azure startboard 中，選取 [ **+** （加號） **Web + Mobile**，然後**邏輯應用程式**。
2. 輸入名稱 (例如 「 ShipOrdersInformix 」)、 應用程式服務規劃、 其他內容，然後選取 [**建立**。
3. 在 Azure startboard 中，選取邏輯您剛剛建立的應用程式、**設定**] 及 [**觸發程序和動作**。
4. 在引動程序及動作刀中，選取 [**從頭開始建立**邏輯應用程式範本。
5. 在 [API 應用程式] 面板中，選取**Informix 連接器**，設定頻率和間隔，然後**核取記號**。
6. 在 [API 應用程式] 面板中，選取**Informix 連接器**，展開 [作業] 清單，選取 [**從 NEWORDERS 選取**。
7. 選取**核取記號**以儲存動作設定] 及 [**儲存**]。 設定應如下所示︰  
![][10]
8. 按一下以關閉 [**觸發程序和動作**刀，，然後按一下以關閉 [**設定**刀。
9. 在下**作業**清單**所有執行**中，按一下第一列的項目 （最近執行）。
10. 在**執行的邏輯應用程式**刀中，按一下的**動作**項目。
11. 在**邏輯應用程式動作**刀中，按一下**輸出連結**。 輸出應如下所示︰  
![][11]


## <a name="logic-app-with-informix-connector-action-to-remove-data"></a>若要移除資料 Informix 連接器動作邏輯應用程式 ##
您可以定義邏輯的應用程式動作移除實體 OData 作業使用 API 刪除或文章 Informix 表格中的資料。 例如，您可以插入新的客戶訂單記錄，處理識別資料行，以定義表格插入 SQL 陳述式傳回識別值或邏輯應用程式受影響的資料列 (選取 ORDID 的最後一個資料表 (插入到 NEWORDERS （CUSTID 送貨、 SHIPADDR、 送貨、 SHIPREG、 SHIPZIP） 值 (？，？，？，？，？，？)))。

## <a name="create-logic-app-using-informix-connector-to-remove-data"></a>建立使用 Informix 連接器來移除資料的邏輯應用程式 ##
您可以建立新的邏輯的應用程式從 Azure 服務商場內，然後使用為動作 Informix 連接器移除客戶訂單。 例如，您可以使用 Informix 連接器條件刪除作業，處理的刪除 SQL 陳述式 (刪除從 NEWORDERS 位置 ORDID > = 10000)。

1. 在 [**啟動**Azure 區的 [中心] 功能表中，按一下 [ **+** （加號），按一下 [ **Web + Mobile**]，然後按一下 [**邏輯應用程式**。 
2. 在**建立邏輯應用程式**刀中，輸入**名稱**，例如**RemoveOrdersInformix**。
3. 選取或定義的其他設定 （例如服務方案，資源群組） 的值。
4. 設定應如下所示。 按一下 [**建立**]:  
![][12]
5. 在**設定**刀中，按一下 [**觸發程序和動作**。
6. 在**引動程序及動作**刀，**邏輯應用程式範本**清單中，按一下 [**建立從頭**。
7. **觸發程序和動作**刀，在 [資源] 群組中，[ **API 應用程式**] 面板中按一下 [**週期性**]。
8. 邏輯應用程式在設計介面上, 按一下 [**週期性**項目設定的**頻率**及**間隔**，，例如 [**天**] 和 [ **1**]，再按一下**核取記號**以儲存的週期性項目設定。
9. 按一下 [**觸發程序和動作**刀，在 [資源] 群組中，[ **API 應用程式**] 面板中的 [ **Informix 接點**]。
10. 邏輯應用程式在設計介面上, 按一下 [ **Informix 連接器**動作項目，按一下以展開 [作業] 清單中的省略符號 （**...**），然後按一下**條件刪除 N**。
11. 在 [Informix 連接器動作項目中，輸入**ordid ge 10000** **運算式的識別項目的子集**。
12. 按一下 [儲存] 動作設定] 中，**核取記號**，然後按一下 [**儲存**。 設定應如下所示︰  
![][13]
13. 按一下以關閉 [**觸發程序和動作**刀，，然後按一下以關閉 [**設定**刀。
14. 在下**作業**清單**所有執行**中，按一下第一列的項目 （最近執行）。
15. 在**執行的邏輯應用程式**刀中，按一下的**動作**項目。
16. 在**邏輯應用程式動作**刀中，按一下**輸出連結**。 輸出應如下所示︰  
![][14]

**附註︰**邏輯應用程式的設計工具會截斷資料表名稱。 例如 [**條件式刪除 NEWORDERS** ] 作業會取至整數**條件**刪除 N。


> [AZURE.TIP] 使用下列 SQL 陳述式來建立範例表格和預存程序。 

您可以建立使用下列 Informix SQL DDL 陳述式範例 NEWORDERS 資料表︰
 
    create table neworders (  
        ordid serial(10000) unique ,  
        custid int not null ,  
        empid int not null default 10000 ,  
        orddate date not null default today ,  
        reqdate date default today ,  
        shipdate date ,  
        shipid int not null default 10000 ,  
        freight decimal (9,2) not null default 0.00 ,  
        shipname char (40) not null ,  
        shipaddr char (60) not null ,  
        shipcity char (20) not null ,  
        shipreg char (15) not null ,  
        shipzip char (10) not null ,  
        shipctry char (15) not null default ''USA'' 
        )


您可以建立使用下列 Informix DDL 陳述式的範例 SPORDERID 預存程序︰
 
    create procedure sporderid ( ord_id int)  
        returning int, int, int, date, date, date, int, decimal (9,2), char (40), char (60), char (20), char (15), char (10), char (15)  
        define xordid, xcustid, xempid, xshipid int;  
        define xorddate, xreqdate, xshipdate date;  
        define xfreight decimal (9,2);  
        define xshipname char (40);  
        define xshipaddr char (60);  
        define xshipcity char (20);  
        define xshipreg, xshipctry char (15);  
        define xshipzip char (10);  
        select ordid, custid, empid, orddate, reqdate, shipdate, shipid, freight, shipname, shipaddr, shipcity, shipreg, shipzip, shipctry  
            into xordid, xcustid, xempid, xorddate, xreqdate, xshipdate, xshipid, xfreight, xshipname, xshipaddr, xshipcity, xshipreg, xshipzip, xshipctry  
            from neworders where ordid = ord_id;  
        return xordid, xcustid, xempid, xorddate, xreqdate, xshipdate, xshipid, xfreight, xshipname, xshipaddr, xshipcity, xshipreg, xshipzip, xshipctry;  
    end procedure; 


## <a name="hybrid-configuration-optional"></a>混合式設定 （選用）

> [AZURE.NOTE] 只有當您使用的 DB2 連接器內部部署您的防火牆需要此步驟。

應用程式服務使用混合式組態管理員安全地連線到您的內部部署系統。 如果連接器使用內部部署 IBM DB2 伺服器的 Windows，混合式連線管理員需要。

請參閱[使用混合式連線管理員](app-service-logic-hybrid-connection-manager.md)。


## <a name="do-more-with-your-connector"></a>執行更多您的連接器
現在，建立連接器時，您可以將其新增商務工作流程使用邏輯的應用程式。 請參閱[邏輯應用程式有哪些？](app-service-logic-what-are-logic-apps.md)。

建立使用 REST Api 的 API 應用程式。 請參閱[連接器與 API 應用程式參照](http://go.microsoft.com/fwlink/p/?LinkId=529766)。

您也可以檢閱效能統計資料，並控制安全性連接器。 請參閱[管理與監視內建的 API 應用程式和連接器](app-service-logic-monitor-your-connectors.md)。


<!--Image references-->
[1]: ./media/app-service-logic-connector-informix/ApiApp_InformixConnector_Create.png
[2]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersInformix_Create.png
[3]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersInformix_TriggersActions.png
[4]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersInformix_Outputs.png
[5]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersBulkInformix_Create.png
[6]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersBulkInformix_TriggersActions.png
[7]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersBulkInformix_Inputs.png
[8]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersBulkInformix_Outputs.png
[9]: ./media/app-service-logic-connector-informix/LogicApp_UpdateOrdersInformix_Create.png
[10]: ./media/app-service-logic-connector-informix/LogicApp_UpdateOrdersInformix_TriggersActions.png
[11]: ./media/app-service-logic-connector-informix/LogicApp_UpdateOrdersInformix_Outputs.png
[12]: ./media/app-service-logic-connector-informix/LogicApp_RemoveOrdersInformix_Create.png
[13]: ./media/app-service-logic-connector-informix/LogicApp_RemoveOrdersInformix_TriggersActions.png
[14]: ./media/app-service-logic-connector-informix/LogicApp_RemoveOrdersInformix_Outputs.png


