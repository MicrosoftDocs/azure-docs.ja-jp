---
title: パスワードなしのセキュリティ キー サインイン (プレビュー) - Azure Active Directory
description: FIDO2 セキュリティ キーを使用した Azure AD へのパスワードなしのセキュリティ キー サインインを有効にする (プレビュー)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 826b8e923575db3d6c6aee7ead230f87f1efb50e
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74848444"
---
# <a name="enable-passwordless-security-key-sign-in-preview"></a>Azure AD へのパスワードレス セキュリティ キー サインインを有効にする (プレビュー)

現在パスワードを使用していて、共有 PC 環境がある企業では、セキュリティ キーにより、ユーザー名やパスワードを入力しないで認証を行うシームレスな方法が、ワーカーに提供されます。 セキュリティ キーを使うと、ワーカーの生産性が向上し、セキュリティが強化されます。

このドキュメントでは、セキュリティ キーに基づくパスワードレス認証を有効にする方法について説明します。 この記事を最後まで読むと、FIDO2 セキュリティ キーを使用して、お使いの Azure AD アカウントで Web ベースのアプリケーションにサインインできるようになります。

|     |
| --- |
| FIDO2 セキュリティ キーは、Azure Active Directory のパブリック プレビュー機能です。 詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。|
|     |

## <a name="requirements"></a>必要条件

- [Azure Multi-Factor Authentication](howto-mfa-getstarted.md)
- [統合されたセキュリティ情報の登録 (プレビュー)](concept-registration-mfa-sspr-combined.md)
- [互換性のある FIDO2 セキュリティ キー](concept-authentication-passwordless.md#fido2-security-keys)
- WebAuthN には、Windows 10 バージョン 1809 以降が必要です**

Web アプリやサービスへのログインにセキュリティ キーを使用するには、WebAuthN プロトコルをサポートするブラウザーが必要です。 これには、Microsoft Edge、Chrome、Firefox、Safari などが含まれます。

## <a name="prepare-devices-for-preview"></a>プレビュー用にデバイスを準備する

パイロット運用を行うデバイスでは、Windows 10 バージョン 1809 以降を実行する必要があります。 Windows 10 バージョン 1903 以降で操作することをお勧めします。

## <a name="enable-passwordless-authentication-method"></a>パスワードなしの認証方法を有効にする

### <a name="enable-the-combined-registration-experience"></a>統合された登録エクスペリエンスを有効にする

パスワードなしの認証方法の登録機能は、統合された登録のプレビューに依存しています。 「[統合されたセキュリティ情報の登録の有効化 (プレビュー)](howto-registration-mfa-sspr-combined.md)」の記事に記載されている手順に従って、統合された登録のプレビューを有効にします。

### <a name="enable-fido2-security-key-method"></a>FIDO2 セキュリティ キーの方法を有効にする

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. **[Azure Active Directory]**  >  **[セキュリティ]**  >  **[認証方法]**  >  **[認証方法ポリシー (プレビュー)]** の順に移動します。
1. 方法 **[FIDO2 セキュリティ キー]** で、次のオプションを選択します。
   1. **有効にする** - [はい] または [いいえ]
   1. **ターゲット** - [すべてのユーザー] または [ユーザーの選択]
1. 構成を**保存**します。

## <a name="user-registration-and-management-of-fido2-security-keys"></a>FIDO2 セキュリティ キーのユーザー登録と管理

1. [https://myprofile.microsoft.com](https://myprofile.microsoft.com) を参照します。
1. まだしていない場合はサインインします。
1. **[セキュリティ情報]** をクリックします。
   1. ユーザーが既に 1 つ以上の Azure Multi-Factor Authentication 方法を登録している場合は、FIDO2 セキュリティ キーをすぐに登録することができます。
   1. どの Azure Multi-Factor Authentication 方法も登録していない場合は、いずれかを追加する必要があります。
1. **[方法の追加]** をクリックし、 **[セキュリティ キー]** を選択して、FIDO2 セキュリティ キーを追加します。
1. **[USB デバイス]** または **[NFC デバイス]** を選択します。
1. キーを準備し、 **[次へ]** を選択します。
1. ボックスが表示され、ユーザーは、セキュリティ キーの PIN を作成/入力してから、生体認証または指紋認証でキーに必要なジェスチャを実行するよう求められます。
1. ユーザーは、結合された登録エクスペリエンスに戻り、キーが複数ある場合にキーを見分けられるよう、わかりやすい名前を指定することを求められます。 **[次へ]** をクリックします。
1. **[完了]** をクリックしてプロセスを完了します。

## <a name="sign-in-with-passwordless-credential"></a>パスワードなしの資格情報でサインインする

以下の例では、ユーザーは既に自分の FIDO2 セキュリティ キーをプロビジョニングしています。 ユーザーは、Windows 10 バージョン 1809 以降のサポートされているブラウザーで FIDO2 セキュリティ キーを使用して、Web 上でサインインすることを選択できます。

![Microsoft Edge でのセキュリティ キーによるサインイン](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-edge-sign-in.png)

## <a name="troubleshooting-and-feedback"></a>トラブルシューティングとサポート

この機能のプレビュー中に、フィードバックを共有したい場合、または問題が発生した場合は、Windows フィードバック ハブ アプリを使用して共有してください。

1. **フィードバック ハブ**を起動し、サインインしていることを確認します。
1. 次の分類でフィードバックを送信します。
   1. カテゴリ:セキュリティとプライバシー
   1. サブカテゴリ: FIDO
1. ログをキャプチャするには、次のオプションを使用します: **[問題の再現]**

## <a name="known-issues"></a>既知の問題

### <a name="security-key-provisioning"></a>セキュリティ キーのプロビジョニング

パブリック プレビューでは、管理者がセキュリティ キーをプロビジョニングし、それを解除することはできません。

### <a name="upn-changes"></a>UPN の変更

ユーザーの UPN が変更された場合、その変更に対応するために FIDO2 セキュリティ キーを変更できなくなります。 解決策は、デバイスをリセットすることです。ユーザーは、FIDO2 のセキュリティ キーを再登録する必要があります。

## <a name="next-steps"></a>次の手順

[FIDO2 セキュリティ キーでの Windows 10 のサインイン](howto-authentication-passwordless-security-key-windows.md)

[オンプレミスのリソースに対する FIDO2 認証を有効にする](howto-authentication-passwordless-security-key-on-premises.md)

[デバイス登録の詳細](../devices/overview.md)

[Azure Multi-factor Authentication の詳細](../authentication/howto-mfa-getstarted.md)
