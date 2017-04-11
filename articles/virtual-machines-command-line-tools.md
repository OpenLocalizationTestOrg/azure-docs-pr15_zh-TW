<properties
    pageTitle="Azure 服務管理模式的 CLI 命令 |Microsoft Azure"
    description="服務管理模式來管理部署傳統部署模型中的 azure 的命令列介面 (CLI) 命令"
    services="virtual-machines-linux,virtual-machines-windows,mobile-services, cloud-services"
    documentationCenter=""
    authors="dlepow"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="multiple"
    ms.workload="multiple"
    ms.tgt_pltfrm="vm-multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/22/2016"
    ms.author="danlep"/>

# <a name="azure-cli-commands-in-azure-service-management-asm-mode"></a>Azure 服務管理 (asm) 模式中的 azure CLI 命令

[AZURE.INCLUDE [learn-about-deployment-models](../includes/learn-about-deployment-models-classic-include.md)]您也可以[閱讀有關的所有資源管理員模型命令](virtual-machines/azure-cli-arm-commands.md)，並使用 CLI 資源管理員模型傳統從[移轉資源](virtual-machines/virtual-machines-linux-cli-migration-classic-resource-manager.md)。

本文提供您經常使用來建立和管理 Azure 資源在傳統的部署模型中的 Azure CLI 命令的語法和選項。 您可以在 Azure 服務管理 (asm) 模式下執行 CLI 存取這些命令。 這不是完整的參照，請與您 CLI 版本可能稍有不同的命令或參數會顯示。 

若要開始，第一個[安裝 Azure CLI](xplat-cli-install.md)並[連線到您 Azure 的訂閱](xplat-cli-connect.md)。

目前的命令語法和命令列選項，輸入`azure help`或以顯示特定的命令，說明`azure help [command]`。 建立及管理特定 Azure 服務的文件中也尋找 CLI 範例。

選用的參數會顯示以方括弧括住 (例如， `[parameter]`)。 所有其他參數是必要。

除了命令特定此處所列的選擇性參數，有三個可用於顯示詳細的輸出，例如 [要求選項和狀態碼的選擇性參數。 `-v`參數提供輸出的詳細資訊，以及`-vv`參數提供更詳細的輸出詳細資訊。 `--json`選項輸出原始 json 格式中的結果。

## <a name="setting-asm-mode"></a>設定 asm 模式

您可以使用下列命令來啟用 Azure CLI 服務管理模式命令。

    azure config mode asm

>[AZURE.NOTE] CLI Azure 資源管理員模式和 Azure 服務管理模式互斥。 就是無法從其他模式管理建立一個模式中的資源。

## <a name="manage-your-account-information-and-publish-settings"></a>管理您的帳戶資訊並發佈設定
CLI 可以連線至您帳戶的其中一個方法是使用 Azure 訂閱資訊。 （取得其他選項，請參閱[連線至 Azure CLI Azure 訂閱](xplat-cli-connect.md)）。從 Azure 傳統的入口網站，如下所述的發佈設定檔中可以取得這項資訊。 常設本機組態設定 CLI 用於後續作業時，您可以匯入的發佈設定檔。 您只需要匯入您一次發佈設定。

**帳戶下載 [選項]**

這個命令會啟動下載.publishsettings 檔案從 Azure 傳統入口網站瀏覽器。

    ~$ azure account download
    info:   Executing command account download
    info:   Launching browser to https://windows.azure.com/download/publishprofile.aspx
    help:   Save the downloaded file, then execute the command
    help:   account import <file>
    info:   account download command OK

**帳戶匯入 [選項]&lt;檔案 >**


這個命令匯入 publishsettings 檔案或憑證，使其可讓工具供日後的工作階段。

    ~$ azure account import publishsettings.publishsettings
    info:   Importing publish settings file publishsettings.publishsettings
    info:   Found subscription: 3-Month Free Trial
    info:   Found subscription: Pay-As-You-Go
    info:   Setting default subscription to: 3-Month Free Trial
    warn:   The 'publishsettings.publishsettings' file contains sensitive information.
    warn:   Remember to delete it now that it has been imported.
    info:   Account publish settings imported successfully

> [AZURE.NOTE] Publishsettings 檔案可以包含多個訂閱的相關細節 （也就是訂閱名稱和 ID）。 Publishsettings 檔案時，會將第一個訂閱作為預設的描述。 若要使用不同的訂閱，請執行下列命令︰
<code>~$ azure config set subscription &lt;other-subscription-id&gt;</code>

**帳戶清除 [選項]**

這個命令移除已匯入儲存的 publishsettings。 如果您完成這台電腦上使用的工具，請使用這個命令，而想要確保的工具不能與您的帳戶供日後的工作階段。

    ~$ azure account clear
    Clearing account info.
    info:   OK

**帳戶] 清單中 [的選項]**

清單匯入的訂閱

    ~$ azure account list
    info:    Executing command account list
    data:    Name                                    Id
           Current
    data:    --------------------------------------  -------------------------------
    -----  -------
    data:    Forums Subscription                     8679c8be-3b05-49d9-b8fb  true
    data:    Evangelism Team Subscription            9e672699-1055-41ae-9c36  false
    data:    MSOpenTech-Prod                         c13e6a92-706e-4cf5-94b6  false

**[選項] 設定的帳戶&lt;訂閱&gt;**

設定目前的訂閱

###<a name="commands-to-manage-your-affinity-groups"></a>若要管理您的相關性群組的命令

**帳戶相關性群組] 清單中 [的選項]**

這個命令可列出 Azure 相關性群組。

當群組的虛擬機器實體的多部電腦時，可以設定相關性群組。 相關性群組指定實體機器應該接近彼此越好，以減少網路延遲。

    ~$ azure account affinity-group list
    + Fetching affinity groups
    data:   Name                                  Label   Location
    data:   ------------------------------------  ------  --------
    data:   535EBAED-BF8B-4B18-A2E9-8755FB9D733F  opentec  West US
    info:   account affinity-group list command OK

**帳戶相關性群組建立 [選項]&lt;名稱&gt;**

這個命令會建立關聯群組

    ~$ azure account affinity-group create opentec -l "West US"
    info:    Executing command account affinity-group create
    + Creating affinity group
    info:    account affinity-group create command OK

**帳戶相關性群組顯示 [選項]&lt;名稱&gt;**

這個命令會顯示相關性群組的詳細資料

    ~$ azure account affinity-group show opentec
    info:    Executing command account affinity-group show
    + Getting affinity groups
    data:    $ xmlns "http://schemas.microsoft.com/windowsazure"
    data:    $ xmlns:i "http://www.w3.org/2001/XMLSchema-instance"
    data:    Name "opentec"
    data:    Label "b3BlbnRlYw=="
    data:    Description $ i:nil "true"
    data:    Location "West US"
    data:    HostedServices ""
    data:    StorageServices ""
    data:    Capabilities Capability 0 "PersistentVMRole"
    data:    Capabilities Capability 1 "HighMemory"
    info:    account affinity-group show command OK

**帳戶相關性群組刪除 [選項]&lt;名稱&gt;**

這個命令會刪除指定的相關性群組

    ~$ azure account affinity-group delete opentec
    info:    Executing command account affinity-group delete
    Delete affinity group opentec? [y/n] y
    + Deleting affinity group
    info:    account affinity-group delete command OK

###<a name="commands-to-manage-your-account-environment"></a>管理您的帳戶環境的命令

**帳戶信封] 清單中 [的選項]**

帳戶環境的清單

    C:\windows\system32>azure account env list
    info:    Executing command account env list
    data:    Name
    data:    ---------------
    data:    AzureCloud
    data:    AzureChinaCloud
    info:    account env list command OK

**帳戶信封顯示 [選項] [環境]**

顯示帳戶環境的詳細資料

    ~$ azure account env show
    info:    Executing command account env show
    Environment name: AzureCloud
    data:    Environment publishingProfile  http://go.microsoft.com/fwlink/?LinkId=2544
    data:    Environment portal  http://go.microsoft.com/fwlink/?LinkId=2544
    info:    account env show command OK

**信封新增 [選項] [環境] 的帳戶**

此命令新增到該帳戶的環境

**帳戶信封設定 [環境] [選項]**

設定帳戶環境

**信封刪除 [選項] [環境] 的帳戶**

這個命令從該帳戶會刪除指定的環境

## <a name="commands-to-manage-your-classic-virtual-machines"></a>管理您傳統的虛擬機器的命令
下圖顯示如何傳統 Azure 虛擬機器會裝載於 Azure 雲端服務的生產部署環境。

![Azure 技術的圖表](./media/virtual-machines-command-line-tools/architecturediagram.jpg)

**建立新**建立的磁碟機中 blob 儲存體 (也就是 e:\ 在圖表中)。**附加**至虛擬機器附加已經建立但從未附加的磁碟。

**vm 建立 [選項] &lt;dns 名稱 >&lt;圖像 >&lt;使用者名稱 > [密碼]**

這個命令會建立 Azure 虛擬機器。 根據預設，每個虛擬機器 (vm) 會建立在雲端服務。 您可以指定至現有的雲端服務，透過使用-c 選項應該新增的虛擬機器，如以下所述。

Vm 建立命令，例如 Azure 傳統入口網站，然後只建立生產部署環境中的 [虛擬機器。 沒有任何選項，來建立虛擬機器暫存部署環境中的雲端服務。 如果您的訂閱未與任何現有的 Azure 儲存體帳戶，[] 命令會建立一個。

您可以指定位置透過--位置參數，您可以指定相關性群組或透過--相關性群組參數。 如果兩者都提供，系統會提示您提供一個有效的位置清單。

所提供的密碼必須是 8 123 字元的長度，而且符合複雜度要求您使用這個虛擬機器的作業系統。

如果您打算使用 SSH 管理部署的 Linux 虛擬機器 （如通常是大小寫），您必須先啟用 SSH 透過-e 選項，當您建立虛擬機器。 無法建立虛擬機器後，啟用 SSH。

Windows 虛擬機器可以啟用 RDP 稍後新增連接埠 3389 為端點。

這個命令可支援下列選擇性參數︰

**-c，-連線**代管服務中建立虛擬機器中的已建立的部署。 如果-vmname 不是使用此選項，就會自動產生新的虛擬機器中的名稱。<br />
**n、-vm 名稱**指定虛擬機器的名稱。 這個參數會依預設使用裝載的服務名稱。 如果未指定-vmname，產生新的虛擬機器的名稱為&lt;服務名稱 >&lt;識別碼 >，其中&lt;識別碼 > 現有的虛擬機器中的服務加上 1 數。 例如，如果您使用此命令新增至代管服務 MyService 含有一個現有的虛擬機器的虛擬機器，新的虛擬機器稱為 MyService2。<br />
**-u、-blob url**指定用來建立虛擬機器系統磁碟的目標 blob 儲存體 URL。 <br />
**z、-虛擬記憶體-大小**指定虛擬機器的大小。 有效的值: 「 ExtraSmall 」、 「 小 」、 「 中 」、 「 大 」、 「 ExtraLarge 」、 」 A5 」、 」 A6 」、 「 A7 」、 「 A8 」、 「 A9 」、 「 A10 」、 」 A11 」、 「 Basic_A0 」、 「 Basic_A1 」、 「 Basic_A2 」、 「 Basic_A3 」、 「 Basic_A4 」、 「 Standard_D1 」、 「 Standard_D2 」、 「 Standard_D3 」、 「 Standard_D4 」、 「 Standard_D11 」、 「 Standard_D12 」、 「 Standard_D13 」、 「 Standard_D14 」、 「 Standard_DS1 」、 「 Standard_DS2 」、 「 Standard_DS3 」、 「 Standard_DS4 」、 「 Standard_DS11 」、 「 Standard_DS12 」、 「 Standard_DS13 」、 「 Standard_DS14 」、 「 Standard_G1 」、 「 Standard_G2 」「 Standard_G3 」、 「 Standard_G4 」、 「 Standard_G55 」。 預設值為 「 小 」。 <br />
**-r**新增 RDP 連線到 Windows 虛擬機器。 <br />
**-e-ssh**新增 SSH 連線到 Windows 虛擬機器。 <br />
**-t、-ssh 憑證**指定 SSH 憑證。 <br />
**-s**訂閱 <br />
**command 和 o，-社群**指定的圖像是社群圖像 <br />
**-w**虛擬網路名稱 <br/>
**-l、-位置**指定的位置 （例如，「 北美美國中部 」）。 <br />
**-、-相關性群組**指定的相關性群組。<br />
**-w，-虛擬網路名稱**指定要加入新的虛擬機器的虛擬網路。 虛擬網路可以設定及管理從 Azure 傳統入口網站。<br />
**-b、-子網路名稱**指定要指派虛擬機器的子網路名稱。

在此範例中，MSFT__Win2K8R2SP1-120514-1520-141205-01-en-us-30GB 是提供平台的圖像。 如需有關作業系統圖像的詳細資訊，請參閱 vm 圖像清單。

    ~$ azure vm create my-vm-name MSFT__Windows-Server-2008-R2-SP1.11-29-2011 username --location "West US" -r
    info:   Executing command vm create
    Enter VM 'my-vm-name' password: ************
    info:   vm create command OK

**vm 建立-從&lt;dns 名稱 >&lt;角色檔案 >**

這個命令建立 Azure 虛擬機器從 JSON 角色檔案。

    ~$ azure vm create-from my-vm example.json
    info:   OK

**vm 清單 [選項]**

這個命令可列出 Azure 虛擬機器。 -Json 選項指定以原始 JSON 格式會傳回結果。

    ~$ azure vm list
    info:   Executing command vm list
    data:   DNS Name                          VM Name      Status
    data:   --------------------------------  -----------  ---------
    data:   my-vm-name.cloudapp-preview.net        my-vm        ReadyRole
    info:   vm list command OK

**vm 位置] 清單中 [的選項]**

