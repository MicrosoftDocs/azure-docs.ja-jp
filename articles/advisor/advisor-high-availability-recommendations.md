---
title: Azure Advisor によるアプリケーションの高可用性の向上 | Microsoft Docs
description: Azure Advisor を使用して、Azure のデプロイの高可用性を向上させます。
services: advisor
documentationcenter: NA
author: kasparks
ms.author: kasparks
ms.service: advisor
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: d78d8a689b860162a742e85c155205f072a3667c
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446790"
---
# <a name="improve-availability-of-your-application-with-azure-advisor"></a>Azure Advisor によるアプリケーションの高可用性の向上

Azure Advisor を使用して、ビジネスに不可欠なアプリケーションの継続稼働を保証し、さらに向上させることができます。 Advisor による高可用性に関する推奨事項は、Advisor ダッシュボードの **[高可用性]** タブで取得できます。

## <a name="ensure-virtual-machine-fault-tolerance"></a>仮想マシンのフォールト トレランスを確保する

アプリケーションに冗長性をもたらすには、可用性セット内に 2 つ以上の仮想マシンをグループ化することをお勧めします。 Advisor は、可用性セットの一部ではない仮想マシンを識別し、それらを可用性セットに移動することを推奨します。 このような構成により、計画済みのメンテナンス イベント中でも計画外のメンテナンス イベント中でも、少なくとも 1 台の仮想マシンが利用可能となり、Azure 仮想マシンの SLA が満たされます。 仮想マシンの可用性セットを作成するか、既存の可用性セットに仮想マシンを追加するかを選択できます。

> [!NOTE]
> 可用性セットを作成する場合は、少なくとも 1 台以上の仮想マシンを可用性セットに追加する必要があります。 2 台以上の仮想マシンを 1 つの可用性セットにグループ化して、障害の発生時に少なくとも 1 台のマシンを使用できるようにしておくことをお勧めします。

## <a name="ensure-availability-set-fault-tolerance"></a>可用性セットのフォールト トレランスを確保する

アプリケーションに冗長性をもたらすには、可用性セット内に 2 つ以上の仮想マシンをグループ化することをお勧めします。 Advisor は、1 台の仮想マシンのみを含む可用性セットを識別し、そのセットにさらに仮想マシンを追加することを推奨します。 このような構成により、計画済みのメンテナンス イベント中でも計画外のメンテナンス イベント中でも、少なくとも 1 台の仮想マシンが利用可能となり、Azure 仮想マシンの SLA が満たされます。 仮想マシンを作成するか、既存の仮想マシンを可用性セットに追加するかを選択できます。  

## <a name="use-managed-disks-to-improve-data-reliability"></a>データの信頼性を改善するために Managed Disks を使用する

ストレージ アカウントまたはストレージ スケール ユニットを共有するディスクが含まれる、可用性セット内の仮想マシンは、停止時の単一ストレージ スケール ユニット障害に対する回復性がありません。 Advisor では、これらの可用性セットを識別し、Azure Managed Disks への移行を推奨します。 そうすることで、単一障害点を避けるために、可用性セット内の異なる仮想マシンのディスクが十分に分離されるようにすることができます。 

## <a name="ensure-application-gateway-fault-tolerance"></a>アプリケーション ゲートウェイのフォールト トレランスを確保する

この推奨事項により、アプリケーション ゲートウェイを備えるミッション クリティカルなアプリケーションのビジネス継続性が確保されます。 Advisor は、フォールト トレランス用に構成されていないアプリケーション ゲートウェイ インスタンスを識別し、実施できる修復アクションを提案します。 Advisor は、中規模または大規模な単一インスタンス アプリケーション ゲートウェイを識別し、少なくとも 1 つ以上のインスタンスを追加することを推奨します。 また、1 つまたは複数の小規模アプリケーション ゲートウェイを識別し、中規模または大規模な SKU に移行することを推奨します。 これらの修復アクションでは、アプリケーション ゲートウェイ インスタンスがこれらのリソースの現在の SLA 要件を満たすように構成されていることを確認します。

## <a name="protect-your-virtual-machine-data-from-accidental-deletion"></a>誤って削除されないように、仮想マシンのデータを保護する

仮想マシンのバックアップを設定することにより、ビジネス クリティカルなデータの可用性が確保され、偶発的な削除または破損からデータを保護します。 Advisor は、バックアップが有効になっていない仮想マシンを識別子、バックアップを有効にすることを推奨します。 

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>必要な時に Azure クラウドの専門家を利用できるようにする

ビジネスに不可欠なワークロードを実行しているとき、必要時にはテクニカル サポートを受けることができるようにしておくことが重要です。 Advisor は、サポート計画にテクニカル サポートが含まれないビジネスに不可欠なサブスクリプションを識別し、テクニカル サポートが含まれるオプションへのアップグレードを勧めします。

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-issues-affect-you"></a>Azure の問題による影響を受けたときに通知する Azure Service Health アラートを作成する

