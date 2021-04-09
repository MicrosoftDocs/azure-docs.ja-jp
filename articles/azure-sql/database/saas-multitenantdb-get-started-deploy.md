---
title: シャード マルチテナント データベース SaaS アプリのデプロイ
description: Azure SQL Database を使用して SaaS パターンを示す、シャード化された Wingtip Tickets SaaS マルチテナント データベース アプリケーションをデプロイおよび操作します。
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 10/16/2018
ms.openlocfilehash: 4de7e428bff0feaafdec00b0c0014bbaf6acb917
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92790969"
---
# <a name="deploy-and-explore-a-sharded-multi-tenant-application"></a>シャード化されたマルチテナント アプリケーションをデプロイおよび操作する
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

このチュートリアルでは、Wingtip Tickets という名前のサンプル マルチテナント SaaS アプリケーションをデプロイおよび詳細確認します。 Wingtip Tickets アプリの目的は、各種 SaaS シナリオの実装を簡素化する Azure SQL Database の機能を紹介することにあります。

Wingtips Tickets アプリのこの実装では、シャード化されたマルチテナント データベース パターンを使用します。 シャーディングはテナント ID ごとに実行されます。 テナントのデータは、テナント ID の値に応じて特定のデータベースに配布されます。 

このデータベースのパターンを使用すると、1 つ以上のテナントを各シャードまたはデータベースに保存できます。 それぞれのデータベースを複数のテナントでシャード化することによって、最小限のコストに最適化できます。 または、それぞれのデータベースに 1 つのテナントのみを格納させることによって分離性を最適化できます。 最適化の選択は、特定のテナントごとに独立して行うことができます。 テナントを最初に格納するときに選択したり、後で変更したりできます。 どちらの場合でもアプリケーションが正しく動作するように設計されています。

## <a name="app-deploys-quickly"></a>アプリのすばやいデプロイ

このアプリは Azure クラウドで実行され、Azure SQL Database を使用します。 以下のデプロイ セクションには **[Deploy to Azure]\(Azure にデプロイ\)** という青いボタンがあります。 このボタンを押すと、アプリは 5 分以内に Azure サブスクリプションに完全にデプロイされます。 個々のアプリケーション コンポーネントを操作するフル アクセスがあります。

アプリケーションは、3 つのサンプル テナント用のデータと共にデプロイされます。 テナントは、1 つのマルチテナント データベースにまとめて保存されます。

誰でも [GitHub リポジトリ][link-github-wingtip-multitenantdb-55g]から Wingtip Tickets の C# および PowerShell のソース コードをダウンロードできます。

## <a name="learn-in-this-tutorial"></a>このチュートリアルの詳細

> [!div class="checklist"]
> - Wingtip Tickets SaaS アプリケーションのデプロイ方法。
> - アプリケーションのソース コード、および管理スクリプトを取得する場所。
> - アプリを構成するサーバーおよびデータベースについて。
> - *カタログ* によるテナントとデータのマッピング方法。
> - 新しいテナントのプロビジョニング方法。
> - アプリでテナントのアクティビティを監視する方法。

この初期デプロイをビルドする一連の関連するチュートリアルを利用できます。 このチュートリアルでは、SaaS の広範な設計と管理パターンについて説明します。 チュートリアルに従って操作すると、指定されたスクリプトをステップ実行して、さまざまな SaaS パターンが実装される方法を確認するように求められます。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次の前提条件を満たしておく必要があります。

- 最新の Azure PowerShell がインストールされていること。 詳細については、「[Azure PowerShell を使ってみる][link-azure-get-started-powershell-41q]」をご覧ください。

## <a name="deploy-the-wingtip-tickets-app"></a>Wingtip Tickets アプリのデプロイ

### <a name="plan-the-names"></a>名前を考える

このセクションの手順では、リソース名がグローバルに一意であることを保証するために使用される "*ユーザー*" 値と、アプリのデプロイによって作成されるすべてのリソースを含む "*リソース グループ*" を指定します。 たとえば、名前が *Ann Finley* である場合、次のような名前にすることをお勧めします。
- *ユーザー:* **af1** *(イニシャルに一桁の数字を付けます。アプリをもう一度デプロイする場合は、別の値を使用します (例: af2)。)* "
- "*リソース グループ:* " **wingtip-mt-af1** " *(wingtip-mt は、これがシャード化されたマルチテナント アプリであることを示します。ユーザー名 af1 の追加によって、リソース グループ名とリソースを含む名前が関連付けられます。)* "

