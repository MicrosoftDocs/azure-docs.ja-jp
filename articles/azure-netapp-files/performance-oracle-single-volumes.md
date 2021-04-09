---
title: Azure NetApp Files の単一ボリュームでの Oracle データベースのパフォーマンス | Microsoft Docs
description: Oracle データベースの Azure NetApp Files 単一ボリュームのパフォーマンス テスト結果について説明します。
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
ms.date: 09/30/2020
ms.author: b-juche
ms.openlocfilehash: c6cdf2f6dada0aa4dea2f70f18237b7ee39e3ea1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91571215"
---
# <a name="oracle-database-performance-on-azure-netapp-files-single-volumes"></a>Azure NetApp Files の単一ボリュームでの Oracle データベースのパフォーマンス

この記事では、クラウドにおける Oracle に関する次のトピックについて説明します。 これらのトピックは、データベース管理者、クラウド アーキテクト、またはストレージ アーキテクトに特に関係があります。   

* オンライン トランザクション処理 (OLTP) ワークロード (ほとんどがランダム I/O) またはオンライン分析処理 (OLAP) ワークロード (ほとんどがシーケンシャル I/O) を処理する場合、パフォーマンスはどのようになるか。   
* 通常の Linux カーネル NFS (kNFS) クライアントと Oracle 独自の Direct NFS クライアントではパフォーマンスはどのように異なるか。
* 帯域幅に関して、1 つの Azure NetApp Files ボリュームのパフォーマンスで十分か。

## <a name="testing-environment-and-components"></a>テスト環境とコンポーネント

次の図は、テストに使用された環境を示しています。 一貫性と簡潔さを実現するために、テスト ベッドのすべての要素をデプロイするために、Ansible プレイブックを使用しました。

![Oracle テスト環境](../media/azure-netapp-files/performance-oracle-test-environment.png)  

### <a name="virtual-machine-configuration"></a>仮想マシンの構成

テストでは、仮想マシンに対して次のセットアップを使用しました。
* オペレーティング システム:   
    RedHat Enterprise Linux 7.8 (wle-ora01)
* インスタンスの種類:   
    テストでは 2 つのモデル（D32s_v3 と D64s_v3）を使用
* ネットワーク インターフェイスの数:   
    サブネット 3 に 1 つ配置  
* ディスク:   
    Oracle バイナリと OS を 1 つの Premium ディスクに配置

### <a name="azure-netapp-files-configuration"></a><a name="anf_config"></a>Azure NetApp Files 構成
テストでは、次の Azure NetApp Files 構成を使用しました。   

* 容量プール サイズ:  
    さまざまなサイズのプールを構成:4 TiB、8 TiB、16 TiB、32 TiB 
* サービス レベル:  
    Ultra (割り当て済みボリューム容量 1 TiB ごとに帯域幅 128 MiB/s)
* ボリューム:  
    1 つおよび 2 つのボリュームのテストを評価

### <a name="workload-generator"></a>ワークロード ジェネレーター 

テストでは、ワークロード ジェネレーター SLOB 2.5.4.2 を使用しました。 SLOB (Silly Little Oracle Benchmark) は、SGA バッファの物理 I/O ワークロードを使用して、I/O サブシステムに負荷をかけてテストすることを目的とした、Oracle スペースでよく知られているワークロード ジェネレーターです。  

SLOB 2.5.4.2 では、プラグ可能なデータベース (PDB) はサポートされていません。 そのため、PDB サポートを追加するために、`setup.sh` および `runit.sh` のスクリプトに変更を加えました。  

以下のセクションで、このテストで使用した SLOB 変数について説明します。

#### <a name="workload-80-select-20-update--random-io--slobconf-variables"></a>ワークロード: 80% SELECT、20% UPDATE | ランダム I/O - `slob.conf` 変数   

`UPDATE_PCT=20`   
`SCAN_PCT=0`   
`RUN_TIME=600`   
`WORK_LOOP=0`   
`SCALE=75G`   
`SCAN_TABLE_SZ=50G`   
`WORK_UNIT=64`   
`REDO_STRESS=LITE`   
`LOAD_PARALLEL_DEGREE=12`   

#### <a name="workload-100-select--sequential-io--slobconf-variables"></a>ワークロード: 100% SELECT | シーケンシャル I/O - `slob.conf` 変数

