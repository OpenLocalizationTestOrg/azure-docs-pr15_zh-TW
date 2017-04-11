<properties 
    pageTitle="已知的網路 |Microsoft Azure" 
    description="藉由設定已知的網路，您可以避免擁有從多個地區登集和登集從 IP 位址可疑的活動報表中包含您組織的 IP 位址。" 
    services="active-directory" 
    documentationCenter="" 
    authors="markusvi" 
    manager="femila"  
    editor=""/>

<tags 
    ms.service="active-directory" 
    ms.workload="identity" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/10/2016" 
    ms.author="markvi"/>

# <a name="known-networks"></a>已知的網路


若要取得完整性與您組織的目錄的安全性，您可以使用 Azure Active Directory 的存取和使用情況報告。 此資訊，目錄管理員可以更有效地決定，好讓他們可以累積規劃來降低風險可能位於可能的安全性風險的位置。

有可能的 「*從多個地區登集*」 和 「*登集來自可疑的活動的 IP 位址*」 的報表不正確標幟實際上由您的組織所擁有的 IP 位址。 

這可能，例如，會發生時︰ 

- 使用者在您 office 已登入遠端資料中心舊金山機場的觸發程序 」 登入集從多個地區 」 的報表 

- 貴組織的使用者嘗試登入數次以不正確的密碼引動程序 」 登集從 IP 位址可疑的活動 」 的報表 

若要防止產生誤導安全性報告這種情況下，您應該新增已知的 IP 位址範圍貴組織的公用 IP 位址的清單。    


###<a name="to-add-your-organizations-public-ip-address-ranges-perform-the-following-steps"></a>若要新增您組織的公用 IP 位址範圍，請執行下列步驟︰ 

1.  登入至[Azure 管理入口網站](https://manage.windowsazure.com)。

2.  在左窗格中，按一下 [ **Active Directory**]。 <br><br>![雲端應用程式探索的運作方式](./media/active-directory-known-networks/known-netwoks-01.png)

3.  在 [**目錄**] 索引標籤中，選取 [目錄]。

4.  在頂端的功能表，按一下 [**設定**]。 <br><br>![雲端應用程式探索的運作方式](./media/active-directory-known-networks/known-netwoks-02.png)

5.  在 [設定] 索引標籤上移至**您的組織公用 IP 位址範圍** <br><br>![雲端應用程式探索的運作方式](./media/active-directory-known-networks/known-netwoks-03.png)

6.  按一下 [**新增已知的 IP 位址範圍**]。

7.  在對話方塊出現的方塊中，新增您的地址範圍，當您完成時，然後按一下 [檢查] 按鈕。 <br><br>![雲端應用程式探索的運作方式](./media/active-directory-known-networks/known-netwoks-04.png)


**其他資源**


* [檢視您存取及使用方式的報表](active-directory-view-access-usage-reports.md)
* [來自可疑活動的 IP 位址登集](active-directory-reporting-sign-ins-from-ip-addresses-with-suspicious-activity.md)
* [從多個地區登集](active-directory-reporting-sign-ins-from-multiple-geographies.md)


