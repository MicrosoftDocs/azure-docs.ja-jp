---
title: Azure NetApp Files のリソース制限 | Microsoft Docs
description: NetApp アカウント、容量プール、ボリューム、スナップショット、委任されたサブネットの制限など、Azure NetApp Files のリソース制限について説明します。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: b-juche
ms.openlocfilehash: b55467d77beb8f97b8e392b72682268ae0407e54
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "65826369"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Azure NetApp Files のリソース制限

Azure NetApp Files のリソース制限を理解すると、ボリュームの管理に役立ちます。

## <a name="resource-limits"></a>リソース制限

次の表は、Azure NetApp Files のリソース制限について説明しています。

|  Resource  |  既定の制限  |  サポート要求による調整の可否  |
|----------------|---------------------|--------------------------------------|
|  Azure サブスクリプションあたりの NetApp アカウント数   |  10    |  はい   |
|  NetApp アカウントあたりの容量プールの数   |    25     |   はい   |
|  容量プールあたりのボリュームの数     |    500   |    はい     |
|  ボリュームあたりのスナップショット数       |    255     |    いいえ        |
|  Azure Virtual Network あたりの Azure NetApp Files (Microsoft.NetApp/volumes) に委任されたサブネットの数    |   1   |    いいえ    |
|  ボリュームに接続可能な VM (ピアリングされた VNet を含む) の最大数     |    1000   |    いいえ   |
|  単一の容量プールの最小サイズ   |  4 TiB     |    いいえ  |
|  単一の容量プールの最大サイズ    |  500 TiB   |   いいえ   |
|  単一のボリュームの最小サイズ    |    100 GiB    |    いいえ    |
|  単一のボリュームの最大割り当てクォータ*   |   92 TiB   |    いいえ   |
|  単一のボリュームの最大サイズ*     |    100 TiB    |    いいえ       |

\* ボリュームは、92 TiB まで手動で作成またはサイズ変更できます。 ただし、超過シナリオでは 100 TiB までボリュームを拡張できます。 容量の超過の詳細については、「[Azure NetApp Files のコスト モデル](azure-netapp-files-cost-model.md)」を参照してください。 

## <a name="request-limit-increase"></a>上限の引き上げを要求する 

上記の表から調整可能な上限を引き上げるように、Azure サポート要求を作成できます。 

Azure portal ナビゲーション プレーンから: 

1. **[ヘルプとサポート]** をクリックします。
2. **[+ New support request]** (新しいサポート要求) をクリックします。
3. [基本] タブで次の情報を指定します。 
    1. [問題の種類]: **[サービスとサブスクリプションの制限 (クォータ)]** を選択します。
    2. [サブスクリプション]:クォータの増加が必要なリソースのサブスクリプションを選択します。
    3. [クォータの種類]: **[Storage:Azure NetApp Files limits]\(ストレージ: Azure NetApp Files の制限\)** を選択します。
    4. **次へ: 「解決方法」** を参照してください。
4. [詳細] タブで次の操作を実行します。
    1. [説明] ボックスで、対応するリソースの種類に対して、次の情報を指定します。

        |  Resource  |    親リソース      |    要求された新しい制限     |    クォータの引き上げの理由       |
        |----------------|------------------------------|---------------------------------|------------------------------------------|
        |  Account |  *サブスクリプション ID*   |  *要求された新しい最大**アカウント**数*    |  *どのシナリオやユース ケースが要求を求めたか?*  |
        |  プール    |  *サブスクリプション ID、アカウント URI*  |  *要求された新しい最大**プール**数*   |  *どのシナリオやユース ケースが要求を求めたか?*  |
        |  ボリューム  |  *サブスクリプション ID、アカウント URI、プール URI*   |  *要求された新しい最大**ボリューム**数*     |  *どのシナリオやユース ケースが要求を求めたか?*  |

    2. 適切なサポート方法を指定し、契約情報を指定します。

    3. **[次へ: 確認および作成]** をクリックして要求を作成します。 


## <a name="next-steps"></a>次の手順  

- [Azure NetApp Files のストレージ階層を理解する](azure-netapp-files-understand-storage-hierarchy.md)
- [Azure NetApp Files のコスト モデル](azure-netapp-files-cost-model.md)
