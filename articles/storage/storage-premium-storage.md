---
title: "高パフォーマンスの Premium Storage と Azure VM ディスク | Microsoft Docs"
description: "高パフォーマンスの Premium Storage および Azure VM の非管理対象ディスクと管理ディスクについて説明します。 Azure DS シリーズ、DSv2 シリーズおよび GS シリーズの VM は、Premium Storage をサポートしています。"
services: storage
documentationcenter: 
author: ramankumarlive
manager: aungoo-msft
editor: tysonn
ms.assetid: e2a20625-6224-4187-8401-abadc8f1de91
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: ramankum
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: 26e78f559fa9a82183a26034580148e39331a214
ms.lasthandoff: 03/17/2017


---
# <a name="high-performance-premium-storage-and-unmanaged-and-managed-azure-vm-disks"></a>高パフォーマンスの Premium Storage および Azure VM の非管理対象ディスクと管理ディスク
Microsoft Azure Premium Storage は、I/O 集中型のワークロードを実行する仮想マシン (VM) 向けに、高パフォーマンスで待機時間の少ないディスク サポートを提供します。 Premium Storage を使用する VM ディスクでは、ソリッド ステート ドライブ (SSD) にデータを格納します。 アプリケーションの VM ディスクを Azure Premium Storage に移行することで、これらのディスクの速度とパフォーマンスを最大限に利用することができます。

Azure VM には複数の Premium Storage ディスクを接続できるので、アプリケーションは VM あたり最大 64 TB のストレージを使用できます。 Premium Storage を使用すると、アプリケーションは、VM あたり 80,000 IOPS (1 秒あたりの入出力操作) と、VM あたり最大 2000 MB/秒のディスク スループットを実現でき、読み取り操作の待機時間も非常に短くなります。

Premium Storage により、Azure は Dynamics AX、Dynamics CRM、Exchange Server、SharePoint Farms、SAP Business Suite などの要求の厳しいエンタープライズ アプリケーションを本当の意味でクラウドにリフトアンドシフトできます。 Premium Storage では、SQL Server、Oracle、MongoDB、MySQL、Redis のような一貫した高パフォーマンスと低待機時間を必要とする負荷の高いさまざまなデータベース ワークロードを実行できます。

> [!NOTE]
> アプリケーションが最高レベルのパフォーマンスを実現できるように、高い IOPS を必要とする仮想マシン ディスクは Premium Storage に移行することをお勧めします。 ディスクが高い IOPS を必要としない場合は、ディスクを Standard Storage 内に保持することでコストを抑えることができます。Standard Storage の場合、仮想マシンのディスク データは SSD ではなくハード ディスク ドライブ (HDD) に格納されます。
> 

Azure VM の Premium ディスクは、次の 2 とおりの方法で作成できます。

**非管理対象ディスク**: これは、VM ディスクに対応する VHD ファイルの格納に使用するストレージ アカウントをユーザーが管理する本来の方法です。 VHD ファイルは、ストレージ アカウントにページ BLOB として格納されます。 

**[Azure Managed Disks](storage-managed-disks-overview.md)**: この機能は、VM ディスクに使用するストレージ アカウントを管理します。 必要なディスクの種類 (Premium または Standard) とサイズを指定すれば、ディスクの作成と管理は Azure によって行われます。 ストレージ アカウントのスケーラビリティの制限を超えないように、複数のストレージ アカウントにディスクを配置することを気に掛ける必要はありません。Azure がこれを管理します。

どちらの種類のディスクも利用できる場合でも、Managed Disks を使用してさまざまな機能を活用することをお勧めします。

