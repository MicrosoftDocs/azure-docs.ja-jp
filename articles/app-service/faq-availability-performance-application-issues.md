---
title: アプリケーションのパフォーマンスに関する FAQ
description: Azure App Service の可用性、パフォーマンス、およびアプリケーションの問題に関するよくあるご質問への回答を得ます。
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: 021e680a2ca5f7c00f113c4a17421b2648ca6230
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/26/2020
ms.locfileid: "82159985"
---
# <a name="application-performance-faqs-for-web-apps-in-azure"></a>Azure での Web アプリのアプリケーションパフォーマンスに関するよくあるご質問

> [!NOTE]
> 以下のガイドラインのいくつかは、Windows または Linux App Services でのみ有効な場合があります。 たとえば、Linux App Services は既定で 64 ビット モードで動作します。
>

この記事では、[Azure App Service の Web アプリ機能](https://azure.microsoft.com/services/app-service/web/)でのアプリケーション パフォーマンスの問題に関するよくあるご質問 (FAQ) への回答を示します。

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-is-my-app-slow"></a>アプリが低速なのはなぜですか?

アプリの低速なパフォーマンスには複数の要因が関与している可能性があります。 詳細なトラブルシューティング手順については、「[Troubleshoot slow web app performance (Web アプリの低速なパフォーマンスのトラブルシューティング)](troubleshoot-performance-degradation.md)」を参照してください。

## <a name="how-do-i-troubleshoot-a-high-cpu-consumption-scenario"></a>CPU 使用量が多いシナリオをトラブルシューティングするにはどうすればよいですか?

CPU 使用量が多い一部のシナリオでは、アプリが実際により多くのコンピューティング リソースを必要としている可能性があります。 その場合は、アプリケーションが必要なすべてのリソースを取得できるように、より高いサービス階層へのスケーリングを検討してください。 その他の場合、CPU 使用量の多さは、不適切なループまたはコーディング方法によって発生している可能性があります。 CPU 使用量が増えている原因の調査は 2 つの部分からなるプロセスです。 最初にプロセス ダンプを作成し、次にプロセス ダンプを分析します。 詳細については、「[Capture and analyze a dump file for high CPU consumption for Web Apps (Web アプリの増加した CPU 使用量のためのダンプ ファイルの取得と分析)](https://blogs.msdn.microsoft.com/asiatech/2016/01/20/how-to-capture-dump-when-intermittent-high-cpu-happens-on-azure-web-app/)」を参照してください。

## <a name="how-do-i-troubleshoot-a-high-memory-consumption-scenario"></a>メモリ使用量が多いシナリオをトラブルシューティングするにはどうすればよいですか?

メモリ使用量が多い一部のシナリオでは、アプリが実際により多くのコンピューティング リソースを必要としている可能性があります。 その場合は、アプリケーションが必要なすべてのリソースを取得できるように、より高いサービス階層へのスケーリングを検討してください。 その他の場合、コード内のバグがメモリ リークの原因になることがあります。 また、コーディング方法によってもメモリ使用量が増えることがあります。 メモリ使用量が多い原因の調査は 2 つの部分からなるプロセスです。 最初にプロセス ダンプを作成し、次にプロセス ダンプを分析します。 Azure サイト拡張機能ギャラリーのクラッシュ診断は、これらの手順の両方を効率的に実行できます。 詳細については、「[Capture and analyze a dump file for intermittent high memory for Web Apps (Web アプリの断続的な増加したメモリ使用量のためのダンプ ファイルの取得と分析)](https://blogs.msdn.microsoft.com/asiatech/2016/02/02/how-to-capture-and-analyze-dump-for-intermittent-high-memory-on-azure-web-app/)」を参照してください。

## <a name="how-do-i-automate-app-service-web-apps-by-using-powershell"></a>PowerShell を使用して App Service Web アプリを自動化するにはどうすればよいですか?

PowerShell コマンドレットを使用すると、App Service Web アプリを管理および維持できます。 ブログの投稿「[Automate web apps hosted in Azure App Service by using PowerShell (PowerShell を使用して Azure App Service でホストされた Web アプリを自動化する)](https://blogs.msdn.microsoft.com/puneetgupta/2016/03/21/automating-webapps-hosted-in-azure-app-service-through-powershell-arm-way/)」では、Azure Resource Manager ベースの PowerShell コマンドレットを使用して一般的なタスクを自動化する方法について説明しています。 このブログの投稿にはまた、さまざまな Web アプリ管理タスクのサンプル コードも含まれています。 すべての App Service Web アプリ コマンドレットの説明と構文については、「[Az.Websites](/powershell/module/az.websites)」を参照してください。

## <a name="how-do-i-view-my-web-apps-event-logs"></a>Web アプリのイベント ログを表示するにはどうすればよいですか?

Web アプリのイベント ログを表示するには:

1. **Kudu の Web サイト** (`https://*yourwebsitename*.scm.azurewebsites.net`) にサインインします。
2. メニューで、 **[デバッグ コンソール]**  >  **[CMD]** を選択します。
3. **LogFiles** フォルダーを選択します。
4. イベント ログを表示するには、**eventlog.xml** の横にある鉛筆アイコンを選択します。
5. ログをダウンロードするには、PowerShell コマンドレット `Save-AzureWebSiteLog -Name webappname` を実行します。

## <a name="how-do-i-capture-a-user-mode-memory-dump-of-my-web-app"></a>Web アプリのユーザーモードのメモリ ダンプをキャプチャするにはどうすればよいですか?

Web アプリのユーザーモードのメモリ ダンプをキャプチャするには:

1. **Kudu の Web サイト** (`https://*yourwebsitename*.scm.azurewebsites.net`) にサインインします。
2. **[プロセス エクスプローラー]** メニューを選択します。
3. **[w3wp.exe]** プロセスまたは [WebJob] プロセスを右クリックします。
4. **[Download Memory Dump] \(メモリ ダンプのダウンロード)**  >  **[Full Dump] \(完全ダンプ)** を選択します。

## <a name="how-do-i-view-process-level-info-for-my-web-app"></a>Web アプリのプロセス レベルの情報を表示するにはどうすればよいですか?

Web アプリのプロセス レベルの情報を表示するには、次の 2 つのオプションがあります。

*   Azure Portal で次の操作を行います。
    1. Web アプリの **[プロセス エクスプローラー]** を開きます。
    2. 詳細を表示するには、 **[w3wp.exe]** プロセスを選択します。
*   Kudu コンソールで次の操作を行います。
    1. **Kudu の Web サイト** (`https://*yourwebsitename*.scm.azurewebsites.net`) にサインインします。
    2. **[プロセス エクスプローラー]** メニューを選択します。
    3. **[w3wp.exe]** プロセスの **[プロパティ]** を選択します。

## <a name="when-i-browse-to-my-app-i-see-error-403---this-web-app-is-stopped-how-do-i-resolve-this"></a>アプリを参照すると、"エラー 403 - この Web アプリが停止しています" が表示されます。 解決するにはどうすればよいですか?

このエラーは、次の 3 つの状態によって発生する可能性があります。

* Web アプリが請求の上限に達したため、サイトが使用できなくなった。
* ポータルで Web アプリが停止された。
* Web アプリが、[無料] または [共有] スケール サービス プランに適用される可能性のあるリソースのクォータ制限に達した。

エラーの原因を表示し、問題を解決するには、「[Web アプリ: "エラー 403 – この Web アプリが停止しています"](https://blogs.msdn.microsoft.com/waws/2016/01/05/azure-web-apps-error-403-this-web-app-is-stopped/)」の手順に従います。

## <a name="where-can-i-learn-more-about-quotas-and-limits-for-various-app-service-plans"></a>各種 App Service プランのクォータと制限に関する詳細情報はどこで得られますか?

クォータと制限については、「[App Service の制限](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits)」を参照してください。 

## <a name="how-do-i-decrease-the-response-time-for-the-first-request-after-idle-time"></a>アイドル時間の後の最初の要求の応答時間を短縮するにはどうすればよいですか?

既定では、設定された期間だけアイドル状態が続くと Web アプリはアンロードされます。 このようにして、システムはリソースを節約できます。 欠点は、Web アプリが読み込まれて応答の処理を開始できるようにするために、Web アプリがアンロードされた後の最初の要求への応答が長くなることです。 [基本] および [標準] サービス プランでは、アプリを常に読み込まれた状態にするために、 **[常時接続]** 設定をオンにすることができます。 これにより、アプリがアイドル状態になった後の読み込み時間が長くなることはなくなります。 **[常時接続]** 設定を変更するには:

1. Azure Portal で、Web アプリに移動します。
2. **[構成]** を選択します。
3. **[全般設定]** を選択します。
4. **[常時接続]** の **[On] \(オン)** を選択します。

## <a name="how-do-i-turn-on-failed-request-tracing"></a>失敗した要求トレースをオンにするにはどうすればよいですか?

失敗した要求トレースをオンにするには:

1. Azure Portal で、Web アプリに移動します。
3. **[すべての設定]**  >  **[診断ログ]** を選択します。
4. **[失敗した要求トレース]** の **[On] \(オン)** を選択します。
5. **[保存]** を選択します。
6. Web アプリ ブレードで **[ツール]** を選択します。
7. **[Visual Studio Online]** を選択します。
8. 設定が **[On] \(オン)** でない場合は、 **[On] \(オン)** を選択します。
9. **[Go] \(移動)** を選択します。
10. **Web.config** を選択します。
11. system.webServer で、この構成を追加します (特定の URL をキャプチャする場合)。

    ```xml
    <system.webServer>
    <tracing> <traceFailedRequests>
    <remove path="*api*" />
    <add path="*api*">
    <traceAreas>
    <add provider="ASP" verbosity="Verbose" />
    <add provider="ASPNET" areas="Infrastructure,Module,Page,AppServices" verbosity="Verbose" />
    <add provider="ISAPI Extension" verbosity="Verbose" />
    <add provider="WWW Server" areas="Authentication,Security,Filter,StaticFile,CGI,Compression, Cache,RequestNotifications,Module,FastCGI" verbosity="Verbose" />
    </traceAreas>
    <failureDefinitions statusCodes="200-999" />
    </add> </traceFailedRequests>
    </tracing>
    ```
12. 低速なパフォーマンスの問題をトラブルシューティングするには、この構成を追加します (キャプチャ要求が 30 秒を超える場合)。
    ```xml
    <system.webServer>
    <tracing> <traceFailedRequests>
    <remove path="*" />
    <add path="*">
    <traceAreas> <add provider="ASP" verbosity="Verbose" />
    <add provider="ASPNET" areas="Infrastructure,Module,Page,AppServices" verbosity="Verbose" />
    <add provider="ISAPI Extension" verbosity="Verbose" />
    <add provider="WWW Server" areas="Authentication,Security,Filter,StaticFile,CGI,Compression, Cache,RequestNotifications,Module,FastCGI" verbosity="Verbose" />
    </traceAreas>
    <failureDefinitions timeTaken="00:00:30" statusCodes="200-999" />
    </add> </traceFailedRequests>
    </tracing>
    ```
13. 失敗した要求トレースをダウンロードするには、[ポータル](https://portal.azure.com)で Web サイトに移動します。
15. **[ツール]**  >  **[Kudu]**  >  **[Go] \(移動)** を選択します。
18. メニューで、 **[デバッグ コンソール]**  >  **[CMD]** を選択します。
19. **LogFiles** フォルダーを選択してから、**W3SVC** で始まる名前を持つフォルダーを選択します。
20. XML ファイルを表示するには、鉛筆アイコンを選択します。

## <a name="i-see-the-message-worker-process-requested-recycle-due-to-percent-memory-limit-how-do-i-address-this-issue"></a>"Worker Process requested recycle due to 'Percent Memory' limit ('メモリ比率' の制限のためにワーカー プロセスがリサイクルを要求しました)" というメッセージが表示されます。 この問題に対処するにはどうすればよいですか?

32 ビット プロセスの使用可能な最大メモリ量は (64 ビット オペレーティング システム上であっても) 2 GB です。 既定では、ワーカー プロセスは App Service では 32 ビットに設定されています (従来の Web アプリケーションとの互換性のため)。

Web worker ロールで使用可能な追加のメモリを利用できるように、64 ビット プロセスに切り替えることを検討してください。 これにより Web アプリの再起動がトリガーされるため、それに応じてスケジュールします。

また、64 ビット環境には [基本] または [標準] サービス プランが必要なことにも注意してください。 [無料] および [共有] プランは常に 32 ビット環境で実行されます。

詳細については、「[Configure web apps in App Service (App Service での Web アプリの構成)](configure-common.md)」を参照してください。

## <a name="why-does-my-request-time-out-after-230-seconds"></a>230 秒後に要求がタイムアウトになるのはなぜですか?

Azure Load Balancer には、4 分という既定のアイドル タイムアウト設定があります。 これは一般に、Web 要求のための妥当な応答時間の制限です。 Web アプリにバックグラウンド処理が必要な場合は、Azure WebJobs を使用することをお勧めします。 Azure Web アプリは WebJobs を呼び出し、バックグラウンド処理の完了時に通知を受けることができます。 WebJobs を使用するための複数の方法 (キューやトリガーなど) から選択できます。

WebJobs は、バックグラウンド処理用に設計されています。 WebJobs では、バックグラウンド処理を必要なだけ実行できます。 WebJobs の詳細については、「[WebJobs でのバックグラウンド タスクの実行](webjobs-create.md)」を参照してください。

## <a name="aspnet-core-applications-that-are-hosted-in-app-service-sometimes-stop-responding-how-do-i-fix-this-issue"></a>App Service でホストされている ASP.NET Core アプリケーションが応答を停止する場合があります。 この問題を解決するにはどうすればよいですか?

以前の [Kestrel バージョン](https://github.com/aspnet/KestrelHttpServer/issues/1182)での既知の問題により、App Service でホストされている ASP.NET Core 1.0 アプリが断続的に応答を停止することがあります。 また、"指定された CGI アプリケーションにエラーが発生し、サーバーがプロセスを停止しました" というメッセージが表示されることもあります。

この問題は Kestrel バージョン 1.0.2 で修正されました。 このバージョンは ASP.NET Core 1.0.3 更新プログラムに含まれています。 この問題を解決するには、Kestrel 1.0.2 を使用するようにアプリの依存関係を更新するようにしてください。 あるいは、ブログの投稿「[ASP.NET Core 1.0 slow perf issues in App Service web apps (App Service Web アプリでの ASP.NET Core 1.0 の低速なパフォーマンスの問題)](https://blogs.msdn.microsoft.com/waws/2016/12/11/asp-net-core-slow-perf-issues-on-azure-websites)」で説明されている 2 つの対処法のいずれかを使用することもできます。


## <a name="i-cant-find-my-log-files-in-the-file-structure-of-my-web-app-how-can-i-find-them"></a>Web アプリのファイル構造内にログ ファイルが見つかりません。 どのようにしたら見つけることができますか?

App Service のローカル キャッシュ機能を使用する場合は、App Service インスタンスに対する LogFiles および Data フォルダーのフォルダー構造が影響を受けます。 ローカル キャッシュが使用されている場合は、LogFiles および Data フォルダーのストレージ内にサブフォルダが作成されます。 これらのサブフォルダは、"一意識別子" + タイムスタンプという名前付けパターンを使用します。 各サブフォルダは、Web アプリが実行されているか、または実行された VM インスタンスに対応します。

ローカル キャッシュを使用しているかどうかを判定するには、App Service の **[アプリケーションの設定]** タブを確認します。ローカル キャッシュが使用されている場合、アプリの設定 `WEBSITE_LOCAL_CACHE_OPTION` は `Always` に設定されています。

ローカル キャッシュを使用していないときにこの問題が発生する場合は、サポート要求を送信してください。

## <a name="i-see-the-message-an-attempt-was-made-to-access-a-socket-in-a-way-forbidden-by-its-access-permissions-how-do-i-resolve-this"></a>"An attempt was made to access a socket in a way forbidden by its access permissions (アクセス許可によって禁止されている方法でソケットへのアクセスが試みられました)" というメッセージが表示されます。 解決するにはどうすればよいですか?

このエラーは通常、VM インスタンス上の送信 TCP 接続が使い果たされた場合に発生します。 App Service では、VM インスタンスごとに作成できる送信接続の最大数に対して制限が適用されます。 詳細については、「[Cross-VM numerical limits (クロス VM の数値制限)](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits)」を参照してください。

このエラーはまた、アプリケーションからローカル アドレスにアクセスしようとした場合にも発生することがあります。 詳細については、「[Local address requests (ローカル アドレス要求)](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#local-address-requests)」を参照してください。

Web アプリでの送信接続の詳細については、[Azure Web サイトへの送信接続](https://www.freekpaans.nl/2015/08/starving-outgoing-connections-on-windows-azure-web-sites/)に関するブログの投稿を参照してください。

## <a name="how-do-i-use-visual-studio-to-remote-debug-my-app-service-web-app"></a>Visual Studio を使用して App Service Web アプリをリモート デバッグするにはどうすればよいですか?

Visual Studio を使用して Web アプリをデバッグする方法を示す詳細なチュートリアルについては、「[Remote debug your App Service web app (App Service Web アプリのリモート デバッグ)](https://blogs.msdn.microsoft.com/benjaminperkins/2016/09/22/remote-debug-your-azure-app-service-web-app/)」を参照してください。
