### <a name="app-service-plan"></a>應用程式服務計劃

建立裝載 web 應用程式的服務方案。 您提供的方案，透過**hostingPlanName**參數的名稱。 計劃的位置會是資源群組所用的同一個位置。 **Sku**和**workerSize**參數會指定價格層和工作者大小

    {
      "apiVersion": "2015-08-01",
      "name": "[parameters('hostingPlanName')]",
      "type": "Microsoft.Web/serverfarms",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "[parameters('sku')]",
        "capacity": "[parameters('workerSize')]"
      },
      "properties": {
        "name": "[parameters('hostingPlanName')]"
      }
    },