`UPDATE_PCT=0`   
`SCAN_PCT=100`   
`RUN_TIME=600`   
`WORK_LOOP=0`   
`SCALE=75G`   
`SCAN_TABLE_SZ=50G`   
`WORK_UNIT=64`   
`REDO_STRESS=LITE`   
`LOAD_PARALLEL_DEGREE=12`   

### <a name="database"></a>データベース

テストで使用した Oracle バージョンは Oracle Database Enterprise Edition 19.3.0.0 です。

Oracle パラメーターは、次のとおりです。  
* `sga_max_size`:4096M
* `sga_target`:4096
* `db_writer_processes`:12
* `awr_pdb_autoflush_enabled`: true
* `filesystemio_options`:SETALL
* `log_buffer`:134217728

SLOB データベースの PDB が作成されました。

次の図は、4つの SLOB ユーザー スキーマをホストするために作成された PERFIO という名前の 600 GB のサイズ (それぞれ 30 GB の 20 個のデータ ファイル) の名前空間を示しています。 各ユーザー スキーマのサイズは 125 GB でした。

![Oracle データベース](../media/azure-netapp-files/performance-oracle-tablespace.png)  

## <a name="performance-metrics"></a>パフォーマンス メトリック

目標は、アプリケーションで見られた IO パフォーマンスを報告することでした。 したがって、この記事のすべての図では、Oracle データベースによって、その Automatic Workload Repository (AWR) レポートで報告されたメトリックが使用されています。 図で使用されるメトリックは次のとおりです。   

* **Average IO Requests/sec (平均 IO 要求数/秒)**    
    プロファイルの読み込みセクションからの平均読み取り IO 要求数/秒と平均書き込み IO 要求数/秒の合計に対応します
* **Average IO MB/sec (平均 IO MB/秒)**    
    プロファイルの読み込みセクションからの平均読み取り IO MB/秒と平均書き込み IO MB/秒の合計に対応します
* **Average Read latency (読み取りの平均待機時間)**    
    Oracle Wait イベントの "db file sequential read" (db ファイルの順次読み取り) の平均待機時間 (マイクロ秒) に対応します
* **Number of threads/schema (スキーマあたりのスレッド数)**    
    ユーザー スキーマあたりの SLOB スレッド数に対応します

## <a name="performance-measurement-results"></a>パフォーマンス測定の結果  

このセクションでは、パフォーマンス測定の結果について説明します。

### <a name="linux-knfs-client-vs-oracle-direct-nfs"></a>Linux kNFS クライアントと Oracle Direct NFS の比較

このシナリオは、Azure VM Standard_D32s_v3 (2.30 GHz の Intel E5-2673 v4) で実行されました。 ワークロードは、75% が SELECT で 25% が UPDATE (ほとんどがランダム I/O) であり、データベース バッファーのヒットは約 7.5% です。 

次の図に示すように、Oracle DNFS クライアントは、通常の Linux kNFS クライアントの最大 2.8 倍のスループットを提供しています。  

![Linux kNFS クライアントと Oracle Direct NFS の比較](../media/azure-netapp-files/performance-oracle-kfns-compared-dnfs.png)  

次の図は、読み取り操作の待機時間曲線を示しています。 このコンテキストでは、kNFS クライアントのボトルネックは、クライアントと NFS サーバー (Azure NetApp Files ボリューム) の間に確立される単一の NFS TCP ソケット接続です。  

![Linux kNFS クライアントと Oracle Direct NFS の待機時間曲線の比較](../media/azure-netapp-files/performance-oracle-latency-curve.png)  

