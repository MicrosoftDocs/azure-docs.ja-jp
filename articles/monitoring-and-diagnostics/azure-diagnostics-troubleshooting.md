---
title: "Azure 診断のトラブルシューティング | Microsoft Docs"
description: "Azure Virtual Machines、Service Fabric、または Cloud Services で Azure 診断を使うときの問題をトラブルシューティングします。"
services: monitoring-and-diagnostics
documentationcenter: .net
author: rboucher
manager: carmonm
editor: 
ms.assetid: 66469bce-d457-4d1e-b550-a08d2be4d28c
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 07/12/2017
ms.author: robb
ms.translationtype: HT
ms.sourcegitcommit: 646886ad82d47162a62835e343fcaa7dadfaa311
ms.openlocfilehash: a0cb529836b14df71e83616f4f625a002c535b7b
ms.contentlocale: ja-jp
ms.lasthandoff: 08/25/2017

---
# <a name="azure-diagnostics-troubleshooting"></a>Azure 診断のトラブルシューティング
Azure 診断の使用に関連する情報をトラブルシューティングします。 Azure 診断の詳細については、 [Azure 診断の概要](azure-diagnostics.md)に関するページを参照してください。

## <a name="logical-components"></a>論理コンポーネント
**診断プラグイン ランチャー (DiagnosticsPluginLauncher.exe)**: Azure 診断拡張機能を起動します。 エントリ ポイント プロセスとして機能します。

**診断プラグイン (DiagnosticsPlugin.exe)**: 前述のランチャーによって起動されるメイン プロセス。監視エージェントの構成と起動、およびその有効期間の管理を行います。 

**監視エージェント (MonAgent\*.exe プロセス)**: これらのプロセスが大部分の処理 (診断データの監視、収集、転送) を実行します。  

## <a name="logartifact-paths"></a>ログ/アーティファクトのパス
ここでは、いくつかの重要なログとアーティファクトのパスを示します。 ドキュメントの残りの部分では、これらのパスを参照します。
### <a name="cloud-services"></a>Cloud Services
| アーティファクト | パス |
| --- | --- |
| **Azure 診断構成ファイル** | %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<バージョン>\Config.txt |
| **ログ ファイル** | C:\Logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<バージョン>\ |
| **診断データのローカル ストア** | C:\Resources\Directory\<クラウド サービスのデプロイ ID>.\<ロール名>.DiagnosticStore\WAD0107\Tables |
| **監視エージェント構成ファイル** | C:\Resources\Directory\<クラウド サービスのデプロイ ID>.\<ロール名>.DiagnosticStore\WAD0107\Configuration\MaConfig.xml |
| **Azure 診断拡張機能パッケージ** | %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<バージョン> |
| **ログ収集ユーティリティのパス** | %SystemDrive%\Packages\GuestAgent\ |
| **MonAgentHost ログ ファイル** | C:\Resources\Directory\<CloudServiceDeploymentID>.\<RoleName>.DiagnosticStore\WAD0107\Configuration\MonAgentHost.<シーケンシャル番号>.log |

### <a name="virtual-machines"></a>Virtual Machines
| アーティファクト | パス |
| --- | --- |
| **Azure 診断構成ファイル** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<バージョン>\RuntimeSettings |
| **ログ ファイル** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<バージョン>\Logs\ |
| **診断データのローカル ストア** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<診断のバージョン>\WAD0107\Tables |
| **監視エージェント構成ファイル** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<診断のバージョン>\WAD0107\Configuration\MaConfig.xml |
| **状態ファイル** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<バージョン>\Status |
| **Azure 診断拡張機能パッケージ** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<診断のバージョン>|
| **ログ収集ユーティリティのパス** | C:\WindowsAzure\Packages |
| **MonAgentHost ログ ファイル** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD0107\Configuration\MonAgentHost.<シーケンシャル番号>.log |

## <a name="metric-data-doesnt-show-in-azure-portal"></a>メトリック データが Azure ポータルに表示されない
Microsoft Azure 診断には、Azure ポータルに表示できる多数のメトリック データが用意されています。 これらのデータのポータルでの表示に問題がある場合は、診断ストレージ アカウント の WADMetrics\* テーブルで、該当するメトリック レコードが存在するかどうかを確認します。 ここで、テーブルの PartitionKey は、仮想マシンまたは仮想マシン スケール セットのリソース ID であり、RowKey は、メトリック名 (つまりパフォーマンス カウンターの名前) です。

