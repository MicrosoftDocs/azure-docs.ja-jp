---
title: パフォーマンスの低下をトラブルシューティングする
description: アプリの動作の監視、データの収集、問題の軽減など、Azure App Service でのアプリパフォーマンスの低下に関する問題のトラブルシューティングの方法について説明します。
tags: top-support-issue
keywords: Web アプリのパフォーマンス、低速なアプリ、アプリが低速
ms.assetid: b8783c10-3a4a-4dd6-af8c-856baafbdde5
ms.topic: article
ms.date: 08/03/2016
ms.custom: seodec18
ms.openlocfilehash: 98c11a72b5aea0fac15d943977402289dc33a970
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/02/2019
ms.locfileid: "74688308"
---
# <a name="troubleshoot-slow-app-performance-issues-in-azure-app-service"></a>Azure App Service でのアプリのパフォーマンス低下に関する問題のトラブルシューティング
この記事は、[Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) でのアプリのパフォーマンス低下に関する問題のトラブルシューティングに役立ちます。

この記事についてさらにヘルプが必要な場合は、いつでも [MSDN の Azure フォーラムとスタック オーバーフロー フォーラム](https://azure.microsoft.com/support/forums/)で Azure エキスパートに問い合わせることができます。 または、Azure サポート インシデントを送信できます。 その場合は、 [Azure サポートのサイト](https://azure.microsoft.com/support/options/) に移動して、 **[サポートの要求]** をクリックします。

## <a name="symptom"></a>症状
ブラウザーでアプリにアクセスしたときのページの読み込みが遅く、タイムアウトすることもある。

## <a name="cause"></a>原因
この症状は多くの場合、アプリケーション レベルの問題が原因で発生します。その例を次に示します。

* ネットワーク要求に時間がかかっている
* アプリケーション コードまたはデータベース クエリが効率的でない
* アプリケーションのメモリ/CPU 使用率が高い
* 例外が発生してアプリケーションがクラッシュする

## <a name="troubleshooting-steps"></a>トラブルシューティングの手順
トラブルシューティングは、大きく次の 3 つのタスクに分けられます。この 3 つのタスクを上から順に行います。

1. [アプリケーションの動作を観察、監視する](#observe)
2. [データを収集する](#collect)
3. [問題を緩和する](#mitigate)

[App Service](overview.md) ではステップごとにさまざまなオプションを使用できます。

<a name="observe" />

### <a name="1-observe-and-monitor-application-behavior"></a>1.アプリケーションの動作を観察、監視する
#### <a name="track-service-health"></a>サービス正常性を追跡する
Microsoft Azure は、サービスの中断やパフォーマンスの低下があるたびに、毎回公表します。 サービスの正常性は、[Azure Portal](https://portal.azure.com/) で追跡できます。 詳細については、[サービスの正常性の追跡](../monitoring-and-diagnostics/insights-service-health.md)に関するページを参照してください。

#### <a name="monitor-your-app"></a>アプリを監視する
Web アプリに問題が発生しているかどうかは、アプリを監視することによって確認することができます。 アプリのブレードで **[要求とエラー]** タイルをクリックします。 **[メトリック]** ブレードには、追加できるすべてのメトリックが表示されます。

アプリに関しては、次のメトリックを監視するようお勧めします。

* 平均メモリ ワーキング セット
* 平均応答時間
* CPU 時間
* メモリ ワーキング セット
* Requests

![アプリのパフォーマンスを監視する](./media/app-service-web-troubleshoot-performance-degradation/1-monitor-metrics.png)

詳細については、次を参照してください。

* [Azure App Service のアプリの監視](web-sites-monitor.md)
* [アラート通知を受け取る](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

#### <a name="monitor-web-endpoint-status"></a>Web エンドポイントの状態を監視する
アプリを **Standard** 価格レベルで実行している場合、App Service で 3 つの地域から 2 つのエンドポイントを監視することができます。

エンドポイント監視では、地理的に分散した場所から Web URL の応答時間とアップタイムをテストする Web テストを構成します。 このテストでは、Web URL に対して HTTP get 操作を実行し、各場所から応答時間とアップタイムを測定します。 構成された各場所では、テストを 5 分ごとに実行します。

アップタイムは HTTP 応答コードを使用して監視され、応答時間はミリ秒単位で測定されます。 HTTP 応答コードが 400 以上である場合、または、応答に 30 秒以上かかる場合、監視テストは失敗します。 すべての指定した場所から監視テストが成功した場合、エンドポイントは利用可能と見なされます。

セットアップ方法については、「[Azure App Service のアプリの監視](web-sites-monitor.md)」を参照してください。

また、「 [Keeping Azure Web Sites up plus Endpoint Monitoring with Stefan Schackow (Azure の Web サイトの保持とエンドポイントの監視 - Stefan Schackow 共演)](https://channel9.msdn.com/Shows/Azure-Friday/Keeping-Azure-Web-Sites-up-plus-Endpoint-Monitoring-with-Stefan-Schackow) 」で、エンドポイント監視に関するビデオをご覧いただけます。

#### <a name="application-performance-monitoring-using-extensions"></a>拡張機能を使用したアプリケーション パフォーマンスの監視
"*サイト拡張機能*" を使用して、アプリケーションのパフォーマンスを監視することもできます。

App Service アプリにはそれぞれ拡張可能な管理エンドポイントが用意されており、サイト拡張機能としてデプロイされている強力なツール一式を使用することができます。 次のような拡張機能があります。 

- [Azure DevOps](https://www.visualstudio.com/products/what-is-visual-studio-online-vs.aspx) などのソース コード エディター。 
- アプリに接続されている MySQL データベースのような、接続されたリソース用の管理ツール。

[Azure Application Insights](https://azure.microsoft.com/services/application-insights/) というパフォーマンス監視を目的としたサイト拡張機能も使用できます。 Application Insights を使用するには、SDK でコードをリビルドします。 追加データへのアクセスを提供する拡張機能をインストールすることもできます。 SDK では、アプリの使用状況とパフォーマンスをさらに詳細に監視するコードを記述できます。 詳細については、「[Web アプリケーションのパフォーマンスを監視する](../azure-monitor/app/web-monitor-performance.md)」を参照してください。

<a name="collect" />

### <a name="2-collect-data"></a>2.データを収集する
App Service は、Web サーバーと Web アプリケーションの両方のログ情報を診断する機能を備えています。 情報は Web サーバー診断とアプリケーション診断に分けられます。

#### <a name="enable-web-server-diagnostics"></a>Web サーバー診断を有効にする
次の種類のログを有効または無効にできます。

* **詳細なエラー ログ** - 障害 (状態コード 400 以上) を示す HTTP 状態コードの詳細なエラー情報。 このログには、サーバーがエラー コードを返した理由を特定するために役立つ情報が記録されている場合があります。
* **失敗した要求トレース** - 要求の処理に使用された IIS コンポーネントのトレースや各コンポーネントにかかった時間など、失敗した要求の詳細情報。 これは、アプリのパフォーマンスを向上したり、特定の HTTP エラーの原因を分離しようとする場合に役立ちます。
* **Web サーバーのログ記録** - W3C 拡張ログ ファイル形式を使用した、HTTP トランザクションに関する情報。 これは、サイトで処理された要求の数や特定の IP アドレスからの要求の数などのアプリの全体的なメトリックを特定するときに役立ちます。

#### <a name="enable-application-diagnostics"></a>アプリケーション診断を有効にする
App Service からアプリケーションのパフォーマンス データを収集するためのオプションは、いくつかあります。Visual Studio からライブでアプリケーションをプロファイルしたり、より多くの情報とトレースをログ記録するようにアプリケーション コードを変更したりできます。 アプリケーションへのアクセスの量と、監視ツールでの観察結果に基づいて、オプションを選択できます。

##### <a name="use-application-insights-profiler"></a>Application Insights Profiler を使用する
Application Insights Profiler を有効にすると、詳細なパフォーマンス トレースのキャプチャを開始することができます。 過去に生じた問題を調査する必要がある場合は、最大で 5 日前までのキャプチャされたトレースにアクセスすることができます。 Azure portal でアプリの Application Insights リソースにアクセスできる場合は、このオプションを選択することができます。

Application Insights Profiler は、各 Web 呼び出しの応答時間に関する統計と、応答が遅くなる原因となったコード行を示すトレースを提供します。 一部のコードがパフォーマンスの高い方法で書かれていないために、App Service アプリが低速になることがあります。 例としては、並列で実行できる順次コードや、予期しないデータベースのロック競合などがあります。 このようなコード内のボトルネックは、除去するとアプリのパフォーマンスが向上しますが、綿密なトレースとログを設定せずに検出することは困難です。 Application Insights Profiler によって収集されたトレースは、アプリケーションの速度低下に繋がったコード行を特定し、App Service アプリのこの問題を解決するために役立ちます。

 詳しくは、「[Profiling live apps in Azure App Service with Application Insights (Application Insights を使用して実行中の Azure App Service アプリのプロファイリングを行う)](../azure-monitor/app/profiler.md)」をご覧ください。

##### <a name="use-remote-profiling"></a>リモート プロファイリングの使用
Azure App Service では、Web アプリ、API アプリ、モバイル バック エンドおよび WebJobs をリモートからプロファイリングすることができます。 アプリ リソースにアクセスでき、問題の再現方法を把握しているか、パフォーマンスの問題が発生する正確な時間間隔がわかっている場合に、このオプションを選択します。

Remote Profiling は、プロセスの CPU 使用率が高く、プロセスの実行が予想より遅いか、HTTP 要求の待ち時間が通常よりも長い場合に、プロセスをリモートでプロファイルし、CPU サンプリング呼び出し履歴を取得して、プロセスのアクティビティとコードのホット パスを分析することができて便利です。

詳細については、「[Remote Profiling support in Azure App Service (Azure App Service におけるリモート プロファイリングのサポート)](https://azure.microsoft.com/blog/remote-profiling-support-in-azure-app-service)」を参照してください。

##### <a name="set-up-diagnostic-traces-manually"></a>診断トレースを手動でセットアップする
Web アプリケーションのソース コードにアクセスできる場合は、アプリケーション診断を使用すると、Web アプリケーションによって生成された情報をキャプチャすることができます。 ASP.NET アプリケーションは、 `System.Diagnostics.Trace` クラスを使用してアプリケーション診断ログに情報を記録できます。 ただし、コードを変更し、アプリケーションを再デプロイする必要があります。 この方法は、アプリをテスト環境で実行している場合にお勧めします。

アプリケーションのログを構成する詳細な手順については、「[Enable diagnostics logging for apps in Azure App Service (Azure App Service のアプリの診断ログの有効化)](troubleshoot-diagnostic-logs.md)」をご覧ください。

#### <a name="use-the-diagnostics-tool"></a>診断ツールの使用
App Service には、アプリのトラブルシューティングに役立つ構成不要のインテリジェントな対話型のエクスペリエンスが用意されています。 アプリに問題が発生した場合、診断ツールは問題点を指摘し、その問題のトラブルシューティングをすばやく簡単に行って解決するための適切な情報へとユーザーをガイドします。

App Service 診断にアクセスするには、[Azure Portal](https://portal.azure.com) の App Service アプリまたは App Service 環境に移動します。 左側のナビゲーションで、 **[問題の診断と解決]** をクリックします。

#### <a name="use-the-kudu-debug-console"></a>Kudu デバッグ コンソールを使用する
App Service には、ファイルのデバッグ、調査、アップロード用のデバッグ コンソールのほか、ご利用の環境についての情報を入手するための JSON エンドポイントが用意されています。 このコンソールは、アプリの "*Kudu コンソール*" または "*SCM ダッシュボード*" と呼ばれます。

ダッシュボードには、**https://&lt;アプリ名>.scm.azurewebsites.net/** リンクからアクセスできます。

Kudu には次のような機能があります。

* アプリケーションの環境設定
* ログ ストリーム
* 診断ダンプ
* デバッグ コンソール (Powershell のコマンドレットや基本的な DOS コマンドを実行可能)

Kudu にはもう 1 つ便利な機能があり、アプリケーションからファーストチャンス例外がスローされた場合に、Kudu と SysInternals ツール Procdump を使用してメモリ ダンプを作成することができます。 このメモリ ダンプはプロセスのスナップショットです。アプリに関して、通常より複雑な問題をトラブルシューティングできる場合も少なくありません。

Kudu で利用できる機能の詳細については、[知っておくべき Azure DevOps ツール](https://azure.microsoft.com/blog/windows-azure-websites-online-tools-you-should-know-about/)に関するページを参照してください。

<a name="mitigate" />

### <a name="3-mitigate-the-issue"></a>3.問題を緩和する
#### <a name="scale-the-app"></a>アプリをスケーリングする
Azure App Service では、アプリケーションが実行されるスケールを調整することによって、パフォーマンスとスループットを高めることができます。 アプリのスケール アップには、2 つの関連する措置が伴います。1 つは、App Service プランの価格レベルを引き上げること、もう 1 つは、価格レベルを引き上げた後に特定の設定を構成することです。

スケーリングの詳細については、[Azure App Service でのアプリのスケーリング](manage-scale-up.md)に関する記事を参照してください。

さらに、アプリケーションを複数のインスタンスで実行することもできます。 スケールアウトすると、処理能力が向上するだけでなく、ある程度のフォールト トレランスを確保することができます。 1 つのインスタンスでプロセスがダウンしても、他のインスタンスが要求の処理を続行します。

スケーリングは、[手動] または [自動] に設定することができます。

#### <a name="use-autoheal"></a>AutoHeal を使用する
AutoHeal は、選択された設定 (構成の変更、要求、メモリに基づく制限、要求の実行に必要な時間など) に従って、アプリのワーカー プロセスをリサイクルします。 ほとんどの場合、問題を回復するための一番の近道は、プロセスをリサイクルすることです。 アプリはいつでも、Azure portal 内から直接再起動できますが、AutoHeal はユーザーの介入なしでアプリの再起動を自動的に実行します。 必要な作業は、アプリのルート web.config にいくつかのトリガーを追加することだけです。 アプリケーションが .NET アプリ以外でも、これらの設定は同じように作用します。

詳細については、 [Azure Web Sites の自動復旧](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites/)に関するページを参照してください。

#### <a name="restart-the-app"></a>アプリを再起動する
1 回限りの問題であれば、通常は再起動が最も簡単な復旧方法です。 アプリを停止または再起動するためのオプションは、[Azure portal](https://portal.azure.com/) のアプリ ブレードにあります。

 ![パフォーマンスの問題を解決するためにアプリを再起動する](./media/app-service-web-troubleshoot-performance-degradation/2-restart.png)

アプリの管理には、Azure PowerShell を使用することもできます。 詳細については、 [リソース マネージャーでの Azure PowerShell の使用](../powershell-azure-resource-manager.md)をご覧ください。
