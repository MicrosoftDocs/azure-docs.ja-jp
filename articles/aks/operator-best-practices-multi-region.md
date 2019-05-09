---
title: オペレーターのベスト プラクティス - Azure Kubernetes Services (AKS) での高可用性とディザスター リカバリー
description: Azure Kubernetes Services (AKS) でのアプリケーション アップタイムを最大化して、高可用性を提供し、ディザスター リカバリーの状況に備えるための、クラスター オペレーター向けベスト プラクティスについて説明します
services: container-service
author: lastcoolnameleft
ms.service: container-service
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: lastcoolnameleft
ms.openlocfilehash: 926f470b8a4dbdb6d6cbfe09ee61349a819600e7
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58098629"
---
# <a name="best-practices-for-business-continuity-and-disaster-recovery-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) での事業継続とディザスター リカバリーに関するベスト プラクティス

Azure Kubernetes Service (AKS) でクラスターを管理するにあたっては、アプリケーションのアップタイムが重要になります。 AKS では、可用性セット内で複数のノードを使用することにより、高可用性が提供されます。 ただしこれらの複数のノードでは、リージョンの障害に備えることはできません。 アップタイムを最大化するには、一定のビジネス継続性とディザスター リカバリー機能を実装する必要があります。

このベスト プラクティス記事では、AKS でのビジネス継続性とディザスター リカバリーを計画するうえでの考慮事項について説明します。 学習内容は次のとおりです。

> [!div class="checklist"]
> * 複数リージョンでの AKS クラスターについて計画する
> * Azure Traffic Manager を使用して、トラフィックを複数のクラスター間でルーティングする
> * コンテナー イメージのレジストリに geo レプリケーションを使用する
> * 複数のクラスター間でのアプリケーション状態について計画する
> * 複数のリージョン間でストレージをレプリケートする

## <a name="plan-for-multi-region-deployment"></a>複数リージョンへのデプロイを計画する

**ベスト プラクティス ガイダンス** - 複数の AKS クラスターをデプロイする際には、AKS が利用可能なリージョンを選択し、ペアになっているリージョンを使用します。

AKS クラスターは、1 つのリージョンにデプロイされます。 リージョンの障害から保護するには、各リージョンをカバーした複数の AKS クラスターにアプリケーションをデプロイする必要があります。 どのリージョンに AKS クラスターをデプロイするかを計画する際には、次のことを考慮しましょう。

