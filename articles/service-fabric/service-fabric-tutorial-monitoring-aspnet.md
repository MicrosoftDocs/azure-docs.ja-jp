---
title: Azure で Service Fabric 上の ASP.NET Core サービスを監視および診断する | Microsoft Docs
description: このチュートリアルでは、Azure Service Fabric の ASP.NET Core アプリケーションの監視と診断をセットアップする方法を説明します。
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/10/2019
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: 1f18aef12978b3df1ba1fd654ea4a0e9548a4b46
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "68228090"
---
# <a name="tutorial-monitor-and-diagnose-an-aspnet-core-application-on-service-fabric-using-application-insights"></a>チュートリアル:Application Insights を使用して Service Fabric 上の ASP.NET Core アプリケーションを監視および診断する

このチュートリアルは、シリーズの第 5 部です。 Application Insights を使用して、Service Fabric クラスターで実行されている ASP.NET Core アプリケーションの監視と診断を設定する手順を説明します。 このチュートリアルの第 1 部「[.NET Service Fabric アプリケーションを構築する](service-fabric-tutorial-create-dotnet-app.md)」で開発したアプリケーションからテレメトリを収集します。

シリーズの第 4 部で学習する内容は次のとおりです。
> [!div class="checklist"]
> * Application Insights をお使いのアプリケーション用に構成する
> * 応答のテレメトリを収集してサービス間の HTTP ベース通信を追跡する
> * Application Insights でアプリ マップ機能を使用する
> * Application Insights API を使用してカスタム イベントを追加する

