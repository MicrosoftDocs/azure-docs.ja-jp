---
title: "Application Insights を使用して Azure で実行中の Web アプリのプロファイリングを行う | Microsoft Docs"
description: "フットプリントの小さいプロファイラーを使用して Web サーバー コードのホット パスを特定できます。"
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: bwren
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: ad9174c47e1af8d5dba080ec82f2a56fbbf78782
ms.contentlocale: ja-jp
ms.lasthandoff: 06/29/2017

---
# <a name="profiling-live-azure-web-apps-with-application-insights"></a>Application Insights を使用して実行中の Azure Web アプリのプロファイリングを行う

*Application Insights のこの機能は、App Services の正規版であり、Compute のプレビュー版です。*

[Azure Application Insights](app-insights-overview.md) のプロファイリング ツールを使用して、実行中の Web アプリケーションで各メソッドにどれくらい時間がかかっているかを確認できます。 アプリによって処理された要求の詳細なプロファイルがライブ表示され、最も多くの時間がかかっている "ホット パス" が強調表示されます。 また、複数の応答時間が含まれたサンプルが自動的に選択されます。 オーバーヘッドは、さまざまな手法を使用して最小化されます。

このプロファイラーは、現在、Azure App Services で Basic 価格レベル以上で実行されている ASP.NET Web アプリに対して使用できます  

<a id="installation"></a>
## <a name="enable-the-profiler"></a>プロファイラーを有効にする

コードに [Application Insights をインストール](app-insights-asp-net.md)します。 既にインストールされている場合は、バージョンが最新であることを確認します  (確認するには、ソリューション エクスプローラーでプロジェクトを右クリックし、[NuGet パッケージの管理] を選択します。 [更新プログラム] を選択し、すべてのパッケージを更新します)。アプリを再デプロイします。

