---
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 02/13/2019
ms.author: danlep
ms.openlocfilehash: f8821060b98ebfc954a6e59abad60350e6779b76
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553775"
---
| リソース | 既定の制限 |
| --- | :--- |
| [サブスクリプション](../articles/billing-buy-sign-up-azure-subscription.md)あたりのコンテナー グループ数 | 100<sup>1</sup> |
| コンテナー グループあたりのコンテナーの数 | 60 |
| コンテナー グループあたりのボリュームの数 | 20 |
| IP あたりのポートの数 | 5 |
| コンテナー インスタンスのログ サイズ - 実行中のインスタンス | 4 MB |
| コンテナー インスタンスのログ サイズ - 停止したインスタンス | 16 KB または 1,000 行 |
| 1 時間あたりのコンテナーの作成件数 |300<sup>1</sup> |
| 5 分あたりのコンテナーの作成件数 | 100<sup>1</sup> |
| 1 時間あたりのコンテナーの削除件数 | 300<sup>1</sup> |
| 5 分あたりのコンテナーの削除件数 | 100<sup>1</sup> |


<sup>1</sup>制限値の引き上げを要求するには、[Azure サポート リクエスト]を作成してください。[azure-support]<br />

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
