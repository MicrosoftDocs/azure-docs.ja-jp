---
title: Microsoft Azure で PerfInsights を使用する方法 | Microsoft Docs
description: PerfInsights を使用して Windows VM のパフォーマンスに関する問題のトラブルシューティングを行う方法を説明します。
services: virtual-machines-windows'
documentationcenter: ''
author: anandhms
manager: cshepard
editor: na
tags: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: eace9c196ebd9f71b7a6f2bac7a59f581e6f313f
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70090457"
---
# <a name="how-to-use-perfinsights"></a>PerfInsights を使用する方法

[PerfInsights](https://aka.ms/perfinsightsdownload) は、診断データを収集および解析するセルフヘルプ診断ツールで、Azure での Windows 仮想マシンのパフォーマンス問題をトラブルシューティングする際に役立ちます。 PerfInsights は、仮想マシンでスタンドアロン ツールとして実行できます。または [Azure 仮想マシンのパフォーマンス診断](performance-diagnostics.md)を使用するか [Azure パフォーマンス診断 VM 拡張機能](performance-diagnostics-vm-extension.md)をインストールすることで、ポータルから直接実行できます。

仮想マシンでパフォーマンスの問題が発生している場合は、サポートに連絡する前にこのツールを実行してください。

## <a name="supported-troubleshooting-scenarios"></a>サポートされているトラブルシューティングのシナリオ

PerfInsights は、いくつかの種類の情報を収集して分析できます。 以降のセクションでは、一般的なシナリオについて説明します。

### <a name="quick-performance-analysis"></a>パフォーマンスのクイック分析

このシナリオでは、ディスク構成やその他の重要な情報を収集します。これには次のものが含まれます。

-   イベント ログ

-   すべての着信および発信接続のネットワークの状態

-   ネットワークとファイアウォールの構成設定

-   システムで現在実行されているすべてのアプリケーションのタスク一覧

-   Microsoft SQL Server データベースの構成設定 (SQL Server を実行しているサーバーとして VM が識別されている場合)

-   ストレージの信頼性カウンター

-   重要な Windows 修正プログラム

-   インストールされているフィルター ドライバー

これは、システムに影響しない情報のパッシブ コレクションです。 

>[!Note]
>このシナリオは、次の各シナリオに自動的に追加されます。

### <a name="benchmarking"></a>ベンチマーク

このシナリオでは、VM に接続されているすべてのドライブに対して [Diskspd](https://github.com/Microsoft/diskspd) ベンチマーク テスト (IOPS および MBPS) を実行します。 

> [!Note]
> このシナリオはシステムに影響する可能性があるため、稼働中の運用システムでは実行しないでください。 また、このシナリオは、問題を回避するために、必要に応じて、専用のメンテナンス期間に実行してください。 トレースまたはベンチマーク テストによってワークロードが増加すると、VM のパフォーマンスに悪影響を及ぼす場合があります。
>

### <a name="performance-analysis"></a>パフォーマンス分析

このシナリオでは、RuleEngineConfig.json ファイルで指定されているカウンターを使用して、[パフォーマンス カウンター](https://msdn.microsoft.com/library/windows/desktop/aa373083(v=vs.85).aspx) トレースを実行します。 VM が、SQL Server を実行しているサーバーとして識別された場合は、パフォーマンス カウンター トレースを実行します。 これは RuleEngineConfig.json ファイルに含まれているカウンターを使用して実行します。 このシナリオには、パフォーマンス診断データも含まれます。

### <a name="azure-files-analysis"></a>Azure Files 分析

このシナリオでは、特別なパフォーマンス カウンター キャプチャと、ネットワーク トレースを実行します。 このキャプチャには、すべてのサーバー メッセージ ブロック (SMB) クライアント共有カウンターが含まれています。 キャプチャに含まれる重要な SMB クライアント共有のパフォーマンス カウンターをいくつか次に示します。

| **Type**     | **SMB クライアント共有カウンター** |
|--------------|-------------------------------|
| IOPS         | データ要求数/秒             |
|              | 読み取り要求数/秒             |
|              | 書き込み要求数/秒            |
| Latency      | 平均秒数/データ要求         |
|              | 平均秒数/読み取り                 |
|              | 平均秒数/書き込み                |
| IO サイズ      | Avg.バイト/データ要求       |
|              | Avg.バイト/読み取り               |
|              | Avg.バイト/書き込み              |
| スループット   | データ バイト数/秒                |
|              | 読み取りバイト数/秒                |
|              | 書き込みバイト数/秒               |
| キューの長さ | Avg.読み取りキューの長さ        |
|              | Avg.書き込みキューの長さ       |
|              | Avg.データ キューの長さ        |

### <a name="advanced-performance-analysis"></a>高度なパフォーマンス分析

高度なパフォーマンス分析を実行する場合は、並行して実行するトレースを選択します。 必要に応じて、それらのすべて (パフォーマンス カウンター、Xperf、ネットワーク、および StorPort) を実行できます。  

> [!Note]
> このシナリオはシステムに影響する可能性があるため、稼働中の運用システムでは実行しないでください。 また、このシナリオは、問題を回避するために、必要に応じて、専用のメンテナンス期間に実行してください。 トレースまたはベンチマーク テストによってワークロードが増加すると、VM のパフォーマンスに悪影響を及ぼす場合があります。
>

## <a name="what-kind-of-information-is-collected-by-perfinsights"></a>PerfInsights で収集される情報の種類

Windows VM、ディスクまたは記憶域プールの構成、パフォーマンス カウンター、ログ、およびさまざまなトレースに関する情報が収集されます。 これは、使用しているパフォーマンス シナリオによって異なります。 次の表で詳細に説明します。

|収集されるデータ                              |  |  | パフォーマンス シナリオ |  |  | |
|----------------------------------|----------------------------|------------------------------------|--------------------------|--------------------------------|----------------------|----------------------|
|                               | パフォーマンスのクイック分析 | ベンチマーク | パフォーマンス分析 | Azure Files 分析 | 高度なパフォーマンス分析 |
| イベント ログの情報       | はい                        | はい                                | はい                      | はい                  | はい                  |
| システム情報                | はい                        | はい                                | はい                      | はい                  | はい                  |
| ボリューム マップ                        | はい                        | はい                                | はい                      | はい                  | はい                  |
| ディスク マップ                          | はい                        | はい                                | はい                      | はい                  | はい                  |
| 実行中のタスク                     | はい                        | はい                                | はい                      | はい                  | はい                  |
| ストレージの信頼性カウンター      | はい                        | はい                                | はい                      | はい                  | はい                  |
| ストレージ情報               | はい                        | はい                                | はい                      | はい                  | はい                  |
| Fsutil 出力                     | はい                        | はい                                | はい                      | はい                  | はい                  |
| フィルター ドライバー情報                | はい                        | はい                                | はい                      | はい                  | はい                  |
| Netstat 出力                    | はい                        | はい                                | はい                      | はい                  | はい                  |
| ネットワーク構成             | はい                        | はい                                | はい                      | はい                  | はい                  |
| ファイアウォールの構成            | はい                        | はい                                | はい                      | はい                  | はい                  |
| SQL Server 構成          | はい                        | はい                                | はい                      | はい                  | はい                  |
| パフォーマンス診断トレース *  | はい                        | はい                                | はい                      | はい                  | はい                  |
| パフォーマンス カウンター トレース **      |                            |                                    | はい                      |                      | はい                  |
| SMB カウンター トレース **              |                            |                                    |                          | はい                  |                      |
| SQL Server カウンター トレース **       |                            |                                    | はい                      |                      | はい                  |
| XPerf トレース                       |                            |                                    |                          |                      | はい                  |
| StorPort トレース                    |                            |                                    |                          |                      | はい                  |
| ネットワーク トレース                     |                            |                                    |                          | はい                  | はい                  |
| Diskspd ベンチマーク トレース ***       |                            | はい                                |                          |                      |                      |
|       |                            |                         |                                                   |                      |                      |

### <a name="performance-diagnostics-trace-"></a>パフォーマンス診断トレース (*)

バックグラウンドでルール ベースのエンジンを実行してデータを収集し、継続的なパフォーマンスの問題を診断します。 現在サポートされているルールは次のとおりです。

- HighCpuUsage ルール: CPU 使用率が高い期間を検出し、それらの期間中に CPU 使用率が最も高いコンシューマーを表示します。
- HighDiskUsage ルール: 物理ディスク上のディスク使用量が多い期間を検出し、それらの期間中にディスク使用量が最も多いコンシューマーを表示します。
- HighResolutionDiskMetric ルール: 物理ディスクごとに 50 ミリ秒あたりの IOPS、スループット、および I/O 待機時間のメトリックを表示します。 ディスク調整期間をすばやく特定するときに役立ちます。
- HighMemoryUsage ルール: メモリ使用量が多い期間を検出し、それらの期間中にメモリ使用量が最も多いコンシューマーを表示します。

> [!NOTE] 
> 現時点では、.NET Framework 4.5 以降のバージョンが搭載された Windows バージョンがサポートされています。

### <a name="performance-counter-trace-"></a>パフォーマンス カウンター トレース (\*\*)

次のパフォーマンス カウンターを収集します。

- \Process、\Processor、\Memory、\Thread、\PhysicalDisk、および \LogicalDisk
- \Cache\Dirty Pages、\Cache\Lazy Write Flushes/sec、\Server\Pool Nonpaged、Failures、および \Server\Pool Paged Failures
- \Network Interface、\IPv4\Datagrams、\IPv6\Datagrams、\TCPv4\Segments、\TCPv6\Segments、\Network Adapter、\WFPv4\Packets、\WFPv6\Packets、\UDPv4\Datagrams、\UDPv6\Datagrams、\TCPv4\Connection、\TCPv6\Connection、\Network QoS Policy\Packets、\Per Processor Network Interface Card Activity、および \Microsoft Winsock BSP の選択されたカウンター

#### <a name="for-sql-server-instances"></a>SQL Server インスタンス向け
- \SQL Server:Buffer Manager、\SQLServer:Resource Pool Stats、および \SQLServer:SQL Statistics\
- \SQLServer:Locks、\SQLServer:General、Statistics
- \SQLServer:Access Methods

#### <a name="for-azure-files"></a>Azure Files 向け
\SMB Client Shares

### <a name="diskspd-benchmark-trace-"></a>Diskspd ベンチマーク トレース (***)
Diskspd I/O ワークロード テスト (OS ディスク [書き込み] とプール ドライブ [読み取り/書き込み])

## <a name="run-the-perfinsights-tool-on-your-vm"></a>VM での PerfInsights ツールの実行

### <a name="what-do-i-have-to-know-before-i-run-the-tool"></a>ツールを実行する前の確認事項 

#### <a name="tool-requirements"></a>ツールの要件

-  このツールを、パフォーマンスに問題がある VM で実行する必要があります。 

-  次のクライアント オペレーティング システムがサポートされています。Windows Server 2008 R2、Windows Server 2012、Windows Server 2012 R2、Windows Server 2016、Windows 8.1、および Windows 10。

#### <a name="possible-problems-when-you-run-the-tool-on-production-vms"></a>運用 VM でこのツールを実行したときに発生する可能性のある問題

-  Xperf または Diskspd を使用するように構成された任意のベンチマーク シナリオまたは "高度なパフォーマンス分析" シナリオの場合、このツールは VM のパフォーマンスに悪影響を与える可能性があります。 運用環境ではこれらのシナリオを実行しないでください。

-  Diskspd を使用するように構成された任意のベンチマーク シナリオまたは "高度なパフォーマンス分析" シナリオの場合は、I/O ワークロードに干渉するバックグラウンド アクティビティが他に存在しないことを確認してください。

-  既定では、ツールは、一時ストレージ ドライブを使用してデータを収集します。 トレースが長時間有効になっている場合は、収集されたデータ量が関連している可能性があります。 これにより使用できる一時ディスク上の領域が減ることがあるため、このドライブに依存するすべてのアプリケーションに影響を与える場合があります。

### <a name="how-do-i-run-perfinsights"></a>PerfInsights の実行方法 

PerfInsights は、[Azure パフォーマンス診断 VM 拡張機能](performance-diagnostics-vm-extension.md)をインストールすることによって仮想マシンで実行できます。 また、スタンドアロン ツールとしても実行できます。 

**Azure Portal から PerfInsights をインストールして実行する**

このオプションの詳細については、「[Install Azure Performance Diagnostics VM Extension (Azure パフォーマンス診断 VM 拡張機能をインストールする)](performance-diagnostics-vm-extension.md#install-the-extension)」を参照してください。  

**スタンドアロン モードで PerfInsights を実行する**

PerfInsights ツールを実行するには、次の手順に従います。


1. [PerfInsights.zip](https://aka.ms/perfinsightsdownload) をダウンロードします。

2. PerfInsights.zip ファイルのブロックを解除します。 これを行うには、PerfInsights.zip ファイルを右クリックし、 **[プロパティ]** を選択します。 **[全般]** タブで **[ブロックの解除]** を選択し、 **[OK]** を選択します。 これにより、追加のセキュリティ プロンプトは表示されずに、確実にツールが実行されます。  

    ![[ブロックの解除] が強調表示された PerfInsights プロパティのスクリーンショット](media/how-to-use-perfInsights/pi-unlock-file.png)

3.  圧縮された PerfInsights.zip ファイルを一時ドライブ (既定では通常、D ドライブ) に展開します。 

4.  管理者として Windows コマンド プロンプトを開いて PerfInsights.exe を実行し、使用可能なコマンド ライン パラメーターを表示します。

    ```
    cd <the path of PerfInsights folder>
    PerfInsights
    ```
    ![PerfInsights コマンド ラインの出力のスクリーンショット](media/how-to-use-perfInsights/pi-commandline.png)
    
    PerfInsights シナリオを実行する基本構文は次のとおりです。
    
    ```
    PerfInsights /run <ScenarioName> [AdditionalOptions]
    ```

    パフォーマンス分析シナリオを 5 分間実行するには、以下の例を使用できます。
    
    ```
    PerfInsights /run vmslow /d 300 /AcceptDisclaimerAndShareDiagnostics
    ```

    次の例を使用して、Xperf とパフォーマンス カウンターをトレースする高度なシナリオを 5 分間実行できます。
    
    ```
    PerfInsights /run advanced xp /d 300 /AcceptDisclaimerAndShareDiagnostics
    ```

    次の例を使用して、パフォーマンス分析シナリオを 5 分間実行し、結果の zip ファイルをストレージ アカウントにアップロードできます。
    
    ```
    PerfInsights /run vmslow /d 300 /AcceptDisclaimerAndShareDiagnostics /sa <StorageAccountName> /sk <StorageAccountKey>
    ```

    **/list** コマンドを使用すると、利用可能なすべてのシナリオとオプションを検索できます。
    
    ```
    PerfInsights /list
    ```

    >[!Note]
    >シナリオを実行する前に、PerfInsights から、診断情報を共有し、使用許諾契約書に同意するよう求めるプロンプトが表示されます。 これらのプロンプトを省略するには、 **/AcceptDisclaimerAndShareDiagnostics** オプションを使用します。 
    >
    >有効な Microsoft のサポート チケットをお持ちで、サポート エンジニアからの依頼に従って PerfInsights を実行している場合は、 **/sr** オプションを使用してサポート チケット番号を入力してください。
    >
    >既定では、PerfInsights は自動的に最新バージョンに更新されます。 自動更新を省略するには、 **/SkipAutoUpdate** または **/sau** パラメーターを使用してください。  
    >
    >期間を切り替えるスイッチ **/d** が指定されていない場合は、PerfInsights から、VM の低速シナリオ、Azure Files シナリオ、および高度なシナリオの実行中に発生した問題を報告することを求められます。 

トレースまたは操作が完了すると、PerfInsights と同じフォルダーに新しいファイルが表示されます。 ファイルの名前は **PerformanceDiagnostics\_yyyy-MM-dd\_hh-mm-ss-fff.zip** です。 このファイルをサポート エージェントに送信して解析してもらったり、ZIP ファイル内のレポートを開いて、調査結果や推奨事項を参照できます。

## <a name="review-the-diagnostics-report"></a>診断レポートを確認する

**PerformanceDiagnostics\_yyyy-MM-dd\_hh-mm-ss-fff.zip** ファイル内に、PerfInsights の結果が詳細に示された HTML レポートがあります。 レポートを確認するには、**PerformanceDiagnostics\_yyyy-MM-dd\_hh-mm-ss-fff.zip** ファイルを展開し、**PerfInsights Report.html** ファイルを開きます。

**[検索結果]** タブを選択します。

![PerfInsights レポートのスクリーンショット](media/how-to-use-perfInsights/pi-finding-tab.png)
![PerfInsights レポートのスクリーンショット](media/how-to-use-perfInsights/pi-findings.png)

> [!NOTE] 
> [高] と分類された結果は、パフォーマンスの問題の原因となる可能性のある既知の問題です。 [中] と分類された結果は、最適ではないが、必ずしもパフォーマンス問題の原因とはならない構成です。 [低] と分類された結果は、単なる情報提供のためのステートメントです。

[高] の結果と [中] の結果に対する推奨事項とリンクを確認します。 それらがパフォーマンスに影響を与える可能性について、またパフォーマンスが最適化された構成のベスト プラクティスについても学習してください。

### <a name="storage-tab"></a>[ストレージ] タブ

**[Findings]\(評価結果\)** セクションには、ストレージに関連する分析結果と推奨事項が表示されます。

**[ディスク マップ]** および **[ボリューム マップ]** セクションは、論理ボリュームと物理ディスクがどのように関連しあっているかについて説明しています。

物理ディスクの観点 ([ディスク マップ]) では、このテーブルはディスク上で実行されているすべての論理ボリュームを示しています。 次の例では、**PhysicalDrive2** は複数のパーティション上に作成された 2 つの論理ボリューム (J および H) を実行しています。

![ディスク タブのスクリーンショット](media/how-to-use-perfInsights/pi-disk-tab.png)

ボリュームの観点 ([ボリューム マップ]) では、このテーブルは各論理ボリュームにあるすべての物理ディスクを示しています。 RAID/動的ディスクの場合は、1 つの論理ボリュームを複数の物理ディスク上で実行する可能性があることに注意してください。 次の例では、*C:\\mount* は、物理ディスク 2 および 3 上で *SpannedDisk* として構成されたマウント ポイントです。

![ボリューム タブのスクリーンショット](media/how-to-use-perfInsights/pi-volume-tab.png)

### <a name="sql-tab"></a>[SQL Server] タブ

ターゲット VM が SQL Server インスタンスをホストしている場合は、レポートに **SQL** という名前の追加のタブが表示されます。

![SQL タブのスクリーンショット](media/how-to-use-perfInsights/pi-sql-tab.png)

このセクションには、 **[検索結果]** タブと、VM 上でホストされている SQL Server インスタンスごとの追加のタブが含まれています。

**[検索結果]** タブには、見つかった SQL に関連するすべてのパフォーマンスの問題の一覧が推奨事項と共に含まれています。

次の例では、**PhysicalDrive0** (C ドライブを実行しています) が表示されます。 これは、**modeldev** と **modellog** の両方のファイルが C ドライブに配置され、それらが別の種類 (それぞれ、データ ファイルとトランザクション ログなど) であるためです。

![ログ情報のスクリーンショット](media/how-to-use-perfInsights/pi-log-info.png)

SQL Server の特定のインスタンスのタブには、選択されたインスタンスに関する基本的な情報を表示する一般的なセクションが含まれています。 これらのタブにはまた、高度な情報 (設定、構成、ユーザー オプションなど) のための追加のセクションも含まれています。

### <a name="diagnostic-tab"></a>[診断] タブ
**[診断]** タブには、PerfInsights を実行している期間中の、コンピューター上の最大の CPU、ディスク、およびメモリ コンシューマーに関する情報が含まれています。 また、システムに欠けている可能性のある重要な修正プログラム、タスクの一覧、および重要なシステム イベントに関する情報を見つけることもできます。 

## <a name="references-to-the-external-tools-used"></a>使用する外部ツールのリファレンス

### <a name="diskspd"></a>Diskspd

Diskspd は、Microsoft からのストレージ ロード ジェネレーターおよびパフォーマンス テスト ツールです。 詳細については、[Diskspd](https://github.com/Microsoft/diskspd) に関するページをご覧ください。

### <a name="xperf"></a>Xperf

Xperf は、Windows パフォーマンス ツールキットからのトレースをキャプチャするためのコマンドライン ツールです。 詳細については、「[Windows Performance Toolkit – Xperf (Windows パフォーマンス ツールキット - Xperf)](https://blogs.msdn.microsoft.com/ntdebugging/2008/04/03/windows-performance-toolkit-xperf/)」を参照してください。

## <a name="next-steps"></a>次の手順

さらに詳しく調べるために、診断ログとレポートを Microsoft サポートにアップロードできます。 サポートは、トラブルシューティング プロセスを支援するために、PerfInsights によって生成された出力を送信するよう要求する可能性があります。

次のスクリーンショットは、受信する可能性があるものと同様のメッセージを示しています。

![Microsoft サポートからのサンプル メッセージのスクリーンショット](media/how-to-use-perfInsights/pi-support-email.png)

メッセージ内の指示に従って、ファイル転送ワークスペースにアクセスします。 セキュリティを強化するために、最初の使用時にパスワードを変更する必要があります。

サインインすると、PerfInsights によって収集された **PerformanceDiagnostics\_yyyy-MM-dd\_hh-mm-ss-fff.zip** ファイルをアップロードするためのダイアログ ボックスが表示されます。

