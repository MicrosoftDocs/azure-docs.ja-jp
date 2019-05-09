---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/14/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: da46687517dbfe189571286087d4ef29d50d1246
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/25/2019
ms.locfileid: "54906324"
---
# <a name="standard-ssd-managed-disks-for-azure-virtual-machine-workloads"></a>Azure 仮想マシンのワークロード用の Standard SSD マネージド ディスク

Azure Standard Solid State Drives (SSD) マネージド ディスクは、下位の IOPS レベルで一貫したパフォーマンスを必要とするワークロード向けに最適化された、コスト効率に優れたストレージ オプションです。 Standard SSD は、クラウドへの移行を希望している方、特にオンプレミスの HDD ソリューションで実行されているワークロードのばらつきによって問題が発生している場合に、エントリ レベルの優れたエクスペリエンスを提供します。 Standard SSD は、HDD ディスクと比較して、可用性、一貫性、信頼性、待機時間の点で優れており、Web サーバー、低 IOPS アプリケーション サーバー、軽い負荷で使用されるエンタープライズ アプリケーション、Dev/Test ワークロードに適しています。

## <a name="standard-ssd-features"></a>Standard SSD の機能

**Managed Disks**:Standard SSD は Managed Disks としてのみ使用できます。 Standard SSD では、アンマネージド ディスクとページ BLOB がサポートされていません。 マネージド ディスクを作成するときに、ディスクの種類を Standard SSD と指定し、必要なディスク サイズを示すと、Azure によって自動的にディスクの作成と管理が行われます。
Standard SSD では、マネージド ディスクによって提供されるすべてのクラシック デプロイ モデル操作がサポートされます。 たとえば、マネージド ディスクと同じ方法で、Standard SSD マネージド ディスクの作成、コピー、スナップショットの作成を行うことができます。

**Virtual Machines**:Standard SSD は、Premium ディスクをサポートしていない VM の種類を含め、すべての Azure VM と共に使用できます。 たとえば、A シリーズ VM、N シリーズ VM、DS シリーズ、またはその他の Azure VM シリーズを使用している場合、その VM と共に Standard SSD を使用できます。 Standard SSD の導入によって、以前は HDD ベースのディスクが使用されていた広範なワークロードを SSD ベースのディスクに移行することで、一貫したパフォーマンス、高可用性、低遅延、全般的なエクスペリエンス向上が実現します。

**優れた耐久性と可用性**:Azure ディスクのプラットフォームには、ディスクに高い可用性と耐久性を一貫して確保してきた実績があります。Standard SSD も、それと同じプラットフォーム上に構築されています。 Azure ディスクは、99.999 パーセントの可用性を実現するように設計されています。 すべてのマネージド ディスクと同様に、Standard SSD でもローカル冗長ストレージ (LRS) が提供されます。 LRS を使用すると、そのプラットフォームで全ディスクのデータのレプリカが複数維持されるため、IaaS ディスクにエンタープライズ レベルの持続性 (業界トップ レベルの年間故障率 0 パーセント) を一貫して提供できます。

**スナップショット**: すべてのマネージド ディスクと同様に、Standard SSD でもスナップショットの作成がサポートされています。 スナップショットの種類には、Standard (HDD) または Premium (SSD) を使用できます。 コストの削減のため、すべての Azure ディスクの種類で、スナップショットの種類として Standard (HDD) を使用することをお勧めします。 これは、スナップショットからマネージド ディスクを作成するときに、いつでも Standard SSD や Premium SSD などの上位レベルを選択できるからです。

## <a name="scalability-and-performance-targets"></a>スケーラビリティとパフォーマンスのターゲット

次の表に、現在 Standard SSD に提供されているディスク サイズを示します。 アスタリスクで示されるサイズはプレビュー中です。

|Standard SSD ディスクの種類  |ディスク サイズ  |ディスクあたりの IOPS  |ディスクあたりのスループット  |
|---------|---------|---------|---------|
|E4     |32 GiB         |最大 120         |最大 25 MiB/秒         |
|E6     |64 GiB         |最大 240         |最大 50 MiB/秒         |
|E10     |128 GiB         |最大 500         |最大 60 MiB/秒         |
|E15     |256 GiB         |最大 500         |最大 60 MiB/秒         |
|E20     |512 GiB         |最大 500         |最大 60 MiB/秒         |
|E30     |1,024 GiB       |最大 500         |最大 60 MiB/秒         |
|E40     |2,048 GiB       |最大 500         |最大 60 MiB/秒         |
|E50     |4,095 GiB       |最大 500         |最大 60 MiB/秒         |
|E60 *     |8,192 GiB       |最大 1,300       |最大 300 MiB/秒        |
|E70 *    |16,384 GiB      |最大 2,000       |最大 500 MiB/秒        |
|E80 *    |32,767 GiB      |最大 2,000       |最大 500 MiB/秒        |

