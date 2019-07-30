---
title: Azure CDN ルール エンジンのリファレンス | Microsoft Docs
description: Azure CDN ルール エンジンの一致条件と機能に関するリファレンス ドキュメント。
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: 5fc611af75a7f733576f9343a4375fb56cacc030
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593160"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-reference"></a>Azure CDN from Verizon Premium ルール エンジンのリファレンス

この記事では、Azure Content Delivery Network (CDN) [ルール エンジン](cdn-verizon-premium-rules-engine.md)で使用できる一致条件と機能について詳しく説明します。

ルール エンジンは、特定の種類の要求が CDN によって処理される方法について、最終的な決定を下すように設計されています。

**一般的な使用法**:

- カスタムのキャッシュ ポリシーをオーバーライドまたは定義します。
- 機密性の高いコンテンツに対する要求をセキュリティで保護するか拒否します。
- 要求をリダイレクトします。
- カスタム ログ データを保存します。

## <a name="terminology"></a>用語集

ルールは、[**条件式**](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)、[**一致条件**](cdn-verizon-premium-rules-engine-reference-match-conditions.md)、および[**機能**](cdn-verizon-premium-rules-engine-reference-features.md)を使用して定義されます。 次の図では、これらの要素が強調表示されています。

 ![CDN の一致条件](./media/cdn-rules-engine-reference/cdn-rules-engine-terminology.png)

## <a name="syntax"></a>構文

特殊文字が扱われる方法は、一致条件または機能でテキスト値を処理する方法によって異なります。 一致条件または機能は、次のいずれかのようにテキストを解釈する場合があります。

