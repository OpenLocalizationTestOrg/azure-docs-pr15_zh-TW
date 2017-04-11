<properties
    pageTitle="Azure 儲存服務加密的其餘部分資料 |Microsoft Azure"
    description="使用 Azure 儲存服務加密功能為加密服務位於您 Azure Blob 儲存體時儲存資料，並解密時擷取資料。"
    services="storage"
    documentationCenter=".net"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="robinsh"/>

# <a name="azure-storage-service-encryption-for-data-at-rest"></a>Azure 儲存服務加密的其餘的資料

Azure 儲存服務加密 (SSE) 的其餘的資料可協助您保護和保護您的資料以符合您的組織的安全性和法規遵循承諾。 使用此功能，Azure 儲存體，自動加密之前保存儲存到您的資料，並解密之前擷取。 加密、 解密和金鑰管理是完全透明給使用者。

下列各節提供如何使用的儲存空間服務加密功能，以及支援的案例和使用者的詳細的指示體驗。

## <a name="overview"></a>概觀

Azure 儲存體提供完整的安全性功能的共同啟用安全的應用程式。 資料可以使用[用戶端加密](storage-client-side-encryption.md)、 HTTPs 或中小企業 3.0 受到保護的應用程式和 Azure 之間傳送之用。 儲存服務加密提供在其餘的加密、 處理加密、 解密和金鑰管理完全透明的方式。 使用 256 位元[AES 加密](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)，其中一個強的區塊加密加密所有資料。

SSE 的運作方式寫入 Azure 儲存空間，並可用於區塊 blob，頁面 blob，附加二進位大型物件時加密資料。 這也適用於下列動作︰

-   一般用途儲存帳戶和 Blob 儲存體帳戶
-   標準的儲存空間，進階版的儲存空間 
-   所有重複層都級 （LRS ZRS、 GRS、 RA GRS）
-   Azure 資源管理員儲存帳戶 （但不是傳統） 
-   所有的區域

