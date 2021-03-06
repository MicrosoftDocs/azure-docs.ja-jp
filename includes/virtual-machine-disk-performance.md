---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 09/25/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 4770ac0181c64ef800aa02ba87284c8add357e36
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92518070"
---
この記事は、ディスクのパフォーマンスと、Azure Virtual Machines と Azure ディスクを組み合わせたときの動作のしくみを明確にするために役立ちます。 ディスク IO のボトルネックを診断する方法と、パフォーマンスを最適化するために行うことができる変更についても説明します。

## <a name="how-does-disk-performance-work"></a>ディスクのパフォーマンス動作のしくみ
Azure 仮想マシンには、仮想マシンの種類とサイズに基づく 1 秒あたりの入出力操作 (IOPS) とスループットのパフォーマンス制限があります。 OS ディスクとデータ ディスクを仮想マシンに接続できます。 ディスクには、独自の IOPS とスループットの制限があります。

仮想マシンまたは接続されているディスクに割り当てられているものを超える IOPS またはスループットが要求された場合、アプリケーションのパフォーマンスは上限に達します。 上限に達した場合、アプリケーションのパフォーマンスは最適とはいえません。 これにより、待機時間の増加などの悪影響が生じる可能性があります。 この概念を明確にするために、いくつかの例を見てみましょう。 これらの例を簡単に追跡できるように、IOPS のみを確認します。 ただし、スループットにも同じロジックが適用されます。

## <a name="disk-io-capping"></a>ディスク IO の上限

**セットアップ:**

- Standard_D8s_v3
  - キャッシュされない IOPS:12,800
- E30 OS ディスク
  - IOPS:500
- 2 台の E30 データ ディスク × 2
  - IOPS:500

![ディスク レベルの上限を示す図。](media/vm-disk-performance/disk-level-throttling.jpg)

仮想マシンで実行されるアプリケーションで、仮想マシンに 10,000 IOPS を必要とする要求を行います。 Standard_D8s_v3 仮想マシンは最大 12,800 IOPS を実行できるため、VM でそのすべてが可能です。

10,000 IOPS 要求は、異なるディスクに対する 3 つの異なる要求に分割されます。

- オペレーティング システム ディスクに 1,000 IOPS が要求されます。
- 各データ ディスクに 4,500 IOPS が要求されます。

接続されているディスクはすべて E30 ディスクであり、500 IOPS のみを処理できます。 そのため、それぞれ 500 IOPS で応答します。 接続されたディスクによってアプリケーションのパフォーマンスが制限され、1,500 IOPS しか処理できません。 Premium SSD P30 ディスクなどのパフォーマンスが高いディスクを使用した場合、アプリケーションが 10,000 IOPS のピーク パフォーマンスで動作する可能性があります。

## <a name="virtual-machine-io-capping"></a>仮想マシンの IO 上限

**セットアップ:**

- Standard_D8s_v3
  - キャッシュされない IOPS:12,800
- P30 OS ディスク
  - IOPS:5,000
- 2 台の P30 データ ディスク × 2
  - IOPS:5,000

![仮想マシン レベルの上限を示す図。](media/vm-disk-performance/vm-level-throttling.jpg)

仮想マシンで実行されるアプリケーションで、15,000 IOPS を必要とする要求を行います。 残念ながら、Standard_D8s_v3 仮想マシンは 12,800 IOPS を処理するようにのみプロビジョニングされています。 アプリケーションで仮想マシンの制限による上限が発生し、割り当て済みの 12,800 IOPS を割り当てる必要があります。

要求された 12,800 IOPS は、異なるディスクに対する 3 つの異なる要求に分割されます。

- オペレーティング システム ディスクに 4,267 IOPS が要求されます。
- 各データ ディスクに 4,266 IOPS が要求されます。

接続されているディスクはすべて P30 ディスクであり、5,000 IOPS を処理できます。 そのため、要求された量で応答します。
