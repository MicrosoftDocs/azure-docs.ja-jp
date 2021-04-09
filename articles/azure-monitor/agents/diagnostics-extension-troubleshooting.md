---
title: Azure Diagnostics 拡張機能のトラブルシューティング
description: Azure Virtual Machines、Service Fabric、または Cloud Services で Azure Diagnostics を使うときの問題をトラブルシューティングします。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/08/2019
ms.openlocfilehash: 431b19595fbe2f5bc1f989e712c9c104af8e839b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101711520"
---
# <a name="azure-diagnostics-troubleshooting"></a>Azure Diagnostics のトラブルシューティング
この記事では、Azure Diagnostics の使用に関連するトラブルシューティング情報について説明します。 Azure Diagnostics の詳細については、[Azure Diagnostics の概要](diagnostics-extension-overview.md)に関するページを参照してください。

## <a name="logical-components"></a>論理コンポーネント
**診断プラグイン ランチャー (DiagnosticsPluginLauncher.exe)** :Azure Diagnostics 拡張機能を起動します。 エントリ ポイント プロセスとして機能します。

**診断プラグイン (DiagnosticsPlugin.exe)** :監視エージェントの有効期間を構成、起動、管理します。 ランチャーによって起動されるメイン プロセスです。

**監視エージェント (MonAgent\*.exe プロセス)** :診断データを監視、収集、転送します。  

## <a name="logartifact-paths"></a>ログ/アーティファクトのパス
以下は、いくつかの重要なログとアーティファクトのパスです。 以下の文章では、この情報を参照します。

### <a name="azure-cloud-services"></a>Azure クラウド サービス
| アーティファクト | Path |
| --- | --- |
| **Azure Diagnostics 構成ファイル** | %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<version>\Config.txt |
| **ログ ファイル** | C:\Logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<version>\ |
| **診断データのローカル ストア** | C:\Resources\Directory\<CloudServiceDeploymentID>.\<RoleName>.DiagnosticStore\WAD0107\Tables |
| **監視エージェント構成ファイル** | C:\Resources\Directory\<CloudServiceDeploymentID>.\<RoleName>.DiagnosticStore\WAD0107\Configuration\MaConfig.xml |
| **Azure Diagnostics 拡張機能パッケージ** | %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<version> |
| **ログ収集ユーティリティのパス** | %SystemDrive%\Packages\GuestAgent\ |
| **MonAgentHost ログ ファイル** | C:\Resources\Directory\<CloudServiceDeploymentID>.\<RoleName>.DiagnosticStore\WAD0107\Configuration\MonAgentHost.<seq_num>.log |

### <a name="virtual-machines"></a>仮想マシン
| アーティファクト | Path |
| --- | --- |
| **Azure Diagnostics 構成ファイル** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<version>\RuntimeSettings |
| **ログ ファイル** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\ |
| **診断データのローカル ストア** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD0107\Tables |
| **監視エージェント構成ファイル** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD0107\Configuration\MaConfig.xml |
| **状態ファイル** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<version>\Status |
| **Azure Diagnostics 拡張機能パッケージ** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>|
| **ログ収集ユーティリティのパス** | C:\WindowsAzure\Logs\WaAppAgent.log |
| **MonAgentHost ログ ファイル** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD0107\Configuration\MonAgentHost.<seq_num>.log |

## <a name="metric-data-doesnt-appear-in-the-azure-portal"></a>メトリック データが Azure ポータルに表示されない
Azure Diagnostics には、Azure ポータルに表示できるメトリック データが用意されています。 ポータルでのデータの表示に問題がある場合は、Azure Diagnostics ストレージ アカウントの WADMetrics\* テーブルを調べて、対応するメトリック レコードがそこにあるかどうかを確認し、[リソース プロバイダー](../../azure-resource-manager/management/resource-providers-and-types.md) Microsoft.Insights を確実に登録します。

ここでは、テーブルの **PartitionKey** がリソース ID、仮想マシン、または仮想マシン スケール セットです。 **RowKey** はメトリック名です (パフォーマンス カウンター名とも呼ばれています)。

