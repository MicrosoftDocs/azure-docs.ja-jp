---
title: Azure Application Insights Snapshot Debugger に関する問題のトラブルシューティング | Microsoft Docs
description: この記事では、Application Insights Snapshot Debugger の有効化または使用で問題が発生している開発者に役立つ、トラブルシューティングの手順と情報を示します。
services: application-insights
documentationcenter: ''
author: brahmnes
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 03/07/2019
ms.author: mbullwin
ms.openlocfilehash: 25ccf20fc78a9ec00d4dfe23a60e824e96d12945
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444545"
---
# <a id="troubleshooting"></a> Application Insights Snapshot Debugger の有効化やスナップショットの表示に関する問題のトラブルシューティング
アプリケーションに対して Application Insights Snapshot Debugger を有効にしたにもかかわらず、例外のスナップショットが表示されない場合は、次の手順を使用してトラブルシューティングを行うことができます。 スナップショットが生成されない理由としては、さまざまなことが考えられます。 スナップショットの正常性チェックを実行すると、いくつかの一般的な原因を特定できます。

## <a name="use-the-snapshot-health-check"></a>スナップショットの正常性チェックを使用する
いくつかの一般的な問題により、[デバッグ スナップショットを開く] が表示されません。 古い Snapshot Collector を使用します (たとえば、1 日のアップロード上限に達した場合)。そうしないと、スナップショットのアップロードに時間がかかることがあります。 一般的な問題のトラブルシューティングには、Snapshot Health Check を使用します。

エンドツーエンドのトレース ビューの例外ウィンドウには、Snapshot Health Check に接続できるリンクが表示されます。

![スナップショットの正常性チェックを入力する](./media/snapshot-debugger/enter-snapshot-health-check.png)

インタラクティブでチャットのようなインターフェイスでは、一般的な問題が検索され、問題の解決が案内されます。

![正常性チェック](./media/snapshot-debugger/healthcheck.png)

それでも問題が解決しない場合は、以下の手動トラブルシューティング手順を参照してください。

## <a name="verify-the-instrumentation-key"></a>インストルメンテーション キーの確認

公開したアプリケーションで、正しいインストルメンテーション キーを使用していることを確認します。 通常、インストルメンテーション キーは、ApplicationInsights.config ファイルから読み取られます。 値が、ポータルに表示される Application Insights リソースのインストルメンテーション キーと同じであることを確認します。

