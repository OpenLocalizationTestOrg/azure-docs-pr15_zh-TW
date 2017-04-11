<properties
   pageTitle="SQL 資料庫備份-自動、 地理多餘 |Microsoft Azure" 
   description="SQL 資料庫會自動建立本機資料庫備份每隔五分鐘，並使用 Azure 讀取權限地理多餘儲存區 (RA GRS) 提供地理重複。 "
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/20/2016"
   ms.author="carlrab;barbkess"/>

<!------------------
This topic is annotated with TEMPLATE guidelines for FEATURE TOPICS.


Metadata guidelines

pageTitle
    60 characters or less. Includes name of the feature - primary benefit. Not the same as H1. Its 60 characters or fewer including all characters between the quotes and the Microsoft Azure site identifier.

description
    115-145 characters. Duplicate of the first sentence in the introduction. This is the abstract of the article that displays under the title when searching in Bing or Google. 

    Example: "SQL Database automatically creates a local database backup every few minutes and uses Azure read-access geo-redundant storage for geo-redundancy."
------------------>

<!----------------

TEMPLATE GUIDELINES for feature topics

The Feature Topic is a one-pager (ok, sometimes longer) that explains a capability of the product or service. It explains what the capability is and characteristics of the capability.  

It is a "learning" topic, not an action topic.

DO explain this:
    • Definition of the feature terminology.  i.e., What is a database backup?
    • Characteristics and capabilities of the feature. (How the feature works)
    • Common uses with links to overview topics that recommend when to use the feature.
    • Reference specifications (Limitations and Restrictions, Permissions, General Remarks, etc.)
    • Next Steps with links to related overviews, features, and tasks.

DON'T explain this:
    • How to steps for using the feature (Tasks)
    • How to solve business problems that incorporate the feature (Overviews)
------------------->

<!------------------
GUIDELINES for the H1 
    
    The H1 should answer the question "What is in this topic?" Write the H1 heading in conversational language and use search key words as much as possible. Since this is a learning topic, make sure the title indicates that and doesn't mislead people to think this will tell them how to do tasks.  
    
    To help people understand this is a learning topic and not an action topic, start the title with "Learn about ... "

    Heading must use an industry standard term. If your feature is a proprietary name like "Elastic database pools", use a synonym. For example:    "Learn about elastic database pools for multi-tenant databases". In this case multi-tenant database is the industry-standard term that will be an anchor for finding the topic.

-------------------->

# <a name="learn-about-sql-database-backups"></a>進一步瞭解 SQL 資料庫的備份

<!------------------
    GUIDELINES for introduction
    
    The introduction is 1-2 sentences.  It is optimized for search and sets proper expectations about what to expect in the article. It should contain the top key words that you are using throughout the article.The introduction should be brief and to the point of what the feature is, what it is used for, and what's in the article. 

    If the introduction is short enough, your article can pop to the top in Google Instant Answers.

    In this example:
    
 

Sentence #1 Explains what the article will cover, which is what the feature is or does. This is also the metadata description. 
    SQL Database automatically creates a local database backup every five minutes and uses Azure read-access geo-redundant storage (RA-GRS) to provide geo-redundancy. 

Sentence #2 Explains why I should care about this.  
    Database backups are an essential part of any business continuity and disaster recovery strategy because they protect your data from accidental corruption or deletion.

-------------------->

SQL 資料庫自動建立本機資料庫備份每隔幾分鐘，並用於地理重複性的 Azure 讀取權限地理多餘的儲存空間。 資料庫備份的業務連續性和損壞復原策略不可或缺的一部分，，從意外損毀或刪除保護您的資料。 

<!-- This image needs work, so not putting it in right now.

This diagram shows SQL Database running in the US East region. It creates a database backup every five minutes, which it stores locally to Azure Read Access Geo-redundant Storage (RA-GRS). Azure uses geo-replication to copy the database backups to a paired data center in the US West region.

![geo-restore](./media/sql-database-geo-restore/geo-restore-1.png)

-->

<!---------------
GUIDELINES for the first ## H2.

    The first ## describes what the feature encompasses and how it is used. It points to related task articles.
    
    For consistency, being the heading with "What is ... "
----------------->

## <a name="what-is-a-sql-database-backup"></a>什麼是 SQL 資料庫備份？  

<!-- 
    Explains what a SQL Database backup is and answers an important question that people want to know.
-->

SQL 資料庫備份包含本機資料庫備份和地理多餘的備份。 這些備份會建立自動並不另外收費。 您不需要執行任何動作，讓它們發生這種情形。

<!----------------- 
    Explains first component of the backup feature
------------------>

本機備份，SQL 資料庫會使用 SQL Server 技術建立[完整](https://msdn.microsoft.com/library/ms186289.aspx)、[差異](https://msdn.microsoft.com/library/ms175526.aspx )，與[交易記錄檔](https://msdn.microsoft.com/library/ms191429.aspx)的備份。 交易記錄檔備份發生每個五分鐘，讓您進行到相同的伺服器裝載資料庫的時間點還原。 當您還原資料庫時，服務會找出哪些完整、 差異] 及交易記錄檔需要還原的備份。

<!--------------- 
    Explicit list of what to do with a local backup. "Use a ..." helps people to scan the topic and find the uses quickly.
---------------->

使用本機資料庫備份] 可︰

- 保留期間內的時間點還原資料庫。 使用資料庫備份，即可還原資料庫中時間點、 時間已遭刪除，還原已刪除的資料庫或還原資料庫至另一個地理區域。 若要執行還原，請參閱[還原資料庫備份的資料庫](sql-database-recovery-using-backups.md)。

