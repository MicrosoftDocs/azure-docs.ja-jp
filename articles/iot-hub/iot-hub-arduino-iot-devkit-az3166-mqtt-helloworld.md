---
title: Azure MQTT クライアント ライブラリを使用して MQTT サーバーにメッセージを送信する | Microsoft Docs
description: クライアントとして DevKit を使用して、MQTT サーバーにメッセージを送信します
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/02/2018
ms.author: liydu
ms.openlocfilehash: 60520f5a72fd7e27d4ea64ac76511a00a727426e
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2019
ms.locfileid: "57552012"
---
# <a name="send-messages-to-an-mqtt-server"></a>MQTT サーバーにメッセージを送信する

モノのインターネット (IoT) のシステムでは、断続的、低品質、または低速なインターネット接続を扱うことがよくあります。 MQTT は、このような課題を念頭に開発された、マシン間 (M2M) の接続プロトコルです。 

ここで使用する MQTT クライアント ライブラリは、[Eclipse Paho](https://www.eclipse.org/paho/) プロジェクトの一部です。このプロジェクトは、複数の転送方法で MQTT を使用するための API を提供します。

## <a name="what-you-learn"></a>学習内容

このプロジェクトでは、以下を学習します。
- MQTT クライアント ライブラリを使用して MQTT ブローカーにメッセージを送信する方法
- MXChip Iot DevKit を MQTT クライアントとして構成する方法

## <a name="what-you-need"></a>必要なもの

[ファースト ステップ ガイド](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started)に従って以下のことを行います。

* DevKit をWi-Fi に接続
* 開発環境の準備

## <a name="open-the-project-folder"></a>プロジェクト フォルダーを開く

1. DevKit が既にご利用のコンピューターに接続されている場合は、コンピューターから切断します。

2. VS Code を起動します。

3. DevKit をコンピューターに接続します。

## <a name="open-the-mqttclient-sample"></a>MQTTClient サンプルを開く

左側の **[Arduino Examples]\(Arduino の例\)** セクションを展開し、**[Examples for MXCHIP AZ3166] > [MQTT]** を参照して、**[MQTTClient]** を選択します。 プロジェクト フォルダーを含む新しい VS Code ウィンドウが開きます。

> [!NOTE]
> コマンド パレットから例を開くこともできます。 `Ctrl+Shift+P` キー (macOS: `Cmd+Shift+P` キー) を使用してコマンド パレットを開き、「**Arduino**」と入力します。次に、**[Arduino:Examples]\(Arduino: 例\)** を見つけて選択します。

## <a name="build-and-upload-the-arduino-sketch-to-the-devkit"></a>DevKit に対する Arduino スケッチのビルドとアップロード

`Ctrl+P` (macOS: `Cmd+P`) と入力して `task device-upload` を実行します。 アップロードが完了すると、DevKit が再起動し、スケッチを実行します。

![device-upload](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/device-upload.jpg)

> [!NOTE]
> "エラー:AZ3166:不明なパッケージ" というエラー メッセージが表示される場合があります。 このエラーは、ボード パッケージ インデックスが正しく更新されない場合に発生します。 このエラーを解決するには、[IoT DevKit FAQ の「Development (開発)」セクション](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development)を参照してください。

## <a name="test-the-project"></a>プロジェクトのテスト

VS Code で、次の手順を実行し、シリアル モニターを開いて設定します。

1. ステータス バーの `COM[X]` の単語をクリックして、右側の COM ポートを `STMicroelectronics` で設定します。![set-com-port](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/set-com-port.jpg)

2. ステータス バーの電源プラグ アイコンをクリックして、シリアル モニターを開きます。![serial-monitor](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/serial-monitor.jpg)
  
3. ステータス バーで、ボー レートを表す数字をクリックして `115200` に設定します。![set-baud-rate](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/set-baud-rate.jpg)

シリアル モニターに、サンプル スケッチによって送信されたすべてのメッセージが表示されます。 スケッチが DevKit を Wi-Fi に接続します。 Wi-fi 接続が成功すると、スケッチが MQTT ブローカーにメッセージを送信します。 その後、このサンプルは QoS 0 と QoS 1 をそれぞれ使用して、2 つの "iot.eclipse.org" メッセージを繰り返し送信します。

![serial-output](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/serial-output.jpg)

## <a name="problems-and-feedback"></a>問題とフィードバック

問題が発生した場合は、[IoT DevKit の FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) に関するページを参照するか、以下のチャネルを使用してお問い合わせください。

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="see-also"></a>関連項目

* [IoT DevKit AZ3166 をクラウドの Azure IoT Hub に接続する](iot-hub-arduino-iot-devkit-az3166-get-started.md)
* [シェイク、シェイクしてツイート](iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message.md)

## <a name="next-steps"></a>次の手順

ここでは、MXChip Iot DevKit を MQTT クライアントとして構成し、MQTT クライアント ライブラリを使用して MQTT ブローカーにメッセージを送信する方法を説明しました。推奨される次の手順は、以下のとおりです。

* [Azure IoT リモート監視ソリューション アクセラレータの概要](https://docs.microsoft.com/azure/iot-suite/)
* [MXChip IoT DevKit デバイスを Azure IoT Central アプリケーションに接続する](https://docs.microsoft.com/microsoft-iot-central/howto-connect-devkit)
