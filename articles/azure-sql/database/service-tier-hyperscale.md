---
title: Hyperscale サービス レベルとは
description: この記事では、Azure SQL Database における仮想コアベースの購入モデルでの Hyperscale サービス レベル、およびそれが General Purpose および Business Critical サービス レベルと異なる点について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 1/13/2021
ms.openlocfilehash: a167fedcb42560dec55cdbce40e36180d65e0179
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2021
ms.locfileid: "104951799"
---
# <a name="hyperscale-service-tier"></a>ハイパースケール サービス レベル

Azure SQL Database は、インフラストラクチャに障害が発生した場合でも 99.99% の可用性を確保するために、クラウド環境に合わせて調整された SQL Server データベース エンジン アーキテクチャに基づいています。 Azure SQL Database で使用されているアーキテクチャ モデルは 3 つあります。

- General Purpose/Standard
- ハイパースケール
- Business Critical/Premium

Azure SQL Database の Hyperscale サービス レベルは、仮想コアベースの購入モデルにおける最新のサービス レベルです。 このサービス レベルは、拡張性の高いストレージおよびコンピューティング パフォーマンス レベルであり、Azure のアーキテクチャを利用して、General Purpose および Business Critical サービス レベルの制限を大きく超えて、Azure SQL Database 用のストレージおよびコンピューティング リソースをスケールアウトします。

> [!NOTE]
>
> - 仮想コアベースの購入モデルでの General Purpose サービス レベルと Business Critical サービス レベルの詳細については、[General Purpose](service-tier-general-purpose.md) サービス レベルと [Business Critical](service-tier-business-critical.md) サービス レベルの記事を参照してください。 仮想コアベースの購入モデルと DTU ベースの購入モデルとの比較については、[Azure SQL Database の購入モデルとリソース](purchasing-models.md)に関する記事をご覧ください。
> - Hyperscale サービス レベルは現在、Azure SQL Database でのみ使用でき、Azure SQL Managed Instance では使用できません。

## <a name="what-are-the-hyperscale-capabilities"></a>ハイパースケールの機能とは

Azure SQL Database の Hyperscale サービス レベルでは、次の追加機能が提供されます。

- 最大 100 TB のデータベース サイズのサポート
- サイズに関係なく、コンピューティング リソースに対する IO の影響もなく、ほぼ瞬間的に行われるデータベース バックアップ (Azure BLOB Storage に格納されたファイル スナップショットに基づく)  
- 数時間あるいは数日かからずに数分間で行われる迅速なデータベース復元 (ファイル スナップショットに基づく) (データ操作の規模ではない)
- データ ボリュームに関係なく、高いログ スループットと速いトランザクション コミット時間による、全体的に高いパフォーマンス
- 迅速なスケールアウト - 読み取りワークロードのオフロード用と、ホット スタンバイ用に、1 つ以上の読み取り専用ノードをプロビジョニングできます。
- 迅速なスケールアップ - 大きいワークロードに対応する必要があるときはコンピューティング リソースを一定の時間でスケールアップでき、必要がなくなったらコンピューティング リソースをスケールダウンして戻すことができます。

Hyperscale サービス レベルでは、クラウド データベースにおいて従来見られた実質的な制限の多くが取り除かれます。 他のほとんどのデータベースは 1 つのノードで使用可能なリソースによって制限されますが、Hyperscale サービス レベルのデータベースにはそのような制限はありません。 ストレージ アーキテクチャの柔軟性が高く、必要に応じてストレージが拡張されます。 実際、Hyperscale データベースの作成時には最大サイズは定義されません。 Hyperscale データベースは必要に応じて拡大し、使用している容量に対してのみ課金されます。 読み取り集中型ワークロードでは、Hyperscale サービス レベルは、読み取りワークロードのオフロード用に必要に応じて追加の読み取りレプリカをプロビジョニングし、迅速なスケールアウトを提供します。

