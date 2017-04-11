<properties
    pageTitle="Azure 儲存體安全性指南 |Microsoft Azure"
    description="保護 Azure 儲存體，包括但不是限於 RBAC、 儲存服務加密、 用戶端加密、 中小企業 3.0 和 Azure 磁碟加密的許多方法的詳細資料。"
    services="storage"
    documentationCenter=".net"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/08/2016"
    ms.author="robinsh"/>

#<a name="azure-storage-security-guide"></a>Azure 儲存空間的安全性指南

##<a name="overview"></a>概觀

Azure 儲存體提供完整的安全性功能的共同啟用安全的應用程式。 可以使用角色型存取控制和 Azure Active Directory 受到保護的儲存空間帳戶本身。 資料可以使用[用戶端加密](storage-client-side-encryption.md)、 HTTPS 或中小企業 3.0 受到保護的應用程式和 Azure 之間傳送之用。 資料可以設定自動加密寫入 Azure 儲存體時使用[儲存空間服務加密 (SSE)](storage-service-encryption.md)。 虛擬機器所使用的作業系統與資料磁碟可以設定為使用[Azure 磁碟加密](../security/azure-security-disk-encryption.md)加密。 Azure 儲存體中的資料物件的委派存取權授與使用[共用的 Access 簽章](storage-dotnet-shared-access-signature-part-1.md)。

本文將提供的每個安全性功能可以搭配 Azure 儲存空間的概觀。 連結所提供的文件，讓每項功能的詳細資料，您可以輕鬆地執行進一步調查每個主題。

以下是涵蓋本文中的主題︰

