<properties
   pageTitle="Microsoft Azure 應用程式服務中使用 DB2 連接器 |Microsoft Azure"
   description="如何使用 DB2 連接器邏輯應用程式引動程序與動作"
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

# <a name="db2-connector"></a>DB2 連接器
>[AZURE.NOTE] 此版本，請參閱適用於邏輯應用程式 2014年-12-01-預覽架構版本。

Microsoft 連接器 db2 是透過 Azure 應用程式服務的應用程式連線到 IBM DB2 資料庫中儲存的資源的 API 應用程式。 連接器包含 Microsoft 用戶端連線到遠端 DB2 伺服器電腦透過 TCP/IP 網路連線，包括 Azure 混合式連線到內部部署 DB2 伺服器使用 Azure 服務匯流排轉送連接器支援下列的資料庫作業︰

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


## <a name="create-the-db2-connector"></a>建立 DB2 連接器
您可以在下列範例想定義中的邏輯應用程式，或從 Azure Marketplace，連接器︰  

1. 在 Azure startboard 中，選取 [**市集**]。
2. 在**所有項目**刀，在 [**搜尋所有項目**] 方塊中，輸入**db2** ，然後按一下 enter 鍵。
3. 在搜尋所有項目結果] 窗格中，選取**DB2 連接器**。
4. DB2 連接器描述刀中，選取 [**建立**。
5. 在 DB2 連接器套件刀中，輸入名稱 (例如 「 Db2ConnectorNewOrders 」)、 應用程式服務規劃及其他屬性。
6. 選取 [**封裝設定**]，然後輸入下列封裝設定︰  

    名稱 | 所需 |  描述
--- | --- | ---
連接字串 | [是] | DB2 用戶端連線字串 (例如，「 網路位址 = 伺服器名稱。網路連接埠 = 50000;使用者識別碼 = 的使用者名稱。密碼 = 密碼; 初始目錄 = 範例。封裝集合 = NWIND; 預設結構描述 = NWIND 」)。
表格 | [是] | 逗點分隔清單的資料表、 檢視和別名所需的 OData 作業，並產生 swagger 使用範例 (例如 「*NEWORDERS*」) 的文件的名稱。
程序 | [是] | 逗點分隔的程序與函數名稱清單 (例如 「 SPORDERID 」)。
OnPremise | 無 | 部署內部部署使用 Azure 服務匯流排轉送。
ServiceBusConnectionString | 無 | Azure 服務匯流排轉送連接字串。
PollToCheckData | 無 | 若要使用的邏輯應用程式觸發程序選取 [計數陳述式 (例如 「 選取 COUNT (\*) 從 NEWORDERS 位置 SHIPDATE IS NULL 」)。
PollToReadData | 無 | 若要使用的邏輯應用程式觸發程序的 SELECT 陳述式 (例如 「 選取\*從 NEWORDERS SHIPDATE 哪裡 NULL FOR 更新 」)。
PollToAlterData | 無 | 更新或刪除陳述式，若要使用的邏輯應用程式觸發程序 (例如 「 更新 NEWORDERS 設定 SHIPDATE = 目前日期在目前的&lt;游標&gt;」)。

7. 選取**[確定**]，然後再選取 [**建立**]。
8. 完成時，看起來類似下列封裝設定︰  
![][1]


## <a name="logic-app-with-db2-connector-action-to-add-data"></a>若要新增資料的 DB2 連接器動作邏輯應用程式 ##
您可以定義 DB2 資料表使用實體 OData 作業 API 插入或文章中新增資料邏輯應用程式動作。 例如，您可以插入新客戶訂單記錄，處理識別資料行，以定義表格插入 SQL 陳述式傳回識別值或邏輯應用程式 （選取 ORDID 最後一個資料表 (插入到 NWIND 受影響資料列。NEWORDERS （CUSTID 送貨、 SHIPADDR、 送貨、 SHIPREG、 SHIPZIP） 的值 (？，？，？，？，？，？)))。

> [AZURE.TIP] DB2 連線 「*張貼到 EntitySet*」 會傳回身分識別資料行的值，並 「*API 插入*「 傳回受影響的資料列

1. 在 [Azure startboard 中，選取 [ **+** （加號） **Web + Mobile**，然後**邏輯應用程式**。
2. 輸入名稱 (例如 「 NewOrdersDb2 」)、 應用程式服務規劃、 其他內容，然後選取 [**建立**。
3. 在 Azure startboard 中，選取邏輯您剛剛建立的應用程式、**設定**] 及 [**觸發程序和動作**。
4. 在引動程序及動作刀中，選取 [**從頭開始建立**邏輯應用程式範本。
5. 在 API 應用程式] 面板中，選取 [**週期]**，設定頻率和間隔，然後**核取記號**。
6. 在 [API 應用程式] 面板中，選取**DB2 連接器**，展開 [作業] 清單，選取 [**插入 NEWORDER**。
7. 展開 [參數] 清單，輸入下列值︰  

    名稱 | 值
--- | --- 
CUSTID | 10042
送貨 | 延遲 K Kountry 存放區 
SHIPADDR | 12 管弦樂團享用
送貨 | Walla Walla 
SHIPREG | 「 台北 」
SHIPZIP | 99362 

8. 選取**核取記號**以儲存動作設定] 及 [**儲存**]。
9. 設定應如下所示︰  
![][3]

10. 在 [**作業**底下**所有執行**清單中選取的第一列的項目 （最近執行）。 
11. 在**執行的邏輯應用程式**刀中，選取 [**動作**項目**db2connectorneworders**]。
12. 在**邏輯應用程式動作**刀中，選取**輸入] 連結**。 DB2 連接器使用參數化的插入陳述式 [處理程序輸入項目。
13. 在**邏輯應用程式動作**刀中，選取**輸出連結**。 輸入應如下所示︰  
![][4]

#### <a name="what-you-need-to-know"></a>您需要知道什麼

- 構成邏輯應用程式動作名稱時，連接器就會截斷 DB2 資料表名稱。 例如，**插入 NEWORDERS**作業會取至整數插入**NEWORDER**。
- 儲存之後邏輯應用程式**引動程序及動作**，邏輯應用程式會處理作業。 可能會有延遲邏輯之前的秒數 （例如 3-5 秒） 的數字的應用程式進行處理作業。 或者，您可以按一下 [**立即執行**處理作業。
- DB2 連接器定義 EntitySet 成員屬性，包括是否對應至 DB2 欄，並有預設的成員，或產生的欄 （例如識別）。 邏輯應用程式顯示 EntitySet 成員識別碼名稱上，以表示 DB2 欄所需的值] 旁的紅色星號。 您不應該 ORDID 成員，其會對應到 DB2 身分識別的資料行中輸入值。 您可能會輸入的其他選用成員 （項目、 ORDDATE、 REQDATE、 SHIPID、 運費、 SHIPCTRY），其會對應到 DB2 使用預設值的資料行的值。 
- 傳回邏輯應用程式 DB2 連接器上張貼到 EntitySet 衍生自 DRDA SQLDARD （SQL 資料區域回覆資料） 包含身分識別的資料行的值，在準備插入 SQL 陳述式的回應。 DB2 伺服器不會傳回使用預設值的資料行插入的值。  


## <a name="logic-app-with-db2-connector-action-to-add-bulk-data"></a>若要新增大量資料 DB2 連接器動作邏輯應用程式 ##
您可以定義新增資料至 DB2 資料表中使用的 API 大量插入作業邏輯應用程式動作。 例如，您可以插入兩個新客戶訂單記錄，處理插入 SQL 陳述式使用陣列身分識別] 欄中，以定義資料表資料列值傳回邏輯應用程式 （選取 ORDID 最後一個資料表 (插入到 NWIND 受影響資料列。NEWORDERS （CUSTID 送貨、 SHIPADDR、 送貨、 SHIPREG、 SHIPZIP） 的值 (？，？，？，？，？，？)))。

1. 在 [Azure startboard 中，選取 [ **+** （加號） **Web + Mobile**，然後**邏輯應用程式**。
2. 輸入名稱 (例如 「 NewOrdersBulkDb2 」)、 應用程式服務規劃、 其他內容，然後選取 [**建立**。
3. 在 Azure startboard 中，選取邏輯您剛剛建立的應用程式、**設定**] 及 [**觸發程序和動作**。
4. 在引動程序及動作刀中，選取 [**從頭開始建立**邏輯應用程式範本。
5. 在 API 應用程式] 面板中，選取 [**週期]**，設定頻率和間隔，然後**核取記號**。
6. 在 [API 應用程式] 面板中，選取**DB2 連接器**，展開選取**大量插入新**的 [作業] 清單。
7. 以陣列方式輸入**的資料列**的值。 例如，複製並貼上下列動作︰

    ```
    [{"CUSTID":10081,"SHIPNAME":"Trail's Head Gourmet Provisioners","SHIPADDR":"722 DaVinci Blvd.","SHIPCITY":"Kirkland","SHIPREG":"WA","SHIPZIP":"98034"},{"CUSTID":10088,"SHIPNAME":"White Clover Markets","SHIPADDR":"305 14th Ave. S. Suite 3B","SHIPCITY":"Seattle","SHIPREG":"WA","SHIPZIP":"98128","SHIPCTRY":"USA"}]
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

- 構成邏輯應用程式動作名稱時，連接器就會截斷 DB2 資料表名稱。 比方說，**大量**插入新的**大量插入 NEWORDERS**作業會遭到截斷。
- 省略身分識別欄 (例如 ORDID)、 空值的資料行 （例如 [出貨日期） 及預設值 （例如 ORDDATE REQDATE、 SHIPID、 運費、 SHIPCTRY） 的資料行，DB2 資料庫會產生的值。
- 藉由指定 「 今天 」 和 「 明天 」，DB2 連接器產生 「 目前日期 」 及 「 目前日期 + 1 天 」 函數 (例如 REQDATE)。 


## <a name="logic-app-with-db2-connector-trigger-to-read-alter-or-delete-data"></a>邏輯應用程式 DB2 連接器引動程序來讀取、 變更或刪除資料 ##
您可以定義邏輯應用程式觸發程序投票及讀取 API 投票資料複合索引作業的 DB2 表格中的資料。 例如，您可以閱讀一或多個新客戶訂單記錄，傳回邏輯應用程式的記錄。 DB2 連線應用程式套件設定應如下所示︰

    應用程式設定 | 值
--- | --- | ---
PollToCheckData | 選取 COUNT (\*) 從 NEWORDERS SHIPDATE 哪裡 NULL
PollToReadData | 選取 [\*從 NEWORDERS SHIPDATE 哪裡 NULL 更新
PollToAlterData | <no value specified>


此外，您可以定義邏輯應用程式觸發程序獲得、 讀取及變更使用的 API 投票資料複合索引作業 DB2 表格中的資料。 例如，您可以閱讀一或多個新客戶訂單的記錄更新資料列的值，傳回所選 （之前更新） 的記錄邏輯應用程式。 DB2 連線應用程式套件設定應如下所示︰

    應用程式設定 | 值
--- | --- | ---
PollToCheckData | 選取 COUNT (\*) 從 NEWORDERS SHIPDATE 哪裡 NULL
PollToReadData | 選取 [\*從 NEWORDERS SHIPDATE 哪裡 NULL 更新
PollToAlterData | 更新 NEWORDERS 設定 SHIPDATE = 目前的日期在目前的&lt;游標&gt;


此外，您可以定義邏輯應用程式觸發程序投票、 讀取及移除 API 投票資料複合索引作業的 DB2 表格中的資料。 例如，您可以閱讀一或多個新客戶訂單記錄，刪除的列，傳回所選 （之前 delete) 的記錄邏輯應用程式。 DB2 連線應用程式套件設定應如下所示︰

    應用程式設定 | 值
--- | --- | ---
PollToCheckData | 選取 COUNT (\*) 從 NEWORDERS SHIPDATE 哪裡 NULL
PollToReadData | 選取 [\*從 NEWORDERS SHIPDATE 哪裡 NULL 更新
PollToAlterData | 刪除 NEWORDERS 位置目前的&lt;游標&gt;

在此範例中，邏輯應用程式會獲得、 讀取、 更新及重新讀取 DB2 表格中的資料。

1. 在 [Azure startboard 中，選取 [ **+** （加號） **Web + Mobile**，然後**邏輯應用程式**。
2. 輸入名稱 (例如 「 ShipOrdersDb2 」)、 應用程式服務規劃、 其他內容，然後選取 [**建立**。
3. 在 Azure startboard 中，選取邏輯您剛剛建立的應用程式、**設定**] 及 [**觸發程序和動作**。
4. 在引動程序及動作刀中，選取 [**從頭開始建立**邏輯應用程式範本。
5. 在 [API 應用程式] 面板中，選取**DB2 連接器**，設定頻率和間隔，然後**核取記號**。
6. 在 [API 應用程式] 面板中，選取**DB2 連接器**，展開 [作業] 清單，選取 [**從 NEWORDERS 選取**。
7. 選取**核取記號**以儲存動作設定] 及 [**儲存**]。 設定應如下所示︰  
![][10]  
8. 按一下以關閉 [**觸發程序和動作**刀，，然後按一下以關閉 [**設定**刀。
9. 在下**作業**清單**所有執行**中，按一下第一列的項目 （最近執行）。
10. 在**執行的邏輯應用程式**刀中，按一下的**動作**項目。
11. 在**邏輯應用程式動作**刀中，按一下**輸出連結**。 輸出應如下所示︰  
![][11]


## <a name="logic-app-with-db2-connector-action-to-remove-data"></a>若要移除資料 DB2 連接器動作邏輯應用程式 ##
您可以定義邏輯的應用程式動作移除 DB2 表格使用實體 OData 作業的 API 刪除或文章中的資料。 例如，您可以插入新客戶訂單記錄，處理識別資料行，以定義表格插入 SQL 陳述式傳回識別值或邏輯應用程式 （選取 ORDID 最後一個資料表 (插入到 NWIND 受影響資料列。NEWORDERS （CUSTID 送貨、 SHIPADDR、 送貨、 SHIPREG、 SHIPZIP） 的值 (？，？，？，？，？，？)))。

## <a name="create-logic-app-using-db2-connector-to-remove-data"></a>建立使用 DB2 連接器來移除資料的邏輯應用程式 ##
您可以建立新的邏輯的應用程式從 Azure 服務商場內，然後使用為動作 DB2 連接器移除客戶訂單。 例如，您可以使用 DB2 連接器條件刪除作業，處理的刪除 SQL 陳述式 (刪除從 NEWORDERS 位置 ORDID > = 10000)。

1. 在 [**啟動**Azure 區的 [中心] 功能表中，按一下 [ **+** （加號），按一下 [ **Web + Mobile**]，然後按一下 [**邏輯應用程式**。 
2. 在**建立邏輯應用程式**刀中，輸入**名稱**，例如**RemoveOrdersDb2**。
3. 選取或定義的其他設定 （例如服務方案，資源群組） 的值。
4. 設定應如下所示。 按一下 [**建立**]:  
![][12]  
5. 在**設定**刀中，按一下 [**觸發程序和動作**。
6. 在**引動程序及動作**刀，**邏輯應用程式範本**清單中，按一下 [**建立從頭**。
7. **觸發程序和動作**刀，在 [資源] 群組中，[ **API 應用程式**] 面板中按一下 [**週期性**]。
8. 邏輯應用程式在設計介面上, 按一下 [**週期性**項目設定的**頻率**及**間隔**，，例如 [**天**] 和 [ **1**]，再按一下**核取記號**以儲存的週期性項目設定。
9. 按一下 [**觸發程序和動作**刀，在 [資源] 群組中，[ **API 應用程式**] 面板中的 [ **DB2 接點**]。
10. 邏輯應用程式在設計介面上, 按一下 [ **DB2 連接器**動作項目，按一下以展開 [作業] 清單中的省略符號 （**...**），然後按一下**條件刪除 N**。
11. 在 [DB2 連接器動作項目，輸入**ORDID ge 10000** **運算式的識別項目的子集**。
12. 按一下 [儲存] 動作設定] 中，**核取記號**，然後按一下 [**儲存**。 設定應如下所示︰  
![][13]  
13. 按一下以關閉 [**觸發程序和動作**刀，，然後按一下以關閉 [**設定**刀。
14. 在下**作業**清單**所有執行**中，按一下第一列的項目 （最近執行）。
15. 在**執行的邏輯應用程式**刀中，按一下的**動作**項目。
16. 在**邏輯應用程式動作**刀中，按一下**輸出連結**。 輸出應如下所示︰  
![][14]

**附註︰**邏輯應用程式的設計工具會截斷資料表名稱。 例如，**條件刪除 NEWORDERS**作業會取至整數**條件**刪除 N。


> [AZURE.TIP] 使用下列 SQL 陳述式來建立範例表格和預存程序。 

您可以建立使用下列 DB2 SQL DDL 陳述式範例 NEWORDERS 資料表︰
 
    CREATE TABLE ORDERS (  
        ORDID INT NOT NULL GENERATED BY DEFAULT AS IDENTITY (START WITH 10000, INCREMENT BY 1) ,  
        CUSTID INT NOT NULL ,  
        EMPID INT NOT NULL DEFAULT 10000 ,  
        ORDDATE DATE NOT NULL DEFAULT CURRENT DATE ,  
        REQDATE DATE DEFAULT CURRENT DATE ,  
        SHIPDATE DATE ,  
        SHIPID INT NOT NULL DEFAULT 10000,  
        FREIGHT DECIMAL (9,2) NOT NULL DEFAULT 0.00 ,  
        SHIPNAME CHAR (40) NOT NULL ,  
        SHIPADDR CHAR (60) NOT NULL ,  
        SHIPCITY CHAR (20) NOT NULL ,  
        SHIPREG CHAR (15) NOT NULL ,  
        SHIPZIP CHAR (10) NOT NULL ,  
        SHIPCTRY CHAR (15) NOT NULL DEFAULT 'USA' ,  
        PRIMARY KEY(ORDID)  
        )  
 
    CREATE UNIQUE INDEX XORDID ON ORDERS (ORDID ASC)  



您可以建立使用下列 DB2 DDL 陳述式的範例 SPOERID 預存程序︰
 
    CREATE OR REPLACE PROCEDURE NWIND.SPORDERID (IN ORDERID VARCHAR(128))  
        DYNAMIC RESULT SETS 1  
    P1: BEGIN  
        DECLARE CURSOR1 CURSOR WITH RETURN FOR  
            SELECT * FROM NWIND.NEWORDERS  
                WHERE ORDID = ORDERID;  
        OPEN CURSOR1;  
    END P1  
    ') 


## <a name="hybrid-configuration-optional"></a>（選擇性） 的混合式設定

> [AZURE.NOTE] 只有當您使用的 DB2 連接器內部部署您的防火牆需要此步驟。

應用程式服務使用混合式組態管理員安全地連線到您的內部部署系統。 如果連接器使用內部部署 IBM DB2 伺服器的 Windows，混合式連線管理員需要。

請參閱[使用混合式連線管理員](app-service-logic-hybrid-connection-manager.md)。


## <a name="do-more-with-your-connector"></a>執行更多您的連接器
現在，建立連接器時，您可以將其新增商務工作流程使用邏輯的應用程式。 請參閱[邏輯應用程式有哪些？](app-service-logic-what-are-logic-apps.md)。

建立使用 REST Api 的 API 應用程式。 請參閱[連接器與 API 應用程式參照](http://go.microsoft.com/fwlink/p/?LinkId=529766)。

您也可以檢閱效能統計資料，並控制安全性連接器。 請參閱[管理與監視內建的 API 應用程式和連接器](app-service-logic-monitor-your-connectors.md)。


<!--Image references-->
[1]: ./media/app-service-logic-connector-db2/ApiApp_Db2Connector_Create.png
[2]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersDb2_Create.png
[3]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersDb2_TriggersActions.png
[4]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersDb2_Outputs.png
[5]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersBulkDb2_Create.png
[6]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersBulkDb2_TriggersActions.png
[7]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersBulkDb2_Inputs.png
[8]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersBulkDb2_Outputs.png
[9]: ./media/app-service-logic-connector-db2/LogicApp_UpdateOrdersDb2_Create.png
[10]: ./media/app-service-logic-connector-db2/LogicApp_UpdateOrdersDb2_TriggersActions.png
[11]: ./media/app-service-logic-connector-db2/LogicApp_UpdateOrdersDb2_Outputs.png
[12]: ./media/app-service-logic-connector-db2/LogicApp_RemoveOrdersDb2_Create.png
[13]: ./media/app-service-logic-connector-db2/LogicApp_RemoveOrdersDb2_TriggersActions.png
[14]: ./media/app-service-logic-connector-db2/LogicApp_RemoveOrdersDb2_Outputs.png

