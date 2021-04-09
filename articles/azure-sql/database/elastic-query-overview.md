---
title: エラスティック クエリの概要
description: エラスティック クエリを使用すると、複数のデータベースにまたがる Transact-SQL クエリを実行できます。
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: overview
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
ms.date: 12/05/2019
ms.openlocfilehash: cac17bbac96d44d8d9bfce2e168de4ea6d4c5c08
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100364955"
---
# <a name="azure-sql-database-elastic-query-overview-preview"></a>Azure SQL Database のエラスティック クエリの概要 (プレビュー)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

エラスティック クエリ機能 (プレビュー) を使うと、Azure SQL Database の複数のデータベースにまたがる Transact-SQL クエリを実行することができます。 データベース間クエリを実行してリモート テーブルにアクセスしたり、Microsoft 製およびサード パーティ製ツール (Excel、Power BI、Tableau など) を接続して複数のデータベースが含まれるデータ層間でクエリを実行したりできます。 この機能を使用すれば、クエリを大規模なデータ層にスケールアウトし、結果をビジネス インテリジェンス (BI) レポートで視覚化することができます。

## <a name="why-use-elastic-queries"></a>エラスティック クエリを使用する理由

### <a name="azure-sql-database"></a>Azure SQL データベース

T-SQL のみで Azure SQL Database 内のデータベースにわたってクエリを実行します。 これにより、リモート データベースの読み取り専用クエリが可能になり、現在の SQL Server のお客様が 3 部および 4 部構成名または SQL Database へのリンク サーバーを使用してアプリケーションを移行できるようになります。

### <a name="available-on-standard-tier"></a>Standard レベルで利用可能

エラスティック クエリは、Standard と Premium の両方のサービス レベルでサポートされます。 下位のサービス レベルのパフォーマンスに関する制限事項については、後述の「プレビューの制限事項」をご覧ください。

### <a name="push-parameters-to-remote-databases"></a>リモート データベースにパラメーターをプッシュする

エラスティック クエリでは、SQL パラメーターを実行用にリモート データベースにプッシュ送信できるようになりました。

### <a name="stored-procedure-execution"></a>ストアド プロシージャの実行

[sp\_execute \_remote](/sql/relational-databases/system-stored-procedures/sp-execute-remote-azure-sql-database) を使用して、リモート ストアド プロシージャ呼び出しまたはリモート関数を実行します。

### <a name="flexibility"></a>柔軟性

エラスティック クエリを使用する外部テーブルで、スキーマまたはテーブル名が異なるリモート テーブルを参照できます。

## <a name="elastic-query-scenarios"></a>エラスティック クエリのシナリオ

目的は、複数のデータベースが 1 つの全体的な結果に行を提供するクエリ シナリオを容易に実現することです。 クエリは、ユーザーまたはアプリケーションによって直接、またはデータベースに接続されているツールを使って間接的に構成できます。 これは特に、商用の BI またはデータ統合ツール (または、変更することができないアプリケーション) を使用してレポートを作成する場合に便利です。 エラスティック クエリを使用すると、Excel、Power BI、Tableau、Cognos などのツールの使い慣れた SQL Server 接続機能を使用して、複数のデータベースにまたがるクエリを実行できます。
また、SQL Server Management Studio や Visual Studio によって発行されるクエリ経由でデータベースのコレクション全体に簡単にアクセスでき、Entity Framework やその他の ORM 環境から複数のデータベースにまたがるクエリを容易に実行できます。 図 1 に示すシナリオでは、既存のクラウド アプリケーション ( [エラスティック データベース クライアント ライブラリ](elastic-database-client-library.md)を使用) はスケールアウトされたデータ層を基盤としており、エラスティック クエリを使用して複数のデータベースにまたがるレポートが作成されます。

**図 1** スケールアウトされたデータ層で使用されるエラスティック クエリ

![スケールアウトされたデータ層で使用されるエラスティック クエリ][1]

エラスティック クエリの顧客シナリオは、次のトポロジによって特徴付けられます。

