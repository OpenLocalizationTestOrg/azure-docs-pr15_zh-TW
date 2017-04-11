<properties
    pageTitle="快速入門 azure AD Java |Microsoft Azure"
    description="如何登入的使用者存取 API Java 命令列應用程式。"
    services="active-directory"
    documentationCenter="java"
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
  ms.tgt_pltfrm="na"
    ms.devlang="java"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="brandwe"/>


# <a name="using-java-command-line-app-to-access-an-api-with-azure-ad"></a>若要存取與 Azure AD API 使用 Java 命令列的應用程式

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Azure AD 可讓簡單且外包 web 應用程式的身分識別管理] 中，直接提供單一登入教具借出只有幾行程式碼。  Java web 應用程式，您可以完成使用 Microsoft 社群導向 ADAL4J 實作。

  以下我們將使用 ADAL4J 至︰
- 登入身分識別提供者為使用 Azure AD 這個應用程式的使用者。
- 顯示使用者的部分資訊。
- 登入登出應用程式的使用者。

若要這麼做，您必須︰

1. 註冊 Azure AD 應用程式
2. 若要使用的 ADAL4J 文件庫中設定您的應用程式。
3. 若要登入及教具借出要求發給 Azure AD 使用 ADAL4J 文件庫。
4. 列印之使用者的相關資料。

若要開始，[下載應用程式的基本架構](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect/archive/skeleton.zip)或[下載已完成的範例](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect\/archive/complete.zip)。  您也必須要註冊您的應用程式 Azure AD 租用戶。  如果您沒有，[瞭解如何取得](active-directory-howto-tenant.md)。

## <a name="1--register-an-application-with-azure-ad"></a>1.註冊 Azure AD 應用程式
若要啟用您的應用程式驗證的使用者，您首先要註冊您的租用戶中新的應用程式。

