<properties
    pageTitle="建立 Jupyter/IPython 筆記本 |Microsoft Azure"
    description="瞭解如何部署 Linux 虛擬機器 Azure 中的資源管理員部署模型建立 Jupyter/IPython 筆記本。"
    services="virtual-machines-linux"
    documentationCenter="python"
    authors="crwilcox"
    manager="wpickett"
    editor=""
    tags="azure-service-management,azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="python"
    ms.topic="article"
    ms.date="11/10/2015"
    ms.author="crwilcox"/>

# <a name="jupyter-notebook-on-azure"></a>在 Azure Jupyter 筆記本

[Jupyter 專案](http://jupyter.org)、 前身為[IPython 專案](http://ipython.org)，提供的工具集合科學計算使用強大的互動式 shell 的合併以建立即時計算文件執行程式碼。 這些筆記本檔案可以包含任何文字、 數學公式、 輸入的代碼，結果、 圖形、 影片和任何其他種類的現代化的網頁瀏覽器所能顯示的媒體。 無論您是絕對的新 Python，而且想要瞭解其有趣的互動式環境中，或執行某些嚴重技術平行/運算，Jupyter 筆記本是很好的選擇。

![螢幕擷取畫面](./media/virtual-machines-linux-jupyter-notebook/ipy-notebook-spectral.png)使用 SciPy 和 Matplotlib 套件，以分析音效錄製的結構。


## <a name="jupyter-two-ways-azure-notebooks-or-custom-deployment"></a>Jupyter 兩種方法︰ Azure 的筆記本或自訂的部署
Azure 提供可用來[快速開始使用 Jupyter](http://blogs.technet.com/b/machinelearning/archive/2015/07/24/introducing-jupyter-notebooks-in-azure-ml-studio.aspx)的服務。  藉由使用 Azure 筆記本服務，您可以輕鬆地存取與 Python 的所有功能可調整計算資源的 Jupyter 的無障礙 web 介面和其許多文件庫。  由於安裝處理服務，使用者可以存取這些資源，而不需要管理和使用者的設定。

如果筆記本服務不適用於您的狀況請繼續閱讀本文，它將會告訴您如何部署 Jupyter 上筆記本 Microsoft Azure 使用 Linux 虛擬機器 (Vm)。

[AZURE.INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## <a name="create-and-configure-a-vm-on-azure"></a>建立及設定 VM Azure 上

第一個步驟是，建立執行 Azure 虛擬機器 (VM)。
此 VM 是完整的作業系統，在雲端，並將用來執行 Jupyter 筆記本。 Azure 所能執行 Linux 和 Windows 虛擬機器，我們會包含這兩種虛擬機器 Jupyter 的設定。

### <a name="create-a-linux-vm-and-open-a-port-for-jupyter"></a>建立 Linux VM 並開啟 Jupyter 連接埠

依照指示指定[以下][portal-vm-linux]建立*Ubuntu*分配的虛擬機器。 本教學課程中使用 Ubuntu 伺服器 14.04 LTS。 我們會假設使用者名稱*azureuser*。

虛擬機器部署後，我們需要開啟網路的 [安全性] 群組中的安全性規則。  從 Azure] 入口網站中，移至 [**網路安全性群組**並開啟 [] 索引標籤的對應到您 VM 安全性群組。 您需要新增連入安全性規則以下列設定︰ **TCP**通訊協定， **\*** （公開） 的來源連接埠和**9999** （私人） 目的地連接埠。

![螢幕擷取畫面](./media/virtual-machines-linux-jupyter-notebook/azure-add-endpoint.png)

在 [您的網路安全性] 群組中，按一下**網路介面**並記下**公用 IP 位址**視需要將其連線至您 VM 中下一步。

## <a name="install-required-software-on-the-vm"></a>VM 上安裝所需的軟體

若要執行 Jupyter 筆記本上我們 VM，我們必須先安裝 Jupyter 和其相依性。 連線到您 linux vm 使用 ssh 和使用者名稱與密碼配對您選擇當您建立 vm。 在本教學課程中，我們將使用 PuTTY 並從 Windows 連線。

### <a name="installing-jupyter-on-ubuntu"></a>在 Ubuntu 安裝 Jupyter
安裝 Anaconda，常用資料科學 python 分配，使用其中一個從[連續分析](https://www.continuum.io/downloads)所提供的連結。  此文件，撰寫，以下連結是最日期版本。

#### <a name="anaconda-installs-for-linux"></a>針對 Linux anaconda 安裝
<table>
  <th>Python 3.4</th>
  <th>Python 2.7</th>
  <tr>
    <td>
        <a href='https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda3-2.3.0-Linux-x86_64.sh'>64 位元</href>
    </td>
    <td>
        <a href='https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda-2.3.0-Linux-x86_64.sh'>64 位元</href>
    </td>
  </tr>
  <tr>
    <td>
        <a href='https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda3-2.3.0-Linux-x86.sh'>32 位元版本</href>
    </td>
    <td>
        <a href='https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda-2.3.0-Linux-x86.sh'>32 位元版本</href>
    </td>  
  </tr>
</table>


#### <a name="installing-anaconda3-230-64-bit-on-ubuntu"></a>安裝 Anaconda3 2.3.0 Ubuntu 上的 64 位元
例如，這是如何安裝 Anaconda Ubuntu 上

    # install anaconda
    cd ~
    mkdir -p anaconda
    cd anaconda/
    curl -O https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda3-2.3.0-Linux-x86_64.sh
    sudo bash Anaconda3-2.3.0-Linux-x86_64.sh -b -f -p /anaconda3

    # clean up home directory
    cd ..
    rm -rf anaconda/

    # Update Jupyter to the latest install and generate its config file
    sudo /anaconda3/bin/conda install jupyter -y
    /anaconda3/bin/jupyter-notebook --generate-config


![螢幕擷取畫面](./media/virtual-machines-linux-jupyter-notebook/anaconda-install.png)


### <a name="configuring-jupyter-and-using-ssl"></a>設定 Jupyter 和使用 SSL
安裝後，我們需要花點時間的 Jupyter 設定的設定檔。 如果您遇到的麻煩設定 Jupyter 可能會很有幫助查看[Jupyter 文件執行筆記本伺服器](http://jupyter-notebook.readthedocs.org/en/latest/public_server.html)。

下一步我們`cd`建立我們的 SSL 憑證和編輯設定檔設定檔的設定檔目錄。

在 Linux 使用下列命令。

    cd ~/.jupyter

您可以使用下列命令來建立 SSL 憑證 （Linux 及 Windows）。

    openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem

請注意，因為我們建立自我簽署的 SSL 憑證，連線到筆記本瀏覽器可讓您的安全性警告時。  用於長期生產，您會想要使用您的組織相關聯的正確簽署的憑證。  由於憑證管理此示範的範圍之內，我們將著重自我簽署憑證以現在。

除了使用的憑證，您也必須提供一個密碼來防止未經授權使用保護您的筆記本。  基於安全性理由 Jupyter 使用加密的密碼在其設定的檔案，所以您必須先加密您的密碼。  IPython 提供一個公用程式來執行此作業。在命令提示字元中，執行下列命令。

    /anaconda3/bin/python -c "import IPython;print(IPython.lib.passwd())"

這會提示您輸入的密碼和確認，然後列印密碼。 請注意此進行下列步驟。

    Enter password:
    Verify password:
    sha1:b86e933199ad:a02e9592e59723da722.. (elided the rest for security)

接下來，我們將在其中編輯設定檔的設定檔中，這是`jupyter_notebook_config.py`在的目錄中的檔案。  請注意，此檔案可能不存在，如果是這樣只要建立。  此檔案有] 欄位有數字，然後依預設全部都註解]。  您可以使用您自己的需要的任何文字編輯器中開啟此檔案，您應該確認它至少下列內容。 **請務必取代中使用上述步驟中 sha1 config c.NotebookApp.password**。

    c = get_config()

    # You must give the path to the certificate file.
    c.NotebookApp.certfile = u'/home/azureuser/.jupyter/mycert.pem'

    # Create your own password as indicated above
    c.NotebookApp.password = u'sha1:b86e933199ad:a02e9592e5 etc... '

    # Network and browser details. We use a fixed port (9999) so it matches
    # our Azure setup, where we've allowed traffic on that port
    c.NotebookApp.ip = '*'
    c.NotebookApp.port = 9999
    c.NotebookApp.open_browser = False

### <a name="run-the-jupyter-notebook"></a>執行 Jupyter 筆記本

現在，我們已準備好開始 Jupyter 筆記本。 若要這麼做，請瀏覽至您想要儲存筆記本中的，下列命令以啟動 Jupyter 筆記本伺服器的目錄。

    /anaconda3/bin/jupyter-notebook

您現在應該可以存取您的地址的 Jupyter 筆記本`https://[PUBLIC-IP-ADDRESS]:9999`。

當您第一次存取您的筆記本時，請登入] 頁面會詢問您的密碼。 然後，當您登入，您會看到 「 Jupyter 筆記本儀表板 」，這是下時中心的所有筆記本作業。  此頁面中，您可以建立新的筆記本，開啟現有的。

![螢幕擷取畫面](./media/virtual-machines-linux-jupyter-notebook/jupyter-tree-view.png)

### <a name="using-the-jupyter-notebook"></a>使用 Jupyter 筆記本

如果您按一下 [**新增**] 按鈕，您會看到下列開啟頁面。

![螢幕擷取畫面](./media/virtual-machines-linux-jupyter-notebook/jupyter-untitled-notebook.png)

標示區域`In []:`提示時輸入的區域中，您可以在這裡輸入任何有效的 Python 程式碼並將執行時按下 [`Shift-Enter`或按一下 [播放] 圖示 （向右三角形工具列中）。

## <a name="a-powerful-paradigm-live-computational-documents-with-rich-media"></a>強大的範例︰ live 計算的文件，與多媒體

筆記本本身應該感到很自然 Python 與 word 處理器，已使用的任何人，因為它是在一些方法混合兩者︰ 您可以執行區塊 Python 程式碼，但您也可以讓備忘稿和其他文字的 「 Markdown 」 的 「 程式碼 」 的儲存格樣式] 工具列中使用下拉式功能表。

Jupyter 是不僅僅 word 處理器為其允許混用計算和豐富媒體 （文字、 圖形、 視訊和幾乎任何現代化的網頁瀏覽器可以顯示）。 您可以混合，文字、 程式碼、 影片和更多 ！

![螢幕擷取畫面](./media/virtual-machines-linux-jupyter-notebook/jupyter-editing-experience.png)

並使用 Python 的多個絕佳文件庫的科學與技術的計算，在以下的螢幕擷取畫面，為複雜的網路分析，一個環境中的所有相同輕鬆可以執行簡單的計算。

此範例使用 [即時運算混合的現代化的 web 提供多種可能，與適用於雲端;您可以使用筆記本︰

* 為計算的手寫板記錄探處理問題。

* 共用與同事，請在 「 live' 計算的表單或複本格式 （HTML、 PDF） 的 [結果]。

* 發佈並進行簡報即時教學所涉及的計算，因此實際的程式碼，可以立即試用學生材料加以修改，然後重新執行互動的方式。

* 若要提供 「 可執行論文 」 的呈現方式，可以立即重製、 驗證及擴充其他人的參考資料的結果。

* 共同作業運算的平台︰ 多個使用者到相同的筆記本伺服器，才能共用即時計算工作階段，可以登入。


如果您移至 IPython 版本代碼[存放庫][]，您會發現使用筆記本範例您可以下載並在您自己的 Azure Jupyter VM 嘗試使用整個目錄。  只要下載`.ipynb`來自網站的檔案並將其拖曳到您的筆記本 Azure VM 的儀表板上傳 （或下載檔案直接將 VM）。

## <a name="conclusion"></a>結論

Jupyter 筆記本提供功能強大的介面互動的方式存取 Python 生態系統上 Azure power。  矩形的各種包括簡單探索及學習 Python、 資料分析與視覺效果、 模擬和平行運算的情況下使用方式。 產生的筆記本文件包含計算的執行，可與其他 Jupyter 使用者共用的完整的記錄。  Jupyter 筆記本可做為本機的應用程式，但它適用於在 Azure 的雲端部署

Jupyter 的核心功能也可透過[Visual Studio Python 工具][](PTVS) 的 Visual Studio 內。 PTVS 是免費，並開啟來源外掛程式 microsoft 提供的分析及平行 Visual Studio 變成包含 intellisense，偵錯的進階的編輯器的進階 Python 開發環境計算整合。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱[Python 開發人員中心](/develop/python/)。

[portal-vm-linux]: https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-tutorial-portal-rm/
[存放庫]: https://github.com/ipython/ipython
[Visual Studio Python 工具]: http://aka.ms/ptvs
