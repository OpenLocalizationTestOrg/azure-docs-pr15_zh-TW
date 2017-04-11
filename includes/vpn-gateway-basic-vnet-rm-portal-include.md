若要建立 VNet 資源管理員部署模型中，使用 Azure 入口網站，請遵循下列步驟。 螢幕擷取畫面所提供的範例。 請務必的值取代為您自己。 如需有關如何使用虛擬網路的詳細資訊，請參閱[虛擬網路概觀](../articles/virtual-network/virtual-networks-overview.md)。

1. 從瀏覽器中，瀏覽至[Azure 入口網站](http://portal.azure.com)，如果有需要，請登入您的 Azure 帳戶。

2. 按一下 [**新增**]。 在 [**搜尋服務商場**] 欄位中，輸入 「 虛擬網路 」。 從 [傳回] 清單中尋找**虛擬網路**，然後按一下以開啟**虛擬網路**刀。

    ![找出虛擬網路資源刀](./media/vpn-gateway-basic-vnet-rm-portal-include/newvnetportal700.png "尋找虛擬網路資源刀")

3. 附近底部的虛擬網路刀，從**選取的部署模型**] 清單中，選取**資源管理員**]，然後按一下 [**建立**。


    ![選取資源管理員](./media/vpn-gateway-basic-vnet-rm-portal-include/resourcemanager250.png "選取資源管理員")

4. 在**建立虛擬網路**防禦，以設定 VNet 設定。 當您在欄位中填滿] 時，紅色驚嘆號會變成綠色的核取記號，欄位中輸入字元時有效。

    ![欄位驗證](./media/vpn-gateway-basic-vnet-rm-portal-include/checkmark300.png "欄位驗證")

5. **建立虛擬網路**刀看起來就像以下的範例。 可能會自動填滿的值。 如果是這樣，請將的值取代為您自己。

    ![建立虛擬網路刀](./media/vpn-gateway-basic-vnet-rm-portal-include/createvnet300.png "建立虛擬網路刀")

6. **名稱**︰ 輸入您的虛擬網路的名稱。

7. **位址空間**︰ 輸入地址。 如果您有多個地址空格，以新增，新增您的第一個位址空間。 您可以建立 VNet 之後的更新版本中，新增其他地址空格。
 
8. **子網路名稱**︰ 新增子網路名稱] 和 [子網路位址範圍。 您可以建立 VNet 之後的更新版本中，新增其他的子網路。

10. **訂閱**︰ 驗證所列的訂閱是正確的項目。 您可以使用下拉式清單，以變更訂閱。

11. **資源群組**︰ 選取現有的資源群組，或建立新輸入新的資源群組的名稱。 如果您要建立新的群組，名稱會依據您計劃的設定值的 [資源] 群組。 如需有關資源群組的詳細資訊，請造訪[Azure 資源管理員的概觀](resource-group-overview.md#resource-groups)。

12. **位置**︰ 選取您 VNet 的位置。 位置會決定您部署到此 VNet 資源所在的位置。

13. 如果您想要能夠輕鬆地在儀表板上尋找您 VNet，然後按一下 [**建立**，請選取 [**固定至儀表板**。
    
    ![釘選至儀表板](./media/vpn-gateway-basic-vnet-rm-portal-include/pintodashboard150.png "釘選至儀表板")

14. 後按一下 [**建立**]，您會看到一個磚會反映您 VNet 的進度您儀表板上。 正在建立 VNet 變成磚。

    ![建立虛擬網路磚](./media/vpn-gateway-basic-vnet-rm-portal-include/deploying150.png "建立虛擬網路磚")