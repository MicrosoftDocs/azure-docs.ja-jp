---
title: "SensorTag デバイスと Azure IoT Gateway - レッスン 2: ツールの取得 (Ubuntu) | Microsoft Docs"
description: "Ubuntu を実行中のホスト コンピューターにツールとソフトウェアをインストールし、IoT ハブを作成し、IoT ハブにデバイスを登録します。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "IoT 開発, IoT ソフトウェア, IoT クラウド サービス, モノのインターネット ソフトウェア, Azure CLI, ubuntu での Git のインストール, gulp の実行, ubuntu での Node.js のインストール"
ms.assetid: 0bac1412-385b-4255-a33f-9d44c35feb3e
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: 6414a4f37367d8acc0bab9356b8a09aadf13bfc9
ms.lasthandoff: 01/25/2017


---
# <a name="get-the-tools-ubuntu-1604"></a>ツールを入手する (Ubuntu 16.04)
> [!div class="op_single_selector"]
> * [Windows 7 以降](iot-hub-gateway-kit-c-lesson2-get-the-tools-win32.md)
> * [Ubuntu 16.04](iot-hub-gateway-kit-c-lesson2-get-the-tools-ubuntu.md)
> * [macOS 10.10](iot-hub-gateway-kit-c-lesson2-get-the-tools-mac.md)

## <a name="what-you-will-do"></a>学習内容

- Git、Node.js、Gulp、Python をインストールします。
- Azure コマンド ライン インターフェイス (Azure CLI) をインストールします。 

問題が発生した場合は、[トラブルシューティングのページ](iot-hub-gateway-kit-c-troubleshooting.md)で解決策を探してください。
## <a name="what-you-will-learn"></a>学習内容

このレッスンでは、次の内容を学習します。

- Git と Node.js をインストールする方法。
  - Git は、オープン ソースの分散型バージョン管理システムです。 このレッスンのサンプル アプリケーションは、Git に格納されています。
  - Node.js は、豊富なパッケージ エコシステムが存在する JavaScript ランタイムです。
- NPM を使用して Node.js 開発ツールをインストールする方法。
  - Node.js の最低限必要なバージョンは 4.5 LTS です。
  - NPM は、Node.js 向けのパッケージ マネージャーの&1; つです。
- Visual Studio Code をインストールする方法。
  - Visual Studio Code は、Windows、Linux、および macOS 向けのクロスプラットフォームの軽量で強力なソース コード エディターです。 デバッグ、Git コントロールの埋め込み、構文の強調表示、インテリジェントなコードの完了、スニペット、およびコードのリファクタリングを広くサポートしています。
- 方法: Azure CLI をインストールする
  - Azure 用のマルチプラットフォーム コマンド ライン機能を提供する Azure CLI をインストールすると、 リソースのプロビジョニングと管理をコマンド ラインで直接行えます。
- Azure CLI を使用して IoT ハブを作成する方法。

## <a name="what-you-need"></a>必要なもの

- インターネット接続 (ツールとソフトウェアをダウンロードするため)。
- Ubuntu 16.04 以降を実行しているコンピューター。

## <a name="install-git-and-nodejs"></a>Git と Node.js のインストール

Git と Node.js をインストールするには、次の手順を実行します。

1. `Ctrl + Alt + T` を押してターミナルを開きます。
2. 次のコマンドを実行します。

   ```bash
   sudo apt-get update
   curl -sL https://deb.nodesource.com/setup_6.x | sudo -E bash -
   sudo apt-get install -y nodejs
   sudo apt-get install git
   ```

## <a name="install-nodejs-development-tools"></a>Node.js 開発ツールのインストール

[gulp.js](http://gulpjs.com/) を使用して、スクリプトのデプロイと実行を自動化します。

gulp をインストールするには、ターミナルで次のコマンドを実行します。

```bash
sudo npm install -g gulp
```

インストールで問題が発生した場合は、[トラブルシューティング ガイド](iot-hub-gateway-kit-c-troubleshooting.md)で一般的な問題に対する解決策を確認してください。

> [!Note]
> Node.js で開発された自動化スクリプトを実行するには、Node、NPM、および Gulp が必要です。

## <a name="install-the-azure-cli"></a>Azure CLI のインストール

Azure CLI をインストールするには、次の手順を実行します。

1. ターミナルで次のコマンドを実行します。

   ```bash
   sudo apt-get update
   sudo apt-get install -y libssl-dev libffi-dev
   sudo apt-get install -y python-dev
   sudo apt-get install -y build-essential
   sudo apt-get install -y python-pip
   sudo pip install --upgrade azure-cli
   sudo pip install --upgrade azure-cli-iot
   ```

   インストールは、5 分ほどかかる可能性があります。

2. 次のコマンドを実行してインストールを確認します。

   ```bash
   az iot -h
   ```
インストールが成功した場合、次の出力が表示されます。
![Azure CLI のインストールの確認](media/iot-hub-gateway-kit-lessons/lesson2/az_iot_help_ubuntu.png)

### <a name="install-visual-studio-code"></a>Visual Studio Code のインストール

チュートリアルの後半で、Visual Studio Code を使用して構成ファイルを編集します。

Visual Studio Code を[ダウンロード](https://code.visualstudio.com/docs/setup/linux)して、インストールします。

## <a name="summary"></a>概要

すべての必要なツールとソフトウェアをホスト コンピューターにインストールしました。 次のタスクは、Azure CLI を使用して IoT ハブを作成し、IoT ハブにデバイスを登録することです。

## <a name="next-steps"></a>次のステップ
[IoT ハブを作成してデバイスを登録する](iot-hub-gateway-kit-c-lesson2-register-device.md)

