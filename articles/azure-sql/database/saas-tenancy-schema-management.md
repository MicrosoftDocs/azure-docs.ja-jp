---
title: シングルテナント アプリでのスキーマの管理
description: Azure SQL Database を使用するシングルテナント アプリで複数のテナントのスキーマを管理します
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/19/2018
ms.openlocfilehash: e4328be0aade0658dedb034dbbb6980b810f771a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92793196"
---
# <a name="manage-schema-in-a-saas-application-using-the-database-per-tenant-pattern-with-azure-sql-database"></a>テナントごとのデータベース パターンを使用した Azure SQL Database での SaaS アプリケーション内のスキーマの管理
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]
 
データベース アプリケーションの進化に伴って、データベース スキーマまたは参照データに対する変更が必然的に必要になります。  データベースのメンテナンス タスクも、定期的に必要です。 テナントごとのデータベース パターンを使用するアプリケーションの管理では、複数のテナント データベースにこれらの変更やメンテナンス タスクを適用する必要があります。

このチュートリアルでは、2 つのシナリオを扱います。すべてのテナントに対する参照データの更新のデプロイと、参照データを含むテーブルのインデックスの再構築です。 [エラスティック ジョブ](./elastic-jobs-overview.md)機能を使用して、すべてのテナント データベースと、新しいテナント データベースを作成するために使用されるテンプレート データベースに対してこれらのアクションを実行します。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> 
> * ジョブ エージェントを作成する
> * T-SQL ジョブをすべてのテナント データベースに対して実行する
> * すべてのテナント データベース内の参照データを更新する
> * すべてのテナント データベース内のテーブルにインデックスを作成する


このチュートリアルを完了するには、次の前提条件を満たしておく必要があります。

* Wingtip Tickets SaaS Database Per Tenant アプリをデプロイします。 5 分未満でデプロイするには、[テナントごとのデータベースを使用する Wingtip Tickets SaaS アプリケーションのデプロイと探索](./saas-dbpertenant-get-started-deploy.md)に関する記事を参照してください。
* Azure PowerShell がインストールされている。 詳しくは、「[Azure PowerShell を使ってみる](/powershell/azure/get-started-azureps)」をご覧ください。
* 最新バージョンの SQL Server Management Studio (SSMS) のインストール。 [SSMS をダウンロードしてインストールします](/sql/ssms/download-sql-server-management-studio-ssms)。


## <a name="introduction-to-saas-schema-management-patterns"></a>SaaS スキーマ管理パターンの概要

テナントごとのデータベース パターンは、テナントのデータを効率的に分離しますが、管理して維持するデータベースの数を増やします。 [エラスティック ジョブ](./elastic-jobs-overview.md)は、複数のデータベースの管理を容易にします。 このジョブを使用して、データベースのグループに対して、タスク (T-SQL スクリプト) を安全かつ確実に実行できます。 ジョブにより、スキーマと一般的な参照データの変更を、アプリケーションのすべてのテナント データベースにわたってデプロイできます。 エラスティック ジョブを使用して、新しいテナントの作成に使用される "*テンプレート*" データベースを管理して、スキーマと参照データを常に最新の状態に保つこともできます。

![スクリーン](./media/saas-tenancy-schema-management/schema-management-dpt.png)


## <a name="elastic-jobs-public-preview"></a>エラスティック ジョブのパブリック プレビュー

Azure SQL Database の統合機能となった新しいバージョンのエラスティック ジョブが入手可能です。 現在、この新しいバージョンのエラスティック ジョブはパブリック プレビュー中です。 現在、このパブリック プレビューでは、ジョブ エージェントを作成するための PowerShell と、ジョブを作成および管理するための T-SQL の使用がサポートされています。
詳細については、[Elastic Database ジョブ](./elastic-jobs-overview.md)に関する記事を参照してください。

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>テナントごとのデータベースを使用する Wingtip Tickets SaaS アプリケーションのスクリプトの入手

