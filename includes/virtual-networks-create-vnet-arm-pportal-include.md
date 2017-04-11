## <a name="how-to-create-a-vnet-in-the-azure-portal"></a>如何建立 VNet Azure 入口網站中

若要建立 VNet，使用 Azure 預覽入口網站，根據上述情況，請遵循下列步驟。

1. 從瀏覽器中，瀏覽至 http://portal.azure.com，如果有需要，請登入您的 Azure 帳戶。
2. 按一下 [**新增** > **網路** > **虛擬網路**，然後按一下 [從**選取的部署模型**] 清單中的 [**資源管理員**，然後按一下 [**建立**] 下圖所示。

    ![Azure 入口網站中建立 VNet](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure1.gif)

3. 在**建立虛擬網路**防禦，以設定 VNet 設定，如下圖所示。

    ![建立虛擬網路刀](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure2.png)

4. 按一下 [**資源群組**並選取資源群組新增 VNet，或按一下 [**建立新**VNet 加入新的資源群組。 下圖顯示新的資源群組稱為**TestRG**群組設定的資源。 如需有關資源群組的詳細資訊，請造訪[Azure 資源管理員的概觀](../articles/resource-group-overview.md#resource-groups)。

    ![資源群組](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure3.png)

5. 如有必要，變更您 VNet 的**訂閱**和**位置**設定。 

6. 如果您不想看到 VNet 中**Startboard**磚，停用**固定至 Startboard**。 

7. 按一下 [**建立**]，並注意下圖所示，名為**建立虛擬網路**圖磚。

    ![建立虛擬網路磚](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure4.png)

8. 若要建立 VNet 時，請等候，然後在**虛擬網路**刀中，按一下 [**所有設定** > **子網路** > **新增**如下所示。

    ![Azure 入口網站中新增子網路](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure5.gif)

9. 指定*後端*子網路的子網路設定，如下所示，然後再按一下**[確定]**。 

    ![子網路設定](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure6.png)

10. 下圖所示，請注意到子網路清單。

    ![在 [VNet 子網路清單](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure7.png)
