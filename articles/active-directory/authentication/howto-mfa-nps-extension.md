---
title: 既存の NPS サーバーを使用して Azure MFA 機能を提供する - Azure Active Directory
description: クラウドベースの 2 段階認証機能を既存の認証インフラストラクチャに追加する
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca6f79b5febdbf12c80ab85d07117bf937babef0
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798205"
---
# <a name="integrate-your-existing-nps-infrastructure-with-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication と既存の NPS インフラストラクチャの統合

Azure MFA のネットワーク ポリシー サーバー (NPS) 拡張機能は、既存のサーバーを使用してクラウド ベースの MFA 機能を認証インフラストラクチャに追加します。 NPS 拡張機能を使用すると、新しいサーバーをインストール、構成、管理することなく、電話、テキスト メッセージ、またはモバイル アプリによる検証を既存の認証フローに追加できます。 

この拡張機能は、Azure MFA Server をデプロイしないで VPN 接続を保護する必要のある組織を対象に作成されました。 NPS 拡張機能は、RADIUS とクラウド ベース Azure MFA の間のアダプターとして機能し、フェデレーション ユーザーまたは同期済みユーザーに、認証の 2 番目の要素を提供します。

Azure MFA の NPS 拡張機能を使用する場合、認証フローには次のコンポーネントが含まれます。 

1. **NAS/VPN サーバー**: VPN クライアントから受信した要求を RADIUS 要求に変換して NPS サーバーに送信します。 
2. **NPS サーバー**: Active Directory に接続して RADIUS 要求のプライマリ認証を行います。成功したら、インストール済みの任意の拡張機能に要求を渡します。  
3. **NPS 拡張機能**: セカンダリ認証のために Azure MFA への要求をトリガーします。 応答を受信したら、MFA チャレンジが成功していた場合は、Azure STS が発行した MFA クレームを含むセキュリティ トークンを NPS サーバーに提供して認証要求を完了します。  
4. **Azure MFA**: Azure Active Directory と通信してユーザーの詳細を取得し、ユーザーに構成されている検証メソッドを使用してセカンダリ認証を行います。

次の図に、この認証要求フローの概要を示します。 

![認証要求フローの図](./media/howto-mfa-nps-extension/auth-flow.png)

## <a name="plan-your-deployment"></a>デプロイを計画する

NPS 拡張機能は、自動的に冗長性を処理するため、特別な構成は不要です。

必要な数だけ Azure MFA 対応の NPS サーバーを作成できます。 複数のサーバーをインストールする場合、サーバーごとに異なるクライアント証明書を使用する必要があります。 サーバーごとに証明書を作成することは、各証明書を個別に更新でき、すべてのサーバー全体でのダウンタイムを心配しなくてもよいことを意味します。

VPN サーバーは認証要求をルーティングするため、新しい Azure MFA 対応の NPS サーバーを認識する必要があります。

## <a name="prerequisites"></a>前提条件

NPS 拡張機能は、既存のインフラストラクチャで使用します。 開始する前に、以下の前提条件を確認してください。

### <a name="licenses"></a>ライセンス

Azure MFA の NPS 拡張機能は、[Azure Multi-Factor Authentication のライセンス](multi-factor-authentication.md) (Azure AD Premium、EMS、または MFA スタンドアロン ライセンスに含まれています) をお持ちのお客様にご利用いただけます。 使用量ベースの Azure MFA のライセンス (ユーザーごと、認証ごとのライセンスなど) は、NPS 拡張機能に対応していません。 

### <a name="software"></a>ソフトウェア

Windows Server 2008 R2 SP1 以上。

### <a name="libraries"></a>ライブラリ

これらのライブラリは拡張機能を含めて自動的にインストールされます。

