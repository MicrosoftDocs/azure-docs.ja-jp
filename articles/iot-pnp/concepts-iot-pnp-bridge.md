---
title: IoT プラグ アンド プレイ ブリッジ | Microsoft Docs
description: IoT プラグ アンド プレイ ブリッジについて、およびそれを使用して Windows または Linux ゲートウェイに接続されている既存のデバイスを IoT プラグ アンド プレイ デバイスとして接続する方法について説明します。
author: usivagna
ms.author: ugans
ms.date: 1/20/2021
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: a45efd90043ecb4d457db7ed39651f1a9b5bbd4d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98890609"
---
# <a name="iot-plug-and-play-bridge"></a>IoT プラグ アンド プレイ ブリッジ

IoT プラグ アンド プレイ ブリッジは、Windows または Linux ゲートウェイに接続されている既存のデバイスを IoT プラグ アンド プレイ デバイスとして接続するためのオープンソース アプリケーションです。 Windows または Linux マシンにアプリケーションをインストールして構成した後、それを使用して、接続されているデバイスを IoT ハブに接続することができます。 このブリッジを使用して、接続されているデバイスによって送信されるテレメトリに IoT プラグ アンド プレイ インターフェイスをマップしたり、デバイスのプロパティを操作したり、コマンドを呼び出したりできます。

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-high-level.png" alt-text="左側には、IoT プラグ アンド プレイ ブリッジを含む Windows または Linux PC に接続されている既存のセンサー (有線とワイヤレスの両方) がいくつかあります。IoT プラグ アンド プレイ ブリッジは、右側の IoT ハブに接続されます":::

IoT プラグ アンド プレイ ブリッジは、Windows 10 または Linux を実行している任意の IoT デバイス、業務用 PC、サーバー、またはゲートウェイにスタンドアロンの実行可能ファイルとしてデプロイできます。 また、アプリケーション コードにコンパイルすることもできます。 単純な構成 JSON ファイルにより、接続されているどのデバイスまたは周辺機器を Azure に公開するかが、IoT プラグ アンド プレイ ブリッジに指示されます。

## <a name="supported-protocols-and-sensors"></a>サポートされているプロトコルとセンサー

IoT プラグ アンド プレイ ブリッジでは、次の種類の周辺機器が既定でサポートされており、アダプターのドキュメントへのリンクがあります。

|周辺装置|Windows|Linux|
|---------|---------|---------|
|[Bluetooth センサー アダプター](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/bluetooth_sensor_adapter.md)で、検出された Bluetooth Low Energy (BLE) 対応センサーを接続します。       |はい|いいえ|
|[カメラ アダプター](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/camera_adapter.md)で、Windows 10 デバイスのカメラを接続します。               |はい|いいえ|
|[Modbus アダプター](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/modbus_adapters.md)で、Modbus デバイスのセンサーを接続します。              |はい|はい|
|[MQTT アダプター](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/mqtt_adapter.md)で、MQTT ブローカーを使用するデバイスを接続します。                  |はい|はい|
|[SerialPnP アダプター](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/serialpnp/Readme.md)で、シリアル接続を介して通信するデバイスを接続します。               |はい|はい|
|[Windows USB 周辺機器](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/coredevicehealth_adapter.md)では、アダプターでサポートされているデバイス インターフェイス クラスの一覧を使用して、特定のハードウェア ID を持つデバイスを接続します。  |はい|適用しない|

IoT プラグ アンド プレイ ブリッジを拡張して追加のデバイス プロトコルをサポートする方法については、「[IoT プラグ アンド プレイ ブリッジの拡張](howto-author-pnp-bridge-adapter.md)」をご覧ください。 IoT プラグ アンド プレイ ブリッジをビルドしてデプロイする方法については、「[IoT プラグ アンド プレイ ブリッジをビルドおよびデプロイする](howto-build-deploy-extend-pnp-bridge.md)」を参照してください。

## <a name="iot-plug-and-play-bridge-architecture"></a>IoT プラグ アンド プレイ ブリッジのアーキテクチャ

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-components.png" alt-text="左側には、IoT プラグ アンド プレイ ブリッジを含む Windows または Linux PC に接続されているさまざまな周辺機器を示すボックスがいくつかあります。上部には、ブリッジに対する構成ポイントとしてラベル付けされたボックスが表示されています。ブリッジは、図の右側にある IoT ハブに接続されます。":::

### <a name="iot-plug-and-play-bridge-adapters"></a>IoT プラグ アンド プレイ ブリッジ アダプター

IoT プラグ アンド プレイ ブリッジでは、さまざまな種類のデバイスに対する一連の IoT プラグ アンド プレイ ブリッジ アダプターがサポートされています。 *アダプター マニフェスト* では、ブリッジに対するアダプターを静的に定義します。

ブリッジ アダプター マネージャーは、マニフェストを使用して、アダプター関数を識別し、呼び出します。 アダプター マネージャーは、構成ファイルに一覧表示されているインターフェイス コンポーネントに必要なブリッジ アダプターに対してのみ、create 関数を呼び出します。 各 IoT プラグ アンド プレイ コンポーネントに対して、アダプター インスタンスが作成されます。

