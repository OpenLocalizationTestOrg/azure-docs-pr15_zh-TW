<properties
   pageTitle="紅色的角色更新基礎結構 (RHUI) |Microsoft Azure"
   description="視需要 Microsoft Azure 中的紅色角色企業 Linux 執行個體瞭解有關紅色角色更新基礎結構 (RHUI)"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="BorisB2015"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="10/14/2016"
   ms.author="borisb"/>

# <a name="red-hat-update-infrastructure-rhui-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>視需要紅色角色企業 Linux Vm Azure 中的紅色角色更新基礎結構 (RHUI)

從 Azure Marketplace 中可用的視紅色角色企業 Linux (RHEL) 影像建立虛擬機器註冊存取紅色角色更新基礎結構 (RHUI) Azure 中部署。  視需要 RHEL 執行個體有權存取，以地區 yum 存放庫，並且可以接收累加更新。

Yum 存放庫清單中，這由 RHUI 管理，被設定期間佈建 RHEL 執行個體。 您不需要進行任何其他設定-執行`yum update`RHEL 執行個體準備要最新的更新後。

> [AZURE.NOTE] Azure RHUI 基礎結構已最近更新 (年 9 月 2016)，並且不會中斷存取 Azure RHUI 需要您現有的 RHEL 執行個體的設定所做的變更。 請參閱 RHUI Azure 基礎結構更新章節，如需詳細資訊。


## <a name="rhui-azure-infrastructure-update"></a>RHUI Azure 基礎結構更新
年 9 月 2016 Azure 會有一組新的紅色角色更新基礎結構 (RHUI) 伺服器。 這些伺服器部署與[Azure 流量管理員]( https://azure.microsoft.com/services/traffic-manager/)以便單一端點 (rhui 1.micrsoft.com) 可讓任何 VM 無論區域。 這些程式也使用 SSL 憑證鏈結知名的憑證授權單位 （巴爾的摩根） 到的。 進行此更新自動應危險的部分的客戶 Acl 或自訂路由 RHUI 更新伺服器資料表，因此此更新 「 參加。 」 這些新伺服器的登入的手動步驟，可在此頁面，並完成登入以自動的方式 （在個別的步驟驗證） 的指令碼。 新 RHEL PAYG 圖像中 Azure Marketplace （版本年 9 月 2016年或更新版本） 會自動指向新 Azure RHUI 伺服器並不需要任何其他動作。

### <a name="the-new-azure-rhui-infrastructure-onboarding-timeline"></a>新的 Azure RHUI 基礎結構登入時間表

| 日期 | 附註 |
| --- | --- |
|2016 年 9 月 22日日 | RHUI 伺服器，且可供使用的安裝指示。 使用 [新 (年 9 月 2016) 部署 Vm RHEL PAYG 服務商場圖像會自動使用新的 RHUI 伺服器，但現有 Vm 「 參加 」
|2016 年 11 月 1日日 | 舊版 RHEL PAYG VM 圖像 （其中使用舊的 Azure RHUI 伺服器） 會從 Azure Marketplace] 庫
|2017 年 1 月 16 日 | 會解除舊 Azure RHUI 伺服器。 若要維持 Azure RHUI 存取此時間來更新所有您受影響的 PAYG RHEL Vm

### <a name="the-ips-for-the-new-rhui-content-delivery-servers-are"></a>針對新的 RHUI 內容傳遞伺服器 IPs 是

```
# Azure Global
13.91.47.76
40.85.190.91
52.187.75.218
52.174.163.213

# Azure US Government
13.72.186.193
```

### <a name="manual-update-procedure-to-use-the-new-azure-rhui-servers"></a>手動更新程序，以使用新的 Azure RHUI 伺服器

下載 （透過捲曲） 公用金鑰簽章

```
curl -o RPM-GPG-KEY-microsoft-azure-release https://download.microsoft.com/download/9/D/9/9d945f05-541d-494f-9977-289b3ce8e774/microsoft-sign-public.asc 
```

確認已下載的鍵

```
gpg --list-packets --verbose < RPM-GPG-KEY-microsoft-azure-release
```

檢查輸出，驗證`keyid`和`user ID packet`:

```
Version: GnuPG v1.4.7 (GNU/Linux)
:public key packet:
        version 4, algo 1, created 1446074508, expires 0
        pkey[0]: [2048 bits]
        pkey[1]: [17 bits]
        keyid: EB3E94ADBE1229CF
:user ID packet: "Microsoft (Release signing) <gpgsecurity@microsoft.com>"
:signature packet: algo 1, keyid EB3E94ADBE1229CF
        version 4, created 1446074508, md5len 0, sigclass 0x13
        digest algo 2, begin of digest 1a 9b
        hashed subpkt 2 len 4 (sig created 2015-10-28)
        hashed subpkt 27 len 1 (key flags: 03)
        hashed subpkt 11 len 5 (pref-sym-algos: 9 8 7 3 2)
        hashed subpkt 21 len 3 (pref-hash-algos: 2 8 3)
        hashed subpkt 22 len 2 (pref-zip-algos: 2 1)
        hashed subpkt 30 len 1 (features: 01)
        hashed subpkt 23 len 1 (key server preferences: 80)
        subpkt 16 len 8 (issuer key ID EB3E94ADBE1229CF)
        data: [2047 bits]
```

安裝公開金鑰

```
sudo install -o root -g root -m 644 RPM-GPG-KEY-microsoft-azure-release /etc/pki/rpm-gpg
sudo rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-microsoft-azure-release
```

下載、 驗證]，並安裝用戶端轉速

下載︰ 為 RHEL 6

```
curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel6/rhui-azure-rhel6-2.0-2.noarch.rpm 
```

針對 RHEL 7

