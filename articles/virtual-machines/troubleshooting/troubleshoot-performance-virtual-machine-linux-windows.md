---
title: Linux または Windows での Azure 仮想マシンのパフォーマンスのトラブルシューティング
description: この記事では、仮想マシン (VM) の監視と観察によるパフォーマンスの一般的なトラブルシューティングについて説明し、発生する可能性のある問題を修復します。
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/18/2019
ms.author: v-miegge
ms.openlocfilehash: 53fd2332224d903c5a4b33563470cf3569f82b13
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2020
ms.locfileid: "86526658"
---
# <a name="troubleshoot-azure-virtual-machine-performance-on-linux-or-windows"></a>Linux または Windows での Azure 仮想マシンのパフォーマンスのトラブルシューティング

この記事では、仮想マシン (VM) の監視と観察によるパフォーマンスの一般的なトラブルシューティングについて説明し、発生する可能性のある問題を修復します。 監視だけでなく、Perfinsights を使用することもできます。これにより、ベスト プラクティスのレコメンデーションと IO/CPU/メモリに関する主要なボトルネックを含むレポートが提供されます。 Perfinsights は、[Windows](./how-to-use-perfinsights.md) と [Linux](./how-to-use-perfinsights-linux.md) の両方の Azure VM に用意されています。

この記事では、監視を使用してパフォーマンスのボトルネックを診断する手順について説明します。

## <a name="enabling-monitoring"></a>監視を有効にする

### <a name="azure-iaas-virtual-machine-monitoring"></a>Azure IaaS 仮想マシンの監視

ゲスト VM を監視するには、Azure VM 監視を使用します。これにより、特定のリソースの状態の概要が通知されます。 VM 診断が有効になっているかどうかを確認するには、[Azure リソース ログの概要](../../azure-monitor/learn/tutorial-resource-logs.md)に関する記事を参照してください。 次のように表示される場合は、診断が有効になっていない可能性が高いです。

![監視が有効になっていない](media/troubleshoot-performance-virtual-machine-linux-windows/1-virtual-machines-monitoring-not-enabled.png)
 
### <a name="enable-vm-diagnostics-through-microsoft-azure-portal"></a>Microsoft Azure portal を使用して VM 診断を有効にする

VM 診断を有効にするには

1. VM に移動します
2. **[診断の設定]** をクリックします
3. ストレージ アカウントを選択し、 **[ゲスト レベルの監視を有効にする]** をクリックします。

   ![[設定]、[診断] の順にクリックする](media/troubleshoot-performance-virtual-machine-linux-windows/2-virtual-machines-diagnostics.png)

診断の設定に使用されたストレージ アカウントは、 **[診断の設定]** の下にある **[エージェント]** タブから確認できます。

![ストレージ アカウントを確認する](media/troubleshoot-performance-virtual-machine-linux-windows/3-check-storage-account.png)

### <a name="enable-storage-account-diagnostics-through-azure-portal"></a>Azure portal を使用したストレージ アカウント診断を有効にする

ストレージは、Azure の仮想マシンの IO パフォーマンスを分析する場合に非常に重要なレベルです。 ストレージ関連のメトリックについては、追加の手順として診断を有効にする必要があります。 ストレージ関連のカウンターのみを分析する場合も、これを有効にすることができます。

1. VM を選択して、VM で使用されているストレージ アカウント (1 つまたは複数) を特定します。 **[設定]** をクリックしてから、 **[ディスク]** をクリックします。

   ![[設定]、[ディスク] の順にクリックする](media/troubleshoot-performance-virtual-machine-linux-windows/4-storage-disks-disks-selection.png)

2. ポータルで、VM のストレージ アカウント (1 つまたは複数) にアクセスし、次の手順を行います。

   1. 上記の手順で見つけたストレージ アカウントの概要をクリックします。
   2. 既定のメトリックが表示されます。 

    ![Default metrics](media/troubleshoot-performance-virtual-machine-linux-windows/5-default-metrics.png)

3. 任意のメトリックをクリックすると、メトリックを構成して追加するためのオプションをより多く含む、別のブレードが表示されます。

   ![メトリックの追加](media/troubleshoot-performance-virtual-machine-linux-windows/6-add-metrics.png)

これらのオプションを構成するには

1.  **[メトリック]** を選びます。
2.  **[リソース]** (ストレージ アカウント) を選択します。
3.  **[名前空間]** を選択します。
4.  **[メトリック]** を選択します。
5.  **[集計]** の種類を選択します。
6.  このビューをダッシュボードにピン留めすることができます。

## <a name="observing-bottlenecks"></a>ボトルネックを観察する

必要なメトリックの初期セットアップ プロセスを実行すると、VM と関連するストレージ アカウントの診断を有効にした後、分析フェーズに移行できるようになります。

