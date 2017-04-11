<properties
   pageTitle="應用程式或特定使用者 Marathon 服務 |Microsoft Azure"
   description="建立應用程式或特定使用者 Marathon 服務"
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="容器，Marathon，微服務，亦即/OS、 Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/12/2016"
   ms.author="rogardle"/>

# <a name="create-an-application-or-user-specific-marathon-service"></a>建立應用程式或特定使用者 Marathon 服務

Azure 容器服務會提供一組我們要在其預先 Apache Mesos 和 Marathon 主伺服器。 這些可以用來協調叢集，應用程式，但最好不使用這項目的主要伺服器。 調整 Marathon 的設定，例如需要登入自己的主要伺服器並加以變更，這可促進的標準稍有不同，而且必須針對在意這次，獨立管理唯一主伺服器。 此外，一小組所需的設定，可能不是另一個小組最佳的設定。

在本文中，我們會說明如何新增應用程式或特定使用者 Marathon 服務。

這項服務會隸屬於單一使用者或小組，因為他們的年齡免費任何他們想要的方式加以設定。 此外，Azure 容器服務可確保服務會繼續執行。 如果服務失敗，請 Azure 容器服務會為您重新。 大部分的時間您不會注意原先停機時間。

## <a name="prerequisites"></a>必要條件

使用 orchestrator 類型 DC/OS 的[部署 Azure 容器服務的執行個體](container-service-deployment.md)，並[確保您的用戶端可以連線到您的叢集](container-service-connect.md)。 此外，請執行下列步驟。

[AZURE.INCLUDE [install the DC/OS CLI](../../includes/container-service-install-dcos-cli-include.md)]

## <a name="create-an-application-or-user-specific-marathon-service"></a>建立應用程式或特定使用者 Marathon 服務

開始建立 JSON 設定檔，以定義您想要建立的應用程式服務的名稱。 以下我們使用`marathon-alice`做為架構的名稱。 將檔案儲存為類似`marathon-alice.json`:

```json
{"marathon": {"framework-name": "marathon-alice" }}
```

接下來，請使用 DC/OS CLI 安裝 Marathon 執行個體在您的設定檔中設定的選項︰

```bash
dcos package install --options=marathon-alice.json marathon
```

您現在應該會看到您`marathon-alice`DC/OS 使用者介面的 [服務] 索引標籤中執行的服務。 會在 UI`http://<hostname>/service/marathon-alice/`如果您想要直接存取的地方。

## <a name="set-the-dcos-cli-to-access-the-service"></a>設定 DC/OS CLI 存取服務

您可以選擇設定存取此新的服務，設定您的 DC/OS CLI`marathon.url`屬性指向`marathon-alice`執行個體，如下所示︰

```bash
dcos config set marathon.url http://<hostname>/service/marathon-alice/
```

您也可以驗證的執行個體您 CLI 適用於針對的 Marathon `dcos config show` ] 命令。 您可以還原為使用您的主版 Marathon 服務命令`dcos config unset marathon.url`。
