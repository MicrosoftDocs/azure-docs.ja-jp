---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 04/09/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: aa740cfb203f50dc97a06359774dae367a20252b
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2019
ms.locfileid: "55147944"
---
## <a name="about-vhds"></a>VHD について

Azure で使用される VHD は .vhd ファイルです。Azure では Standard または Premium Storage アカウントでページ BLOB としてこれらを格納します。 ページ BLOB の詳細については、「[ブロック BLOB およびページ BLOB について](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs/)」をご覧ください。 Premium Storage の詳細については、[高パフォーマンスの Premium Storage と Azure VM](../articles/virtual-machines/windows/premium-storage.md) に関する記事を参照してください。

Azure は VHD フォーマットの固定ディスクをサポートしています。 固定フォーマットの場合、ファイル内で論理ディスクがリニアにレイアウトされるため、ディスク オフセット X は BLOB オフセット X に格納されます。BLOB 末尾の小さなフッターに、VHD のプロパティが記述されます。 固定フォーマットの場合、ほとんどのディスクに大きな未使用の範囲が含まれるため、容量が無駄になることがよくあります。 しかし、Azure では .vhd ファイルをスパース フォーマットで格納するため、固定ディスクのメリットと動的ディスクのメリットを同時に享受できます。 詳細については、「[仮想ハード ディスクの概要](https://technet.microsoft.com/library/dd979539.aspx)」をご覧ください。

ディスクまたはイメージを作成するためのソースとして使用する Azure 内の すべての VHD ファイルは読み取り専用ですが、Azure ストレージにユーザーによってアップロードまたはコピーされた .vhd ファイルは除きます (これらは読み取り/書き込みまたは読み取り専用です)。 ユーザーがディスクやイメージを作成するときに Azure はソース .vhd ファイルのコピーを作成します。 これらのコピーは、ユーザーが VHD をどのように使用するかに応じて、読み出し専用または読み書き可能にすることができます。

ユーザーがイメージから仮想マシンを作成するときに、Azure はソースの .vhd ファイルのコピーである仮想マシンのディスクを作成します。 Azure では、誤って削除されるのを防ぐために、イメージ、オペレーティング システム ディスク、またはデータ ディスクを作成する際に使用されるソースの .vhd ファイルにリースを設定します。

ディスクやイメージを削除してリースを解除しなければ、ソースの .vhd ファイルを削除できません。 仮想マシンがオペレーティング システム ディスクとして使用中の .vhd ファイルを削除する場合、仮想マシンを削除し、関連付けられたすべてのディスクを削除することで、ユーザーは仮想マシン、オペレーティング システム ディスク、およびソースの .vhd ファイルをすべて一度に削除することができます。 ただし、データ ディスクのソースである .vhd ファイルを削除するには、いくつかの手順を決められた順序で行う必要があります。 まず、仮想マシンからディスクを取り外し、ディスクを削除し、続いて .vhd ファイルを削除します。

> [!WARNING]
> ユーザーがストレージからソースの .vhd ファイルを削除する場合、またはストレージ アカウントを削除する場合、Microsoft はそのデータを回復することはできません。

## <a name="types-of-disks"></a>ディスクの種類

Azure ディスクは、99.999% の可用性で設計されています。 エンタープライズレベルの持続性を、業界トップレベルの年間故障率 0% で一貫して提供してきました。

ディスクの作成時に選択できるストレージには、Premium SSD ディスク、Standard SSD、および Standard HDD ストレージという 3 つのパフォーマンス レベルが存在します。 さらに、アンマネージドとマネージドという 2 種類のディスクがあります。

### <a name="standard-hdd-disks"></a>Standard HDD ディスク

Standard HDD ディスクは、HDD を使用して、コスト効果の高いストレージを提供します。 Standard HDD ストレージは、1 つのデータセンターでローカルにレプリケートするか、プライマリ データセンターとセカンダリ データセンターで geo 冗長レプリケートできます。 ストレージのレプリケーションの詳細については、「[Azure Storage のレプリケーション](../articles/storage/common/storage-redundancy.md)」を参照してください。

Standard HDD ディスクの使用の詳細については、[標準ストレージとディスク](../articles/virtual-machines/windows/standard-storage.md)に関する記事を参照してください。

### <a name="standard-ssd-disks"></a>Standard SSD ディスク

Standard SSD ディスクは、Standard HDD ディスクと同種のワークロードに対応するように設計されていますが、一貫したパフォーマンスと信頼性が HDD よりも優れています。 Standard SSD ディスクは、Premium SSD ディスクと Standard HDD ディスクの要素を組み合わせたコスト効率の高いソリューションであり、ディスクの高 IOPS を必要としないアプリケーション (Web サーバーなど) に最適です。 使用可能な場合、Standard SSD ディスクは、ほとんどのワークロードのために推奨される展開オプションです。 Standard SSD ディスクは、マネージド ディスクとして、回復性の種類としてローカル冗長ストレージ (LRS) のみを現在利用可能なすべてのリージョンで利用できます。

### <a name="premium-ssd-disks"></a>Premium SSD ディスク

Premium SSD ディスクでは、SSD を使用して、I/O 集中型のワークロードを実行する VM 向けの高パフォーマンスで待ち時間の少ないディスク サポートを提供します。 通常、Premium SSD ディスクは、シリーズ名に "s" が含まれているサイズで使用できます。 たとえば、Dv3 シリーズと Dsv3 シリーズがある場合、Dsv3 シリーズで Premium SSD ディスクを使用できます。  詳細については、[Premium Storage](../articles/virtual-machines/windows/premium-storage.md) に関する記事を参照してください。

### <a name="unmanaged-disks"></a>非管理対象ディスク

非管理対象ディスクとは、VM で使用されてきた従来の種類のディスクです。 これらのディスクでは、独自のストレージ アカウントを作成し、ディスクの作成時にそのストレージ アカウントを指定します。 同じストレージ アカウントにディスクを配置しすぎないようにしてください。ストレージ アカウントの[スケーラビリティ ターゲット](../articles/storage/common/storage-scalability-targets.md) (たとえば 20,000 IOPS) を超過すると、VM が調整されるためです。 非管理対象ディスクを使用する場合は、VM のパフォーマンスを最大限に引き出すために、1 つ以上のストレージ アカウントを最大限に利用する方法を確認する必要があります。

### <a name="managed-disks"></a>マネージド ディスク

Managed Disks により、ストレージ アカウントの作成/管理はバックグラウンドで処理されるため、ストレージ アカウントのスケーラビリティの制限について心配する必要がなくなります。 ディスク サイズとパフォーマンス レベル (Standard/Premium) を指定するだけで、Azure でディスクが作成され、管理されます。 ディスクの追加や VM のスケールアップとスケールダウンを行うときに、使用されているストレージについて心配する必要はありません。

また、Azure リージョンごとに 1 つのストレージ アカウントでカスタム イメージを管理することができます。このカスタム イメージを使用すると、同じサブスクリプション内で何百もの VM を作成することができます。 Managed Disks の詳細については、[Managed Disks の概要](../articles/virtual-machines/windows/managed-disks-overview.md)に関するページをご覧ください。

Managed Disks の多くの機能を活用するために、新しい VM には Managed Disks を使用し、以前の非管理対象ディスクをマネージド ディスクに変換することをお勧めします。

### <a name="disk-comparison"></a>ディスクの比較

次の表では、Standard HDD、Standard SSD、および Premium SSD のマネージド ディスクとアンマネージド ディスクの両方を比較しており、どちらを使用するかを決定するのに役立ちます。 アスタリスクで示されるサイズはプレビュー中です。

|    | Azure Premium ディスク |Azure Standard SSD ディスク | Azure Standard HDD ディスク
|--- | ------------------ | ------------------------------- | -----------------------
| ディスクの種類 | ソリッド ステート ドライブ (SSD) | ソリッド ステート ドライブ (SSD) | ハード ディスク ドライブ (HDD)  
| 概要  | I/O 集中型のワークロードを実行している VM またはミッション クリティカルな運用環境をホストしている VM 向けの、SSD ベースの高パフォーマンスで待ち時間の少ないディスク サポート |一貫したパフォーマンスと信頼性が HDD よりも優れている。 低 IOPS ワークロード用に最適化| 不定期に起こるアクセス用の HDD ベースのコスト効果の高いディスク
| シナリオ  | 運用環境のワークロードやパフォーマンスに影響されやすいワークロード |Web サーバー、あまり使用されていないエンタープライズ アプリケーション、および開発/テスト| バックアップ、重要ではない、不定期に起こるアクセス
| ディスク サイズ | P4:32 GiB (マネージド ディスクのみ)<br>P6:64 GiB (マネージド ディスクのみ)<br>P10:128 GiB<br>P15:256 GiB (マネージド ディスクのみ)<br>P20:512 GiB<br>P30:1,024 GiB<br>P40:2,048 GiB<br>P50:4,095 GiB<br>P60:8,192 GiB * (8 TiB)<br>P70:16,384 GiB * (16 TiB)<br>P80:32,767 GiB * (32 TiB) |マネージド ディスクのみ:<br>E4:32 GiB<br>E6:64 GiB<br>E10:128 GiB<br>E15:256 GiB<br>E20:512 GiB<br>E30:1,024 GiB<br>E40:2,048 GiB<br>E50:4,095 GiB<br>E60:8,192 GiB * (8 TiB)<br>E70:16,384 GiB * (16 TiB)<br> E80:32,767 GiB * (32 TiB) | 非管理対象ディスク:1 GiB - 4 TiB (4095 GiB) <br><br>管理ディスク:<br> S4:32 GiB <br>S6:64 GiB <br>S10:128 GiB <br>S15:256 GiB <br>S20:512 GiB <br>S30:1,024 GiB <br>S40:2,048 GiB<br>S50:4,095 GiB<br>S60:8,192 GiB * (8 TiB)<br>S70:16,384 GiB * (16 TiB)<br>S80:32,767 GiB * (32 TiB)
| ディスクあたりの最大スループット | P4:25 MiB/秒<br> P6:50 MiB/秒<br> P10:100 MiB/秒<br> P15:125 MiB/秒<br> P20:150 MiB/秒<br> P30:200 MiB/秒<br> P40 - P50:250 MiB/秒<br> P60:480 MiB/秒 *<br> P70 - P80:750 MiB/秒 * | E10 - E50:最大 60 MiB/秒<br> E60:最大 300 MiB/秒 *<br> E70 - E80:500 MiB/秒 *| S4 - S50:最大 60 MiB/秒<br> S60:最大 300 MiB/秒 *<br> S70 - S80:最大 500 MiB/秒 *
| ディスクあたりの最大 IOPS | P4:120 IOPS<br> P6:240 IOPS<br> P10:500 IOPS<br> P15:1,100 IOPS<br> P20:2,300 IOPS<br> P30:5,000 IOPS<br> P40 - P50:7,500 IOPS<br> P60:12,500 IOPS *<br> P70:15,000 IOPS *<br> P80:20,000 IOPS * | E10 - E50:最大 500 IOPS<br> E60:最大 1,300 IOPS *<br> E70 - E80:最大 2,000 IOPS * | S4 - S50:最大 500 IOPS<br> S60:最大 1,300 IOPS *<br> S70 - S80:最大 2,000 IOPS *
