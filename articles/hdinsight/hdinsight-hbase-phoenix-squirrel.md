<properties 
   pageTitle="使用 Apache 鳳凰和松鼠中 HDInsight |Microsoft Azure" 
   description="瞭解如何使用 Apache 鳳凰中 HDInsight，以及如何安裝並設定您連線至 HBase 中叢集 HDInsight 工作站松鼠。" 
   services="hdinsight" 
   documentationCenter="" 
   authors="mumian" 
   manager="jhubbard" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/02/2016"
   ms.author="jgao"/>

# <a name="use-apache-phoenix-and-squirrel-with-windows-based-hbase-clusters-in-hdinsight"></a>使用 Windows 型 HBase 叢集中 HDinsight Apache 鳳凰與松鼠  

瞭解如何使用[Apache 鳳凰](http://phoenix.apache.org/)中 HDInsight，以及如何安裝並設定您連線至 HBase 中叢集 HDInsight 工作站松鼠。 如需有關鳳凰的詳細資訊，請參閱[鳳凰在 15 分鐘之內](http://phoenix.apache.org/Phoenix-in-15-minutes-or-less.html)。 鳳凰文法檢查]，請參閱[鳳凰文法](http://phoenix.apache.org/language/index.html)。

>[AZURE.NOTE] HDInsight 鳳凰版本資訊，請參閱[HDInsight 所提供之 Hadoop 叢集版本中的新功能？][hdinsight-versions].
>
> 這份文件中的資訊是 Windows 型 HDInsight 叢集特定項目。 使用鳳凰 Linux 型 HDInsight 上的資訊，請參閱[使用 Apache 鳳凰 Linux 型 HBase 叢集 HDinsight 中](hdinsight-hbase-phoenix-squirrel-linux.md)。

##<a name="use-sqlline"></a>使用 SQLLine
[SQLLine](http://sqlline.sourceforge.net/)是執行 SQL 命令列公用程式。 

###<a name="prerequisites"></a>必要條件
您可以使用 SQLLine 之前，您必須具備下列項目︰

- **在 HDInsight HBase 叢集**。 如需的資訊佈建 HBase 叢集，請參閱[快速入門中 HDInsight Apache HBase][hdinsight-hbase-get-started]。
- **連線到 HBase 叢集透過遠端桌面通訊協定**。 如需相關指示，請參閱[HDInsight 使用 Azure 傳統入口網站中的管理 Hadoop 叢集][hdinsight-manage-portal]。

**若要找出 [主機名稱**

1. 從桌面開啟**Hadoop 命令列**。
2. 執行下列命令以取得 DNS 後置字元︰

        ipconfig

    寫下**連線特定 DNS 尾碼**。 例如， *myhbasecluster.f5.internal.cloudapp.net*。 當您連線到 HBase 叢集時，您必須連線到其中一個動物園使用 FQDN。 每個 HDInsight 叢集有 3 動物園管理員。 這些是*zookeeper0*、 *zookeeper1*，以及*zookeeper2*。 FQDN 會類似*zookeeper2.myhbasecluster.f5.internal.cloudapp.net*。

**若要使用 SQLLine**

1. 從桌面開啟**Hadoop 命令列**。
2. 執行下列命令以開啟 SQLLine:

        cd %phoenix_home%\bin
        sqlline.py [The FQDN of one of the Zookeepers]

    ![hdinsight hbase 鳳凰 sqlline][hdinsight-hbase-phoenix-sqlline]

    使用範例中的命令︰

        CREATE TABLE Company (COMPANY_ID INTEGER PRIMARY KEY, NAME VARCHAR(225));
        
        !tables
        
        UPSERT INTO Company VALUES(1, 'Microsoft');
        
        SELECT * FROM Company;

如需詳細資訊，請參閱[SQLLine 手動](http://sqlline.sourceforge.net/#manual)及[鳳凰文法檢查](http://phoenix.apache.org/language/index.html)。


















##<a name="use-squirrel"></a>使用松鼠

[松鼠 SQL 用戶端](http://squirrel-sql.sourceforge.net/)是資料庫的圖形的 Java 程式可讓您檢視的 JDBC 相容結構、 瀏覽表格中的資料、 議題 SQL 命令等。可以用於連線至 Apache 鳳凰 HDInsight 上。

本節說明如何安裝和設定松鼠工作站到 HBase 叢集 HDInsight 中透過 VPN 連線。 

###<a name="prerequisites"></a>必要條件

程序之前，您必須具備下列項目︰

- 部署到 DNS 虛擬機器 Azure 虛擬網路 HBase 叢集。  如需相關指示，請參閱[Azure 虛擬網路上的佈建 HBase 叢集][hdinsight-hbase-provision-vnet]。 

    >[AZURE.IMPORTANT] 您必須安裝 DNS 伺服器虛擬網路。 如需相關指示，請參閱[設定兩個 Azure 虛擬網路之間的 DNS](hdinsight-hbase-geo-replication-configure-dns.md)

- 取得 HBase 叢集叢集連線特定 DNS 尾碼。 若要取得其 RDP 到叢集，然後再執行 [IPConfig。  DNS 尾碼非常類似︰

        myhbase.b7.internal.cloudapp.net
- 下載並安裝[Microsoft Visual Studio Express 2013 Windows 桌面版](https://www.visualstudio.com/products/visual-studio-express-vs.aspx)工作站。 您必須 makecert 從套件中建立您的憑證。  
- 下載並安裝[Java 執行階段環境](http://www.oracle.com/technetwork/java/javase/downloads/jre7-downloads-1880261.html)工作站。  松鼠 SQL 3.0 及更新版本的用戶端版本需要 JRE 1.6 或更新版本。  


###<a name="configure-a-point-to-site-vpn-connection-to-the-azure-virtual-network"></a>設定點為網站 VPN 連線至 Azure 虛擬網路

有 3 個步驟設定點為網站 VPN 連線︰

1. [設定虛擬網路和動態路由閘道器](#Configure-a-virtual-network-and-a-dynamic-routing-gateway)
2. [建立自己的憑證](#Create-your-certificates)
3. [設定您的 VPN 用戶端](#Configure-your-VPN-client)

如需詳細資訊，請參閱[設定 Azure 虛擬網路點為網站 VPN 連線](../vpn-gateway/vpn-gateway-point-to-site-create.md)。

#### <a name="configure-a-virtual-network-and-a-dynamic-routing-gateway"></a>設定虛擬網路和動態路由閘道器

確保您有佈建 Azure 虛擬網路中的 HBase 叢集 （請參閱本節的先決條件）。 下一步是點為網站連線的設定。

**若要設定點為網站連線**

1. [Azure 傳統入口網站]登入[azure-portal]。
2. 在左側，按一下 [**網路**]。
3. 按一下您所建立的虛擬網路 (請參閱[Azure 虛擬網路上的佈建 HBase 叢集][hdinsight-hbase-provision-vnet])。
4. 按一下 [從上方的 [**設定**]。
5. 在**點為網站連線**] 區段中，選取 [**設定點為網站連線**]。 
6. 設定**啟動 IP** **CIDR**指定 VPN 用戶端將會收到連線時的 IP 位址的 IP 位址範圍。 範圍不能重疊任何位於您的內部網路和您要連結至 Azure 虛擬網路上的範圍。 例如。 如果您選取的虛擬網路 10.0.0.0/20，您可以選取 10.1.0.0/24 用戶端位址空間。 請參閱[點為網站連線][vnet-point-to-site-connectivity]如需詳細資訊] 頁面。
7. 在虛擬網路位址空格] 區段中，按一下 [**新增閘道器子網路**]。
7. 按一下 [頁面底端的 [**儲存**]。
8. 按一下 [ **]**以確認變更。 請等待系統已完成進行變更，再繼續進行下一個程序。


**若要建立動態路由閘道器**

1. 從 Azure 傳統入口網站中，按一下 [從頁面頂端的**儀表板**]。
2. 按一下頁面底部的**建立閘道器**。
3. 按一下 [ **]**以確認。 請等待建立閘道器。
4. 按一下 [從上方的 [**儀表板**]。  您會看到虛擬網路視覺化的圖表︰

    ![Azure 虛擬網路點為網站虛擬圖表][img-vnet-diagram] 

    下圖顯示 0 的用戶端連線。 虛擬網路的連線之後，數字會更新為其中一個。 

#### <a name="create-your-certificates"></a>建立自己的憑證

若要建立 X.509 憑證的其中一個方法是使用隨附[Microsoft Visual Studio Express 2013 Windows 桌面版](https://www.visualstudio.com/products/visual-studio-express-vs.aspx)的憑證建立工具 (makecert.exe)。 


**若要建立自我簽署的根憑證**

1. 從您的工作站開啟命令提示字元視窗。
2. 瀏覽至 [Visual Studio 工具] 資料夾。 
3. 下列命令，在下面的範例會建立並安裝在您的工作站的個人憑證存放區的根憑證並也建立對應.cer 檔案您可以稍後再上傳至 Azure 傳統入口網站。 

        makecert -sky exchange -r -n "CN=HBaseVnetVPNRootCertificate" -pe -a sha1 -len 2048 -ss My "C:\Users\JohnDole\Desktop\HBaseVNetVPNRootCertificate.cer"

    變更您想要.cer 檔案位於，其中 HBaseVnetVPNRootCertificate 是您想要使用的憑證名稱的目錄。 

    不關閉命令提示字元。  您必須將其在下一個程序。

    >[AZURE.NOTE] 因為您已建立將會產生用戶端憑證根憑證，您可能會想要匯出這個憑證，以及其私密金鑰，並將其儲存至安全的位置可能已復原。 

**若要建立的用戶端憑證**

- 從相同的命令提示字元 （必須是您所建立的根憑證所在的電腦上。 用戶端憑證必須產生從根憑證），執行下列命令︰

        makecert.exe -n "CN=HBaseVnetVPNClientCertificate" -pe -sky exchange -m 96 -ss My -in "HBaseVnetVPNRootCertificate" -is my -a sha1

    HBaseVnetVPNRootCertificate 是根憑證名稱。  它必須符合的根憑證的名稱。  

    根憑證和用戶端憑證會儲存在您電腦上的個人憑證存放區中。 使用 certmgr.msc 驗證。

    ![Azure 虛擬網路點為網站 vpn 憑證][img-certificate]

    用戶端憑證必須在每個您想要連線至虛擬網路的電腦上安裝。 我們建議您建立唯一的用戶端每個您想要連線至虛擬網路的電腦的憑證。 若要匯出的用戶端憑證，請使用 certmgr.msc。 

**若要上傳至 Azure 傳統入口網站的根憑證**

1. 從 Azure 傳統入口網站中，按一下左側的 [**網路**]。
2. 按一下 [虛擬 HBase 叢集部署到的網路]。
3. 按一下 [從上方的 [**憑證**]。
4. 從下方，按一下 [**上傳**，指定您上次之前的程序中所建立的根憑證檔案。 請等待您使用的是匯入的憑證。
5. 按一下頂端上的 [**儀表板**]。  虛擬圖表會顯示的狀態。


#### <a name="configure-your-vpn-client"></a>設定您的 VPN 用戶端



**若要下載並安裝用戶端 VPN 套件**

1. 從儀表板頁面的虛擬網路，在 [快速規則] 區段中，按一下 [**下載 64 位元的用戶端 VPN 套件**或**下載 32 位元的用戶端 VPN 套件**根據您工作站作業系統版本。
2. 按一下 [安裝套件的 [**執行**]。
3. 出現安全性提示時，按一下 [**其他資訊**]，然後按一下**繼續執行**。
4. 按一下 [**是**按兩次。

**連線至 VPN**

1. 在您的工作站桌面上，按一下工作列上的 [網路] 圖示。 您應該使用您的虛擬網路名稱，請參閱 VPN 連線。
2. 按一下 VPN 連線名稱。
3. 按一下 [**連線**]。

**若要測試 VPN 連線和網域名稱解析**

- 從工作站，開啟 [命令提示字元和偵測 （ping） 的其中一個指定 HBase 叢集的尾碼下列名稱是 myhbase.b7.internal.cloudapp.net:

        zookeeper0.myhbase.b7.internal.cloudapp.net
        zookeeper0.myhbase.b7.internal.cloudapp.net
        zookeeper0.myhbase.b7.internal.cloudapp.net
        headnode0.myhbase.b7.internal.cloudapp.net
        headnode1.myhbase.b7.internal.cloudapp.net
        workernode0.myhbase.b7.internal.cloudapp.net

###<a name="install-and-configure-squirrel-on-your-workstation"></a>安裝和設定松鼠工作站

**若要安裝松鼠**

1. 從[http://squirrel-sql.sourceforge.net/#installation](http://squirrel-sql.sourceforge.net/#installation)下載松鼠 SQL 用戶端 jar 檔案。
2. 開啟/執行 jar 檔案。 需要[Java 執行階段環境](http://www.oracle.com/technetwork/java/javase/downloads/jre7-downloads-1880261.html)。
3. 按一下 [**下一步**] 兩次。
4. 指定您有 [寫入] 權限的路徑，然後按一下 [**下一步**。
    >[AZURE.NOTE] 預設的安裝資料夾位於 C:\Program Files\squirrel sql 3.6 資料夾。  若要撰寫此路徑，請安裝程式必須授與系統管理員權限。 您可以開啟命令提示字元以系統管理員身分，瀏覽至 Java 的回收筒] 資料夾，然後再執行 
    >
    >     java.exe -jar [the path of the SQuirreL jar file] 
5. 按一下**[確定**] 以確認建立目標目錄。
6. 安裝基底和標準封包為預設設定。  按一下 [**下一步**]。
7. **按兩次，**然後再按一下 [**完成**]。


**若要安裝鳳凰驅動程式**

鳳凰驅動程式 jar 檔案位於 HBase 叢集。 路徑是類似下列根據版本︰

    C:\apps\dist\phoenix-4.0.0.2.1.11.0-2316\phoenix-4.0.0.2.1.11.0-2316-client.jar
您需要將其複製到 [松鼠安裝資料夾] 下工作站 /lib 路徑。  最簡單的方法是將叢集，RDP，然後再使用檔案複製/貼上 （CTRL + c 鍵與 CTRL + V），將它複製到您的工作站。

**若要新增松鼠鳳凰驅動程式**

1. 從工作站開啟松鼠 SQL 用戶端。
2. 按一下左側的 [**驅動程式**] 索引標籤。
2. 從 [**驅動程式**] 功能表中，按一下 [**新驅動程式**]。
3. 輸入下列資訊︰

    - **名稱**︰ 鳳凰
    - **範例 URL**: jdbc:phoenix:zookeeper2.contoso-hbase-eu.f5.internal.cloudapp.net
    - **類別名稱**︰ org.apache.phoenix.jdbc.PhoenixDriver

    >[AZURE.WARNING] 使用者範例 URL 中的所有小寫。 您可以使用其完整動物園管理員仲裁以防其中已關閉。  主機名稱是 zookeeper0 zookeeper1，與 zookeeper2。

    ![HDInsight HBase 鳳凰松鼠驅動程式][img-squirrel-driver]
4. 按一下**[確定]**。

**若要建立 HBase 叢集別名**

1. 從松鼠，按一下左側的 [**別名**] 索引標籤。
2. 從 [**別名**] 功能表中，按一下 [**新增別名**]。
3. 輸入下列資訊︰

    - **名稱**︰ HBase 叢集或您想要的任何名稱的名稱。
    - **驅動程式**︰ 鳳凰。  這必須符合您在最後一個程序中建立的驅動程式名稱。
    - **URL**︰ 就會從您驅動程式設定中複製的 URL。 確認使用者的所有小寫。
    - **使用者名稱**︰ 這會很有任何文字。  因為您在這裡使用 VPN 連線，使用者名稱不是完全。
    - **密碼**︰ 這會很有任何文字。

    ![HDInsight HBase 鳳凰松鼠驅動程式][img-squirrel-alias]
4. 按一下 [**測試**]。 
5. 按一下 [**連線**]。 連線時，松鼠看起來像︰

    ![HBase 鳳凰松鼠][img-squirrel]

**若要執行測試**

1. 按一下 [**物件**] 索引標籤旁的 [ **SQL** ] 索引標籤。
2. 複製並貼上下列的程式碼︰

        CREATE TABLE IF NOT EXISTS us_population (state CHAR(2) NOT NULL, city VARCHAR NOT NULL, population BIGINT  CONSTRAINT my_pk PRIMARY KEY (state, city))
3. 按一下 [執行] 按鈕。

    ![HBase 鳳凰松鼠][img-squirrel-sql]
4. 切換回 [**物件**] 索引標籤。
5. 展開別名名稱，然後再展開 [**資料表**。  您應該看到新] 下所列的資料表。
 
##<a name="next-steps"></a>後續步驟
本文中，您已經學會如何使用 Apache 鳳凰 HDInsight 中。  若要深入瞭解，請參閱

- [HDInsight HBase 概觀][hdinsight-hbase-overview]: HBase 是內建在提供的非結構化及 semistructured 資料大量隨機存取及強式一致性的 Hadoop Apache 開啟來源 NoSQL 資料庫。
- [佈建 Azure 虛擬網路上的 HBase 叢集][hdinsight-hbase-provision-vnet]︰ 虛擬網路整合 HBase 叢集可以部署到相同的虛擬網路應用程式，讓應用程式可直接通訊與 HBase。
- [設定 HBase HDInsight 複寫](hdinsight-hbase-geo-replication.md)︰ 瞭解如何設定 HBase 複寫跨兩個 Azure 資料中心。 
- [分析 HBase HDInsight 中使用 Twitter 舉動][hbase-twitter-sentiment]︰ 了解如何使用 HBase Hadoop 叢集 HDInsight 中進行即時[舉動分析](http://en.wikipedia.org/wiki/Sentiment_analysis)資料的大。

[azure-portal]: https://portal.azure.com
[vnet-point-to-site-connectivity]: https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNETPT

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-hbase-get-started]: hdinsight-hbase-tutorial-get-started.md
[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md#connect-to-hdinsight-clusters-by-using-rdp
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md

[hdinsight-hbase-phoenix-sqlline]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-phoenix-sqlline.png
[img-certificate]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-vpn-certificate.png
[img-vnet-diagram]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-vnet-point-to-site.png
[img-squirrel-driver]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-driver.png
[img-squirrel-alias]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-alias.png
[img-squirrel]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel.png
[img-squirrel-sql]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-sql.png


 
