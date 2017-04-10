---
title: "SensorTag デバイスと Azure IoT Gateway - レッスン 2: ツールの取得 (macOS) | Microsoft Docs"
description: "Mac コンピューターにツールをインストールし、IoT ハブを作成し、IoT ハブにデバイスを登録します。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "IoT 開発, IoT ソフトウェア, IoT クラウド サービス, モノのインターネット ソフトウェア, Azure CLI, Mac での Python のインストール, Mac での Git のインストール, gulp の実行, Mac での Node.js のインストール"
ms.assetid: 94e538ef-9857-4023-9c3c-e92a0be7c395
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: 8b105cebd5b3e6c5cae726abbf5f2c48c0cd7587
ms.lasthandoff: 01/25/2017


---
# <a name="get-the-tools-macos"></a>ツールの入手 (MacOS)
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

- [Git](https://git-scm.com/) と [Node.js](https://nodejs.org/en/)をインストールする方法。
  - Git は、オープン ソースの分散型バージョン管理システムです。 このレッスンのサンプル アプリケーションは、Git に格納されています。
  - Node.js は、豊富なパッケージ エコシステムが存在する JavaScript ランタイムです。
- [NPM](https://www.npmjs.com/) を使用して Node.js 開発ツールをインストールする方法。
  - Node.js の最低限必要なバージョンは 4.5 LTS です。
  - NPM は、Node.js 向けのパッケージ マネージャーの&1; つです。
- Visual Studio Code をインストールする方法。
  - Visual Studio Code は、Windows、Linux、および macOS 向けのクロスプラットフォームの軽量で強力なソース コード エディターです。 デバッグ、Git コントロールの埋め込み、構文の強調表示、インテリジェントなコードの完了、スニペット、およびコードのリファクタリングを広くサポートしています。
- Python のインストール方法。
  - Python は、広く使用されている高度な汎用のインタープリター型の動的プログラミング言語です。
- Azure CLI をインストールする方法。
  - Azure 用のマルチプラットフォーム コマンド ライン機能を提供する Azure CLI をインストールすると、 リソースのプロビジョニングと管理をコマンド ラインで直接行えます。
- Azure CLI を使用して IoT ハブを作成する方法。

## <a name="what-you-need"></a>必要なもの

- インターネット接続 (ツールとソフトウェアをダウンロードするため)。
- OS X Yosemite (10.10) 以降を実行している Mac コンピューター。

## <a name="install-git-and-nodejs"></a>Git と Node.js のインストール

Git と Node.js をインストールするには、次の手順で Homebrew パッケージ管理ユーティリティを使用します。

1. Homebrew を[ダウンロード](http://brew.sh/)してインストールします。 既に Homebrew のインストールが済んでいる場合は手順 2 に進んでください。
   1. `Cmd + Space` キーを押し、「`Terminal`」と入力して、ターミナルを開きます。
   2. 次のコマンドを実行します。

      ```bash
      /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
      ```

2. 次のコマンドを実行して Git と Node.js をインストールします。

    ```bash
    brew install node git
    ```

## <a name="install-nodejs-development-tools"></a>Node.js 開発ツールのインストール

[gulp.js](http://gulpjs.com/) を使用して、スクリプトのデプロイと実行を自動化します。

gulp をインストールするには、ターミナルで次のコマンドを実行します。

```bash
npm install -g gulp
```

インストールで問題が発生した場合は、[トラブルシューティング ガイド](iot-hub-gateway-kit-c-troubleshooting.md)で一般的な問題に対する解決策を確認してください。

> [!Note]
> Node.js で開発された自動化スクリプトを実行するには、Node、NPM、および Gulp が必要です。

## <a name="install-python"></a>Python のインストール

Mac OS X には Python 2.7 が同梱されていますが、Homebrew で Python をインストールすることをお勧めします。 「[Installing Python on Mac OS X](http://docs.python-guide.org/en/latest/starting/install/osx/)」(Mac OS X への Python のインストール) を参照してください。

次のコマンドを実行して Python と pip をインストールします。

```bash
brew install python
```

## <a name="install-the-azure-cli"></a>Azure CLI のインストール

Azure CLI をインストールするには、次の手順を実行します。

1. ターミナルで次のコマンドを実行します。
   ```bash
   pip install --upgrade azure-cli
   pip install --upgrade azure-cli-iot
   ```
   インストールは、5 分ほどかかる可能性があります。

2. 次のコマンドを実行してインストールを確認します。
   ```bash
   az iot -h
   ```
   インストールが成功した場合、次の出力が表示されます。

   ![Azure CLI のインストールの確認](media/iot-hub-gateway-kit-lessons/lesson2/az_iot_help_osx.png)

## <a name="install-visual-studio-code"></a>Visual Studio Code のインストール

チュートリアルの後半で、Visual Studio Code を使用して構成ファイルを編集します。

Visual Studio Code を[ダウンロード](https://code.visualstudio.com/docs/setup/osx)して、インストールします。

## <a name="summary"></a>まとめ

すべての必要なツールとソフトウェアを Mac コンピューターにインストールしました。 次のタスクは、Azure CLI を使用して IoT ハブを作成し、IoT ハブにデバイスを登録することです。

## <a name="next-steps"></a>次のステップ
[IoT ハブの作成とデバイスの登録](iot-hub-gateway-kit-c-lesson2-register-device.md)

