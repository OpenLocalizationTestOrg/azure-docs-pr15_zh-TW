<properties
    pageTitle="Azure 政府文件 |Microsoft Azure"
    description="此提供功能及指引的比較開發 Azure 政府版的應用程式"
    services="Azure-Government"
    cloud="gov"
    documentationCenter=""
    authors="ryansoc"
    manager="zakramer"
    editor=""/>

<tags
    ms.service="multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="azure-government"
    ms.date="10/12/2016"
    ms.author="ryansoc"/>


#  <a name="azure-government-security-and-identity"></a>Azure 政府安全性與身分識別

##  <a name="key-vault"></a>索引鍵保存庫
這項服務，以及如何使用它的詳細資訊，請參閱<a href="https://azure.microsoft.com/documentation/services/key-vault">Azure 金鑰保存庫公用文件。</a>

### <a name="data-considerations"></a>資料考量
下列資訊識別 Azure 金鑰保存庫 Azure 政府邊界︰

| 允許管理/控制資料 | 不允許的管理/控制資料 |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure 鍵保存庫使用加密的所有資料可能都包含 Regulated/控制資料。 | 包含匯出控制資料不允許 azure 金鑰保存庫中繼資料。 這個中繼資料包含所有設定資料輸入時建立和維護您鍵保存庫。  不要將下列欄位輸入 Regulated/控制資料︰ 資源群組的名稱、 金鑰保存庫的名稱、 訂閱名稱 |

通常 Azure 政府版中使用金鑰保存庫。 與公用，沒有副檔名，因此鍵保存庫才可透過 PowerShell 和 CLI 進行。

## <a name="next-steps"></a>後續步驟

補充資訊，更新的訂閱<a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure 政府部落格。</a>