Azure Premium Storage を使用するには、[無料試用版](https://azure.microsoft.com/pricing/free-trial/)のページをご覧ください。 

既存の VM を Premium Storage に移行する方法については、[Managed Disks への既存の Azure Windows VM の移行](../virtual-machines/virtual-machines-windows-convert-unmanaged-to-managed-disks.md)に関する記事、または[Managed Disks への既存の Azure Linux VM の移行](../virtual-machines/virtual-machines-linux-convert-unmanaged-to-managed-disks.md)に関する記事をご覧ください。

> [!NOTE]
> 現在、Premium Storage はほとんどのリージョンでサポートされています。 [リージョン別の Azure サービス](https://azure.microsoft.com/regions/#services)に関するページの利用可能なリージョンの一覧で、サイズ シリーズの VM (DS、DSV2、Fs、GS) をサポートするリージョンを確認してください。
> 

## <a name="premium-storage-features"></a>Premium Storage の機能

ここでは、Premium Storage の機能をいくつか紹介します。

**Premium Storage ディスク**: Azure Premium Storage は、特定のサイズ シリーズの VM (DS、DSv2、GS、Fs) に接続できる VM ディスクをサポートしています。 それぞれパフォーマンス仕様の異なる P10 (128GiB)、P20 (512GiB)、P30 (1024GiB) の 3 つのディスク サイズから選択できます。 アプリケーションの要件に応じて、これらのディスクを 1 つ以上 VM に接続できます。 [Premium Storage の拡張性とパフォーマンスの目標 ](#premium-storage-scalability-and-performance-targets) に関する次のセクションで、仕様についてさらに詳しく説明します。

**Premium ページ BLOB**: Premium Storage ではページ BLOB をサポートしています。ページ BLOBは、VM の永続非管理対象ディスクを格納するために使用されます。 Standard Storage とは異なり、Premium Storage では、ブロック BLOB、追加 BLOB、ファイル、テーブル、キューはサポートしていません。
Premium Storage アカウントに配置されているオブジェクトはページ BLOB であり、サポートされているプロビジョニング済みサイズのいずれかにスナップされます。 Premium Storage アカウントが小さな BLOB を格納するためのものではないのはこのためです。

**Premium Storage アカウント**: Premium Storage の使用を開始するには、非管理対象ディスクの Premium Storage アカウントを作成します。 [Azure Portal](https://portal.azure.com) を使用する場合は、"Premium" パフォーマンス レベルと、レプリケーション オプションとして "ローカル冗長ストレージ (LRS)" を指定することで、Premium Storage アカウントを作成できます。 また、[Storage REST API](/rest/api/storageservices/fileservices/Azure-Storage-Services-REST-API-Reference) バージョン 2014-02-14 以降、[Service Management REST API](http://msdn.microsoft.com/library/azure/ee460799.aspx) バージョン 2014-10-01 以降 (クラシック デプロイ)、[Azure Storage Resource Provider REST API リファレンス](/rest/api/storagerp) (Resource Manager デプロイ)、[Azure PowerShell](../powershell-install-configure.md) バージョン 0.8.10 以降を使用している場合は、種類として "Premium_LRS" を指定することで、Premium Storage アカウントを作成することもできます。 Premium Storage アカウントの制限については、以降の「 [Premium Storage の拡張性とパフォーマンスの目標](#premium-storage-scalability-and-performance-targets.md)」を参照してください。

**Premium ローカル冗長ストレージ**: Premium Storage アカウントは、レプリケーション オプションとしてローカル冗長ストレージ (LRS) のみをサポートしています。つまり、1 つのリージョン内にデータのコピーを 3 つ保持します。 地域的な障害復旧では、[Azure Backup サービス](../backup/backup-introduction-to-azure-backup.md)と GRS ストレージ アカウントをバックアップ コンテナーとして使用して VM ディスクを別のリージョンにバックアップする必要があります。 

Azure では、ストレージ アカウントを非管理対象ディスクのコンテナーとして使用します。 非管理対象ディスクを使用する Azure DS、DSv2、GS、または Fs VM を作成し、Premium Storage アカウントを選択すると、オペレーティング システム ディスクとデータ ディスクがそのストレージ アカウントに格納されます。

## <a name="premium-storage-supported-vms"></a>Premium Storage でサポートされる VM
Premium Storage は、DS シリーズ、DSv2 シリーズ、GS シリーズ、Fs シリーズの VM をサポートしています。 これらの VM では、Standard Storage ディスクと Premium Storage ディスクの両方を使用できます。 Premium Storage に互換性のない VM シリーズで Premium Storage ディスクを使用することはできません。

Windows VM で使用できる Azure VM の種類やサイズについては、「 [Windows VM のサイズ](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」を参照してください。 Linux VM の VM の種類やサイズについては、「 [Linux VM のサイズ](../virtual-machines/virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照してください。

次に、DS、DSv2、GS、Fs の各シリーズの VM の機能をいくつか紹介します。

**Cloud Service**: DS シリーズ VM は DS シリーズ VM のみを含むクラウド サービスに追加できます。 DS シリーズ以外の VM が存在する既存のクラウド サービスに DS シリーズの VM を追加しないでください。 既存の VHD を DS シリーズ VM のみを実行する新しいクラウド サービスに移行できます。 DS シリーズ VM をホストする新しいクラウド サービスに引き続き同じ仮想 IP アドレス (VIP) を使用するには、 [予約済み IP アドレス](../virtual-network/virtual-networks-instance-level-public-ip.md)を使います。 GS シリーズの VM は、GS シリーズの VM のみを実行している既存のクラウド サービスに追加できます。

**オペレーティング システム ディスク**: Premium Storage で実行できる VM は、Premium または Standard オペレーティング システム (OS) ディスクを使用するように構成できます。 最適なエクスペリエンスを実現するために、Premium Storage ベースの OS ディスクを使用することをお勧めします。

**データ ディスク**: Premium Storage で実行されている同じ VM で、Premium ディスクと Standard ディスクの両方を使用できます。 Premium Storage を使用すると、VM をプロビジョニングし、複数の永続データ ディスクを VM に接続できます。 必要に応じて、ディスク全体をストライピングして容量を増やし、ボリュームのパフォーマンスを高めることができます。

> [!NOTE]
> [記憶域スペース](http://technet.microsoft.com/library/hh831739.aspx)を使用して Premium Storage データ ディスクをストライピングする場合は、使用するディスクごとに 1 つの列で構成する必要があります。 そうしない場合は、ディスク全体のトラフィックの配分が不均等になるため、ストライプ ボリュームの全体的なパフォーマンスが低下する可能性があります。 既定では、サーバー マネージャー ユーザー インターフェイス (UI) で最大 8 つのディスクの列を設定できます。 8 つ以上のディスクがある場合は、PowerShell を使用してボリュームを作成し、列の数を手動で指定する必要があります。 そうしない場合、サーバー マネージャー UI はそれ以上のディスクがある場合でも 8 つの列を使用し続けます。 たとえば、1 つのストライプ セット内に 32 のディスクがある場合は、32 の列を指定する必要があります。 PowerShell の *New-VirtualDisk* コマンドレットの [NumberOfColumns](http://technet.microsoft.com/library/hh848643.aspx) パラメーターを使用して、仮想ディスクが使用する列数を指定できます。 詳しくは、「[記憶域スペースの概要](http://technet.microsoft.com/library/hh831739.aspx)」および「[Storage Spaces Frequently Asked Questions (記憶域スペースに関してよく寄せられる質問)](http://social.technet.microsoft.com/wiki/contents/articles/11382.storage-spaces-frequently-asked-questions-faq.aspx)」をご覧ください。
> 

**キャッシュ**: Premium Storage をサポートするサイズ シリーズの VM は独自のキャッシュ機能を備えています。これにより、基になる Premium Storage ディスク パフォーマンスを上回る高レベルのスループットと待機時間を実現できます。 Premium Storage ディスクでは、ディスク キャッシュ ポリシーを ReadOnly、ReadWrite、または None として構成できます。 既定のディスク キャッシュ ポリシーは、すべてのプレミアム データ ディスクで ReadOnly、オペレーティング システム ディスクで ReadWrite です。 アプリケーションのパフォーマンスを最適化するために、適切な構成設定を使用してください。 たとえば、SQL Server データ ファイルなどの読み取り負荷の高いまたは読み取り専用のデータ ディスクの場合、ディスク キャッシュ ポリシーを "ReadOnly" に設定します。 SQL Server ログ ファイルなどの書き込み負荷の高いまたは書き込み専用のデータ ディスクの場合、ディスク キャッシュ ポリシーを "None" に設定します。 Premium Storage での設計の最適化について詳しくは、「[Premium Storage を使用した高パフォーマンスのための設計](storage-premium-storage-performance.md)」をご覧ください。

**分析**: Premium Storage のディスクを使用して VM のパフォーマンスを分析するには、[Azure Portal](https://portal.azure.com) で VM 診断を有効にします。 詳細については、「[Microsoft Azure Virtual Machine Monitoring with Azure Diagnostics Extension (Azure 診断の拡張機能を使用した Microsoft Azure 仮想マシンの監視)](https://azure.microsoft.com/blog/2014/09/02/windows-azure-virtual-machine-monitoring-with-wad-extension/)」をご覧ください。 ディスクのパフォーマンスを確認するには、オペレーティング システム ベースのツール (Windows VM 向け [Windows パフォーマンス モニター](https://technet.microsoft.com/library/cc749249.aspx)、Linux VM 向け [IOSTAT](http://linux.die.net/man/1/iostat) など) を使用します。

**VM のスケール制限とパフォーマンス**: 各 Premium Storage でサポートされる VM サイズには、スケール制限と、IOPS、帯域幅、および VM ごとに接続できるディスク数に関するパフォーマンス仕様があります。 VM で Premium Storage ディスクを使用するときは、ディスク トラフィックを促進するために、VM に十分な IOPS と使用可能な帯域幅があることを確認してください。
たとえば、STANDARD_DS1 VM では、Premium Storage ディスク トラフィック専用の帯域幅は 32 MB/秒です。 P10 Premium Storage ディスクでは、100 MB/秒の帯域幅が提供されます。 この VM に P10 Premium Storage ディスクが接続されていても、32 MB/秒が上限となり、P10 ディスクの上限である 100 MB/秒に達することはできません。

現時点では、DS シリーズの最大の VM は Standard_DS15_v2 であり、この VM の全ディスクの帯域幅は最大 960 MB/秒です。 GS シリーズの最大の VM は Standard_GS5 であり、この VM の全ディスクの帯域幅は最大 2000 MB/秒です。
これらの制限は、ディスク トラフィックのみを対象としており、キャッシュ ヒットとネットワーク トラフィックは対象外となります。 VM のネットワーク トラフィックの帯域幅は別に用意されており、Premium Storage ディスク専用の帯域幅とは区別されています。

Premium Storage でサポートされる VM の最大 IOPS とスループット (帯域幅) の最新情報については、「[Windows VM のサイズ](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」または「[Linux VM のサイズ](../virtual-machines/virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください。

Premium Storage の各ディスクの IOPS とスループットの制限については、この記事の「[Premium Storage のスケーラビリティとパフォーマンスのターゲット](#premium-storage-scalability-and-performance-targets)」にある表を参照してください。

## <a name="premium-storage-scalability-and-performance-targets"></a>Premium Storage のスケーラビリティとパフォーマンスのターゲット
このセクションでは、Premium Storage を使用する際に考慮する必要のあるすべての拡張性とパフォーマンスの目標について説明します。

Premium Storage アカウントのスケーラビリティ ターゲットは、次のとおりです。

| 合計アカウント容量 | ローカル冗長ストレージ アカウントの合計帯域幅 |
| --- | --- | 
| ディスク容量: 35 TB <br>スナップショット容量: 10 TB | 受信と送信を合わせて最大 50 GB/秒 |

* 受信とはストレージ アカウントに送信されるすべてのデータ (要求) のことです。
* 送信とはストレージ アカウントから送信されるすべてのデータ (応答) のことです。

詳細については、「 [Azure Storage のスケーラビリティおよびパフォーマンスのターゲット](storage-scalability-targets.md)」をご覧ください。

非管理対象ディスクに Premium Storage アカウントを使用しており、アプリケーションが 1 つのストレージ アカウントのスケーラビリティ ターゲットを超えた場合は、Managed Disks への移行を検討してください。 Managed Disks に移行しない場合は、複数のストレージ アカウントを使用するようにアプリケーションをビルドし、それらのストレージ アカウント間でデータをパーティション分割します。 たとえば、多数の VM があり、合計 51 テラバイト (TB) のディスクを接続する場合、Premium Storage アカウント 1 つあたりの上限は 35 TB であるため、ディスクを 2 つのストレージ アカウントに分散させます。 1 つの Premium Storage アカウントでプロビジョニングするディスクの容量が 35 TB を超えることがないようにしてください。

### <a name="premium-storage-disk-limits"></a>Premium Storage ディスクの制限
Premium Storage ディスクをプロビジョニングする場合、ディスクのサイズによって最大 IOPS とスループット (帯域幅) が決まります。 Premium Storage ディスクには、P10、P20、P30 の 3 種類があります。 次の表に示すように、それぞれに IOPS とスループットに固有の制限があります。

|Premium Storage ディスク タイプ | P10 | P20 | P30 |
| --- | --- | --- | --- |
| ディスク サイズ | 128 GiB | 512 GiB | 1024 GiB (1 TB) |
| ディスクあたりの IOPS | 500 | 2300 | 5000 |
ディスクあたりのスループット | 100 MB/秒 | 150 MB/秒 | 200 MB/秒 |

> [!NOTE]
> この記事の「[Premium Storage でサポートされる VM](#premium-storage-supported-vms)」で説明したように、ディスク トラフィックを促進するために VM に十分な帯域幅があることを確認してください。 このとおりでない場合は、ディスクのスループットと IOPS が VM の上限の制約を受けて低くなり、前述の表で示したディスクの上限は達成できなくなります。  
> 
> 

Premium Storage の拡張性とパフォーマンスの目標についか知っておく必要のある重要事項をいくつか説明します。

* **プロビジョニング済みの容量とパフォーマンス**: Premium Storage ディスクをプロビジョニングした場合、Standard Storage とは異なり、対象のディスクの容量、IOPS、スループットが保証されます。 たとえば、P30 ディスクを作成した場合、対象のディスクに 1024 GB のストレージ容量、5000 IOPS、200 MB/秒のスループットがプロビジョニングされます。 アプリケーションでは、容量とパフォーマンスのすべてまたは一部を使用できます。

* **ディスク サイズ**: Azure はディスク サイズ (切り上げたもの) を、表に記載されているオプションの中で最も近い Premium Storage ディスク オプションにマップします。 たとえば、サイズが 100 GiB のディスクは P10 オプションに分類されるので、最大 500 IOPS を実行でき、スループットは最大 100 MB/秒になります。 同様に、サイズが 400 GiB のディスクは P20 に分類されるので、最大 2300 IOPS を実行でき、スループットは最大 150 MB/秒になります。
  
> [!NOTE]
> 既存のディスクのサイズを簡単に増やすことができます。 たとえば、30 GB のディスクのサイズを 128 GB または 1 TB に増やすことができます。 また、容量を増やしたり、IOPS とスループットを向上させたりする必要がある場合は、P20 ディスクを P30 ディスクに変換することもできます。 
> 
 
* **IO サイズ**: 入力/出力 (I/O) ユニット サイズは 256 KB です。 転送されるデータが 256 KB に満たない場合は、単一の I/O ユニットとみなされます。 I/O のサイズが大きくなると、サイズが 256 KB の複数の I/O としてカウントされます。 たとえば、1100 KB I/O は 5 つの I/O ユニットとしてカウントされます。

* **スループット**: スループットの制限には、ディスクへの書き込みと、キャッシュからではなく、そのディスクからの読み取りが含まれます。 たとえば、P10 ディスクのスループットはディスクあたり 100 MB/秒です。 P10 ディスクの有効なスループットの例を以下にいくつか示します。

| P10 ディスクあたりの最大スループット | ディスクからの非キャッシュの読み取り | ディスクからの非キャッシュの書き込み |
| --- | --- | --- |
| 100 MB/秒 | 100 MB/秒 | 0 |
| 100 MB/秒 | 0 | 100 MB/秒 |
| 100 MB/秒 | 60 MB/秒 | 40 MB/秒 |

* **キャッシュ ヒット**: キャッシュ ヒットには、ディスクの割り当て済みの IOPS やスループットによる制限はありません。 たとえば、Premium Storage でサポートされる VM でキャッシュを読み取り専用に設定したデータ ディスクを使用する場合、そのキャッシュからの読み取りは、ディスクの IOPS とスループットの上限の対象にはなりません。 そのため、ワークロードの大部分が読み取りの場合、ディスクから非常に高いスループットが得られます。 キャッシュには、VM サイズに基づいて、VM レベルで IOPS とスループットの制限が個別に適用されます。 DS シリーズ VM は、IOPS が約 4000 で、キャッシュとローカル SSD の IO がコアあたり 33 MB/秒です。 GS シリーズ VM の制限は、IOPS が 5000 で、キャッシュとローカル SSD の IO がコアあたり 50 MB/秒です。 

## <a name="throttling"></a>調整
アプリケーションの IOPS またはスループットが Premium Storage ディスクに割り当てられている制限を超えた場合や、VM の全ディスクのディスク トラフィックの合計がその VM で使用できるディスク帯域幅の上限を超えた場合、調整が行われることがあります。 調整を回避するために、プロビジョニングしたディスクのスケーラビリティとパフォーマンスのターゲット、および VM で使用可能なディスク帯域幅に基づいて、ディスクの保留中の I/O 要求の数を制限することをお勧めします。  

調整を回避できれば、アプリケーションの待ち時間を最短にすることができます。 一方で、ディスクの保留中の I/O 要求の数が少なすぎる場合は、ディスクで利用可能な最大 IOPS とスループット レベルのメリットを活用できません。

次の例は、スロットル レベルの計算方法を示しています。 すべての計算は、256 KB の I/O ユニット サイズに基づいて行われます。

### <a name="example-1"></a>例 1:
アプリケーションは、P10 ディスク上で、1 秒間に 16 KB サイズの I/O ユニットを 495 回実行しました。 これらは、1 秒間あたり (IOPS) 495 I/O ユニットとしてカウントされます。 同じ 1 秒内に 2 MB の I/O を実行すると、I/O の合計ユニットは 495 + 8 となります。 これは、I/O ユニット サイズが 256 KB であることから、2 MB の I/O は 2048 KB / 256 KB = 8 I/O ユニットとなるためです。 合計の 495 + 8 はディスク制限の 500 IOPS を超過するため、調整が発生します。

### <a name="example-2"></a>例 2:
アプリケーションが P10 ディスク上で、256 KB サイズの I/O ユニットを 400 回実行しました。 合計消費帯域幅は、(400 * 256) / 1024 = 100 MB/秒になります。 P10 ディスクには、100 MB/秒というスループット制限があります。 アプリケーションが 1 秒間あたりにより多くの I/O を実行しようとすると、割り当てられた制限を超過してしまうため、調整が発生します。

### <a name="example-3"></a>例 3:
DS4 VM に P30 ディスクが 2 つアタッチされています。 P30 ディスク 1 つあたりのスループットは 200 MB/秒です。 しかし、DS4 VM のディスク帯域幅合計は 256 MB/秒が上限です。 したがって、この DS4 VM では、アタッチされたディスクの両方が最大スループットを発揮することはできません。 このことを解決するには、一方のディスクのトラフィックを 200 MB/秒で維持し、他方では 56 MB/秒で維持します。 ディスク トラフィックの合計が 256 MB/秒を超えると、ディスク トラフィックの調整が行われます。

> [!NOTE]
> ディスク トラフィックの大部分が小さなサイズの I/O で構成されていると、アプリケーションがスループット制限に達する前に、IOPS 制限に達してしまう場合がほとんどです。 逆に、ディスク トラフィックの大部分が大きなサイズの I/O で構成されている場合は、IOPS 制限ではなくスループット制限に達してしまうことがほとんどです。 最適なサイズの I/O を使用し、ディスクの保留中の I/O 要求の数を制限することで、アプリケーションの IOPS とスループットを最大化できます。
> 

Premium Storage を使用した高パフォーマンスのための設計について詳しくは、「 [Premium Storage を使用した高パフォーマンスのための設計](storage-premium-storage-performance.md)」を参照してください。

## <a name="snapshots-and-copy-blob"></a>スナップショットと Copy BLOB

Storage サービスでは、VHD ファイルはページ BLOB です。 ページ BLOB のスナップショットを作成し、別の場所 (別のストレージ アカウントなど) にコピーできます。

### <a name="unmanaged-disks"></a>非管理対象ディスク

Standard Storage でスナップショットを使用する場合と同様に、非管理対象 Premium ディスクの[増分スナップショット](storage-incremental-snapshots.md)を作成できます。 Premium Storage はレプリケーション オプションとしてローカル冗長ストレージ (LRS) のみをサポートするため、スナップショットを作成し、そのスナップショットを geo 冗長 Standard Storage アカウントにコピーすることをお勧めします。 詳細については、「 [Azure Storage 冗長オプション](storage-redundancy.md)」をご覧ください。

ディスクが VM に接続されている場合、ディスクで特定の API 操作を実行できなくなります。 たとえば、ディスクが VM にアタッチされている間は、その BLOB に対して [Copy Blob](/rest/api/storageservices/fileservices/Copy-Blob) 操作を実行できません。 代わりに、[Snapshot Blob](/rest/api/storageservices/fileservices/Snapshot-Blob) REST API メソッドを使って BLOB のスナップショットを作成してから、スナップショットの [Copy Blob](/rest/api/storageservices/fileservices/Copy-Blob) を実行してアタッチされたディスクをコピーします。 または、ディスクの接続を解除してから必要な操作を実行します。

Premium Storage BLOB スナップショットには次の制限が適用されます。

| Premium Storage の制限 | 値 |
| --- | --- |
| 最大 スナップショット数 (BLOB あたり) | 100 |
| スナップショットのストレージ アカウントの容量 (スナップショットのデータのみを含み、ベース BLOB のデータは含まない) | 10 TB |
| 最小 最小時間間隔 (連続したスナップショット) | 10 分 |

スナップショットの地理冗長コピーを維持するには、AzCopy か Copy Blob を使用して、Premium Storage アカウントから地理冗長 Standard Storage アカウントにスナップショットをコピーできます。 詳しくは、「[AzCopy コマンド ライン ユーティリティを使用してデータを転送する](storage-use-azcopy.md)」および「[BLOB のコピー](/rest/api/storageservices/fileservices/Copy-Blob)」をご覧ください。

Premium Storage アカウントでページ BLOB に対して REST 操作を実行する方法の詳細については、「[Using Blob Service Operations with Azure Premium Storage (Azure Premium Storage での Blob service 操作の使用)](http://go.microsoft.com/fwlink/?LinkId=521969)」をご覧ください。

### <a name="managed-disks"></a>管理ディスク

管理ディスクのスナップショットは管理ディスクの読み取り専用コピーであり、Standard 管理ディスクとして保存されます。 Managed Disks では、[増分スナップショット](storage-incremental-snapshots.md)は現在サポートされていませんが、今後サポートされる予定です。 管理ディスクのスナップショットを作成する方法については、[Windows で管理スナップショットを使用して Azure Managed Disk として保存された VHD のコピーを作成する方法](../virtual-machines/virtual-machines-windows-snapshot-copy-managed-disk.md)に関する記事と、[Linux で管理スナップショットを使用して Azure Managed Disk として保存された VHD のコピーを作成する方法](../virtual-machines/linux/virtual-machines-linux-snapshot-copy-managed-disk.md)に関する記事をご覧ください。

管理ディスクが VM に接続されている場合、ディスクで特定の API 操作を実行できなくなります。 たとえば、ディスクが VM に接続されているときには、Shared Access Signature (SAS) を生成してコピー操作を実行することはできません。 代わりに、ディスクのスナップショットを作成してから、スナップショットのコピーを実行します。 または、ディスクの接続を解除してから、Shared Access Signature (SAS) を生成してコピー操作を実行します。


## <a name="using-linux-vms-with-premium-storage"></a>Linux VM とともに Premium Storage を使用する
Premium Storage で Linux VM を構成するときは、次の重要な手順を参照してください。

* すべての Premium Storage ディスクのキャッシュ設定が "ReadOnly" または "None" に設定されている場合、Premium Storage のスケーラビリティ ターゲットを達成するために、ファイル システムをマウントするときに "バリア" を無効にする必要があります。 これらのキャッシュ設定では Premium Storage ディスクへの書き込みの耐久性が保証されるので、このシナリオではバリアは不要です。 書き込み要求が正常に完了した時点で、データは永続的なストアに書き込まれた状態になっています。 お使いのファイル システムに応じて、次のいずれかの方法を使用して "バリア" を無効にしてください。
  
* **reiserFS**: バリアを無効にするには、マウント オプション "barrier=none" を使用します (バリアを有効にするには "barrier=flush" を使用します)。
* **ext3/ext4**: バリアを無効にするには、マウント オプション "barrier=0" を使用します (バリアを有効にするには "barrier=1" を使用します)。
* **XFS**: バリアを無効にするには、マウント オプション "nobarrier" を使用します (バリアを有効にするには "barrier" を使用します)。
* Premium Storage ディスクのキャッシュ設定が "ReadWrite" の場合は、書き込みの耐久性を保証するためにバリアを有効にしてください。
* VM を再起動してもボリューム ラベルが変更されないようにするには、ディスクに対する UUID 参照で /etc/fstab を更新する必要があります。 「[Linux VM に管理ディスクを追加する](../virtual-machines/virtual-machines-linux-add-disk.md)」もご覧ください。

次に示す Linux ディストリビューションは、Premium Storage での検証が完了しています。 Premium Storage を使用するときのパフォーマンスと安定性を高めるために、VM をこれらのバージョン (以降) の少なくとも 1 つにアップグレードすることをお勧めします。 また、バージョンによっては最新の Linux Integration Services (LIS) v4.0 for Microsoft Azure が必要になります。 下記のリンクからダウンロードとインストールを行ってください。 マイクロソフトは今後もイメージの検証を行い、この一覧に追加していきます。 これらのイメージのパフォーマンスは変動することが検証時に判明しています。また、ワークロードの特性とイメージの設定にも依存することにご注意ください。 ワークロードの種類に応じて、異なるイメージをチューニングします。

| ディストリビューション | バージョン | サポートされるカーネル | 詳細 |
| --- | --- | --- | --- |
| Ubuntu | 12.04 | 3.2.0-75.110+ | Ubuntu-12_04_5-LTS-amd64-server-20150119-en-us-30GB |
| Ubuntu | 14.04 | 3.13.0-44.73+ | Ubuntu-14_04_1-LTS-amd64-server-20150123-en-us-30GB |
| Debian | 7.x、8.x | 3.16.7-ckt4-1+ | &nbsp; |
| SUSE | SLES 12| 3.12.36-38.1+| suse-sles-12-priority-v20150213 <br> suse-sles-12-v20150213 |
| SUSE | SLES 11 SP4 | 3.0.101-0.63.1+ | &nbsp; |
| CoreOS | 584.0.0+| 3.18.4+ | CoreOS 584.0.0 |
| CentOS | 6.5、6.6、6.7、7.0 | &nbsp; | [LIS4 が必須 ](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) <br> "*下記の注参照*" |
| CentOS | 7.1+ | 3.10.0-229.1.2.el7+ | [LIS4 が推奨 ](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) <br> "*下記の注参照*" |
| RHEL | 6.8+、7.2+ | &nbsp; | &nbsp; |
| Oracle | 6.0+、7.2+ | &nbsp; | UEK4 または RHCK |
| Oracle | 7.0-7.1 | &nbsp; | UEK4 または RHCK と [LIS 4.1 +](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) |
| Oracle | 6.4-6.7 | &nbsp; | UEK4 または RHCK と [LIS 4.1 +](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) |


### <a name="lis-drivers-for-openlogic-centos"></a>Openlogic Centos 用 LIS ドライバー

OpenLogic CentOS VM を実行しているお客様は、次のコマンドを実行して最新のドライバーをインストールする必要があります。

```
sudo rpm -e hypervkvpd  ## (may return error if not installed, that's OK)
sudo yum install microsoft-hyper-v
```

その後再起動して、新しいドライバーをアクティブ化する必要があります。

## <a name="pricing-and-billing"></a>価格と課金

Premium Storage を使用するときには、課金に関する次の考慮事項が適用されます。

* Premium Storage のディスク/BLOB サイズ
* Premium Storage のスナップショット
* 送信データ転送

**Premium Storage のディスク/BLOB サイズ**: Premium Storage ディスク/BLOB への課金は、ディスク/BLOB のプロビジョニング済みサイズによって異なります。 Azure は、プロビジョニング済みサイズ (切り上げたもの) を、「[Premium Storage のスケーラビリティとパフォーマンスのターゲット](#premium-storage-scalability-and-performance-targets)」にある表に記載されているオプションの中で最も近い Premium Storage ディスク オプションにマップします。 各ディスクは、サポートされているプロビジョニング済みサイズのいずれかにマップされ、それに応じて課金されます。 プロビジョニングされたディスクには、Premium Storage プランの月額料金を使用して、時間割りで計算して課金されます。 たとえば、P10 ディスクをプロビジョニングし、20 時間後にそのディスクを削除した場合は、P10 製品の 20 時間分に対して課金されます。 これは、実際にディスクに書き込まれたデータの量や、使用した IOPS/スループットには関係ありません。

**Premium 非管理対象ディスクのスナップショット**: Premium 非管理対象ディスクでのスナップショットについては、スナップショットで使用された追加の容量に対して課金されます。 スナップショットの詳細については、「 [BLOB のスナップショットの作成](/rest/api/storageservices/fileservices/Snapshot-Blob)」をご覧ください。

**Premium 管理ディスクのスナップショット**: 管理ディスクのスナップショットはディスクの読み取り専用コピーであり、Standard 管理ディスクとして保存されます。 スナップショットのコストは Standard 管理ディスクと同じです。 たとえば、128 GB の Premium 管理ディスクのスナップショットを作成した場合、スナップショットのコストは 128 GB の Standard 管理ディスクと同等になります。  

**送信データ転送**: [送信データ転送](https://azure.microsoft.com/pricing/details/data-transfers/) (Azure データ センターから送信されるデータ) では、帯域幅の使用量に対して課金されます。

Premium Storage、Premium Storage でサポートされる VM、Managed Disks の価格の詳細については、次のページをご覧ください。

* [Azure Storage の料金](https://azure.microsoft.com/pricing/details/storage/)
* [Virtual Machines の料金](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Managed Disks の価格](https://azure.microsoft.com/pricing/details/managed-disks/)

## <a name="azure-backup-service-support"></a>Azure Backup サービスのサポート 

地域的な障害復旧では、[Azure Backup サービス](../backup/backup-introduction-to-azure-backup.md)と GRS ストレージ アカウントをバックアップ コンテナーとして使用して VM ディスクを別のリージョンにバックアップする必要があります。

非管理対象ディスクと Managed Disks の両方で Azure Backup サービスを使用して、時間ベースのバックアップ、VM の簡易復元、バックアップ リテンション期間ポリシーを適用したバックアップ ジョブを作成することもできます。 これについて詳しくは、[Managed Disks での VM に対する Azure Backup サービスの使用](../backup/backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup)と[非管理対象ディスクでの VM に対する Azure Backup サービスの使用](../backup/backup-azure-vms-first-look-arm.md)に関するページをご覧ください 

## <a name="next-steps"></a>次のステップ
Azure Premium Storage の詳細については、以下の記事を参照してください。

### <a name="design-and-implement-with-azure-premium-storage"></a>Design and implement with Azure Premium Storage (Azure Premium Storage での設計と実装)
* [Premium Storage を使用した高パフォーマンスのための設計](storage-premium-storage-performance.md)
* [Azure Premium Storage での BLOB サービス操作の使用](http://go.microsoft.com/fwlink/?LinkId=521969)

### <a name="operational-guidance"></a>操作ガイダンス
* [Azure Premium Storage への移行](storage-migration-to-premium-storage.md)

### <a name="blog-posts"></a>ブログ記事
* [Azure Premium Storage を一般に提供](https://azure.microsoft.com/blog/azure-premium-storage-now-generally-available-2/)
* [Announcing the GS-Series: Adding Premium Storage Support to the Largest VMs in the Public Cloud (GS シリーズの提供開始を発表: パブリック クラウドの最大の VM に Premium Storage サポートを追加)](https://azure.microsoft.com/blog/azure-has-the-most-powerful-vms-in-the-public-cloud/)

