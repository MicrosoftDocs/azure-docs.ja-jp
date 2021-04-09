---
title: SendGrid 電子メール サービスの使用方法 (Node.js) | Microsoft Docs
description: Azure で SendGrid 電子メール サービスを使用して電子メールを送信する方法について説明します。 コード サンプルは Node.js API を使用して記述されています。
services: ''
documentationcenter: nodejs
author: erikre
manager: wpickett
editor: ''
ms.assetid: cac444b4-26b0-45ea-9c3d-eca28d57dacb
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 01/05/2016
ms.author: erikre
ms.openlocfilehash: f2d653441598a47986913d525057672eed24b435
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96015428"
---
# <a name="how-to-send-email-using-sendgrid-from-nodejs"></a>SendGrid を使用して Node.js から電子メールを送信する方法

このガイドでは、Azure の SendGrid 電子メール サービスを使用して一般的なプログラム タスクを実行する方法を紹介します。 サンプルは Node.js API を使用して記述されています。 紹介するシナリオは、**電子メールの作成**、**電子メールの送信**、**添付ファイルの追加**、**フィルターの使用**、および **プロパティの更新** です。 SendGrid と電子メールの送信の詳細については、「 [次のステップ](#next-steps) 」を参照してください。

## <a name="what-is-the-sendgrid-email-service"></a>SendGrid 電子メール サービスとは

SendGrid は、信頼性の高い[トランザクション メール配信]、拡張性、およびリアルタイム分析の機能を備えた[クラウドベースの電子メール サービス]であり、柔軟な API を備えているためカスタム統合も容易です。 SendGrid の一般的な使用シナリオを次に示します。

* 顧客に受信通知を自動送信する
* 顧客に広告メールを月 1 回送信するための配布リストを管理する
* ブロックされた電子メールや顧客の応答性などを表す測定値をリアルタイムで収集する
* 傾向を認識するために役立つレポートを生成する
* 顧客の問い合わせを転送する
* アプリケーションからの電子メール通知

詳細については、「[https://sendgrid.com](https://sendgrid.com)」を参照してください。

## <a name="create-a-sendgrid-account"></a>SendGrid アカウントの作成

[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="reference-the-sendgrid-nodejs-module"></a>SendGrid Node.js モジュールの参照

Node.js 用の SendGrid モジュールは、次のコマンドを使用することによって、ノード パッケージ マネージャー (npm) でインストールできます。

```bash
npm install sendgrid
```

インストールした後、次のコードを使用して、アプリケーションでモジュールを要求できます。

```javascript
var sendgrid = require('sendgrid')(sendgrid_username, sendgrid_password);
```

SendGrid モジュールは、**SendGrid** 機能および **Email** 機能をエクスポートします。
**SendGrid** は Web API による電子メールの送信を処理します。**Email** は電子メール メッセージをカプセル化します。

## <a name="how-to-create-an-email"></a>方法: 電子メールを作成する

SendGrid モジュールを使って電子メール メッセージを作成するには、最初に Email 機能で電子メール メッセージを作成し、次に SendGrid 機能でメッセージを送信します。 以下は、Email 機能を使用した新しいメッセージの作成の例です。

```javascript
var email = new sendgrid.Email({
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.'
});
```

また、html プロパティを設定することによって、HTML メッセージをサポートするクライアント向けに H|TML メッセージを指定することもできます。 次に例を示します。

```javascript
html: This is a sample <b>HTML<b> email message.
```

text プロパティと html プロパティの両方を設定することで、HTML メッセージをサポートできないクライアントに対しては、テキスト コンテンツへの正常なフォールバックを提供できます。

Email 機能でサポートされるすべてのプロパティの詳細については、「[sendgrid-nodejs][sendgrid-nodejs]」を参照してください。

## <a name="how-to-send-an-email"></a>方法: 電子メールを送信する

Email 機能で電子メール メッセージを作成した後で、SendGrid の Web API を使用してメッセージを送信できます。 

### <a name="web-api"></a>Web API

```javascript
sendgrid.send(email, function(err, json){
    if(err) { return console.error(err); }
    console.log(json);
});
```

> [!NOTE]
> 前の例では、電子メール オブジェクトとコールバック関数の受け渡しを示していますが、電子メールのプロパティを直接指定することによって、send 関数を直接呼び出すこともできます。 次に例を示します。  
> 
> ```javascript
> sendgrid.send({
> to: 'john@contoso.com',
> from: 'anna@contoso.com',
> subject: 'test mail',
> text: 'This is a sample email message.'
> });
> ```
>

## <a name="how-to-add-an-attachment"></a>方法: 添付ファイルを追加する
添付ファイルをメッセージに追加するには、**files** プロパティでファイル名とパスを指定します。 次の例に、添付ファイルを送信する方法を示します。

```javascript
sendgrid.send({
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.',
    files: [
        {
            filename:     '',           // required only if file.content is used.
            contentType:  '',           // optional
            cid:          '',           // optional, used to specify cid for inline content
            path:         '',           //
            url:          '',           // == One of these three options is required
            content:      ('' | Buffer) //
        }
    ],
});
```

> [!NOTE]
> **files** プロパティを使用する場合、[fs.readFile](https://nodejs.org/docs/v0.6.7/api/fs.html#fs.readFile) を使ってファイルにアクセスできることが必要です。 添付するファイルが、BLOB コンテナーなどの Azure Storage でホストされている場合、**files** プロパティを使用して添付ファイルとして送信するには、最初にファイルをローカル ストレージまたは Azure ドライブにコピーする必要があります。
> 
> 

## <a name="how-to-use-filters-to-enable-footers-and-tracking"></a>方法: フィルターを使用してフッターと追跡を有効にする

SendGrid では、*フィルター* を使用することでその他の電子メール機能も利用できます。 その設定を電子メール メッセージに追加することで、クリック追跡、Google 分析、サブスクリプション追跡などの独自の機能を有効にすることができます。 すべてのフィルターの一覧については、 [フィルター設定][Filter Settings]に関するページを参照してください。

フィルターは、**フィルター** のプロパティを使用してメッセージに適用できます。
各フィルターは、フィルター固有の設定を格納したハッシュで指定します。
次の例に、フッター フィルターとクリック追跡フィルターの使用方法を示します。

### <a name="footer"></a>フッター

```javascript
var email = new sendgrid.Email({
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.'
});

email.setFilters({
    'footer': {
        'settings': {
            'enable': 1,
            'text/plain': 'This is a text footer.'
        }
    }
});

sendgrid.send(email);
```

### <a name="click-tracking"></a>クリック追跡

```javascript
var email = new sendgrid.Email({
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.'
});

email.setFilters({
    'clicktrack': {
        'settings': {
            'enable': 1
        }
    }
});

sendgrid.send(email);
```

## <a name="how-to-update-email-properties"></a>方法: 電子メールのプロパティを更新する

メールの一部のプロパティは、**setProperty** を使って上書きすることや、**addProperty** を使って追加することができます。 たとえば、次のようにして、新しい受信者を追加できます。

```javascript
email.addTo('jeff@contoso.com');
```

また、次のように、フィルターを設定することもできます。

```javascript
email.addFilter('footer', 'enable', 1);
email.addFilter('footer', 'text/html', '<strong>boo</strong>');
```

詳しくは、「[sendgrid-nodejs][sendgrid-nodejs]」をご覧ください。

## <a name="how-to-use-additional-sendgrid-services"></a>方法: その他の SendGrid サービスを使用する

SendGrid の Web ベース API を使用して、Azure アプリケーションからその他の SendGrid 機能を利用することができます。 詳細については、[SendGrid API に関するドキュメント][SendGrid API documentation]を参照してください。

## <a name="next-steps"></a>次の手順

これで、SendGrid 電子メール サービスの基本を学習できました。さらに詳細な情報が必要な場合は、次のリンク先を参照してください。

* SendGrid Node.js モジュールのリポジトリ: [sendgrid-nodejs][sendgrid-nodejs]
* SendGrid API に関するドキュメント: <https://sendgrid.com/docs>
* Azure ユーザー向けの SendGrid 特別プラン: [http://sendgrid.com/azure.html](https://sendgrid.com/windowsazure.html)

[special offer]: https://sendgrid.com/windowsazure.html
[sendgrid-nodejs]: https://github.com/sendgrid/sendgrid-nodejs
[Filter Settings]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
[SendGrid API documentation]: https://sendgrid.com/docs
[クラウドベースの電子メール サービス]: https://sendgrid.com/email-solutions
[トランザクション メール配信]: https://sendgrid.com/transactional-email
