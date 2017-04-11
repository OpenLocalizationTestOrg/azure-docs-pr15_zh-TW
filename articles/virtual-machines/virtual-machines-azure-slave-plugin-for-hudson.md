<properties
    pageTitle="如何使用外掛程式 Azure 從屬 Hudson 連續整合 |Microsoft Azure"
    description="說明如何使用外掛程式 Azure 從屬 Hudson 連續整合。"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="rmcmurray"
    manager="wpickett"
    editor="" />

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-multiple"
    ms.devlang="java"
    ms.topic="article"
    ms.date="09/20/2016"
    ms.author="robmcm"/>

# <a name="how-to-use-the-azure-slave-plug-in-with-hudson-continuous-integration"></a>如何使用外掛程式 Azure 從屬 Hudson 連續整合

外掛程式]，Hudson Azure 從屬可讓您執行分散式建置時佈建上 Azure 從屬節點。

## <a name="install-the-azure-slave-plug-in"></a>安裝外掛程式 Azure 從屬

1. 在 [Hudson 儀表板中，按一下 [**管理 Hudson**]。

1. 在 [**管理 Hudson**頁面上，按一下 [**管理增益集**。

1. 按一下 [**線上**] 索引標籤。

1. 按一下 [**搜尋]** ，然後輸入**Azure**相關的外掛程式清單限制。

    如果您選擇捲動可用的外掛程式的清單，您會發現 Azure 從屬外掛程式 [**叢集管理和分散式建立**區段下的 [**其他**] 索引標籤。

1. **Azure 從屬外掛程式**，請選取核取方塊。

1. 按一下 [**安裝**]。

1. 重新啟動 Hudson。

現在，已安裝外掛程式，設定您的訂閱 Azure 設定檔的外掛程式]，並建立的範本，將會用於建立從屬節點 VM 是接下來的步驟。

## <a name="configure-the-azure-slave-plug-in-with-your-subscription-profile"></a>設定您的訂閱的設定檔 Azure 備用外掛程式

訂閱的設定檔，也稱為發佈設定，是 XML 檔案包含安全認證必須先在 [您的開發環境中使用 Azure 的其他資訊。 若要設定 Azure 備用外掛程式，您需要︰

* 您的訂閱識別碼
* 管理憑證，您的訂閱

這些可以找到您的[訂閱的設定檔]中。 以下是訂閱設定檔的範例。

    <?xml version="1.0" encoding="utf-8"?>

        <PublishData>

        <PublishProfile SchemaVersion="2.0" PublishMethod="AzureServiceManagementAPI">

        <Subscription

            ServiceManagementUrl="https://management.core.windows.net"

            Id="<Subscription ID>"

            Name="Pay-As-You-Go"
            ManagementCertificate="<Management certificate value>" />

        </PublishProfile>

    </PublishData>

一旦您訂閱的設定檔之後，請遵循下列步驟以設定 Azure 備用外掛程式。

1. 在 [Hudson 儀表板中，按一下 [**管理 Hudson**]。

1. 按一下 [**系統設定**]。

1. 若要尋找 [**雲端**] 區段中的頁面上向下捲動。

1. 按一下 [**新增新的雲端 > Microsoft Azure**。

    ![新增新的雲端][add new cloud]

    這會顯示的欄位必須輸入您的訂閱明細。

    ![設定設定檔][configure profile]

1. 訂閱識別碼和管理憑證複製您訂閱的設定檔，並貼至適當的欄位。

    當複製訂閱識別碼和管理憑證，**請不要**包括引號括住值的。

1. 按一下 [上**驗證設定**。

1. 設定驗證成功之後，按一下 [**儲存**]。

## <a name="set-up-a-virtual-machine-template-for-the-azure-slave-plug-in"></a>設定虛擬機器範本 Azure 從屬外掛程式

虛擬機器範本定義外掛程式用來建立從屬節點上 Azure 參數。 在下列步驟我們會建立範本的 Ubuntu VM。

1. 在 [Hudson 儀表板中，按一下 [**管理 Hudson**]。

1. 按一下 [**系統設定**]。

1. 若要尋找 [**雲端**] 區段中的頁面上向下捲動。

1. 在 [**雲端**] 區段中，尋找**新增 Azure 虛擬機器範本**，然後按一下 [**新增**] 按鈕。

    ![新增 vm 範本][add vm template]

1. 在 [**名稱**] 欄位中指定的雲端服務的名稱。 如果您指定的名稱參照至現有的雲端服務，請將該服務中提供 VM。 否則，Azure 會建立一個新。

1. 在 [**描述**] 欄位中，輸入描述您所建立的範本的文字。 這項資訊僅適用於記錄的用途，而不是在佈建 VM。

1. 在 [**標籤**] 欄位中，輸入**linux**。 此標籤用來識別您所建立的範本，然後就用來建立 Hudson 工作時，請參考範本。

1. 選取 [將會建立 VM 的區域]。

1. 選取適當的虛擬記憶體大小。

1. 指定儲存帳戶建立 VM 的位置。 請確定為雲端服務，您將使用相同的區域。 如果您想要建立新的儲存空間，您可以將此欄位保留空白。

1. 保留時間指定 Hudson 刪除閒置從屬的分鐘數。 將此設定保留 60 的預設值。

1. 在 [**使用情況**中，選取適當的條件，會用到此從屬節點。 現在，選取 [ **Utilize 盡可能這個節點**]。

    此時，看起來類似此表單︰

    ![範本設定][template config]

1. **圖像家用**或識別碼必須指定哪些系統圖像將會安裝在您 VM。 您可以從圖像系列的清單中選取或指定自訂的圖像。

    如果您想要選取圖像系列的清單中，輸入圖像系列名稱的第一個字元 （區分大小寫）。 例如，輸入**U**會顯示 Ubuntu 伺服器系列的清單。 當您從清單中選取時，Jenkins 會使用該系列系統圖像的最新版本，提供您 VM 時。

    ![OS 家庭清單][OS family list]

    如果您有您想要改為使用的自訂圖像，輸入自訂圖像的名稱。 自訂圖像名稱不會顯示在清單中，如此便確保正確輸入的名稱。    

    在此教學課程中，輸入**U** ，帶出 Ubuntu 圖像的清單，然後選取 [ **Ubuntu 伺服器 14.04 LTS**。

1. 選取 [ **SSH****啟動方法**]。

1. 複製下方的指令碼並貼上 [**初始化指令碼**] 欄位中。

        # Install Java

        sudo apt-get -y update

        sudo apt-get install -y openjdk-7-jdk

        sudo apt-get -y update --fix-missing

        sudo apt-get install -y openjdk-7-jdk

        # Install git

        sudo apt-get install -y git

        #Install ant

        sudo apt-get install -y ant

        sudo apt-get -y update --fix-missing

        sudo apt-get install -y ant

    建立 VM 之後，就會執行**初始化指令碼**。 在此範例中，指令碼安裝 Java、 給，以及 ant。

1. 在 [**使用者名稱**和**密碼**] 欄位中，輸入您慣用的值會建立在您 VM 的系統管理員帳戶。

1. 按一下 [上**驗證的範本**，請檢查您指定的參數是否有效。

1. 按一下 [**儲存**]。

## <a name="create-a-hudson-job-that-runs-on-a-slave-node-on-azure"></a>建立執行 Azure 從屬節點的 Hudson 作業

在此區段中，您將會建立 Hudson 任務將在 Azure 從屬節點上執行。

1. 在 Hudson 儀表板中，按一下 [**新工作**]。

1. 輸入您要建立工作的名稱。

1. 工作類型] 中，選取 [**建立的免費樣式軟體工作**]。

1. 按一下**[確定]**。

1. 在 [工作設定] 頁面中，選取 [**限制執行此專案的位置**]。

1. 選取 [**節點] 和 [標籤] 功能表**，然後選取**linux** （在 [上一節中建立的虛擬機器範本時，我們指定此標籤）。

1. 在 [**建立**] 區段中，按一下 [**新增建置步驟**，選取 [**執行命令介面**。

1. 編輯下列指令碼，以適當的值取代**{github 帳戶名稱}**、 **{專案名稱}**及**{專案目錄}** ，並在顯示的文字區域中貼上編輯的指令碼。

        # Clone from git repo

        currentDir="$PWD"

        if [ -e {your project directory} ]; then

            cd {your project directory}

            git pull origin master

        else

            git clone https://github.com/{your github account name}/{your project name}.git

        fi

        # change directory to project

        cd $currentDir/{your project directory}

        #Execute build task

        ant

1. 按一下 [**儲存**]。

1. Hudson 儀表板中尋找您剛剛建立並按一下 [**排程建立**圖示的工作。

接著 Hudson 會建立從屬節點使用前一節中所建立的範本，然後執行此工作建立步驟中所指定的指令碼。

## <a name="next-steps"></a>後續步驟

如需有關使用 Java Azure 的詳細資訊，請參閱[Azure Java 開發人員中心]。

<!-- URL List -->

[Azure Java 開發人員中心]: https://azure.microsoft.com/develop/java/
[訂閱的設定檔]: http://go.microsoft.com/fwlink/?LinkID=396395

<!-- IMG List -->

[add new cloud]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-addcloud.png
[configure profile]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-configureprofile.png
[add vm template]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-addnewvmtemplate.png
[template config]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-templateconfig1-withdata.png
[OS family list]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-oslist.png

