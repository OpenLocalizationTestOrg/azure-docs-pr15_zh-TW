<properties 
    pageTitle="如何管理 Azure Redis 快取 |Microsoft Azure"
    description="瞭解如何執行 Azure Redis 快取管理工作，例如重新啟動和排程的更新"
    services="redis-cache"
    documentationCenter="na"
    authors="steved0x"
    manager="douge"
    editor="tysonn" />
<tags 
    ms.service="cache"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="cache-redis"
    ms.workload="tbd"
    ms.date="09/27/2016"
    ms.author="sdanie" />

# <a name="how-to-administer-azure-redis-cache"></a>如何管理 Azure Redis 快取

本主題說明如何執行系統管理工作，例如重新啟動與排程更新您的 Azure Redis 快取執行個體。

>[AZURE.IMPORTANT] [設定] 和 [本文所述的功能只適用於進階版層快取。


## <a name="administration-settings"></a>管理設定

Azure Redis 快取**管理**設定可讓您的進階版快取中執行下列管理工作。 若要存取 [管理設定，按一下 [**設定**] 或 [**所有設定**從 Redis 快取刀並捲動至 [**管理**] 區段中**設定**刀。

![管理](./media/cache-administration/redis-cache-administration.png)

-   [重新啟動](#reboot)
-   [排程更新](#schedule-updates)

## <a name="reboot"></a>重新啟動

**重新啟動**刀可讓您重新啟動您的快取的一或多個節點。 這可讓您測試您的應用程式，恢復失敗。

![重新啟動](./media/cache-administration/redis-cache-reboot.png)

如果您有啟用叢集進階版快取，您可以選取哪些擊碎快取重新啟動電腦。

![重新啟動](./media/cache-administration/redis-cache-reboot-cluster.png)

若要重新啟動您的快取的一或多個節點，選取所要的節點並按一下 [**重新啟動**。 如果您有啟用叢集進階版快取，請選取 [重新開機，然後按一下 [**重新啟動**shard(s)]。 請稍候幾分鐘，選取的節點重開機，與線上備份稍後幾分鐘。

根據您重新啟動的節點，而異用戶端應用程式中的影響。

-   **母片**-時重新主節點，Azure Redis 快取無法透過複本節點，並將其列升階母片。 在此容錯移轉時，可能會快取連線可能失敗的簡短間隔。
-   **從屬**-時重新開機從屬節點時，都不會影響快取用戶端。
-   **主版及從屬兩**-兩個快取節點重新開機，所有資料中會都遺失快取及連線至快取失敗的主要節點上線，直到。 如果您已設定[資料保存](cache-how-to-premium-persistence.md)，快取回到線上時還原最近的備份。 請注意，在最新的備份之後所發生的任何快取將遺失。
-   **進階版快取以啟用叢集節點**-您重新啟動進階版快取的節點與叢集啟用時，行為是當您重新啟動節點非叢集快取的相同。


>[AZURE.IMPORTANT] 重新啟動只適用於進階版層快取。

## <a name="reboot-faq"></a>重新啟動的常見問題集

-   [若要測試我的應用程式必須重新哪一個節點？](#which-node-should-i-reboot-to-test-my-application)
-   [可以重新啟動若要清除的用戶端連線的快取嗎？](#can-i-reboot-the-cache-to-clear-client-connections)
-   [萬一我會遺失資料從我的快取進行重新啟動電腦嗎？](#will-i-lose-data-from-my-cache-if-i-do-a-reboot)
-   [可以重新啟動我的快取使用 PowerShell、 CLI 或其他管理工具？](#can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools)
-   [哪些價格層可以使用重開機功能嗎？](#what-pricing-tiers-can-use-the-reboot-functionality)


### <a name="which-node-should-i-reboot-to-test-my-application"></a>若要測試我的應用程式必須重新哪一個節點？

若要測試您的應用程式針對失敗的快取的主要節點恢復，重新啟動**母片**節點。 若要測試您的應用程式針對失敗的第二個節點恢復，重新啟動**從屬**節點。 若要測試您的應用程式對整體失敗的快取恢復，重新啟動**兩個**節點。

### <a name="can-i-reboot-the-cache-to-clear-client-connections"></a>可以重新啟動若要清除的用戶端連線的快取嗎？

是的如果您重新啟動快取會清除所有的用戶端連線。 這可以非常適用於所有用戶端連線使用的位置，例如邏輯錯誤或用戶端應用程式中的錯誤。 每一個價格的層級，各種規模的不同的[用戶端連線限制](cache-configure.md#default-redis-server-configuration)，而且這些限制到達後，會接受用戶端連線。 重新啟動快取提供的方式，以清除所有的用戶端連線。

>[AZURE.IMPORTANT] 如果，是因為邏輯錯誤或您的用戶端程式碼錯誤而使用您的用戶端連線，請注意 StackExchange.Redis 在恢復連線意指節點後會自動重新連線。 如果無法解決基礎問題，用戶端連線會繼續使用。

### <a name="will-i-lose-data-from-my-cache-if-i-do-a-reboot"></a>萬一我會遺失資料從我的快取進行重新啟動電腦嗎？

如果您重新啟動**母片**] 和 [**從屬**節點快取 （或如果您使用的進階版快取與啟用叢集該晶怪） 中的所有資料將都會遺失。 如果您已設定[資料保存](cache-how-to-premium-persistence.md)，快取回到線上時，將會還原最近的備份。 請注意，建立備份之後所發生的任何快取將遺失。

如果您重新啟動只是其中一個節點，資料都不通常會遺失，但仍可能。 例如，如果主節點重新開機，而且快取寫入正在進行中，從 [快取寫入的資料是遺失。 如果您重新啟動一個節點而和其他節點，因為失敗到下一次，就會遺失資料的另一種情況。 更多資料遺失的可能原因的詳細資訊，請參閱[我意指中的資料有何改變？](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md)。

### <a name="can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools"></a>可以重新啟動我的快取使用 PowerShell、 CLI 或其他管理工具？

是的如 PowerShell 指示請參閱[重新意指快取](cache-howto-manage-redis-cache-powershell.md#to-reboot-a-redis-cache)。

### <a name="what-pricing-tiers-can-use-the-reboot-functionality"></a>哪些價格層可以使用重開機功能嗎？

重新啟動是只適用於價格層進階版。

## <a name="schedule-updates"></a>排程更新

**排程更新**刀可讓您指定的快取進行的維修作業] 視窗。 指定進行的維修作業] 視窗時，任何意指伺服器會更新期間這個視窗。 請注意，進行的維修作業] 視窗僅適用於 Redis server 更新，並不到任何 Azure 更新或更新的 Vm 主控快取的作業系統。

![排程更新](./media/cache-administration/redis-schedule-updates.png)

若要指定進行的維修作業] 視窗，請核取您要的天指定每一天，進行的維修作業] 視窗開始小時，並按一下**[確定**]。 請注意，進行的維修作業] 視窗時間以 utc 表示。 

>[AZURE.NOTE] 更新的預設進行的維修作業視窗是 5 小時。 此值不是從 Azure 入口網站，可以設定，但您可以設定它在使用 PowerShell `MaintenanceWindow` [新增 AzureRmRedisCacheScheduleEntry](https://msdn.microsoft.com/library/azure/mt763833.aspx) cmdlet 的參數。 如需詳細資訊，請參閱[可以受管理使用 PowerShell、 CLI 或其他管理工具的排程的更新？](#can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools)

## <a name="schedule-updates-faq"></a>排程更新常見問題集

-   [請更新時如果我不是使用 [排程] 更新功能？](#when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature)
-   [[排程進行的維修作業] 視窗中所建立何種類型的更新？](#what-type-of-updates-are-made-during-the-scheduled-maintenance-window)
-   [我可以使用 PowerShell、 CLI 或其他管理工具受管理的排程更新？](#can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools)
-   [哪些價格層可以使用排程更新功能嗎？](#what-pricing-tiers-can-use-the-schedule-updates-functionality)

### <a name="when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature"></a>請更新時如果我不是使用 [排程] 更新功能？

如果您不指定進行的維修作業] 視窗，則可以隨時進行更新。

### <a name="what-type-of-updates-are-made-during-the-scheduled-maintenance-window"></a>[排程進行的維修作業] 視窗中所建立何種類型的更新？

僅限 Redis 的 server 排程的維護期間所做的更新。 進行的維修作業] 視窗不會套用至電子郵件地址] 或更新 VM 作業系統 Azure 更新。

### <a name="can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools"></a>我可以使用 PowerShell、 CLI 或其他管理工具受管理的排程更新？

是的您可以管理您已排程的更新，使用下列 PowerShell 指令程式。

-   [取得 AzureRmRedisCachePatchSchedule](https://msdn.microsoft.com/library/azure/mt763835.aspx)
-   [新 AzureRmRedisCachePatchSchedule](https://msdn.microsoft.com/library/azure/mt763834.aspx)
-   [新 AzureRmRedisCacheScheduleEntry](https://msdn.microsoft.com/library/azure/mt763833.aspx)
-   [移除 AzureRmRedisCachePatchSchedule](https://msdn.microsoft.com/library/azure/mt763837.aspx)

### <a name="what-pricing-tiers-can-use-the-schedule-updates-functionality"></a>哪些價格層可以使用排程更新功能嗎？

只在價格層進階版中使用排程更新。

## <a name="next-steps"></a>後續步驟

-   瀏覽更多的[Azure Redis 快取進階版層](cache-premium-tier-intro.md)功能。





