---
title: ファイルを含める
description: インクルード ファイル
author: rothja
ms.service: azure-resource-manager
ms.topic: include
ms.date: 05/18/2018
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: db822722b12921ab98b3e5cae67e28f4ca7ede04
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87298864"
---
| リソース | 既定の制限 | 上限 |
| --- | --- | --- |
| [サブスクリプション](../articles/billing-buy-sign-up-azure-subscription.md)あたりの vCPU 数<sup>1</sup> |20 |10,000 |
| サブスクリプションあたりの[共同管理者](../articles/cost-management-billing/manage/add-change-subscription-administrator.md)数 |200 |200 |
| サブスクリプションあたりの[ストレージ アカウント数](../articles/storage/common/storage-create-storage-account.md)<sup>2</sup> |100 |100 |
| [クラウド サービス](../articles/cloud-services/cloud-services-choose-me.md) 数 |20 |200 |
| [ローカル ネットワーク](/previous-versions/azure/reference/jj157100(v=azure.100)) 数 |10 |500 |
| サブスクリプションあたりの DNS サーバー数 |9 |100 |
| サブスクリプションあたりの予約済み IP 数 |20 |100 |
| [アフィニティ グループ](../articles/virtual-network/virtual-networks-migrate-to-regional-vnet.md) 数 |256 |256 |
| サブスクリプション名の長さ (文字) | 64 | 64 |

<sup>1</sup>XS インスタンスは、使用する CPU コア数が 1 コアに満たなくても、vCPU 制限を上限として 1 vCPU とカウントされます。

<sup>2</sup>ストレージ アカウントの制限には、Standard および Premium 両方のストレージ アカウントが含まれます。 

