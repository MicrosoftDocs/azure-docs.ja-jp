---
title: "Azure IoT への Raspberry Pi (C) の接続 - レッスン 1: アプリのデプロイ | Microsoft Docs"
description: "GitHub のサンプル C アプリケーションを複製し、gulp を使ってこのアプリケーションを Raspberry Pi 3 ボードにデプロイします。 このサンプル アプリケーションでは、ボードに接続された LED を&2; 秒間隔で点滅させます。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "Raspberry Pi の LED の点滅, Raspberry Pi の点滅 LED"
ms.assetid: f601d1e1-2bc3-4cc5-a6b1-0467e5304dcf
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 5b16230178fa82c7f227e3e3f8742fa16b00c719
ms.lasthandoff: 01/24/2017


---
# <a name="create-and-deploy-the-blink-application"></a>点滅アプリケーションを作成してデプロイする
## <a name="what-you-will-do"></a>学習内容
GitHub のサンプル C アプリケーションを複製し、gulp ツールを使ってサンプル アプリケーションを Raspberry Pi 3 にデプロイします。 このサンプル アプリケーションでは、ボードに接続された LED を&2; 秒間隔で点滅させます。 問題が発生した場合は、[トラブルシューティングのページ](iot-hub-raspberry-pi-kit-c-troubleshooting.md)で解決策を探してください。

## <a name="what-you-will-learn"></a>学習内容
この記事では、次のことについて説明します。

* `device-discover-cli` ツールを使って、Pi のネットワーク情報を取得する方法。
* Pi にサンプル アプリケーションをデプロイし、実行する方法。
* Pi でリモート実行されるアプリケーションをデプロイし、デバッグする方法。

## <a name="what-you-need"></a>必要なもの
次の作業を正常に完了している必要があります。

* [デバイスの構成](iot-hub-raspberry-pi-kit-c-lesson1-configure-your-device.md)
* [ツールの入手](iot-hub-raspberry-pi-kit-c-lesson1-get-the-tools-win32.md)

## <a name="obtain-the-ip-address-and-host-name-of-pi"></a>Pi の IP アドレスとホスト名の取得
Windows のコマンド プロンプトか、macOS または Ubuntu のターミナルを開き、次のコマンドを実行します。

```bash
devdisco list --eth
```

次のような出力が表示されます。

![デバイスの検出](media/iot-hub-raspberry-pi-lessons/lesson1/device_discovery.png)

Pi の `IP address` と `hostname` をメモします。 この情報はこの記事の後半で必要になります。

> [!NOTE]
> 必ず Pi をコンピューターと同じネットワークに接続してください。 たとえば、コンピューターがワイヤレス ネットワークに接続され、Pi がワイヤード (有線) ネットワークに接続されている場合、devdisco の出力に IP アドレスが表示されないことがあります。

## <a name="open-the-sample-application"></a>サンプル アプリケーションを開く
サンプル アプリケーションを開くには、次の手順を実行します。

1. 次のコマンドを実行して、GitHub からサンプル リポジトリを複製します。
   
    ```bash
    git clone https://github.com/Azure-Samples/iot-hub-c-raspberrypi-getting-started.git
    ```
2. 以下のコマンドを実行して、Visual Studio Code でサンプル アプリケーションを開きます。
   
    ```bash
    cd iot-hub-c-raspberrypi-getting-started
    cd Lesson1
    code .
    ```

![リポジトリの構造](media/iot-hub-raspberry-pi-lessons/lesson1/vscode-blink-c-mac.png)

`app` サブフォルダーの `main.c` ファイルは、LED を制御するコードを含むキー ソース ファイルです。

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
   
   構成ファイル `config-raspberrypi.json` には、Pi へのログインに使うユーザー資格情報が含まれています。 ユーザー資格情報の漏洩を避けるために、構成ファイルはコンピューター上のホーム フォルダーの `.iot-hub-getting-started` サブフォルダーに生成されます。

2. 次のコマンドを実行して、Visual Studio Code でデバイス構成ファイルを開きます。
   
   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-raspberrypi.json
   
   # For MacOS or Ubuntu
   code ~/.iot-hub-getting-started/config-raspberrypi.json
   ```

3. プレースホルダー `[device hostname or IP address]` を、前の「Pi の IP アドレスとホスト名の取得」で取得した IP アドレスまたはホスト名で置き換えます。
   
   ![config.json](media/iot-hub-raspberry-pi-lessons/lesson1/vscode-config-mac.png)

> [!NOTE]
> Raspberry Pi に接続するときにユーザー名とパスワードの代わりに SSH キーを使用できます。 そのためには、**ssh-keygen** と **ssh-copy-id pi@\<デバイス アドレス\>** を使用してキーを生成する必要があります。
>
> Windows では、**Git Bash** でこれらのコマンドを使用できます。
>
> MacOS では、**brew install ssh-copy-id** を実行する必要があります。
>
> キーを Raspberry Pi に正常にアップロードした後、**config-raspberrypi.json** の **device_password** を **device_key_path** プロパティで置き換えます。
>
> 更新後の行は次のようになります。
> ```javascript
> "device_user_name": "pi",
> "device_key_path": "id_rsa",
> ```

お疲れさまでした。 Pi の最初のサンプル アプリケーションを正常に作成できました。

## <a name="deploy-and-run-the-sample-application"></a>サンプル アプリケーションのデプロイと実行
### <a name="install-the-azure-iot-hub-sdk-on-pi"></a>Pi での Azure IoT Hub SDK のインストール
次のコマンドを実行して Pi に Azure IoT Hub SDK をインストールします。

```bash
gulp install-tools
```

初めてこのタスクを実行する場合は、数分かかることがあります。

### <a name="deploy-and-run-the-sample-app"></a>サンプル アプリのデプロイと実行
次のコマンドを実行して、サンプル アプリケーションをデプロイして実行します。

```bash
gulp deploy && gulp run
```

### <a name="verify-the-app-works"></a>アプリの動作確認
サンプル アプリケーションは、LED が 20 回点滅した後に自動的に終了します。 LED が点滅していない場合、一般的な問題の解決策については、[トラブルシューティング ガイド](iot-hub-raspberry-pi-kit-c-troubleshooting.md)をご覧ください。
![LED の点滅](media/iot-hub-raspberry-pi-lessons/lesson1/led_blinking.jpg)

## <a name="summary"></a>概要
Pi を操作するために必要なツールをインストールし、Pi にサンプル アプリケーションをデプロイして、LED を点滅させました。 これで、Pi を Azure IoT Hub に接続してメッセージを送受信する別のサンプル アプリケーションを作成、デプロイ、実行する準備が整いました。

## <a name="next-steps"></a>次のステップ
[Azure ツールの入手](iot-hub-raspberry-pi-kit-c-lesson2-get-azure-tools-win32.md)


