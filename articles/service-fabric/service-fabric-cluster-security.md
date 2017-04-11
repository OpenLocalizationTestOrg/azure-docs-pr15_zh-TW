<properties
   pageTitle="安全服務布料的轉印圖樣叢集 |Microsoft Azure"
   description="將服務布料的轉印圖樣叢集和不同技術用來執行這些案例的安全性案例的說明。"
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/19/2016"
   ms.author="chackdan"/>

# <a name="service-fabric-cluster-security-scenarios"></a>服務布料的轉印圖樣叢集安全性案例

服務布料的轉印圖樣叢集是您擁有自己的資源。 若要防止未經授權的使用者連線到您的叢集，尤其是當有生產負載上執行時，才時，應該一律保護叢集。 雖然您可以建立不安全的叢集，如此一來，因此可讓任何匿名使用者，如果它公開管理結束點到公用網際網路連線。 

本文提供叢集 Azure 或獨立以及用來執行這些案例的各種技術上執行的安全性案例的概觀。 叢集安全性情況如下︰

- 節點的安全性
- 用戶端-節點安全性
- 角色型存取控制 (RBAC)

## <a name="node-to-node-security"></a>節點的安全性
保護 Vm 或叢集在電腦之間的通訊。 如此一來，可確保加入叢集已獲授權的電腦可以參與主機服務應用程式與服務叢集。

![節點-通訊的圖表][Node-to-Node]

