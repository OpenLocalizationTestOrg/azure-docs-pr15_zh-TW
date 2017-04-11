<properties
    pageTitle="Azure AD Connect︰ 啟用裝置回寫 |Microsoft Azure"
    description="這份文件詳細說明如何啟用裝置回寫使用 Azure AD Connect"
    services="active-directory"
    documentationCenter=""
    authors="billmath"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"  
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/29/2016"
    ms.author="billmath"/>

# <a name="azure-ad-connect-enabling-device-writeback"></a>Azure AD Connect︰ 啟用裝置回寫

>[AZURE.NOTE] Azure AD Premium 的訂閱時需要裝置回寫。

下列文件提供如何啟用 Azure AD Connect 的裝置回寫功能相關資訊。 裝置回寫在下列情況使用︰

- 啟用裝置 ADFS 所根據的條件存取 (2012 R2 或更新版本) 受保護的應用程式 （依賴廠商信任）。

以下提供額外的安全性和存取應用程式只授與受信任的裝置的保證。 如需有關條件的存取權的詳細資訊，請參閱[管理風險條件存取](active-directory-conditional-access.md)和[使用 Azure Active Directory 裝置登錄的內部部署條件存取的設定](https://msdn.microsoft.com/library/azure/dn788908.aspx)。

>[AZURE.IMPORTANT]
<li>裝置必須位於相同的使用者樹系。 由於裝置必須回寫到單一樹系，此功能目前不支援多個使用者樹系的部署。</li>
<li>只有一個裝置註冊設定物件可以新增到內部部署的 Active Directory 樹系。 此功能不會以多個 Azure AD 目錄同步處理內部部署的 Active Directory 的拓撲與相容。</li>

## <a name="part-1-install-azure-ad-connect"></a>第 1 部分︰ 安裝 Azure AD 連線
1. 安裝使用自訂的 Azure AD Connect 或快速設定。 Microsoft 建議起始所有使用者和群組成功同步都處理您啟用裝置回寫之前。

## <a name="part-2-prepare-active-directory"></a>第 2 部分︰ 準備 Active Directory
若要使用的裝置回寫準備使用下列步驟。

1.  從 Azure AD Connect 安裝所在的電腦，請在提高權限模式中啟動 PowerShell。

2.  如果沒有安裝 Active Directory PowerShell 模組，請安裝並使用下列命令︰

    `Install-WindowsFeature –Name AD-Domain-Services –IncludeManagementTools`

3. 如果沒有安裝 Azure Active Directory PowerShell 模組，然後從下載並安裝該[Azure Active Directory 的 Windows PowerShell 模組 （64 位元版本）](http://go.microsoft.com/fwlink/p/?linkid=236297)。 此元件具有登入小幫手，這與 Azure AD Connect 安裝相依性。

4.  企業版的管理員認證，請執行下列命令，然後結束 PowerShell。

    `Import-Module 'C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1'`

    `Initialize-ADSyncDeviceWriteback {Optional:–DomainName [name] Optional:-AdConnectorAccount [account]}`

企業版的管理員認證必，因為需要變更設定命名空間。 網域管理員不會有足夠的權限。

![啟用裝置回寫的 Powershell](./media/active-directory-aadconnect-feature-device-writeback/powershell.png)

描述︰

- 如果他們已不存在，會建立並設定新的容器和下 CN 物件 = 裝置登錄設定，CN = 服務 CN = 設定，[樹系 dn]。
- 如果他們已不存在，會建立並設定新的容器和下 CN 物件 = RegisteredDevices，[網域 dn]。 在這個容器中，將會建立裝置物件。
- Azure AD 連接器帳戶，來管理您的 Active Directory 裝置上設定必要的權限。
- 只需要執行一個樹系，即使 Azure AD Connect 安裝在多個樹系。

參數︰

- 網域名稱︰ Active Directory 網域建立裝置物件的位置。 附註︰ 指定的 Active Directory 樹系的所有裝置將會都建立一個網域中。
- 將會用於 Azure AD Connect 來管理目錄中的物件的 AdConnectorAccount: Active Directory 帳戶。 這是用來連線到 AD Azure AD Connect 同步處理的帳戶。 如果您安裝使用快速設定，則加上 MSOL_ 的帳戶。

## <a name="part-3-enable-device-writeback-in-azure-ad-connect"></a>第 3 部分︰ 啟用裝置回寫 Azure AD Connect
若要啟用裝置回寫 Azure AD Connect 中的使用下列程序。

1.  再次執行安裝精靈。 從 [其他工作] 頁面中選取 [**自訂同步處理] 選項**，然後按一下 [**下一步**]。
![自訂安裝自訂同步處理選項](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback2.png)
2.  在 [選擇性功能] 頁面裝置回寫不再會呈現灰色。 請注意，如果 Azure AD Connect 準備步驟未完成的裝置回寫會縮小呈現選用的功能] 頁面中。 檢查裝置回寫的方塊，然後按一下 [**下一步**]。 如果仍已停用] 核取方塊，請參閱[疑難排解] 區段](#the-writeback-checkbox-is-still-disabled)。
![自訂安裝裝置回寫選用的功能](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback3.png)
3.  在回寫頁面上，您會看到所提供的網域為預設裝置回寫樹系。
![自訂安裝裝置回寫目標樹系](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback4.png)
4.  完成安裝，而不變更其他設定精靈。 如果有需要請參閱[Azure AD Connect 自訂安裝。](./connect/active-directory-aadconnect-get-started-custom.md)

## <a name="enable-conditional-access"></a>啟用條件的存取
若要啟用這種情況的詳細的指示，可內[使用 Azure Active Directory 裝置登錄的內部部署條件存取的設定](https://msdn.microsoft.com/library/azure/dn788908.aspx)。

## <a name="verify-devices-are-synchronized-to-active-directory"></a>請確認裝置同步處理到 Active Directory
裝置回寫現在應該正常運作。 請注意，可能需要 3 小時的時間要撰寫返回 AD 裝置物件。  若要驗證您的裝置正確地同步，執行下列動作後完成同步處理規則︰

1.  啟動 [Active Directory 系統管理中心]。
2.  展開 RegisteredDevices，正在同盟網域中。
![Active Directory 系統管理中心註冊裝置](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback5.png)
3.  會顯示目前已註冊的裝置。
![Active Directory 系統管理中心登錄的裝置] 清單](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback6.png)

## <a name="troubleshooting"></a>疑難排解

### <a name="the-writeback-checkbox-is-still-disabled"></a>仍會停用回寫] 核取方塊
如果未啟用裝置回寫的核取方塊，即使您遵循上述步驟，下列步驟會引導您在安裝精靈會驗證] 方塊中已啟用之前。

