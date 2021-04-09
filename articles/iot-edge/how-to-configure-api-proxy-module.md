---
title: API モジュールを構成する - Azure IoT Edge | Microsoft Docs
description: IoT Edge ゲートウェイ階層の API プロキシ モジュールをカスタマイズする方法について説明します。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: 1070a4c8daecfedae513f2fd8738c27abfb33078
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103200577"
---
# <a name="configure-the-api-proxy-module-for-your-gateway-hierarchy-scenario-preview"></a>ゲートウェイ階層のシナリオ用に API プロキシ モジュールを構成する (プレビュー)

[!INCLUDE [iot-edge-version-202011](../../includes/iot-edge-version-202011.md)]

API プロキシ モジュールを使用すると、IoT Edge デバイスは、クラウド サービスに直接接続するのではなく、ゲートウェイを介して HTTP 要求を送信できます。 この記事では、ゲートウェイ階層の要件をサポートするようにモジュールをカスタマイズできるようにするための構成オプションについて説明します。

>[!NOTE]
>この機能には、Linux コンテナーを実行している IoT Edge バージョン 1.2 (パブリック プレビュー段階) が必要です。

ネットワーク アーキテクチャによっては、ゲートウェイの背後にある IoT Edge デバイスがクラウドに直接アクセスできない場合があります。 クラウド サービスに接続しようとするモジュールは失敗します。 API プロキシ モジュールは、代わりにゲートウェイ階層のレイヤーを通過するようにモジュール接続を再ルーティングすることにより、この構成でダウンストリーム IoT Edge デバイスをサポートします。 これは、クライアントがトンネリングなしで HTTPS を介して、ただし各レイヤーで接続を終了することによって、複数のサービスと通信するための安全な方法を提供します。 API プロキシ モジュールは、最上位レイヤーの IoT Edge デバイスに達するまで、ゲートウェイ階層内の IoT Edge デバイス間のプロキシ モジュールとして機能します。 その時点では、最上位レイヤーの IoT Edge デバイスで実行されているサービスがこれらの要求を処理し、API プロキシ モジュールは、1 つのポートを介してローカル サービスからクラウドへのすべての HTTP トラフィックをプロキシします。

API プロキシ モジュールを使用すると、下位レイヤーのデバイスでコンテナー イメージをプルしたり、BLOB をストレージにプッシュしたりできるなど、ゲートウェイ階層のさまざまなシナリオを実現できます。 プロキシ規則の構成によって、データのルーティング方法が定義されます。 この記事では、いくつかの一般的な構成オプションについて説明します。

## <a name="deploy-the-proxy-module"></a>プロキシ モジュールをデプロイする

API プロキシ モジュールは、Microsoft Container Registry (MCR) `mcr.microsoft.com/azureiotedge-api-proxy:latest` から入手できます。

API プロキシ モジュールは、Azure Marketplace から直接デプロイすることもできます。[IoT Edge API プロキシ](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.azureiotedge-api-proxy?tab=Overview)。

## <a name="understand-the-proxy-module"></a>プロキシ モジュールについて

API プロキシ モジュールは、nginx リバース プロキシを利用して、ネットワーク レイヤーを介してデータをルーティングします。 プロキシはモジュールに埋め込まれています。つまり、モジュール イメージはプロキシ構成をサポートする必要があります。 たとえば、プロキシが特定のポートでリッスンしている場合、モジュールはそのポートを開いている必要があります。

プロキシは、モジュールに埋め込まれている既定の構成ファイルから始まります。 新しい構成は、その[モジュール ツイン](../iot-hub/iot-hub-devguide-module-twins.md)を使用してクラウドからモジュールに渡すことができます。 また、環境変数を使用して、デプロイ時に構成設定を有効または無効にすることができます。

この記事では、最初に既定の構成ファイルと、環境変数を使用してその設定を有効にする方法に焦点を当てます。 その後、構成ファイルを最後にカスタマイズする方法について説明します。

