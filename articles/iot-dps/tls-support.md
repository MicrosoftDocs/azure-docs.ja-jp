---
title: Azure IoT Device Provisioning Service (DPS) TLS サポート
description: IoT Device Provisioning Service (DPS) と通信するデバイスとサービスにセキュリティで保護された TLS 接続を使用する際のベスト プラクティス
services: iot-dps
author: wesmc7777
ms.service: iot-dps
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: wesmc
ms.openlocfilehash: 3a8910cf0e81bd041d74ef95f45220f1c1e0b34c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91761296"
---
# <a name="tls-support-in-azure-iot-hub-device-provisioning-service-dps"></a>Azure IoT Hub Device Provisioning Service (DPS) の TLS サポート

DPS では、[トランスポート層セキュリティ (TLS)](http://wikipedia.org/wiki/Transport_Layer_Security) を使用して IoT デバイスからの接続をセキュリティで保護します。 

現在 DPS でサポートされている TLS プロトコルのバージョンは次のとおりです。 
* TLS 1.2

TLS 1.0 と 1.1 はレガシと見なされており、非推奨となる予定です。 詳細については、[IoT Hub の TLS 1.0 および 1.1 の廃止](../iot-hub/iot-hub-tls-deprecating-1-0-and-1-1.md)に関する記事を参照してください。 

## <a name="restrict-connections-to-tls-12"></a>TLS 1.2 に接続を制限する

セキュリティを強化するためには、TLS バージョン 1.2 を使用するデバイス クライアント接続 "*のみ*" を許可し、[推奨される暗号](#recommended-ciphers)の使用を強制するように、DPS インスタンスを構成することをお勧めします。

これを行うには、新しい DPS リソースをプロビジョニングし、Azure Resource Manager テンプレートの DPS リソース仕様で `minTlsVersion` プロパティを `1.2` に設定します。 次のサンプル テンプレート JSON では、新しい DPS インスタンスに `minTlsVersion` プロパティが指定されます。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Devices/ProvisioningServices",
            "apiVersion": "2020-01-01",
            "name": "<provide-a-valid-DPS-resource-name>",
            "location": "<any-region>",
            "properties": {
                "minTlsVersion": "1.2"
            },
            "sku": {
                "name": "S1",
                "capacity": 1
            },
        }     
    ]
}
```

テンプレートは次の Azure CLI コマンドでデプロイできます。 

```azurecli
az deployment group create -g <your resource group name> --template-file template.json
```

Resource Manager テンプレートで DPS リソースを作成する詳細については、[Azure Resource Manager テンプレートで DPS を設定する方法](quick-setup-auto-provision-rm.md)に関するページを参照してください。

この構成を使用して作成された DPS リソースは、TLS バージョン 1.0 および 1.1 を使用して接続を試みるデバイスを拒否します。 同様に、デバイス クライアントの HELLO メッセージに[推奨される暗号](#recommended-ciphers)が示されていない場合、TLS ハンドシェイクは拒否されます。

> [!NOTE]
> `minTlsVersion` プロパティは読み取り専用であり、DPS リソースの作成後は変更できません。 したがって、*すべての* IoT デバイスが TLS 1.2 および [推奨される暗号](#recommended-ciphers)と互換性があることを、事前に適切にテストし、検証する必要があります。


> [!NOTE]
> フェールオーバー時、DPS の `minTlsVersion` プロパティは、フェールオーバー後に geo ペア リージョンで有効なままになります。

## <a name="recommended-ciphers"></a>推奨される暗号

TLS 1.2 のみを受け入れるように構成されている DPS インスタンスでは、次の暗号スイートの使用も強制されます。


| 推奨される TLS 1.2 暗号スイート |
| :--- |
| `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`<br>`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`<br>`TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`<br>`TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256` |


### <a name="legacy-cipher-suites"></a>レガシ暗号スイート 

これらの暗号スイートは、現在はまだ DPS によってサポートされていますが、非推奨になる予定です。 可能な場合には、上記の推奨される暗号スイートを使用してください。

| オプション #1 (セキュリティ強化) |
| :--- |
| `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P384   (uses SHA-1)`<br>`TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256   (uses SHA-1)`<br>`TLS_RSA_WITH_AES_256_GCM_SHA384           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_128_GCM_SHA256           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_256_CBC_SHA256           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_128_CBC_SHA256           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_256_CBC_SHA              (uses SHA-1, lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_128_CBC_SHA              (uses SHA-1, lack of Perfect Forward Secrecy)` |

| オプション #2 (パフォーマンス向上) |
| :--- |
| `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256   (uses SHA-1)`<br>`TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P384   (uses SHA-1)`<br>`TLS_RSA_WITH_AES_128_GCM_SHA256           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_256_GCM_SHA384           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_128_CBC_SHA256           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_256_CBC_SHA256           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_128_CBC_SHA              (uses SHA-1, lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_256_CBC_SHA              (uses SHA-1, lack of Perfect Forward Secrecy)` |


## <a name="use-tls-12-in-the-iot-sdks"></a>IoT SDK で TLS 1.2 を使用する

次のリンクを使用して、Azure IoT クライアント SDK で TLS 1.2 および許可されている暗号を構成します。

| Language | TLS 1.2 をサポートするバージョン | ドキュメント |
|----------|------------------------------------|---------------|
| C        | タグ 2019-12-11 以降            | [リンク](https://aka.ms/Tls_C_SDK_IoT) |
| Python   | バージョン 2.0.0 以降             | [リンク](https://aka.ms/Tls_Python_SDK_IoT) |
| C#       | バージョン 1.21.4 以降            | [リンク](https://aka.ms/Tls_CSharp_SDK_IoT) |
| Java     | バージョン 1.19.0 以降            | [リンク](https://aka.ms/Tls_Java_SDK_IoT) |
| NodeJS   | バージョン 1.12.2 以降            | [リンク](https://aka.ms/Tls_Node_SDK_IoT) |

## <a name="use-tls-12-with-iot-hub"></a>IoT Hub と共に TLS 1.2 を使用する

IoT Hub は、デバイスとの通信時に TLS 1.2 を使用するように構成できます。 詳細については、[IoT Hub の TLS 1.0 および 1.1 の廃止](../iot-hub/iot-hub-tls-deprecating-1-0-and-1-1.md)に関する記事を参照してください。

## <a name="use-tls-12-with-iot-edge"></a>IoT Edge と共に TLS 1.2 を使用する

IoT Edge デバイスは、IoT Hub および DPS との通信時に TLS 1.2 を使用するように構成できます。 詳しくは、[IoT Edge ドキュメントのページ](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md)を参照してください。
