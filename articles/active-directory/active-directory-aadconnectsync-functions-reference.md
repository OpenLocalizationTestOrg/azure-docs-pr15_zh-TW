<properties
    pageTitle="Azure AD Connect 同步處理︰ 函數參照 |Microsoft Azure"
    description="宣告式佈建中的運算式 Azure AD Connect 同步處理的參考。"
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
    ms.date="08/23/2016"
    ms.author="andkjell;markvi"/>


# <a name="azure-ad-connect-sync-functions-reference"></a>Azure AD Connect 同步處理︰ 函數參考

在 Azure AD 連線，函數會用來管理同步處理期間的屬性值。  
函數的語法是使用下列格式來表示︰  
`<output type> FunctionName(<input type> <position name>, ..)`

如果函數超載，接受多個語法會列出所有有效的語法。  
函數非常輸入並確認，類型傳遞相符的項目中的記錄的類型。  
如果類型不相符，會發生錯誤。

使用下列語法表示類型︰

- **回收筒**– 二進位
- **bool** – 布林值
- **資料層**– UTC 日期/時間
- **列舉**– 已知的常數的列舉
- **exp** – 必須評估為布林值的運算式
- **mvbin** – 多二進位
- **mvstr** – 多字串
- **mvref** – 多參照
- **數字**-數字
- **ref** – 參考
- **str** – 字串
- **var** – （幾乎） 任何其他類型的變化
- **void** – 不會傳回值

多重值屬性就可以只使用類型**mvbin**、 **mvstr**，及**mvref**函數。 **回收筒**、 **str**，與**ref**函數的運作方式的單一值及多重值屬性。

## <a name="functions-reference"></a>函數參考

