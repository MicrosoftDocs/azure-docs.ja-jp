---
title: Azure NetApp Files を使用する際のボリュームのパフォーマンスとメトリックのベンチマーク テスト | Microsoft Docs
description: Azure NetApp Files を使用する際のボリュームのパフォーマンスとメトリックのベンチマーク テストに関する推奨事項を示します。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: b-juche
ms.openlocfilehash: 12ae9e313655924f11799152b5e58b77776c135c
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2019
ms.locfileid: "67479076"
---
# <a name="benchmark-testing-for-volume-performance-and-metrics-using-azure-netapp-files"></a>Azure NetApp Files を使用する際のボリュームのパフォーマンスとメトリックのベンチマーク テスト

この記事では、Azure NetApp Files を使用する際のボリュームのパフォーマンスとメトリックのベンチマーク テストに関する推奨事項を示します。

## <a name="overview"></a>概要

Azure NetApp Files ボリュームのパフォーマンス特性を理解するために、オープンソース ツールの [FIO](https://github.com/axboe/fio) を使用して一連のベンチマークを実行し、さまざまなワークロードをシミュレートすることができます。 FIO は、Linux と Windows のどちらをベースにしたオペレーティング システムにもインストールできます。  ボリュームの IOPS とスループット両方の簡易スナップショットを取得するための優れたツールです。

### <a name="vm-instance-sizing"></a>VM インスタンスのサイズ設定

最良の結果を得るには、テストを実行するために適切なサイズの仮想マシン (VM) インスタンスを使用していることを確認してください。 次の例では Standard_D32s_v3 インスタンスを使用します。 VM インスタンスのサイズの詳細について、Windows ベースの VM の場合は「[Azure の Windows 仮想マシンのサイズ](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json)」、Linux ベースの VM の場合は「[Azure の Linux 仮想マシンのサイズ](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照してください。

### <a name="azure-netapp-files-volume-sizing"></a>Azure NetApp Files ボリュームのサイズ設定

必ず、予想されるパフォーマンス レベルに合った正しいサービス レベルおよびボリューム クォータ サイズを選択してください。 詳細については、「[Azure NetApp Files のサービス レベル](azure-netapp-files-service-levels.md)」を参照してください。

### <a name="virtual-network-vnet-recommendations"></a>仮想ネットワーク (VNet) の推奨事項

ベンチマーク テストは Azure NetApp Files と同じ VNet で実行する必要があります。 次の例は推奨事項を示しています。

![VNet の推奨事項](../media/azure-netapp-files/azure-netapp-files-benchmark-testing-vnet.png)

## <a name="installation-of-fio"></a>FIO のインストール

FIO は、Linux と Windows の両方でバイナリ形式で入手できます。 [FIO](https://github.com/axboe/fio) の「Binary Packages」セクションに従って、選択したプラットフォームにインストールします。

## <a name="fio-examples-for-iops"></a>IOPS 用の FIO の例 

このセクションの FIO の例では、次の設定を使用します。
* VM インスタンス サイズ:D32s_v3
* 容量プールのサービス レベルとサイズ:Premium / 50 TiB
* ボリュームのクォータ サイズ:48 TiB

次の例は FIO のランダム読み取りおよび書き込みを示します。

### <a name="fio-8k-block-size-100-random-reads"></a>FIO:8k ブロック サイズ 100% ランダム読み取り

`fio --name=8krandomreads --rw=randread --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128 --size=4G --runtime=600 --group_reporting`

### <a name="output-68k-read-iops-displayed"></a>出力:68k 読み取り IOPS を表示

`Starting 4 processes`  
`Jobs: 4 (f=4): [r(4)][84.4%][r=537MiB/s,w=0KiB/s][r=68.8k,w=0 IOPS][eta 00m:05s]`

### <a name="fio-8k-block-size-100-random-writes"></a>FIO:8k ブロック サイズ 100% ランダム書き込み

`fio --name=8krandomwrites --rw=randwrite --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-73k-write-iops-displayed"></a>出力:73k 書き込み IOPS を表示

`Starting 4 processes`  
`Jobs: 4 (f=4): [w(4)][26.7%][r=0KiB/s,w=571MiB/s][r=0,w=73.0k IOPS][eta 00m:22s]`

## <a name="fio-examples-for-bandwidth"></a>帯域幅用の FIO の例

このセクションの例は、FIO のシーケンシャル読み取りおよび書き込みを示します。

### <a name="fio-64k-block-size-100-sequential-reads"></a>FIO:64k ブロック サイズ 100% シーケンシャル読み取り

`fio --name=64kseqreads --rw=read --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-118-gbits-throughput-displayed"></a>出力:11.8 Gbit/秒のスループットを表示

`Starting 4 processes`  
`Jobs: 4 (f=4): [R(4)][40.0%][r=1313MiB/s,w=0KiB/s][r=21.0k,w=0 IOPS][eta 00m:09s]`

### <a name="fio-64k-block-size-100-sequential-writes"></a>FIO:64k ブロック サイズ 100% シーケンシャル書き込み

`fio --name=64kseqwrites --rw=write --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-122-gbits-throughput-displayed"></a>出力:12.2 Gbit/秒のスループットを表示

`Starting 4 processes`  
`Jobs: 4 (f=4): [W(4)][85.7%][r=0KiB/s,w=1356MiB/s][r=0,w=21.7k IOPS][eta 00m:02s]`

## <a name="volume-metrics"></a>ボリューム メトリック

Azure NetApp Files のパフォーマンス データは Azure Monitor のカウンターから取得できます。 カウンターは Azure portal から、また REST API の GET 要求を使用して取得できます。 

次の情報の履歴データを確認できます。
* 読み取りの平均待機時間 
* 書き込みの平均待機時間 
* 読み取り IOPS (平均)
* 書き込み IOPS (平均)
* ボリュームの論理サイズ (平均)
* ボリュームのスナップショット サイズ (平均)

### <a name="using-azure-monitor"></a>Azure Monitor の使用 

次に示すように、[Metrics] (メトリック) ページから、ボリュームごとの Azure NetApp Files カウンターにアクセスできます。

![Azure Monitor のメトリック](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-metrics.png)

Azure NetApp Files 用のダッシュボードを Azure Monitor に作成することもできます。[Metrics] (メトリック) ページに移動し、"NetApp" で絞り込んで、目的のボリューム カウンターを指定します。 

![Azure Monitor ダッシュボード](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-dashboard.png)

### <a name="azure-monitor-api-access"></a>Azure Monitor API アクセス

REST API 呼び出しを使用して Azure NetApp Files カウンターにアクセスできます。 「[Azure Monitor のサポートされるメトリック: Microsoft.NetApp/netAppAccounts/capacityPools/Volumes](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftnetappnetappaccountscapacitypoolsvolumes)」で、容量プールおよびボリュームのカウンターを確認してください。

次の例は、論理ボリューム サイズを確認するための GET URL を示しています。

`#get ANF volume usage`  
`curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/ANFACCOUNTGOESHERE/capacityPools/ANFPOOLGOESHERE/Volumes/ANFVOLUMEGOESHERE/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=VolumeLogicalSize`


## <a name="next-steps"></a>次の手順

- [Azure NetApp Files のサービス レベル](azure-netapp-files-service-levels.md)
- [Azure NetApp Files のパフォーマンス ベンチマーク](azure-netapp-files-performance-benchmarks.md)