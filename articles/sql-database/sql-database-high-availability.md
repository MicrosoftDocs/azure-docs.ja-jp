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
ms.author: sashan
ms.reviewer: carlrab, sashan
manager: craigg
ms.date: 06/10/2019
ms.openlocfilehash: a88842802759a5c3ae7af7334bbe125344c978ea
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67066911"
---
# <a name="high-availability-and-azure-sql-database"></a>高可用性と Microsoft Azure SQL Database

Azure SQL Database での高可用性アーキテクチャの目的は、メンテナンス操作や障害の影響を心配せずに、データベースの稼働および実行の時間が 99.99% 以上になるように保証することです。 Azure では、パッチ適用、バックアップ、Windows および SQL のアップグレードなどの重要なサービス タスクに加えて、基本となるハードウェア、ソフトウェア、またはネットワークのエラーなどの計画外のイベントを自動的に処理します。  基本となる SQL インスタンスがパッチを適用されるか、またはフェールオーバーした場合、お使いのアプリで[再試行ロジックを使用](sql-database-develop-overview.md#resiliency)していれば、ダウンタイムは認識されません。 Azure SQL Database は、クリティカルな状況であっても迅速な復旧が可能であるため、データが常に使用可能であることが保証されます。

高可用性ソリューションは、コミットされたデータが障害によって失われないこと、メンテナンス操作がワークロードに影響を及ぼさないこと、また、データベースがソフトウェア アーキテクチャでの単一障害点にならないことを保証するように設計されています。 データベースのアップグレードやメンテナンスを行うときでも、ワークロードの停止が必要なメンテナンス期間やダウンタイムは発生しません。 

Azure SQL Database に使用される高可用性アーキテクチャ モデルは 2 つあります

- 計算とストレージの分離に基づく Standard 可用性モデル。  リモート ストレージ層の高可用性と信頼性に依存します。 このアーキテクチャでは、メンテナンス作業中に一定のパフォーマンス低下を許容できる予算重視のビジネス アプリケーションを対象とします。
- データベース エンジン プロセスのクラスターに基づく Premium 可用性モデル。 利用可能なデータベース エンジン ノードのクォーラムが常にあるという事実に依存します。 このアーキテクチャでは、高い IO パフォーマンス、高いトランザクション レートを備えたミッション クリティカル なアプリケーションを対象とし、メンテナンス作業中のワークロードに対するパフォーマンスの影響を最小限に抑えることを保証します。

Azure SQL Database は、最新の安定したバージョンの SQL Server データベース エンジンおよび Windows OS 上で実行されています。また、ほとんどのユーザーが認識することなく、アップグレードが継続的に実行されています。

## <a name="basic-standard-and-general-purpose-service-tier-availability"></a>Basic、Standard、および General Purpose サービス レベルの可用性

これらのサービス レベルでは、Standard 可用性アーキテクチャを利用します。 次の図は、計算レイヤーとストレージ レイヤーが分離されている4 つの異なるノードを示しています。

![計算とストレージの分離](media/sql-database-high-availability/general-purpose-service-tier.png)

Standard 可用性モデルには、次の 2 つのレイヤーがあります。

- ステートレス計算レイヤー。`sqlserver.exe` プロセスを実行しており、アタッチされている SSD 上に一時的なデータとキャッシュ データのみが含まれています (TempDB、モデル データベース、プラン キャッシュ、バッファー プール、列のストア プールなど)。 このステートレス ノードは、`sqlserver.exe` の初期化、ノードの正常性の制御、および他のノードへのフェールオーバーを必要に応じて実行する Azure Service Fabric によって操作されます。
- ステートフル データ レイヤー。データベース ファイル (.mdf/.ldf) は Azure BLOB ストレージに保存されています。 Azure BLOB ストレージには、組み込みのデータ可用性と冗長性の機能があります。 SQL Server プロセスがクラッシュした場合でも、ログ ファイル内のすべてのレコードまたはデータ ファイル内のすべてのページが保持されることを保証します。

データベース エンジンまたはオペレーティング システムがアップグレードされた場合、あるいは障害が検出された場合、Azure Service Fabric は常に、ステートレス SQL Server プロセスを十分な空き容量がある別のステートレス計算ノードに移行します。 Azure BLOB ストレージ内のデータは移行による影響を受けず、データ/ログ ファイルは、新しく初期化された SQL Server プロセスにアタッチされます。 このプロセスでは 99.99% の可用性が保証されますが、新しい SQL Server インスタンスがコールド キャッシュを使用して起動されるため、負荷の高いワークロードによって移行中にパフォーマンスの低下が発生する可能性があります。

## <a name="premium-and-business-critical-service-tier-availability"></a>Premium および Business Critical サービス レベルの可用性

Premium および Business Critical サービス レベルでは、Premium 可用性モデルを利用します。1 つのノード上で、計算リソース (SQL Server データベース エンジン プロセス) とストレージ (ローカルに接続されている SSD) を統合します。 高可用性は、3 から 4 つのノードでクラスターを形成している追加ノードに計算とストレージの両方をレプリケートすることで、実現されます。 

![データベース エンジン ノードのクラスター](media/sql-database-high-availability/business-critical-service-tier.png)

基になるデータベース ファイル (.mdf/.ldf) は、非常に低い待機時間の IO をワークロードに提供するために、アタッチされている SSD ストレージ上に配置されています。 高可用性は、SQL Server [Always On 可用性グループ](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)と同様のテクノロジを使用して実装されます。 クラスターには、読み取り/書き込みの顧客ワークロードにアクセス可能な単一のプライマリ レプリカ (SQL Server プロセス) と、データのコピーを格納する最大 3 つのセカンダリ レプリカ (計算とストレージ) が含まれます。 プライマリ ノードは常にセカンダリ ノードへ順番に変更をプッシュし、各トランザクションをコミットする前に少なくとも 1 つのセカンダリ レプリカにデータが同期されるようにします。 このプロセスによって、何らかの理由でプライマリ ノードがクラッシュした場合に、フェールオーバー先となる完全に同期されたノードが常に用意されている状態が保証されます。 フェールオーバーは、Azure Service Fabric によって開始されます。 セカンダリ レプリカが新しいプライマリ ノードになると、もう 1 つのセカンダリ レプリカが作成され、クラスターに十分な数のノード (クォーラム セット) がある状態が保証されます。 フェールオーバーが完了すると、SQL 接続は新しいプライマリ ノードに自動的にリダイレクトされます。

その他の利点としては、Premium 可用性モデルには、セカンダリ レプリカの 1 つに読み取り専用の SQL 接続をリダイレクトする機能が含まれています。 この機能は、[読み取りスケールアウト](sql-database-read-scale-out.md)と呼ばれます。追加料金なしで 100% の追加のコンピューティング容量を提供し、分析ワークロードなどの読み取り専用の操作をプライマリ レプリカからオフロードします。

## <a name="zone-redundant-configuration"></a>ゾーン冗長の構成

既定では、Premium 可用性モデル用のノードのクラスターは、同じデータ センター内に作成されます。 [Azure Availability Zones](../availability-zones/az-overview.md) の導入によって、SQL Database では同じリージョン内のさまざまな可用性ゾーンに対する各種のレプリカを、クラスター内に配置できます。 単一障害点をなくすため、制御リングも複数のゾーンで 3 つのゲートウェイ リング (GW) として複製できます。 特定のゲートウェイ リングへのルーティングは [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) (ATM) によって制御されます。 Premium または Business Critical サービス レベルでのゾーン冗長構成では、追加のデータベース冗長性を作成しないため、追加料金なしで使用できます。 ゾーン冗長構成を選択することで、アプリケーション ロジックにまったく変更を加えずに、データセンターの壊滅的な障害などの極めて大規模な障害に対して、Premium または Business Critical データベースが回復性を備えることができます。 また、既存の Premium または Business Critical データベース、あるいはプールをゾーン冗長構成に変換することもできます。

ゾーン冗長データベースでは、離れた距離に位置するさまざまなデータセンターにレプリカがあるため、ネットワーク待機時間が長くなるとコミット時間が増大し、一部の OLTP ワークロードのパフォーマンスに影響を及ぼす可能性があります。 いつでもゾーン冗長設定を無効にして単一ゾーン構成に戻ることができます。 このプロセスはオンライン操作であり、通常のサービス レベル更新プログラムと似ています。 プロセスの最後に、データベースまたはプールがゾーン冗長リングから単一ゾーン リングに (または逆方向に) 移行されます。

> [!IMPORTANT]
> ゾーン冗長データベースとエラスティック プールは、現在、選択されたリージョンの Premium および Business Critical サービス レベルのみでサポートされています。 Business Critical レベルを使用している場合、ゾーン冗長構成は Gen5 コンピューティング ハードウェアが選択されている場合のみ利用できます。 ゾーン冗長データベースがサポートされているリージョンの最新情報については、「[リージョン別のサービスのサポート](../availability-zones/az-overview.md#services-support-by-region)」を参照してください。  

ゾーン冗長による高可用性アーキテクチャを、次の図に示します。

![高可用性アーキテクチャのゾーン冗長](./media/sql-database-high-availability/zone-redundant-business-critical-service-tier.png)

## <a name="accelerated-database-recovery-adr"></a>高速データベース復旧 (ADR)

[高速データベース復旧 (ADR)](sql-database-accelerated-database-recovery.md) は、特に実行時間の長いトランザクションがある場合に、データベースの可用性を大幅に向上させる、新しい SQL データベース エンジン機能です。 ADR は現在、単一データベース、エラスティック プール、および Azure SQL Data Warehouse で使用できます。

## <a name="conclusion"></a>まとめ

Azure SQL Database の特徴は、Azure プラットフォームと緊密に統合される、組み込みの高可用性ソリューションです。 障害の検出と復旧に Service Fabric を、データ保護に Azure BLOB ストレージを、フォールト トレランスを高めるために Availability Zones を活用しています。 さらに、Azure SQL データベースでは、レプリケーションとフェールオーバーのために、SQL Server から AlwaysOn 可用性グループのテクノロジを活用しています。 これらのテクノロジを組み合わせることで、アプリケーションでは混合ストレージ モデルを最大限に活用して、高要件の SLA にも対応できます。

## <a name="next-steps"></a>次の手順

- [Azure 可用性ゾーン](../availability-zones/az-overview.md)の詳細
- [Service Fabric](../service-fabric/service-fabric-overview.md) の詳細
- [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) の詳細
- 高可用性およびディザスター リカバリーのためのその他のオプションについては、[ビジネス継続性](sql-database-business-continuity.md)に関するページを参照してください。
