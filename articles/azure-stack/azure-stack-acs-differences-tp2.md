
<properties
    pageTitle="Azure 一致的儲存空間︰ 差異和考量 |Microsoft Azure"
    description="瞭解從 Azure 儲存體和其他 Azure 一致的儲存空間部署考量差異。"
    services="azure-stack"
    documentationCenter=""
    authors="MChadalapaka"
    manager="siroy"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/26/2016"
    ms.author="mchad"/>

# <a name="azure-consistent-storage-differences-and-considerations"></a>Azure 一致的儲存空間︰ 差異和考量

Azure 一致的儲存空間是儲存在 Microsoft Azure 堆疊的雲端服務的設定。 Azure 一致的儲存空間提供 blob、 表格、 佇列中，以及 Azure 一致的語意與帳戶管理功能。 這份文件摘要列出已知的 Azure 一致的儲存空間差異與 Azure 儲存體。 也摘要列出其他考量事項請記住，當您部署目前可公開使用 preview 版本的 Microsoft Azure 堆疊。

<span id="Concepts" class="anchor"><span id="_Toc386544169" class="anchor"><span id="_Toc389466742" class="anchor"><span id="_Ref428966996" class="anchor"><span id="_Toc433223853" class="anchor"></span></span></span></span></span>
## <a name="known-differences"></a>已知的差異

Azure 一致的儲存空間此 Technical Preview 版本沒有 100%功能不一致性致歉與 Azure 儲存體支援 API 版本。 已知的功能差異包括下列各項︰

-   某些儲存的帳戶類型目前尚不提供，例如標準\_RAGRS 和標準\_GRS。

-   進階版\_可以佈建 LRS 儲存帳戶。 不過，目前沒有任何效能限制或保證。

-   Azure 檔案功能尚未提供。

-   取得頁面範圍 API 不支援擷取的頁面 blob 的快照之間的不同的頁面。

-   取得頁面範圍 API 傳回具有 4 KB 的資料粒度的頁面。

-   分割索引鍵和 Azure 一致的儲存空間表格實作中的資料列鍵分別 400 字元 （也就是 800 位元組） 的大小。

-   Azure 一致的儲存空間 Blob 服務實作 blob 名稱限於 880 字元 （也就是 1760 位元組） 的大小。

-   租用戶儲存狀況報告提供相同的 Azure 具有相同的語意和單位的儲存空間使用公尺 Azure 一致儲存實作。 目前，不過，儲存交易使用計量表不包括 IaaS 交易及資料傳輸使用計量表不做任何區別頻寬使用量的 Azure 堆疊區域的內部或外部網路流量。

-   某些差異存在於儲存性功能的範圍。 例如，您無法變更的帳戶類型，或有自訂網域。 此外，只 API 層級一致性提供進階版\_LRS 儲存的帳戶類型。

## <a name="deployment-considerations"></a>部署考量

-   **僅限測試。** 不要部署在生產環境中使用目前的 Microsoft Azure 堆疊 Technical Preview 版本的 Azure 一致的儲存空間。 僅供評估之用，實驗室環境中用於此版本。

-   **效能**。 Azure 一致的儲存空間的 Microsoft Azure 堆疊 Technical Preview 版本不完整的效能最佳化。

-   **延展性。** Azure 一致的儲存空間的 Microsoft Azure 堆疊 Technical Preview 版本不適合延展性。

## <a name="version-support-considerations"></a>版本支援考量

下列版本支援這個預覽版本中的 Azure 一致的儲存空間︰

> Azure 儲存用戶端文件庫︰ [Microsoft Azure 儲存體 6.x.NET SDK](http://www.nuget.org/packages/WindowsAzure.Storage/6.2.0)
>
> Azure 儲存資料服務︰ [2015年-04-05 REST API 版本](https://msdn.microsoft.com/library/azure/mt705637.aspx)
>
> Azure 儲存體管理服務︰ [2015年 05-01-預覽](https://msdn.microsoft.com/library/azure/mt163683.aspx)
> [2015年 06-15](https://msdn.microsoft.com/library/azure/mt163683.aspx)
## <a name="next-steps"></a>後續步驟

-   [Azure 一致的儲存空間的簡介](azure-stack-storage-overview.md)