這個命令可列出所有可用的 Azure 帳戶的位置。

    ~$ azure vm location list
    info:   Executing command vm location list
    data:   Name                   Display Name
    data:   ---------------------  ------------
    data:   Azure Preview  West US
    info:   account location list command OK

**vm 顯示 [選項]&lt;名稱 >**

這個命令顯示 Azure 虛擬機器相關詳細資料。 -Json 選項指定以原始 JSON 格式會傳回結果。

    ~$ azure vm show my-vm
    info:   Executing command vm show
    data:   {
    data:       InstanceSize: 'Small',
    data:       InstanceStatus: 'ReadyRole',
    data:       DataDisks: [],
    data:       IPAddress: '10.26.192.206',
    data:       DNSName: 'my-vm.cloudapp.net',
    data:       InstanceStateDetails: {},
    data:       VMName: 'my-vm',
    data:       Network: {
    data:           Endpoints: [
    data:               {
    data:                   Protocol: 'tcp',
    data:                   Vip: '65.52.250.250',
    data:                   Port: '63238' ,
    data:                   LocalPort: '3389',
    data:                   Name: 'RemoteDesktop'
    data:               }
    data:           ]
    data:       },
    data:       Image: 'MSFT__Windows-Server-2008-R2-SP1.11-29-2011',
    data:       OSVersion: 'WA-GUEST-OS-1.18_201203-01'
    data:   }
    info:   vm show command OK

**vm 刪除 [選項]&lt;名稱 >**

這個命令刪除 Azure 虛擬機器。 根據預設，這個命令並不會刪除 Azure blob 的作業系統磁碟與資料磁碟的建立。 若要刪除 blob 所依據的虛擬機器，指定-b 選項。

    ~$ azure vm delete my-vm
    info:   Executing command vm delete
    info:   vm delete command OK

**vm 啟動 [選項]&lt;名稱 >**

這個命令會啟動 Azure 虛擬機器。

    ~$ azure vm start my-vm
    info:   Executing command vm start
    info:   vm start command OK

**vm 重新啟動 [選項]&lt;名稱 >**

這個命令重新啟動 Azure 虛擬機器。

    ~$ azure vm restart my-vm
    info:   Executing command vm restart
    info:   vm restart command OK

**vm 關閉 [選項]&lt;名稱 >**

這個命令關閉 Azure 虛擬機器。 您可能會以指定計算資源的不在關閉釋放使用-p 選項。

```
~$ azure vm shutdown my-vm
info:   Executing command vm shutdown
info:   vm shutdown command OK  
```

**vm 擷取&lt;vm 名稱 >&lt;目標影像名稱 >**

這個命令擷取 Azure 虛擬機器圖像。

**停止**虛擬機器狀態時，只可以擷取的虛擬機器圖像。 繼續進行之前，請先關閉虛擬機器。

    ~$ azure.cmd vm capture my-vm mycaptureimagename --delete
    info:   Executing command vm capture
    + Fetching VMs
    + Capturing VM
    info:   vm capture command OK

**[選項] 的 vm 匯出&lt;vm 名稱 >&lt;檔案路徑 >**

這個命令將 Azure 虛擬機器圖像匯出至檔案

    ~$ azure vm export "myvm" "C:\"
    info:    Executing command vm export
    + Getting virtual machines
    + Exporting the VM
    info:   vm export command OK

##  <a name="commands-to-manage-your-azure-virtual-machine-endpoints"></a>若要管理您的 Azure 虛擬機器結束點] 命令
下圖顯示多個執行個體的傳統的虛擬機器一般部署架構。 在此範例中，連接埠 3389 是開啟每個虛擬機器上 （RDP 存取）。 路由流量至虛擬機器負載平衡器所使用的每個虛擬機器上也有內部 IP 位址 (例如，168.55.11.1)。 此內部的 IP 位址也可以用於虛擬機器之間的通訊。

![azurenetworkdiagram](./media/virtual-machines-command-line-tools/networkdiagram.jpg)

外部要求虛擬機器可幫助負載平衡器。 因此無法針對特定的虛擬機器部署上多個虛擬機器指定要求。 使用多個虛擬機器的部署，連接埠對應必須設定虛擬機器 （vm 連接埠） 與負載平衡器 （lb 連接埠） 之間。

**建立 vm 端點&lt;vm 名稱 > &lt;lb 連接埠 > [vm 連接埠]**

這個命令會建立虛擬機器結束點。 您也可以使用-u 或--啟用行銷-伺服器傳回指定是否要啟用直接伺服器會在傳回此端點，依預設停用。

    ~$ azure vm endpoint create my-vm 8888 8888
    azure vm endpoint create my-vm 8888 8888
    info:   Executing command vm endpoint create
    + Fetching VM
    + Reading network configuration
    + Updating network configuration
    info:   vm endpoint create command OK

**vm 端點建立多重 [選項] &lt;vm 名稱 > &lt;lb 連接埠 > [:&lt;vm 連接埠 > [:&lt;通訊協定 > [:&lt;啟用行銷-伺服器傳回 > [:&lt;lb 集名稱 > [:&lt;探查通訊協定 > [:&lt;探查連接埠 > [:&lt;探查路徑 > [:&lt;內部 lb 名稱 >]]] {1-*}**

建立多個 vm 結束點。

**vm 端點刪除 [選項] &lt;vm 名稱 >&lt;結束點名稱 >**

這個命令刪除虛擬機器結束點。

    ~$ azure vm endpoint delete my-vm http
    azure vm endpoint delete my-vm http
    info:   Executing command vm endpoint delete
    + Fetching VM
    + Reading network configuration
    + Updating network configuration
    info:   vm endpoint delete command OK

**vm 端點清單&lt;vm 名稱 >**

這個命令可列出所有的虛擬機器結束點。 -Json 選項指定以原始 JSON 格式會傳回結果。

    ~$ azure vm endpoint list my-linux-vm
    data:   Name  External Port  Local Port
    data:   ----  -------------  ----------
    data:   ssh   22             22

**vm 端點更新 [選項] &lt;vm 名稱 >&lt;結束點名稱 >**

這個命令會更新為新的值，使用這些選項 vm 結束點。

    -n, --endpoint-name <name>          the new endpoint name
    -lo, --lb-port <port>                the new load balancer port
    -t, --vm-port <port>                the new local port
    -o, --endpoint-protocol <protocol>  the new transport layer protocol for port (tcp or udp)

**vm 端點會顯示 [選項] &lt;vm 名稱 >**

這個命令顯示 vm 端點的詳細資料

    ~$ azure vm endpoint show "mycouchvm"
    info:    Executing command vm endpoint show
    + Getting virtual machines
    data:    Network Endpoints 0 LoadBalancedEndpointSetName "CouchDB_EP-5984"
    data:    Network Endpoints 0 LocalPort "5984"
    data:    Network Endpoints 0 Name "CouchDB_EP"
    data:    Network Endpoints 0 Port "5984"
    data:    Network Endpoints 0 Protocol "tcp"
    data:    Network Endpoints 0 Vip "168.61.9.97"
    data:    Network Endpoints 1 LoadBalancedEndpointSetName "CouchEP_2-2020"
    data:    Network Endpoints 1 LocalPort "2020"
    data:    Network Endpoints 1 Name "CouchEP_2"
    data:    Network Endpoints 1 Port "2020"
    data:    Network Endpoints 1 Protocol "tcp"
    data:    Network Endpoints 1 Vip "168.61.9.97"
    data:    Network Endpoints 2 LocalPort "3389"
    data:    Network Endpoints 2 Name "RemoteDesktop"
    data:    Network Endpoints 2 Port "3389"
    data:    Network Endpoints 2 Protocol "tcp"
    data:    Network Endpoints 2 Vip "168.61.9.97"
    info:    vm endpoint show command OK

## <a name="commands-to-manage-your-azure-virtual-machine-images"></a>若要管理 Azure 虛擬機器圖像的命令

虛擬機器圖像位於已設定，可將複寫的虛擬機器擷取視需要而定。

**vm 影像清單 [的選項]**

這個命令取得虛擬機器圖像的清單。 有三種類型的圖像︰ 由 Microsoft 所建立的圖像會加上"MSFT"，其中會加上廠商的名稱，並圖像您的第三方所建立的圖像建立。 若要建立圖像，您可以擷取現有的虛擬機器，或從上傳至 blob 儲存體自訂.vhd 建立圖像。 如需有關如何使用自訂的.vhd 的詳細資訊，請參閱建立 vm 圖像。
-Json 選項指定以原始 JSON 格式會傳回結果。

    ~$ azure vm image list
    data:   Name                                                                   Category   OS
    data:   ---------------------------------------------------------------------  ---------  -------
    data:   CANONICAL__Canonical-Ubuntu-12-04-20120519-2012-05-19-en-us-30GB.vhd   Canonical  Linux
    data:   MSFT__Windows-Server-2008-R2-SP1.11-29-2011                            Microsoft  Windows
    data:   MSFT__Windows-Server-2008-R2-SP1-with-SQL-Server-2012-Eval.11-29-2011  Microsoft  Windows
    data:   MSFT__Windows-Server-8-Beta.en-us.30GB.2012-03-22                      Microsoft  Windows
    data:   MSFT__Windows-Server-8-Beta.2-17-2012                                  Microsoft  Windows
    data:   MSFT__Windows-Server-2008-R2-SP1.en-us.30GB.2012-3-22                  Microsoft  Windows
    data:   OpenLogic__OpenLogic-CentOS-62-20120509-en-us-30GB.vhd                 OpenLogic  Linux
    data:   SUSE__SUSE-Linux-Enterprise-Server-11SP2-20120521-en-us-30GB.vhd       SUSE       Linux
    data:   SUSE__OpenSUSE64121-03192012-en-us-15GB.vhd                            SUSE       Linux
    data:   WIN2K8-R2-WINRM                                                        User       Windows
    info:   vm image list command OK

**vm 圖像顯示 [選項]&lt;名稱 >**

這個命令會顯示的虛擬機器圖像的詳細資料。

    ~$ azure vm image show MSFT__Windows-Server-2008-R2-SP1.11-29-2011
    + Fetching VM image
    info:   Executing command vm image show
    data:   {
    data:       Label: 'Windows Server 2008 R2 SP1, Nov 2011',
    data:       Name: 'MSFT__Windows-Server-2008-R2-SP1.11-29-2011',
    data:       Description: 'Microsoft Windows Server 2008 R2 SP1',
    data:       @: { xmlns: 'http://schemas.microsoft.com/windowsazure', xmlns:i: 'http://www.w3.org/2001/XMLSchema-instance' },
    data:       Category: 'Microsoft',
    data:       OS: 'Windows',
    data:       Eula: 'http://www.microsoft.com',
    data:       LogicalSizeInGB: '30'
    data:   }
    info:   vm image show command OK

**vm 圖像刪除 [選項]&lt;名稱 >**

這個命令刪除虛擬機器圖像。

    ~$ azure vm image delete my-vm-image
    info:   Executing command vm image delete
    info:   VM image deleted: my-vm-image
    info:   vm image delete command OK

**建立 vm 圖像&lt;名稱 > [來源路徑]**

這個命令所建立的虛擬機器圖像。 您的自訂.vhd 檔案上傳至 blob 儲存體，，然後從該處建立虛擬機器圖像。 然後，您會使用此虛擬機器圖像來建立虛擬機器。 所需的位置和 OS 參數。

>[AZURE.NOTE]目前這個命令只支援固定的.vhd 上傳的檔案。 若要將檔案上傳動態.vhd，使用[Azure VHD 公用程式移](https://github.com/Microsoft/azure-vhd-utils-for-go)。

某些系統強制每個程序檔案描述項限制。 如果超過此限制，工具就會顯示檔案描述項限制錯誤。 您可以執行一次使用-p 命令&lt;數字 > 參數以減少平行上傳的最大數目。 預設平行上傳的數目上限為 96。

    ~$ azure vm image create mytestimage ./Sample.vhd -o windows -l "West US"
    info:   Executing command vm image create
    + Retrieving storage accounts
    info:   VHD size : 13 MB
    info:   Uploading 13312.5 KB
    Requested:100.0% Completed:100.0% Running: 105 Time:    8s Speed:  1721 KB/s
    info:   http://myaccount.blob.core.azure.com/vm-images/Sample.vhd is uploaded successfully
    info:   vm image create command OK

## <a name="commands-to-manage-your-azure-virtual-machine-data-disks"></a>若要管理您的 Azure 虛擬機器資料磁碟] 命令

資料磁碟是.vhd blob 儲存體可以用於虛擬機器中的檔案。 如需有關如何資料磁碟部署 blob 儲存空間，請參閱 Azure 技術圖表稍早所示。

附加資料磁碟的命令 (azure vm 磁碟附加和 azure vm 磁碟附加-新) 附加的資料磁碟，所需的 SCSI 通訊協定指派邏輯單位數字 (LUN)。 附加至虛擬機器的第一個資料磁碟指派 LUN 0，下一步是分派的 LUN 1，依此類推。

當您中斷連結 azure vm 磁碟的資料磁碟中斷連結] 命令，請使用&lt;lun&gt;參數指出哪一個磁碟中斷的連結。

>[AZURE.NOTE] 您應該一律卸離資料磁碟相反順序已指派的最高編號 LUN 開始著手。 Linux SCSI 圖層不支援較高編號 LUN 仍附加時中斷編號較低 LUN。 例如，您應該不中斷連結 LUN 0 仍附加 LUN 1。

**vm 磁碟顯示 [選項]&lt;名稱 >**

這個命令顯示 Azure 磁碟的詳細資料。

    ~$ azure vm disk show anucentos-anucentos-0-20120524070008
    info:   Executing command vm disk show
    data:   AttachedTo DeploymentName "mycentos"
    data:   AttachedTo HostedServiceName "myanucentos"
    data:   AttachedTo RoleName "myanucentos"
    data:   OS "Linux"
    data:   Location "Azure Preview"
    data:   LogicalDiskSizeInGB "30"
    data:   MediaLink "http://mystorageaccount.blob.core.azure-preview.com/vhd-store/mycentos-cb39b8223b01f95c.vhd"
    data:   Name "mycentos-mycentos-0-20120524070008"
    data:   SourceImageName "OpenLogic__OpenLogic-CentOS-62-20120509-en-us-30GB.vhd"
    info:   vm disk show command OK

**vm 磁碟清單 [選項] [vm 名稱]**

這個命令清單 Azure 磁碟或附加至指定的虛擬機器磁碟。 如果執行使用虛擬機器名稱參數時，則會傳回所有磁碟附加至虛擬機器。 虛擬機器中，以建立 Lun 1，然後分別附加任何其他列出的磁碟。

    ~$ azure vm disk list mycentos
    info:   Executing command vm disk list
    data:   Lun  Size(GB)  Blob-Name
    data:   ---  --------  --------------------------------
    data:   1    30        mycentos-cb39b8223b01f95c.vhd
    data:   2    10        mycentos-e3f0d717950bb78d.vhd
    info:   vm disk list command OK

執行此命令沒有虛擬機器名稱參數傳回的所有磁碟。

    ~$ azure vm disk list
    data:   Name                                        OS
    data:   ------------------------------------------  -------
    data:   mycentos-mycentos-0-20120524070008          Linux
    data:   mycentos-mycentos-2-20120525055052
    data:   mywindows-winvm-20120522223119              Windows
    info:   vm disk list command OK

**vm 磁碟刪除 [選項]&lt;名稱 >**

這個命令從個人存放庫刪除 Azure 磁碟。 遭到刪除前，必須從虛擬機器中斷磁碟。

    ~$ azure vm disk delete mycentos-mycentos-2-20120525055052
    info:   Executing command vm disk delete
    info:   Disk deleted: mycentos-mycentos-2-20120525055052
    info:   vm disk delete command OK

**建立 vm 磁碟&lt;名稱 > [來源路徑]**

這個命令上傳，且註冊 Azure 磁碟。 -blob url、-位置，或--必須指定相關性群組。 如果您使用 [來源路徑] 使用此命令時，所指定的.vhd 檔案上傳，並建立圖像。 您可以然後附加此圖像虛擬機器使用 vm 磁碟附加。

某些系統強制每個程序檔案描述項限制。 如果超過此限制，工具就會顯示檔案描述項限制錯誤。 您可以執行一次使用-p 命令&lt;數字 > 參數以減少平行上傳的最大數目。 預設平行上傳的數目上限為 96。

    ~$ azure vm disk create my-data-disk ~/test.vhd --location "West US"
    info:   Executing command vm disk create
    info:   VHD size : 10 MB
    info:   Uploading 10240.5 KB
    Requested:100.0% Completed:100.0% Running:  81 Time:   11s Speed:   952 KB/s
    info:   http://account.blob.core.azure.com/disks/test.vhd is uploaded successfully
    info:   vm disk create command OK

**vm 磁碟上傳 [選項]&lt;來源路徑 > &lt;blob url >&lt;儲存帳戶金鑰 >**

這個命令可讓您上傳 vm 磁碟

    ~$ azure vm disk upload "http://sourcestorage.blob.core.windows.net/vhds/sample.vhd" "http://destinationstorage.blob.core.windows.net/vhds/sample.vhd" "DESTINATIONSTORAGEACCOUNTKEY"
    info:   Executing command vm disk upload
    info:   Uploading 12351.5 KB
    info:   vm disk upload command OK

**vm 磁碟附加&lt;vm 名稱 >&lt;磁碟映像名稱 >**

這個命令會將現有的磁碟 blob 儲存體中附加至現有的虛擬機器部署雲端服務中。

    ~$ azure vm disk attach my-vm my-vm-my-vm-2-201242418259
    info:   Executing command vm disk attach
    info:   vm disk attach command OK

**vm 磁碟附加新&lt;vm 名稱 >&lt;大小中 gb > [blob url]**

這個命令將資料磁碟附加至 Azure 虛擬機器中。 在此範例中，20 是新磁碟 gb，要附加的大小。 或者，您可以使用的最後一個引數為 blob URL 明確指定目標 blob 建立。 如果您沒有指定 blob URL，會自動產生的 blob 物件。

    ~$ azure vm disk attach-new nick-test36 20 http://nghinazz.blob.core.azure-preview.com/vhds/vmdisk1.vhd
    info:   Executing command vm disk attach-new
    info:   vm disk attach-new command OK  

**vm 磁碟卸離&lt;vm 名稱 > &lt;lun >**

這個命令中斷連結至 Azure 虛擬機器連接的資料磁碟。 &lt;lun > 識別中斷連結磁碟。 若要取得的磁碟之前您中斷連結相關聯的磁碟清單，請使用 [vm 磁碟清單&lt;vm 名稱 >。

    ~$ azure vm disk detach my-vm 2
    info:   Executing command vm disk detach
    info:   vm disk detach command OK

## <a name="commands-to-manage-your-azure-cloud-services"></a>若要管理 Azure 雲端服務命令

Azure 雲端服務的應用程式與服務裝載於網頁角色和工作者角色。 下列命令可用於管理 Azure 雲端服務。

**服務建立 [選項] &lt;serviceName >**

這個命令會建立在雲端服務

    ~$ azure service create newservicemsopentech
    info:    Executing command service create
    + Getting locations
    help:    Location:
      1) East Asia
      2) Southeast Asia
      3) North Europe
      4) West Europe
      5) East US
      6) West US
      : 6
    + Creating cloud service
    data:    Cloud service name newservicemsopentech
    info:    service create command OK