### <a name="accessing-the-monitoring"></a>監視にアクセスする

調査する Azure VM を選択し、 **[監視]** を選択します。

![監視を選択する](media/troubleshoot-performance-virtual-machine-linux-windows/7-select-monitoring.png)
 
### <a name="timelines-of-observation"></a>観察のタイムライン

リソースのボトルネックがあるかどうかを特定するには、データを確認します。 コンピューターが正常に実行されているのにパフォーマンスが最近低下したことが報告された場合は、報告された問題の発生前、発生中、発生後のパフォーマンス メトリック データが含まれるデータの時間範囲を確認します。

### <a name="check-for-cpu-bottleneck"></a>CPU のボトルネックを確認する

![CPU のボトルネックを確認する](media/troubleshoot-performance-virtual-machine-linux-windows/8-cpu-bottleneck-time-range.png)

1. グラフを編集します。
2. 時間の範囲を設定します。
3. カウンターに追加する必要があります: CPU の割合 - ゲスト OS
4. 保存します。

### <a name="cpu-observe-trends"></a>CPU 観察の傾向

パフォーマンスの問題を調査するときは、傾向を把握して、その影響を受けるかどうかを判断します。 次のセクションでは、ポータルの監視グラフを使用して傾向を表示します。 同じ期間に異なるリソース動作を相互参照する場合にも役立つことがあります。 グラフをカスタマイズするには、[Azure Monitor データ プラットフォーム](../../azure-monitor/platform/data-platform.md)をクリックします。

急上昇 – 急上昇は、スケジュールされたタスク/既知のイベントに関連する可能性があります。 タスクを特定できる場合は、必要なパフォーマンス レベルでタスクが実行されているかどうかを判断します。 パフォーマンスが許容できる場合は、リソースを増やす必要はない可能性があります。

急上昇後に一定 – 多くの場合、新しいワークロードを示します。 ワークロードが認識されない場合は、VM の監視を有効にして、動作の原因となっているプロセス (1 つまたは複数) を確認します。 プロセスが認識されたら、使用量の増加が、非効率的なコードによるものか、通常の使用なのかを判断します。 通常の使用の場合は、必要なパフォーマンス レベルでプロセスが動作するかどうかを判定します。

一定 – VM が常にこのレベルで実行されているかどうか、または診断が有効になってからそのレベルでのみ実行されているかどうかを判断します。 その場合は、問題の原因となっているプロセス (1 つまたは複数) を特定し、そのリソースをさらに追加することを検討します。

増加し続ける – 消費量が増加し続けるのは、多くの場合、非効率的なコードまたはプロセスがユーザーのワークロードを増やしています。

### <a name="high-cpu-utilization-remediation"></a>高い CPU 使用率の修復

アプリケーションまたはプロセスが適切なパフォーマンス レベルで実行されておらず、常に 95% 以上の CPU 使用率が見られる場合は、次のいずれかのタスクを実行できます。

* 直ちに軽減する - VM のサイズをコア数が多いサイズに増やします
* 問題を把握する – アプリケーション/プロセスを特定し、それに応じてトラブルシューティングを行います。

VM を増やしても CPU の実行が 95% ある場合は、この設定でパフォーマンスが向上するか、アプリケーションのスループットが許容できるレベルになるかを判断します。 それ以外の場合は、個々のアプリケーション\プロセスのトラブルシューティングを行います。

[Windows](./how-to-use-perfinsights.md) または [Linux](./how-to-use-perfinsights-linux.md) に Perfinsights を使用して、どのプロセスが CPU 使用率に影響を与えているかを分析できます。 

## <a name="check-for-memory-bottleneck"></a>メモリのボトルネックを確認する

メトリックを表示するには:

1. セクションを追加します。
2. タイルを追加します。
3. ギャラリーを開きます。
4. メモリ使用量を選択してドラッグします。 タイルがドッキングされたら、右クリックして **[6x4]** を選択します。

### <a name="memory-observe-trends"></a>メモリ観察の傾向

メモリ使用量は、VM で使用されているメモリの量を示します。 傾向と、それが問題が発生した時刻にマップされているかどうかを把握します。 常に 100 MB を超える使用可能なメモリが必要です。

急上昇後に一定/一定の安定した消費量 - 高いメモリ使用率がパフォーマンスの低下の原因ではない場合があります。リレーショナル データベース エンジンなどの一部のアプリケーションで大量のメモリを割り当てても、この使用率に大きな影響がないことがあります。 ただし、メモリを大量に使用するアプリケーションが複数ある場合は、メモリの競合によりパフォーマンスが低下し、ディスクへのトリミングおよびページング/スワップが発生する可能性があります。 このパフォーマンスの低下が、アプリケーションのパフォーマンスに影響を与える顕著な原因になる場合が多くあります。

