<properties
    pageTitle="設定為 IPython 筆記本伺服器虛擬機器 |Microsoft Azure"
    description="設定進階分析設定 Azure 虛擬機器資料科學環境中使用與 IPython 伺服器。"
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun"  />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/19/2016"
    ms.author="xibingao;bradsev" />

# <a name="set-up-an-azure-virtual-machine-as-an-ipython-notebook-server-for-advanced-analytics"></a>Azure 虛擬機器設為進階分析 IPython 筆記本伺服器

本主題說明如何佈建和設定可以做為資料科學環境的組件的進階分析 Azure 虛擬機器。 設定 Windows 虛擬機器是支援例如工具，例如 IPython 筆記本、 Azure 儲存檔案總管、 AzCopy，以及其他公用程式可用於進階的分析專案。 Azure 儲存檔案總管] 及 [AzCopy，例如，提供方便的方式，將資料上傳至 Azure blob 儲存體從本機電腦，或將其從 blob 儲存體下載至您的本機電腦。

## <a name="create-vm"></a>步驟 1︰ 建立一般的用途 Azure 虛擬機器

如果您已經有 Azure 虛擬機器，只是要設定 IPython 筆記本伺服器上，您可以略過此步驟，並前往[步驟 2︰ 將端點新增到現有的虛擬機器 IPython 筆記本](#add-endpoint)。

在開始之前的建立 Azure 虛擬機器程序，您需要判斷電腦的處理專案的資料時所需的大小。 較小的電腦有較少的記憶體和較少的 CPU 核心高於較大，但也可能是較便宜。 電腦類型和價格的清單，請參閱<a href="http://azure.microsoft.com/pricing/details/virtual-machines/" target="_blank">虛擬機器價格</a>] 頁面

1. 登入<a href="https://manage.windowsazure.com" target="_blank">Azure 傳統入口網站</a>，並按一下 [**新增**]，請在左上角。 在視窗就會出現。 選取 [**計算** -> **虛擬機器** -> **從圖庫**。

    ![建立工作區][24]

2. 選擇圖像下列其中一項︰

    * Windows Server 2012 R2 資料中心
    * Windows Server 基礎體驗 (Windows Server 2012 R2)

    然後按一下往右點到下的 [設定] 頁面右下角的箭號。

    ![建立工作區][25]

3. 輸入您要建立，請選取 [大小] 電腦的虛擬機器的名稱 (預設︰ A3) 根據電腦即將程序的資料大小和強大功能您想要的電腦 （記憶體大小和計算核心的數量），可以針對機器輸入使用者名稱和密碼。 然後按一下 [從右至移至下一個 [設定] 頁面指向箭號。

    ![建立工作區][26]

4. 選取含有您打算要用於此虛擬機器中，**儲存帳戶**的**地區/相關性群組/虛擬網路**，然後選取 [儲存帳戶。 輸入端點 (「 IPython 」 以下) 的名稱，按一下 [**結束點**] 欄位中底部新增端點。 您可以選擇任何字串的結束點及任何介於 0 到 65536 的整數，**可以使用****公用連接埠**的**名稱**。 **私人連接埠**必須是**9999**。 使用者應**避免**使用公用已指派的連接埠的網際網路服務。 <a href="http://www.chebucto.ns.ca/~rakerman/port-table.html" target="_blank">連接埠的網際網路服務</a>提供已指派及應避免使用連接埠的清單。

    ![建立工作區][27]

5. 按一下 [核取記號，以開始虛擬機器佈建程序。

    ![建立工作區][28]


可能需要 15 25 分鐘才能完成虛擬機器佈建程序。 在建立虛擬機器之後，這台電腦的狀態應該會顯示為**執行**。

![建立工作區][29]

## <a name="add-endpoint"></a>步驟 2︰ 將端點為 IPython 筆記本新增至現有的虛擬機器

如果您建立虛擬機器依照下列步驟 1 中的指示進行，然後已新增的端點 IPython 筆記本，可以略過此步驟。

如果虛擬機器已經存在，您需要新增端點，您將會安裝在步驟 3 下方，先登入 Azure 傳統入口網站的 IPython 筆記本，請選取虛擬機器，，並新增 IPython 筆記本伺服器的端點。 下圖中將含有入口網站的螢幕擷取畫面之後的端點 IPython 筆記本已新增至 Windows 虛擬機器。

![建立工作區][17]

## <a name="run-commands"></a>步驟 3︰ 安裝 IPython 筆記本和其他支援的工具

建立虛擬機器之後，請登入 Windows 虛擬機器中使用遠端桌面通訊協定 (RDP)。 如需相關指示，請參閱[如何登入虛擬機器執行 Windows Server](../virtual-machines/virtual-machines-windows-classic-connect-logon.md)。 以**管理員**身分開啟**命令提示字元**（**不在 Powershell 命令視窗**），然後執行下列命令。

    set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/MachineSetup/Azure_VM_Setup_Windows.ps1'

    @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"

安裝完成後，會自動在啟動 IPython 筆記本伺服器*c:\\使用者\\\<使用者名稱\>\\文件\\IPython 筆記本*目錄。

出現提示時，請輸入密碼 IPython 筆記本和電腦的系統管理員的密碼。 這可讓 IPython 筆記本，在電腦上執行的服務。

## <a name="access"></a>從網頁瀏覽器的步驟 4︰ 存取 IPython 筆記本
若要存取 IPython 筆記本伺服器，開啟網頁瀏覽器，並輸入*https://&#60;virtual 電腦 DNS 名稱 >: & #60; 公用連接埠號碼 >* URL] 文字方塊中。 在這裡， *& #60; 公用連接埠號碼 >*應該是您所指定的 IPython 筆記本端點新增時的連接埠號碼。

*& #60; 虛擬機器 DNS 名稱 >*位於傳統入口網站的 Azure。 按一下 [記錄] 以傳統入口網站中之後**虛擬機器**中，選取您建立了，電腦，然後選取 [**儀表板**，DNS 名稱就會顯示，如下所示︰

![建立工作區][19]

下圖所示，就會發生警告，指出該_此網站的安全性憑證發生問題_(Internet Explorer) 或_您的連線是不是私人_(Chrome)。 按一下 [**繼續] （不建議使用） 此網站**(Internet Explorer)] 或 [**進階]** ，然後**前進到與 #60;*DNS 名稱*> （不安全） * * (Chrome)，以繼續進行。 然後輸入您先前指定存取 IPython 筆記本的密碼。

**Internet Explorer:**
![建立工作區][20]

**Chrome:**
![建立工作區][21]

您登入 IPython 筆記本之後，目錄*DataScienceSamples*會顯示在瀏覽器上。 此目錄包含 microsoft，協助進行資料科學工作的使用者共用的範例 IPython 筆記本。 這些範例 IPython 筆記本已取出從[**Github 存放庫**](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks)的虛擬機器期間 IPython 筆記本伺服器設定程序。 Microsoft 維護並經常更新此存放庫。 使用者請造訪取得最近更新的範例 IPython 筆記本 Github 存放庫。
![建立工作區][18]

## <a name="upload"></a>步驟 5︰ 上傳至 IPython 筆記本伺服器從本機電腦現有 IPython 筆記本

IPython 筆記本提供一個簡單的方法，將其本機電腦上現有的 IPython 筆記本上傳至虛擬機器上 IPython 筆記本伺服器的使用者。 使用者 IPython 筆記本，在網頁瀏覽器中登入之後，請按一下至 IPython 筆記本會上傳至**目錄**。 然後，選取 [從**檔案總管**] 中的本機電腦上傳和它拖放到 IPython 筆記本目錄上的網頁瀏覽器 IPython 筆記本.ipynb 檔案。 按一下 [**上傳**] 按鈕，.ipynb 檔案上傳至 IPython 筆記本伺服器。 其他使用者可以開始使用中的網頁瀏覽器。

![建立工作區][22]

![建立工作區][23]


##<a name="shutdown"></a>關閉並取消配置在不使用時的虛擬機器

Azure 虛擬機器價格大於為**支付僅適用於您的使用**。 若要確保，您不計費不使用您的虛擬機器時，其位於 [**停止 (Deallocated)** ] 狀態，在不使用時。

> [AZURE.NOTE] 如果您關閉虛擬機器從 VM 內部 （使用 Windows power 選項），VM 停止但仍會保留已配置。 若要確保您無法繼續要付費，隨時停止虛擬機器從[Azure 傳統入口網站](http://manage.windowsazure.com/)。 您也可以停止透過 Powershell VM 呼叫**ShutdownRoleOperation**與 「 PostShutdownAction 「 等於 」 StoppedDeallocated 」。

關閉和解除配置虛擬機器︰

1. [Azure 傳統入口網站](http://manage.windowsazure.com/)使用您的帳戶登入。  

2. 從左側的導覽列中選取**虛擬機器**。

3. 在虛擬機器清單中，按一下您的虛擬機器中的名稱，然後移至**儀表板**頁面]。

4. 在頁面底部，按一下 [**關閉**]。

![VM 關閉][15]

虛擬機器可解除配置，但不是刪除。 您可以隨時從 Azure 傳統入口網站，以重新啟動您的虛擬機器。

## <a name="your-azure-vm-is-ready-to-use-whats-next"></a>您 Azure VM 已可供使用︰ 下一步是什麼？

您的虛擬機器已經準備好在您的資料科學練習中使用。 虛擬機器也是準備好使用 IPython 筆記本伺服器探索及處理資料，以及搭配其他任務 Azure 電腦學習與小組資料科學程序。

小組資料科學程序中的下一個步驟[學習路徑](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/)中對應，且可能包括步驟，將資料移到 HDInsight、 [處理程序和範例並準備資料學習 Azure 電腦學習。


[15]: ./media/machine-learning-data-science-setup-virtual-machine/vmshutdown.png
[17]: ./media/machine-learning-data-science-setup-virtual-machine/add-endpoints-after-creation.png
[18]: ./media/machine-learning-data-science-setup-virtual-machine/sample-ipnbs.png
[19]: ./media/machine-learning-data-science-setup-virtual-machine/dns-name-and-host-name.png
[20]: ./media/machine-learning-data-science-setup-virtual-machine/browser-warning-ie.png
[21]: ./media/machine-learning-data-science-setup-virtual-machine/browser-warning.png
[22]: ./media/machine-learning-data-science-setup-virtual-machine/upload-ipnb-1.png
[23]: ./media/machine-learning-data-science-setup-virtual-machine/upload-ipnb-2.png
[24]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-1.png
[25]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-2.png
[26]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-3.png
[27]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-4.png
[28]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-5.png
[29]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-6.png
