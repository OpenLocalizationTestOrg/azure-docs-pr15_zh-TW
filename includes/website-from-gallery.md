Azure Marketplace 讓許多熱門的 web 應用程式開發 Microsoft、 協力廠商及開啟來源軟體計劃。 建立從 Azure Marketplace 的 web 應用程式並不需要安裝用來連線到[Azure 預覽入口網站](http://go.microsoft.com/fwlink/?LinkId=529715)瀏覽器以外的軟體。 

在本教學課程中，您將學習︰

- 如何建立新的 web 應用程式，透過 Azure Marketplace。

- 如何部署透過 Azure 預覽入口網站的 web 應用程式。
 
您會建立 WordPress 部落格使用預設範本。 下圖顯示已完成的應用程式︰


![Wordpress 部落格][13]

>[AZURE.NOTE] 如果您想要開始使用 Azure 應用程式服務註冊 Azure 帳戶之前，請移至[嘗試應用程式服務](http://go.microsoft.com/fwlink/?LinkId=523751)，可以讓您立即建立短暫入門 web 應用程式在應用程式服務。 必要; 沒有信用卡沒有承諾。

## <a name="create-a-web-app-in-the-portal"></a>建立 web 應用程式，在入口網站

1. Azure 預覽入口網站登入。

2. 按一下 [**市集**] 圖示以開啟 Azure Marketplace:

    ![服務商場圖示][marketplace]

    或按一下右上角的儀表板上的 [**新增**] 圖示，然後在清單] 的 bottow 選取**Marketplace** 。
    
    ![建立新的][5]
    
3. 選取**Web + 行動**。 搜尋**WordPress** ，並按一下 [ **WordPress** ] 圖示。

    ![從清單 WordPress][7]
    
5. 讀取之後 WordPress 應用程式的說明，請選取 [**建立**]。

6. 按一下 [在**Web 應用程式**，並提供必要的值設定 web 應用程式。
    
    ![設定您的應用程式][8]

7. 按一下 [**資料庫**]，並提供必要的值設定 MySQL 資料庫。 

    ![設定資料庫][database]

8. 提供新的資源群組的名稱。

    ![設定資源群組][groupname]

8. 如有必要，按一下 [**訂閱**]，然後指定要使用的訂閱。 

7. 當您完成定義 web 應用程式之後時，請按一下 [**建立**]，然後建立新 web 應用程式時，請等候。

   建立應用程式之後，您會看到包含 web 應用程式與資料庫的資源群組。

   ![顯示] 群組][resourcegroup]

## <a name="launch-and-manage-your-wordpress-web-app"></a>啟動和管理您的 WordPress web 應用程式
    
1. 按一下新的 web 應用程式，查看您的應用程式詳細資料。

    ![啟動儀表板][10]

2. 在 [**基本資訊**] 頁面中，按一下 [**瀏覽**或連結以開啟 web 應用程式的 [歡迎] 頁面的**Url** ] 下。

    ![網站 URL][browse]

3. 如果您沒有安裝 WordPress，請輸入所需的 WordPress 適當的設定資訊，然後按一下 [**安裝 WordPress**完成設定，並開啟 web 應用程式的登入頁面。

4. 按一下 [**登入**，然後輸入您的認證。  

5. 您將有新的 WordPress web 應用程式，看起來就像以下的 web 應用程式。    

    ![WordPress 網站][13]






[5]: ./media/website-from-gallery/start-marketplace.png
[6]: ./media/website-from-gallery/wordpressgallery-02.png
[7]: ./media/website-from-gallery/search-web-app.png
[8]: ./media/website-from-gallery/set-web-app.png
[9]: ./media/website-from-gallery/wordpressgallery-05.png
[10]: ./media/website-from-gallery/select-web.png
[13]: ./media/website-from-gallery/wordpressgallery-09.png
[webapps]: ./media/website-from-gallery/selectwebapps.png
[database]: ./media/website-from-gallery/set-db.png
[resourcegroup]: ./media/website-from-gallery/show-rg.png
[browse]: ./media/website-from-gallery/browse-web.png
[marketplace]: ./media/website-from-gallery/marketplace-icon.png
[groupname]: ./media/website-from-gallery/set-rg.png
