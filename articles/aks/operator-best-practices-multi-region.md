---
title: Azure Kubernetes Services (AKS) での高可用性とディザスター リカバリー
description: Azure Kubernetes Services (AKS) でアプリケーションの最大アップタイムを達成して、高可用性を提供し、ディザスター リカバリーに備えるための、クラスター オペレーターのベスト プラクティスについて説明します。
services: container-service
author: lastcoolnameleft
ms.service: container-service
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: thfalgou
ms.openlocfilehash: 4d4535af1814ab1250bbd56c989b4849013adff6
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67614840"
---
# <a name="best-practices-for-business-continuity-and-disaster-recovery-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) での事業継続とディザスター リカバリーに関するベスト プラクティス

Azure Kubernetes Service (AKS) でクラスターを管理するにあたっては、アプリケーションのアップタイムが重要になります。 AKS では、可用性セット内で複数のノードを使用することにより、高可用性が提供されます。 しかし、これらの複数のノードでは、お使いのシステムはリージョン障害から保護されません。 アップタイムを最大化するには、ビジネス継続性の維持とディザスター リカバリーの準備について事前に計画を立てておきます。

この記事では、AKS でのビジネス継続性とディザスター リカバリーに関する計画を立てる方法に重点を置いて説明します。 学習内容は次のとおりです。

> [!div class="checklist"]
> * 複数リージョン内の AKS クラスターに関する計画を立てる。
> * Azure Traffic Manager を使用して、複数のクラスター間でトラフィックをルーティングする。
> * コンテナー イメージのレジストリに geo レプリケーションを使用する。
> * 複数のクラスター間でのアプリケーション状態に関する計画を立てる。
> * 複数のリージョン間でストレージをレプリケートする。

## <a name="plan-for-multiregion-deployment"></a>複数リージョンのデプロイに関する計画を立てる

**ベスト プラクティス**: 複数の AKS クラスターをデプロイするときは、AKS が利用可能なリージョンを選択し、ペアになっているリージョンを使用します。

AKS クラスターは、1 つのリージョンにデプロイされます。 リージョンの障害からシステムを保護するには、複数のリージョンにまたがる複数の AKS クラスターにアプリケーションをデプロイします。 AKS クラスターをデプロイする場所を計画するときには、次の点を考慮してください。

