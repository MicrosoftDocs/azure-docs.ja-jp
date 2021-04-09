---
title: ハイブリッド FIDO 2 セキュリティ キーの既知の問題とトラブルシューティング - Azure Active Directory
description: Azure Active Directory を使用したパスワードレス ハイブリッド FIDO2 セキュリティ キー サインインの既知の問題とトラブルシューティングの方法について説明します
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 435b27255ce58a3541d6b0d3a76bdf4080aa3962
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101648782"
---
# <a name="troubleshooting-for-hybrid-deployments-of-fido2-security-keys-in-azure-ad"></a>Azure AD での FIDO2 セキュリティ キーのハイブリッド デプロイに関するトラブルシューティング 

この記事では、ハイブリッド Azure AD 参加済みデバイスと、オンプレミスのリソースへのパスワードレス サインインに関してよく寄せられる質問について説明します。 このパスワードレスの機能により、FIDO2 セキュリティ キーを使用して、Windows 10 デバイスをハイブリッド Azure AD 参加済みデバイスとして Azure AD 認証することができます。 ユーザーは、FIDO2 キーのような最新の資格情報を使用してデバイス上の Windows にサインインし、オンプレミスのリソースへのシームレスなシングル サインオン (SSO) エクスペリエンスにより、従来の Active Directory Domain Services (AD DS) ベースのリソースにアクセスできます。

ハイブリッド環境のユーザーに対して、次のシナリオがサポートされています。

* FIDO2 セキュリティ キーを使用してハイブリッド Azure AD 参加済みデバイスにサインインし、オンプレミスのリソースへの SSO アクセスを取得します。
* FIDO2 セキュリティ キーを使用して Azure AD 参加済みデバイスにサインインし、オンプレミスのリソースへの SSO アクセスを取得します。

FIDO2 のセキュリティ キーおよびオンプレミスのリソースへのハイブリッド アクセスの概要については、次の記事を参照してください。

* [パスワードレスのセキュリティ キー](howto-authentication-passwordless-security-key.md)
* [パスワードレスの Windows 10](howto-authentication-passwordless-security-key-windows.md)
* [パスワードレスのオンプレミス](howto-authentication-passwordless-security-key-on-premises.md)

## <a name="known-issues"></a>既知の問題

