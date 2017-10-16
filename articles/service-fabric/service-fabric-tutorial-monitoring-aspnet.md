---
title: "Azure Service Fabric での ASP.NET Core サービスの監視と診断 | Microsoft Docs"
description: "Azure Service Fabric の ASP.NET Core アプリケーションの監視と診断を設定する方法を説明します。"
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/14/2017
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: 68788efffd27edf2813cf455490b651c2c7106a8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-and-diagnose-an-aspnet-core-application-on-service-fabric"></a>Service Fabric での ASP.NET Core アプリケーションの監視と診断
このチュートリアルは、シリーズの第 4 部です。 Application Insights を使用して、Service Fabric クラスターで実行されている ASP.NET Core アプリケーションの監視と診断を設定する手順を説明します。 このチュートリアルの第 1 部「[.NET Service Fabric アプリケーションを構築する](service-fabric-tutorial-create-dotnet-app.md)」で開発したアプリケーションから利用統計情報を収集します。 

シリーズの第 4 部で学習する内容は次のとおりです。
> [!div class="checklist"]
> * Application Insights をお使いのアプリケーション用に構成する
> * 応答の利用統計情報を収集してサービス間の HTTP ベース通信を追跡する
> * Application Insights でアプリケーション マップ機能を使用する
> * Application Insights API を使用してカスタム イベントを追加する

