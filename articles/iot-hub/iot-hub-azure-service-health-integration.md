---
title: Azure IoT Hub サービスとリソース正常性を確認する | Microsoft Docs
description: Azure Service Health と Azure Resource Health を使用して IoT Hub を監視する
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: robinsh
ms.custom:
- amqp
- 'Role: Cloud Development'
- 'Role: Technical Support'
- devx-track-csharp
ms.openlocfilehash: 27fca7b76ab148fc355eb7d52ee0cbcbd3540458
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92548414"
---
# <a name="check-iot-hub-service-and-resource-health"></a>IoT Hub サービスとリソース正常性を確認する

Azure IoT Hub が [Azure Service Health サービス](../service-health/overview.md) と統合されることで、IoT Hub サービスと個々の IoT ハブのサービスレベル正常性を監視する機能が提供されます。 また、IoT Hub サービスまたは IoT ハブの状態が変化したときに通知を受け取るようにアラートを設定することもできます。 Azure Service Health サービスは、3 つの小さなサービス (Azure Resource Health、Azure Service Health、および Azure の状態ページ) を組み合わせたものです。 この記事の各セクションでは、各サービス、および IoT Hub との関係について詳しく説明します。

Azure Service Health サービスは、IoT Hub サービスと個々の IoT ハブの可用性に影響する可能性があるサービスレベル イベント (停止やアップグレードなど) を監視するのに役立ちます。 また、IoT Hub が Azure Monitor と統合することで、特定の IoT ハブで発生する操作上のエラーや状態を監視するために使用できる IoT Hub プラットフォーム メトリックと IoT Hub リソース ログが提供されます。 詳細については、[IoT Hub の監視](monitor-iot-hub.md)に関する記事を参照してください。

## <a name="check-health-of-an-iot-hub-with-azure-resource-health"></a>Azure Resource Health を使用して IoT ハブの正常性を確認する

Azure Resource Health は Azure Service Health サービスの一部であり、個々のリソースの正常性を追跡します。 IoT ハブの正常性状態は、ポータルから直接確認できます。

ポータルを使用して IoT ハブの状態と状態履歴を表示するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com) で、IoT ハブに移動します。

1. 左側のペインの **[サポート + トラブルシューティング]** で、 **[Resource Health]\(リソース正常性\)** を選択します。

    :::image type="content" source="./media/iot-hub-azure-service-health-integration/iot-hub-resource-health.png" alt-text="IoT ハブの [Resource Health]\(リソース正常性\) ページ":::

Azure Resource Health と、正常性データの解釈方法については、Azure Service Health ドキュメントの「[Resource Health の概要](../service-health/resource-health-overview.md)」を参照してください。

また、 **[リソース正常性アラートの追加]** を選択して、IoT ハブの正常性状態が変化したらトリガーされるようにアラートを設定することもできます。 詳細については、Azure Service Health ドキュメントの[Service Health イベントのアラートの構成](../service-health/alerts-activity-log-service-notifications-portal.md)に関する記事と関連トピックを参照してください。

## <a name="check-health-of-iot-hubs-in-your-subscription-with-azure-service-health"></a>Azure Service Health を使用してサブスクリプション内の IoT ハブの正常性を確認する

Azure Service Health を使用して、サブスクリプション内のすべての IoT ハブの正常性状態を確認できます。

IoT Hub の正常性を確認するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com) にサインインします。

2. **[Service Health]\(サービス正常性\)**  >  **[リソース正常性]** に移動します。

3. ドロップダウン ボックスから、サブスクリプションを選択し、リソースの種類として **[IoT ハブ]** を選択します。

Azure Service Health と、正常性データの解釈方法については、Azure Service Health ドキュメントの「[Service Health の概要](../service-health/service-health-overview.md)」を参照してください。

Azure Service Health を使用してアラートを設定する方法については、Azure Service Health ドキュメントの[Service Health イベントのアラートの構成](../service-health/alerts-activity-log-service-notifications-portal.md)に関する記事と関連トピックを参照してください。

## <a name="check-health-of-the-iot-hub-service-by-region-on-azure-status-page"></a>Azure の状態ページでリージョン別の IoT Hub サービスの正常性を確認する

IoT Hub とその他のサービスの状態を世界中のリージョンごとに確認するには、[Azure の状態ページ](https://status.azure.com/status)を使用できます。 Azure の状態ページの詳細については、Azure Service Health ドキュメントの「[Azure の状態の概要](../service-health/azure-status-overview.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

* Azure Service Health、Azure Resource Health、および Azure の状態ページの詳細については、[Azure Service Health サービス](../service-health/overview.md)に関するページを参照してください。
* Azure IoT Hub の監視については、[Azure IoT Hub の監視](monitor-iot-hub.md)に関するページを参照してください。
