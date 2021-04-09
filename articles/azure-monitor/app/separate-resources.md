---
title: Application Insights のデプロイを設計する方法 - 1 つまたは複数のリソース
description: 開発、テスト、および運用スタンプのテレメトリを異なるリソースに送信します。
ms.topic: conceptual
ms.date: 05/11/2020
ms.openlocfilehash: 9a60981e692a45dd3630073300b206289cfd2a30
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102424667"
---
# <a name="how-many-application-insights-resources-should-i-deploy"></a>デプロイする必要がある Application Insights リソースの数

Web アプリケーションの次のバージョンを開発する際に、新しいバージョンの [Application Insights](../../azure-monitor/app/app-insights-overview.md) テレメトリとリリース済みのバージョンのテレメトリが混在することがないように設定できます。 混乱を避けるには、分離インストルメンテーション キー (ikeys) を使用して、異なる開発段階のテレメトリを別の Application Insights リソースに送信します。 バージョンが別の段階に進むときにインストルメンテーション キーを簡単に変更できるように、構成ファイルではなくコード内に ikey を設定できます。

(システムが Azure クラウド サービスである場合は、[個別の iKey を設定する別の方法](../../azure-monitor/app/cloudservices.md)もあります。)

## <a name="about-resources-and-instrumentation-keys"></a>リソースとインストルメンテーション キーについて

Web アプリに対する Application Insights の監視を設定するときは、Microsoft Azure 内に Application Insights *リソース* を作成します。 アプリから収集されたテレメトリを表示して分析するには、Azure ポータルでこのリソースを開きます。 リソースは、*インストルメンテーション キー* (iKey) によって識別されます。 アプリを監視するために Application Insights パッケージをインストールするとき、インストルメンテーション キーを構成して、テレメトリの送信先を認識できるようにします。

各 Application Insights リソースには、すぐに使用できるメトリックが付属しています。 完全に分離されたコンポーネントが同じ Application Insights リソースに報告している場合は、これらのメトリックがダッシュボードまたはアラートにとって意味がない場合があります。

### <a name="when-to-use-a-single-application-insights-resource"></a>単一の Application Insights リソースを使用するケース

-   一緒にデプロイされるアプリケーション コンポーネントの場合。 通常は、1 つのチームによって開発され、同じ DevOps/ITOps ユーザーのセットによって管理されます。
-   それらすべてにわたって既定で応答時間、ダッシュボードの失敗率などの主要業績評価指標 (KPI) を集計することが理にかなっている場合 (メトリックス エクスプローラー エクスペリエンスでロール名でセグメント化することを選択できます)。
-   アプリケーション コンポーネント間で異なる方法で Azure ロールベースのアクセス制御 (Azure RBAC) を管理する必要がない場合。
-   コンポーネント間で異なるメトリック アラート条件が必要ない場合。
-   コンポーネント間で異なる方法で連続エクスポートを管理する必要がない場合。
-   コンポーネント間で異なる方法で請求先またはクォータを管理する必要がない場合。
-   API キーがすべてのコンポーネントのデータに対して同じアクセス権を持っていてもかまわない場合。 これらすべてのニーズに対応するには、10 個の API キーで十分です。
-   すべてのロールで同じスマート検出と作業項目の統合設定を持つことができる場合。

> [!NOTE]
> 複数の Application Insights リソースを統合する場合、統合後の新しい Application Insights リソースに既存のアプリケーション コンポーネントをさし向けることができます。 古いリソースに保管されている利用統計情報は、新しいリソースに転送されません。そのため、事業継続性のために、新しいリソースに十分な利用統計情報がある場合、古いリソースのみを削除してください。

### <a name="other-things-to-keep-in-mind"></a>その他の注意点

