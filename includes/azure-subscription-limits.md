---
title: インクルード ファイル
description: インクルード ファイル
author: rothja
ms.service: azure-resource-manager
ms.topic: include
ms.date: 05/18/2018
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: 74f9c5304237ec77a629bc914d95c345882b784f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95558939"
---
| リソース | 既定の制限 | 上限 |
| --- | --- | --- |
| [サブスクリプション](https://azure.microsoft.com/pricing/)あたりの vCPU 数<sup>1</sup> |20 |10,000 |
| サブスクリプションあたりの[共同管理者](../articles/cost-management-billing/manage/add-change-subscription-administrator.md)数 |200 |200 |
| サブスクリプションあたりの[ストレージ アカウント数](../articles/storage/common/storage-account-create.md)<sup>2</sup> |100 |100 |
| [クラウド サービス](../articles/cloud-services/cloud-services-choose-me.md) 数 |20 |200 |
| [ローカル ネットワーク](/previous-versions/azure/reference/jj157100(v=azure.100)) 数 |10 |500 |
| サブスクリプションあたりの DNS サーバー数 |9 |100 |
| サブスクリプションあたりの予約済み IP 数 |20 |100 |
| [アフィニティ グループ](/previous-versions/azure/virtual-network/virtual-networks-migrate-to-regional-vnet) 数 |256 |256 |
| サブスクリプション名の長さ (文字) | 64 | 64 |

<sup>1</sup>XS インスタンスは、使用する CPU コア数が 1 コアに満たなくても、vCPU 制限を上限として 1 vCPU とカウントされます。

<sup>2</sup>ストレージ アカウントの制限には、Standard および Premium 両方のストレージ アカウントが含まれます。