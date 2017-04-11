<properties
    pageTitle="建立並部署 Java API 應用程式中 Azure 應用程式服務"
    description="瞭解如何建立 Java API 應用程式套件，並將其部署至 Azure 應用程式服務。"
    services="app-service\api"
    documentationCenter="java"
    authors="bradygaster"
    manager="mohisri"
    editor="tdykstra"/>

<tags
    ms.service="app-service-api"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="java"
    ms.topic="get-started-article"
    ms.date="08/31/2016"
    ms.author="rachelap"/>

# <a name="build-and-deploy-a-java-api-app-in-azure-app-service"></a>建立並部署 Java API 應用程式中 Azure 應用程式服務

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

本教學課程中會顯示如何建立 Java 應用程式，並將其部署到 Azure 應用程式服務 API 應用程式使用[給]。 可以在能夠執行 Java 任何作業系統上遵循此教學課程中的指示進行。 本教學課程中的程式碼內建使用[Maven]。 [Jax 瞭解]用來建立 RESTful 的服務，並產生根據[Swagger]中繼資料規格使用[Swagger 編輯器]。

## <a name="prerequisites"></a>必要條件

1. [Java 開發人員套件 8]\(或更新版本)
1. [Maven]開發電腦上安裝
1. [給]您的部署電腦上安裝
1. [Microsoft Azure]付費或[免費試用版]訂閱
1. 例如[郵差]HTTP 測試應用程式

## <a name="scaffold-the-api-using-swaggerio"></a>Scaffold 使用 Swagger.IO API

您可以使用 swagger.io 線上編輯器] 中，輸入 Swagger JSON 或 YAML 程式碼，代表您的 API 的結構。 一旦您有設計的 API 表面區域，您可以匯出各種不同的平台和架構的程式碼。 在下一個區段中，將包含模擬功能修改 scaffolded 的程式碼。 

此示範會開始您會貼到 swagger.io 編輯器]，然後將產生存取 REST API 端點利用 JAX 瞭解程式碼會使用 Swagger JSON 本文。 然後，您要編輯 scaffolded 的程式碼，以傳回模擬的資料，模擬以資料保存機制 REST API。  

1. 將下列 Swagger JSON 程式碼複製到剪貼簿中︰

        {
            "swagger": "2.0",
            "info": {
                "version": "v1",
                "title": "Contact List",
                "description": "A Contact list API based on Swagger and built using Java"
            },
            "host": "localhost",
            "schemes": [
                "http",
                "https"
            ],
            "basePath": "/api",
            "paths": {
                "/contacts": {
                    "get": {
                        "tags": [
                            "Contact"
                        ],
                        "operationId": "contacts_get",
                        "consumes": [],
                        "produces": [
                            "application/json",
                            "text/json"
                        ],
                        "responses": {
                            "200": {
                                "description": "OK",
                                "schema": {
                                    "type": "array",
                                    "items": {
                                        "$ref": "#/definitions/Contact"
                                    }
                                }
                            }
                        },
                        "deprecated": false
                    }
                },
                "/contacts/{id}": {
                    "get": {
                        "tags": [
                            "Contact"
                        ],
                        "operationId": "contacts_getById",
                        "consumes": [],
                        "produces": [
                            "application/json",
                            "text/json"
                        ],
                        "parameters": [
                            {
                                "name": "id",
                                "in": "path",
                                "required": true,
                                "type": "integer",
                                "format": "int32"
                            }
                        ],
                        "responses": {
                            "200": {
                                "description": "OK",
                                "schema": {
                                    "type": "array",
                                    "items": {
                                        "$ref": "#/definitions/Contact"
                                    }
                                }
                            }
                        },
                        "deprecated": false
                    }
                }
            },
            "definitions": {
                "Contact": {
                    "type": "object",
                    "properties": {
                        "Id": {
                            "format": "int32",
                            "type": "integer"
                        },
                        "Name": {
                            "type": "string"
                        },
                        "EmailAddress": {
                            "type": "string"
                        }
                    }
                }
            }
        }

