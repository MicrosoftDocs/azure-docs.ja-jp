---
title: Azure Active Directory で属性マッピングの式を記述するためのリファレンス
description: Azure Active Directory で SaaS アプリ オブジェクトを自動プロビジョニングしているときに、式マッピングを使用して属性値を許容される形式に変換する方法について説明します。 関数の参照一覧が含まれています。
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: reference
ms.date: 03/04/2021
ms.author: kenwith
ms.custom: contperf-fy21q2
ms.openlocfilehash: 0334f52b87071c8f363a0dfcc793170316747096
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102198508"
---
# <a name="reference-for-writing-expressions-for-attribute-mappings-in-azure-ad"></a>Azure AD で属性マッピングの式を記述するためのリファレンス

SaaS アプリケーションに対してプロビジョニングを構成するときに指定できる属性マッピングの種類の 1 つは、式マッピングです。 この場合は、ユーザーのデータを SaaS アプリケーションが許容可能な形式に変換することができる、スクリプトに似た式を記述する必要があります。

## <a name="syntax-overview"></a>構文の概要

属性マッピングの式の構文は、Visual Basic のApplications (VBA) 関数に似ています。

* 式全体は、関数の形式で定義する必要があります。名前の後にかっこで囲んだ引数を続けます。*FunctionName(`<<argument 1>>`,`<<argument N>>`)*
* 各関数内で他の関数を入れ子にすることができます。 次に例を示します。*FunctionOne(FunctionTwo(`<<argument1>>`))*
* 関数には、次の 3 つの異なる種類の引数を渡すことができます。
  
  1. 属性。角かっこで囲む必要があります。 例: [attributeName]
  2. 文字列定数。二重引用符で囲む必要があります。 次に例を示します。"米国"
  3. 他の関数 次に例を示します。FunctionOne(`<<argument1>>`, FunctionTwo(`<<argument2>>`))
