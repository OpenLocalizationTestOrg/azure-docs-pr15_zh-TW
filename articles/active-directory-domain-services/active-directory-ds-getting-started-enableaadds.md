<properties
    pageTitle="Azure AD 網域服務︰ 啟用 Azure AD 網域服務 |Microsoft Azure"
    description="快速入門 Azure Active Directory 網域服務"
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
    ms.topic="get-started-article"
    ms.date="10/19/2016"
    ms.author="maheshu"/>

# <a name="enable-azure-ad-domain-services"></a>啟用 Azure AD 網域服務

## <a name="task-3-enable-azure-ad-domain-services"></a>任務 3︰ 啟用 Azure AD 網域服務
在此工作中，您可以啟用 Azure AD 網域服務為您的目錄。 執行下列設定步驟，以啟用 Azure AD 網域服務為您的目錄。

1. 瀏覽至**Azure 傳統入口網站**([https://manage.windowsazure.com](https://manage.windowsazure.com))。

2. 選取在左窗格的 [ **Active Directory**節點。

3. 選取您要啟用 Azure AD 網域服務 Azure AD 租用戶 （目錄）。

    ![選取 Azure AD 目錄](./media/active-directory-domain-services-getting-started/select-aad-directory.png)

4. 按一下 [**設定**] 索引標籤。

    ![設定目錄] 索引標籤](./media/active-directory-domain-services-getting-started/configure-tab.png)

5. 捲動到**網域服務**一節。

    ![網域服務組態] 區段](./media/active-directory-domain-services-getting-started/domain-services-configuration.png)

6. 切換標題為 [**是]**的 [**啟用這個目錄的網域服務**的選項。 您會看到更多設定選項的 Azure AD 網域服務會出現在頁面上。

    ![啟用網域服務](./media/active-directory-domain-services-getting-started/enable-domain-services.png)

    > [AZURE.NOTE] 當您啟用租用戶的 Azure AD 網域服務時，請 Azure AD 會產生，並儲存所需的驗證使用者 Kerberos 和 NTLM 認證雜湊。

7. 指定**的網域服務的 DNS 網域名稱**。

   - 目錄的預設網域名稱 (也就以**。 onmicrosoft.com**網域後稱謂) 預設為選取狀態。

   - 清單中包含所有已設定的 Azure AD 目錄 – 包括驗證的網域為您設定 「 網域 」] 索引標籤中的未經驗證的網域。

   - 此外，您也可以輸入的自訂網域名稱。 在此範例中，我們已輸入的自訂網域名稱 」 contoso100.com 」。

     > [AZURE.WARNING] 請確定您指定 （例如，「 contoso100 」 的 「 contoso100.com 「 網域名稱） 的網域名稱的網域字首不超過 15 個字元。 您無法建立 Azure AD 網域服務網域的網域前置詞超過 15 個字元。

8. 請確定您已受管理的網域的 DNS 網域名稱已不存在虛擬網路中。 如果，特別是檢查︰

   - 您已使用相同的 DNS 網域名稱，在虛擬網路上的網域。

   - 您已選取的虛擬網路有 VPN 連線與內部部署網路，而且您內部網路上有相同的 DNS 網域名稱的網域。

   - 虛擬的網路上有該名稱與現有的雲端服務。

9. 下一步是選取虛擬網路中您想要設為可用的 Azure AD 網域服務。 選取虛擬網路和固定在標題為 [**連線到此虛擬網路的網域服務**下拉式清單中所建立的子網路。

   - 請確定您已指定虛擬網路屬於支援 Azure AD 網域服務 Azure 區域。 請參閱知道 Azure 區域 Azure AD 網域服務提供 [[依地區 Azure 服務](https://azure.microsoft.com/regions/#services/)] 頁面。

   - 屬於 Azure AD 網域服務不支援的其中一個區域的虛擬網路不會顯示在下拉式清單中。
   
   - Azure AD 網域服務中使用的專用的子網路中虛擬網路。 請確定未選取的閘道器子網路。 請參閱[網路考量](active-directory-ds-networking.md)。 

   - 同樣地，虛擬使用 Azure 資源管理員所建立的網路不在下拉式清單中。 Azure AD 網域服務目前不支援資源管理員為基礎的虛擬網路。

10. 若要啟用 Azure AD 網域服務，按一下 [從] 工作窗格底部的頁面的 [**儲存**]。

11. 頁面會顯示 「 擱置...」 狀態，而 Azure AD 網域服務已啟用您的目錄。

    ![啟用網域服務-擱置狀態](./media/active-directory-domain-services-getting-started/enable-domain-services-pendingstate.png)

    > [AZURE.NOTE] Azure AD 網域服務會提供您受管理的網域的可用性。 啟用 Azure AD 網域服務之後，請注意，網域服務，還有一個接一個虛擬網路顯示 IP 位址。 第二個 IP 位址會顯示稍後推出服務可讓您網域的可用性。 當可用性設定與您網域的作用中時，您應該會看到 [**設定**] 索引標籤的 [**網域服務**] 區段中的兩個 IP 位址。

12. 關於 20-30 分鐘之後，您會看到的網域服務會提供您在 [**設定**] 頁面上的 [ **IP 位址**] 欄位中的虛擬網路的第一個 IP 位址。

    ![啟用-網域服務第一個 IP 佈建後](./media/active-directory-domain-services-getting-started/domain-services-enabled-firstdc-available.png)

13. 可用性您網域的操作時，您會看到兩個頁面上顯示的 IP 位址。 在您選擇的虛擬網路，在這兩個 IP 位址使用您受管理的網域。 請注意下 IP 位址，您可以更新虛擬網路的 DNS 設定。 此步驟可讓虛擬機器虛擬網路連線至的網域，例如網域加入的作業。

    ![已啟用網域服務-佈建後兩個 Ip](./media/active-directory-domain-services-getting-started/domain-services-enabled-bothdcs-available.png)

> [AZURE.NOTE] 根據您 Azure AD 租用戶的大小 (數字的使用者、 群組等等)，需要一段時間的同步處理至您受管理的網域。 此同步處理程序會在幕後。 針對塞滿物件的大的租用戶，可能需要一天或兩個的所有使用者、 群組成員資格和要同步處理的認證。

<br>

## <a name="task-4---update-dns-settings-for-the-azure-virtual-network"></a>4-更新任務 Azure 虛擬網路的 DNS 設定
更新[DNS 設定的 Azure 虛擬網路](active-directory-ds-getting-started-dns.md)是下一個設定工作。
