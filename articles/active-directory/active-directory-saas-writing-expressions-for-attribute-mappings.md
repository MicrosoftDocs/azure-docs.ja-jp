---
title: "Azure Active Directory における属性マッピングの式の書き方 | Microsoft Docs"
description: "Azure Active Directory で SaaS アプリ オブジェクトを自動プロビジョニングしているときに、式マッピングを使用して属性値を許容される形式に変換する方法について説明します。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: b13c51cd-1bea-4e5e-9791-5d951a518943
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: d49236c634be8260cb0fb24a0ee08cd71353f6ba


---
# <a name="writing-expressions-for-attribute-mappings-in-azure-active-directory"></a>Azure Active Directory における属性マッピングの式の書き方
SaaS アプリケーションに対してプロビジョニングを構成するときに指定できる属性マッピングの種類の 1 つは、式マッピングです。 この場合は、ユーザーのデータを SaaS アプリケーションが許容可能な形式に変換することができる、スクリプトのような式を記述する必要があります。

## <a name="syntax-overview"></a>構文の概要
属性マッピングの式の構文は、Visual Basic のApplications (VBA) 関数に似ています。

* 式全体は、関数の形式で定義する必要があります。名前の後にかっこで囲んだ引数を続けます。 <br>
  *FunctionName(<<引数 1>>,<<argument N>>)*
* 各関数内で他の関数を入れ子にすることができます。 次に例を示します。 <br> *FunctionOne (FunctionTwo(<<argument1>>))*
* 関数には、次の 3 つの異なる種類の引数を渡すことができます。
  
  1. 属性。角かっこで囲む必要があります。 例: [attributeName]
  2. 文字列定数。二重引用符で囲む必要があります。 例: "United States"
  3. 他の関数 例: FunctionOne(<<argument1>>, FunctionTwo(<<argument2>>))
