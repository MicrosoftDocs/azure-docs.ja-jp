---
title: Twilio から通話する方法 (.NET) | Microsoft Docs
description: Azure で Twilio API サービスを利用して電話をかける方法について説明します。 コード サンプルは .NET で記述されています。
services: ''
documentationcenter: .net
author: mimckitt
editor: ''
ms.assetid: 789185ad-69dc-4e9e-a936-42e0a25315c8
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/04/2016
ms.author: mimckitt
ms.custom: devx-track-dotnet
ms.openlocfilehash: 22e0a19c0e2f5b29b35ebf43a3f250959c9b636d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95521123"
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-web-role-on-azure"></a>Azure の Web ロールで Twilio を使用して通話する方法
このガイドでは、Azure でホストされる Web ページから Twilio を使用して通話する方法を説明します。 次のスクリーンショットに示すように、作成されたアプリケーションは指定された番号とメッセージで呼び出しを行うことをユーザーに求めます。

![Twilio および ASP.NET を使用した Azure 通話フォーム][twilio_dotnet_basic_form]

## <a name="prerequisites"></a><a name="twilio-prereqs"></a>前提条件
このトピックでコードを使用するためには次の操作を行う必要があります。

1. [Twilio Console][twilio_console] から、Twilio アカウントと認証トークンを取得します。 Twilio の使用を開始するには、[https://www.twilio.com/try-twilio][try_twilio] でサインアップします。 [https://www.twilio.com/pricing][twilio_pricing] で価格を評価できます。 Twilio から提供される API については、[https://www.twilio.com/voice/api][twilio_api] を参照してください。
2. *Twilio .NET ライブラリ* を Web ロールに追加します。 このトピックの「**Twilio ライブラリを Web ロール プロジェクトに追加するには**」のセクションを参照してください。

基本的な [Azure の Web ロール][azure_webroles_get_started]の作成に精通している必要があります。

## <a name="how-to-create-a-web-form-for-making-a-call"></a><a name="howtocreateform"></a>方法: 通話用の Web フォームの作成
<a id="use_nuget"></a>Twilio ライブラリを Web ロール プロジェクトに追加するには

1. Visual Studio でソリューションを開きます。
2. **[参照]** を右クリックします。
3. **[NuGet パッケージの管理]** をクリックします。
4. **[オンライン]** をクリックします。
5. [オンライン検索] ボックスに、「 *twilio*」と入力します。
6. Twilio パッケージで **[インストール]** をクリックします。

次のコードは、通話するためのユーザー データを取得する Web フォームの作成方法を示しています。 この例では、**TwilioCloud** という名前の ASP.NET Web ロールが作成されます。

```aspx
<%@ Page Title="Home Page" Language="C#" MasterPageFile="~/Site.master"
    AutoEventWireup="true" CodeBehind="Default.aspx.cs"
    Inherits="WebRole1._Default" %>

<asp:Content ID="HeaderContent" runat="server" ContentPlaceHolderID="HeadContent">
</asp:Content>
<asp:Content ID="BodyContent" runat="server" ContentPlaceHolderID="MainContent">
    <div>
        <asp:BulletedList ID="varDisplay" runat="server" BulletStyle="NotSet">
        </asp:BulletedList>
    </div>
    <div>
        <p>Fill in all fields and click <b>Make this call</b>.</p>
        <div>
            To:<br /><asp:TextBox ID="toNumber" runat="server" /><br /><br />
            Message:<br /><asp:TextBox ID="message" runat="server" /><br /><br />
            <asp:Button ID="callpage" runat="server" Text="Make this call"
                onclick="callpage_Click" />
        </div>
    </div>
</asp:Content>
```

## <a name="how-to-create-the-code-to-make-the-call"></a><a id="howtocreatecode"></a>方法: 通話用のコードの作成
次のコードはユーザーがフォームへの入力を完了すると呼び出され、このコードによって通話メッセージが作成され、通話が生成されます。 この例では、コードは、フォームのボタンの onclick イベント ハンドラーで実行されます (このコードで `accountSID` と `authToken` に設定しているプレースホルダー値は、Twilio アカウントと認証トークンに置き換えてください)。

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using System.Web.UI;
using System.Web.UI.WebControls;
using Twilio;
using Twilio.Http;
using Twilio.Types;
using Twilio.Rest.Api.V2010;

namespace WebRole1
{
    public partial class _Default : System.Web.UI.Page
    {
        protected void Page_Load(object sender, EventArgs e)
        {

        }

        protected void callpage_Click(object sender, EventArgs e)
        {
            // Call processing happens here.

            // Use your account SID and authentication token instead of
            // the placeholders shown here.
            var accountSID = "ACNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN";
            var authToken =  "NNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN";

            // Instantiate an instance of the Twilio client.
            TwilioClient.Init(accountSID, authToken);

            // Retrieve the account, used later to retrieve the
            var account = AccountResource.Fetch(accountSID);

            this.varDisplay.Items.Clear();

            if (this.toNumber.Text == "" || this.message.Text == "")
            {
                this.varDisplay.Items.Add(
                        "You must enter a phone number and a message.");
            }
            else
            {
                // Retrieve the values entered by the user.
                var to = PhoneNumber(this.toNumber.Text);
                var from = new PhoneNumber("+14155992671");
                var myMessage = this.message.Text;

                // Create a URL using the Twilio message and the user-entered
                // text. You must replace spaces in the user's text with '%20'
                // to make the text suitable for a URL.
                var url = $"https://twimlets.com/message?Message%5B0%5D={myMessage.Replace(" ", "%20")}";
                var twimlUri = new Uri(url);

                // Display the endpoint, API version, and the URL for the message.
                this.varDisplay.Items.Add($"Using Twilio endpoint {
                }");
                this.varDisplay.Items.Add($"Twilioclient API Version is {apiVersion}");
                this.varDisplay.Items.Add($"The URL is {url}");

                // Place the call.
                var call = CallResource.create(to, from, url: twimlUri);
                this.varDisplay.Items.Add("Call status: " + call.Status);
            }
        }
    }
}
```

通話が行われ、Twilio エンドポイント、API バージョン、および通話の状態が表示されます。 次のスクリーンショットは、実行サンプルでの出力を示しています。

![Twilio および ASP.NET を使用した Azure 通話応答][twilio_dotnet_basic_form_output]

TwiML に関するより詳細な情報は [https://www.twilio.com/docs/api/twiml][twiml] で見つけることができます。 &lt;Say&gt; やその他の Twilio 動詞に関するより詳細な情報は [https://www.twilio.com/docs/api/twiml/say][twilio_say] で見つけることができます。

## <a name="next-steps"></a><a id="nextsteps"></a>次のステップ
Azure の ASP.NET Web ロールで Twilio を使用した基本機能を示すために、このコードが用意されました。 運用環境で Azure に展開する前に、エラー処理やその他の機能をさらに追加することができます。 次に例を示します。

* Web フォームを使用する代わりに、Azure BLOB ストレージまたは Azure SQL データベース インスタンスを使用して、電話番号と通話テキストを保存できます。 Azure での BLOB の使用については、[.NET で Azure Blob Storage サービスを使用する方法][howto_blob_storage_dotnet]に関するページを参照してください。 SQL Database の使用については、[.NET アプリケーションで Azure SQL Database を使用する方法][howto_sql_azure_dotnet]に関するページを参照してください。
* `RoleEnvironment.getConfigurationSettings` を使用すると、Twilio アカウント ID と認証トークンは、その値をフォーム内にハードコーディングするのではなく、デプロイの構成設定から取得できます。 `RoleEnvironment` クラスについては、「[Microsoft.WindowsAzure.ServiceRuntime 名前空間][azure_runtime_ref_dotnet]」を参照してください。
* Twilio のセキュリティ ガイドラインについては、[https://www.twilio.com/docs/security][twilio_docs_security] を参照してください。
* Twilio の詳細については、[https://www.twilio.com/docs][twilio_docs] を参照してください。

## <a name="see-also"></a><a name="seealso"></a>関連項目
* [Azure から音声および SMS 機能に Twilio を使用する方法](twilio-dotnet-how-to-use-for-voice-sms.md)

[twilio_console]: https://www.twilio.com/console
[twilio_pricing]: https://www.twilio.com/pricing
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_api]: https://www.twilio.com/voice/api
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified

[twilio_dotnet_basic_form]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form.png
[twilio_dotnet_basic_form_output]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form_output.png

[twiml]: https://www.twilio.com/docs/api/twiml



[howto_twilio_voice_sms_dotnet]: /develop/net/how-to-guides/twilio/

[howto_blob_storage_dotnet]: https://www.windowsazure.com/develop/net/how-to-guides/blob-storage/

[howto_sql_azure_dotnet]: https://www.windowsazure.com/develop/net/how-to-guides/sql-database/


[twilio_docs_security]: https://www.twilio.com/docs/security
[twilio_docs]: https://www.twilio.com/docs
[twilio_say]: https://www.twilio.com/docs/api/twiml/say


[azure_runtime_ref_dotnet]: /previous-versions/azure/reference/ee741722(v=azure.100)
[azure_webroles_get_started]: ./cloud-services/cloud-services-dotnet-get-started.md