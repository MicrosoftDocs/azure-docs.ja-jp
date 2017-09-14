---
title: "既存の NPS サーバーを使用して Azure MFA 機能を提供する | Microsoft Docs"
description: "Azure Multi-Factor Authentication のネットワーク ポリシー サーバー拡張機能は、クラウド ベースの 2 段階認証機能を既存の認証インフラストラクチャに追加するシンプルなソリューションです。"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2017
ms.author: kgremban
ms.reviewer: yossib
ms.custom: H1Hack27Feb2017; it-pro
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: b9061283952ae6b14431f5e88295eefac173ae01
ms.contentlocale: ja-jp
ms.lasthandoff: 08/29/2017

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

![認証要求フローの図](./media/multi-factor-authentication-nps-extension/auth-flow.png)

## <a name="plan-your-deployment"></a>デプロイを計画する

NPS 拡張機能は、自動的に冗長性を処理するため、特別な構成は不要です。

必要な数だけ Azure MFA 対応の NPS サーバーを作成できます。 複数のサーバーをインストールする場合、サーバーごとに異なるクライアント証明書を使用する必要があります。 サーバーごとに証明書を作成することは、各証明書を個別に更新でき、すべてのサーバー全体でのダウンタイムを心配しなくてもよいことを意味します。

VPN サーバーは認証要求をルーティングするため、新しい Azure MFA 対応の NPS サーバーを認識する必要があります。

## <a name="prerequisites"></a>前提条件

NPS 拡張機能は、既存のインフラストラクチャで使用します。 開始する前に、以下の前提条件を確認してください。

### <a name="licenses"></a>ライセンス

Azure MFA の NPS 拡張機能は、[Azure Multi-Factor Authentication のライセンス](multi-factor-authentication.md) (Azure AD Premium、EMS、および MFA サブスクリプションに含まれています) をお持ちのお客様にご利用いただけます。

### <a name="software"></a>ソフトウェア

Windows Server 2008 R2 SP1 以上。

### <a name="libraries"></a>ライブラリ

