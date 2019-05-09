---
title: Azure portal を使用して Azure 通知ハブを作成する | Microsoft Docs
description: このチュートリアルでは、Azure portal を使用して Azure 通知ハブを作成する方法を学習します。
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: quickstart
ms.date: 02/14/2019
ms.author: jowargo
ms.openlocfilehash: 62e72f27e48f7bf220901f4eb36090f926724a2a
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/15/2019
ms.locfileid: "56313986"
---
# <a name="create-an-azure-notification-hub-in-the-azure-portal"></a>Azure portal 内で Azure 通知ハブを作成する 
Azure Notification Hubs は、任意のバックエンド (クラウドまたはオンプレミス) から任意のプラットフォーム (iOS、Android、Windows、Kindle、Baidu など) に通知を送信できる、使いやすく、かつスケールアウトされたプッシュ エンジンを提供します。 このサービスの詳細については、「[Azure Notification Hubs とは](notification-hubs-push-notification-overview.md)」を参照してください。

このクイック スタートでは、Azure portal 内で通知ハブを作成します。 最初のセクションでは、Notification Hubs 名前空間を作成し、その名前空間の中にハブを作成する手順を示します。 2 つ目のセクションでは、既存の Notification Hubs 名前空間の中に通知ハブを作成する手順を示します。 

## <a name="create-a-namespace-and-a-notification-hub"></a>名前空間と通知ハブを作成する
このセクションでは、名前空間を作成し、その名前空間の中にハブを作成します。 

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

## <a name="create-a-notification-hub-in-an-existing-namespace"></a>既存の名前空間の中に通知ハブを作成する
このセクションでは、既存の名前空間の中に通知ハブを作成します。 

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. 左側のメニューで **[すべてのサービス]** を選択し、「**通知ハブ**」を検索します。**[Notification Hub の名前空間]** の横にある**星印** (`*`) を選択して、左側のメニューの **[お気に入り]** セクションに追加します。 **[Notification Hub の名前空間]** を選択します。 

      ![Azure portal - [Notification Hub の名前空間] を選択する](./media/create-notification-hub-portal/select-notification-hub-namespaces-all-services.png)
3. **[Notification Hub の名前空間]** ページで、一覧から目的の名前空間を選択します。 

      ![一覧から目的の名前空間を選択する](./media/create-notification-hub-portal/select-namespace.png)
1. **[Notification Hub の名前空間]** ページで、ツール バーの **[ハブの追加]** を選択します。 

      ![[Notification Hub の名前空間] - [ハブの追加] ボタン](./media/create-notification-hub-portal/add-hub-button.png)
4. **[新しい Notification Hub]** ページで、通知ハブの名前を入力し、**[OK]** を選択します。

      ![[新しい Notification Hub] ページ -> ハブの名前を入力する](./media/create-notification-hub-portal/new-notification-hub-page.png)
4. 上部にある **[通知]** (ベルのアイコン) を選択して、新しいハブのデプロイの状態を確認します。 右隅にある **[X]** を選択して、通知ウィンドウを閉じます。 

      ![デプロイの通知](./media/create-notification-hub-portal/deployment-notification.png)
5. **[Notification Hub の名前空間]** Web ページを最新の情報に更新して、一覧にある新しいハブを確認します。 

      ![Azure Portal > [通知] > [リソースに移動]](./media/create-notification-hub-portal/new-hub-in-list.png)
6. 自分の**通知ハブ**を選択して、通知ハブのホーム ページを確認します。 

      ![Azure Portal > [通知] > [リソースに移動]](./media/create-notification-hub-portal/hub-home-page.png)

## <a name="next-steps"></a>次の手順
このクイック スタートでは、通知ハブを作成します。 プラットフォーム通知システム (PNS) 設定を使用してハブを構成する方法については、[PNS 設定を使用した通知ハブの構成](configure-notification-hub-portal-pns-settings.md)に関するページを参照してください。 