さらに、データベース バックアップの作成に必要な時間や、スケールアップまたはスケールダウンに必要な時間は、データベース内のデータの量に関連しなくなっています。 Hyperscale データベースはほぼ瞬時にバックアップできます。 また、数十テラバイトのデータベースを、数分でスケールアップまたはスケールダウンできます。 この機能により、初期構成の選択によって縛り付けられることを心配する必要はなくなります。

ハイパースケール サービス レベルのコンピューティング サイズについて詳しくは、「[サービス レベルの特性](service-tiers-vcore.md#service-tiers)」をご覧ください。

## <a name="who-should-consider-the-hyperscale-service-tier"></a>Hyperscale サービス レベルを検討する必要があるユーザー

Hyperscale サービス レベルは、個別にスケーラブルなコンピューティング リソースとストレージ リソースを使用して優れた柔軟性と高パフォーマンスを提供するため、ほとんどのビジネス ワークロードを対象としています。 ストレージを最大 100 TB まで自動スケールする機能により、次のようなお客様にとって最適な選択肢となります。

- オンプレミスに大規模なデータベースがあり、クラウドに移行してアプリケーションを最新化する必要がある場合
- 既にクラウドを利用しているが、他のサービス レベルのデータベースの最大サイズ制限によって制限されている (1 ～ 4 TB)
- 使用するデータベースは小さいが、垂直方向と水平方向の高速なコンピューティング スケーリング、高パフォーマンス、インスタント バックアップ、データベースの高速復元を必要としている。

Hyperscale サービス レベルは、純粋な OLTP から純粋な分析まで、さまざまな SQL Server ワークロードをサポートしていますが、主に OLTP とハイブリッド トランザクションおよび分析処理 (HTAP) のワークロード向けに最適化されています。

> [!IMPORTANT]
> エラスティック プールでは、Hyperscale サービス レベルはサポートされていません。

## <a name="hyperscale-pricing-model"></a>ハイパースケールの価格モデル

ハイパースケール サービス レベルは[仮想コア モデル](service-tiers-vcore.md)のみで提供されています。 新しいアーキテクチャに合わせて、価格モデルは General Purpose または Business Critical サービス モデルとは少し異なります。

- **コンピューティング**:

  ハイパースケール コンピューティング ユニットの料金はレプリカ単位です。 [Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-benefit/)の価格が読み取りスケール レプリカに自動的に適用されます。 既定では、Hyperscale データベースごとにプライマリ レプリカと 1 つの読み取り専用レプリカを作成します。  ユーザーは、プライマリを含むレプリカの総数を、1 から 5 までの間で調整できます。

- **ストレージ**:

  ハイパースケールのデータベースを構成するときに、最大データ サイズを指定する必要はありません。 Hyperscale レベルでは、実際の割り当てに基づいてデータベースのストレージに対して課金されます。 ストレージは、40 GB から 100 TB までの間 (10 GB 単位の増分) で自動的に割り当てられます。 必要に応じて、複数のデータ ファイルを同時に拡大することができます。 ハイパースケール データベースは、10 GB の開始サイズで作成され、10分ごとに 10 GB ずつ拡大し、最終的には 40 GB のサイズに達します。

ハイパースケールの価格について詳しくは、「[Azure SQL Database の価格](https://azure.microsoft.com/pricing/details/sql-database/single/)」をご覧ください。

## <a name="distributed-functions-architecture"></a>分散機能アーキテクチャ

1 つの場所/プロセスにすべてのデータ管理機能を集中させる従来のデータベース エンジンとは異なり (今日の運用環境で分散データベースと呼ばれているものでも、モノリシックなデータ エンジンの複数のコピーを使用しています)、Hyperscale データベースでは、さまざまなデータ エンジンのセマンティクスが分岐しているクエリ処理エンジンと、データの長期的なストレージと持続性を提供するコンポーネントが分かれています。 これにより、ストレージ容量をスムーズに必要な限りスケールアウトできます (初期ターゲットは 100 TB)。 読み取り専用レプリカは同じストレージ コンポーネントを共有しているので、新しい読み取り可能レプリカを起動するためにデータのコピーは必要ありません。

次の図では、Hyperscale データベースでのノードのさまざまな種類を示します。

![アーキテクチャ](./media/service-tier-hyperscale/hyperscale-architecture.png)

Hyperscale データベースには、次の種類のコンポーネントが含まれます。

### <a name="compute"></a>Compute

コンピューティング ノードは、リレーショナル エンジンが存在する場所です。 ここで、言語、クエリ、およびトランザクション処理が行われます。 Hyperscale データベースとユーザーのすべてのやり取りは、これらのコンピューティング ノードを通して行われます。 データのページをフェッチするために必要なネットワーク ラウンドトリップの数を最小限に抑えるため、コンピューティング ノードは SSD ベースのキャッシュ (前の図の RBPEX - 弾性バッファー プール拡張機能) を備えています。 プライマリ コンピューティング ノードが 1 つあり、すべての読み書きワークロードとトランザクションがそこで処理されます。 1 つ以上のセカンダリ コンピューティング ノードがあり、フェールオーバーのためのホット スタンバイ ノードとして、また (この機能が必要な場合は) 読み取りワークロードをオフロードするための読み取り専用コンピューティング ノードとして機能します。

ハイパースケール コンピューティング ノード上で実行されるデータベース エンジンは、他の Azure SQL Database サービス レベルと同じです。 ユーザーがハイパースケール コンピューティング ノード上のデータベース エンジンを操作する場合、サポートされる外部からのアクセスとエンジンの動作は、[既知の制限事項](#known-limitations)を除き、他のサービス レベルと同じです。

### <a name="page-server"></a>ページ サーバー

ページ サーバーは、スケールアウトされたストレージ エンジンを表すシステムです。  各ページ サーバーは、データベース内のページのサブセットを受け持ちます。  名目上、各ページ サーバーによって最大 128 GB または最大 1 TB のデータが制御されます。 複数のページ サーバーでデータが共有されることはありません (冗長性と可用性のために保持されているページ サーバー レプリカの外部)。 ページ サーバーの役割は、必要に応じてコンピューティング ノードにデータベース ページを提供し、トランザクションでデータが更新されたらページを更新することです。 ページ サーバーは、ログ サービスからのログ レコードを再生することによって最新の状態に維持されます。 また、パフォーマンスを強化するために、ページ サーバーによっては SSD ベースのキャッシュへの対応も維持されます。 信頼性向上のため、データ ページの長期的なストレージが Azure Storage に保持されます。

### <a name="log-service"></a>ログ サービス

ログ サービスは、プライマリ コンピューティング レプリカからログ レコードを受け取り、持続性キャッシュにそれを保持し、他のコンピューティング レプリカ (キャッシュを更新できるように) および関連するページ サーバーにログ レコードを転送して、それらの場所でデータを更新できるようにします。 このようにして、プライマリ コンピューティング レプリカからのすべてのデータ変更が、ログ サービスを介して、すべてのセカンダリ コンピューティング レプリカとページ サーバーに伝達されます。 最後に、ログ レコードは、実質的に無制限のストレージ リポジトリである Azure Storage の長期ストレージにプッシュされます。 このメカニズムにより、頻繁にログを切り捨てる必要がなくなります。 ログ サービスには、ログ レコードへのアクセスを高速化するためのローカル メモリと SSD キャッシュもあります。

### <a name="azure-storage"></a>Azure Storage

Azure Storage には、データベース内のすべてのデータ ファイルが格納されています。 ページ サーバーは Azure Storage 内のデータ ファイルを最新の状態に保ちます。 このストレージは、バックアップ目的だけでなく、Azure リージョン間のレプリケーションにも使用されます。 バックアップはデータ ファイルのストレージ スナップショットを使用して実行されます。 スナップショットを使用した復元操作は、データ サイズに関係なく高速です。 データはデータベースのバックアップ保有期間内の任意の時点に復元できます。

## <a name="backup-and-restore"></a>バックアップと復元

バックアップはファイル スナップショット ベースなので、ほぼ瞬時に実行されます。 ストレージとコンピューティングの分離により、バックアップ/復元操作をストレージ層に移すことができるので、プライマリ コンピューティング レプリカの処理の負荷が軽減されます。 その結果、データベースのバックアップによりプライマリ コンピューティング ノードのパフォーマンスが影響を受けることはありません。 同様に、ポイント イン タイム リカバリ (PITR) はファイル スナップショットに戻すことによって行われるため、データ サイズに左右される操作ではありません。 同じ Azure リージョンでの Hyperscale データベースの復元は一定時間の操作であり、数テラバイトのデータベースであっても数時間や数日ではなく数分で復元できます。 既存のバックアップを復元することによって新しいデータベースを作成する場合もこの機能を利用します。開発またはテスト目的でのデータベース コピーの作成は、テラバイト単位のデータベースであっても数分で実行できます。

Hyperscale データベースの geo リストアについては、「[Hyperscale データベースを別のリージョンに復元する](#restoring-a-hyperscale-database-to-a-different-region)」を参照してください。

## <a name="scale-and-performance-advantages"></a>スケールとパフォーマンスの利点

追加の読み取り専用コンピューティング ノードを迅速に起動/停止できるので、Hyperscale アーキテクチャでは、読み取りのスケーリング機能が優れ、より多くの書き込み要求に対応できるようにプライマリ コンピューティング ノードを解放することもできます。 また、Hyperscale アーキテクチャの共有ストレージ アーキテクチャのため、コンピューティング ノードをすばやくスケールアップ/スケールダウンできます。

## <a name="create-a-hyperscale-database"></a>ハイパースケール データベースの作成

Hyperscale データベースは、[Azure portal](https://portal.azure.com)、[T-SQL](/sql/t-sql/statements/create-database-transact-sql)、[PowerShell](/powershell/module/azurerm.sql/new-azurermsqldatabase)、または [CLI](/cli/azure/sql/db#az-sql-db-create) を使用して作成できます。 ハイパースケール データベースは、[仮想コアベースの購入モデル](service-tiers-vcore.md)を使用してのみ入手できます。

次の T-SQL コマンドによって、ハイパースケール データベースが作成されます。 `CREATE DATABASE` ステートメントにエディションとサービス目標の両方を指定する必要があります。 有効なサービス目標の一覧については[リソースの制限](./resource-limits-vcore-single-databases.md#hyperscale---provisioned-compute---gen4)に関するページを参照してください。

```sql
-- Create a Hyperscale Database
CREATE DATABASE [HyperscaleDB1] (EDITION = 'Hyperscale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```

これにより、4 コア搭載の Gen5 ハードウェアに Hyperscale データベースが作成されます。

## <a name="upgrade-existing-database-to-hyperscale"></a>既存のデータベースを Hyperscale にアップグレードする

Azure SQL Database の既存のデータベースを Hyperscale に移行するには、[Azure portal](https://portal.azure.com)、[T-SQL](/sql/t-sql/statements/alter-database-transact-sql)、[PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase)、または [CLI](/cli/azure/sql/db#az-sql-db-update) を使用します。 現時点で、これは一方向にしか移行できません。 データのエクスポートとインポート以外の方法で、Hyperscale から別のサービス レベルにデータベースを移動することはできません。 概念実証 (POC) のために、運用データベースのコピーを作成して、コピーを Hyperscale に移行することをお勧めします。 Azure SQL Database の既存のデータベースの Hyperscale レベルへの移行は、データ サイズに左右される操作です。

次の T-SQL コマンドによってデータベースがハイパースケール サービス レベルに移行されます。 `ALTER DATABASE` ステートメントにエディションとサービス目標の両方を指定する必要があります。

```sql
-- Alter a database to make it a Hyperscale Database
ALTER DATABASE [DB2] MODIFY (EDITION = 'Hyperscale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```

## <a name="connect-to-a-read-scale-replica-of-a-hyperscale-database"></a>ハイパースケール データベースの読み取りスケールへの接続

ハイパースケール データベースでは、クライアントによって提供された接続文字列の `ApplicationIntent` 引数により、接続が書き込みレプリカと読み取り専用セカンダリ レプリカのどちらにルーティングされるかが指定されます。 `ApplicationIntent` が `READONLY` に設定され、データベースにセカンダリ レプリカがない場合、接続はプライマリ レプリカにルーティングされ、既定で `ReadWrite` の動作になります。

```cmd
-- Connection string with application intent
Server=tcp:<myserver>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

ハイパースケールのセカンダリ レプリカはすべて同じであり、プライマリ レプリカと同じサービス レベル目標が使用されます。 複数のセカンダリ レプリカが存在する場合、ワークロードは使用可能なすべてのセカンダリ レプリカに分散されます。 各セカンダリ レプリカは個別に更新されます。 そのため、レプリカごとに、プライマリ レプリカに対して異なるデータ遅延が生じる可能性があります。

## <a name="database-high-availability-in-hyperscale"></a>Hyperscale でのデータベースの高可用性

他のすべてのサービス レベルと同様に、Hyperscale は、コンピューティング レプリカの可用性に関係なく、コミットされたトランザクションのデータの持続性を保証します。 プライマリ レプリカが使用できなくなったことによるダウンタイムの程度は、フェールオーバーの種類 (計画されたものと計画外のもの)、および少なくとも 1 つのセカンダリ レプリカの存在に左右されます。 計画フェールオーバー (つまりメンテナンス イベント) では、システムはフェールオーバーを開始する前に新しいプライマリ レプリカを作成するか、既存のセカンダリ レプリカをフェールオーバーのターゲットとして使用します。 計画外のフェールオーバー (つまりプライマリ レプリカでのハードウェア障害) では、システムはセカンダリ レプリカが存在する場合はこれをフェールオーバーのターゲットとして使用し、あるいは使用可能なコンピューティング能力のプールから新しいプライマリ レプリカを作成します。 後者の場合、新しいプライマリ レプリカの作成に必要な追加の手順により、ダウンタイムの期間が長くなります。

Hyperscale の SLA については、「[SLA for Azure SQL Database の SLA](https://azure.microsoft.com/support/legal/sla/sql-database/)」を参照してください。

## <a name="disaster-recovery-for-hyperscale-databases"></a>Hyperscale データベースのディザスター リカバリー

### <a name="restoring-a-hyperscale-database-to-a-different-region"></a>Hyperscale データベースを別のリージョンに復元する

ディザスター リカバリー操作の一環として、またはドリル、再配置などの他の理由で、Azure SQL Database の Hyperscale データベースを、現在ホストされているリージョン以外のリージョンに復元する必要がある場合、主な方法として、データベースの geo リストアを実行します。 これには、SQL Database の他のデータベースを別のリージョンに復元するときとまったく同じ手順が含まれます。

1. ターゲット リージョンにまだ適切なサーバーが存在しない場合は、そこに[サーバー](logical-servers.md)を作成します。  このサーバーは、元の (ソース) サーバーと同じサブスクリプションが所有する必要があります。
2. 自動バックアップからの Azure SQL Database のデータベースの復元に関するページの「[geo リストア](./recovery-using-backups.md#geo-restore)」トピックにある手順に従ってください。

> [!NOTE]
> ソースとターゲットが別々のリージョンにあるため、データベースは、データベースのサイズに関係なく短時間で完了する非 geo リストアの場合と同様に、スナップショット ストレージをソース データベースと共有できません。 Hyperscale データベースの geo リストアの場合、ターゲットが geo レプリケーション ストレージのペア リージョンにある場合でも、データのサイズに関連した操作になります。 そのため、geo リストアでは、復元されるデータベースのサイズに比例した時間がかかります。 ペア リージョン内にターゲットがある場合、データ転送は 1 つのリージョン内で行われます。これは、複数のリージョンにまたがって行われるデータ転送よりもはるかに高速になりますが、それでもデータのサイズに左右される操作になります。

## <a name="available-regions"></a><a name=regions></a>対応リージョン

Azure SQL Database の Hyperscale レベルはすべてのリージョンで利用できますが、以下に示すリージョンでは既定で有効になっています。 Hyperscale が既定で有効にならないリージョンに Hyperscale データベースを作成する場合は、Azure portal 経由でオンボード要求を送信できます。 手順については、「[Azure SQL Database のクォータの増加を要求する](quota-increase-request.md)」を参照してください。 要求を送信するときは、次のガイドラインに従ってください。

- [リージョン アクセス](quota-increase-request.md#region)の SQL Database クォータの種類を使用します。
- 説明に、読み取り可能なレプリカを含むコンピューティング SKU や合計コアを追加し、Hyperscale の容量をリクエストしていることを示します。
- また、時間の経過に伴うすべてのデータベースの合計サイズの予測を TB 単位で指定します。

有効なリージョン:
- オーストラリア東部
- オーストラリア南東部
- オーストラリア中部
- ブラジル南部
- カナダ中部
- 米国中部
- 中国東部 2
- 中国北部 2
- 東アジア
- 米国東部
- 米国東部 2
- フランス中部
- ドイツ中西部
- 東日本
- 西日本
- 韓国中部
- 韓国南部
- 米国中北部
- 北ヨーロッパ
- ノルウェー東部
- ノルウェー西部
- 南アフリカ北部
- 米国中南部
- 東南アジア
- スイス西部
- 英国南部
- 英国西部
- US DoD Central
- US DoD East
- US Gov アリゾナ
- US Gov テキサス
- 米国中西部
- 西ヨーロッパ
- 米国西部
- 米国西部 2

## <a name="known-limitations"></a>既知の制限事項

以下は、一般提供時点の Hyperscale サービス レベルに対する現在の制限事項です。  これらの制限事項ができるだけなくなるように、積極的に取り組んでいます。

| 問題 | 説明 |
| :---- | :--------- |
| サーバーの [バックアップの管理] ペインに、Hyperscale データベースが表示されない。 ビューからフィルターで除外される。  | Hyperscale では別の方法でバックアップが管理されています。そのため、長期的な保有期間と特定の時点のバックアップなどの保有設定が適用されません。 したがって、Hyperscale データベースは、[バックアップの管理] ペインに表示されません。<br><br>他の Azure SQL Database サービス レベルから Hyperscale に移行されたデータベースについては、移行前のバックアップは、ソース データベースの[バックアップ保有](automated-backups-overview.md#backup-retention)期間の間保持されます。 これらのバックアップを使用して、ソース データベースを移行前のある時点まで[復元](recovery-using-backups.md#programmatic-recovery-using-automated-backups)することができます。|
| ポイントインタイム リストア | Hyperscale 以外のデータベースを Hyperscale データベースとして復元することも、Hyperscale データベースを Hyperscale 以外のデータベースとして復元することもできません。 サービス レベルを変更することで Hyperscale に移行された Hyperscale 以外のデータベースの場合は、[プログラムによって](recovery-using-backups.md#programmatic-recovery-using-automated-backups)サポートされているデータベースのバックアップ保有期間内における移行前の特定の時点に復元します。 復元されたデータベースは Hyperscale 以外となります。 |
| Azure SQL Database サービス レベルを Hyperscale に変更した場合は、データベースに 1 TB を超えるデータ ファイルがあると操作は失敗する | 場合によっては、サービス レベルを Hyperscale に変更する前に、サイズの大きいファイルを 1 TB 未満に[圧縮](file-space-manage.md#shrinking-data-files)することで、この問題を回避できることがあります。 データベース ファイルの現在のサイズを確認するには、次のクエリを使用してください。 `SELECT file_id, name AS file_name, size * 8. / 1024 / 1024 AS file_size_GB FROM sys.database_files WHERE type_desc = 'ROWS'`;|
| SQL Managed Instance | 現在、Azure SQL Managed Instance は Hyperscale データベースではサポートされていません。 |
| エラスティック プール |  エラスティック プールは、現在、Hyperscale ではサポートされていません。|
| ハイパースケールへの移行は現在一方向 | データベースが Hyperscale にいったん移行されると、Hyperscale 以外のサービス レベルに直接移行することはできません。 現時点では、ハイパースケールからハイパースケール以外にデータベースを移行する唯一の方法は、BACPAC ファイルまたはその他のデータ移動テクノロジ (一括コピー、Azure Data Factory、Azure Databricks、SSIS など) を使用してエクスポートおよびインポートすることです。Azure portal、PowerShell ([New-AzSqlDatabaseExport](/powershell/module/az.sql/new-azsqldatabaseexport) と [New-AzSqlDatabaseImport](/powershell/module/az.sql/new-azsqldatabaseimport))、Azure CLI ([az sql db export](/cli/azure/sql/db#az-sql-db-export) と [az sql db import](/cli/azure/sql/db#az-sql-db-import))、[REST API](/rest/api/sql/) から BACPAC のエクスポートとインポートを行うことはサポートされていません。 比較的小さい Hyperscale データベース (最大 200 GB) の BACPAC インポートと BACPAC エクスポートは、SSMS と [SqlPackage](/sql/tools/sqlpackage) バージョン 18.4 以降を使用することでサポートされます。 大きなデータベースでは、BACPAC エクスポートと BACPAC インポートに時間がかかり、さまざまな理由で失敗する可能性があります。|
| インメモリ OLTP オブジェクトを使用したデータベースの移行 | Hyperscale では、メモリ最適化テーブルの型、テーブル変数、ネイティブ コンパイルされたモジュールなど、インメモリ OLTP オブジェクトのサブセットがサポートされています。 ただし、どのような種類のインメモリ OLTP オブジェクトでも移行されているデータベースに存在すると、Premium および Business Critical サービス レベルから Hyperscale に移行できません。 このようなデータベースを Hyperscale に移行するには、すべてのインメモリ OLTP オブジェクトとその依存関係を削除する必要があります。 データベースを移行した後は、これらのオブジェクトを再作成できます。 永続的と非永続的なメモリ最適化テーブルは Hyperscale では現在サポートされておらず、ディスク テーブルに変更する必要があります。|
| geo レプリケーション  | Azure SQL Database Hyperscale の geo レプリケーションは、まだ構成できません。 |
| データベース コピー | Hyperscale のデータベース コピーがパブリック プレビューになりました。 |
| インテリジェント データベース機能 | [プランの強制] オプションを除き、他のすべての自動チューニング オプションは Hyperscale ではまだサポートされていません。オプションは有効になっているように見えますが、推奨事項やアクションは実行されません。 |
| Query Performance Insights | Query Performance Insight は現在、Hyperscale データベースではサポートされていません。 |
| データベースの圧縮 | DBCC SHRINKDATABASE または DBCC SHRINKFILE は、現在、Hyperscale データベースではサポートされていません。 |
| データベースの整合性チェック | DBCC CHECKDB は現在、Hyperscale データベースではサポートされていません。 回避策として、DBCC CHECKFILEGROUP と DBCC CHECKTABLE を使用することもできます。 Azure SQL Database におけるデータ整合性管理の詳細については、「[Azure SQL Database でのデータ整合性](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)」を参照してください。 |

## <a name="next-steps"></a>次のステップ

- ハイパースケールの FAQ については、[ハイパースケールに関するよくあるご質問](service-tier-hyperscale-frequently-asked-questions-faq.md)をご覧ください。
- サービス レベルについては、[サービス レベル](purchasing-models.md)に関するページをご覧ください
- サーバーおよびサブスクリプション レベルの制限については、[サーバー上のリソース制限の概要](resource-limits-logical-server.md)に関するページをご覧ください。
- 単一データベースの購入モデルの制限について詳しくは、「[Azure SQL Database の単一データベースに対する仮想コアベースの購入モデルの制限](resource-limits-vcore-single-databases.md)」をご覧ください。
- 機能比較一覧については、[SQL 共通機能](features-comparison.md)に関する記事をご覧ください。