リソース ID が間違っている場合は、[診断構成] -> [メトリック] -> [ResourceId] で、リソース ID が正しく設定されているかどうかを確認します。

特定のメトリックのデータがない場合は、[診断構成] -> [PerformanceCounter] で、メトリック (パフォーマンス カウンター) が含まれているかどうかを確認します。 次のカウンターは、既定で有効になっています。
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

構成は正しく設定されているにもかかわらず、メトリック データを表示できない場合は、以下のガイドラインに従って、さらに調査してください。


## <a name="azure-diagnostics-is-not-starting"></a>Azure 診断が起動しない
診断が起動しない理由について詳しくは、前述のログ ファイルの場所にある **DiagnosticsPluginLauncher.log** ファイルと **DiagnosticsPlugin.log** ファイルを参照してください。 

これらのログで "`Monitoring Agent not reporting success after launch`" が示されている場合、MonAgentHost.exe の起動でエラーが発生したことを意味します。 前のセクションで `MonAgentHost log file` に対して指定されている場所でそのログを調べます。

ログ ファイルの最後の行には、終了コードが含まれています。  

```
DiagnosticsPluginLauncher.exe Information: 0 : [4/16/2016 6:24:15 AM] DiagnosticPlugin exited with code 0
```
終了コードが**負の数**の場合は、「[参照](#references)」にある[終了コードの表](#azure-diagnostics-plugin-exit-codes)を参照してください。

## <a name="diagnostics-data-is-not-logged-to-azure-storage"></a>診断データが Azure Storage に記録されない
データがまったく表示されていないか、一部のデータのみが表示されていないかを確認します。

### <a name="diagnostics-infrastructure-logs"></a>Diagnostics Infrastructure Logs
診断インフラストラクチャ ログには、Azure 診断によって検出されたエラーが記録されます。 診断インフラストラクチャ ログのキャプチャが構成で有効になっていることを確認し (方法については、[こちら](#how-to-check-diagnostics-extension-configuration)を参照)、構成済みのストレージ アカウントで `DiagnosticInfrastructureLogsTable` テーブルに表示されている関連エラーを探してください。

### <a name="no-data-is-showing-up"></a>データが表示されない
イベント データが見つからない最も一般的な原因は、不正に定義されたストレージ アカウント情報です。

解決方法: 診断構成を修正し、診断を再インストールします。

ストレージ アカウントが正しく構成されている場合は、マシンにリモート デスクトップ接続し、DiagnosticsPlugin.exe と MonAgentCore.exe が実行されていることを確認します。 これらが実行されていない場合は、「[Microsoft Azure 診断が起動しない](#azure-diagnostics-is-not-starting)」に記載されている手順に従ってください。 プロセスが実行されている場合は、「[データがローカルでキャプチャされている](#is-data-getting-captured-locally)」に移動し、以降の手順に従ってください。

### <a name="part-of-the-data-is-missing"></a>一部のデータが見つからない
一部のデータのみ取得する場合です。 つまり、データの収集/転送パイプラインが正しく設定されています。 問題を絞り込むには、以下のサブセクションに記載されている手順に従ってください。
#### <a name="is-collection-configured"></a>収集が構成されている 
診断構成には、特定の型のデータを収集するように指示する部分が含まれています。 [構成を確認](#how-to-check-diagnostics-extension-configuration)し、収集用に構成されていないデータを探していないかどうかをチェックします。
#### <a name="is-the-host-generating-data"></a>ホストがデータを生成している
- **パフォーマンス カウンター**: Perfmon を開き、カウンターを確認します。
- **トレース ログ**: VM にリモート デスクトップ接続し、アプリケーションの構成ファイルに TextWriterTraceListener を追加します。  http://msdn.microsoft.com/library/sk36c28t.aspx を参照し、テキスト リスナーをセットアップしてください。  `<trace>` 要素が `<trace autoflush="true">` になっていることを確認します。<br />
トレース ログが生成されてない場合は、「[トレース ログが見つからない場合の詳細](#more-about-trace-logs-missing)」に記載されている手順に従ってください。
- **ETW トレース**: VM にリモート デスクトップ接続し、PerfView をインストールします。  PerfView で、[ファイル] -> [ユーザー コマンド] -> [etwprovder1 のリッスン] や [etwprovider2 のリッスン] を実行します。Listen コマンドは大文字と小文字が区別され、コンマ区切りの一覧の ETW プロバイダー間にスペースを使用することはできません。  コマンドを実行できない場合は、Perfview ツールの右下にある [ログ] ボタンをクリックすると、実行しようとした内容とその結果を確認できます。  入力が正しい場合は、新しいウィンドウがポップアップ表示され、数秒で ETW トレースの表示が開始されます。
- **イベント ログ**: VM にリモート デスクトップ接続します。 `Event Viewer` を開き、イベントが存在することを確認します。
#### <a name="is-data-getting-captured-locally"></a>データがローカルでキャプチャされている: 
次に、データがローカルでキャプチャされていることを確認します。
データは、[診断データのローカル ストア](#log-artifacts-path)の `*.tsf` ファイルにローカルに保存されます。 ログは、種類ごとに異なる `.tsf` ファイルに収集されます。 名前は、Azure Storage のテーブル名に類似しています。 たとえば、`Performance Counters` は `PerformanceCountersTable.tsf` に収集され、イベント ログは `WindowsEventLogsTable.tsf` に収集されます。 「[ローカル ログの抽出](#local-log-extraction)」の手順に従って、ローカルの収集ファイルを開き、ファイルがディスク上で収集されていることを確認します。

ログがローカルで収集されておらず、ホストがデータを生成していることを確認済みの場合は、構成に問題がある可能性があります。 構成の適切なセクションを慎重に確認してください。 また、監視エージェントの [MaConfig.xml](#log-artifacts-path) 用に生成された構成を確認し、関連するログ ソースについて記述したセクションがあること、および Azure 診断構成と監視エージェント構成の間の変換でそのセクションが失われていないことをチェックしてください。
#### <a name="is-data-getting-transferred"></a>データが転送されている
ローカルでキャプチャされていることを確認済みのデータがストレージ アカウントに表示されない場合は、次の手順を実行してください。 
- 最初に、正しいストレージ アカウントを指定したこと、および指定したストレージ アカウントのキーなどがロールオーバーされていないことを確認します。 クラウド サービスでは、`useDevelopmentStorage=true` が更新されていない場合があります。
- 指定したストレージ アカウントが正しい場合は、 コンポーネントによるパブリック ストレージのエンドポイントへのアクセスがネットワーク制限で禁止されていないことを確認してください。 そのための方法の 1 つとして、マシンにリモート デスクトップ接続し、同じストレージ アカウントに何らかの書き込みを行います。
- 最後に、監視エージェントによって報告されたエラーを確認できます。 監視エージェントは、[診断データのローカル ストア](#log-artifacts-path)にある `maeventtable.tsf` にそのログを書き込みます。 「[ローカル ログの抽出](#local-log-extraction)」の手順に従って、このファイルを開き、ローカル ファイルの読み取りまたはストレージへの書き込みができなかったことを示す `errors` があるかどうかを確認してください。

### <a name="capturing--archiving-logs"></a>ログのキャプチャ/アーカイブ
前述の手順を実行しても問題を特定できず、サポートへの問い合わせを検討しているとします。 最初に、マシンのログを収集するようにサポートから求められる可能性があります。 この作業を自分で行うことで時間を節約できます。 [ログ収集ユーティリティのパス](#log-artifacts-path)で `CollectGuestLogs.exe` ユーティリティを実行します。関連するすべての Azure ログを含む zip ファイルが同じフォルダーに生成されます。

## <a name="diagnostics-data-tables-not-found"></a>診断データ テーブルが見つからない
ETW イベントを保持する Azure Storage 内のテーブルの名前には、次のコードを使います。

```C#
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
4 つのテーブルが生成されます。

| イベント | テーブル名 |
| --- | --- |
| provider=”prov1” &lt;Event id=”1” /&gt; |WADEvent+MD5(“prov1”)+”1” |
| provider=”prov1” &lt;Event id=”2” eventDestination=”dest1” /&gt; |WADdest1 |
| provider=”prov1” &lt;DefaultEvents /&gt; |WADDefault+MD5(“prov1”) |
| provider=”prov2” &lt;DefaultEvents eventDestination=”dest2” /&gt; |WADdest2 |

## <a name="references"></a>参照

### <a name="how-to-check-diagnostics-extension-configuration"></a>診断拡張機能の構成を確認する方法
拡張機能の構成を確認する最も簡単な方法としては、http://resources.azure.com にアクセスし、Azure 診断拡張機能 (IaaSDiagnostics / PaaDiagnostics) がインストールされている仮想マシンまたはクラウド サービスに移動します。

または、マシンにリモート デスクトップ接続し、[該当するセクション](#log-artifacts-path)で説明されている Azure 診断構成ファイルを確認します。

どちらの場合でも、**Microsoft.Azure.Diagnostics** を検索し、**xmlCfg** または **WadCfg** フィールドを見つけます。 

仮想マシンでは、WadCfg フィールドが存在する場合は、構成が JSON であることを意味します。 xmlCfg フィールドが存在する場合は、構成が XML であり、base64 でエンコードされていることを意味します。 診断で読み込まれた XML を表示するには、これを[デコードする](http://www.bing.com/search?q=base64+decoder)必要があります。

クラウド サービス ロールでは、ディスクから構成を選択する場合、データは base64 でエンコードされているため、診断で読み込まれた XML を表示するにはこれを[デコードする](http://www.bing.com/search?q=base64+decoder)必要があります。

### <a name="azure-diagnostics-plugin-exit-codes"></a>Azure 診断プラグインの終了コード
プラグインにより、次の終了コードが返されます。

| 終了コード | 説明 |
| --- | --- |
| 0 |成功。 |
| -1 |一般的なエラー。 |
| -2 |rcf ファイルを読み込めませんでした。<p>この内部エラーは、ゲスト エージェント プラグインの起動ツールが VM 上で不正に手動で起動された場合にのみ発生します。 |
| -3 |診断構成ファイルを読み込めませんでした。<p><p>解決方法: 構成ファイルがスキーマ検証を渡さないことが原因です。 スキーマに準拠する構成ファイルを使用してください。 |
| -4 |監視エージェント診断の別のインスタンスが既にローカル リソース ディレクトリを使用しています。<p><p>解決方法: **LocalResourceDirectory** に別の値を指定します。 |
| -6 |ゲスト エージェント プラグインの起動ツールが、無効なコマンド ラインを使用して診断を起動しようとしました。<p><p>この内部エラーは、ゲスト エージェント プラグインの起動ツールが VM 上で不正に手動で起動された場合にのみ発生します。 |
| -10 |診断がハンドルされていない例外で終了しました。 |
| -11 |ゲスト エージェントが、監視エージェントの起動と監視を処理するプロセスを作成できませんでした。<p><p>解決方法: 新しいプロセスを起動するのに十分なシステム リソースが使用できることを確認します。<p> |
| -101 |診断プラグインを呼び出すときの引数が無効です。<p><p>この内部エラーは、ゲスト エージェント プラグインの起動ツールが VM 上で不正に手動で起動された場合にのみ発生します。 |
| -102 |プラグイン プロセスの初期化でエラーが発生しました。<p><p>解決方法: 新しいプロセスを起動するのに十分なシステム リソースが使用できることを確認します。 |
| -103 |プラグイン プロセスの初期化でエラーが発生しました。 具体的にはロガー オブジェクトを作成できません。<p><p>解決方法: 新しいプロセスを起動するのに十分なシステム リソースが使用できることを確認します。 |
| -104 |ゲスト エージェントが指定した rcf ファイルを読み込むことができません。<p><p>この内部エラーは、ゲスト エージェント プラグインの起動ツールが VM 上で不正に手動で起動された場合にのみ発生します。 |
| -105 |診断プラグインが診断構成ファイルを開くことができません。<p><p>この内部エラーは、診断プラグインが VM 上で不正に手動で起動された場合にのみ発生します。 |
| -106 |診断構成ファイルを読み取れませんでした。<p><p>解決方法: 構成ファイルがスキーマ検証を渡さないことが原因です。 そのため、スキーマに準拠する構成ファイルを使用してください。 「[診断拡張機能の構成を確認する方法](#how-to-check-diagnostics-extension-configuration)」を参照してください。 |
| -107 |監視エージェントへのリソース ディレクトリのパスが無効です。<p><p>この内部エラーは、監視エージェントが VM 上で不正に手動で起動された場合にのみ発生します。</p> |
| -108 |診断構成ファイルを監視エージェント構成ファイルに変換できません。<p><p>この内部エラーは、診断プラグインが無効な構成ファイルを使用して手動で起動された場合にのみ発生します。 |
| -110 |一般的な診断構成エラー。<p><p>この内部エラーは、診断プラグインが無効な構成ファイルを使用して手動で起動された場合にのみ発生します。 |
| -111 |監視エージェントを開始できません。<p><p>解決方法: 十分なシステム リソースを使用できることを確認します。 |
| -112 |一般的なエラー |

### <a name="local-log-extraction"></a>ローカル ログの抽出
監視エージェントは、ログとアーティファクトを `.tsf` ファイルとして収集します。 `.tsf` ファイルは読み取り可能ではありませんが、次の方法で `.csv` に変換できます。 

```
<Azure diagnostics extension package>\Monitor\x64\table2csv.exe <relevantLogFile>.tsf
```
対応する `.tsf` ファイルと同じパスに新しいファイル `<relevantLogFile>.csv` が作成されます。

**メモ**: 必要なのは、メインの tsf ファイル (PerformanceCountersTable.tsf など) に対してこのユーティリティを実行することのみです。 付随するファイル (PerformanceCountersTables_\*\*001.tsf、PerformanceCountersTables_\*\*002.tsf など) は自動的に処理されます。

### <a name="more-about-trace-logs-missing"></a>トレース ログが見つからない場合の詳細

**メモ:** ここに記載されている内容は、IaaS VM で実行されているアプリケーションで DiagnosticsMonitorTraceListener を構成した場合を除き、クラウド サービスに該当します。 

- DiagnosticMonitorTraceListener が web.config または app.config で構成されていることを確認します。既定では、DiagnosticsMonitorTraceListener はクラウド サービス プロジェクトで構成されますが、一部のお客様は DiagnosticsMonitorTraceListener をコメント アウトしているため、トレース ステートメントが診断によって収集されません。 
- ログが OnStart メソッドまたは Run メソッドから書き込まれていない場合は、DiagnosticMonitorTraceListener が app.config に含まれていることを確認します。既定では、DiagnosticsMonitorTraceListener は web.config に含まれていますが、w3wp.exe 内で実行されるコードにのみ適用されます。WaIISHost.exe で実行されるトレースをキャプチャするには、app.config に DiagnosticsMonitorTraceListener を追加する必要があります。
- Diagnostics.Debug.WriteXXX ではなく Diagnostics.Trace.TraceXXX を使用していることを確認します。  デバッグ ステートメントはリリース ビルドから削除されます。
- コンパイルしたコードに Diagnostics.Trace 行があることを確認します (Reflector、ildasm、または ILSpy を使用して確認してください)。  条件付きコンパイル シンボルである TRACE を使用しない限り、Diagnostics.Trace コマンドはコンパイルしたバイナリから削除されます。  msbuild を使用してプロジェクトをビルドする場合は、これが問題になります。

## <a name="known-issues-and-mitigations"></a>既知の問題と軽減策
既知の問題と軽減策の一覧を次に示します。

**1. .NET 4.5 の依存関係**

WAD には、.NET Framework 4.5 以降とのランタイムの依存関係があります。 このドキュメントの作成時点では、クラウド サービス用にプロビジョニングされたすべてのマシンおよび公式の Azure Virtual Machines のすべての基本イメージに .NET 4.5 以降がインストールされています。 ただし、.NET 4.5 以降がインストールされていないマシンで WAD を実行することも引き続き可能です。 この状況は、古いイメージやスナップショットからマシンを作成するときや、カスタム ディスクを利用するときに発生します。

通常、DiagnosticsPluginLauncher.exe の実行時には、終了コード **255** が表示されます。 ハンドルされない例外が原因でエラーが発生します。 
```
System.IO.FileLoadException: Could not load file or assembly 'System.Threading.Tasks, Version=1.5.11.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a' or one of its dependencies
```

**軽減策:** .NET 4.5 以降をマシンにインストールします。

**2.パフォーマンス カウンターのデータがストレージでは使用できるが、ポータルに表示されない**

既定では、仮想マシンのポータル エクスペリエンスには特定のパフォーマンス カウンターが表示されます。 これらのカウンターが表示されず、データが生成されていることがわかっている (データがストレージで使用可能なため) 場合は、 次のことを確認してください。
- ストレージ内のデータのカウンター名が英語であるかどうか。 カウンターの名前が英語でない場合、ポータルのメトリック グラフは名前を認識できません。
- パフォーマンス カウンター名にワイルドカード (\*) を使用している場合、ポータルでは構成済みのカウンターと収集されたカウンターを関連付けることができません。

**軽減策**: システム アカウント用に、マシンの言語を英語に変更します。 [コントロール パネル] -> [地域と言語] -> [管理] -> [設定のコピー] に移動して、[ようこそ画面システム アカウント] チェック ボックスをオフにします。これで、ユーザー定義の言語がシステム アカウントに適用されません。 また、ポータルの消費エクスペリエンスを重視する場合は、ワイルドカードを使用していないことを確認してください。

