---
title: SAP ワークロードの Azure Storage の種類
description: SAP ワークロードの Azure Storage の種類に関する計画
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/23/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 668f8ffdc4b797219dc1f3c23fecb858d8f706ad
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88510863"
---
# <a name="azure-storage-types-for-sap-workload"></a>SAP ワークロードの Azure Storage の種類
Azure には、機能、スループット、待機時間、価格が大幅に異なるさまざまな種類のストレージがあります。 ストレージの種類の中には、SAP シナリオでは使用できないものや、制限付きで使用できるものがあります。 一方、いくつかの Azure Storage の種類が、特定の SAP ワークロードのシナリオ用として適切であるか、または最適化されています。 特に SAP HANA に関して、一部の Azure Storage の種類は SAP HANA での使用の認定を受けています。 このドキュメントでは、さまざまな種類のストレージを取り上げて、SAP ワークロードと SAP コンポーネントに対する機能と使用可能性について説明します。

この記事全体を通して使用する単位について解説します。 パブリック クラウド ベンダーは、ギガバイトまたはテラバイトではなく、GiB ([ギビバイト](https://en.wikipedia.org/wiki/Gibibyte)) または TiB ([テビバイト](https://en.wikipedia.org/wiki/Tebibyte)) を使用するように移行しました。 そのため、Azure のすべてのドキュメントと価格で、これらの単位を使用しています。  このドキュメント全体を通して、サイズの単位として MiB、GiB、TiB という単位だけを参照します。 MB、GB、TB を使用して計画することが必要な場合もあります。 そのため、250 MiB/秒のスループットではなく、400 MiB/秒のスループットのサイズを指定する必要がある場合は、計算に少し違いがあることにご注意ください。

## <a name="microsoft-azure-storage-resiliency"></a>Microsoft Azure Storage の回復性

Standard HDD、Standard SSD、Azure Premium Storage、Ultra Disk からなる Microsoft Azure Storage では、ベース VHD (OS を含む) と VM にアタッチされたデータ ディスクまたは VHD が、3 つの異なるストレージ ノード上の 3 つのコピーに保持されます。 記憶域ノード障害が発生した場合の、別のレプリカへのフェールオーバーと新しいレプリカのシード処理は透過的です。 この冗長性の結果、複数の Azure ディスクにわたるどのような種類のストレージ冗長レイヤーも使用する必要は**ありません**。 これは、ローカル冗長ストレージ (LRS) と呼ばれます。 LRS は、Azure 内のこれらの種類のストレージの既定になっています。 [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) では、他のネイティブ Azure Storage と同じ SLA を実現する十分な冗長性が提供されます。

この他に、Azure で提供されるさまざまなストレージの種類の一部に適用されるいくつかの冗長化の方法があり、それらはすべて [Azure Storage のレプリケーション](../../../storage/common/storage-redundancy.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)に関する記事で説明されています。 

### <a name="azure-managed-disks"></a>Azure Managed Disks

マネージド ディスクは Azure Resource Manager のリソースの種類で、Azure ストレージ アカウントに格納されている VHD の代わりに使用できます。 マネージド ディスクはアタッチ先の仮想マシンの [「可用性セット」][virtual-machines-manage-availability] に自動的に配置され、それによって仮想マシンと仮想マシンで実行されているサービスの可用性が向上します。 詳細については、[概要についての記事](../../windows/managed-disks-overview.md)を参照してください。

回復性に関連して、マネージド ディスクの利点を次の例で示します。

- Azure 可用性セットに SAP システム用の DBMS VM を 2 台デプロイするとします。 
- Azure で VM がデプロイされるとき、OS イメージが含まれているディスクは別のストレージ クラスターに配置されます。 これにより、1 つの Azure ストレージ クラスターの問題によって両方の VM が影響を受けることを回避できます。
- これらの VM に割り当ててデータベースのデータとログ ファイルを格納するための新しいマネージド ディスクを作成すると、2 台の VM のこれらの新しいディスクも別々のストレージ クラスターにデプロイされるため、最初の VM のどのディスクも、2 番目の VM のディスクとストレージ クラスターを共有しません。

マネージド ディスクを使用せずにユーザー定義のストレージ アカウントにデプロイする場合、ディスク割り当ては恣意的であり、回復性を目的として AvSet 内に VM がデプロイされるという事実は意識されません。

> [!NOTE]
> この理由と、マネージド ディスクのみで使用可能なその他いくつかの機能強化により、ディスク用に Azure ブロック ストレージ (Azure NetApp Files を除くすべての Azure ストレージ) を使用する VM の新しいデプロイでは、ベース VHD または OS ディスク用、SAP データベース ファイルを含むデータ ディスク用に、Azure マネージド ディスクを使用する必要があります。 VM を可用性セット内にデプロイするか、可用性ゾーン間にデプロイするか、セットやゾーンを使用せずにデプロイするかは関係ありません。 バックアップを格納する目的に使用するディスクは、必ずしもマネージド ディスクである必要はありません。

> [!NOTE]
> Azure Managed Disks ではローカル冗長 (LRS) のみが提供されます。 


## <a name="storage-scenarios-with-sap-workloads"></a>SAP ワークロードでのストレージ シナリオ
Azure にデプロイするスタックのさまざまなコンポーネント内の SAP ワークロードで、永続化されたストレージが必要です。 このようなシナリオとして、少なくとも次のようなものがあります。

- VM のベース VHD (そのディスクにインストールしたオペレーティング システムとその他のソフトウェアを保持する) を永続化します。 このディスクまたは VHD は、VM のルートです。 それに対して加えられた変更は、すべて永続化される必要があります。 そうすることで、次に VM を停止して再起動したときに、それまでに加えられたすべての変更が引き続き存在しています。 VM が最初に実行されていたものとは別のホストに Azure によってデプロイされる場合、特にそうです。
- 永続化されたデータ ディスク。 これらのディスクは、アプリケーション データを格納するためにアタッチする VHD です。 このアプリケーション データは、データベースのデータおよびログ (または再実行) ファイル、バックアップ ファイル、またはソフトウェア インストールである可能性があります。 オペレーティング システムを保持するベース VHD 以外の任意のディスクを意味します。
- NetWeaver または S/4HANA のグローバル トランスポート ディレクトリを含むファイル共有または共有ディスク。 これらの共有の内容は、複数の VM で実行されているソフトウェアによって使用されるか、高可用性フェールオーバー クラスターのシナリオを構築するために使用されます。
- EDI プロセスや類似するもの用の、/sapmnt ディレクトリまたは一般的なファイル共有。 これらの共有の内容は、複数の VM で実行されているソフトウェアによって使用されるか、高可用性フェールオーバー クラスターのシナリオを構築するために使用されます。

次のいくつかのセクションで、上記の 4 つのシナリオに適用されるさまざまな Azure Storage の種類と、SAP ワークロードに対するそれらの使用可能性について説明します。 さまざまな種類の Azure Storage の用途を表す一般的な分類については、「[Azure で利用できるディスクの種類](../../linux/disks-types.md)」を参照してください。 SAP ワークロードに対して使用する Azure Storage の種類が違っても、推奨事項が大きく異なることはありません。

S/4HANA の SAP NetWeaver またはアプリケーション レイヤー用の Azure Storage の種類に関するサポートの制限については、[SAP サポート ノート 2015553](https://launchpad.support.sap.com/#/notes/2015553) を参照してください。SAP HANA 認定のサポート対象 Azure Storage の種類については、「[SAP HANA Azure 仮想マシンのストレージ構成](./hana-vm-operations-storage.md)」を参照してください。

さまざまな Azure Storage の種類を説明するセクションでは、SAP でサポートされているストレージを使用した場合の制限事項と可能性について、追加の背景情報を提供します。 

## <a name="storage-recommendations-for-sap-storage-scenarios"></a>SAP ストレージ シナリオのストレージに関する推奨事項
詳細に進む前に、ドキュメントの冒頭にあらかじめ概要と推奨事項を示します。 その一方このドキュメントでは、このセクションの後に特定の種類の Azure Storage に関する詳細を示します。 SAP ストレージ シナリオのストレージに関する推奨事項をまとめて表にすると、次のようになります。

| 使用シナリオ | Standard HDD | Standard SSD | Premium Storage | Ultra Disk | Azure NetApp Files |
| --- | --- | --- | --- | --- | --- |
| OS ディスク | 不適合 |  制限付き適合 (非運用) | 推奨 | 不可 | 不可 |
| グローバル トランスポート ディレクトリ | サポート対象外 | サポート対象外 | 推奨 | 推奨 | 推奨 |
| /sapmnt | 不適合 | 制限付き適合 (非運用) | 推奨 | 推奨 | 推奨 |
| DBMS データ ボリューム (SAP HANA) M または Mv2 VM ファミリ | サポート対象外 | サポート対象外 | 推奨 | 推奨 | 推奨<sup>2</sup> |
| DBMS ログ ボリューム (SAP HANA) M または Mv2 VM ファミリ | サポート対象外 | サポート対象外 | 推奨<sup>1</sup> | 推奨 | 推奨<sup>2</sup> | 
| DBMS データ ボリューム (SAP HANA) Esv3 または Edsv4 VM ファミリ | サポート対象外 | サポート対象外 | 推奨 | 推奨 | 推奨<sup>2</sup> |
| DBMS ログ ボリューム (SAP HANA) Esv3 または Edsv4 VM ファミリ | サポート対象外 | サポート対象外 | サポート対象外 | 推奨 | 推奨<sup>2</sup> | 
| DBMS データ ボリューム (HANA 以外) | サポート対象外 | 制限付き適合 (非運用) | 推奨 | 推奨 | サポート対象外 |
| DBMS ログ ボリューム (HANA 以外) M または Mv2 VM ファミリ | サポート対象外 | 制限付き適合 (非運用) | 推奨<sup>1</sup> | 推奨 | サポート対象外 |
| DBMS ログ ボリューム (HANA 以外) M または Mv2 以外の VM ファミリ | サポート対象外 | 制限付き適合 (非運用) | 中規模までのワークロードに適合 | 推奨 | サポート対象外 |


<sup>1</sup> ログまたは再実行ログ ボリューム用の M または Mv2 VM ファミリでは [Azure 書き込みアクセラレータ](../../how-to-enable-write-accelerator.md)を使用します。<sup>2</sup> ANF を使用するには ANF 上に /hana/log に加えて /hana/data も必要です。 

さまざまな種類のストレージに期待できる特性は、次のようになります。

| 使用シナリオ | Standard HDD | Standard SSD | Premium Storage | Ultra Disk | Azure NetApp Files |
| --- | --- | --- | --- | --- | --- |
| スループットまたは IOPS SLA | no | no | はい | はい | はい |
| 読み取り待機時間 | high | 中から高 | low | ミリ秒未満 | ミリ秒未満 |
| 書き込み待機時間 | high | 中から高  | 低 (ミリ秒未満<sup>1</sup>) | ミリ秒未満 | ミリ秒未満 |
| HANA のサポート対象 | no | no | はい<sup>1</sup> | はい | はい |
| ディスクのスナップショット可能 | はい | はい | はい | no | はい |
| 可用性セット使用時の異なるストレージ クラスターへのディスクの割り当て | マネージド ディスクを使用 | マネージド ディスクを使用 | マネージド ディスクを使用 | 可用性セットを使用してデプロイした VM ではサポートされていないディスクの種類 | いいえ<sup>3</sup> |
| Availability Zones を使用した配置 | はい | はい | はい | はい | Microsoft の関与が必要 |
| ゾーン冗長 | マネージド ディスク非対応 | マネージド ディスク非対応 | マネージド ディスク非対応 | no | no |
| geo 冗長 | マネージド ディスク非対応 | マネージド ディスク非対応 | no | no | no |


<sup>1</sup> ログまたは再実行ログ ボリューム用の M または Mv2 VM ファミリでは [Azure 書き込みアクセラレータ](../../how-to-enable-write-accelerator.md)を使用します。

<sup>2</sup> コストはプロビジョニングされた IOPS とスループットに依存します。

<sup>3</sup> 異なる ANF 容量プールを作成しても、異なるストレージ ユニットに容量プールがデプロイされる保証はありません。


> [!IMPORTANT]
> Azure NetApp Files (ANF) を使用して 1 ミリ秒未満の I/O 待機時間を実現するには、Microsoft と協力して、VM と ANF に基づく NFS 共有の間の配置を正しく調整する必要があります。 これまでのところ、デプロイされた VM と ANF でホストされている NFS ボリュームとの間に、自動的に近接を提供するメカニズムはありません。 異なる Azure リージョンの設定が異なると、VM と NFS 共有が近接して割り当てられていない場合、ネットワーク待機時間が追加されることで、I/O 待機時間が 1 ミリ秒を超える可能性があります。


> [!IMPORTANT]
> 現在提供されている Azure ブロック ストレージ ベースのいずれのマネージド ディスクでも、または Azure NetApp Files でも、ゾーンまたは地理冗長は提供されません。 そのため、高可用性とディザスター リカバリーのアーキテクチャは、これらのマネージド ディスク、NFS、または SMB 共有に関して、いずれの種類の Azure ネイティブ ストレージ レプリケーションにも依存しないようにする必要があります。


## <a name="azure-premium-storage"></a>Azure Premium Storage
Azure Premium SSD は、以下のことを実現する目的で導入されました。

* 少ない I/O 待機時間
* IOPS とスループットの SLA
* I/O 待機時間の変動の低減

この種類のストレージは、DBMS ワークロード、数ミリ秒台という短い待機時間が求められるストレージ トラフィック、IOPS に基づく SLA をターゲットにしています。Azure Premium Storage の場合のスループット コストの基準は、このようなディスクに格納されている実際のデータ量ではなく、このようなディスクのサイズ カテゴリであり、ディスク内に格納されているデータの量は関係ありません。 なお、Premium Storage では、「[Premium SSD](../../linux/disks-types.md#premium-ssd)」の記事に示されているサイズ カテゴリに直接マップされないディスクを作成することもできます。 この記事からの結論は次のとおりです。

- ストレージは範囲別に編成されています。 たとえば、容量が 513 GiB から 1024 GiB までの範囲にあるディスクでは、同じ機能と同じ月額コストが共有されます。
- GiB あたりの IOPS は、サイズ カテゴリ間で線形になりません。 32 GiB より小さいディスクの場合、GiB あたりの IOPS レートが高くなります。 32 GiB を超えて 1024 GiB までのディスクの場合、GiB あたりの IOPS レートは、GiB あたり 4 から 5 IOPS です。 32,767 GiB までの大容量ディスクになると、GiB あたりの IOPS レートは 1 を下回るようになります。
- このストレージの I/O スループットは、ディスク カテゴリのサイズに対して線形ではありません。 容量 65 GiB と 128 GiB の間のカテゴリのような小容量ディスクの場合、スループットは約 780 KB/GiB です。 一方、32,767 GiB ディスクのような非常に大容量のディスクでは、スループットは約 28 KB/GiB です。
- ディスクの容量を変更せずに IOPS とスループットの SLA を変更することはできません。

Azure には、Azure Premium ストレージまたは Azure Ultra Disk ストレージの使用に関連付けられた、99.9% の単一インスタンス VM SLA があります。 その SLA は、「[仮想マシンの SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)」に記載されています。 この単一 VM SLA に準拠するには、ベース VHD ディスクと、アタッチされている**すべての**ディスクが、Azure Premium ストレージまたは Azure Ultra Disk ストレージのいずれかである必要があります。

SAP ワークロードの機能マトリックスは次のようになります。

| 機能| 解説| 注またはリンク | 
| --- | --- | --- | 
| OS ベース VHD | 適合 | すべてのシステム |
| データ ディスク | 適合 | すべてのシステム - [特に SAP HANA](../../how-to-enable-write-accelerator.md) |
| SAP グローバル トランスポート ディレクトリ | YES | [サポートされています](https://launchpad.support.sap.com/#/notes/2015553) |
| SAP sapmnt | 適合 | すべてのシステム |
| バックアップ ストレージ | 適合 | 短期的なバックアップの保存 |
| ファイル共有または共有ディスク | 利用不可 | Azure Premium Files またはサード パーティが必要 |
| 回復性 | LRS | ディスクに GRS または ZRS は利用不可 |
| Latency | 低から中 | - |
| IOPS SLA | YES | - |
| IOPS は容量に対して線形 | ブラケット内で半直線  | [Managed Disks の価格](https://azure.microsoft.com/pricing/details/managed-disks/) |
| ディスクあたりの最大 IOPS | 20,000 ([ディスク サイズによって異なる](https://azure.microsoft.com/pricing/details/managed-disks/)) | [VM の制限](../../sizes.md)も考慮すること |
| スループット SLA | YES | - |
| スループット (容量に対して線形) | ブラケット内で半直線 | [Managed Disks の価格](https://azure.microsoft.com/pricing/details/managed-disks/) |
| HANA 認定 | YES | [特に SAP HANA](../../how-to-enable-write-accelerator.md) |
| ディスクのスナップショット可能 | YES | - |
| Azure Backup VM スナップショット可能 | YES | [書き込みアクセラレータ](../../how-to-enable-write-accelerator.md)によってキャッシュされたディスクを除く  |
| コスト | MEDIUM | - |

Azure Premium Storage では、Azure Premium Storage で提供される一般的なキャッシュの種類を使用して SAP HANA ストレージの待機時間 KPI が満たされることはありません。 SAP HANA ログ書き込みのストレージ待機時間 KPI を満たすためには、「[書き込みアクセラレータを有効にする](../../how-to-enable-write-accelerator.md)」の説明に従って、Azure 書き込みアクセラレータのキャッシュを使用する必要があります。 Azure 書き込みアクセラレータによって他のすべての DBMS システムに、トランザクション ログの書き込みと再実行ログの書き込みに関するメリットがもたらされます。 そのため、すべての SAP DBMS デプロイで使用することをお勧めします。 SAP HANA の場合、Azure 書き込みアクセラレータを Azure Premium Storage と組み合わせて使用することが必須です。



**概要:** Azure Premium Storage は、SAP ワークロード用に推奨される Azure Storage の種類の 1 つです。 この推奨は、運用だけでなく非運用システムにも適用されます。 Azure Premium Storage は、データベースのワークロードを処理するのに適しています。 Azure 書き込みアクセラレータの使用により、Azure Premium ディスクに対する書き込み待機時間が大幅に短縮されます。 ただし、IOPS とスループット レートが高い DBMS システムの場合は、ストレージ容量を過剰にプロビジョニングするか、Windows 記憶域スペースや Linux の論理ボリューム マネージャーなどの機能を使用して、必要な容量を提供するだけでなく、必要な IOPS またはスループットを最良のコスト効率で提供するストライプ セットを構築する必要があります。


### <a name="azure-burst-functionality-for-premium-storage"></a>Premium Storage の Azure バースト機能
容量が 512 GiB 以下の Azure Premium Storage ディスクの場合、バースト機能が提供されます。 ディスク バーストの動作の詳細については、「[ディスク バースト](../../linux/disk-bursting.md)」をご覧ください。 この記事を読むと、I/O ワークロードがディスクの公称 IOPS およびスループットを下回っているときの、IOPS とスループットの蓄積の概念を理解できます (公称スループットの詳細については、「[Managed Disks の価格](https://azure.microsoft.com/pricing/details/managed-disks/)」をご覧ください)。 現在の使用量とディスクの公称値との差分だけ、IOPS とスループットが蓄積されます。 バーストは最大 30 分に制限されています。

このバースト機能を計画できる理想的なケースとして、別の DBMS のデータ ファイルを含むボリュームまたはディスクが考えられます。 特に、小規模から中規模のシステムで、これらのボリュームに対して予想される I/O ワークロードは次のとおりです。

- データはメモリにキャッシュされるか、HANA のように完全にメモリ内で処理されるのが理想的であるため、低から中程度の読み取りワークロード
- 定期的に発行されるデータベースのチェックポイントまたはセーブポイントによってトリガーされる書き込みのバースト
- ストレージ スナップショットを使用してバックアップが実行されない場合に、連続ストリームを読み取るバックアップ ワークロード
- SAP HANA の場合、インスタンスの再起動後のメモリへのデータの読み込み

特に、ワークロードが 1 秒あたり数百個のトランザクションしか処理しない小規模な DBMS システムでは、トランザクション ログまたは再実行ログを格納するディスクまたはボリュームでも、このようなバースト機能は意味があります。 このようなディスクまたはボリュームに対して予想されるワークロードは次のとおりです。

- アプリケーションによって発行されるすべてのコミットによって I/O 操作がトリガーされる可能性が高いため、ワークロードとワークロードの性質に依存するディスクへの定期的な書き込み
- インデックスの作成や再構築などの運用タスクでのスループットの高いワークロード
- トランザクション ログまたは再実行ログのバックアップを実行するときの読み取りバースト


## <a name="azure-ultra-disk"></a>Azure Ultra Disk
Azure Ultra Disk では、Azure IaaS VM 用に高スループット、高 IOPS、一貫性のある低待機時間のディスク ストレージが提供されます。 Ultra Disk のその他のメリットとして、仮想マシン (VM) を再起動する必要なしに、ワークロードに合わせてディスクの IOPS とスループットを動的に変更できます。 Ultra Disk は、SAP DBMS ワークロードなどのデータ量の多いワークロードに適しています。 Ultra ディスクはデータ ディスクとしてのみ使用でき、オペレーティング システムを格納するベース VHD ディスクとしては使用できません。 Azure Premium Storage をベース VHD ディスクとして使用することをお勧めします。

Ultra ディスクを作成するとき、次の 3 つのディメンションを定義できます。

- ディスクの容量。 範囲は 4 GiB から 65,536 GiB までです。
- ディスクに対してプロビジョニングされた IOPS。 ディスク容量には、さまざまな最大値が適用されます。 詳細については、「[Ultra Disk](../../linux/disks-types.md#ultra-disk)」の記事を参照してください。
- プロビジョニングされたストレージ帯域幅。 ディスクの容量に応じて、さまざまな最大帯域幅が適用されます。 詳細については、「[Ultra Disk](../../linux/disks-types.md#ultra-disk)」の記事を参照してください。

1 個のディスクのコストは、特定のディスクに対して個別に定義できる 3 つのディメンションによって決まります。 


SAP ワークロードの機能マトリックスは次のようになります。

| 機能| 解説| 注またはリンク | 
| --- | --- | --- | 
| OS ベース VHD | 機能しない | - |
| データ ディスク | 適合 | すべてのシステム  |
| SAP グローバル トランスポート ディレクトリ | YES | [サポートされています](https://launchpad.support.sap.com/#/notes/2015553) |
| SAP sapmnt | 適合 | すべてのシステム |
| バックアップ ストレージ | 適合 | 短期的なバックアップの保存 |
| ファイル共有または共有ディスク | 利用不可 | サード パーティが必要 |
| 回復性 | LRS | ディスクに GRS または ZRS は利用不可 |
| Latency | 非常に低い | - |
| IOPS SLA | YES | - |
| IOPS は容量に対して線形 | ブラケット内で半直線  | [Managed Disks の価格](https://azure.microsoft.com/pricing/details/managed-disks/) |
| ディスクあたりの最大 IOPS | 1,200 - 160,000 | ディスク容量に依存 |
| スループット SLA | YES | - |
| スループット (容量に対して線形) | ブラケット内で半直線 | [Managed Disks の価格](https://azure.microsoft.com/pricing/details/managed-disks/) |
| HANA 認定 | YES | - |
| ディスクのスナップショット可能 | NO | - |
| Azure Backup VM スナップショット可能 | NO | - |
| コスト | Premium Storage より高い | - |



**概要:** Azure Ultra Disk は待機時間が少なく、すべての種類の SAP ワークロードに適切なストレージです。 これまでは、Availability Zones (ゾーン デプロイ) を使用してデプロイした VM との組み合わせでのみ、Ultra Disk を使用できました。 現時点では、Ultra Disk でストレージ スナップショットはサポートされません。 Ultra ディスクは他のすべてのストレージとは逆で、ベース VHD ディスクには使用できません。 I/O ワークロードの変動が大きく、デプロイされたストレージのスループットまたは IOPS を、帯域幅と IOPS を最大使用量に合わせてサイズ変更するのではなく、ストレージのワークロード パターンに合わせて調整する場合、Ultra Disk が最適です。


## <a name="azure-netapp-files-anf"></a>Azure NetApp Files (ANF)
[Azure NetApp Files](https://azure.microsoft.com/services/netapp/) は、パフォーマンスに優れた Azure ネイティブの NFS および SMB 共有を実現することを目標として、Microsoft と NetApp の連携による成果です。 DBMS のデプロイ シナリオを実現する待機時間が少なく高帯域幅のストレージを提供すること、やがては Azure を通じて NetApp ストレージの一般的な運用機能も実現することに重点が置かれています。 NFS または SMB 共有は、ストレージのスループットと価格が区別された 3 つの異なるサービス レベルで提供されます。 それらのサービス レベルについては、「[Azure NetApp Files のサービス レベル](../../../azure-netapp-files/azure-netapp-files-service-levels.md)」の記事を参照してください。 SAP ワークロードの種類に応じて、次のサービス レベルを強くお勧めします。

- SAP DBMS ワークロード:   パフォーマンス、理想的には Ultra
- SAPMNT 共有:        パフォーマンス、理想的には Ultra
- グローバル トランスポート ディレクトリ: パフォーマンス、理想的には Ultra

> [!NOTE]
> 最小プロビジョニング サイズは、容量プールと呼ばれる 4 TiB のユニットです。 その後、この容量プールからボリュームを作成します。 一方、構築できる最小のボリュームは 100 GiB です。 TiB 単位で段階的に容量プールを拡張できます。 価格については、「[Azure NetApp Files の価格](https://azure.microsoft.com/pricing/details/netapp/)」の記事をご確認ください。

現在、ANF ストレージは、いくつかの SAP ワークロード シナリオでサポートされています。

- SAP のグローバル トランスポート ディレクトリに SMB または NFS 共有を提供する
- 次のドキュメントに記載されているような高可用性シナリオでの共有 sapmnt:
    - [SAP アプリケーション用の Azure NetApp Files (SMB) を使用した Windows 上の Azure VM における SAP NetWeaver の高可用性](./high-availability-guide-windows-netapp-files-smb.md)
    - [SAP アプリケーション用の Azure NetApp Files を使用した SUSE Linux Enterprise Server 上の Azure VM 上の SAP NetWeaver の高可用性](./high-availability-guide-suse-netapp-files.md)
    - [SAP アプリケーション用の Azure NetApp Files を使用した Red Hat Enterprise Linux 上の SAP NetWeaver 用の Azure Virtual Machines の高可用性](./high-availability-guide-rhel-netapp-files.md)
- /hana/data および /hana/log ボリューム用に NFS v4.1 共有を使用し、かつ (または) /hana/shared ボリューム用に NFS v4.1 か NFS v3 ボリュームを使用した SAP HANA デプロイ (「[SAP HANA Azure 仮想マシンのストレージ構成](./hana-vm-operations-storage.md)」を参照)

> [!NOTE]
> Azure NetApp Files ベースの NFS または SMB 共有では、他の DBMS ワークロードはサポートされていません。 これが変更された場合は、更新プログラムと変更情報が提供されます。

Azure Premium Storage の場合と同様に、スループットにおいてなんらかの最小値に従う必要がある場合は、GB あたりの固定または線形スループット サイズが問題になることがあります。 SAP HANA はこのようなケースに該当します。 ANF では、この問題が Azure Premium ディスクよりも顕著になる可能性があります。 Azure Premium ディスクの場合は、GiB あたりのスループットが相対的に高い複数の小さいディスクを使用し、それら全体をストライピングすることでコスト効率を高め、比較的少ない容量でより高いスループットを実現できます。 この種類のストライピングは、ANF でホストされている NFS または SMB 共有では機能しません。 この制限により、結果的に次のような過剰な容量がデプロイされることがありました。

- たとえば、ANF でホストされている NFS ボリュームで 250 MiB/秒のスループットを実現するには、Ultra サービス レベルの容量 1.95 TiB をデプロイする必要があります。 
- 400 MiB/秒を実現するには、容量 3.125 TiB をデプロイする必要があります。 しかし、ボリュームに必要なスループットを実現するために、容量の過剰プロビジョニングが必要になる場合があります。 この容量の過剰プロビジョニングは、より小規模な HANA インスタンスの価格に影響します。 
- SAP の /sapmnt ディレクトリ用の ANF 上で NFS を使用する空間では、Azure NetApp Files によって適用される 100 GiB から 150 GiB までという最小容量で通常は十分です。 ただし、12.8 MiB/秒 (Ultra サービス レベルを使用) の関連スループットが十分ではなく、SAP システムの安定性に悪影響を及ぼす可能性があることが、カスタマー エクスペリエンスによって判明しました。 このような場合、お客様は /sapmnt ボリュームの容量を増やすことで問題を回避できます。それにより、そのボリュームにより多くのスループットが提供されます。

SAP ワークロードの機能マトリックスは次のようになります。

| 機能| 解説| 注またはリンク | 
| --- | --- | --- | 
| OS ベース VHD | 機能しない | - |
| データ ディスク | 適合 | SAP HANA のみ  |
| SAP グローバル トランスポート ディレクトリ | YES | SMB および NFS |
| SAP sapmnt | 適合 | すべてのシステムの SMB (Windows のみ) または NFS (Linux のみ) |
| バックアップ ストレージ | 適合 | - |
| ファイル共有または共有ディスク | YES | SMB 3.0、NFS v3、および NFS v4.1 |
| 回復性 | LRS | ディスクに GRS または ZRS は利用不可 |
| Latency | 非常に低い | - |
| IOPS SLA | YES | - |
| IOPS は容量に対して線形 | 厳密に線形  | [サービス レベル](../../../azure-netapp-files/azure-netapp-files-service-levels.md)に依存 |
| スループット SLA | YES | - |
| スループット (容量に対して線形) | ブラケット内で半直線 | [サービス レベル](../../../azure-netapp-files/azure-netapp-files-service-levels.md)に依存 |
| HANA 認定 | YES | - |
| ディスクのスナップショット可能 | YES | - |
| Azure Backup VM スナップショット可能 | NO | - |
| コスト | Premium Storage より高い | - |


ANF ストレージのその他の組み込み機能:

- ボリュームのスナップショットを実行する機能
- スナップショットからの ANF ボリュームの複製
- スナップショットからのボリュームの復元 (snap-revert)

**概要**:Azure NetApp Files は、NFS と SMB のボリュームまたは共有をデプロイできる、HANA 認定の待機時間が少ないストレージです。 このストレージには、ボリュームの GiB 容量ごとに異なる線形のスループットと IOPS を提供する、3 つの異なるサービス レベルが用意されています。 ANF ストレージによって、スタンバイ ノードを使用して SAP HANA スケールアウト シナリオをデプロイできるようになります。 このストレージは、/sapmnt または SAP グローバル トランスポート ディレクトリ用に、必要に応じてファイル共有を提供するのに適しています。 ANF ストレージには、ネイティブの NetApp 機能として使用できる機能があります。  



## <a name="azure-standard-ssd-storage"></a>Azure Standard SSD ストレージ
Azure Standard SSD は、Azure Standard HDD ストレージに比べて可用性、一貫性、信頼性、待機時間が優れています。 低い IOPS レベルで一貫性のあるパフォーマンスを必要とするワークロード用に最適化されています。 このストレージは、IOPS とスループットの要件が低い非運用 SAP システムで使用される最小ストレージです。 SAP ワークロードの機能マトリックスは次のようになります。

| 機能| 解説| 注またはリンク | 
| --- | --- | --- | 
| OS ベース VHD | 制限付き適合 | 非運用システム |
| データ ディスク | 制限付き適合 | IOPS と待機時間の要件が低い一部の非運用システム |
| SAP グローバル トランスポート ディレクトリ | NO | [サポートされていません](https://launchpad.support.sap.com/#/notes/2015553) |
| SAP sapmnt | 制限付き適合 | 非運用システム |
| バックアップ ストレージ | 適合 | - |
| ファイル共有または共有ディスク | 利用不可 | サード パーティが必要 |
| 回復性 | LRS、GRS | ディスクに ZRS は利用不可 |
| Latency | high | SAP グローバル トランスポート ディレクトリまたは運用システムには高すぎる |
| IOPS SLA | NO | - |
| ディスクあたりの最大 IOPS | 500 | ディスクのサイズに依存しない |
| スループット SLA | NO | - |
| HANA 認定 | NO | - |
| ディスクのスナップショット可能 | YES | - |
| Azure Backup VM スナップショット可能 | YES | - |
| コスト | LOW | - |



**概要:** Azure Standard SSD ストレージは、非運用 VM のベース VHD (最終的には、待機時間を比較的問題にしない、かつ (または) IOPS とスループット レートが低い DBMS デプロイ) 用として最小の推奨事項です。 この Azure Storage の種類は、SAP グローバル トランスポート ディレクトリをホストするためにはサポートされなくなりました。 



## <a name="azure-standard-hdd-storage"></a>Azure Standard HDD ストレージ
Azure Standard HDD ストレージは、2014 年に Azure インフラストラクチャが SAP NetWeaver ワークロード用として認定を受けたときの、唯一のストレージの種類でした。 2014 年には、Azure 仮想マシンは小規模でストレージのスループットが低いものでした。 そのため、このストレージの種類で要求に対応することができました。 このストレージは待機時間を問題にしないワークロードには理想的なものですが、それは SAP 空間ではまずありえません。 Azure VM のスループットが増加し、これらの VM で生成されるワークロードが増加したことで、このストレージの種類については、SAP シナリオでの使用は考慮されなくなりました。 SAP ワークロードの機能マトリックスは次のようになります。

| 機能| 解説| 注またはリンク | 
| --- | --- | --- | 
| OS ベース VHD | 不適合 | - |
| データ ディスク | 不適合 | - |
| SAP グローバル トランスポート ディレクトリ | NO | [サポートされていません](https://launchpad.support.sap.com/#/notes/2015553) |
| SAP sapmnt | NO | サポートされていません |
| バックアップ ストレージ | 適合 | - |
| ファイル共有または共有ディスク | 利用不可 | Azure Files またはサード パーティが必要 |
| 回復性 | LRS、GRS | ディスクに ZRS は利用不可 |
| Latency | high | DBMS での使用、SAP グローバル トランスポート ディレクトリ、sapmnt または saploc 用には高すぎる |
| IOPS SLA | NO | - |
| ディスクあたりの最大 IOPS | 500 | ディスクのサイズに依存しない |
| スループット SLA | NO | - |
| HANA 認定 | NO | - |
| ディスクのスナップショット可能 | YES | - |
| Azure Backup VM スナップショット可能 | YES | - |
| コスト | LOW | - |



**概要:** Standard HDD は、SAP バックアップを格納するためにのみ使用する必要がある Azure Storage の種類です。 あちこちのデータを検索するために使用される廃止されたシステムなど、かなり非アクティブなシステムのベース VHD としてのみ使用してください。 しかし、アクティブな開発、QA、運用 VM をそのストレージに基づかせることはできません。 また、そのストレージでデータベース ファイルをホストしないでください。


## <a name="azure-vm-limits-in-storage-traffic"></a>ストレージ トラフィックに関する Azure VM の制限
オンプレミスのシナリオとは対照的に、選択する個々の VM の種類は、実現できるストレージ帯域幅において重要な役割を果たします。 ストレージの種類に応じて、次の点を考慮する必要があります。

| ストレージの種類| Linux | Windows | 説明 |
| --- | --- | --- | --- |
| Standard HDD | [Azure の Linux 仮想マシンのサイズ](../../sizes.md) | [Azure の Windows 仮想マシンのサイズ](../../sizes.md) | 中規模または大規模な VM のストレージの制限に触れにくい |
| Standard SSD | [Azure の Linux 仮想マシンのサイズ](../../sizes.md) | [Azure の Windows 仮想マシンのサイズ](../../sizes.md) | 中規模または大規模な VM のストレージの制限に触れにくい |
| Premium Storage | [Azure の Linux 仮想マシンのサイズ](../../sizes.md) | [Azure の Windows 仮想マシンのサイズ](../../sizes.md) | ストレージ構成により IOPS またはストレージ スループットについての VM の制限に達しやすい |
| Ultra Disk Storage | [Azure の Linux 仮想マシンのサイズ](../../sizes.md) | [Azure の Windows 仮想マシンのサイズ](../../sizes.md) | ストレージ構成により IOPS またはストレージ スループットについての VM の制限に達しやすい |
| Azure NetApp Files | [Azure の Linux 仮想マシンのサイズ](../../sizes.md) | [Azure の Windows 仮想マシンのサイズ](../../sizes.md) | ストレージ トラフィックによって、ストレージの帯域幅ではなくネットワーク スループットの帯域幅が使用される |

制限として、次のことにご注意ください。

- VM のサイズが小さいほど、アタッチできるディスクが少なくなります。 これは ANF には適用されません。 NFS または SMB 共有をマウントするので、アタッチする共有ボリュームの数に制限はありません。
- VM には I/O スループットと IOPS の制限があり、Premium Storage ディスクと Ultra ディスクでは、それらを容易に超過する可能性があります。
- ANF を使用すると、共有ボリュームへのトラフィックによって、ストレージの帯域幅ではなく VM のネットワーク帯域幅が消費されます。
- 2 桁の TiB 容量空間に大容量の NFS ボリュームがある場合、1 台の VM からこのようなボリュームにアクセスする場合のスループットは、単一セッションでの共有ボリュームとの対話に関する Linux の制限に基づいて頭打ちになります。 

SAP システムのライフサイクルにおいて Azure VM のサイズを大きくするときは、より容量の大きい新しい VM の種類の IOPS とストレージ スループットの制限を評価する必要があります。 場合によっては、ストレージ構成を Azure VM の新機能に合わせて調整することも意味があります。 


## <a name="striping-or-not-striping"></a>ストライピングするかどうか
複数の Azure ディスクから 1 つの大きなボリュームにストライプ セットを作成すると、個々のディスクの IOPS とスループットを 1 つのボリュームに累積できます。 Azure Standard Storage と Azure Premium Storage にのみ使用されます。 ディスク容量に関係なくスループットと IOPS を構成できる Azure Ultra ディスクでは、ストライプ セットを使用する必要はありません。 NFS または SMB に基づく共有ボリュームをストライピングすることはできません。 Azure Premium Storage のスループットと IOPS には非線形という特徴があるため、大規模な単一の Azure Premium Storage ディスクと同じ IOPS とスループットを持つ、より小さな容量をプロビジョニングすることができます。 これは、Azure Premium Storage を使用して、より高いスループットまたは IOPS をより低コストで実現するための方法です。 次に例を示します。

- 2 台の P15 Premium Storage ディスクをストライピングして実現できるスループットは、 
- 250 MiB/秒です。このようなボリュームは、512 GiB の容量になります。 1 秒あたり 250 MiB のスループットを提供する単一のディスクを希望する場合は、2 TiB の容量を持つ P40 ディスクを選択する必要があります。 
- または、ストライピングにより総容量 512 GiB となる 4 台の P10 Premium Storage ディスクをストライピングすることで、400 MiB/秒のスループットを実現できます。 少なくとも 1 秒あたり 500 MiB のスループットを持つ単一のディスクを希望する場合は、8 TiB の P60 Premium Storage ディスクを選択する必要があります。 Premium Storage で発生するコストは容量に対してほぼ線形であるため、ストライピングを使用することでコスト削減を実感できます。

ストライピングでは、いくつかのルールに従う必要があります。

- Azure Storage によってデータの冗長性は既に維持されているため、VM 内に構成されたストレージは使用しないでください
- ストライプ セットが適用されるディスクは、同じサイズである必要があります。

Azure Premium Storage を使用して優れた価格/パフォーマンス比を実現するには、複数の小さなディスクにまたがるストライピングが最適な方法です。 ストライピングには、追加のデプロイと管理のオーバーヘッドがあることがわかっています。

特定のストライプ サイズに関する推奨事項については、「[SAP HANA Azure 仮想マシンのストレージ構成](./hana-vm-operations-storage.md)」など、さまざまな DBMS のドキュメントを参照してください。




## <a name="next-steps"></a>次のステップ
以下の記事を参照してください。

- [SAP ワークロードのための Azure Virtual Machines DBMS デプロイの考慮事項](./dbms_guide_general.md)
- [SAP HANA Azure 仮想マシンのストレージ構成](./hana-vm-operations-storage.md)
 