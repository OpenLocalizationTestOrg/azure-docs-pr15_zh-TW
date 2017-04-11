<properties
    pageTitle="執行網站復原 HYPER-V 容量規劃工具 |Microsoft Azure"
    description="本文包含使用 HYPER-V 容量規劃工具 Azure 網站復原的指示"
    services="site-recovery"
    documentationCenter="na"
    authors="rayne-wiselman"
    manager="jwhit"
    editor="" />
<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="07/12/2016"
    ms.author="raynew" />

# <a name="run-the-hyper-v-capacity-planner-tool-for-site-recovery"></a>執行網站復原 HYPER-V 容量規劃工具

Azure 網站復原部署的一部分，您會需要找出您複寫與頻寬需求。 網站復原 HYPER-V 容量規劃工具可協助您找出 HYPER-V 虛擬機器複寫您複寫與頻寬需求。


本文將說明如何執行 HYPER-V 容量規劃工具。 此工具來搭配使用的其他容量規劃工具和[網站復原的容量計劃](site-recovery-capacity-planner.md)中所述的資訊。


## <a name="before-you-start"></a>在您開始之前

您 HYPER-V 伺服器或叢集節點，在 [主要] 網站上執行工具。 若要執行這個工具 HYPER-V 主機伺服器需要︰

- 作業系統︰ Windows Server® 2012年或 Windows Server® 2012 R2
- 記憶體︰ 20 MB （最小值）
- CPU: 5 百分比開銷 （最小值）
- 磁碟空間︰ 5 MB （最小值）

執行工具之前必須準備主要的網站。 如果您複製之間兩個內部部署網站和您想要檢查的頻寬，必須先準備複本伺服器。


## <a name="step-1-prepare-the-primary-site"></a>步驟 1︰ 準備主要的網站
1. 在主要的網站進行所有您想要複寫 HYPER-V 虛擬機器與 HYPER-V 主機/叢集要在其它們是位於的清單。 此工具可以執行每次多個獨立主機] 或單一叢集，但不是能同時放在一起。 它也需要針對每個作業系統中，分別執行，因此您應該收集資料，並記下您 HYPER-V 伺服器，如下所示︰

  - Windows Server® 2012年獨立伺服器
  - Windows Server® 2012年叢集
  - Windows Server® 2012 R2 獨立伺服器
  - Windows Server® 2012 R2 叢集

3. 啟用遠端存取的所有 HYPER-V 主機和叢集 WMI。 執行此命令，在每個伺服器/叢集以確定防火牆規則，並設定使用者權限︰

        netsh firewall set service RemoteAdmin enable

