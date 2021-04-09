---
title: Azure Front Door Standard/Premium (プレビュー) のキャッシュの消去
description: この記事は、Azure Front Door Standard/Premium でキャッシュを消去する方法を理解するのに役立ちます。
services: frontdoor
author: duongau
manager: KumudD
ms.service: frontdoor
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: aacbf2ceab8580727b1885bf6533cd74a7c4e60a
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2021
ms.locfileid: "101097935"
---
# <a name="cache-purging-in-azure-front-door-standardpremium-preview"></a>Azure Front Door Standard/Premium (プレビュー) のキャッシュの消去

> [!Note]
> このドキュメントは、Azure Front Door Standard/Premium (プレビュー) を対象としています。 Azure Front Door については、 [こちら](../front-door-overview.md)を参照してください。

Azure Front Door Standard/Premium は、アセットの Time-to-Live (TTL) が期限切れになるまで、そのアセットをキャッシュします。 クライアントが TTL の期限が切れたアセットを要求すると、Azure Front Door 環境はアセットの更新された最新コピーを取得し、要求に対応して、更新されたキャッシュを格納します。

ベスト プラクティスは、ユーザーが常にアセットの最新コピーを取得するようにすることです。 これを行うには、更新ごとにアセットにバージョンを付け、新しい URL として発行します。 Azure Front Door Standard/Premium では、次のクライアント要求のための新しいアセットが直ちに取得されます。 必要に応じて、すべてのエッジ ノードのキャッシュされたコンテンツを消去し、すべてのエッジ ノードが新しい更新されたアセットを取得するように強制することもできます。 キャッシュされたコンテンツを消去する理由としては、アプリケーションに対して新しい更新を行ったか、正しくない情報を含んでいるアセットを更新する場合が挙げられます。

> [!IMPORTANT]
> Azure Front Door Standard/Premium (プレビュー) は現在、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="prerequisites"></a>前提条件

[Azure Front Door キャッシュ](concept-caching.md)を確認して、キャッシュのしくみを理解します。

## <a name="configure-cache-purge"></a>キャッシュの消去を構成する

1. 消去するアセットを含む Azure Front Door プロファイルの概要ページに移動し、 **[キャッシュの消去]** を選択します。

   :::image type="content" source="../media/how-to-cache-purge/front-door-cache-purge-1.png" alt-text="[概要] ページのキャッシュ消去のスクリーンショット。":::

1. エッジ ノードから消去するエンドポイントとドメインを選択します。 *(複数のドメインを選択できます)*

   :::image type="content" source="../media/how-to-cache-purge/front-door-cache-purge-2.png" alt-text="[キャッシュの消去] ページのスクリーンショット。":::

1. すべてのアセットを消去するには、 **[Purge all assets for the selected domains]\(選択したドメインのすべてのアセットを消去\)** を選択します。 それ以外の場合は、 **[パス]** で、消去する各アセットのパスを入力します。

消去するパスの一覧では、次の形式がサポートされています。

* **1 つのパスの消去**: (プロトコルとドメインを除く) アセットの完全なパスをファイル拡張子と共に指定して、個々のアセットを消去します (例: /pictures/strasbourg.png)。
* **ルート ドメインの消去**: パスに "/*" を付けてエンドポイントのルートを削除します。

Azure Front Door Standard/Preium のキャッシュの消去は、大文字と小文字が区別されません。 また、クエリ文字列に依存しません。つまり、URL を消去すると、そのすべてのクエリ文字列バリエーションが消去されます。 

## <a name="next-steps"></a>次のステップ

[Front Door Standard/Premium を作成する](create-front-door-portal.md)方法について学習します。
