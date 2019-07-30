---
title: Azure Active Directory へのパスワードなしのサインインを構成する (プレビュー)
description: FIDO2 セキュリティ キーまたは Microsoft Authenticator アプリを使用して、Azure AD へのパスワードなしのサインインを有効にする (プレビュー)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5ba2545467ebfbd032408aeee25b82b92a628f2a
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711964"
---
# <a name="enable-passwordless-sign-in-for-azure-ad-preview"></a>Azure AD に対してパスワードなしのサインインを有効にする (プレビュー)

## <a name="requirements"></a>必要条件

* Azure Multi-Factor Authentication
* 統合された登録のプレビュー
* FIDO2 セキュリティ キーのプレビューには、互換性のある FIDO2 セキュリティ キーが必要です
* WebAuthN には、Windows 10 バージョン 1809 以降の Microsoft Edge が必要です
* FIDO2 ベースの Windows サインインには、Azure AD 参加済みの Windows 10 バージョン 1809 以降が必要です

## <a name="prepare-devices-for-preview"></a>プレビュー用にデバイスを準備する

パイロット運用を行うデバイスでは、Windows 10 バージョン 1809 以降を実行する必要があります。 Windows 10 バージョン 1903 以降で操作することをお勧めします。

## <a name="enable-security-keys-for-windows-sign-in"></a>Windows サインインのセキュリティ キーを有効にする

組織では、Windows サインインのセキュリティ キーの使用を有効にするために、以下の 1 つまたは複数の方法を利用することを選択できます。

### <a name="enable-credential-provider-via-intune"></a>Intune を介して資格情報プロバイダーを有効にする

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. **[Microsoft Intune]**  >  **[デバイスの登録]**  >  **[Windows の登録]**  >  **[Windows Hello for Business]**  >  **[プロパティ]** の順に移動します。
1. **[設定]** で、 **[サインインのセキュリティ キーを使用]** を **[有効]** に設定します。

サインインのセキュリティ キーの構成は、Windows Hello for Business の構成に依存しません。

#### <a name="enable-targeted-intune-deployment"></a>ターゲットとなる Intune のデプロイを有効にする

資格情報プロバイダーを有効にするために特定のデバイス グループをターゲットにするには、Intune を介して次のカスタム設定を使用します。 

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. **[Microsoft Intune]**  >  **[デバイス構成]**  >  **[プロファイル]**  >  **[プロファイルの作成]** の順に移動します。
1. 次の設定を使用して、新しいプロファイルを構成します
   1. 名前:Windows サインインのセキュリティ キー
   1. 説明:Windows サインイン時に使用される FIDO セキュリティ キーを有効にします
   1. プラットフォーム:Windows 10 以降
   1. プラットフォームの種類:カスタム
   1. カスタム OMA-URI の設定:
      1. 名前:Turn on FIDO Security Keys for Windows Sign-In
      1. OMA-URI: ./Device/Vendor/MSFT/PassportForWork/SecurityKey/UseSecurityKeyForSignin
      1. データ型:整数
      1. 値:1 
