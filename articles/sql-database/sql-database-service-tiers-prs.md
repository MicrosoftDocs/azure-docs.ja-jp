---
title: Azure SQL Database Premium RS サービス レベルの廃止 | Microsoft Docs
description: Premium RS サービス レベルは廃止される予定であり、そのサポートは終了される予定です。移行オプションを参照してください。
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
ms.date: 02/07/2019
ms.openlocfilehash: f8fc1f7e338f69b42df69d9a8983e8ce3bb409e3
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59359633"
---
# <a name="azure-sql-database-premium-rs-service-tier-preview-is-being-retired---options-for-migration"></a>Azure SQL Database Premium RS サービス レベル (プレビュー) の廃止 - 移行のオプション

2018 年 2 月、Microsoft は、Azure SQL Database の Premium RS サービス レベルが一般提供版としてリリースされず、2019 年 1 月 31 日以降にサポートされなくなることを発表しました。 このサポート終了の期限は、2019 年 6 月 30 日まで延期されました。 この記事では、Premium RS サービス レベルから別のサービス レベルへの移行に関するお客様のオプションについて説明します。 2019 年 6 月 30日以降、Microsoft は、お客様の Premium RS データベースをそのパフォーマンス要件に最も合致する一般提供のサービス レベルに自動的に移行します。

以下は、Premium RS のお客様に適切であると考えられる移行先と価格オプションです。

- 仮想コア サービス レベル

  [仮想コアベースの購入モデル](sql-database-service-tiers-vcore.md)の **General Purpose** サービス レベルと **Business Critical** サービス レベル。 これらの 2 つのサービス レベルは一般提供中です。 仮想コアベースの購入モデルでは、最大でデータベースあたり 100 TB の自動スケーリングによってお客様のワークロードのニーズにオンデマンドで適応する**ハイパースケール** サービス レベル (パブリック プレビュー段階) も提供されています。 ハイパースケール サービス レベルでは、Premium RS サービス レベルにより近い価格で、[DTU ベースの購入モデル](sql-database-service-tiers-dtu.md)の Premium サービス レベルに相当する IO パフォーマンスが提供されます。
