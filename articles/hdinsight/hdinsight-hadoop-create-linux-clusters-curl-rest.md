<properties
    pageTitle="在使用捲曲和 Azure REST API HDInsight Linux 上建立 Hadoop、 HBase 或大量叢集 |Microsoft Azure"
    description="瞭解如何建立使用捲曲、 Azure 資源管理員範本和 Azure REST API Linux 型 HDInsight 叢集。 您可以指定叢集類型 （Hadoop、 HBase 或大量，），或使用指令碼來安裝自訂元件。"
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="10/11/2016"
    ms.author="larryfr"/>

#<a name="create-linux-based-clusters-in-hdinsight-using-curl-and-the-azure-rest-api"></a>建立使用捲曲和 Azure REST API HDInsight Linux 型叢集

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

Azure REST API 可讓您管理上執行作業裝載於 Azure 的平台，包括新的資源，例如 Linux 型 HDInsight 叢集建立的服務。 在此文件中，您將學習如何建立 Azure 資源管理員設定 HDInsight 叢集，相關聯的儲存空間，然後使用捲曲 Azure REST api 來建立新的 HDInsight 叢集部署範本的範本。

> [AZURE.IMPORTANT] 這份文件中的步驟 HDInsight 叢集使用預設的工作者節點數目 (4)。 如果您打算 32 個以上的工作者節點，在叢集建立，或藉由縮放叢集之後建立，您就必須選取至少 8 個核心與 14 GB ram 的標頭節點大小。
>
> 如需有關節點大小和相關聯的成本的詳細資訊，請參閱[HDInsight 價格](https://azure.microsoft.com/pricing/details/hdinsight/)。

##<a name="prerequisites"></a>必要條件

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


- **Azure 訂閱**。 請參閱[取得 Azure 免費試用版](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。

- __Azure CLI__。 Azure CLI 用來建立原則，然後用來產生驗證的權杖 Azure REST API 要求的服務。

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

- __cURL__。 此公用程式可透過套件管理系統，或您可以從[http://curl.haxx.se/](http://curl.haxx.se/)下載。

    > [AZURE.NOTE] 如果您使用 PowerShell 來執行這份文件中的命令，您必須先移除`curl`，依預設會建立的別名。 這個別名，而不是當您使用的捲曲使用叫用的 PowerShell cmdlet，WebRequest`curl`命令 PowerShell 提示字元中，將會傳回錯誤的文件中使用的命令。
    > 
    > 若要移除這個別名，請使用下列 PowerShell 提示︰
    >
    > `Remove-item alias:curl`
    >
    > 已移除的別名，您可以使用捲曲系統上已安裝的版本。

### <a name="access-control-requirements"></a>存取控制需求

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

##<a name="create-a-template"></a>建立範本

Azure 資源管理範本是以 JSON 文件描述__資源] 群組__中的所有資源 （例如 HDInsight。)此範本基礎的方法可讓您定義一個範本，HDInsight 需要的所有資源，並將變更套用至] 群組中的__部署__到整個管理] 群組中的變更。

有兩個部分; 通常提供範本範本本身，以及參數檔案的特定值填入您的設定。 範例、 叢集名稱、 系統管理員名稱和密碼。 直接使用時 REST API，您必須將這些成一個檔案合併。 此 JSON 文件的格式為︰

    {
        "properties": {
            "template": {
                contents of template file
            },
            "mode": "deploymentmode",
            "Parameters": {
                contents of the parameters element from parameters file
            }
        }
    }

