---
title: インクルード ファイル
description: インクルード ファイル
services: azure-communication-services
author: bertong
manager: ankita
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/11/2021
ms.topic: include
ms.custom: include file
ms.author: bertong
ms.openlocfilehash: 0d142c477e1de2a2a34a8abfd948800cc0b607ee
ms.sourcegitcommit: 27cd3e515fee7821807c03e64ce8ac2dd2dd82d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103622345"
---
Communication Services JavaScript SMS クライアント ライブラリを使用して SMS メッセージを送信することによって、Azure Communication Services の使用を開始します。

このクイックスタートを完了すると、ご利用の Azure アカウントでわずかな (数セント未満の) コストが発生します。

<!--**TODO: update all these reference links as the resources go live**

[API reference documentation](../../../references/overview.md) | [Library source code](https://github.com/Azure/azure-sdk-for-js-pr/tree/feature/communication/sdk/communication/communication-sms) | [Package (NPM)](https://www.npmjs.com/package/@azure/communication-sms) | [Samples](#todo-samples)-->

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Node.js](https://nodejs.org/) アクティブ LTS およびメンテナンス LTS バージョン (8.11.1 および 10.14.1 を推奨)。
- アクティブな Communication Services リソースと接続文字列。 [Communication Services リソースを作成します](../../create-communication-resource.md)。
- SMS が有効になっている電話番号。 [電話番号を取得します](../get-phone-number.md)。

### <a name="prerequisite-check"></a>前提条件のチェック

- ターミナルまたはコマンド ウィンドウで `node --version` を実行して、Node.js がインストールされていることを確認します。
- Communication Services リソースに関連付けられている電話番号を表示するには、[Azure portal](https://portal.azure.com/) にサインインし、Communication Services リソースを見つけて、左側のナビゲーション ペインから **[電話番号]** タブを開きます。

## <a name="setting-up"></a>設定

### <a name="create-a-new-nodejs-application"></a>新しい Node.js アプリケーションを作成する

まず、ターミナルまたはコマンド ウィンドウを開き、アプリ用の新しいディレクトリを作成して、そこに移動します。

```console
mkdir sms-quickstart && cd sms-quickstart
```

既定の設定で `npm init -y` を実行して、**package.json** ファイルを作成します。

```console
npm init -y
```

テキスト エディターを使用して、プロジェクトのルート ディレクトリに **send-sms.js** というファイルを作成します。 このクイックスタートのソース コードはすべて、以降のセクションでこのファイルに追加することになります。

### <a name="install-the-package"></a>パッケージをインストールする

`npm install` コマンドを使用して、JavaScript 用の Azure Communication Services SMS クライアント ライブラリをインストールします。

```console
npm install @azure/communication-sms --save
```

`--save` オプションを使用すると、**package.json** ファイル内の依存関係としてライブラリが表示されます。

## <a name="object-model"></a>オブジェクト モデル

Node.js 用 Azure Communication Services SMS クライアント ライブラリが備える主な機能のいくつかは、以下のクラスとインターフェイスにより処理されます。

| 名前                                  | 説明                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| SmsClient | このクラスは、すべての SMS 機能に必要となります。 サブスクリプション情報を使用してこれをインスタンス化し、そのインスタンスを使用して SMS を送信します。 |
| SmsSendResult               | このクラスには、SMS サービスからの結果が含まれます。                                          |
| SmsSendOptions | このインターフェイスには、配信レポートを構成するためのオプションが用意されています。 `enableDeliveryReport` が `true` に設定されている場合、配信が成功したときにイベントが生成されます。 |
| SmsSendRequest | このインターフェイスは、sms 要求を作成するためのモデルです (例: 発信および着信電話番号と sms コンテンツの構成)。 |

## <a name="authenticate-the-client"></a>クライアントを認証する

クライアント ライブラリから **SmsClient** をインポートし、接続文字列を使用してインスタンス化します。 次のコードは、`COMMUNICATION_SERVICES_CONNECTION_STRING` という名前の環境変数からリソースの接続文字列を取得します。 [リソースの接続文字列を管理する](../../create-communication-resource.md#store-your-connection-string)方法を参照してください。

次のコードを **send-sms.js** に追加します。

```javascript
const { SmsClient } = require('@azure/communication-sms');

// This code demonstrates how to fetch your connection string
// from an environment variable.
const connectionString = process.env['COMMUNICATION_SERVICES_CONNECTION_STRING'];

// Instantiate the SMS client
const smsClient = new SmsClient(connectionString);
```

## <a name="send-a-1n-sms-message"></a>1:N の SMS メッセージを送信する

受信者の一覧に SMS メッセージを送信するには、受信者の電話番号の一覧を使用して SmsClient から `send` 関数を呼び出します (1 人の受信者にメッセージを送信する場合は、一覧に 1 つの番号のみを含めます)。 **send-sms.js** の末尾に次のコードを追加します。

```javascript
async function main() {
  const sendResults = await smsClient.send({
    from: "<from-phone-number>",
    to: ["<to-phone-number-1>", "<to-phone-number-2>"],
    message: "Hello World 👋🏻 via SMS"
  });

  // individual messages can encounter errors during sending
  // use the "successful" property to verify
  for (const sendResult of sendResults) {
    if (sendResult.successful) {
      console.log("Success: ", sendResult);
    } else {
      console.error("Something went wrong when trying to send this message: ", sendResult);
    }
  }
}

main();
```
`<from-phone-number>` は Communication Services リソースに関連付けられている、SMS が有効になっている電話番号で置き換え、`<to-phone-number>` はメッセージの送信先の電話番号で置き換える必要があります。

## <a name="send-a-1n-sms-message-with-options"></a>オプションを使用して 1:N の SMS メッセージを送信する

また、オプションのオブジェクトを渡して、配信レポートを有効にするかどうか、およびカスタム タグを設定するかどうかを指定することもできます。

```javascript

async function main() {
  await smsClient.send({
    from: "<from-phone-number>",
    to: ["<to-phone-number-1>", "<to-phone-number-2>"],
    message: "Weekly Promotion!"
  }, {
    //Optional parameter
    enableDeliveryReport: true,
    tag: "marketing"
  });

  // individual messages can encounter errors during sending
  // use the "successful" property to verify
  for (const sendResult of sendResults) {
    if (sendResult.successful) {
      console.log("Success: ", sendResult);
    } else {
      console.error("Something went wrong when trying to send this message: ", sendResult);
    }
  }
}

main();
```

`enableDeliveryReport` パラメーターは、配信レポートを構成するために使用できる省略可能なパラメーターです。 これは、SMS メッセージが配信されたときにイベントを生成する場合に便利です。 SMS メッセージの配信レポートを構成するには、[SMS イベントの処理](../handle-sms-events.md)に関するクイックスタートを参照してください。
`tag` は、配信レポートにタグを適用するために使用できる省略可能なパラメーターです。

## <a name="run-the-code"></a>コードの実行

`node` コマンドを使用して、**send-sms.js** ファイルに追加したコードを実行します。

```console

node ./send-sms.js

```
