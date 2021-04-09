---
title: モデルの購入
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Azure SQL Database と Azure SQL Managed Instance で利用できる購入モデルについて説明します。
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/28/2020
ms.openlocfilehash: 8883263d6ddb2fb8ddc809f464288fcd282531bd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92788827"
---
# <a name="choose-between-the-vcore-and-dtu-purchasing-models---azure-sql-database-and-sql-managed-instance"></a>仮想コアと DTU のどちらかの購入モデルを選択する - Azure SQL Database と SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database と Azure SQL Managed Instance を利用すると、パフォーマンスとコストのニーズに合ったフル マネージドのサービスとしてのプラットフォーム (PaaS) データベース エンジンを簡単に購入できます。 Azure SQL Database 用に選択したデプロイ モデルに応じて、目的に沿った購入モデルを選択できます。

- [仮想コア (vCore) ベースの購入モデル](service-tiers-vcore.md) (推奨)。 この購入モデルでは、プロビジョニングされたコンピューティング レベルとサーバーレス コンピューティング レベルのいずれかを選択できます。 プロビジョニングされたコンピューティング レベルでは、お使いのワークロードに対して常にプロビジョニングされる正確なコンピューティング リソース量を選択します。 サーバーレス コンピューティング レベルでは、構成可能なコンピューティングの範囲で、コンピューティング リソースの自動スケーリングを指定します。 このコンピューティング レベルでは、ワークロード アクティビティに基づいてデータベースを自動的に一時停止および再開することもできます。 時間単位あたりの仮想コアの単価は、プロビジョニングされたコンピューティング レベルの方がサーバーレス コンピューティング レベルよりも低くなります。
- [データベース トランザクション ユニット (DTU) ベースの購入モデル](service-tiers-dtu.md)。 この購入モデルでは、一般的なワークロード向けに配分されたコンピューティングとストレージのバンドル パッケージが提供されます。

次の 2 つの購入モデルがあります。

- [仮想コアベースの購入モデル](service-tiers-vcore.md)は、[Azure SQL Database](sql-database-paas-overview.md) と [Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md) の両方で使用できます。 [仮想コアベースの購入モデル](service-tiers-vcore.md)を使用している単一のデータベースには、[Hyperscale サービス レベル](service-tier-hyperscale.md)を利用できます。
- [Azure SQL Database](single-database-manage.md) には、[DTU ベースの購入モデル](service-tiers-dtu.md)を利用できます。

次の表とグラフでは、仮想コア ベースと DTU ベースの購入モデルを比較対照しています。

