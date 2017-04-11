<properties
pageTitle="建立使用 Maven，HBase 應用程式並部署至 Windows 型 HDInsight |Microsoft Azure"
description="瞭解如何使用建置 java Apache HBase 應用程式，然後將其部署到 windows Azure HDInsight 叢集 Apache Maven。"
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="jhubbard"
editor="cgronlun"
tags="azure-portal"/>

<tags
ms.service="hdinsight"
ms.workload="big-data"
ms.tgt_pltfrm="na"
ms.devlang="na"
ms.topic="article"
ms.date="10/03/2016"
ms.author="larryfr"/>

#<a name="use-maven-to-build-java-applications-that-use-hbase-with-windows-based-hdinsight-hadoop"></a>使用 Maven 建置 HBase 使用 Windows 型 HDInsight (Hadoop) Java 應用程式

瞭解如何建立及使用 Apache Maven 建立 java [Apache HBase](http://hbase.apache.org/)應用程式。 使用 Azure HDInsight (Hadoop)，然後使用應用程式。

[Maven](http://maven.apache.org/)是軟體的專案管理和理解工具，可讓您建立軟體、 文件和 Java 專案報表。 本文中，您可以瞭解如何使用它來建立基本的 Java 應用程式所建立的查詢，並刪除 Azure HDInsight 叢集 HBase 資料表。

> [AZURE.NOTE] 這份文件中的步驟假設您使用的 Windows 型 HDInsight 叢集。 使用 Linux 型 HDInsight 叢集的資訊，請參閱[使用 Maven 建立 Java 應用程式的使用與 Linux 型 HDInsight HBase](hdinsight-hbase-build-java-maven-linux.md)

##<a name="requirements"></a>需求

* [Java 平台 JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 7 或更新版本

* [Maven](http://maven.apache.org/)


* [Windows 型 HDInsight 叢集與 HBase](hdinsight-hbase-tutorial-get-started.md#create-hbase-cluster)


    > [AZURE.NOTE] HDInsight 叢集版本 3.2 捨位及 3.3 測試此文件中的步驟進行。 範例中所提供的預設值是 HDInsight 3.3 叢集。

##<a name="create-the-project"></a>建立專案

1. 從命令列在您的開發環境中，變更目錄的位置，您要建立專案，例如`cd code\hdinsight`。

2. 使用 [ __mvn__ ] 命令，這與 Maven 安裝，產生專案的臨時平台。

        mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    這個命令在目前的位置，建立目錄名稱指定__artifactID__參數 (在此範例中的**hbaseapp** )。此目錄包含下列項目︰

    * __pom.xml__: 專案物件模型 ([POM](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html)) 包含用來建立專案的資訊及設定詳細資料。

    * __src__︰ 包含__main\java\com\microsoft\examples__目錄，您會在此撰寫應用程式的目錄。

3. 刪除__src\test\java\com\microsoft\examples\apptest.java__檔案，因為它不會在此範例中使用。

##<a name="update-the-project-object-model"></a>更新專案物件模型

1. 編輯__pom.xml__檔案，並新增下列程式碼`<dependencies>`區段︰

        <dependency>
          <groupId>org.apache.hbase</groupId>
          <artifactId>hbase-client</artifactId>
          <version>1.1.2</version>
        </dependency>

    本節說明 Maven 專案需要__hbase 用戶端__版本__1.1.2__。 編譯時，此相依性是從預設 Maven 存放庫下載。 若要進一步瞭解此相依性，您可以使用[Maven 中央存放庫搜尋](http://search.maven.org/#artifactdetails%7Corg.apache.hbase%7Chbase-client%7C0.98.4-hadoop2%7Cjar)。

    > [AZURE.IMPORTANT] 版本號碼必須符合所提供的 HDInsight 叢集 HBase 的版本。 請使用下表找到正確的版本號碼。

  	| HDInsight 叢集版本 | 若要使用的 HBase 版本 |
  	| ----- | ----- |
  	| 3.2 捨位 | 0.98.4-hadoop2 |
  	| 3.3 | 1.1.2 |

    HDInsight 版本和元件的詳細資訊，請參閱[什麼是用於 HDInsight 的不同 Hadoop 元件](hdinsight-component-versioning.md)。

2. 如果您使用的 HDInsight 3.3 叢集，您必須新增下列`<dependencies>`區段︰

        <dependency>
            <groupId>org.apache.phoenix</groupId>
            <artifactId>phoenix-core</artifactId>
            <version>4.4.0-HBase-1.1</version>
        </dependency>
    
    此相依性會載入鳳凰核心元件，可使用 Hbase 版本 1.1.x。

2. 下列程式碼新增__pom.xml__檔案。 本節必須內`<project>...</project>`標籤，在檔案]，例如之間`</dependencies>`和`</project>`。

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

    `<resources>`區段會設定資源 (__conf\hbase site.xml__) 包含 HBase 的設定資訊。

    > [AZURE.NOTE] 您也可以設定程式碼的設定值。 請參閱執行此動作的__CreateTable__範例中的註解。

    此`<plugins>`區段設定[Maven 編譯器外掛程式](http://maven.apache.org/plugins/maven-compiler-plugin/)和[Maven 上網底外掛程式](http://maven.apache.org/plugins/maven-shade-plugin/)。 外掛程式編譯器用來編譯拓撲。 外掛程式的陰影效果用來防止授權重複 JAR 套件內建的 Maven 中。 這用的原因是重複的授權檔案會造成 HDInsight 叢集上執行階段錯誤。 使用 maven-上網底-外掛程式與`ApacheLicenseResourceTransformer`實作讓這個錯誤。

    烏柏 jar （或 fat jar），也會產生 maven 上網底外掛程式包含的所有應用程式所需的相依性。

3. 儲存__pom.xml__檔案。

4. 建立新名為__conf__ __hbaseapp__目錄中的目錄。 在__conf__目錄中，建立一個名為__hbase site.xml__檔案。 使用下列檔案的內容︰

        <?xml version="1.0"?>
        <?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
        <!--
        /**
          * Copyright 2010 The Apache Software Foundation
          *
          * Licensed to the Apache Software Foundation (ASF) under one
          * or more contributor license agreements.  See the NOTICE file
          * distributed with this work for additional information
          * regarding copyright ownership.  The ASF licenses this file
          * to you under the Apache License, Version 2.0 (the
          * "License"); you may not use this file except in compliance
          * with the License.  You may obtain a copy of the License at
          *
          *     http://www.apache.org/licenses/LICENSE-2.0
          *
          * Unless required by applicable law or agreed to in writing, software
          * distributed under the License is distributed on an "AS IS" BASIS,
          * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
          * See the License for the specific language governing permissions and
          * limitations under the License.
          */
        -->
        <configuration>
          <property>
            <name>hbase.cluster.distributed</name>
            <value>true</value>
          </property>
          <property>
            <name>hbase.zookeeper.quorum</name>
            <value>zookeeper0,zookeeper1,zookeeper2</value>
          </property>
          <property>
            <name>hbase.zookeeper.property.clientPort</name>
            <value>2181</value>
          </property>
        </configuration>

    這個檔案會用載入 HDInsight 叢集 HBase 設定。

    > [AZURE.NOTE] 這是最小的 hbase site.xml 檔案]，並包含 HDInsight 叢集不包裝的最小值設定。

3. 儲存__hbase site.xml__檔案。

##<a name="create-the-application"></a>建立應用程式

1. 移至 [ __hbaseapp\src\main\java\com\microsoft\examples__目錄，然後重新命名__CreateTable.java__app.java 檔案。

2. 開啟__CreateTable.java__檔案，並以下列程式碼取代現有的內容︰

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
            // The following sets the znode root for Linux-based HDInsight
            //config.set("zookeeper.znode.parent","/hbase-unsecure");

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

4. 在__hbaseapp\src\main\java\com\microsoft\examples__目錄中，建立名為__SearchByEmail.java__的新檔案。 使用下列程式碼做為此檔案的內容︰

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

6. 在__hbaseapp\src\main\hava\com\microsoft\examples__目錄中，建立名為__DeleteTable.java__的新檔案。 使用下列程式碼做為此檔案的內容︰

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

1. 開啟命令提示字元中，將變更目錄__hbaseapp__目錄。

2. 若要建立 JAR 檔案包含應用程式中使用下列命令︰

        mvn clean package

    這會清除上一個的任何建立成品，下載您尚未安裝任何相依性，然後建立並封裝應用程式。

3. 命令完成後，請__hbaseapp\target__目錄會包含一個名為__hbaseapp-1.0-SNAPSHOT.jar__檔案。

    > [AZURE.NOTE] __hbaseapp-1.0-SNAPSHOT.jar__檔案是烏柏 jar （有時稱為 fat 糖，） 其中包含執行應用程式所需的相依性。

##<a name="upload-the-jar-file-and-start-a-job"></a>上傳 JAR 檔案並開始工作

有多種方式將檔案上傳到您的 HDInsight 叢集[上, 傳 Hadoop 工作 HDInsight 的資料](hdinsight-upload-data.md)所述。 下列步驟使用 PowerShell 的 Azure。

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]


1. 安裝並設定 PowerShell 的 Azure 之後, 建立新檔案命名__hbase runner.psm1__。 使用下列做為此檔案的內容︰

        <#
        .SYNOPSIS
        Copies a file to the primary storage of an HDInsight cluster.
        .DESCRIPTION
        Copies a file from a local directory to the blob container for
        the HDInsight cluster.
        .EXAMPLE
        Start-HBaseExample -className "com.microsoft.examples.CreateTable"
        -clusterName "MyHDInsightCluster"
        
        .EXAMPLE
        Start-HBaseExample -className "com.microsoft.examples.SearchByEmail"
        -clusterName "MyHDInsightCluster"
        -emailRegex "contoso.com"
        
        .EXAMPLE
        Start-HBaseExample -className "com.microsoft.examples.SearchByEmail"
        -clusterName "MyHDInsightCluster"
        -emailRegex "^r" -showErr
        #>
        
        function Start-HBaseExample {
        [CmdletBinding(SupportsShouldProcess = $true)]
        param(
        #The class to run
        [Parameter(Mandatory = $true)]
        [String]$className,
        
        #The name of the HDInsight cluster
        [Parameter(Mandatory = $true)]
        [String]$clusterName,
        
        #Only used when using SearchByEmail
        [Parameter(Mandatory = $false)]
        [String]$emailRegex,
        
        #Use if you want to see stderr output
        [Parameter(Mandatory = $false)]
        [Switch]$showErr
        )
        
        Set-StrictMode -Version 3
        
        # Is the Azure module installed?
        FindAzure
        
        # Get the login for the HDInsight cluster
        $creds = Get-Credential
        
        # Get storage information
        $storage = GetStorage -clusterName $clusterName
        
        # The JAR
        $jarFile = "wasbs:///example/jars/hbaseapp-1.0-SNAPSHOT.jar"
        
        # The job definition
        $jobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
            -JarFile $jarFile `
            -ClassName $className `
            -Arguments $emailRegex
        
        # Get the job output
        $job = Start-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobDefinition $jobDefinition `
            -HttpCredential $creds
        Write-Host "Wait for the job to complete ..." -ForegroundColor Green
        Wait-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobId $job.JobId `
            -HttpCredential $creds
        if($showErr)
        {
        Write-Host "STDERR"
        Get-AzureRmHDInsightJobOutput `
                    -Clustername $clusterName `
                    -JobId $job.JobId `
                    -DefaultContainer $storage.container `
                    -DefaultStorageAccountName $storage.storageAccount `
                    -DefaultStorageAccountKey $storage.storageAccountKey `
                    -HttpCredential $creds `
                    -DisplayOutputType StandardError
        }
        Write-Host "Display the standard output ..." -ForegroundColor Green
        Get-AzureRmHDInsightJobOutput `
                    -Clustername $clusterName `
                    -JobId $job.JobId `
                    -DefaultContainer $storage.container `
                    -DefaultStorageAccountName $storage.storageAccount `
                    -DefaultStorageAccountKey $storage.storageAccountKey `
                    -HttpCredential $creds
        }
        
        <#
        .SYNOPSIS
        Copies a file to the primary storage of an HDInsight cluster.
        .DESCRIPTION
        Copies a file from a local directory to the blob container for
        the HDInsight cluster.
        .EXAMPLE
        Add-HDInsightFile -localPath "C:\temp\data.txt"
        -destinationPath "example/data/data.txt"
        -ClusterName "MyHDInsightCluster"
        .EXAMPLE
        Add-HDInsightFile -localPath "C:\temp\data.txt"
        -destinationPath "example/data/data.txt"
        -ClusterName "MyHDInsightCluster"
        -Container "MyContainer"
        #>
        
        function Add-HDInsightFile {
            [CmdletBinding(SupportsShouldProcess = $true)]
            param(
                #The path to the local file.
                [Parameter(Mandatory = $true)]
                [String]$localPath,
                
                #The destination path and file name, relative to the root of the container.
                [Parameter(Mandatory = $true)]
                [String]$destinationPath,
                
                #The name of the HDInsight cluster
                [Parameter(Mandatory = $true)]
                [String]$clusterName,
                
                #If specified, overwrites existing files without prompting
                [Parameter(Mandatory = $false)]
                [Switch]$force
            )
            
            Set-StrictMode -Version 3
            
            # Is the Azure module installed?
            FindAzure
            
            # Get authentication for the cluster
            $creds=Get-Credential
            
            # Does the local path exist?
            if (-not (Test-Path $localPath))
            {
                throw "Source path '$localPath' does not exist."
            }
            
            # Get the primary storage container
            $storage = GetStorage -clusterName $clusterName
            
            # Upload file to storage, overwriting existing files if -force was used.
            Set-AzureStorageBlobContent -File $localPath `
                -Blob $destinationPath `
                -force:$force `
                -Container $storage.container `
                -Context $storage.context
        }
        
        function FindAzure {
            # Is there an active Azure subscription?
            $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
            if(-not($sub))
            {
                throw "No active Azure subscription found! If you have a subscription, use the Login-AzureRmAccount cmdlet to login to your subscription."
            }
        }
        
        function GetStorage {
            param(
                [Parameter(Mandatory = $true)]
                [String]$clusterName
            )
            $hdi = Get-AzureRmHDInsightCluster -ClusterName $clusterName
            # Does the cluster exist?
            if (!$hdi)
            {
                throw "HDInsight cluster '$clusterName' does not exist."
            }
            # Create a return object for context & container
            $return = @{}
            $storageAccounts = @{}
            
            # Get storage information
            $resourceGroup = $hdi.ResourceGroup
            $storageAccountName=$hdi.DefaultStorageAccount.split('.')[0]
            $container=$hdi.DefaultStorageContainer
            $storageAccountKey=(Get-AzureRmStorageAccountKey `
                -Name $storageAccountName `
            -ResourceGroupName $resourceGroup)[0].Value
            # Get the resource group, in case we need that
            $return.resourceGroup = $resourceGroup
            # Get the storage context, as we can't depend
            # on using the default storage context
            $return.context = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
            # Get the container, so we know where to
            # find/store blobs
            $return.container = $container
            # Return storage accounts to support finding all accounts for
            # a cluster
            $return.storageAccount = $storageAccountName
            $return.storageAccountKey = $storageAccountKey
            
            return $return
        }
        # Only export the verb-phrase things
        export-modulemember *-*

    檔案包含兩個模組︰

    * __新增 HDInsightFile__ -用來上傳檔案到 HDInsight

    * __開始 HBaseExample__ -用來執行先前建立的類別

2. 儲存__hbase runner.psm1__檔案。

3. 開啟新的 PowerShell 的 Azure 視窗、 __hbaseapp__目錄，變更的目錄，然後執行下列命令。

        PS C:\ Import-Module c:\path\to\hbase-runner.psm1

    變更先前建立的__hbase runner.psm1__檔案的位置的路徑。 這會模組註冊這個 PowerShell 的 Azure 工作階段。

2. 使用下列命令__hbaseapp-1.0-SNAPSHOT.jar__其上傳到您的 HDInsight 叢集。

        Add-HDInsightFile -localPath target\hbaseapp-1.0-SNAPSHOT.jar -destinationPath example/jars/hbaseapp-1.0-SNAPSHOT.jar -clusterName hdinsightclustername

    __Hdinsightclustername__換成您 HDInsight 叢集的名稱。 命令上載__hbaseapp-1.0-SNAPSHOT.jar__中 HDInsight 叢集主要的儲存空間__（每瓶範例/）__位置。

3. 上傳檔案之後，請使用下列程式碼來建立表格，使用__hbaseapp__:

        Start-HBaseExample -className com.microsoft.examples.CreateTable -clusterName hdinsightclustername

    __Hdinsightclustername__換成您 HDInsight 叢集的名稱。

    這個命令會建立新資料表命名 HDInsight 叢集中的__人員__。 這個命令主控台視窗中不顯示任何輸出。

2. 若要搜尋的資料表中的項目，請使用下列命令︰

        Start-HBaseExample -className com.microsoft.examples.SearchByEmail -clusterName hdinsightclustername -emailRegex contoso.com

    __Hdinsightclustername__換成您 HDInsight 叢集的名稱。

    這個命令使用**SearchByEmail**類別搜尋__contactinformation__欄系列與__電子郵件__欄中，包含字串__contoso.com__任何列。 您應該會收到下列結果︰

          Franklin Holtz - ID: 2
          Franklin Holtz - franklin@contoso.com - ID: 2
          Rae Schroeder - ID: 4
          Rae Schroeder - rae@contoso.com - ID: 4
          Gabriela Ingram - ID: 6
          Gabriela Ingram - gabriela@contoso.com - ID: 6

    使用__fabrikam.com__的`-emailRegex`值傳回有__fabrikam.com__電子郵件] 欄位中的使用者。 由於此搜尋實作使用規則運算式為基礎的篩選後，您也可以輸入規則運算式，例如__^ r__，電子郵件，字母 「 r 」 開頭的會傳回項目。

##<a name="delete-the-table"></a>刪除表格

當您完成的範例時，使用下列命令從 Azure PowerShell 工作階段，刪除此範例中的__人員__表格︰

    Start-HBaseExample -className com.microsoft.examples.DeleteTable -clusterName hdinsightclustername

__Hdinsightclustername__換成您 HDInsight 叢集的名稱。

##<a name="troubleshooting"></a>疑難排解

###<a name="no-results-or-unexpected-results-when-using-start-hbaseexample"></a>沒有結果或非預期的結果時使用開始 HBaseExample

使用`-showErr`參數以檢視執行工作時，會產生的標準誤差 (STDERR)。
