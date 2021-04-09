---
title: Azure Notification Hubs で Apple Push Notification Service を構成する | Microsoft Docs
description: Apple Push Notification Service (APNS) の設定で Azure 通知ハブを構成する方法について説明します。
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 06/22/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: 63c7e0c9569428b55420911f253deee52ce440cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "85255400"
---
# <a name="configure-apple-push-notification-service-settings-for-a-notification-hub-in-the-azure-portal"></a>Azure portal で通知ハブ用に Apple Push Notification Service の設定を構成する

この記事では、Azure portal を使用して Azure 通知ハブ用に Apple Push Notification Service (APNS) の設定を構成する方法について説明します。

## <a name="prerequisites"></a>前提条件

通知ハブを作成していない場合は、ここで作成します。 詳細については、「[Azure portal 内で Azure 通知ハブを作成する](create-notification-hub-portal.md)」を参照してください。

## <a name="configure-apple-push-notification-service"></a>Apple Push Notification Service の構成

次の手順は、通知ハブ用に Apple Push Notification Service (APNS) の設定を構成する手順を示しています。

1. Azure portal の **[通知ハブ]** ページで、左側のメニューの **[Apple (APNS)]** を選択します。

1. **[認証モード]** で **[証明書]** または **[トークン]** を選択します。

   - **[証明書]** を選択した場合:
      - ファイル アイコンを選択し、アップロードする *.p12* ファイルを選択します。
      - パスワードを入力します。
      - **[サンドボックス]** モードを選択します。 または、ストアからアプリを購入したユーザーにプッシュ通知を送信する場合は、 **[Production]\(運用\)** モードを選択します。

     ![Azure portal における APNS 証明書構成のスクリーンショット](./media/configure-apple-push-notification-service/notification-hubs-apple-config-cert.png)

   - **[トークン]** を選択した場合:
      - **[キー ID]** 、 **[バンドル ID]** 、 **[チーム ID]** 、 **[トークン]** に値を入力します。
      - **[サンドボックス]** モードを選択します。 または、ストアからアプリを購入したユーザーにプッシュ通知を送信する場合は、 **[Production]\(運用\)** モードを選択します。

     ![Azure portal における APNS トークン構成のスクリーンショット](./media/configure-apple-push-notification-service/notification-hubs-apple-config-token.png)

## <a name="next-steps"></a>次のステップ

iOS デバイスに通知を送信する詳細な手順のチュートリアルについては、次の記事を参照してください: 「[Azure Notification Hubs を使用して iOS アプリにプッシュ通知を送信する](ios-sdk-get-started.md)」。
