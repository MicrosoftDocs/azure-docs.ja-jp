---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: ramankumarlive
ms.service: storage
ms.topic: include
ms.date: 09/24/2018
ms.author: ramankum
ms.custom: include file
ms.openlocfilehash: 125f1a2a041c8c05289c95bd12c10618bfc622a8
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2019
ms.locfileid: "56246996"
---
# <a name="high-performance-premium-storage-and-managed-disks-for-vms"></a>VM 向けの高パフォーマンスの Premium Storage とマネージド ディスク

Azure Premium Storage は、I/O (入力/出力) を集中的に行うワークロードが存在する仮想マシン (VM) 向けに高パフォーマンスで待ち時間の少ないディスク サポートを提供します。 Premium Storage を使用する VM ディスクでは、ソリッドステート ドライブ (SSD) にデータを格納します。 Premium Storage ディスクの速度とパフォーマンスを活用するために、既存の VM ディスクを Premium Storage に移行することができます。

Azure では、VM にいくつかの Premium Storage ディスクを接続することができます。 複数のディスクを使用すると、VM あたり最大 256 TB のストレージをアプリケーションに割り当てることができます。プレビュー サイズを使用する場合、VM あたり最大約 2 PiB のストレージをアプリケーションに割り当てることができます。 Premium Storage を使用した場合、アプリケーションは、VM あたり 80,000 IOPS (秒あたりの I/O 操作数) を達成し、VM あたり最大 2,000 MB/秒のディスク スループットを達成できます。 読み取り操作により、待ち時間は非常に短くなります。

Premium Storage により、Azure では、Dynamics AX、Dynamics CRM、Exchange Server、SAP Business Suite、SharePoint ファームなどの要件の厳しいエンタープライズ アプリケーションを本当の意味でクラウドにリフトアンドシフトできるようになります。 SQL Server、Oracle、MongoDB、MySQL、Redis など、一貫して高いパフォーマンスと短い待ち時間を必要とするアプリケーションではパフォーマンスを集中的に使用するデータベース ワークロードを実行できます。

> [!NOTE]
> アプリケーションで最適なパフォーマンスを実現するには、高い IOPS を必要とする VM ディスクを Premium Storage に移行することをお勧めします。 ディスクが高い IOPS を必要としない場合は、Standard Azure Storage で保持することでコストを制限できます。 Standard Storage では、VM のディスク データは、SSD ではなくハード ディスク ドライブ (HDD) に格納されます。

Azure には、VM 用の Premium Storage ディスクを作成する方法が 2 とおりあります。

* **非管理対象ディスク**

    本来の方法では、非管理対象ディスクを使用します。 非管理対象ディスクでは、VM ディスクに対応する仮想ハード ディスク (VHD) ファイルの格納に使用するストレージ アカウントを管理します。 VHD ファイルは、Azure ストレージ アカウントにページ BLOB として格納されます。 

* **マネージド ディスク**

    [Azure Managed Disks](../articles/virtual-machines/windows/managed-disks-overview.md) を選択すると、Azure は、VM ディスクに使用するストレージ アカウントを管理します。 ディスクの種類 (Premium または Standard) と必要なディスクのサイズを指定します。 Azure により自動的にディスクが作成、管理されます。 ストレージ アカウントのスケーラビリティの制限を超えないように、複数のストレージ アカウントにディスクを配置することを配慮する必要はありません。 これは Azure によって自動的に処理されます。

管理ディスクを選択して、その多くの機能を利用することをお勧めします。

