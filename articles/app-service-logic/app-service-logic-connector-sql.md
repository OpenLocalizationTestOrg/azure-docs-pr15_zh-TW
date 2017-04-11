<properties
   pageTitle="使用邏輯應用程式中的 SQL 連接器 |Microsoft Azure 應用程式服務"
   description="如何建立及設定 SQL 連接器或 API 應用程式，並將其用於 Azure 應用程式服務中的邏輯應用程式"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="09/01/2016"
   ms.author="sameerch"/>


# <a name="get-started-with-the-microsoft-sql-connector-and-add-it-to-your-logic-app"></a>開始使用 Microsoft SQL 連接器並將其新增至您的邏輯應用程式
>[AZURE.NOTE] 此版本，請參閱適用於邏輯應用程式 2014年-12-01-預覽結構描述版本。 Azure SQL 2015-08-01-預覽架構版本中，按一下 [ [SQL Azure API](../connectors/connectors-create-api-sqlazure.md)]。

SQL Server 或 SQL Azure 資料庫來建立及變更您的資訊或資料連線到內部部署。 連接器可以用於邏輯應用程式中擷取程序，或發送資料 」 工作流程 」 的一部分。 當您在您的工作流程中使用 SQL 連接器時，您可以達到各種不同的案例。 例如，您可以︰

- 公開 SQL 資料庫使用的是 web 或行動應用程式中的資料的區段。
- 插入儲存在 SQL 資料庫資料表中的資料。 例如，您可以輸入員工記錄、 更新銷售訂單及等。
- 從 SQL 取得資料，然後將其用於商業程序。 例如，取得客戶記錄，並 SalesForce 中放置這些客戶記錄。

您可以新增 SQL 連接器商務工作流程與程序資料做為此工作流程中的邏輯應用程式的一部分。 

## <a name="triggers-and-actions"></a>觸發程序及動作
*觸發程序*會發生的事件。 新增順序更新時，或當新的客戶，例如。 *動作*是觸發程序的結果。 例如，當更新順序時，傳送提醒給銷售人員 」。 或者，當新增新的客戶時，將歡迎電子郵件傳送給新的客戶。

SQL 連接器可以作為觸發程序或邏輯應用程式支援中的資料和 JSON 和 XML 格式中的動作。 在套件中包含每個資料表的設定] （詳細說明，本主題稍後的），有一組 JSON 巨集及一組 XML 巨集。

SQL 連接線具有下列引動程序和可用的動作︰

