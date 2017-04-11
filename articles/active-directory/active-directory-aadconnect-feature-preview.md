<properties
   pageTitle="Azure AD Connect︰ 功能在預覽中 |Microsoft Azure"
   description="本主題說明中的 Azure AD Connect [預覽] 中的其他詳細資料功能。"
   services="active-directory"
   documentationCenter=""
   authors="andkjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"  
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="06/27/2016"
   ms.author="billmath"/>

# <a name="more-details-about-features-in-preview"></a>在預覽中的功能的相關的更多詳細資料
本主題說明如何使用預覽功能。

## <a name="group-writeback"></a>群組回寫
選用的功能群組回寫的選項可讓您回寫**Office 365 群組**至樹系與安裝的 Exchange。 這是一個隨時掌握雲端中的群組。 如果您有 Exchange 內部，然後您可以撰寫回這些群組至內部部署，因此內部部署 Exchange 信箱的使用者可以傳送及接收電子郵件從這些群組。

您可以找到有關 Office 365 群組，以及如何使用這些[以下](http://aka.ms/O365g)。

此群組的通訊群組內部部署表示 AD DS。 Exchange 2013 積存更新 8 （發行在 2015 年 3 月） 或 Exchange 2016 辨識這個新的群組類型，必須是您內部部署的 Exchange 伺服器。

**在預覽備忘稿**

- 目前不在預覽中才會填入地址活頁簿屬性。 如果沒有這個屬性，群組不會顯示在 GAL 中。 填入此屬性的最簡單方法是使用 Exchange PowerShell cmdlet `update-recipient`。
- 使用 Exchange 的結構描述的樹系是群組的有效目標。 如果偵測到沒有 Exchange，然後群組回寫不會啟用。
- 只有單一樹系 Exchange 組織部署目前支援。 如果您有一個以上的 Exchange 組織內部部署，您會需要您其他樹系中顯示這些群組的內部部署 GALSync 解決方案。
- 群組回寫功能不目前處理安全性群組或通訊群組。

>[AZURE.NOTE] Azure AD Premium 的訂閱，才能群組回寫。

## <a name="user-writeback"></a>使用者回寫
> [AZURE.IMPORTANT] 已移除使用者回寫預覽功能在 2015 年 8 月更新 Azure AD Connect。 如果您已啟用它，然後您應該停用此功能。

## <a name="next-steps"></a>後續步驟
繼續[Azure AD Connect 自訂安裝](./connect/active-directory-aadconnect-get-started-custom.md)。

進一步瞭解[整合您的內部部署識別與 Azure Active Directory](active-directory-aadconnect.md)。
