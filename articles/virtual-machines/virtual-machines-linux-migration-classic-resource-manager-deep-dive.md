<properties
    pageTitle="技術深度剖析在平台支援移轉從傳統至 Azure 資源管理員 |Microsoft Azure"
    description="本文會技術深不動產在平台支援資源的移轉，從傳統至 Azure 資源管理員"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="singhkays"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/12/2016"
    ms.author="kasing"/>

# <a name="technical-deep-dive-on-platform-supported-migration-from-classic-to-azure-resource-manager"></a>技術深度剖析在平台支援移轉從傳統至 Azure 資源管理員

在本文中，我們會執行移轉資源和功能，協助您瞭解如何在平台移轉從傳統部署模型至 Azure 資源管理員部署模型資源層級上的技術深度剖析。 如需詳細資訊，請參閱的服務公告文章︰[平台支援移轉 IaaS 傳統至 Azure 資源管理員資源](virtual-machines-windows-migration-classic-resource-manager.md)。

## <a name="detailed-guidance-on-migration-at-a-resource-and-feature-level"></a>資源與功能層級的移轉詳細的指引

下表中，您可以找到傳統和資源的資源管理員表示。 這次不支援的功能及未列出此表格中的資源。

| 傳統表示法| 資源管理員表示法 | 詳細的備忘稿 |
|--------------------------------------------------------|-------------------------------------------------|----------------------------------------------------------|
| 雲端服務名稱                                     | DNS 名稱                                        | 移轉過程中建立新的資源群組，每個雲端服務的命名圖樣`<cloudservicename>-migrated`。 此資源群組會包含所有資源。 雲端服務名稱會變成的公用 IP 位址相關聯的 DNS 名稱。                                                                                                                                                                                                    |   
| 虛擬機器                                        | 虛擬機器                                 | VM 特定的內容會移轉不變。 附註某些 osProfile 資訊，例如電腦名稱，不會儲存在傳統的部署模型，移轉後會維持空白。                                                                                                                                                                                                                                                                |   
| 附加至 VM 磁碟資源                          | 附加至 VM 隱含磁碟                   | 為資源管理員部署模型中的最上層資源模式尚未建立磁碟。 他們會移轉為下 VM 隱含磁碟。 目前我們支援附加至 VM 的磁碟。 若要啟用移轉，資源管理員 Vm 現在可以使用傳統的儲存空間的帳戶。 這個選項可讓要輕鬆地將移轉到沒有任何更新的資源管理員模型的磁碟。 |   
| VM 副檔名                                          | VM 副檔名                                   | 所有資源的延伸，除了 XML 擴充功能，會從傳統部署模型移轉。                                                                                                                                                                                                                                                                                                                                                                        |   
| 虛擬機器憑證                           | Azure 鍵保存庫中的憑證                 | 如果在雲端服務中包含服務的憑證，平台建立新 Azure 金鑰保存庫每雲端服務，並進入金鑰保存庫中的憑證。 若要從主要的保存庫參考憑證，就會更新 Vm。                                                                                                                                                                                                                               |   
| WinRM 設定                                    | 在 [osProfile 下 WinRM 設定             | Windows 遠端管理設定會移動不變，做為移轉的一部分。                                                                                                                                                                                                                                                                                                                                                                                            |   
| 可用性設定屬性                              | 資源可用性設定                       | 可用性設定規格已在傳統的部署模型 VM 上的屬性。 可用性集成為最上層的資源，做為移轉的一部分。 請注意，我們不支援下列設定︰ 顯示狀態設定為每個雲端服務，或一或多個可用性設定以及 Vm 沒有任何設定雲端服務中的可用性的多個。                                                                                |   
| 在 VM 網路設定                          | 主要網路介面                       | 在 VM 網路設定移轉後表示為主要網路介面資源。 在虛擬網路中的 Vm，內部的 IP 位址變更在移轉過程。                                                                                                                                                                                                                                                            |  
| VM 在多個網路介面                    | 網路介面                              | 如果 VM 有與其相關聯的多個網路介面，每個網路介面就會變成最上層的資源，為資源管理員部署模型] 中，以及所有屬性的移轉的一部分。                                                                                                                                                                                                                                                            |   
| 負載平衡端點設定                             | 負載平衡器                                   | 在傳統的部署模型中，在平台指派給每個雲端服務的隱含的負載平衡器。 在移轉期間，我們建立新的負載平衡器資源，請負載平衡端點設定會變成負載平衡器規則。                                                                                                                                                                                                                                     |   
| 輸入的 NAT 規則                                      | 輸入的 NAT 規則                               | 輸入的 VM 上定義的端點是下負載平衡器轉換成輸入網路存取轉譯規則移轉過程。                                                                                                                                                                                                                                                                                                                                           |   
| VIP 位址                              | 以 DNS 名稱的公用 IP 位址                 | 虛擬 IP 位址的公用 IP 位址並且負載平衡器相關聯。                                                                                                                                                                                                                                                                                                                                                        |   
| 虛擬網路                                        | 虛擬網路                                 | 將移轉虛擬網路所有內容，資源管理員部署模型。 建立新的資源群組名稱`-migrated`。 請注意[不支援的設定](virtual-machines-windows-migration-classic-resource-manager.md)。                                                                                                                                                                                                     |
| 保留的 IPs                                           | 使用靜態配置方式的公用 IP 位址  | 雲端服務的虛擬機器移轉以及負載平衡器相關聯的保留的 IPs 會移轉。 未關聯的保留的 IP 移轉不支援這一次。                                                                                                                                                                                                                                                           |   
| 每個 VM 的公用 IP 位址                               | 使用動態配置方式的公用 IP 位址 | 配置方法] 設定為靜態，VM 相關聯的公用 IP 位址會轉換為公用的 IP 位址資源。                                                                                                                                                                                                                                                                                                                                   |   
| NSGs                                | NSGs                         | 資源管理員部署模型移轉的一部分，則複製子網路相關聯的網路安全性群組。 請注意，在傳統的部署模型 NSG 不會移除移轉過程。 不過，進行移轉時，會封鎖 NSG 管理平面作業。                                                                                                                                                                             |   
| DNS 伺服器                                            | DNS 伺服器                                     | 虛擬網路或 VM 相關聯的 DNS 伺服器移轉資源移轉，以及所有屬性的一部分。                                                                                                                                                                                                                                                                                                                    |   
| UDRs                                    | UDRs                             | 子網路相關聯的使用者定義路由則複製移轉到資源管理員部署模型的一部分。 請注意，在傳統的部署模型 UDR 不會移除移轉過程。 不過，進行移轉時，會封鎖 UDR 管理平面作業。                                                                                                                                                                             |   
| IP 轉接 VM 的網路設定的屬性 | IP 轉接 nic 屬性               | 在移轉時，轉接 VM 屬性的 IP 會轉換為網路介面上的屬性。                                                                                                                                                                                                                                                                                                                                                           |   
| 多個 Ip 的負載平衡器                        | 多個的公用 IP 資源的負載平衡器 | 每個負載平衡器相關聯的公用 IP 轉換為公用 IP 資源，而在移轉之後，與負載平衡器相關聯。                                                                                                                                                                                                                                                                                                       |   
| 內部 DNS VM 上的名稱                           | 在 NIC 的內部 DNS 名稱                    | 在移轉期間，VM 的內部 DNS 尾碼會移轉到 NIC 上命名為 「 InternalDomainNameSuffix 「 唯讀屬性 移轉後稱謂則維持不變，VM 解析度應該繼續做為先前。                                                                                                                                                                                                           |   