* [Windows Hello Face が高速すぎて、これが既定のサインイン メカニズムであるため、ユーザーが FIDO2 セキュリティ キーを使用してサインインできない](#users-are-unable-to-sign-in-using-fido2-security-keys-as-windows-hello-face-is-too-quick-and-is-the-default-sign-in-mechanism)
* [ハイブリッド Azure AD 参加済みコンピューターを作成した直後に、ユーザーが FIDO2 セキュリティ キーを使用できない](#users-arent-able-to-use-fido2-security-keys-immediately-after-they-create-a-hybrid-azure-ad-joined-machine)
* [FIDO2 セキュリティ キーを使用してサインインし、資格情報のプロンプトを受け取った後、ユーザーが my NTLM ネットワーク リソースへの SSO を取得できない](#users-are-unable-to-get-sso-to-my-ntlm-network-resource-after-signing-in-with-a-fido2-security-key-and-receiving-a-credential-prompt)

### <a name="users-are-unable-to-sign-in-using-fido2-security-keys-as-windows-hello-face-is-too-quick-and-is-the-default-sign-in-mechanism"></a>Windows Hello Face が高速すぎて、これが既定のサインイン メカニズムであるため、ユーザーが FIDO2 セキュリティ キーを使用してサインインできない

Windows Hello Face は、ユーザーが登録されているデバイスに最適なエクスペリエンスです。 FIDO2 セキュリティ キーは、共有デバイスで使用するか、Windows Hello for Business への登録が障壁となる場合に使用することが想定されています。

Windows Hello Face のせいでユーザーが FIDO2 セキュリティ キーによるサインインを試行できない場合、ユーザーは **[設定] > [サインイン オプション]** で Face の登録を削除することで Hello Face サインインを無効化できます。

### <a name="users-arent-able-to-use-fido2-security-keys-immediately-after-they-create-a-hybrid-azure-ad-joined-machine"></a>ハイブリッド Azure AD 参加済みコンピューターを作成した直後に、ユーザーが FIDO2 セキュリティ キーを使用できない

ハイブリッド Azure AD 参加済みコンピューターのクリーン インストールで、ドメイン参加と再起動プロセスの後、FIDO2 セキュリティ キーを使用してサインインする前に、パスワードを使用してサインインし、ポリシーが同期されるまで待つ必要があります。

この動作はドメインに参加しているデバイスの既知の制限であり、FIDO2 セキュリティ キーに固有のものではありません。

現在の状態を確認するには、`dsregcmd /status` コマンドを使用します。 *AzureAdJoined* と *DomainJoined* の両方が *YES* と表示されていることを確認します。

### <a name="users-are-unable-to-get-sso-to-my-ntlm-network-resource-after-signing-in-with-a-fido2-security-key-and-receiving-a-credential-prompt"></a>FIDO2 セキュリティ キーを使用してサインインし、資格情報のプロンプトを受け取った後、ユーザーが my NTLM ネットワーク リソースへの SSO を取得できない

時間内に応答してリソース要求を処理できるように、十分な数の DC に修正プログラムが適用されていることを確認してください。 機能を実行しているサーバーが表示できるかどうかを確認するには、`nltest /dsgetdc:<dc name> /keylist /kdc` の出力を確認します。

この機能を使用して DC を表示できる場合は、ユーザーがサインインした後にユーザーのパスワードが変更されているか、別の問題が発生している可能性があります。 次のセクションで説明するように、Microsoft サポート チームがデバッグを行うためのログを収集してください。

## <a name="troubleshoot"></a>トラブルシューティング

トラブルシューティングには 2 つの領域があります。[Windows クライアントの問題](#windows-client-issues)と[デプロイの問題](#deployment-issues)です。

### <a name="windows-client-issues"></a>Windows クライアントの問題

Windows へのサインインや、Windows 10 デバイスからのオンプレミス リソースへのアクセスに関する問題のトラブルシューティングに役立つデータを収集するには、次の手順を実行します。

1. **フィードバック ハブ** アプリを開きます。 アプリの左下に自分の名前が表示されていることを確認してから、 **[新しいフィードバック項目の作成]** を選択します。

    フィードバック項目の種類として、 *[問題]* を選択します。

1. *[セキュリティとプライバシー]* カテゴリ、 *[FIDO]* サブカテゴリの順に選択します。
1. *[フィードバックと共に添付ファイルと診断情報を Microsoft に送信する]* チェック ボックスを切り替えます。
1. *[問題の再現]* を選択してから、 *[キャプチャの開始]* を選択します。
1. FIDO2 セキュリティ キーを使用してコンピューターをロックし、ロックを解除します。 問題が発生した場合は、他の資格情報でロックを解除してみてください。
1. **[フィードバック ハブ]** に戻り、 **[キャプチャの停止]** を選択して、フィードバックを送信します。
1. *[フィードバック]* ページの *[マイ フィードバック]* タブにアクセスします。最近送信したフィードバックを選択します。
1. 右上隅にある *[共有]* ボタンを選択して、フィードバックへのリンクを取得します。 サポート ケースを開くとき、または既存のサポート ケースに割り当てられたエンジニアに返信することは、このリンクを含めてください。

次のイベント ログとレジストリ キー情報が収集されます。

**レジストリ キー**

* *HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\FIDO [\*]*
* *HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\PasswordForWork\* [\*]*
* *HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Policies\PasswordForWork\* [\*]*

**診断情報**

* ライブ カーネル ダンプ
* AppX パッケージ情報の収集
* UIF コンテキスト ファイル

**イベント ログ**

* *%SystemRoot%\System32\winevt\Logs\Microsoft-Windows-AAD%40Operational.evtx*
* *%SystemRoot%\System32\winevt\Logs\Microsoft-Windows-WebAuthN%40Operational.evtx*
* *%SystemRoot%\System32\winevt\Logs\Microsoft-Windows-HelloForBusiness%40Operational.evtx*

### <a name="deployment-issues"></a>展開に関する問題

Azure AD Kerberos サーバーのデプロイの問題をトラブルシューティングするには、Azure AD Connect に含まれている新しい PowerShell モジュールを使用します。

#### <a name="viewing-the-logs"></a>ログの表示

Azure AD Kerberos サーバーの PowerShell コマンドレットでは、標準の Azure AD Connect ウィザードと同じログ記録を使用します。 コマンドレットから情報またはエラーの詳細を表示するには、次の手順を実行します。

1. Azure AD Connect サーバーで、`C:\ProgramData\AADConnect\` を参照します。 このフォルダーは既定では表示されません。
1. このディレクトリにある最新の `trace-*.log` ファイルを開いて表示します。

#### <a name="viewing-the-azure-ad-kerberos-server-objects"></a>Azure AD Kerberos サーバー オブジェクトの表示

Azure AD Kerberos サーバー オブジェクトを表示し、正常な状態であることを確認するには、次の手順を実行します。

1. Azure AD Connect サーバーで、PowerShell を開いて `C:\Program Files\Microsoft Azure Active Directory Connect\AzureADKerberos\` に移動します。
1. 次の PowerShell コマンドを実行して、Azure AD とオンプレミス AD DS の両方の Azure AD Kerberos サーバーを表示します。

    *corp.contoso.com* は、実際のオンプレミス AD DS ドメインの名前に置き換えます。

    ```powershell
    Import-Module ".\AzureAdKerberos.psd1"

    # Specify the on-premises AD DS domain.
    $domain = "corp.contoso.com"

    # Enter an Azure Active Directory Global Admin username and password.
    $cloudCred = Get-Credential

    # Enter a Domain Admin username and password.
    $domainCred = Get-Credential

    # Get the Azure AD Kerberos Server Object
    Get-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential
    $domainCred
    ```

このコマンドは、Azure AD とオンプレミス AD DS の両方の Azure AD Kerberos サーバーのプロパティを出力します。 プロパティを確認して、すべてが正常な状態であることを確認します。 プロパティの確認には、次の表を使用してください。

最初のプロパティ セットは、オンプレミス AD DS 環境のオブジェクトのものです。 後半 (*Cloud** で始まるプロパティ) は、Azure AD の Kerberos サーバー オブジェクトのものです。

| プロパティ           | 説明  |
|--------------------|--------------|
| Id                 | AD DS ドメイン コントローラー オブジェクトの一意の *ID*。 |
| DomainDnsName      | AD DS ドメインの DNS ドメイン名。 |
| ComputerAccount    | Azure AD Kerberos サーバー オブジェクト (DC) のコンピューター アカウント オブジェクト。 |
| UserAccount        | Azure AD Kerberos サーバーの TGT 暗号化キーを保持する、無効なユーザー アカウント オブジェクト。 このアカウントの DN は *CN=krbtgt_AzureAD,CN=Users,<Domain-DN>* です。 |
| KeyVersion         | Azure AD Kerberos サーバーの TGT 暗号化キーのキー バージョン。 バージョンは、キーの作成時に割り当てられます。 バージョンは、キーがローテーションされるたびに増やされます。 増分はレプリケーション メタデータに基づいており、多くの場合に 1 より大きい値になります。<br /><br /> たとえば、初期の *KeyVersion* が *192272* だったとします。 キーが最初にローテーションされると、バージョンは *212621* に進む可能性があります。<br /><br /> 確認すべき重要な点は、オンプレミスのオブジェクトの *KeyVersion* とクラウド オブジェクトの *CloudKeyVersion* が同じであることです。 |
| KeyUpdatedOn       | Azure AD Kerberos サーバーの TGT 暗号化キーが更新または作成された日時。 |
| KeyUpdatedFrom     | Azure AD Kerberos サーバーの TGT 暗号化キーが最後に更新された DC。 |
| CloudId            | Azure AD オブジェクトからの *ID*。 上記の *Id* と一致している必要があります。 |
| CloudDomainDnsName | Azure AD オブジェクトの *DomainDnsName*。 上記の *DomainDnsName* と一致している必要があります。 |
| CloudKeyVersion    | Azure AD オブジェクトの *KeyVersion*。 上記の *KeyVersion* と一致している必要があります。 |
| CloudKeyUpdatedOn  | Azure AD オブジェクトの *KeyUpdatedOn*。 上記の *KeyUpdatedOn* と一致している必要があります。 |

## <a name="next-steps"></a>次のステップ

FIDO2 のセキュリティ キーおよびオンプレミスのリソースへのハイブリッド アクセスの概要については、次の記事を参照してください。

* [パスワードレスの FIDO2 セキュリティ キー](howto-authentication-passwordless-security-key.md)
* [パスワードレスの Windows 10](howto-authentication-passwordless-security-key-windows.md)
* [パスワードレスのオンプレミス](howto-authentication-passwordless-security-key-on-premises.md)
