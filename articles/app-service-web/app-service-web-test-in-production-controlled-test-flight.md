---
title: "Azure App Service でのフライト デプロイ (ベータ テスト)"
description: "アプリの新機能を運用環境下でテストしたり更新のベータ テストを実施したりする方法について説明したエンド ツー エンドのチュートリアルです。 継続的パブリッシング、スロット、トラフィック ルーティング、Application Insights との統合など、App Service の各種機能を組み合わせて実現しています。"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: 17953c51-38f8-442d-bb0b-f69c1542f0e9
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/02/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: 84b5f28fcd0640fd85b5f8c9d655105790c70d62
ms.lasthandoff: 02/16/2017


---
# <a name="flighting-deployment-beta-testing-in-azure-app-service"></a>Azure App Service でのフライト デプロイ (ベータ テスト)
このチュートリアルでは、[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) と [Azure Application Insights](/services/application-insights/) の各種機能を統合することによって*フライト デプロイ*を行う方法について説明します。

*フライト* は、運用環境で行われる主要なテストの一つとして、利用者数を実際よりも少ない状態で新機能や変更を検証するデプロイ プロセスです。 ベータ テストと酷似しており、"Controlled Test Flight (制御された環境で行われるテスト フライト)" と呼ばれることもあります。 Web プレゼンスを確立している多くの大企業が、 [アジャイル開発](https://en.wikipedia.org/wiki/Agile_software_development)の一工程として、アプリを更新したときにまずこの手法を用いて検証を行っています。 同じ DevOps シナリオは、Azure App Service を使用して、運用環境におけるテスト、継続的パブリッシング、Application Insights を統合することによって実現できます。 この手法には次のような利点があります。

* **更新されたアプリを運用環境にリリースする "*前に*" リアルなフィードバックを得る** - フィードバックを得るタイミングとして、リリース直後に勝るものがあるとすれば、リリース前しかありません。 製品ライフ サイクルの中でいつでも自由に、実際のユーザーのトラフィックと振る舞いに基づいて更新版をテストすることができます。
* **[CTDD (Continuous Test-Driven Development: 継続的テスト駆動開発)](https://en.wikipedia.org/wiki/Continuous_test-driven_development) が強化される** - 運用環境におけるテストに、Application Insights を使った継続的統合とインストルメンテーションを組み合わせることで、製品ライフ サイクルの初期段階で自動的にユーザーによる検証を実施することができます。 テストを手動で実施するよりも短時間で済みます。
* **テストのワークフローを最適化する** - 継続的に監視するインストルメント (計器) を実装し、運用環境におけるテストを自動化することで、[統合](https://en.wikipedia.org/wiki/Integration_testing)、[回帰](https://en.wikipedia.org/wiki/Regression_testing)、[ユーザビリティ](https://en.wikipedia.org/wiki/Usability_testing)、アクセシビリティ、ローカリゼーション、[パフォーマンス](https://en.wikipedia.org/wiki/Software_performance_testing)、[セキュリティ](https://en.wikipedia.org/wiki/Security_testing)、[受け入れ](https://en.wikipedia.org/wiki/Acceptance_testing)など、さまざまな種類のテストを&1; つのプロセスで実行することができます。

フライト デプロイの目的は単にライブ トラフィックをルーティングすることではありません。 この環境が目指しているのは、予期しないバグやパフォーマンスの低下、ユーザー エクスペリエンスの課題など、あらゆる問題の本質を短時間で捉えることです。 注目すべき点は、実際の利用者を対象にテストが実施されることです。 そのため次の段階でしかるべき情報に基づく判断を行うために必要なデータをすべて収集するようにフライト デプロイをセットアップする必要があります。 このチュートリアルでは、Application Insights でデータを収集する方法を紹介していますが、実際のシナリオに合わせて New Relic などのテクノロジを使用することもできます。

## <a name="what-you-will-do"></a>学習内容
このチュートリアルでは、次のシナリオを組み合わせながら、運用環境の App Service アプリをテストする方法について説明します。

* [運用環境のトラフィックをルーティング](app-service-web-test-in-production-get-start.md) する
* [インストルメントをアプリ](../application-insights/app-insights-web-track-usage.md) に実装する
* ベータ版のアプリを継続的にデプロイし、実際のアプリのメトリックを追跡する
* 運用環境のアプリとベータ版のアプリとでメトリックを比較し、コードに加えた変更の結果を確認する

## <a name="what-you-will-need"></a>前提条件
* Azure アカウント
* [GitHub](https://github.com/) アカウント
* Visual Studio 2015 ([Community Edition](https://www.visualstudio.com/en-us/products/visual-studio-express-vs.aspx) をダウンロード可能)
* Git Shell ( [GitHub for Windows](https://windows.github.com/)とともにインストールされます) - これにより、同じセッション内で Git コマンドと PowerShell コマンドの両方を実行できます。
* 最新の [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/download/v0.9.8-September2015/azure-powershell.0.9.8.msi) ビット
* 以下の事柄の基礎知識:
  * [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) テンプレートのデプロイ (「[Azure で複雑なアプリケーションを予測どおりにデプロイする](app-service-deploy-complex-application-predictably.md)」を参照してください)
  * [Git](http://git-scm.com/documentation)
  * [PowerShell](https://technet.microsoft.com/library/bb978526.aspx)

> [!NOTE]
> このチュートリアルを完了するには、Azure アカウントが必要です。
>
> * 無料で [Azure アカウントを開く](https://azure.microsoft.com/pricing/free-trial/)ことができます。Azure の有料サービスを試用できるクレジットが提供されます。このクレジットを使い切ってもアカウントは維持されるため、Web Apps など無料の Azure サービスをご利用になれます。
> * [Visual Studio サブスクライバーの特典を有効にする](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) こともできます - Visual Studio サブスクリプションにより、有料の Azure サービスで使用できるクレジットが毎月提供されます。
>
> Azure アカウントにサインアップする前に Azure App Service の使用を開始したい場合は、「[Azure App Service アプリケーションの作成](https://azure.microsoft.com/try/app-service/)」を参照してください。そこでは、App Service で有効期間の短いスターター Web アプリをすぐに作成できます。 このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。
>
>

## <a name="set-up-your-production-web-app"></a>運用 Web アプリをセットアップする
> [!NOTE]
> このチュートリアルで使用するスクリプトは、GitHub リポジトリからの継続的パブリッシングを自動的に構成します。 これを行うには、GitHub 資格情報が既に Azure に保存されている必要があります。保存されていない場合、スクリプト化されたデプロイは、Web アプリに対するソース管理設定を構成しようとした時点で失敗します。
>
> GitHub 資格情報を Azure に保存するには、Web アプリを [Azure Portal](https://portal.azure.com/) で作成し、[GitHub のデプロイを構成](app-service-continuous-deployment.md)します。 この操作を行うのは&1; 回だけです。
>
>

一般的な DevOps シナリオでは、Azure でライブ実行されているアプリケーションがあり、継続的パブリッシングを通してそれを変更します。 このシナリオでは、開発とテストの済んだテンプレートを運用環境にデプロイします。

1. [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) リポジトリの自分専用のフォークを作成します。 フォークの作成の詳細については、「 [リポジトリをフォークする](https://help.github.com/articles/fork-a-repo/)」を参照してください。 フォークが作成されたら、ブラウザーでそれを確認できます。

   ![](./media/app-service-agile-software-development/production-1-private-repo.png)
2. Git Shell セッションを開きます。 Git Shell をまだ持っていない場合は、この時点で [GitHub for Windows](https://windows.github.com/) をインストールします。
3. 次のコマンドを実行して、フォークのローカル クローンを作成します。

     git clone https://github.com/<フォーク>/ToDoApp.git
4. ローカル クローンを作成したら、*&lt;repository_root>*\ARMTemplates に移動し、次のように一意のサフィックスで deploy.ps1 スクリプトを実行します。

     .\deploy.ps1 –RepoUrl https://github.com/<フォーク>/todoapp.git -ResourceGroupSuffix <サフィックス>
5. メッセージが表示されたら、データベースにアクセスするためのユーザー名とパスワードを入力します。 データベースの資格情報は、リソース グループを更新するときにもう一度指定する必要があるため、忘れないように注意してください。

   Azure のさまざまなリソースのプロビジョニングの進行状況が表示されます。 デプロイが完了すると、スクリプトによってアプリケーションがブラウザー内に起動し、わかりやすいビープ音が鳴ります。
   ![](./media/app-service-web-test-in-production-controlled-test-flight/00.1-app-in-browser.png)
6. Git Shell セッションに戻り、次を実行します。

     .\swap –Name ToDoApp<サフィックス>

   ![](./media/app-service-web-test-in-production-controlled-test-flight/00.2-swap-to-production.png)
7. スクリプトが終了したら、フロントエンドのアドレス (http://ToDoApp*&lt;your_suffix>*.azurewebsites.net/) を参照して、アプリケーションが運用環境で実行されていることを確認します。
8. [Azure ポータル](https://portal.azure.com/) にログインして、何が作成されたかを調べます。

   同じリソース グループ内に&2; つの Web アプリがあり、1 つは名前に `Api` サフィックスが付いていることを確認できます。 リソース グループ ビューを表示している場合は、SQL Database とサーバー、App Service プラン、および Web アプリのステージング スロットも表示されます。 さまざまなリソースを参照し、それらを *&lt;repository_root>*\ARMTemplates\ProdAndStage.json と比較して、テンプレート内にどのように構成されているかを確認します。

   ![](./media/app-service-web-test-in-production-controlled-test-flight/00.3-resource-group-view.png)

運用アプリケーションのセットアップは以上で完了です。  ここで、アプリのユーザビリティが低いというフィードバックが寄せられ、 調査することになったとしましょう。 フィードバックを得るためのインストルメントをアプリに実装します。

## <a name="investigate-instrument-your-client-app-for-monitoringmetrics"></a>調査: 監視とメトリックに必要なインストルメントをクライアント アプリに実装する
1. Visual Studio で *&lt;repository_root>*\src\MultiChannelToDo.sln を開きます。
2. ソリューションを右クリックし、**[ソリューションの NuGet パッケージの管理]**  >  **[復元]** を選択して、すべての Nuget パッケージを復元します。
3. **[MultiChannelToDo.Web]** を右クリックして、 > **[Application Insights テレメトリの追加]**  >  **[設定を構成する]** > [リソース グループを ToDoApp*&lt;your_suffix>* に変更]  >  **[Application Insights をプロジェクトに追加]** の順に選択します。
4. Azure ポータルで、 **MultiChannelToDo.Web** Application Insight リソースのブレードを開きます。 次に、**[アプリケーションの使用状況]** 領域で、**[ブラウザーのページ読み込みデータを収集する方法を説明します]**、[コードのコピー] の順にクリックします。
5. コピーした JS インストルメンテーション コードを *&lt;repository_root>*\src\MultiChannelToDo.Web\app\Index.cshtml の終了 `<heading>` タグの直前に追加します。 Application Insights リソースの一意のインストルメンテーション キーが格納されています。

        <script type="text/javascript">
        var appInsights=window.appInsights||function(config){
            function s(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},r=document,f=window,e="script",o=r.createElement(e),i,u;for(o.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js",r.getElementsByTagName(e)[0].parentNode.appendChild(o),t.cookie=r.cookie,t.queue=[],i=["Event","Exception","Metric","PageView","Trace"];i.length;)s("track"+i.pop());return config.disableExceptionTracking||(i="onerror",s("_"+i),u=f[i],f[i]=function(config,r,f,e,o){var s=u&&u(config,r,f,e,o);return s!==!0&&t["_"+i](config,r,f,e,o),s}),t
        }({
            instrumentationKey:"<your_unique_instrumentation_key>"
        });

        window.appInsights=appInsights;
        appInsights.trackPageView();
        </script>
6. 本体の一番下に次のコードを追加し、マウス クリックのカスタム イベントを Application Insights に送信します。

       <script>
           $(document.body).find("*").click(function(event) {

               appInsights.trackEvent(event.target.tagName + ": " + event.target.className);
           });
       </script>

   ユーザーが Web アプリ内の任意の位置でクリックするとそのたびに、この JavaScript スニペットによって、カスタム イベントが Application Insights に送信されます。
7. Git Shell で、必要な変更を GitHub のフォークにコミットしてプッシュします。 しばらくすると、クライアントによってブラウザーが最新の情報に更新されます。

       git add -A :/
       git commit -m "add AI configuration for client app"
       git push origin master
8. デプロイしたアプリの変更を運用環境にスワップします。

     .\swap –Name ToDoApp<サフィックス>
9. 構成した Application Insights リソースにアクセスします。 [カスタム イベント] をクリックします。

   ![](./media/app-service-web-test-in-production-controlled-test-flight/01-custom-events.png)

   カスタム イベントのメトリックが表示されない場合は、数分待って **[最新の情報に更新]**をクリックしてください。

以下のようなグラフが表示されたとします。

![](./media/app-service-web-test-in-production-controlled-test-flight/02-custom-events-chart-view.png)

その下には、次のようなイベント グリッドが表示されます。

![](./media/app-service-web-test-in-production-controlled-test-flight/03-custom-event-grid-view.png)

ToDoApp アプリケーションのコードによれば **BUTTON** イベントは送信ボタンに、**INPUT** イベントはテキスト ボックスに対応しています。 ここまでは問題ありません。 しかし、かなりの回数クリックされている割には、to-do 項目に対するクリックの回数 ( **LI** イベント) が少ないように思えます。

このことから現行のフォームでは、UI 上でクリックできる領域が一部のユーザーに伝わっていない可能性が疑われます。また、その原因は、リスト項目とそのアイコンにユーザーがポインタを重ねたときのカーソルの形状がテキスト選択スタイルになっていること、という仮説を立てることができます。

![](./media/app-service-web-test-in-production-controlled-test-flight/04-to-do-list-item-ui.png)

これは極端な例ですが、 それでも、アプリに改良を加えてフライト デプロイを実施し、ユーザビリティに関するフィードバックを実際の利用者から得ることは可能です。

### <a name="instrument-your-server-app-for-monitoringmetrics"></a>監視とメトリックに必要なインストルメントをサーバー アプリに実装する
ここで少し脱線します。 このチュートリアルのシナリオで取り上げているのは、クライアント アプリのみですが、片手落ちにならないようサーバー側のアプリもセットアップしましょう。

1. **[MultiChannelToDo]** を右クリックして、 > **[Application Insights テレメトリの追加]**  >  **[設定を構成する]** > [リソース グループを ToDoApp*&lt;your_suffix>* に変更]  >  **[Application Insights をプロジェクトに追加]** の順に選択します。
2. Git Shell で、必要な変更を GitHub のフォークにコミットしてプッシュします。 しばらくすると、クライアントによってブラウザーが最新の情報に更新されます。

       git add -A :/
       git commit -m "add AI configuration for server app"
       git push origin master
3. デプロイしたアプリの変更を運用環境にスワップします。

     .\swap –Name ToDoApp<サフィックス>

これで完了です。

## <a name="investigate-add-slot-specific-tags-to-your-client-app-metrics"></a>調査: スロット固有のタグをクライアント アプリのメトリックに追加する
このセクションでは、複数のデプロイ スロットを構成し、スロット固有のテレメトリを同じ Application Insights リソースに送信します。 こうすることで、異なるスロット (デプロイ環境) からのトラフィック間でテレメトリ データを比較できるので、アプリに対する変更の効果が見やすくなります。 同時に、運用アプリについては必要に応じて監視を継続できるよう、運用環境のトラフィックを他のトラフィックから分離することができます。

ここでの収集の対象となるのはクライアントの動作に関するデータであるため、index.cshtml の [JavaScript コードにテレメトリ初期化子を追加](../application-insights/app-insights-api-filtering-sampling.md) します。 サーバー側のパフォーマンスをテストする場合は、同様の作業をサーバー側のコードに対しても行ってください ( [カスタムのイベントとメトリックのための Application Insights API](../application-insights/app-insights-api-custom-events-metrics.md)に関するページを参照)。

1. まず、先ほど `<heading>` タグに追加した JavaScript ブロックに、以下の&2; つの `//` コメントに挟まれたコードを追加します。

        window.appInsights = appInsights;

        // Begin new code
        appInsights.queue.push(function () {
            appInsights.context.addTelemetryInitializer(function (envelope) {
                var telemetryItem = envelope.data.baseData;
                telemetryItem.properties = telemetryItem.properties || {};
                telemetryItem.properties["Environment"] = "@System.Configuration.ConfigurationManager.AppSettings["environment"]";
            });
        });
        // End new code

        appInsights.trackPageView();

    `appInsights` オブジェクトから送信されるすべてのテレメトリには、この初期化子コードによって、`Environment` というカスタム プロパティが追加されます。
2. 次に、Azure の Web アプリに使用する [スロット設定](web-sites-staged-publishing.md#AboutConfiguration) としてこのカスタム プロパティを追加します。 そのためには、Git Shell セッションで次のコマンドを実行します。

        $app = Get-AzureWebsite -Name todoapp<your_suffix> -Slot production
        $app.AppSettings.Add("environment", "Production")
        $app.SlotStickyAppSettingNames.Add("environment")
        $app | Set-AzureWebsite -Name todoapp<your_suffix> -Slot production

    プロジェクトに含まれている Web.config には最初から `environment` アプリケーション設定が定義されています。 この設定を用いアプリをローカルでテストしたときは、目的とするメトリックが `VS Debugger` でタグ付けされます。 ただし、開発者がその変更を Azure にプッシュするときには、Web アプリの構成に含まれている方の `environment` アプリケーション設定が検出されて使用されます。また、目的のメトリックには、`Production` でタグ付けされます。
3. コードの変更を GitHub のフォークにコミットしてプッシュし、新しいアプリをユーザーが使うまで待ちます (ブラウザーを最新の情報に更新する必要があります)。 新しいプロパティが Application Insights `MultiChannelToDo.Web` リソースに反映されるまでに約 15 分かかります。

        git add -A :/
        git commit -m "add environment property to AI events for client app"
        git push origin master
4. 今度は、もう一度 **[カスタム イベント]** ブレードに移動し、`Environment=Production` でメトリックをフィルタリングします。 このフィルターによって、運用スロットにおける新しいカスタム イベントをすべて表示できます。

    ![](./media/app-service-web-test-in-production-controlled-test-flight/05-filter-on-production-environment.png)
5. **[お気に入り]** ボタンをクリックして、現在のメトリックス エクスプローラーの設定を "**Custom events: Production**" などとして保存します。 後でデプロイ スロット ビューとの間で簡単に切り替えることができます。

   > [!TIP]
   > さらに高度な分析が必要な場合は、 [Application Insights リソースを Power BI と統合](../application-insights/app-insights-export-power-bi.md)することを検討してください。
   >
   >

### <a name="add-slot-specific-tags-to-your-server-app-metrics"></a>スロット固有のタグをサーバー アプリのメトリックに追加する
こちらも片手落ちにならないようサーバー側のアプリもセットアップしましょう。 ただし、JavaScript でインストルメントを実装したクライアント アプリとは異なり、サーバー アプリのスロット固有タグは .NET コードで実装します。

1. *&lt;repository_root>*\src\MultiChannelToDo\Global.asax.cs を開きます。 以下のコード ブロックを名前空間の右中かっこの直前に追加します。

        namespace MultiChannelToDo
        {
                ...

                // Begin new code
            public class ConfigInitializer
            : ITelemetryInitializer
            {
                void ITelemetryInitializer.Initialize(ITelemetry telemetry)
                {
                    telemetry.Context.Properties["Environment"] = System.Configuration.ConfigurationManager.AppSettings["environment"];
                }
            }
                // End new code
        }
2. 以下の `using` ステートメントをファイルの冒頭に追加して名前解決のエラーを修正します。

        using Microsoft.ApplicationInsights.Channel;
        using Microsoft.ApplicationInsights.Extensibility;
3. `Application_Start()` メソッドの先頭に以下のコードを追加します。

        TelemetryConfiguration.Active.TelemetryInitializers.Add(new ConfigInitializer());
4. コードの変更を GitHub のフォークにコミットしてプッシュし、新しいアプリをユーザーが使うまで待ちます (ブラウザーを最新の情報に更新する必要があります)。 新しいプロパティが Application Insights `MultiChannelToDo` リソースに反映されるまでに約 15 分かかります。

        git add -A :/
        git commit -m "add environment property to AI events for server app"
        git push origin master

## <a name="update-set-up-your-beta-branch"></a>更新: ベータ ブランチを設定する
1. *&lt;repository_root>*\ARMTemplates\ProdAndStagetest.json を開いて `appsettings` リソースを探します (`"name": "appsettings"` を検索)。 全部で 4 つ存在します (スロットごとに 1 つ)。
2. 各 `appsettings` リソースについて、`properties` 配列の最後に `"environment": "[parameters('slotName')]"` アプリ設定を追加します。 前の行の最後に必ずコンマを付けてください。

    ![](./media/app-service-web-test-in-production-controlled-test-flight/06-arm-app-setting-with-slot-name.png)

    テンプレートのすべてのスロットに `environment` アプリ設定を追加しました。
3. 同じファイルで、`slotconfignames` リソースを探します (`"name": "slotconfignames"` を検索)。 全部で 2 つ存在します (アプリごとに 1 つ)。
4. 各 `slotconfignames` リソースについて、`appSettingNames` 配列の最後に `"environment"` を追加します。 前の行の最後に必ずコンマを付けてください。

    これで両方のアプリの各デプロイ スロットに `environment` アプリケーション設定が適用されます。  
5. Git Shell セッションから、先ほどと同じリソース グループ サフィックスで次のコマンドを実行します。

        git checkout -b beta
        git push origin beta
        .\deploy.ps1 -RepoUrl https://github.com/<your_fork>/ToDoApp.git -ResourceGroupSuffix <your_suffix> -SlotName beta -Branch beta
6. 確認のメッセージが表示されたら、先ほどと同じ SQL Database の資格情報を指定してください。 次に、リソース グループを更新するよう求められたら、「`Y`」と入力し、`ENTER` キーを押します。

    スクリプトの完了後、元のリソース グループに含まれていたリソースをすべて維持したうえで、最初に作成した "Staging" スロットと同じ構成を持った "beta" という名前のスロットが新たに作成されます。

   > [!NOTE]
   > ここで紹介した各種デプロイ環境の作成手法は、[Azure App Service を使用したアジャイル ソフトウェア開発](app-service-agile-software-development.md)で用いられているものとは異なります。 ここで作成しているのはデプロイ スロットを含んだデプロイ環境です。リソース グループを含んだデプロイ環境ではありません。 リソース グループによるデプロイ環境を利用した場合、運用環境を開発者から遠ざけることができますが、運用環境でのテストは困難となります。運用環境でのテストは、スロットから成るデプロイ環境の方が簡単です。
   >
   >

必要であれば、次の方法でアルファ版のアプリを作成することもできます。

    git checkout -b alpha
    git push origin alpha
    .\deploy.ps1 -RepoUrl https://github.com/<your_fork>/ToDoApp.git -ResourceGroupSuffix <your_suffix> -SlotName beta -Branch alpha

このチュートリアルでは、このままベータ版アプリを使用することにします。

## <a name="update-push-your-updates-to-the-beta-app"></a>更新: 更新をベータ版アプリにプッシュする
改良の対象となるアプリに戻ります。

1. 現在位置がベータ ブランチであることを確認します。

        git checkout beta
2. *&lt;repository_root>*\src\MultiChannelToDo.Web\app\Index.cshtml で `<li>` タグを探し、以下のように `style="cursor:pointer"` 属性を追加します。

    ![](./media/app-service-web-test-in-production-controlled-test-flight/07-change-cursor-style-on-li.png)
3. Azure にコミットしてプッシュします。
4. http://todoapp*&lt;your_suffix>*-beta.azurewebsites.net/ に移動して、変更内容がベータ スロットに反映されていることを確認します。 まだ変更が反映されていない場合は、ブラウザーを最新の情報に更新して新しい JavaScript コードを取得してください。

    ![](./media/app-service-web-test-in-production-controlled-test-flight/08-verify-change-in-beta-site.png)

変更内容がベータ スロットで実行されていることを確認できたら、フライト デプロイを実行することができます。

## <a name="validate-route-traffic-to-the-beta-app"></a>検証: ベータ版アプリにトラフィックをルーティングする
このセクションでは、ベータ版のアプリにトラフィックをルーティングします。 デモンストレーションを単純化するために、ここではユーザー トラフィックの大部分をベータ版アプリにルーティングします。 実際には、ルーティングするトラフィックの量は、具体的な条件によって異なります。 たとえば、microsoft.com と同等の規模があるサイトでは、トラフィック全体の&1;% 未満でも、有益なデータを集めることが可能です。

1. 運用環境のトラフィックをベータ スロットにルーティングするには、Git Shell セッションから次のコマンドを実行します。

        $siteName = "ToDoApp<your suffix>"
        $rule = New-Object Microsoft.WindowsAzure.Commands.Utilities.Websites.Services.WebEntities.RampUpRule
        $rule.ActionHostName = "$siteName-beta.azurewebsites.net"
        $rule.ReroutePercentage = 50
        $rule.Name = "beta"
        Set-AzureWebsite $siteName -Slot Production -RoutingRules $rule

   運用環境のトラフィックの 50% をベータ版アプリの URL (`ActionHostName` プロパティで指定) にルーティングするよう `ReroutePercentage=50` プロパティで指定しています。
2. 次に、http://ToDoApp*&lt;your_suffix>*.azurewebsites.net に移動します。 50% のトラフィックがベータ スロットにリダイレクトされているはずです。
3. Application Insights リソースで、environment="beta" としてメトリックをフィルタリングします。

   > [!NOTE]
   > フィルタリングしたビューを別のお気に入りとして保存しておくと、メトリック エクスプローラーの表示を運用環境とベータ環境とで簡単に切り替えることができます。
   >
   >

Application Insights で、次のような画面が表示されているとします。

![](./media/app-service-web-test-in-production-controlled-test-flight/09-test-beta-site-in-production.png)

`<li>` タグは他のタグに比べてクリック数が多いだけでなく、先ほどと比べても、`<li>` タグのクリック数は大幅に増えていることがわかります。 このことから、 `<li>` タグを更新したことで、クリック可能なタグであることにユーザーが気付き、既に完了したタスクをアプリから消去し始めた、と考えられます。

フライト デプロイのデータから、新しい UI は運用環境での利用に耐えると判断することができます。

## <a name="go-live-move-your-new-code-into-production"></a>本番稼働: 新しいコードを運用環境に移す
以上で、更新したコードを運用環境に移動する準備は整いました。 これまでの作業の最大の恩恵は、運用環境にプッシュする *前* に更新内容の有効性が検証済みであることです。 不安を抱えずにデプロイすることができます。 更新の対象が AngularJS クライアント アプリであったため、今回検証したのはクライアント側のコードだけです。 仮にバックエンドの Web API アプリに変更を加えた場合、同じようにして簡単に変更内容を検証することができます。

1. Git Shell から次のコマンドを実行してトラフィック ルーティング ルールを削除します。

        Set-AzureWebsite $siteName -Slot Production -RoutingRules @()
2. 次の Git コマンドを実行します。

        git checkout master
        git pull origin master
        git merge beta
        git push origin master
3. 数分後ステージング スロットに新しいコードがデプロイされたら、http://ToDoApp*&lt;your_suffix>*-staging.azurewebsites.net を起動し、ステージング スロットで新しいコードが使用できる状態になっていることを確認します。 フォークの master ブランチはアプリのステージング スロットにリンクされていることに注意してください。
4. ここで、ステージング スロットを運用スロットにスワップします。

        cd <ROOT>\ToDoApp\ARMTemplates
        .\swap.ps1 -Name todoapp<your_suffix>

## <a name="summary"></a>概要
Azure App Service を使用すると、中小規模の企業が顧客向けのアプリを運用環境で簡単にテストすることができます。従来そのようなテストを実施できるのは大企業だけでした。 このチュートリアルでは、App Service と Application Insights との統合によるフライト デプロイを中心に、DevOps 手法で運用環境下のテストを実現するために必要な情報を紹介しました。この情報が皆様のお役に立てればさいわいです。

## <a name="more-resources"></a>その他のリソース
* [Azure App Service を使用したアジャイル ソフトウェア開発](app-service-agile-software-development.md)
* [Azure App Service の Web アプリのステージング環境を設定する](web-sites-staged-publishing.md)
* [Azure で複雑なアプリケーションを予測どおりにデプロイする](app-service-deploy-complex-application-predictably.md)
* [Azure リソース マネージャーのテンプレートの作成](../azure-resource-manager/resource-group-authoring-templates.md)
* [JSONLint - JSON Validator に関するページ](http://jsonlint.com/)
* [Git のブランチ機能 - 基本的なブランチとマージに関するページ](http://www.git-scm.com/book/en/v2/Git-Branching-Basic-Branching-and-Merging)
* [Azure PowerShell](/powershell/azureps-cmdlets-docs)
* [Project Kudu Wiki](https://github.com/projectkudu/kudu/wiki)