* 文字列定数では、文字列に円記号 (\) または引用符 (") を含める必要がある場合は、円記号 (\) でエスケープする必要があります。 例: "Company name: \"Contoso\""

## <a name="list-of-functions"></a>関数の一覧
[追加](#append) &nbsp;&nbsp;&nbsp;&nbsp; [FormatDateTime](#formatdatetime) &nbsp;&nbsp;&nbsp;&nbsp; [Join](#join) &nbsp;&nbsp;&nbsp;&nbsp; [Mid](#mid) &nbsp;&nbsp;&nbsp;&nbsp; [Not](#not) &nbsp;&nbsp;&nbsp;&nbsp; [*Views\\Home\\AllDates.cshtml*](#replace) &nbsp;&nbsp;&nbsp;&nbsp; [StripSpaces](#stripspaces) &nbsp;&nbsp;&nbsp;&nbsp; [Switch](#switch)

- - -
### <a name="append"></a>追加
**関数:**<br>  Append(source, suffix)

**説明:**<br>  source 文字列値を受け取り、その末尾に suffix を追加します。

**パラメーター:**<br> 

| 名前 | 必須/繰り返し | 型 | メモ |
| --- | --- | --- | --- |
| **source セクション** |必須 |String |通常は、source オブジェクトの属性の名前。 |
| **suffix** |必須 |String |source 値の末尾に追加する文字列。 |

- - -
### <a name="formatdatetime"></a>FormatDateTime
**関数:**<br>  FormatDateTime(source, inputFormat, outputFormat)

**説明:**<br> 1 つの形式の日付文字列を受け取り、別の形式に変換します。

**パラメーター:**<br> 

| 名前 | 必須/繰り返し | 型 | メモ |
| --- | --- | --- | --- |
| **source セクション** |必須 |String |通常は、source オブジェクトの属性の名前。 |
| **inputFormat** |必須 |String |有効な形式の source 値。 サポートされる形式については、 [http://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx](http://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx)を参照してください。 |
| **outputFormat** |必須 |String |出力日付の形式。 |

- - -
### <a name="join"></a>Join
**関数:**<br>  Join(separator, source1, source2, …)

**説明:**<br> Join() は Append() によく似ていますが、Join() では複数の **source** 文字列値を 1 つの文字列に結合できます。文字列値は **separator** で区切って指定します。

source 値の 1 つが複数値属性である場合は、その属性のすべての値を結合し、separator 値で区切ります。

**パラメーター:**<br> 

| 名前 | 必須/繰り返し | 型 | メモ |
| --- | --- | --- | --- |
| **separator** |必須 |String |source 値を 1 つの文字列に連結するときに、各値を区切るのに使用する文字列。 区切り記号が必要ない場合は、“” とすることができます。 |
| **source1 … sourceN ** |必須、回数は可変 |String |結合する文字列値。 |

- - -
### <a name="mid"></a>Mid
**関数:**<br>  Mid(source, start, length)

**説明:**<br>  source 値の部分文字列を返します。 部分文字列は、source 文字列の文字のみを含む文字列です。

**パラメーター:**<br> 

| 名前 | 必須/繰り返し | 型 | メモ |
| --- | --- | --- | --- |
| **source セクション** |必須 |String |通常、属性の名前。 |
| **start** |必須 |integer |部分文字列が始まる **source** 文字列のインデックス。 文字列内の最初の文字のインデックスは 1、2 番目の文字のインデックスは 2です (以降同様)。 |
| **length** |必須 |integer |部分文字列の長さ。 length が **source** 文字列の外で終わる場合は、**start** インデックスから **source** 文字列の末尾までの部分文字列を返します。 |

- - -
### <a name="not"></a>not
**関数:**<br>  Not(source)

**説明:**<br> **source** のブール値を反転します。 **source** 値が "*True*" の場合は "*False*" を返します。 "False" の場合は "*True*" を返します。

**パラメーター:**<br> 

| 名前 | 必須/繰り返し | 型 | メモ |
| --- | --- | --- | --- |
| **source セクション** |必須 |Boolean String |有効な **source** 値は "True" または "False" です。 |

- - -
### <a name="replace"></a>Replace
**関数:**<br>  ObsoleteReplace(source, oldValue, regexPattern, regexGroupName, replacementValue, replacementAttributeName, template)

**説明:**<br>
 文字列内の値を置換します。 指定されたパラメーターに応じて異なる動作をします。

* **oldValue** と **replacementValue** が指定された場合:
  
  * source に含まれるすべての OldValue を replacementValue に置換します。
* **oldValue** と **template** が指定された場合:
  
  * **template** に含まれるすべての **OldValue** を **source** 値に置換します。
* **oldValueRegexPattern**、**oldValueRegexGroupName**、**replacementValue** が指定された場合:
  
  * source 文字列に含まれる OldValueRegexPattern に一致するすべての値を replacementValue に置換します。
* **oldValueRegexPattern**、**oldValueRegexGroupName**、**replacementPropertyName** が指定された場合:
  
  * **source** に値が指定されている場合は、**source** を返します。
  * **source** に値が指定されていない場合は、**oldValueRegexPattern** と **oldValueRegexGroupName** を使用して、**replacementPropertyName** を持つプロパティから置換値を抽出します。 置換値を、結果として返します。

**パラメーター:**<br> 

| 名前 | 必須/繰り返し | 型 | メモ |
| --- | --- | --- | --- |
| **source セクション** |必須 |String |通常は、source オブジェクトの属性の名前。 |
| **oldValue** |省略可能。 |String |**source** または **template** に含まれる置換前の値。 |
| **regexPattern** |省略可能。 |String |**source**に含まれる置換前の値の正規表現パターン。 または、replacementPropertyName を使用するときは、置換プロパティから値を抽出するパターン。 |
| **regexGroupName** |省略可能。 |String |**regexPattern**内のグループの名前。 ReplacementPropertyName を使用した場合にのみ、置換プロパティから replacementValue としてこのグループの値を抽出します。 |
| **replacementValue** |省略可能。 |String |古い値を置き換える新しい値。 |
| **replacementAttributeName** |省略可能。 |String |source に値を指定しないときに、置換値に使用する属性の名前。 |
| **template** |省略可能。 |String |**template** 値が指定されている場合は、template 内の **oldValue** を検索し、source 値で置換します。 |

- - -
### <a name="stripspaces"></a>StripSpaces
**関数:**<br>  StripSpaces(source)

**説明:**<br>  source 文字列からすべての空白文字 (" ") を削除します。

**パラメーター:**<br> 

| 名前 | 必須/繰り返し | 型 | メモ |
| --- | --- | --- | --- |
| **source セクション** |必須 |String |**source セクション セクション** 値。 |

- - -
### <a name="switch"></a>Switch
**関数:**<br>  Switch(source, defaultValue, key1, value1, key2, value2, …)

**説明:**<br> **source** 値が **key** と一致するときに、その **key** の **value** を返します。 **source** 値がどの key とも一致しない場合は、**defaultValue** を返します。  **key** と **value** パラメーターは、常にペアで指定する必要があります。 この関数には、常に、偶数個のパラメーターを指定する必要があります。

**パラメーター:**<br> 

| 名前 | 必須/繰り返し | 型 | メモ |
| --- | --- | --- | --- |
| **source セクション** |必須 |String |**Source** 値。 |
| **defaultValue** |省略可能。 |String |source がどの key とも一致しないときに使用される既定値。 空の文字列 ("") を指定できます。 |
| **key** |必須 |String |**source** 値と比較する **key**。 |
| **値** |必須 |String |key と一致する **source** の置換値。 |

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
`Append([userPrincipalName], ".test"))`

**サンプル入力/出力:** <br>

* **入力**: (userPrincipalName): "John.Doe@contoso.com"
* **出力**: "John.Doe@contoso.com.test"

### <a name="generate-user-alias-by-concatenating-parts-of-first-and-last-name"></a>姓の一部と名の一部を連結することでユーザー エイリアスを生成する
ユーザーの名の最初の 3 文字とユーザーの姓の最初の 5 文字を取得することでユーザー エイリアスを生成する必要があります。

**Expression:** <br>
`Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**サンプル入力/出力:** <br>

* **入力** (givenName): "John"
* **入力** (surname): "Doe"
* **出力**: "JohDoe"

### <a name="output-date-as-a-string-in-a-certain-format"></a>特定の形式の文字列として日付を出力する
SaaS アプリケーションに特定の形式で日付を送信します。 <br>
たとえば、ServiceNow 向けに日付の書式を設定します。

**Expression:** <br>

`FormatDateTime([extensionAttribute1], "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**サンプル入力/出力:**

* **入力** (extensionAttribute1): "20150123105347.1Z"
* **出力**:  "2015-01-23"

### <a name="replace-a-value-based-on-predefined-set-of-options"></a>定義済みのオプション セットに基づいて値を置換する
Azure AD に格納されている都道府県コードに基づいて、ユーザーのタイム ゾーンを定義する必要があります。 <br>
都道府県コードが定義済みオプションのいずれにも一致しない場合は、既定値 "Australia/Sydney" を使用します。

**Expression:** <br>

`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**サンプル入力/出力:**

* **入力** (state): "QLD"
* **出力**: "Australia/Brisbane"

## <a name="related-articles"></a>関連記事
* [Article Index for Application Management in Azure Active Directory](active-directory-apps-index.md)
* [Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](active-directory-saas-app-provisioning.md)
* [ユーザーのプロビジョニング用の属性マッピングのカスタマイズ](active-directory-saas-customizing-attribute-mappings.md)
* [ユーザーのプロビジョニング用のフィルターのスコープ](active-directory-saas-scoping-filters.md)
* [SCIM を使用して、Azure Active Directory からアプリケーションへのユーザーとグループの自動プロビジョニングを有効にする](active-directory-scim-provisioning.md)
* [アカウント プロビジョニング通知](active-directory-saas-account-provisioning-notifications.md)
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)




<!--HONumber=Nov16_HO3-->


