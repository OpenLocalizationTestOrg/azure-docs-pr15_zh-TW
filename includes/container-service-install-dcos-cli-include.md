<properties
   pageTitle="安裝 DC/OS CLI |Microsoft Azure"
   description="安裝 DC/OS CLI。"
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="容器，微服務 DC/OS Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/10/2016"
   ms.author="rogardle"/>

>[AZURE.NOTE] 這是使用 DC/OS 型 ACS 叢集。 有不需要的廣域型 ACS 叢集執行這個動作。

首先，[連線到您的 DC/OS 型 ACS 叢集](../articles/container-service/container-service-connect.md)。 一旦您這麼做，您可以安裝 DC/OS CLI 用戶端電腦的下列命令︰

```bash
sudo pip install virtualenv
mkdir dcos && cd dcos
wget https://raw.githubusercontent.com/mesosphere/dcos-cli/master/bin/install/install-optout-dcos-cli.sh
chmod +x install-optout-dcos-cli.sh
./install-optout-dcos-cli.sh . http://localhost --add-path yes
```

如果您使用的舊版 Python，您可能會發現部分 「 InsecurePlatformWarnings 」。 您可以忽略這些。

開始使用不需要重新啟動您的命令介面，才能執行︰

```bash
source ~/.bashrc
```

當您開始新的殼層時，便無法必要此步驟。

現在您已安裝 CLI 確認︰

```bash
dcos --help
```