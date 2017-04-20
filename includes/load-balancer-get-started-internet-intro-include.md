Azure 負載平衡器為層級 4 （TCP、 UDP） 負載平衡器。 負載平衡器來散佈在雲端服務的正常服務執行個體或一組負載平衡器中的虛擬機器之間的傳入流量提供高可用性。 Azure 負載平衡器也可以呈送那些服務在多個連接埠、 多個 IP 位址，或兩者。

您可以設定負載平衡器以：

* 負載平衡虛擬機器 (Vm) 的內送網際網路傳輸。 我們[網際網路對向負載平衡器](../articles/load-balancer/load-balancer-internet-overview.md)以參照在此案例中的負載平衡。
* 負載平衡流量 (VNet) 的虛擬網路中的 Vm 之間、 雲端服務 Vm 之間或之間的內部電腦與 Vm 跨內部虛擬網路。 我們請參閱負載平衡器此案例中為[內部負載平衡器 (ILB)](../articles/load-balancer/load-balancer-internal-overview.md)。
* 轉接至特定的虛擬機器執行個體的外部流量。
