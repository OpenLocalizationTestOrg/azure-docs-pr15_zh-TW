<properties
pageTitle="Java 使用者定義函數 (UDF) 使用登錄區中 HDInsight |Microsoft Azure"
description="瞭解如何建立及使用登錄區中 HDInsight Java 使用者定義函數 (UDF)。"
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="jhubbard"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="java"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="09/27/2016"
ms.author="larryfr"/>

#<a name="use-a-java-udf-with-hive-in-hdinsight"></a>使用 Java UDF 的登錄區中 HDInsight

登錄區相當適合用於 HDInsight 中, 使用資料，但有時候您需要更多的一般用途語言。 登錄區可讓您建立使用者定義函數 (UDF) 使用各種不同的語言。 在此文件中，您將學習如何使用登錄區 Java UDF。

## <a name="requirements"></a>需求

* Azure 的訂閱

* HDInsight 叢集 (Windows 或 Linux 型)

    > [AZURE.NOTE] 大部分文件中的步驟會使用這兩種叢集類型;不過，用來將編譯的 UDF 上傳到叢集，並執行的步驟會有特定 Linux 型叢集。 可以使用 Windows 型叢集的資訊會提供連結。

* [Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/) 7 或更新版本 （或相同，例如 OpenJDK）

* [Apache Maven](http://maven.apache.org/)

* 在文字編輯器或 Java IDE

    > [AZURE.IMPORTANT] 如果您使用的 Linux 型 HDInsight 伺服器，但建立 Windows 用戶端的 Python 檔案，您必須使用編輯器，會使用 LF 為線條結尾。 如果您不確定是否編輯器使用 LF 或 CRLF，請參閱[疑難排解](#troubleshooting)節的步驟移除 HDInsight 叢集上使用公用程式 CR 字元。

## <a name="create-an-example-udf"></a>建立範例 UDF

1. 從命令列，使用下列命令以建立新的 Maven 專案︰

        mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=ExampleUDF -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    > [AZURE.NOTE] 如果您使用 PowerShell，您必須用引號括住的參數。 例如， `mvn archetype:generate "-DgroupId=com.microsoft.examples" "-DartifactId=ExampleUDF" "-DarchetypeArtifactId=maven-archetype-quickstart" "-DinteractiveMode=false"`。

    這會建立新名為__exampleudf__，其中會包含 Maven 專案的目錄。

2. 建立專案之後, 刪除__exampleudf/src 測試__目錄建立專案;不會在這個範例使用。

3. 開啟__exampleudf/pom.xml__，並取代現有的`<dependencies>`以下列項目︰

        <dependencies>
            <dependency>
                <groupId>org.apache.hadoop</groupId>
                <artifactId>hadoop-client</artifactId>
                <version>2.7.1</version>
                <scope>provided</scope>
            </dependency>
            <dependency>
                <groupId>org.apache.hive</groupId>
                <artifactId>hive-exec</artifactId>
                <version>1.2.1</version>
                <scope>provided</scope>
            </dependency>
        </dependencies>

    這些項目指定 Hadoop 和 HDInsight 3.3 及 3.4 叢集隨附的登錄區的版本。 您可以找到的 Hadoop 和登錄區 HDInsight 提供從[HDInsight 元件版本](hdinsight-component-versioning.md)的文件版本的詳細資訊。

    新增`<build>`區段之前`</project>`線條結尾的檔案。 本節應包含下列各項︰

        <build>
            <plugins>
                <!-- build for Java 1.7, even if you're on a later version -->
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-compiler-plugin</artifactId>
                    <version>3.3</version>
                    <configuration>
                        <source>1.7</source>
                        <target>1.7</target>
                    </configuration>
                </plugin>
                <!-- build an uber jar -->
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-shade-plugin</artifactId>
                    <version>2.3</version>
                    <configuration>
                        <!-- Keep us from getting a can't overwrite file error -->
                        <transformers>
                            <transformer
                                    implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                            </transformer>
                            <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer">
                            </transformer>
                        </transformers>
                        <!-- Keep us from getting a bad signature error -->
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
                        </execution>
                    </executions>
                </plugin>
            </plugins>
        </build>
    
    這些項目定義如何建立專案。 具體來說，Java 專案所使用及如何建立部署叢集 uberjar 的版本。

    將檔案儲存後所做的變更。

4. 重新命名__exampleudf/src/main/java/com/microsoft/examples/App.java__至__ExampleUDF.java__，並在編輯器中開啟檔案。

5. 取代下列命令，然後儲存檔案的__ExampleUDF.java__檔案的內容。

        package com.microsoft.examples;

        import org.apache.hadoop.hive.ql.exec.Description;
        import org.apache.hadoop.hive.ql.exec.UDF;
        import org.apache.hadoop.io.*;

        // Description of the UDF
        @Description(
            name="ExampleUDF",
            value="returns a lower case version of the input string.",
            extended="select ExampleUDF(deviceplatform) from hivesampletable limit 10;"
        )
        public class ExampleUDF extends UDF {
            // Accept a string input
            public String evaluate(String input) {
                // If the value is null, return a null
                if(input == null)
                    return null;
                // Lowercase the input string and return it
                return input.toLowerCase();
            }
        }

    這會實作 UDF 接受字串值，並傳回字串的小寫版本。

## <a name="build-and-install-the-udf"></a>建立並安裝 UDF

1. 編譯及封裝 UDF 中使用下列命令︰

        mvn compile package

    這會建立，然後將__exampleudf/target/ExampleUDF-1.0-SNAPSHOT.jar__封裝 UDF。

2. 使用`scp`] 命令，將檔案複製到 HDInsight 叢集。

        scp ./target/ExampleUDF-1.0-SNAPSHOT.jar myuser@mycluster-ssh.azurehdinsight

    取代__myuser__叢集 SSH 使用者帳戶。 取代__mycluster__叢集名稱。 如果您使用密碼來保護 SSH 帳戶時，系統會提示您輸入密碼。 如果您使用的憑證，您可能需要使用`-i`參數指定私密金鑰檔案。

3. 連線到使用 SSH 叢集。 

        ssh myuser@mycluster-ssh.azurehdinsight.net

    如需有關如何使用 HDInsight SSH 的詳細資訊，請參閱下列文件。

    * [使用上 HDInsight Linux、 Unix，或 OS X 的 Linux 型 Hadoop SSH](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [使用上從 Windows HDInsight Linux 為基礎的 Hadoop SSH](hdinsight-hadoop-linux-use-ssh-windows.md)

4. 從 SSH 工作階段，將 jar 檔案複製到 HDInsight 儲存空間。

        hdfs dfs -put ExampleUDF-1.0-SNAPSHOT.jar /example/jars

## <a name="use-the-udf-from-hive"></a>使用登錄區 UDF

1. 使用下列 Beeline 用戶端起始 SSH 工作階段。

        beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin

    這個命令假設登入帳戶使用預設的__管理員__，為您的叢集。

2. 一旦您到達`jdbc:hive2://localhost:10001/>`提示中，輸入下列命令以將 UDF 新增至群組，並將它公開為函數。

        ADD JAR wasbs:///example/jars/ExampleUDF-1.0-SNAPSHOT.jar;
        CREATE TEMPORARY FUNCTION tolower as 'com.microsoft.examples.ExampleUDF';

3. 使用 UDF 轉換成小寫字串擷取資料表中的值。

        SELECT tolower(deviceplatform) FROM hivesampletable LIMIT 10;

    將資料表中選取裝置平台 （Android、 Windows、 iOS 等），將字串轉換為小寫，並顯示註解然後。 輸出看起來如下。

        +----------+--+
        |   _c0    |
        +----------+--+
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        +----------+--+

## <a name="next-steps"></a>後續步驟

以其他方式來使用登錄區中，請參閱[使用登錄區與 HDInsight](hdinsight-use-hive.md)。

如需有關 Hive User-Defined 函數，請參閱在 apache.org 登錄區 wiki 的[登錄區運算子及使用者定義函數](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF)區段。
