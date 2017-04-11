<properties
   pageTitle="開始使用私人範本 |Microsoft Azure"
   description="新增、 管理及共用您私人的範本，使用 Azure 入口網站、 Azure CLI 或 PowerShell。"
   services="marketplace-customer"
   documentationCenter=""
   authors="VybavaRamadoss"
   manager="asimm"
   editor=""
   tags="marketplace, azure-resource-manager"
   keywords=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/18/2016"
   ms.author="vybavar"/>

# <a name="get-started-with-private-templates-on-the-azure-portal"></a>開始使用 Azure 入口網站上的私人範本

[Azure 資源管理員](../resource-group-authoring-templates.md)範本是用來定義您的部署中的宣告式範本。 您可以定義的解決方案，部署資源，並指定參數和變數，讓您輸入不同環境中的值。 範本包含 JSON 和可用來建構您的部署中的值的運算式。

您可以[Azure Marketplace](https://azure.microsoft.com/marketplace/)的副檔名為使用新**範本**功能[Azure 入口網站](https://portal.azure.com)以及**Microsoft.Gallery**資源提供者，若要讓使用者建立、 管理及部署私人的個人文件庫範本。

這份文件會引導您新增、 管理及共用私人**範本**使用 Azure 入口網站。

## <a name="guidance"></a>指導方針

下列建議可協助您利用完整的**範本**時使用您的方案︰

- **範本**是封裝的資源，含有資源管理員範本和其他的中繼資料。 它行為上非常類似市場中的項目。 主要差異就是私人的項目，而不是公用服務商場項目。
- **範本**文件庫適用於需要自訂部署使用者。
- **範本**適用於需要簡單存放庫 Azure 中的使用者。
- 開始使用現有的資源管理員範本。 尋找[github](https://github.com/Azure/azure-quickstart-templates)或[匯出範本](../resource-manager-export-template.md)中的範本，從現有的資源群組。
- **範本**會受限於發行的使用者。 會顯示每個人都讀取權限的 publisher 名稱。
- **範本**資源管理員資源，而且不能一次發佈重新命名。

## <a name="add-a-template-resource"></a>新增範本資源

有兩種方式 Azure 入口網站中建立**範本**資源。

### <a name="method-1--create-a-new-template-resource-from-a-running-resource-group"></a>方法 1︰ 建立新的範本資源執行的資源群組

1. 瀏覽至 Azure 入口網站上現有的資源群組。 選取 [**設定**] 中的 [**匯出範本**]。
2. 後要匯出的資源管理員範本，請將其儲存至**範本**存放庫使用**儲存的範本**] 按鈕。 找出完整的詳細資料匯出範本的[此處](../resource-manager-export-template.md)。
<br /><br />
![資源群組匯出](media/rg-export-portal1.PNG)  <br />

3. 選取 [**儲存至範本**] 命令按鈕。
<br /><br />

4. 輸入下列資訊︰

    - 名稱 – 範本物件的名稱 (請注意︰ 這是根據 Azure 資源管理員名稱。 所有命名的限制會套用，其無法變更一次建立）。
    - 說明-瞭解範本的快速摘要。

    ![儲存範本](media/save-template-portal1.PNG)  <br />

5. 按一下 [**儲存**]。

    > [AZURE.NOTE] 匯出範本刀匯出的資源管理員範本具有錯誤，但您仍然可以將此資源管理員範本儲存至範本時，會顯示通知。 請確定您核取，然後再重新部署匯出的資源管理員範本修正任何資源管理員範本問題。

### <a name="b-method-2--add-a-new-template-resource-from-browse"></a>B。 方法 2︰ 新增新的範本資源的 [瀏覽

您也可以新增新的**範本**可用使用 + 新增命令按鈕**瀏覽 > 範本**。 您必須提供一個名稱、 描述和資源管理員範本 JSON。

![新增範本](media/add-template-portal1.PNG)  <br />

> [AZURE.NOTE] Microsoft.Gallery 是租用戶基礎 Azure 資源提供者。 範本資源連結給使用者的建立者。 不屬於任何特定的訂閱。 需要部署範本時，只選擇訂閱。

## <a name="view-template-resources"></a>檢視範本的資源

所有可用的**範本**您能看到在**瀏覽 > 範本**。 這包含與不同層級的權限與您共用的項目以及您建立的**範本**。 下列[存取控制](#access-control-for-a-tenant-resource-provider)一節中的更多詳細資料。

![檢視範本](media/view-template-portal1.PNG)  <br />

您可以檢視**範本**的詳細資料，即可將清單中的項目。

![檢視範本](media/view-template-portal2c.png)  <br />

## <a name="edit-a-template-resource"></a>編輯範本資源

您可以啟動編輯流程**範本**，以滑鼠右鍵按一下 [瀏覽] 清單中的項目，或選擇 [編輯] 命令] 按鈕。

![編輯範本](media/edit-template-portal1a.PNG)  <br />

您可以編輯描述或資源管理員範本文字。 您無法編輯名稱，因為它是資源管理員資源名稱。 當您編輯資源管理員範本 JSON 我們將會以確保驗證的是有效的 JSON。 選擇**[確定]** ，然後**儲存**儲存更新的範本。

![編輯範本](media/edit-template-portal2a.PNG)  <br />

**範本**會儲存之後，您會看到確認通知。

![編輯範本](media/edit-template-portal3b.png)  <br />

## <a name="deploy-a-template-resource"></a>部署範本資源

您可以部署有**讀取**權限任何**範本**。 部署流程啟動標準 Azure 範本部署刀。 填寫以進行部署資源管理員範本參數的值。

![部署範本](media/deploy-template-portal1b.png)  <br />

## <a name="share-a-template-resource"></a>共用範本資源

**範本**資源可以與您的同事共用。 共用與 round 類似[Azure 上任何資源](../active-directory/role-based-access-control-configure.md)的角色指派。 **範本**擁有者會提供與範本資源可以互動給其他使用者的權限。 個人或群組的人員共用**範本**會無法看到資源管理員範本及圖庫屬性。

### <a name="access-control-for-the-microsoftgallery-resources"></a>Microsoft.Gallery 資源的存取控制

角色 | 權限
---|----
擁有者 | 範本資源包括共用上的 「 完全控制 」 可讓
閱讀程式 | 允許讀取] 和 [Execute(Deploy) 範本的資源
參與者 | 允許編輯和刪除權限範本的資源。 使用者無法與其他人共用範本

選取 [**共用**] 瀏覽項目上按一下滑鼠右鍵，或檢視刀的特定項目上。 這會啟動的共用體驗。

![共用範本](media/share-template-portal1a.png)  <br />

 您現在可以選擇角色的使用者或群組能夠存取特定的**範本**。 可用的角色是擁有者、 讀取及參與者。 上述[存取控制](#access-control-for-a-tenant-resource-provider)一節中的更多詳細資料。

![共用範本](media/share-template-portal2b.png)  <br />

![共用範本](media/share-template-portal3b.png)  <br />

按一下 [**選取**及**[確定**]。 您現在可以看到的使用者或您新增到資源的群組。

![共用範本](media/share-template-portal4b.png)  <br />

> [AZURE.NOTE] 範本中相同的 Azure Active Directory 租用戶只使用者與群組共用。 如果您的電子郵件地址的不是您的租用戶共用範本，邀請便會傳送要求使用者加入以來賓身分的租用戶。

## <a name="next-steps"></a>後續步驟

- 若要瞭解如何建立資源管理員範本，請參閱[撰寫範本](../resource-group-authoring-templates.md)
- 若要瞭解您可以使用資源管理員範本中的功能，請參閱[範本函數](../resource-group-template-functions.md)
- 範本的設計，請參閱[設計 Azure 資源管理員範本的最佳作法](../best-practices-resource-manager-design-templates.md)
