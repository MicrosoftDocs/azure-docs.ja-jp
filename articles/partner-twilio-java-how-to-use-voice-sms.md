---
title: 音声と SMS に Twilio を使用する方法 (Java) | Microsoft Docs
description: Azure で Twilio API サービスを使用して通話や SMS メッセージの送信を行う方法について学習します。 コード サンプルは Java で記述されています。
services: ''
documentationcenter: java
author: georgewallace
ms.assetid: f3508965-5527-4255-9d51-5d5f926f4d43
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.custom: devx-track-java
ms.openlocfilehash: e7ff925534a9cab6523097f84a8ae199e9cb83eb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100548728"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-java"></a>Java で音声および SMS 機能に Twilio を使用する方法
このガイドでは、Azure の Twilio API サービスを使用して一般的なプログラミング タスクを実行する方法を紹介します。 電話の発信と Short Message Service (SMS) メッセージの送信の各シナリオについて説明します。 Twilio の詳細、およびアプリケーションで音声と SMS を使用する方法については、「 [次のステップ](#NextSteps) 」を参照してください。

## <a name="what-is-twilio"></a><a id="WhatIs"></a>Twilio とは
Twilio は、既存の Web 言語およびスキルを使用して音声および SMS アプリケーションの作成を可能にするテレフォニー Web サービス API です。 Twilio は、サードパーティ製のサービスです (Azure の機能および Microsoft 製品ではありません)。

**Twilio Voice** を使用すると、アプリケーションで音声通話の発着信処理を行うことができます。 **Twilio SMS** を使用すると、アプリケーションで SMS メッセージの送受信を行うことができます。 **Twilio Client** を使用すると、アプリケーションに (モバイル接続を含む) 既存のインターネット接続を使用した音声通信を組み込むことができます。

## <a name="twilio-pricing-and-special-offers"></a><a id="Pricing"></a>Twilio の料金および特別プラン
Twilio の料金に関する情報は、[Twilio の料金][twilio_pricing]に関するページで入手できます。 Azure のお客様には、[特別プラン][special_offer]として 1,000 件のテキストまたは 1,000 分の着信通話の無料クレジットが提供されます。 このプランにサインアップするか、または詳細情報を取得するには、[https://ahoy.twilio.com/azure][special_offer] を参照してください。

## <a name="concepts"></a><a id="Concepts"></a>概念
Twilio API は、アプリケーションに音声および SMS 機能を提供する REST ベースの API です。 クライアント ライブラリは複数の言語で使用できます。一覧については、[Twilio API ライブラリ][twilio_libraries]に関するページを参照してください。

Twilio API の主要な側面として、Twilio 動詞と Twilio Markup Language (TwiML) が挙げられます。

### <a name="twilio-verbs"></a><a id="Verbs"></a>Twilio 動詞
API では、Twilio 動詞を使用します。たとえば、 **&lt;Say&gt;** 動詞は、メッセージを音声で返すことを Twilio に指示します。

Twilio 動詞の一覧を次に示します。

* **&lt;Dial&gt;** : 呼び出し元を別の電話に接続します。
* **&lt;Gather&gt;** : 電話キーパッドに入力された数字を収集します。
* **&lt;Hangup&gt;** : 通話を終了します。
* **&lt;Play&gt;** : 音声ファイルを再生します。
* **&lt;Queue&gt;** : 呼び出し元のキューに追加します。
* **&lt;Pause&gt;** : 何も行わずに指定された秒数待機します。
* **&lt;Record&gt;** : 呼び出し元の声を録音し、声が録音されたファイルの URL を返します。
* **&lt;Redirect&gt;** : 通話または SMS の制御を別の URL の TwiML に転送します。
* **&lt;Reject&gt;** : Twilio 番号への着信通話を拒否します。課金はされません。
* **&lt;Say&gt;** : テキストを音声に変換して返します。
* **&lt;Sms&gt;** : SMS メッセージを送信します。

### <a name="twiml"></a><a id="TwiML"></a>TwiML
TwiML は、Twilio 動詞に基づいた XML ベースの命令のセットで、通話または SMS をどのように処理するかを Twilio に通知します。

たとえば、次の TwiML は、テキスト **Hello World!** を 音声に変換します。

```xml
    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World!</Say>
    </Response>
```

アプリケーションで Twilio API を呼び出す場合は、API パラメーターの 1 つである URL によって TwiML 応答が返されます。 開発用には、Twilio から提供される URL を使用して、アプリケーションで使用する TwiML 応答を提供することができます。 また、独自に URL をホストして、TwiML 応答を生成することもできます。別のオプションとして、**TwiMLResponse** オブジェクトを使用することもできます。

Twilio 動詞、その属性、および TwiML の詳細については、[TwiML][twiml] に関するページを参照してください。 Twilio API の詳細については、[Twilio API][twilio_api] に関するページを参照してください。

## <a name="create-a-twilio-account"></a><a id="CreateAccount"></a>Twilio アカウントを作成する
Twilio アカウントを取得する準備ができたら、[Twilio のサインアップ ページ][try_twilio]でサインアップします。 無料アカウントで始め、後でアカウントをアップグレードすることができます。

Twilio アカウントにサインアップすると、アカウント ID と認証トークンが発行されます。 Twilio API を呼び出すには、この両方が必要になります。 自分のアカウントが不正にアクセスされないように、認証トークンを安全に保管してください。 アカウント ID と認証トークンは、[Twilio Console][twilio_console] の、それぞれ **[ACCOUNT SID]** と **[AUTH TOKEN]** というラベルが付いたフィールドで表示できます。

## <a name="create-a-java-application"></a><a id="create_app"></a>Java アプリケーションの作成
1. Twilio JAR を入手し、Java のビルド パスと WAR デプロイ アセンブリに追加します。 [https://github.com/twilio/twilio-java][twilio_java] では、GitHub ソースをダウンロードして独自の JAR を作成するか、またはビルド済み JAR (依存関係あり、またはなし) をダウンロードできます。
2. JDK の **cacerts** キーストアに Equifax Secure Certificate Authority 証明書と MD5 フィンガープリント 67:CB:9D:C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 が格納されていることを確認します (シリアル番号は 35:DE:F4:CF、SHA1 フィンガープリントは D2:32:09:AD:23:D3:14:23:21:74:E4:0D:7F:9D:62:13:97:86:63:3A)。 これは [https://api.twilio.com][twilio_api_service] サービスの証明機関 (CA) 証明書であり、Twilio API を使用するときに呼び出されます。

Java 用 Twilio クライアント ライブラリを使用する手順の詳細については、「[Azure 上の Java アプリケーションで Twilio を使用して通話する方法][howto_phonecall_java]」で確認できます。

## <a name="configure-your-application-to-use-twilio-libraries"></a><a id="configure_app"></a>Twilio ライブラリを使用するアプリケーションの構成
コードのソース ファイルの先頭に、アプリケーションで使用する Twilio のパッケージまたはクラスの **import** ステートメントを追加できます。

Java ソース ファイルの場合:

```java
    import com.twilio.*;
    import com.twilio.rest.api.*;
    import com.twilio.type.*;
    import com.twilio.twiml.*;
```

Java Server Page (JSP) ソース ファイルの場合:

```java
    import="com.twilio.*"
    import="com.twilio.rest.api.*"
    import="com.twilio.type.*"
    import="com.twilio.twiml.*"
```

使用する Twilio のパッケージまたはクラスによって **import** ステートメントは異なる場合があります。

## <a name="how-to-make-an-outgoing-call"></a><a id="howto_make_call"></a>方法: 発信通話する
次のコードでは、 **Call** クラスを使用して発信通話を行う方法を示しています。 このコードは、Twilio から提供されるサイトも使用して、Twilio Markup Language (TwiML) 応答を返します。 コードを実行する前に、**from** および **to** の電話番号の値を置き換えて、Twilio アカウントの **from** の電話番号を確認します。

```java
    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account_SID";
    String authToken = "your_twilio_authentication_token";

    // Initialize the Twilio client.
    Twilio.init(accountSID, authToken);

    // Use the Twilio-provided site for the TwiML response.
    URI uri = new URI("https://twimlets.com/message" +
            "?Message%5B0%5D=Hello%20World%21");

    // Declare To and From numbers
    PhoneNumber to = new PhoneNumber("NNNNNNNNNN");
    PhoneNumber from = new PhoneNumber("NNNNNNNNNN");

    // Create a Call creator passing From, To and URL values
    // then make the call by executing the create() method
    Call.creator(to, from, uri).create();
```

**Call.creator** メソッドに渡されるパラメーターの詳細については、[https://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls] を参照してください。

既に説明したように、このコードは Twilio から提供されるサイトを使用して、TwiML 応答を返します。 代わりに独自のサイトを使用して TwiML 応答を返すことができます。詳細については、「[方法: 独自の Web サイトから TwiML 応答を返す](#howto_provide_twiml_responses)」を参照してください。

## <a name="how-to-send-an-sms-message"></a><a id="howto_send_sms"></a>方法: SMS メッセージを送信する
次のコードは、**Message** クラスを使用して SMS メッセージを送信する方法を示しています。 試用アカウントで SMS メッセージを送信できるように、**from** の番号として **4155992671** が Twilio から提供されます。 コードを実行する前に、Twilio アカウントの **to** の番号を確認する必要があります。

```java
    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account_SID";
    String authToken = "your_twilio_authentication_token";

    // Initialize the Twilio client.
    Twilio.init(accountSID, authToken);

    // Declare To and From numbers and the Body of the SMS message
    PhoneNumber to = new PhoneNumber("+14159352345"); // Replace with a valid phone number for your account.
    PhoneNumber from = new PhoneNumber("+14158141829"); // Replace with a valid phone number for your account.
    String body = "Where's Wallace?";

    // Create a Message creator passing From, To and Body values
    // then send the SMS message by calling the create() method
    Message sms = Message.creator(to, from, body).create();
```

**Message.creator** メソッドに渡されるパラメーターの詳細については、[https://www.twilio.com/docs/api/rest/sending-sms][twilio_rest_sending_sms] を参照してください。

## <a name="how-to-provide-twiml-responses-from-your-own-website"></a><a id="howto_provide_twiml_responses"></a>独自の Web サイトから TwiML 応答を返す
アプリケーションで Twilio API の呼び出しをインスタンス化する場合 (たとえば、**CallCreator.create** メソッドを使用した場合)、Twilio は TwiML 応答を返すことが想定されている URL にユーザーの要求を送信します。 上の例では、Twilio から提供される URL [https://twimlets.com/message][twimlet_message_url] を使用しています。 (TwiML は Web サービスで使用するように設計されており、ブラウザーで表示できます。 たとえば、[https://twimlets.com/message][twimlet_message_url] をクリックすると、空の **&lt; Response&gt;** 要素が表示されます。別の例として、[https://twimlets.com/message?Message%5B0%5D=Hello%20World%21][twimlet_message_url_hello_world] をクリックすると、 **&lt; Say&gt;** 要素を含む **&lt; Response&gt;** 要素が表示されます。)

Twilio から提供される URL を使用する代わりに、HTTP 応答を返す独自の URL サイトを作成できます。 HTTP 応答を返すサイトは任意の言語で作成できます。このトピックでは、JSP ページで URL をホストするとします。

次の JSP ページでは、通話時の TwiML 応答で **Hello World!** というテキストが読み上げられます。

```xml
    <%@ page contentType="text/xml" %>
    <Response>
        <Say>Hello World!</Say>
    </Response>
```

次の JSP ページでは、複数の言葉と間合いのポーズを含む TwiML 応答が返され、Twilio API バージョンと Azure ロール名に関する情報が読み上げられます。

```xml
    <%@ page contentType="text/xml" %>
    <Response>
        <Say>Hello from Azure!</Say>
        <Pause></Pause>
        <Say>The Twilio API version is <%= request.getParameter("ApiVersion") %>.</Say>
        <Say>The Azure role name is <%= System.getenv("RoleName") %>.</Say>
        <Pause></Pause>
        <Say>Good bye.</Say>
    </Response>
```

**ApiVersion** パラメーターは、SMS 要求ではなく Twilio 音声要求に使用できます。 Twilio の音声と SMS の要求で使用可能な要求パラメーターを表示するには、<https://www.twilio.com/docs/api/twiml/twilio_request> と <https://www.twilio.com/docs/api/twiml/sms/twilio_request> をそれぞれ参照してください。 **RoleName** 環境変数は Azure デプロイの一部として使用できます (カスタム環境変数を追加して **System.getenv** から取得できるようにする場合は、[その他のロール構成設定に関するページ][misc_role_config_settings]の環境変数に関するセクションを参照してください)。

TwiML 応答を提供するように JSP ページを設定したら、**Call.creator** メソッドに渡される URL として JSP ページの URL を使用します。 たとえば、Azure ホステッド サービスにデプロイされた MyTwiML という名前の Web アプリケーションがあり、JSP ページの名前が mytwiml.jsp である場合、次のコード例に示すように URL を **Call.creator** に渡すことができます。

```java
    // Declare To and From numbers and the URL of your JSP page
    PhoneNumber to = new PhoneNumber("NNNNNNNNNN");
    PhoneNumber from = new PhoneNumber("NNNNNNNNNN");
    URI uri = new URI("http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.jsp");

    // Create a Call creator passing From, To and URL values
    // then make the call by executing the create() method
    Call.creator(to, from, uri).create();
```

TwiML で応答するための別の方法は **VoiceResponse** クラスを使用することです。このクラスは **com.twilio.twiml** パッケージに含まれています。

Azure で Java に基づいて Twilio を使用する方法の詳細については、「[Azure 上の Java アプリケーションで Twilio を使用して通話する方法][howto_phonecall_java]」を参照してください。

## <a name="how-to-use-additional-twilio-services"></a><a id="AdditionalServices"></a>方法: その他の Twilio サービスを使用する
ここに示す例以外にも、Twilio が提供する Web ベースの API を使用して、Azure アプリケーションからその他の Twilio 機能を利用することができます。 詳細については、[Twilio API に関するドキュメント][twilio_api_documentation]を参照してください。

## <a name="next-steps"></a><a id="NextSteps"></a>次のステップ
これで、Twilio サービスの基本を学習できました。さらに詳細な情報が必要な場合は、次のリンク先をご覧ください。

* [Twilio のセキュリティ ガイドライン][twilio_security_guidelines]
* [Twilio のハウツー ガイドとコード例][twilio_howtos]
* [Twilio のクイックスタート チュートリアル][twilio_quickstarts]
* [GitHub 上の Twilio に関するページ][twilio_on_github]
* [Twilio に関するサポートへの連絡のページ][twilio_support]

[twilio_java]: https://github.com/twilio/twilio-java
[twilio_api_service]: https://api.twilio.com
[howto_phonecall_java]: partner-twilio-java-phone-call-example.md
[misc_role_config_settings]: /previous-versions/azure/hh690945(v=azure.100)
[twimlet_message_url]: https://twimlets.com/message
[twimlet_message_url_hello_world]: https://twimlets.com/message?Message%5B0%5D=Hello%20World%21
[twilio_rest_making_calls]: https://www.twilio.com/docs/api/rest/making-calls
[twilio_rest_sending_sms]: https://www.twilio.com/docs/api/rest/sending-sms
[twilio_pricing]: https://www.twilio.com/pricing
[special_offer]: https://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api]: https://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_console]:  https://www.twilio.com/console
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified#
[twilio_api_documentation]: https://www.twilio.com/docs
[twilio_security_guidelines]: https://www.twilio.com/docs/security
[twilio_howtos]: https://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: https://www.twilio.com/help/contact
[twilio_quickstarts]: https://www.twilio.com/docs/quickstart
