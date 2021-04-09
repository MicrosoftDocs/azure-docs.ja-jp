---
title: Azure CDN の Standard ルール エンジン リファレンス | Microsoft Docs
description: Azure Content Delivery Network (Azure CDN) の Standard ルール エンジンの一致条件とアクションに関するリファレンス ドキュメント。
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 08/04/2020
ms.author: allensu
ms.openlocfilehash: 1a0f4456f38939632026645500dd48acbf7dbc88
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93242210"
---
# <a name="standard-rules-engine-reference-for-azure-cdn"></a>Azure CDN の Standard ルール エンジン リファレンス

Azure Content Delivery Network (Azure CDN) の [Standard ルール エンジン](cdn-standard-rules-engine.md)では、ルールは、1 つまたは複数の一致条件とアクションから構成されます。 この記事では、Azure CDN の Standard ルール エンジンで利用できる一致条件と機能について詳しく説明します。

ルール エンジンは、特定の種類の要求が Standard Azure CDN によって処理される方法について、最終的な決定を下すように設計されています。

**ルールの一般的な用途**:

- カスタムのキャッシュ ポリシーをオーバーライドまたは定義します。
- 要求をリダイレクトします。
- HTTP の要求ヘッダーや応答ヘッダーを変更します。

## <a name="terminology"></a>用語

ルール エンジンでルールを定義するには、[一致条件](cdn-standard-rules-engine-match-conditions.md)と[アクション](cdn-standard-rules-engine-actions.md)を設定します。

 ![Azure CDN ルールの構造](./media/cdn-standard-rules-engine-reference/cdn-rules-structure.png)

各ルールには、最大で 10 個の一致条件と 5 つのアクションを含めることができます。 各 Azure CDN エンドポイントには、最大 25 個のルールを含めることができます。 

この制限に含まれるのは、既定の *グローバル ルール* です。 グローバル ルールには一致条件がなく、グローバル ルールに定義されているアクションは常にトリガーされます。

   > [!IMPORTANT]
   > 複数のルールが一覧表示される順序は、ルールの処理方法に影響します。 あるルールで指定されているアクションは、後続のルールで上書きされる可能性があります。

## <a name="limits-and-pricing"></a>制限と価格 

各 Azure CDN エンドポイントには、最大 25 個のルールを含めることができます。 各ルールには、最大で 10 個の一致条件と 5 つのアクションを含めることができます。 ルール エンジンの価格は、次のディメンションに従います。 
- ルール: ルールごとに 1 か月あたり $1 
- 処理された要求数: 要求 100 万件あたり $0.60
- 最初の 5 つのルールは無料のまま

## <a name="syntax"></a>構文

ルールにおける特殊文字の扱いは、一致条件とアクションによるテキスト値の扱いの違いによって変わります。 一致条件またはアクションでは、次のいずれかのようにテキストが解釈されます。

- [リテラル値](#literal-values)
- [ワイルドカード値](#wildcard-values)


### <a name="literal-values"></a>リテラル値

リテラル値として解釈されるテキストでは、 *% 記号を除く* すべての特殊文字が、ルールで一致する必要のある値の一部として扱われます。 たとえば、`'*'` に設定されたリテラル一致条件は、正確な値 `'*'` が見つかった場合にのみ満たされます。

パーセント記号は、URL エンコードを示すために使用されます (例: `%20`)。

### <a name="wildcard-values"></a>ワイルドカード値

現在、Standard ルール エンジンの **UrlPath 一致条件** でワイルドカード文字がサポートされています。 \* 文字は、1 つまたは複数の文字を表すワイルドカードです。 

## <a name="next-steps"></a>次のステップ

- [Standard ルール エンジンの一致条件](cdn-standard-rules-engine-match-conditions.md)
- [Standard ルール エンジンのアクション](cdn-standard-rules-engine-actions.md)
- [Standard ルール エンジンを使用して HTTPS を適用する](cdn-standard-rules-engine.md)
- [Azure CDN の概要](cdn-overview.md)
