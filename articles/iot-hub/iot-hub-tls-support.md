---
title: Azure IoT Hub の TLS サポート
description: IoT Hub と通信するデバイスとサービスにセキュリティで保護された TLS 接続を使用する際のベスト プラクティス
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: jlian
ms.openlocfilehash: 8c52037684215d1672ed813389d0bbace9a03e42
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85080615"
---
# <a name="tls-support-in-iot-hub"></a>IoT Hub の TLS サポート

IoT Hub は、トランスポート層セキュリティ (TLS) を使用して、IoT デバイスとサービスからの接続をセキュリティで保護します。 現時点では 3 つのバージョンの TLS プロトコル (つまり、バージョン 1.0、1.1、および 1.2) がサポートされています。

TLS 1.0 と 1.1 はレガシと見なされており、非推奨となる予定です。 詳細については、[IoT Hub の TLS 1.0 および 1.1 の廃止](iot-hub-tls-deprecating-1-0-and-1-1.md)に関する記事を参照してください。 IoT Hub に接続するときは、TLS 1.2 を優先 TLS バージョンとして使用することを強くお勧めします。

## <a name="tls-12-enforcement-available-in-select-regions"></a>一部のリージョンで利用できる TLS 1.2 の強制

セキュリティを強化するために、TLS バージョン 1.2 を使用するクライアント接続 "*のみ*" を許可し、[推奨される暗号](#recommended-ciphers)の使用を強制するように、IoT Hub を構成します。 この機能は、次のリージョンでのみサポートされています。

* 米国東部
* 米国中南部
* 米国西部 2
* US Gov アリゾナ
* US Gov バージニア州

このためには、サポートされているリージョンのいずれかに新しい IoT Hub をプロビジョニングし、Azure Resource Manager テンプレートの IoT ハブ リソース仕様で `minTlsVersion` プロパティを `1.2` に設定します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Devices/IotHubs",
            "apiVersion": "2020-01-01",
            "name": "<provide-a-valid-resource-name>",
            "location": "<any-of-supported-regions-below>",
            "properties": {
                "minTlsVersion": "1.2"
            },
            "sku": {
                "name": "<your-hubs-SKU-name>",
                "tier": "<your-hubs-SKU-tier>",
                "capacity": 1
            }
        }
    ]
}
```

この構成を使用して作成された IoT Hub リソースは、TLS バージョン 1.0 および 1.1 を使用して接続を試みるデバイスとサービス クライアントを拒否します。 同様に、クライアントの HELLO メッセージに[推奨される暗号](#recommended-ciphers)が示されていない場合、TLS ハンドシェイクは拒否されます。

> [!NOTE]
> `minTlsVersion` プロパティは読み取り専用であり、IoT Hub リソースの作成後は変更できません。 したがって、*すべての* IoT デバイスとサービスが TLS 1.2 および[推奨される暗号](#recommended-ciphers)と互換性があることを、事前に適切にテストし、検証する必要があります。
> 
> フェールオーバー時、IoT Hub の `minTlsVersion` プロパティは、フェールオーバー後に geo ペア リージョンで有効なままになります。

## <a name="recommended-ciphers"></a>推奨される暗号

TLS 1.2 のみを受け入れるように構成されている IoT Hub では、次の推奨される暗号の使用も強制されます。

* `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`

TLS 1.2 の強制が構成されていない IoT Hub では、TLS 1.2 は次の暗号で引き続き動作します。

* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_DHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_DHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_RSA_WITH_AES_256_CBC_SHA256`
* `TLS_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_AES_256_CBC_SHA`
* `TLS_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_3DES_EDE_CBC_SHA`

## <a name="use-tls-12-in-your-iot-hub-sdks"></a>IoT Hub SDK で TLS 1.2 を使用する

次のリンクを使用して、IoT Hub クライアント SDK で TLS 1.2 および許可されている暗号を構成します。

| Language | TLS 1.2 をサポートするバージョン | ドキュメント |
|----------|------------------------------------|---------------|
| C        | タグ 2019-12-11 以降            | [リンク](https://aka.ms/Tls_C_SDK_IoT) |
| Python   | バージョン 2.0.0 以降             | [リンク](https://aka.ms/Tls_Python_SDK_IoT) |
| C#       | バージョン 1.21.4 以降            | [リンク](https://aka.ms/Tls_CSharp_SDK_IoT) |
| Java     | バージョン 1.19.0 以降            | [リンク](https://aka.ms/Tls_Java_SDK_IoT) |
| NodeJS   | バージョン 1.12.2 以降            | [リンク](https://aka.ms/Tls_Node_SDK_IoT) |


## <a name="use-tls-12-in-your-iot-edge-setup"></a>IoT Edge のセットアップで TLS 1.2 を使用する

IoT Edge デバイスは、IoT Hub との通信時に TLS 1.2 を使用するように構成できます。 このためには、[IoT Edge ドキュメント ページ](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md)を使用してください。