---
title: "Microsoft Authenticator で携帯電話からサインインする - Azure アカウントと Microsoft アカウント | Microsoft Docs"
description: "パスワードを入力する代わりに、携帯電話を使って Microsoft アカウントにサインインします。 この記事では、この機能についてのよく寄せられる質問に回答します。"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: librown
ms.assetid: 
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/02/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: ec52f719f33db0d893d707b59293fa75a8ff4314
ms.openlocfilehash: 033392783fd98cbdb23fd0254c93687754808286


---
# <a name="sign-in-with-your-phone-not-your-password"></a>パスワードではなく携帯電話でサインインする

Microsoft Authenticator アプリを使うと、パスワードを入力した後で&2; 段階認証を実行することにより、アカウントの安全性を維持できます。 さらに、この機能を使うと、個人用 Microsoft アカウントのパスワードを完全に置き換えることができます。 

この機能は、iOS および Android デバイスで利用でき、個人の Microsoft アカウントで動作します。 

## <a name="how-it-works"></a>動作のしくみ

多くのユーザーは、Microsoft アカウントにサインインするときの&2; 段階認証用に Microsoft Authenticator アプリを使います。 パスワードを入力した後、アプリに移動して、通知を承認するか、検証コードを取得します。 携帯電話でのサインインでは、パスワードの入力を省略し、ID 確認のすべての操作を携帯電話で行うことができます。 これは、ユーザーが知っていることと持っているものを尋ねる&2; 段階認証と同じように機能します。 携帯電話が持っているものであることは同じですが、この場合は、知っていることとして、携帯電話の PIN または生体認証キーの入力が要求されます。 

## <a name="how-to-get-started"></a>ファースト ステップ

携帯電話で個人の Microsoft アカウントにサインインするには、次の手順のようにします。 

1. アカウントで携帯電話によるサインインを有効にします。 

  - Microsoft Authenticator アプリがまだない場合は、[Microsoft Authenticator のページ](microsoft-authenticator-app-how-to.md)の手順に従って、アプリをインストールし、個人の Microsoft アカウントを追加します。 新しく追加されたアカウントは自動的に有効になります。

  - Microsoft Authenticator を&2; 段階認証に既に使っている場合は、アプリのホーム ページでアカウントを選び、ドロップダウン メニューから [電話によるサインインを有効にする] を選びます。

  >[!NOTE] 
  >アカウントを保護するには、PIN または生体認証ロックがデバイスに必要です。 携帯電話がロック解除状態のままにされている場合、アプリは、電話でのサインインを有効にする前に、ロックを設定するよう求めるメッセージを表示します。 

3. Microsoft アカウントのパスワードを通常入力するほとんどのページには、**[代わりにアプリを使用する]** というリンクがあります。 携帯電話でサインインするには、このリンクを選びます。 

4. Microsoft から携帯電話に通知が送信されます。 通知を承認してアカウントにサインインします。   

## <a name="faq"></a>FAQ 

### <a name="how-is-signing-in-with-my-phone-more-secure-than-typing-a-password"></a>携帯電話でのサインインは、パスワードの入力よりどの程度安全ですか。  

現在、ほとんどの人は、ユーザー名とパスワードを使って Web サイトやアプリにサインインします。  残念なことに、パスワードは、なくしたり、盗まれたり、ハッカーに推測されたりすることがよくあります。 Microsoft Authenticator アプリをサインイン用にセットアップすると、アカウントをロック解除できるキーが携帯電話上に生成されます。 このキーは、携帯電話で既に使われている PIN または生体認証によって保護されます。  形態電話でサインインするときは、このキーを使うことで、携帯電話自体およびロックを解除するユーザーの能力という&2; つの要素によって、ユーザーの ID が安全に証明されます。 

使われるキーは、Windows Hello および FIDO Alliance UAF の仕様で使われるキーと似ています。 生体認証データは、ローカルにキーを保護するためだけに使われ、クラウドに保存または送信されることはありません。 
 
### <a name="where-can-i-use-my-phone-to-replace-my-password-and-where-would-i-still-need-the-password"></a>パスワードの代わりに携帯電話を使えるのはどのような場合で、パスワードを使う必要があるのはどのような場合ですか。  

