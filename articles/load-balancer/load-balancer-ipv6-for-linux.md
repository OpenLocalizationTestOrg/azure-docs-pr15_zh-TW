<properties
    pageTitle="設定 Linux Vm DHCPv6 |Microsoft Azure"
    description="如何設定 Linux Vm DHCPv6。"
    services="load-balancer"
    documentationCenter="na"
    authors="sdwheeler"
    manager="carmonm"
    editor=""
    keywords="ipv6 azure 負載平衡器、 雙堆疊、 公用 ip、 原生 ipv6、 行動電話、 iot"
/>
<tags
    ms.service="load-balancer"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="09/14/2016"
    ms.author="sewhee"
/>

# <a name="configuring-dhcpv6-for-linux-vms"></a>設定 Linux Vm DHCPv6

部分 Linux 虛擬機器中的圖像 Azure Marketplace 沒有 DHCPv6 預設設定。 若要支援 IPv6，DHCPv6 必須設定中您使用的 Linux OS 通訊群組。 Linux 散發有不同的方法可以設定 DHCPv6，因為他們使用不同的套件。

>[AZURE.NOTE] 最近的 SUSE Linux 和 CoreOS 圖像中 Azure Marketplace 已預先設定的 DHCPv6。 使用這些圖像時，不所需任何其他變更。

本文說明如何啟用 DHCPv6，以便您 Linux 的虛擬機器取得 IPv6 位址。

>[AZURE.WARNING] 不正確編輯網路設定檔可能會導致您無法將 VM 網路存取。 我們建議您先測試您的設定變更非生產系統。 本文中的指示進行測試 Linux 中的圖像 Azure Marketplace 的最新版本上。 如需詳細指示 Linux 您特定版本，請參閱文件。

## <a name="ubuntu"></a>Ubuntu

1. 編輯檔案`/etc/dhcp/dhclient6.conf`並新增的下列行︰

        timeout 10;

2. 編輯 eth0 介面的網路設定下列設定︰

    * 在**Ubuntu 12.04 和 14.04**，編輯檔案`/etc/network/interfaces.d/eth0.cfg`
    * 在**Ubuntu 16.04**，編輯檔案`/etc/network/interfaces.d/50-cloud-init.cfg`

    ```bash
    iface eth0 inet6 auto
        up sleep 5
        up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true
    ```

3. 更新 IPv6 位址︰

    ```bash
    # sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="debian"></a>Debian

1. 編輯檔案`/etc/dhcp/dhclient6.conf`並新增的下列行︰

        timeout 10;

2. 編輯檔案`/etc/network/interfaces`，並新增下列設定︰

        iface eth0 inet6 auto
            up sleep 5
            up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. 更新 IPv6 位址︰

    ```bash
    # sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="rhel--centos--oracle-linux"></a>RHEL / CentOS / Oracle Linux

1. 編輯檔案`/etc/sysconfig/network`並新增下列參數︰

        NETWORKING_IPV6=yes

2. 編輯檔案`/etc/sysconfig/network-scripts/ifcfg-eth0`並新增下列兩個參數︰

        IPV6INIT=yes
        DHCPV6C=yes

3. 更新 IPv6 位址︰

    ```bash
    # sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-11--opensuse-13"></a>SLES 11 與 openSUSE 13

最近的 SLES 和 openSUSE 圖像 Azure 中已預先設定的 DHCPv6。 使用這些圖像時，不所需任何其他變更。 如果您有 VM 根據較舊版本或自訂 SUSE 圖像，請使用下列步驟︰

1. 安裝`dhcp-client`封裝，如有需要︰

    ```bash
    # sudo zypper install dhcp-client
    ```

2. 編輯檔案`/etc/sysconfig/network/ifcfg-eth0`並新增下列參數︰

        DHCLIENT6_MODE='managed'

3. 更新的 IPv6 位址︰

    ```bash
    # sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-12-and-opensuse-leap"></a>SLES 12 和 openSUSE 閏

最近的 SLES 和 openSUSE 圖像 Azure 中已預先設定的 DHCPv6。 使用這些圖像時，不所需任何其他變更。 如果您有 VM 根據較舊版本或自訂 SUSE 圖像，請使用下列步驟︰

1. 編輯檔案`/etc/sysconfig/network/ifcfg-eth0`及取代此參數

        #BOOTPROTO='dhcp4'

    使用下列的值︰

        BOOTPROTO='dhcp'

2. 新增下列參數`/etc/sysconfig/network/ifcfg-eth0`:

        DHCLIENT6_MODE='managed'

3. 更新的 IPv6 位址︰

    ```bash
    # sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="coreos"></a>CoreOS

已預先設定的 DHCPv6 Azure 中的最近 CoreOS 圖像。 使用這些圖像時，不所需任何其他變更。 如果您有 VM 根據較舊版本或自訂 CoreOS 圖像，請使用下列步驟︰

1. 編輯檔案`/etc/systemd/network/10_dhcp.network`

        [Match]
        eth0

        [Network]
        DHCP=ipv6

2. 更新的 IPv6 位址︰

    ```bash
    # sudo systemctl restart systemd-networkd
    ```
