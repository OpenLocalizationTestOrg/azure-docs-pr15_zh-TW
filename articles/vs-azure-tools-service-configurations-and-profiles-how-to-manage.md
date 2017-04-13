<properties
   pageTitle="如何管理服務的安裝和設定 |Microsoft Azure"
   description="瞭解如何使用服務的安裝和設定設定檔 |其中部署環境設定儲存並發佈的雲端服務設定]。"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="how-to-manage-service-configurations-and-profiles"></a>如何管理服務的安裝和設定

## <a name="overview"></a>概觀

Visual Studio 發佈時雲端服務，儲存在兩種設定檔中設定資訊︰ 服務的安裝和設定。 服務設定 （.cscfg 檔案） 儲存 Azure 雲端服務的部署環境的設定。 Azure 時管理您的雲端服務，請使用這些設定的檔案。 另一方面，設定檔 （.azurePubxml 檔案） 市集發佈雲端服務的設定。 您選擇什麼當您使用發佈精靈，並可使用本機 Visual Studio 記錄時，這些設定。 本主題說明如何使用兩種類型的設定檔。

## <a name="service-configurations"></a>服務設定

您可以建立的每個您部署環境中使用多個服務設定。 例如，您可能會建立本機環境您用來執行並測試 Azure 應用程式與其他服務設定生產環境的服務設定。

您可以新增、 刪除、 重新命名]，然後修改根據您的需求這些服務設定。 您可以從 Visual Studio 中，管理這些服務設定，如下圖所示。

![管理服務設定](./media/vs-azure-tools-service-configurations-and-profiles-how-to-manage/manage-service-config.png)

您也可以從角色的屬性頁面，開啟 [**管理設定**] 對話方塊。 若要開啟角色的屬性 Azure 專案中，開啟該角色的快顯功能表，然後選擇 [**內容]**。 在 [**設定**] 索引標籤中，展開 [**服務設定**] 清單中，，然後選取 [**管理**] 以開啟 [**管理設定**] 對話方塊。

### <a name="to-add-a-service-configuration"></a>若要新增的服務設定

