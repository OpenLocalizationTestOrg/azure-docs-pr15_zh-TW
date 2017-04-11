<properties
   pageTitle="指派 SQL Data Warehouse 中的變數 |Microsoft Azure"
   description="指定 SQL Azure SQL Data Warehouse 變數開發解決方案的秘訣。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/14/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="assign-variables-in-sql-data-warehouse"></a>指派 SQL Data Warehouse 中的變數
使用設定中 SQL Data Warehouse 變數`DECLARE`陳述式或`SET`陳述式。

下列是完全有效的方式設定一個變數的值︰

## <a name="setting-variables-with-declare"></a>設定與 DECLARE 變數

初始化 DECLARE 變數是其中一個最有彈性的方式，可在 SQL Data Warehouse 設定變數值。

```sql
DECLARE @v  int = 0
;
```

您也可以使用 DECLARE 一次設定一個以上的變數。 您無法使用`SELECT`或`UPDATE`進行這項操作︰

```sql
DECLARE @v  INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Smith')
,       @v1 INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Jones')
;
```

您無法初始化，並使用相同的 DECLARE 陳述式中的變數。 **若要描繪出下面的範例中的點作為**@p1同時初始化，而使用相同的 DECLARE 陳述式中。 這會導致錯誤。

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="setting-values-with-set"></a>設定的設定值
設定是設定單一變數的常見方法。

所有下面的範例是一組設定一個變數的有效方法︰

```sql
SET     @v = (Select max(database_id) from sys.databases);
SET     @v = 1;
SET     @v = @v+1;
SET     @v +=1;
```

您只能與集一次設定一個變數。 不過，可以看到上方複合運算子是 permissable。

## <a name="limitations"></a>限制
您無法使用 [選取] 或 [更新變數工作分派。


## <a name="next-steps"></a>後續步驟
如需開發秘訣，請參閱[開發概觀][]。

<!--Image references-->

<!--Article references-->
[開發概觀]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->
