---
title: "2 段階認証のトラブルシューティング |Microsoft Docs"
description: "このドキュメントでは、Azure Multi-factor Authentication で問題が発生した場合の処置に関する情報をユーザーに提供します。"
services: multi-factor-authentication
keywords: "多要素認証のクライアント, 認証の問題, 関連付け ID"
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 8f3aef42-7f66-4656-a7cd-d25a971cb9eb
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 4ff028f88e984f28bc0f4a228aabed1fabc90560
ms.openlocfilehash: 2eab76ba42955616ae4d6909c1568f03da0c1819


---
# <a name="having-trouble-with-two-step-verification"></a>2 段階認証で発生する問題
この記事では、2 段階認証で発生する可能性がある問題について説明します。 発生している問題がここに含まれていない場合は、コメント セクションで詳細なフィードバックをご提供ください。改善に役立てます。

## <a name="i-lost-my-phone-or-it-was-stolen"></a>電話を紛失した場合や電話が盗まれた場合
アカウントにアクセスする方法は 2 つあります。 1 つ目の方法は、代替認証電話番号を設定しているのであれば、その番号を使用することです。 2 つ目の方法は、管理者に設定をクリアしてもらうことです。

電話を紛失した場合や電話が盗まれた場合は、アプリ パスワードのリセットと記憶されたすべてのデバイスのクリアも管理者に依頼することをお勧めします。 これらの実行方法を管理者がわからない場合は、[ユーザーとデバイスの管理](../multi-factor-authentication-manage-users-and-devices.md)に関する記事を示してください。

### <a name="use-an-alternate-phone-number"></a>代替電話番号を使用する
複数の検証オプション (セカンダリ電話番号や別のデバイス上の認証アプリなど) を設定している場合は、いずれかのオプションを使用してサインインできます。

代替電話番号を使用してサインインするには、次の手順に従います。

1. 通常どおりにサインインします。
2. アカウントをさらに検証するメッセージが表示されたら、**[別の確認オプションを使用する]** を選択します。
   
    ![Different Verification](./media/multi-factor-authentication-end-user-manage/differentverification.png)
3. アクセスできる電話番号を選択します。
   
    ![Alternate phone](./media/multi-factor-authentication-end-user-manage/altphone2.png)
4. アカウントにアクセスできたら、[設定の管理](multi-factor-authentication-end-user-manage-settings.md)を行って認証電話番号を変更します。

> [!IMPORTANT]
> セカンダリ認証用電話番号を構成することは重要です。 プライマリ電話番号を持つ電話にモバイル アプリがインストールされている場合は、電話の紛失や盗難に備えて 3 つ目のオプションを設定する必要があります。   

### <a name="clear-your-settings"></a>設定をクリアする
セカンダリ認証電話番号を構成していない場合は、管理者に連絡して、 設定をクリアしてもらう必要があります。これにより、次回のサイン時に[アカウントの設定](multi-factor-authentication-end-user-first-time.md)を求めるメッセージが再び表示されます。

## <a name="i-am-not-receiving-a-text-or-call-on-my-phone"></a>電話でテキストまたは通話呼び出しを受け取ることができない場合
サインインしようとしたが、テキストまたは通話呼び出しを受け取れない理由はいくつかあります。 これまで、同じ電話でテキストまたは通話を正常に受け取っている場合は、アカウントではなく携帯電話会社に問題がある可能性があります。 電波状態が良いことを確認します。テキスト メッセージを受信する場合は、電話とサービス プランでテキスト メッセージがサポートされていることを確認します。

数分待ってもテキストも通話呼び出しも着信しない場合、アカウントにアクセスするための最も簡単な方法は、別のオプションを試すことです。

1. 検証を待っているページで、**[別の確認オプションを使用する]** を選択します。
   
    ![Different Verification](./media/multi-factor-authentication-end-user-troubleshoot/diff_option.png)
2. 使用する電話番号または配信方法を選択します。
   
    複数の確認コードを受信した場合は、最新のコードだけが有効です。

別の方法を構成していない場合は、管理者に連絡して設定のクリアを依頼します。 次回のサインイン時に、[多要素認証の設定](multi-factor-authentication-end-user-first-time.md)を求めるメッセージが再び表示されます。

電波状態が悪いために時間がかかる場合は、スマート フォンで[Microsoft Authenticator アプリ](microsoft-authenticator-app-how-to.md)を使用することをお勧めします。 サインインするために使用するランダムなセキュリティコードをアプリで生成できます。これらのコードでは、携帯電話の信号もインターネット接続も必要ありません。

## <a name="app-passwords-are-not-working"></a>アプリ パスワードが機能しない場合
最初に、アプリ パスワードが正しく入力されていることを確認します。  それでも機能しない場合は、サインインを試し、 [新しいアプリ パスワードを作成](multi-factor-authentication-end-user-app-passwords.md)します。  うまくいかない場合は、管理者に連絡して[既存のアプリ パスワードの削除](../multi-factor-authentication-manage-users-and-devices.md)を依頼します。その後、新しいパスワードを作成できます。

## <a name="i-didnt-find-an-answer-to-my-problem"></a>問題に対する回答が見つからなかった場合
トラブルシューティング手順を試したが、問題が解決されない場合は、管理者または多要素認証を設定した人に問い合わせて、 助言を求めてください。

[Azure AD フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=WindowsAzureAD)に質問を投稿するか、[サポートに問い合わせる](https://support.microsoft.com/contactus)こともできます。できる限り早く回答いたします。

サポートにお問い合わせいただく際は、次の情報をお知らせください。

* **ユーザー ID** – サインインするために使用した電子メール アドレスは何ですか。
* **エラーの一般的な説明** – どのようなエラー メッセージが表示されましたか。  エラー メッセージが表示されなかった場合は、気が付いた予期しない動作について詳しく説明してください。
* **ページ** – エラーが表示されたときに、どのページを表示していましたか (URL を含む)。
* **エラー コード** - 受信した特定のエラー コード。
* **セッション ID** - 受信した特定のセッション ID。
* **関連付け ID** – エラーが表示されたときに生成された関連付け ID コード。
* **タイムスタンプ** – エラーが表示された正確な日時 (タイムゾーンを含む)。

情報の大部分は、サインイン ページで確認できます。 サインイン時に確認していない場合は、**[詳細の表示]** を選択します。

![サインイン エラーの詳細](./media/multi-factor-authentication-end-user-troubleshoot/view_details.png)

これらの情報を含めることで、迅速に問題を解決するのに役立ちます。

## <a name="related-topics"></a>関連トピック
* [2 段階認証設定の管理](multi-factor-authentication-end-user-manage-settings.md)  
* [Microsoft Authenticator アプリケーションに関する FAQ](microsoft-authenticator-app-faq.md)




<!--HONumber=Dec16_HO4-->


