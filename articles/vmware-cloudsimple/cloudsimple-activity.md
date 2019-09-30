---
title: Azure VMware Solution by CloudSimple - CloudSimple アクティビティ管理
description: VMware Solution by CloudSimple でのアクティビティの概念について説明します
author: sharaths-cs
ms.author: dikamath
ms.date: 04/30/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ad7905244ca190badf73c9a812a8f72282cbcdf3
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2019
ms.locfileid: "69563177"
---
# <a name="activity-management-overview"></a>アクティビティ管理の概要

CloudSimple では、プライベート クラウド環境の機能に影響を与える可能性がある、あらゆるアクティビティが追跡記録されます。 アクティビティには、アラート、イベント、タスク、監査アクティビティなどがあります。 [[アクティビティ]](monitor-activity.md) ページには、現在のすべてのアクティビティがまとめられており、詳細情報をドリルダウンできます。

## <a name="events"></a>events

イベントは、CloudSimple ポータルでユーザーおよびシステム アクティビティを追跡します。 イベントは特定のリソースに関連するアクティビティと影響の重大度を示します。  CloudSimple ポータルからイベントを表示できます。

## <a name="alerts"></a>アラート

アラートは、CloudSimple 環境での重要なアクティビティの通知です。 請求やユーザー アクセスに影響するイベントはアラートとして表示されます。  アラートは CloudSimple ポータルから受信確認できます。

## <a name="tasks"></a>タスク

タスクでは、完了するまでに 30 秒以上かかる、あらゆるユーザー操作が追跡されます。 タスクの進行状況は CloudSimple ポータルから監視できます。  タスクが完了すると、完了にかかった合計時間が情報に含まれます。

## <a name="audit"></a>Audit

監査ログでは、ユーザーの操作が追跡されます。 監査ログには、ユーザーが操作に指定したパラメーターが含まれます。  監査ログを使用して、すべてのユーザーのユーザー アクティビティを監視できます。

## <a name="next-steps"></a>次の手順

* [アカウントの概要を表示する](account.md)