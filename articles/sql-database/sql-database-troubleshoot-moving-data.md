<properties
    pageTitle="資料庫伺服器之間移動，之間的訂閱，並與 Azure 登出。"
    description="快速步驟來複製，移動及移轉資料和 Azure SQL 資料庫中的資料庫。"
    services="sql-database"
    documentationCenter=""
    authors="v-shysun"
    manager="felixwu"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="v-shysun"/>

# <a name="move-databases-between-servers-between-subscriptions-and-in-and-out-of-azure"></a>伺服器、 之間的訂閱，及與 Azure 登出之間移動資料庫

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]
##<a name="to-move-a-database-to-a-different-server-in-the-same-subscription"></a>若要將資料庫移到同一份訂閱中不同的伺服器
- 在[Azure 入口網站](https://portal.azure.com)中，按一下 [ **SQL 資料庫**，從清單中，選取資料庫，然後按一下 [**複製**。 如需詳細資訊，請參閱[複製 Azure SQL 資料庫](sql-database-copy.md)。

## <a name="to-move-a-database-between-subscriptions"></a>若要移動的訂閱方案之間的資料庫
- 在[Azure 入口網站](https://portal.azure.com)中，按一下 [ **SQL server** ，然後選取主控您的資料庫，從清單中的伺服器。 按一下 [**移動**，然後選擇要移動的資源和移至訂閱。

## <a name="to-migrate-a-sql-database-into-azure"></a>若要將 SQL 資料庫移轉至 Azure
- 確定資料庫的相容性，然後選取 [根據您的需求的右移轉方法。 請遵循的指導方針和[移轉 SQL Server 資料庫](sql-database-cloud-migrate.md)中的選項。

## <a name="to-create-a-copy-of-a-database-for-use-outside-of-azure"></a>若要建立使用外部 Azure 資料庫的複本
- [匯出 BACPAC 檔案。](sql-database-export.md)
