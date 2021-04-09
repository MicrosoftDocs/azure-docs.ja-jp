---
title: Azure IoT Hub 500xxx 内部エラーのトラブルシューティング
description: 500xxx 内部エラーを修正する方法の概要
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 1333a135f3e123757e268513f73e8329537e630b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92538206"
---
# <a name="500xxx-internal-errors"></a>500xxx 内部エラー

この記事では、**500xxx 内部エラー** の原因と解決策について説明します。

## <a name="symptoms"></a>現象

IoT Hub に対する要求が、500 から始まるエラー、または何らかの種類の "サーバー エラー" で失敗します。 次のような可能性があります。

* **500001 ServerError**: IoT Hub はサーバー側の問題に遭遇しました。

* **500008 GenericTimeout**: IoT Hub は、タイムアウトする前に接続要求を完了できませんでした。

* **ServiceUnavailable (エラー コードなし)** : IoT Hub 内部エラーが発生しました。

* **InternalServerError (エラー コードなし)** : IoT Hub 内部エラーが発生しました。

## <a name="cause"></a>原因

500xxx エラー応答にはさまざまな原因が考えられます。 どのような場合でも、一時的な問題である可能性があります。 IoT ハブ チームは [SLA](https://azure.microsoft.com/support/legal/sla/iot-hub/) を維持するために努力していますが、IoT Hub ノードのごく一部で一時的な障害が発生することがあります。 問題のあるノードにデバイスが接続しようとすると、このエラーが発生します。

## <a name="solution"></a>解決策

500xxx エラーを軽減するには、デバイスから再試行を発行します。 [自動的に再試行を管理](./iot-hub-reliability-features-in-sdks.md#connection-and-retry)するには、最新バージョンの [Azure IoT SDK](./iot-hub-devguide-sdks.md) を使用してください。 一時的な障害処理と再試行のベスト プラクティスについては、「[Transient Fault Handling (一時的な障害処理)](/azure/architecture/best-practices/transient-faults)」を参照してください。  問題が解決しない場合は、[[リソース正常性]](./iot-hub-azure-service-health-integration.md#check-health-of-an-iot-hub-with-azure-resource-health) と [[Azure の状態]](https://status.azure.com/) で IoT Hub に既知の問題があるかどうかを確認します。 [手動フェールオーバー機能](./tutorial-manual-failover.md)を使用することもできます。 既知の問題がなく、問題が引き続き発生する場合は、[サポートに問い合わせて](https://azure.microsoft.com/support/options/)さらに調査してください。
