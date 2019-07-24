---
title: Azure SQL Database の購入モデル | Microsoft Docs
description: Azure SQL Database サービスで利用可能なデータベースである購入モデルについて説明します。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
manager: craigg
ms.date: 02/08/2019
ms.openlocfilehash: 46a620900896d07273da22e53171330b85d3f1ec
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59360181"
---
# <a name="azure-sql-database-purchasing-models"></a>Azure SQL Database の購入モデル

Azure SQL Database では、パフォーマンスとコストのニーズに合ったフル マネージド PaaS データベース エンジンを簡単に購入できます。 Azure SQL Database のデプロイ モデルに応じて、ニーズに合った購入モデルを選択できます。

- [仮想コアベースの購入モデル](sql-database-service-tiers-vcore.md) (推奨)。これによりワークロードのニーズに合わせて正確な量のストレージ容量を選択し、計算することができます。
- [DTU ベースの購入モデル](sql-database-service-tiers-dtu.md)。一般的なワークロード向けに配分されたコンピューティングとストレージのバンドル パッケージを選択できます。

Azure SQL Database のデプロイ モデルでは、次のようなさまざまな購入モデルを利用できます。

- [Azure SQL Database](sql-database-technical-overview.md) の[単一データベース](sql-database-single-databases-manage.md)と[エラスティック プール](sql-database-elastic-pool.md) デプロイ オプションでは、[DTU ベースの購入モデル](sql-database-service-tiers-dtu.md)と[仮想コア ベースの購入モデル](sql-database-service-tiers-vcore.md)の両方が提供されます。
- Azure SQL Database の[マネージド インスタンス](sql-database-managed-instance.md) デプロイ オプションでは、[仮想コア ベースの購入モデル](sql-database-service-tiers-vcore.md)のみが提供されます。

> [!IMPORTANT]
> [ハイパースケール サービス レベル (プレビュー)](sql-database-service-tier-hyperscale.md) は、仮想コア購入モデルを使用する単一データベースのみを対象とするパブリック プレビューです。

次の表とグラフは、この 2 つの購入モデルを比較しています。

