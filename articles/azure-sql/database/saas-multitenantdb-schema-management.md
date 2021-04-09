---
title: マルチテナント アプリでのスキーマの管理
description: Azure SQL Database を使用するマルチテナント アプリケーションで複数のテナントのスキーマを管理します
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/18/2018
ms.openlocfilehash: d222234cd6ff3d910e6dbc51a394695ce467edce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96011858"
---
# <a name="manage-schema-in-a-saas-application-that-uses-sharded-multi-tenant-databases"></a>共有マルチテナント データベースを使用している SaaS アプリケーションでのスキーマの管理
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

このチュートリアルでは、サービスとしてのソフトウェア (SaaS) アプリケーションで、データベース フリートを保持する際の課題について考察します。 データベースのフリート間でスキーマの変更を展開するための、ソリューションが示されています。

他のアプリケーションと同様に、Wingtip Tickets SaaS アプリは時間の経過に従って進化し、データベースへの変更が必要になる場合があります。 変更では、スキーマまたは参照データに影響を与えたり、データベース メンテナンス タスクを適用したりする可能性があります。 テナント パターンごとのデータベースを使用している SaaS アプリケーションの場合、大規模になる可能性があるテナント データベースのフリート間で変更を調整する必要があります。 さらに、データベース プロビジョニング プロセスにこれらの変更を組み込み、新しいデータベースが作成されるときに確実に含まれるようにします。

#### <a name="two-scenarios"></a>2 つのシナリオ

このチュートリアルでは、次の 2 つのシナリオを考察します。
- 参照データの更新をすべてのテナントにわたってデプロイする。
- 参照データを含むテーブルのインデックスを再構築する。

これらの操作を複数のテナント データベースにわたって実行するために、Azure SQL Database の[エラスティック ジョブ](./elastic-jobs-overview.md)機能を使用します。 このジョブは、'テンプレート' テナント データベースに対しても機能します。 Wingtip Tickets サンプル アプリでは、新しいテナント データベースをプロビジョニングするために、このテンプレート データベースがコピーされます。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * ジョブ エージェントを作成する。
> * 複数のテナント データベースで T-SQL クエリを実行する。
> * すべてのテナント データベースで参照データを更新する。
> * すべてのテナント データベース内のテーブルにインデックスを作成する。

## <a name="prerequisites"></a>前提条件

- Wingtip Tickets マルチテナント データベース アプリが、既にデプロイされていること。
    - 手順については、Wingtip Tickets SaaS マルチテナント データベース アプリケーションについて説明している最初のチュートリアル、<br />「[Azure SQL Database を使用するシャード化されたマルチテナント アプリケーションのデプロイと操作](./saas-multitenantdb-get-started-deploy.md)」をご覧ください。
        - デプロイ プロセスは 5 分未満で実行されます。
    - *シャード化されたマルチテナント* バージョンの Wingtip をインストール済みである必要があります。 *スタンドアロン* および *テナントごとのデータベース* バージョンでは、このチュートリアルはサポートされません。

- 最新バージョンの SQL Server Management Studio (SSMS) がインストールされている必要があります。 「[SSMS のダウンロードとインストール](/sql/ssms/download-sql-server-management-studio-ssms)」をご覧ください。

- Azure PowerShell がインストールされている必要があります。 詳細については、「[Azure PowerShell を使ってみる](/powershell/azure/get-started-azureps)」をご覧ください。

