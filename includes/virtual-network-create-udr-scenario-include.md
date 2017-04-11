## <a name="scenario"></a>案例

若要更清楚說明如何建立 UDRs，這份文件會使用下列案例。

![圖像描述](./media/virtual-network-create-udr-scenario-include/figure1.png)

在這個案例中您會建立一個 UDR*正面結束子網路*和其他 UDR 用於*後結束子網路*，如下所述︰ 

- **UDR FrontEnd**。 前端 UDR 會套用到*FrontEnd*子網路，且包含一個路由︰  
    - **RouteToBackend**。 這會將所有流量都傳送至**FW1**虛擬機器的後端子網路。
- **UDR 後端**。 後端 UDR 會套用到*後端*子網路，且包含一個路由︰ 
    - **RouteToFrontend**。 這會將所有流量都傳送至前端子網路**FW1**虛擬機器。

會在**FW1**虛擬機器，作為虛擬應用裝置路由傳送至另一個子網路從所有流量，可確保這些路由的組合。 您也需要開啟該 VM，以確保其可接收其他 Vm 流量 IP 轉寄功能。
