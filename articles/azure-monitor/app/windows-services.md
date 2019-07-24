---
title: Windows サーバーと worker ロールのための Azure Application Insights | Microsoft Docs
description: Application Insights SDK を ASP.NET アプリケーションに手動で追加して、使用状況、可用性、およびパフォーマンスを分析します。
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 106ba99b-b57a-43b8-8866-e02f626c8190
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: mbullwin
ms.openlocfilehash: 85764c0ee5b8ed117fb191657d54abe5bd10a703
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/09/2019
ms.locfileid: "54116553"
---
# <a name="manually-configure-application-insights-for-net-applications"></a>Application Insights を .NET アプリケーション用に手動で構成する

さまざまなアプリケーションやアプリケーション ロール、コンポーネント、またはマイクロサービスを監視するように、[Application Insights](../../azure-monitor/app/app-insights-overview.md) を構成できます。 Web アプリとサービス用に、Visual Studio では[ワンステップ構成](../../azure-monitor/app/asp-net.md)が用意されています。 バックエンド サーバー ロール、デスクトップ アプリケーションなど、他の種類の .NET アプリケーションの場合は、Application Insights を手動で構成できます。

![Example performance monitoring charts](./media/windows-services/10-perf.png)

#### <a name="before-you-start"></a>開始する前に

必要なもの:

* [Microsoft Azure](https://azure.com) サブスクリプション。 チームまたは組織で Azure サブスクリプションを取得している場合、所有者は [Microsoft アカウント](https://live.com)を使用してあなたを追加できます。
* Visual Studio 2013 以降

## <a name="add"></a>1.Application Insights リソースを選択する

"リソース" は、データが収集され、Azure Portal で表示される場所です。 新しく作成するか、既存のものを共有するかを決定する必要があります。

### <a name="part-of-a-larger-app-use-existing-resource"></a>大規模なアプリの一部: 既存のリソースを使用

Web アプリケーションに、フロントエンド Web アプリと 1 つ以上のバックエンド サービスなど、複数のコンポーネントがある場合は、すべてのコンポーネントから同じリソースにテレメトリを送信する必要があります。 そうすることで、それらを 1 つのアプリケーション マップに表示し、1 つのコンポーネントから別のコンポーネントへの要求をトレースできるようになります。

そのため、このアプリの別のコンポーネントを既に監視している場合は、同じリソースを使用します。

[Azure Portal](https://portal.azure.com/) でリソースを開きます。 

### <a name="self-contained-app-create-a-new-resource"></a>自己完結型アプリ: 新しいリソースを作成

新しいアプリが他のアプリケーションと関連していない場合は、独自のリソースが必要です。

[Azure ポータル](https://portal.azure.com/)にサインインし、Application Insights の新しいリソースを作成します。 アプリケーションの種類として ASP.NET を選択します。

![[新規]、[Application Insights] の順にクリックする](./media/windows-services/01-new-asp.png)

アプリケーションの種類を選択すると、[リソース] ブレードの既定のコンテンツが設定されます。

## <a name="2-copy-the-instrumentation-key"></a>2.インストルメンテーション キーのコピー
キーはリソースを識別します。 データをリソースに送信するために、すぐ後で SDK にインストールします。

![[プロパティ] をクリックし、キーを選択して、Ctrl キーを押しながら C キーを押す](./media/windows-services/02-props-asp.png)

## <a name="sdk"></a>3.アプリケーションに Application Insights パッケージをインストールする
Application Insights パッケージのインストールと構成は、作業中のプラットフォームによって異なります。 

1. Visual Studio でプロジェクトを右クリックし、**[NuGet パッケージの管理]** を選択します。
   
    ![プロジェクトを右クリックし、[Nuget パッケージの管理] を選択する](./media/windows-services/03-nuget.png)
2. Windows サーバー アプリ用の Application Insights パッケージ (Microsoft.ApplicationInsights.WindowsServer) をインストールします。
   
    ![Search for "Application Insights"](./media/windows-services/04-ai-nuget.png)
   
    *どのバージョンか*

    最新の機能を試す場合は、**[プレリリースを含める]** をオンにします。 関連するドキュメントまたはブログに、プレリリース バージョンが必要かどうかが記載されています。
    
    *他のパッケージを使用することができますか。*
   
    はい。 独自のテレメトリを送信するために API だけを使用する場合は、[Microsoft.ApplicationInsights] を選択してください。 Windows Server パッケージには、API に加え、他にも多くのパッケージ (パフォーマンス カウンター コレクションや依存関係の監視など) が含まれます。 

### <a name="to-upgrade-to-future-package-versions"></a>新しいバージョンのパッケージにアップグレードするには
SDK の新しいバージョンは不定期でリリースされます。

[パッケージの新しいリリース](https://github.com/Microsoft/ApplicationInsights-dotnet-server/releases/)にアップグレードするには、NuGet パッケージ マネージャーをもう一度開き、インストールされているパッケージに対してフィルターを実行します。 **[Microsoft.ApplicationInsights.Web]** を選択し、**[アップグレード]** を選択します。

ApplicationInsights.config をカスタマイズしている場合は、アップグレードする前にコピーを保存しておき、後から新しいバージョンに変更をマージします。

## <a name="4-send-telemetry"></a>4.テレメトリを送信する
**API パッケージのみをインストールした場合:**

* コードでインストルメンテーション キーを設定します (たとえば、 `main()`内)。 
  
    `TelemetryConfiguration.Active.InstrumentationKey = "` *自分のキー* `";` 
* [API を使用して独自のテレメトリを記述](../../azure-monitor/app/api-custom-events-metrics.md#ikey)します。

**他の Application Insights パッケージをインストールしている場合** 、必要に応じて .config ファイルを使用してインストルメンテーション キーを設定します。

* (NuGet のインストールによって追加された) ApplicationInsights.config を編集します。 次のコードを終了タグの直前に挿入します。
  
    `<InstrumentationKey>`*コピーしたインストルメンテーション キー* `</InstrumentationKey>`
* ソリューション エクスプローラーで ApplicationInsights.config のプロパティが **[ビルド アクション] = [コンテンツ]、[出力ディレクトリにコピー] = [コピー]** に設定されていることを確認します。

[複数のビルド構成でキーを切り替える](../../azure-monitor/app/separate-resources.md)場合は、コードでインストルメンテーション キーを設定すると便利です。 コードでキーを設定する場合は、`.config` ファイルで設定する必要がありません。

## <a name="run"></a> プロジェクトの実行
**F5** キーを使用してアプリケーションを実行します。さまざまなページを開いて、テレメトリを生成してみます。

Visual Studio で、送信されたイベント数が表示されます。

![Event count in Visual Studio](./media/windows-services/appinsights-09eventcount.png)

## <a name="monitor"></a> 利用統計情報を表示する
[Azure ポータル](https://portal.azure.com/) に戻り、Application Insights のリソースを参照します。

概要グラフでデータを探します。 最初、1 つまたは 2 つのポイントだけが表示されます。 例: 

![クリックしてより多くのデータを表示する](./media/windows-services/12-first-perf.png)

任意のグラフをクリックして、より詳細なメトリックを表示します。 [メトリックの詳細についてはこちらをご覧ください。](../../azure-monitor/app/web-monitor-performance.md)

### <a name="no-data"></a>データが表示されない場合
* アプリケーションを使用して、テレメトリがいくつか生成されるようにさまざまなページを開きます。
* [[検索]](../../azure-monitor/app/diagnostic-search.md) タイルを開き、個々のイベントを表示します。 メトリック パイプラインを経由すると、イベントの取得に少し時間がかかる場合があります。
* 数秒待機してから **[最新の情報に更新]** をクリックします。 グラフは周期的に自動で更新されますが、データの表示を待機している場合、手動で更新することもできます。
* [トラブルシューティング](../../azure-monitor/app/troubleshoot-faq.md)に関するページを参照します。

## <a name="publish-your-app"></a>アプリケーションの発行
ここで、アプリケーションをサーバーまたは Azure にデプロイし、データ累積を確認します。

![Visual Studio を使用してアプリを発行する](./media/windows-services/15-publish.png)

デバッグ モードで実行している場合、テレメトリはパイプラインにより時間が短縮されるので、数秒でデータが表示されます。 リリース構成でアプリケーションをデプロイすると、データ累積速度は遅くなります。

### <a name="no-data-after-you-publish-to-your-server"></a>サーバーに発行した後でデータはありませんか。
サーバーのファイアウォールで送信トラフィック用のポートを開きます。 必要なアドレスの一覧については、[こちらのページ](https://docs.microsoft.com/azure/application-insights/app-insights-ip-addresses)を参照してください。 

### <a name="trouble-on-your-build-server"></a>ビルド サーバーで問題が発生した場合
[このトラブルシューティング項目](../../azure-monitor/app/asp-net-troubleshoot-no-data.md#NuGetBuild)を参照してください。

> [!NOTE]
> アプリが大量のテレメトリを生成する場合は、アダプティブ サンプリング モジュールが、代表的な一部のイベントのみを送信することによって、ポータルに送信される量を自動的に削減します。 ただし、同じ要求に関連するイベントはグループ単位で選択または選択解除されるので、関連するイベントごとに操作できます。 
> [サンプリングについてはこちらを参照してください](../../azure-monitor/app/sampling.md)。
> 
> 

## <a name="video"></a>ビデオ

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>次の手順
* [他のテレメトリを追加](../../azure-monitor/app/asp-net-more.md) します。