> [!NOTE]
> このチュートリアルでは、限定プレビューに含まれる Azure SQL Database サービスの機能 ([Elastic Database ジョブ](elastic-database-client-library.md)) を使用します。 このチュートリアルを実行する場合、ご使用のサブスクリプション ID を *SaaSFeedback\@microsoft.com* までお送りください (件名: Elastic Jobs Preview)。 サブスクリプションが有効であることを通知するメールが届いたら、[最新のプレリリース ジョブ コマンドレットをダウンロードしてインストール](https://github.com/jaredmoo/azure-powershell/releases)します。 このプレビューは限定的であるため、関連する質問やサポートについては、*SaaSFeedback\@microsoft.com* にお問い合わせください。

## <a name="introduction-to-saas-schema-management-patterns"></a>SaaS スキーマ管理パターンの概要

このサンプルで使用されているシャード化されたマルチテナント データベース モデルでは、テナント データベースに 1 つまたは複数のテナントを含めることができます。 このサンプルでは、複数テナントのデータベースと 1 テナント のデータベースを組み合わせて *ハイブリッド* テナント管理モデルを実現する可能性を探ります。 これらのデータベースへの変更の管理は、複雑になる場合があります。 [エラスティック ジョブ](./elastic-jobs-overview.md)は、大規模数のデータベースの管理をに使用されます。 ジョブにより、テナント データベースのグループに対して、Transact-SQL スクリプトをタスクとして安全かつ確実に実行できます。 このタスクはユーザーの操作や入力に依存しません。 この方法を使用して、スキーマや共通の参照データに対する変更を、アプリケーションのすべてのテナントにわたってデプロイできます。 エラスティック ジョブは、データベースのゴールデン テンプレートのコピーを保持するためにも使用できます。 このテンプレートは新しいテナントの作成に使用され、常に最新のスキーマと参照データが使用されるようにします。

![スクリーン](./media/saas-multitenantdb-schema-management/schema-management.png)

## <a name="elastic-jobs-limited-preview"></a>エラスティック ジョブの限定プレビュー

Azure SQL Database の統合機能となった新しいバージョンのエラスティック ジョブが入手可能です。 現在、この新しいバージョンのエラスティック ジョブは限定プレビュー中です。 現在、この限定プレビューでは、ジョブ エージェントを作成するための PowerShell と、ジョブを作成および管理するための T-SQL の使用がサポートされています。
> [!NOTE]
> このチュートリアルでは、限定プレビューに含まれる SQL Database サービスの機能を使用します (Elastic Database ジョブ)。 このチュートリアルを実行する場合、サブスクリプション ID を SaaSFeedback@microsoft.com までお送りください (件名: Elastic Jobs Preview)。 サブスクリプションが有効であることを通知するメールが届いたら、最新のプレリリース ジョブ コマンドレットをダウンロードしてインストールします。 このプレビューは限定的であるため、関連する質問やサポートについては、SaaSFeedback@microsoft.com にお問い合わせください。

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Wingtip Tickets SaaS マルチテナント データベース アプリケーションのソース コードとスクリプトを入手する

Wingtip Tickets SaaS マルチテナント データベースのスクリプトとアプリケーション ソース コードは、GitHub の [WingtipTicketsSaaS-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) リポジトリで入手できます。 Wingtip Tickets SaaS のスクリプトをダウンロードしてブロックを解除する手順については、[一般的なガイダンス](saas-tenancy-wingtip-app-guidance-tips.md)に関する記事をご覧ください。

## <a name="create-a-job-agent-database-and-new-job-agent"></a>ジョブ エージェント データベースと新しいジョブ エージェントの作成

このチュートリアルでは、PowerShell を使用してジョブ エージェント データベースとジョブ エージェントを作成する必要があります。 SQL Agent で使用される MSDB データベースと同様に、ジョブ エージェントは Azure SQL Database のデータベースを使用してジョブ定義、ジョブの状態、履歴を格納します。 ジョブ エージェントの作成後は、すぐにジョブを作成および監視することができます。

1. **PowerShell ISE** で、 *…\\Learning Modules\\Schema Management\\Demo-SchemaManagement.ps1* を開きます。
2. **F5** キーを押して、スクリプトを実行します。

*Demo-SchemaManagement.ps1* スクリプトは *Deploy-SchemaManagement.ps1* スクリプトを呼び出して、カタログ サーバーに _jobagent_ という名前のデータベースを作成します。 次に、スクリプトはジョブ エージェントを作成し、_jobagent_ データベースをパラメーターとして渡します。

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>新しい参照データをすべてのテナントにデプロイするジョブの作成

#### <a name="prepare"></a>準備

各テナントのデータベースの **VenueTypes** テーブルには、一連の会場タイプが含まれています。 各会場タイプは、会場で開催できるイベントの種類を定義します。 これらの会場タイプは、テナント イベント アプリに表示される背景画像に対応しています。  この演習では、すべてのデータベースに更新をデプロイして、2 つの会場タイプ *Motorcycle Racing* と *Swimming Club* を追加します。

最初に、各テナント データベースに含まれている会場の種類を確認します。 SQL Server Management Studio (SSMS) でテナント データベースの 1 つに接続し、VenueTypes テーブルを調べます。  このテーブルは、データベース ページからアクセスする Azure Portal のクエリ エディターでクエリを実行することもできます。

1. SSMS を開き、テナント サーバー *tenants1-dpt-&lt;ユーザー&gt;.database.windows.net* に接続します。
1. 現在、*Motorcycle Racing* および *Swimming Club* が **含まれていない** ことを確認するために、*tenants1-dpt-&lt;ユーザー&gt;* サーバーの *contosoconcerthall* データベースを参照し、*VenueTypes* テーブルに対してクエリを実行します。



#### <a name="steps"></a>手順

各テナント データベースの **VenueTypes** テーブルに 2 つの新しい会場タイプを追加してテーブルを更新するジョブを作成します。

新しいジョブを作成するには、_jobagent_ データベースに作成された一連のジョブ システム ストアド プロシージャを使用します。 ジョブ エージェントの作成時に、ストアド プロシージャが作成されました。

1. SSMS で、テナント サーバー tenants1-&lt;user&gt;.database.windows.net に接続します。

2. *tenants1* データベースを参照します。

3. *VenueTypes* テーブルのクエリを実行して、*Motorcycle Racing* と *Swimming Club* が結果の一覧に含まれていないことを確認します。

4. カタログ サーバー *catalog-mt-&lt;user&gt;.database.windows.net* に接続します。

5. カタログ サーバーの _jobagent_ データベースに接続します。

6. SSMS で、ファイル *...\\Learning Modules\\Schema Management\\DeployReferenceData.sql* を開きます。

7. ステートメント set @User = &lt;user&gt; を変更し、Wingtip Tickets SaaS Multi-tenant Database アプリケーションをデプロイしたときに使用した User の値に置き換えます。

8. **F5** キーを押して、スクリプトを実行します。

#### <a name="observe"></a>確認

*DeployReferenceData.sql* スクリプトで、次の項目を確認します。

- **sp\_add\_target\_group** は、ターゲット グループ名 *DemoServerGroup* を作成し、ターゲット メンバーをグループに追加します。

- **sp\_add\_target\_group\_member** は、次の項目を追加します。
    - *server* ターゲット メンバー タイプ。
        - これは、テナント データベースを含む *tenants1-mt-&lt;user&gt;* サーバーです。
        - サーバーの組み込みには、ジョブが実行された時点で存在するテナント データベースを含みます。
    - *catalog-mt-&lt;user&gt;* サーバーにある、テンプレート データベース (*basetenantdb*) の *database* ターゲット メンバー タイプ。
    - 後のチュートリアルで使用する *adhocreporting* データベースを含めるための *database* ターゲット メンバー タイプ。

- **sp\_add\_job** は、*Reference Data Deployment* というジョブを作成します。

- **sp\_add\_jobstep** は、T-SQL コマンド テキストを含むジョブ ステップを作成して、参照テーブル VenueTypes を更新します。

- スクリプトの残りのビューは、オブジェクトの存在を表示し、ジョブの実行を監視します。 これらのクエリを使用して **lifecycle** 列の状態値を調べ、ジョブがいつ終了したかを確認します。 ジョブは、テナント データベースを更新し、参照テーブルを含む 2 つの追加のデータベースを更新します。

SSMS で、*tenants1-mt-&lt;user&gt;* サーバーのテナント データベースを参照します。 *VenueTypes* テーブルのクエリを実行して、*Motorcycle Racing* と *Swimming Club* がテーブルに追加されたことを確認します。 会場タイプの総数が 2 つ増えているはずです。

## <a name="create-a-job-to-manage-the-reference-table-index"></a>参照テーブルのインデックスを管理するジョブの作成

この演習では、すべてのテナント データベースで参照テーブルの主キーにインデックスを再構築するジョブを作成します。 インデックスの再構築は、管理者が大量のデータを読み込んだ後でパフォーマンス向上のために実行できる一般的なデータベース管理操作です。

1. SSMS で、*catalog-mt-&lt;User&gt;.database.windows.net* サーバーの _jobagent_ データベースに接続します。

2. SSMS で、 *...\\Learning Modules\\Schema Management\\OnlineReindex.sql* を開きます。

3. **F5** キーを押して、スクリプトを実行します。

#### <a name="observe"></a>確認

*OnlineReindex.sql* スクリプトで次の項目を確認します。

* **sp\_add\_job** は、*Online Reindex PK\_\_VenueTyp\_\_265E44FD7FD4C885* という新しいジョブを作成します。

* **sp\_add\_jobstep** は、T-SQL コマンド テキストを含むジョブ ステップを作成して、インデックスを更新します。

* スクリプトの残りのビューは、ジョブの実行を監視します。 これらのクエリを使用して **lifecycle** 列の状態値を調べ、すべてのターゲット グループ メンバーでジョブがいつ正常に終了したかを確認します。

## <a name="additional-resources"></a>その他のリソース

<!-- TODO: Additional tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application deployment (*Tutorial link to come*)
(saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
-->
* [スケールアウトされたクラウド データベースの管理](./elastic-jobs-overview.md)

## <a name="next-steps"></a>次のステップ

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * 複数のデータベースにわたって T-SQL ジョブを実行するジョブ エージェントを作成する
> * すべてのテナント データベース内の参照データを更新する
> * すべてのテナント データベース内のテーブルにインデックスを作成する

次は、[アドホック レポートのチュートリアル](./saas-multitenantdb-adhoc-reporting.md)を試して、テナント データベース間で実行されている分散クエリを確認してください。