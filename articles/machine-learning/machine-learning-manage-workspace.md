<properties
    pageTitle="管理電腦學習工作區 |Microsoft Azure"
    description="管理存取權 Azure 電腦學習工作區，並部署及管理毫升 API web 服務"
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/05/2016"
    ms.author="garye"/>


# <a name="manage-an-azure-machine-learning-workspace"></a>管理 Azure 電腦學習工作區

>[AZURE.NOTE] 本文中的程序是 Azure 電腦學習傳統 Web 服務的相關項目。 管理 Web 服務機器學習 Web 服務入口網站中的資訊，請參閱[管理 Web 服務使用 Azure 機器學習 Web 服務入口網站](machine-learning-manage-new-webservice.md)。

您可以使用 Azure 傳統入口網站，來管理您的電腦學習工作區︰

- 監視使用工作區的方式
- 設定工作區，以允許或拒絕存取
- 管理工作區中建立 Web 服務
- 刪除工作區

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

此外，[儀表板] 索引標籤提供您的工作區的使用方式的概觀與快速檢視工作區資訊。  

> [AZURE.TIP] 您可以在 Azure 電腦學習 Studio 中，在 [ **WEB 服務**] 索引標籤上新增、 更新或刪除電腦學習 Web 服務。

管理工作區︰

1.  使用您的 Microsoft Azure 帳戶[Azure 傳統入口網站](https://manage.windowsazure.com/)登入-使用與 Azure 訂閱相關聯的帳戶。
2.  在 Microsoft Azure 服務面板中，按一下 [**電腦學習**]。
3.  按一下您想要管理的工作區。

[工作區] 頁面中有三個索引標籤︰

- **儀表板**-可讓您檢視工作區的使用狀況及資訊
- **設定**-可讓您管理存取工作區
- **WEB 服務**可讓您管理 Web 服務的已發佈的工作區

## <a name="to-monitor-how-the-workspace-is-being-used"></a>監視工作區中使用的方式

按一下 [**儀表板**] 索引標籤。

從儀表板，您可以檢視整體的使用情況的工作區，並取得快速檢視的工作區資訊。

- **計算**圖表會顯示正在使用的工作區中的計算資源。 變更檢視，以顯示相對和絕對的值，而您可以變更圖表中顯示的時間範圍。
- **使用概觀**會顯示正在使用的工作區中的 Azure 儲存空間。
- **快速檢視**提供的摘要工作區資訊和有用的連結。

> [AZURE.NOTE] **登入毫升 Studio**連結會開啟電腦學習 Studio 中使用目前登入 Microsoft 帳戶。 您用來建立工作區登入 Azure 傳統的入口網站的 Microsoft 帳戶自動沒有開啟該工作區的權限。 若要開啟工作區，您必須登入 Microsoft 帳戶所定義的工作區中，擁有者，或您要接收邀請加入工作區擁有者。


## <a name="to-grant-or-suspend-access-for-users"></a>若要授與或暫停使用者的存取 ##

按一下 [**設定**] 索引標籤。

從 [設定] 索引標籤中，您可以︰

- 暫停的存取權的電腦學習工作區，按一下 [拒絕]。 使用者將無法在電腦學習 Studio 中開啟工作區。 若要還原 access，按一下 [允許]。

若要管理有權存取電腦學習 Studio 中的工作區的其他帳戶，請按一下 [**登入毫升 Studio** **儀表板**] 索引標籤 （請參閱關於**登入毫升 Studio**先前記事）。 這會電腦學習 Studio 中開啟工作區。 從這裡開始，按一下 [**設定**] 索引標籤，然後**使用者**。 您可以按一下**[邀請更多使用者**授權讓使用者存取工作區中，或選取使用者，然後按一下 [**移除**]。


## <a name="to-manage-web-services-in-this-workspace"></a>若要管理此工作區中的 web 服務

按一下 [ **WEB 服務**] 索引標籤。

隨後便會顯示此工作區的發佈 web 服務的清單。
若要管理 web 服務，請按一下以開啟 [Web 服務] 頁面的清單] 中的名稱。

Web 服務可能會有一或多個定義的端點。

- 您可以定義端點除了 「 預設 」 結束點。 若要新增端點，請按一下 [**管理端點**底部的儀表板，開啟 [Azure 機器學習 Web 服務入口網站。

- 若要刪除的端點，（您無法刪除 「 預設 」 結束點），按一下 [結束點] 列的開頭] 核取方塊，然後按一下 [**刪除**]。 這會移除 Web 服務端點。

    > [AZURE.NOTE] 如果應用程式使用的 web 服務端點刪除端點時，應用程式會在下一次嘗試存取服務時收到錯誤。

按一下 [Web 服務端點，將其開啟的名稱。 

從儀表板，您可以檢視一段時間的整體 Web 服務使用方式。 您可以選取要檢視的使用圖表右上角的 [期間] 下拉式清單] 功能表的期間。 儀表板顯示下列資訊︰

- **要求透過時間**會顯示在選定的時間週期要求的數字的步驟圖形。 它可協助您識別如果您遇到特殊圖文集的使用方式。
- **要求回應邀請**會顯示在選定的時間週期並多少人無法收到服務的要求回應呼叫總數。
- **平均要求回應計算時間**會顯示所需執行收到的要求的時間的平均值。
- **批次要求**會顯示在選定的時間週期並多少人無法收到服務的批次要求總數。
- **平均工作延遲**會顯示所需執行收到的要求的時間的平均值。
- **錯誤**會顯示彙總的已發生錯誤的 web 服務的電話。
- **服務成本**會顯示服務相關聯的帳單計劃的費用。

從 [設定] 頁面中，您可以更新下列屬性︰

* **描述**可讓您輸入的 Web 服務的描述。 描述會成為必填的欄位。
* **記錄**可讓您以啟用或停用登入端點的錯誤。 如需記錄的詳細資訊，請參閱啟用[電腦學習 web 服務的記錄](machine-learning-web-services-logging.md)。
* **啟用的範例資料**，可讓您提供可用來測試要求回應服務的範例資料。 如果您在電腦學習 Studio 建立 web 服務的範例資料是來自資料您用來訓練模型。 如果您以程式設計方式建立的服務，資料會從您提供 JSON 套件的一部分的範例資料。

[consume]: machine-learning-consume-web-services.md
[marketplace]: machine-learning-publish-web-service-to-azure-marketplace.md