### <a name="default-configuration"></a>既定の構成

API プロキシ モジュールには、一般的なシナリオをサポートしてカスタマイズを可能にする既定の構成が付属しています。 モジュールの環境変数を使用して、既定の構成を制御できます。

現在、既定の環境変数は次のとおりです。

| 環境変数 | 説明 |
| -------------------- | ----------- |
| `PROXY_CONFIG_ENV_VAR_LIST` | 更新するすべての変数をコンマ区切りのリストで一覧表示します。 この手順によって、正しくない構成設定が誤って変更されるのを防ぐことができます。
| `NGINX_DEFAULT_PORT` | nginx プロキシがリッスンするポートを変更します。 この環境変数を更新する場合は、選択したポートがモジュール dockerfile にも公開されており、デプロイ マニフェストでポートのバインドとして宣言されていることを確認してください。<br><br>既定値は 443 です。<br><br>Azure Marketplace からデプロイした場合は、edgeHub モジュールとの競合を防ぐために、既定のポートが 8000 に更新されます。 詳細については、「[開いているポートを最小限に抑える](#minimize-open-ports)」を参照してください。 |
| `DOCKER_REQUEST_ROUTE_ADDRESS` | Docker 要求をルーティングするアドレス。 最上位レイヤーのデバイスで、レジストリ モジュールを指すようにこの変数を変更します。<br><br>既定値は親ホスト名です。 |
| `BLOB_UPLOAD_ROUTE_ADDRESS` | BLOB レジストリ要求をルーティングするアドレス。 最上位レイヤーのデバイスで、BLOB ストレージ モジュールを指すようにこの変数を変更します。<br><br>既定値は親ホスト名です。 |

## <a name="minimize-open-ports"></a>開いているポートを最小限に抑える

開いているポートの数を最小限に抑えるために、API プロキシ モジュールは、edgeHub モジュールをターゲットとするトラフィックを含むすべての HTTPS トラフィック (ポート 443) をリレーする必要があります。 API プロキシ モジュールは、既定でポート 443 のすべての edgeHub トラフィックを再ルーティングするように構成されています。

開いているポートを最小限に抑えるようにデプロイを構成するには、次の手順に従います。

1. EdgeHub モジュールの設定をポート 443 でバインドしないように更新します。そうしないと、ポートのバインドの競合が発生します。 既定では、edgeHub モジュールはポート 443、5671、および 8883 でバインドします。 ポート 443 のバインドを削除し、他の 2 つをそのままにします。

   ```json
   {
     "HostConfig": {
       "PortBindings": {
         "5671/tcp": [
           {
             "HostPort": "5671"
           }
         ],
         "8883/tcp": [
           {
             "HostPort": "8883"
           }
         ]
       }
     }
   }
   ```

1. ポート 443 でバインドするように API プロキシ モジュールを構成します。

   1. **NGINX_DEFAULT_PORT** 環境変数の値を `443` に設定します。
   1. コンテナー作成オプションを更新して、ポート 443 にバインドします。

      ```json
      {
        "HostConfig": {
          "PortBindings": {
            "443/tcp": [
              {
                "HostPort": "443"
              }
            ]
          }
        }
      }
      ```

開いているポートを最小限に抑える必要がない場合は、edgeHub モジュールでポート 443 を使用し、別のポートでリッスンするように API プロキシ モジュールを構成することができます。 たとえば、API プロキシ モジュールは、**NGINX_DEFAULT_PORT** 環境変数の値を `8000` に設定し、ポート 8000 のポートのバインドを作成することによって、ポート 8000 でリッスンできます。

## <a name="enable-container-image-download"></a>コンテナー イメージのダウンロードを有効にする

API プロキシ モジュールの一般的なユース ケースは、下位レイヤーの IoT Edge デバイスでコンテナー イメージをプルできるようにすることです。 このシナリオでは、[Docker レジストリ モジュール](https://hub.docker.com/_/registry)を使用して、クラウドからコンテナー イメージを取得し、最上位レイヤーにキャッシュします。 API プロキシは、すべての HTTPS 要求をリレーして、最上位レイヤーのレジストリ モジュールによって提供されるコンテナー イメージを下位レイヤーからダウンロードします。

このシナリオでは、ダウンストリーム IoT Edge デバイスが、イメージのコンテナー レジストリではなく、後に API プロキシ モジュールのポート番号が続くドメイン名 `$upstream` を指す必要があります。 (例: `$upstream:8000/azureiotedge-api-proxy:1.0`)。

このユース ケースは、[ゲートウェイを使用した IoT Edge デバイスの階層の作成](tutorial-nested-iot-edge.md)のチュートリアルで説明されています。

**最上位レイヤー** で次のモジュールを構成します。

* Docker レジストリ モジュール
  * *registry* のようなわかりやすい名前を使用してモジュールを構成し、要求を受信するためにモジュールのポートを公開します。
  * コンテナー レジストリにマップするようにモジュールを構成します。
* API プロキシ モジュール
  * 以下の環境変数を構成します。

    | 名前 | 値 |
    | ---- | ----- |
    | `DOCKER_REQUEST_ROUTE_ADDRESS` | レジストリ モジュールの名前と開いているポート。 たとえば、「 `registry:5000` 」のように入力します。 |
    | `NGINX_DEFAULT_PORT` | nginx プロキシがダウンストリーム デバイスからの要求をリッスンするポート。 たとえば、「 `8000` 」のように入力します。 |

  * 次の createOptions を構成します。

    ```json
    {
       "HostConfig": {
          "PortBindings": {
             "8000/tcp": [
                {
                   "HostPort": "8000"
                }
             ]
          }
       }
    }
    ```

このシナリオでは、次のモジュールを **下位レイヤー** に構成します。

* API プロキシ モジュール
  * 以下の環境変数を構成します。

    | 名前 | 値 |
    | ---- | ----- |
    | `NGINX_DEFAULT_PORT` | nginx プロキシがダウンストリーム デバイスからの要求をリッスンするポート。 たとえば、「 `8000` 」のように入力します。 |

  * 次の createOptions を構成します。

    ```json
    {
       "HostConfig": {
          "PortBindings": {
             "8000/tcp": [
                {
                   "HostPort": "8000"
                }
             ]
          }
       }
    }
    ```  

## <a name="enable-blob-upload"></a>BLOB アップロードを有効にする

API プロキシ モジュールのもう 1 つのユース ケースは、下位レイヤーの IoT Edge デバイスで BLOB をアップロードできるようにすることです。 このユース ケースでは、モジュール ログのアップロードやサポート バンドルのアップロードなど、下位レイヤーのデバイスでのトラブルシューティング機能が有効になります。

このシナリオでは、最上位レイヤーの [IoT Edge 上の Azure Blob Storage](https://azuremarketplace.microsoft.com/marketplace/apps/azure-blob-storage.edge-azure-blob-storage) モジュールを使用して、BLOB の作成とアップロードを処理します。

**最上位レイヤー** で次のモジュールを構成します。

* IoT Edge 上の Azure Blob Storage モジュール。
* API プロキシ モジュール
  * 以下の環境変数を構成します。

    | 名前 | 値 |
    | ---- | ----- |
    | `BLOB_UPLOAD_ROUTE_ADDRESS` | Blob Storage モジュールの名前と開いているポート。 たとえば、「 `azureblobstorageoniotedge:1102` 」のように入力します。 |
    | `NGINX_DEFAULT_PORT` | nginx プロキシがダウンストリーム デバイスからの要求をリッスンするポート。 たとえば、「 `8000` 」のように入力します。 |

  * 次の createOptions を構成します。

    ```json
    {
       "HostConfig": {
          "PortBindings": {
             "8000/tcp": [
                {
                   "HostPort": "8000"
                }
             ]
          }
       }
    }
    ```

このシナリオでは、次のモジュールを **下位レイヤー** に構成します。

* API プロキシ モジュール
  * 以下の環境変数を構成します。

    | 名前 | 値 |
    | ---- | ----- |
    | `NGINX_DEFAULT_PORT` | nginx プロキシがダウンストリーム デバイスからの要求をリッスンするポート。 たとえば、「 `8000` 」のように入力します。 |

  * 次の createOptions を構成します。

    ```json
    {
       "HostConfig": {
          "PortBindings": {
             "8000/tcp": [
                {
                   "HostPort": "8000"
                }
             ]
          }
       }
    }
    ```

次の手順を使用して、サポート バンドルまたはログ ファイルを、最上位レイヤーにある Blob Storage モジュールにアップロードします。

1. Azure Storage Explorer または REST API のいずれかを使用して、BLOB コンテナーを作成します。 詳細については、「[IoT Edge 上の Azure Blob Storage を使用してエッジにデータを格納する](how-to-store-data-blob.md)」を参照してください。
1. 「[IoT Edge の展開からログを取得する](how-to-retrieve-iot-edge-logs.md)」の手順に従ってログを要求するか、バンドルのアップロードをサポートします。ただし、Blob Storage モジュールのアドレスの代わりに、ドメイン名 `$upstream` と開いているプロキシ ポートを使用します。 次に例を示します。

   ```json
   {
      "schemaVersion": "1.0",
      "sasUrl": "https://$upstream:8000/myBlobStorageName/myContainerName?SAS_key",
      "since": "2d",
      "until": "1d",
      "edgeRuntimeOnly": false
   }
   ```

## <a name="edit-the-proxy-configuration"></a>プロキシ構成を編集する

既定の構成ファイルは API プロキシ モジュールに埋め込まれていますが、モジュール ツインを使用して、クラウド経由でモジュールに新しい構成を渡すことができます。

独自の構成を作成する場合でも、環境を使用してデプロイごとに設定を調整できます。 使用する構文は以下のとおりです。

* 環境変数の値を取得するには、`${MY_ENVIRONMENT_VARIABLE}` を使用します。
* 環境変数の値に基づいて設定を有効または無効にするには、条件付きステートメントを使用します。

   ```conf
   #if_tag ${MY_ENVIRONMENT_VARIABLE}
       statement to execute if environment variable evaluates to 1
   #endif_tag ${MY_ENVIRONMENT_VARIABLE}

   #if_tag !${MY_ENVIRONMENT_VARIABLE}
       statement to execute if environment variable evaluates to 0
   #endif_tag !${MY_ENVIRONMENT_VARIABLE}
   ```

API プロキシ モジュールがプロキシ構成を解析する際は、最初に、`PROXY_CONFIG_ENV_VAR_LIST` に示されているすべての環境変数が、置換を使用して指定された値に置き換えられます。 次に、`#if_tag` と `#endif_tag` のペアの間にあるすべてのものが置き換えられます。 その後、モジュールが、解析された構成を nginx リバース プロキシに提供します。

プロキシ構成を動的に更新するには、次の手順に従います。

1. 構成ファイルを作成します。 この既定のテンプレートを参照 ([nginx_default_config](https://github.com/Azure/iotedge/blob/master/edge-modules/api-proxy-module/templates/nginx_default_config.conf)) として使用することができます。
1. 構成ファイルのテキストをコピーし、base64 に変換します。
1. エンコードされた構成ファイルをモジュール ツインの `proxy_config` の必要なプロパティの値として貼り付けます。

   ![エンコードされた構成ファイルを proxy_config プロパティの値として貼り付ける](./media/how-to-configure-api-proxy-module/change-config.png)

## <a name="next-steps"></a>次のステップ

API プロキシ モジュールを使用して、[ダウンストリームの IoT Edge デバイスを Azure IoT Edge ゲートウェイに接続](how-to-connect-downstream-iot-edge-device.md)します。