<properties
    pageTitle="命名方針的基礎結構 |Microsoft Azure"
    description="深入了解命名 Azure 基礎結構服務中的關鍵設計及實作方針。"
    documentationCenter=""
    services="virtual-machines-windows"
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/08/2016"
    ms.author="iainfou"/>

# <a name="infrastructure-naming-guidelines"></a>基礎結構的命名指引

[AZURE.INCLUDE [virtual-machines-windows-infrastructure-guidelines-intro](../../includes/virtual-machines-windows-infrastructure-guidelines-intro.md)] 

本文著重於了解如何在您的環境中建立一組邏輯且容易辨識的資源所有各種 Azure 資源的命名慣例。

## <a name="implementation-guidelines-for-naming-conventions"></a>實作方針的命名慣例

決策︰

- 什麼是 Azure 資源您命名慣例？

工作︰

- 定義使用您的資源來保持一致性 affixes。
- 定義提供這些都是唯一的全域需求儲存體帳戶名稱。
- 文件使用及散佈到跨部署一致性所有成員的命名慣例。

## <a name="naming-conventions"></a>命名慣例

Azure 中建立項目之前，您應該位置有很好的命名慣例。 命名慣例可確保所有的資源已如預期呈現的名稱，這有助於降低與管理這些資源相關聯的系統管理負擔。

您也可以選擇將一組特定的命名慣例定義為整個組織或特定 Azure 訂閱的帳戶。 雖然您可以輕鬆在組織內的個人建立隱含的規則，需要處理 Azure 中的專案小組使用 Azure 資源時，不會無法正常擴充模型。

同意的一組最前面的命名慣例。 有一些考量命名慣例剪下整個設定的規則。

## <a name="affixes"></a>Affixes

當您想要定義的命名慣例，一個決策提供 label 位於是否為︰

- 名稱 （前置詞） 的開頭
- 名稱 （後稱謂） 的結尾

例如，以下是兩個可能的名稱，資源群組使用`rg`附加︰

- 路由 WebApp （前置字元）
- WebApp 路由 （後稱謂）

Affixes 可以參照不同層面的描述特定的資源。 下表顯示一些常用的範例。

| 長寬                               | 範例                                                               | 備忘稿                                                                                                      |
|:-------------------------------------|:-----------------------------------------------------------------------|:-----------------------------------------------------------------------------------------------------------|
| 環境                          | 開發，stg，產品                                                         | 根據的目的與每個環境的名稱。                                                     |
| 位置                             | usw （西適用於美國），使用 （東亞美國 2）                                         | 根據組織的區域資料中心的區域。                               |
| Azure 元件、 服務或產品 | 用 [資源] 群組中，為虛擬網路 VNet 路由                        | 根據產品資源所提供的支援。                                          |
| 角色                                 | sql、 這個、 預存程序 iis                                                      | 根據的虛擬機器中的角色。                                                              |
| 執行個體                             | 01、 02 03 等。                                                       | 有多個執行個體的資源。 例如，負載平衡雲端服務中的網頁伺服器。 |


當建立您的命名慣例，請確定其清楚地陳述要使用哪一個位置 （前置詞與後置字元），資源的每一種類型的 affixes。

## <a name="dates"></a>日期

通常很重要決定建立資源名稱的日期。 我們建議您 YYYYMMDD 日期格式。 不只會記錄的完整日期，但也只在日期中不同名稱的兩個資源同時排序依字母順序和先後，可確保此格式。

## <a name="naming-resources"></a>命名的資源

定義每個類型中的命名慣例的資源，其應有定義如何將名稱指派給每個資源所建立的規則。 這些規則何時套用至所有類型的資源，例如︰

- 訂閱
- 帳戶
- 儲存帳戶
- 虛擬網路
- 子網路
- 可用性集
- 資源群組
- 虛擬機器
- 結束點
- 網路安全性群組
- 角色

若要確定名稱提供足夠資訊 」 來判斷所參照的資源，您必須使用描述性的名稱。

## <a name="computer-names"></a>電腦名稱

當您建立虛擬機器 (VM) 時，Microsoft Azure 需要 VM 名稱的最多 15 字元用於資源名稱。 Azure 使用 VM 中安裝的作業系統相同的名稱。 不過，這些名稱永遠可能不相同。

萬一 VM 建立從.vhd 圖像檔案已經包含作業系統，VM 名稱 Azure 中的可以與不同 VM 的作業系統的電腦名稱。 這種情況下可以新增某種程度的困難 VM 管理，因此不建議使用。 指派 Azure VM 資源您指派給該 VM 的作業系統的電腦名稱相同的名稱。

我們建議您 Azure VM 名稱為基礎的作業系統的電腦名稱相同。

## <a name="storage-account-names"></a>儲存帳戶名稱

儲存帳戶有特殊的規則的名稱。 您只能使用大小寫字母和數字。 如需詳細資訊，請參閱[建立儲存的帳戶](../storage/storage-create-storage-account.md#create-a-storage-account)。 此外，儲存體帳戶名稱，以及 core.windows.net，應該全域有效且唯一的 DNS 名稱。 例如，儲存帳戶稱為 mystorageaccount，下列產生 DNS 名稱都必須是唯一︰

- mystorageaccount.blob.core.windows.net
- mystorageaccount.table.core.windows.net
- mystorageaccount.queue.core.windows.net


## <a name="next-steps"></a>後續步驟
[AZURE.INCLUDE [virtual-machines-windows-infrastructure-guidelines-next-steps](../../includes/virtual-machines-windows-infrastructure-guidelines-next-steps.md)] 