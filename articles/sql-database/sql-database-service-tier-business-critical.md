---
title: Business Critical レベル - Azure SQL Database サービス | Microsoft Docs
description: Azure SQL Database Business Critical レベルについて説明します
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 12/04/2018
ms.openlocfilehash: 9e398fd7d370d30fac87035b27a218834b4fab22
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2019
ms.locfileid: "70899715"
---
# <a name="business-critical-tier---azure-sql-database"></a>Business Critical レベル - Azure SQL Database

> [!NOTE]
> Business Critical レベルは DTU 購入モデルで Premium と呼ばれています。 仮想コアベースの購入モデルと DTU ベースの購入モデルとの比較については、[Azure SQL Database の購入モデルとリソース](sql-database-purchase-models.md)に関する記事をご覧ください。

Azure SQL Database は、インフラストラクチャに障害が発生した場合でも 99.99% の可用性を確保するために、クラウド環境に合わせて調整された SQL Server データベース エンジン アーキテクチャに基づいています。 Azure SQL Database で使用されているアーキテクチャ モデルは 3 つあります。
- General Purpose/Standard 
- Business Critical/Premium
- ハイパースケール

Premium/Business Critical サービス レベル モデルは、データベース エンジン プロセスのクラスターに基づいています。 このアーキテクチャ モデルは、常に使用可能なデータベース エンジン ノードのクォーラムが存在し、保守作業中でもワークロードに対するパフォーマンスの影響が最小限であるというファクトに依存しています。

Azure では、基盤となるオペレーティング システム、ドライバー、および SQL Server Database エンジンに対して、アップグレードとパッチ適用が透過的に実行されます。そのため、エンド ユーザーのダウンタイムは最小限に抑えられます。 

Premium 可用性は、Azure SQL Database の Premium および Business Critical サービス レベルで有効になり、進行中のメンテナンス操作によるパフォーマンスへの影響を許容できない集中的なワークロードのために設計されています。

Premium モデルでは、Azure SQL データベースが計算およびストレージを単一のノード上に統合しています。 このアーキテクチャ モデルでの高可用性は、SQL Server [Always On 可用性グループ](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)と同様のテクノロジを使用して、4 ノード クラスター内にデプロイされた計算 (SQL Server データベース エンジン プロセス) とストレージ (ローカルに接続された SSD) のレプリケーションによって実現されます。

![データベース エンジン ノードのクラスター](media/sql-database-managed-instance/business-critical-service-tier.png)

SQL データベース エンジン プロセスと基礎となる mdf/ldf ファイルはどちらも、ワークロードの待ち時間を短縮するローカルに接続された SSD ストレージを備えた同じノード上に配置されます。 高可用性は、SQL Server [Always On 可用性グループ](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)と同様のテクノロジを使用して実装されます。 すべてのデータベースは、データベース ノードのクラスターになっています。このクラスターの中で、1 つのプライマリ データベースは、顧客のワークロード用にアクセスすることができ、3 つのセカンダリ プロセスは、データのコピーが格納されています。 プライマリ ノードは、変更内容を絶えずセカンダリ ノードにプッシュしています。これは、何らかの原因でプライマリ ノードがクラッシュした場合でも、セカンダリ レプリカでデータを確実に使用できるようにするためです。 フェールオーバーは、SQL Server データベース エンジンで処理されます。つまり、あるセカンダリ レプリカがプライマリ ノードになると、クラスター内のノード数を十分に確保するために、新しいセカンダリ レプリカが作成されます。 ワークロードは、新しいプライマリ ノードに自動的にリダイレクトされます。

さらに、Business Critical クラスターには、プライマリ ワークロードのパフォーマンスに影響を与えない読み取り専用クエリ (レポートなど) を実行するために使用できる無料の組み込みの読み取り専用ノードを提供する、組み込みの[読み取りスケールアウト](sql-database-read-scale-out.md)機能があります。

## <a name="when-to-choose-this-service-tier"></a>このサービス レベルを選択する場合

Business Critical サービス レベルの対象となるアプリケーションは、基盤となる SSD ストレージからの応答の待機時間が短い (平均 1 - 2 ミリ秒)、基盤となるインフラストラクチャに障害が発生した場合に迅速に復旧するという要件があるか、レポート、分析、および読み取り専用のクエリをプライマリ データベースの無料で読み取り可能なセカンダリ レプリカにオフロードする必要があります。

