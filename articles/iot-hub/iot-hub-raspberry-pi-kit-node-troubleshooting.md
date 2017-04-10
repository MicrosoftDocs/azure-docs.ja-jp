---
title: "Azure IoT への Raspberry Pi (C) の接続 - トラブルシューティング | Microsoft Docs"
description: "Raspberry Pi の Node.js エクスペリエンスに関するトラブルシューティング ページ"
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "IoT の問題, モノのインターネットの問題"
ms.assetid: 22cf50dc-8206-42a2-a1fc-f75fa85135fa
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 2c5e2955584b165ca0ca95ce424a2050fb3f6ccd
ms.lasthandoff: 01/24/2017


---
# <a name="troubleshooting"></a>トラブルシューティング
## <a name="hardware-issues"></a>ハードウェアの問題
### <a name="the-application-runs-well-but-the-led-is-not-blinking"></a>アプリケーションは正常に実行できているものの、LED が点滅しない
この問題は、ハードウェアの回路の接続が原因であると見て間違いありません。 以下の手順に従って問題を特定してください。

1. 基盤で適切な **GPIO** を選択していることを確認します。 使うポートは、**GPIO GND (ピン 6)** と **GPIO 04 (ピン 7)** の 2 つです。
2. LED の極性が正しいことを確認します。 長い方の脚が、**プラス**を示すアノード ピンです。
3. Raspberry Pi 3 の **3.3V ピン**と **GND ピン**を使用します。 Pi を DC 電源として使用し、 LED が正常に動作することを確認してください。

![LED の仕様](media/iot-hub-raspberry-pi-lessons/troubleshooting/led_spec.png)

