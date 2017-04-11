<properties
    pageTitle="設定網域的 HDInsight 叢集 |Microsoft Azure"
    description="瞭解如何設定及設定網域的 HDInsight 叢集"
    services="hdinsight"
    documentationCenter=""
    authors="saurinsh"
    manager="jhubbard"
    editor="cgronlun"
    tags=""/>

<tags
    ms.service="hdinsight"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="10/26/2016"
    ms.author="saurinsh"/>

# <a name="configure-domain-joined-hdinsight-clusters-preview"></a>設定網域的 HDInsight 叢集 （預覽版本）

瞭解如何設定與 Azure Active Directory (Azure AD) [Apache Ranger](http://hortonworks.com/apache/ranger/)利用強式驗證和豐富的角色型存取控制 (RBAC) 原則 Azure HDInsight 叢集。  加入網域的 HDInsight 只可以設定 Linux 型叢集上。 如需詳細資訊，請參閱[介紹網域 HDInsight 叢集](hdinsight-domain-joined-introduction.md)。

本文是一系列的第一個教學課程︰

- 建立連線到 （透過 Azure Directory 網域服務功能） Azure AD HDInsight 叢集 Apache Ranger 啟用。
- 建立和套用 Apache 遊俠，透過群組原則，讓使用者 （例如，資料科學家） 連線到使用 ODBC 為基礎的工具，例如 Excel 等 Tableau 登錄區。Microsoft 正在使用其他工作負載，例如 HBase、 火花，以及大量，新增至網域 HDInsight 推出。

最終拓撲範例如下所示︰

![加入網域的 HDInsight 拓撲](.\media\hdinsight-domain-joined-configure\hdinsight-domain-joined-topology.png)

因為 Azure AD 目前僅支援傳統虛擬網路 (VNets) 並 Linux 型 HDInsight 叢集只支援 Azure 資源管理員 VNets，HDInsight Azure AD 整合需要兩個 VNets 與它們之間對等。 兩種部署模型之間的比較資訊，請參閱[與傳統部署 Azure 資源管理員︰ 瞭解部署模型，以及您資源的狀態](../resource-manager-deployment-model.md)。 兩個 VNets 必須是 Azure AD DS 為相同的區域。

Azure 服務名稱必須是全域唯一的。 在此教學課程中會使用下列名稱。 Contoso 是虛構的名稱。 當您完成教學課程，您必須以不同的名稱取代*contoso* 。 
    
**名稱︰**

|屬性|值|
|--------|-----|
| Azure AD VNet|contosoaadvnet|
| Azure AD 虛擬機器 (VM)|contosoaadadmin。 此 VM 用來設定組織單位並反向 DNS 區域。|
| Azure AD 目錄|contosoaaddirectory|
| Azure AD 網域名稱|contoso (contoso.onmicrosoft.com)|
| HDInsight VNet|contosohdivnet|
| HDInsight VNet 資源群組|contosohdirg|
| HDInsight 叢集|contosohdicluster|

本教學課程中提供的步驟設定網域的 HDInsight 叢集。 每個區段會有更多背景資訊連結至其他文件。

## <a name="prerequisite"></a>必要︰

- 自我熟悉[Azure AD 網域服務](https://azure.microsoft.com/services/active-directory-ds/)[價格](https://azure.microsoft.com/pricing/details/active-directory-ds/)結構。
- 請確定您的訂閱 whitelisted 此公用的預覽。 您可以藉由傳送電子郵件以執行hdipreview@microsoft.com與您的訂閱 id。
- SSL 憑證所簽署的簽章的授權單位為您的網域。 設定 [安全的 LDAP 所需的憑證。 自我簽署的憑證不能使用。

## <a name="procedures"></a>程序

1. 建立您 Azure AD Azure 的傳統 VNet。  
2. 建立及設定 Azure AD 和 Azure AD DS。
3. 若要建立組織單位的傳統 VNet 新增 VM。 
4. 建立 Azure AD ds 組織單位。
5. 建立 HDInsight VNet Azure 資源管理模式中。
6. Azure AD ds 中設定反向 DNS 區域。
6. 對兩個 VNets。
7. 建立 HDInsight 叢集。

> [AZURE.NOTE] 本教學課程，假設您沒有 Azure AD。 如果您有一個，您可以略過在步驟 2 中的部分。
    
## <a name="create-an-azure-classic-vnet"></a>建立 Azure 的傳統 VNet

在此區段中，您可以建立傳統 VNet，使用 [Azure 入口網站。 在下一個區段中，您可以啟用 Azure AD DS 的傳統 VNet 中 Azure AD。 如需有關下列程序，並使用其他 VNet 建立方法的詳細資訊，請參閱[建立虛擬網路 （傳統） 使用 Azure 入口網站](../virtual-network/virtual-networks-create-vnet-classic-portal.md)。

**若要建立傳統的 VNet**

1. [Azure 入口網站](https://portal.azure.com)登入。 
2. 按一下 [**新** > **網路** > **虛擬網路**。
3. **選取 [部署模型**，請選取 [**傳統**，，然後按一下 [**建立**。
4. 輸入或選取下列值︰

    - **名稱**︰ contosoaadvnet
    - **位址空間**︰ 10.1.0.0/16
    - **子網路名稱**︰ Subnet1
    - **子網路位址範圍**︰ 10.1.0.0/24
    - **訂閱**: （選取用來建立此 VNet 訂閱）。
    - **ResourceGroup**:
    - **位置**: （選取 HDInsight 叢集的區域。）

        > [AZURE.IMPORTANT] 您必須選擇支援 Azure AD DS 的位置。 如需詳細資訊，請參閱[區域所提供的產品](https://azure.microsoft.com/en-us/regions/services/)。 
        >
        > 傳統 VNet] 與 [資源群組 VNet 必須是 Azure AD DS 為相同的區域。

5. 按一下 [**建立**]，建立 VNet]。

## <a name="create-and-configure-azure-ad-ds-for-your-azure-ad"></a>建立及設定 Azure AD DS 的 Azure AD

在此區段中，您將會︰

1. 建立 Azure AD。
2. 建立 Azure AD 使用者。 這些使用者是網域。 您可以使用的第一個使用者的 HDInsight 叢集與 Azure AD 的設定。  本教學課程中的其他兩個使用者是選擇性的。 他們會使用中[設定登錄區原則的網域的 HDInsight 叢集](hdinsight-domain-joined-run-hive.md)，當您設定 Apache Ranger 原則。
3. 建立 AAD DC 管理員群組，並將 Azure AD 使用者新增至群組。 您可以使用此使用者來建立組織的單位。
4. 啟用 Azure AD Azure AD 網域服務 (Azure AD DS)。
7. 設定 Azure AD LDAPS。 輕量型目錄存取通訊協定 (LDAP) 用來讀取和寫入 Azure ad。

如果您想要使用現有的 Azure AD，您可以略過步驟 1 和 2。

**若要建立 Azure AD**

1. 從[Azure 傳統入口網站](https://manage.windowsazure.com)中，按一下 [**新增** > **應用程式服務** > **Active Directory** > **目錄** > **建立自訂**。 
3. 輸入或選取下列值︰

    - **名稱**︰ contosoaaddirectory
    - **網域名稱**︰ contoso。  此名稱必須是全域唯一的。
    - **國家或地區**︰ 選取您的國家/地區或區域。
4. 按一下 [**完成**]。


**建立 Azure AD 使用者**

1. 從[Azure 傳統入口網站](https://manage.windowsazure.com)中，按一下 [ **Active Directory** -> **contosoaaddirectory**。 
3. 從上方的功能表中按一下 [**使用者**]。
4. 按一下 [**新增使用者**]。
4. 輸入**使用者名稱**，然後再按 [**下一步**。 
5. 設定使用者設定檔。在 [**角色**中，選取 [**全域管理員**;然後按一下 [**下一步**。  若要建立組織單位需要的全域管理員角色。
6. 按一下 [**建立**]，取得暫時密碼。
7. 複製的密碼]，然後再按一下 [**完成**。 稍後在本教學課程中，您會使用此全域管理員的使用者登入至 [管理員 VM 建立組織單位及設定反向 DNS。


請遵循使用**使用者**角色、 hiveuser1 和 hiveuser2 建立兩個更多使用者的相同程序。 下列使用者將使用中[設定登錄區原則的網域的 HDInsight 叢集](hdinsight-domain-joined-run-hive.md)中。

**若要建立 AAD DC 系統管理員的群組，並新增 Azure AD 使用者**

1. 從[Azure 傳統入口網站](https://manage.windowsazure.com)中，按一下 [ **Active Directory** > **contosoaaddirectory**。 
3. 從上方的功能表中按一下 [**群組**]。
4. 按一下 [**新增群組**] 或 [**新增群組**]。
5. 輸入或選取下列值︰

    - **名稱**︰ AAD DC 系統管理員。  不會變更群組的名稱。
    - **群組類型**︰ 安全性。
6. 按一下 [**完成**]。
7. 按一下 [ **AAD DC 系統管理員**，以開啟群組]。
8. 按一下 [**新增成員**]。
9. 選取您在上一個步驟中，您建立的第一個使用者，然後再按一下 [**完成**]。
10. 重複相同的步驟來建立名為**HiveUsers**，另一個群組，並將兩個登錄區使用者新增至群組。

如需詳細資訊，請參閱[Azure AD 網域服務 （預覽版本）-建立 AAD DC 管理員 」 群組](../active-directory-domain-services/active-directory-ds-getting-started.md)。

**若要啟用 Azure AD Azure AD DS**

1. 從[Azure 傳統入口網站](https://manage.windowsazure.com)中，按一下 [ **Active Directory** > **contosoaaddirectory**。 
3. 從上方的功能表中按一下 [**設定**]。
4. 向下**網域服務**、 捲動，並設定為以下值︰

    - **啟用適用於此 directory 網域服務**: [是]。
    - **網域服務的 DNS 網域名稱**︰ 這會顯示 Azure 目錄的預設 DNS 名稱。 例如，contoso.onmicrosoft.com。
    - **連線到此虛擬網路的網域服務**︰ 選取您先前建立，也就是**contosoaadvnet**傳統虛擬網路。
    
6. 按一下 [從頁面底部的 [**儲存**]。 您會看到**擱置...**旁**啟用適用於此 directory 網域服務**。  
7. 等到**擱置...**就會消失，並取得填入**IP 位址**。 將快速填入兩個 IP 位址。 這些是佈建網域服務的網域控制站的 IP 位址。 相對應的網域控制站且能夠準備好後，會顯示每個 IP 位址。 寫下兩個 IP 位址。 您必須進行更新版本。

如需詳細資訊，請參閱[Azure AD 網域服務 （預覽版本）-啟用 Azure AD 網域服務](../active-directory-domain-services/active-directory-ds-getting-started-enableaadds.md)。

**若要同步處理的密碼**

如果您是使用自己的網域，您需要同步處理的密碼。 請參閱[啟用密碼同步處理至 Azure AD 網域服務的雲端專用 Azure AD 目錄](../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)。


**若要設定 Azure AD LDAPS**

1. 取得 SSL 憑證所簽署的簽章的授權單位為您的網域。 自我簽署的憑證不能使用。 如果您無法取得 SSL 憑證，請連絡hdipreview@microsoft.com例外狀況。
1. 從[Azure 傳統入口網站](https://manage.windowsazure.com)中，按一下 [ **Active Directory** > **contosoaaddirectory**。 
3. 從上方的功能表中按一下 [**設定**]。
4. 捲動到**網域服務**。
5. 按一下 [**設定認證**]。
6. 依照指示指定憑證檔案和密碼]。 您會看到**擱置...**旁**啟用適用於此 directory 網域服務**。  
7. 等到**擱置...**就會消失，並取得填入**安全 LDAP 憑證**。  這可能需要 10 分鐘的時間或更多。
 
>[AZURE.NOTE] 如果 Azure AD DS 上執行某些背景的工作上, 傳憑證時，您可能會看到錯誤-<i>有是此租用戶正在執行的作業。請稍後再試</i>。  萬一發生此錯誤，請再試一段時間後。 第二個網域控制站 IP 可能需要長達 3 小時提供。

如需詳細資訊，請參閱[設定安全 LDAP (LDAPS) 為受管理的 Azure AD 網域服務的網域](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md)。

## <a name="configure-an-organizational-unit-and-reverse-dns"></a>設定組織單位並反向 DNS

在此區段中，您可以 Azure AD VNet 中，以加入虛擬機器，並在此 VM 上安裝系統管理工具，讓您設定組織單位並反向 DNS。 反向 DNS 查閱時需要 Kerberos 驗證。

**若要將虛擬網路建立虛擬機器**

1. 從[Azure 傳統入口網站](https://manage.windowsazure.com)中，按一下 [**新增** > **計算** > **虛擬機器** > **從圖庫**。
3. 選取圖像，然後再按 [**下一步**。  如果您不知道要使用哪一個，請選取 [預設值，**在 Windows Server 2012 R2 資料中心**。
4. 輸入或選取下列值︰

    - 虛擬機器名稱︰ **contosoaadadmin**
    - 層︰**基本**
    - 新的使用者名稱: （輸入使用者名稱）
    - 密碼: （輸入密碼）
    
    請注意的使用者名稱和密碼是本機管理員]。
    
5. 按一下 [**下一步**
6. 在 [**地區/虛擬網路**中，選取您在最後一個步驟 (contosoaadvnet)，建立新的虛擬網路，然後再按 [**下一步**。
7. 按一下 [**完成**]。

**若要 vm RDP**

1. 從[Azure 傳統入口網站](https://manage.windowsazure.com)中，按一下 [**虛擬機器** > **contosoaadadmin**。
3. 從上方的功能表中按一下 [**儀表板**]。
4. 按一下 [從頁面底部的**連線**]。
5. 請依照下列指示，並使用的本機管理員的使用者名稱和密碼連線。

**若要加入 VM Azure AD 網域**

1. 從 RDP 工作階段，按一下 [**開始**]，然後按一下**伺服器管理員**。
2. 按一下 [從左側功能表的 [**本機伺服器**]。
3. 從群組中，按一下 [**工作群組**]。
4. 按一下 [**變更**]。
5. 按一下 [**網域]**，輸入**contoso.onmicrosoft.com**，，然後按一下**[確定]**。
6. 輸入網域使用者認證，然後再按一下**[確定**]。
7. 按一下**[確定]**。
8. 按一下**[確定**] 以同意重新啟動電腦。
9. 按一下 [**關閉**]。
10. 按一下 [**立即重新啟動**]。

如需詳細資訊，請參閱[加入受管理的網域的 Windows Server 虛擬機器](../active-directory-domain-services/active-directory-ds-admin-guide-join-windows-vm.md)。

**若要安裝 Active Directory 系統管理工具和 DNS 工具**

1. RDP 到**contosoaadadmin** Azure AD 的使用者帳戶。
2. 按一下 [**開始**]，然後按一下 [**伺服器管理員**。
3. 按一下 [從左側功能表的 [**儀表板**]。
4. 按一下 [**管理**]，然後按一下 [**新增角色和功能**。
5. 按一下 [**下一步**]。
6. 選取**角色或功能的安裝**]，然後按一下 [**下一步**。
7. 從伺服器集區中，選取目前的虛擬機器，然後按一下 [**下一步**]。
8. 按一下 [**下一步**略過角色。
9. 展開**遠端伺服器管理工具**展開 [**角色管理工具]**、 選取**AD DS 和 AD LDS 工具**] 和 [ **DNS 伺服器工具**]，再按一下 [**下一步**。 
10. 按一下 [**下一步**
10. 按一下 [**安裝**]。

如需詳細資訊，請參閱[虛擬機器上的安裝 Active Directory 系統管理工具](../active-directory-domain-services/active-directory-ds-admin-guide-administer-domain.md#task-2---install-active-directory-administration-tools-on-the-virtual-machine)。


**若要設定反向 DNS**

1. RDP 至 contosoaadadmin Azure AD 的使用者帳戶。
2. 按一下 [**開始]**，按一下 [**系統管理工具**]，再按一下 [ **DNS**。 
3. 按一下 [略過新增 ContosoAADAdmin**無**。
4. 選取**下列電腦**、 輸入您先前設定的第一個 DNS 伺服器的 IP 位址，然後按一下**[確定]**。  您應該看到 DC/DNS 會新增至左邊的窗格。
3. 展開的 DC/DNS 伺服器**反向對應區域**，以滑鼠右鍵按一下，再按一下 [**新增] 區域**。 開啟新的區域精靈]。
4. 按一下 [**下一步**]。
5. 選取 [**主要區域**，，然後按一下 [**下一步**。
6. 選取 [**在此網域中的網域控制站上執行的所有 DNS 伺服器**，然後按一下 [**下一步**。
6. 選取**IPv4 反向對應區域**]，然後按一下 [**下一步**。
7. 在**網路識別碼**，輸入前置詞 HDInsight VNET 網路範圍，然後再按 [**下一步**。 在 [動作] 區段中，您將建立 HDInsight VNet。
8. 按一下 [**下一步**]。
9. 按一下 [**下一步**]。
10. 按一下 [**完成**]。



您建立的組織單位下一步就會建立時使用 HDInsight 叢集。 Hadoop 系統使用者和電腦帳戶會放在這個 OU。

**Azure AD 網域服務受管理的網域建立組織單位 (OU)**

1. 位於**AAD DC 管理員**群組 RDP 到**contosoaadadmin**使用網域的帳戶。
2. 按一下 [**開始]**，按一下 [**系統管理工具**]，再按一下 [ **Active Directory 管理中心**。
5. 按一下左窗格中的網域名稱。 例如，contoso。
6. 按一下 [**新增**]，請在 [**工作**窗格] 中的網域名稱，然後按一下 [**組織單位**。
7. 輸入名稱，例如**HDInsightOU**，然後再按一下**[確定]**。 


如需詳細資訊，請參閱[建立組織單位 (OU) 受管理的 Azure AD 網域服務網域上](../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md)。


## <a name="create-a-resource-manager-vnet-for-hdinsight-cluster"></a>建立資源管理員 VNet HDInsight 叢集

在此區段中，您將建立用於 HDInsight 叢集 Azure 資源管理員 VNet。 如需有關如何建立 Azure VNET 用其他方法的詳細資訊，請參閱[建立虛擬網路](../virtual-network/virtual-networks-create-vnet-arm-pportal.md)

建立 VNet 之後，您將設定使用相同的 DNS 伺服器，先 Azure AD VNet 資源管理員 VNet。 如果您在此教學課程中建立傳統 VNet 和 Azure AD 遵循的步驟，DNS 伺服器而 10.1.0.4 10.1.0.5。

**若要建立資源管理員 VNet**

1. [Azure 入口網站](https://portal.azure.com)登入。
2. 按一下 [**新增**、**網路**、]，然後**虛擬網路**]。 
3. **選取 [部署模型**，請選取**資源管理員**]，然後按一下 [**建立**。
4. 輸入或選取下列值︰

    - **名稱**︰ contosohdivnet
    - **位址空間**︰ 10.2.0.0/16。 請確定位址範圍不能與傳統 VNet 的 IP 位址範圍重疊。
    - **子網路名稱**︰ Subnet1
    - **子網路位址範圍**︰ 10.2.0.0/24
    - **訂閱**: （選取 Azure 訂閱）。
    - **資源群組**︰ contosohdirg
    - **位置**: （Azure AD VNet，亦即 contosoaadvnet 為選取的同一個位置）。

5. 按一下 [**建立**]。


**若要設定 DNS 以資源管理員 VNet**

1. 從[Azure 入口網站](https://portal.azure.com)中，按一下 [**更多服務** -> **虛擬網路**。 按一下 [ **（傳統） 的虛擬網路**，並且不確定。
2. 按一下 [ **contosohdivnet**]。
4. 按一下 [從左側的新刀**DNS 伺服器**。
6. 按一下 [**自訂**]，然後輸入下列值︰

    - 10.1.0.4
    - 10.1.0.5

    Azure AD VNet (傳統 VNet) 中的 DNS 伺服器，必須符合這些 DNS 伺服器 IP 位址。
7. 按一下 [**儲存**]。

























## <a name="peer-the-azure-ad-vnet-and-the-hdinsight-vnet"></a>對等 Azure AD VNet 和 HDInsight VNet

**對等兩個 VNet**

1. [Azure 入口網站](https://portal.azure.com)登入。
2. 按一下 [從左側功能表的 [**更多服務**]。
3. 按一下 [**虛擬網路**]。 不要按 [**虛擬網路 （傳統）**。
4. 按一下 [ **contosohdivnet**]。  這是 HDInsight VNet。
5. 按一下左側功能表的刀**Peerings** 。
6. 從上方的功能表中按一下 [**新增**]。 它會開啟 [**新增對等**的刀。
7. **新增對等**刀，設定或選擇以下的值︰

    - **名稱**︰ ContosoAADHDIVNetPeering
    - **虛擬網路部署模型**︰ 傳統
    - **訂閱**︰ 選取您使用傳統 (Azure AD) vnet 的訂閱名稱。
    - **虛擬網路**︰ contosoaadvnet。
    - **允許虛擬網路存取**: （選取）
    - **允許轉寄流量**: （選取）。 不要選取的其他兩個核取方塊。

8. 按一下**[確定]**。



## <a name="create-hdinsight-cluster"></a>建立 HDInsight 叢集


在此區段中，您可以建立 Linux 型 Hadoop 叢集 HDInsight 使用 Azure 入口網站或[Azure 資源管理員範本](../resource-group-template-deploy.md)中。 其他叢集建立方法和了解設定，請參閱[建立 HDInsight 叢集](hdinsight-hadoop-provision-linux-clusters.md)。 如需有關如何使用資源管理員範本來建立 HDInsight Hadoop 叢集的詳細資訊，請參閱[建立 Hadoop 叢集中使用的資源管理員範本的 HDInsight](hdinsight-hadoop-create-windows-clusters-arm-templates.md)


**若要建立網域 HDInsight 叢集使用 Azure 入口網站**

1. [Azure 入口網站](https://portal.azure.com)登入。
2. 按一下 [**新增**、**智慧 + 分析**，然後**HDInsight**]。
3. 從**新的 HDInsight 叢集**刀中，輸入或選取下列值︰

    - **叢集名稱**︰ 輸入網域 HDInsight 叢集的新叢集名稱。
    - **訂閱**︰ 選取用來建立這個叢集 Azure 訂閱。
    - **叢集設定**︰

        - **叢集類型**︰ Hadoop。 加入網域的 HDInsight 正在只支援的 Hadoop 叢集。
        - **作業系統**︰ Linux。  加入網域的 HDInsight 只支援 Linux 型 HDInsight 叢集上。
        - **版本**︰ Hadoop 2.7.3 (HDI 3.5)。 加入網域的 HDInsight 只支援 HDInsight 叢集版本 3.5。
        - **叢集類型**︰ 進階版

        按一下 [**選取**要儲存變更。

    - **認證**︰ 設定叢集使用者及 SSH 使用者的認證。
    - **資料來源**︰ 建立新的儲存空間帳戶，或是使用現有的儲存空間帳戶為預設儲存帳戶 HDInsight 叢集。 位置必須是兩個 VNets 相同。  位置也是 HDInsight 叢集的位置。
    - **價格**︰ 選取叢集工作者節點的數字。
    - **進階組態**︰ 

        - **網域加入與 Vnet/子網路**︰ 

            - **網域設定**︰ 

                - **網域名稱**︰ contoso.onmicrosoft.com
                - **網域的使用者名稱**︰ 輸入網域的使用者名稱。 此網域必須擁有下列權限︰ 機器加入網域，並將其放在您先前; 設定組織單位建立您稍早; 設定組織單位內的服務原則建立反向 DNS 項目。 此網域的使用者會成為此網域的 HDInsight 叢集的系統管理員。
                - **網域密碼**︰ 輸入網域使用者的密碼。
                - **組織單位**︰ 輸入您先前設定 OU tht 的辨別的名稱。 例如︰ OU = HDInsightOU，亦即 = contoso，亦即 = onmicrosoft，亦即 = com
                - **LDAPS URL**: ldaps://contoso.onmicrosoft.com:636
                - **存取使用者群組**︰ 指定您想叢集同步處理的使用者安全性群組。 例如，HiveUsers。

                按一下 [**選取**要儲存變更。

                ![加入網域的 HDInsight 入口網站的網域設定](./media/hdinsight-domain-joined-configure/hdinsight-domain-joined-portal-domain-setting.png)
            - **虛擬網路**︰ contosohdivnet
            - **子網路**︰ Subnet1

            按一下 [**選取**要儲存變更。       
        按一下 [**選取**要儲存變更。
    - **資源群組**︰ 選取 [資源群組用於 HDInsight VNet (contosohdirg)。

4. 按一下 [**建立**]。  

建立網域 HDInsight 叢集的另一個選項是使用 Azure 資源管理 」 範本。 下列程序會顯示方式︰

**若要建立範本資源管理網域的 HDInsight 叢集**

1. 按一下以開啟 [Azure 入口網站中的 [資源管理員範本的下列圖像。 資源管理員範本位於公用 blob 容器。 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-domain-joined-hdinsight-cluster.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. 從**參數**刀中，輸入下列值︰

    - **訂閱**: （選取 Azure 訂閱）。
    - **資源群組**︰ 按一下 [**使用現有**，指定您使用相同資源群組。  例如 contosohdirg。 
    - **位置**︰ 指定的資源] 群組中的位置。
    - **叢集名稱**︰ 輸入您要建立 Hadoop 叢集的名稱。 例如 contosohdicluster。
    - **叢集類型**︰ 選取叢集類型。  預設值是**hadoop**。
    - **位置**︰ 選取叢集的位置。  預設儲存帳戶使用相同的位置。
    - **叢集工作者節點計數**︰ 選取工作者節點數目。
    - **叢集登入名稱和密碼**︰ 預設的登入名稱是**管理員**。
    - **SSH 使用者名稱和密碼**︰ 預設的使用者名稱是**sshuser**。  您可以將其重新命名。 
    - **虛擬網路識別碼**︰ /subscriptions/&lt;SubscriptionID > /resourceGroups/&lt;ResourceGroupName > /providers/Microsoft.Network/virtualNetworks/&lt;VNetName >
    - **虛擬子網路**︰ /subscriptions/&lt;SubscriptionID > /resourceGroups/&lt;ResourceGroupName > /providers/Microsoft.Network/virtualNetworks/&lt;VNetName >/子網路/Subnet1
    - **網域名稱**︰ contoso.onmicrosoft.com
    - **組織單位 DN**: OU = HDInsightOU，亦即 = contoso，亦即 = onmicrosoft，亦即 = com
    - **叢集使用者群組 D Ns**: 「\"CN = HiveUsers，OU = AADDC 使用者，亦即 =<DomainName>，亦即 = onmicrosoft，亦即 = com\"」
    - **LDAPUrls**: [「 ldaps://contoso.onmicrosoft.com:636 」]
    - **DomainAdminUserName**: （輸入網域管理員的使用者名稱）
    - **DomainAdminPassword**: （輸入網域管理員使用者的密碼）
    - **我同意條款與條件上述**: （選取）
    - **釘選至儀表板**: （選取）

6. 按一下 [**購買**]。 您會看到標題為 「 」**部署範本部署**新的方塊。 建立叢集需要瞭解大約 20 分鐘。 叢集建立之後，您可以按一下叢集刀，在入口網站中，將其開啟。

完成本教學課程後，您可能會想要刪除叢集。 HDInsight，與您的資料會儲存於 Azure 儲存體，以便在不使用安全地刪除叢集。 您也會針對 HDInsight 叢集，即使沒有使用。 由於叢集費用多次多儲存空間的費用，讓經濟叢集時無法使用。 如需刪除叢集的指示，請參閱[管理 Hadoop 叢集 HDInsight 使用 Azure 入口網站中](hdinsight-administer-use-management-portal.md#delete-clusters)。





## <a name="next-steps"></a>後續步驟

- 設定群組原則和執行登錄區查詢，請參閱[設定登錄區原則的網域的 HDInsight 叢集](hdinsight-domain-joined-run-hive.md)。
- 執行網域 HDInsight 叢集上使用 SSH 登錄區查詢，請參閱[使用 SSH Linux 為基礎的 Hadoop HDInsight Linux、 Unix，或 OS X 上使用](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-domain-joined-hdinsight-cluster)。
