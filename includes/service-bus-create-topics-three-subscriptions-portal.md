---
title: インクルード ファイル
description: インクルード ファイル
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 02/20/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: ace42278269ff6af31902dbecead81329815af12
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "67181411"
---
## <a name="create-a-topic-using-the-azure-portal"></a>Azure Portal を使用したトピックの作成
1. **[Service Bus 名前空間]** ページで、左側のメニューの **[トピック]** を選択します。
2. ツール バーの **[+ トピック]** を選択します。 
4. トピックの **名前** を入力します。 他のオプションは既定値のままにしてください。
5. **[作成]** を選択します。

    ![トピックの作成](./media/service-bus-create-topics-subscriptions-portal/create-topic.png)

## <a name="create-subscriptions-to-the-topic"></a>トピックに対するサブスクリプションを作成する
1. 前のセクションで作成した **トピック** を選択します。 
    
    ![トピックの選択](./media/service-bus-create-topics-subscriptions-portal/select-topic.png)
2. **[Service Bus トピック]** ページで、左側のメニューの **[サブスクリプション]** を選択し、ツール バーの **[+ サブスクリプション]** を選択します。 
    
    ![サブスクリプションの追加ボタン](./media/service-bus-create-topics-subscriptions-portal/add-subscription-button.png)
3. **[サブスクリプションの作成]** ページで、サブスクリプションの **名前** に「**S1**」と入力し、**[作成]** を選択します。 

    ![[サブスクリプションの作成] ページ](./media/service-bus-create-topics-subscriptions-portal/create-subscription-page.png)
4. 前の手順を 2 回繰り返して、**S2** と **S3** というサブスクリプションを作成します。