---
title: Azure Application Insights を使用した ASP.NET の Web アプリ分析を設定する | Microsoft Docs
description: オンプレミスまたは Azure でホストされている ASP.NET Web サイトのパフォーマンス、可用性、およびユーザー動作の分析ツールを構成します。
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: d0eee3c0-b328-448f-8123-f478052751db
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: mbullwin
ms.openlocfilehash: 73f62ff8c95fae694a43df48aa99b696fb05d131
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2019
ms.locfileid: "70916278"
---
# <a name="set-up-application-insights-for-your-aspnet-website"></a>ASP.NET Web サイトに Application Insights を設定する

この手順では、[Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) サービスにテレメトリを送信するように ASP.NET Web アプリを構成します。 このサービスは、オンプレミスの IIS サーバーまたはクラウドでホストされる ASP.NET アプリに対して機能します。 アプリのパフォーマンスと利用状況の把握に役立つグラフと強力なクエリ言語が提供され、エラーやパフォーマンスの問題に対する自動アラート機能も備えられています。 多くの開発者にとって、これらの機能はそのままでも便利ですが、必要に応じてテレメトリを拡張したりカスタマイズしたりすることもできます。

セットアップは、Visual Studio でクリック操作を数回行うだけで済みます。 テレメトリの量を制限して、課金を回避するオプションもあります。 この機能により、ユーザーがそれほど多くないサイトを実験してデバッグしたり、監視したりすることができます。 運用サイトに移行し、監視することに決定した場合は、後で制限を簡単に引き上げることができます。

## <a name="prerequisites"></a>前提条件
ASP.NET Web サイトに Application Insights を追加するうえで必要なことは次のとおりです。

