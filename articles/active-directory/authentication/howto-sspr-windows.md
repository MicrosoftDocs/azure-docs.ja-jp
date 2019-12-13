---
title: Windows でのパスワード リセットのセルフ サービス - Azure Active Directory
description: Windows のログオン画面で「パスワードを忘れた場合」を使用してパスワード リセットのセルフ サービスを有効にする方法
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: be1c0e93a51064870635d4f06bd5b365bbfe517a
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74847288"
---
# <a name="how-to-enable-password-reset-from-the-windows-login-screen"></a>方法:Windows のログイン画面からパスワードをリセットできるようにする

Windows 7、8、8.1、および 10 を実行中のコンピューターでは、Windows のログイン画面でユーザーが自分のパスワードをリセットできるように設定できます。 ユーザーは、[SSPR ポータル](https://aka.ms/sspr)にアクセスするために Web ブラウザーがあるデバイスを探す必要はなくなりました。

![SSPR リンクが示されている Windows 7 と 10 のログイン画面の例](./media/howto-sspr-windows/windows-reset-password.png)

## <a name="general-limitations"></a>一般的な制限事項

- リモート デスクトップまたは Hyper-V 拡張セッションからのパスワードのリセットは、現在サポートされていません。
- この機能は、802.1x ネットワーク認証がデプロイされ、[ユーザー ログオンの直前に実行する] オプションが有効になっているネットワークでは動作しません。 802.1x ネットワーク認証がデプロイされているネットワークでこの機能を有効にするには、マシン認証を使用することをお勧めします。
- ハイブリッド Azure AD 参加済みコンピューターでは、新しいパスワードの使用とキャッシュされた資格情報の更新を行うために、ドメイン コントローラーへの ネットワーク接続経路が必要です。
- イメージを使用する場合は、sysprep を実行する前に、CopyProfile 手順の実行に先立ってビルトイン Administrator に対する Web キャッシュがクリアされることを確認してください。 この手順の詳細については、[カスタムの既定のユーザー プロファイルを使用した場合のパフォーマンスの低下](https://support.microsoft.com/help/4056823/performance-issue-with-custom-default-user-profile)に関するサポート記事を参照してください。
- Windows 10 デバイスでは、以下の設定によって、パスワードの使用とリセットを行う機能が干渉されることがわかっています。
    - v1809 より前のバージョンの Windows 10 のポリシーで Ctrl + Alt + Del が求められる場合、**パスワードのリセット**は機能しません。
    - ロック画面の通知がオフになっている場合、"**パスワードのリセット**" は機能しません。
    - HideFastUserSwitching を有効または 1 に設定する
    - DontDisplayLastUserName を有効または 1 に設定する
    - NoLockScreen を有効または 1 に設定する
    - EnableLostMode をデバイスに対して設定する
    - Explorer.exe をカスタム シェルで置き換える
- 次の具体的な 3 つの設定を組み合わせると、この機能が動作しなくなる可能性があります。
    - 対話型ログオン:CTRL + ALT + DEL を必要としない = Disabled
    - DisableLockScreenAppNotifications = 1 または Enabled
    - IsContentDeliveryPolicyEnforced = 1 または True

## <a name="windows-10-password-reset"></a>Windows 10 でのパスワードのリセット

### <a name="windows-10-prerequisites"></a>Windows 10 の前提条件

- 管理者は、Azure portal から Azure AD のパスワード リセットのセルフサービスを有効にする必要があります。
- **ユーザーは、この機能を使用する前に SSPR に登録する必要があります**。
- ネットワーク プロキシの要件
   - Windows 10 デバイス 
       - `passwordreset.microsoftonline.com` と `ajax.aspnetcdn.com` へのポート 443
       - Windows 10 デバイスでは、コンピューターレベルのプロキシ構成のみがサポートされます
- Windows 10 バージョン April 2018 Update (v1803) 以降を実行している必要があります。また、次のいずれかのデバイスを使用する必要があります。
    - Azure AD 参加済み
    - ハイブリッド Azure AD 参加済み

### <a name="enable-for-windows-10-using-intune"></a>Intune を使用して Windows 10 で有効にする

ログイン画面からパスワードをリセットできるようにする構成変更を Intune を使用してデプロイするのは、最も柔軟な方法です。 Intune では、定義する特定のマシンのグループに構成変更をデプロイすることができます。 この方法では、デバイスの Intune 登録が必要です。

#### <a name="create-a-device-configuration-policy-in-intune"></a>Intune でデバイス構成ポリシーを作成する

1. [Azure portal](https://portal.azure.com) にサインインし、 **[Intune]** をクリックします。
1. **[デバイス構成]**  >  **[プロファイル]**  >  **[プロファイルの作成]** の順に移動して、新しいデバイス構成プロファイルを作成します。
   - わかりやすいプロファイル名を入力します。
   - 必要に応じて、そのプロファイルについてのわかりやすい説明を入力します。
   - プラットフォーム: **Windows 10 以降**
   - プロファイルの種類: **カスタム**
1. **設定**を構成します。
   - "パスワードのリセット" リンクを有効にするには、次の OMA-URI 設定を**追加**します。
      - 設定の内容を説明するわかりやすい名前を入力します。
      - 必要に応じて、その設定についてのわかりやすい説明を入力します。
      - **OMA-URI**: `./Vendor/MSFT/Policy/Config/Authentication/AllowAadPasswordReset` に設定します。
      - **データ型**: **整数**に設定します。
      - **値**: **1** に設定します。
      - **[OK]**
   - **[OK]**
1. **[作成]**
1. このポリシーを、特定のユーザー、デバイス、またはグループに割り当てることができます。 詳細については、「[Microsoft Intune でユーザーおよびデバイス プロファイルを割り当てる](https://docs.microsoft.com/intune/device-profile-assign)」を参照してください。

### <a name="enable-for-windows-10-using-the-registry"></a>レジストリを使用して Windows 10 で有効にする

1. 管理者の資格情報を使用して Windows PC にサインインします
1. **regedit** を管理者として実行します。
1. 次のレジストリ キーを設定します。
   - `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\AzureADAccount`
      - `"AllowPasswordReset"=dword:00000001`

#### <a name="troubleshooting-windows-10-password-reset"></a>Windows 10 でのパスワードのリセットのトラブルシューティング

Azure AD 監査ログには、パスワードのリセットが発生した IP アドレスと ClientType に関する情報が含まれます。

![Azure AD 監査ログにおける Windows 7 のパスワードのリセットの例](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

ユーザーが Windows 10 デバイスのログイン画面から自分のパスワードをリセットすると、`defaultuser1` と呼ばれる低特権の一時的なアカウントが作成されます。 このアカウントは、パスワード リセット プロセスを安全に保つために使用されます。 このアカウントは、それ自体でランダムに生成されたパスワードを持ち、デバイスのサインインには表示されず、ユーザーがパスワードをリセットした後に自動的に削除されます。 複数の `defaultuser` プロファイルが存在する可能性がありますが、安全に無視することができます。

## <a name="windows-7-8-and-81-password-reset"></a>Windows 7、8、および 8.1 でのパスワードのリセット

### <a name="windows-7-8-and-81-prerequisites"></a>Windows 7、8、および 8.1 の前提条件

- 管理者は、Azure portal から Azure AD のパスワード リセットのセルフサービスを有効にする必要があります。
- **ユーザーは、この機能を使用する前に SSPR に登録する必要があります**。
- ネットワーク プロキシの要件
   - Windows 7、8、および 8.1 デバイス
       - `passwordreset.microsoftonline.com` へのポート 443
- パッチが適用された Windows 7 または Windows 8.1 オペレーティング システム。
- [トランスポート層セキュリティ (TLS) レジストリ設定](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-12)にあるガイダンスを使用して有効にした TLS 1.2。
- お使いのコンピューターで複数のサード パーティの資格情報プロバイダーが有効になっている場合は、ログオン画面に複数のユーザー プロファイルが表示されます。

> [!WARNING]
> TLS 1.2 は、自動ネゴシエートするように設定されているだけでなく、有効になっている必要があります。

### <a name="install"></a>Install

1. 有効にしたい Windows バージョン用の適切なインストーラーをダウンロードします。
   - ソフトウェアは [https://aka.ms/sspraddin](https://aka.ms/sspraddin) にある Microsoft ダウンロード センターで入手できます
1. インストールしたいマシンにサインインし、インストーラーを実行します。
1. インストール後に再起動することを強くお勧めします。
1. 再起動後に、ログオン画面でユーザーを選択し、「パスワードを忘れた場合」をクリックして、 パスワード リセットのワークフローを開始します。
1. 画面に表示される手順に従ってワークフローを完了すると、パスワードがリセットされます。

![Windows 7 で「パスワードを忘れた場合」をクリックしたときの例 SSPR フロー](media/howto-sspr-windows/windows-7-sspr.png)

#### <a name="silent-installation"></a>サイレント インストール

- サイレント インストールの場合は、コマンド "msiexec/i SsprWindowsLogon.PROD.msi/qn" を使用します
- サイレント アンインストールの場合は、コマンド "msiexec/x SsprWindowsLogon.PROD.msi/qn" を使用します

#### <a name="troubleshooting-windows-7-8-and-81-password-reset"></a>Windows 7、8、および 8.1 でのパスワードのリセットのトラブルシューティング

イベントは、マシンと Azure AD の両方でログ記録されます。 Azure AD イベントには、パスワードのリセットが発生した IP アドレスと ClientType に関する情報が含められます。

![Azure AD 監査ログにおける Windows 7 のパスワードのリセットの例](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

追加のログ記録が必要な場合は、詳細ログ記録を有効にするようにマシンのレジストリ キーを変更できます。 詳細ログ記録は、トラブルシューティングの目的でのみ有効にしてください。

`HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Authentication\Credential Providers\{86D2F0AC-2171-46CF-9998-4E33B3D7FD4F}`

- 詳細ログ記録を有効にするには、`REG_DWORD: “EnableLogging”` を作成し、それを 1 に設定します。
- 詳細ログ記録を無効にするには、`REG_DWORD: “EnableLogging”` を 0 に変更します。

## <a name="what-do-users-see"></a>ユーザーに表示される画面

Windows デバイスでのパスワードのリセットの構成が完了しました。ユーザーにとっては、何が変わったでしょうか? ログイン画面でパスワードをリセットできることを、ユーザーはどのようにして知るのでしょうか。

![SSPR リンクが示されている Windows 7 と 10 のログイン画面の例](./media/howto-sspr-windows/windows-reset-password.png)

ユーザーがサインインを試みると、ログイン画面に **[パスワードのリセット]** または **[パスワードを忘れた場合]** リンクが表示されます。これらのリンクを選択することで、ログイン画面でパスワード リセットのセルフ サービス機能が作動します。 ユーザーがパスワードをリセットするには、この機能を使用するだけでよく、別のデバイスを使用して Web ブラウザーにアクセスする必要はありません。

[職場または学校アカウントのパスワードをリセットする方法](../user-help/active-directory-passwords-update-your-own-password.md)に関するページで、この機能の使い方がユーザー向けに説明されています。

## <a name="next-steps"></a>次の手順

[許可する認証方法を計画する](concept-authentication-methods.md)

[Windows 10 を構成する](https://docs.microsoft.com/windows/configuration/)
