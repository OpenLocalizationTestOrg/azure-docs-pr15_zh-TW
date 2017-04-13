<properties 
   pageTitle="新增行動服務使用 Visual Studio 中的 [已連線服務 |Microsoft Azure"
   description="新增行動服務使用 Visual Studio 新增已連線服務] 對話方塊"
   services="visual-studio-online"
   documentationCenter="na"
   authors="mlhoop"
   manager="douge"
   editor="" />
<tags 
   ms.service="visual-studio-online"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="mobile"
   ms.date="12/16/2015"
   ms.author="mlearned" />

# <a name="adding-mobile-services-by-using-visual-studio-connected-services"></a>新增行動服務使用 Visual Studio 已連線服務

使用 Visual Studio 2015，您可以連線至 Azure 行動服務使用 [**新增連線服務**] 對話方塊。 您可以從任何 C# 用戶端應用程式、 任何 JavaScript 應用程式或跨平台 Cordova 應用程式連線。 當您連線時，您可以建立及存取資料、 建立自訂的 Api 和排程的工作，或新增支援推播通知。  已連線的服務] 作業會將所有適當的參考資料和連線程式碼。 您也可以利用內建的支援驗證使用各種不同的常用的身分識別配置、 Azure AD，例如 Facebook、 Twitter 和 Microsoft 帳戶。

## <a name="supported-project-types"></a>支援的專案類型

>[AZURE.NOTE] 在 Visual Studio 2015，不支援將 Azure 行動服務新增至 Windows 通用 (Windows 10) 的專案中，使用 [新增已連線服務] 對話方塊。 您可以新增 Azure 行動服務安裝適當的封裝使用 NuGet 封裝管理員為您的專案。

若要在下列專案類型中連線至 Azure 行動服務，您可以使用 [已連線服務] 對話方塊。

- .NET Windows 8.1 市集、 手機與通用的應用程式的專案

- JavaScript Windows 8.1 市集、 手機與通用的應用程式的專案

- 建立用於 Apache Cordova Visual Studio 工具的專案


## <a name="connect-to-azure-mobile-services-using-the-add-connected-services-dialog"></a>連線至 Azure 行動服務使用 [新增已連線服務] 對話方塊

1. 請確定您有 Azure 帳戶。 如果您沒有安裝 Azure 帳戶，您可以註冊[免費試用版](http://go.microsoft.com/fwlink/?LinkId=518146)。

1. 開啟 [**新增已連線服務**] 對話方塊。
 - .NET 應用程式，Visual Studio 中開啟專案，在方案總管中開啟 [**參考**] 節點的操作功能表，然後選擇**新增連線的服務**
 
        ![Connecting to Azure Mobile Service](./media/vs-azure-tools-connected-services-add-mobile-services/IC797635.png)

 - Apache Cordova 應用程式的專案，Visual Studio 中開啟專案，在方案總管中開啟專案節點的內容功能表，然後選擇 [**新增連線的服務**。

1. 在 [**新增連線服務**] 對話方塊中，選擇**Azure 行動服務**]，然後選擇 [**設定**] 按鈕。 您可能會提示您登入 Azure，如果您還沒有這麼做。

    ![新增 Azure 的行動訊息服務](./media/vs-azure-tools-connected-services-add-mobile-services/IC797636.png)

1. 如果有的話，請在 [ **Azure 行動服務**] 對話方塊中，選擇現有的行動訊息服務。 若要建立新的 Azure 行動訊息服務，請完成下列程序。 否則，請跳至下一個步驟。

    若要建立新的行動訊息服務帳戶︰
    1. 選擇 [**建立服務**的連結] 對話方塊的底部。
        ![新增新連線的行動訊息服務](./media/vs-azure-tools-connected-services-add-mobile-services/IC797637.png)




    2. 在 [**建立行動訊息服務**] 對話方塊中，您可以選擇 JavaScript 後端行動訊息服務或.NET 的後端行動訊息服務**執行階段**下拉式清單中。 
  
        ![建立行動訊息服務](./media/vs-azure-tools-connected-services-add-mobile-services/IC797638.png)

        JavaScript 後端服務是簡單而強大。 如果您建立 JavaScript 的後端行動訊息服務時，伺服器端 JavaScript 程式碼儲存在雲端，但您可以使用伺服器總管或 Azure 管理入口網站中編輯伺服器指令碼。 

        .NET 的後端行動訊息服務可讓您的完整功能和彈性 Web API 及實體架構。 如果您建立.NET 的後端行動訊息服務時，專案會為您建立及新增至您的方案。 

    1. 選擇您要行動訊息服務，**區域**，然後輸入伺服器的使用者名稱和密碼。
 
    1. 輸入所有必要的資訊後，選擇 [**建立**] 按鈕，以建立行動訊息服務。
    2. 新的行動訊息服務應該會出現 [ **Azure 行動服務**] 對話方塊中的 [服務] 清單中。 在清單中選擇新的行動訊息服務，然後選擇 [**新增**] 按鈕，將服務新增至您的專案。
    

1. 檢閱快速入門頁面會隨即出現，並瞭解如何修改您的專案。 每當您新增連線的服務，快速入門] 頁面會顯示在瀏覽器。 您可以檢閱建議的後續步驟和程式碼範例，或切換到有何改變頁面，請參閱什麼參照已新增至您的專案，以及如何修改您的程式碼和設定檔案。

1. 使用輔助線的程式碼範例，開始撰寫程式碼，以存取您的行動訊息服務 ！

## <a name="how-your-project-is-modified"></a>如何修改您的專案

Visual Studio 如何修改您的專案，則專案類型而定。 C# 用戶端應用程式，請參閱[有何改變 – C# 專案](http://go.microsoft.com/fwlink/p/?LinkId=513119)。 JavaScript 用戶端應用程式，請參閱[有何改變 – JavaScript 專案](http://go.microsoft.com/fwlink/p/?LinkId=513120)。 Cordova 應用程式，請參閱[有何改變 – Cordova 專案](http://go.microsoft.com/fwlink/p/?LinkId=513116)。


##<a name="next-steps"></a>後續步驟

提出問題並取得說明︰ 

 - [MSDN 論壇︰ Azure 行動服務](https://social.msdn.microsoft.com/forums/azure/home?forum=azuremobile)

 - [在 Microsoft Azure 小組部落格 azure 行動服務](https://azure.microsoft.com/blog/topics/mobile/)

 - [在 azure.microsoft.com azure 行動服務](https://azure.microsoft.com/services/mobile-services/)

 - [Azure.microsoft.com azure 行動服務文件](https://azure.microsoft.com/documentation/services/mobile-services/)



