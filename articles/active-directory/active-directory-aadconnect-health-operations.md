<properties
    pageTitle="Azure AD Connect 狀況作業。"
    description="本文將說明您已經部署 Azure AD 連線狀況後，才能執行的其他作業。"
    services="active-directory"
    documentationCenter=""
    authors="karavar"
    manager="samueld"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="vakarand"/>

# <a name="azure-ad-connect-health-operations"></a>Azure AD Connect 狀況作業

下列主題即說明您可以使用 Azure AD 連線狀況執行之各種作業。

## <a name="enable-email-notifications"></a>啟用電子郵件通知
您可以設定 Azure AD 連線狀況服務會產生警示，指出您的身分識別基礎結構不健全時，傳送電子郵件通知。 產生通知時，為時已標示為已解決會發生這種情況。 請遵循下列指示操作來設定電子郵件通知。

![Azure AD Connect 狀況電子郵件通知探索](./media/active-directory-aadconnect-health/email_noti_discover.png)

>[AZURE.NOTE] 預設會停用電子郵件通知。


### <a name="to-enable-azure-ad-connect-health-email-notifications"></a>若要啟用 Azure AD 連線狀況電子郵件通知

1. 開啟您希望收到電子郵件通知服務的通知刀。
2. 按一下 [動作] 列的 [通知設定] 按鈕。
3. 開啟電子郵件通知切換至 [開啟]。
4. 選取核取方塊以設定接收電子郵件通知的所有全域管理員。
5. 如果您希望收到電子郵件通知的任何其他電子郵件地址，您可以在其他電子郵件收件者] 方塊中指定它們。 若要從這份清單中移除的電子郵件地址，以滑鼠右鍵按一下項目，然後選取 [刪除]。
6. 若要完成所做的變更按一下 [儲存]。 只有在您選取 [儲存] 後，所有變更將都會效果。

## <a name="delete-a-server-or-service-instance"></a>刪除伺服器或服務的執行個體

### <a name="delete-a-server-from-azure-ad-connect-health-service"></a>從 Azure AD 連線狀況服務刪除伺服器
在某些情況下，您可能會想要移除伺服器監視。 請遵循下列指示操作來移除 Azure AD 連線狀況服務的伺服器。

當刪除伺服器，請注意下列任一動作︰

