### <a name="to-verify-your-connection-by-using-powershell"></a>使用 PowerShell 來驗證您的連線

您可以驗證您的連線成功使用`Get-AzureRmVirtualNetworkGatewayConnection`cmdlet，包含或不含`-Debug`。 

1. 使用下列 cmdlet 範例中，設定以符合您自己的值。 如果出現提示，請選取 ['A' 才能執行 「 所有 」。 在範例中，`-Name`是指您已建立並想要測試的連線名稱。

        Get-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnection -ResourceGroupName MyRG

2. Cmdlet 完成後，檢視中的值。 在下面的範例中，連線狀態顯示為 「 已連線 」，您可以看見輸入與輸出位元組。

        Body:
        {
          "name": "MyGWConnection",
          "id":
        "/subscriptions/086cfaa0-0d1d-4b1c-94544-f8e3da2a0c7789/resourceGroups/MyRG/providers/Microsoft.Network/connections/MyGWConnection",
          "properties": {
            "provisioningState": "Succeeded",
            "resourceGuid": "1c484f82-23ec-47e2-8cd8-231107450446b",
            "virtualNetworkGateway1": {
              "id":
        "/subscriptions/086cfaa0-0d1d-4b1c-94544-f8e3da2a0c7789/resourceGroups/MyRG/providers/Microsoft.Network/virtualNetworkGa
        teways/vnetgw1"
            },
            "localNetworkGateway2": {
              "id":
        "/subscriptions/086cfaa0-0d1d-4b1c-94544-f8e3da2a0c7789/resourceGroups/MyRG/providers/Microsoft.Network/localNetworkGate
        ways/LocalSite"
            },
            "connectionType": "IPsec",
            "routingWeight": 10,
            "sharedKey": "abc123",
            "connectionStatus": "Connected",
            "ingressBytesTransferred": 33509044,
            "egressBytesTransferred": 4142431
          }

### <a name="to-verify-your-connection-by-using-the-azure-portal"></a>若要使用 Azure 入口網站，以驗證您的連線

在 Azure 入口網站，您可以檢視瀏覽至連線的連線狀態。 有多個方法可以進行這項操作。 下列步驟說明瀏覽至您的連線，並確認其中一個方法。

1. 在[Azure 入口網站](http://portal.azure.com)中，按一下 [**所有資源**，並瀏覽至您的虛擬網路閘道器。
2. 在您的虛擬網路閘道器刀，按一下 [**連線**]。 您可以看到每個連線的狀態。
3. 按一下您想要開啟**基本**驗證的連線名稱。 在基本資訊，您可以將連線的相關檢視詳細資訊。 **狀態**是 「 成功 」 和 「 已連線 」，您所做的成功連線時。

    ![請確認連接](./media/vpn-gateway-verify-connection-rm-include/connectionsucceeded.png)