Azure サービスの問題による影響を受けたときに通知する Azure Service Health アラートを設定することをお勧めします。 [Azure Service Health](https://azure.microsoft.com/features/service-health/) は、Azure サービスの問題による影響を受けた場合に、カスタマイズされたガイダンスとサポートを提供する無料サービスです。 Advisor はアラートが構成されていないサブスクリプションを識別し、アラートの作成を推奨します。

## <a name="configure-traffic-manager-endpoints-for-resiliency"></a>回復性を考慮して Traffic Manager エンドポイントを構成する

複数のエンドポイントを含む Traffic Manager プロファイルは、いずれかのエンドポイントに障害が発生した場合でも高い可用性が確保されます。 エンドポイントを複数の異なるリージョンに配置すると、サービスの信頼性がさらに向上します。 Advisor はエンドポイントが 1 つだけの Traffic Manger プロファイルを識別し、別のリージョンに少なくとも 1 つのエンドポイントを追加することを推奨します。

近接ルーティング用に構成されている Traffic Manager プロファイル内のエンドポイントがすべて同じリージョン内にある場合、他のリージョンのユーザーに対しては接続の遅延が発生する可能性があります。 別のリージョンにエンドポイントを追加または移動すると、一方のリージョン内のすべてのエンドポイントに障害が発生した場合でも全体的なパフォーマンスと可用性が向上します。 Advisor は、近接ルーティング用に構成されていて、すべてのエンドポイントが同じリージョン内にある Traffic Manager プロファイルを識別します。 別の Azure リージョンへのエンドポイントの追加または移動を推奨します。

Traffic Manager プロファイルが地理的なルーティング用に構成されている場合は、定義済みのリージョンに基づいてトラフィックがエンドポイントにルーティングされます。 リージョンで障害が発生した場合、定義済みのフェールオーバーはありません。 エンドポイントで [リージョンのグループ化] を [すべて (世界)] に構成すると、トラフィックのドロップが回避され、サービスの可用性が向上します。 Advisor は、地理的なルーティング用に構成されていて、エンドポイントで [リージョンのグループ化] が [すべて (世界)] に構成されていない Traffic Manager プロファイルを識別します。 エンドポイントが [すべて (世界)] になるように構成を変更することを推奨します。

## <a name="use-soft-delete-on-your-azure-storage-account-to-save-and-recover-data-after-accidental-overwrite-or-deletion"></a>Azure Storage アカウントでデータの論理的な削除を使用し、データを誤って上書きまたは削除した後にデータを保存して復旧する

ストレージ アカウントで[論理的な削除](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete)を有効にすると、削除された BLOB は完全には削除されずに、論理的に削除された状態に切り替わります。 データを上書きした場合、上書きされたデータの状態を保存するために、論理的に削除されたスナップショットが生成されます。 論理的な削除を使用すると、不注意で削除や上書きを行った場合に、回復できるようになります。 Advisor によって、論理的な削除が有効になっていない Azure Storage アカウントが特定され、有効にするように提案が行われます。

## <a name="configure-your-vpn-gateway-to-active-active-for-connection-resiliency"></a>接続の回復性を高めるために VPN ゲートウェイをアクティブ/アクティブに構成する

アクティブ/アクティブ構成では、VPN ゲートウェイの両方のインスタンスが、オンプレミスの VPN デバイスへの S2S VPN トンネルを確立します。 計画的なメンテナンス イベントまたは計画外のイベントが 1 つのゲートウェイ インスタンスで発生すると、トラフィックはもう 1 つのアクティブな IPsec トンネルに自動的に切り替えられます。 Azure Advisor では、アクティブ/アクティブとして構成されていない VPN ゲートウェイが識別され、それらを高可用性のために構成することが推奨されます。

## <a name="use-production-vpn-gateways-to-run-your-production-workloads"></a>運用環境の VPN ゲートウェイを使用して運用ワークロードを実行するには

Azure Advisor では、Basic SKU であるすべての VPN ゲートウェイが確認され、代わりに運用 SKU を使用することが推奨されます。 Basic SKU は、開発とテストを目的にして設計されています。 運用 SKU では、より多くのトンネル、BGP サポート、アクティブ/アクティブ構成オプション、カスタム Ipsec/IKE ポリシー、およびより高い安定性と可用性が提供されます。

## <a name="repair-invalid-log-alert-rules"></a>無効なログ アラート ルールを修復する

Azure Advisor は、それぞれの状態セクションで指定された無効なクエリを含むアラート ルールを検出します。 ログ アラート ルールは Azure Monitor で作成され、指定された間隔で分析クエリを実行するために使用されます。 クエリの結果によって、アラートをトリガーする必要があるかどうかが決定されます。 分析クエリは、参照されるリソース、テーブル、またはコマンドの変更のために、時間の経過と共に無効になることがあります。 Advisor は、アラート ルールが自動的に無効になることを防止し、Azure でのリソースの監視範囲を保証するために、アラート ルール内のクエリを修正することを推奨します。 [アラート ルールのトラブルシューティング](https://aka.ms/aa_logalerts_queryrepair)の詳細を確認してください。

## <a name="configure-consistent-indexing-mode-on-your-cosmos-db-collection"></a>Cosmos DB コレクションで同期 (Consistent) インデックス作成モードを構成する

非同期 (Lazy) インデックス作成モードで構成された Azure Cosmos DB コンテナーが、クエリの結果の鮮度に影響を与える場合があります。 Advisor は、この方法で構成されたコンテナーを検出し、同期モードに切り替えることを推奨します。 [Cosmos DB でのインデックス作成ポリシー](https://aka.ms/cosmosdb/how-to-manage-indexing-policy)の詳細を確認してください。

## <a name="configure-your-azure-cosmos-db-containers-with-a-partition-key"></a>パーティション キーを使用して Azure Cosmos DB コンテナーを構成する

Azure Advisor は、プロビジョニングされたストレージ クォータに近づいている Azure Cosmos DB のパーティション分割されていないコレクションを識別します。 サービスでこれらのコレクションを自動的にスケールアウトできるように、それらのコレクションをパーティション キーが定義された新しいコレクションに移行することを推奨します。 [パーティション キーの選択](https://aka.ms/cosmosdb/choose-partitionkey)の詳細を確認してください。

## <a name="upgrade-your-azure-cosmos-db-net-sdk-to-the-latest-version-from-nuget"></a>Azure Cosmos DB .NET SDK を NuGet からの最新バージョンにアップグレードする

Azure Advisor は、.NET SDK の古いバージョンを使用している Azure Cosmos DB アカウントを識別し、最新の修正プログラム、パフォーマンス向上、および新機能のために NuGet からの最新バージョンにアップグレードすることを推奨します。 [Cosmos DB .NET SDK](https://aka.ms/cosmosdb/sql-api-sdk-dotnet) の詳細を確認してください。

## <a name="upgrade-your-azure-cosmos-db-java-sdk-to-the-latest-version-from-maven"></a>Azure Cosmos DB Java SDK を Maven からの最新バージョンにアップグレードする

Azure Advisor は、Java SDK の古いバージョンを使用している Azure Cosmos DB アカウントを識別し、最新の修正プログラム、パフォーマンス向上、および新機能のために Maven からの最新バージョンにアップグレードすることを推奨します。 [Cosmos DB Java SDK](https://aka.ms/cosmosdb/sql-api-sdk-dotnet) の詳細を確認してください。

## <a name="upgrade-your-azure-cosmos-db-spark-connector-to-the-latest-version-from-maven"></a>Azure Cosmos DB Spark コネクタを Maven からの最新バージョンにアップグレードする

Azure Advisor は、Cosmos DB Spark コネクタの古いバージョンを使用している Azure Cosmos DB アカウントを識別し、最新の修正プログラム、パフォーマンス向上、および新機能のために Maven からの最新バージョンにアップグレードすることを推奨します。 [Cosmos DB Spark コネクタ](https://aka.ms/cosmosdb/spark-connector)の詳細を確認してください。

## <a name="enable-virtual-machine-replication"></a>仮想マシンのレプリケーションを有効にする
他のリージョンへのレプリケーションが有効になっていない仮想マシンは、リージョン内の障害に対する回復性が高くありません。 仮想マシンをレプリケートすることによって、Azure リージョン内での障害発生時にビジネスへの悪影響が低減されます。 Advisor は、レプリケーションが有効になっていない VM を検出し、障害発生時にリモート Azure リージョンで仮想マシンをすばやく起動できるように、レプリケーションを有効にするよう推奨します。 [仮想マシンのレプリケーションの詳細を確認する](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-quickstart)

## <a name="how-to-access-high-availability-recommendations-in-advisor"></a>Advisor の高可用性に関する推奨事項にアクセスする方法

1. [Azure Portal](https://portal.azure.com) にサインインし、[Advisor](https://aka.ms/azureadvisordashboard) を開きます。

2.  Advisor ダッシュボードの **[高可用性]** タブをクリックします。

## <a name="next-steps"></a>次の手順

Advisor の推奨事項について詳しくは、以下を参照してください。
* [Azure Advisor の概要](advisor-overview.md)
* [Advisor の使用を開始する](advisor-get-started.md)
* [Advisor のコストに関する推奨事項](advisor-cost-recommendations.md)
* [Advisor のパフォーマンスに関する推奨事項](advisor-performance-recommendations.md)
* [Advisor のセキュリティに関する推奨事項](advisor-security-recommendations.md)

