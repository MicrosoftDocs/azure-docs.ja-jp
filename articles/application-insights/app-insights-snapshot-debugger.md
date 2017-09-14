---
title: ".NET アプリ向け Azure Application Insights スナップショット デバッガー | Microsoft Docs"
description: "例外が運用 .NET アプリでスローされるときにデバッグ スナップショットが自動的に収集される"
services: application-insights
documentationcenter: 
author: qubitron
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 07/03/2017
ms.author: bwren
ms.translationtype: HT
ms.sourcegitcommit: b6c65c53d96f4adb8719c27ed270e973b5a7ff23
ms.openlocfilehash: bb6c93557ea26bed721315dc82da917e4727b5f9
ms.contentlocale: ja-jp
ms.lasthandoff: 08/17/2017

---
# <a name="debug-snapshots-on-exceptions-in-net-apps"></a>.NET アプリでの例外でのデバッグ スナップショット

例外が発生したとき、実行中の Web アプリケーションからデバッグ スナップショットを自動的に収集できます。 スナップショットには、例外がスローされたときのソース コードと変数の状態が表示されます。 [Azure Application Insights](app-insights-overview.md) のスナップショット デバッガー (プレビュー) により、Web アプリの例外テレメトリが監視されます。 運用環境の問題の診断に必要な情報を入手できるように、スローされる上位の例外に関するスナップショットが収集されます。 [スナップショット コレクター NuGet パッケージ](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector)をアプリケーションに含め、必要に応じて、[ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) にコレクション パラメーターを構成します。 スナップショットが、Application Insights ポータルの[例外](app-insights-asp-net-exceptions.md)に表示されます。

