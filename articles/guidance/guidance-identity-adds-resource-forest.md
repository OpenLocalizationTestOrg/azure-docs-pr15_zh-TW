<properties
   pageTitle="Azure 的架構參照-IaaS︰ 建立 Azure Active Directory 資源樹系 |Microsoft Azure"
   description="如何建立受信任的 Active Directory 網域 Azure 中。"
   services="guidance,vpn-gateway,expressroute,load-balancer,virtual-network,active-directory"
   documentationCenter="na"
   authors="telmosampaio"
   manager="christb"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="telmos"/>

# <a name="creating-a-active-directory-directory-services-adds-resource-forest-in-azure"></a>建立 Azure Active Directory 目錄服務 （新增） 資源樹系

[AZURE.INCLUDE [pnp-RA-branding](../../includes/guidance-pnp-header-include.md)]

本文將說明如何建立分開，但受的 Azure Active Directory 網域內部部署樹系中的網域。

> [AZURE.NOTE] Azure 有兩種不同的部署模型︰[資源管理員][resource-manager-overview]和傳統。 此參考架構會使用資源管理員，Microsoft 建議新的部署。

執行 Active Directory (AD) 內部部署的組織可能樹系包含許多不同的網域。 例如，您可能會建立個別的網域的不同部門或 suborganizations，或新網域可能已新增當做擷取或其他公司的合併。 您可以使用網域隔離功能區域的必須分開保存，可能是基於安全性理由，但您可以之間共用資訊建立信任關係的網域。

組織使用不同的網域，可以利用 Azure 來重新定位至雲端的一或多個網域。 或者，組織可能會想要保留所有雲端資源邏輯這些保留內部部署，並儲存在自己的網域，也會保留在雲端的目錄中的雲端資源的相關資訊。

您可以實作 Active Directory Azure 中以不同的方式，文章[延伸至 Azure 的 Active Directory]中所述[extending-ad-to-azure]與[實作 Azure Active Directory][implementing-aad]。 這份文件著重於其中一個特定案例︰ 建立網域在雲端的不同於會保留在內部部署，任何網域，但可以有內部部署的網域信任關係。 

此結構的一般的使用案例包括︰

- 維護物件和保留在雲端中的身分識別的安全性區隔。

- 將來自內部部署的個別的網域移轉至雲端。

## <a name="architecture-diagram"></a>架構圖表

下圖會醒目提示重要的元件，在這種架構 （[*按一下以拉近顯示*）。 更多關於灰色元素的詳細資訊，請參閱[實作 Azure 中的安全的混合式網路架構][implementing-a-secure-hybrid-network-architecture]和[實作安全混合的網路架構與 Azure 中的網際網路存取][implementing-a-secure-hybrid-network-architecture-with-internet-access]:

[![0]][0]

- **內部部署網路。** 內部部署網路包含自己 AD 樹系和網域。

- **AD 伺服器。** 這些是網域控制站實作執行 Vm 雲端中的目錄服務 (AD DS)。 這些伺服器主機樹系包含一或多個網域，獨立於這些位於內部部署。

- **單向信任關係。** 在圖表中的範例會顯示該網域的單向信任至內部部署網域雲端中。 此關聯可讓內部部署使用者存取雲端，但不其他熟悉網域中的資源。 這是常見的情況。 不過，您可以建立雙向信任，如果雲端使用者也需要存取內部部署資源中。

- **Active Directory 的子網路。** AD DS 伺服器都裝載於不同的子網路。 NSG 規則保護 AD DS 伺服器，並提供對非預期的來源的流量防火牆。

- **網頁層子網路**、**商務層子網路**和**資料層子網路**。 這些子網路主控伺服器和雲端中執行的應用程式的元件。 如需詳細資訊，請參閱[執行 Vm N 層架構在 Azure][running-VMs-for-an-N-tier-architecture-on-Azure]。 資源和 Vm 此子網路中的會包含在雲端網域。

- **Azure 閘道器**。 Azure 閘道器提供內部部署網路與 Azure VNet 之間的連線。 這可能是[VPN 連線][azure-vpn-gateway]或[Azure ExpressRoute][azure-expressroute]。 如需詳細資訊，請參閱[實作 Azure 中的安全的混合式網路架構][implementing-a-secure-hybrid-network-architecture]。

## <a name="recommendations"></a>建議

本節提供建議根據實作基本架構所需的基本元件的清單。 這些建議涵蓋︰

- 新增設定]，然後

- 信任關係設定。

您可能需要額外的或不同的需求，從這裡所述。 您可以使用項目此區段中，開始的考慮如何自訂您自己的系統架構。

### <a name="adds-recommendations"></a>新增建議

實作 Active Directory 雲端中的特定建議，請參閱文件[至 Azure 延伸 Active Directory][extending-ad-to-azure]。 文章[的指導方針部署 Windows Server Active Directory 上 Azure 虛擬機器][ad-azure-guidelines]包含其他的詳細的資訊。

