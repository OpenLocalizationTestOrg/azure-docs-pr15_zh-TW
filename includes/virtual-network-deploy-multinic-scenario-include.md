## <a name="scenario"></a>案例

這份文件會逐步執行 Vm 中使用多個 Nic，在特定案例的部署。 在此案例中，必須裝載於 Azure 雙層 IaaS 工作量。 每一層部署在自己的子網路中虛擬網路 (VNet)。 將前端層被由數個網頁伺服器，設定高可用性負載平衡器中的群組在一起。 後端層被由幾個資料庫伺服器。 兩個 Nic 每個，一個用於存取資料庫，一個用於管理來部署這些資料庫伺服器。 此案例也包含網路安全性群組 (NSGs) 來控制允許哪些流量到每一個子網路，以及 NIC 部署中。 下圖顯示這個案例中的基本架構。  

![MultiNIC 案例](./media/virtual-network-deploy-multinic-scenario-include/Figure1.png)

