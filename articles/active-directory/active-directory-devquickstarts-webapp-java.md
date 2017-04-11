<properties
    pageTitle="快速入門 azure AD Java |Microsoft Azure"
    description="如何建立簽署使用者以公司或學校帳戶的 Java web 應用程式。"
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


# <a name="java-web-app-sign-in--sign-out-with-azure-ad"></a>Java Web 應用程式登入與登出與 Azure AD

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Azure AD 可讓簡單且外包 web 應用程式的身分識別管理] 中，直接提供單一登入教具借出只有幾行程式碼。  Java web 應用程式，您可以完成使用 Microsoft 社群導向 ADAL4J 實作。

  以下我們將使用 ADAL4J 至︰
- 登入身分識別提供者為使用 Azure AD 應用程式的使用者。
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


## <a name="2-set-up-your-app-to-use-adal4j-library-and-prerequisities-using-maven"></a>2.設定您的應用程式使用 ADAL4J 文件庫和使用 Maven prerequisities
在這裡，我們會設定 ADAL4J 使用 OpenID 連線驗證通訊協定。  將用於 ADAL4J 發出登入並教具借出要求與管理使用者的工作階段，取得使用者，內的其他項目之間的相關資訊。

-   在您的專案的根目錄，開啟/建立`pom.xml`並找出`// TODO: provide dependencies for Maven`取代為下列動作︰

```Java
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>adal4jsample</artifactId>
    <packaging>war</packaging>
    <version>0.0.1-SNAPSHOT</version>
    <name>adal4jsample</name>
    <url>http://maven.apache.org</url>
    <properties>
        <spring.version>3.0.5.RELEASE</spring.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>adal4j</artifactId>
            <version>1.1.1</version>
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
        <!-- Spring 3 dependencies -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-core</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-web</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>${spring.version}</version>
        </dependency>
    </dependencies>

    <build>
        <finalName>sample-for-adal4j</finalName>
        <plugins>
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
                <artifactId>maven-war-plugin</artifactId>
                <version>2.4</version>
                <configuration>
                    <warName>${project.artifactId}</warName>
                    <source>${project.basedir}\src</source>
                    <target>${maven.compiler.target}</target>
                    <encoding>utf-8</encoding>
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
        </plugins>
    </build>

</project>

```


## <a name="3-create-the-java-web-application-files-web-inf"></a>3.建立 Java web 應用程式檔案 (WEB INF)

在這裡，我們會設定 Java web 應用程式使用 OpenID 連線驗證通訊協定。  ADAL4J 文件庫會用來發出登入並教具借出要求與管理使用者的工作階段，取得使用者，內的其他項目之間的相關資訊。

