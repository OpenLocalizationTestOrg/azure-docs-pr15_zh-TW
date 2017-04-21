

本文將示範如何部署 Azure 虛擬機器縮放比例設定使用 Visual Studio 資源群組部署。


[Azure 虛擬機器比例集](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview/)是部署及管理類似的虛擬機器，輕鬆地整合式的選項，自動調整大小的集合，以及負載平衡 Azure 計算資源。 您可以佈建及部署 VM 縮放比例設定使用[Azure 資源管理員 (ARM) 範本](https://github.com/Azure/azure-quickstart-templates)。 ARM 範本可以使用 PowerShell Azure CLI 其餘部署，同時直接從 Visual Studio。 Visual Studio 提供一組範例範本，可以部署為 Azure 資源群組部署專案的一部分。

Azure 資源群組的部署是群組在一起，及發佈單一的部署作業的一組相關的 Azure 資源的方法。 您可以進一步瞭解這些以下︰[建立及部署 Visual Studio 透過 Azure 資源群組](../vs-azure-tools-resource-groups-deployment-projects-create-deploy/)。

## <a name="pre-requisites"></a>必要條件

若要開始部署 Visual Studio 中 VM 縮放比例設定您需要下列項目︰

- Visual Studio 2013 或 2015
- Azure SDK 2.7 或將 2.8 顯示

附註︰ 這些指示假設您使用的 Visual Studio 2015 與[Azure SDK 2.8](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/)。

## <a name="creating-a-project"></a>建立專案

1. 在 Visual Studio 2015 中建立新專案，選擇 [**檔案 |新增 |專案**。

    ![新的檔案][file_new]

2. 下**Visual C# |雲端**，選擇 [建立部署 ARM 範本專案**Azure 資源管理員**]。

    ![建立專案][create_project]

3.  從範本清單中，選取 [Linux] 或 [Windows 虛擬機器縮放比例設定範本。

    ![選取範本][select_Template]

4. 建立專案您會看到 PowerShell 部署指令碼、 Azure 資源管理員範本和參數檔案的虛擬機器縮放比例設定。

    ![方案總管][solution_explorer]

## <a name="customize-your-project"></a>自訂您的專案

現在您可以編輯來自訂您的應用程式的需求，例如新增 VM 副檔名屬性或編輯負載平衡規則範本。 依預設 VM 縮放比例設定範本設定為部署 AzureDiagnostics 副檔名就可輕鬆新增自動調整大小的規則。 也會部署負載平衡器的公用 IP 位址，可讓您連線至 SSH (Linux) 或 RDP (Windows) – VM 執行個體的前端連接埠範圍會從 50000，開始輸入 NAT 規則以設定表示若是 Linux，如果您 SSH 連接埠 50000 的公用 IP 位址 （或的網域名稱） 將路由至連接埠 22 的縮放比例設定中的第一個 VM。 連線至連接埠 50001 將會路由至第二個 VM 連接埠 22 等等。

 若要編輯的 Visual Studio 範本的好方法是使用 JSON 大綱以組織參數、 變數和資源。 瞭解結構描述的 Visual Studio 可以指出您的範本中的錯誤之前將其部署。

![JSON 總管][json_explorer]

## <a name="deploy-the-project"></a>部署專案

6. 若要建立 VM 縮放比例設定資源 Azure 部署 ARM 範本。 以滑鼠右鍵按一下專案節點，選擇 [**部署 |新的部署**。

    ![部署範本][5deploy_Template]

7. 在 [部署至資源群組] 對話方塊中，選取您的訂閱。

    ![部署範本][6deploy_Template]

8. 您也可以從這裡開始建立新的 Azure 資源群組部署範本。

    ![新的資源群組][new_resource]

9. 下一步選取 [**編輯參數]**按鈕，輸入將會傳送至您的範本，例如使用者名稱的特定值參數，OS 密碼，才能建立部署。

    ![編輯參數][edit_parameters]

10. 現在按一下 [**部署**]。 **輸出**視窗會顯示部署進度。 請注意，該動作會執行**部署 AzureResourceGroup.ps1**指令碼。

    ![輸出視窗][output_window]

## <a name="exploring-your-vm-scale-set"></a>探索您 VM 縮放設定

部署完成後，您可以檢視新的 VM 縮放比例設定在 Visual Studio**雲端總管**（重新整理清單）。 雲端的檔案總管，可讓您管理 Visual Studio 中的 Azure 資源時開發應用程式。 您也可以檢視您 VM 縮放比例設定 Azure 入口網站和 Azure 資源檔案總管中。

![雲端的檔案總管][cloud_explorer]

 入口網站提供視覺化方式管理與網頁瀏覽器中，您 Azure 基礎結構時 Azure 資源總管提供輕鬆總管和偵錯 Azure 資源，將 「 執行個體檢視 」 進行視窗，而且也顯示您正在查看的資源的 PowerShell 命令的最佳方式。 當 VM 縮放比例設定在預覽中時，資源檔案總管會顯示最詳細的資料，您 VM 小數位數組。

## <a name="next-steps"></a>後續步驟

當您已成功部署透過 Visual Studio VM 比例集您可以進一步自訂您的專案，以符合您的應用程式的需求。 例如設定自動調整大小，藉由新增獲得深入見解資源、 基礎結構加入您的範本，例如獨立 Vm，或部署使用副檔名為自訂指令碼應用程式。 [Azure 快速入門範本](https://github.com/Azure/azure-quickstart-templates)GitHub 存放庫 （搜尋 「 vmss 」） 中找不到範例範本好來源。

[file_new]: ./media/virtual-machines-common-scale-sets-visual-studio/1-FileNew.png
[create_project]: ./media/virtual-machines-common-scale-sets-visual-studio/2-CreateProject.png
[select_Template]: ./media/virtual-machines-common-scale-sets-visual-studio/3b-SelectTemplateLin.png
[solution_explorer]: ./media/virtual-machines-common-scale-sets-visual-studio/4-SolutionExplorer.png
[json_explorer]: ./media/virtual-machines-common-scale-sets-visual-studio/10-JsonExplorer.png
[5deploy_Template]: ./media/virtual-machines-common-scale-sets-visual-studio/5-DeployTemplate.png
[6deploy_Template]: ./media/virtual-machines-common-scale-sets-visual-studio/6-DeployTemplate.png
[new_resource]: ./media/virtual-machines-common-scale-sets-visual-studio/7-NewResourceGroup.png
[edit_parameters]: ./media/virtual-machines-common-scale-sets-visual-studio/8-EditParameter.png
[output_window]: ./media/virtual-machines-common-scale-sets-visual-studio/9-Output.png
[cloud_explorer]: ./media/virtual-machines-common-scale-sets-visual-studio/12-CloudExplorer.png