* [AKS リージョンの可用性](https://docs.microsoft.com/azure/aks/container-service-quotas#region-availability)
  * ユーザーに近いリージョンを選択しましょう。 AKS の対応リージョンは継続的に追加されています。
* [Azure のペアになっているリージョン](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)
  * 利用する地域に対して、相互にペアになった 2 つのリージョンを選択しましょう。 これらのリージョンでは、プラットフォームの更新が調整され、必要に応じて復旧作業の優先順位が付けられます。
* サービスの可用性レベル (ホット/ホット、ホット/ウォーム、ホット/コールド)
  * 両方のリージョンを同時に実行する場合、一方のリージョンをトラフィック処理を開始する "*準備ができた*" 状態にするのか、それとも一方のリージョンはトラフィック処理を準備する時間が必要な状態にするのかを検討しましょう。

AKS リージョンの可用性とリージョンのペアは、複合的に考慮する必要があります。 AKS クラスターは、リージョンのディザスター リカバリーを連携的に管理するよう設計された、ペアのリージョンにデプロイするようにしましょう。 たとえば、AKS は "*米国東部*" と "*米国西部*" で利用できますが、 これらのリージョンもやはり、ペアになっています。 AKS の BC/DR 戦略を作成する際には、これら 2 つのリージョンを使用することをお薦めします。

また、アプリケーションをデプロイする際には、これら複数の AKS クラスターにデプロイするためのもう 1 つのステップを、CI/CD パイプラインに追加する必要があります。 デプロイ パイプラインを更新しなかった場合、アプリケーション環境は、いずれかのリージョンや AKS クラスターにしかデプロイできません。 セカンダリ リージョンに送信されるカスタマー トラフィックでは、最新のコード更新は取得されません。

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Azure Traffic Manager を使用してトラフィックをルーティングする

**ベスト プラクティス ガイダンス** - Azure Traffic Manager を使用すると、トラフィックを最短距離の AKS クラスターやアプリケーション インスタンスに送信することができます。 パフォーマンスと冗長性を最大限に高めるため、すべてのアプリケーション トラフィックは、Traffic Manager を通してから AKS クラスターへ送るようにしましょう。

複数のリージョンに複数の AKS クラスターを配置する場合は、各クラスターで実行されているアプリケーションにトラフィックをどのように送信するかを制御する必要があります。 [Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/) は、ネットワーク トラフィックをリージョン間で分散することができる、DNS ベースのトラフィック ロード バランサーです。 ユーザーのルーティングは、クラスターの応答時間、または地理的な場所に基づいて行うことができます。

![Azure Traffic Manager と AKS](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

AKS クラスターが 1 つだけの場合、ユーザーは通常、アプリケーションの "*サービス IP*" または DNS 名に接続されますが、 複数クラスターの環境では、各 AKS クラスター上のサービスを指す、Traffic Manager DNS 名に接続するようにしましょう。 これらのサービスは、Traffic Manager エンドポイントを使用して定義されます。 各エンドポイントは、"*サービス ロード バランサー IP*" となります。 この構成により、1 つのリージョンの Traffic Manager エンドポイントから、別のリージョンのエンドポイントにネットワーク トラフィックを送信できるようになります。

![Azure Traffic Manager を使用した地理的ルーティング](media/operator-best-practices-bc-dr/traffic-manager-geographic-routing.png)

Traffic Manager は、DNS 参照を実行し、ユーザーに対して最も適切なエンドポイントを返すために使用されます。 入れ子になったプロファイルを使用して、プライマリ ロケーションの優先度を指定することもできます。 たとえば、ユーザーを最短距離の地理的リージョンに最優先で接続する必要がある場合、 そのリージョンに問題が発生した際には、Traffic Manager によって接続先がセカンダリ リージョンへと切り替えられます。 このアプローチを使用すれば、地理的に最も近いリージョンが利用できなくなった場合でも、常にアプリケーション インスタンスに接続できるようになります。

これらのエンドポイントとルーティングを設定する方法については、「[Traffic Manager を使用した地理的トラフィック ルーティング方法の構成](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-geographic-routing-method)」をご覧ください。

### <a name="layer-7-application-routing-with-azure-front-door"></a>Azure Front Door を使用したレイヤー 7 のアプリケーション ルーティング

Azure Traffic Manager では、DNS (レイヤー 3) を使ってトラフィックが整理されます。 [Azure Front Door (現在プレビュー段階にあります)](https://docs.microsoft.com/azure/frontdoor/front-door-overview) を使用すると、HTTP/HTTPS (レイヤー 7) のルーティング オプションを利用できます。 Front Door で提供される追加機能には、SSL 終了、カスタム ドメイン、Web アプリケーション ファイアウォール、URL の書き換え、セッション アフィニティが含まれます。

アプリケーション トラフィックのニーズを確認して、どのソリューションが最も適切かを検討してください。

## <a name="enable-geo-replication-for-container-images"></a>コンテナー イメージの geo レプリケーションを有効にする

**ベスト プラクティス ガイダンス** - コンテナー イメージを Azure Container Registry に格納し、レジストリを各 AKS リージョンに geo レプリケーションしましょう。

AKS でアプリケーションをデプロイして実行するには、コンテナー イメージを格納してプルするための手段が必要です。 Azure Container Registry (ACR) を AKS と統合すれば、コンテナー イメージや Helm チャートを安全に格納することができます。 ACR では、マルチマスターの geo レプリケーションがサポートされており、世界各地の Azure リージョンにイメージを自動的にレプリケートすることができます。 パフォーマンスや可用性を改善するには、ACR の geo レプリケーションを使用して、AKS クラスターが置かれている各リージョンにレジストリを作成するようにしましょう。 これにより、各 AKS クラスターでは、同じリージョン内のローカル ACR レジストリから、コンテナー イメージがプルされるようになります。

![Azure Container Registry を使ったコンテナー イメージの geo レプリケーション](media/operator-best-practices-bc-dr/acr-geo-replication.png)

ACR の geo レプリケーションを使用すると、次のようなメリットがあります。

* **同じリージョンからイメージをプルしたほうが高速。** 同じ Azure リージョン内で、高速かつ低待機時間ｎネットワーク接続からイメージをプルすることができます。
* **同じリージョンからイメージをプルしたほうが信頼性が高まる。** リージョンが利用できなくなった場合、AKS クラスターは利用可能な別の ACR レジストリからイメージをプルします。
* **同じリージョンからイメージをプルしたほうが低コスト。** データセンター間のネットワーク エグレス料金が発生しません。

geo レプリケーションは、*Premium* SKU ACR レジストリの機能です。 レプリケーションを構成する方法については、「[Azure Container Registry の geo レプリケーション](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication)」をご覧ください

## <a name="remove-service-state-from-inside-containers"></a>サービスの状態をコンテナー内から削除する

**ベスト プラクティス ガイダンス** - 可能であれば、サービスの状態をコンテナー内に保存しないようにしましょう。 代わりに、複数リージョンのレプリケーションをサポートしている、Azure PaaS サービスを使用してください。

サービスの状態とは、サービスが機能するために必要な、メモリ内またはディスク上のデータのことです。 これには、サービスによって読み書きされるデータ構造やメンバー変数が含まれます。 サービスの設計によっては、ディスクに保存されているファイルやその他のリソースが含まれる場合もあります。 たとえば、データとトランザクション ログを格納するためにデータベースで使用するファイルが含まれます。

状態は、外部化するか、状態を操作するコードと同じ場所に配置できます。 状態の外部化は通常、ネットワーク上のコンピューターまたは同じコンピューター上のアウト プロセスで実行されているデータベースやその他のデータ ストアを使用して行われます。

コンテナーとマイクロサービスは、それらの内部で実行されるプロセスが状態を保持していないときに、最も回復性が高まります。 アプリケーションではほぼ常に何らかの状態が保持されるので、Azure Database for MySQL/Postgres や Azure SQL などの PaaS (サービスとしてのプラットフォーム) ソリューションを使用するようにしましょう。

移植性の高いアプリケーションを構築する方法について詳しくは、次のガイドラインをご覧ください。

* [Twelve-Factor App メソドロジー](https://12factor.net/)。
* [Web アプリケーションを複数の Azure リージョンで実行する](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region)

## <a name="create-a-storage-migration-plan"></a>ストレージ移行プランの作成

**ベスト プラクティス ガイダンス** - Azure Storage を使用する場合は、ストレージをプライマリからバックアップ リージョンに移行するための方法を準備し、それをテストするようにしましょう。

アプリケーションでは、データの格納に Azure Storage が使用される場合があります。 アプリケーションが複数のリージョンの複数の AKS クラスターに分散している場合は、ストレージの同期を維持する必要があります。 ストレージのレプリケーション方法としては、次の 2 つの方法が一般的です。

* アプリケーションベースの非同期レプリケーション
* インフラストラクチャベースの非同期レプリケーション

### <a name="infrastructure-based-asynchronous-replication"></a>インフラストラクチャベースの非同期レプリケーション

アプリケーションでは、ポッドが削除された後でも、永続的ストレージが必要とされる場合があります。 Kubernetes では、データ ストレージの保持に永続ボリュームを使用できます。 これらの永続ボリュームはノード VM にマウントされ、その後ポッドに公開されます。 永続ボリュームは、ポッドが同じクラスター内の別のノードに移動された場合でも、ポッドに追従します。

レプリケーションの戦略は、ストレージ ソリューションの使用方法によって異なる場合があります。 [Gluster](https://docs.gluster.org/en/latest/Administrator%20Guide/Geo%20Replication/)、[CEPH](http://docs.ceph.com/docs/master/cephfs/disaster-recovery/)、[Rook](https://rook.io/docs/rook/master/disaster-recovery.html)、[Portworx](https://docs.portworx.com/scheduler/kubernetes/going-production-with-k8s.html#disaster-recovery-with-cloudsnaps) などの一般的なストレージ ソリューションには、すべて独自のガイダンスがあります。

主要なアプローチは、アプリケーションがデータを書き込むための共通ストレージ ポイントを配置する方法です。 これらのデータは、その後リージョン間でレプリケートされ、ローカルにアクセスされます。

![インフラストラクチャベースの非同期レプリケーション](media/operator-best-practices-bc-dr/aks-infra-based-async-repl.png)

Azure Managed Disks を使用する場合は、レプリケーションと DR のソリューションとして、次のいずれかのアプローチが使用できます。

* [Azure 上の Ark](https://github.com/heptio/ark/blob/master/docs/azure-config.md)
* [Azure Site Recovery](https://azure.microsoft.com/blog/asr-managed-disks-between-azure-regions/)

### <a name="application-based-asynchronous-replication"></a>アプリケーションベースの非同期レプリケーション

現在のところ、アプリケーションベースの非同期レプリケーションに対応した Kubernetes ネイティブの実装はありません。 コンテナーと Kubernetes は疎結合の性質を持っているので、従来型のアプリケーションや言語アプローチで対応可能です。 主要なアプローチは、アプリケーション自体でストレージ要求をレプリケートした後、各クラスターの基盤のデータ ストレージにそれらを書き込むやり方です。

![アプリケーションベースの非同期レプリケーション](media/operator-best-practices-bc-dr/aks-app-based-async-repl.png)

## <a name="next-steps"></a>次の手順

この記事では、AKS クラスターでのビジネス継続性とディザスター リカバリーに関する考慮事項について説明しました。 AKS でのクラスター操作の詳細については、次のベスト プラクティスを参照してください。

* [マルチ テナント方式とクラスター分離][aks-best-practices-cluster-isolation]
* [Kubernetes スケジューラの基本的な機能][aks-best-practices-scheduler]

<!-- INTERNAL LINKS -->
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
