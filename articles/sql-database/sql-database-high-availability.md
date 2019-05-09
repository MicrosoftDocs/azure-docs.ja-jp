---
title: 高可用性 - Azure SQL Database サービス | Microsoft Docs
description: Microsoft Azure SQL Database サービスの高可用性機能および特色について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlrab, sashan
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: fd6e383c2631a47daa7bf469c5bec59959453252
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2019
ms.locfileid: "59616851"
---
# <a name="high-availability-and-azure-sql-database"></a>高可用性と Microsoft Azure SQL Database

Azure SQL Database は、稼働率 99.99% が保証されている、メンテナンスやダウンタイムの心配が不要な、サービスとしての高可用性データベース プラットフォームです。 これは、Azure クラウドでホストされる、完全に管理された SQL Server データベース エンジン プロセスです。SQL Server データベースは、ワークロードに影響を与えることなく、常にアップグレードされ、パッチが適用されます。 インスタンスにパッチが適用されるか、またはインスタンスがフェールオーバーしたとき、アプリで[再試行ロジックを使用](sql-database-develop-overview.md#resiliency)していれば、一般にダウンタイムは認識されません。 フェールオーバーの完了までの時間が 60 秒を超える場合は、サポート ケースを開いてください。 Azure SQL Database は、クリティカルな状況であっても迅速な復旧が可能であるため、データが常に使用可能であることが保証されます。

すべての Azure SQL Database は Azure プラットフォームによって完全に管理され、データ消失ゼロおよび高いデータ可用性 (%) を実現しています。 基になるハードウェア、ソフトウェア、ネットワークの障害リスクへの対応や、パッチの適用、バックアップ、レプリケーション、障害検出、バグ修正、フェールオーバー、データベースのアップグレードなど、各種メンテナンス タスクは、Azure によって自動的に処理されます。 SQL Server のエンジニアは、定評のあるベスト プラクティスを導入しており、すべてのメンテナンス操作は、データベース運用期間の 0.01% 未満で完了します。 このアーキテクチャは、コミットされたデータが消失せず、メンテナンス操作を実行してもワークロードに影響が及ばないように設計されています。 データベースのアップグレードやメンテナンスを行うときでも、ワークロードの停止が必要なメンテナンス期間やダウンタイムは発生しません。 Azure SQL Database には、高可用性が組み込まれています。そのため、データベースがソフトウェア アーキテクチャの単一障害点にならないことが保証されています。

Azure SQL Database は、インフラストラクチャに障害が発生した場合でも 99.99% の可用性を確保するために、クラウド環境に合わせて調整された SQL Server データベース エンジン アーキテクチャに基づいています。 Azure SQL Database には、2 つの高可用性アーキテクチャ モデルがあります (いずれも 99.99% の可用性を確保しています)。

- 計算とストレージの分離に基づく Standard/汎用モデル サービス レベル。 このアーキテクチャ モデルは、ストレージ層の高可用性と信頼性に依存していますが、保守作業中はパフォーマンスが低下する可能性があります。
- データベース エンジン プロセスのクラスターに基づく Premium/Business Critical サービス レベル モデル。 このアーキテクチャ モデルは、常に使用可能なデータベース エンジン ノードのクォーラムが存在し、保守作業中でもワークロードに対するパフォーマンスの影響が最小限であるというファクトに依存しています。

Azure では、基盤となるオペレーティング システム、ドライバー、および SQL Server Database エンジンに対して、アップグレードとパッチ適用が透過的に実行されます。そのため、エンド ユーザーのダウンタイムは最小限に抑えられます。 Azure SQL データベースは、最新バージョンの SQL Server データベース エンジンおよび Windows OS 上で実行されています。また、アップグレードが、ほぼ気づかれることなく、継続的に実行されています。

## <a name="basic-standard-and-general-purpose-service-tier-availability"></a>Basic、Standard、および General Purpose サービス レベルの可用性

Standard 可用性は、Basic、Standard、および General Purpose サービス レベルで適用される 99.99% の SLA を指します。 このアーキテクチャ モデルの高可用性は、計算層とストレージ層の分離と、ストレージ層でのデータのレプリケーションによって実現されます。

次の図は、計算レイヤーとストレージ レイヤーが分離されている Standard アーキテクチャ モデルの 4 ノードを示しています。

![計算とストレージの分離](media/sql-database-managed-instance/general-purpose-service-tier.png)

Standard 可用性モデルには、次の 2 つのレイヤーがあります。

- ステートレス計算レイヤー。`sqlserver.exe` プロセスを実行しており、一時的なデータとキャッシュ データのみが含まれています (プラン キャッシュ、バッファー プール、列のストア プールなど)。 このステートレス SQL Server ノードは、プロセスの初期化、ノードの正常性の制御、および他の場所へのフェールオーバーを必要に応じて実行する Azure Service Fabric によって操作されます。
- ステートフル データ レイヤー。データベース ファイル (.mdf/.ldf) は Azure Blob Storage に保存されています。 Azure Blob Storage では、データベース ファイル内にあるレコードのデータが消失しないことが保証されています。 Azure Blob ストレージには、データの可用性と冗長性が組み込まれています。そのため、たとえ SQL Server プロセスがクラッシュしても、ログ ファイルのレコードやデータ ファイルのページはすべて維持されます。

データベース エンジンまたはオペレーティング システムがアップグレードされた場合、基となるインフラストラクチャの一部で障害が発生した場合、または Sql Server プロセスで重大な問題が検出された場合、Azure Service Fabric は、ステートレス SQL Server プロセスを別のステートレス計算ノードに移行します。 フェールオーバー時間を最小限に抑えるために、フェールオーバーが発生した場合に新しい計算サービスの実行を待機している一連のスペア ノードがあります。 Azure BLOB ストレージのデータは影響を受けず、データ/ログ ファイルは、新しく初期化された SQL Server プロセスにアタッチされます。 このプロセスは、99.99% の可用性を保証していますが、移行時間や、新しい SQL Server ノードの起動にコールド キャッシュを使用することが原因で、実行中の大きなワークロードに対しては、パフォーマンス上の影響が若干生じる場合があります。

## <a name="premium-and-business-critical-service-tier-availability"></a>Premium および Business Critical サービス レベルの可用性

Premium 可用性は、Azure SQL Database の Premium および Business Critical サービス レベルで有効になり、進行中のメンテナンス操作によるパフォーマンスへの影響を許容できない集中的なワークロードのために設計されています。

Premium モデルでは、Azure SQL Database が計算およびストレージを単一のノード上に統合しています。 このアーキテクチャ モデルでの高可用性は、SQL Server [Always On 可用性グループ](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)と同様のテクノロジを使用して、4 ノード クラスター内にデプロイされた計算 (SQL Server データベース エンジン プロセス) とストレージ (ローカルに接続された SSD) のレプリケーションによって実現されます。

![データベース エンジン ノードのクラスター](media/sql-database-managed-instance/business-critical-service-tier.png)

SQL データベース エンジン プロセスと基礎となる mdf/ldf ファイルはどちらも、ワークロードの待ち時間を短縮するローカルに接続された SSD ストレージを備えた同じノード上に配置されます。 高可用性は、SQL Server [Always On 可用性グループ](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)と同様のテクノロジを使用して実装されます。 すべてのデータベースは、データベース ノードのクラスターになっています。このクラスターの中で、1 つのプライマリ データベースは、顧客のワークロード用にアクセスすることができ、3 つのセカンダリ プロセスは、データのコピーが格納されています。 プライマリ ノードは、変更内容を絶えずセカンダリ ノードにプッシュしています。これは、何らかの原因でプライマリ ノードがクラッシュした場合でも、セカンダリ レプリカでデータを確実に使用できるようにするためです。 フェールオーバーは、Azure Service Fabric で処理されます。つまり、あるセカンダリ レプリカがプライマリ ノードになると、クラスター内のノード数を十分に確保するために、新しいセカンダリ レプリカが作成されます。 ワークロードは、新しいプライマリ ノードに自動的にリダイレクトされます。

さらに、Business Critical クラスターには、プライマリ ワークロードのパフォーマンスに影響を与えない読み取り専用クエリ (レポートなど) を実行するために使用できる無料の組み込みの読み取り専用ノードを提供する、組み込みの[読み取りスケールアウト](sql-database-read-scale-out.md)機能があります。

## <a name="zone-redundant-configuration"></a>ゾーン冗長の構成

既定では、ローカル ストレージ構成のクォーラムセット レプリカが同じデータセンターに作成されます。 [Azure 可用性ゾーン](../availability-zones/az-overview.md)の導入により、同じリージョンのさまざまな可用性ゾーンに対するさまざまなレプリカをクォーラムセットに配置することができます。 単一障害点をなくすため、制御リングも複数のゾーンで 3 つのゲートウェイ リング (GW) として複製できます。 特定のゲートウェイ リングへのルーティングは [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) (ATM) によって制御されます。 ゾーン冗長構成では追加のデータベース冗長性は発生しないため、Premium または Business Critical サービス レベルでの可用性ゾーンを追加料金なしで使用できます。 ゾーン冗長データベースを選択することで、アプリケーション ロジックをまったく変更せずに、データセンターの壊滅的な障害を含む大規模な障害から Premium または Business Critical データベースが回復できるようになります。 また、既存の Premium または Business Critical データベース、あるいはプールをゾーン冗長構成に変換することもできます。

ゾーン冗長クォーラムセットは、離れた距離に位置するさまざまなデータセンターにレプリカがあるため、ネットワーク待機時間が長くなるとコミット時間が延長され、一部の OLTP ワークロードのパフォーマンスに影響する可能性があります。 いつでもゾーン冗長設定を無効にして単一ゾーン構成に戻ることができます。 このプロセスはデータ操作のサイズであり、通常のサービス レベル更新プログラムと似ています。 プロセスの最後に、データベースまたはプールがゾーン冗長リングから単一ゾーン リングに (または逆方向に) 移行されます。

> [!IMPORTANT]
> ゾーン冗長データベースとエラスティック プールは、現時点では Premium サービス レベルでのみサポートされています。 既定では、バックアップや監査レコードは RA-GRS ストレージに格納されるため、ゾーン全体にわたる停止の場合、自動的には使用できない可能性があります。 

ゾーン冗長による高可用性アーキテクチャを、次の図に示します。

![高可用性アーキテクチャのゾーン冗長](./media/sql-database-high-availability/high-availability-architecture-zone-redundant.png)

## <a name="accelerated-database-recovery-adr"></a>高速データベース復旧 (ADR)

[高速データベース復旧 (ADR)](sql-database-accelerated-database-recovery.md) は、SQL データベース エンジンの復旧プロセスを再設計することで、実行時間の長いトランザクションがある場合などにデータベースの可用性を大幅に向上させる、新しい SQL データベース エンジン機能です。 ADR は現在、単一データベース、エラスティック プール、および Azure SQL Data Warehouse で使用できます。

## <a name="conclusion"></a>まとめ

Azure SQL Database は、Azure プラットフォームと緊密に統合されており、障害の検出と復旧に Service Fabric を、データ保護に Azure BLOB ストレージを、フォールト トレランスを高めるために可用性ゾーンを活用します。 同時に、Azure SQL データベースは、レプリケーションとフェールオーバーのために、SQL Server 既成製品からの AlwaysOn 可用性グループ テクノロジをフル活用します。 これらのテクノロジを組み合わせることにより、アプリケーションは混合ストレージ モデルを最大限に活用して、高要件の SLA にも対応できます。

## <a name="next-steps"></a>次の手順

- [Azure 可用性ゾーン](../availability-zones/az-overview.md)の詳細
- [Service Fabric](../service-fabric/service-fabric-overview.md) の詳細
- [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) の詳細
- 高可用性およびディザスター リカバリーのためのその他のオプションについては、[ビジネス継続性](sql-database-business-continuity.md)に関するページを参照してください。
