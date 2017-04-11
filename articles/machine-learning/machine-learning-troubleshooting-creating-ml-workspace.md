<properties
    pageTitle="疑難排解︰ 建立及電腦學習工作區連線 |Microsoft Azure"
    description="建立和連線至 Azure 電腦學習的工作區中的一般問題的解決方案"
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
    ms.date="09/09/2016"
    ms.author="garye"/>


# <a name="troubleshooting-guide-create-and-connect-to-an-machine-learning-workspace"></a>疑難排解指南︰ 建立並連接至電腦學習的工作區

本指南可提供解決方案的一些常見問題時所發生的挑戰設定 Azure 電腦學習工作區。

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="workspace-owner"></a>工作區擁有者

當您建立新的電腦學習工作區時，您在工作區擁有者] 欄位中輸入的識別碼必須是有效的 Microsoft 帳戶 （前稱為 Windows Live ID)，例如john-contoso@live.com或john-contoso@hotmail.com。 不能非 Microsoft 帳戶，例如您的公司電子郵件帳戶。 若要建立免費的 Microsoft 帳戶，請移至[www.live.com](http://www.live.com)。

請注意，您用來登入 Azure 入口網站來建立工作區的帳戶不會不會自動*開啟*的權限的工作區中，除非您擁有者指定該帳戶。 若要在電腦學習 Studio 中開啟工作區，您必須登入 Microsoft 帳戶所定義的工作區中，擁有者，或您要接收邀請加入工作區擁有者。 從 Azure 傳統入口網站，不過，您可以*管理*工作區中，包括變更擁有者，並設定 access 的能力。

如需有關管理工作區的詳細資訊，請參閱[管理 Azure 電腦學習工作區]。

[管理 Azure 電腦學習工作區]: machine-learning-manage-workspace.md

## <a name="allowed-regions"></a>允許的區域

電腦學習位於目前提供有限的區域。 如果您的訂閱不包含這些區域的其中一個，您可能會看到錯誤訊息，「 您有沒有訂閱中允許的區域。 」

若區域會新增至您的訂閱，請選取 [從 Azure 傳統入口網站的 [**連絡 Microsoft 支援人員**、 選擇問題類型為 [**帳單**，然後遵循提示提交您的要求。

![連絡 Microsoft 支援服務][screen1]

## <a name="storage-account"></a>儲存帳戶

若要儲存資料的儲存空間帳戶需要電腦學習服務。 您可以使用現有的儲存空間帳戶，或當您建立新的電腦學習工作區 （如果您有配額來建立新的儲存空間帳戶），您可以建立新的儲存空間帳戶。

<!-- These instructions no longer work, but I'm not sure what to replace them with
To see if you can create a new storage account, in the Classic Portal, go to **Settings** and then click **Usage**.
-->

![建立工作區][screen2]

新的電腦學習工作區建立之後，您可以登入電腦學習 Studio 使用您指定為工作區的擁有者的 Microsoft 帳戶。 如果您遇到錯誤訊息，「 工作區找不到 」 （類似以下的螢幕擷取畫面），請使用下列步驟，若要刪除您在瀏覽器 cookie。

![找不到的工作區][screen3]

**若要刪除瀏覽器 cookie**

如果您是使用 Internet Explorer，請按一下右上角的 [**工具**] 按鈕，然後選取 [**網際網路選項]**。  

![網際網路選項][screen4]

在 [**一般**] 索引標籤中，按一下 [**刪除...**

![一般] 索引標籤][screen5]

在 [**刪除瀏覽歷程記錄**] 對話方塊中，確認已選取**Cookie 與網站資料]** ，然後按一下 [**刪除**]。

![刪除 cookie][screen6]

刪除 cookie 之後，請重新啟動瀏覽器，然後移至[Microsoft Azure 電腦學習](https://studio.azureml.net)頁面。 當系統提示您輸入使用者名稱和密碼時，請輸入您為工作區的擁有者指定的相同 Microsoft 帳戶。

我們的目標是讓電腦學習經驗越好為順暢。 請在[Azure 電腦學習論壇](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning)，可協助我們提供您更好張貼任何註解和問題。

[screen1]:media/machine-learning-troubleshooting-creating-ml-workspace/screen1.png
[screen2]:media/machine-learning-troubleshooting-creating-ml-workspace/screen2.png
[screen3]:media/machine-learning-troubleshooting-creating-ml-workspace/screen3.png
[screen4]:media/machine-learning-troubleshooting-creating-ml-workspace/screen4.png
[screen5]:media/machine-learning-troubleshooting-creating-ml-workspace/screen5.png
[screen6]:media/machine-learning-troubleshooting-creating-ml-workspace/screen6.png
