---
title: "Azure Application Insights での開発、テスト、および運用の監視 | Microsoft Docs"
description: "開発のさまざまな段階で、アプリケーションのパフォーマンスと使用状況を監視します。"
services: application-insights
documentationcenter: 
author: alancameronwills
manager: carmonm
ms.assetid: 578e30f0-31ed-4f39-baa8-01b4c2f310c9
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 43fb1e764c929be14d42c3d214b051aeb5367d77
ms.lasthandoff: 03/15/2017


---
# <a name="separating-application-insights-resources"></a>Application Insights リソースの分離
さまざまなコンポーネントとアプリケーションからのテレメトリは、Application Insights の異なるリソースに送信するべきでしょうか、それとも&1; つのリソースにまとめるべきでしょうか。 この記事では、そのベスト プラクティスと必要な手法について説明します。

まず、質問を理解しておきましょう。 アプリケーションから受け取ったデータは Microsoft Azure *リソース*に格納され、Application Insights によって処理されます。 各リソースは、 *インストルメンテーション キー* (iKey) によって識別されます。 アプリ内で、Application Insights SDK にキーが提供され、これにより、収集したデータを適切なリソースに送信できます。 キーは、コード内または ApplicationInsights.config で指定できます。 SDK 内のキーを変更することで、データをさまざまなリソースに送信できます。 

単純なケースでは、Application Insights にアプリケーションを登録するときに、Application Insights の新しいリソースも作成します。 これは、Visual Studio の *[Application Insights の構成]* ダイアログまたは *[Application Insights の追加]* ダイアログで行います。

大規模な Web サイトでは、複数のサーバー インスタンスにデプロイする場合があります。

さらに複雑なシナリオでは、システムは複数のコンポーネントで構成されます (Web サイトやバックエンド プロセッサなど)。 

## <a name="when-to-use-separate-ikeys"></a>異なる iKey を使用する場合
一般的なガイドラインを次に示します。

