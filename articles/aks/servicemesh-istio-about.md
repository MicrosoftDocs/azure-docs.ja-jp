---
title: Istio の概要
description: Istio の概要を知る
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 8518e30a54c2486abf84cd9ac026cc4dccb3fa84
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "77593902"
---
# <a name="istio"></a>Istio

## <a name="overview"></a>概要

[Istio][istio] は、すべての機能を備えた、カスタマイズ可能で拡張可能なサービス メッシュです。

## <a name="architecture"></a>Architecture

Istio では、[Envoy][envoy-proxy] ベースのサイドカーから成るデータ プレーンが提供されます。 これらのインテリジェント プロキシは、メッシュ化されたアプリとワークロードの内外のすべてのネットワーク トラフィックを制御します。

コントロール プレーンは、次の[コンポーネント][what-is-istio]を使用して、構成、ポリシー、およびテレメトリを管理します。

- **Mixer** - アクセス制御と使用ポリシーを適用します。 [Prometheus][prometheus] にプッシュされたプロキシからテレメトリを収集します。

- **Pilot** - プロキシのサービス検出やトラフィック管理のポリシー/構成を提供します。

- **Citadel** - サービス間の mTLS を可能にする ID とセキュリティの機能を提供します。

- **Galley** - コンポーネントに対する構成の抽象化と提供を行います。

次のアーキテクチャの図は、データ プレーンとコントロール プレーン内のさまざまなコンポーネントがどのように対話するかを示しています。


![Istio コンポーネントとアーキテクチャの概要。](media/servicemesh/istio/about-architecture.png)


## <a name="selection-criteria"></a>選択条件

ワークロードに対して Istio を評価するときは、次の点を理解して考慮することが重要です。

- [設計目標](#design-goals)
- [Capabilities](#capabilities)
- [シナリオ](#scenarios)


### <a name="design-goals"></a>設計目標

次の設計目標は、Istio プロジェクトの[指針][design-goals]となります。

- **透明性の最大化** - 最小限の作業の導入でシステムから真の価値を引き出すことができます。

- **拡張性** - ニーズの変化に合わせて拡張および適応できる必要があります。

- **移植性** - クラウド、オンプレミスなど、さまざまな種類の環境で容易に実行できます。

- **ポリシーの統一性** - さまざまなリソースにわたるポリシー定義の一貫性。


### <a name="capabilities"></a>機能

Istio には、次の一連の機能が用意されています。

- **メッシュ** – ゲートウェイ (マルチクラスター)、仮想マシン (メッシュの拡張)

- **トラフィック管理** – ルーティング、分割、タイムアウト、サーキット ブレーカー、再試行、イングレス、エグレス

- **ポリシー** – アクセス制御、レート制限、クォータ、カスタム ポリシー アダプター

- **セキュリティ** – 認証 (jwt)、承認、暗号化 (mTLS)、外部 CA (HashiCorp Vault)

- **監視** – ゴールデン メトリック、ミラー、トレース、カスタム アダプター、Prometheus、Grafana

### <a name="scenarios"></a>シナリオ

Istio は、次のシナリオに適しており、推奨されています。

- 拡張性と豊富な機能セットが必要

- VM ベースのワークロードを含むメッシュ拡張

- マルチクラスター サービス メッシュ

## <a name="next-steps"></a>次のステップ

次のドキュメントでは、Azure Kubernetes Service (AKS) に Istio をインストールする方法について説明しています。

> [!div class="nextstepaction"]
> [Azure Kubernetes Service (AKS) に Istio をインストールする][istio-install]

さらに、Istio の概念と追加のデプロイ モデルについて調べることもできます。

- [Istio の概念][what-is-istio]
- [Istio のデプロイ モデル][deployment-models]

<!-- LINKS - external -->
[istio]: https://istio.io
[what-is-istio]: https://istio.io/docs/concepts/what-is-istio/
[design-goals]: https://istio.io/docs/concepts/what-is-istio/#design-goals
[deployment-models]: https://istio.io/docs/concepts/deployment-models/

[envoy-proxy]: https://www.envoyproxy.io/
[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/

<!-- LINKS - internal -->
[istio-install]: ./servicemesh-istio-install.md