函數的清單 | | | | |  
--------- | --------- | --------- | --------- | --------- | ---------
**轉換** |  
[CBool](#cbool) | [CDate](#cdate) | [CGuid](#cguid) | [ConvertFromBase64](#convertfrombase64)
[ConvertToBase64](#converttobase64) | [ConvertFromUTF8Hex](#convertfromutf8hex) | [ConvertToUTF8Hex](#converttoutf8hex) | [CNum](#cnum)
[CRef](#cref) | [CStr](#cstr) | [StringFromGuid](#StringFromGuid) | [StringFromSid](#stringfromsid)
**日期 / 時間** |  
[Dateadd 函數](#dateadd) | [DateFromNum](#datefromnum) | [FormatDateTime](#formatdatetime) | [現在](#now)
[NumFromDate](#numfromdate) |  
**目錄** |  
[DNComponent](#dncomponent) | [DNComponentRev](#dncomponentrev) | [EscapeDNComponent](#escapedncomponent)
**評估** |  
[IsBitSet](#isbitset) | [IsDate](#isdate) | [IsEmpty](#isempty) | [IsGuid](#isguid)
[IsNull](#isnull) | [IsNullOrEmpty](#isnullorempty) | [IsNumeric](#isnumeric) | [IsPresent](#ispresent) |
[IsString](#isstring) |  
**數學** |  
[BitAnd](#bitand) | [BitOr](#bitor) | [RandomNum](#randomnum)
**多重值** |  
[包含](#contains) | [字數統計](#count) | [項目](#item) | [ItemOrNull](#itemornull)
[加入](#join) | [RemoveDuplicates](#removeduplicates) | [分割](#split) |
**程式流程** |  
[錯誤](#error) | [IIF](#iif)  | [切換](#switch)
**文字** |  
[GUID](#guid) | [InStr](#instr) | [InStrRev](#instrrev) | [LCase](#lcase)
[向左](#left) | [Len](#len) | [LTrim](#ltrim) | [Mid](#mid)
[PadLeft](#padleft) | [PadRight](#padright) | [PCase](#pcase) | [取代](#replace)
[ReplaceChars](#replacechars) | [權限](#right) | [RTrim](#rtrim) | [修剪](#trim)
[UCase](#ucase) | [Word](#word)

----------
### <a name="bitand"></a>BitAnd

**描述︰**  
BitAnd 函數會指定位元設定值。

**語法︰**  
`num BitAnd(num value1, num value2)`

- value1，value2︰ 應該 AND'ed 放在一起的數值

**註解︰**  
此函數會將兩個參數轉換成二進位表示，並且設定位元︰

- 0-如果一或兩個對應的位元*遮罩*和*標幟*為 0
- 1-如果這兩個對應的位元為 1。

換句話說，則會傳回 0 在所有的情況下，除非對應兩個參數的位元是 1。

**範例︰**  
`BitAnd(&HF, &HF7)`  
會傳回 7，因為十六進位"F"與"F7 」 評估這個值。

----------
### <a name="bitor"></a>BitOr

**描述︰**  
BitOr 函數會指定位元設定值。

**語法︰**  
`num BitOr(num value1, num value2)`

- value1，value2︰ 應該 OR'ed 放在一起的數值

**註解︰**  
此函數將兩個參數轉換成二進位表示並設定位元如果一或兩個遮罩和標幟中對應的位元是 1，1 與 0 的相對應的位元都是 0。 換句話說，則傳回 1 在所有的情況下，除了對應兩個參數的位元為 0 的位置。

----------
### <a name="cbool"></a>CBool

**描述︰**  
CBool 函數會傳回根據評估的運算式為布林值

**語法︰**  
`bool CBool(exp Expression)`

**註解︰**  
如果運算式評估為非零值，然後 CBool，則傳回 True，否則會傳回 False。

**範例︰**  
`CBool([attrib1] = [attrib2])`  

會傳回 True，若兩個屬性有相同的值。

----------
### <a name="cdate"></a>CDate

**描述︰**  
CDate 函數會傳回 UTC DateTime 從字串。 DateTime 不同步處理原生屬性類型，但某些功能使用。

**語法︰**  
`dt CDate(str value)`

- 使用日期、 時間及選擇性地時區字串值︰

**註解︰**  
傳回的字串一律是以 utc 表示。

**範例︰**  
`CDate([employeeStartTime])`  
傳回 DateTime 根據員工的開始時間

`CDate("2013-01-10 4:00 PM -8")`  
傳回日期時間表示 「 2013年-01-11 12:00 AM 」

----------
### <a name="cguid"></a>CGuid

**描述︰**  
CGuid 函數會轉換為二進位表示 GUID 的字串表示。

**語法︰**  
`bin CGuid(str GUID)`

- 格式化在此模式中的字串︰ xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx 或 {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}

----------
### <a name="contains"></a>包含

**描述︰**  
包含函數會找出的字串內的多重值屬性

**語法︰**  
`num Contains (mvstring attribute, str search)`-區分大小寫  
`num Contains (mvstring attribute, str search, enum Casetype)`  
`num Contains (mvref attribute, str search)`-區分大小寫

- 屬性︰ 要搜尋的多重值的屬性。
- 搜尋︰ 若要尋找屬性中的字串。
- Casetype: CaseInsensitive 或 CaseSensitive。

傳回在多重值屬性中的索引，找的字串。 如果找不到的字串，則會傳回 0。

**註解︰**  
多重值的字串屬性的搜尋字串值。  
參照屬性的搜尋的字串必須確實符合視為相符的值。

**範例︰**  
`IIF(Contains([proxyAddresses],"SMTP:")>0,[proxyAddresses],Error("No primary SMTP address found."))`  
如果 proxyAddresses 屬性中具有主要的電子郵件地址 (以大寫字母表示 「 SMTP: 」)，然後返回 proxyAddress 屬性，還會傳回錯誤。

----------
### <a name="convertfrombase64"></a>ConvertFromBase64

**描述︰**  
ConvertFromBase64 函數會將指定的 base64 編碼值轉換為一般的字串。

**語法︰**  
`str ConvertFromBase64(str source)`-假設 Unicode 編碼  
`str ConvertFromBase64(str source, enum Encoding)`

- 來源︰ Base64 編碼字串  
- 編碼︰ Unicode，ASCII，UTF8

**範例**  
`ConvertFromBase64("SABlAGwAbABvACAAdwBvAHIAbABkACEA")`  
`ConvertFromBase64("SGVsbG8gd29ybGQh", UTF8)`

兩個範例會傳回 「*Hello world ！*」

----------
### <a name="convertfromutf8hex"></a>ConvertFromUTF8Hex

**描述︰**  
ConvertFromUTF8Hex 函數會將指定編碼 UTF8 十六進位值轉換為字串。

**語法︰**  
`str ConvertFromUTF8Hex(str source)`

- 來源︰ UTF8 2 位元組編碼螫針

**註解︰**  
此函數與結果是易記 DN 屬性中的 ConvertFromBase64([],UTF8) 之間的差異。  
Azure Active Directory 會使用此格式為 DN。

**範例︰**  
`ConvertFromUTF8Hex("48656C6C6F20776F726C6421")`  
傳回 「*Hello world ！*」

----------
### <a name="converttobase64"></a>ConvertToBase64

**描述︰**  
ConvertToBase64 函數會將字串轉換成 Unicode base64 字串。  
轉換相等的字串表示底數 64 位數編碼陣列的整數值。

**語法︰**  
`str ConvertToBase64(str source)`

**範例︰**  
`ConvertToBase64("Hello world!")`  
傳回 「 SABlAGwAbABvACAAdwBvAHIAbABkACEA 」

----------
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex

**描述︰**  
ConvertToUTF8Hex 函數會將編碼 UTF8 十六進位值的字串。

**語法︰**  
`str ConvertToUTF8Hex(str source)`

**註解︰**  
Azure Active Directory 會使用此函數的輸出格式為 DN 屬性格式。

**範例︰**  
`ConvertToUTF8Hex("Hello world!")`  
傳回 48656C6C6F20776F726C6421

----------
### <a name="count"></a>字數統計

**描述︰**  
Count 函數會傳回的項目數內的多重值屬性

**語法︰**  
`num Count(mvstr attribute)`

----------
### <a name="cnum"></a>CNum

**描述︰**  
CNum 函數會採用字串，並傳回數值的資料類型。

**語法︰**  
`num CNum(str value)`

----------
### <a name="cref"></a>CRef

**描述︰**  
將字串轉換成參考屬性

**語法︰**  
`ref CRef(str value)`

**範例︰**  
`CRef("CN=LC Services,CN=Microsoft,CN=lcspool01,CN=Pools,CN=RTC Service," & %Forest.LDAP%)`

----------
### <a name="cstr"></a>CStr

**描述︰**  
CStr 函數將轉換成字串資料類型。

**語法︰**  
`str CStr(num value)`  
`str CStr(ref value)`  
`str CStr(bool value)`  

- 值︰ 可以是數字的值，參考屬性或布林值。

**範例︰**  
`CStr([dn])`  
會傳回 「 cn = 王，亦即 = contoso，dc = com 」

----------
### <a name="dateadd"></a>Dateadd 函數

**描述︰**  
傳回包含已新增指定的時間間隔的日期。

**語法︰**  
`dt DateAdd(str interval, num value, dt date)`

- 間隔︰ 的字串是您想要新增的時間間隔的運算式。 字串必須具備下列的值的其中一個︰
 - yyyy 年
 - 問︰ 當季
 - m 月
 - 一年的 y 天
 - d 天
 - w 工作日
 - 全球週
 - h
 - n 分鐘
 - s 第二個
- 值︰ 您想要新增的單位數量。 它可以是 （可在未來日期） 是正值或負值 （若要取得過去的日期）。
- 日期︰ 代表的日期加上間隔的日期時間。

**範例︰**  
`DateAdd("m", 3, CDate("2001-01-01"))`  
新增 3 個月，並傳回日期時間表示 「 2001年-04-01 」。

----------
### <a name="datefromnum"></a>DateFromNum

**描述︰**  
DateFromNum 函數轉換為 DateTime 類型設定格式的廣告的日期值。

**語法︰**  
`dt DateFromNum(num value)`

**範例︰**  
`DateFromNum([lastLogonTimestamp])`  
`DateFromNum(129699324000000000)`  
傳回日期時間表示 2012年-01-01 23:00:00

----------
### <a name="dncomponent"></a>DNComponent

**描述︰**  
DNComponent 函數會傳回指定的 DN 元件，從左邊的值。

**語法︰**  
`str DNComponent(ref dn, num ComponentNumber)`

- dn︰ 若要判讀準參考屬性
- 若要傳回 dn 元件 ComponentNumber:

**範例︰**  
`DNComponent([dn],1)`  
如果 dn 是 「 cn = 王，ou =...]，「 會傳回王

----------
### <a name="dncomponentrev"></a>DNComponentRev

**描述︰**  
DNComponentRev 函數會傳回指定的 DN 元件，將從右 （結束） 的值。

**語法︰**  
`str DNComponentRev(ref dn, num ComponentNumber)`  
`str DNComponentRev(ref dn, num ComponentNumber, enum Options)`

- dn︰ 若要判讀準參考屬性
- ComponentNumber-傳回 dn 元件
- 選項︰ DC – 略過所有元件 「 dc = 」

**範例︰**  
如果 dn 」 cn = 王，ou = 亞特蘭大，ou = GA，ou = 美國，亦即 = contoso，dc = com 」 然後  
`DNComponentRev([dn],3)`  
`DNComponentRev([dn],1,"DC")`  
都會傳回給我們。

----------
### <a name="error"></a>錯誤

**描述︰**  
誤差函數用來傳回自訂錯誤。

**語法︰**  
`void Error(str ErrorMessage)`

**範例︰**  
`IIF(IsPresent([accountName]),[accountName],Error("AccountName is required"))`  
如果屬性 accountName 不存在，請在物件上引發錯誤。

----------
### <a name="escapedncomponent"></a>EscapeDNComponent

**描述︰**  
EscapeDNComponent 函數的 DN 一個元件然後讓它可以用來表示 LDAP 逸出。

**語法︰**  
`str EscapeDNComponent(str value)`

**範例︰**  
`EscapeDNComponent("cn=" & [displayName]) & "," & %ForestLDAP%)`  
可確保可以 LDAP 目錄中建立的物件，即使的 displayName 屬性必須在 LDAP 逸出字元。

----------
### <a name="formatdatetime"></a>FormatDateTime

**描述︰**  
FormatDateTime 函數用來設定字串的日期時間格式使用指定的格式

**語法︰**  
`str FormatDateTime(dt value, str format)`

- 值︰ DateTime 格式的值
- 格式︰ 代表要轉換的格式字串。

**註解︰**  
可以在這裡找到可能的值的格式︰[使用者定義日期/時間格式 （Format 函數）](http://msdn2.microsoft.com/library/73ctwf33\(VS.90\).aspx)

**範例︰**  

`FormatDateTime(CDate("12/25/2007"),"yyyy-mm-dd")`  
在 「 2007年-12-25 」 的結果。

`FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")`  
可能會導致 「 20140905081453.0Z 」

----------
### <a name="guid"></a>GUID

**描述︰**  
函數 GUID 會產生一個新的隨機 GUID

**語法︰**  
`str GUID()`

----------
### <a name="iif"></a>IIF

**描述︰**  
IIF 函數會傳回一組根據指定的條件的可能值的其中一項。

**語法︰**  
`var IIF(exp condition, var valueIfTrue, var valueIfFalse)`

- 條件︰ 任何值或運算式評估為 true 或 false。
- valueIfTrue︰ 如果條件評估為 true 時，所傳回的值。
- valueIfFalse︰ 如果條件評估為 false，所傳回的值。

**範例︰**  
`IIF([employeeType]="Intern","t-" & [alias],[alias])`  
 保留使用者時，會傳回與 「 t-」 新增至其開頭，其他使用者的別名會傳回該使用者的別名現狀。

----------
### <a name="instr"></a>InStr

**描述︰**  
InStr 函數字串中找到的子字串的第一個項目

**語法︰**  

`num InStr(str stringcheck, str stringmatch)`  
`num InStr(str stringcheck, str stringmatch, num start)`  
`num InStr(str stringcheck, str stringmatch, num start , enum compare)`

- stringcheck︰ 搜尋字串
- stringmatch︰ 找不到的字串
- 開始︰ 開始尋找子字串的位置
- 比較︰ vbTextCompare 或 vbBinaryCompare

**註解︰**  
傳回位置找不到子字串或 0 找不到的位置。

**範例︰**  
`InStr("The quick brown fox","quick")`  
5 Evalues

`InStr("repEated","e",3,vbBinaryCompare)`  
評估為 7

----------
### <a name="instrrev"></a>InStrRev

**描述︰**  
InStrRev 函數字串中找到的子字串的最後一個項目

**語法︰**  
`num InstrRev(str stringcheck, str stringmatch)`  
`num InstrRev(str stringcheck, str stringmatch, num start)`  
`num InstrRev(str stringcheck, str stringmatch, num start, enum compare)`

- stringcheck︰ 搜尋字串
- stringmatch︰ 找不到的字串
- 開始︰ 開始尋找子字串的位置
- 比較︰ vbTextCompare 或 vbBinaryCompare

**註解︰**  
傳回位置找不到子字串或 0 找不到的位置。

**範例︰**  
`InStrRev("abbcdbbbef","bb")`  
傳回 7

----------
### <a name="isbitset"></a>IsBitSet

**描述︰**  
已設定 IsBitSet 測試位元的函數

**語法︰**  
`bool IsBitSet(num value, num flag)`

- 值︰ 是 evaluated.flag 的數值︰ 具有要評估的位元的數值

**範例︰**  
`IsBitSet(&HF,4)`  
會傳回 True，因為位元 「 4 」 設定十六進位值"F"

----------
### <a name="isdate"></a>IsDate

**描述︰**  
如果運算式可評估為 DateTime 類型，然後 IsDate 函數會評估為 True。

**語法︰**  
`bool IsDate(var Expression)`

**註解︰**  
用來決定 CDate() 是否可以順利。

----------
### <a name="isempty"></a>IsEmpty

**描述︰**  
如果屬性存在於 CS 或 MV，但結果為空字串時，IsEmpty 函數評估為 True。

**語法︰**  
`bool IsEmpty(var Expression)`

----------
### <a name="isguid"></a>IsGuid

**描述︰**  
如果無法轉換為 GUID 的字串，然後 IsGuid 函數評估為 true。

**語法︰**  
`bool IsGuid(str GUID)`

**註解︰**  
GUID 定義為字串，下列其中一種模式︰ xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx 或 {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}

用來決定 CGuid() 是否可以順利。

**範例︰**  
`IIF(IsGuid([strAttribute]),CGuid([strAttribute]),NULL)`  
如果 StrAttribute GUID 格式，傳回的二進位表示法，否則傳回 null 值。

----------
### <a name="isnull"></a>IsNull

**描述︰**  
如果運算式評估為 Null，然後 IsNull 函數會傳回 true。

**語法︰**  
`bool IsNull(var Expression)`

**註解︰**  
屬性，null 值表示沒有的屬性。

**範例︰**  
`IsNull([displayName])`  
如果屬性存在於不 CS 或 MV，則傳回 True。

----------
### <a name="isnullorempty"></a>IsNullOrEmpty

**描述︰**  
如果運算式是空值或空白的字串，然後 IsNullOrEmpty 函數會傳回 true。

**語法︰**  
`bool IsNullOrEmpty(var Expression)`

**註解︰**  
屬性，這會評估為 True 如果屬性不存在或但為空字串。  
此函數的反函數值稱為 IsPresent。

**範例︰**  
`IsNullOrEmpty([displayName])`  
如果屬性不存在，或在 CS 或 MV 空字串，則傳回 True。

----------
### <a name="isnumeric"></a>IsNumeric

**描述︰**  
IsNumeric 函數會傳回一個 Boolean 值，指出運算式是否可以評估為數字類型。

**語法︰**  
`bool IsNumeric(var Expression)`

**註解︰**  
用來決定是否 CNum() 就可以成功要剖析的運算式。

----------
### <a name="isstring"></a>IsString

**描述︰**  
可評估運算式為字串類型，然後 IsString 函數評估為 True 時。

**語法︰**  
`bool IsString(var expression)`

**註解︰**  
用來決定是否 CStr() 就可以成功要剖析的運算式。

----------
### <a name="ispresent"></a>IsPresent

**描述︰**  
如果運算式評估為不是空值，不是空的字串，然後 IsPresent 函數會傳回 true。

**語法︰**  
`bool IsPresent(var expression)`

**註解︰**  
此函數的反函數值稱為 IsNullOrEmpty。

**範例︰**  
`Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])`

----------
### <a name="item"></a>項目

**描述︰**  
項目函數會傳回一個項目從多重值的字串/屬性。

**語法︰**  
`var Item(mvstr attribute, num index)`

- 屬性︰ 多重值的屬性
- 索引︰ 索引的多重值的字串中的項目。

**註解︰**  
項目函數很有用與包含函數之後，後者函數傳回至多重值屬性中的項目。

如果索引超出範圍，就會擲回錯誤。

**範例︰**  
`Mid(Item([proxyAddress],Contains([proxyAddress], "SMTP:")),6)`  
傳回的主要電子郵件地址。

----------
### <a name="itemornull"></a>ItemOrNull

**描述︰**  
ItemOrNull 函數會傳回一個項目從多重值的字串/屬性。

**語法︰**  
`var ItemOrNull(mvstr attribute, num index)`

- 屬性︰ 多重值的屬性
- 索引︰ 索引的多重值的字串中的項目。

**註解︰**  
ItemOrNull 函數是包含函數與實用，因為後者函數傳回至多重值屬性中的項目。

如果索引超出範圍，然後傳回 Null 值。

----------
### <a name="join"></a>加入

**描述︰**  
加入函數會採用多重值的字串，然後傳回單一值的字串與特定插入每個項目之間的分隔。

**語法︰**  
`str Join(mvstr attribute)`  
`str Join(mvstr attribute, str Delimiter)`

- 屬性︰ 包含要結合的字串的多重值的屬性。
- 分隔符號︰ 用來分隔傳回字串中的子字串的任何字串。 如果省略，空格字元 (「 」) 使用。 如果分隔符號是零長度字串 ("」) 或執行任何動作，不會使用分隔符號串連清單中的所有項目。

**註解**  
加入與分割函數之間有不一致性致歉。 加入函數字串的陣列，然後使用結合分隔符號字串，以傳回單一字串。 分割函數會採用字串，並加以隔開在分隔符號，以傳回陣列的字串。 不過，主要的差異是加入可以與任何分隔符號字串的字串串連，只分割區隔使用單一字元分隔符號的字串。

**範例︰**  
`Join([proxyAddresses],",")`  
會傳回︰"SMTP:john.doe@contoso.com,smtp:jd@contoso.com"

----------
### <a name="lcase"></a>LCase

**描述︰**  
LCase 函數會將字串中的所有字元都轉換為小寫。

**語法︰**  
`str LCase(str value)`

**範例︰**  
`LCase("TeSt")`  
傳回 「 測試 」。

----------
### <a name="left"></a>向左

**描述︰**  
Left 的函數會傳回指定的字元數，從左邊的字串。

**語法︰**  
`str Left(str string, num NumChars)`

- 字串︰ 若要傳回字元的字串
- NumChars︰ 數字識別的字元數，傳回字串開頭 （左）

**註解︰**  
包含字串中的第一個 numChars 字元的字串︰

- 如果 numChars = 0，傳回空字串。
- 如果 numChars < 0，傳回輸入的字串。
- 如果字串是空值，會傳回空字串。

如果字串包含數字的指定 numChars 比較少字元，則會傳回字串與字串 （也就，包含參數 1 中的所有字元） 相同。

**範例︰**  
`Left("John Doe", 3)`  
傳回 「 Joh 」。

----------
### <a name="len"></a>Len

**描述︰**  
Len 函數會傳回字串中的字元數。

**語法︰**  
`num Len(str value)`

**範例︰**  
`Len("John Doe")`  
傳回 8

----------
### <a name="ltrim"></a>LTrim

**描述︰**  
LTrim 函數移除字串的前置空格。

**語法︰**  
`str LTrim(str value)`

**範例︰**  
`LTrim(" Test ")`  
傳回 「 測試 」

----------
### <a name="mid"></a>Mid

**描述︰**  
Mid 函數會傳回指定的字元數，從字串中的指定位置。

**語法︰**  
`str Mid(str string, num start, num NumChars)`

- 字串︰ 若要傳回字元的字串
- 啟動︰ 將傳回字元的字串中的識別開始的數字
- NumChars︰ 數字識別的字元數，傳回字串中的位置

**註解︰**  
傳回 numChars 字元字串中的位置開始從開始。  
包含從字串中的位置開始 numChars 字元的字串︰

- 如果 numChars = 0，傳回空字串。
- 如果 numChars < 0，傳回輸入的字串。
- 如果開始 > 字串的長度，傳回輸入的字串。
- 如果開始 < = 0，傳回輸入的字串。
- 如果字串是空值，會傳回空字串。

如果不是 numChar 字元位置開始，因為許多字串中的剩餘盡可能會傳回字元。

**範例︰**  
`Mid("John Doe", 3, 5)`  
傳回 「 hn 執行 」。

`Mid("John Doe", 6, 999)`  
傳回 「 華 」

----------
### <a name="now"></a>現在

**描述︰**  
Now 函數會傳回指定目前日期和時間，根據您的電腦系統日期和時間的日期時間。

**語法︰**  
`dt Now()`

----------
### <a name="numfromdate"></a>NumFromDate

**描述︰**  
NumFromDate 函數會傳回日期在 AD 的日期格式。

**語法︰**  
`num NumFromDate(dt value)`

**範例︰**  
`NumFromDate(CDate("2012-01-01 23:00:00"))`  
傳回 129699324000000000

----------
### <a name="padleft"></a>PadLeft

**描述︰**  
PadLeft 函數左邊-板至指定的長度，使用所提供的邊框之字元的字串。

**語法︰**  
`str PadLeft(str string, num length, str padCharacter)`

- 字串︰ 鍵台的字串。
- 長度︰ 整數，代表所要的字串長度。
- padCharacter︰ 字串，其中包含用來作為填補單一字元

**註解︰**

- 如果小於長度字串的長度，則 padCharacter 重複附加至 （左） 字串的長度之前等於長度的開頭。
- PadCharacter 可以是空格字元，但不是能 null 值。
- 如果等於或大於長度字串的長度，則會傳回不變字串。
- 如果字串的長度大於或等於長度，則會傳回字串與字串相同。
- 如果小於長度字串的長度，想要的長度的新字串會傳回包含塞滿 padCharacter 的字串。
- 如果字串是空值，則函數會傳回空字串。

**範例︰**  
`PadLeft("User", 10, "0")`  
傳回 「 000000User 」。

----------
### <a name="padright"></a>PadRight

**描述︰**  
PadRight 函數右邊-板至指定之長度使用所提供的邊框之字元的字串。

**語法︰**  
`str PadRight(str string, num length, str padCharacter)`

- 字串︰ 鍵台的字串。
- 長度︰ 整數，代表所要的字串長度。
- padCharacter︰ 字串，其中包含用來作為填補單一字元

**註解︰**

- 如果小於長度字串的長度，則 padCharacter 重複附加至結尾 （右） 字串長度等於長度之前。
- padCharacter 可以是空格字元，但不是能 null 值。
- 如果等於或大於長度字串的長度，則會傳回不變字串。
- 如果字串的長度大於或等於長度，則會傳回字串與字串相同。
- 如果小於長度字串的長度，想要的長度的新字串會傳回包含塞滿 padCharacter 的字串。
- 如果字串是空值，則函數會傳回空字串。

**範例︰**  
`PadRight("User", 10, "0")`  
傳回 「 User000000 」。

----------
### <a name="pcase"></a>PCase

**描述︰**  
PCase 函數會將第一個字元的字串中的每個空格分隔文字轉換為大寫、 與其他所有字元都轉換為小寫。

**語法︰**  
`String PCase(string)`

**註解︰**

- 此函數不目前提供適當的大小寫轉換為全部大寫，例如縮寫的單字。

**範例︰**  
`PCase("TEsT")`  
傳回 「 測試 」。

`PCase(LCase("TEST"))`  
傳回 「 測試 」

----------
### <a name="randomnum"></a>RandomNum

**描述︰**  
RandomNum 函數會傳回一個隨機數字之間指定的間隔。

**語法︰**  
`num RandomNum(num start, num end)`

- 開始︰ 數字識別的隨機值下限產生
- 結束︰ 數字識別的隨機數上限產生

**範例︰**  
`Random(100,999)`  
可傳回 734。

----------
### <a name="removeduplicates"></a>RemoveDuplicates

**描述︰**  
RemoveDuplicates 函數會採用多重值的字串，並確定每個值是唯一的。

**語法︰**  
`mvstr RemoveDuplicates(mvstr attribute)`

**範例︰**  
`RemoveDuplicates([proxyAddresses])`  
傳回處理過的 proxyAddress 屬性已被移除所有重複的值。

----------
### <a name="replace"></a>取代

**描述︰**  
Replace 函數會取代另一個字串的字串的所有項目。

**語法︰**  
`str Replace(str string, str OldValue, str NewValue)`

- 字串︰ 若要取代的值的字串。
- OldValue: 字串搜尋及取代。
- NewValue︰ 若要取代字串。

**註解︰**  
此函數會辨識下列特殊 moniker:

- \n – 新的一行
- \r-換行
- \t –] 索引標籤

**範例︰**  
`Replace([address],"\r\n",", ")`  
取代 CRLF 逗點和空格，且可能會造成 「 一個 Microsoft 的方式，雷蒙市，「 台北 」，美國 」

----------
### <a name="replacechars"></a>ReplaceChars

**描述︰**  
ReplaceChars 函數會取代找到 ReplacePattern 字串中字元的所有項目。

**語法︰**  
`str ReplaceChars(str string, str ReplacePattern)`

- 字串︰ 若要取代的字元的字串。
- ReplacePattern︰ 包含的字典，若要取代的字元的字串。

格式是 {source1，而使得}: {target1}，{source2}: {target2}，{sourceN}，{targetN} 來源是找出並目標要取代的字串的字元的位置。

**註解︰**

- 函數會採用的已定義之來源的每個項目，然後加以取代目標。
- 來源必須是一個 (unicode) 字元。
- 來源不得為空白或超過一個字元 （剖析錯誤）。
- 目標可以有多個字元，例如 ö:oe、 β:ss。
- 目標可以是空白指出字元應該會移除。
- 來源是區分大小寫，而且必須完全相符。
- ，（逗號） 和: （冒號） 會保留的字元，無法使用此函數會被取代。
- 會略過空格及其他白色 ReplacePattern 字串中的字元。

**範例︰**  
`%ReplaceString% = ’:,Å:A,Ä:A,Ö:O,å:a,ä:a,ö,o`

`ReplaceChars("Räksmörgås",%ReplaceString%)`  
傳回 Raksmorgas

`ReplaceChars("O’Neil",%ReplaceString%)`  
若要移除定義會傳回 「 ONeil 」，一個刻度。

----------
### <a name="right"></a>權限

**描述︰**  
Right 函數會傳回指定的字元數，從字串的右邊 （結束）。

**語法︰**  
`str Right(str string, num NumChars)`

- 字串︰ 若要傳回字元的字串
- NumChars︰ 數字識別的字元數，傳回字串的 （右） 結尾

**註解︰**  
從字串的最後一個位置，會傳回 NumChars 字元。

包含字串中的最後一個 numChars 字元的字串︰

- 如果 numChars = 0，傳回空字串。
- 如果 numChars < 0，傳回輸入的字串。
- 如果字串是空值，會傳回空字串。

如果字串包含數字的指定 NumChars 比較少字元，則會傳回字串與字串相同。

**範例︰**  
`Right("John Doe", 3)`  
傳回 「 華 」。

----------
### <a name="rtrim"></a>RTrim

**描述︰**  
RTrim 函數移除字串結尾空格。

**語法︰**  
`str RTrim(str value)`

**範例︰**  
`RTrim(" Test ")`  
傳回 「 測試 」。

----------
### <a name="split"></a>分割

**描述︰**  
分割函數會採用字串分隔的分隔符號，並讓它的多重值的字串。

**語法︰**  
`mvstr Split(str value, str delimiter)`  
`mvstr Split(str value, str delimiter, num limit)`

- 值︰ 分隔符號字元來分隔的字串。
- 分隔符號︰ 單一作為分隔符號字元。
- 限制︰ 最大可傳回的值。

**範例︰**  
`Split("SMTP:john.doe@contoso.com,smtp:jd@contoso.com",",")`  
多重值以傳回字串 2 個項目適合 proxyAddress 屬性。

----------
### <a name="stringfromguid"></a>StringFromGuid

**描述︰**  
StringFromGuid 函數會採用二進位 GUID 然後將其轉換成字串

**語法︰**  
`str StringFromGuid(bin GUID)`

----------
### <a name="stringfromsid"></a>StringFromSid

**描述︰**  
StringFromSid 函數將轉換位元組陣列，包含字串的安全性識別碼。

**語法︰**  
`str StringFromSid(bin ObjectSID)`  

----------
### <a name="switch"></a>切換

**描述︰**  
Switch 函數用來傳回單一值根據評估的條件。

**語法︰**  
`var Switch(exp expr1, var value1[, exp expr2, var value … [, exp expr, var valueN]])`

- expr︰ 您想要評估的 Variant 運算式。
- 值︰ 會對應運算式為 True 時傳回的值。

**註解︰**  
切換函數的引數清單包含運算式和值。 從左到右，評估運算式，並傳回相關聯的第一個運算式評估為 True 的值。 如果部分配對不正確，就會發生執行階段錯誤。

例如，如果 expr1 為 True，切換會傳回 value1。 如果 expr-1 為 False，但 expr 2 為 True，切換會傳回值為 2，依此類推。

切換傳回無如果︰

- 沒有一個運算式為 true，則。
- 第一個為 True 的運算式具有 Null 的對應值。

切換會評估所有運算式，即使會傳回只有一個。 因此，您應該監看非預期的側邊效果。 例如，如果評估的任何運算式產生除數為零的錯誤，發生錯誤。

值，也可以讓錯誤函數，傳回自訂字串。

**範例︰**  
`Switch([city] = "London", "English", [city] = "Rome", "Italian", [city] = "Paris", "French", True, Error("Unknown city"))`  
傳回的一些主要的城市的語言，否則會傳回錯誤。

----------
### <a name="trim"></a>修剪

**描述︰**  
Trim 函數移除前置及結尾空格的字串。

**語法︰**  
`str Trim(str value)`  

**範例︰**  
`Trim(" Test ")`  
傳回 「 測試 」。

`Trim([proxyAddresses])`  
移除前置及結尾空格 proxyAddress 屬性中的每個值。

----------
### <a name="ucase"></a>UCase

**描述︰**  
UCase 函數會將字串中的所有字元都轉換成大寫。

**語法︰**  
`str UCase(str string)`

**範例︰**  
`UCase("TeSt")`  
傳回 「 測試 」。

----------
### <a name="word"></a>Word

**描述︰**  
Word 函數會傳回包含在字串內，根據參數描述分隔符號，以傳回字與使用 word。

**語法︰**  
`str Word(str string, num WordNumber, str delimiters)`

- 字串︰ 傳回從文字字串。
- 識別哪些 word 數字的數字會傳回 WordNumber:。
- 分隔符號︰ 代表應該用來識別文字 delimiter(s) 字串

**註解︰**  
每個以其中一種分隔符號字元的字串中字元的字串會識別為 [文字︰

- 如果數值 < 1，會傳回空字串。
- 如果字串是空值，會傳回空字串。

如果字串包含數字的文字，小於字串不含任何由分隔符號的單字，則會傳回空字串。

**範例︰**  
`Word("The quick brown fox",3," ")`  
傳回 「 深褐色 」

`Word("This,string!has&many separators",3,",!&#")`  
會傳回 「 的 」

## <a name="additional-resources"></a>其他資源

* [了解宣告式佈建運算式](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md)
* [Azure AD 連線同步處理︰ 自訂同步處理選項](active-directory-aadconnectsync-whatis.md)
* [整合您的內部部署識別與 Azure Active Directory](active-directory-aadconnect.md)