アプリケーションのソース コードと管理スクリプトは、[WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub リポジトリから入手できます。 Wingtip Tickets SaaS スクリプトをダウンロードし、ブロックを解除する手順については、[一般的なガイダンス](saas-tenancy-wingtip-app-guidance-tips.md)に関する記事をご覧ください。

## <a name="create-a-job-agent-database-and-new-job-agent"></a>ジョブ エージェント データベースと新しいジョブ エージェントの作成

このチュートリアルでは、PowerShell を使用してジョブ エージェントとバッキング ジョブ エージェント データベースを作成する必要があります。 ジョブ エージェント データベースは、ジョブの定義、ジョブの状態、および履歴を保持します。 ジョブ エージェントとそのデータベースを作成したら、すぐにジョブを作成して監視できます。

1. **PowerShell ISE** で、…\\Learning Modules\\Schema Management\\*Demo-SchemaManagement.ps1* を開きます。
1. **F5** キーを押して、スクリプトを実行します。

*Demo-SchemaManagement.ps1* スクリプトでは、*Deploy-SchemaManagement.ps1* スクリプトが呼び出されて、カタログ サーバーに *osagent* という名前のデータベースが作成されます。 次に、このデータベースをパラメーターとして使用して、ジョブ エージェントを作成します。

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>新しい参照データをすべてのテナントにデプロイするジョブの作成

Wingtip Tickets アプリでは、各テナント データベースに、サポートされている一連の会場の種類が含まれています。 各会場には、ホストできるイベントの種類を定義し、アプリで使用される背景画像を決定する特定の会場の種類があります。 新しい種類のイベントをサポートするアプリケーションでは、この参照データを更新して、新しい会場の種類を追加する必要があります。  この演習では、すべてのテナント データベースに更新をデプロイして、2 つの会場タイプ *Motorcycle Racing* と *Swimming Club* を追加します。

最初に、各テナント データベースに含まれている会場の種類を確認します。 SQL Server Management Studio (SSMS) でテナント データベースの 1 つに接続し、VenueTypes テーブルを調べます。  このテーブルは、データベース ページからアクセスする Azure Portal のクエリ エディターでクエリを実行することもできます。 

1. SSMS を開き、テナント サーバー *tenants1-dpt-&lt;ユーザー&gt;.database.windows.net* に接続します。
1. 現在、*Motorcycle Racing* および *Swimming Club* が **含まれていない** ことを確認するために、*tenants1-dpt-&lt;ユーザー&gt;* サーバーの _contosoconcerthall_ データベースを参照し、*VenueTypes* テーブルに対してクエリを実行します。

それでは、すべてのテナント データベースの *VenueTypes* テーブルを更新するジョブを作成し、新しい会場の種類を追加してみましょう。

新しいジョブを作成するには、ジョブ エージェントの作成時に _jobagent_ データベース内に作成された一連のジョブ システム ストアド プロシージャを使用します。

1. SSMS で、カタログ サーバー *catalog-dpt-&lt;ユーザー&gt;.database.windows.net* サーバーに接続します 
1. SSMS で、ファイル …\\Learning Modules\\Schema Management\\DeployReferenceData.sql を開きます
1. ステートメントを次のように変更します:SET @wtpUser = &lt;ユーザー&gt;。ユーザーの値は、Wingtip Tickets SaaS Database Per Tenant アプリをデプロイしたときに使用したユーザーの値に置き換えます
1. _jobagent_ データベースに接続していることを確認し、**F5** キーを押してスクリプトを実行します。

*DeployReferenceData.sql* スクリプトで、次の要素を確認します。
* **sp\_add\_target\_group** は、DemoServerGroup という名前のターゲット グループを作成します。
* **sp\_add\_target\_group\_member** を使用して、ターゲット データベースのセットが定義されます。  最初に _tenants1-dpt-&lt;ユーザー&gt;_ サーバーが追加されます。  ターゲットとしてサーバーを追加すると、ジョブの実行時にそのサーバーに存在するデータベースがジョブに含まれるようになります。 その後、_basetenantdb_ データベースと *adhocreporting* データベース (後のチュートリアルで使用されます) がターゲットとして追加されます。
* **sp\_add\_job** は、_Reference Data Deployment_ という名前のジョブを作成します。
* **sp\_add\_jobstep** は、T-SQL コマンド テキストを含むジョブ ステップを作成して、参照テーブル VenueTypes を更新します。
* スクリプトの残りのビューは、オブジェクトの存在を表示し、ジョブの実行を監視します。 これらのクエリを使用して **lifecycle** 列の状態値を調べ、すべてのターゲット データベースでジョブがいつ終了したかを確認します。

スクリプトが完了したら、参照データが更新されていることを確認できます。  SSMS で、*tenants1-dpt-&lt;ユーザー&gt;* サーバーの *contosoconcerthall* データベースを参照し、*VenueTypes* テーブルに対してクエリを実行します。  *Motorcycle Racing* と *Swimming Club* が **存在している** ことを確認します。


## <a name="create-a-job-to-manage-the-reference-table-index"></a>参照テーブルのインデックスを管理するジョブの作成

この演習では、参照テーブルの主キーにインデックスを再構築するジョブを使用します。  これは、大量のデータを読み込んだ後に実行される一般的なデータベース メンテナンス操作です。

同じジョブ 'システム' ストアド プロシージャを使用したジョブの作成

1. SSMS を開き、_catalog-dpt-&lt;ユーザー&gt;.database.windows.net_ サーバーに接続します
1. _…\\Learning Modules\\Schema Management\\OnlineReindex.sql_ ファイルを開きます。
1. 右クリックして [接続] を選択し、_catalog-dpt-&lt;ユーザー&gt;.database.windows.net_ サーバーに接続します (まだ接続していない場合)。
1. _jobagent_ データベースに接続していることを確認し、**F5** キーを押してスクリプトを実行します。

_OnlineReindex.sql_ スクリプトで次の要素を確認します。
* **sp\_add\_job** は、"Online Reindex PK\_\_VenueTyp\_\_265E44FD7FD4C885" という新しいジョブを作成します
* **sp\_add\_jobstep** は、T-SQL コマンド テキストを含むジョブ ステップを作成して、インデックスを更新します
* スクリプトの残りのビューは、ジョブの実行を監視します。 これらのクエリを使用して **lifecycle** 列の状態値を調べ、すべてのターゲット グループ メンバーでジョブがいつ正常に終了したかを確認します。



## <a name="next-steps"></a>次のステップ

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> 
> * 複数のデータベースで T-SQL ジョブを実行するジョブ エージェントを作成する
> * すべてのテナント データベース内の参照データを更新する
> * すべてのテナント データベース内のテーブルにインデックスを作成する

次は、[アドホック レポートのチュートリアル](./saas-tenancy-cross-tenant-reporting.md)を試して、テナント データベース間で実行されている分散クエリを確認してください。


## <a name="additional-resources"></a>その他のリソース

* [Wingtip Tickets SaaS Database Per Tenant アプリケーションのデプロイに基づく作業のための追加のチュートリアル](./saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [スケールアウトされたクラウド データベースの管理](./elastic-jobs-overview.md)