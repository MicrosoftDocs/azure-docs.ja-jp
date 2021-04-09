---
title: 高可用性
titleSuffix: Azure SQL Database and SQL Managed Instance
description: Azure SQL Database と SQL Managed Instance サービスの高可用性機能および特色について説明します。
services: sql-database
ms.service: sql-db-mi
ms.subservice: high-availability
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: emlisa
ms.author: emlisa
ms.reviewer: sstein, emlisa
ms.date: 10/28/2020
ms.openlocfilehash: 1c210eab0332d01fc6514edc790d729172ed2174
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104889061"
---
# <a name="high-availability-for-azure-sql-database-and-sql-managed-instance"></a>Azure SQL Database と SQL Managed Instance の高可用性
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database と SQL Managed Instance での高可用性アーキテクチャの目的は、メンテナンス操作や障害の影響を心配せずに、データベースの稼働および実行の時間が 99.99% 以上になるように保証することです (さまざまなレベルに固有の SLA の詳細については、[Azure SQL Database と SQL Managed Instance の SLA](https://azure.microsoft.com/support/legal/sla/sql-database/) に関するページを参照してください)。 Azure では、パッチ適用、バックアップ、Windows および Azure SQL のアップグレードなどの重要なサービス タスクに加えて、基本となるハードウェア、ソフトウェア、またはネットワークのエラーなどの計画外のイベントを自動的に処理します。  Azure SQL Database の基本となるデータベースがパッチを適用されるか、またはフェールオーバーした場合、お使いのアプリで[再試行ロジックを使用](develop-overview.md#resiliency)していれば、ダウンタイムは認識されません。 SQL Database と SQL Managed Instance は、クリティカルな状況であっても迅速な復旧が可能であるため、データが常に使用可能であることが保証されます。

高可用性ソリューションは、コミットされたデータが障害によって失われないこと、メンテナンス操作がワークロードに影響を及ぼさないこと、また、データベースがソフトウェア アーキテクチャでの単一障害点にならないことを保証するように設計されています。 データベースのアップグレードやメンテナンスを行うときでも、ワークロードの停止が必要なメンテナンス期間やダウンタイムは発生しません。

高可用性アーキテクチャ モデルには、次の 2 つがあります。

- 計算とストレージの分離に基づく **Standard 可用性モデル**。  リモート ストレージ層の高可用性と信頼性に依存します。 このアーキテクチャでは、メンテナンス作業中に一定のパフォーマンス低下を許容できる予算重視のビジネス アプリケーションを対象とします。
- データベース エンジン プロセスのクラスターに基づく **Premium 可用性モデル**。 利用可能なデータベース エンジン ノードのクォーラムが常にあるという事実に依存します。 このアーキテクチャでは、高い IO パフォーマンス、高いトランザクション レートを備えたミッション クリティカル なアプリケーションを対象とし、メンテナンス作業中のワークロードに対するパフォーマンスの影響を最小限に抑えることを保証します。

SQL Database と SQL Managed Instance は、どちらも最新の安定したバージョンの SQL Server データベース エンジンおよび Windows オペレーティング システム上で実行されています。また、ほとんどのユーザーが認識することなく、アップグレードが継続的に実行されています。

## <a name="basic-standard-and-general-purpose-service-tier-locally-redundant-availability"></a>Basic、Standard、および General Purpose サービス レベルのローカル冗長可用性

Basic、Standard、および General Purpose サービス レベルでは、サーバーレス コンピューティングとプロビジョニングされたコンピューティングの両方で、標準の可用性アーキテクチャを利用します。 次の図は、計算レイヤーとストレージ レイヤーが分離されている4 つの異なるノードを示しています。

![計算とストレージの分離](./media/high-availability-sla/general-purpose-service-tier.png)

Standard 可用性モデルには、次の 2 つのレイヤーがあります。

- ステートレス計算レイヤー。`sqlservr.exe` プロセスを実行しており、一時的なデータとキャッシュ データのみ (TempDB、アタッチされた SSD 上のモデル データベース、およびメモリ内のプラン キャッシュ、バッファー プール、列ストア プールなど) が含まれています。 このステートレス ノードは、`sqlservr.exe` の初期化、ノードの正常性の制御、および他のノードへのフェールオーバーを必要に応じて実行する Azure Service Fabric によって操作されます。
- ステートフル データ レイヤー。データベース ファイル (.mdf/.ldf) は Azure BLOB ストレージに保存されています。 Azure BLOB ストレージには、組み込みのデータ可用性と冗長性の機能があります。 `sqlservr.exe` プロセスがクラッシュした場合でも、ログ ファイル内のすべてのレコードまたはデータ ファイル内のすべてのページが保持されることを保証します。

データベース エンジンまたはオペレーティング システムがアップグレードされた場合、あるいは障害が検出された場合、Azure Service Fabric は常に、ステートレス `sqlservr.exe` プロセスを十分な空き容量がある別のステートレス計算ノードに移行します。 Azure BLOB ストレージ内のデータは移行による影響を受けず、データ/ログ ファイルは、新しく初期化された `sqlservr.exe` プロセスにアタッチされます。 このプロセスでは 99.99% の可用性が保証されますが、新しい `sqlservr.exe` プロセスがコールド キャッシュを使用して起動されるため、負荷の高いワークロードによって移行中にパフォーマンスの低下が発生する可能性があります。

## <a name="general-purpose-service-tier-zone-redundant-availability-preview"></a>General Purpose サービス レベルのゾーン冗長可用性 (プレビュー)

General Purpose サービス レベルのゾーン冗長構成では、[Azure Availability Zones](../../availability-zones/az-overview.md)  を利用して、Azure リージョン内の複数の物理的な場所にデータベースをレプリケートします。 ゾーン冗長を選択することで、アプリケーション ロジックを変更することなく、データセンターの壊滅的な障害を含む、大規模な障害から新規および既存の汎用単一データベースやエラスティック プールを回復できるようになります。

General Purpose レベル向けのゾーン冗長構成には、次の 2 つの層があります。  

- ステートフル データ レイヤー。データベース ファイル (.mdf/.ldf) は ZRS PFS (ゾーン冗長[ストレージ Premium ファイル共有](../../storage/files/storage-how-to-create-file-share.md)) に保存されています。 [ゾーン冗長ストレージ](../../storage/common/storage-redundancy.md)を使用すると、データとログ ファイルは、物理的に分離された 3 つの Azure 可用性ゾーン間で同期的にコピーされます。
- ステートレス コンピューティング レイヤー。sqlservr.exe プロセスを実行しており、一時的なデータとキャッシュ データのみ (TempDB、アタッチされた SSD 上のモデル データベース、およびメモリ内のプラン キャッシュ、バッファー プール、列ストア プールなど) が含まれています。 このステートレス ノードは、sqlservr.exe の初期化、ノードの正常性の制御、および他のノードへのフェールオーバーを必要に応じて実行する Azure Service Fabric によって操作されます。 ゾーン冗長 General Purpose データベースの場合、予備の容量があるノードをフェールオーバーのために他の Availability Zones ですぐに使用できます。

ゾーン冗長による General Purpose サービス レベル向けの高可用性アーキテクチャを、次の図に示します。

![General Purpose 向けのゾーン冗長構成](./media/high-availability-sla/zone-redundant-for-general-purpose.png)

> [!IMPORTANT]
> ゾーン冗長構成は Gen5 コンピューティング ハードウェアが選択されている場合のみ利用できます。 この機能は、SQL Managed Instance では使用できません。 General Purpose レベルのゾーン冗長構成は、次のリージョンでのみ利用できます。米国東部、米国東部 2、米国西部 2、北ヨーロッパ、西ヨーロッパ、東南アジア、オーストラリア東部、東日本、英国南部、およびフランス中部。

> [!NOTE]
> 80 個の仮想コアを備えたサイズの General Purpose データベースでは、ゾーン冗長構成によるパフォーマンスの低下が発生する可能性があります。 また、バックアップ、復元、データベース コピー、Geo DR のリレーションシップの設定などの操作では、1 TB を超える単一データベースのパフォーマンスが低下する可能性があります。 
> 
> [!NOTE]
> プレビューは予約インスタンスの対象になりません

## <a name="premium-and-business-critical-service-tier-locally-redundant-availability"></a>Premium および Business Critical サービス レベルのローカル冗長可用性

Premium および Business Critical サービス レベルでは、Premium 可用性モデルを利用します。1 つのノード上で、計算リソース (`sqlservr.exe` プロセス) とストレージ (ローカルに接続されている SSD) を統合します。 高可用性は、3 から 4 つのノードでクラスターを形成している追加ノードに計算とストレージの両方をレプリケートすることで、実現されます。

![データベース エンジン ノードのクラスター](./media/high-availability-sla/business-critical-service-tier.png)

基になるデータベース ファイル (.mdf/.ldf) は、非常に低い待機時間の IO をワークロードに提供するために、アタッチされている SSD ストレージ上に配置されています。 高可用性は、SQL Server [Always On 可用性グループ](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)と同様のテクノロジを使用して実装されます。 クラスターには、読み取り/書き込みの顧客ワークロードにアクセス可能な単一のプライマリ レプリカと、データのコピーを格納する最大 3 つのセカンダリ レプリカ (計算とストレージ) が含まれます。 プライマリ ノードは常にセカンダリ ノードへ順番に変更をプッシュし、各トランザクションをコミットする前に少なくとも 1 つのセカンダリ レプリカにデータが同期されるようにします。 このプロセスによって、何らかの理由でプライマリ ノードがクラッシュした場合に、フェールオーバー先となる完全に同期されたノードが常に用意されている状態が保証されます。 フェールオーバーは、Azure Service Fabric によって開始されます。 セカンダリ レプリカが新しいプライマリ ノードになると、もう 1 つのセカンダリ レプリカが作成され、クラスターに十分な数のノード (クォーラム セット) がある状態が保証されます。 フェールオーバーが完了すると、Azure SQL 接続は新しいプライマリ ノードに自動的にリダイレクトされます。

その他の利点としては、Premium 可用性モデルには、セカンダリ レプリカの 1 つに読み取り専用の Azure SQL 接続をリダイレクトする機能が含まれています。 この機能は、[読み取りスケールアウト](read-scale-out.md)と呼ばれます。追加料金なしで 100% の追加のコンピューティング容量を提供し、分析ワークロードなどの読み取り専用の操作をプライマリ レプリカからオフロードします。

## <a name="premium-and-business-critical-service-tier-zone-redundant-availability"></a>Premium および Business Critical サービス レベルのゾーン冗長可用性 

既定では、Premium 可用性モデル用のノードのクラスターは、同じデータ センター内に作成されます。 [Azure Availability Zones](../../availability-zones/az-overview.md) の導入によって、SQL Database では同じリージョン内のさまざまな可用性ゾーンに対する Business Critical データベースのさまざまなレプリカを配置できます。 単一障害点をなくすため、制御リングも複数のゾーンで 3 つのゲートウェイ リング (GW) として複製できます。 特定のゲートウェイ リングへのルーティングは [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) (ATM) によって制御されます。 Premium または Business Critical サービス レベルでのゾーン冗長構成では、追加のデータベース冗長性を作成しないため、追加料金なしで使用できます。 ゾーン冗長構成を選択することで、アプリケーション ロジックにまったく変更を加えずに、データセンターの壊滅的な障害などの極めて大規模な障害に対して、Premium または Business Critical データベースが回復性を備えることができます。 また、既存の Premium または Business Critical データベース、あるいはプールをゾーン冗長構成に変換することもできます。

ゾーン冗長データベースでは、離れた距離に位置するさまざまなデータセンターにレプリカがあるため、ネットワーク待機時間が長くなるとコミット時間が増大し、一部の OLTP ワークロードのパフォーマンスに影響を及ぼす可能性があります。 いつでもゾーン冗長設定を無効にして単一ゾーン構成に戻ることができます。 このプロセスはオンライン操作であり、通常のサービス レベル更新プログラムと似ています。 プロセスの最後に、データベースまたはプールがゾーン冗長リングから単一ゾーン リングに (または逆方向に) 移行されます。

> [!IMPORTANT]
> Business Critical レベルを使用している場合、ゾーン冗長構成は Gen5 コンピューティング ハードウェアが選択されている場合のみ利用できます。 ゾーン冗長データベースがサポートされているリージョンの最新情報については、「[リージョン別のサービスのサポート](../../availability-zones/az-region.md)」を参照してください。

> [!NOTE]
> この機能は、SQL Managed Instance では使用できません。

ゾーン冗長による高可用性アーキテクチャを、次の図に示します。

![高可用性アーキテクチャのゾーン冗長](./media/high-availability-sla/zone-redundant-business-critical-service-tier.png)


## <a name="hyperscale-service-tier-availability"></a>Hyperscale サービス レベルの可用性

ハイパースケール サービス レベルのアーキテクチャは「[分散機能アーキテクチャ](./service-tier-hyperscale.md#distributed-functions-architecture)」で説明されており、現在は SQL Managed Instance ではなく SQL Database でのみ使用できます。

![Hyperscale 機能のアーキテクチャ](./media/high-availability-sla/hyperscale-architecture.png)

Hyperscale の可用性モデルには、次の 4 つのレイヤーが含まれます。

- ステートレス計算レイヤー。`sqlservr.exe` プロセスを実行しており、一時的なデータとキャッシュ データのみ (カバーしない RBPEX キャッシュ、TempDB、アタッチされた SSD 上のモデル データベースなど、およびメモリ内のプラン キャッシュ、バッファー プール、列ストア プールなど) が含まれています。 このステートレス レイヤーには、プライマリ計算レプリカと、必要に応じて、フェールオーバー ターゲットとして機能できる多くのセカンダリ計算レプリカが含まれています。
- ページ サーバーによって形成されるステートレス ストレージ レイヤー。 このレイヤーは、計算レプリカで実行されている `sqlservr.exe` プロセス用の分散ストレージ エンジンです。 各ページ サーバーには、アタッチされた SSD 上のカバーする RBPEX キャッシュ、メモリにキャッシュされたデータ ページなど、一時的なデータとキャッシュされたデータのみが含まれます。 各ページ サーバーでは、負荷分散、冗長性、高可用性を提供するためのアクティブ/アクティブ構成にペアのページ サーバーがあります。
- ステートフルなトランザクション ログのストレージ レイヤー。ログ サービス プロセス、トランザクション ログのランディング ゾーン、およびトランザクション ログの長期保存を実行する計算ノードによって形成されます。 ランディング ゾーンと長期保存では Azure Storage を使用します。これにより、トランザクション ログの可用性と[冗長性](../../storage/common/storage-redundancy.md)が提供され、コミットされたトランザクションのデータの持続性が確保されます。
- ステートフルなデータ ストレージ レイヤー。Azure Storage に格納され、ページ サーバーによって更新される、データベース ファイル (.mdf/.ndf) が含まれます。 このレイヤーでは、Azure Storage のデータの可用性と[冗長性](../../storage/common/storage-redundancy.md)の機能を使用します。 これにより、Hyperscale アーキテクチャの他のレイヤーのプロセスがクラッシュした場合や、計算ノードで障害が発生した場合でも、データ ファイル内のすべてのページが保持されることが保証されます。

すべての Hyperscale レイヤー内の計算ノードは、Azure Service Fabric で実行されます。これにより、各ノードの正常性が制御され、必要に応じて使用できる正常なノードへのフェールオーバーが行われます。

Hyperscale の高可用性の詳細については、「[ハイパースケールでのデータベースの高可用性](./service-tier-hyperscale.md#database-high-availability-in-hyperscale)」を参照してください。


## <a name="accelerated-database-recovery-adr"></a>高速データベース復旧 (ADR)

[高速データベース復旧 (ADR)](../accelerated-database-recovery.md) は、特に実行時間の長いトランザクションがある場合に、データベースの可用性を大幅に向上させる、新しいデータベース エンジン機能です。 ADR は現在、Azure SQL Database、Azure SQL Managed Instance、Azure Synapse Analytics で使用可能です。

## <a name="testing-application-fault-resiliency"></a>アプリケーションの障害回復性のテスト

高可用性は、データベース アプリケーションに対して透過的に機能する SQL Database と SQL Managed Instance のプラットフォームの基礎となる部分です。 しかし、計画済みまたは計画外のイベント時に開始された自動フェールオーバー操作がアプリケーションに与える影響をテストしてから、運用環境にデプロイする必要があると Microsoft は認識しています。 特別な API を呼び出してデータベース、エラスティック プール、またはマネージド インスタンスを再起動することにより、手動でフェールオーバーをトリガーできます。 ゾーン冗長データベースまたはエラスティック プールの場合、API 呼び出しによって、クライアント接続が、古いプライマリの可用性ゾーンとは異なる可用性ゾーン内の新しいプライマリにリダイレクトされます。 そのため、フェールオーバーが既存のデータベース セッションにどのように影響するかをテストするだけでなく、ネットワーク待機時間の変化によってエンドツーエンドのパフォーマンスを変化させるかどうかを確認することもできます。 再起動操作が影響を及ぼし、その多くがプラットフォームに負荷をかける可能性があるため、各データベース、エラスティック プール、またはマネージド インスタンスに対しては、15 分ごとに 1 つのフェールオーバー呼び出しのみが許可されます。

フェールオーバーは、PowerShell、REST API または Azure CLI を使用して開始できます。

|デプロイの種類|PowerShell|REST API| Azure CLI|
|:---|:---|:---|:---|
|データベース|[Invoke-AzSqlDatabaseFailover](/powershell/module/az.sql/invoke-azsqldatabasefailover)|[データベース フェールオーバー](/rest/api/sql/databases/failover)|Azure CLI から REST API 呼び出しを呼び出すために [az rest](/cli/azure/reference-index#az-rest) が使用できます|
|エラスティック プール|[Invoke-AzSqlElasticPoolFailover](/powershell/module/az.sql/invoke-azsqlelasticpoolfailover)|[エラスティック プールのフェールオーバー](/rest/api/sql/elasticpools/failover)|Azure CLI から REST API 呼び出しを呼び出すために [az rest](/cli/azure/reference-index#az-rest) が使用できます|
|マネージド インスタンス|[Invoke-AzSqlInstanceFailover](/powershell/module/az.sql/Invoke-AzSqlInstanceFailover/)|[マネージド インスタンス - フェールオーバー](/rest/api/sql/managed%20instances%20-%20failover/failover)|[az sql mi failover](/cli/azure/sql/mi/#az-sql-mi-failover)|

> [!IMPORTANT]
> フェールオーバー コマンドは、ハイパースケール データベースの読み取り可能なセカンダリ レプリカでは使用できません。

## <a name="conclusion"></a>まとめ

Azure SQL Database と Azure SQL Managed Instance の特徴は、Azure プラットフォームと緊密に統合される、組み込みの高可用性ソリューションです。 これは、障害の検出と復旧に Service Fabric を、データ保護に Azure BLOB ストレージを、フォールト トレランスを高めるために Availability Zones を活用しています (ドキュメントの冒頭に記載のあるとおり、これは Azure SQL Managed Instance には適用されません)。 さらに、SQL Database と SQL Managed Instance では、レプリケーションとフェールオーバーのために、SQL Server インスタンスから Always On 可用性グループのテクノロジを活用しています。 これらのテクノロジを組み合わせることで、アプリケーションでは混合ストレージ モデルを最大限に活用して、高要件の SLA にも対応できます。

## <a name="next-steps"></a>次のステップ

- [Azure 可用性ゾーン](../../availability-zones/az-overview.md)の詳細
- [Service Fabric](../../service-fabric/service-fabric-overview.md) の詳細
- [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) の詳細
- [SQL Managed Instance で手動フェールオーバーを開始する方法](../managed-instance/user-initiated-failover.md)の詳細
- 高可用性およびディザスター リカバリーのためのその他のオプションについては、[ビジネス継続性](business-continuity-high-availability-disaster-recover-hadr-overview.md)に関するページを参照してください。
