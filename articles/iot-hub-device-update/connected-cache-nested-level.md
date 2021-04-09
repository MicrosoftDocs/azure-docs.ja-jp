---
title: アウトバンドの認証されていないプロキシによる Microsoft 接続キャッシュの 2 レベルの入れ子の Azure IoT Edge ゲートウェイ | Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: アウトバンドの認証されていないプロキシによる Microsoft 接続キャッシュの 2 レベルの入れ子の Azure IoT Edge ゲートウェイ チュートリアル
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 0128d0de4f078b62bc9571c8758d80cb26585354
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102615382"
---
# <a name="microsoft-connected-cache-preview-deployment-scenario-sample-two-level-nested-azure-iot-edge-gateway-with-outbound-unauthenticated-proxy"></a>Microsoft 接続キャッシュ プレビュー デプロイ シナリオのサンプル: アウトバンドの認証されていないプロキシによる 2 レベルの入れ子の Azure IoT Edge ゲートウェイ

次の図に示すように、このシナリオでは、Azure IoT Edge ゲートウェイとダウンストリーム Azure IoT Edge デバイスがあります。別の Azure IoT Edge ゲートウェイを親とする 1 つの Azure IoT Edge ゲートウェイと IT DMZ にあるプロキシ サーバーです。 次に、Azure IoT Edge ゲートウェイにデプロイされた両方の MCC モジュールに対して、Azure portal UX で設定される Microsoft 接続キャッシュ環境変数の例を示します。 この例では、Azure IoT Edge ゲートウェイの 2 レベルの構成を示していますが、Microsoft 接続キャッシュでサポートされるアップストリーム ホストの深さに制限はありません。 前の例の MCC コンテナー作成オプションに違いはありません。

Azure IoT Edge ゲートウェイの多層デプロイの構成の詳細については、[ダウンストリーム IoT Edge デバイスの接続 - Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-to-connect-downstream-iot-edge-device?view=iotedge-2020-11&tabs=azure-portal&preserve-view=true) に関するドキュメントを参照してください。 さらに、Azure IoT Edge、Microsoft 接続キャッシュ、およびカスタム モジュールをデプロイする場合、すべてのモジュールが同じコンテナー レジストリに存在する必要があることにも注意してください。

次の図は、CDN リソースへの直接アクセスとして 1 つの Azure IoT Edge ゲートウェイが、Raspberry Pi などの Azure IoT リーフ デバイスの親として機能する別の Azure IoT Edge ゲートウェイの親として機能しているシナリオを示しています。 CDN リソースへのインターネット接続があるのは Azure IoT Edge ゲートウェイの親のみで、Azure IoT Edge の子と Azure IoT デバイスはどちらもインターネットから分離されています。 

  :::image type="content" source="media/connected-cache-overview/nested-level-proxy.png" alt-text="入れ子になった Microsoft 接続キャッシュ" lightbox="media/connected-cache-overview/nested-level-proxy.png":::

## <a name="parent-gateway-configuration"></a>親ゲートウェイ構成

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

## <a name="child-gateway-configuration"></a>子ゲートウェイ構成

>[!Note]
>独自のプライベート レジストリで構成に使用されているコンテナーをレプリケートした場合、モジュールのデプロイの config.xml 設定とランタイム設定を変更する必要があります。 詳細については、[チュートリアル - IoT Edge デバイスの階層の作成 - Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-nested-iot-edge?view=iotedge-2020-11&tabs=azure-portal&preserve-view=true#deploy-modules-to-the-lower-layer-device) に関するページを参照してください。

1. 次の例に示すように、Edge エージェントのイメージ パスを変更します。

```markdown
[agent]
name = "edgeAgent"
type = "docker"
env = {}
[agent.config]
image = "<parent_device_fqdn_or_ip>:8000/iotedge/azureiotedge-agent:1.2.0-rc2"
auth = {}
```
2. 次の例に示すように、Azure IoT Edge デプロイの Edge Hub と Edge エージェントのランタイム設定を変更します。
    
    * [Edge Hub] の下の [イメージ] フィールドに、「```$upstream:8000/iotedge/azureiotedge-hub:1.2.0-rc2```」と入力します
    * [Edge エージェント] の下の [イメージ] フィールドに、「```$upstream:8000/iotedge/azureiotedge-agent:1.2.0-rc2```」と入力します

3. Azure IoT Hub で、Azure IoT Edge ゲートウェイ デバイスのデプロイに Microsoft 接続キャッシュ モジュールを追加します。

   * モジュールの名前を選択します。```ConnectedCache```
   * イメージの URI を変更します。```$upstream:8000/mcc/linux/iot/mcc-ubuntu-iot-amd64:latest```

4. 親デプロイで使用されている同じ環境変数とコンテナー作成オプションを追加します。

Microsoft 接続キャッシュが正常に機能していることを確認するには、モジュールをホスティングする IoT Edge デバイスのターミナル、またはネットワーク上の任意のデバイスで、次のコマンドを実行します。

```bash
    wget "http://<CHILD Azure IoT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com
```