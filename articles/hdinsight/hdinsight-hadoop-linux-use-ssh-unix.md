<properties
   pageTitle="使用 SSH 機碼具有 Linux 式 Hadoop Linux、 Unix，或 OS X |Microsoft Azure"
   description=" 您可以存取 Linux 型 HDInsight 使用安全殼層 (SSH)。 本文件提供在 Linux、 Unix 或 OS X 用戶端中使用 HDInsight SSH 資訊。"
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"
    tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/13/2016"
   ms.author="larryfr"/>

#<a name="use-ssh-with-linux-based-hadoop-on-hdinsight-from-linux-unix-or-os-x"></a>使用 SSH 具有 Linux 型 Hadoop HDInsight Linux、 Unix，或 OS X 上

> [AZURE.SELECTOR]
- [Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
- [Linux、 Unix、 OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

[安全殼層 (SSH)](https://en.wikipedia.org/wiki/Secure_Shell)可讓您從遠端執行使用命令列介面您 Linux 型 HDInsight 叢集上的作業。 本文件提供在 Linux、 Unix、 或 OS X 用戶端中使用 HDInsight SSH 資訊。

> [AZURE.NOTE] 在本文中的步驟假設您已使用 Linux、 Unix 或 OS X 用戶端。 如果您已安裝套件提供，可能會在 Windows 型用戶端上執行這些步驟`ssh`和`ssh-keygen`，例如[被 Ubuntu windows 上](https://msdn.microsoft.com/commandline/wsl/about)。
>
> 如果您沒有 SSH Windows 型用戶端上安裝，用於步驟中[使用 SSH 與 Linux 型 HDInsight (Hadoop) 從 Windows](hdinsight-hadoop-linux-use-ssh-windows.md)上安裝及使用 PuTTY 資訊。

##<a name="prerequisites"></a>必要條件

* **ssh-keygen**和**ssh** Linux、 Unix、 和 OS X 用戶端。 此公用程式通常會提供您的作業系統，或透過套件管理系統。

* 摩登的網頁瀏覽器支援 HTML5。

OR

* [Azure CLI](../xplat-cli-install.md)。

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)] 

##<a name="what-is-ssh"></a>什麼是 SSH？

SSH 是用於登入，並從遠端執行命令的遠端伺服器上的公用程式。 使用 Linux 型 HDInsight、 SSH 建立叢集 headnode 加密的連線，並提供您用來在命令中輸入命令列。 然後直接在伺服器上執行命令。

###<a name="ssh-user-name"></a>SSH 使用者名稱

SSH 使用者名稱是您用以驗證 HDInsight 叢集的名稱。 當您在叢集建立期間指定 SSH 使用者名稱時，此使用者建立在叢集中的所有節點上。 叢集建立之後，您可以使用此使用者名稱來連線至 HDInsight 叢集 headnodes。 從 headnodes，您可以然後連線至的個別工作者節點。

###<a name="ssh-password-or-public-key"></a>SSH password 或公開金鑰

SSH 使用者可以使用的密碼或公開金鑰進行驗證。 Password 是剛公開金鑰時產生的唯一識別您的密碼編譯金鑰組的一部分，讓的文字字串。

不過它需要其他步驟以產生金鑰並必須維護檔案，其中包含在安全的位置中的索引鍵的主要是比密碼更安全的。 如果任何人都可以用於存取重要檔案、 其存取您的帳戶。 或如果您遺失的主要檔案時，您將無法登入您的帳戶。

重要的一組所組成 （這傳送給 HDInsight 伺服器） 的公開金鑰與私密金鑰 （以保留您的用戶端電腦上。）當您連線至使用 SSH HDInsight 伺服器時、 SSH 用戶端會與伺服器進行驗證您的電腦上使用將私密金鑰。

##<a name="create-an-ssh-key"></a>建立 SSH 機碼

如果您打算使用 SSH 金鑰搭配您叢集，請使用下列資訊。 如果您打算使用密碼，您可以略過這一節。

1. 開啟 [終端機工作階段並使用下列命令來查看是否有任何現有的 SSH 機碼：

        ls -al ~/.ssh

    尋找下列檔案中列出的目錄。 這些是常見公用 SSH 機碼名稱。

    * 識別碼\_dsa.pub
    * 識別碼\_ecdsa.pub
    * 識別碼\_ed25519.pub
    * 識別碼\_rsa.pub

2. 如果您不想要使用現有的檔案，或有任何現有的 SSH 機碼，請使用下列命令以產生新的檔案：

        ssh-keygen -t rsa

    您將會提示輸入下列資訊：

    * 檔案位置-位置的預設是 ~/.ssh/id\_rsa。
    * 複雜密碼-將會提示您重新輸入此。

        > [AZURE.NOTE] 我們強烈建議您鍵使用安全的複雜密碼。 不過，如果您忘記複雜密碼，沒有方法加以復原。

    命令完成後，您將會有兩個新的檔案私密金鑰 (例如**識別碼\_rsa**) 與公開金鑰 (例如**識別碼\_rsa.pub**)。

##<a name="create-a-linux-based-hdinsight-cluster"></a>建立 Linux 型 HDInsight 叢集

當建立 Linux 型 HDInsight 叢集，您必須提供先前建立的公開金鑰。 Linux、 Unix 或 OS X 用戶端，有兩種方式可以建立 HDInsight 叢集：

* **Azure 入口網站**-使用 web 式入口網站建立叢集。

* **Mac、 Linux 及 Windows azure CLI** -使用命令列工具命令來建立叢集。

每種方法將會需要密碼或公開金鑰。 如需建立 Linux 型 HDInsight 叢集的完整資訊，請參閱 ＜[佈建 Linux 型 HDInsight 叢集](hdinsight-hadoop-provision-linux-clusters.md)。

###<a name="azure-portal"></a>Azure 入口網站

使用[Azure 入口網站]時[preview-portal]建立 Linux 型 HDInsight 叢集，您必須輸入**SSH 使用者名稱**]，並選取 [輸入**密碼**或**SSH 公開金鑰**。

如果您選取**SSH 公開金鑰**，可以將公開金鑰 （包含在**.pub**副檔名的檔案） 貼到__SSH PublicKey__ ] 欄位中，或選取 [瀏覽並選取 [公開金鑰檔案的 [__選取檔案__。

![表單要求公開金鑰的影像](./media/hdinsight-hadoop-linux-use-ssh-unix/ssh-key.png)

> [AZURE.NOTE] 主要檔案是只是以文字檔案。 內容應該會出現類似下列的：
> ```
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCelfkjrpYHYiks4TM+r1LVsTYQ4jAXXGeOAF9Vv/KGz90pgMk3VRJk4PEUSELfXKxP3NtsVwLVPN1l09utI/tKHQ6WL3qy89WVVVLiwzL7tfJ2B08Gmcw8mC/YoieT/YG+4I4oAgPEmim+6/F9S0lU2I2CuFBX9JzauX8n1Y9kWzTARST+ERx2hysyA5ObLv97Xe4C2CQvGE01LGAXkw2ffP9vI+emUM+VeYrf0q3w/b1o/COKbFVZ2IpEcJ8G2SLlNsHWXofWhOKQRi64TMxT7LLoohD61q2aWNKdaE4oQdiuo8TGnt4zWLEPjzjIYIEIZGk00HiQD+KCB5pxoVtp user@system
> ```

這會建立指定之使用者的登入的密碼或您所提供的公開金鑰。

###<a name="azure-command-line-interface-for-mac-linux-and-windows"></a>Mac、 Linux 及 Windows azure 的命令列介面

您可以使用[Mac、 Linux 及 Windows Azure CLI](../xplat-cli-install.md)使用建立的新叢集`azure hdinsight cluster create`命令。

如需使用此命令的詳細資訊，請參閱 ＜[使用自訂選項的 HDInsight 中佈建 Hadoop Linux 叢集](hdinsight-hadoop-provision-linux-clusters.md)。

##<a name="connect-to-a-linux-based-hdinsight-cluster"></a>連線至 Linux 型 HDInsight 叢集

從終端機工作階段，使用 SSH 命令來連線至叢集 headnode 所提供的地址和使用者名稱：

* **SSH 地址**-有可能會用來連線至使用 SSH 叢集中的兩個地址：

    * **連線至 headnode**︰ 叢集名稱，後面加上**-ssh.azurehdinsight.net**。 例如， **mycluster ssh.azurehdinsight.net**。
    
    * **連線至 edge 節點**： 如果您叢集上 HDInsight R 伺服器、 叢集也會包含使用**RServer.CLUSTERNAME.ssh.azurehdinsight.net**、 __CLUSTERNAME__所在的叢集名稱可以存取 edge 節點。

* **使用者名稱**-SSH 使用者提供的名稱建立叢集時。

下列範例會為使用者**我**連線至**mycluster**主要 headnode：

    ssh me@mycluster-ssh.azurehdinsight.net

如果您的使用者帳戶的密碼，系統會提示您輸入密碼。

如果您是使用受到保護的 SSH 金鑰與複雜密碼，系統會提示您輸入複雜密碼。 否則請 SSH 會嘗試自動驗證您的用戶端上使用其中一個本機的私密金鑰。

> [AZURE.NOTE] SSH 不會不會自動與正確的私密金鑰進行驗證，請使用**-i**參數並指定私密金鑰的路徑。 下列範例會載入私密金鑰從`~/.ssh/id_rsa`：
>
> `ssh -i ~/.ssh/id_rsa me@mycluster-ssh.azurehdinsight.net`

如果您要連接的 headnode 使用地址且未指定任何連接埠、 SSH 會使用預設連接埠 22，這會連線至主要 headnode HDInsight 叢集上。 如果您使用連接埠 23，您會連線至次要資料庫。 Headnodes 的詳細資訊，請參閱[的可用性及可靠性的中 HDInsight Hadoop 叢集](hdinsight-high-availability-linux.md)。

###<a name="connect-to-worker-nodes"></a>連線至工作者節點

工作者節點不是直接存取外部 Azure 資料中心，但他們可以從透過 SSH 叢集 headnode 存取。

如果您使用 SSH 金鑰來驗證您的使用者帳戶，您必須在您的用戶端上完成下列步驟：

1. 使用文字編輯器開啟`~/.ssh/config`。 如果這個檔案不存在，則可以建立輸入`touch ~/.ssh/config`在終端機。

2. 將以下內容新增至檔案。 以*CLUSTERNAME*取代 HDInsight 叢集的名稱。

        Host CLUSTERNAME-ssh.azurehdinsight.net
          ForwardAgent yes

    這會轉接 HDInsight 叢集的 SSH 代理程式。

3. 測試 SSH 代理程式轉寄使用終端機從下列命令：

        echo "$SSH_AUTH_SOCK"

    這應該傳回類似下列資訊：

        /tmp/ssh-rfSUL1ldCldQ/agent.1792

    若將不會傳回，則指出該**ssh-代理程式**未執行。 請洽詢您的作業系統說明文件上安裝及設定**ssh-代理程式**] 的特定步驟，或參閱[使用 ssh-代理程式與 ssh](http://mah.everybody.org/docs/ssh)。

4. 一旦確認該**ssh-代理程式**執行之後，請將 SSH 私密金鑰新增至代理程式使用下列：

        ssh-add ~/.ssh/id_rsa

    如果您的私密金鑰儲存在不同的檔案，取代`~/.ssh/id_rsa`具有檔案的路徑。

使用下列步驟來連線至您的叢集工作者節點。

> [AZURE.IMPORTANT] 如果您使用 SSH 金鑰來驗證您的帳戶，您必須完成上述步驟來確認運作轉寄該代理程式。

1. 使用 SSH 如前文所述連線至 HDInsight 叢集。

2. 一旦您已連線，請使用下列擷取清單中您叢集節點。 *ADMINPASSWORD*取代為您的叢集管理帳戶的密碼。 *CLUSTERNAME*取代為您的叢集的名稱。

        curl --user admin:ADMINPASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/hosts

    這會傳回在叢集節點的 JSON 格式的資訊包括`host_name`，其中包含每個節點的完整的網域名稱 (FQDN)。 以下是範例`host_name` **curl**命令所傳回的項目：

        "host_name" : "workernode0.workernode-0-e2f35e63355b4f15a31c460b6d4e1230.j1.internal.cloudapp.net"

3. 之後您想要連線至工作者節點清單，使用下列命令以伺服器從 SSH 工作階段開啟連線至工作者節點：

        ssh USERNAME@FQDN

    取代 SSH 使用者名稱和*FQDN*工作者節點 fqdn 的*使用者名稱*。 例如， `workernode0.workernode-0-e2f35e63355b4f15a31c460b6d4e1230.j1.internal.cloudapp.net`。

    > [AZURE.NOTE] 如果您是使用密碼來驗證您 SSH 工作階段會提示您重新輸入密碼。 如果您使用 SSH 機碼，不需任何提示完成連線。

4. 一旦建立工作階段之後會變更終端機提示`username@hn#-clustername`至`username@wk#-clustername`指出您已連線至工作者節點。 此時執行的命令會在工作者節點上執行。

4. 一旦完成工作者節點上執行動作，使用`exit`命令來關閉工作階段的工作者節點。 這會傳回您`username@hn#-clustername`提示。

## <a name="connect-to-a-domain-joined-hdinsight-cluster"></a>連線至已加入網域的 HDInsight 叢集

[已加入網域的 HDInsight](hdinsight-domain-joined-introduction.md) Hadoop HDInsight 中與整合 Kerberos。 因為 SSH 使用者不是使用中的 Direcotry 網域使用者、 此使用者帳戶不能 Hadoop 命令 SSH 命令介面執行已加入網域的叢集直接。 您必須先執行*kinit* 。 

**若要執行的登錄區查詢上使用 SSH 已加入網域的 HDInsight 叢集**

1. 連線至已加入網域的 HDInsight 叢集使用 SSH。  針對 instrocutions，請參閱[Connect to Linux 型 HDInsight 叢集。](#connect-to-a-linux-based-hdinsight-cluster)
2. 執行 kinit。 它會詢問您的網域使用者名稱與網域使用者密碼。 更多資訊上設定網域使用者的已加入網域的 HDInsight 叢集，請參閱[設定網域加入 HDInisight 叢集](hdinsight-domain-joined-configure.md)。

    ![HDInsight Hadoop 網域加入 kinit](./media/hdinsight-hadoop-linux-use-ssh-unix/hdinsight-domain-joined-hadoop-kinit.png)
3. 開啟由主控台輸入登錄區：

        hive

    然後您可以執行登錄區命令。

##<a name="add-more-accounts"></a>新增多個帳戶

1. 產生新的公開金鑰與私密金鑰的新的使用者帳戶，[建立 SSH 金鑰](#create-an-ssh-key-optional)小節所述。

    > [AZURE.NOTE] 將私密金鑰也應該產生使用者要用來連線至該叢集，或其可安全地建立後轉接至這類用戶端的用戶端上。

1. 從叢集 SSH 工作階段，新增新使用者利用下列命令：

        sudo adduser --disabled-password <username>

    這會建立新的使用者帳戶，但會停用密碼驗證。

2. 建立目錄和檔案使用下列命令來保留機碼：

        sudo mkdir -p /home/<username>/.ssh
        sudo touch /home/<username>/.ssh/authorized_keys
        sudo nano /home/<username>/.ssh/authorized_keys

3. Nano 編輯器開啟時，複製並貼上新的使用者帳戶的公開金鑰的內容中。 最後，使用**Ctrl X**來儲存檔案並結束編輯器。

    ![nano 編輯器與範例機碼的圖像](./media/hdinsight-hadoop-linux-use-ssh-unix/nano.png)

4. 若要變更為新的使用者帳戶的.ssh 資料夾及內容的擁有權使用下列命令：

        sudo chown -hR <username>:<username> /home/<username>/.ssh

5. 您現在應該要與新的使用者帳戶和私密金鑰的伺服器進行驗證。

##<a id="tunnel"></a>SSH 通道

SSH 可用來建立通道本機要求，例如 web 要求，到 HDInsight 叢集。 要求將會路由傳送至所要求資源好像有郵件 HDInsight 叢集 headnode 上。

> [AZURE.IMPORTANT] SSH 通道是用於存取 web UI 一些 Hadoop services 的需求。 例如，[工作歷程記錄 UI] 或 [資源管理員 UI 僅可使用 SSH 通道。

如需建立及使用 SSH 通道的詳細資訊，請參閱[使用 SSH 通道存取 Ambari web 使用者介面、 ResourceManager、 JobHistory、 NameNode、 Oozie、 及使用者介面的其他網頁](hdinsight-linux-ambari-ssh-tunnel.md)。

##<a name="next-steps"></a>後續步驟

既然您了解如何使用 SSH 金鑰進行驗證，了解如何使用 MapReduce 與 Hadoop HDInsight 上。

* [使用 HDInsight 登錄區](hdinsight-use-hive.md)

* [搭配 HDInsight Pig](hdinsight-use-pig.md)

* [使用 HDInsight MapReduce 工作](hdinsight-use-mapreduce.md)

[preview-portal]: https://portal.azure.com/
