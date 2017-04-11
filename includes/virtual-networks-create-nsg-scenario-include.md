## <a name="scenario"></a>案例

若要更清楚說明如何建立 NSGs，這份文件會使用下列案例。

![VNet 案例](./media/virtual-networks-create-nsg-scenario-include/figure1.png)

在這個案例中，您將建立在**TestVNet**虛擬網路中，每一個子網路 NSG 如下所述︰ 

- **NSG FrontEnd**。 前端 NSG 會套用到*FrontEnd*子網路，且包含兩個規則︰  
    - **rdp 規則**。 此規則可讓 RDP *FrontEnd*子網路流量。
    - **網頁規則**。 此規則可讓 HTTP *FrontEnd*子網路流量。
- **NSG 後端**。 後端 NSG 會套用到*後端*子網路，且包含兩個規則︰ 
    - **sql 規則**。 此規則可讓僅從*主選單*子網路 SQL 流量。
    - **網頁規則**。 此規則拒絕所有的網際網路的*後端*子網路繫結流量。

組合，這些規則建立 DMZ 類似的情況下，位置的後端子網路才能接收內送的流量 sql 前端子網路，且不能存取網際網路時前端子網路可與網際網路通訊和接收內送 HTTP 要求僅。
 
