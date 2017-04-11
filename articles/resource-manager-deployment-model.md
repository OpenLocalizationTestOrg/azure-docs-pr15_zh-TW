<properties
   pageTitle="資源管理員] 及 [傳統部署 |Microsoft Azure"
   description="說明資源管理員部署模型與傳統之間的差異 （或服務管理） 部署模型。"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/27/2016"
   ms.author="tomfitz"/>

# <a name="azure-resource-manager-vs-classic-deployment-understand-deployment-models-and-the-state-of-your-resources"></a>與傳統部署 azure 資源管理員︰ 瞭解部署模型，以及您資源的狀態

本主題中，您瞭解 Azure 資源管理員] 及 [傳統部署模型，您的資源的狀態，以及您的資源已部署與一個或另一個原因。 資源管理員] 及 [傳統部署模型代表部署及管理您的 Azure 解決方案的兩種不同的方式。 您使用，透過兩個不同的 API 集，並已部署的資源可以包含重要的差異。 不完全相容的兩個模型。 本主題說明的不同點。

若要簡化的部署及管理資源，Microsoft 建議資源管理員用於所有新的資源。 如果可能的話，Microsoft 建議您重新部署現有資源透過資源管理員。

如果您是新增到資源管理員，您可能會想要先檢閱[Azure 資源管理員的概觀](azure-resource-manager/resource-group-overview.md)中定義的術語。

## <a name="history-of-the-deployment-models"></a>部署模型的歷程記錄

Azure 原本提供傳統部署模型。 在此模式中，每個資源存在獨立;沒有任何方法可以分成相關的資源。 不過，您必須手動追蹤組成您解決方案或應用程式中的資源，並請記得要管理其在互相搭配的方法。 若要部署解決方案，您必須建立個別透過 [傳統] 入口網站的每個資源] 或 [建立部署順序正確無誤的所有資源的指令碼。 若要刪除解決方案，您必須個別刪除每個資源。 不容易，您可以套用和更新存取控制 」 原則的相關的資源。 最後，您無法套用資源對字詞可協助您將它們加上標籤的標籤監控您的資源，及管理帳單。

在 2014 年，Azure 推出資源管理員，新增 [資源群組的概念。 資源群組是共用常見的生命週期的資源的容器。 資源管理員部署模型提供幾項優點︰

- 您可以部署、 管理及群組，監控您的方案的服務，而不是個別處理這些服務。
- 您可以重複部署生命週期方案，並部署資源一致的信賴度。
- 您可以套用存取控制所有資源在資源的群組，而新資源新增到資源群組時，就會自動套用原則，則。
- 您可以將標籤套用至，組織您的訂閱中的所有資源的資源。
- 您可以使用 JavaScript 物件標記法 (JSON) 來定義解決方案的基礎結構。 資源管理員範本就是 JSON 檔案。
- 您可以定義資源，因此部署順序正確無誤之間的相依性。

資源管理員已新增時，所有資源回溯都加入至預設的資源群組。 如果您建立傳統部署現在資源時，會自動建立資源在該服務的預設資源群組內即使您未指定在部署該資源群組。 不過，只要現有的資源群組內不代表資源管理員模型中已轉換的資源。 我們會查看每個服務的下一節中的兩種部署模型的處理方式。 

## <a name="understand-support-for-the-models"></a>了解支援模型 

當您判斷哪一個部署模型，以使用您的資源，有三種情況，必須注意︰

1. 服務支援資源管理員，並提供只為單一類型。
2. 服務支援資源管理員，但提供兩種類型的資源管理員和傳統的。 此案例僅適用於虛擬機器、 儲存帳戶和虛擬網路。
3. 服務不支援資源管理員。

若要探索服務是否支援資源管理員，請參閱[資源管理員支援提供者](resource-manager-supported-services.md)。

如果您想要使用的服務並不支援資源管理員，您必須繼續使用傳統的部署。

如果服務支援資源管理員並**沒有**虛擬機器、 儲存帳戶或虛擬網路，您可以使用資源管理員沒有任何的複雜性。

虛擬機器、 儲存帳戶及虛擬網路，如果資源建立傳統的部署，您必須繼續執行運算透過傳統的作業。 如果資源管理員部署建立虛擬機器、 儲存的帳戶或虛擬網路，您必須繼續使用資源管理員作業。 當您的訂閱包含建立透過資源管理員和傳統部署資源時，此區別可以取得混亂。 這種組合的資源可以建立非預期的結果，因為資源不支援的相同的作業。

在某些情況下，資源管理員命令可以擷取透過傳統部署中，建立資源的相關資訊，或執行系統管理工作，例如移動到另一個資源群組的傳統的資源。 不過，這種情況下不應將類型的支援資源管理員作業的印象。 例如，假設您有包含虛擬機器以傳統部署所建立的資源群組。 如果您執行下列資源管理員 PowerShell 命令︰

    Get-AzureRmResource -ResourceGroupName ExampleGroup -ResourceType Microsoft.ClassicCompute/virtualMachines

會傳回虛擬機器︰
    
    Name              : ExampleClassicVM
    ResourceId        : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.ClassicCompute/virtualMachines/ExampleClassicVM
    ResourceName      : ExampleClassicVM
    ResourceType      : Microsoft.ClassicCompute/virtualMachines
    ResourceGroupName : ExampleGroup
    Location          : westus
    SubscriptionId    : {guid}

不過，資源管理員 cmdlet**取得 AzureRmVM**只會傳回虛擬機器部署透過資源管理員。 下列命令不會傳回建立傳統的部署的虛擬機器。

    Get-AzureRmVM -ResourceGroupName ExampleGroup

僅建立資源透過資源管理員支援標籤。 您無法將標籤套用至傳統的資源。

## <a name="resource-manager-characteristics"></a>資源管理員特性

若要可協助您瞭解兩種模式，讓我們來檢閱資源管理員類型的特性︰

- 透過[Azure 入口網站](https://portal.azure.com/)建立。

     ![Azure 入口網站](./media/resource-manager-deployment-model/portal.png)

     用於計算儲存空間，與網路資源，您可以使用資源管理員或傳統部署的選擇。 選取**資源管理員**]。

     ![資源管理員部署](./media/resource-manager-deployment-model/select-resource-manager.png)

- 建立與資源管理員新版 Azure PowerShell cmdlet。 這些命令具有*動詞 AzureRmNoun*的格式。

        New-AzureRmResourceGroupDeployment

- 建立透過[Azure 資源管理員 REST API](https://msdn.microsoft.com/library/azure/dn790568.aspx)其餘作業。

- 建立透過 Azure CLI 命令在**arm**模式中執行。

        azure config mode arm
        azure group deployment create 

- 資源類型不包含**（傳統）**的名稱。 下圖會顯示為**儲存的帳戶**類型。

    ![web 應用程式](./media/resource-manager-deployment-model/resource-manager-type.png)

## <a name="classic-deployment-characteristics"></a>傳統部署特性

您也可能會知道傳統部署模型服務管理模型。

資源傳統部署模型中建立共用下列特性︰

- 建立到[傳統的入口網站](https://manage.windowsazure.com)

     ![傳統的入口網站](./media/resource-manager-deployment-model/classic-portal.png)

     或者，Azure 入口網站，並指定 [**傳統**部署 （適用於計算、 儲存及網路）。

     ![傳統的部署](./media/resource-manager-deployment-model/select-classic.png)

- 建立透過 Azure PowerShell cmdlet 的服務管理版本。 下列命令名稱具有*動詞 AzureNoun*的格式。

        New-AzureVM 

- 建立透過[服務管理 REST API](https://msdn.microsoft.com/library/azure/ee460799.aspx)其餘作業。
- 建立透過 Azure CLI 命令在**asm**模式中執行。

        azure config mode asm
        azure vm create 

- 資源類型] 名稱中包含**（傳統）** 。 下圖顯示為**（傳統） 的儲存空間帳戶**的類型。

    ![傳統的類型](./media/resource-manager-deployment-model/classic-type.png)

您可以使用 [Azure 入口網站管理透過傳統部署所建立的資源。

## <a name="changes-for-compute-network-and-storage"></a>變更計算、 網路和儲存空間

下圖顯示計算、 網路和儲存部署透過資源管理員的資源。

![資源管理員架構](./media/virtual-machines-azure-resource-manager-architecture/arm_arch3.png)

請注意下列資源關係︰

- 資源群組內，存在的所有資源。
- 虛擬機器取決於儲存資源提供者，將其磁碟 blob 儲存體 （必填） 中定義的特定儲存帳戶。
- 虛擬機器參照網路資源提供者 （必填） 和可用性設定定義計算資源的提供者 （選用） 中定義的特定 NIC。
- 在 NIC 參照虛擬機器指派的 IP 位址 （必填） 虛擬機器 （必填），以及網路安全性群組 （可省略） 的虛擬網路的子網路。
- 內虛擬網路的子網路參照網路安全性群組 （選擇性）。
- 負載平衡器執行個體參照包含的虛擬機器 （選擇性） 在 NIC 及參照一個負載平衡器公用或私用的 IP 位址 （可省略） 的 IP 位址的後端資料庫。

以下是元件和傳統部署及其關聯︰

![傳統架構](./media/virtual-machines-azure-resource-manager-architecture/arm_arch1.png)

裝載虛擬機器傳統的方案包括︰

- 必要的雲端服務，做為容器的主機服務虛擬機器 （計算）。 虛擬機器會自動提供網路介面卡 (NIC) 和 Azure 所指派的 IP 位址。 此外，雲端服務包含外部負載平衡器執行個體與公用的 IP 位址]，允許 Windows 型虛擬機器的遠端桌面與遠端 PowerShell 流量和 Linux 型虛擬機器安全命令介面 (SSH) 流量的預設端點。
- 必要的儲存空間帳戶儲存 Vhd 虛擬機器，包括作業系統，暫時，且其他資料磁碟 （儲存）。
- 選用的虛擬網路做為其他的容器，您可以在其中建立子的結構，並指定虛擬機器所在的子網路 （網路）。

下表說明變更計算、 網路和儲存空間的資源提供者互動的方式︰

 項目 | 傳統 | 資源管理員
 ---|---|---
| 雲端服務的虛擬機器 |  雲端服務已保留平台與負載平衡必要可用性虛擬機器的容器。 | 雲端服務已不再需要建立虛擬機器使用新的模型物件。 |
| 虛擬網路 | 虛擬網路是選擇性的虛擬機器。 如果包含，虛擬網路無法部署與資源管理員。 | 虛擬機器需要虛擬網路與資源管理員已部署。 |
| 儲存帳戶 | 虛擬機器需要儲存作業系統，請暫時，且其他資料磁碟 Vhd 儲存帳戶。 | 虛擬機器需要將其磁碟 blob 儲存體中的儲存空間帳戶。 |
| 可用性集 | 藉由虛擬機器中設定相同的 「 AvailabilitySetName 」 指出可用性]，以在平台。 故障網域的最大值為 2。 | 顯示狀態設定為資源所公開之 Microsoft.Compute 提供者。 需要可用性的虛擬機器必須包含在可用性設定。 故障網域的最大次數現在 3。 |
| 相關性群組 | 相關性群組所需建立虛擬網路。 不過，隨著地區的虛擬網路，，不需要再。 |若要簡化，公開透過 Azure 資源管理員的 Api 中不存在的相關性群組概念。 |
| 負載平衡    | 雲端服務建立隱含的負載平衡器提供部署虛擬機器。 | 負載平衡器為資源所公開之 Microsoft.Network 提供者。 需要負載平衡虛擬機器的主要網路介面應該參考負載平衡器。 負載平衡器可以內部或外部。 負載平衡器執行個體參照包含的虛擬機器 （選擇性） 在 NIC 及參照一個負載平衡器公用或私用的 IP 位址 （可省略） 的 IP 位址的後端資料庫。 [閱讀更多。](../articles/resource-groups-networking.md) |
|虛擬 IP 位址 | 雲端服務時發生 VM 會新增至雲端服務的預設 VIP （虛擬 IP 位址）。 虛擬 IP 位址是隱含負載平衡器相關聯的位址。    | 公用 IP 位址是公開 Microsoft.Network 提供者的資源。 靜態 （保留） 或動態，可使用的公用 IP 位址。 動態的公用 Ip 可以指派給負載平衡器。 公用 IPs 可以使用安全性群組受到保護。 |
|保留的 IP 位址|   您可以保留 Azure 中的 IP 位址，並與雲端服務，以確保的 IP 位址是自黏產生關聯。   | 「 靜態 」 模式中可以建立的公用 IP 位址，並提供為 「 保留 IP 位址 」 的相同功能。 靜態的公用 Ip 可以只指派給負載平衡器立即。 |
|每個 VM 的公用 IP 位址 (PIP) | 公用 IP 位址也可以與 VM 直接。 | 公用 IP 位址是公開 Microsoft.Network 提供者的資源。 靜態 （保留） 或動態，可使用的公用 IP 位址。 不過，動態的公用 Ip 可以立即取得每 VM 的公用 IP 指派給網路介面。 |
|結束點| 虛擬機器開啟設定為特定的連接埠的連線設定，需要輸入的結束點。 下列其中一項連線至虛擬機器完成設定輸入的結束點的常見模式。 | 輸入的 NAT 規則可以在達成相同的功能啟用上特定的連接埠需連線至 Vm 端點的負載平衡器設定。 |
|DNS 名稱| 在雲端服務會取得隱含的全域唯一 DNS 名稱。 例如︰ `mycoffeeshop.cloudapp.net`。 | DNS 名稱都可以指定的公用 IP 位址資源的選擇性參數。 FQDN 是以下列格式- `<domainlabel>.<region>.cloudapp.azure.com`。 |
|網路介面 | 主要和次要網路介面和其內容已定義的虛擬機器網路設定。 | 網路介面為資源所公開之 Microsoft.Network 提供者。 網路介面的生命週期不受限於虛擬機器。 它會參考虛擬機器指派的 IP 位址 （必填） 虛擬機器 （必填），以及網路安全性群組 （可省略） 的虛擬網路的子網路。 |

若要瞭解如何從不同的部署模型連線虛擬網路，請參閱[連線虛擬網路，從不同的部署模型，在入口網站](./vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md)。

## <a name="migrate-from-classic-to-resource-manager"></a>從傳統移轉到資源管理員

如果您已準備好將您的資源移轉到資源管理員部署傳統的部署，請參閱︰

1. [技術深度剖析在平台支援移轉從傳統至 Azure 資源管理員](./virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
2. [支援的平台 IaaS 資源傳統至 Azure 資源管理員移轉](./virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
3. [IaaS 資源從移轉傳統至 Azure 資源管理員使用 PowerShell 的 Azure](./virtual-machines/virtual-machines-windows-ps-migration-classic-resource-manager.md)
4. [IaaS 資源從移轉傳統至 Azure 資源管理員使用 Azure CLI](./virtual-machines/virtual-machines-linux-cli-migration-classic-resource-manager.md)

## <a name="frequently-asked-questions"></a>常見問題集

**可以建立虛擬機器部署虛擬網路或儲存帳戶建立使用傳統的部署文件中使用 Azure 資源管理員嗎？**

不支援此功能。 您無法使用 Azure 資源管理員虛擬網路，建立使用傳統的部署到部署虛擬機器。

**可以建立虛擬機器使用從 Azure 服務管理 Api 建立的使用者圖像 Azure 資源管理員嗎？**

不支援此功能。 不過，您可以複製 VHD 檔案儲存空間帳戶使用服務管理的 Api，建立的並將其新增至 [透過 Azure 資源管理員建立新的帳戶。

**什麼是對我的訂閱的配額的影響？**

虛擬機器、 虛擬網路，及建立透過 Azure 資源管理員中的儲存空間帳戶配額是獨立於其他配額。 每個訂閱取得建立使用新的 Api 資源配額。 您可以閱讀更多關於其他配額[以下](../articles/azure-subscription-service-limits.md)。

**我可以繼續使用我自動化的指令碼佈建虛擬機器、 虛擬網路和資源管理員 Api 的儲存空間帳戶嗎？**

所有的自動化及您建立的指令碼可繼續使用現有的虛擬機器 Azure 服務管理模式下建立虛擬網路。 不過，指令碼有更新，以使用新的結構描述建立相同資源的資源管理員模式。

**哪裡可以找到 Azure 資源管理員範本的範例？**

完整的入門範本位於[Azure 資源管理員快速入門範本](https://azure.microsoft.com/documentation/templates/)。

## <a name="next-steps"></a>後續步驟

- 若要逐步建立的範本，以定義的虛擬機器、 儲存帳戶和虛擬網路，請參閱[資源管理員範本逐步解說](resource-manager-template-walkthrough.md)。
- 若要查看部署範本的命令，請參閱[部署具有 Azure 資源管理員範本的應用程式](resource-group-template-deploy.md)。
