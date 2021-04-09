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
ms.date: 08/03/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/28/2019
ms.openlocfilehash: 1455259bc42aea9d506a9a2a19d725cac3d643f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87562771"
---
# <a name="change-pricing-tier-of-an-azure-notification-hubs-namespace"></a>Azure Notification Hubs 名前空間の価格レベルを変更する

Notification Hubs は、**Free**、**Basic**、**Standard** の 3 つのレベルで提供されます。 この記事では、Azure Notification Hubs 名前空間の価格レベルを変更する方法を示します。

## <a name="overview"></a>概要

Azure Notification Hubs では、"*通知ハブ*" が最小のリソースでありエンティティです。 これは一般に 1 つのアプリケーションにマップされ、アプリのために Microsoft がサポートしているプラットフォーム通知システム (PNS) ごとに 1 つの証明書を保持できます。 アプリケーションは、ハイブリッドまたはネイティブのクロスプラットフォーム アプリケーションが可能です。

*名前空間* は通知ハブのコレクションです。 それぞれの名前空間は、通常、関連していて特定の目的に使用されるハブで構成されます。 たとえば、それぞれが開発、テスト、および運用を目的とする 3 つの異なる名前空間を用意できます。

名前空間は **無料**、**Basic**、または **Standard** 価格レベルに関連付けることができます。 任意の名前空間で、要件に合うレベルを使用できます。 以降のセクションでは、Notification Hubs 名前空間の価格レベルを変更する方法を示します。

## <a name="use-azure-portal"></a>Azure Portal の使用

Azure portal 使用時には、名前空間のページまたはハブのページで、名前空間の価格レベルを変更できます。 ハブのページで変更すると、実際に名前空間レベルで変更することになります。 これにより、名前空間と、名前空間内のすべてのハブの価格レベルが変更されます。

### <a name="change-tier-on-the-namespace-page"></a>名前空間のページでレベルを変更する

次の手順は、名前空間のページで名前空間の価格レベルを変更する方法を示しています。 名前空間のレベルを変更すると、その名前空間内のすべてのハブに変更が適用されます。

1. [Azure portal](https://portal.azure.com) にサインインします。
2. 左側のメニューから、 **[すべてのサービス]** を選択します。
3. **[Internet of Things] (モノのインターネット)** セクションで、 **[Notification Hub Namespaces] (Notification Hub の名前空間)** を選択します。 テキストの横にあるアスタリスク (`*`) を選択すると、その項目が左側のナビゲーション バーの **[お気に入り]** の下に追加されます。 これは、名前空間のページに次にアクセスするときに役に立ちます。 **[お気に入り]** に追加したら、 **[Notification Hub の名前空間]** を選択します。

    ![[すべてのサービス] -> [Notification Hub Namespaces] (Notification Hub の名前空間)](./media/change-pricing-tier/all-services-nhub.png)

4. **[Notification Hub Namespaces] (Notification Hub の名前空間)** ページで、価格レベルを変更する名前空間を選択します。
5. 目的の名前空間の **[Notification Hub Namespaces] (Notification Hub の名前空間)** ページでは、 **[Essentials] (要点)** セクションに、名前空間の現在の価格レベルが表示されます。 次の図では、名前空間の価格レベルが **Free** であることを確認できます。

    ![名前空間のページでの現在の価格レベル](./media/change-pricing-tier/pricing-tier-before.png)

6. 名前空間の **[通知ハブの名前空間]** ページで、 **[管理]** セクションの **[価格レベル]** を選択します。

    ![名前空間のページで価格レベルを選択する](./media/change-pricing-tier/namespace-select-pricing-menu.png)

7. 価格レベルを変更し、 **[選択]** ボタンをクリックします。
8. **[アラート]** で、レベル変更アクションの状態を確認できます。
9. **[概要]** ページに切り替えます。 **[Essentials] (要点)** セクションの **[価格レベル]** フィールドに新しいレベルが表示されることを確認します。
10. この手順は省略可能です。 名前空間内でいずれかのハブを選択します。 **[Essentials] (要点)** セクションに同じ価格レベルが表示されることを確認します。 その名前空間内のすべてのハブで、同じ価格レベルが表示されるはずです。

### <a name="change-tier-on-the-hub-page"></a>ハブのページでレベルを変更する

ハブ ページで名前空間の価格レベルを変更するには、次の手順のようにします。 この手順はハブ ページから開始しますが、実際には名前空間と、その名前空間内のすべてのハブの価格レベルが変更されます。

1. [Azure portal](https://portal.azure.com) にサインインします。
2. 左側のメニューから、 **[すべてのサービス]** を選択します。
3. **[Internet of Things] (モノのインターネット)** セクションで **[Notification Hub]** を選択します。
4. 目的の通知 **ハブ** を選択します。
5. 左側のメニューで **[価格レベル]** を選択します。
6. 価格レベルを変更し、 **[選択]** ボタンをクリックします。 この操作で、ハブを含む名前空間の価格レベルの設定が変更されます。 そのため、名前空間のページと、すべてのハブのページに、新しい価格レベルが表示されます。

> [!NOTE]
> すべての価格レベルの変更は、すぐに有効になります。

## <a name="use-rest-api"></a>REST API を使用する

以下のリソース プロバイダー REST API を使用して、現在の価格レベルを取得し、それを更新することができます。

### <a name="get-current-pricing-tier-for-a-namespace"></a>名前空間の現在の価格レベルを取得する

現在の名前空間のレベルを取得するには、次の例に示すように GET コマンドを送信します。

```REST
GET: https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/notificationhubplan
```

### <a name="update-pricing-tier-for-a-namespace"></a>名前空間の価格レベルを更新する

名前空間のレベルを更新するには、次の例に示すように PUT コマンドを送信します。

```REST
PUT: https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/notificationhubplan
Body: <NotificationHubPlan xmlns:i="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"><SKU>Standard</SKU></NotificationHubPlan>
```

## <a name="next-steps"></a>次のステップ

これらのレベルと料金の詳細については、[Notification Hubs の料金](https://azure.microsoft.com/pricing/details/notification-hubs/)に関するページを参照してください。
