---
title: Twilio から通話する方法 (Java) | Microsoft Docs
description: Azure 上の Java アプリケーションで Twilio を使用して Web ページから通話する方法について説明します。
services: ''
documentationcenter: java
author: georgewallace
ms.assetid: 0381789e-e775-41a0-a784-294275192b1d
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.custom: devx-track-java
ms.openlocfilehash: fdaa2eeef58599cbff8dbf7daa34f9a30f31b9cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100548762"
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-java-application-on-azure"></a>Azure 上の Java アプリケーションで Twilio を使用して通話する方法
次の例では、Azure でホストされる Java Web ページから Twilio を使用して通話する方法を示しています。 次のスクリーンショットに示すように、作成されたアプリケーションは通話に関する値の入力をユーザーに求めます。

![Twilio および Java を使用した Azure 通話フォーム][twilio_java]

このトピックでコードを使用するためには次の操作を行う必要があります。

1. Twilio アカウントと認証トークンを取得します。 Twilio を利用し始める前に、[https://www.twilio.com/pricing][twilio_pricing] で価格を検討します。 [https://www.twilio.com/try-twilio][try_twilio] でサインアップできます。 Twilio から提供される API については、[https://www.twilio.com/api][twilio_api] を参照してください。
2. Twilio JAR を入手します。 [https://github.com/twilio/twilio-java][twilio_java_github] では、GitHub ソースをダウンロードして独自の JAR を作成するか、またはビルド済み JAR (依存関係あり、またはなし) をダウンロードできます。
   このトピックでのコードは、ビルド済み TwilioJava-3.3.8-with-dependencies JAR を使用して記述されています。
3. JAR を Java ビルド パスに追加します。
4. この Java アプリケーションの作成に Eclipse を使用している場合は、Eclipse のデプロイ アセンブリ機能を使用して、アプリケーション デプロイ ファイル (WAR) に Twilio JAR をインクルードできます。 この Java アプリケーションの作成に Eclipse を使用していない場合、Twilio JAR が Java アプリケーションと同じ Azure ロールにインクルードされており、アプリケーションのクラス パスに追加されていることを確認してください。
5. cacerts キーストアに Equifax Secure Certificate Authority 証明書と MD5 フィンガープリント 67:CB:9D:C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 が格納されていることを確認します (シリアル番号は 35:DE:F4:CF、SHA1 フィンガープリントは D2:32:09:AD:23:D3:14:23:21:74:E4:0D:7F:9D:62:13:97:86:63:3A)。 これは [https://api.twilio.com][twilio_api_service] サービスの証明機関 (CA) 証明書であり、Twilio API を使用するときに呼び出されます。

さらに、[Azure Toolkit for Eclipse を使用した Hello World アプリケーションの作成][azure_java_eclipse_hello_world]に関するページにある情報、または Eclipse を使用していない場合は Azure で Java アプリケーションをホストするための他の手法に精通することを強くお勧めします。

## <a name="create-a-web-form-for-making-a-call"></a>通話用の Web フォームの作成
次のコードは、通話するためのユーザー データを取得する Web フォームの作成方法を示しています。 この例では、**TwilioCloud** という名前の新しい動的 Web プロジェクトを作成し、**callform.jsp** を JSP ファイルとして追加しました。

```jsp
<%@ page language="java" contentType="text/html; charset=ISO-8859-1"
    pageEncoding="ISO-8859-1" %>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "https://www.w3.org/TR/html4/loose.dtd">
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
        <title>Automated call form</title>
    </head>
    <body>
        <p>Fill in all fields and click <b>Make this call</b>.</p>
        <br/>
        <form action="makecall.jsp" method="post">
            <table>
                <tr>
                    <td>To:</td>
                    <td><input type="text" size=50 name="callTo" value="" />
                    </td>
                </tr>
                <tr>
                    <td>From:</td>
                    <td><input type="text" size=50 name="callFrom" value="" />
                    </td>
                </tr>
                <tr>
                    <td>Call message:</td>
                    <td><input type="text" size=400 name="callText" value="Hello. This is the call text. Good bye." />
                    </td>
                </tr>
                <tr>
                    <td colspan=2><input type="submit" value="Make this call" />
                    </td>
                </tr>
            </table>
        </form>
        <br/>
    </body>
</html>
```

## <a name="create-the-code-to-make-the-call"></a>通話用のコードの作成
次のコードは、callform.jsp によって表示されるフォームへの入力が完了すると呼び出され、通話メッセージを作成して通話を生成します。 この例では、**makecall.jsp** という名前の JSP ファイルを **TwilioCloud** プロジェクトに追加しました  (次のコードは、**accountSID** および **authToken** に割り当てられたプレースホルダー値の代わりに Twilio アカウントと認証トークンを使用します)。

```jsp
<%@ page language="java" contentType="text/html; charset=ISO-8859-1"
import="java.util.*"
import="com.twilio.*"
import="com.twilio.sdk.*"
import="com.twilio.sdk.resource.factory.*"
import="com.twilio.sdk.resource.instance.*"
pageEncoding="ISO-8859-1" %>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "https://www.w3.org/TR/html4/loose.dtd">
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
        <title>Call processing happens here</title>
    </head>
    <body>
        <b>This is my make call page.</b><p/>
<%
try 
{
    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account";
    String authToken = "your_twilio_authentication_token";

    // Instantiate an instance of the Twilio client.     
    TwilioRestClient client;
    client = new TwilioRestClient(accountSID, authToken);

    // Retrieve the account, used later to retrieve the CallFactory.
    Account account = client.getAccount();

    // Display the client endpoint. 
    out.println("<p>Using Twilio endpoint " + client.getEndpoint() + ".</p>");

    // Display the API version.
    String APIVERSION = TwilioRestClient.DEFAULT_VERSION;
    out.println("<p>Twilio client API version is " + APIVERSION + ".</p>");

    // Retrieve the values entered by the user.
    String callTo = request.getParameter("callTo");  
    // The Outgoing Caller ID, used for the From parameter,
    // must have previously been verified with Twilio.
    String callFrom = request.getParameter("callFrom");
    String userText = request.getParameter("callText");

    // Replace spaces in the user's text with '%20', 
    // to make the text suitable for a URL.
    userText = userText.replace(" ", "%20");

    // Create a URL using the Twilio message and the user-entered text.
    String Url="https://twimlets.com/message";
    Url = Url + "?Message%5B0%5D=" + userText;

    // Display the message URL.
    out.println("<p>");
    out.println("The URL is " + Url);
    out.println("</p>");

    // Place the call From, To and URL values into a hash map. 
    HashMap<String, String> params = new HashMap<String, String>();
    params.put("From", callFrom);
    params.put("To", callTo);
    params.put("Url", Url);

    CallFactory callFactory = account.getCallFactory();
    Call call = callFactory.create(params);
    out.println("<p>Call status: " + call.getStatus()  + "</p>"); 
} 
catch (TwilioRestException e) 
{
    out.println("<p>TwilioRestException encountered: " + e.getMessage() + "</p>");
    out.println("<p>StackTrace: " + e.getStackTrace().toString() + "</p>");
}
catch (Exception e) 
{
    out.println("<p>Exception encountered: " + e.getMessage() + "");
    out.println("<p>StackTrace: " + e.getStackTrace().toString() + "</p>");
}
%>
    </body>
</html>
```

通話を行うだけでなく、makecall.jsp は Twilio エンドポイント、API バージョン、通話状態を表示します。 たとえば、次のスクリーンショットのようになります。

![Twilio および Java を使用した Azure 通話応答][twilio_java_response]

## <a name="run-the-application"></a>アプリケーションの実行
アプリケーションを実行するための高レベルの手順を次に示します。この手順の詳細は、[Azure Toolkit for Eclipse を使用した Hello World アプリケーションの作成][azure_java_eclipse_hello_world]に関するページで見つけることができます。

1. TwilioCloud WAR を Azure の **approot** フォルダーにエクスポートします。 
2. TwilioCloud WAR を解凍するように **startup.cmd** を変更します。
3. アプリケーションをコンピューティング エミュレーター用にコンパイルします。
4. コンピューティング エミュレーターでデプロイを開始します。
5. ブラウザーを開いて､`http://localhost:8080/TwilioCloud/callform.jsp` を実行します。
6. フォームで値を入力し、 **[電話をかける]** をクリックして、makecall.jsp で結果を確認します。

Azure にデプロイする準備ができたら、クラウドへのデプロイ用に再コンパイルし、Azure にデプロイして、ブラウザーで http://*your_hosted_name*.cloudapp.net/TwilioCloud/callform.jsp を実行します (*your_hosted_name* は実際の値に置き換えてください)。

## <a name="next-steps"></a>次のステップ
Azure 上の Java で Twilio を使用した基本機能を示すために、このコードが用意されました。 運用環境で Azure に展開する前に、エラー処理やその他の機能をさらに追加することができます。 次に例を示します。

* Web フォームを使用する代わりに、Azure ストレージ BLOB または SQL Database を使用して、電話番号と通話テキストを保存できます。 Java で Azure ストレージ BLOB を使用する方法の詳細については、 [Java から BLOB ストレージ サービスを使用する方法に関するページ][howto_blob_storage_java]を参照してください。 
* **RoleEnvironment.getConfigurationSettings** を使用すると、Twilio アカウント ID と認証トークンは、その値を makecall.jsp 内にハードコーディングするのではなく、デプロイの構成設定から取得できます。 **RoleEnvironment** クラスについては、[JSP での Azure サービス ランタイム ライブラリの使用][azure_runtime_jsp]に関するページを参照してください。
* makecall.jsp コードは、Twilio から提供される URL ([https://twimlets.com/message][twimlet_message_url]) を **Url** 変数に割り当てます。 この URL で、通話の次の動作を Twilio に指示する TwiML (Twilio マークアップ言語) 応答が返されるようにします。 たとえば、返される TwiML 応答に **&lt;Say&gt;** 動詞を含めて、通話受信者に対してテキストが読み上げられるようにできます。 Twilio から提供される URL を使用する代わりに、Twilio の要求に応答する独自のサービスを構築することもできます。詳細については、「[Java で音声および SMS 機能に Twilio を使用する方法][howto_twilio_voice_sms_java]」を参照してください。 TwiML に関するより詳細な情報は [https://www.twilio.com/docs/api/twiml][twiml] で見つけることができ、 **&lt; Say&gt;** やその他の Twilio 動詞に関するより詳細な情報は [https://www.twilio.com/docs/api/twiml/say][twilio_say] で見つけることができます。
* Twilio のセキュリティ ガイドラインについては、[https://www.twilio.com/docs/security][twilio_docs_security] を参照してください。

Twilio の詳細については、[https://www.twilio.com/docs][twilio_docs] を参照してください。

## <a name="see-also"></a>参照
* [Java で音声および SMS 機能に Twilio を使用する方法][howto_twilio_voice_sms_java]

[twilio_pricing]: https://www.twilio.com/pricing
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_api]: https://www.twilio.com/api
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_java_github]: https://github.com/twilio/twilio-java
[twimlet_message_url]: https://twimlets.com/message
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api_service]: https://api.twilio.com
[azure_java_eclipse_hello_world]: /java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app
[howto_twilio_voice_sms_java]: partner-twilio-java-how-to-use-voice-sms.md
[howto_blob_storage_java]: https://www.windowsazure.com/develop/java/how-to-guides/blob-storage/
[howto_sql_azure_java]: https://msdn.microsoft.com/library/windowsazure/hh749029.aspx
[azure_runtime_jsp]: /previous-versions/azure/hh690948(v=azure.100)
[twilio_docs_security]: https://www.twilio.com/docs/security
[twilio_docs]: https://www.twilio.com/docs
[twilio_say]: https://www.twilio.com/docs/api/twiml/say
[twilio_java]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaCallForm.jpg
[twilio_java_response]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaMakeCall.jpg
