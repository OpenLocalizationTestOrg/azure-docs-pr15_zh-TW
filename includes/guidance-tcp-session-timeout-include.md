##<a name="tcp-settings-for-azure-vms"></a>Azure Vm TCP 設定

使用[NAT] azure Vm 通訊與公用網際網路[nat]（網路位址轉譯）。 NAT 裝置指派的公用 IP 位址和連接埠 Azure vm，讓該 VM 建立通訊端使用其他裝置。 如果封包停止通過該通訊端在特定時間後，請 NAT 裝置刪除對應，然後通訊端就可以使用其他 Vm]。

這是預期端維護超出逾時期間的 TCP 基礎應用程式可能會導致通訊問題的常見 NAT 行為。 有兩個閒置逾時設定，*建立連線*狀態的工作階段的考量︰

- **輸入**透過[Azure 負載平衡器][azure-lb-timeout]。 此逾時預設值為 4 分鐘，並可以調整設定，為 30 分鐘。
- **輸出**使用[SNAT] [ snat] (來源 NAT)。 此逾時設定為 4 分鐘，且無法調整。

若要確保連線不會遺失逾時限制以外，請確定您的應用程式就會保存工作階段，或是您可以設定基礎作業系統執行此作業。 設定以使用不同 Linux 和 Windows 系統，如下所示。

針對[Linux][linux]，您應該變更下列核心變數。
net.ipv4.tcp_keepalive_time = 120 net.ipv4.tcp_keepalive_intvl = 30 net.ipv4.tcp_keepalive_probes = 8
 
[在 Windows]版[windows]，您應該變更下列登錄值。
KeepAliveInterval = 30 KeepAliveTime = 120 TcpMaxDataRetransmissions = 8


上述設定可確保維持作用封包是 2 分鐘 （120 秒） 閒置時間之後, 傳送]，然後傳送每隔 30 秒。 而且如果失敗的封包 8，放工作階段。

<!-- links -->
[nat]: http://computer.howstuffworks.com/nat.htm
[snat]: ../load-balancer/load-balancer-overview.md/#source-nat
[linux]: http://tldp.org/HOWTO/TCP-Keepalive-HOWTO/usingkeepalive.html
[windows]: http://blogs.technet.com/b/nettracer/archive/2010/06/03/things-that-you-may-want-to-know-about-tcp-keepalives.aspx
[azure-lb-timeout]: ../load-balancer/load-balancer-tcp-idle-timeout.md