1. 瀏覽至[線上 Swagger 編輯器]。 一次，按一下 [**檔案]-> [貼上 JSON**功能表項目。

    ![貼上 JSON 功能表項目][paste-json]

1. 在連絡人清單 API Swagger JSON 您先前複製貼上。 

    ![JSON 程式碼貼入 Swagger][pasted-swagger]

1. 檢視文件頁面和呈現編輯器中的 API 摘要。 

    ![檢視 Swagger 產生文件][view-swagger-generated-docs]

1. 選取**產生伺服器]-> [JAX 瞭解**功能表選項 scaffold，您可以稍後編輯新增模擬實作伺服器端程式碼。 

    ![產生的程式碼] 功能表項目][generate-code-menu-item]

    一旦產生的程式碼，您會提供下載 ZIP 檔案。 此檔案包含 scaffolded Swagger 程式碼產生器的程式碼，所有相關聯建立指令碼。 解壓縮開發工作站目錄整份文件庫。 

## <a name="edit-the-code-to-add-api-implementation"></a>編輯程式碼，新增 API 實作

在此區段中，您將會取代 Swagger 產生的程式碼的伺服器端實作與您的自訂程式碼。 新的程式碼會傳回陣列清單的連絡人的實體呼叫的用戶端。 

1. 開啟*Contact.java*模型檔案，位於 [ *src/產生/java/io/swagger/模型*] 資料夾中，使用[Visual Studio 程式碼]或您偏好使用的文字編輯器。 

    ![開啟連絡人的模型檔案][open-contact-model-file]

1. 將**連絡人**類別中的下列建構函式。 

        public Contact(Integer id, String name, String email) 
        {
            this.id = id;
            this.name = name;
            this.emailAddress = email;
        }

1. 開啟位於 [ *src/主要/java/io/swagger/api/impl* ] 資料夾中，使用[Visual Studio 程式碼]或您偏好使用的文字編輯器的*ContactsApiServiceImpl.java*服務實作檔案。

    ![開啟連絡人的服務代碼檔案][open-contact-service-code-file]

1. 覆寫此新的程式碼新增至服務代碼的模擬實作檔案中的程式碼。 

        package io.swagger.api.impl;

        import io.swagger.api.*;
        import io.swagger.model.*;
        import com.sun.jersey.multipart.FormDataParam;
        import io.swagger.model.Contact;
        import java.util.*;
        import io.swagger.api.NotFoundException;
        import java.io.InputStream;
        import com.sun.jersey.core.header.FormDataContentDisposition;
        import com.sun.jersey.multipart.FormDataParam;
        import javax.ws.rs.core.Response;
        import javax.ws.rs.core.SecurityContext;

        @javax.annotation.Generated(value = "class io.swagger.codegen.languages.JaxRSServerCodegen", date = "2015-11-24T21:54:11.648Z")
        public class ContactsApiServiceImpl extends ContactsApiService {
  
            private ArrayList<Contact> loadContacts()
            {
                ArrayList<Contact> list = new ArrayList<Contact>();
                list.add(new Contact(1, "Barney Poland", "barney@contoso.com"));
                list.add(new Contact(2, "Lacy Barrera", "lacy@contoso.com"));
                list.add(new Contact(3, "Lora Riggs", "lora@contoso.com"));
                return list;
            }
  
            @Override
            public Response contactsGet(SecurityContext securityContext)
            throws NotFoundException {
                ArrayList<Contact> list = loadContacts();
                return Response.ok().entity(list).build();
                }
  
            @Override
            public Response contactsGetById(Integer id, SecurityContext securityContext)
            throws NotFoundException {
                ArrayList<Contact> list = loadContacts();
                Contact ret = null;
            
                for(int i=0; i<list.size(); i++)
                {
                    if(list.get(i).getId() == id)
                        {
                            ret = list.get(i);
                        }
                }
                return Response.ok().entity(ret).build();
            }
        }

