<properties
   pageTitle="安全叢集使用 Windows 安全性的 Windows 上執行 |Microsoft Azure"
   description="瞭解如何設定在獨立叢集上使用 Windows 安全性的 Windows 上執行的節點-和用戶端-節點安全性。"
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/25/2016"
   ms.author="ryanwi"/>


# <a name="secure-a-standalone-cluster-on-windows-using-windows-security"></a>安全獨立叢集使用 Windows 安全性的 Windows 上

若要防止未經授權的存取服務布料的轉印圖樣叢集必須保護，特別是在有生產負載上執行時。 本文將說明如何設定使用*ClusterConfig.JSON*檔案中的 Windows 安全性的節點-和用戶端-節點安全性，並設定安全性步驟[建立獨立叢集執行 Windows 上](service-fabric-cluster-creation-for-windows-server.md)的對應。 如需有關服務布料的轉印圖樣如何使用 Windows 安全性的詳細資訊，請參閱[叢集安全性案例](service-fabric-cluster-security.md)。

>[AZURE.NOTE]
謹慎，因為有一個安全性選項並未叢集升級到另一個，您應該考慮節點-安全性安全性選項。 變更安全性選取範圍，就需要完整叢集重建。

## <a name="configure-windows-security"></a>設定 Windows 安全性
範例*ClusterConfig.Windows.JSON*設定檔案下載與[Microsoft.Azure.ServiceFabric.WindowsServer。<version>。zip](http://go.microsoft.com/fwlink/?LinkId=730690)獨立叢集套件包含設定 Windows 安全性的範本。  Windows 的安全性設定 [**屬性**] 區段中︰

```
"security": {
            "ClusterCredentialType": "Windows",
            "ServerCredentialType": "Windows",
            "WindowsIdentities": {
        "ClusterIdentity" : "[domain\machinegroup]",
                "ClientIdentities": [{
                    "Identity": "[domain\username]",
                    "IsAdmin": true
                }]
            }
        }
```

|**設定的設定**|**描述**|
|-----------------------|--------------------------|
|ClusterCredentialType|Windows 安全性為啟用設定*Windows* **ClusterCredentialType**參數。|
|ServerCredentialType|用戶端的 Windows 安全性為啟用設定*Windows* **ServerCredentialType**參數。 這表示的叢集和叢集本身，用戶端正在執行中的 Active Directory 網域。|
|WindowsIdentities|包含叢集和用戶端的身分識別。|
|ClusterIdentity|設定節點的安全性。 逗點分隔的群組清單管理服務帳戶或電腦名稱。|
|ClientIdentities|設定用戶端-節點安全性。 用戶端使用者帳戶的陣列。|
|身分識別|用戶端身分識別，網域使用者。|
|IsAdmin|True 指定的網域使用者擁有系統管理員用戶端存取權，使用者用戶端存取，則為 false。|

[節點安全性節點](service-fabric-cluster-security.md#node-to-node-security)，並使用**ClusterIdentity**設定進行設定。 若要建立節點的信任關係，他們必須所發生的每個。 這可以完成兩種方式︰ 指定群組受管理的服務帳戶包含所有節點叢集或叢集中指定的所有節點網域節點身分識別。 我們強烈建議使用[群組受管理的服務帳戶 (gMSA)](https://technet.microsoft.com/library/hh831782.aspx)的方法，尤其是較大叢集 （10 封以上的節點） 或叢集，可能會放大或縮小。
這種方法可讓節點加入或移除 gMSA，而不需變更叢集資訊清單。 此方法時，不需要的叢集系統管理員已授與存取權限新增和移除成員網域群組的建立。 如需詳細資訊，請參閱[開始使用受管理的服務帳戶的群組](http://technet.microsoft.com/library/jj128431.aspx)。

[用戶端節點安全性](service-fabric-cluster-security.md#client-to-node-security)設定使用**ClientIdentities**。 若要建立的用戶端和叢集之間的信任，您必須設定叢集知道的用戶端可以信任的身分識別。 這可以兩種方式︰ 指定的網域群組使用者可以連線或指定可以連線的網域節點使用者。 服務布料的轉印圖樣的用戶端連線至服務布料的轉印圖樣叢集之支援兩種不同的存取控制項類型︰ 系統管理員和使用者。 存取控制提供叢集系統管理員，限制存取特定類型的不同使用者群組，使叢集更安全叢集作業的能力。  系統管理員擁有完整存取權管理功能 （包括讀/寫功能）。 使用者，根據預設，具備只管理功能 （例如，查詢功能） 的 「 讀取 」 權限和解決應用程式與服務的能力。

下列範例**安全性**] 區段，設定 Windows 安全性，並指定在*ServiceFabric/clusterA.contoso.com*機器是叢集一部分*CONTOSO\usera*具有管理員用戶端存取權︰

```
"security": {
    "ClusterCredentialType": "Windows",
    "ServerCredentialType": "Windows",
    "WindowsIdentities": {
        "ClusterIdentity" : "ServiceFabric/clusterA.contoso.com",
        "ClientIdentities": [{
            "Identity": "CONTOSO\\usera",
        "IsAdmin": true
        }]
    }
},
```

## <a name="next-steps"></a>後續步驟

設定後 Windows 安全性*ClusterConfig.JSON*檔案中，繼續叢集建立程序中[建立獨立叢集在 Windows 上執行](service-fabric-cluster-creation-for-windows-server.md)。

如需有關如何節點的安全性、 用戶端-節點安全性和角色型存取控制，請參閱[叢集安全性案例](service-fabric-cluster-security.md)。

如需使用 PowerShell 或 FabricClient 連線的範例，請參閱[連線至安全叢集](service-fabric-connect-to-secure-cluster.md)。
