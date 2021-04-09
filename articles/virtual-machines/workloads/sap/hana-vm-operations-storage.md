---
title: SAP HANA Azure 仮想マシンのストレージ構成 | Microsoft Docs
description: SAP HANA がデプロイされている VM のストレージの推奨事項。
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP、Azure HANA、ストレージ Ultra Disk、Premium Storage
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/03/2021
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0c0fbb1280fc2a7eaca1d97e7e016cf480873c8b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101666577"
---
# <a name="sap-hana-azure-virtual-machine-storage-configurations"></a>SAP HANA Azure 仮想マシンのストレージ構成

Azure は、SAP HANA を実行する Azure VM に適した、異なる種類のストレージを提供します。 SAP HANA のデプロイのために検討できる **SAP HANA 認定の Azure ストレージの種類** は次のとおりです。 

- Azure Premium SSD または Premium Storage 
- [Ultra Disk](../../disks-enable-ultra-ssd.md)
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 

これらのディスクの種類の詳細については、「[SAP ワークロードの Azure Storage の種類](./planning-guide-storage.md)」と[ディスクの種類の選択](../../disks-types.md)に関する記事をご覧ください。

Azure では、Azure Standard および Premium Storage の 2 つの VHD デプロイ方法を提供しています。 Azure ブロック ストレージのデプロイには、[Azure マネージド ディスク](https://azure.microsoft.com/services/managed-disks/)のご利用をお勧めします。 

ストレージの種類と、IOPS およびストレージ スループットの SLA の一覧については、[マネージド ディスクに関する Azure ドキュメント](https://azure.microsoft.com/pricing/details/managed-disks/)をご覧ください。

> [!IMPORTANT]
> 選択した Azure ストレージの種類に関係なく、そのストレージで使用されるファイル システムは、特定のオペレーティング システムと DBMS 向けに SAP でサポートされている必要があります。 [SAP サポート ノート #2972496](https://launchpad.support.sap.com/#/notes/2972496) には、SAP HANA を含むさまざまなオペレーティング システムとデータベースでサポートされるファイル システムの一覧が示されています。 これは、任意のタスクの読み取りと書き込みのために SAP HANA がアクセスする可能性があるすべてのボリュームに適用されます。 特に Azure for SAP HANA で NFS を使用する場合は、この記事で後述するように、NFS バージョンに関する追加の制限が適用されます 


各種ストレージにおける SAP HANA 認定の最低条件は次のとおりです。 

- Azure Premium Storage - **/hana/log** は、Azure [書き込みアクセラレータ](../../how-to-enable-write-accelerator.md)でサポートされている必要があります。 **/hana/data** ボリュームは、Azure 書き込みアクセラレータなしで Premium Storage に配置することも、Ultra Disk に配置することもできます。
- **/hana/log** ボリュームには、少なくとも Azure Ultra Disk が必要です。 **/hana/data** ボリュームは、Azure 書き込みアクセラレータなしで Premium Storage に配置できます。また、再起動時間を短縮するために Ultra Disk に配置することもできます。
- **/hana/log および /hana/data** には、Azure NetApp Files 上の **NFS v4.1** ボリュームを使用します。 /hana/shared のボリュームでは、NFS v3 または NFS v4.1 プロトコルを使用できます。

ストレージの種類によっては、組み合わせることができます。 たとえば、 **/hana/data** を Premium Storage に配置し、必要な低遅延を確保するために、 **/hana/log** を Ultra Disk ストレージに配置できます。 **/hana/data** に ANF に基づくボリュームを使用する場合は、 **/hana/log** ボリュームも、ANF 上の NFS に基づく必要があります。 一方のボリューム (/hana/data など) に ANF 上の NFS を使用し、もう一方のボリューム ( **/hana/log** など) に Azure Premium Storage または Ultra Disk を使用することは **サポートされていません**。

オンプレミスでは、I/O サブシステムとその機能を気にする必要はほとんどありませんでした。 SAP HANA に対する最低限のストレージ要件が満たされていることを、アプライアンスのベンダーが保証する必要があったためです。 Azure インフラストラクチャを自身でビルドする場合は、これらの SAP が発行した要件の一部に注意する必要があります。 SAP が推奨している最小スループット特性の一部を次に示します。

- I/O サイズが 1 MB の **/hana/log** での、250 MB/秒の読み取り/書き込み
- I/O サイズが 16 MB および 64 MB の **/hana/data** での、少なくとも 400 MB/秒の読み取りアクティビティ
- I/O サイズが 16 MB および 64 MB の **/hana/data** での、少なくとも 250 MB/秒の書き込みアクティビティ

DBMS システムではストレージの待ち時間が短いことが重要であるため、SAP HANA のようなシステムでもデータをメモリ内に保持します。 通常、ストレージのクリティカル パスは、DBMS システムのトランザクション ログの書き込みの周辺にあります。 しかし、クラッシュ復旧後のセーブポイントの書き込みやメモリ内のデータの読み込みなどの操作も重要である場合があります。 そのため、 **/hana/data** および **/hana/log** ボリュームには、Azure Premium Storage、Ultra Disk、または ANF を利用することが **必須** となります。 


HANA のストレージ構成を選択する際の基本原則は次のとおりです。

- 「[SAP ワークロードの Azure Storage の種類](./planning-guide-storage.md)」および[ディスクの種類の選択](../../disks-types.md)に関する記事に基づいて、ストレージの種類を決定します。
- VM のサイズ設定や決定を行うときは、VM の全体的な I/O スループットと IOPS の上限を考慮します。 VM の全体的なストレージ スループットについては、「[メモリ最適化済み仮想マシンのサイズ](../../sizes-memory.md)」をご覧ください。
- ストレージ構成を決定するときは、 **/hana/data** ボリューム構成で VM の全体的なスループットの上限を超えないようにします。 セーブポイントの書き込みにより、SAP HANA は I/O を積極的に発行できます。 セーブポイントを書き込むと、 **/hana/data** ボリュームのスループットの上限にすぐに達する可能性があります。 **/hana/data** ボリュームが構築されているディスクのスループットが、VM で許可されるスループットよりも高い場合、セーブポイントの書き込みで使用されるスループットが、再実行ログ書き込みのスループット要求の妨げとなる状況に陥る可能性があります。 アプリケーションのスループットに影響を与える可能性のある状況


> [!IMPORTANT]
> ストレージ構成の提案は、最初の方向性を示すことを目的としています。 ワークロードを実行し、ストレージの使用パターンを分析すると、提供されているストレージ帯域幅または IOPS をすべて使用しているわけではないことに気付く場合があります。 その場合は、ストレージのダウンサイジングを検討してください。 逆に、これらの構成で推奨されているストレージ スループットよりも多くのスループットがワークロードに必要な場合もあります。 その結果、より多くの容量、IOPS、またはスループットをデプロイすることが必要となります。 必要なストレージ容量、必要なストレージ待ち時間、必要なストレージ スループットと IOPS、最も安価な構成のバランスの面で、ユーザーと HANA ワークロードにとって適切な妥協点を見つけ、それに合わせて調整できるように、Azure には機能や価格がそれぞれ異なるさまざまなストレージの種類が用意されています。


## <a name="stripe-sets-versus-sap-hana-data-volume-partitioning"></a>ストライプ セットと SAP HANA データ ボリュームのパーティション分割
Azure Premium Storage を使用すると、複数の Azure ディスクにわたって **/hana/data** または **/hana/log** ボリューム、またはその両方をストライピングするときに、最適な価格/パフォーマンス比に達しない場合があります。 より大きなディスク ボリュームをデプロイするほど、必要な IOPS またはスループットが増え、そうしなくて済みます。 これまでは、Linux の一部である LVM と MDADM ボリューム マネージャーを使用することで、これが実現されていました。 ディスクをストライピングする方法は、数十年間使用され、よく知られています。 これらのストライプ ボリュームは、必要になる可能性のある IOPS またはスループットの能力を利用できるようになるためにメリットがありますが、これらのストライプ ボリュームの管理に関しては複雑さが増します。 ボリュームの容量に拡張する必要がある場合は特にそうなります。 少なくとも **/hana/data** に対しては、SAP は複数の Azure ディスクにまたがるストライピングと同じ目標を達成する代替方法を導入しました。 SAP HANA 2.0 SPS03 以降、HANA IndexServer は、さまざまな Azure ディスクに配置されている複数の HANA データ ファイル間で I/O アクティビティをストライピングできます。 この利点は、複数の Azure ディスクにまたがるストライプ ボリュームの作成と管理を行う必要がないことです。 データ ボリュームのパーティション分割の SAP HANA 機能の詳細については、次を参照してください。

- [HANA 管理者ガイド](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.05/en-US/40b2b2a880ec4df7bac16eae3daef756.html?q=hana%20data%20volume%20partitioning)
- [SAP HANA に関するブログ - データ ボリュームのパーティション分割](https://blogs.sap.com/2020/10/07/sap-hana-partitioning-data-volumes/)
- [SAP ノート #2400005](https://launchpad.support.sap.com/#/notes/2400005)
- [SAP ノート #2700123](https://launchpad.support.sap.com/#/notes/2700123)

詳細を読むと、この機能を利用することで、ボリューム マネージャー ベースのストライプ セットの複雑さが解消されることが明らかになります。 また、HANA データ ボリュームのパーティション分割は、Azure Premium Storage のような Azure ブロック ストレージに対して機能するだけでないこともわかります。 この機能は、これらの共有に IOPS またはスループットの制限がある場合に備えて、NFS 共有間でストライピングするためにも使用できます。  


## <a name="linux-io-scheduler-mode"></a>Linux I/O Scheduler モード
Linux には、数種類の I/O スケジューリング モードがあります。 Linux ベンダーおよび SAP が一般的に推奨しているのは、ディスク ボリュームの I/O スケジューラ モードを **mq-deadline** または **kyber** モードから **noop** モード (マルチ キュー以外) または **none** モード (マルチ キューの場合) に再構成することです (SLES saptune プロファイルでまだ行っていない場合)。 詳細については、次を参照してください。 

- [SAP ノート #1984787](https://launchpad.support.sap.com/#/notes/1984787)
- [SAP ノート #2578899](https://launchpad.support.sap.com/#/notes/2578899) 
- [SLES 12 SP4 の noop 設定の問題](https://www.suse.com/support/kb/doc/?id=000019547)

Red Hat では、さまざまな SAP アプリケーションの特定のチューニング プロファイルによって確立されたとおりの設定にします。


## <a name="solutions-with-premium-storage-and-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>Azure M シリーズ仮想マシン用の Azure 書き込みアクセラレータおよび Premium Storage を使用するソリューション
Azure 書き込みアクセラレータは、Azure M シリーズ VM 専用に提供される機能です。 名前が示すように、この機能の目的は、Azure Premium Storage に対する書き込みの I/O 待ち時間を短縮することです。 SAP HANA の場合、書き込みアクセラレータは **/hana/log** ボリュームに対してのみ使用する必要があります。 したがって、 **/hana/data** と **/hana/log** は別々のボリュームであり、Azure 書き込みアクセラレータは **/hana/log** ボリュームのみをサポートします。 

> [!IMPORTANT]
> Azure Premium Storage を使用する場合、 **/hana/log** ボリュームに Azure [書き込みアクセラレータ](../../how-to-enable-write-accelerator.md)を使用することが必須となります。 書き込みアクセラレータは、Premium Storage と、M シリーズおよび Mv2 シリーズの VM でのみ使用できます。 書き込みアクセラレータは、Esv3 や Edsv4 などの他の Azure VM ファミリとの組み合わせでは機能しません。

Azure Premium Disk のキャッシュに関する下記の推奨事項は、次のような SAP HANA の I/O 特性を前提としています。

- HANA データ ファイルに対する読み取りワークロードはほとんどありません。 例外は、HANA インスタンスの再開後や、データが HANA に読み込まれたときの、大きなサイズの I/O です。 データ ファイルに対する大きな読み取り I/O の別のケースとして、HANA データベースのバックアップがあり得ます。 結果として、大部分のケースではすべてのデータ ファイル ボリュームを完全に読み取る必要があるため、読み取りキャッシュはほとんど意味がありません。 
- データ ファイルに対する書き込みは、HANA セーブポイントと HANA クラッシュ復旧に基づいてバーストで発生します。 セーブポイントの書き込みは非同期であり、どのユーザー トランザクションも保持されません。 クラッシュ復旧中のデータの書き込みは、システムの応答性を再び向上させるためにパフォーマンス クリティカルです。 ただし、クラッシュ復旧はどちらかといえば例外的な状況です。
- HANA redo ファイルからの読み取りはほとんどありません。 例外は、トランザクション ログ バックアップやクラッシュ復旧の実行時、または HANA インスタンスの再開フェーズでの大きなサイズの I/O です。  
- SAP HANA redo ログ ファイルに対する主な負荷は書き込みです。 ワークロードの性質に応じて、I/O サイズを 4 KB まで小さくしたり、1 MB 以上の I/O サイズにできます。 SAP HANA redo ログに対する書き込み待機時間はパフォーマンス クリティカルです。
- すべての書き込みを信頼性の高い方法でディスク上に保存する必要があります。

**推奨事項:SAP HANA で確認されたこれらの I/O パターンの結果として、Azure Premium Storage を使用したさまざまなボリュームのキャッシュは、次のように設定する必要があります。**

- **/hana/data** - キャッシュおよび読み取りキャッシュなし
- **/hana/log** - キャッシュなし - M シリーズおよび Mv2 シリーズの VM の場合は例外 (Azure 書き込みアクセラレータを有効にする必要がある) 
- **/hana/shared** - 読み取りキャッシュ
- **OS ディスク** - VM の作成時に Azure によって設定される既定のキャッシュを変更しない


LVM または mdadm を使用して、複数の Azure Premium ディスクにまたがるストライプ セットを構築する場合は、ストライプ サイズを定義する必要があります。 これらのサイズは、 **/hana/data** と **/hana/log** で異なります。 **推奨事項:ストライプ サイズとして、以下を使用することをお勧めします。**

- **/hana/data** の場合は 256 KB
- **/hana/log** の場合は 64 KB

> [!NOTE]
> **/hana/data** のストライプ サイズは、最新の Linux バージョンでのカスタマー エクスペリエンスに基づいて、64 KB または 128 KB を求めていた以前の推奨事項から 256 KB に変更されました。 256 KB のサイズではパフォーマンスが少し向上します。 I/O サイズを大きくして十分なスループットを得るために、 **/hana/log** の推奨ストライプ サイズも 32 KB から 64 KB に変更されました。

> [!NOTE]
> Azure ブロック ストレージでは VHD の 3 つのイメージが保持されるので、RAID ボリュームを使用して冗長レベルを構成する必要はありません。 Azure Premium ディスクでのストライプ セットの使用は、十分な IOPS や I/O スループットを提供するボリュームの構成のみを目的としています。

ストライプ セットの下の Azure VHD の数を増やすと、IOPS とストレージ スループットが累積されます。 そのため、3 つの Azure Premium Storage P30 ディスクでストライプ セットを構成した場合、1 つの Azure Premium Storage P30 ディスクの 3 倍の IOPS と 3 倍のストレージ スループットが得られます。

> [!IMPORTANT]
> LVM または mdadm をボリューム マネージャーとして使用して、複数の Azure Premium ディスクにまたがるストライプ セットを作成する場合は、SAP HANA ファイルシステムの /data、/log、/shared の 3 つを既定またはルート ボリューム グループに配置しないでください。 通常は、/data、/log、および /shared 用に個々のボリューム グループを作成するという Linux ベンダーのガイダンスに従うことを強くお勧めします。


### <a name="azure-burst-functionality-for-premium-storage"></a>Premium Storage の Azure バースト機能
容量が 512 GiB 以下の Azure Premium Storage ディスクの場合、バースト機能が提供されます。 ディスク バーストの動作の詳細については、「[ディスク バースト](../../disk-bursting.md)」をご覧ください。 この記事を読むと、I/O ワークロードがディスクの公称 IOPS およびスループットを下回っているときの、IOPS とスループットの蓄積の概念を理解できます (公称スループットの詳細については、「[Managed Disks の価格」をご覧ください](https://azure.microsoft.com/pricing/details/managed-disks/))。 現在の使用量とディスクの公称値との差分だけ、IOPS とスループットが蓄積されます。 バーストは最大 30 分に制限されています。

このバースト機能を計画できる理想的なケースとして、別の DBMS のデータ ファイルを含むボリュームまたはディスクが考えられます。 特に、小規模から中規模のシステムで、これらのボリュームに対して予想される I/O ワークロードは次のとおりです。

- データはメモリにキャッシュされるか、HANA のように完全にメモリ内で処理されるのが理想的であるため、低から中程度の読み取りワークロード
- 定期的に発行されるデータベースのチェックポイントまたはセーブポイントによってトリガーされる書き込みのバースト
- ストレージ スナップショットを使用してバックアップが実行されない場合に、連続ストリームを読み取るバックアップ ワークロード
- SAP HANA の場合、インスタンスの再起動後のメモリへのデータの読み込み

特に、ワークロードが 1 秒あたり数百個のトランザクションしか処理しない小規模な DBMS システムでは、トランザクション ログまたは再実行ログを格納するディスクまたはボリュームでも、このようなバースト機能は意味があります。 このようなディスクまたはボリュームに対して予想されるワークロードは次のとおりです。

- アプリケーションによって発行されるすべてのコミットによって I/O 操作がトリガーされる可能性が高いため、ワークロードとワークロードの性質に依存するディスクへの定期的な書き込み
- インデックスの作成や再構築などの運用タスクでのスループットの高いワークロード
- トランザクション ログまたは再実行ログのバックアップを実行するときの読み取りバースト


### <a name="production-recommended-storage-solution-based-on-azure-premium-storage"></a>運用環境で推奨される Azure Premium Storage に基づくストレージ ソリューション

> [!IMPORTANT]
> Azure M シリーズ仮想マシンの SAP HANA 認定は、 **/hana/log** ボリュームに Azure 書き込みアクセラレータを使用している場合に限定されます。 そのため、運用環境シナリオでの Azure M シリーズ仮想マシンへの SAP HANA のデプロイは、 **/hana/log** ボリュームに Azure 書き込みアクセラレータを使用して構成することが求められます。  

> [!NOTE]
> Azure Premium Storage を含むシナリオでは、バースト機能が構成に実装されます。 ストレージ テスト ツールを使おうとしているときには、その内容を問わず、[Azure Premium ディスクのバースト動作](../../disk-bursting.md)を考慮に入れてください。 SAP HWCCT または HCMT ツールによって提供されるストレージ テストを実行する場合、一部のテストが、蓄積できるバースト クレジットを超えるため、すべてのテストが基準を満たすことは想定されていません (特に、すべてのテストが中断なく連続して実行される場合)。


> [!NOTE]
> 運用環境シナリオでは、[IAAS に関する SAP ドキュメント](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)で、特定の VM の種類が SAP HANA でサポートされているかどうかを確認してください。

**推奨事項:運用環境のシナリオで推奨される Azure Premium Storage を使用した構成は次のようになります。**

SAP **/hana/data** ボリュームの構成:

| VM の SKU | RAM | 最大 VM I/O<br /> スループット | /hana/data | プロビジョニングされたスループット | 最大バースト スループット | IOPS | バースト IOPS |
| --- | --- | --- | --- | --- | --- | --- | 
| M32ts | 192 GiB | 500 MBps | 4 x P6 | 200 MBps | 680 MBps | 960 | 14,000 |
| M32ls | 256 GiB | 500 MBps | 4 x P6 | 200 MBps | 680 MBps | 960 | 14,000 |
| M64ls | 512 GiB | 1,000 MBps | 4 x P10 | 400 MBps | 680 MBps | 2,000 | 14,000 |
| M64s | 1,000 GiB | 1,000 MBps | 4 x P15 | 500 MBps | 680 MBps | 4,400 | 14,000 |
| M64ms | 1,750 GiB | 1,000 MBps | 4 x P20 | 600 MBps | 680 MBps | 9,200 | 14,000 |  
| M128s | 2,000 GiB | 2,000 MBps | 4 x P20 | 600 MBps | 680 MBps | 9,200| 14,000 | 
| M128ms | 3,800 GiB | 2,000 MBps | 4 x P30 | 800 MBps | バースティングなし | 20,000 | バースティングなし | 
| M208s_v2 | 2,850 GiB | 1,000 MBps | 4 x P30 | 800 MBps | バースティングなし | 20,000| バースティングなし | 
| M208ms_v2 | 5,700 GiB | 1,000 MBps | 4 x P40 | 1,000 MBps | バースティングなし | 30,000 | バースティングなし |
| M416s_v2 | 5,700 GiB | 2,000 MBps | 4 x P40 | 1,000 MBps | バースティングなし | 30,000 | バースティングなし |
| M416ms_v2 | 11,400 GiB | 2,000 MBps | 4 x P50 | 2,000 MBps | バースティングなし | 30,000 | バースティングなし |


**/hana/log** ボリュームの場合、 構成は次のようになります。

| VM の SKU | RAM | 最大 VM I/O<br /> スループット | **/hana/log** ボリューム | プロビジョニングされたスループット | 最大バースト スループット | IOPS | バースト IOPS |
| --- | --- | --- | --- | --- | --- | --- | 
| M32ts | 192 GiB | 500 MBps | 3 x P10 | 300 MBps | 510 MBps | 1,500 | 10,500 | 
| M32ls | 256 GiB | 500 MBps | 3 x P10 | 300 MBps | 510 MBps | 1,500 | 10,500 | 
| M64ls | 512 GiB | 1,000 MBps | 3 x P10 | 300 MBps | 510 MBps | 1,500 | 10,500 | 
| M64s | 1,000 GiB | 1,000 MBps | 3 x P15 | 375 MBps | 510 MBps | 3,300 | 10,500 | 
| M64ms | 1,750 GiB | 1,000 MBps | 3 x P15 | 375 MBps | 510 MBps | 3,300 | 10,500 |  
| M128s | 2,000 GiB | 2,000 MBps | 3 x P15 | 375 MBps | 510 MBps | 3,300 | 10,500|  
| M128ms | 3,800 GiB | 2,000 MBps | 3 x P15 | 375 MBps | 510 MBps | 3,300 | 10,500 | 
| M208s_v2 | 2,850 GiB | 1,000 MBps | 3 x P15 | 375 MBps | 510 MBps | 3,300 | 10,500 |  
| M208ms_v2 | 5,700 GiB | 1,000 MBps | 3 x P15 | 375 MBps | 510 MBps | 3,300 | 10,500 |  
| M416s_v2 | 5,700 GiB | 2,000 MBps | 3 x P15 | 375 MBps | 510 MBps | 3,300 | 10,500 |  
| M416ms_v2 | 11,400 GiB | 2,000 MBps | 3 x P15 | 375 MBps | 510 MBps | 3,300 | 10,500 | 


その他のボリュームの場合、構成は次のようになります。

| VM の SKU | RAM | 最大 VM I/O<br /> スループット | /hana/shared | /root ボリューム | /usr/sap |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 GiB | 500 MBps | 1 x P15 | 1 x P6 | 1 x P6 |
| M32ls | 256 GiB | 500 MBps |  1 x P15 | 1 x P6 | 1 x P6 |
| M64ls | 512 GiB | 1000 MBps | 1 x P20 | 1 x P6 | 1 x P6 |
| M64s | 1,000 GiB | 1,000 MBps | 1 x P30 | 1 x P6 | 1 x P6 |
| M64ms | 1,750 GiB | 1,000 MBps | 1 x P30 | 1 x P6 | 1 x P6 | 
| M128s | 2,000 GiB | 2,000 MBps | 1 x P30 | 1 x P10 | 1 x P6 | 
| M128ms | 3,800 GiB | 2,000 MBps | 1 x P30 | 1 x P10 | 1 x P6 |
| M208s_v2 | 2,850 GiB | 1,000 MBps |  1 x P30 | 1 x P10 | 1 x P6 |
| M208ms_v2 | 5,700 GiB | 1,000 MBps | 1 x P30 | 1 x P10 | 1 x P6 | 
| M416s_v2 | 5,700 GiB | 2,000 MBps |  1 x P30 | 1 x P10 | 1 x P6 | 
| M416ms_v2 | 11,400 GiB | 2,000 MBps | 1 x P30 | 1 x P10 | 1 x P6 | 


推奨されるさまざまなボリュームのストレージ スループットが、実行するワークロードに対応できるかどうかを確認します。 ワークロードに **/hana/data** および **/hana/log** のより大きなボリュームが必要な場合は、Azure Premium Storage VHD の数を増やす必要があります。 記載されている数よりも多くの VHD でボリュームのサイズを設定すると、その Azure 仮想マシンの種類の制限内で IOPS と I/O スループットが向上します。

Azure 書き込みアクセラレータは、[Azure マネージド ディスク](https://azure.microsoft.com/services/managed-disks/)との連携でのみ動作します。 そのため、少なくとも、 **/hana/log** ボリュームを形成する Azure Premium Storage ディスクはマネージド ディスクとしてデプロイする必要があります。 Azure 書き込みアクセラレータの詳細と制限事項については、[書き込みアクセラレータ](../../how-to-enable-write-accelerator.md)に関する記事をご覧ください。

Azure [Esv3](../../ev3-esv3-series.md?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#esv3-series) ファミリおよび [Edsv4](../../edv4-edsv4-series.md?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#edsv4-series) の HANA 認定 VM の場合、 **/hana/data** および **/hana/log** ボリュームに ANF を使用する必要があります。 または、 **/hana/log** ボリュームにのみ、Azure Premium Storage ではなく Azure Ultra Disk Storage を利用する必要があります。 その結果、Azure Premium Storage 上の **/hana/data** ボリュームの構成は次のようになります。

| VM の SKU | RAM | 最大 VM I/O<br /> スループット | /hana/data | プロビジョニングされたスループット | 最大バースト スループット | IOPS | バースト IOPS |
| --- | --- | --- | --- | --- | --- | --- |
| E20ds_v4 | 160 GiB | 480 MBps | 3 x P10 | 300 MBps | 510 MBps | 1,500 | 10,500 |
| E32ds_v4 | 256 GiB | 768 Mbps | 3 x P10 |  300 MBps | 510 MBps | 1,500 | 10,500|
| E48ds_v4 | 384 GiB | 1,152 MBps | 3 x P15 |  375 MBps |510 MBps | 3,300  | 10,500 | 
| E64ds_v4 | 504 GiB | 1,200 MBps | 3 x P15 |  375 MBps | 510 MBps | 3,300 | 10,500 | 
| E64s_v3 | 432 GiB | 1,200 MB/秒 | 3 x P15 |  375 MBps | 510 MBps | 3,300 | 10,500 | 

Ultra Disk 上の **/hana/log** など、その他のボリュームの場合、構成は次のようになります。

| VM の SKU | RAM | 最大 VM I/O<br /> スループット | /hana/log volume | /hana/log I/O throughput | /hana/log IOPS | /hana/shared | /root ボリューム | /usr/sap |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| E20ds_v4 | 160 GiB | 480 MBps | 80 GB | 250 MBps | 1,800 | 1 x P15 | 1 x P6 | 1 x P6 |
| E32ds_v4 | 256 GiB | 768 Mbps | 128 GB | 250 MBps | 1,800 | 1 x P15 | 1 x P6 | 1 x P6 |
| E48ds_v4 | 384 GiB | 1,152 MBps | 192 GB | 250 MBps | 1,800 | 1 x P20 | 1 x P6 | 1 x P6 |
| E64ds_v4 | 504 GiB | 1,200 MBps | 256 GB | 250 MBps | 1,800 | 1 x P20 | 1 x P6 | 1 x P6 |
| E64s_v3 | 432 GiB | 1,200 MBps | 220 GB | 250 MBps | 1,800 | 1 x P20 | 1 x P6 | 1 x P6 |


## <a name="azure-ultra-disk-storage-configuration-for-sap-hana"></a>SAP HANA 用の Azure Ultra Disk ストレージ構成
もう 1 つの Azure Storage の種類は、[Azure Ultra Disk](../../disks-types.md#ultra-disk) と呼ばれます。 これまでに提供されていた Azure Storage と Ultra Disk との大きな違いは、ディスク機能がディスク サイズにバインドされなくなることです。 顧客として、Ultra Disk に次の機能を定義できます。

- 4 GiB から 65,536 GiB のディスク範囲のサイズ
- 100 IOPS から 160K IOPS の IOPS の範囲 (最大値は VM の種類によっても異なる)
- 300 MB/秒から 2,000 MB/秒のストレージのスループット

Ultra Disk によって、自分のサイズ、IOPS、ディスク スループットの範囲を実行する 1 つのディスクを定義することができます。 Azure Premium Storage 上の LVM や MDADM などの論理ボリューム マネージャーを使用する代わりに、IOPS とストレージ スループットの要件を満たすボリュームを構築します。 Ultra Disk と Premium Storage の間で構成の組み合わせを実行できます。 その場合、Ultra Disk の使用をパフォーマンス クリティカルな /**hana/data** および **/hana/log** ボリュームに限定し、その他のボリュームには Azure Premium Storage で対応できます。

Ultra Disk のその他の利点は、Premium Storage と比較して読み取り待ち時間が短いことです。 読み取り待機時間が短いということは、HANA の起動時間とその後のメモリへのデータの読み込みを短縮する必要がある場合に利点があります。 Ultra Disk ストレージの利点は、HANA によってセーブポイントが書き込まれているときにも感じることができます。 

> [!NOTE]
> Ultra Disk は、まだ、すべての Azure リージョンには存在しておらず、すべての種類の VM がサポートされているわけでもありません。 使用可能な Ultra Disk、サポートされている VM ファミリの詳細については、「[Azure で利用できるディスクの種類](../../disks-types.md#ultra-disk)」を参照してください。

### <a name="production-recommended-storage-solution-with-pure-ultra-disk-configuration"></a>純粋な Ultra Disk 構成を使用した運用環境で推奨されるストレージ ソリューション
この構成では、 **/hana/data** ボリュームと **/hana/log** ボリュームを別々に保持します。 推奨値は、[SAP TDI ストレージに関するホワイトペーパー](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) で推奨されているように SAP HANA およびストレージ構成に対して VM の種類を認定するために SAP が用意している KPI から導出されます。

推奨事項は、この記事で既に説明したように、SAP の最小要件を超えていることがよくあります。 一覧表示される推奨事項は、SAP による推奨サイズと、さまざまな種類の VM で提供される最大ストレージ スループットの間の妥協点です。

> [!NOTE]
> Azure Ultra Disk は、ディスク容量 1 ギガバイトあたり 2 IOPS 以上を適用しています。


| VM の SKU | RAM | 最大 VM I/O<br /> スループット | /hana/data volume | /hana/data I/O throughput | /hana/data IOPS | /hana/log volume | /hana/log I/O throughput | /hana/log IOPS |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| E20ds_v4 | 160 GiB | 480 MB/秒 | 200 GB | 400 MBps | 2,500 | 80 GB | 250 MB | 1,800 |
| E32ds_v4 | 256 GiB | 768 MB/秒 | 300 GB | 400 MBps | 2,500 | 128 GB | 250 MBps | 1,800 |
| E48ds_v4 | 384 GiB | 1152 MB/秒 | 460 GB | 400 MBps | 3,000 | 192 GB | 250 MBps | 1,800 |
| E64ds_v4 | 504 GiB | 1200 MB/秒 | 610 GB | 400 MBps | 3,500 |  256 GB | 250 MBps | 1,800 |
| E64s_v3 | 432 GiB | 1,200 MB/秒 | 610 GB | 400 MBps | 3,500 | 220 GB | 250 MB | 1,800 |
| M32ts | 192 GiB | 500 MB/秒 | 250 GB | 400 MBps | 2,500 | 96 GB | 250 MBps  | 1,800 |
| M32ls | 256 GiB | 500 MB/秒 | 300 GB | 400 MBps | 2,500 | 256 GB | 250 MBps  | 1,800 |
| M64ls | 512 GiB | 1,000 MB/秒 | 620 GB | 400 MBps | 3,500 | 256 GB | 250 MBps  | 1,800 |
| M64s | 1,000 GiB | 1,000 MB/秒 |  1,200 GB | 600 MBps | 5,000 | 512 GB | 250 MBps  | 2,500 |
| M64ms | 1,750 GiB | 1,000 MB/秒 | 2,100 GB | 600 MBps | 5,000 | 512 GB | 250 MBps  | 2,500 |
| M128s | 2,000 GiB | 2,000 MB/秒 |2,400 GB | 750 MBps | 7,000 | 512 GB | 250 MBps  | 2,500 | 
| M128ms | 3,800 GiB | 2,000 MB/秒 | 4,800 GB | 750 MBps |9,600 | 512 GB | 250 MBps  | 2,500 | 
| M208s_v2 | 2,850 GiB | 1,000 MB/秒 | 3,500 GB | 750 MBps | 7,000 | 512 GB | 250 MBps  | 2,500 | 
| M208ms_v2 | 5,700 GiB | 1,000 MB/秒 | 7,200 GB | 750 MBps | 14,400 | 512 GB | 250 MBps  | 2,500 | 
| M416s_v2 | 5,700 GiB | 2,000 MB/秒 | 7,200 GB | 1,000 MBps | 14,400 | 512 GB | 400 MBps  | 4,000 | 
| M416ms_v2 | 11,400 GiB | 2,000 MB/秒 | 14,400 GB | 1,500 MBps | 28,800 | 512 GB | 400 MBps  | 4,000 |   

**一覧の値は出発点として使用することを目的としており、実際の要求に照らして評価する必要があります。** Azure Ultra Disk を使用する利点は、VM または停止しているシステムに適用されたワークロードをシャットダウンする必要なく、IOPS とスループットの値を適用できることです。   

> [!NOTE]
> これまでのところ、Ultra Disk ストレージでのストレージ スナップショットは利用できません。 これにより、Azure Backup Services での VM スナップショットの使用はブロックされます。


## <a name="nfs-v41-volumes-on-azure-netapp-files"></a>Azure NetApp Files 上の NFS v4.1 ボリューム
HANA 用 ANF の詳細については、「[SAP HANA 用 Azure NetApp Files 上の NFS v4.1 ボリューム](./hana-vm-operations-netapp.md)」を参照してください。




## <a name="cost-conscious-solution-with-azure-premium-storage"></a>Azure Premium Storage を使用する、コストを意識したソリューション
これまでこのドキュメントの「[Azure M シリーズ仮想マシン用の Azure 書き込みアクセラレータおよび Premium Storage を使用するソリューション](#solutions-with-premium-storage-and-azure-write-accelerator-for-azure-m-series-virtual-machines)」セクションで説明した Azure Premium Storage ソリューションは、SAP HANA の運用がサポートされるシナリオに向けられていました。 運用のサポートが可能な構成の特性の 1 つは、SAP HANA データと再実行ログのボリュームを 2 つの異なるボリュームに分離することです。 このような分離の理由として、これらのボリュームでのワークロード特性が異なっていることが挙げられます。 また、推奨される運用構成では、さまざまな種類のキャッシュ、または異なる種類の Azure ブロック ストレージが必要とされる場合があります。 運用環境以外のシナリオの場合、運用システムでは考慮される考慮事項の一部が、よりローエンドの非運用システムには当てはまらない可能性があります。 結果として、HANA のデータとログ ボリュームを組み合わせることができます。 ただし、運用システムで必要とされている一定のスループットや待機時間の KPI を結局満たせないなど、最終的に何らかの問題が生じることがあります。 このような環境でコストを削減するために考えられる別の方法は、[Azure Standard SSD ストレージ](./planning-guide-storage.md#azure-standard-ssd-storage)を使用することです。 Standard SSD または Standard HDD Azure ストレージを選択すると、「[仮想マシンの SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines)」という記事に記載されているように、シングル VM SLA に影響が出ることにご留意ください。

このような構成に向けた、より低コストの代替構成は次のようになります。


| VM の SKU | RAM | 最大 VM I/O<br /> スループット | /hana/data and /hana/log<br /> LVM または MDADM によるストライピング | /hana/shared | /root ボリューム | /usr/sap | comments |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 112 GiB | 768 MB/秒 | 4 x P6 | 1 x E10 | 1 x E6 | 1 x E6 | 1 ミリ秒未満のストレージ待機時間は達成されません<sup>1</sup> |
| E16v3 | 128 GiB | 384 MB/秒 | 4 x P6 | 1 x E10 | 1 x E6 | 1 x E6 | VM の種類は HANA では未認定です <br /> 1 ミリ秒未満のストレージ待機時間は達成されません<sup>1</sup> |
| M32ts | 192 GiB | 500 MB/秒 | 3 x P10 | 1 x E15 | 1 x E6 | 1 x E6 | データとログの組み合わせボリュームのために書き込みアクセラレータを使用すると、IOPS 量が 5,000 に制限されます<sup>2</sup> |
| E20ds_v4 | 160 GiB | 480 MB/秒 | 4 x P6 | 1 x E15 | 1 x E6 | 1 x E6 | 1 ミリ秒未満のストレージ待機時間は達成されません<sup>1</sup> |
| E32v3 | 256 GiB | 768 MB/秒 | 4 x P10 | 1 x E15 | 1 x E6 | 1 x E6 | VM の種類は HANA では未認定です <br /> 1 ミリ秒未満のストレージ待機時間は達成されません<sup>1</sup> |
| E32ds_v4 | 256 GiB | 768 Mbps | 4 x P10 | 1 x E15 | 1 x E6 | 1 x E6 | 1 ミリ秒未満のストレージ待機時間は達成されません<sup>1</sup> |
| M32ls | 256 GiB | 500 MB/秒 | 4 x P10 | 1 x E15 | 1 x E6 | 1 x E6 | データとログの組み合わせボリュームのために書き込みアクセラレータを使用すると、IOPS 量が 5,000 に制限されます<sup>2</sup> |
| E48ds_v4 | 384 GiB | 1,152 MBps | 6 x P10 | 1 x E20 | 1 x E6 | 1 x E6 | 1 ミリ秒未満のストレージ待機時間は達成されません<sup>1</sup> |
| E64v3 | 432 GiB | 1,200 MB/秒 | 6 x P10 | 1 x E20 | 1 x E6 | 1 x E6 | 1 ミリ秒未満のストレージ待機時間は達成されません<sup>1</sup> |
| E64ds_v4 | 504 GiB | 1200 MB/秒 |  7 x P10 | 1 x E20 | 1 x E6 | 1 x E6 | 1 ミリ秒未満のストレージ待機時間は達成されません<sup>1</sup> |
| M64ls | 512 GiB | 1,000 MB/秒 | 7 x P10 | 1 x E20 | 1 x E6 | 1 x E6 | データとログの組み合わせボリュームのために書き込みアクセラレータを使用すると、IOPS 量が 10,000 に制限されます<sup>2</sup> |
| M64s | 1,000 GiB | 1,000 MB/秒 | 7 x P15 | 1 x E30 | 1 x E6 | 1 x E6 | データとログの組み合わせボリュームのために書き込みアクセラレータを使用すると、IOPS 量が 10,000 に制限されます<sup>2</sup> |
| M64ms | 1,750 GiB | 1,000 MB/秒 | 6 x P20 | 1 x E30 | 1 x E6 | 1 x E6 | データとログの組み合わせボリュームのために書き込みアクセラレータを使用すると、IOPS 量が 10,000 に制限されます<sup>2</sup> |
| M128s | 2,000 GiB | 2,000 MB/秒 |6 x P20 | 1 x E30 | 1 x E10 | 1 x E6 | データとログの組み合わせボリュームのために書き込みアクセラレータを使用すると、IOPS 量が 20,000 に制限されます<sup>2</sup> |
| M208s_v2 | 2,850 GiB | 1,000 MB/秒 | 4 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | データとログの組み合わせボリュームのために書き込みアクセラレータを使用すると、IOPS 量が 10,000 に制限されます<sup>2</sup> |
| M128ms | 3,800 GiB | 2,000 MB/秒 | 5 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | データとログの組み合わせボリュームのために書き込みアクセラレータを使用すると、IOPS 量が 20,000 に制限されます<sup>2</sup> |
| M208ms_v2 | 5,700 GiB | 1,000 MB/秒 | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 | データとログの組み合わせボリュームのために書き込みアクセラレータを使用すると、IOPS 量が 10,000 に制限されます<sup>2</sup> |
| M416s_v2 | 5,700 GiB | 2,000 MB/秒 | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 | データとログの組み合わせボリュームのために書き込みアクセラレータを使用すると、IOPS 量が 20,000 に制限されます<sup>2</sup> |
| M416ms_v2 | 11400 GiB | 2,000 MB/秒 | 7 x P40 | 1 x E30 | 1 x E10 | 1 x E6 | データとログの組み合わせボリュームのために書き込みアクセラレータを使用すると、IOPS 量が 20,000 に制限されます<sup>2</sup> |


<sup>1</sup> [Azure 書き込みアクセラレータ](../../how-to-enable-write-accelerator.md)は、Ev4 と Ev4 VM ファミリでは使用できません。 Azure Premium Storage を使用した結果として、I/O 待機時間が 1 ミリ秒未満になることはありません

<sup>2</sup> この VM ファミリでは、[Azure 書き込みアクセラレータ](../../how-to-enable-write-accelerator.md)がサポートされますが、書き込みアクセラレータの IOPS 制限によって、このディスク構成での IOPS 機能が制限される可能性があります

SAP HANA のデータとログのボリュームを組み合わせる場合は、ストライプ ボリュームを構成するディスクで、読み取りキャッシュや読み取り/書き込みキャッシュを有効にしないでください。

一覧に示した VM の種類には、SAP では認定されていないものがあります。そのため、いわゆる [SAP HANA ハードウェア ディレクトリ](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)の一覧には含まれていません。 お客様からのフィードバックによれば、非運用環境の一部のタスクについては、一覧に含まれない種類の VM が正常に使用されました。


## <a name="next-steps"></a>次のステップ
詳細については、次を参照してください。

- [Azure 仮想マシンの SAP HANA 高可用性ガイド](./sap-hana-availability-overview.md)。