1. 在方案總管中開啟 Azure 專案的快顯功能表，然後再選取 [**管理設定**。

    [**管理服務設定**] 對話方塊隨即出現。

1. 若要新增的服務設定，您必須建立一份現有的設定。 若要這麼做，請選擇您想要複製 [名稱] 清單，然後選取 [**建立複本**的設定。

1. （選用）若要提供的服務設定不同的名稱，從 [名稱] 清單中選擇新的服務設定，然後選取 [**重新命名**。 在 [**名稱**] 文字方塊中，輸入您想要用於此服務設定，然後選取**[確定]**的名稱。

    新服務設定檔，名稱為 ServiceConfiguration。[新名稱].cscfg 會新增至 Azure 專案總管] 中。


### <a name="to-delete-a-service-configuration"></a>若要刪除的服務設定

1. 在方案總管中開啟 Azure 專案的快顯功能表，然後再選取 [**管理設定**。

    [**管理服務設定**] 對話方塊隨即出現。

1. 若要刪除的服務設定，請選擇 [設定您要刪除的 [**名稱**] 清單，然後選取 [**移除**]。 對話方塊會顯示，請確認您要刪除此設定。

1. 選取 [**刪除**]。

     從 Azure 專案總管] 中移除服務設定檔。


### <a name="to-rename-a-service-configuration"></a>若要重新命名服務設定

1. 在方案總管中開啟 Azure 專案的快顯功能表，然後再選取 [**管理設定**。

    [**管理服務設定**] 對話方塊隨即出現。

1. 若要重新命名 [服務設定]，從 [**名稱**] 清單中，選擇新的服務設定，然後選取 [**重新命名**。 在 [**名稱**] 文字方塊中，輸入您想要用於此服務設定] 的名稱，然後選取**[確定]**。

    Azure 專案總管] 中變更服務設定檔的名稱。

### <a name="to-change-a-service-configuration"></a>若要變更的服務設定

- 如果您想要變更的服務設定，請開啟您想要變更 Azure 專案中的特定角色的快顯功能表，然後選取 [**摘要資訊**。 請參閱[如何︰ 設定 Visual Studio Azure 雲端服務的角色](https://msdn.microsoft.com/library/azure/hh369931.aspx)如需詳細資訊。

## <a name="make-different-setting-combinations-by-using-profiles"></a>使用設定檔進行其他設定組合

藉由使用設定檔，您可以自動填入**發佈精靈**] 中設定的不同組合不同用途。 例如，您可以有一個設定檔的偵錯時，而另一個版本建立方程式。 在此情況下，您**偵錯**的設定檔必須啟用**IntelliTrace**和**偵錯**設定選取，並有**IntelliTrace**停用和已選取的**版本**設定您的**版本**設定檔。 您也可以使用不同的設定檔若要使用不同的儲存帳戶部署服務。

當您第一次執行精靈時，將會建立預設的設定檔。 Visual Studio 儲存.azurePubXml 副檔名為，也會加入至 Azure**設定檔**資料夾中的 [專案檔案中的設定檔。 如果您手動指定不同的選擇，您稍後再執行精靈時，檔案就會自動更新。 執行下列程序之前，您應該已發佈您的雲端服務至少一次。

### <a name="to-add-a-profile"></a>若要新增設定檔

1. 開啟您 Azure 專案的快顯功能表，然後選取 [**發佈]**。

1. **目標設定檔**] 清單旁，選取 [**儲存設定檔**] 按鈕下, 圖所示。 這會為您建立設定檔。

    ![建立新的設定檔](./media/vs-azure-tools-service-configurations-and-profiles-how-to-manage/create-new-profile.png)

1. 建立設定檔之後， **<...管理 >**清單中選取**目標設定檔**。

    [**管理設定檔**] 對話方塊隨即出現，如下列圖例所示。

    ![[管理設定檔] 對話方塊](./media/vs-azure-tools-service-configurations-and-profiles-how-to-manage/manage-profiles.png)

1. 在 [**名稱**] 清單中，選擇設定檔，然後再選取 [**建立複本**。

1. 選擇 [**關閉**] 按鈕。

    新的設定檔會出現在目標設定檔] 清單中。

1. 在 [**目標設定檔**] 清單中，選取您剛剛建立的設定檔。 發佈精靈設定被填入從您選擇的設定檔的選項。

1. 選取 [**上一個**] 及 [**下一步**] 按鈕，以顯示 [發佈] 精靈的每個頁面，然後自訂 [這個設定檔的設定。 如需的資訊，請參閱[發佈 Azure 應用程式精靈](http://go.microsoft.com/fwlink/p/?LinkID=623085)。

1. 自訂設定之後，請選取 [**下一步**，前往 [設定] 頁面。 當您使用這些設定來發佈服務，或選取 [**儲存**] 旁邊的設定檔清單，就會儲存設定檔。

### <a name="to-rename-or-delete-a-profile"></a>若要重新命名或刪除設定檔

1. 開啟您 Azure 專案的快顯功能表，然後選取 [**發佈]**。

1. 在 [**目標設定檔**] 清單中選取 [**管理**。

1. 在 [**管理設定檔**] 對話方塊中，選取您想要刪除的設定檔，然後選取 [**移除**。

1. 在出現的 [確認] 對話方塊中選取**[確定]**。

1. 選取 [**關閉**]。

### <a name="to-change-a-profile"></a>若要變更設定檔

1. 開啟您 Azure 專案的快顯功能表，然後選取 [**發佈]**。

1. 在 [**目標設定檔**] 清單中，選取您想要變更的設定檔。

1. 選取 [**上一個**] 及 [**下一步**] 按鈕，以顯示每個頁面的 [發佈精靈]，然後再變更 [您要的設定。 如需的資訊，請參閱[發佈 Azure 應用程式精靈](http://go.microsoft.com/fwlink/p/?LinkID=623085)。

1. 變更設定之後，請選取 [**下一步**，前往 [**設定**] 頁面。

1. （選擇性） 選取**發佈**] 發佈雲端服務使用新的設定。 如果您不想要發佈您的雲端服務，此時，關閉 [發佈精靈，Visual Studio 會詢問您是否要儲存設定檔所做的變更。

## <a name="next-steps"></a>後續步驟

若要瞭解如何設定 Azure 從 Visual Studio 專案的其他部分，請參閱[設定 Azure 專案](http://go.microsoft.com/fwlink/p/?LinkID=623075)
