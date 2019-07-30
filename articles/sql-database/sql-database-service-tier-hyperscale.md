---
title: Azure SQL Database Hyperscale の概要 | Microsoft Docs
description: この記事では、Azure SQL Database における仮想コアベースの購入モデルでのハイパースケール サービス レベル、およびそれが General Purpose および Business Critical サービス レベルと異なる点について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 05/06/2019
ms.openlocfilehash: 4aeda5612b2b3e9e2073a65320b238266c8bb33a
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2019
ms.locfileid: "67537867"
---
# <a name="hyperscale-service-tier-for-up-to-100-tb"></a>最大 100 TB の Hyperscale サービス レベル

Azure SQL Database は、インフラストラクチャに障害が発生した場合でも 99.99% の可用性を確保するために、クラウド環境に合わせて調整された SQL Server データベース エンジン アーキテクチャに基づいています。 Azure SQL Database で使用されているアーキテクチャ モデルは 3 つあります。
- General Purpose/Standard 
-  ハイパースケール
-  Business Critical/Premium

Azure SQL Database の Hyperscale サービス レベルは、仮想コアベースの購入モデルにおける最新のサービス レベルです。 このサービス レベルは、拡張性の高いストレージおよびコンピューティング パフォーマンス レベルであり、Azure のアーキテクチャを利用して、General Purpose および Business Critical サービス レベルの制限を大きく超えて、Azure SQL Database 用のストレージおよびコンピューティング リソースをスケールアウトします。

> 
> [!NOTE]
> 仮想コアベースの購入モデルでの General Purpose サービス レベルと Business Critical サービス レベルの詳細については、[General Purpose](sql-database-service-tier-general-purpose.md) サービス レベルと [Business Critical](sql-database-service-tier-business-critical.md) サービス レベルの記事を参照してください。 仮想コアベースの購入モデルと DTU ベースの購入モデルとの比較については、[Azure SQL Database の購入モデルとリソース](sql-database-service-tiers.md)に関する記事をご覧ください。


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

