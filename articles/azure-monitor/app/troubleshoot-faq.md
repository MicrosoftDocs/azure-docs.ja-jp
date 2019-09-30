---
title: Azure Application Insights の FAQ | Microsoft Docs
description: Application Insights についてよく寄せられる質問。
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 0e3b103c-6e2a-4634-9e8c-8b85cf5e9c84
ms.service: application-insights
ms.workload: mobile
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/17/2018
ms.author: mbullwin
ms.openlocfilehash: 778a95db8ce462d06e2464db56b542f8113a4960
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67875372"
---
# <a name="application-insights-frequently-asked-questions"></a>Application Insights:よく寄せられる質問

## <a name="configuration-problems"></a>構成の問題
*次のセットアップで問題が発生しています。*

* [.NET アプリ](asp-net-troubleshoot-no-data.md)
* [既に実行中のアプリの監視](monitor-performance-live-website-now.md#troubleshoot)
* [Azure Diagnostics](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md)
* [Java Web アプリ](java-troubleshoot.md)

*サーバーからデータを取得できません。*

* [ファイアウォール例外の設定](ip-addresses.md)
* [ASP.NET サーバーのセットアップ](monitor-performance-live-website-now.md)
* [Java サーバーのセットアップ](java-agent.md)

## <a name="can-i-use-application-insights-with-"></a>Application Insights と共に使用できるもの

* [Azure VM または Azure 仮想マシン スケール セットの IIS サーバー上の Web アプリ](azure-vm-vmss-apps.md)
* [オンプレミスの IIS サーバーまたは VM 内で動作する Web アプリ](asp-net.md)
* [Java Web アプリ](java-get-started.md)
* [Node.js アプリ](nodejs.md)
* [Azure で動作する Web アプリ](azure-web-apps.md)
* [Azure の Cloud Services](cloudservices.md)
* [Docker で実行中のアプリ サーバー](docker.md)
* [単一ページの Web アプリ](javascript.md)
* [SharePoint](sharepoint.md)
* [Windows デスクトップ アプリ](windows-desktop.md)
* [その他のプラットフォーム](platforms.md)

## <a name="is-it-free"></a>これは無料ですか。

はい、試験段階用です。 Basic 価格プランでは、アプリケーションは毎月無料で一定のデータ許容量を送信できます。 無料許容量は、開発や、少数のユーザー向けにアプリを発行するのに十分な大きさです。 指定した以上のデータ量が処理されることを防ぐために上限を設定できます。

上限を超える量のテレメトリは GB 単位で課金されます。 課金額を制限する方法について、[こちら](pricing.md)でいくつかのヒントを紹介しています。

Enterprise プランでは、テレメトリを送信した Web サーバー ノードごとに、1 日単位で料金が請求されます。 このプランは、連続エクスポートを大規模に使用する場合に適しています。

[価格プランを参照してください](https://azure.microsoft.com/pricing/details/application-insights/)。

## <a name="how-much-is-it-costing"></a>どれくらいのコストが発生していますか?

* Application Insights リソースで **[使用量と推定コスト] ページ**を開きます。 最近の利用状況のグラフが表示されます。 必要に応じて、データ量の上限を設定できます。
* [Azure の [課金] ブレード](https://portal.azure.com/#blade/Microsoft_Azure_Billing/BillingBlade/Overview)を開き、リソース全体での請求額を確認します。

## <a name="q14"></a>Application Insights によってどのような変更がプロジェクトに加えられますか?
詳細は、プロジェクトの種類によって異なります。 Web アプリケーションの場合:

* 次のファイルがプロジェクトに追加されます。

  * ApplicationInsights.config
  * ai.js
* これらの NuGet パッケージをインストールします。

  * *Application Insights API* - コア API
  * *Application Insights API for Web Applications* - サーバーからテレメトリを送信するために使用されます
  * *Application Insights API for JavaScript Applications* - クライアントからテレメトリを送信するために使用されます

    パッケージには、これらのアセンブリが含まれます。
  * Microsoft.ApplicationInsights
  * Microsoft.ApplicationInsights.Platform
* 次の項目を挿入します。

  * web.config
  * packages.config
* (新しいプロジェクトのみ。[Application Insights を既存のプロジェクトに追加している場合][start]は、手動でこの操作を行う必要があります)。これらを Application Insights リソース ID で初期化するためのスニペットを、クライアントとサーバーのコードに挿入します。 たとえば、MVC アプリでは、Views/Shared/_Layout.cshtml マスター ページにコードが挿入されます。

## <a name="how-do-i-upgrade-from-older-sdk-versions"></a>以前のバージョンの SDK からアップグレードする方法。
お使いのアプリケーションに適切な SDK については、「 [リリース ノート](release-notes.md) 」をご覧ください。

## <a name="update"></a>自分のプロジェクトがデータを送信する Azure のリソースを変更するにはどうすればいいですか?
ソリューション エクスプローラーで、 `ApplicationInsights.config` を右クリックし、 **[Application Insights の更新]** を選択します。 Azure の既存または新規のリソースにデータを送信できます。 更新ウィザードでは、サーバー SDK のデータの送信先を決定する、ApplicationInsights.config のインストルメンテーション キーを変更します。 [すべて更新] を選択解除している場合を除き、Web ページ内のキーが表示される場所でもキーが変更されます。

## <a name="what-is-status-monitor"></a>Status Monitor とは何ですか?

IIS Web サーバーで Web アプリ内の Application Insights を構成するために使用できるデスクトップ アプリです。 テレメトリは収集されないので、アプリの構成中以外は停止しておくことができます。 

[詳細情報](monitor-performance-live-website-now.md#questions)。

## <a name="what-telemetry-is-collected-by-application-insights"></a>Application Insights では、どのようなテレメトリが収集されますか?

サーバーの Web アプリから:

* HTTP 要求
* [依存関係](asp-net-dependencies.md)。 SQL Database の呼び出し、外部サービスの HTTP 呼び出し、Azure Cosmos DB、テーブル、Blob Storage、およびキューの呼び出し。 
* [例外](asp-net-exceptions.md)とスタック トレース。
* [パフォーマンス カウンター](performance-counters.md) - [Status Monitor](monitor-performance-live-website-now.md)、[Azure monitoring for App Services](azure-web-apps.md)、[Azure monitoring for VM または仮想マシン スケール セット](azure-vm-vmss-apps.md)、または[Application Insights collectd ライター](java-collectd.md)を使用している場合。
* コード化する[カスタム イベントとカスタム メトリック](api-custom-events-metrics.md)。
* [トレース ログ](asp-net-trace-logs.md) (適切なコレクターを構成する場合)。

[クライアントの Web ページ](javascript.md)から:

* [ページ ビュー数](usage-overview.md)
* [AJAX 呼び出し](asp-net-dependencies.md)。実行中のスクリプトから行われる要求。
* ページ ビューの読み込みデータ
* ユーザー数とセッション数
* [認証されたユーザー ID](api-custom-events-metrics.md#authenticated-users)

その他のソースから (構成する場合):

* [Azure Diagnostics](../platform/diagnostics-extension-to-application-insights.md)
* [Analytics へのインポート](../platform/data-collector-api.md)
* [Log Analytics](../platform/data-collector-api.md)
* [Logstash](../platform/data-collector-api.md)

## <a name="can-i-filter-out-or-modify-some-telemetry"></a>一部のテレメトリを除外または変更することはできますか?

はい。サーバーで以下のようなものを記述できます。

* 選択されたテレメトリ項目に対して、それがアプリから送信される前にフィルター処理やプロパティの追加を行うテレメトリ プロセッサ。
* テレメトリの全項目にプロパティを追加するテレメトリ初期化子。

ASP.NET の場合は[こちら](api-filtering-sampling.md)、Java の場合は[こちら](java-filter-telemetry.md)で詳細を確認してください。

## <a name="how-are-city-countryregion-and-other-geo-location-data-calculated"></a>市区町村や国や地域などの geo ロケーション データはどのように計算されますか?

Web クライアントの IP アドレス (IPv4 または IPv6) の検索に [GeoLite2](https://dev.maxmind.com/geoip/geoip2/geolite2/) を使用しています。

* ブラウザー テレメトリ:送信者の IP アドレスを収集します。
* サーバー テレメトリ:Application Insights モジュールでクライアントの IP アドレスが収集されます。 `X-Forwarded-For` が設定されている場合は収集されません。

別のヘッダーから IP アドレスを取得するように `ClientIpHeaderTelemetryInitializer` を構成できます。 たとえば一部のシステムでは、プロキシ、ロード バランサー、または CDN によって IP アドレスが `X-Originating-IP` に移動されます。 [詳細情報](https://apmtips.com/blog/2016/07/05/client-ip-address/)。

[Power BI を使用する](export-power-bi.md )と、要求テレメトリを地図上に表示できます。


## <a name="data"></a>ポータルでのデータ保持期間はどのくらいですか? セキュリティで保護されていますか?
[データの保持とプライバシー][data]に関するページをご覧ください。

## <a name="could-personal-data-be-sent-in-the-telemetry"></a>テレメトリで個人データが送信されることはありますか?

コードでそのようなデータを送信する場合は可能性があります。 また、スタック トレース内の変数に個人データが含まれている場合にも、送信される可能性があります。 その個人データの処理が適切に行われるように、開発チームでリスク評価を実施する必要があります。 [こちら](data-retention-privacy.md)から、データ リテンション期間とプライバシーについての詳細を確認してください。

geo ロケーション属性の検索後、クライアント Web アドレスの**すべて**のオクテットは常に 0 に設定されます。

## <a name="my-instrumentation-key-is-visible-in-my-web-page-source"></a>Web ページのソースに自分のインストルメンテーション キーが表示されます。 

* 監視ソリューションにおいてはよくあることです。
* iKey を使用してデータを盗むことはできません。
* データ スキューやアラートのトリガーに使用される可能性はあります。
* これまでに、お客様がそのような問題に遭遇したという事例は報告されていません。

以下のような方法で対処できます。

* クライアント データとサーバー データに 2 つの独立したインストルメンテーション キー (別々の Application Insights リソース) を使用します。 または
* サーバーで実行するためのプロキシを記述し、Web クライアントからそのプロキシを介してデータを送信します。

## <a name="post"></a>診断検索で POST データを表示する方法を教えてください。
POST データは自動ではログに記録されませんが、TrackTrace 呼び出しを使用してメッセージ パラメーターにデータを格納できます。 文字列プロパティの制限よりもサイズ制限は大きいですが、フィルター処理には使用できません。

## <a name="should-i-use-single-or-multiple-application-insights-resources"></a>Application Insights リソースは、単一リソースと複数リソースのどちらを使用すべきですか?

1 つのビジネス システム内のすべてのコンポーネントまたはロールに対しては、単一リソースを使用します。 開発、テスト、およびリリースのバージョン、および独立したアプリケーションに対しては、複数のリソースを使用します。

* [こちら](separate-resources.md)の説明を参照してください
* [例 - worker ロールと web ロールを使用したクラウド サービス](cloudservices.md)

## <a name="how-do-i-dynamically-change-the-instrumentation-key"></a>インストルメンテーション キーを動的に変更するには、どうすればよいですか?

* [こちら](separate-resources.md)で説明しています
* [例 - worker ロールと web ロールを使用したクラウド サービス](cloudservices.md)

## <a name="what-are-the-user-and-session-counts"></a>ユーザー数とセッション数とは何ですか?

* JavaScript SDK では、Web クライアントにユーザー Cookie を設定することで戻ってきたユーザーを識別し、セッション Cookie を設定することでグループ アクティビティを識別します。
* クライアント側のスクリプトがない場合は、[サーバーで Cookie を設定](https://apmtips.com/blog/2016/07/09/tracking-users-in-api-apps/)できます。
* 1 人の実在するユーザーが、複数の異なるブラウザーや、プライベート/シークレット ブラウズ、または複数のコンピューターでサイトを利用した場合、それらは複数のユーザーとしてカウントされます。
* 複数のコンピューターやブラウザー間でログイン済みのユーザーを識別するには、[setAuthenticatedUserContext()](api-custom-events-metrics.md#authenticated-users) の呼び出しを追加します。

## <a name="q17"></a> Application Insights の機能をすべて有効にしているでしょうか?
| 表示内容 | 表示方法 | 用途 |
| --- | --- | --- |
| 可用性グラフ |[Web テスト](monitor-web-app-availability.md) |Web アプリが稼働しているか確認する |
| サーバー アプリ パフォーマンス: 応答時間、... |[Application Insights をプロジェクトに追加する](asp-net.md)か、[AI Status Monitor をサーバーにインストールする](monitor-performance-live-website-now.md) (または独自のコードを記述して[依存関係を追跡する](api-custom-events-metrics.md#trackdependency)) |パフォーマンスの問題を検出する |
| 依存関係テレメトリ |[AI Status Monitor をサーバーにインストールする](monitor-performance-live-website-now.md) |データベースや、その他の外部コンポーネントの問題を診断する |
| 例外からスタック トレースを取得する |[コード内に TrackException 呼び出しを挿入する](asp-net-exceptions.md) (自動で報告されるものもある) |例外を検出して診断する |
| ログ トレースの検索 |[ログ アダプターを追加する](asp-net-trace-logs.md) |例外、パフォーマンスの問題を診断する |
| クライアントの利用状況の基本情報: ページ ビュー、セッション、... |[Web ページの JavaScript の初期化子](javascript.md) |Usage analytics |
| クライアントのカスタム メトリック |[Web ページでの呼び出しの追跡](api-custom-events-metrics.md) |ユーザー エクスペリエンスを向上させる |
| サーバーのカスタム メトリック |[サーバーでの呼び出しの追跡](api-custom-events-metrics.md) |Business intelligence |

## <a name="why-are-the-counts-in-search-and-metrics-charts-unequal"></a>検索グラフとメトリック グラフで数が等しくないのはなぜですか?

[サンプリング](sampling.md)により、アプリからポータルに実際に送信されたテレメトリ項目 (要求、カスタム イベントなど) の数が減少します。 検索グラフには、実際に受信した項目の数が表示されます。 イベント数を表示するメトリック グラフには、発生した元のイベントの数が表示されます。 

送信される各項目には、その項目が表す元のイベントの数を示す `itemCount` プロパティが含まれています。 Analytics で次のクエリを実行すると、サンプリング操作を確認できます。

```
    requests | summarize original_events = sum(itemCount), transmitted_events = count()
```


## <a name="automation"></a>Automation

### <a name="configuring-application-insights"></a>Application Insights の構成

Azure Resource Monitor を使用して [PowerShell スクリプトを記述する](powershell.md)ことにより、以下の操作を実行できます。

* Application Insights リソースの作成と更新。
* 価格プランの設定。
* インストルメンテーション キーの取得。
* メトリック アラートの追加。
* 可用性テストの追加。

メトリックス エクスプローラー レポートや連続エクスポートの設定はできません。

### <a name="querying-the-telemetry"></a>テレメトリに対するクエリの実行

[REST API](https://dev.applicationinsights.io/) を使用して [Analytics](analytics.md) クエリを実行します。

## <a name="how-can-i-set-an-alert-on-an-event"></a>イベントにアラートを設定するには、どうすればよいですか?

Azure アラートはメトリックにのみ設定できます。 イベントが発生するたびにしきい値を超える、カスタム メトリックを作成してください。 その後、メトリックにアラートを設定します。 次の点に注意: メトリックがしきい値を超えると、超えたときの方向がどちらであっても通知が届くことになります。初期値がしきい値より高くても低くても、最初にしきい値を超えるまで通知は届きません。常に数分の待ち時間が発生します。

## <a name="are-there-data-transfer-charges-between-an-azure-web-app-and-application-insights"></a>Azure Web アプリと Application Insights の間でデータ転送料金は発生しますか?

* Application Insights の収集エンドポイントがあるデータ センターに Azure Web アプリがホストされている場合、料金はかかりません。 
* ホスト データ センターに収集エンドポイントがない場合は、アプリのテレメトリに対して [Azure の送信料金](https://azure.microsoft.com/pricing/details/bandwidth/)が発生します。

これは、Application Insights リソースがホストされている場所ではなく、 単に Microsoft のエンドポイントの割り当てによって決まります。

## <a name="can-i-send-telemetry-to-the-application-insights-portal"></a>テレメトリを Application Insights ポータルに送信できますか?

Microsoft の SDK と [SDK API](api-custom-events-metrics.md) を使用することをお勧めします。 各種[プラットフォーム](platforms.md)向けに異なる SDK が用意されています。 これらの SDK では、バッファリング、圧縮、調整、再試行などを処理します。 ただし、[取り込みスキーマ](https://github.com/Microsoft/ApplicationInsights-dotnet/tree/develop/Schema/PublicSchema)と[エンドポイント プロトコル](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/ENDPOINT-PROTOCOL.md)はパブリックです。

## <a name="can-i-monitor-an-intranet-web-server"></a>イントラネット Web サーバーを監視できますか?

はい。ただし、ファイアウォールの例外またはプロキシによるリダイレクトを使用して、Microsoft のサービスへのトラフィックを許可する必要があります。
- QuickPulse `https://rt.services.visualstudio.com:443` 
- ApplicationIdProvider `https://dc.services.visualstudio.com:443` 
- TelemetryChannel `https://dc.services.visualstudio.com:443` 


[ここ](../../azure-monitor/app/ip-addresses.md)でサービスと IP アドレスのすべての一覧を確認できます。

### <a name="firewall-exception"></a>ファイアウォールの例外

ご利用の Web サーバーから Microsoft のエンドポイントへの利用統計情報の送信を許可します。 

### <a name="gateway-redirect"></a>ゲートウェイのリダイレクト

構成内のエンドポイントを上書きすることによって、ご利用のサーバーからのトラフィックをイントラネット上のゲートウェイにルーティングします。
これらの "Endpoint" プロパティが config に存在しない場合、これらのクラスは、次の ApplicationInsights.config の例に示されているように、既定値を使用します。 

ご利用のゲートウェイは、Microsoft のエンドポイントのベース アドレスにトラフィックをルーティングする必要があります。 構成内の既定値を `http://<your.gateway.address>/<relative path>` に置き換えてください。


#### <a name="example-applicationinsightsconfig-with-default-endpoints"></a>既定のエンドポイントを使用する ApplicationInsights.config の例:
```xml
<ApplicationInsights>
  ...
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <QuickPulseServiceEndpoint>https://rt.services.visualstudio.com/QuickPulseService.svc</QuickPulseServiceEndpoint>
    </Add>
  </TelemetryModules>
    ...
  <TelemetryChannel>
     <EndpointAddress>https://dc.services.visualstudio.com/v2/track</EndpointAddress>
  </TelemetryChannel>
  ...
  <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
    <ProfileQueryEndpoint>https://dc.services.visualstudio.com/api/profiles/{0}/appId</ProfileQueryEndpoint>
  </ApplicationIdProvider>
  ...
</ApplicationInsights>
```

_ApplicationIdProvider は v2.6.0 以降で使用できます_

### <a name="proxy-passthrough"></a>プロキシのパススルー

プロキシのパススルーは、マシン レベルとアプリケーション レベルのどちらかのプロキシを構成することで実現できます。
詳細については、[DefaultProxy](https://docs.microsoft.com/dotnet/framework/configure-apps/file-schema/network/defaultproxy-element-network-settings) に関する dotnet の記事を参照してください。
 
 Web.config の例:
 ```xml
<system.net>
    <defaultProxy>
      <proxy proxyaddress="http://xx.xx.xx.xx:yyyy" bypassonlocal="true"/>
    </defaultProxy>
</system.net>
```
 

## <a name="can-i-run-availability-web-tests-on-an-intranet-server"></a>イントラネット サーバーで可用性 Web テストを実行できますか?

Microsoft の [Web テスト](monitor-web-app-availability.md)は、世界中に分布する Points of Presence で実行されます。 以下に示す 2 つのソリューションがあります。

* ファイアウォール ドア - [頻繁に変更される多数の Web テスト エージェント](ip-addresses.md)からのサーバーへの要求を許可します。
* イントラネット内部からサーバーに定期的な要求を送信する独自のコードを記述します。 Visual Studio Web テストを実行して、このコードをテストすることができます。 テストの結果は TrackAvailability() API を使用して Application Insights に送信できます。

## <a name="how-long-does-it-take-for-telemetry-to-be-collected"></a>テレメトリの収集にはどれくらいの時間がかかりますか?

Application Insights のほとんどのデータは、待ち時間が 5 分未満となっています。 一部のデータ (通常は、大きなログ ファイル) については、それよりも時間がかかることがあります。 詳細については、「[Application Insights の SLA](https://azure.microsoft.com/support/legal/sla/application-insights/v1_2/)」を参照してください。

## <a name="more-answers"></a>その他の回答
* [Application Insights フォーラム](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)

<!--Link references-->

[data]: data-retention-privacy.md
[platforms]: platforms.md
[start]: app-insights-overview.md
[windows]: app-insights-windows-get-started.md
