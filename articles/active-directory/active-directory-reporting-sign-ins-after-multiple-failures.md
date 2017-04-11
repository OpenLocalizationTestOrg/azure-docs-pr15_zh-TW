<properties
    pageTitle="多個失敗之後，登集"
    description="報表，指出多個連續失敗符號中嘗試後有順利登入的使用者。"
    services="active-directory"
    documentationCenter=""
    authors="SSalahAhmed"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="03/04/2016"
    ms.author="saah;kenhoff"/>

# <a name="sign-ins-after-multiple-failures"></a>多個失敗之後登增益集。
此報表會指出多個連續失敗符號中嘗試後有順利登入的使用者。 可能原因包括︰

- 使用者忘記使用者的密碼</li><li>使用者是成功密碼猜測劇烈的受害者強制攻擊

此報表中的結果會顯示數個連續失敗的登入，嘗試之前順利登入和相關聯的第一個成功登入的時間戳記。

**報表設定**︰ 您可以嘗試必須先將會顯示在報表中設定連續失敗符號中的最小的數目。 當您變更此設定很重要的一點，這些變更會不會套用至集目前顯示在現有報表中任何現有失敗符號。 不過，它們會套用到所有的未來登增益集。 此報表可以只會做的變更授權管理員。


![多個失敗之後登集](./media/active-directory-reporting-sign-ins-after-multiple-failures/signInsAfterMultipleFailures.PNG)