1. 開啟命令提示字元中，將目錄變更您的應用程式的根資料夾。

1. 執行下列 Maven 命令來建立程式碼，並執行本機使用 Jetty 應用程式伺服器。 

        mvn package jetty:run

1. 您應該會看到反映 Jetty 已啟動您的程式碼，連接埠 8080 上的 [命令] 視窗。 

    ![開啟連絡人的服務代碼檔案][run-jetty-war]

1. 使用[郵差]進行要求 「 取得所有連絡人 」 在 http://localhost:8080/api 連絡人的 API 方法。

    ![撥號給連絡人的 API][calling-contacts-api]

1. 使用[郵差]要求 「 取得特定連絡人 」 API 方法位於 http://localhost:8080/api/連絡人/2。

    ![撥號給連絡人的 API][calling-specific-contact-api]

1. 最後，藉由執行下列 Maven 命令，在您的主控台建立 Java 馬 （Web 封存） 檔案。 

        mvn package war:war

1. 馬檔案建立後，就會放到**目標**資料夾。 瀏覽到**目標**資料夾，然後重新命名**ROOT.war**馬檔案。 （請確定大小寫符合此格式）。

         rename swagger-jaxrs-server-1.0.0.war ROOT.war

1. 最後，執行下列命令，從您的應用程式建立**部署**使用部署 Azure 馬檔案資料夾的根資料夾。 

         mkdir deploy
         mkdir deploy\webapps
         copy target\ROOT.war deploy\webapps
         cd deploy

## <a name="publish-the-output-to-azure-app-service"></a>輸出發佈至 Azure 應用程式服務

