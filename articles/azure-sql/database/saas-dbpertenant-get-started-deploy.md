---
title: テナント単位データベース SaaS チュートリアル
description: Azure SQL Database を使用してテナント単位データベース パターンおよびその他の SaaS パターンを示す、Wingtip Tickets SaaS マルチ テナント アプリケーションをデプロイして操作します。
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 497e714289c834e026c6b9b767ed2b7af5442783
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92780837"
---
# <a name="deploy-and-explore-a-multitenant-saas-app-that-uses-the-database-per-tenant-pattern-with-azure-sql-database"></a>Azure SQL Database によるテナント単位データベース パターンを使用するマルチテナント SaaS アプリケーションをデプロイして操作する

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

このチュートリアルでは、Wingtip Tickets SaaS テナント単位データベース アプリケーション (Wingtip) をデプロイして操作します。 このアプリでは、複数のテナントのデータを保存するために、テナント単位データベース パターンが使用されます。 このアプリは、SaaS シナリオを実現する方法を簡素化する Azure SQL Database の機能を紹介することを目的としています。

**[Azure にデプロイ]** を選択すると、5 分後にマルチテナント SaaS アプリケーションが作成されます。 アプリには Azure SQL Database で実行されるデータベースが含まれています。 アプリは、それぞれ独自のデータベースを使用する 3 つのサンプル テナントと共にデプロイされます。 すべてのデータベースが SQL エラスティック プールにデプロイされます。 アプリは Azure サブスクリプションにデプロイされます。 ユーザーには、アプリの個々のコンポーネントを確認して操作するフル アクセス権があります。 アプリケーションの C# ソース コードと管理スクリプトは、[WingtipTicketsSaaS-DbPerTenant GitHub リポジトリ][github-wingtip-dpt]で入手できます。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> - Wingtip SaaS アプリケーションをデプロイする方法。
> - アプリケーションのソース コード、および管理スクリプトを取得する場所。
> - アプリを構成するサーバー、プール、データベースについて。
> - *カタログ* によるテナントとデータのマッピング方法。
> - 新しいテナントのプロビジョニング方法。
> - アプリでテナントのアクティビティを監視する方法。

SaaS の設計と管理のさまざまなパターンを確認するために、[一連の関連チュートリアル](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)が用意されています。 各チュートリアルは、この初期デプロイを基に作成されています。 これらのチュートリアルを使用すると、用意されているスクリプトを調べて、さまざまな SaaS パターンの実装方法を確認できます。 各スクリプトは、SQL Database の機能によって、SaaS アプリケーションの開発がどのように簡素化されるのかを示しています。

## <a name="prerequisites"></a>前提条件

このチュートリアルに取り組む前に、Azure PowerShell がインストールされていることを確認してください。 詳細については、[Azure PowerShell の概要](/powershell/azure/get-started-azureps)に関するページを参照してください。

## <a name="deploy-the-wingtip-tickets-saas-application"></a>Wingtip Tickets SaaS アプリケーションのデプロイ

### <a name="plan-the-names"></a>名前を考える

このセクションの手順では、リソースの名前がグローバルに一意であることを確認するために使用する、ユーザー値を指定します。 また、アプリのデプロイメントによって作成されるすべてのリソースが含まれる、リソース グループの名前も指定します。 たとえば、架空の名前が Ann Finley である場合、次のような名前にすることをお勧めします。

- **ユーザー**: *af1* は Ann Finley の頭文字と 1 桁の数字で構成されます。 アプリをもう一度デプロイする場合は、別の値を使用します (例: af2)。
- **リソース グループ:** *wingtip-dpt-af1* は、これがテナント単位データベース アプリであることを示します。 ユーザー名 af1 の追加によって、リソース グループ名とリソースを含む名前が関連付けられます。

名前を選択し、書き留めておきます。

### <a name="steps"></a>手順

