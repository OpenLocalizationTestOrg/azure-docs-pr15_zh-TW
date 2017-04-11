<properties
   pageTitle="在雲端的資料庫間的分散式的交易"
   description="彈性的資料庫交易 Azure SQL 資料庫的概觀"
   services="sql-database"
   documentationCenter=""
   authors="torsteng"
   manager="jhubbard"
   editor="torsteng"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="sql-database"
   ms.date="05/27/2016"
   ms.author="torsteng"/>

# <a name="distributed-transactions-across-cloud-databases"></a>在雲端的資料庫間的分散式的交易

彈性的資料庫交易 Azure SQL 資料庫 (SQL DB) 可讓您執行橫跨多個資料庫 SQL 資料庫中的交易。 彈性的資料庫交易的 SQL 資料庫可供使用 ADO.NET.NET 應用程式，並使用[System.Transaction](https://msdn.microsoft.com/library/system.transactions.aspx)類別的熟悉程式設計體驗與整合。 若要取得文件庫，請參閱[.NET Framework 4.6.1 （Web 安裝程式）](https://www.microsoft.com/download/details.aspx?id=49981)。

內部部署，這種狀況通常需要執行 Microsoft 分散式交易協調器 (MSDTC)。 由於 MSDTC 不適用於平台為-的-服務應用程式中 Azure，分散式的交易進行共同作業功能已經現在直接整合至 SQL 資料庫。 應用程式可以連線到任何 SQL 資料庫啟動分散式的交易，其中一個資料庫會無障礙地進行共同作業分散式的交易，如下圖所示。 

  ![使用彈性的資料庫交易 Azure SQL 資料庫使用的分散式的交易 ][1]

## <a name="common-scenarios"></a>常見的案例

彈性的資料庫交易的 SQL DB 啟用應用程式，以變更原子儲存多個不同的 SQL 資料庫中的資料。 預覽解釋 C# 和.NET 中的用戶端開發體驗。 伺服器端體驗︰ 使用 T SQL 規劃的更新版本的時間。  
彈性的資料庫交易的目標以下情況︰

* Azure 中的多重資料庫應用程式︰ 使用這個案例中，資料會垂直分割到 SQL 資料庫中的數個資料庫，例如不同類型的資料位於不同的資料庫。 某些作業需要兩個或多個資料庫中的資料的變更。 應用程式使用彈性的資料庫交易在資料庫中進行變更，並確保完整性。

* Azure 中的 sharded 資料庫應用程式︰ 使用這個案例中，資料層使用的[彈性的資料庫用戶端文件庫](sql-database-elastic-database-client-library.md)或自動 sharding 水平分割 SQL 資料庫中的許多資料庫之間的資料。 一個顯眼的使用案例是需要時變更橫跨租用戶，sharded 多租用戶應用程式的執行原子變更。 將想執行個體從一個租用戶轉接到另一個，兩個位於不同的資料庫。 第二個案例是微調 sharding 以納入其間的大型租用戶表示通常在某些原子作業需要延伸到使用相同的租用戶的數個資料庫的容量需求。 第三種情況下是不可更新參照會複寫到資料庫的資料。 沿著這些、 交易，作業現在可以協調跨多個資料庫時使用預覽中。
彈性的資料庫交易使用兩階段認可確保資料庫交易完整性。 很適合用單一交易中一次小於 100 的資料庫的交易。 不會強制執行這些限制，但其中一個應該預期的效能與降低超出這些限制時的彈性的資料庫交易的成功率。


## <a name="installation-and-migration"></a>安裝及升級

透過更新.NET 文件庫 System.Data.dll 和 System.Transactions.dll 提供彈性的資料庫交易 SQL 資料庫中的功能。 Dll 確定該兩階段的認可會使用必要時，以確保完整性。 若要開始使用彈性的資料庫交易開發應用程式，請安裝[.NET Framework 4.6.1](https://www.microsoft.com/download/details.aspx?id=49981)或更新版本。 交易在舊版的.NET framework 上執行時，將無法升級為分散式交易，並會引發例外狀況。

安裝完成後，您可以使用分散式的交易 Api System.Transactions SQL 資料庫連線。 如果您有使用這些 Api 的現有 MSDTC 應用程式，即可重新建立.NET 4.6 您現有的應用程式安裝 4.6.1 後架構。 如果您的專案目標.NET 4.6，他們會自動使用更新的 Dll 從新的架構版本，並立即繼續分散式的交易 API 通話連線至 SQL 資料庫與組合。

請記住，彈性的資料庫交易不需要安裝 MSDTC。 不過，彈性的資料庫交易是直接管理及 SQL 資料庫中。 由於 MSDTC 的部署，則不需要以 SQL DB 使用分散式的交易，這大幅簡化了雲端案例。 4 節更多詳細資料說明如何部署彈性的資料庫交易和必要的.NET framework 與 Azure 您雲端應用程式。

## <a name="development-experience"></a>開發體驗

### <a name="multi-database-applications"></a>多重資料庫應用程式

下列範例會使用.NET System.Transactions 熟悉的程式設計經驗。 TransactionScope 類別建立.net 環境交易。 （「 環境交易 」 是一個放在目前的執行緒）。開啟 TransactionScope 內的所有連線都參與交易。 如果加入不同的資料庫，交易會自動較高的分散式的交易。 設定完成表示認可範圍控制交易的結果。

    using (var scope = new TransactionScope())
    {
        using (var conn1 = new SqlConnection(connStrDb1))
        {
            conn1.Open();
            SqlCommand cmd1 = conn1.CreateCommand();
            cmd1.CommandText = string.Format("insert into T1 values(1)");
            cmd1.ExecuteNonQuery();
        }

        using (var conn2 = new SqlConnection(connStrDb2))
        {
            conn2.Open();
            var cmd2 = conn2.CreateCommand();
            cmd2.CommandText = string.Format("insert into T2 values(2)");
            cmd2.ExecuteNonQuery();
        }

        scope.Complete();
    }

### <a name="sharded-database-applications"></a>Sharded 資料庫應用程式
 
彈性的資料庫交易的 SQL DB 也支援協調分散式位置您使用的彈性的用戶端資料庫的 OpenConnectionForKey 方法來開啟出不按比例縮放的資料連線的交易層。 請考慮要跨多個不同的 sharding 關鍵值保證交易的一致性，變更的情況。 連線至裝載的不同 sharding 索引鍵值擊碎被代理使用 OpenConnectionForKey。 一般的大小寫，在連線可以是不同的擊碎，例如確保交易保證需要分散式的交易。 下列程式碼範例說明這個方法。 假設變數，稱為 「 shardmap 會用於從彈性的用戶端資料庫代表晶怪地圖︰

    using (var scope = new TransactionScope())
    {
        using (var conn1 = shardmap.OpenConnectionForKey(tenantId1, credentialsStr))
        {
            conn1.Open();
            SqlCommand cmd1 = conn1.CreateCommand();
            cmd1.CommandText = string.Format("insert into T1 values(1)");
            cmd1.ExecuteNonQuery();
        }
        
        using (var conn2 = shardmap.OpenConnectionForKey(tenantId2, credentialsStr))
        {
            conn2.Open();
            var cmd2 = conn2.CreateCommand();
            cmd2.CommandText = string.Format("insert into T1 values(2)");
            cmd2.ExecuteNonQuery();
        }

        scope.Complete();
    }


## <a name="net-installation-for-azure-cloud-services"></a>Azure 雲端服務的.NET 安裝

Azure 提供數種方案主機.NET 應用程式。 [Azure 應用程式服務、 雲端服務及虛擬機器比較](../app-service-web/choose-web-site-cloud-service-vm.md)中使用不同的方案的比較。 如果所提供的來賓 OS 小於.NET 4.6.1 所需的彈性的交易，必須先將來賓 OS 升級至 4.6.1。 

如需 Azure 應用程式服務，來賓 OS 升級目前不支援。 針對 Azure 虛擬機器中，只需登入 VM，並執行最新的.NET framework 的安裝程式。 如需 Azure 雲端服務，您需要包括.NET 新版的安裝，即可將您的部署中的啟動工作。 概念與步驟所述[安裝的.NET 雲端服務角色](../cloud-services/cloud-services-dotnet-install-dotnet.md)。  

請注意，.NET 4.6.1 的安裝程式可能會需要更多的暫存期間啟動載入的程序，安裝程式於.NET 4.6 Azure 雲端服務。 若要確保安裝成功，您需要提高暫存 Azure 雲端服務中 ServiceDefinition.csdef 檔案 LocalResources 區段和環境您啟動工作設定，如下列範例所示︰

    <LocalResources>
    ...
        <LocalStorage name="TEMP" sizeInMB="5000" cleanOnRoleRecycle="false" />
        <LocalStorage name="TMP" sizeInMB="5000" cleanOnRoleRecycle="false" />
    </LocalResources>
    <Startup>
        <Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
            <Environment>
        ...
                <Variable name="TEMP">
                    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='TEMP']/@path" />
                </Variable>
                <Variable name="TMP">
                    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='TMP']/@path" />
                </Variable>
            </Environment>
        </Task>
    </Startup>
    
