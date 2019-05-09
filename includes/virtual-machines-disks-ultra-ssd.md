---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/24/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 212506667a56befb4e3926dec7a9e3eb9772ebed
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/04/2019
ms.locfileid: "55735999"
---
# <a name="ultra-ssd-preview-managed-disks-for-azure-virtual-machine-workloads"></a>Azure 仮想マシンのワークロード用の Ultra SSD (プレビュー) Managed Disks

Azure Ultra SSD (プレビュー) は、Azure IaaS VM 用に高スループット、高 IOPS、一貫性のある低待機時間のディスク ストレージを提供します。 この新しいオファリングは、Microsoft の既存のディスク オファリングと同じレベルの可用性で最上のパフォーマンスを提供します。 Ultra SSD のその他のメリットとして、仮想マシンを再起動する必要なしに、ワークロードに合わせてディスクのパフォーマンスを動的に変更できます。 Ultra SSD は、SAP HANA、最上位層のデータベース、トランザクションの多いワークロードなど、データ集中型のワークロードに適しています。

## <a name="ultra-ssd-features"></a>Ultra SSD の機能

**Managed Disks**: Ultra SSD はマネージド ディスクとしてのみ使用できます。 Ultra SSD は、アンマネージド ディスクまたはページ BLOB としてデプロイすることはできません。 マネージド ディスクを作成するときに、ディスクの種類を UltraSSD_LRS と指定し、必要なディスク サイズ、IOPS、スループットを指定すると、Azure によって自動的にディスクの作成と管理が行われます。  

**Virtual Machines**: Ultra SSD は、すべての Premium SSD 対応 Azure 仮想マシン SKU で動作するように設計されています。ただし、現在は、プレビュー段階であるため、VM のサイズは ES/DS v3 として設定されています。

**動的パフォーマンス構成**: Ultra SSD を使用すると、仮想マシンを再起動しなくても、ワークロードのニーズに合わせてディスクのパフォーマンス (IOPS とスループット) を動的に変更できます。

## <a name="scalability-and-performance-targets"></a>スケーラビリティとパフォーマンスのターゲット

Ultra SSD をプロビジョニングするときに、ディスクの容量とパフォーマンスを個別に構成することができます。 Ultra SSD は、4 GiB から最大 64 TiB までの複数の固定されたサイズで提供されます。IOPS とスループットを個別に構成できる柔軟なパフォーマンス構成モデルであることが特徴です。 Ultra SSD はデータ ディスクとしてのみ利用できます。 OS ディスクとしては Premium SSD を使用することをお勧めします。

Ultra SSD の主な機能は次のとおりです。

- ディスク容量: Ultra SSD には、4 GiB から最大 64 TiB の範囲の異なるディスク サイズがあります。
- ディスク IOPS: Ultra SSD は、300 IOPS/GiB の IOPS 制限をサポートし、ディスクあたり最大 160 K の IOPS をサポートします。 プロビジョニングした IOPS を達成するには、選択したディスクの IOPS が VM の IOPS 未満であることを確認してください。 最小ディスク IOPS は 100 IOPS です。
- ディスク スループット: Ultra SSD では、1 つのディスクのスループットは、プロビジョニングされた IOPS ごとに 256 KiB/秒に制限され、ディスクあたり最大 2000 MBps に制限されます (MBps = 秒あたり 10^6 バイト)。 最小ディスク スループットは 1 MiB です。

各ディスク サイズでサポートされるさまざまな構成を以下の表にまとめます。  

### <a name="ultra-ssd-managed-disk-offerings"></a>Ultra SSD マネージド ディスクのオファリング

|ディスク サイズ (GiB)  |IOPS の上限  |スループットの上限 (MBps)  |
|---------|---------|---------|
|4     |1,200         |300         |
|8     |2,400         |600         |
|16     |4,800         |1,200         |
|32     |9,600         |2,000         |
|64     |19,200         |2,000         |
|128     |38,400         |2,000         |
|256     |76,800         |2,000         |
|512     |80,000         |2,000         |
|1,024 - 65,536 (この範囲内のサイズは 1 TiB ずつ増えます)     |160,000         |2,000         |

## <a name="pricing-and-billing"></a>価格と課金

Ultra SSD を使用するときには、課金に関する次の考慮事項が適用されます。

- マネージド ディスクのサイズ
- マネージド ディスクのプロビジョニング IOPS
- マネージド ディスクのプロビジョニング スループット
- Ultra SSD VM 予約料金

### <a name="managed-disk-size"></a>マネージド ディスクのサイズ

