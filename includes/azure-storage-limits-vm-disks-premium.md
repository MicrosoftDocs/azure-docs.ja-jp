---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: rogarana
ms.openlocfilehash: e7e57c6a821731874dcb1d99a3133b6ede1da26e
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "67181164"
---
**Premium 非管理対象の仮想マシン ディスク: アカウントあたりの制限**

| リソース | 既定の制限 |
| --- | --- |
| アカウントごとのディスク容量合計 |35 TB |
| アカウントごとのスナップショット容量合計 |10 TB |
| アカウントごとの最大帯域幅 (受信 + 送信)<sup>1</sup> |50 Gbps 以下 |

<sup>1</sup>*受信*とはストレージ アカウントに送信されるすべてのデータのことです。 *送信*とは、ストレージ アカウントから受信するすべてのデータのことです。

**Premium 非管理対象の仮想マシン ディスク: ディスクあたりの制限**

| Premium Storage ディスクの種類 | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- |
| ディスク サイズ |128 GiB |512 GiB |1,024 GiB (1 TB) |2,048 GiB (2 TB)|4,095 GiB (4 TB)|
| ディスクあたりの最大 IOPS |500 |2,300 |5,000 |7,500 |7,500 |
| ディスクあたりの最大スループット |100 MB/秒 | 150 MB/秒 |200 MB/秒 |250 MB/秒 |250 MB/秒 |
| ストレージ アカウントあたりの最大ディスク数 |280 |70 |35 | 17 | 8 |

**Premium 非管理対象の仮想マシン ディスク: VM あたりの制限**

| リソース | 既定の制限 |
| --- | --- |
| VM あたりの最大 IOPS |GS5 VM で 80,000 IOPS |
| VM あたりの最大スループット |GS5 VM で 2,000 MB/秒 |

