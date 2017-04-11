## <a name="how-to-create-a-vnet-using-a-network-config-file-in-the-azure-portal"></a>如何建立使用 Azure 入口網站中的網路設定檔 VNet

Azure 使用 xml 檔案，以定義所有 VNets 可用的訂閱。 您可以下載此檔案中，並加以修改或刪除現有 VNets，編輯並建立新的。 在此文件中，您可以瞭解如何下載此檔案，稱為 「 網路設定 （或 netcgf） 檔案，並編輯以建立新的 VNet。 核取[Azure 虛擬網路組態結構描述](https://msdn.microsoft.com/library/azure/jj157100.aspx)，若要進一步瞭解網路設定檔。

若要建立 VNet，使用 netcfg 檔案透過 Azure 入口網站，請遵循下列步驟。

1. 從瀏覽器中，瀏覽至 http://manage.windowsazure.com，如果有需要，請登入您的 Azure 帳戶。
2. 向下捲動的服務] 清單上，按一下在**網路**上如下所示。

    ![Azure 虛擬網路](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure1.gif)

3. 在頁面底部，按一下 [**匯出**] 按鈕，如下所示。

    ![[匯出] 按鈕](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure2.png)

4. 在 [**匯出網路設定**] 頁面上，選取您要匯出的虛擬網路設定的訂閱，然後按一下核取記號按鈕，在頁面的左側角。
5. 請遵循**NetworkConfig.xml**檔案儲存您在瀏覽器的指示。 請確定您記下您要儲存檔案的位置。
6. 開啟您在步驟 5 上方使用任何 XML 或文字編輯器應用程式中儲存的檔案，並尋找**<VirtualNetworkSites>**項目。 如果您有任何已建立的網路，每個網路將會顯示為自己**<VirtualNetworkSite>**項目。
7. 若要建立虛擬網路所述，在這個案例中，新增下列 XML 只在**<VirtualNetworkSites>**項目︰

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

8.  儲存網路設定檔。
9.  Azure 入口網站，在頁面] 的左側角中按一下 [**新增**]，然後按一下**網路服務**]，然後按一下**虛擬網路**]，然後按一下**匯入設定**下圖所示。

    ![匯入設定](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure3.gif)

10.  在**匯入網路設定檔**頁面上，按一下 [**瀏覽的檔案...**]，然後瀏覽至您在上述步驟 8 中儲存檔案的資料夾，選取檔案，，然後按一下**開啟**。 網頁應該類似下圖中使用。 在右下角的頁面，按一下 [箭號] 按鈕，移至下一個步驟。

    ![匯入網路的設定檔頁面](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure4.png)

11.   **建立您的網路**] 頁面中，注意項目為您的新 VNet 下, 圖所示。

    ![建立您的網路] 頁面](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure5.png)

12.   按一下右下角的 [頁面] 以建立 VNet [核取符號] 按鈕。 在數秒後您 VNet 會顯示清單中的可用 VNets，如下圖所示。

    ![新的虛擬網路](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure6.png)