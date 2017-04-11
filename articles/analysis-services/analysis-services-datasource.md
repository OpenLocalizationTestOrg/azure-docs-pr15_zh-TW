<properties
   pageTitle="資料來源連線 |Microsoft Azure"
   description="說明中 Azure Analysis Services 資料模型的資料來源連線。"
   services="analysis-services"
   documentationCenter=""
   authors="minewiskan"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="analysis-services"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="na"
   ms.date="10/25/2016"
   ms.author="owend"/>

# <a name="datasource-connections"></a>資料來源連線

Azure Analysis Services 中的資料模型連線至特定的資料來源時，可能需要不同的資料提供者。 在某些情況下，使用原生的提供者，例如 SQL Server Native Client (SQLNCLI11) 的資料來源連線的表格式模型，可能會傳回錯誤。

例如;如果您有記憶體中，或直接查詢資料模型的連線至雲端資料來源，例如 Azure SQL 資料庫]，如果您是使用原生 SQLOLEDB 以外的提供者，您可能會看到錯誤訊息: **「 未註冊 'SQLNCLI11.1 」 的提供者 」**。

或者，如果您有不同模型連線到內部部署資料來源，如果您是使用原生的提供者可能會看到錯誤訊息︰ **」 建立 OLE DB 資料列設定錯誤。「 限制 」 附近的語法不正確 」**。

## <a name="data-source-providers"></a>資料來源的提供者

下列資料來源提供者支援的記憶體內，或直接查詢資料模型時，連線到內部部署或雲端資料來源︰

|               | **資料來源**                     | **記憶體內**                            |  **直接查詢**                                           |
|---------------------------|-------------------------------|---------------------------------------------|---------------------------------------------|
| **雲端**                     | Azure SQL Data Warehouse      | .NET framework Data Provider for SQL Server | .NET framework Data Provider for SQL Server |
|                           | Azure SQL 資料庫            | .NET framework Data Provider for SQL Server | .NET framework Data Provider for SQL Server |
| **內部部署**（透過閘道） | SQL Server                    | SQL Server Native Client 11.0               | .NET framework Data Provider for SQL Server |
|                           |  SQL Server                             | SQL Server Microsoft OLE DB 提供者    |   .NET framework Data Provider for SQL Server                                          |
|                           |  SQL Server                             | .NET framework Data Provider for SQL Server |  .NET framework Data Provider for SQL Server                                           |
|                           | Oracle                        | Microsoft OLE DB Provider for Oracle        | Oracle Data Provider for.NET               |
|                           |  Oracle                             | Oracle Data Provider for.NET               | Oracle Data Provider for.NET                                            |
|                           | Teradata                      | Teradata 的 OLE DB 提供者                | .NET 的 Teradata 資料提供者             |
|                           |  Teradata                             | .NET 的 Teradata 資料提供者             |  .NET 的 Teradata 資料提供者                                            |
|                           | 分析平台系統 | .NET framework Data Provider for SQL Server | .NET framework Data Provider for SQL Server |


> [AZURE.NOTE] 請確定使用內部部署的閘道器時，會安裝 64 位元提供者。

當移轉至 Azure Analysis Services 的內部部署的 SQL Server Analysis Services 表格式模型，可能需要變更提供者。

**若要指定資料來源的提供者**

1. 在 [SSDT >**表格式模型總管** > **資料來源**，以滑鼠右鍵按一下資料來源連線，然後再按一下 [**編輯資料來源**。

2. 在 [**編輯連線**，按一下 [**進階]**以開啟 [進階內容] 視窗。

3. **設定 [進階摘要資訊**中 > **提供者**，然後選取適當的提供者。

## <a name="impersonation"></a>模擬
在某些情況下，可能必須指定不同的模擬帳戶。 可以在 SSDT 或 SSMS 指定模擬帳戶。

針對內部部署資料來源︰

- 如果使用 SQL 驗證，模擬應該服務帳戶。
- 如果使用 Windows 驗證，設定 Windows 使用者密碼。 SQL Server，與特定的模擬帳戶的 Windows 驗證被支援僅適用於記憶體內資料模型。

雲端資料來源︰

- 如果使用 SQL 驗證，模擬應該服務帳戶。


## <a name="next-steps"></a>後續步驟

如果您擁有內部部署資料來源，請務必安裝[內部部署的閘道器](analysis-services-gateway.md)。 若要進一步瞭解如何管理您的伺服器 SSDT 或 SSMS 中，請參閱[管理您的伺服器](analysis-services-manage.md)。
