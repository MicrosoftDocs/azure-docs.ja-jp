---
title: Azure Notification Hubs で Windows プッシュ通知サービスを構成する | Microsoft Docs
description: Azure 通知ハブ用に Windows プッシュ通知サービスの設定を構成する方法について説明します。
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
ms.openlocfilehash: 62d73a23fff3bd15d873a579d451b2f91509b7fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87758732"
---
# <a name="configure-windows-push-notification-service-settings-in-the-azure-portal"></a>Azure portal で Windows プッシュ通知サービスの設定を構成する

この記事では、Azure portal を使用して Azure 通知ハブ用に Windows 通知サービス (WNS) の設定を構成する方法について説明します。  

## <a name="prerequisites"></a>前提条件

通知ハブを作成していない場合は、ここで作成します。 詳細については、「[Azure portal 内で Azure 通知ハブを作成する](create-notification-hub-portal.md)」を参照してください。

## <a name="configure-windows-push-notification-service-wns"></a>Windows プッシュ通知サービス (WNS) の構成

次の手順は、通知ハブ用に Windows プッシュ通知サービス (WNS) の設定を構成する方法を説明しています。

1. Azure portal の **[通知ハブ]** ページで、左側のメニューの **[Windows (WNS)]** を選択します。
2. **[パッケージ SID]** と **[セキュリティ キー]** に値を入力します。
3. **[保存]** を選択します。

   ![[パッケージ SID] ボックスと [セキュリティ キー] ボックスを示すスクリーンショット](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

## <a name="next-steps"></a>次のステップ

Azure Notification Hubs と Windows プッシュ通知サービス (WNS) を使用して、ユニバーサル Windows プラットフォーム アプリケーションにプッシュ通知を送信する詳細な手順のチュートリアルについては、[Azure Notification Hubs を使用した UWP アプリへの通知の送信](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)に関するページを参照してください。
