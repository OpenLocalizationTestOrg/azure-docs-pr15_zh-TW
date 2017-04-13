<properties
   pageTitle="使用動態 DNS 登錄主機名稱"
   description="此頁面詳細說明如何設定在自己的 DNS 伺服器註冊 hostname 動態 DNS。"
   services="dns"
   documentationCenter="na"
   authors="GarethBradshawMSFT"
   manager="carmonm"
   editor="" />
<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/31/2016"
   ms.author="sewhee" />

# <a name="using-dynamic-dns-to-register-hostnames-in-your-own-dns-server"></a>使用動態 DNS 註冊您自己的 DNS 伺服器主機名稱

[Azure 提供名稱解析](virtual-networks-name-resolution-for-vms-and-role-instances.md)虛擬機器 (Vm) 和角色執行個體。 不過，當您的名稱解析需要以外提供 Azure，您可以提供您自己的 DNS 伺服器。 這可讓您依需求自訂您的 DNS 解決方案，以符合特定需求的電源。 例如，您可能需要存取您的 Active Directory 網域控制站的內部部署資源。

當您自訂的 DNS 伺服器所裝載 Azure Vm 為您可以轉寄主機名稱的查詢相同 vnet 來解決 hostname Azure。 如果您不想使用此路由，您可以在您的 DNS 伺服器使用動態 DNS 中註冊您 VM 主機名稱。  Azure 沒有直接建立記錄在 [您的 DNS 伺服器]，讓替代的排列方式所需要的功能 （例如認證）。 以下是一些常見的案例的替代方案。

## <a name="windows-clients"></a>Windows 用戶端

非加入網域的 Windows 用戶端嘗試不安全的動態 DNS (DDNS) 更新，當他們開機，或變更其 IP 位址。 DNS 名稱是主機名稱加上主要 DNS 尾碼。 Azure 將主要 DNS 尾碼保留為空白，但您可以在 VM，透過[UI](https://technet.microsoft.com/library/cc794784.aspx)或[使用自動化](https://social.technet.microsoft.com/forums/windowsserver/3720415a-6a9a-4bca-aa2a-6df58a1a47d7/change-primary-dns-suffix)設定。

網域的 Windows 用戶端 IP 位址的網域控制站使用登錄安全的動態 DNS。 網域加入程序設定主要 DNS 尾碼用戶端上建立和維護的信任關係。

## <a name="linux-clients"></a>Linux 用戶端

Linux 用戶端通常不註冊與 DNS 伺服器在啟動時，其假設 DHCP 伺服器會。 Azure 的 DHCP 伺服器上沒有的功能或認證，以在您的 DNS 伺服器註冊記錄。  您可以使用工具，稱為*nsupdate*，包含繫結套件，將動態 DNS 更新。 因為標準化的動態 DNS 通訊協定，您可以使用*nsupdate* ，即使您不使用繫結 DNS 伺服器上。

您可以使用攔截程序所提供的 DHCP 用戶端來建立和維護主機名稱中的項目 DNS 伺服器。 DHCP 週期用戶端，請在 [ */etc/dhcp/dhclient-exit-hooks.d/*中執行指令碼。 這可以用於使用*nsupdate*註冊新的 IP 位址。 例如︰

        #!/bin/sh
        requireddomain=mydomain.local

        # only execute on the primary nic
        if [ "$interface" != "eth0" ]
        then
            return
        fi

        # when we have a new IP, perform nsupdate
        if [ "$reason" = BOUND ] || [ "$reason" = RENEW ] ||
           [ "$reason" = REBIND ] || [ "$reason" = REBOOT ]
        then
            host=`hostname`
            nsupdatecmds=/var/tmp/nsupdatecmds
              echo "update delete $host.$requireddomain a" > $nsupdatecmds
              echo "update add $host.$requireddomain 3600 a $new_ip_address" >> $nsupdatecmds
              echo "send" >> $nsupdatecmds

              nsupdate $nsupdatecmds
        fi

        #done
        exit 0;

您也可以使用 [ *nsupdate* ] 命令來執行安全的動態 DNS 更新。 例如，當您使用的繫結 DNS 伺服器，公開私密金鑰組會[產生](http://linux.yyz.us/nsupdate/)。  DNS 伺服器是使用公用索引鍵的組件的[設定](http://linux.yyz.us/dns/ddns-server.html)，讓它可以驗證要求的簽章。 您必須使用*-k*選項以提供要*nsupdate*動態 dns 順序的金鑰組更新簽署的要求。

當您使用 Windows DNS 伺服器時，您可以使用 Kerberos 驗證和*-g*參數中*nsupdate* （不適用於 Windows 版本的*nsupdate*）。 若要這麼做，請使用*kinit*載入 （例如從[keytab 檔案](http://www.itadmintools.com/2011/07/creating-kerberos-keytab-files.html)） 的認證。 然後從快取認證挑選*nsupdate g* 。

如有需要您可以新增您的 Vm 尾碼搜尋。 */Etc/resolv.conf*檔案中指定的尾碼。 大部分的 Linux distros 自動管理內容的此檔案，因此通常就無法編輯。 不過，您可以使用 DHCP 用戶端的 [*取代*] 命令來覆寫的後置字元。 若要這麼做， */etc/dhcp/dhclient.conf*中加入︰

        supersede domain-name <required-dns-suffix>;

