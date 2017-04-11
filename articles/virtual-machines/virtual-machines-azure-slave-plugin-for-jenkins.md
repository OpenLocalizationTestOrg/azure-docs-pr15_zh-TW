<properties
    pageTitle="如何使用外掛程式 Azure 從屬 Jenkins 連續整合 |Microsoft Azure"
    description="說明如何使用外掛程式 Azure 從屬 Jenkins 連續整合。"
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

# <a name="how-to-use-the-azure-slave-plug-in-with-jenkins-continuous-integration"></a>如何使用外掛程式 Azure 從屬 Jenkins 連續整合

您可以使用 Azure 備用外掛程式的 Jenkins 佈建從屬節點上 Azure 時執行分散式組建。

## <a name="install-the-azure-slave-plug-in"></a>安裝外掛程式 Azure 從屬

1. 在 [Jenkins 儀表板中，按一下 [**管理 Jenkins**]。

1. 在 [**管理 Jenkins**頁面上，按一下 [**管理增益集**]。

1. 按一下 [**線上**] 索引標籤。

1. 在 [可用的外掛程式的清單上方 [篩選] 欄位中，輸入**Azure**相關的外掛程式清單限制。

    如果您選擇捲動可用的外掛程式的清單，您會在 [**叢集管理並分佈建立**區段底下，尋找 Azure 從屬外掛程式。

1. 選取**Azure 從屬外掛程式**] 核取方塊。

1. 按一下 [**安裝，而不重新啟動**，或**[立即下載並安裝後重新啟動**]。

現在已安裝外掛程式下, 一個步驟是設定您的訂閱 Azure 設定檔的外掛程式]，並建立從屬節點的虛擬機器中建立範本，會用。


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

            Id="<Subscription ID value>"

            Name="Pay-As-You-Go"
            ManagementCertificate="<Management certificate value>" />

        </PublishProfile>

    </PublishData>

您訂閱的設定檔之後，請遵循下列步驟以設定 Azure 備用外掛程式︰

1. 在 [Jenkins 儀表板中，按一下 [**管理 Jenkins**]。

1. 按一下 [**系統設定**]。

1. 若要尋找 [**雲端**] 區段中的頁面上向下捲動。

1. 按一下 [**新增新的雲端 > Microsoft Azure**。

    ![雲端區段][cloud section]

    這會顯示的欄位必須輸入您的訂閱明細。

    ![訂閱設定][subscription configuration]

1. 訂閱識別碼和管理憑證值，複製您訂閱的設定檔，並貼至適當的欄位。

    當複製訂閱識別碼和管理憑證，不會包含引號括住值的。

1. 按一下 [**驗證設定**。

1. 設定時驗證似乎正確，請按一下 [**儲存**]。

## <a name="set-up-a-virtual-machine-template-for-the-azure-slave-plug-in"></a>設定虛擬機器範本 Azure 從屬外掛程式

虛擬機器範本定義外掛程式用來建立從屬節點上 Azure 參數。 在下列步驟中，我們會建立 Ubuntu 虛擬機器的範本。

1. 在 [Jenkins 儀表板中，按一下 [**管理 Jenkins**]。

1. 按一下 [**系統設定**]。

1. 若要尋找 [**雲端**] 區段中的頁面上向下捲動。

1. 在**雲端**] 區段中，找到**新增 Azure 虛擬機器範本**，然後再按一下 [**新增**。

    ![新增 vm 範本][add vm template]

    這會顯示的欄位，輸入您要建立範本的詳細資料的位置。

    ![空白的一般設定][blank general configuration]

1. 在 [**名稱**] 方塊中輸入 [Azure 雲端服務名稱]。 如果您輸入的名稱參照至現有的雲端服務，請將該服務中提供虛擬機器。 否則，Azure 會建立一個新。

1. 在 [**描述**] 方塊中，輸入描述您所建立的範本的文字。 這只適用於您的記錄，而不使用佈建虛擬機器中。

1. [**標籤**] 方塊用來識別您所建立的範本，然後就用來建立 Jenkins 工作時，請參考範本。 我們的目的，在此方塊中輸入**linux** 。

1. 在 [**地區**] 清單中，按一下 [將會建立虛擬機器中的區域]。

1. 在**虛擬機器大小**] 清單中，按一下適當的大小。

1. 在**儲存體帳戶名稱**] 方塊中，指定將會建立虛擬機器儲存帳戶。 請確定為雲端服務，您將使用相同的區域。 如果您想要建立新的儲存空間，您可以將此方塊保留空白。

1. 保留時間指定 Jenkins 刪除閒置從屬的分鐘數。 將此設定保留 60 的預設值。 您也可以選擇關閉而閒置時從屬。 若要這樣做，請選取**關閉只 （執行刪除） 之後保留時間**] 核取方塊。

1. 將會用到此從屬節點，請在 [**使用方式**] 清單中，按一下適當的條件。 現在，請按一下**Utilize 盡可能這個節點**。

    此時，表單看起來應該有點像這樣︰

    ![重點複習一般範本設定][checkpoint general template config]

    下一步是提供您想要在中建立從屬作業系統圖像相關詳細資料。

1. 在 [**圖像家人或識別碼**] 方塊中，您必須指定哪些系統映像的虛擬電腦上安裝。 您可以從圖像系列的清單中選取或指定自訂的圖像。

    如果您想要選取圖像系列的清單中，輸入圖像系列名稱的第一個字元 （區分大小寫）。 例如，輸入**U**會顯示 Ubuntu 伺服器系列的清單。 從清單中選取之後，Jenkins 會使用該系列系統圖像的最新版本，提供您的虛擬機器時。

    ![OS 影像清單範例][OS Image list sample]

    如果您有您想要改為使用的自訂圖像，輸入自訂圖像的名稱。 自訂圖像名稱不會顯示在清單中，讓您以確保正確輸入的名稱。

    在此教學課程中，輸入**U**以顯示 Ubuntu 圖像的清單，然後按一下**Ubuntu 伺服器 14.04 LTS**。

1. 在 [**啟動方法**] 清單中，按一下 [ **SSH**。

1. 複製下方的指令碼，並將其貼於**初始化指令碼**] 方塊。

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

    建立虛擬機器之後，就會執行初始化指令碼。 在此範例中，指令碼安裝 Java、 給，以及 ant。

1. 在**使用者名稱**和**密碼**] 方塊中，輸入您慣用的值會在您的虛擬機器建立的系統管理員帳戶。

1. 按一下 [**驗證範本**來檢查指定的參數，是否有效。

1. 按一下 [**儲存**]。


## <a name="create-a-jenkins-job-that-runs-on-a-slave-node-on-azure"></a>建立執行 Azure 從屬節點的 Jenkins 作業

在此區段中，您將會建立 Jenkins 任務將在 Azure 從屬節點上執行。 您需要有 GitHub 追蹤您自己的專案。

1. 在 Jenkins 儀表板中，按一下 [**新項目**。

1. 輸入您要建立工作的名稱。

1. 專案類型] 中，按一下 [ **Freestyle 專案**]。

1. 按一下 [**確定**]。

1. 在 [任務設定] 頁面中，選取 [**限制執行此專案的位置**]。

1. 在 [**標籤運算式**] 方塊中，輸入**linux**。 前一節中，我們所建立的命名為 [ **linux**，這是我們正在以下指定從屬範本。

1. 在 [**建置**] 區段中，按一下 [**新增建置步驟**，選取 [**執行命令介面**。

1. 編輯下列指令碼**（您 GitHub 帳戶的名稱）**、 **（您的專案名稱）**及**（您的專案目錄）**取代為正確的值，並貼上編輯的指令碼在顯示的文字區域中。

        # Clone from git repo

        currentDir="$PWD"

        if [ -e (your project directory) ]; then

            cd (your project directory)

            git pull origin master

        else

            git clone https://github.com/(your GitHub account name)/(your project name).git

        fi

        # change directory to project

        cd $currentDir/(your project directory)

        #Execute build task

        ant

1. 按一下 [**儲存**]。

1. 在 [Jenkins 儀表板，將游標移到您剛剛建立的工作，按一下下拉式箭號以顯示任務選項。

1. 按一下 [**立即建立**]。

然後 Jenkins 會使用前一節中所建立的範本來建立從屬節點，並執行這項工作的建立步驟中所指定的指令碼。

## <a name="next-steps"></a>後續步驟

如需有關使用 Java Azure 的詳細資訊，請參閱[Azure Java 開發人員中心]。

<!-- URL List -->

[Azure Java 開發人員中心]: https://azure.microsoft.com/develop/java/
[訂閱的設定檔]: http://go.microsoft.com/fwlink/?LinkID=396395

<!-- IMG List -->

[cloud section]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-cloud-section.png
[subscription configuration]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-account-configuration-fields.png
[add vm template]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-add-vm-template.png
[blank general configuration]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-slave-template-general-configuration-blank.png
[checkpoint general template config]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-slave-template-general-configuration.png
[OS Image list sample]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-os-family-list-sample.png