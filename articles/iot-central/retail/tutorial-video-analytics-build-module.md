---
title: チュートリアル - IoT Edge ライブ ビデオ分析モジュールを変更する
description: このチュートリアルでは、Video analytics - object and motion detection (ビデオ分析 - 物体とモーションの検出) アプリケーション テンプレートで使用されるライブ ビデオ分析ゲートウェイ モジュールに変更を加えてビルドする方法について説明します。
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 07/31/2020
ms.openlocfilehash: d21eb8d8d79ec04f0f7e766b4eeb370811553e64
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2020
ms.locfileid: "88037996"
---
# <a name="tutorial-modify-and-build-the-live-video-analytics-gateway-modules"></a>チュートリアル:ライブ ビデオ分析ゲートウェイ モジュールの変更とビルド

このチュートリアルでは、ライブ ビデオ分析 (LVA) モジュールの IoT Edge モジュール コードに変更を加える方法について説明します。

先行する各チュートリアルでは、既製のモジュール イメージが使用されています。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要になります。

* [Node.js](https://nodejs.org/en/download/) v10 以降
* [TSLint](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-typescript-tslint-plugin) 拡張機能がインストールされた [Visual Studio Code](https://code.visualstudio.com/Download)
* [Docker](https://www.docker.com/products/docker-desktop) エンジン
* モジュールのバージョンをホストするための [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/)。
* [Azure Media Services](https://docs.microsoft.com/azure/media-services/) アカウント。 先行するチュートリアルが済んでいる場合は、以前に作成したものを再利用してかまいません。

## <a name="clone-the-repository"></a>リポジトリの複製

まだリポジトリをクローンしていない場合は、次のコマンドを使用して、ローカル コンピューター上の適切な場所にクローンしてください。

```cmd
git clone https://github.com/Azure/live-video-analytics
```

VS Code で、ローカルの *live-video-analytics* リポジトリ フォルダーを開きます。

## <a name="edit-the-deploymentamd64json-file"></a>deployment.amd64.json ファイルを編集する

1. **lva-gateway** リポジトリのローカル コピーに、*ref-apps/lva-edge-iot-central-gateway/storage* というフォルダーを作成します (まだ作成していない場合)。 不注意で秘密情報をチェックインしてしまうことがないように、Git ではこのフォルダーが無視されます。
1. *setup* フォルダーから *storage* フォルダーに *deployment.amd64.json* ファイルをコピーします。
1. VS Code で、*storage/deployment.amd64.json* ファイルを開きます。
1. `registryCredentials` セクションを編集して、Azure Container Registry の資格情報を追加します。
1. `LvaEdgeGatewayModule` モジュール セクションを編集して、イメージの名前と AMS アカウントの名前を `env:amsAccountName:value` に追加します。
1. `lvaYolov3` モジュール セクションを編集して、イメージの名前を追加します。
1. `lvaEdge` モジュール セクションを編集して、イメージの名前を追加します。
1. 構成の方法について詳しくは、[Azure IoT Central でのビデオ分析アプリケーションの作成](tutorial-video-analytics-create-app.md)に関するページを参照してください。

## <a name="build-the-code"></a>コードのビルド

1. コードの初回ビルドを試みる前に、VS Code ターミナルを使用して `npm install` コマンドを実行します。 このコマンドによって、必要なパッケージがインストールされ、セットアップ スクリプトが実行されます。

    > [!TIP]
    > より新しいバージョンのリポジトリ (GitHub リポジトリ) をプルする場合は、*node_modules* フォルダーを削除してから再度 `npm install` を実行してください。

1. *./setup/imageConfig.json* ファイルを編集し、コンテナー レジストリ名に基づく名前のイメージを更新します。

    ```json
    {
        "arch": "amd64",
        "imageName": "[Server].azurecr.io/lva-edge-gateway"
    }
    ```

1. VS Code ターミナルを使用して `docker login [your server].azurecr.io` コマンドを実行します。 モジュールの配置マニフェストに指定したものと同じ資格情報を使用してください。

1. VS Code ターミナルを使用して **npm version patch** コマンドを実行します。 このビルド スクリプトは、コンテナー レジストリにイメージをデプロイします。 ビルドが成功したかどうかは、VS Code のターミナル ウィンドウの出力に表示されます。

1. ビルドが完了するたびに、**LvaEdgeGatewayModule** イメージのバージョンが増分されます。 配置マニフェスト ファイルには、このバージョンを使用する必要があります。

## <a name="next-steps"></a>次のステップ

以上、Video analytics - object and motion detection (ビデオ分析 - 物体とモーションの検出) アプリケーション テンプレートと LVA IoT Edge モジュールについて説明しました。今度は、次のテーマについて理解を深めることをお勧めします。

> [!div class="nextstepaction"]
> [Azure IoT Central を使用した小売ソリューションの構築](overview-iot-central-retail.md)