## <a name="transactions-across-multiple-servers"></a>在多個伺服器的交易

彈性的資料庫交易支援透過 Azure SQL 資料庫中的不同邏輯伺服器。 當交易交互邏輯伺服器範圍時，參與的伺服器必須輸入相互通訊的關聯。 一旦建立通訊的關聯，任何資料庫中的任何兩個伺服器可以參與從其他伺服器的資料庫時的彈性交易。 跨兩個以上的邏輯伺服器的交易，通訊的關聯性必須在任何對的邏輯伺服器的位置。

您可以使用下列 PowerShell cmdlet 來管理彈性的資料庫交易的跨伺服器通訊關聯︰

* **新增 AzureRmSqlServerCommunicationLink**︰ 使用這個 cmdlet 來建立新的通訊關係 Azure SQL 資料庫中的兩個邏輯伺服器之間。 關聯性為對稱表示這兩個伺服器可以啟動交易與其他伺服器。
* **取得 AzureRmSqlServerCommunicationLink**︰ 使用這個 cmdlet 來擷取現有的通訊關聯性和其內容。
* **移除 AzureRmSqlServerCommunicationLink**︰ 使用這個 cmdlet 來移除現有的通訊關聯性。 


## <a name="monitoring-transaction-status"></a>監控交易狀態

