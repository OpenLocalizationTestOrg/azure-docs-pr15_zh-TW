<properties
    pageTitle="清單與 Microsoft Azure 儲存用戶端的文件庫的 Azure 儲存體資源 c + + |Microsoft Azure"
    description="瞭解如何使用 Microsoft Azure 儲存用戶端文件庫中 c + + 清單 Api 列舉容器、 二進位大型物件、 佇列、 表格和項目。"
    documentationCenter=".net"
    services="storage"
    authors="dineshmurthy"
    manager="jahogg"
    editor="tysonn"/>
<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="dineshm"/>

# <a name="list-azure-storage-resources-in-c"></a>C + + 清單 Azure 儲存體資源

運算列表是關鍵許多開發案例與 Azure 儲存體。 本文將說明如何最有效率地列舉 Azure 儲存體使用 Api c + + Microsoft Azure 儲存用戶端的文件庫中提供的清單中的物件。

>[AZURE.NOTE] 本指南為目標 c + + 版本 Azure 儲存用戶端程式庫 2.x，可透過[NuGet](http://www.nuget.org/packages/wastorage)或[GitHub](https://github.com/Azure/azure-storage-cpp)。

儲存空間的用戶端程式庫提供各種不同的方法來 Azure 儲存體中的清單或查詢的物件。 本文涵蓋以下情況︰

-   在帳戶中的清單容器
-   容器或虛擬 blob 目錄中的二進位大型物件清單
-   在帳戶中的清單佇列
-   在帳戶中的清單表格
-   在表格中的查詢項目

這些方法會顯示不同的情況下使用不同的多載。

## <a name="asynchronous-versus-synchronous"></a>非同步與同步

內建儲存用戶端文件庫 c + + [c + + 其餘部分文件庫](https://github.com/Microsoft/cpprestsdk)的上方，因為我們原本支援非同步作業使用[pplx::task](http://microsoft.github.io/cpprestsdk/classpplx_1_1task.html)。 例如︰

    pplx::task<list_blob_item_segment> list_blobs_segmented_async(continuation_token& token) const;

同步作業自動換行的相對應的非同步作業︰

    list_blob_item_segment list_blobs_segmented(const continuation_token& token) const
    {
        return list_blobs_segmented_async(token).get();
    }

如果您正在使用多執行緒處理的應用程式或服務，我們建議直接，而不是建立執行緒使用非同步 Api，撥打嚴重影響您的效能的 Api 同步處理。

## <a name="segmented-listing"></a>[區段的清單

雲端儲存空間的小數位數需要區段的清單。 例如，您可以讓透過 Azure blob 容器中的百萬個 blob 或億 Azure 表格中的項目。 這些不是理論的數字，但實際的客戶使用情況。

因此，這是適合清單中單一回應的所有物件。 不過，您可以列出使用分頁的物件。 每個清單 Api 有*分割*超載。

區段的清單作業的回應包含︰

-   <i>_segment</i>，其中包含一組清單 API 單一呼叫傳回的結果。
-   *continuation_token*，會傳遞至下一個通話若要取得結果的下一個頁面。 傳回沒有其他結果時，接續權杖為 null。

例如，在一般的通話清單方塊中的所有 blob 看起來可能像下列程式碼片段。 程式碼適用於我們的[範例](https://github.com/Azure/azure-storage-cpp/blob/master/Microsoft.WindowsAzure.Storage/samples/BlobsGettingStarted/Application.cpp)︰

    // List blobs in the blob container
    azure::storage::continuation_token token;
    do
    {
        azure::storage::list_blob_item_segment segment = container.list_blobs_segmented(token);
        for (auto it = segment.results().cbegin(); it != segment.results().cend(); ++it)
    {
        if (it->is_blob())
        {
            process_blob(it->as_blob());
        }
        else
        {
            process_diretory(it->as_directory());
        }
    }

        token = segment.continuation_token();
    }
    while (!token.empty());

請注意，在頁面中傳回的結果數目可以控制參數*max_results*中的每個 API，超量的狀況，例如︰

    list_blob_item_segment list_blobs_segmented(const utility::string_t& prefix, bool use_flat_blob_listing,
        blob_listing_details::values includes, int max_results, const continuation_token& token,
        const blob_request_options& options, operation_context context)

如果您沒有指定*max_results*參數，多達 5000 結果的預設最大值會傳回在單一頁面。

另請注意，針對 Azure 資料表儲存體的查詢，可能會傳回任何記錄或較少的記錄超過您指定的*max_results*參數值即使接續權杖不是空的。 其中一個原因可能是查詢無法完成五個的秒數。 只要接續權杖不是空白的應該繼續查詢，以及您的程式碼不應該假設區段結果的大小。

在大部分情況下的建議編碼模式區隔清單，其中提供的清單或查詢，和服務如何回應每一條明確的進度。 特別為 c + + 應用程式或服務，較低層級控制清單進度有助於控制項記憶體與效能。

## <a name="greedy-listing"></a>窮盡清單

舊版的儲存空間用戶端文件庫 c + + (預覽版本 0.5.0 及較舊版本) 包含非分段清單 Api 資料表和佇列，如下列範例所示︰

    std::vector<cloud_table> list_tables(const utility::string_t& prefix) const;
    std::vector<table_entity> execute_query(const table_query& query) const;
    std::vector<cloud_queue> list_queues() const;

這些方法是因為執行分段 Api 包裝函式。 每個回應的區段的清單，程式碼會加上向量結果，並傳回所有結果之後掃描完整容器。

儲存帳戶或表格中有少數幾個物件時，可能會使用這種方法。 不過的物件數目，增加，所需的記憶體可能會增加無限制，因為所有結果都留在記憶體中。 一個清單作業花費很長的時間，在來電者之間有沒有進度的相關資訊。

這些窮盡清單中 SDK 的 Api C#、 Java 或 JavaScript Node.js 環境中不存在。 若要避免使用這些窮盡 Api 的潛在問題，我們已將其移除版本 0.6.0 預覽。

如果您的程式碼呼叫這些窮盡 Api:

    std::vector<azure::storage::table_entity> entities = table.execute_query(query);
    for (auto it = entities.cbegin(); it != entities.cend(); ++it)
    {
        process_entity(*it);
    }

然後，您應該修改使用該區段的清單 Api 程式碼︰

    azure::storage::continuation_token token;
    do
    {
        azure::storage::table_query_segment segment = table.execute_query_segmented(query, token);
        for (auto it = segment.results().cbegin(); it != segment.results().cend(); ++it)
        {
            process_entity(*it);
        }

        token = segment.continuation_token();
    } while (!token.empty());

藉由指定*max_results*參數的區段，您可以一邊之間的數字的要求和記憶體使用量，以符合您的應用程式的效能考量。

此外，如果您使用的區段的清單 Api，但將資料儲存在本機集合中的 「 窮盡 」 樣式，我們也強烈建議您重構程式碼處理儲存在謹慎本機集合中的資料。

## <a name="lazy-listing"></a>延遲的清單

雖然窮盡清單引發潛在問題，但仍方便是否有不太多物件容器中。

如果您也使用 C#] 或 [Oracle Java Sdk，您應該可列舉程式設計模型，提供麻煩樣式清單，其中的資料在特定的位移只擷取視需要的部分。 C + + iterator 為基礎的範本也會提供類似的方法。

一般的麻煩清單 API，例如，使用**list_blobs**看起來像這樣︰

    list_blob_item_iterator list_blobs() const;

一般的程式碼片段麻煩清單模式可能看起來像這樣︰

    // List blobs in the blob container
    azure::storage::list_blob_item_iterator end_of_results;
    for (auto it = container.list_blobs(); it != end_of_results; ++it)
    {
        if (it->is_blob())
        {
            process_blob(it->as_blob());
        }
        else
        {
            process_directory(it->as_directory());
        }
    }

請注意，延遲清單只適用於同步模式。

相較於窮盡清單，延遲的清單會擷取資料僅在必要時。 在幕後它擷取資料從 Azure 儲存體才下一步 iterator 進入下一個區段。 因此，記憶體使用量控制受限制的大小，而且作業是快速。

延遲清單 Api 所含的儲存空間的用戶端程式庫 c + + 版本 2.2.0。

## <a name="conclusion"></a>結束時

本文中，我們討論儲存空間的用戶端文件庫中的不同物件清單 Api c + + 不同多的載。 摘要總結︰

-   在多執行緒處理的情況下，建議非同步 Api。
-   在大部分情況下，建議區段的清單。
-   延遲清單提供在同步的案例中方便包裝紙是在文件庫中。
-   建議您不要和已經從文件庫移除窮盡的清單。

##<a name="next-steps"></a>後續步驟

如需 c + + Azure 儲存體和用戶端文件庫的詳細資訊，請參閱下列資源。

-   [如何使用 c + + Blob 儲存體](storage-c-plus-plus-how-to-use-blobs.md)
-   [如何使用 c + + 資料表儲存體](storage-c-plus-plus-how-to-use-tables.md)
-   [如何使用 c + + 佇列中儲存空間](storage-c-plus-plus-how-to-use-queues.md)
-   [Azure 儲存用戶端文件庫的 c + + API 文件。](http://azure.github.io/azure-storage-cpp/)
-   [Azure 儲存體小組部落格](http://blogs.msdn.com/b/windowsazurestorage/)
-   [Azure 儲存文件](https://azure.microsoft.com/documentation/services/storage/)