* **列方向のパーティション分割 - データベース間クエリ** (トポロジ 1): データは、データ層内の複数のデータベースの間で列方向にパーティション分割されます。 通常は、データベースごとに異なるテーブルのセットが存在します。 これは、異なるデータベースではスキーマが異なることを意味します。 たとえば、あるデータベースに在庫に関するすべてのテーブルが含まれていて、別のデータベースには会計に関連するすべてのテーブルが含まれているケースが該当します。 このトポロジを使用する一般的なユース ケースでは、複数のデータベースの複数のテーブルを対象にクエリを実行したりレポートを作成したりする必要があります。
* **行方向のパーティション分割 - シャーディング** (トポロジ 2): データは行方向にパーティション分割され、スケールアウトされたデータ層の全体に行が分散されます。 この方法では、参加しているすべてのデータベースでスキーマが同じになります。 この方法は、"シャーディング" とも呼ばれます。 シャーディングは、(1) エラスティック データベース ツール ライブラリまたは (2) 自己シャーディングを使って実行、管理できます。 エラスティック クエリは、複数のシャードを対象にクエリを実行したりレポートを作成するために使用します。 シャードは、通常は、エラスティック プール内のデータベースです。 データベースが共通のスキーマを共有している限り、エラスティック クエリは、エラスティック プールのすべてのデータベースを一度にクエリするための効率的な方法とみなすことができます。

