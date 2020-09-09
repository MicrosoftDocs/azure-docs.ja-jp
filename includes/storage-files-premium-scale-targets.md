---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 08/10/2020
ms.author: rogarana
ms.openlocfilehash: d704c6026e9d007a7365a3b72649ca509585da4d
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2020
ms.locfileid: "88057677"
---
#### <a name="additional-premium-file-share-level-limits"></a>Premium ファイル共有レベルの追加の制限

|領域  |移行先  |
|---------|---------|
|最小サイズ増減    |1 GiB      |
|ベースライン IOPS    |GiB あたり 1 IOPS (最大 100,000)|
|IOPS バースト    |GiB あたり 3x IOPS (最大 100,000)|
|エグレス レート         |60 MiB/s + 0.06 * プロビジョニング済み GiB        |
|イングレス レート| 40 MiB/s + 0.04 * プロビジョニング済み GiB |

#### <a name="file-level-limits"></a>ファイル レベルの制限

|領域  |Standard ファイル  |Premium ファイル  |
|---------|---------|---------|
|Size     |1 TiB         |4 TiB         |
|ファイルあたりの最大 IOPS      |1,000         |5,000         |
|コンカレント処理数     |2,000         |2,000         |
|エグレス     |Standard ファイルのスループット値を参照してください         |300 MiB/秒         |
|イングレス     |Standard ファイルのスループット値を参照してください         |200 MiB/秒         |
|スループット     |最大 60 MiB/秒         |Premium ファイルのイングレスとエグレスの値を参照してください         |