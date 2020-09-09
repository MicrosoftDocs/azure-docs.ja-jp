---
title: Azure における SQL Server のパフォーマンスに関するガイドライン | Microsoft Docs
description: Microsoft Azure Virtual Machines での SQL Server のパフォーマンスを最適化するためのガイドラインを紹介します。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: a0c85092-2113-4982-b73a-4e80160bac36
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/18/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: a2ba89a9adec5443ed8ae2a10e0230874b571f46
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88690240"
---
# <a name="performance-guidelines-for-sql-server-on-azure-virtual-machines"></a>Azure Virtual Machines 上の SQL Server のパフォーマンスに関するガイドライン
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

この記事では、Microsoft Azure Virtual Machines での SQL Server のパフォーマンスを最適化するためのガイダンスを紹介します。

## <a name="overview"></a>概要

 Azure Virtual Machines 上の SQL Server を実行するときは、オンプレミスのサーバー環境で SQL Server に適用されるデータベース パフォーマンス チューニング オプションと同じものを引き続き使用することをお勧めします。 ただし、パブリック クラウド内のリレーショナル データベースのパフォーマンスは、仮想マシンのサイズやデータ ディスクの構成などのさまざまな要素に左右されます。

[Azure portal でプロビジョニングされる SQL Server イメージ](sql-vm-create-portal-quickstart.md)は、一般的なストレージ構成のベスト プラクティスに従います (ストレージの構成方法の詳細については、「[SQL Server 仮想マシン (VM) のストレージの構成](storage-configuration.md)」を参照してください)。 プロビジョニングした後で、この記事で説明するその他の最適化を適用することを検討します。 ワークロードに関する選択を基準に、テストを通して検証します。

> [!TIP]
> 通常、コストの最適化とパフォーマンスの最適化はトレードオフの関係になっています。 この記事では、Azure Virtual Machines 上の SQL Server の "*最大の*" パフォーマンスを得ることに重点を置いています。 ワークロードの要求が厳しくない場合は、以下に示す最適化がすべて必要になるわけではありません。 各推奨事項を評価するときに、パフォーマンスのニーズ、コスト、およびワークロードのパターンを考慮してください。

## <a name="quick-checklist"></a>クイック チェックリスト

Azure Virtual Machines 上の SQL Server の最適なパフォーマンスを実現するためのクイック チェックリストを次に示します。

