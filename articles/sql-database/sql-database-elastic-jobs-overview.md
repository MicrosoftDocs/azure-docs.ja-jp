---
title: スケールアウトされたクラウド データベースの管理 | Microsoft Docs
description: エラスティック データベース ジョブ サービスを使用して、データベース グループ全体に対してスクリプトを実行します。
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: 8abb2e3ac4f62a3ea51cc686bbf23260fccc4077
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2019
ms.locfileid: "57441374"
---
# <a name="managing-scaled-out-cloud-databases"></a>スケールアウトされたクラウド データベースの管理

[!INCLUDE [elastic-database-jobs-deprecation](../../includes/sql-database-elastic-jobs-deprecate.md)]

**Elastic Database ジョブ**は顧客がホストする Azure クラウド サービスです。**ジョブ**と呼ばれるアドホック タスクとスケジュールされた管理タスクを実行できます。 ジョブを利用すると、Transact-SQL スクリプトで管理作業を実行することで、大規模なグループの Azure SQL データベースを簡単かつ信頼できる方法で管理できます。

スケールアウトされシャード化されたデータベースを管理するために、**Elastic Database ジョブ**機能 (プレビュー) を利用すると、次を含むデータベースのグループ全体で、信頼できる方法で Transact-SQL (T-SQL) スクリプトを実行できます。

- データベースのカスタム定義コレクション (詳細は後述)
- [エラスティック プール](sql-database-elastic-pool.md)内のすべてのデータベース
- シャード セット ( [Elastic Database クライアント ライブラリ](sql-database-elastic-database-client-library.md)を使用して作成)

