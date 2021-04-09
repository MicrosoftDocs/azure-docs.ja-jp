---
title: Azure IoT Hub のエラー 403002 IoTHubQuotaExceeded のトラブルシューティング
description: エラー 403002 IoTHubQuotaExceeded を修正する方法の概要
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 8312c3267e826088f34b3bffe1520703eec00bdc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "76960392"
---
# <a name="403002-iothubquotaexceeded"></a>403002 IoTHubQuotaExceeded

この記事では、**403002 IoTHubQuotaExceeded** エラーの原因と解決策について説明します。

## <a name="symptoms"></a>現象

IoT Hub に対するすべての要求がエラー **403002 IoTHubQuotaExceeded** で失敗します。 Azure portal に IoT ハブ デバイスの一覧が読み込まれません。

## <a name="cause"></a>原因

IoT ハブの 1 日あたりのメッセージ クォータを超えています。 

## <a name="solution"></a>解決策

[IoT ハブ上のユニット数をアップグレードするか増やします](iot-hub-upgrade.md)。または、1 日あたりのクォータが更新される翌日 (UTC) まで待ちます。

## <a name="next-steps"></a>次のステップ

* ツイン クエリやダイレクト メソッドなど、クォータに対する操作のカウント方法については、[IoT Hub の価格](iot-hub-devguide-pricing.md#charges-per-operation)に関する記事を参照してください。
* 1 日のクォータ使用量の監視を設定するには、メトリック "*Total number of messages used*" (使用されているメッセージの合計数) を使用してアラートを設定します。 詳細な手順については、[IoT Hub を使用したメトリックとアラートの設定](tutorial-use-metrics-and-diags.md#set-up-metrics)に関する記事を参照してください。