監視器狀態和進度進行中的彈性資料庫交易的 SQL 資料庫中使用動態管理檢視 (Dmv)。 交易相關的所有 Dmv 都是 SQL 資料庫中的分佈交易相關項目。 您可以找到對應清單 Dmv 以下︰[交易相關動態管理檢視和函數 (SQL)](https://msdn.microsoft.com/library/ms178621.aspx)。
 
這些 Dmv 是非常有用︰

* **sys.dm\_交易\_作用中\_交易**︰ 列出目前使用中的交易及他們的狀態。 UOW （工時單位） 欄可以識別屬於相同的分散式交易不同子交易。 在相同的分散式交易中所有的交易執行 UOW 值相同。 請參閱更多詳細資料的[DMV 文件](https://msdn.microsoft.com/library/ms174302.aspx)。
* **sys.dm\_交易\_資料庫\_交易**︰ 提供交易，例如位置的交易記錄檔中的其他資訊。 請參閱更多詳細資料的[DMV 文件](https://msdn.microsoft.com/library/ms186957.aspx)。
* **sys.dm\_交易\_鎖定**︰ 提供目前由進行中的交易鎖定的相關資訊。 請參閱更多詳細資料的[DMV 文件](https://msdn.microsoft.com/library/ms190345.aspx)。

## <a name="limitations"></a>限制 

下列限制目前適用於 SQL 資料庫中的彈性的資料庫交易中︰

* 只有在 SQL 資料庫中的資料庫間的交易支援。 其他[X / 開啟 XA](https://en.wikipedia.org/wiki/X/Open_XA)資源提供者與 SQL DB 以外的資料庫，無法參與彈性的資料庫交易。 這表示彈性的資料庫交易無法拉內部部署的 SQL Server 和 Azure SQL 資料庫。 內部部署的分散式交易，繼續使用 MSDTC。 
* 僅限用戶端協調.NET 應用程式支援交易。 伺服器端支援 T SQL 例如開始分散式交易是計劃，但無法使用。 
* 支援只 Azure SQL 資料庫 V12 的資料庫。
* 不支援交易跨 WCF 服務。 例如，必須執行交易的 WCF 服務方法。 為[System.ServiceModel.ProtocolException](https://msdn.microsoft.com/library/system.servicemodel.protocolexception)，但交易範圍內通話會失敗。

## <a name="additional-resources"></a>其他資源

尚未 Azure 應用程式的使用彈性的資料庫功能？ 請查看我們[的說明文件地圖](https://azure.microsoft.com/documentation/learning-paths/sql-database-elastic-scale/)。 如問題，請連絡我們在[SQL 資料庫論壇](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted)上和功能要求，請將它們新增至[SQL 資料庫意見反應](https://feedback.azure.com/forums/217321-sql-database/)。

<!--Image references-->
[1]: ./media/sql-database-elastic-transactions-overview/distributed-transactions.png



