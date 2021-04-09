---
title: Windows デバイスでのセルフサービス パスワード リセット - Azure Active Directory
description: Windows のサインイン画面で Azure Active Directory のセルフサービス パスワード リセットを有効にする方法について説明します。
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/17/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa2d910c017d3cc626f737bdab50315aef8d1e77
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99491387"
---
# <a name="enable-azure-active-directory-self-service-password-reset-at-the-windows-sign-in-screen"></a>Windows のサインイン画面で Azure Active Directory のセルフサービス パスワード リセットを有効にする

セルフサービス パスワード リセット (SSPR) により、Azure Active Directory (Azure AD) のユーザーは、管理者やヘルプ デスクに頼らなくても、自分のパスワードを変更またはリセットできるようになります。 通常、ユーザーは別のデバイスで Web ブラウザーを開いて [SSPR ポータル](https://aka.ms/sspr)にアクセスします。 Windows 7、8、8.1、および 10 を搭載しているコンピューターでのエクスペリエンスを向上させるため、Windows のサインイン画面でユーザーが自分のパスワードをリセットできるようにすることができます。

![SSPR リンクが示されている Windows 7 と 10 のログイン画面の例](./media/howto-sspr-windows/windows-reset-password.png)

> [!IMPORTANT]
> このチュートリアルでは、企業の Windows デバイスの SSPR を有効にする方法を管理者向けに説明します。
>
> IT チームが Windows デバイスから SSPR を使用する機能を有効にしていない場合、またはサインイン時に問題が発生した場合は、ヘルプデスクに連絡して追加のサポートを依頼してください。

## <a name="general-limitations"></a>一般的な制限事項

Windows のサインイン画面から SSPR を使用する場合は、次の制限事項が適用されます。

- リモート デスクトップまたは Hyper-V 拡張セッションからのパスワードのリセットは、現在サポートされていません。
- サード パーティの一部の資格情報プロバイダーでは、この機能に関する問題が発生することがわかっています。
- [EnableLUA レジストリ キー](/openspecs/windows_protocols/ms-gpsb/958053ae-5397-4f96-977f-b7700ee461ec)を変更することによって UAC を無効にすると、問題が発生することが確認されています。
- この機能は、802.1x ネットワーク認証がデプロイされ、[ユーザー ログオンの直前に実行する] オプションが有効になっているネットワークでは動作しません。 802.1x ネットワーク認証がデプロイされているネットワークでこの機能を有効にするには、マシン認証を使用することをお勧めします。
- ハイブリッド Azure AD 参加済みコンピューターでは、新しいパスワードの使用とキャッシュされた資格情報の更新を行うために、ドメイン コントローラーへの ネットワーク接続経路が必要です。 これは、デバイスを組織の内部ネットワークに配置するか、オンプレミスのドメイン コントローラーにネットワーク アクセスできる VPN に配置する必要があることを意味します。
- イメージを使用する場合は、sysprep を実行する前に、CopyProfile 手順の実行に先立ってビルトイン Administrator に対する Web キャッシュがクリアされることを確認してください。 この手順の詳細については、[カスタムの既定のユーザー プロファイルを使用した場合のパフォーマンスの低下](https://support.microsoft.com/help/4056823/performance-issue-with-custom-default-user-profile)に関するサポート記事を参照してください。
- Windows 10 デバイスでは、以下の設定によって、パスワードの使用とリセットを行う機能が干渉されることがわかっています。
    - Windows 10 のポリシーで Ctrl + Alt + Del が求められる場合、**パスワードのリセット** は機能しません。
    - ロック画面の通知がオフになっている場合、**パスワードのリセット** は機能しません。
    - *HideFastUserSwitching* が有効または 1 に設定されている
    - *DontDisplayLastUserName* が有効または 1 に設定されている
    - *NoLockScreen* が有効または 1 に設定されている
    - *EnableLostMode* がデバイスで設定されている
    - Explorer.exe をカスタム シェルで置き換える
- 次の具体的な 3 つの設定を組み合わせると、この機能が動作しなくなる可能性があります。
    - 対話型ログオン:CTRL + ALT + DEL を必要としない = Disabled
    - *DisableLockScreenAppNotifications* = 1 または Enabled
    - Windows SKU が Home または Professional エディションではない

> [!NOTE]
> これらの制限は、デバイスのロック画面からの Windows Hello for Business の PIN のリセットにも適用されます。
>

## <a name="windows-10-password-reset"></a>Windows 10 でのパスワードのリセット

サインイン画面での SSPR を行うために Windows 10 デバイスを構成するには、次の前提条件と構成手順を確認します。

### <a name="windows-10-prerequisites"></a>Windows 10 の前提条件

- 管理者は、[Azure portal から Azure AD のセルフサービス パスワード リセット を有効にする必要があります](tutorial-enable-sspr.md)。
- ユーザーは、この機能を使用する前に [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) で SSPR に登録する必要があります
    - Windows のサインイン画面から SSPR を使用する場合に限らず、すべてのユーザーはパスワードをリセットする前に認証の連絡先情報を入力する必要があります。
- ネットワーク プロキシの要件:
    - `passwordreset.microsoftonline.com` と `ajax.aspnetcdn.com` へのポート 443
    - Windows 10 デバイスでは、マシンレベルのプロキシ構成のみがサポートされます。
- Windows 10 バージョン April 2018 Update (v1803) 以降を実行している必要があります。また、次のいずれかのデバイスを使用する必要があります。
    - Azure AD 参加済み
    - ハイブリッド Azure AD 参加済み

### <a name="enable-for-windows-10-using-intune"></a>Intune を使用して Windows 10 で有効にする

ログイン画面から SSPR を有効にする構成変更を Intune を使用してデプロイするのは、最も柔軟な方法です。 Intune では、定義する特定のマシンのグループに構成変更をデプロイすることができます。 この方法では、デバイスの Intune 登録が必要です。

#### <a name="create-a-device-configuration-policy-in-intune"></a>Intune でデバイス構成ポリシーを作成する

1. [Azure portal](https://portal.azure.com) にサインインして **[Intune]** を選択します。
1. **[デバイス構成]**  >  **[プロファイル]** の順に移動してから、 **[+ Create Profile]\(+ プロファイルの作成\)** を選択して新しいデバイス構成プロファイルを作成します
   - **[プラットフォーム]** には、 *[Windows 10 以降]* を選択します
   - **[プロファイルの種類]** には *[カスタム]* を選択します
1. **[作成]** を選択し、"*Windows 10 サインイン画面用 SSPR*" など、プロファイルにわかりやすい名前を入力します

    必要に応じて、そのプロファイルについてのわかりやすい説明を入力し、 **[次へ]** を選択します。
1. *[構成設定]* で、 **[追加]** を選択し、次の OMA-URI 設定を指定して、パスワードのリセット リンクを有効にします。
      - "*SSPR リンクを追加*" など、設定の内容を説明するわかりやすい名前を入力します。
      - 必要に応じて、その設定についてのわかりやすい説明を入力します。
      - **OMA-URI**: `./Vendor/MSFT/Policy/Config/Authentication/AllowAadPasswordReset` に設定します。
      - **データ型**: **整数** に設定します。
      - **値**: **1** に設定します。

    **[追加]** 、 **[次へ]** の順に選択します。
1. このポリシーを、特定のユーザー、デバイス、またはグループに割り当てることができます。 環境に合わせて必要なプロファイルを割り当て (理想的には最初にデバイスのテスト グループに割り当て)、 **[次へ]** を選択します。

    詳細については、[Microsoft Intune でユーザーとデバイスのプロファイルを割り当てる](/mem/intune/configuration/device-profile-assign)方法に関するページを参照してください。

1. *[Assign profile if OS edition is Windows 10 Enterprise]\(OS エディションが Windows 10 Enterprise の場合にプロファイルを割り当てる\)* など、環境に合わせて適用性ルールを構成し、 **[次へ]** を選択します。
1. プロファイルを確認し、 **[作成]** を選択します。

### <a name="enable-for-windows-10-using-the-registry"></a>レジストリを使用して Windows 10 で有効にする

レジストリ キーを使用してサインイン画面で SSPR を有効にするには、次の手順を実行します。

1. 管理者の資格情報を使用して Windows PC にサインインします。
1. **Windows** + **R** を押して *[実行]* ダイアログを開き、管理者として **regedit** を実行します
1. 次のレジストリ キーを設定します。

    ```cmd
    HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\AzureADAccount
       "AllowPasswordReset"=dword:00000001
    ```

#### <a name="troubleshooting-windows-10-password-reset"></a>Windows 10 でのパスワードのリセットのトラブルシューティング

Windows のサインイン画面から SSPR を使用するときに問題が発生した場合、Azure AD 監査ログには、次の出力例に示すように、パスワードのリセットが発生した IP アドレスと *ClientType* に関する情報が含まれます。

![Azure AD 監査ログにおける Windows 7 のパスワードのリセットの例](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

ユーザーが Windows 10 デバイスのサインイン画面から自分のパスワードをリセットすると、`defaultuser1` と呼ばれる低特権の一時的なアカウントが作成されます。 このアカウントは、パスワード リセット プロセスを安全に保つために使用されます。

このアカウントは、それ自体でランダムに生成されたパスワードを持ち、デバイスのサインインには表示されず、ユーザーがパスワードをリセットした後に自動的に削除されます。 複数の `defaultuser` プロファイルが存在する可能性がありますが、安全に無視することができます。

## <a name="windows-7-8-and-81-password-reset"></a>Windows 7、8、および 8.1 でのパスワードのリセット

サインイン画面での SSPR を行うために Windows 7、8、または 8.1 デバイスを構成するには、次の前提条件と構成手順を確認します。

### <a name="windows-7-8-and-81-prerequisites"></a>Windows 7、8、および 8.1 の前提条件

- 管理者は、[Azure portal から Azure AD のセルフサービス パスワード リセット を有効にする必要があります](tutorial-enable-sspr.md)。
- ユーザーは、この機能を使用する前に [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) で SSPR に登録する必要があります
    - Windows のサインイン画面から SSPR を使用する場合に限らず、すべてのユーザーはパスワードをリセットする前に認証の連絡先情報を入力する必要があります。
- ネットワーク プロキシの要件:
    - `passwordreset.microsoftonline.com` へのポート 443
- パッチが適用された Windows 7 または Windows 8.1 オペレーティング システム。
- [トランスポート層セキュリティ (TLS) レジストリ設定](/windows-server/security/tls/tls-registry-settings#tls-12)にあるガイダンスを使用して有効にした TLS 1.2。
- お使いのコンピューターで複数のサード パーティの資格情報プロバイダーが有効になっている場合は、ログオン画面に複数のユーザー プロファイルが表示されます。

> [!WARNING]
> TLS 1.2 は、自動ネゴシエートするように設定されているだけでなく、有効になっている必要があります。

### <a name="install"></a>インストール

Windows 7、8、および 8.1 のサインイン画面で SSPR を有効にするには、コンピューターに小さなコンポーネントがインストールされている必要があります。 この SSPR コンポーネントをインストールするには、次の手順を実行します。

1. 有効にしたい Windows バージョン用の適切なインストーラーをダウンロードします。

    ソフトウェア インストーラは [https://aka.ms/sspraddin](https://aka.ms/sspraddin) にある Microsoft ダウンロード センターで入手できます
1. インストールしたいマシンにサインインし、インストーラーを実行します。
1. インストール後に再起動することを強くお勧めします。
1. 再起動後に、サインイン画面でユーザーを選択し、「パスワードを忘れた場合」を選択します パスワード リセットのワークフローを開始します。
1. 画面に表示される手順に従ってワークフローを完了すると、パスワードがリセットされます。

![Windows 7 で「パスワードを忘れた場合」をクリックしたときの例 SSPR フロー](media/howto-sspr-windows/windows-7-sspr.png)

#### <a name="silent-installation"></a>サイレント インストール

SSPR コンポーネントは、次のコマンドを使用することで、プロンプトを使用せずにインストールまたはアンインストールできます。

- サイレント インストールの場合は、コマンド "msiexec/i SsprWindowsLogon.PROD.msi/qn" を使用します
- サイレント アンインストールの場合は、コマンド "msiexec/x SsprWindowsLogon.PROD.msi/qn" を使用します

#### <a name="troubleshooting-windows-7-8-and-81-password-reset"></a>Windows 7、8、および 8.1 でのパスワードのリセットのトラブルシューティング

Windows のサインイン画面から SSPR を使用するときに問題が発生した場合は、コンピューターと Azure AD の両方でイベントがログに記録されます。 Azure AD イベントには、次の出力例に示すように、パスワードのリセットが発生した IP アドレスと ClientType に関する情報が含まれます。

![Azure AD 監査ログにおける Windows 7 のパスワードのリセットの例](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

追加のログ記録が必要な場合は、詳細ログ記録を有効にするようにマシンのレジストリ キーを変更できます。 トラブルシューティングの目的でのみ詳細ログ記録を有効にするには、次のレジストリ キー値を使用します。

```cmd
HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Authentication\Credential Providers\{86D2F0AC-2171-46CF-9998-4E33B3D7FD4F}
```

- 詳細ログ記録を有効にするには、`REG_DWORD: "EnableLogging"` を作成し、それを 1 に設定します。
- 詳細ログ記録を無効にするには、`REG_DWORD: "EnableLogging"` を 0 に変更します。

## <a name="what-do-users-see"></a>ユーザーに表示される画面

Windows デバイスで SSPR を構成すると、ユーザーにとっては何が変更されるのでしょうか。 ログイン画面でパスワードをリセットできることを、ユーザーはどのようにして知るのでしょうか。 次のスクリーンショットの例は、SSPR を使用してユーザーが自分のパスワードをリセットするための追加オプションを示しています。

![SSPR リンクが示されている Windows 7 と 10 のログイン画面の例](./media/howto-sspr-windows/windows-reset-password.png)

ユーザーがサインインを試みると、ログイン画面に **[パスワードのリセット]** または **[パスワードを忘れた場合]** リンクが表示されます。これらのリンクを選択することで、ログイン画面でセルフサービス パスワード リセット機能が作動します。 ユーザーがパスワードをリセットするには、この機能を使用するだけでよく、別のデバイスを使用して Web ブラウザーにアクセスする必要はありません。

この機能を使用する場合のユーザー向けの詳細については、「[職場または学校のパスワードをリセットする](../user-help/active-directory-passwords-update-your-own-password.md)」を参照してください

## <a name="next-steps"></a>次のステップ

ユーザー登録エクスペリエンスを簡略化するために、[SSPR のためのユーザー認証の連絡先情報を事前に設定](howto-sspr-authenticationdata.md)することができます。
