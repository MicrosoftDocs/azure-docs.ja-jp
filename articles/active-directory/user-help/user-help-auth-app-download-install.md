---
title: Microsoft Authenticator アプリをダウンロードしてインストールする - Azure AD
description: 2 要素検証の使用中に ID を検証するために、Microsoft Authenticator アプリをダウンロードしてインストールします。
services: active-directory
author: curtand
manager: daveba
ms.assetid: 3065a1ee-f253-41f0-a68d-2bd84af5ffba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 05/31/2020
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: 5aac6c0b903a3b2adf481a2c63847272122ce2d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88798977"
---
# <a name="download-and-install-the-microsoft-authenticator-app"></a>Microsoft Authenticator アプリのダウンロードとインストール

>[!Important]
>このコンテンツはユーザーを対象としています。 管理者の方は、Azure Active Directory (Azure AD) 環境の設定と管理方法の詳細については、[Azure Active Directory の管理ドキュメント](../index.yml)を参照してください。
>
> アカウントへのサインインに問題がある場合は、「[Microsoft アカウントにサインインできない場合](https://support.microsoft.com/help/12429)」をヘルプとして参照してください。  また、Microsoft アカウントへのサインイン試行時に ["指定した Microsoft アカウントは登録されていません"](https://support.microsoft.com/help/13811) というメッセージが表示される場合の対処方法を、詳しく確認することができます。

Microsoft Authenticator アプリは、2 要素認証を使用する場合に、アカウントにサインインするために役立ちます。 2 要素認証を使用すると、アカウントをより安全に使用するのに役立ちます。これは、パスワードは忘れたり、盗まれたり、危害を受けたりするためです。 2 要素認証では、電話のような 2 つ目の要素を使用して、他のユーザーが自分のアカウントに侵入するのを困難にします。 Microsoft Authenticator アプリは次のような複数の方法で使用できます。

- **2 要素認証。** 標準の認証方法。その要素の 1 つはパスワードです。 ユーザー名とパスワードを使用したサインイン後、通知を承認するか、提供された認証コードを入力します。

- **電話によるサインイン。** 2 要素認証の 1 バージョン。ユーザー名と、指紋、顔、PIN によるモバイル デバイスを使用して、パスワードを必要とせずにサインインすることができます。

- **コードの生成。** 認証アプリをサポートする他のアカウント用のコード ジェネレーターとして使用します。

Authenticator は、2 要素認証を使用し、時間ベースのワンタイム パスワード (TOTP) 標準をサポートする任意のアカウントで動作します。

組織では、サインインして組織のデータやドキュメントにアクセスするために、Authenticator アプリの使用がユーザーに要求されている場合があります。 アプリにユーザー名が表示される場合でも、登録を完了するまで、アカウントは検証方法として実際に設定されていません。 詳しくは、「[職場または学校のアカウントを追加する](user-help-auth-app-add-work-school-account.md)」をご覧ください。

## <a name="download-and-install-the-app"></a>アプリのダウンロードとインストール

オペレーティング システムに基づいて、最新バージョンの Microsoft Authenticator アプリをインストールします。

- **Google Android**。 Android デバイスでは、Google Play に移動して、[Microsoft Authenticator アプリをダウンロードして、インストール](https://app.adjust.com/e3rxkc_7lfdtm?fallback=https%3A%2F%2Fplay.google.com%2Fstore%2Fapps%2Fdetails%3Fid%3Dcom.azure.authenticator)します。

- **Apple iOS**。 Apple iOS デバイスでは、App Store に移動して、[Microsoft Authenticator アプリをダウンロードして、インストール](https://app.adjust.com/e3rxkc_7lfdtm?fallback=https%3A%2F%2Fitunes.apple.com%2Fus%2Fapp%2Fmicrosoft-authenticator%2Fid983156458)します。

>[!Important]
>現在モバイル デバイスに接続していない場合でも、[Microsoft Authenticator のページ](https://www.microsoft.com/en-us/account/authenticator)から自分にダウンロード リンクを送信して Microsoft Authenticator アプリを入手できます。

## <a name="next-steps"></a>次のステップ

アプリをダウンロードしてインストールしたら、[Authenticator アプリの概要](user-help-auth-app-overview.md)を参照して、詳細を確認してください。 セットアップ オプションの詳細については、以下を参照してください。

- **認証アプリ。** 認証アプリをダウンロードして使用する場合、2 段階認証やパスワード リセットのために承認通知かランダムに生成された承認コードを取得できます。 Microsoft Authenticator アプリの設定方法と使用方法に関する段階的な説明が必要な場合は、「[認証アプリを使用するようにセキュリティ情報を設定する](security-info-setup-auth-app.md)」を参照してください。

- **モバイル デバイスのテキスト。** モバイル デバイスの番号を入力し、2 段階認証やパスワード リセットに使用するテキスト コードを取得します。 テキスト メッセージ (SMS) による本人確認方法に関する段階的な説明が必要な場合は、「[Set up security info to use text messaging (SMS)](security-info-setup-text-msg.md)」(テキスト メッセージ (SMS) を使用するようにセキュリティ情報を設定する) を参照してください。

- **モバイル デバイスまたは職場の電話の呼び出し。** モバイル デバイスの番号を入力し、電話の呼び出しで 2 段階認証やパスワード リセットを行います。 電話番号による本人確認方法に関する段階的説明が必要な場合、「[電話を使用するようにセキュリティ情報を設定する](security-info-setup-phone-number.md)」を参照してください。

- **セキュリティ キー。** Microsoft と互換性のあるセキュリティ キーを登録し、それを PIN と一緒に使用して、2 段階認証またはパスワードのリセットを行います。 セキュリティ キーによる本人確認方法に関する段階的説明が必要な場合、[セキュリティ キーを使用するようにセキュリティ情報を設定する](security-info-setup-security-key.md)方法に関するページを参照してください。

- **メール アドレス。** 職場または学校のメール アドレスを入力して、パスワードをリセットするためのメールを受け取ります。 このオプションは、2 段階認証用には使用できません。 メールの設定方法に関する段階的説明が必要な場合、「[Set up security info to use email (メールを使用するようにセキュリティ情報を設定する)](security-info-setup-email.md)」を参照してください。

- **セキュリティの質問。** 管理者が組織のために作成したいくつかのセキュリティの質問に答えます。 この選択肢はパスワード リセットの場合にのみ選択できます。2 段階認証には選択できません。 セキュリティの質問の設定方法に関する段階的な説明が必要な場合は、「[Set up security info to use security questions ](security-info-setup-questions.md)」(セキュリティの質問を使用するようにセキュリティ情報を設定する) を参照してください。