このチュートリアル シリーズで学習する内容は次のとおりです。
> [!div class="checklist"]
> * [.NET Service Fabric アプリケーションを構築する](service-fabric-tutorial-create-dotnet-app.md)
> * [アプリケーションをリモート クラスターにデプロイする](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * [Visual Studio Team Services を使用して CI/CD を構成する](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * アプリケーションの監視と診断を設定する

## <a name="prerequisites"></a>前提条件
このチュートリアルを開始する前に
- Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成します。
- [Visual Studio 2017 をインストール](https://www.visualstudio.com/)し、**Azure 開発**ワークロードと **ASP.NET および Web 開発**ワークロードをインストールします。
- [Service Fabric SDK をインストール](service-fabric-get-started.md)します。

## <a name="download-the-voting-sample-application"></a>投票サンプル アプリケーションをダウンロードする
[このチュートリアル シリーズの第 1 部](service-fabric-tutorial-create-dotnet-app.md)で投票サンプル アプリケーションをビルドしていない場合は、ダウンロードすることができます。 コマンド ウィンドウまたはターミナルで次のコマンドを実行して、サンプル アプリ リポジトリをローカル コンピューターに複製します。

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="set-up-an-application-insights-resource"></a>Application Insights リソースを設定する
Application Insights は、Azure のアプリケーション パフォーマンス管理プラットフォームであり、アプリケーションを監視および診断するための Service Fabric の推奨プラットフォームです。 Application Insights リソースを作成するには、[Azure Portal](https://portal.azure.com) に移動します。 左側のナビゲーション メニューで **[新規]** をクリックして Azure Marketplace を開きます。 **[監視 + 管理]**、**[Application Insights]** の順にクリックします。

![新しい AI リソースの作成](./media/service-fabric-tutorial-monitoring-aspnet/new-ai-resource.png)

作成するリソースの属性に関する必要な情報を、ここで入力する必要があります。 適切な*名前*、*リソース グループ*、および*サブスクリプション*を入力します。 *[場所]* に、将来 Service Fabric クラスターをデプロイする場所を設定します。 このチュートリアルではローカル クラスターにアプリをデプロイするため、*[場所]* フィールドは関係ありません。 *[アプリケーションの種類]* は "ASP.NET Web アプリケーション" のままにしておく必要があります。 

![AI リソース属性](./media/service-fabric-tutorial-monitoring-aspnet/new-ai-resource-attrib.png)

必要な情報を入力したら、**[作成]** をクリックしてリソースをプロビジョニングします。終わるまでに 1 分ほどかかります。 
<!-- When completed, navigate to the newly deployed resource, and find the "Instrumentation Key" (visible in the "Essentials" drop down section). Copy it to clipboard, since we will need it in the next step. -->

## <a name="add-application-insights-to-the-applications-services"></a>Application Insights をアプリケーションのサービスに追加する

昇格された特権で Visual Studio 2017 を起動します。 これを行うには、スタート メニューで Visual Studio アイコンを右クリックし、**[管理者として実行]** を選択します。 **[ファイル]**  >  **[開く]**  >  **[Project/Solution]\(プロジェクト/ソリューション\)** の順にクリックし、投票アプリケーション (チュートリアルの第 1 部または git からの複製のいずれかで作成) に移動します。 *Voting.sln* を開き、アプリケーションの NuGet パッケージを復元するかを尋ねるプロンプトが表示されたら **[はい]** をクリックします。

次の手順に従って、VotingWeb と VotingData の両方のサービス用に Application Insights を構成します。
1. サービス名を右クリックして、**[Application Insights の構成...]** をクリックします。

    ![AI の構成](./media/service-fabric-tutorial-monitoring-aspnet/configure-ai.png)

2. **[開始 (無料)]**.をクリックします。
3. (Azure サブスクリプションの設定にも使用した) アカウントにサインインし、Application Insights リソースを作成したサブスクリプションを選択します。 [リソース] ボックスの *[Application Insights の既存のリソース]* でリソースを探します。 **[登録]** をクリックして Application Insights をサービスに追加します。

    ![AI の登録](./media/service-fabric-tutorial-monitoring-aspnet/register-ai.png)

4. **[完了]** をクリックすると、ポップアップ表示されるダイアログ ボックスの操作が完了になります。
    
アプリケーションの**両方**のサービスで上記の手順を実行して、アプリケーション用の Application Insights の構成を完了します。 受信要求と送信要求、およびサービス間の通信を確認するために、両方のサービスで同じ Application Insights リソースを使用します。

## <a name="add-the-microsoftapplicationinsightsservicefabricnative-nuget-to-the-services"></a>Microsoft.ApplicationInsights.ServiceFabric.Native NuGet をサービスに追加する

Application Insights では、シナリオによって使い分けられる Service Fabric 固有の 2 つの NuGets があります。 1 つは Service Fabric のネイティブ サービスで使用し、もう 1 つはコンテナーおよびゲストの実行可能ファイルで使用します。 今回は、Microsoft.ApplicationInsights.ServiceFabric.Native NuGet を使用し、そこから得られるサービス コンテキストの知識を活用します。 Application Insights SDK と Service Fabric 固有の NuGets の詳細については、「[Microsoft Application Insights for Service Fabric (Service Fabric での Microsoft Application Insights)](https://github.com/Microsoft/ApplicationInsights-ServiceFabric/blob/develop/README.md)」をご覧ください。 

次に、NuGet を設定する手順を示します。
1. ソリューション エクスプローラーの上部にある **'Voting' ソリューション**を右クリックし、**[ソリューションの NuGet パッケージの管理...]** をクリックします。
2. [NuGet - ソリューション] ウィンドウ上部のナビゲーション メニューにある **[参照]** をクリックし、検索バーの横にある **[プレリリースを含める]** チェックボックスをオンにします。
3. `Microsoft.ApplicationInsights.ServiceFabric.Native` を検索し、適切な NuGet パッケージをクリックします。
4. 右側で、アプリケーションの 2 つのサービス (**[VotingWeb]** と **[VotingData]**) の横にある 2 つのチェックボックスをオンにして、**[インストール]** をクリックします。
    ![AI 登録の完了](./media/service-fabric-tutorial-monitoring-aspnet/aisdk-sf-nuget.png)
5. ポップアップで表示される *[変更の確認]* ダイアログ ボックスで **[OK]** をクリックし、*[ライセンスの同意]* に同意します。 これで、サービスへの NuGet の追加が完了します。
6. 今度は 2 つのサービスに利用統計情報の初期化子を設定する必要があります。 この処理を行うには、*VotingWeb.cs* と *VotingData.cs* を開きます。 両方に対して、次の 2 つの手順を実行します。
    1. それぞれの *\<サービス名>.cs* の上部に、次の 2 つの *using* ステートメントを追加します。
    
    ```csharp
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.ServiceFabric;
    ```
    
    2. *CreateServiceInstanceListeners()* または *CreateServiceReplicaListeners()* の入れ子になった *return* ステートメント内で、*ConfigureServices*  >  *services* の下に宣言されている 2 つのシングルトン サービスの間に、以下を追加します。`.AddSingleton<ITelemetryInitializer>((serviceProvider) => FabricTelemetryInitializerExtension.CreateFabricTelemetryInitializer(serviceContext))`
    これにより、お使いの利用統計情報に*サービス コンテキスト*が追加され、Application Insights の利用統計情報のソースをより深く理解できるようになります。 *VotingWeb.cs* の入れ子になった *return* ステートメントは、次のようになります。
    
    ```csharp
    return new WebHostBuilder().UseWebListener()
        .ConfigureServices(
            services => services
                .AddSingleton<StatelessServiceContext>(serviceContext)
                .AddSingleton<ITelemetryInitializer>((serviceProvider) => FabricTelemetryInitializerExtension.CreateFabricTelemetryInitializer(serviceContext))
                .AddSingleton<HttpClient>())
        .UseContentRoot(Directory.GetCurrentDirectory())
        .UseStartup<Startup>()
        .UseApplicationInsights()
        .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
        .UseUrls(url)
        .Build();
    ```

    同様に、*VotingData.cs* では次のようになります。

    ```csharp
    return new WebHostBuilder()
        .UseKestrel()
        .ConfigureServices(
            services => services
                .AddSingleton<StatefulServiceContext>(serviceContext)
                .AddSingleton<ITelemetryInitializer>((serviceProvider) => FabricTelemetryInitializerExtension.CreateFabricTelemetryInitializer(serviceContext))
                .AddSingleton<IReliableStateManager>(this.StateManager))
        .UseContentRoot(Directory.GetCurrentDirectory())
        .UseStartup<Startup>()
        .UseApplicationInsights()
        .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
        .UseUrls(url)
        .Build();
    ```

この時点で、アプリケーションをデプロイする準備が整いました。 上部にある **[開始]** をクリックする (または **F5** キーを押す) と、Visual Studio でアプリケーションがビルドおよびパッケージ化され、ローカル クラスターが設定され、そのクラスターにアプリケーションがデプロイされます。 

アプリケーションのデプロイが完了した後に [localhost:8080](localhost:8080) にアクセスすると、単一ページの投票アプリケーションのサンプルを確認できます。 サンプル データと利用統計情報を作成する項目をいくつか選んで投票します。この例ではデザートにしています!

![AI 投票のサンプル](./media/service-fabric-tutorial-monitoring-aspnet/vote-sample.png)

投票を追加し終わったら、投票オプションの一部を*削除*することもできます。

## <a name="view-telemetry-and-the-app-map-in-application-insights"></a>Application Insights で利用統計情報とアプリケーション マップを表示する 

Azure Portal で Application Insights リソースにアクセスして、リソースの左側のナビゲーション バーで *[構成]* の下にある **[プレビュー]** をクリックします。 使用可能なプレビューの一覧で、*[複数ロールのアプリケーション マップ]* を **[オン]** にします。

![AI アプリケーション マップを有効にする](./media/service-fabric-tutorial-monitoring-aspnet/ai-appmap-enable.png)

**[概要]** をクリックして、お使いのリソースのランディング ページに戻ります。 次に、上部の **[検索]** をクリックして受信トレースを確認します。 Application Insights にトレースが表示されるまで数分かかります。 何も表示されない場合は、しばらく待ってから上部の **[更新]** をクリックします。
![AI トレースの確認](./media/service-fabric-tutorial-monitoring-aspnet/ai-search.png)

*[検索]* ウィンドウを下にスクロールすると、Application Insights ですぐに利用できるすべての受信利用統計情報が表示されます。 投票アプリケーションで行った各操作によって、*VotingWeb* からの送信 PUT 要求 (PUT Votes/Put [name]) と *VotingData* からの受信 PUT 要求 (PUT VoteData/Put [name]) が発生し、その後、表示データを最新の情報に更新するように求めるペアの GET 要求が発生します。 それらは HTTP 要求であるため、ローカルホストでも HTTP に対する依存関係トレースが発生します。 1 つの投票がどのように追加されるかが分かるサンプルを次に示します。![AI 要求トレースのサンプル](./media/service-fabric-tutorial-monitoring-aspnet/sample-request.png)

トレースのひとつをクリックすると、その詳細を表示できます。 Application Insights で提供される情報には、*応答時間*や*要求 URL* など、要求に関する有用な情報があります。 また、Service Fabric 固有の NuGet を追加したため、後述の *Custom Data* セクションでは、お使いのアプリケーションに関する情報を Service Fabric クラスターのコンテキストで取得することもできます。 これにはサービス コンテキストが含まれるため、要求のソースの *PartitionID* と *ReplicaId* を確認して、アプリケーションでのエラー診断時に問題箇所を適切に特定することができます。

![AI トレースの詳細](./media/service-fabric-tutorial-monitoring-aspnet/trace-details.png)

また、アプリケーション マップを有効にしたため、*[概要]* ページで**アプリケーション マップ** アイコンをクリックすると、接続されている両方のサービスが表示されます。

![AI トレースの詳細](./media/service-fabric-tutorial-monitoring-aspnet/app-map.png)

アプリケーション マップを利用すると、アプリケーションのトポロジをより深く理解できます。特に、連携して動く複数の異なるサービスを追加し始めるときには有用です。 また、これによって要求の成功率に関する基本的なデータを取得し、失敗した要求を診断して、問題が起きた可能性のある場所を把握するのに役立てることもできます。 アプリケーション マップの使用方法について詳しくは、「[Application Insights のアプリケーション マップ](../application-insights/app-insights-app-map.md)」をご覧ください。

## <a name="add-custom-instrumentation-to-your-application"></a>カスタム インストルメンテーションをアプリケーションに追加する

Application Insights では多くの利用統計情報をすぐに利用できますが、さらにカスタム インストルメンテーションを追加することもできます。 これは、ビジネス ニーズに基づいて行うことも、またはアプリケーションでの問題発生時に診断を改善するために行うこともできます。 Application Insights にはカスタム イベントとメトリックを取り込める API があります。詳細については[こちら](../application-insights/app-insights-api-custom-events-metrics.md)をご覧ください。

*VoteDataController.cs* (*VotingData*  >  *Controllers* の下) にカスタム イベントを追加して、基礎となる *votesDictionary* に対していつ投票が追加され、削除されるかを追跡します。 
1. 他の using ステートメントの最後に `using Microsoft.ApplicationInsights;` を追加します。
2. クラスの最初の *IReliableStateManager* の作成の下で、新しい *TelemetryClient* を次のように宣言します。`private TelemetryClient telemetry = new TelemetryClient();`
3. *Put()* 関数に、投票が追加されたことを確認するイベントを追加します。 トランザクションが完了した後の、return *OkResult* ステートメントの直前に `telemetry.TrackEvent($"Added a vote for {name}");` を追加します。
4. *Delete()* には、*votesDictionary* に指定された投票オプションに対応する投票を含むという条件の "if/else" が存在します。 
    1. 投票が削除されたことを確認するイベントを、*if* ステートメント内の *await tx.CommitAsync()* の後に追加します。`telemetry.TrackEvent($"Deleted votes for {name}");`
    2. 削除が行われなかったことを示すイベントを、*else* ステートメント内の以下の return ステートメントの前に追加します。`telemetry.TrackEvent($"Unable to delete votes for {name}, voting option not found");`

以下に、イベントを追加した後の *Put()* 関数と *Delete()* 関数の例を示します。

```csharp
// PUT api/VoteData/name
[HttpPut("{name}")]
public async Task<IActionResult> Put(string name)
{
    var votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

    using (ITransaction tx = this.stateManager.CreateTransaction())
    {
        await votesDictionary.AddOrUpdateAsync(tx, name, 1, (key, oldvalue) => oldvalue + 1);
        await tx.CommitAsync();
    }

    telemetry.TrackEvent($"Added a vote for {name}");
    return new OkResult();
}

// DELETE api/VoteData/name
[HttpDelete("{name}")]
public async Task<IActionResult> Delete(string name)
{
    var votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

    using (ITransaction tx = this.stateManager.CreateTransaction())
    {
        if (await votesDictionary.ContainsKeyAsync(tx, name))
        {
            await votesDictionary.TryRemoveAsync(tx, name);
            await tx.CommitAsync();
            telemetry.TrackEvent($"Deleted votes for {name}");
            return new OkResult();
        }
        else
        {
            telemetry.TrackEvent($"Unable to delete votes for {name}, voting option not found");
            return new NotFoundResult();
        }
    }
}
```

これらの変更が完了したら、アプリケーションを **[開始]** して、最新バージョンのビルドとデプロイを行います。 アプリケーションのデプロイが完了したら、[localhost:8080](localhost:8080) にアクセスし、一部の投票オプションの追加と削除を行います。 次に、Application Insights リソースに戻って、最新の実行に関するトレースを確認します (前と同様に、トレースが Application Insights に表示されるまでに 1 ～ 2 分かかります)。 追加および削除したすべての投票の "カスタム イベント" が、すべての応答の利用統計情報とともに表示されます。 

![カスタム イベント](./media/service-fabric-tutorial-monitoring-aspnet/custom-events.png)

## <a name="next-steps"></a>次のステップ
このチュートリアルで学習した内容は次のとおりです。
> [!div class="checklist"]
> * Application Insights をお使いのアプリケーション用に構成する
> * 応答の利用統計情報を収集してサービス間の HTTP ベース通信を追跡する
> * Application Insights でアプリケーション マップ機能を使用する
> * Application Insights API を使用してカスタム イベントを追加する

ASP.NET アプリケーションの監視と診断の設定が完了したところで、以下を試してみてください。
- [Service Fabric での監視と診断についてさらに詳しく知る](service-fabric-diagnostics-overview.md)
- [Application Insights を使用した Service Fabric イベントの分析](service-fabric-diagnostics-event-analysis-appinsights.md)
- Application Insights の詳細については、[Application Insights のドキュメント](https://docs.microsoft.com/en-us/azure/application-insights/)をご覧ください。