### <a name="trust-recommendations"></a>信任的建議

內部部署網域都包含在不同的樹系從雲端中的網域。 若要啟用的雲端中的內部部署使用者驗證，在雲端的網域必須信任在內部部署樹系登入網域。 同樣地，如果雲端提供的外部使用者的登入網域，可能需要內部部署樹系信任雲端網域。

您可以藉由[建立樹系信任]建立樹系層級的信任[creating-forest-trusts]，或藉由[建立外部信任]的網域層級[creating-external-trusts]。 樹系層級信任所建立的所有網域中兩個樹系之間的關聯。 外部網域層級信任只會建立兩個指定的網域之間的關聯。 您應該只建立外部網域層級信任網域中不同的樹系之間。

信任可以是單向 （單向） 或雙向 （雙方）︰

- 單向信任可讓使用者在一個網域或樹系 （亦稱為*內送*的網域或樹系） 中存取另一個使用的資源 （*外寄*的網域或樹系）。 

- 雙向信任可讓使用者在網域或樹系存取中其他的資源。

下表摘要列出一些簡單的案例的信任設定︰

| 案例 | 內部部署信任 | 雲端信任 |
|----------|-------------------|-------------|
| 內部部署使用者要求存取資源在雲端，但無法從 | 單向、 連入 | 單向、 連出 |
| 雲端中的使用者要求存取資源位於內部部署，但無法從 | 單向、 連出 | 單向、 連入 |
| 在雲端和內部部署使用者需要存取內部部署與雲端中的資源 | 雙向、 傳入和傳出 | 雙向、 傳入和傳出 |

## <a name="security-considerations"></a>安全性考量

樹系層級信任是錯誤。 如果您樹系層級和之間建立信任的內部部署樹系樹系在雲端，則會將這個信任延伸至其他任一個樹系所建立的新網域。 如果您使用的網域提供分隔基於安全性的考量，請考慮建立信任網域層級只。 網域層級信任是非轉移。

AD 特定的安全性考量，請參閱[延伸至 Azure 的 Active Directory]中的*安全性考量*區段[extending-ad-to-azure]。

## <a name="availability-considerations"></a>可用性考量

實作兩個以上的網域控制站，針對每個網域。 這可讓伺服器間的自動複製。 建立設定 Vm 做為 AD 伺服器控制每個網域的可用性。 請確定在設定有兩個以上的伺服器。 

此外，請考慮指定為[備用操作母片]的每個網域中的一或多個伺服器[standby-operations-masters]以防做為 FSMO 角色的伺服器的連線失敗。

## <a name="scalability-considerations"></a>延展性考量

AD 是網域的屬於相同的網域控制站的自動調整。 要求分佈網域中的所有控制站。 您可以新增另一個網域控制站，並將其同步處理會自動與網域。 未設定不同的負載平衡器，將網域中的 [控制站流量導向。 確定所有網域控制站都有足夠的記憶體和儲存資源來處理網域資料庫。 將所有的網域控制站 Vm 設為相同的大小。

## <a name="management-and-monitoring-considerations"></a>管理及監視考量

管理及監視考量的相關資訊，請參閱[延伸至 Azure 的 Active Directory]中的對等章節[extending-ad-to-azure]。 

如需詳細資訊，請參閱[監控 Active Directory][monitoring_ad]。 您可以安裝工具，例如[Microsoft 系統管理中心][microsoft_systems_center]在管理子網路，以協助執行這些工作的監控伺服器上。 

## <a name="solution-components"></a>解決方案元件

範例解決方案指令碼，[部署 ReferenceArchitecture.ps1][solution-script]，提供您可以使用實作架構的本文所述的建議。 這個指令碼利用 Azure 資源管理員範本。 為每個執行特定動作，例如建立 VNet 或設定 NSG 基本的建置組塊，一組可範本。 指令碼的目的，是協調範本部署。

範本會參數化，以不同的 JSON 檔案中的參數。 您可以修改設定以符合您自己的需求部署這些檔案中的參數。 您不需要修改範本本身。 您不能變更參數檔案中的物件的結構描述。

當您編輯的樣板時，建立物件追蹤[建議的命名慣例 Azure 資源]所述的命名慣例[naming-conventions]。

建立範例方案，並在雲端實作名為*treyresearch.com*網域設定環境。 這種環境中包括新增子網路與伺服器、 DMZ、 web 層、 商務層和資料的存取層元件，VPN 閘道和管理層。 範例方案也包含使用自己的網域， *contoso.com*建立模擬內部部署環境的選擇性的設定。 方案包含在可讓內部部署使用者存取*treyresearch.com*網域在雲端中的物件這些網域建立信任關係的指令碼。

下列各節說明的元素的內部部署和雲端設定。

### <a name="on-premises-components"></a>內部部署元件

>[AZURE.NOTE] 這些元件無法在此文件中所描述的架構的主要工作，以及可供您只是讓您得以安全地，而不是使用實數生產環境中測試雲端環境。 因此，此區段只摘要索引鍵的參數檔案。 您可以修改設定，例如 IP 位址或 Vm 的大小，但建議您離開的許多其他參數保持不變。

這種環境中包括 AD 樹系*contoso.com*網域。 網域中包含兩個 192.168.0.4 和 192.168.0.5 的 IP 位址新增伺服器。 下列兩個伺服器也會執行 DNS 服務。 本機系統管理員帳戶在兩個 Vm 稱為`testuser`密碼`AweS0me@PW`。 此外，設定為需連線至雲端 VNet VPN 閘道器。 您可以修改設定，編輯下列 JSON 檔案位於[**參數/onpremise**] [on-premises-folder]資料夾︰

- ** [virtualNetwork.parameters.json][on-premises-vnet-parameters]**。 這個檔案定義內部部署環境的網路位址空間。

- ** [virtualMachines adds.parameters.json][on-premises-virtualmachines-adds-parameters]**。 這個檔案包含內部部署 Vm 主機新增服務的設定。 根據預設，會建立兩個*標準-DS3-v2* Vm。

- ** [virtualNetworkGateway.parameters.json] [on-premises-virtualnetworkgateway-parameters]**和**[connection.parameters.json][on-premises-connection-parameters]**。 這些檔案會保留在雲端，包括共用要用來保護流量通過閘道器金鑰 Azure VPN 閘道器的 VPN 連線的設定。

在資料夾中的剩餘檔案包含用來建立內部部署網域 (*contoso.com*)，使用此基礎結構的設定資訊。 您用來安裝相加，設定 DNS，並建立內部部署樹系。

方案也會使用下列的指令碼，名為[內送 trust.ps1][incoming-trust]，其在網域] 內部部署電腦上執行︰

