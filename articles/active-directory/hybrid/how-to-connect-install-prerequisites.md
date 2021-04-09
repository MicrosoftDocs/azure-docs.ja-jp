---
title: Azure AD Connect:前提条件とハードウェア |Microsoft Docs
description: この記事では、Azure AD Connect を使用するための前提条件とハードウェア要件について説明します。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 91b88fda-bca6-49a8-898f-8d906a661f07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/16/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e758933b80efbf36dc263b7bd7d2d3c45a59a9f8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102426792"
---
# <a name="prerequisites-for-azure-ad-connect"></a>Azure AD Connect の前提条件
この記事では、Azure Active Directory (Azure AD) Connect を使用するための前提条件とハードウェア要件について説明します。

## <a name="before-you-install-azure-ad-connect"></a>Azure AD Connect をインストールする前に
Azure AD Connect をインストールする前に、いくつか必要な項目があります。

### <a name="azure-ad"></a>Azure AD
* Azure AD テナントが必要です。 [Azure 無料試用版](https://azure.microsoft.com/pricing/free-trial/)に 1 つ付属します。 次のポータルのいずれかを使用して、Azure AD Connect を管理できます。
  * [Azure ポータル](https://portal.azure.com)。
  * [Office ポータル](https://portal.office.com)。
* [ドメインを追加して検証](../fundamentals/add-custom-domain.md) します。 たとえば、ユーザー向けに contoso.com を使用する予定の場合は、そのドメインが検証されていることと、使用しているドメインが既定のドメインである contoso.onmicrosoft.com だけではないことを確認します。
* Azure AD テナントでは、既定で 50,000 個のオブジェクトを使用できます。 ドメインを検証すると、このオブジェクトの制限が 300,000 個に増加します。 Azure AD でさらに多くのオブジェクトが必要な場合は、制限をさらに増加させるためにサポート ケースを開きます。 500,000 個を超えるオブジェクトが必要な場合は、Microsoft 365、Azure AD Premium、または Enterprise Mobility + Security などのライセンスが必要です。

### <a name="prepare-your-on-premises-data"></a>オンプレミスのデータの準備
* Azure AD および Microsoft 365 に同期する前に、[IdFix](https://support.office.com/article/Install-and-run-the-Office-365-IdFix-tool-f4bd2439-3e41-4169-99f6-3fabdfa326ac) を使用して、ディレクトリ内の重複部分や書式の問題などのエラーを特定してください。
* [Azure AD で有効にできるオプションの同期機能](how-to-connect-syncservice-features.md)について確認し、どの機能を有効にする必要があるかを検討してください。

### <a name="on-premises-active-directory"></a>オンプレミスの Active Directory
* Active Directory スキーマのバージョンとフォレストの機能レベルは、Windows Server 2003 以降である必要があります。 ドメイン コントローラーは、スキーマのバージョンとフォレストレベルの要件が満たされていれば、任意のバージョンを実行できます。
* "*パスワード ライトバック*" 機能を使用する場合、ドメイン コントローラーは Windows Server 2012 以降にインストールされている必要があります。
* Azure AD で使用されるドメイン コントローラーは、書き込み可能である必要があります。 読み取り専用ドメイン コントローラー (RODC) の使用は *サポートされておらず*、Azure AD Connect は書き込みリダイレクトに従いません。
* "ドット形式" (名前にピリオド "." が含まれる) を使用した NetBIOS 名を使用するオンプレミスのフォレストまたはドメインは *使用できません*。
* [Active Directory のごみ箱を有効にする](how-to-connect-sync-recycle-bin.md)ことをお勧めします。

### <a name="powershell-execution-policy"></a>PowerShell 実行ポリシー
Azure Active Directory Connect では、インストールの一部として署名付きの PowerShell スクリプトが実行されます。 PowerShell 実行ポリシーでスクリプトの実行が許可されていることを確認します。

インストール中に推奨される実行ポリシーは "RemoteSigned" です。

PowerShell 実行ポリシーの設定の詳細については、「[Set-ExecutionPolicy](/powershell/module/microsoft.powershell.security/set-executionpolicy)」を参照してください。


### <a name="azure-ad-connect-server"></a>Azure AD Connect サーバー
Azure AD Connect サーバーには、重要な ID データが含まれています。 このサーバーへの管理アクセスが適切にセキュリティで保護されていることが重要です。 「[特権アクセスの保護](/windows-server/identity/securing-privileged-access/securing-privileged-access)」のガイドラインに従ってください。 

Azure AD Connect サーバーは、「[Active Directory 管理階層モデル](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)」の説明に従い、階層 0 のコンポーネントとして取り扱う必要があります 

Active Directory 環境のセキュリティ保護の詳細については、[Active Directory を保護するためのベスト プラクティス](/windows-server/identity/ad-ds/plan/security-best-practices/best-practices-for-securing-active-directory)に関する記事を参照してください。

#### <a name="installation-prerequisites"></a>設置の前提条件

- Azure AD Connect は、ドメインに参加している Windows Server 2012 以降にインストールする必要があります。 
- Small Business Server または 2019 より前の Windows Server Essentials には、Azure AD Connect をインストールできません (Windows Server Essentials 2019 はサポートされます)。 サーバーは Windows Server Standard 以上を使用する必要があります。 
- Azure AD Connect サーバーには、完全な GUI がインストールされている必要があります。 Windows Server Core への Azure AD Connect のインストールはサポートされていません。 
- Azure AD Connect ウィザードを使用して Active Directory フェデレーション サービス (AD FS) 構成を管理する場合、Azure AD Connect サーバーで PowerShell トランスクリプション グループ ポリシーを有効にすることはできません。 Azure AD Connect ウィザードを使用して同期構成を管理する場合は、PowerShell トランスクリプションを有効にすることができます。 
- AD FS が展開されている場合: 
    - AD FS または Web アプリケーション プロキシがインストールされるサーバーは、Windows Server 2012 R2 以降である必要があります。 リモート インストールを行うには、これらのサーバーで Windows リモート管理を有効にする必要があります。 
    - TLS/SSL 証明書を構成する必要があります。 詳細については、[AD FS の SSL/TLS プロトコルおよび暗号スイートの管理](/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs)および [AD FS での SSL 証明書の管理](/windows-server/identity/ad-fs/operations/manage-ssl-certificates-ad-fs-wap)に関する記事を参照してください。
    - 名前解決を構成する必要があります。 
- 全体管理者が MFA を有効にしている場合、URL https://secure.aadcdn.microsoftonline-p.com は信頼済みサイトの一覧に *なければなりません*。 MFA チャレンジを求められたときに、この URL がまだ追加されていない場合は、信頼済みサイトの一覧に追加するように促されます。 信頼済みサイトへの追加には、Internet Explorer を使用できます。
- 同期に Azure AD Connect Health を使用する予定の場合は、Azure AD Connect Health の前提条件も満たされていることを確認してください。 詳細については、「[Azure AD Connect Health エージェントのインストール](how-to-connect-health-agent-install.md)」を参照してください。

#### <a name="harden-your-azure-ad-connect-server"></a>Azure AD Connect サーバーを強化する 
Azure AD Connect サーバーを強化して、お客様の IT 環境に含まれるこの重要なコンポーネントに対する、セキュリティの攻撃面を縮小することをお勧めします。 これらの推奨事項に従うことで、組織に対するセキュリティ上のリスクを軽減することができます。

- Azure AD Connect は、ドメイン コントローラーやその他の階層 0 のリソースと同じように扱います。 詳細については、「[Active Directory 管理階層モデル](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)」を参照してください。
- Azure AD Connect サーバーへの管理アクセスを、ドメイン管理者またはその他の厳重に管理されたセキュリティ グループのみに制限します。
- [特権アクセスがあるすべてのユーザーに対して専用アカウント](/windows-server/identity/securing-privileged-access/securing-privileged-access)を作成します。 管理者は、高い特権を持つアカウントを使用して Web を閲覧したり、メールをチェックしたり、日常業務を実行すべきではありません。
- 「[特権アクセスの保護](/windows-server/identity/securing-privileged-access/securing-privileged-access)」に記載されているガイダンスに従ってください。 
- AADConnect サーバーでの NTLM 認証の使用を拒否します。 これを行うには、次の方法があります。[AADConnect サーバーで NTLM を制限](/windows/security/threat-protection/security-policy-settings/network-security-restrict-ntlm-outgoing-ntlm-traffic-to-remote-servers)し、[ドメインで NTLM を制限](/windows/security/threat-protection/security-policy-settings/network-security-restrict-ntlm-ntlm-authentication-in-this-domain)する
- すべてのマシンに一意のローカル管理者パスワードが構成されていることを確認します。 詳細については、[ローカル管理者パスワード ソリューション (LAPS)](https://support.microsoft.com/help/3062591/microsoft-security-advisory-local-administrator-password-solution-laps) に関する記事を参照してください。これを使用することで、ワークステーションおよびサーバーごとに一意のランダム パスワードを構成し、ACL によって保護された Active Directory に保存することができます。 資格のある許可されているユーザーのみが、これらのローカル管理者アカウントのパスワードの読み取りまたはリセットの要求を行うことができます。 [Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=46899)から、ワークステーションとサーバーで使用する LAPS を取得できます。 LAPS と 特権アクセス ワークステーション (PAW) を使用して環境を運用するための追加のガイダンスについては、「[クリーン ソースの原則に基づく運用基準](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material#operational-standards-based-on-clean-source-principle)」を参照してください。 
- 組織の情報システムへの特権アクセスを持つすべての担当者に対して、専用の[特権アクセス ワークステーション](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)を実装します。 
- Active Directory 環境の攻撃面を減らすには、これらの[追加のガイドライン](/windows-server/identity/ad-ds/plan/security-best-practices/reducing-the-active-directory-attack-surface)に従います。


### <a name="sql-server-used-by-azure-ad-connect"></a>Azure AD Connect で使用される SQL Server
* Azure AD Connect には、ID データを格納する SQL Server データベースが必要です。 既定では、SQL Server 2012 Express LocalDB (SQL Server Express の簡易バージョン) がインストールされています。 SQL Server Express のサイズ制限は 10 GB で、約 100,000 オブジェクトを管理できます。 さらに多くのディレクトリ オブジェクトを管理する必要がある場合は、インストール ウィザードで別の SQL Server インストール済み環境を指定します。 SQL Server のインストールの種類により、[Azure AD Connect のパフォーマンス](./plan-connect-performance-factors.md#sql-database-factors)に影響することがあります。
* SQL Server の別のインストールを使用する場合は、次の要件が適用されます。
  * Azure AD Connect では、SQL Server 2012 (最新の Service Pack 付き) から SQL Server 2019 までのすべてのバージョンがサポートされています。 Azure SQL Database は、データベースとしては *サポートされていません*。
  * 大文字と小文字が区別されない SQL 照合順序を使用する必要があります。 これらの照合順序は、名前に含まれる \_CI_ で識別します。 大文字と小文字が区別される照合順序 (名前に含まれる \_CS_ で識別) は *サポートされていません*。
  * 1 つの SQL インスタンスにつき保持できる同期エンジンは 1 つだけです。 FIM/MIM Sync、DirSync、または Azure AD Sync との SQL インスタンスの共有は *サポートされていません*。

### <a name="accounts"></a>アカウント
* 統合する Azure AD テナントの Azure AD 全体管理者アカウントが必要です。 このアカウントは *学校または組織のアカウント* である必要があり、*Microsoft アカウント* を使用することはできません。
* [簡単設定](reference-connect-accounts-permissions.md#express-settings-installation)を使用するか、DirSync からアップグレードする場合は、オンプレミスの Active Directory のエンタープライズ管理者アカウントが必要です。
* カスタム設定のインストール パスを使用する場合、さらにオプションがあります。 詳細については、「[カスタム インストールの設定](reference-connect-accounts-permissions.md#custom-installation-settings)」を参照してください。

### <a name="connectivity"></a>接続
* Azure AD Connect サーバーには、イントラネット用とインターネット用の両方の DNS 解決が必要です。 DNS サーバーは、オンプレミス Active Directory と Azure AD エンドポイントの両方の名前を解決できる必要があります。
* Azure AD Connect には、構成されているすべてのドメインへのネットワーク接続が必要です
* お使いのイントラネット環境でファイアウォールを使用していて、Azure AD Connect サーバーとドメイン コントローラーの間でポートを開く必要がある場合の詳細については、[Azure AD Connect のポート](reference-connect-ports.md)に関する記事を参照してください。
* プロキシまたはファイアウォールによってアクセスできる URL が制限されている場合は、「[Office 365 URL および IP アドレス範囲](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)」に記載されている URL を開く必要があります。 「[ファイアウォールまたはプロキシ サーバーのセーフリストに Azure portal の URL を追加する](../../azure-portal/azure-portal-safelist-urls.md?tabs=public-cloud)」も参照してください。
  * ドイツで Microsoft Cloud を使用する場合、または Microsoft Azure Government クラウドを使用する場合は、[Azure AD Connect 同期サービス インスタンスの考慮事項](reference-connect-instances.md)に関するページで URL を確認してください。
* Azure AD Connect (バージョン 1.1.614.0 以降) では、同期エンジンと Azure AD との間の通信の暗号化に既定で TLS 1.2 が使用されます。 基盤となるオペレーティング システムで TLS 1.2 が使用できない場合は、1 つ前のプロトコル (TLS 1.1 と TLS 1.0) に段階的にフォールバックされます。
* バージョン 1.1.614.0 未満の Azure AD Connect では、同期エンジンと Azure AD との間の通信の暗号化に既定で TLS 1.0 が使用されます。 TLS 1.2 に変更するには、「[Azure AD Connect 用に TLS 1.2 を有効にする](#enable-tls-12-for-azure-ad-connect)」の手順に従います。
* 送信プロキシを使用してインターネットに接続する場合は、**C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config** ファイルに次の設定を追加して、インストール ウィザードと Azure AD Connect 同期がインターネットと Azure AD に接続できるようにする必要があります。 このテキストは、ファイルの末尾に入力する必要があります。 このコードの *&lt;PROXYADDRESS&gt;* は実際のプロキシ IP アドレスまたはホスト名を表します。

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

* プロキシ サーバーで認証が必要な場合、[サービス アカウント](reference-connect-accounts-permissions.md#adsync-service-account)はドメイン内にある必要があります。 カスタマイズした設定のインストール パスを使用して、[カスタム サービス アカウント](how-to-connect-install-custom.md#install-required-components)を指定します。 machine.config に別の変更も必要です。この machine.config の変更によって、インストール ウィザードと同期エンジンは、プロキシ サーバーからの認証要求に応答します。 **[構成]** ページを除くインストール ウィザードのすべてのページで、サインインしたユーザーの資格情報が使用されます。 インストール ウィザードの最後にある **[構成]** ページで、コンテキストが、自分で作成した [サービス アカウント](reference-connect-accounts-permissions.md#adsync-service-account)に切り替わります。 machine.config のセクションは、次のようになっているはずです。

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

* プロキシ構成が既存のセットアップで行われている場合、Azure AD Connect にプロキシ設定を読み込んで動作を更新するには **Microsoft Azure AD Sync サービス** を 1 回再起動する必要があります。 
* Azure AD Connect がディレクトリ同期の過程で Web 要求を Azure AD に送信するとき、Azure AD から応答が返されるまでに長くて 5 分程度かかる場合があります。 一般的に、プロキシ サーバーには接続アイドル タイムアウトの構成を適用します。 この構成は最低でも 6 分以上に設定します。

詳細については、[既定のプロキシ要素](/dotnet/framework/configure-apps/file-schema/network/defaultproxy-element-network-settings)に関する MSDN を参照してください。
接続に問題が発生した場合は、[接続の問題に対するトラブルシューティング](tshoot-connect-connectivity.md)についてのページを参照してください。

### <a name="other"></a>その他
省略可能:テスト ユーザー アカウントを使用して同期を検証します。

## <a name="component-prerequisites"></a>コンポーネントの前提条件
### <a name="powershell-and-net-framework"></a>PowerShell と .NET Framework
Azure AD Connect は、Microsoft PowerShell と .NET 4.5.1 に依存しています。 これ以降のバージョンがサーバーにインストールされている必要があります。 Windows Server のバージョンに応じて、次の操作を実行します。

* Windows Server 2012 R2
  * Microsoft PowerShell は既定でインストールされています。 必要な操作はありません。
  * .NET Framework 4.5.1 以降のリリースは、Windows Update によって提供されます。 コントロール パネルで、Windows Server に最新の更新プログラムがインストールされていることを確認します。
* Windows Server 2012
  * Microsoft PowerShell の最新バージョンは、[Microsoft ダウンロード センター](https://www.microsoft.com/downloads)の Windows Management Framework 4.0 で入手できます。
  * .NET Framework 4.5.1 以降のリリースは、[Microsoft ダウンロード センター](https://www.microsoft.com/downloads)で入手できます。


### <a name="enable-tls-12-for-azure-ad-connect"></a>Azure AD Connect 用に TLS 1.2 を有効にする
バージョン 1.1.614.0 未満の Azure AD Connect では、同期エンジン サーバーと Azure AD との間の通信の暗号化に既定で TLS 1.0 が使用されます。 サーバーで TLS 1.2 を既定で使用するように .NET アプリケーションを構成できます。 TLS 1.2 について詳しくは、[Microsoft セキュリティ アドバイザリ 2960358](/security-updates/SecurityAdvisories/2015/2960358) に関するページを参照してください。

1. オペレーティング システムに .NET 4.5.1 修正プログラムがインストールされていることを確認します。 詳しくは、[Microsoft セキュリティ アドバイザリ 2960358](/security-updates/SecurityAdvisories/2015/2960358) に関する記事を参照してください。 既にこの修正プログラムやこれ以降のリリースをサーバーにインストールしている可能性があります。

1. すべてのオペレーティング システムに対して、次のレジストリ キーを設定してサーバーを再起動します。
    ```
    HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319
    "SchUseStrongCrypto"=dword:00000001
    ```
1. 同期エンジン サーバーとリモート SQL Server の間でも TLS 1.2 を有効にする場合は、[Microsoft SQL Server 用の TLS 1.2 のサポート](https://support.microsoft.com/kb/3135244)に必要なバージョンがインストールされていることを確認してください。

### <a name="dcom-prerequisites-on-the-synchronization-server"></a>同期サーバーでの DCOM の前提条件
同期サービスのインストール中に、Azure AD Connect によって次のレジストリ キーが存在するかどうかが確認されます。

- HKEY_LOCAL_MACHINE:  Software\Microsoft\Ole

このレジストリ キーの下で、Azure AD Connect によって、次の値が存在し、破損していないかどうかが確認されます。 

- [MachineAccessRestriction](/windows/win32/com/machineaccessrestriction)
- [MachineLaunchRestriction](/windows/win32/com/machinelaunchrestriction)
- [DefaultLaunchPermission](/windows/win32/com/defaultlaunchpermission)

## <a name="prerequisites-for-federation-installation-and-configuration"></a>フェデレーションのインストールと構成の前提条件
### <a name="windows-remote-management"></a>Windows リモート管理
Azure AD Connect を使用して AD FS または Web アプリケーション プロキシ (WAP) を展開する場合は、次の要件を確認してください。

* ターゲット サーバーがドメインに参加している場合は、Windows リモート管理が有効であることを確認します。
  * 管理者特権の PowerShell コマンド ウィンドウで、コマンド `Enable-PSRemoting –force` を使用します。
* ターゲット サーバーが、ドメインに参加していない WAP マシンである場合は、次のいくつかの追加の要件があります。
  * ターゲット コンピューター (WAP コンピューター) での要件
    * Windows Remote Management/WS-Management (WinRM) サービスが、サービス スナップインから実行されていることを確認します。
    * 管理者特権の PowerShell コマンド ウィンドウで、コマンド `Enable-PSRemoting –force` を使用します。
  * ウィザードが実行されているマシン (ターゲット マシンがドメインに参加していないか、信頼されていないドメインである場合) の場合:
    * 管理者特権の PowerShell コマンド ウィンドウで、コマンド `Set-Item.WSMan:\localhost\Client\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate` を使用します。
    * サーバー マネージャーで、以下を行います。
      * DMZ WAP ホストをコンピューター プールに追加します。 サーバー マネージャーで、 **[管理]**  >  **[サーバーの追加]** を選択し、 **[DNS]** タブを使用します。
      * **サーバー マネージャーの [すべてのサーバー]** タブで、WAP サーバーを右クリックし、 **[Manage As]\(管理に使用する資格情報\)** を選択します。 WAP マシンのローカル (ドメインではない) 資格情報を入力します。
      * リモートの PowerShell 接続を検証するには、**サーバー マネージャーの [すべてのサーバー]** タブで WAP サーバーを右クリックし、 **[Windows PowerShell]** を選択します。 リモート PowerShell セッションが開き、リモート PowerShell セッションを確立できるようになります。

### <a name="tlsssl-certificate-requirements"></a>TLS/SSL 証明書の要件
* AD FS ファームのすべてのノードとすべての Web アプリケーション プロキシ サーバーで同じ TLS/SSL 証明書を使用することをお勧めします。
* この証明書は x509 証明書である必要があります。
* テスト ラボ環境では、フェデレーション サーバーで自己署名証明書を使用できます。 運用環境では、公的証明機関から証明書を取得することを勧めします。
  * 公的に信頼されていない証明書を使用する場合は、各 Web アプリケーション プロキシ サーバーにインストールされている証明書がローカル サーバーとすべてのフェデレーション サーバーで信頼されていることを確認します。
* 証明書の ID は、フェデレーション サービス名 (sts.contoso.com など) と一致する必要があります。
  * この ID は、dNSName タイプのサブジェクト代替名 (SAN) 拡張、または SAN エントリがない場合は共通名として指定されたサブジェクト名のどちらかになります。
  * 複数の SAN エントリを証明書に表示できますが、そのうちの 1 つはフェデレーション サービス名に一致させます。
  * Workplace Join を使用する場合は、値 **enterpriseregistration.** の後に組織のユーザー プリンシパル名 (UPN) サフィックスが続く追加の SAN が必要です (例: enterpriseregistration.contoso.com)。
* CryptoAPI Next Generation (CNG) キーとキー記憶域プロバイダー (KSP) に基づく証明書はサポートされません。 そのため、ユーザーは KSP ではなく、暗号化サービス プロバイダー (CSP) に基づく証明書を使用する必要があります。
* ワイルドカード証明書がサポートされます。

### <a name="name-resolution-for-federation-servers"></a>フェデレーション サーバーの名前解決
* イントラネット (内部 DNS サーバー) とエクストラネット (ドメイン レジストラー経由のパブリック DNS) の両方の AD FS 名 (sts.contoso.com など) の DNS レコードを設定します。 イントラネットの DNS レコードの場合は、A レコードを使用し、CNAME レコードは使用しないようにします。 A レコードの使用は、Windows 認証をドメインに参加しているマシンから正常に動作するために必要なことです。
* 複数の AD FS サーバーまたは Web アプリケーション プロキシ サーバーを展開する場合は、必ずロード バランサーを構成し、AD FS 名 (sts.contoso.com など) の DNS レコードでロード バランサーを指定してください。
* イントラネットで Internet Explorer を使用するブラウザー アプリケーションに対して動作する Windows 統合認証の場合は、必ず AD FS 名 (sts.contoso.com など) を、Internet Explorer のイントラネット ゾーンに追加してください。 この要件は、グループ ポリシーを使用して制御し、ドメインに参加しているすべてのマシンにデプロイすることができます。

## <a name="azure-ad-connect-supporting-components"></a>Azure AD Connect でサポートされるコンポーネント
Azure AD Connect により、Azure AD Connect がインストールされているサーバーに次のコンポーネントがインストールされます。 この一覧は、基本的な高速インストール用です。 **[同期サービスのインストール]** ページで異なる SQL Server を使用することを選択した場合、SQL Express LocalDB はローカルにインストールされません。

* Azure AD Connect Health
* Microsoft SQL Server 2012 のコマンド ライン ユーティリティ
* Microsoft SQL Server 2012 Express LocalDB
* Microsoft SQL Server 2012 Native Client
* Microsoft Visual C++ 2013 再配布パッケージ

## <a name="hardware-requirements-for-azure-ad-connect"></a>Azure AD Connect のハードウェア要件
次の表は、Azure AD Connect Sync マシンの最小要件を示しています。

| Active Directory 内のオブジェクトの数 | CPU | メモリ | ハード ドライブのサイズ |
| --- | --- | --- | --- |
| 10,000 未満 |1.6 GHz |4 GB |70 GB |
| 10,000 ～ 50,000 |1.6 GHz |4 GB |70 GB |
| 50,000 ～ 100,000 |1.6 GHz |16 GB |100 GB |
| オブジェクトが 100,000 個以上の場合は完全バージョンの SQL Server が必要 | | | |
| 100,000 ～ 300,000 |1.6 GHz |32 GB |300 GB |
| 300,000 ～ 600,000 |1.6 GHz |32 GB |450 GB |
| 600,000 を超過 |1.6 GHz |32 GB |500 GB |

AD FS または Web アプリケーション プロキシ サーバーを実行するマシンの最小要件を次に示します。

* CPU: デュアル コア 1.6 GHz 以上
* メモリ:2 GB 以上
* Azure VM:A2 構成またはそれ以上

## <a name="next-steps"></a>次のステップ
「 [オンプレミス ID と Azure Active Directory の統合](whatis-hybrid-identity.md)」をご覧ください。