*ASP.NET Core を使用している場合は、[ここをクリックしてください](#aspnetcore)。*

[https://portal.azure.com](https://portal.azure.com) で、Web アプリの Application Insights リソースを開きます。 **[パフォーマンス]** を開き、**[Application Insights Profiler を有効にする...]** をクリックします。

![Profiler の有効化バナーをクリック][enable-profiler-banner]

または、**[構成]** をいつでもクリックして、状態を表示し、Profiler を有効または無効にすることができます。

![[パフォーマンス] ブレードで [構成] をクリックします。][performance-blade]

Application Insights で構成されている Web アプリは、[構成] ブレードに表示されます。 必要に応じて、手順に従って Profiler エージェントをインストールします。 Application Insights で Web アプリがまだ構成されていない場合は、*[Add Linked Apps (リンクされたアプリの追加)]* をクリックします。

[構成] ブレードの *[Enable Profiler (Profiler を有効にする)]* または *[Disable Profiler (Profiler を無効にする)]* ボタンを使用して、すべてのリンクされた Web アプリの Profiler を管理します。



![[構成] ブレード][linked app services]

個々の App Service インスタンスのプロファイラーを停止または再起動するには、**対象の App Service リソース**の **Web ジョブ**で実行できます。 また、削除する場合は、**[拡張機能]** で実行できます。

![Web ジョブのプロファイラーを無効にする][disable-profiler-webjob]

パフォーマンスの問題をできるだけ早く検出するために、すべての Web アプリで Profiler を有効にすることをお勧めします。

Web アプリケーションに対する変更を WebDeploy を使用してデプロイする場合は、デプロイ中の削除対象から **App_Data** フォルダーを除外してください。 除外しなかった場合、次に Web アプリケーションを Azure にデプロイするときにプロファイラーの拡張機能のファイルが削除されます。

### <a name="using-profiler-with-azure-vms-and-compute-resources-preview"></a>Azure VM および Compute リソースでのプロファイラーの使用 (プレビュー)

[実行時の Azure App Services に Application Insights を有効](app-insights-azure-web-apps.md#run-time-instrumentation-with-application-insights)にした場合、自動的に Profiler が使用できるようになります。 (リソースに既に Application Insights を有効にしている場合は、**[構成]** ウィザードを使用して最新バージョンに更新する必要がある場合があります。)

[Azure Compute リソース用の Profiler のプレビュー版](https://go.microsoft.com/fwlink/?linkid=848155)が利用可能です。


## <a name="limits"></a>制限

データは既定で 5 日間保持されます。 また、1 日に最大 10 GB のデータを取り込むことができます。

プロファイラー サービスの料金は発生しません。 Web アプリは、少なくとも App Services の Basic レベルでホストする必要があります。

## <a name="viewing-profiler-data"></a>プロファイラー データを表示する

[パフォーマンス] ブレードを開き、操作の一覧が表示されるまで下へスクロールします。




![Application Insights の [パフォーマンス] ブレードの [例] 列][performance-blade-examples]

表には次の列が表示されます。

* **カウント** - ブレードに表示された時間の範囲における要求の数。
* **中央値** - アプリが要求に応答するのに要する標準的な時間。 全応答の半数が、これより短い時間で行われました。
* **95 パーセンタイル** - 応答の 95% が、これより短い時間で行われました。 この数値と中央値との差が非常に大きい場合は、アプリに一時的な問題が発生している可能性があります  (または、キャッシュなどの設計特性が原因である可能性があります)。
* **例** - プロファイラーがこの操作のスタック トレースをキャプチャしたことを示すアイコン。

[例] アイコンをクリックすると、トレース エクスプローラーが開きます。 このエクスプローラーには、プロファイラーがキャプチャしたいくつかのサンプルが応答時間ごとに分類されて表示されます。

いずれかのサンプルを選択すると、要求の実行に費やした時間の内訳がコード レベルで表示されます。

![Application Insights トレース エクスプローラー][trace-explorer]

**[ホット パスの表示]** を選択すると、最大リーフ ノード、またはそれに近いノードが表示されます。 ほとんどの場合、このようなノードはパフォーマンスのボトルネックのすぐ近くにあります。



* **ラベル**: 関数またはイベントの名前。 このツリーには、コードと発生したイベント (SQL イベントや http イベント) が混在して表示されます。 最上位のイベントは要求時間全体に相当します。
* **経過時間**: 操作の開始から終了までの期間。
* **When (実行期間)**: 関数/イベントが他の関数に関連して実行された期間を示しています。

## <a name="how-to-read-performance-data"></a>パフォーマンス データの解釈方法

Microsoft サービス プロファイラーでは、アプリケーションのパフォーマンス分析に、サンプリング メソッドとインストルメンテーションを組み合わせて使用します。
詳細情報の収集中、サービス プロファイラーは、コンピューターの各 CPU の命令ポインターをミリ秒単位サンプリングします。
各サンプルでは、実行中のスレッドのコール スタック全体がキャプチャされ、これをもとに抽象化が最も高いレベルと最も低いレベルの両方でそのスレッドが実行した内容に関する有用な詳細情報が取得されます。 また、サービス プロファイラーでは、アクティビティの相関関係と因果関係を追跡するために、他のイベント (コンテキスト スイッチ イベント、TPL イベント、スレッドプール イベントなど) も収集されます。

タイムライン ビューに表示されるコール スタックは、上記のサンプリングとインストルメンテーションの結果です。 スレッドのコール スタック全体がキャプチャされるため、各サンプルには、.NET Framework だけでなく、参照している他のフレームワークのコードも含まれます。

### <a id="jitnewobj"></a>オブジェクトの割り当て (`clr!JIT\_New or clr!JIT\_Newarr1`)
`clr!JIT\_New and clr!JIT\_Newarr1` は、マネージ ヒープからメモリを割り当てる .NET Framework 内部のヘルパー関数です。 `clr!JIT\_New` は、オブジェクトが割り当てられるときに呼び出されます。 `clr!JIT\_Newarr1` は、オブジェクト配列が割り当てられるときに呼び出されます。 これら 2 つの関数は、通常は非常に高速で、比較的短時間で完了します。 タイムライン上で `clr!JIT\_New` または `clr!JIT\_Newarr1` に長い時間がかかっている場合は、コードが多くのオブジェクトを割り当て、大量のメモリを消費している可能性があることを示しています。

### <a id="theprestub"></a>コードの読み込み (`clr!ThePreStub`)
`clr!ThePreStub` は、最初に実行するコードを準備する .NET Framework 内部のヘルパー関数です。 これには一般的に、JIT (Just In Time) コンパイルなどが含まれます (ただし、これに限定されません)。 C# の各メソッドでは、プロセスの有効期間中に `clr!ThePreStub` が最大 1 回呼び出されます。

要求に対して `clr!ThePreStub` がかなりの時間を要している場合は、その要求がこのメソッドを実行する最初の要求であり、.NET Framework ランタイムがメソッドを読み込むのに時間がかかっていることを示しています。 このような場合は、ユーザーがアクセスする前にコードのその部分を実行するウォーミングアップ プロセスを追加するか、アセンブリで NGen を実行することを検討してください。

### <a id="lockcontention"></a>ロックの競合 (`clr!JITutil\_MonContention` or `clr!JITutil\_MonEnterWorker`)
`clr!JITutil\_MonContention` と `clr!JITutil\_MonEnterWorker` は、現在のスレッドが、ロックが解放されるのを待機していることを示しています。 これは通常、C# のロック ステートメントの実行時、Monitor.Enter メソッドの呼び出し時、または MethodImplOptions.Synchronized 属性を持つメソッドの呼び出し時に表示されます。 ロックの競合は一般的に、スレッド A がロックを取得し、それを解放する前にスレッド B が同じロックを取得しようとすると発生します。

### <a id="ngencold"></a>コードの読み込み (`[COLD]`)
`mscorlib.ni![COLD]System.Reflection.CustomAttribute.IsDefined` のように、メソッド名に `[COLD]` が含まれている場合は、.NET Framework ランタイムが<a href="https://msdn.microsoft.com/library/e7k32f4k.aspx">ガイド付き最適化のプロファイル</a>によって最適化されていないコードを初めて実行していることを示しています。 これが表示されるのは、プロセスの有効期間中、メソッドごとに最大 1 回です。

要求に対してコードの読み込みがかなりの時間を要している場合は、その要求がこのメソッドの最適化されていない部分を実行する最初の要求であることを示しています。 このような場合は、ユーザーがアクセスする前にコードのその部分を実行するウォーミングアップ プロセスを追加することを検討してください。

### <a id="httpclientsend"></a>HTTP 要求の送信
`HttpClient.Send` などのメソッドは、コードが HTTP 要求が完了するのを待機していることを示しています。

### <a id="sqlcommand"></a>データベース操作
SqlCommand.Execute などのメソッドは、コードがデータベース操作が完了するのを待機していることを示しています。

### <a id="await"></a>待機 (`AWAIT\_TIME`)
`AWAIT\_TIME` は、コードが別のタスクが完了するのを待機していることを示しています。 これは通常、C# の "await" ステートメントで発生します。 コードで C# の "await" が実行されると、スレッドはアンワインドして、スレッドプールの制御を戻します。"await" が終了するのを待機している間にスレッドがブロックされることはありません。 ただし、論理的に見れば、"await" を実行したスレッドは、操作の完了を待機している間 "ブロック" されます。 `AWAIT\_TIME` は、タスクが完了するのを待機しているブロック時間を示しています。

### <a id="block"></a>ブロック時間
`BLOCKED_TIME` は、別のリソースが使用可能になるのをコードが待機していることを示しています。たとえば、同期オブジェクトを待機している、スレッドが使用可能性になるのを待機している、要求が完了するのを待機している、などが該当します。

### <a id="cpu"></a>CPU 時間
命令の実行中のため、CPU がビジー状態です。

### <a id="disk"></a>ディスク時間
アプリケーションがディスク操作を実行しています。

### <a id="network"></a>ネットワーク時間
アプリケーションがネットワーク操作を実行しています。

### <a id="when"></a>[When (実行期間)] 列
これは、ノードについて収集された包括的なサンプルが時間の経過と共にどのように変化するかを視覚化したものです。 要求の全範囲が 32 個のタイム バケットに分割され、対象ノードの包括的なサンプルが 32 個のバケットに蓄積されます。 各バケットはバーとして表されます。バーの長さは、調整された値を表しています。 ノードに `CPU_TIME` または `BLOCKED_TIME` と表示されている場合、リソース (CPU、ディスク、スレッド) の消費という明確な関係があるため、バーはそのバケットの期間にリソースのいずれかを消費していることを表しています。 これらのメトリックでは、複数のリソースを消費することによって値が 100% を超える場合があります。 たとえば、特定の期間に平均して 2 つの CPU を使用する場合、CPU 使用率は 200% になります。


## <a id="troubleshooting"></a>トラブルシューティング

### <a name="how-can-i-know-whether-application-insights-profiler-is-running"></a>Application Insights プロファイラーが実行されているかどうかを確認するにはどうすればよいですか。

プロファイラーは、Web アプリで継続的な Web ジョブとして実行されます。 https://portal.azure.com で Web アプリ リソースを開き、[WebJobs] ブレードで "ApplicationInsightsProfiler" の状態を確認できます。 実行されていない場合は、**[ログ]** を開いて詳細を確認できます。

### <a name="why-cant-i-find-any-stack-examples-even-though-the-profiler-is-running"></a>プロファイラーが実行されているのにスタック サンプルが見つからないのはなぜですか。

次の点を確認してください。

1. Web App Service プランが Basic レベルかそれ以上であることを確認します。
2. Web アプリで Application Insights SDK 2.2 Beta かそれ以上が有効になっていることを確認します。
3. Web アプリの APPINSIGHTS_INSTRUMENTATIONKEY 設定に、Application Insights SDK で使用されているのと同じインストルメンテーション キーが使用されていることを確認します。
4. Web アプリが .Net Framework 4.6 で動作していることを確認します。
5. ASP.NET Core アプリケーションの場合は、[必須の依存関係](#aspnetcore)があるかどうかも確認してください。

プロファイラーが起動されると、短いウォーミングアップ時間中に複数のパフォーマンス トレースが高頻度で収集されます。 その後は、1 時間ごとに 2 分間パフォーマンス トレースが収集されます。  

### <a name="i-was-using-azure-service-profiler-what-happened-to-it"></a>これまで Azure サービス プロファイラーを使用していましたが、 それはどうなったのでしょうか。  

Application Insights プロファイラーを有効にすると、Azure サービス プロファイラー エージェントは無効になります。

### <a id="double-counting"></a>並列スレッドの二重カウント

スタック ビューアーに表示される合計時間メトリックが実際の要求期間よりも長くなる場合があります。

これは、要求に関連する 2 つ以上のスレッドが並行して動作している場合に発生します。 このとき、スレッド時間全体は経過時間よりも長くなります。 多くの場合、あるスレッドが他のスレッドが完了するのを待機している可能性があります。 ビューアーではこれを検出して不要な待機を省略しようとしますが、重要かもしれない情報は省略しないようにするため、結果として情報が過剰になる傾向があります。  

トレースに並列スレッドがある場合は、要求のクリティカル パスを特定できるように、どのスレッドが待機しているかを判別する必要があります。 通常は、すぐに待機状態になるスレッドが他のスレッドを待機しています。 他のスレッドのほうに注目し、待機しているスレッドの時間は無視してください。

### <a id="issue-loading-trace-in-viewer"></a>プロファイリング データがない

1. 表示しようとしているデータが 2 週間以上前のものである場合は、時間フィルターを絞り込んで、もう一度試します。

2. プロキシまたはファイアウォールが https://gateway.azureserviceprofiler.net へのアクセスをブロックしていないことを確認します。

3. アプリで使用している Application Insights インストルメンテーション キーが、プロファイリングを有効にしたときに使用した Application Insights リソースと同じであることを確認します。 キーは通常は ApplicationInsights.config にありますが、web.config または app.config にある場合もあります。

### <a name="error-report-in-the-profiling-viewer"></a>プロファイリング ビューアーのエラーの報告

ポータルからサポート チケットを提出してください。 その際、エラー メッセージの関連付け ID を含めてください。

## <a name="manual-installation"></a>手動のインストール

プロファイラーを構成すると、Web アプリの設定が次のように更新されます。 Azure App Service Environment (ASE) でアプリケーションを実行している場合など、環境に応じて手動で設定することもできます。

1. [Web アプリ] コントロール ブレードで、[設定] を開きます。
2. [.Net Framework バージョン] を [v4.6] に設定します。
3. [Always On] を [オン] に設定します。
4. アプリ設定 "__APPINSIGHTS_INSTRUMENTATIONKEY__" を追加し、その値を SDK によって使用されたのと同じインストルメンテーション キーに設定します。
5. [高度なツール] を開きます。
6. [移動] をクリックして、Kudu の Web サイトを開きます。
7. Kudu の Web サイトで、[Site extensions]\(サイトの機能拡張\) を選択します。
8. [Gallery]\(ギャラリー\) から __Application Insights__ をインストールします。
9. Web アプリを再起動します。

## <a id="aspnetcore"></a>ASP.NET Core のサポート

ASP.NET Core アプリケーションでは、Microsoft.ApplicationInsights.AspNetCore Nuget パッケージ 2.1.0-beta6 以上をインストールして、プロファイラーと共に使用する必要があります。 2017 年 6 月 27 日以降は、これより下位のバージョンはサポートされません。

## <a name="next-steps"></a>次のステップ

* [Visual Studio での Application Insights の操作](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[performance-blade]: ./media/app-insights-profiler/performance-blade.png
[performance-blade-examples]: ./media/app-insights-profiler/performance-blade-examples.png
[trace-explorer]: ./media/app-insights-profiler/trace-explorer.png
[trace-explorer-toolbar]: ./media/app-insights-profiler/trace-explorer-toolbar.png
[trace-explorer-hint-tip]: ./media/app-insights-profiler/trace-explorer-hint-tip.png
[trace-explorer-hot-path]: ./media/app-insights-profiler/trace-explorer-hot-path.png
[enable-profiler-banner]: ./media/app-insights-profiler/enable-profiler-banner.png
[disable-profiler-webjob]: ./media/app-insights-profiler/disable-profiler-webjob.png
[linked app services]: ./media/app-insights-profiler/linked-app-services.png

