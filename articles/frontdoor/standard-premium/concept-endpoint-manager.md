---
title: 'Azure Front Door: エンドポイント マネージャー'
description: この記事では、Azure Front Door エンドポイント マネージャーの概要を示します。
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: c916be9a54d62e16f488c94f4fa88a2207fb8788
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101098046"
---
# <a name="what-is-azure-front-door-standardpremium-preview-endpoint-manager"></a>Azure Front Door Standard/Premium (プレビュー) エンドポイント マネージャーとは

> [!NOTE]
> * このドキュメントは、Azure Front Door Standard/Premium (プレビュー) を対象としています。 Azure Front Door については、 [Azure Front Door ドキュメント](../front-door-overview.md)を参照してください。

エンドポイント マネージャーは、Azure Front Door 用に構成したエンドポイントの概要を提供します。 エンドポイントとは、ドメインとそれに関連付けられている構成の論理的なグループです。 エンドポイント マネージャーを使用すると、CRUD (作成、読み取り、更新、および削除) 操作のエンドポイントのコレクションを管理できます。 次のエンドポイントの要素は、エンドポイント マネージャーを使用して管理できます。

* ドメイン
* 配信元グループ
* ルート
* セキュリティ

:::image type="content" source="../media/concept-endpoint-manager/endpoint-manager-1.png" alt-text="構成されていないエンドポイント マネージャーのスクリーンショット。" lightbox="../media/concept-endpoint-manager/endpoint-manager-1-expanded.png":::

エンドポイント マネージャーには、エンドポイント内に作成された各要素のインスタンスの数が一覧表示されます。 各要素の関連付けの状態も表示されます。 たとえば、複数のドメインと配信元グループを作成し、それらの間の関連付けを別のルートに割り当てることができます。

> [!IMPORTANT]
> * Azure Front Door Standard/Premium (プレビュー) は現在、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳細については、「[**Microsoft Azure プレビューの追加使用条件**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="linked-view"></a>リンク ビュー

エンドポイント マネージャー内のリンク ビューを使用すると、次のような Azure Front Door 要素間の関連付けを簡単に識別できます。

* 現在のエンドポイントに関連付けられているドメインは何か
* どの配信元グループがどのドメインに関連付けられているのか
* どの WAF ポリシーがどのドメインに関連付けられているのか

:::image type="content" source="../media/concept-endpoint-manager/endpoint-manager-2.png" alt-text="構成されたエンドポイント マネージャーのスクリーンショット。" lightbox="../media/concept-endpoint-manager/endpoint-manager-2-expanded.png":::

## <a name="next-steps"></a>次の手順

[Front Door Standard/Premium を作成する](create-front-door-portal.md)方法について学習します。
