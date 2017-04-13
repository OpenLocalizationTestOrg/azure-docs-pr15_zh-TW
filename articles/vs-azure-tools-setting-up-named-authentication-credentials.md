<properties
   pageTitle="設定命名驗證認證 |Microsoft Azure"
   description="瞭解如何以提供認證的 Visual Studio 使用驗證要求 Azure 發佈至 Azure 應用程式從 Visual Studio 或監控現有的雲端服務。 "
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

# <a name="setting-up-named-authentication-credentials"></a>設定命名的驗證認證

若要發佈至 Azure 應用程式從 Visual Studio 或監控現有的雲端服務，您必須提供 Visual Studio 可以用來驗證 Azure 要求認證。 有幾個地方在 Visual Studio 中，您可以登入提供這些認證。 比方說，從伺服器總管] 中，您可以開啟**Azure**節點的快顯功能表，然後選擇 [**連線至 Azure**。 當您登入時，您 Azure 帳戶相關聯的訂閱資訊是用於 Visual Studio 中，並沒有任何其他您需要執行。

Azure 工具也支援較舊的方式提供的認證，使用訂閱檔案 （.publishsettings 檔案）。 本主題說明中 Azure SDK 2.2 仍然支援這種方式。

下列項目，都需要驗證 Azure。

- 您的訂閱識別碼

- 有效的 X.509 v3 憑證

>[AZURE.NOTE] X.509 v3 憑證金鑰的長度必須至少 2048 位元。 Azure 將拒絕的不符合這項需求或的不是有效的憑證。

Visual Studio 認證為使用您的訂閱識別碼與憑證資料。 在訂閱的檔案 （.publishsettings 檔案），其中包含憑證公開金鑰參考適當的認證。 訂閱檔案可以包含多個訂閱的認證。

本主題稍後的所述，您可以編輯 [**新增/編輯訂閱**] 對話方塊中的訂閱資訊。

如果您想要建立您自己的憑證，您可以參照中[建立和上傳 Azure 管理憑證](https://msdn.microsoft.com/library/windowsazure/gg551722.aspx)的指示進行，然後再以手動方式至[Azure 傳統入口網站](http://go.microsoft.com/fwlink/?LinkID=213885)上載憑證。

>[AZURE.NOTE] 管理您的雲端服務的 Visual Studio 需要這些憑證不相同的認證必須驗證的儲存空間 Azure 服務要求。

## <a name="modify-or-export-authentication-credentials-in-visual-studio"></a>修改或匯出 Visual Studio 中的驗證憑證

您也可以設定、 修改或匯出您的驗證認證，**新的訂閱**在對話方塊中，會顯示您執行下列動作︰

- 在 [**伺服器總管]**中，開啟**Azure**節點的快顯功能表，選擇 [**管理訂閱**、 選擇 [**憑證**] 索引標籤，然後選擇 [**新增**] 或 [**編輯**] 按鈕。

- 當您發佈**發佈 Azure 應用程式**精靈 Azure 雲端服務時，**選擇您的訂閱**清單中，選擇 [**管理**，然後選擇 [憑證] 索引標籤，然後選擇 [**新增**] 或 [**編輯**] 按鈕。

下列程序假設**新的訂閱**] 對話方塊中的已開啟。

### <a name="to-set-up-authentication-credentials-in-visual-studio"></a>若要設定 Visual Studio 中驗證憑證

1. 在**選取現有的憑證**驗證] 清單中，選擇 [憑證]。

1. 選擇 [**複製的完整路徑**] 按鈕。憑證 （.cer 檔案） 的路徑會複製到剪貼簿。

    >[AZURE.IMPORTANT] 若要發佈從 Visual Studio Azure 應用程式，您必須將這個憑證上傳至[Azure 傳統入口網站](http://go.microsoft.com/fwlink/?LinkID=213885)。

1. 若要上傳至[Azure 傳統入口網站](http://go.microsoft.com/fwlink/?LinkID=213885)的憑證︰

    1. 選擇 [Azure 入口網站連結。

         隨即會開啟 [ [Azure 傳統入口網站](http://go.microsoft.com/fwlink/?LinkID=213885)。

    1. [Azure 傳統入口網站](http://go.microsoft.com/fwlink/?LinkID=213885)中，登入，然後選擇 [**雲端服務**] 按鈕。

    1. 選擇您感興趣的雲端服務。

        [服務] 頁面隨即開啟。

    1. 在 [**憑證**] 索引標籤中，選擇 [**上傳**] 按鈕。

    1. 貼上您剛剛建立的.cer 檔案的完整路徑，然後輸入您所指定的密碼。
