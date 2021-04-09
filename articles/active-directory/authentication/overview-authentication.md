---
title: Azure Active Directory 認証の概要
description: Azure Active Directory を使用したユーザー サインインに関するさまざまな認証方法とセキュリティ機能について説明します。
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: overview
ms.date: 01/22/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: sahenry, michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6f4659b9ee809cc1f1caeb1cb9c0d626b1b3a3b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98725656"
---
# <a name="what-is-azure-active-directory-authentication"></a>Azure Active Directory 認証とは

ID プラットフォームの主な機能の 1 つとして、ユーザーがデバイス、アプリケーション、またはサービスにサインインする際の資格情報の確認、つまり "*認証*" があります。 Azure Active Directory (Azure AD) での認証は、ユーザー名とパスワードの確認だけではありません。 セキュリティを強化し、ヘルプ デスクによるサポートのニーズを軽減するために、Azure AD の認証には、次の構成要素が含まれています。

* セルフサービス パスワード リセット
* Azure AD Multi-Factor Authentication
* パスワードの変更をオンプレミス環境に書き戻すためのハイブリッド統合
* パスワード保護ポリシーをオンプレミス環境に適用するためのハイブリッド統合
* パスワードレスの認証

これらの認証コンポーネントの詳細については、こちらのショート ビデオをご覧ください。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4KVJA]

## <a name="improve-the-end-user-experience"></a>エンドユーザーのエクスペリエンスを向上させる

Azure AD は、ユーザーの ID を保護し、サインイン エクスペリエンスを簡略化するのに役立ちます。 セルフサービス パスワード リセットなどの機能により、ユーザーは任意のデバイスから Web ブラウザーを使用して自分のパスワードを更新または変更することができます。 この機能が特に役立つのは、ユーザーがパスワードを忘れた場合やアカウントがロックされた場合です。 ヘルプデスクや管理者の対応を待つことなく、ユーザーは自分自身のブロックを解除し、作業を継続することができます。

Azure AD Multi-Factor Authentication を使用すると、ユーザーは、サインイン時の認証形式を追加で選択できます (通話、モバイル アプリ通知など)。 この機能によって、ハードウェア トークンなど、単一の固定された形式のセカンダリ認証の必要性が低減します。 追加の認証形式をユーザーが有していなければ、別の方法を選んで作業を継続することができます。

![サインイン画面で使用されている認証方法](media/concept-authentication-methods/overview-login.png)

パスワードレス認証を使用すれば、ユーザーが安全なパスワードを作成して記憶する必要がなくなります。 Windows Hello for Business や FIDO2 セキュリティ キーなどの機能を使用すると、ユーザーはパスワードなしで、デバイスやアプリケーションにサインインできます。 この機能により、さまざまな環境でパスワードを管理する複雑さが軽減されます。

## <a name="self-service-password-reset"></a>セルフサービス パスワード リセット

セルフサービス パスワード リセットにより、ユーザーは、管理者やヘルプ デスクが関与することなく、自分のパスワードを変更またはリセットできるようになります。 ユーザーはアカウントがロックされた場合やパスワードを忘れた場合でも、画面の指示に従って自分自身のブロックを解除して、作業に戻ることができます。 この機能により、ユーザーが自分のデバイスやアプリケーションにサインインできなくなった場合のヘルプ デスクの問い合わせが減り、生産性の喪失も軽減されます。

セルフサービス パスワード リセットは、次のようなシナリオで役立ちます。

* **パスワードの変更 -** ユーザーが自分のパスワードを知っているものの、新しいパスワードへの変更を希望する場合。
* **パスワードのリセット -** ユーザーがパスワードを忘れるなどしてサインインできず、パスワードのリセットを希望する場合。
* **アカウントのロック解除 -** アカウントがロックアウトされているためにサインインできなくなったユーザーが、アカウントのロック解除を希望する場合。

ユーザーがセルフサービス パスワード リセットを使用して自分のパスワードを更新またはリセットすると、そのパスワードは、オンプレミスの Active Directory 環境に書き戻すこともできます。 パスワード ライトバックのおかげで、ユーザーは、更新された資格情報をすぐに、オンプレミスのデバイスやアプリケーションで使用できるようになります。

## <a name="azure-ad-multi-factor-authentication"></a>Azure AD Multi-Factor Authentication

