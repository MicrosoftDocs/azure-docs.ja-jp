---
title: Azure Notification Hubs で Google Firebase Cloud Messaging を構成する | Microsoft Docs
description: Google Firebase Cloud Messaging の設定で Azure 通知ハブを構成する方法について説明します。
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 08/04/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: 0da103c11e2412108535ca322917632f5d95559d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96003567"
---
# <a name="configure-google-firebase-settings-for-a-notification-hub-in-the-azure-portal"></a>Azure portal で通知ハブ用に Google Firebase の設定を構成する

この記事では、Azure portal を使用して Azure 通知ハブ用に Google Firebase Cloud Messaging (FCM) の設定を構成する方法について説明します。  

## <a name="prerequisites"></a>前提条件

通知ハブを作成していない場合は、ここで作成します。 詳細については、「[Azure portal 内で Azure 通知ハブを作成する](create-notification-hub-portal.md)」を参照してください。

## <a name="configure-google-firebase-cloud-messaging-fcm"></a>Google Firebase Cloud Messaging (FCM) の構成

次の手順は、通知ハブ用に Google Firebase Cloud Messaging (FCM) の設定を構成する手順を説明しています。

1. Azure portal の **[通知ハブ]** ページで、左側のメニューの **[Google (GCM/FCM)]** を選択します。
2. 前に保存した FCM プロジェクトの **API キー** を貼り付けます。
3. **[保存]** を選択します。

   ![Google FCM 用に Notification Hubs を構成する方法を示したスクリーンショット](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)

## <a name="next-steps"></a>次のステップ

Azure Notification Hubs と Google Firebase Cloud Messaging を使用して、Android デバイスに通知を送信する詳細な手順のチュートリアルについては、[Notification Hubs と Google FCM を使用した Android デバイスへのプッシュ通知の送信](notification-hubs-android-push-notification-google-fcm-get-started.md)に関するページを参照してください。
