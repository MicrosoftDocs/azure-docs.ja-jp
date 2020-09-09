---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/23/2020
ms.author: tamram
ms.openlocfilehash: 43a72d915fa5a00c54bef7a06fe3815a7d63f2fc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85805620"
---
| リソース | 移行先 | ターゲット (プレビュー) |
|-|-|-|
| 単一の BLOB コンテナーの最大サイズ | ストレージ アカウントの最大容量と同じ |  |
| ブロック BLOB 内または追加 BLOB 内の最大ブロック数 | 50,000 ブロック |  |
| ブロック BLOB 内の最大ブロック サイズ | 100 MiB | 4000 MiB (プレビュー) |
| ブロック BLOB の最大サイズ | 50,000 x 100 MiB (約 4.75 TiB) | 50,000 x 4000 MiB (約 190.7 TiB) (プレビュー) |
| 追加 BLOB のブロックの最大サイズ | 4 MiB |  |
| 追加 BLOB の最大サイズ | 50,000 x 4 MiB (約 195 GiB) |  |
| ページ BLOB の最大サイズ | 8 TiB |  |
| BLOB コンテナーごとの保存されるアクセス ポリシーの最大数 | 5 |  |
| 1 つの BLOB のターゲット要求率 | 最大 500 要求/秒 |  |
| 1 つのページ BLOB のターゲット スループット | 最大 60 MiB/秒 |  |
| 1 つのブロック BLOB のターゲット スループット | ストレージ アカウントイングレス/エグレスの上限まで<sup>1</sup> |  |

<sup>1</sup> 1 つの BLOB のスループットはいくつかの要因に左右されます。それにはコンカレンシー、要求サイズ、パフォーマンス レベル、アップロード元の速度、ダウンロード先などがありますが、それらに限定されません。 [ 高スループット ブロック BLOB](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/) のパフォーマンス機能強化を活用するには、もっと大きな BLOB またはブロックをアップロードします。 具体的には、[Put Blob](/rest/api/storageservices/put-blob) または [Put Block](/rest/api/storageservices/put-block) 操作を呼び出すとき、標準ストレージ アカウントの 4 MiB を超える BLOB またはブロック サイズを指定します。 プレミアム ブロック BLOB や Data Lake Storage Gen2 ストレージ アカウントの場合、256 KiB を超えるブロックまたは BLOB サイズを使用します。

次の表では、サービス バージョンで許可される最大ブロック サイズと BLOB サイズについて説明します。

| サービスのバージョン | 最大ブロック サイズ (Put Block 使用) | 最大 BLOB サイズ (Put Block List 使用) | 1 回の書き込み操作での最大 BLOB サイズ (Put Blob 使用) |
|-|-|-|-|
| バージョン 2019-12-12 以降 | 4000 MiB (プレビュー) | 約 190.7 TiB (4000 MiB x 50,000 ブロック) (プレビュー) | 5000 MiB (プレビュー) |
| バージョン 2016-05-31 からバージョン 2019-07-07 | 100 MiB | 約 4.75 TiB (100 MiB x 50,000 ブロック) | 256 MiB |
| 2016-05-31 よりも前のバージョン | 4 MiB | 約 195 GiB (4 MiB x 50,000 ブロック) | 64 MiB |
