---
title: Azure Notification Hubs で Baidu クラウド プッシュを構成する | Microsoft Docs
description: Azure 通知ハブ用に Baidu の設定を構成する方法について説明します。
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: jowargo
ms.openlocfilehash: 9942b1d4d8b5d538f5150a36e596753282039be7
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58488103"
---
# <a name="configure-baidu-cloud-push-settings-for-a-notification-hub-in-the-azure-portal"></a>Azure portal で通知ハブ用に Baidu クラウド プッシュの設定を構成する
この記事では、Azure portal を使用して Azure 通知ハブ用に Baidu クラウド プッシュの設定を構成する方法について説明します。 

## <a name="prerequisites"></a>前提条件
通知ハブを作成していない場合は、ここで作成します。 詳細については、「[Azure portal 内で Azure 通知ハブを作成する](create-notification-hub-portal.md)」を参照してください。 

## <a name="configure-baidu-cloud-push"></a>Baidu Cloud Push の構成
次の手順は、通知ハブ用に Baidu クラウド プッシュの設定を構成する手順を示しています。

1. Azure portal の **[通知ハブ]** ページで、左側のメニューの **[Baidu (Android China)]** を選択します。 
2. Baidu コンソールから取得した、Baidu クラウド プッシュ プロジェクトの **API キー**を入力します。 
3. Baidu コンソールから取得した、Baidu クラウド プッシュ プロジェクトの**秘密鍵**を入力します。 
4. **[保存]** を選択します。 

    ![Baidu (Android China) のプッシュ通知構成を示した Notification Hubs のスクリーンショット](./media/notification-hubs-baidu-get-started/AzureNotificationServicesBaidu.png)

## <a name="next-steps"></a>次の手順
Azure Notification Hubs と Baidu クラウド プッシュを使用して、Baidu にプッシュ通知を送信する詳細な手順については、「[Baidu での Notification Hubs の使用](notification-hubs-baidu-china-android-notifications-get-started.md)」を参照してください。
