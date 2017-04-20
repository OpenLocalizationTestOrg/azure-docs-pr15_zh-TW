<properties
   pageTitle="在 Windows 虛擬機器 (Vm) – 規劃與實作指南 SAP NetWeaver |Microsoft Azure"
   description="在 Windows 虛擬機器 (Vm) – 規劃與實作指南 SAP NetWeaver"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="MSSedusch"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"
   keywords=""/>
<tags
   ms.service="virtual-machines-windows"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="08/18/2016"
   ms.author="sedusch"/>

# <a name="sap-netweaver-on-windows-virtual-machines-vms--planning-and-implementation-guide"></a>在 Windows 虛擬機器 (Vm) – 規劃與實作指南 SAP NetWeaver

[767598]:https://service.sap.com/sap/support/notes/767598
[773830]:https://service.sap.com/sap/support/notes/773830
[826037]:https://service.sap.com/sap/support/notes/826037
[965908]:https://service.sap.com/sap/support/notes/965908
[1031096]:https://service.sap.com/sap/support/notes/1031096
[1139904]:https://service.sap.com/sap/support/notes/1139904
[1173395]:https://service.sap.com/sap/support/notes/1173395
[1245200]:https://service.sap.com/sap/support/notes/1245200
[1409604]:https://service.sap.com/sap/support/notes/1409604
[1558958]:https://service.sap.com/sap/support/notes/1558958
[1585981]:https://service.sap.com/sap/support/notes/1585981
[1588316]:https://service.sap.com/sap/support/notes/1588316
[1590719]:https://service.sap.com/sap/support/notes/1590719
[1597355]:https://service.sap.com/sap/support/notes/1597355
[1605680]:https://service.sap.com/sap/support/notes/1605680
[1619720]:https://service.sap.com/sap/support/notes/1619720
[1619726]:https://service.sap.com/sap/support/notes/1619726
[1619967]:https://service.sap.com/sap/support/notes/1619967
[1750510]:https://service.sap.com/sap/support/notes/1750510
[1752266]:https://service.sap.com/sap/support/notes/1752266
[1757924]:https://service.sap.com/sap/support/notes/1757924
[1757928]:https://service.sap.com/sap/support/notes/1757928
[1758182]:https://service.sap.com/sap/support/notes/1758182
[1758496]:https://service.sap.com/sap/support/notes/1758496
[1772688]:https://service.sap.com/sap/support/notes/1772688
[1814258]:https://service.sap.com/sap/support/notes/1814258
[1882376]:https://service.sap.com/sap/support/notes/1882376
[1909114]:https://service.sap.com/sap/support/notes/1909114
[1922555]:https://service.sap.com/sap/support/notes/1922555
[1928533]:https://service.sap.com/sap/support/notes/1928533
[1941500]:https://service.sap.com/sap/support/notes/1941500
[1956005]:https://service.sap.com/sap/support/notes/1956005
[1973241]:https://service.sap.com/sap/support/notes/1973241
[1984787]:https://service.sap.com/sap/support/notes/1984787
[1999351]:https://service.sap.com/sap/support/notes/1999351
[2002167]:https://service.sap.com/sap/support/notes/2002167
[2015553]:https://service.sap.com/sap/support/notes/2015553
[2039619]:https://service.sap.com/sap/support/notes/2039619
[2121797]:https://service.sap.com/sap/support/notes/2121797
[2134316]:https://service.sap.com/sap/support/notes/2134316
[2178632]:https://service.sap.com/sap/support/notes/2178632
[2191498]:https://service.sap.com/sap/support/notes/2191498
[2233094]:https://service.sap.com/sap/support/notes/2233094
[2243692]:https://service.sap.com/sap/support/notes/2243692

[azure-cli]:../xplat-cli-install.md
[azure-portal]:https://portal.azure.com
[azure-ps]:../powershell-install-configure.md
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-subscription-service-limits]:../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../azure-subscription-service-limits.md#subscription

[dbms-guide]:virtual-machines-windows-sap-dbms-guide.md (在 Windows 虛擬機器 (Vm) – DBMS 部署指南 SAP NetWeaver) [dbms-guide-2.1]:virtual-machines-windows-sap-dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f （快取 Vm 和 Vhd） [dbms-guide-2.2]:virtual-machines-windows-sap-dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 (軟體 RAID) [dbms-guide-2.3]:virtual-machines-windows-sap-dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 （Microsoft Azure 儲存體） [dbms-guide-2]:virtual-machines-windows-sap-dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 （結構 RDBMS 部署的） [dbms-guide-3]:virtual-machines-windows-sap-dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 （可用性和 Azure Vm 與損毀修復） [dbms-guide-5.5.1]:virtual-machines-windows-sap-dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 (SQL Server 2012 SP1 CU4 及更新版本) [dbms-guide-5.5.2]:virtual-machines-windows-sap-dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b （SQL Server 2012 SP1 CU3 及舊版） [dbms-guide-5.6]:virtual-machines-windows-sap-dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 （不在 Microsoft Azure Marketplace 使用 SQL Server 圖像） [dbms-guide-5.8]:virtual-machines-windows-sap-dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 (一般的 SAP Azure 摘要上的 SQL Server) [dbms-guide-5]:virtual-machines-windows-sap-dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 （SQL Server RDBMS 細節） [dbms-guide-8.4.1]:virtual-machines-windows-sap-dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 （儲存設定） [dbms-guide-8.4.2]:virtual-machines-windows-sap-dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d （備份和還原） [dbms-guide-8.4.3]:virtual-machines-windows-sap-dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c （效能考量備份與還原） [dbms-guide-8.4.4]:virtual-machines-windows-sap-dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 （其他） [dbms-guide-900-sap-cache-server-on-premises]:virtual-machines-windows-sap-dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8

[dbms-guide-figure-100]:./media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]:./media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]:./media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]:./media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]:./media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]:./media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]:./media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]:./media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]:./media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]:virtual-machines-windows-sap-deployment-guide.md (在 Windows 虛擬機器 (Vm) – 部署指南 SAP NetWeaver) [deployment-guide-2.2]:virtual-machines-windows-sap-deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 （SAP 資源） [deployment-guide-3.1.2]:virtual-machines-windows-sap-deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab （部署自訂圖像 VM） [deployment-guide-3.2]:virtual-machines-windows-sap-deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 (案例 1︰ 部署登出 SAP Azure 服務商場 VM) [deployment-guide-3.3]:virtual-machines-windows-sap-deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 (案例 2︰ 為 SAP 部署自訂圖像 VM) [deployment-guide-3.4]:virtual-machines-windows-sap-deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 (案例 3︰ 從內部部署非一般化 Azure VHD 使用 SAP 移動 VM) [deployment-guide-3]:virtual-machines-windows-sap-deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e (部署案例的 Vm 的 Microsoft Azure 上 SAP) [deployment-guide-4.1]:virtual-machines-windows-sap-deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 (部署 Azure PowerShell cmdlet) [deployment-guide-4.2]:virtual-machines-windows-sap-deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e (下載並匯入 SAP 相關 PowerShell cmdlet) [deployment-guide-4.3]:virtual-machines-windows-sap-deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc (加入 VM 將內部部署網域-僅在 Windows) [deployment-guide-4.4.2]:virtual-machines-windows-sap-deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 (Linux) [deployment-guide-4.4]:virtual-machines-windows-sap-deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d （下載、 安裝並啟用 Azure VM 代理程式） [deployment-guide-4.5.1]:virtual-machines-windows-sap-deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 (PowerShell 的 Azure) [deployment-guide-4.5.2]:virtual-machines-windows-sap-deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f (Azure CLI) [deployment-guide-4.5]:virtual-machines-windows-sap-deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca （設定 Azure 增強型監控副檔名為 SAP） [deployment-guide-5.1]:virtual-machines-windows-sap-deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 （整備檢查 Azure 增強型監視 SAP） [部署-快速入門-5.2]:virtual-machines-windows-sap-deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 （Azure 監控基礎結構設定的狀況檢查） [deployment-guide-5.3]:virtual-machines-windows-sap-deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 （進一步疑難排解 Azure 監控基礎結構的 SAP）

[deployment-guide-configure-monitoring-scenario-1]:virtual-machines-windows-sap-deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b (Configure Monitoring)
[deployment-guide-configure-proxy]:virtual-machines-windows-sap-deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d (Configure Proxy)
[deployment-guide-figure-100]:./media/virtual-machines-shared-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-1000]:./media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-11]:virtual-machines-windows-sap-deployment-guide.md#figure-11
[deployment-guide-figure-1100]:./media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]:./media/virtual-machines-shared-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]:./media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-14]:virtual-machines-windows-sap-deployment-guide.md#figure-14
[deployment-guide-figure-1400]:./media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-300]:./media/virtual-machines-shared-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]:./media/virtual-machines-shared-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-5]:virtual-machines-windows-sap-deployment-guide.md#figure-5
[deployment-guide-figure-50]:./media/virtual-machines-shared-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-500]:./media/virtual-machines-shared-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-6]:virtual-machines-windows-sap-deployment-guide.md#figure-6
[deployment-guide-figure-600]:./media/virtual-machines-shared-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-7]:virtual-machines-windows-sap-deployment-guide.md#figure-7
[deployment-guide-figure-700]:./media/virtual-machines-shared-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]:./media/virtual-machines-shared-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-900]:./media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-azure-cli-installed]:virtual-machines-windows-sap-deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]:virtual-machines-windows-sap-deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda
[deployment-guide-install-vm-agent-windows]:virtual-machines-windows-sap-deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-troubleshooting-chapter]:virtual-machines-windows-sap-deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b (Checks and Troubleshooting for End-to-End Monitoring Setup for SAP on Azure)

[deploy-template-cli]:../resource-group-template-deploy.md#deploy-with-azure-cli-for-mac-linux-and-windows
[deploy-template-portal]:../resource-group-template-deploy.md#deploy-with-the-preview-portal
[deploy-template-powershell]:../resource-group-template-deploy.md#deploy-with-powershell

[dr-guide-classic]:http://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:virtual-machines-windows-sap-get-started.md
[getting-started-dbms]:virtual-machines-windows-sap-get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]:virtual-machines-windows-sap-get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]:virtual-machines-windows-sap-get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]:virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]:virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]:virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]:virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]:virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]:virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]:http://go.microsoft.com/fwlink/?LinkId=613056

[ha-guide]:virtual-machines-windows-sap-high-availability-guide.md

[install-extension-cli]:virtual-machines-linux-enable-aem.md

[Logo_Linux]:./media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:./media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-azurermvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

[planning-guide]:virtual-machines-windows-sap-planning-guide.md (Windows 虛擬機器上 (Vm) – 規劃與實作指南 SAP NetWeaver) [planning-guide-1.2]:virtual-machines-windows-sap-planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff （資源） [planning-guide-11.4]:virtual-machines-windows-sap-planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 （高可用性 (HA) 和損毀復原 (DR) 的 SAP NetWeaver 執行上 Azure 虛擬機器） [planning-guide-11.4.1]:virtual-machines-windows-sap-planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 （可用性 SAP 應用程式伺服器） [planning-guide-11.5]:virtual-machines-windows-sap-planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f （使用自動啟動之 SAP 執行個體） [planning-guide-2.1]:virtual-machines-windows-sap-planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 (雲端專用-不在內部部署的客戶網路上的相依性的虛擬機器部署到 Azure) [planning-guide-2.2]:virtual-machines-windows-sap-planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 （交互內部部署-部署單一或多個 SAP Vm 到的正完全整合內部部署網路需求與 Azure） [planning-guide-3.1]:virtual-machines-windows-sap-planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a （Azure 區域） [planning-guide-3.2.1]:virtual-machines-windows-sap-planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 （故障網域） [planning-guide-3.2.2]:virtual-machines-windows-sap-planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 （升級網域） [planning-guide-3.2.3]:virtual-machines-windows-sap-planning-guide.md#18810088-f9be-4 c 97-958a-27996255 c 665 （Azure 可用性組） [planning-guide-3.2]:virtual-machines-windows-sap-planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 （Microsoft Azure 虛擬機器概念） [planning-guide-3.3.2]:virtual-machines-windows-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 （Azure 進階版儲存區） [（移動 VM 來自內部部署至 Azure 以非一般化光碟） planning-guide-5.1.1]:virtual-machines-windows-sap-planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 [planning-guide-5.1.2]:virtual-machines-windows-sap-planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c （部署客戶特定圖像 VM） [planning-guide-5.2.1]:virtual-machines-windows-sap-planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef (準備將來自內部部署的 VM 移至與 Azure非一般化磁碟） [planning-guide-5.2.2]:virtual-machines-windows-sap-planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 （準備的 SAP 部署客戶特定圖像 VM） [planning-guide-5.2]:virtual-machines-windows-sap-planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 (準備和 Azure 的 SAP Vm) [planning-guide-5.3.1]:virtual-machines-windows-sap-planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 （Azure 磁碟之間的差異和 Azure 圖像） [planning-guide-5.3.2]:virtual-machines-windows-sap-planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a （上傳至 Azure VHD 來自內部部署） [planning-guide-5.4.2]:virtual-machines-windows-sap-planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 （Azure 儲存體帳戶之間複製磁碟） [計劃-快速入門-5.5.1]:virtual-machines-windows-sap-planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 （VM/VHD 結構 SAP 部署） [planning-guide-5.5.3]:virtual-machines-windows-sap-planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d （設定自動附加磁碟掛接） [planning-guide-7.1]:virtual-machines-windows-sap-planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30 (與 SAP NetWeaver 示範訓練案例的單一 VM) [planning-guide-7]:virtual-machines-windows-sap-planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14 （SAP 執行個體的概念的 Cloud-Only 部署） [planning-guide-9.1]:virtual-machines-windows-sap-planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 （Azure 監控解決方案 SAP） [planning-guide-azure-premium-storage]:virtual-machines-windows-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 （Azure 進階版儲存區）

[planning-guide-figure-100]:./media/virtual-machines-shared-sap-planning-guide/100-single-vm-in-azure.png
[planning-guide-figure-1300]:./media/virtual-machines-shared-sap-planning-guide/1300-ref-config-iaas-for-sap.png
[planning-guide-figure-1400]:./media/virtual-machines-shared-sap-planning-guide/1400-attach-detach-disks.png
[planning-guide-figure-1600]:./media/virtual-machines-shared-sap-planning-guide/1600-firewall-port-rule.png
[planning-guide-figure-1700]:./media/virtual-machines-shared-sap-planning-guide/1700-single-vm-demo.png
[planning-guide-figure-1900]:./media/virtual-machines-shared-sap-planning-guide/1900-vm-set-vnet.png
[planning-guide-figure-200]:./media/virtual-machines-shared-sap-planning-guide/200-multiple-vms-in-azure.png
[planning-guide-figure-2100]:./media/virtual-machines-shared-sap-planning-guide/2100-s2s.png
[planning-guide-figure-2200]:./media/virtual-machines-shared-sap-planning-guide/2200-network-printing.png
[planning-guide-figure-2300]:./media/virtual-machines-shared-sap-planning-guide/2300-sapgui-stms.png
[planning-guide-figure-2400]:./media/virtual-machines-shared-sap-planning-guide/2400-vm-extension-overview.png
[planning-guide-figure-2500]:./media/virtual-machines-shared-sap-planning-guide/2500-vm-extension-details.png
[planning-guide-figure-2600]:./media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:./media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:./media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:./media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-300]:./media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:./media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:./media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-400]:./media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:./media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:./media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:./media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:virtual-machines-windows-sap-planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd (Microsoft Azure Networking)
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:virtual-machines-windows-sap-planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f (Storage: Microsoft Azure Storage and Data Disks)

[powershell-install-configure]:../powershell-install-configure.md
[resource-group-authoring-templates]:../resource-group-authoring-templates.md
[resource-group-overview]:../resource-group-overview.md
[resource-groups-networking]:../virtual-network/resource-groups-networking.md
[sap-pam]:https://support.sap.com/pam (SAP Product Availability Matrix)
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[storage-azure-cli]:../storage/storage-azure-cli.md
[storage-azure-cli-copy-blobs]:../storage/storage-azure-cli.md#copy-blobs
[storage-introduction]:../storage/storage-introduction.md
[storage-powershell-guide-full-copy-vhd]:../storage/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]:../storage/storage-premium-storage.md
[storage-redundancy]:../storage/storage-redundancy.md
[storage-scalability-targets]:../storage/storage-scalability-targets.md
[storage-use-azcopy]:../storage/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:virtual-machines-linux-attach-disk-portal.md
[virtual-machines-windows-attach-disk-portal]:virtual-machines-windows-attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../resource-manager-deployment-model.md
[virtual-machines-azurerm-versus-azuresm]:virtual-machines-windows-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:virtual-machines-linux-cli-deploy-templates.md (Deploy and manage virtual machines by using Azure Resource Manager templates and the Azure CLI)
[virtual-machines-deploy-rmtemplates-powershell]:virtual-machines-windows-ps-manage.md (Manage virtual machines using Azure Resource Manager and PowerShell)
[virtual-machines-linux-agent-user-guide]:virtual-machines-linux-agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]:virtual-machines-linux-agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]:virtual-machines-linux-capture-image.md
[virtual-machines-linux-capture-image-capture]:virtual-machines-linux-capture-image.md#capture-the-vm
[virtual-machines-windows-capture-image]:virtual-machines-windows-create-vm-generalized.md
[virtual-machines-windows-capture-image-prepare-the-vm-for-image-capture]:virtual-machines-windows-create-vm-generalized.md
[virtual-machines-linux-configure-lvm]:virtual-machines-linux-configure-lvm.md
[virtual-machines-linux-configure-raid]:virtual-machines-linux-configure-raid.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:virtual-machines-linux-suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:virtual-machines-linux-redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:virtual-machines-linux-add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:virtual-machines-linux-add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:virtual-machines-linux-quick-create-cli.md
[virtual-machines-linux-update-agent]:virtual-machines-linux-update-agent.md
[virtual-machines-manage-availability]:virtual-machines-windows-manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:virtual-machines-windows-ps-create.md
[virtual-machines-sizes]:virtual-machines-windows-sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:virtual-machines-windows-sql-high-availability-dr.md
[virtual-machines-sql-server-infrastructure-services]:virtual-machines-windows-sql-server-iaas-overview.md
[virtual-machines-sql-server-performance-best-practices]:virtual-machines-windows-sql-performance.md
[virtual-machines-upload-image-windows-resource-manager]:virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]:virtual-machines-windows-hero-tutorial.md
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/documentation/templates/sql-server-2014-alwayson-dsc/
[virtual-network-deploy-multinic-arm-cli]:../virtual-network/virtual-network-deploy-multinic-arm-cli.md
[virtual-network-deploy-multinic-arm-ps]:../virtual-network/virtual-network-deploy-multinic-arm-ps.md
[virtual-network-deploy-multinic-arm-template]:../virtual-network/virtual-network-deploy-multinic-arm-template.md
[virtual-networks-configure-vnet-to-vnet-connection]:../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../virtual-network/virtual-networks-create-vnet-arm-pportal.md
[virtual-networks-manage-dns-in-vnet]:../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics]:../virtual-network/virtual-networks-multiple-nics.md
[virtual-networks-nsg]:../virtual-network/virtual-networks-nsg.md
[virtual-networks-reserved-private-ip]:../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-networks-static-private-ip-arm-pportal]:../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-udr-overview]:../virtual-network/virtual-networks-udr-overview.md
[vpn-gateway-about-vpn-devices]:../vpn-gateway/vpn-gateway-about-vpn-devices.md
[vpn-gateway-create-site-to-site-rm-powershell]:../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]:../vpn-gateway/vpn-gateway-plan-design.md
[vpn-gateway-site-to-site-create]:../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md
[vpn-gateway-vpn-faq]:../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../xplat-cli-install.md
[xplat-cli-azure-resource-manager]:../xplat-cli-azure-resource-manager.md

Microsoft Azure 可讓最小的時間，而不冗長採購循環取得計算和儲存資源的公司。 Azure 虛擬機器讓公司等 SAP NetWeaver 基礎 Azure 應用程式部署古典應用程式，並將其可靠性和可用性延伸而不需要進一步的資源可用內部部署。 Azure 虛擬機器服務也支援跨內部部署連線，讓公司積極整合到他們的私人雲朵和其 SAP 系統橫向其內部部署網域的 [Azure 虛擬機器。
這本白皮書描述基本概念的 Microsoft Azure 虛擬機器提供規劃與實作考量 SAP NetWeaver 安裝 Azure 中的逐步解說，並為應該要 Azure 上啟動實際的部署的 SAP NetWeaver 之前，請先閱讀的文件。
紙張補充 SAP 安裝文件和 SAP 備忘稿表示安裝及部署的 SAP 軟體的主要資源上指定的平台。

[AZURE.INCLUDE [windows-warning](../../includes/virtual-machines-linux-sap-warning.md)]

## <a name="summary"></a>摘要
雲端運算常用的字詞的最大且跨國公司的小型公司的 IT 產業內的多重要性取得。

Microsoft Azure 是雲端服務的平台向 Microsoft 提供的新的可能性。 現在客戶就能以在雲端，服務快速佈建和取消提供的應用程式，因此不會限制為技術或預算限制。 而非投入時間和預算硬體基礎結構，公司可以著重在應用程式、 商務程序與利益客戶和使用者。

從 Microsoft Azure 虛擬機器服務，Microsoft 會提供全面涵蓋所有內容的基礎結構服務 (IaaS) 平台。 SAP NetWeaver 基礎應用程式支援上 Azure 虛擬機器 (IaaS)。 本白皮書會說明如何規劃及實作為選用的平台的 Microsoft Azure 中的基礎 SAP NetWeaver 應用程式。

紙張本身將著重在兩個主要特性︰

* 第一個部分會說明 Azure 基礎 SAP NetWeaver 應用程式的兩個支援的部署模式。 它也會說明一般處理的 Azure 記住 SAP 部署。
* 第二部分將詳細說明實作第一組件中所述的兩種不同情況。

其他資源，請參閱這份文件中的 [資源] [計劃-快速入門-1.2] 一章。

### <a name="definitions-upfront"></a>旅途定義
在文件中，我們會使用下列各項︰

* 以服務 IaaS︰ 基礎結構。
* 以服務 PaaS︰ 平台。
* SaaS︰ 軟體的服務。
* ARM: Azure 資源管理員
* SAP 元件︰ 個別 SAP 應用程式，如 ECC、 BW、 方案管理員或 EP.  SAP 元件可以根據傳統 ABAP 或 Java 技術或非 NetWeaver 基礎應用程式，例如商務物件。
* SAP 環境︰ 執行某項業務功能開發、 QAS、 訓練、 DR 或生產等邏輯組成群組的一或多個 SAP 元件。
* SAP 橫向︰ 這是指客戶的整個 SAP 資產 IT 橫向。 SAP 橫向包含所有生產和非生產環境。
* SAP 系統︰ DBMS 圖層和應用程式層級，例如 SAP ERP 開發系統、 SAP BW 測試系統、 SAP CRM 生產系統、 等的組合... Azure 部署中不支援將內部部署和 Azure 之間兩種層級。 SAP 系統可能會這表示部署內部部署或部署 Azure 中。 不過，您可以部署至 Azure 或內部部署 SAP 橫向的不同系統。 例如，您可以部署 SAP CRM 開發，測試系統 Azure 但 SAP CRM 生產系統內部部署。
* 僅供雲端部署︰ Azure 訂閱未連線至網站或內部網路基礎結構的 ExpressRoute 連線透過部署。 共同 Azure 文件這類的部署，也會以 「 雲端專用 ' 部署。 虛擬機器部署使用此方法是透過網際網路與公用 ip 位址和/或公用 DNS 名稱指派給 Azure 中 Vm 存取。 Microsoft windows 內部部署 Active Directory (AD) 和 DNS 不延伸至 Azure 中的部署這些類型。 因此 Vm 不是內部部署的 Active Directory 的一部分。 也是一樣的使用，例如 OpenLDAP + Kerberos Linux 實作。

> [AZURE.NOTE] 在這份文件中的雲端專用部署定義為以獨佔模式中 Azure Active Directory 的副檔名不執行完整 SAP 環境 / OpenLDAP 或從內部部署到公用雲端名稱解析。 雲端專用的設定不支援生產 SAP 系統或設定 SAP STMS 或其他內部部署資源需要以裝載於 Azure 和位於內部部署資源的 SAP 系統之間的位置。

* 跨內部部署︰ 描述的案例 Vm 部署 Azure 訂閱具有網站的網站、 多網站或內部部署 datacenter(s) 與 Azure ExpressRoute 連線的位置。 Azure 中的共有的文件，這些類型的部署，也會以跨內部部署案例。 原因延伸內部部署網域可連線的內部部署 Active Directory / OpenLDAP 和內部部署的 DNS 到 Azure。 內部部署橫向延伸至 Azure 資產的訂閱。 有至此分機]，Vm 可在內部部署網域。 內部部署網域的網域使用者可以存取伺服器，並可在這些 Vm （例如 DBMS 服務） 上執行服務。 部署 Vm 內部部署和 Azure 部署的 Vm 之間的通訊和名稱解析有可能是。 這是我們預期大部分的 SAP 資產部署在此案例。  請參閱[此][vpn-gateway-cross-premises-options]文件，以及[此][vpn-gateway-site-to-site-create]如需詳細資訊。