使用量が増加し続ける – アプリケーションの "ウォーム アップ" の可能性があります。この使用料は、データベース エンジンの起動でよく見られます。 しかしながら、アプリケーションのメモリ リークの兆候である可能性もあります。 アプリケーションを特定し、動作が想定されたものであるかどうかを判断します。

ページまたはスワップ ファイルの使用 – Windows のページング ファイルを使用している (D:\) にある)、または Linux のスワップ ファイル (`/dev/sdb` にある) が頻繁に使用されているかどうかを確認します。 これらのファイル以外には、これらのボリュームに何もない場合は、それらのディスクで読み取り/書き込みが多いかどうかを確認してください。 この問題は、メモリ不足の状態を示しています。

### <a name="high-memory-utilization-remediation"></a>高いメモリ使用率の修復

高メモリ使用率を解決するには、次のいずれかのタスクを実行します。

* 直ちに軽減する場合や、ページまたはスワップ ファイルの使用量が多い場合は、VM のサイズを、メモリの多いものに増やしてから監視します。
* 問題を把握する – アプリケーション/プロセスを見つけ、大量のメモリを消費するアプリケーションを特定するためのトラブルシューティングを行います。
* アプリケーションがわかっている場合は、メモリ割り当てが制限されているかどうかを確認します。

より大きな VM にアップグレードした後も、100% まで一定して増加し続けていることがわかった場合は、アプリケーション/プロセスを特定し、トラブルシューティングを行います。

[Windows](./how-to-use-perfinsights.md) または [Linux](./how-to-use-perfinsights-linux.md) に Perfinsights を使用して、どのプロセスがメモリ使用率に影響を与えているかを分析できます。 

## <a name="check-for-disk-bottleneck"></a>ディスクのボトルネックを確認する

VM のストレージ サブシステムを確認するには、VM 診断のカウンターとストレージ アカウント診断も使用して、Azure VM レベルで診断を確認します。

VM 内の固有のトラブルシューティングでは、[Windows](./how-to-use-perfinsights.md) または [Linux](./how-to-use-perfinsights-linux.md) に Perfinsights を使用できます。これにより、どのプロセスが IO に影響を与えているかを分析できます。 

ゾーン冗長および Premium Storage アカウントのカウンターはないことに注意してください。 これらのカウンターに関連する問題の場合は、サポート ケースを生成します。

### <a name="viewing-storage-account-diagnostics-in-monitoring"></a>監視でストレージ アカウント診断を表示する

以下の項目を操作するには、ポータルで VM のストレージ アカウントにアクセスします。

![監視でストレージ アカウント診断を表示する](media/troubleshoot-performance-virtual-machine-linux-windows/9-virtual-machine-storage-account.png)

1. 監視グラフを編集します。
2. 時間の範囲を設定します。
3. 後述の手順で説明されているカウンターを追加します。
4. 変更を保存します。

### <a name="disk-observe-trends-standard-storage-only"></a>ディスク観察の傾向 (標準ストレージのみ)

ストレージに関する問題を特定するには、ストレージ アカウント診断と VM 診断のパフォーマンス メトリックを確認します。

以下のチェックごとに、問題の時間範囲内で問題が発生した場合の主な傾向を確認します。

#### <a name="check-azure-storage-availability--add-the-storage-account-metric-availability"></a>Azure ストレージの可用性を確認する – ストレージ アカウントのメトリックを追加する: 可用性

