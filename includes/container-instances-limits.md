---
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 07/22/2020
ms.author: danlep
ms.openlocfilehash: 6878180ffedfaa53f25d2bdc6db72dcd7dd8b38b
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2020
ms.locfileid: "87384831"
---
| リソース | 制限 |
| --- | :--- |
| 1 リージョン、1 サブスクリプションあたりの Standard SKU コンテナー グループ | 100<sup>1</sup> |
| 1 リージョン、1 サブスクリプションあたりの Dedicated SKU コンテナー グループ | 0<sup>1</sup> |
| コンテナー グループあたりのコンテナーの数 | 60 |
| コンテナー グループあたりのボリュームの数 | 20 |
| 1 リージョン、1 サブスクリプションあたりの Standard SKU コア (CPU) | 10<sup>1、2</sup> | 
| 1 リージョン、1 サブスクリプションあたりの K80 GPU 用 Standard SKU コア (CPU) | 18<sup>1、2</sup> |
| 1 リージョン、1 サブスクリプションあたりの P100 または V100 GPU 用 Standard SKU コア (CPU) | 0<sup>1、2</sup> |
| IP あたりのポートの数 | 5 |
| コンテナー インスタンスのログ サイズ - 実行中のインスタンス | 4 MB |
| コンテナー インスタンスのログ サイズ - 停止したインスタンス | 16 KB または 1,000 行 |
| 1 時間あたりのコンテナーの作成件数 |300<sup>1</sup> |
| 5 分あたりのコンテナーの作成件数 | 100<sup>1</sup> |
| 1 時間あたりのコンテナーの削除件数 | 300<sup>1</sup> |
| 5 分あたりのコンテナーの削除件数 | 100<sup>1</sup> |


<sup>1</sup>制限値の引き上げを要求するには、[Azure サポート リクエスト][azure-support]を作成してください。 [Azure 無料アカウント](https://azure.microsoft.com/offers/ms-azr-0044p/)や [Azure for Students](https://azure.microsoft.com/offers/ms-azr-0170p/) などの無料サブスクリプションは、制限またはクォータの引き上げの適用対象外です。 無料サブスクリプションをご利用の場合は、従量課金制サブスクリプションに[アップグレード](../articles/cost-management-billing/manage/upgrade-azure-subscription.md)してください。<br />
<sup>2</sup>[従量課金制](https://azure.microsoft.com/offers/ms-azr-0003p/)サブスクリプションの既定の制限。 他のカテゴリの種類では制限が異なる場合があります。<br/>

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