これらのライブラリは拡張機能を含めて自動的にインストールされます。
-   [Visual Studio 2013 (X64) の Visual C++ 再頒布可能パッケージ](https://www.microsoft.com/download/details.aspx?id=40784)
-   [Windows PowerShell 用 Microsoft Azure Active Directory モジュール バージョン 1.1.166.0](https://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=59185)

### <a name="azure-active-directory"></a>Azure Active Directory

NPS の拡張機能を使用するすべてのユーザーが、Azure AD Connect を使用して Azure Active Directory に同期され、MFA の対象として登録されている必要があります。

拡張機能をインストールするときに、Azure AD テナントのディレクトリ ID と管理資格情報が必要です。 ディレクトリ ID は [Azure Portal](https://portal.azure.com) で確認できます。 管理者としてサインインし、左側の **[Azure Active Directory]** アイコンを選択して、**[プロパティ]** を選択します。 **[ディレクトリ ID]** ボックス内の GUID をコピーして保存します。 NPS 拡張機能をインストールする際は、この GUID をテナント ID として使用します。

![Azure Active Directory のプロパティでディレクトリ ID を探す](./media/multi-factor-authentication-nps-extension/find-directory-id.png)

## <a name="prepare-your-environment"></a>環境を準備する

NPS 拡張機能をインストールする前に、認証トラフィックを処理するように環境を準備する必要があります。

### <a name="enable-the-nps-role-on-a-domain-joined-server"></a>ドメインに参加しているサーバーで NPS 役割を有効にする

NPS サーバーは、Azure Active Directory に接続し、MFA 要求を認証します。 この役割に対して 1 台のサーバーを選択します。 NPS 拡張機能は RADIUS でないすべての要求に対してエラーをスローするため、他のサービスからの要求を処理しないサーバーを選択することをお勧めします。

1. サーバーで、サーバー マネージャーの [クイック スタート] メニューから、**[役割と機能の追加ウィザード]**を開きます。
2. インストールの種類として、**[役割ベースまたは機能ベースのインストール]**を選択します。
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

新しい同期を開始する必要がある場合は、「[Azure AD Connect 同期: Scheduler](../active-directory/connect/active-directory-aadconnectsync-feature-scheduler.md#start-the-scheduler)」の手順を使用します。

### <a name="determine-which-authentication-methods-your-users-can-use"></a>ユーザーが使用できる認証方法を決定する

NPS 拡張機能のデプロイで使用できる認証方法に影響する 2 つの要素があります。

1. RADIUS クライアント (VPN、Netscaler サーバーなど) と NPS サーバー間で使用されるパスワードの暗号化アルゴリズム。
   - **PAP** は、クラウドでの Azure MFA のすべての認証方法 (電話、一方向テキスト メッセージ、モバイル アプリの通知、およびモバイル アプリの確認コード) をサポートします。
   - **CHAPV2** と **EAP** は、電話とモバイル アプリの通知をサポートします。
2. クライアント アプリケーション (VPN、Netscaler サーバーなど) が処理できる入力方式。 たとえば、VPN クライアントに、ユーザーがテキストまたはモバイル アプリから確認コードを入力できるようにする何らかの手段があるかどうか。

NPS 拡張機能をデプロイするときに、これらの要素を使用して、ユーザーに使用できる方法を評価します。 RADIUS クライアントは PAP をサポートしているが、クライアント UX に確認コードの入力フィールドがない場合は、サポートされるオプションは電話とモバイル アプリの通知の 2 つになります。

Azure で[サポートされていない認証方法を無効にする](multi-factor-authentication-whats-next.md#selectable-verification-methods)ことができます。

### <a name="enable-users-for-mfa"></a>ユーザーの MFA を有効にする

完全な NPS 拡張機能をデプロイする前に、2 段階認証を実行するユーザーに対して MFA を有効にする必要があります。 もっと早く、拡張機能をデプロイ時にテストするには、Multi-Factor Authentication に対して完全に登録されている少なくとも 1 つのテスト アカウントが必要です。

テスト アカウントを開始するには、次の手順を使用します。
1. テスト アカウントで [https://aka.ms/mfasetup](https://aka.ms/mfasetup) にサインインします。 
2. 表示されたメッセージに従って、確認方法を設定します。
3. 条件付きアクセス ポリシーを作成するか、[ユーザー状態を変更](multi-factor-authentication-get-started-user-states.md)して、テスト アカウントの 2 段階認証を要求します。 

NPS 拡張機能を使って認証するには、この手順に従って登録しておく必要もあります。

## <a name="install-the-nps-extension"></a>NPS 拡張機能のインストール

> [!IMPORTANT]
> VPN アクセス ポイントとは異なるサーバーに NPS 拡張機能をインストールします。

### <a name="download-and-install-the-nps-extension-for-azure-mfa"></a>Azure MFA の NPS 拡張機能をダウンロードしてインストールする

1.  Microsoft ダウンロード センターから [NPS 拡張機能をダウンロード](https://aka.ms/npsmfa)します。
2.  構成するネットワーク ポリシー サーバーにバイナリをコピーします。
3.  *setup.exe* を実行してインストールの指示に従います。 エラーが発生した場合は、前提条件のセクションの 2 つのライブラリが正常にインストールされていることを再確認します。

### <a name="run-the-powershell-script"></a>PowerShell スクリプトの実行

インストーラーによって、`C:\Program Files\Microsoft\AzureMfa\Config` (C:\ はインストール先のドライブ) の場所に PowerShell スクリプトが作成されます。 この PowerShell スクリプトは、次のアクションを実行します。

-   自己署名証明書を作成します。
-   Azure AD のサービス プリンシパルに証明書の公開キーを関連付ける。
-   ローカル コンピューターの証明書ストアに証明書を格納する。
-   ネットワーク ユーザーに証明書の秘密キーへのアクセスを許可する。
-   NPS を再起動する。

(PowerShell スクリプトで生成される自己署名証明書ではなく) 独自の証明書を使用する場合を除き、PowerShell スクリプトを実行してインストールを完了します。 複数のサーバーに拡張機能をインストールする場合は、それぞれに独自の証明書が必要です。

1. Windows PowerShell を管理者として実行します。
2. ディレクトリを変更します。

   `cd "C:\Program Files\Microsoft\AzureMfa\Config"`

3. インストーラーによって作成された PowerShell スクリプトを実行します。

   `.\AzureMfaNpsExtnConfigSetup.ps1`

4. PowerShell によって、テナント ID の入力が求められます。 前提条件セクションで、 Azure Portal からコピーしたディレクトリ ID の GUID を使用します。
5. 管理者として Azure AD にログインします。
6. スクリプトが終了すると、PowerShell によって成功メッセージが表示されます。  

負荷分散用に設定するすべての追加 NPS サーバーで、この手順を繰り返します。

>[!NOTE]
>PowerShell スクリプトを使用して証明書を生成するのではなく独自の証明書を使用する場合は、NPS 名前付け規則に合わせてください。 サブジェクト名は **CN=\<TenantID\>,OU=Microsoft NPS Extension** にする必要があります。 

## <a name="configure-your-nps-extension"></a>NPS 拡張機能の構成

このセクションでは、NPS 拡張機能を正常にデプロイするために必要な設計上の考慮事項と提案を示します。

### <a name="configuration-limitations"></a>構成の制限

- Azure MFA の NPS 拡張機能には、MFA サーバーからクラウドにユーザーと設定を移行するためのツールは含まれません。 このため、既存のデプロイではなく、新しいデプロイに拡張機能を使用することをお勧めします。 既存のデプロイで拡張機能を使用する場合、ユーザーはクラウドに MFA の詳細を設定するために、再度セキュリティ確認を実行する必要があります。  
- NPS 拡張機能は、オンプレミスの Active Directory の UPN を使用して Azure MFA のユーザーを識別し、セカンダリ認証を行います。代替ログイン ID や カスタム Active Directory フィールドなど、UPN 以外の識別子を使用するように NPS 拡張機能を構成できます。 詳細については、[Multi-Factor Authentication の NPS の拡張機能の詳細構成オプション](multi-factor-authentication-advanced-vpn-configurations.md)に関するページをご覧ください。
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

この設定により、MFA に登録されていないユーザーの扱いが決まります。 キーが存在しないか、設定されていないか、または TRUE に設定されていて、ユーザーが登録されていない場合は、拡張機能による MFA チャレンジが失敗します。 キーが FALSE に設定されていて、ユーザーが登録されていない場合は、MFA を実行することがなく認証が行われます。

ユーザーのオンボーディング中、Azure MFA への登録の一部がまだ完了しないうちに、このキーを作成して FALSE に設定できます。 ただし、MFA に登録されていないユーザーのサインインが許可されることになるため、このキーは運用環境に移行する前に削除してください。

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="how-do-i-verify-that-the-client-cert-is-installed-as-expected"></a>クライアント証明書が正常にインストールされていることを確認するにはどうすればよいですか。

インストーラーによって証明書ストア内に作成された自己署名証明書を探して、ユーザー **NETWORK SERVICE** が秘密キーへのアクセスを許可されていることを確認します。 この証明書のサブジェクト名は **CN \<tenantid\>, OU = Microsoft NPS Extension** になります。

-------------------------------------------------------------

### <a name="how-can-i-verify-that-my-client-cert-is-associated-to-my-tenant-in-azure-active-directory"></a>クライアント証明書が Azure Active Directory のテナントに関連付けられていることを確認するにはどうすればよいですか。

PowerShell コマンド プロンプトを開き、次のコマンドを実行します。

```
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1 
```

このコマンドは、テナントと NPS 拡張機能のインスタンスを関連付けているすべての証明書を PowerShell セッションに出力します。 クライアント証明書を "Base-64 encoded X.509(.cer)" ファイルとして秘密キーなしでエクスポートし、PowerShell が出力したリストと比較します。

1 つ以上の証明書が返されている場合は、判読できる形式のタイムスタンプ Valid-From と Valid-Until を使用して、明らかに無関係な証明書を除外することができます。

-------------------------------------------------------------

### <a name="why-are-my-requests-failing-with-adal-token-error"></a>要求が ADAL トークン エラーで失敗するのはなぜですか。

このエラーにはいくつかの原因が考えられます。 次の手順でトラブルシューティングしてみてください。

1. NPS サーバーを再起動します。
2. クライアント証明書が正常にインストールされていることを確認します。
3. 証明書が Azure AD のテナントに関連付けられていることを確認します。
4. 拡張機能を実行しているサーバーから https://login.microsoftonline.com/ にアクセスできることを確認します。

-------------------------------------------------------------

### <a name="why-does-authentication-fail-with-an-error-in-http-logs-stating-that-the-user-is-not-found"></a>HTTP ログにユーザーが見つからないというエラーが記録され、認証が失敗するのはなぜですか。

AD Connect が実行していること、およびユーザーが Windows Active Directory と Azure Active Directory の両方に存在していることを確認します。

------------------------------------------------------------

### <a name="why-do-i-see-http-connect-errors-in-logs-with-all-my-authentications-failing"></a>すべての認証が失敗し、ログに HTTP 接続エラーが記録されるのはなぜですか。

NPS 拡張機能を実行しているサーバーから https://adnotifications.windowsazure.com に到達できることを確認します。


## <a name="next-steps"></a>次のステップ

- ログインに別の ID を設定するか、「[Advanced configuration options for the NPS extension for Multi-Factor Authentication](nps-extension-advanced-configuration.md)」 (Multi-Factor Authentication の NPS 拡張機能の高度な構成オプション) の 2 段階認証を実行しない IP の例外リストを設定する

- NPS 拡張機能を使用して[リモート デスクトップ ゲートウェイ](nps-extension-remote-desktop-gateway.md)と [VPN サーバー](nps-extension-vpn.md)を統合する方法を学習する

- [Azure Multi-Factor Authentication の NPS 拡張機能からのエラー メッセージを解決する](multi-factor-authentication-nps-errors.md)

