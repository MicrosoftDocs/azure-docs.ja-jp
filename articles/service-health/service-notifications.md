---
title: Azure Portal を使用したサービス正常性通知の表示
description: Azure portal にサービス正常性通知を表示します。 サービス正常性通知は、Azure インフラストラクチャから Azure アクティビティ ログに発行されます。
ms.topic: conceptual
ms.date: 6/27/2019
ms.openlocfilehash: 9f9f3e7b10d9aa0014e4e00e7bfa72c9dc66e142
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100588002"
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>Azure Portal を使用したサービス正常性通知の表示

サービス正常性通知は、Azure インフラストラクチャから [Azure アクティビティ ログ](../azure-monitor/essentials/platform-logs-overview.md)に発行されます。  通知には、お客様のサブスクリプションに属するリソースに関する情報が含まれています。 アクティビティ ログに大量の情報が格納されている可能性を考慮し、サービス正常性通知に関するアラートを表示および設定しやすくするための個別のユーザー インターフェイスがあります。 

サービス正常性通知には、クラスに応じて、情報のみの通知とアクションが必要になる通知があります。

さまざまなクラスのサービス正常性通知の詳細については、[サービス正常性通知のプロパティ](service-health-notifications-properties.md)に関するページを参照してください。

## <a name="view-your-service-health-notifications-in-the-azure-portal"></a>Azure Portal でのサービス正常性通知の表示

1. [Azure Portal](https://portal.azure.com) で、 **[モニター]** を選択します。

    ![Azure Portal メニューのスクリーンショット。[モニター] が選択されている](./media/service-notifications/home-monitor.png)

    Azure Monitor は、すべての監視設定とデータが 1 つにまとめられた統合ビューです。 最初に開くのは **[アクティビティ ログ]** セクションです。

1. **[Alerts]** (アラート) を選択します。

    ![モニターのアクティビティ ログのスクリーンショット。[アラート] が選択されている](./media/service-notifications/service-health-summary.png)

1. **[+ アクティビティ ログ アラートの追加]** を選択し、今後サービスの通知を受け取るようにアラートを設定します。 詳細については、「[サービス通知のアクティビティ ログ アラートを作成する](./alerts-activity-log-service-notifications-portal.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

* [アクティビティ ログのアラート](../azure-monitor/alerts/activity-log-alerts.md)について詳しく学習します。
