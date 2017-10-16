---
title: "Azure イベント ハブを作成する | Microsoft Docs"
description: "Azure Portal を使用して Azure Event Hubs 名前空間とイベント ハブを作成します"
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: ff99e327-c8db-4354-9040-9c60c51a2191
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/01/2017
ms.author: sethm
ms.openlocfilehash: 816bf1426704d3391550e80c0700f1b011683a94
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2017
---
# <a name="create-an-event-hubs-namespace-and-an-event-hub-using-the-azure-portal"></a>Azure Portal を使用して Event Hubs 名前空間とイベント ハブを作成する

## <a name="create-an-event-hubs-namespace"></a>Event Hubs 名前空間を作成します
1. [Azure Portal][Azure portal] にログインし、画面の左上にある **[新規]** をクリックします。
1. **[モノのインターネット]**、**[Event Hubs]** の順にクリックします。
   
    ![](./media/event-hubs-create/create-event-hub9.png)
1. **[名前空間の作成]** ブレードで、名前空間の名前を入力します。 その名前が使用できるかどうかがすぐに自動で確認されます。
   
    ![](./media/event-hubs-create/create-event-hub1.png)
1. 入力した名前空間の名前が使用できることを確認したら、価格レベル (Basic または Standard) を選択します。 Azure サブスクリプション、リソース グループ、リソースが作成される場所を選択します。 
1. **[作成]** をクリックして、名前空間を作成します。 システム リソースを完全にプロビジョニングするまで、しばらく時間を待機する必要があります。
2. 名前空間のポータルの一覧で、新しく作成された名前空間をクリックします。
2. をクリックして**共有アクセス ポリシー**、クリックして**RootManageSharedAccessKey**です。
    
    ![](./media/event-hubs-create/create-event-hub7.png)

3. コピー ボタンをクリックして **RootManageSharedAccessKey** 接続文字列をクリップボードにコピーします。 後で使用する、メモ帳などの一時的な場所にこの接続文字列を保存します。
    
    ![](./media/event-hubs-create/create-event-hub8.png)

## <a name="create-an-event-hub"></a>イベント ハブの作成

1. イベント ハブ名前空間の一覧で、新しく作成された名前空間をクリックします。      
   
    ![](./media/event-hubs-create/create-event-hub2.png) 

2. 名前空間ブレードで **[Event Hubs]**をクリックします。
   
    ![](./media/event-hubs-create/create-event-hub3.png)

1. ブレード上部の **[イベント ハブの追加]**をクリックします。
   
    ![](./media/event-hubs-create/create-event-hub4.png)
1. イベント ハブの名前を入力し、 **[作成]**をクリックします。
   
    ![](./media/event-hubs-create/create-event-hub5.png)

Event Hubs が作成され、イベントの送受信に必要な接続文字列が手元にあります。

## <a name="next-steps"></a>次のステップ
Event Hubs の詳細については、次のリンク先を参照してください。

* [Event Hubs の概要](event-hubs-what-is-event-hubs.md)
* [Event Hubs API 概要](event-hubs-api-overview.md)

[Azure portal]: https://portal.azure.com/