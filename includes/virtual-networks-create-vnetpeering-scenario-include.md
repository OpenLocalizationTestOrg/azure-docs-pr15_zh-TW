## <a name="scenario"></a>案例

若要更清楚說明如何建立 VNet 和子網路，這份文件會使用下列案例。

![VNet 案例](./media/virtual-networks-create-vnet-scenario-include/vnet-scenario.png)

在這個案例中，您將建立名為**TestVNet**與**192.168.0.0./16**保留 CIDR 區塊 VNet。 您 VNet 會包含下列的子網路︰ 

- **FrontEnd**，使用**192.168.1.0/24**為其 CIDR 區塊。
- **後端**，使用**192.168.2.0/24**為其 CIDR 區塊。

 