Standard SSD では、ほとんどの IO 操作で 1 桁ミリ秒の待機時間を実現し、99% の時間で上記の表に記載されている最大 IOPS と最大スループットを提供するように設計されています。 実際の IOPS とスループットは、トラフィック パターンによって異なる場合があります。 Standard SSD は、HDD ディスクよりも一貫したパフォーマンスを提供し、待機時間が短くなります。

一方、Premium SSD は、Standard SSD よりもパフォーマンスが優れており、低遅延、高 IOPS/スループットを実現し、プロビジョニング済みディスクのパフォーマンスとの一貫性をさらに高めます。 Premium SSD は重要な運用環境のワークロード向けに推奨されるディスクの種類です。 ワークロードで高パフォーマンス、低待機時間のディスク サポートが必要な場合は、Premium Storage を使用することを検討してください。

Premium SSD と同様に、Standard SSD も 256 KiB の IO ユニット サイズを使用します。 転送されるデータが 256 KiB に満たない場合は、1 つの I/O ユニットとみなされます。 I/O のサイズが大きくなると、サイズが 256 KiB の複数の I/O としてカウントされます。 たとえば、1,100 KiB の I/O は 5 つの I/O ユニットとしてカウントされます。

## <a name="pricing-and-billing"></a>価格と課金

Standard SSD を使用するときには、課金に関する次の考慮事項が適用されます。

- マネージド ディスクのサイズ
- スナップショット
- 送信データ転送
- トランザクション

**マネージド ディスクのサイズ**:マネージド ディスクは、プロビジョニング済みのサイズに対して課金されます。 Azure では、プロビジョニング済みサイズ (切り上げたもの) を、最も近いディスク サイズのプランにマップします。 提供されるディスク サイズの詳細については、上記の「スケーラビリティとパフォーマンスのターゲット」のセクションの表を参照してください。 各ディスクは、サポートされているプロビジョニング済みディスク サイズにマップされ、それに応じて課金されます。 たとえば、200 GiB の Standard SSD をプロビジョニングした場合、E15 (256 GiB) のディスク サイズのプランにマップされます。 プロビジョニングされたディスクには、Premium Storage プランの月額料金を使用して、時間割りで計算して課金されます。 たとえば、E10 ディスクをプロビジョニングし、20 時間後にそのディスクを削除した場合は、E10 製品の 20 時間分に対して課金されます。 ディスクに書き込まれた実際のデータ量には関係ありません。

**スナップショット**: Managed Disks のスナップショットが存在する場合、ターゲットとソースでスナップショットによって使用された容量に対して課金されます。 スナップショットの詳細については、「[管理ディスクのスナップショット](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#managed-disk-snapshots)」を参照してください。

**送信データ転送**: [送信データ転送](https://azure.microsoft.com/pricing/details/bandwidth/) (Azure データ センターから送信されるデータ) では、帯域幅の使用量に対して課金されます。

**トランザクション**:Standard HDD と同様に、Standard SSD 上のトランザクションは課金されます。 トランザクションには、ディスクに対する読み書き操作の両方が含まれます。 Standard SSD 上のトランザクションの計算に使用される I/O ユニット サイズは 256 KiB です。 I/O のサイズが大きくなると、サイズが 256 KiB の複数の I/O としてカウントされます。

Virtual Machines と Managed Disks の価格の詳細については、次のページをご覧ください。

- [Virtual Machines の料金](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)
- [Managed Disks の価格](https://azure.microsoft.com/pricing/details/managed-disks/)

## <a name="next-steps"></a>次の手順

Standard SSD の作成の詳細については、複数の Standard SSD を搭載した VM を作成する方法を示すサンプルを参照してください。

> [!div class="nextstepaction"]
> [複数の Standard SSD を搭載した VM の作成](https://github.com/azure/azure-quickstart-templates/tree/master/101-vm-with-standardssd-disk/)