Premium Storage の使用を開始するには、[無料の Azure アカウントを作成してください](https://azure.microsoft.com/pricing/free-trial/)。 

既存の VM を Premium Storage に移行する方法については、[非管理対象ディスクから管理ディスクへの Windows VM の変換](../articles/virtual-machines/windows/convert-unmanaged-to-managed-disks.md)に関する記事または[非管理対象ディスクから管理ディスクへの Linux VM の変換](../articles/virtual-machines/linux/convert-unmanaged-to-managed-disks.md)に関する記事を参照してください。

> [!NOTE]
> Premium Storage は、ほとんどのリージョンで使用できます。 利用可能なリージョンの一覧については、[リージョン別の利用可能な Azure 製品](https://azure.microsoft.com/regions/#services)に関するページの **Disk Storage** の行を参照してください。

## <a name="features"></a>機能

ここでは、Premium Storage の機能のいくつかを紹介します。

* **Premium Storage ディスク**

    Premium Storage では、特定のサイズ シリーズの VM に接続できる VM ディスクがサポートされています。 Premium Storage では、さまざまな Azure VM をサポートします。 8 つの GA ディスク サイズの選択肢があります:P4 (32 GiB)、P6 (64 GiB)、P10 (128 GiB)、P15 (256 GiB)、P20 (512 GiB)、P30 (1,024 GiB)、P40 (2,048 GiB)、P50 (4,095 GiB)。 プレビュー段階の 3 つのディスク サイズもあります:P60 8,192 GiB (8 TiB)、P70 16,348 GiB (16 TiB)、P80 32,767 GiB (32 TiB)。 P4、P6、P15、P60、P70、P80 ディスク サイズは、現在、Managed Disks のみでサポートされています。 各ディスク サイズは、それぞれ独自のパフォーマンス仕様があります。 アプリケーションの要件に応じて、VM には 1 つ以上のディスクを接続できます。 仕様の詳細については、[Premium Storage のスケーラビリティとパフォーマンスのターゲット](#scalability-and-performance-targets)に関するセクションで説明します。

* **Premium ページ BLOB**

    Premium Storage では、ページ BLOB がサポートされています。 Premium Storage に VM 用の永続的な非管理対象ディスクを保存するには、ページ BLOB を使用します。 Standard Azure Storage とは異なり、Premium Storage では、ブロック BLOB、追加 BLOB、ファイル、テーブル、またはキューがサポートされていません。 Premium ページ BLOB では、P10 から P50、および P60 (8,191 GiB) の 6 つのサイズがサポートされています。 P60 Premium ページ BLOB を VM ディスクとしてアタッチすることは、サポートされていません。 

    Premium Storage アカウントに配置されたオブジェクトはページ BLOB になります。 ページ BLOB は、サポートされているプロビジョニング済みのサイズのいずれかにスナップされます。 このため、Premium Storage アカウントは小さな BLOB の格納に使用するためのものではありません。

* **Premium Storage アカウント**

    Premium Storage の使用を開始するには、非管理対象ディスクの Premium Storage アカウントを作成します。 [Azure Portal](https://portal.azure.com) で、Premium Storage アカウントを作成するには、**[Premium]** パフォーマンス レベルを選択します。 **[ローカル冗長ストレージ (LRS)]** レプリケーション オプションを選択します。 また、パフォーマンス レベルを **Premium_LRS** に設定することで、Premium Storage アカウントを作成することもできます。 パフォーマンス レベルを変更するには、次の方法のいずれかを使用します。
     
    - [Azure Storage 用の PowerShell](../articles/storage/common/storage-powershell-guide-full.md#manage-the-storage-account)
    - [Azure Storage 用の Azure CLI](../articles/storage/common/storage-azure-cli.md#manage-storage-accounts)
    - [Azure Storage Resource Provider REST API](https://docs.microsoft.com/rest/api/storagerp) (Azure Resource Manager デプロイメント向け)、または Azure Storage リソース プロバイダーのクライアント ライブラリのいずれか

    Premium Storage アカウントの制限については、Premium Storage のスケーラビリティとパフォーマンスのターゲットに関するセクションを参照してください。

* **Premium ローカル冗長ストレージ**

    Premium Storage アカウントでは、レプリケーション オプションとしてローカル冗長ストレージのみがサポートされています。 ローカル冗長ストレージでは、1 つのリージョン内にデータのコピーを 3 つ保持します。 リージョンのディザスター リカバリーでは、[Azure Backup](../articles/backup/backup-introduction-to-azure-backup.md) を使用して VM ディスクを別のリージョンにバックアップする必要があります。 また、バックアップ コンテナーとして geo 冗長ストレージ (GRS) アカウントを使用する必要もあります。 

    Azure では、ストレージ アカウントを非管理対象ディスクのコンテナーとして使用します。 非管理対象ディスクがある Premium Storage をサポートする Azure VM を作成し、Premium Storage アカウントを選択すると、オペレーティング システム ディスクとデータ ディスクがそのストレージ アカウントに格納されます。

## <a name="supported-vms"></a>サポート対象の VM

Premium Storage は、さまざまな Azure VM によってサポートされています。 これらの VM の種類では、Standard Storage ディスクと Premium Storage ディスクを使用できます。 Premium Storage に互換性のない VM シリーズで Premium Storage ディスクを使用することはできません。


Windows 向けの Azure の VM の種類とサイズについては、[Windows VM のサイズ](../articles/virtual-machines/windows/sizes.md)に関する記事を参照してください。 Linux 向けの Azure の VM の種類とサイズについては、[Linux VM のサイズ](../articles/virtual-machines/linux/sizes.md)に関する記事を参照してください。

Premium Storage に対応した VM では、次に示すいくつかの機能がサポートされています。

* **可用性セット**

    DS シリーズの VM の例を使用して、DS シリーズの VM のみが存在するクラウド サービスに DS シリーズの VM を追加できます。 DS シリーズ以外の種類の VM が存在する既存のクラウド サービスに DS シリーズの VM を追加しないでください。 既存の VHD は、DS シリーズの VM のみを実行する新しいクラウド サービスに移行できます。 DS シリーズの VM をホストする新しいクラウド サービスに同じ仮想 IP アドレスを使用する場合は、[予約済み IP アドレス](../articles/virtual-network/virtual-networks-instance-level-public-ip.md)を使用します。

* **オペレーティング システム ディスク**

    Premium または Standard オペレーティング システム ディスクを使用するように Premium Storage VM をセットアップできます。 最適なエクスペリエンスを実現するために、Premium Storage ベースのオペレーティング システム ディスクを使用することをお勧めします。

* **データ ディスク**

    同じ Premium Storage VM で Premium ディスクと Standard ディスクを使用できます。 Premium Storage を使用すると、VM をプロビジョニングし、複数の永続データ ディスクを VM に接続できます。 必要に応じて、ボリュームの容量を増やしてパフォーマンスを高めるには、ディスク全体をストライピングすることができます。

    > [!NOTE]
    > [記憶域スペース](https://technet.microsoft.com/library/hh831739.aspx)を使用して Premium Storage データ ディスクをストライピングする場合は、使用するディスクごとに 1 つの列を使用して記憶域スペースを設定します。 そうしないと、ディスク間でのトラフィックの分散が不均等になるため、ストライプ ボリュームの全体的なパフォーマンスが予想よりも低下する可能性があります。 既定では、サーバー マネージャーで、最大 8 個のディスクの列を設定することができます。 8 個を超えるディスクがある場合は、PowerShell を使用してボリュームを作成します。 列の数を手動で指定します。 そうしないと、サーバー マネージャー UI では、それ以上のディスクがある場合でも引き続き 8 つの列を使用します。 たとえば、1 つのストライプ セット内に 32 個のディスクがある場合は、32 列を指定します。 仮想ディスクで使用する列の数を指定するには、PowerShell の [New-VirtualDisk](https://technet.microsoft.com/library/hh848643.aspx) コマンドレットで *NumberOfColumns* パラメーターを使用します。 詳細については、「[記憶域スペースの概要](https://technet.microsoft.com/library/hh831739.aspx)」および[記憶域スペースの FAQ](https://social.technet.microsoft.com/wiki/contents/articles/11382.storage-spaces-frequently-asked-questions-faq.aspx) に関するページを参照してください。
    >
    > 

* **キャッシュ**

    Premium Storage をサポートする仮想マシン (VM) には、高いレベルのスループットと低い待ち時間を実現するための独自のキャッシュ機能が備わっています。 これらのキャッシュ機能は、基になる Premium Storage ディスク パフォーマンスを上回ります。 ただし、すべての VM がキャッシュをサポートするわけではないため、詳細については VM の仕様を確認して、関心を持っている VM のサイズについて調べてください。  キャッシュをサポートしている VM の場合、VM の仕様の「Max cached and temp storage throughput (最大キャッシュおよび一時ストレージのスループット)」測定にこの情報が示されています。  また、VM のタイトルのすぐ下にも指定されています。
    
    キャッシュが使用できる場合、Premium Storage ディスク上のディスク キャッシュ ポリシーを **ReadOnly**、**ReadWrite**、または **None** に設定できます。 既定のディスク キャッシュ ポリシーは、すべての Premium データ ディスクでは **ReadOnly**、オペレーティング システム ディスクでは **ReadWrite** です。 アプリケーションに最適なパフォーマンスを実現するために、適切なキャッシュ設定を使用してください。 
    
    たとえば、SQL Server データ ファイルなどの読み取り負荷の高いまたは読み取り専用のデータ ディスクの場合、ディスク キャッシュ ポリシーを **ReadOnly** に設定します。 SQL Server ログ ファイルなどの書き込み負荷の高いまたは書き込み専用のデータ ディスクの場合、ディスク キャッシュ ポリシーを **None** に設定します。 
    
    Premium Storage での設計の最適化の詳細については、[Premium Storage でのパフォーマンスのための設計](../articles/virtual-machines/windows/premium-storage-performance.md)に関する記事を参照してください。

* **Analytics**

    Premium Storage のディスクを使用して VM のパフォーマンスを分析するには、[Azure Portal](https://portal.azure.com) で VM の診断を有効にします。 詳細については、[Azure Diagnostics の拡張機能を使用した Azure VM の監視](https://azure.microsoft.com/blog/2014/09/02/windows-azure-virtual-machine-monitoring-with-wad-extension/)に関する記事を参照してください。 

    ディスクのパフォーマンスを確認するには、[Windows パフォーマンス モニター](https://technet.microsoft.com/library/cc749249.aspx) (Windows VM の場合) や [iostat](http://linux.die.net/man/1/iostat) コマンド (Linux VM の場合) などのオペレーティング システム ベースのツールを使用します。

* **VM のスケール制限とパフォーマンス**

    Premium Storage でサポートされる VM サイズごとに、スケール制限のほか、IOPS、帯域幅、および VM ごとに接続できるディスク数に関するパフォーマンス仕様があります。 VM で Premium Storage ディスクを使用するときは、ディスク トラフィックを促進するために、VM に十分な IOPS と帯域幅があることを確認してください。

    たとえば、STANDARD_DS1 VM では、Premium Storage ディスク トラフィック専用の帯域幅は 32 MB/秒です。 P10 Premium Storage ディスクでは、100 MB/秒の帯域幅が提供されます。 P10 Premium Storage ディスクがこの VM に接続されると、32 MB/秒が上限になります。 P10 ディスクの上限である 100 MB/秒を使用することはできません。

    現時点では、DS シリーズの最大の VM は Standard_DS15_v2 です。 Standard_DS15_v2 のすべてのディスクの帯域幅は最大 960 MB/秒になります。 GS シリーズの最大の VM は Standard_GS5 です。 Standard_GS5 のすべてのディスクの帯域幅は最大 2,000 MB/秒になります。

    これらの制限は、ディスク トラフィックのみを対象としています。 これらの制限にキャッシュ ヒット数とネットワーク トラフィックは含まれません。 VM ネットワーク トラフィックには、別の帯域幅を使用できます。 ネットワーク トラフィックの帯域幅は、Premium Storage ディスクで使用される専用の帯域幅とは異なります。

    Premium Storage でサポートされる VM の最大 IOPS とスループット (帯域幅) の最新情報については、[Windows VM のサイズ](../articles/virtual-machines/windows/sizes.md)に関する記事または [Linux VM のサイズ](../articles/virtual-machines/linux/sizes.md)に関する記事を参照してください。

    Premium Storage ディスクと、その IOPS およびスループットの制限の詳細については、次のセクションの表を参照してください。

## <a name="scalability-and-performance-targets"></a>スケーラビリティとパフォーマンスのターゲット
このセクションでは、Premium Storage を使用する際に考慮すべきスケーラビリティとパフォーマンスのターゲットについて説明します。

Premium Storage アカウントのスケーラビリティ ターゲットは、次のとおりです。

| 合計アカウント容量 | ローカル冗長ストレージ アカウントの合計帯域幅 |
| --- | --- | 
| ディスク容量:35 TB <br>スナップショット容量:10 TB | 受信<sup>1</sup> と送信<sup>2</sup> を合わせて最大 50 GB/秒 |

<sup>1</sup> ストレージ アカウントに送信されるすべてのデータ (要求)

<sup>2</sup> ストレージ アカウントから受信されるすべてのデータ (応答)

詳細については、「[Azure Storage のスケーラビリティおよびパフォーマンスのターゲット](../articles/storage/common/storage-scalability-targets.md)」を参照してください。

非管理対象ディスクに Premium Storage アカウントを使用しており、アプリケーションが 1 つのストレージ アカウントのスケーラビリティ ターゲットを超えた場合、管理ディスクへの移行が必要になることがあります。 管理ディスクに移行しない場合は、複数のストレージ アカウントを使用するようにアプリケーションを作成します。 その後、それらのストレージ アカウント間でデータをパーティション分割します。 たとえば、複数の VM で合計 51 TB のディスクを接続する場合、ディスクを 2 つのストレージ アカウントに分散します。 Premium Storage アカウント 1 つあたりの上限は 35 TB です。 1 つの Premium Storage アカウントでプロビジョニングするディスクの容量が 35 TB を超えることがないようにしてください。

### <a name="premium-storage-disk-limits"></a>Premium Storage ディスクの制限
Premium Storage ディスクをプロビジョニングする場合、ディスクのサイズによって最大 IOPS とスループット (帯域幅) が決まります。 Azure では、Premium Storage ディスクとして 8 種類の GA を提供しています:P4 (Managed Disks のみ)、P6 (Managed Disks のみ)、P10、P15 (Managed Disks のみ)、P20、P30、P40、P50。 プレビュー段階の 3 つのディスク サイズもあります:P60、P70、P80。 Premium Storage ディスクの種類それぞれには、IOPS とスループットに関する固有の制限があります。 ディスクの種類の制限については、次の表で説明します。

アスタリスクで示されるサイズはプレビュー中です。

| Premium ディスクの種類  | P4    | P6    | P10    | P15    | P20    | P30              | P40             | P50             | P60 *            | P70 *               | P80 *               |
|---------------------|-------|-------|--------|--------|--------|------------------|-----------------|-----------------|-----------------|--------------------|--------------------|
| ディスク サイズ           | 32 GiB| 64 GiB| 128 GiB| 256 GiB| 512 GiB| 1024 GiB (1 TiB) | 2048 GiB (2 TiB)| 4095 GiB (4 TiB)| 8192 GiB (8 TiB)| 16,384 GiB (16 TiB)| 32,767 GiB (32 TiB)|
| ディスクあたりの IOPS       | 120   | 240   | 500    | 1100   | 2300   | 5000             | 7500            | 7500            | 12,500          | 15,000             | 20,000             |
| ディスクあたりのスループット | 25 MB/秒 | 50 MB/秒 | 100 MB/秒 | 125 MB/秒 | 150 MB/秒 | 200 MB/秒 | 250 MB/秒 | 250 MB/秒 | 480 MB/秒 | 750 MB/秒 | 750 MB/秒 |

> [!NOTE]
> [Premium Storage でサポートされる VM]() に関するセクションの説明に従って、ディスク トラフィックを促進するために、VM で十分な帯域幅を使用できることを確認してください。 確認できない場合、ディスクのスループットと IOPS の値は低くなります。 スループットと IOPS の最大値は、上記の表で説明したディスクの制限ではなく、VM の制限に基づきます。  
> Azure では、超並列のプラットフォームとして Premium Storage が設計されました。 アプリケーションをマルチスレッド形式に設計することで、より大きなディスク サイズで高いパフォーマンス目標を達成できます。

Premium Storage のスケーラビリティとパフォーマンスのターゲットについか知っておくべき重要事項をいくつか説明します。

* **プロビジョニング済みの容量とパフォーマンス**

    Premium Storage ディスクをプロビジョニングすると、Standard Storage とは異なり、対象のディスクの容量、IOPS、スループットが保証されます。 たとえば、P50 ディスクを作成した場合、対象のディスクに 4,095 GB のストレージ容量、7,500 IOPS、および 250 MB/秒のスループットがプロビジョニングされます。 アプリケーションでは、容量とパフォーマンスのすべてまたは一部を使用できます。 Premium SSD ディスクは、99.9% の時間においてターゲット パフォーマンスを提供するように設計されています。

* **ディスク サイズ**

    Azure はディスク サイズ (切り上げたもの) を、前のセクションの表に記載されているオプションの中で最も近い Premium Storage ディスク オプションにマップします。 たとえば、ディスク サイズが 100 GB の場合は、P10 オプションとして分類されます。 この場合は、最大 500 IOPS を実行でき、スループットは最大 100 MB/秒になります。 同様に、ディスク サイズが 400 GB の場合は、P20 として分類されます。 この場合は、最大 2,300 IOPS を実行でき、スループットは 150 MB/秒になります。

    > [!NOTE]
    > 既存のディスクのサイズを簡単に増やすことができます。 たとえば、30 GB のディスクのサイズを 128 GB または 1 TB に増やすことができます。 また、容量の増加や IOPS とスループットの向上が必要になるため、P20 ディスクを P30 ディスクに変換することもできます。 

* **I/O サイズ**

    I/O のサイズは 256 KB です。 転送されるデータが 256 KB に満たない場合は、1 つの I/O ユニットとみなされます。 I/O のサイズが大きくなると、サイズが 256 KB の複数の I/O としてカウントされます。 たとえば、1,100 KB I/O は 5 つの I/O ユニットとしてカウントされます。

* **スループット**

    スループットの上限には、ディスクへの書き込みのほか、キャッシュからではないディスク読み取り操作も含まれます。 たとえば、P10 ディスクのスループットはディスクあたり 100 MB/秒です。 次の表に、P10 ディスクの有効なスループットの例をいくつか示します。

    | P10 ディスクあたりの最大スループット | ディスクからの非キャッシュの読み取り | ディスクへの非キャッシュの書き込み |
    | --- | --- | --- |
    | 100 MB/秒 | 100 MB/秒 | 0 |
    | 100 MB/秒 | 0 | 100 MB/秒 |
    | 100 MB/秒 | 60 MB/s | 40 MB/秒 |

* **キャッシュ ヒット数**

    キャッシュ ヒット数には、ディスクの割り当て済みの IOPS またはスループットによる制限はありません。 たとえば、Premium Storage でサポートされている VM でキャッシュが **ReadOnly** に設定されているデータ ディスクを使用する場合、そのキャッシュからの読み取りは、ディスクの IOPS とスループットの上限の対象にはなりません。 ディスクのワークロードの大部分が読み取りの場合、スループットが非常に高くなる可能性があります。 キャッシュには、VM サイズに基づいて、VM レベルで IOPS とスループットの制限が個別に適用されます。 DS シリーズの VM では、IOPS が約 4,000、キャッシュとローカル SSD の I/O のコアあたりのスループットが 33 MB/秒になります。 GS シリーズの VM には、IOPS が 5,000 まで、キャッシュとローカル SSD の I/O のコアあたりのスループットが 50 MB/秒までという制限があります。

## <a name="throttling"></a>Throttling

アプリケーションの IOPS またはスループットが Premium Storage ディスクに割り当てられている制限を超えた場合、調整が行われることがあります。 また、VM 上のすべてのディスクのディスク トラフィックの合計がその VM で使用可能なディスク帯域幅の上限を超えた場合にも調整が行われることがあります。 調整を回避するには、ディスクの保留中の I/O 要求の数を制限することをお勧めします。 プロビジョニングしたディスクのスケーラビリティとパフォーマンスのターゲットに基づいた制限や、VM で使用可能なディスク帯域幅に基づいた制限を使用してください。  

調整を回避できれば、アプリケーションの待ち時間を最短にすることができます。 ただし、ディスクの保留中の I/O 要求の数が少なすぎる場合は、ディスクで使用可能な最大 IOPS とスループット レベルのメリットを活用できません。

次の例では、調整レベルの計算方法を示しています。 すべての計算は、I/O ユニット サイズ 256 KB に基づいて行われます。

### <a name="example-1"></a>例 1

アプリケーションは、P10 ディスク上で、1 秒間に 16 KB サイズの I/O ユニットを 495 回処理しました。 I/O ユニットは、495 IOPS と見なされます。 同じ 1 秒内に 2 MB の I/O を実行すると、I/O ユニットの合計は 495 + 8 IOPS となります。 これは、I/O ユニット サイズが 256 KB である場合、2 MB の I/O は 2,048 KB / 256 KB = 8 I/O ユニットとなるためです。 495 + 8 の合計はディスク制限の 500 IOPS を超過するため、調整が発生します。

### <a name="example-2"></a>例 2

アプリケーションは、P10 ディスク上で、256 KB サイズの I/O ユニットを 400 回処理しました。 合計消費帯域幅は、(400 &#215; 256) / 1,024 KB = 100 MB/秒になります。 P10 ディスクには、100 MB/秒というスループット制限があります。 アプリケーションが 1 秒間あたりにより多くの I/O 操作を実行しようとすると、割り当てられた制限を超過してしまうため、調整されます。

### <a name="example-3"></a>例 3

DS4 VM に P30 ディスクが 2 つアタッチされています。 各 P30 ディスクのスループットは 200 MB/秒です。 ただし、DS4 VM のディスク帯域幅合計は 256 MB/秒が上限です。 この DS4 VM では、接続されたディスクの両方で同時に最大スループットを発揮することはできません。 これを解決するには、一方のディスクのトラフィックを 200 MB/秒で維持し、他方のディスクのトラフィックを 56 MB/秒で維持します。 ディスク トラフィックの合計が 256 MB/秒を超えると、ディスク トラフィックは調整されます。

> [!NOTE]
> ディスク トラフィックの大部分が小さなサイズの I/O で構成されている場合、アプリケーションは、スループット制限に達する前に、IOPS 制限に達することがよくあります。 一方、ディスク トラフィックの大部分が大きなサイズの I/O で構成されている場合は、IOPS 制限ではなく最初にスループット制限に達することがよくあります。 最適なサイズの I/O を使用して、アプリケーションの IOPS とスループット容量を最大化できます。 また、ディスクの保留中の I/O 要求の数を制限することもできます。

Premium Storage を使用した高パフォーマンスのための設計の詳細については、[Premium Storage でのパフォーマンスのための設計](../articles/virtual-machines/windows/premium-storage-performance.md)に関する記事を参照してください。

## <a name="snapshots-and-copy-blob"></a>スナップショットと Copy Blob

Storage サービスでは、VHD ファイルはページ BLOB です。 ページ BLOB のスナップショットを作成し、別の場所 (別のストレージ アカウントなど) にコピーできます。

### <a name="unmanaged-disks"></a>非管理対象ディスク

Standard Storage でスナップショットを使用する場合と同様に、非管理対象 Premium ディスクの[増分スナップショット](../articles/virtual-machines/linux/incremental-snapshots.md)を作成します。 Premium Storage では、レプリケーション オプションとしてローカル冗長ストレージのみがサポートされています。 スナップショットを作成し、そのスナップショットを geo 冗長 Standard Storage アカウントにコピーすることをお勧めします。 詳細については、[Azure Storage の冗長オプション](../articles/storage/common/storage-redundancy.md)に関する記事を参照してください。

ディスクが VM に接続されている場合、そのディスクに対する一部の API 操作は許可されません。 たとえば、ディスクが VM に接続されている場合は、その BLOB に対して [Copy Blob](/rest/api/storageservices/Copy-Blob) 操作を実行できません。 代わりに、まず、[Snapshot Blob](/rest/api/storageservices/Snapshot-Blob) REST API を使用してその BLOB のスナップショットを作成します。 その後、スナップショットの [Copy Blob](/rest/api/storageservices/Copy-Blob) を実行して、接続されたディスクをコピーします。 または、ディスクの接続を解除してから必要な操作を実行できます。

Premium Storage BLOB スナップショットには次の制限が適用されます。

| Premium Storage の制限 | 値 |
| --- | --- |
| BLOB あたりのスナップショットの最大数 | 100 |
| スナップショットのストレージ アカウントの容量<br>(スナップショットのデータのみを含み、 ベース BLOB のデータは含まない) | 10 TB |
| 連続したスナップショットの最小時間間隔 | 10 分 |

スナップショットの地理冗長コピーを維持するには、AzCopy か Copy Blob を使用して、Premium Storage アカウントから地理冗長 Standard Storage アカウントにスナップショットをコピーできます。 詳細については、「[AzCopy コマンド ライン ユーティリティを使用してデータを転送する](../articles/storage/common/storage-use-azcopy.md)」および「[Copy Blob](/rest/api/storageservices/Copy-Blob)」を参照してください。

Premium Storage アカウントでページ BLOB に対して REST 操作を実行する方法の詳細については、[Azure Premium Storage での Blob service の操作](https://go.microsoft.com/fwlink/?LinkId=521969)に関する記事を参照してください。

### <a name="managed-disks"></a>マネージド ディスク

管理ディスクのスナップショットは、管理ディスクの読み取り専用コピーです。 このスナップショットは、Standard 管理ディスクとして保存されます。 現時点では、管理ディスクの[増分スナップショット](../articles/virtual-machines/windows/incremental-snapshots.md)はサポートされていません。 管理ディスクのスナップショットを作成する方法については、[Windows で管理スナップショットを使用して Azure 管理ディスクとして保存された VHD のコピーを作成する方法](../articles/virtual-machines/windows/snapshot-copy-managed-disk.md)に関する記事と、[Linux で管理スナップショットを使用して Azure 管理ディスクとして保存された VHD のコピーを作成する方法](../articles/virtual-machines/linux/snapshot-copy-managed-disk.md)に関する記事を参照してください。

管理ディスクが VM に接続されている場合、そのディスクに対する一部の API 操作は許可されません。 たとえば、ディスクが VM に接続されているときには、Shared Access Signature (SAS) を生成してコピー操作を実行することはできません。 代わりに、ディスクのスナップショットを作成してから、スナップショットのコピーを実行します。 または、ディスクの接続を解除してから、SAS を生成してコピー操作を実行することもできます。


## <a name="premium-storage-for-linux-vms"></a>Linux VM 用の Premium Storage
次の情報を使用すると、Premium Storage で Linux VM をセットアップすることができます。

Premium Storage でスケーラビリティのターゲットを達成するには、キャッシュが **ReadOnly** または **None** に設定されているすべての Premium Storage ディスクで、ファイル システムをマウントするときに "バリア" を無効にする必要があります。 これらのキャッシュ設定では Premium Storage ディスクへの書き込みの耐久性が保証されるため、このシナリオではバリアが必要ありません。 書き込み要求が正常に完了した時点で、データは永続的なストアに書き込まれた状態になっています。 "バリア" を無効にするには、次のいずれかの方法を使用します。 ファイル システムに応じて選択してください。
  
* **reiserFS** の場合、バリアを無効にするには、`barrier=none` マウント オプションを使用します  (バリアを有効にするには、`barrier=flush` を使用します)。
* **ext3/ext4** の場合、バリアを無効にするには、`barrier=0` マウント オプションを使用します  (バリアを有効にするには、`barrier=1` を使用します)。
* **XFS** の場合、バリアを無効にするには、`nobarrier` マウント オプションを使用します  (バリアを有効にするには、`barrier` を使用します)。
* キャッシュが **ReadWrite** に設定されている Premium Storage ディスクの場合は、書き込みの耐久性のためにバリアを有効にしてください。
* VM を再起動してもボリューム ラベルが変更されないようにするには、ディスクに対する汎用一意識別子 (UUID) 参照で /etc/fstab を更新する必要があります。 詳細については、[Linux VM への管理ディスクの追加](../articles/virtual-machines/linux/add-disk.md)に関する記事を参照してください。

Azure Premium Storage では、次の Linux ディストリビューションが検証されました。 Premium Storage でパフォーマンスと安定性を高めるには、VM を最低でも次のいずれかのバージョン (またはそれ以降のバージョン) にアップグレードすることをお勧めします。 バージョンによっては、Azure 向けの最新の Linux Integration Services (LIS) v4.0 が必要になります。 ディストリビューションをダウンロードしてインストールするには、次の表に記載されているリンクを参照してください。 検証が完了すると、イメージが一覧に追加されます。 イメージごとにパフォーマンスが変動することが検証によって判明することに注意してください。 パフォーマンスは、ワークロードの特性やイメージの設定に応じて異なります。 ワークロードの種類に応じて、異なるイメージをチューニングします。

| ディストリビューション | Version | サポートされるカーネル | 詳細 |
| --- | --- | --- | --- |
| Ubuntu | 12.04 | 3.2.0-75.110+ | Ubuntu-12_04_5-LTS-amd64-server-20150119-en-us-30GB |
| Ubuntu | 14.04 | 3.13.0-44.73+ | Ubuntu-14_04_1-LTS-amd64-server-20150123-en-us-30GB |
| Debian | 7.x、8.x | 3.16.7-ckt4-1+ | &nbsp; |
| SUSE | SLES 12| 3.12.36-38.1+| suse-sles-12-priority-v20150213 <br> suse-sles-12-v20150213 |
| SUSE | SLES 11 SP4 | 3.0.101-0.63.1+ | &nbsp; |
| CoreOS | 584.0.0+| 3.18.4+ | CoreOS 584.0.0 |
| CentOS | 6.5、6.6、6.7、7.0 | &nbsp; | [LIS4 が必須](https://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) <br> "*次のセクションの注を参照してください*" |
| CentOS | 7.1+ | 3.10.0-229.1.2.el7+ | [LIS4 が推奨](https://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) <br> "*次のセクションの注を参照してください*" |
| Red Hat Enterprise Linux (RHEL) | 6.8+、7.2+ | &nbsp; | &nbsp; |
| Oracle | 6.0+、7.2+ | &nbsp; | UEK4 または RHCK |
| Oracle | 7.0-7.1 | &nbsp; | UEK4 または RHCK と [LIS 4.1 +](https://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) |
| Oracle | 6.4-6.7 | &nbsp; | UEK4 または RHCK と [LIS 4.1 +](https://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) |


### <a name="lis-drivers-for-openlogic-centos"></a>OpenLogic CentOS 用 LIS ドライバー

OpenLogic CentOS VM を実行している場合は、次のコマンドを実行して、最新のドライバーをインストールしてください。

```
sudo rpm -e hypervkvpd  ## (Might return an error if not installed. That's OK.)
sudo yum install microsoft-hyper-v
```

新しいドライバーをアクティブにするには、VM を再起動してください。

## <a name="pricing-and-billing"></a>価格と課金

Premium Storage の使用時には、課金に関する次の考慮事項が適用されます。

* **Premium Storage のディスク/BLOB サイズ**

    Premium Storage ディスクまたは BLOB への課金は、ディスクまたは BLOB のプロビジョニング済みサイズによって異なります。 Azure では、プロビジョニング済みサイズ (切り上げたもの) を、最も近い Premium Storage ディスク オプションにマップします。 詳細については、[Premium Storage のスケーラビリティとパフォーマンスのターゲット]()に関するセクションの表を参照してください。 各ディスクは、サポートされているプロビジョニング済みディスク サイズにマップされ、それに応じて課金されます。 プロビジョニングされたディスクには、Premium Storage プランの月額料金を使用して、時間割りで計算して課金されます。 たとえば、P10 ディスクをプロビジョニングし、20 時間後にそのディスクを削除した場合は、P10 製品の 20 時間分に対して課金されます。 これは、実際にディスクに書き込まれたデータの量や、使用された IOPS およびスループットには関係ありません。

* **Premium 非管理対象ディスクのスナップショット**

    Premium 非管理対象ディスクのスナップショットについては、スナップショットで使用された追加の容量に対して課金されます。 スナップショットの詳細については、[BLOB のスナップショットの作成](/rest/api/storageservices/Snapshot-Blob)に関する記事を参照してください。

* **Premium 管理ディスクのスナップショット**

    管理ディスクのスナップショットは、ディスクの読み取り専用コピーです。 このディスクは、Standard 管理ディスクとして保存されます。 スナップショットのコストは Standard 管理ディスクと同じです。 たとえば、128 GB の Premium 管理ディスクのスナップショットを作成した場合、スナップショットのコストは 128 GB の Standard 管理ディスクと同等になります。  

* **送信データ転送**

    [送信データ転送](https://azure.microsoft.com/pricing/details/data-transfers/) (Azure データセンターから送信されるデータ) は、帯域幅の使用量に対して課金されます。

Premium Storage、Premium Storage でサポートされる VM、管理ディスクの価格の詳細については、次の記事を参照してください。

* [Azure Storage の料金](https://azure.microsoft.com/pricing/details/storage/)
* [Virtual Machines の料金](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Managed Disks の価格](https://azure.microsoft.com/pricing/details/managed-disks/)

## <a name="azure-backup-support"></a>Azure Backup のサポート

リージョンのディザスター リカバリーでは、[Azure Backup](../articles/backup/backup-introduction-to-azure-backup.md) のほか、バックアップ コンテナーとして GRS ストレージ アカウントを使用して、VM ディスクを別のリージョンにバックアップする必要があります。

時間ベースのバックアップ、VM の簡易復元、バックアップの保持ポリシーを使用してバックアップ ジョブを作成するには、Azure Backup を使用します。 Backup は、非管理対象ディスクと管理ディスクの両方で使用できます。 詳細については、[非管理対象ディスクを使用した VM に対する Azure Backup](../articles/backup/backup-azure-vms-first-look-arm.md) に関する記事と[管理ディスクを使用した VM に対する Azure Backup](../articles/backup/backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup) に関する記事を参照してください。 

## <a name="next-steps"></a>次の手順

Premium Storage の詳細については、次の記事を参照してください。
