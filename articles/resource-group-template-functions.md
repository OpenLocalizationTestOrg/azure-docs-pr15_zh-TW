<properties
   pageTitle="資源管理員範本函數 |Microsoft Azure"
   description="描述使用 Azure 資源管理員範本中擷取的值，使用字串和數字，並擷取部署資訊的函數。"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/12/2016"
   ms.author="tomfitz"/>

# <a name="azure-resource-manager-template-functions"></a>Azure 資源管理員範本函數

本主題即說明您可以使用 Azure 資源管理員範本中的所有函數。

範本函式和其參數是不區分大小寫。 例如，資源管理員會解析**variables('var1')**和**VARIABLES('VAR1')**作為相同。 評估時，除非函數明文修改 （例如 toUpper 或 toLower） 的大小寫，函數會保留大小寫。 特定的資源類型可能有大小寫的需求，不管資料如何評估函數。

## <a name="numeric-functions"></a>數字的函數

資源管理員使用整數提供下列功能︰

- [新增](#add)
- [copyIndex](#copyindex)
- [div](#div)
- [int](#int)
- [mod](#mod)
- [mul](#mul)
- [sub](#sub)


<a id="add" />
### <a name="add"></a>新增

**新增 (operand1 operand2)**

傳回兩個提供整數的總和。

| 參數                          | 所需 | 描述
| :--------------------------------: | :------: | :----------
| operand1                           |   [是]    | 若要新增的第一個整數。
| operand2                           |   [是]    | 若要新增的第二個整數。

下列範例會將兩個參數。

    "parameters": {
      "first": {
        "type": "int",
        "metadata": {
          "description": "First integer to add"
        }
      },
      "second": {
        "type": "int",
        "metadata": {
          "description": "Second integer to add"
        }
      }
    },
    ...
    "outputs": {
      "addResult": {
        "type": "int",
        "value": "[add(parameters('first'), parameters('second'))]"
      }
    }

<a id="copyindex" />
### <a name="copyindex"></a>copyIndex

**copyIndex(offset)**

傳回目前的索引反覆運算循環播放。 

| 參數                          | 所需 | 描述
| :--------------------------------: | :------: | :----------
| 位移                           |   無    | 若要新增至目前反覆運算值量。

**複製**物件永遠會使用此函數。 如何使用**copyIndex**的完整描述，請參閱[建立資源 Azure 資源管理員] 中的多個執行個體](resource-group-create-multiple.md)。

下列範例會顯示複製迴圈及名稱中包含的索引值。 

    "resources": [ 
      { 
        "name": "[concat('examplecopy-', copyIndex())]", 
        "type": "Microsoft.Web/sites", 
        "copy": { 
          "name": "websitescopy", 
          "count": "[parameters('count')]" 
        }, 
        ...
      }
    ]


<a id="div" />
### <a name="div"></a>div

**div （operand1、 operand2）**

傳回兩個提供整數的整數除法運算。

| 參數                          | 所需 | 描述
| :--------------------------------: | :------: | :----------
| operand1                           |   [是]    | 分割的整數。
| operand2                           |   [是]    | 用來分隔的整數。 無法為 0。

下列範例會將另一個參數的參數。

    "parameters": {
      "first": {
        "type": "int",
        "metadata": {
          "description": "Integer being divided"
        }
      },
      "second": {
        "type": "int",
        "metadata": {
          "description": "Integer used to divide"
        }
      }
    },
    ...
    "outputs": {
      "divResult": {
        "type": "int",
        "value": "[div(parameters('first'), parameters('second'))]"
      }
    }

<a id="int" />
### <a name="int"></a>int

**int(valueToConvert)**

將指定的值轉換為整數。

| 參數                          | 所需 | 描述
| :--------------------------------: | :------: | :----------
| valueToConvert                     |   [是]    | 若要轉換成整數值。 類型的值只能字串或整數。

下列範例會將使用者提供的參數值轉換成整數。

    "parameters": {
        "appId": { "type": "string" }
    },
    "variables": { 
        "intValue": "[int(parameters('appId'))]"
    }


<a id="mod" />
### <a name="mod"></a>mod

**mod （operand1、 operand2）**

傳回整數除法使用兩個提供的整數部分。

| 參數                          | 所需 | 描述
| :--------------------------------: | :------: | :----------
| operand1                           |   [是]    | 分割的整數。
| operand2                           |   [是]    | 用來分隔，必須從 0 不同的整數。

下列範例會傳回 multiple 所得的餘數一個由另一個參數的參數。

    "parameters": {
      "first": {
        "type": "int",
        "metadata": {
          "description": "Integer being divided"
        }
      },
      "second": {
        "type": "int",
        "metadata": {
          "description": "Integer used to divide"
        }
      }
    },
    ...
    "outputs": {
      "modResult": {
        "type": "int",
        "value": "[mod(parameters('first'), parameters('second'))]"
      }
    }

<a id="mul" />
### <a name="mul"></a>mul

**mul （operand1、 operand2）**

傳回兩個提供的整數乘。

| 參數                          | 所需 | 描述
| :--------------------------------: | :------: | :----------
| operand1                           |   [是]    | 將第一個整數。
| operand2                           |   [是]    | 將第二個整數。

下列範例會將乘以一個由另一個參數的參數。

    "parameters": {
      "first": {
        "type": "int",
        "metadata": {
          "description": "First integer to multiply"
        }
      },
      "second": {
        "type": "int",
        "metadata": {
          "description": "Second integer to multiply"
        }
      }
    },
    ...
    "outputs": {
      "mulResult": {
        "type": "int",
        "value": "[mul(parameters('first'), parameters('second'))]"
      }
    }

<a id="sub" />
### <a name="sub"></a>sub

**sub （operand1、 operand2）**

傳回兩個提供整數的減法。

| 參數                          | 所需 | 描述
| :--------------------------------: | :------: | :----------
| operand1                           |   [是]    | 會從減去的整數。
| operand2                           |   [是]    | 減去的整數。

下列範例中減去另一個參數的參數。

    "parameters": {
      "first": {
        "type": "int",
        "metadata": {
          "description": "Integer subtracted from"
        }
      },
      "second": {
        "type": "int",
        "metadata": {
          "description": "Integer to subtract"
        }
      }
    },
    ...
    "outputs": {
      "subResult": {
        "type": "int",
        "value": "[sub(parameters('first'), parameters('second'))]"
      }
    }

## <a name="string-functions"></a>字串函數

資源管理員使用的字串提供下列功能︰

- [base64](#base64)
- [concat 函數](#concat)
- [長度](#lengthstring)
- [padLeft](#padleft)
- [取代](#replace)
- [略過](#skipstring)
- [分割](#split)
- [字串](#string)
- [子字串](#substring)
- [記錄](#takestring)
- [toLower](#tolower)
- [toUpper](#toupper)
- [修剪](#trim)
- [uniqueString](#uniquestring)
- [uri](#uri)


<a id="base64" />
### <a name="base64"></a>base64

**base64 (inputString)**

會傳回 base64 輸入字串。

| 參數                          | 所需 | 描述
| :--------------------------------: | :------: | :----------
| inputString                        |   [是]    | 若要傳回為 base64 表示字串值。

下列範例會示範如何使用 base64 函數。

    "variables": {
      "usernameAndPassword": "[concat('parameters('username'), ':', parameters('password'))]",
      "authorizationHeader": "[concat('Basic ', base64(variables('usernameAndPassword')))]"
    }

<a id="concat" />
### <a name="concat---string"></a>concat-字串

**concat 函數 （string1、 string2 string3，...）**

結合多個字串值，並傳回串連的字串。 

| 參數                          | 所需 | 描述
| :--------------------------------: | :------: | :----------
| string1                        |   [是]    | 若要連接字串值。
| 其他字串             |   無     | 字串串連的值。

此函數可採取任何引數的數字，並可以接受字串或參數的陣列。 如需串連陣列的範例，請參閱[concat-陣列](#concatarray)。

下列範例會示範如何合併多個字串值，以傳回串連的字串。

    "outputs": {
        "siteUri": {
          "type": "string",
          "value": "[concat('http://', reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
        }
    }


<a id="lengthstring" />
### <a name="length---string"></a>長度-字串

**length(string)**

傳回字串中的字元數。

| 參數                          | 所需 | 描述
| :--------------------------------: | :------: | :----------
| 字串                        |   [是]    | 若要使用的字元數字串值。

如需使用陣列長度的範例，請參閱[長度-陣列](#length)。

下列範例會傳回字串中的字元數。 

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "nameLength": "[length(parameters('appName'))]"
    }
        

<a id="padleft" />
### <a name="padleft"></a>padLeft

**padLeft （valueToPad、 totalLength paddingCharacter）**

藉由新增左邊的字元，直到抵達總指定的長度傳回靠右對齊的字串。
  
| 參數                          | 所需 | 描述
| :--------------------------------: | :------: | :----------
| valueToPad                         |   [是]    | 字串或 int 靠右對齊。
| totalLength                        |   [是]    | 傳回字串中的字元總數。
| paddingCharacter                   |   無     | 要用於左側邊框的總長度為止的字元。 預設值是一個空格。

下列範例會示範如何鍵台新增零字元字串達到 10 個字元的使用者所提供的參數值。 如果原始的參數值大於 10 個字元，則會不新增任何字元。

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "paddedAppName": "[padLeft(parameters('appName'),10,'0')]"
    }

<a id="replace" />
### <a name="replace"></a>取代

**取代 （originalString、 oldCharacter newCharacter）**

傳回一個字元的所有執行個體的新字串中指定的字串，由另一個字元。

| 參數                          | 所需 | 描述
| :--------------------------------: | :------: | :----------
| originalString                     |   [是]    | 具有所有執行個體由另一個字元的一個字元的字串。
| oldCharacter                       |   [是]    | 要移除原始字串的字元。
| newCharacter                       |   [是]    | 若要移除的字元取代新增字元。

下列範例會示範如何移除使用者提供的字串中的所有虛線。

    "parameters": {
        "identifier": { "type": "string" }
    },
    "variables": { 
        "newidentifier": "[replace(parameters('identifier'),'-','')]"
    }

<a id="skipstring" />
### <a name="skip---string"></a>略過-字串
**略過 （originalValue、 numberToSkip）**

字串中指定的數字後，會傳回包含的所有字元的字串。

| 參數                          | 所需 | 描述
| :--------------------------------: | :------: | :----------
| originalValue                      |   [是]    | 要用於略過的字串。
| numberToSkip                       |   [是]    | 若要跳過的字元數。 如果此值為 0 或較少，則會傳回字串中的所有字元。 如果是大於字串的長度，則會傳回空字串。 

如需使用陣列略過的範例，請參閱[略過-陣列](#skip)。

下列範例會略過指定的字串中字元數。

    "parameters": {
      "first": {
        "type": "string",
        "metadata": {
          "description": "Value to use for skipping"
        }
      },
      "second": {
        "type": "int",
        "metadata": {
          "description": "Number of characters to skip"
        }
      }
    },
    "resources": [
    ],
    "outputs": {
      "return": {
        "type": "string",
        "value": "[skip(parameters('first'),parameters('second'))]"
      }
    }


<a id="split" />
### <a name="split"></a>分割

**分割 （inputString、 delimiterString）**

**分割 （inputString、 delimiterArray）**

傳回陣列的字串包含輸入字串，由指定的分隔符號分隔的子字串。

| 參數                          | 所需 | 描述
| :--------------------------------: | :------: | :----------
| inputString                        |   [是]    | 要分割的字串。
| 分隔符號                          |   [是]    | 若要使用，分隔符號可以是單一字串陣列。

下列範例會分隔並以逗號輸入的字串。

    "parameters": {
        "inputString": { "type": "string" }
    },
    "variables": { 
        "stringPieces": "[split(parameters('inputString'), ',')]"
    }

下一個範例分隔逗號或分號輸入的字串。

    "variables": {
      "stringToSplit": "test1,test2;test3",
      "delimiters": [ ",", ";" ]
    },
    "resources": [ ],
    "outputs": {
      "exampleOutput": {
        "value": "[split(variables('stringToSplit'), variables('delimiters'))]",
        "type": "array"
      }
    }

<a id="string" />
### <a name="string"></a>字串

**string(valueToConvert)**

將指定的值轉換為字串。

| 參數                          | 所需 | 描述
| :--------------------------------: | :------: | :----------
| valueToConvert                     |   [是]    | 要轉換成字串的值。 可以轉換任何類型的值，包括物件及陣列。

下列範例會將使用者提供的參數值轉換成字串。

    "parameters": {
      "jsonObject": {
        "type": "object",
        "defaultValue": {
          "valueA": 10,
          "valueB": "Example Text"
        }
      },
      "jsonArray": {
        "type": "array",
        "defaultValue": [ "a", "b", "c" ]
      },
      "jsonInt": {
        "type": "int",
        "defaultValue": 5
      }
    },
    "variables": { 
      "objectString": "[string(parameters('jsonObject'))]",
      "arrayString": "[string(parameters('jsonArray'))]",
      "intString": "[string(parameters('jsonInt'))]"
    }

<a id="substring" />
### <a name="substring"></a>子字串

**子字串 （stringToParse、 startIndex 長度）**

傳回指定的字元位置開始，並包含指定的字元數的子字串。

| 參數                          | 所需 | 描述
| :--------------------------------: | :------: | :----------
| stringToParse                     |   [是]    | 原始的字串，要從中擷取子字串。
| startIndex                         | 無      | 起始起始字元位置的子字串。
| 長度                             | 無      | 子字串的字元數。

下列範例會起參數中的前三個字元。

    "parameters": {
        "inputString": { "type": "string" }
    },
    "variables": { 
        "prefix": "[substring(parameters('inputString'), 0, 3)]"
    }

<a id="takestring" />
### <a name="take---string"></a>取回-字串
**記錄 （originalValue、 numberToTake）**

從字串的開頭傳回包含指定的字元數的字串。

| 參數                          | 所需 | 描述
| :--------------------------------: | :------: | :----------
| originalValue                      |   [是]    | 要採取的個字元的字串。
| numberToTake                       |   [是]    | 若要採取的字元數。 如果此值為 0 或較少，則會傳回空字串。 如果是大於指定的字串的長度，則會傳回字串中的所有字元。

範例使用陣列中的記錄，請參閱[需要-陣列](#take)。

下列範例會指定的字串的字元數。

    "parameters": {
      "first": {
        "type": "string",
        "metadata": {
          "description": "Value to use for taking"
        }
      },
      "second": {
        "type": "int",
        "metadata": {
          "description": "Number of characters to take"
        }
      }
    },
    "resources": [
    ],
    "outputs": {
      "return": {
        "type": "string",
        "value": "[take(parameters('first'), parameters('second'))]"
      }
    }

<a id="tolower" />
### <a name="tolower"></a>toLower

**toLower(stringToChange)**

將指定的字串轉換為小寫。

| 參數                          | 所需 | 描述
| :--------------------------------: | :------: | :----------
| stringToChange                     |   [是]    | 要轉換成小寫字母的字串。

下列範例會將使用者提供的參數值轉換為小寫。

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "lowerCaseAppName": "[toLower(parameters('appName'))]"
    }

<a id="toupper" />
### <a name="toupper"></a>toUpper

**toUpper(stringToChange)**

將指定的字串轉換成大寫。

| 參數                          | 所需 | 描述
| :--------------------------------: | :------: | :----------
| stringToChange                     |   [是]    | 要轉換成大寫的字串。

下列範例會將使用者提供的參數值轉換成大寫。

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "upperCaseAppName": "[toUpper(parameters('appName'))]"
    }

<a id="trim" />
### <a name="trim"></a>修剪

**修剪 (stringToTrim)**

從指定的字串中移除所有的前置及結尾空格字元。

| 參數                          | 所需 | 描述
| :--------------------------------: | :------: | :----------
| stringToTrim                       |   [是]    | 修剪字串。

下列範例會修剪空白字元使用者提供的參數值。

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "trimAppName": "[trim(parameters('appName'))]"
    }

<a id="uniquestring" />
### <a name="uniquestring"></a>uniqueString

**uniqueString (baseString，...)**

建立確定雜湊字串的參數值。 

| 參數                          | 所需 | 描述
| :--------------------------------: | :------: | :----------
| baseString      |   [是]    | 字串雜湊函數中用來建立唯一的字串。
| 視需要額外的參數    | 無       | 您可以新增任何數量的字串，視需要建立指定的唯一性層級的值。

當您需要建立資源的唯一名稱時，這個功能很有幫助。 您提供限制結果的唯一性範圍的參數值。 您可以指定是否下訂閱、 資源] 群組中或部署唯一名稱。 

傳回的值不隨機的字串，但而雜湊函數的結果。 傳回的值是 13 個字元。 您無法全域唯一。 您可能會想要組合值的前置詞，從您的命名慣例，來建立有意義的名稱。 下列範例會顯示所傳回的值的格式。 當然的實際值會因提供的參數。

    tcvhiyu5h2o5o

下列範例會顯示如何使用 uniqueString 建立常用的層級的唯一值。

唯一訂閱的範圍

    "[uniqueString(subscription().subscriptionId)]"

唯一範圍資源群組

    "[uniqueString(resourceGroup().id)]"

唯一範圍部署資源群組

    "[uniqueString(resourceGroup().id, deployment().name)]"
    
下列範例會示範如何建立唯一的名稱儲存帳戶，根據您的資源群組 （此資源] 群組的名稱不是唯一的 if 建構相同的方式）。

    "resources": [{ 
        "name": "[concat('contosostorage', uniqueString(resourceGroup().id))]", 
        "type": "Microsoft.Storage/storageAccounts", 
        ...



<a id="uri" />
### <a name="uri"></a>uri

**uri （baseUri、 relativeUri）**

藉由組合 baseUri 以及 relativeUri 字串建立絕對 URI。

| 參數                          | 所需 | 描述
| :--------------------------------: | :------: | :----------
| baseUri                            |   [是]    | 基底 uri 字串。
| relativeUri                        |   [是]    | 若要新增至基底 uri 字串相對 uri 字串。

**BaseUri**參數的值可包含特定檔案，但建構 URI 時使用基本的路徑。 例如，傳遞**http://contoso.com/resources/azuredeploy.json**做為 baseUri 基底 URI **http://contoso.com/resources/**的參數結果。

下列範例會示範如何建構根據父樣板的值的巢狀範本的連結。

    "templateLink": "[uri(deployment().properties.templateLink.uri, 'nested/azuredeploy.json')]"

## <a name="array-functions"></a>陣列函數

資源管理員提供數種函數使用陣列值。

- [concat 函數](#concatarray)
- [長度](#length)
- [略過](#skip)
- [記錄](#take)

若要取得分隔值的字串值的陣列，請參閱[分割](#split)。

<a id="concatarray" />
### <a name="concat---array"></a>concat 函數的陣列

**concat 函數 （array1、 array2 array3，...）**

結合多個陣列，並傳回串連的陣列。 

| 參數                          | 所需 | 描述
| :--------------------------------: | :------: | :----------
| array1                        |   [是]    | 若要串連陣列。
| 其他的陣列             |   無     | 若要連接的陣列。

此函數可採取任何引數的數字，並可以接受字串或參數的陣列。 串連的字串值的範例，請參閱[concat-字串](#concat)。

下列範例會示範如何合併兩個陣列。

    "parameters": {
        "firstarray": {
            type: "array"
        }
        "secondarray": {
            type: "array"
        }
     },
     "variables": {
         "combinedarray": "[concat(parameters('firstarray'), parameters('secondarray'))]
     }
        

<a id="length" />
### <a name="length---array"></a>長度-陣列

**length(array)**

傳回陣列中的項目數。

| 參數                          | 所需 | 描述
| :--------------------------------: | :------: | :----------
| 陣列                        |   [是]    | 若要使用的元素數目陣列。

若要指定重複次數，建立資源時，您可以使用陣列使用此函數。 在下列範例中，若要建立網站時，使用名稱的陣列請參數**siteNames** 。

    "copy": {
        "name": "websitescopy",
        "count": "[length(parameters('siteNames'))]"
    }

如需關於陣列中使用此函數的詳細資訊，請參閱[建立資源 Azure 資源管理員] 中的多個執行個體](resource-group-create-multiple.md)。 

如需使用字串值長度的範例，請參閱[長度的字串](#lengthstring)。

<a id="skip" />
### <a name="skip---array"></a>略過-陣列
**略過 （originalValue、 numberToSkip）**

陣列中指定的數字後，會傳回陣列的所有項目。

| 參數                          | 所需 | 描述
| :--------------------------------: | :------: | :----------
| originalValue                      |   [是]    | 若要使用的略過陣列。
| numberToSkip                       |   [是]    | 若要跳過的項目數目。 如果此值為 0 或較少，則會傳回陣列中的所有項目。 如果是大於陣列的長度，則會傳回空白陣列。 

如需使用字串略過的範例，請參閱[略過的字串](#skipstring)。

下列範例會略過指定的數字陣列中的項目。

    "parameters": {
      "first": {
        "type": "array",
        "metadata": {
          "description": "Values to use for skipping"
        },
        "defaultValue": [ "one", "two", "three" ]
      },
      "second": {
        "type": "int",
        "metadata": {
          "description": "Number of elements to skip"
        }
      }
    },
    "resources": [
    ],
    "outputs": {
      "return": {
        "type": "array",
        "value": "[skip(parameters('first'), parameters('second'))]"
      }
    }

<a id="take" />
### <a name="take---array"></a>需要-陣列
**記錄 （originalValue、 numberToTake）**

傳回陣列的開頭為指定的項目數的陣列。

| 參數                          | 所需 | 描述
| :--------------------------------: | :------: | :----------
| originalValue                      |   [是]    | 陣列，才能從項目。
| numberToTake                       |   [是]    | 若要採取的項目數目。 如果此值為 0 或較少，則會傳回空白陣列。 如果是大於指定陣列的長度，則會傳回陣列中的所有項目。

如需使用字串筆記的範例，請參閱[採取的字串](#takestring)。

下列範例會指定的數字陣列中的項目。

    "parameters": {
      "first": {
        "type": "array",
        "metadata": {
          "description": "Values to use for taking"
        },
        "defaultValue": [ "one", "two", "three" ]
      },
      "second": {
        "type": "int",
        "metadata": {
          "description": "Number of elements to take"
        }
      }
    },
    "resources": [
    ],
    "outputs": {
      "return": {
        "type": "array",
        "value": "[take(parameters('first'),parameters('second'))]"
      }
    }

## <a name="deployment-value-functions"></a>部署值函數

資源管理員提供下列函數從 [範本與值的部署相關的章節取得值︰

- [部署](#deployment)
- [參數](#parameters)
- [變數](#variables)

若要取得資源、 資源群組]，或訂閱的值，請參閱[資源函數](#resource-functions)。

<a id="deployment" />
### <a name="deployment"></a>部署

**deployment()**

傳回目前的部署作業的相關資訊。

此函數會傳回傳遞部署期間的物件。 傳回之物件中的內容而有所不同部署物件是否傳送連結或內嵌物件。 

當部署物件過去內嵌，例如，使用 PowerShell 的 Azure **-TemplateFile**參數指向的本機檔案時，所傳回的物件就會有下列格式︰

    {
        "name": "",
        "properties": {
            "template": {
                "$schema": "",
                "contentVersion": "",
                "parameters": {},
                "variables": {},
                "resources": [
                ],
                "outputs": {}
            },
            "parameters": {},
            "mode": "",
            "provisioningState": ""
        }
    }

當傳遞的物件是為連結，例如使用時**-TemplateUri**參數指向遠端物件，則會傳回物件以下列格式。 

    {
        "name": "",
        "properties": {
            "templateLink": {
                "uri": ""
            },
            "template": {
                "$schema": "",
                "contentVersion": "",
                "parameters": {},
                "variables": {},
                "resources": [],
                "outputs": {}
            },
            "parameters": {},
            "mode": "",
            "provisioningState": ""
        }
    }

下列範例會示範如何使用 deployment() 連結至父樣板的 URI 所依據的其他範本。

    "variables": {  
        "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"  
    }  

<a id="parameters" />
### <a name="parameters"></a>參數

**參數 (parameterName)**

傳回參數值。 指定的參數名稱必須定義範本的 [參數] 區段中。

| 參數                          | 所需 | 描述
| :--------------------------------: | :------: | :----------
| parameterName                      |   [是]    | 若要傳回參數的名稱。

下列範例顯示簡化的使用參數函數。

    "parameters": { 
      "siteName": {
          "type": "string"
      }
    },
    "resources": [
       {
          "apiVersion": "2014-06-01",
          "name": "[parameters('siteName')]",
          "type": "Microsoft.Web/Sites",
          ...
       }
    ]

<a id="variables" />
### <a name="variables"></a>變數

**變數 (variableName)**

傳回值的變數。 指定的變數名稱必須定義範本的 [變數] 區段中。

| 參數                          | 所需 | 描述
| :--------------------------------: | :------: | :----------
| 變數名稱                      |   [是]    | 若要傳回變數的名稱。

下列範例會使用變數值。

    "variables": {
      "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
      {
        "type": "Microsoft.Storage/storageAccounts",
        "name": "[variables('storageName')]",
        ...
      }
    ],

## <a name="resource-functions"></a>資源函數

資源管理員會提供下列功能，用於取得資源值︰

- [listKeys 和清單值](#listkeys)
- [提供者](#providers)
- [參照](#reference)
- [resourceGroup](#resourcegroup)
- [預設](#resourceid)
- [訂閱](#subscription)

若要取得值參數、 變數，或目前的部署，請參閱[部署值函數](#deployment-value-functions)。

<a id="listkeys" />
<a id="list" />
### <a name="listkeys-and-listvalue"></a>listKeys 和清單值

**listKeys resourceName 或 resourceIdentifier (apiVersion）**

**清單值 （resourceName 或 resourceIdentifier、 apiVersion）**

傳回值的任何支援的清單中作業的資源類型。 最常見的使用方式是**listKeys**。 
  
| 參數                          | 所需 | 描述
| :--------------------------------: | :------: | :----------
| resourceName 或 resourceIdentifier |   [是]    | 資源的唯一識別碼。
| apiVersion                         |   [是]    | 資源執行階段狀態 API 版本。

開始使用**清單**可以是任何作業在範本中使用函數。 可用的作業包含**listKeys**，不僅**清單**、 **listAdminKeys**及**listStatus**等的作業。 若要判斷資源有哪些類型的清單作業，請使用下列 PowerShell 命令。

    Get-AzureRmProviderOperation -OperationSearchString *  | where {$_.Operation -like "*list*"} | FT Operation

或者，您也可以擷取與 Azure CLI 清單。 下列範例會擷取**apiapps**，所有的作業，並使用 JSON 公用程式[jq](http://stedolan.github.io/jq/download/)篩選清單作業。

    azure provider operations show --operationSearchString */apiapps/* --json | jq ".[] | select (.operation | contains(\"list\"))"

使用[預設函數](#resourceid)，或使用的格式，您可以指定預設**{providerNamespace} / {resourceType} / {resourceName}**。

下列範例會示範如何在 [輸出] 區段中的儲存空間帳戶傳回主要和次要鍵。

    "outputs": { 
      "listKeysOutput": { 
        "value": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2016-01-01')]", 
        "type" : "object" 
      } 
    } 

傳回的 listKeys 物件具有下列格式︰

    {
      "keys": [
        {
          "keyName": "key1",
          "permissions": "Full",
          "value": "{value}"
        },
        {
          "keyName": "key2",
          "permissions": "Full",
          "value": "{value}"
        }
      ]
    }

<a id="providers" />
### <a name="providers"></a>提供者

**提供者 （providerNamespace，[resourceType]）**

傳回資源提供者和其支援的資源類型的相關資訊。 如果您不提供的資源類型，則函數會傳回所有支援的類型資源提供者。

| 參數                          | 所需 | 描述
| :--------------------------------: | :------: | :----------
| providerNamespace                  |   [是]    | 命名空間的提供者
| resourceType                       |   無     | 指定的命名空間中的資源類型。

每個支援的類型會傳回以下列格式。 不保證陣列順序。

    {
        "resourceType": "",
        "locations": [ ],
        "apiVersions": [ ]
    }

下列範例會示範如何使用提供者函數︰

    "outputs": {
        "exampleOutput": {
            "value": "[providers('Microsoft.Storage', 'storageAccounts')]",
            "type" : "object"
        }
    }

<a id="reference" />
### <a name="reference"></a>參照

**參照 （resourceName 或 resourceIdentifier，[apiVersion]）**

傳回代表另一個資源執行階段狀態的物件。

| 參數                          | 所需 | 描述
| :--------------------------------: | :------: | :----------
| resourceName 或 resourceIdentifier |   [是]    | 名稱或資源的唯一識別碼。
| apiVersion                         |   無     | 指定資源的 API 版本。 資源不提供相同的範本中時，請包含此參數。

**參照**函數衍生從執行階段狀態，其值，因此無法使用 [變數] 區段中。 它可以用於輸出的範本] 區段中。

使用參考函數，您隱含宣告如果參照的資源提供相同的範本中的某個資源取決於其他的資源。 您不需要使用**dependsOn**屬性。 直到參照的資源已經完成部署，則不會評估此函數。

下列範例會參照已部署在同一個範本的儲存空間帳戶。

    "outputs": {
        "NewStorage": {
            "value": "[reference(parameters('storageAccountName'))]",
            "type" : "object"
        }
    }

下列範例會參照未部署此範本，但部署資源為相同的 [資源] 群組中的儲存空間帳戶。

    "outputs": {
        "ExistingStorage": {
            "value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2016-01-01')]",
            "type" : "object"
        }
    }

您可以從傳回的物件，例如 blob 端點 URI，擷取特定的值，如下列範例所示。

    "outputs": {
        "BlobUri": {
            "value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2016-01-01').primaryEndpoints.blob]",
            "type" : "string"
        }
    }

下列範例會參照另一個資源群組中的儲存空間帳戶。

    "outputs": {
        "BlobUri": {
            "value": "[reference(resourceId(parameters('relatedGroup'), 'Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2016-01-01').primaryEndpoints.blob]",
            "type" : "string"
        }
    }

依資源類型而定**參考**函數所傳回的物件上的內容。 若要查看的屬性名稱和資源類型的值，建立簡單的範本會傳回**輸出**] 區段中的物件。 如果您有該類型的現有的資源，您的範本只會傳回物件沒有部署任何新的資源。 如果您沒有該類型的現有的資源，您的範本部署只有該類型，並傳回物件。 然後，新增這些屬性動態擷取的值，部署時需要其他範本。 

<a id="resourcegroup" />
### <a name="resourcegroup"></a>resourceGroup

**resourceGroup()**

傳回表示目前的 [資源] 群組的物件。 

傳回的物件是以下列格式︰

    {
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
      "name": "{resourceGroupName}",
      "location": "{resourceGroupLocation}",
      "tags": {
      },
      "properties": {
        "provisioningState": "{status}"
      }
    }

下列範例會使用資源群組的位置，來指定網站的位置。

    "resources": [
       {
          "apiVersion": "2014-06-01",
          "type": "Microsoft.Web/sites",
          "name": "[parameters('siteName')]",
          "location": "[resourceGroup().location]",
          ...
       }
    ]

<a id="resourceid" />
### <a name="resourceid"></a>預設

**預設 ([subscriptionId]，[resourceGroupName]，resourceType，resourceName1，[resourceName2]...)**

傳回資源的唯一識別碼。 
      
| 參數         | 所需 | 描述
| :---------------: | :------: | :----------
| subscriptionId    |   無     | 預設值為最新訂閱。 當您需要擷取其他訂閱中的資源時，指定這個值。
| resourceGroupName |   無     | 預設值為目前的 [資源] 群組。 當您要擷取的另一個資源] 群組中的資源時，指定這個值。
| resourceType      |   [是]    | 包括資源提供者命名空間的資源類型。
| resourceName1     |   [是]    | 資源名稱。
| resourceName2     |   無     | 下一個資源名稱區段資源是巢狀結構。

資源名稱不明確或不提供相同的範本中時，您可以使用此函數。 會傳回識別項，以下列格式︰

    /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/{resourceProviderNamespace}/{resourceType}/{resourceName}

下列範例會示範如何擷取網站和資料庫的資源識別碼。 資源群組名稱**myWebsitesGroup**中存在的網站，而且此範本目前的 [資源] 群組中的資料庫。

    [resourceId('myWebsitesGroup', 'Microsoft.Web/sites', parameters('siteName'))]
    [resourceId('Microsoft.SQL/servers/databases', parameters('serverName'), parameters('databaseName'))]
    
通常，您需要使用替代的資源] 群組中的儲存空間帳戶或虛擬網路時，請使用此函數。 儲存帳戶或虛擬網路可能會用於跨多個資源群組。因此，您不想刪除單一資源群組時，請刪除這些。 下列範例會顯示如何從外部資源群組資源可以輕鬆地使用︰

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
          "virtualNetworkName": {
              "type": "string"
          },
          "virtualNetworkResourceGroup": {
              "type": "string"
          },
          "subnet1Name": {
              "type": "string"
          },
          "nicName": {
              "type": "string"
          }
      },
      "variables": {
          "vnetID": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks', parameters('virtualNetworkName'))]",
          "subnet1Ref": "[concat(variables('vnetID'),'/subnets/', parameters('subnet1Name'))]"
      },
      "resources": [
      {
          "apiVersion": "2015-05-01-preview",
          "type": "Microsoft.Network/networkInterfaces",
          "name": "[parameters('nicName')]",
          "location": "[parameters('location')]",
          "properties": {
              "ipConfigurations": [{
                  "name": "ipconfig1",
                  "properties": {
                      "privateIPAllocationMethod": "Dynamic",
                      "subnet": {
                          "id": "[variables('subnet1Ref')]"
                      }
                  }
              }]
           }
      }]
    }

<a id="subscription" />
### <a name="subscription"></a>訂閱

**subscription()**

傳回以下列格式的訂閱的詳細資料。

    {
        "id": "/subscriptions/#####",
        "subscriptionId": "#####",
        "tenantId": "#####"
    }

下列範例會顯示在 [輸出] 區段中稱為訂閱函數。 

    "outputs": { 
      "exampleOutput": { 
          "value": "[subscription()]", 
          "type" : "object" 
      } 
    } 


## <a name="next-steps"></a>後續步驟
- Azure 資源管理員範本] 區段的說明，請參閱[撰寫 Azure 資源管理員範本](resource-group-authoring-templates.md)
- 若要合併多個範本，請參閱[使用連結的範本與 Azure 資源管理員](resource-group-linked-templates.md)
- 若要重複指定的次數時建立的資源類型，請參閱[建立資源 Azure 資源管理員] 中的多個執行個體](resource-group-create-multiple.md)
- 若要查看如何部署您所建立的範本，請參閱[部署具有 Azure 資源管理員範本的應用程式](resource-group-template-deploy.md)

