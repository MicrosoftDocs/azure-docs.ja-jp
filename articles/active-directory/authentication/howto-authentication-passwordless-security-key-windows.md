---
title: Windows のパスワードレス セキュリティ キー サインイン - Azure Active Directory
description: FIDO2 セキュリティ キーを使用した Azure AD へのパスワードなしのセキュリティ キー サインインを有効にする (プレビュー)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: ce2b420c2124c86610058ce2f31cd6d7bf620a97
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74848461"
---
# <a name="enable-passwordless-security-key-sign-in-to-windows-10-devices-preview"></a>Windows 10 デバイスへのパスワードなしのセキュリティ キー サインインを有効にする (プレビュー)

このドキュメントでは、FIDO2 セキュリティ キーに基づくパスワードレス認証を、Windows 10 デバイスとの間で有効にする方法について説明します。 この記事を最後まで読むと、FIDO2 セキュリティ キーを使用して、お使いの Azure AD アカウントで Web ベースのアプリケーションと Azure AD 参加済み Windows 10 デバイスの両方にサインインできるようになります。

|     |
| --- |
| FIDO2 セキュリティ キーは、Azure Active Directory のパブリック プレビュー機能です。 詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。|
|     |

## <a name="requirements"></a>必要条件

- [Azure Multi-Factor Authentication](howto-mfa-getstarted.md)
- [統合されたセキュリティ情報の登録 (プレビュー)](concept-registration-mfa-sspr-combined.md)
- 互換性のある [FIDO2 セキュリティ キー](concept-authentication-passwordless.md#fido2-security-keys)
- WebAuthN には、Windows 10 バージョン 1809 以降が必要です
- [Azure AD 参加済みデバイス](../devices/concept-azure-ad-join.md)には、Windows 10 バージョン 1809 以降が必要です
- [Microsoft Intune](https://docs.microsoft.com/intune/fundamentals/what-is-intune) (オプション)
- プロビジョニング パッケージ (オプション)

### <a name="unsupported-scenarios"></a>サポートされていないシナリオ

- Windows Server Active Directory Domain Services (AD DS) ドメインに参加済みの (オンプレミス専用デバイスの) 展開は**サポートされていません**。
- セキュリティ キーを使用する RDP、VDI、および Citrix のシナリオは**サポートされていません**。
- セキュリティ キーを使用する S/MIME は**サポートされていません**。
- セキュリティ キーを使用する "実行" は**サポートされていません**。
- セキュリティ キーを使用するサーバーへのログインは**サポートされていません**。
- オンラインのときにセキュリティ キーを使用してデバイスにサインインしていない場合、そのデバイスをサインインまたはオフラインでのロック解除には使用できません。
- 複数の Azure AD アカウントが含まれるセキュリティ キーで Windows 10 デバイスにサインインするか、ロックを解除します。 このシナリオでは、セキュリティ キーに追加された最後のアカウントが利用されます。 WebAuthN の場合、ユーザーは自分が使用するアカウントを選択できます。

## <a name="prepare-devices-for-preview"></a>プレビュー用にデバイスを準備する

パイロット運用を行う Azure AD 参加済みデバイスでは、Windows 10 バージョン 1809 以降を実行している必要があります。 Windows 10 バージョン 1903 以降で操作することをお勧めします。

## <a name="enable-security-keys-for-windows-sign-in"></a>Windows サインインのセキュリティ キーを有効にする

組織では、Windows サインインのセキュリティ キーの使用を有効にするために、組織の要件に基づいて以下の方法の 1 つまたは複数を利用することを選択できます。

- [Intune を使用して有効にする](#enable-with-intune)
- [ターゲットとなる Intune のデプロイ](#targeted-intune-deployment)
- [プロビジョニング パッケージを使用して有効にする](#enable-with-a-provisioning-package)

### <a name="enable-with-intune"></a>Intune を使用して有効にする

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. **[Microsoft Intune]**  >  **[デバイスの登録]**  >  **[Windows の登録]**  >  **[Windows Hello for Business]**  >  **[プロパティ]** の順に移動します。
1. **[設定]** で、 **[サインインのセキュリティ キーを使用]** を **[有効]** に設定します。

サインインのセキュリティ キーの構成は、Windows Hello for Business の構成に依存しません。

### <a name="targeted-intune-deployment"></a>ターゲットとなる Intune のデプロイ

資格情報プロバイダーを有効にするために特定のデバイス グループをターゲットにするには、Intune を介して次のカスタム設定を使用します。

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. **[Microsoft Intune]**  >  **[デバイス構成]**  >  **[プロファイル]**  >  **[プロファイルの作成]** の順に移動します。
1. 次の設定を使用して、新しいプロファイルを構成します
   1. 名前:Windows サインインのセキュリティ キー
   1. 説明:Windows サインイン時に使用される FIDO セキュリティ キーを有効にします
   1. プラットフォーム:Windows 10 以降
   1. プロファイルの種類:カスタム
   1. カスタム OMA-URI の設定:
      1. 名前:Turn on FIDO Security Keys for Windows Sign-In
      1. OMA-URI: ./Device/Vendor/MSFT/PassportForWork/SecurityKey/UseSecurityKeyForSignin
      1. データ型:整数
      1. 値:1
1. このポリシーは、特定のユーザー、デバイス、またはグループに割り当てることができます。 詳細については、「[Microsoft Intune でユーザーおよびデバイス プロファイルを割り当てる](https://docs.microsoft.com/intune/device-profile-assign)」の記事を参照してください。

![Intune のカスタム デバイス構成ポリシーの作成](./media/howto-authentication-passwordless-security-key/intune-custom-profile.png)

### <a name="enable-with-a-provisioning-package"></a>プロビジョニング パッケージを使用して有効にする

Intune で管理されていないデバイスの場合は、プロビジョニング パッケージをインストールして機能を有効にすることができます。 Windows 構成デザイナー アプリは、[Microsoft Store](https://www.microsoft.com/en-us/p/windows-configuration-designer/9nblggh4tx22) からインストールできます。

1. Windows 構成デザイナーを起動します。
1. **[ファイル]**  >  **[新しいプロジェクト]** の順に選択します。
1. プロジェクトに名前を付け、プロジェクトが作成される場所へのパスをメモしておきます。
1. **[次へ]** を選択します。
1. **[プロビジョニング パッケージ]** は、 **[Selected project workflow]\(選択されたプロジェクト ワークフロー\)** として選択されたままにし、 **[次へ]** を選びます。
1. **[表示および構成する設定の選択]** で **[すべての Windows デスクトップ エディション]** を選択し、 **[次へ]** を選びます。
1. **[完了]** を選択します。
1. 新しく作成されたプロジェクトで、 **[ランタイムの設定]**  >  **[WindowsHelloForBusiness]**  >  **[SecurityKeys]**  >  **[UseSecurityKeyForSignIn]** の順に移動します。
1. **[UseSecurityKeyForSignIn]** を **[有効]** に設定します。
1. **[エクスポート]**  >  **[プロビジョニング パッケージ]** の順に選択します
1. **[ビルド]** ウィンドウの **[プロビジョニング パッケージの説明]** では既定値のままにし、 **[次へ]** を選択します。
1. **[ビルド]** ウィンドウの **[プロビジョニング パッケージのセキュリティ情報の選択]** では既定値のままにし、 **[次へ]** を選択します。
1. **[ビルド]** ウィンドウの **[プロビジョニング パッケージの保存先の選択]** のパスをメモするか、変更して、 **[次へ]** を選択します。
1. **[プロビジョニング パッケージのビルド]** ページで **[ビルド]** を選択します。
1. 作成された 2 つのファイル (ppkg と cat) を、後でコンピューターに適用できる場所に保存します。
1. 「[プロビジョニング パッケージの適用](https://docs.microsoft.com/windows/configuration/provisioning-packages/provisioning-apply-package)」の記事のガイダンスに従って、作成したプロビジョニング パッケージを適用します。

> [!NOTE]
> Windows 10 バージョン 1809 を実行しているデバイスでは、共有 PC モードも有効にする (EnableSharedPCMode) 必要があります。 この機能の有効化に関する情報は、[Windows 10 を使用して共有 PC またはゲスト PC をセットアップする](https://docs.microsoft.com/windows/configuration/set-up-shared-or-guest-pc)ことに関する記事に記載されています。

## <a name="sign-in-to-windows-with-a-fido2-security-key"></a>FIDO2 セキュリティ キーを使用して Windows にサインインする

下の例では、Bala Sandhu というユーザーが、前の記事の手順に従って FIDO2 セキュリティ キーを既にプロビジョニングしているので、[パスワードを使用しないセキュリティ キーのサインインを有効にします](howto-authentication-passwordless-security-key.md#user-registration-and-management-of-fido2-security-keys)。 Bala は、Windows 10 のロック画面からセキュリティ キー資格情報プロバイダーを選択し、Windows にサインインするためのセキュリティ キーを挿入できます。

![Windows 10 のロック画面でのセキュリティ キーによるサインイン](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-sign-in-lock-screen.png)

### <a name="manage-security-key-biometric-pin-or-reset-security-key"></a>セキュリティ キーの生体認証、PIN を管理する、またはセキュリティ キーをリセットする

* Windows 10 バージョン 1903 以降
   * ユーザーは自分のデバイスで、 **[Windows 設定]** > **[アカウント]**  >  **[セキュリティ キー]** の順に開くことができます
   * ユーザーは PIN を変更したり、生体認証を更新したり、セキュリティ キーをリセットしたりすることができます

## <a name="troubleshooting-and-feedback"></a>トラブルシューティングとフィードバック

この機能のプレビュー中に、フィードバックを共有したい場合、または問題が発生した場合は、Windows フィードバック ハブ アプリを使用して共有してください。

1. **フィードバック ハブ**を起動し、サインインしていることを確認します。
1. 次の分類でフィードバックを送信します。
   1. カテゴリ:セキュリティとプライバシー
   1. サブカテゴリ: FIDO
1. ログをキャプチャするには、次のオプションを使用します: **[Recreate my Problem] (問題を再現する)**

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="does-this-work-in-my-on-premises-environment"></a>これはオンプレミス環境で動作しますか。

この機能は、純粋なオンプレミスの Active Directory Domain Services (AD DS) 環境では機能しません。

### <a name="my-organization-requires-two-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement"></a>私の組織はリソースにアクセスするために 2 要素認証を求めています。この要件をサポートするために何ができますか。

セキュリティ キーは、さまざまなフォーム ファクターで提供されています。 2 番目の要素として PIN または生体認証を使用してデバイスを有効にする方法については、目的のデバイス製造元にお問い合わせください。

### <a name="can-admins-set-up-security-keys"></a>管理者はセキュリティ キーを設定できますか。

Microsoft はこの機能を一般提供 (GA) するため、この機能に取り組んでいます。

### <a name="where-can-i-go-to-find-compliant-security-keys"></a>準拠しているセキュリティ キーはどこで見つけることができますか。

[FIDO2 セキュリティ キー](concept-authentication-passwordless.md#fido2-security-keys)

### <a name="what-do-i-do-if-i-lose-my-security-key"></a>セキュリティ キーを紛失した場合はどうすればよいですか。

セキュリティ情報ページに移動してセキュリティ キーを削除すると、Azure portal からキーを削除できます。

## <a name="next-steps"></a>次の手順

[デバイス登録の詳細](../devices/overview.md)

[Azure Multi-factor Authentication の詳細](../authentication/howto-mfa-getstarted.md)
