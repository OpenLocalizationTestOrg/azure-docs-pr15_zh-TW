## <a name="how-to-create-a-vnet-in-the-azure-portal"></a>如何建立 VNet Azure 入口網站中

若要建立 VNet，根據上述情況，請遵循下列步驟。

1. 從瀏覽器中，瀏覽至 http://manage.windowsazure.com，如果有需要，請登入您的 Azure 帳戶。
2. 按一下 [**新增** > **網路服務** > **虛擬網路** > **自訂建立**下圖所示。

    ![在管理入口網站建立 VNet](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure1.gif)

3. 在**虛擬網路詳細資料**頁面上，輸入 VNet 的**名稱**，選取其**位置**中，，然後按一下上的步驟 2 到頁面右下角的箭號。 下圖顯示我們的案例的設定。

    ![虛擬網路詳細資料] 頁面](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure2.png)

4. 在 [ **DNS 伺服器及 VPN 連線**] 頁面上指定的名稱和最 9 DNS 伺服器，若要使用的 IP 位址。 如果您未指定的 DNS 伺服器，則您 VNet 會使用內部命名 Azure 所提供的解析度解析度。 我們案例中，我們將不會設定 DNS 伺服器。
5. 如果您想要提供您 VNet 點為網站要有 vpn 才能存取時，啟用**設定點為網站 VPN** ] 核取方塊。 如果您沒有設定點為網站 VPN，您可以將其新增至您 VNet 建立之後，隨時。 我們案例中，我們將不會設定點為網站 VPN。
6. 如果您想要提供您 VNet 和其他 VNet 或內部網路之間的網站-VPN 連線時，啟用**設定網站-VPN** ] 核取方塊，然後指定您要用於連線至**ExpressRoute**或記事，與網路的名稱。 如果您沒有設定網站-VPN，您可以將其新增至您 VNet 建立之後，隨時。 我們案例中，我們將不會設定網站-VPN。
7. 按一下右下角的 [若要進入步驟 3.下圖顯示我們的案例的設定] 頁面上的箭號。

    ![DNS 伺服器及 VPN 連線] 頁面](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure3.png)

8. 在**虛擬網路位址空間**] 頁面上，於**啟動 IP**， *10.0.0.0*變更 VNet 位址空間中，按一下，然後輸入您想要使用的起始位址空間。 我們案例中，輸入*192.168.0.0*。 
9. 在**CIDR （地址計數）**底下，選取 [子網路遮罩的位元數]。 我們案例中，選取 [ *16 (65536)*]。
10. 在**子網路**中，按一下*子網路-1* ，視需要重新命名子網路。 我們案例中，將其重新命名到*主選單*。

    >[AZURE.NOTE] 如果您按一下子網路的 [名稱] 文字方塊外您將無法編輯的名稱，如果的子網路。 若要修正問題，您需要移除子網路按一下其右側的 [X] 按鈕，然後新增新的子網路步驟 13，如下所述。

11. 在第一個子網路**啟動 IP**指定子網路的起始 IP 位址。 我們案例中，輸入*192.168.1.0*。
12. 在**CIDR （地址計數）**底下，選取數字的位元版的第一個子網路的子網路遮罩。 我們案例中，選取 [ *24 (256)*]。
13. 如有必要，請按一下 [新增新的子網路] 的 [**新增子網路**]。 我們案例中，新增子網路，重複步驟 10 到 12 設定 VNet 下圖所示。

    ![虛擬網路位址空白頁面](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure4.png)

14. 按一下右下角的 [頁面] 以建立 VNet [核取符號] 按鈕。 在數秒後您 VNet 會顯示清單中的可用 VNets 下, 圖所示。

    ![新的虛擬網路](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure5.png)