ハイパースケール サービス レベルのコンピューティング サイズについて詳しくは、「[サービス レベルの特性](sql-database-service-tiers-vcore.md#service-tier-characteristics)」をご覧ください。

## <a name="who-should-consider-the-hyperscale-service-tier"></a>Hyperscale サービス レベルを検討する必要があるユーザー

Hyperscale サービス レベルの対象として主に意図されているのは、オンプレミスに大規模なデータベースを持っていて、クラウドに移行することによってアプリケーションを最新化することを考えているお客様、または既にクラウドを利用していて、データベースの最大サイズ (1 から 4 TB) によって制限されているお客様です。 また、ストレージとコンピューティングに対して高いパフォーマンスと高いスケーラビリティを必要としているお客様も対象になります。

Hyperscale サービス レベルはすべての SQL Server ワークロードをサポートしますが、主に OLTP 用に最適化されています。 Hyperscale サービス レベルは、ハイブリッド ワークロードと分析 (データ マート) ワークロードもサポートしています。

> [!IMPORTANT]
> エラスティック プールでは、Hyperscale サービス レベルはサポートされていません。

## <a name="hyperscale-pricing-model"></a>ハイパースケールの価格モデル

ハイパースケール サービス レベルは[仮想コア モデル](sql-database-service-tiers-vcore.md)のみで提供されています。 新しいアーキテクチャに合わせて、価格モデルは General Purpose または Business Critical サービス モデルとは少し異なります。

- **コンピューティング**:

  ハイパースケール コンピューティング ユニットの料金はレプリカ単位です。 [Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-benefit/)の価格が読み取りスケール レプリカに自動的に適用されます。 既定では、Hyperscale データベースごとにプライマリ レプリカと 1 つの読み取り専用レプリカを作成します。  ユーザーは、プライマリを含むレプリカの総数を、1 から 5 までの間で調整できます。

- **ストレージ**:

  ハイパースケールのデータベースを構成するときに、最大データ サイズを指定する必要はありません。 ハイパースケール レベルでは、実際の使用量に基づき、データベース用のストレージに料金が発生します。 ストレージは 10 GB から 100 TB の間で自動的に割り当てられ、増加分は 10 GB から 40 GB の間で動的に調整されます。  

ハイパースケールの価格について詳しくは、「[Azure SQL Database の価格](https://azure.microsoft.com/pricing/details/sql-database/single/)」をご覧ください。

## <a name="distributed-functions-architecture"></a>分散機能アーキテクチャ

1 つの場所/プロセスにすべてのデータ管理機能を集中させる従来のデータベース エンジンとは異なり (今日の運用環境で分散データベースと呼ばれているものでも、モノリシックなデータ エンジンの複数のコピーを使用しています)、Hyperscale データベースでは、さまざまなデータ エンジンのセマンティクスが分岐しているクエリ処理エンジンと、データの長期的なストレージと持続性を提供するコンポーネントが分かれています。 これにより、ストレージ容量をスムーズに必要な限りスケールアウトできます (初期ターゲットは 100 TB)。 読み取り専用レプリカは同じストレージ コンポーネントを共有しているので、新しい読み取り可能レプリカを起動するためにデータのコピーは必要ありません。 

次の図では、Hyperscale データベースでのノードのさまざまな種類を示します。

![アーキテクチャ](./media/sql-database-hyperscale/hyperscale-architecture.png)

Hyperscale データベースには、次の種類のノードが含まれます。

### <a name="compute-node"></a>コンピューティング ノード

コンピューティング ノードにはリレーショナル エンジンが存在するので、すべての言語要素、クエリ処理などがここで発生します。 Hyperscale データベースとユーザーのすべてのやり取りは、これらのコンピューティング ノードを通して行われます。 データのページをフェッチするために必要なネットワーク ラウンドトリップの数を最小限に抑えるため、コンピューティング ノードは SSD ベースのキャッシュ (前の図の RBPEX - 弾性バッファー プール拡張機能) を備えています。 プライマリ コンピューティング ノードが 1 つあり、すべての読み書きワークロードとトランザクションがそこで処理されます。 1 つ以上のセカンダリ コンピューティング ノードがあり、フェールオーバーのためのホット スタンバイ ノードとして、また (この機能が必要な場合は) 読み取りワークロードをオフロードするための読み取り専用コンピューティング ノードとして機能します。

### <a name="page-server-node"></a>ページ サーバー ノード

ページ サーバーは、スケールアウトされたストレージ エンジンを表すシステムです。  各ページ サーバーは、データベース内のページのサブセットを受け持ちます。  通常、各ページ サーバーでは 128 GB から 1 TB のデータが制御されます。 複数のページ サーバーでデータが共有されることはありません (冗長性と可用性のために保持されているレプリカの外部)。 ページ サーバーの役割は、必要に応じてコンピューティング ノードにデータベース ページを提供し、トランザクションでデータが更新されたらページを更新することです。 ページ サーバーは、ログ サービスからのログ レコードを再生することによって最新の状態に維持されます。 また、ページ サーバーはパフォーマンスを強化するために SSD ベースのキャッシュも備えています。 信頼性向上のため、データ ページの長期的なストレージが Azure Storage に保持されます。

### <a name="log-service-node"></a>ログ サービス ノード

ログ サービス ノードは、プライマリ コンピューティング ノードからログ レコードを受け取り、持続性キャッシュにそれを保持し、他のコンピューティング ノード (キャッシュを更新できるように) および関連するページ サーバーにログ レコードを転送して、それらの場所でデータを更新できるようにします。 このようにして、プライマリ コンピューティング ノードからのすべてのデータ変更が、ログ サービスを介して、すべてのセカンダリ コンピューティング ノードとページ サーバーに伝達されます。 最後に、ログ レコードは、無期限のストレージ リポジトリである Azure Storage の長期ストレージにプッシュされます。 このメカニズムにより、頻繁にログを切り捨てる必要がなくなります。 また、ログ サービスはアクセスを高速化するためのローカル キャッシュも備えています。

### <a name="azure-storage-node"></a>Azure Storage ノード

Azure Storage ノードは、ページ サーバーからのデータの最終的な送り先です。 このストレージは、バックアップ目的だけでなく、Azure リージョン間のレプリケーションにも使用されます。 バックアップは、データ ファイルのスナップショットで構成されます。 復元操作はこれらのスナップショットから行われるため高速であり、任意の時点にデータを復元できます。

## <a name="backup-and-restore"></a>バックアップと復元

バックアップはファイル スナップショット ベースなので、ほぼ瞬時に実行されます。 ストレージとコンピューティングの分離により、バックアップ/復元操作をストレージ層に移すことができるので、プライマリ コンピューティング ノードの処理の負荷が軽減されます。 その結果、大規模なデータベースのバックアップでも、プライマリ コンピューティング ノードのパフォーマンスには影響ありません。 同様に、復元はファイル スナップショットのコピーによって行われ、データ操作のサイズではありません。 同じストレージ アカウント内の復元の場合、復元操作は高速です。

## <a name="scale-and-performance-advantages"></a>スケールとパフォーマンスの利点

追加の読み取り専用コンピューティング ノードを迅速に起動/停止できるので、Hyperscale アーキテクチャでは、読み取りのスケーリング機能が優れ、より多くの書き込み要求に対応できるようにプライマリ コンピューティング ノードを解放することもできます。 また、Hyperscale アーキテクチャの共有ストレージ アーキテクチャのため、コンピューティング ノードをすばやくスケールアップ/スケールダウンできます。

## <a name="create-a-hyperscale-database"></a>ハイパースケール データベースの作成

ハイパースケール データベースは、[Azure portal](https://portal.azure.com)、[T-SQL](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current)、[Powershell](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqldatabase)、または [CLI](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create) を使用して作成できます。 ハイパースケール データベースは、[仮想コアベースの購入モデル](sql-database-service-tiers-vcore.md)のみを使用して入手できます。

次の T-SQL コマンドによって、ハイパースケール データベースが作成されます。 `CREATE DATABASE` ステートメントにエディションとサービス目標の両方を指定する必要があります。 有効なサービス目標の一覧については[リソースの制限](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-single-databases#hyperscale-service-tier)に関するページを参照してください。

```sql
-- Create a HyperScale Database
CREATE DATABASE [HyperScaleDB1] (EDITION = 'HyperScale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```
これにより、4 コア搭載の Gen5 ハードウェアに Hyperscale データベースが作成されます。

## <a name="migrate-an-existing-azure-sql-database-to-the-hyperscale-service-tier"></a>既存の Azure SQL Database のハイパースケール サービス レベルへの移行

既存の Azure SQL データベースをハイパースケールに移行するには、[Azure portal](https://portal.azure.com)、[T-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current)、[Powershell](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqldatabase)、または [CLI](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update) を使用します。 現時点で、これは一方向にしか移行できません。 ハイパースケールから他のサービス レベルにデータベースを移行することはできません。 運用データベースのコピーを作成して、概念実証 (POC) のためにハイパースケールに移行することをお勧めします。

次の T-SQL コマンドによってデータベースがハイパースケール サービス レベルに移行されます。 `ALTER DATABASE` ステートメントにエディションとサービス目標の両方を指定する必要があります。

```sql
-- Alter a database to make it a HyperScale Database
ALTER DATABASE [DB2] MODIFY (EDITION = 'HyperScale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```

## <a name="connect-to-a-read-scale-replica-of-a-hyperscale-database"></a>ハイパースケール データベースの読み取りスケールへの接続

ハイパースケール データベースでは、クライアントによって提供された接続文字列の `ApplicationIntent` 引数により、接続が書き込みレプリカと読み取り専用セカンダリ レプリカのどちらにルーティングされるかが指定されます。 `ApplicationIntent` が `READONLY` に設定され、データベースにセカンダリ レプリカがない場合、接続はプライマリ レプリカにルーティングされ、既定で `ReadWrite` の動作になります。

```cmd
-- Connection string with application intent
Server=tcp:<myserver>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```
## <a name="disaster-recovery-for-hyperscale-databases"></a>Hyperscale データベースのディザスター リカバリー
### <a name="restoring-a-hyperscale-database-to-a-different-geography"></a>Hyperscale データベースを別の地理的な場所に復元する
ディザスター リカバリー操作の一環として、またはドリル、再配置などの他の理由で、Azure SQL Database Hyperscale DB を、現在ホストされているリージョン以外のリージョンに復元する必要がある場合、主な方法として、データベースの geo リストアを実行します。  これには、他の AZURE SQL DB を別のリージョンに復元するときとまったく同じ手順が含まれます。
1. ターゲット リージョンにまだ適切なサーバーが存在しない場合は、そこに SQL Database サーバーを作成します。  このサーバーは、元の (ソース) サーバーと同じサブスクリプションが所有する必要があります。
2. 自動バックアップからの Azure SQL データベースの復元に関するページの「[geo リストア](https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups#geo-restore)」トピックにある手順に従ってください。

> [!NOTE]
> ソースとターゲットが別々のリージョンにあるため、データベースは、geo リストア以外と同様に、スナップショット ストレージをソース データベースと共有することができません。これは、非常に短時間で完了します。  Hyperscale データベースの geo リストアの場合、ターゲットが geo レプリケーション ストレージのペア リージョンにある場合でも、データのサイズに関連した操作になります。  つまり、geo リストアを実行すると、復元されるデータベースのサイズに比例した時間がかかります。  ターゲットがペア リージョンにある場合、コピーはデータセンター内にあり、インターネット経由の遠距離コピーよりもかなり高速ですが、それでもすべてのビットがコピーされます。

## <a name=regions></a>対応リージョン

現在、Azure SQL Database Hyperscale レベルは次のリージョンで使用できます。

- オーストラリア東部
- オーストラリア南東部
- ブラジル南部
- カナダ中部
- 米国中部
- 中国東部 2
- 中国北部 2
- 東アジア
- East US
- 米国東部 2
- フランス中部
- 東日本
- 西日本
- 韓国中部
- 韓国南部
- 米国中北部
- 北ヨーロッパ
- 南アフリカ北部
- 米国中南部
- 東南アジア
- 英国南部
- 英国西部
- 西ヨーロッパ
- 米国西部
- 米国西部 2

サポート対象として掲載されていないリージョンに Hyperscale データベースを作成したい場合は、Azure portal 経由でオンボード要求を送信できます。 サポート対象リージョンの一覧は随時更新されているため、最新のリージョンの一覧を確認してください。

掲載されていないリージョンに Hyperscale データベースを作成できるように要求するには:

1. [Azure の [ヘルプとサポート] ブレード](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)に移動します

2. [ **[新しいサポート リクエスト]** ](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) をクリックします

    ![Azure の [ヘルプとサポート] ブレード](media/sql-database-service-tier-hyperscale/whitelist-request-screen-1.png)

3. **[問題の種類]** で、 **[サービスとサブスクリプションの制限 (クォータ)]** を選択します

4. データベースの作成に使用するサブスクリプションを選択します

5. **[クォータの種類]** で、 **[SQL データベース]** を選択します

6. **[次へ: ソリューション]** をクリックします

1. **[詳細の指定]** をクリックします

    ![問題の詳細](media/sql-database-service-tier-hyperscale/whitelist-request-screen-2.png)

8. **[SQL Database のクォータの種類]** で **[その他のクォータ要求]** を選択します

9. 次のテンプレートを入力します。

    ![クォータの詳細](media/sql-database-service-tier-hyperscale/whitelist-request-screen-3.png)

    テンプレートで、以下の情報を指定します

    > 新しいリージョンで Azure Hyperscale SQL Database を作成するための要求<br/> リージョン: [要求対象のリージョンを入力]  <br/>
    > コンピューティング SKU/コア総数 (読み取り可能なレプリカを含む) <br/>
    > 推定 TB 数 
    >

10. **[重要度 C]** を選択します。

11. 適切な連絡方法を選択し、詳細を入力します。

12. **[保存]** 、 **[続行]** の順にクリックします

## <a name="known-limitations"></a>既知の制限事項
以下は、一般提供時点の Hyperscale サービス レベルに対する現在の制限事項です。  これらの制限事項ができるだけなくなるように、積極的に取り組んでいます。

| 問題 | 説明 |
| :---- | :--------- |
| [バックアップの管理] ウィンドウに、ハイパースケール データベースが表示されません。"SQL server" でフィルター処理すると表示されます。  | ハイパースケールは別の方法でバックアップを管理しています。そのため、長期的な保有期間と特定の時点のバックアップなどの保有設定が適用されず、無効になります。 したがって、ハイパースケールのデータベースは、[バックアップの管理] ウィンドウに表示されません。 |
| ポイントインタイム リストア | データベースがハイパースケール サービス レベルに移行された後で、移行よりも前の特定の時点への復元はサポートされません。|
| Hyperscale 以外の DB と Hypserscale の間での復元 | Hyperscale データベースを Hyperscale 以外のデータベースに復元することも、Hyperscale 以外のデータベースを Hyperscale データベースに復元することもできません。|
| データベース ファイルが移行時にアクティブなワークロードのために大きくなり、ファイル境界が 1 TB を超えると、移行が失敗する | 軽減策: <br> - 可能であれば、更新ワークロードが実行されていないときに、データベースを移行します。<br> - 移行を再試行します。移行時に 1 TB の境界を越えない限り、成功します。|
| マネージド インスタンス | Azure SQL Database Managed Instance は、現在、Hyperscale データベースではサポートされていません。 |
| エラスティック プール |  エラスティック プールは、現在、SQL Database Hyperscale ではサポートされていません。|
| ハイパースケールへの移行は現在一方向 | データベースがハイパースケールにいったん移行されると、ハイパースケール以外のサービス レベルに直接移行することはできません。 現時点では、ハイパースケールからハイパースケール以外にデータベースを移行するには、BACPAC ファイルを使用してエクスポート/インポートするしかありません。|
| メモリ内オブジェクトを含むデータベースの移行 | データベースがハイパースケール サービス レベルに移行される前に、メモリ内オブジェクトは削除され、メモリ内ではないオブジェクトとして再作成されます。|
| データ変更追跡 | Hyperscale データベースでは、データ変更追跡を使用できません。 |
| geo レプリケーション  | Azure SQL Database Hyperscale の geo レプリケーションは、まだ構成できません。  geo リストア (DR や他の目的で、データベースを別の地理的な場所に復元すること) は実行できます |
| TDE/AKV の統合 | Azure Key Vault を使用した透過的データベース暗号化 (通常、Bring Your Own Key (BYOK) と呼ばれます) は、Azure SQL Database Hyperscale ではまだサポートされていませんが、サービス マネージド キーを使用した TDE は完全にサポートされています。 |
|インテリジェント データベース機能 | 1.Create Index、Drop Index アドバイザー モデルは、Hyperscale DB 向けにトレーニングされていません。 <br/>2.Schema Issue、DbParameterization - 最近追加されたアドバイザーは Hyperscale データベースではサポートされていません。|



## <a name="next-steps"></a>次の手順

- ハイパースケールの FAQ については、[ハイパースケールに関するよくあるご質問](sql-database-service-tier-hyperscale-faq.md)をご覧ください。
- サービス レベルについては、[サービス レベル](sql-database-service-tiers.md)に関するページをご覧ください
- サーバーおよびサブスクリプション レベルの制限については、[論理サーバー上のリソース制限の概要](sql-database-resource-limits-logical-server.md)に関するページをご覧ください。
- 単一データベースの購入モデルの制限について詳しくは、「[Azure SQL Database の単一データベースに対する仮想コアベースの購入モデルの制限](sql-database-vcore-resource-limits-single-databases.md)」をご覧ください。
- 機能比較一覧については、[SQL 共通機能](sql-database-features.md)に関する記事をご覧ください。