ブリッジ アダプターは、デジタル ツイン インターフェイス ハンドルを作成し、取得します。 アダプターは、このハンドルを使用して、デバイスの機能をデジタル ツインにバインドします。

ブリッジ アダプターは、構成ファイルの情報を使用しながら、次の手法を使用して、ブリッジを介してデバイス全体でデジタル ツイン通信を行えるようにします。

- 通信チャネルを直接確立します。
- 通信チャネルが使用可能になるまで待機するデバイス ウォッチャーを作成します。

### <a name="configuration-file"></a>構成ファイル

IoT プラグ アンド プレイ ブリッジは、次の内容を指定する JSON ベースの構成ファイルを使用します。

- IoT ハブまたは IoT Central アプリケーションに接続する方法:オプションには、接続文字列、認証パラメーター、または Device Provisioning Service (DPS) があります。
- ブリッジが使用する IoT プラグ アンド プレイ機能モデルの場所。 このモデルでは、IoT プラグ アンド プレイ デバイスの機能が定義されており、静的で変更不可です。
- IoT プラグ アンド プレイ インターフェイス コンポーネントと、各コンポーネントの次の情報の一覧:
- インターフェイス ID とコンポーネント名。
- コンポーネントとの対話に必要なブリッジ アダプター。
- ブリッジ アダプターがデバイスとの通信を確立するために必要なデバイス情報。 たとえば、ハードウェア ID や、アダプター、インターフェイス、プロトコルに関する特定の情報などです。
- アダプターが類似のデバイスとの複数の通信の種類をサポートしている場合は、オプションのブリッジ アダプターのサブタイプまたはインターフェイスの構成。 例では、Bluetooth センサー コンポーネントをどのように構成できるかを示します。

    ```json
    {
      "_comment": "Component BLE sensor",
      "pnp_bridge_component_name": "blesensor1",
      "pnp_bridge_adapter_id": "bluetooth-sensor-pnp-adapter",
      "pnp_bridge_adapter_config": {
        "bluetooth_address": "267541100483311",
        "blesensor_identity" : "Blesensor1"
      }
    }
    ```

- グローバル ブリッジ アダプター パラメーターの省略可能な一覧。 たとえば、Bluetooth センサー ブリッジ アダプターには、サポートされている構成の辞書があります。 Bluetooth センサー アダプターを必要とするインターフェイス コンポーネントは、次のいずれかの構成を `blesensor_identity` として選択できます。

    ```json
    {
      "pnp_bridge_adapter_global_configs": {
        "bluetooth-sensor-pnp-adapter": {
          "Blesensor1" : {
            "company_id": "0x499",
            "endianness": "big",
            "telemetry_descriptor": [
              {
                "telemetry_name": "humidity",
                "data_parse_type": "uint8",
                "data_offset": 1,
                "conversion_bias": 0,
                "conversion_coefficient": 0.5
              },
              {
                "telemetry_name": "temperature",
                "data_parse_type": "int8",
                "data_offset": 2,
                "conversion_bias": 0,
                "conversion_coefficient": 1.0
              },
              {
                "telemetry_name": "pressure",
                "data_parse_type": "int16",
                "data_offset": 4,
                "conversion_bias": 0,
                "conversion_coefficient": 1.0
              },
              {
                "telemetry_name": "acceleration_x",
                "data_parse_type": "int16",
                "data_offset": 6,
                "conversion_bias": 0,
                "conversion_coefficient": 0.00980665
              },
              {
                "telemetry_name": "acceleration_y",
                "data_parse_type": "int16",
                "data_offset": 8,
                "conversion_bias": 0,
                "conversion_coefficient": 0.00980665
              },
              {
                "telemetry_name": "acceleration_z",
                "data_parse_type": "int16",
                "data_offset": 10,
                "conversion_bias": 0,
                "conversion_coefficient": 0.00980665
              }
            ]
          }
        }
      }
    }
    ```

## <a name="download-iot-plug-and-play-bridge"></a>IoT プラグ アンド プレイ ブリッジをダウンロードする

「[IoT プラグ アンド プレイ ブリッジのリリース](https://github.com/Azure/iot-plug-and-play-bridge/releases)」で、サポートされているアダプターを含む事前ビルドされたバージョンのブリッジをダウンロードし、最新のリリースの資産の一覧を拡張することができます。 ご利用のオペレーティング システム用の最新バージョンのアプリケーションをダウンロードします。

[GitHub の IoT プラグ アンド プレイ ブリッジ](https://github.com/Azure/iot-plug-and-play-bridge)のソース コードをダウンロードして表示することもできます。

## <a name="next-steps"></a>次の手順

ここでは、IoT プラグ アンド プレイ ブリッジのアーキテクチャの概要を示しました。次の手順では以下の詳細を参照してください。

- [Linux または Windows 上で実行されている IoT プラグ アンド プレイ ブリッジのサンプルを IoT Hub に接続する方法](./howto-use-iot-pnp-bridge.md)
- [IoT プラグ アンド プレイ ブリッジのビルドとデプロイ](howto-build-deploy-extend-pnp-bridge.md)
- [IoT プラグ アンド プレイ ブリッジの拡張](howto-author-pnp-bridge-adapter.md)
- [GitHub の IoT プラグ アンド プレイ ブリッジ](https://github.com/Azure/iot-plug-and-play-bridge)
