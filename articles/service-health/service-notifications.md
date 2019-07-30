---
title: Azure Portal を使用したサービス正常性通知の表示
description: サービス正常性通知を使用すると、Microsoft Azure によって発行されるサービスの正常性に関するメッセージを表示できます。
author: stephbaron
ms.author: stbaron
services: monitoring
ms.service: service-health
ms.topic: conceptual
ms.date: 6/27/2019
ms.subservice: ''
ms.openlocfilehash: d2e18ae28e79590cb04ee0045341ea817be4a3bc
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2019
ms.locfileid: "67538143"
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>Azure Portal を使用したサービス正常性通知の表示

サービス正常性通知は、Azure インフラストラクチャから [Azure アクティビティ ログ](../azure-monitor/platform/activity-logs-overview.md)に発行されます。  通知には、お客様のサブスクリプションに属するリソースに関する情報が含まれています。 アクティビティ ログに大量の情報が格納されている可能性を考慮し、サービス正常性通知に関するアラートを表示および設定しやすくするための個別のユーザー インターフェイスがあります。 

サービス正常性通知には、クラスに応じて、情報のみの通知とアクションが必要になる通知があります。

さまざまなクラスのサービス正常性通知の詳細については、[サービス正常性通知のプロパティ](service-health-notifications-properties.md)に関するページを参照してください。

## <a name="view-your-service-health-notifications-in-the-azure-portal"></a>Azure Portal でのサービス正常性通知の表示

1. [Azure Portal](https://portal.azure.com) で、 **[モニター]** を選択します。

    ![Azure Portal メニューのスクリーンショット。[モニター] が選択されている](./media/service-notifications/home-monitor.png)

    Azure Monitor は、すべての監視設定とデータが 1 つにまとめられた統合ビューです。 最初に開くのは **[アクティビティ ログ]** セクションです。

1. **[Alerts]** (アラート) を選択します。

    ![モニターのアクティビティ ログのスクリーンショット。[アラート] が選択されている](./media/service-notifications/service-health-summary.png)

1. **[+ アクティビティ ログ アラートの追加]** を選択し、今後サービスの通知を受け取るようにアラートを設定します。 詳細については、「[サービス通知のアクティビティ ログ アラートを作成する](../azure-monitor/platform/alerts-activity-log-service-notifications.md)」を参照してください。

## <a name="next-steps"></a>次の手順

* [アクティビティ ログのアラート](../azure-monitor/platform/activity-log-alerts.md)について詳しく学習します。
