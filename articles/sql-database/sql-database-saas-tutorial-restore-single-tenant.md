---
title: "マルチテナント SaaS アプリで Azure SQL Database を復元する | Microsoft Docs"
description: "データを誤って削除した場合にシングル テナントの SQL データベースを復元する方法について説明します"
keywords: "SQL データベース チュートリアル"
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: billgib;sstein
ms.openlocfilehash: 792476849e796695dde3f2ec80b56431a17e8fc0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="restore-a-single-tenants-azure-sql-database-in-a-multi-tenant-saas-app"></a>マルチ テナント SaaS アプリでは、1 つのテナントの Azure SQL データベースを復元します。

Wingtip SaaS アプリは、テナントごとのデータベース モデルを使用して構築されています。このモデルでは、各テナントに独自のデータベースが用意されています。 このモデルの利点の 1 つは、他のテナントに影響を与えることなく、シングル テナントのデータを切り離して簡単に復元できることです。

このチュートリアルでは、2 つのデータ回復パターンについて説明します。

> [!div class="checklist"]

> * データベースを並列データベースに復元する
> * データベースを所定の時点に復元する


|||
|:--|:--|
| **テナントを以前の特定の時点の並列データベースに復元する** | このパターンは、校閲、監査、コンプライアンスなどの用途のテナントで使用できます。元のデータベースはオンラインのまま変更されません。 |
| **テナントを所定の時点に復元する** | 通常、このパターンは、テナントのデータを誤って削除した後で、以前の特定の時点にテナントを回復するために使用されます。 元のデータベースはオフラインになり、復元されたデータベースに置き換えられます。 |
|||

このチュートリアルを完了するには、次の前提条件を満たしておく必要があります。

