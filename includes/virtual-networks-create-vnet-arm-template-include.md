## <a name="download-and-understand-the-arm-template"></a>下載並瞭解 ARM 範本

您可以下載現有 ARM 範本建立 VNet 和兩個子網路從 github、 進行任何變更，您可能會想，並重複使用它。 若要這麼做，請遵循下列步驟。

1. 瀏覽至[[範例範本] 頁面](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets)。
2. 按一下 [ **azuredeploy.json**，，然後按一下 [ **RAW**。
3. 將檔案儲存到您的電腦上的本機資料夾。
4. 如果您熟悉 ARM 範本，請跳到步驟 7。
5. 開啟您儲存的檔案，並查看**參數**中列 5 的內容。 ARM 範本參數提供可以填寫部署期間的值的版面配置區。

    | 參數 | 描述 |
    |---|---|
    | **位置** | 將會建立 VNet azure 區域 |
    | **vnetName** | 新的 VNet 名稱 |
    | **addressPrefix** | VNet，CIDR 格式的位址空間 |
    | **subnet1Name** | 第一個 VNet 名稱 |
    | **subnet1Prefix** | 第一個子網路 CIDR 區塊 |
    | **subnet2Name** | 第二個 VNet 名稱 |
    | **subnet2Prefix** | 第二個子網路 CIDR 區塊 |

    >[AZURE.IMPORTANT] ARM 範本中 github 維持可以變更一段時間。 請確定您使用之前，先檢查範本。
    
6. 核取 [**資源**] 下的內容，請注意下列動作︰

    - [**類型**]。 範本所建立的資源類型。 在此情況下， **Microsoft.Network/virtualNetworks**，代表 VNet。
    - **名稱**。 資源名稱。 請注意， **[parameters('vnetName')]**，表示名稱會由使用者或參數檔案提供做為輸入部署時使用。
    - [**屬性**]。 資源屬性的清單。 此範本是 VNet 建立期間使用的地址空間和子網路屬性。

7. 瀏覽回[[範例範本] 頁面](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets)。
8. 按一下 [ **azuredeploy paremeters.json**，，然後按一下 [ **RAW**。
9. 將檔案儲存到您的電腦上的本機資料夾。
10. 開啟您儲存的檔案，然後編輯參數的值。 部署案例中所述 VNet 使用下列的值。

        {
          "location": {
            "value": "Central US"
          },
          "vnetName": {
              "value": "TestVNet"
          },
          "addressPrefix": {
              "value": "192.168.0.0/16"
          },
          "subnet1Name": {
              "value": "FrontEnd"
          },
          "subnet1Prefix": {
            "value": "192.168.1.0/24"
          },
          "subnet2Name": {
              "value": "BackEnd"
          },
          "subnet2Prefix": {
              "value": "192.168.2.0/24"
          }
        }

11. 儲存檔案。
  