---
title: クロス オリジン リソース共有を利用した Azure Front Door Standard/Premium の使用
description: クロス オリジン リソース共有 (CORS) を利用して Azure Front Door (AFD) を使用する方法について説明します。
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: ee8f19aca62d2e331fcf59551d47c2dac93783b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101097857"
---
# <a name="using-azure-front-door-standardpremium-with-cross-origin-resource-sharing-cors"></a>クロス オリジン リソース共有 (CORS) を利用した Azure Front Door Standard/Premium の使用

> [!Note]
> このドキュメントは、Azure Front Door Standard/Premium (プレビュー) を対象としています。 Azure Front Door については、 [こちら](../front-door-overview.md)を参照してください。

## <a name="what-is-cors"></a>CORS とは

CORS (クロス オリジン リソース共有) は、あるドメインで実行されている Web アプリケーションが別のドメイン内にあるリソースにアクセスできるようにする HTTP 機能です。 クロスサイト スクリプティング攻撃の可能性を低減させるために、すべての最新の Web ブラウザーには[同一オリジン ポリシー](https://www.w3.org/Security/wiki/Same_Origin_Policy)と呼ばれるセキュリティ制限が実装されています。 これにより、Web ページは他のドメイン内の API を呼び出すことができません。  CORS を使用すれば、あるオリジン (オリジン ドメイン) から他のオリジン内の API を安全に呼び出すことができます。

> [!IMPORTANT]
> Azure Front Door Standard/Premium (プレビュー) は現在、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="how-it-works"></a>しくみ

CORS 要求には、"*簡単な要求*" と "*複雑な要求*" の 2 種類があります。

### <a name="for-simple-requests"></a>単純な要求の場合:

1. ブラウザーが、別の **Origin** HTTP 要求ヘッダーを含む CORS 要求を送信します。 このヘッダーの値は、親ページを提供したオリジンで、"*プロトコル"、"* *ドメイン*"、および "*ポート*" の組み合わせとして定義されます。  https\://www.contoso.com からのページが fabrikam.com オリジンのユーザーのデータにアクセスしようとすると、次の要求ヘッダーが fabrikam.com に送信されます。

   `Origin: https://www.contoso.com`

2. サーバーからは次のいずれかの応答が返される場合があります。

   * 許可されるオリジン サイトを示す、応答の **Access-Control-Allow-Origin** ヘッダー。 次に例を示します。

     `Access-Control-Allow-Origin: https://www.contoso.com`

   * 403 などの HTTP エラー コード (Origin ヘッダーの確認後、サーバーによってクロス オリジン要求が許可されなかった場合)

   * すべてのオリジンを許可するワイルドカードが含まれる **Access-Control-Allow-Origin** ヘッダー。

     `Access-Control-Allow-Origin: *`

### <a name="for-complex-requests"></a>複雑な要求:

複雑な要求は CORS 要求です。この要求で、ブラウザーは実際の CORS 要求を送信する前に、"*プレフライト要求*" (準備プローブ) を送信します。 プレフライト要求は、サーバーのアクセス許可に対して、元の CORS 要求が処理を続行できるかどうかと、その要求が、同じ URL への `OPTIONS` 要求かどうかをたずねます。

> [!TIP]
> CORS フローおよびよくある落とし穴の詳細については、[CORS for REST API ガイド](https://www.moesif.com/blog/technical/cors/Authoritative-Guide-to-CORS-Cross-Origin-Resource-Sharing-for-REST-APIs/)を参照してください。
>
>

## <a name="wildcard-or-single-origin-scenarios"></a>ワイルドカードまたは単一のオリジンのシナリオ
Azure Front Door の CORS は、**Access-Control-Allow-Origin** ヘッダーがワイルドカード (*) または単一のオリジンに設定されている場合、そのままの構成で自動的に動作します。  最初の応答が Azure Front Door によってキャッシュされ、要求では同じヘッダーが使用されます。

オリジンに CORS が設定される前に Azure Front Door に対し要求が行われている場合、エンドポイント コンテンツのコンテンツを消去して、**Access-Control-Allow-Origin** ヘッダーと共にそのコンテンツを再度読み込む必要があります。

## <a name="multiple-origin-scenarios"></a>複数のオリジンのシナリオ
複数のオリジンを CORS で許可する必要がある場合は、若干複雑になります。 最初の CORS オリジンの **Access-Control-Allow-Origin** ヘッダーが CDN にキャッシュされるときに問題が発生します。  異なる CORS オリジンが別の要求を行った場合、CDN によってキャッシュされた **Access-Control-Allow-Origin** ヘッダーが提供されても、オリジンが一致しません。 この問題を修正するにはいくつかの方法があります。

### <a name="azure-front-door-rule-set"></a>Azure Front Door ルール セット

Azure Front Door では、Azure Front Door [ルール セット](concept-rule-set.md)でルールを作成して、要求の **Origin** ヘッダーを確認することができます。 オリジンが有効である場合、ルールによって、正しい値が **Access-Control-Allow-Origin** ヘッダーに設定されます。 この場合は、ファイルの配信元サーバーからの **Access-Control-Allow-Origin** ヘッダーは無視され、AFD のルール エンジンが、許可される CORS オリジンを完全に管理します。

:::image type="content" source="../media/troubleshooting-cross-origin-resource-sharing/cross-origin-resource.png" alt-text="ルール セットを使用したルールの例のスクリーンショット。":::

> [!TIP]
> 追加のアクションをルールに追加して、**Access-Control-Allow-Methods** などの追加の応答ヘッダーを変更できます。
> 

## <a name="next-steps"></a>次のステップ

* [フロント ドアの作成](create-front-door-portal.md)方法について学習します。
