---
title: "SensorTag デバイスと Azure IoT Gateway - トラブルシューティング | Microsoft Docs"
description: "Intel NUC ゲートウェイのトラブルシューティングのページ"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "IoT の問題, モノのインターネットの問題"
ms.assetid: 5f742c38-0e33-465a-9a0d-1e41e8d17187
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: f24a620a18d7756b24f8f5f63d8b31fca63ade25
ms.lasthandoff: 01/25/2017


---
# <a name="troubleshooting"></a>トラブルシューティング

## <a name="hardware-issues"></a>ハードウェアの問題

### <a name="ti-sensortag-cannot-be-connected"></a>TI SensorTag を接続できない

SensorTag の接続問題をトラブルシューティングするには、[SensorTag アプリ](http://processors.wiki.ti.com/index.php/SensorTag_User_Guide#SensorTag_App_user_guide)を使用します。

### <a name="have-an-issue-with-intel-nuc"></a>Intel NUC に問題がある

起動の問題をトラブルシューティングするには、「[Troubleshooting No Boot Issues on Intel NUC](http://www.intel.com/content/www/us/en/support/boards-and-kits/000005845.html)」 (Intel NUC が起動しない問題のトラブルシューティング) を参照してください。

オペレーティング システムの問題をトラブルシューティングするには、「[roubleshooting Operating System Issues on Intel NUC](http://www.intel.com/content/www/us/en/support/boards-and-kits/000006018.html)」 (Intel NUC のオペレーティング システム問題のトラブルシューティング) を参照してください。

その他の問題をトラブルシューティングするには、「[Blink Codes and Beep Codes for Intel NUC](http://www.intel.com/content/www/us/en/support/boards-and-kits/intel-nuc-boards/000005854.html)」(Intel NUC の点滅コードとビープ コード) を参照してください。

## <a name="nodejs-package-issues"></a>Node.js パッケージの問題

### <a name="no-response-during-gulp-tasks"></a>gulp タスクの最中に応答がない

gulp タスクの実行中に問題が発生した場合には、デバッグのために `--verbose` オプションを追加します。 `Ctrl + C` を使用して現在の gulp タスクを終了したうえで、コンソール ウィンドウで以下のコマンドを実行し、デバッグ メッセージを確認してください。 コンソールの出力として詳しいエラー メッセージが表示されることがあります。

```bash
gulp --verbose
```

### <a name="device-discovery-issues"></a>デバイスの検出の問題

`discover-sensortag` コマンドに関する一般的な問題のトラブルシューティングについては、[Wiki ページ](https://wiki.archlinux.org/index.php/bluetooth#Bluetoothctl)を参照してください。

### <a name="npm-issues"></a>npm の問題

次のコマンドを実行して npm パッケージを更新してみてください。

```bash
npm install -g npm
```

問題が解決しなかった場合には、この記事の末尾にコメントを残すか、Microsoft の[サンプル リポジトリ](https://github.com/azure-samples/iot-hub-c-intel-nuc-gateway-getting-started)で GitHub の問題を作成してください。

## <a name="remote-debugging"></a>リモート デバッグ
> 以下の説明は、このチュートリアルで使用される node.js スクリプトをデバッグするためのものです。
### <a name="run-the-sample-application-in-debug-mode"></a>デバッグ モードでサンプル アプリケーションを実行する

次のコマンドを実行して、サンプル アプリケーションをデバッグ モードで実行します。

```bash
gulp run --debug
```

デバッグ エンジンの準備ができたら、コンソールの出力に "`Debugger listening on port 5858`" と表示されます。

### <a name="configure-visual-studio-code-to-connect-to-the-remote-device"></a>Visual Studio Code を構成してリモート デバイスに接続する

1. 左側の **[デバッグ]** パネルを開きます。
2. 緑色の **[デバッグ開始]** (F5) ボタンをクリックします。 Visual Studio Code で `launch.json` ファイルが開きます。
3. `launch.json` ファイルを次の内容に更新します。 `[device hostname or IP address]` を実際のデバイスの IP アドレスまたはホスト名で置き換えます。

   ``` json
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
            "remoteRoot": "~/ble_sample"
        }
     ]
   }
   ```

![リモート デバッグの構成](./media/iot-hub-gateway-kit-lessons/troubleshooting/remote_debugging_configuration.png)

### <a name="attach-to-the-remote-application"></a>リモート アプリケーションにアタッチする

緑色の **[デバッグ開始]** (F5) ボタンをクリックして、デバッグを開始します。

デバッガーの詳細については、「[JavaScript in VS Code (VS Code での JavaScript の使用)](https://code.visualstudio.com/docs/languages/javascript#_debugging)」を参照してください。

![BLE サンプルのデバッグ](./media/iot-hub-gateway-kit-lessons/troubleshooting/debugging_ble_sample.png)

## <a name="azure-cli-issues"></a>Azure CLI の問題

Azure コマンド ライン インターフェイス (Azure CLI) は、プレビュー ビルドです。 問題の解決方法をお探しの際は、「[Preview Install Guide (プレビュー インストール ガイド)](https://github.com/Azure/azure-cli/blob/master/doc/preview_install_guide.md)」を参照してください。

ツールにバグを発見した場合には、GitHub リポジトリの **[Issues (問題)]** セクションで[問題](https://github.com/Azure/azure-cli/issues)を報告してください。

よくある問題のトラブルシューティングについては、[readme](https://github.com/Azure/azure-cli/blob/master/README.rst) を参照してください。

"要件を満たすバージョンが見つかりません" と表示された場合は、次のコマンドを実行して pip を最新バージョンにアップグレードしてください。

```bash
python -m pip install --upgrade pip
```

## <a name="python-installation-issues"></a>Python のインストールに関する問題

### <a name="legacy-installation-issues-macos"></a>レガシのインストールに関する問題 (macOS)

以前のパッケージが **su** アクセス許可の下でインストールされている場合、pip をインストールするときにアクセス許可のエラーがスローされます。 このような状況は、以前に brew (macOS) を使ってインストールした Python が完全にアンインストールできていないために発生します。 以前のインストールの pip パッケージは一部がルートによって作成されており、これがアクセス許可のエラーを引き起こします。 このエラーを解決する方法は、ルートによってインストールされたパッケージを削除することです。 そのためには、以下の手順を実行してください。

1. `/usr/local/lib/python2.7/site-packages` に移動します
2. ルートによって作成されたパッケージを表示します: `ls -l | grep root`
3. 手順 2. の結果として表示されたパッケージをアンインストールします: `sudo rm -rf {package name}`
4. Python を再インストールします。

## <a name="azure-iot-hub-issues"></a>Azure IoT Hub の問題

Azure CLI で Azure IoT ハブを正常にプロビジョニングしているときに、IoT ハブに接続するデバイスを管理するためのツールが必要な場合には、以下をお試しください。

### <a name="device-explorer"></a>デバイス エクスプローラー

[デバイス エクスプローラー](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer)は、Windows ローカル コンピューターで実行され、Azure の IoT Hub に接続するツールです。 このツールでは、以下の [IoT Hub エンドポイント](https://azure.microsoft.com/en-us/documentation/articles/iot-hub-devguide/) と通信します。

- デバイス ID 管理: IoT Hub に登録されているデバイスのプロビジョニングと管理を実行できます。
- デバイスからクラウドへのメッセージ受信: デバイスから IoT ハブに送信されるメッセージを監視できます。
- クラウドからデバイスへのメッセージ送信: IoT ハブからデバイスにメッセージを送信できます。

このツールで IoT ハブ接続文字列を構成すると、その機能を余すところなく使用できます。

### <a name="iothub-explorer"></a>iothub-explorer

[iothub-explorer](https://github.com/Azure/iothub-explorer) は、デバイス クライアントを管理するためのサンプル マルチプラットフォーム CLI ツールです。 このツールを使うと、ID レジストリにあるデバイスの管理、デバイスからクラウドへのメッセージの監視、クラウドからデバイスへのコマンドの送信が可能になります。

iothub-explorer ツールの最新版 (プレリリース版) をインストールするには、次のコマンドを実行します。

```bash
npm install -g iothub-explorer@latest
```

iothub-explorer のすべてのコマンドとそのパラメーターの詳しいヘルプを取得するには、次のコマンドを実行します。

```bash
iothub-explorer help
```

### <a name="the-azure-portal"></a>Azure ポータル

完全な CLI エクスペリエンスでは、すべての Azure リソースを作成および管理できます。 Azure リソースのプロビジョニング、管理、およびデバッグにはほかにも、[Azure Portal](https://azure.microsoft.com/en-us/documentation/articles/azure-portal-overview/) を使用できます。

## <a name="azure-storage-issues"></a>Azure Storage の問題

[Microsoft Azure Storage エクスプローラー (プレビュー)](http://storageexplorer.com/) は、Windows、macOS、Linux で Azure Storage のデータを操作できる Microsoft 製の無料のスタンドアロン アプリです。 このツールでは、テーブルに接続し、そこに格納されているデータを表示できます。 このツールを使えば、Azure Storage の問題をトラブルシューティングできます。

