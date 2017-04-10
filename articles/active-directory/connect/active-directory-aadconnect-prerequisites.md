---
title: "Azure AD Connect: 前提条件とハードウェア |Microsoft Docs"
description: "このトピックでは、Azure AD Connect を使用するための前提条件とハードウェア要件について説明します。"
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: 91b88fda-bca6-49a8-898f-8d906a661f07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/17/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 9ee7213aa30a11b13d4aa091b403b8b27fb78197
ms.lasthandoff: 03/18/2017


---
# <a name="prerequisites-for-azure-ad-connect"></a>Azure AD Connect の前提条件
このトピックでは、Azure AD Connect を使用するための前提条件とハードウェア要件について説明します。

## <a name="before-you-install-azure-ad-connect"></a>Azure AD Connect をインストールする前に
Azure AD Connect をインストールする前に、いくつか必要な項目があります。

### <a name="azure-ad"></a>Azure AD
* Azure サブスクリプションまたは [Azure 試用版](https://azure.microsoft.com/pricing/free-trial/)サブスクリプション。 このサブスクリプションは、Azure Portal へのアクセスにのみ必要です。Azure AD Connect の使用には不要です。 PowerShell または Office 365 を使用している場合は、Azure サブスクリプションがなくても Azure AD Connect を使用できます。 Office 365 ライセンスを持っている場合は、Office 365 ポータルも使用できます。 有料の Office 365 ライセンスを使用して、Office 365 ポータルから Azure Portal にアクセスすることもできます。
  * また、[Azure Portal](https://portal.azure.com) で Azure AD プレビュー機能を使用することもできます。 このポータルでは、Azure ライセンスは必要ありません。
* [ドメインを追加して検証](../active-directory-add-domain.md) します。 たとえば、ユーザー向けに contoso.com を使用する予定の場合は、そのドメインが検証されていることと、使用しているドメインが既定のドメインである contoso.onmicrosoft.com だけではないことを確認します。
* Azure AD テナントでは、既定では 50,000 個のオブジェクトを使用できます。 ドメインを検証すると、制限が 300,000 個のオブジェクトに増加します。 Azure AD でさらに多くのオブジェクトが必要な場合は、制限を緩和するサポート ケースを開く必要があります。 500,000 個を超えるオブジェクトが必要な場合は、Office 365、Azure AD Basic、Azure AD Premium、Enterprise Mobility Suite などのライセンスが必要です。

### <a name="prepare-your-on-premises-data"></a>オンプレミスのデータの準備
* [Azure AD で有効にできるオプションの同期機能](active-directory-aadconnectsyncservice-features.md) について確認し、どの機能を有効にする必要があるかを検討してください。

### <a name="on-premises-active-directory"></a>オンプレミスの Active Directory
* AD スキーマのバージョンとフォレストの機能レベルは、Windows Server 2003 以降である必要があります。 ドメイン コントローラーは、スキーマとフォレスト レベルの要件を満たしていれば、任意のバージョンを実行できます。
* **パスワード ライトバック**機能を使用する場合、ドメイン コントローラーが (最新の SP が適用された) Windows Server 2008 以降にインストールされている必要があります。 ドメイン コントローラーが 2008 (R2 より前のバージョン) にインストールされている場合は、[修正プログラム KB2386717](http://support.microsoft.com/kb/2386717) も適用する必要があります。
* Azure AD で使用されるドメイン コントローラーは、書き込み可能である必要があります。 RODC (読み取り専用ドメイン コントローラー) は**使用できません**。Azure AD Connect では、書き込みのリダイレクトを行いません。
* SLD (シングル ラベル ドメイン) を使用するオンプレミスのフォレスト/ドメインは**使用できません**。
* "ドット形式" (名前にピリオド "." が含まれる) の NetBios 名を使用するオンプレミスのフォレスト/ドメインは**使用できません**。

### <a name="azure-ad-connect-server"></a>Azure AD Connect サーバー
* Small Business Server または Windows Server Essentials には、Azure AD Connect をインストールできません。 サーバーは Windows Server Standard 以上を使用する必要があります。
* Azure AD Connect サーバーには、完全な GUI がインストールされている必要があります。 サーバー コアにインストールすることは**できません**。
* Azure AD Connect は、Windows Server 2008 以降にインストールする必要があります。 このサーバーをドメイン コントローラーにすることができます。Express 設定を使用する場合はメンバー サーバーにすることもできます。 カスタム設定を使用する場合、サーバーはスタンドアロンにすることもでき、ドメインに参加する必要はありません。
* Azure AD Connect を Windows Server 2008 にインストールする場合は、Windows Update から最新の修正プログラムが適用されていることを確認してください。 修正プログラムが適用されていないサーバーでインストールを開始することはできません。
* **パスワード同期**機能を使用する場合、Azure AD Connect サーバーが Windows Server 2008 R2 SP1 以降にインストールされている必要があります。
* Azure AD Connect サーバーには、[.NET Framework 4.5.1](#component-prerequisites) 以降と [Microsoft PowerShell 3.0](#component-prerequisites) 以降がインストールされている必要があります。
* Active Directory Federation Services をデプロイする場合、AD FS または Web アプリケーション プロキシがインストールされるサーバーは、Windows Server 2012 R2 以降である必要があります。 [Windows リモート管理](#windows-remote-management) を有効にする必要があります。
* Active Directory フェデレーション サービスがデプロイされている場合は、 [SSL 証明書](#ssl-certificate-requirements)が必要です。
* Active Directory フェデレーション サービス (AD FS) がデプロイされている場合は、 [名前解決](#name-resolution-for-federation-servers)を構成する必要があります。
* 全体管理者が MFA を有効にしている場合は、URL **https://secure.aadcdn.microsoftonline-p.com** が信頼済みサイトの一覧に追加されている必要があります。 MFA チャレンジを求められたときに、この URL がまだ追加されていない場合は、信頼済みサイトの一覧に追加するように促されます。 信頼済みサイトへの追加には、Internet Explorer を使用できます。

### <a name="sql-server-used-by-azure-ad-connect"></a>Azure AD Connect で使用される SQL Server
* Azure AD Connect には、ID データを格納する SQL Server データベースが必要です。 既定では、SQL Server 2012 Express LocalDB (SQL Server Express の簡易バージョン) がインストールされ、サービスのサービス アカウントがローカル コンピューターに作成されます。 SQL Server Express のサイズ制限は 10 GB で、約 100,000 オブジェクトを管理できます。 さらに多くのディレクトリ オブジェクトを管理する必要がある場合は、インストール ウィザードで別の SQL Server インストール済み環境を指定する必要があります。
* 別の SQL Server を使用する場合は、次の要件が適用されます。
  * Azure AD Connect では、SQL Server 2008 (最新の Service Pack) から SQL Server 2016 まで、すべてのエディションの Microsoft SQL Server がサポートされています。 Microsoft Azure SQL Database は、データベースとして **サポートされていません** 。
  * 大文字と小文字が区別されない SQL 照合順序を使用する必要があります。 これらの照合順序は、名前に含まれる \_CI_ で識別します。 大文字と小文字が区別される照合順序 (名前に含まれる \_CS_ で識別) は**サポートされていません**。
  * 1 つの SQL インスタンスにつき保持できる同期エンジンは 1 つだけです。 FIM/MIM Sync、DirSync、または Azure AD Sync との SQL インスタンスの共有は**サポートされていません**。

### <a name="accounts"></a>アカウント
* 統合する Azure AD テナントの Azure AD 全体管理者アカウント。 このアカウントには**学校または組織のアカウント**を使用する必要があり、**Microsoft アカウント**を使用することはできません。
* 簡単設定を使用するか、DirSync からアップグレードする場合は、ローカルの Active Directory のエンタープライズ管理者アカウント。
* カスタム設定のインストール パスを使用する場合は、[Active Directory 内のアカウント](active-directory-aadconnect-accounts-permissions.md)。

### <a name="connectivity"></a>接続
* Azure AD Connect サーバーには、イントラネット用とインターネット用の両方の DNS 解決が必要です。 DNS サーバーは、オンプレミス Active Directory と Azure AD エンドポイントの両方の名前を解決できる必要があります。
* お使いのイントラネット環境でファイアウォールを使用していて、Azure AD Connect サーバーとドメイン コントローラーの間でポートを開く必要がある場合の詳細については、[Azure AD Connect のポート](active-directory-aadconnect-ports.md)に関する記事を参照してください。
* アクセスできる URL をプロキシまたはファイアウォールが制限している場合は、「[Office 365 URL および IP アドレス範囲](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)」に記載されている URL を開く必要があります。
  * ドイツで Microsoft Cloud を使用する場合、または Microsoft Azure Government クラウドを使用する場合は、 [Azure AD Connect 同期サービス インスタンスの考慮事項](active-directory-aadconnect-instances.md) に関するページで URL を確認してください。
* Azure AD Connect は、Azure AD との通信に既定で TLS 1.0 を使用します。 これを TLS 1.2 に変更するには、「 [Azure AD Connect 用に TLS 1.2 を有効にする](#enable-tls-12-for-azure-ad-connect)」の手順に従います。
* 送信プロキシを使用してインターネットに接続する場合は、次の設定を **C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config** ファイルに追加して、インストール ウィザードと Azure AD Connect 同期がインターネットと Azure AD に接続できるようにする必要があります。 このテキストは、ファイルの末尾に入力する必要があります。 このコードの &lt;PROXYADRESS&gt; は実際のプロキシ IP アドレスまたはホスト名を表します。

```
    <system.net>
        <defaultProxy>
            <proxy
            usesystemdefault="true"
            proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
            bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

* プロキシ サーバーで認証が必要な場合は、[サービス アカウント](active-directory-aadconnect-accounts-permissions.md#azure-ad-connect-sync-service-accounts)をドメイン内に配置する必要があり、カスタマイズした設定のインストール パスを使用して、[カスタム サービス アカウント](active-directory-aadconnect-get-started-custom.md#install-required-components)を指定する必要があります。 machine.config に別の変更も必要です。 この machine.config の変更によって、インストール ウィザードと同期エンジンは、プロキシ サーバーからの認証要求に応答します。 **[構成]** ページを除くインストール ウィザードのすべてのページで、サインインしたユーザーの資格情報を使用します。 インストール ウィザードの最後の **[構成]** ページで、コンテキストが、自分で作成した[サービス アカウント](active-directory-aadconnect-accounts-permissions.md#azure-ad-connect-sync-service-accounts)に切り替わります。 machine.config のセクションは、次のようになるはずです。

```
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy
            usesystemdefault="true"
            proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
            bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

詳細については、[既定のプロキシ要素](https://msdn.microsoft.com/library/kd3cf2ex.aspx)に関する MSDN を参照してください。  
接続に問題が発生した場合は、[接続の問題に対するトラブルシューティング](active-directory-aadconnect-troubleshoot-connectivity.md)についてのページを参照してください。

### <a name="other"></a>その他
* 省略可能: 同期を検証するテスト ユーザー アカウント。

## <a name="component-prerequisites"></a>コンポーネントの前提条件
### <a name="powershell-and-net-framework"></a>PowerShell と .NET Framework
Azure AD Connect は、Microsoft PowerShell と .NET 4.5.1 に依存しています。 これ以降のバージョンがサーバーにインストールされている必要があります。 Windows Server のバージョンに応じて、次の操作を行います。

* Windows Server 2012R2
  * Microsoft PowerShell は既定でインストールされています。 操作は必要ありません。
  * .NET Framework 4.5.1 以降のリリースは、Windows Update によって提供されます。 コントロール パネルで、Windows Server に最新の更新プログラムがインストールされていることを確認します。
* Windows Server 2008R2 と Windows Server 2012
  * Microsoft PowerShell の最新バージョンは、 **Microsoft ダウンロード センター**の [Windows Management Framework 4.0](http://www.microsoft.com/downloads)で入手できます。
  * .NET Framework 4.5.1 以降のリリースは、 [Microsoft ダウンロード センター](http://www.microsoft.com/downloads)で入手できます。
* Windows Server 2008
  * PowerShell の最新のサポート バージョンは、 **Microsoft ダウンロード センター**の [Windows Management Framework 3.0](http://www.microsoft.com/downloads)で入手できます。
  * .NET Framework 4.5.1 以降のリリースは、 [Microsoft ダウンロード センター](http://www.microsoft.com/downloads)で入手できます。

### <a name="enable-tls-12-for-azure-ad-connect"></a>Azure AD Connect 用に TLS 1.2 を有効にする
Azure AD Connect は、同期エンジン サーバーと Azure AD の間での通信を暗号化するために、既定で TLS 1.0 を使用します。 これを変更するには、サーバーで TLS 1.2 を既定で使用するように .NET アプリケーションを構成します。 TLS 1.2 の詳細については、「[Microsoft セキュリティ アドバイザリ 2960358](https://technet.microsoft.com/security/advisory/2960358)」を参照してください。

1. Windows Server 2008 で TLS 1.2 を有効にすることはできません。 有効にするには、Windows Server 2008 R2 以降が必要です。 オペレーティング システムに .NET 4.5.1 修正プログラムがインストールされていることを確認してください。詳細については、「[Microsoft セキュリティ アドバイザリ 2960358](https://technet.microsoft.com/security/advisory/2960358)」を参照してください。 既にこの修正プログラムやこれ以降のリリースをサーバーにインストールしている可能性があります。
2. Windows Server 2008 R2 を使用している場合は、TLS 1.2 が有効になっていることを確認してください。 Windows Server 2012 以降のバージョンのサーバーでは、TLS 1.2 が既に有効になっています。
   ```
   [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
   [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
   [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
   ```
3. すべてのオペレーティング システムに対して、次のレジストリ キーを設定してサーバーを再起動します。
   ```
   HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319
   "SchUseStrongCrypto"=dword:00000001
   ```
4. 同期エンジン サーバーとリモート SQL Server の間でも TLS 1.2 を有効にする場合は、 [Microsoft SQL Server 用の TLS 1.2 のサポート](https://support.microsoft.com/kb/3135244)に必要なバージョンがインストールされていることを確認してください。

## <a name="prerequisites-for-federation-installation-and-configuration"></a>フェデレーションのインストールと構成の前提条件
### <a name="windows-remote-management"></a>Windows リモート管理
Azure AD Connect を使用して Active Directory フェデレーション サービスまたは Web アプリケーション プロキシをデプロイする場合、以下の要件を確認します。

* 対象サーバーがドメインに参加している場合は、Windows リモート管理が有効であることを確認します。
  * 管理者特権の PSH コマンド ウィンドウで、 `Enable-PSRemoting –force`
* 対象サーバーが、ドメインに参加していない WAP コンピューターである場合は、いくつかの追加の要件があります。
  * ターゲット コンピューター (WAP コンピューター) での要件
    * サービス スナップインから winrm (Windows Remote Management / WS-Management) サービスが実行されていることを確認します。
    * 管理者特権の PSH コマンド ウィンドウで、 `Enable-PSRemoting –force`
      * ウィザードを実行しているコンピューターでの要件 (ターゲット コンピューターがドメインに参加していないか、信頼されていないドメインにある場合)
      * 管理者特権の PSH コマンド ウィンドウで、 `Set-Item WSMan:\localhost\Client\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate`
    * サーバー マネージャーでの要件
      * DMZ WAP ホストをコンピューターのプールに追加します ([サーバー マネージャー]、[管理]、[サーバーの追加] の順にクリックし、[DNS] タブを使用)。
      * サーバー マネージャーの [すべてのサーバー] タブで、 WAP サーバーを右クリックし、[管理に使用する資格情報] を選択し、WAP コンピューターのローカルの資格情報 (ドメインの資格情報ではない) を入力します。
      * リモートの PSH 接続を検証するには、サーバー マネージャーの [すべてのサーバー] タブで WAP サーバーを右クリックし、[Windows PowerShell] を選択します。 リモート PSH セッションが開き、リモート PowerShell セッションを確立できます。

### <a name="ssl-certificate-requirements"></a>SSL 証明書の要件
* AD FS ファームのすべてのノードとすべての Web アプリケーション プロキシ サーバーで同じ SSL 証明書を使用することを強くお勧めします。
* この証明書は x509 証明書である必要があります。
* テスト ラボ環境では、フェデレーション サーバーで自己署名証明書を使用できます。 ただし、運用環境では、パブリック CA から証明書を取得することを勧めします。
  * 公的に信頼されていない証明書を使用する場合は、各 Web アプリケーション プロキシ サーバーにインストールされている証明書がローカル サーバーとすべてのフェデレーション サーバーで信頼されていることを確認します。
* 証明書の ID は、フェデレーション サービス名 (sts.contoso.com など) と一致する必要があります。
  * ID は、dNSName タイプのサブジェクト代替名 (SAN) 拡張、または SAN エントリがない場合は共通名として指定されたサブジェクト名のどちらかになります。  
  * 複数の SAN エントリを証明書に表示できますが、そのうちの 1 つはフェデレーション サービス名に一致させます。
  * 社内参加を使用する場合は、値 **enterpriseregistration**  の後に組織のユーザー プリンシパル名 (UPN) サフィックス (**enterpriseregistration.contoso.com** など) が続く追加の SAN が必要です。
* CryptoAPI Next Generation (CNG) キーとキー記憶域プロバイダーに基づく証明書はサポートされません。 つまり、KSP (キー記憶域プロバイダー) ではなく CSP (暗号化サービス プロバイダー) に基づく証明書を使用する必要があります。
* ワイルドカード証明書がサポートされます。

### <a name="name-resolution-for-federation-servers"></a>フェデレーション サーバーの名前解決
* イントラネット (内部 DNS サーバー) とエクストラネット (ドメイン レジストラー経由のパブリック DNS) の両方の AD FS フェデレーション サービス名 (sts.contoso.com など) の DNS レコードを設定します。 イントラネットの DNS レコードの場合は、A レコードを使用し、CNAME レコードは使用しないようにします。 これは、windows 認証をドメイン参加しているマシンから正常に動作するために必要なことです。
* 複数の AD FS サーバーまたは Web アプリケーション プロキシ サーバーをデプロイする場合は、必ずロード バランサーを構成し、AD FS フェデレーション サービス名 (sts.contoso.com など) の DNS レコードでロード バランサーを指定してください。
* イントラネットで Internet Explorer を使用するブラウザー アプリケーションに対して動作する windows 統合認証の場合は、必ず AD FS フェデレーション サービス名 (sts.contoso.com など) を、IE のイントラネット ゾーンに追加してください。 これは、グループ ポリシーを使用して制御し、ドメインに参加しているすべてのコンピューターにデプロイすることができます。

## <a name="azure-ad-connect-supporting-components"></a>Azure AD Connect でサポートされるコンポーネント
Azure AD Connect によって Azure AD Connect のインストール先にインストールされるコンポーネントの一覧を次に示します。 この一覧は、基本的な高速インストール用です。 [同期サービスのインストール] ページで異なる SQL Server を使用することを選択した場合、SQL Express LocalDB はローカルにインストールされません。

* Azure AD Connect Health
* IT プロフェッショナル用 Microsoft Online Services サインイン アシスタント (インストール済みではあるものの、依存されてはいない)
* Microsoft SQL Server 2012 のコマンド ライン ユーティリティ
* Microsoft SQL Server 2012 Express LocalDB
* Microsoft SQL Server 2012 Native Client
* Microsoft Visual C++ 2013 再配布パッケージ

## <a name="hardware-requirements-for-azure-ad-connect"></a>Azure AD Connect のハードウェア要件
次の表は、Azure AD Connect Sync コンピューターの最小要件を示しています。

| Active Directory 内のオブジェクトの数 | CPU | メモリ | ハード ドライブのサイズ |
| --- | --- | --- | --- |
| 10,000 未満 |1.6 GHz |4 GB |70 GB |
| 10,000 ～ 50,000 |1.6 GHz |4 GB |70 GB |
| 50,000 ～ 100,000 |1.6 GHz |16 GB |100 GB |
| オブジェクトが 100,000 個以上の場合は完全バージョンの SQL Server が必要 | | | |
| 100,000 ～ 300,000 |1.6 GHz |32 GB |300 GB |
| 300,000 ～ 600,000 |1.6 GHz |32 GB |450 GB |
| 600,000 を超過 |1.6 GHz |32 GB |500 GB |

AD FS または Web アプリケーション サーバーを実行するコンピューターの最小要件を次に示します。

* CPU: デュアル コア 1.6 GHz 以上
* メモリ: 2 GB 以上
* Azure VM: A2 構成またはそれ以上

## <a name="next-steps"></a>次のステップ
「 [オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)」をご覧ください。

