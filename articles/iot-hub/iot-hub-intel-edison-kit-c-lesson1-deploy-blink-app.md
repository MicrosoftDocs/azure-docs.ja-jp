---
title: "Azure IoT への Intel Edison (C) の接続 - レッスン 1: アプリケーションのデプロイ | Microsoft Docs"
description: "GitHub のサンプル C アプリケーションを複製し、gulp を実行してこのアプリケーションを Intel Edison ボードにデプロイします。 このサンプル アプリケーションでは、ボードに接続された LED を&2; 秒間隔で点滅させます。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "Arduino LED プロジェクト, Arduino LED 点滅, Arduino LED 点滅コード, Arduino 点滅プログラム, Arduino 点滅の例"
ms.assetid: b02dfd3f-28fd-4b52-8775-eb0eaf74d707
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 475b25f02715a60493e79ecd2170854019dfc4ac
ms.openlocfilehash: c0589d488be5ec62686551b97d8949e5fed2f0a3
ms.lasthandoff: 01/25/2017


---
# <a name="create-and-deploy-the-blink-application"></a>点滅アプリケーションを作成してデプロイする
## <a name="what-you-will-do"></a>学習内容
GitHub のサンプル C アプリケーションを複製し、gulp ツールを使ってサンプル アプリケーションを Intel Edison にデプロイします。 このサンプル アプリケーションでは、ボードに接続された LED を&2; 秒間隔で点滅させます。 問題が発生した場合は、[トラブルシューティングのページ][troubleshooting]で解決策を探してください。

## <a name="what-you-will-learn"></a>学習内容
* Edison にサンプル アプリケーションをデプロイして実行する方法。

## <a name="what-you-need"></a>必要なもの
次の作業を正常に完了している必要があります。

* [デバイスの構成][configure-your-device]
* [ツールの入手][get-the-tools]

## <a name="open-the-sample-application"></a>サンプル アプリケーションを開く
サンプル アプリケーションを開くには、次の手順を実行します。

1. 次のコマンドを実行して、GitHub からサンプル リポジトリを複製します。

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-c-edison-getting-started.git
   ```
2. 以下のコマンドを実行して、Visual Studio Code でサンプル アプリケーションを開きます。

   ```bash
   cd iot-hub-c-edison-getting-started
   cd Lesson1
   code .
   ```

   ![リポジトリの構造][repo-structure]

`app` サブフォルダーのこのファイルは、LED を制御するコードを含むキー ソース ファイルです。

### <a name="install-application-dependencies"></a>アプリケーションの依存関係のインストール
次のコマンドを実行して、サンプル アプリケーションに必要なライブラリやその他のモジュールをインストールします。

```bash
npm install
```

## <a name="configure-the-device-connection"></a>デバイス接続の構成
デバイス接続を構成するには、次の手順を実行します。

1. 次のコマンドを実行して、デバイス構成ファイルを生成します。

   ```bash
   gulp init
   ```

   構成ファイル `config-edison.json` には、Edison へのログインに使うユーザー資格情報が含まれています。 ユーザー資格情報の漏洩を避けるために、構成ファイルはコンピューター上のホーム フォルダーの `.iot-hub-getting-started` サブフォルダーに生成されます。

2. 次のコマンドを実行して、Visual Studio Code でデバイス構成ファイルを開きます。

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-edison.json

   # For MacOS or Ubuntu
   code ~/.iot-hub-getting-started/config-edison.json
   ```

3. プレースホルダー `[device hostname or IP address]`と `[device password]` を、前のレッスンでメモしておいた IP アドレスとパスワードに置き換えます。

   ![config.json](media/iot-hub-intel-edison-lessons/lesson1/vscode-config-mac.png)

ご利用ありがとうございます。 Edison の最初のサンプル アプリケーションの作成に成功しました。

## <a name="deploy-and-run-the-sample-application"></a>サンプル アプリケーションのデプロイと実行
### <a name="install-the-azure-iot-hub-sdk-on-edison"></a>Edison での Azure IoT Hub SDK のインストール
次のコマンドを実行して、Edison に Azure IoT Hub SDK をインストールします。

```bash
gulp install-tools
```

ネットワーク接続に応じて、このタスクが完了するまでに時間がかかる場合があります。 これは、1 つの Edison に対して&1; 回だけ実行する必要があります。

### <a name="deploy-and-run-the-sample-app"></a>サンプル アプリのデプロイと実行
次のコマンドを実行して、サンプル アプリケーションをデプロイして実行します。

```bash
gulp deploy && gulp run
```

### <a name="verify-the-app-works"></a>アプリの動作確認
サンプル アプリケーションは、LED が 20 回点滅した後に自動的に終了します。 LED が点滅していない場合、一般的な問題の解決策については、[トラブルシューティング ガイド][troubleshooting]をご覧ください。

![LED の点滅][led-blinking]

## <a name="summary"></a>概要
Edison を操作するために必要なツールをインストールし、Edison にサンプル アプリケーションをデプロイして、LED を点滅させました。 これで、Edison を Azure IoT Hub に接続してメッセージを送受信する別のサンプル アプリケーションを作成、デプロイ、実行する準備が整いました。

## <a name="next-steps"></a>次のステップ
[Azure ツールを入手する][get-the-azure-tools]

<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-c-troubleshooting.md
[Configure-your-device]: iot-hub-intel-edison-kit-c-lesson1-configure-your-device.md
[get-the-tools]: iot-hub-intel-edison-kit-c-lesson1-get-the-tools-win32.md
[repo-structure]: media/iot-hub-intel-edison-lessons/lesson1/repo_structure_c.png
[led-blinking]: media/iot-hub-intel-edison-lessons/lesson1/led_blinking_c.jpg
[get-the-azure-tools]: iot-hub-intel-edison-kit-c-lesson2-get-azure-tools-win32.md

