---
title: Notification Hubs 名前空間の価格レベルを変更する | Microsoft Docs
description: Azure Notification Hubs 名前空間の価格レベルを変更する方法について説明します。
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 01/28/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/28/2019
ms.openlocfilehash: 679e8b58e5876d5460b426e694df85ded992a36d
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212567"
---
# <a name="change-pricing-tier-of-an-azure-notification-hubs-namespace"></a>Azure Notification Hubs 名前空間の価格レベルを変更する
Notification Hubs は、**Free**、**Basic**、**Standard** の 3 つのレベルで提供されます。 この記事では、Azure Notification Hubs 名前空間の価格レベルを変更する方法を示します。 

## <a name="overview"></a>概要
Azure Notification Hubs では、**ハブ**が最小のリソース/エンティティです。 これは一般に 1 つのアプリケーションにマップされ、アプリのために Microsoft がサポートしているプラットフォーム通知システムごとに 1 つの証明書を保持できます。 アプリケーションは、ハイブリッドまたはネイティブの、クロス プラットフォーム アプリケーションである場合があります。

**名前空間**は通知ハブのコレクションです。 それぞれの名前空間は、通常、関連していて特定の目的に使用されるハブで構成されます。 たとえば、開発、テスト、および運用の目的で、それぞれに 3 つの異なる名前空間を用意できます。 

価格レベルは、名前空間レベルで関連付けることができます。 Notification Hubs は、**Free**、**Basic**、**Standard** の 3 つのレベルをサポートしています。 お客様の要件に合う名前空間のレベルを使用できます。 以降のセクションでは、Notification Hubs 名前空間の価格レベルを変更する方法を示します。 

## <a name="use-azure-portal"></a>Azure Portal の使用 
Azure portal 使用時には、名前空間のページまたはハブのページで、名前空間の価格レベルを変更できます。  ハブのページで変更すると、実際に名前空間レベルで変更することになります。 これにより、名前空間と、名前空間内のすべてのハブの価格レベルが変更されます。 

### <a name="change-tier-on-the-namespace-page"></a>名前空間のページでレベルを変更する
次の手順は、名前空間のページで名前空間の価格レベルを変更する手順を示しています。 名前空間のレベルを変更すると、名前空間内のすべてのハブに変更が適用されます。

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. 左側のメニューから、 **[すべてのサービス]** を選択します。 
3. **[Internet of Things] (モノのインターネット)** セクションで、 **[Notification Hub Namespaces] (Notification Hub の名前空間)** を選択します。 テキストの横にある星 (`*`) を選択すると、その項目が左側のナビゲーション バーの **[お気に入り]** の下に追加されます。 これは、次回以降、名前空間のページによりすばやくアクセスする助けになります。 これを [お気に入り] に追加したら、 **[Notification Hub Namespaces] (Notification Hub の名前空間)** を選択します。 

    ![[すべてのサービス] -> [Notification Hub Namespaces] (Notification Hub の名前空間)](./media/change-pricing-tier/all-services-nhub.png)
1. **[Notification Hub Namespaces] (Notification Hub の名前空間)** ページで、価格レベルを変更する名前空間を選択します。 
2. 目的の名前空間の **[Notification Hub Namespaces] (Notification Hub の名前空間)** ページでは、 **[Essentials] (要点)** セクションに、名前空間の現在の価格レベルが表示されます。 次の図では、名前空間の価格レベルが **Free** であることを確認できます。 

    ![名前空間のページでの現在の価格レベル](./media/change-pricing-tier/pricing-tier-before.png)
1. 目的の名前空間の **[Notification Hub Namespaces] (Notification Hub の名前空間)** で、 **[管理]** セクションの **[価格レベル]** を選択します。 

    ![名前空間のページで価格レベルを選択する](./media/change-pricing-tier/namespace-select-pricing-menu.png)
6. 価格レベルを変更し、 **[選択]** ボタンをクリックします。    
7. **[アラート]** で、レベル変更アクションの状態を確認します。 
8. **[概要]** ページに切り替えます。 **[Essentials] (要点)** セクションの **[価格レベル]** フィールドに新しいレベルが表示されることを確認します。     
1. この手順は省略可能です。 名前空間内でいずれかのハブを選択します。 **[Essentials] (要点)** セクションに同じ価格レベルが表示されることを確認します。 その名前空間内のすべてのハブで、同じ価格レベルが表示されるはずです。 

### <a name="change-tier-on-the-hub-page"></a>ハブのページでレベルを変更する
次の手順は、ハブのページで名前空間の価格レベルを変更する手順を示しています。 これらの手順はハブのページから開始しているにもかかわらず、実際には名前空間と、その名前空間内のすべてのハブの価格レベルを変更します。 

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. 左側のメニューから、 **[すべてのサービス]** を選択します。
3. **[Internet of Things] (モノのインターネット)** セクションで **[Notification Hub]** を選択します。 
4. 目的の通知**ハブ**を選択します。 
5. 左側のメニューで **[価格レベル]** を選択します。 
6. 価格レベルを変更し、 **[選択]** ボタンをクリックします。 この操作で、ハブを含む名前空間の価格レベルの設定が変更されます。 そのため、名前空間のページと、すべてのハブのページに、新しい価格レベルが表示されます。 

## <a name="use-rest-api"></a>REST API を使用する
以下のリソース プロバイダー REST API を使用して、現在の価格レベルを取得し、それを更新することができます。 

### <a name="get-current-pricing-tier-for-a-namespace"></a>名前空間の現在の価格レベルを取得する
**現在の名前空間のレベル**を取得するには、次の例に示すように GET コマンドを送信します。 

```REST
GET: https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/notificationhubplan
```

### <a name="update-pricing-tier-for-a-namespace"></a>名前空間の価格レベルを更新する
**名前空間のレベルを更新する**には、次の例に示すように PUT コマンドを送信します。 

```REST
PUT: https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/notificationhubplan
Body: <NotificationHubPlan xmlns:i="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"><SKU>Standard</SKU></NotificationHubPlan>
```



## <a name="next-steps"></a>次の手順
これらのレベルと料金の詳細については、[Notification Hubs の料金](https://azure.microsoft.com/pricing/details/notification-hubs/)に関するページを参照してください。
