---
title: "Azure IoT への Intel Edison (Node) の接続 - レッスン 4: トラブルシューティング | Microsoft Docs"
description: "Intel Edison の Node.js エクスペリエンスに関するトラブルシューティング ページ"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "Arduino トラブルシューティング"
ms.assetid: f11c5521-8a36-44c0-baad-f5ec26ab4618
ms.service: iot-hub
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: adf5b10721a28432e6b37ef73c6a7e7ec9f93cdd
ms.openlocfilehash: d77ee0be35a3f4b38c50cf9322124e4dfbba6ddc
ms.lasthandoff: 01/25/2017


---
# <a name="troubleshooting"></a>トラブルシューティング
## <a name="hardware-issues"></a>ハードウェアの問題
Intel Edison に関してよくある問題の解決方法については、[公式のトラブルシューティング ページ](https://software.intel.com/en-us/node/637974)を参照してください。

## <a name="nodejs-package-issues"></a>Node.js パッケージの問題
### <a name="no-response-during-gulp-tasks"></a>gulp タスクの最中に応答がない
gulp タスクの実行中に問題が発生した場合には、デバッグのために `--verbose` オプションを追加します。 `Ctrl + C` を使用して現在の gulp タスクを終了したうえで、コンソール ウィンドウで以下のコマンドを実行し、デバッグ メッセージを確認してください。 コンソールの出力として詳しいエラー メッセージが表示されることがあります。 

```bash
gulp --verbose
```

### <a name="npm-issues"></a>NPM の問題
以下のコマンドで NPM パッケージを更新してみてください。

```bash
npm install -g npm
```

問題が解決しなかった場合には、この記事の末尾にコメントを残すか、Microsoft の[サンプル リポジトリ][sample-repository]で GitHub の問題を作成してください。

## <a name="remote-debugging"></a>リモート デバッグ

### <a name="run-the-sample-application-in-debug-mode"></a>デバッグ モードでサンプル アプリケーションを実行する

```bash
gulp run --debug
```

デバッグ エンジンの準備ができたら、コンソールの出力に "```Debugger listening on port 5858```" と表示されます。

### <a name="configure-vs-code-to-connect-to-the-remote-device"></a>VS Code を構成してリモート デバイスに接続する

左側の **[デバッグ]** パネルを開きます。

緑色の **[デバッグ開始]** (F5) ボタンをクリックします。 VS Code により、**launch.json** ファイルが開きます。このファイルには、更新が必要です。

**launch.json** ファイルを以下の内容で更新して、`[device hostname or IP address]` を実際のデバイスの IP アドレスかホスト名で置き換えます。  

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

![リモート デバッグの構成](media/iot-hub-intel-edison-lessons/troubleshooting/remote_debugging_configuration.png)

### <a name="attach-to-the-remote-application"></a>リモート アプリケーションにアタッチする

緑色の **[デバッグ開始]** (F5) ボタンをクリックして、デバッグを実行します。

デバッガーの詳細については、「[JavaScript in VS Code (VS Code での JavaScript の使用)](https://code.visualstudio.com/docs/languages/javascript#_debugging)」をご覧ください。

![対話式リモート デバッグ](media/iot-hub-intel-edison-lessons/troubleshooting/remote_debugging_interactive.png)

## <a name="azure-cli-issues"></a>Azure CLI の問題
Azure コマンド ライン インターフェイス (Azure CLI) は、プレビュー ビルドです。 解決方法をお探しの際は、[プレビュー インストール ガイド](https://github.com/Azure/azure-cli/blob/master/doc/preview_install_guide.md)に関するページを参照してください。 コマンドが正常に動作しない場合は、Azure CLI を最新バージョンにアップグレードしてみてください。

ツールにバグを発見した場合には、GitHub リポジトリの **[Issues (問題)]** セクションで[問題](https://github.com/Azure/azure-cli/issues)を報告してください。

よくある問題のトラブルシューティングについては、[readme](https://github.com/Azure/azure-cli/blob/master/README.rst) を参照してください。

"Could not find a version that satisfies the requirement (要件を満たすバージョンが見つかりません)" と表示された場合は、次のコマンドを実行して pip を最新バージョンにアップグレードしてください。

```bash
python -m pip install --upgrade pip
```

## <a name="python-installation-issues"></a>Python のインストールに関する問題
### <a name="legacy-installation-issues-macos"></a>レガシのインストールに関する問題 (macOS)
**pip** をインストールする際に、以前のパッケージが **su** アクセス許可の下でインストールされている場合には、アクセス許可のエラーがスローされます。 このような状況は、以前に brew (macOS) を使ってインストールした Python が完全にアンインストールできていないために発生します。 以前のインストールの **pip** パッケージは一部がルートによって作成されており、これがアクセス許可のエラーを引き起こします。 このエラーを解決する方法は、ルートによってインストールされたパッケージを削除することです。 そのためには、以下の手順を実行してください。

1. /usr/local/lib/python2.7/site-packages に移動します。
2. ルートによって作成されたパッケージを表示します: `ls -l | grep root`
3. 手順 2. の結果として表示されたパッケージをアンインストールします: `sudo rm -rf {package name}`
4. Python を再インストールします。

## <a name="azure-iot-hub-issues"></a>Azure IoT Hub の問題
`azure-cli` で Azure IoT ハブを正常にプロビジョニングでき、IoT ハブに接続するデバイスを管理するためのツールが必要な場合には、以下をお試しください。

### <a name="device-explorer"></a>デバイス エクスプローラー
[デバイス エクスプローラー](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer)は、Windows ローカル コンピューターで実行され、Azure の IoT Hub に接続するツールです。 このツールでは、以下の [IoT Hub エンドポイント](iot-hub-devguide.md) と通信します。

- "_デバイス ID 管理_": IoT Hub に登録されているデバイスのプロビジョニングと管理ができます。
- "_デバイスからクラウドへのメッセージ受信_": デバイスから IoT ハブに送信されるメッセージを監視できます。
- "_クラウドからデバイスへのメッセージ送信_" IoT ハブからデバイスにメッセージを送信できます。

このツールで `IoT hub connection string` を構成すると、その機能を余すところなく使用できます。

### <a name="iot-hub-explorer"></a>IoT Hub エクスプローラー
[IoT Hub エクスプローラー](https://github.com/Azure/iothub-explorer) は、デバイス クライアントを管理するためのサンプル マルチプラットフォーム CLI ツールです。 このツールを使うと、ID レジストリにあるデバイスの管理、デバイスからクラウドへのメッセージの監視、クラウドからデバイスへのコマンドの送信が可能になります。

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
[Microsoft Azure Storage エクスプローラー (プレビュー)](http://storageexplorer.com) は、Windows、macOS、Linux で [Azure Storage](https://azure.microsoft.com/en-us/services/storage/) のデータ操作に使用できる Microsoft 製の無料のスタンドアロン アプリです。 このツールでは、テーブルに接続し、そこに格納されているデータを表示できます。 このツールを使えば、Azure Storage の問題をトラブルシューティングできます。

## <a name="next-steps"></a>次のステップ
このページに記載されているのは、Intel Edison キットでよくある問題のみです。 さらなるトラブルシューティングのために、下部にコメントを残して問題を報告することもできます。

「[Intel Edison (Node.js) を使ってみる](iot-hub-intel-edison-kit-node-get-started.md)」に戻る

<!-- Images and links -->

[sample-repository]: https://github.com/Azure-Samples/iot-hub-node-edison-getting-started
