<properties
   pageTitle="自動化虛擬機器副檔名為應用程式部署 |Microsoft Azure"
   description="Azure 虛擬機器 DotNet 核心教學課程"
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="neilpeterson"
   manager="timlt"
   editor="tysonn"
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="09/21/2016"
   ms.author="nepeters"/>

# <a name="application-deployment-with-azure-resource-manager-templates"></a>使用 Azure 資源管理員範本的應用程式部署

一旦識別及部署範本轉換成數所有 Azure infrastructural 需求，實際的應用程式部署需要解決。 在此應用程式部署參考安裝至 Azure 資源實際的應用程式的二進位檔案。 音樂市集範例，.Net 核心與 NGINX，主管必須安裝並設定每個虛擬機器上。 音樂市集二進位檔案必須安裝至虛擬機器，和預先建立的音樂儲存庫資料庫。

這份文件詳細說明如何虛擬機器副檔名可以自動化應用程式部署和 Azure 虛擬機器的設定。 所有的相依性和唯一的設定會醒目提示。 為了獲得最佳體驗，預先部署至 Azure 訂閱及工作，以及 Azure 資源管理員範本解決方案的執行個體。 完成範本可找到以下 – [Ubuntu 上的音樂市集部署](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux)。

## <a name="configuration-script"></a>設定指令碼

虛擬機器擴充功能是針對提供設定自動化的虛擬機器執行的特定的程式。 多個特定用途，例如防毒軟體、 記錄設定和 Docker 設定的可延伸。 自訂指令碼副檔名為可用於執行虛擬機器中的任何指令碼。 音樂市集範例中，是由設定 Ubuntu 虛擬機器並安裝的音樂市集應用程式的自訂指令碼副檔名。

之前詳細說明如何將虛擬機器延伸宣告 Azure 資源管理員範本中，檢查執行的指令碼。 這個指令碼設定 Ubuntu 虛擬機器裝載的音樂市集應用程式。 當執行時，指令碼會安裝所有需要的軟體，安裝音樂 store 應用程式從來源控制項，並準備資料庫。 

若要進一步瞭解裝載.Net 核心 Linux 上的應用程式，請參閱[發佈至 Linux 生產環境](https://docs.asp.net/en/latest/publishing/linuxproduction.html)。 

> 此範例是供示範。

```none
#!/bin/bash

# install dotnet core
sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ trusty main" > /etc/apt/sources.list.d/dotnetdev.list'
sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
sudo apt-get update
sudo apt-get install -y dotnet-dev-1.0.0-preview2-003121

# download application
sudo wget https://raw.github.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/music-app/music-store-azure-demo-pub.tar /
sudo mkdir /opt/music
sudo tar -xf music-store-azure-demo-pub.tar -C /opt/music

# install nginx, update config file
sudo apt-get install -y nginx
sudo service nginx start
sudo touch /etc/nginx/sites-available/default
sudo wget https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/music-app/nginx-config/default -O /etc/nginx/sites-available/default
sudo cp /opt/music/nginx-config/default /etc/nginx/sites-available/
sudo nginx -s reload

# update and secure music config file
sed -i "s/<replaceserver>/$1/g" /opt/music/config.json
sed -i "s/<replaceuser>/$2/g" /opt/music/config.json
sed -i "s/<replacepass>/$3/g" /opt/music/config.json
sudo chown $2 /opt/music/config.json
sudo chmod 0400 /opt/music/config.json

# config supervisor
sudo apt-get install -y supervisor
sudo touch /etc/supervisor/conf.d/music.conf
sudo wget https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/music-app/supervisor/music.conf -O /etc/supervisor/conf.d/music.conf
sudo service supervisor stop
sudo service supervisor start

# pre-create music store database
/usr/bin/dotnet /opt/music/MusicStore.dll &
```

## <a name="vm-script-extension"></a>VM 指令碼副檔名

VM 副檔名可以針對來執行虛擬機器建立時間，包括中 Azure 資源管理員範本的副檔名資源。 使用 Visual Studio 新增資源] 精靈中，或有效 JSON 插入範本，您可以加入副檔名。 指令碼副檔名資源是巢狀嵌入的虛擬機器資源。這可以在下列範例中看到。

請遵循這個連結，請參閱 JSON 範例中的資源管理員範本 – [VM 指令碼副檔名](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L359)。 

請注意，在 JSON，指令碼會儲存在 GitHub 下方。 這個指令碼也可能會儲存在 Azure Blob 儲存體。 此外，Azure 資源管理員範本可讓建構，例如範本參數值可以做為參數用於執行指令碼的指令碼執行字串。 部署範本] 時，在此情況下提供資料，並執行指令碼時，可以再使用這些值。

```none
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
      ]
    },
    "protectedSettings": {
      "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
  }
}
```

如需有關如何使用自訂指令碼延伸模組的詳細資訊，請參閱[自訂指令碼副檔名與資源管理員範本](./virtual-machines-linux-extensions-customscript.md)。

## <a name="next-step"></a>下一步

<hr>

[瀏覽更多 Azure 資源管理員範本](https://github.com/Azure/azure-quickstart-templates)