- 次のワークロードを使って、[Windows 用の Visual Studio 2019](https://www.visualstudio.com/downloads/) をインストールします。
    - ASP.NET と Web 開発 (オプションのコンポーネントをオフにしないでください)
    - Azure の開発

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

## <a name="ide"></a>手順 1: Application Insights SDK を追加する

> [!IMPORTANT]
> このサンプルのスクリーンショットは、Visual Studio 2017 バージョン 15.9.9 以降に基づきます。 Application Insights に追加する経験は、Visual Studio のバージョンや ASP.NET テンプレート タイプによって異なります。 旧バージョンでは、「Application Insights の構成」といった代替テキストがない場合があります。

ソリューション エクスプローラーで Web アプリ名を右クリックし、 **[追加]**  >  **[Application Insights Telemetry]** を選択します。

![[Application Insights の構成] が強調表示された、ソリューション エクスプローラーのスクリーンショット](./media/asp-net/add-telemetry-new.png)

(Application Insights SDK のバージョンに応じて、最新の SDK リリースへのアップグレードを促すメッセージが表示されることがあります。 メッセージが表示されたら、 **[SDK の更新]** を選択します。)

![スクリーンショット: Microsoft Application Insights SDK の新しいバージョンがあります。 [SDK の更新] が強調表示されています](./media/asp-net/0002-update-sdk.png)

Application Insights の構成画面:

**[開始]** を選択します。

![[アプリを Application Insights に登録します] ページのスクリーンショット](./media/asp-net/00004-start-free.png)

データを格納するリソース グループまたは場所を設定するには、 **[設定の構成]** をクリックします。 リソース グループは、データへのアクセスの制御に使用されます。 たとえば、同じシステムの一部を構成する複数のアプリがある場合、そのアプリに関する Application Insights のデータを同じリソース グループ内に配置することができます。

 **[登録]** を選択します。

![[アプリを Application Insights に登録します] ページのスクリーンショット](./media/asp-net/00005-register-ed.png)

 **[プロジェクト]**  >  **[NuGet パッケージの管理]**  >  **[パッケージ ソース: nuget.org]** の順に選択して、Application Insights SDK の最新の安定版リリースであることを確認します。

 デバッグ時と、アプリを発行した後に、[Azure Portal](https://portal.azure.com) にテレメトリが送信されます。
> [!NOTE]
> デバッグ中はポータルにテレメトリを送信しない場合は、Application Insights SDK をアプリに追加するだけにして、ポータルのリソースの構成は行いません。 デバッグ中は、Visual Studio でテレメトリを表示することができます。 この構成ページには後で戻ってくることができます。また、アプリがデプロイされ、[実行時のテレメトリが有効にされる](../../azure-monitor/app/monitor-performance-live-website-now.md)まで待つこともできます。

## <a name="run"></a>手順 2: アプリケーションを実行する
F5 キーを押して、アプリを実行します。 ある程度のテレメトリを生成するために、複数のページを開きます。

Visual Studio で、ログに記録されたイベント数が表示されます。

![Visual Studio のスクリーンショット。 デバッグ中に表示される [Application Insights] ボタン。](./media/asp-net/00006-Events.png)

## <a name="step-3-see-your-telemetry"></a>手順 3: テレメトリを確認する
Visual Studio または Application Insights Web ポータルで、テレメトリを確認できます。 Visual Studio でテレメトリを検索し、アプリのデバッグに役立てます。 システムを稼働させたら、Web ポータルでパフォーマンスと使用状況を監視します。 

### <a name="see-your-telemetry-in-visual-studio"></a>Visual Studio でのテレメトリの表示

Visual Studio で Application Insights のデータを表示するには、  **[ソリューション エクスプローラー]**  >  **[接続済みサービス]** の順に選択してから **[Application Insights]** を右クリックし、続いて **[ライブ テレメトリの検索]** をクリックします。

Visual Studio の [Application Insights の検索] ウィンドウに、アプリのサーバー側で生成されたテレメトリについて、アプリケーションのデータが表示されます。 フィルターを試したり、任意のイベントをクリックして詳細を表示したりしてみましょう。

![[Application Insights] ウィンドウの [Data from Debug session (デバッグ セッションからのデータ)] ビューのスクリーンショット。](./media/asp-net/55.png)

> [!Tip]
> データが何も表示されない場合は、時間の範囲が正しいかどうかを確認し、検索アイコンをクリックします。

[Visual Studio の Application Insights ツールの詳細については、こちらを参照してください](../../azure-monitor/app/visual-studio.md)。

<a name="monitor"></a>
### <a name="see-telemetry-in-web-portal"></a>Web ポータルでのテレメトリの表示

Application Insights Web ポータルでもテレメトリを確認できます (SDK のみをインストールする場合を除く)。 ポータルには、Visual Studio より多くのグラフ、分析ツール、クロスコンポーネント ビューが用意されています。 ポータルには、アラートも用意されています。

Application Insights リソースを開きます。 [Azure Portal](https://portal.azure.com/) にサインインするか、**ソリューション エクスプローラー** >  **[接続済みサービス]** > **[Application Insights]** を右クリック >  **[Application Insights ポータルを開く]** の順に選択すると、そこに表示されます。

ポータルを開くと、アプリのテレメトリが表示されます。

![Application Insights 概要ページのスクリーンショット](./media/asp-net/007.png)

詳細を表示するには、ポータルで任意のタイルまたはグラフをクリックします。

## <a name="step-4-publish-your-app"></a>手順 4:アプリケーションの発行
IIS サーバーまたは Azure にアプリを発行します。 [ライブ メトリック ストリーム](../../azure-monitor/app/metrics-explorer.md#live-metrics-stream) を観察して、必要な処理がすべて滞りなく実行されていることを確認してください。

Application Insights ポータルにはテレメトリが蓄積されており、メトリックを監視したり、目的のテレメトリを検索したりすることができます。 強力な [Kusto クエリ言語](/azure/kusto/query/)を使用して、使用状況やパフォーマンスを分析したり、特定のイベントを見つけたりすることができます。

[Visual Studio](../../azure-monitor/app/visual-studio.md) から各種ツール (診断検索、[傾向](../../azure-monitor/app/visual-studio-trends.md)など) を使って継続的にテレメトリを分析することもできます。

> [!NOTE]
> アプリから送信されたテレメトリの量が[スロットル制限](../../azure-monitor/app/pricing.md#limits-summary)に近づくと、自動[サンプリング](../../azure-monitor/app/sampling.md)がオンに変わります。 アプリから送信されるテレメトリの量をサンプリングによって抑えながら、診断に利用できる相関性のあるデータを維持することができます。
>
>

## <a name="land"></a>設定の完了

お疲れさまでした。 Application Insights パッケージをアプリにインストールし、Azure の Application Insights サービスにテレメトリを送信するように構成しました。

アプリのテレメトリを受信する Azure リソースは、*インストルメンテーション キー*によって識別されます。 このキーは、ApplicationInsights.config ファイルにあります。


## <a name="upgrade-to-future-sdk-versions"></a>新しいバージョンの SDK にアップグレードする
[SDK の新しいリリース](https://github.com/Microsoft/ApplicationInsights-dotnet-server/releases)にアップグレードするには、**NuGet パッケージ マネージャー**を開き、インストールされているパッケージに対してフィルターを実行します。 **[Microsoft.ApplicationInsights.Web]** 、 **[アップグレード]** の順に選択します。

ApplicationInsights.config をカスタマイズしている場合は、アップグレードする前にコピーを保存しておきます。 その後、新しいバージョンに変更をマージします。

## <a name="video"></a>ビデオ

* [.NET アプリケーションを使って最初から Application Insights を構成する](https://www.youtube.com/watch?v=blnGAVgMAfA)ステップ バイ ステップの外部ビデオ。

## <a name="next-steps"></a>次の手順

関心をお持ちの場合は、他にも次のようなトピックをご覧いただけます。

* [実行時の Web アプリのインストルメント化](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Azure Cloud Services](../../azure-monitor/app/cloudservices.md)

### <a name="more-telemetry"></a>テレメトリの追加

* **[ブラウザーとページ読み込みデータ](../../azure-monitor/app/javascript.md)** - Web ページにコード スニペットを挿入します。
* **[より詳細な依存関係の取得と例外の監視](../../azure-monitor/app/monitor-performance-live-website-now.md)** - サーバーに Status Monitor をインストールします。
* **[コード カスタム イベント](../../azure-monitor/app/api-custom-events-metrics.md)** - ユーザー アクションのカウント、時間の計測、または測定を行います。
* **[ログ データの取得](../../azure-monitor/app/asp-net-trace-logs.md)** - ログ データをテレメトリに関連付けます。

### <a name="analysis"></a>分析

* **[Visual Studio での Application Insights の操作](../../azure-monitor/app/visual-studio.md)**<br/>テレメトリ、診断検索、コードのドリル スルーを使用したデバッグについて説明しています。
* **[Analytics](../../azure-monitor/log-query/get-started-portal.md)** - 強力なクエリ言語です。

### <a name="alerts"></a>アラート

* [可用性テスト](../../azure-monitor/app/monitor-web-app-availability.md): サイトが Web で表示できることを確認するためのテストを作成します。
* [スマート診断](../../azure-monitor/app/proactive-diagnostics.md): これらのテストは自動的に実行されます。セットアップするために何かをする必要はありません。 アプリの要求が失敗する割合が異常な場合に通知します。
* [メトリック アラート](../../azure-monitor/app/alerts.md): メトリックがしきい値を超えた場合に警告するようにアラートを設定 します。 メトリック アラートはカスタム メトリックで設定し、コード化してアプリに組み込むことができます。

### <a name="automation"></a>Automation

* [Application Insights リソースの作成の自動化](../../azure-monitor/app/powershell.md)