例如，以下是從[https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password)，建立 Linux 型叢集使用密碼來保護 SSH 使用者帳戶的範本和參數的檔案合併。

    {
        "properties": {
            "template": {
                "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                "contentVersion": "1.0.0.0",
                "parameters": {
                    "location": {
                        "type": "string",
                        "allowedValues": ["Central US",
                        "East Asia",
                        "East US",
                        "Japan East",
                        "Japan West",
                        "North Europe",
                        "South Central US",
                        "Southeast Asia",
                        "West Europe",
                        "West US"],
                        "metadata": {
                            "description": "The location where all azure resources will be deployed."
                        }
                    },
                    "clusterType": {
                        "type": "string",
                        "allowedValues": ["hadoop",
                        "hbase",
                        "storm",
                        "spark"],
                        "metadata": {
                            "description": "The type of the HDInsight cluster to create."
                        }
                    },
                    "clusterName": {
                        "type": "string",
                        "metadata": {
                            "description": "The name of the HDInsight cluster to create."
                        }
                    },
                    "clusterLoginUserName": {
                        "type": "string",
                        "metadata": {
                            "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
                        }
                    },
                    "clusterLoginPassword": {
                        "type": "securestring",
                        "metadata": {
                            "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
                        }
                    },
                    "sshUserName": {
                        "type": "string",
                        "metadata": {
                            "description": "These credentials can be used to remotely access the cluster."
                        }
                    },
                    "sshPassword": {
                        "type": "securestring",
                        "metadata": {
                            "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
                        }
                    },
                    "clusterStorageAccountName": {
                        "type": "string",
                        "metadata": {
                            "description": "The name of the storage account to be created and be used as the cluster's storage."
                        }
                    },
                    "clusterWorkerNodeCount": {
                        "type": "int",
                        "defaultValue": 4,
                        "metadata": {
                            "description": "The number of nodes in the HDInsight cluster."
                        }
                    }
                },
                "variables": {
                    "defaultApiVersion": "2015-05-01-preview",
                    "clusterApiVersion": "2015-03-01-preview"
                },
                "resources": [{
                    "name": "[parameters('clusterStorageAccountName')]",
                    "type": "Microsoft.Storage/storageAccounts",
                    "location": "[parameters('location')]",
                    "apiVersion": "[variables('defaultApiVersion')]",
                    "dependsOn": [],
                    "tags": {
                        
                    },
                    "properties": {
                        "accountType": "Standard_LRS"
                    }
                },
                {
                    "name": "[parameters('clusterName')]",
                    "type": "Microsoft.HDInsight/clusters",
                    "location": "[parameters('location')]",
                    "apiVersion": "[variables('clusterApiVersion')]",
                    "dependsOn": ["[concat('Microsoft.Storage/storageAccounts/',parameters('clusterStorageAccountName'))]"],
                    "tags": {
                        
                    },
                    "properties": {
                        "clusterVersion": "3.2",
                        "osType": "Linux",
                        "clusterDefinition": {
                            "kind": "[parameters('clusterType')]",
                            "configurations": {
                                "gateway": {
                                    "restAuthCredential.isEnabled": true,
                                    "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
                                    "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
                                }
                            }
                        },
                        "storageProfile": {
                            "storageaccounts": [{
                                "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
                                "isDefault": true,
                                "container": "[parameters('clusterName')]",
                                "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), variables('defaultApiVersion')).key1]"
                            }]
                        },
                        "computeProfile": {
                            "roles": [{
                                "name": "headnode",
                                "targetInstanceCount": "2",
                                "hardwareProfile": {
                                    "vmSize": "Standard_D3"
                                },
                                "osProfile": {
                                    "linuxOperatingSystemProfile": {
                                        "username": "[parameters('sshUserName')]",
                                        "password": "[parameters('sshPassword')]"
                                    }
                                }
                            },
                            {
                                "name": "workernode",
                                "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
                                "hardwareProfile": {
                                    "vmSize": "Standard_D3"
                                },
                                "osProfile": {
                                    "linuxOperatingSystemProfile": {
                                        "username": "[parameters('sshUserName')]",
                                        "password": "[parameters('sshPassword')]"
                                    }
                                }
                            }]
                        }
                    }
                }],
                "outputs": {
                    "cluster": {
                        "type": "object",
                        "value": "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
                    }
                }
            },
            "mode": "incremental",
            "Parameters": {
                "location": {
                    "value": "North Europe"
                },
                "clusterName": {
                    "value": "newclustername"
                },
                "clusterType": {
                    "value": "hadoop"
                },
                "clusterStorageAccountName": {
                    "value": "newstoragename"
                },
                "clusterLoginUserName": {
                    "value": "admin"
                },
                "clusterLoginPassword": {
                    "value": "changeme"
                },
                "sshUserName": {
                    "value": "sshuser"
                },
                "sshPassword": {
                    "value": "changeme"
                }
            }
        }
    }

此範例會用於這份文件中的步驟進行。 您必須以您想要使用叢集的值來取代版面配置區中的_值_在文件結尾處的 [__參數__] 區段。

##<a name="login-to-your-azure-subscription"></a>登入您 Azure 的訂閱

請依照在[連線至 Azure 訂閱從 Azure 命令列介面 (Azure CLI)](../xplat-cli-connect.md)記錄的步驟，並連線到您的訂閱使用`azure login`] 命令。

##<a name="create-a-service-principal"></a>建立服務原則

> [AZURE.NOTE] 這些步驟是資訊的 dpm 的版本的_本金使用密碼-Azure CLI 驗證服務_] 區段中的[驗證服務主要與 Azure 資源管理員](../resource-group-authenticate-service-principal.md#authenticate-service-principal-with-password---azure-cli)的文件中所提供。 這些步驟建立新的服務主體，可以用來驗證用來建立 Azure 的資源，例如 HDInsight 叢集 REST API 邀請。

1. 在命令提示字元中，終端機工作階段或命令介面，會使用下列命令清單的 Azure 訂閱。

        azure account list
        
    在清單中，選取您想要使用，並記下__識別碼__] 資料行的訂閱。 這是__訂閱識別碼__，並將用於大部分的文件中的步驟。