在 Windows 上執行的 Azure 或獨立叢集上執行的叢集可以使用[憑證安全性](https://msdn.microsoft.com/library/ff649801.aspx)或[Windows 安全性](https://msdn.microsoft.com/library/ff649396.aspx)的 Windows Server 的電腦。
### <a name="node-to-node-certificate-security"></a>節點-憑證安全性
服務布料的轉印圖樣使用當您建立叢集節點類型設定中所指定的 X.509 伺服器憑證。 在本文結尾提供這些憑證是什麼，以及如何取得或建立的快速概觀。

憑證的安全性設定時建立叢集透過 Azure 入口網站、 Azure 資源管理員範本或獨立 JSON 範本。 您可以指定主要憑證和憑證變換用來選擇性次要憑證。 您指定的主要和次要憑證不能與管理員用戶端與您指定的[用戶端-節點安全性](#client-to-node-security)的唯讀的用戶端憑證。

Azure 閱讀，瞭解如何設定叢集憑證安全性的 [[設定叢集使用 Azure 資源管理員範本](service-fabric-cluster-creation-via-arm.md)。

獨立的 Windows Server 閱讀[安全獨立叢集使用 X.509 憑證的 Windows 上](service-fabric-windows-cluster-x509-security.md)

### <a name="node-to-node-windows-security"></a>節點的 windows 安全性
獨立的 Windows Server 閱讀[安全獨立叢集使用 Windows 安全性的 Windows 上](service-fabric-windows-cluster-windows-security.md)

## <a name="client-to-node-security"></a>用戶端-節點安全性
驗證用戶端和保護用戶端和叢集個別節點之間的通訊。 此類型的安全性驗證並保護用戶端通訊，可確保只有授權的使用者可以存取叢集和部署叢集上的應用程式。 透過 Windows 安全性憑證或其憑證安全性認證唯一識別用戶端。

![用戶端-節點通訊的圖表][Client-to-Node]

[安全性憑證](https://msdn.microsoft.com/library/ff649801.aspx)或[Windows 安全性](https://msdn.microsoft.com/library/ff649396.aspx)，可以使用叢集 Azure 或獨立叢集在 Windows 上執行上執行。

### <a name="client-to-node-certificate-security"></a>用戶端-節點憑證安全性
 用戶端-節點憑證的安全性設定透過 Azure 入口網站，資源管理員範本] 或 [指定管理員用戶端憑證及/或使用者用戶端憑證獨立 JSON 範本建立叢集時。  管理員用戶端和使用者用戶端憑證您指定應該不同於您指定的[節點-安全性](#node-to-node-security)的主要和次要憑證。

連接到叢集使用管理員認證的用戶端可以完全存取管理功能。  用戶端連線到使用唯讀使用者用戶端憑證叢集有僅限讀取權限管理功能。 換句話說本文稍後所述的角色基底存取控制 (RBAC) 使用這些憑證。

Azure 閱讀，瞭解如何設定叢集憑證安全性的 [[設定叢集使用 Azure 資源管理員範本](service-fabric-cluster-creation-via-arm.md)。

獨立的 Windows Server 閱讀[安全獨立叢集使用 X.509 憑證的 Windows 上](service-fabric-windows-cluster-x509-security.md)

### <a name="client-to-node-azure-active-directory-aad-security-on-azure"></a>Azure 的用戶端-節點 Azure Active Directory (AAD) 安全性
Azure 上執行的叢集也可以安全存取使用 Azure Active Directory (AAD) 管理結束點。 如何建立所需的 AAD 成品、 如何將它們填入叢集建立期間，以及如何連線到這些叢集之後的資訊，請參閱[設定叢集來使用 Azure 資源管理員範本](service-fabric-cluster-creation-via-arm.md)。

## <a name="security-recommendations"></a>安全性的建議
Azure 叢集，建議您使用 AAD 安全性驗證用戶端和節點-安全性的憑證。

獨立的 Windows Server 的建議] 群組中使用 Windows 安全叢集管理帳戶 (GMA) 如果您有 Windows Server 2012 R2 與 Active Directory。 否則，仍使用 Windows 帳戶的 Windows 安全性。

## <a name="role-based-access-control-rbac"></a>角色存取控制 (RBAC)
存取控制 」 可讓叢集系統管理員，限制存取特定的叢集操作，針對不同的使用者，使叢集更安全的群組。 兩種不同的存取控制項類型支援的用戶端連線至叢集︰ 管理員角色和使用者角色。

系統管理員擁有完整存取權管理功能 （包括讀/寫功能）。 使用者，根據預設，具備只管理功能 （例如，查詢功能） 的 「 讀取 」 權限和解決應用程式與服務的能力。

您指定的系統管理員和使用者的用戶端角色叢集建立同時提供個別的身分識別 （憑證，AAD 等） 每個。 如需有關預設存取控制設定，以及如何變更預設設定的詳細資訊，請參閱[角色存取控制服務布料的轉印圖樣的用戶端](service-fabric-cluster-security-roles.md)。


## <a name="x509-certificates-and-service-fabric"></a>X.509 憑證和服務布料的轉印圖樣
X.509 數位憑證常用驗證用戶端和伺服器，來加密並數位簽署的郵件。 如需這些憑證的詳細資訊，請移至[使用的憑證](http://msdn.microsoft.com/library/ms731899.aspx)。

請考慮的一些重要事項︰

- 必須使用正確設定的 Windows Server 的憑證服務建立或從 [已核准的[憑證授權單位 (CA)](https://en.wikipedia.org/wiki/Certificate_authority)取得叢集執行生產負載中使用的憑證。
- 不使用任何暫時或所建立的工具，例如 MakeCert.exe 生產環境中測試憑證。
- 您可以使用自我簽署的憑證，但應該只如此做為測試叢集而非生產。

### <a name="server-x509-certificates"></a>伺服器 X.509 憑證

伺服器憑證必須驗證伺服器 （節點） 用戶端，或驗證伺服器 （節點） 的伺服器 （節點） 的主要工作。 初始檢查在用戶端或節點進行驗證節點時可核取 [主旨] 欄位中的一般名稱的值。 此常見的名稱或其中一項憑證的主體替代名稱都必須有清單中的 [允許常用的名稱。

下列文章說明如何產生主旨替代名稱 （舊） 的憑證︰[如何新增至安全的 LDAP 憑證的主體替代名稱](http://support.microsoft.com/kb/931351)。

[主旨] 欄位可以包含多個值，每個初始化表示值類型的前置字元。 初始設定大多數情況下，是 「 CN 」 常見的名稱。例如，「 CN = www.contoso.com 」。 您也可為 [主旨] 欄位為空白。 如果已填入選擇性主旨的替代名稱] 欄位中，它必須包含的憑證的常見名稱和主旨的替代名稱每一個項目。 這些會輸入為 DNS 名稱值。

憑證的 [使用目的欄位的值應該包含適當的值，例如 「 伺服器驗證 」 或 「 用戶端驗證 」。

### <a name="client-x509-certificates"></a>用戶端 X.509 憑證

用戶端憑證通常是不是由協力廠商憑證授權單位核發。 不過，目前使用者位置的個人存放區通常包含放在這裡的根授權，請使用 「 用戶端驗證 」 的預定目的的用戶端憑證。 需要相互驗證時，用戶端可以使用的認證。

>[AZURE.NOTE] 服務布料的轉印圖樣叢集上的所有管理作業都需要伺服器憑證。 用戶端憑證無法用於管理。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->


## <a name="next-steps"></a>後續步驟

本文提供有關叢集安全性的概念性資訊。 [建立叢集 Azure 使用資源管理員範本中](service-fabric-cluster-creation-via-arm.md)的下一步] 或 [透過[Azure 入口網站](service-fabric-cluster-creation-via-portal.md)。

<!--Image references-->
[Node-to-Node]: ./media/service-fabric-cluster-security/node-to-node.png
[Client-to-Node]: ./media/service-fabric-cluster-security/client-to-node.png