このチュートリアル シリーズで学習する内容は次のとおりです。
> [!div class="checklist"]
> * [.NET Service Fabric アプリケーションを構築する](service-fabric-tutorial-create-dotnet-app.md)
> * [アプリケーションをリモート クラスターにデプロイする](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * [ASP.NET Core フロントエンド サービスに HTTPS エンドポイントを追加する](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
> * [Azure Pipelines を使用して CI/CD を構成する](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * アプリケーションの監視と診断を設定する

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に

* Azure サブスクリプションを持っていない場合は[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成する
* [Visual Studio 2019 をインストール](https://www.visualstudio.com/)し、**Azure 開発**ワークロードと **ASP.NET および Web 開発**ワークロードをインストールします。
* [Service Fabric SDK をインストール](service-fabric-get-started.md)します。

## <a name="download-the-voting-sample-application"></a>投票サンプル アプリケーションをダウンロードする

[このチュートリアル シリーズの第 1 部](service-fabric-tutorial-create-dotnet-app.md)で投票サンプル アプリケーションをビルドしていない場合は、ダウンロードすることができます。 コマンド ウィンドウまたはターミナルで次のコマンドを実行して、サンプル アプリ リポジトリをローカル コンピューターに複製します。

```git
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="set-up-an-application-insights-resource"></a>Application Insights リソースを設定する

Application Insights は、Azure のアプリケーション パフォーマンス管理プラットフォームであり、アプリケーションを監視および診断するための Service Fabric の推奨プラットフォームです。

Application Insights リソースを作成するには、[Azure Portal](https://portal.azure.com) に移動します。 左側のナビゲーション メニューで **[リソースの作成]** をクリックして Azure Marketplace を開きます。 **[監視 + 管理]** 、 **[Application Insights]** の順にクリックします。

![新しい AI リソースの作成](./media/service-fabric-tutorial-monitoring-aspnet/new-ai-resource.png)

作成するリソースの属性に関する必要な情報を、ここで入力する必要があります。 適切な*名前*、*リソース グループ*、および*サブスクリプション*を入力します。 *[場所]* に、将来 Service Fabric クラスターをデプロイする場所を設定します。 このチュートリアルではローカル クラスターにアプリをデプロイするため、 *[場所]* フィールドは関係ありません。 *[アプリケーションの種類]* は "ASP.NET Web アプリケーション" のままにしておく必要があります。

![AI リソース属性](./media/service-fabric-tutorial-monitoring-aspnet/new-ai-resource-attrib.png)

必要な情報を入力したら、 **[作成]** をクリックしてリソースをプロビジョニングします。終わるまでに 1 分ほどかかります。
<!-- When completed, navigate to the newly deployed resource, and find the "Instrumentation Key" (visible in the "Essentials" drop down section). Copy it to clipboard, since we will need it in the next step. -->

## <a name="add-application-insights-to-the-applications-services"></a>Application Insights をアプリケーションのサービスに追加する

スタート メニューで Visual Studio アイコンを右クリックし、 **[管理者として実行]** を選択して、昇格された特権で Visual Studio 2019 を起動します。 **[ファイル]**  >  **[開く]**  >  **[プロジェクト/ソリューション]** の順にクリックし、投票アプリケーション (チュートリアルの第 1 部で作成済みまたは git から複製済み) に移動します。 *Voting.sln* を開きます。 アプリケーションの NuGet パッケージを復元するかどうかをたずねるプロンプトが表示されたら、 **[はい]** をクリックします。

次の手順に従って、VotingWeb と VotingData の両方のサービス用に Application Insights を構成します。

1. サービス名を右クリックして、 **[追加]、[接続済みサービス]、[Application Insights での監視]** の順にクリックします。

    ![AI の構成](./media/service-fabric-tutorial-monitoring-aspnet/configure-ai.png)
>[!NOTE]
>プロジェクト タイプによっては、サービスの名前を右クリックするときに、[追加]、[Application Insights Telemetry] の順にクリックする必要があります。

2. **[はじめに]** をクリックします。
3. Azure サブスクリプションの設定に使用したアカウントにサインインし、Application Insights リソースを作成したサブスクリプションを選択します。 [リソース] ボックスの *[Application Insights の既存のリソース]* でリソースを探します。 **[登録]** をクリックして Application Insights をサービスに追加します。

    ![AI の登録](./media/service-fabric-tutorial-monitoring-aspnet/register-ai.png)

4. ポップアップ表示されるダイアログ ボックスの操作が完了したら、 **[完了]** をクリックします。

> [!NOTE]
> アプリケーションの**両方**のサービスで上記の手順を実行して、アプリケーション用の Application Insights の構成を完了します。
> 受信要求と送信要求、およびサービス間の通信を確認するために、両方のサービスで同じ Application Insights リソースを使用します。

## <a name="add-the-microsoftapplicationinsightsservicefabricnative-nuget-to-the-services"></a>Microsoft.ApplicationInsights.ServiceFabric.Native NuGet をサービスに追加する

Application Insights では、シナリオによって使い分けられる Service Fabric 固有の 2 つの NuGets があります。 1 つは Service Fabric のネイティブ サービスで使用し、もう 1 つはコンテナーおよびゲストの実行可能ファイルで使用します。 今回は、Microsoft.ApplicationInsights.ServiceFabric.Native NuGet を使用し、そこから得られるサービス コンテキストの知識を活用します。 Application Insights SDK と Service Fabric 固有の NuGets の詳細については、「[Microsoft Application Insights for Service Fabric (Service Fabric での Microsoft Application Insights)](https://github.com/Microsoft/ApplicationInsights-ServiceFabric/blob/master/README.md)」をご覧ください。

次に、NuGet パッケージを設定する手順を示します。

1. ソリューション エクスプローラーの最上部にある **"Voting" ソリューション**を右クリックし、 **[Manage NuGet Packages for Solution...]\(ソリューションの NuGet パッケージの管理\)** をクリックします。
2. [NuGet - ソリューション] ウィンドウ上部のナビゲーション メニューにある **[参照]** をクリックし、検索バーの横にある **[プレリリースを含める]** チェックボックスをオンにします。
>[!NOTE]
>Microsoft.ServiceFabric.Diagnostics.Internal パッケージが事前にインストールされていない場合は、Application Insights パッケージをインストールする前に同様の方法でこのパッケージをインストールすることが必要になる場合があります

3. `Microsoft.ApplicationInsights.ServiceFabric.Native` を検索し、適切な NuGet パッケージをクリックします。
4. 右側で、アプリケーション内の 2 つのサービス ( **[VotingWeb]** と **[VotingData]** ) の横にある 2 つのチェック ボックスをオンにして、 **[インストール]** をクリックします。
    ![AI sdk Nuget](./media/service-fabric-tutorial-monitoring-aspnet/ai-sdk-nuget-new.png)
5. *[変更のプレビュー]* ダイアログ ボックスが表示されたら **[OK]** をクリックし、 *[ライセンスの同意]* に同意します。 これで、サービスへの NuGet の追加が完了します。
6. 今度は 2 つのサービスにテレメトリの初期化子を設定する必要があります。 そのためには、*VotingWeb.cs* と *VotingData.cs* を開きます。 両方に対して、次の 2 つの手順を実行します。
    1. それぞれの *\<サービス名>.cs* の上部で、既存の *using* ステートメントの下に次の 2 つの *using* ステートメントを追加します。

    ```csharp
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.ServiceFabric;
    ```

    2. 両方のファイルで、*CreateServiceInstanceListeners()* または *CreateServiceReplicaListeners()* の入れ子になった *return* ステートメント内の *ConfigureServices* > *services* の下に、宣言されている他のシングルトン サービスと一緒に以下を追加します。
    ```csharp
    .AddSingleton<ITelemetryInitializer>((serviceProvider) => FabricTelemetryInitializerExtension.CreateFabricTelemetryInitializer(serviceContext))
    ```
    これにより、お使いのテレメトリに*サービス コンテキスト*が追加され、Application Insights のテレメトリのソースをより深く理解できるようになります。 *VotingWeb.cs* の入れ子になった *return* ステートメントは、次のようになります。

    ```csharp
    return new WebHostBuilder()
        .UseKestrel()
        .ConfigureServices(
            services => services
                .AddSingleton<HttpClient>(new HttpClient())
                .AddSingleton<FabricClient>(new FabricClient())
                .AddSingleton<StatelessServiceContext>(serviceContext)
                .AddSingleton<ITelemetryInitializer>((serviceProvider) => FabricTelemetryInitializerExtension.CreateFabricTelemetryInitializer(serviceContext)))
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
                .AddSingleton<IReliableStateManager>(this.StateManager)
                .AddSingleton<ITelemetryInitializer>((serviceProvider) => FabricTelemetryInitializerExtension.CreateFabricTelemetryInitializer(serviceContext)))
        .UseContentRoot(Directory.GetCurrentDirectory())
        .UseStartup<Startup>()
        .UseApplicationInsights()
        .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
        .UseUrls(url)
        .Build();
    ```

上記のように、*VotingWeb.cs* と *VotingData.cs* の両方のファイルで `UseApplicationInsights()` メソッドが呼び出されていることを再度確認します。

>[!NOTE]
>このサンプル アプリでは、サービスの通信に http を使用します。 サービスのリモート処理 V2 を使用してアプリを開発する場合は、上記と同じ場所に次のコード行も追加する必要があります

```csharp
ConfigureServices(services => services
    ...
    .AddSingleton<ITelemetryModule>(new ServiceRemotingDependencyTrackingTelemetryModule())
    .AddSingleton<ITelemetryModule>(new ServiceRemotingRequestTrackingTelemetryModule())
)
```

この時点で、アプリケーションをデプロイする準備が整いました。 上部にある **[開始]** をクリックする (または **F5** キーを押す) と、Visual Studio でアプリケーションがビルドおよびパッケージ化され、ローカル クラスターが設定され、そのクラスターにアプリケーションがデプロイされます。

>[!NOTE]
>.NET Core SDK の最新バージョンがインストールされていないと、ビルド エラーが発生することがあります。

アプリケーションのデプロイが完了した後に [localhost:8080](localhost:8080) にアクセスすると、単一ページの投票アプリケーションのサンプルを確認できます。 サンプル データとテレメトリを作成する項目をいくつか選んで投票します。この例ではデザートにしています!

![AI 投票のサンプル](./media/service-fabric-tutorial-monitoring-aspnet/vote-sample.png)

投票を追加し終わったら、投票オプションの一部を*削除*することもできます。

## <a name="view-telemetry-and-the-app-map-in-application-insights"></a>Application Insights でテレメトリとアプリ マップを表示する

Azure Portal で、Application Insights リソースのページに移動します。

**[概要]** をクリックして、お使いのリソースのランディング ページに戻ります。 次に、上部の **[検索]** をクリックして受信トレースを確認します。 Application Insights にトレースが表示されるまで数分かかります。 何も表示されない場合は、しばらく待ってから上部の **[更新]** をクリックします。
![AI トレースの確認](./media/service-fabric-tutorial-monitoring-aspnet/ai-search.png)

*[検索]* ウィンドウを下にスクロールすると、Application Insights ですぐに利用できるすべての受信テレメトリが表示されます。 投票アプリケーションで行った操作ごとに、*VotingWeb* からの送信 PUT 要求 (PUT Votes/Put [name]) と *VotingData* からの受信 PUT 要求 (PUT VoteData/Put [name]) が発生し、その後、 表示データを最新の情報に更新するように求める GET 要求が対になって発生します。 それらは HTTP 要求であるため、ローカルホストでも HTTP に対する依存関係トレースが発生します。 1 つの投票がどのように追加されるかが分かるサンプルを次に示します。

![AI サンプル要求のトレース](./media/service-fabric-tutorial-monitoring-aspnet/sample-request.png)

トレースのひとつをクリックすると、その詳細を表示できます。 Application Insights で提供される情報には、*応答時間*や*要求 URL* など、要求に関する有用な情報があります。 また、Service Fabric 固有の NuGet を追加したため、後述の *Custom Data* セクションでは、お使いのアプリケーションに関する情報を Service Fabric クラスターのコンテキストで取得することもできます。 これにはサービス コンテキストが含まれるため、要求のソースの *PartitionID* と *ReplicaId* を確認して、アプリケーションでのエラー診断時に問題箇所を適切に特定することができます。

![AI トレースの詳細](./media/service-fabric-tutorial-monitoring-aspnet/trace-details.png)

さらに、[概要] ページの左側のメニューの *[Application map]\(アプリケーション マップ\)* をクリックするか、 **[アプリ マップ]** アイコンをクリックすると、2 つのサービスが接続されていることを示すアプリ マップが表示されます。

![AI トレースの詳細](./media/service-fabric-tutorial-monitoring-aspnet/app-map-new.png)

アプリ マップを利用すると、アプリケーションのトポロジをより深く理解できます。特に、連携して動く複数の異なるサービスを追加し始めるときには有用です。 また、これによって要求の成功率に関する基本的なデータを取得し、失敗した要求を診断して、問題が起きた可能性のある場所を把握するのに役立てることもできます。 アプリケーション マップの使用方法について詳しくは、「[Application Insights のアプリケーション マップ](../azure-monitor/app/app-map.md)」をご覧ください。

## <a name="add-custom-instrumentation-to-your-application"></a>カスタム インストルメンテーションをアプリケーションに追加する

Application Insights では多くのテレメトリをすぐに利用できますが、さらにカスタム インストルメンテーションを追加することもできます。 これは、ビジネス ニーズに基づいて行うことも、またはアプリケーションでの問題発生時に診断を改善するために行うこともできます。 Application Insights にはカスタム イベントとメトリックを取り込める API があります。詳細については[こちら](../azure-monitor/app/api-custom-events-metrics.md)をご覧ください。

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

これらの変更が完了したら、アプリケーションを **[開始]** して、最新バージョンのビルドとデプロイを行います。 アプリケーションのデプロイが完了したら、[localhost:8080](localhost:8080) にアクセスし、一部の投票オプションの追加と削除を行います。 次に、Application Insights リソースに戻って、最新の実行に関するトレースを確認します (前と同様に、トレースが Application Insights に表示されるまでに 1 ～ 2 分かかります)。 追加および削除したすべての投票の "カスタム イベント"\* が、すべての応答のテレメトリとともに表示されます。

![カスタム イベント](./media/service-fabric-tutorial-monitoring-aspnet/custom-events.png)

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。
> [!div class="checklist"]
> * Application Insights をお使いのアプリケーション用に構成する
> * 応答のテレメトリを収集してサービス間の HTTP ベース通信を追跡する
> * Application Insights でアプリ マップ機能を使用する
> * Application Insights API を使用してカスタム イベントを追加する

ASP.NET アプリケーションの監視と診断の設定が完了したところで、以下を試してみてください。

* [Service Fabric での監視と診断についてさらに詳しく知る](service-fabric-diagnostics-overview.md)
* [Application Insights を使用した Service Fabric イベントの分析](service-fabric-diagnostics-event-analysis-appinsights.md)
* Application Insights の詳細については、[Application Insights のドキュメント](https://docs.microsoft.com/azure/application-insights/)をご覧ください。
