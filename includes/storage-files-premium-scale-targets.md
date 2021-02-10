---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 08/10/2020
ms.author: rogarana
ms.openlocfilehash: 86bf4911026e46c997469b956f9e7c75c4f17164
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/22/2021
ms.locfileid: "98698120"
---
#### <a name="additional-premium-file-share-level-limits"></a>Premium ファイル共有レベルの追加の制限

|領域  |移行先  |
|---------|---------|
|最小サイズ増減    |1 GiB      |
|ベースライン IOPS    |400 + GiB あたり 1 IOPS (最大 100,000)|
|IOPS バースト    |最大 (4000, 3 x GiB あたり IOPS) (最大 100,000)|
|エグレス レート         |60 MiB/s + 0.06 * プロビジョニング済み GiB        |
|イングレス レート| 40 MiB/s + 0.04 * プロビジョニング済み GiB |

#### <a name="file-level-limits"></a>ファイル レベルの制限

|領域  |Standard ファイル  |Premium ファイル  |
|---------|---------|---------|
|Size     |1 TiB         |4 TiB         |
|ファイルあたりの最大 IOPS      |1,000         |最大 8,000*         |
|コンカレント処理数     |2,000         |2,000         |
|エグレス     |Standard ファイルのスループット値を参照してください         |300 MiB/秒 (SMB マルチチャネル プレビューで最大 1 GiB/秒)**         |
|イングレス     |Standard ファイルのスループット値を参照してください         |200 MiB/秒 (SMB マルチチャネル プレビューで最大 1 GiB/秒)**        |
|スループット     |最大 60 MiB/秒         |Premium ファイルのイングレスとエグレスの値を参照してください         |

\* <sup> 読み取りと書き込みの IO に適用 (一般的に IO サイズは 64K 以下)。読み取りと書き込み以外のメタデータ操作の場合、それより低くなることがあります。</sup>

\*\* <sup> コンピューターのネットワーク制限、利用できる帯域幅、IO サイズ、キューの深さ、その他の要因に左右されます。詳しくは「[SMB マルチチャネルのパフォーマンス](../articles/storage/files/storage-files-smb-multichannel-performance.md)」を参照してください。</sup>