- [Visual Studio 2013 (X64) の Visual C++ 再頒布可能パッケージ](https://www.microsoft.com/download/details.aspx?id=40784)
- [Windows PowerShell 用 Microsoft Azure Active Directory モジュール バージョン 1.1.166.0](https://www.powershellgallery.com/packages/MSOnline/1.1.166.0)

Windows PowerShell 用 Microsoft Azure Active Directory モジュールは、セットアップ プロセスの一環として実行する構成スクリプトによってインストールされます (モジュールがまだ存在しない場合)。 このモジュールをまだインストールしていない場合、事前にインストールする必要はありません。

### <a name="azure-active-directory"></a>Azure Active Directory

NPS の拡張機能を使用するすべてのユーザーが、Azure AD Connect を使用して Azure Active Directory に同期され、MFA の対象として登録されている必要があります。

拡張機能をインストールするときに、Azure AD テナントのディレクトリ ID と管理資格情報が必要です。 ディレクトリ ID は [Azure Portal](https://portal.azure.com) で確認できます。 管理者としてサインインし、左側の **[Azure Active Directory]** アイコンを選択して、 **[プロパティ]** を選択します。 **[ディレクトリ ID]** ボックス内の GUID をコピーして保存します。 NPS 拡張機能をインストールする際は、この GUID をテナント ID として使用します。

![Azure Active Directory のプロパティでディレクトリ ID を探す](./media/howto-mfa-nps-extension/find-directory-id.png)

### <a name="network-requirements"></a>ネットワークの要件

NPS サーバーは、ポート 80 および 443 を使って次の URL と通信できる必要があります。

- https:\//adnotifications.windowsazure.com
- https:\//login.microsoftonline.com

さらに、[指定された PowerShell スクリプトを使用してアダプターの設定](#run-the-powershell-script)を行うには、次の URL への接続が必要です。

- https:\//login.microsoftonline.com
- https:\//provisioningapi.microsoftonline.com
- https:\//aadcdn.msauth.net

## <a name="prepare-your-environment"></a>環境を準備する

NPS 拡張機能をインストールする前に、認証トラフィックを処理するように環境を準備する必要があります。

### <a name="enable-the-nps-role-on-a-domain-joined-server"></a>ドメインに参加しているサーバーで NPS 役割を有効にする

NPS サーバーは、Azure Active Directory に接続し、MFA 要求を認証します。 この役割に対して 1 台のサーバーを選択します。 NPS 拡張機能は RADIUS でないすべての要求に対してエラーをスローするため、他のサービスからの要求を処理しないサーバーを選択することをお勧めします。 NPS サーバーを、環境のプライマリおよびセカンダリ認証サーバーとしてセットアップする必要があります。別のサーバーに RADIUS 要求をプロキシすることはできません。

1. サーバーで、サーバー マネージャーの [クイック スタート] メニューから、 **[役割と機能の追加ウィザード]** を開きます。
2. インストールの種類として、 **[役割ベースまたは機能ベースのインストール]** を選択します。
3. **[ネットワーク ポリシーとアクセス サービス]** サーバーの役割を選択します。 ウィンドウがポップアップし、この役割を実行するために必要な機能が通知される場合があります。
4. [確認] ページまでウィザードを続行します。 **[インストール]** を選択します。

NPS 用にサーバーを指定したので、VPN ソリューションからの着信 RADIUS 要求を処理するように、このサーバーを構成する必要もあります。

### <a name="configure-your-vpn-solution-to-communicate-with-the-nps-server"></a>NPS サーバーと通信するように VPN ソリューションを構成する

使用する VPN ソリューションに応じて、RADIUS 認証ポリシーを構成する手順は異なります。 このポリシーを RADIUS NPS サーバーをポイントするように構成します。

### <a name="sync-domain-users-to-the-cloud"></a>クラウドにドメイン ユーザーを同期する

この手順は、テナントで既に完了している可能性がありますが、Azure AD Connect が最近データベースを同期させたことを再確認することをお勧めします。

1. [Azure Portal](https://portal.azure.com) に管理者としてサインインします。
2. **[Azure Active Directory]**  >  **[Azure AD Connect]** を選択します。
3. 同期の状態が **[有効]** であり、最後の同期が 1 時間以内であったことを確認します。

新しい同期を開始する必要がある場合は、「[Azure AD Connect 同期: スケジューラ](../hybrid/how-to-connect-sync-feature-scheduler.md#start-the-scheduler)」の手順を使用します。

### <a name="determine-which-authentication-methods-your-users-can-use"></a>ユーザーが使用できる認証方法を決定する

NPS 拡張機能のデプロイで使用できる認証方法に影響する 2 つの要素があります。

1. RADIUS クライアント (VPN、Netscaler サーバーなど) と NPS サーバー間で使用されるパスワードの暗号化アルゴリズム。
   - **PAP** は、クラウドでの Azure MFA のすべての認証方法 (電話、一方向テキスト メッセージ、モバイル アプリの通知、およびモバイル アプリの確認コード) をサポートします。
   - **CHAPV2** と **EAP** は、電話とモバイル アプリの通知をサポートします。

      > [!NOTE]
      > NPS 拡張機能をデプロイするときに、これらの要素を使用して、ユーザーに使用できる方法を評価します。 RADIUS クライアントは PAP をサポートしているが、クライアント UX に確認コードの入力フィールドがない場合は、サポートされるオプションは電話とモバイル アプリの通知の 2 つになります。
      >
      > さらに、VPN クライアント UX が入力フィールドをサポートしていて、ネットワーク アクセス ポリシーが構成されている場合、認証は成功するかもしれませんが、ネットワーク ポリシーで構成された RADIUS 属性は、RRAS サーバーのようなネットワーク アクセス デバイスにも VPN クライアントにも適用されません。 その結果、VPN クライアントは、必要以上に多くのアクセス権を持つか、アクセス権が足りずにアクセスできなくなる可能性があります。
      >

2. クライアント アプリケーション (VPN、Netscaler サーバーなど) が処理できる入力方式。 たとえば、VPN クライアントに、ユーザーがテキストまたはモバイル アプリから確認コードを入力できるようにするなんらかの手段があるかどうか。

Azure で[サポートされていない認証方法を無効にする](howto-mfa-mfasettings.md#verification-methods)ことができます。

### <a name="register-users-for-mfa"></a>ユーザーを MFA に登録する

NPS 拡張機能を展開して使用する前に、2 段階認証を実行する必要があるユーザーを MFA に登録する必要があります。 もっと早く、拡張機能をデプロイ時にテストするには、Multi-Factor Authentication に対して完全に登録されている少なくとも 1 つのテスト アカウントが必要です。

テスト アカウントを開始するには、次の手順を使用します。

1. テスト アカウントで [https://aka.ms/mfasetup](https://aka.ms/mfasetup) にサインインします。
2. 表示されたメッセージに従って、確認方法を設定します。
3. テスト アカウントに対して多要素認証を要求するには、[条件付きアクセス ポリシーを作成](howto-mfa-getstarted.md#create-conditional-access-policy)します。

## <a name="install-the-nps-extension"></a>NPS 拡張機能のインストール

> [!IMPORTANT]
> VPN アクセス ポイントとは異なるサーバーに NPS 拡張機能をインストールします。

### <a name="download-and-install-the-nps-extension-for-azure-mfa"></a>Azure MFA の NPS 拡張機能をダウンロードしてインストールする

1. Microsoft ダウンロード センターから [NPS 拡張機能をダウンロード](https://aka.ms/npsmfa)します。
2. 構成するネットワーク ポリシー サーバーにバイナリをコピーします。
3. *setup.exe* を実行してインストールの指示に従います。 エラーが発生した場合は、前提条件のセクションの 2 つのライブラリが正常にインストールされていることを再確認します。

#### <a name="upgrade-the-nps-extension"></a>NPS 拡張機能のアップグレード

既存の NPS 拡張機能のインストールをアップグレードするときは、基になるサーバーの再起動を回避するために、次の手順を行います。

1. 既存のバージョンをアンインストールする
1. 新しいインストーラーを実行する
1. ネットワーク ポリシー サーバー (IAS) サービスを再起動する

### <a name="run-the-powershell-script"></a>PowerShell スクリプトの実行

インストーラーによって、`C:\Program Files\Microsoft\AzureMfa\Config` (C:\ はインストール先のドライブ) の場所に PowerShell スクリプトが作成されます。 この PowerShell スクリプトは、実行されるたびに次のアクションを実行します。

- 自己署名証明書を作成します。
- Azure AD のサービス プリンシパルに証明書の公開キーを関連付ける。
- ローカル コンピューターの証明書ストアに証明書を格納する。
- ネットワーク ユーザーに証明書の秘密キーへのアクセスを許可する。
- NPS を再起動する。

(PowerShell スクリプトで生成される自己署名証明書ではなく) 独自の証明書を使用する場合を除き、PowerShell スクリプトを実行してインストールを完了します。 複数のサーバーに拡張機能をインストールする場合は、それぞれに独自の証明書が必要です。

1. Windows PowerShell を管理者として実行します。
2. ディレクトリを変更します。

   `cd "C:\Program Files\Microsoft\AzureMfa\Config"`

3. インストーラーによって作成された PowerShell スクリプトを実行します。

   `.\AzureMfaNpsExtnConfigSetup.ps1`

4. 管理者として Azure AD にログインします。
5. PowerShell によって、テナント ID の入力が求められます。 前提条件セクションで、 Azure Portal からコピーしたディレクトリ ID の GUID を使用します。
6. スクリプトが終了すると、PowerShell によって成功メッセージが表示されます。  

負荷分散用に設定するすべての追加 NPS サーバーで、この手順を繰り返します。

以前のコンピューター証明書が期限切れになり、新しい証明書が生成された場合は、期限切れの証明書をすべて削除する必要があります。 期限切れの証明書があると、NPS 拡張機能の起動で問題が生じる可能性があります。

> [!NOTE]
> PowerShell スクリプトを使用して証明書を生成するのではなく独自の証明書を使用する場合は、NPS 名前付け規則に合わせてください。 サブジェクト名は **CN=\<TenantID\>,OU=Microsoft NPS Extension** にする必要があります。 

### <a name="certificate-rollover"></a>証明書のロールオーバー

NPS 拡張機能のリリース 1.0.1.32 では、複数の証明書の読み取りがサポートされるようになりました。 この機能により、証明書の有効期限が切れる前のローリング アップデートが容易になります。 組織で以前のバージョンの NPS 拡張機能を実行している場合は、バージョン 1.0.1.32 以降にアップグレードする必要があります。

`AzureMfaNpsExtnConfigSetup.ps1` スクリプトによって作成された証明書は、2 年間有効です。 IT 組織は、証明書の有効期限を監視する必要があります。 NPS 拡張機能の証明書は、ローカル コンピューターの証明書ストアの [個人用] に配置され、スクリプトに提供されるテナント ID に対して発行されます。

証明書の有効期限が近づいている場合は、置き換えるための新しい証明書を作成する必要があります。  このプロセスを完了するには、`AzureMfaNpsExtnConfigSetup.ps1` を再度実行し、メッセージが表示されたら同じテナント ID を指定します。 このプロセスは、環境内の各 NPS サーバーで繰り返す必要があります。

## <a name="configure-your-nps-extension"></a>NPS 拡張機能の構成

このセクションでは、NPS 拡張機能を正常にデプロイするために必要な設計上の考慮事項と提案を示します。

### <a name="configuration-limitations"></a>構成の制限

- Azure MFA の NPS 拡張機能には、MFA サーバーからクラウドにユーザーと設定を移行するためのツールは含まれません。 このため、既存のデプロイではなく、新しいデプロイに拡張機能を使用することをお勧めします。 既存のデプロイで拡張機能を使用する場合、ユーザーはクラウドに MFA の詳細を設定するために、再度セキュリティ確認を実行する必要があります。  
- NPS 拡張機能は、オンプレミスの Active Directory の UPN を使用して Azure MFA のユーザーを識別し、セカンダリ認証を行います。代替ログイン ID や カスタム Active Directory フィールドなど、UPN 以外の識別子を使用するように NPS 拡張機能を構成できます。 詳細については、[Microsoft Multi-Factor Authentication のための NPS 拡張機能の詳細構成オプション](howto-mfa-nps-extension-advanced.md)に関するページをご覧ください。
- すべての暗号化プロトコルで、すべての検証メソッドがサポートされるわけではありません。
   - **PAP** は、電話、テキスト メッセージ、モバイル アプリの通知、およびモバイル アプリの確認コードをサポートします。
   - **CHAPV2** と **EAP** は、電話とモバイル アプリの通知をサポートします。

### <a name="control-radius-clients-that-require-mfa"></a>MFA を必須とする RADIUS クライアントの制御

NPS 拡張機能を使用して RADIUS クライアントに対して MFA を有効にすると、このクライアントに対するすべての認証で MFA の実行が必須になります。 一部の RADIUS クライアントに対してのみ MFA を有効にする場合は、2 つの NPS サーバーを構成し、そのうちの一方に拡張機能をインストールします。 MFA を必須とする RADIUS クライアントについては、拡張機能が構成された NPS サーバーに要求を送信するよう構成し、他の RADIUS クライアントについては、拡張機能が構成されていない NPS サーバーに要求を送信するよう構成します。

### <a name="prepare-for-users-that-arent-enrolled-for-mfa"></a>MFA に登録されていないユーザーのための準備

MFA に登録されていないユーザーがいる場合は、そのユーザーが認証しようとしたときの動作を決める必要があります。 レジストリ パス *HKLM\Software\Microsoft\AzureMFA* にあるレジストリ設定 *REQUIRE_USER_MATCH* を使用して、この機能の動作を制御します。 この設定の構成オプションは 1 つだけです。

| キー | 値 | 既定値 |
| --- | ----- | ------- |
| REQUIRE_USER_MATCH | TRUE または FALSE | 未設定 (TRUE に相当) |

この設定により、MFA に登録されていないユーザーの扱いが決まります。 キーが存在しないか、設定されていないか、または TRUE に設定されていて、ユーザーが登録されていない場合は、拡張機能による MFA チャレンジが失敗します。 キーが FALSE に設定されていて、ユーザーが登録されていない場合は、MFA を実行することがなく認証が行われます。 ユーザーが MFA に登録されている場合、REQUIRE_USER_MATCH が FALSE に設定されている場合でも、ユーザーは MFA で認証する必要があります。

ユーザーのオンボーディング中、Azure MFA への登録の一部がまだ完了しないうちに、このキーを作成して FALSE に設定できます。 ただし、MFA に登録されていないユーザーのサインインが許可されることになるため、このキーは運用環境に移行する前に削除してください。

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="nps-extension-health-check-script"></a>NPS 拡張機能の正常性チェック スクリプト

次のスクリプトは、NPS 拡張機能のトラブルシューティングを行うときに、基本的な正常性チェックの手順を実行するために、TechNet ギャラリーから入手できます。

[MFA_NPS_Troubleshooter.ps1](https://gallery.technet.microsoft.com/Azure-MFA-NPS-Extension-648de6bb)

---

### <a name="how-do-i-verify-that-the-client-cert-is-installed-as-expected"></a>クライアント証明書が正常にインストールされていることを確認するにはどうすればよいですか。

インストーラーによって証明書ストア内に作成された自己署名証明書を探して、ユーザー **NETWORK SERVICE** が秘密キーへのアクセスを許可されていることを確認します。 この証明書のサブジェクト名は **CN \<tenantid\>, OU = Microsoft NPS Extension** になります。

*AzureMfaNpsExtnConfigSetup.ps1* スクリプトによって生成された自己署名証明書の有効期間も 2 年間です。 証明書がインストールされていることを確認するときは、証明書の有効期限が切れていないことも確認する必要があります。

---

### <a name="how-can-i-verify-that-my-client-cert-is-associated-to-my-tenant-in-azure-active-directory"></a>クライアント証明書が Azure Active Directory のテナントに関連付けられていることを確認するにはどうすればよいですか。

PowerShell コマンド プロンプトを開き、次のコマンドを実行します。

``` PowerShell
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1
```

このコマンドは、テナントと NPS 拡張機能のインスタンスを関連付けているすべての証明書を PowerShell セッションに出力します。 クライアント証明書を "Base-64 encoded X.509(.cer)" ファイルとして秘密キーなしでエクスポートし、PowerShell が出力したリストと比較します。

次のコマンドによって、名前が "npscertificate"、形式が .cer のファイルが "C:" ドライブに作成されます。

``` PowerShell
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1 | select -ExpandProperty "value" | out-file c:\npscertficicate.cer
```

このコマンドを実行したら、ご利用の C ドライブに移動して、そのファイルを見つけてダブルクリックします。 [詳細] に移動して、[サムプリント] までスクロール ダウンし、サーバーにインストールされている証明書のサムプリントとこれを比較します。 証明書のサムプリントが一致する必要があります。

1 つ以上の証明書が返されている場合は、判読できる形式のタイムスタンプ Valid-From と Valid-Until を使用して、明らかに無関係な証明書を除外することができます。

---

### <a name="why-cant-i-sign-in"></a>サインインできないのはなぜか

パスワードの有効期限が切れていないことを確認します。 NPS 拡張機能では、サインイン ワークフローの中でパスワードを変更することはできません。 ご自分の組織の IT スタッフに連絡してサポートを依頼してください。

---

### <a name="why-are-my-requests-failing-with-adal-token-error"></a>要求が ADAL トークン エラーで失敗するのはなぜですか。

このエラーにはいくつかの原因が考えられます。 次の手順でトラブルシューティングしてみてください。

1. NPS サーバーを再起動します。
2. クライアント証明書が正常にインストールされていることを確認します。
3. 証明書が Azure AD のテナントに関連付けられていることを確認します。
4. 拡張機能を実行しているサーバーから https://login.microsoftonline.com/ にアクセスできることを確認します。

---

### <a name="why-does-authentication-fail-with-an-error-in-http-logs-stating-that-the-user-is-not-found"></a>HTTP ログにユーザーが見つからないというエラーが記録され、認証が失敗するのはなぜですか。

AD Connect が実行していること、およびユーザーが Windows Active Directory と Azure Active Directory の両方に存在していることを確認します。

---

### <a name="why-do-i-see-http-connect-errors-in-logs-with-all-my-authentications-failing"></a>すべての認証が失敗し、ログに HTTP 接続エラーが記録されるのはなぜですか。

NPS 拡張機能を実行しているサーバーから https://adnotifications.windowsazure.com に到達可能であることを確認します。

---

### <a name="why-is-authentication-not-working-despite-a-valid-certificate-being-present"></a>有効な証明書があるにもかかわらず認証が機能しないのはなぜですか。

以前のコンピューター証明書が期限切れになり、新しい証明書が生成された場合は、期限切れの証明書をすべて削除する必要があります。 期限切れの証明書があると、NPS 拡張機能の起動で問題が生じる可能性があります。

有効な証明書があるかどうかを確認するには、MMC を使用してローカル コンピューター アカウントの証明書ストアをチェックし、その証明書が有効期限を過ぎていないことを確認してください。 有効な証明書を新しく生成するには、「[PowerShell スクリプトの実行](#run-the-powershell-script)」セクションの手順を再度行います。

## <a name="managing-the-tlsssl-protocols-and-cipher-suites"></a>TLS/SSL プロトコルと暗号スイートの管理

組織で求められない限り、以前の強度の低い暗号スイートを無効にするか、削除することをお勧めします。 このタスクを完了する方法については、「[Managing SSL/TLS Protocols and Cipher Suites for AD FS (AD FS の SSL/TLS プロトコルおよび暗号スイートの管理)](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs)」を参照してください

### <a name="additional-troubleshooting"></a>その他のトラブルシューティング

その他のトラブルシューティングのガイダンスと可能なソリューションについては、「[Azure Multi-Factor Authentication の NPS 拡張機能からのエラー メッセージを解決する](howto-mfa-nps-extension-errors.md)」を参照してください。

## <a name="next-steps"></a>次の手順

- ログインに別の ID を設定するか、「[Advanced configuration options for the NPS extension for Multi-Factor Authentication](howto-mfa-nps-extension-advanced.md)」 (Multi-Factor Authentication の NPS 拡張機能の高度な構成オプション) の 2 段階認証を実行しない IP の例外リストを設定する

- NPS 拡張機能を使用して[リモート デスクトップ ゲートウェイ](howto-mfa-nps-extension-rdg.md)と [VPN サーバー](howto-mfa-nps-extension-vpn.md)を統合する方法を学習する

- [Azure Multi-Factor Authentication の NPS 拡張機能からのエラー メッセージを解決する](howto-mfa-nps-extension-errors.md)
