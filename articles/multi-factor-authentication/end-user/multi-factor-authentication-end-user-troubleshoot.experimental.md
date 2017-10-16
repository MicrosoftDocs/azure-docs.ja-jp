---
title: "2 段階認証のトラブルシューティング |Microsoft Docs"
description: "このドキュメントでは、Azure Multi-factor Authentication で問題が発生した場合の処置に関する情報をユーザーに提供します。"
services: multi-factor-authentication
keywords: "多要素認証のクライアント, 認証の問題, 関連付け ID"
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 8f3aef42-7f66-4656-a7cd-d25a971cb9eb
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2017
ms.author: kgremban
ms.reviewer: yossib
ms.custom: end-user
ms.openlocfilehash: 9dbe88a59b68bfb424c43dd89acf55d8c73fdf39
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="get-help-with-two-step-verification"></a>2 段階認証のサポートを受ける
この記事では、2 段階認証に関してお客様から寄せられる最も一般的な質問の回答を掲載しています。 

## <a name="why-do-i-have-to-perform-two-step-verification-can-i-turn-it-off"></a>2 段階認証を実行しなければならない理由を教えてください。 無効にできますか。

2 段階認証は、組織によってユーザー アカウントを保護するために使用が選択されたセキュリティ機能です。 ユーザーが知っているものとユーザーが所有するものの 2 つの形式の認証に依存するため、パスワードだけよりも安全性が高くなります。 ユーザーが知っているものは、パスワードです。 ユーザーが所有するものは、ユーザーが通常携帯している電話やデバイスです。 2 段階認証でアカウントが保護されると、悪意のあるハッカーが何らかの手段でユーザーのパスワードを入手したとしてもユーザー本人としてサインインすることはできません。 ハッカーは、ユーザーの電話にアクセスできないため、サインインできません。 

Microsoft では 2 段階認証を提供していますが、この機能の使用は組織によって選択されます。 IT 部門によって必須に設定された場合は、アカウントを保護するパスワードの使用をユーザーが無効にできないのと同様に、これを無効にすることはできません。 

Microsoft の個人用アカウントで 2 段階認証が有効になっており、この設定を変更したい場合は、代わりに「[2 段階認証について](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification)」をご覧ください。 

## <a name="i-dont-have-my-phone-with-me-today"></a>今日は、自分の携帯電話が手元にありません

携帯電話を自宅に忘れたものの、職場でサインインする必要がある場合があります。 最初に、別の確認方法でのサインインを試してください。 2 段階認証を登録したときに、複数の電話番号を設定しましたか。 別の方法でサインインしてみるには、次の手順に従います。

1. 通常どおりにサインインします。
2. 2 段階認証のページが開いたら、**[別の確認オプションを使用する]** を選択します。

   ![Different Verification](./media/multi-factor-authentication-end-user-troubleshoot/diff_option.png)

3. 使用する確認オプションを選択します。 
  - 代替方法にもアクセスできない場合は、IT 部門に連絡してアカウントへのサインインのサポートを受けてください。
  - 代替方法にアクセスできる場合は、2 段階認証を続行します。

**[別の確認オプションを使用する]** のリンクが表示されない場合は、2 段階認証を最初に登録したときに、代替方法を設定しなかったことを意味します。 IT 部門に連絡して、アカウントへのサインインのサポートを受けてください。 サインインしたら、[設定を管理](multi-factor-authentication-end-user-manage-settings.md)し、次回に備えて別の確認方法を加えてください。 

## <a name="i-lost-my-phone-or-got-a-new-number"></a>電話を紛失、または新しい電話番号を取得しました
アカウントにアクセスする方法は 2 つあります。 1 つ目の方法は、代替認証電話番号を設定しているのであれば、その番号を使用することです。 2 つ目の方法は、IT 部門に設定をクリアしてもらうことです。

電話を紛失したり盗まれたりした場合は、IT 部門に連絡することをお勧めします。 IT 部門は、アプリのパスワードをリセットし、記憶されたデバイスをクリアする必要があります。 