5. 啟用效能監視伺服器及叢集，，如下所示︰

  - 使用 [**進階安全性**嵌入式管理單元中，開啟 [Windows 防火牆]，然後啟用 [輸入下列規則︰ **COM + 網路存取 （DCOM 在）**與**遠端的事件記錄檔管理] 群組**中的所有規則。

## <a name="step-2-prepare-a-replica-server-on-premises-to-on-premises-replication"></a>步驟 2︰ 準備複本伺服器 （內部部署至內部部署的試驗）

您不需要這麼做，如果您複製至 Azure。

我們建議您設定單一 HYPER-V 主機為復原伺服器以便 dummy VM 可以複製到該核取頻寬。  您可以跳過此，但您將無法測量的頻寬，除非您進行此動作。

1. 如果您想要使用叢集節點複本設定 HYPER-V 複本代理人︰

    - 在 [**伺服器管理員**] 中，開啟 [**容錯移轉叢集管理員**]。
    - 連線到叢集醒目提示叢集名稱，按一下 [**動作** > 來開啟 [高可用性精靈**設定的角色**。
    - 在 [**選取角色** **HYPER-V 複本代理人**。 在精靈中提供的**NetBIOS 名稱**及**IP 位址**當做連接點叢集 （稱為用戶端存取點）。 將會設定**HYPER-V 複本代理人**，結果，請注意用戶端存取點名稱。
    - 確認 HYPER-V 複本代理人角色上線成功可以容錯之間叢集的所有節點。 執行此動作，以滑鼠右鍵按一下該角色，指向**移動**，然後按一下 [**選取節點**。 選取 [節點 > **[確定]**。
    - 如果您使用憑證驗證，請確定每個叢集節點與用戶端存取所有已安裝的憑證的點。
2.  啟用複本伺服器︰

    - 叢集開啟失敗叢集管理員，連線到叢集，按一下 [**角色**> 選取角色 >**複寫設定**] >**啟用此叢集為複本伺服器**。 請注意，是否您使用的叢集複本作為您必須簡報的 HYPER-V 複本代理人角色叢集在主要網站上。
    - 獨立的伺服器開啟 HYPER-V 管理員。 在 [**動作**] 窗格中，按一下您想要啟用的伺服器**HYPER-V 設定**，**複寫組態**中按一下 [**啟用這台電腦為複本伺服器。**
3. 設定驗證︰

    - 在 [**認證及連接埠**選取驗證主要伺服器和驗證連接埠。 如果您使用的憑證按一下以選取其中一個**選取憑證**。 如果主要及復原 HYPER-V 主機位於相同的網域或受信任的網域，請使用 Kerberos]。 使用不同的網域或工作群組部署的憑證。
    - **授權與儲存**在區段中，允許**任何**已驗證 （主要） 伺服器複寫資料傳送到此複本伺服器。 按一下 [**確定**] 或 [**套用**]。

    ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image1.png)

    - 執行**netsh http 顯示 servicestate**檢查接聽正在執行您指定的通訊協定/連接埠︰  
4. 設定防火牆。 HYPER-V 安裝期間會建立防火牆規則，允許流量預設連接埠 （HTTPS 上 443、 Kerberos 80 上的）。 啟用這些規則如下所示︰

        - Certificate authentication on cluster (443): **Get-ClusterNode | ForEach-Object {Invoke-command -computername \$\_.name -scriptblock {Enable-Netfirewallrule -displayname "Hyper-V Replica HTTPS Listener (TCP-In)"}}**
        - Kerberos authentication on cluster (80): **Get-ClusterNode | ForEach-Object {Invoke-command -computername \$\_.name -scriptblock {Enable-Netfirewallrule -displayname "Hyper-V Replica HTTP Listener (TCP-In)"}}**
        - Certificate authentication on standalone server: **Enable-Netfirewallrule -displayname "Hyper-V Replica HTTPS Listener (TCP-In)"**
        - Kerberos authentication on standalone server: **Enable-Netfirewallrule -displayname "Hyper-V Replica HTTP Listener (TCP-In)"**

## <a name="step-3-run-the-capacity-planner-tool"></a>步驟 3︰ 執行容量規劃工具

當您準備好您主要的網站，並設定復原伺服器之後，您可以執行工具。

1. [下載](https://www.microsoft.com/download/details.aspx?id=39057)Microsoft 下載中心] 工具。
2. 從主要的伺服器 （或其中的主要叢集節點） 執行工具。 .Exe 檔案，以滑鼠右鍵按一下，然後選擇 [**以系統管理員身分執行**。
3. 在**您開始之前，**指定您想要收集資料的時間長度。 我們建議您以確保資料代表生產時間執行工具。 如果您只想要驗證的網路連線，您可以收集只有一分鐘。

    ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image2.png)

4. **主站台詳細資料**中指定伺服器名稱或 FQDN 獨立主機，或對叢集指定的用戶端 FQDN 接受點、 叢集名稱或在叢集任何節點，然後再按 [**下一步**。 工具會自動偵測的伺服器執行的名稱。 工具會 Vm 可以監視對指定伺服器。

    ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image3.png)

5. **複本網站**詳細資料如果您複製至 Azure，或如果您複製到第二個資料中心，複本伺服器未設定，選取 [**略過涉及複本網站的測試**。 如果您複製到第二個資料中心，而且您已設定 FQDN 複本輸入獨立的伺服器或**伺服器名稱 （或） HYPER-V 複本代理人首字放大**中叢集的用戶端存取點。

    ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image4.png)

6. **延伸複本詳細資料**中啟用**略過涉及延伸複本網站的測試**。 他們不支援網站復原。
7. **複寫的選擇 Vm**中工具連線到伺服器或叢集顯示 Vm 和主要伺服器上執行的磁碟] 的設定所指定在**主要網站詳細資料**] 頁面上。 請注意，不在執行的已啟用複寫或的 Vm 不會顯示。 選取您要收集計量的 Vm。 自動選取 Vhd 收集資料的 Vm 太。
9. 如果您已設定複本伺服器或叢集，在**網路資訊**指定 [您認為大約 WAN 頻寬適用於之間的主要及複本的網站，並選取憑證，如果您已設定憑證驗證]。

    ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image5.png)

10. 在**摘要**核取 [設定]，然後按 [**下一步**開始收集指標。 工具進度及狀態會顯示在 [**計算容量**頁面上。 受邀執行工具完成按一下到輸出上**檢視報告**]。 預設報表和記錄會儲存在**%systemdrive%\Users\Public\Documents\Capacity 規劃**。

    ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image6.png)


## <a name="step-4-interpret-the-results"></a>步驟 4︰ 解譯結果
以下是重要的指標。 您可以略過的未列於此處的指標。 他們不相關的網站修復。

### <a name="on-premises-to-on-premises-replication"></a>內部部署至內部部署的試驗
  - 在主要主機的計算，記憶體複寫的影響
  - 在主要，修復主機儲存空間的磁碟空間，IOPS 複寫的影響
  - 總差異複寫 (Mbps) 所需的頻寬
  - 主要主機和修復主機 (Mbps) 之間的觀察的網路頻寬
  - 建議的兩個主機/叢集之間的作用中平行傳輸理想的數目

### <a name="on-premises-to-azure-replication"></a>內部部署 Azure 複寫
  - 在主要主機的計算，記憶體複寫的影響
  - 在主要主機的儲存空間的磁碟空間，IOPS 複寫的影響
  - 總差異複寫 (Mbps) 所需的頻寬

## <a name="more-resources"></a>更多資源

- 如需 [閱讀上述工具下載的文件] 工具的詳細資訊。
- 觀看 Keith Mayer [TechNet 部落格](http://blogs.technet.com/b/keithmayer/archive/2014/02/27/guided-hands-on-lab-capacity-planner-for-windows-server-2012-hyper-v-replica.aspx)工具的逐步解說。
- [取得結果](site-recovery-performance-and-scaling-testing-on-premises-to-on-premises.md)的內部部署的內部部署 HYPER-V 複寫測試我們效能



## <a name="next-steps"></a>後續步驟

容量規劃完成後，您可以開始部署網站修復︰

- [Azure 複寫 HYPER-V Vm VMM 雲朵中](site-recovery-vmm-to-azure.md)
- [Azure 複寫 HYPER-V Vm （不含 VMM)](site-recovery-hyper-v-site-to-azure.md)
- [複製 HYPER-V Vm 之間 VMM 網站](site-recovery-vmm-to-vmm.md)
- [使用舊的 VMM 網站之間複製 HYPER-V Vm](site-recovery-vmm-san.md)
- [複製 HYPER-V Vm 單一 VMM 伺服器上](site-recovery-single-vmm.md)