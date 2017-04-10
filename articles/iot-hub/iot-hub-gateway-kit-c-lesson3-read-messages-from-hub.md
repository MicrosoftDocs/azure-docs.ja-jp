---
title: "SensorTag デバイスと Azure IoT Gateway - レッスン 3: メッセージの読み取り | Microsoft Docs"
description: "IoT ハブからメッセージを読み取るには、ホスト コンピューターでサンプル コードを実行します。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "クラウドのデータ, クラウドのデータの収集, IoT クラウド サービス, IoT データ"
ms.assetid: cc88be24-b5c0-4ef2-ba21-4e8f77f3e167
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: 0a5dd7e010ba46b724bb6a2526dc2a2730acc10f
ms.lasthandoff: 01/25/2017


---

# <a name="read-messages-from-your-iot-hub"></a>IoT ハブからのメッセージの読み取り

## <a name="what-you-will-do"></a>学習内容

- IoT ハブからメッセージを読み取るには、ホスト コンピューターでサンプル コードを実行します。

問題が発生した場合は、[トラブルシューティングのページ](iot-hub-gateway-kit-c-troubleshooting.md)で解決策を探してください。

## <a name="what-you-will-learn"></a>学習内容

gulp ツールを使用して IoT hub からメッセージを読み取る方法。

## <a name="what-you-need"></a>必要なもの

- レッスン 3 で正常に実行された BLE サンプル アプリケーション。

## <a name="get-your-iot-hub-and-device-connection-strings"></a>IoT ハブとデバイスの接続文字列の取得

デバイス接続文字列は、デバイスが IoT ハブに接続する際に使用されます。 IoT ハブ接続文字列は、IoT ハブ内の ID レジストリに接続して、IoT ハブへの接続を許可されるデバイスを管理するために使用されます。

- 次のコマンドを実行して、リソース グループ内のすべての IoT ハブを一覧表示します。

   ```bash
   az iot hub list -g iot-gateway --query [].name
   ```

   値を変更していない場合、`{resource group name}` の値として `iot-gateway` を使用します。
- 次のコマンドを実行して、IoT ハブ接続文字列を取得します。

   ```bash
   az iot hub show-connection-string --name {my hub name} -g iot-gateway
   ```

   `{my hub name}` は、レッスン 2 で指定した名前です。

## <a name="configure-the-device-connection-for-the-sample-code"></a>サンプル コード用のデバイス接続の構成

デバイス構成ファイル `config-azure.json` を更新して、ホスト コンピューターで IoT ハブからメッセージを読み取ることができるようにします。 そのためには、次の手順に従います。

1. コンソール ウィンドウで次のコマンドを実行して、Visual Studio Code で `config-azure.json` を開きます。

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-azure.json
   # For MacOS or Ubuntu
   code ~/.iot-hub-getting-started/config-azure.json
   ```

2. `config-azure.json` ファイルの値を次のように置き換えます。

   ![config-azure のスクリーンショット](media/iot-hub-gateway-kit-lessons/lesson3/config_azure.png)

   `[IoT hub connection string]` を、取得済みの IOT ハブ接続文字列に置き換えます。

## <a name="read-messages-from-your-iot-hub"></a>IoT ハブからのメッセージの読み取り

TI SensorTag がある場合は、SensorTag が既にオンになっていることを確認します。 次のコマンドを使用して、ゲートウェイ サンプル アプリケーションを実行して IoT Hub メッセージを読み取ります。

```bash
gulp run --iot-hub
```

このコマンドは、2 秒ごとに SensorTag またはシミュレートされたデバイスから温度データを読み取ってパッケージ化し、IoT ハブにメッセージを送信する BLE サンプル アプリケーションを実行します。 さらに、メッセージを受信する子プロセスを生成します。

送受信されるすべてのメッセージは、ホスト コンピューターのコンソール ウィンドウにすぐに表示されます。 このサンプル アプリケーション インスタンスは 40 秒後に自動的に終了します。

![メッセージの送受信を行ったサンプル アプリケーション](media/iot-hub-gateway-kit-lessons/lesson3/gulp_run_read_hub.png)

## <a name="summary"></a>まとめ

IoT hub からメッセージを読み取るサンプル コードを実行しました。 Azure Table ストレージに格納されているメッセージを読み取る準備ができました。

## <a name="next-steps"></a>次のステップ
[Azure Function App と Azure ストレージ アカウントの作成](iot-hub-gateway-kit-c-lesson4-deploy-resource-manager-template.md)