> [AZURE.NOTE] 生產 SAP 系統支援跨內部部署的 SAP 系統執行 SAP 系統 Azure 虛擬機器哪裡內部部署網域的成員。 跨內部部署設定的部署部分支援，或將 Azure 完成 SAP 環境。 即使執行 Azure 中的 [完成 SAP 橫向需要有這些 Vm 的一部分內部部署的網域與廣告 / OpenLDAP。 在文件的先前版本我們討論混合式 IT 案例、 字詞 '混合式' 根目錄中是內部部署與 Azure 跨內部部署連線的位置。 加上，在 Azure Vm 是內部部署的 Active Directory 的部分 / OpenLDAP。

某些 Microsoft 文件描述跨內部部署案例稍有不同，尤其是 DBMS HA 設定。 如果 SAP 相關的文件，只是此跨內部部署案例能夠至網站或私人 (ExpressRoute) 連線能力和 SAP 橫向散佈內部部署和 Azure 之間的事實。  

### <a name="e55d1e22-c2c8-460b-9897-64622a34fdff"></a>資源
下列額外的輔助線可在 Azure SAP 部署的主題︰

* [SAP NetWeaver Windows 虛擬機器上 (Vm) – 規劃與實作指南 （此文件）][規劃指南]
* [Windows 虛擬機器 (Vm) – 部署指南上 SAP NetWeaver][部署指南]
* [Windows 虛擬機器 (Vm) – DBMS 部署指南上 SAP NetWeaver][dbms 輔助線]
* [在 Windows 虛擬機器 (Vm) – 可用性部署指南 SAP NetWeaver][ha-guide]

> [AZURE.IMPORTANT] 參照的 SAP 安裝指南的連結可能會使用任何 (參考 InstGuide-01，請參閱<http://service.sap.com/instguides>)。 時的必要條件和安裝程序，SAP NetWeaver 安裝指南應該一律會仔細閱讀，為此文件僅涵蓋 SAP NetWeaver 系統安裝 Microsoft Azure 虛擬機器中的特定工作。

下列 SAP 備忘稿相關主題的 SAP Azure 上︰

| 記事編號 | 標題 |
|--------------|-------|
| [1928533] | SAP 應用程式上 Azure︰ 支援產品和縮放 |
| [2015553] | 在 Microsoft Azure SAP︰ 支援的先決條件 |
| [1999351] | 疑難排解增強型 Azure 監視 SAP |
| [2178632] | 在 Microsoft Azure SAP 監視指標的鍵 |
| [1409604] | 在 Windows 上的虛擬化︰ 增強型監控 |
| [2191498] | SAP linux 與 Azure︰ 增強型監控
| [2243692] | 在 Microsoft Azure (IaaS) VM Linux: SAP 授權問題
| [1984787] | SUSE LINUX Enterprise Server 12︰ 安裝備忘稿
| [2002167] | 紅色的角色企業 Linux 7.x︰ 安裝及升級

請閱讀也包含所有的 SAP 筆記的 Linux [SCN Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) 。

一般的預設限制和 Azure 訂閱的最大限制，請參閱[本文][azure-subscription-service-limits-subscription] 

## <a name="possible-scenarios"></a>可能的案例
SAP 視為一個企業中最重要的應用程式。 架構及這些應用程式的作業大多很複雜，務必確定電腦符合需求可用性和效能。

因此企業必須謹慎哪些應用程式可以執行公用雲端環境，獨立於所選的雲端提供者的想法。

可能的系統類型部署 SAP NetWeaver 基礎內公用雲端，如下所示的環境的應用程式︰

1. 中度的尺寸生產系統
1. 開發系統
1. 測試系統
1. 原型系統
1. 學習 / 示範系統

才能成功部署至 Azure IaaS 或 IaaS 的 SAP 系統一般，請務必瞭解嚴重和之間的差異傳統外包商或主機服務提供者的售價 IaaS 方案。 傳統主或委外廠商來調整客戶想要主控的工作負載的基礎結構 （網路、 儲存及伺服器類型），而改為是選擇正確的工作量 IaaS 部署客戶的責任。

第一個步驟是，客戶必須確認下列項目︰

* SAP 支援 Azure VM 類型
* SAP 支援 Azure 產品/版本
* 支援的作業系統與 DBMS 釋放 Azure 中的特定 SAP 版本更新
* 不同 Azure sku 皆可提供 SAP 處理量

SAP 筆記[1928533]可以讀取這些問題的答案。 

第二個步驟，需要進行比較的內部部署系統的實際資源消耗 Azure 資源與頻寬限制。 因此，客戶必須熟悉 Azure 類型支援 SAP 的區域中的不同的功能︰

* CPU 和記憶體資源的不同 VM 類型和
* 不同的 VM 類型的 IOPS 頻寬和 
* 網路不同 VM 類型的功能。

您可以找到大部分的資料[以下][virtual-machines-sizes]

請記住，在上方連結列的限制是上限。 它不表示的情況下提供的資源，例如 IOPS 任何限制。 但不包括 CPU 和記憶體的資源所選的 VM 類型。 SAP 支援 VM 類型，CPU 和記憶體資源位於保留，因此可在任何時候耗用的 VM 內的時間。

等其他 IaaS 平台版的 Microsoft Azure 平台是多租用戶的平台。 這表示存放裝置、 網路及其他資源租用戶之間共用。 智慧節流和配額邏輯用來防止一個租用戶大幅方式影響效能的另一個租用戶 （雜訊相鄰）。 雖然 Azure 中的邏輯嘗試時發生的頻寬小型的高度共用平台的變異數通常會比許多客戶的資源/頻寬可用性介紹較大的變異數的保留會使用他們的內部部署中。 如此一來，您可能會遇到不同層級的頻寬中的網路或儲存空間 I/O （大量以及延遲） 分鐘分鐘。 SAP 系統上 Azure 可能會遇到較大的變異數超過在內部部署系統必須考慮的機率。

在最後一個步驟是評估可用性需求。 它就會發生，Azure 基礎需要更新，且需要執行重新啟動 Vm 主機。 在下列情況下，就會關閉並重新啟動以及 Vm 這些主機上執行。 完成的時間，例如進行的維修作業的特定區域非核心上班時間，而較寬潛在的幾個小時期間會重新啟動電腦的視窗。 有各種技術內，您可以設定減輕部分或所有的這類更新影響的 Azure 平台。 未來的增強功能的 Azure 的平台 DBMS 和 SAP 應用程式設計用來將最小化的這類重新啟動的影響。 

才能成功部署至 Azure SAP 系統，內部部署 SAP 系統的作業系統，資料庫並 SAP 應用程式必須出現在 SAP Azure 支援矩陣圖中，符合內資源可以提供 Azure 基礎結構的可以使用可用性 Sla Microsoft Azure 優惠。 這些系統會識別出，您必須決定在其中一個下列兩種部署案例。

### <a name="1625df66-4cc6-4d60-9202-de8a0b77f803"></a>雲端專用-不在內部部署的客戶網路上的相依性的虛擬機器部署到 Azure
 
![單一 VM SAP 示範或部署 Azure 中的訓練課程案例][planning-guide-figure-100]

這種情況通常會單元中，或示範系統中單一 VM SAP 及非 SAP 軟體的所有元素的都安裝位置。 生產 SAP 系統不支援在這個案例中部署。 一般而言，這種情況符合下列需求︰

* Vm 本身就可以存取公用網路。 不需要執行內 Vm 擁有示範或單元中的內容或客戶的任一的公司內部網路的應用程式直接網路連線。 
* 若代表單元中或示範案例的多個 Vm，網路通訊與名稱解析必須 Vm 之間執行工作。 但 Vm 一組之間的通訊必須隔離，讓多組 Vm 可以並排部署不干擾。  
* 網際網路連線，才能將遠端登入至裝載於 Azure Vm 使用者。 根據來賓 OS、 終端機服務/RDS 或 VNC/ssh 會用來存取滿足訓練工作，或執行示範 VM。 如果 SAP 連接埠例如 3200、 3300 與 3600 可以公開也可以從任何網際網路連線桌面存取 SAP 應用程式執行個體。
* SAP 系統 （和 VM(s)) 代表只需要公用網際網路連線的使用者權限，並不需要連線至其他 Vm Azure 中 Azure 中獨立的情況。
* SAPGUI 和瀏覽器安裝並直接在 VM 上執行。 
* 快速重設的原始狀態 VM 和新部署的原始狀態一次需要。 
* 若是示範和訓練案例支付的時間，在多個 Vm，Active Directory / OpenLDAP 及/或 DNS 服務時需要 Vm 每組。


![VM 的代表一示範或訓練案例 Azure 雲端服務中的群組][planning-guide-figure-200]

請務必記住，在每一個組 VM(s) 需要部署平行，每個集合中的 VM 名稱完全相同。

### <a name="f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10"></a>跨內部部署-部署單一或多個 SAP Vm 到 Azure 與被完全整合內部部署網路的需求
 
![VPN 網站-連線 （交互內部部署）][planning-guide-figure-300]

這種情況下是以多個可能的部署圖樣跨內部部署案例。 可以執行 SAP 的某些部分橫向內部部署，並在 Azure 上的其他部分的 SAP 橫向只要描述。 Azure 執行的 SAP 元件的組件的所有層面應該都是透明的使用者。 因此 SAP 傳輸校正系統 (STMS)、 RFC 通訊、 列印、 安全性 （例如 SSO) 等會流暢地工作 SAP 系統上 Azure 執行。 但跨內部部署案例也的狀況的描述完成 SAP 橫向 Azure 中執行與客戶的網域和 DNS 延伸至 Azure 位置。 

> [AZURE.NOTE] 這是支援執行生產力 SAP 系統部署。

閱讀[本文][vpn-gateway-create-site-to-site-rm-powershell]如需有關如何將您的內部部署網路連線到 Microsoft Azure

> [AZURE.IMPORTANT] 時所討論 Azure 及內部部署客戶部署之間的跨內部部署案例中，我們會查看整個 SAP 系統的方式。 跨內部部署案例_不支援_的情況如下︰
> 
> * 在不同的部署方法執行不同層級 SAP 應用程式。 例如︰ 執行 DBMS 圖層上內部部署，但 SAP 應用程式層級中 Vm 部署 Azure Vm 或反向操作。
> * SAP [圖層 Azure 和一些內部部署中的部分元件。 例如︰ 分割 SAP 應用程式層級，內部部署和 Azure Vm 之間的執行個體。 
> * 不支援的 Vm 在多個 Azure 區域執行 SAP 系統執行個體的通訊群組。
> 
> 這些限制的原因是很低延遲高效能網路中 SAP 系統，尤其是應用程式執行個體和 DBMS 層之間的 SAP 系統需求。



### <a name="supported-os-and-database-releases"></a>支援的作業系統和資料庫發行

* 支援的 Azure 虛擬機器服務會列在本文中的 Microsoft 伺服器軟體︰ <http://support.microsoft.com/kb/2721672>。 
* 支援的作業系統的版本類似，支援 Azure 虛擬機器服務搭配使用 SAP 軟體與資料庫系統版本會記錄在 SAP 筆記[1928533]。 
* SAP 應用程式和版本支援 Azure 虛擬機器服務 SAP 筆記[1928533]所述。
* 僅限 64 位元圖像支援以來賓 Vm Azure 中執行的 SAP 案例。 這也表示支援只 64 位元 SAP 應用程式與資料庫。

## <a name="microsoft-azure-virtual-machine-services"></a>Microsoft Azure 虛擬機器服務
Microsoft Azure 平台是網際網路比例雲端服務的平台裝載營運及 Microsoft 資料中心。 在平台包含 Microsoft Azure 虛擬機器服務 （基礎結構服務，或 IaaS） 及豐富的平台服務 (PaaS) 功能為一組。

Azure 平台預先技術需要及購買基礎結構。 簡化維護及視需要計算，來主控、 不按比例縮放和管理 web 應用程式與連接的應用程式的儲存空間提供作業系統應用程式。 管理基礎結構會自動與專為可用性平台和動態縮放以符合 pay-as-you-go 價格模型的使用需求。


 
![Microsoft Azure 虛擬機器服務的位置][planning-guide-figure-400]

Azure 虛擬機器服務，Microsoft 讓您能夠部署自訂的伺服器圖像至 Azure，為 IaaS 執行個體 （請參閱圖 4）。 Azure 虛擬機器根據 HYPER-V 虛擬硬碟 (VHD)，可以為來賓 OS 執行不同的作業系統。

操作的觀點來看，從 Azure 虛擬機器服務會提供類似的體驗，為內部部署的虛擬機器。 然而，有明顯您不需要採購、 管理及管理基礎結構的優點。 開發人員和系統管理員具有 「 完全控制這些虛擬機器中的作業系統圖像。 系統管理員可以將這些執行維護及疑難排解工作，以及軟體部署工作的虛擬機器遠端登入。 部署中，就只限制，請將其大小和 Azure Vm 功能。 這些可能無法正常為細緻的設定為此無法完成內部部署。 有代表的組合 VM 類型選擇︰

* 數字的 vCPUs，
* 記憶體
* 可附加的 Vhd 的數字
* 網路和儲存空間的頻寬。

大小及的各種不同的虛擬機器可以在[本文]中的表格中看到所提供的大小限制[virtual-machines-sizes]

您會發現有不同系列或一系列的虛擬機器。 您可以將到 Dec 2015 區分 Vm 的家族如下︰

* A0 A7 VM 類型︰ 所有的儲存為 SAP 取得認證。 Azure IaaS 您使用的是新的第一個 VM 數列。
* A8 A11 VM 類型︰ 高效能運算執行個體。 在不同的好執行執行計算裝載於其他的數列 Vm。
* D 數列 VM 類型︰ 比 A0 A7 更有效地執行。 而非所有 VM 類型是與 SAP 認證。
* DS 數列 VM 類型︰ 相同主機作為 D 系列，但無法連線至 Azure 進階版儲存空間 （請參閱本文件的章節 [Azure 進階版儲存體] [計劃-快速入門-3.3.2]）。 一次使用 SAP 取得認證並非所有 VM 類型。
* G 數列 VM 類型︰ 高記憶體 VM 類型。 
* GS 數列 VM 類型︰ G 數列只包括使用 Azure 進階版儲存空間的選項 （請參閱本文件的章節 [Azure 進階版儲存體] [計劃-快速入門-3.3.2]）。 使用資料庫伺服器 GS 數列 Vm 時，強制 DB 資料和交易記錄檔的使用進階版儲存空間


您可能會發現相同的 CPU 和記憶體設定不同的 VM 數列中。 不過，當您在查詢的下列 Vm 登出不同系列的處理效能他們可能會有大幅的不同。 還是有相同的 CPU 和記憶體設定。 原因是在不同的 VM 類型簡介基礎 host （主機） 伺服器硬體有不同的處理量特性。  通常會顯示在處理效能的差異也會反映在不同的 Vm 的價格。

請注意不所有不同 VM 數列可能會提供中每一種 Azure 區域 （適用於 Azure 區域，請參閱下一章）。 也請注意 SAP 認證並非所有 Vm 數列。

> [AZURE.IMPORTANT] 使用 SAP NetWeaver 基礎的應用程式，支援的 VM 類型與設定 SAP 筆記[1928533]中所列的子集。

### <a name="be80d1b9-a463-4845-bd35-f4cebdb5424a"></a>Azure 區域
Microsoft 可讓部署虛擬機器到稱為 「 Azure 區域]。 Azure 地區可能位於接近的一個或多個資料中心。 大部分的世界淉區域的 Microsoft 會有兩個以上的 Azure 區域。 例如︰ 在 Europe 沒有 ' 北美歐洲 」 和 「 西歐 」 的其中一個 Azure 區域。 這種淉區域內的兩個 Azure 區域隔開明顯的距離，讓自然或技術損毀不會影響相同淉區域中的兩個 Azure 區域。 Microsoft 持續全域建置出不同淉區域中的新 Azure 區域，因為這些區域的持續增加及數到 Dec 2015 達到 [已宣告的其他區域 20 Azure 區域數量。 您為客戶可以部署 SAP 系統將所有這些區域，包括兩個 Azure 區域 china （中國）。 最新的目前 Azure 區域的相關資訊請參閱這個網站︰ <https://azure.microsoft.com/regions/>

### <a name="8d8ad4b8-6093-4b91-ac36-ea56d80dbf77"></a>Microsoft Azure 虛擬機器概念
Microsoft Azure 服務 (IaaS) 解決方案，類似的功能主機虛擬機器做為內部部署虛擬化解決方案，提供基礎結構。 您就可以建立虛擬機器從 Azure 入口網站、 PowerShell 或 CLI，也提供部署及管理功能。

Azure 資源管理員可讓您佈建應用程式使用宣告式範本。 在單一範本中，您可以部署多個服務，以及它們的相依性。 您可以使用相同的範本以重複部署應用程式在每個階段的應用程式生命週期。

可以在這裡找到使用 ARM 範本的相關資訊︰

* [部署及管理使用 Azure 資源管理員範本和 Azure CLI 的虛擬機器][virtual-machines-linux-cli-deploy-templates]
* [管理使用 Azure 資源管理員與 PowerShell 的虛擬機器][virtual-machines-deploy-rmtemplates-powershell]
* <https://azure.microsoft.com/documentation/templates/>

另一個感興趣的功能是能夠建立虛擬機器中的圖像可讓您準備要從中您就可以快速部署符合您需求的虛擬機器執行個體特定存放庫。

深入瞭解建立虛擬機器中的圖像可以找到[本文 (Windows)]中[virtual-machines-windows-capture-image]或本文[(Linux)][virtual-machines-linux-capture-image]。

#### <a name="df49dc09-141b-4f34-a4a2-990913b30358"></a>故障網域
故障網域代表實際的單位失敗，非常密切相關資料中心，和在實體刀或機架可以視為故障網域時所包含的實體基礎結構，有兩個之間沒有直接一對一對應。 

在多個虛擬機器部署屬於 Microsoft Azure 虛擬機器服務中的一個 SAP 系統時，您可能會影響 Azure 布料的轉印圖樣控制器部署至不同的故障網域，藉此會議的需求的 Microsoft Azure SLA 應用程式。 不過的故障網域透過 Azure 刻度] 單位 （數百運算節點或儲存空間節點和網路的集合） 或工作分派的 Vm 到特定錯誤的網域是不能直接控制的。 若要直接移至不同的故障網域部署 Vm 一組 Azure 布料的轉印圖樣控制器，您需要指派 Azure 可用性設定 Vm 部署時。 如需有關 Azure 可用性集的詳細資訊，請參閱此文件中的章節 [Azure 可用性組] [計劃-快速入門-3.2.3]。

#### <a name="fc1ac8b2-e54a-487c-8581-d3cc6625e560"></a>升級的網域
升級網域代表邏輯單位，以決定如何 VM 內的 SAP 系統，包括 SAP 執行之執行個體中多個 Vm，就會更新。 升級時，Microsoft Azure 會經歷的更新這些升級網域一個接一個程序。 分配 Vm 部署時，移至不同的升級的網域，您可以保護您 SAP 系統只能從潛在停機時間。 若要強制部署分散到不同的升級網域 SAP 系統的 Vm Azure，您需要的每個 VM 部署時設定特定的屬性。 類似故障網域，Azure 刻度] 單位分成多個升級的網域。 若要直接移至不同的升級網域部署 Vm 一組 Azure 布料的轉印圖樣控制器，您需要指派 Azure 可用性設定 Vm 部署時。 如需有關 Azure 可用性集的詳細資訊，請參閱下方的章節 [Azure 可用性組] [計劃-快速入門-3.2.3]。

#### <a name="18810088-f9be-4c97-958a-27996255c665"></a>Azure 可用性集
Azure 虛擬機器中一個 Azure 可用性設定將會透過不同的錯誤和升級網域散發 Azure 布料的轉印圖樣控制器。 移到不同的錯誤和升級網域分配的目的是為了防止 SAP 系統上的所有 Vm 要關閉在基礎結構進行的維修作業或一個錯誤網域中的任何失敗。 根據預設，Vm 不是可用性設定的一部分。 部署時，或稍後重新設定並重新部署 VM 定義 VM 參與可用性設定。

若要瞭解的概念 Azure 可用性集和可用性組關聯到錯誤，升級的網域的方式，請閱讀[本文][virtual-machines-manage-availability]