* Wingtip SaaS アプリがデプロイされている。 5 分未満でデプロイするには、[Wingtip SaaS アプリケーションのデプロイと確認](sql-database-saas-tutorial.md)に関するページを参照してください。
* Azure PowerShell がインストールされている。 詳しくは、「[Azure PowerShell を使ってみる](https://docs.microsoft.com/powershell/azure/get-started-azureps)」をご覧ください。

## <a name="introduction-to-the-saas-tenant-restore-pattern"></a>SaaS テナントの復元パターンの概要

テナントの復元パターンには、個々のテナントのデータを復元するためのシンプルなパターンが 2 つあります。 テナント データベースは相互に切り離されているため、一方のテナントを復元する際に、もう一方のテナントのデータに影響を及ぼすことはありません。

最初のパターンでは、新しいデータベースにデータが復元されます。 テナントには、このデータベースと実稼動データへのアクセス権が付与されます。 このパターンでは、テナント管理者が復元されたデータを確認できます。また、そのデータを使用して、現在のデータの値を選択的に上書きできる場合もあります。 使用するデータ回復オプションのレベルは SaaS アプリ デザイナーが決定します。 一部のシナリオでは、特定の時点におけるデータの状態のみ確認できればよい場合もあります。 データベースで [geo レプリケーション](sql-database-geo-replication-overview.md)が使用されている場合は、復元されたコピーから元のデータベースに必要なデータをコピーすることをお勧めします。 元のデータベースを復元したデータベースに置き換える場合は、geo レプリケーションを再構成して再同期する必要があります。

2 番目のパターン (テナントでデータが失われたか、またはデータが破損したことが前提) では、テナントの運用データベースが以前の時点に復元されます。 所定の時点への復元パターンでは、テナントが短時間オフラインになり、データベースを復元するとオンラインに戻ります。 元のデータベースは削除されますが、さらに前の時点に戻る必要がある場合は、そのデータベースから復元可能です。 このパターンのバリエーションとして、データベースを削除する代わりにデータベース名を変更できます。ただし、データ セキュリティの観点では、データベース名の変更による追加のメリットはありません。

## <a name="get-the-wingtip-application-scripts"></a>Wingtip アプリケーションのスクリプトを取得する

Wingtip SaaS のスクリプトとアプリケーション ソース コードは、[WingtipSaaS](https://github.com/Microsoft/WingtipSaaS) GitHub リポジトリから入手できます。 [Wingtip SaaS のスクリプトをダウンロードする手順](sql-database-wtp-overview.md#download-and-unblock-the-wingtip-saas-scripts)。

## <a name="simulate-a-tenant-accidentally-deleting-data"></a>データを誤って削除した状態のテナントをシミュレートする

前述の回復シナリオを示すには、いずれかのテナント データベースで一部のデータを "*誤って*" 削除する必要があります。 任意のレコードを削除できますが、次の手順では、参照整合性違反によってブロックされないようにデモを設定します。 また、「*Wingtip SaaS Analytics のチュートリアル*」で後から使用できるチケット購入データも追加します。

チケット ジェネレーター スクリプトを実行して、追加のデータを作成します。 チケット ジェネレーターは、各テナントの最後のイベントのチケットを意図的に購入しません。

1. *PowerShell ISE* で ...\\Learning Modules\\Utilities\\*Demo-TicketGenerator.ps1* を開きます。
1. **F5** キーを押してスクリプトを実行し、顧客とチケットの売上データを生成します。


### <a name="open-the-events-app-to-review-the-current-events"></a>Events アプリを開いて現在のイベントを確認する

1. *Events Hub* (http://events.wtp.&lt;user&gt;.trafficmanager.net) を開き、**[Contoso Concert Hall]** をクリックします。

   ![Events Hub](media/sql-database-saas-tutorial-restore-single-tenant/events-hub.png)

1. イベントの一覧をスクロールして、一覧の最後のイベントをメモします。

   ![最後のイベント](media/sql-database-saas-tutorial-restore-single-tenant/last-event.png)


### <a name="run-the-demo-scenario-to-accidentally-delete-the-last-event"></a>デモ シナリオを実行し、最後のイベントを誤って削除する

1. *PowerShell ISE* で ...\\Learning Modules\\Business Continuity and Disaster Recovery\\RestoreTenant\\*Demo-RestoreTenant.ps1* を開き、次の値を設定します。
   * **$DemoScenario** = **1**。**1** に設定すると、チケットの売上がないイベントを削除します。
1. **F5** キーを押してスクリプトを実行し、最後のイベントを削除します。 次のような確認メッセージが表示されます。

   ```Console
   Deleting unsold events from Contoso Concert Hall ...
   Deleted event 'Seriously Strauss' from Contoso Concert Hall venue.
   ```

1. Contoso のイベント ページが開きます。 下へスクロールし、イベントが表示されていないことを確認します。 イベントが一覧に残っている場合は、[最新の情報に更新] をクリックして、イベントが表示されていないことを確認します。

   ![最後のイベント](media/sql-database-saas-tutorial-restore-single-tenant/last-event-deleted.png)


## <a name="restore-a-tenant-database-in-parallel-with-the-production-database"></a>運用データベースと並行してテナント データベースを復元する

ここでは、Contoso Concert Hall データベースをイベントが削除される前の時点に復元します。 前の手順でイベントが削除された後に、削除されたデータを回復して確認するとします。 削除されたレコードを含む運用データベースを復元する必要はありませんが、その他のビジネス上の理由から、古いデータにアクセスするために古いデータベースを回復する必要があります。

 *Restore-TenantInParallel.ps1* スクリプトは、並列テナント データベースと並列カタログ エントリ (どちらも *ContosoConcertHall\_old* という名前) を作成します。 この復元パターンは、小規模なデータ損失からの回復、またはコンプライアンスや監査の回復シナリオに最適です。 また、[geo レプリケーション](sql-database-geo-replication-overview.md)の使用時にも推奨されるアプローチです。

1. 「[データを誤って削除した状態のテナントをシミュレートする](#simulate-a-tenant-accidentally-deleting-data)」の手順を完了します。
1. *PowerShell ISE* で ...\\Learning Modules\\Business Continuity and Disaster Recovery\\RestoreTenant\\_Demo-RestoreTenant.ps1_ を開きます。
1. **$DemoScenario** = **2** と設定します。**2** は、"*テナントを並行して復元する*" 場合の設定です。
1. **F5** キーを押して、スクリプトを実行します。

このスクリプトは、テナント データベースを前述のセクションでイベントが削除される前の時点 (並列データベース) に復元します。 これにより、2 番目のデータベースが作成され、このデータベースに存在する既存のカタログ メタデータが削除されます。また、カタログの *ContosoConcertHall\_old* エントリにデータベースが追加されます。

デモ スクリプトでは、イベント ページをブラウザーで開きます。 URL に関するメモ: ```http://events.wtp.&lt;user&gt;.trafficmanager.net/contosoconcerthall_old```。これは、復元されたデータベースからのデータを示しており、*_old* が名前に追加されています。

ブラウザーに表示されるイベントをスクロールして、前述のセクションで削除したイベントが復元されていることを確認します。

なお、チケットを参照するための独自のイベント アプリを使用して、復元されたテナントを追加のテナントとして公開すると、復元されたデータに対するテナント アクセスが提供される可能性は低いですが、復元パターンを簡単に示すことができます。

実際には、復元されたこのデータベースを一定の期間のみ保持します。 処理の完了後に *Remove-RestoredTenant.ps1* スクリプトを呼び出すと、復元されたテナント エントリを削除できます。

1. **$DemoScenario** を **4** に設定して、"*復元されたテナントの削除*" シナリオを選択します。
1. **F5** **を使用して****実行します**。
1. これで、*ContosoConcertHall\_old* エントリがカタログから削除されました。 ブラウザーで先に進み、このテナントのイベント ページを閉じます。


## <a name="restore-a-tenant-in-place-replacing-the-existing-tenant-database"></a>テナントを所定の時点に復元し、既存のテナント データベースを置き換える

ここでは、Contoso Concert Hall テナントをイベントが削除される前の時点に復元します。 *Restore-TenantInPlace* スクリプトは、現在のテナント データベースを、以前の時点を指す新しいデータベースに復元し、元のデータベースを削除します。 この復元パターンは、重大なデータ破損からの回復に最適です (テナントで対応が必要となる重大なデータ損失が発生する可能性があるため)。

1. PowerShell ISE で **Demo-RestoreTenant.ps1** ファイルを開きます。
1. **$DemoScenario** を **5** に設定して、"*所定の時点へのテナントの復元シナリオ*" を選択します。
1. **F5** を使用して実行します。

このスクリプトは、イベントが削除される 5 分前の時点にテナント データベースを復元します。 そのためには、最初に Contoso Concert Hall テナントをオフラインにして、データが更新されないようにします。 次に、復元ポイントからの復元によって並列データベースが作成され、データベース名が既存のテナント データベース名と競合しないように、タイムスタンプを使用して名前が指定されます。 続いて、古いテナント データベースが削除され、復元されたデータベースの名前が元のデータベース名に変更されます。 最後に、Contoso Concert Hall がオンラインに戻り、復元されたデータベースへのアプリからのアクセスが許可されます。

これで、イベントが削除される前の時点にデータベースが正常に復元されました。 イベント ページを開き、最後のイベントが復元されていることを確認してください。


## <a name="next-steps"></a>次のステップ

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]

> * データベースを並列データベースに復元する
> * データベースを所定の時点に復元する

[テナント データベースのスキーマの管理に関するページ](sql-database-saas-tutorial-schema-management.md)

## <a name="additional-resources"></a>その他のリソース

* [Wingtip SaaS アプリケーションに基づく作業のための追加のチュートリアル](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* [Azure SQL Database によるビジネス継続性の概要](sql-database-business-continuity.md)
* [SQL Database バックアップについての詳細情報](sql-database-automated-backups.md)
