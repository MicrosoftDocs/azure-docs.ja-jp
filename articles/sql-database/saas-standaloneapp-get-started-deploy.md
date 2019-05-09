---
title: シングルテナント SaaS チュートリアル - Azure SQL Database | Microsoft Docs
description: Azure SQL Database を使用するスタンドアロン シングルテナント SaaS アプリケーションをデプロイおよび操作します。
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: sstein
manager: craigg
ms.date: 11/07/2018
ms.openlocfilehash: 4dbf53df4d3f34e80757f9575981b4b053587d97
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58012685"
---
# <a name="deploy-and-explore-a-standalone-single-tenant-application-that-uses-azure-sql-database"></a>Azure SQL Database を使用するスタンドアロン シングルテナント アプリケーションをデプロイおよび操作する

このチュートリアルでは、スタンドアロン アプリケーション、テナントごとのアプリ、またはパターンを使用して開発された Wingtip Tickets SaaS サンプル アプリケーションをデプロイして調査します。  アプリケーションの目的は、マルチテナント SaaS シナリオの有効化を簡単にする Azure SQL Database の機能を紹介することにあります。

スタンドアロン アプリケーションまたはテナントごとのアプリケーション パターンでは、各テナントに対するアプリケーション インスタンスをデプロイします。  各アプリケーションは、特定のテナント用に構成され、別個の Azure リソース グループにデプロイされます。 アプリケーションの複数のインスタンスは、マルチテナント ソリューションを提供するようにプロビジョニングされます。 このパターンは、テナント分離が最優先事項になっているテナントの数がより少ない場合に最適です。 Azure には、リソースをテナントのサブスクリプションにデプロイできるパートナー プログラムがあり、テナントの代わりにサービス プロバイダーによって管理されます。 

このチュートリアルでは、3 つのテナントに対応する 3 つのスタンドアロン アプリケーションを Azure サブスクリプションにデプロイします。  ユーザーには、個々のアプリケーション コンポーネントを確認して操作するフル アクセスがあります。

アプリケーションのソース コードと管理スクリプトは、[WingtipTicketsSaaS-StandaloneApp](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp) GitHub リポジトリから入手できます。 このアプリケーションは Visual Studio 2015 で作成されました。更新なしでは、Visual Studio 2017 で開いたり、コンパイルしたりできません。


このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Wingtip Tickets SaaS スタンドアロン アプリケーションをデプロイする方法。
> * アプリケーションのソース コード、および管理スクリプトを取得する場所。
> * アプリを構成するサーバーおよびデータベースについて。

追加のチュートリアルがリリースされます。 これにより、このアプリケーション パターンに基づいて管理シナリオの範囲を調べることができます。   

## <a name="deploy-the-wingtip-tickets-saas-standalone-application"></a>Wingtip Tickets SaaS スタンドアロン アプリケーションをデプロイする

次のように 3 つの指定されたテナント用にアプリをデプロイします。

