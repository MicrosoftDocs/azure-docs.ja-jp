---
title: Advisor によるアプリケーションの信頼性の向上
description: Azure Advisor を使用すると、Bus Critical な Azure デプロイの信頼性を確認し、向上させることができます。
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: 0d96974e53f24d5a01eeee8b08eee578177a9ad2
ms.sourcegitcommit: ef055468d1cb0de4433e1403d6617fede7f5d00e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2020
ms.locfileid: "88258496"
---
# <a name="improve-the-reliability-of-your-application-by-using-azure-advisor"></a>Azure Advisor を使用してアプリケーションの信頼性を向上させる

Azure Advisor を使用して、ビジネスに不可欠なアプリケーションの継続稼働を保証し、さらに向上させることができます。 Advisor からの信頼性に関する推奨事項は、Advisor ダッシュボードの **[信頼性]** タブで確認できます。

## <a name="check-the-version-of-your-check-point-network-virtual-appliance-image"></a>Check Point ネットワーク仮想アプライアンス イメージのバージョンを確認する

Advisor では、プラットフォームのサービス操作中にネットワーク接続が失われることがわかっているバージョンの Check Point イメージを仮想マシンが実行しているかどうかを特定できます。 Advisor の推奨事項は、この問題に対処する新しいバージョンのイメージにアップグレードするのに役立ちます。 この検査により、より安定したネットワーク接続によってビジネスの継続性が確保されます。

## <a name="ensure-application-gateway-fault-tolerance"></a>アプリケーション ゲートウェイのフォールト トレランスを確保する

この推奨事項により、アプリケーション ゲートウェイを備えるミッション クリティカルなアプリケーションのビジネス継続性が確保されます。 Advisor は、フォールト トレランス用に構成されていないアプリケーション ゲートウェイ インスタンスを識別します。 その後、実行できる修復アクションが提案されます。 Advisor は、中規模または大規模な単一インスタンス アプリケーション ゲートウェイを識別し、少なくとも 1 つ以上のインスタンスを追加することを推奨します。 また、単一インスタンスおよびマルチインスタンスの小規模アプリケーション ゲートウェイを識別し、中規模または大規模な SKU に移行することを推奨します。 Advisor が推奨するこれらの修復アクションにより、アプリケーション ゲートウェイ インスタンスがこれらのリソースの現在の SLA 要件を満たすように構成されるようになります。

## <a name="protect-your-virtual-machine-data-from-accidental-deletion"></a>誤って削除されないように、仮想マシンのデータを保護する

仮想マシンのバックアップを設定することにより、ビジネス クリティカルなデータの可用性が確保され、偶発的な削除または破損からデータを保護します。 Advisor は、バックアップが有効になっていない仮想マシンを識別し、バックアップを有効にすることを推奨します。 

## <a name="ensure-you-have-access-to-azure-experts-when-you-need-it"></a>必要な時に Azure の専門家を利用できるようにする

Bus Critical なワークロードを実行している場合、必要な時にサポート チームを利用できるようにしておくことが重要です。 Advisor は、サポート プランにサポート チームが含まれていない Bus Critical なサブスクリプションを特定します。 サポート チームを含むオプションにアップグレードすることが推奨されます。

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-problems-affect-you"></a>Azure の問題による影響を受けるときに通知される Azure Service Health アラートを作成する