* 文字列定数では、文字列に円記号 (\) または引用符 (") を含める必要がある場合は、円記号 (\) でエスケープする必要があります。 次に例を示します。"会社名:\\"Contoso\\""
* この構文では大文字と小文字が区別されます。これは、関数に文字列として入力するときに考慮する必要があり、ここから直接コピーして貼り付けることもできます。 

## <a name="list-of-functions"></a>関数の一覧

[Append](#append) &nbsp;&nbsp;&nbsp;&nbsp; [BitAnd](#bitand) &nbsp;&nbsp;&nbsp;&nbsp; [CBool](#cbool) &nbsp;&nbsp;&nbsp;&nbsp; [Coalesce](#coalesce) &nbsp;&nbsp;&nbsp;&nbsp; [ConvertToBase64](#converttobase64) &nbsp;&nbsp;&nbsp;&nbsp; [ConvertToUTF8Hex](#converttoutf8hex) &nbsp;&nbsp;&nbsp;&nbsp; [Count](#count) &nbsp;&nbsp;&nbsp;&nbsp; [CStr](#cstr) &nbsp;&nbsp;&nbsp;&nbsp; [DateFromNum](#datefromnum) &nbsp;[FormatDateTime](#formatdatetime) &nbsp;&nbsp;&nbsp;&nbsp; [Guid](#guid) &nbsp;&nbsp;&nbsp;&nbsp; [IIF](#iif) &nbsp;&nbsp;&nbsp;&nbsp;[InStr](#instr) &nbsp;&nbsp;&nbsp;&nbsp; [IsNull](#isnull) &nbsp;&nbsp;&nbsp;&nbsp; [IsNullOrEmpty](#isnullorempty) &nbsp;&nbsp;&nbsp;&nbsp; [IsPresent](#ispresent) &nbsp;&nbsp;&nbsp;&nbsp; [IsString](#isstring) &nbsp;&nbsp;&nbsp;&nbsp; [Item](#item) &nbsp;&nbsp;&nbsp;&nbsp; [Join](#join) &nbsp;&nbsp;&nbsp;&nbsp; [Left](#left) &nbsp;&nbsp;&nbsp;&nbsp; [Mid](#mid) &nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp; [NormalizeDiacritics](#normalizediacritics) [Not](#not) &nbsp;&nbsp;&nbsp;&nbsp; [NumFromDate](#numfromdate) &nbsp;&nbsp;&nbsp;&nbsp; [RemoveDuplicates](#removeduplicates) &nbsp;&nbsp;&nbsp;&nbsp; [Replace](#replace) &nbsp;&nbsp;&nbsp;&nbsp; [SelectUniqueValue](#selectuniquevalue)&nbsp;&nbsp;&nbsp;&nbsp; [SingleAppRoleAssignment](#singleapproleassignment)&nbsp;&nbsp;&nbsp;&nbsp; [Split](#split)&nbsp;&nbsp;&nbsp;&nbsp;[StripSpaces](#stripspaces) &nbsp;&nbsp;&nbsp;&nbsp; [Switch](#switch)&nbsp;&nbsp;&nbsp;&nbsp; [ToLower](#tolower)&nbsp;&nbsp;&nbsp;&nbsp; [ToUpper](#toupper)&nbsp;&nbsp;&nbsp;&nbsp; [Word](#word)

---
### <a name="append"></a>Append

**関数:** Append(source, suffix)

**説明:** source 文字列値を受け取り、その末尾に suffix を追加します。

**パラメーター:**

| 名前 | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **source** |必須 |String |通常は、source オブジェクトの属性の名前。 |
| **suffix** |必須 |String |source 値の末尾に追加する文字列。 |


### <a name="append-constant-suffix-to-user-name"></a>ユーザー名に定数のサフィックスを追加する
例: Salesforce Sandbox を使用している場合は、ユーザー名を同期する前に、すべてのユーザー名に追加のサフィックスを追加する必要があります。

**式:**  
`Append([userPrincipalName], ".test")`

**サンプル入力/出力:** 

* **入力**: (userPrincipalName): "John.Doe@contoso.com"
* **出力**:  "John.Doe@contoso.com.test"


---
### <a name="bitand"></a>BitAnd
**関数:** BitAnd(value1, value2)

**説明:** この関数は両方のパラメーターをバイナリ表現に変換し、ビットを次に設定します。

- 0 - value1 と value2 内の対応するビットの 1 つまたは両方が 0 の場合
- 1 - 対応するビットの両方が 1 の場合。

つまり、両方のパラメーターの対応するビットが 1 の場合を除くすべてのケースで 0 を返します。

**パラメーター:** 

| 名前 | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **value1** |必須 |num |value2 と AND で処理する数値|
| **value2** |必須 |num |value1 と AND で処理する数値|

**例:** 
`BitAnd(&HF, &HF7)`

11110111 AND 00000111 = 00000111。`BitAnd` からは 7 が返されます。その 2 進数は 00000111 です。

---
### <a name="cbool"></a>CBool
**関数:**  
`CBool(Expression)`

**説明:**  
`CBool` からは、式の評価結果に基づいてブール値が返されます。 式の評価結果が 0 以外の値の場合は `CBool` によって *True* が返され、それ以外の場合は *False* が返されます。

**パラメーター:** 

| 名前 | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **式 (expression)** |必須 | expression | 任意の有効な式 |

**例:** 
`CBool([attribute1] = [attribute2])`                                                                    
両方の属性が同じ値を持つ場合は、True を返します。

---
### <a name="coalesce"></a>Coalesce
**関数:** Coalesce(source1, source2, ..., defaultValue)

**説明:** NULL ではない最初のソース値が返されます。 すべての引数が NULL で、defaultValue が存在する場合、defaultValue が返されます。 すべての引数が NULL で、defaultValue が存在しない場合、Coalesce からは NULL が返されます。

**パラメーター:** 

| 名前 | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **source1  … sourceN** | 必須 | String |必須、回数は可変。 通常は、source オブジェクトの属性の名前。 |
| **defaultValue** | 省略可能 | String | すべてのソース値が NULL の場合に使用される既定値。 空の文字列 ("") を指定できます。

### <a name="flow-mail-value-if-not-null-otherwise-flow-userprincipalname"></a>NULL でない場合はフロー メール値、それ以外の場合は userPrincipalName
例: メール属性が存在する場合は、それをフローすることをお勧めします。 存在しない場合、代わりに userPrincipalName の値をフローしてください。

**式:**  
`Coalesce([mail],[userPrincipalName])`

**サンプル入力/出力:** 

* **入力** (mail):NULL
* **入力** (userPrincipalName): "John.Doe@contoso.com"
* **出力**:  "John.Doe@contoso.com"


---
### <a name="converttobase64"></a>ConvertToBase64
**関数:** ConvertToBase64(source)

**説明:** ConvertToBase64 関数は、文字列を Unicode の base64 文字列に変換します。

**パラメーター:** 

| 名前 | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **source** |必須 |String |base 64 に変換される文字列|

**例:** 
`ConvertToBase64("Hello world!")`

"SABlAGwAbABvACAAdwBvAHIAbABkACEA" を返します。

---
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**関数:** ConvertToUTF8Hex(source)

**説明:** ConvertToUTF8Hex 関数は、文字列を UTF8 の 16 進数でエンコードされた値に変換します。

**パラメーター:** 

| 名前 | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **source** |必須 |String |UTF8 Hex に変換される文字列|

**例:** 
`ConvertToUTF8Hex("Hello world!")`

48656C6C6F20776F726C6421 を返します。

---
### <a name="count"></a>Count
**関数:** Count(attribute)

**説明:** Count 関数は、複数値の属性内の要素数を返します。

**パラメーター:** 

| 名前 | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **attribute** |必須 |属性 (attribute) |要素がカウントされる複数値の属性|

---
### <a name="cstr"></a>CStr
**関数:** CStr(value)

**説明:** CStr 関数では、値が文字列データ型に変換されます。

**パラメーター:** 

| 名前 | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **value** |必須 | 数値、参照、またはブール値 | 数値、参照属性、ブール値を指定できます。 |

**例:** 
`CStr([dn])`

"cn=Joe,dc=contoso,dc=com" が返されます

---
### <a name="datefromnum"></a>DateFromNum
**関数:** DateFromNum(value)

**説明:** DateFromNum 関数は、AD の日付形式の値を DateTime 型に変換します。

**パラメーター:** 

| 名前 | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **value** |必須 | Date | DateTime 型に変換される AD 日付 |

**例:** 
`DateFromNum([lastLogonTimestamp])`

`DateFromNum(129699324000000000)`

2012 年 1 月 1 日の午後 11:00 を表す DateTime が返されます。

---
### <a name="formatdatetime"></a>FormatDateTime
**関数:** FormatDateTime(source, dateTimeStyles, inputFormat, outputFormat)

**説明:** 1 つの形式の日付文字列を受け取り、別の形式に変換します。

**パラメーター:** 

| 名前 | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **source** |必須 |String |通常は、source オブジェクトの属性の名前。 |
| **dateTimeStyles** | 省略可能 | String | これを使用して、いくつかの日時解析メソッドによる文字列の解析をカスタマイズする形式指定オプションを指定します。 サポートされる値については、[DateTimeStyles のドキュメント](/dotnet/api/system.globalization.datetimestyles)を参照してください。空のままにすると、使用される既定値は DateTimeStyles.RoundtripKind、DateTimeStyles.AllowLeadingWhite、DateTimeStyles.AllowTrailingWhite です  |
| **inputFormat** |必須 |String |有効な形式の source 値。 サポートされている形式については、[/dotnet/standard/base-types/custom-date-and-time-format-strings](/dotnet/standard/base-types/custom-date-and-time-format-strings) を参照してください。 |
| **outputFormat** |必須 |String |出力日付の形式。 |



### <a name="output-date-as-a-string-in-a-certain-format"></a>特定の形式の文字列として日付を出力する
例: ServiceNow などの SaaS アプリケーションに特定の形式で日付を送信する場合があります。 次の式を使用することを検討してください。 

**Expression:** 

`FormatDateTime([extensionAttribute1], , "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**サンプル入力/出力:**

* **入力** (extensionAttribute1):"20150123105347.1Z"
* **出力**:"2015-01-23"


---
### <a name="guid"></a>Guid
**関数:** Guid()

**説明:** GUID 関数は、新しいランダムな GUID を生成します。

---
### <a name="iif"></a>IIF
**関数:** IIF(condition,valueIfTrue,valueIfFalse)

**説明:** IIF 関数は、指定した条件に基づいて、使用できる一連の値のうち、いずれかを返します。

**パラメーター:** 

| 名前 | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **condition** |必須 |変数または式 |true または false に評価できる任意の値または式。 |
| **valueIfTrue** |必須 |変数または文字列 | 条件の評価結果が true の場合に返される値。 |
| **valueIfFalse** |必須 |変数または文字列 |条件の評価結果が false の場合に返される値。|

**例:** 
`IIF([country]="USA",[country],[department])`

---
### <a name="instr"></a>InStr
**関数:** InStr(value1, value2, start, compareType)

**説明:** InStr 関数は文字列内の最初の部分文字列を検索します。

**パラメーター:** 

| 名前 | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **value1** |必須 |String |検索対象の文字列 |
| **value2** |必須 |String |検索する文字列 |
| **start** |省略可能 |Integer |部分文字列の検索を開始する位置|
| **compareType** |省略可能 |列挙型 |vbTextCompare か vbBinaryCompare になります |

**例:** 
`InStr("The quick brown fox","quick")`

評価結果は 5 になります。

`InStr("repEated","e",3,vbBinaryCompare)`

評価結果は 7 になります。

---
### <a name="isnull"></a>IsNull
**関数:** IsNull(Expression)

**説明:** 式の評価結果が Null の場合、IsNull 関数は true を返します。 属性の場合、Null は属性の不在によって表されます。

**パラメーター:** 

| 名前 | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **式 (expression)** |必須 |expression |評価の対象となる式 |

**例:** 
`IsNull([displayName])`

属性がない場合は True が返されます。

---
### <a name="isnullorempty"></a>IsNullorEmpty
**関数:** IsNullOrEmpty(Expression)

**説明:** 式が null または空の文字列の場合、IsNullOrEmpty 関数は true を返します。 属性の場合は、属性がないか、存在しても空の文字列の場合、評価結果は True になります。
この関数の逆の関数は IsPresent です。

**パラメーター:** 

| 名前 | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **式 (expression)** |必須 |expression |評価の対象となる式 |

**例:** 
`IsNullOrEmpty([displayName])`

属性がないか、空の文字列の場合は True が返されます。

---
### <a name="ispresent"></a>IsPresent
**関数:** IsPresent(Expression)

**説明:** 式の評価結果が Null でもなく、空でもない文字列の場合、IsPresent 関数は true を返します。 この関数の逆関数は IsNullOrEmpty です。

**パラメーター:** 

| 名前 | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **式 (expression)** |必須 |expression |評価の対象となる式 |

**例:** 
`Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])`

---
### <a name="isstring"></a>IsString
**関数:** IsString(Expression)

**説明:** 式を文字列型として評価できる場合、IsString 関数の評価結果は True になります。

**パラメーター:** 

| 名前 | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **式 (expression)** |必須 |expression |評価の対象となる式 |

---
### <a name="item"></a>Item
**関数:** Item(attribute, index)

**説明:** Item 関数は複数値の文字列/属性から 1 つの項目を返します。

**パラメーター:** 

| 名前 | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **attribute** |必須 |属性 |検索対象の複数値の属性 |
| **インデックス** |必須 |Integer | 複数値の文字列内の項目のインデックス|

**例:** 
`Item([proxyAddresses], 1)` は、複数値属性の 2 番目の項目を返します。

---
### <a name="join"></a>Join
**関数:** Join(separator, source1, source2, …)

**説明:** Join() は Append() によく似ていますが、Join() では複数の **source** 文字列値を 1 つの文字列に結合できます。文字列値は **separator** で区切って指定します。

source 値の 1 つが複数値属性である場合は、その属性のすべての値を結合し、separator 値で区切ります。

**パラメーター:** 

| 名前 | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **separator** |必須 |String |source 値を 1 つの文字列に連結するときに、各値を区切るのに使用する文字列。 区切り記号が必要ない場合は、“” とすることができます。 |
| **source1  … sourceN** |必須、回数は可変 |String |結合する文字列値。 |

---
### <a name="left"></a>Left
**関数:** Left(String,NumChars)

**説明:** Left 関数は文字列の左端から数えて指定した文字数分の文字を返します。 numChars = 0 の場合、空の文字列を返します。
numChars < 0 の場合、入力文字列を返します。
string が null の場合、空の文字列を返します。
string に含まれる文字数が numChars で指定した数より少ない場合は、string と同一の文字列 (パラメーター 1 のすべての文字が含まれる) が返されます。

**パラメーター:** 

| 名前 | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **String** |必須 |属性 | 返される文字を含む文字列 |
| **NumChars** |必須 |Integer | 文字列の先頭 (左端) から取得する文字数を示す値|

**例:** 
`Left("John Doe", 3)`

"Joh" を返します。

---
### <a name="mid"></a>Mid
**関数:** Mid(source, start, length)

**説明:** source 値の部分文字列を返します。 部分文字列は、source 文字列の文字のみを含む文字列です。

**パラメーター:** 

| 名前 | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **source** |必須 |String |通常、属性の名前。 |
| **start** |必須 |整数 (integer) |部分文字列が始まる **source** 文字列のインデックス。 文字列内の最初の文字のインデックスは 1、2 番目の文字のインデックスは 2です (以降同様)。 |
| **length** |必須 |整数 (integer) |部分文字列の長さ。 length が **source** 文字列の外で終わる場合は、**start** インデックスから **source** 文字列の末尾までの部分文字列を返します。 |

---
### <a name="normalizediacritics"></a>NormalizeDiacritics
**関数:** NormalizeDiacritics(source)

**説明:** 1 つの文字列引数が必要です。 文字列を返しますが、分音記号はそれ以外の同等の記号で置換されます。 通常、分音記号 (アクセント記号) を含む姓と名を、さまざまなユーザー識別子 (ユーザー プリンシパル名、SAM アカウント名、電子メール アドレスなど) で使用できる有効な値に変換するために使用します。

**パラメーター:** 

| 名前 | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **source** |必須 |String | 通常は、名または姓の属性です。 |


### <a name="remove-diacritics-from-a-string"></a>文字列から分音記号を削除する
例: アクセント記号を含む文字を、アクセント記号を含まない同等の文字に置換する必要があります。

**Expression:** NormalizeDiacritics([givenName])

**サンプル入力/出力:** 

* **入力** (givenName):"Zoë"
* **出力**:"Zoe"


---
### <a name="not"></a>Not
**関数:** Not(source)

**説明:** **source** のブール値を反転します。 **source** 値が True の場合は False を返します。 そうでない場合は、True を返します。

**パラメーター:** 

| 名前 | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **source** |必須 |Boolean String |有効な **source** 値は "True" または "False" です。 |

---
### <a name="numfromdate"></a>NumFromDate
**関数:** NumFromDate(value)

**説明:** NumFromDate 関数は、DateTime 値を、[accountExpires](/windows/win32/adschema/a-accountexpires) などの属性を設定するために必要な Active Directory 形式に変換します。 この関数を使用して、Workday や SuccessFactors などのクラウド HR アプリから受信した DateTime 値を、それと等価な AD 表現に変換します。 

**パラメーター:** 

| 名前 | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **value** |必須 | String | サポートされる形式の日付と時刻の文字列。 サポートされる形式については、 https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx をご覧ください。 |

**例:**
* Workday の例: *2020-12-31-08:00* 形式の Workday の *ContractEndDate* 属性を、AD の *accountExpires* フィールドにマップする場合に、この関数を使用して、ロケールに合わせてタイムゾーン オフセットを変更する方法をここで示します。 
  `NumFromDate(Join("", FormatDateTime([ContractEndDate], ,"yyyy-MM-ddzzz", "yyyy-MM-dd"), "T23:59:59-08:00"))`

* SuccessFactors の例: *M/d/yyyy hh:mm:ss tt* 形式の SuccessFactors の *endDate* 属性を、AD の *accountExpires* フィールドにマップする場合に、この関数を使用して、ロケールに合わせてタイムゾーン オフセットを変更する方法をここで示します。
  `NumFromDate(Join("",FormatDateTime([endDate], ,"M/d/yyyy hh:mm:ss tt","yyyy-MM-dd"),"T23:59:59-08:00"))`


---
### <a name="removeduplicates"></a>RemoveDuplicates
**関数:** RemoveDuplicates(attribute)

**説明:** RemoveDuplicates 関数は複数値の文字列を受け取り、各値が一意になるように処理します。

**パラメーター:** 

| 名前 | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **attribute** |必須 |複数値の属性 |重複が削除される複数値の属性|

**例:** 
`RemoveDuplicates([proxyAddresses])` 重複する値がすべて削除された、校正済みの proxyAddress 属性を返します。

---
### <a name="replace"></a>Replace
**関数:** Replace(source, oldValue, regexPattern, regexGroupName, replacementValue, replacementAttributeName, template)

**説明:** 大文字と小文字を区別して、文字列内の値を置換します。 この関数は指定されたパラメーターに応じて異なる動作をします。

* **oldValue** と **replacementValue** が指定された場合:
  
  * **source** に含まれるすべての **oldValue** を **replacementValue** に置換します。
* **oldValue** と **template** が指定された場合:
  
  * **template** に含まれるすべての **OldValue** を **source** 値に置換します。
* **regexPattern** と **replacementValue** が指定された場合:

  * この関数で **regexPattern** が **source** の文字列に適用され、regex グループ名を使って **replacementValue** の文字列を作成できます。
* **regexPattern**、**regexGroupName**、**replacementValue** が指定された場合:
  
  * この関数で **regexPattern** が **source** の文字列に適用され、**regexGroupName** と一致するすべての値が **replacementValue** に置き換えられます
* **regexPattern**、**regexGroupName**、**replacementAttributeName** が指定された場合:
  
  * **source** に値が指定されていない場合は、**source** を返します。
  * **source** に値がある場合、この関数で **regexPattern** が **source** の文字列に適用され、**regexGroupName** と一致するすべての値が **replacementAttributeName** に関連付けられた値に置き換えられます

**パラメーター:** 

| 名前 | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **source** |必須 |String |通常は、**source** オブジェクトの属性の名前。 |
| **oldValue** |省略可能 |String |**source** または **template** に含まれる置換前の値。 |
| **regexPattern** |省略可能 |String |**source** に含まれる置換前の値の正規表現パターン。 または、**replacementPropertyName** が使われるときは、**replacementPropertyName** から値を抽出するパターン。 |
| **regexGroupName** |省略可能 |String |**regexPattern** 内のグループの名前。 **replacementPropertyName** を使用した場合にのみ、このグループの値が **replacementPropertyName** から **replacementValue** として抽出されます。 |
| **replacementValue** |省略可能 |String |古い値を置き換える新しい値。 |
| **replacementAttributeName** |省略可能 |String |置換値に使用する属性の名前 |
| **template** |省略可能 |String |**template** の値を指定した場合、template 内で **oldValue** が検索され、**source** の値で置換されます。 |

### <a name="replace-characters-using-a-regular-expression"></a>正規表現を使用して文字を置換します
例: 正規表現の値と一致する文字を見つけて削除する必要があります。

**Expression:** 

Replace([mailNickname], , "[a-zA-Z_]*", , "", , )

**サンプル入力/出力:**

* **INPUT** (mailNickname: "john_doe72"
* **出力**:"72"


---
### <a name="selectuniquevalue"></a>SelectUniqueValue
**関数:** SelectUniqueValue(uniqueValueRule1, uniqueValueRule2, uniqueValueRule3, …)

**説明:** 少なくとも 2 つの引数が必要です。引数は、式を使用して定義されている一意値生成ルールです。 関数では、各ルールが評価された後、生成された値の対象となるアプリ/ディレクトリでの一意性が確認されます。 最初に見つかった一意の値が返されます。 すべての値がターゲットに既に存在する場合、エントリはエスクローされて、理由が監査ログに記録されます。 渡すことができる引数の数に上限はありません。


 - これは最上位の関数であり、入れ子にはできません。
 - この関数は、照合の優先順位を持つ属性には適用できません。     
 - この関数は、エントリの作成に使用されることだけを目的としたものです。 属性で使用するときは、 **[Apply Mapping]\(マッピングの適用\)** プロパティを **[オブジェクトの作成中のみ]** に設定します。
 - この関数は現在、"Workday から Active Directory へのユーザー プロビジョニング" と "SuccessFactors から Active Directory へのユーザー プロビジョニング" でのみサポートされています。 他のプロビジョニング アプリでは使用できません。 


**パラメーター:** 

| 名前 | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **uniqueValueRule1  … uniqueValueRuleN** |2 つ以上必要であり、上限はありません |String | 評価する一意値生成ルールの一覧。 |

### <a name="generate-unique-value-for-userprincipalname-upn-attribute"></a>userPrincipalName (UPN) 属性用に一意の値を生成する
例: ユーザーの名、ミドル ネーム、姓を基にして UPN 属性の値を生成し、値を UPN 属性に割り当てる前に、対象の AD ディレクトリで値が一意であることを確認する必要があります。

**Expression:** 

```ad-attr-mapping-expr
    SelectUniqueValue( 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"), 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 1), [PreferredLastName]))), "contoso.com"),
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 2), [PreferredLastName]))), "contoso.com")
    )
