<properties
    pageTitle="建立使用 Maven 和 Java，HBase 應用程式，然後部署至 Linux 型 HDInsight |Microsoft Azure"
    description="瞭解如何使用 Apache Maven 建置 java Apache HBase 應用程式，然後將其部署至 Azure 雲端 Linux 為基礎的 HDInsight。"
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="larryfr"/>

#<a name="use-maven-to-build-java-applications-that-use-hbase-with-linux-based-hdinsight-hadoop"></a>使用 Maven 建置 Linux HDInsight (Hadoop) 搭配使用 HBase Java 應用程式

瞭解如何建立及使用 Apache Maven 建立 java [Apache HBase](http://hbase.apache.org/)應用程式。 與 Linux 型 HDInsight 叢集，然後使用應用程式。

[Maven](http://maven.apache.org/)是軟體的專案管理和理解工具，可讓您建立軟體、 文件和 Java 專案報表。 本文中，您將學習如何使用它建立基本的 Java 應用程式建立的查詢，並刪除以 Linux 型 HDInsight 叢集 HBase 資料表。

> [AZURE.NOTE] 這份文件中的步驟假設您使用的 Linux 型 HDInsight 叢集。 使用 Windows 型 HDInsight 叢集的資訊，請參閱[使用 Maven 建立 Java 應用程式的使用與 Windows 型 HDInsight HBase](hdinsight-hbase-build-java-maven.md)

##<a name="requirements"></a>需求

* [Java 平台 JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 7 或更新版本

* [Maven](http://maven.apache.org/)

* [使用 HBase Linux 型 Azure HDInsight 叢集](../hdinsight-hbase-tutorial-get-started-linux.md#create-hbase-cluster)

    > [AZURE.NOTE] HDInsight 叢集版本 3.2 捨位 3.3，與 3.4 測試此文件中的步驟。 範例中所提供的預設值是 HDInsight 3.4 叢集。

* **熟悉 SSH 和 SCP**。 如需有關使用 SSH 和 SCP HDInsight 的詳細資訊，請參閱下列各項︰

    * **Linux、 Unix 或 OS X 的用戶端**︰ 請參閱[使用 SSH Linux 為基礎的 Hadoop HDInsight Linux、 OS X 或 Unix 上使用](hdinsight-hadoop-linux-use-ssh-unix.md)

    * **Windows 用戶端**︰ 請參閱[使用 SSH Linux 為基礎的 Hadoop HDInsight 從 Windows 上使用](hdinsight-hadoop-linux-use-ssh-windows.md)

##<a name="create-the-project"></a>建立專案

1. 從命令列在您的開發環境中，變更目錄的位置，您要建立專案，例如`cd code/hdinsight`。

2. 使用 [ __mvn__ ] 命令，這與 Maven 安裝，產生專案的臨時平台。

        mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    這會建立新的目錄中目前的目錄，名稱指定__artifactID__參數 (在此範例中的**hbaseapp** )。此目錄會包含下列項目︰

    * __pom.xml__: 專案物件模型 ([POM](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html)) 包含用來建立專案的資訊及設定詳細資料。

    * __src__︰ 包含__主要/java/com/microsoft/範例__目錄，您會在此撰寫應用程式的目錄。

3. 刪除__src/test/java/com/microsoft/examples/apptest.java__檔案，因為它不會在此範例中使用。

##<a name="update-the-project-object-model"></a>更新專案物件模型

1. 編輯__pom.xml__檔案，並新增下列程式碼`<dependencies>`區段︰

        <dependency>
          <groupId>org.apache.hbase</groupId>
          <artifactId>hbase-client</artifactId>
          <version>1.1.2</version>
        </dependency>

    這就是告訴 Maven 專案需要__hbase 用戶端__版本__1.1.2__。 編譯時，這就會從下載預設 Maven 存放庫。 若要進一步瞭解此相依性，您可以使用[Maven 中央存放庫搜尋](http://search.maven.org/#artifactdetails%7Corg.apache.hbase%7Chbase-client%7C0.98.4-hadoop2%7Cjar)。

    > [AZURE.IMPORTANT] 版本號碼必須符合所提供的 HDInsight 叢集 HBase 的版本。 請使用下表找到正確的版本號碼。

  	| HDInsight 叢集版本 | 若要使用的 HBase 版本 |
  	| ----- | ----- |
  	| 3.2 捨位 | 0.98.4-hadoop2 |
  	| 3.3 和 3.4 | 1.1.2 |

    HDInsight 版本和元件的詳細資訊，請參閱[什麼是用於 HDInsight 的不同 Hadoop 元件](hdinsight-component-versioning.md)。

2. 如果您使用 HDInsight 3.3 或 3.4 叢集，您必須新增下列`<dependencies>`區段︰

        <dependency>
            <groupId>org.apache.phoenix</groupId>
            <artifactId>phoenix-core</artifactId>
            <version>4.4.0-HBase-1.1</version>
        </dependency>
    
    這樣就會載入鳳凰核心元件，所需使用 Hbase 版本 1.1.x。

2. 下列程式碼新增__pom.xml__檔案。 這必須是內部`<project>...</project>`標籤，在檔案]，例如之間`</dependencies>`和`</project>`。

        <build>
          <sourceDirectory>src</sourceDirectory>
          <resources>
            <resource>
              <directory>${basedir}/conf</directory>
              <filtering>false</filtering>
              <includes>
                <include>hbase-site.xml</include>
              </includes>
            </resource>
          </resources>
          <plugins>
            <plugin>
              <groupId>org.apache.maven.plugins</groupId>
              <artifactId>maven-compiler-plugin</artifactId>
                        <version>3.3</version>
              <configuration>
                <source>1.7</source>
                <target>1.7</target>
              </configuration>
            </plugin>
            <plugin>
              <groupId>org.apache.maven.plugins</groupId>
              <artifactId>maven-shade-plugin</artifactId>
              <version>2.3</version>
              <configuration>
                <transformers>
                  <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                  </transformer>
                </transformers>
              </configuration>
              <executions>
                <execution>
                  <phase>package</phase>
                  <goals>
                    <goal>shade</goal>
                  </goals>
                </execution>
              </executions>
            </plugin>
          </plugins>
        </build>

    這會設定資源 (__conf/hbase-site.xml__)，包含 HBase 的設定資訊。

    > [AZURE.NOTE] 您也可以設定程式碼的設定值。 請參閱執行此動作的__CreateTable__範例中的註解。

    這也會設定[Maven 編譯器外掛程式](http://maven.apache.org/plugins/maven-compiler-plugin/)和[Maven 上網底外掛程式](http://maven.apache.org/plugins/maven-shade-plugin/)。 外掛程式編譯器用來編譯拓撲。 外掛程式的陰影效果用來防止內建的 Maven JAR 封裝中的授權重複。 這用的原因是重複的授權檔案會造成 HDInsight 叢集上執行階段錯誤。 使用 maven-上網底-外掛程式與`ApacheLicenseResourceTransformer`實作讓這個錯誤。

    烏柏 jar （或 fat jar，），也會產生 maven 上網底外掛程式包含的所有應用程式所需的相依性。

3. 儲存__pom.xml__檔案。

4. 建立新名為__conf__ __hbaseapp__目錄中的目錄。 這將會用於保留連線到 HBase 的設定資訊。

5. 您可以使用下列命令，從 HDInsight 伺服器 HBase 設定複製到__conf__目錄。 取代**使用者名稱**您 SSH 登入名稱。 **CLUSTERNAME**取代 HDInsight 叢集名稱︰

        scp USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml ./conf/hbase-site.xml

    > [AZURE.NOTE] 如果您使用 SSH 帳戶的密碼，系統會提示您輸入密碼。 如果您使用 SSH 鍵帳戶，您可能需要使用`-i`參數指定重要的檔案路徑。 下列範例會載入從私密金鑰`~/.ssh/id_rsa`:
    >
    > `scp -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml ./conf/hbase-site.xml`

##<a name="create-the-application"></a>建立應用程式

1. 移至 [ __hbaseapp/src/主要/java/com/microsoft/範例__目錄，然後重新命名__CreateTable.java__app.java 檔案。

2. 開啟__CreateTable.java__檔案，並以下列取代現有的內容︰

        package com.microsoft.examples;
        import java.io.IOException;

        import org.apache.hadoop.conf.Configuration;
        import org.apache.hadoop.hbase.HBaseConfiguration;
        import org.apache.hadoop.hbase.client.HBaseAdmin;
        import org.apache.hadoop.hbase.HTableDescriptor;
        import org.apache.hadoop.hbase.TableName;
        import org.apache.hadoop.hbase.HColumnDescriptor;
        import org.apache.hadoop.hbase.client.HTable;
        import org.apache.hadoop.hbase.client.Put;
        import org.apache.hadoop.hbase.util.Bytes;

        public class CreateTable {
          public static void main(String[] args) throws IOException {
            Configuration config = HBaseConfiguration.create();

            // Example of setting zookeeper values for HDInsight
            // in code instead of an hbase-site.xml file
            //
            // config.set("hbase.zookeeper.quorum",
            //            "zookeepernode0,zookeepernode1,zookeepernode2");
            //config.set("hbase.zookeeper.property.clientPort", "2181");
            //config.set("hbase.cluster.distributed", "true");
            //
            //NOTE: Actual zookeeper host names can be found using Ambari:
            //curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts"
            
            //Linux-based HDInsight clusters use /hbase-unsecure as the znode parent
            config.set("zookeeper.znode.parent","/hbase-unsecure");

            // create an admin object using the config
            HBaseAdmin admin = new HBaseAdmin(config);

            // create the table...
            HTableDescriptor tableDescriptor = new HTableDescriptor(TableName.valueOf("people"));
            // ... with two column families
            tableDescriptor.addFamily(new HColumnDescriptor("name"));
            tableDescriptor.addFamily(new HColumnDescriptor("contactinfo"));
            admin.createTable(tableDescriptor);

            // define some people
            String[][] people = {
                { "1", "Marcel", "Haddad", "marcel@fabrikam.com"},
                { "2", "Franklin", "Holtz", "franklin@contoso.com" },
                { "3", "Dwayne", "McKee", "dwayne@fabrikam.com" },
                { "4", "Rae", "Schroeder", "rae@contoso.com" },
                { "5", "Rosalie", "burton", "rosalie@fabrikam.com"},
                { "6", "Gabriela", "Ingram", "gabriela@contoso.com"} };

            HTable table = new HTable(config, "people");

            // Add each person to the table
            //   Use the `name` column family for the name
            //   Use the `contactinfo` column family for the email
            for (int i = 0; i< people.length; i++) {
              Put person = new Put(Bytes.toBytes(people[i][0]));
              person.add(Bytes.toBytes("name"), Bytes.toBytes("first"), Bytes.toBytes(people[i][1]));
              person.add(Bytes.toBytes("name"), Bytes.toBytes("last"), Bytes.toBytes(people[i][2]));
              person.add(Bytes.toBytes("contactinfo"), Bytes.toBytes("email"), Bytes.toBytes(people[i][3]));
              table.put(person);
            }
            // flush commits and close the table
            table.flushCommits();
            table.close();
          }
        }

    這是__CreateTable__類別，這樣會建立一個名為__人員__資料表填入部分預先定義的使用者。

3. 儲存__CreateTable.java__檔案。

4. 在__hbaseapp/src/主要/java/com/microsoft/範例__目錄中，建立名為__SearchByEmail.java__的新檔案。 使用下列做為此檔案的內容︰

        package com.microsoft.examples;
        import java.io.IOException;

        import org.apache.hadoop.conf.Configuration;
        import org.apache.hadoop.hbase.HBaseConfiguration;
        import org.apache.hadoop.hbase.client.HTable;
        import org.apache.hadoop.hbase.client.Scan;
        import org.apache.hadoop.hbase.client.ResultScanner;
        import org.apache.hadoop.hbase.client.Result;
        import org.apache.hadoop.hbase.filter.RegexStringComparator;
        import org.apache.hadoop.hbase.filter.SingleColumnValueFilter;
        import org.apache.hadoop.hbase.filter.CompareFilter.CompareOp;
        import org.apache.hadoop.hbase.util.Bytes;
        import org.apache.hadoop.util.GenericOptionsParser;

        public class SearchByEmail {
          public static void main(String[] args) throws IOException {
            Configuration config = HBaseConfiguration.create();

            // Use GenericOptionsParser to get only the parameters to the class
            // and not all the parameters passed (when using WebHCat for example)
            String[] otherArgs = new GenericOptionsParser(config, args).getRemainingArgs();
            if (otherArgs.length != 1) {
              System.out.println("usage: [regular expression]");
              System.exit(-1);
            }

            // Open the table
            HTable table = new HTable(config, "people");

            // Define the family and qualifiers to be used
            byte[] contactFamily = Bytes.toBytes("contactinfo");
            byte[] emailQualifier = Bytes.toBytes("email");
            byte[] nameFamily = Bytes.toBytes("name");
            byte[] firstNameQualifier = Bytes.toBytes("first");
            byte[] lastNameQualifier = Bytes.toBytes("last");

            // Create a new regex filter
            RegexStringComparator emailFilter = new RegexStringComparator(otherArgs[0]);
            // Attach the regex filter to a filter
            //   for the email column
            SingleColumnValueFilter filter = new SingleColumnValueFilter(
              contactFamily,
              emailQualifier,
              CompareOp.EQUAL,
              emailFilter
            );

            // Create a scan and set the filter
            Scan scan = new Scan();
            scan.setFilter(filter);

            // Get the results
            ResultScanner results = table.getScanner(scan);
            // Iterate over results and print  values
            for (Result result : results ) {
              String id = new String(result.getRow());
              byte[] firstNameObj = result.getValue(nameFamily, firstNameQualifier);
              String firstName = new String(firstNameObj);
              byte[] lastNameObj = result.getValue(nameFamily, lastNameQualifier);
              String lastName = new String(lastNameObj);
              System.out.println(firstName + " " + lastName + " - ID: " + id);
              byte[] emailObj = result.getValue(contactFamily, emailQualifier);
              String email = new String(emailObj);
              System.out.println(firstName + " " + lastName + " - " + email + " - ID: " + id);
            }
            results.close();
            table.close();
          }
        }

    可以使用__SearchByEmail__類別至查詢的資料列，並以電子郵件地址。 因為其使用規則運算式篩選，您可以使用類別時提供字串或規則運算式。

5. 儲存__SearchByEmail.java__檔案。

6. 在__hbaseapp/src/主要/hava/com/microsoft/範例__目錄中，建立名為__DeleteTable.java__的新檔案。 使用下列做為此檔案的內容︰

        package com.microsoft.examples;
        import java.io.IOException;

        import org.apache.hadoop.conf.Configuration;
        import org.apache.hadoop.hbase.HBaseConfiguration;
        import org.apache.hadoop.hbase.client.HBaseAdmin;

        public class DeleteTable {
          public static void main(String[] args) throws IOException {
            Configuration config = HBaseConfiguration.create();

            // Create an admin object using the config
            HBaseAdmin admin = new HBaseAdmin(config);

            // Disable, and then delete the table
            admin.disableTable("people");
            admin.deleteTable("people");
          }
        }

    這個類別是可以清除此範例中，以停用拖放__CreateTable__類別建立的表格。

7. 儲存__DeleteTable.java__檔案。

##<a name="build-and-package-the-application"></a>建立及封裝應用程式

2. 從__hbaseapp__目錄，請建立 JAR 檔案包含應用程式使用下列命令︰

        mvn clean package

    這會清除上一個的任何建立成品、 下載您尚未安裝任何相依性，然後建置和封裝應用程式。

3. 命令完成後，請__hbaseapp/目標__目錄會包含一個名為__hbaseapp-1.0-SNAPSHOT.jar__檔案。

    > [AZURE.NOTE] __hbaseapp-1.0-SNAPSHOT.jar__檔案是烏柏 jar （有時稱為 fat 糖，） 其中包含執行應用程式所需的相依性。

##<a name="upload-the-jar-file-and-run-jobs"></a>上傳 JAR 檔案，並執行工作

1. 使用下列糖其上傳到 HDInsight 叢集。 取代**使用者名稱**您 SSH 登入名稱。 **CLUSTERNAME**取代 HDInsight 叢集名稱︰

        scp ./target/hbaseapp-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.

    這會將檔案上傳至您 SSH 的使用者帳戶的主目錄。

    > [AZURE.NOTE] 如果您使用 SSH 帳戶的密碼，系統會提示您輸入密碼。 如果您使用 SSH 鍵帳戶，您可能需要使用`-i`參數指定重要的檔案路徑。 下列範例會載入從私密金鑰`~/.ssh/id_rsa`:
    >
    > `scp -i ~/.ssh/id_rsa ./target/hbaseapp-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.`

2. 使用 SSH HDInsight 叢集連線。 取代**使用者名稱**您 SSH 登入名稱。 **CLUSTERNAME**取代 HDInsight 叢集名稱︰

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    > [AZURE.NOTE] 如果您使用 SSH 帳戶的密碼，系統會提示您輸入密碼。 如果您使用 SSH 鍵帳戶，您可能需要使用`-i`參數指定重要的檔案路徑。 下列範例會載入從私密金鑰`~/.ssh/id_rsa`:
    >
    > `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`

3. 連線後，使用下列命令以建立新的 HBase 資料表使用 Java 應用程式︰

        hadoop jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.CreateTable

    這會建立新的 HBase 表格命名為 [__人員__]，並填入資料。

4. 接下來，請使用下列搜尋儲存在資料表中的電子郵件地址︰

        hadoop jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.SearchByEmail contoso.com

    您應該會收到下列結果︰

        Franklin Holtz - ID: 2
        Franklin Holtz - franklin@contoso.com - ID: 2
        Rae Schroeder - ID: 4
        Rae Schroeder - rae@contoso.com - ID: 4
        Gabriela Ingram - ID: 6
        Gabriela Ingram - gabriela@contoso.com - ID: 6

##<a name="delete-the-table"></a>刪除表格

當您完成的範例時，使用下列命令從 Azure PowerShell 工作階段，刪除此範例中的__人員__表格︰

    hadoop jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.DeleteTable

