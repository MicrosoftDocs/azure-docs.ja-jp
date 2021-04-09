---
title: Business Critical サービス レベル
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Azure SQL Database と Azure SQL Managed Instance の Business Critical サービス レベルについて説明します。
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 12/04/2018
ms.openlocfilehash: 830ecc44d0def13e51cb06704bef429bb8860cd6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92780225"
---
# <a name="business-critical-tier---azure-sql-database-and-azure-sql-managed-instance"></a>Business Critical レベル - Azure SQL Database と Azure SQL Managed Instance 
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

> [!NOTE]
> Business Critical レベルは DTU 購入モデルで Premium と呼ばれています。 仮想コアベースの購入モデルと DTU ベースの購入モデルとの比較については、[Azure SQL Database の購入モデルとリソース](purchasing-models.md)に関する記事をご覧ください。

Azure SQL Database と Azure SQL Managed Instance はいずれも、インフラストラクチャに障害が発生した場合でも 99.99% の可用性を確保するために、クラウド環境に合わせて調整された SQL Server データベース エンジン アーキテクチャに基づいています。 使用される 3 つのアーキテクチャ モデルがあります。
- General Purpose/Standard 
- Business Critical/Premium
- ハイパースケール

Premium/Business Critical サービス レベル モデルは、データベース エンジン プロセスのクラスターに基づいています。 このアーキテクチャ モデルは、常に使用可能なデータベース エンジン ノードのクォーラムが存在し、保守作業中でもワークロードに対するパフォーマンスの影響が最小限であるというファクトに依存しています。 ハイパースケール サービス レベルは、現在、(SQL Managed Instance ではなく) Azure SQL Database でのみ使用でき、非常にスケーラブルなストレージおよびコンピューティング パフォーマンス レベルであり、Azure のアーキテクチャを利用して、General Purpose および Business Critical サービス レベルの制限を大きく超えて、Azure SQL Database のデータベース用のストレージおよびコンピューティング リソースをスケールアウトします。

Azure では、基盤となるオペレーティング システム、ドライバー、および SQL Server データベース エンジンに対して、アップグレードとパッチ適用が透過的に実行されます。そのため、エンド ユーザーのダウンタイムは最小限に抑えられます。 

Premium 可用性は、Premium および Business Critical サービス レベルで有効になり、進行中のメンテナンス操作によるパフォーマンスへの影響を許容できない集中的なワークロードのために設計されています。

コンピューティングとストレージは、Premium モデルの単一ノードで統合されています。 このアーキテクチャ モデルでの高可用性は、SQL Server [Always On 可用性グループ](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)と同様のテクノロジを使用して、4 ノード クラスター内にデプロイされた計算 (SQL Server データベース エンジン プロセス) とストレージ (ローカルに接続された SSD) のレプリケーションによって実現されます。

![データベース エンジン ノードのクラスター](./media/service-tier-business-critical/business-critical-service-tier.png)

SQL Server データベース エンジン プロセスと、基礎となる .mdf/.ldf ファイルは、両方とも同じノード上に配置されます。このノードには、SSD ストレージが接続されているため、ワークロードに対する待ち時間が短くなります。 高可用性は、SQL Server [Always On 可用性グループ](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)と同様のテクノロジを使用して実装されます。 すべてのデータベースは、データベース ノードのクラスターになっています。このクラスターの中で、1 つのプライマリ データベースは、顧客のワークロード用にアクセスすることができ、3 つのセカンダリ プロセスは、データのコピーが格納されています。 プライマリ ノードは、変更内容を絶えずセカンダリ ノードにプッシュしています。これは、何らかの原因でプライマリ ノードが失敗した場合でも、セカンダリ レプリカでデータを確実に使用できるようにするためです。 フェールオーバーは、SQL Server データベース エンジンで処理されます。つまり、あるセカンダリ レプリカがプライマリ ノードになると、クラスター内のノード数を十分に確保するために、新しいセカンダリ レプリカが作成されます。 ワークロードは、新しいプライマリ ノードに自動的にリダイレクトされます。

さらに、Business Critical クラスターには、プライマリ ワークロードのパフォーマンスに影響を与えない読み取り専用クエリ (レポートなど) を実行するために使用できる無料の組み込みの読み取り専用ノードを提供する、組み込みの[読み取りスケールアウト](read-scale-out.md)機能があります。

## <a name="when-to-choose-this-service-tier"></a>このサービス レベルを選択する場合

