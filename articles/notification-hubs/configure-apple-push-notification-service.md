---
title: Azure Notification Hubs で Apple Push Notification Service を構成する | Microsoft Docs
description: Apple Push Notification Service (APNS) の設定で Azure 通知ハブを構成する方法について説明します。
services: notification-hubs
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: 74e7e3c74934e292b668b8bff594a5efbca19716
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212547"
---
# <a name="configure-apple-push-notification-service-apns-settings-for-a-notification-hub-in-the-azure-portal"></a>Azure portal で通知ハブ用に Apple Push Notification Service (APNS) の設定を構成する
この記事では、Azure portal を使用して Azure 通知ハブ用に Apple Push Notification Service (APNS) の設定を構成する方法について説明します。 

## <a name="prerequisites"></a>前提条件
通知ハブを作成していない場合は、ここで作成します。 詳細については、「[Azure portal 内で Azure 通知ハブを作成する](create-notification-hub-portal.md)」を参照してください。 

## <a name="configure-apple-push-notification-service"></a>Apple Push Notification Service の構成

次の手順は、通知ハブ用に Apple Push Notification Service (APNS) の設定を構成する手順を示しています。

1. Azure portal の **[通知ハブ]** ページで、左側のメニューの **[Apple (APNS)]** を選択します。

1. **[認証モード]** で **[証明書]** または **[トークン]** を選択します。

   a. **[証明書]** を選択した場合:
   * ファイル アイコンを選択し、アップロードする *.p12* ファイルを選択します。
   * パスワードを入力します。
   * **[サンドボックス]** モードを選択します。 または、ストアからアプリを購入したユーザーにプッシュ通知を送信する場合は、 **[Production]\(運用\)** モードを選択します。

     ![Azure portal における APNS 証明書構成のスクリーンショット](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png)

   b. **[トークン]** を選択した場合:

   * **[キー ID]** 、 **[バンドル ID]** 、 **[チーム ID]** 、 **[トークン]** に値を入力します。
   * **[サンドボックス]** モードを選択します。 または、ストアからアプリを購入したユーザーにプッシュ通知を送信する場合は、 **[Production]\(運用\)** モードを選択します。

     ![Azure portal における APNS トークン構成のスクリーンショット](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-token.png)

## <a name="next-steps"></a>次の手順
iOS デバイスにプッシュ通知を送信する詳細な手順については、[Azure Notification Hubs と APNS を使用した iOS デバイスへのプッシュ通知の送信](notification-hubs-ios-apple-push-notification-apns-get-started.md)に関するページをご覧ください
