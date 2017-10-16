---
title: "複数の Azure SQL データベースに対する分析クエリの実行 | Microsoft Docs"
description: "オフライン分析用にテナント データベースから分析データベースにデータを抽出する"
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
ms.date: 06/16/2017
ms.author: billgib; sstein
ms.openlocfilehash: 4e32407d5f321198358e07980907c3420aaf56c6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="extract-data-from-tenant-databases-into-an-analytics-database-for-offline-analysis"></a>オフライン分析用にテナント データベースから分析データベースにデータを抽出する

このチュートリアルでは、エラスティック ジョブを使用して各テナント データベースに対してクエリを実行します。 このジョブでは、チケットの売上データを抽出し、分析用に分析データベース (またはデータ ウェアハウス) に読み込みます。 その後、分析データベースにクエリを実行して、すべてのテナントの日々の運用データからインサイトを抽出します。


このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * テナント分析データベースを作成する
> * データの取得と分析データベースへのデータ設定を行うスケジュール済みジョブを作成する

このチュートリアルを完了するには、次の前提条件を満たしておく必要があります。

* Wingtip SaaS アプリがデプロイされている。 5 分未満でデプロイするには、[Wingtip SaaS アプリケーションのデプロイと確認](sql-database-saas-tutorial.md)に関するページを参照してください。
* Azure PowerShell がインストールされている。 詳しくは、「[Azure PowerShell を使ってみる](https://docs.microsoft.com/powershell/azure/get-started-azureps)」をご覧ください。
* 最新バージョンの SQL Server Management Studio (SSMS) のインストール。 [SSMS をダウンロードしてインストールします](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)。

## <a name="tenant-operational-analytics-pattern"></a>テナント運用分析パターン

SaaS アプリケーションで得られる優れた機会の 1 つは、クラウドで保存している豊富なテナント データを使用することです。 このデータを使用して、アプリケーションの運用と使用状況やテナントに関するインサイトを入手します。 このデータは、機能の開発、操作性の改良、およびアプリとプラットフォームへのその他の投資に役立ちます。 単一のマルチテナント データベース内にある場合、このデータの入手は容易です。ただし、ともすれば数千ものデータベースに及ぶ規模で分散されている場合には簡単ではありません。 このデータを入手するための 1 つのアプローチがエラスティック ジョブの使用です。これにより、ジョブを実行して結果を返すクエリを、出力データベースとテーブルにキャプチャすることができます。

## <a name="get-the-wingtip-application-scripts"></a>Wingtip アプリケーションのスクリプトを取得する

Wingtip SaaS のスクリプトとアプリケーション ソース コードは、[WingtipSaaS](https://github.com/Microsoft/WingtipSaaS) GitHub リポジトリから入手できます。 [Wingtip SaaS のスクリプトをダウンロードする手順](sql-database-wtp-overview.md#download-and-unblock-the-wingtip-saas-scripts)。

## <a name="deploy-a-database-for-tenant-analytics-results"></a>テナント分析結果用のデータベースをデプロイする

このチュートリアルでは、スクリプト (結果を返すクエリが含まれる) のジョブ実行の結果をキャプチャするためにデータベースをデプロイする必要があります。 このために tenantanalytics という名前のデータベースを作成します。

1. …\\Learning Modules\\Operational Analytics\\Tenant Analytics\\*Demo-TenantAnalyticsDB.ps1* を *PowerShell ISE* で開き、次の値を設定します。
   * **$DemoScenario** = **2** "*運用分析データベースをデプロイ*"
1. **F5** キーを押して、テナント分析データベースを作成するデモ スクリプト (*Deploy-TenantAnalyticsDB.ps1* スクリプトを呼び出す) を実行します。

## <a name="create-some-data-for-the-demo"></a>デモ用のデータを作成する

1. …\\Learning Modules\\Operational Analytics\\Tenant Analytics\\*Demo-TenantAnalyticsDB.ps1* を *PowerShell ISE* で開き、次の値を設定します。
   * **$DemoScenario** = **1** "*すべての会場のイベントのチケットを購入*"
1. **F5** キーを押して、スクリプトを実行し、チケット購入履歴を作成します。


## <a name="create-a-scheduled-job-to-retrieve-tenant-analytics-about-ticket-purchases"></a>チケット購入に関するテナント分析を取得するスケジュール済みジョブを作成する

このスクリプトでは、すべてのテナントのチケット購入情報を取得するジョブを作成します。 1 つのテーブルにデータが集計されると、テナント全体のチケット購入パターンについてインサイトに富んだメトリックを得ることができます。

1. SSMS を開き、catalog-&lt;user&gt;.database.windows.net サーバーに接続します。
1. ...\\Learning Modules\\Operational Analytics\\Tenant Analytics\\*TicketPurchasesfromAllTenants.sql* を開きます。
1. &lt;User&gt; を変更します。これには、スクリプト先頭部分の **sp\_add\_target\_group\_member** と **sp\_add\_jobstep** で Wingtip SaaS アプリをデプロイしたときに使用したユーザー名を使用します。
1. 右クリックして **[接続]** を選択し、catalog-&lt;User&gt;.database.windows.net サーバーに接続します (まだ接続していない場合)。
1. **jobaccount** データベースに接続していることを確認し、**F5** キーを押してスクリプトを実行します。

* **sp\_add\_target\_group** では、ターゲット グループ名 *TenantGroup* が作成されます。次は、ターゲット メンバーを追加する必要があります。
* **sp\_add\_target\_group\_member** は、*server* ターゲット メンバー タイプを追加します。これがジョブに含まれると、ジョブの実行時に、そのサーバー (テナント データベースを含む customer1-&lt;User&gt; サーバー) 内のすべてのデータベースを対象とします。
* **sp\_add\_job** は、毎週スケジュール設定される “Ticket Purchases from all Tenants” という新しいジョブを作成します。
* **sp\_add\_jobstep** は、すべてのテナントのチケット購入情報を取得して、返される結果セットを *AllTicketsPurchasesfromAllTenants* というテーブルにコピーする T-SQL コマンド テキストを含むジョブ ステップを作成します。
* スクリプトの残りのビューは、オブジェクトの存在を表示し、ジョブの実行を監視します。 状態を監視する **lifecycle** 列で状態の値を確認してください。 「Succeeded」であれば、ジョブがすべてのテナント データベースと、参照テーブルを含む 2 つの追加データベースで完了しています。

スクリプトが正常に実行されると次のような結果になります。

![results](media/sql-database-saas-tutorial-tenant-analytics/ticket-purchases-job.png)

## <a name="create-a-job-to-retrieve-a-summary-count-of-ticket-purchases-from-all-tenants"></a>すべてのテナントのチケット購入の集計数を取得するジョブを作成する

このスクリプトでは、すべてのテナントのすべてのチケット購入の合計を取得するジョブを作成します。

1. SSMS を開き、*catalog-&lt;User&gt;.database.windows.net* サーバーに接続します。
1. ファイル …\\Learning Modules\\Provision and Catalog\\Operational Analytics\\Tenant Analytics\\*Results-TicketPurchasesfromAllTenants.sql* を開きます。
1. &lt;User&gt; を変更します。これには、スクリプトの **sp\_add\_jobstep** ストアド プロシージャで Wingtip SaaS アプリをデプロイしたときに使用したユーザー名を使用します。
1. 右クリックして **[接続]** を選択し、catalog-&lt;User&gt;.database.windows.net サーバーに接続します (まだ接続していない場合)。
1. **tenantanalytics** データベースに接続していることを確認し、**F5** キーを押してスクリプトを実行します。

スクリプトが正常に実行されると次のような結果になります。

![results](media/sql-database-saas-tutorial-tenant-analytics/total-sales.png)



* **sp\_add\_job** は、毎週スケジュールされた “ResultsTicketsOrders” という新しいジョブを作成します。

* **sp\_add\_jobstep** は、すべてのテナントのチケット購入情報を取得して、返される結果セットを CountofTicketOrders というテーブルにコピーする T-SQL コマンド テキストを含むジョブ ステップを作成します。

* スクリプトの残りのビューは、オブジェクトの存在を表示し、ジョブの実行を監視します。 状態を監視する **lifecycle** 列で状態の値を確認してください。 「Succeeded」であれば、ジョブがすべてのテナント データベースと、参照テーブルを含む 2 つの追加データベースで完了しています。


## <a name="next-steps"></a>次のステップ

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * テナント分析データベースをデプロイする
> * テナント全体の分析データを取得するスケジュール済みジョブを作成する

ご利用ありがとうございます。

## <a name="additional-resources"></a>その他のリソース

* [Wingtip SaaS アプリケーションに基づく作業のための追加のチュートリアル](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* [エラスティック ジョブ](sql-database-elastic-jobs-overview.md)
