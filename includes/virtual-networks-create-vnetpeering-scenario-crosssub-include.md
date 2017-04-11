## <a name="peering-across-subscriptions"></a>對等訂閱

在這個案例中，您將建立對等屬於不同的訂閱的兩個 VNets 之間。

![跨子案例](./media/virtual-networks-create-vnetpeering-scenario-crosssub-include/figure01.PNG)

對等 VNet 需要授權的角色型存取控制 (RBAC)。 跨訂閱案例中，您必須先足夠的權限授與使用者將會建立對等的連結︰

> [AZURE.NOTE] 如果相同的使用者有權限在兩個訂閱，您可以跳 step1 4 下方。