```
curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel7/rhui-azure-rhel7-2.0-2.noarch.rpm  
```

請確認︰

```
rpm -Kv azureclient.rpm
```

檢查列印輸出套件的簽章是

```
azureclient.rpm:
    Header V3 RSA/SHA256 Signature, key ID be1229cf: OK
    Header SHA1 digest: OK (927a3b548146c95a3f6c1a5d5ae52258a8859ab3)
    V3 RSA/SHA256 Signature, key ID be1229cf: OK
    MD5 digest: OK (c04ff605f82f4be8c96020bf5c23b86c)
```

安裝轉速

```
sudo rpm -U azureclient.rpm
```

完成時，請確認您可以存取 Azure RHUI 表單 VM

### <a name="all-in-one-script-for-automating-the-above-task"></a>全部的一個指令碼自動執行上述的任務
使用下列指令碼視自動更新受影響的 Vm 到新的 Azure RHUI 伺服器的工作。

```
# Download key
curl -o RPM-GPG-KEY-microsoft-azure-release https://download.microsoft.com/download/9/D/9/9d945f05-541d-494f-9977-289b3ce8e774/microsoft-sign-public.asc 

# Validate key
if ! gpg --list-packets --verbose < RPM-GPG-KEY-microsoft-azure-release | grep -q "keyid: EB3E94ADBE1229CF"; then
    echo "Keyfile azure.asc NOT valid. Exiting."
    exit 1
fi

# Install Key
sudo install -o root -g root -m 644 RPM-GPG-KEY-microsoft-azure-release /etc/pki/rpm-gpg
sudo rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-microsoft-azure-release

# Download RPM package
if grep -q "release 7" /etc/redhat-release; then
    ver=7
elif  grep -q "release 6" /etc/redhat-release; then
    ver=6
else
    echo "Version not supported, exiting"
    exit 1
fi

url=https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel$ver/rhui-azure-rhel$ver-2.0-2.noarch.rpm
curl -o azureclient.rpm "$url"

# Verify package
if ! rpm -Kv azureclient.rpm | grep -q "key ID be1229cf: OK"; then
    echo "RPM failed validation ($url)"
    exit 1
fi

# Install package
sudo rpm -U azureclient.rpm
```

## <a name="rhui-overview"></a>RHUI 概觀
[紅色角色更新基礎結構](https://access.redhat.com/products/red-hat-update-infrastructure)提供彈性的解決方案，來管理所裝載的紅色角色認證雲端提供者的紅色角色企業 Linux 雲端執行個體 yum 存放庫的內容。 根據上游 Pulp 專案，RHUI 可讓本機鏡像紅色角色裝載存放庫內容、 使用自己的內容，建立自訂存放庫及大型群組的使用者透過負載平衡內容傳遞系統提供這些存放庫雲端提供者。

## <a name="regions-where-rhui-is-available"></a>RHUI 可用的地區
RHUI 位於 RHEL 視圖像可在所有區域。 目前，其會包含所有公用[Azure 狀態儀表板](https://azure.microsoft.com/status/)頁面和 Azure 美國政府區域所列出的區域。 佈建後的 RHEL 視圖像 Vm RHUI 存取權會包含在他們的價格。 我們在未來展開 RHEL 視需要顯示狀態時，就會更新其他國家地區/雲端可用性。

> [AZURE.NOTE] Azure 裝載 RHUI 存取受限於[Microsoft Azure 資料中心的 IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)內 Vm。

## <a name="get-updates-from-another-update-repository"></a>從另一個更新存放庫取得更新

若要從不同的更新存放庫 （而非 Azure 裝載 RHUI) 取得更新，您必須取消註冊您的執行個體，從 RHUI 並重新註冊他們想要的更新基礎結構 （例如紅色角色附屬或紅色角色客戶入口網站 CDN）。 您必須適當的紅色角色訂閱這些服務及註冊[Azure 中](https://access.redhat.com/ecosystem/partners/ccsp/microsoft-azure)的紅色角色雲端存取。

若要解除登錄 RHUI，並登錄，才能更新基礎結構待處理] 下方的步驟。

1.  編輯 /etc/yum.repos.d/rh-cloud.repo 並變更所有`enabled=1`至`enabled=0`。 例如︰

        # sed -i 's/enabled=1/enabled=0/g' /etc/yum.repos.d/rh-cloud.repo

2.  編輯 /etc/yum/pluginconf.d/rhnplugin.conf 並變更`enabled=0`至`enabled=1`。
3.  然後註冊所需的基礎結構，例如紅色角色客戶入口網站。 如何[註冊和訂閱的系統紅色角色客戶入口網站](https://access.redhat.com/solutions/253273)，請遵循紅色角色解決方案指南。

> [AZURE.NOTE] Azure 裝載 RHUI 存取會包含在 RHEL Pay-As-You-Go (PAYG) 圖像價格。 取消註冊 PAYG RHEL VM 從 Azure 裝載 RHUI 並不會轉換成提到您-擁有的授權 (BYOL) 類型 VM 的虛擬機器，因此您可能會會產生雙費用與更新的其他來源註冊時相同的 VM。 
> 
> 若要一致以外使用更新基礎結構 Azure 裝載 RHUI 請考慮建立及部署[建立和上傳紅色角色型 Azure 的虛擬機器](virtual-machines-linux-redhat-create-upload-vhd.md)本文所述您自己 （BYOL 類型） 圖像。

## <a name="next-steps"></a>後續步驟
若要從 Azure Marketplace Pay-As-You-Go 圖像和善用建立紅色角色企業 Linux VM Azure 裝載 RHUI 移至[Azure Marketplace](https://azure.microsoft.com/marketplace/partners/redhat/)。 您可以使用`yum update`中沒有任何其他設定您 RHEL 執行個體。