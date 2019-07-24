---
title: Azure SQL Database Hyperscale の概要 | Microsoft Docs
description: この記事では、Azure SQL Database における仮想コアベースの購入モデルでのハイパースケール サービス レベル、およびそれが General Purpose および Business Critical サービス レベルと異なる点について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 04/04/2019
ms.openlocfilehash: 5e323b28913e0ba259654d39f97e0436e6bff2db
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59786024"
---
# <a name="hyperscale-service-tier-preview-for-up-to-100-tb"></a>最大 100 TB のハイパースケール サービス レベル (プレビュー)

Azure SQL Database は、インフラストラクチャに障害が発生した場合でも 99.99% の可用性を確保するために、クラウド環境に合わせて調整された SQL Server データベース エンジン アーキテクチャに基づいています。 Azure SQL Database で使用されているアーキテクチャ モデルは 3 つあります。

- General Purpose/Standard 
- Business Critical/Premium
- ハイパースケール

Azure SQL Database の Hyperscale サービス レベルは、仮想コアベースの購入モデルにおける最新のサービス レベルです。 このサービス レベルは、拡張性の高いストレージおよびコンピューティング パフォーマンス レベルであり、Azure のアーキテクチャを利用して、General Purpose および Business Critical サービス レベルの制限を大きく超えて、Azure SQL Database 用のストレージおよびコンピューティング リソースをスケールアウトします。