```Powershell
# Run the following powershell script in ra-adtrust-onpremise-ad-vm1 (ip 192.168.0.4)

$TrustedDomainName = "contoso.com"
#$TrustedDomainDnsIpAddresses = "192.168.0.4,192.168.0.5"

$TrustingDomainName = "treyresearch.com"
$TrustingDomainDnsIpAddresses = "10.0.4.4,10.0.4.5"

$ForwardIpAddress  = $TrustingDomainDnsIpAddresses
$ForwardDomainName = $TrustingDomainName

$IpAddresses = @()
foreach($address in $ForwardIpAddress.Split(',')){
    $IpAddresses += [IPAddress]$address.Trim()
}
Add-DnsServerConditionalForwarderZone -Name "$ForwardDomainName" -ReplicationScope "Domain" -MasterServers $IpAddresses

netdom trust $TrustingDomainName /d:$TrustedDomainName /add
```

這個指令碼在雲端新增 AD DS 伺服器的 IP 位址 （請參閱下一節） 本機 DNS 服務中，然後使用 [ [netdom] [ netdom] ] 命令，從雲端 (*treyresearch.com*) 中的網域建立內送的單向信任。

### <a name="cloud-components"></a>雲端元件

下列元件表單此架構的核心。 這些設定*treyresearch.com*網域的基礎結構，並使用內部部署*contoso.com*網域建立的信任關係。 [**參數/azure**] [azure-folder]資料夾包含下列參數檔案設定下列元件︰

- ** [virtualNetwork.parameters.json][vnet-parameters]**。 這個檔案定義 VNet 的結構 Vm 和雲端中的其他元件。 其包含設定，例如名稱、 地址空間、 子網路及任何所需的 DNS 伺服器的位址。 DNS 地址會顯示在此範例中參照的內部部署的 DNS 伺服器，以及預設 Azure DNS 伺服器 IP 位址。 修改參考您自己的 DNS 設定，如果您不使用範例內部部署環境這些地址︰
    
    ```json
    "virtualNetworkSettings": {
      "value": {
        "name": "ra-adtrust-vnet",
        "resourceGroup": "ra-adtrust-network-rg",
        "addressPrefixes": [
          "10.0.0.0/16"
        ],
        "subnets": [
          {
            "name": "dmz-private-in",
            "addressPrefix": "10.0.0.0/27"
          },
          {
            "name": "dmz-private-out",
            "addressPrefix": "10.0.0.32/27"
          },
          {
            "name": "dmz-public-in",
            "addressPrefix": "10.0.0.64/27"
          },
          {
            "name": "dmz-public-out",
            "addressPrefix": "10.0.0.96/27"
          },
          {
            "name": "mgmt",
            "addressPrefix": "10.0.0.128/25"
          },
          {
            "name": "GatewaySubnet",
            "addressPrefix": "10.0.255.224/27"
          },
          {
            "name": "web",
            "addressPrefix": "10.0.1.0/24"
          },
          {
            "name": "biz",
            "addressPrefix": "10.0.2.0/24"
          },
          {
            "name": "data",
            "addressPrefix": "10.0.3.0/24"
          },
          {
            "name": "adds",
            "addressPrefix": "10.0.4.0/27"
          }
        ],
        "dnsServers": [
          "10.0.4.4",
          "10.0.4.5",
          "168.63.129.16"
        ]
      }
    }
    ```