若要啟用或停用儲存服務加密儲存帳戶，登入[Azure 入口網站](https://azure.portal.com)，請選取儲存的帳戶。 設定防禦，以在尋找 Blob 服務] 區段中，如下圖所示，在此螢幕擷取畫面，按一下加密。

![入口網站的螢幕擷取畫面顯示 [加密] 選項](./media/storage-service-encryption/image1.png)

按一下 [加密設定之後，您可以啟用或停用儲存服務加密。

![入口網站的螢幕擷取畫面顯示加密屬性](./media/storage-service-encryption/image2.png)

##<a name="encryption-scenarios"></a>加密案例

儲存帳戶層級，就可以啟用儲存服務加密。 支援下列客戶案例︰

-   封鎖 blob 的加密附加二進位大型物件，然後頁面二進位大型物件。

-   加密封存 Vhd 及連線至 Azure 來自內部部署的範本。

-   使用您 Vhd 建立 IaaS Vm 下 OS 和資料的磁碟加密。

SSE 有以下的限制︰

-   不支援加密的傳統的儲存空間的帳戶。

-   不支援加密的傳統帳戶移轉至資源管理員儲存帳戶的儲存空間。

-   現有資料-SSE 之後啟用加密，只加密新建立的資料。 如果，例如您建立新的資源管理員儲存帳戶，但不開啟加密，然後二進位大型物件或封存的 Vhd 上傳至該儲存帳戶，然後再開啟 SSE，除非它們重新撰寫或複製不會加密這些二進位大型物件。

-   服務商場支援-啟用加密 Vm 建立使用[Azure 入口網站](https://portal.azure.com)、 PowerShell 和 Azure CLI 市集。 VHD 基底圖像仍會維持加密。不過，完成後 VM 具有開始任何寫入將會加密。

-   表格、 佇列，並不會加密檔案的資料。

##<a name="getting-started"></a>快速入門

###<a name="step-1-create-a-new-storage-accountstorage-create-storage-accountmd"></a>步驟 1︰[建立新的儲存空間帳戶](storage-create-storage-account.md)。

###<a name="step-2-enable-encryption"></a>步驟 2︰ 啟用加密。

您可以啟用加密︰ 使用[Azure 入口網站](https://portal.azure.com)。

> [AZURE.NOTE] 如果您想要以程式設計方式啟用或停用在儲存帳戶儲存服務加密，您可以使用[Azure 儲存體資源提供者 REST API](https://msdn.microsoft.com/library/azure/mt163683.aspx)、[儲存資源提供者用戶端文件庫的.NET](https://msdn.microsoft.com/library/azure/mt131037.aspx)、 [PowerShell 的 Azure](../powershell-install-configure.md)或[Azure CLI](storage-azure-cli.md)。

###<a name="step-3-copy-data-to-storage-account"></a>步驟 3︰ 將資料複製到儲存帳戶

如果您在儲存的帳戶啟用 SSE，然後寫入 blob 儲存體帳戶時，將會加密二進位大型物件。 任何 blob 儲存體帳戶中找不會加密，直到其會重新寫入。 您可以將資料從一個儲存帳戶複製到的 SSE 加密，或甚至啟用 SSE 和二進位大型物件複製到另一個容器至確定先前的資料已加密。 您可以使用下列工具的完成這項工作。

#### <a name="using-azcopy"></a>使用 AzCopy

AzCopy 是專為使用簡單的命令，以獲得最佳效能的 Microsoft Azure Blob、 檔案及資料表儲存體中往返複製資料的命令列公用程式視窗。 您可以使用此選項複製到另一個已啟用 SSE 您 blob 儲存體帳戶。 

若要深入瞭解，請造訪[傳送以 AzCopy 命令列公用程式的資料](storage-use-azcopy.md)。

#### <a name="using-the-storage-client-libraries"></a>使用儲存空間的用戶端文件庫

Blob 儲存體或使用我們豐富的儲存空間包括.NET、 c + +、 Java、 Android、 Node.js、 PHP、 Python，以及 [注音標示的用戶端程式庫的儲存空間帳戶之間，您可以複製 blob 資料。

若要深入瞭解，請造訪我們的[快速入門使用.NET Azure Blob 儲存體](storage-dotnet-how-to-use-blobs.md)。

#### <a name="using-a-storage-explorer"></a>使用儲存的檔案總管

建立儲存帳戶、 上傳和下載資料、 檢視內容的二進位大型物件，並瀏覽目錄，您可以使用儲存的檔案總管]。 您可以使用其中一種上 blob 儲存體帳戶傳與啟用加密。 使用某些儲存瀏覽器中，您也可以複製資料從現有的 blob 儲存體到不同的容器中的儲存空間帳戶或具有 SSE 啟用的新儲存帳戶。

若要深入瞭解，請造訪[Azure 儲存體探險者](storage-explorers.md)。

###<a name="step-4-query-the-status-of-the-encrypted-data"></a>步驟 4︰ 查詢加密資料的狀態

已部署，可讓您要查詢的物件以判斷是否已加密或不狀態更新的儲存空間的用戶端文件庫的版本。 範例會在不久的將來新增至這份文件。

同時，您可以呼叫驗證的儲存空間帳戶有啟用加密或 Azure 入口網站中檢視儲存帳戶內容[存取帳戶的內容](https://msdn.microsoft.com/library/azure/mt163553.aspx)。

##<a name="encryption-and-decryption-workflow"></a>加密並解密工作流程

以下是加密/解密工作流程的簡短描述︰

-   客戶可讓儲存帳戶的加密。

-   當客戶寫入 （放置 Blob、 將區塊、 將頁面等） 的新資料 Blob 儲存體。每次寫入已加密使用 256 位元[AES 加密](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)，其中一個強的區塊加密。

-   客戶需要存取 （取得 Blob 等） 的資料，資料時，會自動解密之前返回 [使用者。

-   如果已停用加密，新寫入不再加密，直到使用者重寫現有的加密的資料仍會保留加密。 啟用加密時, 將會加密寫入 Blob 儲存體。 資料狀態不會變更與使用者啟用/停用加密儲存帳戶之間切換。

-   儲存、 加密，以及由 Microsoft 管理所有加密金鑰。

##<a name="frequently-asked-questions-about-storage-service-encryption-for-data-at-rest"></a>常見問題集儲存服務加密的其餘的資料

**問︰ 我有現有的傳統的儲存空間帳戶。可以啟用 SSE 嗎？**

答︰ 不對 SSE 只支援資源管理員儲存帳戶。

**問︰ 如何加密傳統的儲存空間帳戶中的資料？**

A︰ 您可以建立新的資源管理員儲存帳戶，並複製資料使用現有的傳統的儲存空間帳戶[AzCopy](storage-use-azcopy.md)您新建立的資源管理員儲存帳戶。 

另一個選項是資源管理儲存空間帳戶移轉您傳統的儲存空間的帳戶。 如需詳細資訊，請參閱[平台支援移轉的 IaaS 資源傳統到資源管理員](https://azure.microsoft.com/blog/iaas-migration-classic-resource-manager/)。

**問︰ 我有現有的資源管理員儲存帳戶。可以啟用 SSE 嗎？**

是的但只有新撰寫 blob a︰ 將會加密。 它不會回到並加密已經有的資料。 

**問︰ 我想要加密現有的資源管理員儲存帳戶中目前的資料？**

A︰ 您可以在任何時間資源管理員儲存帳戶啟用 SSE。 不過，不會加密已經存在的二進位大型物件。 若要將加密的二進位大型物件，您可以將它們複製到另一個名稱或另一個容器，然後移除 [加密的版本。

**問︰ 我使用的進階版儲存空間。可以使用 SSE 嗎？**

答︰ 是，SSE 被支援在標準的儲存空間和進階版儲存空間。

**問︰ 如果我建立新的儲存空間帳戶並啟用 SSE，然後建立新的 VM 使用該儲存帳戶，這表示我 VM 已加密嗎？**

A: [是]。 使用新的儲存空間帳戶任何磁碟建立將加密，只要在建立之後 SSE 已啟用。 如果使用 Azure 服務商場建立 VM，VHD 基底圖像仍會維持加密。不過，完成後 VM 具有開始任何寫入將會加密。

**問︰ 可以建立新的儲存空間帳戶啟用使用 PowerShell 的 Azure 和 Azure CLI SSE 使用？**

A: [是]。

**問︰ 如何更 Azure 儲存體錢 SSE 如果？**

答︰ 沒有任何其他成本。

**問︰ 誰管理加密金鑰？**

答︰ 索引鍵是由 Microsoft 管理。

**問︰ 可以使用自己的加密金鑰？**

答︰ 我們正在處理提供的客戶，將自己的加密金鑰的功能。

**問︰ 我是否可以撤銷存取權的加密金鑰？**

答︰ 不行。按鍵完全是由 Microsoft 管理。

**問︰ 時，預設為啟用 SSE 建立新的儲存空間帳戶？**

答︰ SSE 不會啟用的預設值;若要啟用它，您可以使用 [Azure 入口網站。 您也以程式設計方式可以啟用使用儲存空間資源提供者 REST API 這項功能。

**問︰ 如何這是不同的 Azure 磁碟機加密？**

答︰ 此功能用來加密 Azure Blob 儲存體中的資料。 Azure 磁碟加密用來加密 IaaS Vm OS 和資料磁碟。 如需詳細資訊，請造訪我們的[儲存空間安全性指南](storage-security-guide.md)。

**問︰ 如果我啟用 SSE，然後移並啟用 Azure 磁碟加密磁碟上嗎？**

答︰ 這會流暢地工作。 您的資料將會加密，這兩種方法。

**問︰ 我儲存的帳戶已設定為地理多餘複寫。如果啟用 SSE，將我多餘的複本也會加密嗎？**

A: [是]，支援所有重複選項 – 本機多餘儲存空間 (LRS)、 區域多餘儲存空間 (ZRS)、 地理多餘儲存空間 (GRS)，並讀取權限地理多餘儲存空間 (RA GRS) – 及加密所有複本儲存帳戶。

**問︰ 我無法在我儲存的帳戶啟用加密。**

答︰ 是該資源管理員儲存帳戶？ 不支援傳統的儲存空間的帳戶。 

**問︰ SSE 只允許在特定的區域內嗎？**

答︰ SSE 都提供所有的區域。 

**問︰ 如何連絡其他人如果我已經有任何問題，或是要提供意見反應？**

答︰ 請連絡[ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com)的任何儲存服務加密相關的問題。

##<a name="next-steps"></a>後續步驟

Azure 儲存體提供完整的安全性功能的共同啟用安全的應用程式。 如需詳細資訊，請造訪[儲存安全性指南](storage-security-guide.md)。
