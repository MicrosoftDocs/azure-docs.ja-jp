---
title: Azure Application Insights のアプリケーション マップ | Microsoft Docs
description: アプリケーション マップを使用した複雑なアプリケーション トポロジの監視
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 3bf37fe9-70d7-4229-98d6-4f624d256c36
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/15/2019
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: 73cf6fd1c20f2e4208d1f7c28a756f28a2fad839
ms.sourcegitcommit: af58483a9c574a10edc546f2737939a93af87b73
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2019
ms.locfileid: "68302571"
---
# <a name="application-map-triage-distributed-applications"></a>アプリケーション マップ:分散アプリケーションのトリアージ

アプリケーション マップを使用すると、分散アプリケーションのすべてのコンポーネントを対象にパフォーマンスのボトルネックや障害のホットスポットを特定できます。 マップ上の各ノードは、アプリケーション コンポーネントまたはその依存関係を表すと共に、正常性の KPI やアラートの状態を保持しています。 任意のコンポーネントをクリックして、さらに詳しい診断結果 (Application Insights イベントなど) にアクセスすることができます。 アプリで Azure サービスを使用している場合は、Azure Diagnostics (SQL Database アドバイザーのアドバイス情報など) をクリックすることもできます。

## <a name="what-is-a-component"></a>コンポーネントとは

コンポーネントは、分散/マイクロサービス アプリケーションの、個別にデプロイできる部分です。 開発者と運用チームには、これらのアプリケーション コンポーネントによって生成されたテレメトリに対して、コード レベルの可視性またはアクセスがあります。 

* コンポーネントは、チームや組織が (コードやテレメトリに) アクセスできない可能性がある SQL や EventHub などの "観察される" 外部依存関係とは異なります。
* コンポーネントは、任意の数のサーバー/ロール/コンテナーのインスタンス上で実行されます。
* コンポーネントは、個別の Application Insights インストルメンテーション キーである場合も (サブスクリプションが異なる場合も)、単一の Application Insights インストルメンテーション キーにレポートするさまざまなロールである場合もあります。 プレビュー マップのエクスペリエンスには、各コンポーネントがその設定方法に関係なく表示されます。

## <a name="composite-application-map"></a>複合アプリケーション マップ

関連するアプリケーション コンポーネントの階層を横断的に見ながら完全なアプリケーション トポロジを表示できます。 コンポーネントとしては、さまざまな Application Insights リソースや、単一のリソースに含まれる各種ロールが該当します。 アプリ マップでは、Application Insights SDK がインストールされているサーバー間での HTTP 依存関係呼び出しに従ってコンポーネントを検索します。 

このエクスペリエンスでは、最初にコンポーネントが順次検出されます。 アプリケーション マップを初めて読み込むとき、一連のクエリがトリガーされて、対象コンポーネントに関連したコンポーネントが検出されます。 左上隅のボタンには、対象アプリケーション内のコンポーネント数が表示され、その数は、コンポーネントが検出されるにつれて更新されます。 

[マップ コンポーネントの更新] をクリックすると、その時点で検出されたすべてのコンポーネントでマップが更新されます。 アプリケーションの複雑さによっては、読み込みに数分かかることがあります。

すべてのコンポーネントが 1 つの Application Insights リソース内のロールである場合、この検出ステップは不要です。 そのようなアプリケーションでは、そのすべてのコンポーネントが初回読み込みで認識されます。

![アプリケーション マップのスクリーンショット](media/app-map/app-map-001.png)

このエクスペリエンスが目指す主な目的の 1 つは、何百というコンポーネントを含む複雑なトポロジを視覚化することです。

いずれかのコンポーネントをクリックすると、関連する分析情報を表示したり、そのコンポーネントについてのパフォーマンスと障害のトリアージ機能にアクセスしたりすることができます。

![ポップアップ](media/app-map/application-map-002.png)

### <a name="investigate-failures"></a>エラーを調査する

**[investigate failures] (エラーの調査)** を選択すると、[failures] ウィンドウが開きます。

![[investigate failures] (エラーの調査) ボタンのスクリーンショット](media/app-map/investigate-failures.png)

![[failures] エクスペリエンスのスクリーンショット](media/app-map/failures.png)

### <a name="investigate-performance"></a>パフォーマンスを調査する

パフォーマンスの問題のトラブルシューティングを行うには、**パフォーマンスの調査** を選択します。

![[investigate performance] (パフォーマンスの調査) ボタンのスクリーンショット](media/app-map/investigate-performance.png)

![[performance] (パフォーマンス) エクスペリエンスのスクリーンショット](media/app-map/performance.png)

### <a name="go-to-details"></a>詳細の表示

呼び出し履歴レベルまでのビューを提供できるエンドツーエンドのトランザクション エクスペリエンスの詳細を表示するには、 **[詳細に移動]** を選択します。

![[go-to-details] (詳細の表示) ボタンのスクリーンショット](media/app-map/go-to-details.png)

![[end-to-end transaction details] (エンドツーエンドのトランザクションの詳細) のスクリーンショット](media/app-map/end-to-end-transaction.png)