Azure サービスの問題による影響を受けるときに通知される Azure Service Health アラートを設定することをお勧めします。 [Azure Service Health](https://azure.microsoft.com/features/service-health/) は、Azure サービスの問題による影響を受ける場合に、カスタマイズされたガイダンスとサポートを提供する無料サービスです。 Advisor はアラートが構成されていないサブスクリプションを識別し、構成するように推奨します。

## <a name="configure-traffic-manager-endpoints-for-resiliency"></a>回復性を考慮して Traffic Manager エンドポイントを構成する

複数のエンドポイントを含む Azure Traffic Manager プロファイルは、いずれかのエンドポイントに障害が発生した場合でも高い可用性が確保されます。 エンドポイントを複数の異なるリージョンに配置すると、サービスの信頼性がさらに向上します。 Advisor はエンドポイントが 1 つだけの Traffic Manger プロファイルを識別し、別のリージョンに少なくとも 1 つのエンドポイントを追加することを推奨します。

近接ルーティング用に構成されている Traffic Manager プロファイル内のエンドポイントがすべて同じリージョン内にある場合、他のリージョンのユーザーに対しては接続の遅延が発生する可能性があります。 別のリージョンにエンドポイントを追加または移動すると、一方のリージョン内のすべてのエンドポイントに障害が発生した場合でも全体的なパフォーマンスと可用性が向上します。 Advisor は、近接ルーティング用に構成されていて、すべてのエンドポイントが同じリージョン内にある Traffic Manager プロファイルを識別します。 別の Azure リージョンへのエンドポイントの追加または移動を推奨します。

Traffic Manager プロファイルが地理的なルーティング用に構成されている場合は、定義済みのリージョンに基づいてトラフィックがエンドポイントにルーティングされます。 リージョンで障害が発生した場合、定義済みのフェールオーバーはありません。 [リージョンのグループ化] が **[すべて (世界)]** に構成されたエンドポイントがある場合、トラフィックのドロップを回避し、サービスの可用性を向上させることができます。 Advisor は、地理的なルーティング用に構成されていて、[リージョンのグループ化] が **[すべて (世界)]** に構成されたエンドポイントがない Traffic Manager プロファイルを識別します。 エンドポイントが **[すべて (世界)]** になるように構成を変更することが推奨されます。

## <a name="use-soft-delete-on-your-azure-storage-account-to-save-and-recover-data-after-accidental-overwrite-or-deletion"></a>Azure ストレージ アカウントでデータの論理的な削除を使用し、データを誤って上書きまたは削除した後にデータを保存して復旧する

ストレージ アカウントで[論理的な削除](../storage/blobs/soft-delete-overview.md)を有効にすると、削除された BLOB は完全には削除されずに、論理的に削除された状態に切り替わります。 データを上書きした場合、上書きされたデータの状態を保存するために、論理的に削除されたスナップショットが生成されます。 論理的な削除を使用すると、不注意な削除や上書きから回復できます。 Advisor によって、論理的な削除が有効になっていない Azure ストレージ アカウントが特定され、有効にするように提案が行われます。

## <a name="configure-your-vpn-gateway-to-active-active-for-connection-resiliency"></a>接続の回復性を高めるために VPN ゲートウェイをアクティブ/アクティブに構成する

アクティブ/アクティブ構成では、VPN ゲートウェイの両方のインスタンスが、オンプレミスの VPN デバイスへの S2S VPN トンネルを確立します。 計画メンテナンス イベントまたは計画外のイベントが 1 つのゲートウェイ インスタンスで発生すると、トラフィックはもう 1 つのアクティブな IPsec トンネルに自動的に切り替えられます。 Azure Advisor では、アクティブ/アクティブとして構成されていない VPN ゲートウェイが識別され、それらを高可用性のために構成することが推奨されます。

## <a name="use-production-vpn-gateways-to-run-your-production-workloads"></a>運用環境の VPN ゲートウェイを使用して運用ワークロードを実行するには

Azure Advisor では、Basic SKU を使用するすべての VPN ゲートウェイが確認され、代わりに運用 SKU を使用することが推奨されます。 Basic SKU は、開発とテスト向けに設計されています。 運用環境の SKU は以下を提供します。
- より多くのトンネル。 
- BGP のサポート。 
- アクティブ/アクティブ構成のオプション。 
- カスタム IPsec/IKE ポリシー。 
- 高い安定性と可用性。

## <a name="ensure-virtual-machine-fault-tolerance-temporarily-disabled"></a>仮想マシンのフォールト トレランスを確保する (一時的に無効)

アプリケーションに冗長性を持たすために、1 つの可用性セット内に 2 つ以上の仮想マシンをグループ化することをお勧めします。 Advisor は、可用性セットの一部ではない仮想マシンを識別し、それらをその 1 つに移動することを推奨します。 このような構成により、計画済みまたは計画外のメンテナンスにおいて、少なくとも 1 台の仮想マシンが利用可能となり、Azure 仮想マシンの SLA が満たされます。 仮想マシンの可用性セットを作成するか、既存の可用性セットに仮想マシンを追加するかを選択できます。

> [!NOTE]
> 可用性セットを作成する場合は、少なくとも 1 台以上の仮想マシンをそれに追加する必要があります。 2 台以上の仮想マシンを 1 つの可用性セットにグループ化して、障害の発生時に少なくとも 1 台のマシンを使用できるようにしておくことをお勧めします。

## <a name="ensure-availability-set-fault-tolerance-temporarily-disabled"></a>可用性セットのフォールト トレランスを確保する (一時的に無効)

アプリケーションに冗長性を持たすために、1 つの可用性セット内に 2 つ以上の仮想マシンをグループ化することをお勧めします。 Advisor は、1 台の仮想マシンのみを含む可用性セットを識別し、そのセットにさらに仮想マシンを追加することを推奨します。 このような構成により、計画済みまたは計画外のメンテナンスにおいて、少なくとも 1 台の仮想マシンが利用可能となり、Azure 仮想マシンの SLA が満たされます。 仮想マシンを作成するか、既存の仮想マシンを可用性セットに追加するかを選択できます。  

## <a name="use-managed-disks-to-improve-data-reliability-temporarily-disabled"></a>データの信頼性の向上にマネージド ディスクを使用する (一時的に無効)

ストレージ アカウントまたはストレージ スケール ユニットを共有するディスクが含まれる、可用性セット内の仮想マシンは、停止時の単一ストレージ スケール ユニットの障害に対する回復性がありません。 Advisor では、このような可用性セットが識別され、Azure マネージド ディスクへの移行が推奨されます。 この移行により、可用性セット内の仮想マシンのディスクが十分に分離され、単一障害点を避けることができます。 

## <a name="repair-invalid-log-alert-rules"></a>無効なログ アラート ルールを修復する

Azure Advisor は、条件セクションに無効なクエリが指定された警告ルールを検出します。 Azure Monitor でログ警告ルールを作成し、それらを使用して、指定された間隔で分析クエリを実行できます。 クエリの結果によって、アラートをトリガーする必要があるかどうかが決定します。 分析クエリは、参照されるリソース、テーブル、またはコマンドの変更のために、時間の経過と共に無効になることがあります。 Advisor は、警告ルールが自動的に無効になることを防止し、Azure でのリソースの監視範囲を保証するために、警告ルール内のクエリを修正することを推奨します。 [警告ルールのトラブルシューティングの詳細を確認してください。](https://aka.ms/aa_logalerts_queryrepair)

## <a name="configure-consistent-indexing-mode-on-your-azure-cosmos-db-collection"></a>Azure Cosmos DB コレクションで同期 (Consistent) インデックス モードを構成する

Azure Cosmos DB コンテナーを非同期 (Lazy) インデックス作成モードで構成すると、クエリの結果の鮮度に影響を与える場合があります。 Advisor は、この方法で構成されたコンテナーを検出し、同期 (Consistent) モードに切り替えることを推奨します。 [Azure Cosmos DB でのインデックス作成ポリシー](https://aka.ms/cosmosdb/how-to-manage-indexing-policy)の詳細を確認してください。

## <a name="configure-your-azure-cosmos-db-containers-with-a-partition-key"></a>パーティション キーを使用して Azure Cosmos DB コンテナーを構成する

Azure Advisor は、プロビジョニングされたストレージ クォータに近づいている Azure Cosmos DB のパーティション分割されていないコレクションを識別します。 サービスによって自動的にスケール アウトできるように、これらのコレクションをパーティション キーが定義された新しいコレクションに移行するように推奨されます。 [パーティション キーの選択](https://aka.ms/cosmosdb/choose-partitionkey)の詳細を確認してください。

## <a name="upgrade-your-azure-cosmos-db-net-sdk-to-the-latest-version-from-nuget"></a>Azure Cosmos DB .NET SDK を NuGet からの最新バージョンにアップグレードする

Azure Advisor は、古いバージョンの .NET SDK を使用している Azure Cosmos DB アカウントを識別します。 最新の修正、パフォーマンスの向上、機能を入手するため、NuGet から最新バージョンにアップグレードすることが推奨されます。 [Azure Cosmos DB の .NET SDK](https://aka.ms/cosmosdb/sql-api-sdk-dotnet) の詳細を確認してください。

## <a name="upgrade-your-azure-cosmos-db-java-sdk-to-the-latest-version-from-maven"></a>Azure Cosmos DB Java SDK を Maven からの最新バージョンにアップグレードする

Azure Advisor は、古いバージョンの Java SDK を使用している Azure Cosmos DB アカウントを識別します。 最新の修正、パフォーマンスの向上、機能を入手するため、Maven から最新バージョンにアップグレードすることが推奨されます。 [Azure Cosmos DB の Java SDK](https://aka.ms/cosmosdb/sql-api-sdk-async-java) の詳細を確認してください。

## <a name="upgrade-your-azure-cosmos-db-spark-connector-to-the-latest-version-from-maven"></a>Azure Cosmos DB Spark コネクタを Maven からの最新バージョンにアップグレードする

Azure Advisor は、古いバージョンの Azure Cosmos DB Spark コネクタを使用している Azure Cosmos DB アカウントを識別します。 最新の修正、パフォーマンスの向上、機能を入手するため、Maven から最新バージョンにアップグレードすることが推奨されます。 [Azure Cosmos DB Spark コネクタ](https://aka.ms/cosmosdb/spark-connector)の詳細を確認してください。

## <a name="consider-moving-to-kafka-21-on-hdinsight-40"></a>HDInsight 4.0 で Kafka 2.1 への移行を検討する

2020 年 7 月 1 日から、Azure HDInsight 4.0 で Kafka 1.1 を使用して新しい Kafka クラスターを作成することはできません。 既存のクラスターはそのまま動作しますが、Microsoft からのサポートはありません。 システムやサポートが中断される可能性を回避するため、2020 年 6 月 30 日までに HDInsight 4.0 で Kafka 2.1 に移行することを検討してください。

## <a name="consider-upgrading-older-spark-versions-in-hdinsight-spark-clusters"></a>HDInsight Spark クラスターでの Spark の古いバージョンのアップグレードを検討する

2020 年 7 月 1 日から、HDInsight 3.6 で Spark 2.1 または 2.2 を使用して新しい Spark クラスターを作成することはできません。 HDInsight 4.0 で Spark 2.3 を使用して新しい Spark クラスターを作成することはできません。 既存のクラスターはそのまま動作しますが、Microsoft からのサポートはありません。 

## <a name="enable-virtual-machine-replication"></a>仮想マシンのレプリケーションを有効にする
他のリージョンへのレプリケーションが有効になっていない仮想マシンは、リージョンの障害に対する回復性がありません。 仮想マシンをレプリケートすることによって、Azure リージョンの障害時にビジネスへの悪影響が低減されます。 Advisor は、レプリケーションが有効になっていない VM を検出し、有効にすることを推奨します。 レプリケーションを有効にすると、障害が発生した場合に、リモートの Azure リージョンで仮想マシンをすばやく起動できます。 [仮想マシンのレプリケーション](../site-recovery/azure-to-azure-quickstart.md)の詳細を確認してください。

## <a name="how-to-access-high-availability-recommendations-in-advisor"></a>Advisor の高可用性に関する推奨事項にアクセスする方法

1. [Azure Portal](https://portal.azure.com) にサインインし、[Advisor](https://aka.ms/azureadvisordashboard) を開きます。

2.  Advisor ダッシュボードの **[高可用性]** タブを選択します。

## <a name="next-steps"></a>次のステップ

Advisor の推奨事項について詳しくは、以下を参照してください。
* [Advisor 入門](advisor-overview.md)
* [Advisor の使用を開始する](advisor-get-started.md)
* [Advisor のコストに関する推奨事項](advisor-cost-recommendations.md)
* [Advisor のパフォーマンスに関する推奨事項](advisor-performance-recommendations.md)
* [Advisor のセキュリティに関する推奨事項](advisor-security-recommendations.md)
* [Advisor のオペレーショナル エクセレンスに関する推奨事項](advisor-operational-excellence-recommendations.md)
