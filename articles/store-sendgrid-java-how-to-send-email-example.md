---
title: store-sendgrid-java-how-to-send-email-example
description: Azure デプロイで Java から SendGrid を使用して電子メールを送信する方法
services: ''
documentationcenter: java
author: thinkingserious
manager: sendgrid
editor: mollybos
ms.assetid: af096a73-6985-4350-92e4-ce1722c8d72f
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 10/30/2014
ms.author: erikre
ms.reviewer: vibhork;dominic.may@sendgrid.com;elmer.thomas@sendgrid.com
ms.openlocfilehash: 35307848c09391ae4468afc00adafd8171aaaa7b
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876482"
---
# <a name="how-to-send-email-using-sendgrid-from-java-in-an-azure-deployment"></a>Azure デプロイで Java から SendGrid を使用して電子メールを送信する方法
次の例では、Azure でホストされる Web ページから SendGrid を使用して電子メールを送信する方法を示しています。 次のスクリーンショットに示すように、作成されたアプリケーションはメールに関する値の入力をユーザーに求めます。

![電子メール フォーム][emailform]

送信されたメールは次のスクリーンショットのようになります。

![電子メール メッセージ][emailsent]

このトピックでコードを使用するためには次の操作を行う必要があります。

1. <https://www.oracle.com/technetwork/java/javamail/index.html> などから、javax.mail JAR を取得します。
2. JAR を Java のビルド パスに追加します。
3. この Java アプリケーションの作成に Eclipse を使用している場合は、Eclipse のデプロイ アセンブリ機能を使用して、アプリケーション デプロイ ファイル (WAR) に SendGrid ライブラリを含めることができます。 この Java アプリケーションの作成に Eclipse を使用していない場合、ライブラリが Java アプリケーションと同じ Azure ロールにインクルードされており、アプリケーションのクラス パスに追加されていることを確認してください。

また、電子メールを送信するには、SendGrid のユーザー名とパスワードを取得している必要があります。 SendGrid を利用し始めるには、「 [Java から SendGrid を使用して電子メールを送信する方法](store-sendgrid-java-how-to-send-email.md)」を参照してください。

さらに、 [Microsoft Azure 用の Hello World アプリケーションを Eclipse で作成する方法に関するトピック](/java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app?view=azure-java-stable)にある情報に精通すること、または、Eclipse を使用していない場合は、Microsoft Azure 上の Java アプリケーションをホストする別の手法に精通することを強くお勧めします。

## <a name="create-a-web-form-for-sending-email"></a>電子メール送信用の Web フォームの作成
次のコードでは、電子メール送信用のユーザー データを取得する Web フォームの作成方法を示しています。 このコンテンツでは、JSP ファイルに **emailform.jsp**という名前を付けています。

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "https://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Email form</title>
    </head>
    <body>
     <p>Fill in all fields and click <b>Send this email</b>.</p>
     <br/>
      <form action="sendemail.jsp" method="post">
       <table>
         <tr>
           <td>To:</td>
           <td><input type="text" size=50 name="emailTo">
           </td>
         </tr>
         <tr>
           <td>From:</td>
           <td><input type="text" size=50 name="emailFrom">
           </td>
         </tr>
         <tr>
           <td>Subject:</td>
           <td><input type="text" size=100 name="emailSubject" value="My email subject">
           </td>
         </tr>
         <tr>
           <td>Text:</td>
           <td><input type="text" size=400 name="emailText" value="Hello,<p>This is my message.</p>Thank you." />
           </td>
         </tr>
         <tr>
           <td>SendGrid user name:</td>
           <td><input type="text" name="sendGridUser">
           </td>
         </tr>
         <tr>
           <td>SendGrid password:</td>
           <td><input type="password" name="sendGridPassword">
           </td>
         </tr>
         <tr>
           <td colspan=2><input type="submit" value="Send this email">
           </td>
         </tr>
       </table>
     </form>
     <br/>
    </body>
    </html>

