---
title: Azure NetApp Files リージョン間レプリケーションのボリューム レプリケーションまたはボリュームを削除する | Microsoft Docs
description: ソース ボリュームとコピー先のボリュームの間で不要になったレプリケーション接続を削除する方法について説明します。
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
ms.topic: how-to
ms.date: 11/18/2020
ms.author: b-juche
ms.openlocfilehash: 5ce7a591acd8203775808457219b0ec392cd696e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95249896"
---
# <a name="delete-volume-replications-or-volumes"></a>ボリューム レプリケーションまたはボリュームを削除する

この記事では、ボリューム レプリケーションを削除する方法について説明します。 また、ソース ボリュームまたはターゲット ボリュームを削除する方法についても説明します。

## <a name="delete-volume-replications"></a>ボリューム レプリケーションを削除する

ボリュームのレプリケーションを削除することで、ソース ボリュームとコピー先のボリューム間のレプリケーション接続を終了できます。 レプリケーションは、ターゲット ボリュームからが削除する必要があります。 削除操作では、レプリケーションの承認のみが削除されます。ソース ボリュームまたはコピー先のボリュームは削除されません。 

1. ボリューム レプリケーションを削除する前に、レプリケーション ピアリングが中断されていることを確認してください。 レプリケーション ピアリングを中断するには: 

    1. *コピー先* のボリュームを選択します。 [ストレージ サービス] の **[レプリケーション]** をクリックします。  

    2.  続行する前に、次のフィールドを確認してください。  
        * ミラーの状態に、***[Mirrored]\(ミラー化\)*** と表示されていることを確認します。   
            ミラーの状態が *[Uninitialized]\(未初期化\)* と表示されている場合は、レプリケーションのピアリングを中断しないようにしてください。
        * リレーションシップの状態に ***[Idle]\(アイドル状態\)*** と表示されていることを確認します。   
            リレーションシップの状態が *[Transferring]\(転送中\)* と表示されている場合は、レプリケーションのピアリングを中断しないようにしてください。   

        「[レプリケーション関係の正常性状態を表示する](cross-region-replication-display-health-status.md)」を参照してください。 

    3.  **[Break Peering]\(ピアリングの中断\)** をクリックします。  

    4.  確認を求められたら「**Yes**」と入力し、 **[中断]** をクリックします。 

        ![レプリケーション ピアリングの中断](../media/azure-netapp-files/cross-region-replication-break-replication-peering.png)


1. ボリュームのレプリケーションを削除するには、ソースまたはコピー先のボリュームから **[レプリケーション]** を選択します。  

2. **[削除]** をクリックします。    

3. 「**はい**」と入力して削除を確認し、 **[削除]** をクリックします。   

    ![レプリケーションの削除](../media/azure-netapp-files/cross-region-replication-delete-replication.png)

## <a name="delete-source-or-destination-volumes"></a>ソース ボリュームまたはターゲット ボリュームの削除

ソース ボリュームまたはターゲット ボリュームを削除する場合は、次の手順を順番どおりに実行する必要があります。 これに従わない場合は、`Volume with replication cannot be deleted`エラーが発生します。  

1. ターゲット ボリュームから[ボリューム レプリケーションを削除します](#delete-volume-replications)。   

2. ボリューム名を右クリックし、 **[削除]** を選択して、ターゲット ボリュームまたはソース ボリュームを、必要に応じて削除します。   

    ![ボリュームの右クリック メニューを示すスクリーンショット。](../media/azure-netapp-files/cross-region-replication-delete-volume.png)

## <a name="next-steps"></a>次のステップ  

* [リージョン間レプリケーション](cross-region-replication-introduction.md)
* [リージョン間レプリケーションを使用するための要件と考慮事項](cross-region-replication-requirements-considerations.md)
* [レプリケーション関係の正常性状態を表示する](cross-region-replication-display-health-status.md)
* [リージョン間レプリケーションのトラブルシューティング](troubleshoot-cross-region-replication.md)