**服務會顯示 [選項] &lt;serviceName >**

這個命令顯示 Azure 雲端服務的詳細資料

    ~$ azure service show newservicemsopentech
    info:    Executing command service show
    + Getting cloud service
    data:    Name newservicemsopentech
    data:    Url https://management.core.windows.net/9e672699-1055-41ae-9c36-e85152f2e352/services/hostedservices/newservicemsopentech
    data:    Properties location West US
    data:    Properties label newservicemsopentech
    data:    Properties status Created
    data:    Properties dateCreated
    data:    Properties dateLastModified
    info:    service show command OK

**服務] 清單中 [的選項]**

這個命令可列出 Azure 雲端服務。

    ~$ azure service list
    info:   Executing command service list
    data:   Name         Status
    data:   -----------  -------
    data:   service1     Created
    data:   service2     Created
    info:   service list command OK

**服務刪除 [選項]&lt;名稱 >**

這個命令刪除 Azure 雲端服務。

    ~$ azure service delete myservice
    info:   Executing command service delete myservice
    info:   cloud-service delete command OK

若要強制刪除時，使用`-q`參數。


## <a name="commands-to-manage-your-azure-certificates"></a>若要管理自己的 Azure 憑證] 命令

Azure 服務的憑證是連線至 Azure 帳戶的 SSL 憑證。 如需有關 Azure 憑證的詳細資訊，請參閱[管理憑證](http://msdn.microsoft.com/library/azure/gg981929.aspx)。

**服務的憑證] 清單中 [的選項]**

這個命令可列出 Azure 的憑證。

    ~$ azure service cert list
    info:   Executing command service cert list
    + Fetching cloud services
    + Fetching certificates
    data:   Service   Thumbprint                                Algorithm
    data:   --------  ----------------------------------------  ---------
    data:   myservice  262DBF95B5E61375FA27F1E74AC7D9EAE842916C  sha1
    info:   service cert list command OK

**服務的憑證建立&lt;dns 前置詞 >&lt;檔案 > [密碼]**

這個命令上傳的憑證。 將密碼的提示空白的不是密碼保護的憑證。

    ~$ azure service cert create nghinazz ~/publishSet.pfx
    info:   Executing command service cert create
    Cert password:
    + Creating certificate
    info:   service cert create command OK

**服務的憑證刪除 [選項]&lt;指紋 >**

這個命令刪除憑證。

    ~$ azure service cert delete 262DBF95B5E61375FA27F1E74AC7D9EAE842916C
    info:   Executing command service cert delete
    + Deleting certificate
    info:   nghinazz : cert deleted
    info:   service cert delete command OK

## <a name="commands-to-manage-your-web-apps"></a>管理 web 應用程式命令

Azure web 應用程式是由 URI 的無障礙 web 設定。 在虛擬機器中裝載 web 應用程式，但您不需要自行考慮建立及部署虛擬機器的詳細資料。 Azure 會為您處理這些詳細資料。

**[選項] 的網站清單**

這個命令可列出您的 web 應用程式。

    ~$ azure site list
    info:   Executing command site list
    data:   Name            State    Host names
    data:   --------------  -------  --------------------------------------------------
    data:   mongosite       Running  mongosite.antdf0.antares.windows.net
    data:   myphpsite       Running  myphpsite.antdf0.antares.windows.net
    data:   mydrupalsite36  Running  mydrupalsite36.antdf0.antares.windows.net
    info:   site list command OK

**網站設定 [選項] [名稱]**

設定 web 應用程式 [名稱] 的設定選項

    ~$ azure site set
    info:    Executing command site set
    Web site name: mydemosite
    + Getting sites
    + Updating site config information
    info:    site set command OK

**網站 deploymentscript [選項]**

這個命令會產生自訂部署指令碼

    ~$ azure site deploymentscript --node
    info:    Executing command site deploymentscript
    info:    Generating deployment script for node.js Web Site
    info:    Generated deployment script files
    info:    site deploymentscript command OK

**網站建立的 [選項] [名稱]**

這個命令建立 web 應用程式，並本機目錄。

    ~$ azure site create mysite
    info:   Executing command site create
    info:   Using location northeuropewebspace
    info:   Creating a new web site
    info:   Created web site at  mysite.antdf0.antares.windows.net
    info:   Initializing repository
    info:   Repository initialized
    info:   site create command OK

> [AZURE.NOTE] 網站名稱必須是唯一的。 您無法建立網站使用現有的網站的同一個 DNS 名稱。

**網站瀏覽] [選項] [名稱]**

這個命令會在瀏覽器中開啟 web 應用程式。

    ~$ azure site browse mysite
    info:   Executing command site browse
    info:   Launching browser to http://mysite.antdf0.antares-test.windows-int.net
    info:   site browse command OK

**網站顯示 [選項] [名稱]**

這個命令會顯示在 web 應用程式的詳細資料。

    ~$ azure site show mysite
    info:   Executing command site show
    info:   Showing details for site
    data:   Site AdminEnabled true
    data:   Site HostNames mysite.antdf0.antares-test.windows-int.net
    data:   Site Name mysite
    data:   Site Owner 00060000814EDDEE
    data:   Site RepositorySiteName mysite
    data:   Site SelfLink https://s1.api.antdf0.antares.windows.net:454/subscriptions/444e62ff-4c5f-4116-a695-5c803ed584a5/webspaces/northeuropewebspace/sites/mysite
    data:   Site State Running
    data:   Site UsageState Normal
    data:   Site WebSpace northeuropewebspace
    data:   Config AppSettings
    data:   Config ConnectionStrings
    data:   Config DefaultDocuments 0=Default.htm, 1=Default.asp, 2=index.htm, 3=index.html, 4=iisstart.htm, 5=default.aspx, 6=index.php, 7=hostingstart.aspx
    data:   Config DetailedErrorLoggingEnabled false
    data:   Config HttpLoggingEnabled false
    data:   Config Metadata
    data:   Config NetFrameworkVersion v4.0
    data:   Config NumberOfWorkers 1
    data:   Config PhpVersion 5.3
    data:   Config PublishingPassword rJ}[Er2v[Y]q16B6vTD]n$[C2z}Z.pvgLfRcLnAp%ax]xstiLny};o@vmMAote@d
    data:   Config RequestTracingEnabled false
    data:   Repository https://mysite.scm.antdf0.antares-test.windows-int.net/
    info:   site show command OK

