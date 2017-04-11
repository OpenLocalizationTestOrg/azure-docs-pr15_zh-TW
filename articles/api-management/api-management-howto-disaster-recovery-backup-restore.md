<properties 
    pageTitle="如何實作損毀修復使用服務備份和還原 Azure API 管理 |Microsoft Azure" 
    description="瞭解如何使用備份與還原 Azure API 管理中執行復原損毀。" 
    services="api-management" 
    documentationCenter="" 
    authors="steved0x" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>

# <a name="how-to-implement-disaster-recovery-using-service-backup-and-restore-in-azure-api-management"></a>如何實作損毀修復使用服務備份和還原 Azure API 管理

選擇發佈並管理您的 Api 透過 Azure API 管理您利用許多容錯能力和基礎結構功能原本設計、 實作及管理。 Azure 平台降低成本的分數，可能發生失敗的大型分數。

若要復原可用性問題會影響您的 API 管理服務會裝載您的地區應準備好重新您的服務不同區域組成隨時。 根據您的可用性目標和復原時間目標可能會想要保留在一或多個區域內的備份服務，並嘗試維護其設定和內容的作用中服務同步。 服務備份和還原功能提供必要的建置組塊實作損毀還原策略。

如何驗證 Azure 資源管理員的要求，以及如何備份及還原您的 API 管理服務執行個體，則會顯示本指南。

>[AZURE.NOTE] 備份及還原損毀修復 API 管理服務執行個體的程序也可以用於複寫 API 管理服務執行個體，如臨時案例。
>
>請注意，每個備份到期後 7 天。 如果您嘗試將備份還原過期 7 日過期期間之後，將會失敗還原`Cannot restore: backup expired`訊息。

## <a name="authenticating-azure-resource-manager-requests"></a>驗證 Azure 資源專案經理要求

