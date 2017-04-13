<properties
   pageTitle="Azure 虛擬機器部署主廚 |Microsoft Azure"
   description="瞭解如何使用主廚做自動化的虛擬機器部署及 Microsoft Azure 上的設定"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="diegoviso"
   manager="timlt"
   tags="azure-service-management,azure-resource-manager"
   editor=""/>

<tags ms.service="virtual-machines-windows" ms.workload="infrastructure-services"
ms.tgt_pltfrm="vm-multiple"
ms.devlang="na"
ms.topic="article"
ms.date="05/19/2015"
ms.author="diviso"/>

# <a name="automating-azure-virtual-machine-deployment-with-chef"></a>自動化 Azure 虛擬機器部署主廚

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

主廚進行自動化的絕佳工具，而想要設定的狀態。

我們最新的雲端 api 版本主廚會提供與 Azure 緊密整合可讓您能夠佈建和部署透過單一命令設定狀態。

在本文中，我會顯示如何設定佈建 Azure 虛擬機器並引導您建立原則或 「 食譜 」，然後部署 Azure 虛擬機器詳加主廚環境。

現在就讓我們開始吧 ！

## <a name="chef-basics"></a>主廚基本概念

在開始之前，建議您檢閱主廚的基本概念。 有絕佳材料<a href="http://www.chef.io/chef" target="_blank">以下</a>而建議您先做好快速讀取您嘗試此逐步解說。 我會在我們開始之前，不過複習基本概念。

下圖說明高層級的主廚架構。

![][2]

主廚有三個主要的建築元件︰ 主廚伺服器、 主廚用戶端 （節點） 及主廚工作站。

主廚伺服器我們管理點，而且有主廚伺服器的兩個選項︰ 主控的解決方案或內部部署解決方案。 我們會使用主控的解決方案。

主廚用戶端 （節點） 是您正在管理伺服器上代理程式。

主廚工作站是我們管理員工作站我們建立我們原則及執行我們管理命令的位置。 我們來管理我們的基礎結構的主廚工作站執行**刀**的命令。

此外，還有 「 食譜 」 與 「 食譜 」 的概念。 這些都是有效的原則，我們定義，並套用至伺服器。

## <a name="preparing-the-workstation"></a>準備工作站

首先，可讓準備工作站。 我使用的標準的 Windows 工作站。 我們需要建立我們的設定檔和食譜儲存目錄。

第一次建立名為 C:\chef 目錄。

然後建立第二個目錄稱為 c:\chef\cookbooks。

現在，我們需要下載我們 Azure 的設定檔，以便主廚能通訊與我們 Azure 訂閱。

