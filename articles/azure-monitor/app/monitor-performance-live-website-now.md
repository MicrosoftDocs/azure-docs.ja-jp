---
title: Azure Application Insights を使用してライブ ASP.NET Web アプリを監視する | Microsoft Docs
description: Web サイトを再デプロイせずにそのパフォーマンスを監視します。 オンプレミスまたは VM でホストされた ASP.NET Web アプリが対象です。
ms.topic: conceptual
ms.date: 08/26/2019
ms.custom: devx-track-dotnet
ms.openlocfilehash: 79e14c171adde89c43c5ea82a60db39133157293
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100576433"
---
# <a name="instrument-web-apps-at-runtime-with-application-insights-codeless-attach"></a>Application Insights コードなしアタッチを使用した実行時の Web アプリのインストルメント化

> [!IMPORTANT]
> Status Monitor の使用は推奨されなくなり、**2021 年 6 月 1 日以降**、このバージョンの Status Monitor はサポートされません。 これは Azure Monitor Application Insights エージェント (旧称 Status Monitor v2) に置き換えられました。 [オンプレミス サーバーのデプロイ](./status-monitor-v2-overview.md)または [Azure 仮想マシンと仮想マシン スケール セットのデプロイ](./azure-vm-vmss-apps.md)に関するドキュメントを参照してください。

