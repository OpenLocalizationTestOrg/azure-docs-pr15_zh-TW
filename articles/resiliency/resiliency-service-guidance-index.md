<properties
   pageTitle="服務恢復指引 |Microsoft Azure"
   description="連結至損毀修復與主動式恢復與可用性指導 Microsoft Azure 服務。"
   services=""
   documentationCenter="na"
   authors="adamglick"
   manager="saladki"
   editor=""/>

<tags
   ms.service="resiliency"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/18/2016"
   ms.author="aglick"/>

# <a name="microsoft-azure-service-resiliency-guidance"></a>Microsoft Azure 服務恢復指南
Microsoft Azure 是設計用來提供您需要請在需要時的資源。 做為好的設計和操作實務的一部分，您應該知道同時設計您達成可用性 Azure 服務的使用方式，以及您的應用程式受到服務中斷時該怎麼辦。 若要協助您在此程序中，這份文件包含損毀復原指引，以及各種 Azure 服務的設計指南的連結。

##<a name="disaster-recovery-guidance"></a>修復損毀的指引
下列連結會損毀復原指引可以提供可協助您快速取得您的應用程式上線，如果您受到 Azure 服務中斷所需的資訊。 建立這些連結，協助您回答的問題，「 我正在正在受到 Azure 服務中斷，該怎麼辦？ 」

##<a name="design-guidance"></a>設計指南
下面的設計指南連結是設計及建築的指導方針可協助您瞭解如何最適合使用每個 Azure 服務會最大化應用程式的執行時間的方式建立。 建立這些連結，協助您回答的問題 」 如何確定錯誤、 硬體失敗、 服務中斷或其他失敗，就不會影響整體是否可使用我的應用程式嗎？ 」 如果不有任何特定的指導方針您目前正在尋找的服務，[內建在 Microsoft Azure 應用程式的可用性](./resiliency-high-availability-azure-applications.md)文章可能可以協助您在設計中的其他資訊。 

##<a name="service-guidance"></a>服務指導方針
| 服務  | 修復損毀的指引 | 設計指南 |
|:---------|:--------------------------:|:------------------:|
| [雲端服務](https://azure.microsoft.com/services/cloud-services/ "Azure 雲端服務")       | [連結](../cloud-services/cloud-services-disaster-recovery-guidance.md "Azure 雲端服務損毀復原指引")   | 無法使用 |
| [索引鍵保存庫](https://azure.microsoft.com/services/key-vault/ "Azure 鍵保存庫")                      | [連結](../key-vault/key-vault-disaster-recovery-guidance.md "Azure 鍵保存庫損毀復原指引")        | 無法使用 |
| [儲存空間](https://azure.microsoft.com/services/storage/ "Azure 儲存體")                            | [連結](../storage/storage-disaster-recovery-guidance.md "Azure 儲存體損毀復原指引")          | 無法使用 |
| [SQL 資料庫](https://azure.microsoft.com/services/sql-database/ "Azure SQL 資料庫")           | [連結](../sql-database/sql-database-disaster-recovery.md  "Azure SQL 資料庫損毀復原指引")    | [連結](../sql-database/sql-database-business-continuity.md "概觀與 Azure SQL 資料庫的業務連續性") |
| [虛擬機器](https://azure.microsoft.com/services/virtual-machines/ "Azure 虛擬機器") | [連結](../virtual-machines/virtual-machines-disaster-recovery-guidance.md "Azure 虛擬機器損毀復原指引") | 無法使用 |
| [虛擬網路](https://azure.microsoft.com/services/virtual-network/ "Azure 虛擬網路")    | [連結](../virtual-network/virtual-network-disaster-recovery-guidance.md "Azure 虛擬網路損毀復原指引")  | 無法使用 |

##<a name="next-steps"></a>後續步驟
如果您要尋找更多人解釋系統和解決方案的指引，請閱讀[損毀修復和內建在 Microsoft Azure 應用程式的可用性](https://aka.ms/drtechguide)。
