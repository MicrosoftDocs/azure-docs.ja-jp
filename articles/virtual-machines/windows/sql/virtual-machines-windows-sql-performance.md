---
title: Azure における SQL Server のパフォーマンスに関するガイドライン | Microsoft Docs
description: Microsoft Azure VM で SQL Server のパフォーマンスを最適化するためのガイドラインを紹介します。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a0c85092-2113-4982-b73a-4e80160bac36
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 09/26/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 8d31f04c355b47720a1c9b0334042ba2f6654768
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58448570"
---
# <a name="performance-guidelines-for-sql-server-in-azure-virtual-machines"></a>Azure Virtual Machines における SQL Server のパフォーマンスに関するガイドライン

## <a name="overview"></a>概要

この記事では、Microsoft Azure Virtual Machine で SQL Server のパフォーマンスを最適化するためのガイダンスを紹介します。 Azure Virtual Machines で SQL Server を実行するときは、オンプレミスのサーバー環境で SQL Server に適用されるデータベース パフォーマンス チューニング オプションと同じものを引き続き使用することをお勧めします。 ただし、パブリック クラウド内のリレーショナル データベースのパフォーマンスは、仮想マシンのサイズやデータ ディスクの構成などのさまざまな要素に左右されます。

[Azure portal でプロビジョニングされる SQL Server イメージ](quickstart-sql-vm-create-portal.md)は、一般的なストレージ構成のベスト プラクティスに従います (ストレージの構成方法の詳細については、「[SQL Server VM のストレージの構成](virtual-machines-windows-sql-server-storage-configuration.md)」を参照してください)。 プロビジョニングした後で、この記事で説明するその他の最適化を適用することを検討します。 ワークロードに関する選択を基準に、テストを通して検証します。

> [!TIP]
> 通常、コストの最適化とパフォーマンスの最適化はトレードオフの関係になっています。 この記事は、Azure VM で SQL Server の *最適な* パフォーマンスを得ることに重点を置いています。 ワークロードの要求が厳しくない場合は、以下に示す最適化がすべて必要になるわけではありません。 各推奨事項を評価するときに、パフォーマンスのニーズ、コスト、およびワークロードのパターンを考慮してください。

## <a name="quick-check-list"></a>クイック チェック リスト

Azure Virtual Machines で SQL Server の最適なパフォーマンスを実現するためのクイック チェック リストを次に示します。

