---
title: Azure IoT Hub の TLS サポート
description: IoT Hub と通信するデバイスおよびサービスにセキュリティで保護された TLS 接続を使用する方法について説明します
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: jlian
ms.openlocfilehash: d36a7917693aef9063ade473759f2f451d3a677f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98234020"
---
# <a name="transport-layer-security-tls-support-in-iot-hub"></a>IoT Hub でのトランスポート層セキュリティ (TLS) のサポート

IoT Hub は、トランスポート層セキュリティ (TLS) を使用して、IoT デバイスとサービスからの接続をセキュリティで保護します。 現時点では 3 つのバージョンの TLS プロトコル (つまり、バージョン 1.0、1.1、および 1.2) がサポートされています。

TLS 1.0 と 1.1 はレガシと見なされており、非推奨となる予定です。 詳細については、[IoT Hub の TLS 1.0 および 1.1 の廃止](iot-hub-tls-deprecating-1-0-and-1-1.md)に関する記事を参照してください。 今後の問題を回避するには、IoT Hub に接続するときに、TLS バージョンとして TLS 1.2 のみを使用してください。

## <a name="iot-hubs-server-tls-certificate"></a>IoT Hub のサーバー TLS 証明書

TLS ハンドシェイク中には、IoT Hub から RSA キー付きサーバー証明書が接続元のクライアントに提示されます。 そのルートは、Baltimore CyberTrust Root CA です。 最近、Microsoft は TLS サーバー証明書に対する変更をロールアウトしましたので、それは新しい中間証明機関 (ICA) から発行されるようになります。 詳細については、「[IoT Hub TLS 証明書の更新](https://azure.microsoft.com/updates/iot-hub-tls-certificate-update/)」を参照してください。

### <a name="elliptic-curve-cryptography-ecc-server-tls-certificate-preview"></a>楕円曲線暗号 (ECC) サーバー TLS 証明書 (プレビュー)

IoT Hub ECC サーバー TLS 証明書は、パブリック プレビューとして提供されています。 ECC 証明書の検証 (ECC のみの暗号スイートを使用) を使用すると、RSA 証明書と同様のセキュリティが提供されると同時に、コンピューティング、メモリ、および帯域幅の使用が最大 40% 削減されます。 このような削減は、IoT デバイスにとって重要です。そのプロファイルおよびメモリのサイズが小さくなるためであり、ネットワーク帯域幅が制限された環境でのユース ケースに対応することができます。 

IoT Hub の ECC サーバー証明書をプレビューするには、次のようにします。

1. [プレビュー モードをオンにした状態で新しい IoT ハブを作成します](iot-hub-preview-mode.md)。
1. ECDSA の暗号スイート "*のみ*" を含め、RSA のものについてはいずれも "*除外*" するように、[クライアントを構成](#tls-configuration-for-sdk-and-iot-edge)してください。 ECC 証明書のパブリック プレビューでサポートされている暗号スイートは次のとおりです。
    - `TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256`
    - `TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384`
    - `TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256`
    - `TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384`
1. クライアントをプレビュー IoT ハブに接続します。

## <a name="tls-12-enforcement-available-in-select-regions"></a>一部のリージョンで利用できる TLS 1.2 の強制

セキュリティを強化するために、TLS バージョン 1.2 を使用するクライアント接続 "*のみ*" を許可し、[暗号スイート](#cipher-suites)の使用を強制するように、IoT Hub を構成します。 この機能は、次のリージョンでのみサポートされています。

* 米国東部
* 米国中南部
* 米国西部 2
* US Gov アリゾナ
* US Gov バー ジニア (TLS 1.0/1.1 のサポートはこのリージョンでは利用できません - TLS 1.2 の強制を有効にする必要があります。そうしないと、IoT ハブの作成は失敗します)

TLS 1.2 の強制を有効にするには、[Azure portal での IoT ハブの作成](iot-hub-create-through-portal.md)に関する記事の手順に従ってください。

- 前述の一覧から **リージョン** を選択します。
- **[管理] -> [詳細設定] -> [トランスポート層セキュリティ (TLS)] -> [TLS の最小バージョン]** で、**1.2** を選択します。 この設定は、サポートされているリージョンで作成された IoT ハブにのみ表示されます。

    :::image type="content" source="media/iot-hub-tls-12-enforcement.png" alt-text="IoT ハブの作成時に TLS 1.2 の強制を有効にする方法を示すスクリーンショット":::

ARM テンプレートを使用して作成するには、サポートされているリージョンのいずれかに新しい IoT ハブをプロビジョニングし、次のリソース仕様で `minTlsVersion` プロパティを `1.2` に設定します。

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

この構成を使用して作成された IoT Hub リソースは、TLS バージョン 1.0 および 1.1 を使用して接続を試みるデバイスとサービス クライアントを拒否します。 同様に、`ClientHello` メッセージに[推奨される暗号](#cipher-suites)が示されていない場合、TLS ハンドシェイクは拒否されます。

> [!NOTE]
> `minTlsVersion` プロパティは読み取り専用であり、IoT Hub リソースの作成後は変更できません。 したがって、*すべての* IoT デバイスとサービスが TLS 1.2 および [推奨される暗号](#cipher-suites)と互換性があることを、事前に適切にテストし、検証する必要があります。
> 
> フェールオーバー時、IoT Hub の `minTlsVersion` プロパティは、フェールオーバー後に geo ペア リージョンで有効なままになります。

## <a name="cipher-suites"></a>暗号スイート

TLS 1.2 のみを受け入れるように構成されている IoT Hub では、次の推奨される暗号スイートの使用も強制されます。

* `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`

TLS 1.2 の強制が構成されていない IoT Hub では、TLS 1.2 は次の暗号スイートで引き続き動作します。

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

クライアントからは、`ClientHello` の間、上位の暗号スイートの一覧を提案できます。 ただし、IoT Hub ではサポートされないものもあります (`ECDHE-ECDSA-AES256-GCM-SHA384` など)。 その場合、IoT Hub はクライアントの設定に従おうとしますが、最終的には `ServerHello` と暗号化スイートをネゴシエートします。

## <a name="tls-configuration-for-sdk-and-iot-edge"></a>SDK および IoT Edge 用の TLS 構成

次のリンクを使用して、IoT Hub クライアント SDK で TLS 1.2 および許可されている暗号を構成します。

| Language | TLS 1.2 をサポートするバージョン | ドキュメント |
|----------|------------------------------------|---------------|
| C        | タグ 2019-12-11 以降            | [リンク](https://aka.ms/Tls_C_SDK_IoT) |
| Python   | バージョン 2.0.0 以降             | [リンク](https://aka.ms/Tls_Python_SDK_IoT) |
| C#       | バージョン 1.21.4 以降            | [リンク](https://aka.ms/Tls_CSharp_SDK_IoT) |
| Java     | バージョン 1.19.0 以降            | [リンク](https://aka.ms/Tls_Java_SDK_IoT) |
| NodeJS   | バージョン 1.12.2 以降            | [リンク](https://aka.ms/Tls_Node_SDK_IoT) |

IoT Edge デバイスは、IoT Hub との通信時に TLS 1.2 を使用するように構成できます。 このためには、[IoT Edge ドキュメント ページ](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md)を使用してください。

## <a name="device-authentication"></a>デバイスの認証

TLS ハンドシェイクに成功すると、IoT Hub では、対称キーか X.509 証明書を利用してデバイスを認証できます。 証明書ベースの認証の場合、ECC など、あらゆる X.509 証明書を利用できます。 IoT Hub では、指定した拇印または認証機関 (CA) を利用し、証明書の有効性を検証します。 詳細については、「[サポートされている X.509 証明書](iot-hub-devguide-security.md#supported-x509-certificates)」をご覧ください。

## <a name="tls-maximum-fragment-length-negotiation-preview"></a>TLS 最大フラグメント長ネゴシエーション (プレビュー)

IoT Hub では、TLS 最大フラグメント長ネゴシエーションもサポートされています。これは、TLS フレーム サイズ ネゴシエーションと呼ばれることもあります。 この機能はパブリック プレビュー段階にあります。 

この機能を使用すると、プレーンテキストの最大フラグメント長を、既定の 2^14 バイトより小さい値に指定できます。 ネゴシエートされると、IoT Hub とクライアントによってメッセージのフラグメント化が開始され、すべてのフラグメントがネゴシエートされた長さよりも確実に小さくなります。 この動作は、コンピューティングまたはメモリに制約があるデバイスに役立ちます。 詳細については、[公式の TLS 拡張機能仕様](https://tools.ietf.org/html/rfc6066#section-4)を参照してください。

このパブリック プレビュー機能の公式 SDK サポートはまだ利用できません。 開始するには

1. [プレビュー モードをオンにした状態で新しい IoT ハブを作成します](iot-hub-preview-mode.md)。
1. OpenSSL を使用する場合は、[SSL_CTX_set_tlsext_max_fragment_length](https://manpages.debian.org/testing/libssl-doc/SSL_CTX_set_max_send_fragment.3ssl.en.html) を呼び出して、フラグメントのサイズを指定します。
1. クライアントをプレビュー IoT Hub に接続します。

## <a name="next-steps"></a>次のステップ

- IoT Hub のセキュリティおよびアクセス制御の詳細については、「[IoT Hub へのアクセスの制御](iot-hub-devguide-security.md)」を参照してください。
- デバイス認証に X509 証明書を使用する方法の詳細については、「[X.509 CA 証明書を使用したデバイス認証](iot-hub-x509ca-overview.md)」を参照してください。