多要素認証は、携帯電話にコードを入力する、指紋スキャンを行うなど、サインイン プロセス中に追加で本人確認できるものをユーザーに求めるプロセスです。

ユーザーの認証にパスワードのみを使用する場合、不安な攻撃ベクトルが残ります。 パスワードの強度が低いか、どこかで流出していたとしたら、そのユーザー名とパスワードでサインインしようとしているユーザーは本人なのか、攻撃者なのかわかりません。 2 つ目の認証形式を義務付ければ、その二次的な要素は攻撃者が容易に取得したり複製したりできるようなものではないため、セキュリティが向上します。

![さまざまな形式の多要素認証の概念図](./media/concept-mfa-howitworks/methods.png)

Azure AD Multi-Factor Authentication は、次の認証方法のうち 2 つ以上を要求することで機能します。

* ユーザーが知っているもの (通常はパスワード)。
* ユーザーが持っているもの (携帯電話やハードウェア キーのように、簡単には複製できない信頼できるデバイスなど)。
* ユーザー自身 (指紋スキャンや顔面認識などの生体認証)。

オンボーディング エクスペリエンスを簡略化するために、セルフサービス パスワード リセットと Azure AD Multi-Factor Authentication の両方にユーザー自身が 1 回のステップで登録できるようになっています。 どのような形式のセカンダリ認証を使用できるかは、管理者が定義できます。 Azure AD Multi-Factor Authentication は、ユーザーがセルフサービス パスワード リセットを実行する場合に、さらにそのプロセスのセキュリティを高めるために義務付けることもできます。

## <a name="password-protection"></a>パスワード保護

既定で、脆弱なパスワード (*Password1* など) は Azure AD によってブロックされます。 既知の脆弱なパスワードを含んだグローバル禁止パスワード リストが自動的に更新されて適用されます。 Azure AD ユーザーが自分のパスワードをこれらの脆弱なパスワードのいずれかに設定しようとすると、より安全なパスワードを選択するよう通知されます。

セキュリティを強化するために、カスタム パスワード保護ポリシーを定義できます。 これらのポリシーでは、フィルターを使用して、*Contoso* などの名前や *London* のような地名が含まれるパスワードのバリエーションをブロックすることができます。

ハイブリッド セキュリティが必要であれば、Azure AD によるパスワード保護をオンプレミスの Active Directory 環境と統合することができます。 オンプレミス環境にインストールされたコンポーネントが Azure AD からグローバル禁止パスワード リストとカスタム パスワード保護ポリシーを受信すると、ドメイン コントローラーがそれらを使用してパスワード変更イベントを処理します。 ユーザーがどこでどのように資格情報を変更しても、このハイブリッドなアプローチであれば、強力なパスワードの使用を確実に強制することができます。

## <a name="passwordless-authentication"></a>パスワードレスの認証

多くの環境が最終的に目指しているのは、サインイン イベントの過程でパスワードの使用をなくすことです。 Azure パスワード保護や Azure AD Multi-Factor Authentication などの機能はセキュリティの強化に役立ちますが、ユーザー名とパスワードは、流出やブルート フォース攻撃の標的になりうる脆弱な認証形式であることに変わりありません。

![パスワードレスに至るまでの認証プロセスでのセキュリティと利便性の関係](./media/concept-authentication-passwordless/passwordless-convenience-security.png)

パスワードレスの方法でサインインする際には、Windows Hello for Business での生体認証や FIDO2 セキュリティ キーなどの方法を使用して資格情報が渡されます。 これらの認証方法は、攻撃者が簡単に複製することはできません。

ユーザーのサインイン エクスペリエンスを簡略化し、攻撃のリスクを軽減するために、Azure AD にはパスワードレスの方法を使用してネイティブに認証する方法が用意されています。

## <a name="next-steps"></a>次のステップ

最初に、[セルフサービス パスワード リセット (SSPR) のチュートリアル][tutorial-sspr]と [Azure AD Multi-Factor Authentication][tutorial-azure-mfa] に関するページを参照してください。

セルフサービス パスワード リセットの概念の詳細については、[Azure AD のセルフサービス パスワード リセットのしくみ][concept-sspr]に関するページを参照してください。

多要素認証の概念の詳細については、[Azure AD Multi-Factor Authentication のしくみ][concept-mfa]に関するページを参照してください。

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md
