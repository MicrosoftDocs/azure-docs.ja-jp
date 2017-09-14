---
title: "マルチテナント アプリで Azure SQL Database スキーマを管理する | Microsoft Docs"
description: "Azure SQL Database を使用するマルチテナント アプリケーションで複数のテナントのスキーマを管理します"
keywords: "SQL データベース チュートリアル"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/28/2017
ms.author: billgib; sstein
ms.translationtype: HT
ms.sourcegitcommit: 6e76ac40e9da2754de1d1aa50af3cd4e04c067fe
ms.openlocfilehash: 78d76efb88bf11fa18a416b59e6f881539141232
ms.contentlocale: ja-jp
ms.lasthandoff: 07/31/2017

---
# <a name="manage-schema-for-multiple-tenants-in-the-wingtip-saas-application"></a>Wingtip SaaS アプリケーションでの複数テナントのスキーマの管理

[最初の Wingtip SaaS チュートリアル](sql-database-saas-tutorial.md)では、アプリがテナント データベースをどのようにプロビジョニングし、カタログに登録するかを示します。 他のアプリケーション同様、Wingtip SaaS アプリは時間の経過に従って進化し、データベースへの変更が必要になる場合があります。 変更には、新しいまたは変更されたスキーマ、新しいまたは変更された参照データ、およびアプリの最適なパフォーマンスを得るための定期的なデータベース メンテナンス タスクなどがあります。 SaaS アプリケーションにより、これらの変更は、大容量になる可能性のあるテナント データベース全体に体系的な方法でデプロイされる必要があります。 これらの変更が将来のテナント データベースに含まれるようにするには、プロビジョニング プロセスに変更を組み込む必要があります。

このチュートリアルでは、2 つのシナリオを扱います。すべてのテナントに対する参照データのデプロイと、参照データを含むテーブルでのインデックスの再調整です。 [エラスティック ジョブ](sql-database-elastic-jobs-overview.md)機能を使用して、これらの操作をすべてのテナントにわたって実行したり、新しいデータベースのテンプレートとして使用される "*ゴールデン*" テナント データベースに対して実行したりします。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]

> * ジョブ アカウントを作成する
> * 複数のテナントにわたってクエリを実行する
> * すべてのテナント データベースでデータを更新する
> * すべてのテナント データベース内のテーブルにインデックスを作成する


このチュートリアルを完了するには、次の前提条件を満たしておく必要があります。

