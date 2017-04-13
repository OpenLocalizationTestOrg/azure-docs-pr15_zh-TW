<properties
    pageTitle="虛擬機器 tomcat |Microsoft Azure"
    description="本教學課程中使用傳統的部署模型，以建立的資源，並示範如何建立 Windows 虛擬機器，並將它設定為執行 Apache Tomcat 應用程式伺服器。"
    services="virtual-machines-windows"
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""
    tags="azure-service-management" />

<tags
    ms.service="virtual-machines-windows"
    ms.workload="web"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

# <a name="how-to-run-a-java-application-server-on-a-virtual-machine-created-with-the-classic-deployment-model"></a>如何建立使用傳統的部署模型虛擬機器上執行 Java 應用程式伺服器

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


您可以使用 Azure 虛擬機器提供伺服器功能。 作為範例，可以設定執行 Azure 虛擬機器裝載 Java 應用程式伺服器，例如 Apache Tomcat。 完成本指南之後，您必須瞭解如何建立虛擬機器 Azure 上執行，並將它設定為執行 Java 應用程式伺服器。

您將瞭解︰

* 如何建立具有 Java 開發套件 (JDK) 已安裝的虛擬機器。
* 如何從遠端登入您的虛擬機器。
* 如何在您的虛擬機器上安裝 Java 應用程式伺服器。
* 如何建立虛擬機器的端點。
* 如何開啟防火牆應用程式伺服器的連接埠。

進行本教學課程，Apache Tomcat 應用程式伺服器將會安裝虛擬機器上。 完成的安裝會導致 Tomcat 安裝，如下所示。

![執行 Apache Tomcat 的虛擬機器][virtual_machine_tomcat]

[AZURE.INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## <a name="to-create-a-virtual-machine"></a>若要建立虛擬機器

1. [Azure 傳統入口網站](https://manage.windowsazure.com)登入。
2. 按一下 [**新增**]、 按一下**計算**，按一下**虛擬機器**、，然後按一下 [**從圖庫**。
3. 在 [**選取虛擬機器圖像**] 對話方塊中，選取**JDK 7 Windows Server 2012**。
請注意， **JDK 6 Windows Server 2012**如果您不是準備好 JDK 7 中執行的舊版應用程式。
4. 按一下 [**下一步**]。
5. 在**虛擬機器設定**] 對話方塊中︰
    1. 指定虛擬機器的名稱。
    2. 指定要使用的虛擬機器的大小。
    3. 在 [**使用者名稱**] 欄位中輸入系統管理員的名稱。 請記住，此名稱和接下來，您將輸入的密碼，您會使用您從遠端登入時虛擬機器。
    4. 在 [**新密碼**] 欄位中，輸入密碼，然後重新輸入 [**確認**] 欄位中。 這是系統管理員帳戶的密碼。
    5. 按一下 [**下一步**]。
6. 在下一個**虛擬機器設定**] 對話方塊中︰
    1. **雲端服務**的使用預設值**建立新的雲端服務**。
    2. 跨 cloudapp.net，**雲端服務的 DNS 名稱**] 的值必須是唯一的。 如有需要修改此值，讓該 Azure 表示它是唯一。
    2. 指定地區、 相關性] 群組中或虛擬網路。 在進行本教學課程中，指定的區域**西美國**等。
    2. **儲存帳戶**，選取 [**使用自動產生的儲存空間帳戶**。
    3. **可用性設定**，請選取 [ **（無）**]。
    4. 按一下 [**下一步**]。
7. 在畢業**虛擬機器設定**] 對話方塊中︰
    1. 接受預設的端點項目。
    2. 按一下 [**完成**]。

## <a name="to-remotely-sign-in-to-your-virtual-machine"></a>若要從遠端登入您的虛擬機器

1. [Azure 傳統入口網站](https://manage.windowsazure.com)登入。
2. 按一下 [**虛擬機器**。
3. 按一下您想要登入的虛擬機器的名稱。
4. 虛擬機器開始後，在頁面底端] 快顯功能表可讓連線。
5. 按一下 [**連線**]。
6. 視需要連線至虛擬機器回應提示。 此移交儲存或開啟.rdp 檔案的連線的詳細資訊。 您可能為.rdp 檔案的第一行的最後一部分複製 url︰ 連接埠並將其貼於遠端登入應用程式。

## <a name="to-install-a-java-application-server-on-your-virtual-machine"></a>在 [您的虛擬機器上安裝 Java 應用程式伺服器

您可以將 Java 應用程式伺服器複製到您的虛擬機器，或者，您可以安裝 Java 應用程式伺服器透過安裝程式。

在進行本教學課程中，將會安裝 Tomcat。

1. 當您登入您的虛擬機器時，請開啟[Apache Tomcat](http://tomcat.apache.org/download-70.cgi)的瀏覽器工作階段。
2. 按兩下 [ **32 位元/64 位元的 Windows 服務安裝程式**] 連結。 藉由使用此技巧，Tomcat 會安裝 Windows 服務。
3. 出現提示時，選擇 [執行安裝程式]。
4. 在**Apache Tomcat 設定**精靈中，請依照提示安裝 Tomcat。 本教學課程中的目的，接受預設值是沒問題了。 當您到達**完成 Apache Tomcat 設定精靈**] 對話方塊時，您可以選擇性地檢查**執行 Apache Tomcat**有 Tomcat 立即開始。 按一下 [**完成**] 以完成 Tomcat 設定程序。

## <a name="to-start-tomcat"></a>若要開始 Tomcat
如果您未選擇執行 Tomcat**完成 Apache Tomcat 設定精靈**] 對話方塊中，請開啟您的虛擬機器上的命令提示字元，並執行**網路啟動 Tomcat7**來啟動它。