### <a name="view-in-analytics"></a>Analytics での表示

さらにアプリケーション データのクエリと調査を行うには、**Analytics で表示** をクリックします。

![[view in analytics] (Analytics で表示) ボタンのスクリーンショット](media/app-map/view-in-analytics.png)

![Analytics エクスペリエンスのスクリーンショット](media/app-map/analytics.png)

### <a name="alerts"></a>アラート

アクティブなアラートと、アラートがトリガーされる原因になっている元のルールを表示するには、**アラート** を選択します。

![[alerts] (アラート) ボタンのスクリーンショット](media/app-map/alerts.png)

![Analytics エクスペリエンスのスクリーンショット](media/app-map/alerts-view.png)

## <a name="set-cloud-role-name"></a>クラウド ロール名の設定

アプリケーション マップでは、**クラウド ロール名**プロパティを使用して、マップ上のコンポーネントが識別されます。 Application Insights SDK では、コンポーネントで生成されたテレメトリにクラウド ロール名プロパティが自動的に追加されます。 たとえば、SDK では、Web サイト名またはサービス ロール名がクラウド ロール名プロパティに追加されます。 ただし、既定値をオーバーライドする必要のある場合があります。 クラウド ロール名をオーバーライドし、アプリケーション マップ上に表示される内容を変更するには、次のようにします。

### <a name="netnet-core"></a>.NET/.NET Core

**以下のようにカスタム TelemetryInitializer を作成します。**

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace CustomInitializer.Telemetry
{
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
            {
                //set custom role name here
                telemetry.Context.Cloud.RoleName = "Custom RoleName";
                telemetry.Context.Cloud.RoleInstance = "Custom RoleInstance";
            }
        }
    }
}
```

**ASP.NET アプリ: アクティブな TelemetryConfiguration に初期化子を読み込む**

ApplicationInsights.config:

```xml
    <ApplicationInsights>
      <TelemetryInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="CustomInitializer.Telemetry.MyTelemetryInitializer, CustomInitializer"/>
        ...
      </TelemetryInitializers>
    </ApplicationInsights>
```

ASP.NET Web アプリのもう 1 つの方法は、Global.aspx.cs などのコード内で初期化子をインスタンス化することです。

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
    }
```

> [!NOTE]
> `ApplicationInsights.config` または `TelemetryConfiguration.Active` を使用して初期化子を追加することは、ASP.NET Core アプリケーションでは無効です。 

**ASP.NET Core アプリ: アクティブな TelemetryConfiguration に初期化子を読み込む**

