雖然可能 MongoLab URI 貼上您的程式碼，建議您環境中，以便於的管理設定。 如此一來，如果 URI 變更，您可以更新，透過 Azure 入口網站不跳至程式碼。


1. 在 [Azure 入口網站中，選取 [ **Web 應用程式**]。
1. 按一下 [在 Web 應用程式清單中的 web 應用程式的名稱。  
![WebAppEntry][entry-website]  
顯示 [Web App 儀表板。

1. 按一下功能表列中的 [**設定**]。  
![WebAppDashboardConfig][focus-mongolab-websitedashboard-config]

1. 捲動到 [連線字串] 區段。  
![WebAppConnectionStrings][focus-mongolab-websiteconnectionstring]

1. [**名稱**] 中，輸入 MONGOLAB_URI。
1. 針對**值**，貼上您在前一節我們取得連線字串。
1. 選取 [**類型**] 下拉式清單 （而非預設**SQLAzure**） 中的 [**自訂**]。
1. 在工具列上，按一下 [**儲存**]。  
![SaveWebApp][button-website-save]

**附註︰**Azure 新增**CUSTOMCONNSTR\_**加上字首，此變數，會參照上方的程式碼**CUSTOMCONNSTR\_MONGOLAB_URI。**

[entry-website]: ./media/howto-save-connectioninfo-mongolab/entry-website.png
[focus-mongolab-websitedashboard-config]: ./media/howto-save-connectioninfo-mongolab/focus-mongolab-websitedashboard-config.png
[focus-mongolab-websiteconnectionstring]: ./media/howto-save-connectioninfo-mongolab/focus-mongolab-websiteconnectionstring.png
[button-website-save]: ./media/howto-save-connectioninfo-mongolab/button-website-save.png