第一個項目第一個︰

- 請確定至少有一個樹系 Windows Server 2012R2。 您必須有裝置物件類型。
- 如果已在執行 [安裝] 精靈，然後的任何變更會不會偵測。 在此情況下，完成安裝精靈]，再執行一次。
- 請確定在初始化指令碼中所提供的帳戶是正確的使用者使用 Active Directory 連接器。 若要檢查，請遵循下列步驟︰
    - 從 [開始] 功能表中，開啟 [**同步處理服務**]。
    - 開啟 [**連接器**] 索引標籤。
    - 使用 Active Directory 網域服務類型找到連接器並加以選取。
    - 在 [**動作**] 底下，選取 [**屬性**]。
    - 移至**連線到 Active Directory 樹系**。 請確認網域和使用者名稱指定此畫面相符項目上的指令碼提供的帳戶。
![連接器帳戶中同步處理服務管理員](./media/active-directory-aadconnect-feature-device-writeback/connectoraccount.png)

驗證 Active Directory 中的設定︰
- 請確認裝置註冊服務位於下列位置 (CN = DeviceRegistrationService，CN = 裝置註冊服務 CN = 裝置登錄設定，CN = 服務 CN = 設定) 下設定命名內容。

![疑難排解，請在設定的命名空間 DeviceRegistrationService](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot1.png)

- 確認有是只有一個設定物件搜尋設定命名空間。 如果有多個，請刪除重複的。

![疑難排解、 搜尋重複的物件](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot2.png)

- 在裝置註冊服務物件，請確定屬性 msDS DeviceLocation 且的值。 查閱此位置，請確定是使用 Save msDS DeviceContainer 進行簡報。

![疑難排解，msDS DeviceLocation](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot3.png)

![疑難排解，RegisteredDevices 物件類別](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot4.png)

- 驗證 Active Directory 連接器所使用的帳戶上一個步驟所找到的註冊裝置容器具有必要的權限。 這是預期的權限，此容器上︰

![疑難排解，請確認在容器上的權限](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot5.png)

- 驗證 Active Directory 帳戶擁有權限 CN = 裝置登錄設定，CN = 服務 CN = 設定物件。

![疑難排解，請確認裝置註冊設定的權限](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot6.png)

## <a name="additional-information"></a>其他資訊
- [管理風險條件的存取](active-directory-conditional-access.md)
- [設定使用 Azure Active Directory 裝置註冊內部部署條件存取權限](https://msdn.microsoft.com/library/azure/dn788908.aspx)

## <a name="next-steps"></a>後續步驟
進一步瞭解[整合您的內部部署識別與 Azure Active Directory](active-directory-aadconnect.md)。
