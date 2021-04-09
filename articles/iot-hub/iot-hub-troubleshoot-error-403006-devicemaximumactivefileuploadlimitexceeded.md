---
title: Azure IoT Hub のエラー 403006 DeviceMaximumActiveFileUploadLimitExceeded のトラブルシューティング
description: エラー 403006 DeviceMaximumActiveFileUploadLimitExceeded を修正する方法の概要
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 1e3c05e4cc3ccf34573b55d3729aded16e26d66e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "76960308"
---
# <a name="403006-devicemaximumactivefileuploadlimitexceeded"></a>403006 DeviceMaximumActiveFileUploadLimitExceeded

この記事では、**403006 DeviceMaximumActiveFileUploadLimitExceeded** エラーの原因と解決策について説明します。

## <a name="symptoms"></a>現象

ファイルのアップロード要求がエラー コード **403006**、メッセージ "Number of active file upload requests cannot exceed 10" (アクティブなファイルのアップロード要求数は 10 個を超えることはできません) で失敗します。

## <a name="cause"></a>原因

各デバイス クライアントは、[同時ファイル アップロード数が 10 個](./iot-hub-devguide-quotas-throttling.md#other-limits)に制限されています。 

ファイル アップロードの完了時にデバイスから IoT Hub に通知しないと、この制限をたやすく超える可能性があります。 この問題は、通常、信頼性の低いデバイス側ネットワークが原因で発生します。

## <a name="solution"></a>解決策

デバイスからすぐに [IoT Hub ファイルのアップロードの完了を通知](./iot-hub-devguide-file-upload.md#notify-iot-hub-of-a-completed-file-upload)できることを確認します。 次に、[ファイル アップロードの構成に関する SAS トークンの TTL を減らす](iot-hub-configure-file-upload.md)ようにします。

## <a name="next-steps"></a>次の手順

ファイルのアップロードの詳細については、「[IoT Hub を使用したファイルのアップロード](./iot-hub-devguide-file-upload.md)」と「[Azure Portal を使用して IoT Hub ファイルのアップロードを構成する](./iot-hub-configure-file-upload.md)」を参照してください。