- ** [virtualMachines adds.parameters.json ] [ virtualmachines-adds-parameters] **。 此檔案設定 Vm 雲端中執行相加。 設定包含兩個 Vm。 變更管理員的使用者名稱和密碼在`virtualMachineSettings`] 區段中，而且您也可以修改虛擬記憶體大小以符合的網域需求︰

    如需詳細資訊，請參閱[延伸至 Azure 的 Active Directory][extending-ad-to-azure]。
    
    ```json
    "virtualMachinesSettings": {
      "value": {
        "namePrefix": "ra-adtrust-ad",
        "computerNamePrefix": "aad",
        "size": "Standard_DS3_v2",
        "osType": "Windows",
        "adminUsername": "testuser",
        "adminPassword": "AweS0me@PW",
        "osAuthenticationType": "password",
        "nics": [
          {
            "isPublic": "false",
            "subnetName": "adds",
            "privateIPAllocationMethod": "Static",
            "startingIPAddress": "10.0.4.4",
            "enableIPForwarding": false,
            "dnsServers": [
            ],
            "isPrimary": "true"
          }
        ],
        "imageReference": {
          "publisher": "MicrosoftWindowsServer",
          "offer": "WindowsServer",
          "sku": "2012-R2-Datacenter",
          "version": "latest"
        },
        "dataDisks": {
          "count": 1,
          "properties": {
            "diskSizeGB": 127,
            "caching": "None",
            "createOption": "Empty"
          }
        },
        "osDisk": {
          "caching": "ReadWrite"
        },
        "extensions": [
        ],
        "availabilitySet": {
          "useExistingAvailabilitySet": "No",
          "name": "ra-adtrust-as"
        }
      }
    },
    "virtualNetworkSettings": {
      "value": {
        "name": "ra-adtrust-vnet",
        "resourceGroup": "ra-adtrust-network-rg"
      }
    },
    "buildingBlockSettings": {
      "value": {
        "storageAccountsCount": 2,
        "vmCount": 2,
        "vmStartIndex": 1
      }
    }
    ```

- **[新增-新增的網域-controller.parameters.json][add-adds-domain-controller-parameters]**。 此檔案會包含建立橫跨相加伺服器*treyresearch.com*網域的設定。 使用自訂延伸，建立網域，並為其新增新增伺服器。 除非您建立其他相加伺服器 (在此情況下，您應該新增他們`vms`陣列)、 預設值，變更其名稱，或想要使用不同的名稱，您不需要修改此檔案建立網域。

- ** [virtualNetworkGateway.parameters.json][virtualnetworkgateway-parameters]**。 這個檔案包含用來建立 Azure VPN 閘道器用來連線到內部部署網路雲端中的設定。 您應該修改`sharedKey`中的值`connectionsSettings`以符合的內部部署 VPN 裝置的區段。 如需詳細資訊，請參閱[實作混合的網路架構與 Azure 及內部部署 VPN][hybrid-azure-on-prem-vpn]。

