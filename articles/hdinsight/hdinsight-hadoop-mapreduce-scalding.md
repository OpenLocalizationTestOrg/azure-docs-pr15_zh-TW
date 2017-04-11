<properties
 pageTitle="開發 Maven Scalding MapReduce 工作 |Microsoft Azure"
 description="瞭解如何使用 Maven 建立 Scalding MapReduce 工作，然後部署並上 Hadoop HDInsight 叢集上執行作業。"
 services="hdinsight"
 documentationCenter=""
 authors="Blackmist"
 manager="jhubbard"
 editor="cgronlun"
    tags="azure-portal"/>
<tags
 ms.service="hdinsight"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="big-data"
 ms.date="10/18/2016"
 ms.author="larryfr"/>

# <a name="develop-scalding-mapreduce-jobs-with-apache-hadoop-on-hdinsight"></a>開發上 HDInsight Apache Hadoop Scalding MapReduce 工作

Scalding 是 Scala 文件庫，可讓您更輕鬆地建立 Hadoop MapReduce 工作。 提供精簡的語法，以及與 Scala 緊密整合。

在此文件，瞭解如何使用 Maven 建立基本的 word 計算 MapReduce 工作撰寫 Scalding。 然後，您將學習如何部署和 HDInsight 叢集上執行此工作。

## <a name="prerequisites"></a>必要條件