シャード化されたデータベースの概念の詳細については、「[Sharding a SQL Server Database](https://blog.pythian.com/sharding-sql-server-database/)」(SQL Server Database のシャード化) をご覧ください。

## <a name="documentation"></a>ドキュメント

- [エラスティック データベース ジョブ コンポーネントのインストール](sql-database-elastic-jobs-service-installation.md)
- [Elastic Database ジョブの概要](sql-database-elastic-jobs-getting-started.md)
- [PowerShell を使用したジョブの作成と管理](sql-database-elastic-jobs-powershell.md)
- [スケールアウトされた Azure SQL データベースの作成と管理](sql-database-elastic-jobs-getting-started.md)

![エラスティック データベース ジョブ][1]

## <a name="why-use-jobs"></a>ジョブを使用する理由

### <a name="manage"></a>管理

スキーマの変更、資格情報の管理、参照データの更新、パフォーマンス データの収集、テナント (顧客) テレメトリの収集を簡単に実行できます。

### <a name="reports"></a>Reports

Azure SQL データベースのコレクションから 1 つのテーブルにデータを集約します。

### <a name="reduce-overhead"></a>オーバーヘッドの削減

通常、Transact-SQL ステートメントを実行するか、その他の管理タスクを実行するには、各データベースに個別に接続する必要があります。 ジョブを利用すれば、ターゲット グループ内の各データベースのログイン タスクが処理されます。 また、Azure SQL データベースのグループに対して実行する Transact-SQL スクリプトを定義、保守、維持することもできます。

### <a name="accounting"></a>会計

ジョブによりスクリプトが実行され、各データベースの実行状態が記録されます。 また、エラーが発生すると自動的に再試行を行います。

### <a name="flexibility"></a>柔軟性

Azure SQL データベースのカスタム グループを定義し、ジョブを実行するスケジュールを定義します。

> [!NOTE]
> Azure ポータルでは、SQL Azure エラスティック プールに限定された一連の少ない機能のみ利用できます。 現在のすべての機能にアクセスするには、PowerShell API を使用してください。

## <a name="applications"></a>[アプリケーション]

- 新しいスキーマのデプロイなどの管理タスクを実行します。
- 参照データ、すなわちすべてのデータベースに共通の製品情報を更新します。 または、平日の営業時間終了後に自動更新されるようにスケジュールします。
- インデックスを再構築して、クエリのパフォーマンスを向上します。 ピーク時以外の時間などに、データベース コレクション全体に対して定期的に実行するように再構築を構成できます。
- データベース セットのクエリ結果をリアルタイムで中央のテーブルに収集します。 パフォーマンス クエリを継続的に実行して、その他のタスクの実行をトリガーするように構成できます。
- 顧客の製品利用統計情報収集など、大規模なデータセット全体に対して、時間がかかるデータ処理クエリを実行します。 結果は 1 つの対象テーブルに収集され、分析に使用されます。

## <a name="elastic-database-jobs-end-to-end"></a>Elastic Database ジョブ: エンド ツー エンド

1. **エラスティック データベース ジョブ** コンポーネントをインストールします。 詳細については、「 [エラスティック データベース ジョブのインストール](sql-database-elastic-jobs-service-installation.md)」を参照してください。 インストールが失敗した場合は、「 [アンインストールする方法](sql-database-elastic-jobs-uninstall.md)」をご覧ください。
2. カスタム定義のデータベース コレクションの作成、スケジュールの追加、結果セットの収集など、その他の機能にアクセスするには、PowerShell API を使用します。 **エラスティック プール**に対して実行するように制限されたジョブの簡単なインストール、作成、監視には、ポータルを使用します。
3. ジョブ実行用に暗号化された資格情報を作成し、 [グループ内の各データベースにユーザー (またはロール) を追加します](sql-database-security-overview.md)。
4. グループ内のすべてのデータベースに対して実行できるべき等 T-SQL スクリプトを作成します。
5. 次の手順に従い、Azure portal を使用してジョブ作成します: [エラスティック データベース ジョブの作成と管理](sql-database-elastic-jobs-create-and-manage.md)。
6. または PowerShell スクリプトを使用します: [PowerShell を使用した SQL Database のエラスティック データベース ジョブの作成と管理 (プレビュー)](sql-database-elastic-jobs-powershell.md)。

## <a name="idempotent-scripts"></a>べき等スクリプト

スクリプトは [べき等](https://en.wikipedia.org/wiki/Idempotence)にする必要があります。 簡単に言えば、「べき等」とは、スクリプトが成功し、再度実行されるとき、結果が同じになることを意味します。 一時的なネットワークの問題により、スクリプトが失敗することがあります。 その場合、ジョブは事前に設定した回数に達するまで自動的にスクリプトを再試行します。 べき等スクリプトは、2 回実行して成功した場合、結果が同じになります。

単純な方法として、作成前に、オブジェクトの存在をテストします。  

```sql
    IF NOT EXIST (some_object)
    -- Create the object
```

同様に、スクリプトは、それが検出する条件を論理的に試験し、対処することで正常に実行できる必要があります。

## <a name="failures-and-logs"></a>エラーとログ

スクリプトが数回の試行後に失敗した場合、ジョブはエラーをログに記録し、続行します。 ジョブの終了後 (つまり、グループ内のすべてのデータベースに対して実行した後)、エラーが発生した試行の一覧を確認できます。 ログには、問題のあるスクリプトをデバッグするための詳細があります。

## <a name="group-types-and-creation"></a>グループの種類と作成

次の 2 種類のグループがあります。

1. シャード セット
2. カスタム グループ

シャード セットは [Elastic Database ツール](sql-database-elastic-scale-introduction.md)で作成されます。 シャード セット グループを作成すると、データベースが自動的にグループに追加されるか、グループから削除されます。 たとえば、シャード マップにシャードが追加されると、新しいシャードが自動的にグループに入ります。 以降はグループに対してジョブを実行できます。

その一方で、カスタム グループは厳格に定義されます。 明示的にカスタム グループにデータベースを追加するか、カスタム グループからデータベースを削除する必要があります。 データベースがグループから削除されると、ジョブはデータベースに対してスクリプトを試行し、結果的にエラーが発生します。 Azure ポータルで作成されたグループは現在のところ、カスタム グループです。

## <a name="components-and-pricing"></a>コンポーネントと価格

次のコンポーネントは、連携して管理ジョブのアドホック実行を可能にする Azure クラウド サービスを作成します。 コンポーネントはサブスクリプションで、セットアップ時に自動的にインストールされ、構成されます。 これらはすべて同じ自動生成された名前を持っているため、サービスを識別できます。 名前は一意であり、プレフィックス "edj" とその後に続くランダムに生成された 21 文字で構成されます。

- Azure Cloud Services

  エラスティック データベース ジョブ (プレビュー) は、顧客によってホストされる AzureCloud Service として配信され、要求されたタスクを実行します。 ポータルから、サービスは Microsoft Azure サブスクリプションにデプロイされ、ホストされます。 デプロイされる既定のサービスは、高可用性のための 2 つの worker ロールの最小値で実行されます。 各 worker ロール (ElasticDatabaseJobWorker) の既定のサイズは A0 インスタンスで実行されます。 料金については、「 [Cloud Services 料金](https://azure.microsoft.com/pricing/details/cloud-services/)」をご覧ください。

- Azure SQL Database

  このサービスは、**管理データベース**と呼ばれる Azure SQL Database を使用してすべてのジョブ メタデータを保持します。 既定のサービス層は、S0 です。 詳細については、「 [SQL Database の料金](https://azure.microsoft.com/pricing/details/sql-database/)」をご覧ください。

- Azure Service Bus

  Azure Service Bus は、Azure Cloud Service 内の作業を調整します。 「 [Service Bus 料金](https://azure.microsoft.com/pricing/details/service-bus/)」をご覧ください。

- Azure Storage

  Azure ストレージ アカウントは問題にさらにデバッグが必要な場合に、診断出力のログ記録を格納するために使用されます (「[Azure Cloud Services および Virtual Machines の診断機能](../cloud-services/cloud-services-dotnet-diagnostics.md)」を参照してください)。 料金については、「 [Azure Storage の料金](https://azure.microsoft.com/pricing/details/storage/)」をご覧ください。

## <a name="how-elastic-database-jobs-work"></a>エラスティック データベース ジョブの機能

1. Azure SQL Database には、すべてのメタデータと状態データが保存される **管理データベース** が指定されています。
2. 実行するジョブの開始と追跡のために、 **ジョブ サービス** から管理データベースにアクセスされます。
3. 次の 2 種類のロールが管理データベースと通信します。
   - コントローラー: 要求されたジョブを実行するタスクが必要なジョブを決定し、新しいジョブ タスクを作成して失敗したジョブを再試行します。
   - ジョブ タスク実行: ジョブ タスクを実行します。

### <a name="job-task-types"></a>ジョブ タスクの種類

ジョブを実行するジョブ タスクの種類は複数あります。

- ShardMapRefresh

  シャード マップに対してクエリを実行して、シャードとして使用されているすべてのデータベースを決定します
- ScriptSplit

  'GO' ステートメント全体のスクリプトを複数のバッチに分割します
- ExpandJob

  データベース グループを対象とするジョブから各データベースの子ジョブを作成します
- ScriptExecution

  定義済みの資格情報を使用して、特定のデータベースに対してスクリプトを実行します
- Dacpac

  特定の資格情報を使用して、DACPAC を特定のデータベースに適用します

## <a name="end-to-end-job-execution-work-flow"></a>エンドツーエンドの実行ワークフロー

1. ポータルまたは PowerShell API を使用して、ジョブを**管理データベース**に挿入します。 ジョブから、特定の資格情報を使用してデータベース グループに対して Transact-SQL スクリプトの実行が要求されます。
2. コントローラーで、新しいジョブが識別されます。 スクリプトを分割し、グループのデータベースを更新するジョブ タスクが作成され、実行されます。 最後に、ジョブを展開し、新しい子ジョブを作成する新しいジョブが作成され、実行されます。各子ジョブは、グループ内の個々のデータベースに対して Transact-SQL スクリプトを実行するように指定されています。
3. コントローラーで、作成された子ジョブが識別されます。 コントローラーで、ジョブごとにデータベースに対してスクリプトを実行するジョブ タスクが作成され、トリガーされます。
4. すべてのジョブのタスクが完了すると、コントローラーのジョブの状態は完了に更新されます。
   ジョブ実行中の任意の時点で、ジョブ実行の現在の状態を確認する PowerShell API を使用できます。 PowerShell API から返されるすべての時刻は、UTC 形式です。 必要に応じて、取り消し要求を開始してジョブを停止できます。

## <a name="next-steps"></a>次の手順

[コンポーネントをインストール](sql-database-elastic-jobs-service-installation.md)し、[データベース グループ内の各データベースにログインを作成し、追加](sql-database-manage-logins.md)します。 ジョブの作成と管理の詳細については、「 [エラスティック データベース ジョブの作成と管理](sql-database-elastic-jobs-create-and-manage.md)」を参照してください。 「 [Elastic Database ジョブの概要](sql-database-elastic-jobs-getting-started.md)」も参照してください。

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-overview/elastic-jobs.png
<!--anchors-->
