---
title: "Azure IoT への Arduino の接続 - レッスン 1: ツールの入手 (Windows) | Microsoft Docs"
description: "Windows 7 以降で、Adafruit Feather M0 WiFi の最初のサンプル アプリケーションに必要なツールとソフトウェアをダウンロードしてインストールします。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "Arduino 開発ツール, IoT 開発, IoT ソフトウェア, モノのインターネット ソフトウェア, Windows での Git のインストール, Windows での Node.js のインストール"
ms.assetid: 9cfb8cd2-eafb-4ba2-b23e-d94e114ff3a6
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: dbf4be49bf806415c7a7817acb37c7e33be6267d
ms.lasthandoff: 01/24/2017


---
# <a name="get-the-tools-windows-7-or-later"></a>ツールの入手 (Windows 7 以降)

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
* Windows を実行しているコンピューター。

## <a name="install-git-and-nodejs"></a>Git と Node.js のインストール

以下のリンクをクリックして、Windows 用の Git と Node.js LTS をダウンロードしてインストールします。

* [Git for Windows の入手](https://git-scm.com/download/win/)
* [Windows 用の Node.js LTS の入手](https://nodejs.org/en/)

## <a name="install-additional-nodejs-development-tools"></a>追加の Node.js 開発ツールのインストール

[gulp.js](http://gulpjs.com) を使って、Arduino ボードへのサンプル アプリケーションのデプロイを自動化します。

管理者としてコマンド プロンプトを起動します。 ターミナルで次のコマンドを実行して、`gulp` と `device-discovery-cli` をインストールします。

```bash
npm install -g gulp device-discovery-cli
```

コンピューターで Node.js やこれらの追加の開発ツールをインストールする際に問題が発生した場合、一般的な問題の解決策については、[トラブルシューティング ガイド][troubleshooting]を参照してください。

## <a name="install-visual-studio-code"></a>Visual Studio Code のインストール

Visual Studio Code を[ダウンロード](https://code.visualstudio.com/docs/setup/windows)して、インストールします。 Visual Studio Code は、Windows、Linux、macOS 向けの軽量で強力なソース コード エディターです。 チュートリアルの後半で、このエディターを使ってサンプル コードを編集します。

## <a name="summary"></a>概要

最初のサンプル アプリケーションに必要な開発ツールとソフトウェアをインストールしました。 次のタスクでは、サンプル アプリケーションを作成し、Arduino ボードにデプロイして、実行します。

## <a name="next-steps"></a>次のステップ

[サンプルの点滅アプリケーションを作成してデプロイする][create-and-deploy-the-blink-sample-application]
<!-- Images and links -->

[windows]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-get-the-tools-win32.md
[ubuntu]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-get-the-tools-ubuntu.md
[macos]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-get-the-tools-mac.md
[troubleshooting]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md
[create-and-deploy-the-blink-sample-application]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-deploy-blink-app.md
