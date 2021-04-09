---
title: Azure Front Door Standard/Premium (プレビュー) でのファイル圧縮によるパフォーマンスの向上
description: Azure Front Door でファイルを圧縮して、ファイル転送速度とページ読み込みのパフォーマンスを向上させる方法を説明します。
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: article
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 4b526d82465862b1c0d27aed6443c6d7199bfb5b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101097908"
---
# <a name="improve-performance-by-compressing-files-in-azure-front-door-standardpremium-preview"></a>Azure Front Door Standard/Premium (プレビュー) でのファイル圧縮によるパフォーマンスの向上

> [!Note]
> このドキュメントは、Azure Front Door Standard/Premium (プレビュー) を対象としています。 Azure Front Door については、 [こちら](../front-door-overview.md)を参照してください。

ファイル圧縮は、ファイル転送速度を改善し、ページ読み込みパフォーマンスを向上させる有効な方法です。 圧縮によって、サーバーによって送信される前に、ファイルのサイズが縮小されます。 ファイル圧縮により、帯域幅のコストを軽減し、ユーザー エクスペリエンスを向上させることができます。

> [!IMPORTANT]
> Azure Front Door Standard/Premium (プレビュー) は現在、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。
ファイル圧縮を有効にするには、次の 2 つの方法があります。

- 配信元サーバーで圧縮を有効にします。 Azure Front Door は圧縮ファイルを受け渡し、要求したクライアントに配信します。
- Azure Front Door POP サーバーで直接、圧縮を有効にします (*即時圧縮*)。 この場合、Azure Front Door によってファイルが圧縮され、エンド ユーザーに送信されます。

> [!IMPORTANT]
> Azure Front Door 構成の変更は、ネットワーク全体に反映されるまでに最大で 10 分かかります。 今回初めて CDN エンドポイントの圧縮を設定した場合は、トラブルシューティングの前に、圧縮設定が確実にすべての POP に反映されるように 1 ～ 2 時間待機することを検討してください。

## <a name="enabling-compression"></a>圧縮の有効化

> [!Note]
> Azure Front Door では、圧縮はルートの **[キャッシュを有効にする]** の一部です。 **[キャッシュを有効にする]** の場合にのみ、Azure Front Door で圧縮を利用できます。

圧縮は、次の方法で有効にできます。
* 簡易作成時 - キャッシュを有効にすると、圧縮を有効にできます。
* カスタム作成時 - ルートを追加するときにキャッシュと圧縮を有効にします。 
* エンドポイント マネージャーのルート。
* [最適化] ページ。

### <a name="enable-compression-in-endpoint-manager"></a>エンドポイント マネージャーで圧縮を有効にする

1. Azure Front Door の Standard/Premium プロファイル ページから、 **[エンドポイント マネージャー]** にアクセスして、圧縮を有効にするエンドポイントを選択します。

1. **[エンドポイントの編集]** を選択してから、圧縮を有効にする **ルート** を選択します。 

   :::image type="content" source="../media/how-to-compression/front-door-compression-endpoint-manager-1.png" alt-text="エンドポイント マネージャーのランディング ページのスクリーンショット。" lightbox="../media/how-to-compression/front-door-compression-endpoint-manager-1-expanded.png":::   

1. **[キャッシュを有効にする]** をオンにしてから、 **[圧縮を有効にする]** のチェックボックスをオンにします。

   :::image type="content" source="../media/how-to-compression/front-door-compression-endpoint-manager-2.png" alt-text="エンドポイント マネージャーで圧縮を有効にする。":::   

1. **[更新]** を選択して構成を保存します。

### <a name="enable-compression-in-optimization"></a>最適化時に圧縮を有効にする

1. Azure Front Door の Standard/Premium プロファイル ページで、[設定] の **[最適化]** に移動します。 エンドポイントを展開して、ルートの一覧を表示します。 

1. 圧縮が "*無効*" になっている **ルート** の横にある 3 つのドットを選択します。 次に、 **[Configure route]\(ルートの構成\)** を選択します。

   :::image type="content" source="../media/how-to-compression/front-door-compression-optimization-1.png" alt-text="[最適化] ページの [圧縮を有効にする] の画面。" lightbox="../media/how-to-compression/front-door-compression-optimization-1-expanded.png"::: 