> [!NOTE]
> エラスティック クエリは、大部分の処理 (フィルター処理、集計) を外部ソース側で実行できるレポート シナリオに最も適しています。 ETL 操作は、リモート データベースから大量のデータが転送されるところには適していません。 より複雑なクエリが含まれている負荷の高いレポート ワークロードやデータ ウェアハウス シナリオの場合は、[Azure Synapse Analytics](https://azure.microsoft.com/services/synapse-analytics) を使用することも検討してください。
>  

## <a name="vertical-partitioning---cross-database-queries"></a>列方向のパーティション分割 – データベース間クエリ

コード作成を開始するには、 [データベース間クエリの概要 (列方向のパーティション分割)](elastic-query-getting-started-vertical.md)に関するページを参照してください。

エラスティック クエリを使用すると、SQL Database 内のデータベースにあるデータを SQL Database 内の他のデータベースで利用できるようにすることができます。 これにより、あるデータベースからのクエリで、SQL Database 内の他の任意のリモート データベースにあるテーブルを参照することができます。 まず、各リモート データベースの外部データ ソースを定義します。 外部データ ソースは、リモート データベース上にあるテーブルにアクセスするローカル データベースに定義します。 リモート データベースに変更を加える必要はありません。 スキーマがデータベースごとに異なる一般的な列方向のパーティション分割シナリオでは、エラスティック クエリを使用して、参照データへのアクセスや、データベース間クエリなどの一般的なユース ケースを実装できます。

> [!IMPORTANT]
> ユーザーは、ALTER ANY EXTERNAL DATA SOURCE アクセス許可を所有している必要があります。 このアクセス許可は、ALTER DATABASE アクセス許可に含まれています。 ALTER ANY EXTERNAL DATA SOURCE アクセス許可は、基になるデータ ソースを参照するために必要です。
>

**参照データ**: 参照データの管理のためにトポロジが使われます。 次の図では、参照データを含む 2 つのテーブル (T1 と T2) は、専用のデータベースに保持されています。 エラスティック クエリを使用すると、図に示すように、他のデータベースからリモートでテーブル T1 と T2 にアクセスできるようになります。 参照テーブルのサイズが小さい場合や参照テーブルへのリモート クエリに選択的述語が含まれる場合は、トポロジ 1 を使用します。

**図 2** 列方向のパーティション分割 - エラスティック クエリを使用して参照データを照会する

![列方向のパーティション分割 - エラスティック クエリを使用して参照データを照会する][3]

**データベース間クエリ**: エラスティック クエリを使用すると、SQL Database で複数のデータベースにまたがるクエリを必要とするユース ケースに対応できます。 図 3 には 4 つの異なるデータベース (CRM、Inventory、HR、Products) が示されています。 これらのデータベースのいずれかで実行されるクエリでは、他のデータベースにもアクセスする必要があります。 エラスティック クエリを使用すると、4 つのデータベースごとにいくつかの単純な DDL ステートメントを実行することで、このケースに対応するデータベースを構成できます。 この 1 回限りの構成を行った後は、T-SQL クエリまたは BI ツールからローカル テーブルを参照するだけでリモート テーブルにアクセスできます。 この方法は、リモート クエリからサイズの大きな結果が返されない場合にお勧めします。

**図 3** 列方向のパーティション分割 - エラスティック クエリを使用して複数のデータベースにまたがって照会する

![列方向のパーティション分割 - エラスティック クエリを使用して複数のデータベースにまたがって照会する][4]

次の手順では、SQL Database 内の同じスキーマを持つリモート データベース上にあるテーブルへのアクセスを必要とする列方向のパーティション分割シナリオ向けに、エラスティック データベース クエリを構成します。

* [CREATE MASTER KEY](/sql/t-sql/statements/create-master-key-transact-sql) mymasterkey
* [CREATE DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/create-database-scoped-credential-transact-sql) mycredential
* [CREATE/DROP EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql) mydatasource ( **RDBMS**
* [CREATE/DROP EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql) mytable

DDL ステートメントを実行すると、ローカル テーブルであるかのようにリモート テーブル "mytable" にアクセスできます。 Azure SQL Database により、リモート データベースへの接続が自動的に開かれてリモート データベースで要求が処理され、その結果が返されます。

## <a name="horizontal-partitioning---sharding"></a>行方向のパーティション分割 - シャーディング

エラスティック クエリを使用してシャーディングされた (行方向にパーティション分割された) データ層に対してレポート タスクを実行するには、データ層のデータベースを表す [エラスティック データベース シャード マップ](elastic-scale-shard-map-management.md) が必要です。 一般に、このシナリオではシャード マップが 1 つだけ使用され、エラスティック クエリ機能 (ヘッド ノード) を備えた専用のデータベースがレポート クエリのエントリ ポイントとして機能します。 シャード マップにアクセスする必要があるのは、この専用のデータベースのみです。 図 4 に、このトポロジと、エラスティック クエリ データベースおよびシャード マップを使用した構成を示します。 エラスティック データベース クライアント ライブラリの概要とシャード マップの作成方法の詳細については、「 [シャード マップの管理](elastic-scale-shard-map-management.md)」を参照してください。

**図 4** 行方向のパーティション分割: シャーディングされたデータ層に対してエラスティック クエリを使用する

![行方向のパーティション分割: シャーディングされたデータ層に対してエラスティック クエリを使用する][5]

> [!NOTE]
> エラスティック クエリ データベース (ヘッド ノード) は、別のデータベースにするか、シャード マップをホストする同じデータベースにすることができます。
> 選択した構成にかかわらず、データベースに期待されるログイン/クエリ要求数を処理できるレベルのサービス レベルとコンピューティング サイズを確保します。

次の手順では、SQL Database 内の (通常は) いくつかのリモート データベース上にある一連のテーブルへのアクセスを必要とする行方向のパーティション分割シナリオ向けに、エラスティック データベース クエリを構成します。

* [CREATE MASTER KEY](/sql/t-sql/statements/create-master-key-transact-sql) mymasterkey
* [CREATE DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/create-database-scoped-credential-transact-sql) mycredential
* エラスティック データベース クライアント ライブラリを使用して、データ層を表す [シャード マップ](elastic-scale-shard-map-management.md) を作成します。
* [CREATE/DROP EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql) mydatasource (**SHARD_MAP_MANAGER** 型)
* [CREATE/DROP EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql) mytable

これらの手順を実行すると、ローカル テーブルであるかのように、行方向にパーティション分割されたテーブル "mytable" にアクセスできます。 Azure SQL Database により、テーブルが物理的に格納されているリモート データベースへの複数の並列接続が自動的に開かれます。さらに、リモート データベースで要求が処理され、その結果が返されます。
行方向のパーティション分割シナリオに必要な手順の詳細については、[行方向のパーティション分割のためのエラスティック クエリ](elastic-query-horizontal-partitioning.md)に関するページをご覧ください。

コーディングを開始するには、「[クロスデータベース クエリの概要 (列方向のパーティション分割) (プレビュー)](elastic-query-getting-started.md)」をご覧ください。

> [!IMPORTANT]
> 多数のデータベースに対するエラスティック クエリの正常な実行は、クエリの実行中に各データベースを使用できることに大きく依存しています。 いずれかのデータベースが使用できない場合は、クエリ全体が失敗します。 数百または数千のデータベースに対するクエリを一度に実行することを計画している場合は、クライアント アプリケーションに再試行ロジックが埋め込まれていることを確認してください。または、[Elastic Database Jobs](./job-automation-overview.md) (プレビュー) を活用して、データベースの小さなサブセットに対してクエリを実行し、各クエリの結果を単一の宛先に統合することを検討してください。

## <a name="t-sql-querying"></a>T-SQL クエリの実行

外部データ ソースと外部テーブルを定義すると、通常の SQL Server 接続文字列を使用して、外部テーブルが定義されているデータベースに接続できます。 次に、その接続で外部テーブルに対して T-SQL ステートメントを実行できます。この場合、次に示す制限事項があります。 T-SQL クエリの詳細とサンプルについては、[行方向のパーティション分割](elastic-query-horizontal-partitioning.md)と[列方向のパーティション分割](elastic-query-vertical-partitioning.md)に関するページをご覧ください。

## <a name="connectivity-for-tools"></a>ツールの接続性

通常の SQL Server 接続文字列を使用して、アプリケーション、BI、またはデータ統合ツールを、外部テーブルを持つデータベースに接続できます。 使用しているツールのデータ ソースとして SQL Server がサポートされていることを確認してください。 接続されたら、ツールを使用して接続する他の SQL Server データベースと同様に、エラスティック クエリ データベースと外部テーブルを参照します。

> [!IMPORTANT]
> Azure Active Directory とエラスティック クエリを使用した認証は、現時点ではサポートされていません。

## <a name="cost"></a>コスト

エラスティック クエリは、Azure SQL Database のコストに含まれます。 リモート データベースがエラスティック クエリ エンドポイントとは異なるデータ センターに配置されるトポロジはサポートされますが、リモート データベースからのデータ送信は、通常の [Azure 料金](https://azure.microsoft.com/pricing/details/data-transfers/)で課金されます。

## <a name="preview-limitations"></a>プレビューの制限事項

* Standard サービス レベルでは、初回のエラスティック クエリの実行に数分かかる場合があります。 これは、エラスティック クエリ機能の読み込みに要する時間です。サービス レベルおよびコンピューティング サイズが上位になるほど、読み込みのパフォーマンスが高くなります。
* SSMS または SSDT の外部データ ソースまたは外部テーブルからのスクリプト処理はまだサポートされていません。
* SQL Database の Import/Export では、外部データ ソースと外部テーブルはまだサポートされていません。 Import/Export を使用する必要がある場合は、エクスポートの前にこれらのオブジェクトを削除し、インポート後にこれらのオブジェクトを再作成します。
* 現在、エラスティック クエリでは、外部テーブルへの読み取り専用アクセスだけがサポートされています。 ただし、外部テーブルが定義されているデータベースでは、完全な T-SQL 機能を使用できます。 これは、SELECT <column_list> INTO <local_table> を使用して一時的な結果を保持する場合や、外部テーブルを参照するエラスティック クエリ データベースでストアド プロシージャを定義する場合などに便利です。
* 外部テーブル定義では、nvarchar(max) 以外の LOB 型 (空間型を含む) はサポートされていません。 この制限を回避するために、リモート データベースで LOB 型を nvarchar(max) にキャストするビューを作成し、ベース テーブルではなくそのビューで外部テーブルを定義し、クエリを使ってこれを元の LOB 型にキャストするという方法を利用できます。
* 結果セット内の nvarchar(max) データ型の列は、エラスティック クエリの実装で使用される高度なバッチ テクニックを無効にするため、クエリのパフォーマンスを 1 桁変化させる可能性があります。クエリの結果として未集計の大量のデータが転送される標準的でないユース ケースでは、パフォーマンスの変化は 2 桁にもなる可能性があります。
* 外部テーブルに対する列の統計情報は、現在サポートされていません。 テーブルの統計はサポートされていますが、手動で作成する必要があります。
* エラスティック クエリは Azure SQL Database でのみ機能します。 SQL Server インスタンスのクエリに使用することはできません。

## <a name="share-your-feedback"></a>フィードバックを共有する

以下に示す MSDN フォーラムまたは Stack Overflow で、エラスティック クエリに関するエクスペリエンスのフィードバックを共有してください。 サービスに関して何でもご意見とご感想をお寄せください (障害、機能差、悪口など)。

## <a name="next-steps"></a>次のステップ

* 列方向のパーティション分割のチュートリアルについては、「[クロスデータベース クエリの概要 (列方向のパーティション分割) (プレビュー)](elastic-query-getting-started-vertical.md)」をご覧ください。
* 列方向にパーティション分割されたデータの構文とサンプル クエリについては、「[例: 列方向にパーティション分割されたデータベースのクエリ](elastic-query-vertical-partitioning.md)」をご覧ください。
* 行方向のパーティション分割 (シャード化) のチュートリアルについては、「[スケールアウトされたクラウド データベース全体のレポート (プレビュー)](elastic-query-getting-started.md)」をご覧ください。
* 行方向にパーティション分割されたデータの構文とサンプル クエリについては、「[スケールアウトされたクラウド データベース全体をレポートする (プレビュー)](elastic-query-horizontal-partitioning.md)」をご覧ください。
* 行方向のパーティション分割方式でシャードとして機能する単一のリモート Azure SQL Database またはデータベースのセットに対して Transact-SQL ステートメントを実行するストアド プロシージャについては、「[sp\_execute\_remote](/sql/relational-databases/system-stored-procedures/sp-execute-remote-azure-sql-database)」をご覧ください。

<!--Image references-->
[1]: ./media/elastic-query-overview/overview.png
[2]: ./media/elastic-query-overview/topology1.png
[3]: ./media/elastic-query-overview/vertpartrrefdata.png
[4]: ./media/elastic-query-overview/verticalpartitioning.png
[5]: ./media/elastic-query-overview/horizontalpartitioning.png

<!--anchors-->
