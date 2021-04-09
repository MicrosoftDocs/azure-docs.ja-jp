---
title: 音声および SMS に Twilio を使用する方法 (PHP) | Microsoft Docs
description: Azure で Twilio API サービスを使用して通話や SMS メッセージの送信を行う方法について学習します。 コード サンプルは PHP で記述されています。
documentationcenter: php
services: ''
author: georgewallace
ms.assetid: 007f22e3-ac75-4868-8315-da000c2e0dd0
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: bf1ab01b39d594002bc5e677ffe6c3049fbb91ce
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "95521021"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-php"></a>PHP で音声および SMS 機能に Twilio を使用する方法
このガイドでは、Azure の Twilio API サービスを使用して一般的なプログラミング タスクを実行する方法を紹介します。 電話の発信と Short Message Service (SMS) メッセージの送信の各シナリオについて説明します。 Twilio の詳細、およびアプリケーションで音声と SMS を使用する方法については、「 [次のステップ](#NextSteps) 」を参照してください。

## <a name="what-is-twilio"></a><a id="WhatIs"></a>Twilio とは
Twilio は、開発者がアプリケーションに音声、VoIP、およびメッセージングを埋め込むことを可能にし、ビジネス コミュニケーションを強化していきます。 必要なすべてのインフラストラクチャをクラウド ベースのグローバル環境で仮想化し、Twilio 通信 API プラットフォームを通じてそれを公開します。 アプリケーションは構築しやすく、スケーラビリティも優れています。 従量課金制の柔軟性と、クラウドの信頼性の利点を活用できます。

**Twilio Voice** を使用すると、アプリケーションで音声通話の発着信処理を行うことができます。 **Twilio SMS** を使用すると、アプリケーションでテキスト メッセージの送受信を行うことができます。 **Twilio Client** では、任意の電話、タブレット、またはブラウザーから VoIP 通話を行うことができ、WebRTC がサポートされています。

## <a name="twilio-pricing-and-special-offers"></a><a id="Pricing"></a>Twilio の料金および特別プラン
Azure ユーザーには、[特別プラン](https://www.twilio.com/azure)として、Twilio アカウントをアップグレードする際に、$10 の Twilio クレジットが提供されます。 この Twilio クレジットは、任意の Twilio 使用に対して利用できます。$10 のクレジットは、約 1,000 件の SMS メッセージの送信、または最大で 1,000 分の受信音声に相当します (ご利用の電話番号の場所と、メッセージまたは通話の相手の場所に応じて異なります)。 この Twilio クレジットを利用するには、[https://ahoy.twilio.com/azure](https://ahoy.twilio.com/azure) にアクセスします。

Twilio は、従量課金制サービスです。 セットアップ料金は不要で、いつでもアカウントを閉じることができます。 詳細については、 [Twilio の料金のページ][twilio_pricing]をご覧ください。

## <a name="concepts"></a><a id="Concepts"></a>概念
Twilio API は、アプリケーションに音声および SMS 機能を提供する REST ベースの API です。 クライアント ライブラリは複数の言語で使用できます。一覧については、[Twilio API ライブラリ][twilio_libraries]に関するページを参照してください。

Twilio API の主要な側面として、Twilio 動詞と Twilio Markup Language (TwiML) が挙げられます。

### <a name="twilio-verbs"></a><a id="Verbs"></a>Twilio 動詞
API では、Twilio 動詞を使用します。たとえば、 **&lt;Say&gt;** 動詞は、メッセージを音声で返すことを Twilio に指示します。

Twilio 動詞の一覧を次に示します。 他の動詞と機能については、 [Twilio Markup Language のドキュメント](https://www.twilio.com/docs/api/twiml)を参照してください。

* **&lt;Dial&gt;** : 呼び出し元を別の電話に接続します。
* **&lt;Gather&gt;** : 電話キーパッドに入力された数字を収集します。
* **&lt;Hangup&gt;** : 通話を終了します。
* **&lt;Play&gt;** : 音声ファイルを再生します。
* **&lt;Pause&gt;** : 何も行わずに指定された秒数待機します。
* **&lt;Record&gt;** : 呼び出し元の声を録音し、声が録音されたファイルの URL を返します。
* **&lt;Redirect&gt;** : 通話または SMS の制御を別の URL の TwiML に転送します。
* **&lt;Reject&gt;** : Twilio 番号への着信通話を拒否します。課金はされません。
* **&lt;Say&gt;** : テキストを音声に変換して返します。
* **&lt;Sms&gt;** : SMS メッセージを送信します。

### <a name="twiml"></a><a id="TwiML"></a>TwiML
TwiML は、Twilio 動詞に基づいた XML ベースの命令のセットで、通話または SMS をどのように処理するかを Twilio に通知します。

たとえば、次の TwiML は、テキスト **Hello World** を音声に変換します。

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<Response>
    <Say>Hello World</Say>
</Response>
```

アプリケーションで Twilio API を呼び出す場合は、API パラメーターの 1 つである URL によって TwiML 応答が返されます。 開発用には、Twilio から提供される URL を使用して、アプリケーションで使用する TwiML 応答を提供することができます。 また、独自に URL をホストして、TwiML 応答を生成することもできます。別のオプションとして、**TwiMLResponse** オブジェクトを使用することもできます。

Twilio 動詞、その属性、および TwiML の詳細については、[TwiML][twiml] に関するページを参照してください。 Twilio API の詳細については、[Twilio API][twilio_api] に関するページを参照してください。

## <a name="create-a-twilio-account"></a><a id="CreateAccount"></a>Twilio アカウントを作成する
Twilio アカウントを取得する準備ができたら、[Twilio のサインアップ ページ][try_twilio]でサインアップします。 無料アカウントで始め、後でアカウントをアップグレードすることができます。

Twilio アカウントにサインアップすると、アカウント ID と認証トークンが発行されます。 Twilio API を呼び出すには、この両方が必要になります。 自分のアカウントが不正にアクセスされないように、認証トークンを安全に保管してください。 アカウント ID と認証トークンは、[Twilio アカウント ページ][twilio_account]の **[ACCOUNT SID]\(アカウント SID\)** フィールドと **[AUTH TOKEN]\(認証トークン\)** フィールドでそれぞれ確認できます。

## <a name="create-a-php-application"></a><a id="create_app"></a>PHP アプリケーションの作成
Twilio サービスを使用する Azure 上の PHP アプリケーションと、Twilio サービスを使用するその他の PHP アプリケーションに違いはありません。 Twilio サービスは REST ベースであり、PHP からいくつかの方法で呼び出すことができますが、この記事は [GitHub の PHP 用の Twilio ライブラリ][twilio_php]で Twilio サービスを使用する方法に重点を置いています。 PHP 用の Twilio ライブラリの使用の詳細については、[https://www.twilio.com/docs/libraries/php][twilio_lib_docs] を参照してください。

Twilio/PHP アプリケーションを構築して Azure にデプロイするための詳細な手順については、「[Azure 上の PHP アプリケーションで Twilio を使用して通話する方法][howto_phonecall_php]」を参照してください。

## <a name="configure-your-application-to-use-twilio-libraries"></a><a id="configure_app"></a>Twilio ライブラリを使用するアプリケーションの構成
PHP 用 Twilio ライブラリを使用するようにアプリケーションを構成するには、次の 2 つの方法があります。

1. GitHub ([https://github.com/twilio/twilio-php][twilio_php]) から PHP 用の Twilio ライブラリをダウンロードし、**Services** ディレクトリをアプリケーションに追加します。
   
    \- または -
2. PHP 用 Twilio ライブラリを PEAR パッケージとしてインストールします。 インストールには、次のコマンドを使用できます。

    ```bash
    $ pear channel-discover twilio.github.com/pear
    $ pear install twilio/Services_Twilio
    ```

PHP 用 Twilio ライブラリをインストールしたら、ライブラリを参照する **require_once** ステートメントを PHP ファイルの先頭に追加できます。

```php
require_once 'Services/Twilio.php';
```

詳細については、「[https://github.com/twilio/twilio-php/blob/master/README.md][twilio_github_readme]」を参照してください。

## <a name="how-to-make-an-outgoing-call"></a><a id="howto_make_call"></a>方法: 発信通話する
次のコードでは、**Services_Twilio** クラスを使用して発信通話を行う方法を示しています。 このコードは、Twilio から提供されるサイトも使用して、Twilio Markup Language (TwiML) 応答を返します。 コードを実行する前に、**From** および **To** の電話番号の値を置き換えて、Twilio アカウントの **From** の電話番号を確認します。

```php
// Include the Twilio PHP library.
require_once 'Services/Twilio.php';

// Library version.
$version = "2010-04-01";

// Set your account ID and authentication token.
$sid = "your_twilio_account_sid";
$token = "your_twilio_authentication_token";

// The number of the phone initiating the call.
$from_number = "NNNNNNNNNNN";

// The number of the phone receiving call.
$to_number = "NNNNNNNNNNN";

// Use the Twilio-provided site for the TwiML response.
$url = "https://twimlets.com/message";

// The phone message text.
$message = "Hello world.";

// Create the call client.
$client = new Services_Twilio($sid, $token, $version);

//Make the call.
try
{
    $call = $client->account->calls->create(
        $from_number, 
        $to_number,
        $url.'?Message='.urlencode($message)
    );
}
catch (Exception $e) 
{
    echo 'Error: ' . $e->getMessage();
}
```

既に説明したように、このコードは Twilio から提供されるサイトを使用して、TwiML 応答を返します。 代わりに独自のサイトを使用して TwiML 応答を返すことができます。詳細については、「[方法: 独自の Web サイトから TwiML 応答を返す](#howto_provide_twiml_responses)」を参照してください。

* **注**: TLS/SSL 証明書の検証エラーをトラブルシューティングするには、[https://www.twilio.com/docs/api/errors][ssl_validation] を参照してください 

## <a name="how-to-send-an-sms-message"></a><a id="howto_send_sms"></a>方法: SMS メッセージを送信する
次のコードでは、**Services_Twilio** クラスを使用して SMS メッセージを送信する方法を示しています。 試用アカウントで SMS メッセージを送信できるように、 **From** の番号が Twilio から提供されます。 コードを実行する前に、Twilio アカウントの **To** の番号を確認する必要があります。

```php
// Include the Twilio PHP library.
require_once 'Services/Twilio.php';

// Library version.
$version = "2010-04-01";

// Set your account ID and authentication token.
$sid = "your_twilio_account_sid";
$token = "your_twilio_authentication_token";


$from_number = "NNNNNNNNNNN"; // With trial account, texts can only be sent from your Twilio number.
$to_number = "NNNNNNNNNNN";
$message = "Hello world.";

// Create the call client.
$client = new Services_Twilio($sid, $token, $version);

// Send the SMS message.
try
{
    $client->$client->account->messages->sendMessage($from_number, $to_number, $message);
}
catch (Exception $e) 
{
    echo 'Error: ' . $e->getMessage();
}
```

## <a name="how-to-provide-twiml-responses-from-your-own-website"></a><a id="howto_provide_twiml_responses"></a>独自の Web サイトから TwiML 応答を返す
アプリケーションで Twilio API の呼び出しを開始すると、Twilio は TwiML 応答を返すことが想定されている URL にユーザーの要求を送信します。 上の例では、Twilio から提供される URL [https://twimlets.com/message][twimlet_message_url] を使用しています。 (TwiML は Twilio で使用するように設計されており、ブラウザーで表示できます。 たとえば、[https://twimlets.com/message][twimlet_message_url] をクリックすると、空の `<Response>` 要素が表示されます。別の例として、[https://twimlets.com/message?Message%5B0%5D=Hello%20World][twimlet_message_url_hello_world] をクリックすると、`<Say>` 要素を含む `<Response>` 要素が表示されます。)

Twilio から提供される URL を使用する代わりに、HTTP 応答を返す独自のサイトを作成できます。 XML 応答を返すサイトは任意の言語で作成できます。このトピックでは、PHP を使用して TwiML を作成するとします。

次の PHP ページでは、通話時の TwiML 応答で " **Hello World** " というテキストが読み上げられます。

```xml
<?php    
    header("content-type: text/xml");    
    echo "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n";
?>
<Response>    
    <Say>Hello world.</Say>
</Response>
```

この例からわかるように、TwiML 応答は XML ドキュメントにすぎません。 PHP 用 Twilio ライブラリには、TwiML を生成するクラスが用意されています。 次の例では、前の例と同じ応答が生成されますが、PHP 用 Twilio ライブラリの **Services\_Twilio\_Twiml** クラスを使用しています。

```php
require_once('Services/Twilio.php');

$response = new Services_Twilio_Twiml();
$response->say("Hello world.");
print $response;
```

TwiML の詳細については、[https://www.twilio.com/docs/api/twiml][twiml_reference] を参照してください。 

TwiML 応答を返すように PHP ページを設定したら、その PHP ページの URL を、`Services_Twilio->account->calls->create` メソッドに渡します。 たとえば、Azure ホステッド サービスにデプロイされた **MyTwiML** という名前の Web アプリケーションがあるとします。その PHP ページの名前が **mytwiml.php** である場合、次の例に示すように URL を **Services_Twilio->account->calls->create** に渡すことができます。

```php
require_once 'Services/Twilio.php';

$sid = "your_twilio_account_sid";
$token = "your_twilio_authentication_token";
$from_number = "NNNNNNNNNNN";
$to_number = "NNNNNNNNNNN";
$url = "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.php";

// The phone message text.
$message = "Hello world.";

$client = new Services_Twilio($sid, $token, "2010-04-01");

try
{
    $call = $client->account->calls->create(
        $from_number, 
        $to_number,
        $url.'?Message='.urlencode($message)
    );
}
catch (Exception $e) 
{
    echo 'Error: ' . $e->getMessage();
}
```

Azure の PHP での Twilio の使用の詳細については、「[Azure 上の PHP アプリケーションで Twilio を使用して通話する方法][howto_phonecall_php]」を参照してください。

## <a name="how-to-use-additional-twilio-services"></a><a id="AdditionalServices"></a>方法: その他の Twilio サービスを使用する
ここに示す例以外にも、Twilio が提供する Web ベースの API を使用して、Azure アプリケーションからその他の Twilio 機能を利用することができます。 詳細については、[Twilio API に関するドキュメント][twilio_api_documentation]を参照してください。

## <a name="next-steps"></a><a id="NextSteps"></a>次のステップ
これで、Twilio サービスの基本を学習できました。さらに詳細な情報が必要な場合は、次のリンク先をご覧ください。

* [Twilio のセキュリティ ガイドライン][twilio_security_guidelines]
* [Twilio のハウツー ガイドとコード例][twilio_howtos]
* [Twilio のクイックスタート チュートリアル][twilio_quickstarts] 
* [GitHub 上の Twilio に関するページ][twilio_on_github]
* [Twilio に関するサポートへの連絡のページ][twilio_support]

[twilio_php]: https://github.com/twilio/twilio-php
[twilio_lib_docs]: https://www.twilio.com/docs/libraries/php
[twilio_github_readme]: https://github.com/twilio/twilio-php/blob/master/README.md
[ssl_validation]: https://www.twilio.com/docs/api/errors
[twilio_api_service]: https://api.twilio.com
[howto_phonecall_php]: partner-twilio-php-make-phone-call.md
[twilio_voice_request]: https://www.twilio.com/docs/api/twiml/twilio_request
[twilio_sms_request]: https://www.twilio.com/docs/api/twiml/sms/twilio_request
[misc_role_config_settings]: /previous-versions/azure/hh690945(v=azure.100)
[twimlet_message_url]: https://twimlets.com/message
[twimlet_message_url_hello_world]: https://twimlets.com/message?Message%5B0%5D=Hello%20World
[twiml_reference]: https://www.twilio.com/docs/api/twiml
[twilio_pricing]: https://www.twilio.com/pricing
[special_offer]: https://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api]: https://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/user/account
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_api_documentation]: https://www.twilio.com/api
[twilio_security_guidelines]: https://www.twilio.com/docs/security
[twilio_howtos]: https://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: https://www.twilio.com/help/contact
[twilio_quickstarts]: https://www.twilio.com/docs/quickstart