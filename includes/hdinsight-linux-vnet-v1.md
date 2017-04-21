> 您無法使用 v1 （傳統） Linux 型 HDInsight Azure 虛擬網路。 虛擬網路必須 v2 （Azure 資源管理員），使其列為 Azure 預覽入口網站中的 HDInsight 叢集建立程序期間的選項，或從 Azure CLI 或 PowerShell 的 Azure 建立叢集時可重複使用。
> 
> 如果您有資源 v1 網路，且您想要讓 HDInsight 直接存取這些資源透過虛擬網路，請參閱[連線至新的 VNets 的傳統 VNets](../articles/vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md)如何 v1 虛擬網路連線 v2 虛擬網路上的資訊。 此連線建立之後，您可以建立 HDInsight 叢集 v2 虛擬網路中。