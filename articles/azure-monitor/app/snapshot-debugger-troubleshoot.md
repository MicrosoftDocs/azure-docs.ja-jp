---
title: Azure Application Insights スナップショット デバッガーのトラブルシューティング
description: この記事では、開発者が Application Insights スナップショット デバッガーを有効にして使用するのに役立つトラブルシューティングの手順と情報を示します。
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 03/07/2019
ms.reviewer: mbullwin
ms.openlocfilehash: a285f26a406caa88d91da5647b3b79cffc9b614f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102217416"
---
# <a name="troubleshoot-problems-enabling-application-insights-snapshot-debugger-or-viewing-snapshots"></a><a id="troubleshooting"></a> Application Insights Snapshot Debugger の有効化やスナップショットの表示に関する問題のトラブルシューティング
アプリケーションで Application Insights スナップショット デバッガーを有効にしても、例外のスナップショットが表示されない場合は、こちらの手順を使用してトラブルシューティングを行うことができます。

スナップショットが生成されない理由としては、さまざまなことが考えられます。 まずスナップショットの正常性チェックを実行することにより、考えられるいくつかの一般的な原因を特定できます。

## <a name="make-sure-youre-using-the-appropriate-snapshot-debugger-endpoint"></a>適切なスナップショット デバッガー エンドポイントを使用していることを確認する

