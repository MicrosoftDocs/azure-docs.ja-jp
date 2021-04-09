---
title: マルチテナント SaaS アプリでデータベースを復元する
description: データを誤って削除した場合にシングル テナントの Azure SQL Database を復元する方法について説明します
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/04/2018
ms.openlocfilehash: 88496a39b0186cefb7c64e227530b5d73e693094
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92780480"
---
# <a name="restore-a-single-tenant-with-a-database-per-tenant-saas-application"></a>テナント単位データベース SaaS アプリケーションで、シングル テナントを復元します。
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

テナント単位データベース モデルは、他のテナントに影響を与えずにシングル テナントを以前の特定の時点まで簡単に復元することを可能にします。

このチュートリアルでは、2 つのデータ回復パターンについて説明します。

> [!div class="checklist"]
> * データベースを並列データベース (サイド バイ サイド) に復元する。
> * 特定の場所にデータベースを復元し、既存のデータベースと置換する。

| Pattern | 説明 |
|:--|:--|
| 並列データベースに復元する | このパターンは、レビュー、監査、コンプライアンスなどのタスクに使用され、テナントで以前の時点からのデータを調査できるようにします。 テナントの現在のデータベースはオンラインのままで、変化しません。 |
| 特定の場所に復元する | このパターンは通常、テナントのデータを誤って削除または破損した後で、以前の特定の時点にテナントを回復するために使用されます。 元のデータベースはオフラインになり、復元されたデータベースに置き換えられます。 |
|||

このチュートリアルを完了するには、次の前提条件を満たしておく必要があります。

* Wingtip SaaS アプリがデプロイされている。 5 分未満でデプロイする方法については、[Wingtip SaaS アプリケーションのデプロイと確認](./saas-dbpertenant-get-started-deploy.md)に関するページを参照してください。
* Azure PowerShell がインストールされている。 詳細については、「[Azure PowerShell を使ってみる](/powershell/azure/get-started-azureps)」を参照してください。

## <a name="introduction-to-the-saas-tenant-restore-patterns"></a>SaaS テナントの復元パターンの概要

個々のテナントのデータを復元するためのシンプルなパターンが 2 つあります。 テナント データベースは相互に切り離されているため、一方のテナントを復元する際に、もう一方のテナントのデータに影響を及ぼすことはありません。 Azure SQL Database のポイント イン タイム リストア (PITR) 機能は、両方のパターンで使用されます。 PITR は常に、新しいデータベースを作成します。

* **並列での復元**: 最初のパターンでは、テナントの現在のデータベースの隣に、新しい並列データベースが作成されます。 その後、テナントには、復元されたデータベースに対する読み取り専用アクセスが付与されます。 復元されたデータはレビューされ、現在のデータ値を上書きするために潜在的に使用されます。 テナントによる復元されたデータベースへのアクセス方法や提供される復元用オプションを決定するのは、アプリ デザイナーの担当です。 一部のシナリオでは、テナントで以前の時点のデータを簡単に確認可能であることが、唯一の要件になる場合もあります。

* **特定の場所への復元**: 2 つ目のパターンは、データが損失または破損し、テナントで以前の時点まで戻す場合に役立ちます。 データベースの復元中は、テナントがオフラインになります。 元のデータベースが削除され、復元されたデータベースの名前が変更されます。 元のデータベースのバックアップ チェーンは、削除後もアクセス可能なままになり、必要に応じて、以前の時点までデータベースを復元することができます。

データベースで[アクティブ geo レプリケーション](active-geo-replication-overview.md)が使用されて並行して復元される場合は、復元されたコピーから元のデータベースに必要なデータをコピーすることをお勧めします。 元のデータベースを復元したデータベースに置き換える場合は、geo レプリケーションを再構成して再同期する必要があります。

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Wingtip Tickets SaaS テナント単位データベース アプリケーションのスクリプトを入手する

