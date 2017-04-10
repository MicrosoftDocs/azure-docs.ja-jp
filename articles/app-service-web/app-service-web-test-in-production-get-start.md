---
title: "Web アプリの運用環境におけるテストの基本"
description: "Azure App Service Web Apps を運用環境でテストする Test in Production (TiP) 機能について説明します。"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: 4623468d-886e-4203-8012-8f86deb2790b
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/13/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: f9d423e87938e55daeb07268b11c930fb62755b6
ms.openlocfilehash: 8fa7ca18fd2ea3a3f672854a0b2e0397cfdbb2bc
ms.lasthandoff: 12/14/2016


---
# <a name="get-started-with-test-in-production-for-web-apps"></a>Web アプリの運用環境におけるテストの基本
運用環境におけるテスト、つまり実際の利用者のトラフィックを使った Web アプリのライブ テストは、アプリ開発者の間でその [アジャイル開発](https://en.wikipedia.org/wiki/Agile_software_development) 手法に急速に浸透しつつあるテスト ストラテジです。 テスト環境で合成されたデータではなく、運用環境における実際のユーザー トラフィックを使ってアプリの品質をテストすることができます。 実際のユーザーに最新のアプリを公開することにより、デプロイ後に現実に起こりうる問題についての知見を得ることができます。 テスト環境では決して見積もることのできない実際のユーザー トラフィックの量、ベロシティ、多様性に対して、更新後のアプリの機能、パフォーマンス、有用性を検証することができます。

## <a name="traffic-routing-in-app-service-web-apps"></a>App Service Web アプリにおけるトラフィックのルーティング
[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) のトラフィック ルーティング機能を使用して一部のライブ ユーザー トラフィックを[デプロイ スロット](web-sites-staged-publishing.md)にリダイレクトしたうえで、[Azure Application Insights](/services/application-insights/) や [Azure HDInsight](/services/hdinsight/)、またはサードパーティのツール ([New Relic](/marketplace/partners/newrelic/newrelic/) など) を使い、アプリを分析することによって変更内容を検証することができます。 たとえば、App Service を使って次のシナリオを実現することができます。

* サイト全域にデプロイする前に、更新後の機能的なバグを発見したり、パフォーマンスのボトルネックを特定したりする。
* ベータ版アプリに関するユーザビリティのメトリックを評価することによって、変更に対する "Controlled Test Flight (制御された環境で行われるテスト フライト)" を実行する。
* 徐々に最新版に引き上げ、エラーが発生した場合は、支障をきたさないように現行バージョンに引き下げる。 
* 複数のデプロイ スロットで [A/B テスト](https://en.wikipedia.org/wiki/A/B_testing)または[多変量テスト](https://en.wikipedia.org/wiki/Multivariate_testing_in_marketing)を実行することにより、ビジネス上のアプリの成果を最適化する。

### <a name="requirements-for-using-traffic-routing-in-web-apps"></a>Web アプリでトラフィック ルーティングを使用するための要件
* Web アプリが **Standard** レベルまたは **Premium** レベルで実行されている必要があります (複数のデプロイ スロットを使用するために必要)。
* トラフィック ルーティングが適切に機能するためには、ユーザーのブラウザーで Cookie が有効になっている必要があります。 クライアント セッションの有効期間中は、Cookie を使用してクライアントのブラウザーがデプロイ スロットに固定されます。
* トラフィック ルーティングは、Azure PowerShell のコマンドレットによって高度な TiP (Test in Production) のシナリオに対応しています。

## <a name="route-traffic-segment-to-a-deployment-slot"></a>一定量のトラフィックをデプロイ スロットにルーティングする
基本的に運用環境でのテストはどれも、あらかじめ決めた割合のライブ トラフィックを非運用環境のデプロイ スロットにルーティングすることになります。 これを行うには、次の手順に従います。

> [!NOTE]
> ここに記載した手順は、[非運用環境のデプロイ スロット](web-sites-staged-publishing.md)が既に存在し、適切な Web アプリのコンテンツが既に[デプロイされている](web-sites-deploy.md)ことを前提としています。
> 
> 

1. [Azure ポータル](https://portal.azure.com/)にログインします。
2. Web アプリのブレードで、**[設定]** > **[トラフィック ルーティング]** の順にクリックします。
   ![](./media/app-service-web-test-in-production/01-traffic-routing.png)
3. トラフィックのルーティング先となるスロットと、必要なトラフィックの全体に対するパーセンテージとを選択し、 **[保存]**をクリックします。
   
    ![](./media/app-service-web-test-in-production/02-select-slot.png)
4. デプロイ スロットのブレードに移動します。 ライブ トラフィックがルーティングされていることを確認できます。
   
    ![](./media/app-service-web-test-in-production/03-traffic-routed.png)

トラフィック ルーティングの構成後、指定した割合のクライアントが、非運用スロットに対してランダムにルーティングされます。 ただし、一度特定のスロットに対して自動的にルーティングされたクライアントは、そのクライアント セッションの有効期間中、同じスロットに "固定" されます。 ユーザー セッションの固定には Cookie が使用されます。 HTTP 要求を検査すると、後続のすべての要求に `TipMix` という Cookie の存在が確認できます。

![](./media/app-service-web-test-in-production/04-tip-cookie.png)

## <a name="force-client-requests-to-a-specific-slot"></a>クライアントの要求を特定のスロットに強制的に割り当てる
App Service は自動トラフィック ルーティングだけでなく、特定のスロットに要求をルーティングすることもできます。 この方法は、ベータ版アプリへの参加をユーザーが自由に選択または拒否できるようにしたい場合に有効です。 これは、 `x-ms-routing-name` クエリ パラメーターを使用して行います。

`x-ms-routing-name`を使って特定のスロットにユーザーをリダイレクトするには、そのスロットが、トラフィック ルーティング リストに追加済みであることを確認する必要があります。 特定のスロットに対して明示的にルーティングすることが目的であるため、ルーティングの実質的なパーセンテージ設定は重要ではありません。 必要であれば、"ベータ版へのリンク" を作成し、それをユーザーがクリックしてベータ版アプリにアクセスできるようにしてください。

![](./media/app-service-web-test-in-production/06-enable-x-ms-routing-name.png)

### <a name="opt-users-out-of-beta-app"></a>ベータ版アプリへの参加をユーザーが拒否できるようにする
ベータ版アプリへの参加をユーザーが拒否できるようにする方法としては、たとえば以下のようなリンクを Web ページに設置することが考えられます。

    <a href="<webappname>.azurewebsites.net/?x-ms-routing-name=self">Go back to production app</a>

文字列 `x-ms-routing-name=self` に指定されているのは運用スロットです。 このリンクにアクセスしたクライアント ブラウザーは運用スロットにリダイレクトされます。加えて、それ以降の要求には、セッションを運用スロットに固定する `x-ms-routing-name=self` Cookie が格納されます。

![](./media/app-service-web-test-in-production/05-access-production-slot.png)

### <a name="opt-users-in-to-beta-app"></a>ベータ版アプリへの参加をユーザーが許可できるようにする
ベータ版アプリへの参加をユーザーが許可できるようにするには、同じクエリ パラメーターに、非運用スロットの名前を設定します。以下に示したのはその例です。

        <webappname>.azurewebsites.net/?x-ms-routing-name=staging

## <a name="more-resources"></a>その他のリソース
* [Azure App Service の Web アプリのステージング環境を設定する](web-sites-staged-publishing.md)
* [Azure で複雑なアプリケーションを予測どおりにデプロイする](app-service-deploy-complex-application-predictably.md)
* [Azure App Service を使用したアジャイル ソフトウェア開発](app-service-agile-software-development.md)
* [Web アプリに対して DevOps 環境を効果的に使用する](app-service-web-staged-publishing-realworld-scenarios.md)


