---
title: Azure AD Connect クラウド同期の式と関数のリファレンス
description: reference
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 869d60d4ccb3bae58924fe4221f83b1b0125ae04
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98612593"
---
# <a name="writing-expressions-for-attribute-mappings-in-azure-active-directory"></a>Azure Active Directory における属性マッピングの式の書き方
クラウド同期を構成するときに指定できる属性マッピングの種類の 1 つは、式マッピングです。 

式マッピングでは、スクリプトに似た式を使用して属性をカスタマイズすることができます。  そうすることでオンプレミス データを新しい (つまり異なる) 値に変換することができます。  たとえば、2 つの属性を 1 つの属性に結合して、いずれかのクラウド アプリケーションで使用するケースが考えられます。

以降のドキュメントでは、データの変換に使用される、スクリプトに似た式について取り上げます。  これはプロセスの一部分にすぎません。  その後、この式をテナントに対する Web 要求に含める必要があります。  詳細については、「[変換](how-to-transformation.md)」を参照してください。

## <a name="syntax-overview"></a>構文の概要
属性マッピングの式の構文は、Visual Basic のApplications (VBA) 関数に似ています。

* 式全体は、関数の形式で定義する必要があります。名前の後にかっこで囲んだ引数を続けます。 <br>
  *FunctionName(`<<argument 1>>`,`<<argument N>>`)*
* 各関数内で他の関数を入れ子にすることができます。 次に例を示します。 <br> *FunctionOne(FunctionTwo(`<<argument1>>`))*
* 関数には、次の 3 つの異なる種類の引数を渡すことができます。
  
  1. 属性。角かっこで囲む必要があります。 例: [attributeName]
  2. 文字列定数。二重引用符で囲む必要があります。 次に例を示します。"米国"
  3. 他の関数 次に例を示します。FunctionOne(`<<argument1>>`, FunctionTwo(`<<argument2>>`))
