## <a name="how-to-create-a-vnet-using-a-network-config-file-from-powershell"></a>如何建立 VNet，使用 PowerShell 從網路設定檔案

Azure 使用 xml 檔案，以定義所有 VNets 可用的訂閱。 您可以下載此檔案中，並加以修改或刪除現有 VNets，編輯並建立新的。 在此文件中，您可以瞭解如何下載此檔案，稱為 「 網路設定 （或 netcgf） 檔案，並編輯以建立新的 VNet。 核取[Azure 虛擬網路組態結構描述](https://msdn.microsoft.com/library/azure/jj157100.aspx)，若要進一步瞭解網路設定檔。

若要建立 VNet，使用使用 PowerShell netcfg 檔案，請遵循下列步驟。

1. 如果您從未使用 PowerShell 的 Azure，瞭解[如何安裝和設定 Azure PowerShell](../articles/powershell-install-configure.md) ，並遵循指示到登入 Azure，然後選取您的訂閱結束。
2. 從 Azure PowerShell 主控台中，使用**取得 AzureVnetConfig** cmdlet 來執行下列命令以下載網路設定檔。 

        Get-AzureVNetConfig -ExportToFile c:\NetworkConfig.xml

    預期的輸出︰

        XMLConfiguration                                                                                                     
        ----------------                                                                                                     
        <?xml version="1.0" encoding="utf-8"?>...  

3. 開啟您使用任何 XML 或文字編輯器應用程式，上述步驟 2 中儲存的檔案，並尋找**<VirtualNetworkSites>**項目。 如果您有任何已建立的網路，每個網路將會顯示為自己**<VirtualNetworkSite>**項目。
4. 若要建立虛擬網路所述，在這個案例中，新增下列 XML 只在**<VirtualNetworkSites>**項目︰

        <VirtualNetworkSite name="TestVNet" Location="Central US">
          <AddressSpace>
            <AddressPrefix>192.168.0.0/16</AddressPrefix>
          </AddressSpace>
          <Subnets>
            <Subnet name="FrontEnd">
              <AddressPrefix>192.168.1.0/24</AddressPrefix>
            </Subnet>
            <Subnet name="BackEnd">
              <AddressPrefix>192.168.2.0/24</AddressPrefix>
            </Subnet>
          </Subnets>
        </VirtualNetworkSite>

9.  儲存網路設定檔。
10. 從 Azure PowerShell 主控台中，使用**設定 AzureVnetConfig**指令程式，執行下列命令上, 傳網路設定檔。 請注意指令下的成果，您應該會看到**成功** **OperationStatus**底下。 如果是不的情況下，核取錯誤的 xml 檔案。

        Set-AzureVNetConfig -ConfigurationPath c:\NetworkConfig.xml

    以下是預期的輸出上方的命令︰

        OperationDescription OperationId                          OperationStatus
        -------------------- -----------                          ---------------
        Set-AzureVNetConfig  49579cb9-3f49-07c3-ada2-7abd0e28c4e4 Succeeded 
    
11. 從 Azure PowerShell 主控台中，使用**取得 AzureVnetSite**指令程式來確認，執行下列命令以新增新的網路。 

        Get-AzureVNetSite -VNetName TestVNet

    以下是預期的輸出上方的命令︰

        AddressSpacePrefixes : {192.168.0.0/16}
        Location             : Central US
        AffinityGroup        : 
        DnsServers           : {}
        GatewayProfile       : 
        GatewaySites         : 
        Id                   : b953f47b-fad9-4075-8cfe-73ff9c98278f
        InUse                : False
        Label                : 
        Name                 : TestVNet
        State                : Created
        Subnets              : {FrontEnd, BackEnd}
        OperationDescription : Get-AzureVNetSite
        OperationId          : 3f35d533-1f38-09c0-b286-3d07cd0904d8
        OperationStatus      : Succeeded