此區段中，將瞭解如何建立新的 API 應用程式，使用 [Azure 入口網站，準備裝載 Java 應用程式，該 API 應用程式和 Azure 應用程式服務執行新的 API 應用程式部署新建馬檔案。 

1. [Azure 入口網站]中建立新的 API 應用程式，按一下 [**新增]-> [Web + Mobile]-> [API 應用程式**功能表項目，輸入您的應用程式詳細資料，然後按一下 [**建立**。

    ![建立新的 API 應用程式][create-api-app]

1. 一旦您 API 應用程式建立之後，開啟您的應用程式**設定**刀，，然後按一下 [**應用程式設定**] 功能表項目。 從可用的選項，請選取最新的 Java 版本，然後從 [ **Web 容器**] 功能表中，選取 [最新的 Tomcat，然後按一下 [**儲存**。

    ![設定 API 應用程式刀 Java][set-up-java]

1. 按一下 [**部署認證**設定功能表項目，然後提供使用者名稱和密碼，您想要使用的檔案發佈至您的 API 應用程式。 

    ![設定部署認證][deployment-credentials]

1. 按一下 [**部署來源**設定] 功能表項目。 一次，按一下 [**選擇來源**] 按鈕，選取 [**本機給存放庫**] 選項中，，然後按一下**[確定]**。 這會建立給存放庫 Azure 有關聯的 API 應用程式中執行。 您認可程式碼*主*分支，您就可以給存放庫中，每次您的程式碼將發佈到即時執行 API 應用程式執行個體。 

    ![設定新的本機給存放庫][select-git-repo]

1. 新給存放庫的 URL 複製到剪貼簿。 儲存這會很重要的一些時間。 

    ![設定您的應用程式的新給儲存機制][copy-git-repo-url]

1. 給推馬檔案到線上儲存機制。 若要這麼做，請瀏覽到您先前建立的讓您可以輕鬆地認可進位到儲存機制在您的應用程式服務中執行的程式碼，將 [**部署**] 資料夾。 您是主控台視窗中，並瀏覽到 webapps 資料夾的所在位置的資料夾，請提交下列給命令來啟動程序，並啟動關閉部署。 

         git init
         git add .
         git commit -m "initial commit"
         git remote add azure [YOUR GIT URL]
         git push azure master

    一旦您發出**發送**要求時，系統會要求您先前建立的部署認證的密碼。 輸入您的認證後，您應該會看到您顯示已部署更新的入口網站。

1. 如果您再次叫用新部署 API 應用程式執行 Azure 應用程式服務中使用郵差，您會看到是一致的行為，現在會傳回連絡人資料如預期般，，並使用簡單的程式碼變更 Swagger.io scaffolded Java 程式碼。 

    ![Azure 中使用您 Java 連絡人 REST API 即時][postman-calling-azure-contacts]

## <a name="next-steps"></a>後續步驟

在本文中，您就可以開始 Swagger JSON 檔案，然後部分 scaffolded 取自 Swagger.io 編輯器 Java 程式碼]。 從這裡簡單的變更，就可以給部署程序導致無法運作的 API 應用程式，以 Java 撰寫。 下一個教學課程會顯示如何[使用 JavaScript 用戶端，使用 CORS API 應用程式][App Service API CORS]。 更新版本的教學課程系列中會顯示如何實作驗證與授權。

若要建立在此範例中，您可以進一步瞭解[儲存 SDK java]保存 JSON 二進位大型物件。 或者，您可以使用[文件 DB Java SDK] Azure 文件 db 儲存您的連絡人資料。 

如需有關使用 Java Azure 中的詳細資訊，請參閱[Java 開發人員中心]。

<!-- URL List -->

[App Service API CORS]: app-service-api-cors-consume-javascript.md
[Azure 入口網站]: https://portal.azure.com/
[文件 DB Java SDK]: ../documentdb/documentdb-java-application.md
[免費試用版]: https://azure.microsoft.com/pricing/free-trial/
[給]: http://www.git-scm.com/
[Java 開發人員中心]: /develop/java/
[Java 開發人員套件 8]: http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html
[Jax 瞭解]: https://jax-rs-spec.java.net/
[Maven]: https://maven.apache.org/
[Microsoft Azure]: https://azure.microsoft.com/
[線上 Swagger 編輯器]: http://editor.swagger.io/
[郵差]: https://www.getpostman.com/
[儲存 java SDK]: ../storage/storage-java-how-to-use-blob-storage.md
[Swagger]: http://swagger.io/
[Swagger 編輯器]: http://editor.swagger.io/
[Visual Studio 程式碼]: https://code.visualstudio.com

<!-- IMG List -->

[paste-json]: ./media/app-service-api-java-api-app/paste-json.png
[pasted-swagger]: ./media/app-service-api-java-api-app/pasted-swagger.png
[view-swagger-generated-docs]: ./media/app-service-api-java-api-app/view-swagger-generated-docs.png
[generate-code-menu-item]: ./media/app-service-api-java-api-app/generate-code-menu-item.png
[open-contact-model-file]: ./media/app-service-api-java-api-app/open-contact-model-file.png
[open-contact-service-code-file]: ./media/app-service-api-java-api-app/open-contact-service-code-file.png
[run-jetty-war]: ./media/app-service-api-java-api-app/run-jetty-war.png
[calling-contacts-api]: ./media/app-service-api-java-api-app/calling-contacts-api.png
[calling-specific-contact-api]: ./media/app-service-api-java-api-app/calling-specific-contact-api.png
[create-api-app]: ./media/app-service-api-java-api-app/create-api-app.png
[set-up-java]: ./media/app-service-api-java-api-app/set-up-java.png
[deployment-credentials]: ./media/app-service-api-java-api-app/deployment-credentials.png
[select-git-repo]: ./media/app-service-api-java-api-app/select-git-repo.png
[copy-git-repo-url]: ./media/app-service-api-java-api-app/copy-git-repo-url.png
[postman-calling-azure-contacts]: ./media/app-service-api-java-api-app/postman-calling-azure-contacts.png
