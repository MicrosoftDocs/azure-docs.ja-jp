---
title: SendGrid 電子メール サービスの使用方法 (Java) | Microsoft Docs
description: Azure で SendGrid 電子メール サービスを使用して電子メールを送信する方法について説明します。 コード サンプルは Java で記述されています。
services: ''
documentationcenter: java
author: thinkingserious
manager: sendgrid
editor: mollybos
ms.assetid: cc75c43e-ede9-492b-98c2-9147fcb92c21
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 10/30/2014
ms.author: erikre
ms.reviewer: elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork
ms.openlocfilehash: 8ae948e9c79cff4cd0c896b250743fd9dc521752
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876519"
---
# <a name="how-to-send-email-using-sendgrid-from-java"></a>Java から SendGrid を使用して電子メールを送信する方法
このガイドでは、Azure の SendGrid 電子メール サービスを使用して一般的なプログラム タスクを実行する方法を紹介します。 コード サンプルは Java で記述されています。 紹介するシナリオは、**電子メールの作成**、**電子メールの送信**、**添付ファイルの追加**、**フィルターの使用**、および**プロパティの更新**です。 SendGrid と電子メールの送信の詳細については、「 [次のステップ](#next-steps) 」を参照してください。

## <a name="what-is-the-sendgrid-email-service"></a>SendGrid 電子メール サービスとは
SendGrid は、信頼性の高い[トランザクション メール配信]、拡張性、およびリアルタイム分析の機能を備えた[クラウドベースの電子メール サービス]であり、柔軟な API を備えているためカスタム統合も容易です。 SendGrid の一般的な使用シナリオを次に示します。

* 顧客に受信通知を自動送信する
* 顧客に広告メールを月 1 回送信するための配布リストを管理する
* ブロックされた電子メールや顧客の応答性などを表す測定値をリアルタイムで収集する
* 傾向を認識するために役立つレポートを生成する
* 顧客の問い合わせを転送する
* アプリケーションからの電子メール通知

詳細については、<https://sendgrid.com> を参照してください。

## <a name="create-a-sendgrid-account"></a>SendGrid アカウントの作成
[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="how-to-use-the-javaxmail-libraries"></a>方法:javax.mail ライブラリを使用する
javax.mail ライブラリを <https://www.oracle.com/technetwork/java/javamail> などから取得し、自分のコードにインポートします。 大まかに言えば、javax.mail ライブラリと SMTP を使用して電子メールを送信するプロセスは、次の処理を実行することです。

1. SMTP に関する値を指定します。たとえば、SMTP サーバーは、 SendGrid では smtp.sendgrid.net になります。

```
        import java.util.Properties;
        import javax.mail.*;
        import javax.mail.internet.*;

        public class MyEmailer {
           private static final String SMTP_HOST_NAME = "smtp.sendgrid.net";
           private static final String SMTP_AUTH_USER = "your_sendgrid_username";
           private static final String SMTP_AUTH_PWD = "your_sendgrid_password";

           public static void main(String[] args) throws Exception{
               new MyEmailer().SendMail();
           }

           public void SendMail() throws Exception
           {
              Properties properties = new Properties();
                 properties.put("mail.transport.protocol", "smtp");
                 properties.put("mail.smtp.host", SMTP_HOST_NAME);
                 properties.put("mail.smtp.port", 587);
                 properties.put("mail.smtp.auth", "true");
                 // …
```

1. *javax.mail.Authenticator* クラスを継承し、*getPasswordAuthentication* メソッドの実装内で SendGrid のユーザー名とパスワードを返します。  

       private class SMTPAuthenticator extends javax.mail.Authenticator {
       public PasswordAuthentication getPasswordAuthentication() {
          String username = SMTP_AUTH_USER;
          String password = SMTP_AUTH_PWD;
          return new PasswordAuthentication(username, password);
       }
2. *javax.mail.Session* オブジェクトを使用して、認証された電子メール セッションを作成します。  

       Authenticator auth = new SMTPAuthenticator();
       Session mailSession = Session.getDefaultInstance(properties, auth);
3. メッセージを作成し、**To**、**From**、**Subject**、およびコンテンツの値を設定します。 これは、「[方法: 電子メールを作成する](#how-to-create-an-email)」のセクションに示されています。
4. *javax.mail.Transport* オブジェクトを使用してメッセージを送信します。 これは、[方法: 電子メールを送信する][#how-to-send-an-email] のセクションに示されています。

## <a name="how-to-create-an-email"></a>方法:電子メールを作成する
次のコードは電子メールに関する値を指定する方法を示しています。

    MimeMessage message = new MimeMessage(mailSession);
    Multipart multipart = new MimeMultipart("alternative");
    BodyPart part1 = new MimeBodyPart();
    part1.setText("Hello, Your Contoso order has shipped. Thank you, John");
    BodyPart part2 = new MimeBodyPart();
    part2.setContent(
        "<p>Hello,</p>
        <p>Your Contoso order has <b>shipped</b>.</p>
        <p>Thank you,<br>John</br></p>",
        "text/html");
    multipart.addBodyPart(part1);
    multipart.addBodyPart(part2);
    message.setFrom(new InternetAddress("john@contoso.com"));
    message.addRecipient(Message.RecipientType.TO,
       new InternetAddress("someone@example.com"));
    message.setSubject("Your recent order");
    message.setContent(multipart);

## <a name="how-to-send-an-email"></a>方法:電子メールを送信する
次のコードは電子メールを送信する方法を示しています。

    Transport transport = mailSession.getTransport();
    // Connect the transport object.
    transport.connect();
    // Send the message.
    transport.sendMessage(message, message.getAllRecipients());
    // Close the connection.
    transport.close();

## <a name="how-to-add-an-attachment"></a>方法:添付ファイルを追加する
次のコードは添付ファイルを追加する方法を示しています。

    // Local file name and path.
    String attachmentName = "myfile.zip";
    String attachmentPath = "c:\\myfiles\\";
    MimeBodyPart attachmentPart = new MimeBodyPart();
    // Specify the local file to attach.
    DataSource source = new FileDataSource(attachmentPath + attachmentName);
    attachmentPart.setDataHandler(new DataHandler(source));
    // This example uses the local file name as the attachment name.
    // They could be different if you prefer.
    attachmentPart.setFileName(attachmentName);
    multipart.addBodyPart(attachmentPart);

## <a name="how-to-use-filters-to-enable-footers-tracking-and-analytics"></a>方法:フィルターを使用してフッター、追跡、および分析を有効にする
SendGrid では、 *フィルター*を使用することでその他の電子メール機能も利用することができます。 その設定を電子メール メッセージに追加することで、クリック追跡、Google 分析、サブスクリプション追跡などの独自の機能を有効にすることができます。 すべてのフィルターの一覧については、 [フィルター設定][Filter Settings]に関するページを参照してください。

* 次のコードでは、フッター フィルターを挿入して、送信される電子メールの 下部に HTML テキストが追加されるようにしています。

      message.addHeader("X-SMTPAPI",
          "{\"filters\":
          {\"footer\":
          {\"settings\":
          {\"enable\":1,\"text/html\":
          \"<html><b>Thank you</b> for your business.</html>\"}}}}");
* 別のフィルターの例として、クリック追跡があります。 電子メールのテキストに次のようなハイパーリンクが含まれており、そのクリック率を追跡するとします。

      messagePart.setContent(
          "Hello,
          <p>This is the body of the message. Visit
          <a href='http://www.contoso.com'>http://www.contoso.com</a>.</p>
          Thank you.",
          "text/html");
* クリック追跡を有効にするには、次のコードを使用します。

      message.addHeader("X-SMTPAPI",
          "{\"filters\":
          {\"clicktrack\":
          {\"settings\":
          {\"enable\":1}}}}");

## <a name="how-to-update-email-properties"></a>方法:電子メールのプロパティを更新する
メールの一部のプロパティは、**set Property** を使って上書きすることや、**add Property** を使って追加することができます。

たとえば、 **ReplyTo** アドレスを指定するには、次のコードを使用します。

    InternetAddress addresses[] =
        { new InternetAddress("john@contoso.com"),
          new InternetAddress("wendy@contoso.com") };

    message.setReplyTo(addresses);

**Cc** 受信者を追加するには、次のコードを使用します。

    message.addRecipient(Message.RecipientType.CC, new
    InternetAddress("john@contoso.com"));

## <a name="how-to-use-additional-sendgrid-services"></a>方法:その他の SendGrid サービスを使用する
SendGrid の Web ベース API を使用して、Azure アプリケーションからその他の SendGrid 機能を利用することができます。 詳細については、 [SendGrid API に関するドキュメント][SendGrid API documentation]を参照してください。

## <a name="next-steps"></a>次の手順
これで、SendGrid 電子メール サービスの基本を学習できました。さらに詳細な情報が必要な場合は、次のリンク先を参照してください。

* Azure デプロイでの SendGrid の使用を示すサンプル: [Azure デプロイで Java から SendGrid を使用して電子メールを送信する方法](store-sendgrid-java-how-to-send-email-example.md)
* SendGrid Java SDK: <https://sendgrid.com/docs/Code_Examples/java.html>
* SendGrid API に関するドキュメント: <https://sendgrid.com/docs/API_Reference/index.html>
* Azure ユーザー向けの SendGrid 特別プラン: <https://sendgrid.com/windowsazure.html>

[https://sendgrid.com]: https://sendgrid.com
[https://sendgrid.com/pricing.html]: https://sendgrid.com/pricing.html
[http://www.sendgrid.com/azure.html]: https://www.sendgrid.com/windowsazure.html
[https://sendgrid.com/features]: https://sendgrid.com/features
[https://www.oracle.com/technetwork/java/javamail]: https://www.oracle.com/technetwork/java/javamail/index.html
[Filter Settings]: https://sendgrid.com/docs/API_Reference/Web_API/filter_settings.html
[SendGrid API documentation]: https://sendgrid.com/docs/API_Reference/index.html
[https://sendgrid.com/azure.html]: https://sendgrid.com/windowsazure.html
[クラウドベースの電子メール サービス]: https://sendgrid.com/email-solutions
[トランザクション メール配信]: https://sendgrid.com/transactional-email
