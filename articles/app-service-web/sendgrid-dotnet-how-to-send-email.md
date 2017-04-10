---
title: "SendGrid 電子メール サービスの使用方法 (.NET) | Microsoft Docs"
description: "Azure で SendGrid 電子メール サービスを使用して電子メールを送信する方法について説明します。 コード サンプルは C# で記述され、.NET API を使用しています。"
services: app-service\web
documentationcenter: .net
author: thinkingserious
manager: erikre
editor: 
ms.assetid: 21bf4028-9046-476b-9799-3d3082a0f84c
ms.service: app-service-web
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/15/2017
ms.author: dx@sendgrid.com
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: 534397378a4d83414bfe62f2dd2c57678f09c429
ms.lasthandoff: 03/10/2017


---
# <a name="how-to-send-email-using-sendgrid-with-azure"></a>SendGrid を使用した Azure での電子メールの送信方法
## <a name="overview"></a>概要
このガイドでは、Azure の SendGrid 電子メール サービスを使用して一般的なプログラム タスクを実行する方法を紹介します。 サンプルは C\#
 で記述され、.NET Standard 1.3 をサポートしています。 紹介するシナリオは、電子メールの作成、電子メールの送信、添付ファイルの追加、さまざまな電子メールおよび追跡設定の有効化です。 SendGrid と電子メールの送信の詳細については、「[次のステップ][Next steps]」を参照してください。

## <a name="what-is-the-sendgrid-email-service"></a>SendGrid 電子メール サービスとは
SendGrid は、信頼性の高い[トランザクション メール配信]、拡張性、およびリアルタイム分析の機能を備えた[クラウドベースの電子メール サービス]であり、柔軟な API を備えているためカスタム統合も容易です。 SendGrid の一般的な用途は次のとおりです。

* 顧客に配信確認メッセージまたは購入確認を自動的に送信する。
* 顧客に月&1; 回広告メールを送信するための配布リストを管理する。
* ブロックされた電子メールや顧客エンゲージメントなどを表すリアルタイムのメトリックを収集する。
* 顧客の問い合わせを転送する。
* 受信電子メールを処理する。

詳細については、[https://sendgrid.com](https://sendgrid.com) または SendGrid の [C# ライブラリ][sendgrid-csharp] GitHub リポジトリにアクセスしてください。

## <a name="create-a-sendgrid-account"></a>SendGrid アカウントの作成
[!INCLUDE [sendgrid-sign-up](../../includes/sendgrid-sign-up.md)]

## <a name="reference-the-sendgrid-net-class-library"></a>SendGrid .NET クラス ライブラリを参照する
[SendGrid NuGet パッケージ](https://www.nuget.org/packages/Sendgrid) は、SendGrid API を取得し、すべての依存関係を備えたアプリケーションを構成する最も簡単な方法です。 NuGet は Microsoft Visual Studio 2015 以降に含まれる Visual Studio 拡張機能であり、これを使用してライブラリおよびツールのインストールと更新を簡単に行うことができます。

> [!NOTE]
> Visual Studio 2015 よりも前のバージョンの Visual Studio を利用している場合、NuGet をインストールするには、 [http://www.nuget.org](http://www.nuget.org)にアクセスして、 **[Install NuGet]** ボタンをクリックしてください。
>
>

アプリケーションに SendGrid NuGet パッケージをインストールするには、次のステップを行います。

1. **[新しいプロジェクト]** をクリックし、**[テンプレート]** を選択します。

   ![新しいプロジェクトを作成する][create-new-project]
2. **[ソリューション エクスプローラー]** で、**[参照]** を右クリックし、**[NuGet パッケージの管理]** をクリックします。

   ![SendGrid NuGet パッケージ][SendGrid-NuGet-package]
3. 「**SendGrid**」を検索し、検索結果の一覧から **SendGrid** を選択します。
4. バージョン ドロップダウンから NuGet パッケージの最新の安定バージョンを選択して、この記事で説明するオブジェクト モデルと API を操作できるようにします。

   ![SendGrid パッケージ][sendgrid-package]
5. **[インストール]** をクリックしてインストールを実行した後、このダイアログを閉じます。

SendGrid の .NET クラス ライブラリは、**SendGrid** という名前です。 これには次の名前空間が含まれます。

* **SendGrid**、SendGrid の API との通信に使用します。
* **SendGrid.Helpers.Mail**、電子メールの送信方法を指定する SendGridMessage オブジェクトをヘルパー メソッドで簡単に作成できます。

プログラムを使用して SendGrid 電子メール サービスにアクセスするすべての C# ファイルの冒頭部分に、名前空間を宣言する次のコードを追加します。

    using SendGrid;
    using SendGrid.Helpers.Mail

## <a name="how-to-create-an-email"></a>方法: 電子メールを作成する
**SendGridMessage** オブジェクトを使用して電子メール メッセージを作成します。 メッセージ オブジェクトを作成すると、電子メール送信者、電子メール受信者、電子メールの件名、本文などのプロパティやメソッドを設定することができます。

次の例に、すべての値が設定された電子メール オブジェクトの作成方法を示します。

    var msg = new SendGridMessage();

    msg.SetFrom(new EmailAddress("dx@example.com", "SendGrid DX Team"));

    var recipients = new List<EmailAddress>
    {
        new EmailAddress("jeff@example.com", "Jeff Smith"),
        new EmailAddress("anna@example.com", "Anna Lidman"),
        new EmailAddress("peter@example.com", "Peter Saddow")
    };
    msg.AddTos(recipients);

    msg.SetSubject("Testing the SendGrid C# Library");

    msg.AddContent(MimeType.Text, "Hello World plain text!");
    msg.AddContent(MimeType.Html, "<p>Hello World!</p>");

**SendGrid** 型でサポートされるすべてのプロパティとメソッドの詳細については、GitHub の [sendgrid-csharp][sendgrid-csharp] に関するページを参照してください。

## <a name="how-to-send-an-email"></a>方法: 電子メールを送信する
電子メール メッセージを作成した後で、SendGrid の API を使用してメッセージを送信することができます。 または、[.NET の組み込みライブラリ][NET-library]を使用する方法もあります。

電子メールを送信するには、SendGrid API キーを指定する必要があります。 API キーを構成する方法の詳細については、SendGrid の API キーの[ドキュメント][documentation]を参照してください。

これらの資格情報は、Azure Portal で [アプリケーションの設定] をクリックし、アプリケーションの設定にキーと値のペアを追加することによって保存できます。

 ![Azure app settings][azure_app_settings]

 これらには、次のようにしてアクセスすることができます。

    var apiKey = System.Environment.GetEnvironmentVariable("SENDGRID_APIKEY");
    var client = new SendGridClient(apiKey);

次の例に、Web API でメッセージを送信する方法を示します。

    using System;
    using System.Threading.Tasks;
    using SendGrid;
    using SendGrid.Helpers.Mail;

    namespace Example
    {
        internal class Example
        {
            private static void Main()
            {
                Execute().Wait();
            }

            static async Task Execute()
            {
                var apiKey = System.Environment.GetEnvironmentVariable("SENDGRID_APIKEY");
                var client = new SendGridClient(apiKey);
                var msg = new SendGridMessage()
                {
                    From = new EmailAddress("test@example.com", "DX Team"),
                    Subject = "Hello World from the SendGrid CSharp SDK!",
                    PlainTextContent = "Hello, Email!",
                    HtmlContent = "<strong>Hello, Email!</strong>"
                };
                msg.AddTo(new EmailAddress("test@example.com", "Test User"));
                var response = await client.SendEmailAsync(msg);
            }
        }
    }

## <a name="how-to-add-an-attachment"></a>方法: 添付ファイルを追加する
添付ファイルをメッセージに追加するには、**AddAttachment** メソッドを呼び出し、少なくとも添付するファイル名と Base64 でエンコードされた内容を指定します。 複数のファイルを添付するには、添付する各ファイルにつき&1; 回このメソッドを呼び出すか、**AddAttachments** メソッドを使用します。 次の例に、メッセージに添付ファイルを追加する方法を示します。

    var banner2 = new Attachment()
    {
        Content = Convert.ToBase64String(raw_content),
        Type = "image/png",
        Filename = "banner2.png",
        Disposition = "inline",
        ContentId = "Banner 2"
    };
    msg.AddAttachment(banner2);

## <a name="how-to-use-mail-settings-to-enable-footers-tracking-and-analytics"></a>方法: 電子メール設定を使用してフッター、追跡、および分析を有効にする
SendGrid では、電子メール設定と追跡設定を使用することでその他の電子メール機能も利用することができます。 これらの設定を電子メール メッセージに追加することで、クリック追跡、Google 分析、サブスクリプション追跡などの特定の機能を有効にすることができます。 アプリの完全な一覧については、[設定に関するドキュメント][settings-documentation]を参照してください。

アプリは、**SendGridMessage** クラスの一部として実装されたメソッドを使用する **SendGrid** 電子メール メッセージに適用できます。 次の例に、フッター フィルターとクリック追跡フィルターの使用方法を示します。

次の例に、フッター フィルターとクリック追跡フィルターの使用方法を示します。

### <a name="footer-settings"></a>フッターの設定
    msg.SetFooterSetting(
                         true,
                         "Some Footer HTML",
                         "<strong>Some Footer Text</strong>");

### <a name="click-tracking"></a>クリック追跡
    msg.SetClickTracking(true);

## <a name="how-to-use-additional-sendgrid-services"></a>方法: その他の SendGrid サービスを使用する
SendGrid のいくつかの API と webhook を使用して、Azure アプリケーション内でその他の機能を利用することができます。 詳細については、[SendGrid API のリファレンス][SendGrid API documentation]を参照してください。

## <a name="next-steps"></a>次のステップ
これで、SendGrid 電子メール サービスの基本を学習できました。さらに詳細な情報が必要な場合は、次のリンク先を参照してください。

* SendGrid C\# ライブラリ レポート: [sendgrid-csharp][sendgrid-csharp]
* SendGrid API に関するドキュメント: <https://sendgrid.com/docs>

[Next steps]: #next-steps
[What is the SendGrid Email Service?]: #whatis
[Create a SendGrid Account]: #createaccount
[Reference the SendGrid .NET Class Library]: #reference
[How to: Create an Email]: #createemail
[How to: Send an Email]: #sendemail
[How to: Add an Attachment]: #addattachment
[How to: Use Filters to Enable Footers, Tracking, and Analytics]: #usefilters
[How to: Use Additional SendGrid Services]: #useservices

[create-new-project]: ./media/sendgrid-dotnet-how-to-send-email/new-project.png
[SendGrid-NuGet-package]: ./media/sendgrid-dotnet-how-to-send-email/reference.png
[sendgrid-package]: ./media/sendgrid-dotnet-how-to-send-email/sendgrid-package.png
[azure_app_settings]: ./media/sendgrid-dotnet-how-to-send-email/azure-app-settings.png
[sendgrid-csharp]: https://github.com/sendgrid/sendgrid-csharp
[SMTP vs. Web API]: https://sendgrid.com/docs/Integrate/index.html
[App Settings]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
[SendGrid API documentation]: https://sendgrid.com/docs/API_Reference/api_v3.html
[NET-library]: https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html#-Using-NETs-Builtin-SMTP-Library
[documentation]: https://sendgrid.com/docs/Classroom/Send/api_keys.html
[settings-documentation]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html

[クラウドベースの電子メール サービス]: https://sendgrid.com/solutions
[トランザクション メール配信]: https://sendgrid.com/use-cases/transactional-email