## <a name="illustration-of-a-simple-migration-walkthrough"></a>逐步解說簡單移轉的圖例

在下列的範例螢幕擷取畫面，您可以看到以表示的雲端服務 （不在虛擬網路） VM 與準備階段之後。

![螢幕擷取畫面顯示傳統表示之後準備](./media/virtual-machines-windows-migration-classic-resource-manager/classic-migration-prepare-portal.png)
![螢幕擷取畫面顯示資源管理員表示之後準備](./media/virtual-machines-windows-migration-classic-resource-manager/resourcemanager-migration-prepare-portal.png)

## <a name="next-steps"></a>後續步驟

現在您瞭解傳統 IaaS 資源至資源管理員移轉時，您可以開始移轉資源。

- [使用 PowerShell 來從傳統至 Azure 資源管理員移轉 IaaS 資源](virtual-machines-windows-ps-migration-classic-resource-manager.md)
- [使用 CLI 從傳統至 Azure 資源管理員移轉 IaaS 資源](virtual-machines-linux-cli-migration-classic-resource-manager.md)
- [支援的平台的資源的移轉 IaaS 從傳統至 Azure 資源管理員](virtual-machines-windows-migration-classic-resource-manager.md)
- [使用社群 PowerShell 指令碼複製傳統的虛擬機器至 Azure 資源管理員](virtual-machines-windows-migration-scripts.md)