> [!IMPORTANT]
> ハイパースケール サービス レベルは現在パブリック プレビュー段階であり、一部の Azure リージョンのみで使用できます。 完全なリージョンの一覧については、[ハイパースケール サービス レベル対応リージョン](#available-regions)をご覧ください。 ハイパースケール データベースで本番用のワークロードを実行することは、現時点ではお勧めしません。 ハイパースケール データベースを他のサービス レベルに更新することはできません。 テスト用として、現在のデータベースのコピーを作成し、そのコピーをハイパースケール サービス レベルに更新することをお勧めします。
> [!NOTE]
> 仮想コアベースの購入モデルでの General Purpose サービス レベルと Business Critical サービス レベルの詳細については、[General Purpose](sql-database-service-tier-general-purpose.md) サービス レベルと [Business Critical](sql-database-service-tier-business-critical.md) サービス レベルの記事を参照してください。 仮想コアベースの購入モデルと DTU ベースの購入モデルとの比較については、[Azure SQL Database の購入モデルとリソース](sql-database-purchase-models.md)に関する記事をご覧ください。

## <a name="what-are-the-hyperscale-capabilities"></a>ハイパースケールの機能とは

Azure SQL Database の Hyperscale サービス レベルでは、次の追加機能が提供されます。

- 最大 100 TB のデータベース サイズのサポート
- サイズに関係なく、コンピューティングに対する IO の影響もなく、ほぼ瞬間的に行われるデータベース バックアップ (Azure BLOB Storage に格納されたファイル スナップショットに基づく)   
- 数時間あるいは数日かからずに数分間で行われる迅速なデータベース復元 (ファイル スナップショットに基づく) (データ操作の規模ではない)
- データ ボリュームに関係なく、高いログ スループットと速いトランザクション コミット時間による、全体的に高いパフォーマンス
- 迅速なスケールアウト - 読み取りワークロードのオフロード用と、ホット スタンバイ用に、1 つ以上の読み取り専用ノードをプロビジョニングできます。
- 迅速なスケールアップ - 大きいワークロードに対応する必要があるときはコンピューティング リソースを一定の時間でスケールアップでき、必要がなくなったらコンピューティング リソースをスケールダウンして戻すことができます。

Hyperscale サービス レベルでは、クラウド データベースにおいて従来見られた実質的な制限の多くが取り除かれます。 他のほとんどのデータベースは 1 つのノードで使用可能なリソースによって制限されますが、Hyperscale サービス レベルのデータベースにはそのような制限はありません。 ストレージ アーキテクチャの柔軟性が高く、必要に応じてストレージが拡張されます。 実際、Hyperscale データベースの作成時には最大サイズは定義されません。 Hyperscale データベースは必要に応じて拡大し、使用している容量に対してのみ課金されます。 読み取り集中型ワークロードでは、Hyperscale サービス レベルは、読み取りワークロードのオフロード用に必要に応じて追加の読み取りレプリカをプロビジョニングし、迅速なスケールアウトを提供します。

さらに、データベース バックアップの作成に必要な時間や、スケールアップまたはスケールダウンに必要な時間は、データベース内のデータの量に関連しなくなっています。 Hyperscale データベースはほぼ瞬時にバックアップできます。 また、数十テラバイトのデータベースを、数分でスケールアップまたはスケールダウンできます。 この機能により、初期構成の選択によって縛り付けられることを心配する必要はなくなります。

ハイパースケール サービス レベルのコンピューティング サイズについて詳しくは、「[サービス レベルの特性](sql-database-service-tiers-vcore.md#service-tier-characteristics)」をご覧ください。

## <a name="who-should-consider-the-hyperscale-service-tier"></a>Hyperscale サービス レベルを検討する必要があるユーザー

Hyperscale サービス レベルの対象として主に意図されているのは、オンプレミスに大規模なデータベースを持っていて、クラウドに移行することによってアプリケーションを最新化することを考えているお客様、または既にクラウドを利用していて、データベースの最大サイズ (1 から 4 TB) によって制限されているお客様です。 また、ストレージとコンピューティングに対して高いパフォーマンスと高いスケーラビリティを必要としているお客様も対象になります。

Hyperscale サービス レベルはすべての SQL Server ワークロードをサポートしますが、主に OLTP 用に最適化されています。 Hyperscale サービス レベルは、ハイブリッド ワークロードと分析 (データ マート) ワークロードもサポートしています。

> [!IMPORTANT]
> エラスティック プールでは、Hyperscale サービス レベルはサポートされていません。

## <a name="hyperscale-pricing-model"></a>ハイパースケールの価格モデル

ハイパースケール サービス レベルは[仮想コア モデル](sql-database-service-tiers-vcore.md)のみで提供されています。 新しいアーキテクチャに合わせて、価格モデルは General Purpose または Business Critical サービス モデルとは少し異なります。

- **コンピューティング**:

  ハイパースケール コンピューティング ユニットの料金はレプリカ単位です。 [Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-benefit/)の価格が読み取りスケール レプリカに自動的に適用されます。 パブリック プレビューでは、ハイパースケール データベースごとに既定で 2 つのレプリカを作成します。

- **ストレージ**:

  ハイパースケールのデータベースを構成するときに、最大データ サイズを指定する必要はありません。 ハイパースケール レベルでは、実際の使用量に基づき、データベース用のストレージに料金が発生します。 ストレージは、5 GB から 100 TB までの範囲で、動的に 1 GB 単位で割り当てられます。  

ハイパースケールの価格について詳しくは、「[Azure SQL Database の価格](https://azure.microsoft.com/pricing/details/sql-database/single/)」をご覧ください。

## <a name="distributed-functions-architecture"></a>分散機能アーキテクチャ

1 つの場所/プロセスにすべてのデータ管理機能を集中させる従来のデータベース エンジンとは異なり (今日の運用環境で分散データベースと呼ばれているものでも、モノリシックなデータ エンジンの複数のコピーを使用しています)、Hyperscale データベースでは、さまざまなデータ エンジンのセマンティクスが分岐しているクエリ処理エンジンと、データの長期的なストレージと持続性を提供するコンポーネントが分かれています。 これにより、ストレージ容量をスムーズに必要な限りスケールアウトできます (初期ターゲットは 100 TB)。 読み取り専用レプリカは同じコンピューティング コンポーネントを共有しているので、新しい読み取り可能レプリカを起動するためにデータのコピーは必要ありません。 プレビュー段階では 1 つの読み取り専用レプリカのみがサポートされます。

次の図では、Hyperscale データベースでのノードのさまざまな種類を示します。

![アーキテクチャ](./media/sql-database-hyperscale/hyperscale-architecture.png)

Hyperscale データベースには、次の種類のノードが含まれます。

### <a name="compute-node"></a>コンピューティング ノード

コンピューティング ノードにはリレーショナル エンジンが存在するので、すべての言語要素、クエリ処理などがここで発生します。 Hyperscale データベースとユーザーのすべてのやり取りは、これらのコンピューティング ノードを通して行われます。 データのページをフェッチするために必要なネットワーク ラウンドトリップの数を最小限に抑えるため、コンピューティング ノードは SSD ベースのキャッシュ (前の図の RBPEX - 弾性バッファー プール拡張機能) を備えています。 プライマリ コンピューティング ノードが 1 つあり、すべての読み書きワークロードとトランザクションがそこで処理されます。 1 つ以上のセカンダリ コンピューティング ノードがあり、フェールオーバーのためのホット スタンバイ ノードとして、また (この機能が必要な場合は) 読み取りワークロードをオフロードするための読み取り専用コンピューティング ノードとして機能します。

### <a name="page-server-node"></a>ページ サーバー ノード

ページ サーバーは、スケールアウトされたストレージ エンジンを表すシステムです。  各ページ サーバーは、データベース内のページのサブセットを受け持ちます。  通常、各ページ サーバーでは 1 テラバイトのデータが制御されます。 複数のページ サーバーでデータが共有されることはありません (冗長性と可用性のために保持されているレプリカの外部)。 ページ サーバーの役割は、必要に応じてコンピューティング ノードにデータベース ページを提供し、トランザクションでデータが更新されたらページを更新することです。 ページ サーバーは、ログ サービスからのログ レコードを再生することによって最新の状態に維持されます。 また、ページ サーバーはパフォーマンスを強化するために SSD ベースのキャッシュも備えています。 信頼性向上のため、データ ページの長期的なストレージが Azure Storage に保持されます。

### <a name="log-service-node"></a>ログ サービス ノード

ログ サービス ノードは、プライマリ コンピューティング ノードからログ レコードを受け取り、持続性キャッシュにそれを保持し、他のコンピューティング ノード (キャッシュを更新できるように) および関連するページ サーバーにログ レコードを転送して、それらの場所でデータを更新できるようにします。 このようにして、プライマリ コンピューティング ノードからのすべてのデータ変更が、ログ サービスを介して、すべてのセカンダリ コンピューティング ノードとページ サーバーに伝達されます。 最後に、ログ レコードは、無期限のストレージ リポジトリである Azure Storage の長期ストレージにプッシュされます。 このメカニズムにより、頻繁にログを切り捨てる必要がなくなります。 また、ログ サービスはアクセスを高速化するためのローカル キャッシュも備えています。

### <a name="azure-storage-node"></a>Azure Storage ノード

Azure Storage ノードは、ページ サーバーからのデータの最終的な送り先です。 このストレージは、バックアップ目的だけでなく、Azure リージョン間のレプリケーションにも使用されます。 バックアップは、データ ファイルのスナップショットで構成されます。 復元操作はこれらのスナップショットから行われるため高速であり、任意の時点にデータを復元できます。

## <a name="backup-and-restore"></a>バックアップと復元

バックアップはファイル スナップショット ベースなので、ほぼ瞬時に実行されます。 ストレージとコンピューティングの分離により、バックアップ/復元操作をストレージ層に移すことができるので、プライマリ コンピューティング ノードの処理の負荷が軽減されます。 その結果、大規模なデータベースのバックアップでも、プライマリ コンピューティング ノードのパフォーマンスには影響ありません。 同様に、復元はファイル スナップショットのコピーによって行われ、データ操作のサイズではありません。 同じストレージ アカウント内の復元の場合、復元操作は高速です。

## <a name="scale-and-performance-advantages"></a>スケールとパフォーマンスの利点

追加の読み取り専用コンピューティング ノードを迅速に起動/停止できるので、Hyperscale アーキテクチャでは、読み取りのスケーリング機能が優れ、より多くの書き込み要求に対応できるようにプライマリ コンピューティング ノードを解放することもできます。 また、Hyperscale アーキテクチャの共有ストレージ アーキテクチャのため、コンピューティング ノードをすばやくスケールアップ/スケールダウンできます。

## <a name="create-a-hyperscale-database"></a>ハイパースケール データベースの作成

ハイパースケール データベースは、[Azure portal](https://portal.azure.com)、[T-SQL](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current)、[PowerShell](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase)、または [CLI](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create) を使用して作成できます。 ハイパースケール データベースは、[仮想コアベースの購入モデル](sql-database-service-tiers-vcore.md)のみを使用して入手できます。

次の T-SQL コマンドによって、ハイパースケール データベースが作成されます。 `CREATE DATABASE` ステートメントにエディションとサービス目標の両方を指定する必要があります。

```sql
-- Create a HyperScale Database
CREATE DATABASE [HyperScaleDB1] (EDITION = 'HyperScale', SERVICE_OBJECTIVE = 'HS_Gen4_4');
GO
```

## <a name="migrate-an-existing-azure-sql-database-to-the-hyperscale-service-tier"></a>既存の Azure SQL Database のハイパースケール サービス レベルへの移行

既存の Azure SQL データベースをハイパースケールに移行するには、[Azure portal](https://portal.azure.com)、[T-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current)、[PowerShell](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase)、または [CLI](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update) を使用します。 パブリック プレビューでは一方向にしか移行できません。 ハイパースケールから他のサービス レベルにデータベースを移行することはできません。 運用データベースのコピーを作成して、概念実証 (POC) のためにハイパースケールに移行することをお勧めします。

次の T-SQL コマンドによってデータベースがハイパースケール サービス レベルに移行されます。 `ALTER DATABASE` ステートメントにエディションとサービス目標の両方を指定する必要があります。

```sql
-- Alter a database to make it a HyperScale Database
ALTER DATABASE [DB2] MODIFY (EDITION = 'HyperScale', SERVICE_OBJECTIVE = 'HS_Gen4_4');
GO
```

## <a name="connect-to-a-read-scale-replica-of-a-hyperscale-database"></a>ハイパースケール データベースの読み取りスケールへの接続

ハイパースケール データベースでは、クライアントによって提供された接続文字列の `ApplicationIntent` 引数により、接続が書き込みレプリカと読み取り専用セカンダリ レプリカのどちらにルーティングされるかが指定されます。 `ApplicationIntent` が `READONLY` に設定され、データベースにセカンダリ レプリカがない場合、接続はプライマリ レプリカにルーティングされ、既定で `ReadWrite` の動作になります。

```cmd
-- Connection string with application intent
Server=tcp:<myserver>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

## <a name="available-regions"></a>対応リージョン

ハイパースケール サービス レベルは現在パブリック プレビュー段階であり、次の Azure リージョンで使用できます。米国東部 1、米国東部 2、米国西部 2、米国中部、米国中北部、西ヨーロッパ、北ヨーロッパ、オーストラリア東部、オーストラリア南東部、東南アジア、東日本、韓国中部

## <a name="known-limitations"></a>既知の制限事項

| 問題 | 説明 |
| :---- | :--------- |
| SQL Database サーバーの [バックアップの管理] ウィンドウに、ハイパースケール データベースが表示されません。"SQL server" でフィルター処理すると表示されます。  | ハイパースケールは別の方法でバックアップを管理しています。そのため、長期的な保有期間と特定の時点のバックアップなどの保有設定が適用されず、無効になります。 したがって、ハイパースケールのデータベースは、[バックアップの管理] ウィンドウに表示されません。 |
| ポイントインタイム リストア | データベースがハイパースケール サービス レベルに移行された後で、移行よりも前の特定の時点への復元はサポートされません。|
| データベース ファイルが移行時にアクティブなワークロードのために大きくなり、ファイル境界が 1 TB を超えると、移行が失敗する | 軽減策: <br> - 可能であれば、更新ワークロードが実行されていないときに、データベースを移行します。<br> - 移行を再試行します。移行時に 1 TB の境界を越えない限り、成功します。|
| マネージド インスタンスが現在サポートされない | 現在、サポートされていません |
| ハイパースケールへの移行は現在一方向 | データベースがハイパースケールにいったん移行されると、ハイパースケール以外のサービス レベルに直接移行することはできません。 現時点では、ハイパースケールからハイパースケール以外にデータベースを移行するには、BACPAC ファイルを使用してエクスポート/インポートするしかありません。|
| メモリ内オブジェクトを含むデータベースの移行が現在サポートされない | データベースがハイパースケール サービス レベルに移行される前に、メモリ内オブジェクトは削除され、メモリ内ではないオブジェクトとして再作成されます。|
| 変更データ追跡は現在サポートされていません。 | ハイパースケール データベースでは、変更データ追跡を使用できません。

## <a name="next-steps"></a>次の手順

- ハイパースケールの FAQ については、[ハイパースケールに関するよくあるご質問](sql-database-service-tier-hyperscale-faq.md)をご覧ください。
- サービス レベルについては、[サービス レベル](sql-database-purchase-models.md)に関するページをご覧ください
- サーバーおよびサブスクリプション レベルの制限については、[SQL Database サーバー上のリソース制限の概要](sql-database-resource-limits-database-server.md)に関するページをご覧ください。
- 単一データベースの購入モデルの制限について詳しくは、「[Azure SQL Database の単一データベースに対する仮想コアベースの購入モデルの制限](sql-database-vcore-resource-limits-single-databases.md)」をご覧ください。
- 機能比較一覧については、[SQL 共通機能](sql-database-features.md)に関する記事をご覧ください。
