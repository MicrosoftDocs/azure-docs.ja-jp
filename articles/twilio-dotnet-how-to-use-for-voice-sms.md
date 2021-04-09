---
title: 音声および SMS に Twilio を使用する方法 (.NET) | Microsoft Docs
description: Azure で Twilio API サービスを使用して通話や SMS メッセージの送信を行う方法について学習します。 コード サンプルは .NET で記述されています。
services: ''
documentationcenter: .net
author: georgewallace
ms.assetid: 74d4f3c9-f1cb-4968-b744-36b32cd0e834
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/24/2015
ms.author: gwallace
ms.custom: devx-track-dotnet
ms.openlocfilehash: 104f969f5e27ef36ad43eb10e19176a4bcfd6648
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96003763"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-from-azure"></a>Azure から音声および SMS 機能に Twilio を使用する方法
このガイドでは、Azure の Twilio API サービスを使用して一般的なプログラミング タスクを実行する方法を紹介します。 電話の発信と Short Message Service (SMS) メッセージの送信の各シナリオについて説明します。 Twilio の詳細とアプリケーションで音声と SMS を使用する方法については、「 [次の手順](#NextSteps) 」を参照してください。

## <a name="what-is-twilio"></a><a id="WhatIs"></a>Twilio とは
Twilio は、開発者がアプリケーションに音声、VoIP、およびメッセージングを埋め込むことを可能にし、ビジネス コミュニケーションを強化していきます。 必要なすべてのインフラストラクチャをクラウド ベースのグローバル環境で仮想化し、Twilio 通信 API プラットフォームを通じてそれを公開します。 アプリケーションは構築しやすく、スケーラビリティも優れています。 従量課金制の柔軟性と、クラウドの信頼性の利点を活用できます。

**Twilio Voice** を使用すると、アプリケーションで音声通話の発着信処理を行うことができます。 **Twilio SMS** を使用すると、アプリケーションで SMS メッセージの送受信を行うことができます。 **Twilio Client** では、任意の電話、タブレット、またはブラウザーから VoIP 通話を行うことができ、WebRTC がサポートされています。

## <a name="twilio-pricing-and-special-offers"></a><a id="Pricing"></a>Twilio の料金および特別プラン
Azure ユーザーには、[特別プラン](https://www.twilio.com/azure)として、Twilio アカウントをアップグレードする際に、$10 の Twilio クレジットが提供されます。 この Twilio クレジットは、任意の Twilio 使用に対して利用できます。$10 のクレジットは、約 1,000 件の SMS メッセージの送信、または最大で 1,000 分の受信音声に相当します (ご利用の電話番号の場所と、メッセージまたは通話の相手の場所に応じて異なります)。 この Twilio クレジットを引き換えるには、[twilio.com/azure](https://twilio.com/azure) にアクセスします。

Twilio は、従量課金制サービスです。 セットアップ料金は不要で、いつでもアカウントを閉じることができます。 詳細については、 [Twilio の料金のページ](https://www.twilio.com/voice/pricing)をご覧ください。

## <a name="concepts"></a><a id="Concepts"></a>概念
Twilio API は、アプリケーションに音声および SMS 機能を提供する REST ベースの API です。 クライアント ライブラリは複数の言語で使用できます。一覧については、[Twilio API ライブラリ][twilio_libraries]に関するページを参照してください。

Twilio API の主要な側面として、Twilio 動詞と Twilio Markup Language (TwiML) が挙げられます。

### <a name="twilio-verbs"></a><a id="Verbs"></a>Twilio 動詞
API では、Twilio 動詞を使用します。たとえば、 **&lt;Say&gt;** 動詞は、メッセージを音声で返すことを Twilio に指示します。

Twilio 動詞の一覧を次に示します。  他の動詞と機能については、 [Twilio Markup Language のドキュメント](https://www.twilio.com/docs/api/twiml)を参照してください。

* `<Dial>`: 呼び出し元を別の電話に接続します。
* `<Gather>`: 電話キーパッドで入力された数字を収集します。
* `<Hangup>`: 通話を終了します。
* `<Play>`: オーディオ ファイルを再生します。
* `<Pause>`: 指定された秒数だけ静かに待ちます。
* `<Record>`: 呼び出し元の音声を録音し、その録音を含むファイルの URL を返します。
* `<Redirect>`: 通話または SMS の制御を別の URL に存在する TwiML に転送します。
* `<Reject>`: Twilio 番号への受信通話を、課金することなく拒否します。
* `<Say>`: テキストを通話で流れる音声に変換します。
* `<Sms>`: SMS メッセージを送信します。

### <a name="twiml"></a>TwiML
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

## <a name="create-an-azure-application"></a><a id="create_app"></a>Azure アプリケーションの作成
Twilio 対応のアプリケーションをホストする Azure アプリケーションには、他の Azure アプリケーションとの違いはありません。 Twilio .NET ライブラリを追加し、その Twilio .NET ライブラリを使用するようにロールを構成します。
最初の Azure プロジェクトの作成に関する詳細については、[Visual Studio での Azure プロジェクトの作成][vs_project]に関するページを参照してください。

## <a name="configure-your-application-to-use-twilio-libraries"></a><a id="configure_app"></a>Twilio ライブラリを使用するアプリケーションの構成
Twilio は、一連の .NET ヘルパー ライブラリを提供します。このライブラリは、Twilio のさまざまな側面をラップし、Twilio REST API および Twilio Client と対話して TwiML 応答を生成するためのシンプルで簡単な方法を提供します。

Twilio は、.NET 開発者向けに次の 5 つのライブラリを用意しています。

| ライブラリ | 説明 |
| --- | --- |
| Twilio.API | 扱いやすい .NET ライブラリで Twilio REST API をラップする主要な Twilio ライブラリです。 このライブラリは、.NET、Silverlight、および Windows Phone 7 向けに用意されています。 |
| Twilio.TwiML | .NET 対応の方法で TwiML マークアップを生成します。 |
| Twilio.MVC | ASP.NET MVC を使用する開発者向けのこのライブラリは、TwilioController、TwiML ActionResult、および要求検証の属性を含んでいます。 |
| Twilio.WebMatrix | Microsoft が無償で提供する WebMatrix 開発ツールを使用する開発者向けのこのライブラリは、Twilio のさまざまな操作に対応する Razor 構文ヘルパーを含んでいます。 |
| Twilio.Client.Capability | Twilio Client JavaScript SDK と共に使用するための Capability トークン ジェネレーターを含んでいます。 |

> [!Important]
> すべてのライブラリに .NET 3.5、Silverlight 4、または Windows Phone 7 以降が必要です。

このガイドに記載した例では、Twilio.API ライブラリを使用します。

これらのライブラリは、Visual Studio 2010 から 2015 で利用可能な [NuGet パッケージ マネージャー拡張機能を使用してインストール](https://www.twilio.com/docs/csharp/install)できます。  ソース コードは [GitHub][twilio_github_repo] でホストされています。これには、ライブラリの使用に関するすべてのドキュメントを網羅した Wiki が含まれています。

Microsoft Visual Studio 2010 では既定で NuGet の Version 1.2 がインストールされます。 Twilio ライブラリをインストールするには、NuGet の Version 1.6 以降が必要です。 NuGet のインストールまたは更新については、[https://nuget.org/][nuget] を参照してください。

> [!NOTE]
> NuGet の最新バージョンをインストールするには、最初に Visual Studio 拡張機能マネージャーを使用して、読み込み済みのバージョンをアンインストールする必要があります。 そのためには、Visual Studio を管理者として実行する必要があります。 そのようにしない場合、[アンインストール] ボタンは無効です。
>
>

### <a name="to-add-the-twilio-libraries-to-your-visual-studio-project"></a><a id="use_nuget"></a>Twilio ライブラリを Visual Studio プロジェクトに追加するには、次の手順を実行します。
1. Visual Studio でソリューションを開きます。
2. **[参照]** を右クリックします。
3. **[NuGet パッケージの管理]**
4. **[オンライン]** をクリックします。
5. [オンライン検索] ボックスに、「 *twilio*」と入力します。
6. Twilio パッケージで **[インストール]** をクリックします。

## <a name="how-to-make-an-outgoing-call"></a><a id="howto_make_call"></a>方法: 発信通話する
次のコードでは、**CallResource** クラスを使用して、発信通話を行う方法を示しています。 このコードは、Twilio から提供されるサイトも使用して、Twilio Markup Language (TwiML) 応答を返します。 コードを実行する前に、**from** および **to** の電話番号の値を置き換えて、Twilio アカウントの **from** の電話番号を確認します。

```csharp
// Use your account SID and authentication token instead
// of the placeholders shown here.
const string accountSID = "your_twilio_account";
const string authToken = "your_twilio_authentication_token";

// Initialize the TwilioClient.
TwilioClient.Init(accountSID, authToken);

// Use the Twilio-provided site for the TwiML response.
var url = "https://twimlets.com/message";
url = $"{url}?Message%5B0%5D=Hello%20World";

// Set the call From, To, and URL values to use for the call.
// This sample uses the sandbox number provided by
// Twilio to make the call.
var call = CallResource.Create(
    to: new PhoneNumber("+NNNNNNNNNN"),
    from: new PhoneNumber("NNNNNNNNNN"),
    url: new Uri(url));
    }
```

**CallResource.Create** メソッドに渡されるパラメーターの詳細については、[https://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls] を参照してください。

既に説明したように、このコードは Twilio から提供されるサイトを使用して、TwiML 応答を返します。 代わりに独自のサイトを使用して TwiML 応答を返すこともできます。 詳細については、「[方法: 独自の Web サイトから TwiML 応答を返す](#howto_provide_twiml_responses)」を参照してください。

## <a name="how-to-send-an-sms-message"></a><a id="howto_send_sms"></a>方法: SMS メッセージを送信する
次のスクリーンショットは、**MessageResource** クラスを使用して SMS メッセージを送信する方法を示しています。 試用アカウントで SMS メッセージを送信できるように、**From** の番号が Twilio から提供されます。 コードを実行する前に、Twilio アカウントの **To** の番号を確認する必要があります。

```csharp
// Use your account SID and authentication token instead
// of the placeholders shown here.
const string accountSID = "your_twilio_account";
const string authToken = "your_twilio_authentication_token";

// Initialize the TwilioClient.
TwilioClient.Init(accountSID, authToken);

try
{
    // Send an SMS message.
    var message = MessageResource.Create(
        to: new PhoneNumber("+12069419717"),
        from: new PhoneNumber("+14155992671"),
        body: "This is my SMS message.");
}
catch (TwilioException ex)
{
    // An exception occurred making the REST call
    Console.WriteLine(ex.Message);
}
```

## <a name="how-to-provide-twiml-responses-from-your-own-website"></a><a id="howto_provide_twiml_responses"></a>方法: 独自の Web サイトから TwiML 応答を返す
アプリケーションで Twilio API の呼び出しをインスタンス化する場合 (たとえば、**CallResource.Create** メソッドを使用した場合)、Twilio は TwiML 応答を返すことが想定されている URL にユーザーの要求を送信します。 「[方法: 発信通話する](#howto_make_call)」の例では、Twilio で提供される URL [https://twimlets.com/message][twimlet_message_url] を使用して応答を返します。

> [!NOTE]
> TwiML は Web サービスで使用するように設計されており、ブラウザーで表示できます。 たとえば、[https://twimlets.com/message][twimlet_message_url] をクリックすると、空の `<Response>` 要素が表示されます。別の例として、[https://twimlets.com/message?Message%5B0%5D=Hello%20World](https://twimlets.com/message?Message%5B0%5D=Hello%20World) をクリックすると、&lt; Say&gt; 要素を含む `<Response>` 要素が表示されます。
>

Twilio から提供される URL を使用する代わりに、HTTP 応答を返す独自の URL サイトを作成できます。 HTTP 応答を返すサイトは、任意の言語で作成できます。 このトピックでは、ASP.NET 汎用ハンドラーから URL をホストすることを想定しています。

次の ASP.NET ハンドラーは、通話時の TwiML 応答で **Hello World** というテキストを生成します。

```csharp
using System.Text;
using System.Web;

namespace WebRole1
{
    /// <summary>
    /// Summary description for Handler1
    /// </summary>
    public class Handler1 : IHttpHandler
    {
        public void ProcessRequest(HttpContext context)
        {
            const string twiMLResponse =
                "<Response><Say>Hello World.</Say></Response>";

            context.Response.Clear();
            context.Response.ContentType = "text/xml";
            context.Response.ContentEncoding = Encoding.UTF8;
            context.Response.Write(twiMLResponse);
            context.Response.End();
        }

        public bool IsReusable
        {
            get
            {
                return false;
            }
        }
    }
}
```
    
この例からわかるように、TwiML 応答は XML ドキュメントにすぎません。 Twilio.TwiML ライブラリには、TwiML を生成するクラスが用意されています。 次の例では、前の例と同じ応答が生成されますが、**VoiceResponse** クラスを使用しています。

```csharp
using System.Web;
using Twilio.TwiML;

namespace WebRole1
{
    /// <summary>
    /// Summary description for Handler1
    /// </summary>
    public class Handler1 : IHttpHandler
    {

        public void ProcessRequest(HttpContext context)
        {
            var twiml = new VoiceResponse();
            twiml.Say("Hello World.");

            context.Response.Clear();
            context.Response.ContentType = "text/xml";
            context.Response.Write(twiml.ToString());
            context.Response.End();
        }

        public bool IsReusable
        {
            get
            {
                return false;
            }
        }
    }
}
```

TwiML の詳細については、[https://www.twilio.com/docs/api/twiml](https://www.twilio.com/docs/api/twiml) を参照してください。

TwiML 応答を提供する方法を設定したら、**CallResource.Create** メソッドにその URL を渡すことができます。 たとえば、Microsoft Azure クラウド サービスにデプロイされた MyTwiML という名前の Web アプリケーションがあり、ASP.NET ハンドラーの名前が mytwiml.ashx である場合、次のコード例に示すように URL を **CallResource.Create** に渡すことができます。

```csharp
// This sample uses the sandbox number provided by Twilio to make the call.
// Place the call.
var call = CallResource.Create(
    to: new PhoneNumber("+NNNNNNNNNN"),
    from: new PhoneNumber("NNNNNNNNNN"),
    url: new Uri("http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.ashx"));
    }
```

ASP.NET を使用する Azure での Twilio の使用に関する詳細については、「[Azure の Web ロールで Twilio を使用して通話する方法][howto_phonecall_dotnet]」をご覧ください。

[!INCLUDE [twilio-additional-services-and-next-steps](../includes/twilio-additional-services-and-next-steps.md)]

[howto_phonecall_dotnet]: partner-twilio-cloud-services-dotnet-phone-call-web-role.md

[twimlet_message_url]: https://twimlets.com/message

[twilio_rest_making_calls]: https://www.twilio.com/docs/api/rest/making-calls

[vs_project]:https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-azure-project-create
[nuget]:https://nuget.org/
[twilio_github_repo]:https://github.com/twilio/twilio-csharp

[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api]: https://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/console
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified
