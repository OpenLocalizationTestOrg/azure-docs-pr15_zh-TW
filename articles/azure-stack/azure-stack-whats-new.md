<properties
    pageTitle="什麼是 Azure 堆疊的新 |Microsoft Azure"
    description="Azure 堆疊中的新功能"
    services="azure-stack"
    documentationCenter=""
    authors="HeathL17"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="helaw"/>

# <a name="whats-new-in-azure-stack-technical-preview-2"></a>Azure 堆疊技術預覽 2] 中的新功能
此版本提供租用戶和系統管理員的新功能。

## <a name="network"></a>網路   
   - [Idn](azure-stack-understanding-dns-in-tp2.md)提供內部網路名稱登錄及網域名稱系統 (DNS) 解析度，而其他 DNS 基礎結構。
   - [虛擬網路閘道器](azure-stack-create-vpn-connection-one-node-tp2.md)提供 Azure 或內部部署資源的 VPN 連線選項。
   - 使用者定義路由可以路由透過防火牆、 安全性、 其他裝置及其他服務的網路流量。
   - 您可以建立網路資源市集。   

## <a name="storage"></a>儲存空間
 - [Azure 佇列](https://msdn.microsoft.com/library/dd179353.aspx)啟用可靠及持續服務訊息。
 - [儲存分析](https://msdn.microsoft.com/library/azure/hh343270.aspx)擷取儲存效能資料。 您可以使用這項資料來追蹤要求、 分析趨勢，及使用您儲存的帳戶診斷問題。
 - Blob 儲存體支援[附加區塊作業](https://msdn.microsoft.com/library/azure/mt427365.aspx)。
 - 進階版儲存 API 帳戶支援。
 - 租用戶常見的工具和 Sdk，例如 Azure CLI、 PowerShell、.NET、 Python 和 Java SDK 的儲存空間服務支援。 
 - [儲存帳戶共用 Access 簽名](https://msdn.microsoft.com/library/azure/mt584140.aspx)啟用細微委派存取您儲存的服務而不必共用您的完整帳戶金鑰。  
 - 儲存服務現在使用強大的安全性與管理負擔[受管理的服務帳戶的群組](https://technet.microsoft.com/library/hh831477.aspx)。
 - 您可以回收未使用過的租用戶資源視。
 - 刪除的儲存帳戶保留長度是設定的。
 - 您可以復原已刪除的租用戶儲存帳戶。

## <a name="compute"></a>計算
- 您可以解除虛擬機器。
- 您可以重新部署用於疑難排解] 和 [組態管理虛擬機器副檔名。
- 您可以調整大小的虛擬機器磁碟。
- 虛擬機器可以有多個網路介面。

### <a name="portal-experience"></a>入口網站的體驗
 - Azure 堆疊區域是一個邏輯單位的小數位數] 和 [管理 Azure 堆疊內。 在這個預覽中，您可以檢視在計算、 網路和依地區的儲存空間等服務的資訊。
 - 您現在可以預覽 （更新） [azure-堆疊-updates.md] 介面。

## <a name="key-vault"></a>索引鍵保存庫
- [Azure 堆疊的金鑰保存庫](azure-stack-kv-intro.md)提供安全管理的索引鍵和雲端應用程式的密碼。
- 您可以稽核與監視應用程式並 Vm 金鑰的使用方式。

## <a name="billing-and-usage"></a>計費及使用方式
 - [帳單與消耗量 Api](azure-stack-billing-and-chargeback.md)公開資料如何使用您的服務。  
 - 委派的提供者啟用轉售商提供給他們的客戶的堆疊 Azure 服務。

## <a name="monitoring-and-health"></a>監控和健康狀況
 - 新監視適用於入口網站和 Api 功能可讓您主動檢視和管理您的環境中的提醒。  

## <a name="next-steps"></a>後續步驟
- [了解 Azure 堆疊 POC 架構](azure-stack-architecture.md)      
- [瞭解部署先決條件](azure-stack-deploy.md)
- [部署 Azure 堆疊](azure-stack-run-powershell-script.md)

  
