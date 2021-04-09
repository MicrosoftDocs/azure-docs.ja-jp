---
title: 'Azure Front Door: ルール セット'
description: この記事では、Azure Front Door Standard または Premium のルール セット機能の概要を示します。
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 8e6ceebc9e92dabe66baeb9aeff0ae9692e2bdad
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2021
ms.locfileid: "101097995"
---
# <a name="what-is-a-rule-set-for-azure-front-door-standardpremium-preview"></a>Azure Front Door Standard または Premium (プレビュー) のルール セットとは

> [!Note]
> このドキュメントは、Azure Front Door Standard/Premium (プレビュー) を対象としています。 Azure Front Door については、 [こちら](../front-door-overview.md)を参照してください。

ルール セットとは、カスタマイズされたルール エンジンであり、複数のルールの組み合わせを 1 つのセットにまとめたものです。これを複数のルートに関連付けることができます。 ルール セットを使用すると、エッジで要求が処理される方法と、Azure Front Door でそれらの要求を処理する方法をカスタマイズできます。

> [!IMPORTANT]
> Azure Front Door Standard/Premium (プレビュー) は現在、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="common-supported-scenarios"></a>サポートされている一般的なシナリオ

* HTTP Strict-Transport-Security (HSTS)、X-XSS-Protection、Content-Security-Policy、X-Frame-Options など、ブラウザーベースの脆弱性を防ぐためのセキュリティ ヘッダー、およびクロスオリジン リソース共有 (CORS) シナリオのための Access-Control-Allow-Origin ヘッダーを実装します。 セキュリティベースの属性は、Cookie でも定義できます。

* クライアント デバイスの種類に基づいてアプリケーションのモバイルまたはデスクトップ バージョンに要求をルーティングします。

* リダイレクト機能を使用して 301、302、307、308 のリダイレクトをクライアントに返し、それらを新しいホスト名、パス、クエリ文字列、またはプロトコルに送信します。

* 受信要求に基づき、ルートのキャッシュ構成を動的に変更します。

* 要求 URL パスを書き換えて、構成されている配信元グループ内の適切な配信元に要求を転送します。

* 要求または応答ヘッダーを追加、変更、または削除して機密情報を非表示にしたり、ヘッダーを通じて重要な情報をキャプチャしたりします。

* 新しいページが読み込まれるときや、フォームがポストされるときなどに、要求または応答ヘッダーや URL 書き換えパスまたはクエリ文字列を動的に変更するためのサーバー変数をサポートします。 サーバー変数は、現在、 **[ルール セットのアクション](concept-rule-set-actions.md)** でのみサポートされています。

## <a name="architecture"></a>Architecture

ルール セットは、エッジで要求を処理します。 要求が Azure Front Door Standard または Premium のエンドポイントに到着すると、最初に WAF が実行され、次に、ルートに構成されている設定が続きます。 これらの設定には、ルートに関連付けられているルール セットが含まれます。 ルール セットは、ルート内で上から下に処理されます。 同じことが、ルール セット内のルールにも適用されます。 各ルール内のすべてのアクションが実行されるようにするには、ルール内のすべての一致条件が満たされる必要があります。 要求がルール セット構成内のどの条件にも一致しない場合は、ルート内の構成のみが実行されます。

**[Stop evaluating remaining rules]\(残りのルールの評価を停止する\)** がオンになっている場合、そのルートに関連付けられた残りのすべてのルール セットは実行されません。  

### <a name="example"></a>例

次の図では、WAF ポリシーが最初に実行されます。 ルール セットは、応答ヘッダーを追加するように構成されます。 そして、一致条件が満たされると、ヘッダーのキャッシュ制御の最長有効期間が変更されます。

:::image type="content" source="../media/concept-rule-set/front-door-rule-set-architecture-1.png" alt-text="ルール セットのアーキテクチャを示す図。" lightbox="../media/concept-rule-set/front-door-rule-set-architecture-1-expanded.png":::

## <a name="terminology"></a>用語

Azure Front Door のルール セットを使用すると、それぞれが一連のルールで構成されているルール セット構成の組み合わせを作成できます。 ルール セットを構成する際に役立ついくつかの用語を次に示します。

クォータ制限の詳細については、「[Azure サブスクリプションとサービスの制限、クォータ、制約](../../azure-resource-manager/management/azure-subscription-service-limits.md)」をご覧ください。

* "*ルール セット*": 1 つまたは複数の[ルート](concept-route.md)に関連付けられる一連のルール。 各構成は 25 のルールに制限されます。 最大 10 個の構成を作成できます。

* "*ルール セット ルール*": 最大 10 個の一致条件と 5 個のアクションで構成されるルール。 ルールは 1 つのルール セットに対してローカルであり、ルール セット間で使用するためにエクスポートすることはできません。 ユーザーは、複数のルール セット内に同じルールを作成できます。

* "*一致条件*": 受信要求を解析するために利用できる一致条件は多数あります。 1 つのルールに最大 10 個の一致条件を含めることができます。 一致条件は、**AND** 演算子で評価されます。 *条件では、正規表現がサポートされています*。 一致条件の完全な一覧については、[ルール セットのアクション](concept-rule-set-match-conditions.md)に関するページを参照してください。

* "*アクション*": アクションでは、一致条件に基づいてAFD で着信要求がどのように処理されるかが示されます。 キャッシュ動作の変更、要求ヘッダーや応答ヘッダーの変更、URL の書き換えおよび URL のリダイレクトを行うことができます。 *サーバー変数は、アクションでサポートされています*。 1 つのルールに最大 10 個の一致条件を含めることができます。 アクションの完全な一覧については、[ルール セットのアクション](concept-rule-set-actions.md)に関するページをご覧ください。

## <a name="next-steps"></a>次のステップ

* [Front Door Standard または Premium を作成する](create-front-door-portal.md)方法について説明します。
* 最初の[ルール セット](how-to-configure-rule-set.md)を構成する方法について説明します。
 
