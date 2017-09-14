---
title: "Microsoft Azure で PerfInsights を使用する方法 | Microsoft Docs"
description: "PerfInsights を使用して Windows VM のパフォーマンスに関する問題のトラブルシューティングを行う方法を説明します。"
services: virtual-machines-windows'
documentationcenter: 
author: genlin
manager: cshepard
editor: na
tags: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: genli
ms.translationtype: HT
ms.sourcegitcommit: a9cfd6052b58fe7a800f1b58113aec47a74095e3
ms.openlocfilehash: f22bd42302b96118dba0d4e5e387c6798a0b8777
ms.contentlocale: ja-jp
ms.lasthandoff: 08/12/2017

---
# <a name="how-to-use-perfinsights"></a>PerfInsights を使用する方法 

[PerfInsights](http://aka.ms/perfinsightsdownload) は自動化されたスクリプトで、有益な診断情報を収集し、I/O ストレス負荷を実行するほか、分析レポートを提供することで、Microsoft Azure で Windows VM のパフォーマンスに関する問題のトラブルシューティングを行います。 

このスクリプトは、VM パフォーマンスの問題に関するサポート チケットを Microsoft で開く前に実行することをお勧めします。

## <a name="supported-troubleshooting-scenarios"></a>サポートされているトラブルシューティングのシナリオ

PerfInsights では、固有シナリオにグループ化された数種類の情報を収集および分析できます。

### <a name="collect-disk-configuration"></a>ディスク構成の収集 

このシナリオでは、ディスク構成と他の重要な情報を収集します。これには次の項目が含まれます。

-   イベント ログ

-   すべての着信および発信接続のネットワークの状態

-   ネットワークとファイアウォールの構成設定

-   システムで現在実行されているすべてのアプリケーションのタスク一覧

-   仮想マシン (VM) に対して msinfo32 によって作成された情報ファイル

-   Microsoft SQL Server データベースの構成設定 (SQL Server を実行しているサーバーとして VM が識別されている場合)

-   ストレージの信頼性カウンター

-   重要な Windows 修正プログラム

-   インストールされているフィルター ドライバー

これは、システムに影響しない情報のパッシブ コレクションです。 

>[!Note]
>このシナリオは、次の各シナリオに自動的に追加されます。

### <a name="benchmarkstorage-performance-test"></a>ベンチマーク/ストレージ パフォーマンス テスト

このシナリオでは、VM に接続されているすべてのドライブに対して、[diskspd](https://github.com/Microsoft/diskspd) ベンチマーク テスト (IOPS および MBPS) を実行します。 

> [!Note]
> このシナリオはシステムに影響する可能性があるため、稼働中の運用システムでは実行しないでください。 また、このシナリオは、問題を回避するために、必要に応じて、専用のメンテナンス期間に実行してください。 トレースまたはベンチマーク テストによってワークロードが増加すると、VM のパフォーマンスに悪影響を及ぼす場合があります。
>

### <a name="general-vm-slow-analysis"></a>一般的な VM 低速分析 

このシナリオでは、Generalcounters.txt ファイルで指定されているカウンターを使用して、[パフォーマンス カウンター](https://msdn.microsoft.com/library/windows/desktop/aa373083(v=vs.85).aspx) トレースを実行します。 SQL Server を実行しているサーバーとして VM が特定されると、その VM は、Sqlcounters.txt ファイルで見つかったカウンターを使用して、パフォーマンス カウンターのトレースを実行します。 これには、パフォーマンス診断データも含まれます。

### <a name="vm-slow-analysis-and-benchmark"></a>VM 低速分析とベンチマーク

このシナリオでは、[パフォーマンス カウンター](https://msdn.microsoft.com/library/windows/desktop/aa373083(v=vs.85).aspx) トレースの後に、[diskspd](https://github.com/Microsoft/diskspd) ベンチマーク テストを実行します。 

> [!Note]
> このシナリオはシステムに影響する可能性があるため、稼働中の運用システムでは実行しないでください。 また、このシナリオは、問題を回避するために、必要に応じて、専用のメンテナンス期間に実行してください。 トレースまたはベンチマーク テストによってワークロードが増加すると、VM のパフォーマンスに悪影響を及ぼす場合があります。
>

### <a name="azure-files-analysis"></a>Azure Files 分析 

このシナリオでは、特別なパフォーマンス カウンター キャプチャと、ネットワーク トレースを実行します。 キャプチャには、すべての "SMB クライアント共有" カウンターが含まれます。 キャプチャに含まれる重要な SMB クライアント共有のパフォーマンス カウンターをいくつか次に示します。

| **型**     | **SMB クライアント共有カウンター** |
|--------------|-------------------------------|
| IOPS         | データ要求数/秒             |
|              | 読み取り要求数/秒             |
|              | 書き込み要求数/秒            |
| 待機時間      | Avg. 秒/データ要求         |
|              | Avg. 秒/読み取り                 |
|              | Avg. 秒/書き込み                |
| IO サイズ      | Avg. バイト/データ要求       |
|              | Avg. バイト/読み取り               |
|              | Avg. バイト/書き込み              |
| スループット   | データ バイト数/秒                |
|              | 読み取りバイト数/秒                |
|              | 書き込みバイト数/秒               |
| キューの長さ | Avg. 読み取りキューの長さ        |
|              | Avg. 書き込みキューの長さ       |
|              | Avg. データ キューの長さ        |

### <a name="custom-configuration"></a>カスタム構成 

カスタム構成を実行すると、選択されている各種トレースの数に応じて、すべてのトレース (パフォーマンスの診断、パフォーマンス カウンター、xperf、ネットワーク、storport) が並行して実行されます。 トレースの完了後、ツールによって diskspd ベンチマークが実行されます (選択されている場合)。 

> [!Note]
> このシナリオはシステムに影響する可能性があるため、稼働中の運用システムでは実行しないでください。 また、このシナリオは、問題を回避するために、必要に応じて、専用のメンテナンス期間に実行してください。 トレースまたはベンチマーク テストによってワークロードが増加すると、VM のパフォーマンスに悪影響を及ぼす場合があります。
>

## <a name="what-kind-of-information-is-collected-by-the-script"></a>スクリプトで収集される情報の種類

Windows VM、ディスクまたは記憶域プールの構成、パフォーマンス カウンター、ログ、およびさまざまなトレースに関する情報が、使用されているパフォーマンス シナリオに応じて収集されます。

|収集されるデータ                              |  |  | パフォーマンス シナリオ |  |  | |
|----------------------------------|----------------------------|------------------------------------|--------------------------|--------------------------------|----------------------|----------------------|
|                              | ディスク構成の収集 | ベンチマーク/ストレージ パフォーマンス テスト | 一般的な VM 低速分析 | VM 低速分析とベンチマーク | Azure Files 分析 | カスタム構成 |
| イベント ログの情報      | あり                        | あり                                | あり                      | あり                            | あり                  | あり                  |
| システム情報               | あり                        | あり                                | あり                      | あり                            | あり                  | あり                  |
| ボリューム マップ                       | あり                        | あり                                | あり                      | あり                            | あり                  | あり                  |
| ディスク マップ                         | あり                        | あり                                | あり                      | あり                            | あり                  | あり                  |
| 実行中のタスク                    | あり                        | あり                                | あり                      | あり                            | あり                  | あり                  |
| ストレージの信頼性カウンター     | あり                        | あり                                | あり                      | あり                            | あり                  | あり                  |
| ストレージ情報              | あり                        | あり                                | あり                      | あり                            | あり                  | あり                  |
| Fsutil 出力                    | あり                        | あり                                | あり                      | あり                            | あり                  | あり                  |
| フィルター ドライバー情報               | あり                        | あり                                | あり                      | あり                            | あり                  | あり                  |
| Netstat 出力                   | あり                        | あり                                | あり                      | あり                            | あり                  | あり                  |
| ネットワーク構成            | あり                        | あり                                | あり                      | あり                            | あり                  | あり                  |
| ファイアウォールの構成           | あり                        | あり                                | あり                      | あり                            | あり                  | あり                  |
| SQL Server 構成         | あり                        | あり                                | あり                      | あり                            | あり                  | あり                  |
| パフォーマンス診断トレース * |                            |                                    | あり                      |                                |                      | あり                  |
| パフォーマンス カウンター トレース **     |                            |                                    |                          |                                |                      | あり                  |
| SMB カウンター トレース **             |                            |                                    |                          |                                | あり                  |                      |
| SQL Server カウンター トレース **      |                            |                                    |                          |                                |                      | あり                  |
| XPerf トレース                      |                            |                                    |                          |                                |                      | あり                  |
| StorPort トレース                   |                            |                                    |                          |                                |                      | あり                  |
| ネットワーク トレース                    |                            |                                    |                          |                                | あり                  | あり                  |
| Diskspd ベンチマーク トレース ***      |                            | あり                                |                          | あり                            |                      |                      |
|       |                            |                         |                                                   |                      |                      |

### <a name="performance-diagnostics-trace-"></a>パフォーマンス診断トレース (*)

バックグラウンドでルール ベースのエンジンを実行してデータを収集し、継続的なパフォーマンスの問題を診断します。 現在サポートされているルールは次のとおりです。

- HighCpuUsage ルール: CPU 使用率が高い期間を検出し、その期間で CPU 使用率が最も高いコンシューマーを表示します。
- HighDiskUsage ルール: 物理ディスクでディスク使用量が多い期間を検出し、その期間でディスク使用量が最も多いコンシューマーを表示します。
- HighResolutionDiskMetric ルール: 物理ディスクごとに 50 ミリ秒あたりの IOPS、スループット、および IO 待機時間の指標を表示します。 ディスク調整期間をすばやく特定するときに役立ちます。
- HighMemoryUsage ルール: メモリ使用量が多い期間を検出し、その期間でメモリ使用量が最も多いコンシューマーを表示します。

> [!NOTE] 
> 現時点では、.NET Framework 3.5 以降のバージョンが搭載された Windows バージョンがサポートされています。

### <a name="performance-counter-trace-"></a>パフォーマンス カウンター トレース (**)

次のパフォーマンス カウンターを収集します。

- \Process、\Processor、\Memory、\Thread、\PhysicalDisk、\LogicalDisk
- \Cache\Dirty Pages、\Cache\Lazy Write Flushes/sec、\Server\Pool Nonpaged、Failures、\Server\Pool Paged Failures
- \Network Interface、\IPv4\Datagrams、\IPv6\Datagrams、\TCPv4\Segments、\TCPv6\Segments、\Network Adapter、\WFPv4\Packets、\WFPv6\Packets、\UDPv4\Datagrams、\UDPv6\Datagrams、\TCPv4\Connection、\TCPv6\Connection、\Network QoS Policy\Packets、\Per Processor Network Interface Card Activity、\Microsoft Winsock BSP の選択されたカウンター

#### <a name="for-sql-server-instances"></a>SQL Server インスタンス向け
- \SQL Server:Buffer Manager、\SQLServer:Resource Pool Stats、\SQLServer:SQL Statistics\
- \SQLServer:Locks、\SQLServer:General、Statistics
- \SQLServer:Access Methods

#### <a name="for-azure-files"></a>Azure Files 向け
\SMB Client Shares

### <a name="diskspd-benchmark-trace-"></a>Diskspd ベンチマーク トレース (***)
Diskspd IO ワークロードのテスト [OS ディスク (書き込み) とプール ドライブ (読み取り/書き込み)]

## <a name="run-the-perfinsights-on-your-vm"></a>VM での PerfInsights の実行

### <a name="what-do-i-have-to-know-before-i-run-the-script"></a>スクリプトを実行する前の確認事項 

**スクリプトの要件**

1.  このスクリプトは、パフォーマンスに問題がある VM で実行する必要があります。 

2.  サポートされている OS は Windows Server 2008 R2、2012、2012 R2、2016、Windows 8.1、および Windows 10 です。

**運用環境でスクリプトを実行するときに発生する可能性のある問題**

1.  スクリプトは、XPerf または DiskSpd を使用して構成された "ベンチマーク" または "カスタム" シナリオと共に使用すると、VM のパフォーマンスに悪影響を及ぼす可能性があります。 運用環境でスクリプトを実行するときは気を付けてください。

2.  DiskSpd を使用して構成された "ベンチマーク" または "カスタム" シナリオと一緒にスクリプトを使用するときは、テスト対象のディスク上の I/O ワークロードに干渉するバックグラウンド アクティビティが他にないことを確認します。

3.  既定では、スクリプトは、一時ストレージ ドライブを使用してデータを収集します。 トレースが長時間有効になっている場合は、収集されたデータ量が関連している可能性があります。 これにより使用できる一時ディスク領域が少なくなるため、このドライブに依存するすべてのアプリケーションに影響します。

### <a name="how-do-i-run-perfinsights"></a>PerfInsights の実行方法 

スクリプトを実行するには、次の手順に従います。

1. [PerfInsights.zip](http://aka.ms/perfinsightsdownload) をダウンロードします。

2. PerfInsights.zip ファイルのブロックを解除します。 これを行うには、PerfInsights.zip ファイルを右クリックし、**[プロパティ]** を選択します。 **[全般]** タブで **[ブロックの解除]** を選択し、**[OK]** を選択します。 これにより確実にスクリプトが実行されます。その際、追加のセキュリティ プロンプトは表示されません。  

    ![zip ファイルのロック解除](media/how-to-use-perfInsights/unlock-file.png)

3.  圧縮 PerfInsights.zip ファイルを一時ドライブ (既定では、通常、D ドライブ) に展開します。 圧縮されたファイルには、次のファイルとフォルダーが含まれます。

    ![zip フォルダー内のファイル](media/how-to-use-perfInsights/file-folder.png)

4.  Windows PowerShell を管理者として開き、PerfInsights.ps1 スクリプトを実行します。

    ```
    cd <the path of PerfInsights folder >
    Powershell.exe -ExecutionPolicy UnRestricted -NoProfile -File .\\PerfInsights.ps1
    ```

    実行ポリシーの変更の確認を求められた場合は、"y" を入力してください。

    [免責事項] ダイアログ ボックスに、診断情報を Microsoft サポートと共有するオプションが表示されます。 続行するには、ライセンス契約に同意する必要もあります。 選択内容を確認し､**[スクリプトの実行]** をクリックします。

    ![[免責事項] ボックス](media/how-to-use-perfInsights/disclaimer.png)

5.  スクリプトを実行するときに、サポート案件番号 (使用できる場合) を送信します (これは Microsoft の統計を目的としています)。 次に、 **[OK]**をクリックします
    
    ![サポート ID の入力](media/how-to-use-perfInsights/enter-support-number.png)

6.  一時ストレージ ドライブを選択します。 ドライブのドライブ文字はスクリプトにより自動検出されます。 この段階で問題が発生した場合は、ドライブを選択するように求められることがあります (既定のドライブは D)。 生成されたログは、この場所の log\_collection フォルダーに保存されます。 ドライブ文字を入力するか、そのまま使用して、**[OK]** をクリックします。

    ![ドライブの入力](media/how-to-use-perfInsights/enter-drive.png)

7.  表示された一覧から、トラブルシューティングのシナリオを選択します。

       ![サポート シナリオの選択](media/how-to-use-perfInsights/select-scenarios.png)

8.  UI を使わずに PerfInsights を実行することもできます。

    次のコマンドでは、UI プロンプトなしで "一般的な VM 低速分析" トラブルシューティング シナリオを実行するか、30 秒間データをキャプチャします。 手順 4. で示されたものと同じ免責事項と EULA に同意するように求められます。

        powershell.exe -ExecutionPolicy UnRestricted -NoProfile -Command ".\\PerfInsights.ps1 -NoGui -Scenario vmslow -TracingDuration 30"

    PerfInsights をサイレント モードで実行する場合は、**- AcceptDisclaimerAndShareDiagnostics** パラメーターを使用します。 たとえば、次のコマンドを使用します。

        powershell.exe -ExecutionPolicy UnRestricted -NoProfile -Command ".\\PerfInsights.ps1 -NoGui -Scenario vmslow -TracingDuration 30 -AcceptDisclaimerAndShareDiagnostics"

### <a name="how-do-i-troubleshoot-issues-while-running-the-script"></a>スクリプトの実行中に問題のトラブルシューティングを行う方法

スクリプトが異常終了した場合は、次に示すように、-Cleanup スイッチと共にスクリプトを実行して、不整合な状態をクリーンアップできます。

    powershell.exe -ExecutionPolicy UnRestricted -NoProfile -Command ".\\PerfInsights.ps1 -Cleanup"

一時ドライブの自動検出中に問題が発生した場合は、そのドライブを選択するように求められることがあります (既定のドライブは D)。

![ドライブの入力](media/how-to-use-perfInsights/enter-drive.png)

スクリプトによってユーティリティ ツールがアンインストールされ、一時フォルダーが削除されます。

### <a name="troubleshooting-other-script-issues"></a>その他のスクリプトに関する問題のトラブルシューティング 

スクリプトの実行時に問題が発生した場合は、Ctrl + C キーを押して、スクリプトの実行を中断します。 一時オブジェクトを削除するには、"異常終了後のクリーンアップ" に関するセクションを参照してください。

何回か実行した後もスクリプト エラーが発生し続ける場合は、起動時に "-Debug" パラメーター オプションを使用して、"デバッグ モード" でスクリプトを実行することをお勧めします。

障害が発生した後、PowerShell コンソールの出力全体をコピーして、問題のトラブルシューティングを支援する Microsoft サポート担当者に送信します。

### <a name="how-do-i-run-the-script-in-custom-configuration-mode"></a>カスタム構成モードでスクリプトを実行する方法

**カスタム**構成を選択すると、複数のトレースを並行して有効にできます (複数を選択するには Shift キーを使用)。

![シナリオの選択](media/how-to-use-perfInsights/select-scenario.png)

パフォーマンス診断、パフォーマンス カウンターのトレース、XPerf トレース、ネットワーク トレース、または Storport トレースのシナリオを選択したら、ダイアログ ボックスの指示に従って操作し、トレースを開始した後、低速パフォーマンスの問題を再現します。

次のダイアログ ボックスで、トレースを開始できます。

![トレースの開始](media/how-to-use-perfInsights/start-trace-message.png)

トレースを停止するには、2 番目のダイアログ ボックスで、コマンドを確認する必要があります。

![トレースの停止](media/how-to-use-perfInsights/stop-trace-message.png)
![トレースの停止](media/how-to-use-perfInsights/ok-trace-message.png)

トレースまたは操作が完了すると、**CollectedData\_yyyy-MM-dd\_hh\_mm\_ss.zip** という名前の新しいファイルが D:\\log\_collection (一時ドライブ) に生成されます。 このファイルは、分析のためにサポート担当者に送信できます。

## <a name="review-the-diagnostics-report-created-by-perfinsights"></a>PerfInsights で作成された診断レポートのレビュー

PerfInsights によって作成された **CollectedData\_yyyy-MM-dd\_hh\_mm\_ss.zip ファイル**に、PerfInsights の結果の詳細が示されている HTML レポートがあります。 レポートを確認するには、**CollectedData\_yyyy-MM-dd\_hh\_mm\_ss.zip** ファイルを展開し、**PerfInsights Report.html** ファイルを開きます。

**[検索結果]** タブを選択します。

![結果タブ](media/how-to-use-perfInsights/findingtab.png)

**メモ**

-   赤のメッセージは、パフォーマンスの問題を引き起こしている可能性がある既知の構成の問題です。

-   黄色のメッセージは、最適ではない構成を表す警告ですが、必ずしもこの構成によりパフォーマンスの問題が発生しているわけではありません。

-   青色のメッセージは、情報提供のみを目的としています。

赤のエラー メッセージすべての HTTP リンクを確認して、結果の詳細を入手し、こうしたエラーがパフォーマンスや、パフォーマンスに最適化された構成のためのベスト プラクティスにどうように影響しているかを調べます。

### <a name="disk-configuration-tab"></a>[ディスク構成] タブ

**[概要]** セクションには、Diskpart や記憶域スペースからの情報など、ストレージの構成がさまざまなビューで表示されます

**[DiskMap]** セクションと **[VolumeMap]** セクションでは、論理ボリュームと物理ディスクがどのように関連し合っているかを両方の分析観点から説明します。

PhysicalDisk 分析観点 (DiskMap) のテーブルには、ディスクで実行されているすべての論理ボリュームが示されます。 次の例では、PhysicalDrive2 で、複数のパーティション (J と H) で作成された 2 つの論理ボリュームが実行されています。

![データ タブ](media/how-to-use-perfInsights/disktab.png)

ボリューム分析観点 (*VolumeMap*) のテーブルには、論理ボリュームごとにすべての物理ディスクが表示されます。 RAID/動的ディスクについては、複数の物理ディスクで 1 つの論理ボリュームを実行している可能性があることに注意してください。 次の例の *C:\\mount* は、PhysicalDisk \#2 と \#3 で *SpannedDisk* として構成されたマウントポイントです。

![ボリューム タブ](media/how-to-use-perfInsights/volumetab.png)

### <a name="sql-server-tab"></a>[SQL Server] タブ

ターゲット VM で SQL Server インスタンスがホストされている場合は、レポートに **SQL Server** という名前の追加のタブが表示されます。

![[SQL Server] タブ](media/how-to-use-perfInsights/sqltab.png)

このセクションには、"概要" サブ タブと、VM でホストされている SQL Server インスタンスごとに追加のサブ タブがあります。

"概要" セクション内の便利なテーブルには、データ ファイルとトランザクション ログ ファイルが混在して含まれる、実行中のすべての物理ディスク (システム ディスクとデータ ディスク) が示されています。

次の例では、*PhysicalDrive0* (C ドライブを実行) が表示されています。ファイルの種類が異なる *modeldev* ファイルと*modellog* ファイル (それぞれデータ ファイルとトランザクション ログ) の両方が C ドライブに配置されているためです。

![ログ情報](media/how-to-use-perfInsights/loginfo.png)

SQL Server インスタンス固有のタブには、選択したインスタンスに関する基本情報が示される全般セクションと、設定、構成、ユーザー オプションなどの詳細情報が示される追加セクションがあります。

## <a name="references-to-the-external-tools-used"></a>使用する外部ツールのリファレンス

### <a name="diskspd"></a>Diskspd

DISKSPD は、Windows と Windows Server、およびクラウド サーバー インフラストラクチャ エンジニアリング チームの、ストレージ ロード ジェネレーターおよびパフォーマンス テスト ツールです。 詳細については、[Diskspd](https://github.com/Microsoft/diskspd) に関するページをご覧ください。

### <a name="xperf"></a>XPerf

Xperf は、Windows パフォーマンス ツールキットからトレースをキャプチャするコマンドライン ツールです。

詳細については、「[Windows Performance Toolkit – Xperf (Windows パフォーマンス ツールキット - Xperf)](https://blogs.msdn.microsoft.com/ntdebugging/2008/04/03/windows-performance-toolkit-xperf/)」を参照してください。

## <a name="next-steps"></a>次のステップ

### <a name="upload-diagnostics-logs-and-reports-to-microsoft-support-for-further-review"></a>さらに詳しく調べるために、診断ログとレポートを Microsoft サポートにアップロードする

Microsoft サポートに連絡すると、トラブルシューティング プロセスのために、担当者から PerfInsights で生成された出力を送信するように求められることがあります。

サポート担当者が DTM ワークスペースを作成すると、ユーザーには、その [DTM ポータル (https://filetransfer.support.microsoft.com/EFTClient/Account/Login.htm) へのリンクと、一意のユーザー ID およびパスワードが記載されている電子メール メッセージが届きます。

このメッセージは、**CTS 自動診断サービス**(ctsadiag@microsoft.com) から送信されます。

![メッセージの例](media/how-to-use-perfInsights/supportemail.png)

セキュリティを強化するために、最初にパスワードを変更する必要があります。

DTM にログインすると、PerfInsights によって収集された **CollectedData\_yyyy-MM-dd\_hh\_mm\_ss.zip** ファイルをアップロードするダイアログ ボックスが表示されます。

