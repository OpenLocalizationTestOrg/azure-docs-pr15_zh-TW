<properties
    pageTitle="如何產生和 Azure 金鑰保存庫的轉接 HSM 受保護的按鍵 |Microsoft Azure"
    description="使用本文可協助您規劃、 產生，並且轉接自己 HSM 受保護的鍵，以使用 Azure 金鑰保存庫。"
    services="key-vault"
    documentationCenter=""
    authors="cabailey"
    manager="mbaldwin"
    tags="azure-resource-manager"/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/24/2016"
    ms.author="cabailey"/>
#<a name="how-to-generate-and-transfer-hsm-protected-keys-for-azure-key-vault"></a>如何產生和傳輸 HSM 保護金鑰 Azure 金鑰保存庫

##<a name="introduction"></a>簡介

新增保證，當您使用 Azure 金鑰保存庫，您可以匯入或永遠不會讓 HSM 邊界的硬體安全性模組 (Hsm) 中會產生金鑰。 這種情況通常稱為*將您自己的金鑰*，或 BYOK。 Hsm 是的 FIPS 140-2 層級 2 驗證。 Azure 鍵保存庫使用 Hsm Thales nShield 系列保護索引鍵。

使用本主題中的資訊可協助您規劃、 產生，並且轉接自己 HSM 受保護的鍵，以使用 Azure 金鑰保存庫。

此功能不適用於 Azure china （中國）。 

>[AZURE.NOTE] 如需有關 Azure 金鑰保存庫的詳細資訊，請參閱[什麼是 Azure 金鑰保存庫？](key-vault-whatis.md)  
>
>取得使用教學課程中，包括建立 HSM 受保護的按鍵的按鍵保存庫，請參閱[開始使用 Azure 金鑰保存庫](key-vault-get-started.md)。

深入瞭解產生，並透過網際網路傳送 HSM 受保護的索引鍵的詳細資訊︰

- 您從離線工作站，減少攻擊產生金鑰。

- 使用金鑰 Exchange 鍵 (KEK)，其中保持加密，直到其轉移到 Azure 金鑰保存庫 Hsm 加密金鑰。 您的金鑰加密的版本保留原始工作站。

- 工具組設定您的租用戶金鑰，以便您金鑰繫結至 Azure 金鑰保存庫安全性世界各地的屬性。 因此 Azure 金鑰保存庫 Hsm 接收並解密金鑰之後，只有這些 Hsm 可以使用它。 無法匯出您的金鑰。 此繫結會強制執行由 Thales Hsm。

- 索引鍵 Exchange 鍵 (KEK)，用來加密金鑰會產生 Azure 金鑰保存庫 Hsm 內，而且不會匯出。 Hsm 強制可外 Hsm KEK 無清除版本。 此外，工具組包含從 Thales KEK 不能匯出，並產生內 Thales 製造正版 HSM 審查。

- 工具組包含從 Azure 金鑰保存庫安全性世界上 Thales 所製造正版 HSM 也產生 Thales 審查。 此審查證明您的 Microsoft 使用的真正的硬體。

- Microsoft 會使用不同的 KEKs 和分隔每個地理區域中的安全性世界。 這種分隔可確保您的金鑰可用只在其加密地區資料中心。 例如，從歐洲客戶鍵無法使用北美地區或亞洲地區資料中心。

##<a name="more-information-about-thales-hsms-and-microsoft-services"></a>Thales Hsm 與 Microsoft 服務的相關詳細資訊

Thales e 安全性是資料加密和網路安全性解決方案的財務服務、 高技術、 製造、 政府版和技術︰ 類股的前置全域提供者。 使用 40 年追蹤記錄保護企業及政府資訊，Thales 解決方案會使用五個最大能源和太空公司中的四筆。 解決方案 22 北大西洋國家/地區，也會使用，及保護多個 80 每西非中部的全球付款交易。