1. Azure Portal で Wingtip Tickets SaaS テナント単位データベース デプロイ テンプレートを開くには、 **[Azure にデプロイ]** ボタンを選択します。

   [![[Azure にデプロイ] というラベルの付いたボタンが示されている画像。](https://azuredeploy.net/deploybutton.png)](https://aka.ms/deploywingtipdpt)

1. テンプレートの必要なパラメーターに値を入力します。

    > [!IMPORTANT]
    > 一部の認証とサーバー ファイアウォールは、デモンストレーションのために、意図的に保護されていません。 新しいリソース グループを作成することをおすすめします。 既存のリソース グループ、サーバー、またはプールを使用しないでください。 運用環境には、このアプリケーション、スクリプト、およびデプロイされたリソースを使用しないでください。 関連する課金を停止するために、サンプル アプリケーションの操作が終了したら、このリソース グループを削除してください。

    - **[リソース グループ]** : **[新規作成]** を選択し、先ほど選択したリソース グループの一意の名前を指定します。
    - **[場所]** :ドロップダウン リストから場所を選択します。
    - **[ユーザー]** :先ほど選択したユーザー名の値を使用します。

1. アプリケーションをデプロイします。

    a. 使用条件に同意する場合はオンにします。

    b. **[購入]** を選択します。

1. デプロイの状態を監視するには、 **[通知]** (検索ボックスの右にあるベル アイコン) を選択します。 Wingtip Tickets SaaS アプリのデプロイには約 5 分かかります。

   ![デプロイメント成功](./media/saas-dbpertenant-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-wingtip-tickets-management-scripts"></a>Wingtip Tickets 管理スクリプトのダウンロードとブロック解除

アプリケーションのデプロイ中に、ソース コードと管理スクリプトをダウンロードします。

> [!IMPORTANT]
> zip ファイルを外部ソースからダウンロードして抽出すると、実行可能なコンテンツ (スクリプトと DLL) が Windows によってブロックされる場合があります。 スクリプトを展開する前に、手順に従って .zip ファイルのブロックを解除してください。 ブロック解除により、スクリプトの実行が許可されます。

1. [WingtipTicketsSaaS-DbPerTenant GitHub リポジトリ][github-wingtip-dpt]を参照します。
1. **[クローンまたはダウンロード]** を選択します。
1. **[Download ZIP]/(ZIP をダウンロード/)** を選択し、ファイルを保存します。
1. **WingtipTicketsSaaS-DbPerTenant-master.zip** ファイルを右クリックし、 **[Properties]\(プロパティ\)** を選択します。
1. **[General]\(全般\)** タブで **[Unblock]\(ブロックの解除\)**  >  **[Apply]\(適用\)** と選択します。
1. **[OK]** を選択し、ファイルを展開します。

スクリプトは、...\\WingtipTicketsSaaS-DbPerTenant-master\\Learning Modules フォルダーにあります。

## <a name="update-the-user-configuration-file-for-this-deployment"></a>このデプロイのユーザー構成ファイルの更新

スクリプトを実行する前に、User Config ファイルのリソース グループとユーザーの値を更新します。 これらの変数は、デプロイ中に使用した値に設定します。

1. PowerShell ISE で、...\\Learning Modules\\**UserConfig.psm1** を開きます。
1. **ResourceGroupName** と **Name** をデプロイの固有の値で更新します (10 行目および 11 行目のみ)。
1. 変更を保存します。

これらの値は、ほぼすべてスクリプトで参照されます。

## <a name="run-the-application"></a>アプリケーションの実行

アプリは、イベントを開催する会場の窓口の機能を提供します。 会場の種類には、コンサート ホール、ジャズ クラブ、スポーツ クラブなどがあります。 Wingtip Tickets アプリでは、会場がテナントとして登録されます。 テナントになることで、会場が顧客に対して、イベントの一覧表示やチケットの販売を簡単に行うことができます。 各会場が、イベントの表示やチケット販売を行うためのパーソナライズされた Web サイトを利用できます。

アプリの内部では、テナントごとにデータベースが作成され、1 つのエラスティック プールにデプロイされます。

中央の **Events Hub** ページには、デプロイのテナントへのリンク一覧が表示されます。

1. Web ブラウザーで URL http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net と指定して Events Hub を開きます。 &lt;user&gt; をデプロイのユーザーの値に置換してください。

    ![Events Hub](./media/saas-dbpertenant-get-started-deploy/events-hub.png)

2. Events Hub で **[Fabrikam Jazz Club]** を選択します。

    ![events](./media/saas-dbpertenant-get-started-deploy/fabrikam.png)

### <a name="azure-traffic-manager"></a>Azure の Traffic Manager

Wingtip アプリケーションでは、[*Azure Traffic Manager*](../../traffic-manager/traffic-manager-overview.md) を使用して受信要求の分散を制御します。 特定のテナントのイベント ページにアクセスする URL では、次の形式を使用します。

- http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net/fabrikamjazzclub

    上記の形式の各部分の説明を次の表に示します。

    | URL の部分        | 説明       |
    | :-------------- | :---------------- |
    | events.wingtip-dpt | Wingtip アプリのイベントの部分。<br /><br /> *-dpt* によって、Wingtip Tickets の "*テナント単位データベース*" の実装が他の実装と区別されます。 たとえば、テナントごとの "*単一*" アプリ ( *-sa*) の実装や "*マルチテナント データベース*" ( *-mt*) の実装です。 |
    | . *&lt;user&gt;* | この例では *af1*。 |
    | .trafficmanager.net/ | Traffic Manager、ベース URL。 |
    | fabrikamjazzclub | Fabrikam Jazz Club という名前のテナントを識別します。 |
    | &nbsp; | &nbsp; |

- イベント アプリによって URL からテナント名が解析されます。
- テナント名を使用してキーが作成されます。
- テナントのデータベースの場所を取得するために、このキーを使用してカタログにアクセスします。
  - カタログは、"*シャード マップ管理*" を使用して実装されています。
- Events Hub は、カタログ内の拡張メタデータを使用して、各テナントのイベント ページの URL の一覧を作成します。

運用環境では、通常は、Traffic Manager DNS 名に対して、"[*会社のインターネット ドメインを指す*](../../traffic-manager/traffic-manager-point-internet-domain.md)" CNAME DNS レコードを作成します。

> [!NOTE]
> このチュートリアルでは、トラフィック マネージャーの用途がすぐには明らにされない場合があります。 この一連のチュートリアルの目的は、複雑な運用環境のスケールを処理できるパターンを示すことです。 そのような場合、たとえば、複数の Web アプリが世界中に分散されていて、データベースと併置されていると、これらのインスタンス間でルーティングを行うためにトラフィック マネージャーが必要になります。
ただしトラフィック マネージャーの利用を紹介する別のチュートリアルのセットには、[geo 復元](./saas-dbpertenant-dr-geo-restore.md)チュートリアルや [geo レプリケーション](./saas-dbpertenant-dr-geo-replication.md) チュートリアルがあります。 これらのチュートリアルでは、トラフィック マネージャーは、リージョンでの停止が発生した場合に SaaS アプリの復旧インスタンスへの切り替えを助けるために使用されます。

## <a name="start-generating-load-on-the-tenant-databases"></a>テナント データベースに対する負荷の生成

これで、アプリがデプロイされました。作業を開始しましょう。

*Demo-LoadGenerator* PowerShell スクリプトは、すべてのテナント データベースに対して実行されるワークロードを開始します。 多くの SaaS アプリの実際の負荷は散発的で予測不能です。 この種の負荷をシミュレートするために、ジェネレーターは各テナントでのアクティビティのランダムなスパイクまたはバーストによる負荷を生成します。 バーストはランダムな間隔で発生します。 負荷パターンの生成には数分かかります。 少なくとも 3 ～ 4 分間ジェネレーターを実行してから負荷を監視することをおすすめします。

1. PowerShell ISE で、...\\Learning Modules\\Utilities\\*Demo-LoadGenerator.ps1* スクリプトを開きます。
2. F5 キーを押してスクリプトを実行し、ロード ジェネレーターを起動します。 現時点では、既定のパラメーター値をそのまま使用します。
3. Azure アカウントにサインインし、必要に応じて、使用するサブスクリプションを選択します。

ロード ジェネレーター スクリプトが、カタログ内の各データベースに対してバックグラウンド ジョブを開始した後、停止します。 ロード ジェネレーター スクリプトを再実行した場合、ロード ジェネレーター スクリプトは、実行中のすべてのバックグラウンド ジョブを停止した後、新しいバックグラウンド ジョブを開始します。

### <a name="monitor-the-background-jobs"></a>バックグラウンド ジョブを監視する

バックグラウンド ジョブを制御および監視する場合は、次のコマンドレットを使用します。

- `Get-Job`
- `Receive-Job`
- `Stop-Job`

### <a name="demo-loadgeneratorps1-actions"></a>Demo-LoadGenerator.ps1 のアクション

*Demo-LoadGenerator.ps1* は、顧客トランザクションのアクティブなワークロードを模倣します。 次の手順は、*Demo-LoadGenerator.ps1* で開始される一連のアクションを示しています。

1. *Demo-LoadGenerator.ps1* は、フォアグラウンドで *LoadGenerator.ps1* を起動します。

    - これらの.ps1 ファイルはいずれも、Learning Modules\\Utilities\\ のフォルダーに格納されています。

2. *LoadGenerator.ps1* は、カタログ内のすべてのテナント データベースをループ処理します。

3. *LoadGenerator.ps1* は、各テナント データベースに対して PowerShell のバックグラウンド ジョブを開始します。

    - 既定では、バックグラウンド ジョブは 120 分間実行されます。
    - 各ジョブでは、*sp_CpuLoadGenerator* の実行によって、1 つのテナント データベースで CPU ベースの負荷が発生します。 負荷の強度と期間は、`$DemoScenario` によって異なります。
    - *sp_CpuLoadGenerator* は、高い CPU 負荷を発生させる SQL SELECT ステートメントでループします。 SELECT の発行間隔は、制御可能な CPU の負荷を作成するパラメーター値によって決まります。 負荷のレベルと間隔は、現実的な負荷をシミュレートするためにランダム化されます。
    - この .sql ファイルは、*WingtipTenantDB\\dbo\\StoredProcedures\\* に格納されています。

4. `$OneTime = $false` の場合、ロード ジェネレーターはバックグラウンド ジョブを開始し、そのまま実行されます。 10 秒ごとにプロビジョニングされた新しいテナントを監視します。 `$OneTime = $true` を設定した場合、ロード ジェネレーターは、バックグラウンド ジョブを開始した後、フォア グラウンドでの実行を停止します。 このチュートリアルでは、`$OneTime = $false` のままにしてください。

   ロード ジェネレーターを停止するか再開するには、Ctrl + C または [操作の停止] (Ctrl + Break) を使用します。

   ロード ジェネレーターをフォアグラウンドで実行したままにする場合は、別の PowerShell ISE インスタンスを使用して他の PowerShel スクリプトを実行します。

&nbsp;

次のセクションに進む前に、ロード ジェネレーターをジョブ呼び出し状態で実行したままにしておきます。

## <a name="provision-a-new-tenant"></a>新しいテナントのプロビジョニング

初期デプロイでは 3 つのサンプル テナントが作成されます。 ここでは、別のテナントを作成して、デプロイ済みのアプリケーションへの影響を確認します。 Wingtip アプリで新しいテナントをプロビジョニングするワークフローについては、[プロビジョニングとカタログに関するチュートリアル](saas-dbpertenant-provision-and-catalog.md)をご覧ください。 このフェーズでは、1 分足らずで新しいテナントを作成します。

1. 新しい PowerShell ISE を開きます。
2. \\Learning Modules\Provision and Catalog\\*Demo-ProvisionAndCatalog.ps1* を開きます。
3. F5 キーを押してスクリプトを実行します。 現時点では、既定値をそのまま使用します。

   > [!NOTE]
   > 多くの Wingtip SaaS スクリプトは、 *$PSScriptRoot* を使用してフォルダーを参照し、他のスクリプト内の関数を呼び出します。 この変数は F5 キーを押してスクリプト全体が実行されたときにのみ評価されます。  F8 キーで選択項目を強調表示して実行すると、エラーが発生する可能性があります。 F5 キーを押して、スクリプトを実行します。

新しいテナント データベースは次のように処理されます。

- SQL エラスティック プールに作成される。
- 初期化される。
- カタログに登録される。

プロビジョニングが正常に完了すると、新しいテナントの *Events* サイトがブラウザーに表示されます。

![新しいテナント](./media/saas-dbpertenant-get-started-deploy/red-maple-racing.png)

Events Hub を更新すると、新しいテナントが一覧に表示されます。

## <a name="explore-the-servers-pools-and-tenant-databases"></a>サーバー、プール、テナント データベースを操作する

ここまでで、テナントのコレクションに対する負荷の実行を開始しました。次は、デプロイされたリソースをいくつか見てみましょう。

1. [Azure Portal](https://portal.azure.com) で、SQL サーバーの一覧を参照します。 次に、**catalog-dpt-&lt;User&gt;** サーバーを展開します。
    - カタログ サーバーには 2 つのデータベース **tenantcatalog** と **basetenantdb** (新しいテナントを作成するためにコピーされたテンプレート データベース) が含まれます。

   ![2 つのデータベースが表示されたカタログ サーバーの概要ページを示すスクリーンショット。](./media/saas-dbpertenant-get-started-deploy/databases.png)

2. SQL サーバーの一覧に戻ります。

3. テナント データベースを保持している **tenants1-dpt-&lt;ユーザー&gt;** サーバーを開きます。

4. 次の項目を確認します。

    - 各テナント データベースは **エラスティック標準** データベースで、eDTU が 50 の Standard プールにあります。
    - Red Maple Racing データベース。これは、以前にプロビジョニングしたテナント データベースです。

   ![データベースを保持しているサーバー](./media/saas-dbpertenant-get-started-deploy/server.png)

## <a name="monitor-the-pool"></a>プールの監視

*LoadGenerator.ps1* を数分間実行したら、十分な量のデータが生成されています。このデータを使って、一部の監視機能を見てみましょう。 これらの機能は、プールとデータベースに組み込まれています。

**tenants1-dpt-&lt;user&gt;** サーバーを参照し、**Pool1** をクリックして、プールのリソース使用率を表示します。 次のグラフでは、ロード ジェネレーターを 1 時間実行しました。

   ![プールの監視](./media/saas-dbpertenant-get-started-deploy/monitor-pool.png)

- **Resource utilization** というラベルが付いた最初のグラフは、プールの eDTU 使用率を示しています。
- 2 番目のグラフは、プール内のアクティブな上位 5 つのデータベースの eDTU 使用率を示しています。

この 2 つのグラフは、エラスティック プールと SQL Database が予測できない SaaS アプリケーション ワークロードに適していることを示しています。 これらのグラフは、4 つのデータベースがそれぞれ 40 eDTU までバーストしても、50 eDTU プールで問題なくサポートされていることを示しています。 50 eDTU プールは、さらに重いワークロードをサポートできます。 データベースが単一データベースとしてプロビジョニングされた場合は、バーストをサポートするために S2 (50 DTU) が必要になります。 4 つの単一 S2 データベースのコストは、プールの価格の約 3 倍になります。 実際の環境では、SQL Database のお客様用に最大 500 のデータベースが 200 eDTU のプールで実行されます。 詳細については、[パフォーマンスの監視](saas-dbpertenant-performance-monitoring.md)に関するチュートリアルを参照してください。

## <a name="additional-resources"></a>その他のリソース

- 詳細については、[データベース単位テナント Wingtip Tickets SaaS アプリケーションに基づく追加のチュートリアル](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)を参照してください。
- エラスティック プールの詳細については、[Azure SQL エラスティック プール](elastic-pool-overview.md)に関するページを参照してください。
- エラスティック ジョブの詳細については、「[スケールアウトされたクラウド データベースの管理](./elastic-jobs-overview.md)」を参照してください。
- マルチテナント SaaS アプリケーションの詳細については、[マルチテナント SaaS アプリケーションの設計パターン](saas-tenancy-app-design-patterns.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> - Wingtip Tickets SaaS アプリケーションのデプロイ方法。
> - アプリを構成するサーバー、プール、データベースについて。
> - *カタログ* によるテナントとデータのマッピング方法。
> - 新しいテナントのプロビジョニング方法。
> - プール使用率を表示してテナント アクティビティを監視する方法。
> - サンプル リソースを削除して、関連する課金を停止する方法。

次に、[プロビジョニングとカタログに関するチュートリアル](saas-dbpertenant-provision-and-catalog.md)をお試しください。

<!-- Link references. -->

[github-wingtip-dpt]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant