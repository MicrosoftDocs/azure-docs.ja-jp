---
title: Azure での音声、VoIP、および SMS メッセージングのための Twilio の使用
description: Azure で Twilio API サービスを使用して通話や SMS メッセージの送信を行う方法について学習します。 コード サンプルは Node.js で記述されています。
documentationcenter: nodejs
author: georgewallace
ms.assetid: f558cbbd-13d2-416f-b9b1-33a99c426af9
ms.service: multiple
ms.devlang: nodejs
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: c4b1c506ff0e78e5e304ceb1682c73f9cd784c95
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88080543"
---
# <a name="using-twilio-for-voice-voip-and-sms-messaging-in-azure"></a>Azure での音声、VoIP、および SMS メッセージングのための Twilio の使用
このガイドでは、Azure で Twilio と node.js によって通信するアプリケーションを構築する方法を示します。

<a name="whatis"></a>

## <a name="what-is-twilio"></a>Twilio とは
Twilio は、開発者が簡単に音声通話の発着信処理、テキスト メッセージの送受信、およびブラウザー ベースでネイティブのモバイル アプリケーションへの VoIP 通話の埋め込みができるようにする API プラットフォームです。 ここでは、詳細について説明する前に、そのしくみを簡単に解説します。

### <a name="receiving-calls-and-text-messages"></a>通話とテキスト メッセージの受信
Twilio を使用すると、開発者は[プログラム可能な電話番号を購入][purchase_phone]できます。この番号は、通話およびテキスト メッセージの送信と受信の両方に使用できます。 Twilio 番号が受信通話またはテキストを受け取ると、Twilio は Web アプリケーションに HTTP POST または GET 要求を送信し、通話またはテキストの処理方法に関する命令を求めます。 サーバーは Twilio の HTTP 要求に [TwiML][twiml] で応答します。これは、通話またはテキストの処理方法についての命令を含む XML タグの単純なセットです。 TwiML の例は、すぐ後で示します。

### <a name="making-calls-and-sending-text-messages"></a>通話の発信とテキスト メッセージの送信
Twilio Web サービス API に対する HTTP 要求を作成することで、開発者はテキスト メッセージを送信したり、発信通話を開始したりすることができます。 発信通話では、開発者は発信通話の接続後の処理方法についての TwiML 命令を返す URL も指定する必要があります。

### <a name="embedding-voip-capabilities-in-ui-code-javascript-ios-or-android"></a>UI コード (JavaScript、iOS、または Android) への VoIP 機能の埋め込み
Twilio にはクライアント側 SDK が用意されており、デスクトップ Web ブラウザー、iOS アプリ、または Android アプリを VoIP 電話にすることができます。 この記事では、主にブラウザーで VoIP 通話を使用する方法について説明します。 ブラウザーで実行される *Twilio JavaScript SDK* のほかに、サーバー側アプリケーション (node.js アプリケーション) を使用して、JavaScript クライアントに "機能トークン" を発行する必要があります。 node.js での VoIP の使用については、[Twilio 開発ブログ][voipnode]で詳細を読むことができます。

<a name="signup"></a>

## <a name="sign-up-for-twilio-microsoft-discount"></a>Twilio へのサインアップ (Microsoft ディスカウント)
Twilio サービスを使用する前に、まず、[アカウントにサインアップ][signup]する必要があります。 Microsoft Azure のお客様は特別割引をご利用いただけます。[こちらからサインアップ][signup]してください。

<a name="azuresite"></a>

## <a name="create-and-deploy-a-nodejs-azure-website"></a>node.js Azure Web サイトの作成およびデプロイ
次に、Azure 上で実行される node.js Web サイトを作成する必要があります。 [そのための公式なドキュメントは、ここにあります][azure_new_site]。 大まかには、次の作業を行います。

* まだ Azure アカウントを持っていない場合は、サインアップする
* Azure 管理コンソールを使用して新しい Web サイトを作成する
* ソース管理サポートを追加する (git が使用されたことを想定)
* シンプルな node.js Web アプリケーションでファイル `server.js` を作成する
* このシンプルなアプリケーションを Azure にデプロイする

<a name="twiliomodule"></a>

## <a name="configure-the-twilio-module"></a>Twilio モジュールの構成
次に、Twilio API を使用するシンプルな node.js アプリケーションの記述を開始します。 その前に、Twilio アカウント資格情報を構成する必要があります。

### <a name="configuring-twilio-credentials-in-system-environment-variables"></a>システム環境変数での Twilio 資格情報の構成
Twilio バックエンドに対して認証された要求を行うには、アカウント SID と認証トークンが必要です。これらが Twilio アカウントのユーザー名とパスワードのセットとして機能します。 これらを Azure のノード モジュールで使用するように構成する最も安全な方法は、システム環境変数を通じて行う方法です。システム環境変数は、Azure 管理コンソールで直接設定できます。

node.js Web サイトを選択し、[構成] リンクをクリックします。  下へ少しスクロールすると表示される領域で、アプリケーションの構成プロパティを設定します。  以下のように、Twilio アカウント資格情報を入力します ([Twilio Console で確認できます][twilio_console])。それぞれを "`TWILIO_ACCOUNT_SID`" と "`TWILIO_AUTH_TOKEN`" にします。

![Azure 管理コンソール][azure-admin-console]

これらの変数を構成したら、Azure コンソールでアプリケーションを再起動します。

### <a name="declaring-the-twilio-module-in-packagejson"></a>package.json での Twilio モジュールの宣言
次に、package.json を作成して、 [npm]を通じてノード モジュールの依存関係を管理する必要があります。 *Azure/node.js* のチュートリアルで作成した `server.js` ファイルと同じレベルで、`package.json` という名前のファイルを作成します。  このファイル内に、次のコードを追加します。

```json
{
  "name": "application-name",
  "version": "0.0.1",
  "private": true,
  "scripts": {
    "start": "node server"
  },
  "dependencies": {
    "body-parser": "^1.16.1",
    "ejs": "^2.5.5",
    "errorhandler": "^1.5.0",
    "express": "^4.14.1",
    "morgan": "^1.8.1",
    "twilio": "^2.11.1"
  }
}
```

このコードは、一般的な [Express Web フレームワーク][express]および EJS テンプレート エンジンと共に、Twilio モジュールを依存関係として宣言します。  これで設定はすべて終わりました。コードの記述を始めましょう。

<a name="makecall"></a>

## <a name="make-an-outbound-call"></a>発信通話の実行
選択した番号に電話をかける単純なフォームを作成しましょう。 `server.js` を開き、次のコードを入力します。 "CHANGE_ME" と記述されている個所は、自分の Azure Web サイトの名前に置き換えてください。

```javascript
// Module dependencies
const express = require('express');
const path = require('path');
const http = require('http');
const twilio = require('twilio');
const logger = require('morgan');
const bodyParser = require('body-parser');
const errorHandler = require('errorhandler');
const accountSid = process.env.TWILIO_ACCOUNT_SID;
const authToken = process.env.TWILIO_AUTH_TOKEN;
// Create Express web application
const app = express();

// Express configuration
app.set('port', process.env.PORT || 3000);
app.set('views', __dirname + '/views');
app.set('view engine', 'ejs');
app.use(logger('tiny'));
app.use(bodyParser.urlencoded({ extended: false }))
app.use(bodyParser.json())
app.use(express.static(path.join(__dirname, 'public')));

if (app.get('env') !== 'production') {
  app.use(errorHandler());
}

// Render an HTML user interface for the application's home page
app.get('/', (request, response) => response.render('index'));

// Handle the form POST to place a call
app.post('/call', (request, response) => {
  var client = twilio(accountSid, authToken);

  client.makeCall({
    // make a call to this number
    to:request.body.number,

    // Change to a Twilio number you bought - see:
    // https://www.twilio.com/console/phone-numbers/incoming
    from:'+15558675309',

    // A URL in our app which generates TwiML
    // Change "CHANGE_ME" to your app's name
    url:'https://CHANGE_ME.azurewebsites.net/outbound_call'
  }, () => {
      // Go back to the home page
      response.redirect('/');
  });
});

// Generate TwiML to handle an outbound call
app.post('/outbound_call', (request, response) => {
  var twiml = new twilio.TwimlResponse();

  // Say a message to the call's receiver
  twiml.say('hello - thanks for checking out Twilio and Azure', {
      voice:'woman'
  });

  response.set('Content-Type', 'text/xml');
  response.send(twiml.toString());
});

// Start server
app.listen(app.get('port'), function(){
  console.log(`Express server listening on port ${app.get('port')}`);
});
```

次に、`views` という名前のディレクトリを作成します。このディレクトリ内に、次の内容を持つ `index.ejs` という名前のファイルを作成します。

```html
<!DOCTYPE html>
<html>
<head>
  <title>Twilio Test</title>
  <style>
    input { height:20px; width:300px; font-size:18px; margin:5px; padding:5px; }
  </style>
</head>
<body>
  <h1>Twilio Test</h1>
  <form action="/call" method="POST">
      <input placeholder="Enter a phone number" name="number"/>
      <br/>
      <input type="submit" value="Call the number above"/>
  </form>
</body>
</html>
```

ここで、Web サイトを Azure に展開し、ホームを開きます 自分の電話番号をテキスト フィールドに入力し、Twilio 番号からの通話を着信できるはずです。

<a name="sendmessage"></a>

## <a name="send-an-sms-message"></a>SMS メッセージの送信
ここでは、テキスト メッセージの送信ロジックを処理するユーザー インターフェイスとフォームをセットアップします。 `server.js` を開いて、最後の `app.post` の呼び出しの後に次のコードを追加します。

```javascript
app.post('/sms', (request, response) => {
  const client = twilio(accountSid, authToken);

  client.sendSms({
      // send a text to this number
      to:request.body.number,

      // A Twilio number you bought - see:
      // https://www.twilio.com/console/phone-numbers/incoming
      from:'+15558675309',

      // The body of the text message
      body: request.body.message

  }, () => {
      // Go back to the home page
      response.redirect('/');
  });
});
```

`views/index.ejs` で、最初のフォームの下に、番号とテキスト メッセージを送信するためのもう 1 つのフォームを追加します。

```html
<form action="/sms" method="POST">
  <input placeholder="Enter a phone number" name="number"/>
  <br/>
  <input placeholder="Enter a message to send" name="message"/>
  <br/>
  <input type="submit" value="Send text to the number above"/>
</form>
```

アプリケーションを Azure に再デプロイします。今度は、そのフォームを送信し、テキスト メッセージを自分 (または親しい友人) に送ることができるはずです。

<a name="nextsteps"></a>

## <a name="next-steps"></a>次の手順
これで、通信するアプリケーションを node.js と Twilio を使用して構築する方法の基本を学習できました。 ただし、これらは、Twilio と node.js でできることの初歩的な例にすぎません。 Node.js と Twilio の使用方法の詳細については、次のリソースをご覧ください。

* [公式のモジュールのドキュメント][docs]
* [node.js アプリケーションによる VoIP のチュートリアル][voipnode]
* [Votr - node.js と CouchDB によるリアルタイムの SMS 投票アプリケーション (3 部構成)][votr]
* [node.js によるブラウザーでのペア プログラミング][pair]

Azure で node.js と Twilio を活用してください。

[purchase_phone]: https://www.twilio.com/console/phone-numbers/search
[twiml]: https://www.twilio.com/docs/api/twiml
[signup]: https://ahoy.twilio.com/azure
[azure_new_site]: app-service/quickstart-nodejs.md
[twilio_console]: https://www.twilio.com/console
[npm]: https://npmjs.org
[express]: https://expressjs.com
[voipnode]: https://www.twilio.com/blog/2013/04/introduction-to-twilio-client-with-node-js.html
[docs]: https://www.twilio.com/docs/libraries/reference/twilio-node/
[votr]: https://www.twilio.com/blog/2012/09/building-a-real-time-sms-voting-app-part-1-node-js-couchdb.html
[pair]: https://www.twilio.com/blog/2013/06/pair-programming-in-the-browser-with-twilio.html
[azure-admin-console]: ./media/partner-twilio-nodejs-how-to-use-voice-sms/twilio_1.png
