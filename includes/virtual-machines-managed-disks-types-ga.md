---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/13/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 88a4110d68dc8aa921d647f90de654d2ebb4e17d
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2019
ms.locfileid: "58395742"
---
## <a name="premium-ssd"></a>Premium SSD

Azure Premium SSD では、IO (入出力) を集中的に行うワークロードが存在する仮想マシン (VM) 向けに、高パフォーマンスで待ち時間の少ないディスク サポートが提供されます。 Premium Storage ディスクの速度とパフォーマンスを活用するために、既存の VM ディスクを Premium SSD に移行することができます。 Premium SSD は、ミッション クリティカルな運用アプリケーションに適しています。

### <a name="disk-size"></a>ディスク サイズ
[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

## <a name="standard-ssd"></a>Standard SSD

Azure Standard SSD は、比較的低い IOPS レベルで一貫したパフォーマンスを必要とするワークロード向けに最適化された、コスト効果に優れたストレージ オプションです。 Standard SSD は、クラウドへの移行を希望している方、特にオンプレミスの HDD ソリューションで実行されているワークロードのばらつきによって問題が発生している場合に、エントリ レベルの優れたエクスペリエンスを提供します。 Standard SSD は、HDD ディスクに比べて、可用性、一貫性、信頼性、待機時間が優れています。 Standard SSD は、Web サーバー、低い IOPS のアプリケーション サーバー、使用の少ないエンタープライズ アプリケーション、開発/テストのワークロードに適しています。

### <a name="disk-size"></a>ディスク サイズ
[!INCLUDE [disk-storage-standard-ssd-sizes](disk-storage-standard-ssd-sizes.md)]

## <a name="standard-hdd"></a>Standard HDD

Azure Standard HDD では、待機時間の影響を受けないワークロードを実行する VM 向けの信頼性の高い低コストのディスク サポートが提供されます。 また、BLOB、テーブル、キュー、ファイルもサポートしています。 Standard Storage では、データはハード ディスク ドライブ (HDD) に格納されます。 VM を使用するとき、開発/テスト シナリオや重要度の低いワークロードには Standard SSD および HDD ディスクを使用できます。 Standard Storage はすべての Azure リージョンで利用できます。

### <a name="disk-size"></a>ディスク サイズ
[!INCLUDE [disk-storage-standard-hdd-sizes](disk-storage-standard-hdd-sizes.md)]

## <a name="billing"></a>課金

マネージド ディスクを使用するときには、課金に関する次の考慮事項が適用されます。

- ディスクの種類
- マネージド ディスクのサイズ
- スナップショット
- 送信データ転送
- トランザクション数

**マネージド ディスクのサイズ**: マネージド ディスクは、プロビジョニング済みサイズに対して課金されます。 Azure では、プロビジョニング済みサイズ (切り上げたもの) は、提供される最も近いディスク サイズにマップされます。 提供されるディスク サイズの詳細については、前の表を参照してください。 各ディスクは、サポートされているプロビジョニング済みディスク サイズのオファリングにマップされ、それに応じて課金されます。 たとえば、200 GiB の Standard SSD をプロビジョニングした場合、E15 (256 GiB) のディスク サイズのプランにマップされます。 プロビジョニングされたディスクには、Premium Storage プランの月額料金を使用して、時間割りで計算して課金されます。 たとえば、E10 ディスクをプロビジョニングし、20 時間後にそのディスクを削除した場合は、E10 製品の 20 時間分に対して課金されます。 ディスクに書き込まれた実際のデータ量には関係ありません。

**スナップショット**: スナップショットは、使用されているサイズに基づいて請求されます。 たとえば、64 GiB のプロビジョニング済み容量でマネージド ディスクのスナップショットを作成し、実際に使用されたデータ サイズが 10 GiB である場合、スナップショットは使用されたデータ サイズである 10 GiB 分のみ課金されます。