* [**AKS リージョンの可能性**](https://docs.microsoft.com/azure/aks/quotas-skus-regions#region-availability):ユーザーに近いリージョンを選択しましょう。 AKS では、継続的に新しいリージョンを展開しています。
* [**Azure のペアになっているリージョン**](https://docs.microsoft.com/azure/best-practices-availability-paired-regions):利用する地域に対して、相互にペアになった 2 つのリージョンを選択しましょう。 ペアになったリージョンでは、プラットフォームの更新が調整され、必要に応じて復旧作業の優先順位が付けられます。
* **サービスの可用性**:ペアになったリージョンを、ホット/ホット、ホット/ウォーム、またはホット/コールドのいずれにするかを決定します。 両方のリージョンを同時に実行する場合、一方のリージョンをトラフィックの提供を開始する "*準備ができた*" 状態にするのか、 それとも一方のリージョンはトラフィックの提供を準備する時間が必要な状態にするのかを検討しましょう。

AKS リージョンの可用性と、ペアになったリージョンは、複合的に考慮する必要があります。 AKS クラスターは、リージョンのディザスター リカバリーを連携的に管理するよう設計された、ペアのリージョンにデプロイするようにしましょう。 たとえば、AKS は "米国東部" と "米国西部" で利用できます。 これらのリージョンはペアになっています。 AKS の BC/DR 戦略を作成するときは、これらの 2 つのリージョンを選択します。

アプリケーションをデプロイするときは、これらの複数の AKS クラスターにデプロイするためのもう 1 つのステップを CI/CD パイプラインに追加します。 デプロイ パイプラインを更新しないと、アプリケーションが 1 つのリージョンや AKS クラスターのみにデプロイされる可能性があります。 セカンダリ リージョンに送信されるカスタマー トラフィックは、最新のコード更新を受け取りません。

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Azure Traffic Manager を使用してトラフィックをルーティングする

**ベスト プラクティス**: Azure Traffic Manager は、お客様を最も近い AKS クラスターおよびアプリケーション インスタンスに送ることができます。 パフォーマンスと冗長性を最大限に高めるため、すべてのアプリケーション トラフィックは、Traffic Manager を介して AKS クラスターへ送るようにします。

複数のリージョンに複数の AKS クラスターがある場合は、Traffic Manager を使用して、各クラスターで実行されているアプリケーションにトラフィックをどのように送信するかを制御します。 [Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/) は、ネットワーク トラフィックをリージョン間で分散することができる、DNS ベースのトラフィック ロード バランサーです。 Traffic Manager を使用して、クラスターの応答時間、または地理的な場所に基づいてユーザーをルーティングします。

![Traffic Manager を使用する AKS](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

1 つだけの AKS クラスターを持つお客様は、通常、特定のアプリケーションのサービス IP または DNS 名に接続します。 複数クラスター デプロイでは、お客様は、各 AKS クラスター上のサービスを指す、Traffic Manager の DNS 名に接続する必要があります。 これらのサービスは、Traffic Manager エンドポイントを使用して定義します。 各エンドポイントは、"*サービス ロード バランサー IP*" です。 この構成を使用して、あるリージョンの Traffic Manager エンドポイントから別のリージョンのエンドポイントにネットワーク トラフィックを送信します。

![Traffic Manager を使用した地理的ルーティング](media/operator-best-practices-bc-dr/traffic-manager-geographic-routing.png)

Traffic Manager は DNS 参照を実行して、ユーザーの最も適切なエンドポイントを返します。 入れ子になったプロファイルは、プライマリの場所の優先順位を付けることができます。 たとえば、ユーザーは通常、地理的に最も近いリージョンに接続します。 そのリージョンに問題がある場合、Traffic Manager は代わりにユーザーをセカンダリ リージョンに送ります。 この方法により、地理的に最も近いリージョンが利用できなくても、お客様はアプリケーション インスタンスに確実に接続することができます。

エンドポイントとルーティングを設定する方法については、[Traffic Manager を使用した地理的トラフィック ルーティング方法の構成](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-geographic-routing-method)に関する記事をご覧ください。

### <a name="layer-7-application-routing-with-azure-front-door-service"></a>Azure Front Door Service を使用したレイヤー 7 のアプリケーション ルーティング

Traffic Manager は、DNS (レイヤー 3) を使ってトラフィックのシェーピングを行います。 [Azure Front Door Service](https://docs.microsoft.com/azure/frontdoor/front-door-overview) には、HTTP/HTTPS (レイヤー 7) のルーティング オプションが用意されています。 Azure Front Door Service の追加機能としては、SSL 終了、カスタム ドメイン、Web アプリケーション ファイアウォール、URL の書き換え、セッション アフィニティがあります。 アプリケーション トラフィックのニーズを確認して、どのソリューションが最も適切かを検討してください。

## <a name="enable-geo-replication-for-container-images"></a>コンテナー イメージの geo レプリケーションを有効にする

**ベスト プラクティス**: コンテナー イメージを Azure Container Registry に格納し、レジストリを各 AKS リージョンに geo レプリケーションします。

AKS でアプリケーションをデプロイして実行するには、コンテナー イメージを格納してプルするための手段が必要です。 Container Registry は AKS と統合することで、コンテナー イメージや Helm チャートを安全に格納することができます。 Container Registry は、マルチマスターの geo レプリケーションをサポートして、世界各地の Azure リージョンにイメージを自動的にレプリケートします。 

パフォーマンスと可用性を改善するには、Container Registry の geo レプリケーションを使用して、AKS クラスターが置かれている各リージョンにレジストリを作成します。 これにより、各 AKS クラスターは、同じリージョン内のローカル コンテナー レジストリからコンテナー イメージをプルします。

![コンテナー イメージに対する Container Registry の geo レプリケーション](media/operator-best-practices-bc-dr/acr-geo-replication.png)

Container Registry の geo レプリケーションを使用して同じリージョンからイメージをプルすると、次のような結果になります。

* **より高速**:同じ Azure リージョン内で、高速かつ低待機時間のネットワーク接続からイメージをプルします。
* **信頼性の向上**:リージョンが利用できない場合、AKS クラスターは、使用可能なコンテナー レジストリからイメージをプルします。
* **低コスト**:データセンター間のネットワーク エグレス料金が発生しません。

geo レプリケーションは、*Premium* SKU コンテナー レジストリの機能です。 geo レプリケーションを構成する方法については、[Container Registry の geo レプリケーション](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication)に関する記事をご覧ください

## <a name="remove-service-state-from-inside-containers"></a>サービスの状態をコンテナー内から削除する

**ベスト プラクティス**: 可能な場合、サービスの状態をコンテナー内に格納しないでください。 代わりに、マルチリージョン レプリケーションをサポートする Azure のサービスとしてのプラットフォーム (PaaS) を使用します。

*サービスの状態*とは、サービスが機能するために必要な、メモリ内またはディスク上のデータのことです。 これには、サービスによって読み書きされるデータ構造やメンバー変数が含まれます。 サービスの設計方法に応じて、状態には、ディスクに格納されているファイルやその他のリソースも含まれることがあります。 たとえば、状態には、データとトランザクション ログを格納するためにデータベースで使用されるファイルが含まれる場合があります。

状態は、外部化することも、状態を操作するコードと同じ場所に配置することもできます。 通常、状態の外部化は、ネットワーク上のさまざまなコンピューターで実行されている、または同一コンピューター上でアウト オブ プロセスを実行しているデータベースやその他のデータ ストアを使用して行います。

コンテナーとマイクロサービスは、それらの内部で実行されているプロセスが状態を保持していないときに、回復性が最も高くなります。 アプリケーションにはほぼ常に何らかの状態が含まれるので、Azure Database for MySQL、Azure Database for PostgreSQL、または Azure SQL Database などの PaaS ソリューションを使用してください。

ポータブル アプリケーションをビルドするには、次のガイドラインを参照してください。

* [Twelve-Factor App Methodology](https://12factor.net/)
* [Web アプリケーションを複数の Azure リージョンで実行する](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region)

## <a name="create-a-storage-migration-plan"></a>ストレージ移行プランの作成

**ベスト プラクティス**: Azure Storage を使用する場合は、ストレージをプライマリ リージョンからバックアップ リージョンに移行する方法を準備してテストします。

お使いのアプリケーションで、Azure Storage がデータに使用される場合があります。 アプリケーションは複数のリージョン内の複数の AKS クラスターに分散されるので、ストレージを同期しておく必要があります。 ストレージをレプリケートする 2 つの一般的な方法は次のとおりです。

* インフラストラクチャベースの非同期レプリケーション
* アプリケーションベースの非同期レプリケーション

### <a name="infrastructure-based-asynchronous-replication"></a>インフラストラクチャベースの非同期レプリケーション

アプリケーションには、ポッドが削除された後も、永続的ストレージが必要な場合があります。 Kubernetes では、永続ボリュームを使用してデータ ストレージを保持することができます。 永続ボリュームはノード VM にマウントされてから、ポッドに公開されます。 永続ボリュームは、ポッドが同じクラスター内の別のノードに移動されても、ポッドに従います。

使用するレプリケーションの方法は、お使いのストレージ ソリューションによって決まります。 [Gluster](https://docs.gluster.org/en/latest/Administrator%20Guide/Geo%20Replication/)、[Ceph](http://docs.ceph.com/docs/master/cephfs/disaster-recovery/)、[Rook](https://rook.io/docs/rook/master/disaster-recovery.html)、[Portworx](https://docs.portworx.com/scheduler/kubernetes/going-production-with-k8s.html#disaster-recovery-with-cloudsnaps) などの一般的なストレージ ソリューションには、ディザスター リカバリーとレプリケーションに関する独自のガイダンスがあります。

一般的な方法は、アプリケーションがデータを書き込める共通のストレージ ポイントを提供するというものです。 これらのデータは、その後リージョン間でレプリケートされ、ローカルにアクセスされます。

![インフラストラクチャベースの非同期レプリケーション](media/operator-best-practices-bc-dr/aks-infra-based-async-repl.png)

Azure Managed Disks を使用している場合は、次のようなレプリケーションと DR ソリューションを選択することができます。

* [Azure での Velero](https://github.com/heptio/velero/blob/master/site/docs/master/azure-config.md)
* [Azure Site Recovery](https://azure.microsoft.com/blog/asr-managed-disks-between-azure-regions/)

### <a name="application-based-asynchronous-replication"></a>アプリケーションベースの非同期レプリケーション

現在のところ、Kubernetes では、アプリケーションベースの非同期レプリケーションに対応したネイティブ実装は提供されていません。 コンテナーと Kubernetes は疎結合されているので、従来型のアプリケーションや言語アプローチが機能するはずです。 通常は、アプリケーション自体がストレージ要求をレプリケートし、次にそれらが、各クラスターの基盤となるデータ ストレージに書き込まれます。

![アプリケーションベースの非同期レプリケーション](media/operator-best-practices-bc-dr/aks-app-based-async-repl.png)

## <a name="next-steps"></a>次の手順

この記事では、AKS クラスターでのビジネス継続性とディザスター リカバリーに関する考慮事項に重点を置いて説明しました。 AKS でのクラスター操作の詳細については、ベスト プラクティスに関する次の記事を参照してください。

* [マルチテナント方式とクラスター分離][aks-best-practices-cluster-isolation]
* [Kubernetes スケジューラの基本的な機能][aks-best-practices-scheduler]

<!-- INTERNAL LINKS -->
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