若要定義可用性[rest api 規格](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-compute/2015-06-15/swagger/compute.json)和 「 可用性 」 的搜尋，請參閱透過 json 範本的手錶集。

### <a name="a72afa26-4bf4-4a25-8cf7-855d6032157f"></a>儲存空間︰ Microsoft Azure 儲存體及資料磁碟
Microsoft Azure 虛擬機器使用不同的儲存空間類型。 Azure 虛擬機器服務實作 SAP 時請務必瞭解這兩種主要類型的儲存空間之間的差異︰

* 非持續、 動態儲存空間。
* 持續性的儲存空間。

非持續儲存空間直接連接到執行的虛擬機器上及運算節點本身 – 本機的執行個體儲存空間 （暫時儲存區）。 大小取決於虛擬機器部署啟動時所選擇的大小。 此儲存空間類型動態並重新啟動虛擬機器執行個體時，因此初始化磁碟。 一般而言，作業系統的分頁位於暫時磁碟上。

___

> ![Windows][Logo_Windows] Windows
>
> 在 Windows Vm temp 磁碟機被連接成磁碟機中部署 VM D:\。
>
> ![Linux][Logo_Linux] Linux
> 
> 在 Linux Vm 上它會裝載 /mnt/resource 或 /mnt。 請參閱以下更多詳細資料︰
> 
> * [如何將資料磁碟附加到 Linux 虛擬機器][virtual-machines-linux-how-to-attach-disk]
> * <http://blogs.msdn.com/b/mast/archive/2013/12/07/understanding-the-temporary-drive-on-windows-azure-virtual-machines.aspx>

___

Host （主機） 伺服器上快速儲存之後，所以動態實際的磁碟機。 如果重新部署移動 VM （例如，因為進行的維護作業主機或關閉並重新啟動） 磁碟機的內容會遺失。 因此，即表示無法儲存此磁碟機上的重要資料的選項。 使用此類型的儲存空間的媒體類型不同之間的年 6 月 2015 年看起來像非常不同的效能特性不同 VM 數列︰

* A5 A7︰ 非常有限的效能。 建議您不要超出頁面檔案的任何項目
* A8 A11︰ 使用一些十位分節點 IOPS 很好的效能特性和 > 1 GB/sec 處理量。
* D 系列︰ 使用一些然後千分位 IOPS 很好的效能特性和 > 1 GB/sec 處理量。
* DS 系列︰ 使用一些十位分節點 IOPS 很好的效能特性和 > 1 GB/sec 處理量。
* G 系列︰ 使用一些十位分節點 IOPS 很好的效能特性和 > 1 GB/sec 處理量。
* GS 系列︰ 使用一些十位分節點 IOPS 很好的效能特性和 > 1 GB/sec 處理量。

上述陳述式會套用至取得認證和 SAP VM 類型。 某些 DBMS 功能來善用合格 VM 數列與絕佳 IOPS 處理量。 請參閱 [DBMS 部署指南] [dbms 輔助線] 以取得詳細資料。

Microsoft Azure 儲存體提供保存儲存空間及保護與重複舊存放區上顯示的一般層級。 根據 Azure 儲存空間的磁碟是虛擬硬碟 (Vhd) 位於 Azure 儲存服務。 OS 本機磁碟 (Windows c:\, Linux / (/ 開發/sda1)) 儲存於 Azure 儲存體和其他區/磁碟裝載的 vm 取得儲存在那裡，太。

上傳現有的 VHD 來自內部部署或建立空白的文件從 Azure 中並附加至部署 Vm 可能是。 這些 Vhd 參照為 Azure 磁碟。 

建立或上傳到 Azure 儲存體的 VHD 之後, 可能是裝載及附加至現有的虛擬機器並複製現有的 （卸載） VHD。

這些 Vhd 會持續，資料和內的那些變更受到安全重新開機，然後重新建立虛擬機器執行個體。 即使已刪除的執行個體，這些 Vhd 保持安全，可以部署或若非 OS 磁碟可以連接至其他 Vm。

Azure 儲存體的網路中，您可以設定不同的重複性層級︰

* 您可以選取的最小層級是 「 本機複本，' 相當於 Azure 區域的相同的資料中心的資料的三個-複本 （請參閱章節 [Azure Regions][planning-guide-3.1])。 
* 區域多餘的儲存它會將三個圖像分散不同的資料中心的相同的 Azure 區域內。
* 預設重複層級是地理重複性的非同步會內容複製到另一個 Azure 地區是裝載在相同的淉地區資料的另一個 3 的圖像。

另請參閱本文中的不同重複選項上方表格︰ <https://azure.microsoft.com/pricing/details/storage/> 

如需詳細資訊與 Azure 儲存體可以在這裡找到︰ 

* <https://azure.microsoft.com/documentation/services/storage/>
* <https://azure.microsoft.com/services/site-recovery>
* <https://msdn.microsoft.com/library/windowsazure/ee691964.aspx>
* <https://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/azure-disk-encryption-for-linux-and-windows-virtual-machines-public-preview.aspx>


#### <a name="azure-standard-storage"></a>Azure 標準的儲存空間
Azure 標準 BLOB 儲存體發行 Azure IaaS 時可用的儲存區類型。 沒有強制執行每個單一 VHD IOPS 配額。 延遲經驗不相同的類別為舊/NAS 裝置通常部署高階 SAP 系統裝載於內部部署。 不過，Azure 標準存放證明滿足需要數百所需同時部署 Azure 中的 SAP 系統。

Azure 標準存放負責上所儲存的實際資料的儲存空間交易輸出資料傳輸，選擇重複選項音量。 許多 Vhd 可以建立在 [最大的 1 TB 的大小，但只要維持空白不另外收費。 如果您然後填滿與 100 GB 每一個 VHD，您需要付費儲存 100 GB，不能用於 nominal VHD 您建立的大小。

#### <a name="ff5ad0f9-f7f4-4022-9102-af07aef3bc92"></a>Azure 進階版儲存空間
在 2015 年 4 月 Microsoft 推出 Azure 進階版儲存空間。 進階版儲存您使用的被新提供目標︰

* 更好的 I/O 延遲。
* 更好的處理量。
* 在 I/O 延遲較少變化。

為該目的的兩個最重要的引進了許多變更︰

* Azure 儲存體節點中的 SSD 磁碟的使用方式
* 新的讀取儲存於本機 SSD Azure 運算節點的快取

在以標準功能未變更的儲存空間的磁碟 （或 VHD），大小而定進階版儲存目前有 3 結尾的常見問題集在區段前本文會顯示不同的磁碟分類︰ <https://azure.microsoft.com/pricing/details/storage/>

您看到 IOPS/VHD 和磁碟處理量/VHD 會依據 [大小] 類別的磁碟

若是進階版儲存空間的成本的基礎不是儲存在這種 Vhd，但這類 VHD，VHD 中儲存的資料量不受影響的 [大小] 類別中實際的資料量。

您也可以建立 Vhd 上不會直接對應到大小類別顯示的進階版儲存空間。 這可能大小寫，尤其是標準的儲存空間 Vhd 複製到進階版儲存空間。 在這種情況下被執行對應到下一個最大進階版儲存磁碟] 選項。 

請注意，只有特定 VM 數列的效益從 Azure 進階版儲存空間。 到 Dec 2015，這些是 DS 和 GS 數列。 DS 數列基本上是相同 D 數列 DS 數列能夠裝載進階版儲存為例外狀況 Vm 此外與 Vhd 裝載基於 Azure 標準的儲存空間。 同樣的動作是有效的相較於 GS 數列 G 數列。

如果您取出 DS 數列 Vm[本文]中的部分[virtual-machines-sizes]您也會發現有資料大量限制進階版的儲存空間 vhd 資料粒度 VM 層級。 不同的 DS 數列] 或 [GS 數列 Vm 也有不同的限制對 Vhd 可以裝載的數目。 這些限制會記錄在以及上述的文件。 但基本上這表示您例如裝載 32 x P30 磁碟/Vhd 與單一 DS14 VM 如果您可以不取得 32 x P30 磁碟的最大的處理量。 請改為 VM 層級為文件中的最大處理能力會限制資料處理量。 

可以在這裡找到進階版儲存空間的詳細資訊︰ <http://azure.microsoft.com/blog/2015/04/16/azure-premium-storage-now-generally-available-2>

#### <a name="azure-storage-accounts"></a>Azure 儲存體帳戶
在部署的服務或 Vm Azure 中的時，必須組織部署 Vhd 和 VM 圖像稱為 Azure 儲存體帳戶的單位。 在規劃的 Azure 部署時，必須先仔細考慮 Azure 的限制。 在一側邊，還有有限的儲存空間帳戶每個 Azure 訂閱。 雖然每個 Azure 儲存體帳戶可保留 VHD 檔案有很多，固定的限制上沒有總 IOPS 每個儲存帳戶。 在下的數百個 SAP Vm 部署與 DBMS 系統建立嚴重 IO 通話時，建議發佈高 IOPS DBMS Vm 多個 Azure 儲存體帳戶。 必須小心，並且不超過 Azure 儲存體帳戶的每個訂閱目前的限制。 由於儲存資料庫的部署 SAP 系統很重要的一部分，此概念會討論已參照 [DBMS 部署指南] [dbms 輔助線] 中的更多詳細資料。

[本文]中找 Azure 儲存體帳戶的相關資訊[storage-scalability-targets]。 閱讀本文，您會發現有限制 Azure 標準的儲存空間帳戶和進階版儲存帳戶之間的差異。 主要差異是可以儲存這類儲存帳戶內的資料量。 在標準的儲存空間音量大小大於進階版儲存空間。 另一端標準的儲存空間帳戶嚴重的有限的 IOPS （請參閱 「 總要求工資率] 欄），而 Azure 進階版儲存帳戶有這樣的限制。 在討論的 SAP 系統，尤其是 DBMS 伺服器部署時，我們將討論詳細資料] 與 [這些差異的結果。

中儲存的帳戶，您必須建立不同的容器，來組織及分類不同 Vhd 的可能性。 例如個別 Vhd 的不同 Vm 通常會使用這些容器。 不有使用只要容器或單一 Azure 儲存體帳戶下方的多個容器中的任何效能的影響。

Azure 中 VHD 名稱，請遵循下列命名連線所提供 VHD Azure 中的唯一名稱︰

    http(s)://<storage account name>.blob.core.windows.net/<container name>/<vhd name>

如上文所提上述字串必須唯一識別 VHD 儲存於 Azure 儲存體。

### <a name="61678387-8868-435d-9f8c-450b2424f5bd"></a>Microsoft Azure 網路
Microsoft Azure 會提供可讓所有的情況下，我們要使用 SAP 軟體發現的對應的網路基礎結構。 功能包括︰

* 從存取外，直接透過 Windows 終端機服務或 ssh/VNC Vm
* 存取服務和 Vm 內的應用程式所使用的特定連接埠
* 內部通訊和 Vm 部署為 Azure Vm 群組之間的名稱解析
* 跨內部部署客戶的內部網路和 Azure 網路之間的連線
* 跨 Azure 地區資料中心之間或連線 Azure 網站 

詳細資訊，請參閱以下︰ <https://azure.microsoft.com/documentation/services/virtual-network/>

有許多不同設定 Azure 中的名稱及 IP 解析度的可能性。 在此文件中雲端專用的案例依賴使用 Azure DNS （相較於定義自己的 DNS 服務） 的預設值。 也有可供，設定您自己的 DNS 伺服器而不是新 Azure DNS 服務。 可以在[本文]中找到更多資訊[virtual-networks-manage-dns-in-vnet]和在[此頁面](https://azure.microsoft.com/services/dns/)上。

我們會針對跨內部部署案例依賴交易的內部部署至 Azure AD/OpenLDAP DNS 延伸透過 VPN 或私人的連線。 針對某些如以下所述的情況下，可能需要安裝 Azure AD/OpenLDAP 複本。

因為網路名稱解析相當重要的 SAP 系統資料庫的部署，此概念討論 [DBMS 部署指南] [dbms 輔助線] 中的更多詳細資料。


##### <a name="azure-virtual-networks"></a>Azure 虛擬網路

藉由建立 Azure 虛擬網路中，您可以定義地址的私人 IP 位址範圍配置 Azure DHCP 功能。 在跨內部部署案例中定義的 IP 位址範圍會仍使用 DHCP 由配置 Azure。 不過，網域名稱解析都是內部部署 （假設 Vm 內部部署的網域的一部分），因此可以解決超出不同 Azure 雲端服務的地址。

[註解]: <> (MSSedusch 仍需嗎？TODO 原本 Azure 虛擬網路繫結至相關性的群組。用中 Azure 虛擬網路是 Azure 刻度] 單位相關性群組有指派給的限制。最後，這表示虛擬網路已限制為 Azure 刻度] 單位中可用的資源。由於已經和 Azure 虛擬網路現在拉多個 Azure 刻度] 單位。不過，需要 Azure 虛擬網路是 * * 不 * * 相關聯的相似性群組留在建立時間。我們已先前所述，在以一年之內，建議您應該 * * 不會再利用 Azure 相關性群組 * *。如需詳細資訊，請參閱 < https://azure.microsoft.com/blog/regional-virtual-networks/>)

每個虛擬機器中 Azure 必須虛擬網路連線。

