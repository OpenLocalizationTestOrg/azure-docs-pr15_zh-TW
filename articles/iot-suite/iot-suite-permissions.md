<properties
  pageTitle="Azure IoT 套件和 Azure Active Directory |Microsoft Azure"
  description="描述如何 Azure IoT 套件使用 Azure Active Directory 可用來管理權限。"
  services=""
  suite="iot-suite"
  documentationCenter=""
  authors="aguilaaj"
  manager="timlt"
  editor=""/>

<tags
  ms.service="iot-suite"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="na"
  ms.workload="na"
  ms.date="10/24/2016"
  ms.author="araguila"/>
  
# <a name="permissions-on-the-azureiotsuitecom-site"></a>Azureiotsuite.com 網站上的權限

## <a name="what-happens-when-you-sign-in"></a>當您登入時，會發生什麼情況

當您第一次登入在[azureiotsuite.com][lnk-azureiotsuite]，網站會決定您有根據目前選取的 Azure Active Directory (AAD) 租用戶和 Azure 訂閱的權限等級。

1.  網站第一次會找出從 Azure 填入租用戶看到您已登入的使用者名稱] 旁的清單，才能隸屬於哪些 AAD 租用戶。 此時，網站只能取得一個租用戶的使用者權杖，一次。 如此一來，當您切換至不同的租用戶，在右上角中使用下拉式清單，網站重新記錄在該租用戶權杖取得該租用戶。

2.  接下來，網站會找出哪些訂閱有相關聯的所選的租用戶的 azure。 當您建立新的預先設定的方案時，您會看到可用的訂閱。

3.  最後，網站擷取全部標記為預先設定的解決方案的訂閱和資源群組中的資源，並填入 [首頁] 頁面中的磚。

下列各節說明控制存取權的角色預先設定的解決方案。

## <a name="aad-roles"></a>AAD 角色

AAD 角色控制能力預先設定的佈建解決方案，及管理使用者在預先設定的方案中。

您可以尋找管理員角色的相關詳細資訊中[指派管理員角色中 Azure AD]AAD[lnk-aad-admin]，但本文著重於**全域管理員**和為預先設定的解決方案所使用的**網域使用者成員**角色。

**全域管理員︰**可以有許多全域管理員，每個 AAD 租用戶。 當您建立 AAD 租用戶時，您會依預設該租用戶的全域管理員。 全域管理員可以佈建預先設定的方案，並指派的應用程式內其 AAD 租用戶系統**管理員**角色。 不過，如果在相同的 AAD 租用戶中的另一位使用者建立應用程式，全域管理員已授與的預設角色才**隱含閱讀**。 全域管理員可以指派的應用程式使用[Azure 傳統入口網站]角色[lnk-classic-portal]。

**網域使用者成員︰**可以有多個網域使用者/成員的每個 AAD 租用戶。 網域使用者可以佈建預先設定的方案，透過[azureiotsuite.com] [lnk-azureiotsuite]網站。 他們授與他們佈建應用程式的預設角色是**系統管理員**。 他們可以建立應用程式中的[azure iot-遠端-監視]使用 build.cmd 指令碼[lnk-rm-github-repo]或[azure iot-預測-維護][lnk-pm-github-repo]存放庫，但是他們授與的預設角色為**隱含唯讀**，並沒有指派角色的權限。 如果 AAD 租用戶中的另一位使用者建立應用程式，而被指派**隱含唯讀**角色依預設，該應用程式。 沒有可指派角色的應用程式。因此也無法新增使用者或應用程式使用者的角色即使他們佈建。

**來賓使用者訪客︰**可以有許多來賓使用者/來賓每個 AAD 租用戶。 來賓使用者 AAD 租用戶中有一組的權限。 如此一來，來賓使用者無法佈建 AAD 租用戶中預先設定的解決方案。

如需詳細資訊，請參閱下列資源︰

- [建立或編輯使用者 Azure AD 中][lnk-create-edit-users]
- [指派角色 AAD 應用程式][lnk-assign-app-roles]

## <a name="azure-subscription-administrator-roles"></a>Azure 訂閱的管理員角色

Azure 管理員角色控制對應的 Azure 訂閱 AD 租用戶的能力。

您可以瞭解更多關於[如何新增或變更 Azure 共同管理員、 服務管理員與帳戶管理員]，請參閱 Azure 共同管理員、 服務管理員與帳戶管理員角色[lnk-admin-roles]。

## <a name="application-roles"></a>應用程式角色

應用程式角色控制您預先設定的解決方案中裝置存取。

有兩種定義和定義您佈建預先設定的解決方案時所建立的應用程式中的其中一個隱含角色。

-   **系統管理員︰**具有 「 完全控制新增、 管理及移除裝置

-   **唯讀︰**若要檢視裝置的能力

-   **隱含讀取的僅︰**這是以唯讀模式，相同，但授與您 AAD 租用戶的所有使用者。 這麼做是為了方便開發期間。 您可以移除此角色修改[RolePermissions.cs] [lnk-resource-cs]來源檔案。

### <a name="changing-application-roles-for-a-user"></a>變更應用程式使用者角色

若要讓使用者在您的 Active Directory 中預先設定方案的系統管理員，您可以使用下列程序。

您必須是 AAD 全域管理員，才能變更使用者的角色︰

