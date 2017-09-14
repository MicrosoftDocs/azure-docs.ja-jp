---
title: "Azure IoT Edge の IoT ゲートウェイでのデータ変換 | Microsoft Docs"
description: "IoT ゲートウェイを使用して、Azure IoT Edge のカスタマイズされたモジュールを通してセンサー データのフォーマットを変換します。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "IoT ゲートウェイ データ変換、IoT ゲートウェイでのデータ変換"
ms.assetid: 75f2573d-500b-4405-bff7-61021c4c3500
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/25/2017
ms.author: xshi
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: 5f5151c9e250fb8a19a953c6212dd2675004dc55
ms.contentlocale: ja-jp
ms.lasthandoff: 06/26/2017

---
# <a name="use-iot-gateway-for-sensor-data-transformation-with-azure-iot-edge"></a>Azure IoT Edge でセンサー データを変換するための IoT ゲートウェイを使用する

> [!NOTE]
> このチュートリアルを開始する前に、次のレッスンを順を完了しておいてください。
> * [Intel NUC を IoT ゲートウェイとしてセットアップする](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md)
> * [IoT ゲートウェイを使用してモノをクラウドに接続する -  SensorTag から Azure IoT Hub へ](iot-hub-gateway-kit-c-iot-gateway-connect-device-to-cloud.md)

Iot ゲートウェイの目的の 1 つは、収集されたデータをクラウドに送信する前に処理することです。 Azure IoT Edge には、データ処理ワークフローを形成するために作成して組み立てることができるモジュールが導入されています。 モジュールは、メッセージを受信し、何らかのアクションを実行した後、他のモジュールで処理するために先に進みます。

## <a name="what-you-learn"></a>学習内容

メッセージを SensorTag から別の形式に変換するモジュールを作成する方法を学びます。

## <a name="what-you-do"></a>作業内容

* 受信したメッセージを .json 形式に変換するモジュールを作成します。
* モジュールをコンパイルします。
* Azure IoT Edge からモジュールを BLE サンプル アプリケーションに追加します。
* サンプル アプリケーションを実行します。

## <a name="what-you-need"></a>必要なもの

* 次のチュートリアルを順に完了していること。
  * [Intel NUC を IoT ゲートウェイとしてセットアップする](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md)
  * [IoT ゲートウェイを使用してモノをクラウドに接続する -  SensorTag から Azure IoT Hub へ](iot-hub-gateway-kit-c-iot-gateway-connect-device-to-cloud.md)
* ホスト コンピューターで実行される SSH クライアント。 Windows では PuTTY をお勧めします。 Linux と macOS には既に SSH クライアントが付属しています。
* IP アドレスと、SSH クライアントからゲートウェイにアクセスするためのユーザー名とパスワード。
* インターネット接続。

## <a name="create-a-module"></a>モジュールを作成する

1. ホスト コンピューターで SSH クライアントを実行して IoT ゲートウェイに接続します。
1. 次のコマンドを実行して、変換モジュールのソース ファイルを GitHub から IoT ゲートウェイのホーム ディレクトリに複製します。

   ```bash
   cd ~
   git clone https://github.com/Azure-Samples/iot-hub-c-intel-nuc-gateway-customized-module.git
   ```

   これは、C プログラミング言語で記述された Azure Edge のネイティブ モジュールです。 このモジュールは、受信したメッセージの形式を、次のものに変換します。

   ```json
   {"deviceId": "Intel NUC Gateway", "messageId": 0, "temperature": 0.0}
   ```

## <a name="compile-the-module"></a>モジュールをコンパイルする

モジュールをコンパイルするには、次のコマンドを実行します。

```bash
cd iot-hub-c-intel-nuc-gateway-customized-module/my_module
# change the build script runnable
chmod 777 build.sh
# remove the invalid windows character
sed -i -e "s/\r$//" build.sh
# run the build shell script
./build.sh
```

コンパイルが完了すると、`libmy_module.so` ファイルができます。 このファイルの絶対パスをメモしておきます。

## <a name="add-the-module-to-the-ble-sample-application"></a>モジュールを BLE サンプル アプリケーションに追加する

1. 次のコマンドを実行して、samples フォルダーに移動します。

   ```bash
   cd /usr/share/azureiotgatewaysdk/samples
   ```

1. 次のコマンドを実行して、構成ファイルを開きます。

   ```bash
   vi ble_gateway.json
   ```

1. 次のコードを `modules` セクションに挿入することで、モジュールを追加します。

   ```json
   {
       "name": "MyModule",
       "loader": {
           "name": "native",
           "entrypoint":{
               "module.path": "[Your libmy_module.so path]"
               }
            },
        "args": null
    },
    ```

1. コードの `[Your libmy_module.so path]` を、libmy_module.so ファイルの絶対パスに置き換えます。
1. `links` セクションのコードを、次のコードに置き換えます。

   ```json
   {
       "source": "SensorTag",
       "sink": "MyModule"
   },
   {
       "source": "MyModule",
       "sink": "mapping"
   }
   ```

1. `ESC` キーを押し、`:wq` と入力してファイルを保存します。

## <a name="run-the-sample-application"></a>サンプル アプリケーションを実行する

1. SensorTag の電源を入れます。
1. 次のコマンドを実行して、SSL_CERT_FILE 環境変数を設定します。

   ```bash
   export SSL_CERT_FILE=/etc/ssl/certs/ca-certificates.crt
   ```

1. 次のコマンドを実行して、追加したモジュールを含むサンプル アプリケーションを実行します。

   ```bash
   ./ble_gateway ble_gateway.json
   ```

## <a name="next-steps"></a>次のステップ

IoT ゲートウェイを使用して、SensorTag からのメッセージを .json 形式に変換しました。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