- Dev/Test 価格

  [Dev/Test 価格](https://azure.microsoft.com/pricing/dev-test/)では、お客様の Visual Studio サブスクリプションによって、ライセンス込みの価格と比較して最大 55% の割引が提供されます。
- Azure ハイブリッド特典と予約容量価格

  [Azure ハイブリッド特典と予約容量価格](https://azure.microsoft.com/pricing/details/sql-database/)では、ライセンス込みの価格と比較して最大 80% の割引が提供されます。 これらのオプションの詳細については、[SQL Server 向けの Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-benefit/)に関するページと [Azure SQL Database の予約容量](sql-database-reserved-capacity.md)に関するページを参照してください。

## <a name="act-now-to-migrate-your-premium-rs-databases-to-alternative-sql-database-service-tiers"></a>お客様の Premium RS データベースを別の SQL Database サービス レベルに移行するために今すぐ行動する

Microsoft の価格およびドキュメントと共にこの記事のガイダンスを確認し、お客様の Premium RS ワークロードにとって適切な移行先を特定してください。

## <a name="migrate-compute-intensive-workloads-and-save"></a>コンピューティング集中型ワークロードを移行して節約する

お客様のコンピューティング集中型 Premium RS ワークロードについては、一般提供されている仮想コアベースの General Purpose サービス レベルに移行し、SQL Server 向けの Azure ハイブリッド特典と予約容量のオファーを使用してライセンス込みの価格よりさらに節約することをお勧めします。 DTU ベースの購入オプションに留まりたい場合は、お客様のコンピューティング集中型 Premium RS データベースを Standard サービス レベルに移行しても、(仮に一般提供されていた場合の) Premium RS の一般提供価格より節約できます。

> [!WARNING]
> お客様の Premium RS ワークロードを DTU ベースの Premium サービス レベルに移行すると、現在の Premium RS 価格よりも月額料金が高くなる場合があります。 Premium RS と同程度の料金またはそれよりも低い料金を維持するために、Azure ハイブリッド特典と予約容量価格を利用したハイパースケール レベルまたは Business Critical レベルを検討することをお勧めします。

### <a name="premium-rs-databases"></a>Premium RS データベース

|**現在利用しているのが以下の場合**|**相当する仮想コアベースの以下に移行**|**相当する DTU ベースの以下に移行**|
|---|---|---|
|Premium RS 1|General Purpose 1 仮想コア (Gen 4)|Standard 3|
|Premium RS 2|General Purpose 2 仮想コア (Gen 4)|Standard 4|
|Premium RS 4|General Purpose 4 仮想コア (Gen 4)|Standard 6|
|Premium RS 6|General Purpose 6 仮想コア (Gen 4)|Standard 7|

### <a name="premium-rs-pools"></a>Premium RS プール

|**現在利用しているのが以下の場合**|**相当する仮想コアベースの以下に移行**|**相当する DTU ベースの以下に移行**|
|---|---|---|
|Premium RS プール 125 DTU|General Purpose 1 仮想コア (Gen 4)|Standard プール 100 eDTU|
|Premium RS プール 250 DTU|General Purpose 2 仮想コア (Gen 4)|Standard プール 250 eDTU|
|Premium RS プール 500 DTU|General Purpose 4 仮想コア (Gen 4)|Standard プール 500 eDTU|
|Premium RS プール 1000 DTU|General Purpose 8 仮想コア (Gen 4)|Standard プール 1000 eDTU|

## <a name="optimize-savings-and-performance-for-your-io-intensive-workloads"></a>お客様の IO 集中型ワークロードに関して節約とパフォーマンスを最適化する

パフォーマンスとコストの組み合わせを最適化するために、お客様の IO 集中型単一データベースは仮想コアベースのハイパースケール レベル (現在プレビュー段階) に移行し、お客様の IO 集中型データベース プールは一般提供されている Business Critical レベルに移行することをお勧めします。  次の仮想コアベースのオプションでは、お客様の現在のパフォーマンスが維持または改善されます。また、Azure ハイブリッド特典および予約容量価格と組み合わせてコストを節約できる場合があります。

|**現在利用しているのが以下の場合**|**相当する仮想コアベースの以下に移行**|**相当する DTU ベースの以下に移行**|
|---|---|---|
|Premium RS 1|(プレビュー) ハイパースケール 1 仮想コア (Gen4) または Business Critical 1 仮想コア (Gen4)|Premium 1|
|Premium RS 2|(プレビュー) ハイパースケール 2 仮想コア (Gen4) または Business Critical 2 仮想コア (Gen4)|Premium 2|
|Premium RS 4|(プレビュー) ハイパースケール 4 仮想コア (Gen4) または Business Critical 4 仮想コア (Gen4)|Premium 4
|Premium RS 6|(プレビュー) ハイパースケール 6 仮想コア (Gen4) または Business Critical 6 仮想コア (Gen4)|Premium 6|

|**現在利用しているのが以下の場合**|**相当する仮想コアベースの以下に移行**|**相当する DTU ベースの以下に移行**|
|---|---|---|
|Premium RS プール 125 DTU|Business Critical 2 仮想コア (Gen4)|Premium プール 125 eDTU|
|Premium RS プール 250 DTU|Business Critical 2 仮想コア (Gen4)|Premium プール 250 eDTU|
|Premium RS プール 500 DTU|Business Critical 4 仮想コア (Gen4)|Premium プール 500 eDTU|
|Premium RS プール 1000 DTU|Business Critical 8 仮想コア (Gen4)|Premium プール 1000 eDTU|

## <a name="take-advantage-of-our-new-offers"></a>Microsoft の新しいオファーを利用する

仮想コアベースの購入モデルのサービス レベルは、ライセンス込みの価格と比較して最大 80% 節約できる特別オファーの対象となります。 アクティブなソフトウェア アシュアランス付きの SQL Server Standard エディションまたは Enterprise エディションのライセンスを使用し、[SQL Server 向けのAzure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-benefit/)によってライセンス込みの価格と比較して最大 55% 節約できます。 1 年または 3 年の期間で前払いを行うと、ハイブリッド特典を [Azure SQL Database 予約容量](sql-database-reserved-capacity.md)価格と組み合わせて最大 80% 節約できます。  今すぐ Azure portal で両方の特典をアクティブ化しましょう。

この変更についてご質問や不明な点がある場合、または移行に関するサポートが必要な場合は、[Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) にお問い合わせください。

## <a name="migration-from-a-premium-rs-service-tier-to-a-service-tier-in-either-the-dtu-or-the-vcore-model"></a>Premium RS サービス レベルから DTU または仮想コアのモデルのサービス レベルへの移行

### <a name="migration-of-a-database"></a>データベースの移行

Premium RS サービス レベルから DTU モデルまたは仮想コア モデルのサービス レベルへの移行は、Premium RS サービス レベル内のサービス レベル間でのアップグレードまたはダウングレードに類似しています。

### <a name="using-database-copy-to-convert-a-premium-rs-database-to-a-dtu-based-or-vcore-based-database"></a>データベースのコピーを使用した、Premium RS データベースから DTU ベースまたは仮想コアベースのデータベースへの変換

ターゲットのコンピューティング サイズがソースのデータベースの最大データベース サイズをサポートしている限り、制限や特別なシーケンス処理を伴うことなく、Premium RS コンピューティング サイズのデータベースを DTU ベースまたは仮想コアベースのコンピューティング サイズのデータベースにコピーできます。 データベースのコピーによって、コピー操作を開始した時点のデータ スナップショットが作成されるので、コピー元とコピー先の間でデータ同期が行われません。

## <a name="next-steps"></a>次の手順

- 単一データベースに対して選択できる具体的なコンピューティング サイズとストレージ サイズの詳細については、[単一データベースに対する SQL Database の仮想コアベースのリソース制限](sql-database-vcore-resource-limits-single-databases.md#general-purpose-service-tier-storage-sizes-and-compute-sizes)に関するページを参照してください。
- エラスティック プールに対して選択できる具体的なコンピューティング サイズとストレージ サイズの詳細については、[エラスティック プールに対する SQL Database 仮想コア ベースのリソース制限](sql-database-vcore-resource-limits-elastic-pools.md#general-purpose-service-tier-storage-sizes-and-compute-sizes)に関するページを参照してください。
