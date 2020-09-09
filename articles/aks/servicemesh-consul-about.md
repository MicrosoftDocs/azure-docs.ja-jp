---
title: Consul の概要
description: Consul の概要を取得する
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 77cb6a693c5a73d0498b0acf9bc4ad8cc9f8f32f
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/21/2020
ms.locfileid: "83774004"
---
# <a name="consul"></a>Consul

## <a name="overview"></a>概要

[Consul][consul] は、ランタイム プラットフォーム全体にサービスを接続してセキュリティで保護するための、複数のデータ センターに対応したサービスのネットワーク ソリューションです。 [接続][consul-features]は、サービス メッシュ機能を提供するコンポーネントです。

## <a name="architecture"></a>Architecture

Consul では、既定では [Envoy][envoy-proxy] ベースの[サイドカー][consul-sidecar]から成るデータ プレーンが提供されます。 Consul には、プラグ可能なプロキシ アーキテクチャがあります。 これらのインテリジェント プロキシは、メッシュ化されたアプリとワークロードの内外のすべてのネットワーク トラフィックを制御します。

コントロール プレーンは、次の[コンポーネント][consul-architecture]を使用して、構成およびポリシーを管理します。

- **サーバー** - Consul クラスター状態を維持するサーバー モードで実行される Consul エージェント。

- **クライアント** - 簡易クライアント モードで実行されている Consul エージェント。 各コンピューティング ノードには、クライアント エージェントが実行されている必要があります。 このクライアントは、ワークロードと Consul 構成の間の構成とポリシーを仲介します。 

次のアーキテクチャの図は、データ プレーンとコントロール プレーン内のさまざまなコンポーネントがどのように対話するかを示しています。

![Consul コンポーネントとアーキテクチャの概要。](media/servicemesh/consul/about-architecture.png)


## <a name="selection-criteria"></a>選択条件

ワークロードに対して Consul を評価するときは、次の点を理解して考慮することが重要です。

- [Consul の原則](#consul-principles)
- [Capabilities](#capabilities)
- [シナリオ](#scenarios)


### <a name="consul-principles"></a>Consul の原則

次の原則は、Consul プロジェクトの[指針][consul-principles]となります。

- **API 駆動型** - すべての構成とポリシーを体系化します。

- **あらゆる場所で実行して接続** - ランタイム プラットフォーム (Kubernetes、Vm、サーバーレス) 間でワークロードを接続します。

- **拡張と統合** - インフラストラクチャ間でワークロードを安全に接続します。


### <a name="capabilities"></a>機能

Consul には、次の一連の機能が用意されています。

- **メッシュ** – ゲートウェイ (複数のデータ センター)、仮想マシン (クラスター ノード外)、サービス同期、組み込みのデバッグ オプション

- **プロキシ** – Envoy、組み込みプロキシ、プラグ可能、Windows ワークロードで使用できる l4 プロキシ

- **トラフィック管理** – ルーティング、分割、解決

- **ポリシー** – 意図、ACL

- **セキュリティ** – 承認、認証、暗号化、SPIFFE ベースの ID、外部 CA (コンテナー)、証明書の管理、およびローテーション

- **可観測性** – メトリック、UI ダッシュボード、prometheus、grafana


### <a name="scenarios"></a>シナリオ

Consul は、次のシナリオに適しており、推奨されています。

- 既存の Consul 接続のワークロードの拡張

- 証明書管理に関するコンプライアンス要件

- マルチ クラスター サービス メッシュ

- サービス メッシュに含まれる VM ベースのワークロード



## <a name="next-steps"></a>次のステップ

次のドキュメントでは、Azure Kubernetes Service (AKS) に Consul をインストールする方法について説明しています。

> [!div class="nextstepaction"]
> [Azure Kubernetes Service (AKS) で Consul をインストールする][consul-install]

さらに、Consul の機能とアーキテクチャについても詳しく調べることができます。

- [Consul の概要チュートリアル][consul-getting-started]
- [Consul の機能][consul-features]
- [Consul アーキテクチャ][consul-architecture]
- [Consul - 接続のしくみ][consul-how-connect-works]

<!-- LINKS - external -->
[consul]: https://www.consul.io/mesh.html
[consul-features]: https://www.consul.io/docs/connect/index.html
[consul-architecture]: https://www.consul.io/docs/internals/architecture.html
[consul-sidecar]: https://www.consul.io/docs/connect/proxies.html
[consul-how-connect-works]: https://www.consul.io/docs/connect/connect-internals.html
[consul-principles]: https://www.consul.io/
[consul-getting-started]:https://learn.hashicorp.com/consul?track=gs-consul-service-mesh#gs-consul-service-mesh

[envoy-proxy]: https://www.envoyproxy.io/
[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/

<!-- LINKS - internal -->
[consul-install]: ./servicemesh-consul-install.md
