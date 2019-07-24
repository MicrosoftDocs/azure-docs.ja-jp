---
title: Node.js を使用してイベントを送信する - Azure Event Hubs | Microsoft Docs
description: この記事では、Azure Event Hubs からイベントを送信する Node.js アプリケーションを作成するためのチュートリアルを提供します。
services: event-hubs
author: spelluru
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.custom: seodec18
ms.date: 02/19/2019
ms.author: spelluru
ms.openlocfilehash: ec3182d11f1b2ffa31acd05fa1f2db695f3f2cf7
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/20/2019
ms.locfileid: "56447720"
---
# <a name="send-events-to-azure-event-hubs-using-nodejs"></a>Node.js を使用して Azure Event Hubs にイベントを送信する

Azure Event Hubs はビッグ データ ストリーミング プラットフォームであり、毎秒数百万のイベントを受け取って処理できるイベント インジェスト サービスです。 Event Hubs では、分散されたソフトウェアやデバイスから生成されるイベント、データ、またはテレメトリを処理および格納できます。 イベント ハブに送信されたデータは、任意のリアルタイム分析プロバイダーやバッチ処理/ストレージ アダプターを使用して、変換および保存できます。 Event Hubs の詳しい概要については、[Event Hubs の概要](event-hubs-about.md)と [Event Hubs の機能](event-hubs-features.md)に関するページをご覧ください。

このチュートリアルでは、Node.js で書かれたアプリケーションからイベント ハブにイベントを送信する方法について説明します。

> [!NOTE]
> このクイック スタートをサンプルとして [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client) からダウンロードし、`EventHubConnectionString` と `EventHubName` の文字列を対象のイベント ハブの値に置き換え、実行します。 または、このチュートリアルの手順に従って独自のものを作成します。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次の前提条件を用意しておく必要があります。

- Node.js バージョン 8.x 以降。 [https://nodejs.org](https://nodejs.org) から最新の LTS バージョンをダウンロードします。
- Visual Studio Code (推奨) または他の任意の IDE

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Event Hubs 名前空間とイベント ハブを作成する
最初の手順では、[Azure Portal](https://portal.azure.com) を使用して Event Hubs 型の名前空間を作成し、アプリケーションがイベント ハブと通信するために必要な管理資格情報を取得します。 名前空間とイベント ハブを作成するには、[こちらの記事](event-hubs-create.md)の手順を実行した後、このチュートリアルに示されている手順を続けます。

次の記事の手順に従って、イベント ハブ名前空間用の接続文字列を取得します。[接続文字列を取得する](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)。 このチュートリアルの中で、その接続文字列が後で必要になります。

## <a name="clone-the-sample-git-repository"></a>サンプル Git リポジトリを複製する
サンプルの Git リポジトリを [GitHub](https://github.com/Azure/azure-event-hubs-node) からお使いのコンピューターに複製します。 

## <a name="install-nodejs-package"></a>Node.js パッケージをインストールする
Azure Event Hubs 用の Node.js パッケージをお使いのコンピューターにインストールします。 

```shell
npm install @azure/event-hubs
```

## <a name="clone-the-git-repository"></a>Git リポジトリを複製する
GitHub から[サンプル](https://github.com/Azure/azure-event-hubs-node/tree/master/client/examples)をダウンロードまたは複製します。 

## <a name="send-events"></a>送信イベント
複製した SDK には、Node.js を使用してイベント ハブにイベントを送信する方法を示す、複数のサンプルが含まれます。 このクイック スタートでは、**simpleSender.js** の例を使用します。 受信しているイベントを確認するには、別のターミナルを開き、[受信のサンプル](event-hubs-node-get-started-receive.md)を使用してイベントを受信します。

1. Visual Studio Code でプロジェクトを開きます。 
2. **client** フォルダーに **.env** という名前のファイルを作成します。 ルート フォルダーの **sample.env** からサンプルの環境変数をコピーして貼り付けます。
3. 自分のイベント ハブの接続文字列、イベント ハブ名、およびストレージ エンドポイントを構成します。 イベント ハブの接続文字列を取得する方法については、「[接続文字列を取得する](event-hubs-create.md#create-an-event-hubs-namespace)」をご覧ください。
4. Azure CLI で、**client** フォルダー パスに移動します。 次のコマンドを実行して、ノード パッケージをインストールし、プロジェクトをビルドします。

    ```shell
    npm i
    npm run build
    ```
5. 次のコマンドを実行して、イベントの送信を開始します。 

    ```shell
    node dist/examples/simpleSender.js
    ```


## <a name="review-the-sample-code"></a>サンプル コードを確認する 
イベントをイベント ハブに送信するサンプル コードを simpleSender.js ファイルで確認します。

```javascript
"use strict";
Object.defineProperty(exports, "__esModule", { value: true });
const lib_1 = require("../lib");
const dotenv = require("dotenv");
dotenv.config();
const connectionString = "EVENTHUB_CONNECTION_STRING";
const entityPath = "EVENTHUB_NAME";
const str = process.env[connectionString] || "";
const path = process.env[entityPath] || "";

async function main() {
    const client = lib_1.EventHubClient.createFromConnectionString(str, path);
    const data = {
        body: "Hello World!!"
    };
    const delivery = await client.send(data);
    console.log(">>> Sent the message successfully: ", delivery.tag.toString());
    console.log(delivery);
    console.log("Calling rhea-promise sender close directly. This should result in sender getting reconnected.");
    await Object.values(client._context.senders)[0]._sender.close();
    // await client.close();
}

main().catch((err) => {
    console.log("error: ", err);
});

```

スクリプトを実行する前に、環境変数を設定することを忘れないでください。 次の例に示すように、コマンド ラインでこれを構成するか、[dotenv パッケージ](https://www.npmjs.com/package/dotenv#dotenv)を使用することができます。 

```shell
// For windows
set EVENTHUB_CONNECTION_STRING="<your-connection-string>"
set EVENTHUB_NAME="<your-event-hub-name>"

// For linux or macos
export EVENTHUB_CONNECTION_STRING="<your-connection-string>"
export EVENTHUB_NAME="<your-event-hub-name>"
```

## <a name="next-steps"></a>次の手順
このクイック スタートでは、Node.js を使用してメッセージをイベント ハブに送信しました。 Node.js を使用してイベント ハブからイベントを受信する方法については、[Node.js を使用してイベント ハブからイベントを受信する方法](event-hubs-node-get-started-receive.md)に関するページをご覧ください。

Event Hubs に関する他の Node.js のサンプルを [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client/examples/) で確認してください。
