## <a name="deploy-the-arm-template-by-using-click-to-deploy"></a>藉由按一下部署部署手臂範本

您可以重複使用預先定義的手錶範本上傳至維持 microsoft github 存放庫，並開啟社群。 這些範本可以部署直接登出 github，或下載並修改以符合您的需求。 若要部署的範本，建立兩個子網路 VNet，遵循下列步驟。

1. 從瀏覽器中，瀏覽至[https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates)。
2. 範本] 清單中向下捲動並按一下**101 vnet-兩子**。 檢查**README.md**檔案，如下所示。

    ![在 [github READEME.md 檔案](./media/virtual-networks-create-vnet-arm-template-click-include/figure1.png)

3. 按一下 [**部署至 Azure**]。 如有需要，輸入 Azure 登入認證。 
4. 在**參數**刀中，輸入您要用來建立您的新 VNet 的值，然後按一下**[確定]**。 下圖顯示我們的案例的值。

    ![ARM 範本參數](./media/virtual-networks-create-vnet-arm-template-click-include/figure2.png)

4. 按一下 [**資源群組**並選取要新增至 VNet 資源群組或按一下 [**建立新**VNet 加入新的資源群組。 下圖顯示新的資源群組稱為**TestRG**群組設定的資源。

    ![資源群組](./media/virtual-networks-create-vnet-arm-template-click-include/figure3.png)

5. 如有必要，變更您 VNet 的**訂閱**和**位置**設定。
6. 如果您不想看到 VNet 中**Startboard**磚，停用**固定至 Startboard**。
5. 按一下**Leagl 字詞**、 閱讀條款內容，然後按一下 [同意 [**購買**。 
6. 按一下 [**建立**]，建立 VNet]。

    ![在預覽入口網站提交部署磚](./media/virtual-networks-create-vnet-arm-template-click-include/figure4.png)

7. 一旦完成部署後，按一下 [ **TestVNet** > **所有設定** > **子網路**，查看 [子網路] 屬性，如下所示。

    ![在 [預覽] 入口網站中建立 VNet](./media/virtual-networks-create-vnet-arm-template-click-include/figure5.gif)