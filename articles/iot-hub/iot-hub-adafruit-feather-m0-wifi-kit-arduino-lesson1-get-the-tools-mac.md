---
title: "Azure IoT への Arduino の接続 - レッスン 1: ツールの入手 (macOS) | Microsoft Docs"
description: "Mac OS で、Adafruit Feather M0 WiFi の最初のサンプル アプリケーションに必要なツールとソフトウェアをダウンロードしてインストールします。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "Arduino 開発ツール, IoT 開発, IoT ソフトウェア, モノのインターネット ソフトウェア, Mac での Git のインストール, Mac での Node.js のインストール"
ms.assetid: 0262f3dd-0259-4eb0-962f-9fb534f8359e
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 41b2277a26f0e99052a699cfab8a8129a6c7ddb1
ms.lasthandoff: 01/24/2017


---
# <a name="get-the-tools-macos-1010"></a>ツールを入手する (macOS 10.10)
> [!div class="op_single_selector"]
> * [Windows 7 以降][windows]
> * [Ubuntu 16.04][ubuntu]
> * [macOS 10.10][macos]

## <a name="what-you-will-do"></a>学習内容

Adafruit Feather M0 WiFi Arduino ボードの最初のサンプル アプリケーション用の開発ツールとソフトウェアをダウンロードします。 

問題が発生した場合は、[トラブルシューティングのページ][troubleshooting]で解決策を探してください。

> [!NOTE]
> メイン ロジックのプログラミング言語は Arduino ですが、レッスンでは、サンプル アプリケーションのビルドとデプロイに Node.js ツールを使用します。

## <a name="what-you-will-learn"></a>学習内容
この記事では、次のことについて説明します。

* Git と Node.js をインストールする方法。
  * [Git](https://git-scm.com) は、オープン ソースの分散型バージョン管理システムです。 この記事のサンプル アプリケーションは、Git に格納されています。
  * [Node.js](https://nodejs.org/en/) は、豊富なパッケージ エコシステムが存在する JavaScript ランタイムです。
* NPM を使って、追加の Node.js 開発ツールをインストールする方法。
  * Node.js の最低限必要なバージョンは 4.5 LTS です。
  * [NPM](https://www.npmjs.com) は、Node.js のパッケージ マネージャーの&1; つです。

## <a name="what-you-need"></a>必要なもの
この操作を完了するには、以下が必要です。
* インターネット接続 (開発ツールとソフトウェアをダウンロードするため)。
* macOS Yosemite (10.10) 以降を実行している Mac。

## <a name="install-git-and-nodejs"></a>Git と Node.js のインストール
Git と Node.js をインストールするには、次の手順で [Homebrew](http://brew.sh) パッケージ管理ユーティリティを使います。

1. Homebrew をインストールします。 既に Homebrew のインストールが済んでいる場合、手順 2. に進んでください。

   1. `Cmd + Space` キーを押し、「`Terminal`」と入力して、ターミナルを開きます。
   2. 次のコマンドを実行します。

      ```bash
      /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
      ```
2. 次のコマンドを実行して Git と Node.js をインストールします。

   ```bash
   brew install node git
   ```

## <a name="install-additional-nodejs-development-tools"></a>追加の Node.js 開発ツールのインストール
[gulp.js](http://gulpjs.com) を使って、Arduino ボードへのサンプル アプリケーションのデプロイを自動化します。

ターミナルで次のコマンドを実行して、`gulp` と `device-discovery-cli` をインストールします。

```bash
sudo npm install -g gulp device-discovery-cli
```

Mac OS で Node.js やこれらの追加の開発ツールをインストールする際に問題が発生した場合、一般的な問題の解決策については、[トラブルシューティング ガイド][troubleshooting]をご覧ください。

## <a name="install-visual-studio-code"></a>Visual Studio Code のインストール
Visual Studio Code を[ダウンロード](https://code.visualstudio.com/docs/setup/osx)して、インストールします。 Visual Studio Code は、Windows、Linux、macOS 向けの軽量で強力なソース コード エディターです。 チュートリアルの後半で、このエディターを使ってサンプル コードを編集します。

## <a name="summary"></a>概要
最初のサンプル アプリケーションに必要な開発ツールとソフトウェアをインストールしました。 次のタスクでは、サンプル アプリケーションを作成し、Arduino ボードにデプロイして、実行します。

## <a name="next-steps"></a>次のステップ
[点滅アプリケーションを作成してデプロイする][create-and-deploy-the-blink-application]
<!-- Images and links -->

[windows]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-get-the-tools-win32.md
[ubuntu]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-get-the-tools-ubuntu.md
[macos]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-get-the-tools-mac.md
[troubleshooting]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md
[create-and-deploy-the-blink-application]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-deploy-blink-app.md