-   [管理平面安全性](#management-plane-security)︰ 保護您儲存的帳戶

    管理平面包含用來管理您儲存的帳戶的資源。 在此區段中，我們將討論 Azure 資源管理員部署模型，以及如何使用角色型存取控制 (RBAC) 來控制存取您儲存的帳戶。 我們也會與管理您儲存帳戶金鑰，以及如何重新產生它們。

-   [資料平面安全性](#data-plane-security)︰ 保護資料的存取

    在此區段中，我們會查看允許存取實際資料物件，在您儲存的帳戶，例如二進位大型物件、 檔案、 佇列和表格中使用共用的 Access 簽章和儲存存取原則。 我們會說明服務層級 SA 和帳戶層級 SA。 我們也會看到如何限制存取特定的 IP 位址 （或 IP 位址範圍），限制為 HTTPS，使用的通訊協定以及撤銷共用 Access 簽章，而不需等待為過期。

-   [在傳輸時會加密](#encryption-in-transit)

    本節將討論如何保護資料，當您傳送進或移出 Azure 儲存體。 我們將討論建議使用 HTTPS 及中小企業 3.0 用於 Azure 檔案共用加密。 我們也會在用戶端加密，可讓您之前在用戶端應用程式中，將儲存空間傳送加密的資料，並解密資料傳輸不儲存後的外觀。

-   [在其餘的加密](#encryption-at-rest)

    我們會討論儲存服務加密 (SSE)，及如何啟用它儲存帳戶，導致您的封鎖 blob，頁面 blob 和附加 blob 寫入 Azure 儲存體時自動加密。 我們也會看看如何使用 Azure 磁碟加密，並瀏覽基本差異和與 SSE 與用戶端加密的磁碟加密的情況。 我們會簡短地看 FIPS 規範的美國政府電腦。

-   使用[儲存空間分析](#storage-analytics)稽核存取 Azure 儲存體

    本節將討論如何尋找要求儲存分析資料記錄中的資訊。 我們將會看看實際的儲存空間分析記錄資料，請參閱如何分辨是否要求與儲存帳戶金鑰]，使用共用的 Access 簽章，或以匿名方式，以及是否成功或失敗。

-   [啟用瀏覽器型的用戶端使用 CORS](#Cross-Origin-Resource-Sharing-CORS)

    本節說明如何允許十字形，資源共用 (CORS)。 我們將討論跨網域存取權，以及如何處理 Azure 儲存體內建 CORS 功能。

##<a name="management-plane-security"></a>管理平面安全性

管理平面組成會影響本身的儲存空間帳戶的作業。 例如，您建立或刪除儲存帳戶、 取得訂閱的儲存空間的帳戶清單、 擷取儲存的帳戶金鑰，或重新產生儲存帳戶金鑰。

當您建立新的儲存空間帳戶時，您可以選取 [傳統] 或 [資源管理員部署的模型。 Azure 中建立資源的傳統模式只允許訂閱，然後在 [開啟，儲存帳戶中的全存取。

本指南著重於資源管理員模型也就是建立儲存帳戶的建議的方法。 資源管理員儲存帳戶，而非整個訂閱提供存取，您可以控制使用角色型存取控制 (RBAC) 管理平面有限的更多層級的存取權。

###<a name="how-to-secure-your-storage-account-with-role-based-access-control-rbac"></a>如何保護您儲存的帳戶使用角色型存取控制 (RBAC)

淺談 RBAC 是什麼，您如何使用它。 每個 Azure 的訂閱有 Azure Active Directory。 使用者、 群組和從該目錄的應用程式可以授與存取權管理 Azure 訂閱中使用的資源管理員部署模型的資源。 這被指角色型存取控制 (RBAC)。 若要管理存取權，您可以使用[Azure 入口網站](https://portal.azure.com/)、 [Azure CLI 工具](../xplat-cli-install.md)、 [PowerShell](../powershell-install-configure.md)或[Azure 儲存體資源提供者 REST Api](https://msdn.microsoft.com/library/azure/mt163683.aspx)。

資源管理員模型中，您將儲存帳戶資源群組，並控制 access 中的 Azure Active Directory 的特定儲存帳戶管理平面。 例如，您可以授權讓特定使用者能夠存取儲存的帳戶金鑰，而其他使用者可以檢視儲存帳戶的相關資訊，但無法存取儲存帳戶金鑰。

####<a name="granting-access"></a>授與存取權

將適當的 RBAC 角色指派給使用者、 群組和應用程式]，在正確的範圍內授與存取。 若要授與存取權的整個訂閱，您可以指派角色訂閱層級。 您可以授與資源群組本身的權限授與存取權的所有資源的 [資源] 群組中。 您也可以指定特定角色特定的資源，例如儲存的帳戶。

以下是重點必須知道如何使用 RBAC 存取 Azure 儲存體帳戶的管理作業︰

-   當您指派存取時，您基本上角色指派給您想要有權存取的帳戶。 您可以控制存取管理該儲存帳戶所用的作業，但無法帳戶中的資料物件。 例如，您可以授與權限來擷取帳戶內容的儲存空間 （例如重複），但無法將容器或內 Blob 儲存體容器內的資料。

-   人有權存取資料中的物件儲存的帳戶，您就可以授權讓他們讀取儲存的帳戶金鑰，權限，該使用者可以然後使用這些按鍵存取二進位大型物件、 佇列、 表格及檔案。

-   特定的使用者帳戶，群組的使用者，或是特定應用程式，可以指派角色。

-   每個的角色具有動作和不動作的清單。 虛擬機器參與者角色，例如有 「 listKeys 」 的動作可讓讀取儲存帳戶金鑰。 參與者含有 「 不動作 」，例如更新 Active Directory 中的使用者存取。

-   儲存空間的角色包括 （但不限於） 下列動作︰

    -   擁有者 – 他們可以管理所有項目，包括存取。

    -   參與者 – 他們可以執行任何項目擁有者可以執行以外的指派存取權。 此角色與其他人可以檢視，並重新產生儲存帳戶金鑰。 使用儲存的帳戶金鑰，他們可以存取資料物件。

    -   閱讀程式 –，才能檢視儲存的帳戶，除了密碼的相關資訊。 比方說，如果您儲存帳戶的讀取權限的角色指派給其他人時，他們可以檢視帳戶內容的儲存空間，但是他們不能對內容進行任何變更，或檢視儲存的帳戶金鑰。

    -   儲存帳戶參與者 – 他們可以管理儲存空間帳戶 – 他們可以讀取訂閱的資源群組和資源，並建立及管理訂閱資源群組部署。 他們也可以存取儲存帳戶金鑰，表示在他們可以存取資料平面。

    -   使用者存取系統管理員 – 他們可以管理使用者存取儲存的帳戶。 例如，他們可以特定使用者授與閱讀程式存取。

    -   虛擬機器參與者 – 他們可以管理虛擬機器，但無法連線到儲存的帳戶。 此角色可以] 清單中儲存的帳戶金鑰，這表示您要此角色指派對象的使用者可以更新的資料平面。

        若要讓使用者建立虛擬機器，他們必須能夠建立對應 VHD 檔案儲存空間帳戶。 若要執行這項作業，他們必須能夠擷取儲存帳戶金鑰，並將它建立 VM API。 因此，他們必須具有此權限，讓他們可以清單儲存帳戶金鑰。

- 定義自訂的角色的功能可讓您撰寫的一組可用的動作可 Azure 資源執行的動作從清單的功能。

- 使用者必須之前先設定您的 Azure Active Directory 中您可以為其指派角色。

- 您可以建立誰授與/撤銷哪一類的存取與人員，以及使用 PowerShell 或 Azure CLI 哪些範圍的報表。

####<a name="resources"></a>資源

-   [Azure Active Directory 角色型存取控制](../active-directory/role-based-access-control-configure.md)

    本文說明 Azure Active Directory 角色型存取控制和運作方式。

-   [RBAC︰ 內建的角色](../active-directory/role-based-access-built-in-roles.md)

    本文詳細說明所有 RBAC 提供的內建角色。

-   [了解資源管理員部署及傳統的部署](../resource-manager-deployment-model.md)

    本文說明的資源管理員部署及傳統部署模型，並說明使用資源管理員] 及 [資源群組的好處

-   [Azure 計算、 網路和儲存提供者下 Azure 資源管理員](../virtual-machines/virtual-machines-windows-compare-deployment-models.md)

    本文說明在資源管理員模型運作 Azure 計算、 網路和儲存提供者的方式。

-   [管理角色型存取控制以 REST API](../active-directory/role-based-access-control-manage-access-rest.md)

    本文說明如何使用 REST API 來管理 RBAC。

-   [Azure 儲存體資源提供者 REST API 參考](https://msdn.microsoft.com/library/azure/mt163683.aspx)

    這是您可以使用來管理您儲存的帳戶以程式設計方式的 Api 的參照。

-   [開發人員指南和 Azure 資源管理員 API 的授權](http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/)

    本文說明如何使用資源管理員 Api 進行驗證。

-   [Microsoft Azure 從 Ignite 角色型存取控制](https://channel9.msdn.com/events/Ignite/2015/BRK2707)

    這是連結至頻道 9 上的視訊從 2015 MS Ignite 會議。 在這個工作階段，他們談論存取 [管理和 Azure 中的報告權限及探索安全存取使用 Azure Active Directory Azure 訂閱的最佳做法。

###<a name="managing-your-storage-account-keys"></a>管理儲存空間帳戶索引鍵

儲存帳戶金鑰是的以及儲存體帳戶名稱，可用來存取儲存在儲存帳戶中的資料物件，例如 blob 的表格、 佇列中的郵件和 Azure 檔案共用上的檔案中的項目 Azure 所建立的 512 位元字串。 控制儲存帳戶金鑰控制項的存取權的資料平面該儲存帳戶的存取。

每個儲存帳戶有稱為 「 金鑰 1 」 和 「 按鍵 2 」 PowerShell cmdlet 和[Azure 入口網站](http://portal.azure.com/)中的兩個機碼。 這些可以重新產生手動使用其中一個有數種方法，包括，但不是限於使用[Azure 入口網站](https://portal.azure.com/)、 PowerShell 的 Azure CLI，或使用.NET 儲存用戶端的文件庫或 Azure 儲存服務 REST API，以程式設計方式。

有任何數字的原因重新產生您儲存帳戶金鑰。

-   您可能會產生定期基於安全性理由。

-   如果某人管理入侵應用程式，並可擷取的索引鍵的硬式編碼或儲存在設定檔中，讓他們的完整存取您儲存的帳戶，您想要重新產生您儲存帳戶金鑰。

-   重新產生金鑰的另一種情況就是您的小組使用的儲存空間總管應用程式，保留儲存帳戶金鑰]，並且小組成員的其中一個離開。 應用程式會繼續運作，這些授予存取權限您儲存的帳戶後看不見了。 這是實際的主要原因建立帳戶層級共用 Access 簽章 – 您可以使用帳戶層級 SA，而不是儲存在設定檔中的便捷鍵。

####<a name="key-regeneration-plan"></a>重新產生金鑰計劃

您不想要只要重新產生的金鑰您使用的不規劃。 如果您執行這項作業時，您可以剪下關閉所有的 access 的儲存空間帳戶，可能會導致主要中斷。 這是為什麼有兩個機碼。 您應該產生一個索引鍵一次。

您重新產生金鑰之前，先確定您具備的所有儲存的帳戶，而定的應用程式，以及您使用的 Azure 中的任何其他服務清單項目。 例如，如果您使用的 Azure 媒體服務，取決於您儲存的帳戶，您必須重新同步處理便捷鍵媒體服務後重新產生金鑰。 如果您使用的任何應用程式，例如儲存檔案總管，您必須提供這些應用程式至新的金鑰。 請注意，是否您有的 Vm 其 VHD 檔案會儲存在儲存帳戶時，他們會不會受到重新產生儲存帳戶金鑰。

您可以重新產生金鑰 Azure 入口網站中。 重新產生金鑰後才會 10 分鐘的時間，跨儲存服務無法同步處理。

當您準備好時，以下是詳述您應該如何變更您的金鑰的一般程序。 在此情況下，假設是您目前正在使用金鑰 1，並在您要變更改為使用金鑰 2 的所有項目。

1.  重新產生金鑰 2，以確保安全。 Azure 入口網站中，您可以執行這個動作。

2.  在所有儲存的儲存空間索引鍵的應用程式，變更以使用金鑰 2 的新值的儲存空間鍵。 測試並發佈應用程式]。

3.  所有的應用程式與服務設定，而且順利執行，重新產生金鑰 1。 如此一來，可確保任何人對象您未明文給新的金鑰將不再有儲存帳戶的存取權。

如果您目前正在使用鍵 2，您可以使用相同的程序，但反向索引鍵的名稱。

變更每個應用程式，以使用新的金鑰，發佈，您可以透過幾天，進行移轉。 全部完成後，您應該然後返回並重新產生舊金鑰，所以無法再運作。

另一個選項是在為私人[Azure 金鑰保存庫](https://azure.microsoft.com/services/key-vault/)保留儲存帳戶金鑰]，並從該處擷取金鑰應用程式。 然後重新產生金鑰更新 Azure 金鑰保存庫時，應用程式不會需要因為他們挑選新的金鑰從 Azure 金鑰保存庫自動重新部署。 請注意，您可以有讀取機碼，您需要每次應用程式或您可以快取它在記憶體中及失敗時，使用時，擷取索引鍵一次從 Azure 金鑰保存庫。

使用 Azure 金鑰保存庫時，也會將另一個層級的儲存空間索引鍵的安全性。 如果您使用這個方法，您會永遠不必儲存金鑰硬式編碼，在設定檔中，以移除的人沒有特定權限按鍵存取途徑。

使用 Azure 金鑰保存庫的其他優點是您也可以控制存取權使用 Azure Active Directory 索引鍵。 這表示您可以授與存取權的少數幾個需要從 Azure 金鑰保存庫，擷取索引鍵，並知道其他應用程式不會存取按鍵，而不授與這些權限特別的應用程式。

附註︰ 建議同時在所有應用程式中使用其中一個鍵。 如果您使用金鑰 1，在某些與索引鍵 2 的其他人，您無法旋轉索引鍵不會失去存取某些應用程式。

####<a name="resources"></a>資源

-   [關於 Azure 儲存體帳戶](storage-create-storage-account.md#regenerate-storage-access-keys)

    本文提供儲存帳戶的概觀，並將告訴您檢視、 複製及重新產生儲存便捷鍵。

-   [Azure 儲存體資源提供者 REST API 參考](https://msdn.microsoft.com/library/mt163683.aspx)

    本文包含有關擷取儲存帳戶金鑰，以及使用 REST API Azure 帳戶重新儲存帳戶金鑰特定的文件的連結。 注意︰ 這是資源管理員儲存帳戶。

-   [儲存帳戶的作業](https://msdn.microsoft.com/library/ee460790.aspx)

    儲存服務管理員 REST API 參照本文章包含連結至特定的文件上擷取及重新產生使用 REST API 儲存帳戶金鑰。 注意︰ 這是傳統儲存帳戶。

-   [從此按鍵管理 – 管理存取 Azure 儲存體資料使用 Azure AD 您不再需要](http://www.dushyantgill.com/blog/2015/04/26/say-goodbye-to-key-management-manage-access-to-azure-storage-data-using-azure-ad/)

    本文說明如何使用 Active Directory 來控制您 Azure 儲存體中鍵 Azure 金鑰保存庫的存取權。 也會顯示如何重新產生金鑰地使用 Azure 自動化工作。

##<a name="data-plane-security"></a>資料平面安全性

資料平面安全性指的是用來保護 Azure 儲存體 – 二進位大型物件、 佇列、 表格及檔案中儲存的資料物件的方法。 我們看到期間的資料，傳輸時會加密資料與安全性的方法，但您該如何讓物件的存取權嗎？

基本上有兩種方法來控制存取權的資料物件本身。 第一個來控制存取權的儲存空間帳戶機碼中，而第二個使用的共用的 Access 簽章來將特定資料物件的存取權授與特定的一段時間。

請注意的例外是，您可以藉由設定的存取層級會相應地保存 blob 容器至您的二進位大型物件允許公用存取。 如果您可以設定存取 Blob 或容器的容器，它會允許公用 blob 的容器中的讀取權限。 這表示 url，指向 [blob 的容器中的任何人都可以在瀏覽器中開啟它，而不使用共用的 Access 簽章或有儲存帳戶金鑰。

###<a name="storage-account-keys"></a>儲存帳戶金鑰

儲存帳戶金鑰是的以及儲存體帳戶名稱，可以用來存取儲存在儲存帳戶中的資料物件 Azure 所建立的 512 位元字串。

例如，您可以讀取二進位大型物件、 寫入佇列、 建立資料表，及修改檔案。 許多這些動作可透過 Azure 入口網站，來執行或使用其中一個許多儲存空間總管應用程式。 您也可以撰寫程式碼，使用 REST API 或其中一個儲存用戶端文件庫來執行這些作業。

[管理平面安全性](#management-plane-security)討論] 區段中，為傳統儲存帳戶的儲存空間索引鍵可以會授與存取權授予完整存取權的 Azure 訂閱。 透過角色型存取控制 (RBAC)，就可以控制存取儲存帳戶使用 Azure 資源管理員模型的儲存空間金鑰。

###<a name="how-to-delegate-access-to-objects-in-your-account-using-shared-access-signatures-and-stored-access-policies"></a>如何代理人存取權使用共用的 Access 簽章和儲存存取原則您帳戶中的物件

共用 Access 簽章是存取的字串，其中包含可附加至 URI，可讓您代理人存取權儲存物件，然後指定日期/時間範圍權限等的限制式安全性 token。

您可以授與存取權二進位大型物件、 容器、 佇列訊息、 檔案和表格。 使用表格，您可以實際授與權限可指定您要使用者有權存取的磁碟分割和列索引鍵範圍來存取範圍的表格中的項目。 例如，如果您有儲存地理省/市的資料分割索引鍵的資料，您可能會提供給該人員加州的存取權的資料。

在另一個範例中，您可能會讓 web 應用程式的 SA 權杖，讓它可以寫入佇列中，項目，並且讓工作者角色應用程式的 SA 權杖，若要從佇列中取得郵件並進行處理。 或者您可能會提供一位客戶的 SA 權杖，他們可以使用上傳至容器中 Blob 儲存的圖片，並賦予讀取這些圖片 web 應用程式權限。 在這兩種情況下，有分離問題，因此可給予每個應用程式只需要才能執行工作的存取權。 這是因為使用共用的 Access 簽章。

####<a name="why-you-want-to-use-shared-access-signatures"></a>為什麼要使用共用的 Access 簽章

您要使用 SA，而非僅提供您儲存帳戶金鑰]，因此更容易在為何？ 提供您儲存的帳戶金鑰，就像共用您的儲存空間英國的索引鍵。 授與完整存取權限。 其他人可以使用索引鍵，並將其音樂整份文件庫上傳您儲存的帳戶。 他們也無法以病毒感染的版本，取代您的檔案，或竊取您的資料。 提供無限制地的存取您儲存的帳戶是應該輕輕經過的項目。

使用共用的 Access 簽章，您可以授與用戶端只有限的時間所需的權限。 例如，如果有人已將 blob 上傳至您的帳戶，您可以授與他們寫入存取權，只是足夠的時間上, 傳 blob （當然，取決於 blob 的大小）。 然後，如果您改變主意，您可以撤銷存取權。

此外，您可以指定使用 SA 要求會受限於特定 IP 位址或外部 Azure 的 IP 位址範圍。 您也可以要求的要求使用特定的通訊協定 （HTTPS 或 HTTP/HTTPS）。 這表示若您只想要允許 HTTPS 流量，您可以設定為 HTTPS 的必要的通訊協定僅會封鎖 HTTP 流量。

####<a name="definition-of-a-shared-access-signature"></a>共用的 Access 簽章的定義

共用 Access 簽章是指向資源在 url 查詢參數的一組

提供可存取及允許該存取的時間長度的相關資訊。 以下是範例。此 URI 提供五分鐘 blob 的 「 讀取 」 權限。 注意 SA 查詢參數必須 URL 編碼，例如 %3a 冒號 （:） 或 20%的空格。

    http://mystorage.blob.core.windows.net/mycontainer/myblob.txt (URL to the blob)
    ?sv=2015-04-05 (storage service version)
    &st=2015-12-10T22%3A18%3A26Z (start time, in UTC time and URL encoded)
    &se=2015-12-10T22%3A23%3A26Z (end time, in UTC time and URL encoded)
    &sr=b (resource is a blob)
    &sp=r (read access)
    &sip=168.1.5.60-168.1.5.70 (requests can only come from this range of IP addresses)
    &spr=https (only allow HTTPS requests)
    &sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D (signature used for the authentication of the SAS)

####<a name="how-the-shared-access-signature-is-authenticated-by-the-azure-storage-service"></a>共用 Access 簽章驗證 Azure 儲存服務的方式

儲存服務收到要求時，它會採用輸入的查詢參數，並建立簽章呼叫程式為使用相同的方法。 然後，它會比較兩個簽章。 如果他們同意、 儲存服務可以核取要確定它是有效、 確認目前的日期和時間是指定的視窗中，請務必要求存取對應到所做的要求等的儲存空間服務版本。

例如，我們上述的 url，如果 URL 指向檔案，而不是 blob，此要求會失敗，因為它會指定共用 Access 簽章是 blob 的。 如果更新 blob 呼叫 [其他] 命令，它會失敗共用 Access 簽章指定允許之僅限讀取權限。

####<a name="types-of-shared-access-signatures"></a>共用的 Access 簽章的類型

-   服務等級 SA 可用來存取儲存帳戶中有特定的資源。 部分的範例會擷取 blob 容器，下載 blob、 更新表格中的實體、 加佇列中的郵件或上傳檔案至檔案共用中的清單。

-   帳戶層級 SA 可用來存取任何服務層級 SA 可用於的項目。 此外，它可以授權讓不允許與服務等級 SA，例如建立容器、 表格、 佇列和檔案共用能力資源的選項。 您也可以一次指定多個服務的存取。 例如，您可能會授與其他人存取您儲存的帳戶中的檔案和二進位大型物件。

####<a name="creating-an-sas-uri"></a>建立 SA URI

1.  您可以建立臨機操作 URI，視情況下，定義所有查詢參數的每次。

    這是真正的彈性，但如果您有一組邏輯類似每次的參數，使用儲存存取原則是個不錯。

2.  您可以建立儲存 Access 原則的整個容器、 檔案共用、 表格或佇列。 然後您可以使用此為基礎建立 SA uri。 根據儲存存取原則的權限可以輕鬆地撤銷。 您可以讓最多 5 在每個容器、 佇列、 表格或檔案共用上定義的原則。

    例如，如果您已有許多閱讀 blob 容器中將特定的人，您可以建立儲存存取原則指出 「 提供的讀取權限 」，也是一樣的每次的任何其他設定。 然後，您可以建立 SA URI 使用的儲存存取原則設定，並指定到期日的日期/時間。 利用這是您沒有指定的所有查詢參數，每次。

####<a name="revocation"></a>撤銷

假設您 SA 已洩露，或您想要變更其公司安全性或法規遵循需求。 您要如何撤銷使用該 SA 資源的存取權？ 建立 SA URI 的方式而定。

如果您使用臨機操作 URI，您會有三個選項。 您可以議題 SA 權杖具有簡短到期原則，並直接等待到期 SA。 您可以重新命名或刪除資源 （假設權杖範圍至單一物件）。 您可以變更儲存帳戶金鑰。 最後一個選項可能會有顯示較大的影響，取決於數服務會使用該儲存帳戶，可能不是您要在不規劃。

如果您使用的衍生自儲存存取原則 SA，您可以移除存取撤銷儲存存取原則，而只可以變更它，因此已過期，或您可以完全移除。 這會立即生效，使每個 SA 建立使用該儲存存取原則。 更新或移除儲存存取原則可能會影響人員存取該特定容器檔案共用] 資料表，或透過 SA，但其要求新的 SA 時的舊的項目就會變成無效，因此如果撰寫用戶端的佇列中即可運作。

由於使用 SA，衍生自儲存存取原則可讓您立即撤銷該 SA，因此建議的最佳方式使用時可能出現儲存存取原則。

####<a name="resources"></a>資源

如需詳細資訊，使用共用的 Access 簽章和儲存存取原則，完成的範例，請參閱下列文章︰

-   這些是參考文章。

    -   [服務 SA](https://msdn.microsoft.com/library/dn140256.aspx)

        本文提供的服務層級 SA 使用二進位大型物件、 佇列中訊息、 表格的範圍，以及檔案的範例。

    -   [建構 SA 服務](https://msdn.microsoft.com/library/dn140255.aspx)

    -   [建構 SA 帳戶](https://msdn.microsoft.com/library/mt584140.aspx)

-   這些是使用.NET 用戶端文件庫建立共用的 Access 簽章和儲存存取原則的教學課程。

    -   [使用共用的 Access 簽章 (SA)](storage-dotnet-shared-access-signature-part-1.md)

    -   [共用 Access 簽章，第 2 部分︰ 建立及使用 SA Blob 服務](storage-dotnet-shared-access-signature-part-2.md)

        本文包含以下 SA 模型的共用的 Access 簽章，範例說明及最佳作法的建議使用的 SA。 也討論已撤銷的授與的權限。

-   限制存取 IP 位址 (IP Acl)

    -   [什麼是端點存取控制清單 (Acl)？](../virtual-network/virtual-networks-acl.md)

    -   [建構 SA 服務](https://msdn.microsoft.com/library/azure/dn140255.aspx)

        服務等級 sa; 參考本文其包含 IP ACLing 的範例。

    -   [建構 SA 帳戶](https://msdn.microsoft.com/library/azure/mt584140.aspx)

        這是帳戶層級 SA; 參考本文其包含 IP ACLing 的範例。

-   驗證

    -    [驗證 Azure 儲存服務](https://msdn.microsoft.com/library/azure/dd179428.aspx)

-   共用 Access 快速入門教學課程的簽章

    -   [SA 開始教學課程](https://github.com/Azure-Samples/storage-dotnet-sas-getting-started)

##<a name="encryption-in-transit"></a>在傳輸時會加密

###<a name="transport-level-encryption--using-https"></a>傳輸層級加密 – 使用 HTTPS

您應以確保 Azure 儲存體資料的安全性的另一個步驟是將加密的用戶端和 Azure 儲存體之間的資料。 第一個建議使用[HTTPS](https://en.wikipedia.org/wiki/HTTPS)通訊協定，公用網際網路上可安全通訊。

呼叫 REST Api 或存取儲存區中的物件時，應該一律使用 HTTPS。 此外，**共用 Access 簽章**，可以用來代理人存取權 Azure 儲存體物件，包括選項，指定使用共用 Access 簽章，確保任何人送出含 SA 權杖的連結會使用適當的通訊協定時，可以使用 HTTPS 通訊協定。

####<a name="resources"></a>資源

-   [啟用 HTTPS Azure 應用程式服務中的應用程式](../app-service-web/web-sites-configure-ssl-certificate.md)

    本文將示範如何啟用 HTTPS Azure Web 應用程式。

###<a name="using-encryption-during-transit-with-azure-file-shares"></a>在傳輸時會加密︰ 使用 Azure 檔案共用

Azure 檔案儲存空間支援 HTTPS 時使用 REST API，但是其他常用的 SMB 檔案共用區連接至 VM。 SMB 2.1 不支援加密，因此 Azure 中相同的區域內只允許的連線。 不過，中小企業 3.0 便支援加密，以及可以搭配 Windows Server 2012 R2，Windows 8、 Windows 8.1 和 Windows 10，允許跨地區存取及偶數在桌面上的存取權。

請注意，而 unix 使用 Azure 檔案共用，Linux SMB 用戶端還不支援加密，只允許存取 Azure 的區域內。 加密支援 Linux 位於 Linux 開發人員負責中小企業功能的藍圖。 對方將新增加密，您必須以存取 linux Azure 檔案共用，當您執行 Windows 版的相同功能。

####<a name="resources"></a>資源

-   [如何使用 Linux Azure 檔案儲存空間](storage-how-to-use-files-linux.md)

    本文將示範如何在 Linux 系統裝載 Azure 檔案共用及上傳下載檔案。

-   [開始使用 windows Azure 檔案儲存空間](storage-dotnet-how-to-use-files.md)

    本文將概述 Azure 檔案共用及如何裝載及使用方法使用 PowerShell 和.NET。

-   [內 Azure 檔案儲存空間](https://azure.microsoft.com/blog/inside-azure-file-storage/)

    本文宣告開放 Azure 檔案儲存空間，並提供有關中小企業 3.0 加密技術詳細資料。

###<a name="using-client-side-encryption-to-secure-data-that-you-send-to-storage"></a>使用用戶端加密保護您在傳送給儲存的資料

可協助您確保您的資料安全時的用戶端應用程式和儲存之間傳送的另一個選項是用戶端加密。 資料已加密之前傳輸到 Azure 儲存體。 從 Azure 儲存體中擷取資料，當資料解密後收到在用戶端。 即使資料已加密通過網路，建議您，您也可以使用 HTTPS，當有資料完整性檢查內建於說明降低網路錯誤資料的完整性。

用戶端加密也是加密的其餘部分中，您資料的方法如下的資料儲存在其加密的表單。 我們將討論此更多詳細資料] 區段中[，其餘](#encryption-at-rest)的加密。

##<a name="encryption-at-rest"></a>在其餘的加密

有三個 Azure 功能，可提供在其餘的加密。 Azure 磁碟加密用來加密的作業系統與資料的磁碟 IaaS 虛擬機器中的商務連絡人。 其他兩個 – 用戶端加密和 SSE – 會同時用於加密 Azure 儲存體中的資料。 現在就讓我們看看這些項目，然後進行比較，並查看每個時可以使用。

雖然您可以使用用戶端加密，來加密資料在傳輸時會 （這也存放加密的形式儲存區中），您可能會想要直接使用 HTTPS 期間傳送及儲存時自動加密資料的方法。 有兩種方式若要這麼做，Azure 磁碟加密和 SSE。 其中一個用來直接加密 Vm 所使用的作業系統與資料磁碟上的資料及其他用來加密資料寫入 Azure Blob 儲存體。

###<a name="storage-service-encryption-sse"></a>儲存服務加密 (SSE)

SSE 可讓您要求寫入 Azure 儲存體時，儲存服務自動加密資料。 當您從 Azure 儲存體讀取資料時，將會解密儲存服務，再傳回。 這可讓您保護您的資料，而不需要修改程式碼，或將程式碼新增至任何應用程式。

這是適用於整個儲存帳戶的設定。 您可以啟用及停用此功能，藉由變更的設定值。 若要這麼做，您可以使用 [Azure 入口網站、 PowerShell、 Azure CLI、 儲存資源提供者 REST API 或.NET 儲存用戶端的文件庫。 根據預設，SSE 已關閉。

此時，所用的加密金鑰是由 Microsoft 管理。 我們原本，產生的金鑰，並管理所定義的內部的 Microsoft 原則的安全儲存空間的按鍵，以及將一般的旋轉。 在未來，您會取得能夠管理加密金鑰，並提供客戶管理鍵從 Microsoft 管理索引鍵的移轉路徑。

使用資源管理員部署模型建立的標準和進階版儲存帳戶使用這項功能。 SSE 僅適用於封鎖 blob，頁面 blob 與附加二進位大型物件。 其他類型的資料，包括資料表、 佇列和檔案，不會被加密。

啟用 SSE 且資料寫入 Blob 儲存體只加密資料。 啟用或停用 SSE 不會影響現有的資料。 也就是說，當您啟用此加密，它會不返回並加密已經存在於; 資料也不會解密已存在，當您停用 SSE 的資料。

如果您想要使用此功能以傳統儲存帳戶，您可以建立的新的資源管理員儲存帳戶，並使用 AzCopy 將資料複製到新帳戶。 

###<a name="client-side-encryption"></a>用戶端加密

我們討論的資料在傳輸時會加密時所述用戶端加密。 此功能可讓您以程式設計方式加密用戶端應用程式，然後再傳送以在網路上才能寫入 Azure 儲存空間，並以程式設計方式後擷取 Azure 儲存體解密資料中的資料。

這會提供加密傳輸，但也提供加密其餘的功能。 請注意，雖然資料在傳輸時會加密，我們還是建議使用 HTTPS 善用協助降低網路錯誤資料完整性的內建資料完整性檢查。

位置，您可能會使用這個範例就是您有 web 應用程式儲存二進位大型物件和擷取二進位大型物件，並且您想要盡可能的應用程式和資料。 在此情況下，您會使用用戶端加密。 用戶端和 Azure Blob 服務之間的流量包含加密的資源，並沒有人可以解譯資料在傳輸時會重新組成，將您私人的二進位大型物件。

用戶端加密內建 Java 和.NET 儲存用戶端文件庫使用 Azure 金鑰保存庫 Api，讓您實作很簡單。 加密並解密資料的程序使用信封技巧，並將儲存使用中儲存的每個物件的加密的中繼資料。 例如的二進位大型物件，它將它儲存 blob 中繼資料中時的佇列，它將它加入至每個佇列中的訊息。

加密本身，您可以產生與管理加密金鑰。 您也可以使用 Azure 儲存用戶端程式庫所產生的金鑰，也可以使用會產生的金鑰 Azure 金鑰保存庫。 您可以將加密金鑰儲存在您內部部署重要的儲存空間，或您可以將它們儲存在 Azure 金鑰保存庫。 Azure 鍵保存庫可讓您存取 Azure 金鑰保存庫中的機密授與特定的使用者使用 Azure Active Directory。 這代表不只是都可以讀取 Azure 金鑰保存庫，以及擷取您使用的用戶端加密金鑰。

####<a name="resources"></a>資源

-   [加密並解密二進位大型物件中使用 Azure 金鑰保存庫的 Microsoft Azure 儲存體](storage-encrypt-decrypt-blobs-key-vault.md)

    本文說明如何使用 Azure 金鑰保存庫，包括如何建立 KEK，並將其儲存在使用 PowerShell 保存庫中的用戶端加密。

-   [Microsoft Azure 儲存空間的用戶端加密和 Azure 金鑰保存庫](storage-client-side-encryption.md)

    本文提供用戶端加密的說明，並提供若要加密並解密資源的四個的儲存空間服務使用的儲存空間用戶端文件庫的範例。 並提到相關 Azure 金鑰保存庫。

###<a name="using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines"></a>若要將加密使用您的虛擬機器的磁碟使用 Azure 磁碟加密

Azure 磁碟加密是目前預覽中的新功能。 此功能可讓您加密 OS 磁碟和資料使用的 IaaS 虛擬機器。 Windows 的磁碟機使用業界標準 BitLocker 加密技術加密。 Linux 的磁碟使用 DM 窖技術加密。 此整合 Azure 金鑰保存庫，可讓您控制與管理磁碟加密金鑰。

Azure 磁碟加密解決方案支援下列三種客戶加密案例︰

-   啟用從客戶加密 VHD 檔案與客戶提供加密金鑰，儲存於 Azure 金鑰保存庫中建立新的 IaaS Vm 加密。

-   啟用從 Azure Marketplace 建立的新 IaaS Vm 加密。

-   啟用執行 Azure 中的現有 IaaS Vm 加密。

>[AZURE.NOTE] Azure 中執行的 Linux Vm 或從 Azure Marketplace 中的圖像建立新的 Linux Vm，OS 磁碟的加密目前不支援。 僅適用於加密內部部署並上傳至 Azure Vm 支援加密的 Linux Vm OS 音量。 這項限制僅適用於 OS 磁碟。資料區 Linux vm 被支援加密。

方案支援 IaaS Vm 公用預覽版本 Microsoft Azure 中啟用下列動作︰

-   Azure 鍵保存庫整合

-   標準[A、 D 和 G 數列 IaaS Vm](https://azure.microsoft.com/pricing/details/virtual-machines/)

-   在使用[Azure 資源管理員](../azure-resource-manager/resource-group-overview.md)模型建立 IaaS Vm 上啟用加密

-   所有 Azure 公用[區域](https://azure.microsoft.com/regions/)

此功能可確保您虛擬機器磁碟上的所有資料已都加密在 Azure 儲存體中的其餘部分。

####<a name="resources"></a>資源

-   [Azure 磁碟加密 for Windows 和 Linux IaaS 虛擬機器](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)

    本文討論 Azure 磁碟加密的預覽版本，並提供連結下載白皮書 （英文）。

###<a name="comparison-of-azure-disk-encryption-sse-and-client-side-encryption"></a>Azure 磁碟加密 SSE，與用戶端加密的比較

####<a name="iaas-vms-and-their-vhd-files"></a>IaaS Vm 和其 VHD 檔案

使用 IaaS Vm 磁碟，建議您使用 Azure 磁碟加密。 您可以開啟 SSE 加密 VHD 檔案移到最下層中 Azure 儲存體，這些磁碟所使用的但只會加密新撰寫的資料。 如果您建立 VM，然後保留 VHD 檔案儲存空間帳戶啟用 SSE，只要將變更將會加密，這就表示不原始 VHD 檔案。

如果您建立 VM 從 Azure Marketplace，使用圖像 Azure 中 Azure 儲存體中，執行[淺層複本](https://en.wikipedia.org/wiki/Object_copying)至您儲存的帳戶的圖像，，不會加密即使已啟用 SSE。 它會建立 VM，並啟動更新圖像之後，就會開始 SSE，加密資料。 因此，最好使用 Vm 建立從 Azure Marketplace 中的圖像，如果您想要完全加密 Azure 磁碟加密。

如果您將預先加密的 VM Azure 將來自內部部署，您可以將加密金鑰上載至 Azure 金鑰保存庫，並繼續使用您所使用的內部部署的 VM 加密。 Azure 磁碟加密已啟用處理這種狀況。

如果您有來自內部部署的非加密 VHD，可以上傳到圖庫以自訂圖像，並佈建從 VM。 如果您使用的資源管理員範本，您可以要求將它開機 VM 設定時，開啟 Azure 磁碟加密。

當您將資料磁碟新增，並裝載 VM 時，您可以開啟 Azure 磁碟加密資料磁碟。 它會在第一次，加密本機磁碟的資料，然後服務管理圖層會執行延遲寫入針對儲存空間，儲存內容已加密。

####<a name="client-side-encryption"></a>用戶端加密####

用戶端加密是最安全的方法加密您的資料，因為它加密、 傳輸之前，加密其餘的資料。 不過，它需要您將程式碼新增至使用儲存空間，您不想要的應用程式。 在這些情況下，您可以使用 HTTPs 適合您的資料在傳輸時會和 SSE 加密的其餘資料。

使用用戶端加密，您可以加密表格的實體、 佇列中的郵件和二進位大型物件。 使用 SSE，您可以只加密二進位大型物件。 如果您需要加密的資料表和佇列中的資料，您應該使用用戶端加密。

用戶端加密會完全管理應用程式。 這是最安全的方法，但會要求您以程式設計方式變更您的應用程式，並將金鑰管理程序中的位置。 您可以使用這要額外的安全性、 傳輸期間，而您想要加密您儲存的資料。

用戶端加密多負載在用戶端，而您必須考慮這延展性規劃，尤其是如果您是加密並傳送大量資料。

####<a name="storage-service-encryption-sse"></a>儲存服務加密 (SSE)

SSE 會管理 Azure 儲存體。 使用 SSE 不提供的安全性、 傳輸中的資料，但它會寫入到 Azure 儲存體來加密資料。 有不會影響效能時使用這項功能。

您可以只加密區塊 blob、 附加 blob，並頁面 blob 使用 SSE。 如果您需要加密表格資料或佇列中的資料，您應該考慮使用用戶端加密。

如果您有封存或您要用做為基礎來建立新的虛擬機器 VHD 檔案的文件庫，您可以建立新的儲存空間帳戶啟用 SSE，與然後將 VHD 檔案上傳到該帳戶。 將加密這些 VHD 檔案，以 Azure 儲存體。

如果您有啟用 VM 和 SSE 上按住 VHD 檔案的儲存空間帳戶啟用的磁碟 Azure 磁碟加密，它會正常運作。將會導致按兩次加密任何新撰寫資料。

##<a name="storage-analytics"></a>儲存空間狀況分析

###<a name="using-storage-analytics-to-monitor-authorization-type"></a>使用儲存空間分析監控授權類型

針對每個儲存帳戶，您可以啟用 Azure 來執行記錄及儲存指標資料的儲存空間分析。 這是帳戶的您想要檢查效能指標的儲存空間，或需要疑難排解儲存帳戶，因為您發生效能問題時使用的好工具。

您可以看到儲存分析記錄中的資料的另一個是存取儲存時，由使用的驗證方法。 例如，Blob 儲存體，您可以查看使用共用的 Access 簽章或儲存的帳戶金鑰，或如果存取 blob 公用。

這可能是很實用，如果您緊密保護存取儲存空間。 例如 [Blob 儲存體中的所有容器設為私人和整個應用程式實作 SA 服務使用。 然後，您可以檢查定期，查看是否使用儲存的帳戶金鑰，可能表示的安全性漏洞，存取您的二進位大型物件，或如果 blob 為公開狀態，但不應該是記錄。

####<a name="what-do-the-logs-look-like"></a>記錄外觀為何？

之後您啟用儲存帳戶指標，並透過 Azure 入口網站的記錄，分析資料會開始快速累積。 記錄和每個服務的指標會不同。登入只寫入活動中時該儲存帳戶時將記錄指標，每一分鐘、 每小時或每一天，根據您的設定。

記錄會儲存在名為在儲存帳戶 $logs 容器中的區塊 blob。 啟用儲存分析資料時，會自動建立此容器。 此容器建立後，您無法刪除，雖然您可以刪除其內容。

在 $logs 容器，有每個服務的資料夾，還有子資料夾的年/月/日/小時。 在小時、 記錄只要頁碼的編號。 這是目錄結構的外觀︰

![記錄檔的檢視](./media/storage-security-guide/image1.png)

記錄到 Azure 儲存體每個要求。 以下是記錄檔中，顯示第一個的幾個欄位的快照。

![記錄檔的快照](./media/storage-security-guide/image2.png)

您可以查看您可以使用記錄追蹤任何類型的來電至] 以儲存帳戶。

####<a name="what-are-all-of-those-fields-for"></a>什麼是所有這些欄位？

有列在下方的資源文章提供記錄和用途的多個欄位的清單。 以下是欄位的順序清單︰

![在記錄檔中的欄位的快照](./media/storage-security-guide/image3.png)

我們感興趣的項目 GetBlob，和驗證方式，因此我們需要尋找具有作業類型 」 取得-Blob 」 的項目，並核取的要求狀態 （<sup>第</sup>4 欄），並授權類型 （8<sup>第</sup>欄）。

例如中的前幾個資料列在上述清單，要求的狀態是 「 成功 」，然後授權類型 「 驗證 」。 這表示要求已確認使用的儲存空間帳戶金鑰。

####<a name="how-are-my-blobs-being-authenticated"></a>我的二進位大型物件的驗證方式？

我們有我們感興趣的三種情況。

1.  Blob 公用，而存取使用沒有共用 Access 簽章的 URL。 在此情況下，要求的狀態是 「 AnonymousSuccess 」，並授權類型是 「 匿名 」。

    1.0; 2015年-11-17T02:01:29.0488963Z;GetBlob;**AnonymousSuccess**; 200; 124; 37;**匿名**;mystorage...

2.  Blob 是私人群組，然後使用使用共用的 Access 簽章。 在此情況下，要求的狀態是 「 SASSuccess 」，並授權類型是 「 sa 」。

    1.0; 2015年-11-16T18:30:05.6556115Z;GetBlob;**SASSuccess**; 200; 416; 64;**sa**;mystorage...

3.  Blob 是私人群組，然後儲存金鑰用來存取。 如此一來，要求的狀態是 「**成功**」，並授權類型是 「**驗證**」。

    1.0; 2015年-11-16T18:32:24.3174537Z;GetBlob;**成功**; 206; 59; 22;**驗證**; mystorage...

您可以使用 Microsoft 訊息分析程式來檢視和分析這些記錄。 其中包含搜尋及篩選功能。 例如，您可能會想要搜尋的使用狀況是否為您預期的事情，亦即，請確定其他人未存取您儲存的帳戶不當 GetBlob 執行個體。

####<a name="resources"></a>資源

-   [儲存空間狀況分析](storage-analytics.md)

    這篇文章的儲存空間分析及如何啟用這些的概觀。

-   [儲存分析記錄格式](https://msdn.microsoft.com/library/azure/hh343259.aspx)

    本文說明儲存分析記錄格式] 和 [詳細資料可用的欄位其中，包括驗證類型，亦即指出要求所使用的驗證類型。

-   [監控 Azure 入口網站中儲存的帳戶](storage-monitor-storage-account.md)

    本文說明如何設定的指標監視與記錄儲存帳戶。

-   [使用 Azure 儲存指標和記錄、 AzCopy 及郵件分析器的端對端疑難排解](storage-e2e-troubleshooting.md)

    本文說話有關使用儲存空間分析的疑難排解，並說明如何使用 Microsoft 訊息分析程式。

-   [Microsoft 訊息分析器作業系統指南](https://technet.microsoft.com/library/jj649776.aspx)

    本文為 Microsoft 訊息分析程式的參照，並包括教學課程中快速入門，與功能摘要的連結。

##<a name="cross-origin-resource-sharing-cors"></a>十字形，資源共用 (CORS)

###<a name="cross-domain-access-of-resources"></a>跨網域存取的資源

執行一個網域中的網頁瀏覽器可讓資源的 HTTP 要求您從不同的網域，這稱為跨原點 HTTP 要求。 例如，contoso.com 從提供 HTML 頁面提出裝載於 fabrikam.blob.core.windows.net 為 jpeg 要求。 基於安全性理由瀏覽器限制發起的租用戶的指令碼，例如 JavaScript 中跨原點 HTTP 要求。 這表示當在 contoso.com 上的網頁上的部分 JavaScript 程式碼要求該 jpeg fabrikam.blob.core.windows.net 上的，在瀏覽器將不允許要求。

這會對 Azure 儲存體有什麼？ 如果您儲存靜態例如 JSON 或 XML 資料檔案的資產 Blob 儲存體中使用儲存空間帳戶稱為 Fabrikam 資產的網域會 fabrikam.blob.core.windows.net，與 contoso.com web 應用程式將無法存取使用 JavaScript，因為網域有不同。 這也是則為 true，如果您正嘗試呼叫其中一個 Azure 儲存服務的 – 例如資料表儲存體 – 的 JSON 資料傳回由 JavaScript 用戶端。

####<a name="possible-solutions"></a>可能的解決方案

若要解決這個問題的其中一個方法是將自訂的網域，例如 「 storage.contoso.com 」 指派給 fabrikam.blob.core.windows.net。 問題是您僅可以指派的自訂網域一儲存的帳戶。 如果資產會儲存在多個儲存帳戶？

若要解決這個問題的另一個方法是有作為儲存通話 proxy web 應用程式。 這表示您將檔案上傳至 Blob 儲存體]、 web 應用程式會是本機寫並將它複製到 Blob 儲存體，或它會讀取所有的記憶體，然後將它寫入 Blob 儲存體。 或者，您可以撰寫的專用的 web 應用程式 （例如網頁的 API) 上傳的本機檔案，並將它們寫入 Blob 儲存體。 兩種方式，您必須決定延展性需要時，該函數的帳戶。

####<a name="how-can-cors-help"></a>如何協助 CORS？

Azure 儲存空間，可讓您啟用 CORS – 交互原點資源共用。 針對每個儲存帳戶，您可以指定可以存取儲存帳戶中的資源的網域。 例如，在上述範例中，我們可以啟用 CORS fabrikam.blob.core.windows.net 儲存帳戶，並將它設定為允許存取 contoso.com。 然後 web 應用程式 contoso.com 可直接存取 fabrikam.blob.core.windows.net 中的資源。

注意是 CORS 可讓存取權，但不提供驗證]，這是必要的所有非公用存取存放資源。 這表示您只能存取 blob 如果他們是公用或包含適當的權限授與您共用的 Access 簽名。 表格、 佇列和檔案沒有公用存取權，並要求 SA。

根據預設，CORS 上已停用所有服務。 您可以啟用 CORS 呼叫其中一個方法來設定服務原則使用 REST API 或儲存空間的用戶端文件庫。 當您執行的動作時，您會包含 CORS 規則，也就是 xml。 以下是使用 Blob 服務設定服務內容作業儲存帳戶已設定 CORS 規則的範例。 您可以執行的作業，使用儲存空間的用戶端文件庫或 REST Api 用 Azure 儲存體。

    <Cors>    
        <CorsRule>
            <AllowedOrigins>http://www.contoso.com, http://www.fabrikam.com</AllowedOrigins>
            <AllowedMethods>PUT,GET</AllowedMethods>
            <AllowedHeaders>x-ms-meta-data*,x-ms-meta-target*,x-ms-meta-abc</AllowedHeaders>
            <ExposedHeaders>x-ms-meta-*</ExposedHeaders>
            <MaxAgeInSeconds>200</MaxAgeInSeconds>
        </CorsRule>
    <Cors>

以下是每個資料列代表的意義︰

-   **AllowedOrigins**這會告訴哪些不相符的網域可以要求並接收儲存服務的資料。 這會顯示，contoso.com 和 fabrikam.com 都可以要求資料 Blob 儲存體從特定儲存帳戶。 您也可以將此為使用萬用字元 (\*) 若要允許存取要求的所有網域。

-   **AllowedMethods**這是一種方法 （HTTP 要求動作） 提出要求時，可以使用的清單。 在此範例中，只放入取得允許和。 您可以將此為使用萬用字元 (\*) 若要讓所有要使用的方法。

-   **AllowedHeaders**這是要求的標頭提出要求時，可以指定原點網域。 在此範例中，所有的中繼資料標題 x-ms-中繼資料，以開始 x-ms-中繼-目標，並 x ms-中繼 abc 允許。 萬用字元 (\*) 表示是否允許指定的前置詞與任何頁首開頭。

-   **ExposedHeaders**這就是告訴哪一個回應標頭應該公開，以要求發行者的瀏覽器。 在此範例中，以開始任何頁首 」 x-ms-中繼-」 將公開。

-   **MaxAgeInSeconds**這是時間的最大的在瀏覽器會快取預先選項要求量。 （如需有關預先要求的詳細資訊，請核取下列第一份文件）。

####<a name="resources"></a>資源

如需有關 CORS，以及如何將它啟用的詳細資訊，請查看這些資源。

-   [十字形，資源共用 (CORS) 支援的 Azure.com Azure 儲存服務](storage-cors-support.md)

    本文提供 CORS 並瞭解如何針對不同的儲存空間服務規則的概觀。

-   [十字形，資源共用 (CORS) 支援的 MSDN Azure 儲存服務](https://msdn.microsoft.com/library/azure/dn535601.aspx)

    這是 Azure 儲存服務 CORS 支援的參考文件。 此已套用至每個儲存服務的文章連結，並顯示範例，並說明 CORS 檔案中的每個項目。

-   [Microsoft Azure 儲存空間︰ 介紹 CORS](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/02/03/windows-azure-storage-introducing-cors.aspx)

    這是宣佈使用 CORS 及顯示如何使用它初始的部落格文章的連結。

##<a name="frequently-asked-questions-about-azure-storage-security"></a>Azure 儲存體安全性相關的常見問題集

1.  **我要如何確認我無法使用 HTTPS 通訊協定正在轉接進或移出 Azure 儲存體 blob 的完整性？**

    如果您需要使用 HTTP，而 HTTPS，而不是任何原因區塊二進位大型物件的操作，您可以使用 MD5 檢查以協助確認傳輸 blob 的完整性。 這將可協助保護從網路/傳輸層錯誤，但不是一定中繼攻擊。

    您可以使用 HTTPS 提供傳輸層級安全性，然後使用 [MD5 檢查重複而非必要。
    
    如需詳細資訊，請查看[Azure Blob MD5 概觀](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/02/18/windows-azure-blob-md5-overview.aspx)。

2.  **FIPS 規範的美國政府呢？**

    美國聯邦資訊處理標準 (FIPS) 定義加密演算法來保護機密資料的美國聯邦政府電腦系統核准使用。 啟用 FIPS Windows 伺服器或桌上型電腦上的模式就是告訴 OS 應只 FIPS 驗證加密演算法。 如果應用程式使用非相容演算法，將會中斷應用程式。 With.NET framework 4.5.2 或更高，應用程式會自動切換 FIPS 模式電腦時，使用 FIPS 相容演算法的加密演算法。

    Microsoft 保留最多每一位客戶，以決定是否要啟用 FIPS 模式。 我們相信沒有令人讚嘆的客戶沒有啟用 FIPS 模式預設政府法規理由。

    **資源**

-   [為什麼我們不建議您將 「 FIPS 模式 」 不再](http://blogs.technet.com/b/secguide/archive/2014/04/07/why-we-re-not-recommending-fips-mode-anymore.aspx)

    此部落格文章提供 FIPS 的概觀，並說明它們為何沒有啟用 FIPS 模式預設。

-   [FIPS 140 驗證](https://technet.microsoft.com/library/cc750357.aspx)

    本文提供有關如何 Microsoft 產品及密碼編譯的模組遵守美國聯邦政府版的 FIPS 標準。

-   [「 系統加密︰ 使用 FIPS 相容演算法加密，雜湊，以及簽署 「 Windows XP 和更新版本的 Windows 中的安全性設定效果](https://support.microsoft.com/kb/811833)

    本文討論的較舊版本的 Windows 電腦中的 FIPS 模式下使用。
