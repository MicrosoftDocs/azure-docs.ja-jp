---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 4/20/2019
ms.author: tamram
ms.openlocfilehash: aab17966862c57a52f252b3c4e9b757673078b0a
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "67181387"
---
| リソース | ターゲット        |
|----------|---------------|
| 単一の BLOB コンテナーの最大サイズ | ストレージ アカウントの最大容量と同じ |
| ブロック BLOB 内または追加 BLOB 内の最大ブロック数 | 50,000 ブロック |
| ブロック BLOB 内の最大ブロック サイズ | 100 MiB |
| ブロック BLOB の最大サイズ | 50,000 x 100 MiB (約 4.75 TiB) |
| 追加 BLOB のブロックの最大サイズ | 4 MiB |
| 追加 BLOB の最大サイズ | 50,000 x 4 MiB (約 195 GiB) |
| ページ BLOB の最大サイズ | 8 TiB |
| BLOB コンテナーごとの保存されるアクセス ポリシーの最大数 | 5 |
|単一 BLOB のターゲット スループット |ストレージ アカウントイングレス/エグレスの上限まで<sup>1</sup> |

<sup>1</sup> 1 回のオブジェクト スループットはいくつかの要因に左右されます。それにはコンカレンシー、要求サイズ、パフォーマンス レベル、アップロード元の速度、ダウンロード先などがありますが、それらに限定されません。 [高スループット ブロック BLOB](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/) パフォーマンス拡張を活用するには、サイズが 4 MiB 以上のプット BLOB またはプット ブロック要求を使用します (プレミアムパフォーマンス ブロック BLOB ストレージまたは Data Lake Storage Gen2 の場合は 256 KiB 以上)。