1. このポリシーは、特定のユーザー、デバイス、またはグループに割り当てることができます。 詳細については、「[Microsoft Intune でユーザーおよびデバイス プロファイルを割り当てる](https://docs.microsoft.com/intune/device-profile-assign)」の記事を参照してください。

![Intune のカスタム デバイス構成ポリシーの作成](./media/howto-authentication-passwordless-enable/intune-custom-profile.png)

### <a name="enable-credential-provider-via-provisioning-package"></a>プロビジョニング パッケージを使用して資格情報プロバイダーを有効にする

Intune で管理されていないデバイスの場合は、プロビジョニング パッケージをインストールして機能を有効にすることができます。 Windows 構成デザイナー アプリは、[Microsoft Store](https://www.microsoft.com/store/apps/9nblggh4tx22) からインストールできます。

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

## <a name="obtain-fido2-security-keys"></a>FIDO2 セキュリティ キーを取得する

サポートされるキーと製造元の詳細については、「[What is passwordless?](concept-authentication-passwordless.md)」 (パスワードなしとは) の記事の「FIDO2 Security Keys」 (FIDO2 セキュリティ キー) セクションを参照してください。

> [!NOTE]
> NFC ベースのセキュリティ キーを購入して使用する予定の場合は、サポートされている NFC リーダーが必要になります。

## <a name="enable-passwordless-authentication-methods"></a>パスワードなしの認証方法を有効にする

### <a name="enable-the-combined-registration-experience"></a>統合された登録エクスペリエンスを有効にする

FIDO2 セキュリティ キーの登録機能は、結合された登録のプレビューに依存します。 統合された登録のプレビューを有効にするには、以下の手順に従います。

1. [Azure ポータル](https://portal.azure.com)
1. **[Azure Active Directory]**  >  **[ユーザー設定]** の順に移動します
   1. **[アクセス パネル プレビュー機能の設定を管理]** をクリックします
   1. **[ユーザーはセキュリティ情報の登録と管理のためにプレビュー機能を使用できます – 拡張]** で、次のようにします。
      1. **[選択済み]** を選び、プレビューに参加するユーザーのグループを選択します。
      1. または、 **[すべて]** を選び、ディレクトリ内のすべてのユーザーに対して有効にします。
1. **[保存]**

### <a name="enable-new-passwordless-authentication-methods"></a>新しいパスワードなしの認証方法を有効にする

1. [Azure ポータル](https://portal.azure.com)
1. **[Azure Active Directory]**  >  **[認証方法]**  >  **[認証方法ポリシー (プレビュー)]** の順に移動します
1. 各 **[方法]** で、次のオプションを選択します
   1. **有効にする** - [はい] または [いいえ]
   1. **ターゲット** - [すべてのユーザー] または [ユーザーの選択]
1. 各方法を**保存**します

> [!WARNING]
> FIDO2 の “キーの制限ポリシー” はまだ機能しません。 この機能は一般提供前に使用できるようになります。これらのポリシーは既定値から変更しないでください。

> [!NOTE]
> パスワードなしの方法を両方とも選択する必要はありません (1 つのパスワードなしの方法のみをプレビューしたい場合は、その方法のみを有効にすることができます)。 両方の方法にはそれぞれ独自の利点があるため、両方を試すことをお勧めします。

## <a name="user-registration-and-management-of-fido2-security-keys"></a>FIDO2 セキュリティ キーのユーザー登録と管理

1. [https://myprofile.microsoft.com](https://myprofile.microsoft.com) に移動します。
1. サインインします (まだサインインしていない場合)
1. **[セキュリティ情報]** をクリックします
   1. ユーザーが既に 1 つ以上の Azure Multi-Factor Authentication 方法を登録している場合は、FIDO2 セキュリティ キーをすぐに登録することができます。
   1. どの Azure Multi-Factor Authentication 方法も登録していない場合は、いずれかを追加する必要があります。
1. **[方法の追加]** をクリックし、 **[セキュリティ キー]** を選択して、FIDO2 セキュリティ キーを追加します
1. **[USB デバイス]** または **[NFC デバイス]** を選びます
1. キーを準備し、 **[次へ]** を選択します
1. ボックスが表示され、セキュリティ キーの PIN を作成/入力してから、生体認証または指紋認証でキーに必要なジェスチャを実行するよう求められます。
1. 結合された登録エクスペリエンスに戻り、トークンが複数ある場合はどのトークンであるかを特定できるようにわかりやすい名前を指定するよう求められます。 **[次へ]** をクリックします。
1. **[完了]** をクリックしてプロセスを完了します

### <a name="manage-security-key-biometric-pin-or-reset-security-key"></a>セキュリティ キーの生体認証、PIN を管理する、またはセキュリティ キーをリセットする

* Windows 10 バージョン 1809
   * セキュリティ キー ベンダーからのコンパニオン ソフトウェアが必要です
* Windows 10 バージョン 1903 以降
   * ユーザーは自分のデバイスで、 **[Windows 設定]** > **[アカウント]**  >  **[セキュリティ キー]** の順に開くことができます
   * ユーザーは PIN を変更したり、生体認証を更新したり、セキュリティ キーをリセットしたりすることができます

## <a name="user-registration-and-management-of-microsoft-authenticator-app"></a>Microsoft Authenticator アプリのユーザー登録と管理

電話によるサインイン用に Microsoft Authenticator アプリを構成するには、「[Sign in to your accounts using the Microsoft Authenticator app](../user-help/user-help-auth-app-sign-in.md)」 (Microsoft Authenticator アプリを使用してアカウントにサインインする) の記事のガイダンスに従います。

## <a name="sign-in-with-passwordless-credentials"></a>パスワードなしの資格情報でサインインする

### <a name="sign-in-at-the-lock-screen"></a>ロック画面でサインインする

以下の例では、ユーザー Bala Sandhu は既に自分の FIDO2 セキュリティ キーをプロビジョニングしています。 Bala は、Windows 10 のロック画面からセキュリティ キー資格情報プロバイダーを選択し、Windows にサインインするためのセキュリティ キーを挿入できます。

![Windows 10 のロック画面でのセキュリティ キーによるサインイン](./media/howto-authentication-passwordless-enable/fido2-windows-10-1903-sign-in-lock-screen.png)

### <a name="sign-in-on-the-web"></a>Web 上でサインインする

以下の例では、ユーザーは既に自分の FIDO2 セキュリティ キーをプロビジョニングしています。 ユーザーは、Windows 10 バージョン 1809 以降の Microsoft Edge ブラウザー内で FIDO2 セキュリティ キーを使用して、Web 上でサインインすることを選択できます。

![Microsoft Edge でのセキュリティ キーによるサインイン](./media/howto-authentication-passwordless-enable/fido2-windows-10-1903-edge-sign-in.png)

Microsoft Authenticator アプリを使用するサインインについては、「[Sign in to your accounts using the Microsoft Authenticator app](../user-help/user-help-auth-app-sign-in.md)」 (Microsoft Authenticator アプリを使用してアカウントにサインインする) の記事を参照してください。

## <a name="known-issues"></a>既知の問題

### <a name="fido2-security-keys"></a>FIDO2 セキュリティ キー

#### <a name="security-key-provisioning"></a>セキュリティ キーのプロビジョニング

パブリック プレビューでは、管理者がセキュリティ キーをプロビジョニングし、それを解除することはできません。

#### <a name="hybrid-azure-ad-join"></a>ハイブリッド Azure AD 参加

Microsoft Authenticator アプリでのパスワードなしのサインインや FIDO2 セキュリティ キーなどの管理されている資格情報を使用する WIA SSO に依存するユーザーが SSO の利点を得るには、Windows 10 のハイブリッド結合が必要になります。 しかし、現時点では、セキュリティ キーは Azure Active Directory 参加済みのコンピューターでのみ機能します。 純粋な Azure Active Directory 参加済みのコンピューターでのみ、Windows のロック画面の FIDO2 セキュリティ キーを試すことをお勧めします。 Web にはこの制限は適用されません。

#### <a name="upn-changes"></a>UPN の変更

ハイブリッド AADJ および AADJ デバイスで UPN を変更できるようにする機能のサポートに取り組んでいます。 ユーザーの UPN が変更された場合、それに対応するために FIDO2 セキュリティ キーを変更できなくなります。 したがって、唯一の方法はデバイスをリセットすることです。その場合、ユーザーは再登録する必要があります。

### <a name="authenticator-app"></a>Authenticator アプリ

#### <a name="ad-fs-integration"></a>AD FS の統合

ユーザーが Microsoft Authenticator のパスワードなしの資格情報を有効にすると、そのユーザーの認証は常に、既定で承認のための通知を送信します。 このロジックは、ハイブリッド テナントのユーザーが、[代わりにパスワードを使用する] をクリックする追加の手順を行わずに、サインイン確認のために ADFS に移動されることを防ぎます。 このプロセスは、オンプレミスの条件付きアクセス ポリシーとパススルー認証フローもバイパスします。 このプロセスの例外として、login_hint が指定されている場合、ユーザーは AD FS に自動転送され、パスワードなしの資格情報を使用するオプションをバイパスします。

#### <a name="azure-mfa-server"></a>Azure MFA サーバー

組織のオンプレミスの Azure MFA サーバーを介して MFA に対して有効にされたエンド ユーザーは、単一のパスワードなしの電話によるサインイン資格情報を引き続き作成し、使用することができます。 ユーザーが資格情報で Microsoft Authenticator の複数のインストール (5 つ以上) をアップグレードしようとすると、この変更はエラーになることがあります。  

#### <a name="device-registration"></a>デバイス登録

この新しい、強力な資格情報を作成するための前提条件の 1 つは、それを配置するデバイスが、個々のユーザーに対し、Azure AD テナント内に登録されていることです。 デバイス登録の制限により、デバイスは、1 つのテナントにしか登録できません。 この制限は、電話によるサインインに対して有効にできるのは、Microsoft Authenticator アプリの職場または学校の 1 つのアカウントのみであることを意味します。

## <a name="next-steps"></a>次の手順

[デバイス登録の詳細](../devices/overview.md)

[Azure Multi-Factor Authentication の詳細](../authentication/howto-mfa-getstarted.md)
