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
ms.openlocfilehash: 48cde2f96083779bdeb13ba5f39b68c18b395045
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/15/2019
ms.locfileid: "69515366"
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

## <a name="next-steps"></a>次の手順

- [Managed Instance](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) の Business Critical レベル、[仮想コア モデル](sql-database-vcore-resource-limits-single-databases.md#business-critical-service-tier-for-provisioned-compute)または [DTU モデル](sql-database-dtu-resource-limits-single-databases.md#premium-service-tier)の単一データベース、[仮想コア モデル](sql-database-vcore-resource-limits-elastic-pools.md#business-critical-service-tier-storage-sizes-and-compute-sizes)および [DTU モデル](sql-database-dtu-resource-limits-elastic-pools.md#premium-elastic-pool-limits)のエラスティック プールのリソース特性 (コア数、IO、メモリ) を確認します。
- [General Purpose](sql-database-service-tier-general-purpose.md) レベルと [Hyperscale](sql-database-service-tier-hyperscale.md) レベルについて学びます。
- [Service Fabric](../service-fabric/service-fabric-overview.md) について学びます。
- 高可用性およびディザスター リカバリーのためのその他のオプションについては、[ビジネス継続性](sql-database-business-continuity.md)に関するページを参照してください。