リソース ID が間違っている場合は、**診断** **構成** > **メトリック** > **ResourceId** をチェックして、リソース ID が正しく設定されているかどうかを確認します。

特定のメトリックのデータがない場合は、 **[診断構成]**  > 、 **[PerformanceCounter]** で、メトリック (パフォーマンス カウンター) が含まれているかどうかを確認します。 次のカウンターは、既定で有効になっています。
- \Processor(_Total)\% Processor Time
- \Memory\Available Bytes
- \ASP.NET Applications(__Total__)\Requests/Sec
- \ASP.NET Applications(__Total__)\Errors Total/Sec
- \ASP.NET\Requests Queued
- \ASP.NET\Requests Rejected
- \Processor(w3wp)\% Processor Time
- \Process(w3wp)\Private Bytes
- \Process(WaIISHost)\% Processor Time
- \Process(WaIISHost)\Private Bytes
- \Process(WaWorkerHost)\% Processor Time
- \Process(WaWorkerHost)\Private Bytes
- \Memory\Page Faults/sec
- \.NET CLR Memory(_Global_)\% Time in GC
- \LogicalDisk(C:)\Disk Write Bytes/sec
- \LogicalDisk(C:)\Disk Read Bytes/sec
- \LogicalDisk(D:)\Disk Write Bytes/sec
- \LogicalDisk(D:)\Disk Read Bytes/sec

構成は正しく設定されているがそれでもメトリック データが表示されない場合、次の方針に沿って問題解決をお試しください。


## <a name="azure-diagnostics-is-not-starting"></a>Azure Diagnostics が起動しない
Azure Diagnostics が起動しない理由については、前述のログ ファイルの場所にある **DiagnosticsPluginLauncher.log** ファイルと **DiagnosticsPlugin.log** ファイルを参照してください。

これらのログで "`Monitoring Agent not reporting success after launch`" が示されている場合、MonAgentHost.exe の起動でエラーが発生したことを意味します。 前のセクションで `MonAgentHost log file` に対して指定されている場所でそのログを調べます。

ログ ファイルの最後の行には、終了コードが含まれています。  