```

**サンプル入力/出力:**

* **入力** (PreferredFirstName):"John"
* **入力** (PreferredLastName):"Smith"
* **出力**: John.Smith@contoso.com の UPN 値がディレクトリにまだ存在しない場合は "John.Smith@contoso.com"
* **出力**: John.Smith@contoso.com の UPN 値がディレクトリに既に存在する場合は "J.Smith@contoso.com"
* **出力**: 上記の 2 つの UPN 値がディレクトリに既に存在する場合は "Jo.Smith@contoso.com"



---
### <a name="singleapproleassignment"></a>SingleAppRoleAssignment
**関数:** SingleAppRoleAssignment(appRoleAssignments)

**説明:** 指定したアプリケーションで 1 人のユーザーに割り当てられた appRoleAssignments の全一覧から、単一の appRoleAssignment を返します。 appRoleAssignments オブジェクトを単一のロール名文字列に変換するために、この関数が必要になります。 ベスト プラクティスは、確実に 1 つの appRoleAssignment だけが一度に 1 人のユーザーに割り当てられるようにすることです。複数のロールが割り当てられると、返されるロール文字列を予測できません。 

**パラメーター:** 

| 名前 | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **appRoleAssignments** |必須 |String |**appRoleAssignments** オブジェクト |

---
### <a name="split"></a>Split
**関数:** Split(source, delimiter)

**説明:** 指定された区切り記号文字を使用して、文字列を複数値の配列に分割します。

**パラメーター:** 

| 名前 | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **source** |必須 |String |**source** 値。 |
| **delimiter** |必須 |String |文字列の分割に使用される文字を指定します (例: ",") |

### <a name="split-a-string-into-a-multi-valued-array"></a>文字列を複数値の配列に分割します
例: コンマ区切りの一覧になっている文字列を受け取り、Salesforce の PermissionSets 属性などの複数値の属性にプラグインできる配列に分割する必要があります。 この例では、アクセス許可セットの一覧が、Azure AD の extensionAttribute5 に格納されています。

**Expression:** Split([extensionAttribute5], ",")

**サンプル入力/出力:** 

* **INPUT** (extensionAttribute5):
* **OUTPUT**:  ["PermissionSetOne", "PermissionSetTwo"]


---
### <a name="stripspaces"></a>StripSpaces
**関数:** StripSpaces(source)

**説明:** source 文字列からすべての空白文字 (" ") を削除します。

**パラメーター:** 

| 名前 | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **source** |必須 |String |**source** 値。 |

---
### <a name="switch"></a>Switch
**関数:** Switch(source, defaultValue, key1, value1, key2, value2, …)

**説明:** **source** 値が **key** と一致するときに、その **key** の **value** を返します。 **source** 値がどの key とも一致しない場合は、**defaultValue** を返します。  **key** と **value** パラメーターは、常にペアで指定する必要があります。 この関数には、常に、偶数個のパラメーターを指定する必要があります。 この関数は、manager などの参照属性には使用しないでください。 

**パラメーター:** 

| 名前 | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **source** |必須 |String |**Source** 値。 |
| **defaultValue** |省略可能 |String |source がどの key とも一致しないときに使用される既定値。 空の文字列 ("") を指定できます。 |
| **key** |必須 |String |**source** 値と比較する **key**。 |
| **value** |必須 |String |key と一致する **source** の置換値。 |

### <a name="replace-a-value-based-on-predefined-set-of-options"></a>定義済みのオプション セットに基づいて値を置換する
例: Azure AD に格納されている都道府県コードに基づいて、ユーザーのタイム ゾーンを定義する必要があります。 都道府県コードが定義済みオプションのいずれにも一致しない場合は、既定値 "Australia/Sydney" を使用します。

**式:**  
`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**サンプル入力/出力:**

