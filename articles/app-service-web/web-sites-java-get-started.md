<properties
    pageTitle="Azure 應用程式服務中建立 Java web 應用程式 |Microsoft Azure"
    description="本教學課程中會顯示如何將 Java web 應用程式部署 Azure 應用程式服務。"
    services="app-service\web"
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="get-started-article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

# <a name="create-a-java-web-app-in-azure-app-service"></a>Azure 應用程式服務中建立 Java web 應用程式

[AZURE.INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

本教學課程中會顯示如何建立 Java [Azure 應用程式服務中的 web 應用程式]使用[Azure 入口網站]。 Azure 入口網站是您可以用來管理您的 Azure 資源的 web 介面。

> [AZURE.NOTE] 若要完成此教學課程中，您需要 Microsoft Azure 帳戶。 如果您沒有帳戶，您可以[啟動您的 Visual Studio 訂閱權益]或[註冊免費試用版]。
>
> 如果您想要快速入門 Azure 應用程式服務註冊 Azure 帳戶之前，請移至[嘗試應用程式服務]。 那里，您可以立即建立短暫入門 web 應用程式的應用程式服務;沒有信用卡資格和沒有承諾。

## <a name="java-application-options"></a>Java 應用程式選項

有數種方式，您可以設定應用程式服務 web 應用程式中 Java 應用程式。 

1. 建立應用程式，然後設定 [**應用程式設定**。

    應用程式服務，提供數種 Tomcat 與 Jetty 版本的預設設定。 如果您將主控的應用程式會使用其中一個內建的版本，這個設定網頁容器的方法是簡單的方法時，則完美所有您想要做為上傳至網頁容器的馬檔案。 這個方法，您 Azure 入口網站中建立應用程式，然後移至您選擇的 Java 的版本，以及所需的 Java web 容器的應用程式的**應用程式設定**刀。 當您使用這個方法是從 [Program Files 執行 Java 和您的 web 容器。 其他方法將 web 容器和潛在 JVM 磁碟空間。 當您使用此模型時，您不需要在檔案系統的此組件中編輯檔案的權限。 這表示您無法在執行項目，例如設定*server.xml*檔案，或將*/lib*資料夾中的文件庫檔案。 如需詳細資訊，請參閱[建立及設定 Java web app](#appsettings)本教學課程中的一節。
    
2. 從 Azure Marketplace 使用範本。

    Azure Marketplace 包含自動建立及設定 Tomcat 或 Jetty web 容器 Java web 應用程式範本。 範本建立 web 容器進行設定。 如需詳細資訊，請參閱本教學課程中的 [[使用從 Azure Marketplace Java 範本](#marketplace)] 區段。
  
3. 建立應用程式，然後手動複製並編輯設定檔 

    您可能會想要主控不會在任何應用程式服務所提供的 web 容器中部署自訂 Java 應用程式。 例如︰
    
    * Java 應用程式需要 Tomcat 或 Jetty 直接不受支援的應用程式服務或圖庫中提供的版本。
    * Java 應用程式會 HTTP 要求，而不會為一個部署至現有的 web 容器。
    * 您想要自行設定網頁容器中從頭開始。 
    * 您想要使用的不支援的應用程式服務的 Java 版本，而且想要上傳您自己。

    類似的情況下，您可以建立使用 Azure 入口網站應用程式，並以手動方式提供適當的執行階段檔案。 在此情況下，檔案會針對您應用程式服務計劃您儲存空間配額進行計算。 如需詳細資訊，請參閱[上傳至 Azure 自訂 Java web 應用程式]。

## <a name="portal"></a>建立及設定 Java web 應用程式

本節說明如何建立 web 應用程式，並將它設定為 Java 使用入口網站的**應用程式設定**刀。

1. [Azure 入口網站]登入。

2. 按一下 [**新增 > Web + 行動 > Web 應用程式**。

    ![新的 Web 應用程式][newwebapp]

4. 在**Web 應用程式**] 方塊中輸入的 web 應用程式的名稱。

    此名稱必須是唯一的 azurewebsites.net 網域，因為 web 應用程式的 URL 會 {名稱}。 azurewebsites.net。 如果您輸入的名稱不是唯一的一個紅色驚嘆號會出現在 [文字] 方塊中。

5. 選取**資源群組**或建立新的項目。

    如需有關資源群組的詳細資訊，請參閱[使用 Azure 入口網站管理 Azure 資源]。

6. 選取**應用程式服務方案/位置**，或建立新的項目。

    如需有關應用程式服務方案的詳細資訊，請參閱[Azure 應用程式服務方案概觀]。

7. 按一下 [**建立**]。

    ![建立 Web 應用程式][newwebapp2]
 
8. 建立 web 應用程式之後，按一下 [ **Web 應用程式 > {web 應用程式}**。
 
    ![選取 Web 應用程式][selectwebapp]

9. 在**Web 應用程式**刀中，按一下 [**設定**]。

10. 按一下 [**應用程式設定**]。

11. 選擇所要的**Java 版本**。 

12. 選擇所要的**Java 次要版本**。 如果您選取**最新**時，您的應用程式會使用該 Java 主要版本的應用程式服務中使用的最新的次要版本。 **最新**項目是唯一 Java 應用程式建立的**應用程式設定**。 如果您是從圖庫建立 Java 應用程式，您就必須管理您自己的容器和 JVM 變更。 

12. 選擇所需的**Web 容器**。 如果您選取的第一句是**最新**容器名稱，您的應用程式將會保留在該網站容器主要版本中應用程式服務所提供的最新版本。 

    ![Web 容器版本][versions]

13. 按一下 [**儲存**]。

    在幾分鐘的時間，您的 web 應用程式會變成 Java 為基礎，且設定為使用您所選的 web 容器。

14. 按一下 [ **Web 應用程式 > {新的 web 應用程式}**。

15. 按一下以瀏覽至新網站的**URL** 。

    網頁上，確認您已建立 Java 式 web 應用程式。

## <a name="marketplace"></a>從 Azure Marketplace 使用 Java 範本

本節說明如何使用 Azure Marketplace 建立 Java web 應用程式。 相同的一般流程也可以用於建立 Java 為基礎的行動電話] 或 [API 應用程式。 

1. 登入[Azure 入口網站]

2. 按一下 [**新增 > 服務商場**。

    ![新的服務商場][newmarketplace]

3. 按一下 [ **Web + Mobile**]。

    您可能必須向左捲動以查看**服務商場**刀您可以在其中選取**Web + 行動**。

4. 在 [搜尋文字] 方塊中輸入**Apache Tomcat**或**Jetty**，例如 Java 應用程式伺服器的名稱，然後按一下 Enter。

5. 在搜尋結果中，按一下 Java 應用程式伺服器。

    ![Web 行動 Jetty][webmobilejetty]

6. 在第一**Apache Tomcat**或**Jetty**把，按一下 [**建立**。

    ![Jetty 入口網站刀][jettyblade]

7. 在下一個**Apache Tomcat**或**Jetty**刀輸入在**Web 應用程式**] 方塊中的 web 應用程式的名稱。

    此名稱必須是唯一的 azurewebsites.net 網域，因為 web 應用程式的 URL 會 {名稱}。 azurewebsites.net。 如果您輸入的名稱不是唯一的一個紅色驚嘆號會出現在 [文字] 方塊中。

8. 選取**資源群組**或建立新的項目。

    如需有關資源群組的詳細資訊，請參閱[使用 Azure 入口網站管理 Azure 資源]。

9. 選取**應用程式服務方案/位置**，或建立新的項目。

    如需有關應用程式服務方案的詳細資訊，請參閱[Azure 應用程式服務方案概觀]。

10. 按一下 [**建立**]。

    ![建立 jetty 入口網站][jettyportalcreate2]

    在一段時間，通常不到一分鐘，Azure 完成建立新 web 應用程式。

11. 按一下 [ **Web 應用程式 > {新的 web 應用程式}**。

12. 按一下以瀏覽至新網站的**URL** 。

    ![Jetty URL][jettyurl]

    Tomcat 隨附一組預設的網頁。如果您選擇 Tomcat 時，您會看到類似以下的範例頁面。

    ![使用 Apache Tomcat 的 web 應用程式][tomcat]

    如果您選擇 Jetty 時，您會看到類似以下的範例頁面。 Jetty 不讓一組的預設頁面，以下重複使用相同的 JSP 空白 Java 網站使用。

    ![使用 Jetty 的 web 應用程式][jetty]

現在，您已使用應用程式容器建立 web 應用程式，請參閱如何上傳您的 web 應用程式的應用程式的相關資訊的 [[下一步](#next-steps)] 區段。

## <a name="next-steps"></a>後續步驟

此時，必須在 Azure 應用程式服務 web 應用程式中執行的 Java 應用程式伺服器。 若要將您自己的程式碼部署到 web 應用程式中，請參閱[新增應用程式或網頁 Java web 應用程式]。

如需開發 Java Azure 中的應用程式的詳細資訊，請參閱[Java 開發人員中心]。

<!-- URL List -->

[新增至您 Java web 應用程式的應用程式或網頁]: ./web-sites-java-add-app.md
[Azure 應用程式服務方案概觀]: ../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md
[Azure 入口網站]: https://portal.azure.com/
[啟動您的 Visual Studio 訂閱優惠]: http://go.microsoft.com/fwlink/?LinkId=623901
[註冊免費試用版]: http://go.microsoft.com/fwlink/?LinkId=623901
[請嘗試應用程式服務]: http://go.microsoft.com/fwlink/?LinkId=523751
[Azure 應用程式服務中的 web 應用程式]: http://go.microsoft.com/fwlink/?LinkId=529714
[Java 開發人員中心]: /develop/java/
[使用 [Azure 入口網站管理 Azure 資源]: ../azure-portal/resource-group-portal.md
[將自訂的 Java web 應用程式上傳至 Azure]: ./web-sites-java-custom-upload.md

<!-- IMG List -->

[newwebapp]: ./media/web-sites-java-get-started/newwebapp.png
[newwebapp2]: ./media/web-sites-java-get-started/newwebapp2.png
[selectwebapp]: ./media/web-sites-java-get-started/selectwebapp.png
[versions]: ./media/web-sites-java-get-started/versions.png
[newmarketplace]: ./media/web-sites-java-get-started/newmarketplace.png
[webmobilejetty]: ./media/web-sites-java-get-started/webmobilejetty.png
[jettyblade]: ./media/web-sites-java-get-started/jettyblade.png
[jettyportalcreate2]: ./media/web-sites-java-get-started/jettyportalcreate2.png
[jettyurl]: ./media/web-sites-java-get-started/jettyurl.png
[tomcat]: ./media/web-sites-java-get-started/tomcat.png
[jetty]: ./media/web-sites-java-get-started/jetty.png
