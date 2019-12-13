---
title: Azure AD パスワード保護をデプロイする - Azure Active Directory
description: Azure AD パスワード保護をデプロイして、オンプレミスでの間違ったパスワードの使用を禁止します
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: f98373fe8eab07519e665ab1eddfd7a9ce6b7e22
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74847868"
---
# <a name="deploy-azure-ad-password-protection"></a>Azure AD のパスワード保護をデプロイする

[Windows Server Active Directory に対して Azure AD パスワード保護を適用する方法](concept-password-ban-bad-on-premises.md)が理解できたので、次の手順は、そのデプロイを計画して実行することです。

## <a name="deployment-strategy"></a>展開戦略

監査モードでデプロイを開始することをお勧めします。 監査モードは既定の初期設定であり、パスワードは後から設定できます。 ブロックされるパスワードはイベント ログに記録されます。 プロキシ サーバーと DC エージェントを監査モードでデプロイした後で、パスワード ポリシーが適用されるときに、そのポリシーがユーザーと環境に与える影響を監視する必要があります。

この監査段階中に、多くの組織は、以下に気付きます。

* より安全なパスワードを使用するように既存の運用プロセスを改善する必要がある。
* 多くの場合、ユーザーは安全でないパスワードを使用している。
* セキュリティの適用の今後の変更、生じる可能性がある影響、およびより安全なパスワードを選択する方法をユーザーに通知する必要がある。

より強力なパスワード検証が、既存の Active Directory ドメイン コントローラーの展開の自動化に影響を与える可能性もあります。 このような問題を事前に発見できるように、監査期間の評価中に、少なくとも 1 つの DC 昇格と 1 つの DC 降格が行われるようにすることをお勧めします。  詳細については、次を参照してください。