更多詳細資料，請參閱[本文][resource-groups-networking]和在[此頁面](https://azure.microsoft.com/documentation/services/virtual-network/)上。

[註解]: <> (MShermannd TODO 無法尋找包含 OpenLDAP 主題 + ARM; 的文件)
[註解]: <> (MSSedusch < https://channel9.msdn.com/Blogs/Open/Load-balancing-highly-available-Linux-services-on-Windows-Azure-OpenLDAP-and-MySQL>)

> [AZURE.NOTE] 根據預設，一旦 VM 部署您無法變更虛擬網路設定。 Azure DHCP 伺服器，必須保持 TCP/IP 設定。 預設行為是動態 IP 工作分派。

虛擬網路介面卡的 MAC 地址，例如可能會變更後重新調整大小和 Windows 或 Linux 的來賓 OS 挑選新網路介面卡，並會自動使用 DHCP，在本例中指定的 IP 和 DNS 的地址。

##### <a name="static-ip-assignment"></a>靜態 IP 工作分派
若要指派給 Vm Azure 虛擬網路中的 [固定或保留 IP 位址可能是。 Azure 虛擬網路中執行 Vm 隨即會開啟好可能會使用這項功能，如果有需要或所需的某些情況。 IP 指派仍然有效整個 VM，獨立於 VM 是否執行或關閉存在。 如此一來，您需要的虛擬網路定義的 IP 位址範圍時需要 Vm (執行並已停止 VM) 至帳戶的整體數目。 [直到 VM 和其網路介面會刪除或取消重新指派的 IP 位址取得，仍然指定的 IP 位址。 [本文]中的詳細的資訊，請參閱[virtual-networks-static-private-ip-arm-pportal]。

##### <a name="multiple-nics-per-vm"></a>多個 Nic 每 VM
您可以定義多個虛擬網路介面卡 (vNIC) 的 Azure 虛擬機器。 有多個 vNICs，您可以開始設定網路流量的分隔位置例如用戶端流量會導向到一個 vNIC 和後端的流量會經由第二個 vNIC。 相依的類型 VM 有是不同的限制，對 vNICs 數目。 確切的詳細資料、 功能和限制您可以找到這些文件︰
 
* [建立多個 Nic VM][virtual-networks-multiple-nics]
* [部署多重 NIC Vm 使用範本][virtual-network-deploy-multinic-arm-template]
* [部署多重 NIC Vm 使用 PowerShell][virtual-network-deploy-multinic-arm-ps]
* [部署多重 NIC Vm 使用 Azure CLI][virtual-network-deploy-multinic-arm-cli]

#### <a name="site-to-site-connectivity"></a>連線至網站
跨內部部署是 Azure Vm 和內部部署與透明和永久 VPN 連線連結。 預期成為 Azure 中的常見 SAP 部署模式。 假設是操作程序和 Azure 中的 SAP 執行個體的程序應該使用無障礙。 這表示您應該可以列印出這些系統，以及使用 SAP 傳輸管理系統 (TMS) 傳輸變更從 Azure 開發系統的測試系統部署內部部署。 網站-周圍的更多文件，請參閱[本文][vpn-gateway-create-site-to-site-rm-powershell]

##### <a name="vpn-tunnel-device"></a>VPN 通道裝置
若要建立網站的連線 （內部部署資料中心 Azure 資料中心），您必須取得並設定 VPN 裝置，或使用與 Windows Server 2012 的軟體元件路由及遠端存取服務 (RRAS) 早的。 

* [使用 PowerShell 網站-VPN 連線建立虛擬網路][vpn-gateway-create-site-to-site-rm-powershell]
* [連線至網站 VPN 閘道器的 VPN 和裝置有關][vpn-gateway-about-vpn-devices]
* [VPN 閘道器常見問題集][vpn-gateway-vpn-faq]

![內部部署和 Azure 之間的網站的連線][planning-guide-figure-600]

圖顯示兩個 Azure 訂閱 Azure 虛擬網路中有保留使用量的 IP 位址子範圍。 透過 VPN 建立 Azure 從內部部署網路的連線。

#### <a name="point-to-site-vpn"></a>點-網站 VPN
點-網站 VPN 需要有自己的 VPN 連線到 Azure 每個用戶端電腦。 SAP 情況我們掌控的情況下，點為網站連線並不實用。 因此，會不指定任何進一步的參照，以點為網站 VPN 連線。

[註解]: <> (MSSedusch-詳細資訊，請參閱以下)
[註解]: <> (MShermannd TODO 連結不再有效。但仍不支援手臂-請參閱下面的連結)
[註解]: <> (MSSedusch-< http://msdn.microsoft.com/library/azure/dn133798.aspx>。)
[註解]: <> (MShermannd TODO 指向的手錶還不支援的網站)
[註解]: <> (MSSedusch-< https://azure.microsoft.com/documentation/articles/vpn-gateway-point-to-site-create/>)

#### <a name="multi-site-vpn"></a>多網站 VPN
Azure 最近也會提供可能會建立一個 Azure 訂閱的多網站 VPN 連線。 先前有一個訂閱限於一個網站-VPN 連線。 這項限制不見了以單一訂閱的多網站 VPN 連線。 這可以讓您運用透過跨內部部署設定為特定訂閱的多個 Azure 區域。

如需更多文件請參閱[這篇文章][vpn-gateway-create-site-to-site-rm-powershell]
[註解]︰ <> （MShermannd TODO 找到沒有 ARM 記錄連結）

#### <a name="vnet-to-vnet-connection"></a>VNet 到 VNet 連線
使用多個網站 VPN，必須先設定在每一個區域中的個別 Azure 虛擬網路。 不過通常，您必須在不同區域內的軟體元件應該彼此的需求。 最好是從內部部署的一個 Azure 區域，然後從該處 Azure 區域，應該不傳送此通訊。 到快速鍵 Azure 會提供設定從一個 Azure 虛擬網路連線到另一個 Azure 虛擬網路的一個區域中的可能性裝載於其他區域。 這項功能稱為 VNet-VNet 連線。 此功能的更多詳細資料可以在這裡找到︰ <https://azure.microsoft.com/documentation/articles/vpn-gateway-vnet-vnet-rm-ps/>。

#### <a name="private-connection-to-azure--expressroute"></a>私人連線到 Azure – ExpressRoute
Microsoft Azure ExpressRoute 可讓您建立的私人 Azure 資料中心之間有客戶的內部部署基礎結構，或在代管環境中的連線。 由各種 MPLS 所提供 ExpressRoute （封包切換） VPN 提供者或其他網路服務提供者。 ExpressRoute 連線，請勿繼續進行公用網際網路上。 ExpressRoute 連接在網際網路上可提供較高的安全性、 更多可靠性透過多個平行電路、 更快的速度和較低的延遲時間，比一般的連線。 

尋找更多詳細資料，Azure ExpressRoute 和以下方案︰

* <https://azure.microsoft.com/documentation/services/expressroute/>
* <https://azure.microsoft.com/pricing/details/expressroute/>
* <https://azure.microsoft.com/documentation/articles/expressroute-faqs/>

快速傳送啟用一個 ExpressRoute 電路透過 Azure 的多個訂閱，如以下所述 

* <https://azure.microsoft.com/documentation/articles/expressroute-howto-linkvnet-arm/> 
* <https://azure.microsoft.com/documentation/articles/expressroute-howto-circuit-arm/>


#### <a name="forced-tunneling-in-case-of-cross-premise"></a>強制通道若跨內部部署
Vm 加入至網站、 點為網站，或是 ExpressRoute 內部部署網域，您要確定這些 Vm 以及中的所有使用者的快速都部署網際網路 proxy 設定。 根據預設，那些 Vm 或使用者存取網際網路使用瀏覽器中執行的軟體不想要瀏覽公司 proxy]，但想要直接透過 Azure 網際網路連線。 但甚至 proxy 設定不會 100%解決方案，將透過公司 proxy 流量，因為它是軟體及服務檢查 proxy 責任。 如果 VM 中執行的軟體不會執行，或系統管理員管理設定，可以再次 detoured 網際網路流量直接透過 Azure 至網際網路。 

若要避免此問題，您可以設定強制通道網站-內部部署和 Azure 之間的連線。 強制通道功能的詳細的描述是發佈此處<https://azure.microsoft.com/documentation/articles/vpn-gateway-forced-tunneling-rm/> 

使用 ExpressRoute 強制的通道會啟用通知的預設路由，透過 ExpressRoute BGP 對等工作階段的客戶。

#### <a name="summary-of-azure-networking"></a>Azure 網路的摘要
此章節包含許多關於 Azure 網路的重要事項。 以下是重點的摘要︰


* Azure 虛擬的網路可讓設定網路，根據您自己的需求
* Azure 虛擬網路可以利用來將 IP 位址範圍指派給 Vm 或指定 Vm 固定的 IP 位址
* 若要設定以網站為網站或點為網站連線必須先建立 Azure 虛擬網路
* 部署虛擬機器後就不再可能會變更指派給 VM 虛擬網路

### <a name="quotas-in-azure-virtual-machine-services"></a>Azure 虛擬機器服務中的配額
我們需要清除有關共用的儲存空間和網路基礎結構的之間 Vm Azure 基礎結構中執行各種不同的服務。 而且一樣客戶自己的資料中心，過度佈建基礎結構資源的一些會進行某種程度。 Microsoft Azure 平台使用磁碟、 CPU、 網路和其他配額限制消耗資源並保留一致且確定效能。  不同的 VM 類型 （A5、 A6 等），有不同的配額的 CPU、 RAM 和網路的磁碟數目。

> [AZURE.NOTE] SAP 支援 VM 類型的 CPU 和記憶體資源是主機節點上預先配置。 這表示已部署 VM，主機上的資源將會提供 VM 類型所定義。

規劃及縮放 SAP 上 Azure 解決方案時必須考慮的每個虛擬機器大小配額。  說明 VM 配額[以下][virtual-machines-sizes]。

所述的配額代表理論的最大值。  每個 VHD IOPS 限制可能達成小型 IOs (8 kb)，但可能可能無法達到搭配大型 IOs (1 Mb)。  IOPS 會強制執行限制在單一 Vhd 的方式。

為概略決策樹決定 SAP 系統是否符合 Azure 虛擬機器服務和功能或現有的系統是否需要才能部署 Azure 中的系統設定不同，您可以使用下列決策樹︰
 
![若要決定部署上 Azure SAP 能力決策樹][planning-guide-figure-700]

**步驟 1**︰ 最重要的資訊所指定的 SAP 系統 SAP 需求。 SAP 需求需要分開到 DBMS 組件 」 與 「 SAP 應用程式組件，即使 SAP 系統已部署內部部署的 2 層設定。 對於現有系統，經常使用的硬體相關的 SAP 可以取決於或根據現有的 SAP 基準估計。 您可以在這裡找到結果︰ <http://global.sap.com/campaigns/benchmark/index.epx>。 最新的 SAP 系統，您應該已經完成縮放練習應該決定 SAP 系統需求。
請參閱此部落格和 SAP 縮放附加文件上 Azure: <http://blogs.msdn.com/b/saponsqlserver/archive/2015/12/01/new-white-paper-on-sizing-sap-solutions-on-azure-public-cloud.aspx>

**步驟 2**︰ 現有系統，I/O 音量和秒 DBMS 伺服器上的 I/O 作業應該測量。 新計劃系統，縮放練習中新的系統也應該讓 I/O 需求的 DBMS 端的概略想法。 如果不確定，最後需要進行的概念。

**步驟 3**︰ 比較不同的 Azure VM 類型可提供 SAP DBMS 伺服器 SAP 需求。 SAP 筆記[1928533]說明 SAP Azure VM 不同類型的資訊。 焦點應該 DBMS VM 上第一次由於資料庫階層圖層中不會在大部分的部署延展 SAP NetWeaver 系統。 相反地，SAP 應用程式層級，可以調整。 如果 SAP 都不受支援 Azure VM 類型可以進行必要的 SAP，無法在 Azure 執行計劃 SAP 系統的工作量。 您可能需要部署系統內部部署或您需要變更系統的工作量音量。

**步驟 4**︰ 為記錄[以下][virtual-machines-sizes]，無論您使用標準的儲存空間或進階版儲存 Azure 強制執行每 VHD 獨立 IOPS 配額。 VM 類型而定，可以裝載的 Vhd 數目不盡相同。 如此一來，您可以計算 IOPS 數目上限可以達成與每個不同的 VM 類型。 資料庫檔案版面配置而定，您可以等量磁碟區成為一個大量來賓 OS Vhd。 不過，如果目前 IOPS 大量的部署 SAP 系統超過最大 VM 類型 Azure 及是否有更多記憶體賠償沒有機會的計算結果的限制，工作負載的 SAP 系統可能會受到影響嚴重。 在這種情況下，您可以按應不部署 Azure 中的系統的點。

**步驟 5**︰ 特別是在 SAP 系統會部署於內部部署的 2 層設定，有可能是因為系統可能需要在 Azure 3 層設定，設定。 在此步驟中，您需要檢查 SAP 應用程式層其無法橫向和其想不符合不同 Azure VM 類型所提供的 CPU 和記憶體資源是否元件。 如果確實有這類元件，請將 Azure 無法部署 SAP 系統和其工作量。 但如果您可以向外 SAP 應用程式元件到多個 Azure Vm，可以將 Azure 部署系統。 

**步驟 6**︰ 如果中 Azure Vm，就可以執行 DBMS 和 SAP 應用程式層元件，請設定，必須使用 regard 來定義︰

* Azure Vm 的數字
* 個別的元件 VM 類型
* 數字的中提供足夠 IOPS DBMS VM Vhd

## <a name="managing-azure-assets"></a>管理 Azure 資產

### <a name="azure-portal"></a>Azure 入口網站
Azure 入口網站是一種來管理 Azure VM 部署的三個介面。 基本管理工作，例如部署 Vm 的圖像，可以透過 Azure 入口網站。 此外，建立儲存帳戶、 虛擬網路和其他 Azure 元件，也會 Azure 入口網站可處理很好的工作。 不過，功能，例如上傳 Vhd 從內部部署至 Azure 或複製內 Azure VHD 需要協力廠商工具] 或 [透過 PowerShell 或 CLI 管理工作。
 
![Microsoft Azure 入口網站的虛擬機器概觀][planning-guide-figure-800]

[註解]: <> (MSSedusch * < https://azure.microsoft.com/documentation/articles/virtual-networks-create-vnet-arm-pportal/>)
[註解]: <> (MSSedusch * < https://azure.microsoft.com/documentation/articles/virtual-machines-windows-tutorial/>)

從 Azure 入口網站中可能會出現管理和設定工作的虛擬機器執行個體。 

除了重新啟動和關閉虛擬機器您也可以附加、 卸離並建立資料磁碟的虛擬機器執行個體，擷取圖像準備的執行個體，然後設定的虛擬機器執行個體的大小。

Azure 入口網站提供部署和 Vm 及許多其他 Azure 服務所設定的基本功能。 Azure 入口網站涵蓋但不所有可用的功能。 在 [Azure 入口網站，並不執行工作，例如︰

* 上傳 Vhd 至 Azure
* 複製 Vm

[註解]: <> (MShermannd TODO 應該怎麼使用自動化服務的 SAP Vm？)
[註解]: <> (同時可能的多個 Vm os MSSedusch 部署)
[註解]: <> (也 MSSedusch 自動化部署相關的任何類型無法用 Azure 入口網站。工作，例如指令碼的部署的多個 Vm 不可能透過 Azure 入口網站。) 

### <a name="management-via-microsoft-azure-powershell-cmdlets"></a>管理透過 Microsoft Azure PowerShell cmdlet
Windows PowerShell 是已被廣泛部署較大的數字系統 Azure 中的客戶所採用的強大且可延伸架構。 在桌上型電腦、 膝上型電腦或專用的管理站台上的 PowerShell 指令程式的安裝後, 的 PowerShell 指令程式可以從遠端執行。

若要啟用本機桌面/膝上型電腦的 Azure PowerShell cmdlet，以及如何設定這些 Azure 訂閱用法說明[本文]中的使用狀況的程序[powershell-install-configure]。 

如何安裝、 更新及設定 Azure PowerShell cmdlet 的詳細的步驟也可以找到 [部署指南 》 的這一章] 的 [部署-快速入門-4.1]。

客戶經驗到目前為止已 PowerShell (PS) 當然是功能更強大的工具來部署 Vm 和 Vm 部署建立自訂的步驟。 所有的客戶，Azure 中執行 SAP 執行個體補充管理工作，其 Azure 入口網站中執行，或甚至使用 PS cmdlet 以獨佔模式來管理 Azure 中的部署使用 PS cmdlet。 由於 Azure 特定 cmdlet 共用相同的命名慣例，為多個 2000 Windows 相關 cmdlet，則簡單的工作，適用於 Windows 系統管理員以運用這些 cmdlet。

請參閱此處的範例︰ <http://blogs.technet.com/b/keithmayer/archive/2015/07/07/18-steps-for-end-to-end-iaas-provisioning-in-the-cloud-with-azure-resource-manager-arm-powershell-and-desired-state-configuration-dsc.aspx>

[註解]: <> (MShermannd TODO 說明時測試新的 CLI] 命令)
部署 SAP Azure 監控副檔名 (請參閱章節 [Azure 監控解決方案 SAP] [計劃-快速入門-9.1] 文件中) 僅能透過 PowerShell 或 CLI。 因此會強制來安裝和設定 PowerShell 或 CLI 時部署或管理 Azure SAP NetWeaver 系統。  

Azure 提供更多的功能，新 PS cmdlet，將會新增您的需要的 cmdlet 的更新。 因此合理至少一次查看 Azure 下載網站的新版本的 cmdlet 月<https://azure.microsoft.com/downloads/> 。 頂端的舊版本，將只會安裝新的版本。

Azure 一般清單相關的 PowerShell 命令查看此處的︰ <https://msdn.microsoft.com/library/azure/dn708514.aspx>。 

### <a name="management-via-microsoft-azure-cli-commands"></a>管理透過 Microsoft Azure CLI 命令

客戶使用 Linux 並想要管理 Azure 資源 Powershell 可能無法選項。 Microsoft 提供 Azure CLI 另一個方法。
Azure CLI 提供一組開啟來源] 命令的 [使用 Azure 平台的跨平台。 Azure CLI 提供許多的 Azure 入口網站中的相同功能。

有關如何安裝、 設定及如何使用 CLI 命令來完成 Azure 工作請參閱

* [安裝 Azure CLI][xplat-cli]
* [部署及管理使用 Azure 資源管理員範本和 Azure CLI 的虛擬機器][virtual-machines-linux-cli-deploy-templates]
* [使用 Azure CLI for Mac、 Linux 和 Windows 與 Azure 資源管理員][xplat-cli-azure-resource-manager]

請也閱讀章節 [Azure CLI 的 Linux Vm] [部署-快速入門-4.5.2] 中 [部署指南] [計劃指南] 如何使用 Azure CLI 部署 SAP Azure 監控副檔名。

## <a name="different-ways-to-deploy-vms-for-sap-in-azure"></a>部署 Vm SAP Azure 中的不同方式
在此章節中，您將學習部署 VM Azure 中的不同方法。 此章節中包含其他準備程序，以及處理 Vhd 和 Vm Azure 中。

### <a name="deployment-of-vms-for-sap"></a>SAP 的 Vm 的部署
Microsoft Azure 提供多種方式部署 Vm 及相關聯的磁碟。 因此務必瞭解差異，因為 Vm 的準備工作可能會有所不同的部署方法。 一般而言，我們會看看以下情況︰

#### <a name="4d175f1b-7353-4137-9d2f-817683c26e53"></a>將 VM 來自內部部署移至 Azure，以非一般化光碟
您打算將來自內部部署的特定的 SAP 系統移至 Azure。 這可以經由上含有 OS、 SAP 二進位檔案和 DBMS 二進位 VHD 加上 Vhd 傳與 Azure DBMS 的資料和記錄檔。 相反的 [案例下列 #2] [計劃-快速入門-5.1.2]，您將主機名稱、 SAP SID 和 SAP 使用者帳戶 Azure VM 中為內部部署環境中設定。 因此，一般化圖像並不需要。 [準備將 VM 來自內部部署移至 Azure，以非一般化光碟] 的章節，請參閱 [計劃-快速入門-5.2.1] 的內部部署準備步驟和非一般化 Vm 或 Vhd Azure 要上的傳此文件。 請閱讀章節 [案例 3︰ 從內部部署非一般化 Azure VHD 使用 SAP 移動 VM] [部署-快速入門-3.4] 中 [部署指南] [部署指南] 部署這類圖像 Azure 中的詳細步驟。

#### <a name="e18f7839-c0e2-4385-b1e6-4538453a285c"></a>部署 VM 客戶特定圖像
您的作業系統或 DBMS 版本的特定修補程式需求，因為在 Azure Marketplace 提供的圖像可能不符合您的需求。 因此，您可能需要建立 VM 使用您自己 「 私人 」 的部署數次之後的 OS/DBMS VM 圖像。 若要準備重複這類 「 私人 」 的圖像，必須考慮下列項目︰

___

> ![Windows][Logo_Windows] Windows
>
> 必須在內部部署 VM sysprep 命令透過區隔/一般化的 Windows 設定 （例如 Windows SID 或主機名稱）。
[註解]: <> (MSSedusch > 查看以下詳細資料︰)
[註解]: <> (MShermannd TODO 第一個連結的傳統的模型。找不到 Azure 記錄文章)
[註解]: <> (MSSedusch >< https://azure.microsoft.com/documentation/articles/virtual-machines-create-upload-vhd-windows-server/>)
[註解]: <> (MSSedusch >< http://blogs.technet.com/b/blainbar/archive/2014/09/12/modernizing-your-infrastructure-with-hybrid-cloud-using-custom-vm-images-and-resource-groups-in-microsoft-azure-part-21-blain-barton.aspx>)
>
> ![Linux][Logo_Linux] Linux
>
> 請遵循這些文件的[SUSE] [virtual-machines-linux-create-upload-vhd-suse]或[紅色角色][virtual-machines-linux-redhat-create-upload-vhd]準備上傳至 Azure VHD。

___

如果您已安裝在您的內部部署 VM （特別是 2 層系統） SAP 內容，您可以採用 SAP 系統設定後的執行個體透過 Azure VM 部署重新命名 [SAP 軟體佈建管理員 (SAP 筆記[1619720]) 所支援的程序。 請參閱章節 [準備的 SAP 部署客戶特定圖像 VM] [計劃-快速入門-5.2.2] 及 [上傳至 Azure 來自內部部署 VHD] [計劃-快速入門-5.3.2] 的內部部署準備步驟及上傳至 Azure 一般化 VM 的這份文件。 請閱讀章節 [案例 2︰ 為 SAP 部署自訂圖像 VM] [部署-快速入門-3.3] 中 [部署指南] [部署指南] 部署這類圖像 Azure 中的詳細步驟。

#### <a name="deploying-a-vm-out-of-the-azure-marketplace"></a>部署 VM 登出 Azure Marketplace
您想要使用 Microsoft 或第 3 的廠商提供的部署您 VM Azure Marketplace VM 圖像。 部署 Azure 中的您 VM 之後，您可以依照相同的準則及安裝 SAP 軟體及/或您 VM 內部 DBMS，當您想要執行的內部部署環境中的工具。 如需詳細部署描述，請參閱章節 [案例 1︰ 部署 VM SAP Azure 服務商場出] [部署-快速入門-3.2 捨位] 中 [部署指南] [部署指南]。

### <a name="6ffb9f41-a292-40bf-9e70-8204448559e7"></a>準備 Azure Vm 和 SAP
上傳到 Azure，請確定您需要的 Vm 之前 Vm 和 Vhd 符合特定需求。 有些許差異，根據用部署方法。 

#### <a name="1b287330-944b-495d-9ea7-94b83aff73ef"></a>準備將 VM 來自內部部署移至 Azure，以非一般化光碟
常見的部署方法是將來自內部部署的 SAP 系統執行 Azure 至現有 VM。 該 VM，VM SAP 系統只應該執行 Azure 中使用相同的主機名稱和很可能是相同的 SAP SID。 在此情況下來賓 OS 的 VM 不應該一般化的多個部署。 如果內部網路有延伸至 Azure (請參閱章節 [跨內部部署-部署單一或多個 SAP Vm 到的正完全整合內部部署網路需求與 Azure] [計劃-快速入門-2.2] 文件中)，然後甚至相同的網域帳戶中可以使用 VM 那些使用內部部署之前。 

準備自己 Azure VM 磁碟時的需求如下︰

* 原本包含作業系統 VHD 可能有大小上限 127 gb 只。 這項限制您已在 2015 年 3 月結尾處。 現在包含作業系統 VHD 可 1 TB 的大小，以及任何其他 Azure 儲存體裝載的 VHD。
[註解]: <> (MShermannd TODO 需要核取 [如果 CLI 也將轉換成靜態)
* 它必須位於 [固定 VHD 格式。 動態 Vhd 或 Vhd VHDx 格式是尚未支援 Azure。 動態 Vhd 會轉換為靜態 Vhd，當您上傳與 PowerShell shell 指令程式 VHD 或 CLI
* 已裝載的 vm，並應會裝載再次 Azure 中 VM 必須位於 [固定 VHD 格式以及 Vhd。 OS 磁碟的相同的大小限制適用於以及資料磁碟。 Vhd 可以有 1 tb 的大小上限。 動態 Vhd 會轉換為靜態 Vhd，當您上傳與 PowerShell shell 指令程式 VHD 或 CLI
* 新增另一個本機系統管理員權限可以使用 Microsoft 支援服務或其可以指派為服務和應用程式執行，直到 VM 部署的內容，而更適當的使用者帳戶可以使用。
* 使用僅供雲端部署案例的大小寫的 (請參閱章節 [雲端專用-不在內部部署的客戶網路上的相依性的虛擬機器部署到 Azure] [計劃-指南-2.1] 的 [這份文件) 搭配此部署方法，網域的帳戶可能無法運作後 Azure 中部署 Azure 磁碟。 特別是用來執行 DBMS 或 SAP 應用程式等服務的帳戶。 因此您必須以 VM 本機帳戶取代這類網域帳戶和刪除內部部署的網域帳戶 VM 中。 保留 VM 圖像中的內部部署網域使用者不是問題章節 [跨內部部署-部署單一或多個 SAP Vm 到的正完全整合內部部署網路需求與 Azure] 中所述 VM 部署跨內部部署情境中操作時 [計劃-快速入門-2.2] 文件中。
* 如果網域的帳戶當成 DBMS 登入或使用者執行系統內部部署與這些 Vm 應該在雲端專用的案例中部署時，網域使用者需要刪除。 您需要確認的本機系統管理員以及其他 VM 本機使用者已新增的身分登入/到系統管理員的身分 DBMS。
* 新增其他本機帳戶，因為這些可能需要為特定的部署案例。

___

> ![Windows][Logo_Windows] Windows
>
> 在這個案例中上傳並部署上 Azure VM 需要 VM 沒有歸納 (sysprep)。
> 請確定的磁碟機 D:\ 不是用於設定磁碟自動掛接連接的磁碟章節 [設定自動附加磁碟掛接] 中所述 [計劃-快速入門-5.5.3] 文件中。
> 
> ![Linux][Logo_Linux] Linux
>
> 在這個案例中沒有一般化 (waagent-deprovision) 的 VM 需要上傳並部署上 Azure VM。
> 請確定不會使用/mnt/資源所有磁碟會都裝載透過 uuid。 針對 OS 磁碟務必開機載入器項目也會反映 uuid 為基礎的連接。

___

#### <a name="57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3"></a>準備的 SAP 部署 VM 客戶特定圖像
VHD 檔案包含一般化的 OS 也會儲存在容器上 Azure 儲存體帳戶。 您可以部署新的這類圖像 VHD VM 章節所述，做為來源部署範本檔案中的 VHD 參照 VHD [案例 2︰ 為 SAP 部署自訂圖像 VM] [部署-快速入門-3.3] 的 [部署指南] [部署指南]。 

準備自己 Azure VM 圖像時的需求如下︰

* 原本包含作業系統 VHD 可能有大小上限 127 gb 只。 這項限制您已在 2015 年 3 月結尾處。 現在包含作業系統 VHD 可 1 TB 的大小，以及任何其他 Azure 儲存體裝載的 VHD。
[註解]: <> (MShermannd TODO 需要核取 [如果 CLI 也將轉換成靜態)
* 它必須位於 [固定 VHD 格式。 動態 Vhd 或 Vhd VHDx 格式是尚未支援 Azure。 動態 Vhd 會轉換為靜態 Vhd，當您上傳與 PowerShell shell 指令程式 VHD 或 CLI
* 已裝載的 vm，並應會裝載再次 Azure 中 VM 必須位於 [固定 VHD 格式以及 Vhd。 OS 磁碟的相同的大小限制適用於以及資料磁碟。 Vhd 可以有 1 tb 的大小上限。 動態 Vhd 會轉換為靜態 Vhd，當您上傳與 PowerShell shell 指令程式 VHD 或 CLI
* 由於網域的所有使用者都註冊為 VM 中的使用者不存在於雲端專用的情境中操作 (請參閱章節 [雲端專用-不在內部部署的客戶網路上的相依性的虛擬機器部署到 Azure] [計劃-指南-2.1] 的 [這份文件)、 使用這類網域的帳戶可能無法運作後圖像部署 Azure 中的服務。 特別是用來執行 DBMS 或 SAP 應用程式等服務的帳戶。 因此您必須以 VM 本機帳戶取代這類網域帳戶和刪除內部部署的網域帳戶 VM 中。 保留 VM 圖像中的內部部署網域使用者可能無法問題章節 [跨內部部署-部署單一或多個 SAP Vm 到的正完全整合內部部署網路需求與 Azure] 中所述 VM 部署跨內部部署情境中操作時 [計劃-快速入門-2.2] 文件中。
* 新增具有管理員權限的問題調查或其可以指派為服務和應用程式執行，直到 VM 部署的內容中的 Microsoft 支援服務和其他適當的使用者可以使用另一個本機帳戶可以使用。
* 在雲端專用的部署和位置的網域帳戶用來做為 DBMS 登入或使用者執行系統內部部署時，網域使用者應加以刪除。 您需要確認本機系統管理員以及其他 VM 本機使用者已新增為 DBMS 系統管理員的身分登入/使用者。
* 新增其他本機帳戶，因為這些可能需要為特定的部署案例。
* 如果 SAP NetWeaver 的安裝和重新命名的從 Azure 部署在原始名稱，主機名稱，包含圖像很可能，建議您複製到範本的 SAP 軟體佈建管理員 DVD 的最新版本。 這會讓您輕鬆地使用所提供的 SAP 重新命名功能來調整變更主機名稱及/或變更的 SAP 系統中部署 VM 圖像 SID，就會立即開始新的複本。

___

> ![Windows][Logo_Windows] Windows
>
> 請確定的磁碟機 D:\ 不是用於設定磁碟自動掛接連接的磁碟章節 [設定自動附加磁碟掛接] 中所述 [計劃-快速入門-5.5.3] 文件中。
> 
> ![Linux][Logo_Linux] Linux
>
> 請確定不會使用/mnt/資源所有磁碟會都裝載透過 uuid。 針對 OS 磁碟請確定開機載入器項目也會反映 uuid 為基礎的連接。

___

* SAP GUI (的系統管理及設定供) 可以預先安裝在這類範本。
* 只要此軟體，可以使用重新命名的 VM 可以安裝在跨內部部署案例中順利執行 Vm 需要其他軟體。

如果 VM 已準備好充分一般及最後獨立的帳戶/使用者目標 Azure 部署案例中無法使用，一般化這類圖像的最後一個準備步驟進行。

##### <a name="generalizing-a-vm"></a>一般化 VM 

___

[註解]: <> (MShermannd TODO 需要找出更好的文章 / 記錄相關一般化的手錶 Vm)
> ![Windows][Logo_Windows] Windows
>
> 最後一個步驟是 VM 以系統管理員帳戶登入。 開啟 Windows 命令視窗為 「 系統管理員]。 移至...\windows\system32\sysprep，然後執行 sysprep.exe。
> 會顯示一個小型的視窗。 請務必以核取 [（預設為取消核取） '一般化 」 選項，以及變更 [關閉] 選項預設值為 「 重新啟動 」 「 關機 」。 此程序假設執行內部部署的 VM 來賓 os sysprep 程序。
> 如果您想要執行的程序與 VM Azure 中執行，請按照[本文]所述的步驟[virtual-machines-windows-capture-image]。
> 
> ![Linux][Logo_Linux] Linux
>
> [如何擷取 Linux 虛擬機器用來作為資源管理員範本][virtual-machines-linux-capture-image]

___

### <a name="transferring-vms-and-vhds-between-on-premises-to-azure"></a>轉接 Vm 和 Vhd 內部部署至 Azure 之間
由於將 VM 圖像和磁碟上傳至 Azure 不可能透過 Azure 入口網站，您需要使用 PowerShell 的 Azure cmdlet 或 CLI。 另一個可能是工具的使用 'AzCopy'。 此工具可以內部部署和 Azure 間複製 Vhd （在兩個方向）。 它也可以複製 Azure 區域間 Vhd。 請參考[此文件][storage-use-azcopy]下載及使用方式 AzCopy。

第三個，就是使用各種不同的第三方 GUI 導向工具。 不過，請確定這些工具會支援 Azure 頁面二進位大型物件。 我們需要用於我們使用 Azure 頁面 Blob 儲存區 (差異如下所示︰ <https://msdn.microsoft.com/library/windowsazure/ee691964.aspx>)。 還有非常有效率壓縮 Vm 和 Vhd 需要上傳 Azure 所提供的工具。 這是很重要，因為此壓縮的效率減少上傳時間 （其中異繼續上傳連結至網際網路從內部部署設備和目標的 Azure 部署地區）。 是上, 傳的 VM 或到美國從歐洲位置 VHD 基礎 Azure 資料中心帶超過上傳到歐洲 Azure 資料中心的相同 Vm/Vhd 展覽假設。 

#### <a name="a43e40e6-1acc-4633-9816-8f095d5a7b6a"></a>上傳至 Azure VHD 來自內部部署
從內部部署網路上傳現有的 VM 或 VHD 這類 VM 或 VHD 必須符合需求為章節 [準備將 VM 來自內部部署移至 Azure，以非一般化光碟] 中列出 [規劃-快速入門-5.2.1] 的 [這份文件。

這類 VM 不需要加以一般化，可以上傳的狀態和圖案的內部部署一邊關閉後有中。 是一樣的其他 Vhd 其中不含任何作業系統。 

##### <a name="uploading-a-vhd-and-making-it-an-azure-disk"></a>上傳 VHD，並將之 Azure 磁碟
在此情況下，我們想要上傳 VHD，不論有 OS，並為資料磁碟 vm 連接或將其作為 OS 磁碟。 這是多重步驟的程序 

__Powershell__

* 登入您的_登入 AzureRmAccount_的訂閱
* 設定內容_設定 AzureRmContext_與參數 SubscriptionId 或 SubscriptionName 的訂閱，請參閱<https://msdn.microsoft.com/library/mt619263.aspx>
* 上傳與_新增 AzureRmVhd_ VHD，Azure 儲存體帳戶，請參閱<https://msdn.microsoft.com/library/mt603554.aspx>
* 設定 OS 磁碟上的 [新的 VM 設定為 [與_設定 AzureRmVMOSDisk_ VHD，請參閱<https://msdn.microsoft.com/library/mt603746.aspx>
* 建立新的 VM 從與_新增 AzureRmVM_ VM 設定，請參閱<https://msdn.microsoft.com/library/mt603754.aspx>
* 將資料磁碟新增至新的 [_新增 AzureRmVMDataDisk_ VM，請參閱<https://msdn.microsoft.com/library/mt603673.aspx>

__Azure CLI__

* 切換至_azure config 模式 arm_ Azure 資源管理員模式
* 登入您的訂閱，與_azure 登入_
* 選取您的訂閱，與_azure 帳戶設定`<subscription name or id`_
* 上傳的_儲存空間 azure blob 上傳_VHD，請參閱[使用 Azure CLI 與 Azure 儲存體][storage-azure-cli]
* 建立新的 VM 指定為以_azure vm 建立_參數及參數和 d 的 OS 磁碟的上傳的 VHD
* 加入新 VM 與_vm 磁碟附加新_資料磁碟

__範本__

* 上傳使用 Powershell 或 Azure CLI VHD
* 使用 JSON 範本，如下圖所示，在[此範例 JSON 範本](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-from-specialized-vhd/azuredeploy.json)參照 VHD 部署 VM。

#### <a name="deployment-of-a-vm-image"></a>VM 圖像的部署
若要上傳現有的 VM 或 VHD 從內部部署網路以將其作為 Azure VM 圖像這類 VM 或 VHD 必須符合章節 [準備的 SAP 部署客戶特定圖像 VM] 中列出的需求 [計劃-快速入門-5.2.2] 的 [這份文件。

* 在 Windows 上使用_sysprep_或_waagent-deprovision_ linux 一般化您 VM-請參閱[如何擷取 Windows 虛擬機器中的資源管理員部署模型][virtual-machines-windows-capture-image-prepare-the-vm-for-image-capture]或[如何擷取 Linux 虛擬機器用來作為資源管理員範本][virtual-machines-linux-capture-image-capture]
* 登入您的_登入 AzureRmAccount_的訂閱
* 設定內容_設定 AzureRmContext_與參數 SubscriptionId 或 SubscriptionName 的訂閱，請參閱<https://msdn.microsoft.com/library/mt619263.aspx>
* 上傳與_新增 AzureRmVhd_ VHD，Azure 儲存體帳戶，請參閱<https://msdn.microsoft.com/library/mt603554.aspx>
* 設定 OS 磁碟上的 [新的 VM 設定為 [與 VHD_設定 AzureRmVMOSDisk SourceImageUri-CreateOption fromImage_ -請參閱<https://msdn.microsoft.com/library/mt603746.aspx>
* 建立新的 VM 從與_新增 AzureRmVM_ VM 設定，請參閱<https://msdn.microsoft.com/library/mt603754.aspx>

__Azure CLI__

* 在 Windows 上使用_sysprep_或_waagent-deprovision_ linux 一般化您 VM-請參閱[如何擷取 Windows 虛擬機器中的資源管理員部署模型][virtual-machines-windows-capture-image-prepare-the-vm-for-image-capture]或[如何擷取 Linux 虛擬機器用來作為資源管理員範本][ virtual-machines-linux-capture-image-capture] Linux 的
* 切換至_azure config 模式 arm_ Azure 資源管理員模式
* 登入您的訂閱，與_azure 登入_
* 選取您的訂閱，與_azure 帳戶設定`<subscription name or id`_
* 上傳的_儲存空間 azure blob 上傳_VHD，請參閱[使用 Azure CLI 與 Azure 儲存體][storage-azure-cli]
* 建立新的 VM 指定為以_azure vm 建立_參數及參數的 OS 磁碟的上傳的 VHD-Q

__範本__

* 在 Windows 上使用_sysprep_或_waagent-deprovision_ linux 一般化您 VM-請參閱[如何擷取 Windows 虛擬機器中的資源管理員部署模型][virtual-machines-windows-capture-image-prepare-the-vm-for-image-capture]或[如何擷取 Linux 虛擬機器用來作為資源管理員範本][ virtual-machines-linux-capture-image-capture] Linux 的
* 上傳使用 Powershell 或 Azure CLI VHD
* 部署 VM JSON 範本參照 VHD 如下圖所示，在[這個範例 JSON 範本](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-from-user-image/azuredeploy.json)中的圖像。

#### <a name="downloading-vhds-to-on-premises"></a>下載 Vhd 內部部署
Azure 基礎結構服務不是單向的只無法上傳 Vhd 和 SAP 系統。 您可以移動 SAP 系統從 Azure 回內部部署世界各地。

下載的期間 Vhd 無法作用中。 即使下載至 Vm 會裝載的 Vhd VM 必須關閉。 如果您只要下載資料庫內容的然後應該會用來設定新的系統內部部署和是否可接受的期間下載的時間，以及新的系統仍可以操作 Azure 中的系統的設定，您可能長停機時間，以避免將 VHD 執行壓縮的資料庫備份，只要下載，而不是也下載 OS 基底 VM 該 VHD。

#### <a name="powershell"></a>Powershell

當 SAP 系統已停止 VM 已關閉，您可以使用內部部署目標中的 PowerShell 指令程式儲存 AzureRmVhd 回內部部署全球下載 VHD 磁碟。 才能執行，您需要的 VHD 您可以在中找到 URL Azure 入口網站 （需要瀏覽至該儲存帳戶並建立 VHD 存放容器） ' 儲存區段 」，您必須知道應 VHD 複製到。

然後您可以利用 [] 命令，只定義為 VHD （包括其名稱） 的實際位置的 URL 下載 VHD 然後 LocalFilePath 參數 SourceUri。 命令可能看起來像︰

```powerhell
Save-AzureRmVhd -ResourceGroupName <resource group name of storage account> -SourceUri http://<storage account name>.blob.core.windows.net/<container name>/sapidedata.vhd -LocalFilePath E:\Azure_downloads\sapidesdata.vhd
```

如需詳細的儲存 AzureRmVhd cmdlet 的詳細資訊，請檢查以下<https://msdn.microsoft.com/library/mt622705.aspx>。 

#### <a name="cli"></a>CLI

SAP 系統已停止並 VM 已關閉，您可以使用內部部署目標 Azure CLI 命令儲存 azure blob 下載至內部部署全球下載 VHD 磁碟。 若要執行這項作業，您需要名稱並 VHD 您可以 Azure 入口網站 （需要瀏覽至該儲存帳戶並建立 VHD 存放容器） 的儲存空間區段中找到的容器，而且您必須知道應 VHD 複製到。

然後您可以利用] 命令，只需定義參數 blob 和 VHD 的容器下載並目的地為 VHD （包括其名稱） 的實體目標位置。 命令可能看起來像︰

```
azure storage blob download --blob <name of the VHD to download> --container <container of the VHD to download> --account-name <storage account name of the VHD to download> --account-key <storage account key> --destination <destination of the VHD to download> 
```

### <a name="transferring-vms-and-vhds-within-azure"></a>轉接 Vm 和 Azure 中 Vhd

#### <a name="copying-sap-systems-within-azure"></a>複製 Azure 中的 SAP 系統

SAP 系統或甚至專用的 DBMS 伺服器支援 SAP 應用程式層可能包含多個 Vhd 其中包含 [使用的二進位檔案 OS 或 SAP 資料庫的資料和記錄檔。 不的複製 Vhd Azure 功能和 Azure Vhd 儲存到磁碟的功能沒有想同步快照多個 Vhd 同步處理機制。 因此，複製或儲存 Vhd 狀態即使那些已針對相同 VM 裝載會不同。 這表示，有不同的資料和 logfile(s) 不同 Vhd 中所包含的具體的大小寫，在資料庫中結尾可能不一致。 

**結束︰ 若要複製或儲存 Vhd 的一部分的 SAP 系統設定您需要停止 SAP 系統，而且也需要關閉部署 VM。只有您可以複製或下載 Vhd 建立一份 SAP 系統 Azure 或內部部署中的設定。**

資料磁碟會儲存為 VHD Azure 儲存體帳戶中的檔案，並可直接虛擬機器中附加或做為圖像。 在此情況下，VHD 會複製到另一個位置 beeing 附加至虛擬機器之前。 Azure 中 VHD 檔案的完整名稱中必須是唯一 Azure。 如先前已經所述，名稱會是一種看起來像的三個部分名稱︰ 

    http(s)://<storage account name>.blob.core.windows.net/<container name>/<vhd name>

##### <a name="powershell"></a>Powershell
您可以使用 Azure PowerShell cmdlet 來複製 VHD，[本文]中所示[storage-powershell-guide-full-copy-vhd]。

##### <a name="cli"></a>CLI
您可以使用 Azure CLI 複製 VHD[本文]中所示[storage-azure-cli-copy-blobs]

##### <a name="azure-storage-tools"></a>Azure 儲存工具

* <http://azurestorageexplorer.codeplex.com/releases/view/125870>

此外，還有一些專業版本，就可以在這裡找到 Azure 儲存瀏覽器︰

* <http://www.cerebrata.com/>
* <http://clumsyleaf.com/products/cloudxplorer> 


儲存帳戶內本身 VHD 複本是採用只有在幾秒鐘 （類似寫入建立快速查詢延遲複製] 和 [複製舊硬體） 程序。 有一份複本之後的 VHD 檔案，您可以附加至虛擬機器或將其作為圖像來虛擬機器中附加 VHD 複本。

##### <a name="powershell"></a>Powershell

```powershell
# attach a vhd to a vm
$vm = Get-AzureRmVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzureRmVMDataDisk -VM $vm -Name newdatadisk -VhdUri <path to vhd> -Caching <caching option> -DiskSizeInGB $null -Lun <lun e.g. 0> -CreateOption attach
$vm | Update-AzureRmVM

# attach a copy of the vhd to a vm
$vm = Get-AzureRmVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzureRmVMDataDisk -VM $vm -Name newdatadisk -VhdUri <new path of vhd> -SourceImageUri <path to image vhd> -Caching <caching option> -DiskSizeInGB $null -Lun <lun e.g. 0> -CreateOption fromImage
$vm | Update-AzureRmVM
```
##### <a name="cli"></a>CLI
```
azure config mode arm 

# attach a vhd to a vm
azure vm disk attach <resource group name> <vm name> <path to vhd>

# attach a copy of the vhd to a vm
# this scenario is currently not possible with Azure CLI. A workaround is to manually copy the vhd to the destination.
```

#### <a name="9789b076-2011-4afa-b2fe-b07a8aba58a1"></a>複製 Azure 儲存體帳戶之間的磁碟
Azure 入口網站上無法執行這項工作。 您可以 ise [以系統 Azure PowerShell cmdlet、 Azure CLI 或協力廠商儲存空間瀏覽器。 PowerShell cmdlet 或 CLI 命令可以建立及管理二進位大型物件，包括複製的能力非同步 blob 儲存體帳戶和 Azure 訂閱中的區域。

##### <a name="powershell"></a>Powershell 

訂閱方案之間複製 Vhd，也可以。 如需詳細資訊，請閱讀[文章][storage-powershell-guide-full-copy-vhd]。 

PS cmdlet 邏輯的基本流程看起來像這樣︰

* 建立與_新增 AzureStorageContext_的儲存空間帳戶內容來源儲存帳戶，請參閱<https://msdn.microsoft.com/library/dn806380.aspx>
* 建立_新增 AzureStorageContext_目標儲存帳戶的儲存空間帳戶內容，請參閱<https://msdn.microsoft.com/library/dn806380.aspx>
* 啟動與複本

```powershell
Start-AzureStorageBlobCopy -SrcBlob <source blob name> -SrcContainer <source container name> -SrcContext <variable containing context of source storage account> -DestBlob <target blob name> -DestContainer <target container name> -DestContext <variable containing context of target storage account>
```

* 檢查與迴圈中複製的狀態
 
```powershell
Get-AzureStorageBlobCopyState -Blob <target blob name> -Container <target container name> -Context <variable containing context of target storage account>
```

* 如上所述，請將新 VHD 附加至虛擬機器中。

如需範例，請參閱[本文][storage-powershell-guide-full-copy-vhd]

##### <a name="cli"></a>CLI
* 啟動與複本

```
  azure storage blob copy start --source-blob <source blob name> --source-container <source container name> --account-name <source storage account name> --account-key <source storage account key> --dest-container <target container name> --dest-blob <target blob name> --dest-account-name <target storage account name> --dest-account-key <target storage account name>
```

* 如果核取狀態的迴圈中的複本

```
azure storage blob copy show --blob <target blob name> --container <target container name> --account-name <target storage account name> --account-key <target storage account name>
```
  
* 如上所述，請將新 VHD 附加至虛擬機器中。

如需範例，請參閱[本文][storage-azure-cli-copy-blobs]

### <a name="disk-handling"></a>磁碟處理
#### <a name="4efec401-91e0-40c0-8e64-f2dceadff646"></a>SAP 部署 VM/VHD 結構
理想的結構 VM 及相關聯的 Vhd 處理應該非常簡單。 在內部部署安裝客戶開發的組織結構伺服器安裝多種方式。 

* 一個包含 OS 及所有的二進位的 DBMS 及/或 SAP 基底 VHD。 自年 3 月 2015年此 VHD 可以是 1 TB 的大小，而不是舊版限於 127 GB 的限制。 
* 一個或多個 Vhd （如果 DBMS 支援此），包含 DBMS SAP 資料庫的記錄檔和 [DBMS temp 儲存空間] 區域的記錄檔。 如果資料庫記錄 IOPS 需求很多，您需要等量磁碟區才能達到 IOPS 音量所需的多個 Vhd。 
* Vhd 包含一或兩個資料庫檔案的 SAP 資料庫及 DBMS temp 資料檔案 （如果 DBMS 支援此） 的數字。

![參照設定的 SAP 的 Azure IaaS VM][planning-guide-figure-1300]

[註解]: <> (MShermannd TODO 描述 Linux 結構)

___

> ![Windows][Logo_Windows] Windows
>
> 與多個客戶我們看到設定，例如 SAP 和 DBMS 的二進位檔案未安裝 c:\ 磁碟機上安裝作業系統。 有各種原因，但當我們回到根目錄，通常是︰ 在磁碟機都小，和 OS 升級所需的額外空間 10-15 幾年前。 兩個條件不會套用這些天太通常不再。 今天可在大量磁碟或 Vm 對應 c:\ 磁碟機。 若要簡化部署其結構中，建議追蹤 SAP NetWeaver 系統 Azure 中的下列部署模式
>
> Windows 作業系統分頁檔應該是 d 磁碟機上 （非持續磁碟） 
> 
> ![Linux][Logo_Linux] Linux
>
> [本文]所述 /mnt/mnt/資源之下 Linux 交換檔置於 Linux[virtual-machines-linux-agent-user-guide]。 您可以 Linux 代理程式 /etc/waagent.conf 的設定檔中設定交換檔案。 新增或變更下列設定︰

```
ResourceDisk.EnableSwap=y
ResourceDisk.SwapSizeMB=30720
```

若要啟動所做的變更，您必須重新啟動與 Linux 代理程式

```
sudo service waagent restart
```

請閱讀 SAP 筆記[1597355]如需詳細資訊的建議的交換檔案大小

___

用於 DBMS 資料檔案和 Azure 儲存體這些 Vhd 裝載的類型 Vhd 數應該取決於 IOPS 需求及所需的延遲。 [本文]中所描述的確切的配額[virtual-machines-sizes]

SAP 部署在過去 2 年的體驗教我們一些教學單元的可摘要為︰

* 不同的資料檔案的 IOPS 流量不一定相同由於現有客戶系統可能會有不同大小代表其 SAP 資料庫的資料檔案。 如此一來試驗更有效地使用 RAID 設定在多個 Vhd 放置 Lun 劃分不相關的資料檔案。 沒有情況下，特別是有 Azure 標準存放位置的 IOPS 率叫用單一 VHD 與 DBMS 交易記錄檔的配額。 在此情況下建議使用進階版儲存空間或或者彙總多個標準儲存 Vhd RAID 軟體。

___

> ![Windows][Logo_Windows] Windows
>
> * [效能最佳作法 SQL Server Azure 虛擬機器中的商務連絡人][virtual-machines-sql-server-performance-best-practices]
> 
> ![Linux][Logo_Linux] Linux
>
> * [設定軟體襲擊 Linux][virtual-machines-linux-configure-raid]
> * [設定 LVM Linux VM Azure 中][virtual-machines-linux-configure-lvm]
> * [Azure 儲存密碼和 Linux I/O 最佳化](http://blogs.msdn.com/b/igorpag/archive/2014/10/23/azure-storage-secrets-and-linux-i-o-optimizations.aspx)

___

* 進階版儲存為顯示重要較佳的效能，特別是的要徑交易寫入記錄。 進行像效能生產預期的 SAP 情況下，建議使用可以運用 Azure 進階版儲存空間的 VM 數列。

請記住，其中包含 OS，VHD 和建議，SAP 的二進位檔案以及資料庫 (基底 VM)，而不不再限於 127 GB。 它現在可以有 1 TB 的大小。 應該是空間不足，無法保留例如包括 SAP 批次工作記錄所有必要的檔案。

如需更多建議和特別的 DBMS Vm 的其他詳細資料請參考 [DBMS 部署指南] [dbms 輔助線]


#### <a name="disk-handling"></a>磁碟處理
在大多數情況下，您需要將 SAP 資料庫部署到 VM 建立額外的磁碟。 我們討論的考量事項章節 [SAP 部署 VM/VHD 結構] 中的數字的 Vhd 上 [計劃-快速入門-5.5.1] 的 [這份文件。 Azure 入口網站可讓附加及部署基底 VM 後卸離磁碟。 附加/卸離 VM 已啟動和執行以及它停止時，可以是磁碟。 當附加光碟，Azure 入口網站提供附加空的磁碟或現有的硬碟，目前不附加到另一個 VM。 

**附註**︰ Vhd 僅可附加一個 VM 在任何時間。
 
![附加/中斷連結 Azure 標準的儲存空間的磁碟][planning-guide-figure-1400]

您必須決定您是否要建立新和空白 VHD （由想要做為基底 VM 位於相同的儲存空間帳戶中建立），或您是否要選取現有的 VHD 上傳的時間較舊版本，應該現在附加至 VM。 

**重要事項**︰ 您**不**想要使用主機快取 Azure 標準的儲存空間。 您應該保留預設的 [無主機快取喜好設定。 含有 Azure 進階版存放您應該啟用閱讀快取如果 I/O 特性讀取等資料庫資料檔案的一般 I/O 流量。 資料庫交易記錄檔若無快取建議。

___

> ![Windows][Logo_Windows] Windows
>
> [如何將附加 Azure 入口網站中的資料磁碟][virtual-machines-windows-attach-disk-portal]
>
> 如果附加磁碟，必須先開啟 [Windows 磁碟管理員 VM 到登入。 如果在 [設定自動附加磁碟掛接] 章節中的建議，不會啟用自動掛接 [計劃-快速入門-5.5.3]，將新附加的音量需要採取 online 和初始化。
>
> ![Linux][Logo_Linux] Linux
>
> 附加磁碟，如果您需要登入到 VM 與[本文]所述，初始化磁碟[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]

___

新的磁碟是空的磁碟，如果您需要設定格式的磁碟以及。 格式設定，特別是 DBMS 資料和記錄檔的相同建議的 DBMS 答對金屬部署與適用於。

已經說明過章 [Microsoft Azure 虛擬機器概念] [計劃-快速入門-3.2 捨位] 中，Azure 儲存體帳戶並不提供無限資源量 I/O IOPS 和資料的音量。 通常 DBMS Vm 是最受影響。 最好的每個 VM 使用不同的儲存空間帳戶，如果您有幾個 I/O 大量 Vm 部署才能保持 Azure 儲存體帳戶大量的限制。 否則，您需要查看如何取得平衡這些 Vm 之間不同的儲存空間帳戶，而不叫用的每個單一儲存帳戶的限制。 更多詳細資料討論 [DBMS 部署指南] [dbms 輔助線]。 您也應該請記住，純粹 SAP 應用程式伺服器 Vm 或其他 Vm 最後可能需要額外 Vhd 這些限制。

這是儲存帳戶的相關的其他主題是儲存帳戶 Vhd 是否已準備地理複寫。 啟用或停用儲存帳戶層級，而不是在 VM 層級地理複寫。 如果啟用地理複寫，儲存帳戶內 Vhd 會複製到相同的區域內的另一個 Azure 資料中心。 在決定之前的說明，請考慮下列限制︰

Azure 地理複寫 VM 中每個 VHD 本機處理，並不會依時間順序 IOs 複寫跨多個 Vhd VM 中。 因此，表示底數 VM VHD，以及附加至 VM 任何其他 Vhd 複寫個別獨立。 這表示在不同的 Vhd 變更之間沒有同步。 亦其寫入所代表的意義的順序複寫的 IOs 交易的地理複寫不是值的有分佈多個 Vhd 其資料庫的資料庫伺服器。 除了 DBMS，也可能會有其他應用程式處理程序撰寫或管理在不同的 Vhd 和重要變更的順序是資料的位置。 如果這是要求時，不應該啟用地理複寫 Azure 中。 取決於您是否需要或另一組一組 Vm，但不是想地理複寫，您可以已分類 Vm 和其相關的 Vhd 到不同的儲存空間帳戶的地理複寫啟用或停用。

#### <a name="17e0d543-7e8c-4160-a7da-dd7117a1ad9d"></a>設定自動掛接附加磁碟

___


> ![Windows][Logo_Windows] Windows
> 
> 若是 Vm 會建立自己的圖像或磁碟，則需要檢查，可能是設定自動掛接參數。 設定此參數，可讓 VM 後重新啟動或重新部署 Azure 一次自動裝載附加/裝載磁碟機中。 
> 參數是設定由 Microsoft Azure Marketplace 提供的圖像。
>
> 若要設定自動掛接，請選取命令列可執行 diskpart.exe 以下文的件︰ 
> 
> * [DiskPart 命令列選項](https://technet.microsoft.com/library/cc766465.aspx)
> * [自動掛接](http://technet.microsoft.com/library/cc753703.aspx)
> 
> 應該以系統管理員身分開啟命令列視窗。
> 
> 如果附加磁碟，必須先開啟 [Windows 磁碟管理員 VM 到登入。 如果在 [設定自動附加磁碟掛接] 章節中的建議，不會啟用自動掛接 [計劃-快速入門-5.5.3]，新附加的音量 > 需要採取 online 和初始化。
>
> ![Linux][Logo_Linux] Linux
>
> 您需要初始化[本文]所述的新連結的空白磁碟[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]。
> 您也需要新增新的磁碟到 /etc/fstab。

___


### <a name="final-deployment"></a>最終的部署
最終的部署及確切的步驟，能夠部署 SAP 延伸監視，請參閱 [部署指南] [部署指南]。

## <a name="accessing-sap-systems-running-within-azure-vms"></a>存取 SAP 系統中 Azure Vm 執行
僅雲端的情況下，您可能會想要使用 SAP GUI 公用網際網路連線這些 SAP 系統。 這種情況下，下列程序必須套用。

我們將文件稍後討論的主要案例，連線至 SAP 系統中跨內部部署，必須是網站-連線 （VPN 通道） 或內部部署系統和 Azure 系統之間的 Azure ExpressRoute 連線。


### <a name="remote-access-to-sap-systems"></a>遠端存取 SAP 系統

使用 Azure 資源管理員沒有預設端點不再像離職傳統模型中。 所有的連接埠的 Azure ARM VM 已開啟，只要︰

1. 沒有網路安全性群組定義子網路或網路介面。 Azure Vm 網路流量可以受到保護，透過所謂 「 網路安全性群組 」。 如需詳細資訊，請參閱[什麼是網路安全性群組 (NSG)？][virtual-networks-nsg]
1. 沒有 Azure 負載平衡器定義網路介面   
 
[本文]所述，請參閱架構與之間的差異傳統模型 ARM[virtual-machines-azure-resource-manager-architecture]。
 
#### <a name="configuration-of-the-sap-system-and-sap-gui-connectivity-for-cloud-only-scenario"></a>雲端專用的案例的 SAP 系統和 SAP GUI 連線的設定
本文說明此主題的詳細資訊，請參閱︰ <http://blogs.msdn.com/b/saponsqlserver/archive/2014/06/24/sap-gui-connection-closed-when-connecting-to-sap-system-in-azure.aspx> 

#### <a name="changing-firewall-settings-within-vm"></a>變更內 VM 的防火牆設定
可能需要在您的虛擬機器允許輸入的流量 SAP 系統上設定防火牆。 

___

> ![Windows][Logo_Windows] Windows
>
> 根據預設，Azure 的部署 VM 內的 [Windows 防火牆已開啟。 您現在必須允許開啟 SAP 連接埠，否則 SAP GUI 無法連線。
> 若要執行這項操作︰
>
>  * 控制開啟 Panel\System 和 Security\Windows 防火牆 ' 進階設定]。
>  * 現在以滑鼠右鍵按一下電子郵件地址]，選擇 [新增規則] 輸入規則。
>  * 在 [建立新的 「 連接埠 」 規則下列精靈選擇。
>  * 在精靈的下一個步驟中，讓您想要開啟的連接埠號碼 TCP]，然後輸入的設定。 因為我們 SAP 執行個體識別碼 00，我們會花費 3200。 如果您執行個體有不同的執行個體數字，則應該開啟您定義較舊版本執行個體數字為基礎的連接埠。
>  * 在精靈的下一個部分中，您需要離開的 「 允許連線] 核取的項目。
>  * 在精靈的下一個步驟中，您需要定義是否將規則套用網域私人與公用網路。 請視需要調整，以您的需求。 不過，從外部透過公用網路連線與 SAP GUI，必須先將規則套用至公用網路。
>  * 在精靈的最後一個步驟中，您需要輸入規則的名稱，然後再按下 [完成] 儲存規則
>
>  規則會立即生效。
>
> ![連接埠規則定義][planning-guide-figure-1600]
>
> ![Linux][Logo_Linux] Linux
>
> 在 Azure Marketplace Linux 圖像預設不啟用 iptables 防火牆，然後連線到您的 SAP 系統應該使用。 如果您啟用 iptables 或另一個防火牆，請參閱 iptables 或使用的防火牆，允許輸入的 tcp 連接埠 32xx 流量 （xx 是 SAP 系統的系統數） 的文件。 

___

#### <a name="security-recommendations"></a>安全性的建議

SAP GUI 不會立即連線至 SAP 執行個體 (連接埠 32xx) 執行，但第一次連線至 SAP 郵件伺服器程序 (連接埠 36xx) 開啟的連接埠透過任一。 過去的訊息伺服器已使用相同的連接埠應用程式執行個體內部的通訊。 若要防止內部部署的應用程式伺服器不小心通訊與郵件伺服器中 Azure 可以變更內部通訊的連接埠。 強烈建議您將內部和之間的通訊 SAP 郵件伺服器的應用程式執行個體變更為不同的連接埠號碼，系統已將其複製從內部部署系統，例如開發的專案測試等的複本。這可以使用預設設定檔參數︰

>   rdisp/msserv_internal

如詳加說明︰ <https://help.sap.com/saphelp_nwpi71/helpdata/en/47/c56a6938fb2d65e10000000a42189c/content.htm> 

## <a name="96a77628-a05e-475d-9df3-fb82217e8f14"></a>僅供雲端部署的 SAP 執行個體的概念

### <a name="3e9c3690-da67-421a-bc3f-12c520d99a30"></a>單一 VM 與 SAP NetWeaver 示範訓練案例
 
![執行的單一 VM SAP 示範系統相同 VM 名稱，隔離在 Azure 雲端服務][planning-guide-figure-1700]

在這個案例中 （請參閱 [雲端專用] [計劃-快速入門-2.1] 此文件的章節） 我們實作單一 VM 位置包含完整的訓練課程示範案例的一般的訓練課程示範系統案例。 我們假設部署經由 VM 圖像範本。 我們也假設這些示範/單元中的多個 Vm 需要部署 vm 有相同的名稱。

假設是章 [準備和 Azure 的 SAP Vm] 的某些節所述，建立 VM 圖像 [計劃-快速入門-5.2] 文件中。

實作案例的事件順序看起來像這樣︰

[註解]: <> (MShermannd TODO 必須提供手臂範例說明使用 json 範本 + 釐清有關手臂虛擬網路中的唯一 VM 名稱)   
##### <a name="powershell"></a>Powershell

* 建立新的資源群組的每個訓練/示範橫向

```powershell
$rgName = "SAPERPDemo1"
New-AzureRmResourceGroup -Name $rgName -Location "North Europe"
```

* 建立新的儲存空間帳戶

```powershell
$suffix = Get-Random -Minimum 100000 -Maximum 999999
$account = New-AzureRmStorageAccount -ResourceGroupName $rgName -Name "saperpdemo$suffix" -SkuName Standard_LRS -Kind "Storage" -Location "North Europe"
```

* 建立新的虛擬網路的每個訓練/示範橫向，若要啟用的相同主機名稱及 IP 位址的使用方式。 虛擬網路受到網路安全性群組，只允許 SSH 連接埠 3389 啟用遠端桌面存取和連接埠 22 流量。 

```powershell
# Create a new Virtual Network
$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name SAPERPDemoNSGRDP -Protocol * -SourcePortRange * -DestinationPortRange 3389 -Access Allow -Direction Inbound -SourceAddressPrefix * -DestinationAddressPrefix * -Priority 100
$sshRule = New-AzureRmNetworkSecurityRuleConfig -Name SAPERPDemoNSGSSH -Protocol * -SourcePortRange * -DestinationPortRange 22 -Access Allow -Direction Inbound -SourceAddressPrefix * -DestinationAddressPrefix * -Priority 101
$nsg = New-AzureRmNetworkSecurityGroup -Name SAPERPDemoNSG -ResourceGroupName $rgName -Location  "North Europe" -SecurityRules $rdpRule,$sshRule

$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name Subnet1 -AddressPrefix  10.0.1.0/24 -NetworkSecurityGroup $nsg
$vnet = New-AzureRmVirtualNetwork -Name SAPERPDemoVNet -ResourceGroupName $rgName -Location "North Europe"  -AddressPrefix 10.0.1.0/24 -Subnet $subnetConfig
```

* 建立新的公用 IP 位址可以用來從網際網路存取虛擬機器

```powershell
# Create a public IP address with a DNS name
$pip = New-AzureRmPublicIpAddress -Name SAPERPDemoPIP -ResourceGroupName $rgName -Location "North Europe" -DomainNameLabel $rgName.ToLower() -AllocationMethod Dynamic
```

* 建立新的網路介面虛擬機器

```powershell 
# Create a new Network Interface
$nic = New-AzureRmNetworkInterface -Name SAPERPDemoNIC -ResourceGroupName $rgName -Location "North Europe" -Subnet $vnet.Subnets[0] -PublicIpAddress $pip 
```

* 建立虛擬機器。 雲端專用案例的每個 VM 會有相同的名稱。 SAP SID SAP NetWeaver 中的執行個體這些 Vm 都一樣，也。 在 Azure 資源] 群組中，VM 名稱必須是唯一的但您可以在不同的 Azure 資源群組執行 Vm 使用相同的名稱。 預設的 「 系統管理員] 帳戶在 Windows 或 Linux 的 「 根不是有效的。 因此，新的系統管理員使用者名稱，必須定義與密碼。 若要定義也必須 VM 的大小。

```powershell
#####
# Create a new virtual machine with an official image from the Azure Marketplace
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzureRmVMConfig -VMName SAPERPDemo -VMSize Standard_D11

# select image
$vmconfig = Set-AzureRmVMSourceImage -VM $vmconfig -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer" -Skus "2012-R2-Datacenter" -Version "latest"
$vmconfig = Set-AzureRmVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
# $vmconfig = Set-AzureRmVMSourceImage -VM $vmconfig -PublisherName "SUSE" -Offer "SLES" -Skus "12" -Version "latest"
# $vmconfig = Set-AzureRmVMSourceImage -VM $vmconfig -PublisherName "RedHat" -Offer "RHEL" -Skus "7.2" -Version "latest"
# $vmconfig = Set-AzureRmVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vmconfig = Add-AzureRmVMNetworkInterface -VM $vmconfig -Id $nic.Id

$diskName="os"
$osDiskUri=$account.PrimaryEndpoints.Blob.ToString() + "vhds/" + $diskName  + ".vhd"
$vmconfig = Set-AzureRmVMOSDisk -VM $vmconfig -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage
$vm = New-AzureRmVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

```powershell
#####
# Create a new virtual machine with a VHD that contains the private image that you want to use
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzureRmVMConfig -VMName SAPERPDemo -VMSize Standard_D11

$vmconfig = Add-AzureRmVMNetworkInterface -VM $vmconfig -Id $nic.Id

$diskName="osfromimage"
$osDiskUri=$account.PrimaryEndpoints.Blob.ToString() + "vhds/" + $diskName  + ".vhd"

$vmconfig = Set-AzureRmVMOSDisk -VM $vmconfig -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri <path to VHD that contains the OS image> -Windows
$vmconfig = Set-AzureRmVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred
#$vmconfig = Set-AzureRmVMOSDisk -VM $vmconfig -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri <path to VHD that contains the OS image> -Linux
#$vmconfig = Set-AzureRmVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vm = New-AzureRmVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

* 您也可以新增其他磁碟與還原必要的內容。 請注意，所有 blob 名稱 (Url blob) 中必須都是唯一 Azure。

```powershell
# Optional: Attach additional data disks
$vm = Get-AzureRmVM -ResourceGroupName $rgName -Name SAPERPDemo
$dataDiskUri = $account.PrimaryEndpoints.Blob.ToString() + "vhds/datadisk.vhd"
Add-AzureRmVMDataDisk -VM $vm -Name datadisk -VhdUri $dataDiskUri -DiskSizeInGB 1023 -CreateOption empty | Update-AzureRmVM
```

##### <a name="cli"></a>CLI

在下列程式碼可以用於 Linux。 Windows 上，請使用上方所述的 PowerShell 或調整，而不是 $rgName 使用 %rgname%，並設定環境變數，使用 [Windows] 命令，_設定_的範例。

* 建立新的資源群組的每個訓練/示範橫向

```
rgName=SAPERPDemo1
rgNameLower=saperpdemo1
azure group create $rgName "North Europe"
```

* 建立新的儲存空間帳戶

```
azure storage account create --resource-group $rgName --location "North Europe" --kind Storage --sku-name LRS $rgNameLower
```

* 建立新的虛擬網路的每個訓練/示範橫向，若要啟用的相同主機名稱及 IP 位址的使用方式。 虛擬網路受到網路安全性群組，只允許 SSH 連接埠 3389 啟用遠端桌面存取和連接埠 22 流量。 

```
azure network nsg create --resource-group $rgName --location "North Europe" --name SAPERPDemoNSG
azure network nsg rule create --resource-group $rgName --nsg-name SAPERPDemoNSG --name SAPERPDemoNSGRDP --protocol \* --source-address-prefix \* --source-port-range \* --destination-address-prefix \* --destination-port-range 3389 --access Allow --priority 100 --direction Inbound
azure network nsg rule create --resource-group $rgName --nsg-name SAPERPDemoNSG --name SAPERPDemoNSGSSH --protocol \* --source-address-prefix \* --source-port-range \* --destination-address-prefix \* --destination-port-range 22 --access Allow --priority 101 --direction Inbound

azure network vnet create --resource-group $rgName --name SAPERPDemoVNet --location "North Europe" --address-prefixes 10.0.1.0/24
azure network vnet subnet create --resource-group $rgName --vnet-name SAPERPDemoVNet --name Subnet1 --address-prefix 10.0.1.0/24 --network-security-group-name SAPERPDemoNSG
```

* 建立新的公用 IP 位址可以用來從網際網路存取虛擬機器

```
azure network public-ip create --resource-group $rgName --name SAPERPDemoPIP --location "North Europe" --domain-name-label $rgNameLower --allocation-method Dynamic
```

* 建立新的網路介面虛擬機器

```
azure network nic create --resource-group $rgName --location "North Europe" --name SAPERPDemoNIC --public-ip-name SAPERPDemoPIP --subnet-name Subnet1 --subnet-vnet-name SAPERPDemoVNet 
```

* 建立虛擬機器。 雲端專用案例的每個 VM 會有相同的名稱。 SAP SID SAP NetWeaver 中的執行個體這些 Vm 都一樣，也。 在 Azure 資源] 群組中，VM 名稱必須是唯一的但您可以在不同的 Azure 資源群組執行 Vm 使用相同的名稱。 預設的 「 系統管理員] 帳戶在 Windows 或 Linux 的 「 根不是有效的。 因此，新的系統管理員使用者名稱，必須定義與密碼。 若要定義也必須 VM 的大小。

```
azure vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nic-name SAPERPDemoNIC --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest --os-type Windows --admin-username <username> --admin-password <password> --vm-size Standard_D11 --os-disk-vhd https://$rgNameLower.blob.core.windows.net/vhds/os.vhd --disable-boot-diagnostics
# azure vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nic-name SAPERPDemoNIC --image-urn SUSE:SLES:12:latest --os-type Linux --admin-username <username> --admin-password <password> --vm-size Standard_D11 --os-disk-vhd https://$rgNameLower.blob.core.windows.net/vhds/os.vhd --disable-boot-diagnostics
# azure vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nic-name SAPERPDemoNIC --image-urn RedHat:RHEL:7.2:latest --os-type Linux --admin-username <username> --admin-password <password> --vm-size Standard_D11 --os-disk-vhd https://$rgNameLower.blob.core.windows.net/vhds/os.vhd --disable-boot-diagnostics
```

```
#####
# Create a new virtual machine with a VHD that contains the private image that you want to use
#####
azure vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nic-name SAPERPDemoNIC --os-type Windows --admin-username <username> --admin-password <password> --vm-size Standard_D11 --os-disk-vhd https://$rgNameLower.blob.core.windows.net/vhds/os.vhd -Q <path to image vhd> --disable-boot-diagnostics
#azure vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nic-name SAPERPDemoNIC --os-type Linux --admin-username <username> --admin-password <password> --vm-size Standard_D11 --os-disk-vhd https://$rgNameLower.blob.core.windows.net/vhds/os.vhd -Q <path to image vhd> --disable-boot-diagnostics
```

* 您也可以新增其他磁碟與還原必要的內容。 請注意，所有 blob 名稱 (Url blob) 中必須都是唯一 Azure。

```
# Optional: Attach additional data disks
azure vm disk attach-new --resource-group $rgName --vm-name SAPERPDemo --size-in-gb 1023 --vhd-name datadisk
```

##### <a name="template"></a>範本
您可以使用 [範例範本上 github azure-快速入門-範本存放庫。

* [簡單的 Linux VM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
* [簡單的 Windows VM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
* [VM 從的圖像](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

### <a name="implement-a-set-of-vms-which-need-to-communicate-within-azure"></a>實作 Vm 需要通訊 Azure 中的一組
這個雲端專用的案例是常見的案例訓練課程及示範用途的位置代表示範/訓練課程的軟體案例分散到多個 Vm。 彼此需要安裝不同 Vm 不同元件。 同樣地，在這個案例中沒有內部部署網路通訊，或需要跨內部部署案例。

這種情況下是安裝章節 [SAP NetWeaver 示範訓練案例與單一 VM] 所述的延伸 [計劃-指南-7.1] 的 [這份文件。 在此情況下的虛擬機器便會新增至現有的資源群組。 在下列範例訓練橫向組成 SAP ASCS/SCS VM 執行 DBMS 和 SAP 應用程式伺服器執行個體 VM VM。

建立這個案例中之前必須考量下列事項已經之前情境中執行基本的設定。

#### <a name="resource-group-and-virtual-machine-naming"></a>資源群組 」 和 「 虛擬機器命名
資源群組的所有名稱都必須都是唯一的。 開發您自己的命名配置，您的資源，例如`<rg-name`>-後置字元。 

虛擬機器名稱必須是唯一的 [資源] 群組中。 

#### <a name="setup-network-for-communication-between-the-different-vms"></a>設定網路不同 Vm 之間的通訊
 
![Azure 虛擬網路內部的 Vm 一組][planning-guide-figure-1900]

若要避免命名衝突複製的相同的訓練課程/示範環境中，您需要建立的每個橫向 Azure 虛擬網路。 將由 Azure 提供 DNS 名稱解析，或您可以設定您自己的 DNS 伺服器外 Azure （不以進一步討論以下）。 在這個案例中我們不設定自己的 DNS。 Azure 虛擬網路內所有的虛擬機器會啟用透過 hostname 通訊。 

分隔虛擬網路，不只是資源群組訓練或示範環境的原因可能是︰

* 設定為 SAP 橫向自己 AD/OpenLDAP，並且網域伺服器需要每個環境的一部分。  
* 設定為 SAP 橫向具有需要使用固定的 IP 位址的元件。

Azure 虛擬網路，以及如何定義其相關的更多詳細資料，請參閱[本文][virtual-networks-create-vnet-arm-pportal]。

## <a name="deploying-sap-vms-with-corporate-network-connectivity-cross-premises"></a>部署 SAP Vm 與公司網路連線 （交互內部部署）

您執行 SAP 橫向，並且想要除以之間高階 DBMS 伺服器的答對金屬部署，內部部署虛擬化環境的應用程式層級和較小的 2 層設定 SAP 系統和 Azure IaaS。 基底假設是一個 SAP 橫向內的 SAP 系統必須進行溝通，在公司，其部署表單的獨立部署其他許多軟體元件。 也應該引入部署表單使用者與 SAP GUI 或其他介面連線的差異。 我們有的內部部署 Active Directory/OpenLDAP 及擴充網站-至-網站/多台間的連線，或是私人的連線，例如 Azure ExpressRoute Azure 系統的 DNS 服務時，可以只符合下列條件。

若要取得更多背景的 SAP Azure 上的實作詳細資料，我們建議您閱讀章節 [SAP 執行個體的概念的 Cloud-Only 部署] [計劃-指南-7] 的說明一些 Azure 及如何這些必須搭配 Azure 中的 SAP 應用程式的基本概念建構此文件。

### <a name="scenario-of-a-sap-landscape"></a>SAP 橫向的案例

跨內部部署案例大致介紹像在下列圖形︰
 
![內部部署與 Azure 資產網站的連線][planning-guide-figure-2100]

以上顯示的案例說明案例位置內部部署 AD/OpenLDAP 和 DNS 延伸至 Azure。 在內部部署邊，每個 Azure 訂閱保留特定 IP 位址範圍。 Azure 虛擬網路 Azure 端指派的 IP 位址範圍。

#### <a name="security-considerations"></a>安全性考量

最低需求是例如 SSL/TLS 的安全通訊協定使用瀏覽器存取或 vpn 連線至 Azure 服務的系統存取。 假設是公司非常以不同方式處理其公司網路和 Azure 之間的連線。 有些公司 blankly 可能會開啟所有的連接埠。 其他的公司可能會想要精確哪些需要開啟等等。 

在 [一般 SAP 下表列出通訊的連接埠。 基本上只開啟 SAP 閘道連接埠。

| 服務 | 連接埠名稱 | 範例`<nn`> = 01 | 預設範圍 （最小值的最大值） | 註解 |
|---------|-----------|-------------------|-------------------------|---------|
| 發送器 | sapdp`<nn>`查看 * | 3201 | 3200 – 3299 | SAP 發送器，使用 Windows 的 SAP GUI 和 Java |
| 郵件伺服器 | sapms`<sid`> 查看 * * | 3600 | 免費 sapms`<anySID`> | sid = SAP 系統識別碼 |
| 閘道器 | sapgw`<nn`> 查看 * | 3301 | 免費 | SAP 閘道器，用於 CPIC 與 RFC 通訊 |
| SAP 路由器 | sapdp99 | 3299 | 免費 | 安裝後，可以中的任意值 /etc/services 指派只 SONT （中央的執行個體） 服務名稱。 |

*） nn = SAP 執行個體

*） sid = SAP 系統識別碼

如需詳細資訊連接埠所需的不同 SAP 產品或服務的 SAP 產品可以在這裡找到<http://scn.sap.com/docs/DOC-17124>。 這份文件與您應該可以在特定的 SAP 產品和案例所需的 VPN 裝置開啟專用的連接埠。

其他安全性措施時部署 Vm 在這種情況下可能是建立[網路安全性群組][virtual-networks-nsg]定義存取規則。

### <a name="dealing-with-different-virtual-machine-series"></a>處理不同的虛擬機器數列

在最後一個 12 個月 Microsoft 新增許多不同的 vCPUs，記憶體中的更多 VM 類型或更重要的硬體上執行。 並非所有這些 Vm 支援 SAP （請參閱支援 VM 類型 SAP 筆記[1928533]中的）。 這些 Vm 部分在不同的主機硬體代上執行時。 這些主機硬體代快速部署中的資料粒度的 Azure 比例單位。 所代表的意義的情況下可能會在您選擇不同的 VM 大小] 無法在執行相同的縮放比例單位。 橫跨刻度的不同的硬體基礎的能力受限於可用的設定。  例如︰ 如果您想要執行 DBMS A5 A11 Vm 和 G 數列 Vm SAP 應用程式層上，您就必須部署單一 SAP 系統或不同的 SAP 系統中的可用性組不同。


#### <a name="printing-on-a-local-network-printer-from-sap-instance-in-azure"></a>從 Azure 中的 SAP 執行個體的區域網路印表機上列印
##### <a name="printing-over-tcpip-in-cross-premises-scenario"></a>透過 TCP/IP 列印跨內部部署的情境中操作


設定您內部部署 TCP/IP 網路中的印表機 Azure VM 是整體與公司網路，假設您有 VPN-網站通道或建立的 ExpressRoute 連線相同。 

___

> ![Windows][Logo_Windows] Windows
>
> 若要執行這項操作︰
> - 某些網路印表機了就可輕鬆設定中 Azure VM 印表機設定精靈。 如果沒有精靈軟體已散發與印表機設定印表機的 「 手動 」 方式就是建立新的 TCP/IP 印表機連接埠。
> - 開啟 [控制台]-> [裝置和印表機]-> [新增印表機 
> - 選擇 [新增印表機使用 TCP/IP 地址或主機名稱
> - 在 [印表機的 IP 位址
> - 印表機連接埠標準 9100
> - 必要時手動安裝適當的印表機驅動程式。 
> 
> ![Linux][Logo_Linux] Linux
>
> - Windows 只為想要安裝網路印表機的標準程序
> - 如何新增印表機，只要[SUSE](https://www.suse.com/documentation/sles-12/book_sle_deployment/data/sec_y2_hw_print.html)或[紅色角色](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Deployment_Guide/sec-Printer_Configuration.html)遵循公用 Linux 輔助線。

___

 
![網路列印][planning-guide-figure-2200]



##### <a name="host-based-printer-over-smb-shared-printer-in-cross-premises-scenario"></a>Host （主機） 型印表機中小企業 （共用印表機） 透過跨內部部署情境中操作

Host （主機） 為基礎的印表機不網路相容的設計。 但主機型印表機能共用網路上的電腦，只要可在電腦上已連線的印表機。 將您的公司網路至網站或 ExpressRoute 及共用您的本機印表機。 SMB 通訊協定做 NetBIOS，而不是 DNS 名稱服務。 NetBIOS 主機名稱可能不同的 DNS 主機名稱。 標準大小寫的是 NetBIOS 主機名稱和 DNS 主機名稱相同。 DNS 網域意義 NetBIOS 名稱空間中。 因此，組成的 DNS 主機名稱和 DNS 網域的完整的 DNS 主機名稱不必須 NetBIOS 名稱區中使用。

印表機共用識別網路中的唯一名稱︰

* 主機名稱中小企業 host （主機） （永遠有需要）。 
* 共用 （永遠有需要） 的名稱。 
* 網域名稱如果印表機共用不在 SAP 系統相同的網域。 
* 此外，使用者名稱和密碼可能需要存取印表機共用]。

如何︰

___

> ![Windows][Logo_Windows] Windows
>
> 共用您的本機印表機。
> Azure VM 中開啟 [Windows 檔案總管] 並輸入名稱] 中的印表機。
> 印表機安裝精靈會引導您完成安裝程序。
>
> ![Linux][Logo_Linux] Linux
>
> 以下是一些範例中 Linux 設定網路印表機或包括章節標題相關的文件中 Linux 關於列印。 運作方式相同的方式中 Azure Linux VM VM 屬於 VPN 如下︰
>
> * SLES <https://en.opensuse.org/SDB:Printing_via_SMB_ (Samba) _Share_or_Windows_Share>
> * RHEL <https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Deployment_Guide/s1-printing-smb-printer.html>

___


##### <a name="usb-printer-printer-forwarding"></a>USB 印表機 （印表機轉接） 

Azure 中無法使用遠端桌面服務可提供使用者的存取權其本機印表機裝置遠端工作階段。

___

> ![Windows][Logo_Windows] Windows
>
> 在列印與 Windows 的更多詳細資料可以在這裡找到︰ <http://technet.microsoft.com/library/jj590748.aspx>。

___

 
#### <a name="integration-of-sap-azure-systems-into-correction-and-transport-system-tms-in-cross-premises"></a>SAP 整合到校正，然後在跨內部部署中的傳輸系統 (TMS) Azure 系統

SAP 變更和傳輸系統 (TMS) 必須設定為匯出及匯入系統橫向中的傳輸要求。 我們假設開發的執行個體 SAP 系統 （開發） 需位於 Azure 而品質保證 (QA) 和生產力系統 (PRD) 就是內部部署。 此外，我們會假設是中央傳輸目錄。

##### <a name="configuring-the-transport-domain"></a>設定傳輸網域
您指定為傳輸網域控制站所述[設定傳輸網域控制站](http://help.sap.com/erp2005_ehp_04/helpdata/en/44/b4a0b47acc11d1899e0000e829fbbd/content.htm)系統上設定您傳輸的網域。 將產生系統使用者將會建立 TMSADM 與必要的 RFC 目的地。 您可能會檢查使用交易 SM59 這些 RFC 連線。 在您傳輸的網域，必須先啟用主機名稱解析。 

如何︰

* 案例中，我們決定內部部署 QAS 系統會 CTS 網域控制站。 [撥號給交易 STMS。 [TMS] 對話方塊隨即出現。 設定傳輸網域] 對話方塊隨即出現。 （只會出現此對話方塊如果尚未設定傳輸網域。）
* 請確定已授權自動建立的使用者 TMSADM (SM59]-> [ABAP 連線]-> [ TMSADM@E61.DOMAIN_E61 ]-> [詳細資料]-> [Utilities(M)]-> [授權測試)。 交易的初始畫面 STMS 應該會顯示，此 SAP 系統現在做為傳輸網域控制站如下所示︰
 
![交易 STMS 網域控制站的初始畫面][planning-guide-figure-2300]

#### <a name="including-sap-systems-in-the-transport-domain"></a>在 [網域] 傳輸包括 SAP 系統

包括 SAP 系統傳輸網域中的順序如下所示︰

* 開發系統 Azure 中上移至傳輸系統 （用戶端 000），然後呼叫交易 STMS。 從 [] 對話方塊中選擇 [其他設定，然後繼續包含系統網域。 指定網域控制站做為目標主機 （[包括 SAP 系統傳輸網域中](http://help.sap.com/erp2005_ehp_04/helpdata/en/44/b4a0c17acc11d1899e0000e829fbbd/content.htm?frameset=/en/44/b4a0b47acc11d1899e0000e829fbbd/frameset.htm)）。 包含在傳輸網域正在等候系統。
* 基於安全性理由，然後必須回到網域控制站，以確認您的要求。 選擇 [系統概觀 」 和 「 核准等候系統。 然後確認提示時，以及設定將會散發。

此 SAP 系統現在包含其他所有的 SAP 系統傳輸網域中的必要資訊。 同時，所有其他的 SAP 系統，傳送新的 SAP 系統的地址資料並 SAP 系統輸入傳輸控制程式傳輸設定檔中。 核取 Rfc 和存取傳輸目錄的網域是否運作。

繼續傳輸系統平常所述的文件[變更與傳輸系統](http://help.sap.com/saphelp_nw70ehp3/helpdata/en/48/c4300fca5d581ce10000000a42189c/content.htm?frameset=/en/44/b4a0b47acc11d1899e0000e829fbbd/frameset.htm)的設定。

如何︰

* 請確定您 STMS 內部部署的設定正確。
* 請確定您的虛擬機器上 Azure 和倒目的可以解決傳輸網域控制站的主機名稱。
* 通話交易 STMS]-> [其他設定]-> [網域中的 [包含系統。
* 確認上的部署 TMS 系統中的 [連線]。
* 依照正常設定傳輸路由、 群組及圖層。

在網站-連線跨內部部署的情況下，內部部署和 Azure 之間的延遲仍然可以獲得超乎。 如果我們請依照下列順序傳輸開發的物件和測試生產系統或思考套用傳輸或支援套件，以不同的系統時，您會發現，中央傳輸目錄的位置而定，部分系統會遇到在高延遲讀取或寫入中央傳輸目錄中的資料。 這種情況很類似 SAP 橫向設定不同的系統展現不同的資料中心，獲得超乎資料中心之間的距離的位置。

若要解決這類延遲，而且有快速在讀取或寫入或傳輸目錄，您可以設定工作的系統兩個 STMS 傳輸網域 (內部部署和 Azure 中的系統並連結傳輸網域。 請檢查本說明背後 SAP TMS 此概念的原則的文件︰ <http://help.sap.com/saphelp_me60/helpdata/en/c4/6045377b52253de10000009b38f889/content.htm?frameset=/en/57/38dd924eb711d182bf0000e829fbfe/frameset.htm>。 

如何︰

* 設定在每個位置 （內部部署和 Azure） 傳輸網域使用交易 STMS <http://help.sap.com/saphelp_nw70ehp3/helpdata/en/44/b4a0b47acc11d1899e0000e829fbbd/content.htm>
* 連結網域網域的連結，並確認兩個網域之間的連結。 
  <http://help.sap.com/saphelp_nw73ehp1/helpdata/en/a3/139838280c4f18e10000009b38f8cf/content.htm>
* 發佈到連結系統設定。

#### <a name="rfc-traffic-between-sap-instances-located-in-azure-and-on-premises-cross-premises"></a>SAP 執行個體位於 Azure 及內部部署 （交互內部部署） 之間的 RFC 流量

RFC 流量的內部部署系統之間和 Azure 必須搭配使用。 若要在來源系統中要定義 RFC 連往的目標系統設定連線通話交易 SM59。 設定很類似 RFC 連線的標準設定。

假設在跨內部部署案例中，Vm 相同的網域中的執行需要彼此的 SAP 系統。 因此 RFC 之間的連線 SAP 系統設定與並無不同的設定步驟及輸入內部部署案例。

#### <a name="accessing-local-fileshares-from-sap-instances-located-in-azure-or-vice-versa"></a>存取 「 區域 」 的 fileshares 從 SAP 執行個體位於 Azure 或反向操作

SAP 執行個體位於 Azure 需要存取公司的部署中的檔案共用。 此外，需要存取檔案共用 Azure 在內部部署 SAP 執行個體。 若要啟用檔案共用，您必須設定的權限及本機系統上的共用選項。 請務必先開啟 [VPN 或 ExpressRoute 和之間的連線 Azure 資料中心的連接埠。

## <a name="supportability"></a>支援性
### <a name="6f0a47f3-a289-4090-a053-2521618a28c3"></a>Azure 監視 SAP 解決方案
若要啟用監視工具 SAPOSCOL 或 SAP 主機代理程式 SAP 監控任務重要 SAP 系統上 Azure 取得透過 Azure 監控副檔名為 SAP Azure 虛擬機器服務主機上的資料。 Sap 要求所特有 SAP 應用程式，因為 Microsoft 決定不一般實作 Azure 到所需的功能，但保留為客戶 Azure 中執行其虛擬機器部署的所需的監控元件和設定。 不過，監控元件的部署及生命週期管理會大多自動化 Azure。

#### <a name="solution-design"></a>解決方案的設計

若要啟用 SAP 監控開發解決方案為基礎架構 Azure VM 代理程式與副檔名架構。 Azure VM 代理程式與分機號碼架構的概念是允許安裝軟體應用程式可用內 VM Azure VM 副檔名圖庫中。 允許 （以類似的情況下 SAP Azure 監控副檔名），將 VM 的特殊功能的部署和部署時間的這類軟體設定為此概念的原則概念。 

自 2014 年 2 月的年，' Azure VM 代理程式 」 可讓特定 Azure VM 副檔名的 VM 內之處理方式插入 Windows Vm VM 建立 Azure 入口網站中的預設。 若 SUSE 或紅色角色 Linux VM 代理程式已經是 Azure Marketplace 圖像的一部分。 萬一其中一個要上傳至 Azure VM 代理程式手動安裝 Linux VM 來自內部部署。


基本的建置組塊 Azure 中監控解決方案的 SAP 看起來像這樣︰
 
![Microsoft Azure 副檔名元件][planning-guide-figure-2400]

區塊圖所示，Azure VM 圖像和 Azure 副檔名庫這是由 Azure 作業的全域複寫存放庫主控 SAP 監控解決方案的一部分。 是處理 Azure 實作與發佈新版本的 SAP Azure 監控副檔名 Azure 作業搭配使用 SAP 共同 SAP/MS 小組的責任。 SAP 這個 Azure 監控延伸會使用 Microsoft Azure 診斷 (WAD) 副檔名或 Linux Azure 診斷 （小），以取得所需的資訊。 

當您部署新的 Windows VM 時，' Azure VM 代理程式] 會自動新增到 VM。 此代理程式的功能是進行共同作業的載入和 Azure 的副檔名監控 SAP NetWeaver 系統的設定。 為 Linux Vm Azure VM 代理程式已經是 Azure Marketplace OS 圖像的一部分。

不過，就是仍需執行的客戶的步驟。 這是預先啟動啟用及設定的效能集合。 相關的 「 設定 」 的程序自動化的 PowerShell 指令碼或 CLI] 命令。 您可以在 Microsoft Azure 指令碼管理中心 [部署指南] [部署指南] 中所述下載的 PowerShell 指令碼。

SAP Azure 監控方案的整體架構看起來像︰
 
![Azure 監視 SAP NetWeaver 解決方案][planning-guide-figure-2500]

**完全相關及部署期間使用下列 PowerShell 指令程式或 CLI 命令的詳細步驟，請遵循 [部署指南] [部署指南] 中的指示。**

### <a name="integration-of-azure-located-sap-instance-into-saprouter"></a>Azure 整合位於 SAProuter SAP 執行個體

可從 SAProuter 也要 Azure 中執行的 SAP 執行個體。
 
![SAP 路由器網路連線][planning-guide-figure-2600]

SAProuter 啟用 TCP/IP 通訊如果沒有直接 IP 連線的參與系統之間。 這會提供沒有端對端之間的連線通訊合作夥伴網路層級是必要的優點。 預設的連接埠 3299 上接聽 SAProuter。
透過 SAProuter 連線 SAP 執行個體，您需要提供 SAProuter 字串和主機名稱與嘗試連線。

## <a name="sap-netweaver-as-java"></a>SAP NetWeaver 另存新檔 Java

目前的文件焦點已 SAP NetWeaver 一般或 SAP NetWeaver ABAP 堆疊。 在此小型節]，列出的 SAP Java 堆疊的特定考量。 最重要的 SAP NetWeaver Java 獨佔基礎應用程式的內容之一是 SAP 企業版入口網站。 SAP PI 和 SAP 方案管理員使用的 SAP NetWeaver ABAP 」 及 「 Java 堆疊等其他 SAP NetWeaver 依據應用程式。 因此，有當然就需要考慮的 SAP NetWeaver Java 堆疊的特定方面。

### <a name="sap-enterprise-portal"></a>SAP 企業版入口網站

如果您在跨內部部署案例中部署上的部署安裝不會不同 SAP 入口網站 Azure 虛擬機器中的設定。 由於 DNS 由內部部署，可以完成的個別執行個體的連接埠設定為設定的內部部署。 建議及目前文件中所描述的限制適用於應用程式，例如 SAP 企業版入口網站或 SAP NetWeaver Java 堆疊一般。 

![公開的 SAP 入口網站][planning-guide-figure-2700]

某些客戶特殊的部署情況時的虛擬機器主機已連線至公司網路透過網站-VPN 通道或 ExpressRoute SAP 企業版入口網站的直接公開至網際網路。 這種情況，您必須確定特定的連接埠是開啟及不封鎖的防火牆或網路安全性群組。 當您想要從內部部署雲端專用情境中連線至 SAP Java 執行個體時，套用所需相同技巧。

初始的入口網站 URI 是 http(s):`<Portalserver`>: 5XX00/irj 連接埠 50000 加號 （Systemnumber × 100） 來的正確位置。 在預設入口網站 URI 的 SAP 系統 00 `<dns name`>。`<azure region`>.Cloudapp.azure.com:PublicPort/irj。 如需詳細資訊，可以查看<http://help.sap.com/saphelp_nw70ehp1/helpdata/de/a2/f9d7fed2adc340ab462ae159d19509/frameset.htm>。 
 
![結束點設定][planning-guide-figure-2800]

如果您想要自訂 URL 及/或連接埠的 SAP 企業版入口網站，請選取這份文件︰

* [變更入口網站的 URL](http://wiki.scn.sap.com/wiki/display/EP/Change+Portal+URL) 
* [變更預設連接埠號碼、 入口網站連接埠號碼](http://wiki.scn.sap.com/wiki/display/NWTech/Change+Default++port+numbers%2C+Portal+port+numbers) 


## <a name="high-availability-ha-and-disaster-recovery-dr-for-sap-netweaver-running-on-azure-virtual-machines"></a>最高的顯示狀態 (HA) 以及損毀復原 (DR) 的 SAP NetWeaver 執行 Azure 虛擬機器上的商務連絡人
### <a name="definition-of-terminologies"></a>技術的定義

一組提供透過 IT 服務的業務連續性將 IT 干擾降到最低的技術通常相關字詞**可用性 (HA)**重複、 容錯或容錯移轉受保護的**相同**資料中心內的元件。 在本例中一個 Azure 區域。

至 IT 服務中斷及他們的復原**損毀修復 (DR)**也指定目標，但在**不同**的資料中心，通常是位於下的數百個公里辦公室。 我們通常之間不同的 Azure 區域內的相同的淉地區或由您為客戶既有的大小寫。

### <a name="overview-of-high-availability"></a>可用性的概觀
我們可以將 SAP 可用性 Azure 中將兩個部分的討論︰

* **Azure 基礎結構可用性**，例如運算 (Vm)、 網路、 儲存空間等 HA 和其增加 SAP 應用程式可用性的優點。
* **SAP 應用程式可用性**，例如 HA 的 SAP 軟體元件︰
    * SAP 應用程式伺服器
    * SAP ASCS/SCS 執行個體 
    * 資料庫伺服器

然後如何 Azure 基礎結構 HA 與合併]。

SAP 可用性 Azure 中有內部部署實體或虛擬環境相較於 SAP 可用性一些不同之處。 從 SAP 下列紙張說明在 Windows 上的虛擬環境中的標準 SAP 可用性設定︰ <http://scn.sap.com/docs/DOC-44415>。 沒有 sapinst 整合 SAP-HA 設定 Linux 像 Windows 版存在。 關於 SAP HA 內部部署 Linux 找到以下資訊︰ <http://scn.sap.com/docs/DOC-8541>。

### <a name="azure-infrastructure-high-availability"></a>Azure 基礎結構可用性
沒有任何單一 VM SLA 上 Azure 虛擬機器立即。 若要的瞭解如何的單一 VM 可用性看起來像您可以直接在建立不同的可用 Azure Sla 的產品︰ <https://azure.microsoft.com/support/legal/sla/>。

計算的基準是每月或 43200 分鐘 30 天。 因此，0.05%停機時間會對應到 21.6 分鐘。 如同以往，可使用不同的服務會將以下列方式︰

（顯示狀態服務 #1/100）*（顯示狀態服務 #2/100）*（顯示狀態服務 #3/100） *...

例如︰

(99.95) / 100)*(99.9) / 100)*(99.9) / 100) = 0.9975 或 99.75%的整體可用性。

#### <a name="virtual-machine-vm-high-availability"></a>虛擬機器 (VM) 可用性

有兩種類型的 Azure 平台事件的影響您的虛擬機器中的可用性︰ 預定進行的維修作業與計畫進行的維修作業。

* 預定進行的維修作業事件會定期更新 microsoft Azure 基礎平台來改善整體的可靠性、 效能及安全性的平台基礎結構的虛擬機器上執行。
* 尚未計劃進行的維修作業事件發生時的硬體或實際基礎結構的基礎虛擬機器有錯誤的一些方式。 這可能包括失敗的區域網路、 本機磁碟失敗次數或其他機架層級失敗。 偵測到這類失敗時，Azure 的平台會自動移轉您的虛擬機器不佳的實體伺服器裝載您的狀況良好的實體伺服器的虛擬機器。 這類事件很少，但也可能會導致您的虛擬機器重新啟動電腦。

更多詳細資料，請參閱此文件︰ <http://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability>

#### <a name="azure-storage-redundancy"></a>Azure 儲存複本

您的 Microsoft Azure 儲存體帳戶中的資料會自動被複寫永久性且高的顯示狀態，會議 Azure 儲存體 SLA 甚至面對暫時性硬體失敗

由於 Azure 儲存體保持 3 圖像的資料，根據預設，就不需要 RAID5 或 RAID1 在多個 Azure 磁碟。

更多詳細資料，請參閱這篇文章︰ <http://azure.microsoft.com/documentation/articles/storage-redundancy/> 

#### <a name="utilizing-azure-infrastructure-vm-restart-to-achieve-higher-availability-of-sap-applications"></a>利用 Azure 基礎結構 VM 重新啟動達到 「 高 」 SAP 應用程式

如果您決定不想使用功能像 Windows Server 容錯移轉叢集 (WSFC) 或 Linux 對等 （後者其中一個目前不支援在 Azure SAP 軟體搭配）、 重新啟動 Azure VM 利用保護 SAP 系統針對計劃和計畫停機時間的 Azure 實體伺服器基礎架構與整體的基礎 Azure 平台。 
 
> [AZURE.NOTE] 請務必提及的 Azure VM 重新啟動主要保護 Vm 及非應用程式。 重新啟動 VM 不提供 SAP 應用程式的可用性，但它會提供某種程度的基礎結構的顯示狀態，因此間接 」 更高的可用性 」 的 SAP 系統。 計畫之內主機中斷後重新啟動 VM 花費的時間也沒有 SLA。 因此，例如 (A) SCS 或 DBMS SAP 系統的要徑元件不適可用性 」 的方法。

高可用性的另一個重要的基礎結構元素都是儲存空間。 例如︰ Azure 的儲存空間 SLA 是 99,9%的可用性。 如果有一個部署其磁碟的所有 Vm 至單一 Azure 儲存體帳戶，可能無法使用會導致無法使用的所有 Vm 置於該 Azure 儲存體帳戶和也所有 SAP 元件執行這些 Vm 內的 Azure 儲存體。  

而不是將所有 Vm 都放到單一 Azure 儲存體帳戶，您也可以使用專用的儲存帳戶每個 VM，然後在這種方式使用多個獨立的 Azure 儲存體帳戶增加整體 VM 和 SAP 應用程式的可用性。 

使用 Azure 基礎結構的 SAP NetWeaver 系統範例架構 HA 可能看起來像這樣︰
 
![利用 Azure 基礎結構 HA 達成 SAP 應用程式 」 更高 」 的可用性][planning-guide-figure-2900]

要徑 SAP 元件我們達成下列為止︰

* SAP 應用程式伺服器 （另存新檔） 的可用性

SAP 應用程式伺服器執行個體是多餘的元件。 每個 SAP 自己 VM 執行的不同 Azure 發生錯誤，並升級網域上部署執行個體 (請參閱章節 [故障網域] 和 [計劃-快速入門-3.2.1] 和 [升級 Domains][planning-guide-3.2.2])。 這被確保使用 Azure 可用性集 （請參閱章節 [Azure 可用性 Sets][planning-guide-3.2.3])。 Azure 故障或升級的網域的潛在的計畫之內無法使用會導致無法使用限制 Vm 的數字，與他們 SAP 另存新檔執行個體。 每個 SAP 執行個體會放置在它自己的 Azure 儲存體帳戶 – 的 Azure 儲存體帳戶可能無法使用會導致無法使用的只有一個 VM 其 SAP 另存新檔的執行個體。 不過，請注意，有一個 Azure 訂閱中 Azure 儲存體帳戶的限制。 若要確保 (A) SCS 執行個體的自動啟動 VM 重新啟動後，請務必述章節 [使用自動啟動之 SAP 執行個體] (A) SCS 執行個體開始設定檔中設定自動啟動參數 [計劃-快速入門-11.5]。
請也閱讀章節 [可用性 SAP 應用程式伺服器] [計劃-快速入門-11.4.1] 以取得詳細資料。

* _更高_SAP (A) SCS 執行個體的顯示狀態
 
以下我們利用 Azure VM 重新啟動保護 VM 已安裝的 SAP (A) SCS 執行個體。 若是計畫之內停機時間的 Azure 伺服器，Vm 將會在另一個可用的伺服器上重新啟動。 如先前所述，重新啟動 Azure VM 主要保護 Vm 及非應用程式，在此案例 (A) SCS 執行個體中。 透過 VM 重新啟動我們會連絡間接 」 更高的可用性 」 SAP (A) SCS 執行個體。 若要確保 (A) SCS 執行個體的自動啟動 VM 重新啟動後，請務必述章節 [使用自動啟動之 SAP 執行個體] (A) SCS 執行個體開始設定檔中設定自動啟動參數 [計劃-快速入門-11.5]。 (A) SCS 執行個體為單一錯誤點 (SPOF) 在單一 VM 中執行這表示會可用性的整個 SAP 橫向 determinative 因子變異數。 

* _更高_DBMS 伺服器的可用性

在這裡，類似於 SAP (A) SCS 執行個體使用大小寫，我們利用 Azure VM 重新啟動保護 VM 安裝 DBMS 軟體，以及我們達到 「 高 」 透過 VM 重新啟動 DBMS 軟體。 DBMS 在單一 VM 執行也是 SPOF，而且可用性的整個 SAP 橫向 determinative 因子變異數。 

### <a name="sap-application-high-availability-on-azure-iaas"></a>SAP 應用程式可用性 Azure IaaS 上
若要達到完整 SAP 系統高可用性，我們需要保護所有要徑 SAP 系統元件，例如多餘 SAP 應用程式的伺服器，和等唯一元件 （例如單一錯誤點） SAP (A) SCS 執行個體和 DBMS。 

#### <a name="5d9d36f9-9058-435d-8367-5ad05f00de77"></a>SAP 應用程式伺服器的可用性
SAP 應用程式伺服器/對話方塊執行個體不需要考慮特定可用性解決方案。 可用性只要的方法是由重複，因此在不同的虛擬機器中有足夠。 他們應該所有放在同一個 Azure 可用性設定，以避免 Vm 可能會更新同時期間預定進行的維護停機時間。 章節 [升級的網域] [計劃-快速入門-3.2.2] 已已經採用不同的升級和 Azure 刻度] 單位中的錯誤網域為基礎的基本功能。 Azure 可用性集顯示在此文件的章節 [Azure 可用性組] [計劃-快速入門-3.2.3]。 

沒有無限發生錯誤，並可讓 Azure 中 Azure 刻度] 單位的顯示狀態設定升級網域的號碼。 這表示將數字的 Vm 放入遲早中的多個 VM 結束相同故障或升級的網域中的一個可用性設定

部署幾個 SAP 應用程式伺服器執行個體中其專用 Vm，假設變異數，我們會取得 5 升級的網域，如下圖誕生結尾。 發生錯誤，並更新的網域，在可用性組中的實際最大數量可能會在未來變更︰
 
![HA SAP 應用程式中的伺服器 Azure][planning-guide-figure-3000]

更多詳細資料，請參閱此文件︰ <http://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability>


#### <a name="high-availability-for-the-sap-ascs-instance-on-windows"></a>在 Windows 上的 SAP (A) SCS 執行個體的可用性

Windows Server 容錯移轉叢集 (WSFC) 是以保護 SAP (A) SCS 執行個體常用的解決方案。 也整合 sapinst 在表單中的 「 HA 安裝 」。 在目前不提供功能，以設定必要的 Windows Server 容錯移轉叢集相同的方式完成內部部署 Azure 基礎結構。

2016 年 1 月到執行 Windows 作業系統的 Azure 雲端平台不提供兩個 Azure Vm 之間共用的磁碟上使用叢集共用大量的可能性。

有效的解決方案是 3rd 方軟體來提供可以整合 WSFC 同步和透明磁碟複寫共用的區的使用方式。 此方法暗示主動叢集節點能夠存取磁碟份點的其中一個時間。 年 1 月 2016 此 HA 設定受支援保護 Windows 來賓 OS 上 Azure Vm 3rd 方軟體 SIOS DataKeeper 搭配的 SAP (A) SCS 執行個體。

SIOS DataKeeper 解決方案提供由 Windows 容錯共用的磁碟叢集資源︰

* 其他的 Azure VHD 附加至每個位於 Windows 叢集設定虛擬機器 (Vm)
* 在兩個 VM 節點上執行 SIOS DataKeeper 叢集版本
* 無法同步會反映的其他 VHD 內容的方式設定 SIOS DataKeeper 叢集 Edition 附加的音量 Vm 來源，以其他 VHD 附加目標 VM 的音量。
* SIOS DataKeeper 是抽象化來源及目標本機磁碟區，顯示這些 Windows 容錯移轉叢集為單一共用磁碟。
 
您可以找到所有詳細資料如何安裝 Windows 容錯移轉叢集 SIOS Datakeeper 與 SAP[叢集 SAP ASCS 執行個體使用 Windows Server 容錯移轉叢集上使用 SIOS DataKeeper Azure] [ha-guide-classic]白皮書 （英文)。 

#### <a name="high-availability-for-the-sap-ascs-instance-on-linux"></a>SAP (A) SCS 執行個體 linux 可用性
 
到 Dec 2015 也有不等於共用磁碟上 Azure Linux Vm 的 WSFC。 使用 3rd 廠商軟體，例如 SIOS for Windows 的替代方案不會執行 SAP linux 上 Azure 尚未驗證。



#### <a name="high-availability-for-the-sap-database-instance"></a>SAP 資料庫執行個體的可用性
一般 SAP DBMS HA 設定根據兩個 DBMS Vm DBMS 高可用性功能會用於的被動式 DBMS 執行個體的第二個 vm 將資料從作用中的 DBMS 執行個體。

可用性及損毀修復功能 DBMS 一般，以及特定 DBMS 會有 [DBMS 部署指南] [dbms 輔助線] 中的說明。


#### <a name="end-to-end-high-availability-for-the-complete-sap-system"></a>完成 SAP 系統的端對端高可用性

以下是兩個範例的完整 SAP NetWeaver HA 架構中 Azure-Windows 和 Linux 的。
以下所述的概念，可能需要會影響此有點部署許多 SAP 系統和 Vm 部署數會出現與超出每個訂閱的儲存空間帳戶上限。 在這種情況下，Vhd 的 Vm 需要合併在一個儲存帳戶。 通常您可以藉由組合 Vhd 的 SAP 應用程式層級的不同 SAP 系統 Vm 執行此作業。  我們也合併不同 Vhd 的不同 DBMS Vm 的 Azure 儲存體帳戶中的不同 SAP 系統。 因此記住 IOPS 限制的 Azure 儲存體帳戶 ( <https://azure.microsoft.com/documentation/articles/storage-scalability-targets> )

##### <a name="windowslogowindows-ha-on-windows"></a>![Windows][Logo_Windows] HA Windows 上

![SAP NetWeaver 應用程式 HA 架構與 Azure IaaS 中的 SQL Server][planning-guide-figure-3200]

下列 Azure 建構適用於 SAP NetWeaver 系統，最小化的影響基礎結構的問題，並裝載修補︰

* 部署完成系統上 Azure （必要-DBMS 層級、 (A) SCS 執行個體和完整的應用程式層需要執行的同一個位置）。
* 完整的系統執行一個 Azure 的訂閱 （必填） 中。
* Azure 虛擬網路 （必填） 中，執行完整的系統。
* 即使使用屬於相同的虛擬網路的所有 Vm，有可能是一個 SAP 系統的 Vm 到三個可用性集區隔。
* 執行 DBMS 一個 SAP 系統執行個體的所有 Vm 都為一個可用性設定。 假設您有多個 VM 自原生 DBMS 可用性使用功能，例如 SQL Server AlwaysOn 或 Oracle 資料保護執行每個系統 DBMS 執行個體。
* 執行 DBMS 執行個體的所有 Vm 都使用自己的儲存空間帳戶。 從一個儲存帳戶複寫 DBMS 資料和記錄檔到另一個使用 DBMS 可用性函數的同步處理資料的儲存空間帳戶。 無法使用的儲存空間帳戶會導致，一個 SQL Windows 叢集節點，但不是整個的 SQL Server 服務無法使用。 
* 執行一個 SAP 系統 (A) SCS 執行個體的所有 Vm 都為一個可用性設定。 這些 Vm 內部是設定 Windows 伺服器容錯移轉叢集 (WSFC) 來保護 (A) SCS 執行個體。
* 執行 (A) SCS 執行個體的所有 Vm 都使用自己的儲存空間帳戶。 (A)使用 SIOS DataKeeper 複寫的其他儲存帳戶，會將 SCS 執行個體檔案和 SAP 全域資料夾複寫從一個儲存帳戶。 無法使用的儲存空間帳戶會導致無法使用其中一個 (A) SCS Windows 叢集節點，但不是整數 (A) SCS 服務。 
* 代表 SAP 應用程式的伺服器層的所有 Vm 會在第三個可用性都設定。
* 執行 SAP 應用程式伺服器的所有 Vm 都使用自己的儲存空間帳戶。 無法使用的儲存空間帳戶會導致無法使用一個 SAP 應用程式伺服器] 繼續執行其他 SAP 另存新檔的位置。

##### <a name="linuxlogolinux-ha-on-linux"></a>![Linux][Logo_Linux] HA linux

架構的 SAP HA linux 上 Azure 基本上是與 Windows 相同上所述。 2016 年 1 月齡有兩個限制不過︰

* SAP ASE 16 目前支援 linux Azure 上沒有任何 ASE 複寫功能。 
* 沒有 SAP (A) SCS HA 解決方案 linux Azure 上目前尚未支援

因此齡 2016 年 1 月 SAP-Linux-Azure 系統無法達成相同可用性 SAP Windows Azure 系統由於遺漏 (A) SCS HA 執行個體和單一執行個體 SAP ASE 資料庫。

### <a name="4e165b58-74ca-474f-a7f4-5e695a93204f"></a>使用自動啟動之 SAP 執行個體

SAP 提供的 VM 內 OS 開始後立即啟動 SAP 執行個體的功能。 確切步驟所述 SAP 知識庫文章[1909114] -如何啟動 SAP 執行個體自動使用參數自動啟動。 不過，如果 SAP 不建議使用的設定不再因為沒有控制項以執行個體重新啟動順序，假設變異數個以上的 VM 您使用的是受影響，或多個執行個體執行每 VM。 假設一般 Azure SAP 應用程式伺服器執行個體之一 VM 和單一 VM 最後快速重新啟動的大小寫的情況下，自動啟動不很重要，並可啟用藉此參數︰

    Autostart = 1

將 SAP ABAP 及/或 Java 執行個體的開始設定檔。

> [AZURE.NOTE] 
> 自動啟動參數可以有一些困難以及。 更多詳細資料，請在參數啟動相關的 Windows/Linux 服務的執行個體時觸發 SAP ABAP 或 Java 執行個體的開頭。 作業系統開機設定時一定是這樣。 不過，重新啟動 SAP 服務也是常見的作業 SAP 軟體生命週期管理功能，例如加總或其他更新或升級。 這些功能不預期重新啟動自動在所有執行個體。 因此，自動啟動參數應該執行此工作之前，先停用。 自動啟動參數也不應群組直條圖，例如 ASCS/SCS/SONT 的 SAP 執行個體。

請參閱關於自動啟動的其他資訊的 SAP 執行個體以下執行個體︰

* [啟動停止以及您 Unix 伺服器的開始/停止 SAP](http://scn.sap.com/community/unix/blog/2012/08/07/startstop-sap-along-with-your-unix-server-startstop)
* [啟動和停止 SAP NetWeaver 管理代理程式](https://help.sap.com/saphelp_nwpi711/helpdata/en/49/9a15525b20423ee10000000a421938/content.htm)
* [如何啟用自動開始的 HANA 資料庫](http://www.freehanatutorials.com/2012/10/how-to-enable-auto-start-of-hana.html)


### <a name="larger-3-tier-sap-systems"></a>較大的 3 層 SAP 系統
您已經在舊版的各節討論 3 層 SAP 設定高可用性方面。 但應該做什麼系統 DBMS 伺服器需求哪裡太大，讓它位於 Azure，但 SAP 應用程式層可以部署到 Azure？

#### <a name="location-of-3-tier-sap-configurations"></a>3 層 SAP 設定的位置

不支援分割應用程式層本身或應用程式及 DBMS 層內部部署和 Azure 之間。 SAP 系統是 [完全部署內部部署或 Azure 中。 它不也有 Azure 中執行內部部署與一些其他人的應用程式伺服器支援。 這是討論的起點。 我們也不支援有 SAP 系統和 SAP 應用程式伺服器層級部署在兩個不同的 Azure 區域內的 DBMS 元件。 例如︰ 在 [美國中部西和 SAP 應用程式層 DBMS。 不支援這類設定的原因是延遲的敏感度 SAP NetWeaver 架構。

不過，共去年資料中心開發的合作夥伴共同位置 Azure 區域。 這些共同位置通常是非常接近實體 Azure 資料 Azure 區域內的中心。 簡短的距離，連線到 Azure ExpressRoute 透過代管資產的可能會導致小於 2 毫秒的延遲時間。 在這種情況下，找到 DBMS 圖層 （包括儲存空間舊/NAS） 在這種共同的位置和 SAP 應用程式層 Azure 中的有可能是。 到 Dec 2015 我們沒有任何部署想。 但不同客戶非 SAP 應用程式部署已使用這種方法。 

### <a name="offline-backup-of-sap-systems"></a>離線備份的 SAP 系統

依存於有選擇 （2 層或 3 層） SAP 組態可能需要備份。 VM 本身加號備份資料庫的內容。 DBMS 相關備份都必須完成資料庫的方法。 詳細的說明的不同的資料庫，可以找到 [DBMS 指南] 和 [dbms 輔助線]。 另一方面，SAP 資料可以備份 （包括的資料庫內容） 的方式離線本節所述在線上或下一節所述。

離線備份基本上需要關機的 VM 透過 Azure 入口網站，而且基底 VM 磁碟加上所有的複本附加 Vhd vm。 這會保留 VM 和其相關聯的磁碟的時間點。 建議您將 「 備份 」，複製到不同的 Azure 儲存體帳戶。 程序因而章節 [Azure 儲存體帳戶之間複製磁碟] 所述 [計劃-快速入門-5.4.2] 的文件會套用。
除了關機使用其中一個 [Azure 入口網站也可以執行，透過 Powershell 或 CLI 如下所示︰ <https://azure.microsoft.com/documentation/articles/virtual-machines-deploy-rmtemplates-powershell/>

還原的狀態就會刪除基底 VM，以及原始的基底 VM 磁碟含有，並且裝載 Vhd，複製到原始儲存帳戶回儲存的 Vhd，然後重新部署系統。
本文將示範範例如何指令碼的 Powershell 程序︰ <http://www.westerndevs.com/azure-snapshots/>

請務必 restoing VM 備份如上所述會建立新的硬體金鑰安裝新的 SAP 授權。

### <a name="online-backup-of-an-sap-system"></a>SAP 系統的線上備份

備份 DBMS 執行 DBMS 特定方法 [DBMS 指南] [dbms 輔助線] 中所述。 

在 SAP 系統中的其他 Vm 備份使用 Azure 虛擬機器備份功能。 Azure 虛擬機器備份您使用的是在 2015年簡略介紹並同時是備份完成 VM Azure 中的標準方法。 Azure 備份 Azure 中儲存的備份，並允許的 VM 還原一次。 

> [AZURE.NOTE] 
> 2015 年 12 月齡使用 VM 備份不會保留唯一的 VM 識別碼用於 SAP 授權。 這表示從 VM 備份檔案還原需要安裝新的 SAP 授權金鑰，為還原的 VM 被視為新 VM 和不離職一來，已儲存的替代方案。 2016 年 1 月齡 Azure VM 備份不尚未支援 Vm 部署與 Azure Resourc 管理員。

> ![Windows][Logo_Windows] Windows
>
> 理論上執行的資料庫可以備份一致的方式，也如果 DBMS 系統支援 Windows VSS 的 Vm (大量陰影複製服務<https://msdn.microsoft.com/library/windows/desktop/bb968832(v=vs.85).aspx> ) 為例如 SQL Server 時。
> 不過，請注意，根據 Azure VM 備份還原的資料庫的時間點不可能。 因此，建議您執行 DBMS 功能，而不要依賴 Azure VM 備份資料庫的備份
>
> 熟悉 Azure 虛擬機器備份請從這裡開始︰ <https://azure.microsoft.com/documentation/articles/backup-azure-vms/>。
>
> 其他可能的方式是使用組合的 Microsoft 資料保護管理員安裝在 Azure VM 和 Azure 備份備份/還原資料庫。 詳細資訊，請參閱以下︰ <https://azure.microsoft.com/documentation/articles/backup-azure-dpm-introduction/>。  


> ![Linux][Logo_Linux] Linux
> 
> 有 Windows VSS 中 Linux 不等於。 因此檔案一致的備份都可能，但不是應用程式一致的備份。 SAP DBMS 備份應該使用 DBMS 功能。 檔案系統包括 SAP 相關可以儲存資料，例如使用 tar 所述以下︰ <http://help.sap.com/saphelp_nw70ehp2/helpdata/en/d3/c0da3ccbb04d35b186041ba6ac301f/content.htm>


### <a name="azure-as-dr-site-for-production-sap-landscapes"></a>Azure 生產 SAP 環境的 DR 網站

Mid 於 2014 年，自 HYPER-V、 系統管理中心和 Azure 各種元件的擴充功能會啟用 Vm 執行內部部署的 DR 網站根據 HYPER-V Azure 的使用方式。 

此處所列詳細說明如何將此方案部署部落格︰ <http://blogs.msdn.com/b/saponsqlserver/archive/2014/11/19/protecting-sap-solutions-with-azure-site-recovery.aspx>

## <a name="summary"></a>摘要
SAP 系統 Azure 中重點是可用性的︰

* 在目前，失敗 SAP 點無法保護完全相同的方法為可以內部部署中已完成。 原因是共用磁碟叢集無法尚未建置 Azure 中不會使用第 3 廠商軟體。
* DBMS 圖層中，您需要使用的不是共用的磁碟叢集技術使用 DBMS 功能。 詳細資料會記錄在 [DBMS 指南] [dbms 輔助線]。
* 若要最小化 Azure 基礎結構或主機維護內故障網域問題的影響，您應該使用 Azure 可用性集︰
    * 建議您有一個可用性設定的 SAP 應用程式層級。
    * 建議您有另一個可用性設定的 SAP DBMS 圖層。
    * 不建議使用套用相同的可用性 Vm 不同 SAP 系統的設定。
* 為了備份 SAP DBMS 圖層，請核取 [DBMS 指南] [dbms 輔助線]。
* 備份 SAP 對話方塊執行個體意義小因為它是通常更快速重新部署簡單的對話方塊執行個體。
* 備份 VM 其中包含全域目錄 SAP 系統和，所有設定檔的不同的執行個體，會有意義和應該執行的 Windows 備份或例如 linux tar。 因為 Windows Server 2008 (R2) 和 Windows Server 2012 (R2) 之間的差異，其更容易備份使用較新的 Windows Server 版本，我們建議您執行 Windows Server 2012 (R2) 作為 Windows 來賓作業系統。 
