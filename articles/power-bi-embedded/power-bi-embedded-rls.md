<properties
   pageTitle="使用 Power BI 內嵌的資料列層級安全性"
   description="關於使用 Power BI 內嵌的資料列層級安全性的詳細資料"
   services="power-bi-embedded"
   documentationCenter=""
   authors="guyinacube"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="10/04/2016"
   ms.author="asaxton"/>

# <a name="row-level-security-with-power-bi-embedded"></a>使用 Power BI 內嵌的資料列層級安全性

若要限制使用者存取報表或資料集]，允許多個不同的使用者使用所有文件都看到不同的資料時相同的報表中的特定資料可列層級安全性 (RLS)。 Power BI 內嵌現在支援 RLS 以設定的資料集。

![](media\power-bi-embedded-rls\pbi-embedded-rls-flow-1.png)

好充分利用 RLS，請務必瞭解三個主要的概念。使用者、 角色及規則。 讓我們來看深入瞭解每一個︰

**使用者**– 這些是實際的使用者檢視的報表。 在 Power BI 內嵌，使用者會識別應用程式權杖中的 [使用者名稱] 屬性。

**角色**– 屬於角色的使用者。 角色為規則容器，可以命名項目類似 「 銷售管理員 」 或 「 業務代表 」。 在 Power BI 內嵌，使用者會識別應用程式權杖中的 [角色] 屬性。

**規則**– 角色規則，而且這些規則，將會套用至資料的實際篩選。 這可能是非常簡單，「 國家/地區 = 美國 」 或更動態的項目。

### <a name="example"></a>範例

本文的其餘部分中，我們會提供的撰寫 RLS，然後內嵌應用程式中使用的範例。 我們的範例使用[零售分析範例](http://go.microsoft.com/fwlink/?LinkID=780547)PBIX 檔案。

![](media\power-bi-embedded-rls\pbi-embedded-rls-scenario-2.png)

我們零售分析的範例會顯示特定零售鏈中的所有儲存區銷售額。 不 RLS，無論哪些地區管理員登入並檢視報表，就會看到相同的資料。 高階管理判定每個地區管理員應只會看到他們管理，請儲存的銷售額，若要這麼做，可以使用 RLS。

在 Power BI Desktop 中撰寫 RLS。 資料集和報表會開啟時，我們可以切換至 [圖表檢視]，查看結構描述︰

![](media\power-bi-embedded-rls\pbi-embedded-rls-diagram-view-3.png)

以下是一些可使用此結構描述請注意︰

-   **總銷售額**，所有的量值會儲存在**銷售**交易資料表中。
-   有四個額外的相關的維度資料表︰**項目**、**時間**、**儲存**、 和**地區**。
-   關聯線的箭號表示篩選到另一個，可以流程從一個資料表的方式。 例如，如果篩選會放在**[日期] 時間**，目前的結構描述中它會只篩選下**Sales**資料表中的值。 沒有其他資料表會影響此篩選，因為所有關聯線的箭號指向 sales 資料表然後不在辦公室。
-   **地區**表格表示者管理員是針對每個地區︰

    ![](media\power-bi-embedded-rls\pbi-embedded-rls-district-table-4.png)

根據此結構描述，如果我們**地區管理員**中的資料行 [地區] 資料表中套用篩選，但如果的使用者檢視報表篩選相符的項目，該篩選也將篩選，僅顯示資料的特定地區管理員**儲存**和**銷售**資料表下。

以下是如何︰

1.  在 [模型] 索引標籤上按一下 [**管理角色**]。  
![](media\power-bi-embedded-rls\pbi-embedded-rls-modeling-tab-5.png)

2.  建立新的角色稱為**管理員**。  
![](media\power-bi-embedded-rls\pbi-embedded-rls-manager-role-6.png)

3.  在 [**地區**] 資料表中輸入下列 DAX 運算式︰ **[地區管理員] = USERNAME()**  
![](media\power-bi-embedded-rls\pbi-embedded-rls-manager-role-7.png)

4.  要確定使用的規則，在 [**模型**] 索引標籤上按一下 [**檢視] 作為角色**，並輸入下列︰  
![](media\power-bi-embedded-rls\pbi-embedded-rls-view-as-roles-8.png)

    如果您已登入**為 Andrew 台中**，報表現在會顯示資料。

套用篩選，在這裡，我們的方式就會篩選下**地區**、**儲存**、 和**銷售**資料表中的所有記錄。 不過，由於**銷售**與**階段**間的關聯上顯示篩選器的方向，**銷售**和**項目**，和**項目**及**時間**的表格不會被向下。

![](media\power-bi-embedded-rls\pbi-embedded-rls-diagram-view-9.png)

可能會確定這項需求，不過，如果不想查看其對方沒有任何銷售的項目的管理員，我們可以開啟雙向交互篩選的關聯性，流程兩個方向的安全性篩選。 這可以經由編輯**銷售****項目**之間的關係，像這樣︰

![](media\power-bi-embedded-rls\pbi-embedded-rls-edit-relationship-10.png)

現在，篩選可以也會從流向 Sales 資料表的**項目**資料表︰

![](media\power-bi-embedded-rls\pbi-embedded-rls-diagram-view-11.png)

**附註**如果您使用 DirectQuery 模式的資料，您將需要啟用雙向交互篩選選取這兩個選項︰

1.  **檔案** -> **選項和設定** -> **預覽功能** -> **交叉篩選以不同的兩個方向的啟用**。
2.  **檔案** -> **選項和設定** -> **DirectQuery** -> **允許不受限制的量值在以 DirectQuery 模式**。


若要進一步瞭解雙向交互篩選，下載[雙向交互篩選 SQL Server Analysis Services 2016 和 Power BI Desktop 中](http://download.microsoft.com/download/2/7/8/2782DF95-3E0D-40CD-BFC8-749A2882E109/Bidirectional cross-filtering in Analysis Services 2016 and Power BI.docx)白皮書。

這會摘要需要 Power BI Desktop，完成的所有工作，但有一個更多份工作需要完成進行 RLS 的規則我們在 Power BI 內嵌定義工作。 驗證及應用程式授權的使用者，應用程式權杖會用來授與使用者對特定的 Power BI 內嵌報表。 Power BI 內嵌沒有任何特定的資訊是您的使用者。 為 RLS 搭配使用，您必須屬於您的應用程式權杖傳遞一些額外的內容︰
-   **使用者名稱**（選擇性） – 使用 RLS 這是可以用來協助您識別使用者套用 RLS 規則時的字串。 請參閱使用 Power bi 的層級安全性內嵌的資料列
-   **角色**– 字串，包含套用列層級安全性規則時，選取的角色。 如果傳遞一個以上的角色，他們應該傳遞字串陣列。

如果出現 [使用者名稱] 屬性，那麼您也必須在角色傳遞至少有一個值。

完整的應用程式權杖看起來會像這樣︰

![](media\power-bi-embedded-rls\pbi-embedded-rls-app-token-string-12.png)

現在，有所有分成一組，當有人登入檢視此報告中，我們應用程式，只可以查看所定義的資料列層級安全性，若要查看，允許他們的資料。

![](media\power-bi-embedded-rls\pbi-embedded-rls-dashboard-13.png)

## <a name="see-also"></a>另請參閱
[使用 Power 列層級安全性 (RLS)](https://powerbi.microsoft.com/en-us/documentation/powerbi-admin-rls/)