Business Critical サービス レベルの対象となるアプリケーションは、基盤となる SSD ストレージからの応答の待機時間が短い (平均 1 - 2 ミリ秒)、基盤となるインフラストラクチャに障害が発生した場合に迅速に復旧するという要件があるか、レポート、分析、および読み取り専用のクエリをプライマリ データベースの無料で読み取り可能なセカンダリ レプリカにオフロードする必要があります。

General Purpose レベルではなく、Business Critical サービス レベルを選択すべき主な理由は、次のとおりです。
-   **短いI/O 待機時間の要件**。ストレージ レイヤーから迅速な応答が必要なワークロード (平均 1 - 2 ミリ秒) では、Business Critical レベルを使用する必要があります。 
-   **アプリケーションとデータベース間に頻繁に通信がある**。 アプリケーション レイヤーのキャッシュまたは[要求のバッチ処理](../performance-improve-use-batching.md)を利用できない、迅速に処理する必要のある SQL クエリを多数送信する必要のあるアプリケーションには、Business Critical レベルが必要です。
-   **多数の更新**。挿入、更新および削除操作により、メモリ内のデータ ページが変更された場合 (ダーティ ページ)、これらは、`CHECKPOINT` 操作でデータ ファイルに保存される必要があります。 ダーティ ページが多いことにより、データベース エンジンのプロセスがクラッシュしたり、データベースがフェールオーバーする場合、General Purpose レベルの復旧に時間がかかる場合があります。 メモリ内で変更が多く発生するワークロードでは、Business Critical レベルを使用します。 
-   **データが変更する長期実行されるトランザクション**。 長期間開かれているトランザクションでは、ログ ファイルの切り捨てが行われません。その結果、ログ サイズと[仮想ログ ファイル (VLF)](/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide#physical_arch) 数が増加する可能性があります。 VLF が多数ある場合、フェールオーバー後のデータベースの復旧が遅くなる場合があります。
-   **レポートと分析クエリを使用したワークロード**。これにより、無料の読み取り専用のセカンダリ レプリカにリダイレクトされる可能性があります。
- **障害からのより高い回復性とより早い復旧**。 システムで障害が発生した場合、プライマリ インスタンス上のデータベースは無効になり、セカンダリ レプリカの 1 つが、クエリを処理できる新しい読み取りまたは書き込みプライマリ データベースに直ちになります。 データベース エンジンは、ログ ファイルを分析し、トランザクションを再実行し、メモリ バッファーのすべてのデータを読み込む必要はありません。
- **データの破損からの高度な保護**。 Business Critical レベルでは、事業継続のためにデータベース レプリカがバックグラウンドで活用されます。また、サービスでは、SQL Server データベースの[ミラーリングおよび可用性グループ](/sql/sql-server/failover-clusters/automatic-page-repair-availability-groups-database-mirroring)で使用されるものと同じテクノロジであるページの自動修復が活用されます。 データの整合性の問題が原因で、レプリカがページを読み取ることができない場合、読み取り不可能なページは、データの損失や顧客のダウンタイムなしで、別のレプリカから新しいコピーが取得され、置き換えられます。 この機能は、データベースに geo セカンダリ レプリカがある場合、General Purpose レベルに適用されます。
- **高可用性**。Multi-AZ 構成の Business Critical レベルでは、General Purpose レベルの 99.99% と比較して、99.995% の高可用性が保証されます。
- **高速 geo リカバリー**。geo レプリケーションが構成された Business Critical レベルには、展開されている時間、5 秒の回復ポイントの目標 (RPO) と 30 秒の回復時刻の目標 (RTO) が 100% 保証されています。

## <a name="next-steps"></a>次のステップ

- [SQL Managed Instance](../managed-instance/resource-limits.md#service-tier-characteristics) の Business Critical レベル、[仮想コア モデル](resource-limits-vcore-single-databases.md#business-critical---provisioned-compute---gen4)または [DTU モデル](resource-limits-dtu-single-databases.md#premium-service-tier)の単一データベース、[仮想コア モデル](resource-limits-vcore-elastic-pools.md#business-critical---provisioned-compute---gen4)および [DTU モデル](resource-limits-dtu-elastic-pools.md#premium-elastic-pool-limits)のエラスティック プールのリソース特性 (コア数、I/O、メモリ) を確認します。
- [General Purpose](service-tier-general-purpose.md) レベルと [Hyperscale](service-tier-hyperscale.md) レベルについて学びます。
- [Service Fabric](../../service-fabric/service-fabric-overview.md) について学びます。
- 高可用性およびディザスター リカバリーのためのその他のオプションについては、[ビジネス継続性](business-continuity-high-availability-disaster-recover-hadr-overview.md)に関するページを参照してください。