---
title: "Azure CDN ルール エンジンのリファレンス | Microsoft Docs"
description: "Azure CDN ルール エンジンの一致条件と機能に関するリファレンス ドキュメント。"
services: cdn
documentationcenter: 
author: Lichard
manager: akucer
editor: 
ms.assetid: 669ef140-a6dd-4b62-9b9d-3f375a14215e
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: rli
translationtype: Human Translation
ms.sourcegitcommit: dccb945e170bd3e3f23283359db25e574a2d4296
ms.openlocfilehash: c10145661a8c575381493c9aaa901c3ef92c2e81


---
# <a name="azure-cdn-rules-engine"></a>Azure CDN ルール エンジン
このトピックでは、Azure Content Delivery Network (CDN) [ルール エンジン](cdn-rules-engine.md)で利用できる一致条件と機能について詳しく説明します。

HTTP ルール エンジンは、特定のタイプの要求が CDN によって処理される方法について、最終的な決定を下すように設計されています。

**一般的な使用法**:

- カスタムのキャッシュ ポリシーをオーバーライドまたは定義します。
- 機密性の高いコンテンツに対する要求をセキュリティで保護するか拒否します。
- 要求をリダイレクトします。
- カスタム ログ データを保存します。

## <a name="terminology"></a>用語集
ルールは、[**条件式**](cdn-rules-engine-reference-conditional-expressions.md)、 [**一致**](cdn-rules-engine-reference-match-conditions.md)、および[**機能**](cdn-rules-engine-reference-features.md)を使用することで定義されます。 これらの要素は、次の図で強調表示されます。

 ![CDN の一致条件](./media/cdn-rules-engine-reference/cdn-rules-engine-terminology.png)

## <a name="syntax"></a>構文

特殊文字が扱われる方法は、一致条件または機能によるテキスト値の処理方法によって異なります。 一致条件または機能は、次のいずれかのようにテキストを解釈する場合があります。

1. [**リテラル値**](#literal-values) 
2. [**ワイルドカード値**](#wildcard-values)
3. [**正規表現**](#regular-expressions)

### <a name="literal-values"></a>リテラル値
リテラル値として解釈されるテキストでは、"%" 記号を除いたすべての特殊文字は、一致する必要がある値の一部として扱われます。 つまり、リテラルな一致条件で `\'*'\` と設定した場合、完全に一致した値 (つまり、 `\'*'\`) が見つかったときにのみ満たされます。
 
パーセント記号は、URL エンコードの指定に使用されます (例: `%20`)。

### <a name="wildcard-values"></a>ワイルドカード値
ワイルドカード値として解釈されるテキストでは、特殊文字に付加的な意味を割り当てます。 以下のテーブルでは、次の一連の文字の解釈方法について説明します。

Character | Description
----------|------------
\ | バックスラッシュは、このテーブルで指定されているすべて文字からのエスケープに使用されます。 エスケープする特殊文字の直前にバックスラッシュを指定する必要があります。<br/>たとえば、次の構文では、アスタリスクをエスケープします。`\*`
% | パーセント記号は、URL エンコードの指定に使用されます (例: `%20`)。
* | アスタリスクは、1 つまたは複数の文字を表すワイルドカードです。
スペース | 空白文字は、指定した値またはパターンのいずれかで一致条件を満たすことができることを示します。
'値' | 一重引用符には、特別な意味はありません。 ただし、一連の一重引用符することで、値はリテラル値として扱われることを示します。 次のように使用できます。<br><br/>- 指定した値が比較対象値の任意の部分と一致するたびに、一致条件を満たすようにします。  たとえば、`'ma'` は、次の文字列のいずれかと一致します。 <br/><br/>/business/**ma**rathon/asset.htm<br/>**ma**p.gif<br/>/business/template.**ma**p<br /><br />- 特殊文字をリテラル文字として指定できます。 たとえば、一重引用符のセットの中に空白文字を囲むことで、リテラルの空白文字を指定することがあります (つまり、`' '` または `'sample value'`)。<br/>- 空白の値を指定できます。 一重引用符のセット (つまり、'') を指定することで、空白の値を指定します。<br /><br/>**重要:**<br/>- 指定した値にワイルドカードが含まれていない場合は、自動的にリテラル値としてみなされます。 つまり、一重引用符のセットを指定する必要はありません。<br/>- バックスラッシュがこのテーブル内の別の文字をエスケープしない場合、一重引用符のセット内で指定することにより無視します。<br/>- 特殊文字をリテラル文字として指定する別の方法は、バックスラッシュ (つまり、 `\`) を使用してエスケープすることです。

### <a name="regular-expressions"></a>正規表現

正規表現では、テキスト値内で検索するパターンを定義します。 正規表現の表記は、さまざまなシンボルに特定の意味を定義します。 次のテーブルでは、特殊文字が一致条件や正規表現をサポートする機能によってどのように扱われるかが示されます。

特殊文字 | Description
------------------|------------
\ | バックスラッシュは、続く文字をエスケープします。 これにより、その文字は、正規表現の意味としてではなく、リテラル値として扱われます。 たとえば、次の構文では、アスタリスクをエスケープします。`\*`
% | パーセント記号の意味は、その使用法によって異なります。<br/><br/> `%{HTTPVariable}`: この構文では、HTTP 変数を識別します。<br/>`%{HTTPVariable%Pattern}`: この構文では、区切り記号として、また HTTP 変数を識別するために、パーセント記号を使用します。<br />`\%`: パーセント記号をエスケープすると、その記号をリテラル値として使用したり、URL エンコードを示したりする場合に使用できます (たとえば、`\%20`)。
* | アスタリスクでは、直前の文字を&0; 回以上一致すことができます。 
スペース | 空白文字は一般的にリテラル文字として扱われます。 
'値' | 一重引用符は、リテラル文字として扱われます。 一重引用符のセットには、特別な意味はありません。


## <a name="next-steps"></a>次のステップ
* [ルール エンジンの一致条件](cdn-rules-engine-reference-match-conditions.md)
* [ルール エンジンの条件式](cdn-rules-engine-reference-conditional-expressions.md)
* [ルール エンジンの機能](cdn-rules-engine-reference-features.md)
* [規則エンジンを使用した既定の HTTP 動作のオーバーライド](cdn-rules-engine.md)
* [Azure CDN の概要](cdn-overview.md)



<!--HONumber=Jan17_HO4-->


