---
title: Windows のパスワードレス セキュリティ キー サインイン - Azure Active Directory
description: FIDO2 セキュリティ キーを使用して Azure Active Directory へのパスワードレス セキュリティ キー サインインを有効にする方法について説明します
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
ms.openlocfilehash: 190e9c857f1ec9d19eb89493dc4b4a9fb68fac87
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101653509"
---
# <a name="enable-passwordless-security-key-sign-in-to-windows-10-devices-with-azure-active-directory"></a>Azure Active Directory を使用して Windows 10 デバイスへのパスワードレス セキュリティ キー サインインを有効にする 

このドキュメントでは、FIDO2 セキュリティ キーに基づくパスワードレス認証を、Windows 10 デバイスとの間で有効にする方法について説明します。 この記事を最後まで読むと、FIDO2 セキュリティ キーを使用して、お使いの Azure AD アカウントで Azure AD とHybrid Azure AD 参加済み Windows 10 デバイスの両方にサインインできるようになります。

## <a name="requirements"></a>必要条件

| デバイスの種類 | Azure AD 参加済み | ハイブリッド Azure AD 参加済み |
| --- | --- | --- |
| [Azure AD Multi-Factor Authentication](howto-mfa-getstarted.md) | X | X |
| [統合されたセキュリティ情報の登録](concept-registration-mfa-sspr-combined.md) | X | X |
| 互換性のある [FIDO2 セキュリティ キー](concept-authentication-passwordless.md#fido2-security-keys) | X | X |
| WebAuthN には、Windows 10 バージョン 1903 以降が必要です | X | X |
| [Azure AD 参加済みデバイス](../devices/concept-azure-ad-join.md)には、Windows 10 バージョン 1909 以降が必要です | X |   |
| [Hybrid Azure AD 参加済みデバイス](../devices/concept-azure-ad-join-hybrid.md)には、Windows 10 バージョン 2004 以降が必要です |   | X |
| 完全にパッチが適用された Windows Server 2016/2019 ドメイン コントローラー。 |   | X |
| [Azure AD Connect](../hybrid/how-to-connect-install-roadmap.md#install-azure-ad-connect) バージョン 1.4.32.0 以降 |   | X |
| [Microsoft Intune](/intune/fundamentals/what-is-intune) (オプション) | X | X |
| プロビジョニング パッケージ (オプション) | X | X |
| グループ ポリシー (省略可能) |   | X |

### <a name="unsupported-scenarios"></a>サポートされていないシナリオ

以下のシナリオはサポートされていません。

- Windows Server Active Directory Domain Services (AD DS) ドメイン参加済みの (オンプレミス専用デバイスの) 展開。
- セキュリティ キーを使用した RDP、VDI、および Citrix のシナリオ。
- セキュリティ キーを使用した S/MIME。
- セキュリティ キーを使用した [別のユーザーとして実行]。
- セキュリティ キーを使用したサーバーへのログイン。
- オンラインのときにセキュリティ キーを使用してデバイスにサインインしていない場合、そのデバイスをオフラインでのサインインまたはロック解除に使用することはできません。
- 複数の Azure AD アカウントが含まれるセキュリティ キーで Windows 10 デバイスにサインインするか、ロックを解除します。 このシナリオでは、セキュリティ キーに最後に追加されたアカウントが利用されます。 WebAuthN の場合、ユーザーは自分が使用するアカウントを選択できます。
- Windows 10 バージョン 1809 を実行しているデバイスのロックを解除します。 最適なエクスペリエンスを得るには、Windows 10 バージョン 1903 以降を使用してください。

## <a name="prepare-devices"></a>デバイスを準備する

Azure AD 参加済みデバイスでは、Windows 10 バージョン 1909 以降が実行されている必要があります。

Hybrid Azure AD 参加済みデバイスでは、Windows 10 バージョン 2004 以降が実行されている必要があります。

## <a name="enable-security-keys-for-windows-sign-in"></a>Windows サインインのセキュリティ キーを有効にする

組織では、Windows サインインのセキュリティ キーの使用を有効にするために、組織の要件に基づいて以下の方法の 1 つまたは複数を利用することを選択できます。

- [Intune を使用して有効にする](#enable-with-intune)
- [ターゲットとなる Intune のデプロイ](#targeted-intune-deployment)
- [プロビジョニング パッケージを使用して有効にする](#enable-with-a-provisioning-package)
- [グループ ポリシーを使用して有効にする (Hybrid Azure AD 参加済みデバイスのみ)](#enable-with-group-policy)

> [!IMPORTANT]
> **Hybrid Azure AD 参加済みデバイス** を使用している組織は、Windows 10 FIDO2 セキュリティ キーの認証が機能する前に、「[オンプレミスのリソースに対する FIDO2 認証を有効にする](howto-authentication-passwordless-security-key-on-premises.md)」に記載されている手順 **も** 完了する必要があります。
>
> **Azure AD 参加済みデバイス** を使用している組織は、FIDO2 セキュリティ キーを使用してデバイスがオンプレミスのリソースに対して認証される前に、この操作を行う必要があります。

### <a name="enable-with-intune"></a>Intune を使用して有効にする

Intune を使用してセキュリティ キーを使用できるようにするには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. **[Microsoft Intune]**  >  **[デバイスの登録]**  >  **[Windows の登録]**  >  **[Windows Hello for Business]**  >  **[プロパティ]** の順に移動します。
1. **[設定]** で、 **[サインインのセキュリティ キーを使用]** を **[有効]** に設定します。

サインインのセキュリティ キーの構成は、Windows Hello for Business の構成に依存しません。

### <a name="targeted-intune-deployment"></a>ターゲットとなる Intune のデプロイ

資格情報プロバイダーを有効にするために特定のデバイス グループをターゲットにするには、Intune を介して次のカスタム設定を使用します。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. **[Microsoft Intune]**  >  **[デバイス構成]**  >  **[プロファイル]**  >  **[プロファイルの作成]** の順に移動します。
1. 次の設定を使用して、新しいプロファイルを構成します。
   - 名前:Windows サインインのセキュリティ キー
   - 説明:Windows サインイン時に使用される FIDO セキュリティ キーを有効にします
   - プラットフォーム:Windows 10 以降
   - プロファイルの種類:Custom
   - カスタム OMA-URI の設定:
      - 名前:Turn on FIDO Security Keys for Windows Sign-In
      - OMA-URI: ./Device/Vendor/MSFT/PassportForWork/SecurityKey/UseSecurityKeyForSignin
      - データ型:Integer
      - 値:1
1. このポリシーを、特定のユーザー、デバイス、またはグループに割り当てることができます。 詳細については、[Microsoft Intune でユーザーとデバイスのプロファイルを割り当てる](/intune/device-profile-assign)方法に関するページを参照してください。

![Intune のカスタム デバイス構成ポリシーの作成](./media/howto-authentication-passwordless-security-key/intune-custom-profile.png)

### <a name="enable-with-a-provisioning-package"></a>プロビジョニング パッケージを使用して有効にする

Intune で管理されていないデバイスの場合は、プロビジョニング パッケージをインストールして機能を有効にすることができます。 Windows 構成デザイナー アプリは、[Microsoft Store](https://www.microsoft.com/p/windows-configuration-designer/9nblggh4tx22) からインストールできます。 プロビジョニング パッケージを作成するには、次の手順を実行します。

1. Windows 構成デザイナーを起動します。
1. **[ファイル]**  >  **[新しいプロジェクト]** の順に選択します。
1. プロジェクトに名前を付け、プロジェクトが作成される場所へのパスを書き留めてから、 **[次へ]** を選択します。
1. *[プロビジョニング パッケージ]* は、 **[Selected project workflow]\(選択されたプロジェクト ワークフロー\)** として選択されたままにし、 **[次へ]** を選びます。
1. **[表示および構成する設定の選択]** で *[すべての Windows デスクトップ エディション]* を選択し、 **[次へ]** を選びます。
1. **[完了]** を選択します。
1. 新しく作成されたプロジェクトで、 **[ランタイムの設定]**  >  **[WindowsHelloForBusiness]**  >  **[SecurityKeys]**  >  **[UseSecurityKeyForSignIn]** の順に移動します。
1. **[UseSecurityKeyForSignIn]** を *[有効]* に設定します。
1. **[エクスポート]**  >  **[プロビジョニング パッケージ]** の順に選択します
1. **[ビルド]** ウィンドウの **[プロビジョニング パッケージの説明]** は既定値のままにし、 **[次へ]** を選択します。
1. **[ビルド]** ウィンドウの **[プロビジョニング パッケージのセキュリティ情報の選択]** では既定値のままにし、 **[次へ]** を選択します。
1. **[ビルド]** ウィンドウの **[プロビジョニング パッケージの保存先の選択]** のパスをメモするか、変更して、 **[次へ]** を選択します。
1. **[プロビジョニング パッケージのビルド]** ページで **[ビルド]** を選択します。
1. 作成された 2 つのファイル (*ppkg* と *cat*) を、後でコンピューターに適用できる場所に保存します。
1. 作成したプロビジョニング パッケージを適用するには、「[プロビジョニング パッケージの適用](/windows/configuration/provisioning-packages/provisioning-apply-package)」を参照してください。

> [!NOTE]
> Windows 10 バージョン 1903 を実行しているデバイスでは、共有 PC モード (*EnableSharedPCMode*) も有効にする必要があります。 この機能を有効にする方法の詳細については、「[Windows 10 での共有またはゲスト PC の設定](/windows/configuration/set-up-shared-or-guest-pc)」を参照してください。

### <a name="enable-with-group-policy"></a>グループ ポリシーを使用して有効にする

**Hybrid Azure AD 参加済みデバイス** では、組織は次のグループ ポリシー設定を構成して、FIDO セキュリティ キー サインインを有効にすることができます。 この設定は、 **[コンピューターの構成]**  >  **[管理用テンプレート]**  >  **[システム]**  >  **[ログオン]**  >  **[Turn on security key sign-in]\(セキュリティ キー サインインを有効にする\)** で見つけることができます。

- このポリシーを **[有効]** に設定すると、ユーザーはセキュリティ キーを使用してサインインできるようになります。
- このポリシーを **[無効]** または **[未構成]** に設定すると、ユーザーはセキュリティ キーを使用してサインインできなくなります。

このグループ ポリシー設定には、`CredentialProviders.admx` グループ ポリシー テンプレートの更新バージョンが必要です。 この新しいテンプレートは、Windows Server の次のバージョンと Windows 10 20H1 で使用できます。 この設定は、これらの新しいバージョンの Windows が動作しているデバイスで管理するか、サポート トピックのガイダンス「[Windows でグループ ポリシー管理用テンプレート用のセントラル ストアを作成および管理する方法](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra)」に従って一元管理することもできます。

## <a name="sign-in-with-fido2-security-key"></a>FIDO2 セキュリティ キーを使用してサインインする

下の例では、Bala Sandhu という名前のユーザーが、前の[パスワードレス セキュリティ キー サインインを有効にする](howto-authentication-passwordless-security-key.md#user-registration-and-management-of-fido2-security-keys)方法に関する記事の手順に従って、FIDO2 セキュリティ キーを既にプロビジョニングしています。 Hybrid Azure AD 参加済みデバイスの場合は、[オンプレミス リソースへのパスワードレス セキュリティ キー サインインが有効になっている](howto-authentication-passwordless-security-key-on-premises.md)ことも確認してください。 Bala は、Windows 10 のロック画面からセキュリティ キー資格情報プロバイダーを選択し、Windows にサインインするためのセキュリティ キーを挿入できます。

![Windows 10 のロック画面でのセキュリティ キーによるサインイン](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-sign-in-lock-screen.png)

### <a name="manage-security-key-biometric-pin-or-reset-security-key"></a>セキュリティ キーの生体認証、PIN を管理する、またはセキュリティ キーをリセットする

* Windows 10 バージョン 1903 以降
   * ユーザーは自分のデバイスで、 **[Windows 設定]** > **[アカウント]**  >  **[セキュリティ キー]** の順に開くことができます
   * ユーザーは PIN を変更したり、生体認証を更新したり、セキュリティ キーをリセットしたりすることができます

## <a name="troubleshooting-and-feedback"></a>トラブルシューティングとフィードバック

この機能についてフィードバックを共有したい場合、または問題が発生した場合は、次の手順を使用して Windows フィードバック Hub アプリ経由で共有してください。

1. **フィードバック ハブ** を起動し、サインインしていることを確認します。
1. 次の分類でフィードバックを送信します。
   - カテゴリ:セキュリティとプライバシー
   - サブカテゴリ: FIDO
1. ログをキャプチャするには、 **[Recreate my Problem]\(問題の再現\)** オプションを使用します。

## <a name="next-steps"></a>次のステップ

[Azure AD と Hybrid Azure AD 参加済みデバイスのオンプレミス リソースへのアクセスを有効にする](howto-authentication-passwordless-security-key-on-premises.md)

[デバイス登録の詳細](../devices/overview.md)

[Azure AD Multi-factor Authentication の詳細](../authentication/howto-mfa-getstarted.md)