Microsoft 具有共用與 Thales 來強化 Hsm 狀態的圖案。 這些增強功能可讓您取得裝載的服務，而不需索引鍵理由才能放棄控制一般優點。 具體來說，這些增強功能可讓 Microsoft 管理 Hsm，讓您不需要。 為雲端服務，Azure 金鑰保存庫縮放比例，以符合您的組織使用尖峰簡短的通知。 同時，您的金鑰受到保護 Microsoft 的 Hsm 內︰ 您保留金鑰生命週期的控制權，因為您產生索引鍵，並傳送到 Microsoft 的 Hsm。

##<a name="implementing-bring-your-own-key-byok-for-azure-key-vault"></a>實作的 Azure 金鑰保存庫顯示您自己的金鑰 (BYOK)

使用下列資訊與程序，如果您將會產生自己 HSM 受保護的金鑰，然後將它傳送到 Azure 金鑰保存庫 — 提到您自己的鍵 (BYOK) 案例。


##<a name="prerequisites-for-byok"></a>BYOK 的先決條件

請參閱下表的必要條件清單的 Azure 金鑰保存庫顯示您自己的金鑰 (BYOK)。

|要求|詳細資訊|
|---|---|
|Azure 訂閱|若要建立 Azure 金鑰保存庫，您需要的 Azure 訂閱︰[註冊免費試用版](https://azure.microsoft.com/pricing/free-trial/)|
|支援 HSM 受保護的按鍵 Azure 金鑰保存庫進階版服務層級|如需 Azure 金鑰保存庫服務層和功能的詳細資訊，請參閱[Azure 金鑰保存庫價格](https://azure.microsoft.com/pricing/details/key-vault/)網站。|
|Thales HSM 與智慧卡，支援軟體|您必須存取 Thales 硬體安全性模組以及 Thales Hsm 基本操作知識。 相容的模型，或如果您的確具備購買 HSM 清單，請參閱[Thales 硬體安全性模組](https://www.thales-esecurity.com/msrms/buy)。|
|下列的硬體與軟體︰<ol><li>離線的 x64 工作站與最小的 Windows 作業系統的最小的 Windows 7 和 Thales nShield 軟體版本 11.50。<br/><br/>如果這個工作站執行 Windows 7 中，您必須[安裝 Microsoft.NET Framework 4.5](http://download.microsoft.com/download/b/a/4/ba4a7e71-2906-4b2d-a0e1-80cf16844f5f/dotnetfx45_full_x86_x64.exe)。</li><li>工作站連線至網際網路，並有 Windows 7 的最小 Windows 作業系統。</li><li>USB 磁碟機或其他可攜式存放裝置已至少 16 MB 的可用空間。</li></ol>|基於安全性理由，我們建議您的第一個工作站未連接至網路。 不過，此建議是不以程式設計方式執行。<br/><br/>請注意，請依照下列指示，在這個工作站稱為中斷連線工作站。</p></blockquote><br/>此外，如果您的租用戶索引鍵是生產網路，我們建議您使用的第二個、 個別工作站下載工具組，然後上傳的租用戶鍵。 但供測試之用，您可以使用相同的工作站作為第一個。<br/><br/>請注意，請依照下列指示，在第二個工作站就是連線到網際網路的工作站。</p></blockquote><br/>|

##<a name="generate-and-transfer-your-key-to-azure-key-vault-hsm"></a>產生和 Azure 金鑰保存庫 HSM 傳輸金鑰

您會使用下列五個步驟，來產生和 Azure 金鑰保存庫 HSM 傳輸金鑰︰

- [步驟 1︰ 準備您的網際網路連線工作站](#step-1-prepare-your-internet-connected-workstation)
- [步驟 2︰ 準備中斷連線的工作站](#step-2-prepare-your-disconnected-workstation)
- [步驟 3︰ 產生金鑰](#step-3-generate-your-key)
- [步驟 4︰ 準備您傳送的金鑰。](#step-4-prepare-your-key-for-transfer)
- [步驟 5︰ 將您的金鑰轉移到 Azure 金鑰保存庫](#step-5-transfer-your-key-to-azure-key-vault)

## <a name="step-1-prepare-your-internet-connected-workstation"></a>步驟 1︰ 準備您的網際網路連線工作站
第一個步驟中，請執行下列程序，在您連線到網際網路的工作站上。


###<a name="step-11-install-azure-powershell"></a>安裝 Azure PowerShell 以步驟 1.1 （英文）︰

從網際網路連線工作站下載並安裝 Azure PowerShell 模組包含 cmdlet 來管理 Azure 金鑰保存庫。 需要 0.8.13 的版本。

如需安裝指示，請參閱[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md)。

###<a name="step-12-get-your-azure-subscription-id"></a>步驟 1.2︰ 取得您 Azure 訂閱識別碼

啟動 Azure PowerShell 工作階段，並登入您的 Azure 帳戶使用下列命令︰

        Add-AzureAccount
在快顯的瀏覽器視窗中，輸入您的 Azure 帳戶使用者名稱和密碼。 然後，使用 [[取得 AzureSubscription](https://msdn.microsoft.com/library/azure/dn790366.aspx)命令︰

        Get-AzureSubscription
從輸出中找出您將會用於 Azure 金鑰保存庫的訂閱的識別碼。 您稍後需要此訂閱 ID。

不關閉 PowerShell 的 Azure 視窗。

###<a name="step-13-download-the-byok-toolset-for-azure-key-vault"></a>步驟 1.3︰ 下載適用於 Azure 金鑰保存庫 BYOK 工具組

前往 Microsoft 下載中心並為您的地理區域或 Azure 執行個體的 [[下載 Azure 金鑰保存庫 BYOK 工具組](http://www.microsoft.com/download/details.aspx?id=45345)。 若要識別下載和其對應的 sha-256 套件雜湊套件名稱，請使用下列資訊︰

---

**北美地區︰**

KeyVault-BYOK-工具-United States.zip

305F44A78FEB750D1D478F6A0C345B097CD5551003302FA465C73D9497AB4A03

---

**歐洲︰**

KeyVault BYOK 工具-Europe.zip

C73BB0628B91471CA7F9ADFCE247561C6016A5103EF1A315D49C3EA23AFC0B9C

---

**亞洲︰**

KeyVault BYOK 工具-AsiaPacific.zip

BE9A84B6C76661929F9FDAD627005D892B3B8F9F19F351220BB4F9C356694192

---

**拉丁美洲︰**

KeyVault BYOK 工具-LatinAmerica.zip
    
9E8EE11972DECE8F05CD898AF64C070C375B387CED716FDCB788544AE27D3D23

---

**日本︰**

KeyVault BYOK 工具-Japan.zip

E6B88C111D972A02ABA3325F8969C4E36FD7565C467E9D7107635E3DDA11A8B2

---

**澳大利亞︰**

KeyVault BYOK 工具-Australia.zip

7660D7A675506737857B14F527232BE51DC269746590A4E5AB7D50EDD220675D

---

[**Azure 政府版︰**](https://azure.microsoft.com/features/gov/)

KeyVault BYOK 工具-USGovCloud.zip

53801A3043B0F8B4A50E8DC01A935C2BFE61F94EE027445B65C52C1ACC2B5E80

---

**加拿大︰**

KeyVault BYOK 工具-Canada.zip

A42D9407B490E97693F8A5FA6B60DC1B06B1D1516EDAE7C9A71AA13E12CF1345

---

**德國︰**

KeyVault BYOK 工具-Germany.zip

4795DA855E027B2CA8A2FF1E7AE6F03F772836C7255AFC68E576410BDD28B48E

---
**印度︰**

KeyVault BYOK 工具-India.zip

26853511EB767A33CF6CD880E78588E9BBE04E619B17FBC77A6B00A5111E800C

---

若要驗證您下載 BYOK 工具組，從您的 PowerShell 的 Azure 工作階段的完整性使用[取得 FileHash](https://technet.microsoft.com/library/dn520872.aspx)指令程式。

    Get-FileHash KeyVault-BYOK-Tools-*.zip

工具組包括下列各項︰

- 含有名稱開頭為的金鑰 Exchange 鍵 (KEK) 套件**BYOK-KEK-版本-。**
- 含有名稱開頭為的安全性世界套件**BYOK-SecurityWorld-版本-。**
- 名為 v python 指令碼**erifykeypackage.py。**
- 命令列可執行檔檔案命名的**KeyTransferRemote.exe**及相關聯的 Dll。
- Visual c + + 可轉散發套件，名為**vcredist_x64.exe。**

封裝複製到 USB 磁碟機或其他可攜式的儲存空間。

##<a name="step-2-prepare-your-disconnected-workstation"></a>步驟 2︰ 準備中斷連線的工作站

第二個步驟中，請執行下列程序未連接至網路 （網際網路或內部網路） 工作站上。


###<a name="step-21-prepare-the-disconnected-workstation-with-thales-hsm"></a>步驟 2.1︰ 準備中斷連線與 Thales HSM 工作站

在 Windows 電腦上安裝 nCipher (Thales) 支援軟體，然後附加 Thales HSM 至該電腦。

確定您的 path （**%nfast_home%\bin**和**%nfast_home%\python\bin**） 中 Thales 工具。 例如，輸入以下資料︰

        set PATH=%PATH%;”%nfast_home%\bin”;”%nfast_home%\python\bin”

如需詳細資訊，請參閱 Thales HSM 隨附的使用者指南。

###<a name="step-22-install-the-byok-toolset-on-the-disconnected-workstation"></a>步驟 2.2︰ 中斷連線工作站上安裝 BYOK 工具組

USB 磁碟機或其他可攜式的儲存空間，複製 BYOK 工具組套件，然後執行下列動作︰

1. 從套件解壓縮檔案下載到的任何資料夾。
2. 從該資料夾中，執行 vcredist_x64.exe。
3. 依照指示安裝 Visual c + + 執行階段元件 Visual Studio 2013。

##<a name="step-3-generate-your-key"></a>步驟 3︰ 產生金鑰

第三個步驟中，請執行下列程序中斷連線工作站上。

###<a name="step-31-create-a-security-world"></a>步驟 3.1︰ 建立一個安全性世界

啟動命令提示字元，並執行 Thales 新全球程式。

    new-world.exe --initialize --cipher-suite=DLf1024s160mRijndael --module=1 --acs-quorum=2/3

此程式建立的**安全性世界**檔案 %nfast_kmdata%\local\world，對應到 C:\ProgramData\nCipher\Key 管理 Data\local 資料夾。 您可以使用不同的值，仲裁，但在我們的範例，系統會提示您輸入的每一個三個空白卡片和 pin。 然後，任何兩個卡完整存取權授予安全性世界。 這些卡片會成為**管理員卡片設定**新的安全性世界。

然後執行下列動作︰

- 備份全球檔案。 安全及保護全球檔案、 系統管理員的卡片及他們的 pin，請確定的單一人員都有一個以上的卡的存取。

###<a name="step-32-validate-the-downloaded-package"></a>步驟 3.2 捨位︰ 驗證下載的套件

此為選用步驟但建議使用，讓您可以驗證下列動作︰

- 從 genuine 的 Thales HSM 已經產生所包含的工具組索引鍵 Exchange 鍵。
- 安全性全球工具組中所包含的雜湊已經產生正版 Thales HSM 中。
- 索引鍵 Exchange 鍵可非匯出。

>[AZURE.NOTE]若要驗證下載的套件，HSM 必須連線，電源已開啟，且必須在其上一個安全性世界 （例如您剛剛建立的項目）。

驗證下載的套件︰

1.  將下列一個動作，根據您的地理區域或 Azure 執行個體來執行 verifykeypackage.py 指令碼︰
    - 於北美︰

            python verifykeypackage.py -k BYOK-KEK-pkg-NA-1 -w BYOK-SecurityWorld-pkg-NA-1
    - 在 [歐洲︰

            python verifykeypackage.py -k BYOK-KEK-pkg-EU-1 -w BYOK-SecurityWorld-pkg-EU-1
    - 針對亞洲︰

            python verifykeypackage.py -k BYOK-KEK-pkg-AP-1 -w BYOK-SecurityWorld-pkg-AP-1
    - 針對拉丁美洲︰

            python verifykeypackage.py -k BYOK-KEK-pkg-LATAM-1 -w BYOK-SecurityWorld-pkg-LATAM-1
    - 針對日本︰

            python verifykeypackage.py -k BYOK-KEK-pkg-JPN-1 -w BYOK-SecurityWorld-pkg-JPN-1
    - 澳大利亞︰

            python verifykeypackage.py -k BYOK-KEK-pkg-AUS-1 -w BYOK-SecurityWorld-pkg-AUS-1
    - [Azure 政府版](https://azure.microsoft.com/features/gov/)，其中會使用 Azure 的美國政府執行個體︰

            python verifykeypackage.py -k BYOK-KEK-pkg-USGOV-1 -w BYOK-SecurityWorld-pkg-USGOV-1
    - 加拿大︰

            python verifykeypackage.py -k BYOK-KEK-pkg-CANADA-1 -w BYOK-SecurityWorld-pkg-CANADA-1
    - 德國︰

            python verifykeypackage.py -k BYOK-KEK-pkg-GERMANY-1 -w BYOK-SecurityWorld-pkg-GERMANY-1
    - 針對印度︰

            python verifykeypackage.py -k BYOK-KEK-pkg-INDIA-1 -w BYOK-SecurityWorld-pkg-INDIA-1
    >[AZURE.TIP]Thales 軟體包含在 %NFAST_HOME%\python\bin python

2.  確認您看到下列，亦即指出驗證成功︰**結果︰ 成功**

這個指令碼會驗證簽章者鏈結到 Thales 根鍵。 此根機碼雜湊內嵌指令碼中，而且其值應該是**59178a47 de508c3f 291277ee 184f46c4 f1d9c639**。 您也可以瀏覽[Thales 網站](http://www.thalesesec.com/)分別確認此值。

現在，您準備好要建立新的金鑰。

###<a name="step-33-create-a-new-key"></a>步驟 3.3︰ 建立新的金鑰

使用 Thales **generatekey**程式產生金鑰。

執行下列命令以產生金鑰︰

    generatekey --generate simple type=RSA size=2048 protect=module ident=contosokey plainname=contosokey nvram=no pubexp=

當您執行此命令時，使用這些指示︰

- *保護*參數必須設定為 [**模組**值，如下圖所示。 這樣會建立模組受保護的金鑰。 BYOK 工具組不支援 OCS 受保護的金鑰。

- 使用任何字串值取代*contosokey* **ident**和**plainname**的值。 若要管理的額外費用最小化，並減少錯誤的風險，我們建議您都使用相同的值。 **Ident**值必須包含數字、 虛線和小寫字母。

- Pubexp 留空白 （預設） 在此範例中，但您可以指定特定的值。 如需詳細資訊，請參閱 Thales 文件。

這個命令 %NFAST_KMDATA%\local] 資料夾中建立 token 化金鑰檔案，以開始**key_simple_**，後面接著**ident**命令中指定的名稱。 例如︰ **key_simple_contosokey**。 這個檔案包含加密的金鑰。

備份此 token 化金鑰檔案，在安全的位置。

>[AZURE.IMPORTANT] 當您稍後將您的金鑰傳輸至 Azure 金鑰保存庫時，則 Microsoft 無法在使其變成相當重要，先備份您的索引鍵和安全性全球安全地匯出此按鍵給您。 連絡 Thales 指導和備份您的金鑰的最佳作法。

您已準備好傳送至 Azure 金鑰保存庫的 [您的金鑰。

##<a name="step-4-prepare-your-key-for-transfer"></a>步驟 4︰ 準備您傳送的金鑰。

第四個步驟中，請執行下列程序中斷連線工作站上。

###<a name="step-41-create-a-copy-of-your-key-with-reduced-permissions"></a>步驟 4.1︰ 建立您的金鑰複本有精簡的權限

若要降低上您的金鑰的權限的命令提示字元中，執行下列一個動作，根據您的地理區域或 Azure 執行個體︰

- 於北美︰

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1
- 在 [歐洲︰

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1
- 針對亞洲︰

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1
- 針對拉丁美洲︰

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1
- 針對日本︰

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1
- 澳大利亞︰

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1
- [Azure 政府版](https://azure.microsoft.com/features/gov/)，其中會使用 Azure 的美國政府執行個體︰

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1
- 加拿大︰

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1
- 德國︰

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1
- 針對印度︰

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1


當您執行此命令時，以在所指定的值相同來取代*contosokey* **步驟 3.3︰ 建立新的金鑰**的[產生金鑰](#step-3-generate-your-key)步驟。

系統會要求您插入安全性全球系統卡片。

命令完成時，您看到**結果︰ 成功**命名 key_xferacId_ 檔案內容的權限與金鑰複本且<contosokey>。

###<a name="step-42-inspect-the-new-copy-of-the-key"></a>步驟 4.2︰ 檢查新的金鑰複本

您也可以執行 Thales 公用程式，以確認 [最小的權限，新的機碼︰

- aclprint.py:

        "%nfast_home%\bin\preload.exe" -m 1 -A xferacld -K contosokey "%nfast_home%\python\bin\python" "%nfast_home%\python\examples\aclprint.py"
- kmfile-dump.exe:

        "%nfast_home%\bin\kmfile-dump.exe" "%NFAST_KMDATA%\local\key_xferacld_contosokey"
當您執行下列命令時，則將 contosokey 取代在所指定的值相同**步驟 3.3︰ 建立新的金鑰**的[產生金鑰](#step-3-generate-your-key)步驟。

###<a name="step-43-encrypt-your-key-by-using-microsofts-key-exchange-key"></a>步驟 4.3︰ 使用 Microsoft 的金鑰 Exchange 金鑰加密金鑰

執行下列命令，根據您的地理區域或 Azure 執行個體其中一項︰

- 於北美︰

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- 在 [歐洲︰

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- 針對亞洲︰

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- 針對拉丁美洲︰

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- 針對日本︰

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- 澳大利亞︰

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- [Azure 政府版](https://azure.microsoft.com/features/gov/)，其中會使用 Azure 的美國政府執行個體︰

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- 加拿大︰

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- 德國︰

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- 針對印度︰

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey


當您執行此命令時，使用這些指示︰

- 取代您用來產生的索引鍵的識別項*contosokey* **步驟 3.3︰ 建立新的金鑰**的[產生金鑰](#step-3-generate-your-key)步驟。

- 取代*SubscriptionID* Azure 訂閱包含您的主要保存庫的識別碼。 擷取此值之前，在**步驟 1.2︰ 取得您 Azure 訂閱 ID**的[準備您的網際網路連線工作站](#step-1-prepare-your-internet-connected-workstation)步驟。

- 取代*ContosoFirstHSMKey*標籤所用您的輸出檔案名稱。

當此順利完成時，它會顯示**結果︰ 成功**且包含下列名稱的目前資料夾中新的檔案︰ TransferPackage-*ContosoFirstHSMkey*.byok

###<a name="step-44-copy-your-key-transfer-package-to-the-internet-connected-workstation"></a>步驟 4.4︰ 連線到網際網路的工作站複製您的主要傳輸套件

使用 USB 磁碟機或其他可攜式儲存輸出檔案複製到您的網際網路連線工作站的上一個步驟 (KeyTransferPackage ContosoFirstHSMkey.byok)。

##<a name="step-5-transfer-your-key-to-azure-key-vault"></a>步驟 5︰ 將您的金鑰轉移到 Azure 金鑰保存庫

最後一個步驟中，在連線到網際網路的工作站上，使用[新增 AzureKeyVaultKey](https://msdn.microsoft.com/library/azure/dn868048\(v=azure.300\).aspx) cmdlet 來上傳您從中斷連線工作站複製到 Azure 金鑰保存庫 HSM 金鑰傳輸套件︰

    Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMkey' -KeyFilePath 'c:\TransferPackage-ContosoFirstHSMkey.byok' -Destination 'HSM'

如果上傳成功，您就會看到顯示您剛才新增的索引鍵的屬性。


##<a name="next-steps"></a>後續步驟

您現在可以在您的主要保存庫中使用此 HSM 受保護的按鍵。 如需詳細資訊，請參閱[快速入門 Azure 金鑰保存庫](key-vault-get-started.md)教學課程中的 [**如果您想要使用硬體安全性模組 (HSM)** ] 區段。
