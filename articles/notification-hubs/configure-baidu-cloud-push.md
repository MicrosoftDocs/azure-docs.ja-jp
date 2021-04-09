---
title: Azure Notification Hubs で Baidu クラウド プッシュを構成する | Microsoft Docs
description: Azure 通知ハブ用に Baidu の設定を構成する方法について説明します。
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: 759e35ba353f470ea3abc5f5d4182fa2b2ea0e73
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96003584"
---
# <a name="deprecated-configure-baidu-cloud-push-settings-for-a-notification-hub-in-the-azure-portal"></a>非推奨: Azure portal で通知ハブ用に Baidu クラウド プッシュの設定を構成する

この記事では、Azure portal を使用して Azure 通知ハブ用に Baidu クラウド プッシュの設定を構成する方法について説明します。

> [!IMPORTANT]
> このチュートリアルは非推奨です。 

## <a name="prerequisites"></a>前提条件
通知ハブを作成していない場合は、ここで作成します。 詳細については、「[Azure portal 内で Azure 通知ハブを作成する](create-notification-hub-portal.md)」を参照してください。 

## <a name="configure-baidu-cloud-push"></a>Baidu Cloud Push の構成
次の手順は、通知ハブ用に Baidu クラウド プッシュの設定を構成する手順を示しています。

1. Azure portal の **[通知ハブ]** ページで、左側のメニューの **[Baidu (Android China)]** を選択します。 
2. Baidu コンソールから取得した、Baidu クラウド プッシュ プロジェクトの **API キー** を入力します。 
3. Baidu コンソールから取得した、Baidu クラウド プッシュ プロジェクトの **秘密鍵** を入力します。 
4. **[保存]** を選択します。 

    ![Baidu (Android China) のプッシュ通知構成を示した Notification Hubs のスクリーンショット](./media/notification-hubs-baidu-get-started/AzureNotificationServicesBaidu.png)

## <a name="next-steps"></a>次のステップ
Azure Notification Hubs と Baidu クラウド プッシュを使用して、Baidu にプッシュ通知を送信する詳細な手順については、「[Baidu での Notification Hubs の使用](notification-hubs-baidu-china-android-notifications-get-started.md)」を参照してください。
