<properties
    pageTitle="Azure 儲存複寫 |Microsoft Azure"
    description="在您的 Microsoft Azure 儲存體帳戶中的資料會持續性及可用性複寫。 複寫選項包括 [本機多餘的儲存空間 (LRS)、 區域多餘的儲存空間 (ZRS)、 地理多餘的儲存空間 (GRS) 及讀取權限地理多餘的儲存空間 (RA GRS)。"
    services="storage"
    documentationCenter=""
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="tamram"/>

# <a name="azure-storage-replication"></a>Azure 儲存複寫

您的 Microsoft Azure 儲存體帳戶中的資料永遠會以確保持續性及高可用性複寫。 複寫複製資料，在相同的資料中心，或第二個資料中心，請在您選擇複寫選項而定。 複寫保護您的資料，並保留您的應用程式-時間發生暫時性硬體失敗。 如果您的資料複製到第二個資料中心，，也會保護您的資料嚴重失敗的主索引的位置。

複寫可確保您儲存的帳戶符合[服務等級協定 (SLA) 的儲存空間](https://azure.microsoft.com/support/legal/sla/storage/)，甚至面對失敗。 請參閱 Azure 儲存體有關 SLA 保證持續性及可用性。 

當您建立的儲存空間帳戶時，您可以選取下列複寫選項的其中一個︰  

- [在本機上多餘的儲存空間 (LRS)](#locally-redundant-storage)
- [區域多餘的儲存空間 (ZRS)](#zone-redundant-storage)
- [地理多餘的儲存空間 (GRS)](#geo-redundant-storage)
- [讀取權限地理多餘的儲存空間 (RA GRS)](#read-access-geo-redundant-storage)

當您建立新的儲存空間帳戶時，讀取權限地理多餘的儲存空間 (RA GRS) 是預設的選項。

後續各節地址的每一種複寫的更多詳細資料時下, 表將提供 LRS、 ZRS、 GRS 和 RA-GRS 之間的差異的快速概觀。

| 複寫策略                                                               | LRS | ZRS | GRS | RA GRS |
|:----------------------------------------------------------------------------------|:---|:---|:---|:------|
| 跨多個資料中心複製資料。                                     | 無  | [是] | [是] | [是]    |
| 可以讀取資料，從第二個位置，以及從主要的位置。 | 無  | 無  | 無  | [是]    |
| 複製的資料來維護個別節點的數字。                             | 3   | 3   | 6   | 6      |

價格不同重複選項的資訊，請參閱[Azure 儲存體價格](https://azure.microsoft.com/pricing/details/storage/)。

>[AZURE.NOTE] 進階版存放支援只在本機上多餘的儲存空間 (LRS)。 進階版存放裝置的相關資訊，請參閱[進階版儲存空間︰ Azure 虛擬機器負載高效能儲存空間](storage-premium-storage.md)。

## <a name="locally-redundant-storage"></a>在本機上多餘的儲存空間

在本機上多餘的儲存空間 (LRS) 會將複製資料儲存區刻度] 單位是裝載在您建立您儲存的帳戶的區域資料中心內三次。 寫入要求成功傳回一次寫入所有三個的複本。 下列三個複本每個位於另一個錯誤網域和升級網域中一儲存刻度] 單位。 

儲存刻度] 單位是機架存放節點的集合。 故障網域 (FD) 是代表失敗的實體單位，可以視為節點屬於相同的實體機架節點的群組。 一群服務升級 （範圍） 的程序期間共同升級的節點升級的網域 （「 ud 」）。 三個的複本會分散 UDs 和 FDs 一儲存刻度] 單位以確保資料是可用，即使硬體設定會影響單一的機架，或當節點升級期間範圍內。 

LRS，最低的成本選項，並提供最持續性相較於其他選項。 資料中心層級損毀 (fire，癱瘓等等) 事件所有三個複本可能會遺失或無法復原。 若要降低風險地理多餘儲存空間 (GRS) 建議大部分的應用程式。

在本機上多餘的儲存空間可能仍會需要在某些情況下︰ 

- 提供最大頻寬 Azure 儲存體複寫選項。

- 如果您的應用程式儲存可以輕鬆更新的資料，您可以選擇的 LRS。

- 部分應用程式限於複寫只在因為資料管理需求的國家/地區內的資料。 成對的地區可能是在另一個國家/地區。請如需的資訊區域組，參閱[Azure 的區域](https://azure.microsoft.com/regions/)。

## <a name="zone-redundant-storage"></a>區域多餘的儲存空間

區域多餘的儲存空間 (ZRS) 會將資料非同步跨除了儲存 LRS，因此提供更高的持續性比 LRS 類似的三個複本的一或兩個區域內的資料中心。 即使主要資料中心是無法使用或無法復原，是長期 ZRS 中儲存的資料。
規劃使用 ZRS 客戶應該知道的︰ 

- ZRS 只適用於區塊二進位大型物件在一般用途儲存帳戶，並會儲存服務版本 2014年-02-14 中僅支援及更新版本。 

- 由於非同步複寫牽涉延遲，本機損毀事件可能是尚未複寫至次要資料庫的變更若無法從主要復原資料將會遺失。

- 複本可能無法使用，直到 Microsoft 起始容錯移轉至次要。

- ZRS 帳戶無法轉換成 LRS 或 GRS 的更新版本。 同樣地，現有 LRS 或 GRS 帳戶無法轉換成 ZRS 帳戶。

- ZRS 帳戶沒有指標或記錄功能。 

## <a name="geo-redundant-storage"></a>地理多餘的儲存空間

地理多餘的儲存空間 (GRS) 會將您的資料複製到下的數百個主要區域離英哩的第二個區域。 如果您儲存的帳戶有啟用 GRS，您的資料是長期即使完成的地區資料或損毀的主要區域是無法復原。

啟用 GRS 儲存帳戶，更新第一次承諾的主要區域，它會在此複寫三次。 然後更新非同步複製到第二個區域，其中也會同時複寫三次。 

GRS 主要和次要區域個別故障網域管理複本與升級中儲存縮放比例單位與 LRS 所述的網域。

考量︰

- 由於非同步複寫牽涉延遲，地區損毀事件可能是尚未複寫到第二個區域的變更會遺失，如果無法復原資料從主要的區域。

- 複本無法使用，除非 Microsoft 起始容錯移轉至第二個區域。

- 如果想要從第二區域中讀取的應用程式使用者應該啟用 RA GRS。 

當您建立的儲存空間帳戶時，您可以選取該帳戶的主要區域。 第二個區域取決於的主要區域，且無法變更。 下表顯示主要和次要地區配對。

| 主要             | 次要           |
|---------------------|---------------------|
| 北美美國中部    | 美國中部南美洲    |
| 美國中部南美洲    | 北美美國中部    |
| 設定適用於美國             | 西美制]。             |
| 西美制]。             | 設定適用於美國             |
| 美國東部 2           | 美國中部          |
| 美國中部          | 美國東部 2           |
| 北美歐洲        | 西歐         |
| 西歐         | 北美歐洲        |
| 東南亞     | 中式地址           |
| 中式地址           | 東南亞     |
| 東亞 china （中國)          | 北美 china （中國)         |
| 北美 china （中國)         | 東亞 china （中國)          |
| 日本東亞          | 日本西部          |
| 日本西部          | 日本東亞          |
| 巴西南部        | 美國中部南美洲    |
| 澳大利亞東亞      | 澳大利亞 Southeast |
| 澳大利亞 Southeast | 澳大利亞東亞      |
| 印度南部         | 印度 Central       |
| 印度 Central       | 印度南部         |
| 美國 Gov 稍後         | 美國 Gov 維吉尼亞州     |
| 美國 Gov 維吉尼亞州     | 美國 Gov 稍後         |
| 加拿大中部      | 加拿大東亞          |
| 加拿大東亞         | 加拿大中部      |
| 西部 uk （英國）             | 南部 uk （英國）            |
| 南部 uk （英國）            | 西部 uk （英國）             |
| 德國 Central     | 德國 Northeast   |
| 德國 Northeast   | 德國 Central     |
| 西美國 2           | 美國中部西     |
| 美國中部西     | 西美國 2           |

