## <a name="how-to-create-a-classic-vnet-in-the-azure-portal"></a>如何建立傳統的 VNet Azure 入口網站中

若要建立傳統的 VNet 根據上述情況，請遵循下列步驟。

1. 從瀏覽器中，瀏覽至 http://portal.azure.com，如果有需要，請登入您的 Azure 帳戶。
2. 按一下 [**新增** > **網路** > **虛擬網路**，請注意，**選取部署模型**] 清單中已顯示 [**傳統**，，然後按一下 [**建立**] 下, 圖所示。

    ![Azure 入口網站中建立 VNet](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure1.gif)

3. 在**虛擬網路**刀中，輸入**名稱**的 VNet，然後再按一下**位址空間**。 設定您的地址空間設定 VNet 和其第一個子網路]，然後按一下**[確定]**。 下圖顯示我們的案例的 CIDR 區塊設定。

    ![地址空間刀](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure2.png)

4. 按一下 [**資源群組**和選取新增，VNet 資源群組或按一下 [**建立新的資源群組**將 VNet 新增至新的資源群組]。 下圖顯示新的資源群組稱為**TestRG**群組設定的資源。 如需有關資源群組的詳細資訊，請造訪[Azure 資源管理員的概觀](../articles/virtual-network/resource-group-overview.md#resource-groups)。

    ![建立資源群組刀](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure3.png)

5. 如有必要，變更您 VNet 的**訂閱**和**位置**設定。 

6. 如果您不想看到 VNet 中**Startboard**磚，停用**固定至 Startboard**。 

7. 按一下 [**建立**]，並注意下圖所示，名為**建立虛擬網路**圖磚。

    ![在管理入口網站建立 VNet](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure4.png)

8. 若要建立 VNet 時，請等候，當您看到 [下方] 方塊，按一下以新增更多的子網路。

    ![在管理入口網站建立 VNet](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure5.png)

9. 如下所示，您應該會看到您 VNet 的**設定**。 

    ![在管理入口網站建立 VNet](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure6.png)

10. 按一下**子網路** > **新增**]，然後輸入**名稱**，然後指定您子網路**位址範圍 （CIDR 區塊）** ，然後再按一下 [ **[確定]**。 下圖顯示目前案例的設定。

    ![Azure 入口網站中建立 VNet](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure7.gif)