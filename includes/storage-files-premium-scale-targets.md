---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 06/07/2019
ms.author: rogarana
ms.openlocfilehash: b28427b3ede0cfaeb9e08d3c73b15ea7f2961f1b
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/22/2019
ms.locfileid: "71180058"
---
#### <a name="additional-premium-file-share-level-limits"></a>Premium ファイル共有レベルの追加の制限

|領域  |ターゲット  |
|---------|---------|
|最小サイズ増減    |1 GiB      |
|ベースライン IOPS    |GiB あたり 1 IOPS (最大 100,000)|
|IOPS バースト    |GiB あたり 3x IOPS (最大 100,000)|
|エグレス レート         |60 MiB/s + 0.06 * プロビジョニング済み GiB        |
|イングレス レート| 40 MiB/s + 0.04 * プロビジョニング済み GiB |

#### <a name="file-level-limits"></a>ファイル レベルの制限

|領域  |Premium ファイル  |Standard ファイル |
|---------|---------|---------|
|Size                  |1 TiB         |1 TiB|
|ファイルあたりの最大 IOPS     |5,000         |1,000|
|コンカレント処理数    |2,000         |2,000|
|エグレス  |300 MiB/秒|      Standard ファイルのスループット値を参照してください|
|イングレス  |200 MiB/秒| Standard ファイルのスループット値を参照してください|
|スループット| Premium ファイルのイングレスとエグレスの値を参照してください| 最大 60 MiB/秒|