* [Ntdsutil.exe で、ディレクトリ サービスの修復モードの弱いパスワードを設定できない](howto-password-ban-bad-on-premises-troubleshoot.md##ntdsutilexe-fails-to-set-a-weak-dsrm-password)
* [ディレクトリ サービスの修復モードの弱いパスワードが原因でドメイン コントローラー レプリカの昇格が失敗する](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-replica-promotion-fails-because-of-a-weak-dsrm-password)
* [弱いローカル管理者パスワードのためにドメイン コントローラーの降格が失敗する](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-demotion-fails-due-to-a-weak-local-administrator-password)

妥当な期間にわたってこの機能を監査モードで実行した後、構成を "*監査*" から "*適用*" に切り替えて、さらに安全なパスワードを要求できます。 この期間中に、集中的な監視を行うことをお勧めします。

## <a name="deployment-requirements"></a>デプロイ要件

* Azure AD パスワード保護でのライセンス要件については、[組織内の不適切なパスワードの排除](concept-password-ban-bad.md#license-requirements)に関する記事を参照してください。
* Azure AD パスワード保護 DC エージェント ソフトウェアがインストールされるマシンではすべて、Windows Server 2012 以降を実行する必要があります。 この要件は、Active Directory ドメインまたはフォレストも Windows Server 2012 ドメインまたはフォレストの機能レベルにする必要があることを意味するものではありません。 「[設計原則](concept-password-ban-bad-on-premises.md#design-principles)」で説明されているように、DC エージェントまたはプロキシ ソフトウェアが実行するために必要な最低限の DFL または FFL はありません。
* DC エージェント サービスがインストールされるすべてのマシンには、.NET 4.5 をインストールする必要があります。
* Azure AD パスワード保護プロキシ サービスがインストールされるマシンではすべて、Windows Server 2012 R2 以降を実行する必要があります。
   > [!NOTE]
   > ドメイン コントローラーにインターネットへの直接の送信接続があった場合でも、Azure AD パスワード保護のデプロイには、プロキシ サービスのデプロイが必須要件です。 
   >
* Azure AD パスワード保護プロキシ サービスがインストールされるすべてのマシンには、.NET 4.7 をインストールしておく必要があります。
  .NET 4.7 は、完全に更新された Windows Server には既にインストールされています。 必要な場合は、「[Windows 用の.NET Framework の 4.7 オフライン インストーラー](https://support.microsoft.com/help/3186497/the-net-framework-4-7-offline-installer-for-windows)」にあるインストーラーをダウンロードして実行してください。
* ドメイン コントローラーを含め、Azure AD パスワード保護コンポーネントがインストールされているすべてのマシンに、ユニバーサル C ランタイムがインストールされている必要があります。 Windows Update からすべての更新プログラムを確実に取得することでランタイムを入手できます。 または、OS 固有の更新プログラム パッケージで入手できます。 詳しくは、「[Update for Universal C Runtime in Windows (Windows のユニバーサル C ランタイムの更新プログラム)](https://support.microsoft.com/help/2999226/update-for-uniersal-c-runtime-in-windows)」をご覧ください。
* 各ドメイン内の少なくとも 1 つのドメイン コントローラーと、パスワード保護用のプロキシ サービスをホストする少なくとも 1 つのサーバーとの間に、ネットワーク接続が存在する必要があります。 この接続では、ドメイン コントローラーがプロキシ サービス上の RPC エンドポイント マッパー ポート 135 および RPC サーバー ポートにアクセスできるようにする必要があります。 RPC サーバー ポートは、既定では動的 RPC ポートですが、[静的ポートを使用](#static)するように構成することができます。
* Azure AD のパスワード保護プロキシ サービスがインストールされるすべてのマシンに、次のエンドポイントへのネットワーク アクセスが必要です。

    |**エンドポイント**|**目的**|
    | --- | --- |
    |`https://login.microsoftonline.com`|認証要求|
    |`https://enterpriseregistration.windows.net`|Azure AD パスワード保護機能|
 
* Microsoft Azure AD Connect エージェント アップデーターの前提条件

  Microsoft Azure AD Connect エージェント アップデーター サービスは、Azure AD パスワード保護プロキシ サービスとサイド バイ サイドでインストールされます。 Microsoft Azure AD Connect エージェント アップデーター サービスを機能させるには、追加の構成が必要です。

  お使いの環境で http プロキシ サーバーを使用している場合は、「[既存のオンプレミス プロキシ サーバーと連携する](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-connectors-with-proxy-servers)」で指定されているガイドラインに従う必要があります。

  Microsoft Azure AD Connect Agent Updater サービスでは、[TLS 要件](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#tls-requirements)で指定されている TLS 1.2 の手順も必要です。

  「[アプリケーション プロキシ環境の設定手順](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#prepare-your-on-premises-environment)」で指定されている一連のポートと URL に対し、ネットワーク アクセスを有効にする必要があります。

  > [!WARNING]
  > Azure AD パスワード保護プロキシとアプリケーション プロキシでは、異なるバージョンの Microsoft Azure AD Connect エージェント アップデーター サービスがインストールされ、そのため説明ではアプリケーション プロキシのコンテンツが参照されています。 これらの異なるバージョンは、サイド バイ サイドでインストールすると互換性がないため、Azure AD パスワード保護プロキシとアプリケーション プロキシを同じコンピューターにサイド バイ サイドでインストールすることはお勧めしません。

* パスワード保護用のプロキシ サービスがホストされているすべてのコンピューターを、プロキシ サービスにログオンする機能をドメイン コントローラーに許可するように、構成する必要があります。 この機能は、"ネットワーク経由でコンピューターへアクセス" 特権の割り当てによって制御されます。
* パスワード保護用プロキシ サービスがホストされているすべてのマシンで、送信 TLS 1.2 HTTP トラフィックを許可するように構成する必要があります。
* Azure AD でパスワード保護用プロキシ サービスとフォレストを登録するグローバル管理者アカウント。
* Azure AD で Windows Server Active Directory フォレストを登録するために、フォレスト ルート ドメインの Active Directory ドメイン管理者特権を持つアカウント。
* DC エージェント サービス ソフトウェアを実行しているすべての Active Directory ドメインは、sysvol レプリケーションに分散ファイル システム レプリケーション (DFSR) を使用する必要があります。

  ご利用のドメインでまだ DFSR を使用していない場合、Azure AD パスワード保護をインストールする前に DFSR 使用にドメインを移行する必要があります。 詳細については、次のリンクを参照してください。

  [SYSVOL レプリケーション移行ガイド: FRS レプリケーションから DFS レプリケーションに移行する](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd640019(v=ws.10))

  > [!WARNING]
  > Azure AD パスワード保護 DC エージェント ソフトウェアは現在のところ、sysvol レプリケーションのために依然として FRS (DFSR の前身テクノロジ) を使用しているドメインにあるドメイン コントローラーにインストールされますが、この環境ではソフトウェアは正しく機能しません。 その他のマイナスの副作用としては、個々のファイルを複製できない、sysvol 復元処理が成功したように見えたが、一部のファイルの複製に失敗しており、何のエラーも表示されない、などがあります。 できるだけ早く、DFSR の使用にドメインを移行してください。DFSR に固有のメリットがあるだけでなく、Azure AD パスワード保護のデプロイのブロックを解除します。 今後のバージョンでは、ドメインで依然として FRS を使用している場合、このソフトウェアは自動的に無効になります。

* Windows Server 2012 を実行するドメイン内のすべてのドメイン コントローラーで、キー配布サービスを有効にする必要があります。 既定では、このサービスは手動トリガーで開始して有効化されます。

## <a name="single-forest-deployment"></a>単一フォレスト デプロイ

次の図は、Azure AD パスワード保護の基本コンポーネントが、オンプレミスの Active Directory 環境でどのように連携し、機能するかを示したものです。

![Azure AD パスワード保護コンポーネントの連携方法](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

ソフトウェアをデプロイする前にしくみを確認することをお勧めします。 [Azure AD パスワード保護の概念の概要](concept-password-ban-bad-on-premises.md)に関するページをご覧ください。

### <a name="download-the-software"></a>ソフトウェアをダウンロードする

Azure AD パスワード保護には 2 つのインストーラーが必要です。 [Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=57071)で入手できます。

### <a name="install-and-configure-the-proxy-service-for-password-protection"></a>パスワード保護用のプロキシ サービスをインストールして構成する

1. パスワード保護用プロキシ サービスをホストする 1 つまたは複数のサーバーを選択します。
   * これら各サービスは、単一フォレストのパスワード ポリシーのみを提供できます。 ホスト コンピューターは、そのフォレスト内のドメインに参加する必要があります。 ルート ドメインと子ドメインの両方がサポートされます。 フォレストの各ドメインの少なくとも 1 つの DC とパスワード保護マシンの間にネットワーク接続が必要です。
   * テストのためにドメイン コント ローラー上でプロキシ サービスを実行できます。 ただし、その後でそのドメイン コントローラーがインターネット接続を求め、セキュリティの問題になる可能性があります。 この構成はテスト専用としてお勧めします。
   * 冗長性のために少なくとも 2 つのプロキシ サーバーをお勧めします。 「[高可用性](howto-password-ban-bad-on-premises-deploy.md#high-availability)」をご覧ください。

1. `AzureADPasswordProtectionProxySetup.exe` ソフトウェア インストーラーを使用して、Azure AD パスワード保護プロキシ サービスをインストールします。
   * このソフトウェアのインストールでは、再起動は必要ありません。 ソフトウェアのインストールは、標準 MSI プロシージャを使用して自動化できます。次に例を示します。

      `AzureADPasswordProtectionProxySetup.exe /quiet`

      > [!NOTE]
      > インストール エラーを回避するため、AzureADPasswordProtectionProxySetup.msi パッケージをインストールする前に Windows Firewall サービスを実行しておく必要があります。 Windows Firewall が実行されないように構成されている場合は、回避策として、インストールの際に一時的に Windows Firewall サービスを有効化して実行します。 インストール後はプロキシ ソフトウェアが Windows Firewall に特に依存することはありません。 サードパーティ製のファイアウォールを使用している場合も、デプロイ要件を満たすように構成する必要があります。 これらには、ポート 135 およびプロキシ RPC サーバー ポートへの着信アクセスの許可が含まれます。 「[デプロイ要件](howto-password-ban-bad-on-premises-deploy.md#deployment-requirements)」を確認してください。

1. 管理者として PowerShell ウィンドウを開きます。
   * パスワード保護プロキシ ソフトウェアには、新しい PowerShell モジュール *AzureADPasswordProtection* が含まれています。 この後の手順では、この PowerShell モジュールからさまざまなコマンドレットを実行します。 この新しいモジュールを次のようにインポートします。

      ```powershell
      Import-Module AzureADPasswordProtection
      ```

   * 次の PowerShell コマンドを使用して、サービスが実行されていることを確認します。

      `Get-Service AzureADPasswordProtectionProxy | fl`

     結果の **Status** が "Running" と表示される必要があります。

1. プロキシを登録します。
   * 手順 3 が完了すると、プロキシ サービスはマシンで実行しています。 ただし、サービスには Azure AD との通信に必要な資格情報がまだありません。 Azure AD への登録が必要です。

     `Register-AzureADPasswordProtectionProxy`

     このコマンドレットでは、Azure テナントのグローバル管理者の資格情報が必要です。 また、フォレスト ルート ドメイン内のオンプレミス Active Directory ドメイン管理者特権も必要です。 このコマンドが 1 つのプロキシ サービスで 1 回成功すると、次回以降の呼び出しも成功しますが、これ以上の呼び出しは必要ありません。

      `Register-AzureADPasswordProtectionProxy` コマンドレットでは、以下の 3 つの認証モードがサポートされます。 Azure Multi-Factor Authentication は、最初の 2 つのモードではサポートされますが、3 つ目のモードではサポートされません。 詳細については、以下のコメントを参照してください。

     * 対話型認証モード:

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > このモードは、Server Core オペレーティング システムでは機能しません。 代わりに以下の認証モードのいずれかを使用できます。 また、Internet Explorer の強化されたセキュリティ構成が有効になっている場合、このモードは失敗する可能性があります。 回避策としては、その構成を無効にし、プロキシを登録してから、もう一度構成を有効にします。

     * デバイスコード認証モード:

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XYZABC123 to authenticate.
        ```

        この後、別のデバイスに表示された手順に従って認証を完了します。

     * サイレント (パスワードベース) 認証モード:

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionProxy -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > このモードは、お使いのアカウントで Azure Multi-Factor Authentication が必要な場合は失敗します。 その場合は、前の 2 つの認証モードのいずれかを使用するか、MFA を必要としない別のアカウントを使用してください。
        >
        > グローバルに MFA を要求するように Azureデバイス登録 (Azure AD のパスワード保護によってバックグラウンドで使用されます) が構成されている場合も、MFA が必要であることが表示されます。 これを回避するには、MFA をサポートしている別のアカウントで前の 2 つの認証モードのいずれかを使用します。または、Azure デバイス登録の MFA 要件を一時的に緩めることもできます。 これを行うには、Azure 管理ポータルに移動し、[Azure Active Directory]、[デバイス]、[デバイスの設定] の順に選択した後、[デバイスを参加させるには多要素認証が必要] を [いいえ] に設定します。 登録が完了したら、この設定を必ず [はい] に再構成してください。
        >
        > MFA の要件のバイパスは、テスト目的でのみ使用することをお勧めします。

       現時点では、今後提供される機能用に予約された *-ForestCredential* パラメーターを指定する必要はありません。

   パスワード保護用のプロキシ サービスの登録は、サービスの有効期間内に 1 回だけ行う必要があります。 その後、他の必要なメンテナンスは、プロキシ サービスによって自動的に実行されます。

   > [!TIP]
   > このコマンドレットを特定の Azure テナントに対して最初に実行するときは、完了するまでにかなり時間がかかることがあります。 エラーが報告されない限り、この遅延については心配しないでください。

1. フォレストを登録します。
   * `Register-AzureADPasswordProtectionForest` PowerShell コマンドレットを使用して、Azure と通信するために必要な資格情報で、オンプレミスの Active Directory フォレストを初期化する必要があります。 このコマンドレットでは、Azure テナントのグローバル管理者の資格情報が必要です。 また、オンプレミスの Active Directory のエンタープライズ管理者特権も必要です。 この手順は、フォレストごとに 1 回実行されます。

      `Register-AzureADPasswordProtectionForest` コマンドレットでは、以下の 3 つの認証モードがサポートされます。 Azure Multi-Factor Authentication は、最初の 2 つのモードではサポートされますが、3 つ目のモードではサポートされません。 詳細については、以下のコメントを参照してください。

     * 対話型認証モード:

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > このモードは、Server Core オペレーティング システムでは機能しません。 代わりに以下の 2 つの認証モードのいずれかを使用します。 また、Internet Explorer の強化されたセキュリティ構成が有効になっている場合、このモードは失敗する可能性があります。 回避策としては、その構成を無効にし、フォレストを登録してから、もう一度構成を有効にします。  

     * デバイスコード認証モード:

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XYZABC123 to authenticate.
        ```

        この後、別のデバイスに表示された手順に従って認証を完了します。

     * サイレント (パスワードベース) 認証モード:

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionForest -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > このモードは、お使いのアカウントで Azure Multi-Factor Authentication が必要な場合は失敗します。 その場合は、前の 2 つの認証モードのいずれかを使用するか、MFA を必要としない別のアカウントを使用してください。
        >
        > グローバルに MFA を要求するように Azureデバイス登録 (Azure AD のパスワード保護によってバックグラウンドで使用されます) が構成されている場合も、MFA が必要であることが表示されます。 これを回避するには、MFA をサポートしている別のアカウントで前の 2 つの認証モードのいずれかを使用します。または、Azure デバイス登録の MFA 要件を一時的に緩めることもできます。 これを行うには、Azure 管理ポータルに移動し、[Azure Active Directory]、[デバイス]、[デバイスの設定] の順に選択した後、[デバイスを参加させるには多要素認証が必要] を [いいえ] に設定します。 登録が完了したら、この設定を必ず [はい] に再構成してください。
        >
        > MFA の要件のバイパスは、テスト目的でのみ使用することをお勧めします。

       これらの例は、現在サインインしているユーザーがルート ドメインの Active Directory ドメイン管理者でもある場合にのみ正常に機能します。 これに該当しない場合は、 *-ForestCredential* パラメーターを使用して、代替のドメイン資格情報を指定できます。

   > [!NOTE]
   > 環境に複数のプロキシ サーバーがインストールされている場合、フォレストの登録にどのプロキシ サーバーを使用しても問題はありません。
   >
   > [!TIP]
   > このコマンドレットを特定の Azure テナントに対して最初に実行するときは、完了するまでにかなり時間がかかることがあります。 エラーが報告されない限り、この遅延については心配しないでください。

   Active Directory フォレストの登録は、フォレストの有効期間中に 1 回だけ必要です。 その後、他の必要なメンテナンスは、フォレスト内のドメイン コントローラー エージェントによって自動的に実行されます。 フォレストに対して `Register-AzureADPasswordProtectionForest` が正常に実行された後、それ以降のコマンドレットの呼び出しも成功しますが、必要ありません。

   `Register-AzureADPasswordProtectionForest` を成功させるには、Windows Server 2012 以降を実行している少なくとも 1 台のドメイン コントローラーがプロキシ サーバーのドメイン内で使用できる必要があります。 この手順の前に、DC エージェント ソフトウェアをいずれかのドメイン コントローラーにインストールしておく必要はありません。

1. HTTP プロキシを通じて通信するようにパスワード保護用のプロキシ サービスを構成します。

   ご使用の環境で、Azure との通信に特定の HTTP プロキシを使用する必要がある場合は、この方法を使用します。*AzureADPasswordProtectionProxy.exe.config* ファイルを %ProgramFiles%\Azure AD Password Protection Proxy\Service フォルダーに作成します。 次の内容を含めます。

      ```xml
      <configuration>
        <system.net>
          <defaultProxy enabled="true">
           <proxy bypassonlocal="true"
               proxyaddress="http://yourhttpproxy.com:8080" />
          </defaultProxy>
        </system.net>
      </configuration>
      ```

   HTTP プロキシで認証が必要な場合は、*useDefaultCredentials* タグを追加します。

      ```xml
      <configuration>
        <system.net>
          <defaultProxy enabled="true" useDefaultCredentials="true">
           <proxy bypassonlocal="true"
               proxyaddress="http://yourhttpproxy.com:8080" />
          </defaultProxy>
        </system.net>
      </configuration>
      ```

   いずれの場合も、`http://yourhttpproxy.com:8080` を特定の HTTP プロキシ サーバーのアドレスとポートに置き換えます。

   HTTP プロキシが承認ポリシーを使用するように構成されている場合は、パスワード保護用プロキシ サービスをホストしているコンピューターの Active Directory コンピューター アカウントにアクセス許可を付与する必要があります。

   *AzureADPasswordProtectionProxy.exe.config* ファイルを作成または更新した後では、プロキシ サービスを停止してから再起動することをお勧めします。

   プロキシ サービスでは、HTTP プロキシへの接続に特定の資格情報を使用することはサポートされていません。

1. 省略可能:特定のポートでリッスンするようにパスワード保護用プロキシ サービスを構成します。
   * ドメイン コントローラー上のパスワード保護用 DC エージェント ソフトウェアは、TCP 経由の RPC を使用してプロキシ サービスと通信します。 既定では、プロキシ サービスは、使用可能な任意の動的 RPC エンドポイントでリッスンします。 ただし、ご使用環境のネットワーク トポロジまたはファイアウォールの要件のために必要な場合には、特定の TCP ポート上でリッスンするようにサービスを構成できます。
      * <a id="static" /></a>静的ポートで実行するようにサービスを構成するには、`Set-AzureADPasswordProtectionProxyConfiguration` コマンドレットを使用します。

         ```powershell
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort <portnumber>
         ```

         > [!WARNING]
         > これらの変更を有効にするには、サービスを停止して、再起動する必要があります。

      * 動的ポートで実行するようにサービスを構成するには、同じ手順を使用しますが、*StaticPort* の設定を 0 に戻します。

         ```powershell
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort 0
         ```

         > [!WARNING]
         > これらの変更を有効にするには、サービスを停止して、再起動する必要があります。

   > [!NOTE]
   > パスワード保護用プロキシ サービスは、ポート構成の変更後に、手動で再起動する必要があります。 ただし、これらの構成変更の後で、ドメイン コントローラー上の DC エージェント サービス ソフトウェアを再起動する必要はりません。

   * サービスの現在の構成を確認するには、`Get-AzureADPasswordProtectionProxyConfiguration` コマンドレットを使用します。

      ```powershell
      Get-AzureADPasswordProtectionProxyConfiguration | fl

      ServiceName : AzureADPasswordProtectionProxy
      DisplayName : Azure AD password protection Proxy
      StaticPort  : 0
      ```

### <a name="install-the-dc-agent-service"></a>DC エージェント サービスをインストールする

   `AzureADPasswordProtectionDCAgentSetup.msi` パッケージを使用して、パスワード保護用の DC エージェント サービスをインストールします。

   ソフトウェアをインストールまたはアンインストールすると再起動が必要になります。 この要件は、パスワード フィルター DLL は再起動しないとロードまたはアンロードされないためです。

   DC エージェント サービスは、ドメイン コントローラーになっていないコンピューターにインストールできます。 この場合、サービスは開始して実行されますが、コンピューターがドメイン コントローラーにレベル上げされるまで、サービスはアクティブになりません。

   ソフトウェアのインストールは、標準 MSI プロシージャを使用して自動化できます。 例:

   `msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart`

   インストーラーでコンピューターが自動的に再起動されるようにする場合は、`/norestart` フラグを省略できます。

インストールが完了するのは、DC エージェント ソフトウェアがドメイン コントローラーにインストールされ、そのコンピューターが再起動された後です。 これ以外の構成は必要ないか、可能ではありません。

## <a name="upgrading-the-proxy-agent"></a>プロキシ エージェントのアップグレード

新しいバージョンの Azure AD パスワード保護プロキシ ソフトウェアを使用できるようになったら、最新バージョンの `AzureADPasswordProtectionProxySetup.exe` ソフトウェア インストーラーを実行してアップグレードを行います。 最新バージョンのソフトウェアは、[Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=57071)で入手できます。

現在のバージョンのプロキシ ソフトウェアをアンインストールする必要はありません。インストーラーによってインプレース アップグレードが実行されます。 プロキシ ソフトウェアをアップグレードするときに、再起動は必要ありません。 ソフトウェアのアップグレードは、標準 MSI プロシージャを使用して自動化できます。次はその例です: `AzureADPasswordProtectionProxySetup.exe /quiet`。

プロキシ エージェントでは、自動アップグレードがサポートされています。 自動アップグレードでは、プロキシ サービスとサイド バイ サイドでインストールされる Microsoft Azure AD Connect Agent Updater サービスが使用されます。 自動アップグレードは既定でオンになっており、`Set-AzureADPasswordProtectionProxyConfiguration` コマンドレットを使用して有効または無効にすることができます。 現在の設定は、`Get-AzureADPasswordProtectionProxyConfiguration` コマンドレットを使用して照会できます。 自動アップグレードの設定を常に有効にしておくことをお勧めします。

`Get-AzureADPasswordProtectionProxy` コマンドレットを使用して、フォレストに現在インストールされているすべてのプロキシ エージェントのソフトウェア バージョンを照会できます。

## <a name="upgrading-the-dc-agent"></a>DC エージェントのアップグレード

新しいバージョンの Azure AD パスワード保護 DC エージェント ソフトウェアを使用できるようになったら、最新バージョンの `AzureADPasswordProtectionDCAgentSetup.msi` ソフトウェア パッケージを実行してアップグレードを行います。 最新バージョンのソフトウェアは、[Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=57071)で入手できます。

現在のバージョンの DC エージェント ソフトウェアをアンインストールする必要はありません。インストーラーによってインプレース アップグレードが実行されます。 DC エージェント ソフトウェアをアップグレードするときは、再起動が常に必要になります。この要件は、Windows のコア動作によるものです。 

ソフトウェアのアップグレードは、標準 MSI プロシージャを使用して自動化できます。次はその例です: `msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart`。

インストーラーでコンピューターが自動的に再起動されるようにする場合は、`/norestart` フラグを省略できます。

`Get-AzureADPasswordProtectionDCAgent` コマンドレットを使用して、フォレストに現在インストールされているすべての DC エージェントのソフトウェア バージョンを照会できます。

## <a name="multiple-forest-deployments"></a>複数のフォレストへの展開

Azure AD パスワード保護を複数のフォレストに展開するための追加要件はありません。 各フォレストは、「単一フォレスト デプロイ」セクションで説明したように、個別に構成されます。 各パスワード保護プロキシは、参加先のフォレストのドメイン コントローラーのみをサポートします。 どのフォレスト内のパスワード保護ソフトウェアも、Active Directory 信頼の構成に関係なく、別のフォレストにデプロイされているパスワード保護ソフトウェアを認識しません。

## <a name="read-only-domain-controllers"></a>読み取り専用ドメイン コントローラー

パスワードの変更/設定は、読み取り専用ドメイン コントローラー (RODC) では処理および永続化されません。 これらは、書き込み可能なドメイン コントローラーに転送されます。 そのため、RODC に DC エージェント ソフトウェアをインストールする必要はありません。

## <a name="high-availability"></a>高可用性

パスワード保護の高可用性に関する主要な関心事は、フォレストのドメイン コントローラーが Azure から新しいポリシーやその他のデータをダウンロードしようとしたときのプロキシ サーバーの可用性です。 各 DC エージェントは、どのプロキシ サーバーを呼び出すかを決定するときに、単純なラウンドロビン方式のアルゴリズムを使用します。 エージェントは、応答しないプロキシ サーバーをスキップします。 完全に接続された Active Directory デプロイの場合、ディレクトリと sysvol フォルダー状態の両方の正常なレプリケーションがあるときは、可用性を保証するために 2 つのプロキシ サーバーがあれば十分です。 この場合には、新しいポリシーやその他のデータがタイミングよくダウンロードされます。 ただし、追加のプロキシ サーバーをデプロイすることもできます。

高可用性に関連する一般的な問題は、DC エージェント ソフトウェアの設計によって軽減されています。 DC エージェントは、ごく最近ダウンロードされたパスワード ポリシーのローカル キャッシュを保持します。 登録されているすべてのプロキシ サーバーが使用できなくなった場合でも、DC エージェントは、キャッシュされたパスワード ポリシーを引き続き適用します。 大規模なデプロイでのパスワード ポリシーの妥当な更新頻度は、通常は日単位であり、時間単位やそれ以下の単位ではありません。 そのため、プロキシ サーバーが短時間停止しても、Azure AD パスワード保護に大きな影響はありません。

## <a name="next-steps"></a>次の手順

Azure AD パスワード保護に必要なサービスがオンプレミス サーバーにインストールされたので、[インストール後の構成を行い、レポート情報を収集](howto-password-ban-bad-on-premises-operations.md)して、デプロイを完了します。

[Azure AD パスワード保護の概念の概要](concept-password-ban-bad-on-premises.md)