可用性が低下している場合は、プラットフォームに問題がある可能性があるため、[Azure の状態](https://azure.microsoft.com/status/)を確認します。 問題が何も表示されない場合は、新しいサポート リクエストを生成します。

#### <a name="check-for-azure-storage-timeout---add-the-storage-account-metrics"></a>Azure ストレージのタイムアウトを確認する - ストレージ アカウントのメトリックを追加する:

* ClientTimeOutError
* ServerTimeOutError
* AverageE2ELatency
* AverageServerLatency
* TotalRequests

\* TimeOutError メトリックの値は、IO 操作の時間が長すぎてタイムアウトになったことを示します。次の手順を行うと、潜在的な原因を特定するのに役立ちます。

TimeOutErrors で同時に AverageServerLatency が増加する場合は、プラットフォームの問題である可能性があります。 この場合は、新しいサポート リクエストを生成します。

AverageE2ELatency は、クライアントの待機時間を表します。 アプリケーションで IOPS がどのように実行されているかを確認します。 増加、または TotalRequests メトリックが常に高いかを確認します。 このメトリックは IOPS を表します。 ストレージ アカウントまたは単一の VHD の上限に達し始めている場合は、待機時間が調整に関連している可能性があります。

#### <a name="check-for-azure-storage-throttling---add-the-storage-account-metrics-throttlingerror"></a>Azure ストレージの調整を確認する - ストレージ アカウントのメトリックを追加する: ThrottlingError

調整の値は、ストレージ アカウント レベルで調整されていることを示します。これは、アカウントの IOPS 制限に達したことを意味します。 IOP のしきい値に達しているかどうかを確認するには、メトリック **TotalRequests** を確認します。

各 VHD には 500 IOPS または 60 MBit の制限がありますが、ストレージ アカウントあたりの 20000 IOPS の累積制限に縛られます。

このメトリックでは、どの BLOB が調整の原因であり、どれがその影響を受けているかは判断できません。 ただし、ストレージ アカウントの IOPS またはイングレス/エグレスの制限のいずれかに達しています。

IOPS の上限に達しているかどうかを確認するには、ストレージ アカウントの診断にアクセスし、TotalRequests を調べて、TotalRequests が 20000 に達しているかどうかを確認します。 パターンの変更、制限が初めて表示されているかどうか、またはこの制限が特定の時間に発生するかどうかを特定します。

Standard ストレージの新しいディスク オファリングでは、IOPS とスループットの制限が異なる場合がありますが、Standard ストレージ アカウントの累積制限は 20000 IOPS です (Premium ストレージの制限は、アカウント レベルまたはディスク レベルで異なります)。 さまざまな Standard ストレージのディスク オファリングとディスクごとの制限については、以下を参照してください。

* [Windows 上の VM ディスクのスケーラビリティおよびパフォーマンスの目標](../windows/disk-scalability-targets.md)

#### <a name="references"></a>References

* [Premium ページ BLOB ストレージ アカウントのスケーラビリティおよびパフォーマンス ターゲット](../../storage/blobs/scalability-targets-premium-page-blobs.md)

ストレージ アカウントの帯域幅は、ストレージ アカウントのメトリックで測定されます: TotalIngress および TotalEgress。 帯域幅のしきい値は、冗長性とリージョンの種類によって異なります。

* [標準ストレージ アカウントのスケーラビリティとパフォーマンスのターゲット](../../storage/common/scalability-targets-standard-account.md)

ストレージ アカウントの冗長性の種類とリージョンのイングレスおよびエグレスの制限に対して TotalIngress と TotalEgress を確認します。

VM に接続されている VHD のスループットの上限を確認します。 VM メトリック ディスクの読み取りと書き込みを追加します。

Standard ストレージの新しいディスク オファリングには、さまざまな IOPS とスループットの制限があります (IOPS は VHD ごとには公開されません)。 データを見て、ディスクの読み取りと書き込みを使用して VM レベルでの VHD の合計スループット (MB) の上限に達しているかどうかを確認し、VM ストレージ構成を最適化して、1 つの VHD 制限を超えてスケーリングします。 さまざまな Standard ストレージのディスク オファリングとディスクごとの制限については、以下を参照してください。

* [Windows 上の VM ディスクのスケーラビリティおよびパフォーマンスの目標](../windows/disk-scalability-targets.md)

### <a name="high-disk-utilizationlatency-remediation"></a>ディスクの高使用率と待機時間の修復

クライアントの待機時間を短縮し、VM IO を最適化して、VHD の制限を超えてスケーリングします

* [Azure での Windows の IO の最適化](../../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md?toc=/azure/virtual-machines/windows/toc.json)

* [Azure での Linux の IO の最適化](/archive/blogs/igorpag/azure-storage-secrets-and-linux-io-optimizations)

#### <a name="reduce-throttling"></a>調整を減らす

ストレージ アカウントの上限に達した場合は、ストレージ アカウント間で VHD のバランスを再調整します。 [Azure Storage のスケーラビリティおよびパフォーマンスのターゲット](../../storage/common/scalability-targets-standard-account.md)に関する記事をご覧ください。

### <a name="increase-throughput-and-reduce-latency"></a>スループットの向上と待機時間の短縮

待機時間の影響を受けやすいアプリケーションがあり、高いスループットが必要な場合は、DS および GS シリーズの VM を使用して、VHD を Azure Premium Storage に移行します。

次の記事では、特定のシナリオについて説明します。

* [Azure Premium Storage への移行](../windows/migrate-to-managed-disks.md)

* [Azure Premium Storage と SQL Server の使用](/previous-versions/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-server-premium-storage)

## <a name="next-steps"></a>次のステップ

この記事についてさらにヘルプが必要な場合は、[MSDN の Azure フォーラムと Stack Overflow フォーラム](https://azure.microsoft.com/support/forums/)で Azure エキスパートにお問い合わせください。

または、Azure サポート インシデントを送信してください。 その場合は、 [Azure サポートのサイト](https://azure.microsoft.com/support/options/) に移動して、 **[サポートの要求]** をクリックします。
