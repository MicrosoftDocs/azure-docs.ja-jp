---
title: Microsoft Authenticator アプリの概要 - Azure Active Directory | Microsoft Docs
description: Microsoft Authenticator アプリについて、その概要としくみのほか、コンテンツのこのセクションに記載されている情報を説明します。
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: overview
ms.date: 01/24/2019
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: d3766436851cb2f31fa7bb070402191e03ad9ad1
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2019
ms.locfileid: "68382341"
---
# <a name="what-is-the-microsoft-authenticator-app"></a>Microsoft Authenticator アプリとは

>[!Important]
>このコンテンツはユーザーを対象としています。 管理者の方は、Azure Active Directory (Azure AD) 環境の設定と管理方法の詳細については、「[Azure Active Directory のドキュメント](https://docs.microsoft.com/azure/active-directory)」を参照してください。

Microsoft Authenticator アプリは、2 要素認証を使用する場合に、アカウントにサインインするために役立ちます。 2 要素認証は、特に機密情報を閲覧する際に、アカウントにより安全にアクセスするために役立ちます。 パスワードは忘れたり、盗まれたり、侵害されたりすることがあるため、2 要素認証は、他のユーザーの侵入を困難にすることでお客様のアカウントの保護に役立つ追加のセキュリティ手順です。

Microsoft Authenticator アプリは次のような複数の方法で使用できます。

- ユーザー名とパスワードでサインインした後に、認証のためのプロンプトに応答する。

- ユーザー名、認証アプリ、およびモバイル デバイスで指紋、顔、または PIN を使用して、パスワードを入力せずにサインインする。

- 認証アプリをサポートする他のアカウント用のコード ジェネレーターとして使用する。

> [!Important]
> Microsoft Authenticator アプリは、2 要素認証を使用し、時間ベースのワンタイム パスワード (TOTP) 標準をサポートする任意のアカウントで動作します。
>
> 組織では、サインインして組織のデータやドキュメントにアクセスするために、認証アプリの使用がユーザーに要求されている場合があります。 アプリにユーザー名が表示される場合でも、登録プロセスを完了するまで、アカウントは検証方法として機能するように実際に設定されてはいません。 詳しくは、「[職場または学校のアカウントを追加する](user-help-auth-app-add-work-school-account.md)」をご覧ください。
> 
> アカウントへのサインインに問題がある場合は、「[Microsoft アカウントにサインインできない場合](https://support.microsoft.com/help/12429)」をヘルプとして参照してください。 Microsoft アカウントへのサインイン試行時に ["指定した Microsoft アカウントは登録されていません"](https://support.microsoft.com/help/13811) というメッセージが表示される場合の対処方法を、詳しく確認してください。

## <a name="terminology"></a>用語集

| 期間|説明|
| ----|-----------|
| 2 要素認証 | わずか 2 種類の認証情報 (パスワードと PIN など) を特に使用しなければならない認証プロセスです。 Microsoft Authenticator アプリでは、標準の 2 要素認証とパスワードなしのサインインの両方がサポートされます。 |
| Multi-Factor Authentication (MFA) | すべての 2 要素認証は多要素認証です。お客様の組織の要件に基づいて、"*少なくとも*" 2 種類の認証情報を使用することが求められます。 |
| Microsoft アカウント (別称: MSA) | コンシューマー向けの Microsoft 製品およびクラウド サービス (Outlook、OneDrive、Xbox LIVE、Office 365 など) にアクセスするために、お客様が自分の個人アカウントを作成します。 お使いの Microsoft アカウントは、Microsoft が運営する Microsoft コンシューマー ID アカウント システムを使用して作成、保存されます。 |
| 会社または学校のアカウント | 内部リソースおよび制限付きの可能性があるリソース (Microsoft Azure、Windows Intune、Office 365 など) へのアクセスをお客様に許可するために、お客様の組織が職場または学校アカウント (alain@contoso.com など) を作成します。 |
| 確認コード | 認証アプリで、追加された各アカウントの下に表示される 6 桁のコードです。 他のユーザーによってコードが複数回使用されないように、確認コードは 30 秒ごとに変更されます。 これは、ワンタイム パスコード (OTP) とも呼ばれます。 |

## <a name="how-two-factor-verification-works-with-the-app"></a>アプリでの 2 要素認証のしくみ

Microsoft Authenticator アプリでは、2 要素認証が次の方法で機能します。

- **通知**。 職場または学校アカウントあるいは個人の Microsoft アカウントのいずれかについて、お客様がログインするデバイスにユーザー名とパスワードを入力します。その後、Microsoft Authenticator アプリから、**サインインの承認**をお客様に求める通知が送信されます。 サインインを承認する場合は **[承認]** を選択します。 承認しない場合は、 **[拒否]** を選択します。 **[拒否]** を選択すると、要求を不正としてマークすることもできます。

- **確認コード**。 職場または学校アカウントあるいは個人の Microsoft アカウントのいずれかについて、お客様がログインするデバイスにユーザー名とパスワードを入力します。その後、関連付けられている確認コードを Microsoft Authenticator アプリの **[アカウント]** 画面からコピーします。 確認コードは、ワンタイム パスコード (OTP) 認証とも呼ばれます。

- **パスワードなしのサインイン。** 職場または学校アカウントあるいは個人の Microsoft アカウントのいずれかについて、お客様がログインするデバイスにユーザー名とパスワードを入力します。その後、モバイル デバイスを使って、自分の指紋、顔、または PIN を使用して本人確認を行います。 この方法では、パスワードを入力する必要がありません。

### <a name="whether-to-use-your-devices-biometric-capabilities"></a>デバイスの生体認証機能を使用するかどうか

PIN を使用して認証プロセスを完了する場合、代わりにお客様のデバイスの指紋認識機能または顔認識機能 (生体認証) を使用するよう、Microsoft Authenticator アプリを設定できます。 これを設定するには、認証アプリでの初回のアカウント確認時に、デバイスの生体認証機能を (PIN の代わりに) ID として使用するオプションを選択します。

## <a name="who-decides-if-you-use-this-feature"></a>この機能の使用を決定するユーザー

アカウントの種類に応じて、2 要素認証を使用しなければならないことをお客様の組織が決定する場合、またはお客様が自分で決定できる場合があります。

- **職場または学校のアカウント。** 職場または学校アカウント (例: alain@contoso.com) を使用している場合、お客様が 2 要素認証を使用しなければならないかどうかは、特定の認証方法と共にお客様の組織が決定します。 Microsoft Authenticator アプリへの職場または学校アカウントの追加について詳しくは、「[職場または学校のアカウントを追加する](user-help-auth-app-add-work-school-account.md)」をご覧ください。

- **個人の Microsoft アカウント。** 個人の Microsoft アカウント (例: alain@outlook.com) の場合、2 要素認証の設定を選択できます。 個人の Microsoft アカウントの追加について詳しくは、[個人用アカウントの追加](user-help-auth-app-add-personal-ms-account.md)に関するページを参照してください。

- **非 Microsoft アカウント。** Microsoft 以外のアカウント (例: alain@gmail.com) に対して 2 要素認証を設定できます。 Microsoft 以外の個人アカウントでは 2 要素認証という用語が使用されていない可能性がありますが、**セキュリティ**設定内または**サインイン**設定内で機能を見つけられるはずです。 Microsoft Authenticator アプリは、TOTP 標準をサポートする任意のアカウントで動作します。 Microsoft 以外のアカウントの追加について詳しくは、「[Microsoft 以外のアカウントを追加する](user-help-auth-app-add-non-ms-account.md)」を参照してください。

## <a name="in-this-section"></a>このセクションの内容

| 記事 | 説明 |
| ------ | ------------ |
| [アプリのダウンロードとインストール](user-help-auth-app-download-install.md) | Android および iOS が実行されている各デバイス用の Microsoft Authenticator アプリの入手場所、入手方法、およびインストール方法について説明します。 |
| [職場または学校アカウントの追加](user-help-auth-app-add-work-school-account.md) | さまざまな職場または学校アカウントと個人アカウントを Microsoft Authenticator アプリに追加する方法について説明します。 |
| [個人アカウントの追加](user-help-auth-app-add-personal-ms-account.md) | 個人の Microsoft アカウントを Microsoft Authenticator アプリに追加する方法について説明します。 |
| [Microsoft 以外のアカウントを追加する](user-help-auth-app-add-non-ms-account.md) | Microsoft 以外のアカウントを Microsoft Authenticator アプリに追加する方法について説明します。 |
| [手動によるアカウントの追加](user-help-auth-app-add-account-manual.md) | 提供された QR コードをスキャンできない場合に、お客様のアカウントを Microsoft Authenticator アプリに手動で追加する方法について説明します。 |
| [アプリを使用したサインイン](user-help-auth-app-sign-in.md) | Microsoft Authenticator アプリを使用して、さまざまアカウントにサインインする方法について説明します。|
| [アカウント資格情報のバックアップと復旧](user-help-auth-app-backup-recovery.md) | Microsoft Authenticator アプリを使用してアカウント資格情報をバックアップおよび復旧する方法について説明します。 |
| [Microsoft Authenticator アプリに関する FAQ](user-help-auth-app-faq.md) | アプリについてよく寄せられる質問とその回答を紹介します。 |