- 這個動作會停止從伺服器收集任何進一步的資料。 此伺服器會從監視服務。 此動作之後，您將無法檢視新的通知，此伺服器的監控] 或 [使用狀況分析資料。
- 這個動作無法解除安裝或移除您的伺服器的健康情況代理程式。 如果您無法解除安裝健康情況代理程式執行此步驟之前，您可能會看到錯誤事件狀況代理程式相關的伺服器上。
- 此動作不會刪除從此伺服器已收集的資料。 依照 Microsoft Azure 資料保留原則，將會刪除該資料。
- 執行此動作之後, 如果您想要開始監視相同的伺服器，您必須解除安裝並重新安裝 [此伺服器上的健康情況代理程式。


#### <a name="to-delete-a-server-from-azure-ad-connect-health-service"></a>若要刪除 Azure AD 連線狀況服務的伺服器

Azure AD Connect 狀況的 AD FS 和 Azure AD 連線 （同步處理）︰

1. 選取要移除的伺服器名稱，從伺服器清單刀開啟伺服器刀。
2. 在伺服器刀中，按一下 [動作] 列的 「 刪除 」 按鈕。
3. 確認刪除伺服器確認方塊中輸入伺服器名稱的動作。
4. 按一下 [刪除] 按鈕。

Azure AD Connect AD ds 的狀況︰

1. 開啟 [網域控制站儀表板。
2. 選取要移除的網域。
3. 按一下 [動作] 列的 「 選取 [刪除] 按鈕。
4. 確認刪除伺服器所要的動作。
5. 按一下 [刪除] 按鈕。

### <a name="delete-a-service-instance-from-azure-ad-connect-health-service"></a>服務執行個體刪除 Azure AD 連線狀況服務

在某些情況下，您可能會想要移除服務執行個體。 請遵循下列指示操作來移除 Azure AD 連線狀況服務的服務執行個體。

當刪除服務執行個體，請注意下列任一動作︰

- 這個動作會移除目前的服務執行個體監視服務。
- 這個動作無法解除安裝或移除任何監視做為此服務執行個體的一部分的伺服器的健康情況代理程式。 如果您無法解除安裝健康情況代理程式執行此步驟之前，您可能會看到錯誤事件狀況代理程式相關的伺服器。
- 依照 Microsoft Azure 資料保留原則，將會刪除此服務執行個體中所有的資料。
- 執行此動作之後, 如果您想要開始監視服務，請解除安裝並重新安裝所有要監視之伺服器上的健康情況代理程式。 執行此動作之後, 如果您想要開始監視相同的伺服器，您必須解除安裝並重新安裝 [此伺服器上的健康情況代理程式。


#### <a name="to-delete-a-service-instance-from-azure-ad-connect-health-service"></a>若要從 Azure AD 連線狀況服務刪除服務執行個體

1. 選取您想要移除的服務識別項 （伺服器陣列的名稱），從服務清單刀開啟服務刀。
2. 在伺服器刀中，按一下 [動作] 列的 「 刪除 」 按鈕。
3. 在確認方塊中輸入密碼以確認服務名稱。 (例如︰ sts.contoso.com)
4. 按一下 [刪除] 按鈕。
<br><br>


[//]: # (Start of RBAC section)
## <a name="manage-access-with-role-based-access-control"></a>管理角色為基礎的存取權存取控制
### <a name="overview"></a>概觀
Azure AD 連線狀況[角色基礎存取控制](role-based-access-control-configure.md)提供存取 Azure AD 連線健康情況的服務使用者及/或全域管理員以外的群組。 這透過將角色指派給使用者和 \ 或群組，並提供機制來限制您的目錄中的全域系統管理員。

#### <a name="roles"></a>角色
Azure AD 連線狀況支援下列內建的角色。

| 角色 | 權限 |
| ----------- | ---------- |
| 擁有者 | 擁有者可以***管理存取權***（例如指派角色的使用者/群組），請***檢視所有資訊***（例如都檢視通知） 從都入口網站和***變更設定***（例如電子郵件通知） 中 Azure AD 連線狀況。 <br>根據預設，Azure AD 全域系統管理員會指派此角色，這不會變更。  |
|參與者|  參與者可以***檢視所有資訊***（例如都檢視通知） 從都入口網站和***變更設定***（例如電子郵件通知） 中 Azure AD 連線狀況。|
|閱讀程式| 讀者可以***檢視所有資訊***（例如都檢視通知） 從入口網站中 Azure AD 連線狀況。|

所有其他的角色 （例如 「 使用者存取系統管理員 」 或 「 DevTest 實驗室使用者]），即使用於入口網站的體驗，沒有存取 Azure AD 連線狀況內任何影響。

#### <a name="access-scope"></a>存取範圍

Azure AD Connect 支援在兩種層級的管理存取權︰

- ***所有服務執行個體***︰ 這是最客戶的建議的路徑，在所有的角色類型正在監視 Azure AD 連線狀況的控制項的所有服務執行個體 （例如在 ADFS 伺服器陣列） 存取。

- ***服務執行個體***︰ 在某些情況下，您可能需要將存取權的角色型或服務執行個體。 在此情況下，您可以管理服務執行個體層級的存取權。  

權限授與如果使用者擁有存取 [目錄] 或 [服務執行個體層級。


### <a name="how-to-allow-users-or-groups-access-to-azure-ad-connect-health"></a>如何允許使用者或群組的存取權 Azure AD 連線狀況
#### <a name="steps-1-select-the-appropriate-access-scope"></a>步驟 1︰ 選取適當的存取範圍
若要允許使用者存取 Azure AD 連線狀況內的*所有服務執行個體*層級，請開啟主刀中 Azure AD 連線狀況。<br>
#### <a name="step-2-add-users-groups-and-assign-roles"></a>步驟 2︰ 新增使用者、 群組及指派角色
1. 按一下 [設定] 區段中的 「 使用者 」 部分。<br>
![Azure AD Connect 狀況 RBAC 主刀](./media/active-directory-aadconnect-health/RBAC_main_blade.png)
2. 選取 [新增]
3. 選取 「 角色 」 等 「 擁有者 」<br>
![Azure AD Connect 狀況 RBAC 新增使用者](./media/active-directory-aadconnect-health/RBAC_add.png)
4. 輸入名稱或識別碼的目標的使用者或群組。 您可以同時選取一或多個使用者或群組。 按一下 [選取]。
![Azure AD Connect 狀況 RBAC 選取使用者](./media/active-directory-aadconnect-health/RBAC_select_users.png)
5. 選取 [確定]。<br>

6. 完成的角色指派之後，使用者及/或群組會出現在清單中。<br>
![Azure AD Connect 狀況 RBAC 使用者清單](./media/active-directory-aadconnect-health/RBAC_user_list.png)

列出的使用者和群組依照其指派角色的存取權，可讓這些步驟。
>[AZURE.NOTE]
- 全域管理員只能的完整存取所有作業，但全域管理員帳戶將不會出現在上述的清單。
- Azure AD 連線狀況中不支援 「 邀請使用者 」 功能。

#### <a name="step-3-share-the-blade-location-with-users-or-groups"></a>步驟 3︰ 與使用者或群組共用刀位置
1. 之後指派權限，使用者可以移至[http://aka.ms/aadconnecthealth](http://aka.ms/aadconnecthealth)存取 Azure AD 連線狀況。
2. 一次在刀，使用者可以固定刀或儀表板的不同部分只要按一下 [固定至儀表板 」<br>
![Azure AD 連線狀況 RBAC pin 刀](./media/active-directory-aadconnect-health/RBAC_pin_blade.png)


>[AZURE.NOTE] 與 「 讀取 」 角色指派給使用者無法執行 「 建立 「 若要從 Azure Marketplace 取得 Azure AD 連線狀況副檔名。 刀仍然可以存取此使用者移至上述的連結。 後續的用法，使用者可以釘選至儀表板刀。

### <a name="remove-users-andor-groups"></a>移除使用者或群組
您可以移除使用者或群組新增至 Azure AD 連線狀況角色基礎存取控制組件，以滑鼠右鍵按一下，然後選取 [移除]。<br>
![Azure AD Connect 狀況 RBAC 移除使用者](./media/active-directory-aadconnect-health/RBAC_remove.png)

[//]: # (End of RBAC section)

## <a name="related-links"></a>相關的連結

* [Azure AD Connect 健康狀況](active-directory-aadconnect-health.md)
* [Azure AD Connect 狀況代理程式的安裝](active-directory-aadconnect-health-agent-install.md)
* [使用 Azure AD 連線健康狀況與 AD FS](active-directory-aadconnect-health-adfs.md)
* [使用同步處理 Azure AD 連線狀況](active-directory-aadconnect-health-sync.md)
* [使用 Azure AD 連線健康狀況與 AD DS](active-directory-aadconnect-health-adds.md)
* [Azure AD Connect 狀況常見問題集](active-directory-aadconnect-health-faq.md)
* [Azure AD Connect 狀況版本歷程記錄](active-directory-aadconnect-health-version-history.md)
