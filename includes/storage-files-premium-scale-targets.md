---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 06/07/2019
ms.author: rogarana
ms.openlocfilehash: 368f08272173b019873dfe20e1164d6baf72ff5e
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2019
ms.locfileid: "67542662"
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
|イングレス  |300 MiB/秒|      Standard ファイルのスループット値を参照してください|
|エグレス   |200 MiB/秒| Standard ファイルのスループット値を参照してください|
|スループット| Premium ファイルのイングレスとエグレスの値を参照してください| 最大 60 MiB/秒|