- **Azure 訂閱**。 請參閱[取得 Azure 免費試用版](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
* **在 Windows 或 Linux 基礎 Hadoop HDInsight 叢集上**。 如需詳細資訊，請參閱[在 HDInsight 佈建 Linux 為基礎的 Hadoop](hdinsight-hadoop-provision-linux-clusters.md)或[佈建 Windows 型的 Hadoop HDInsight 上](hdinsight-provision-clusters.md)。

* **[Maven](http://maven.apache.org/)**

* **[Java 平台 JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 7 或更新版本**

## <a name="create-and-build-the-project"></a>建立並建立專案

1. 若要建立新的 Maven 專案中使用下列命令︰

        mvn archetype:generate -DgroupId=com.microsoft.example -DartifactId=scaldingwordcount -DarchetypeGroupId=org.scala-tools.archetypes -DarchetypeArtifactId=scala-archetype-simple -DinteractiveMode=false

    這個命令將會建立名為**scaldingwordcount**，新的目錄，並建立臨時平台 Scala 應用程式。

2. 在**scaldingwordcount**目錄中，請開啟**pom.xml**檔案，並以下列取代內容︰

        <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
            <modelVersion>4.0.0</modelVersion>
            <groupId>com.microsoft.example</groupId>
            <artifactId>scaldingwordcount</artifactId>
            <version>1.0-SNAPSHOT</version>
            <name>${project.artifactId}</name>
            <properties>
            <maven.compiler.source>1.6</maven.compiler.source>
            <maven.compiler.target>1.6</maven.compiler.target>
            <encoding>UTF-8</encoding>
            </properties>
            <repositories>
            <repository>
                <id>conjars</id>
                <url>http://conjars.org/repo</url>
            </repository>
            <repository>
                <id>maven-central</id>
                <url>http://repo1.maven.org/maven2</url>
            </repository>
            </repositories>
            <dependencies>
            <dependency>
                <groupId>com.twitter</groupId>
                <artifactId>scalding-core_2.11</artifactId>
                <version>0.13.1</version>
            </dependency>
            <dependency>
                <groupId>org.apache.hadoop</groupId>
                <artifactId>hadoop-core</artifactId>
                <version>1.2.1</version>
                <scope>provided</scope>
            </dependency>
            </dependencies>
            <build>
            <sourceDirectory>src/main/scala</sourceDirectory>
            <plugins>
                <plugin>
                <groupId>org.scala-tools</groupId>
                <artifactId>maven-scala-plugin</artifactId>
                <version>2.15.2</version>
                <executions>
                    <execution>
                    <id>scala-compile-first</id>
                    <phase>process-resources</phase>
                    <goals>
                        <goal>add-source</goal>
                        <goal>compile</goal>
                    </goals>
                    </execution>
                </executions>
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
                    <filters>
                    <filter>
                        <artifact>*:*</artifact>
                        <excludes>
                        <exclude>META-INF/*.SF</exclude>
                        <exclude>META-INF/*.DSA</exclude>
                        <exclude>META-INF/*.RSA</exclude>
                        </excludes>
                    </filter>
                    </filters>
                </configuration>
                <executions>
                    <execution>
                    <phase>package</phase>
                    <goals>
                        <goal>shade</goal>
                    </goals>
                    <configuration>
                        <transformers>
                        <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                            <mainClass>com.twitter.scalding.Tool</mainClass>
                        </transformer>
                        </transformers>
                    </configuration>
                    </execution>
                </executions>
                </plugin>
            </plugins>
            </build>
        </project>

    此檔案說明專案、 相依性及外掛程式]。 以下是重要的項目︰

    * **maven.compiler.source**和**maven.compiler.target**︰ 設定為此專案的 Java 版本

    * **存放庫**︰ 包含此專案使用的相依性檔案存放庫

    * **scalding core_2.11**和**hadoop 核心**︰ 此專案取決於 Scalding 和 Hadoop 核心套件

    * **外掛程式 scala maven 用**︰ 編譯 scala 應用程式的外掛程式

    * **插上網底 maven 件**︰ 若要建立的外掛程式會加上陰影 (fat) （每瓶）。 此外掛程式適用於篩選器及轉換;specificially:

        * **篩選**︰ 套用的篩選修改隨附 jar 檔案中的中繼資訊。 若要防止登執行階段的例外狀況，不等各種可能包含的相依性的簽章檔案。

        * **執行**︰ 套件階段執行設定指定的**com.twitter.scalding.Tool**類別套件的主要類別。 不這麼做，您必須指定 com.twitter.scalding.Tool，以及 [hadoop] 命令以執行工作時包含的應用程式邏輯的類別。

3. 當您將不會建立測試此範例中，請刪除**src/測試**目錄。

4. 開啟**src/main/scala/com/microsoft/example/App.scala**檔案，並以下列取代內容︰

        package com.microsoft.example

        import com.twitter.scalding._

        class WordCount(args : Args) extends Job(args) {
            // 1. Read lines from the specified input location
            // 2. Extract individual words from each line
            // 3. Group words and count them
            // 4. Write output to the specified output location
            TextLine(args("input"))
            .flatMap('line -> 'word) { line : String => tokenize(line) }
            .groupBy('word) { _.size }
            .write(Tsv(args("output")))

            //Tokenizer to split sentance into words
            def tokenize(text : String) : Array[String] = {
            text.toLowerCase.replaceAll("[^a-zA-Z0-9\\s]", "").split("\\s+")
            }
        }

    這會執行基本的 word 計算工作。

5. 儲存並關閉檔案。

6. 使用下列命令，從**scaldingwordcount**目錄建立及封裝應用程式︰

        mvn package

    這項作業完成後，可以在**target/scaldingwordcount-1.0-SNAPSHOT.jar**找到包含 WordCount 應用程式套件。

## <a name="run-the-job-on-a-linux-based-cluster"></a>在 Linux 叢集上執行工作

> [AZURE.NOTE] 下列步驟使用 SSH 和 Hadoop] 命令。 執行 MapReduce 工作的其他方法，請參閱[使用 MapReduce 上 HDInsight Hadoop 中](hdinsight-use-mapreduce.md)。

1. 您可以使用下列命令套件上傳至您的 HDInsight 叢集︰

        scp target/scaldingwordcount-1.0-SNAPSHOT.jar username@clustername-ssh.azurehdinsight.net:

    這會從本機系統複製檔案到主節點。

    > [AZURE.NOTE] 如果您是使用密碼保護 SSH 帳號，系統會提示您輸入密碼。 如果您使用 SSH 鍵，您可能必須使用`-i`參數和私密金鑰的路徑。 例如，`scp -i /path/to/private/key target/scaldingwordcount-1.0-SNAPSHOT.jar username@clustername-ssh.azurehdinsight.net:.`

2. 連線至叢集主節點使用下列命令︰

        ssh username@clustername-ssh.azurehdinsight.net

    > [AZURE.NOTE] 如果您是使用密碼保護 SSH 帳號，系統會提示您輸入密碼。 如果您使用 SSH 鍵，您可能必須使用`-i`參數和私密金鑰的路徑。 例如，`ssh -i /path/to/private/key username@clustername-ssh.azurehdinsight.net`

3. 連線到主節點後，使用下列命令以執行 word 計算工作

        yarn jar scaldingwordcount-1.0-SNAPSHOT.jar com.microsoft.example.WordCount --hdfs --input wasbs:///example/data/gutenberg/davinci.txt --output wasbs:///example/wordcountout

    此動作會執行您先前實作 WordCount 類別。 `--hdfs`會使用 HDFS 工作的指示。 `--input`指定輸入的文字檔案，而`--output`指定輸出位置。

4. 「 工作 」 完成後，可用於下列檢視輸出。

        hdfs dfs -text wasbs:///example/wordcountout/*

    這會顯示類似下列資訊︰

        writers 9
        writes  18
        writhed 1
        writing 51
        writings        24
        written 208
        writtenthese    1
        wrong   11
        wrongly 2
        wrongplace      1
        wrote   34
        wrotefootnote   1
        wrought 7

## <a name="run-the-job-on-a-windows-based-cluster"></a>在 Windows 型叢集上執行工作

下列步驟使用 Windows PowerShell。 執行 MapReduce 工作的其他方法，請參閱[使用 MapReduce 上 HDInsight Hadoop 中](hdinsight-use-mapreduce.md)。

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

2. 開始 PowerShell 的 Azure 和登入您的 Azure 帳戶。 提供您的認證後,] 命令會傳回您的帳戶的相關資訊。

        Add-AzureRMAccount

        Id                             Type       ...
        --                             ----
        someone@example.com            User       ...

3. 如果您有多個訂閱，提供您想要使用的部署的訂閱識別碼。

        Select-AzureRMSubscription -SubscriptionID <YourSubscriptionId>

    > [AZURE.NOTE] 您可以使用`Get-AzureRMSubscription`即可取得您的帳戶，其中包含每個訂閱 Id 相關聯的所有訂閱的清單。

4. 使用下列指令碼上傳並執行 WordCount 工作。 取代`CLUSTERNAME`您 HDInsight 名稱叢集，並確定`$fileToUpload`是正確的__scaldingwordcount-1.0-SNAPSHOT.jar__檔案路徑。

        #Cluster name, file to be uploaded, and where to upload it
        $clustername = Read-Host -Prompt "Enter the HDInsight cluster name"
        $fileToUpload = Read-Host -Prompt "Enter the path to the scaldingwordcount-1.0-SNAPSHOT.jar file"
        $blobPath = "example/jars/scaldingwordcount-1.0-SNAPSHOT.jar"

        #Login to your Azure subscription
        Login-AzureRmAccount
        #Get HTTPS/Admin credentials for submitting the job later
        $creds = Get-Credential -Message "Enter the login credentials for the cluster"
        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
            -ResourceGroupName $resourceGroup)[0].Value

        #Create a storage content and upload the file
        $context = New-AzureStorageContext `
            -StorageAccountName $storageAccountName `
            -StorageAccountKey $storageAccountKey
            
        Set-AzureStorageBlobContent `
            -File $fileToUpload `
            -Blob $blobPath `
            -Container $container `
            -Context $context
            
        #Create a job definition and start the job
        $jobDef=New-AzureRmHDInsightMapReduceJobDefinition `
            -JobName ScaldingWordCount `
            -JarFile wasbs:///example/jars/scaldingwordcount-1.0-SNAPSHOT.jar `
            -ClassName com.microsoft.example.WordCount `
            -arguments "--hdfs", `
                        "--input", `
                        "wasbs:///example/data/gutenberg/davinci.txt", `
                        "--output", `
                        "wasbs:///example/wordcountout"
        $job = Start-AzureRmHDInsightJob `
            -clustername $clusterName `
            -jobdefinition $jobDef `
            -HttpCredential $creds
        Write-Output "Job ID is: $job.JobId"
        Wait-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobId $job.JobId `
            -HttpCredential $creds
        #Download the output of the job
        Get-AzureStorageBlobContent `
            -Blob example/wordcountout/part-00000 `
            -Container $container `
            -Destination output.txt `
            -Context $context
        #Log any errors that occured
        Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $job.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

     當您執行指令碼時，系統會提示您輸入的 HDInsight 叢集管理員的使用者名稱和密碼。 將記錄執行工作時，會發生任何錯誤，主控台。
     
6. 工作完成後，輸出會下載至目前的目錄中的檔案__output.txt__ 。 使用下列命令以顯示的結果。

        cat output.txt

    檔案必須包含類似下列的值︰

        writers 9
        writes  18
        writhed 1
        writing 51
        writings        24
        written 208
        writtenthese    1
        wrong   11
        wrongly 2
        wrongplace      1
        wrote   34
        wrotefootnote   1
        wrought 7

## <a name="next-steps"></a>後續步驟

現在您已經學會如何使用 Scalding 建立 HDInsight MapReduce 工作，使用下列連結以探索 Azure HDInsight 所使用的其他方法。

* [使用 HDInsight 的登錄區](hdinsight-use-hive.md)

* [使用 HDInsight 的豬](hdinsight-use-pig.md)

* [使用 HDInsight MapReduce 工作](hdinsight-use-mapreduce.md)