マネージド ディスクは、新しい Azure VM のプロビジョニング時に選択した VM サイズに基づいて課金されます。 Azure では、プロビジョニング済みサイズ (切り上げたもの) を、最も近いディスク サイズのプランにマップします。 提供されるディスク サイズの詳細については、上記の「スケーラビリティとパフォーマンスのターゲット」のセクションの表を参照してください。 各ディスクは、サポートされているプロビジョニング済みのディスク サイズにマップされ、それに応じて時間単位で課金されます。 たとえば、200 GiB の Ultra SSD ディスクをプロビジョニングし、20 時間後に削除した場合、256 GiB のディスク サイズ プランにマップされ、20 時間分の 256 GiB に対して課金されます。 この課金は、ディスクに実際に書き込まれたデータ量に関係なく、コンピューティング時間消費に基づいて計算されたものです。

### <a name="managed-disk-provisioned-iops"></a>マネージド ディスクのプロビジョニング IOPS

IOPS は、アプリケーションが 1 秒あたりにディスクに送信する要求の数です。 入力/出力操作は、順次読み取り/書き込みの場合も、ランダム読み取り/書き込みの場合もあります。 ディスク サイズ、または VM に接続されたディスク数に基づき、IOPS の平均数に対して 1 時間単位で課金されます。 提供されるディスク IOPS の詳細については、前述の「スケーラビリティとパフォーマンスのターゲット」セクションの表を参照してください。

### <a name="managed-disk-provisioned-throughput"></a>マネージド ディスクのプロビジョニング スループット

スループットは、指定された期間にアプリケーションがディスクに送信するデータの量 (バイト/秒単位) です。 アプリケーションが大規模な入力/出力操作を実行する場合、高スループットが必要となります。  

スループットと IOPS の間には、次の式に示すような関係があります。IOPS x IO サイズ = スループット

そのため、アプリケーションが必要とする最適なスループットと IOPS の値を特定することが重要です。 一方を最適化すると、もう一方も影響を受けます。 16 KiB の IO サイズに対応するスループットから開始し、より多くのスループットが必要な場合は調整することをお勧めします。

Ultra SSD でサポートされているディスク スループットの詳細については、前述の「スケーラビリティとパフォーマンスのターゲット」セクションの表を参照してください。 ディスク サイズや IOPS と同様に、プロビジョニング スループットは、プロビジョニングされた MBps あたりの時間単位で課金されます。

### <a name="ultra-ssd-vm-reservation-fee"></a>Ultra SSD VM 予約料金

Microsoft は、VM が Ultra SSD と互換性があることを示す機能を VM に導入しています。 Ultra SSD 互換 VM は、コンピューティング VM インスタンスとブロック ストレージ スケール ユニット間に専用の帯域幅キャパシティを割り当ててパフォーマンスを最適化し、待機時間を削減しています。 このキャパシティを VM に追加すると、Ultra SSD ディスクを接続していない VM 上で Ultra SSD キャパシティを有効にした場合にのみ、予約料金がかかります。 Ultra SSD 互換の VM に Ultra SSD ディスクが接続されている場合、この料金は適用されません。 この料金は、VM 上にプロビジョニングされている vCPU ごとにかかります。

制限付きのプレビューで利用できる新しい Ultra SSD ディスクの価格については、[Azure ディスクの価格に関するページ](https://azure.microsoft.com/pricing/details/managed-disks/)を参照してください。

### <a name="ultra-ssd-preview-scope-and-limitations"></a>Ultra SSD プレビューの範囲と制限事項

プレビュー段階の Ultra SSD ディスク:

- 最初は米国東部 2 の単一の可用性ゾーンでサポートされる予定です  
- Availability Zones でのみ使用できます (ゾーン外の可用性セットと単一の VM デプロイでは、Ultra SSD ディスクを接続できません)
- ES/DS v3 VM でのみサポートされています
- データ ディスクとしてのみ使用可能であり、4k 物理セクター サイズのみをサポートしています  
- 空のディスクとしてのみ作成できます  
- 現在、デプロイには Resource Manager テンプレート、CLI、および Python SDK のみを使用できます。
- ディスク スナップショット、VM イメージ、可用性セット、Virtual Machine Scale Sets、Azure Disk Encryption はサポートされていません。
- Azure Backup または Azure Site Recovery との統合は (まだ) サポートされていません。
-  [ほとんどのプレビュー](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)と同様に、一般公開 (GA) されるまではこの機能を運用環境のワークロードに使用しないでください。
