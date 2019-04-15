---
title: Azure Stack のクォータの種類 | Microsoft Docs
description: Azure Stack のサービスとリソースで使用可能なさまざまなクォータの種類を表示して編集します。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: sethm
ms.reviewer: xiaofmao
ms.lastreviewed: 12/07/2018
ms.openlocfilehash: 848b2a0c912a00a2185d7e4b7b8d8446bc1f6aca
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2019
ms.locfileid: "58497178"
---
# <a name="quota-types-in-azure-stack"></a>Azure Stack のクォータの種類

*適用対象:Azure Stack 統合システムと Azure Stack Development Kit*

[クォータ](azure-stack-plan-offer-quota-overview.md#plans)は、ユーザー サブスクリプションがプロビジョニングまたは使用できるリソースの制限を定義します。 たとえば、1 つのクォータでユーザー 1 人に最大 5 つの仮想マシンの作成を許可できます。 各リソースは、独自のクォータの種類を持つことが可能です。

> [!IMPORTANT]
> ユーザー ポータルで新しいクォータが使用可能になるまで、または変更されたクォータが適用されるまでに最大で 2 時間かかることがあります。

## <a name="compute-quota-types"></a>Compute クォータの種類

| **Type** | **既定値** | **説明** |
| --- | --- | --- |
| [Maximum number of virtual machines]\(仮想マシンの最大数\) | 50 | 1 つのサブスクリプションでこの場所に作成できる仮想マシンの最大数です。 |
| [Maximum number of virtual machine cores]\(仮想マシン コアの最大数\) | 100 | 1 つのサブスクリプションでこの場所に作成できるコアの最大数です (たとえば、A3 VM のコア数は 4 です)。 |
| [Maximum number of availability sets]\(可用性セットの最大数\) | 10 | この場所に作成できる可用性セットの最大数です。 |
| [Maximum number of virtual machine scale sets]\(仮想マシン スケール セットの最大数\) | 100 | この場所に作成できる仮想マシン スケールセットの最大数です。 |
| [Maximum capacity (in GB) of standard managed disk]\(Standard マネージド ディスクの最大容量 (GB)\) | 2048 | この場所に作成できる Standard マネージド ディスクの最大容量。 |
| [Maximum capacity (in GB) of premium managed disk]\(Premium マネージド ディスクの最大容量 (GB)\) | 2048 | この場所に作成できる Premium マネージド ディスクの最大容量。 |

> [!NOTE]  
> アンマネージド ディスク (ページ BLOB) の最大容量は、マネージド ディスクのクォータとは区別されます。 この値は**ストレージ クォータ**で設定できます。

## <a name="storage-quota-types"></a>Storage クォータの種類

| **項目** | **既定値** | **説明** |
| --- | --- | --- |
| 最大容量 (GB) |2048 |1 つのサブスクリプションがこの場所で使用できるストレージの総容量です (BLOB と、関連付けられているすべてのスナップショット、テーブル、キューが含まれます)。 |
| [Total number of storage accounts]\(ストレージ アカウントの合計数\) |20 |1 つのサブスクリプションでこの場所に作成できるストレージ アカウントの最大数です。 |

> [!NOTE]  
> マネージド ディスクの最大容量は、ストレージ クォータの合計とは区別されます。 この値は**コンピューティング クォータ**で設定できます。

## <a name="network-quota-types"></a>Network クォータの種類

| **項目** | **既定値** | **説明** |
| --- | --- | --- |
| [Maximum public IPs]\(パブリック IP の最大数\) |50 |1 つのサブスクリプションでこの場所に作成できるパブリック IP アドレスの最大数です。 |
| [Maximum virtual networks]\(仮想ネットワークの最大数\) |50 |1 つのサブスクリプションでこの場所に作成できる仮想ネットワークの最大数です。 |
| [Maximum virtual network gateways]\(仮想ネットワーク ゲートウェイの最大数\) |1 |1 つのサブスクリプションでこの場所に作成できる仮想ネットワーク ゲートウェイ (VPN ゲートウェイ) の最大数です。 |
| [Maximum network connections]\(ネットワーク接続の最大数\) |2 |1 つのサブスクリプションで、すべての仮想ネットワーク ゲートウェイにまたがってこの場所に作成できるネットワーク接続 (ポイント対ポイントまたはサイト対サイト) の最大数です。 |
| [Maximum load balancers]\(ロード バランサーの最大数\) |50 |1 つのサブスクリプションでこの場所に作成できるロード バランサーの最大数です。 |
| [Maximum NICs]\(最大 NIC 数\) |100 |1 つのサブスクリプションでこの場所に作成できるネットワーク インターフェイスの最大数です。 |
| [Maximum network security groups]\(ネットワーク セキュリティ グループの最大数\) |50 |1 つのサブスクリプションでこの場所に作成できるネットワーク セキュリティ グループの最大数です。 |

## <a name="view-an-existing-quota"></a>[View an existing quota]\(既存のクォータの表示\)

既存のクォータを表示するには、2 つの異なる方法があります。

### <a name="plans"></a>プラン

1. 管理者ポータルの左側のナビゲーション ウィンドウで、**[プラン]** を選択します。
2. 詳細を表示するプランの名前をクリックして選択します。
3. 表示されたブレードで、**[サービスとクォータ]** を選択します。
4. 表示するクォータの **[名前]** 列をクリックして選択します。

    [![クォータ](media/azure-stack-quota-types/quotas1sm.png "クォータの表示")](media/azure-stack-quota-types/quotas1.png#lightbox)

### <a name="resource-providers"></a>リソース プロバイダー

1. 管理ポータルの既定のダッシュボードで、**[リソース プロバイダー]** タイルを見つけます。
2. **[コンピューティング]**、**[ネットワーク]**、**[ストレージ]** など、表示するクォータを含むサービスを選択します。
3. **[クォータ]** を選択して、表示するクォータを選択します。

## <a name="edit-a-quota"></a>クォータの編集

クォータを編集するには、2 つの異なる方法があります。

### <a name="edit-a-plan"></a>プランを編集する

1. 管理者ポータルの左側のナビゲーション ウィンドウで、**[プラン]** を選択します。
2. 編集するプランの名前をクリックして選択します。
3. 表示されたブレードで、**[サービスとクォータ]** を選択します。
4. 編集するクォータの **[名前]** 列をクリックして選択します。
    [![クォータ](media/azure-stack-quota-types/quotas1sm.png "クォータの表示")](media/azure-stack-quota-types/quotas1.png#lightbox)

5. 表示されたブレードで、**[Edit in Compute]\(コンピューティングで編集\)**、**[Edit in Network]\(ネットワークで編集\)**、または **[Edit in Storage]\(\)** を選択します。
    ![クォータ](media/azure-stack-quota-types/quotas3.png "クォータの表示")

または、次の手順に従ってクォータを編集できます。

1. 管理者ポータルの既定のダッシュボードで、**[リソース プロバイダー]** タイルを見つけます。
2. **[計算]**、**[ネットワーク]**、**[ストレージ]** など、変更するクォータを含んだサービスを選択します。
3. 次に、**[クォータ]** を選択して、変更するクォータを選択します。
4. **[Set Storage quotas]\(ストレージのクォータの設定\)**、**[Set Compute quotas]\(コンピューティングのクォータの設定\)**、または **[Set Network quotas]\(ネットワークのクォータの設定\)** ウィンドウ (編集するために選択したクォータの種類によって決まります) で、**[保存]** を選択します。

### <a name="edit-original-configuration"></a>元の構成を編集する
  
[アドオン プランを使用](create-add-on-plan.md)する代わりに、クォータの元の構成を編集することもできます。 クォータを編集すると、そのクォータを使うすべてのプランおよびそれらのプランを使う既存のサブスクリプションすべてに対してグローバルに、新しい構成が自動的に適用されます。 クォータの編集は、ユーザーが任意でサブスクライブする変更されたクォータをアドオン プランで提供するときとは異なります。

変更されたクォータを使うすべてのプランおよびそれらのプランを使う既存の全サブスクリプションに対して、クォータの新しい値がグローバルに適用されます。

## <a name="next-steps"></a>次の手順

- [プラン、オファーおよびクォータについて](azure-stack-plan-offer-quota-overview.md)
- [プランの作成時にクォータを作成する](azure-stack-create-plan.md)
