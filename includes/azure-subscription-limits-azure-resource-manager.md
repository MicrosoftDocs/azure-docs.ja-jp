---
title: インクルード ファイル
description: インクルード ファイル
services: billing
author: rothja
ms.service: billing
ms.topic: include
ms.date: 04/02/2019
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: d490cab4d437c30fdb211ea27397777afc27e72e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59804476"
---
| Resource | 既定の制限 | 上限 |
| --- | --- | --- |
|  [サブスクリプション](../articles/billing-buy-sign-up-azure-subscription.md) |リージョンあたり 25,000<sup>1</sup>。 |リージョンあたり 25,000。 |
| [サブスクリプション](../articles/billing-buy-sign-up-azure-subscription.md)あたりの VM の合計コア数 |リージョンあたり 20<sup>1</sup>。 | サポートにお問い合せください。 |
| [サブスクリプション](../articles/billing-buy-sign-up-azure-subscription.md)あたりのシリーズ (Dv2 や F など) ごとの VM のコア数 |リージョンあたり 20<sup>1</sup>。 | サポートにお問い合せください。 |
| サブスクリプションあたりの[共同管理者](../articles/billing-add-change-azure-subscription-administrator.md)数 |無制限。 |無制限。 |
| サブスクリプションあたりのリージョンごとの[ストレージ アカウント数](../articles/storage/common/storage-quickstart-create-account.md) |250 |250 |
| サブスクリプションあたりの[リソース グループ数](../articles/azure-resource-manager/resource-group-overview.md) |980 |980 |
| サブスクリプションあたりの[可用性セット](../articles/virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy)数 |リージョンあたり 2,000。 |リージョンあたり 2,000。 |
| Azure Resource Manager API 要求サイズ |4,194,304 バイト。 |4,194,304 バイト。 |
| サブスクリプションあたりのタグ数<sup>2</sup> |無制限。 |無制限。 |
| サブスクリプションあたりの一意のタグの計算<sup>2</sup> | 10,000 | 10,000 |
| [クラウド サービス](../articles/cloud-services/cloud-services-choose-me.md) 数 |該当なし<sup>3</sup> |該当なし<sup>3</sup> |
| [アフィニティ グループ](../articles/virtual-network/virtual-networks-migrate-to-regional-vnet.md) 数 |該当なし<sup>3</sup> |該当なし<sup>3</sup> |
| 場所あたりの[サブスクリプション レベルのデプロイ](../articles/azure-resource-manager/deploy-to-subscription.md)数 | 800 | 800 |

<sup>1</sup>既定の制限は、オファー カテゴリの種類 (無料試用版や従量課金制など) とシリーズ (Dv2、F、G など) によって異なります。

<sup>2</sup>1 つのサブスクリプションで適用できるタグの数に制限はありません。 リソースまたはリソース グループあたりのタグの数は 15 に制限されています。 サブスクリプションに存在する[一意のタグ名と値のリスト](/rest/api/resources/tags)が Resource Manager から返されるのは、タグの数が 10,000 以下の場合に限られます。 タグの数が 10,000 を超える場合でも、タグでリソースを検出することはできます。  

<sup>3</sup>これらの機能は、Azure リソース グループと Resource Manager では必要なくなりました。

> [!NOTE]
> 仮想マシンのコア数には、リージョン別総数の制限があります。 さらに、リージョンごとにサイズ シリーズ (Dv2 や F など) あたりの制限もあります。これらの制限は別々に適用されます。 たとえば、米国東部で VM のコア上限が 30、A シリーズのコア上限が 30、D シリーズのコア上限が 30 のサブスクリプションがあるとします。 このサブスクリプションでは、30 個の A1 VM、または 30 台の D1 VM、または合計 30 個のコア数を超えないようにこれらの 2 つを組み合わせでデプロイすることができます。 組み合わせの例としては、10 個の A1 VM と 20 個の D1 VM が考えられます。  
> <!-- -->
> 
> 