Wingtip Tickets SaaS マルチテナント データベースのスクリプトとアプリケーション ソース コードは、[WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub リポジトリで入手できます。 Wingtip Tickets SaaS のスクリプトをダウンロードしてブロックを解除する手順については、[一般的なガイダンス](saas-tenancy-wingtip-app-guidance-tips.md)を参照してください。

## <a name="before-you-start"></a>開始する前に

データベースが作成されると、最初の完全バックアップから復元可能になるまでに、10 ～ 15 分かかる場合があります。 アプリケーションをインストールした直後の場合は、このシナリオを進める前に数分間待つ必要があることがあります。

## <a name="simulate-a-tenant-accidentally-deleting-data"></a>データを誤って削除した状態のテナントをシミュレートする

これらの回復シナリオの実践のために、まずいずれかのテナント データベースでイベントを "誤って" 削除します。 

### <a name="open-the-events-app-to-review-the-current-events"></a>Events アプリを開いて現在のイベントを確認する

1. Events Hub (http://events.wtp.&lt;user&gt;.trafficmanager.net) を開き、 **[Contoso Concert Hall]** を選択します。

   ![Events Hub](./media/saas-dbpertenant-restore-single-tenant/events-hub.png)

2. イベントの一覧をスクロールして、一覧の最後のイベントをメモします。

   ![最後のイベントが表示される](./media/saas-dbpertenant-restore-single-tenant/last-event.png)

### <a name="accidentally-delete-the-last-event"></a>"誤って" 最後のイベントを削除する

1. PowerShell ISE で ...\\Learning Modules\\Business Continuity and Disaster Recovery\\RestoreTenant\\*Demo-RestoreTenant.ps1* を開き、次の値を設定します。

   * **$DemoScenario** = **1** *((チケットの販売がない) 最後のイベントを削除します)* 。
2. F5 キーを押してスクリプトを実行し、最後のイベントを削除します。 次の確認メッセージが表示されます。

   ```Console
   Deleting last unsold event from Contoso Concert Hall ...
   Deleted event 'Seriously Strauss' from Contoso Concert Hall venue.
   ```

3. Contoso のイベント ページが開きます。 下へスクロールし、そのイベントがなくなったことを確認します。 まだイベントが一覧に残っている場合は、 **[最新の情報に更新]** をクリックして、イベントがなくなったことを確認します。
   ![最後のイベントが削除された](./media/saas-dbpertenant-restore-single-tenant/last-event-deleted.png)

## <a name="restore-a-tenant-database-in-parallel-with-the-production-database"></a>運用データベースと並行してテナント データベースを復元する

ここでは、Contoso Concert Hall データベースをイベントが削除される前の時点に復元します。 このシナリオでは、並列データベースで削除されたデータを確認することのみが目的であると仮定しています。

 *Restore-TenantInParallel.ps1* スクリプトは、並列カタログ エントリを持つ *ContosoConcertHall\_old* という名前の並列テナント データベースを作成します。 この復元パターンは、小規模なデータ損失から回復するシナリオに最適です。 また、このパターンはコンプライアンスや監査の目的でデータをレビューする必要がある場合にも使用できます。 [アクティブ geo レプリケーション](active-geo-replication-overview.md)を使用するときに推奨されるアプローチです。

1. 「[データを誤って削除した状態のテナントをシミュレートする](#simulate-a-tenant-accidentally-deleting-data)」の手順を完了します。
2. PowerShell ISE で、...\\Learning Modules\\Business Continuity and Disaster Recovery\\RestoreTenant\\_Demo-RestoreTenant.ps1_ を開きます。
3. **$DemoScenario** = **2** と設定します *(並列でテナントを復元します)* 。
4. F5 キーを押してスクリプトを実行します。

このスクリプトは、イベントが削除される前の特定の時点にテナント データベースを復元します。 データベースは、_ContosoConcertHall\_old_ という名前の新しいデータベースに復元されます。 この復元されたデータベースに存在するカタログ メタデータが削除されて、データベースは、*ContosoConcertHall\_old* という名前から構築されたキーを使ってカタログに追加されます。

デモ スクリプトは、ブラウザーでこの新しいテナント データベースのイベント ページを開きます。 URL に関するメモ: ```http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net/contosoconcerthall_old```。このページは、復元されたデータベースからのデータを示しており、 *_old* が名前に追加されています。

ブラウザーに表示されるイベントをスクロールして、前述のセクションで削除したイベントが復元されていることを確認します。

独自のイベント アプリを使用して、復元されたテナントを追加のテナントとして公開することは、復元されたデータに対してテナント アクセスを提供する方法とは異なります。 復元パターンを示すのに役立ちます。 一般的に、古いデータへの読み取りアクセス許可を付与し、復元されたデータベースを定義した期間保持します。 サンプルでは、_復元されたテナントの削除_ のシナリオの実行を完了した後に、復元されたテナント エントリを削除できます。

1. **$DemoScenario** = **4** を設定します *(復元されたテナントを削除します)* 。
2. F5 キーを押してスクリプトを実行します。
3. これで、*ContosoConcertHall\_old* エントリがカタログから削除されました。 ブラウザーでこのテナントのイベント ページを閉じます。

## <a name="restore-a-tenant-in-place-replacing-the-existing-tenant-database"></a>テナントを所定の時点に復元し、既存のテナント データベースを置き換える

ここでは、Contoso Concert Hall テナントをイベントが削除される前の時点に復元します。 *Restore-TenantInPlace* スクリプトは、テナント データベースを新しいデータベースに復元して、元のデータベースを削除します。 この復元パターンは、重大なデータ破損からの回復に最適であり、テナントは大規模なデータ損失に対応する必要がある場合があります。

1. PowerShell ISE で **Demo-RestoreTenant.ps1** ファイルを開きます。
2. **$DemoScenario** = **5** を設定します *(テナントを所定の場所に復元します)* 。
3. F5 キーを押してスクリプトを実行します。

このスクリプトは、イベントが削除される前の時点にテナント データベースを復元します。 それ以上更新されないように、最初に Contoso Concert Hall テナントをオフラインにします。 その後、復元ポイントから復元することによって、並列データベースが作成されます。 データベース名が既存のテナント データベース名と競合しないように、タイムスタンプを使用して、復元されたデータベースの名前が指定されます。 続いて、古いテナント データベースが削除され、復元されたデータベースの名前が元のデータベース名に変更されます。 最後に、Contoso Concert Hall がオンラインに戻り、復元されたデータベースへのアプリからのアクセスが許可されます。

これで、イベントが削除される前の時点にデータベースが正常に復元されました。 **イベント** ページを開き、最後のイベントが復元されたことを確認してください。

データベースを復元した後は、再度復元するために最初の完全バックアップが利用可能になるまでに、10 ～ 15 分かかります。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * データベースを並列データベース (サイド バイ サイド) に復元する。
> * データベースを特定の場所に復元する。

[テナント データベースのスキーマ管理](saas-tenancy-schema-management.md)のチュートリアルを試す。

## <a name="additional-resources"></a>その他のリソース

* [Wingtip SaaS アプリケーションに基づく作業のための追加チュートリアル](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Azure SQL Database によるビジネス継続性の概要](business-continuity-high-availability-disaster-recover-hadr-overview.md)
* [SQL Database バックアップについての詳細情報](automated-backups-overview.md)