---
title: "Microsoft Authenticator アプリのヘルプとサポート | Microsoft Docs"
description: "Microsoft 認証アプリと Multi-factor Authentication に関してよく寄せられる質問の一覧を提供します。"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: pblachar, librown
ms.assetid: f04d5bce-e99e-4f75-82d1-ef6369be3402
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/17/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 7c91ae265dd86e8d7f206c1ada021c40fd8c4479
ms.openlocfilehash: cb3b7cfefe22176e17064ad6cbb9df4da8c57c0f
ms.lasthandoff: 03/01/2017


---
# <a name="microsoft-authenticator-app-faq"></a>Microsoft Authenticator アプリに関する FAQ

この記事では、Microsoft Authenticator アプリに関する一般的な質問にお答えします。 質問に対する回答がここで見つからない場合は、[Microsoft Authenticator アプリ フォーラム](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp)をご覧ください。 アプリの特定の機能については、[携帯電話でのサインインに関する FAQ](microsoft-authenticator-app-phone-signin-faq.md) もご覧ください。

Microsoft Authenticator アプリは、Azure Authenticator アプリに置き換わり、Azure 多要素認証を使用するときに推奨されるアプリです。 Microsoft Authenticator アプリは、[Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071)、[Android](http://go.microsoft.com/fwlink/?Linkid=825072)、[IOS](http://go.microsoft.com/fwlink/?Linkid=825073) で利用できます。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="what-are-the-codes-in-the-app-for-why-does-the-number-keep-counting-down"></a>アプリのコードの目的は何ですか。 番号が常にカウントダウンされるのはなぜですか。

Microsoft Authenticator アプリを開くと、追加したアカウントが表示され、各アカウントのそばに&6; 桁または&8; 桁の番号が表示されます。 30 秒タイマーがカウントダウンしているのがわかります。

これらのコードは、アカウントにサインインするときに使用されます。 ユーザー名とパスワードを入力した後に、確認コードの入力を求められる場合があります。 Microsoft Authenticator アプリを開き、現在表示されているコードをコピーします。 サインイン ページにそのコードを入力すると、操作が完了します。

コードが 30 秒ごとに変更されるのは、同じコードを 2 回使用できないようにするためです。 パスワードとは異なり、コードを覚えておく必要はありません。 つまり、その携帯電話にアクセスできるユーザーにしか確認コードはわからないということです。

コードにはインターネットもデータも不要であるため、電話サービスへのサインインや、アプリがデータ プランを使い果たすことを心配する必要はありません。 アプリを閉じると、アプリがバックグラウンドで動作し続けることはないので、バッテリが消費されることはありません。 アプリを閉じたら、次回サインインするときまでアプリを無視できます。  

### <a name="im-already-using-the-microsoft-authenticator-application-for-verification-codes-how-do-i-switch-to-one-click-push-notifications"></a>検証コードで Microsoft Authenticator アプリケーションを既に使用しています。 ワンクリック プッシュ通知に切り替えるにはどうすればよいですか。
プッシュ通知によるサインインの承認は、個人の Microsoft アカウントと職場または学校の Microsoft アカウントでのみ可能であり、Google や Facebook などのサード パーティのアカウントでは使用できません。 職場または学校の Microsoft アカウントを使用している場合、所属している組織がこのオプションを無効にすることを選択している可能性があります。

Microsoft アカウントを個人のアカウントで使用しているときにプッシュ通知に切り替える場合は、自分のアカウントをもう一度追加する必要があります。 デバイスを自分のアカウントで再登録し、プッシュ通知を設定します。  

Microsoft Authenticator を職場または学校のアカウントで使用している場合、ワンクリック通知を許可するかどうかは所属している組織が決定します。

### <a name="do-one-click-push-notifications-work-for-non-microsoft-accounts"></a>ワンクリック プッシュ通知は Microsoft 以外のアカウントでも機能しますか。
残念ですが、プッシュ通知は、Microsoft アカウントと Azure Active Directory アカウントでのみ機能します。 職場または学校で Azure AD アカウントを使用している場合は、この機能を無効にしている可能性があります。  

### <a name="i-restored-my-device-from-a-backup-and-my-account-codes-are-missing-or-not-working-what-happened"></a>デバイスをバックアップから復元しましたが、アカウント コードが見つからないか、機能していません。 なぜでしょうか?
セキュリティのため、アプリのバックアップからアカウントは復元されません。  アプリケーションを復元したら、アカウントを削除し、もう一度追加してください。

### <a name="i-got-a-new-device-how-do-i-remove-the-microsoft-authenticator-app-from-my-old-device-and-move-to-the-new-one"></a>新しいデバイスを取得しました。 古いデバイスから Microsoft Authenticator アプリを削除して新しいデバイスに移すにはどうすればよいですか。
新しいデバイスに Microsoft Authenticator アプリを追加しても、他のデバイスからそのアプリが自動的に削除されることはありません。 アカウントで構成されるデバイスを管理するには、2 段階認証を管理するために使用するのと同じ Web サイトにアクセスして、古いアプリの削除を選択してください。

個人の Microsoft アカウントでは、この Web サイトは、[アカウント セキュリティ](https://account.microsoft.com/security) ページです。 職場または学校の Microsoft アカウントでは、この Web サイトは、[MyApps](https://myapps.microsoft.com) または所属する組織がセットアップしたカスタム ポータルのいずれかです。

### <a name="how-do-i-remove-an-account-from-the-app"></a>アプリからアカウントを削除するにはどうすればよいですか。
* iOS: メイン画面で、アカウント タイルを左にスワイプします。 **[削除]**を選択します。
* Windows Phone: メイン画面で、メニュー ボタンを選択し、**[アカウントの編集]** を選択します。 アカウント名の横にある **[X]** をタップします。
* Android: メイン画面で、メニュー ボタンを選択し、**[アカウントの編集]** を選択します。 アカウント名の横にある **[X]** をタップします。

所属している組織に登録されているデバイスの場合、自分のアカウントを削除するには、追加手順の完了が必要な可能性があります。 これらのデバイスでは、Microsoft Authenticator アプリがデバイスの管理者として自動的に登録されます。 アプリを完全にアンインストールする場合は、その前にアプリ設定でアプリの登録を解除する必要があります。

### <a name="why-does-the-app-request-so-many-permissions"></a>アプリで多くのアクセス許可が要求されるのはなぜですか。
要求されるアクセス許可の完全な一覧と、アプリでの用途を次に示します。 表示される具体的なアクセス許可は、電話の種類によって異なります。

* **カメラ**: 職場のアカウント、学校のアカウント、または Microsoft 以外のアカウントを追加すると、カメラを使用して QR コードがスキャンされます。
* **連絡先と電話番号**: 個人の Microsoft アカウントでサインインすると、電話で使用されている既存のアカウントを検索することでプロセスの簡略化が図られます。
* **SMS**: 個人の Microsoft アカウントで初めてサインインすると、電話番号が記録されているものと一致することの確認が必要になります。 アプリをダウンロードした電話にテキスト メッセージが送信されます。 このメッセージには、6 ～ 8 桁の検証コードが含まれています。 このコードを見つけてアプリに入力する代わりに、テキスト メッセージ内のテキストが自動的に検索されます。
* **他のアプリの上に表示**: 本人確認通知を受信すると、他のアプリが実行されていれば、その上に通知が表示されます。
* **インターネットからのデータの受信**: このアクセス許可は、通知を送信するために必要です。
* **電話がスリープしないようにする**: 所属する組織にデバイスを登録している場合は、組織が電話に関するこのポリシーを変更している可能性があります。
* **振動の制御**: 本人確認通知を受信したときに、振動させるかどうかを選択できます。
* **指紋ハードウェアの使用**: 一部の職場と学校のアカウントでは、本人確認を行うときに、追加の PIN が必要です。 プロセスを簡単にするために、PIN を入力する代わりに指紋を使用できます。
* **ネットワーク接続の表示**: アプリに Microsoft アカウントを追加するにはネットワーク/インターネット接続が必要です。
* **ストレージの内容の読み取り**: このアクセス許可は、アプリ設定で技術的な問題を報告するときにのみ使用されます。 問題を診断するために、ストレージから情報が収集されます。
* **完全なネットワーク アクセス**: このアクセス許可は、本人確認通知を送信するために必要です。
* **スタートアップ時に実行**: このアクセス許可は、電話機を再起動した場合でも、引き続き本人確認通知を受信できるようにします。

### <a name="why-does-the-microsoft-authenticator-app-allow-you-to-approve-a-request-without-unlocking-the-device"></a>Microsoft Authenticator アプリで、デバイスのロックを解除せずに要求を承認できるのはなぜですか。

これは設計によるものです。 2 段階認証では、2 つのもの、つまりユーザーが知っているものとユーザーが持っているものを証明する必要があります。 知っているものは、パスワードです。 持っているものは、(Microsoft Authenticator アプリで設定され、MFA の確認手段として登録された) 電話です。したがって、電話を持っていて要求を承認することは、認証の&2; 番目の要素に関する条件を満たします。 

## <a name="next-steps"></a>次のステップ

### <a name="contact-us"></a>お問い合わせ
疑問点への答えがここで見つからなかった場合は、ぜひお知らせください。 [Microsoft Authenticator アプリ フォーラム](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp)に質問を投稿してコミュニティからのサポートを受けるか、このページにコメントを残してください。


### <a name="related-topics"></a>関連トピック
* Microsoft アカウントの [2 段階認証について](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification)
* 職場または学校のアカウントの [2 段階認証で問題が発生した場合](multi-factor-authentication-end-user-troubleshoot.md)
* [Microsoft Authenticator を使って携帯電話からサインインする](microsoft-authenticator-app-phone-signin-faq.md)