| 領域 | 最適化 |
| --- | --- |
| [VM サイズ](#vm-size-guidance) | - [E4S_v3](../../../virtual-machines/ev3-esv3-series.md) 以上や [DS12_v2](../../../virtual-machines/dv2-dsv2-series-memory.md) 以上などの vCPU が 4 個以上の VM サイズを使用します。<br/><br/> - [Es、Eas、Ds、および Das シリーズ](../../../virtual-machines/sizes-general.md)は、OLTP ワークロードのパフォーマンスに必要な最適なメモリと vCPU の比率を提供します。 <br/><br/> - [M シリーズ](../../../virtual-machines/m-series.md)は、ミッション クリティカルなパフォーマンスに必要な最も高いメモリと vCPU の比率を提供し、データ ウェアハウスのワークロードに最適です。 <br/><br/> - [アプリケーションのパフォーマンス要件チェックリスト](../../../virtual-machines/windows/premium-storage-performance.md#application-performance-requirements-checklist)に従ってピーク時のターゲット ワークロードの [IOPS](../../../virtual-machines/windows/premium-storage-performance.md#iops)、[スループット](../../../virtual-machines/windows/premium-storage-performance.md#throughput)、および[待機時間](../../../virtual-machines/windows/premium-storage-performance.md#latency)の要件を収集した後、ワークロードのパフォーマンス要件にスケーリングできる [VM サイズ](../../../virtual-machines/sizes-general.md)を選択します。|
| [Storage](#storage-guidance) | - TPC-E および TPC_C ベンチマークによる Azure Virtual Machines 上の SQL Server のパフォーマンスの詳細なテストについては、[OLTP のパフォーマンスの最適化](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794)に関するブログを参照してください。 <br/><br/> - 最適な価格/パフォーマンス比の利点を得るために [Premium SSD](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794) を使用します。 データ ファイルに対しては[読み取り専用キャッシュ](../../../virtual-machines/windows/premium-storage-performance.md#disk-caching)を、ログ ファイルに対してはキャッシュなしを構成します。 <br/><br/> - ワークロードで 1 ミリ秒未満のストレージ待機時間が必要な場合は、[Ultra ディスク](../../../virtual-machines/disks-types.md#ultra-disk)を使用します。 詳細については、[Ultra Disk への移行](storage-migrate-to-ultradisk.md)に関するページを参照してください。 <br/><br/> - ディスクの種類を選択する前に、[アプリケーションを監視](../../../virtual-machines/windows/premium-storage-performance.md#application-performance-requirements-checklist)することによって、SQL Server データ、ログ、および Temp DB ファイルのストレージ待機時間の要件を収集します。 1 ミリ秒未満のストレージ待機時間が必要な場合は Ultra ディスクを使用し、それ以外の場合は Premium SSD を使用します。 短い待機時間がログ ファイルにのみ必要であり、データ ファイルには必要ない場合は、ログ ファイルに対してのみ、必要な IOPS とスループットのレベルで [Ultra ディスクをプロビジョニング](../../../virtual-machines/disks-enable-ultra-ssd.md)します。 <br/><br/> SQL Server フェールオーバー クラスター インスタンスの共有ストレージとして、-  [Premium ファイル共有](failover-cluster-instance-premium-file-share-manually-configure.md)が推奨されます。 Premium ファイル共有ではキャッシュがサポートされず、Premium SSD ディスクと比較して提供されるパフォーマンスが制限されます。 スタンドアロンの SQL インスタンスには、Premium ファイル共有より Premium SSD マネージド ディスクを選択します。ただし、メンテナンスの容易性や柔軟なスケーラビリティのために、フェールオーバー クラスター インスタンスの共有ストレージには Premium ファイル共有を利用してください。 <br/><br/> - 標準ストレージは、開発とテストの目的またはバックアップ ファイルにのみ推奨されます。運用ワークロードには使用しないでください。 <br/><br/> - [ストレージ アカウント](../../../storage/common/storage-create-storage-account.md)と SQL Server VM を同じリージョンに保持します。<br/><br/> - ストレージ アカウントで [Azure geo 冗長ストレージ](../../../storage/common/storage-redundancy.md) (geo レプリケーション) を無効にします。  |
| [ディスク](#disks-guidance) | - 少なくとも 2 つの [Premium SSD ディスク](../../../virtual-machines/disks-types.md#premium-ssd) (ログ ファイル用に 1 つとデータ ファイル用に 1 つ) を使用します。 <br/><br/> - 1 ミリ秒未満の IO 待機時間が必要なワークロードの場合は、M シリーズの書き込みアクセラレータを有効にし、Es および DS シリーズでの Ultra SSD ディスクの使用を考慮します。 <br/><br/> - データ ファイルをホストするディスクで[読み取り専用キャッシュ](../../../virtual-machines/windows/premium-storage-performance.md#disk-caching)を有効にします。<br/><br/> - [SQL Server データ、ログ、および TempDB ファイルのストレージを構成](storage-configuration.md)するときに、ワークロードに必要な追加の 20% Premium IOPS/スループット容量を追加します。 <br/><br/> - データベース ストレージまたはログに、オペレーティング システム ディスクまたは一時ディスクを使用することは避けてください。<br/><br/> - ログ ファイルをホストするディスクでは、キャッシュを有効にしないでください。  **重要**:Azure Virtual Machines ディスクのキャッシュ設定を変更するときは、SQL Server サービスを停止してください。<br/><br/> - 複数の Azure データ ディスクをストライプして、ストレージ スループットを向上させます。<br/><br/> - ドキュメントに記載されている割り当てサイズでフォーマットします。 <br/><br/> - ミッション クリティカルな SQL Server ワークロードのために、TempDB をローカル SSD の `D:\` ドライブに配置します (適切な VM サイズを選択した後に行います)。 Azure portal または Azure クイックスタート テンプレートから VM を作成し、[ローカル ディスクに Temp DB を配置する](https://techcommunity.microsoft.com/t5/SQL-Server/Announcing-Performance-Optimized-Storage-Configuration-for-SQL/ba-p/891583)場合は、それ以上のアクションは必要ありません。その他のすべての場合は、再起動後の障害を防止するために、[SSD を使用した TempDB の格納](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/)のブログにある手順に従ってください。 ローカル ドライブの容量が Temp DB サイズに対して十分でない場合は、[読み取り専用キャッシュ](../../../virtual-machines/windows/premium-storage-performance.md#disk-caching)を備えた Premium SSD ディスク上の[ストライプされた](../../../virtual-machines/windows/premium-storage-performance.md)ストレージ プールに Temp DB を配置します。 |
| [I/O](#io-guidance) |- データベース ページの圧縮を有効にします。<br/><br/> - データ ファイルの瞬時初期化を有効にします。<br/><br/> - データベースの自動拡張を制限します。<br/><br/> - データベースの自動圧縮を無効にします。<br/><br/> - システム データベースも含め、すべてのデータベースをデータ ディスクに移動します。<br/><br/> - SQL Server エラー ログとトレース ファイルのディレクトリをデータ ディスクに移動します。<br/><br/> - 既定のバックアップとデータベース ファイルの場所を構成します。<br/><br/> - [メモリ内のロックされたページを有効にします](/sql/database-engine/configure-windows/enable-the-lock-pages-in-memory-option-windows?view=sql-server-2017)。<br/><br/> - SQL Server パフォーマンス修正プログラムを適用します。 |
| [機能固有](#feature-specific-guidance) | - Azure Blob Storage に直接バックアップします。<br/><br/>- 12 TB を超えるデータベースには[ファイル スナップショットのバックアップ](/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure)を使用します。 <br/><br/>- 複数の Temp DB ファイル (コアあたり 1 ファイル、最大 8 ファイル) を使用します。<br/><br/>- 最大サーバー メモリを 90% で設定するか、またはオペレーティング システムに最大 50 GB を残します。 <br/><br/>- ソフト NUMA を有効にします。 |

これらの最適化を行う*方法*と*理由*については、以下のセクションに記載されている詳細とガイダンスをご確認ください。

## <a name="vm-size-guidance"></a>VM サイズのガイダンス

最初に、ピーク時のワークロードの CPU、メモリ、およびストレージのスループット要件を収集します。 \LogicalDisk\Disk Reads/Sec および \LogicalDisk\Disk Writes/Sec パフォーマンス カウンターを使用すると、読み取りと書き込みの IOPS 要件を収集でき、\LogicalDisk\Disk Bytes/Sec カウンターを使用すると、データ、ログ、および Temp DB ファイルの[ストレージのスループット要件](../../../virtual-machines/premium-storage-performance.md#disk-caching)を収集できます。 ピーク時の IOPS とスループット要件が定義されたら、VM サイズによってその容量が提供されるかどうかを評価します。 たとえば、ワークロードがピーク時に 20 K の読み取り IOPS と 10K の書き込み IOPS を必要としている場合は、E16s_v3 (最大 32 K のキャッシュあり IOPS と 25600 のキャッシュなし IOPS)、または 2 つの P30 ディスクを備えた M16_s (最大 20 K のキャッシュあり IOPS と 10K のキャッシュなし IOPS) のどちらかを選択できます。 VM には IOPS とスループットに対して異なるスケールの制限があるため、ワークロードのスループット要件と IOPS 要件の両方を把握するようにしてください。<br/><br/>[DSv_3](../../../virtual-machines/dv3-dsv3-series.md) および [Es_v3 シリーズ](../../../virtual-machines/ev3-esv3-series.md)は、Intel Haswell または Broadwell プロセッサを備えた汎用ハードウェアでホストされます。 [M シリーズ](../../../virtual-machines/m-series.md)は、最大の SQL Server ワークロードのための最も多い vCPU 数とメモリを提供し、Skylake プロセッサ ファミリを備えたメモリ最適化ハードウェアでホストされます。 これらの VM シリーズは、ホスト レベルの読み取りキャッシュによる最適なパフォーマンスのために推奨される Premium Storage をサポートしています。 また、Es_v3 シリーズと M シリーズはどちらも、[制約されたコア サイズ](../../../virtual-machines/constrained-vcpu.md)で使用できます。これにより、コンピューティングの要求が低く、ストレージ容量の要求が高いワークロードのコストが削減されます。 

## <a name="storage-guidance"></a>ストレージのガイダンス

TP C-E および TPC_C ベンチマークによる Azure Virtual Machines 上の SQL Server のパフォーマンスの詳細なテストについては、[OLTP のパフォーマンスの最適化](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794)に関するブログを参照してください。 

Premium SSD を使用した Azure BLOB キャッシュは、すべての運用ワークロードに推奨されます。 

> [!WARNING]
> Standard HDD と SSD には、さまざまな待機時間や帯域幅があり、開発/テスト ワークロードにのみ推奨されます。 運用環境のワークロードでは、Premium SSD を使用する必要があります。

さらに、転送遅延を低減するために、SQL Server 仮想マシンと同じデータ センターで Azure ストレージ アカウントを作成することをお勧めします。 ストレージ アカウントの作成時に、geo レプリケーションを無効にします。複数のディスクでの一貫性のある書き込み順序が保証されないためです。 代わりに、2 つの Azure データ センター間で SQL Server ディザスター リカバリー テクノロジを構成することを検討します。 詳細については、「 [Azure Virtual Machines における SQL Server の高可用性と障害復旧](business-continuity-high-availability-disaster-recovery-hadr-overview.md)」を参照してください。

## <a name="disks-guidance"></a>ディスクのガイダンス

Azure 仮想マシンには、3 種類のメイン ディスクがあります。

* **OS ディスク**:Azure 仮想マシンを作成すると、プラットフォームによって、オペレーティング システム ディスク用に少なくとも 1 つのディスク (**C** ドライブとしてラベル付けされます) が VM に接続されます。 このディスクは、ページ BLOB としてストレージに格納されている VHD です。
* **一時ディスク**:Azure Virtual Machines には、一時ディスクと呼ばれる別のディスク (**D**: ドライブとしてラベル付けされる) が含まれています。 これは、スクラッチ領域に使用できるノード上のディスクです。
* **データ ディスク**:追加のディスクをデータ ディスクとして仮想マシンに接続することもできます。これらのディスクは、ページ BLOB としてストレージに格納されます。

次のセクションでは、これらの異なるディスクの使用に関する推奨事項について説明します。

### <a name="operating-system-disk"></a>オペレーティング システム ディスク

オペレーティング システム ディスクは、実行中のバージョンのオペレーティング システムとして起動およびマウントできる VHD であり、**C** ドライブとしてラベル付けされます。

オペレーティング システム ディスクの既定のキャッシュ ポリシーは、 **読み取り/書き込み**です。 パフォーマンス重視のアプリケーションでは、オペレーティング システム ディスクではなく、データ ディスクを使用することをお勧めします。 下記のデータ ディスクに関するセクションをご覧ください。

### <a name="temporary-disk"></a>一時ディスク

**D** ドライブのラベルが付いた一時ストレージ ドライブは、Azure Blob Storage に保持されません。 ユーザー データベース ファイルやユーザー トランザクション ログ ファイルを **D**: ドライブに保存しないでください。

ミッション クリティカルな SQL Server ワークロードのために (適切な VM サイズを選択した後) TempDB をローカル SSD の `D:\` ドライブに配置します。 Azure portal または Azure クイックスタート テンプレートから VM を作成し、[ローカル ディスクに Temp DB を配置する](https://techcommunity.microsoft.com/t5/SQL-Server/Announcing-Performance-Optimized-Storage-Configuration-for-SQL/ba-p/891583)場合は、それ以上のアクションは必要ありません。その他のすべての場合は、再起動後の障害を防止するために、[SSD を使用した TempDB の格納](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/)のブログにある手順に従ってください。 ローカル ドライブの容量が Temp DB サイズに対して十分でない場合は、[読み取り専用キャッシュ](../../../virtual-machines/premium-storage-performance.md#disk-caching)を備えた Premium SSD ディスク上の[ストライプされた](../../../virtual-machines/premium-storage-performance.md)ストレージ プールに Temp DB を配置します。

Premium SSD をサポートする VM の場合は、読み取りキャッシュが有効になっている Premium SSD をサポートするディスクに TempDB を格納することもできます。


### <a name="data-disks"></a>データ ディスク

* **データ ファイルとログ ファイルに Premium SSD ディスクを使用する**: ディスク ストライピングを使用していない場合は、1 つのディスクにログ ファイルが含まれ、もう一方のディスクにデータが含まれた 2 つの Premium SSD ディスクを使用します。 [ディスクの種類の選択](../../../virtual-machines/disks-types.md)に関する記事に示されているように、各 Premium SSD は、そのサイズに応じた量の IOPS と帯域幅 (MB/秒) を提供します。 記憶域スペースなどのディスク ストライピング技法を使用している場合は、最適なパフォーマンスを実現するには、2 つのプール (ログ ファイル用に 1 つとデータ ファイル用に 1 つ) を使用します。 ただし、SQL Server フェールオーバー クラスター インスタンス (FCI) を使用する予定がある場合は、1 つのプールを構成するか、または代わりに [Premium ファイル共有](failover-cluster-instance-premium-file-share-manually-configure.md)を利用する必要があります。

   > [!TIP]
   > - さまざまなディスクとワークロードの構成に関するテスト結果については、ブログ記事「[Azure Virtual Machines 上の SQL Server 用ストレージ構成ガイドライン](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm/)」を参照してください。
   > - 最大 50,000 の IOPS を必要とする SQL Server のミッション クリティカルなパフォーマンスには、10 -P30 ディスクを Ultra SSD と交換することを検討してください。 詳細については、ブログ記事「[Mission critical performance with Ultra SSD (Ultra SSD によるミッション クリティカルなパフォーマンス)](https://azure.microsoft.com/blog/mission-critical-performance-with-ultra-ssd-for-sql-server-on-azure-vm/)」を参照してください。

   > [!NOTE]
   > SQL Server VM をポータルからプロビジョニングするとき、必要に応じてストレージの構成を編集することができます。 Azure では、実際の構成に応じて 1 つまたは複数のディスクが構成されます。 複数のディスクは、ストライピングを使用して 1 つの記憶域プールにまとめられます。 この構成では、データ ファイルとログ ファイルが一緒に格納されます。 詳細については、「[SQL Server VM のストレージの構成](storage-configuration.md)」を参照してください。

* **ディスク ストライピング**:スループットを向上させるために、データ ディスクをさらに追加し、ディスク ストライピングを使用できます。 データ ディスク数を決定するには、ログ ファイルおよびデータと TempDB ファイルのために必要な IOPS 数と帯域幅を分析する必要があります。 VM サイズが異なると、サポートされる IOPS 数と帯域幅の制限も変わります。[VM サイズ](../../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)ごとの IOPS ついて表を参照してください。 次のガイドラインに従ってください。

  * Windows 8/Windows Server 2012 以降の場合は､次のガイドラインに従った[記憶域スペース](https://technet.microsoft.com/library/hh831739.aspx)を使用します｡

      1. パーティションの不整合によるパフォーマンスへの影響を回避するために、OLTP ワークロードの場合はインタリーブ (ストライプ サイズ) を 64 KB (65,536 バイト) に、データ ウェアハウス ワークロードの場合は 256 KB (262,144 バイト) に設定します。 これは､PowerShell を使って設定する必要があります｡
      2. カラム数 = 物理ディスク数を設定します｡ 8 つを超えるディスクを構成する場合は PowerShell を使用します (Server Manager の UI ではない)｡ 

    たとえば、次の PowerShell では記憶域プールが新規作成され、インターリーブ サイズは 64 KB、カラム数はこの記憶域プール内の物理ディスクの容量と等しくなります。

    ```powershell
    $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}
    
    New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" `
        -PhysicalDisks $PhysicalDisks | New- VirtualDisk -FriendlyName "DataFiles" `
        -Interleave 65536 -NumberOfColumns $PhysicalDisks .Count -ResiliencySettingName simple `
        –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter `
        -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" `
        -AllocationUnitSize 65536 -Confirm:$false 
    ```

  * Windows 2008 R2 以前では、ダイナミック ディスク (OS ストライプ ボリューム) を使用できます。ストライプ サイズは常に 64 KB です。 このオプションは、Windows 8/Windows Server 2012 の時点で非推奨となっています。 詳細については、[Windows Storage Management API に移行しつつある仮想ディスク サービス](https://msdn.microsoft.com/library/windows/desktop/hh848071.aspx)に関するページでサポートに関する声明をご覧ください。

  * [記憶域スペース ダイレクト (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm) を [SQL Server フェールオーバー クラスター インスタンス](failover-cluster-instance-storage-spaces-direct-manually-configure.md)で使用している場合は、単一プールを構成する必要があります。 その単一プール上にはさまざまなボリュームを作成できますが、それらはすべて同じ特性 (たとえば、同じキャッシュ ポリシー) を共有します。

  * 負荷予測に基づいて、ご使用の記憶域プールに関連付けるディスク数を決定します。 接続できるデータ ディスクの数は VM サイズによって異なることに注意してください。 詳細については、 [仮想マシンのサイズ](../../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関するページをご覧ください。

  * Premium SSD (開発/テスト シナリオ) を使用しない場合は、ご使用の [VM サイズ](../../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)でサポートされる最大数のデータ ディスクを追加し、ディスク ストライピングを使用することをお勧めします。

* **キャッシュ ポリシー**:ストレージ構成に応じたキャッシュ ポリシーに対する次の推奨事項に注意してください。

  * データとログ ファイル用に別個のディスクを使用する場合は、データ ファイルと TempDB データ ファイルをホストするデータ ディスクで読み取りキャッシュを有効にします。 これにより、パフォーマンスが大幅に向上する可能性があります。 ログ ファイルを保持するディスクではキャッシュを有効にしないでください。これを行うと、パフォーマンスが多少低下します。

  * 単一ストレージ プールでディスク ストライピングを使用すると、ほとんどのワークロードで読み取りキャッシュのメリットが得られます。 ログ ファイルとデータ ファイルで異なるストレージ プールを使用している場合は、データ ファイル用のストレージ プールでのみ読み取りキャッシュを有効にします。 書き込みが多いワークロードでは、キャッシュなしのほうがパフォーマンスが向上する場合があります。 これは、テストを実行することでのみ判断できます。

  * これらの推奨事項は、Premium SSD ディスクに適用されます。 Premium SSD を使用していない場合は、どのデータ ディスクでもキャッシュを有効にしないでください。

  * ディスク キャッシュの構成手順については、以下の記事をご覧ください。 クラシック (ASM) デプロイ モデルの場合:[Set-AzureOSDisk](https://msdn.microsoft.com/library/azure/jj152847) と [Set-AzureDataDisk](https://msdn.microsoft.com/library/azure/jj152851.aspx) に関するページを参照してください。 Azure Resource Manager デプロイ モデルについては、[Set-AzOSDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk) と [Set-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmdatadisk) に関するページを参照してください。

     > [!WARNING]
     > データベースの破損の可能性を回避するために、Azure Virtual Machines ディスクのキャッシュ設定を変更するときは、SQL Server サービスを停止してください。

* **NTFS アロケーション ユニット サイズ**:データ ディスクをフォーマットするときは、データ ファイルとログ ファイルに加えて TempDB にも 64 KB アロケーション ユニット サイズを使用することをお勧めします。 TempDB が一時ディスク (D:\ ドライブ) に配置されている場合、このドライブの利用で得られるパフォーマンスのメリットは 64K のアロケーション ユニット サイズの必要性を上回ります。 

* **ディスク管理のベスト プラクティス**:データ ディスクの削除またはキャッシュの種類の変更を行う場合、変更中は SQL Server サービスを停止します。 OS ディスクでキャッシュ設定が変更されると、Azure は VM を停止し、キャッシュの種類を変更して、VM を再起動します。 データ ディスクのキャッシュ設定が変更されても VM は停止されませんが、変更中、データ ディスクが VM から切断され、その後再接続されます。

  > [!WARNING]
  > これらの操作中に、SQL Server サービスの停止を怠ると、データベースが破損するおそれがあります。


## <a name="io-guidance"></a>I/O のガイダンス

* Premium SSD では、アプリケーションと要求の並列処理を実行するときに最良の結果が得られます。 Premium SSD は、IO キューの深さが 1 より大きいシナリオ向けに設計されているので、シングル スレッド シリアル要求では (ストレージを集中的に使用する場合でも)、パフォーマンスの向上はほとんどまたはまったくありません。 たとえば、これは、パフォーマンス分析ツール (SQLIO など) のシングル スレッド テストの結果に影響する可能性があります。

* I/O 集中型ワークロードのパフォーマンスを向上させるために、 [データベース ページの圧縮](https://msdn.microsoft.com/library/cc280449.aspx) を使用することを検討します。 ただし、データ圧縮を使用すると、データベース サーバーでの CPU 消費量が増加する場合があります。

* 初期ファイル割り当てに必要な時間を短縮するために、ファイルの瞬時初期化を有効にすることを検討します。 ファイルの瞬時初期化を利用するには、SQL Server (MSSQLSERVER) サービス アカウントに SE_MANAGE_VOLUME_NAME を付与し、 **[ボリュームの保守タスクを実行]** セキュリティ ポリシーにそのサービス アカウントを追加します。 Azure の SQL Server プラットフォーム イメージを使用している場合、既定のサービス アカウント (NT Service\MSSQLSERVER) は、 **[ボリュームの保守タスクを実行]** セキュリティ ポリシーに追加されません。 つまり、SQL Server Azure プラットフォーム イメージでは、ファイルの瞬時初期化は有効になりません。 **[ボリュームの保守タスクを実行]** セキュリティ ポリシーに SQL Server サービス アカウントを追加したら、SQL Server サービスを再起動します。 この機能を使用する場合、セキュリティに関する考慮事項があります。 詳細については、「 [データベース ファイルの初期化](https://msdn.microsoft.com/library/ms175935.aspx)」をご覧ください。

* **自動拡張**は、予想外の増加に付随するものと見なされることに注意してください。 自動拡張を使用して、データやログの増加に日常的に対処しないでください。 自動拡張を使用する場合は、Size スイッチを使用してファイルを事前に拡張します。

* パフォーマンスに悪影響を及ぼすおそれのある不要なオーバーヘッドを回避するために、 **自動圧縮** が無効になっていることを確認します。

* システム データベースも含め、すべてのデータベースをデータ ディスクに移動します。 詳細については、「 [システム データベースの移動](https://msdn.microsoft.com/library/ms345408.aspx)」を参照してください。

* SQL Server エラー ログとトレース ファイルのディレクトリをデータ ディスクに移動します。 これは、SQL Server インスタンスを右クリックしてプロパティを選択することにより、SQL Server 構成マネージャーで実行できます。 エラー ログとトレース ファイルの設定は、 **[起動時のパラメーター]** タブで変更できます。ダンプ ディレクトリは、 **[詳細設定]** タブで指定します。次のスクリーンショットでは、エラー ログの起動時のパラメーターを検索する場所を示します。

    ![SQL ErrorLog のスクリーンショット](./media/performance-guidelines-best-practices/sql_server_error_log_location.png)

* 既定のバックアップ ファイルとデータベース ファイルの場所を設定します。 この記事では、推奨事項を使用し、[サーバーのプロパティ] ウィンドウで変更を行います。 手順については、「 [データ ファイルとログ ファイルの既定の場所の表示または変更 (SQL Server Management Studio)](https://msdn.microsoft.com/library/dd206993.aspx)」をご覧ください。 次のスクリーンショットでは、これらの変更を行う場所を示します。

    ![SQL データのログおよびバックアップ ファイル](./media/performance-guidelines-best-practices/sql_server_default_data_log_backup_locations.png)
* ロックされたページを有効にして、IO とページング アクティビティを減らします。 詳細については、「 [Lock Pages in Memory オプションの有効化 (Windows)](https://msdn.microsoft.com/library/ms190730.aspx)」をご覧ください。

* SQL Server 2012 を実行している場合は、Service Pack 1 Cumulative Update 10 をインストールします。 この更新プログラムには、SQL Server 2012 で一時テーブルに対して SELECT INTO ステートメントを実行したときに I/O のパフォーマンスが低下する問題に対処するための修正プログラムが含まれています。 詳細については、この [サポート技術情報の記事](https://support.microsoft.com/kb/2958012)をご覧ください。

* Azure との間での転送時にデータ ファイルを圧縮することを検討します。

## <a name="feature-specific-guidance"></a>機能固有のガイダンス

一部のデプロイでは、より高度な構成手法を使用することで、パフォーマンスがさらに向上する場合があります。 パフォーマンスの向上を実現する際に役立つ SQL Server の機能を次に示します。

### <a name="back-up-to-azure-storage"></a>Azure Storage にバックアップする
Azure Virtual Machines で実行される SQL Server のバックアップを実行する際は、[URL への SQL Server のバックアップ](https://msdn.microsoft.com/library/dn435916.aspx)を使用できます。 SQL Server 2012 SP1 CU2 以降で使用できるこの機能は、接続されているデータ ディスクにバックアップする場合に推奨されます。 Azure Storage との間でバックアップと復元を行うときは、「[SQL Server の URL へのバックアップに関するベスト プラクティスとトラブルシューティング](https://msdn.microsoft.com/library/jj919149.aspx)」に記載されている推奨事項に従ってください。 [Azure Virtual Machines 上の SQL Server の自動バックアップ](../../../azure-sql/virtual-machines/windows/automated-backup-sql-2014.md)を使用して、これらのバックアップを自動化することもできます。

SQL Server 2012 より前のバージョンでは、 [SQL Server Backup to Azure Tool](https://www.microsoft.com/download/details.aspx?id=40740)を使用できます。 このツールでは、複数のバックアップ ストライプ ターゲットを使用することで、バックアップ スループットを向上させることができます。

### <a name="sql-server-data-files-in-azure"></a>Azure 内の SQL Server データ ファイル

[Azure 内の SQL Server データ ファイル](https://msdn.microsoft.com/library/dn385720.aspx)は、SQL Server 2014 以降で使用できる新機能です。 Azure 内のデータ ファイルを使用して SQL Server を実行すると、Azure データ ディスクを使用する場合と同等のパフォーマンス特性が得られます。

### <a name="failover-cluster-instance-and-storage-spaces"></a>フェールオーバー クラスター インスタンスと記憶域スペース

記憶域スペースを使用している場合は、 **[確認]** ページでクラスターにノードを追加するときに、 **[使用可能な記憶域をすべてクラスターに追加する]** チェック ボックスをオフにします。 

![使用可能な記憶域をオフにする](./media/performance-guidelines-best-practices/uncheck-eligible-cluster-storage.png)

記憶域スペースを使っている場合に、 **[使用可能な記憶域をすべてクラスターに追加する]** をオフにしないと、Windows はクラスター作成処理中に仮想ディスクをデタッチします。 その結果、記憶域スペースがクラスターから削除され、PowerShell を使って再アタッチされるまで、仮想ディスクはディスク マネージャーやエクスプローラーに表示されなくなります。 記憶域スペースは、複数のディスクを記憶域プールにグループ化します。 詳細については、[記憶域スペース](/windows-server/storage/storage-spaces/overview)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

ストレージとパフォーマンスの詳細については、「[Azure Virtual Machines 上の SQL Server 用ストレージ構成ガイドライン](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm/)」を参照してください。

セキュリティのベスト プラクティスについては、「[Azure Virtual Machines 上の SQL Server のセキュリティに関する考慮事項](security-considerations-best-practices.md)」をご覧ください。

SQL Server Virtual Machines に関する他の記事については、[Azure Virtual Machines 上の SQL Server の概要](sql-server-on-azure-vm-iaas-what-is-overview.md)に関するページをご覧ください。 SQL Server の仮想マシンに関するご質問については、[よくあるご質問](frequently-asked-questions-faq.md)に関するページをご覧ください。
