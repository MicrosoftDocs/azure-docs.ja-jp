---
title: デバイスと Azure デバイス プロビジョニング サービスの間でペイロードを転送する方法
description: このドキュメントでは、デバイスとデバイス プロビジョニング サービス (DPS) の間でペイロードを転送する方法について説明します
author: menchi
ms.author: menchi
ms.date: 02/11/2020
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: a3ee7f3fca3fff1cd401f26489b01fb9cc4e09c5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99259521"
---
# <a name="how-to-transfer-payloads-between-devices-and-dps"></a>デバイスと DPS の間でペイロードを転送する方法
DPS では、デバイスを正しい IoT Hub に適切にプロビジョニングするために、デバイスの追加データが必要になる場合があり、データはそのデバイスから提供される必要があります。 逆に、DPS では、クライアント側ロジックの処理を進めるためにデバイスにデータを返すことができます。 

## <a name="when-to-use-it"></a>いつ使用するか
この機能は[カスタム割り当て](./how-to-use-custom-allocation-policies.md)の拡張機能として使用できます。 たとえば、人間が介入することなく、デバイス モデルに基づいてデバイス名を割り当てる必要があります。 この場合は[カスタム割り当て](./how-to-use-custom-allocation-policies.md)を使用します。 [登録デバイス呼び出し](/rest/api/iot-dps/runtimeregistration/registerdevice)の一部としてモデル情報を報告するようにデバイスを構成します。 デバイスのペイロードが DPS からカスタム割り当て Webhook に渡されます。 また、関数では、モデル情報を受け取ると、このデバイスをプロビジョニングする IoT Hub を判別できます。 同様に、webhook からデバイスに何らかのデータを返す場合は、webhook 応答内の文字列としてそのデータを渡します。  

## <a name="device-sends-data-payload-to-dps"></a>デバイスから DPS にデータ ペイロードを送信する
デバイスが[デバイスの登録呼び出し](/rest/api/iot-dps/runtimeregistration/registerdevice)を DPS に送信しているときに、本文内の他のフィールドを取得するように登録呼び出しを拡張できます。 本文は次のようになります。 
   ```
   { 
       “registrationId”: “mydevice”, 
       “tpm”:                
       { 
           “endorsementKey”: “stuff”, 
           “storageRootKey”: “things” 
       }, 
       “payload”: “your additional data goes here. It can be nested JSON.” 
    } 
   ```

## <a name="dps-returns-data-to-the-device"></a>DPS がデバイスにデータを返す
カスタム割り当てポリシー webhook からデバイスに何らかのデータを返す場合は、webhook 応答内の文字列としてそのデータを渡します。 この変更は、以下の "payload" セクションにあります。 
   ```
   { 
       "iotHubHostName": "sample-iot-hub-1.azure-devices.net", 
       "initialTwin": { 
           "tags": { 
               "tag1": true 
               }, 
               "properties": { 
                   "desired": { 
                       "tag2": true 
                    } 
                } 
            }, 
        "payload": "whatever is returned by the webhook" 
    } 
   ```

## <a name="sdk-support"></a>SDK のサポート
この機能は、C、C#、JAVA および Node.js の[クライアント SDK](./index.yml) で使用できます。  

## <a name="next-steps"></a>次のステップ
* Azure IoT Hub および Azure IoT Hub Device Provisioning Service 用に [Azure IoT SDK]( https://github.com/Azure/azure-iot-sdks) を使用する開発