-   若要開始，請開啟`web.xml`檔案位於 [ `\webapp\WEB-INF\`，並在 [xml 中輸入您的應用程式設定值。

檔案應該如下所示︰

```xml
<?xml version="1.0"?>
<web-app id="WebApp_ID" version="2.4"
    xmlns="http://java.sun.com/xml/ns/j2ee" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://java.sun.com/xml/ns/j2ee 
    http://java.sun.com/xml/ns/j2ee/web-app_2_4.xsd">
    <display-name>Archetype Created Web Application</display-name>
    <context-param>
        <param-name>authority</param-name>
        <param-value>https://login.windows.net/</param-value>
    </context-param>
    <context-param>
        <param-name>tenant</param-name>
        <param-value>YOUR_TENANT_NAME</param-value>
    </context-param>

    <filter>
        <filter-name>BasicFilter</filter-name>
        <filter-class>com.microsoft.aad.adal4jsample.BasicFilter</filter-class>
        <init-param>
            <param-name>client_id</param-name>
            <param-value>YOUR_CLIENT_ID</param-value>
        </init-param>
        <init-param>
            <param-name>secret_key</param-name>
            <param-value>YOUR_CLIENT_SECRET</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>BasicFilter</filter-name>
        <url-pattern>/secure/*</url-pattern>
    </filter-mapping>

    <servlet>
        <servlet-name>mvc-dispatcher</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <load-on-startup>1</load-on-startup>
    </servlet>

    <servlet-mapping>
        <servlet-name>mvc-dispatcher</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>

    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>/WEB-INF/mvc-dispatcher-servlet.xml</param-value>
    </context-param>

    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>
</web-app>

```


    -   `YOUR_CLIENT_ID`指派給您的應用程式，請在註冊入口網站的**應用程式識別碼**。
    -   `YOUR_CLIENT_SECRET`是您在入口網站中建立的**應用程式的密碼**。
    - `YOUR_TENANT_NAME`是指您的應用程式，例如 contoso.onmicrosoft.com**租用戶名稱**

保留其餘的設定參數了解單獨使用。

> [AZURE.NOTE]
您可以看到 XML 檔案中所撰寫的一部分 JSP/Servlet webapp`mvc-dispatcher`的會使用`BasicFilter`時我們造訪 / 安全 URL。 您會看到相同我們撰寫我們會使用 / 安全為我們受保護的內容的所在的位置的其餘部分中，而且會強制 Azure Active Directory 驗證。

-   接下來，建立`mvc-dispatcher-servlet.xml`檔案位於 [ `\webapp\WEB-INF\`，並輸入以下動作︰

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:context="http://www.springframework.org/schema/context"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="
        http://www.springframework.org/schema/beans     
        http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
        http://www.springframework.org/schema/context 
        http://www.springframework.org/schema/context/spring-context-3.0.xsd">

    <context:component-scan base-package="com.microsoft.aad.adal4jsample" />

    <bean
        class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix">
            <value>/</value>
        </property>
        <property name="suffix">
            <value>.jsp</value>
        </property>
    </bean>

</beans>
```

這將告訴 webapp 使用春天和我們將撰寫下方我們.jsp 檔案的位置。

## <a name="4-create-the-java-jsp-view-files-for-basicfilter-mvc"></a>4.（適用於 BasicFilter MVC) 建立 Java JSP 檢視檔案

我們會只一半 fished 我們我們 webapp WEB INF 中的設定。 接下來，我們需要建立我們 webapp 會執行我們在有所提示我們設定中的實際 Java 伺服器網頁檔案。

如果您記得，跟 Java 我們 xml 我們的設定檔`/`載入.jsp 檔案的資源和`/secure`我們稱為應該經過篩選資源`BasicFilter`。 

現在就讓我們立即進行這些。

-   若要開始，建立`index.jsp`檔案位於 [ `\webapp\`，和 [剪下/貼上下列動作︰

```jsp
<html>
<body>
    <h2>Hello World!</h2>
    <ul>
    <li><a href="secure/aad">Secure Page</a></li>
    </ul>
</body>
</html>

```

這只會重新導向至受我們篩選安全頁面。

- 接下來，在相同的目錄可讓建立`error.jsp`攔截任何錯誤，可能會發生這種情形的檔案︰

```jsp
<html>
<body>
    <h2>ERROR PAGE!</h2>
    <p>
        Exception -
        <%=request.getAttribute("error")%></p>
    <ul>
        <li><a href="<%=request.getContextPath()%>/index.jsp">Go Home</a></li>
    </ul>
</body>
</html>
```

- 最後，我們讓我們想要建立資料夾底下的安全網頁`\webapp`稱為`\secure`以便目錄我們現在`\webapp\secure`。 

- 在此目錄，然後建立`aad.jsp`檔案] 和 [剪下/貼上下列動作︰

```jsp
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>AAD Secure Page</title>
</head>
<body>

    <h1>Directory - Users List</h1>
    <p>${users}</p>

    <ul>
        <li><a href="<%=request.getContextPath()%>/secure/aad?cc=1">Get
                new Access Token via Client Credentials</a></li>
    </ul>
    <ul>
        <li><a href="<%=request.getContextPath()%>/secure/aad?refresh=1">Get
                new Access Token via Refresh Token</a></li>
    </ul>
    <ul>
        <li><a href="<%=request.getContextPath()%>/index.jsp">Go Home</a></li>
    </ul>
</body>
</html>
```

您會看到此頁面將會重新導向到特定的要求它我們 BasicFilter servlet 會閱讀，然後執行使用`ADAJ4J`文件庫。 相當簡單，吧？

當然，我們需要現在需要設定我們 Java 檔案，以便 servlet 可以執行工作。

## <a name="5-create-some-java-helper-files-for-basicfilter-mvc"></a>5。 （適用於 BasicFilter MVC) 建立一些 Java 說明檔案

我們的目標是建立將某些 Java 檔案︰

1. 允許登入並教具借出的使用者
2. 取得有關使用者的一些資料。

> [AZURE.NOTE] 
若要取得有關使用者的資料，我們需要使用圖形 API 從 Azure Active Directory。 圖形 API 是安全的 web 服務的可用來擷取您的組織，包括個別使用者的相關資料。 這是更預先填入權杖機密資料，因為它可確保使用者要求資料授權，可能會發生這種情形，吸引的權杖 （從 jailbroken 電話或網頁瀏覽器快取桌面） 的任何人都不會收到一堆使用者或組織的相關的重要詳細資料。

讓我們來撰寫進行這項工作的一些 Java 檔案︰

1. 在您儲存所有我們 java 檔案稱為 「 adal4jsample 」 的根目錄中建立的資料夾。 

我們會使用命名空間`com.microsoft.aad.adal4jsample`我們 java 檔案中。 大部分的 Ide 這個建立巢狀的資料夾結構 (例如`/com/microsoft/aad/adal4jsample`)。 您可以這麼做，但並不需要。

2. 在這個資料夾中，建立一個稱為檔案`JSONHelper.java`，我們將用來協助我們從我們的權杖的 JSON 資料剖析。 您可以剪下/貼上此從下面︰

```Java

package com.microsoft.aad.adal4jsample;

import java.lang.reflect.Field;
import java.util.Arrays;
import java.util.Enumeration;
import java.util.List;

import javax.servlet.http.HttpServletRequest;

import org.apache.commons.lang3.text.WordUtils;
import org.apache.log4j.Logger;
import org.json.JSONArray;
import org.json.JSONException;
import org.json.JSONObject;

/**
 * This class provides the methods to parse JSON Data from a JSON Formatted
 * String.
 * 
 * @author Azure Active Directory Contributor
 * 
 */
public class JSONHelper {

    private static Logger logger = Logger.getLogger(JSONHelper.class);

    JSONHelper() {
        // PropertyConfigurator.configure("log4j.properties");
    }

    /**
     * This method parses an JSON Array out of a collection of JSON Objects
     * within a string.
     * 
     * @param jSonData
     *            The JSON String that holds the collection.
     * @return An JSON Array that would contains all the collection object.
     * @throws Exception
     */
    public static JSONArray fetchDirectoryObjectJSONArray(JSONObject jsonObject) throws Exception {
        JSONArray jsonArray = new JSONArray();
        jsonArray = jsonObject.optJSONObject("responseMsg").optJSONArray("value");
        return jsonArray;
    }

    /**
     * This method parses an JSON Object out of a collection of JSON Objects
     * within a string
     * 
     * @param jsonObject
     * @return An JSON Object that would contains the DirectoryObject.
     * @throws Exception
     */
    public static JSONObject fetchDirectoryObjectJSONObject(JSONObject jsonObject) throws Exception {
        JSONObject jObj = new JSONObject();
        jObj = jsonObject.optJSONObject("responseMsg");
        return jObj;
    }

    /**
     * This method parses the skip token from a json formatted string.
     * 
     * @param jsonData
     *            The JSON Formatted String.
     * @return The skipToken.
     * @throws Exception
     */
    public static String fetchNextSkiptoken(JSONObject jsonObject) throws Exception {
        String skipToken = "";
        // Parse the skip token out of the string.
        skipToken = jsonObject.optJSONObject("responseMsg").optString("odata.nextLink");

        if (!skipToken.equalsIgnoreCase("")) {
            // Remove the unnecessary prefix from the skip token.
            int index = skipToken.indexOf("$skiptoken=") + (new String("$skiptoken=")).length();
            skipToken = skipToken.substring(index);
        }
        return skipToken;
    }

    /**
     * @param jsonObject
     * @return
     * @throws Exception
     */
    public static String fetchDeltaLink(JSONObject jsonObject) throws Exception {
        String deltaLink = "";
        // Parse the skip token out of the string.
        deltaLink = jsonObject.optJSONObject("responseMsg").optString("aad.deltaLink");
        if (deltaLink == null || deltaLink.length() == 0) {
            deltaLink = jsonObject.optJSONObject("responseMsg").optString("aad.nextLink");
            logger.info("deltaLink empty, nextLink ->" + deltaLink);

        }
        if (!deltaLink.equalsIgnoreCase("")) {
            // Remove the unnecessary prefix from the skip token.
            int index = deltaLink.indexOf("deltaLink=") + (new String("deltaLink=")).length();
            deltaLink = deltaLink.substring(index);
        }
        return deltaLink;
    }

    /**
     * This method would create a string consisting of a JSON document with all
     * the necessary elements set from the HttpServletRequest request.
     * 
     * @param request
     *            The HttpServletRequest
     * @return the string containing the JSON document.
     * @throws Exception
     *             If there is any error processing the request.
     */
    public static String createJSONString(HttpServletRequest request, String controller) throws Exception {
        JSONObject obj = new JSONObject();
        try {
            Field[] allFields = Class.forName(
                    "com.microsoft.windowsazure.activedirectory.sdk.graph.models." + controller).getDeclaredFields();
            String[] allFieldStr = new String[allFields.length];
            for (int i = 0; i < allFields.length; i++) {
                allFieldStr[i] = allFields[i].getName();
            }
            List<String> allFieldStringList = Arrays.asList(allFieldStr);
            Enumeration<String> fields = request.getParameterNames();

            while (fields.hasMoreElements()) {

                String fieldName = fields.nextElement();
                String param = request.getParameter(fieldName);
                if (allFieldStringList.contains(fieldName)) {
                    if (param == null || param.length() == 0) {
                        if (!fieldName.equalsIgnoreCase("password")) {
                            obj.put(fieldName, JSONObject.NULL);
                        }
                    } else {
                        if (fieldName.equalsIgnoreCase("password")) {
                            obj.put("passwordProfile", new JSONObject("{\"password\": \"" + param + "\"}"));
                        } else {
                            obj.put(fieldName, param);

                        }
                    }
                }
            }
        } catch (JSONException e) {
            e.printStackTrace();
        } catch (SecurityException e) {
            e.printStackTrace();
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }
        return obj.toString();
    }

    /**
     * 
     * @param key
     * @param value
     * @return string format of this JSON obje
     * @throws Exception
     */
    public static String createJSONString(String key, String value) throws Exception {

        JSONObject obj = new JSONObject();
        try {
            obj.put(key, value);
        } catch (JSONException e) {
            e.printStackTrace();
        }

        return obj.toString();
    }

    /**
     * This is a generic method that copies the simple attribute values from an
     * argument jsonObject to an argument generic object.
     * 
     * @param jsonObject
     *            The jsonObject from where the attributes are to be copied.
     * @param destObject
     *            The object where the attributes should be copied into.
     * @throws Exception
     *             Throws a Exception when the operation are unsuccessful.
     */
    public static <T> void convertJSONObjectToDirectoryObject(JSONObject jsonObject, T destObject) throws Exception {

        // Get the list of all the field names.
        Field[] fieldList = destObject.getClass().getDeclaredFields();

        // For all the declared field.
        for (int i = 0; i < fieldList.length; i++) {
            // If the field is of type String, that is
            // if it is a simple attribute.
            if (fieldList[i].getType().equals(String.class)) {
                // Invoke the corresponding set method of the destObject using
                // the argument taken from the jsonObject.
                destObject
                        .getClass()
                        .getMethod(String.format("set%s", WordUtils.capitalize(fieldList[i].getName())),
                                new Class[] { String.class })
                        .invoke(destObject, new Object[] { jsonObject.optString(fieldList[i].getName()) });
            }
        }
    }

    public static JSONArray joinJSONArrays(JSONArray a, JSONArray b) {
        JSONArray comb = new JSONArray();
        for (int i = 0; i < a.length(); i++) {
            comb.put(a.optJSONObject(i));
        }
        for (int i = 0; i < b.length(); i++) {
            comb.put(b.optJSONObject(i));
        }
        return comb;
    }

}

```

3. 接下來，請建立一個稱為檔案`HttpClientHelper.java`，我們將用來協助我們從我們的 AAD 端點的 HTTP 資料剖析。 您可以剪下/貼上此從下面︰

```Java

package com.microsoft.aad.adal4jsample;

import java.io.BufferedReader;
import java.io.ByteArrayOutputStream;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.io.OutputStreamWriter;
import java.net.HttpURLConnection;

import org.json.JSONException;
import org.json.JSONObject;

/**
 * This is Helper class for all RestClient class.
 * 
 * @author Azure Active Directory Contributor
 * 
 */
public class HttpClientHelper {

    public HttpClientHelper() {
        super();
    }

    public static String getResponseStringFromConn(HttpURLConnection conn, boolean isSuccess) throws IOException {

        BufferedReader reader = null;
        if (isSuccess) {
            reader = new BufferedReader(new InputStreamReader(conn.getInputStream()));
        } else {
            reader = new BufferedReader(new InputStreamReader(conn.getErrorStream()));
        }
        StringBuffer stringBuffer = new StringBuffer();
        String line = "";
        while ((line = reader.readLine()) != null) {
            stringBuffer.append(line);
        }

        return stringBuffer.toString();
    }

    public static String getResponseStringFromConn(HttpURLConnection conn, String payLoad) throws IOException {

        // Send the http message payload to the server.
        if (payLoad != null) {
            conn.setDoOutput(true);
            OutputStreamWriter osw = new OutputStreamWriter(conn.getOutputStream());
            osw.write(payLoad);
            osw.flush();
            osw.close();
        }

        // Get the message response from the server.
        BufferedReader br = new BufferedReader(new InputStreamReader(conn.getInputStream()));
        String line = "";
        StringBuffer stringBuffer = new StringBuffer();
        while ((line = br.readLine()) != null) {
            stringBuffer.append(line);
        }

        br.close();

        return stringBuffer.toString();
    }

    public static byte[] getByteaArrayFromConn(HttpURLConnection conn, boolean isSuccess) throws IOException {

        InputStream is = conn.getInputStream();
        ByteArrayOutputStream baos = new ByteArrayOutputStream();
        byte[] buff = new byte[1024];
        int bytesRead = 0;

        while ((bytesRead = is.read(buff, 0, buff.length)) != -1) {
            baos.write(buff, 0, bytesRead);
        }

        byte[] bytes = baos.toByteArray();
        baos.close();
        return bytes;
    }

    /**
     * for bad response, whose responseCode is not 200 level
     * 
     * @param responseCode
     * @param errorCode
     * @param errorMsg
     * @return
     * @throws JSONException
     */
    public static JSONObject processResponse(int responseCode, String errorCode, String errorMsg) throws JSONException {
        JSONObject response = new JSONObject();
        response.put("responseCode", responseCode);
        response.put("errorCode", errorCode);
        response.put("errorMsg", errorMsg);

        return response;
    }

    /**
     * for bad response, whose responseCode is not 200 level
     * 
     * @param responseCode
     * @param errorCode
     * @param errorMsg
     * @return
     * @throws JSONException
     */
    public static JSONObject processGoodRespStr(int responseCode, String goodRespStr) throws JSONException {
        JSONObject response = new JSONObject();
        response.put("responseCode", responseCode);
        if (goodRespStr.equalsIgnoreCase("")) {
            response.put("responseMsg", "");
        } else {
            response.put("responseMsg", new JSONObject(goodRespStr));
        }

        return response;
    }

    /**
     * for good response
     * 
     * @param responseCode
     * @param responseMsg
     * @return
     * @throws JSONException
     */
    public static JSONObject processBadRespStr(int responseCode, String responseMsg) throws JSONException {

        JSONObject response = new JSONObject();
        response.put("responseCode", responseCode);
        if (responseMsg.equalsIgnoreCase("")) { // good response is empty string
            response.put("responseMsg", "");
        } else { // bad response is json string
            JSONObject errorObject = new JSONObject(responseMsg).optJSONObject("odata.error");

            String errorCode = errorObject.optString("code");
            String errorMsg = errorObject.optJSONObject("message").optString("value");
            response.put("responseCode", responseCode);
            response.put("errorCode", errorCode);
            response.put("errorMsg", errorMsg);
        }

        return response;
    }

}

```

## <a name="6-create-the-java-graph-api-model-files-for-basicfilter-mvc"></a>6.建立 java 圖形 API 模型檔案 （適用於 BasicFilter MVC)

上述，我們將會使用圖形 API 取得登入的使用者有關的資料。 若要讓我們輕鬆這個我們應該建立的檔案來代表**目錄物件**和個別檔案，以便可 Java OO 模式代表**使用者**。

1. 建立檔案稱為`DirectoryObject.java`，我們將用來儲存基本的資料之任何 DirectoryObject （您可以依需要您可能會執行任何其他圖表查詢稍後使用此）。 您可以剪下/貼上此從下面︰

```Java

package com.microsoft.aad.adal4jsample;

/**
 * @author Azure Active Directory Contributor
 *
 */
public abstract class DirectoryObject {
    
    public DirectoryObject() {
        super();
    }
    
    /**
     * 
     * @return
     */
    public abstract String getObjectId();
    
    /**
     * @param objectId
     */
    public abstract void setObjectId(String objectId);

    /**
     * 
     * @return
     */
    public abstract String getObjectType();

    /**
     * 
     * @param objectType
     */
    public abstract void setObjectType(String objectType);
    
    /**
     * 
     * @return
     */
    public abstract String getDisplayName();

    /**
     * 
     * @param displayName
     */
    public abstract void setDisplayName(String displayName);

}

```

2. 建立檔案稱為`User.java`，我們將用來儲存任何使用者目錄中的基本資料。 同樣地，這是很基本 getter/setter 目錄資料，因此您可以剪下/貼上此從下面︰

```Java

package com.microsoft.aad.adal4jsample;

import java.security.acl.Group;
import java.util.ArrayList;

import javax.xml.bind.annotation.XmlRootElement;

import org.json.JSONObject;

/**
 *  The User Class holds together all the members of a WAAD User entity and all the access methods and set methods
 *  @author Azure Active Directory Contributor
 */
@XmlRootElement
public class User extends DirectoryObject{
    
    // The following are the individual private members of a User object that holds
    // a particular simple attribute of an User object.
    protected String objectId;
    protected String objectType;
    protected String accountEnabled;
    protected String city;
    protected String country;
    protected String department;
    protected String dirSyncEnabled;
    protected String displayName;
    protected String facsimileTelephoneNumber;
    protected String givenName;
    protected String jobTitle;
    protected String lastDirSyncTime;
    protected String mail;
    protected String mailNickname;
    protected String mobile;
    protected String password;
    protected String passwordPolicies;
    protected String physicalDeliveryOfficeName;
    protected String postalCode;
    protected String preferredLanguage;
    protected String state;
    protected String streetAddress;
    protected String surname;
    protected String telephoneNumber;
    protected String usageLocation;
    protected String userPrincipalName;
    protected boolean isDeleted;  // this will move to dto

    /**
     * below 4 properties are for future use
     */
    // managerDisplayname of this user
    protected String managerDisplayname;
    
    // The directReports holds a list of directReports
    private ArrayList<User> directReports;
    
    // The groups holds a list of group entity this user belongs to. 
    private ArrayList<Group> groups;
    
    // The roles holds a list of role entity this user belongs to. 
    private ArrayList<Group> roles;
    
    
    /**
     * The constructor for the User class. Initializes the dynamic lists and managerDisplayname variables.
     */
    public User(){
        directReports = null;
        groups = new ArrayList<Group>();
        roles = new ArrayList<Group>();
        managerDisplayname = null;
    }
//  
//  public User(String displayName, String objectId){
//      setDisplayName(displayName);
//      setObjectId(objectId);
//  }
//  
//  public User(String displayName, String objectId, String userPrincipalName, String accountEnabled){
//      setDisplayName(displayName);
//      setObjectId(objectId);
//      setUserPrincipalName(userPrincipalName);
//      setAccountEnabled(accountEnabled);
//  }
//  

    /**
     * @return The objectId of this user.
     */
    public String getObjectId() {
        return objectId;
    }
    
    /**
     * @param objectId The objectId to set to this User object.
     */
    public void setObjectId(String objectId) {
        this.objectId = objectId;
    }


    /**
     * @return The objectType of this User.
     */
    public String getObjectType() {
        return objectType;
    }

    /**
     * @param objectType The objectType to set to this User object.
     */
    public void setObjectType(String objectType) {
        this.objectType = objectType;
    }

    /**
     * @return The userPrincipalName of this User.
     */
    public String getUserPrincipalName() {
        return userPrincipalName;
    }

    /**
     * @param userPrincipalName The userPrincipalName to set to this User object.
     */
    public void setUserPrincipalName(String userPrincipalName) {
        this.userPrincipalName = userPrincipalName;
    }

    
    /**
     * @return The usageLocation of this User.
     */
    public String getUsageLocation() {
        return usageLocation;
    }

    /**
     * @param usageLocation The usageLocation to set to this User object.
     */
    public void setUsageLocation(String usageLocation) {
        this.usageLocation = usageLocation;
    }

    /**
     * @return The telephoneNumber of this User.
     */
    public String getTelephoneNumber() {
        return telephoneNumber;
    }

    /**
     * @param telephoneNumber The telephoneNumber to set to this User object.
     */
    public void setTelephoneNumber(String telephoneNumber) {
        this.telephoneNumber = telephoneNumber;
    }

    /**
     * @return The surname of this User.
     */
    public String getSurname() {
        return surname;
    }

    /**
     * @param surname The surname to set to this User Object.
     */
    public void setSurname(String surname) {
        this.surname = surname;
    }

    /**
     * @return The streetAddress of this User.
     */
    public String getStreetAddress() {
        return streetAddress;
    }

    /**
     * @param streetAddress The streetAddress to set to this User.
     */
    public void setStreetAddress(String streetAddress) {
        this.streetAddress = streetAddress;
    }

    /**
     * @return The state of this User.
     */
    public String getState() {
        return state;
    }

    /**
     * @param state The state to set to this User object.
     */
    public void setState(String state) {
        this.state = state;
    }

    /**
     * @return The preferredLanguage of this User.
     */
    public String getPreferredLanguage() {
        return preferredLanguage;
    }

    /**
     * @param preferredLanguage The preferredLanguage to set to this User.
     */
    public void setPreferredLanguage(String preferredLanguage) {
        this.preferredLanguage = preferredLanguage;
    }

    /**
     * @return The postalCode of this User.
     */
    public String getPostalCode() {
        return postalCode;
    }

    /**
     * @param postalCode The postalCode to set to this User.
     */
    public void setPostalCode(String postalCode) {
        this.postalCode = postalCode;
    }

    /**
     * @return The physicalDeliveryOfficeName of this User.
     */
    public String getPhysicalDeliveryOfficeName() {
        return physicalDeliveryOfficeName;
    }

    /**
     * @param physicalDeliveryOfficeName The physicalDeliveryOfficeName to set to this User Object.
     */
    public void setPhysicalDeliveryOfficeName(String physicalDeliveryOfficeName) {
        this.physicalDeliveryOfficeName = physicalDeliveryOfficeName;
    }

    /**
     * @return The passwordPolicies of this User.
     */
    public String getPasswordPolicies() {
        return passwordPolicies;
    }

    /**
     * @param passwordPolicies The passwordPolicies to set to this User object.
     */
    public void setPasswordPolicies(String passwordPolicies) {
        this.passwordPolicies = passwordPolicies;
    }

    /**
     * @return The mobile of this User.
     */
    public String getMobile() {
        return mobile;
    }

    /**
     * @param mobile The mobile to set to this User object.
     */
    public void setMobile(String mobile) {
        this.mobile = mobile;
    }
    
    /**
     * @return The Password of this User.
     */
    public String getPassword() {
        return password;
    }

    /**
     * @param password The mobile to set to this User object.
     */
    public void setPassword(String password) {
        this.password = password;
    }

    /**
     * @return The mail of this User.
     */
    public String getMail() {
        return mail;
    }

    /**
     * @param mail The mail to set to this User object.
     */
    public void setMail(String mail) {
        this.mail = mail;
    }
    
    /**
     * @return The MailNickname of this User.
     */
    public String getMailNickname() {
        return mailNickname;
    }

    /**
     * @param mail The MailNickname to set to this User object.
     */
    public void setMailNickname(String mailNickname) {
        this.mailNickname = mailNickname;
    }


    /**
     * @return The jobTitle of this User.
     */
    public String getJobTitle() {
        return jobTitle;
    }

    /**
     * @param jobTitle The jobTitle to set to this User Object.
     */
    public void setJobTitle(String jobTitle) {
        this.jobTitle = jobTitle;
    }

    /**
     * @return The givenName of this User.
     */
    public String getGivenName() {
        return givenName;
    }

    /**
     * @param givenName The givenName to set to this User.
     */
    public void setGivenName(String givenName) {
        this.givenName = givenName;
    }

    /**
     * @return The facsimileTelephoneNumber of this User.
     */
    public String getFacsimileTelephoneNumber() {
        return facsimileTelephoneNumber;
    }

    /**
     * @param facsimileTelephoneNumber The facsimileTelephoneNumber to set to this User Object.
     */
    public void setFacsimileTelephoneNumber(String facsimileTelephoneNumber) {
        this.facsimileTelephoneNumber = facsimileTelephoneNumber;
    }

    /**
     * @return The displayName of this User.
     */
    public String getDisplayName() {
        return displayName;
    }

    /**
     * @param displayName The displayName to set to this User Object.
     */
    public void setDisplayName(String displayName) {
        this.displayName = displayName;
    }

    /**
     * @return The dirSyncEnabled of this User.
     */
    public String getDirSyncEnabled() {
        return dirSyncEnabled;
    }

    /**
     * @param dirSyncEnabled The dirSyncEnabled to set to this User.
     */
    public void setDirSyncEnabled(String dirSyncEnabled) {
        this.dirSyncEnabled = dirSyncEnabled;
    }

    /**
     * @return The department of this User.
     */
    public String getDepartment() {
        return department;
    }

    /**
     * @param department The department to set to this User.
     */
    public void setDepartment(String department) {
        this.department = department;
    }

    /**
     * @return The lastDirSyncTime of this User.
     */
    public String getLastDirSyncTime() {
        return lastDirSyncTime;
    }

    /**
     * @param lastDirSyncTime The lastDirSyncTime to set to this User.
     */
    public void setLastDirSyncTime(String lastDirSyncTime) {
        this.lastDirSyncTime = lastDirSyncTime;
    }

    /**
     * @return The country of this User.
     */
    public String getCountry() {
        return country;
    }

    /**
     * @param country The country to set to this User.
     */
    public void setCountry(String country) {
        this.country = country;
    }

    /**
     * @return The city of this User.
     */
    public String getCity() {
        return city;
    }

    /**
     * @param city The city to set to this User.
     */
    public void setCity(String city) {
        this.city = city;
    }

    /**
     * @return The accountEnabled attribute of this User.
     */
    public String getAccountEnabled() {
        return accountEnabled;
    }

    /**
     * @param accountEnabled The accountEnabled to set to this User.
     */
    public void setAccountEnabled(String accountEnabled) {
        this.accountEnabled = accountEnabled;
    }
    
    public boolean isIsDeleted() {
        return this.isDeleted;
    }

    public void setIsDeleted(boolean isDeleted) {
        this.isDeleted = isDeleted;
    }

    @Override
    public String toString() {
        return new JSONObject(this).toString();
    }
    
    public String getManagerDisplayname(){
        return managerDisplayname;
    }
    
    public void setManagerDisplayname(String managerDisplayname){
        this.managerDisplayname = managerDisplayname;
    }
}


/**
 * The Class DirectReports Holds the essential data for a single DirectReport entry. Namely,
 * it holds the displayName and the objectId of the direct entry. Furthermore, it provides the
 * access methods to set or get the displayName and the ObjectId of this entry.
 */
//class DirectReport extends User{
//
//  private String displayName;
//  private String objectId;
//   
//  /**
//   * Two arguments Constructor for the DirectReport Class.
//   * @param displayName
//   * @param objectId
//   */
//  public DirectReport(String displayName, String objectId){
//      this.displayName = displayName;
//      this.objectId = objectId;
//  }
//
//  /**
//   * @return The diaplayName of this direct report entry.
//   */
//  public String getDisplayName() {
//      return displayName;
//  }
//
//  
//  /**
//   *  @return The objectId of this direct report entry. 
//   */
//  public String getObjectId() {
//      return objectId;
//  }
//
//}

```

## <a name="7-create-the-authentication-modelcontroller-files-for-basicfilter"></a>7.建立驗證模型控制站檔案 （適用於 BasicFilter)

是的 Java 而詳細資訊，但我們即將完成。 姓氏、 旁我們撰寫處理我們要求 BasicFilter servlet 之前，請讓我們來撰寫一些更多協助程式的檔案`ADAL4J`需要文件庫。 

1. 建立檔案稱為`AuthHelper.java`讓我們來決定狀態的登入使用，我們將使用的方法。 這些功能包括︰

- `isAuthenticated()`如果使用者已有登入或不會傳回的方法
- `containsAuthenticationData()`這會告訴我們權杖是否有資料，或不
- `isAuthenticationSuccessful()`這會告訴我們是否驗證成功的使用者。

剪下/貼上下列的程式碼︰

```Java
package com.microsoft.aad.adal4jsample;

import java.util.Map;

import javax.servlet.http.HttpServletRequest;

import com.microsoft.aad.adal4j.AuthenticationResult;
import com.nimbusds.openid.connect.sdk.AuthenticationResponse;
import com.nimbusds.openid.connect.sdk.AuthenticationResponseParser;
import com.nimbusds.openid.connect.sdk.AuthenticationSuccessResponse;

public final class AuthHelper {

    public static final String PRINCIPAL_SESSION_NAME = "principal";

    private AuthHelper() {
    }

    public static boolean isAuthenticated(HttpServletRequest request) {
        return request.getSession().getAttribute(PRINCIPAL_SESSION_NAME) != null;
    }

    public static AuthenticationResult getAuthSessionObject(
            HttpServletRequest request) {
        return (AuthenticationResult) request.getSession().getAttribute(
                PRINCIPAL_SESSION_NAME);
    }

    public static boolean containsAuthenticationData(
            HttpServletRequest httpRequest) {
        Map<String, String[]> map = httpRequest.getParameterMap();
        return httpRequest.getMethod().equalsIgnoreCase("POST") && (httpRequest.getParameterMap().containsKey(
                        AuthParameterNames.ERROR)
                        || httpRequest.getParameterMap().containsKey(
                                AuthParameterNames.ID_TOKEN) || httpRequest
                        .getParameterMap().containsKey(AuthParameterNames.CODE));
    }

    public static boolean isAuthenticationSuccessful(
            AuthenticationResponse authResponse) {
        return authResponse instanceof AuthenticationSuccessResponse;
    }
}
```

2. 建立檔案稱為`AuthParameterNames.java`讓我們一些不變的變數`ADAL4J`會要求。 剪下/pate 下列動作︰

```Java
package com.microsoft.aad.adal4jsample;

public final class AuthParameterNames {

    private AuthParameterNames() {
    }

    public static String ERROR = "error";
    public static String ERROR_DESCRIPTION = "error_description";
    public static String ERROR_URI = "error_uri";
    public static String ID_TOKEN = "id_token";
    public static String CODE = "code";
}
```

3. 最後，建立一個稱為檔案`AadController.java`是我們 MVC 模式，它會提供我們 JSP 控制器，公開的控制站`secure/aad`我們應用程式的 URL 結束點。 此外，我們將此檔案以及圖形查詢。

剪下/貼上下列動作︰

```Java
package com.microsoft.aad.adal4jsample;

import java.net.HttpURLConnection;
import java.net.URL;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpSession;

import org.json.JSONArray;
import org.json.JSONObject;
import org.springframework.stereotype.Controller;
import org.springframework.ui.ModelMap;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;

import com.microsoft.aad.adal4j.AuthenticationResult;

@Controller
@RequestMapping("/secure/aad")
public class AadController {

    @RequestMapping(method = { RequestMethod.GET, RequestMethod.POST })
    public String getDirectoryObjects(ModelMap model, HttpServletRequest httpRequest) {
        HttpSession session = httpRequest.getSession();
        AuthenticationResult result = (AuthenticationResult) session.getAttribute(AuthHelper.PRINCIPAL_SESSION_NAME);
        if (result == null) {
            model.addAttribute("error", new Exception("AuthenticationResult not found in session."));
            return "/error";
        } else {
            String data;
            try {
                data = this.getUsernamesFromGraph(result.getAccessToken(), session.getServletContext()
                        .getInitParameter("tenant"));
                model.addAttribute("users", data);
            } catch (Exception e) {
                model.addAttribute("error", e);
                return "/error";
            }
        }
        return "/secure/aad";
    }

    private String getUsernamesFromGraph(String accessToken, String tenant) throws Exception {
        URL url = new URL(String.format("https://graph.windows.net/%s/users?api-version=2013-04-05", tenant,
                accessToken));

        HttpURLConnection conn = (HttpURLConnection) url.openConnection();
        // Set the appropriate header fields in the request header.
        conn.setRequestProperty("api-version", "2013-04-05");
        conn.setRequestProperty("Authorization", accessToken);
        conn.setRequestProperty("Accept", "application/json;odata=minimalmetadata");
        String goodRespStr = HttpClientHelper.getResponseStringFromConn(conn, true);
        // logger.info("goodRespStr ->" + goodRespStr);
        int responseCode = conn.getResponseCode();
        JSONObject response = HttpClientHelper.processGoodRespStr(responseCode, goodRespStr);
        JSONArray users = new JSONArray();

        users = JSONHelper.fetchDirectoryObjectJSONArray(response);

        StringBuilder builder = new StringBuilder();
        User user = null;
        for (int i = 0; i < users.length(); i++) {
            JSONObject thisUserJSONObject = users.optJSONObject(i);
            user = new User();
            JSONHelper.convertJSONObjectToDirectoryObject(thisUserJSONObject, user);
            builder.append(user.getUserPrincipalName() + "<br/>");
        }
        return builder.toString();
    }

}

```

## <a name="8-create-the-basicfilter-file-for-basicfilter-mvc"></a>8。 建立 BasicFilter 檔案 （適用於 BasicFilter MVC)

我們已最後準備好建立 BasicFilter 檔案來處理我們要求從我們的檢視 （JSP 檔案）。

建立檔案稱為`BasicFilter.java`其中包含下列動作︰

```Java

package com.microsoft.aad.adal4jsample;

import java.io.IOException;
import java.io.UnsupportedEncodingException;
import java.net.URI;
import java.net.URLEncoder;
import java.util.Date;
import java.util.HashMap;
import java.util.Map;
import java.util.UUID;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Future;

import javax.naming.ServiceUnavailableException;
import javax.servlet.Filter;
import javax.servlet.FilterChain;
import javax.servlet.FilterConfig;
import javax.servlet.ServletException;
import javax.servlet.ServletRequest;
import javax.servlet.ServletResponse;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import com.microsoft.aad.adal4j.AuthenticationContext;
import com.microsoft.aad.adal4j.AuthenticationResult;
import com.microsoft.aad.adal4j.ClientCredential;
import com.nimbusds.oauth2.sdk.AuthorizationCode;
import com.nimbusds.openid.connect.sdk.AuthenticationErrorResponse;
import com.nimbusds.openid.connect.sdk.AuthenticationResponse;
import com.nimbusds.openid.connect.sdk.AuthenticationResponseParser;
import com.nimbusds.openid.connect.sdk.AuthenticationSuccessResponse;

public class BasicFilter implements Filter {

    private String clientId = "";
    private String clientSecret = "";
    private String tenant = "";
    private String authority;

    public void destroy() {

    }

    public void doFilter(ServletRequest request, ServletResponse response,
            FilterChain chain) throws IOException, ServletException {

        if (request instanceof HttpServletRequest) {
            HttpServletRequest httpRequest = (HttpServletRequest) request;
            HttpServletResponse httpResponse = (HttpServletResponse) response;
            try {

                String currentUri = request.getScheme()
                        + "://"
                        + request.getServerName()
                        + ("http".equals(request.getScheme())
                                && request.getServerPort() == 80
                                || "https".equals(request.getScheme())
                                && request.getServerPort() == 443 ? "" : ":"
                                + request.getServerPort())
                        + httpRequest.getRequestURI();
                String fullUrl = currentUri
                        + (httpRequest.getQueryString() != null ? "?"
                                + httpRequest.getQueryString() : "");
                // check if user has a session
                if (!AuthHelper.isAuthenticated(httpRequest)) {
                    if (AuthHelper.containsAuthenticationData(httpRequest)) {
                        Map<String, String> params = new HashMap<String, String>();
                        for (String key : request.getParameterMap().keySet()) {
                            params.put(key,
                                    request.getParameterMap().get(key)[0]);
                        }
                        AuthenticationResponse authResponse = AuthenticationResponseParser
                                .parse(new URI(fullUrl), params);
                        if (AuthHelper.isAuthenticationSuccessful(authResponse)) {

                            AuthenticationSuccessResponse oidcResponse = (AuthenticationSuccessResponse) authResponse;
                            AuthenticationResult result = getAccessToken(
                                    oidcResponse.getAuthorizationCode(),
                                    currentUri);
                            createSessionPrincipal(httpRequest, result);
                        } else {
                            AuthenticationErrorResponse oidcResponse = (AuthenticationErrorResponse) authResponse;
                            throw new Exception(String.format(
                                    "Request for auth code failed: %s - %s",
                                    oidcResponse.getErrorObject().getCode(),
                                    oidcResponse.getErrorObject()
                                            .getDescription()));
                        }
                    } else {
                            // not authenticated
                            httpResponse.setStatus(302);
                            httpResponse
                                    .sendRedirect(getRedirectUrl(currentUri));
                            return;
                    }
                } else {
                    // if authenticated, how to check for valid session?
                    AuthenticationResult result = AuthHelper
                            .getAuthSessionObject(httpRequest);

                    if (httpRequest.getParameter("refresh") != null) {
                        result = getAccessTokenFromRefreshToken(
                                result.getRefreshToken(), currentUri);
                    } else {
                        if (httpRequest.getParameter("cc") != null) {
                            result = getAccessTokenFromClientCredentials();
                        } else {
                            if (result.getExpiresOnDate().before(new Date())) {
                                result = getAccessTokenFromRefreshToken(
                                        result.getRefreshToken(), currentUri);
                            }
                        }
                    }
                    createSessionPrincipal(httpRequest, result);
                }
            } catch (Throwable exc) {
                httpResponse.setStatus(500);
                request.setAttribute("error", exc.getMessage());
                httpResponse.sendRedirect(((HttpServletRequest) request)
                        .getContextPath() + "/error.jsp");
            }
        }
        chain.doFilter(request, response);
    }

    private AuthenticationResult getAccessTokenFromClientCredentials()
            throws Throwable {
        AuthenticationContext context = null;
        AuthenticationResult result = null;
        ExecutorService service = null;
        try {
            service = Executors.newFixedThreadPool(1);
            context = new AuthenticationContext(authority + tenant + "/", true,
                    service);
            Future<AuthenticationResult> future = context.acquireToken(
                    "https://graph.windows.net", new ClientCredential(clientId,
                            clientSecret), null);
            result = future.get();
        } catch (ExecutionException e) {
            throw e.getCause();
        } finally {
            service.shutdown();
        }

        if (result == null) {
            throw new ServiceUnavailableException(
                    "authentication result was null");
        }
        return result;
    }

    private AuthenticationResult getAccessTokenFromRefreshToken(
            String refreshToken, String currentUri) throws Throwable {
        AuthenticationContext context = null;
        AuthenticationResult result = null;
        ExecutorService service = null;
        try {
            service = Executors.newFixedThreadPool(1);
            context = new AuthenticationContext(authority + tenant + "/", true,
                    service);
            Future<AuthenticationResult> future = context
                    .acquireTokenByRefreshToken(refreshToken,
                            new ClientCredential(clientId, clientSecret), null,
                            null);
            result = future.get();
        } catch (ExecutionException e) {
            throw e.getCause();
        } finally {
            service.shutdown();
        }

        if (result == null) {
            throw new ServiceUnavailableException(
                    "authentication result was null");
        }
        return result;

    }

    private AuthenticationResult getAccessToken(
            AuthorizationCode authorizationCode, String currentUri)
            throws Throwable {
        String authCode = authorizationCode.getValue();
        ClientCredential credential = new ClientCredential(clientId,
                clientSecret);
        AuthenticationContext context = null;
        AuthenticationResult result = null;
        ExecutorService service = null;
        try {
            service = Executors.newFixedThreadPool(1);
            context = new AuthenticationContext(authority + tenant + "/", true,
                    service);
            Future<AuthenticationResult> future = context
                    .acquireTokenByAuthorizationCode(authCode, new URI(
                            currentUri), credential, null);
            result = future.get();
        } catch (ExecutionException e) {
            throw e.getCause();
        } finally {
            service.shutdown();
        }

        if (result == null) {
            throw new ServiceUnavailableException(
                    "authentication result was null");
        }
        return result;
    }

    private void createSessionPrincipal(HttpServletRequest httpRequest,
            AuthenticationResult result) throws Exception {
        httpRequest.getSession().setAttribute(
                AuthHelper.PRINCIPAL_SESSION_NAME, result);
    }

    private String getRedirectUrl(String currentUri)
            throws UnsupportedEncodingException {
        String redirectUrl = authority
                + this.tenant
                + "/oauth2/authorize?response_type=code%20id_token&scope=openid&response_mode=form_post&redirect_uri="
                + URLEncoder.encode(currentUri, "UTF-8") + "&client_id="
                + clientId + "&resource=https%3a%2f%2fgraph.windows.net"
                + "&nonce=" + UUID.randomUUID() + "&site_id=500879";
        return redirectUrl;
    }

    public void init(FilterConfig config) throws ServletException {
        clientId = config.getInitParameter("client_id");
        authority = config.getServletContext().getInitParameter("authority");
        tenant = config.getServletContext().getInitParameter("tenant");
        clientSecret = config.getInitParameter("secret_key");
    }

}
```

此 servlet 公開的所有方法的`ADAL4J`會預期執行應用程式。 包括︰

- `getAccessTokenFromClientCredentials()`-從我們的密碼，取得存取權杖
- `getAccessTokenFromRefreshToken()`-從的重新整理權杖，取得存取權杖
- `getAccessToken()`-取得存取權杖 OpenID 連線的流程 （其中我們使用）
- `createSessionPrincipal()`-建立的原則，我們使用圖形 API 的存取
- `getRedirectUrl()`-取得 redirectURL 至與您在輸入入口網站的值進行比較。

##<a name="compile-and-run-the-sample-in-tomcat"></a>編譯和執行 Tomcat 中的範例

變更遠至您的根目錄，然後執行下列命令以建立範例您只是放置在一起使用`maven`。 這會使用`pom.xml`您所撰寫的相依性的檔案。

`$ mvn package`

您現在應該`adal4jsample.war`檔案中您`/targets`目錄。 您可能會在您 Tomcat 容器中部署的和造訪的 URL 

`http://localhost:8080/adal4jsample/`


> [AZURE.NOTE] 
您可以輕鬆非常部署的最新的 Tomcat 伺服器馬。 只要瀏覽至`http://localhost:8080/manager/`並遵循指示上傳您 ' adal4jsample.war 」 檔案。 它會為您的 autodeploy 以正確的端點。

##<a name="next-steps"></a>後續步驟

恭喜您 ！ 現在，您可以使用 Java 應用程式的人員能夠安全地驗證的使用者，而呼叫使用 OAuth 2.0 的 Web Api，並取得基本的使用者資訊。  如果您未，現在是填入您的租用戶，且有些使用者的時間。

參照的已完成的範例 （不含您設定的值） [，提供為的.zip](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect/archive/complete.zip)，或者您可以複製從 GitHub:

```git clone --branch complete https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect.git```

