---
title: Microsoft Authenticator アプリの認証方法 - Azure Active Directory
description: Azure Active Directory で Microsoft Authenticator アプリを使用して、サインイン イベントの改善と安全性の確保に役立てる方法について説明します
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8e33c87d53580d96363ba15bccbc889370f2479d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102212911"
---
# <a name="authentication-methods-in-azure-active-directory---microsoft-authenticator-app"></a>Azure Active Directory の認証方法 - Microsoft Authenticator アプリ

Microsoft Authenticator アプリは、Azure AD の職場または学校アカウント、または Microsoft アカウントに対する追加のセキュリティ レベルとして機能し、[Android](https://go.microsoft.com/fwlink/?linkid=866594) と [iOS](https://go.microsoft.com/fwlink/?linkid=866594) で使用できます。 Microsoft Authenticator アプリを使用すると、ユーザーはサインイン時にパスワードなしの方法で認証したり、セルフサービス パスワード リセット (SSPR) または Azure AD Multi-Factor Authentication イベントの際に追加の検証オプションとして認証したりすることができます。

ユーザーは、モバイル アプリから通知を受け取って承認または拒否することも、Authenticator アプリを使用してサインイン インターフェイスに入力できる OAUTH 確認コードを生成することもできます。 通知コードと確認コードの両方を有効にすると、Authenticator アプリを登録したユーザーはいずれかの方法を使用して本人確認を行うことができます。

ユーザー名とパスワードの組み合わせではなく、サインイン プロンプトで Authenticator アプリを使用する方法については、「[Microsoft Authenticator アプリを使用したパスワードなしのサインインを有効にする](howto-authentication-passwordless-phone.md)」を参照してください。

> [!NOTE]
> SSPR を有効にしたときに、ユーザーにモバイル アプリを登録するオプションはありません。 代わりに、ユーザーは、[https://aka.ms/mfasetup](https://aka.ms/mfasetup) で、または統合されたセキュリティ情報の登録の一環として [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) で、モバイル アプリを登録できます。

## <a name="passwordless-sign-in"></a>パスワードなしのサインイン

ユーザー名の入力後にパスワードの入力を求めるプロンプトを表示するのではなく、Microsoft Authenticator アプリから電話によるサインインを有効にしているユーザーには、アプリで番号をタップするように伝えるメッセージが表示されます。 正しい番号を選択すると、サインイン プロセスは完了です。

![サインインを承認するようユーザーに求めている、ブラウザーでのサインインの例](./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png)

この認証方法によって高レベルのセキュリティが実現し、ユーザーがサインイン時にパスワードを入力する必要がなくなります。 

パスワードなしのサインインを開始するには、「[Microsoft Authenticator アプリを使用したパスワードなしのサインインを有効にする](howto-authentication-passwordless-phone.md)」を参照してください。

## <a name="notification-through-mobile-app"></a>モバイル アプリでの通知

Authenticator アプリは、スマートフォンまたはタブレットに通知をプッシュして、アカウントへの不正アクセスを防止したり、不正なトランザクションを停止させたりするのに役立ちます。 ユーザーは通知を確認し、適切であった場合は、 **[確認]** を選択します。 適切でない場合は、 **[拒否]** を選択します。

![サインイン プロセスを完了するために Authenticator アプリを Web ブラウザーで要求する例のスクリーンショット](media/tutorial-enable-azure-mfa/azure-multi-factor-authentication-browser-prompt.png)

> [!NOTE]
> 中国勤務または出張中のスタッフが組織にいる場合、その国/地域では、Android デバイスの "*モバイル アプリによる通知*" 方法は機能しません。これは、Google Play のサービス (プッシュ通知など) がその地域でブロックされているためです。 ただし、iOS の通知は機能します。 Android デバイスの場合、それらのユーザーが代替の認証方法を利用できるようにする必要があります。

## <a name="verification-code-from-mobile-app"></a>モバイル アプリからの確認コード

Authenticator アプリをソフトウェア トークンとして使用して、OATH 確認コードを生成できます。 ユーザー名とパスワードを入力したら、Authenticator アプリから提供されたコードをサインイン インターフェイスに入力します。 検証コードにより、2 番目の形式の認証が行われます。

ユーザーは、最大 5 つの OATH ハードウェア トークンまたはいつでも使用されるように構成された認証アプリケーション (Microsoft Authenticator アプリなど) を組み合わせることもできます。

> [!WARNING]
> セルフサービス パスワード リセットで、リセットに必要な方法が 1 つのみのときに最高レベルのセキュリティを確保する場合、ユーザーが使用できるオプションは確認コードのみです。
>
> 2 つの方法が必要な場合、ユーザーは、通知または確認コードのいずれかと、他の有効な方法を使用して、リセットを行うことができます。

## <a name="next-steps"></a>次の手順

パスワードなしのサインインを開始するには、「[Microsoft Authenticator アプリを使用したパスワードなしのサインインを有効にする](howto-authentication-passwordless-phone.md)」を参照してください。

[Microsoft Graph REST API ベータ版](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta)を使用した認証方法の構成の詳細について確認してください。
