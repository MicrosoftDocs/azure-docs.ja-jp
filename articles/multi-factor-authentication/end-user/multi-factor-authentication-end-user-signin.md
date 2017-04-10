---
title: "2 段階認証を使用した Azure MFA サインイン エクスペリエンス | Microsoft Docs"
description: "このページでは、Azure MFA で利用可能な各種サインイン方法を確認できる参照先情報を示します。"
keywords: "ユーザー認証, サインイン エクスペリエンス, 携帯電話でのサインイン, 会社の電話でのサインイン"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: pblachar
ms.assetid: b310b762-471b-4b26-887a-a321c9e81d46
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/02/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: e972446ea92e8fd31406c9ccff7832b7441f3a2a
ms.lasthandoff: 03/04/2017


---
# <a name="the-sign-in-experience-with-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication を使用したサインイン エクスペリエンス
> [!NOTE]
> この記事では、一般的なサインイン エクスペリエンスを紹介しています。 サインインに関するヘルプやトラブルシューティングについては、「[2 段階認証で発生する問題](multi-factor-authentication-end-user-troubleshoot.md)」を参照してください。

## <a name="what-will-your-sign-in-experience-be"></a>さまざまなサインイン エクスペリエンス
2 番目の要素として使うものに何を選ぶかにより (電話、認証アプリ、またはテキスト)、サインイン エクスペリエンスが異なります。 お客さまの操作内容に最も近いものを選択してください。

| サインインの方法 | 
| --- |
| [携帯電話また職場の電話の呼び出しを使用してサインインする](#signing-in-with-a-phone-call) |
| [携帯電話へのテキストを使用してサインインする](#signing-in-with-a-text-message)
| [Microsoft Authenticator アプリの通知を使用してサインインする](#signing-in-with-the-microsoft-authenticator-app-using-notification) |
| [Microsoft Authenticator アプリの確認コードを使用してサインインする](#signing-in-with-the-microsoft-authenticator-app-using-verification-code) |
| [通常の方法を使用できないので、別の方法でサインインする](#signing-in-with-an-alternate-method) |

## <a name="signing-in-with-a-phone-call"></a>電話の呼び出しを使用してサインインする
ユーザーの携帯電話または職場の電話への呼び出しによる&2; 段階認証のエクスペリエンスは次のとおりです。

1. Office 365 などのアプリケーションまたはサービスにユーザー名とパスワードを使用してサインインします。  
2. Microsoft がユーザーに電話をかけます。  
3. 電話に出て、# キーを押します。  
4. これでサインインできます。  

## <a name="signing-in-with-a-text-message"></a>テキスト メッセージを使用してサインインする
ユーザーの携帯電話へのテキスト メッセージによる&2; 段階認証のエクスペリエンスは次のとおりです。

1. Office 365 などのアプリケーションまたはサービスにユーザー名とパスワードを使用してサインインします。 
2. Microsoft からユーザーに、番号コードを含むテキスト メッセージが送信されます。 
3. サインイン ページのボックスにコードを入力します。 
4. これでサインインできます。 

## <a name="signing-in-with-the-microsoft-authenticator-app"></a>Microsoft Authenticator アプリを使用してサインインする 
2 段階認証に Microsoft Authenticator アプリを使用するエクスペリエンスは次のとおりです。 アプリを使用したサインインの方法は&2; 種類あります。 デバイスでプッシュ通知を受け取るか、アプリを開いて確認コードを取得します。

### <a name="to-sign-in-with-a-notification-from-the-microsoft-authenticator-app"></a>Microsoft Authenticator アプリの通知を使用してサインインするには
1. Office 365 などのアプリケーションまたはサービスにユーザー名とパスワードを使用してサインインします。
2. Microsoft がお客様のデバイス上の Microsoft Authenticator アプリに通知を送信します。

  ![Microsoft が通知を送信する](./media/multi-factor-authentication-end-user-signin/notify.png)

3. お客様の電話で通知を開き、**確認**キーを選択します。 PIN の入力が必要な企業の場合は、ここで入力します。
4. これでサインインできます。

### <a name="to-sign-in-using-a-verification-code-with-the-microsoft-authenticator-app"></a>Microsoft Authenticator アプリと確認コードを使用してサインインするには

Microsoft Authenticator アプリを使用して確認コードを取得する場合、アプリを開くとアカウント名の下に番号が表示されます。 この番号は 30 秒ごとに変更されるので、同じ番号を再度使用することはありません。 確認コードを求められたらアプリを開き、そのとき表示されている番号を入力してください。 

1. Office 365 などのアプリケーションまたはサービスにユーザー名とパスワードを使用してサインインします。
2. Microsoft によって確認コードを求めるプロンプトが表示されます。

  ![検証コードを入力する](./media/multi-factor-authentication-end-user-signin/verify3.png)

3. お客様の電話で Microsoft Authenticatior アプリを開き、サインインのためのボックスにコードを入力します。
4. これでサインインできます。

## <a name="signing-in-with-an-alternate-method"></a>別の方法を使用してサインインする
場合によっては、通常の確認方法として設定した電話やデバイスを使用できないこともあります。 アカウントにバックアップの方法を設定することをお勧めするのはそのためです。 次のセクションでは、主要な方法が使用できないときに、別の方法でサインインする方法を示します。

1. Office 365 などのアプリケーションまたはサービスにユーザー名とパスワードを使用してサインインします。
2. **[別の確認オプションを使用する]** を選択します。 設定済みの確認オプションが表示されます。

  ![別の方法を使用する](./media/multi-factor-authentication-end-user-signin/alt.png)

3. 代替方法を選択し、サインインします。

## <a name="next-steps"></a>次のステップ

2 段階認証を使用したサインインに問題がある場合は、「[2 段階認証で発生する問題](multi-factor-authentication-end-user-troubleshoot.md)」を参照してください。

2 段階認証設定を管理する方法については、「[2 段階認証設定の管理](multi-factor-authentication-end-user-manage-settings.md)」を参照してください。

SMS や電話を受ける代わりに通知を使用してサインインする方法については、「[Get started with the Microsoft Authenticator app (Microsoft Authenticator アプリの概要)](microsoft-authenticator-app-how-to.md)」を参照してください。 
