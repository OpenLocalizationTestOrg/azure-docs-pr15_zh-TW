<properties
    pageTitle="設定上 Linux VM Apache Tomcat |Microsoft Azure"
    description="瞭解如何設定 Apache Tomcat7 使用是 Azure 的虛擬機器 (VM) 執行 Linux。"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="NingKuang"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/15/2015"
    ms.author="ningk"/>

#<a name="how-to-set-up-tomcat7-on-a-linux-virtual-machine-with-microsoft-azure"></a>如何設定 Tomcat7 Linux 與 Microsoft Azure 虛擬機器

Apache Tomcat 或只是 Tomcat （舊稱也雅加達 Tomcat） 是開啟來源網頁伺服器及 servlet 容器開發 Apache 軟體 Foundation (ASF)。 Tomcat 實作 Java Servlet 和 JavaServer 頁面 (JSP) 的相關規格，從太陽 Microsystems，並提供純粹 Java HTTP web 伺服器的環境，以執行 Java 程式碼。 依最簡單的設定，Tomcat 會在單一作業系統程序執行。 此程序執行 Java 虛擬機器 (JVM)。 做為 Tomcat 程序中的個別執行緒處理 Tomcat 每個 HTTP 要求從瀏覽器。  

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


本指南中，您將會安裝 tomcat7 Linux 圖像上，並將其部署 Microsoft Azure 中。  

您將瞭解︰  

-   如何建立 Azure 虛擬機器。
-   如何虛擬機器準備 tomcat7。
-   如何安裝 tomcat7。