* **入力** (state):"QLD"
* **出力**:"Australia/Brisbane"


---
### <a name="tolower"></a>ToLower
**関数:** ToLower(source, culture)

**説明:** *source* 文字列値を受け取り、指定されたカルチャ ルールを使用して小文字に変換します。 *culture* 情報が指定されていない場合は、インバリアント カルチャが使用されます。

**パラメーター:** 

| 名前 | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **source** |必須 |String |通常は、source オブジェクトの属性の名前。 |
| **culture** |省略可能 |String |RFC 4646 に基づくカルチャ名の形式は、*languagecode2-country/regioncode2* です。ここで、*languagecode2* は 2 文字の言語コードで、*country/regioncode2* は 2 文字のサブカルチャ コードです。 例には、日本語 (日本) の場合の ja-JP と英語 (米国) の場合の en-US が含まれています。 2 文字の言語コードが使用できない場合は、ISO 639-2 から派生した 3 文字のコードが使用されます。|

### <a name="convert-generated-userprincipalname-upn-value-to-lower-case"></a>生成された userPrincipalName (UPN) の値を小文字に変換します
例: PreferredFirstName および PreferredLastName のソース フィールドを連結して、すべての文字を小文字に変換することで、UPN 値を生成する場合があります。 

`ToLower(Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"))`