- 登入 Azure 管理入口網站。
- 在左側導覽中，按一下 [ **Active Directory**。
- 選取您想要註冊的應用程式的租用戶。
- 按一下 [**應用程式**] 索引標籤，底部抽屜中按一下 [新增]。
- 依照提示操作，並建立新**Web 應用程式及/或 WebAPI**。
    - 應用程式的**名稱**會說明您的使用者的應用程式
    - **登入 URL**是您的應用程式的基本的 URL。  基本架構的預設值是`http://localhost:8080/adal4jsample/`。
    - **應用程式識別碼 URI**是您的應用程式的唯一識別碼。  慣例是使用`https://<tenant-domain>/<app-name>`，例如︰`http://localhost:8080/adal4jsample/`
- 當您完成註冊時，AAD 將會在唯一的用戶端識別碼指派您的應用程式。  此值必須在下一個區段中，因此複製的 [設定] 索引標籤。

一次在入口網站應用程式建立應用程式的**應用程式密碼**並將其往下複製。  您將需要引進了。


## <a name="2-set-up-your-app-to-use-adal4j-library-and-prerequisites-using-maven"></a>2.設定您的應用程式使用 ADAL4J 文件庫和使用 Maven 的先決條件
在這裡，我們會設定 ADAL4J 使用 OpenID 連線驗證通訊協定。  將用於 ADAL4J 發出登入並教具借出要求與管理使用者的工作階段，取得使用者，內的其他項目之間的相關資訊。

-   在您的專案的根目錄，開啟/建立`pom.xml`並找出`// TODO: provide dependencies for Maven`取代為下列動作︰

```Java
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>public-client-adal4j-sample</artifactId>
    <packaging>jar</packaging>
    <version>0.0.1-SNAPSHOT</version>
    <name>public-client-adal4j-sample</name>
    <url>http://maven.apache.org</url>
    <properties>
        <spring.version>3.0.5.RELEASE</spring.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>adal4j</artifactId>
            <version>1.1.2</version>
        </dependency>
        <dependency>
            <groupId>com.nimbusds</groupId>
            <artifactId>oauth2-oidc-sdk</artifactId>
            <version>4.5</version>
        </dependency>
        <dependency>
            <groupId>org.json</groupId>
            <artifactId>json</artifactId>
            <version>20090211</version>
        </dependency>
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>3.0.1</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
            <version>1.7.5</version>
        </dependency>
</dependencies>
    <build>
        <finalName>public-client-adal4j-sample</finalName>
        <plugins>
                <plugin>
            <groupId>org.codehaus.mojo</groupId>
            <artifactId>exec-maven-plugin</artifactId>
            <version>1.2.1</version>
            <configuration>
                <mainClass>PublicClient</mainClass>
            </configuration>
        </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <source>1.7</source>
                    <target>1.7</target>
                    <encoding>UTF-8</encoding>
                </configuration>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-dependency-plugin</artifactId>
                <executions>
                    <execution>
                        <id>install</id>
                        <phase>install</phase>
                        <goals>
                            <goal>sources</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-resources-plugin</artifactId>
                <version>2.5</version>
                <configuration>
                    <encoding>UTF-8</encoding>
                </configuration>
            </plugin>
            <plugin>
        <artifactId>maven-assembly-plugin</artifactId>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>single</goal>
            </goals>
          </execution>
        </executions>
        <configuration>
          <descriptorRefs>
            <descriptorRef>jar-with-dependencies</descriptorRef>
          </descriptorRefs>
        </configuration>
      </plugin>
      <plugin>
  <groupId>org.apache.maven.plugins</groupId>
  <artifactId>maven-assembly-plugin</artifactId>
  <configuration>
    <archive>
      <manifest>
        <mainClass>PublicClient</mainClass>
      </manifest>
    </archive>
  </configuration>
</plugin>
        </plugins>
    </build>

</project>


```



## <a name="3-create-the-java-publicclient-file"></a>3.建立 java PublicClient 檔案

上述，我們將會使用圖形 API 取得登入的使用者有關的資料。 若要讓我們輕鬆這個我們應該建立的檔案來代表**目錄物件**和個別檔案，以便可 Java OO 模式代表**使用者**。

1. 建立檔案稱為`DirectoryObject.java`，我們將用來儲存基本的資料之任何 DirectoryObject （您可以依需要您可能會執行任何其他圖表查詢稍後使用此）。 您可以剪下/貼上此從下面︰

```Java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Future;

import javax.naming.ServiceUnavailableException;

import com.microsoft.aad.adal4j.AuthenticationContext;
import com.microsoft.aad.adal4j.AuthenticationResult;

public class PublicClient {

    private final static String AUTHORITY = "https://login.microsoftonline.com/common/";
    private final static String CLIENT_ID = "2a4da06c-ff07-410d-af8a-542a512f5092";

    public static void main(String args[]) throws Exception {

        try (BufferedReader br = new BufferedReader(new InputStreamReader(
                System.in))) {
            System.out.print("Enter username: ");
            String username = br.readLine();
            System.out.print("Enter password: ");
            String password = br.readLine();

            AuthenticationResult result = getAccessTokenFromUserCredentials(
                    username, password);
            System.out.println("Access Token - " + result.getAccessToken());
            System.out.println("Refresh Token - " + result.getRefreshToken());
            System.out.println("ID Token - " + result.getIdToken());
        }
    }

    private static AuthenticationResult getAccessTokenFromUserCredentials(
            String username, String password) throws Exception {
        AuthenticationContext context = null;
        AuthenticationResult result = null;
        ExecutorService service = null;
        try {
            service = Executors.newFixedThreadPool(1);
            context = new AuthenticationContext(AUTHORITY, false, service);
            Future<AuthenticationResult> future = context.acquireToken(
                    "https://graph.windows.net", CLIENT_ID, username, password,
                    null);
            result = future.get();
        } finally {
            service.shutdown();
        }

        if (result == null) {
            throw new ServiceUnavailableException(
                    "authentication result was null");
        }
        return result;
    }
}


```


##<a name="compile-and-run-the-sample"></a>編譯和執行範例

變更遠至您的根目錄，然後執行下列命令以建立範例您只是放置在一起使用`maven`。 這會使用`pom.xml`您所撰寫的相依性的檔案。

`$ mvn package`

您現在應該`adal4jsample.war`檔案中您`/targets`目錄。 您可能會在您 Tomcat 容器中部署的和造訪的 URL 

`http://localhost:8080/adal4jsample/`


> [AZURE.NOTE] 
您可以輕鬆非常部署的最新的 Tomcat 伺服器了的話。 只要瀏覽至`http://localhost:8080/manager/`並遵循指示上傳您 ' adal4jsample.war 」 檔案。 它會為您的 autodeploy 以正確的端點。

##<a name="next-steps"></a>後續步驟

恭喜您 ！ 現在，您可以使用 Java 應用程式的人員能夠安全地驗證的使用者，而呼叫使用 OAuth 2.0 的 Web Api，並取得基本的使用者資訊。  如果您未，現在是填入您的租用戶，且有些使用者的時間。

參照的已完成的範例 （不含您設定的值） [，提供為的.zip](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect/archive/complete.zip)，或者您可以複製從 GitHub:

```git clone --branch complete https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect.git```