1. 移至 [ [Azure 傳統入口網站][lnk-classic-portal]。

2. 選取 [ **Active Directory**]。

3. 按一下您 AAD 的租用戶 （此為您佈建方案時選擇在 azureiotsuite.com 目錄） 的名稱。

4. 按一下 [**應用程式**]。

5. 按一下您預先設定的方案名稱相符的應用程式的名稱。 如果您沒有看到您的應用程式清單中，切換至**我的公司所擁有的應用程式****顯示**下拉式清單，然後按一下核取記號。

7. 按一下 [**使用者**]。

8. 選取您想要切換角色的使用者。

9. 按一下 [**指派**]，然後選取該角色 （例如**管理員**） 您想要指派給使用者，請按一下核取記號。

## <a name="faq"></a>常見問題集

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-aad-tenant-how-do-i-do-this"></a>我是服務管理員，並想要變更目錄和之間的對應訂閱特定 AAD 租用戶。 如何執行此？

1. 移至 [ [Azure 傳統入口網站][lnk-classic-portal]，按一下 [**設定**]，請在左側的服務] 清單中。

2. 選取您想要變更目錄對應的訂閱。

3. 按一下 [**編輯目錄**。

4. 選取您想要使用下拉式清單中的**目錄**。 按一下轉寄的箭號。

5. 確認目錄對應及受影響的共同管理員。 請注意，是否您從另一個目錄中移動，會移除原始的目錄中的所有共同管理員。

### <a name="im-a-domain-usermember-on-the-aad-tenant-and-ive-created-a-preconfigured-solution-how-do-i-get-assigned-a-role-for-my-application"></a>我的網域使用者/成員 AAD 租用戶，我已經建立預先設定的解決方案。 如何我收到的角色指派我的應用程式？

要求全域管理員才能將您指派為 AAD 租用戶，取得權限指派角色給使用者自己的全域管理員或全域管理員才能將您指派角色。 如果您想要變更的 AAD 租用戶預先設定的方案已部署至] 下，請參閱下一個問題。

### <a name="how-do-i-switch-the-aad-tenant-my-remote-monitoring-preconfigured-solution-and-application-are-assigned-to"></a>我要如何切換指派給我遠端監視預先設定的解決方案和應用程式的 AAD 租用戶？

您可以從<https://github.com/Azure/azure-iot-remote-monitoring>執行雲端部署，並重新部署新建立的 AAD 租用戶的。 由於您是預設為全域管理員，當您建立新的 AAD 租用戶時，您必須新增使用者並指派給這些使用者的角色的存取權。

1. [Azure 管理入口網站]中建立新的 AAD 目錄[lnk-classic-portal]。

2. 移至<https://github.com/Azure/azure-iot-remote-monitoring>。

3. 執行`build.cmd cloud [debug | release] {name of previously deployed remote monitoring solution}`(例如， `build.cmd cloud debug myRMSolution`)

4. 出現提示時，會設定為您新建立的租用戶，而不是您先前的租用戶**tenantid** 。


### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organisational-account"></a>我要變更的服務系統管理員或登入 organisational 帳戶時的共同管理員

請參閱支援文章︰[變更服務管理員和時使用 organisational 的帳戶登入的共同管理員][lnk-service-admins]。

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>為什麼會看見此錯誤？ 「 您的帳戶沒有適當的權限，可建立的解決方案。 請檢查您的帳戶管理員或嘗試使用不同的帳戶。 」

看看下圖︰

![][img-flowchart]

> [AZURE.NOTE] 您仍可看到的錯誤之後驗證您是否 AAD 租用戶的全域管理員和訂閱的共同管理員，您的帳戶管理員移除使用者，並重新指派此順序的必要權限︰ 全域管理員的身分新增使用者，然後將使用者新增為 Azure 訂閱的共同管理員。 如果問題持續發生，請連絡[說明及支援][lnk-help-support]。

**為什麼我有 Azure 訂閱時看到此錯誤？** *Azure 的訂閱，才能建立預先設定的解決方案。您可以建立的免費的試用帳戶在幾分鐘。*

如果您確定您有 Azure 訂閱，驗證您的訂閱將對應的租用戶，並確保已在下拉式清單中選取正確的租用戶。 如果您已驗證的所需的租用戶是否正確，請依照上述圖表並驗證您的訂閱，此 AAD 租用戶的對應。

## <a name="next-steps"></a>後續步驟

若要繼續學習如何 IoT 套件，請參閱如何[自訂預先設定的解決方案][lnk-customize]。

[img-flowchart]: media/iot-suite-permissions/flowchart.png

[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-rm-github-repo]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-pm-github-repo]: https://github.com/Azure/azure-iot-predictive-maintenance
[lnk-aad-admin]: https://azure.microsoft.com/documentation/articles/active-directory-assign-admin-roles/
[lnk-classic-portal]: https://manage.windowsazure.com/
[lnk-create-edit-users]: https://azure.microsoft.com/documentation/articles/active-directory-create-users/
[lnk-assign-app-roles]: https://azure.microsoft.com/documentation/articles/active-directory-application-manifest/
[lnk-service-admins]: https://azure.microsoft.com/support/changing-service-admin-and-co-admin/
[lnk-admin-roles]: https://azure.microsoft.com/documentation/articles/billing-add-change-azure-subscription-administrator/
[lnk-resource-cs]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/DeviceAdministration/Web/Security/RolePermissions.cs
[lnk-help-support]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