### <a name="other-hardware-issues"></a>ハードウェアに関するその他の問題
Raspberry Pi 3 に関してよくある問題の解決方法については、[公式のトラブルシューティング ページ](http://elinux.org/R-Pi_Troubleshooting)を参照してください。

## <a name="nodejs-package-issues"></a>Node.js パッケージの問題
### <a name="no-response-during-gulp-tasks"></a>gulp タスクの最中に応答がない
gulp タスクの実行中に問題が発生した場合には、デバッグのために `--verbose` オプションを追加します。 Ctrl キーを押しながら C キーを押して現在の gulp タスクを終了したうえで、コンソール ウィンドウで以下のコマンドを実行し、デバッグ メッセージを確認してください。 コンソールの出力として詳しいエラー メッセージが表示されることがあります。

```bash
gulp --verbose
```

### <a name="device-discovery-issues"></a>デバイスの検出の問題
`devdisco` コマンドに関してよくある問題のトラブルシューティングについては、[readme](https://github.com/Azure/device-discovery-cli/blob/develop/readme.md) を参照してください。

### <a name="npm-issues"></a>npm の問題
以下のコマンドを使用して npm パッケージを更新してみてください。

```bash
npm install -g npm
```

問題が解決しなかった場合には、この記事の末尾にコメントを残すか、Microsoft の[サンプル リポジトリ](https://github.com/Azure-Samples/iot-hub-node-raspberrypi-getting-started)で GitHub の問題を作成してください。

## <a name="remote-debugging"></a>リモート デバッグ
### <a name="run-the-sample-application-in-debug-mode"></a>デバッグ モードでサンプル アプリケーションを実行する
```bash
gulp run --debug
```

デバッグ エンジンの準備ができたら、コンソールの出力に "```Debugger listening on port 5858```" と表示されます。

### <a name="configure-visual-studio-code-to-connect-to-the-remote-device"></a>Visual Studio Code を構成してリモート デバイスに接続する
1. 左側の **[デバッグ]** パネルを開きます。
2. 緑色の **[デバッグ開始]** (F5) ボタンをクリックします。 Visual Studio Code で launch.json ファイルが開かれます。
3. launch.json ファイルを以下の内容で更新します。 `[device hostname or IP address]` を実際のデバイスの IP アドレスまたはホスト名で置き換えます。

> [!NOTE]
> Visual Studio のデバッグの詳細については、[Visual Studio Code でのデバッグ](https://code.visualstudio.com/Docs/editor/debugging#_launchjson-attributes)に関する記事を参照してください。


```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Attach",
            "type": "node",
            "request": "attach",
            "port": 5858,
            "address": "[device hostname or IP address]",
            "restart": false,
            "sourceMaps": false,
            "outDir": null,
            "localRoot": "${workspaceRoot}",
            "remoteRoot": null
        }
    ]
}
```

![リモート デバッグの構成](media/iot-hub-raspberry-pi-lessons/troubleshooting/remote_debugging_configuration.png)

### <a name="attach-to-the-remote-application"></a>リモート アプリケーションにアタッチする
緑色の **[デバッグ開始]** (F5) ボタンをクリックして、デバッグを開始します。

デバッガーの詳細については、「[JavaScript in VS Code (VS Code での JavaScript の使用)](https://code.visualstudio.com/docs/languages/javascript#_debugging)」を参照してください。

![対話式リモート デバッグ](media/iot-hub-raspberry-pi-lessons/troubleshooting/remote_debugging_interactive.png)

## <a name="azure-cli-issues"></a>Azure CLI の問題
Azure コマンド ライン インターフェイス (Azure CLI) は、プレビュー ビルドです。 問題の解決方法をお探しの際は、「[Preview Install Guide (プレビュー インストール ガイド)](https://github.com/Azure/azure-cli/blob/master/doc/preview_install_guide.md)」を参照してください。

ツールにバグを発見した場合には、GitHub リポジトリの **[Issues (問題)]** セクションで[問題](https://github.com/Azure/azure-cli/issues)を報告してください。

よくある問題のトラブルシューティングについては、[readme](https://github.com/Azure/azure-cli/blob/master/README.rst) を参照してください。

## <a name="python-installation-issues"></a>Python のインストールに関する問題
### <a name="legacy-installation-issues-macos"></a>レガシのインストールに関する問題 (macOS)
以前のパッケージが **su** アクセス許可の下でインストールされている場合、pip をインストールするときにアクセス許可のエラーがスローされます。 このような状況は、以前に brew (macOS) を使ってインストールした Python が完全にアンインストールできていないために発生します。 以前のインストールの pip パッケージは一部がルートによって作成されており、これがアクセス許可のエラーを引き起こします。 このエラーを解決する方法は、ルートによってインストールされたパッケージを削除することです。 そのためには、以下の手順を実行してください。

1. /usr/local/lib/python2.7/site-packages に移動します。
2. ルートによって作成されたパッケージを表示します: `ls -l | grep root`
3. 手順 2. の結果として表示されたパッケージをアンインストールします: `sudo rm -rf {package name}`
4. Python を再インストールします。

## <a name="azure-iot-hub-issues"></a>Azure IoT Hub の問題
Azure CLI で Azure IoT ハブを正常にプロビジョニングでき、IoT ハブに接続するデバイスを管理するためのツールが必要な場合には、以下をお試しください。

### <a name="device-explorer"></a>デバイス エクスプローラー
[デバイス エクスプローラー](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer) ツールは、Windows ローカル コンピューターで実行され、Azure の IoT Hub に接続するツールです。 このツールでは、以下の [IoT Hub エンドポイント](iot-hub-devguide.md) と通信します。


* "*デバイス ID 管理*": IoT Hub に登録されているデバイスのプロビジョニングと管理ができます。
* "*デバイスからクラウドへのメッセージ受信*": デバイスから IoT ハブに送信されるメッセージを監視できます。
* "*クラウドからデバイスへのメッセージ送信*" IoT ハブからデバイスにメッセージを送信できます。

このツールで IoT ハブ接続文字列を構成すると、その機能を余すところなく使用できます。

### <a name="iothub-explorer"></a>iothub-explorer
[iothub-explorer](https://github.com/Azure/iothub-explorer) は、デバイスを管理するためのサンプル マルチプラットフォーム CLI ツールです。 このツールを使うと、ID レジストリにあるデバイスの管理、デバイスからクラウドへのメッセージの監視、クラウドからデバイスへのメッセージの送信が可能になります。

iothub-explorer ツールの最新版 (プレリリース版) をインストールするには、コマンド ライン環境で以下のコマンドを実行します。

```bash
npm install -g iothub-explorer@latest
```

以下のコマンドでは、iothub-explorer のコマンドとそのパラメーターすべてについて、詳しいヘルプを取得できます。

```bash
iothub-explorer help
```

### <a name="azure-portal"></a>Azure Portal
完全な CLI エクスペリエンスでは、すべての Azure リソースを作成および管理できます。 Azure リソースのプロビジョニング、管理、およびデバッグにはほかにも、[Azure Portal](../azure-portal-overview.md) を使用できます。

## <a name="azure-storage-issues"></a>Azure Storage の問題
[Microsoft Azure Storage エクスプローラー (プレビュー)](http://storageexplorer.com) は、Windows、macOS、Linux で Azure Storage のデータを操作できる Microsoft 製の無料のスタンドアロン アプリです。 このツールでは、テーブルに接続し、そこに格納されているデータを表示できます。 このツールを使えば、Azure Storage の問題をトラブルシューティングできます。