- ** [dmz private.parameters.json] [dmz-private-parameters]**和**[dmz public.parameters.json ] [ dmz-public-parameters] **。 這些檔案設定輸入 （公開） 輸出 （私人） 兩側 Vm 包含 DMZ 保護雲端中的伺服器。 如需有關這些項目] 和 [其設定的詳細資訊，請參閱[實作 Azure 與網際網路之間 DMZ][implementing-a-secure-hybrid-network-architecture-with-internet-access]。

- ** [loadBalancer web.parameters.json][loadBalancer-web-parameters]**， ** [loadBalancer biz.parameters.json][loadBalancer-biz-parameters]**，及**[loadBalancer data.parameters.json][loadBalancer-data-parameters]**。 這些參數檔案包含網頁、 商業及資料存取層級的 VM 規格，並設定的每一層的負載平衡器。 這些是 Vm 主控 web 應用程式，並資料庫，並執行商務工作負載的組織。 在網頁層 Vm 加入到雲端中的網域的使用中的設定**[web vm-網域 join.parameters.json] [web-vm-domain-join-parameters]**檔案。

    每個檔案包含兩組設定參數。 `virtualMachineSettings`區段定義 Vm 裝載在雲端 ADFS 服務。 根據預設，指令碼會建立兩個 Vm 在相同的可用性設定。 下列片段顯示*loadBalancer web.parameters.json*檔案的相關的組件︰

    ```json
    "virtualMachinesSettings": {
      "value": {
        "namePrefix": "ra-adtrust-web",
        "computerNamePrefix": "web",
        "size": "Standard_DS1_v2",
        "osType": "windows",
        "adminUsername": "testuser",
        "adminPassword": "AweS0me@PW",
        "osAuthenticationType": "password",
        "nics": [
          {
            "isPublic": "false",
            "subnetName": "web",
            "privateIPAllocationMethod": "Dynamic",
            "isPrimary": "true",
            "enableIPForwarding": false,
            "dnsServers": [ ]
          }
        ],
        "imageReference": {
          "publisher": "MicrosoftWindowsServer",
          "offer": "WindowsServer",
          "sku": "2012-R2-Datacenter",
          "version": "latest"
        },
        "dataDisks": {
          "count": 1,
          "properties": {
            "diskSizeGB": 128,
            "caching": "None",
            "createOption": "Empty"
          }
        },
        "osDisk": {
          "caching": "ReadWrite"
        },
        "extensions": [ ],
        "availabilitySet": {
          "useExistingAvailabilitySet": "No",
          "name": "ra-adtrust-web-vm-as"
        }
      }
    },
    ...
    "buildingBlockSettings": {
      "value": {
        "storageAccountsCount": 2,
        "vmCount": 2,
        "vmStartIndex": 1
      }
    }
    ````
    您可以根據您的需求修改大小和 Vm 的每一層的數字。

    `loadBalancerSettings`節提供這些負載平衡器的描述 Vm。 負載平衡器要傳送流量出現的連接埠 80 (HTTP) 和連接埠 443 (HTTPS) 到一個或另一 Vm。 

    >[AZURE.NOTE] TCP 連接，而不是 HTTP，則會使用連接埠 80 規則。 這是因為您在安裝 IIS 網站層已設定為僅支援 Windows 驗證。 已停用匿名驗證。 *偵測 （ping）*嘗試透過 HTTP 連線的連接埠 80 錯誤而失敗 401 （未經授權），而只使用 TCP 連線偵測到的連接埠是否是使用中︰

    ```json
    "loadBalancerSettings": {
      "value": {
        "name": "ra-adtrust-web-lb",
        "frontendIPConfigurations": [
          {
            "name": "ra-adtrust-web-lb-fe",
            "loadBalancerType": "internal",
            "internalLoadBalancerSettings": {
              "privateIPAddress": "10.0.1.254",
              "subnetName": "web"
            }
          }
        ],
        "backendPools": [
          {
            "name": "ra-adtrust-web-lb-bep",
            "nicIndex": 0
          }
        ],
        "loadBalancingRules": [
          {
            "name": "http-rule",
            "frontendPort": 80,
            "backendPort": 80,
            "protocol": "Tcp",
            "backendPoolName": "ra-adtrust-web-lb-bep",
            "frontendIPConfigurationName": "ra-adtrust-web-lb-fe",
            "probeName": "http-probe",
            "enableFloatingIP": false
          },
          {
            "name": "https-rule",
            "frontendPort": 443,
            "backendPort": 443,
            "protocol": "Tcp",
            "backendPoolName": "ra-adtrust-web-lb-bep",
            "frontendIPConfigurationName": "ra-adtrust-web-lb-fe",
            "probeName": "https-probe",
            "enableFloatingIP": false
          }
        ],
        "probes": [
          {
            "name": "http-probe",
            "port": 80,
            "protocol": "Tcp",
            "requestPath": null
          },
          {
            "name": "https-probe",
            "port": 443,
            "protocol": "Tcp",
            "requestPath": null
          }
        ],
        "inboundNatRules": [ ]
      }
    }
    ```

- ** [virtualMachines mgmt.parameters.json][virtualMachines-mgmt-parameters]**。 這個檔案包含跳轉] 方塊/管理 Vm 的設定。 您才可以取得登入與管理存取權網頁、 企業版和資料層 Vm 跳轉] 方塊。 根據預設，指令碼會建立單一*Standard_DS1_v2* VM，但您可以修改這個檔案來建立更大或其他 Vm 時可能會嚴重管理工作量。

設定也會使用[輸出 trust.ps1] [ outgoing-trust] *contoso.com*網域建立單向外寄信任下方顯示的指令碼︰

```powershell
# prerequiste: 
# You need to first run incoming-trust.ps1 in ra-adtrust-onpremise-ad-vm1 (ip 192.168.0.4)
# Then,
# Run the following powershell script in ra-adtrust-ad-vm1 (ip 10.0.4.4)

$TrustedDomainName = "contoso.com"
$TrustedDomainDnsIpAddresses = "192.168.0.4,192.168.0.5"

#$TrustingDomainName = "treyresearch.com"
#$TrustingDomainDnsIpAddresses = "10.0.4.4,10.0.4.5"

$ForwardIpAddress  = $TrustedDomainDnsIpAddresses
$ForwardDomainName = $TrustedDomainName

$IpAddresses = @()
foreach($address in $ForwardIpAddress.Split(',')){
    $IpAddresses += [IPAddress]$address.Trim()
}
Add-DnsServerConditionalForwarderZone -Name "$ForwardDomainName" -ReplicationScope "Domain" -MasterServers $IpAddresses

#netdom trust $TrustingDomainName /d:$TrustedDomainName /add
```

這個指令碼很類似稍早所述的*內送 trust.ps1*指令碼。 它會將內部部署的 IP 位址 AD DS 伺服器本機 DNS 服務，然後使用 [ [netdom] [netdom]命令，以建立外寄的信任關係。

## <a name="solution-deployment"></a>解決方案部署

解決方案假設下列先決條件︰

- 您有現有的 Azure 訂閱，您可以在其中建立資源群組。

- 您必須下載並安裝最新的 Powershell 的 Azure 建置。 請參閱[以下][azure-powershell-download]如需相關指示。

若要執行部署解決方案的指令碼︰

1. 在您的本機電腦上移動最方便的資料夾，並建立下列子資料夾︰

    - 指令碼

    - 指令碼/參數

    - 指令碼/參數/Onpremise

    - 指令碼/參數/Azure

2. 下載[部署 ReferenceArchitecture.ps1] [solution-script]的指令碼資料夾的檔案

3. 下載內容的[參數/onpremise] [on-premises-folder]指令碼/參數/Onpremise 資料夾的資料夾︰

4. 下載內容的[參數/azure] [azure-folder]指令碼/參數/Azure 資料夾的資料夾。

5. 部署 ReferenceArchitecture.ps1 中編輯檔案的指令碼資料夾，並變更下列行指定的資源群組，必須建立，或用來儲存建立的指令碼的資源︰

    ```powershell
    # Azure Onpremise Deployments
    $onpremiseNetworkResourceGroupName = "ra-adtrust-onpremise-rg"

    # Azure ADDS Deployments
    $azureNetworkResourceGroupName = "ra-adtrust-network-rg"
    $workloadResourceGroupName = "ra-adtrust-workload-rg"
    $securityResourceGroupName = "ra-adtrust-security-rg"
    $addsResourceGroupName = "ra-adtrust-adds-rg"
    ```

6. 參數中編輯檔案的指令碼/參數/Onpremise 和指令碼/參數/Azure 資料夾。 更新資源] 群組中的參考這些檔案，以符合部署 ReferenceArchitecture.ps1 檔案中的變數分派的資源群組的名稱。 下表顯示哪些參數檔案參照的資源群組。 *Ra adfs-工作量路由*、 *ra adfs-安全性路由*、 *ra-adfs-新增-路由*、 *ra adfs-adfs 路由*及*ra adfs-proxy 路由*資源群組只能用於 PowerShell 指令碼，並不會發生參數檔案中。

  	|資源群組|參數檔案|
  	|--------------|--------------|
  	|ra adtrust-onpremise 路由|parameters\onpremise\connection.parameters.json<br /> parameters\onpremise\virtualMachines-adds.parameters.json<br />parameters\onpremise\virtualNetwork-adds-dns.parameters.json<br />parameters\onpremise\virtualNetwork.parameters.json<br />parameters\onpremise\virtualNetworkGateway.parameters.json<br />parameters\azure\virtualNetworkGateway.parameters.json
  	|ra adtrust-網路路由|parameters\onpremise\connection.parameters.json<br />parameters\azure\dmz-private.parameters.json<br />parameters\azure\dmz-public.parameters.json<br />parameters\azure\loadBalancer-biz.parameters.json<br />parameters\azure\loadBalancer-data.parameters.json<br />parameters\azure\loadBalancer-web.parameters.json<br />parameters\azure\virtualMachines-adds.parameters.json<br />parameters\azure\virtualMachines-mgmt.parameters.json<br />parameters\azure\virtualNetwork-adds-dns.parameters.json<br />parameters\azure\virtualNetwork.parameters.json<br />parameters\azure\virtualNetworkGateway.parameters.json （*兩個*）

    此外，設定的內部部署和雲端[解決方案元件]所述的元件，[solution-components]一節。

7. 開啟 PowerShell 的 Azure 視窗，移至 [指令碼] 資料夾，然後執行下列命令︰

    ```powershell
    .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> <mode>
    ```

    取代`<subscription id>`您 Azure 訂閱 id。

    針對`<location>`，指定 Azure 的區域，例如`eastus`或`westus`。

    `<mode>`參數會擁有下列的值︰

    - `Onpremise`來建立模擬內部部署環境。

    - `Infrastructure`建立 VNet 基礎結構，並跳雲端中的方塊。

    - `CreateVpn`若要建立 Azure 虛擬網路閘道器，並將其連線至內部部署網路。

    - `AzureADDS`以建構 Vm 做為新增伺服器，這些 Vm 部署 Active Directory 和在雲端建立網域。

    - `WebTier`以建立網頁層 Vm 和負載平衡器。

    - `Prepare`會執行上述的所有工作。 **如果您要建立全新的部署，且您未現有的內部部署基礎結構，這是建議的選項。**

    - `Workload`若要建立商務及資料層 Vm 負載平衡器。 不包含這些 Vm`Prepare`選項。

    >[AZURE.NOTE] 如果您是使用`Prepare`選項，指令碼會幾小時的時間才能完成。

8.  如果您使用範例內部部署設定︰

    1. 連線至 (*ra adtrust-管理 vm1* *ra adtrust-安全性路由*的 [資源] 群組中) 上的 [跳轉] 方塊。 *Testuser*密碼以登入*AweS0me@PW*。

    2.  在 [跳轉] 方塊，請在*contoso.com*網域 （內部部署的網域） 中開啟第一個 VM 上的 RDP 工作階段。 此 VM 有 IP 位址 192.168.0.4。 使用者名稱與密碼*contoso\testuser* *AweS0me@PW*。

    3. 下載[內送 trust.ps1] [incoming-trust]指令碼，執行建立連入信任從*treyresearch.com*網域。

9. 如果您使用自己內部部署基礎結構︰

    1. 下載[內送 trust.ps1] [incoming-trust]指令碼。

    2. 編輯指令碼及取代的值`$TrustedDomainName`變數您自己的網域的名稱。

    3. 執行指令碼。

10. 從跳轉方塊中，連線到*treyresearch.com*網域 （在雲端的網域） 中的第一個 VM。 此 VM 有 IP 位址 10.0.4.4。 使用者名稱與密碼*treyresearch\testuser* *AweS0me@PW*。

11. 下載[輸出 trust.ps1] [outgoing-trust]指令碼，執行建立連入信任從*treyresearch.com*網域。 如果您使用自己內部部署電腦，然後先編輯指令碼。 設定`$TrustedDomainName`變數至您的內部部署網域名稱，並指定 [伺服器 IP 位址 AD DS 中的此網域`$TrustedDomainDnsIpAddresses`變數。

12. 在內部部署電腦上執行[驗證信任的]文件中所述的步驟[verify-a-trust]來決定是否信任關係已正確設定之間的*contoso.com*與*treyresearch.com*網域。 您可能需要幾分鐘的時間才能驗證信任完成上述步驟後，請等候。

剩餘的選用步驟說明如何判斷是否如預期般運作的網域信任。 這些步驟需要安裝的 Visual Studio 有開發電腦的存取權。

1.  從 Azure] 視窗中，執行下列命令以建立 web 層，如果您有不將其設定先前 (使用`Prepare`選項):

    ```powershell
    .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> WebTier
    ```

    這個命令建立 web 層，並將 Vm 加入至*treyresearch.com*網域。

2. 使用 Visual Studio 開發電腦上，建立名為*TreyResearchWebApp*ASP.NET Web 應用程式。 使用.NET Framework 4.5.2。

3. 選取*MVC*範本，然後變更驗證*Windows*驗證]。 不在雲端建立應用程式服務。

3. 建立並執行測試驗證應用程式。 確認您目前的 Windows 使用者名稱顯示在頁面上，向右頂端的功能表列中。

4. 關閉 Internet Explorer。

5. 在*方案總管*] 視窗中，以滑鼠右鍵按一下 TreyResearchWebApp 專案，按一下 [*發佈*]。

6. 在 [*發佈網站*] 視窗中，按一下 [*自訂*]。 建立自訂設定檔名為*TreyResearchWebApp*。

7. 在 [*連線*] 頁面中，將*檔案系統*的*發佈方法*並指定名為*TreyResearchWebApp*，位於 [開發電腦適當的位置。

8. 在 [*設定*] 頁面中，設定*設定**發行版本*。

9. 在*預覽*頁面上，按一下 [*發佈]*。

10. 連線至每個 VM 在網頁層依次 （透過 [跳轉] 方塊中），然後執行下列工作。 網站的 IP 位址層 Vm 是 10.0.1.4 和 10.0.1.5。 兩個 Vm 的使用者名稱會以密碼*treyresearch\testuser* *AweS0me@PW*:

    1. 將從開發電腦*TreyResearchWebApp*資料夾和其內容複製到 [ *C:\inetpub* ] 資料夾。

    2. 使用網際網路服務 (IIS) 管理員主控台，瀏覽至*Sites\Default 網站*在電腦上。

    3. 在 [*動作*] 窗格中，按一下 [*基本設定*]，並變更網站的實體路徑為*%SystemDrive%\inetpub\TreyResearchWebApp*。

    4. 在 [*功能檢視*] 窗格中，按兩下 [*驗證*]。 確認已啟用*Windows 驗證*] 和 [已停用*匿名驗證*。

11. 從內部部署電腦上，開啟 Internet Explorer，瀏覽至網站的 http://10.0.1.254 （這是網頁層負載平衡器的地址）。

12. 在 [ *Windows 安全性*] 對話方塊中，輸入內部部署網域中的使用者認證。 指定不存在於*treyresearch*網域的使用者名稱。 如果您使用的模擬內部部署環境第一次建立*contoso*網域中的使用者，並指定此使用者的認證。

13. 當 [首頁] 頁面隨即出現時，請確認正確的網域和使用者名稱會出現在頁面上，向右頂端的功能表列中。

## <a name="next-steps"></a>後續步驟

- [延伸至 Azure 您內部部署新增網域]最佳作法[adds-extend-domain]

- 瞭解[建立 ADFS 基礎結構]的最佳作法[adfs]Azure 中。

<!-- links -->

[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[adfs]: ./guidance-identity-adfs.md
[adds-extend-domain]: ./guidance-identity-adds-extend-domain.md
[extending-ad-to-azure]: ./guidance-identity-adds-extend-domain.md
[implementing-aad]: ./guidance-identity-aad.md
[implementing-a-multi-tier-architecture-on-Azure]: ./guidance-compute-n-tier-vm.md
[implementing-a-secure-hybrid-network-architecture-with-internet-access]: ./guidance-iaas-ra-secure-vnet-dmz.md
[implementing-a-secure-hybrid-network-architecture]: ./guidance-iaas-ra-secure-vnet-hybrid.md
[implementing-a-hybrid-network-architecture-with-vpn]: ./guidance-hybrid-network-vpn.md
[running-VMs-for-an-N-tier-architecture-on-Azure]: ./guidance-compute-n-tier-vm.md
[azure-vpn-gateway]: https://azure.microsoft.com/documentation/articles/vpn-gateway-about-vpngateways/
[azure-expressroute]: https://azure.microsoft.com/documentation/articles/expressroute-introduction/
[ad-azure-guidelines]: https://msdn.microsoft.com/library/azure/jj156090.aspx
[standby-operations-masters]: https://technet.microsoft.com/library/cc794737(v=ws.10).aspx
[best_practices_ad_password_policy]: https://technet.microsoft.com/magazine/ff741764.aspx
[monitoring_ad]: https://msdn.microsoft.com/library/bb727046.aspx
[microsoft_systems_center]: https://www.microsoft.com/server-cloud/products/system-center-2016/
[creating-forest-trusts]: https://technet.microsoft.com/library/cc816810(v=ws.10).aspx
[creating-external-trusts]: https://technet.microsoft.com/library/cc816837(v=ws.10).aspx
[naming-conventions]: ./guidance-naming-conventions.md
[azure-powershell-download]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[hybrid-azure-on-prem-vpn]: ./guidance-hybrid-network-vpn.md
[verify-a-trust]: https://technet.microsoft.com/library/cc753821.aspx
[netdom]: https://technet.microsoft.com/library/cc835085.aspx
[solution-script]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/Deploy-ReferenceArchitecture.ps1
[on-premises-folder]: https://github.com/mspnp/reference-architectures/tree/master/guidance-identity-adds-trust/parameters/onpremise
[on-premises-vnet-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/onpremise/virtualNetwork.parameters.json
[on-premises-virtualmachines-adds-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/onpremise/virtualMachines-adds.parameters.json
[on-premises-virtualnetworkgateway-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/onpremise/virtualNetworkGateway.parameters.json
[on-premises-connection-parameters]: https://github.com/mspnp/reference-architectures/blob/master/guidance-identity-adds-trust/parameters/onpremise/connection.parameters.json
[incoming-trust]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/extensions/incoming-trust.ps1
[outgoing-trust]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/extensions/outgoing-trust.ps1
[azure-folder]: https://github.com/mspnp/reference-architectures/tree/master/guidance-identity-adds-trust/parameters/azure
[vnet-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/virtualNetwork.parameters.json
[virtualmachines-adds-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/virtualMachines-adds.parameters.json
[add-adds-domain-controller-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/add-adds-domain-controller.parameters.json
[virtualnetworkgateway-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/virtualNetwork.parameters.json
[dmz-private-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/dmz-private.parameters.json
[dmz-public-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/dmz-public.parameters.json
[loadBalancer-web-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/loadBalancer-web.parameters.json
[loadBalancer-biz-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/loadBalancer-biz.parameters.json
[loadBalancer-data-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/loadBalancer-data.parameters.json
[virtualMachines-mgmt-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/virtualMachines-mgmt.parameters.json
[web-vm-domain-join-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/web-vm-domain-join.parameters.json
[solution-components]: [#solution_components]
[0]: ./media/guidance-identity-aad-resource-forest/figure1.png "安全的混合式網路架構與另一個 AD 網域"