* 他のコンポーネントとは独立してスケールアップ/スケールダウンできるサーバー インスタンスのセットで実行する個別にデプロイ可能なアプリケーション ユニットがある場合は、通常、それを&1; つのリソースにマップします。つまり、1 つのインストルメンテーション キー (iKey) を使用します。
* これに対し、次のような場合には異なる iKey を使用します。
  * 異なるコンポーネントからの異なるメトリックを読みやすくする。
  * 量の少ないテレメトリと量の多いテレメトリを分離し、あるストリームのスロットル、クォータ、サンプリングが他のストリームに影響を与えないようにする。
  * アラート、エクスポート、作業項目の構成を分離する。
  * テレメトリ クォータ、スロットル、Web テスト数などの [制限](app-insights-pricing.md#limits-summary) を分散させる。
  * 開発およびテスト中のコードが、運用スタンプとは異なる iKey を送信する必要がある。  

Application Insights ポータルのエクスペリエンスの多くは、これらのガイドラインを考慮して設計されています。 たとえば、サーバーは、1 つの論理コンポーネントに関するテレメトリは複数のサーバー インスタンスからのものであると想定して、サーバー インスタンスのセグメントを表示します。

## <a name="single-ikey"></a>単一の iKey
複数のコンポーネントから&1; つの iKey にテレメトリを送信する場合:

* コンポーネントの ID でセグメント分割およびフィルター処理できるプロパティをすべてのテレメトリに追加します。 ロール ID はサーバー ロール インスタンスからテレメトリに自動的に追加されますが、それ以外の場合は、 [テレメトリ初期化子](app-insights-api-filtering-sampling.md#add-properties) を使用してプロパティを追加できます。
* 同時に複数の異なるコンポーネントで Application Insights SDK を更新します。 1 つの iKey のテレメトリは、同じバージョンの SDK から生成される必要があります。

## <a name="separate-ikeys"></a>複数の iKey
異なるアプリケーション コンポーネントごとに複数の iKey を使用する場合:

* 異なるアプリケーション コンポーネントから送られてくる論理アプリケーションの重要なテレメトリをまとめて表示するために、 [ダッシュボード](app-insights-dashboards.md) を作成します。 ダッシュボードは共有できるので、1 つの論理システム ビューを異なるチームで使用できます。
* [リソース グループ](app-insights-resources-roles-access-control.md) をチーム レベルで整理します。 アクセス許可はリソース グループによって割り当てられ、これにはアラートを設定するアクセス許可が含まれます。 
* [Azure Resource Manager テンプレートと PowerShell](app-insights-powershell.md) を使用して、アラート ルールや Web テストなどのアーティファクトを管理します。

## <a name="separate-ikeys-for-devtest-and-production"></a>開発/テスト用と運用用に異なる iKey
アプリ リリース時のキーの自動変更を容易にするには、ApplicationInsights.config ではなくコードで iKey を設定します。

(システムが Azure クラウド サービスである場合は、[個別の iKey を設定する別の方法](app-insights-cloudservices.md)もあります。)

### <a name="dynamic-ikey"></a> 動的なインストルメンテーション キー
ASP.NET サービスの global.aspx.cs など、初期化メソッドでキーを設定します。

*C#*

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.AppSettings["ikey"];
      ...

この例では、さまざまなリソースの ikeys が Web 構成ファイルのさまざまなバージョンに配置されます。 リリース スクリプトの一部として行うことができる Web 構成ファイルのスワップは、ターゲット リソースをスワップします。

### <a name="web-pages"></a>Web ページ
iKey は、アプリの Web ページや、 [クイック スタート ブレードから取得したスクリプト](app-insights-javascript.md)内でも使用されます。 スクリプトにそのままコーディングするのではなく、サーバーの状態からこれを生成します。 たとえば、ASP.NET アプリで以下の操作を行います。

*Razor の JavaScript*

    <script type="text/javascript">
    // Standard Application Insights web page script:
    var appInsights = window.appInsights || function(config){ ...
    // Modify this part:
    }({instrumentationKey:  
      // Generate from server property:
      "@Microsoft.ApplicationInsights.Extensibility.
         TelemetryConfiguration.Active.InstrumentationKey"
    }) // ...


## <a name="creating-an-additional-application-insights-resource"></a>その他の Application Insights リソースの作成
異なるアプリケーション コンポーネントのテレメトリまたは同じコンポーネントの異なるスタンプ (開発/テスト/運用) のテレメトリを分ける場合は、新しい Application Insights リソースを作成する必要あります。

[portal.azure.com](https://portal.azure.com)で、Application Insights リソースを追加します。

![[新規]、[Application Insights] の順にクリックする](./media/app-insights-separate-resources/01-new.png)

* **アプリケーションの種類** に応じて、概要ブレードに表示されるものと [メトリック エクスプローラー](app-insights-metrics-explorer.md)で使用できるプロパティが決まります。 自分のアプリの種類が表示されない場合、Web ページの Web の種類を&1; つ選択します。
* **リソース グループ** は、 [アクセス制御](app-insights-resources-roles-access-control.md)などのプロパティを管理するのに便利です。 開発、テスト、および実稼働用に別々のリソース グループを使用できます。
* **サブスクリプション** は、Azure での支払いアカウントです。
* **場所** は、データの保存場所です。 現在、これは変更できません。 
* **ダッシュボードへの追加** は、Azure ホーム ページ上のリソースに対するクイック アクセス タイルを入れます。 

リソースの作成には数秒かかります。 完了したら、アラートが表示されます。

([PowerShell スクリプト](app-insights-powershell-script-create-resource.md) を作成して、リソースを自動で作成できます。)

## <a name="getting-the-instrumentation-key"></a>インストルメンテーション キーの取得
インストルメンテーション キーは作成したリソースを識別します。 

![Essentials、インストルメンテーション キーの順にクリックし、Ctrl キーを押しながら C キーを押します。](./media/app-insights-separate-resources/02-props.png)

アプリがデータを送信する宛先のすべてのリソースのインストルメンテーション キーが必要です。