2. Azure Active Directory 中建立新的應用程式。

        azure ad app create --name "exampleapp" --home-page "https://www.contoso.org" --identifier-uris "https://www.contoso.org/example" --password <Your_Password>
        
    取代的值`--name`， `--home-page`，及`--identifier-uris`使用您自己的值。 新的 Active Directory 項目的提供的密碼。
    
    > [AZURE.NOTE] 因為您正在建立驗證服務主體，透過此應用程式`--home-page`和`--identifier-uris`值不需要參照裝載於網際網路; 實際的網頁只需要是唯一的 Uri。
    
    傳回的資料，從儲存__AppId__值。
    
        data:    AppId:          4fd39843-c338-417d-b549-a545f584a745
        data:    ObjectId:       4f8ee977-216a-45c1-9fa3-d023089b2962
        data:    DisplayName:    exampleapp
        ...
        info:    ad app create command OK
    
3. 建立主要使用__AppId__值傳回先前的服務。

        azure ad sp create 4fd39843-c338-417d-b549-a545f584a745
        
     傳回的資料，從儲存__物件識別碼__值。
     
        info:    Executing command ad sp create
        - Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
        data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
        data:    Display Name:     exampleapp
        data:    Service Principal Names:
        data:                      4fd39843-c338-417d-b549-a545f584a745
        data:                      https://www.contoso.org/example
        info:    ad sp create command OK
        
4. __擁有者__角色指派給本金使用__物件識別碼__值傳回先前的服務。 您也必須使用較舊版本取得__訂閱識別碼__。
    
        azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Owner -c /subscriptions/{SubscriptionID}/
        
    這個命令完成後，主要服務現在有擁有者指定的訂閱識別碼存取

##<a name="get-an-authentication-token"></a>取得驗證權杖

1. 您可以使用下列來尋找您的訂閱的__租用戶識別碼__。

        azure account show -s <subscription ID>
        
    傳回的資料，從尋找的__租用戶識別碼__。
    
        info:    Executing command account show
        data:    Name                        : MyAzureAccount
        data:    ID                          : 45a1014d-0f27-25d2-b838-b8f373d6d52e
        data:    State                       : Enabled
        data:    Tenant ID                   : 22f988bf-56f1-41af-91ab-3d7cd011db47
        data:    Is Default                  : true
        data:    Environment                 : AzureCloud
        data:    Has Certificate             : No
        data:    Has Access Token            : Yes
        data:    User name                   : myname@contoso.org
        data:    
        info:    account show command OK

2. 產生新的權杖使用 Azure REST API。

        curl -X "POST" "https://login.microsoftonline.com/TenantID/oauth2/token" \
        -H "Cookie: flight-uxoptin=true; stsservicecookie=ests; x-ms-gateway-slice=productionb; stsservicecookie=ests" \
        -H "Content-Type: application/x-www-form-urlencoded" \
        --data-urlencode "client_id=AppID" \
        --data-urlencode "grant_type=client_credentials" \
        --data-urlencode "client_secret=password" \
        --data-urlencode "resource=https://management.azure.com/"
    
    取得或使用先前的值取代__TenantID__、 __AppID__和__密碼__。

    如果此要求成功，您會收到 200 數列回應，而且回應內容會包含 JSON 文件。

    此要求傳回 JSON 文件會包含名稱為__access_token__; 的項目此元素的值是您必須驗證使用這份文件的下一節使用要求存取權杖。
    
        {
            "token_type":"Bearer",
            "expires_in":"3599",
            "expires_on":"1463409994",
            "not_before":"1463406094",
            "resource":"https://management.azure.com/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWoNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL21hbmFnZW1lbnQuYXp1cmUuY29tLyIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI2Ny8iLCJpYXQiOjE0NjM0MDYwOTQsIm5iZiI6MTQ2MzQwNjA5NCwiZXhwIjoxNDYzNDA5OTk5LCJhcHBpZCI6IjBlYzcyMzM0LTZkMDMtNDhmYi04OWU1LTU2NTJiODBiZDliYiIsImFwcGlkYWNyIjoiMSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJvaWQiOiJlNjgxZTZiMi1mZThkLTRkZGUtYjZiMS0xNjAyZDQyNWQzOWYiLCJzdWIiOiJlNjgxZTZiMi1mZThkLTRkZGUtYjZiMS0xNjAyZDQyNWQzOWYiLCJ0aWQiOiI3MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDciLCJ2ZXIiOiIxLjAifQ.nJVERbeDHLGHn7ZsbVGBJyHOu2PYhG5dji6F63gu8XN2Cvol3J1HO1uB4H3nCSt9DTu_jMHqAur_NNyobgNM21GojbEZAvd0I9NY0UDumBEvDZfMKneqp7a_cgAU7IYRcTPneSxbD6wo-8gIgfN9KDql98b0uEzixIVIWra2Q1bUUYETYqyaJNdS4RUmlJKNNpENllAyHQLv7hXnap1IuzP-f5CNIbbj9UgXxLiOtW5JhUAwWLZ3-WMhNRpUO2SIB7W7tQ0AbjXw3aUYr7el066J51z5tC1AK9UC-mD_fO_HUP6ZmPzu5gLA6DxkIIYP3grPnRVoUDltHQvwgONDOw"
        }