**サンプル入力/出力:**

* **入力** (PreferredFirstName):"John"
* **入力** (PreferredLastName):"Smith"
* **出力**: "john.smith@contoso.com"


---
### <a name="toupper"></a>ToUpper
**関数:** ToUpper(source, culture)

**説明:** *source* 文字列値を受け取り、指定されたカルチャ ルールを使用して大文字に変換します。 *culture* 情報が指定されていない場合は、インバリアント カルチャが使用されます。

**パラメーター:** 

| 名前 | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **source** |必須 |String |通常は、source オブジェクトの属性の名前。 |
| **culture** |省略可能 |String |RFC 4646 に基づくカルチャ名の形式は、*languagecode2-country/regioncode2* です。ここで、*languagecode2* は 2 文字の言語コードで、*country/regioncode2* は 2 文字のサブカルチャ コードです。 例には、日本語 (日本) の場合の ja-JP と英語 (米国) の場合の en-US が含まれています。 2 文字の言語コードが使用できない場合は、ISO 639-2 から派生した 3 文字のコードが使用されます。|

---
### <a name="word"></a>Word
**関数:** Word(String,WordNumber,Delimiters)

**説明:** Word 関数は、使用する区切り記号と返す単語の番号を表すパラメーターに基づいて、文字列内に含まれる単語を返します。 delimiters 内のいずれかの文字で区切られた string 内の各文字列が、単語として識別されます。

