---
title: "Azure Application Insights を使用した ASP.NET の Web アプリ分析を設定する | Microsoft Docs"
description: "オンプレミスまたは Azure でホストされている ASP.NET Web サイトのパフォーマンス、可用性、利用状況の分析を構成します。"
services: application-insights
documentationcenter: .net
author: CFreemanwa
manager: carmonm
ms.assetid: d0eee3c0-b328-448f-8123-f478052751db
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/15/2017
ms.author: bwren
ms.translationtype: HT
ms.sourcegitcommit: c30998a77071242d985737e55a7dc2c0bf70b947
ms.openlocfilehash: 1960fa0e17ea9aa0ca15bc3ecb8ce5b1f6630a60
ms.contentlocale: ja-jp
ms.lasthandoff: 08/02/2017

---
# <a name="set-up-application-insights-for-your-aspnet-website"></a>ASP.NET Web サイトに Application Insights を設定する

この手順では、[Azure Application Insights](app-insights-overview.md) サービスにテレメトリを送信するように ASP.NET Web アプリを構成します。 このサービスは、IIS サーバーまたはクラウドでホストされる ASP.NET アプリに対して機能します。 アプリのパフォーマンスと利用状況の把握に役立つグラフと強力なクエリ言語が提供され、エラーやパフォーマンスの問題に対する自動アラート機能も備えられています。 多くの開発者にとって、これらの機能はそのままでも便利ですが、必要に応じてテレメトリを拡張したりカスタマイズしたりすることもできます。

セットアップは、Visual Studio でクリック操作を数回行うだけで済みます。 テレメトリの量を制限して、課金を回避するオプションもあります。 そうすることで、ユーザーがそれほど多くないサイトを実験してデバッグしたり、監視したりすることができます。 運用サイトに移行し、監視することに決定した場合は、後で制限を簡単に引き上げることができます。

## <a name="before-you-start"></a>開始する前に
必要なもの:

* Visual Studio 2013 Update 3 以降。 より新しいバージョンが適しています。
* [Microsoft Azure](http://azure.com) サブスクリプション。 チームまたは組織で Azure サブスクリプションを取得している場合、所有者は [Microsoft アカウント](http://live.com)を使用してあなたを追加できます。

関心をお持ちの場合は、他にも次のようなトピックをご覧いただけます。

* [実行時の Web アプリのインストルメント化](app-insights-monitor-performance-live-website-now.md)
* [Azure Cloud Services](app-insights-cloudservices.md)

## <a name="ide"></a>手順 1: Application Insights SDK を追加する

ソリューション エクスプローラーで Web アプリのプロジェクトを右クリックし、**[追加]** > **[Application Insights Telemetry...]** または **[Application Insights の構成]** の順に選択します。

![[追加] と [Application Insights Telemetry...] が強調表示された、ソリューション エクスプローラーのスクリーンショット](./media/app-insights-asp-net/appinsights-03-addExisting.png)

(Visual Studio 2015 でも、[新しいプロジェクト] ダイアログに、Application Insights を追加するオプションがあります。)

Application Insights の構成ページに進みます。

![[アプリを Application Insights に登録します] ページのスクリーンショット](./media/app-insights-asp-net/visual-studio-register-dialog.png)

**a.** Azure へのアクセスに使用するアカウントとサブスクリプションを選択します。

**b.** Azure のリソースを選択します。このリソースのデータをアプリに表示します。 通常は、次のようにします。

* 単一のアプリケーションの[さまざまなコンポーネント用の 1 つのリソース](app-insights-monitor-multi-role-apps.md)を使用します。 
* 関係性のない各アプリケーション用に個別のリソースを作成します。
 
データを格納するリソース グループまたは場所を設定するには、**[設定の構成]** をクリックします。 リソース グループは、データへのアクセスの制御に使用されます。 たとえば、同じシステムの一部を構成する複数のアプリがある場合、そのアプリに関する Application Insights のデータを同じリソース グループ内に配置することができます。

**c.** 課金を回避するために、無料のデータ ボリュームの範囲で上限を設定します。 Application Insights では、一定の量までのテレメトリが無料で提供されます。 リソースが作成された後、ポータルで **[機能と価格設定]** > **[データ管理]** > **[日次ボリューム上限]** の順に開いて選択内容を変更することができます。

**d.** **[登録]** をクリックして、Web アプリ向けに Application Insights を構成します。 デバッグ時と、アプリを発行した後に、[Azure Portal](https://portal.azure.com) にテレメトリが送信されます。

**e.** デバッグ中はポータルにテレメトリを送信しない場合は、Application Insights SDK をアプリに追加するだけにして、ポータルのリソースの構成は行いません。 デバッグ中は、Visual Studio でテレメトリを表示することができます。 この構成ページには後で戻ってくることができます。また、アプリがデプロイされ、[実行時のテレメトリが有効にされる](app-insights-monitor-performance-live-website-now.md)まで待つこともできます。


## <a name="run"></a>手順 2: アプリを実行する
F5 キーを押して、アプリを実行します。 ある程度のテレメトリを生成するために、複数のページを開きます。

Visual Studio で、ログに記録されたイベントの数が表示されます。

![Visual Studio のスクリーンショット。 デバッグ中に表示される [Application Insights] ボタン。](./media/app-insights-asp-net/54.png)

## <a name="step-3-see-your-telemetry"></a>手順 3: テレメトリの確認
Visual Studio または Application Insights Web ポータルで、テレメトリを確認できます。 Visual Studio でテレメトリを検索し、アプリのデバッグに役立てます。 システムを稼働させたら、Web ポータルでパフォーマンスと使用状況を監視します。 

### <a name="see-your-telemetry-in-visual-studio"></a>Visual Studio でのテレメトリの表示

Visual Studio で、[Application Insights] ウィンドウを開きます。 **[Application Insights]** ボタンをクリックするか、ソリューション エクスプローラーでプロジェクトを右クリックし、**[Application Insights]** を選択してから **[ライブ テレメトリの検索]** をクリックします。

Visual Studio の [Application Insights の検索] ウィンドウの **[Data from Debug session (デバッグ セッションからのデータ)]** ビューで、アプリのサーバー側で生成されたテレメトリを確認します。 フィルターを試したり、任意のイベントをクリックして詳細を表示したりしてみましょう。

![[Application Insights] ウィンドウの [Data from Debug session (デバッグ セッションからのデータ)] ビューのスクリーンショット。](./media/app-insights-asp-net/55.png)

> [!NOTE]
> データが何も表示されない場合は、時間の範囲が正しいかどうかを確認し、検索アイコンをクリックします。

[Visual Studio の Application Insights ツールの詳細については、こちらを参照してください](app-insights-visual-studio.md)。

<a name="monitor"></a>
### <a name="see-telemetry-in-web-portal"></a>Web ポータルでのテレメトリの表示

Application Insights Web ポータルでもテレメトリを確認できます (SDK のみをインストールする場合を除く)。 ポータルには、Visual Studio より多くのグラフ、分析ツール、クロスコンポーネント ビューが用意されています。 ポータルには、アラートも用意されています。

Application Insights リソースを開きます。 [Azure Portal](https://portal.azure.com/) にサインインすると、そこに表示されます。または、Visual Studio でプロジェクトを右クリックし、メニューから表示します。

![Application Insights ポータルを開く方法を示した Visual Studio のスクリーンショット](./media/app-insights-asp-net/appinsights-04-openPortal.png)

> [!NOTE]
> アクセス エラーが発生した場合: Microsoft 資格情報のセットが複数あり、間違ったセットでサインインしていませんか。 ポータルでサインアウトし、もう一度サインインしてください。

ポータルを開くと、アプリのテレメトリが表示されます。

![Application Insights 概要ページのスクリーンショット](./media/app-insights-asp-net/66.png)

詳細を表示するには、ポータルで任意のタイルまたはグラフをクリックします。

[Azure Portal での Application Insights の使用方法の詳細については、こちらを参照してください](app-insights-dashboards.md)。

## <a name="step-4-publish-your-app"></a>手順 4: アプリを発行する
IIS サーバーまたは Azure にアプリを発行します。 [ライブ メトリック ストリーム](app-insights-metrics-explorer.md#live-metrics-stream) を観察して、必要な処理がすべて滞りなく実行されていることを確認してください。

Application Insights ポータルにはテレメトリが蓄積されており、メトリックを監視したり、目的のテレメトリを検索したり、[ダッシュボード](app-insights-dashboards.md)を設定したりすることができます。 強力な [Log Analytics クエリ言語](https://docs.loganalytics.io/)を使って使用状況やパフォーマンスを分析したり、特定のイベントを見つけたりすることができます。

[Visual Studio](app-insights-visual-studio.md) から各種ツール (診断検索、[傾向](app-insights-visual-studio-trends.md)など) を使って継続的にテレメトリを分析することもできます。

> [!NOTE]
> アプリから送信されたテレメトリの量が[スロットル制限](app-insights-pricing.md#limits-summary)に近づくと、自動[サンプリング](app-insights-sampling.md)がオンに変わります。 アプリから送信されるテレメトリの量をサンプリングによって抑えながら、診断に利用できる相関性のあるデータを維持することができます。
>
>

## <a name="land"></a>設定の完了

ご利用ありがとうございます。 Application Insights パッケージをアプリにインストールし、Azure の Application Insights サービスにテレメトリを送信するように構成しました。

![テレメトリの移動のダイアグラム](./media/app-insights-asp-net/01-scheme.png)

アプリのテレメトリを受信する Azure リソースは、*インストルメンテーション キー*によって識別されます。 このキーは、ApplicationInsights.config ファイルにあります。


## <a name="upgrade-to-future-sdk-versions"></a>新しいバージョンの SDK にアップグレードする
[SDK の新しいリリース](https://github.com/Microsoft/ApplicationInsights-dotnet-server/releases)にアップグレードするには、**NuGet パッケージ マネージャー**をもう一度開き、インストールされているパッケージに対してフィルターを実行します。 **[Microsoft.ApplicationInsights.Web]**、**[アップグレード]** の順に選択します。

ApplicationInsights.config をカスタマイズしている場合は、アップグレードする前にコピーを保存しておきます。 その後、新しいバージョンに変更をマージします。

## <a name="video"></a>ビデオ

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>次のステップ

### <a name="more-telemetry"></a>テレメトリの追加

* **[ブラウザーとページ読み込みデータ](app-insights-javascript.md)** - Web ページにコード スニペットを挿入します。
* **[より詳細な依存関係の取得と例外の監視](app-insights-monitor-performance-live-website-now.md)** - サーバーに Status Monitor をインストールします。
* **[コード カスタム イベント](app-insights-api-custom-events-metrics.md)** - ユーザー アクションのカウント、時間の計測、または測定を行います。
* **[ログ データの取得](app-insights-asp-net-trace-logs.md)** - ログ データをテレメトリに関連付けます。

### <a name="analysis"></a>分析

* **[Visual Studio での Application Insights の操作](app-insights-visual-studio.md)**<br/>テレメトリ、診断検索、コードのドリル スルーを使用したデバッグについて説明しています。
* **[Application Insights ポータルの操作](app-insights-dashboards.md)**<br/> ダッシュボード、強力な診断および分析ツール、アラート、アプリケーションのリアルタイム依存関係マップ、テレメトリのエクスポートについて説明しています。
* **[Analytics](app-insights-analytics-tour.md)** - 強力なクエリ言語です。

### <a name="alerts"></a>アラート

* [可用性テスト](app-insights-monitor-web-app-availability.md): サイトが Web で表示できることを確認するために、テストを作成します。
* [スマート診断](app-insights-proactive-diagnostics.md): これらのテストは自動的に実行されます。セットアップするために何かをする必要はありません。 アプリの要求が失敗する割合が異常な場合に通知します。
* [メトリック アラート](app-insights-alerts.md): メトリックがしきい値を超えた場合に警告するように設定します。 メトリック アラートはカスタム メトリックで設定し、コード化してアプリに組み込むことができます。

### <a name="automation"></a>Automation

* [Application Insights リソースの作成の自動化](app-insights-powershell.md)

