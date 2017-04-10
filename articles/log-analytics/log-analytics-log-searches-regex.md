---
title: "OMS Log Analytics のログ検索における正規表現 | Microsoft Docs"
description: "Log Analytics のログ検索で RegEx キーワードを使用すると、正規表現に基づいて結果をフィルター処理できます。  この記事では、正規表現の構文と例を示します。"
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2017
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: 73739f4f154bebe271ce29bd285122ea7f56d769
ms.openlocfilehash: bcf36cdec6c1dda7aa0213c42adf8d0281dc28d2
ms.lasthandoff: 02/23/2017


---
# <a name="using-regular-expressions-to-filter-log-searches-in-log-analytics"></a>正規表現を使用した Log Analytics のログ検索のフィルター処理

[ログ検索](log-analytics-log-searches.md)を使用すると、Log Analytics リポジトリから情報を抽出できます。  [フィルター式](log-analytics-search-reference.md#filter-expression)を使用すると、特定の条件に従って検索結果をフィルター処理できます。  **RegEx** キーワードを使用すると、そのフィルターの正規表現を指定できます。  

この記事では、Log Analytics で使用される正規表現の構文について詳しく説明します。


## <a name="regex-keyword"></a>RegEx キーワード

ログ検索で **RegEx** キーワードを使用するには、次の構文を使用します。  正規表現自体の構文については、この記事の他のセクションで確認できます。 

    field:Regex("Regular Expression")
    field=Regex("Regular Expression")

たとえば、正規表現を使用して種類が *Warning* または *Error* のアラート レコードを返すには、次のログ検索を使用します。 

    Type=Alert AlertSeverity=RegEx("Warning|Error")

## <a name="partial-matches"></a>部分一致
正規表現は、プロパティのテキスト全体に一致する必要があります。  部分一致ではレコードは返されません。  たとえば、srv01.contoso.com という名前のコンピューターのレコードを返そうとしている場合、次のログ検索ではレコードは**返されません**。

    Computer=RegEx("srv..") 

これは、名前の最初の部分しか正規表現に一致しないためです。  次の&2; つのログ検索は名前全体に一致するので、このコンピューターのレコードが返されます。 

    Computer=RegEx("srv..@")
    Computer=RegEx("srv...contoso.com") 

## <a name="characters"></a>文字
さまざまな文字を指定します。

| Character | Description | 例 | 一致の例 |
|:--|:--|:--|:--|
| a | この文字が&1; 回出現することを表します。 | Computer=RegEx("srv01.contoso.com") | srv01.contoso.com |
| に関するページを参照してください。 | 任意の&1; 文字を表します。 | Computer=RegEx("srv...contoso.com") | srv01.contoso.com<br>srv02.contoso.com<br>srv03.contoso.com |
| a? | この文字が&0; 回または&1; 回出現することを表します。 | Computer=RegEx("srv01?.contoso.com") | srv0.contoso.com<br>srv01.contoso.com |
| a* | この文字が&0; 回以上出現することを表します。 | Computer=RegEx("srv01*.contoso.com") | srv0.contoso.com<br>srv01.contoso.com<br>srv011.contoso.com<br>srv0111.contoso.com |
| a+ | この文字が&1; 回以上出現することを表します。 | Computer=RegEx("srv01+.contoso.com") | srv01.contoso.com<br>srv011.contoso.com<br>srv0111.contoso.com |
| [*abc*] | 角かっこ内の任意の&1; 文字と一致することを表します。 | Computer=RegEx("srv0[123].contoso.com") | srv01.contoso.com<br>srv02.contoso.com<br>srv03.contoso.com |
| [*a*-*z*] | この範囲内の&1; 文字と一致することを表します。  複数の範囲を含めることができます。 | Computer=RegEx("srv0[1-3].contoso.com") | srv01.contoso.com<br>srv02.contoso.com<br>srv03.contoso.com |
| [^*abc*] | 角かっこ内の文字以外であることを表します。 | Computer=RegEx("srv0[^123].contoso.com") | srv05.contoso.com<br>srv06.contoso.com<br>srv07.contoso.com |
| [^*a*-*z*] | この範囲内の文字以外であることを表します。 | Computer=RegEx("srv0[^1-3].contoso.com") | srv05.contoso.com<br>srv06.contoso.com<br>srv07.contoso.com |
| [*n*-*m*] | 数字の範囲に一致することを表します。 | Computer=RegEx("srv[01-03].contoso.com") | srv01.contoso.com<br>srv02.contoso.com<br>srv03.contoso.com |
| @ | 任意の文字列を表します。 | Computer=RegEx("srv@.contoso.com") | srv01.contoso.com<br>srv02.contoso.com<br>srv03.contoso.com |


## <a name="multiple-occurences-of-character"></a>文字の複数回の出現
特定の文字の出現回数を指定します。

| Character | Description | 例 | 一致の例 |
|:--|:--|:--|:--|
| a{n} |  この文字が *n* 回出現することを表します。 | Computer=RegEx("bw-win-sc01{3}.bwren.lab") | bw-win-sc0111.bwren.lab |
| a{n,} |  この文字が *n* 回以上出現することを表します。 | Computer=RegEx("bw-win-sc01{3,}.bwren.lab") | bw-win-sc0111.bwren.lab<br>bw-win-sc01111.bwren.lab<br>bw-win-sc011111.bwren.lab<br>bw-win-sc0111111.bwren.lab |
| a{n,m} |  この文字が *n* ～ *m* 回出現することを表します。 | Computer=RegEx("bw-win-sc01{3,5}.bwren.lab") | bw-win-sc0111.bwren.lab<br>bw-win-sc01111.bwren.lab<br>bw-win-sc011111.bwren.lab |


## <a name="logical-expressions"></a>論理式
複数の値の中から選択します。

| Character | Description | 例 | 一致の例 |
|:--|:--|:--|:--|
| &#124; | 論理 OR。  いずれかの式で一致する場合に結果を返します。 | Type=Alert AlertSeverity=RegEx("Warning&#124;Error") | 警告<br>エラー |
| & | 論理 AND。  両方の式で一致する場合に結果を返します。 | EventData=regex("(Security.\*&.\*success.\*)") | Security auditing successful |


## <a name="literals"></a>リテラル
特殊文字をリテラル文字に変換します。  これには、?-\*^\[\]{}\(\)+\|.& などの正規表現に機能を提供する文字が含まれます。

| Character | Description | 例 | 一致の例 |
|:--|:--|:--|:--|
| \\ | 特殊文字をリテラルに変換します。 | Status_CF=\\[Error\\]@<br>Status_CF=Error\\-@ | [Error]File not found.<br>Error-File not found. |


## <a name="next-steps"></a>次のステップ

* [ログ検索](log-analytics-log-searches.md)を使用して、Log Analytics リポジトリ内のデータを表示および分析します。

