---
title: "シミュレートされたデバイスと Azure IoT Gateway - レッスン 3: メッセージの読み取り | Microsoft Docs"
description: "IoT ハブからメッセージを読み取るには、ホスト コンピューターでサンプル コードを実行します。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "クラウドのデータ, クラウドのデータの収集, IoT クラウド サービス, IoT データ"
ms.assetid: 5a6ec9c1-d83c-41c1-beaf-7c0d3395d77f
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: 08ccd35c6e6a8e77f0fd4637f14a1f27730560d6
ms.lasthandoff: 01/25/2017


---

# <a name="read-messages-from-your-iot-hub"></a>IoT ハブからのメッセージの読み取り

## <a name="what-you-will-do"></a>学習内容

- IoT ハブからメッセージを読み取るには、ホスト コンピューターでサンプル コードを実行します。

問題が発生した場合は、[トラブルシューティングのページ](iot-hub-gateway-kit-c-sim-troubleshooting.md)で解決策を探してください。

## <a name="what-you-will-learn"></a>学習内容

gulp ツールを使用して IoT hub からメッセージを読み取る方法。

## <a name="what-you-need"></a>必要なもの

- 「[シミュレート デバイスからクラウドへのアップロードを実行するサンプル アプリケーションの構成と実行](iot-hub-gateway-kit-c-sim-lesson3-configure-simulated-device-app.md)」のシミュレート デバイス サンプル。

## <a name="get-your-iot-hub-and-device-connection-strings"></a>IoT ハブとデバイスの接続文字列の取得

デバイス接続文字列は、シミュレート デバイスが IoT ハブに接続するために使用されます。 IoT ハブ接続文字列は、IoT ハブ内の ID レジストリに接続して、IoT ハブへの接続を許可されるデバイスを管理するために使用されます。

- 次のコマンドを実行して、リソース グループ内のすべての IoT ハブを一覧表示します。

   ```bash
   az iot hub list -g iot-gateway --query [].name
   ```

   値を変更していない場合は、`{resource group name}` の値として `iot-gateway` を使用します。
- 次のコマンドを実行して、IoT ハブ接続文字列を取得します。

   ```bash
   az iot hub show-connection-string --name {my hub name} -g iot-gateway
   ```

   `{my hub name}` は、レッスン 2 で指定した名前です。

## <a name="configure-the-device-connection-for-the-sample-code"></a>サンプル コード用のデバイス接続の構成

次の手順を実行して、`config-azure.json` 内の IoT ハブとデバイスの接続構成を更新します。

1. コンソール ウィンドウで次のコマンドを実行して、Visual Studio Code で `config-azure.json` を開きます。

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-azure.json
   # For MacOS or Ubuntu
   code ~/.iot-hub-getting-started/config-azure.json
   ```

2. `config-azure.json` ファイルの値を次のように置き換えます。

   ![config-azure のスクリーンショット](media/iot-hub-gateway-kit-lessons/lesson3/config_azure.png)

   `[IoT hub connection string]` を、IoT ハブ接続文字列に置き換えます。

## <a name="read-messages-from-your-iot-hub"></a>IoT ハブからのメッセージの読み取り

次のコマンドを使用して、シミュレート デバイス サンプル アプリケーションを実行して IoT Hub メッセージを読み取ります。

```bash
gulp run --iot-hub
```

このコマンドは、2 秒ごとに IoT ハブにメッセージを送信するアプリケーションを実行します。 さらに、メッセージを受信する子プロセスを生成します。

送受信されるすべてのメッセージは、ホスト コンピューターのコンソール ウィンドウにすぐに表示されます。 このアプリケーションは 40 秒後に終了します。

![シミュレーション サンプル アプリケーションの実行と送受信されたメッセージ](media/iot-hub-gateway-kit-lessons/lesson3/gulp_run_read_hub_simudev.png)

## <a name="summary"></a>まとめ

シミュレート デバイスを使用して IoT ハブにデータを送信するサンプル アプリケーションを正常に実行しました。 さらに、IoT ハブに送信されたメッセージを読み取りました。

## <a name="next-steps"></a>次のステップ
[Azure Function App と Azure ストレージ アカウントの作成](iot-hub-gateway-kit-c-sim-lesson4-deploy-resource-manager-template.md)