General Purpose レベルではなく、Business Critical サービス レベルを選択すべき主な理由は、次のとおりです。
-   IO に低待機時間が必要。ストレージ レイヤーから迅速な応答が必要なワークロード (平均 1 - 2 ミリ秒) では、Business Critical レベルを使用する必要があります。 
-   アプリケーションとデータベース間に頻繁に通信がある。 アプリケーション レイヤーのキャッシュまたは[要求のバッチ処理](sql-database-use-batching-to-improve-performance.md)を利用できない、迅速に処理する必要のある SQL クエリを多数送信する必要のあるアプリケーションには、Business Critical レベルが必要です。
-   挿入、更新および削除操作などの多数の更新により、メモリ内のデータ ページが変更された場合 (ダーティ ページ)、これらは、`CHECKPOINT` 操作でデータ ファイルに保存される必要があります。 ダーティ ページが多いことにより、データベース エンジンのプロセスがクラッシュしたり、データベースがフェールオーバーする場合、General Purpose レベルの復旧に時間がかかる場合があります。 メモリ内で変更が多く発生するワークロードでは、Business Critical レベルを使用します。 
-   データが変更する長期実行されるトランザクション。 長期開かれているトランザクションでは、ログ サイズと[仮想ログ ファイル (VLF)](https://docs.microsoft.com/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide#physical_arch) 数が増加する可能性があるログ ファイルの切り捨てが行われません。 VLF が多数ある場合、フェイルオーバー後のデータベースの復旧が遅くなる場合があります。
-   無料の読み取り専用のセカンダリ レプリカにリダイレクトされる可能性のあるレポートと分析クエリを使用したワークロード。
- 障害からのより高い回復性とより早い復旧。 システムで障害が発生した場合、プライマリ インスタンス上のデータベースは無効になり、セカンダリ レプリカの 1 つが、クエリを処理できる新しい読み取り/書き込みプライマリ データベースに直ちになります。 データベース エンジンは、ログ ファイルを分析し、トランザクションを再実行し、メモリ バッファーのすべてのデータを読み込む必要はありません。
- データの破損の高度な保護。Business Critical レベルでは、ビジネス継続性のためにデータベース レプリカがバックグラウンドで活用されます。また、サービスでは、SQL Server データベースの[ミラーリングおよび可用性グループ](https://docs.microsoft.com/sql/sql-server/failover-clusters/automatic-page-repair-availability-groups-database-mirroring)で使用されるものと同じテクノロジであるページの自動修復が活用されます。 データの整合性の問題が原因で、レプリカがページを読み取ることができない場合、読み取り不可能なページは、データの損失や顧客のダウンタイムなしで、別のレプリカから新しいコピーが取得され、置き換えられます。 この機能は、データベースに geo セカンダリ レプリカがある場合、General Purpose レベルに適用されます。
- 高可用性。Multi-AZ 構成の Business Critical レベルでは、General Purpose レベルの 99.99% と比較して、99.995% の高可用性が保証されます。
- 高速 geo リカバリー。geo レプリケーションが構成された Business Critical レベルには、展開されている時間、5 秒の回復ポイントの目標 (RPO) と 30 秒の回復時刻の目標 (RTO) が 100% 保証されています。

## <a name="next-steps"></a>次の手順

- [Managed Instance](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) の Business Critical レベル、[仮想コア モデル](sql-database-vcore-resource-limits-single-databases.md#business-critical-service-tier-for-provisioned-compute)または [DTU モデル](sql-database-dtu-resource-limits-single-databases.md#premium-service-tier)の単一データベース、[仮想コア モデル](sql-database-vcore-resource-limits-elastic-pools.md#business-critical-service-tier-storage-sizes-and-compute-sizes)および [DTU モデル](sql-database-dtu-resource-limits-elastic-pools.md#premium-elastic-pool-limits)のエラスティック プールのリソース特性 (コア数、IO、メモリ) を確認します。
- [General Purpose](sql-database-service-tier-general-purpose.md) レベルと [Hyperscale](sql-database-service-tier-hyperscale.md) レベルについて学びます。
- [Service Fabric](../service-fabric/service-fabric-overview.md) について学びます。
- 高可用性およびディザスター リカバリーのためのその他のオプションについては、[ビジネス継続性](sql-database-business-continuity.md)に関するページを参照してください。
