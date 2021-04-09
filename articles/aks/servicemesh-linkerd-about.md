---
title: Linkerd の概要
description: Linkerd の概要を取得する
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 3181be62a14ec1b3450bd181172b5323ca176427
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "77593769"
---
# <a name="linkerd"></a>Linkerd

## <a name="overview"></a>概要

[Linkerd][linkerd] は、使いやすく軽量のサービス メッシュです。

## <a name="architecture"></a>Architecture

Linkerd は、超軽量の [Linkerd ][linkerd-proxy] に特化したプロキシ サイドカーで構成されるデータ プレーンを提供します。 これらのインテリジェント プロキシは、メッシュ化されたアプリとワークロードの内外のすべてのネットワーク トラフィックを制御します。 また、プロキシは [Prometheus ][prometheus] メトリック エンドポイントを介してメトリックを公開します。

コントロール プレーンは、次の[コンポーネント][linkerd-architecture]を使用して、構成と集計されたテレメトリを管理します。

- **コントローラー** - Linkerd CLI とダッシュボードを駆動する API を提供します。 プロキシの構成を提供します。

- **タップ** - 要求と応答に関するリアルタイム監視を確立します。

- **ID** - サービス間の mTLS を可能にする ID とセキュリティの機能を提供します。

- **Web** - Linkerd ダッシュボードを提供します。


次のアーキテクチャの図は、データ プレーンとコントロール プレーン内のさまざまなコンポーネントがどのように対話するかを示しています。


![Linkerd コンポーネントとアーキテクチャの概要。](media/servicemesh/linkerd/about-architecture.png)


## <a name="selection-criteria"></a>選択条件

ワークロードに対して Linkerd を評価するときは、次の点を理解して考慮することが重要です。

- [設計原則](#design-principles)
- [Capabilities](#capabilities)
- [シナリオ](#scenarios)


### <a name="design-principles"></a>設計原則

次の設計原則は、Linkerd プロジェクトの[指針][design-principles]となります。

- **シンプルにする** - 使いやすく理解しやすいものにする必要があります。

- **リソース要件を最小限に抑える** - パフォーマンスとリソース コストを最小限に抑えます。

- **ただ動作するだけ** - 既存のアプリケーションを中断させず、複雑な構成を必要としないようにします。


### <a name="capabilities"></a>機能

Linkerd には、次の一連の機能が用意されています。

- **メッシュ** – 組み込みのデバッグ オプション

- **トラフィック管理** – 分割、タイムアウト、再試行、イングレス

- **セキュリティ** -暗号化 (mTLS)、24 時間ごとにオートローテーションされる証明書

- **可観測性**  – ゴールデン メトリック、タップ、トレース、サービス プロファイルとルートごとのメトリック、トポロジ グラフを含む Web ダッシュボード、prometheus、grafana


### <a name="scenarios"></a>シナリオ

Linkerd は、次のシナリオに適しており、推奨されています。

- 必要不可欠な一連の機能要件だけで簡単に使用できる

- 低待機時間、低オーバーヘッド、可観測性に重点を置いたシンプルなトラフィック管理


## <a name="next-steps"></a>次のステップ

次のドキュメントでは、Azure Kubernetes Service (AKS) に Linkerd をインストールする方法について説明しています。

> [!div class="nextstepaction"]
> [Azure Kubernetes Service (AKS) で Linkerd をインストールする][linkerd-install]

さらに、Linkerd の機能とアーキテクチャについても詳しく調べることができます。

- [Linkerd の機能][linkerd-features]
- [Linkerd アーキテクチャ][linkerd-architecture]

<!-- LINKS - external -->
[linkerd]: https://linkerd.io/2/overview/
[linkerd-architecture]: https://linkerd.io/2/reference/architecture/
[linkerd-features]: https://linkerd.io/2/features/
[design-principles]: https://linkerd.io/2/design-principles/
[linkerd-proxy]: https://github.com/linkerd/linkerd2-proxy

[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/

<!-- LINKS - internal -->
[linkerd-install]: ./servicemesh-linkerd-install.md