|**購入モデル**|**説明**|**最適な用途**|
|---|---|---|
|DTU ベースのモデル|このモデルは、コンピューティング、ストレージ、および IO リソースのバンドルされた測定値に基づいています。 コンピューティング サイズは、単一データベースの場合はデータベース トランザクション ユニット (DTU) で、エラスティック プールの場合はエラスティック データベース トランザクション ユニット (eDTU) で表されます。 DTU と eDTU の詳細については、[DTU と eDTU の概要](sql-database-purchase-models.md#dtu-based-purchasing-model)に関する記事を参照してください。|シンプルな構成済みリソースのオプションを希望するお客様に最適です。|
|仮想コアベースのモデル|このモデルでは、コンピューティング リソースとストレージ リソースを個別に選択できます。 また、仮想コアベースの購入モデルでは、[SQL Server 向けの Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-benefit/)を使用して、コストを削減することもできます。|柔軟性、制御、および透明性を重視するお客様に最適です。|
||||  

![価格モデル](./media/sql-database-service-tiers/pricing-model.png)

## <a name="compute-costs"></a>コンピューティング コスト

コンピューティング コストには、アプリケーションに対してプロビジョニングされたコンピューティング能力の合計が反映されています。 Business Critical サービス レベルでは、少なくとも 3 つのレプリカが自動的に割り当てられます。 追加で割り当てられたこのコンピューティング リソースを反映するため、仮想コアベースの購入モデルでは、General Purpose サービス レベルより Business Critical サービス レベルの方が約 2.7 倍高くなっています。 同じ理由で、Business Critical サービス レベルでは GB あたりのストレージ価格も高く、これには SSD ストレージの高 IO と低遅延が反映されています。 一方、バックアップ ストレージについては、両方のケースで Standard Storage クラスが使用されているため、2 つのサービス レベルの間でコストに違いはありません。

## <a name="storage-costs"></a>ストレージ コスト

ストレージの種類によって課金の方法は異なります。 データ ストレージの場合、選択したデータベースまたはプールの最大サイズに基づいて、プロビジョニングされたストレージに対して課金されます。 最大値を増減しない限り、コストは変わりません。 バックアップ ストレージは、インスタンスの自動バックアップに関連付けられ、動的に割り当てられます。 バックアップのリテンション期間を長くすると、ご自分のインスタンスで使用されるバックアップ ストレージが増えます。

既定ではデータベースの 7 日分の自動バックアップが、RA-GRS Standard BLOB ストレージにコピーされます。 このストレージは、毎週の完全バックアップ、毎日の差分バックアップ、5 分ごとにコピーされるトランザクション ログ バックアップによって使用されます。 トランザクション ログのサイズは、データベースの変化率によって異なります。 データベース サイズの 100% に等しい最小ストレージ量は、追加料金なしで提供されます。 100% を超えるバックアップ ストレージの使用については、月あたりの GB 数で請求されます。

ストレージの価格について詳しくは、[価格](https://azure.microsoft.com/pricing/details/sql-database/single/)のページをご覧ください。

## <a name="vcore-based-purchasing-model"></a>仮想コアベースの購入モデル

仮想コアは、ハードウェアの世代とハードウェアの物理特性 (コア数、メモリ、ストレージ サイズなど) を選択できる論理 CPU を表します。 仮想コアベースの購入モデルでは、個々のリソース使用量において柔軟性、管理性、透明性が実現されており、オンプレミスのワークロード要件をクラウドに容易に移行できます。 このモデルでは、ワークロードの必要性に基づいて、コンピューティング、メモリ、ストレージを選択できます。 仮想コアベースの購入モデルでは、[単一データベース](sql-database-single-database-scale.md)、[エラスティック プール](sql-database-elastic-pool.md)、および[マネージド インスタンス](sql-database-managed-instance.md)について、[General Purpose](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability) または [Business Critical](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) のいずれかのサービス レベルを選択できます。 単一データベースの場合は、[ハイパースケール サービス レベル (プレビュー)](sql-database-service-tier-hyperscale.md) も選択できます。

仮想コア ベースの購入モデルでは、コンピューティングおよびストレージ リソースを個別に選択し、オンプレミスのパフォーマンスを一致させて、コストを最適化できます。 仮想コアベースの購入モデルでは、お客様には次のものに対してお支払いいただきます。

- コンピューティング (サービス レベル + 仮想コアの数とメモリの量 + ハードウェアの世代)
- データおよびログ ストレージの種類と容量
- バックアップ ストレージ (RA-GRS)

> [!IMPORTANT]
> コンピューティング、IO、データおよびログ ストレージは、データベースまたはエラスティック プールごとに課金されます。 バックアップ ストレージはデータベースごとに課金されます。 マネージド インスタンスの料金について詳しくは、[マネージド インスタンス](sql-database-managed-instance.md)に関するページを参照してください。
> **リージョンの制限:** サポートされているリージョンの現在の一覧については、[リージョンで利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all)に関するページを参照してください。 現在サポートされていないリージョンで Managed Instance を作成したい場合は、[Azure portal 経由でサポート要求を送信](sql-database-managed-instance-resource-limits.md#obtaining-a-larger-quota-for-sql-managed-instance)できます。
。

単一データベースまたはエラスティック プールで消費量が 300 DTU を超える場合は、仮想コアベースの購入モデルに変換すると、コストが減る可能性があります。 変換する場合は、任意の API または Azure portal を使用して、ダウンタイムなしで変換できます。 ただし、変換は必須ではなく、自動的には行われません。 DTU ベースの購入モデルが自分のパフォーマンスおよびビジネス要件を満たしている場合は、このモデルを引き続き使用してください。 DTU ベースの購入モデルから仮想コアベースの購入モデルに変換する場合は、次の原則を使用してコンピューティング サイズを選択します。

- Standard レベルの 100 DTU ごとに、General Purpose レベルでは少なくとも 1 つの仮想コアが必要になります
- Premium レベルの 125 DTU ごとには、Business Critical レベルでは少なくとも 1 つの仮想コアが必要になります

## <a name="dtu-based-purchasing-model"></a>DTU ベースの購入モデル

データベース トランザクション ユニット (DTU) は、CPU、メモリ、読み取り、書き込みの測定値を組み合わせて算出されます。 DTU ベースの購入モデルは、事前構成済みコンピューティング リソースと付属ストレージのバンドル セットを提供することで、さまざまなレベルのアプリケーション パフォーマンスを実現します。 事前構成済みバンドルと毎月支払う料金が決まっているというシンプルさを好むお客様については、この DTU ベースのモデルがニーズに適している可能性があります。 DTU ベースの購入モデルでは、[単一データベース](sql-database-single-database-scale.md)と[エラスティック プール](sql-database-elastic-pool.md)の両方について、お客様は **Basic**、**Standard**、**Premium** のいずれかのサービス レベルを選択できます。 この購入モデルは、[マネージド インスタンス](sql-database-managed-instance.md)では利用できません。

### <a name="database-transaction-units-dtus"></a>データベース トランザクション ユニット (DTU)

[サービス レベル](sql-database-single-database-scale.md)内の特定のコンピューティング サイズの単一データベースの場合、Microsoft では、そのデータベース (Azure クラウド内の他のデータベースから独立した) に対し、特定のレベルのリソースと予測可能なレベルのパフォーマンスの提供を保証します。 リソースの量は、データベース トランザクション ユニット (DTU) の数として計算され、コンピューティング、ストレージ、および IO リソースのバンドルされたメジャーです。 これらのリソース間の比率は、最初に一般的な現実の OLTP ワークロードとして設計された [OLTP ベンチマーク ワークロード](sql-database-benchmark-overview.md)によって特定されます。 ワークロードがこれらのいずれかのリソースの量を超えると、スループットが調整され、パフォーマンスが低下し、タイムアウトが発生します。 ワークロードによって使用されるリソースは、Azure クラウド内の他の SQL Database で使用できるリソースには影響せず、他のワークロードによって使用されるリソースは、SQL Database で使用できるリソースに影響しません。

![境界ボックス](./media/sql-database-what-is-a-dtu/bounding-box.png)

DTU は、さまざまなコンピューティング サイズとサービス レベルの Azure SQL Database 間のリソースの相対量を理解するために最も役立ちます。 たとえば、データベースのコンピューティング サイズを上げて DTU を 2 倍にすると、そのデータベースが利用できるリソースを 2 倍にしたのと同じ効果が得られます。 たとえば、DTU が 1750 である Premium P11 データベースは、DTU が 5 である Basic データベースと比べ、DTU 換算で 350 倍の計算能力を持ちます。  

ワークロードのリソース (DTU) の使用量の詳細を把握するには、[クエリ パフォーマンスの分析情報](sql-database-query-performance.md)を使用して、次のようにします。

- パフォーマンス向上のためのチューニングの対象となる可能性がある CPU/期間/実行回数の上位クエリを特定します。 たとえば、IO 負荷の高いクエリは、特定のサービス レベルとコンピューティング　サイズで使用可能なメモリを効率的に使用するために、[メモリ内最適化手法](sql-database-in-memory.md)を使用すると利点が得られる可能性があります。
- クエリの詳細にドリルダウンして、そのテキストやリソース使用率の履歴を表示します。
- [SQL Database Advisor](sql-database-advisor.md) によって実行されるアクションを示すアクセス パフォーマンス チューニングの推奨事項。

### <a name="elastic-database-transaction-units-edtus"></a>エラスティック データベース トランザクション ユニット (eDTU)

常に使用できる SQL Database にとって常に必要とは限らない専用のリソース (DTU) セットを提供する代わりに、SQL Database サーバー上の[エラスティック プール](sql-database-elastic-pool.md)に、これらのデータベース間でリソースのプールを共有するデータベースを配置できます。 エラスティック プール内の共有リソースは、エラスティック データベース トランザクション ユニット (eDTU) によって測定されます。 エラスティック プールは、多種多様な予測できない使用パターンを持つ複数のデータベースに対するパフォーマンス目標を管理するための、簡単でコスト効率に優れたソリューションを提供します。 エラスティック プールは、プール内の 1 つのデータベースによってリソースを消費できないことを保証する一方で、プール内の各データベースが常に、最低限必要な量の利用可能なリソースを持っていることを保証します。

プールには、設定価格に合わせて、設定された eDTU 数が与えられます。 エラスティック プール内の個々のデータベースには、構成された境界内で自動スケールを行う柔軟性が与えられます。 データベースの負荷が増加すると、eDTU の消費量を増やして需要に対応します。 データベースの負荷が減少すると、eDTU の消費が減ります。 負荷のないデータベースは eDTU を消費しません。 データベースごとではなく、プール全体のリソースをプロビジョニングすることにより、管理タスクが簡素化され、プールの予算が予測可能になります。

データベースを停止せず、プール内のデータベースに影響を及ぼさずに、既存のプールに eDTU を追加できます。 同様に、不要になった eDTU は、いつでも既存のプールから削除できます。 プールのデータベースを増減できるほか、負荷が大きいときにデータベースが使用できる eDTU の量を制限して、他のデータベース用の eDTU を確保することもできます。 データベースのリソース使用率が低いと予測できる場合は、プールから削除して、予測可能な量の必要リソースを備えた単一データベースとして構成できます。

### <a name="determine-the-number-of-dtus-needed-by-a-workload"></a>ワークロードで必要とされる DTU の数を決定する

既に存在するオンプレミスのワークロードや、SQL Server 仮想マシンのワークロードを Azure SQL Database に移行することを検討している場合には、[DTU Calculator](https://dtucalculator.azurewebsites.net/) を使用すると、必要な DTU のおおよその数がわかります。 既存の Azure SQL Database ワークロードについては、[クエリ パフォーマンスの分析情報](sql-database-query-performance.md)を使用してデータベース リソースの消費量 (DTU) を把握でき、ワークロードを最適化するための詳細な分析情報が得られます。 このほか、[sys.dm_db_ resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) DMV を使用して、過去 1 時間のリソース消費量を確認することもできます。 また、カタログ ビュー [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) は過去 14 日間のリソース消費量を表示しますが、こちらはデータの精度がやや低く、5 分間の平均となります。

### <a name="workloads-that-benefit-from-an-elastic-pool-of-resources"></a>リソースのエラスティック プールを使うとメリットがあるワークロード

プールは、多数のデータベースが一定のパターンで使用されている場合に適しています。 あるデータベースは、使用が急増することはあまりなく、使用量平均が低いパターンの特徴を持っています。 SQL Database は、既存の SQL Database サーバー内にあるデータベースのリソース使用量の履歴を自動的に評価し、Azure ポータルでのプールの適切な構成を推奨します。 詳細については、「[エラスティック プールの使用に適した状況](sql-database-elastic-pool.md)」を参照してください。

## <a name="purchase-model-frequently-asked-questions-faq"></a>購入モデルに関してよくあるご質問 (FAQ)

### <a name="do-i-need-to-take-my-application-offline-to-convert-from-a-dtu-based-database-to-a-vcore-based-service-tier"></a>DTU ベースのデータベースから仮想コアベースのサービス レベルに変更するには、アプリケーションをオフラインにする必要がありますか

新しいサービス レベルでは、シンプルなオンラインの変換方法が提供されます。これは、Standard と Premium のサービス レベルの間でデータベースを切り替える既存のプロセスと同様です。 この変換は、Azure ポータル、PowerShell、Azure CLI、T-SQL、または REST API を使用して開始できます。 [単一データベースの管理](sql-database-single-database-scale.md)および[エラスティック プールの管理](sql-database-elastic-pool.md)に関するページをご覧ください。

### <a name="can-i-convert-a-database-from-a-service-tier-using-the-vcore-based-purchase-to-a-service-tier-using-the-dtu-based-purchasing-model"></a>仮想コアベースの購入モデルを使用するサービス レベルから DTU ベースの購入モデルを使用するサービス レベルにデータベースを変換できますか

はい。お使いのデータベースは、Azure portal、PowerShell、Azure CLI、T-SQL、REST API のいずれかで、サポートされている任意のパフォーマンス目標に簡単に変換できます。 [単一データベースの管理](sql-database-single-database-scale.md)および[エラスティック プールの管理](sql-database-elastic-pool.md)に関するページをご覧ください。

## <a name="next-steps"></a>次の手順

- 仮想コア ベースの購入モデルについては、[仮想コア ベースの購入モデル](sql-database-service-tiers-vcore.md)に関する記事を参照してください。
- DTU ベースの購入モデルについては、「[DTU ベースの購入モデル](sql-database-service-tiers-dtu.md)」を参照してください。
