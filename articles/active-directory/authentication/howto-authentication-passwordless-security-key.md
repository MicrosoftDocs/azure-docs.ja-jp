---
title: パスワードなしのセキュリティ キー サインイン - Azure Active Directory
description: FIDO2 セキュリティ キーを使用した Azure AD へのパスワードなしのセキュリティ キー サインインを有効にする
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 02/22/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71a29a2a7e379e253b52813eb7a76f669abbf668
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101653832"
---
# <a name="enable-passwordless-security-key-sign-in"></a>パスワードなしのセキュリティ キー サインインを有効にする 

現在パスワードを使用していて、共有 PC 環境がある企業では、セキュリティ キーにより、ユーザー名やパスワードを入力しないで認証を行うシームレスな方法が、ワーカーに提供されます。 セキュリティ キーを使うと、ワーカーの生産性が向上し、セキュリティが強化されます。

このドキュメントでは、セキュリティ キーに基づくパスワードレス認証を有効にする方法について説明します。 この記事を最後まで読むと、FIDO2 セキュリティ キーを使用して、お使いの Azure AD アカウントで Web ベースのアプリケーションにサインインできるようになります。

## <a name="requirements"></a>必要条件

- [Azure AD Multi-Factor Authentication](howto-mfa-getstarted.md)
- [統合されたセキュリティ情報の登録](concept-registration-mfa-sspr-combined.md)の有効化
- 互換性のある [FIDO2 セキュリティ キー](concept-authentication-passwordless.md#fido2-security-keys)
- WebAuthN requires Windows 10 バージョン 1903 以降**

Web アプリやサービスへのログインにセキュリティ キーを使用するには、WebAuthN プロトコルをサポートするブラウザーが必要です。 これには、Microsoft Edge、Chrome、Firefox、Safari などが含まれます。

## <a name="prepare-devices"></a>デバイスを準備する

Azure AD 参加済みデバイスでは、Windows 10 バージョン 1903 以降を使用することをお勧めします。

Hybrid Azure AD 参加済みデバイスでは、Windows 10 バージョン 2004 以降が実行されている必要があります。

## <a name="enable-passwordless-authentication-method"></a>パスワードなしの認証方法を有効にする

### <a name="enable-the-combined-registration-experience"></a>統合された登録エクスペリエンスを有効にする

パスワードなしの認証方法の登録機能は、統合された登録機能に依存しています。 [統合されたセキュリティ情報の登録の有効化](howto-registration-mfa-sspr-combined.md)に関する記事に記載されている手順に従って、統合された登録を有効にします。

### <a name="enable-fido2-security-key-method"></a>FIDO2 セキュリティ キーの方法を有効にする

1. [Azure portal](https://portal.azure.com) にサインインします。
1. **[Azure Active Directory]**  >  **[セキュリティ]**  >  **[認証方法]**  >  **[認証方法ポリシー]** の順に移動します。
1. 方法 **[FIDO2 セキュリティ キー]** で、次のオプションを選択します。
   1. **有効にする** - [はい] または [いいえ]
   1. **ターゲット** - [すべてのユーザー] または [ユーザーの選択]
1. 構成を **保存** します。

## <a name="user-registration-and-management-of-fido2-security-keys"></a>FIDO2 セキュリティ キーのユーザー登録と管理

1. [https://myprofile.microsoft.com](https://myprofile.microsoft.com) を参照します。
1. まだしていない場合はサインインします。
1. **[セキュリティ情報]** をクリックします。
   1. ユーザーが既に 1 つ以上の Azure AD Multi-Factor Authentication 方法を登録している場合は、FIDO2 セキュリティ キーをすぐに登録することができます。
   1. 少なくとも 1 つの Azure AD Multi-Factor Authentication 方法を登録していない場合は、いずれかを追加する必要があります。
1. **[方法の追加]** をクリックし、 **[セキュリティ キー]** を選択して、FIDO2 セキュリティ キーを追加します。
1. **[USB デバイス]** または **[NFC デバイス]** を選択します。
1. キーを準備し、 **[次へ]** を選択します。
1. ボックスが表示され、ユーザーは、セキュリティ キーの PIN を作成/入力してから、生体認証または指紋認証でキーに必要なジェスチャを実行するよう求められます。
1. ユーザーは、結合された登録エクスペリエンスに戻り、キーが複数ある場合にキーを見分けられるよう、わかりやすい名前を指定することを求められます。 **[次へ]** をクリックします。
1. **[完了]** をクリックしてプロセスを完了します。

## <a name="sign-in-with-passwordless-credential"></a>パスワードなしの資格情報でサインインする

以下の例では、ユーザーは既に自分の FIDO2 セキュリティ キーをプロビジョニングしています。 ユーザーは、Windows 10 バージョン 1903 以降のサポートされているブラウザーで FIDO2 セキュリティ キーを使用して、Web 上でサインインすることを選択できます。

![Microsoft Edge でのセキュリティ キーによるサインイン](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-edge-sign-in.png)

## <a name="troubleshooting-and-feedback"></a>トラブルシューティングとフィードバック

この機能についてフィードバックを共有したい場合、または問題が発生した場合は、次の手順を使用して Windows フィードバック Hub アプリ経由で共有してください。

1. **フィードバック ハブ** を起動し、サインインしていることを確認します。
1. 次の分類でフィードバックを送信します。
   - カテゴリ:セキュリティとプライバシー
   - サブカテゴリ: FIDO
1. ログをキャプチャするには、 **[Recreate my Problem]\(問題の再現\)** オプションを使用します。

## <a name="known-issues"></a>既知の問題

### <a name="security-key-provisioning"></a>セキュリティ キーのプロビジョニング

管理者がセキュリティ キーをプロビジョニングし、それをプロビジョニング解除することはできません。

### <a name="upn-changes"></a>UPN の変更

Microsoft では、Hybrid Azure AD 参加済みデバイスと Azure AD 参加済みデバイスに対する、UPN の変更を可能にする機能のサポートに取り組んでいます。 ユーザーの UPN が変更されると、その変更に対応するために FIDO2 セキュリティ キーを変更することはできなくなります。 解決策は、デバイスをリセットすることです。その場合、ユーザーを再登録する必要があります。

## <a name="next-steps"></a>次のステップ

[FIDO2 セキュリティ キーでの Windows 10 のサインイン](howto-authentication-passwordless-security-key-windows.md)

[オンプレミスのリソースに対する FIDO2 認証を有効にする](howto-authentication-passwordless-security-key-on-premises.md)

[デバイス登録の詳細](../devices/overview.md)

[Azure AD Multi-factor Authentication の詳細](../authentication/howto-mfa-getstarted.md)