* 文字列定数では、文字列に円記号 (\) または引用符 (") を含める必要がある場合は、円記号 (\) でエスケープする必要があります。 次に例を示します。"会社名:\\"Contoso\\""

## <a name="list-of-functions"></a>関数の一覧
| 関数の一覧 | 説明 |
|-----|----|
|[Append](#append)|source 文字列値を受け取り、その末尾に suffix を追加します。|
|[BitAnd](#bitand)|BitAnd 関数は、値に指定のビットを設定します。|
|[CBool](#cbool)|CBool 関数は、式の評価結果に基づいてブール値を返します。|
|[ConvertFromBase64](#convertfrombase64)|ConvertFromBase64 関数は、指定した base64 でエンコードされた値を正規の文字列に変換します。|
|[ConvertToBase64](#converttobase64)|ConvertToBase64 関数は、文字列を Unicode の base64 文字列に変換します。 |
|[ConvertToUTF8Hex](#converttoutf8hex)|ConvertToUTF8Hex 関数は、文字列を UTF8 の 16 進数でエンコードされた値に変換します。|
|[Count](#count)|Count 関数は、複数値の属性内の要素数を返します。|
|[Cstr](#cstr)|CStr 関数は、文字列データ型に変換します。|
|[DateFromNum](#datefromnum)|DateFromNum 関数は、AD の日付形式の値を DateTime 型に変換します。|
|[DNComponent](#dncomponent)|DNComponent 関数は、指定した DN コンポーネントの値を左から返します。|
|[Error](#error)|Error 関数は、カスタム エラーを返すために使用します。|
|[FormatDateTime](#formatdatetime) |1 つの形式の日付文字列を受け取り、別の形式に変換します。| 
|[GUID](#guid)|GUID 関数は、新しいランダムな GUID を生成します。|           
|[IIF](#iif)|IIF 関数は、指定した条件に基づいて、使用できる一連の値のうち、いずれかを返します。|
|[InStr](#instr)|InStr 関数は文字列内の最初の部分文字列を検索します。|
|[IsNull](#isnull)|式の評価結果が Null の場合、IsNull 関数は true を返します。|
|[IsNullOrEmpty](#isnullorempty)|式が null または空の文字列の場合、IsNullOrEmpty 関数は true を返します。|         
|[IsPresent](#ispresent)|式の評価結果が Null でもなく、空でもない文字列の場合、IsPresent 関数は true を返します。|    
|[IsString](#isstring)|式を文字列型として評価できる場合、IsString 関数の評価結果は True になります。|
|[Item](#item)|Item 関数は複数値の文字列/属性から 1 つの項目を返します。|
|[Join](#join) |Join() は Append() によく似ていますが、Join() では複数の **source** 文字列値を 1 つの文字列に結合できます。文字列値は **separator** で区切って指定します。| 
|[Left](#left)|Left 関数は文字列の左端から数えて指定した文字数分の文字を返します。|
|[Mid](#mid) |source 値の部分文字列を返します。 部分文字列は、source 文字列の文字のみを含む文字列です。|
|[NormalizeDiacritics](#normalizediacritics)|1 つの文字列引数が必要です。 文字列を返しますが、分音記号はそれ以外の同等の記号で置換されます。|
|[Not](#not) |**source** のブール値を反転します。 **source** 値が "*True*" の場合は "*False*" を返します。 "False" の場合は "*True*" を返します。| 
|[RemoveDuplicates](#removeduplicates)|RemoveDuplicates 関数は複数値の文字列を受け取り、各値が一意になるように処理します。| 
|[Replace](#replace) |文字列内の値を置換します。 | 
|[SelectUniqueValue](#selectuniquevalue)|少なくとも 2 つの引数が必要です。引数は、式を使用して定義されている一意値生成ルールです。 関数では、各ルールが評価された後、生成された値の対象となるアプリ/ディレクトリでの一意性が確認されます。| 
|[SingleAppRoleAssignment](#singleapproleassignment)|指定したアプリケーションで 1 人のユーザーに割り当てられた appRoleAssignments の全一覧から、単一の appRoleAssignment を返します。| 
|[Split](#split)|指定された区切り記号文字を使用して、文字列を複数値の配列に分割します。|
|[StringFromSID](#stringfromsid)|StringFromSid 関数は、セキュリティ識別子が含まれるバイト配列を文字列に変換します。| 
|[StripSpaces](#stripspaces) |source 文字列からすべての空白文字 (" ") を削除します。| 
|[スイッチ](#switch)|**source** 値が **key** と一致するときに、その **key** の **value** を返します。 | 
|[ToLower](#tolower)|*source* 文字列値を受け取り、指定されたカルチャ ルールを使用して小文字に変換します。| 
|[ToUpper](#toupper)|*source* 文字列値を受け取り、指定されたカルチャ ルールを使用して大文字に変換します。|
|[Trim](#trim)|Trim 関数は、文字列の先頭と末尾の空白文字を削除します。|
|[Word](#word)|Word 関数は、使用する区切り記号と返す単語の番号を表すパラメーターに基づいて、文字列内に含まれる単語を返します。|

---
### <a name="append"></a>Append
**関数:**<br> Append(source, suffix)

**説明:**<br> source 文字列値を受け取り、その末尾に suffix を追加します。

**パラメーター:**<br> 

   | 名前 | 必須/繰り返し | Type | Notes |
   | --- | --- | --- | --- |
   | **source** |必須 |String |通常は、source オブジェクトの属性の名前。 |
   | **suffix** |必須 |String |source 値の末尾に追加する文字列。 |

---
### <a name="bitand"></a>BitAnd
**説明:**  
BitAnd 関数は、値に指定のビットを設定します。

**構文 :**  
`num BitAnd(num value1, num value2)`

* value1, value2: ともに AND になる数値

**備考:**  
この関数は両方のパラメーターをバイナリ表現に変換し、ビットを次に設定します。

* 0 - *value1* と *value2* 内の対応するビットの 1 つまたは両方が 0 の場合
* 1 - 対応するビットの両方が 1 の場合。

つまり、両方のパラメーターの対応するビットが 1 の場合を除くすべてのケースで 0 を返します。

**例:**  
 
 `BitAnd(&HF, &HF7)`</br>
 16 進数の "F" と "F7" の AND は 7 と評価されるため、7 を返します。

---

### <a name="cbool"></a>CBool
**説明:**  
CBool 関数は、式の評価結果に基づいてブール値を返します。

**構文 :**  
`bool CBool(exp Expression)`

**備考:**  
式の評価結果が 0 以外の値の場合は CBool によって True が返され、それ以外の場合は False が返されます。

**例:**  
`CBool([attrib1] = [attrib2])`  

両方の属性が同じ値を持つ場合は、True を返します。

---
### <a name="convertfrombase64"></a>ConvertFromBase64
**説明:**  
ConvertFromBase64 関数は、指定した base64 でエンコードされた値を正規の文字列に変換します。

**構文 :**  
`str ConvertFromBase64(str source)` - エンコードには Unicode を想定しています  
`str ConvertFromBase64(str source, enum Encoding)`

* source:Base64 でエンコードされた文字列  
* Encoding:Unicode、ASCII、UTF8

**例**  
`ConvertFromBase64("SABlAGwAbABvACAAdwBvAHIAbABkACEA")`  
`ConvertFromBase64("SGVsbG8gd29ybGQh", UTF8)`

どちらの例でも "*Hello world!* " を返します。

---
### <a name="converttobase64"></a>ConvertToBase64
**説明:**  
ConvertToBase64 関数は、文字列を Unicode の base64 文字列に変換します。  
整数の配列の値を、base 64 桁でエンコードされているそれと同等の文字列表現に変換します。

**構文 :**  
`str ConvertToBase64(str source)`

**例:**  
`ConvertToBase64("Hello world!")`  
"SABlAGwAbABvACAAdwBvAHIAbABkACEA" を返します。

---
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**説明:**  
ConvertToUTF8Hex 関数は、文字列を UTF8 の 16 進数でエンコードされた値に変換します。

**構文 :**  
`str ConvertToUTF8Hex(str source)`

**備考:**  
この関数の出力形式は、DN 属性の形式として Azure Active Directory で使用されます。

**例:**  
`ConvertToUTF8Hex("Hello world!")`  
48656C6C6F20776F726C6421 を返します。

---
### <a name="count"></a>Count
**説明:**  
Count 関数は、複数値の属性内の要素数を返します。

**構文 :**  
`num Count(mvstr attribute)`

---
### <a name="cstr"></a>CStr
**説明:**  
CStr 関数は、文字列データ型に変換します。

**構文 :**  
`str CStr(num value)`  
`str CStr(ref value)`  
`str CStr(bool value)`  

* value: 数値、参照属性、ブール値を指定できます。

**例:**  
`CStr([dn])`  
"cn=Joe,dc=contoso,dc=com" を返します。

---
### <a name="datefromnum"></a>DateFromNum
**説明:**  
DateFromNum 関数は、AD の日付形式の値を DateTime 型に変換します。

**構文 :**  
`dt DateFromNum(num value)`

**例:**  
`DateFromNum([lastLogonTimestamp])`  
`DateFromNum(129699324000000000)`  
2012-01-01 23:00:00 を表す DateTime を返します。

---
### <a name="dncomponent"></a>DNComponent
**説明:**  
DNComponent 関数は、指定した DN コンポーネントの値を左から返します。

**構文 :**  
`str DNComponent(ref dn, num ComponentNumber)`

* dn: 解釈する参照属性
* ComponentNumber:返される DN のコンポーネント

**例:**  
`DNComponent(CRef([dn]),1)`  
dn が "cn=Joe,ou=…," の場合は、Joe が返されます。

---
### <a name="error"></a>エラー
**説明:**  
Error 関数は、カスタム エラーを返すために使用します。

**構文 :**  
`void Error(str ErrorMessage)`

**例:**  
`IIF(IsPresent([accountName]),[accountName],Error("AccountName is required"))`  
属性 accountName が存在しない場合は、オブジェクトでエラーをスローします。

---
### <a name="formatdatetime"></a>FormatDateTime
**関数:**<br> FormatDateTime(source, inputFormat, outputFormat)

**説明:**<br> 1 つの形式の日付文字列を受け取り、別の形式に変換します。

**パラメーター:**<br> 

   | 名前 | 必須/繰り返し | Type | Notes |
   | --- | --- | --- | --- |
   | **source** |必須 |String |通常は、source オブジェクトの属性の名前。 |
   | **inputFormat** |必須 |String |有効な形式の source 値。 サポートされている形式については、[/dotnet/standard/base-types/custom-date-and-time-format-strings](/dotnet/standard/base-types/custom-date-and-time-format-strings) を参照してください。 |
   | **outputFormat** |必須 |String |出力日付の形式。 |

---
### <a name="guid"></a>Guid
**説明:**  
GUID 関数は、新しいランダムな GUID を生成します。

**構文 :**  
`str Guid()`

---
### <a name="iif"></a>IIF
**説明:**  
IIF 関数は、指定した条件に基づいて、使用できる一連の値のうち、いずれかを返します。

**構文 :**  
`var IIF(exp condition, var valueIfTrue, var valueIfFalse)`

* condition: 評価結果が true または false になる任意の値または式。
* valueIfTrue:条件の評価結果が true の場合に返される値。
* valueIfFalse:条件の評価結果が false の場合に返される値。

**例:**  
`IIF([employeeType]="Intern","t-" & [alias],[alias])`  
 ユーザーがインターンの場合はユーザーのエイリアスの先頭に "t-" を付けて返し、そうでない場合はユーザーのエイリアスをそのまま返します。

---
### <a name="instr"></a>InStr
**説明:**  
InStr 関数は文字列内の最初の部分文字列を検索します。

**構文 :**  

`num InStr(str stringcheck, str stringmatch)`  
`num InStr(str stringcheck, str stringmatch, num start)`  
`num InStr(str stringcheck, str stringmatch, num start, enum compare)`

* stringcheck: 検索対象の文字列
* stringmatch: 検出対象の文字列
* start: 部分文字列の検索開始位置
* compare: vbTextCompare または vbBinaryCompare

**備考:**  
部分文字列が見つかった位置を返します。見つからなかった場合は 0 を返します。

**例:**  
`InStr("The quick brown fox","quick")`  
評価結果は 5 になります。

`InStr("repEated","e",3,vbBinaryCompare)`  
評価結果は 7 になります。

---
### <a name="isnull"></a>IsNull
**説明:**  
式の評価結果が Null の場合、IsNull 関数は true を返します。

**構文 :**  
`bool IsNull(var Expression)`

**備考:**  
属性の場合、Null は属性の不在によって表されます。

**例:**  
`IsNull([displayName])`  
CS または MV に属性がない場合は True を返します。

---
### <a name="isnullorempty"></a>IsNullOrEmpty
**説明:**  
式が null または空の文字列の場合、IsNullOrEmpty 関数は true を返します。

**構文 :**  
`bool IsNullOrEmpty(var Expression)`

**備考:**  
属性の場合は、属性がないか、存在しても空の文字列の場合、評価結果は True になります。  
この関数の逆の関数は IsPresent です。

**例:**  
`IsNullOrEmpty([displayName])`  
CS または MV に属性がないか、空の文字列の場合は True を返します。

---
### <a name="ispresent"></a>IsPresent
**説明:**  
式の評価結果が Null でもなく、空でもない文字列の場合、IsPresent 関数は true を返します。

**構文 :**  
`bool IsPresent(var expression)`

**備考:**  
この関数の逆関数は IsNullOrEmpty です。

**例:**  
`Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])`

---
### <a name="item"></a>Item
**説明:**  
Item 関数は複数値の文字列/属性から 1 つの項目を返します。

**構文 :**  
`var Item(mvstr attribute, num index)`

* attribute: 複数値の属性
* index: 複数値の文字列内の項目へのインデックス。

**備考:**  
Contains 関数は複数値の属性内の項目に対するインデックスを返すため、Item 関数を Contains 関数と共に使用すると便利です。

インデックスが範囲外にある場合は、エラーをスローします。

**例:**  
`Mid(Item([proxyAddresses],Contains([proxyAddresses], "SMTP:")),6)`  
プライマリ メール アドレスを返します。

---
### <a name="isstring"></a>IsString
**説明:**  
式を文字列型として評価できる場合、IsString 関数の評価結果は True になります。

**構文 :**  
`bool IsString(var expression)`

**備考:**  
CStr() が式の解析に成功するかどうかを判断するために使用します。

---
### <a name="join"></a>Join
**関数:**<br> Join(separator, source1, source2, …)

**説明:**<br> Join() は Append() によく似ていますが、Join() では複数の **source** 文字列値を 1 つの文字列に結合できます。文字列値は **separator** で区切って指定します。

source 値の 1 つが複数値属性である場合は、その属性のすべての値を結合し、separator 値で区切ります。

**パラメーター:**<br> 

   | 名前 | 必須/繰り返し | Type | Notes |
   | --- | --- | --- | --- |
   | **separator** |必須 |String |source 値を 1 つの文字列に連結するときに、各値を区切るのに使用する文字列。 区切り記号が必要ない場合は、“” とすることができます。 |
   | **source1  … sourceN** |必須、回数は可変 |String |結合する文字列値。 |

---
### <a name="left"></a>Left
**説明:**  
Left 関数は文字列の左端から数えて指定した文字数分の文字を返します。

**構文 :**  
`str Left(str string, num NumChars)`

* string: 返される文字を含む文字列
* NumChars: 文字列の左端から数えて返される文字数を指定する数値

**備考:**  
string 内の最初の numChars 文字分の文字を含む文字列。

* numChars = 0 の場合、空の文字列を返します。
* numChars < 0 の場合、入力文字列を返します。
* string が null の場合、空の文字列を返します。

string に含まれる文字数が numChars で指定した数より少ない場合は、string と同一の文字列 (パラメーター 1 のすべての文字が含まれる) が返されます。

**例:**  
`Left("John Doe", 3)`  
`Joh` が返されます。

---
### <a name="mid"></a>Mid
**関数:**<br> Mid(source, start, length)

**説明:**<br> source 値の部分文字列を返します。 部分文字列は、source 文字列の文字のみを含む文字列です。

**パラメーター:**<br> 

   | 名前 | 必須/繰り返し | Type | Notes |
   | --- | --- | --- | --- |
   | **source** |必須 |String |通常、属性の名前。 |
   | **start** |必須 |整数 (integer) |部分文字列が始まる **source** 文字列のインデックス。 文字列内の最初の文字のインデックスは 1、2 番目の文字のインデックスは 2です (以降同様)。 |
   | **length** |必須 |整数 (integer) |部分文字列の長さ。 length が **source** 文字列の外で終わる場合は、**start** インデックスから **source** 文字列の末尾までの部分文字列を返します。 |

---
### <a name="normalizediacritics"></a>NormalizeDiacritics
**関数:**<br> NormalizeDiacritics(source)

**説明:**<br> 1 つの文字列引数が必要です。 文字列を返しますが、分音記号はそれ以外の同等の記号で置換されます。 通常、分音記号 (アクセント記号) を含む姓と名を、さまざまなユーザー識別子 (ユーザー プリンシパル名、SAM アカウント名、電子メール アドレスなど) で使用できる有効な値に変換するために使用します。

**パラメーター:**<br> 

   | 名前 | 必須/繰り返し | Type | Notes |
   | --- | --- | --- | --- |
   | **source** |必須 |String | 通常は、名または姓の属性です。 |

---
### <a name="not"></a>Not
**関数:**<br> Not(source)

**説明:**<br> **source** のブール値を反転します。 **source** 値が "*True*" の場合は "*False*" を返します。 "False" の場合は "*True*" を返します。

**パラメーター:**<br> 

   | 名前 | 必須/繰り返し | Type | Notes |
   | --- | --- | --- | --- |
   | **source** |必須 |Boolean String |有効な **source** 値は "True" または "False" です。 |

---
### <a name="removeduplicates"></a>RemoveDuplicates
**説明:**  
RemoveDuplicates 関数は複数値の文字列を受け取り、各値が一意になるように処理します。

**構文 :**  
`mvstr RemoveDuplicates(mvstr attribute)`

**例:**  
`RemoveDuplicates([proxyAddresses])`  
重複する値がすべて削除された、校正済みの proxyAddress 属性を返します。

---
### <a name="replace"></a>Replace
**関数:**<br> Replace(source, oldValue, regexPattern, regexGroupName, replacementValue, replacementAttributeName, template)

**説明:**<br>
文字列内の値を置換します。 指定されたパラメーターに応じて異なる動作をします。

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

**パラメーター:**<br> 

   | 名前 | 必須/繰り返し | Type | Notes |
   | --- | --- | --- | --- |
   | **source** |必須 |String |通常は、**source** オブジェクトの属性の名前。 |
   | **oldValue** |省略可能 |String |**source** または **template** に含まれる置換前の値。 |
   | **regexPattern** |省略可能 |String |**source** に含まれる置換前の値の正規表現パターン。 または、**replacementPropertyName** が使われるときは、**replacementPropertyName** から値を抽出するパターン。 |
   | **regexGroupName** |省略可能 |String |**regexPattern** 内のグループの名前。 **replacementPropertyName** を使用した場合にのみ、このグループの値が **replacementPropertyName** から **replacementValue** として抽出されます。 |
   | **replacementValue** |省略可能 |String |古い値を置き換える新しい値。 |
   | **replacementAttributeName** |省略可能 |String |置換値に使用する属性の名前 |
   | **template** |省略可能 |String |**template** の値を指定した場合、template 内で **oldValue** が検索され、**source** の値で置換されます。 |

---
### <a name="selectuniquevalue"></a>SelectUniqueValue
**関数:**<br> SelectUniqueValue(uniqueValueRule1, uniqueValueRule2, uniqueValueRule3, …)

**説明:**<br> 少なくとも 2 つの引数が必要です。引数は、式を使用して定義されている一意値生成ルールです。 関数では、各ルールが評価された後、生成された値の対象となるアプリ/ディレクトリでの一意性が確認されます。 最初に見つかった一意の値が返されます。 すべての値がターゲットに既に存在する場合、エントリはエスクローされて、理由が監査ログに記録されます。 渡すことができる引数の数に上限はありません。

> [!NOTE]
> - これは最上位の関数であり、入れ子にはできません。
> - この関数は、照合の優先順位を持つ属性には適用できません。  
> - この関数は、エントリの作成に使用されることだけを目的としたものです。 属性で使用するときは、 **[Apply Mapping]\(マッピングの適用\)** プロパティを **[オブジェクトの作成中のみ]** に設定します。
> - 現在、この関数は "Workday to Active Directory User Provisioning" でのみサポートされています。 他のプロビジョニング アプリでは使用できません。 


**パラメーター:**<br> 

   | 名前 | 必須/繰り返し | Type | Notes |
   | --- | --- | --- | --- |
   | **uniqueValueRule1  … uniqueValueRuleN** |2 つ以上必要であり、上限はありません |String | 評価する一意値生成ルールの一覧。 |


---
### <a name="singleapproleassignment"></a>SingleAppRoleAssignment
**関数:**<br> SingleAppRoleAssignment(appRoleAssignments)

**説明:**<br> 指定したアプリケーションで 1 人のユーザーに割り当てられた appRoleAssignments の全一覧から、単一の appRoleAssignment を返します。 appRoleAssignments オブジェクトを単一のロール名文字列に変換するために、この関数が必要になります。 ベスト プラクティスは、確実に 1 つの appRoleAssignment だけが一度に 1 人のユーザーに割り当てられるようにすることです。複数のロールが割り当てられると、返されるロール文字列を予測できません。 

**パラメーター:**<br> 

  | 名前 | 必須/繰り返し | Type | Notes |
  |--- | --- | --- | --- |
  | **appRoleAssignments** |必須 |String |**appRoleAssignments** オブジェクト |

---
### <a name="split"></a>Split
**関数:**<br> Split(source, delimiter)

**説明:**<br> 指定された区切り記号文字を使用して、文字列を複数値の配列に分割します。

**パラメーター:**<br> 

   | 名前 | 必須/繰り返し | Type | Notes |
   | --- | --- | --- | --- |
   | **source** |必須 |String |**source** 値。 |
   | **delimiter** |必須 |String |文字列の分割に使用される文字を指定します (例: ",") |

---
### <a name="stringfromsid"></a>StringFromSid
**説明:**  
StringFromSid 関数は、セキュリティ識別子が含まれるバイト配列を文字列に変換します。

**構文 :**  
`str StringFromSid(bin ObjectSID)`  

---
### <a name="stripspaces"></a>StripSpaces
**関数:**<br> StripSpaces(source)

**説明:**<br> source 文字列からすべての空白文字 (" ") を削除します。

**パラメーター:**<br> 

   | 名前 | 必須/繰り返し | Type | Notes |
   | --- | --- | --- | --- |
   | **source** |必須 |String |**source** 値。 |

---
### <a name="switch"></a>Switch
**関数:**<br> Switch(source, defaultValue, key1, value1, key2, value2, …)

**説明:**<br> **source** 値が **key** と一致するときに、その **key** の **value** を返します。 **source** 値がどの key とも一致しない場合は、**defaultValue** を返します。  **key** と **value** パラメーターは、常にペアで指定する必要があります。 この関数には、常に、偶数個のパラメーターを指定する必要があります。

**パラメーター:**<br> 

   | 名前 | 必須/繰り返し | Type | Notes |
   | --- | --- | --- | --- |
   | **source** |必須 |String |確認する **source** 値。 |
   | **defaultValue** |省略可能 |String |source がどの key とも一致しないときに使用される既定値。 空の文字列 ("") を指定できます。 |
   | **key** |必須 |String |**source** 値と比較する **key**。 |
   | **value** |必須 |String |key と一致する **source** の置換値。 |

---
### <a name="tolower"></a>ToLower
**関数:**<br> ToLower(source, culture)

**説明:**<br> *source* 文字列値を受け取り、指定されたカルチャ ルールを使用して小文字に変換します。 *culture* 情報が指定されていない場合は、インバリアント カルチャが使用されます。

**パラメーター:**<br> 

   | 名前 | 必須/繰り返し | Type | Notes |
   | --- | --- | --- | --- |
   | **source** |必須 |String |通常は、source オブジェクトの属性の名前。 |
   | **culture** |省略可能 |String |RFC 4646 に基づくカルチャ名の形式は、*languagecode2-country/regioncode2* です。ここで、*languagecode2* は 2 文字の言語コードで、*country/regioncode2* は 2 文字のサブカルチャ コードです。 例には、日本語 (日本) の場合の ja-JP と英語 (米国) の場合の en-US が含まれています。 2 文字の言語コードが使用できない場合は、ISO 639-2 から派生した 3 文字のコードが使用されます。|

---

### <a name="toupper"></a>ToUpper
**関数:**<br> ToUpper(source, culture)

**説明:**<br> *source* 文字列値を受け取り、指定されたカルチャ ルールを使用して大文字に変換します。 *culture* 情報が指定されていない場合は、インバリアント カルチャが使用されます。

**パラメーター:**<br> 

  | 名前 | 必須/繰り返し | Type | Notes |
  | --- | --- | --- | --- |
  | **source** |必須 |String |通常は、source オブジェクトの属性の名前。 |
  | **culture** |省略可能 |String |RFC 4646 に基づくカルチャ名の形式は、*languagecode2-country/regioncode2* です。ここで、*languagecode2* は 2 文字の言語コードで、*country/regioncode2* は 2 文字のサブカルチャ コードです。 例には、日本語 (日本) の場合の ja-JP と英語 (米国) の場合の en-US が含まれています。 2 文字の言語コードが使用できない場合は、ISO 639-2 から派生した 3 文字のコードが使用されます。|

---

### <a name="trim"></a>Trim
**説明:**  
Trim 関数は、文字列の先頭と末尾の空白文字を削除します。

**構文 :**  
`str Trim(str value)`  

**例:**  
`Trim(" Test ")`  
"test" を返します。

`Trim([proxyAddresses])`  
proxyAddress 属性の値ごとに先頭と末尾の空白文字を削除します。

---
### <a name="word"></a>Word
**説明:**  
Word 関数は、使用する区切り記号と返す単語の番号を表すパラメーターに基づいて、文字列内に含まれる単語を返します。

**構文 :**  
`str Word(str string, num WordNumber, str delimiters)`

* string: 返される単語を含む文字列。
* WordNumber: 返すべき単語の番号を指定する数値。
* delimiters: 単語を識別するために使用される区切り記号を表す文字列

**備考:**  
delimiters 内のいずれかの文字で区切られた string 内の各文字列が、単語として識別されます。

* num < 1 の場合、空の文字列を返します。
* string が null の場合、空の文字列を返します。

string に含まれる単語の数が指定より少ないか、区切り記号文字で識別されるどの単語も string に含まれていない場合は、空の文字列が返されます。

**例:**  
`Word("The quick brown fox",3," ")`  
"brown" を返します。

`Word("This,string!has&many separators",3,",!&#")`  
"has" を返します。

## <a name="examples"></a>例
### <a name="strip-known-domain-name"></a>既知のドメイン名をストリップする
ユーザーの電子メールから既知のドメイン名をストリップして、ユーザー名を取得する必要があります。 <br>
たとえば、ドメインが "contoso.com" の場合は、次の式を使用することができます。

**Expression:** <br>
`Replace([mail], "@contoso.com", , ,"", ,)`

**サンプル入力/出力:** <br>

* **入力** (mail): "john.doe@contoso.com"
* **出力**: "john.doe"

### <a name="append-constant-suffix-to-user-name"></a>ユーザー名に定数のサフィックスを追加する
Salesforce Sandbox を使用している場合は、ユーザー名を同期する前に、すべてのユーザー名に追加のサフィックスを追加する必要があります。

**Expression:** <br>
`Append([userPrincipalName], ".test")`

**サンプル入力/出力:** <br>

* **入力**: (userPrincipalName): "John.Doe@contoso.com"
* **出力**:  "John.Doe@contoso.com.test"

### <a name="generate-user-alias-by-concatenating-parts-of-first-and-last-name"></a>姓の一部と名の一部を連結することでユーザー エイリアスを生成する
ユーザーの名の最初の 3 文字とユーザーの姓の最初の 5 文字を取得することでユーザー エイリアスを生成する必要があります。

**Expression:** <br>
`Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**サンプル入力/出力:** <br>

* **入力** (givenName):"John"
* **入力** (surname):"Doe"
* **出力**:"JohDoe"

### <a name="remove-diacritics-from-a-string"></a>文字列から分音記号を削除する
アクセント記号を含む文字を、アクセント記号を含まない同等の文字に置換する必要があります。

**Expression:** <br>
NormalizeDiacritics([givenName])

**サンプル入力/出力:** <br>

* **入力** (givenName):"Zoë"
* **出力**:"Zoe"

### <a name="split-a-string-into-a-multi-valued-array"></a>文字列を複数値の配列に分割します
コンマ区切りの一覧になっている文字列を受け取り、Salesforce の PermissionSets 属性などの複数値の属性にプラグインできる配列に分割する必要があります。 この例では、アクセス許可セットの一覧が、Azure AD の extensionAttribute5 に格納されています。

**Expression:** <br>
Split([extensionAttribute5], ",")

**サンプル入力/出力:** <br>

* **INPUT** (extensionAttribute5):
* **OUTPUT**:  ["PermissionSetOne", "PermissionSetTwo"]

### <a name="output-date-as-a-string-in-a-certain-format"></a>特定の形式の文字列として日付を出力する
SaaS アプリケーションに特定の形式で日付を送信します。 <br>
たとえば、ServiceNow 向けに日付の書式を設定します。

**Expression:** <br>

`FormatDateTime([extensionAttribute1], "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**サンプル入力/出力:**

* **入力** (extensionAttribute1):"20150123105347.1Z"
* **出力**:"2015-01-23"

### <a name="replace-a-value-based-on-predefined-set-of-options"></a>定義済みのオプション セットに基づいて値を置換する

Azure AD に格納されている都道府県コードに基づいて、ユーザーのタイム ゾーンを定義する必要があります。 <br>
都道府県コードが定義済みオプションのいずれにも一致しない場合は、既定値 "Australia/Sydney" を使用します。

**Expression:** <br>
`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**サンプル入力/出力:**

* **入力** (state):"QLD"
* **出力**:"Australia/Brisbane"

### <a name="replace-characters-using-a-regular-expression"></a>正規表現を使用して文字を置換します
正規表現の値と一致する文字を見つけて削除する必要があります。

**Expression:** <br>

Replace([mailNickname], , "[a-zA-Z_]*", , "", , )

**サンプル入力/出力:**

* **INPUT** (mailNickname: "john_doe72"
* **出力**:"72"

### <a name="convert-generated-userprincipalname-upn-value-to-lower-case"></a>生成された userPrincipalName (UPN) の値を小文字に変換します
次の例では、PreferredFirstName および PreferredLastName ソース フィールドを連結することで UPN 値が生成され、ToLower 関数は、すべての文字を小文字に変換するために、生成された文字列で機能します。 

`ToLower(Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"))`

**サンプル入力/出力:**

* **入力** (PreferredFirstName):"John"
* **入力** (PreferredLastName):"Smith"
* **出力**: "john.smith@contoso.com"

### <a name="generate-unique-value-for-userprincipalname-upn-attribute"></a>userPrincipalName (UPN) 属性用に一意の値を生成する
ユーザーの名、ミドル ネーム、姓を基にして UPN 属性の値を生成し、値を UPN 属性に割り当てる前に、対象の AD ディレクトリで値が一意であることを確認する必要があります。

**Expression:** <br>

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


## <a name="next-steps"></a>次のステップ 

- [プロビジョニングとは](what-is-provisioning.md)
- [Azure AD Connect クラウド同期とは](what-is-cloud-sync.md)