名前を選択し、書き留めておきます。 

### <a name="steps"></a>手順

1. **[Deploy to Azure]\(Azure にデプロイ\)** という青いボタンをクリックします。
   - Wingtip Tickets SaaS デプロイ テンプレートが指定された状態で Azure Portal が開きます。

     [![[Deploy to Azure]\(Azure にデプロイ\) ボタン。][image-deploy-to-azure-blue-48d]][link-aka-ms-deploywtp-mtapp-52k]

1. デプロイに必須のパラメーター値を入力します。

    > [!IMPORTANT]
    > このデモの動作確認では、既存のリソース グループ、サーバー、またはプールを一切使用しないでください。 代わりに、 **[新しいリソース グループの作成]** を選択します。 関連する課金を停止するために、サンプル アプリケーションの操作が終了したら、このリソース グループを削除してください。
    > 運用環境にはこのアプリケーション、またはこのアプリケーションが作成したリソースを使用しないでください。 認証における一部の局面およびサーバーのファイアウォール設定は、デモを円滑にするため、このアプリ上では意図的にセキュリティで保護されていません。

    - **[リソース グループ]** - **[新規作成]** を選択して、リソース グループの **[名前]** を指定します (大文字と小文字が区別されます)。
        - ドロップダウン から **[場所]** を選択します。
    - **ユーザー** - **ユーザー** には短い値を選択することをお勧めします。

1. **アプリケーションをデプロイします**。

    - 使用条件に同意したら、チェック ボックスをオンにします。
    - **[購入]** をクリックします。

