<properties
    pageTitle="Azure Active Directory 網域服務︰ 加入受管理的網域的 Windows Server VM |Microsoft Azure"
    description="加入至 Azure AD 網域服務的 Windows Server 虛擬機器"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/02/2016"
    ms.author="maheshu"/>

# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain"></a>加入受管理網域的 Windows Server 虛擬機器

> [AZURE.SELECTOR]
- [Azure 傳統入口網站-Windows](active-directory-ds-admin-guide-join-windows-vm.md)
- [PowerShell 的 Windows](active-directory-ds-admin-guide-join-windows-vm-classic-powershell.md)

<br>

本文將示範如何加入至 Azure AD 網域服務受管理的網域，使用 [Azure 傳統入口網站執行 Windows Server 2012 R2 虛擬機器。


## <a name="step-1-create-the-windows-server-virtual-machine"></a>步驟 1︰ 建立 Windows Server 虛擬機器
依照指示[建立虛擬機器執行 Windows Azure 傳統入口網站中](../virtual-machines/virtual-machines-windows-classic-tutorial.md)的教學課程中所述。 請務必確保此新建立的虛擬機器會加入至相同的虛擬網路，您可以在其中啟用 Azure AD 網域服務。 [快速建立] 選項無法讓您加入虛擬機器虛擬網路。 因此，您需要建立虛擬機器中使用 「 從圖庫] 選項。

執行下列步驟，以建立 Windows 虛擬機器加入虛擬已啟用 Azure AD 網域服務的網路。

1. 在 Azure 的傳統入口網站中的視窗底部的命令列上按一下 [**新增**]。

2. 在**計算**，按一下**虛擬機器**]，然後按一下 [**從圖庫**。

3. 第一個畫面中可讓您**選擇圖像**的虛擬機器從清單中可用的圖像。 選擇適當的圖像。

    ![選取圖像](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-image.png)

4. 第二個畫面可讓您選擇的電腦名稱、 大小及管理使用者名稱和密碼。 使用層及執行您的應用程式或工作負載所需的大小。 您在這裡挑選的使用者名稱是在電腦上的本機系統管理員使用者。 不要輸入以下的網域使用者帳戶認證]。

    ![設定虛擬機器](./media/active-directory-domain-services-admin-guide/create-windows-vm-config.png)

5. 第三個畫面可讓您設定的網路、 儲存及可用性資源。 請確定您選取您已啟用從**地區/相關性/虛擬] 群組中的網路**] 下拉式清單的 Azure AD 網域服務的虛擬網路。 視您的虛擬機器中指定的**雲端服務的 DNS 名稱**。

    ![選取虛擬網路的虛擬機器](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-vnet.png)

    > [AZURE.WARNING]
    請確定您已啟用 Azure AD 網域服務相同的虛擬網路加入虛擬機器。 如此一來，虛擬機器可以看到該網域，並執行工作，例如加入網域。 如果您選擇建立虛擬機器中不同的虛擬網路，請將該虛擬的網路連線到虛擬已啟用 Azure AD 網域服務的網路。

6. 第四個畫面可讓您 VM 代理程式安裝及設定可用的延伸部分。

    ![完成工作](./media/active-directory-domain-services-admin-guide/create-windows-vm-done.png)

7. 建立虛擬機器之後，[傳統] 入口網站會列出新的虛擬機器**虛擬機器**節點下。 同時虛擬機器和雲端服務會自動開始時，他們的狀態會列為**執行**。

    ![虛擬機器會啟動並執行](./media/active-directory-domain-services-admin-guide/create-windows-vm-running.png)


## <a name="step-2-connect-to-the-windows-server-virtual-machine-using-the-local-administrator-account"></a>步驟 2︰ 連線到 Windows Server 虛擬機器使用本機系統管理員帳戶
現在，我們會連接到新建 Windows Server 虛擬機器，加入網域。 使用建立虛擬機器中，進行連線時，您所指定的本機系統管理員認證。

執行下列步驟，以連線至虛擬機器。

1. 瀏覽至 [傳統] 入口網站中的**虛擬機器**節點。 選取您在步驟 1 建立的虛擬機器，然後按一下視窗底部的命令列上的 [**連線**]。

    ![連線到 Windows 虛擬機器](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)

2. [傳統] 入口網站會提示您開啟或儲存檔案副檔名為 「.rdp 」，這用來連線到虛擬機器。 按一下以完成下載後，請開啟檔案。

3. 在登入提示字元中，輸入您的**本機系統管理員認證**，您所指定建立虛擬機器時。 例如，我們已在此範例中使用 'localhost\mahesh'。

此時，您應該登入到新建立 Windows 虛擬機器使用本機系統管理員認證。 下一步是虛擬機器加入網域。


## <a name="step-3-join-the-windows-server-virtual-machine-to-the-aad-ds-managed-domain"></a>步驟 3︰ 加入 Windows Server 虛擬機器 AAD DS 管理網域
執行下列步驟，以加入 AAD DS 受管理網域的 Windows Server 虛擬機器。

1. 連線到 Windows 伺服器，如下圖所示，在 [步驟 2。 從 [開始] 畫面中，開啟 [**伺服器管理員**]。

2. 在 [伺服器管理員] 視窗的左窗格中，按一下 [**本機伺服器**。

    ![虛擬機器上啟動伺服器管理員](./media/active-directory-domain-services-admin-guide/join-domain-server-manager.png)

3. 在 [**屬性**] 區段底下，按一下 [**工作群組**]。 在 [**系統內容**屬性] 頁面中，按一下 [**變更**加入網域。

    ![系統內容] 頁面](./media/active-directory-domain-services-admin-guide/join-domain-system-properties.png)

4. 指定 Azure AD 網域服務的網域名稱管理網域**的網域**] 文字方塊中，按一下**[確定]**。

    ![指定要加入的網域](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-domain.png)

5. 系統會提示您輸入您的認證以加入網域。 請確定您**指定的認證以屬於 AAD DC 系統管理員的使用者**群組。 僅限此群組的成員權限的電腦加入受管理的網域。

    ![指定的認證以網域加入](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-credentials.png)

6. 您可以使用下列其中一項來指定的認證︰

    - UPN 格式︰ Azure AD 中的設定，指定使用者帳戶的 UPN 尾碼。 在此範例中，為 「 鮑 」 之使用者的 UPN 尾碼'bob@domainservicespreview.onmicrosoft.com'。

    - SAMAccountName 格式︰ 您可以指定帳戶名稱的 SAMAccountName 格式。 在此範例中，「 鮑 」 使用者必須輸入 「 CONTOSO100\bob 」。

        > [AZURE.NOTE] **我們建議使用 UPN 格式至指定的認證。** SAMAccountName 可能會自動產生如果使用者的 UPN 首碼太長 （例如，「 joereallylongnameuser 」）。 如果有多名使用者 Azure AD 租用戶的相同的 UPN 前置詞 （例如，「 鮑 」），其 SAMAccountName 格式可能會自動產生的服務。 在下列情況下，UPN 格式可用可靠的方式來登入網域。

7. 成功加入網域後，您會看到下列訊息一則歡迎您的網域。 重新啟動虛擬機器的網域加入操作才能完成。

    ![歡迎使用網域](./media/active-directory-domain-services-admin-guide/join-domain-done.png)


## <a name="troubleshooting-domain-join"></a>疑難排解網域加入
### <a name="connectivity-issues"></a>連線問題
如果找不到網域虛擬機器，請參閱下列疑難排解步驟︰

- 請確定已連線到相同的虛擬網路，以啟用網域服務中虛擬機器。 如果沒有，虛擬機器無法連線至該網域，因此無法加入網域。

- 如果虛擬機器連線到另一個虛擬網路，請確定此虛擬網路已連線至虛擬已啟用網域服務的網路。

- 請試著偵測 （ping） 使用受管理的網域 (例如，「 偵測 （ping) contoso100.com 」) 的網域名稱的網域。 如果您無法這麼做，請試著偵測 （ping） 頁面上顯示您已啟用 Azure AD 網域服務中該網域的 IP 位址 （例如，「 偵測 （ping) 10.0.0.4 」）。 如果您無法偵測 （ping） 的 IP 位址，但不是網域，可能不正確設定 DNS。 您可能無法做為 DNS 伺服器的虛擬網路設定網域的 IP 的位址。

- 請嘗試清除 DNS 解析程式快取虛擬機器 （' ipconfig /flushdns，即可 」）。

如果您收到要求加入網域認證的對話方塊，表示您沒有連線發生問題。


### <a name="credentials-related-issues"></a>認證相關問題
如果您無法使用的認證，且無法加入網域，請參閱下列步驟。

- 請嘗試使用 UPN 格式指定的認證。 您的帳戶 SAMAccountName 可能會自動產生如果有多個使用者，以在您的租用戶中相同的 UPN 前置詞或您 UPN 前置詞為過長。 因此，您可能會不同於什麼預期或在您的內部部署網域中使用您的帳戶的 SAMAccountName 格式。

- 嘗試使用的電腦加入受管理的網域 」 AAD DC 管理員 」 群組的使用者帳戶認證。

- 請確定您已[啟用密碼同步處理](active-directory-ds-getting-started-password-sync.md)根據的快速入門指南 》 中所述的步驟。

- 請確定您使用的是使用者的 UPN，為 Azure AD 中設定 (例如，'bob@domainservicespreview.onmicrosoft.com')登入。

- 請確定您有等候久，完成的快速入門中所指定的密碼同步處理。


## <a name="related-content"></a>相關的內容

- [Azure AD 網域服務-快速入門指南](./active-directory-ds-getting-started.md)

- [管理 Azure AD 網域服務管理的網域](./active-directory-ds-admin-guide-administer-domain.md)