1. [**リテラル値**](#literal-values)
2. [**ワイルドカード値**](#wildcard-values)
3. [**正規表現**](#regular-expressions)

### <a name="literal-values"></a>リテラル値

リテラル値として解釈されるテキストでは、"%" 記号を除くすべての特殊文字が、一致する必要のある値の一部として扱われます。 つまり、`\'*'\` に設定されたリテラルの一致条件は、完全に一致する値 (つまり、`\'*'\`) が見つかった場合にのみ満たされます。

パーセント記号は、URL エンコードを示すために使用されます (例: `%20`)。

### <a name="wildcard-values"></a>ワイルドカード値

ワイルドカード値として解釈されるテキストでは、特殊文字に付加的な意味が割り当てられます。 次の表は、以下の一連の文字がどのように解釈されるのかを示しています。

Character | 説明
----------|------------
\ | バックスラッシュは、このテーブルで指定されているすべて文字からのエスケープに使用されます。 エスケープする特殊文字の直前にバックスラッシュを指定する必要があります。<br/>たとえば、次の構文では、アスタリスクをエスケープします。`\*`
% | パーセント記号は、URL エンコードを示すために使用されます (例: `%20`)。
\* | アスタリスクは、1 つまたは複数の文字を表すワイルドカードです。
スペース | 空白文字は、指定した値またはパターンのいずれかで一致条件を満たすことができることを示します。
'値' | 一重引用符には、特別な意味はありません。 ただし、一連の一重引用符することで、値はリテラル値として扱われることを示します。 次のように使用できます。<br><br/>- 指定した値が比較対象値の任意の部分と一致するたびに、一致条件を満たすようにします。  たとえば、`'ma'` は、次の文字列のいずれかと一致します。 <br/><br/>/business/**ma**rathon/asset.htm<br/>**ma**p.gif<br/>/business/template.**ma**p<br /><br />- 特殊文字をリテラル文字として指定できます。 たとえば、空白文字を一重引用符で囲むことで、リテラルの空白文字を指定できます (つまり、`' '` または `'sample value'`)。<br/>- 空白の値を指定できます。 一重引用符のセット ('') を指定することで、空白の値を指定します。<br /><br/>**重要:**<br/>- 指定した値にワイルドカードが含まれていない場合は、自動的にリテラル値と見なされるので、一重引用符のセットを指定する必要はありません。<br/>- バックスラッシュがこの表の別の文字をエスケープしない場合は、一重引用符のセット内に指定すると無視されます。<br/>- 特殊文字をリテラル文字として指定するもう 1 つの方法は、バックスラッシュ (`\`) を使用してエスケープすることです。

### <a name="regular-expressions"></a>正規表現

正規表現では、テキスト値内で検索するパターンを定義します。 正規表現の表記は、さまざまなシンボルに特定の意味を定義します。 次のテーブルでは、特殊文字が一致条件や正規表現をサポートする機能によってどのように扱われるかが示されます。

特殊文字 | 説明
------------------|------------
\ | バックスラッシュは、その後の文字をエスケープします。これにより、その文字は、正規表現の意味としてではなく、リテラル値として扱われます。 たとえば、次の構文では、アスタリスクをエスケープします。`\*`
% | The meaning of a percentage symbol depends on its usage.<br/><br/> `%{HTTPVariable}`: This syntax identifies an HTTP variable.<br/>`%{HTTPVariable%Pattern}`: This syntax uses a percentage symbol to identify an HTTP variable and as a delimiter.<br />`\%`: Escaping a percentage symbol allows it to be used as a literal value or to indicate URL encoding (for example, `\%20`).
\* | An asterisk allows the preceding character to be matched zero or more times.
Space | A space character is typically treated as a literal character.
'value' | Single quotes are treated as literal characters. A set of single quotes does not have special meaning.

## Next steps

- [Rules engine match conditions](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Rules engine conditional expressions](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Rules engine features](cdn-verizon-premium-rules-engine-reference-features.md)
- [Override HTTP behavior using the rules engine](cdn-verizon-premium-rules-engine.md)
- [Azure CDN overview](cdn-overview.mdパーセント記号の意味は、その使用法によって異なります。 Docs
descr`%{HTTPVariable}`:この構文では、HTTP 変数を識別します。match`%{HTTPVariable%Pattern}`:この構文では、区切り記号として、また HTTP 変数を識別するために、パーセント記号を使用します。1/2019パーセント記号をエスケープすると、リテラル値として使用したり、URL エンコードを示したりすることができます (例: 20`)。`\``: Escaping a percentage symbol allows it to be used as a literal value or to indicate URL encoding (for example, `\`f the available match conditions and features for the Azure Content Delivery Network (CDN) [rules engine](cdn-verizon-premium-rules-engine.md).

The rules engine is designed to be the final authority on how specific types of requests are processed by the CDN.

**Common uses**:

- Override or define a custom cache policy.
- Secure or deny requests for sensitive content.
- Redirect requests.
- Store custom log data.

## Terminology

A rule is defined through the use of [**conditional expressions**](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md), [**match conditions**](cdn-verizon-premium-rules-engine-reference-match-conditions.md), and [**features**](cdn-verizon-premium-rules-engine-reference-features.md). These elements are highlighted in the following illustration:

 ![CDN match condition](./media/cdn-rules-engine-reference/cdn-rules-engine-terminology.png)

## Syntax

The manner in which special characters are treated varies according to how a match condition or feature handles text values. A match condition or feature may interpret text in one of the following ways:

1. [**Literal values**](#literal-values)
2. [**Wildcard values**](#wildcard-values)
3. [**Regular expressions**](#regular-expressions)

### Literal values

Text that is interpreted as a literal value treats all special characters, with the exception of the % symbol, as a part of the value that must be matched. In other words, a literal match condition set to `\'*'\` is only satisfied when that exact value (that is, `\'*'\`) is found.

A percentage symbol is used to indicate URL encoding (for example, `%20`).

### Wildcard values

Text that is interpreted as a wildcard value assigns additional meaning to special characters. The following table describes how the following set of characters is interpreted:

Character | Description
----------|------------
\ | A backslash is used to escape any of the characters specified in this table. A backslash must be specified directly before the special character that should be escaped.<br/>For example, the following syntax escapes an asterisk: `\*`
% | A percentage symbol is used to indicate URL encoding (for example, `%20`).
\* | An asterisk is a wildcard that represents one or more characters.
Space | A space character indicates that a match condition may be satisfied by either of the specified values or patterns.
'value' | A single quote does not have special meaning. However, a set of single quotes is used to indicate that a value should be treated as a literal value. It can be used in the following ways:<br><br/>- It allows a match condition to be satisfied whenever the specified value matches any portion of the comparison value.  For example, `'ma'` would match any of the following strings: <br/><br/>/business/**ma**rathon/asset.htm<br/>**ma**p.gif<br/>/business/template.**ma**p<br /><br />- It allows a special character to be specified as a literal character. For example, you may specify a literal space character by enclosing a space character within a set of single quotes (that is, `' '` or `'sample value'`).<br/>- It allows a blank value to be specified. Specify a blank value by specifying a set of single quotes (that is, '').<br /><br/>**Important:**<br/>- If the specified value does not contain a wildcard, then it is automatically considered a literal value, which means that it is not necessary to specify a set of single quotes.<br/>- If a backslash does not escape another character in this table, it is ignored when it is specified within a set of single quotes.<br/>- Another way to specify a special character as a literal character is to escape it using a backslash (that is, `\`).

### Regular expressions

Regular expressions define a pattern that is searched for within a text value. Regular expression notation defines specific meanings to a variety of symbols. The following table indicates how special characters are treated by match conditions and features that support regular expressions.

Special Character | Description
------------------|------------
\ | A backslash escapes the character the follows it, which causes that character to be treated as a literal value instead of taking on its regular expression meaning. For example, the following syntax escapes an asterisk: `\*`
% | The meaning of a percentage symbol depends on its usage.<br/><br/> `%{HTTPVariable}`: This syntax identifies an HTTP variable.<br/>`%{HTTPVariable%Pattern}`: This syntax uses a percentage symbol to identify an HTTP variable and as a delimiter.<br />`\%`: Escaping a percentage symbol allows it to be used as a literal value or to indicate URL encoding (for example, `\%20`).
\* | アスタリスクでは、直前の文字を 0 回以上一致すことができます。
スペース | 空白文字は一般的にリテラル文字として扱われます。
'値' | 一重引用符は、リテラル文字として扱われます。 一重引用符のセットには、特別な意味はありません。

## <a name="next-steps"></a>次の手順

- [ルール エンジンの一致条件](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [ルール エンジンの条件式](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [ルール エンジンの機能](cdn-verizon-premium-rules-engine-reference-features.md)
- [ルール エンジンを使用して HTTP 動作をオーバーライドする](cdn-verizon-premium-rules-engine.md)
- [Azure CDN の概要](cdn-overview.md)