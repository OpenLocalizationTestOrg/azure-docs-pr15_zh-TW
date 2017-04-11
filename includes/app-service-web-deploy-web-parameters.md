使用 Azure 資源管理員中，您可以定義您想要指定部署範本時的值的參數。 範本包含稱為參數的參數值的所有節。
您應該定義的參數會根據您要部署的專案，或根據您要部署到的環境而這些值。 不會定義參數會永遠保持不變的值。 每個參數值範本中用於定義部署的資源。 

定義參數時, 使用**allowedValues**欄位來指定哪些使用者可以部署期間所提供的值。 使用**預設值]**欄位指定值參數，如果在部署未不提供任何值。

我們會說明在範本中的每個參數。

### <a name="sitename"></a>站台名稱

您想要建立 web 應用程式的名稱。

    "siteName":{
      "type":"string"
    }

### <a name="hostingplanname"></a>hostingPlanName

若要使用的主機服務 web 應用程式的應用程式服務方案名稱。
    
    "hostingPlanName":{
      "type":"string"
    }

### <a name="sku"></a>sku

主機服務方案的價格層。

    "sku": {
      "type": "string",
      "allowedValues": [
        "F1",
        "D1",
        "B1",
        "B2",
        "B3",
        "S1",
        "S2",
        "S3",
        "P1",
        "P2",
        "P3",
        "P4"
      ],
      "defaultValue": "S1",
      "metadata": {
        "description": "The pricing tier for the hosting plan."
      }
    }

範本定義此參數，允許的值，並會指派預設值 (S1)，如果未指定的值。

### <a name="workersize"></a>workerSize

執行個體的大小 （小型、 中、 大型） 的主機服務方案。

    "workerSize":{
      "type":"string",
      "allowedValues":[
        "0",
        "1",
        "2"
      ],
      "defaultValue":"0"
    }
    
範本定義允許使用此參數 （0、 1 或 2） 的值，並會指派預設值 (0)，如果未指定的值。 值會對應到小型、 中型和大型的範圍。
