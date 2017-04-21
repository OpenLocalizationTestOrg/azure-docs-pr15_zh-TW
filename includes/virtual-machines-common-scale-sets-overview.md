

應用程式的需要調整計算資源看]，並在隱含對稱作業的縮放比例發生錯誤，並更新的網域。 簡介 VM 縮放比例設定請參閱最近[Azure 部落格公告](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview/)。

查看這些影片有關 VM 縮放比例設定的詳細資訊︰

 - [標記 Russinovich 說話 Azure 縮放設定](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)  

 - [虛擬機器縮放比例設定為使用 Guy Bowerman](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

## <a name="creating-and-managing-vm-scale-sets"></a>建立及管理 VM 縮放設定

VM 比例集可以定義和部署使用 JSON 範本和[REST Api](https://msdn.microsoft.com/library/mt589023.aspx)只等個別 Azure 資源管理員 Vm。 因此，您可以使用任何標準 Azure 資源管理員部署方法。 如需有關範本的詳細資訊，請參閱[撰寫 Azure 資源管理員範本](../articles/resource-group-authoring-templates.md)。

一組 VM 比例組範例範本，請參閱 Azure 快速入門範本 GitHub 存放庫這裡︰

[https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates) - _vmss_在標題中使用的範本外觀。

這些範本的詳細資料頁面中，您會看到入口網站的部署功能連結] 按鈕。 若要部署 VM 縮放設定，按一下 [] 按鈕，然後填入入口網站中所需任何參數。 如果您不確定是否為資源，支援更安全，若要一律使用小寫參數值中的大寫或混合大小寫。 此外，還有 VM 縮放比例設定範本以下的方便視訊 dissection:

[VM 縮放比例設定範本 Dissection](https://channel9.msdn.com/Blogs/Windows-Azure/VM-Scale-Set-Template-Dissection/player)

## <a name="scaling-a-vm-scale-set-out-and-in"></a>看]，並在 [縮放比例 VM 縮放設定

若要增加或減少的虛擬機器中 VM 縮放比例設定數，只要將_容量_屬性變更，然後重新部署範本。 這種可讓您更輕鬆地撰寫您自己自訂的縮放比例圖層，如果您想要定義自訂的比例 Azure 自動調整大小不支援的事件。

如果您部署範本來變更容量，您可定義多小範本只包含 [SKU 和更新的容量。 此處所示的範例︰ [https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-scale-in-or-out/azuredeploy.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-linux-nat/azuredeploy.json)。

若要逐步建立自動縮放比例設定的步驟，請參閱[自動縮放比例機器集中虛擬機器縮放比例](../articles/virtual-machines/virtual-machines-windows-vmss-powershell-creating.md)

## <a name="monitoring-your-vm-scale-set"></a>監控您 VM 縮放設定

目前建議您使用[Azure 資源檔案總管](https://resources.azure.com)來檢視 VM 縮放比例設定。 VM 縮放比例設定會是下 Microsoft.Compute，資源，以便從這個網站您展開下列連結︰

    subscriptions -> your subscription -> resourceGroups -> providers -> Microsoft.Compute -> virtualMachineScaleSets -> your VM scale set -> etc.

## <a name="vm-scale-set-scenarios"></a>VM 縮放比例設定案例

本節會列出一些標準 VM 縮放比例設定案例。 某些高階 Azure 服務 （例如批次服務布料的轉印圖樣、 Azure 容器服務） 會使用這些案例。

 - **RDP / SSH VM 縮放設定執行個體**-VM 縮放比例設定會建立內 VNET 及個別 Vm 中的不會配置的公用 IP 位址。 因為您通常不想配置在計算格線中，所有的無資源的個別 IP 位址的費用和管理負擔，您可以輕鬆地中連線至這些 Vm 與其他資源包括的公用 IP 位址，例如負載平衡器或獨立虛擬機器您 VNET，這是一種很好的項目。

 - **連線到 Vm 使用 NAT 規則**-您可以建立的公用 IP 位址，將它指派給負載平衡器，並定義輸入的 NAT 規則在 VM 縮放比例設定 VM 的連接埠對應的 IP 位址的連接埠。 例如︰

    公用 IP]-> [連接埠 50000 vmss]-> [\_0]-> [連接埠 22 公用 IP-> 連接埠 50001]-> [vmss\_1]-> [連接埠 22 公用 IP-> 連接埠 50002]-> [vmss\_2]-> [連接埠 22

    建立使用 NAT 規則啟用 SSH 連線設定使用單一的公用 IP 的比例中每個 VM VM 縮放比例設定的範例如下︰ [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat)

    以下是執行 Windows RDP 與相同的範例︰ [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat)

 - **連線到 Vm 使用 「 jumpbox 」** -如果您建立的 VM 比例設定，然後 VM 相同 VNET、 獨立 VM 和 Vm 可以連線至另一個使用其內部 IP VM 縮放比例設定獨立地址所定義的 VNET/子網路。 如果您建立的公用 IP 位址，並將其指派給獨立 VM 可以 RDP 或 SSH 獨立 VM 然後連線到您 VM 縮放比例設定執行個體的 [從電腦。 您可能會注意到此時簡單的 VM 縮放比例設定為較為安全比簡單的獨立 VM 預設設定的公用 IP 位址。

    如需這種方法的範例，此範本會建立簡單 Mesos 叢集由獨立的母片 VM 管理 Vm 基礎 VM 縮放比例設定叢集︰ [https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json](https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json)

 - **循環負載平衡 VM 縮放比例設定執行個體**-如果您想要的 「 循環 」 的方法 Vm 叢集進行工作，您可以設定 Azure 負載平衡器與負載平衡據此規則。 您也可以定義以驗證您的應用程式正在執行 ping 探查指定通訊協定、 間隔和邀請路徑的連接埠。

    以下是範例會建立 VM 比例集 Vm 執行 IIS 網頁伺服器，並使用負載平衡器平衡負載每個 VM 接收。 它也會以特定的 URL，在每個 VM 偵測 （ping） 使用 HTTP 通訊協定︰ [https://github.com/gbowerman/azure-myriad/blob/master/vmss-win-iis-vnet-storage-lb.json](https://github.com/gbowerman/azure-myriad/blob/master/vmss-win-iis-vnet-storage-lb.json) -看 Microsoft.Network/loadBalancers 資源類型的 networkProfile 及 extensionProfile virtualMachineScaleSet 中的。

 - **部署 VM 縮放比例設定為在 PaaS 叢集管理員叢集**-VM 集有時稱為 [下一步] 產生工作者角色的小數位數。 是有效的描述，但它也混亂的小數位數的風險設定功能會以執行 PaaS v1 工作者角色功能。 在意義 VM 縮放比例設定提供 true 」 工作者角色 」] 或 [背景工作資源所提供的平台/runtime 獨立，一般化的計算資源的可自訂及整合到 Azure 資源管理員 IaaS。

    PaaS v1 工作者角色，龐大的平台/執行階段支援 （僅限 Windows 平台圖像） 有限時也包含服務，例如 VIP 交換可設定的升級設定、 執行階段/應用程式部署特定設定不會_還_提供 VM 縮放設定，或傳送的其他較高的層級 PaaS 服務等服務布料的轉印圖樣。 有了這個記住，您可以查看 VM 縮放比例設定為支援 PaaS 基礎結構。 即 PaaS 解決方案等服務布料的轉印圖樣或叢集管理員等 Mesos 可以建立上方 VM 縮放設定為可調整計算圖層。

    以下是 Mesos 叢集的部署 VM 縮放比例設定為獨立 VM 相同 VNET 中的範例。 獨立 VM 是 Mesos 母片]，並 VM 縮放比例設定代表一組從屬節點︰ [https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json](https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json)。 未來版本的[Azure 容器服務](https://azure.microsoft.com/blog/azure-container-service-now-and-the-future/)會部署 VM 縮放比例設定為根據這種情況的多個複數/強化版本。

## <a name="vm-scale-set-performance-and-scale-guidance"></a>VM 縮放比例設定效能，並不按比例縮放的指引

- 期間公用預覽，不要建立多個 500 Vm 在多個 VM 縮放比例設定一次。
- 每個儲存帳戶不超過 40 Vm 計劃。
- 擴張盡可能儲存體帳戶名稱的第一個字母。  [Azure 快速入門範本](https://github.com/Azure/azure-quickstart-templates/)中的範例 VMSS 範本提供如何進行此動作的範例。
- 如果使用的自訂 Vm，規劃不超過 40 Vm 每 VM 縮放設定，在單一儲存帳戶。  您必須預先複製到儲存帳戶之前可以 VM 縮放比例設定部署的圖像。 如需詳細資訊的常見問題集，請參閱。
- 每個 VNET 不超過 2048 Vm 計劃。  這項限制會在未來增加。
- 您可以建立的 Vm 數受限於您任何區域中的計算核心配額。 您可能需要連絡客戶支援，以增加增加即使您為使用雲端服務或 IaaS v1 核心高限制今天您計算配額限制。 若要查詢您的配額中，您可以執行下列 Azure CLI 命令︰ _azure vm 清單使用方式_，與下列 PowerShell 命令︰_取得 AzureRmVMUsage_ （如果使用 PowerShell 下方 1.0 版使用_取得 AzureVMUsage_）。

## <a name="vm-scale-set-frequently-asked-questions"></a>VM 縮放比例設定常見問題集

**問:。** 您可以在 VM 縮放比例設定中有多少 Vm？

**答︰** 如果您使用的由多個儲存帳戶的平台影像，100。 如果您是使用自訂的圖像，最多 40，因為自訂圖像限於單一儲存帳戶期間預覽。

**問︰**其他資源限制存在 VM 縮放比例設定？

**答︰** 您所建立不超過 500 Vm 在多個小數位數組每個地區預覽期間。 現有[Azure 訂閱服務限制 /](../articles/azure-subscription-service-limits.md)套用。

**問:。** 位於支援的資料磁碟 VM 縮放比例設定？

**答︰** 不在初始的版本。 將資料儲存選項是︰

- Azure 檔案 （SMB 共用磁碟機）

- OS 磁碟機

- 暫存的磁碟機 （本機，不支援 Azure 儲存）

- 外部資料服務 （例如遠端 DB、 Azure 資料表、 Azure blob）

**問:。** 哪些 Azure 地區支援 VM 縮放比例設定？

**答︰** 支援 Azure 資源管理員的任何區域支援 VM 縮放比例設定。

**問:。** 如何建立 VM 縮放比例設定使用自訂的圖像？

**答︰** 留 vhdContainers 屬性，例如︰

    "storageProfile": {
        "osDisk": {
            "name": "vmssosdisk",
            "caching": "ReadOnly",
            "createOption": "FromImage",
            "image": {
                "uri": [https://mycustomimage.blob.core.windows.net/system/Microsoft.Compute/Images/mytemplates/template-osDisk.vhd](https://mycustomimage.blob.core.windows.net/system/Microsoft.Compute/Images/mytemplates/template-osDisk.vhd)
            },
            "osType": "Windows"
        }
    },


**問:。** 如果我減少我 VM 縮放比例設容量 20 15，將會移除 Vm 嗎？

**答︰** 虛擬機器移除了平均跨升級的網域和故障網域設定為最大化可用性小數位數。

**問:。** 如何將其如果然後提升 15 容量 18？

**答︰** 如果您增加 18，Vm 索引 15，16，將會建立 17。 在這兩種情況下 Vm 是由上 FDs 和 UDs 平衡。

**問:。** 使用多個副檔名中時 VM 縮放設定，我可以強制執行的執行順序？

**答︰** 不是直接，但 customScript 延伸模組，您的指令碼無法等待完成的另一個副檔名 (例如來監控副檔名為登入，請參閱[https://github.com/Azure/azure-quickstart-templates/blob/master/201-vmss-lapstack-autoscale/install\_lap.sh](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vmss-lapstack-autoscale/install_lap.sh))。

**問:。** VM 縮放比例設定要使用 Azure 可用性集？

**答︰** [是]。 VM 縮放比例設定為 3 FDs 與 5 UDs 設定隱含可用性。 您不需要設定 virtualMachineProfile 底下的任何項目。 在未來的版本類似，VM 縮放比例設定有可能橫跨多個租用戶但現在縮放比例設定是單一可用性設定。
