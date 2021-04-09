---
title: Azure Application Insights Profiler に関する問題のトラブルシューティング
description: この記事では、開発者が Application Insights Profiler を有効にして使用するのに役立つトラブルシューティングの手順と情報を示します。
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 622a83c6d91bf2a30c2844e3279d6fd4b89d429f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102213795"
---
# <a name="troubleshoot-problems-enabling-or-viewing-application-insights-profiler"></a>Application Insights Profiler の有効化または表示に関する問題のトラブルシューティング

## <a name="general-troubleshooting"></a><a id="troubleshooting"></a>一般的なトラブルシューティング

### <a name="make-sure-youre-using-the-appropriate-profiler-endpoint"></a>必ず適切なプロファイラー エンドポイントを使用してください

現在、エンドポイントの変更が必要なリージョンは [Azure Government](https://docs.microsoft.com/azure/azure-government/compare-azure-government-global-azure#application-insights) と [Azure China](https://docs.microsoft.com/azure/china/resources-developer-guide) のみです。

|アプリ設定    | 米国政府のクラウド | China Cloud |   
|---------------|---------------------|-------------|
|ApplicationInsightsProfilerEndpoint         | `https://profiler.monitor.azure.us`    | `https://profiler.monitor.azure.cn` |
|ApplicationInsightsEndpoint | `https://dc.applicationinsights.us` | `https://dc.applicationinsights.azure.cn` |

### <a name="profiles-are-uploaded-only-if-there-are-requests-to-your-application-while-profiler-is-running"></a>Profiler の実行中にアプリケーションへの要求がある場合しか、プロファイルがアップロードされない

Azure Application Insights Profiler では、1 時間ごとに 2 分間だけデータが収集されます。 また、 **[Application Insights Profiler の構成]** ペインで **[今すぐプロファイル]** ボタンを選択したときにも、データを収集できます。

> [!NOTE]
> プロファイリング データがアップロードされるのは、Profiler の実行中に発生した要求に添付できる場合のみです。 

Profiler では、トレース メッセージとカスタム イベントが Application Insights リソースに書き込まれます。 これらのイベントを使用して、Profiler がどのように実行されているかを確認できます。

1. Profiler から Application Insights リソースに送信されたトレース メッセージとカスタム イベントを検索します。 次の検索文字列を使用すると、関連するデータを検索することができます。

    ```
    stopprofiler OR startprofiler OR upload OR ServiceProfilerSample
    ```
    次の図には、2 つの AI リソースからの検索の例が 2 つ示されています。 
    
   * 左側の場合、Profiler は実行されていますが、アプリケーションでは要求が受信されていません。 アクティビティがないためにアップロードが取り消されたことが、メッセージで示されています。 

   * 右側の場合は、Profiler は開始され、その実行中に発生した要求を検出して、カスタム イベントを送信しています。 `ServiceProfilerSample` カスタムイベントが表示されている場合は、プロファイルがキャプチャされており、 **[Application Insights パフォーマンス]** ペインで使用できることを意味します。

     レコードが表示されていない場合は、Profiler は実行されていません。 トラブルシューティングについては、この記事の後半にある、特定のアプリの種類ごとのトラブルシューティング セクションをご覧ください。  

     ![Profiler のテレメトリを検索する][profiler-search-telemetry]

### <a name="other-things-to-check"></a>チェックすべきその他の項目
* アプリが .NET Framework 4.6 で動作していることを確認します。
* Web アプリが ASP.NET Core アプリケーションの場合は、ASP.NET Core 2.0 以降が実行されている必要があります。
* 表示しようとしているデータが 2 週間以上前のものである場合は、時間フィルターを絞り込んで、もう一度試します。 トレースは 7 日後に削除されます。
* プロキシまたはファイアウォールによって https://gateway.azureserviceprofiler.net へのアクセスがブロックされていないことを確認します。
* Profiler は、Free または Shared App Service プランではサポートされていません。 これらのプランのいずれかを使用している場合は、Basic プランのいずれかにスケールアップすると、Profiler が動作を開始します。

### <a name="double-counting-in-parallel-threads"></a><a id="double-counting"></a>並列スレッドの二重カウント

スタック ビューアーに表示される合計時間メトリックが、要求期間よりも長くなる場合があります。

この状況は、1 つの要求に 2 つ以上の並列スレッドが関連付けられている場合に発生することがあります。 この場合、スレッド時間全体は経過時間よりも長くなります。

あるスレッドが、他のスレッドが完了するのを待機している可能性があります。 ビューアーでは、このような状況の検出が試みられ、不要な待機は除外されます。 その際、重大になる可能性がある情報を省略するのではなく、誤って多すぎる情報が表示されます。

トレースに並列スレッドがある場合は、要求のホット パスを特定できるように、どのスレッドが待機しているかを確認してください。

通常は、すぐに待機状態になるスレッドが他のスレッドを待機しています。 他のスレッドに集中し、待機中のスレッドの時間は無視してください。

### <a name="error-report-in-the-profile-viewer"></a>プロファイル ビューアーのエラーの報告
ポータルでサポート チケットを送信してください。 エラー メッセージの関連付け ID を必ず含めてください。

## <a name="troubleshoot-profiler-on-azure-app-service"></a>Azure App Service での Profiler のトラブルシューティング

Profiler を正常に動作させるためには:
* Web アプリ サービス プランは Basic レベル以上である必要があります。
* Web アプリで Application Insights が有効になっている必要があります。
* Web アプリには、次のアプリ設定が必要です。

    |アプリ設定    | 値    |
    |---------------|----------|
    |APPINSIGHTS_INSTRUMENTATIONKEY         | Application Insights リソースの iKey    |
    |APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
    |DiagnosticServices_EXTENSION_VERSION | ~3 |


* **ApplicationInsightsProfiler3** WebJob が実行されている必要があります。 WebJob を確認するには:
   1. [Kudu](/archive/blogs/cdndevs/the-kudu-debug-console-azure-websites-best-kept-secret) に移動します。
   1. **[Tools]\(ツール\)** メニューの **[WebJobs Dashboard]\(WebJobs ダッシュボード\)** を選択します。  
      **[WebJobs]\(WebJobs\)** ウィンドウが開きます。 
   
      ![ジョブの名前、状態、および最終実行時刻が表示された [WebJobs] ウィンドウを表示するスクリーンショット。][profiler-webjob]   
   
   1. ログなどの WebJob の詳細を見るには、**ApplicationInsightsProfiler3** リンクを選択します。  
     **[Continuous WebJob Details]\(継続的な WebJob の詳細\)** ウィンドウが開きます。

      ![[Continuous WebJob Details]\(継続的な WebJob の詳細\) ウィンドウを表示するスクリーンショット。][profiler-webjob-log]

Profiler が動作していない場合は、ログをダウンロードし、Microsoft のチームに送信して支援を受けることができます (serviceprofilerhelp@microsoft.com)。

### <a name="check-the-diagnostic-services-site-extension-status-page"></a>診断サービスのサイト拡張機能の状態ページを確認する
Profiler がポータルの[[Application Insights] ペイン](profiler.md)から有効にされた場合は、診断サービスのサイト拡張機能によって有効にされています。

> [!NOTE]
> Application Insights Profiler のコードなしインストールは .NET Core サポート ポリシーに準拠します。
> サポートされているランタイムの詳細については、[.Net Core サポート ポリシー](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)に関するページを参照してください。

この拡張機能の状態ページを確認するには、次の URL に移動します: `https://{site-name}.scm.azurewebsites.net/DiagnosticServices`

> [!NOTE]
> 状態ページ リンクのドメインは、クラウドによって異なります。
このドメインは App Service の Kudu 管理サイトと同じです。

この状態ページには、Profiler と Snapshot Collector エージェントのインストール状態が表示されます。 予期しないエラーが発生した場合は、それが表示され、その修正方法が示されます。

App Service の Kudu 管理サイトを使用して、この状態ページのベース URL を取得できます。
1. Azure Portal で App Service アプリケーションを開きます。
2. **[高度なツール]** を選択するか、「**Kudu**」を検索します。
3. **[Go] \(移動)** を選択します。
4. Kudu 管理サイトが表示されたら、URL に **`/DiagnosticServices` を追加して、Enter キーを押します**。
 最終的には次のようになります: `https://<kudu-url>/DiagnosticServices`

次のような状態ページが表示されます。![診断サービスの状態ページ](./media/diagnostic-services-site-extension/status-page.png)
    
### <a name="manual-installation"></a>手動のインストール

Profiler を構成すると、Web アプリの設定に対して更新が行われます。 環境で必要な場合は、更新プログラムを手動で適用できます。 1 つの例として、アプリケーションが PowerApps 用の Web Apps 環境で実行されている場合があります。 更新を手動で適用するには:

1. **[Web App Control] \(Web アプリ コントロール)** ウィンドウで、 **[設定]** を開きます。

1. **.NET Framework バージョン** を **v4.6** に設定します。

1. **[Always On]** を **オン** に設定します。
1. 次のアプリ設定を作成します。

    |アプリ設定    | 値    |
    |---------------|----------|
    |APPINSIGHTS_INSTRUMENTATIONKEY         | Application Insights リソースの iKey    |
    |APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
    |DiagnosticServices_EXTENSION_VERSION | ~3 |

### <a name="too-many-active-profiling-sessions"></a>アクティブなプロファイリング セッションが多すぎる

同じサービス プランで実行されている最大 4 つの Azure Web アプリで Profiler を有効にできます。 4 つより多い場合は、Profiler によって *Microsoft.ServiceProfiler.Exceptions.TooManyETWSessionException* がスローされる可能性があります。 これを解決するには、一部の Web アプリを別のサービス プランに移動します。

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootapp_datajobs"></a>配置エラー:ディレクトリが空ではありません 'D:\\home\\site\\wwwroot\\App_Data\\jobs'

Profiler が有効になっている Web Apps リソースに Web アプリを再デプロイしている場合は、次のようなメッセージが表示されることがあります。

*ディレクトリが空ではありません 'D:\\home\\site\\wwwroot\\App_Data\\jobs'*

このエラーは、スクリプトまたは Azure Pipelines から Web 配置を実行した場合に発生します。 解決するには、次のデプロイ パラメーターを、Web 配置タスクに追加します。

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

こうしたパラメーターにより、Application Insights Profiler で使用されたフォルダーが削除され、再デプロイ プロセスのブロックが解除されます。 これらは、現在実行されている Profiler インスタンスに影響を与えません。

### <a name="how-do-i-determine-whether-application-insights-profiler-is-running"></a>Application Insights Profiler が実行されているかどうかを確認するにはどうすればよいですか。

Profiler は、Web アプリ内の継続的な WebJob として実行されます。 Web アプリのリソースは、[Azure Portal](https://portal.azure.com) で開くことができます。 **[WebJobs]** ウィンドウで、**ApplicationInsightsProfiler** の状態を確認してください。 実行されていない場合は、 **[ログ]** を開いて詳細情報を入手します。

## <a name="troubleshoot-vms-and-cloud-services"></a>VM と Cloud Services のトラブルシューティング

>**Cloud Services 向けの WAD に付属しているプロファイラーのバグが修正されました。** Cloud Services 向け WAD の最新バージョン (1.12.2.0) は、最近リリースされたすべての App Insights SDK バージョンで動作します。 Cloud Service のホストでは WAD が自動的にアップグレードされますが、即時に行われるわけではありません。 アップグレードを強制するには、サービスを再デプロイするか、ノードを再起動します。

Azure Diagnostics によって Profiler が正しく構成されているかどうかを確認するには、次の手順を実行します。 
1. デプロイされた Azure Diagnostics 構成の内容が予期したとおりであることを確認します。 

1. 2 つ目として、Azure Diagnostics によって Profiler のコマンド ラインで適切な iKey が渡されていることを確認します。 

1. 3 つ目として、Profiler は実行されたがエラーが発生したかどうかを、Profiler のログ ファイルで確認します。 

Azure Diagnostics の構成に使用された設定を確認するには:

1. 仮想マシン (VM) にサインインした後、次の場所にあるログ ファイルを開きます。 マシン上のプラグインの方がより新しいバージョンである場合があります。
    
    VM の場合:
    ```
    c:\WindowsAzure\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.log
    ```
    
    Cloud Services の場合:
    ```
    c:\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.log  
    ```

1. そのファイルでは、**WadCfg** という文字列を検索して、Azure Diagnostics を構成するために VM に渡された設定を確認できます。 Profiler シンクによって使用された iKey が正しいかどうかを確認できます。

1. Profiler の起動に使用されたコマンド ラインを確認します。 Profiler の起動に使用される引数は、次のファイルにあります。 (ドライブは c: または d: である可能性があり、ディレクトリは非表示である可能性があります)。

    VM の場合:
    ```
    C:\ProgramData\ApplicationInsightsProfiler\config.json
    ```
    
    Cloud Services の場合:
    ```
    D:\ProgramData\ApplicationInsightsProfiler\config.json
    ```

1. Profiler のコマンド ラインの iKey が正しいことを確認します。 

1. 前記の *config.json* ファイルで見つかるパスを使用して、**BootstrapN.log** という名前の Profiler のログ ファイルを確認します。 Profiler で使用されている設定を示すデバッグ情報が表示されます。 また、Profiler の状態とエラー メッセージも表示されます。  

    VM の場合、ファイルはこちらにあります。
    ```
    C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\1.17.0.6\ApplicationInsightsProfiler
    ```

    Cloud Services の場合:
    ```
    C:\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\1.17.0.6\ApplicationInsightsProfiler
    ```

    アプリケーションが要求を受信している間に Profiler が実行された場合は、次のようなメッセージが表示されます。*Activity detected from iKey* (iKey からアクティビティが検出されました)。 

    トレースがアップロードされているときは、次のメッセージが表示されます。*Start to upload trace* (トレースのアップロードを開始します)。 


## <a name="edit-network-proxy-or-firewall-rules"></a>ネットワーク プロキシまたはファイアウォール規則を編集する

アプリケーションがプロキシまたはファイアウォール経由でインターネットに接続される場合は、Profile サービスと通信するように規則を更新する必要がある場合があります。

Application Insights Profiler によって使用される IP は、Azure Monitor サービス タグに含まれています。 詳細については、[サービス タグに関するドキュメント](../../virtual-network/service-tags-overview.md)を参照してください。


[profiler-search-telemetry]:./media/profiler-troubleshooting/Profiler-Search-Telemetry.png
[profiler-webjob]:./media/profiler-troubleshooting/Profiler-webjob.png
[profiler-webjob-log]:./media/profiler-troubleshooting/Profiler-webjob-log.png