| 領域 | 最適化 |
| --- | --- |
| [VM サイズ](#vm-size-guidance) | SQL Enterprise Edition: - [DS3_v2](../sizes-general.md) 以上。<br/><br/> SQL Standard Edition および Web Edition: - [DS2_v2](../sizes-general.md) 以上。 |
| [Storage](#storage-guidance) | - [Premium SSD](../disks-types.md) を使用する。 標準ストレージは、開発/テストにのみ使用することをお勧めします。<br/><br/> - [ストレージ アカウント](../../../storage/common/storage-create-storage-account.md)と SQL Server VM を同じリージョンに保持します。<br/><br/> * ストレージ アカウントで [Azure geo 冗長ストレージ](../../../storage/common/storage-redundancy.md) (geo レプリケーション) を無効にします。 |
| [ディスク](#disks-guidance) | - 少なくとも 2 つの [P30 ディスク](../disks-types.md#premium-ssd) (ログ ファイル用に 1 つとデータ ファイル用に 1 つ (TempDB を含む)) を使用します。 最大 50,000 の IOPS を必要とするワークロードには、Ultra SSD の使用を検討してください。 <br/><br/> - データベース ストレージまたはログに、オペレーティング システム ディスクまたは一時ディスクを使用することは避けてください。<br/><br/> - データ ファイルと TempDB データ ファイルをホストするディスクで読み取りキャッシュを有効にします。<br/><br/> - ログ ファイルをホストするディスクでは、キャッシュを有効にしないでください。  **重要**:Azure VM ディスクのキャッシュ設定を変更するときには、SQL Server サービスを停止してください。<br/><br/> - 複数の Azure データ ディスクをストライプして、IO スループットを向上させます。<br/><br/> - ドキュメントに記載されている割り当てサイズでフォーマットします。 <br/><br/> - ミッション クリティカルな SQL Server ワークロードのために、TempDB をローカル SSD に配置します (適切な VM サイズを選択した後に行います)。 |
| [I/O](#io-guidance) |- データベース ページの圧縮を有効にします。<br/><br/> - データ ファイルの瞬時初期化を有効にします。<br/><br/> - データベースの自動拡張を制限します。<br/><br/> - データベースの自動圧縮を無効にします。<br/><br/> - システム データベースも含め、すべてのデータベースをデータ ディスクに移動します。<br/><br/> - SQL Server エラー ログとトレース ファイルのディレクトリをデータ ディスクに移動します。<br/><br/> - 既定のバックアップ ファイルとデータベース ファイルの場所を設定します。<br/><br/> - ロックされたページを有効にします。<br/><br/> - SQL Server パフォーマンス修正プログラムを適用します。 |
| [機能固有](#feature-specific-guidance) | - BLOB ストレージに直接バックアップします。 |

これらの最適化を行う*方法*と*理由*については、以下のセクションに記載されている詳細とガイダンスをご確認ください。

## <a name="vm-size-guidance"></a>VM サイズのガイダンス

パフォーマンス重視のアプリケーションでは、次の[仮想マシン サイズ](../sizes.md)を使用することをお勧めします。

* **SQL Server Enterprise エディション**:DS3_v2 以降
* **SQL Server Standard および Web エディション**:DS2_v2 以降

[DSv2 シリーズ](../sizes-general.md#dsv2-series)の VM では、Premium Storage をサポートしています。最適なパフォーマンスを得るには、これをお勧めします。 ここで推奨されるサイズが基準ですが、選択する実際のマシン サイズはワークロードの必要に応じて異なります。 DSv2 シリーズの VM がさまざまなワークロードに適している汎用 VM であるのに対して、その他のマシン サイズは特定のワークロードの種類用に最適化されています。 たとえば、[M シリーズ](../sizes-memory.md#m-series)では、最大の SQL Server ワークロードに対して最大の vCPU 数とメモリを提供します。 [GS シリーズ](../sizes-memory.md#gs-series)と [DSv2 シリーズ 11-15](../sizes-memory.md#dsv2-series-11-15) は、大量のメモリ要件のために最適化されています。 また、これらのシリーズはどちらも、[制約付きの主要なサイズ](../../windows/constrained-vcpu.md)で利用することができ、低いコンピューティング要求のワークロードの場合は節約できます。 [Ls シリーズ](../sizes-storage.md)のマシンは、高いディスク スループットと IO 用に最適化されています。 特定の SQL Server ワークロードを考慮し、VM シリーズとサイズの選択に適用することが重要です。

## <a name="storage-guidance"></a>ストレージのガイダンス

(DSv2 シリーズおよび GS シリーズと共に) DS シリーズの VM では、[Premium SSD](../disks-types.md) がサポートされています。 すべての運用環境のワークロードに Premium SSD をお勧めします。

> [!WARNING]
> Standard HDD と SSD には、さまざまな待機時間や帯域幅があり、開発/テスト ワークロードにのみ推奨されます。 運用環境のワークロードでは、Premium SSD を使用する必要があります。

さらに、転送遅延を低減するために、SQL Server 仮想マシンと同じデータ センターで Azure ストレージ アカウントを作成することをお勧めします。 ストレージ アカウントの作成時に、geo レプリケーションを無効にします。複数のディスクでの一貫性のある書き込み順序が保証されないためです。 代わりに、2 つの Azure データ センター間で SQL Server ディザスター リカバリー テクノロジを構成することを検討します。 詳細については、「[Azure 仮想マシンにおける SQL Server の高可用性と障害復旧](virtual-machines-windows-sql-high-availability-dr.md)」をご覧ください。

## <a name="disks-guidance"></a>ディスクのガイダンス

Azure VM には、次の 3 種類のメイン ディスクがあります。

* **OS ディスク**:Azure 仮想マシンを作成すると、プラットフォームによって、オペレーティング システム ディスク用に少なくとも 1 つのディスク (**C** ドライブとしてラベル付けされる) が VM に接続されます。 このディスクは、ページ BLOB としてストレージに格納されている VHD です。
* **一時ディスク**:Azure Virtual Machines には、一時ディスクと呼ばれる別のディスク (**D**: ドライブとしてラベル付けされる) が含まれています。 これは、スクラッチ領域に使用できるノード上のディスクです。
* **データ ディスク**:追加のディスクをデータ ディスクとして仮想マシンに接続することもできます。これらのディスクは、ページ BLOB としてストレージに格納されます。

次のセクションでは、これらの異なるディスクの使用に関する推奨事項について説明します。

### <a name="operating-system-disk"></a>オペレーティング システム ディスク

オペレーティング システム ディスクは、実行中のバージョンのオペレーティング システムとして起動およびマウントできる VHD であり、 **C** ドライブとしてラベル付けされます。

オペレーティング システム ディスクの既定のキャッシュ ポリシーは、 **読み取り/書き込み**です。 パフォーマンス重視のアプリケーションでは、オペレーティング システム ディスクではなく、データ ディスクを使用することをお勧めします。 下記のデータ ディスクに関するセクションをご覧ください。

### <a name="temporary-disk"></a>一時ディスク

**D**: ドライブとしてラベル付けされる一時ストレージ ドライブは、Azure BLOB ストレージに保持されません。 ユーザー データベース ファイルやユーザー トランザクション ログ ファイルを **D**: ドライブに保存しないでください。

D シリーズ、Dv2 シリーズ、および G シリーズの VM では、これらの VM 上の一時ドライブは SSD ベースです。 一時オブジェクトや複雑な結合などのワークロードで TempDB が多用される場合、TempDB を **D** ドライブに格納すると、TempDB のスループットが向上し、TempDB の遅延時間が短縮される可能性があります。 サンプル シナリオについては、TempDB の説明が記載されているブログ記事「[Storage Configuration Guidelines for SQL Server on Azure VM (Azure VM の SQL Server 用ストレージ構成ガイドライン)](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm)」を参照してください。

Premium SSD がサポートされる VM (DS シリーズ、DSv2 シリーズ、および GS シリーズ) の場合は、Premium SSD がサポートされていて、読み取りキャッシングが有効なディスクに、TempDB を格納することをお勧めします。

この推奨事項には例外が 1 つあります。_TempDB の使用が書き込み重視である場合は、TempDB をローカル **D** ドライブに格納することで、パフォーマンスを向上させることができます。これも、マシン サイズに基づく SSD ベースです。_

### <a name="data-disks"></a>データ ディスク

* **データ ファイルとログ ファイル用のデータ ディスクの使用**:ディスク ストライピングを使用していない場合は、2 つの Premium SSD P30 ディスクを使用し、一方のディスクにログ ファイル、もう一方にデータと TempDB ファイルを格納します。 [ディスクの種類の選択](../disks-types.md)に関する記事で説明されているように、各 Premium SSD ディスクでは、サイズに応じた IOPS 数と帯域幅 (MB/秒) が提供されます。 記憶域スペースなどのディスク ストライピング技法を使用している場合は、最適なパフォーマンスを実現するには、2 つのプール (ログ ファイル用に 1 つとデータ ファイル用に 1 つ) を使用します。 ただし、SQL Server フェールオーバー クラスター インスタンス (FCI) を使用する場合は、1 つのプールを構成する必要があります。

   > [!TIP]
   > - さまざまなディスクとワークロードの構成に関するテスト結果については、ブログ記事「[Storage Configuration Guidelines for SQL Server on Azure VM (Azure VM の SQL Server 用ストレージ構成ガイドライン)](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm/)」を参照してください。
   > - 最大 50,000 の IOPS を必要とする SQL Server のミッション クリティカルなパフォーマンスには、10 -P30 ディスクを Ultra SSD と交換することを検討してください。 詳細については、ブログ記事「[Mission critical performance with Ultra SSD (Ultra SSD によるミッション クリティカルなパフォーマンス)](https://azure.microsoft.com/blog/mission-critical-performance-with-ultra-ssd-for-sql-server-on-azure-vm/)」を参照してください。

   > [!NOTE]
   > SQL Server VM をポータルからプロビジョニングするとき、必要に応じてストレージの構成を編集することができます。 Azure では、実際の構成に応じて 1 つまたは複数のディスクが構成されます。 複数のディスクは、ストライピングを使用して 1 つの記憶域プールにまとめられます。 この構成では、データ ファイルとログ ファイルが一緒に格納されます。 詳細については、「[SQL Server VM のストレージの構成](virtual-machines-windows-sql-server-storage-configuration.md)」を参照してください。

* **ディスク ストライピング**:スループットを向上させるために、データ ディスクをさらに追加し、ディスク ストライピングを使用することができます。 データ ディスク数を決定するには、ログ ファイルおよびデータと TempDB ファイルのために必要な IOPS 数と帯域幅を分析する必要があります。 VM サイズが異なると、サポートされる IOPS 数と帯域幅の制限も変わります。[VM サイズ](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)ごとの IOPS ついて表を参照してください。 次のガイドラインに従ってください。

  * Windows 8/Windows Server 2012 以降の場合は､次のガイドラインに従った[記憶域スペース](https://technet.microsoft.com/library/hh831739.aspx)を使用します｡

      1. パーティションの配置不良を原因とするパフォーマンスの低下を回避するため､インターリーブ (ストライプ サイズ) としては OLTP ワークロードに対しては 64 KB (65536 バイト)、データ ウェアハウス ワークロードに対しては 256 KB (262144 バイト) を設定します｡ これは､PowerShell を使って設定する必要があります｡
      2. カラム数 = 物理ディスク数を設定します｡ 8 つを超えるディスクを構成する場合は PowerShell を使用します (Server Manager の UI ではない)｡ 

    たとえば次の PowerShell では､インターリーブサイズが 64 KB､カラム数が 2 の記憶域プールが新規作成されます｡

    ```powershell
    $PoolCount = Get-PhysicalDisk -CanPool $True
    $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}

    New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" -PhysicalDisks $PhysicalDisks | New-VirtualDisk -FriendlyName "DataFiles" -Interleave 65536 -NumberOfColumns 2 -ResiliencySettingName simple –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" -AllocationUnitSize 65536 -Confirm:$false 
    ```

  * Windows 2008 R2 以前では、ダイナミック ディスク (OS ストライプ ボリューム) を使用できます。ストライプ サイズは常に 64 KB です。 Windows 8 および Windows Server 2012 の時点で、このオプションは非推奨となっています。 詳細については、[Windows Storage Management API に移行しつつある仮想ディスク サービス](https://msdn.microsoft.com/library/windows/desktop/hh848071.aspx)に関するページでサポートに関する声明をご覧ください。

  * [記憶域スペース ダイレクト (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm) を [SQL Server フェールオーバー クラスター インスタンス](virtual-machines-windows-portal-sql-create-failover-cluster.md)で使用している場合は、単一プールを構成する必要があります。 その単一プール上にはさまざまなボリュームを作成できますが、それらのボリュームはすべて、同一のキャッシング ポリシーのような同じ特性を共有することになります。

  * 負荷予測に基づいて、ご使用の記憶域プールに関連付けるディスク数を決定します。 接続できるデータ ディスクの数は VM サイズによって異なることに注意してください。 詳細については、[仮想マシンのサイズ](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関するページをご覧ください。

  * Premium SSD (開発/テスト シナリオ) を使用しない場合は、ご使用の [VM サイズ](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)でサポートされる最大数のデータ ディスクを追加し、ディスク ストライピングを使用することをお勧めします。

* **キャッシュ ポリシー**:ストレージ構成に応じたキャッシュ ポリシーに対する次の推奨事項に注意してください。

  * データとログ ファイル用に別個のディスクを使用する場合は、データ ファイルと TempDB データ ファイルをホストするデータ ディスクで読み取りキャッシュを有効にします。 これにより、パフォーマンスが大幅に向上する可能性があります。 ログ ファイルを保持するディスクではキャッシュを有効にしないでください。これを行うと、パフォーマンスが多少低下します。

  * 単一ストレージ プールでディスク ストライピングを使用すると、ほとんどのワークロードで読み取りキャッシュのメリットが得られます。 ログ ファイルとデータ ファイルで異なるストレージ プールを使用している場合は、データ ファイル用のストレージ プールでのみ読み取りキャッシュを有効にします。 書き込みが多いワークロードでは、キャッシュなしのほうがパフォーマンスが向上する場合があります。 これは、テストを実行することでのみ判断できます。

  * これらの推奨事項は、Premium SSD ディスクに適用されます。 Premium SSD を使用していない場合は、どのデータ ディスクでもキャッシュを有効にしないでください。

  * ディスク キャッシュの構成手順については、以下の記事をご覧ください。 クラシック (ASM) デプロイ モデルの場合:[Set-AzureOSDisk](https://msdn.microsoft.com/library/azure/jj152847) と [Set-AzureDataDisk](https://msdn.microsoft.com/library/azure/jj152851.aspx) に関するページを参照してください。 Azure Resource Manager デプロイ モデルの場合:[Set-AzOSDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk) と [Set-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmdatadisk) に関するページを参照してください。

     > [!WARNING]
     > データベースの破損の可能性を回避するために、Azure VM ディスクのキャッシュ設定を変更するときには、SQL Server サービスを停止してください。

* **NTFS アロケーション ユニット サイズ**:データ ディスクをフォーマットするときは、データ ファイルとログ ファイルに加えて TempDB にも 64 KB アロケーション ユニット サイズを使用することをお勧めします。

* **ディスク管理のベスト プラクティス**:データ ディスクの削除またはキャッシュの種類の変更を行う場合、変更中は SQL Server サービスを停止します。 OS ディスクでキャッシュ設定が変更されると、Azure は VM を停止し、キャッシュの種類を変更して、VM を再起動します。 データ ディスクのキャッシュ設定が変更されても VM は停止されませんが、変更中、データ ディスクが VM から切断され、その後再接続されます。

  > [!WARNING]
  > これらの操作中に、SQL Server サービスの停止を怠ると、データベースが破損するおそれがあります。


## <a name="io-guidance"></a>I/O のガイダンス

* Premium SSD では、アプリケーションと要求の並列処理を実行するときに最良の結果が得られます。 Premium SSD は、IO キューの深さが 1 より大きいシナリオ向けに設計されているので、シングル スレッド シリアル要求では (ストレージを集中的に使用する場合でも)、パフォーマンスの向上はほとんどまたはまったくありません。 たとえば、これは、パフォーマンス分析ツール (SQLIO など) のシングル スレッド テストの結果に影響する可能性があります。

* I/O 集中型ワークロードのパフォーマンスを向上させるために、 [データベース ページの圧縮](https://msdn.microsoft.com/library/cc280449.aspx) を使用することを検討します。 ただし、データ圧縮を使用すると、データベース サーバーでの CPU 消費量が増加する場合があります。

* 初期ファイル割り当てに必要な時間を短縮するために、ファイルの瞬時初期化を有効にすることを検討します。 ファイルの瞬時初期化を利用するには、SQL Server (MSSQLSERVER) サービス アカウントに SE_MANAGE_VOLUME_NAME を付与し、**[ボリュームの保守タスクを実行]** セキュリティ ポリシーにそのサービス アカウントを追加します。 Azure の SQL Server プラットフォーム イメージを使用している場合、既定のサービス アカウント (NT Service\MSSQLSERVER) は、**[ボリュームの保守タスクを実行]** セキュリティ ポリシーに追加されません。 つまり、SQL Server Azure プラットフォーム イメージでは、ファイルの瞬時初期化は有効になりません。 **[ボリュームの保守タスクを実行]** セキュリティ ポリシーに SQL Server サービス アカウントを追加したら、SQL Server サービスを再起動します。 この機能を使用する場合、セキュリティに関する考慮事項があります。 詳細については、「 [データベース ファイルの初期化](https://msdn.microsoft.com/library/ms175935.aspx)」をご覧ください。

* **自動拡張** は、予想外の増加に付随するものと見なされています。 自動拡張を使用して、データやログの増加に日常的に対処しないでください。 自動拡張を使用する場合は、Size スイッチを使用してファイルを事前に拡張します。

* パフォーマンスに悪影響を及ぼすおそれのある不要なオーバーヘッドを回避するために、 **自動圧縮** が無効になっていることを確認します。

* システム データベースも含め、すべてのデータベースをデータ ディスクに移動します。 詳細については、「 [システム データベースの移動](https://msdn.microsoft.com/library/ms345408.aspx)」をご覧ください。

* SQL Server エラー ログとトレース ファイルのディレクトリをデータ ディスクに移動します。 これは、SQL Server インスタンスを右クリックしてプロパティを選択することにより、SQL Server 構成マネージャーで実行できます。 エラー ログとトレース ファイルの設定は、 **[起動時のパラメーター]** タブで変更できます。ダンプ ディレクトリは、 **[詳細設定]** タブで指定します。次のスクリーンショットでは、エラー ログの起動時のパラメーターを検索する場所を示します。

    ![SQL ErrorLog のスクリーンショット](./media/virtual-machines-windows-sql-performance/sql_server_error_log_location.png)

* 既定のバックアップ ファイルとデータベース ファイルの場所を設定します。 この記事では、推奨事項を使用し、[サーバーのプロパティ] ウィンドウで変更を行います。 手順については、「 [データ ファイルとログ ファイルの既定の場所の表示または変更 (SQL Server Management Studio)](https://msdn.microsoft.com/library/dd206993.aspx)」をご覧ください。 次のスクリーンショットでは、これらの変更を行う場所を示します。

    ![SQL データのログおよびバックアップ ファイル](./media/virtual-machines-windows-sql-performance/sql_server_default_data_log_backup_locations.png)
* ロックされたページを有効にして、IO とページング アクティビティを減らします。 詳細については、「 [Lock Pages in Memory オプションの有効化 (Windows)](https://msdn.microsoft.com/library/ms190730.aspx)」をご覧ください。

* SQL Server 2012 を実行している場合は、Service Pack 1 Cumulative Update 10 をインストールします。 この更新プログラムには、SQL Server 2012 で一時テーブルに対して SELECT INTO ステートメントを実行したときに I/O のパフォーマンスが低下する問題に対処するための修正プログラムが含まれています。 詳細については、この [サポート技術情報の記事](https://support.microsoft.com/kb/2958012)をご覧ください。

* Azure との間での転送時にデータ ファイルを圧縮することを検討します。

## <a name="feature-specific-guidance"></a>機能固有のガイダンス

一部のデプロイでは、より高度な構成手法を使用することで、パフォーマンスがさらに向上する場合があります。 パフォーマンスの向上を実現する際に役立つ SQL Server の機能を次に示します。

* **Azure Storage へのバックアップ**:Azure 仮想マシンで実行される SQL Server のバックアップを実行する際は、[SQL Server Backup to URL](https://msdn.microsoft.com/library/dn435916.aspx) を使用できます。 SQL Server 2012 SP1 CU2 以降で使用できるこの機能は、接続されているデータ ディスクにバックアップする場合に推奨されます。 Azure Storage との間でバックアップと復元を行うときは、「 [SQL Server Backup to URL に関するベスト プラクティスとトラブルシューティング](https://msdn.microsoft.com/library/jj919149.aspx)」に記載されている推奨事項に従ってください。 [Azure Virtual Machines での SQL Server の自動バックアップ](virtual-machines-windows-sql-automated-backup.md)を使用して、バックアップを自動化することもできます。

    SQL Server 2012 より前のバージョンでは、 [SQL Server Backup to Azure Tool](https://www.microsoft.com/download/details.aspx?id=40740)を使用できます。 このツールでは、複数のバックアップ ストライプ ターゲットを使用することで、バックアップ スループットを向上させることができます。

* **Azure 内の SQL Server データ ファイル**:[Azure 内の SQL Server データ ファイル](https://msdn.microsoft.com/library/dn385720.aspx)は、SQL Server 2014 以降で使用できる新機能です。 Azure 内のデータ ファイルを使用して SQL Server を実行すると、Azure データ ディスクを使用する場合と同等のパフォーマンス特性が得られます。

## <a name="next-steps"></a>次の手順

ストレージとパフォーマンスの詳細については、「[Storage Configuration Guidelines for SQL Server on Azure VM (Azure VM の SQL Server 用ストレージ構成ガイドライン)](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm/)」を参照してください

セキュリティのベスト プラクティスについては、[Azure Virtual Machines における SQL Server のセキュリティに関する考慮事項](virtual-machines-windows-sql-security.md)に関するページをご覧ください。

SQL Server Virtual Machines に関する他の記事については、[Azure Virtual Machines 上の SQL Server の概要](virtual-machines-windows-sql-server-iaas-overview.md)に関するページをご覧ください。 SQL Server の仮想マシンに関するご質問については、[よくあるご質問](virtual-machines-windows-sql-server-iaas-faq.md)に関するページをご覧ください。