現在、携帯電話でのサインイン機能を使用できるのは、個人の Microsoft アカウントを利用している Web アプリとサービス、個人の Microsoft アカウントを使っている iOS アプリまたは Android アプリ、個人の Microsoft アカウントを使っている Windows 10 上のアプリだけです。 これらの Web サイトまたはアプリにサインインするときは、通常であればパスワードを入力するページに、**[代わりにアプリを使用する]** というリンクが表示されます。 

現時点では、Windows PC、XBOX、デスクトップ バージョンの Microsoft アプリ (Office アプリなど) を、携帯電話によるサインインを使ってロック解除することはできません。 
 
### <a name="does-this-replace-two-step-verification-should-i-turn-it-off"></a>この機能は&2; 段階認証に代わるものですか。 2 段階認証を向こうにする必要がありますか。   

場合によります。 携帯電話によるサインインを使用できる範囲を広げる作業を行っていますが、Microsoft のエコシステムの中にはこの機能をサポートしない部分がまだ存在します。 そのような場所では、サインインのセキュリティ保護に&2; 段階認証がまだ使われています。 そのため、アカウントの&2; 段階認証は無効にしないでください。 
 
### <a name="okay-if-i-keep-two-step-verification-turned-on-for-my-account-will-i-have-to-approve-two-notifications"></a>アカウントの&2; 段階認証を有効にしたままにする場合、2 つの通知を承認する必要がありますか。

いいえ、ありません。 携帯電話による Microsoft アカウントへのサインインは、2 段階認証とみなされます。 パスワードを入力してから通知を承認する代わりに、携帯電話のロック解除方法を知っていることで ID を証明し、その後で通知を承認します。 承認のための通知が&2; 回送信されることはありません。

### <a name="what-if-i-lose-my-phone-or-dont-have-it-with-me-how-can-i-access-my-account"></a>電話をなくした場合、または電話が手元にない場合は、どのようにしてアカウントにアクセスできますか。  

サインイン ページの **[代わりにパスワードを使用する]** をクリックすればいつでも、パスワードの使用に戻すことができます。 2 段階認証を使っている場合は、やはり第&2; の方法でサインインを確認する必要があることに注意してください。 そのため、余分な最新のセキュリティ情報をアカウントに保持することを強くお勧めします。 セキュリティ情報は https://account.live.com/proofs/manage で管理できます。 
 
### <a name="how-do-i-stop-using-this-feature-and-go-back-to-entering-my-password"></a>この機能の使用を停止してパスワードの入力に戻すにはどうすればよいですか。

サインインするときに、**[代わりにパスワードを使用する]** をクリックします。 最新の選択内容が記憶されているので、次にサインインするときは既定でパスワード入力になります。 携帯電話でのサインインに戻したい場合は、**[代わりにアプリを使用する]** をクリックします。 
 
### <a name="can-i-use-the-app-to-sign-in-to-all-my-accounts-with-microsoft"></a>アプリを使って、Microsoft のすべてのアカウントにサインインできますか。   
現時点では、この機能は個人の Microsoft アカウントでのみ使用できます。 
 
### <a name="can-i-sign-into-my-pc-with-my-phone"></a>携帯電話で PC にサインインできますか。  
PC の場合は、Windows 10 で顔、指紋、または PIN を使って Windows Hello によりサインインすることをお勧めします。   
 
### <a name="can-i-sign-in-with-my-windows-phone"></a>Windows Phone でサインインできますか。  
現時点では、Windows Phone の Microsoft Authenticator 用のこの機能は開発されていません。 

## <a name="next-steps"></a>次のステップ
Microsoft Authenticator アプリをダウンロードしていない場合は、ダウンロードしてください。 このアプリは [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071) で使用でき、携帯電話でのサインインは [Android](http://go.microsoft.com/fwlink/?Linkid=825072) および [IOS](http://go.microsoft.com/fwlink/?Linkid=825073) 用の Microsoft Authenticator アプリで使用できます。

アプリに関する一般的な質問については、「[Microsoft Authenticator アプリに関する FAQ](microsoft-authenticator-app-faq.md)」をご覧ください。



<!--HONumber=Feb17_HO2-->


