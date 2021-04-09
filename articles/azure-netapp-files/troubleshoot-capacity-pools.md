---
title: Azure NetApp Files の容量プールに関する問題をトラブルシューティングする | Microsoft Docs
description: 容量プールを管理するときに発生する可能性のある問題について説明し、問題の解決策を示します。
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
ms.topic: troubleshooting
ms.date: 01/14/2021
ms.author: b-juche
ms.openlocfilehash: 759759b67582b241d0bab1e043dd15e54a804faf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98251541"
---
# <a name="troubleshoot-capacity-pool-issues"></a>容量プールに関する問題のトラブルシューティング

この記事では、プールの変更操作など、容量プールを管理するときに発生する可能性がある問題の解決策について説明します。 

## <a name="issues-managing-a-capacity-pool"></a>容量プールの管理に関する問題 

|     エラー状態    |     解像度    |
|-|-|
| 容量プールの作成に関する問題 |  容量プールの数が制限を超えていないことを確認してください。 「[Azure NetApp Files のリソース制限](azure-netapp-files-resource-limits.md)」を参照してください。  数が制限を下回っていても問題が発生する場合は、サポート チケットを提出し、容量プール名を指定します。 |
| 容量プールの削除に関する問題  |  容量プールを削除しようとしているサブスクリプション内で、Azure NetApp Files のすべてのボリュームとスナップショットを削除するようにしてください。 <br> 既にすべてのボリュームとスナップショットを削除しても容量プールを削除できない場合は、ポータルに表示されることなく、リソースへの参照がまだ存在している可能性があります。 この場合は、サポート チケットを提出し、上記の推奨される手順を実行したことを指定してください。 |
| ボリュームの作成または変更が `Requested throughput not available` エラーで失敗する | 1 つのボリュームで使用可能なスループットは、容量プールのサイズとサービス レベルによって決まります。 十分なスループットがない場合は、プール サイズを増やすか、既存のボリュームのスループットを調整する必要があります。 | 

## <a name="issues-when-changing-the-capacity-pool-of-a-volume"></a>ボリュームの容量プールを変更するときの問題 

|     エラー状態    |     解像度    |
|-|-|
| ボリュームの容量プールを変更することが許可されない。 | この機能を使用することがまだ承認されていない可能性があります。 <br> ボリュームを別の容量プールに移動する機能は、現在プレビューの段階です。 この機能を初めて使用する場合は、まず機能を登録し、`-FeatureName ANFTierChange` を設定する必要があります。 「[ボリュームのサービス レベルを動的に変更する](dynamic-change-volume-service-level.md)」の登録手順を参照してください。 |
| ボリュームの合計サイズに対して容量プールのサイズが小さすぎる。 |  このエラーは、移動先の容量プールに、移動しようとしているボリュームのために使用できる容量がないことの結果です。  <br> 移動先のプールのサイズを増やすか、より大きな別のプールを選択してください。  「[容量プールまたはボリュームをサイズ変更する](azure-netapp-files-resize-capacity-pools-or-volumes.md)」を参照してください。   |
|  `'{source pool name}'` という名前のボリュームが移動先プール `'{target pool name}'` に既に存在するため、プールの変更を完了できない | このエラーは、同じ名前のボリュームが、移動先の容量プールに既に存在しているために発生します。  同じ名前のボリュームがない、別の容量プールを選択してください。   | 

## <a name="next-steps"></a>次のステップ  

* [容量プールを設定する](azure-netapp-files-set-up-capacity-pool.md)
* [手動 QoS 容量プールを管理する](manage-manual-qos-capacity-pool.md)
* [ボリュームのサービス レベルを動的に変更する](dynamic-change-volume-service-level.md)
* [容量プールまたはボリュームをサイズ変更する](azure-netapp-files-resize-capacity-pools-or-volumes.md)
