<properties
   pageTitle="Hadoop SSH 鍵使用 Linux 為基礎的叢集，從 Windows 上 |Microsoft Azure"
   description="瞭解如何建立和使用 SSH 鍵 Linux 型 HDInsight 叢集進行驗證。 從 Windows 型的用戶端連線叢集使用 PuTTY SSH 用戶端。"
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
   ms.date="08/30/2016"
   ms.author="larryfr"/>

#<a name="use-ssh-with-linux-based-hadoop-on-hdinsight-from-windows"></a>使用上從 Windows HDInsight Linux 為基礎的 Hadoop SSH

> [AZURE.SELECTOR]
- [Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
- [Linux，Unix，OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

[安全命令介面 (SSH)](https://en.wikipedia.org/wiki/Secure_Shell)可讓您從遠端執行作業 Linux 型 HDInsight 叢集使用命令列介面。 這份文件提供使用 PuTTY SSH 用戶端連線至 HDInsight Windows 型的用戶端的相關資訊。

> [AZURE.NOTE] 本文中的步驟假設您使用的 Windows 型的用戶端。 如果您使用 Linux、 Unix 或 OS X 的用戶端，請參閱[使用 SSH Linux 為基礎的 Hadoop HDInsight Linux、 Unix，或 OS X 上使用](hdinsight-hadoop-linux-use-ssh-unix.md)。
>
> 如果您有 Windows 10，而且[在 Windows 上 Ubuntu 被](https://msdn.microsoft.com/commandline/wsl/about)使用，您可以[使用 SSH Linux 為基礎的 Hadoop HDInsight Linux、 Unix，或 OS X 上的](hdinsight-hadoop-linux-use-ssh-unix.md)文件中使用的步驟。

##<a name="prerequisites"></a>必要條件

* **PuTTY**和**PuTTYGen**適用於 Windows 型的用戶端。 這些公用程式，可從[http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)。

* 新的網頁瀏覽器支援 html 5。

OR

* [Azure CLI](../xplat-cli-install.md)。

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)] 

##<a name="what-is-ssh"></a>什麼是 SSH？

SSH 是公用程式的登入，並從遠端執行遠端伺服器上的命令。 使用 Linux 型 HDInsight，SSH 會建立叢集主節點的加密的連線，並提供您用來在命令中輸入的命令列。 然後直接在伺服器上執行命令。

###<a name="ssh-user-name"></a>SSH 使用者名稱

SSH 使用者名稱是您使用 HDInsight 叢集驗證的名稱。 當您在叢集建立期間指定 SSH 使用者名稱時，在叢集所有節點上建立的使用者。 叢集建立之後，您可以使用這個使用者名稱，以連線至 HDInsight 叢集標頭節點。 從標頭的節點，您可以再連線至個別工作者節點。

###<a name="ssh-password-or-public-key"></a>SSH 密碼或公開金鑰

SSH 使用者可以使用密碼或公開金鑰進行驗證。 密碼不只是公開金鑰時產生的唯一識別您的密碼編譯組的一部分，讓的文字字串。

索引鍵是比密碼更安全，但是它需要額外步驟才能產生金鑰，您必須維護檔案包含在安全的位置鍵。 如果任何人取得金鑰檔案的存取權，他們存取您的帳戶。 或者，如果您遺失了重要的檔案，您會無法登入您的帳戶。

金鑰組包含公開金鑰 （會傳送至 HDInsight 伺服器） 和私密金鑰 （這會保留在您的用戶端電腦上。）當您連線至 HDInsight 伺服器使用 SSH 時，SSH 用戶端會與伺服器驗證您的電腦上使用私密金鑰。

##<a name="create-an-ssh-key"></a>建立 SSH 鍵

如果您打算使用叢集 SSH 鍵，請使用下列資訊。 如果您打算使用密碼，您可以略過此節。

1. 開啟 PuTTYGen。

2. **，產生的類型**，選取**SSH 2 RSA**，然後按 [**產生]**。

    ![PuTTYGen 介面](./media/hdinsight-hadoop-linux-use-ssh-windows/puttygen.png)

3. 移動滑鼠進度列下方區域中，直到列填滿。 移動滑鼠，就會產生隨機會用來產生索引鍵的資料。

    ![移動滑鼠](./media/hdinsight-hadoop-linux-use-ssh-windows/movingmouse.png)

    一旦已產生金鑰，將會顯示公開金鑰。

4. 增加安全性，您可以在 [**索引鍵複雜密碼**] 欄位中，輸入複雜密碼，然後再輸入**複雜確認密碼**] 欄位中的 [相同的值。

    ![複雜密碼](./media/hdinsight-hadoop-linux-use-ssh-windows/key.png)

    > [AZURE.NOTE] 我們強烈建議您鍵使用安全的複雜密碼。 不過，如果您忘記複雜密碼，就是無法復原。

5. 按一下 [**儲存私密金鑰**儲存至**.ppk**檔案的金鑰。 此按鍵將會用於驗證 Linux 型 HDInsight 叢集。

    > [AZURE.NOTE] 您應該在安全的位置，儲存此按鍵時，可以用來存取您的 Linux 型 HDInsight 叢集。

6. 按一下 [**儲存公開金鑰**儲存為**.txt**檔案的金鑰。 這個選項可讓您日後公開金鑰當您建立其他 Linux 型 HDInsight 叢集。

    > [AZURE.NOTE] 公開金鑰也會顯示在頂端 PuTTYGen。 您可以以滑鼠右鍵按一下此欄位與複製值]，然後貼到表單時使用 Azure 入口網站建立叢集。

##<a name="create-a-linux-based-hdinsight-cluster"></a>建立 Linux 型 HDInsight 叢集

當建立 Linux 型 HDInsight 叢集，您必須提供公開金鑰先前所建立。 從 Windows 型的用戶端，有兩種方式來建立 Linux 型 HDInsight 叢集︰

* **Azure 入口網站**-會用來建立叢集的 web 式入口網站。

* **For Mac、 Linux 和 Windows azure CLI** -會用來建立叢集命令列的命令。

這些方法會要求公開金鑰。 完成建立 Linux 型 HDInsight 叢集的詳細資訊，請參閱 <<c0>佈建 Linux 型 HDInsight 叢集。

###<a name="azure-portal"></a>Azure 入口網站

使用[Azure 入口網站]時[preview-portal]建立 Linux 型 HDInsight 叢集，您必須輸入**SSH 使用者名稱**，並選取以輸入的**密碼**或**SSH 公用鍵**。

如果您選取**SSH 公用鍵**時，您可以 [貼上公開金鑰 (顯示在__公開金鑰貼入 OpenSSH 授權\_鍵檔案__PuttyGen，在功能變數) 到__SSH PublicKey__ ] 欄位中，或選取 [__選取檔案__瀏覽並選取 [包含公開金鑰的檔案。

![公開金鑰要求表單的圖像](./media/hdinsight-hadoop-linux-use-ssh-windows/ssh-key.png)

這會指定的使用者的登入，並且可密碼驗證或 SSH 金鑰驗證。

###<a name="azure-command-line-interface-for-mac-linux-and-windows"></a>For Mac、 Linux 和 Windows azure 命令列介面

您可以藉由建立新的叢集使用[For Mac、 Linux 和 Windows Azure CLI](../xplat-cli-install.md) `azure hdinsight cluster create` ] 命令。

如需有關如何使用此命令的詳細資訊，請參閱[佈建 Hadoop Linux 叢集中 HDInsight 使用自訂的選項](hdinsight-hadoop-provision-linux-clusters.md)。

##<a name="connect-to-a-linux-based-hdinsight-cluster"></a>連線到 Linux 型 HDInsight 叢集

1. 開啟 PuTTY。

    ![putty 介面](./media/hdinsight-hadoop-linux-use-ssh-windows/putty.png)

2. 如果您在建立您的使用者帳戶時，您就會提供 SSH 鍵，您必須執行下列步驟來選取叢集驗證時使用的私人機碼︰

    在**類別**中，展開**連線**，請展開**SSH**，然後選取**驗證**。 最後，按一下 [**瀏覽**並選取包含您的私密金鑰.ppk 檔案。

    ![putty 介面，請選取 [私密金鑰](./media/hdinsight-hadoop-linux-use-ssh-windows/puttykey.png)

3. 在 [**類別**中，選取 [**工作階段**]。 從**您 PuTTY 工作階段的基本選項**] 畫面中，輸入 SSH 地址 HDInsight 伺服器**主機名稱 （或 IP 位址）** ] 欄位中。 有兩個可能的 SSH 位址叢集連線時，您可以使用︰

    * __不對節點位址__︰ 若要連線到叢集主節點，請使用您叢集的名稱，然後**-ssh.azurehdinsight.net**。 例如， **mycluster ssh.azurehdinsight.net**。
    
    * __邊緣節點位址__︰ 如果您連線到 R 伺服器 HDInsight 叢集上，您可以連線至 R 伺服器邊緣節點使用地址__RServer.CLUSTERNAME.ssh.azurehdinsight.net__，其中 CLUSTERNAME 是叢集的名稱。 例如， __RServer.mycluster.ssh.azurehdinsight.net__。

    ![putty 介面 ssh 輸入地址](./media/hdinsight-hadoop-linux-use-ssh-windows/puttyaddress.png)

4. 若要儲存以供日後使用的連線資訊，請輸入此連線下**儲存的工作階段**的名稱，然後按一下 [**儲存**。 連線會新增至清單中儲存的工作階段。

5. 按一下 [**開啟**連接到叢集。

    > [AZURE.NOTE] 如果這是您已連線至叢集第一次，您會收到的安全性提醒中。 這是標準。 選取 [ **]**以快取伺服器的 RSA2 金鑰以繼續。

6. 出現提示時，輸入當您建立叢集您輸入的使用者。 如果您為使用者提供密碼，系統會提示您輸入密碼，也。

> [AZURE.NOTE] 上述步驟假設您使用的會連線到 HDInsight 叢集主要 headnode 的連接埠 22。 如果您使用連接埠 23，您會連線至次要。 標頭節點的詳細資訊，請參閱[可用性及可靠性 Hadoop 叢集 HDInsight 中](hdinsight-high-availability-linux.md)。

###<a name="connect-to-worker-nodes"></a>連線至工作者節點

工作者節點不是直接存取外部 Azure 資料中心，但可以從叢集主節點透過 SSH 加以存取。

如果您在建立您的使用者帳戶時，您就會提供 SSH 鍵，您必須執行下列步驟，以驗證叢集，如果您要連線至工作者節點時，使用私密金鑰。

1. 從[http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)安裝擠進去。 此工具用來 PuTTY 的快取 SSH 金鑰。

2. 執行擠進去。 它會最小化至狀態匣中的圖示。 以滑鼠右鍵按一下 [] 圖示，然後選取 [**新增索引鍵**。

    ![新增索引鍵](./media/hdinsight-hadoop-linux-use-ssh-windows/addkey.png)

3. 瀏覽對話方塊出現時，選取.ppk 檔案包含索引鍵，然後再按一下 [**開啟**]。 如此會將金鑰加入擠進去，會提供給 PuTTY 叢集連線時。

    > [AZURE.IMPORTANT] 如果您使用 SSH 鍵來保護您的帳戶，您必須先完成上述步驟，才能您無法連線至工作者節點。

4. 開啟 PuTTY。

5. 如果您使用 SSH 鍵來進行驗證，在 [**類別**] 區段中，展開**連線**、 展開**SSH**，，然後選取**驗證**。

    在 [**驗證參數**] 區段中，啟用**允許代理程式轉接**。 這個選項可讓 PuTTY 連線至工作者節點時，自動將透過連接的憑證驗證傳遞到叢集主節點。

    ![允許轉接代理程式](./media/hdinsight-hadoop-linux-use-ssh-windows/allowforwarding.png)

6. 連線到叢集，如先前所述。 如果您使用 SSH 鍵驗證時，您不需要選取鍵-新增至擠進去的 SSH 鍵將會用於驗證叢集。

7. 在建立連線之後，請使用下列擷取叢集中節點的清單。 *ADMINPASSWORD*換成您叢集的管理員帳戶的密碼。 *CLUSTERNAME*換成您叢集的名稱。

        curl --user admin:ADMINPASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/hosts

    這會傳回資訊 JSON 格式節點叢集，包括`host_name`，其中包含每個節點的完整的網域名稱 (FQDN)。 以下是範例`host_name` **curl**命令傳回的項目︰

        "host_name" : "workernode0.workernode-0-e2f35e63355b4f15a31c460b6d4e1230.j1.internal.cloudapp.net"

8. 一旦您有您要連線的工作者節點的清單，使用 PuTTY 工作階段的 [動作] 命令來開啟工作者節點的連線︰

        ssh USERNAME@FQDN

    取代 SSH 使用者名稱和*FQDN*工作者節點的 FQDN 與*使用者名稱*。 例如， `workernode0.workernode-0-e2f35e63355b4f15a31c460b6d4e1230.j1.internal.cloudapp.net`。

    > [AZURE.NOTE] 如果您是使用密碼以驗證您的 SSH 工作階段，系統會提示您再次輸入密碼。 如果您使用 SSH 鍵時，應該會沒有任何提示完成連線。

9. 一旦建立工作階段，將會變更您 PuTTY 工作階段的提示`username@hn#-clustername`至`username@wn#-clustername`，表示您已連線到工作者節點。 您現在執行任何命令會執行工作者節點。

10. 一旦您完成工作者節點上執行動作時，使用`exit`關閉工作階段的工作者節點的命令。 這會傳回您`username@hn#-clustername`提示。

##<a name="add-more-accounts"></a>新增更多帳戶

如果您要將多個帳戶新增至您的叢集，請執行下列步驟︰

1. 產生新的公用金鑰和私密金鑰的新使用者帳戶，先前所述。

2. 從叢集 SSH 工作階段，新增新的使用者使用下列命令︰

        sudo adduser --disabled-password <username>

    這會建立新的使用者帳戶，但會停用密碼驗證。

3. 建立目錄及檔案至按住鍵使用下列命令︰

        sudo mkdir -p /home/<username>/.ssh
        sudo touch /home/<username>/.ssh/authorized_keys
        sudo nano /home/<username>/.ssh/authorized_keys

4. Nano 編輯器] 中開啟時，複製並貼上內容中的新使用者帳戶的公開金鑰。 最後，使用**Ctrl X**儲存檔案，然後結束編輯器]。

    ![使用範例金鑰 nano 編輯器的圖像](./media/hdinsight-hadoop-linux-use-ssh-windows/nano.png)

5. 若要變更新的使用者帳戶.ssh 資料夾和內容的擁有權，使用下列命令︰

        sudo chown -hR <username>:<username> /home/<username>/.ssh

6. 您現在應該使用新的使用者帳戶和私密金鑰伺服器進行驗證。

##<a id="tunnel"></a>SSH 通道

SSH 可以用於通道本機的要求，例如網頁的要求，HDInsight 叢集。 如果已在 HDInsight 叢集來自，要求然後傳送要求的資源。

> [AZURE.IMPORTANT] SSH 通道是用來存取網站使用者介面上的某些 Hadoop 服務要求。 例如，同時工作歷程記錄 UI 或資源管理員使用者介面只能使用 SSH 通道。

如需有關建立和使用 SSH 通道的詳細資訊，請參閱[使用 SSH 通道存取 Ambari 網頁 UI、 ResourceManager、 JobHistory、 NameNode、 Oozie 及其他網站使用者介面的](hdinsight-linux-ambari-ssh-tunnel.md)。

##<a name="next-steps"></a>後續步驟

瞭解如何使用 SSH 金鑰進行驗證，瞭解如何使用 MapReduce Hadoop HDInsight 上使用。

* [使用 HDInsight 的登錄區](hdinsight-use-hive.md)

* [使用 HDInsight 的豬](hdinsight-use-pig.md)

* [使用 HDInsight MapReduce 工作](hdinsight-use-mapreduce.md)

[preview-portal]: https://portal.azure.com/