>[AZURE.IMPORTANT] 備份與還原 REST API 使用 Azure 資源管理員，而且有不同的驗證機制比 REST Api 來管理您的 API 管理實體。 本節中的步驟說明如何驗證 Azure 資源管理員的要求。 如需詳細資訊，請參閱[驗證 Azure 資源管理員的要求](http://msdn.microsoft.com/library/azure/dn790557.aspx)。

所有的工作，您可以在 [資源使用 Azure 資源管理員必須與使用下列步驟的 Azure Active Directory 驗證。

-   新增應用程式的 Azure Active Directory 租用戶。
-   設定您所新增的應用程式的權限。
-   取得的權杖驗證至 Azure 資源管理員的要求。

第一步是建立的 Azure Active Directory 應用程式。 登入[Azure 傳統入口網站](http://manage.windowsazure.com/)使用包含您的 API 管理服務執行個體的訂閱，然後瀏覽至您的預設 Azure Active Directory 的**應用程式**索引標籤。

>[AZURE.NOTE] 如果您的帳戶看不到 [預設 Azure Active Directory 的目錄，請連絡 Azure 訂閱的系統管理員，授與您的帳戶所需的權限。 尋找您的預設目錄的詳細資訊，請參閱[尋找您的預設目錄](../virtual-machines/virtual-machines-windows-create-aad-work-id.md#locate-your-default-directory-in-the-azure-portal)。

![建立 Azure Active Directory 應用程式][api-management-add-aad-application]

按一下 [**新增**]，**新增組織內部開發的應用程式**]，然後選擇 [**原生的用戶端應用程式**。 輸入描述性的名稱，然後按一下下一步的箭號。 輸入的版面配置區的 URL，例如`http://resources`為**重新導向 URI**的成為必填的欄位，但不是稍後使用的值。 按一下儲存應用程式的核取方塊。

後儲存應用程式時，按一下 [**設定**]，向下捲動**其他應用程式的權限**] 區段中，然後按一下 [**新增應用程式**。

![新增權限][api-management-aad-permissions-add]

選取**在 Windows** **Azure 服務管理 API** ，然後按一下 [新增應用程式的核取方塊。

![新增權限][api-management-aad-permissions]

按一下 [**委派的權限**旁邊，新加入的**Windows** **Azure 服務管理 API**應用程式，**存取 Azure 服務管理 （預覽版本）**，核取方塊，按一下 [**儲存**]。

![新增權限][api-management-aad-delegated-permissions]

之前叫用的 Api 產生備份，並將它還原，則必須取得權杖。 下列範例會使用來擷取權杖[Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) nuget 套件。

    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System;

    namespace GetTokenResourceManagerRequests
    {
        class Program
        {
            static void Main(string[] args)
            {
                var authenticationContext = new AuthenticationContext("https://login.windows.net/{tenant id}");
                var result = authenticationContext.AcquireToken("https://management.azure.com/", {application id}, new Uri({redirect uri});

                if (result == null) {
                    throw new InvalidOperationException("Failed to obtain the JWT token");
                }

                Console.WriteLine(result.AccessToken);

                Console.ReadLine();
            }
        }
    }

取代`{tentand id}`， `{application id}`，及`{redirect uri}`使用下列的指示。

取代`{tenant id}`您剛剛建立的 Azure Active Directory 應用程式的租用戶識別碼。 您可以存取識別碼，即可**檢視結束點**。

![結束點][api-management-aad-default-directory]

![結束點][api-management-endpoint]

取代`{application id}`和`{redirect uri}`從 Azure Active Directory 應用程式的**設定**] 索引標籤上的 [**重新導向 Uri** ] 區段中使用**用戶端識別碼**] 和 [URL。 

![資源][api-management-aad-resources]

一旦指定的值，在程式碼範例應傳回權杖類似下面的範例。

![權杖][api-management-arm-token]

呼叫之前下列各節所述的備份與還原作業，設定您的其餘部分來電的授權要求標頭。

    request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);

## <a name="step1"></a>備份 API 管理服務
若要備份的 API 管理服務問題下列 HTTP 要求︰

`POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/backup?api-version={api-version}`

位置︰

* `subscriptionId`-訂閱包含您要的 API 管理服務的備份
* `resourceGroupName`-在表單中的 'Api 預設-{服務地區}' 字串位置`service-region`識別 Azure 區域位置 API 管理服務您嘗試裝載的備份，例如︰`North-Central-US`
* `serviceName`-API 管理服務名稱您要製作備份指定其建立的時間
* `api-version`-取代`2014-02-14`

本文中的要求，指定目標 Azure 儲存體帳戶名稱、 便捷鍵、 blob 容器名稱和備份的名稱︰

    '{  
        storageAccount : {storage account name for the backup},  
        accessKey : {access key for the account},  
        containerName : {backup container name},  
        backupName : {backup blob name}  
    }'

設定的值`Content-Type`要求標頭`application/json`。

備份是長時間執行的作業，可能需要多個分鐘才能完成。  如果已成功，並啟動備份程序就會收到`202 Accepted`與回應狀態碼`Location`頁首。  讓取得邀請中的 url`Location`頁首，找出作業的狀態。 在進行備份時您會繼續收到 「 202 接受' 狀態碼。 回應代碼的`200 OK`會指出備份作業成功完成。

**附註**︰

- 在邀請內文，**必須存在**指定的**容器**。
* 備份進行時您**不應該嘗試任何服務管理作業**等 SKU 升級或降級網域名稱的變更等。 
* 還原的**備份保證僅適用於 7 天**後的建立。 
* **使用資料**用來建立分析報表**不包含**在備份。 使用[Azure API 管理 REST API][]定期擷取妥善保存分析報表。
* 您可以與執行服務備份的頻率會影響您的復原點目標。 若要將其最小化我們建議實作定期備份，以及您的 API 管理服務的重要變更後執行指定備份。
* 備份作業時 （例如 Api、 原則、 開發人員入口網站外觀） 服務設定所做的**變更**是程序**可能不會包含在備份，因此會遺失**。

## <a name="step2"></a>回復 API 管理服務
若要還原 API 管理從先前建立備份檔案的服務，請執行下列 HTTP 要求︰

`POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/restore?api-version={api-version}`

位置︰

* `subscriptionId`-id 包含您要還原的備份到 API 管理服務的訂閱
* `resourceGroupName`-在表單中的 'Api 預設-{服務地區}' 字串位置`service-region`識別 Azure 區域裝載您要還原的備份到 API 管理服務的位置，例如︰`North-Central-US`
* `serviceName`-API 管理服務進行還原到指定一次建立的名稱
* `api-version`-取代`2014-02-14`

本文中的要求，指定備份檔案的位置，也就是 Azure 儲存體帳戶名稱、 便捷鍵、 blob 容器名稱和備份的名稱︰

    '{  
        storageAccount : {storage account name for the backup},  
        accessKey : {access key for the account},  
        containerName : {backup container name},  
        backupName : {backup blob name}  
    }'

設定的值`Content-Type`要求標頭`application/json`。

還原是長時間執行的作業，最多可能需要 30 分鐘才能完成。  如果已成功與還原程序啟動您會收到`202 Accepted`與回應狀態碼`Location`頁首。  讓取得邀請中的 url`Location`頁首，找出作業的狀態。 在進行還原時您還是會繼續收到 「 202 接受' 狀態碼。 回應代碼的`200 OK`會表示還原作業成功完成。

>[AZURE.IMPORTANT] **SKU**的服務正在還原到**必須符合**的 SKU，要還原的備份服務。
>
>進度**可能會覆寫**位於時還原作業 （例如 Api、 原則、 開發人員入口網站外觀） 服務設定所做的**變更**。

## <a name="next-steps"></a>後續步驟
請參閱下列 Microsoft 部落格的備份/還原程序的兩個不同的逐步解說。

-   [複寫 Azure API 管理帳戶](https://www.returngis.net/en/2015/06/replicate-azure-api-management-accounts/) 
    -   感謝您以 Gisela 她的比重這份文件。
-   [Azure API 管理︰ 備份和還原設定](http://blogs.msdn.com/b/stuartleeks/archive/2015/04/29/azure-api-management-backing-up-and-restoring-configuration.aspx)
    -   依蔡詳細的方法不相符的正式的指導方針，但它可以很有趣。

[Backup an API Management service]: #step1
[Restore an API Management service]: #step2


[Azure API 管理 REST API]: http://msdn.microsoft.com/library/azure/dn781421.aspx

[api-management-add-aad-application]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-add-aad-application.png

[api-management-aad-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions.png
[api-management-aad-permissions-add]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions-add.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-delegated-permissions.png
[api-management-aad-default-directory]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-default-directory.png
[api-management-aad-resources]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-resources.png
[api-management-arm-token]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-arm-token.png
[api-management-endpoint]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-endpoint.png
 
