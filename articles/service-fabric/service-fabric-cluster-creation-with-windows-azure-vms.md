<properties
   pageTitle="執行 Windows Azure Vm 以建立獨立叢集 |Microsoft Azure"
   description="瞭解如何建立及管理在執行 Windows Server Azure 虛擬機器上 Azure 服務布料的轉印圖樣叢集。"
   services="service-fabric"
   documentationCenter=".net"
   authors="dsk-2015"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/05/2016"
   ms.author="dkshir;chackdan"/>



# <a name="create-a-three-node-standalone-service-fabric-cluster-with-azure-virtual-machines-running-windows-server"></a>執行 Windows Server Azure 虛擬機器以建立三個節點獨立版服務布料的轉印圖樣叢集

本文說明如何建立使用適用於 Windows Server 獨立版服務布料的轉印圖樣安裝程式在 windows Azure 虛擬機器 (Vm)，叢集。 這是一種特殊情況[建立及管理 Windows 伺服器上執行叢集](service-fabric-cluster-creation-for-windows-server.md)Vm 在哪裡[執行 Windows Server Azure Vm](../virtual-machines/virtual-machines-windows-hero-tutorial.md)，但是您不需要建立[Azure 雲端服務布料的轉印圖樣叢集](service-fabric-cluster-creation-via-portal.md)。 不同的是由下列步驟所建立的獨立版服務布料的轉印圖樣叢集完全由您、 時 Azure 雲端服務布料的轉印圖樣叢集管理及升級服務布料的轉印圖樣資源提供者。


## <a name="steps-to-create-the-standalone-cluster"></a>建立獨立叢集的步驟

1. 登入 Azure 入口網站，然後在 [資源] 群組中建立新 Windows Server 2012 R2 資料中心 VM。 閱讀文件[建立 Windows VM Azure 入口網站中](../virtual-machines/virtual-machines-windows-hero-tutorial.md)的 [更多詳細資料。
2. 將相同的 [資源] 群組中的幾個更多在 Windows Server 2012 R2 資料中心 Vm。 確定每個 Vm 具有相同的系統管理員使用者名稱和密碼時所建立。 您應該會看到相同的虛擬網路中的所有三個 Vm 建立一次。
3. 連線至每個 Vm，關閉 Windows 防火牆使用[伺服器管理員、 本機伺服器儀表板](https://technet.microsoft.com/library/jj134147.aspx)。 如此一來，可確保網路流量能以無聲的電腦之間。 連線至每一部電腦，取得 IP 位址，請開啟命令提示字元中，輸入`ipconfig`。 或者，您可以選取 [資源] 群組的虛擬網路資源 Azure 入口網站中看到每一部電腦的 IP 位址。
4. 連線到其中一個 Vm 和測試可以順利 ping 其他兩個 Vm。
5. 到新的資料夾，在電腦上連線到其中一個 Vm 和[下載 Windows Server 的獨立版服務布料的轉印圖樣套件](http://go.microsoft.com/fwlink/?LinkId=730690)與解壓縮套件。
6. 開啟*ClusterConfig.Unsecure.MultiMachine.json*檔案在 「 記事本 」，並編輯每個節點的電腦的三個 IP 位址。 變更叢集名稱上方，然後儲存檔案。  叢集資訊清單的部分範例如下所示。

    ```
    {
        "name": "TestCluster",
        "clusterManifestVersion": "1.0.0",
        "apiVersion": "2015-01-01-alpha",
        "nodes": [
        {
            "nodeName": "vm0",
            "metadata": "Replace the localhost with valid IP address or FQDN below",
            "iPAddress": "10.7.0.5",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD0"
        },
        {
            "nodeName": "vm1",
            "metadata": "Replace the localhost with valid IP address or FQDN below",
            "iPAddress": "10.7.0.4",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc2/r0",
            "upgradeDomain": "UD1"
        },
        {
            "nodeName": "vm2",
            "metadata": "Replace the localhost with valid IP address or FQDN below",
            "iPAddress": "10.7.0.6",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc3/r0",
            "upgradeDomain": "UD2"
        }
    ],
    ```

7. 開啟[PowerShell ise [以系統] 視窗](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise)。 瀏覽至資料夾建構下載的獨立安裝程式套件及儲存叢集資訊清單檔案的位置。 執行下列動作的 PowerShell 命令。

    ```
    .\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab
    ```

8. 您應該會看到 PowerShell 執行、 連線至每一部電腦，以及建立叢集。 大約一分鐘，您可以檢查叢集是否作業，例如連接至電腦的 IP 位址其中一項服務布料的轉印圖樣總管使用後`http://10.7.0.5:19080/Explorer/index.html`。 因為這是獨立叢集，使其更安全，您會有部署[Azure Vm 憑證](service-fabric-windows-cluster-x509-security.md)或設定為[Windows 驗證的 Windows Server Active Directory (AD) 控制器](service-fabric-windows-cluster-windows-security.md)，就像您想要執行內部部署的其中一個機器使用 Azure Vm。


## <a name="next-steps"></a>後續步驟
- [建立獨立版服務布料的轉印圖樣叢集 Windows Server 或 Linux](service-fabric-deploy-anywhere.md)
- [新增或移除獨立版服務布料的轉印圖樣叢集節點](service-fabric-cluster-windows-server-add-remove-nodes.md)
- [獨立 Windows 叢集組態設定](service-fabric-cluster-manifest.md)
- [安全獨立叢集使用 Windows 安全性的 Windows 上](service-fabric-windows-cluster-windows-security.md)
- [安全獨立叢集上 Windows 使用 X509 憑證](service-fabric-windows-cluster-x509-security.md)
