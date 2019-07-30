---
title: Application Insights Profiler に関する問題のトラブルシューティング | Microsoft Docs
description: この記事では、Application Insights Profiler の有効化または使用で問題が発生している開発者に役立つ、トラブルシューティングの手順と情報を示します。
services: application-insights
documentationcenter: ''
author: cweining
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 08/06/2018
ms.author: cweining
ms.openlocfilehash: 6b57ffbd3cb2b31da3fc2882e941f9788d83fea8
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341672"
---
# <a name="troubleshoot-problems-enabling-or-viewing-application-insights-profiler"></a>Application Insights Profiler の有効化または表示に関する問題のトラブルシューティング

## <a id="troubleshooting"></a>一般的なトラブルシューティング

### <a name="profiles-are-uploaded-only-if-there-are-requests-to-your-application-while-profiler-is-running"></a>Profiler の実行中にアプリケーションへの要求がある場合しか、プロファイルがアップロードされない

Azure Application Insights Profiler では、1 時間ごとに 2 分間だけプロファイリング データが収集されます。 また、 **[Application Insights Profiler の構成]** ウィンドウで **[今すぐプロファイル]** ボタンを選択したときにも、データが収集されます。 ただし、プロファイリング データがアップロードされるのは、Profiler の実行中に発生した要求に添付できる場合のみです。 

Profiler では、トレース メッセージとカスタム イベントが Application Insights リソースに書き込まれます。 これらのイベントを使用して、Profiler がどのように実行されているかを確認できます。 Profiler が実行されていてトレースがキャプチャされているはずなのに、 **[パフォーマンス]** ウィンドウに表示されない場合は、Profiler の実行状態を確認できます。

1. Profiler から Application Insights リソースに送信されたトレース メッセージとカスタム イベントを検索します。 次の検索文字列を使用すると、関連するデータを検索することができます。

    ```
    stopprofiler OR startprofiler OR upload OR ServiceProfilerSample
    ```
    次の図には、2 つの AI リソースからの検索の例が 2 つ示されています。 
    
   * 左側の場合、Profiler は実行されていますが、アプリケーションでは要求が受信されていません。 アクティビティがないためにアップロードが取り消されたことが、メッセージで示されています。 

   * 右側の場合は、Profiler は開始され、その実行中に発生した要求を検出して、カスタム イベントを送信しています。 ServiceProfilerSample カスタム イベントが表示される場合は、Profiler によってトレースが要求に添付されており、**Application Insights の [パフォーマンス]** ウィンドウでトレースを表示できることを意味します。

     テレメトリが表示されない場合、Profiler は実行されていません。 トラブルシューティングについては、この記事の後半にある、特定のアプリの種類ごとのトラブルシューティング セクションをご覧ください。  

     ![Profiler のテレメトリを検索する][profiler-search-telemetry]

1. Profiler の実行中に要求があった場合は、Profiler が有効になっているアプリケーションの部分で要求が処理されることを確認します。 アプリケーションは複数のコンポーネントで構成されていることもありますが、Profiler はコンポーネントの一部に対してのみ有効にされます。 **[Application Insights Profiler の構成]** ウィンドウには、トレースをアップロードしたコンポーネントが表示されます。

### <a name="other-things-to-check"></a>チェックすべきその他の項目
* アプリが .NET Framework 4.6 で動作していることを確認します。
* Web アプリが ASP.NET Core アプリケーションの場合は、ASP.NET Core 2.0 以降が実行されている必要があります。
* 表示しようとしているデータが 2 週間以上前のものである場合は、時間フィルターを絞り込んで、もう一度試します。 トレースは 7 日後に削除されます。
* プロキシまたはファイアウォールが https://gateway.azureserviceprofiler.net へのアクセスをブロックしていないことを確認します。

### <a id="double-counting"></a>並列スレッドの二重カウント

スタック ビューアーに表示される合計時間メトリックが、要求期間よりも長くなる場合があります。

この状況は、ある要求に 2 つ以上のスレッドが関連付けられ、それらが並行して動作している場合に発生することがあります。 この場合、スレッド時間全体は経過時間よりも長くなります。 あるスレッドが、他のスレッドが完了するのを待機している可能性があります。 ビューアーでは、このような状況の検出が試みられ、不要な待機は除外されます。 その際、重大になる可能性がある情報を省略するのではなく、誤って多すぎる情報が表示されます。