假設讀者已有 Azure 的訂閱。  如果不是您可以在[http://azure.microsoft.com](https://azure.microsoft.com/)免費試用版註冊。 如果您有 MSDN 訂閱，請參閱[Microsoft Azure 優惠︰ MSDN MPN，與 Bizspark 優點](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/?c=14-39)。 若要進一步瞭解 Azure，請參閱[什麼是 Azure？](https://azure.microsoft.com/overview/what-is-azure/)。

本主題假設您有 tomcat 和 Linux 的基本工作知識。  

##<a name="phase-1-create-an-image"></a>階段 1︰ 建立圖像
在此階段中，您將建立使用 Linux 圖像中 Azure 虛擬機器。  

###<a name="step-1-generate-an-ssh-authentication-key"></a>步驟 1︰ 產生 SSH 驗證索引鍵
SSH 是系統管理員重要的工具。 不過，設定 access 安全性根據人工決定密碼不是最佳作法。 惡意使用者可以中斷系統根據使用者名稱和弱式密碼。

好是讓遠端存取保持開啟，而且不必擔心密碼的方式。 包含非對稱密碼編譯的驗證方法。 使用者的私密金鑰是授與驗證。 您甚至可以鎖定完全不允許密碼驗證的使用者帳戶。

這個方法的另一個優點是，您不需要不同的密碼，登入到不同的伺服器。 您可以使用個人私密金鑰所有在伺服器上，以避免您不必記住數個密碼進行驗證。

您也可使用此方法需要密碼以登入。

遵循下列步驟，以產生 SSH 驗證。

1.  下載並安裝 puttygen 從下列位置︰ [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
2.  執行 PUTTYGEN。執行檔。
3.  按一下 [**產生**]，來產生金鑰。 程序中，您可以增加隨機移動滑鼠在視窗中的空白區域上。  
![][1]
4.  產生程序後 Puttygen.exe 會顯示您產生的金鑰。 例如︰  
![][2]
5.  選取並複製公開金鑰**鍵**並將其儲存為檔案命名 publicKey.pem。 不要按一下 [**儲存公開金鑰**，因為儲存的公開金鑰的檔案格式公開金鑰，我們希望不同。
6.  按一下 [**儲存私密金鑰**，並將其儲存在名為 privateKey.ppk 的檔案。

###<a name="step-2-create-the-image-in-the-azure-portal"></a>步驟 2︰ 建立 Azure 入口網站中的圖像。
[Azure 入口網站](https://portal.azure.com/)中，在 [建立圖像，請選擇根據您的需求 Linux 圖像工作列中的 [**新增**]。 下列範例會使用 Ubuntu 14.04 圖像。
![][3]

針對**主機名稱**會指定寄件者和網際網路用戶端用來存取此虛擬機器的 url 名稱。 定義最後的部分 DNS 名稱，例如 tomcatdemo，且 Azure 會產生的 URL 為 tomcatdemo.cloudapp.net。  

**SSH 驗證金鑰**，請從**publicKey.pem**檔案，其中包含公開金鑰 puttygen 所產生的複製的值。  
![][4]

視需要設定其他設定，然後按一下 [建立。  

##<a name="phase-2-prepare-your-virtual-machine-for-tomcat7"></a>階段 2︰ 將您的虛擬機器準備 Tomcat7
在階段中，您會設定 tomcat 流量的端點，然後連線到新的虛擬機器。
###<a name="step-1-open-the-http-port-to-allow-web-access"></a>步驟 1︰ 開啟允許 web access 的 HTTP 連接埠
Azure 中的端點包含通訊協定 （TCP 或 UDP），以及公開及私密金鑰的連接埠。 私人的連接埠是服務接聽虛擬機器的連接埠。 公用連接埠是 Azure 雲端服務上接聽外部內送、 網際網路流量的連接埠。  

TCP 連接埠 8080 是哪一個 tomcat 接聽上的預設連接埠號碼。 開啟此連接埠與 Azure 端點，可讓您與其他用戶端存取網際網路 tomcat 頁面。  

1.  Azure 入口網站中，按一下 [**瀏覽** -> **虛擬機器**]，然後按一下您所建立的虛擬機器。  
![][5]
2.  要加入您的虛擬機器中的端點，請按一下 [**結束點**] 方塊。
![][6]
3.  按一下 [**新增**]。  
    1.  **結束點**，在端點，請輸入端點的名稱，然後輸入 80**公用**連接埠。  

        如果您將它設定為 [80，不需要的 URL，可讓您存取 tomcat 中包含連接埠號碼。 例如，http://tomcatdemo.cloudapp.net。    

        如果您將它設定為另一個值，例如 81，您需要將連接埠號碼新增至存取 tomcat 的 URL。 例如，http://tomcatdemo.cloudapp.net:81 /。
    2.  輸入 8080 私人連接埠。 根據預設，tomcat 接聽 TCP 連接埠 8080。 如果您已變更預設聆聽 tomcat 連接埠，您應該更新私人連接埠是 tomcat 相同接聽連接埠。  
    ![][7]

4.  按一下**[確定**]，以加入您的虛擬機器中的端點。



###<a name="step-2-connect-to-the-image-you-created"></a>步驟 2︰ 連線至您建立的圖像。
您可以選擇以連線至您的虛擬機器任何 SSH 工具。 在此範例中，我們使用 Putty。  

首先，取得您的虛擬機器 DNS 名稱從 Azure 入口網站。 **按一下 [瀏覽** -> **虛擬機器**]-> [您的虛擬機器中的名稱]-> [**屬性**]，然後查看**網域名稱**] 欄位中的 [**屬性**] 方塊中。  

取得從**SSH**欄位 SSH 連線的連接埠號碼。 以下是範例。  
![][8]

從 [[在這裡](http://www.putty.org/)下載 Putty。  

下載之後，按一下 [可執行檔 PUTTY]。執行檔。 設定基本的選項，主機名稱和連接埠號碼的虛擬機器屬性。 以下是範例︰  
![][9]

在左窗格中，按一下 [**連線** -> **SSH** -> **驗證**，然後按一下 [到指定檔案的位置**privateKey.ppk** ，其中包含私密金鑰的 [**瀏覽**產生的 puttygen 階段 1︰ 建立圖像。 以下是範例︰  
![][10]

按一下 [**開啟**]。 您可能會收到的郵件] 方塊。 如果您已設定 DNS 名稱正確連接埠號碼，請按一下**[是]**。
![][11]  


您應該會看到下列動作︰  
![][12]

輸入使用者名稱，指定在您建立虛擬機器中階段 1︰ 建立圖像。 您會看到如下所示的項目︰  
![][13]





##<a name="phase-3-install-software"></a>階段 3︰ 安裝軟體
在此階段中，您必須安裝 Java 執行階段環境、 tomcat，以及其他 tomcat 元件。  

###<a name="java-runtime-environment"></a>Java 執行階段環境
Java 是撰寫 tomcat。 有兩種 Java 開發套件 (JDKs) （OpenJDK 及 Oracle JDK），您可以選擇您要的項目。  

>AZURE。附註︰ 兩個 JDKs 幾乎相同的程式碼的類別中 Java API，但是虛擬機器的程式碼是實際不同。 當文件庫，OpenJDK 可能會在 Oracle 通常會使用已關閉的同時使用開啟文件庫。 但 Oracle JDK 有更多的類別，以及一些固定的錯誤，而 Oracle JDK 是更穩定比 OpenJDK。

下列命令下載不同 JDKs。  

開啟 jdk   

    sudo apt-get update  
    sudo apt-get install openjdk-7-jre  

oracle jdk  

-   若要下載 JDK 從 Oracle 網站︰  

        wget --header "Cookie: oraclelicense=accept-securebackup-cookie" http://download.oracle.com/otn-pub/java/jdk/8u5-b13/jdk-8u5-linux-x64.tar.gz  

-   若要建立的目錄，以包含 JDK 檔案︰  

        sudo mkdir /usr/lib/jvm  

-   若要將/usr/文件庫/jvm/目錄擷取 JDK 檔案︰  

        sudo tar -zxf jdk-8u5-linux-x64.tar.gz  -C /usr/lib/jvm/  

-   若要設定 Oracle JDK JVM 的預設值︰  

        sudo update-alternatives --install /usr/bin/java java /usr/lib/jvm/jdk1.8.0_05/bin/java 100  
        sudo update-alternatives --install /usr/bin/javac javac /usr/lib/jvm/jdk1.8.0_05/bin/javac 100  

####<a name="test"></a>測試︰
您可以使用類似下列命令以測試 Java 執行階段環境安裝正確︰  

    java -version  

如果您安裝開啟 jdk 時，您應該會看到下列訊息︰![][14]

如果您安裝 oracle jdk 時，您應該會看到下列訊息︰![][15]

###<a name="tomcat7"></a>Tomcat7
若要安裝 tomcat7 使用下列命令︰  

    sudo apt-get install tomcat7  

如果您不使用 tomcat7，請使用這個命令的適當的變化。  

####<a name="test"></a>測試︰

若要檢查是否已成功安裝 tomcat7，瀏覽至 tomcat 伺服器的 DNS 名稱 （http://tomcatexample.cloudapp.net/ 是本文中的範例 URL）。 如果您看到類似下列頁面，您必須安裝 tomcat7 正確。
![][16]


###<a name="install-other-tomcat-components"></a>安裝其他 Tomcat 元件
有其他選用 tomcat 元件，您也可以安裝。  

若要查看所有可用的元件使用**sudo 引起快取搜尋 tomcat7** ] 命令。 下列命令是安裝一些實用的組件的範例。  

    sudo apt-get install tomcat7-admin      #admin web applications
    sudo apt-get install tomcat7-user         #tools to create user instances  

##<a name="phase-4-configure-tomcat"></a>階段 4︰ 設定 Tomcat
在此階段中，您可以管理 tomcat。
###<a name="start-and-stop-tomcat7"></a>啟動和停止 tomcat7
您在安裝時，會自動開啟 tomcat7 伺服器。 您也可以啟動它自己使用下列命令︰   

    sudo /etc/init.d/tomcat7 start

若要停止 tomcat7:  

    sudo /etc/init.d/tomcat7 stop

若要檢視的 tomcat7 狀態︰  

    sudo /etc/init.d/tomcat7 status

若要重新啟動 tomcat 服務︰  

    sudo /etc/init.d/tomcat7 restart

###<a name="tomcat-administration"></a>Tomcat 管理
您可以編輯 Tomcat 使用者設定檔，設定您的管理員認證，使用下列命令︰  

    sudo vi  /etc/tomcat7/tomcat-users.xml   

以下是範例︰  
![][17]  

>AZURE。附註︰ 建立強式密碼管理員使用者名稱。  

之後編輯此檔案，請重新啟動下列命令，以確保所做的變更生效 tomcat7 服務︰  

    sudo /etc/init.d/tomcat7 restart  

開啟瀏覽器中，並輸入的 URL **http://<your tomcat server DNS name>/管理員/html**。 本文中例如，URL 是 http://tomcatexample.cloudapp.net/manager/html。  

連線之後，您應該會看到類似以下內容︰  
![][18]

##<a name="common-issues"></a>常見的問題

###<a name="cant-access-the-virtual-machine-with-tomcat-and-moodle-from-the-internet"></a>無法從網際網路存取 Tomcat 與 Moodle 虛擬機器

-   **徵狀**  
Tomcat 正在執行，但您無法看到 [Tomcat 預設頁面與瀏覽器。
-   **可能的根大小寫**   
    1.  無法與您的虛擬機器端點 tomcat 流量的私人連接埠相同 tomcat 接聽連接埠。  

        檢查您的公用連接埠和私人連接埠端點設定，並且確實私人連接埠 tomcat 相同接聽連接埠。 請參閱階段 1︰ 建立指示的影像上設定您的虛擬機器中的端點。  

        若要判斷 tomcat 接聽連接埠，開啟 /etc/httpd/conf/httpd.conf （紅色角色發行版本） 或 /etc/tomcat7/server.xml （Debian 發行版本）。 根據預設，tomcat 接聽連接埠是 8080。 以下是範例︰  

            <Connector port="8080" protocol="HTTP/1.1"  connectionTimeout="20000"  URIEncoding="UTF-8"            redirectPort="8443" />  

        如果您使用的虛擬機器等 Debian 或 Ubuntu 和您想要變更預設連接埠的 Tomcat 聆聽 (例如 8081)，那麼您也應該 os 開啟連接埠。 首先，開啟 [設定檔︰  

            sudo vi /etc/default/tomcat7  

        接著取消註解的最後一行，然後變更 「 沒有 」 [是]。  

            AUTHBIND=yes

    2.  防火牆已停用 tomcat 接聽連接埠。

        如果您只可以看到 Tomcat 預設頁面從本機主機，問題是最有可能由 tomcat 聆聽連接埠被防火牆封鎖。 您可以使用 w3m 工具來瀏覽網頁。 下列命令安裝 w3m，並瀏覽至 Tomcat 預設頁面︰  

            sudo yum  install w3m w3m-img
            w3m http://localhost:8080  

-   **解決方案**
    1. 如果 tomcat 聆聽連接埠不相同的虛擬機器流量的端點私人連接埠，您需要變更為 tomcat 相同接聽連接埠私人連接埠。   

    2.  如果防火牆/iptables 導致問題，請將下列行新增至 /etc/sysconfig/iptables:  

            -A INPUT -p tcp -m tcp --dport 80 -j ACCEPT
            -A INPUT -p tcp -m tcp --dport 443 -j ACCEPT  

        請注意，第二行時，才需要 https 流量。  

        請確定此上方會全域限制存取，例如下列任何行︰  

            -A INPUT -j REJECT --reject-with icmp-host-prohibited  

        若要重新載入 iptables，執行下列命令︰  

            service iptables restart  

        這的上 CentOS 6.3 測試。

###<a name="permission-denied-when-upload-you-project-files-to-varlibtomcat7webapps"></a>權限拒專案 /var/lib/tomcat7/webapps 檔案上傳 /  

-   **徵狀**  
連線到您的虛擬機器並瀏覽至 /var/lib/tomcat7/webapps/發佈您的網站，您可以使用任何的 SFTP 用戶端 （例如 FileZilla)，當您收到錯誤訊息如下︰  

        status: Listing directory /var/lib/tomcat7/webapps
        Command:    put "C:\Users\liang\Desktop\info.jsp" "info.jsp"
        Error:  /var/lib/tomcat7/webapps/info.jsp: open for write: permission denied
        Error:  File transfer failed

-   **可能的根大小寫**您有沒有存取 /var/lib/tomcat7/webapps 資料夾的權限。  
-   **解決方案**  
您需要從根帳戶取得權限。 若要使用佈建電腦時所用的使用者名稱，您可以從根變更該資料夾的擁有權。 以下是使用 azureuser 帳戶名稱的範例︰  

        sudo chown azureuser -R /var/lib/tomcat7/webapps

    若要套用的目錄內的所有檔案的權限也使用-R 選項。  

    請注意，也可以使用這個命令目錄。 -R 選項會變更所有檔案和目錄目錄內的權限。 以下是範例︰  

        sudo chown -R username:group directory  

    這個命令會變更擁有權 （使用者和群組） 的所有檔案和 eml 和目錄本身的目錄。  

    下列命令只會變更的資料夾目錄的權限，但保留的檔案與資料夾目錄內了解單獨使用。  

        sudo chown username:group directory



[1]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-01.png
[2]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-02.png
[3]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-03.png
[4]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-04.png
[5]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-05.png
[6]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-06.png
[7]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-07.png
[8]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-08.png
[9]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-09.png
[10]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-10.png
[11]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-11.png
[12]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-12.png
[13]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-13.png
[14]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-14.png
[15]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-15.png
[16]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-16.png
[17]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-17.png
[18]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-18.png