下載您發佈[以下](https://manage.windowsazure.com/publishsettings/)設定。

發佈設定將檔案儲存為 C:\chef。

##<a name="creating-a-managed-chef-account"></a>建立受管理的主廚帳戶

主控主廚帳戶登入[以下](https://manage.chef.io/signup)。

進行註冊程序時將會要求您建立新的組織。

![][3]

您的組織建立後，請下載入門套件。

![][4]

> [AZURE.NOTE] 如果您收到警告，會重設索引鍵的提示時，您可以繼續我們已設定為還沒有現有基礎結構。

入門套件 zip 檔案包含您的組織設定檔與索引鍵。

##<a name="configuring-the-chef-workstation"></a>設定主廚工作站

擷取至 C:\chef 主廚 starter.zip 的內容。

複製主廚-starter\chef-repo 下的所有檔案\.主廚您 c:\chef 的目錄。

您的目錄現在看起來應該像下列範例。

![][5]

現在，您應該會有四個 c:\chef 的根目錄中包括 Azure 發佈檔案的檔案。

PEM 檔案會包含您的組織和私密金鑰通訊管理員，而 knife.rb 檔案包含您刀的設定。 我們需要編輯 knife.rb 檔案。

在您所選擇的編輯器中開啟檔案，並修改 「 cookbook_path 」，移除 /。從路徑，使其顯示如下圖所示 [下一步。

    cookbook_path  ["#{current_dir}/cookbooks"]

新增下列行思考您 Azure 名稱發佈的設定檔。

    knife[:azure_publish_settings_file] = "yourfilename.publishsettings"

Knife.rb 檔案現在看起來類似下列範例。

![][6]

刀食譜目錄下 c:\chef\cookbooks，參考，及也 Azure 作業期間會使用我們 Azure 發佈設定檔，就能確定這些線條。

## <a name="installing-the-chef-development-kit"></a>安裝主廚開發套件

下一步][下載並安裝](http://downloads.getchef.com/chef-dk/windows)設定主廚工作站 ChefDK （主廚開發套件）。

![][7]

安裝 c:\opscode 的預設位置。 此安裝帶大約 10 分鐘的時間。

確認您的 PATH 變數 C:\opscode\chefdk\bin; 包含項目C:\opscode\chefdk\embedded\bin;c:\users\yourusername\.chefdk\gem\ruby\2.0.0\bin

如果沒有發生，請確定您新增這些路徑 ！

*請注意路徑的順序是很重要 ！* 如果您 opscode 路徑不是您會有問題的順序正確無誤。

重新啟動您的工作站，再繼續。

接下來，我們將會安裝刀 Azure 副檔名。 這會提供刀 」 Azure 外掛程式 」。

執行下列命令。

    chef gem install knife-azure ––pre

> [AZURE.NOTE] – 前引數可確保您收到刀 Azure 外掛程式用來提供最新的設定 Api 的存取 RC 的最新版本。

很可能也會同時安裝相依性的數字。

![][8]


若要確保一切已正確設定，請執行下列命令。

    knife azure image list

如果所有項目已正確設定，您會看到可用 Azure 影像捲動的清單。

恭喜。 設定工作站 ！

##<a name="creating-a-cookbook"></a>建立食譜

食譜主廚，用於定義一組您想要在您受管理的用戶端上執行的命令。 建立食譜很簡單，我們使用**主廚產生食譜**命令來產生我們食譜範本。 我會呼叫我食譜的網頁伺服器要自動部署 IIS 原則。

在 [您 C:\Chef 目錄中，執行下列命令。

    chef generate cookbook webserver

這會產生一組 C:\Chef\cookbooks\webserver 目錄下的檔案。 現在，我們需要定義一組，我們希望我們受管理的虛擬機器上執行我們主廚用戶端的命令。

命令會儲存在檔案 default.rb。 在此檔案中，我會定義一組命令，安裝 IIS、 啟動 IIS，並將範本檔案複製到 wwwroot 資料夾。

修改 C:\chef\cookbooks\webserver\recipes\default.rb 檔案，並新增下列幾行。

    powershell_script 'Install IIS' do
        action :run
        code 'add-windowsfeature Web-Server'
    end

    service 'w3svc' do
        action [ :enable, :start ]
    end

    template 'c:\inetpub\wwwroot\Default.htm' do
        source 'Default.htm.erb'
        rights :read, 'Everyone'
    end

將檔案儲存在您完成。

## <a name="creating-a-template"></a>建立的範本

如先前所述，我們需要產生範本檔案，將會使用我們 default.html 頁面的形式。

執行下列命令以產生範本。

    chef generate template webserver Default.htm

現在瀏覽至 C:\chef\cookbooks\webserver\templates\default\Default.htm.erb 檔案。 新增一些簡單的 「 什麼 」 HTML 程式碼，以編輯檔案，然後再儲存檔案。



## <a name="upload-the-cookbook-to-the-chef-server"></a>上傳至主廚伺服器的食譜

在此步驟中，我們會參加我們已在本機電腦建立食譜複本，並將它上傳至主廚代管伺服器。 上傳後食譜會出現在 [**原則**] 索引標籤中。

    knife cookbook upload webserver

![][9]

## <a name="deploy-a-virtual-machine-with-knife-azure"></a>部署與刀 Azure 虛擬機器

現在，我們會部署 Azure 虛擬機器，並套用 「 web 伺服器上 「 食譜會安裝我們 IIS web 服務與預設的網頁。

若要這麼做，請使用**刀 azure 伺服器建立**命令。

正在命令的範例顯示下一步。

    knife azure server create --azure-dns-name 'diegotest01' --azure-vm-name 'testserver01' --azure-vm-size 'Small' --azure-storage-account 'portalvhdsxxxx' --bootstrap-protocol 'cloud-api' --azure-source-image 'a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-Datacenter-201411.01-en.us-127GB.vhd' --azure-service-location 'Southeast Asia' --winrm-user azureuser --winrm-password 'myPassword123' --tcp-endpoints 80,3389 --r 'recipe[webserver]'

參數是自我闡明。 您的特定變數替代並執行。

> [AZURE.NOTE] 透過命令列中，我正在也我端點網路篩選規則使用自動化 – tcp 端點參數。 已開啟連接埠 80 和 3389 來存取我的網站] 頁面和 RDP 工作階段。

當您執行命令時，移至 Azure 入口網站，您會看到您的電腦開始進行佈建。

![][13]

命令提示字元顯示下一步。

![][10]

部署完成後，我們無法連線至 web 服務連接埠 80 我們佈建虛擬機器刀 Azure 命令時，我們已開啟連接埠。 為這個虛擬機器是唯一的虛擬機器我雲端服務中，它會連線雲端服務的 url。

![][11]

如您所見，我收到創意與我的 HTML 程式碼。

別忘了我們也可以連線到從 Azure 傳統的入口網站，透過連接埠 3389 RDP 工作階段。

希望這只是很有幫助 ！ 移，並立即啟動的程式碼生涯時使用 Azure 基礎結構 ！


<!--Image references-->
[2]: ./media/virtual-machines-windows-chef-automation/2.png
[3]: ./media/virtual-machines-windows-chef-automation/3.png
[4]: ./media/virtual-machines-windows-chef-automation/4.png
[5]: ./media/virtual-machines-windows-chef-automation/5.png
[6]: ./media/virtual-machines-windows-chef-automation/6.png
[7]: ./media/virtual-machines-windows-chef-automation/7.png
[8]: ./media/virtual-machines-windows-chef-automation/8.png
[9]: ./media/virtual-machines-windows-chef-automation/9.png
[10]: ./media/virtual-machines-windows-chef-automation/10.png
[11]: ./media/virtual-machines-windows-chef-automation/11.png
[13]: ./media/virtual-machines-windows-chef-automation/13.png


<!--Link references-->
