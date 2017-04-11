<properties
    pageTitle="Internet Explorer 的疑難排解 Access 控制台副檔名 |Microsoft Azure"
    description="如何使用群組原則部署 Internet Explorer 附加元件的 [我的應用程式入口網站。"
    services="active-directory"
    documentationCenter=""
    authors="MarkusVi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="08/16/2016"
    ms.author="markvi"/>

#<a name="troubleshooting-the-access-panel-extension-for-internet-explorer"></a>Internet Explorer 的疑難排解 Access 控制台副檔名

本文可協助您解決下列問題︰

- 您無法使用 Internet Explorer 時可以透過 [我的應用程式入口網站存取您的應用程式。
- 即使您已經安裝軟體，您會看到 「 安裝軟體 」 訊息。

如果您是系統管理員，請參閱︰[如何部署使用群組原則的 Internet Explorer 的 Access 控制台副檔名](active-directory-saas-ie-group-policy.md)

##<a name="run-the-diagnostic-tool"></a>執行診斷工具

您可以下載並執行存取面板診斷工具診斷 Access 控制台副檔名安裝問題︰

1. [若要下載的診斷工具，請按一下這裡。](https://account.activedirectory.windowsazure.com/applications/AccessPanelExtensionDiagnosticTool/AccessPanelExtensionDiagnosticTool.zip)

2. 開啟檔案，然後按**擷取所有**] 按鈕。

    ![按下擷取所有](./media/active-directory-saas-ie-troubleshooting/extract1.png)

3. 然後按下 [**擷取**] 按鈕，繼續。

    ![按下解壓縮](./media/active-directory-saas-ie-troubleshooting/extract2.png)

4. 若要執行這個工具，以滑鼠右鍵按一下檔案名稱為**AccessPanelExtensionDiagnosticTool**，然後選取 [**開啟 > Microsoft Windows 基礎 Script Host**。

    ![開啟 > Microsoft Windows Script host （主機)](./media/active-directory-saas-ie-troubleshooting/open_tool.png)

5. 然後，您會看到下列診斷視窗，將您的安裝問題的說明。

    ![[診斷] 視窗的範例](./media/active-directory-saas-ie-troubleshooting/tool_preview.png)

6. 按一下 「****」，讓修正找到問題的程式。

7. 若要儲存這些變更，關閉每個 Internet Explorer 視窗，然後再開啟 Internet Explorer。<br />如果您仍無法存取您的應用程式，請嘗試下列步驟。

##<a name="check-that-the-access-panel-extension-is-enabled"></a>檢查已啟用 Access 控制台副檔名

若要確認 Access 控制台副檔名已啟用 Internet Explorer 中︰

1. 在 Internet Explorer 中，按一下上的視窗右上角的**齒輪圖示**。 然後選取 [**網際網路選項]**。<br />(較舊版本的 Internet Explorer 中您可以找到這下**工具 > 網際網路選項**。

    ![移至 [工具 > 網際網路選項](./media/active-directory-saas-ie-troubleshooting/internetoptions.png)

2. 按一下 [**程式**] 索引標籤，然後按一下 [**管理附加元件**] 按鈕。

    ![按一下 [管理附加元件](./media/active-directory-saas-ie-troubleshooting/internetoptions_programs.png)

3. 在此對話方塊中，選取**Access 控制台副檔名**，然後按一下 [**啟用**] 按鈕。

    ![按一下 [啟用](./media/active-directory-saas-ie-troubleshooting/enableaddon.png)

4. 若要儲存這些變更，關閉每個 Internet Explorer 視窗並重新開啟 Internet Explorer。

##<a name="enable-extensions-for-inprivate-browsing"></a>啟用副檔名 InPrivate 瀏覽

如果您使用的 InPrivate 瀏覽模式︰

1. 在 Internet Explorer 中，按一下上的視窗右上角的**齒輪圖示**。 然後選取 [**網際網路選項]**。<br />(較舊版本的 Internet Explorer 中您可以找到這下**工具 > 網際網路選項**。

    ![[診斷] 視窗的範例](./media/active-directory-saas-ie-troubleshooting/inprivateoptions.png)

2. 移至 [**隱私權**] 索引標籤，然後連標示為 [**停用工具列和延伸模組 InPrivate 瀏覽啟動時**核取方塊 [**取消核取**</p>

    ![取消核取 [停用工具列和延伸模組 InPrivate 瀏覽啟動時](./media/active-directory-saas-ie-troubleshooting/enabletoolbars.png)

3. 若要儲存這些變更，關閉每個 Internet Explorer 視窗並重新開啟 Internet Explorer。

##<a name="uninstall-the-access-panel-extension"></a>解除安裝 Access 控制台副檔名

若要從電腦解除安裝 Access 控制台副檔名為︰

1. 在鍵盤上按**Windows 鍵**以開啟 [開始] 功能表。 開啟功能表時，您可以輸入要搜尋的任何項目。 輸入 「 控制台 」，然後開啟 [**控制台**] 時，它會出現在搜尋結果。

    ![搜尋 [控制台]](./media/active-directory-saas-ie-troubleshooting/search_sm.png)

2. 在右上角的 [控制台]，變更 [**檢視方式]**選項為 [**大圖示**。 接著尋找，然後按一下 [**程式和功能**] 按鈕。

    ![若要顯示 [大圖示 Chang 檢視](./media/active-directory-saas-ie-troubleshooting/control_panel.png)

3. 從清單中，選取 [ **Access 控制台副檔名**]，然後再按一下 [**解除安裝**] 按鈕上。

    ![按一下 [解除安裝](./media/active-directory-saas-ie-troubleshooting/uninstall.png)

4. 您可以再嘗試安裝副檔名為一次以查看是否已解決問題。

如果您遇到問題解除安裝副檔名，您也可以移除其使用[Microsoft 修正該](https://go.microsoft.com/?linkid=9779673)工具。

## <a name="related-articles"></a>相關的文章

- [Azure Active Directory 中的應用程式管理文件索引](active-directory-apps-index.md)
- [應用程式存取和單一登入與 Azure Active Directory](active-directory-appssoaccess-whatis.md)
- [如何使用群組原則的 Internet Explorer 的部署 Access 控制台副檔名](active-directory-saas-ie-group-policy.md)