現在、エンドポイントの変更が必要なリージョンは [Azure Government](https://docs.microsoft.com/azure/azure-government/compare-azure-government-global-azure#application-insights) と [Azure China](https://docs.microsoft.com/azure/china/resources-developer-guide) のみです。

Application Insights SDK を使用する App Service とアプリケーションでは、次の定義されているように、サポートされているスナップショット デバッガーのオーバーライドを使用して接続文字列を更新する必要があります。

|接続文字列プロパティ    | 米国政府のクラウド | China Cloud |   
|---------------|---------------------|-------------|
|SnapshotEndpoint         | `https://snapshot.monitor.azure.us`    | `https://snapshot.monitor.azure.cn` |

その他の接続のオーバーライドの詳細については、[Application Insights のドキュメント](https://docs.microsoft.com/azure/azure-monitor/app/sdk-connection-string?tabs=net#connection-string-with-explicit-endpoint-overrides)を参照してください。

Function App の場合は、サポートされている次のオーバーライドを使用して `host.json` を更新する必要があります。

|プロパティ    | 米国政府のクラウド | China Cloud |   
|---------------|---------------------|-------------|
|AgentEndpoint         | `https://snapshot.monitor.azure.us`    | `https://snapshot.monitor.azure.cn` |

米国政府のクラウド エージェント エンドポイントで更新された `host.json` の例を次に示します。
```json
{
  "version": "2.0",
  "logging": {
    "applicationInsights": {
      "samplingExcludedTypes": "Request",
      "samplingSettings": {
        "isEnabled": true
      },
      "snapshotConfiguration": {
        "isEnabled": true,
        "agentEndpoint": "https://snapshot.monitor.azure.us"
      }
    }
  }
}
```

## <a name="use-the-snapshot-health-check"></a>スナップショットの正常性チェックを使用する
いくつかの一般的な問題により、[デバッグ スナップショットを開く] が表示されません。 古い Snapshot Collector を使用します (たとえば、1 日のアップロード上限に達した場合)。そうしないと、スナップショットのアップロードに時間がかかることがあります。 一般的な問題のトラブルシューティングには、Snapshot Health Check を使用します。

エンドツーエンドのトレース ビューの例外ウィンドウには、Snapshot Health Check に接続できるリンクが表示されます。

![スナップショットの正常性チェックを入力する](./media/snapshot-debugger/enter-snapshot-health-check.png)

インタラクティブでチャットのようなインターフェイスでは、一般的な問題が検索され、問題の解決が案内されます。

![正常性チェック](./media/snapshot-debugger/healthcheck.png)

それでも問題が解決しない場合は、以下の手動トラブルシューティング手順を参照してください。

## <a name="verify-the-instrumentation-key"></a>インストルメンテーション キーの確認

公開したアプリケーションで、正しいインストルメンテーション キーを使用していることを確認します。 通常、インストルメンテーション キーは、ApplicationInsights.config ファイルから読み取られます。 値が、ポータルに表示される Application Insights リソースのインストルメンテーション キーと同じであることを確認します。

## <a name="check-tlsssl-client-settings-aspnet"></a><a id="SSL"></a>TLS/SSL クライアント設定の確認 (ASP.NET)

ASP.NET アプリケーションが、Azure App Service または仮想マシンの IIS でホストされている場合、SSL セキュリティ プロトコルがないため、お使いのアプリケーションがスナップショット デバッガー サービスに接続できない可能性があります。

[スナップショット デバッガー エンドポイントには、TLS バージョン 1.2 が必要です](snapshot-debugger-upgrade.md?toc=/azure/azure-monitor/toc.json)。 SSL セキュリティ プロトコルのセットは、web.config の system.web セクションの httpRuntime targetFramework 値によって有効にされる特性の 1 つです。httpRuntime targetFramework が 4.5.2 以下の場合、TLS 1.2 は既定では含まれていません。

> [!NOTE]
> httpRuntime targetFramework 値は、アプリケーションのビルド時に使用されるターゲット フレームワークに依存しません。

設定を確認するには、web.config ファイルを開き、system.web セクションを見つけます。 `httpRuntime` の `targetFramework` が 4.6 以上に設定されていることを確認します。

   ```xml
   <system.web>
      ...
      <httpRuntime targetFramework="4.7.2" />
      ...
   </system.web>
   ```

> [!NOTE]
> httpRuntime targetFramework の値を変更すると、アプリケーションに適用されるランタイムの特性が変更され、その他の微妙な動作の変更が発生する可能性があります。 この変更を行った後は、アプリケーションを十分にテストするようにしてください。 互換性の変更の完全な一覧については、 https://docs.microsoft.com/dotnet/framework/migration-guide/application-compatibility#retargeting-changes を参照してください。

> [!NOTE]
> targetFramework が 4.7 以上の場合、使用可能なプロトコルは Windows によって決定されます。 Azure App Service では、TLS 1.2 を使用できます。 ただし、独自の仮想マシンを使用している場合は、OS で TLS 1.2 を有効にすることが必要になる場合があります。

## <a name="preview-versions-of-net-core"></a>.NET Core のプレビュー バージョン
.NET Core のプレビュー バージョンを使用している場合、またはアプリケーションによって、依存アセンブリを介して直接または間接的に Application Insights SDK が参照されている場合は、[その他の環境用にスナップショット デバッガーを有効にする](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)方法に関する記事の手順に従ってください。

## <a name="check-the-diagnostic-services-site-extension-status-page"></a>診断サービスのサイト拡張機能の状態ページを確認する
スナップショット デバッガーがポータルの[[Application Insights] ペイン](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)から有効にされた場合は、診断サービスのサイト拡張機能によって有効にされています。

> [!NOTE]
> Application Insights スナップショット デバッガーのコード不要のインストールは、.NET Core サポート ポリシーに従います。
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

## <a name="upgrade-to-the-latest-version-of-the-nuget-package"></a>最新バージョンの NuGet にアップグレードする
スナップショット デバッガーが有効化された方法に基づいて、次のオプションを参照してください。

* [ポータルの [Application Insights] ウィンドウ](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)を通じて Snapshot Debugger を有効にした場合、アプリケーションでは既に最新の NuGet パッケージが実行されています。

* [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet パッケージを含めることで Snapshot Debugger を有効にした場合は、Visual Studio の NuGet パッケージ マネージャーを使用して、Microsoft.ApplicationInsights.SnapshotCollector の最新バージョンが使用されているかどうかを確認してください。

最新の更新プログラムとバグ修正については、[リリース ノートを参照してください](./snapshot-collector-release-notes.md)。

## <a name="check-the-uploader-logs"></a>アップローダー ログの確認

スナップショットの作成後、ミニダンプ ファイル (.dmp) がディスク上に作成されます。 個別アップローダー プロセスでは、そのミニダンプ ファイルを作成し、これを関連する PDB と共に Application Insights のスナップショット デバッガーのストレージにアップロードします。 ミニダンプは、正常にアップロードされた後、ディスクから削除されます。 アップローダー プロセスのログ ファイルは、ディスク上に保持されます。 App Service 環境では、これらのログは `D:\Home\LogFiles` にあります。 App Service の Kudu 管理サイトを使用すると、これらのログ ファイルを検索できます。

1. Azure Portal で App Service アプリケーションを開きます。
2. **[高度なツール]** を選択するか、「**Kudu**」を検索します。
3. **[Go] \(移動)** を選択します。
4. **[Debug console]** (デバッグ コンソール) ドロップダウン リスト ボックスで、 **[CMD]** を選択します。
5. **[LogFiles]** を選択します。

`Uploader_` または `SnapshotUploader_`で始まる名前で拡張子が `.log` であるファイルが 1 つ以上表示されます。 適切なアイコンを選択してログ ファイルをダウンロードするかブラウザーで開きます。
ファイル名には、App Service インスタンスを識別する一意のサフィックスが含まれます。 App Service のインスタンスが 1 つ以上のコンピューターでホストされている場合は、コンピューターごとに個別のログ ファイルがあります。 アップローダーは、新しいミニダンプ ファイルを検出すると、ログ ファイルに記録します。 スナップショットとアップロードの成功の例を次に示します。

```
SnapshotUploader.exe Information: 0 : Received Fork request ID 139e411a23934dc0b9ea08a626db16c5 from process 6368 (Low pri)
    DateTime=2018-03-09T01:42:41.8571711Z
SnapshotUploader.exe Information: 0 : Creating minidump from Fork request ID 139e411a23934dc0b9ea08a626db16c5 from process 6368 (Low pri)
    DateTime=2018-03-09T01:42:41.8571711Z
SnapshotUploader.exe Information: 0 : Dump placeholder file created: 139e411a23934dc0b9ea08a626db16c5.dm_
    DateTime=2018-03-09T01:42:41.8728496Z
SnapshotUploader.exe Information: 0 : Dump available 139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:45.7525022Z
SnapshotUploader.exe Information: 0 : Successfully wrote minidump to D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:45.7681360Z
SnapshotUploader.exe Information: 0 : Uploading D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp, 214.42 MB (uncompressed)
    DateTime=2018-03-09T01:42:45.7681360Z
SnapshotUploader.exe Information: 0 : Upload successful. Compressed size 86.56 MB
    DateTime=2018-03-09T01:42:59.6184651Z
SnapshotUploader.exe Information: 0 : Extracting PDB info from D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp.
    DateTime=2018-03-09T01:42:59.6184651Z
SnapshotUploader.exe Information: 0 : Matched 2 PDB(s) with local files.
    DateTime=2018-03-09T01:42:59.6809606Z
SnapshotUploader.exe Information: 0 : Stamp does not want any of our matched PDBs.
    DateTime=2018-03-09T01:42:59.8059929Z
SnapshotUploader.exe Information: 0 : Deleted D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:59.8530649Z
```

> [!NOTE]
> 上記の例は、Microsoft.ApplicationInsights.SnapshotCollector NuGet パッケージのバージョン 1.2.0 です。 以前のバージョンのアップローダー プロセスは `MinidumpUploader.exe` で、ログはこれほど詳しくありません。

インストルメンテーション キーは、前の例では、`c12a605e73c44346a984e00000000000` です。 この値は、アプリケーションのインストルメンテーション キーと一致する必要があります。
ミニダンプは ID `139e411a23934dc0b9ea08a626db16c5` を持つスナップショットに関連付けられています。 この ID は、後で Application Insights Analytics で関連する例外レコードを検索するために使用できます。

アップローダーは、約 15 分ごとに 1 回新しい PDB をスキャンします。 次に例を示します。

```
SnapshotUploader.exe Information: 0 : PDB rescan requested.
    DateTime=2018-03-09T01:47:19.4457768Z
SnapshotUploader.exe Information: 0 : Scanning D:\home\site\wwwroot for local PDBs.
    DateTime=2018-03-09T01:47:19.4457768Z
SnapshotUploader.exe Information: 0 : Local PDB scan complete. Found 2 PDB(s).
    DateTime=2018-03-09T01:47:19.4614027Z
SnapshotUploader.exe Information: 0 : Deleted PDB scan marker : D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\6368.pdbscan
    DateTime=2018-03-09T01:47:19.4614027Z
```

App Service にホストされて "_いない_" アプリケーションでは、アップローダー ログは、ミニダンプと同じフォルダー `%TEMP%\Dumps\<ikey>` にあります (`<ikey>` はインストルメンテーション キー)。

## <a name="troubleshooting-cloud-services"></a>クラウド サービスのトラブルシューティング
クラウド サービスでは、既定の一時フォルダーがミニダンプ ファイルを保持するには小さすぎて、スナップショットが失われる可能性があります。

必要な領域は、アプリケーションの合計ワーキング セットと同時実行スナップショット数によって異なります。

32 ビット ASP.NET web ロールのワーキング セットは、通常は 200 MB から 500 MB です。 少なくとも 2 つの同時実行スナップショットを許可します。

たとえば、アプリケーションで 1 GB の合計ワーキング セットが使用されている場合は、スナップショットを格納するために少なくとも 2 GB のディスク領域が存在するようにする必要があります。

次の手順に従って、スナップショット専用のローカル リソースを持つクラウド サービス ロールを構成します。

1. クラウド サービス定義 (.csdef) ファイルを編集して、新しいローカル リソースをクラウド サービスに追加します。 次の例では、サイズが 5 GB の `SnapshotStore` というリソースを定義します。
   ```xml
   <LocalResources>
     <LocalStorage name="SnapshotStore" cleanOnRoleRecycle="false" sizeInMB="5120" />
   </LocalResources>
   ```

2. ロールの起動コードを変更して、`SnapshotStore` ローカル リソースを指す環境変数を追加します。 worker ロールの場合は、コードをロールの `OnStart` メソッドに追加する必要があります。
   ```csharp
   public override bool OnStart()
   {
       Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
       return base.OnStart();
   }
   ```
   Web ロール (ASP.NET) の場合は、コードを Web アプリケーションの `Application_Start` メソッドに追加する必要があります。
   ```csharp
   using Microsoft.WindowsAzure.ServiceRuntime;
   using System;

   namespace MyWebRoleApp
   {
       public class MyMvcApplication : System.Web.HttpApplication
       {
          protected void Application_Start()
          {
             Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
             // TODO: The rest of your application startup code
          }
       }
   }
   ```

3. ロールの ApplicationInsights.config ファイルを更新して、`SnapshotCollector` によって使用される一時フォルダーの場所をオーバーライドします。
   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Use the SnapshotStore local resource for snapshots -->
      <TempFolder>%SNAPSHOTSTORE%</TempFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

## <a name="overriding-the-shadow-copy-folder"></a>シャドウ コピー フォルダーのオーバーライド

Snapshot Collector を起動すると、Snapshot Uploader プロセスを実行するのに適したディスク上のフォルダーが検索されます。 選択されたフォルダーは、シャドウ コピー フォルダーと呼ばれます。

Snapshot Collector は、いくつかのよく知られている場所を確認し、Snapshot Uploader のバイナリをコピーするアクセス許可があることを確認します。 次の環境変数が使用されます。
- Fabric_Folder_App_Temp
- LOCALAPPDATA
- APPDATA
- TEMP

適切なフォルダーが見つからない場合は、Snapshot Collector によって " _"Could not find a suitable shadow copy folder (適切なシャドウ コピー フォルダーが見つかりません)"_ " というエラーが報告されます。

コピーに失敗した場合、Snapshot Collector は `ShadowCopyFailed` エラーを報告します。

アップローダーを起動できない場合、Snapshot Collector は `UploaderCannotStartFromShadowCopy` エラーを報告します。 メッセージの本文に `System.UnauthorizedAccessException` が含まれることがよくあります。 このエラーは、通常、アクセス許可が制限されたアカウントの下でアプリケーションが実行されているために発生します。 このアカウントには、シャドウ コピー フォルダーに書き込むためのアクセス許可が与えられていますが、コードを実行するためのアクセス許可が与えられていません。

これらのエラーは通常、起動時に発生するため、"_Uploader failed to start (アップローダーを起動できませんでした)_ " という `ExceptionDuringConnect` エラーが続いて表示されます。

これらのエラーを回避するには、`ShadowCopyFolder` 構成オプションを使用して、シャドウ コピー フォルダーを手動で指定します。 たとえば、ApplicationInsights.config を使用する場合は、次のように指定します。

   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Override the default shadow copy folder. -->
      <ShadowCopyFolder>D:\SnapshotUploader</ShadowCopyFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

または、.NET Core アプリケーションで appsettings.json を使用する場合は、次のように指定します。

   ```json
   {
     "ApplicationInsights": {
       "InstrumentationKey": "<your instrumentation key>"
     },
     "SnapshotCollectorConfiguration": {
       "ShadowCopyFolder": "D:\\SnapshotUploader"
     }
   }
   ```

## <a name="use-application-insights-search-to-find-exceptions-with-snapshots"></a>Application Insights 検索を使用してスナップショット付きの例外を検索する

スナップショットが作成されている場合、例外がスローされるとスナップショット ID がタグ付けされます。 Application Insights に例外が報告されると、そのスナップショット ID はカスタム プロパティとして含まれます。 Application Insights の **[検索]** を使用すると、`ai.snapshot.id` カスタム プロパティを持つすべてのレコードを見つけることができます。

1. Azure Portal の Application Insights のリソースを参照します。
2. **[Search]** を選択します。
3. [検索] テキスト ボックスに `ai.snapshot.id` と入力し、Enter キーを押します。

![ポータルでスナップショット ID を使用してテレメトリを検索](./media/snapshot-debugger/search-snapshot-portal.png)

この検索で結果が返されない場合は、選択された時間範囲内に Application Insights に報告されたスナップショットがなかったことを示しています。

アップローダー ログから特定のスナップショット ID を検索するには、[検索] ボックスにその ID を入力します。 アップロードされたことがわかっているスナップショットのレコードを検出できない場合は、こちらの手順に従ってください。

1. インストルメンテーション キーを確認することにより、正しい Application Insights のリソースを探していることを再確認します。

2. アップローダー ログからのタイムスタンプを使用して、その時間範囲をカバーするように検索の時間範囲フィルターを調整します。

それでもそのスナップショット ID の例外が表示されない場合、例外レコードは Application Insights に報告されていません。 この状況は、スナップショットの作成後、例外レコードを報告する前に、アプリケーションがクラッシュした場合に発生する可能性があります。 この場合、`Diagnose and solve problems` で App Service を確認し、予期しない再起動またはハンドルされない例外があったかどうかを確認します。

## <a name="edit-network-proxy-or-firewall-rules"></a>ネットワーク プロキシまたはファイアウォール規則を編集する

アプリケーションがプロキシまたはファイアウォール経由でインターネットに接続される場合は、スナップショット デバッガー サービスと通信するように規則を更新する必要がある場合があります。

Application Insights スナップショット デバッガーで使用される IP は、Azure Monitor サービス タグに含まれています。 詳細については、[サービス タグに関するドキュメント](../../virtual-network/service-tags-overview.md)を参照してください。