* Wingtip SaaS アプリがデプロイされている。 5 分未満でデプロイするには、[Wingtip SaaS アプリケーションのデプロイと確認](sql-database-saas-tutorial.md)に関するページを参照してください。
* Azure PowerShell がインストールされている。 詳しくは、「[Azure PowerShell を使ってみる](https://docs.microsoft.com/powershell/azure/get-started-azureps)」をご覧ください。
* 最新バージョンの SQL Server Management Studio (SSMS) のインストール。 [SSMS のダウンロードとインストール](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)

"*このチュートリアルでは、限定プレビューに含まれる SQL Database サービスの機能を使用します (Elastic Database ジョブ)。このチュートリアルを実行する場合、サブスクリプション ID を SaaSFeedback@microsoft.com までお送りください (件名: Elastic Jobs Preview)。サブスクリプションが有効であることを通知するメールが届いたら、[最新のプレリリース ジョブ コマンドレットをダウンロードしてインストール](https://github.com/jaredmoo/azure-powershell/releases)します。このプレビューは限定的であるため、関連する質問やサポートについては、SaaSFeedback@microsoft.com にお問い合わせください。*


## <a name="introduction-to-saas-schema-management-patterns"></a>SaaS スキーマ管理パターンの概要

データベースごとのテナントが 1 つの場合、SaaS パターンでは、多数のデータベースの維持および管理がより複雑になるデータの分離から多くの点でメリットを得られます。 [エラスティック ジョブ](sql-database-elastic-jobs-overview.md)を使用すると、SQL データ層の管理が容易になります。 ジョブでは、ユーザーの対話操作または入力とは関係なく、データベースのグループに対して、タスク (T-SQL スクリプト) を安全かつ確実に実行できます。 このメソッドを使用して、スキーマおよび一般的な参照データの変更を、アプリケーションのすべてのテナントにわたってデプロイできます。 また、エラスティック ジョブを使用すると、新しいテナントの作成に使用されるデータベースの "*ゴールデン*" コピーを維持できます。これにより、スキーマおよび参照データを常に最新の状態に保つことができます。

![スクリーン](media/sql-database-saas-tutorial-schema-management/schema-management.png)


## <a name="elastic-jobs-limited-preview"></a>エラスティック ジョブの限定プレビュー

Azure SQL Database の統合機能となったエラスティック ジョブの新しいバージョンが入手可能です (追加のサービスまたはコンポーネントを必要としない)。 現在、この新しいバージョンのエラスティック ジョブは限定プレビュー中です。 現在、この限定プレビューでは、ジョブ アカウントを作成するための PowerShell と、ジョブを作成および管理するための T-SQL がサポートされています。

> [!NOTE]
> "*このチュートリアルでは、限定プレビューに含まれる SQL Database サービスの機能を使用します (Elastic Database ジョブ)。このチュートリアルを実行する場合、サブスクリプション ID を SaaSFeedback@microsoft.com までお送りください (件名: Elastic Jobs Preview)。サブスクリプションが有効であることを通知するメールが届いたら、[最新のプレリリース ジョブ コマンドレットをダウンロードしてインストール](https://github.com/jaredmoo/azure-powershell/releases)します。このプレビューは限定的であるため、関連する質問やサポートについては、SaaSFeedback@microsoft.com にお問い合わせください。*

## <a name="get-the-wingtip-application-scripts"></a>Wingtip アプリケーションのスクリプトを取得する

Wingtip SaaS のスクリプトとアプリケーション ソース コードは、[WingtipSaaS](https://github.com/Microsoft/WingtipSaaS) GitHub リポジトリから入手できます。 [Wingtip SaaS のスクリプトをダウンロードする手順](sql-database-wtp-overview.md#download-and-unblock-the-wingtip-saas-scripts)。

## <a name="create-a-job-account-database-and-new-job-account"></a>ジョブ アカウント データベースと新しいジョブ アカウントの作成

このチュートリアルでは、PowerShell を使用して、ジョブ アカウント データベースとジョブ アカウントを作成する必要があります。 MSDB や SQL エージェントと同様、エラスティック ジョブは Azure SQL データベースを使用して、ジョブ定義、ジョブの状態、履歴を格納します。 ジョブ アカウントが作成されたら、すぐにジョブを作成および監視することができます。

1. **PowerShell ISE** で …\\Learning Modules\\Schema Management\\*Demo-SchemaManagement.ps1* を開きます。
1. **F5** キーを押して、スクリプトを実行します。

*Demo-SchemaManagement.ps1* スクリプトは *Deploy-SchemaManagement.ps1* スクリプトを呼び出して、カタログ サーバーに **jobaccount** という名前の *S2* データベースを作成します。 次に、スクリプトはジョブ アカウントを作成します。これにより jobaccount データベースがパラメーターとしてジョブ アカウント作成呼び出しに渡されます。

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>新しい参照データをすべてのテナントにデプロイするジョブの作成

各テナント データベースには、会場でホストされているイベントの種類を定義する会場タイプ セットが含まれています。 この演習では、すべてのテナント データベースに更新プログラムをデプロイして、次の 2 つの会場タイプを追加します: *Motorcycle Racing*、*Swimming Club*。 これらの会場タイプは、テナント イベント アプリに表示される背景画像に対応しています。

[会場のタイプ] ドロップダウン メニューをクリックし、10 個の会場タイプ オプションのみが利用可能であること、また Motorcycle Racing と Swimming Club が一覧に含まれていないことを確認します。

それでは、すべてのテナント データベースの *VenueTypes* テーブルを更新するジョブを作成し、新しい会場タイプを追加してみましょう。

新しいジョブを作成するには、ジョブ アカウントが作成されたときに jobaccount データベースに作成された一連のジョブ システム ストアド プロシージャを使用します。

1. SSMS を開き、カタログ サーバー catalog-\<user\>.database.windows.net server に接続します。
1. さらに、テナント サーバー tenants1-\<user\>.database.windows.net に接続します。
1. *tenants1* サーバーで *contosoconcerthall* データベースを参照し、*VenueTypes* テーブルにクエリを実行して *Motorcycle Racing* および *Swimming Club* が結果の一覧に**含まれていない**ことを確認します。
1. ファイル …\\Learning Modules\\Schema Management\\DeployReferenceData.sql を開きます。
1. ステートメント SET @wtpUser = &lt;user&gt; を変更し、Wingtip アプリをデプロイしたときに使用した User の値に置き換えます。
1. jobaccount データベースに接続していることを確認し、**F5** キーを押してスクリプトを実行します。

* **sp\_add\_target\_group** は、ターゲット グループ名 DemoServerGroup を作成します。次に、ターゲット メンバーを追加する必要があります。
* **sp\_add\_target\_group\_member** は、*server* ターゲット メンバー タイプを追加します。これがジョブに含まれると、ジョブの実行時に、そのサーバー (テナント データベースを含む tenants1-&lt;User&gt; サーバー) 内のすべてのデータベースがジョブに含まれます。次に、*database* ターゲット メンバー タイプ、具体的には "ゴールデン" データベース (basetenantdb) を追加します。これは、catalog-&lt;User&gt; サーバーにあります。最後に、他の *database* ターゲット グループ メンバー タイプを追加して、後のチュートリアルで使用される adhocanalytics データベースを含めます。
* **sp\_add\_job** は、"Reference Data Deployment" というジョブを作成します。
* **sp\_add\_jobstep** は、T-SQL コマンド テキストを含むジョブ ステップを作成して、参照テーブル VenueTypes を更新します。
* スクリプトの残りのビューは、オブジェクトの存在を表示し、ジョブの実行を監視します。 これらのクエリを使用して **lifecycle** 列の状態値を調べ、すべてのテナント データベースおよび参照テーブルを含むその他 2 つのデータベースで、ジョブがいつ正常に終了したかを確認します。

1. SSMS で、*tenants1* サーバーで *contosoconcerthall* データベースを参照し、*VenueTypes* テーブルにクエリを実行して *Motorcycle Racing* および *Swimming Club* が結果の一覧に**含まれている**ことを確認します。


## <a name="create-a-job-to-manage-the-reference-table-index"></a>参照テーブルのインデックスを管理するジョブの作成

前の演習と同様に、この演習では参照テーブルの主キーにインデックスを再構築するジョブを作成します。これは、テーブルへの大量データ ロードの後に管理者が実行する可能性のある一般的なデータベース管理操作です。

同じジョブ 'システム' ストアド プロシージャを使用したジョブの作成

1. SSMS を開き、catalog-&lt;User&gt;.database.windows.net サーバーに接続します。
1. ファイル …\\Learning Modules\\Schema Management\\OnlineReindex.sql を開きます。
1. 右クリックして [接続] を選択し、catalog-&lt;User&gt;.database.windows.net サーバーに接続します (まだ接続していない場合)。
1. jobaccount データベースに接続していることを確認し、F5 キーを押してスクリプトを実行します。

* sp\_add\_job は、"Online Reindex PK\_\_VenueTyp\_\_265E44FD7FD4C885" という新しいジョブを作成します。
* sp\_add\_jobstep は、T-SQL コマンド テキストを含むジョブ ステップを作成して、インデックスを更新します。




## <a name="next-steps"></a>次のステップ

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]

> * 複数のテナントにわたってクエリを実行するジョブ アカウントを作成する
> * すべてのテナント データベースでデータを更新する
> * すべてのテナント データベース内のテーブルにインデックスを作成する

[アドホック分析の実行のチュートリアル](sql-database-saas-tutorial-adhoc-analytics.md)


## <a name="additional-resources"></a>その他のリソース

* [Wingtip SaaS アプリケーションのデプロイに基づく作業のための追加のチュートリアル](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* [スケールアウトされたクラウド データベースの管理](sql-database-elastic-jobs-overview.md)
* [スケールアウトしたクラウド データベースの作成と管理](sql-database-elastic-jobs-create-and-manage.md)