更新地區 Azure 所支援的詳細資訊，請參閱[Azure 區域](https://azure.microsoft.com/regions/)。
 
## <a name="read-access-geo-redundant-storage"></a>讀取權限地理多餘的儲存空間

讀取權限地理多餘的儲存空間 (RA GRS) 會將儲存帳戶的可用性最大化提供唯讀存取次要的位置，除了複寫跨 GRS 所提供的兩個區域中的資料。 

當您啟用唯讀存取您在第二區域中的資料時，就有一個資料位於第二個端點，除了您儲存的帳戶的主要結束點上。 第二個端點主要的端點，類似，但會將後置字元`–secondary`帳戶名稱。 例如，如果您主要的端點 Blob 服務是`myaccount.blob.core.windows.net`，為您的第二個端點`myaccount-secondary.blob.core.windows.net`。 儲存帳戶便捷鍵是相同的主要和次要結束點。

考量︰

- 您的應用程式有管理使用 RA GRS 時，與進行互動的結束點。 

- RA GRS 供高可用性。 如需延展性指引，請檢閱[效能檢查清單](storage-performance-checklist.md)。

## <a name="next-steps"></a>後續步驟

- [Azure 儲存體價格](https://azure.microsoft.com/pricing/details/storage/)
- [關於 Azure 儲存體帳戶](storage-create-storage-account.md)
- [Azure 儲存體延展性和效能目標](storage-scalability-targets.md)
- [Microsoft Azure 儲存體重複選項] 和 [讀取權限地理多餘的儲存空間](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)  
- [SOSP 紙張 Azure 儲存空間︰ 高度可用雲端儲存服務強式的一致性](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)  