## <a name="create-the-code-to-send-the-email"></a>電子メール送信用のコードの作成
次のコードは、emailform.jsp によって表示されるフォームへの入力が完了すると呼び出され、電子メール メッセージを作成して送信します。 このコンテンツでは、JSP ファイルに **sendemail.jsp**という名前を付けています。

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" import="javax.activation.*, javax.mail.*, javax.mail.internet.*, java.util.Date, java.util.Properties" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "https://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Email processing happens here</title>
    </head>
    <body>
        <b>This is my send mail page.</b><p/>
     <%

     final String sendGridUser = request.getParameter("sendGridUser");
     final String sendGridPassword = request.getParameter("sendGridPassword");

     class SMTPAuthenticator extends Authenticator
     {
       public PasswordAuthentication getPasswordAuthentication()
       {
            String username = sendGridUser;
            String password = sendGridPassword;

            return new PasswordAuthentication(username, password);   
       }
     }
     try
     {

         // The SendGrid SMTP server.
         String SMTP_HOST_NAME = "smtp.sendgrid.net";

         Properties properties;

         properties = new Properties();

         // Specify SMTP values.
         properties.put("mail.transport.protocol", "smtp");
         properties.put("mail.smtp.host", SMTP_HOST_NAME);
         properties.put("mail.smtp.port", 587);
         properties.put("mail.smtp.auth", "true");

         // Display the email fields entered by the user. 
         out.println("Value entered for email Subject: " + request.getParameter("emailSubject") + "<br/>");        
         out.println("Value entered for email      To: " + request.getParameter("emailTo") + "<br/>");
         out.println("Value entered for email    From: " + request.getParameter("emailFrom") + "<br/>");
         out.println("Value entered for email    Text: " + "<br/>" + request.getParameter("emailText") + "<br/>");

         // Create the authenticator object.
         Authenticator authenticator = new SMTPAuthenticator();

         // Create the mail session object.
         Session mailSession;
         mailSession = Session.getDefaultInstance(properties, authenticator);

         // Display debug information to stdout, useful when using the
         // compute emulator during development.
         mailSession.setDebug(true);

         // Create the message and message part objects.
         MimeMessage message;
         Multipart multipart;
         MimeBodyPart messagePart; 

         message = new MimeMessage(mailSession);

         multipart = new MimeMultipart("alternative");
         messagePart = new MimeBodyPart();
         messagePart.setContent(request.getParameter("emailText"), "text/html");
         multipart.addBodyPart(messagePart);            

         // Specify the email To, From, Subject and Content. 
         message.setFrom(new InternetAddress(request.getParameter("emailFrom")));
         message.addRecipient(Message.RecipientType.TO, new InternetAddress(request.getParameter("emailTo")));
         message.setSubject(request.getParameter("emailSubject")); 
         message.setContent(multipart);

         // Uncomment the following if you want to add a footer.
         // message.addHeader("X-SMTPAPI", "{\"filters\": {\"footer\": {\"settings\": {\"enable\":1,\"text/html\": \"<html>This is my <b>email footer</b>.</html>\"}}}}");

         // Uncomment the following if you want to enable click tracking.
         // message.addHeader("X-SMTPAPI", "{\"filters\": {\"clicktrack\": {\"settings\": {\"enable\":1}}}}");

         Transport transport;
         transport = mailSession.getTransport();
         // Connect the transport object.
         transport.connect();
         // Send the message.
         transport.sendMessage(message,  message.getRecipients(Message.RecipientType.TO));
         // Close the connection.
         transport.close();

        out.println("<p>Email processing completed.</p>");

     }
     catch (Exception e)
     {
         out.println("<p>Exception encountered: " + 
                            e.getMessage()     +
                            "</p>");   
     }
    %>

    </body>
    </html>

メールを送信するだけでなく、emailform.jsp はユーザーに結果を返します。たとえば、次のスクリーンショットのようになります。

![電子メール送信の結果][emailresult]

## <a name="next-steps"></a>次の手順
アプリケーションをコンピューティング エミュレーターにデプロイし、ブラウザーで emailform.jsp を実行します。フォームに値を入力し、 **[Send this email (電子メールの送信)]** をクリックして、sendemail.jsp で結果を確認します。

Azure 上で Java から SendGrid を使用する方法を示すために、このコードが用意されています。 運用環境で Azure に展開する前に、エラー処理やその他の機能をさらに追加することができます。 例: 

* Web フォームを使用する代わりに、Azure ストレージ BLOB または SQL データベースを使用して、電子メール アドレスと電子メール メッセージを保存するようにします。 Java で Azure ストレージ BLOB を使用する方法の詳細については、 [Java から BLOB ストレージ サービスを使用する方法に関するページ](https://azure.microsoft.com/develop/java/how-to-guides/blob-storage/)を参照してください。 Java で SQL データベースを使用する方法の詳細については、 [Java での SQL データベースの使用に関するページ](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-java)を参照してください。
* Java での SendGrid の使用については、「 [Java から SendGrid を使用して電子メールを送信する方法](store-sendgrid-java-how-to-send-email.md)」を参照してください。

[emailform]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailform.jpg
[emailsent]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailSent.jpg
[emailresult]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaResult.jpg
