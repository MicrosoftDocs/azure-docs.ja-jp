---
title: "Azure IoT への Intel Edison (Node) の接続 - レッスン 1: ツールの入手 (Windows) | Microsoft Docs"
description: "Windows 7 以降で、Edison の最初のサンプル アプリケーションに必要なツールとソフトウェアをダウンロードしてインストールします。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "Arduino 開発ツール, IoT 開発, IoT ソフトウェア, モノのインターネット ソフトウェア, Windows での Git のインストール, Windows での Node.js のインストール"
ms.assetid: 4164b5a1-5a42-4d8a-9ff6-441e79fcc936
ms.service: iot-hub
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: adf5b10721a28432e6b37ef73c6a7e7ec9f93cdd
ms.openlocfilehash: 67e140e086d1deb17b7075133d969a93b859951c
ms.lasthandoff: 01/25/2017


---
# <a name="get-the-tools-windows-7-or-later"></a>ツールの入手 (Windows 7 以降)
> [!div class="op_single_selector"]
> * [Windows 7 以降][windows]
> * [Ubuntu 16.04][ubuntu]
> * [macOS 10.10][macos]

## <a name="what-you-will-do"></a>学習内容
Intel Edison の最初のサンプル アプリケーション用の開発ツールとソフトウェアをダウンロードします。 問題が発生した場合は、[トラブルシューティングのページ][troubleshooting]で解決策をお探しください。

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

[gulp.js](http://gulpjs.com) を使って、Edison へのサンプル アプリケーションのデプロイを自動化します。

管理者としてコマンド プロンプトを起動します。 次のコマンドを実行して `gulp` をインストールします。

```cmd
npm install -g gulp
```

コンピューターで Node.js やこれらの追加の開発ツールをインストールする際に問題が発生した場合、一般的な問題の解決策については、[トラブルシューティング ガイド][troubleshooting]を参照してください。

## <a name="install-visual-studio-code"></a>Visual Studio Code のインストール

Visual Studio Code を[ダウンロード](https://code.visualstudio.com/docs/setup/windows)して、インストールします。 Visual Studio Code は、Windows、Linux、macOS 向けの軽量で強力なソース コード エディターです。 チュートリアルの後半で、このエディターを使ってサンプル コードを編集します。

## <a name="summary"></a>概要

最初のサンプル アプリケーションに必要な開発ツールとソフトウェアをインストールしました。 次のタスクでは、サンプル アプリケーションを作成し、Edison にデプロイして、実行します。

## <a name="next-steps"></a>次のステップ

[点滅アプリケーションを作成してデプロイする][create-and-deploy-the-blink-application]
<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-node-troubleshooting.md
[create-and-deploy-the-blink-application]: iot-hub-intel-edison-kit-node-lesson1-deploy-blink-app.md
[windows]: iot-hub-intel-edison-kit-node-lesson1-get-the-tools-win32.md
[ubuntu]: iot-hub-intel-edison-kit-node-lesson1-get-the-tools-ubuntu.md
[macos]: iot-hub-intel-edison-kit-node-lesson1-get-the-tools-mac.md