您現在應該會看到 Tomcat 如果您執行虛擬機器的瀏覽器並開啟<http://localhost:8080>執行。

若要查看 Tomcat 執行的從外部電腦，您需要建立端點，並開啟連接埠。

## <a name="to-create-an-endpoint-for-your-virtual-machine"></a>若要建立虛擬機器的端點
1. [Azure 傳統入口網站](https://manage.windowsazure.com)登入。
2. 按一下 [**虛擬機器**。
3. 按一下虛擬機器執行 Java 應用程式伺服器的名稱。
4. 按一下 [**結束點**]。
5. 按一下 [**新增**]。
6. 在**新增結束點**] 對話方塊中，確定已選取，**新增獨立結束點**，然後再按 [**下一步**。
7. 在**新的結束點詳細資料**] 對話方塊中︰
    1. 指定端點; 的名稱例如， **HttpIn**。
    2. 指定**TCP**通訊協定。
    3. 指定公用連接埠**80** 。
    4. 指定**8080**私人的連接埠。
    5. 按一下 [**完成**] 按鈕，以關閉對話方塊。 現在會建立您的結束點。

## <a name="to-open-a-port-in-the-firewall-for-your-virtual-machine"></a>若要開啟防火牆虛擬機器中的連接埠
1. 登入您的虛擬機器。
2. 按一下 [ **Windows [開始**]。
3. 按一下 [**控制台**]。
4. 按一下 [**系統及安全性]**，按一下 [ **Windows 防火牆**]，再按一下 [**進階設定]**。
5. 按一下 [**輸入規則**]，然後按一下 [**新增規則**。
 ![輸入新的規則][NewIBRule]
6. **規則類型**選取**連接埠**，然後按 [**下一步**。
 ![新輸入的規則的連接埠][NewRulePort]
7. 在 [**通訊協定和連接埠**] 畫面上選取**TCP**為**特定的本機連接埠**中，指定**8080** ，然後按 [**下一步**。
 ![輸入新的規則][NewRuleProtocol]
8. 在 [**動作**] 畫面中，選取 [**允許連線**，然後再按 [**下一步**。
 ![新輸入的規則的動作][NewRuleAction]
9. 在 [**設定檔**] 畫面中，確定**網域**、**私人**，及**公用**選取，然後按 [**下一步**。
 ![新輸入的規則的設定檔][NewRuleProfile]
10. 在 [**名稱**] 畫面中指定的規則 （規則名稱不一定要符合的結束點名稱，不過） **HttpIn** ，例如的名稱，然後按一下**完成**。  
 ![新輸入的規則的名稱][NewRuleName]

此時，您 Tomcat 網站應該可從外部的瀏覽器中檢視使用表單的 URL * *http://*您\_DNS\_名稱*。 cloudapp.net**，其中***您\_DNS\_名稱** * 是指定當您建立虛擬機器 DNS 名稱。

## <a name="application-lifecycle-considerations"></a>應用程式生命週期考量
* 您可以建立自己的 web 應用程式封存檔 （馬），將其新增至 [ **webapps** ] 資料夾。 例如建立基本的 Java 服務] 頁面 (JSP) 動態網站專案並將其匯出為馬檔案、 複製馬的 Apache Tomcat **webapps**資料夾虛擬機器，然後在瀏覽器中執行。
* 安裝 Tomcat 服務時，預設設定，以手動方式啟動。 您可以切換為自動啟動使用服務。 啟動服務嵌入式管理單元**Windows [開始**]、 [**系統管理工具**] 中，按一下，再按一下**服務**。 按兩下**Apache Tomcat**服務，然後將**啟動類型**設定為 [**自動]**。

    ![設定為自動啟動的服務][service_automatic_startup]

    自動擁有 Tomcat 開始的優點是，它就會啟動虛擬機器重新 （例如之後安裝軟體更新需要重新啟動）。

## <a name="next-steps"></a>後續步驟
深入瞭解其他服務 （例如 Azure 儲存體、 服務匯流排和 SQL 資料庫），您可能會想要包含的 Java 應用程式[Java 開發人員中心](https://azure.microsoft.com/develop/java/)檢視可用的資訊。

[virtual_machine_tomcat]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/WA_VirtualMachineRunningApacheTomcat.png

[service_automatic_startup]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/WA_TomcatServiceAutomaticStart.png









[NewIBRule]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/NewInboundRule.png
[NewRulePort]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/NewRulePort.png
[NewRuleProtocol]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/NewRuleProtocol.png
[NewRuleAction]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/NewRuleAction.png
[NewRuleName]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/NewRuleName.png
[NewRuleProfile]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/NewRuleProfile.png
