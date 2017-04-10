---
title: "既存の NPS サーバーを使用して Azure MFA 機能を提供する | Microsoft Docs"
description: "Azure Multi-Factor Authentication のネットワーク ポリシー サーバー拡張機能は、クラウド ベースの 2 段階認証機能を既存の認証インフラストラクチャに追加するシンプルなソリューションです。"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2017
ms.author: kgremban
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 9553c9ed02fa198d210fcb64f4657f84ef3df801
ms.openlocfilehash: 34919221a82a024bd3a1d09c1def6040ff6c55e1
ms.lasthandoff: 03/23/2017

---
# <a name="integrate-your-existing-nps-infrastructure-with-azure-multi-factor-authentication---public-preview"></a>Azure Multi-Factor Authentication と既存の NPS インフラストラクチャの統合 - パブリック プレビュー

Azure MFA のネットワーク ポリシー サーバー (NPS) 拡張機能は、既存のサーバーを使用してクラウド ベースの MFA 機能を認証インフラストラクチャに追加します。 NPS 拡張機能を使用すると、新しいサーバーをインストール、構成、管理することなく、電話、SMS、またはモバイル アプリによる検証を既存の認証フローに追加できます。 
 
Azure MFA の NPS 拡張機能を使用する場合、認証フローには次のコンポーネントが含まれます。 

1. **NAS/VPN サーバー**: VPN クライアントから受信した要求を RADIUS 要求に変換して NPS サーバーに送信します。 
2. **NPS サーバー**: Active Directory に接続して RADIUS 要求のプライマリ認証を行います。成功したら、インストール済みの任意の拡張機能に要求を渡します。  
3. **NPS 拡張機能**: セカンダリ認証のために Azure MFA への要求をトリガーします。 応答を受信したら、MFA チャレンジが成功していた場合は、Azure STS が発行した MFA クレームを含むセキュリティ トークンを NPS サーバーに提供して認証要求を完了します。  
4. **Azure MFA**: Azure Active Directory と通信してユーザーの詳細を取得し、ユーザーに構成されている検証メソッドを使用してセカンダリ認証を行います。

次の図に、この認証要求フローの概要を示します。 

![認証要求フローの図](./media/multi-factor-authentication-nps-extension/auth-flow.png)

## <a name="prerequisites"></a>前提条件

NPS 拡張機能は、既存のインフラストラクチャで使用します。 開始する前に、以下の前提条件を確認してください。

### <a name="licenses"></a>ライセンス

Azure MFA の NPS 拡張機能は、[Azure Multi-Factor Authentication のライセンス](multi-factor-authentication.md) (Azure AD Premium、EMS、および MFA サブスクリプションに含まれています) をお持ちのお客様にご利用いただけます。

### <a name="software"></a>ソフトウェア

Windows Server 2008 R2 SP1 以降 (NPS コンポーネントが有効になっている必要があります)

### <a name="libraries"></a>ライブラリ

-    [Visual Studio 2013 (X64) の Visual C++ 再頒布可能パッケージ](https://www.microsoft.com/download/details.aspx?id=40784)
-    [Windows PowerShell 用 Microsoft Azure Active Directory モジュール バージョン 1.1.166.0](https://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=59185)

### <a name="azure-active-directory"></a>Azure Active Directory

NPS の拡張機能を使用するすべてのユーザーが、Azure AD Connect を使用して Azure Active Directory に同期され、MFA を有効にしている必要があります。

拡張機能をインストールするときに、Azure AD テナントのディレクトリ ID と管理資格情報が必要です。 ディレクトリ ID は [Azure Portal](https://portal.azure.com) で確認できます。 管理者としてサインインし、左側の **[Azure Active Directory]** アイコンを選択して、**[プロパティ]** を選択します。 **[ディレクトリ ID]** ボックス内の GUID をコピーして保存します。

![Azure Active Directory のプロパティでディレクトリ ID を探す](./media/multi-factor-authentication-nps-extension/find-directory-id.png)

## <a name="install-the-nps-extension"></a>NPS 拡張機能のインストール

> [!IMPORTANT]
> VPN アクセス ポイントとは異なるサーバーに NPS 拡張機能をインストールします。 

Azure MFA の NPS 拡張機能をインストールするには:

1.    Microsoft Download Center から [NPS 拡張機能をダウンロード](https://aka.ms/npsmfa)します。
2.    構成するバイナリをネットワーク ポリシー サーバーにコピーします。
3.    *setup.exe* を実行してインストールの指示に従います。

インストールが完了したら、次の場所に PowerShell スクリプトが作成されます: `C:\Program Files\Microsoft\AzureMfa\Config` (C:\ はインストール先のドライブ)。 この PowerShell スクリプトは、次のアクションを実行します。

-    自己署名証明書を作成します。
-    Azure AD のサービス プリンシパルに証明書の公開キーを関連付ける。
-    ローカル コンピューターの証明書ストアに証明書を格納する。
-    ネットワーク ユーザーに証明書の秘密キーへのアクセスを許可する。
-    NPS を再起動する。

(PowerShell スクリプトで生成される自己署名証明書ではなく) 独自の証明書を使用する場合を除き、PowerShell スクリプトを実行してインストールを完了します。 複数のサーバーに拡張機能をインストールする場合は、証明書を更新する場合にダウンタイムがないように、それぞれのサーバーに独自の証明書が必要です。 

## <a name="configure-your-nps-extension"></a>NPS 拡張機能の構成

このセクションでは、NPS 拡張機能を正常にデプロイするために必要な設計上の考慮事項と提案を示します。

### <a name="configurations-limitations"></a>構成の制限事項

- NPS 拡張機能は新規デプロイ用であり、既存のデプロイメントで使用するものではありません。 このため、Azure MFA の NPS 拡張機能には、MFA サーバーからクラウドにユーザーと設定を移行するためのツールは含まれません。

- NPS 拡張機能は、オンプレミスの Active Directory の UPN を使用して Azure MFA のユーザーを識別し、セカンダリ認証を行います。 代替ログイン ID や カスタム AD フィールドなど、UPN 以外の識別子を使用するように NPS 拡張機能を構成することはできません。  

### <a name="control-radius-clients-that-require-mfa"></a>MFA を必須とする RADIUS クライアントの制御

NPS 拡張機能を使用して RADIUS クライアントに対して MFA を有効にすると、このクライアントに対するすべての認証で MFA の実行が必須になります。 一部の RADIUS クライアントに対してのみ MFA を有効にする場合は、2 つの NPS サーバーを構成し、そのうちの一方に拡張機能をインストールします。 MFA を必須とする RADIUS クライアントについては、拡張機能が構成された NPS サーバーに要求を送信するよう構成し、他の RADIUS クライアントについては、拡張機能が構成されていない NPS サーバーに要求を送信するよう構成します。

### <a name="prepare-for-users-that-arent-enrolled-for-mfa"></a>MFA に登録されていないユーザーのための準備

MFA に登録されていないユーザーがいる場合は、そのユーザーが認証しようとしたときの動作を決める必要があります。 レジストリ パス *HKLM\Software\Microsoft\AzureMFA* にあるレジストリ設定 *REQUIRE_USER_MATCH* を使用して、この機能の動作を制御します。 この設定の構成オプションは 1 つだけです。

| キー | 値 | 既定値 |
| --- | ----- | ------- |
| REQUIRE_USER_MATCH | TRUE または FALSE | 未設定 (TRUE に相当) |

この設定により、MFA に登録されていないユーザーの扱いが決まります。 キーが存在しないか、設定されていないか、または TRUE に設定されていて、ユーザーが登録されていない場合は、拡張機能による MFA チャレンジが失敗します。 キーが FALSE に設定されていて、ユーザーが登録されていない場合は、MFA を実行することがなく認証が行われます。

ユーザーのオンボーディングの際に、このキーを作成して FALSE に設定することができますが、 そうすると、MFA に登録されていないユーザーのサインインが、チャレンジなしで許可されることになります。このキーは運用環境に移行する前に削除してください。

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="how-do-i-verify-that-the-client-cert-is-installed-as-expected"></a>クライアント証明書が正常にインストールされていることを確認するにはどうすればよいですか。

インストーラーによって証明書ストア内に作成された自己署名証明書を探して、ユーザー **NETWORK SERVICE** が秘密キーへのアクセスを許可されていることを確認します。 この証明書のサブジェクト名は **CN \<tenantid\>, OU = Microsoft NPS Extension** になります。

-------------------------------------------------------------

### <a name="how-can-i-verify-that-my-client-cert-is-associated-to-my-tenant-in-azure-active-directory"></a>クライアント証明書が Azure Active Directory のテナントに関連付けられていることを確認するにはどうすればよいですか。

PowerShell コマンド プロンプトを開き、次のコマンドを実行します。

```
> import-module MSOnline
> Connect-MsolService
> Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1 
```

このコマンドは、テナントと NPS 拡張機能のインスタンスを関連付けているすべての証明書を PowerShell セッションに出力します。 クライアント証明書を "Base-64 encoded X.509(.cer)" ファイルとして秘密キーなしでエクスポートし、PowerShell が出力したリストと比較します。

1 つ以上の証明書が返されている場合は、判読できる形式のタイムスタンプ Valid-From と Valid-Until を使用して、明らかに無関係な証明書を除外することができます。

-------------------------------------------------------------

### <a name="why-are-my-requests-failing-with-adal-token-error"></a>要求が ADAL トークン エラーで失敗するのはなぜですか。

このエラーにはいくつかの原因が考えられます。 次の手順でトラブルシューティングしてみてください。

1. NPS サーバーを再起動します。
2. クライアント証明書が正常にインストールされていることを確認します。
3. 証明書が Azure AD のテナントに関連付けられていることを確認します。
4. 拡張機能を実行しているサーバーから https://login.windows.net/ にアクセスできることを確認します。

-------------------------------------------------------------

### <a name="why-does-authentication-fail-with-an-error-in-http-logs-stating-that-the-user-is-not-found"></a>HTTP ログにユーザーが見つからないというエラーが記録され、認証が失敗するのはなぜですか。

AD Connect が実行していること、およびユーザーが Windows Active Directory と Azure Active Directory の両方に存在していることを確認します。

------------------------------------------------------------

### <a name="why-do-i-see-http-connect-errors-in-logs-with-all-my-authentications-failing"></a>すべての認証が失敗し、ログに HTTP 接続エラーが記録されるのはなぜですか。

NPS 拡張機能を実行しているサーバーから https://adnotifications.windowsazure.com に到達できることを確認します。

## <a name="next-steps"></a>次のステップ

Azure MFA を [Active Directory](multi-factor-authentication-get-started-server-dirint.md)、[RADIUS 認証](multi-factor-authentication-get-started-server-radius.md)、および [LDAP 認証](multi-factor-authentication-get-started-server-ldap.md) と統合する方法を参照してください。

