---
title: Azure CDN ルール エンジンのリファレンス | Microsoft Docs
description: Azure CDN ルール エンジンの一致条件と機能に関するリファレンス ドキュメント。
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: aab93204c850223756f28a56ea550f912e28e0d2
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/23/2019
ms.locfileid: "69996768"
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
% | パーセント記号の意味は、その使用法によって異なります。<br/><br/> `%{HTTPVariable}`:この構文では、HTTP 変数を識別します。<br/>`%{HTTPVariable%Pattern}`:この構文では、区切り記号として、また HTTP 変数を識別するために、パーセント記号を使用します。<br />`\%`:パーセント記号をエスケープすると、リテラル値として使用したり、URL エンコードを示したりすることができます (例: `\%20`)。
\* | アスタリスクでは、直前の文字を 0 回以上一致すことができます。
スペース | 空白文字は一般的にリテラル文字として扱われます。
'値' | 一重引用符は、リテラル文字として扱われます。 一重引用符のセットには、特別な意味はありません。

正規表現をサポートする一致条件と機能は、Perl Compatible Regular Expressions (PCRE) で定義されているパターンを受け入れます。

## <a name="next-steps"></a>次の手順

- [ルール エンジンの一致条件](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [ルール エンジンの条件式](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [ルール エンジンの機能](cdn-verizon-premium-rules-engine-reference-features.md)
- [ルール エンジンを使用して HTTP 動作をオーバーライドする](cdn-verizon-premium-rules-engine.md)
- [Azure CDN の概要](cdn-overview.md)
