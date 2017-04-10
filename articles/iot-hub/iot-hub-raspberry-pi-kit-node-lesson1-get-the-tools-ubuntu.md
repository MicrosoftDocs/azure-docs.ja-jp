---
title: "Azure IoT への Raspberry Pi (Node) の接続 - レッスン 1: ツールの入手 (Ubuntu) | Microsoft Docs"
description: "Ubuntu で、Pi の最初のサンプル アプリケーションに必要なツールとソフトウェアをダウンロードしてインストールします。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "IoT 開発, IoT ソフトウェア, モノのインターネット ソフトウェア, Ubuntu での Git のインストール, gulp の実行, Ubuntu での Node.js のインストール"
ms.assetid: 4d5e45c0-1db9-4662-a039-99ba26333085
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: a43690c4fd35a701472b9a1c747e969d1d3a2ea4
ms.lasthandoff: 01/24/2017


---
# <a name="get-the-tools-ubuntu-1604"></a>ツールを入手する (Ubuntu 16.04)

> [!div class="op_single_selector"]
> * [Windows 7 以降](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-win32.md)
> * [Ubuntu 16.04](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-ubuntu.md)
> * [macOS 10.10](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-mac.md)


## <a name="what-you-will-do"></a>学習内容
Raspberry Pi 3 の最初のサンプル アプリケーション用の開発ツールとソフトウェアをダウンロードします。 問題が発生した場合は、[トラブルシューティングのページ](iot-hub-raspberry-pi-kit-node-troubleshooting.md)で解決策を探してください。

## <a name="what-you-will-learn"></a>学習内容
この記事では、次のことについて説明します。

* Git と Node.js をインストールする方法。
  * [Git](https://git-scm.com) は、オープン ソースの分散型バージョン管理システムです。 この記事のサンプル アプリケーションは、Git に格納されています。
  * [Node.js](https://nodejs.org/en/) は、豊富なパッケージ エコシステムが存在する JavaScript ランタイムです。
* NPM を使って、追加の Node.js 開発ツールをインストールする方法。
  * Node.js の最低限必要なバージョンは 4.5 LTS です。
  * [NPM](https://www.npmjs.com) は、Node.js のパッケージ マネージャーの&1; つです。

## <a name="what-do-you-need"></a>必要なもの
この操作を完了するには、以下が必要です。

* インターネット接続 (開発ツールとソフトウェアをダウンロードするため)。
* Ubuntu 16.04 以降を実行しているコンピューター。

## <a name="install-git-nodejs-and-npm"></a>Git、Node.js、NPM のインストール
キーボード ショートカット `Ctrl + Alt + T` を使って、ターミナルを開き、次のコマンドを実行します。

```bash
sudo apt-get update
curl -sL https://deb.nodesource.com/setup_6.x | sudo -E bash -
sudo apt-get install -y nodejs
sudo apt-get install git
```

## <a name="install-additional-nodejs-development-tools"></a>追加の Node.js 開発ツールのインストール
[gulp.js](http://gulpjs.com) を使って、Pi へのサンプル アプリケーションのデプロイを自動化します。 また、[device-discovery-cli](https://github.com/Azure/device-discovery-cli) を使って、IoT デバイスのネットワーク情報を取得します。

ターミナルで次のコマンドを実行して、`gulp` と `device-discovery-cli` をインストールします。

```bash
sudo npm install -g device-discovery-cli gulp
```

Ubuntu で Node.js やこれらの追加の開発ツールをインストールする際に問題が発生した場合、一般的な問題の解決策については、[トラブルシューティング ガイド](iot-hub-raspberry-pi-kit-node-troubleshooting.md)をご覧ください。

## <a name="install-visual-studio-code"></a>Visual Studio Code のインストール
Visual Studio Code を[ダウンロード](https://code.visualstudio.com/docs/setup/linux)して、インストールします。 Visual Studio Code は、Windows、Linux、macOS 向けの軽量で強力なソース コード エディターです。 チュートリアルの後半で、このエディターを使ってサンプル コードを編集します。

## <a name="summary"></a>概要
最初のサンプル アプリケーションに必要な開発ツールとソフトウェアをインストールしました。 次のタスクでは、サンプル アプリケーションを作成し、Pi にデプロイして、実行します。

## <a name="next-steps"></a>次のステップ
[サンプルの点滅アプリケーションの作成とデプロイ](iot-hub-raspberry-pi-kit-node-lesson1-deploy-blink-app.md)


