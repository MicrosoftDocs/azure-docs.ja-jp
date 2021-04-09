---
title: Microsoft 接続キャッシュ プレビュー デプロイ シナリオのサンプル | Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: Microsoft 接続キャッシュ プレビュー デプロイ シナリオのサンプル チュートリアル
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: ae07926d7d8c768170e945e916367bee41999571
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101658741"
---
# <a name="microsoft-connected-cache-preview-deployment-scenario-samples"></a>Microsoft 接続キャッシュ プレビュー デプロイ シナリオのサンプル

## <a name="single-level-azure-iot-edge-gateway-no-proxy"></a>単一レベルの Azure IoT Edge ゲートウェイ プロキシなし

次の図は、Azure IoT Edge ゲートウェイが CDN リソースに直接アクセスし、Azure IoT Edge ゲートウェイのインターネットで分離された子デバイスである Raspberry PI などの Azure IoT リーフ デバイスがあるシナリオを示しています。 

  :::image type="content" source="media/connected-cache-overview/disconnected-device-update.png" alt-text="Microsoft 接続キャッシュの切断デバイス更新プログラム" lightbox="media/connected-cache-overview/disconnected-device-update.png":::

1. Azure IoT Hub で、Azure IoT Edge ゲートウェイ デバイスのデプロイに Microsoft 接続キャッシュ モジュールを追加 します (モジュールを取得する方法の詳細については、「`MCC concepts`」を参照してください)。
2. デプロイに対する環境変数を追加します。 環境変数の例を次に示します。

    **環境変数**
    
    | 名前                 | 値                                       |
    | ----------------------------- | --------------------------------------------| 
    | CACHE_NODE_ID                 | 上記の環境変数の説明を参照してください。 |
    | CUSTOMER_ID                   | 上記の環境変数の説明を参照してください。 |
    | CUSTOMER_KEY                  | 上記の環境変数の説明を参照してください。 |
    | STORAGE_ *N* _SIZE_GB           | N = 5                                       |

3. デプロイのコンテナー作成オプションを追加します。 コンテナー作成オプションの例を次に示します。

### <a name="container-create-options"></a>コンテナーの作成オプション

```markdown
{
    "HostConfig": {
        "Binds": [
            "/MicrosoftConnectedCache1/:/nginx/cache1/"
        ],
        "PortBindings": {
            "8081/tcp": [
                {
                    "HostPort": "80"
                }
            ],
            "5000/tcp": [
                {
                    "HostPort": "5100"
                }
            ]
        }
    }
```

Microsoft 接続キャッシュが正常に機能していることを確認するには、モジュールをホスティングする IoT Edge デバイスのターミナル、またはネットワーク上の任意のデバイスで、次のコマンドを実行します。

```bash
    wget "http://<IOT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com
```

## <a name="single-level-azure-iot-edge-gateway-with-outbound-unauthenticated-proxy"></a>認証されていない送信プロキシを使用する単一レベルの Azure IoT Edge ゲートウェイ

このシナリオには、認証されていない送信プロキシを介して CDN リソースにアクセスできる Azure IoT Edge ゲートウェイがあります。 Microsoft 接続キャッシュは、カスタム リポジトリからコンテンツをキャッシュするように構成されており、概要レポートはネットワーク上のいずれかのユーザーに対して表示されています。 設定される MCC 環境変数の例を次に示します。

  :::image type="content" source="media/connected-cache-overview/single-level-proxy.png" alt-text="Microsoft 接続キャッシュの単一レベルのプロキシ" lightbox="media/connected-cache-overview/single-level-proxy.png":::

1. Azure IoT Hub で、Azure IoT Edge ゲートウェイ デバイスのデプロイに Microsoft 接続キャッシュ モジュールを追加します。
2. デプロイに対する環境変数を追加します。 環境変数の例を次に示します。

    **環境変数**

    | 名前                 | 値                                       |
    | ----------------------------- | --------------------------------------------| 
    | CACHE_NODE_ID                 | 上記の環境変数の説明を参照してください。 |
    | CUSTOMER_ID                   | 上記の環境変数の説明を参照してください。 |
    | CUSTOMER_KEY                  | 上記の環境変数の説明を参照してください。 |
    | STORAGE_ *N* _SIZE_GB           | N = 5                                       |
    | CACHEABLE_CUSTOM_1_HOST       | Packagerepo.com:80                          |
    | CACHEABLE_CUSTOM_1_CANONICAL  | Packagerepo.com                             |
    | IS_SUMMARY_ACCESS_UNRESTRICTED| true                                        |
    | UPSTREAM_PROXY                | プロキシ サーバーの IP または FQDN                     |

3. デプロイのコンテナー作成オプションを追加します。 前の例の MCC コンテナー作成オプションに違いはありません。 コンテナー作成オプションの例を次に示します。

### <a name="container-create-options"></a>コンテナーの作成オプション

```json
{
    "HostConfig": {
        "Binds": [
            "/MicrosoftConnectedCache1/:/nginx/cache1/"
        ],
        "PortBindings": {
            "8081/tcp": [
                {
                    "HostPort": "80"
                }
            ],
            "5000/tcp": [
                {
                    "HostPort": "5100"
                }
            ]
        }
    }
```

Microsoft 接続キャッシュが正常に機能していることを確認するには、モジュールをホスティングする Azure IoT Edge デバイスのターミナル、またはネットワーク上の任意のデバイスで、次のコマンドを実行します。

```bash
    wget "http://<Azure IOT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com
```