##<a name="create-a-resource-group"></a>建立資源群組

使用下列建立新的資源群組。 您可以建立的資源，例如 HDInsight 叢集之前，您必須先建立群組。 

* 取代__SubscriptionID__建立服務主要時收到的訂閱識別碼。
* 在上一個步驟中收到的存取權杖取代__AccessToken__ 。
* __DataCenterLocation__換成您想要在中建立資源] 群組中，與資源資料中心。 例如，「 南部美國中部 」。 
* __ResourceGroupName__換成您想要使用此群組的名稱︰

```
curl -X "PUT" "https://management.azure.com/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName?api-version=2015-01-01" \
    -H "Authorization: Bearer AccessToken" \
    -H "Content-Type: application/json" \
    -d $'{
"location": "DataCenterLocation"
}'
```

如果此要求成功，您會收到 200 數列回應，而且回應內容會包含 JSON 文件包含有關群組的資訊。 `"provisioningState"`項目會包含值`"Succeeded"`。

##<a name="create-a-deployment"></a>建立部署

使用下列部署叢集設定 （範本和參數值、） 資源群組。

* __SubscriptionID__和__AccessToken__取代之前的值。 
* __ResourceGroupName__換成您在前一節中建立的資源群組名稱。
* __DeploymentName__換成您想要使用此部署的名稱。

```
curl -X "PUT" "https://management.azure.com/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName/providers/microsoft.resources/deployments/DeploymentName?api-version=2015-01-01" \
-H "Authorization: Bearer AccessToken" \
-H "Content-Type: application/json" \
-d "{set your body string to the template and parameters}"
```

> [AZURE.NOTE] 如果您已儲存的範本和檔案的參數值在包含 JSON 文件，您可以使用下列而不是`-d "{ template and parameters}"`:
>
> `--data-binary "@/path/to/file.json"`

如果此要求成功，您會收到 200 數列回應並回應內容包含 JSON 文件包含有關部署作業的資訊。

> [AZURE.IMPORTANT] 請注意，部署已送出，但尚未完成這一次。 可能需要幾分鐘，通常約 15，以供部署完成。

##<a name="check-the-status-of-a-deployment"></a>檢查部署的狀態

若要檢查的部署狀態，請使用下列步驟︰

* __SubscriptionID__和__AccessToken__取代之前的值。 
* __ResourceGroupName__換成您在前一節中建立的資源群組名稱。

```
curl -X "GET" "https://management.azure.com/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName/providers/microsoft.resources/deployments/DeploymentName?api-version=2015-01-01" \
-H "Authorization: Bearer AccessToken" \
-H "Content-Type: application/json"
```

這會傳回資訊 JSON 文件包含有關部署作業的資訊。 `"provisioningState"`項目會包含部署; 的狀態如果此頁面包含值`"Succeeded"`，然後部署已順利完成。 此時，叢集應該可供使用。

##<a name="next-steps"></a>後續步驟

現在您已成功建立 HDInsight 叢集，使用下列若要瞭解如何使用叢集。 

###<a name="hadoop-clusters"></a>Hadoop 叢集

* [使用 HDInsight 的登錄區](hdinsight-use-hive.md)
* [使用 HDInsight 的豬](hdinsight-use-pig.md)
* [使用 HDInsight MapReduce](hdinsight-use-mapreduce.md)

###<a name="hbase-clusters"></a>HBase 叢集

* [在 HDInsight HBase 快速入門](hdinsight-hbase-tutorial-get-started-linux.md)
* [Java 為開發應用程式上 HDInsight HBase](hdinsight-hbase-build-java-maven-linux.md)

###<a name="storm-clusters"></a>大量叢集

* [開發上 HDInsight 大量 Java 拓撲](hdinsight-storm-develop-java-topology.md)
* [使用大量上 HDInsight Python 元件](hdinsight-storm-develop-python-topology.md)
* [部署，並監控與上 HDInsight 大量的拓撲](hdinsight-storm-deploy-monitor-topology-linux.md)