- 資料庫複製到相同或不同區域中的 SQL server。 複製為目前的 SQL 資料庫交易一致。 若要執行複本，請參閱[資料庫複本](sql-database-copy.md)。

- 封存超過備份保留期資料庫備份。 若要執行的封存、[匯出 BACPAC SQL 資料庫](sql-database-export.md)檔案。 然後可以封存長期儲存至 BACPAC，並將其儲存超過您保留期限。 或者，您也可以使用 BACPAC 到 SQL Server，[內部部署或 Azure 虛擬機器 (VM)，傳送您的資料庫的複本。

<!----------------- 
    Explains first component of the backup feature
------------------>

地理多餘的備份，SQL 資料庫會使用[Azure 儲存體複寫](../storage/storage-redundancy.md)。 SQL 資料庫會儲存本機資料庫的備份檔案[讀取權限地理多餘的儲存空間 (RA GRS)](../storage/storage-redundancy.md#read-access-geo-redundant-storage)帳戶。 Azure 會將備份檔案複製到[成對的資料中心](../best-practices-availability-paired-regions.md)。 

<!--------------- 
    Explicit list of what to do with a geo-redundant backup. "Use a ..." helps people to scan the topic and find the uses quickly.
---------------->

使用地理重複備份] 可︰

- 還原資料庫至不同的地理區域，以防您無法從您的主要資料庫地區存取資料庫備份。 

>[AZURE.NOTE] Azure 儲存空間，在字詞*複寫*是指檔案複製到另一個位置。 SQL 的*資料庫複寫*指的是保留多重副的資料庫與主要資料庫同步處理。 

<!----------------
    The next ## H2's discuss key characteristics of how the feature works. The title is in conversational language and asks the question that will be answered.
------------------->
## <a name="how-much-backup-storage-is-included-at-no-cost"></a>多少儲存空間，備份是包含免費的？

SQL 資料庫備份的儲存空間免費提供最多 200%的最大能夠的資料庫儲存空間。 例如，如果您有 250 GB 的能夠 DB 大小的標準 DB 執行個體，請在不另外收費有 500 GB 的備份的儲存空間。 如果您的資料庫超過提供備份的儲存空間，您可以選擇連絡支援 Azure 減少保留期限。 另一個選項是支付的在標準的讀取權限地理區域多餘的儲存 (RA GRS) 工資率計費備份額外儲存空間。 

## <a name="how-often-do-backups-happen"></a>備份頻率改變？

本機資料庫備份的完整資料庫備份發生這種情形每週] 差異資料庫備份發生每小時及備份每隔五分鐘發生這種情形的交易記錄檔。 建立資料庫後立即安排在第一個完整的備份。 30 分鐘內，通常會完成，但嚴重大小的資料庫時可能需要更長的時間。 例如，初始備份可能需要花久上還原的資料庫或資料庫複本。 備份之後，第一個完整所有進一步備份自動排程，並在背景中自動管理上。 完整及[差異](https://msdn.microsoft.com/library/ms175526.aspx)資料庫備份的精確時間會決定為其平衡整體系統工作量。 

地理多餘的備份，完整及差異備份會複製根據 Azure 儲存體複寫排程。

## <a name="how-long-do-you-keep-my-backups"></a>您要多久保留我的備份？

每個 SQL 資料庫備份有保留期間為基礎的[服務層級](sql-database-service-tiers.md)的資料庫。 資料庫中的保留期限:

<!------------------

    Using a list so the information is easy to find when scanning.
------------------->

- 基本服務層級是七天。
- 標準服務層是 35 天。
- 進階版服務層是 35 天。


如果您降級從標準或進階版服務層級的資料庫，以基本，備份會儲存七天。 所有的現有備份七天之前已不再提供。 

如果您將從基本服務層的資料庫升級為標準或進階版時，SQL 資料庫會保留現有的備份，直到它們是 35 天以前。 35 天發生時，它會保留新的備份。
 
如果您刪除資料庫時，SQL 資料庫會保留相同的方式一樣線上資料庫的備份。 例如，假設您刪除七天的保留週期的基本資料庫。 四個天以前的備份會儲存的三天。

>[AZURE.IMPORTANT]
    如果您主控 SQL 資料庫刪除 Azure SQL server，也會一併刪除所有屬於伺服器的資料庫，且無法復原。 您無法還原已刪除的伺服器。

<!-------------------
OPTIONAL section
## Best practices 
--------------------->

<!-------------------
OPTIONAL section
## General remarks
--------------------->

<!-------------------
OPTIONAL section
## Limitations and restrictions
--------------------->

<!-------------------
OPTIONAL section
## Metadata
--------------------->

<!-------------------
OPTIONAL section
## Performance
--------------------->

<!-------------------
OPTIONAL section
## Permissions
--------------------->

<!-------------------
OPTIONAL section
## Security
--------------------->

<!-------------------
GUIDELINES for Next Steps

    The last section is Next Steps. Give a next step that would be relevant to the customer after they have learned about the feature and the tasks associated with it.  Perhaps point them to one or two key scenarios that use this feature.

    You don't need to repeat links you have already given them.
--------------------->

## <a name="next-steps"></a>後續步驟

資料庫備份的業務連續性和損壞復原策略不可或缺的一部分，，從意外損毀或刪除保護您的資料。 若要查看如何資料庫備份到狀況提出更廣義的策略，請參閱[業務連續性概觀](sql-database-business-continuity.md)。