### <a name="use-an-alternate-phone-number"></a>代替電話番号を使用する
複数の検証オプション (セカンダリ電話番号や別のデバイス上の認証アプリなど) を設定している場合は、いずれかのオプションを使用してサインインします。

代替電話番号を使用してサインインするには、次の手順に従います。

1. 通常どおりにサインインします。
2. アカウントをさらに検証するメッセージが表示されたら、**[別の確認オプションを使用する]** を選択します。
   
   ![Different Verification](./media/multi-factor-authentication-end-user-troubleshoot/diff_option.png)

3. アクセスできる電話番号またはデバイスを選択します。
4. アカウントにアクセスできたら、[設定の管理](multi-factor-authentication-end-user-manage-settings.md)を行って認証電話番号を変更します。

### <a name="clear-your-settings"></a>設定をクリアする
セカンダリ認証電話番号を構成していない場合は、IT 部門に連絡して、 設定をクリアしてもらう必要があります。これにより、次回のサインイン時に [2 段階認証の登録](multi-factor-authentication-end-user-first-time.md)を求めるメッセージが再び表示されます。

## <a name="i-am-not-receiving-a-text-or-call-on-my-phone"></a>電話でテキストまたは通話呼び出しを受け取ることができない場合
サインインしようとしたが、テキストまたは通話呼び出しを受け取れない理由はいくつかあります。 これまで、同じ電話でテキストまたは通話を正常に受け取っている場合は、アカウントではなく携帯電話会社に問題がある可能性があります。 電波状態が良いことを確認します。 テキスト メッセージを受信する場合は、テキスト メッセージの受信がサポートされていることを確認します。 友人に依頼して、テストとして通話呼び出しまたはテキストを送信してもらいます。 

数分待ってもテキストも通話呼び出しも着信しない場合、アカウントにアクセスするための最も簡単な方法は、別のオプションを試すことです。

1. 検証を待っているページで、**[別の確認オプションを使用する]** を選択します。
   
    ![Different Verification](./media/multi-factor-authentication-end-user-troubleshoot/diff_option.png)
2. 使用する電話番号または配信方法を選択します。
   
    複数の確認コードを受信した場合は、最新のコードを使用してください。

別の方法を構成していない場合は、IT 部門に連絡して設定のクリアを依頼します。 次回のサインイン時に、[多要素認証の設定](multi-factor-authentication-end-user-first-time.md)を求めるメッセージが再び表示されます。

電波状態が悪いために時間がかかる場合は、スマート フォンで[Microsoft Authenticator アプリ](microsoft-authenticator-app-how-to.md)を使用することをお勧めします。 サインインするために使用するランダムなセキュリティコードをアプリで生成できます。これらのコードでは、携帯電話の信号もインターネット接続も必要ありません。

## <a name="app-passwords-are-not-working"></a>アプリ パスワードが機能しない場合
最初に、アプリ パスワードが正しく入力されていることを確認します。 2 段階認証をサポートしていない古いバージョンのデスクトップ アプリケーションに限り、生成されたアプリ パスワードによって通常のパスワードが置き換えられます。 それでもうまくいかない場合は、サインインして[新しいアプリ パスワードを作成](multi-factor-authentication-end-user-app-passwords.md)してみてください。  それでもうまくいかない場合は、IT 部門に連絡して[既存のアプリ パスワードの削除](../multi-factor-authentication-manage-users-and-devices.md)を依頼します。その後、新しいパスワードを作成できます。

## <a name="i-didnt-find-an-answer-to-my-problem"></a>問題に対する回答が見つからなかった場合
トラブルシューティングのこれらの手順を試してもまだ問題が発生する場合は、IT 部門に連絡して、 助言を求めてください。

## <a name="related-topics"></a>関連トピック
* [2 段階認証設定の管理](multi-factor-authentication-end-user-manage-settings.md)  
* [Microsoft Authenticator アプリケーションに関する FAQ](microsoft-authenticator-app-faq.md)

