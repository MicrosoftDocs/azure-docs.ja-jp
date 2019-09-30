---
title: Twilio を使用して音声通話と SMS を実行する方法 (Python) - Microsoft Docs
description: Azure で Twilio API サービスを使用して通話や SMS メッセージの送信を行う方法について学習します。 コード サンプルは Python で記述されています。
services: ''
documentationcenter: python
author: georgewallace
ms.assetid: 561bc75b-4ac4-40ba-bcba-48e901f27cc3
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 02/19/2015
ms.author: gwallace
ms.openlocfilehash: 3b9022dd2b5998f95179ee3dfabe6706b55036bc
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2019
ms.locfileid: "69636009"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-python"></a>Twilio を使用して音声通話と SMS を実行する方法 (Python)
このガイドでは、Azure の Twilio API サービスを使用して一般的なプログラミング タスクを実行する方法を紹介します。 電話の発信と Short Message Service (SMS) メッセージの送信の各シナリオについて説明します。 Twilio の詳細、およびアプリケーションで音声と SMS を使用する方法については、「 [次のステップ](#NextSteps) 」を参照してください。

## <a id="WhatIs"></a>Twilio とは
Twilio は、開発者がアプリケーションに音声、VoIP、およびメッセージングを埋め込むことを可能にし、ビジネス コミュニケーションを強化していきます。 必要なすべてのインフラストラクチャをクラウド ベースのグローバル環境で仮想化し、Twilio 通信 API プラットフォームを通じてそれを公開します。 アプリケーションは構築しやすく、スケーラビリティも優れています。 従量課金制の柔軟性と、クラウドの信頼性の利点を活用できます。

**Twilio Voice** を使用すると、アプリケーションで音声通話の発着信処理を行うことができます。
**Twilio SMS** を使用すると、アプリケーションでテキスト メッセージの送受信を行うことができます。
**Twilio Client** では、任意の電話、タブレット、またはブラウザーから VoIP 通話を行うことができ、WebRTC がサポートされています。

## <a id="Pricing"></a>Twilio の料金および特別プラン
Azure のお客様には、Twilio アカウントをアップグレードするときに、[特別プラン][special_offer]として 10 ドルの Twilio クレジットが提供されます。 この Twilio クレジットは、任意の Twilio 使用に対して利用できます。$10 のクレジットは、約 1,000 件の SMS メッセージの送信、または最大で 1,000 分の受信音声に相当します (ご利用の電話番号の場所と、メッセージまたは通話の相手の場所に応じて異なります)。 この [Twilio クレジット][special_offer]を利用して開始します。

Twilio は、従量課金制サービスです。 セットアップ料金は不要で、いつでもアカウントを閉じることができます。 詳細については、 [Twilio の料金のページ][twilio_pricing]をご覧ください。

## <a id="Concepts"></a>概念
Twilio API は、アプリケーションに音声および SMS 機能を提供する REST ベースの API です。 クライアント ライブラリは複数の言語で使用できます。一覧については、[Twilio API ライブラリ][twilio_libraries]に関するページを参照してください。

Twilio API の主要な側面として、Twilio 動詞と Twilio Markup Language (TwiML) が挙げられます。

### <a id="Verbs"></a>Twilio 動詞
API では、Twilio 動詞を使用します。たとえば、 **&lt;Say&gt;** 動詞は、メッセージを音声で返すことを Twilio に指示します。

Twilio 動詞の一覧を次に示します。 他の動詞と機能については、 [Twilio Markup Language のドキュメント][twiml]を参照してください。

* **&lt;Dial&gt;** : 呼び出し元を別の電話に接続します。
* **&lt;Gather&gt;** : 電話キーパッドで入力された数字を収集します。
* **&lt;Hangup&gt;** : 通話を終了します。
* **&lt;Pause&gt;** :指定された秒数だけ静かに待ちます。
* **&lt;Play&gt;** :オーディオ ファイルを再生します。
* **&lt;Queue&gt;** :呼び出し元のキューに追加します。
* **&lt;Record&gt;** :呼び出し元の声を録音し、声が録音されたファイルの URL を返します。
* **&lt;Redirect&gt;** :通話または SMS の制御を別の URL に存在する TwiML に転送します。
* **&lt;Reject&gt;** :Twilio 番号への受信通話を、課金することなく拒否します。
* **&lt;Say&gt;** :テキストを通話で流れる音声に変換します。
* **&lt;Sms&gt;** : SMS メッセージを送信します。

### <a id="TwiML"></a>TwiML
TwiML は、Twilio 動詞に基づいた XML ベースの命令のセットで、通話または SMS をどのように処理するかを Twilio に通知します。

たとえば、次の TwiML は、テキスト **Hello World** を音声に変換します。

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
      <Say>Hello World</Say>
    </Response>

アプリケーションで Twilio API を呼び出す場合は、API パラメーターの 1 つである URL によって TwiML 応答が返されます。 開発用には、Twilio から提供される URL を使用して、アプリケーションで使用する TwiML 応答を提供することができます。 また、独自に URL をホストして、TwiML 応答を生成することもできます。別のオプションとして、`TwiMLResponse` オブジェクトを使用することもできます。

Twilio 動詞、その属性、および TwiML の詳細については、[TwiML][twiml] に関するページを参照してください。 Twilio API の詳細については、[Twilio API][twilio_api] に関するページを参照してください。

## <a id="CreateAccount"></a>Twilio アカウントを作成する
Twilio アカウントを取得する準備ができたら、[Twilio のサインアップ ページ][try_twilio]でサインアップします。 無料アカウントで始め、後でアカウントをアップグレードすることができます。

Twilio アカウントにサインアップすると、アカウント SID と認証トークンが発行されます。 Twilio API を呼び出すには、この両方が必要になります。 自分のアカウントが不正にアクセスされないように、認証トークンを安全に保管してください。 アカウント SID と認証トークンは、[Twilio Console][twilio_console] の、それぞれ **[ACCOUNT SID]** と **[AUTH TOKEN]** というラベルが付いたフィールドで表示できます。

## <a id="create_app"></a>Python アプリケーションを作成する
Twilio サービスを利用し、Azure 上で実行される Python アプリケーションは、Twilio サービスを利用するその他の Python アプリケーションと何ら違いはありません。 Twilio サービスは REST ベースであり、Python からいくつかの方法で呼び出すことができますが、この記事は [GitHub の Python 用の Twilio ライブラリ][twilio_python]で Twilio サービスを使用する方法に重点を置いています。 Python 用の Twilio ライブラリの使用の詳細については、[https://www.twilio.com/docs/libraries/python][twilio_lib_docs] を参照してください。

まず、新しい Python Web アプリケーションのホストとして動作する、新しい Azure Linux VM をセットアップします。 仮想マシンの実行が開始したら、次に説明するように、アプリケーションをパブリック ポートで公開する必要があります。

### <a name="add-an-incoming-rule"></a>受信ルールの追加
  1. [ネットワーク セキュリティ グループ] ページに移動します。
  2. 仮想マシンに対応するネットワーク セキュリティ グループを選択します。
  3. **ポート 80** の **[Outgoing Rule (発信ルール)]** を追加します。 すべてのアドレスからの着信を許可してください。

### <a name="set-the-dns-name-label"></a>DNS 名のラベルの設定
  1. [パブリック IP アドレス][azure_ips] ページに移動します。
  2. 目的の仮想マシンに対応するパブリック IP を選択します。
  3. **[構成]** セクションで **[DNS 名ラベル]** を設定します。 この例では、*your-domain-label*.centralus.cloudapp.azure.com のようになります。

SSH から仮想マシンに接続できるようになると、任意の Web フレームワークをインストールできます (Python でよく知られている Web フレームワークは [Flask](http://flask.pocoo.org/) や [Django](https://www.djangoproject.com) です)。 いずれも `pip install` コマンドを実行するだけでインストールできます。

仮想マシンがポート 80 のみでトラフィックを許可するように構成したことに留意してください。 つまり、このポートを使用するようにアプリケーションを構成します。

## <a id="configure_app"></a>Twilio ライブラリを使用するアプリケーションの構成
Python 用 Twilio ライブラリを使用するようにアプリケーションを構成するには、次の 2 つの方法があります。

* Pip パッケージとして Python 用 Twilio ライブラリをインストールします。 インストールには、次のコマンドを使用できます。
   
        $ pip install twilio

    \- または -

* GitHub ([https://github.com/twilio/twilio-python][twilio_python]) から Python 用の Twilio ライブラリをダウンロードし、それを次のようにインストールします。

        $ python setup.py install

Python 用 Twilio ライブラリをインストールしたら、Python ファイルで `import` を行うことができます。

        import twilio

詳細については、「[twilio_github_readme](https://github.com/twilio/twilio-python/blob/master/README.rst)」を参照してください。

## <a id="howto_make_call"></a>方法:発信通話する
次のコードでは、発信通話を行う方法を示します。 このコードは、Twilio から提供されるサイトも使用して、Twilio Markup Language (TwiML) 応答を返します。 コードを実行する前に、**from_number** および **to_number** の電話番号の値を置き換えて、Twilio アカウントの **from_number** の電話番号を確認します。

    from urllib.parse import urlencode

    # Import the Twilio Python Client.
    from twilio.rest import TwilioRestClient

    # Set your account ID and authentication token.
    account_sid = "your_twilio_account_sid"
    auth_token = "your_twilio_authentication_token"

    # The number of the phone initiating the call.
    # This should either be a Twilio number or a number that you've verified
    from_number = "NNNNNNNNNNN"

    # The number of the phone receiving call.
    to_number = "NNNNNNNNNNN"

    # Use the Twilio-provided site for the TwiML response.
    url = "https://twimlets.com/message?"

    # The phone message text.
    message = "Hello world."

    # Initialize the Twilio client.
    client = TwilioRestClient(account_sid, auth_token)

    # Make the call.
    call = client.calls.create(to=to_number,
                               from_=from_number,
                               url=url + urlencode({'Message': message}))
    print(call.sid)

既に説明したように、このコードは Twilio から提供されるサイトを使用して、TwiML 応答を返します。 代わりに独自のサイトを使用して TwiML 応答を返すことができます。詳細については、「[方法: 独自の Web サイトから TwiML 応答を返す](#howto_provide_twiml_responses)」を参照してください。

## <a id="howto_send_sms"></a>方法:SMS メッセージを送信する
次のコードは、`TwilioRestClient` クラスを使用して SMS メッセージを送信する方法を示しています。 試用アカウントで SMS メッセージを送信できるように、**from_number** の番号が Twilio から提供されます。 コードを実行する前に、Twilio アカウントの **to_number** の番号を確認する必要があります。

    # Import the Twilio Python Client.
    from twilio.rest import TwilioRestClient

    # Set your account ID and authentication token.
    account_sid = "your_twilio_account_sid"
    auth_token = "your_twilio_authentication_token"

    from_number = "NNNNNNNNNNN"  # With trial account, texts can only be sent from your Twilio number.
    to_number = "NNNNNNNNNNN"
    message = "Hello world."

    # Initialize the Twilio client.
    client = TwilioRestClient(account_sid, auth_token)

    # Send the SMS message.
    message = client.messages.create(to=to_number,
                                     from_=from_number,
                                     body=message)

## <a id="howto_provide_twiml_responses"></a>方法:独自の Web サイトから TwiML 応答を返す
アプリケーションで Twilio API の呼び出しを開始すると、Twilio は TwiML 応答を返すことが想定されている URL にユーザーの要求を送信します。 上の例では、Twilio から提供される URL [https://twimlets.com/message][twimlet_message_url] を使用しています。 (TwiML は Twilio で使用するように設計されており、ブラウザーで表示できます。 たとえば、[https://twimlets.com/message][twimlet_message_url] をクリックすると、空の `<Response>` 要素が表示されます。別の例として、[https://twimlets.com/message?Message%5B0%5D=Hello%20World][twimlet_message_url_hello_world] をクリックすると、`<Say>` 要素を含む `<Response>` 要素が表示されます。)

Twilio から提供される URL を使用する代わりに、HTTP 応答を返す独自のサイトを作成できます。 XML 応答を返すサイトは任意の言語で作成できます。このトピックでは、Python を使用して TwiML を作成するとします。

次の例では、通話時の TwiML 応答で " **Hello World** " というテキストが読み上げられます。

Flask の場合:

    from flask import Response
    @app.route("/")
    def hello():
        xml = '<Response><Say>Hello world.</Say></Response>'
        return Response(xml, mimetype='text/xml')

Django の場合:

    from django.http import HttpResponse
    def hello(request):
        xml = '<Response><Say>Hello world.</Say></Response>'
        return HttpResponse(xml, content_type='text/xml')

この例からわかるように、TwiML 応答は XML ドキュメントにすぎません。 Python 用 Twilio ライブラリには、TwiML を生成するクラスが用意されています。 次の例では、前の例と同じ応答が生成されますが、Python 用 Twilio ライブラリの `twiml` モジュールを使用しています。

    from twilio import twiml

    response = twiml.Response()
    response.say("Hello world.")
    print(str(response))

TwiML の詳細については、[https://www.twilio.com/docs/api/twiml][twiml_reference] を参照してください。

TwiML 応答を返すように Python アプリケーションを設定したら、その Python アプリケーションの URL を、`client.calls.create` メソッドに渡します。 たとえば、**MyTwiML** という名前の Web アプリケーションを、Azure でホストされるサービスにデプロイした場合、次の例のようにその URL を webhook として使用できます。

    from twilio.rest import TwilioRestClient

    account_sid = "your_twilio_account_sid"
    auth_token = "your_twilio_authentication_token"
    from_number = "NNNNNNNNNNN"
    to_number = "NNNNNNNNNNN"
    url = "http://your-domain-label.centralus.cloudapp.azure.com/MyTwiML/"

    # Initialize the Twilio client.
    client = TwilioRestClient(account_sid, auth_token)

    # Make the call.
    call = client.calls.create(to=to_number,
                               from_=from_number,
                               url=url)
    print(call.sid)

## <a id="AdditionalServices"></a>方法:その他の Twilio サービスを使用する
ここに示す例以外にも、Twilio が提供する Web ベースの API を使用して、Azure アプリケーションからその他の Twilio 機能を利用することができます。 詳細については、[Twilio API のドキュメント][twilio_api]を参照してください。

## <a id="NextSteps"></a>次のステップ
これで、Twilio サービスの基本を学習できました。さらに詳細な情報が必要な場合は、次のリンク先を参照してください。

* [Twilio のセキュリティ ガイドライン][twilio_security_guidelines]
* [Twilio のハウツー ガイドとコード例][twilio_howtos]
* [Twilio のクイックスタート チュートリアル][twilio_quickstarts]
* [GitHub 上の Twilio][twilio_on_github]
* [Twilio サポートへの問い合わせ][twilio_support]

[special_offer]: https://ahoy.twilio.com/azure
[twilio_python]: https://github.com/twilio/twilio-python
[twilio_lib_docs]: https://www.twilio.com/docs/libraries/python
[twilio_github_readme]: https://github.com/twilio/twilio-python/blob/master/README.md

[twimlet_message_url]: https://twimlets.com/message
[twimlet_message_url_hello_world]: https://twimlets.com/message?Message%5B0%5D=Hello%20World
[twiml_reference]: https://www.twilio.com/docs/api/twiml
[twilio_pricing]: https://www.twilio.com/pricing

[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api]: https://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_console]:  https://www.twilio.com/console
[twilio_security_guidelines]: https://www.twilio.com/docs/security
[twilio_howtos]: https://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: https://www.twilio.com/help/contact
[twilio_quickstarts]: https://www.twilio.com/docs/quickstart
