<properties
    pageTitle="Azure Active Directory B2C︰ 生產縮放比例與預覽 B2C 租用戶 |Microsoft Azure"
    description="主題 Azure Active Directory B2C 租用戶的類型"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/30/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-production-scale-vs-preview-b2c-tenants"></a>Azure Active Directory B2C︰ 生產縮放比例與預覽 B2C 租用戶

如果您打算在 Azure Active Directory (Azure AD) B2C 撰寫生產應用程式，必須先確定您有正確的租用戶 「 type 」 到即時上。 若要查看您已有、 瀏覽[至 B2C 功能刀](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)請遵循下列步驟，Azure 入口網站上並**租用戶類型**] 下查看。

## <a name="summary"></a>摘要

Azure AD B2C 支援只開啟**生產比例**B2C 租用戶北美地區的實際執行應用程式。

| 租用戶類型 | 國家/地區 | -推出？ |
| ----------- | -------------- | --------------------- |
| **生產比例租用戶** | 北美地區的國家/地區 | [是] |
| **生產比例租用戶** | 所有國家/地區北美以外 | 無 |
| **預覽租用戶** | 所有國家/地區 | 無 |

> [AZURE.NOTE]
Azure AD B2C 租用戶 （適用於消費者） 位於目前無法使用幾個國家/地區或區域 Azure AD 租用戶 （員工用） 可的位置。 請閱讀以下各節，如需詳細資訊。

## <a name="production-scale-b2c-tenant-in-north-america"></a>北美地區生產比例 B2C 租用戶

如果您[建立您 B2C 租用戶](active-directory-b2c-get-started.md)北美地區，也就是在其中一個動作的國家或地區︰ United 狀態、 加拿大、 哥斯大黎加、 多明尼加、 薩爾瓦多、 瓜地馬拉、 墨西哥、 巴拿馬、 波多黎各和千里達及托巴哥，和您 B2C 管理員使用者介面上的**租用戶類型**指出**生產縮放比例**，可用於您的租用戶的實際執行應用程式。

> [AZURE.NOTE]
生產比例租用戶也能縮放到 100 s 的數百萬消費者身分識別每個租用戶。

![生產比例租用戶的螢幕擷取畫面](./media/active-directory-b2c-reference-tenant-type/production-scale-b2c-tenant.png)

## <a name="preview-b2c-tenant-in-any-countryregion"></a>預覽 B2C 租用戶中任何國家/地區

如果您之前在 Azure AD B2C 的預覽期間建立 B2C 租用戶，可能是，您的**租用戶輸入**顯示**預覽租用戶**。 如果這種情況，您必須先使用您的租用戶，僅適用於開發和測試之用，而非生產應用程式。

> [AZURE.IMPORTANT]
沒有生產比例 B2C 租用戶從預覽 B2C 租用戶移轉路徑。 請注意，那里已知問題，當您刪除預覽 B2C 租用戶，並重新建立生產比例 B2C 租用戶使用相同的網域名稱。 您必須使用不同的網域名稱建立生產比例 B2C 租用戶。

![預覽租用戶的螢幕擷取畫面](./media/active-directory-b2c-reference-tenant-type/preview-b2c-tenant.png)

## <a name="production-scale-b2c-tenant-outside-of-north-america"></a>北美以外的生產比例 B2C 租用戶

Azure AD B2C 目前不是-推出北美以外。 不過您可以建立並使用生產比例租用戶，開發和測試方面，下列國家或地區的其中一種︰ 阿爾及利亞、 奧地利、 亞塞拜然、 巴林，白俄羅斯、 比利時、 保加利亞、 克羅埃西亞、 賽浦路斯、 捷克共和國、 丹麥、 埃及、 愛沙尼亞、 芬蘭、 法國、 德國、 希臘、 匈牙利、 冰島、 愛爾蘭、 以色列、 義大利、 約旦、 哈薩克、 肯亞、 科威特、 Lativa，黎巴嫩、 列支敦斯登、 Lituania，盧森堡、 FYRO，馬其頓、 馬爾他、 蒙特內哥羅、 摩洛哥、 荷蘭、 奈及利亞、 挪威阿曼、 巴基斯坦、 波蘭、 葡萄牙、 卡達、 羅馬尼亞、 俄羅斯、 沙烏地阿拉伯、 塞爾維亞、 斯洛伐克、 斯洛維尼亞、 南非、 西班牙、 瑞典、 瑞士、 突尼西亞、 土耳其、 烏克蘭，United 阿拉伯聯合大公國和英國。

一旦 Azure AD B2C 宣告國家/地區或區域上方開放中的，您可以繼續使用這些生產比例租用戶，並在線上使用您的實際執行應用程式，而不遺失任何資料。

## <a name="availability-of-b2c-tenants"></a>B2C 租用戶的可用性

B2C 租用戶中的下列國家或地區是目前無法使用︰ 阿富汗、 阿根廷、 澳大利亞、 巴西、 智利、 哥倫比亞、 厄瓜多、 香港特別行政區、 印度、 印尼、 伊拉克、 日本、 korea （韓國）、 馬來西亞、 紐西蘭、 巴拉圭、 秘魯、 菲律賓、 新加坡、 斯里蘭卡、 台灣、 泰國、 烏拉圭和委內瑞拉。 我們計劃以將其包含在未來。
