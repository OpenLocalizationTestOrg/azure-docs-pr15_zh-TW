<properties
    pageTitle="如何使用群組原則的 Internet Explorer 的部署 Access 控制台副檔名 |Microsoft Azure"
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

#<a name="how-to-deploy-the-access-panel-extension-for-internet-explorer-using-group-policy"></a>如何使用群組原則的 Internet Explorer 的部署 Access 控制台副檔名

本教學課程中會顯示如何從遠端安裝 Access 控制台副檔名為 Internet Explorer 的使用者的電腦上使用群組原則。 需要 Internet Explorer，使用者需要登入應用程式設定成使用[密碼以單一登入](active-directory-appssoaccess-whatis.md#password-based-single-sign-on)至此分機]。

建議的管理員自動部署至此分機]。 否則，使用者必須下載並安裝擴充功能，其容易使用者錯誤，且需要系統管理員權限。 本教學課程涵蓋自動化軟體部署使用群組原則的其中一個方法。 [進一步瞭解群組原則。](https://technet.microsoft.com/windowsserver/bb310732.aspx)

Access 控制台副檔名也適用於[Chrome](https://go.microsoft.com/fwLink/?LinkID=311859)和[Firefox](https://go.microsoft.com/fwLink/?LinkID=626998)，這兩者需要安裝的系統管理員權限。

##<a name="prerequisites"></a>必要條件

- 您已設定[Active Directory 網域服務](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)與您已將使用者的電腦加入至您的網域。
- 您必須以編輯群組原則物件 (Gpo) 的 [編輯設定] 權限。 根據預設，下列安全性群組的成員具有此權限︰ 網域系統管理員、 企業系統管理員和群組原則建立者擁有者。 [瞭解更多。](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx)

##<a name="step-1-create-the-distribution-point"></a>步驟 1︰ 建立發佈點

首先，您必須將安裝程式套件上的可存取您想要從遠端上安裝副檔名為電腦的所有網路位置。 若要這麼做，請遵循下列步驟︰

1. 以系統管理員身分登入伺服器

2. 在 [**伺服器管理員**] 視窗中，移至 [**檔案及儲存服務**。

    ![開啟 [檔案及儲存服務](./media/active-directory-saas-ie-group-policy/files-services.png)

3. 移至 [**共用**] 索引標籤。 然後按一下 [**工作**] > **新的共用...**

    ![開啟 [檔案及儲存服務](./media/active-directory-saas-ie-group-policy/shares.png)

4. 完成**新增共用精靈**，並設定權限，以確保其可存取從使用者的電腦。 [深入瞭解共用。](https://technet.microsoft.com/library/cc753175.aspx)

5. 下載下列 Microsoft Windows Installer 套件 （.msi 檔案）︰[存取面板 Extension.msi](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access Panel Extension.msi)

6. 複製安裝程式套件上共用所要的位置。

    ![複製該.msi 檔案，您可以共用。](./media/active-directory-saas-ie-group-policy/copy-package.png)

8. 請確認您的用戶端電腦從共用存取安裝程式套件。 

##<a name="step-2-create-the-group-policy-object"></a>步驟 2︰ 建立的群組原則物件

1. 登入您的 Active Directory 網域服務 (AD DS) 安裝的伺服器。

2. 在 [伺服器管理員] 中，移至 [**工具** > **群組原則管理**。

    ![移至 [工具 > 群組原則安全性群組](./media/active-directory-saas-ie-group-policy/tools-gpm.png)

3. 在 [**群組原則管理**] 視窗的左窗格中，檢視您的組織單位 (OU) 階層，然後決定您想要套用的群組原則的範圍。 舉個例說，您可能決定要挑選一個小型 OU 部署幾個使用者，以進行測試，或選取要部署至此整個組織的頂層 OU。

    > [AZURE.NOTE] 如果您想要建立或編輯您的組織單位 (Ou)、 切換回至伺服器管理員並移至 [**工具** > **Active Directory 使用者和電腦**。

4. 您已經選取 OU，以滑鼠右鍵按一下它，並選取 [**建立 GPO，在此網域]，並連結到...**

    ![建立新的 GPO](./media/active-directory-saas-ie-group-policy/create-gpo.png)

5. 在**新的 GPO**提示中，輸入新的群組原則物件的名稱。

    ![新的名稱](./media/active-directory-saas-ie-group-policy/name-gpo.png)

6. 以滑鼠右鍵按一下您剛建立的群組原則物件，然後選取 [**編輯**]。

    ![編輯新 GPO](./media/active-directory-saas-ie-group-policy/edit-gpo.png)

##<a name="step-3-assign-the-installation-package"></a>步驟 3︰ 將指定的安裝套件

1. 決定您想要部署根據 [**電腦設定**] 或 [**使用者設定**的副檔名。 使用[電腦設定](https://technet.microsoft.com/library/cc736413%28v=ws.10%29.aspx)，會無論哪些使用者登入電腦上安裝副檔名。 另一方面，[使用者設定](https://technet.microsoft.com/library/cc781953%28v=ws.10%29.aspx)，使用者會有安裝無論的電腦登入他們的副檔名。

2. 在 [**群組原則管理編輯器**] 視窗的左窗格中，移至其中一個下列資料夾的路徑，在您選擇根據哪種類型的設定︰
    - `Computer Configuration/Policies/Software Settings/`
    - `User Configuration/Policies/Software Settings/`

3. 以滑鼠右鍵按一下 [**安裝軟體**，然後選取 [**新增** > **套件...**

    ![建立新的軟體安裝套件](./media/active-directory-saas-ie-group-policy/new-package.png)

4. 移至包含從安裝程式套件的共用資料夾[步驟 1︰ 建立發佈點](#step-1-create-the-distribution-point)、 選取該.msi 檔案，然後按一下 [**開啟**。

    > [AZURE.IMPORTANT] 如果共用位於相同的伺服器，請確認存取.msi 透過網路檔案路徑，而不是本機的檔案路徑。

    ![從 [共用] 資料夾中選取的安裝套件。](./media/active-directory-saas-ie-group-policy/select-package.png)

5. 在**部署軟體**提示中，選取您的部署方法**指派**。 然後按一下**[確定]**。

    ![選取 [指派]，然後按一下 [確定]。](./media/active-directory-saas-ie-group-policy/deployment-method.png)

副檔名為現在已部署到您所選的 OU。 [進一步瞭解群組原則軟體安裝。](https://technet.microsoft.com/library/cc738858%28v=ws.10%29.aspx)

##<a name="step-4-auto-enable-the-extension-for-internet-explorer"></a>步驟 4︰ 自動啟用 Internet Explorer 的副檔名 

除了執行安裝程式，Internet Explorer 的每個副檔名必須是明確啟用，才可以使用。 請遵循下列步驟來啟用使用群組原則的 Access 控制台副檔名為︰

1. 在 [**群組原則管理編輯器**] 視窗中，移至下列路徑，視哪種類型的設定而定，您選擇在其中一項[步驟 3︰ 指派安裝套件](#step-3-assign-the-installation-package):
    - `Computer Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`
    - `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`

2. 在 [**附加元件] 清單**中，以滑鼠右鍵按一下，然後選取 [**編輯**]。
    ![編輯附加元件清單]。](./media/active-directory-saas-ie-group-policy/edit-add-on-list.png)

3. 在 [**附加元件清單**] 視窗中，選取 [**啟用]**。 然後，在 [**選項**] 區段中，按一下 [**顯示...**。

    ![按一下 [啟用]，然後按一下 [顯示...](./media/active-directory-saas-ie-group-policy/edit-add-on-list-window.png)

4. 在 [**顯示的內容**] 視窗中，執行下列步驟︰

    1. 第一欄 （欄位**值的名稱**），複製並貼上下列的類別識別碼︰`{030E9A3F-7B18-4122-9A60-B87235E4F59E}`

    2. 第二個欄 （[**值**] 欄位） 中，輸入下列值︰`1`

    3. 按一下**[確定**] 關閉 [**顯示的內容**] 視窗。

    ![填寫上方所指定的值。](./media/active-directory-saas-ie-group-policy/show-contents.png)

5. 按一下**[確定]**套用變更並關閉 [**附加元件清單**] 視窗。

在所選 OU 機器現在應該啟用副檔名。 [進一步瞭解使用群組原則來啟用或停用 Internet Explorer 附加元件。](https://technet.microsoft.com/library/dn454941.aspx)

##<a name="step-5-optional-disable-remember-password-prompt"></a>步驟 5 （選用）︰ 停用 [記住密碼] 提示

Internet Explorer 當使用者登入網站使用 Access 控制台副檔名時，可能會顯示下列提示 「 您要儲存您的密碼嗎？ 」

![](./media/active-directory-saas-ie-group-policy/remember-password-prompt.png)

如果您想要防止使用者看到此提示中，然後依照下列步驟以避免自動完成記住密碼︰

1. 在 [**群組原則管理編輯器**] 視窗中，移至下所列的路徑。 請注意，此設定的設定只提供**使用者設定**] 底下。
    - `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/`

2. 尋找名為 [**開啟自動完成功能的使用者名稱和密碼在表單上**的設定。

    > [AZURE.NOTE] 舊版的 Active Directory 可能會列出這項設定**不允許自動完成儲存密碼**的名稱。 本教學課程中所述設定設定有所不同該設定的設定。

    ![請記得在使用者設定的外觀。](./media/active-directory-saas-ie-group-policy/disable-auto-complete.png)

3. 上述設定，請以滑鼠右鍵按一下，然後選取 [**編輯**]。

4. 在標題為 [**使用者名稱和密碼在表單上的自動完成功能開啟**的視窗中，選取 [**停用**]。

    ![選取 [停用](./media/active-directory-saas-ie-group-policy/disable-passwords.png)

5. 按一下**[確定]**以套用這些變更並關閉視窗。

使用者將無法儲存其認證或使用自動完成存取先前儲存的認證。 不過，此原則允許使用者仍繼續其他類型的表單欄位，例如搜尋欄位中使用 [自動完成。

> [AZURE.WARNING] 如果之後使用者選擇要儲存一些認證，此原則會啟用此原則，則*不*清除已儲存的認證。

##<a name="step-6-testing-the-deployment"></a>步驟 6︰ 測試部署

請遵循下列步驟來確認副檔名部署是否已順利完成︰

1. 如果您部署使用**電腦的設定**，請登入您在選取的 OU 所屬的用戶端電腦[步驟 2︰ 建立群組原則物件](#step-2-create-the-group-policy-object)。 如果您部署使用**使用者的設定**，請確定該 ou 所屬的使用者身分登入。

2. 可能需要幾個登集的群組原則會變更為完全更新與此電腦。 若要強制更新，開啟**命令提示字元**視窗，然後執行下列命令︰`gpupdate /force`

3. 您必須重新啟動電腦進行安裝。 開機，可能需要更多時間比一般時副檔名為安裝。

4. 重新啟動後，開啟**Internet Explorer**。 在視窗的右上角，按一下在**工具**（齒輪圖示），然後選取 [**管理附加元件**。

    ![移至 [工具 > 管理附加元件](./media/active-directory-saas-ie-group-policy/manage-add-ons.png)

5. 在 [**管理附加元件**] 視窗中，確認已安裝**Access 控制台副檔名**為其**狀態**] 已設定為**啟用**。

    ![請確認 Access 控制台副檔名已安裝並啟用。](./media/active-directory-saas-ie-group-policy/verify-install.png)

## <a name="related-articles"></a>相關的文章

- [Azure Active Directory 中的應用程式管理文件索引](active-directory-apps-index.md)
- [應用程式存取和單一登入與 Azure Active Directory](active-directory-appssoaccess-whatis.md)
- [Internet Explorer 的疑難排解 Access 控制台副檔名](active-directory-saas-ie-troubleshooting.md)