```
DiagnosticsPluginLauncher.exe Information: 0 : [4/16/2016 6:24:15 AM] DiagnosticPlugin exited with code 0
```
終了コードが **負の数** の場合は、「[参照](#references)」セクションにある [終了コードの表](#azure-diagnostics-plugin-exit-codes)を参照してください。

## <a name="diagnostics-data-is-not-logged-to-azure-storage"></a>診断データが Azure Storage に記録されない
データがまったく表示されないのか、一部だけ表示されるのか、確認します。

### <a name="diagnostics-infrastructure-logs"></a>診断インフラストラクチャ ログ
Azure 診断では、あらゆるエラーが診断インフラストラクチャ ログに記録されます。 [診断インフラストラクチャ ログの記録](#how-to-check-diagnostics-extension-configuration)を有効にしていることを構成で確認してください。 有効になっていれば、構成したストレージ アカウントの `DiagnosticInfrastructureLogsTable` テーブルで、関連するエラーが表示されていないか確認できます。

### <a name="no-data-is-appearing"></a>データがまったく表示されない
イベント データがまったく表示されない最も一般的な理由は、ストレージ アカウント情報が間違って定義されていることにあります。

解決方法:診断構成を修正し、診断を再インストールします。

ストレージ アカウントが正しく構成されている場合、マシンにリモート アクセスし、*DiagnosticsPlugin.exe* と *MonAgentCore.exe* が実行されていることを確認します。 実行されていない場合、「[Azure Diagnostics が起動しない](#azure-diagnostics-is-not-starting)」の手順に従ってください。

プロセスが実行されている場合、「[データがローカルでキャプチャされているか](#is-data-getting-captured-locally)」に進み、そこの手順に従ってください。

これで問題が解決しない場合は、次のことを試してみてください。

1. エージェントのアンインストール
2. ディレクトリ C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics の削除
3. エージェントの再インストール


### <a name="part-of-the-data-is-missing"></a>一部のデータが見つからない
全部ではないが一部のデータが表示される場合、データ収集/転送のパイプラインは正しく設定されています。 以下、現象をさらに分類しています。これに基づいて問題を絞り込んでください。

#### <a name="is-the-collection-configured"></a>収集は構成されているか
診断構成には、特定の型のデータを収集する指示が含まれています。 [構成を見直し](#how-to-check-diagnostics-extension-configuration)、収集するように設定しているデータだけが調べられていることを確認してください。

#### <a name="is-the-host-generating-data"></a>ホストはデータを生成しているか
- **パフォーマンス カウンター**:perfmon を開き、カウンターを確認します。

- **トレース ログ**:VM にリモート アクセスし、アプリの構成ファイルに TextWriterTraceListener を追加します。  テキスト リスナーの設定方法については、 https://msdn.microsoft.com/library/sk36c28t.aspx をご覧ください。  `<trace>` 要素が `<trace autoflush="true">` になっていることを確認します。<br />
トレース ログが生成されていない場合、「トレース ログが見つからない場合 (詳細)」をご覧ください。

- **ETW トレース**:VM にリモート アクセスし、PerfView をインストールします。  PerfView で、 **[ファイル]**  >  **[ユーザー コマンド]**  >  の順に選択し、**Listen etwprovder1** >  を実行します。必要に応じてさらに **etwprovider2** 以降に Listen コマンドを実行します。 **Listen** コマンドは大文字と小文字が区別され、コンマ区切りの一覧の ETW プロバイダー間にスペースを使用することはできません。 コマンドを実行できない場合は、Perfview ツールの右下にある **[ログ]** ボタンを選択すると、実行しようとした内容とその結果を確認できます。  入力が正しいとすれば、新しいウィンドウが表示されます。 数秒後、ETW トレースが表示され始めます。

- **イベント ログ**:VM にリモート アクセスします。 `Event Viewer` を開き、イベントが存在することを確認します。

#### <a name="is-data-getting-captured-locally"></a>データはローカルでキャプチャされているか
次に、データがローカルでキャプチャされていることを確認します。
データは、診断データのローカル ストアの `*.tsf` ファイルにローカルに保存されます。 ログは、種類ごとに異なる `.tsf` ファイルに収集されます。 名前は、Azure Storage のテーブル名に類似しています。

たとえば、`Performance Counters` は `PerformanceCountersTable.tsf` で収集されます。 イベント ログは `WindowsEventLogsTable.tsf` で収集されます。 「[ローカル ログの抽出](#local-log-extraction)」の手順に従って、ローカルの収集ファイルを開き、ファイルがディスク上で収集されていることを確認します。

ログがローカルで収集されておらず、ホストがデータを生成していることを確認済みの場合は、構成に問題がある可能性があります。 構成を注意深く見直します。

MonitoringAgent MaConfig.xml に生成された構成も確認します。 関連ログ ソースについて説明するセクションがあることを確認します。 次に、診断構成と監視エージェント構成の間の変換で失われていないことを確認します。

#### <a name="is-data-getting-transferred"></a>データは転送されているか
ローカルでキャプチャされていることを確認済みのデータがストレージ アカウントに表示されない場合、次の手順に従ってください。

- 正しいストレージ アカウントを指定したことと、指定したストレージ アカウントのキーなどがロールオーバーされていないことを確認します。 Azure クラウド サービスでは、`useDevelopmentStorage=true` が更新されていない場合があります。

- 指定したストレージ アカウントが正しいことを確認してください。 コンポーネントによるパブリック ストレージのエンドポイントへのアクセスがネットワーク制限で禁止されていないことを確認してください。 そのための方法の 1 つとして、マシンにリモート アクセスし、同じストレージ アカウントに何らかの書き込みを行います。

- 最後に、監視エージェントによって報告されたエラーを確認できます。 監視エージェントは、診断データのローカル ストアにある `maeventtable.tsf` にそのログを書き込みます。 「[ローカル ログの抽出](#local-log-extraction)」セクションにある方法でこのファイルを開きます。 ローカル ファイルの読み込みやストレージへの書き込みで発生したエラーを示す `errors` がないか探してみてください。

### <a name="capturing-and-archiving-logs"></a>ログのキャプチャ/アーカイブ
サポートに問い合わせることを検討している場合、最初にマシンのログを収集するように求められることがあります。 この作業を自分で行うことで時間を節約できます。 ログ コレクション ユーティリティ パスで `CollectGuestLogs.exe` ユーティリティを実行します。 すべての関連 Azure ログを含む .zip ファイルが同じフォルダーに生成されます。

## <a name="diagnostics-data-tables-not-found"></a>診断データ テーブルが見つからない
ETW イベントを保持する Azure Storage 内のテーブルの名前には、次のコードを使います。

```csharp
        if (String.IsNullOrEmpty(eventDestination)) {
            if (e == "DefaultEvents")
                tableName = "WADDefault" + MD5(provider);
            else
                tableName = "WADEvent" + MD5(provider) + eventId;
        }
        else
            tableName = "WAD" + eventDestination;
```

たとえば次のようになります。

```XML
        <EtwEventSourceProviderConfiguration provider="prov1">
          <Event id="1" />
          <Event id="2" eventDestination="dest1" />
          <DefaultEvents />
        </EtwEventSourceProviderConfiguration>
        <EtwEventSourceProviderConfiguration provider="prov2">
          <DefaultEvents eventDestination="dest2" />
        </EtwEventSourceProviderConfiguration>
```
```JSON
"EtwEventSourceProviderConfiguration": [
    {
        "provider": "prov1",
        "Event": [
            {
                "id": 1
            },
            {
                "id": 2,
                "eventDestination": "dest1"
            }
        ],
        "DefaultEvents": {
            "eventDestination": "DefaultEventDestination",
            "sinks": ""
        }
    },
    {
        "provider": "prov2",
        "DefaultEvents": {
            "eventDestination": "dest2"
        }
    }
]
```
このコードは 4 つのテーブルを生成します。

| Event | テーブル名 |
| --- | --- |
| provider="prov1" &lt;Event id="1" /&gt; |WADEvent+MD5("prov1")+"1" |
| provider="prov1" &lt;Event id="2" eventDestination="dest1" /&gt; |WADdest1 |
| provider="prov1" &lt;DefaultEvents /&gt; |WADDefault+MD5("prov1") |
| provider="prov2" &lt;DefaultEvents eventDestination="dest2" /&gt; |WADdest2 |

## <a name="references"></a>References

### <a name="how-to-check-diagnostics-extension-configuration"></a>診断拡張機能の構成を確認する方法
拡張機能の構成を確認する最も簡単な方法としては、[Azure Resource Explorer](https://resources.azure.com) に移動し、Azure Diagnostics 拡張機能 (IaaSDiagnostics / PaaDiagnostics) がインストールされている仮想マシンまたはクラウド サービスに移動します。

または、マシンにリモート デスクトップ接続し、Azure 診断構成ファイルを確認します。詳細は、ログ アーティファクト パス セクションにあります。

どちらの場合でも、**Microsoft.Azure.Diagnostics** を検索し、**xmlCfg** または **WadCfg** フィールドを見つけます。

仮想マシンで検索し、**WadCfg** フィールドが存在する場合、構成が JSON 形式であることを意味します。 **xmlCfg** フィールドが存在する場合、構成が XML であり、base64 でエンコードされていることを意味します。 診断で読み込まれた XML を表示するには、これを[デコードする](https://www.bing.com/search?q=base64+decoder)必要があります。

クラウド サービス ロールでは、ディスクから構成を選択する場合、データは base64 でエンコードされているため、診断で読み込まれた XML を表示するにはこれを[デコードする](https://www.bing.com/search?q=base64+decoder)必要があります。

### <a name="azure-diagnostics-plugin-exit-codes"></a>Azure Diagnostics プラグインの終了コード
プラグインにより、次の終了コードが返されます。

| 終了コード | 説明 |
| --- | --- |
| 0 |正常終了しました。 |
| -1 |一般的なエラー。 |
| -2 |rcf ファイルを読み込めませんでした。<p>この内部エラーは、ゲスト エージェント プラグインの起動ツールが VM 上で不正に手動で起動された場合にのみ発生します。 |
| -3 |診断構成ファイルを読み込めませんでした。<p><p>解決方法:構成ファイルがスキーマ検証を渡さないことが原因です。 スキーマに準拠する構成ファイルを使用してください。 |
| -4 |監視エージェント診断の別のインスタンスが既にローカル リソース ディレクトリを使用しています。<p><p>解決方法:**LocalResourceDirectory** に別の値を指定します。 |
| -6 |ゲスト エージェント プラグインの起動ツールが、無効なコマンド ラインを使用して診断を起動しようとしました。<p><p>この内部エラーは、ゲスト エージェント プラグインの起動ツールが VM 上で不正に手動で起動された場合にのみ発生します。 |
| -10 |診断がハンドルされていない例外で終了しました。 |
| -11 |ゲスト エージェントが、監視エージェントの起動と監視を処理するプロセスを作成できませんでした。<p><p>解決方法:新しいプロセスを起動するのに十分なシステム リソースを使用できることを確認します。<p> |
| -101 |診断プラグインを呼び出すときの引数が無効です。<p><p>この内部エラーは、ゲスト エージェント プラグインの起動ツールが VM 上で不正に手動で起動された場合にのみ発生します。 |
| -102 |プラグイン プロセスの初期化でエラーが発生しました。<p><p>解決方法:新しいプロセスを起動するのに十分なシステム リソースを使用できることを確認します。 |
| -103 |プラグイン プロセスの初期化でエラーが発生しました。 具体的にはロガー オブジェクトを作成できません。<p><p>解決方法:新しいプロセスを起動するのに十分なシステム リソースを使用できることを確認します。 |
| -104 |ゲスト エージェントが指定した rcf ファイルを読み込むことができません。<p><p>この内部エラーは、ゲスト エージェント プラグインの起動ツールが VM 上で不正に手動で起動された場合にのみ発生します。 |
| -105 |診断プラグインが診断構成ファイルを開くことができません。<p><p>この内部エラーは、診断プラグインが VM 上で不正に手動で起動された場合にのみ発生します。 |
| -106 |診断構成ファイルを読み取れませんでした。<p><p>構成ファイルがスキーマ検証を渡さないことが原因です。 <br><br>解決方法:スキーマに準拠する構成ファイルを指定してください。 詳細については、「[診断拡張機能の構成を確認する方法](#how-to-check-diagnostics-extension-configuration)」を参照してください。 |
| -107 |監視エージェントへのリソース ディレクトリのパスが無効です。<p><p>この内部エラーは、監視エージェントが VM 上で不正に手動で起動された場合にのみ発生します。</p> |
| -108 |診断構成ファイルを監視エージェント構成ファイルに変換できません。<p><p>この内部エラーは、診断プラグインが無効な構成ファイルを使用して手動で起動された場合にのみ発生します。 |
| -110 |一般的な診断構成エラー。<p><p>この内部エラーは、診断プラグインが無効な構成ファイルを使用して手動で起動された場合にのみ発生します。 |
| -111 |監視エージェントを開始できません。<p><p>解決方法:十分なシステム リソースを使用できることを確認します。 |
| -112 |一般的なエラー |

### <a name="local-log-extraction"></a>ローカル ログの抽出
監視エージェントは、ログとアーティファクトを `.tsf` ファイルとして収集します。 `.tsf` ファイルは読み取り可能ではありませんが、次の方法で `.csv` に変換できます。

```
<Azure diagnostics extension package>\Monitor\x64\table2csv.exe <relevantLogFile>.tsf
```
対応する `.tsf` ファイルと同じパスに新しいファイル `<relevantLogFile>.csv` が作成されます。

>[!NOTE]
> 必要なのは、メインの tsf ファイル (PerformanceCountersTable.tsf など) に対してこのユーティリティを実行することのみです。 付随するファイル (PerformanceCountersTables_\*\*001.tsf、PerformanceCountersTables_\*\*002.tsf など) は自動的に処理されます。

### <a name="more-about-missing-trace-logs"></a>トレース ログが見つからない場合 (詳細)

>[!NOTE]
> 以下の情報は主に、IaaS VM で実行されているアプリケーションで DiagnosticsMonitorTraceListener を構成した場合を除き、Azure クラウド サービスに当てはまります。

- **DiagnosticMonitorTraceListener** が web.config または app.config で構成されていることを確認します。これは既定でクラウド サービス プロジェクトで構成されます。 ただし、ユーザーがこれをコメントアウトすることもあります。その場合、診断でトレース ステートメントが収集されることはありません。

- ログが **OnStart** メソッドまたは **Run** メソッドから書き込まれていない場合は、**DiagnosticMonitorTraceListener** が app.config に含まれていることを確認します。既定では、これは web.config にありますが、w3wp.exe 内で実行されているコードにのみ適用されます。 そのため、WaIISHost.exe で実行されているトレースを保存するために app.config で必要になります。

- **Diagnostics.Debug.WriteXXX** ではなく **Diagnostics.Trace.TraceXXX** を使用していることを確認します。 デバッグ ステートメントはリリース ビルドから削除されます。

- コンパイルしたコードに **Diagnostics.Trace 行** があることを確認します (Reflector、ildasm、または ILSpy を使用して確認してください)。 条件付きコンパイル シンボルである TRACE を使用しない限り、**Diagnostics.Trace** コマンドはコンパイルしたバイナリから削除されます。 これは msbuild でプロジェクトを構築するときに発生する一般的な問題です。   

## <a name="known-issues-and-mitigations"></a>既知の問題と軽減策
既知の問題と軽減策の一覧を次に示します。

**1. .NET 4.5 の依存関係**

Windows Azure Diagnostics 拡張機能は .NET 4.5 framework 以降にランタイムが依存します。 このドキュメントの作成時点では、Azure クラウド サービス用にプロビジョニングされたすべてのマシンと Azure 仮想マシンに基づくすべての公式イメージに .NET 4.5 以降がインストールされています。

.NET 4.5 以降がインストールされていない Windows Azure Diagnostics 拡張機能を実行する状況も考えられます。 古いイメージやスナップショットからマシンを作成するときや、カスタム ディスクを利用するときです。

通常、**DiagnosticsPluginLauncher.exe** の実行時には、終了コード **255** が表示されます。 次のように、例外が処理されないことが原因でエラーが発生します。
```
System.IO.FileLoadException: Could not load file or assembly 'System.Threading.Tasks, Version=1.5.11.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a' or one of its dependencies
```

**対応策:** .NET 4.5 以降をマシンにインストールします。

**2.パフォーマンス カウンターのデータがストレージでは使用できるが、ポータルに表示されない**

既定では、仮想マシンのポータルに特定のパフォーマンス カウンターが表示されます。 パフォーマンス カウンターは表示されないが、データが生成されていることはわかっている (データがストレージで使用できるため) 場合、以下を確認してください。

- ストレージ内のデータのカウンター名が英語であるかどうか。 カウンターの名前が英語でない場合、ポータルのメトリック グラフは名前を認識できません。 **対応策**:システム アカウント用に、マシンの言語を英語に変更します。 **[コントロール パネル]**  >  **[地域と言語]**  >  **[管理]**  >  **[設定のコピー]** の順に選択します。 次に、 **[ようこそ画面とシステム アカウント]** の選択を解除し、カスタム言語がシステム アカウントに適用されないようにします。

- パフォーマンス カウンター名にワイルドカード (\*) を使用している場合、パフォーマンス カウンターが Azure Storage シンクに送られるときに、構成済みのカウンターと収集されたカウンターをポータルが関連付けることができなくなります。 **対応策**:ワイルドカードを使用でき、ポータルで (\*) を展開できることを確認するために、ご自分のパフォーマンス カウンターを Azure Monitor シンクにルーティングします。
