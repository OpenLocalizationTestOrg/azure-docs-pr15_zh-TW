<properties
    pageTitle="在 VM 運算密集 Java 應用程式 |Microsoft Azure"
    description="瞭解如何建立 Azure 虛擬機器執行運算密集 Java 應用程式可以監視另一個 Java 應用程式。"
    services="virtual-machines-windows"
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""
    tags="azure-service-management,azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

# <a name="how-to-run-a-compute-intensive-task-in-java-on-a-virtual-machine"></a>如何執行運算密集工作 java 虛擬機器

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]
 

您可以使用 Azure 虛擬機器來處理運算密集工作。 例如，虛擬機器可以處理工作，並提供用戶端電腦或行動應用程式的結果。 閱讀本文之後，您必須瞭解如何建立虛擬機器執行運算密集 Java 應用程式可以監視另一個 Java 應用程式。

本教學課程假設您已經知道如何建立 Java 主控台應用程式、 Java 應用程式，可以匯入文件庫，並可以產生 Java 封存 (JAR)。 Microsoft Azure 不知道會引用本身。

您將瞭解︰

* 如何建立虛擬機器與 Java 開發套件 (JDK) 已安裝。
* 如何從遠端登入您的虛擬機器。
* 如何建立服務匯流排命名空間。
* 如何建立 Java 應用程式執行需要大量計算的工作。
* 如何建立 Java 應用程式的監視運算密集任務的進度。
* 如何執行 Java 應用程式。
* 如何停止 Java 應用程式。

本教學課程中會使用 Traveling 過關問題運算密集工作。 以下是範例 Java 應用程式正在執行需要大量計算的工作。

![Traveling 過關問題規劃求解][solver_output]

以下是監控運算密集工作 Java 應用程式的範例。

![Traveling 過關問題用戶端][client_output]

[AZURE.INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## <a name="to-create-a-virtual-machine"></a>若要建立虛擬機器

1. [Azure 傳統入口網站](https://manage.windowsazure.com)登入。
2. 按一下 [**新增**]、 按一下**計算**，按一下**虛擬機器**、，然後按一下 [**從圖庫**。
3. 在 [**選取虛擬機器圖像**] 對話方塊中，選取**JDK 7 Windows Server 2012**。
請注意， **JDK 6 Windows Server 2012**可在必要時尚未準備好 JDK 7 中執行的舊版應用程式。
4. 按一下 [**下一步**]。
4. 在**虛擬機器設定**] 對話方塊中︰
    1. 指定虛擬機器的名稱。
    2. 指定要使用的虛擬機器的大小。
    3. 在 [**使用者名稱**] 欄位中輸入系統管理員的名稱。 請記住，此名稱和接下來，您將輸入的密碼，您會使用您從遠端登入虛擬機器時。
    4. 在 [**新密碼**] 欄位中，輸入密碼，然後重新輸入 [**確認**] 欄位中。 這是系統管理員帳戶的密碼。
    5. 按一下 [**下一步**]。
5. 在下一個**虛擬機器設定**] 對話方塊中︰
    1. **雲端服務**的使用預設值**建立新的雲端服務**。
    2. 跨 cloudapp.net，**雲端服務的 DNS 名稱**] 的值必須是唯一的。 如有需要修改此值，讓該 Azure 表示它是唯一。
    2. 指定地區、 相關性] 群組中或虛擬網路。 為了本教學課程中，指定的區域**西美國**等。
    2. **儲存帳戶**，選取 [**使用自動產生的儲存空間帳戶**。
    3. **可用性設定**，請選取 [ **（無）**]。
    4. 按一下 [**下一步**]。
5. 在畢業**虛擬機器設定**] 對話方塊中︰
    1. 接受預設的端點項目。
    2. 按一下 [**完成**]。

## <a name="to-remotely-log-in-to-your-virtual-machine"></a>若要從遠端登入您的虛擬機器