DNFS クライアントは、数百の TCP ソケット接続を作成できるため 1 秒あたりの IO 要求のプッシュ回数が増加しました。このため、並列処理の利点を活用できます。 「[Azure NetApp Files 構成](#anf_config)」で説明されているように、容量を 1 TiB 追加で割り当てるごとに、128 MiB/秒の帯域幅を追加できます。 DNFS のスループットは 1 GiB/秒で頭打ちとなります。これは 8 TiB の容量選択によって課される制限です。 容量を増やすと、より多くのスループットが得られます。

スループットは考慮事項の 1 つにすぎません。 もう 1 つの考慮事項は、ユーザー エクスペリエンスに大きな影響を与える待機時間です。 次の図に示すように、kNFS の方が DNFS よりも、待機時間の急激な増加が予想されます。 

![Linux kNFS クライアントと Oracle Direct NFS の読み取り待機時間の比較](../media/azure-netapp-files/performance-oracle-read-latency.png)  

ヒストグラムは、データベースの待機時間に関する優れた洞察を提供します。 次の図は、最大の同時実行データ ポイント (32 スレッド/スキーマ) で DNFS を使用したときの、記録された "db file sequential read"(db ファイルの順次読み取り) の観点から見た完全なビューを示しています。 次の図に示すように、全読み取り操作の 47% が 512 マイクロ秒と 1000 マイクロ秒の間で処理され、全読み取り操作の 90% が 2 ミリ秒未満の待機時間で処理されました。

![Linux kNFS クライアントと Oracle Direct NFS のヒストグラムの比較](../media/azure-netapp-files/performance-oracle-histogram-read-latency.png)  

結論としては明らかなのは、NFS で Oracle データベース インスタンスのパフォーマンスを向上させるには DNFS が必要であるということです。

### <a name="single-volume-performance-limits"></a>単一ボリュームのパフォーマンス制限

このセクションでは、ランダム I/O とシーケンシャル I/O を使用した単一ボリュームのパフォーマンスの制限について説明します。 

#### <a name="random-io"></a>ランダム I/O

DNFS は、Azure NetApp Files の 8 TB のパフォーマンス クォータによって提供されるものよりもはるかに広い帯域幅を消費できます。 Azure NetApp Files ボリューム容量を 16 TiB に増やすことで (これは瞬時に変更されます)、ボリューム帯域幅の量は 1024 MiB/秒から 2 倍の 2048 MiB/秒に増加しました。 

次の図は、80% の SELECT および 20% の UPDATE のワークロードで、データベース バッファーのヒット率が 8% の構成を示しています。 SLOB では、単一ボリュームで 1 秒間に 20 万 NFS I/O 要求まで実行することができました。 各操作が 8 KiB のサイズであることを考慮すると、テスト対象のシステムでは、1 秒間に約 20 万 IO 要求/秒または 1600 MiB/秒を処理できました。
 
![Oracle DNFS のスループット](../media/azure-netapp-files/performance-oracle-dnfs-throughput.png)  

次の読み取り待機時間曲線図は、読み取りスループットが増加するにつれて、待機時間が 1 ミリ秒未満で緩やかに増加し、1 秒間の平均読み取り IO 要求数が約 165,000 で、平均読み取り待機時間が約 1.3 ミリ秒のときに、曲線の急な折れ曲がりに達したことを示しています。  この値は、Azure Cloud の他のほとんどすべてのテクノロジで実現できない I/O レートでの優れた待機時間値です。 

![Oracle DNFS の待機時間曲線](../media/azure-netapp-files/performance-oracle-dnfs-latency-curve.png)  

#### <a name="sequential-io"></a>シーケンシャル I/O  

たとえば RMAN バックアップまたはフル テーブル スキャンを、できるだけ多くの帯域幅を必要とするワークロードとみなした場合、次の図に示すように、すべての I/O がランダムであるとは限りません。  前に説明したのと同じ構成を、ボリュームのサイズを 32 TiB に変更して使用した場合、次の図は、1 つの Oracle DB インスタンスが 3900 MB/秒のスループットを超えていることを示しており、これは Azure NetApp Files ボリュームの 32 TB のパフォーマンス クォータ (128 MB/秒 * 32 = 4096 MB/秒) に非常に近いものです。

![Oracle DNFS の I/O](../media/azure-netapp-files/performance-oracle-dnfs-io.png)  

要約すると、Azure NetApp Files は Oracle データベースをクラウドに移行するのに役立ちます。 これにより、データベースが要求したときにパフォーマンスが提供されます。 ボリューム クォータのサイズは、動的に、かつ中断することなく、いつでも変更できます。

## <a name="next-steps"></a>次のステップ

- [Azure NetApp Files のパフォーマンス ベンチマークのテスト レコメンデーション](azure-netapp-files-performance-metrics-volumes.md)
- [Linux のパフォーマンス ベンチマーク](performance-benchmarks-linux.md)