## <a name="preview-versions-of-net-core"></a>.NET Core のプレビュー バージョン
アプリケーションにプレビュー バージョンの .NET Core が使用され、ポータルの [Application Insights ウィンドウ](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)でスナップショット デバッガーが有効な場合、スナップショット デバッガーが起動しない場合があります。 まず「[その他の環境用にスナップショット デバッガーを有効にする](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)」の手順を実行して [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet パッケージをアプリケーションに含め、"***さらに***" [Application Insights ウィンドウで有効にします](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)。


## <a name="upgrade-to-the-latest-version-of-the-nuget-package"></a>最新バージョンの NuGet にアップグレードする

[ポータルの [Application Insights] ウィンドウ](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)を通じて Snapshot Debugger を有効にした場合、アプリケーションでは既に最新の NuGet パッケージが実行されています。 [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet パッケージを含めることで Snapshot Debugger を有効にした場合は、Visual Studio の NuGet パッケージ マネージャーを使用して、Microsoft.ApplicationInsights.SnapshotCollector の最新バージョンが使用されているかどうかを確認してください。 リリース ノートについては https://github.com/Microsoft/ApplicationInsights-Home/issues/167 を参照してください。

## <a name="check-the-uploader-logs"></a>アップローダー ログの確認

スナップショットの作成後、ミニダンプ ファイル (.dmp) がディスク上に作成されます。 個別アップローダー プロセスでは、そのミニダンプ ファイルを作成し、これを関連する PDB と共に Application Insights のスナップショット デバッガーのストレージにアップロードします。 ミニダンプは、正常にアップロードされた後、ディスクから削除されます。 アップローダー プロセスのログ ファイルは、ディスク上に保持されます。 App Service 環境では、これらのログは `D:\Home\LogFiles` にあります。 App Service の Kudu 管理サイトを使用すると、これらのログ ファイルを検索できます。

1. Azure Portal で App Service アプリケーションを開きます。
2. **[高度なツール]** をクリックするか、**Kudu** を検索します。
3. **[Go]** をクリックします。
4. **[Debug console]** (デバッグ コンソール) ドロップダウン リスト ボックスで、 **[CMD]** を選択します。
5. **[LogFiles]** をクリックします。

`Uploader_` または `SnapshotUploader_`で始まる名前で拡張子が `.log` であるファイルが 1 つ以上表示されます。 適切なアイコンをクリックしてログ ファイルをダウンロードするか、またはブラウザーでログ ファイルを開きます。
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
ミニダンプは ID `139e411a23934dc0b9ea08a626db16c5` を持つスナップショットに関連付けられています。 この ID は、Application Insights Analytics で関連する例外テレメトリを検索するために後で使用できます。

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
クラウド サービスのロールでは、既定の一時フォルダーが minidump ファイルを保持するためには小さすぎる場合があり、スナップショットが失われる可能性があります。
必要な領域は、アプリケーションの合計ワーキング セットと同時実行スナップショット数によって異なります。
32 ビット ASP.NET web ロールのワーキング セットは、通常は 200 MB から 500 MB です。
少なくとも 2 つの同時実行スナップショットを許可します。
たとえば、アプリケーションが 1 GB の合計ワーキング セットを使用する場合は、スナップショットを格納するために少なくとも 2 GB のディスク領域があることを確認する必要があります。
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

適切なフォルダーが見つからない場合は、Snapshot Collector によって "_Could not find a suitable shadow copy folder (適切なシャドウ コピー フォルダーが見つかりません)_ " エラーが報告されます。

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

スナップショットが作成されている場合、例外がスローされるとスナップショット ID がタグ付けされます。 Application Insights に例外テレメトリがレポートされると、そのスナップショット ID はカスタム プロパティとして含まれます。 Application Insights の **[検索]** を使用すると、`ai.snapshot.id` カスタム プロパティを使用してすべてのテレメトリを見つけることができます。

1. Azure Portal の Application Insights のリソースを参照します。
2. **[Search (検索)]** をクリックします。
3. [検索] テキスト ボックスに `ai.snapshot.id` と入力し、Enter キーを押します。

![ポータルでスナップショット ID を使用してテレメトリを検索](./media/snapshot-debugger/search-snapshot-portal.png)

この検索に結果が返されない場合、アプリケーションが選択した時間範囲に Application Insights にスナップショットをレポートしなかったということです。

アップローダー ログから特定のスナップショット ID を検索するには、[検索] ボックスにその ID を入力します。 既知のスナップショットのテレメトリを検索できない場合、次の手順に従います。

1. インストルメンテーション キーを確認することにより、正しい Application Insights のリソースを探していることを再確認します。

2. アップローダー ログからのタイムスタンプを使用して、その時間範囲をカバーするように検索の時間範囲フィルターを調整します。

それでもそのスナップショット ID を持つ例外が表示されない場合、その例外テレメトリが Application Insights にレポートされなかったということです。 このような状況は、スナップショットを取得した後かつ例外テレメトリにレポートする前にアプリケーションがクラッシュした場合に発生する可能性があります。 この場合、`Diagnose and solve problems` で App Service を確認し、予期しない再起動またはハンドルされない例外があったかどうかを確認します。

## <a name="edit-network-proxy-or-firewall-rules"></a>ネットワーク プロキシまたはファイアウォール規則を編集する

アプリケーションがプロキシまたはファイアウォールを介してインターネットに接続する場合は、アプリケーションがスナップショット デバッガーサービスと通信できるように規則を編集する必要があります。 こちらの[スナップショット デバッガーで使用される IP アドレスとポートの一覧](../../azure-monitor/app/ip-addresses.md#snapshot-debugger)を参照してください。