1. 青い **[Azure へのデプロイ]** ボタンを 1 つずつクリックして、[Azure Portal](https://portal.azure.com) でデプロイ テンプレートを開きます。 各テンプレートには、2 つのパラメーター値が必要です。新しいリソース グループの名前と、アプリの他のデプロイからこのデプロイを区別するユーザー名です。 次の手順では、これらの値を設定するための詳細を提供します。<br><br>
    <a href="https://aka.ms/deploywingtipsa-contoso" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a> &nbsp; **Contoso Concert Hall**
<br><br>
    <a href="https://aka.ms/deploywingtipsa-dogwood" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a> &nbsp; **Dogwood Dojo**
<br><br>
    <a href="https://aka.ms/deploywingtipsa-fabrikam" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a> &nbsp; **Fabrikam Jazz Club**

2. 各デプロイに必須のパラメーター値を入力します。

    > [!IMPORTANT]
    > 一部の認証とサーバー ファイアウォールは、デモンストレーションのために、意図的に保護されていません。 アプリケーション デプロイごとに**新しいリソース グループを作成します**。  既存のリソース グループを使用しないでください。 運用環境にはこのアプリケーション、またはこのアプリケーションが作成したリソースを使用しないでください。 関連する課金を停止するために、サンプル アプリケーションの操作が終了したら、すべてのリソース グループを削除してください。

    リソース名にはアルファベットの小文字、数字、およびハイフンのみを使用することをお勧めします。
    * **リソース グループ**の場合、[新規作成] を選択して、リソース グループの名前を小文字で指定します。 **wingtip-sa-\<venueName\>-\<user\>** は、推奨されるパターンです。  \<VenueName\> には、空白なしの会場の名前を代入します。 \<user\> には、次のいずれかのユーザー値を代入します。  このパターンでは、リソース グループ名は *wingtip-sa-contosoconcerthall-af1*、*wingtip-sa-dogwooddojo-af1*、*wingtip-sa-fabrikamjazzclub-af1* のいずれかになります。
    * ドロップダウン から **[場所]** を選択します。

    * **ユーザー** - イニシャルに一桁の数字を付加するなど、短いユーザー値をお勧めします (*af1* など)。


3. **アプリケーションをデプロイします**。

    * 使用条件に同意したら、チェック ボックスをオンにします。
    * **[購入]** をクリックします。

4. **[通知]** (検索ボックスの右にあるベル アイコン) をクリックして、3 つのデプロイすべての状態を監視します。 アプリのデプロイには、約 5 分間かかります。


## <a name="run-the-applications"></a>アプリケーションの実行

アプリは、イベントを開催する会場の窓口の機能を提供します。  会場は、アプリケーションのテナントです。 各会場が、イベントを表示してチケットを販売するためのパーソナライズされた Web サイトを利用できます。 会場の種類には、コンサート ホール、ジャズ クラブ、スポーツ クラブなどがあります。 サンプルでは、場所の種類によって、会場の Web サイトに表示される背景写真が決定されます。   スタンドアロン アプリ モデルでは、各会場に、独自のスタンドアロン SQL データベースを備えた個別のアプリケーション インスタンスがあります。

1. 個別のブラウザー タブで、次のように 3 つのテナントごとにイベント ページを開きます。

   - http://events.contosoconcerthall.&lt;user&gt;.trafficmanager.net
   - http://events.dogwooddojo.&lt;user&gt;.trafficmanager.net
   - http://events.fabrikamjazzclub.&lt;user&gt;.trafficmanager.net

     (各 URL で、&lt;user&gt; を、デプロイのユーザー値に置き換えてください。)

   ![events](./media/saas-standaloneapp-get-started-deploy/fabrikam.png)

アプリは、着信要求の分散を制御するために [*Azure Traffic Manager*](../traffic-manager/traffic-manager-overview.md) を使用します。 各テナント固有のアプリ インスタンスには、URL 内のドメイン名の一部としてテナント名が含まれています。 すべてのテナント URL に、特定の**ユーザー**値が含まれます。 URL の形式を次に示します。
- http://events.&lt;venuename&gt;.&lt;user&gt;.trafficmanager.net

各テナントのデータベースの**場所**は、対応するデプロイされたアプリのアプリ設定に含まれています。

運用環境では、通常、[*会社のインターネット ドメインで Traffic Manager プロファイルの URL が参照*](../traffic-manager/traffic-manager-point-internet-domain.md)されるようにCNAME DNS レコードを作成します。


## <a name="explore-the-servers-and-tenant-databases"></a>サーバーおよびテナント データベースを操作する

デプロイされたリソースの一部を見てみましょう。

1. [Azure Portal](https://portal.azure.com) で、リソース グループの一覧を参照します。
2. 3 つのテナントのリソース グループが表示されるはずです。
3. **wingtip-sa-fabrikam-&lt;user&gt;** リソース グループを開きます。これには、Fabrikam Jazz Club デプロイのリソースが含まれます。  **fabrikamjazzclub-&lt;user&gt;** サーバーには、**fabrikamjazzclub** データベースが含まれます。

各テナント データベースは 50 DTU *スタンドアロン* データベースです。

## <a name="additional-resources"></a>その他のリソース

<!--
* Additional [tutorials that build on the Wingtip SaaS application](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* To learn about elastic pools, see [*What is an Azure SQL elastic pool*](sql-database-elastic-pool.md)
* To learn about elastic jobs, see [*Managing scaled-out cloud databases*](sql-database-elastic-jobs-overview.md)
-->

- マルチテナント SaaS アプリケーションの詳細については、[マルチテナント SaaS アプリケーションの設計パターン](saas-tenancy-app-design-patterns.md)に関するページをご覧ください。

 
## <a name="delete-resource-groups-to-stop-billing"></a>課金を停止するリソース グループを削除する ##

サンプルを使用し終えたら、関連付けられた課金を停止するために、作成したすべてのリソース グループを削除してください。

## <a name="next-steps"></a>次の手順

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * Wingtip Tickets SaaS スタンドアロン アプリケーションをデプロイする方法。
> * アプリを構成するサーバーおよびデータベースについて。
> * サンプル リソースを削除して、関連する課金を停止する方法。

次に、[プロビジョニングとカタログ](saas-standaloneapp-provision-and-catalog.md)のチュートリアルを確認して、一定の範囲のスキーマ管理やテナント分析などのクロス テナント シナリオを有効にするテナントのカタログの使用について調査してください。
 

