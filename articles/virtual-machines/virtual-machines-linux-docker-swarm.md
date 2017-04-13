<properties
   pageTitle="使用上 Azure 廣域 docker 快速入門"
   description="說明如何建立群組 Vm Docker VM 副檔名，並使用廣域建立 Docker 叢集。"
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="squillace"
   manager="timlt"
   editor="tysonn"
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="01/04/2016"
   ms.author="rasquill"/>

# <a name="how-to-use-docker-with-swarm"></a>如何使用廣域 docker

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


本主題說明使用[docker](https://www.docker.com/) [廣域](https://github.com/docker/swarm)Azure 上建立廣域管理叢集的簡單方法。 建立四個虛擬機器中 Azure 屬於一 docker 主機的三個作為廣域管理員] 中，其中一個。 當您完成時，您可以使用廣域以查看叢集並開始使用 docker。 此外，Azure CLI 來電本主題中的使用的服務管理 (asm) 模式。 

> [AZURE.NOTE] 本主題會使用 docker 與廣域 Azure CLI*而不*使用**docker 電腦**才能顯示不同的工具如何一起運作，但是會保留獨立。 **docker 電腦**上有**-廣域**可讓您直接將節點新增至廣域使用**docker 電腦**的選項。 例如，請參閱[docker 電腦](https://github.com/docker/machine)的文件。 如果您未接來電**docker 電腦**針對 Azure Vm 執行，請參閱[如何使用 Azure docker-電腦](virtual-machines-linux-docker-machine.md)。

## <a name="create-docker-hosts-with-azure-virtual-machines"></a>建立 docker 主機與 Azure 虛擬機器

本主題會建立四個 Vm，但您可以使用您想要的任何數字。 通話與下列*&lt;密碼&gt;*由您選擇的密碼。

    azure vm docker create swarm-master -l "East US" -e 22 $imagename ops <password>
    azure vm docker create swarm-node-1 -l "East US" -e 22 $imagename ops <password>
    azure vm docker create swarm-node-2 -l "East US" -e 22 $imagename ops <password>
    azure vm docker create swarm-node-3 -l "East US" -e 22 $imagename ops <password>

當您完成時，您應該可以使用**azure vm 清單**以查看您的 Azure Vm:

    $ azure vm list | grep "swarm-[mn]"
    data:    swarm-master     ReadyRole           East US       swarm-master.cloudapp.net                               100.78.186.65
    data:    swarm-node-1     ReadyRole           East US       swarm-node-1.cloudapp.net                               100.66.72.126
    data:    swarm-node-2     ReadyRole           East US       swarm-node-2.cloudapp.net                               100.72.18.47  
    data:    swarm-node-3     ReadyRole           East US       swarm-node-3.cloudapp.net                               100.78.24.68  

## <a name="installing-swarm-on-the-swarm-master-vm"></a>安裝廣域廣域母片 VM 上

本主題會使用[從 docker 安裝的容器模型廣域文件](https://github.com/docker/swarm#1---docker-image)，但您也可以 SSH**廣域母片**。 在此模式中，為執行廣域 docker 容器下載**廣域**。 下方，我們執行此步驟*從使用 docker 我們膝上型電腦*連線到**廣域母片**VM 並判斷要使用 [叢集識別碼建立] 命令，**廣域建立**。 叢集 id 是如何**廣域**探索廣域群組的成員。 (您也可以複製存放庫並建立您自己，讓您 「 完全控制，並啟用偵錯。)

    $ docker --tls -H tcp://swarm-master.cloudapp.net:2376 run --rm swarm create
    Unable to find image 'swarm:latest' locally
    511136ea3c5a: Pull complete
    a8bbe4db330c: Pull complete
    9dfb95669acc: Pull complete
    0b3950daf974: Pull complete
    633f3d9a9685: Pull complete
    bba5f98a0414: Pull complete
    defbc1ab4462: Pull complete
    92d78d321ff2: Pull complete
    swarm:latest: The image you are pulling has been verified. Important: image verification is a tech preview feature and should not be relied on to provide security.
    Status: Downloaded newer image for swarm:latest
    36731c17189fd8f450c395db8437befd

最後一行是叢集識別碼。將其複製某個位置因為您將會使用一次當您加入節點 Vm 廣域母片，以建立 「 廣域 」。 在此範例中，叢集 id 是**36731c17189fd8f450c395db8437befd**。

> [AZURE.NOTE] 只，我們會使用我們本機 docker 安裝連線至**廣域母片**VM 中 Azure 和指示**廣域母片**下載、 安裝及執行 [**建立**] 命令，所傳回我們用途探索稍後我們叢集識別碼。
<!-- -->
> 若要確認此問題，請執行`docker -H tcp://` * &lt;hostname&gt; * ` images`清單容器和程序**廣域母片**上的比較的另一個節點 （因為我們在前一個廣域命令時發生搭配**-rm** 、 容器已移除，完成後，使用**docker ps-**不會傳回任何項目）。:


        $ docker --tls -H tcp://swarm-master.cloudapp.net:2376 images
        REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
        swarm               latest              92d78d321ff2        11 days ago         7.19 MB
        $ docker --tls -H tcp://swarm-node-1.cloudapp.net:2376 images
        REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
        $
<P />
>如果您熟悉**docker**，就會知道其他節點有任何項目，因為下載及尚未執行任何圖像。

## <a name="join-the-node-vms-to-our-docker-cluster"></a>節點 Vm 加入我們 docker 叢集

針對每個節點，列出使用 Azure CLI 的結束點資訊。 下面這是**廣域節點 1** docker 主機以取得節點的 docker 連接埠。

    $ azure vm endpoint list swarm-node-1
    info:    Executing command vm endpoint list
    + Getting virtual machines
    data:    Name    Protocol  Public Port  Private Port  Virtual IP      EnableDirectServerReturn  Load Balanced
    data:    ------  --------  -----------  ------------  --------------  ------------------------  -------------
    data:    docker  tcp       2376         2376          138.91.112.194  false                     No
    data:    ssh     tcp       22           22            138.91.112.194  false                     No
    info:    vm endpoint list command OK


使用**docker**和`-H`選項，指向您的節點 VM docker 用戶端加入您正在建立傳遞叢集識別碼] 和 [節點的 docker 連接埠群集節點 (後者使用**-位址**):

    $ docker --tls -H tcp://swarm-node-1.cloudapp.net:2376 run -d swarm join --addr=138.91.112.194:2376 token://36731c17189fd8f450c395db8437befd
    Unable to find image 'swarm:latest' locally
    511136ea3c5a: Pull complete
    a8bbe4db330c: Pull complete
    9dfb95669acc: Pull complete
    0b3950daf974: Pull complete
    633f3d9a9685: Pull complete
    bba5f98a0414: Pull complete
    defbc1ab4462: Pull complete
    92d78d321ff2: Pull complete
    swarm:latest: The image you are pulling has been verified. Important: image verification is a tech preview feature and should not be relied on to provide security.
    Status: Downloaded newer image for swarm:latest
    bbf88f61300bf876c6202d4cf886874b363cd7e2899345ac34dc8ab10c7ae924

看起來。 若要確認**廣域****廣域節點 1**我們輸入上執行︰

    $ docker --tls -H tcp://swarm-node-1.cloudapp.net:2376 ps -a
        CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS               NAMES
        bbf88f61300b        swarm:latest        "/swarm join --addr=   13 seconds ago      Up 12 seconds       2375/tcp            angry_mclean

重複叢集中的所有其他節點。 在此例中，我們執行的**廣域節點 2**及**廣域-節點-3**。

## <a name="begin-managing-the-swarm-cluster"></a>開始廣域叢集管理

    $ docker --tls -H tcp://swarm-master.cloudapp.net:2376 run -d -p 2375:2375 swarm manage token://36731c17189fd8f450c395db8437befd
    d7e87c2c147ade438cb4b663bda0ee20981d4818770958f5d317d6aebdcaedd5

然後您可以] 清單中查看您的節點叢集中︰

    ralph@local:~$ docker --tls -H tcp://swarm-master.cloudapp.net:2376 run --rm swarm list token://73f8bc512e94195210fad6e9cd58986f
    54.149.104.203:2375
    54.187.164.89:2375
    92.222.76.190:2375

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>後續步驟

前往您廣域上執行作業。 若要尋找靈感，請參閱[https://github.com/docker/swarm/](https://github.com/docker/swarm/)，或可能是[視訊](https://www.youtube.com/watch?v=EC25ARhZ5bI)。

<!-- links -->

[docker-machine-azure]: virtual-machines-linux-docker-machine.md
 
