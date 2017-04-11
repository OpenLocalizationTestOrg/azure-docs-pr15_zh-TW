<properties
   pageTitle="與 Azure 儲存體 Blob 的 azure 資料湖存放比較 |Microsoft Azure"
   description="與 Azure 儲存體 Blob 的 azure 資料湖存放比較"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="08/15/2016"
   ms.author="nitinme"/>

# <a name="comparing-azure-data-lake-store-and-azure-blob-storage"></a>比較 Azure 資料湖存放和 Azure Blob 儲存體

本文中的資料表摘要列出沿著大資料處理的一些重要層面 Azure 資料湖存放和 Azure Blob 儲存體之間的差異。 Azure Blob 儲存體是一般用途，針對各種不同的儲存空間案例的可調整物件存放區。 Azure 資料湖存放是最適合用於顯示較大的資料分析負載超小數位數儲存機制。

|    | Azure 資料湖存放區  | Azure Blob 儲存體  |
|----|-----------------------|--------------------|
| 用途  | 顯示較大的資料分析負載最佳化的儲存空間                                                                          | 一般用途物件儲存的各種不同的儲存空間案例                                                                                |
| 使用案例                                   | 批次，互動，例如串流分析及電腦學習資料記錄檔，IoT 資料，請按一下 [資料流時，大型資料集 | 任何類型的文字或二進位資料，例如應用程式返回結束、 備份資料、 媒體串流和一般用途資料的儲存空間 |
| 重要概念                                | 資料湖存放帳戶含有資料夾，其中包含資料儲存為檔案 | 儲存帳戶有容器，而二進位大型物件的表單中的資料 |
| 結構 | 階層式檔案系統                                                                                                    | 使用一般命名空間的物件存放區  |
| API   | 透過 HTTPS REST API | REST API，透過 HTTP/HTTPS                                                                                                                            |
| 伺服器端 API                             | [WebHDFS 相容 REST API](https://msdn.microsoft.com/library/azure/mt693424.aspx)                                                                                                 | [Azure Blob 儲存體 REST API](https://msdn.microsoft.com/library/azure/dd135733.aspx)                                                                                                                         |
| Hadoop 檔案系統的用戶端                   | [是]                                                                                                                         | [是]                                                                                                                                                 |
| 資料作業-驗證            | 根據[Azure Active Directory 身分識別](../active-directory/active-directory-authentication-scenarios.md) | 根據共用密碼-[帳戶便捷鍵](../storage/storage-create-storage-account.md#manage-your-storage-account)及[共用的 Access 簽章鍵](../storage/storage-dotnet-shared-access-signature-part-1.md)。                                                                       |
| 資料作業-驗證通訊協定     | OAuth 2.0。 通話必須包含有效 JWT （JSON Web 權杖） 發出 Azure Active Directory                     | 雜湊訊息驗證碼 (HMAC)。 通話必須包含 Base64 編碼 sha-256 雜湊 HTTP 要求的上方。 |
| 資料作業-授權               | POSIX 存取控制清單 (Acl)。  根據 Azure Active Directory 身分識別的 Acl 可設定檔案與資料夾的層級。 | 如需授權帳戶層級 – 使用[帳戶便捷鍵](../storage/storage-create-storage-account.md#manage-your-storage-account)<br>帳戶、 容器或 blob 授權-使用[共用的 Access 簽章索引鍵](../storage/storage-dotnet-shared-access-signature-part-1.md) |
| 資料作業-稽核                    | 使用。 請參閱[以下](data-lake-store-diagnostic-logs.md)的資訊。                                                                                                                   | 使用                                                                                                                                           |
| 靜止加密資料                     | 透明的伺服器端 （即將推出）<ul><li>使用服務管理中的索引鍵</li><li>與客戶管理 Azure KeyVault 鍵</li></ul>| <ul><li>透明，伺服器端</li> <ul><li>使用服務管理中的索引鍵</li><li>與客戶管理鍵 Azure KeyVault （即將推出）</li></ul><li>用戶端加密</li></ul> |
| 管理作業 （例如帳戶建立） | [角色型存取控制](../active-directory/role-based-access-control-what-is.md)(RBAC) 所提供 Azure 帳戶管理                                                                       | [角色型存取控制](../active-directory/role-based-access-control-what-is.md)(RBAC) 所提供 Azure 帳戶管理                                                                                               |
| 開發人員 Sdk                              | .NET Java，Node.js                                                                                                         | .Net Java，Python Node.js、 c + + [注音標示                                                                                                              |
| 分析工作負載的效能              | 平行分析負載最佳化的效能。 高處理量，IOPS。                                           | 不適合用來分析工作負載                                                                                                               |
| 大小限制                                 | 沒有限制帳戶大小、 檔案大小或檔案的數目                                                                   | 限制特定記錄[以下](../azure-subscription-service-limits.md#storage-limits)                                                                                                                     |
| 地理重複                              | 本機重複 （一 Azure 區域中的資料的多份複本）                                                             | 本機多餘 (LRS)、 全域多餘 (GRS)，讀取權限全域多餘 (RA-GRS)。 請參閱[以下](../storage/storage-redundancy.md)如需詳細資訊 |
| 服務狀態                               | 公用預覽                                                                                                              | 推出                                                                                                                                 |
| 地區的可用性  | 請參閱[以下](https://azure.microsoft.com/regions/#services)| 請參閱[以下](https://azure.microsoft.com/regions/#services) |
| 價格                                       |     請參閱[價格](https://azure.microsoft.com/pricing/details/data-lake-store/)| 請參閱[價格](https://azure.microsoft.com/pricing/details/storage/) |

### <a name="next-steps"></a>後續步驟

- [Azure 資料湖存放區的概觀](data-lake-store-overview.md)
- [開始使用資料湖存放區](data-lake-store-get-started-portal.md)