[ASP.NET Core](asp-net-core.md#adding-telemetryinitializers) アプリケーションの場合、新しい `TelemetryInitializer` を追加するには、次に示すように Dependency Injection コンテナーに追加します。 これは `Startup.cs` クラスの `ConfigureServices` メソッドで行われます。

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```

### <a name="nodejs"></a>Node.js

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY').start();
appInsights.defaultClient.context.tags["ai.cloud.role"] = "your role name";
appInsights.defaultClient.context.tags["ai.cloud.roleInstance"] = "your role instance";
```

### <a name="alternate-method-for-nodejs"></a>Node.js の別の方法

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY').start();

appInsights.defaultClient.addTelemetryProcessor(envelope => {
    envelope.tags["ai.cloud.role"] = "your role name";
    envelope.tags["ai.cloud.roleInstance"] = "your role instance"
});
```

### <a name="java"></a>Java

Application Insights Spring Boot スターターで Spring Boot を使用する場合、必要な変更は application.properties ファイルにアプリケーションのカスタム名を設定することだけです。

`spring.application.name=<name-of-app>`

Spring Boot スターターにより、クラウド ロール名が、ユーザーが spring.application.name プロパティに入力した値に自動的に割り当てられます。

Java の相関関係と、SpringBoot 以外のアプリケーションにクラウド ロール名を構成する方法の詳細については、この相関関係に関する[セクション](https://docs.microsoft.com/azure/application-insights/application-insights-correlation#role-name)を調べてください。

### <a name="clientbrowser-side-javascript"></a>クライアント/ブラウザー側の JavaScript

```javascript
appInsights.queue.push(() => {
appInsights.addTelemetryInitializer((envelope) => {
  envelope.tags["ai.cloud.role"] = "your role name";
  envelope.tags["ai.cloud.roleInstance"] = "your role instance";
});
});
```

### <a name="understanding-cloud-role-name-within-the-context-of-the-application-map"></a>アプリケーション マップのコンテキスト内でのクラウド ロール名の理解

**クラウド ロール名**をどのように考えるかに関する限り、複数のクラウド ロール名が存在するアプリケーション マップを調べると役に立つ可能性があります。

![アプリケーション マップのスクリーンショット](media/app-map/cloud-rolename.png)

上記のアプリケーション マップでは、緑色の囲みの中の名前が、それぞれこの特定の分散アプリケーションの異なる側面のクラウド ロール名の値になります。 したがって、このアプリでは、ロールは `Authentication`、`acmefrontend`、`Inventory Management`、`Payment Processing Worker Role` で構成されます。 

このアプリの場合、クラウド ロール名のそれぞれが、一意の Application Insights リソースと各自のインストルメンテーション キーも表しています。 このアプリケーションの所有者は、これら 4 つの異なる Application Insights リソースにアクセスできるため、アプリケーション マップによって基になる関係を 1 つのマップにまとめることができます。

[公式な定義](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/39a5ef23d834777eefdd72149de705a016eb06b0/Schema/PublicSchema/ContextTagKeys.bond#L93):

```
   [Description("Name of the role the application is a part of. Maps directly to the role name in azure.")]
    [MaxStringLength("256")]
    705: string      CloudRole = "ai.cloud.role";
    
    [Description("Name of the instance where the application is running. Computer name for on-premises, instance name for Azure.")]
    [MaxStringLength("256")]
    715: string      CloudRoleInstance = "ai.cloud.roleInstance";
```

または、**クラウド ロール名**によって Web フロント エンドのどこかに問題があることが通知されたが、Web フロント エンドは複数の負荷分散サーバーで実行されているため、Kusto クエリによってレイヤーを深くドリルインして、問題がすべての Web フロント エンド サーバー/インスタンスに影響するのか、1 つだけに影響するのかを知ることが非常に重要なシナリオでは、**クラウド ロール インスタンス**が有用である可能性があります。

コンテナー化された環境でアプリが実行されている場合は、個々のサーバーを知るだけでは特定の問題を見つけられない可能性があり、クラウド ロール インスタンスの値をオーバーライドした方がよいシナリオです。

クラウド ロール名プロパティをテレメトリ初期化子でオーバーライドする方法の詳細については、[ITelemetryInitializer プロパティの追加](api-filtering-sampling.md#add-properties-itelemetryinitializer)に関するページを参照してください。

## <a name="troubleshooting"></a>トラブルシューティング

アプリケーション マップを期待どおりに動作させることで問題が発生している場合は、次の手順を試してください。

### <a name="general"></a>全般

1. 正式にサポートされている SDK を使用していることを確認します。 サポートされていない/コミュニティ SDK は相関関係をサポートしていない場合があります。

    サポートされている SDK の一覧については、この[記事](https://docs.microsoft.com/azure/application-insights/app-insights-platforms)をご覧ください。

2. すべてのコンポーネントを最新の SDK バージョンにアップグレードします。

3. C# で Azure Functions を使用している場合は、[Functions V2](https://docs.microsoft.com/azure/azure-functions/functions-versions) にアップグレードします。

4. [クラウド ロール名](#set-cloud-role-name)が正しく構成されていることを確認します。

5. 依存関係が欠落している場合は、[自動収集された依存関係](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies)の一覧にあることを確認します。 ない場合でも、[TrackDependency 呼び出し](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency)を使えば手動で追跡することができます。

### <a name="too-many-nodes-on-the-map"></a>マップ上のノードが多すぎる

アプリケーション マップでは、要求テレメトリ内に存在する一意のクラウド ロール名ごとにアプリケーション ノードが作成され、依存関係テレメトリ内のタイプ、ターゲット、およびクラウド ロール名の一意の組み合わせごとに依存関係ノードが作成されます。 テレメトリ内のノード数が 10,000 を超える場合は、アプリケーション マップですべてのノードとリンクをフェッチできないため、マップが不完全になります。 この場合、マップを表示したときに警告メッセージが表示されます。

また、アプリケーション マップでサポートされる、個別にレンダリングされるグループ化解除ノードは、一度に 1000 個までです。 アプリケーション マップでは、視覚化の複雑さを低減するために、種類と呼び出し元が同じ依存関係がグループ化されます。ただし、テレメトリの一意のクラウド ロール名が多すぎる場合や、依存関係の種類が多すぎる場合は、そのグループ化でも不十分となり、マップでレンダリングできなくなります。

これを解決するには、クラウド ロール名、依存関係の種類、および依存関係のターゲットのフィールドが正しく設定されるように、インストルメンテーションを変更する必要があります。

* 依存関係のターゲットは、依存関係の論理名を表す必要があります。 多くの場合、これは依存関係のサーバーまたはリソース名と等しくなります。 たとえば、HTTP の依存関係の場合は、ホスト名に設定にされます。 要求ごとに変化する一意の ID またはパラメーターを含めてはなりません。

* 依存関係の種類は、依存関係の論理型を表す必要があります。 たとえば、一般的な依存関係の種類には、HTTP、SQL、Azure Blob などがあります。 一意の ID は含めないでください。

* クラウド ロール名の目的については、[上記のセクション](https://docs.microsoft.com/azure/azure-monitor/app/app-map#set-cloud-role-name)を参照してください。

## <a name="portal-feedback"></a>ポータルのフィードバック

ご意見やご感想は、フィードバック オプションからお寄せください。

![MapLink-1 image](./media/app-map/14-updated.png)

## <a name="next-steps"></a>次の手順

* [関連付けについて](https://docs.microsoft.com/azure/application-insights/application-insights-correlation)
