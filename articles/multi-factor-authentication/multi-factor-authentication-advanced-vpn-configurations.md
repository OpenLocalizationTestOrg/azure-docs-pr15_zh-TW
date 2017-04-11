<properties
    pageTitle="Azure 多重因素驗證與第 3 派對 Vpn 進階分析藍本"
    description="本頁面提供與第 3 廠商 prodcuts Azure MFA 逐步設定設定資訊。"
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban" 
    manager="femila"
    editor="curtland"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="advanced-scenarios-with-azure-multi-factor-authentication-and-3rd-party-vpn"></a>Azure 多重因素驗證與第 3 派對 VPN 進階分析藍本
Azure 多重因素驗證可以用於流暢地使用各種不同的第 3 廠商 VPN 解決方案連線。  這包含 Cisco® ASA VPN 應用裝置與 Citrix NetScaler SSL VPN 應用 Juniper 網路安全存取/脈衝安全連線安全 SSL VPN 應用裝置。

## <a name="cisco-asa-vpn-appliance-and-azure-multi-factor-authentication"></a>Cisco ASA VPN 應用裝置和 Azure 多重因素驗證
Azure 多重因素驗證完美整合了您的 Cisco® ASA VPN 應用裝置提供額外的安全性 Cisco AnyConnect® 要有 vpn 才能登入與入口網站的存取權。  這可以使用 [LDAP] 或 [RADIUS 通訊協定。  選取其中一個動作，下載的詳細逐步設定輔助線。

設定指南  | 描述
------------- | ------------- |
[Cisco ASA 為 LDAP Anyconnect VPN 和 Azure MFA 設定](http://download.microsoft.com/download/A/2/0/A201567C-C3DE-4227-AF89-4567A470899E/Cisco_ASA_Azure_MFA_LDAP.docx) | 使用 LDAP Azure MFA 完美整合您 Cisco ASA VPN 應用裝置|
[Cisco ASA 與 RADIUS Anyconnect VPN 和 Azure MFA 設定](http://download.microsoft.com/download/4/5/7/4579C1CF-35B0-4FBE-8A1A-B49CB2CC0382/Cisco_ASA_Azure_MFA_RADIUS.docx) | 使用 RADIUS Azure MFA 完美整合您 Cisco ASA VPN 應用裝置

## <a name="citrix-netscaler-ssl-vpn-and-azure-multi-factor-authentication"></a>Citrix NetScaler SSL VPN 和 Azure 多重因素驗證
Azure 多重因素驗證完美整合了您的 Citrix NetScaler SSL VPN 應用裝置提供額外的安全性 Citrix NetScaler SSL 要有 vpn 才能登入與入口網站的存取權。  這可以使用 [LDAP] 或 [RADIUS 通訊協定。  選取其中一個動作，下載的詳細逐步設定輔助線。

設定指南  | 描述
------------- | ------------- |
[LDAP 的 Citrix NetScaler SSL VPN 和 Azure MFA 設定](http://download.microsoft.com/download/2/4/E/24E1E722-72DF-471F-A88A-D1338DB1AF83/Citrix_NS_Azure_MFA_LDAP.docx) | Azure MFA 應用裝置使用 LDAP 完美整合您 Citrix NetScaler SSL VPN|
[針對 Citrix NetScaler SSL VPN 和 Azure MFA 設定](http://download.microsoft.com/download/1/A/4/1A482764-4A63-45C2-A5EC-2B673ACCDD12/Citrix_NS_Azure_MFA_RADIUS.docx) | 使用 RADIUS Azure MFA 完美整合您 Citrix NetScaler SSL VPN 應用裝置

##<a name="juniperpulse-secure-ssl-vpn-appliance-and-azure-multi-factor-authentication"></a>Juniper/脈衝安全 SSL VPN 應用裝置和 Azure 多重因素驗證
Azure 多重因素驗證完美整合了您的 Juniper/脈衝安全 SSL VPN 應用裝置提供額外的安全性 Juniper/脈衝安全 SSL VPN 登入與入口網站的存取權。  這可以使用 [LDAP] 或 [RADIUS 通訊協定。  選取其中一個動作，下載的詳細逐步設定輔助線。

設定指南  | 描述
------------- | ------------- |
[LDAP 的 juniper/脈衝安全 SSL VPN 和 Azure MFA 設定](http://download.microsoft.com/download/6/5/8/6587B418-75B1-4FCB-84D4-984BC479309E/JuniperPulse_Azure_MFA_LDAP.docx)| Azure MFA 應用裝置使用 LDAP 完美整合您 Juniper/脈衝安全 SSL VPN|
[針對 juniper/脈衝安全 SSL VPN 和 Azure MFA 設定](http://download.microsoft.com/download/7/9/A/79AB3DAD-4799-4379-B1DA-B95ABDF231DC/JuniperPulse_Azure_MFA_RADIUS.docx) | 使用 RADIUS Azure MFA 完美整合您 Juniper/脈衝安全 SSL VPN 應用裝置
