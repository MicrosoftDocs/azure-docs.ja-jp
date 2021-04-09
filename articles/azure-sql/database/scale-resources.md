---
title: リソースのスケール
description: この記事では、割り当てられたリソースを追加または削除することで、Azure SQL Database と SQL Managed Instance でデータベースをスケーリングする方法について説明します。
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: wiassaf, sstein
ms.date: 06/25/2019
ms.openlocfilehash: 453d7e118b946d60eb3d84c6a66abdbea7db2410
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96499222"
---
# <a name="dynamically-scale-database-resources-with-minimal-downtime"></a>最小限のダウンタイムでデータベースのリソースを動的にスケーリングする
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database と SQL Managed Instance では、最小限の[ダウンタイム](https://azure.microsoft.com/support/legal/sla/sql-database)でデータベースにリソースを動的に追加できます。ただし、データベースへの接続が短時間失われる切り替え期間があります。これは、再試行ロジックを使用することで軽減できます。

## <a name="overview"></a>概要

少数のデバイスや顧客から始まったアプリの需要が何百万という数にまで膨れ上がると、Azure SQL Database と SQL Managed Instance は、最小限のダウンタイムですばやくスケーリングされます。 スケーラビリティは、必要なときにサービスにリソースを動的に追加することができる、サービスとしてのプラットフォーム (PaaS) の最も重要な特性の 1 つです。 Azure SQL Database では、データベースに割り当てられたリソース (CPU パワー、メモリ、IO スループット、ストレージ) を簡単に変更することができます。

インデックス付けやクエリ書き換えの方法では修正できない、アプリケーション使用量増加によるパフォーマンスの問題を軽減することができます。 リソースを追加すると、データベースが現在のリソース制限に達し、受信ワークロードの処理にいっそうのパワーが必要なときに、迅速に対応できます。 Azure SQL Database では、必要のないリソースをスケールダウンしてコストを抑えることもできます。

ハードウェアの購入や、基になるインフラストラクチャの変更について、心配する必要はありません。 データベースのスケーリングは、Azure portal でスライダーを使って簡単に実行できます。

![データベースのパフォーマンスをスケーリングする](./media/scale-resources/scale-performance.svg)

Azure SQL Database には、[DTU ベースの購入モデル](service-tiers-dtu.md)と[仮想コアベースの購入モデル](service-tiers-vcore.md)が用意されていますが、Azure SQL Managed Instance には[仮想コアベースの購入モデル](service-tiers-vcore.md)のみが用意されています。 

- [DTU ベースの購入モデル](service-tiers-dtu.md)には、データベースの軽量ワークロードから重量ワークロードまでをサポートする、計算リソース、メモリ リソース、および I/O リソースの組み合わせがそれぞれ異なる、Basic、Standard、Premium の 3 つのサービス レベルがあります。 各レベルにおけるパフォーマンス レベルでは、これらのリソースのさまざまな組み合わせが提供され、ストレージ リソースを追加することができます。
- [仮想コアベースの購入モデル](service-tiers-vcore.md)では、仮想コアの数、メモリの量、およびストレージの容量と速度を選択できます。 この購入モデルは 3 つのサービス レベルを提供します:General Purpose、Business Critical、および Hyperscale。

最初に Basic、Standard、または General Purpose サービス レベルで月額の安い小さな単一データベースにアプリをビルドし、後でいつでもソリューションのニーズに合わせて手動またはプログラムでサービス レベルを Premium または Business Critical サービス レベルに変更できます。 アプリにも顧客にもダウンタイムを発生させずにパフォーマンスを調整することができます。 動的なスケーラビリティにより、データベースは変化の激しいリソース要件に透過的に対処することができ、必要なときに必要な分のリソースにのみ課金されます。

> [!NOTE]
> 動的スケーラビリティは自動スケールとは異なります。 自動スケールは、基準に基づいてサービスが自動的にスケールされるのに対し、動的スケーラビリティでは、最小限のダウンタイムで手動スケールすることができます。

Azure SQL Database の単一データベースは、手動の動的スケーラビリティをサポートしていますが、自動スケーリングはサポートしていません。 *自動* 操作を増やすには、エラスティック プールの使用を検討してください。エラスティック プールを使用すると、データベースが個々のデータベースのニーズに基づいてプール内のリソースを共有できます。
ただし、Azure SQL Database の単一データベースのスケーラビリティを自動化できるスクリプトがあります。 例については、「[PowerShell を使用して単一の SQL データベースを監視およびスケーリングする](scripts/monitor-and-scale-database-powershell.md)」を参照してください。

[DTU サービス層](service-tiers-dtu.md)または[仮想コアの特性](resource-limits-vcore-single-databases.md)はいつでも変更することが可能で、アプリケーションのダウンタイムも最小限に留められます (通常、平均で 4 秒未満)。 特に使用パターンが比較的予測可能である場合、多くのビジネスとアプリについては、データベースを作成し、要求に応じてパフォーマンスを調整する能力は十分です。 しかし、使用パターンが予測できない場合、コストおよびビジネス モデルを管理するのが難しくなる可能性があります。 このシナリオでは、プール内の複数のデータベース間で共有される特定の数の DTU でエラスティック プールを使用します。

![SQL Database の概要:階層とレベル別の 1 つのデータベースの DTU](./media/scale-resources/single_db_dtus.png)

Azure SQL Database には、データベースを動的にスケーリングする機能が用意されています。

- [単一データベース](single-database-scale.md)では、[DTU](resource-limits-dtu-single-databases.md) モデルまたは[仮想コア](resource-limits-vcore-single-databases.md) モデルを使って、各データベースに割り当てられる最大リソース量を定義できます。
- [エラスティック プール](elastic-pool-scale.md)を使用すると、プール内のデータベースのグループごとの最大リソース制限を定義できます。

Azure SQL Managed Instance を使用すると、次のようにスケーリングすることもできます。 

- [SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md) では[仮想コア](../managed-instance/sql-managed-instance-paas-overview.md#vcore-based-purchasing-model) モードが使用され、インスタンスに割り当てられる最大 CPU コア数と最大ストレージ量を定義できます。 マネージド インスタンス内のすべてのデータベースによって、インスタンスに割り当てられたリソースが共有されます。

いずれかの種類でスケールアップまたはスケールダウンのアクションを開始すると、データベース エンジン プロセスが再起動され、必要に応じて別の仮想マシンに移動されます。 新しい仮想マシンへのデータベース エンジン プロセスの移動は、プロセスの実行中に既存の Azure SQL Database サービスの使用を継続できる **オンライン プロセス** です。 ターゲット データベース エンジンが完全に初期化され、クエリを処理する準備が整うと、接続は[ソースからターゲット データベース エンジンに切り替えられます](single-database-scale.md#impact)。

> [!NOTE]
> スケールアップ/スケールダウン処理が完了するとき、短時間の接続の中断が予想されます。 [標準の一時的なエラーのための再試行ロジック](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors)を実装している場合、フェールオーバーを意識することはありません。

## <a name="alternative-scale-methods"></a>別のスケーリング方法

リソースのスケーリングは、データベースまたはアプリケーション コードを変更することなく、データベースのパフォーマンスを向上させることができる、最も簡単で効果的な方法です。 場合によっては、最高のサービス レベル、コンピューティング サイズ、パフォーマンス最適化であっても、コスト効果の高い方法でワークロードを正常に処理できないことがあります。 そのような場合は、次の追加オプションでデータベースの規模を変更できます。

- [読み取りスケールアウト](read-scale-out.md)は、データの 1 つの読み取り専用レプリカを取得している場合に利用できる機能であり、レポートなどの負荷が高い読み取り専用クエリを実行できます。 読み取り専用レプリカでは、プライマリ データベースのリソース使用量に影響を与えることなく、読み取り専用ワークロードが処理されます。
- [データベース シャーディング](elastic-scale-introduction.md)は、データを複数のデータベースに分割してそれらを個別にスケーリングできる一連のテクニックです。

## <a name="next-steps"></a>次のステップ

- データベース コードの変更によってデータベースのパフォーマンスを向上させる方法の詳細については、「[パフォーマンスに関する推奨事項の検索と適用](database-advisor-find-recommendations-portal.md)」をご覧ください。
- 組み込みのデータベース インテリジェンスを使ってデータベースを最適化する方法については、「[自動チューニング](automatic-tuning-overview.md)」をご覧ください。
- Azure SQL Database での読み取りスケールアウトについては、[読み取り専用レプリカを使用して読み取り専用クエリ ワークロードを負荷分散する](read-scale-out.md)方法を参照してください。
- データベース シャーディングの詳細については、「[Azure SQL Database によるスケール アウト](elastic-scale-introduction.md)」をご覧ください。