トレースに並列スレッドがある場合は、要求のクリティカル パスを特定できるように、どのスレッドが待機しているかを確認してください。 通常は、すぐに待機状態になるスレッドが他のスレッドを待機しています。 他のスレッドに集中し、待機中のスレッドの時間は無視してください。

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
   1. [Kudu](https://blogs.msdn.microsoft.com/cdndevs/2015/04/01/the-kudu-debug-console-azure-websites-best-kept-secret/) に移動します。
   1. **[Tools]\(ツール\)** メニューの **[WebJobs Dashboard]\(WebJobs ダッシュボード\)** を選択します。  
      **[WebJobs]\(WebJobs\)** ウィンドウが開きます。 
   
      ![profiler-webjob]   
   
   1. ログなどの WebJob の詳細を見るには、**ApplicationInsightsProfiler3** リンクを選択します。  
     **[Continuous WebJob Details]\(継続的な WebJob の詳細\)** ウィンドウが開きます。

      ![profiler-webjob-log]

Profiler が動作していない理由がわからない場合は、ログをダウンロードし、チームに送信して支援を受けることができます (serviceprofilerhelp@microsoft.com)。 
    
### <a name="manual-installation"></a>手動のインストール

Profiler を構成すると、Web アプリの設定に対して更新が行われます。 環境で必要な場合は、更新プログラムを手動で適用できます。 1 つの例として、アプリケーションが PowerApps 用の Web Apps 環境で実行されている場合があります。 更新を手動で適用するには:

1. **[Web App Control] \(Web アプリ コントロール)** ウィンドウで、 **[設定]** を開きます。

1. **.NET Framework バージョン**を **v4.6** に設定します。

1. **[Always On]** を**オン**に設定します。
1. 次のアプリ設定を作成します。

    |アプリ設定    | 値    |
    |---------------|----------|
    |APPINSIGHTS_INSTRUMENTATIONKEY         | Application Insights リソースの iKey    |
    |APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
    |DiagnosticServices_EXTENSION_VERSION | ~3 |

### <a name="too-many-active-profiling-sessions"></a>アクティブなプロファイリング セッションが多すぎる

現在、同じサービス プランで実行されている最大 4 つの Azure Web アプリとデプロイ スロットで Profiler を有効にできます。 1 つの App Service プランで 4 つより多くの Web アプリが実行されている場合は、Profiler で *Microsoft.ServiceProfiler.Exceptions.TooManyETWSessionException* がスローされる可能性があります。 Profiler は Web アプリごとに個別に実行され、アプリごとに Event Trace for Windows (ETW) セッションの開始が試みられます。 ただし、一度にアクティブにできる ETW セッションの数には制限があります。 Profiler WebJob では、報告されているアクティブなプロファイリング セッションが多すぎる場合は、一部の Web アプリが別のサービス プランに移動されます。

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootappdatajobs"></a>配置エラー:ディレクトリが空ではありません 'D:\\home\\site\\wwwroot\\App_Data\\jobs'

Profiler が有効になっている Web Apps リソースに Web アプリを再デプロイしている場合は、次のようなメッセージが表示されることがあります。

*ディレクトリが空ではありません 'D:\\home\\site\\wwwroot\\App_Data\\jobs'*

このエラーは、スクリプトまたは Azure DevOps デプロイ パイプラインから Web 配置を実行した場合に発生します。 解決するには、次の追加デプロイ パラメーターを、Web 配置タスクに追加します。

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

こうしたパラメーターにより、Application Insights Profiler で使用されたフォルダーが削除され、再デプロイ プロセスのブロックが解除されます。 これらは、現在実行されている Profiler インスタンスに影響を与えません。

### <a name="how-do-i-determine-whether-application-insights-profiler-is-running"></a>Application Insights Profiler が実行されているかどうかを確認するにはどうすればよいですか。

Profiler は、Web アプリ内の継続的な WebJob として実行されます。 Web アプリのリソースは、[Azure Portal](https://portal.azure.com) で開くことができます。 **[WebJobs]** ウィンドウで、**ApplicationInsightsProfiler** の状態を確認してください。 実行されていない場合は、 **[ログ]** を開いて詳細情報を入手します。

## <a name="troubleshoot-problems-with-profiler-and-azure-diagnostics"></a>Profiler と Azure Diagnostics に関する問題のトラブルシューティング

>**Cloud Services 向けの WAD に付属しているプロファイラーのバグが修正されました。** Cloud Services 向け WAD の最新バージョン (1.12.2.0) は、最近リリースされたすべての App Insights SDK バージョンで動作します。 Cloud Service のホストでは WAD が自動的にアップグレードされますが、即時に行われるわけではありません。 アップグレードを強制するには、サービスを再デプロイするか、ノードを再起動します。

Azure Diagnostics によって Profiler が正しく構成されているかどうかを確認するには、次の 3 つのことを行います。 
1. 1 つ目として、デプロイされている Azure Diagnostics の構成の内容が意図したとおりであることを確認します。 

1. 2 つ目として、Azure Diagnostics によって Profiler のコマンド ラインで適切な iKey が渡されていることを確認します。 

1. 3 つ目として、Profiler は実行されたがエラーが発生したかどうかを、Profiler のログ ファイルで確認します。 

Azure Diagnostics の構成に使用された設定を確認するには:

1. 仮想マシン (VM) にサインインした後、次の場所にあるログ ファイルを開きます。 (ドライブは c: または d: の場合があり、プラグインのバージョンは異なる可能性があります)。

    ```
    c:\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.log  
    ```
    or
    ```
    c:\WindowsAzure\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.log
    ```

1. そのファイルでは、**WadCfg** という文字列を検索して、Azure Diagnostics を構成するために VM に渡された設定を確認できます。 Profiler シンクによって使用された iKey が正しいかどうかを確認できます。

1. Profiler の起動に使用されたコマンド ラインを確認します。 Profiler の起動に使用される引数は、次のファイルにあります。 (ドライブは c: または d:)

    ```
    D:\ProgramData\ApplicationInsightsProfiler\config.json
    ```

1. Profiler のコマンド ラインの iKey が正しいことを確認します。 

1. 前記の *config.json* ファイルで見つかるパスを使用して、Profiler のログ ファイルを確認します。 Profiler で使用されている設定を示すデバッグ情報が表示されます。 また、Profiler の状態とエラー メッセージも表示されます。  

    アプリケーションが要求を受信している間に Profiler が実行された場合は、次のようなメッセージが表示されます。*Activity detected from iKey* (iKey からアクティビティが検出されました)。 

    トレースがアップロードされているときは、次のメッセージが表示されます。*Start to upload trace* (トレースのアップロードを開始します)。 


[profiler-search-telemetry]:./media/profiler-troubleshooting/Profiler-Search-Telemetry.png
[profiler-webjob]:./media/profiler-troubleshooting/Profiler-webjob.png
[profiler-webjob-log]:./media/profiler-troubleshooting/Profiler-webjob-log.png