1. **[通知]** (検索ボックスの右にあるベル アイコン) をクリックして、デプロイ ステータスを監視します。 Wingtip アプリのデプロイには約 5 分かかります。

   ![デプロイメントに成功しました](./media/saas-multitenantdb-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-management-scripts"></a>管理スクリプトのダウンロードとブロック解除

アプリケーションをデプロイ中に、アプリケーションのソース コードと管理スクリプトをダウンロードします。

> [!NOTE]
> zip ファイルを外部ソースからダウンロードして抽出すると、実行可能なコンテンツ (スクリプト、DLL) が Windows によってブロックされる場合があります。 zip ファイルからスクリプトを抽出するとき、以下の手順を実行して、.zip ファイルをブロック解除してから抽出します。 .zip ファイルをブロック解除することによってスクリプトを実行できるようになります。

1. [WingtipTicketsSaaS-MultiTenantDb GitHub リポジトリ](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb)を参照します。
2. **[Clone or download]** をクリックします。
3. **[Download ZIP]** をクリックし、ファイルを保存します。
4. **WingtipTicketsSaaS-MultiTenantDb-master.zip** ファイルを右クリックし、 **[プロパティ]** を選択します。
5. **[全般]** タブで **[ブロックの解除]** を選択し、 **[適用]** をクリックします。
6. **[OK]** をクリックします。
7. ファイルを解凍します。

スクリプトは、 *..\\WingtipTicketsSaaS-MultiTenantDb-master\\Learning Modules\\* フォルダーにあります。

## <a name="update-the-configuration-file-for-this-deployment"></a>このデプロイの構成ファイルの更新

スクリプトを実行する前に、**UserConfig.psm1** で *リソース グループ* と *ユーザー* の値を設定します。 これらの変数は、デプロイ中に設定した値に設定します。

1. *PowerShell ISE* で ...\\Learning Modules\\*UserConfig.psm1* を開きます。
2. *ResourceGroupName* と *Name* をデプロイの固有の値で更新します (10 行目および 11 行目のみ)。
3. 変更を保存します。

このファイルで設定された値はすべてのスクリプトで使用されるため、正確であることが重要です。 アプリを再デプロイする場合は、ユーザーとリソース グループにそれぞれ別の値を選択する必要があります。 次に新しい値で UserConfig.psm1 ファイルを再度更新します。

## <a name="run-the-application"></a>アプリケーションの実行

Wingtip アプリでは、テナントは会場です。 会場は、イベントを開催するコンサート ホール、スポーツ クラブなどです。 会場は顧客として Wingtip に登録され、テナント ID は会場ごとに生成されます。 各会場の開催予定のイベントが Wingtip 上で一覧表示され、ユーザーはイベントのチケットを購入できます。

会場ごとに、イベントの一覧表示やチケット販売を行うためのカスタマイズされた Web アプリを利用できます。 各 Web アプリは、他のテナントから切り離され、相互に独立しています。 Azure SQL Database　の内部では、それぞれのテナントの各データが既定でシャード化されたマルチテナント データベースに保存されます。 すべてのデータにはテナント ID がタグ付けされます。

中心となる **Events Hub** Web ページには、特定のデプロイのテナントへのリンク一覧が表示されます。 **Events Hub** Web ページや個別の Web アプリを体験するには、次の手順に従います。

1. Web ブラウザーで **Events Hub** を開きます。
   - http://events.wingtip-mt.&lt;user&gt;.trafficmanager.net &nbsp; *(&lt; user&gt; は実際のデプロイのユーザー値に置き換えてください)。*

     ![Events Hub](./media/saas-multitenantdb-get-started-deploy/events-hub.png)

2. **Events Hub** で **[Fabrikam Jazz Club]** をクリックします。

   ![events](./media/saas-multitenantdb-get-started-deploy/fabrikam.png)

### <a name="azure-traffic-manager"></a>Azure の Traffic Manager

Wingtip アプリは、受信要求の配布を制御するために [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) を使用します。 各テナントのイベント ページには、URL にテナント名が含まれています。 各 URL には、固有のユーザー値も含まれています。 各 URL は、以下の手順で、次に示す形式に従います。

- http://events.wingtip-mt.&lt;user&gt;.trafficmanager.net/*fabrikamjazzclub*

1. イベント アプリによって、URL からテナント名が解析されます。 前の URL の例では、テナント名は *fabrikamjazzclub* です。
2. アプリはテナント名をハッシュして、[シャード マップ管理](elastic-scale-shard-map-management.md)を使用するカタログにアクセスするためのキーを作成します。
3. アプリはカタログ内のキーを検索し、テナントのデータベースの場所を取得します。
4. アプリは場所情報を使用して、テナントのすべてのデータを含む 1 つのデータベースを見つけてアクセスします。

### <a name="events-hub"></a>Events Hub

1. **Events Hub** はカタログおよびその会場に登録されているすべてのテナントを一覧表示します。
2. **Events Hub** は、カタログ内の拡張メタデータを使用して、各マッピングに関連付けられたテナントの名前を取得し、URL を構築します。

運用環境では、通常、[会社のインターネット ドメインで Traffic Manager プロファイルが参照されるように](../../traffic-manager/traffic-manager-point-internet-domain.md) CNAME DNS レコードを作成します。

## <a name="start-generating-load-on-the-tenant-databases"></a>テナント データベースに対する負荷の生成

これで、アプリがデプロイされました。作業を開始しましょう。 *Demo-LoadGenerator* PowerShell スクリプトは、各テナントに対して実行されるワークロードを開始します。 多くの SaaS アプリの実際の負荷は通常、散発的で予測不能です。 この種の負荷をシミュレートするために、ジェネレーターはすべてのテナントに分散される負荷を生成します。 負荷には、各テナントでランダムな間隔で発生するランダム化されたバーストが含まれています。 負荷パターンの生成には数分かかります。よって、少なくとも 3 ～ 4 分間ジェネレーターを実行してから負荷を監視することをお勧めします。

1. *PowerShell ISE* で、...\\Learning Modules\\Utilities\\*Demo-LoadGenerator.ps1* スクリプトを開きます。
2. **F5** を押すと、スクリプトが実行され、ロード ジェネレーターが起動します (既定のパラメーター値はそのままにしておきます)。

*Demo-LoadGenerator.ps1* スクリプトはロード ジェネレーターが実行されている他の PowerShell セッションを開きます。 ロード ジェネレーターは、テナントごとに 1 つバックグラウンドの負荷生成ジョブを起動するフォアグラウンド タスクとしてこのセッションで実行されています。

フォアグラウンド タスクは、開始した後にはジョブ呼び出し状態のままになります。 このタスクは、その後にプロビジョニングされる新しいテナントに対して追加のバックグラウンド ジョブを開始します。

PowerShell セッションを閉じると、すべてのジョブが停止します。

ロード ジェネレーター セッションを再起動して、異なるパラメーター値を使用する必要が生じる場合があります。 その場合、PowerShell 生成セッションを閉じてから、*Demo-LoadGenerator.ps1* を再度実行します。

## <a name="provision-a-new-tenant-into-the-sharded-database"></a>シャード化されたデータベースへの新しいテナントのプロビジョニング

最初のデプロイの *Tenants1* データベースには 3 つのサンプル テナントが含まれています。 別のテナントを作成して、デプロイ済みのアプリケーションに及ぼす影響を確認してみましょう。 この手順では、キー操作 1 つで新しいテナントを作成します。

1. *PowerShell ISE* で ...\\Learning Modules\\Provision and Catalog\\*Demo-ProvisionTenants.ps1* を開きます。
2. **F5** (**F8** ではない) を押してスクリプトを実行します (この時点では、既定値のままにしておきます)。

   > [!NOTE]
   > **F8** キーを押してスクリプトの選択部分を実行するのではなく、**F5** キーだけを押して PowerShell スクリプトを実行する必要があります。 **F8** の問題は、 *$PSScriptRoot* 変数が評価されないことです。 この変数は、フォルダー間の移動、別のスクリプトの呼び出し、モジュールのインポートなどを行う多くのスクリプトで必要です。

新しい Red Maple Racing テナントが *Tenants1* データベースに追加され、カタログに登録されます。 ブラウザーに新しいテナントのチケット販売 **Events** サイトが開きます。

![新しいテナント](./media/saas-multitenantdb-get-started-deploy/red-maple-racing.png)

**Events Hub** を更新すると、新しいテナントが一覧に表示されます。

## <a name="provision-a-new-tenant-in-its-own-database"></a>独自のデータベース内の新しいテナントをプロビジョニングする

シャード化されたマルチテナント モデルでは、別のテナントを含むデータベース内の新しいテナントをプロビジョニングするか、または独自のデータベース内のテナントをプロビジョニングするかを選択できます。 独自のデータベースに分離されたテナントには、次の利点があります。

- そのテナントのデータベースのパフォーマンスを、他のテナントのニーズに悪影響を及ぼすことなく管理できます。
- 影響を受けるテナントがないため、必要に応じて、データベースを過去のある時点の状態に復元できます。

無料試用版または通常の顧客をマルチテナント データベースに格納することを選択できます。 各プレミアム テナントを独自の専用データベースに格納できます。 1 つのテナントのみを格納している多数のデータベースを作成した場合、それらすべてをエラスティック プールで一括して管理し、リソース コストを最適化できます。

次に、別のテナントをプロビジョニングします。今回は独自のデータベースにプロビジョニングします。

1. ...\\Learning Modules\\Provision and Catalog\\*Demo-ProvisionTenants.ps1* で、 *$TenantName* を **Salix Salsa** に、 *$VenueType* を **dance** に、 *$Scenario* を **2** にそれぞれ変更します。

2. **F5** キーを押して、スクリプトを再実行します。
    - **F5** キーを押すと、別のデータベースに新しいテナントがプロビジョニングされます。 データベースとテナントはカタログに登録されます。 ブラウザーからテナントの [Events] ページを開きます。

   ![Salix Salsa イベント ページ](./media/saas-multitenantdb-get-started-deploy/salix-salsa.png)

   - ページの一番下までスクロールします。 バナーの中にデータベース名が表示され、ここにテナントのデータが保存されます。

3. **Events Hub** を更新し、2 つの新しいテナントが一覧に表示されます。

## <a name="explore-the-servers-and-tenant-databases"></a>サーバーおよびテナント データベースを操作する

ここでデプロイされたリソースの一部が表示されます。

1. [Azure Portal](https://portal.azure.com) で、リソース グループの一覧を参照します。 アプリケーションをデプロイしたときに作成したリソース グループを開きます。

   ![resource group](./media/saas-multitenantdb-get-started-deploy/resource-group.png)

2. **catalog-mt&lt;ユーザー&gt;** サーバーをクリックします。 カタログ サーバーには、*tenantcatalog* と *basetenantdb* という名前の 2 つのデータベースが含まれています。 *basetenantdb* データベースは空のテンプレート データベースです。 これをコピーして新しいテナント データベースを作成します。多くのテナント用または 1 つのテナントのみに使用します。

   ![カタログ サーバー](./media/saas-multitenantdb-get-started-deploy/catalog-server.png)

3. リソース グループに戻り、 テナント データベースを保持している *tenants1 mt* サーバーを選択します。
    - tenants1 データベースは、マルチテナント データベースです。ここには、元の 3 つのテナントおよび追加した最初のテナントが保存されています。 これは 50 DTU Standard データベースとして構成されています。
    - **salixsalsa** データベースは、その唯一のテナントとして Salix Salsa ダンス会場 を保持しています。 これは、既定で 50 DTU の Standard Edition データベースとして構成されています。

   ![テナント サーバー](./media/saas-multitenantdb-get-started-deploy/tenants-server.png)

## <a name="monitor-the-performance-of-the-database"></a>データベースのパフォーマンスの監視

ロード ジェネレーターを数分間実行したら、十分なテレメトリが生成されています。このテレメトリを使って、Azure Portal に組み込まれているデータベース監視機能を確認してみましょう。

1. **tenants1-mt&lt;ユーザー&gt;** サーバーを参照し、 **[tenants1]** をクリックして 4 つのテナントを持つデータベースのリソース使用率を表示します。 各テナントは、ロード ジェネレーターから散発的な高負荷がかけられます。

   ![tenants1 の監視](./media/saas-multitenantdb-get-started-deploy/monitor-tenants1.png)

   DTU 使用率のグラフは、多くのテナント間の予測不能な負荷をマルチテナント データベースがサポートする方法を適切に示しています。 ここでは、ロード ジェネレーターは、各テナントに約 30 DTU の散発的な負荷をかけています。 この負荷は、50 DTU データベースの 60% の使用率に相当します。 60% を超えるピークは、同時に複数のテナントに負荷がかけられた結果です。

2. **tenants1-mt&lt;ユーザー&gt;** サーバーを参照し、**salixsalsa** データベースをクリックします。 1 つのテナントのみを含むこのデータベースのリソース使用率を表示できます。

   ![salixsalsa データベース](./media/saas-multitenantdb-get-started-deploy/monitor-salix.png)

ロード ジェネレーターは、各テナントがどのデータベースにあるかに関係なく、各テナントに同様の負荷をかけます。 **salixsalsa** データベースにはテナントが 1 つしかないため、このデータベースは、複数のテナントを含むデータベースよりもはるかに高い負荷に耐えることができます。 

### <a name="resource-allocations-vary-by-workload"></a>ワークロードによって異なるリソース割り当て

マルチテナント データベースでは、優れたパフォーマンスのために、シングルテナント データベースより多くのリソースが必要な場合があります (必ずではありません)。 システム内のテナントでは、リソースの最適な割り当ては、特定のワークロード特性によって異なります。

負荷ジェネレーター スクリプトによって生成されたワークロードは、説明のためのものです。

## <a name="additional-resources"></a>その他のリソース

- マルチテナント SaaS アプリケーションの詳細については、[マルチテナント SaaS アプリケーションの設計パターン](saas-tenancy-app-design-patterns.md)に関するページをご覧ください。

- エラスティック プールの詳細については、以下をご覧ください。

  - [エラスティック プールを利用した Azure SQL Database 内の複数のデータベースの管理およびスケーリング](elastic-pool-overview.md)
  - [Azure SQL Database によるスケールアウト](elastic-scale-introduction.md)

## <a name="next-steps"></a>次のステップ

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> - Wingtip Tickets SaaS マルチテナント データベース アプリケーションのデプロイ方法。
> - アプリを構成するサーバーおよびデータベースについて。
> - *カタログ* によるテナントとデータのマッピング。
> - マルチテナント データベースとシングルテナント データベースに新しいテナントをプロビジョニングする方法。
> - プール使用率を表示してテナント アクティビティを監視する方法。
> - サンプル リソースを削除して、関連する課金を停止する方法。

続いては、[プロビジョニングとカタログに関するチュートリアル](saas-multitenantdb-provision-and-catalog.md)をお試しください。


<!--  Link references.

A [series of related tutorials] is available that build upon this initial deployment.
[link-wtp-overivew-jumpto-saas-tutorials-97j]: saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials

-->

[link-aka-ms-deploywtp-mtapp-52k]: https://aka.ms/deploywtp-mtapp


[link-azure-get-started-powershell-41q]: /powershell/azure/get-started-azureps

[link-github-wingtip-multitenantdb-55g]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB/



<!--  Image references.

[image-deploy-to-azure-blue-48d]: https://aka.ms/deploywtp-mtapp "Button for Deploy to Azure."
-->

[image-deploy-to-azure-blue-48d]: media/saas-multitenantdb-get-started-deploy/deploy.png "Azure にデプロイするためのボタン。"