**網站刪除 [選項] [名稱]**

這個命令刪除 web 應用程式。

    ~$ azure site delete mysite
    info:   Executing command site delete
    info:   Deleting site mysite
    info:   Site mysite has been deleted
    info:   site delete command OK

 **網站交換 [選項] [名稱]**

這個命令交換兩個 web 應用程式的位置。

這個命令支援下列其他選項︰

**-q 或**-無 * *: 不會提示進行確認。 自動化的指令碼中使用此選項。


**網站開始 [選項] [名稱]**

這個命令會啟動 web 應用程式。

    ~$ azure site start mysite
    info:   Executing command site start
    info:   Starting site mysite
    info:   Site mysite has been started
    info:   site start command OK

**網站停駐點 [選項] [名稱]**

這個命令停駐點的 web 應用程式。

    ~$ azure site stop mysite
    info:   Executing command site stop
    info:   Stopping site mysite
    info:   Site mysite has been stopped
    info:   site stop command OK

**網站重新啟動 [選項] [名稱]**

這個命令停駐點，便會開始在指定的 web 應用程式。

這個命令支援下列其他選項︰

**-位置**&lt;位置 >︰ 重新啟動位置的名稱。


**網站位置] 清單中 [的選項]**

這個命令可列出您的 web 應用程式位置。

    ~$ azure site location list
    info:    Executing command site location list
    + Getting locations
    data:    Name
    data:    ----------------
    data:    West Europe
    data:    West US
    data:    North Central US
    data:    North Europe
    data:    East Asia
    data:    East US
    info:    site location list command OK

###<a name="commands-to-manage-your-web-app-application-settings"></a>管理 web 應用程式的應用程式設定] 命令

**網站 appsetting 清單 [選項] [名稱]**

這個命令清單新增至 web 應用程式的應用程式設定。

    ~$ azure site appsetting list
    info:    Executing command site appsetting list
    Web site name: mydemosite
    + Getting sites
    + Getting site config information
    data:    Name  Value
    data:    ----  -----
    data:    test  value
    info:    site appsetting list command OK

**網站 appsetting 新增 [選項] &lt;keyvaluepair > [名稱]**

這個命令加入您的 web 應用程式為索引鍵值組應用程式設定。

    ~$ azure site appsetting add test=value
    info:    Executing command site appsetting add
    Web site name: mydemosite
    + Getting sites
    + Getting site config information
    + Updating site config information
    info:    site appsetting add command OK

**網站 appsetting 刪除 [選項]&lt;鍵 > [名稱]**

這個命令會刪除 web 應用程式中的指定的應用程式設定。

    ~$ azure site appsetting delete test
    info:    Executing command site appsetting delete
    Web site name: mydemosite
    + Getting sites
    + Getting site config information
    Delete application setting test? [y/n] y
    + Updating site config information
    info:    site appsetting delete command OK

**網站 appsetting 顯示 [選項]&lt;鍵 > [名稱]**

這個命令會顯示指定的應用程式設定的詳細資料

    ~$ azure site appsetting show test
    info:    Executing command site appsetting show
    Web site name: mydemosite
    + Getting sites
    + Getting site config information
    data:    Value:  value
    info:    site appsetting show command OK

###<a name="commands-to-manage-your-web-app-certificates"></a>若要管理您的 web 應用程式憑證的命令

**網站憑證清單 [選項] [名稱]**

這個命令顯示 web 應用程式憑證的清單。

    ~$ azure site cert list
    info:    Executing command site cert list
    Web site name: mydemosite
    + Getting sites
    + Getting site information
    data:    Subject                       Expiration Date                    Thumbprint
    data:    ----------------------------  -----------------------------------------
    ----------------  ----------------------------------------
    data:    *.msopentech.com              Fri Nov 28 2014 09:49:57 GMT-0800 (Pacific Standard Time)  A40E82D3DC0286D1F58650E570ECF8224F69A148
    data:    msopentech.azurewebsites.net  Fri Jun 19 2015 11:57:32 GMT-0700 (Pacific Daylight Time)  CE1CD6538852BF7A5DC32001C2E26A29B541F0E8
    info:    site cert list command OK

**網站憑證新增 [選項]&lt;憑證路徑 > [名稱]**

**網站憑證刪除 [選項]&lt;指紋 > [名稱]**

**網站憑證顯示 [選項]&lt;指紋 > [名稱]**

這個命令會顯示憑證詳細資料

    ~$ azure site cert show CE1CD65852B38DC32001C2E0E8F7A526A29B541F
    info:    Executing command site cert show
    Web site name: mydemosite
    + Getting sites
    + Getting site information
    data:    Certificate hostNames 0=msopentech.azurewebsites.net
    data:    Certificate expirationDate
    data:    Certificate friendlyName msopentech.azurewebsites.net
    data:    Certificate issueDate
    data:    Certificate issuer CN=MSIT Machine Auth CA 2, DC=redmond, DC=corp, DC=microsoft, DC=com
    data:    Certificate subjectName msopentech.azurewebsites.net
    data:    Certificate thumbprint CE1CD65852B38DC32001C2E0E8F7A526A29B541F
    info:    site cert show command OK

###<a name="commands-to-manage-your-web-app-connection-strings"></a>管理 web 應用程式連接字串的命令

**網站 [選項] [名稱] 的連接字串清單**

**網站連接字串新增 [選項] &lt;connectionname >&lt;值 >&lt;類型 > [名稱]**

**網站連接字串刪除 [選項] &lt;connectionname > [名稱]**

**網站連接字串顯示 [選項] &lt;connectionname > [名稱]**

###<a name="commands-to-manage-your-web-app-default-documents"></a>管理 web 應用程式的預設文件] 命令

**網站 defaultdocument 清單 [選項] [名稱]**

**網站 defaultdocument 新增 [選項]&lt;文件 > [名稱]**

**網站 defaultdocument 刪除 [選項]&lt;文件 > [名稱]**

###<a name="commands-to-manage-your-web-app-deployments"></a>若要管理您的 web 應用程式部署] 命令

**網站部署清單 [選項] [名稱]**

**網站部署顯示 [選項] &lt;commitId > [名稱]**

**網站部署重新部署的機會 [選項] &lt;commitId > [名稱]**

**網站部署 github [選項] [名稱]**

**網站部署使用者設定的 [選項] [使用者名稱] [傳遞]**

###<a name="commands-to-manage-your-web-app-domains"></a>若要管理您的 web 應用程式網域的命令

**[選項] [名稱] 的網站的網域清單**

**網站的網域新增 [選項] &lt;dn > [名稱]**

**網站網域刪除 [選項] &lt;dn > [名稱]**

###<a name="commands-to-manage-your-web-app-handler-mappings"></a>若要管理您的 web 應用程式處理常式對應的命令

**網站處理常式清單 [選項] [名稱]**

**網站處理常式新增 [選項]&lt;副檔名 >&lt;處理器 > [名稱]**

**網站處理常式刪除 [選項]&lt;副檔名 > [名稱]**

###<a name="commands-to-manage-your-web-jobs"></a>管理您的 Web 工作的命令

**[選項] [名稱] 的網站工作清單**

這個命令] 清單下 web app 的 web 工作。

這個命令支援下列選項︰

+ **-工作類型**&lt;工作類型 >︰ 選用。 Webjob 類型。 有效的值為 「 觸發 」 或 「 連續 」。 依預設會傳回所有類型的 webjobs。
+ **-位置**&lt;位置 >︰ 重新啟動位置的名稱。

**網站工作顯示 [選項]&lt;工作名稱 > &lt;jobType > [名稱]**

這個命令顯示特定的 web 工作的詳細資料。

這個命令支援下列選項︰

+ **-工作名稱**&lt;工作名稱 >︰ 必要。 Webjob 名稱。
+ **-工作類型**&lt;工作類型 >︰ 必要。 Webjob 類型。 有效的值為 「 觸發 」 或 「 連續 」。
+ **-位置**&lt;位置 >︰ 重新啟動位置的名稱。

**網站工作刪除 [選項]&lt;工作名稱 > &lt;jobType > [名稱]**

這個命令刪除指定的 web 工作。

這個命令支援下列選項︰

+ **-工作名稱**&lt;工作名稱 > 必要。 Webjob 名稱。
+ **-工作類型**&lt;工作類型 > 必要。 Webjob 類型。 有效的值為 「 觸發 」 或 「 連續 」。
+ **-q**或**-安靜**︰ 不會提示進行確認。 自動化的指令碼中使用此選項。
+ **-位置**&lt;位置 >︰ 重新啟動位置的名稱。

**網站工作上傳 [選項]&lt;工作名稱 > &lt;jobType > <jobFile> [名稱]**

這個命令刪除指定的 web 工作。

這個命令支援下列選項︰

+ **-工作名稱**&lt;工作名稱 >︰ 必要。 Webjob 名稱。
+ **-工作類型**&lt;工作類型 >︰ 必要。 Webjob 類型。 有效的值為 「 觸發 」 或 「 連續 」。
+ **-工作檔案**&lt;工作檔案 >︰ 必要。 工作檔案。
+ **-位置**&lt;位置 >︰ 重新啟動位置的名稱。

**網站工作開始 [選項]&lt;工作名稱 > &lt;jobType > [名稱]**

這個命令會啟動特定的 web 工作。

這個命令支援下列選項︰

+ **-工作名稱**&lt;工作名稱 >︰ 必要。 Webjob 名稱。
+ **-工作類型**&lt;工作類型 >︰ 必要。 Webjob 類型。 有效的值為 「 觸發 」 或 「 連續 」。
+ **-位置**&lt;位置 >︰ 重新啟動位置的名稱。

**網站工作停駐點 [選項]&lt;工作名稱 > &lt;jobType > [名稱]**

這個命令停止指定的 web 工作。 只有連續工作可能會停止。

這個命令支援下列選項︰

+ **-工作名稱**&lt;工作名稱 >︰ 必要。 Webjob 名稱。
+ **-位置**&lt;位置 >︰ 重新啟動位置的名稱。

###<a name="commands-to-manage-your-web-jobs-history"></a>若要管理您的 Web 工作歷程記錄] 命令

**網站工作歷程記錄清單的 [選項] [工作名稱] [名稱]**

這個命令顯示歷程記錄的特定的 web 工作的執行。

這個命令支援下列選項︰

+ **-工作名稱**&lt;工作名稱 >︰ 必要。 Webjob 名稱。
+ **-位置**&lt;位置 >︰ 重新啟動位置的名稱。

**網站工作歷程記錄會顯示 [選項] [工作名稱] [runid 為] [名稱]**

這個命令取得執行為指定的 web 工作的工作詳細資料。

這個命令支援下列選項︰

+ **-工作名稱**&lt;工作名稱 >︰ 必要。 Webjob 名稱。
+ **-執行識別碼**&lt;執行識別碼 >︰ 選用。 執行歷程記錄的識別碼。 如果未指定顯示最新的執行。
+ **-位置**&lt;位置 >︰ 重新啟動位置的名稱。

###<a name="commands-to-manage-your-web-app-diagnostics"></a>若要管理您的 web 應用程式診斷] 命令

**網站記錄下載 [選項] [名稱]**

下載.zip 檔案包含您的 web 應用程式診斷。

    ~$ azure site log download
    info:    Executing command site log download
    Web site name: mydemosite
    + Getting sites
    + Getting site information
    + Downloading diagnostic log to diagnostics.zip
    info:    site log download command OK

**網站記錄新增了 [選項] [名稱]**

這個命令連線終端機記錄串流服務。

    ~$ azure site log tail
    info:    Executing command site log tail
    Web site name: mydemosite
    + Getting sites
    + Getting site information
    2013-11-19T17:24:17  Welcome, you are now connected to log-streaming service.

**網站記錄設定 [選項] [名稱]**

這個命令設定 web 應用程式的診斷選項。

    ~$ azure site log set -a
    info:    Executing command site log set
    + Getting output options
    help:    Output:
      1) file
      2) storage
      : 1
    Web site name: mydemosite
    + Getting locations
    + Getting sites
    + Getting site information
    + Getting diagnostic settings
    + Updating diagnostic settings
    info:    site log set command OK

###<a name="commands-to-manage-your-web-app-repositories"></a>管理您的 web 應用程式存放庫的命令

**[選項] 的網站存放庫分支&lt;分支 > [名稱]**

**網站存放庫刪除 [選項] [名稱]**

**網站存放庫同步處理 [選項] [名稱]**

###<a name="commands-to-manage-your-web-app-scaling"></a>管理 web 應用程式比例縮放的命令

**[選項] 的網站比例模式&lt;模式 > [名稱]**

**網站比例執行個體的 [選項]&lt;執行個體 > [名稱]**


## <a name="commands-to-manage-azure-mobile-services"></a>若要管理 Azure 行動服務的命令

Azure 行動服務結合一的組 Azure 服務可讓您的應用程式的後端功能。 行動電話服務命令分成下列類別︰