Azure Application Insights を使用すれば、ライブ Web アプリケーションをインストルメント化できます。その際、コードに変更を加えたり、再デプロイしたりする必要はありません。 [Microsoft Azure](https://azure.com) サブスクリプションが必要です。

Status Monitor は、オンプレミスまたは VM の IIS でホストされた .NET アプリケーションをインストルメント化するために使用されます。

- お客様のアプリが Azure VM または Azure 仮想マシン スケール セットにデプロイされている場合、[こちらの手順](azure-vm-vmss-apps.md)に従ってください。
- お客様のアプリが Azure のアプリ サービスにデプロイされている場合、[こちらの手順](azure-web-apps.md)に従ってください。
- お客様のアプリが Azure VM にデプロイされている場合は、Azure コントロール パネルから Application Insights の監視を有効にすることができます
- ([Azure Cloud Services](./cloudservices.md) のインストルメント化については、個別の記事もあります)。


![App Insights の概要グラフ (失敗した要求、サーバー応答時間、サーバー要求に関する情報) のスクリーンショット](./media/monitor-performance-live-website-now/overview-graphs.png)

Application Insights を .NET Web アプリケーションに適用する方法には、次の 2 つがあります。

* **ビルド時:** Web アプリ コードに [Application Insights SDK を追加][greenbrown]します。
* **実行時:** 後述するように、コードのリビルドと再デプロイを行うことなく、サーバー上の Web アプリをインストルメント化します。

> [!NOTE]
> ビルド時のインストルメンテーションを使用する場合は、実行時のインストルメンテーションはオンにしても機能しません。

各方法の特徴について概要を次に示します。

|  | ビルド時 | 実行時 |
| --- | --- | --- |
| **要求と例外** |はい |はい |
| **[より詳細な例外](./asp-net-exceptions.md)** | |はい |
| **[依存関係の診断](./asp-net-dependencies.md)** |.NET 4.6 以降 (詳細レベルは低い) |はい。全詳細: 結果コード、SQL コマンド テキスト、HTTP 動詞|
| **[システム パフォーマンス カウンター](./performance-counters.md)** |はい |はい |
| **[カスタム テレメトリの API][api]** |はい |いいえ |
| **[トレース ログ統合](./asp-net-trace-logs.md)** |はい |いいえ |
| **[ページ ビューとユーザー データ](./javascript.md)** |はい |いいえ |
| **コードのリビルドが必要** |はい | いいえ |



## <a name="monitor-a-live-iis-web-app"></a>ライブ IIS Web アプリの監視

アプリが IIS サーバーでホストされている場合は、Status Monitor を使用して Application Insights を有効にします。

1. IIS Web サーバーで、管理者の資格情報を使用してサインインします。
2. Application Insights Status Monitor がまだインストールされていない場合は、[インストーラーをダウンロードして実行します](#download)。
3. Status Monitor で、監視するインストール済みの Web アプリケーションまたは Web サイトを選択します。 Azure の資格情報でサインインします。

    Application Insights ポータルで結果を表示するときに使用するリソースを構成します。 (通常は、新しいリソースを作成するのが最良です。 このアプリに対して [Web テスト][availability]や[クライアントの監視][client]を既に設定している場合は、既存のリソースを選択します。) 

    ![アプリとリソースを選択します。](./media/monitor-performance-live-website-now/appinsights-036-configAIC.png)

4. IIS を再起動します。

    ![ダイアログの上部にある [再起動] を選択します。](./media/monitor-performance-live-website-now/appinsights-036-restart.png)

    少しの間、Web サービスが中断されます。

## <a name="customize-monitoring-options"></a>監視オプションのカスタマイズ

Application Insights を有効にすると、Web アプリに DLL と ApplicationInsights.config が追加されます。 [この .config ファイルを編集](./configuration-with-applicationinsights-config.md)して、一部のオプションを変更できます。

## <a name="when-you-re-publish-your-app-re-enable-application-insights"></a>アプリを再発行するときに、Application Insights を再度有効にしてください。

アプリを再発行する前に、[Visual Studio でコードに Application Insights を追加する][greenbrown]ことを検討してください。 より詳細なテレメトリが得られ、カスタム テレメトリを記述できるようになります。

コードに Application Insights を追加せずに再発行する場合は、デプロイ プロセスによって発行済み Web サイトから DLL と ApplicationInsights.config が削除される可能性があることに注意してください。 そのため、次のようになります。

1. ApplicationInsights.config を編集した場合は、アプリを再発行する前に ApplicationInsights.config のコピーを作成します。
2. アプリを再発行します。
3. Application Insights の監視を再度有効にします。 (Azure Web アプリのコントロール パネルまたは IIS ホストの Status Monitor のいずれか適切な方法を使用します)。
4. .config ファイルで実行した編集を再開します。


## <a name="troubleshooting"></a><a name="troubleshoot"></a>トラブルシューティング

### <a name="confirm-a-valid-installation"></a>有効なインストールの確認 

インストールが成功したことを確認するために実行できるいくつかの手順を以下に示します。

- applicationInsights.config ファイルがターゲット アプリ ディレクトリ内に存在し、お客様の ikey を含んでいることを確認します。

- データがないと思われる場合は、[Analytics](../logs/log-analytics-tutorial.md) でクエリを実行して、現在テレメトリを送信中のクラウド ロールをすべて一覧表示できます。
  ```Kusto
  union * | summarize count() by cloud_RoleName, cloud_RoleInstance
  ```

- Application Insights が正常にアタッチされたことを確認する必要がある場合は、コマンド ウィンドウで [Sysinternals の Handle](/sysinternals/downloads/handle) を実行して、applicationinsights.dll が IIS によって読み込まれたことを確認できます。

  ```console
  handle.exe /p w3wp.exe
  ```


### <a name="cant-connect-no-telemetry"></a>接続できない テレメトリが見つかりませんか?

* Status Monitor が動作するように、サーバーのファイアウォールで、[必要ないくつかの送信ポート](./ip-addresses.md#outgoing-ports)を開きます。

### <a name="unable-to-login"></a>ログインできない

Status Monitor でログインできない場合は、代わりにコマンド ライン インストールを行ってください。 お客様の ikey を収集するために Status Monitor によってログインが試行されますが、これはコマンドを使用して手動で指定できます。

```powershell
Import-Module 'C:\Program Files\Microsoft Application Insights\Status Monitor\PowerShell\Microsoft.Diagnostics.Agent.StatusMonitor.PowerShell.dll'
Start-ApplicationInsightsMonitoring -Name appName -InstrumentationKey 00000000-000-000-000-0000000
```

### <a name="could-not-load-file-or-assembly-systemdiagnosticsdiagnosticsource"></a>ファイルまたはアセンブリ "System.Diagnostics.DiagnosticSource" を読み込めませんでした

Application Insights を有効にした後にこのエラーが発生する場合があります。 これは、インストーラーによってお客様の bin ディレクトリ内の dll が置き換えられるためです。
修正するには、お客様の web.config を更新します。

```xml
<dependentAssembly>
    <assemblyIdentity name="System.Diagnostics.DiagnosticSource" publicKeyToken="cc7b13ffcd2ddd51"/>
    <bindingRedirect oldVersion="0.0.0.0-4.*.*.*" newVersion="4.0.2.1"/>
</dependentAssembly>
```

[こちら](https://github.com/MohanGsk/ApplicationInsights-Home)でこの問題を追跡しています。


### <a name="application-diagnostic-messages"></a>アプリケーションの診断メッセージ

* Status Monitor を開き、左ウィンドウ枠でアプリケーションを選択します。 「通知の構成」セクションに、このアプリケーションの診断メッセージがあるかどうかを確認します。

  ![Open the Performance blade to see request, response time, dependency and other data](./media/monitor-performance-live-website-now/appinsights-status-monitor-diagnostics-message.png)
  
### <a name="detailed-logs"></a>詳細ログ

* 既定では、Status Monitor は `C:\Program Files\Microsoft Application Insights\Status Monitor\diagnostics.log` に診断ログを出力します。

* 詳細ログを出力するには、構成ファイル `C:\Program Files\Microsoft Application Insights\Status Monitor\Microsoft.Diagnostics.Agent.StatusMonitor.exe.config` を修正し、`<add key="TraceLevel" value="All" />` を `appsettings` に追加します。
次に、Status Monitor を再起動します。

* Status Monitor は .NET アプリケーションであるため、[適切な診断を構成ファイルに追加することによる .net トレース](/dotnet/framework/configure-apps/file-schema/trace-debug/system-diagnostics-element)の有効化も実行できます。 たとえば、いくつかのシナリオでは、[ネットワーク トレースを構成する](/dotnet/framework/network-programming/how-to-configure-network-tracing)ことで、ネットワーク レベルで何が起こるかを確認するのに役立つ可能性があります。

### <a name="insufficient-permissions"></a>アクセス許可が不十分である
  
* サーバーに「権限が不十分」であるという内容のメッセージが表示される場合、次を実行してください。
  * IIS マネージャーで、アプリケーション プールを選択し、 **[詳細設定]** を開きます。 **[プロセス モデル]** に表示されている ID をメモします。
  * コンピューターの管理コントロール パネルで、この ID をパフォーマンス モニター ユーザー グループに追加します。

### <a name="conflict-with-systems-center-operations-manager"></a>Systems Center Operations Manager との競合

* MMA/SCOM (Systems Center Operations Manager) がサーバーにインストールされている場合、一部のバージョンで競合が発生することがあります。 SCOM と Status Monitor の両方をアンインストールし、最新バージョンを再度インストールしてください。

### <a name="failed-or-incomplete-installation"></a>失敗したインストールまたは不完全なインストール

インストール中に Status Monitor が失敗すると、不完全なインストールが残って、そこから Status Monitor を復旧できなくなる場合があります。 この場合、手動のリセットが必要です。

お客様のアプリケーション ディレクトリにある以下のファイルをすべて削除します。
- お客様の bin ディレクトリ内にあり、"Microsoft.AI." または "Microsoft.ApplicationInsights." で始まるすべての DLL
- お客様の bin ディレクトリ内にある DLL "Microsoft.Web.Infrastructure.dll"
- お客様の bin ディレクトリ内にある DLL "System.Diagnostics.DiagnosticSource.dll"
- お客様のアプリケーション ディレクトリで "App_Data\packages" を削除します
- お客様のアプリケーション ディレクトリで "applicationinsights.config" を削除します


### <a name="additional-troubleshooting"></a>その他のトラブルシューティング

* その他の[トラブルシューティング][qna]をご覧ください。

## <a name="system-requirements"></a>システム要件
サーバー上の Application Insights Status Monitor をサポートする OS:

* Windows Server 2008
* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

最新の SP と .NET Framework 4.5 を使用 (Status Monitor はこのバージョンのフレームワーク上に構築されます)

クライアント側:.NET Framework 4.5 がインストールされた Windows 7、8、8.1、10

IIS のサポート:IIS 7、7.5、8、8.5 (IIS は必須)。

## <a name="automation-with-powershell"></a>PowerShell でのオートメーション
監視の開始と停止は、IIS サーバーで PowerShell を使って実行できます。

最初に Application Insights モジュールをインポートします。

```powershell
Import-Module 'C:\Program Files\Microsoft Application Insights\Status Monitor\PowerShell\Microsoft.Diagnostics.Agent.StatusMonitor.PowerShell.dll'
```

監視されているアプリを特定します。

`Get-ApplicationInsightsMonitoringStatus [-Name appName]`

* `-Name` (省略可能) Web アプリの名前。
* この IIS サーバーに存在する各 Web アプリ (または指定されたアプリ) について、Application Insights の監視ステータスを表示します。
* 各アプリの `ApplicationInsightsApplication` を返します。

  * `SdkState==EnabledAfterDeployment`:アプリは監視中です。Status Monitor ツールまたは `Start-ApplicationInsightsMonitoring` によって実行時にインストルメント化されました。
  * `SdkState==Disabled`:アプリは Application Insights 用にインストルメント化されていません。 過去に一度もインストルメント化されたことがないか、Status Monitor ツールまたは `Stop-ApplicationInsightsMonitoring`によって実行時の監視が無効に設定されています。
  * `SdkState==EnabledByCodeInstrumentation`:アプリは、ソース コードに SDK を追加することによってインストルメント化されています。 SDK は更新することも停止することもできません。
  * `SdkVersion` は、このアプリの監視に使用されているバージョンを示します。
  * `LatestAvailableSdkVersion`は、現在 NuGet ギャラリーで入手できるバージョンを示します。 このバージョンにアプリをアップグレードするには、 `Update-ApplicationInsightsMonitoring`を使用します。

`Start-ApplicationInsightsMonitoring -Name appName -InstrumentationKey 00000000-000-000-000-0000000`

* `-Name` IIS に存在するアプリの名前。
* `-InstrumentationKey` 結果を表示する Application Insights リソースの ikey。
* このコマンドレットが作用するのは、まだインストルメント化されていないアプリ (SdkState==NotInstrumented) だけです。

    このコマンドレットは、既にインストルメント化されたアプリには作用しません。 SDK をコードに追加することによってビルド時にインストルメント化されたアプリか、過去にこのコマンドレットを使用したことによって実行時にインストルメント化されたアプリかは問題ではありません。

    アプリをインストルメント化するときに使用される SDK バージョンは、このサーバーに最近ダウンロードされたバージョンとなります。

    最新バージョンをダウンロードするには、Update-ApplicationInsightsVersion を使用してください。
* 成功すると `ApplicationInsightsApplication` が返されます。 失敗した場合、トレースが stderr に出力されます。

   ```output
   Name                      : Default Web Site/WebApp1
   InstrumentationKey        : 00000000-0000-0000-0000-000000000000
   ProfilerState             : ApplicationInsights
   SdkState                  : EnabledAfterDeployment
   SdkVersion                : 1.2.1
   LatestAvailableSdkVersion : 1.2.3
   ```

`Stop-ApplicationInsightsMonitoring [-Name appName | -All]`

* `-Name` IIS に存在するアプリの名前。
* `-All` この IIS サーバーに存在する、ステータスが `SdkState==EnabledAfterDeployment` であるすべてのアプリの監視を停止します。
* 指定されたアプリの監視を停止し、インストルメンテーションを解除します。 実行時に Status Monitoring ツールまたは Start-ApplicationInsightsApplication を使用してインストルメント化されたアプリに対してのみ正しく動作します。 (`SdkState==EnabledAfterDeployment`)
* ApplicationInsightsApplication が返されます。

`Update-ApplicationInsightsMonitoring -Name appName [-InstrumentationKey "0000000-0000-000-000-0000"`]

* `-Name`:IIS に存在する Web アプリの名前。
* `-InstrumentationKey` (省略可能)。アプリのテレメトリの送信先となるリソースを変更する場合に使用します。
* このコマンドレットの機能を次に示します。
  * 最近このマシンにダウンロードされた SDK バージョンに、指定されたアプリをアップグレードします (`SdkState==EnabledAfterDeployment` の場合にのみ機能します)。
  * インストルメンテーション キーが指定された場合、そのキーを持ったリソースにテレメトリを送信するよう、指定されたアプリを再構成します (`SdkState != Disabled` の場合に機能します)。

`Update-ApplicationInsightsVersion`

* 最新の Application Insights SDK をサーバーにダウンロードします。

## <a name="questions-about-status-monitor"></a><a name="questions"></a>Status Monitor に関する質問

### <a name="what-is-status-monitor"></a>Status Monitor とは何ですか?

IIS Web サーバーにインストールするデスクトップ アプリケーションです。 Web アプリのインストルメント化と構成を支援します。 

### <a name="when-do-i-use-status-monitor"></a>どのような場合に Status Monitor を使うのですか?

* IIS サーバーで実行される任意の Web アプリをインストルメント化する場合。既に実行中の場合でも、インストルメント化が可能です。
* コンパイル時に [Application Insights SDK でビルドされた](./asp-net.md) Web アプリ用の追加のテレメトリを有効にする場合。 

### <a name="can-i-close-it-after-it-runs"></a>実行後に閉じることはできますか?

はい。 選択した Web サイトをインストルメント化した後で、閉じることができます。

単独でテレメトリを収集することはありません。 Web アプリの構成と、一部のアクセス許可の設定のみを行います。

### <a name="what-does-status-monitor-do"></a>Status Monitor では何ができますか?

Status Monitor でインストルメント化する Web アプリを選択する際、次のことができます。

* Web アプリのバイナリ フォルダーで、Application Insights のアセンブリと ApplicationInsights.config ファイルをダウンロードし、配置します。
* CLR プロファイリングを有効にして、依存関係の呼び出しを収集します。

### <a name="what-version-of-application-insights-sdk-does-status-monitor-install"></a>Status Monitor でインストールされる Application Insights SDK のバージョンは何ですか?

現時点では、Status Monitor でインストールできる Application Insights SDK のバージョンは 2.3 または 2.4 のみです。 

Application Insights SDK バージョン 2.4 は [.NET 4.0 をサポートする最後のバージョン](https://github.com/microsoft/ApplicationInsights-dotnet/releases/tag/v2.5.0-beta1)であり、[2016 年 1 月に提供が終了しています](https://devblogs.microsoft.com/dotnet/support-ending-for-the-net-framework-4-4-5-and-4-5-1/)。 そのため、現時点では、Status Monitor を使用して .NET 4.0 アプリケーションをインストルメント化できます。 

### <a name="do-i-need-to-run-status-monitor-whenever-i-update-the-app"></a>アプリを更新するときは、毎回 Status Monitor を実行する必要がありますか?

段階的に再デプロイする場合は必要ありません。 

発行プロセスで [既存のファイルの削除] オプションを選択した場合は、Status Monitor を再実行して Application Insights を構成する必要があります。

### <a name="what-telemetry-is-collected"></a>どのようなテレメトリが収集されますか?

Status Monitor を使用して実行時にのみインストルメント化するアプリケーションの場合:

* HTTP 要求
* 依存関係の呼び出し
* 例外
* パフォーマンス カウンター

コンパイル時に既にインストルメント化されているアプリケーションの場合:

 * プロセス カウンター。
 * 依存関係の呼び出し (.NET 4.5) と、依存関係の呼び出しの戻り値 (.NET 4.6)。
 * 例外スタック トレースの値。

[詳細情報](https://apmtips.com/posts/2016-11-18-how-application-insights-status-monitor-not-monitors-dependencies/)

## <a name="video"></a>ビデオ

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="download-status-monitor"></a><a name="download"></a>Status Monitor のダウンロード

- 新しい[PowerShell モジュール](./status-monitor-v2-overview.md)を使用します
- [Status Monitor インストーラー](https://go.microsoft.com/fwlink/?LinkId=506648)をダウンロードし、実行します。
- または、[Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx) を実行し、その中で Application Insights Status Monitor を検索します。

## <a name="next-steps"></a><a name="next"></a>次のステップ

テレメトリの表示:

* パフォーマンスと使用状況を監視するための[メトリックを探索](../essentials/metrics-charts.md)します
* 問題を診断するために[イベントとログを検索][diagnostic]します
* より高度なクエリのために [Analytics](../logs/log-query-overview.md) を使用します

テレメトリの追加:

* サイトがライブの状態であることを確認するために [Web テストを作成][availability]します。
* Web ページ コードからの例外を参照してトレースの呼び出しを挿入するために、[Web クライアント テレメトリ][usage]を追加します。
* トレースとログの呼び出しを挿入できるように、[Application Insights SDK をコードに追加][greenbrown]します

<!--Link references-->

[api]: ./api-custom-events-metrics.md
[availability]: monitor-web-app-availability.md
[client]: ./javascript.md
[diagnostic]: ./diagnostic-search.md
[greenbrown]: ./asp-net.md
[qna]: ../faq.md
[roles]: ./resources-roles-access-control.md
[usage]: ./javascript.md