num < 1 の場合、空の文字列を返します。
string が null の場合、空の文字列を返します。
string に含まれる単語の数が指定より少ないか、区切り記号文字で識別されるどの単語も string に含まれていない場合は、空の文字列が返されます。

**パラメーター:** 

| 名前 | 必須/繰り返し | Type | Notes |
| --- | --- | --- | --- |
| **String** |必須 |複数値の属性 |返される単語を含む文字列|
| **WordNumber** |必須 | Integer | 返すべき単語の番号を指定する数値|
| **delimiters** |必須 |String| 単語を識別するために使用される区切り記号を表す文字列|

**例:** 
`Word("The quick brown fox",3," ")`

"brown" が返されます。

`Word("This,string!has&many separators",3,",!&#")`

"has" が返されます。

---

## <a name="examples"></a>例
ここでは、式関数の使用例をさらに詳しく説明します。 

### <a name="strip-known-domain-name"></a>既知のドメイン名をストリップする
ユーザーの電子メールから既知のドメイン名をストリップして、ユーザー名を取得する必要があります。 たとえば、ドメインが "contoso.com" の場合は、次の式を使用することができます。

**式:**  
`Replace([mail], "@contoso.com", , ,"", ,)`

**サンプル入力/出力:** 

* **入力** (mail): "john.doe@contoso.com"
* **出力**: "john.doe"


### <a name="generate-user-alias-by-concatenating-parts-of-first-and-last-name"></a>姓の一部と名の一部を連結することでユーザー エイリアスを生成する
ユーザーの名の最初の 3 文字とユーザーの姓の最初の 5 文字を取得することでユーザー エイリアスを生成する必要があります。

**式:**  
`Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**サンプル入力/出力:** 

* **入力** (givenName):"John"
* **入力** (surname):"Doe"
* **出力**:"JohDoe"


## <a name="related-articles"></a>関連記事
* [Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)
* [ユーザーのプロビジョニング用の属性マッピングのカスタマイズ](../app-provisioning/customize-application-attributes.md)
* [ユーザーのプロビジョニング用のフィルターのスコープ](define-conditional-rules-for-provisioning-user-accounts.md)
* [SCIM を使用して、Azure Active Directory からアプリケーションへのユーザーとグループの自動プロビジョニングを有効にする](../app-provisioning/use-scim-to-provision-users-and-groups.md)
* [アカウント プロビジョニング通知](../app-provisioning/user-provisioning.md)
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](../saas-apps/tutorial-list.md)