ポータルで [Debug Snapshots (デバッグ スナップショット)] を表示して、コール スタックを表示し、各呼び出しスタック フレームで変数を確認できます。 ソース コードによるデバッグ エクスペリエンスをさらに向上させるには、Visual Studio 2017 Enterprise でスナップショットを開きます。このためには、[Visual Studio 向けのスナップショット デバッガー拡張機能をダウンロード](https://aka.ms/snapshotdebugger)します。

スナップショット コレクションは次のアプリケーションで使用できます。
* .NET Framework 4.5 以降を実行している .NET Framework アプリケーションと ASP.NET アプリケーション。
* Windows 上で動作している .NET core 2.0 アプリケーションと ASP.NET Core 2.0 アプリケーション。

### <a name="configure-snapshot-collection-for-aspnet-applications"></a>ASP.NET アプリケーションのスナップショット コレクションの構成

1. まだ有効にしていない場合は、[Web アプリで Application Insights を有効](app-insights-asp-net.md)にします。

2. [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet パッケージをアプリに含めます。 

3. パッケージが [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) に追加した既定のオプションを確認します。

    ```xml
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
        <!-- The default is true, but you can disable Snapshot Debugging by setting it to false -->
        <IsEnabled>true</IsEnabled>
        <!-- Snapshot Debugging is usually disabled in developer mode, but you can enable it by setting this to true. -->
        <!-- DeveloperMode is a property on the active TelemetryChannel. -->
        <IsEnabledInDeveloperMode>false</IsEnabledInDeveloperMode>
        <!-- How many times we need to see an exception before we ask for snapshots. -->
        <ThresholdForSnapshotting>5</ThresholdForSnapshotting>
        <!-- The maximum number of examples we create for a single problem. -->
        <MaximumSnapshotsRequired>3</MaximumSnapshotsRequired>
        <!-- The maximum number of problems that we can be tracking at any time. -->
        <MaximumCollectionPlanSize>50</MaximumCollectionPlanSize>
        <!-- How often to reset problem counters. -->
        <ProblemCounterResetInterval>06:00:00</ProblemCounterResetInterval>
        <!-- The maximum number of snapshots allowed in one minute. -->
        <SnapshotsPerMinuteLimit>2</SnapshotsPerMinuteLimit>
        <!-- The maximum number of snapshots allowed per day. -->
        <SnapshotsPerDayLimit>50</SnapshotsPerDayLimit>
        </Add>
    </TelemetryProcessors>
    ```

4. スナップショットは、Application Insights にレポートされる例外についてのみ収集されます。 場合によっては (たとえば、.NET プラットフォームの古いバージョン)、[例外コレクションを構成](app-insights-asp-net-exceptions.md#exceptions)して、ポータルでスナップショット付きの例外を表示する必要があります。


### <a name="configure-snapshot-collection-for-aspnet-core-20-applications"></a>ASP.NET Core 2.0 アプリケーションのスナップショット コレクションの構成

1. まだ有効にしていない場合は、[ASP.NET Core Web アプリで Application Insights を有効](app-insights-asp-net-core.md)にします。

2. [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet パッケージをアプリに含めます。

3. `ConfigureServices`アプリケーションのクラスのメソッド`Startup`を変更し、スナップショット コレクターのテレメトリ プロセッサを追加します。 追加する必要があるコードは、Microsoft.ApplicationInsights.ASPNETCore NuGet パッケージの参照されるバージョンによって異なります。

   Microsoft.ApplicationInsights.AspNetCore 2.1.0 の場合、次を追加します。
   ```C#
   using Microsoft.ApplicationInsights.SnapshotCollector;
   ...
   class Startup
   {
       // This method is called by the runtime. Use it to add services to the container.
       public void ConfigureServices(IServiceCollection services)
       {
           services.AddSingleton<Func<ITelemetryProcessor, ITelemetryProcessor>>(next => new SnapshotCollectorTelemetryProcessor(next));
           // TODO: Add any other services your application needs here.
       }
   }
   ```

   Microsoft.ApplicationInsights.AspNetCore 2.1.1 の場合、次を追加します。
   ```C#
   using Microsoft.ApplicationInsights.SnapshotCollector;
   ...
   class Startup
   {
       private class SnapshotCollectorTelemetryProcessorFactory : ITelemetryProcessorFactory
       {
           public ITelemetryProcessor Create(ITelemetryProcessor next) =>
               new SnapshotCollectorTelemetryProcessor(next);
       }

       // This method is called by the runtime. Use it to add services to the container.
       public void ConfigureServices(IServiceCollection services)
       {
            services.AddSingleton<ITelemetryProcessorFactory>(new SnapshotCollectorTelemetryProcessorFactory());
           // TODO: Add any other services your application needs here.
       }
   }
   ```

### <a name="configure-snapshot-collection-for-other-net-applications"></a>その他の .NET アプリケーションのスナップショット コレクションの構成

1. アプリケーションがまだ Application Insights を使用してインストルメント化されていない場合、まず[Application Insights を有効にし、インストルメンテーション キーを設定](app-insights-windows-desktop.md)します。

2. [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet パッケージをアプリに追加します。

3. スナップショットは、Application Insights にレポートされる例外についてのみ収集されます。 例外をレポートするようにコードを変更する必要があります。 例外処理コードは、アプリケーションの構造によって異なります。次に例を示します。
    ```C#
   TelemetryClient _telemetryClient = new TelemetryClient();

   void ExampleRequest()
   {
        try
        {
            // TODO: Handle the request.
        }
        catch (Exception ex)
        {
            // Report the exception to Application Insights.
            _telemetryClient.TrackException(ex);

            // TODO: Rethrow the exception if desired.
        }
   }
    ```
    
## <a name="grant-permissions"></a>アクセス許可を付与する

Azure サブスクリプションの所有者は、スナップショットを検査できます。 他のユーザーは、所有者から権限を付与してもらう必要があります。

権限を付与するには、スナップショットを検査する予定のユーザーに `Application Insights Snapshot Debugger` のロールを割り当てます。 このロールは、Application Insights のターゲット リソース、リソース グループ、またはサブスクリプションに関するサブスクリプション所有者が、個々のユーザーまたはグループに割り当てることができます。

1. [アクセス制御] \(IAM) ブレードを開きます。
1. [+ 追加] をクリックします。
1. [ロール] ボックスの一覧の [Application Insights スナップショット デバッガー] を選択します。
1. 追加するユーザーの名前を探して入力します。
1. [保存] ボタンをクリックして、ユーザーをロールに追加します。


[!IMPORTANT]
    スナップショットは、変数とパラメーターの値に個人情報などの機密情報を含んでいる可能性があります。

## <a name="debug-snapshots-in-the-application-insights-portal"></a>Application Insights ポータルのデバッグ スナップショット

特定の例外または問題 ID のスナップショットが使用できるようになると、**[デバッグ スナップショットを開く]** ボタンが Application Insights ポータルの [[例外]](app-insights-asp-net-exceptions.md) に表示されます。

![例外の [Debug Snapshot (デバッグ スナップショット)] ボタンを開く](./media/app-insights-snapshot-debugger/snapshot-on-exception.png)

デバッグ スナップショット ビューには、コール スタックと変数ウィンドウが表示されます。 コール スタック ウィンドウでコール スタックのフレームを選択すると、変数ウィンドウでその関数呼び出しのローカル変数とパラメーターを表示できます。

![ポータルでのデバッグ スナップショットの表示](./media/app-insights-snapshot-debugger/open-snapshot-portal.png)

スナップショットには機密情報が含まれている可能性があるため、既定では非表示になっています。 スナップショットを表示するには、`Application Insights Snapshot Debugger` のロールを割り当てられている必要があります。

## <a name="debug-snapshots-with-visual-studio-2017-enterprise"></a>Visual Studio 2017 Enterprise でのデバッグ スナップショット
1. **[Download Snapshot]\(スナップショットのダウンロード\)** をクリックして `.diagsession` ファイルをダウンロードします。このファイルは Visual Studio 2017 Enterprise で開くことができます。 

2. `.diagsession` ファイルを開くには、まず [Visual Studio 用のスナップショット デバッガー拡張機能をダウンロードしてインストール](https://aka.ms/snapshotdebugger)する必要があります。

3. スナップショット ファイルを開くと、Visual Studio の[ミニダンプ デバッグ] ページが表示されます。 **[Debug Managed Code]\(マネージ コードをデバッグする\)** をクリックして、スナップショットのデバッグを開始します。 例外がスローされたコード行がスナップショットに表示され、プロセスの現在の状態をデバッグできます。

    ![Visual Studio でのデバッグ スナップショットの表示](./media/app-insights-snapshot-debugger/open-snapshot-visualstudio.png)

ダウンロードしたスナップショットには、Web アプリケーション サーバーで検出されたすべてのシンボル ファイルが含まれます。 これらのシンボル ファイルは、スナップショット データをソース コードと関連付けるために必要です。 App Service アプリの場合は、Web アプリを公開するときにシンボル デプロイを有効にしてください。

## <a name="how-snapshots-work"></a>スナップショットのしくみ

アプリケーションの開始時に、スナップショット要求についてアプリケーションを監視する、個別のスナップショット アップローダー プロセスが作成されます。 スナップショットが要求されると、実行中のプロセスのシャドウ コピーが約 10 分 から 20 分で実行されます。 次に、シャドウ プロセスが分析され、メイン プロセスがユーザーへのトラフィックの実行と提供を続ける間にスナップショットが作成されます。 次に、スナップショットの表示に必要な関連するシンボル (.pdb) ファイルと共にスナップショットが Application Insights にアップロードされます。

## <a name="current-limitations"></a>現時点での制限事項

### <a name="publish-symbols"></a>シンボルの公開
スナップショット デバッガーでは、Visual Studio で変数をデコードし、デバッグ エクスペリエンスを提供するために、運用サーバーにシンボル ファイルが必要です。 Visual Studio 2017 の 15.2 リリースでは、App Service に公開する際に、既定でリリース ビルドのシンボルを公開します。 以前のバージョンでは、シンボルがリリース モードで公開されるように、発行プロファイルの `.pubxml` ファイルに次の行を追加する必要があります。

```xml
    <ExcludeGeneratedDebugSymbol>False</ExcludeGeneratedDebugSymbol>
```

Azure Compute や他の種類の場合、シンボル ファイルがメイン アプリケーション .dll (通常は `wwwroot/bin`) の同じフォルダーにあるか、現在のパスで使用できる必要があります。

### <a name="optimized-builds"></a>最適化されたビルド
場合によっては、ビルド プロセスで適用される最適により、ローカル変数がリリース ビルドに表示できません。

## <a name="troubleshooting"></a>トラブルシューティング

次のヒントは、スナップショット デバッガーの問題のトラブルシューティングに役立ちます。

### <a name="verify-the-instrumentation-key"></a>インストルメンテーション キーの確認

公開したアプリケーションで、正しいインストルメンテーション キーを使用していることを確認します。 通常、Application Insights は、ApplicationInsights.config ファイルからインストルメンテーション キーを読み取ります。 値が、ポータルに表示される Application Insights リソースのインストルメンテーション キーと同じであることを確認します。

### <a name="check-the-uploader-logs"></a>アップローダー ログの確認

スナップショットの作成後、ミニダンプ ファイル (.dmp) がディスク上に作成されます。 個別アップローダー プロセスでは、そのミニダンプ ファイルを取得し、これを関連する PDB と共に Application Insights のスナップショット デバッガーのストレージにアップロードします。 ミニダンプは、正常にアップロードされた後、ディスクから削除されます。 ミニダンプ アップローダーのログ ファイルは、ディスク上に保持されます。 App Service 環境では、これらのログは `D:\Home\LogFiles\Uploader_*.log` にあります。 App Service の Kudu 管理サイトを使用すると、これらのログ ファイルを検索できます。

1. Azure Portal で App Service アプリケーションを開きます。

2. **[Advanced Tools]**(高度なツール) ブレードを選択するか、**Kudu** を検索します。
3. **[Go]** をクリックします。
4. **[Debug console]**(デバッグ コンソール) ドロップダウン リスト ボックスで、**[CMD]** を選択します。
5. **[LogFiles]** をクリックします。

`Uploader_` で始まる名前で拡張子が `.log` であるファイルが 1 つ以上表示されます。 適切なアイコンをクリックしてログ ファイルをダウンロードするか、またはブラウザーでログ ファイルを開きます。
ファイル名には、コンピューター名が含まれています。 App Service のインスタンスが 1 つ以上のコンピューターでホストされている場合は、コンピューターごとに個別のログ ファイルがあります。 アップローダーは、新しいミニダンプ ファイルを検出すると、ログ ファイルに記録します。 アップロードの成功の例を次に示します。

```
MinidumpUploader.exe Information: 0 : Dump available 139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2017-05-25T14:25:08.0349846Z
MinidumpUploader.exe Information: 0 : Uploading D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp, 329.12 MB
    DateTime=2017-05-25T14:25:16.0145444Z
MinidumpUploader.exe Information: 0 : Upload successful.
    DateTime=2017-05-25T14:25:42.9164120Z
MinidumpUploader.exe Information: 0 : Extracting PDB info from D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp.
    DateTime=2017-05-25T14:25:42.9164120Z
MinidumpUploader.exe Information: 0 : Matched 2 PDB(s) with local files.
    DateTime=2017-05-25T14:25:44.2310982Z
MinidumpUploader.exe Information: 0 : Stamp does not want any of our matched PDBs.
    DateTime=2017-05-25T14:25:44.5435948Z
MinidumpUploader.exe Information: 0 : Deleted D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2017-05-25T14:25:44.6095821Z
```

インストルメンテーション キーは、前の例では、`c12a605e73c44346a984e00000000000` です。 この値は、アプリケーションのインストルメンテーション キーと一致する必要があります。
ミニダンプは ID `139e411a23934dc0b9ea08a626db16c5` を持つスナップショットに関連付けられています。 この ID は、Application Insights Analytics で関連する例外テレメトリを検索するために後で使用できます。

アップローダーは、約 15 分ごとに 1 回新しい PDB をスキャンします。 次に例を示します。

```
MinidumpUploader.exe Information: 0 : PDB rescan requested.
    DateTime=2017-05-25T15:11:38.8003886Z
MinidumpUploader.exe Information: 0 : Scanning D:\home\site\wwwroot\ for local PDBs.
    DateTime=2017-05-25T15:11:38.8003886Z
MinidumpUploader.exe Information: 0 : Scanning D:\local\Temporary ASP.NET Files\root\a6554c94\e3ad6f22\assembly\dl3\81d5008b\00b93cc8_dec5d201 for local PDBs.
    DateTime=2017-05-25T15:11:38.8160276Z
MinidumpUploader.exe Information: 0 : Local PDB scan complete. Found 2 PDB(s).
    DateTime=2017-05-25T15:11:38.8316450Z
MinidumpUploader.exe Information: 0 : Deleted PDB scan marker D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\.pdbscan.
    DateTime=2017-05-25T15:11:38.8316450Z
```

App Service にホストされて_いない_アプリケーションでは、アップローダー ログは、ミニダンプと同じフォルダー `%TEMP%\Dumps\<ikey>` にあります (`<ikey>` はインストルメンテーション キー)。

### <a name="use-application-insights-search-to-find-exceptions-with-snapshots"></a>Application Insights 検索を使用してスナップショット付きの例外を検索する

スナップショットが作成されている場合、例外がスローされるとスナップショット ID がタグ付けされます。 Application Insights に例外テレメトリがレポートされると、そのスナップショット ID はカスタム プロパティとして含まれます。 Application Insights の [検索] ブレードを使用すると、`ai.snapshot.id` カスタム プロパティを使用してすべてのテレメトリを見つけることができます。

1. Azure Portal の Application Insights のリソースを参照します。
2. **[Search (検索)]**をクリックします。
3. [検索] テキスト ボックスに `ai.snapshot.id` と入力し、Enter キーを押します。

![ポータルでスナップショット ID を使用してテレメトリを検索](./media/app-insights-snapshot-debugger/search-snapshot-portal.png)

この検索に結果が返されない場合、アプリケーションが選択した時間範囲に Application Insights にスナップショットをレポートしなかったということです。

アップローダー ログから特定のスナップショット ID を検索するには、[検索] ボックスにその ID を入力します。 既知のスナップショットのテレメトリを検索できない場合、次の手順に従います。

1. インストルメンテーション キーを確認することにより、正しい Application Insights のリソースを探していることを再確認します。

2. アップローダー ログからのタイムスタンプを使用して、その時間範囲をカバーするように検索の時間範囲フィルターを調整します。

それでもそのスナップショット ID を持つ例外が表示されない場合、その例外テレメトリが Application Insights にレポートされなかったということです。 このような状況は、スナップショットを取得した後かつ例外テレメトリにレポートする前にアプリケーションがクラッシュした場合に発生する可能性があります。 この場合、`Diagnose and solve problems` で App Service を確認し、予期しない再起動またはハンドルされない例外があったかどうかを確認します。

## <a name="next-steps"></a>次のステップ

* [コードでスナップポイントを設定](https://azure.microsoft.com/blog/snapshot-debugger-for-azure/)し、例外を待たずにスナップショットを取得します。
* [Web アプリの例外の診断](app-insights-asp-net-exceptions.md)に関する記事では、Application Insights に表示される例外を増やす方法を説明しています。 
* [スマート検出](app-insights-proactive-diagnostics.md)は、パフォーマンスの異常を自動的に検出します。

