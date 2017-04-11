## <a name="service-chaining---transit-through-peered-vnet"></a>服務 Chaining-透過 peered VNet 傳輸

雖然使用系統路由，協助流量會自動為您的部署，為您要控制封包路由透過虛擬應用裝置的情況。
在此案例中，有兩個 VNets HubVNet 和 VNet1 下圖中所述的訂閱。 您部署網路虛擬 Appliance(NVA) VNet HubVNet 中。 建立之後 VNet 外面 HubVNet 和 VNet1 之間，您可以設定使用者定義的路徑，並指定 HubVNet NVA 下一個躍點。

![NVA 傳輸](./media/virtual-networks-create-vnetpeering-scenario-transit-include/figure01.PNG)

> [AZURE.NOTE] 為求，假設所有 VNets 以下都位於同一份訂閱。 但也適用於跨訂閱案例。

若要啟用傳輸路由索引鍵的屬性是 「 允許轉寄流量 」 參數。 這個選項可讓接受並傳送流量從 / NVA peered VNet。  