-   [Cloud_RoleName](./app-map.md?tabs=net#set-or-override-cloud-role-name) 属性に意味のある値が設定されるようにするには、カスタム コードを追加することが必要になる場合があります。 この属性に対して意味のある値が設定されていないと、どのポータル エクスペリエンスも機能 "*しません*"。
- Service Fabric アプリケーションと従来のクラウド サービスについては、SDK によって Azure ロール環境から自動的に読み取られ、これらが設定されます。 他のすべての種類のアプリでは、これを明示的に設定する必要があります。
-   Live Metrics エクスペリエンスでは、ロール名による分割はサポートされていません。

## <a name="dynamic-instrumentation-key"></a><a name="dynamic-ikey"></a> 動的なインストルメンテーション キー

運用の段階が変わるときのコードの ikey の変更を容易にするには、値をハードコードしたり静的な値は使用したりせずに、コードでそのキーが動的に参照されるようにしてください。

ASP.NET サービスの global.aspx.cs など、初期化メソッドでキーを設定します。

```csharp
protected void Application_Start()
{
  Microsoft.ApplicationInsights.Extensibility.
    TelemetryConfiguration.Active.InstrumentationKey = 
      // - for example -
      WebConfigurationManager.AppSettings["ikey"];
  ...
```

この例では、さまざまなリソースの ikeys が Web 構成ファイルのさまざまなバージョンに配置されます。 リリース スクリプトの一部として行うことができる Web 構成ファイルのスワップは、ターゲット リソースをスワップします。

### <a name="web-pages"></a>Web ページ
iKey は、アプリの Web ページや、[クイックスタート ペインから取得したスクリプト](../../azure-monitor/app/javascript.md)内でも使用されます。 スクリプトにそのままコーディングするのではなく、サーバーの状態からこれを生成します。 たとえば、ASP.NET アプリで以下の操作を行います。

```javascript
<script type="text/javascript">
// Standard Application Insights web page script:
var appInsights = window.appInsights || function(config){ ...
// Modify this part:
}({instrumentationKey:  
  // Generate from server property:
  "@Microsoft.ApplicationInsights.Extensibility.
     TelemetryConfiguration.Active.InstrumentationKey"
  }
 )
//...
```

## <a name="create-additional-application-insights-resources"></a>追加の Application Insights リソースを作成する

Application Insights リソースを作成するには、[リソース作成ガイド](./create-new-resource.md)に従います。

### <a name="getting-the-instrumentation-key"></a>インストルメンテーション キーの取得
インストルメンテーション キーは作成したリソースを識別します。

アプリがデータを送信する宛先のすべてのリソースのインストルメンテーション キーが必要です。

## <a name="filter-on-build-number"></a>ビルド番号でのフィルター処理
新しいバージョンのアプリを発行するときは、異なるビルドのテレメトリを区別する必要があります。

アプリケーション バージョン プロパティを設定することで、[検索](../../azure-monitor/app/diagnostic-search.md)と[メトリックス エクスプローラー](../../azure-monitor/essentials/metrics-charts.md)の結果をフィルター処理できます。

アプリケーション バージョン プロパティを設定するには複数の方法があります。

* 直接設定します。

    `telemetryClient.Context.Component.Version = typeof(MyProject.MyClass).Assembly.GetName().Version;`
* その行を [テレメトリ初期化子](../../azure-monitor/app/api-custom-events-metrics.md#defaults) にラップして、すべての TelemetryClient インスタンスが一貫して設定されるようにします。
* [ASP.NET] `BuildInfo.config`でバージョンを設定します。 Web モジュールは BuildLabel ノードからバージョンを取得します。 このファイルをプロジェクトに追加し、ソリューション エクスプローラーで [常にコピーする] プロパティを設定します。

    ```XML
    <?xml version="1.0" encoding="utf-8"?>
    <DeploymentEvent xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/VisualStudio/DeploymentEvent/2013/06">
      <ProjectName>AppVersionExpt</ProjectName>
      <Build type="MSBuild">
        <MSBuild>
          <BuildLabel kind="label">1.0.0.2</BuildLabel>
        </MSBuild>
      </Build>
    </DeploymentEvent>

    ```
* [ASP.NET] MSBuild で自動的に BuildInfo.config を生成します。 そのためには、`.csproj` ファイルに数行を追加します。

    ```XML
    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup>
    ```

    これにより、 *プロジェクト名*.BuildInfo.config という名前のファイルが生成されます。これは発行プロセスで BuildInfo.config という名前に変更されます。

    Visual Studio でビルドすると、ビルド ラベルにはプレースホルダー (AutoGen_...) が含まれます。 一方、MSBuild でビルドすると、適切なバージョン番号が設定されます。

    MSBuild がバージョン番号を生成できるようにするには、AssemblyReference.cs で `1.0.*` のようなバージョンを設定します。

## <a name="version-and-release-tracking"></a>バージョンおよびリリースの追跡
アプリケーションのバージョンを追跡するには、Microsoft Build Engine プロセスによって `buildinfo.config` が生成されたことを確認してください。 `.csproj` ファイルに以下を追加します。  

```XML
<PropertyGroup>
  <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>
  <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
</PropertyGroup>
```

ビルド情報がある場合、Application Insights Web モジュールは、 **アプリケーションのバージョン** をプロパティとしてテレメトリのすべての項目に自動的に追加します。 これにより、[診断の検索](../../azure-monitor/app/diagnostic-search.md)を実行するとき、または[メトリックを調べる](../../azure-monitor/essentials/metrics-charts.md)ときに、バージョンによってフィルター処理できます。

ただし、Visual Studio の開発者向けのビルドではなく、Microsoft Build Engine でのみビルド バージョン番号が生成されることに注意してください。

### <a name="release-annotations"></a>リリース注釈
Azure DevOps を使用する場合は、新しいバージョンをリリースするたびに、グラフに[注釈マーカーを追加](../../azure-monitor/app/annotations.md)できます。 

## <a name="next-steps"></a>次のステップ

* [複数のロール用の共有リソース](../../azure-monitor/app/app-map.md)
* [A |B のバリアントを区別するためのテレメトリ初期化子を作成する](../../azure-monitor/app/api-filtering-sampling.md#add-properties)