1. [Azure 傳統入口網站](https://manage.windowsazure.com)登入。
2. 按一下 [**虛擬機器**。
3. 按一下您想要登入的虛擬機器的名稱。
4. 按一下 [**連線**]。
5. 視需要連線至虛擬機器回應提示。 當系統管理員名稱和密碼的提示，請使用時建立虛擬機器所提供的值。

請注意 Azure 服務匯流排功能需要安裝屬於您 JRE **cacerts**市集巴爾的摩 CyberTrust 根憑證。 這個憑證會自動包含在 Java 執行階段環境 (JRE) 使用此教學課程。 如果您沒有這個憑證 JRE **cacerts**存放區中，請參閱[新增 Java CA 憑證儲存憑證][add_ca_cert]新增其 （以及 cacerts 存放區中檢視憑證的詳細資訊） 的資訊。

## <a name="how-to-create-a-service-bus-namespace"></a>如何建立服務匯流排命名空間

若要開始使用服務匯流排佇列 Azure 中，您必須先建立服務命名空間。 服務命名空間提供範圍容器來處理您的應用程式中的服務匯流排資源。

若要建立服務命名空間︰

1.  [Azure 傳統入口網站](https://manage.windowsazure.com)登入。
2.  在左下方導覽窗格中的 Azure 傳統入口網站，按一下 [**服務匯流排、 存取控制和快取**。
3.  在 Azure 傳統入口網站的左上窗格中，按一下 [**服務匯流排**節點，，然後按一下 [**新增**] 按鈕。  
    ![服務匯流排節點的螢幕擷取畫面][svc_bus_node]
4.  在 [**建立新的服務命名空間**] 對話方塊中，輸入**命名空間**，然後確認該唯一，請按一下 [**檢查可用性**] 按鈕。  
    ![建立新的命名空間螢幕擷取畫面][create_namespace]
5.  有確保命名空間名稱之後，選擇 [國家或地區的命名空間應會裝載，然後按一下 [**建立命名空間**] 按鈕。  

    您建立的命名空間會顯示在 Azure 傳統入口網站，並會啟動一些時間。 請等待再繼續進行下一個步驟是**作用中**狀態。

## <a name="obtain-the-default-management-credentials-for-the-namespace"></a>取得預設管理認證的命名空間

若要執行管理作業，例如佇列中，建立新的命名空間，您需要取得命名空間的管理認證。

1.  在左側的功能窗格中，按一下 [**服務匯流排**節點顯示可用的命名空間的清單。
    ![可用的命名空間的螢幕擷取畫面][avail_namespaces]
2.  選取您剛才從顯示的清單建立的命名空間。
    ![命名空間清單螢幕擷取畫面][namespace_list]
3.  右側的 [**屬性**] 窗格會列出新的命名空間的屬性。
    ![內容窗格螢幕擷取畫面][properties_pane]
4.  隱藏**預設鍵**。 按一下 [**檢視**] 按鈕，以顯示的安全性憑證。
    ![預設索引鍵的螢幕擷取畫面][default_key]
5.  當您將會使用下列這項資訊來執行作業的命名空間，則請記下**預設的發行者**] 及 [**預設的索引鍵**。

## <a name="how-to-create-a-java-application-that-performs-a-compute-intensive-task"></a>如何建立 Java 應用程式執行需要大量計算的工作

1. 在您的部署電腦 （這沒有為您建立的虛擬機器），下載[Java Azure SDK](https://azure.microsoft.com/develop/java/)。
2. 建立使用程式碼範例結尾的此區段 Java 主控台應用程式。 在本教學課程中，我們將使用**TSPSolver.java**作為 Java 檔案名稱。 修改**您\_服務\_匯流排\_命名空間**，**您\_服務\_匯流排\_擁有者**，及**您\_服務\_匯流排\_鍵**版面配置區以使用您的服務可匯流排**命名空間**、**預設發行者**及**預設的索引鍵**的值，分別。
3. 後編碼，匯出應用程式，以執行 Java 封存 (JAR)，並將產生 JAR 封裝必要的文件庫。 在本教學課程中，我們將使用**TSPSolver.jar**作為產生 JAR 名稱。

<p/>

    // TSPSolver.java

    import com.microsoft.windowsazure.services.core.Configuration;
    import com.microsoft.windowsazure.services.core.ServiceException;
    import com.microsoft.windowsazure.services.serviceBus.*;
    import com.microsoft.windowsazure.services.serviceBus.models.*;
    import java.io.*;
    import java.text.DateFormat;
    import java.text.SimpleDateFormat;
    import java.util.ArrayList;
    import java.util.Date;
    import java.util.List;

    public class TSPSolver {

        //  Value specifying how often to provide an update to the console.
        private static long loopCheck = 100000000;  

        private static long nTimes = 0, nLoops=0;

        private static double[][] distances;
        private static String[] cityNames;
        private static int[] bestOrder;
        private static double minDistance;
        private static ServiceBusContract service;

        private static void buildDistances(String fileLocation, int numCities) throws Exception{
            try{
                BufferedReader file = new BufferedReader(new InputStreamReader(new DataInputStream(new FileInputStream(new File(fileLocation)))));
                double[][] cityLocs = new double[numCities][2];
                for (int i = 0; i<numCities; i++){
                    String[] line = file.readLine().split(", ");
                    cityNames[i] = line[0];
                    cityLocs[i][0] = Double.parseDouble(line[1]);
                    cityLocs[i][1] = Double.parseDouble(line[2]);
                }
                for (int i = 0; i<numCities; i++){
                    for (int j = i; j<numCities; j++){
                        distances[i][j] = Math.hypot(Math.abs(cityLocs[i][0] - cityLocs[j][0]), Math.abs(cityLocs[i][1] - cityLocs[j][1]));
                        distances[j][i] = distances[i][j];
                    }
                }
            } catch (Exception e){
                throw e;
            }
        }

        private static void permutation(List<Integer> startCities, double distSoFar, List<Integer> restCities) throws Exception {

            try
            {
                nTimes++;
                if (nTimes == loopCheck)
                {
                    nLoops++;
                    nTimes = 0;
                    DateFormat dateFormat = new SimpleDateFormat("MM/dd/yyyy HH:mm:ss");
                    Date date = new Date();
                    System.out.print("Current time is " + dateFormat.format(date) + ". ");
                    System.out.println(  "Completed " + nLoops + " iterations of size of " + loopCheck + ".");
                }

                if ((restCities.size() == 1) && ((minDistance == -1) || (distSoFar + distances[restCities.get(0)][startCities.get(0)] + distances[restCities.get(0)][startCities.get(startCities.size()-1)] < minDistance))){
                    startCities.add(restCities.get(0));
                    newBestDistance(startCities, distSoFar + distances[restCities.get(0)][startCities.get(0)] + distances[restCities.get(0)][startCities.get(startCities.size()-2)]);
                    startCities.remove(startCities.size()-1);
                }
                else{
                    for (int i=0; i<restCities.size(); i++){
                        startCities.add(restCities.get(0));
                        restCities.remove(0);
                        permutation(startCities, distSoFar + distances[startCities.get(startCities.size()-1)][startCities.get(startCities.size()-2)],restCities);
                        restCities.add(startCities.get(startCities.size()-1));
                        startCities.remove(startCities.size()-1);
                    }
                }
            }
            catch (Exception e)
            {
                throw e;
            }
        }

        private static void newBestDistance(List<Integer> cities, double distance) throws ServiceException, Exception {
            try
            {
                minDistance = distance;
                String cityList = "Shortest distance is "+minDistance+", with route: ";
                for (int i = 0; i<bestOrder.length; i++){
                    bestOrder[i] = cities.get(i);
                    cityList += cityNames[bestOrder[i]];
                    if (i != bestOrder.length -1)
                        cityList += ", ";
                }
                System.out.println(cityList);
                service.sendQueueMessage("TSPQueue", new BrokeredMessage(cityList));
            }
            catch (ServiceException se)
            {
                throw se;
            }
            catch (Exception e)
            {
                throw e;
            }
        }

        public static void main(String args[]){

            try {

                Configuration config = ServiceBusConfiguration.configureWithWrapAuthentication(
                        "your_service_bus_namespace", "your_service_bus_owner",
                        "your_service_bus_key",
                        ".servicebus.windows.net",
                        "-sb.accesscontrol.windows.net/WRAPv0.9");

                service = ServiceBusService.create(config);

                int numCities = 10;  // Use as the default, if no value is specified at command line.
                if (args.length != 0)
                {
                    if (args[0].toLowerCase().compareTo("createqueue")==0)
                    {
                        // No processing to occur other than creating the queue.
                        QueueInfo queueInfo = new QueueInfo("TSPQueue");

                        service.createQueue(queueInfo);

                        System.out.println("Queue named TSPQueue was created.");

                        System.exit(0);
                    }

                    if (args[0].toLowerCase().compareTo("deletequeue")==0)
                    {
                        // No processing to occur other than deleting the queue.
                        service.deleteQueue("TSPQueue");

                        System.out.println("Queue named TSPQueue was deleted.");

                        System.exit(0);
                    }

                    // Neither creating or deleting a queue.
                    // Assume the value passed in is the number of cities to solve.
                    numCities = Integer.valueOf(args[0]);  
                }

                System.out.println("Running for " + numCities + " cities.");

                List<Integer> startCities = new ArrayList<Integer>();
                List<Integer> restCities = new ArrayList<Integer>();
                startCities.add(0);
                for(int i = 1; i<numCities; i++)
                    restCities.add(i);
                distances = new double[numCities][numCities];
                cityNames = new String[numCities];
                buildDistances("c:\\TSP\\cities.txt", numCities);
                minDistance = -1;
                bestOrder = new int[numCities];
                permutation(startCities, 0, restCities);
                System.out.println("Final solution found!");
                service.sendQueueMessage("TSPQueue", new BrokeredMessage("Complete"));
            }
            catch (ServiceException se)
            {
                System.out.println(se.getMessage());
                se.printStackTrace();
                System.exit(-1);
            }
            catch (Exception e)
            {
                System.out.println(e.getMessage());
                e.printStackTrace();
                System.exit(-1);
            }
        }

    }



## <a name="how-to-create-a-java-application-that-monitors-the-progress-of-the-compute-intensive-task"></a>如何建立監視運算密集任務的進度 Java 應用程式

1. 在您的部署電腦上建立 Java 主控台應用程式使用程式碼範例結尾的這一節。 在本教學課程中，我們將使用**TSPClient.java**作為 Java 檔案名稱。 如先前所示，修改**您\_服務\_匯流排\_命名空間**，**您\_服務\_匯流排\_擁有者**，及**您\_服務\_匯流排\_鍵**版面配置區以使用您的服務可匯流排**命名空間**、**預設發行者**及**預設的索引鍵**的值，分別。
2. 匯出可執行的 JAR，應用程式，並將產生 JAR 封裝必要的文件庫。 在本教學課程中，我們將使用**TSPClient.jar**作為產生 JAR 名稱。

<p/>

    // TSPClient.java

    import java.util.Date;
    import java.text.DateFormat;
    import java.text.SimpleDateFormat;
    import com.microsoft.windowsazure.services.serviceBus.*;
    import com.microsoft.windowsazure.services.serviceBus.models.*;
    import com.microsoft.windowsazure.services.core.*;

    public class TSPClient
    {

        public static void main(String[] args)
        {
                try
                {

                    DateFormat dateFormat = new SimpleDateFormat("MM/dd/yyyy HH:mm:ss");
                    Date date = new Date();
                    System.out.println("Starting at " + dateFormat.format(date) + ".");

                    String namespace = "your_service_bus_namespace";
                    String issuer = "your_service_bus_owner";
                    String key = "your_service_bus_key";

                    Configuration config;
                    config = ServiceBusConfiguration.configureWithWrapAuthentication(
                            namespace, issuer, key,
                            ".servicebus.windows.net",
                            "-sb.accesscontrol.windows.net/WRAPv0.9");

                    ServiceBusContract service = ServiceBusService.create(config);

                    BrokeredMessage message;

                    int waitMinutes = 3;  // Use as the default, if no value is specified at command line.
                    if (args.length != 0)
                    {
                        waitMinutes = Integer.valueOf(args[0]);  
                    }

                    String waitString;

                    waitString = (waitMinutes == 1) ? "minute." : waitMinutes + " minutes.";

                    // This queue must have previously been created.
                    service.getQueue("TSPQueue");

                    int numRead;

                    String s = null;

                    while (true)
                    {

                        ReceiveQueueMessageResult resultQM = service.receiveQueueMessage("TSPQueue");
                        message = resultQM.getValue();

                        if (null != message && null != message.getMessageId())
                        {

                            // Display the queue message.
                            byte[] b = new byte[200];

                            System.out.print("From queue: ");

                            s = null;
                            numRead = message.getBody().read(b);
                            while (-1 != numRead)
                            {
                                s = new String(b);
                                s = s.trim();
                                System.out.print(s);
                                numRead = message.getBody().read(b);
                            }
                            System.out.println();
                            if (s.compareTo("Complete") == 0)
                            {
                                // No more processing to occur.
                                date = new Date();
                                System.out.println("Finished at " + dateFormat.format(date) + ".");
                                break;
                            }
                        }
                        else
                        {
                            // The queue is empty.
                            System.out.println("Queue is empty. Sleeping for another " + waitString);
                            Thread.sleep(60000 * waitMinutes);
                        }
                    }

            }
            catch (ServiceException se)
            {
                System.out.println(se.getMessage());
                se.printStackTrace();
                System.exit(-1);
            }
            catch (Exception e)
            {
                System.out.println(e.getMessage());
                e.printStackTrace();
                System.exit(-1);
            }

        }

    }

## <a name="how-to-run-the-java-applications"></a>Java 應用程式的執行方式
執行運算密集應用程式，第一次來建立佇列中，然後解決出差 Saleseman 問題，但會將目前的最佳路由新增至服務匯流排佇列中。 執行 （或之後），運算密集應用程式時執行用戶端來顯示從服務匯流排佇列中的結果。

### <a name="to-run-the-compute-intensive-application"></a>若要執行運算密集應用程式

1. 登入您的虛擬機器。
2. 建立的資料夾，您會在其中執行您的應用程式。 例如， **c:\TSP**。
3. 將**TSPSolver.jar**複製到**c:\TSP**，
4. 建立一個名為**c:\TSP\cities.txt**使用下列內容檔案。

        City_1, 1002.81, -1841.35
        City_2, -953.55, -229.6
        City_3, -1363.11, -1027.72
        City_4, -1884.47, -1616.16
        City_5, 1603.08, -1030.03
        City_6, -1555.58, 218.58
        City_7, 578.8, -12.87
        City_8, 1350.76, 77.79
        City_9, 293.36, -1820.01
        City_10, 1883.14, 1637.28
        City_11, -1271.41, -1670.5
        City_12, 1475.99, 225.35
        City_13, 1250.78, 379.98
        City_14, 1305.77, 569.75
        City_15, 230.77, 231.58
        City_16, -822.63, -544.68
        City_17, -817.54, -81.92
        City_18, 303.99, -1823.43
        City_19, 239.95, 1007.91
        City_20, -1302.92, 150.39
        City_21, -116.11, 1933.01
        City_22, 382.64, 835.09
        City_23, -580.28, 1040.04
        City_24, 205.55, -264.23
        City_25, -238.81, -576.48
        City_26, -1722.9, -909.65
        City_27, 445.22, 1427.28
        City_28, 513.17, 1828.72
        City_29, 1750.68, -1668.1
        City_30, 1705.09, -309.35
        City_31, -167.34, 1003.76
        City_32, -1162.85, -1674.33
        City_33, 1490.32, 821.04
        City_34, 1208.32, 1523.3
        City_35, 18.04, 1857.11
        City_36, 1852.46, 1647.75
        City_37, -167.44, -336.39
        City_38, 115.4, 0.2
        City_39, -66.96, 917.73
        City_40, 915.96, 474.1
        City_41, 140.03, 725.22
        City_42, -1582.68, 1608.88
        City_43, -567.51, 1253.83
        City_44, 1956.36, 830.92
        City_45, -233.38, 909.93
        City_46, -1750.45, 1940.76
        City_47, 405.81, 421.84
        City_48, 363.68, 768.21
        City_49, -120.3, -463.13
        City_50, 588.51, 679.33

5. 在命令提示字元中，變更 c:\TSP 目錄。
6. 確定 JRE bin 資料夾位於 PATH 環境變數。
7. 您需要建立服務匯流排佇列中，才能執行 TSP 規劃求解的排列方式數目。 執行下列命令以建立服務匯流排佇列。

        java -jar TSPSolver.jar createqueue

8. 現在，建立佇列中，您可以執行 TSP 規劃求解的排列方式數目。 例如，執行下列命令以執行規劃求解的 8 的城市。

        java -jar TSPSolver.jar 8

 如果您不指定數字，它將執行的 10 個城市。 規劃求解尋找目前最短的路由，因為它會將其新增至佇列中。

> [AZURE.NOTE]
> 大的數字的指定，再將執行的 「 規劃求解 」。 例如，執行 14 城市可能需要幾分鐘，然後執行 15 城市會需要幾個小時。 增加到 16 或多個城市，可能會導致執行階段 （最後週、 數月和年） 的天數。 這是因為規劃求解評估的城市增加數的排列方式數目快速增加。

### <a name="how-to-run-the-monitoring-client-application"></a>監控的用戶端應用程式的執行方式
1. 登入電腦，您會在其中執行的用戶端應用程式。 這不需要為相同的電腦執行**TSPSolver**應用程式時，雖然可以。
2. 建立的資料夾，您會在其中執行您的應用程式。 例如， **c:\TSP**。
3. 將**TSPClient.jar**複製到**c:\TSP**，
4. 確定 JRE bin 資料夾位於 PATH 環境變數。
5. 在命令提示字元中，變更 c:\TSP 目錄。
6. 執行下列命令。

        java -jar TSPClient.jar

    或者，您可以指定休眠之間檢查佇列中，以傳遞的命令列引數中的分鐘數。 檢查佇列中的預設就寢期間是 3 分鐘，如果沒有命令列引數傳遞到**TSPClient**用於之。 如果您想要使用不同的值來就寢時間間隔，例如一分鐘，執行下列命令。

        java -jar TSPClient.jar 1

    用戶端便會執行，直到看到佇列中郵件的 「 完成 」。 請注意，是否您執行的 「 規劃求解 」 的多個項目，而不執行用戶端，您可能需要執行用戶端完全清空佇列中的多個時間。 或者，您可以刪除佇列中，然後再建立一次。 若要刪除佇列中，執行下列**TSPSolver** (不**TSPClient**) 的命令。

        java -jar TSPSolver.jar deletequeue

    檢查所有路由之後，就會執行規劃求解。

## <a name="how-to-stop-the-java-applications"></a>如何停止 Java 應用程式
規劃求解及用戶端應用程式，您可以按**Ctrl + c 鍵**以結束如果您想要正常完成之前結束。


[solver_output]: ./media/virtual-machines-windows-classic-java-run-compute-intensive-task/WA_JavaTSPSolver.png
[client_output]: ./media/virtual-machines-windows-classic-java-run-compute-intensive-task/WA_JavaTSPClient.png
[svc_bus_node]: ./media/virtual-machines-windows-classic-java-run-compute-intensive-task/SvcBusQueues_02_SvcBusNode.jpg
[create_namespace]: ./media/virtual-machines-windows-classic-java-run-compute-intensive-task/SvcBusQueues_03_CreateNewSvcNamespace.jpg
[avail_namespaces]: ./media/virtual-machines-windows-classic-java-run-compute-intensive-task/SvcBusQueues_04_SvcBusNode_AvailNamespaces.jpg
[namespace_list]: ./media/virtual-machines-windows-classic-java-run-compute-intensive-task/SvcBusQueues_05_NamespaceList.jpg
[properties_pane]: ./media/virtual-machines-windows-classic-java-run-compute-intensive-task/SvcBusQueues_06_PropertiesPane.jpg
[default_key]: ./media/virtual-machines-windows-classic-java-run-compute-intensive-task/SvcBusQueues_07_DefaultKey.jpg
[add_ca_cert]: ../java-add-certificate-ca-store.md