+ [管理行動訊息服務的執行個體的命令](#Mobile_Services)
+ [管理行動訊息服務設定] 命令](#Mobile_Configuration)
+ [若要管理行動訊息服務的表格] 命令](#Mobile_Tables)
+ [管理行動訊息服務指令碼命令](#Mobile_Scripts)
+ [若要管理排程的工作] 命令](#Mobile_Jobs)
+ [若要縮放行動訊息服務的命令](#Mobile_Scale)

下列選項會套用至大部分的行動電話服務命令︰

+ **-h**或**-協助**︰ 顯示輸出的使用狀況資訊。
+ **-s`<id>`**或**-訂閱`<id>`**︰ 使用特定的訂閱，指定為`<id>`。
+ **-v**或**-詳細**︰ 撰寫輸出詳細資訊。
+ **-json**︰ 撰寫 JSON 輸出。

### <a name="Mobile_Services"></a>管理行動訊息服務的執行個體的命令

**行動裝置位置的 [選項]**

這個命令可列出受支援的行動電話服務的地理位置。

    ~$ azure mobile locations
    info:    Executing command mobile locations
    info:    East US (default)
    info:    West US
    info:    North Europe

**行動建立 [servicename] [sqlAdminUsername] [sqlAdminPassword] [選項]**

這個命令會建立並使用 SQL 資料庫與伺服器的行動訊息服務。

    ~$ azure mobile create todolist your_login_name Secure$Password
    info:    Executing command mobile create
    + Creating mobile service
    info:    Overall application state: Healthy
    info:    Mobile service (todolist) state: ProvisionConfigured
    info:    SQL database (todolist_db) state: Provisioned
    info:    SQL server (e96ean1c6v) state: ProvisionConfigured
    info:    mobile create command OK

這個命令支援下列選項︰

+ **-r`<sqlServer>`**或**-sqlServer `<sqlServer>` **︰ 使用現有的 SQL 資料庫伺服器，指定為`<sqlServer>`。
+ **-d`<sqlDb>`**或**-sqlDb `<sqlDb>` **︰ 使用現有的 SQL 資料庫，指定為`<sqlDb>`。
+ **-l`<location>`**或**-位置`<location>`**︰ 建立服務中的特定位置，指定為`<location>`。 執行 azure 行動的位置，以取得可用的位置。
+ **-sqlLocation `<location>` **︰ 建立 SQL server 中的特定`<location>`;預設值為行動訊息服務的位置。

**行動裝置刪除 [servicename] [選項]**

這個命令刪除行動訊息服務以及其的 SQL 資料庫伺服器。

    ~$ azure mobile delete todolist -a -q
    info:    Executing command mobile delete
    data:    Mobile service todolist
    data:    SQL database todolistAwrhcL60azo1C401
    data:    SQL server fh1kvbc7la
    + Deleting mobile service
    info:    Deleted mobile service
    + Deleting SQL server
    info:    Deleted SQL server
    + Deleting mobile application
    info:    Deleted mobile application
    info:    mobile delete command OK

這個命令支援下列選項︰

+ **-d**或**-deleteData**︰ 刪除此資料庫中的行動訊息服務中的所有資料。
+ **-**或**-deleteAll**︰ 刪除 SQL 資料庫伺服器。
+ **-q**或**-安靜**︰ 不會提示進行確認。 自動化的指令碼中使用此選項。

**行動裝置清單中 [的選項]**

這個命令可列出您的行動服務。

    ~$ azure mobile list
    info:    Executing command mobile list
    data:    Name          State  URL
    data:    ------------  -----  --------------------------------------
    data:    todolist      Ready  https://todolist.azure-mobile.net/
    data:    mymobileapp   Ready  https://mymobileapp.azure-mobile.net/
    info:    mobile list command OK

**行動裝置顯示 [選項] [servicename]**

這個命令顯示行動訊息服務的詳細資料。

    ~$ azure mobile show todolist
    info:    Executing command mobile show
    + Getting information
    info:    Mobile application
    data:    status Healthy
    data:    Mobile service name todolist
    data:    Mobile service status ProvisionConfigured
    data:    SQL database name todolistAwrhcL60azo1C401
    data:    SQL database status Linked
    data:    SQL server name fh1kvbc7la
    data:    SQL server status Linked
    info:    Mobile service
    data:    name todolist
    data:    state Ready
    data:    applicationUrl https://todolist.azure-mobile.net/
    data:    applicationKey XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    data:    masterKey XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    data:    webspace WESTUSWEBSPACE
    data:    region West US
    data:    tables TodoItem
    info:    mobile show command OK

**行動重新啟動 [servicename] [選項]**

這個命令重新啟動行動訊息服務的執行個體。

    ~$ azure mobile restart todolist
    info:    Executing command mobile restart
    + Restarting mobile service
    info:    Service was restarted.
    info:    mobile restart command OK

**行動記錄 [servicename] [選項]**

這個命令傳回行動訊息服務記錄檔，篩選出所有的記錄類型，但`error`。

    ~$ azure mobile log todolist -t error
    info:    Executing command mobile log
    data:
    data:    timeCreated 2013-01-07T16:04:43.351Z
    data:    type error
    data:    source /scheduler/TestingLogs.js
    data:    message This is an error.
    data:
    info:    mobile log command OK

這個命令支援下列選項︰

+ **-r`<query>`**或**-查詢`<query>`**︰ 執行指定的記錄查詢。
+ **-t`<type>`**或**-類型`<type>`**︰ 篩選項目所傳回的記錄`<type>`，它可以是`information`， `warning`，或`error`。
+ **-k`<skip>`**或**-略過`<skip>`**︰ 略過指定的資料列數目`<skip>`。
+ **-p`<top>`**或**-頂端`<top>`**︰ 傳回特定數目的列中，指定`<top>`。

> [AZURE.NOTE] **-查詢**參數會優先於**-類型**， **-略過**，及**-頂端**。

**行動裝置的復原 [unhealthyservicename] [healthyservicename] [選項]**

這個命令移到不同區域的健全行動訊息服務，來復原不佳的行動訊息服務。

這個命令支援下列其他選項︰

**-q**或**-安靜**︰ 隱藏復原的確認訊息的提示。

**行動裝置的金鑰重新產生的 [選項] [servicename] [類型]**

這個命令重新產生行動訊息服務應用程式鍵。

    ~$ azure mobile key regenerate todolist application
    info:    Executing command mobile key regenerate
    info:    New application key is SmLorAWVfslMcOKWSsuJvuzdJkfUpt40
    info:    mobile key regenerate command OK

主要類型`master`和`application`。

> [AZURE.NOTE] 當您重新產生金鑰時，使用舊的金鑰的用戶端可能無法存取您的行動訊息服務。 當您重新產生的應用程式鍵時，您應該使用新的索引鍵值來更新您的應用程式。

**行動裝置設定 [選項] [servicename] [類型] [值]**

設定行動訊息服務鍵為特定的值。


### <a name="Mobile_Configuration"></a>管理行動訊息服務設定] 命令

**行動裝置設定清單 [servicename] [選項]**

這個命令可列出行動訊息服務的設定選項。

    ~$ azure mobile config list todolist
    info:    Executing command mobile config list
    + Getting mobile service configuration
    data:    dynamicSchemaEnabled true
    data:    microsoftAccountClientSecret Not configured
    data:    microsoftAccountClientId Not configured
    data:    microsoftAccountPackageSID Not configured
    data:    facebookClientId Not configured
    data:    facebookClientSecret Not configured
    data:    twitterClientId Not configured
    data:    twitterClientSecret Not configured
    data:    googleClientId Not configured
    data:    googleClientSecret Not configured
    data:    apnsMode none
    data:    apnsPassword Not configured
    data:    apnsCertifcate Not configured
    info:    mobile config list command OK

**行動裝置設定取得 [servicename] [鍵] [選項]**

這個命令取得行動訊息服務，在本例中動態結構描述的特定設定選項。

    ~$ azure mobile config get todolist dynamicSchemaEnabled
    info:    Executing command mobile config get
    data:    dynamicSchemaEnabled true
    info:    mobile config get command OK

**設定行動裝置設定 [選項] [servicename] [鍵] [值]**

這個命令設定行動訊息服務，在本例中動態結構描述的特定設定選項。

    ~$ azure mobile config set todolist dynamicSchemaEnabled false
    info:    Executing command mobile config set
    info:    mobile config set command OK


### <a name="Mobile_Tables"></a>若要管理行動訊息服務的表格] 命令

**行動裝置的表格清單 [servicename] [選項]**

這個命令可列出您的行動訊息服務中的所有資料表。

    ~$azure mobile table list todolist
    info:    Executing command mobile table list
    data:    Name      Indexes  Rows
    data:    --------  -------  ----
    data:    Channel   1        0
    data:    TodoItem  1        0
    info:    mobile table list command OK

**行動裝置的表格會顯示 [選項] [servicename] [表格名稱]**

這個命令顯示傳回特定資料表的詳細資料。

    ~$azure mobile table show todolist
    info:    Executing command mobile table show
    + Getting table information
    info:    Table statistics:
    data:    Number of records 5
    info:    Table operations:
    data:    Operation  Script       Permissions
    data:    ---------  -----------  -----------
    data:    insert     1900 bytes   user
    data:    read       Not defined  user
    data:    update     Not defined  user
    data:    delete     Not defined  user
    info:    Table columns:
    data:    Name  Type           Indexed
    data:    ----  -------------  -------
    data:    id    bigint(MSSQL)  Yes
    data:    text      string
    data:    complete  boolean
    info:    mobile table show command OK

**行動裝置的資料表建立 [選項] [servicename] [表格名稱]**

這個命令建立的資料表。

    ~$azure mobile table create todolist Channels
    info:    Executing command mobile table create
    + Creating table
    info:    mobile table create command OK

這個命令支援下列其他選項︰

+ **-p`&lt;permissions>`**或**-權限`&lt;permissions>`**︰ 逗點分隔清單`<operation>` = `<permission>`組，其中`<operation>`是`insert`， `read`， `update`，或`delete`和`&lt;permissions>`是`public`， `application` （預設）、 `user`，或`admin`。

**讀取行動裝置的資料的 [選項] [servicename] [表格名稱] [查詢]**

這個命令會讀取表格中的資料。

    ~$azure mobile data read todolist TodoItem
    info:    Executing command mobile data read
    data:    id  text     complete
    data:    --  -------  --------
    data:    1   item #1  false
    data:    2   item #2  true
    data:    3   item #3  false
    data:    4   item #4  true
    info:    mobile data read command OK

這個命令支援下列選項︰

+ **-k`<skip>`**或**-略過`<skip>`**︰ 略過指定的資料列數目`<skip>`。
+ **-t`<top>`**或**-頂端`<top>`**︰ 傳回特定數目的列中，指定`<top>`。
+ **-l**或**-清單**︰ 傳回清單格式的資料。

**行動裝置的表格更新 [選項] [servicename] [表格名稱]**

這個命令會變更為 [僅限管理員的刪除權限的表格。

    ~$azure mobile table update todolist Channels -p delete=admin
    info:    Executing command mobile table update
    + Updating permissions
    info:    Updated permissions
    info:    mobile table update command OK

這個命令支援下列選項︰

+ **-p`&lt;permissions>`**或**-權限`&lt;permissions>`**︰ 逗點分隔清單`<operation>` = `<permission>`組，其中`<operation>`是`insert`， `read`， `update`，或`delete`和`&lt;permissions>`是`public`， `application` （預設）、 `user`，或`admin`。
+ **-deleteColumn `<columns>` **︰ 若要刪除的資料行逗點分隔清單為`<columns>`。
+ **-q**或**-安靜**︰ 而不提示您確認刪除欄。
+ **-addIndex `<columns>` **︰ 逗點分隔包含在索引中的資料行清單。
+ **-deleteIndex `<columns>` **︰ 逗點分隔要排除索引資料行清單。

**刪除 [選項] [servicename] [表格名稱] 的行動裝置的資料表**

這個命令刪除一個資料表。

    ~$azure mobile table delete todolist Channels
    info:    Executing command mobile table delete
    Do you really want to delete the table (yes/no): yes
    + Deleting table
    info:    mobile table delete command OK

指定-q 參數，刪除不確認的表格。 執行此防止封鎖自動化指令碼的動作。

**行動裝置資料捨去的 [選項] [servicename] [表格名稱]**

這個命令會移除資料表中的所有資料列的資料。

    ~$azure mobile data truncate todolist TodoItem
    info:    Executing command mobile data truncate
    info:    There are 7 data rows in the table.
    Do you really want to delete all data from the table? (y/n): y
    info:    Deleted 7 rows.
    info:    mobile data truncate command OK


### <a name="Mobile_Scripts"></a>若要管理指令碼命令

本節中的命令會用來管理屬於行動訊息服務的伺服器指令碼。 如需詳細資訊，請參閱[使用行動電話服務中的伺服器指令碼](https://github.com/Azure/azure-mobile-services/blob/master/docs/mobile-services-how-to-use-server-scripts.md)。

**行動裝置的指令碼清單 [servicename] [選項]**

這個命令可列出已註冊的指令碼，包括資料表和排程器指令碼。

    ~$azure mobile script list todolist
    info:    Executing command mobile script list
    + Getting script information
    info:    Table scripts
    data:    Name                   Size
    data:    ---------------------  ----
    data:    table/TodoItem.delete  256
    data:    table/Devices.insert   1660
    error:   Unable to get shared scripts
    info:    Scheduler scripts
    data:    Name                 Status     Interval   Last run   Next run
    data:    -------------------  ---------  ---------  ---------  ---------
    data:    scheduler/undefined  undefined  undefined  undefined  undefined
    data:    scheduler/undefined  undefined  undefined  undefined  undefined
    info:    mobile script list command OK

**行動裝置的指令碼下載 [servicename] [scriptname] [選項]**

這個命令下載 TodoItem 資料表中要命名的檔案插入指令碼`todoitem.insert.js`中`table`子資料夾。

    ~$azure mobile script download todolist table/todoitem.insert.js
    info:    Executing command mobile script download
    info:    Saved script to ./table/todoitem.insert.js
    info:    mobile script download command OK

這個命令支援下列選項︰

+ **-p`<path>`**或**-路徑`<path>`**︰ 目前的工作目錄是預設值的位置來儲存指令碼，檔案中的位置。
+ **-f`<file>`**或**-檔案`<file>`**︰ 要將指令碼儲存檔案的名稱。
+ **-o**或**-覆寫**︰ 覆寫現有的檔案。
+ **-c**或**-主控台**︰ 撰寫指令碼，而不是主控台檔案。

**行動裝置的指令碼上傳 [servicename] [scriptname] [選項]**

這個命令上傳指令碼名為`todoitem.insert.js`從`table`子資料夾。

    ~$azure mobile script upload todolist table/todoitem.insert.js
    info:    Executing command mobile script upload
    info:    mobile script upload command OK

從資料表和作業名稱包含檔案的名稱。 它必須位於資料表子資料夾相對於已執行此命令的位置。 您也可以使用**-f`<file>`**或**-檔案`<file>`**參數指定不同的檔名和包含要註冊指令碼之檔案的路徑。


**行動裝置的指令碼刪除 [servicename] [scriptname] [選項]**

這個命令會移除 TodoItem 資料表中的現有插入指令碼。

    ~$azure mobile script delete todolist table/todoitem.insert.js
    info:    Executing command mobile script delete
    info:    mobile script delete command OK

### <a name="Mobile_Jobs"></a>若要管理排程的工作] 命令

本節中的命令會用來管理屬於行動訊息服務的排程的工作。 如需詳細資訊，請參閱[排定工作](http://msdn.microsoft.com/library/windowsazure/jj860528.aspx)。

**行動裝置的工作清單的 [選項] [servicename]**

這個命令可列出已排程的工作。

    ~$azure mobile job list todolist
    info:    Executing command mobile job list
    info:    Scheduled jobs
    data:    Job name    Script name           Status    Interval     Last run              Next run
    data:    ----------  --------------------  --------  -----------  --------------------  --------------------
    data:    getUpdates  scheduler/getUpdates  enabled   15 [minute]  2013-01-14T16:15:00Z  2013-01-14T16:30:00Z
    info:    You can manipulate scheduled job scripts using the 'azure mobile script' command.
    info:    mobile job list command OK

**行動裝置的工作建立 [選項] [servicename] [工作名稱]**

這個命令建立名為工作`getUpdates`的排程執行每小時。

    ~$azure mobile job create -i 1 -u hour todolist getUpdates
    info:    Executing command mobile job create
    info:    Job was created in disabled state. You can enable the job using the 'azure mobile job update' command.
    info:    You can manipulate the scheduled job script using the 'azure mobile script' command.
    info:    mobile job create command OK

這個命令支援下列選項︰

+ **-i`<number>`**或**-間隔`<number>`**︰ 工作間隔，做為整數。 預設值是`15`。
+ **-u`<unit>`**或**-intervalUnit `<unit>` **︰ 的單位_間隔_，則可為下列值︰
    + **分鐘**（預設值）
    + **小時**
    + **一天**
    + **月**
    + **無**（視需要工作）
+ **-t `<time>`****-開始時間`<time>`**開始時間的第一次執行指令碼，ISO 格式。 預設值是`now`。

> [AZURE.NOTE] 因為必須仍可上傳指令碼停用狀態的建立新工作。 使用 [**行動裝置的指令碼上傳**] 命令上傳指令碼和**行動裝置的工作更新**的命令，以啟用工作。

**行動裝置的工作更新 [選項] [servicename] [工作名稱]**

下列命令可讓停用`getUpdates`工作。

    ~$azure mobile job update -a enabled todolist getUpdates
    info:    Executing command mobile job update
    info:    mobile job update command OK

這個命令支援下列選項︰

+ **-i`<number>`**或**-間隔`<number>`**︰ 工作間隔，做為整數。 預設值是`15`。
+ **-u`<unit>`**或**-intervalUnit `<unit>` **︰ 的單位_間隔_，則可為下列值︰
    + **分鐘**（預設值）
    + **小時**
    + **一天**
    + **月**
    + **無**（視需要工作）
+ **-t `<time>`****-開始時間`<time>`**開始時間的第一次執行指令碼，ISO 格式。 預設值是`now`。
+ **-`<status>`**或**-狀態`<status>`**︰ 工作狀態，可以是`enabled`或`disabled`。

**行動裝置的工作刪除 [選項] [servicename] [工作名稱]**

這個命令會移除 TodoList 伺服器 getUpdates 排程的工作。

    ~$azure mobile job delete todolist getUpdates
    info:    Executing command mobile job delete
    info:    mobile job delete command OK

> [AZURE.NOTE] 刪除工作時，也會刪除上傳指令碼。

### <a name="Mobile_Scale"></a>若要縮放行動訊息服務的命令

本節中的命令會使用不按比例縮放行動訊息服務。 如需詳細資訊，請參閱[行動訊息服務的縮放比例](http://msdn.microsoft.com/library/windowsazure/jj193178.aspx)。

**行動裝置的縮放比例顯示 [選項] [servicename]**

這個命令顯示小數位數的資訊，包括目前計算模式及執行個體數目。

    ~$azure mobile scale show todolist
    info:    Executing command mobile scale show
    data:    webspace WESTUSWEBSPACE
    data:    computeMode Free
    data:    numberOfInstances 1
    info:    mobile scale show command OK

**行動裝置的縮放比例變更 [servicename] [選項]**

這個命令會變更為進階模式從可用的行動訊息服務的小數位數。

    ~$azure mobile scale change -c Reserved -i 1 todolist
    info:    Executing command mobile scale change
    + Rescaling the mobile service
    info:    mobile scale change command OK

這個命令支援下列選項︰

+ **-c`<mode>`**或**-computeMode `<mode>` **︰ 計算模式必須`Free`或`Reserved`。
+ **-i`<count>`**或**-numberOfInstances `<count>` **︰ 保留模式中執行時所使用的執行個體數目。

> [AZURE.NOTE] 若要設定計算模式時`Reserved`，在進階模式中執行相同的範圍中所有行動服務。


###<a name="commands-to-enable-preview-features-for-your-mobile-service"></a>若要啟用您的行動訊息服務的預覽功能的命令

**行動裝置版清單 [servicename] [選項]**

這個命令會顯示可用的預覽功能指定的服務，以及是否啟用。

    ~$ azure mobile preview list mysite
    info:    Executing command mobile preview list
    + Getting preview features
    data:    Preview feature  Enabled
    data:    ---------------  -------
    data:    SourceControl    No
    data:    Users            No
    info:    You can enable preview features using the 'azure mobile preview enable' command.
    info:    mobile preview list command OK

**行動裝置版啟用 [servicename] [featurename] [選項]**

這個命令可讓行動訊息服務的指定的預覽功能。 一旦啟用，預覽功能無法停用的行動訊息服務。

###<a name="commands-to-manage-your-mobile-service-apis"></a>若要管理您的行動訊息服務的 Api 命令

**行動裝置的 api 清單 [servicename] [選項]**

這個命令會顯示清單行動服務建立您的行動訊息服務的自訂 Api。

    ~$ azure mobile api list mysite
    info:    Executing command mobile api list
    + Retrieving list of APIs
    info:    APIs
    data:    Name                  Get          Put          Post         Patch        Delete
    data:    --------------------  -----------  -----------  -----------  -----------  -----------
    data:    myCustomRetrieveAPI   application  application  application  application  application
    info:    You can manipulate API scripts using the 'azure mobile script' command.
    info:    mobile api list command OK

**行動裝置的 api 建立的 [選項] [servicename] [apiname]**

建立行動訊息服務的自訂 API

    ~$ azure mobile api create mysite myCustomRetrieveAPI
    info:    Executing command mobile api create
    + Creating custom API: 'myCustomRetrieveAPI'
    info:    API was created successfully. You can modify the API using the 'azure mobile script' command.
    info:    mobile api create command OK

這個命令支援下列其他選項︰

**-p**或**-權限**&lt;權限 >︰ 逗點分隔清單&lt;方法 > =&lt;權限 > 配對。

**[選項] [servicename] [apiname] 來更新行動 api**

這個命令就會更新指定行動訊息服務自訂 API。

這個命令支援下列其他選項︰

這個命令支援下列選項︰

+ **-p**或**-權限**&lt;權限 >︰ 逗點分隔清單&lt;方法 > =&lt;權限 > 配對。
+ **-f**或**-強制**︰ 覆寫任何自訂權限的中繼資料檔案的變更。

**行動裝置的 api 刪除 [servicename] [apiname] [選項]**

    ~$ azure mobile api delete mysite myCustomRetrieveAPI
    info:    Executing command mobile api delete
    + Deleting API: 'myCustomRetrieveAPI'
    info:    mobile api delete command OK

這個命令刪除指定行動訊息服務自訂 API。

###<a name="commands-to-manage-your-mobile-application-app-settings"></a>若要管理您的行動應用程式應用程式設定] 命令

**行動 appsetting 清單 [servicename] [選項]**

這個命令顯示指定的服務的行動應用程式應用程式設定。

    ~$ azure mobile appsetting list mysite
    info:    Executing command mobile appsetting list
    + Retrieving app settings
    data:    Name               Value
    data:    -----------------  -----
    data:    enablebetacontent  true
    info:    mobile appsetting list command OK

**行動 appsetting 新增 [選項] [servicename] [名稱] [值]**

這個命令會將您的行動訊息服務的自訂應用程式設定。

    ~$ azure mobile appsetting add mysite enablebetacontent true
    info:    Executing command mobile appsetting add
    + Retrieving app settings
    + Adding app setting
    info:    mobile appsetting add command OK

**行動 appsetting 刪除 [選項] [servicename] [名稱]**

這個命令會移除您的行動訊息服務的指定的應用程式設定。

    ~$ azure mobile appsetting delete mysite enablebetacontent
    info:    Executing command mobile appsetting delete
    + Retrieving app settings
    + Removing app setting 'enablebetacontent'
    info:    mobile appsetting delete command OK

**行動 appsetting 顯示 [選項] [servicename] [名稱]**

這個命令會移除您的行動訊息服務的指定的應用程式設定。

    ~$ azure mobile appsetting show mysite enablebetacontent
    info:    Executing command mobile appsetting show
    + Retrieving app settings
    info:    enablebetacontent: true
    info:    mobile appsetting show command OK

## <a name="manage-tool-local-settings"></a>管理工具本機設定

本機設定是您的訂閱識別碼和預設儲存體帳戶名稱。

**設定清單 [的選項]**

這個命令顯示組態設定。

    ~$ azure config list
    info:   Displaying config settings
    data:   Setting                Value
    data:   ---------------------  ------------------------------------
    data:   subscription           32-digit-subscription-key
    data:   defaultStorageAccount  name

**設定設定 [選項]&lt;名稱&gt;，&lt;值&gt;**

這個命令會變更組態設定。

    ~$ azure config set defaultStorageAccount myname
    info:   Setting 'defaultStorageAccount' to value 'myname'
    info:   Changes saved.

## <a name="commands-to-manage-service-bus"></a>管理服務匯流排命令

使用下列命令，管理您的服務匯流排帳戶

**sb 命名空間核取 [選項]&lt;名稱 >**

檢查服務匯流排命名空間法律和使用。

**建立 sb 命名空間&lt;名稱 >&lt;位置 >**

建立服務匯流排命名空間。

    ~$ azure sb namespace create mysbnamespacea-test "West US"
    info:    Executing command sb namespace create
    + Creating namespace mysbnamespacea-test in region West US
    data:    Name: mysbnamespacea-test
    data:    Region: West US
    data:    DefaultKey: fBu8nQ9svPIesFfMFVhCFD+/sY0rRbifWMoRpYy0Ynk=
    data:    Status: Activating
    data:    CreatedAt: 2013-11-14T16:23:29.32Z
    data:    AcsManagementEndpoint: https://mysbnamespacea-test-sb.accesscontrol.windows.net/
    data:    ServiceBusEndpoint: https://mysbnamespacea-test.servicebus.windows.net/

    data:    ConnectionString: Endpoint=sb://mysbnamespacea-test.servicebus.windows.
    net/;SharedSecretIssuer=owner;SharedSecretValue=fBu8nQ9svPIesFfMFVhCFD+/sY0rRbif
    WMoRpYy0Ynk=
    data:    SubscriptionId: 8679c8be3b0549d9b8fb4bd232a48931
    data:    Enabled: true
    data:    _: [object Object]
    info:    sb namespace create command OK


**刪除 sb 命名空間&lt;名稱 >**

移除命名空間。

    ~$ azure sb namespace delete mysbnamespacea-test
    info:    Executing command sb namespace delete
    Delete namespace mysbnamespacea-test? [y/n] y
    + Deleting namespace mysbnamespacea-test
    info:    sb namespace delete command OK

**sb 命名空間清單**

列出您的帳戶所建立的所有命名空間。

    ~$ azure sb namespace list
    info:    Executing command sb namespace list
    + Getting namespaces
    data:    Name                 Region   Status
    data:    -------------------  -------  ------
    data:    mysbnamespacea-test  West US  Active
    info:    sb namespace list command OK


**sb 命名空間位置] 清單**

顯示所有可用的命名空間位置的清單。

    ~$ azure sb namespace location list
    info:    Executing command sb namespace location list
    + Getting locations
    data:    Name              Code
    data:    ----------------  ----------------
    data:    East Asia         East Asia
    data:    West Europe       West Europe
    data:    North Europe      North Europe
    data:    East US           East US
    data:    Southeast Asia    Southeast Asia
    data:    North Central US  North Central US
    data:    West US           West US
    data:    South Central US  South Central US
    info:    sb namespace location list command OK

**sb 命名空間顯示&lt;名稱 >**

顯示特定的命名空間的詳細資料。

    ~$ azure sb namespace show mysbnamespacea-test
    info:    Executing command sb namespace show
    + Getting namespace
    data:    Name: mysbnamespacea-test
    data:    Region: West US
    data:    DefaultKey: fBu8nQ9svPIesFfMFVhCFD+/sY0rRbifWMoRpYy0Ynk=
    data:    Status: Active
    data:    CreatedAt: 2013-11-14T16:23:29.32Z
    data:    AcsManagementEndpoint: https://mysbnamespacea-test-sb.accesscontrol.windows.net/
    data:    ServiceBusEndpoint: https://mysbnamespacea-test.servicebus.windows.net/

    data:    ConnectionString: Endpoint=sb://mysbnamespacea-test.servicebus.windows.
    net/;SharedSecretIssuer=owner;SharedSecretValue=fBu8nQ9svPIesFfMFVhCFD+/sY0rRbif
    WMoRpYy0Ynk=
    data:    SubscriptionId: 8679c8be3b0549d9b8fb4bd232a48931
    data:    Enabled: true
    data:    UpdatedAt: 2013-11-14T16:25:37.85Z
    info:    sb namespace show command OK

**sb 命名空間驗證&lt;名稱 >**

檢查是否有可用的命名空間。

## <a name="commands-to-manage-your-storage-objects"></a>管理您的儲存空間物件的命令

###<a name="commands-to-manage-your-storage-accounts"></a>若要管理您儲存的帳戶] 命令

**儲存帳戶] 清單中 [的選項]**

這個命令會顯示儲存帳戶，您的訂閱。

    ~$ azure storage account list
    info:    Executing command storage account list
    + Getting storage accounts
    data:    Name             Label  Location
    data:    ---------------  -----  --------
    data:    mybasestorage           West US
    info:    storage account list command OK

**儲存帳戶顯示 [選項]<name>**

這個命令顯示包括 URI 與帳戶屬性指定的儲存帳戶的相關資訊。

**儲存帳戶建立 [選項]<name>**

這個命令建立儲存帳戶依照所提供的選項。

    ~$ azure storage account create mybasestorage --label PrimaryStorage --location "West US"
    info:    Executing command storage account create
    + Creating storage account
    info:    storage account create command OK

這個命令支援下列選項︰

+ **-e**或**-標籤**&lt;標籤 >︰ 儲存帳戶的標籤。
+ **-d**或**-描述**&lt;描述 >︰ 描述儲存帳戶。
+ **-l**或**-位置**&lt;名稱 >︰ 的地理區域中建立的儲存空間的帳戶。
+ **-**或**-相關性群組**&lt;名稱 >︰ 相關性群組和關聯的儲存空間的帳戶。 
+ **-類型**︰ 指出要建立的帳戶類型: [標準存放重複選項 (LRS/ZRS/GRS/RAGRS) 或進階版儲存空間 (PLRS)。

**儲存帳戶設定 [選項]<name>**

這個命令就會更新指定的儲存帳戶。

    ~$ azure storage account set mybasestorage --kind Storage --sku-name GRS
    info:    Executing command storage account set
    + Updating storage account
    info:    storage account set command OK

這個命令支援下列選項︰

+ **-e**或**-標籤**&lt;標籤 >︰ 儲存帳戶的標籤。
+ **-d**或**-描述**&lt;描述 >︰ 描述儲存帳戶。
+ **-l**或**-位置**&lt;名稱 >︰ 的地理區域中建立的儲存空間的帳戶。
+ **-類型**︰ 指出新的帳戶類型: [標準存放重複選項 (LRS/ZRS/GRS/RAGRS) 或進階版儲存空間 (PLRS)。

**儲存帳戶刪除 [選項]<name>**

這個命令刪除指定的儲存帳戶。

這個命令支援下列其他選項︰

**-q**或**-安靜**︰ 不會提示進行確認。 自動化的指令碼中使用此選項。

###<a name="commands-to-manage-your-storage-account-keys"></a>若要管理您儲存帳戶金鑰] 命令

**儲存帳戶金鑰] 清單中 [的選項]<name>**

這個命令可列出指定的儲存帳戶的主要和次要索引鍵。

**儲存帳戶金鑰更新的 [選項]<name>**

###<a name="commands-to-manage-your-storage-container"></a>管理您的存放容器的命令

**儲存容器清單 [首碼] [選項]**

這個命令顯示為指定的儲存帳戶存放容器清單。 儲存帳戶被指定的連接字串或儲存體帳戶名稱和帳戶金鑰。

這個命令支援下列選項︰

+ **-p**或**-首碼**&lt;前置詞 >︰ 存放容器名稱前置字元。
+ **-**或**-帳戶名稱** &lt;accountName >︰ 儲存體帳戶名稱。
+ **-k**或**-帳戶金鑰** &lt;accountKey >: 儲存帳戶金鑰]。
+ **-c**或**-連線字串**&lt;連接字串 >︰ 儲存空間連線字串。
+ **-偵錯**︰ 在 [偵錯模式中執行 [儲存] 命令。

**儲存容器顯示 [選項] [容器]**
**存放容器建立 [容器] [選項]**

這個命令建立儲存容器指定的儲存帳戶。 儲存帳戶被指定的連接字串或儲存體帳戶名稱和帳戶金鑰。

這個命令支援下列選項︰

+ **-容器**&lt;容器 >︰ 若要建立存放容器的名稱。
+ **-p**或**-首碼**&lt;前置詞 >︰ 存放容器名稱前置字元。
+ **-**或**-帳戶名稱** &lt;accountName >︰ 儲存體帳戶名稱
+ **-k**或**-帳戶金鑰** &lt;accountKey >︰ 儲存帳戶金鑰
+ **-c**或**-連線字串**&lt;連接字串 >︰ 儲存空間連線字串
+ **-偵錯**︰ 在 [偵錯模式中執行 [儲存] 命令。

**儲存容器刪除 [容器] [選項]**

這個命令刪除指定的儲存格容器。 儲存帳戶被指定的連接字串或儲存體帳戶名稱和帳戶金鑰。

這個命令支援下列選項︰

+ **-容器**&lt;容器 >︰ 若要建立存放容器的名稱。
+ **-p**或**-首碼**&lt;前置詞 >︰ 存放容器名稱前置字元。
+ **-**或**-帳戶名稱** &lt;accountName >︰ 儲存體帳戶名稱。
+ **-k**或**-帳戶金鑰** &lt;accountKey >: 儲存帳戶金鑰]。
+ **-c**或**-連線字串**&lt;連接字串 >︰ 儲存空間連線字串。
+ **-偵錯**︰ 在 [偵錯模式中執行 [儲存] 命令。

**儲存容器設定 [容器] [選項]**

這個命令設定存放容器存取控制清單。 儲存帳戶被指定的連接字串或儲存體帳戶名稱和帳戶金鑰。

這個命令支援下列選項︰

+ **-容器**&lt;容器 >︰ 若要建立存放容器的名稱。
+ **-p**或**-首碼**&lt;前置詞 >︰ 存放容器名稱前置字元。
+ **-**或**-帳戶名稱** &lt;accountName >︰ 儲存體帳戶名稱。
+ **-k**或**-帳戶金鑰** &lt;accountKey >: 儲存帳戶金鑰]。
+ **-c**或**-連線字串**&lt;連接字串 >︰ 儲存空間連線字串。
+ **-偵錯**︰ 在 [偵錯模式中執行 [儲存] 命令。

###<a name="commands-to-manage-your-storage-blob"></a>管理您的儲存空間 blob 的命令

**儲存 blob 清單 [容器] [首碼] [選項]**

這個命令會傳回指定的儲存容器中的儲存空間 blob 的清單。

這個命令支援下列選項︰

+ **-容器**&lt;容器 >︰ 若要建立存放容器的名稱。
+ **-p**或**-首碼**&lt;前置詞 >︰ 存放容器名稱前置字元。
+ **-**或**-帳戶名稱** &lt;accountName >︰ 儲存體帳戶名稱。
+ **-k**或**-帳戶金鑰** &lt;accountKey >: 儲存帳戶金鑰]。
+ **-c**或**-連線字串**&lt;連接字串 >︰ 儲存空間連線字串。
+ **-偵錯**︰ 在 [偵錯模式中執行 [儲存] 命令。

**儲存 blob 顯示 [選項] [容器] [blob]**

這個命令顯示指定的儲存 blob 的詳細資料。

這個命令支援下列選項︰

+ **-容器**&lt;容器 >︰ 若要建立存放容器的名稱。
+ **-p**或**-首碼**&lt;前置詞 >︰ 存放容器名稱前置字元。
+ **-**或**-帳戶名稱** &lt;accountName >︰ 儲存體帳戶名稱。
+ **-k**或**-帳戶金鑰** &lt;accountKey >: 儲存帳戶金鑰]。
+ **-c**或**-連線字串**&lt;連接字串 >︰ 儲存空間連線字串。
+ **-偵錯**︰ 在 [偵錯中執行 [儲存] 命令。

**blob 儲存體刪除 [容器] [blob] [選項]**

這個命令支援下列選項︰

+ **-容器**&lt;容器 >︰ 若要建立存放容器的名稱。
+ **-b**或**-blob** &lt;blobName >︰ 若要刪除的儲存空間 blob 的名稱。
+ **-q**或**-安靜**︰ 移除未確認指定的儲存空間 blob。
+ **-**或**-帳戶名稱** &lt;accountName >︰ 儲存體帳戶名稱。
+ **-k**或**-帳戶金鑰** &lt;accountKey >: 儲存帳戶金鑰]。
+ **-c**或**-連線字串**&lt;連接字串 >︰ 儲存空間連線字串。
+ **-偵錯**︰ 在 [偵錯中執行 [儲存] 命令。

**儲存 blob 上傳 [檔案] [容器] [blob] [選項]**

這個命令指定的檔案上傳至 specified\ 儲存 blob。

這個命令支援下列選項︰

+ **-容器**&lt;容器 >︰ 若要建立存放容器的名稱。
+ **-b**或**-blob** &lt;blobName >︰ 上傳的儲存空間 blob 的名稱。
+ **-t**或**-blobtype** &lt;blobtype >︰ 儲存空間 blob 類型︰ 頁面或區塊。
+ **-p**或**-屬性**&lt;屬性 >︰ 上傳檔案的儲存空間 blob 摘要資訊。 屬性是重要 = 值組 s，並以分號分隔。 可用的屬性是 contentType、 contentEncoding、 contentLanguage 及 cacheControl。
+ **-m**或**-中繼資料**&lt;中繼資料 >︰ 上傳的檔案儲存空間 blob 中繼資料。 中繼資料是重要 = d 並以分號 （;） 分隔的值組。
+ **-concurrenttaskcount**&lt;concurrenttaskcount >︰ 同時上傳要求的最大數目。
+ **-q**或**-安靜**︰ 覆寫未確認指定的儲存空間 blob。
+ **-**或**-帳戶名稱** &lt;accountName >︰ 儲存體帳戶名稱。
+ **-k**或**-帳戶金鑰** &lt;accountKey >: 儲存帳戶金鑰]。
+ **-c**或**-連線字串**&lt;連接字串 >︰ 儲存空間連線字串。
+ **-偵錯**︰ 在 [偵錯中執行 [儲存] 命令。

**儲存 blob 下載 [容器] [blob] [目的地] [選項]**

這個命令下載指定的儲存 blob。

這個命令支援下列選項︰

+ **-容器**&lt;容器 >︰ 若要建立存放容器的名稱。
+ **-b**或**-blob** &lt;blobName >︰ 儲存 blob 名稱。
+ **-d**或**-目的地**[目的地]: 下載目的地檔案或目錄路徑。
+ **-m**或**-checkmd5**: 核取 md5sum 下載的檔案。
+ **-concurrenttaskcount**&lt;concurrenttaskcount > 同時上傳要求的最大數目
+ **-q**或**-安靜**︰ 覆寫未確認目的地檔案。
+ **-**或**-帳戶名稱** &lt;accountName >︰ 儲存體帳戶名稱。
+ **-k**或**-帳戶金鑰** &lt;accountKey >: 儲存帳戶金鑰]。
+ **-c**或**-連線字串**&lt;連接字串 >︰ 儲存空間連線字串。
+ **-偵錯**︰ 在 [偵錯中執行 [儲存] 命令。

## <a name="commands-to-manage-sql-databases"></a>若要管理 SQL 資料庫] 命令

使用下列命令，管理 Azure SQL 資料庫

###<a name="commands-to-manage-sql-servers"></a>若要管理 SQL Server 的命令。

使用下列命令，管理您的 SQL Server

**sql server 建立&lt;administratorLogin > &lt;administratorPassword >&lt;位置 >**

建立資料庫伺服器

    ~$ azure sql server create test T3stte$t "West US"
    info:    Executing command sql server create
    + Creating SQL Server
    data:    Server Name i1qwc540ts
    info:    sql server create command OK

**sql server 顯示&lt;名稱 >**

顯示伺服器詳細資料。

    ~$ azure sql server show xclfgcndfg
    info:    Executing command sql server show
    + Getting SQL server
    data:    SQL Server Name xclfgcndfg
    data:    SQL Server AdministratorLogin msopentechforums
    data:    SQL Server Location West US
    data:    SQL Server FullyQualifiedDomainName xclfgcndfg.database.windows.net
    info:    sql server show command OK

**sql server 清單**

取得伺服器的清單。

    ~$ azure sql server list
    info:    Executing command sql server list
    + Getting SQL server
    data:    Name        Location
    data:    ----------  --------
    data:    xclfgcndfg  West US
    info:    sql server list command OK

**sql server 刪除&lt;名稱 >**

刪除伺服器

    ~$ azure sql server delete i1qwc540ts
    info:    Executing command sql server delete
    Delete server i1qwc540ts? [y/n] y
    + Removing SQL Server
    info:    sql server delete command OK

###<a name="commands-to-manage-sql-databases"></a>若要管理 SQL 資料庫] 命令

您可以使用這些命令來管理 SQL 資料庫。

**sql 資料庫建立 [選項]&lt;伺服器名稱 >&lt;資料庫名稱 > &lt;administratorPassword >**

建立資料庫執行個體

    ~$ azure sql db create fr8aelne00 newdb test
    info:    Executing command sql db create
    Administrator password: ********
    + Creating SQL Server Database
    info:    sql db create command OK

**sql db 顯示 [選項]&lt;伺服器名稱 >&lt;資料庫名稱 > &lt;administratorPassword >**

顯示資料庫的詳細資訊。

    C:\windows\system32>azure sql db show fr8aelne00 newdb test
    info:    Executing command sql db show
    Administrator password: ********
    + Getting SQL server databases
    data:    Database _ ContentRootElement=m:properties, id=https://fr8aelne00.datab
    ase.windows.net/v1/ManagementService.svc/Server2('fr8aelne00')/Databases(4), ter
    m=Microsoft.SqlServer.Management.Server.Domain.Database, scheme=http://schemas.m
    icrosoft.com/ado/2007/08/dataservices/scheme, link=[rel=edit, title=Database, hr
    ef=Databases(4), rel=http://schemas.microsoft.com/ado/2007/08/dataservices/relat
    ed/Server, type=application/atom+xml;type=entry, title=Server, href=Databases(4)
    /Server, rel=http://schemas.microsoft.com/ado/2007/08/dataservices/related/Servi
    ceObjective, type=application/atom+xml;type=entry, title=ServiceObjective, href=
    Databases(4)/ServiceObjective, rel=http://schemas.microsoft.com/ado/2007/08/data
    services/related/DatabaseMetrics, type=application/atom+xml;type=entry, title=Da
    tabaseMetrics, href=Databases(4)/DatabaseMetrics, rel=http://schemas.microsoft.c
    om/ado/2007/08/dataservices/related/DatabaseCopies, type=application/atom+xml;ty
    pe=feed, title=DatabaseCopies, href=Databases(4)/DatabaseCopies], title=, update
    d=2013-11-18T19:48:27Z, name=
    data:    Database Id 4
    data:    Database Name newdb
    data:    Database ServiceObjectiveId 910b4fcb-8a29-4c3e-958f-f7ba794388b2
    data:    Database AssignedServiceObjectiveId 910b4fcb-8a29-4c3e-958f-f7ba794388b2
    data:    Database ServiceObjectiveAssignmentState 1
    data:    Database ServiceObjectiveAssignmentStateDescription Complete
    data:    Database ServiceObjectiveAssignmentErrorCode
    data:    Database ServiceObjectiveAssignmentErrorDescription
    data:    Database ServiceObjectiveAssignmentSuccessDate
    data:    Database Edition Web
    data:    Database MaxSizeGB 1
    data:    Database MaxSizeBytes 1073741824
    data:    Database CollationName SQL_Latin1_General_CP1_CI_AS
    data:    Database CreationDate
    data:    Database RecoveryPeriodStartDate
    data:    Database IsSystemObject
    data:    Database Status 1
    data:    Database IsFederationRoot
    data:    Database SizeMB -1
    data:    Database IsRecursiveTriggersOn
    data:    Database IsReadOnly
    data:    Database IsFederationMember
    data:    Database IsQueryStoreOn
    data:    Database IsQueryStoreReadOnly
    data:    Database QueryStoreMaxSizeMB
    data:    Database QueryStoreFlushPeriodSeconds
    data:    Database QueryStoreIntervalLengthMinutes
    data:    Database QueryStoreClearAll
    data:    Database QueryStoreStaleQueryThresholdDays
    info:    sql db show command OK

**sql 資料庫清單中 [的選項]&lt;伺服器名稱 > &lt;administratorPassword >**

列出的資料庫。

    ~$ azure sql db list fr8aelne00 test
    info:    Executing command sql db list
    Administrator password: ********
    + Getting SQL server databases
    data:    Name    Edition  Collation                     MaxSizeInGB
    data:    ------  -------  ----------------------------  -----------
    data:    master  Web      SQL_Latin1_General_CP1_CI_AS  5
    info:    sql db list command OK

**sql 資料庫刪除 [選項]&lt;伺服器名稱 >&lt;資料庫名稱 > &lt;administratorPassword >**

刪除資料庫。

    ~$ azure sql db delete fr8aelne00 newdb test
    info:    Executing command sql db delete
    Administrator password: ********
    Delete database newdb? [y/n] y
    + Getting SQL server databases
    + Removing database
    info:    sql db delete command OK

###<a name="commands-to-manage-your-sql-server-firewall-rules"></a>管理您的 SQL Server 防火牆規則的命令

使用下列命令，管理您的 SQL Server 防火牆規則

**sql firewallrule 建立 [選項]&lt;伺服器名稱 > &lt;ruleName > &lt;startIPAddress > &lt;endIPAddress >**

建立 SQL Server 防火牆規則。

    ~$ azure sql firewallrule create fr8aelne00 allowed 131.107.0.0 131.107.255.255
    info:    Executing command sql firewallrule create
    + Creating Firewall Rule
    info:    sql firewallrule create command OK

**sql firewallrule 顯示 [選項]&lt;伺服器名稱 > &lt;ruleName >**

顯示防火牆規則的詳細資訊。

    ~$ azure sql firewallrule show fr8aelne00 allowed
    info:    Executing command sql firewallrule show
    + Getting firewall rule
    data:    Firewall rule Name allowed
    data:    Firewall rule Type Microsoft.SqlAzure.FirewallRule
    data:    Firewall rule State Normal
    data:    Firewall rule SelfLink https://management.core.windows.net/9e672699-105
    5-41ae-9c36-e85152f2e352/services/sqlservers/servers/fr8aelne00/firewallrules/allowed
    data:    Firewall rule ParentLink https://management.core.windows.net/9e672699-1
    055-41ae-9c36-e85152f2e352/services/sqlservers/servers/fr8aelne00
    data:    Firewall rule StartIPAddress 131.107.0.0
    data:    Firewall rule EndIPAddress 131.107.255.255
    info:    sql firewallrule show command OK

**sql firewallrule 清單 [選項]&lt;伺服器名稱 >**

列出防火牆規則。

    ~$ azure sql firewallrule list fr8aelne00
    info:    Executing command sql firewallrule list
    \data:    Name     Start IP address  End IP address
    data:    -------  ----------------  ---------------
    data:    allowed  131.107.0.0       131.107.255.255
    +
    info:    sql firewallrule list command OK

**sql firewallrule 刪除 [選項]&lt;伺服器名稱 > &lt;ruleName >**

這個命令刪除防火牆規則。

    ~$ azure sql firewallrule delete fr8aelne00 allowed
    info:    Executing command sql firewallrule delete
    Delete rule allowed? [y/n] y
    + Removing firewall rule
    info:    sql firewallrule delete command OK

## <a name="commands-to-manage-your-virtual-networks"></a>管理您的虛擬網路的命令

使用下列命令，管理您的虛擬網路

**網路 vnet 建立 [選項]&lt;位置 >**

建立虛擬網路。

    ~$ azure network vnet create vnet1 --location "West US" -v
    info:    Executing command network vnet create
    info:    Using default address space start IP: 10.0.0.0
    info:    Using default address space cidr: 8
    info:    Using default subnet start IP: 10.0.0.0
    info:    Using default subnet cidr: 11
    verbose: Address Space [Starting IP/CIDR (Max VM Count)]: 10.0.0.0/8 (16777216)
    verbose: Subnet [Starting IP/CIDR (Max VM Count)]: 10.0.0.0/11 (2097152)
    verbose: Fetching Network Configuration
    verbose: Fetching or creating affinity group
    verbose: Fetching Affinity Groups
    verbose: Fetching Locations
    verbose: Creating new affinity group AG1
    info:    Using affinity group AG1
    verbose: Updating Network Configuration
    info:    network vnet create command OK

**網路 vnet 顯示&lt;名稱 >**

顯示虛擬網路的詳細的資訊。

    ~$ azure network vnet show vnet1
    info:    Executing command network vnet show
    + Fetching Virtual Networks
    data:    Name "vnet1"
    data:    Id "25786fbe-08e8-4e7e-b1de-b98b7e586c7a"
    data:    AffinityGroup "AG1"
    data:    State "Created"
    data:    AddressSpace AddressPrefixes 0 "10.0.0.0/8"
    data:    Subnets 0 Name "subnet-1"
    data:    Subnets 0 AddressPrefix "10.0.0.0/11"
    info:    network vnet show command OK

**網路 vnet 清單**

列出所有現有的虛擬網路。

    ~$ azure network vnet list
    info:    Executing command network vnet list
    + Fetching Virtual Networks
    data:    Name        Status   AffinityGroup
    data:    ----------  -------  -------------
    data:    vnet1      Created  AG1
    data:    vnet2      Created  AG1
    data:    vnet3      Created  AG1
    data:    vnet4      Created  AG1
    info:    network vnet list command OK


**網路 vnet 刪除&lt;名稱 >**

刪除指定虛擬網路。

    ~$ azure network vnet delete opentechvn1
    info:    Executing command network vnet delete
    + Fetching Network Configuration
    Delete the virtual network opentechvn1 ?  (y/n) y
    + Deleting the virtual network opentechvn1
    info:    network vnet delete command OK

**網路匯出 [檔案路徑]**

進階的網路設定，您可以匯出您的網路設定本機。 匯出的網路設定包含 DNS 伺服器設定、 虛擬網路設定、 區域網路網站設定] 及其他設定。

**網路匯入 [檔案路徑]**

匯入的區域網路設定。

**網路 dnsserver register] [選項] &lt;dnsIP >**

註冊您打算使用您的網路設定中的名稱解析 DNS 伺服器。

    ~$ azure network dnsserver register 98.138.253.109 --dns-id FrontEndDnsServer
    info:    Executing command network dnsserver register
    + Fetching Network Configuration
    + Updating Network Configuration
    info:    network dnsserver register command OK

**網路 dnsserver 清單**

列出註冊您的網路設定中的所有 DNS 伺服器。

    ~$ azure network dnsserver list
    info:    Executing command network dnsserver list
    + Fetching Network Configuration
    data:    DNS Server ID         DNS Server IP
    data:    --------------------  --------------
    data:    DNS-bb39b4ac34d66a86  44.55.22.11
    data:    FrontEndDnsServer     98.138.253.109
    info:    network dnsserver list command OK

**網路 dnsserver 解除登錄 [選項] &lt;dnsIP >**

移除網路設定的 DNS 伺服器項目。

    ~$ azure network dnsserver unregister 77.88.99.11
    info:    Executing command network dnsserver unregister
    + Fetching Network Configuration
    Delete the DNS server entry dns-4 ( 77.88.99.11 ) %s ? (y/n) y
    + Deleting the DNS server entry dns-4 ( 77.88.99.11 )
    info:    network dnsserver unregister command OK