1. **[キャッシュを有効にする]** をオンにしてから、 **[圧縮を有効にする]** のチェックボックスをオンにします。

     :::image type="content" source="../media/how-to-compression/front-door-compression-endpoint-manager-2.png" alt-text="エンドポイント マネージャーで圧縮を有効にする画面のスクリーンショット。"::: 

1. **[Update]** をクリックします。

## <a name="modify-compression-content-type"></a>圧縮コンテンツの種類を変更する

[最適化] ページで、MIME の種類の既定の一覧を変更できます。

1. Azure Front Door の Standard/Premium プロファイル ページで、[設定] の **[最適化]** に移動します。 次に、圧縮が "*有効*" になっている **ルート** を選択します。

1. 圧縮が "*有効*" になっている **ルート** の横にある 3 つのドットを選択します。 次に、**View Compressed file types\(圧縮ファイルの種類の表示\)** を選択します。

   :::image type="content" source="../media/how-to-compression/front-door-compression-edit-content-type.png" alt-text="[最適化] ページのスクリーンショット。" lightbox="../media/how-to-compression/front-door-compression-edit-content-type-expanded.png"::: 

1. 既定の形式を削除するか、 **[追加]** を選択して新しいコンテンツの種類を追加します。

   :::image type="content" source="../media/how-to-compression/front-door-compression-edit-content-type-2.png" alt-text="[customize file compression]\(ファイル圧縮のカスタマイズ\) ページのスクリーンショット。"::: 

1. **[保存]** を選択して、圧縮の構成を更新します。

## <a name="disabling-compression"></a>圧縮の無効化

圧縮は、次の方法で無効にできます。
* エンドポイント マネージャー ルートで圧縮を無効にする。
* [最適化] ページで圧縮を無効にする。

### <a name="disable-compression-in-endpoint-manager"></a>エンドポイント マネージャーで圧縮を無効にする

1. Azure Front Door の Standard/Premium プロファイル ページで、[設定] の **[エンドポイント マネージャー]** に移動します。 圧縮を無効にするエンドポイントを選択します。

1. **[エンドポイントの編集]** を選択してから、圧縮を無効にする **ルート** を選択します。 **[圧縮を有効にする]** チェックボックスをオフにします。

1. **[更新]** を選択して構成を保存します。

### <a name="disable-compression-in-optimizations"></a>最適化で圧縮を無効にする

1. Azure Front Door の Standard/Premium プロファイル ページで、[設定] の **[最適化]** に移動します。 次に、圧縮が "*有効*" になっている **ルート** を選択します。

1. 圧縮が "*有効*" になっている **ルート** の横にある 3 つのドットを選択し、 *[Configure route]\(ルートの構成\)* を選択します。

    :::image type="content" source="../media/how-to-compression/front-door-disable-compression-optimization.png" alt-text="[最適化] ページで圧縮を無効する画面のスクリーンショット。"::: 

1. **[圧縮を有効にする]** チェックボックスをオフにします。

    :::image type="content" source="../media/how-to-compression/front-door-disable-compression-optimization-2.png" alt-text="圧縮を無効にするためのルート更新ページのスクリーンショット。"::: 

1. **[更新]** を選択して構成を保存します。

## <a name="compression-rules"></a>圧縮のルール

Azure Front Door では、対象となるファイルのみが圧縮されます。 圧縮を実行可能にするためのファイルの条件は、次のとおりです。
* MIME の種類である 
* 1 KB より大きい
* 8 MB 未満

これらのプロファイルでは、次の圧縮エンコードがサポートされています。
* gzip (GNU zip)
* brotli 

要求で複数の圧縮の種類がサポートされている場合、brotli 圧縮が優先されます。

アセットの要求で gzip 圧縮が指定され、要求がキャッシュ ミスになった場合、Azure Front Door は POP サーバー上で直接アセットの gzip 圧縮を行います。 その後、圧縮ファイルがキャッシュから提供されます。

配信元がチャンク転送エンコード (CTE) を使用して圧縮データを Azure Front Door POP に送信する場合、8 MB より大きい応答サイズはサポートされません。 

## <a name="next-steps"></a>次のステップ

- 最初の[ルール セット](how-to-configure-rule-set.md)を構成する方法について確認します
- [ルート セットの一致条件](concept-rule-set-match-conditions.md)の詳細を確認します
- [Azure Front Door のルール セット](concept-rule-set.md)の詳細を確認します