|**購入モデル**|**説明**|**最適な用途**|
|---|---|---|
|DTU ベース|このモデルは、コンピューティング、ストレージ、および I/O リソースのバンドルされた測定値に基づいています。 コンピューティング サイズは、単一データベースの場合は DTU で、エラスティック プールの場合はエラスティック データベース トランザクション ユニット (eDTU) で表されます。 DTU と eDTU については、[DTU および eDTU とは何か](purchasing-models.md#dtu-based-purchasing-model)に関する記述をご覧ください。|シンプルな事前構成済みリソースのオプションを希望するお客様|
|仮想コアベース|このモデルでは、コンピューティング リソースとストレージ リソースを個別に選択できます。 また、仮想コアベースの購入モデルでは、SQL Server 向けの [Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-benefit/)を使用して、コストを削減することもできます。|柔軟性、制御、および透明性を重視するお客様|
||||  

![価格モデルの比較](./media/purchasing-models/pricing-model.png)

クラウドの支出を最適化して節約しますか?

[!INCLUDE [cost-management-horizontal](../../../includes/cost-management-horizontal.md)]

## <a name="compute-costs"></a>コンピューティング コスト

### <a name="provisioned-compute-costs"></a>プロビジョニングされたコンピューティングのコスト

プロビジョニングされたコンピューティング レベルのコンピューティング コストには、アプリケーションに対してプロビジョニングされたコンピューティング能力の合計が反映されています。

Business Critical サービス レベルでは、少なくとも 3 つのレプリカが自動的に割り当てられます。 コンピューティング リソースのこの追加割り当てを反映するために、仮想コアベースの購入モデルの価格は、General Purpose サービス レベルより Business Critical サービス レベルの方が約 2.7 倍高くなっています。 同様に、Business Critical サービス レベルでは GB あたりのストレージ価格も高く、SSD ストレージの高い IO 上限と低待機時間が反映されています。

Business Critical サービス レベルと General Purpose サービス レベルでは、どちらのレベルでもバックアップ用に標準のストレージが使用されているため、バックアップ ストレージのコストは同じになります。

### <a name="serverless-compute-costs"></a>サーバーレス コンピューティング コスト

サーバーレス コンピューティング レベルでのコンピューティング容量の定義方法とコストの計算方法については、[SQL Database サーバーレスのレベル](serverless-tier-overview.md)に関するページを参照してください。

## <a name="storage-costs"></a>ストレージ コスト

ストレージの種類によって課金の方法は異なります。 データ ストレージの場合、選択したデータベースまたはプールの最大サイズに基づいて、プロビジョニングされたストレージに対して課金されます。 最大値を増減しない限り、コストは変わりません。 バックアップ ストレージは、インスタンスの自動バックアップに関連付けられ、動的に割り当てられます。 バックアップのリテンション期間を長くすると、ご自身のインスタンスで使用されるバックアップ ストレージが増えます。

既定では、データベースの 7 日分の自動バックアップが、読み取りアクセス geo 冗長ストレージ (RA-GRS) の標準 BLOB ストレージ アカウントにコピーされます。 このストレージは、毎週の完全バックアップ、毎日の差分バックアップ、5 分ごとにコピーされるトランザクション ログ バックアップによって使用されます。 トランザクション ログのサイズは、データベースの変化率に応じて異なります。 データベース サイズの 100% に等しい最小ストレージ量は、追加料金なしで提供されます。 バックアップ ストレージの超過使用分については、月ごとに GB 単位で請求されます。

ストレージの価格について詳しくは、[価格](https://azure.microsoft.com/pricing/details/sql-database/single/)のページをご覧ください。

## <a name="vcore-based-purchasing-model"></a>仮想コアベースの購入モデル

仮想コア (vCore) は論理 CPU を表し、ハードウェアの世代とハードウェアの物理特性 (コア数、メモリ、ストレージ サイズなど) を選択できるオプションを提供します。 仮想コア ベースの購入モデルでは、個々のリソース使用量において柔軟性、管理性、透明性が実現されており、オンプレミスのワークロード要件をクラウドに容易に移行する方法を提供しています。 このモデルでは、ワークロードの必要性に基づいて、コンピューティング、メモリ、ストレージのリソースを選択できます。

仮想コアベースの購入モデルでは、SQL Database と SQL Managed Instance について、[General Purpose](high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability) と [Business Critical](high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability) のどちらかのサービス レベルを選択できます。  単一データベースの場合は、[Hyperscale サービス レベル](service-tier-hyperscale.md)も選択できます。

仮想コア ベースの購入モデルでは、コンピューティングとストレージのリソースを個別に選択し、オンプレミスのパフォーマンスと一致させて、価格を最適化できます。 仮想コア ベースの購入モデルでは、以下に対して支払いを行います。

- コンピューティング リソース (サービス レベル + 仮想コアの数とメモリの量 + ハードウェアの世代)。
- データおよびログ ストレージの種類と容量。
- バックアップ ストレージ (RA-GRS)。

> [!IMPORTANT]
> コンピューティング リソース、I/O、データとログのストレージは、データベースまたはエラスティック プールごとに課金されます。 バックアップ ストレージはデータベースごとに課金されます。 SQL Managed Instance の料金の詳細については、[SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md) に関するページを参照してください。
> **リージョンの制限:** サポートされているリージョンの現在の一覧については、[リージョンで利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all)に関するページを参照してください。 現在サポートされていないリージョンでマネージド インスタンスを作成するには、[Azure portal 経由でサポート要求を送信](quota-increase-request.md)します。

データベースで消費量が 300 DTU を超える場合は、仮想コア ベースの購入モデルに変換すると、コストが減る可能性があります。 任意の API を使用するか、または Azure portal を使用して、ダウンタイムなしで変換できます。 ただし、変換は必須ではなく、自動的には行われません。 DTU ベースの購入モデルが自分のパフォーマンスおよびビジネス要件を満たしている場合は、このモデルを引き続き使用してください。

DTU ベースの購入モデルから仮想コアベースの購入モデルに変換するには、[DTU から仮想コアへのへの移行](migrate-dtu-to-vcore.md)に関するページを参照してください。

## <a name="dtu-based-purchasing-model"></a>DTU ベースの購入モデル

データベース トランザクション ユニット (DTU) は、CPU、メモリ、読み取り、書き込みを組み合わせた測定値を表します。 DTU ベースの購入モデルは、事前構成済みコンピューティング リソースと付属ストレージのバンドル セットを提供することで、さまざまなレベルのアプリケーション パフォーマンスを実現します。 事前構成済みバンドルと毎月支払う料金が決まっているというシンプルさを好む場合は、DTU ベースのモデルがニーズに適している可能性があります。

DTU ベースの購入モデルでは、Azure SQL Database に対して、Basic、Standard、Premium のいずれかのサービス レベルを選択できます。 DTU ベースの購入モデルは、Azure SQL Managed Instance には使用できません。

### <a name="database-transaction-units-dtus"></a>データベース トランザクション ユニット (DTU)

[サービス レベル](single-database-scale.md)内にある特定のコンピューティング サイズの単一データベースの場合、Azure では、そのデータベース (Azure クラウド内の他のデータベースから独立した) に対し、特定のレベルのリソースが保証されます。 この保証によって、予測可能なレベルのパフォーマンスが提供されます。 データベースに割り当てられるリソースの量は、DTU の数として計算され、コンピューティング、ストレージ、および I/O リソースのバンドルされた測定値です。

これらのリソース間の比率は、本来、一般的な現実の OLTP ワークロードになるように設計された[オンライン トランザクション処理 (OLTP) ベンチマーク ワークロード](service-tiers-dtu.md)によって決定されます。 ワークロードがこれらのいずれかのリソース量を超えると、スループットが調整され、パフォーマンスが低下してタイムアウトが発生します。

お使いのワークロードによって使用されるリソースは、Azure クラウドにある他のデータベースで使用できるリソースには影響を及ぼしません。 同様に、その他のワークロードによって使用されるリソースが、お使いのデータベースで使用できるリソースに影響を及ぼすことはありません。

![境界ボックス](./media/purchasing-models/bounding-box.png)

DTU は、さまざまなコンピューティング サイズとサービス レベルのデータベースに割り当てられるリソースの相対量を理解するために最も役立ちます。 次に例を示します。

- データベースのコンピューティング サイズを引き上げて DTU を 2 倍にするのは、そのデータベースに利用できるリソース セットを 2 倍にすることと同等です。
- DTU が 1750 である Premium サービス レベルの P11 データベースは、DTU が 5 である Basic サービス レベルのデータベースと比べて、350 倍の DTU コンピューティング能力を提供します。  

お使いのワークロードのリソース (DTU) 使用量の詳細を把握するには、[クエリ パフォーマンスの分析情報](query-performance-insight-use.md)を使用して、次のようにします。

- CPU/期間/実行回数によって、パフォーマンス向上のためのチューニング対象になり得る上位クエリを特定します。 たとえば、I/O 負荷の高いクエリでは、特定のサービス レベルとコンピューティング サイズで利用可能なメモリをより効率的に使用する[メモリ内最適化手法](../in-memory-oltp-overview.md)によって、メリットが得られる場合があります。
- クエリの詳細にドリルダウンして、そのテキストやリソース使用率の履歴を表示します。
- [SQL Database Advisor](database-advisor-implement-performance-recommendations.md) によって実行されるアクションを示すアクセス パフォーマンス チューニングの推奨事項。

### <a name="elastic-database-transaction-units-edtus"></a>エラスティック データベース トランザクション ユニット (eDTU)

常時利用可能なデータベースには、必ずしも必要になるとはかぎらない専用のリソース セット (DTU) を提供する代わりに、これらのデータベースを[エラスティック プール](elastic-pool-overview.md)に配置できます。 エラスティック プール内のデータベースは、単一のサーバー上にあり、リソースのプールを共有します。

エラスティック プール内の共有リソースは、エラスティック データベース トランザクション ユニット (eDTU) によって測定されます。 エラスティック プールは、幅広く異なる予測できない使用パターンを持つ複数のデータベースに対するパフォーマンス目標を管理するための、簡単かつコスト効率に優れたソリューションを提供します。 エラスティック プールは、プール内の 1 つのデータベースによってすべてのリソースを消費できないことを保証する一方で、プール内の各データベースが常に、最低限必要な量の利用可能なリソースを保持していることを保証します。

プールには、設定価格に合わせて、設定された eDTU 数が与えられます。 エラスティック プールでは、個々のデータベースは、構成された境界内で自動スケーリングすることが可能です。 データベースの負荷が増加すると、eDTU の消費量を増やして需要に対応します。 データベースの負荷が減少すると、eDTU の消費が減ります。 負荷のないデータベースは eDTU を消費しません。 データベースごとではなく、プール全体に対してリソースがプロビジョニングされるため、エラスティック プールでは管理タスクを簡素化し、プールの予算が予測可能になります。

データベースを停止せず、プール内のデータベースに影響を及ぼさずに、既存のプールにさらに eDTU を追加できます。 同様に、追加の eDTU は、必要がなくなれば、既存のプールからいつでも削除してください。 また、プールに対するデータベースの追加やデータベースの削除は、いつでも可能です。 他のデータベース用に eDTU を予約するには、負荷の高い状況下でデータベースが使用できる eDTU の数を制限します。 データベースによるリソース使用率が継続的に低い場合は、プールから削除して、予測できる必要なリソース量を備えた単一データベースとして構成できます。

### <a name="determine-the-number-of-dtus-needed-by-a-workload"></a>ワークロードで必要とされる DTU の数を決定する

既存のオンプレミスのワークロードや、SQL Server 仮想マシンのワークロードを SQL Database に移行することを検討している場合は、[DTU Calculator](https://dtucalculator.azurewebsites.net/) を使用すると、必要な DTU のおおよその数がわかります。 既存の SQL Database ワークロードについては、[クエリ パフォーマンスの分析情報](query-performance-insight-use.md)を使用してデータベース リソースの消費量 (DTU) を把握し、ワークロードを最適化するための詳細な分析情報が得られます。 [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) 動的管理ビュー (DMV) を使用して、過去 1 時間のリソース消費量を表示できます。 [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) カタログ ビューは、過去 14 日間のリソース消費量を表示しますが、データの精度がやや低く、5 分間の平均となります。

### <a name="determine-dtu-utilization"></a>DTU 使用率を決定する

データベースまたはエラスティック プールの DTU/eDTU 上限に対する相対的な DTU/eDTU の平均使用率を決定するには、次の式を使用します。

`avg_dtu_percent = MAX(avg_cpu_percent, avg_data_io_percent, avg_log_write_percent)`

この式に入力する値は、[sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)、[sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)、および [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) DMV から取得できます。 つまり、データベースまたはエラスティック プールの DTU/eDTU 上限に対する DTU/eDTU の使用率を決定するには、特定の時点での `avg_cpu_percent`、`avg_data_io_percent`、および `avg_log_write_percent` から、最大の割合値を選びます。

> [!NOTE]
> データベースの DTU 上限は、そのデータベースで使用できる CPU、読み取り、書き込み、およびメモリによって決まります。 ただし、SQL Database エンジンでは、通常、パフォーマンス向上のためにそのデータ キャッシュ用に利用できるすべてのメモリが使用されるため、`avg_memory_usage_percent` の値は、現在のデータベースの負荷に関係なく、通常は 100% 近くになります。 そのため、メモリが DTU 上限に間接的に影響する場合でも、DTU 使用率の式では使用されません。

### <a name="workloads-that-benefit-from-an-elastic-pool-of-resources"></a>リソースのエラスティック プールを使うとメリットがあるワークロード

プールは、リソース使用率の平均が低く、使用率の急上昇が比較的発生しにくいデータベースに適しています。 詳しくは、「[SQL Database エラスティック プールを検討すべきとき](elastic-pool-overview.md)」をご覧ください。

### <a name="hardware-generations-in-the-dtu-based-purchasing-model"></a>DTU ベースの購入モデルのハードウェア世代

DTU ベースの購入モデルでは、お客様は自分のデータベースに使用されるハードウェア世代を選択できません。 データベースは通常、長い間 (通例、数か月)、特定のハードウェア世代に留まりますが、データベースを別のハードウェア世代に移すイベントがあります。

たとえば、別のサービス目標にスケール アップまたはスケール ダウンする場合、データ センターにある現在のインフラストラクチャがその容量制限に近づいている場合、あるいは現在使用されているハードウェアが耐用年数終了により使用停止になる場合、データベースを別のハードウェア世代に移動できます。

データベースを別のハードウェアに移動すると、ワークロード パフォーマンスが変わることがあります。 DTU モデルでは、サービス目標 (DTU の数) が同じである限り、データベースを別のハードウェア世代に移動しても、[DTU ベンチマーク](./service-tiers-dtu.md#dtu-benchmark) ワークロードのスループットと応答時間がおおむね同じになります。

ただし、Azure SQL Database で実行されている顧客ワークロードは幅が広く、同じサービス目標に別のハードウェアを使用したときの影響がもっと目立つこともあります。 ハードウェアの構成や特徴が違えば、ワークロードが受ける恩恵もさまざまです。 そのため、DTU ベンチマーク以外のワークロードについては、データベースをあるハードウェア世代から別のものに移した場合、パフォーマンスに違いが見られることがあります。

たとえば、ネットワークの待機時間が重要となるアプリケーションの場合、Gen5 と Gen4 を比較したとき、高速ネットワークを使用しているため Gen5 の方がパフォーマンスの面で優れています。一方、集中的な読み取り IO を扱うアプリケーションの場合、Gen4 ではコアあたりのメモリ比率が高いため、Gen4 ハードウェアのパフォーマンスが Gen5 と比べて 優れている可能性があります。

ワークロードがハードウェア変更の影響を受けやすい場合、あるいはデータベースのハードウェア世代の選択を制御する必要がある場合は、[仮想コア](service-tiers-vcore.md) モデルを使用して、データベースの作成およびスケーリング時、好みのハードウェア世代を選択できます。 仮想コア モデルでは、[単一データベース](resource-limits-vcore-single-databases.md)と[エラスティック プール](resource-limits-vcore-elastic-pools.md)の両方に対して、ハードウェア世代ごとに各サービス目標のリソース上限が記録されます。 仮想コア モデルのハードウェア世代の詳細については、「[ハードウェアの世代](./service-tiers-vcore.md#hardware-generations)」を参照してください。

## <a name="frequently-asked-questions-faqs"></a>よく寄せられる質問 (FAQ)

### <a name="do-i-need-to-take-my-application-offline-to-convert-from-a-dtu-based-service-tier-to-a-vcore-based-service-tier"></a>DTU ベースのサービス レベルから仮想コア ベースのサービス レベルに変換するには、アプリケーションをオフラインにする必要がありますか。

いいえ。 アプリケーションをオフラインにする必要はありません。 新しいサービス レベルでは、Standard と Premium のサービス レベル間でデータベースを切り替える既存のプロセスとほぼ同じ、シンプルなオンラインの変換方法を提供しています。 この変換は、Azure portal、PowerShell、Azure CLI、T-SQL、または REST API を使用して開始できます。 [単一データベースの管理](single-database-scale.md)および[エラスティック プールの管理](elastic-pool-overview.md)に関するページをご覧ください。

### <a name="can-i-convert-a-database-from-a-service-tier-in-the-vcore-based-purchasing-model-to-a-service-tier-in-the-dtu-based-purchasing-model"></a>仮想コア ベース購入モデルでのサービス レベルから DTU ベース購入モデルでのサービス レベルへ、データベースを変換することはできますか。

はい。お使いのデータベースは、Azure portal、PowerShell、Azure CLI、T-SQL、または REST API を使用して、サポートされている任意のパフォーマンス目標に簡単に変換できます。 [単一データベースの管理](single-database-scale.md)および[エラスティック プールの管理](elastic-pool-overview.md)に関するページをご覧ください。

## <a name="next-steps"></a>次のステップ

- 仮想コア ベースの購入モデルについて詳しくは、[仮想コア ベースの購入モデル](service-tiers-vcore.md)に関するページを参照してください。
- DTU ベースの購入モデルについて詳しくは、[DTU ベースの購入モデル](service-tiers-dtu.md)に関するページを参照してください。