觸發程序 | 動作
--- | ---
投票資料 | <ul><li>插入表格</li><li>更新目錄</li><li>從資料表中選取</li><li>從資料表刪除</li><li>呼叫 [預存程序</li></ul>

## <a name="create-the-sql-connector"></a>建立 SQL 連接器

連接器可以建立邏輯應用程式中，或直接從 Azure Marketplace 建立。 若要建立從服務商場的連接器︰  

1. 在 Azure startboard 中，選取 [**市集**]。
2. 搜尋 「 SQL 連接器 」、 加以選取，然後選取 [**建立**]。
3. 輸入名稱、 應用程式服務規劃及其他屬性。
4. 輸入下列封裝設定︰

    名稱 | 所需 |  描述
--- | --- | ---
伺服器名稱 | [是] | 輸入 SQL Server 名稱。 例如，輸入*SQLserver/sqlexpress*或*SQLserver.mydomain.com*。
連接埠 | 無 | 預設值為 1433年。
使用者名稱 | [是] | 輸入使用者名稱可以登入 SQL Server。 如果連線到內部部署的 SQL Server，請輸入 SQL 驗證認證。
密碼 | [是] | 輸入使用者名稱密碼。
資料庫名稱 | [是] | 輸入您要連接的資料庫。 例如，您可以輸入*客戶*或*dbo/訂單*。
內部部署 | [是] | 預設值為 False。 如果連線到 Azure SQL 資料庫，請輸入 False。 如果連線到內部部署的 SQL Server，請輸入 True。
服務匯流排連線字串 | 無 | 如果您連線到內部部署中，輸入服務匯流排轉送連線字串。<br/><br/>[使用混合式連線管理員](app-service-logic-hybrid-connection-manager.md)<br/>[服務匯流排價格](https://azure.microsoft.com/pricing/details/service-bus/)
合作夥伴伺服器名稱 | 無 | 如果主要伺服器無法使用，您可以輸入協力廠商伺服器為替代或備份伺服器。
表格 | 無 | 列出可更新連接器的資料庫資料表。 例如，輸入*OrdersTable*或*EmployeeTable*。 如果輸入的資料表，就可以使用所有資料表。 有效的資料表及/或預存程序，才能使用此連接器為的動作。
預存程序 | 無 | 輸入現有的預存程序可呼叫連接器。 例如，輸入*sp_IsEmployeeEligible*或*sp_CalculateOrderDiscount*。 有效的資料表及/或預存程序，才能使用此連接器為的動作。
資料使用的查詢 | 觸發程序支援 | 若要判斷是否適用於輪詢 SQL Server 資料庫表格的任何資料的 SQL 陳述式。 這應該會傳回一個數字的值，表示可用的資料列數目。 範例︰ 從 table_name 選取 COUNT(*)。
投票資料查詢 | 觸發程序支援 | SQL 陳述式以獲得的 SQL Server 資料庫資料表。 您可以輸入以分號隔開的 SQL 陳述任何的式數字。 此陳述式交易執行，而只認可資料安全地儲存邏輯應用程式中。 範例︰ 從 table_name; 選取*刪除從 table_name。<br/>**Note**<br/>您必須提供可避免刪除、 移動或更新選取的資料，以確保相同的資料無法再輪詢無限迴圈的投票陳述式。

5. 完成時，看起來類似下列封裝設定︰  
![][1]  

6. 選取 [**建立**]。 


## <a name="use-the-connector-as-a-trigger"></a>使用連接器的觸發程序
讓我們來看看輪詢 SQL 資料表的資料，將資料新增另一個資料表，及更新資料的簡單邏輯應用程式。

若要使用的 SQL 連接器的觸發程序，輸入 [**資料可查詢**] 和 [**投票資料查詢]**值。 在您輸入，判斷是否有任何資料的排程執行**資料使用的查詢**。 此查詢只會傳回純量的數字，因為它可以調整並經常執行最佳化。

資料使用查詢表示的資料使用**投票資料查詢**只會執行。 此陳述式交易中執行，並只認可擷取的資料長期是儲存在您的工作流程。 請務必避免不斷重新擷取相同的資料。 此執行的交易性質可刪除或更新資料，以確保它不收集下一次在查詢資料。

> [AZURE.NOTE] 傳回此陳述式的結構描述識別您連接器中可用的屬性。 必須名稱的所有資料行。

#### <a name="data-available-query-example"></a>資料使用查詢範例

    SELECT COUNT(*) FROM [Order] WHERE OrderStatus = 'ProcessedForCollection'

#### <a name="poll-data-query-example"></a>投票資料查詢範例

    SELECT *, GetData() as 'PollTime' FROM [Order]
        WHERE OrderStatus = 'ProcessedForCollection'
        ORDER BY Id DESC;
    UPDATE [Order] SET OrderStatus = 'ProcessedForFrontDesk'
        WHERE Id =
        (SELECT Id FROM [Order] WHERE OrderStatus = 'ProcessedForCollection' ORDER BY Id DESC)

### <a name="add-the-trigger"></a>新增觸發程序
1. 在建立或編輯邏輯應用程式，選取您所建立的 SQL 連接器為觸發程序。 這會列出使用引動程序︰**投票資料 (JSON)**和**投票資料 (XML)**:  
![][5]

2. 選取 [**投票資料 (JSON)**觸發程序、 輸入頻率，然後按一下 [✓︰  
![][6]

3. 邏輯應用程式中的設定，現在會顯示觸發程序。 輸出的觸發程序會顯示，並可做為輸入任何後續的動作︰  
![][7]

## <a name="use-the-connector-as-an-action"></a>使用連接器為的動作
使用簡單的邏輯應用程式案例輪詢 SQL 資料表、 資料會在另一個資料表中，新增資料，並更新的資料。

若要使用 SQL 連接器為動作，請輸入名稱的資料表及/或輸入當您建立 SQL 連接器的預存程序︰

1. 觸發程序後 （或選擇 [手動執行這個邏輯]），新增您是從圖庫建立 SQL 連接器。 選取 [插入動作，例如*插入到 TempEmployeeDetails (JSON)*的其中一個︰  
![][8]

2. 輸入的記錄，以插入，然後按一下 ✓ 輸入的值︰  
![][9]

3. 從圖庫中，選取 [相同您建立的 SQL 連接器]。 為動作，選取 [更新] 動作相同的資料表，例如*更新 EmployeeDetails*上︰  
![][11]

4. 輸入輸入的值更新動作，然後按一下 [在 ✓ 上︰  
![][12]

您可以藉由新增一筆新記錄的輪詢資料表中測試的邏輯應用程式。

## <a name="what-you-can-and-cannot-do"></a>可執行及無法執行

SQL 查詢 | 支援 | 不支援
--- | --- | ---
位置子句 | <ul><li>運算子:，或者，=、 <> <、 < =、 >，> =，例如</li><li>可以合併多個 sub 條件 」 （」 和 「） 」</li><li>字串串連，Datetime （住單引號括住），數字 （應該只能包含數字字元）</li><li>嚴格應該位於二進位運算式格式，如 ((operand operator operand) 及/或 （運算元運算子運算元）) *</li></ul> | <ul><li>運算子︰ 之間在</li><li>所有的內建功能，例如 ADD()、 max （） now （）、 POWER() 等等</li><li>數學運算子等 *、-，+ 等等</li><li>使用的字串串連 +。</li><li>所有的連接</li><li>NULL，而不是 Null</li><li>任何非數值的字元，例如十六進位數字的數字</li></ul>
欄位 （在選取的查詢） | <ul><li>有效的資料行名稱，並以逗號分隔。 沒有資料表名稱首碼允許 （一次一個資料表連接器適用於）。</li><li>名稱可以使用逸出字元 ' [」 和 「] 」</li></ul> | <ul><li>關鍵字，例如頂端、 DISTINCT、 等等</li><li>別名，例如街道 + 縣/市 + Zip 另存新檔地址</li><li>所有的內建的函數，例如 ADD()、 max （） now （）、 POWER()，等等</li><li>數學運算子，例如 *、-，+ 等等</li><li>使用的字串串連 +</li></ul>

#### <a name="tips"></a>秘訣

- 進階查詢，建議您建立的預存程序及使用儲存的執行程序 API 執行。
- 使用內部的查詢，即可使用預存程序中。
- 加入多個條件，您可以使用 「 AND 」 和 「 OR 」 運算子。

## <a name="hybrid-configuration-optional"></a>（選擇性） 的混合式設定

> [AZURE.NOTE] 只有當您使用的 SQL Server 內部部署您的防火牆需要此步驟。

應用程式服務使用混合式組態管理員安全地連線到您的內部部署系統。 如果您是連接器使用內部部署的 SQL Server，混合式連線管理員需要。

> [AZURE.NOTE] 如果您想要開始使用 Azure 邏輯應用程式註冊 Azure 帳戶之前，請移至[試邏輯應用程式](https://tryappservice.azure.com/?appservice=logic)，可以讓您立即建立短暫入門邏輯應用程式在應用程式服務。 必要; 沒有信用卡沒有承諾。

請參閱[使用混合式連線管理員](app-service-logic-hybrid-connection-manager.md)。


## <a name="do-more-with-your-connector"></a>執行更多您的連接器
現在，建立連接器時，您可以將其新增商務工作流程使用邏輯的應用程式。 請參閱[邏輯應用程式有哪些？](app-service-logic-what-are-logic-apps.md)。

在[連接器與 API 應用程式參照](http://go.microsoft.com/fwlink/p/?LinkId=529766)檢視 Swagger REST API 參照。

您也可以檢閱效能統計資料，並控制安全性連接器。 請參閱[管理與監視內建的 API 應用程式和連接器](app-service-logic-monitor-your-connectors.md)。


<!--Image references-->
[1]: ./media/app-service-logic-connector-sql/Create.png
[5]: ./media/app-service-logic-connector-sql/LogicApp1.png
[6]: ./media/app-service-logic-connector-sql/LogicApp2.png
[7]: ./media/app-service-logic-connector-sql/LogicApp3.png
[8]: ./media/app-service-logic-connector-sql/LogicApp4.png
[9]: ./media/app-service-logic-connector-sql/LogicApp5.png
[10]: ./media/app-service-logic-connector-sql/LogicApp6.png
[11]: ./media/app-service-logic-connector-sql/LogicApp7.png
[12]: ./media/app-service-logic-connector-sql/LogicApp8.png
