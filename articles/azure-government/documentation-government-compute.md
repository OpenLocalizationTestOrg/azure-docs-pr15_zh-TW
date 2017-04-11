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
    ms.date="09/29/2016"
    ms.author="ryansoc"/>


#  <a name="azure-government-compute"></a>Azure 政府運算

##  <a name="virtual-machines"></a>虛擬機器

這項服務，以及如何使用它的詳細資訊，請參閱[Azure 虛擬機器大小](../virtual-machines/virtual-machines-windows-sizes.md)。

### <a name="variations"></a>變化

下列 VM Sku 是推出 (GA) 中 Azure 政府版︰

VM SKU|美國 Gov VA|美國 Gov IA|備忘稿
---|---|---|---
A|GA|GA|無
Dv1|GA|-|無
DSv1|GA|-|無
Dv2|GA|GA|15 即將推出
F|GA|GA|無
G|計劃|-|無

###  <a name="data-considerations"></a>資料考量

下列資訊識別 Azure 政府邊界的 Azure 虛擬機器︰

| 允許管理/控制資料 | 不允許的管理/控制資料 |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 資料輸入、 儲存及處理內 VM 可以包含控制匯出資料。 執行 Azure 虛擬機器中的商務連絡人的二進位檔案。 靜態驗證器，例如密碼和 Azure 平台元件以存取智慧卡 Pin。 用來管理 Azure 平台元件憑證私密金鑰。 SQL 連接字串。  其他安全性資訊/機密資料，例如憑證、 加密金鑰、 主索引鍵和儲存 Azure 服務中的儲存空間金鑰。  | 包含匯出控制資料不允許的中繼資料。 這個中繼資料包含所有設定資料時建立和維護 Azure 虛擬機器輸入。  不要將下列欄位輸入 Regulated/控制資料︰ 租用戶角色名稱、 資源群組、 部署名稱、 資源名稱、 資源標籤  

## <a name="next-steps"></a>後續步驟

補充資訊，更新的訂閱<a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure 政府部落格。</a>
