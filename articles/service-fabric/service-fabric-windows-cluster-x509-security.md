<properties
   pageTitle="連線至安全的私人叢集 |Microsoft Azure"
   description="本文將說明如何維護獨立或私人叢集以及介於用戶端下列和叢集內的通訊安全。"
   services="service-fabric"
   documentationCenter=".net"
   authors="dsk-2015"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/08/2016"
   ms.author="dkshir"/>

# <a name="secure-a-standalone-cluster-on-windows-using-x509-certificates"></a>安全獨立叢集使用 X.509 憑證的 Windows 上

以及如何進行驗證使用的用戶端連線到此叢集，X.509 憑證時，本文將說明如何保護獨立 Windows 叢集，各種節點之間的通訊。 這可確保只有授權的使用者可以存取叢集，已部署的應用程式，並執行管理工作。  建立叢集時，應該啟用安全性憑證叢集上。  

如需有關叢集安全性，例如節點-安全性與用戶端-節點安全性角色型存取控制的詳細資訊，請參閱[叢集安全性案例](service-fabric-cluster-security.md)。

## <a name="which-certificates-will-you-need"></a>您會需要的憑證？

若要開始，到其中一個叢集中節點的 [[下載獨立叢集套件](service-fabric-cluster-creation-for-windows-server.md#downloadpackage)。 下載套件中，您會發現**ClusterConfig.X509.MultiMachine.json**檔案。 開啟檔案，並檢閱 [] 區段，在 [**屬性**] 區段底下的**安全性**︰

    "security": {
        "metadata": "The Credential type X509 indicates this is cluster is secured using X509 Certificates. The thumbprint format is - d5 ec 42 3b 79 cb e5 07 fd 83 59 3c 56 b9 d5 31 24 25 42 64.",
        "ClusterCredentialType": "X509",
        "ServerCredentialType": "X509",
        "CertificateInformation": {
            "ClusterCertificate": {
                "Thumbprint": "[Thumbprint]",
                "ThumbprintSecondary": "[Thumbprint]",
                "X509StoreName": "My"
            },
            "ServerCertificate": {
                "Thumbprint": "[Thumbprint]",
                "ThumbprintSecondary": "[Thumbprint]",
                "X509StoreName": "My"
            },
            "ClientCertificateThumbprints": [{
                "CertificateThumbprint": "[Thumbprint]",
                "IsAdmin": false
            }, {
                "CertificateThumbprint": "[Thumbprint]",
                "IsAdmin": true
            }],
            "ClientCertificateCommonNames": [{
                "CertificateCommonName": "[CertificateCommonName]",
                "CertificateIssuerThumbprint" : "[Thumbprint]",
                "IsAdmin": true
            }]
            "HttpApplicationGatewayCertificate":{
                "Thumbprint": "[Thumbprint]",
                "X509StoreName": "My"
            }
        }
    }

本節描述所需的安全獨立 Windows 叢集憑證。 若要啟用憑證為基礎的安全性設定**ClusterCredentialType**和**ServerCredentialType**的值為*X509*。

>[AZURE.NOTE] [指紋](https://en.wikipedia.org/wiki/Public_key_fingerprint)是憑證的主索引識別碼。 若要找出您建立的憑證指紋讀取[如何擷取指紋的憑證](https://msdn.microsoft.com/library/ms734695.aspx)。

下表列出的憑證，您必須在叢集設定︰

|**CertificateInformation 設定**|**描述**|
|-----------------------|--------------------------|
|ClusterCertificate|這個憑證必須安全叢集節點之間的通訊。 您可以使用兩個不同的憑證、 主要及次要升級。 設定的主要憑證指紋**指紋**區段以及**ThumbprintSecondary**變數中的次要。|
|伺服器憑證|嘗試連線到此叢集時，會顯示用戶端這個憑證。 為了方便，您可以選擇要用於*ClusterCertificate*和*伺服器憑證*的相同的憑證。 您可以使用兩個不同的伺服器憑證、 主要及次要升級。 設定的主要憑證指紋**指紋**區段以及**ThumbprintSecondary**變數中的次要。 |
|ClientCertificateThumbprints|這是一組您要驗證的用戶端上安裝的憑證。 您可以讓您想要允許存取叢集電腦上安裝的其他用戶端憑證的數字。 設定的每個憑證指紋**CertificateThumbprint**變數中。 如果您設定**IsAdmin**為*true*，然後用戶端安裝此憑證可以執行管理員叢集上的管理活動。 如果**IsAdmin**為*false*，這個憑證的用戶端只能執行允許使用者存取權限，通常是唯讀的動作。 如需有關角色閱讀[角色存取控制 (RBAC)](service-fabric-cluster-security.md/#role-based-access-control-rbac)  |
|ClientCertificateCommonNames|設定**CertificateCommonName**常見的第一個用戶端憑證的名稱。 **CertificateIssuerThumbprint**是這個憑證的發行者的指紋。 閱讀[使用憑證](https://msdn.microsoft.com/library/ms731899.aspx)来進一步瞭解常見的名稱和發行者。|
|HttpApplicationGatewayCertificate|這是選用的憑證可指定您是否要安全 Http 應用程式閘道。 請確定 reverseProxyEndpointPort 集 nodeTypes，如果您使用這個憑證。|

以下是範例叢集設定位置已有叢集、 伺服器，以及用戶端憑證。

 ```
 {
    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "2016-09-26",
    "nodes": [{
        "nodeName": "vm0",
        "metadata": "Replace the localhost below with valid IP address or FQDN",
        "iPAddress": "10.7.0.5",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r0",
        "upgradeDomain": "UD0"
    }, {
      "nodeName": "vm1",
            "metadata": "Replace the localhost with valid IP address or FQDN",
        "iPAddress": "10.7.0.4",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r1",
        "upgradeDomain": "UD1"
    }, {
        "nodeName": "vm2",
      "iPAddress": "10.7.0.6",
            "metadata": "Replace the localhost with valid IP address or FQDN",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r2",
        "upgradeDomain": "UD2"
    }],
    "properties": {
        "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "FileShare",
        "IsEncrypted": "false",
        "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
        }
        "security": {
            "metadata": "The Credential type X509 indicates this is cluster is secured using X509 Certificates. The thumbprint format is - d5 ec 42 3b 79 cb e5 07 fd 83 59 3c 56 b9 d5 31 24 25 42 64.",
            "ClusterCredentialType": "X509",
            "ServerCredentialType": "X509",
            "CertificateInformation": {
                "ClusterCertificate": {
                    "Thumbprint": "a8 13 67 58 f4 ab 89 62 af 2b f3 f2 79 21 be 1d f6 7f 43 26",
                    "X509StoreName": "My"
                },
                "ServerCertificate": {
                    "Thumbprint": "a8 13 67 58 f4 ab 89 62 af 2b f3 f2 79 21 be 1d f6 7f 43 26",
                    "X509StoreName": "My"
                },
                "ClientCertificateThumbprints": [{
                    "CertificateThumbprint": "c4 c18 8e aa a8 58 77 98 65 f8 61 4a 0d da 4c 13 c5 a1 37 6e",
                    "IsAdmin": false
                }, {
                    "CertificateThumbprint": "71 de 04 46 7c 9e d0 54 4d 02 10 98 bc d4 4c 71 e1 83 41 4e",
                    "IsAdmin": true
                }]
            }
        },
        "reliabilityLevel": "Bronze",
        "nodeTypes": [{
            "name": "NodeType0",
            "clientConnectionEndpointPort": "19000",
            "clusterConnectionEndpoint": "19001",
            "httpGatewayEndpointPort": "19080",
            "applicationPorts": {
                "startPort": "20001",
                "endPort": "20031"
            },
            "ephemeralPorts": {
                "startPort": "20032",
                "endPort": "20062"
            },
            "isPrimary": true
        }
         ],
        "fabricSettings": [{
            "name": "Setup",
            "parameters": [{
                "name": "FabricDataRoot",
                "value": "C:\\ProgramData\\SF"
            }, {
                "name": "FabricLogRoot",
                "value": "C:\\ProgramData\\SF\\Log"
            }]
        }]
    }
}
 ```

## <a name="aquire-the-x509-certificates"></a>取得 X.509 憑證
若要安全叢集通訊，您需要取得叢集節點的 X.509 憑證。 此外，若要限制此叢集授權的電腦使用者連線，您需要取得並安裝用戶端電腦的憑證。

執行的實際執行工作負載的叢集，則應使用的[憑證授權單位 (CA)](https://en.wikipedia.org/wiki/Certificate_authority)登入至安全叢集的 X.509 憑證。 如需取得這些憑證的詳細資訊，請移至[如何︰ 取得憑證](http://msdn.microsoft.com/library/aa702761.aspx)。

針對您要用於測試用途的叢集，您可以選擇使用自我簽署的憑證。

## <a name="optional-create-a-self-signed-certificate"></a>可省略︰ 建立自我簽署的憑證
若要建立自我簽署的憑證可以正確受保護的其中一個方法是使用*CertSetup.ps1*指令碼*C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\Secure*目錄中的 [服務布料的轉印圖樣 SDK] 資料夾中。 編輯此檔案，並使用此選項建立憑證以適當的名稱。

現在憑證匯出到 PFX 檔案以受保護的密碼。 首先，您必須取得指紋的憑證。 執行 certmgr.exe 應用程式。 瀏覽至**本機 Computer\Personal**資料夾，尋找您剛剛建立的憑證。 按兩下加以開啟，請選取 [*詳細資料*] 索引標籤並捲動至 [*指紋*] 欄位的憑證。 複製下方的 PowerShell 命令至指紋值移除空格。  變更適合安全密碼保護，並執行 PowerShell *$pswd*值︰

```   
$pswd = ConvertTo-SecureString -String "1234" -Force –AsPlainText
Get-ChildItem -Path cert:\localMachine\my\<Thumbprint> | Export-PfxCertificate -FilePath C:\mypfx.pfx -Password $pswd
```

若要查看的電腦上安裝的憑證詳細資料，您可以執行下列 PowerShell 命令︰

```
$cert = Get-Item Cert:\LocalMachine\My\<Thumbprint>
Write-Host $cert.ToString($true)
```

或者，如果您有 Azure 的訂閱，請遵循[新增憑證金鑰保存庫](service-fabric-cluster-creation-via-arm.md#add-certificate-to-key-vault)] 區段。

## <a name="install-the-certificates"></a>安裝的憑證
憑證之後，您可以叢集節點上安裝。 您的節點必須擁有最新的 Windows PowerShell 3.x 上加以安裝。 您必須重複這些步驟，在每個節點，叢集和伺服器的憑證，以及任何次要的憑證。

1. 將.pfx 檔複製到節點。
2. 開啟以系統管理員身分 PowerShell 視窗，然後輸入下列命令。 *$Pswd*換成您用來建立這個憑證的密碼。 取代*$PfxFilePath*複製到這個節點.pfx 的完整路徑。

    ```
    $pswd = "1234"
    $PfcFilePath ="C:\mypfx.pfx"
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\LocalMachine\My -FilePath $PfxFilePath -Password (ConvertTo-SecureString -String $pswd -AsPlainText -Force)
    ```

3. 接下來，您需要設定這個憑證的存取控制項，讓服務布料的轉印圖樣程序，可以執行網路服務的帳號] 之下，可以使用它來執行下列指令碼。 提供憑證和服務帳戶的 「 網路服務 」 的指紋。 您可以檢查在憑證上的 Acl 會使用 certmgr.exe 工具，並查看憑證管理私密金鑰正確。

    ```
    param
    (
        [Parameter(Position=1, Mandatory=$true)]
        [ValidateNotNullOrEmpty()]
        [string]$pfxThumbPrint,

        [Parameter(Position=2, Mandatory=$true)]
        [ValidateNotNullOrEmpty()]
        [string]$serviceAccount
        )

        $cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object -FilterScript { $PSItem.ThumbPrint -eq $pfxThumbPrint; };

        # Specify the user, the permissions and the permission type
        $permission = "$($serviceAccount)","FullControl","Allow"
        $accessRule = New-Object -TypeName System.Security.AccessControl.FileSystemAccessRule -ArgumentList $permission;

        # Location of the machine related keys
        $keyPath = $env:ProgramData + "\Microsoft\Crypto\RSA\MachineKeys\";
        $keyName = $cert.PrivateKey.CspKeyContainerInfo.UniqueKeyContainerName;
        $keyFullPath = $keyPath + $keyName;

        # Get the current acl of the private key
        $acl = (Get-Item $keyFullPath).GetAccessControl('Access')

        # Add the new ace to the acl of the private key
        $acl.SetAccessRule($accessRule);

        # Write back the new acl
        Set-Acl -Path $keyFullPath -AclObject $acl -ErrorAction Stop

        #Observe the access rights currently assigned to this certificate.
        get-acl $keyFullPath| fl
        ```

4. Repeat the steps above for each server certificate. You can also use these steps to install the client certificates on the machines that you want to allow access to the cluster.

## Create the secure cluster
After configuring the **security** section of the **ClusterConfig.X509.MultiMachine.json** file, you can proceed to [Create your cluster](service-fabric-cluster-creation-for-windows-server.md#createcluster) section to configure the nodes and create the standalone cluster. Remember to use the **ClusterConfig.X509.MultiMachine.json** file while creating the cluster. For example, your command might look like the following:

```
.\CreateServiceFabricCluster.ps1-ClusterConfigFilePath.\ClusterConfig.X509.MultiMachine.json-MicrosoftServiceFabricCabFilePath.\MicrosoftAzureServiceFabric.cab-AcceptEULA $true
```

Once you have the secure standalone Windows cluster successfully running, and have setup the authenticated clients to connect to it, follow the section [Connect to a secure cluster using PowerShell](service-fabric-connect-to-secure-cluster.md#connectsecurecluster) to connect to it. For example:

```
連線 ServiceFabricCluster-ConnectionEndpoint 10.7.0.4:19000 KeepAliveIntervalInSec 10-X509Credential-ServerCertThumbprint 057b9544a6f2733e0c8d3a60013a58948213f551-FindType FindByThumbprint-FindValue 057b9544a6f2733e0c8d3a60013a58948213f551-StoreLocation CurrentUser-storename] 拖曳我
```

If you are logged on to one of the machines in the cluster, since this already has the certificate installed locally you can simply run the Powershell command to connect to the cluster and show a list of nodes:

```
連線 ServiceFabricCluster 取得-ServiceFabricNode
```
To remove the cluster call the following command:

```
.\RemoveServiceFabricCluster.ps1-ClusterConfigFilePath.\ClusterConfig.X509.MultiMachine.json-MicrosoftServiceFabricCabFilePath.\MicrosoftAzureServiceFabric.cab
```
