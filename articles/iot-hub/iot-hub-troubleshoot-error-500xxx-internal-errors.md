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
ms.openlocfilehash: 7f3f5177e084693c45bed1088a4e1d091be100ed
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84688910"
---
# <a name="500xxx-internal-errors"></a>500xxx 内部エラー

この記事では、**500xxx 内部エラー**の原因と解決策について説明します。

## <a name="symptoms"></a>現象

IoT Hub に対する要求が、500 から始まるエラー、または何らかの種類の "サーバー エラー" で失敗します。 次のような可能性があります。

* **500001 ServerError**:IoT Hub はサーバー側の問題に遭遇しました。

* **500008 GenericTimeout**:IoT Hub は、タイムアウトする前に接続要求を完了できませんでした。

* **ServiceUnavailable (エラー コードなし)** :IoT Hub 内部エラーが発生しました。

* **InternalServerError (エラー コードなし)** :IoT Hub 内部エラーが発生しました。

## <a name="cause"></a>原因

500xxx エラー応答にはさまざまな原因が考えられます。 どのような場合でも、一時的な問題である可能性があります。 IoT ハブ チームは [SLA](https://azure.microsoft.com/support/legal/sla/iot-hub/) を維持するために努力していますが、IoT Hub ノードのごく一部で一時的な障害が発生することがあります。 問題のあるノードにデバイスが接続しようとすると、このエラーが発生します。

## <a name="solution"></a>解決策

500xxx エラーを軽減するには、デバイスから再試行を発行します。 [自動的に再試行を管理](./iot-hub-reliability-features-in-sdks.md#connection-and-retry)するには、最新バージョンの [Azure IoT SDK](./iot-hub-devguide-sdks.md) を使用してください。 一時的な障害処理と再試行のベスト プラクティスについては、「[Transient Fault Handling (一時的な障害処理)](https://docs.microsoft.com/azure/architecture/best-practices/transient-faults)」を参照してください。  問題が解決しない場合は、[[リソース正常性]](./iot-hub-monitor-resource-health.md#use-azure-resource-health) と [[Azure の状態]](https://status.azure.com/) で IoT Hub に既知の問題があるかどうかを確認します。 [手動フェールオーバー機能](./tutorial-manual-failover.md)を使用することもできます。 既知の問題がなく、問題が引き続き発生する場合は、[サポートに問い合わせて](https://azure.microsoft.com/support/options/)さらに調査してください。
