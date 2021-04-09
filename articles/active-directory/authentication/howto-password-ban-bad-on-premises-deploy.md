---
title: オンプレミスの Azure AD パスワード保護のデプロイ
description: オンプレミスの Active Directory Domain Services 環境で Azure AD パスワード保護を計画してデプロイする方法について説明します
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 03/05/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: c8cae19bd07e1cc87a0aaa25e47cf5f431d566ba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101653815"
---
# <a name="plan-and-deploy-on-premises-azure-active-directory-password-protection"></a>オンプレミスの Azure Active Directory パスワード保護を計画してデプロイする

ユーザーは多くの場合、学校、スポーツ チーム、有名人などのありふれたローカル単語を使用してパスワードを作成します。 これらのパスワードは簡単に推測できるため、辞書ベースの攻撃に対しては脆弱です。 組織に強力なパスワードを適用するため、Azure Active Directory (Azure AD) パスワード保護では、グローバルかつカスタムの禁止パスワードの一覧が提供されます。 この禁止パスワードの一覧に一致するものがある場合、パスワードの変更要求はエラーとなります。

オンプレミスの Active Directory Domain Services (AD DS) 環境を保護するために、Azure AD パスワード保護をインストールして、オンプレミスの DC と連携するように構成することができます。 この記事では、オンプレミスの環境に Azure AD パスワード保護プロキシ サービスと Azure AD パスワード保護 DC エージェントをインストールして登録する方法について説明します。

オンプレミス環境での Azure AD パスワード保護のしくみの詳細については、[Windows Server Active Directory に Azure AD パスワード保護を適用する方法](concept-password-ban-bad-on-premises.md)に関する記事を参照してください。

## <a name="deployment-strategy"></a>展開戦略

次の図は、Azure AD パスワード保護の基本コンポーネントが、オンプレミスの Active Directory 環境でどのように連携するかを示しています。

![Azure AD パスワード保護コンポーネントの連携方法](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

ソフトウェアをデプロイする前にしくみを確認することをお勧めします。 詳細については、[Azure AD パスワード保護の概念の概要](concept-password-ban-bad-on-premises.md)に関する記事を参照してください。

"*監査*" モードでデプロイを開始することをお勧めします。 監査モードは既定の初期設定であり、パスワードは後から設定できます。 ブロックされるパスワードはイベント ログに記録されます。 プロキシ サーバーと DC エージェントを監査モードでデプロイした後、パスワード ポリシーが適用されるときに、そのポリシーがユーザーに与える影響を監視してください。

多くの組織では、この監査段階で次のような状況を認識します。

* より安全なパスワードを使用するように既存の運用プロセスを改善する必要がある。
* 多くの場合、ユーザーは安全でないパスワードを使用している。
* セキュリティの適用の今後の変更、生じる可能性がある影響、およびより安全なパスワードを選択する方法をユーザーに通知する必要がある。

より強力なパスワード検証が、既存の Active Directory ドメイン コントローラーのデプロイの自動化に影響を与える可能性もあります。 このような問題を発見できるように、監査期間の評価中に、少なくとも 1 つの DC 昇格と 1 つの DC 降格が行われるようにすることをお勧めします。 詳細については、次の記事を参照してください。

* [Ntdsutil.exe で、ディレクトリ サービスの修復モードの弱いパスワードを設定できない](howto-password-ban-bad-on-premises-troubleshoot.md#ntdsutilexe-fails-to-set-a-weak-dsrm-password)
* [ディレクトリ サービスの修復モードの弱いパスワードが原因でドメイン コントローラー レプリカの昇格が失敗する](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-replica-promotion-fails-because-of-a-weak-dsrm-password)
* [弱いローカル管理者パスワードのためにドメイン コントローラーの降格が失敗する](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-demotion-fails-due-to-a-weak-local-administrator-password)

妥当な期間にわたってこの機能を監査モードで実行した後、構成を "*監査*" から "*適用*" に切り替えて、さらに安全なパスワードを要求できます。 この期間中に、追加の監視を行うことをお勧めします。

Azure AD のパスワード保護では、パスワードの変更または設定操作中にのみパスワードの検証を行うことができることに注意してください。 Azure AD のパスワード保護をデプロイする前に Active Directory に受け入れられ保存されたパスワードは、検証されず、そのまま動作し続けます。 時間の経過に伴い、すべてのユーザーとアカウントでは、通常どおり既存のパスワードの有効期限が切れると Azure AD のパスワード保護で検証されたパスワードの使用が開始されます。 ただし、[パスワードを無期限にする] を使用して構成されたアカウントを除きます。

### <a name="multiple-forest-considerations"></a>複数のフォレストに関する考慮事項

Azure AD パスワード保護を複数のフォレストに展開するための追加要件はありません。

各フォレストは、次のセクションで説明するように、[オンプレミスの Azure AD パスワード保護をデプロイする](#download-required-software)ために個別に構成されます。 各 Azure AD パスワード保護プロキシでサポートできるのは、参加先のフォレストのドメイン コントローラーのみです。

どのフォレスト内の Azure AD パスワード保護ソフトウェアも、Active Directory 信頼の構成に関係なく、別のフォレストにデプロイされているパスワード保護ソフトウェアを認識しません。

### <a name="read-only-domain-controller-considerations"></a>読み取り専用ドメイン コントローラーに関する考慮事項

パスワードの変更または設定のイベントは、読み取り専用ドメイン コントローラー (RODC) では処理および永続化されません。 代わりに、これらは、書き込み可能なドメイン コントローラーに転送されます。 RODC に Azure AD パスワード保護 DC エージェント ソフトウェアをインストールする必要はありません。

さらに、読み取り専用ドメイン コントローラーでの Azure AD パスワード保護プロキシ サービスの実行はサポートされていません。

### <a name="high-availability-considerations"></a>高可用性に関する考慮事項

パスワード保護に関する主要な関心事は、フォレストの DC が Azure から新しいポリシーやその他のデータをダウンロードしようとしたときの Azure AD パスワード保護プロキシ サーバーの可用性です。 各 Azure AD パスワード保護 DC エージェントでは、どのプロキシ サーバーを呼び出すかを決定するときに、単純なラウンドロビン方式のアルゴリズムを使用します。 エージェントは、応答しないプロキシ サーバーをスキップします。

完全に接続された Active Directory デプロイの場合、ディレクトリと sysvol フォルダー状態の両方の正常なレプリケーションがあるときは、可用性を確保するには 2 つの Azure AD パスワード保護プロキシ サーバーがあれば十分です。 この構成により、新しいポリシーやその他のデータがタイミングよくダウンロードされます。 必要に応じて、追加の Azure AD パスワード保護プロキシ サーバーをデプロイできます。

高可用性に関連する一般的な問題は、Azure AD パスワード保護 DC エージェント ソフトウェアの設計によって軽減されています。 Azure AD パスワード保護 DC エージェントは、最後にダウンロードされたパスワード ポリシーのローカル キャッシュを保持します。 登録されているすべてのプロキシ サーバーが使用できなくなった場合でも、Azure AD パスワード保護 DC エージェントは、キャッシュされたパスワード ポリシーを引き続き適用します。

大規模なデプロイでのパスワード ポリシーの妥当な更新頻度は、通常は日単位であり、時間単位やそれ以下の単位ではありません。 そのため、プロキシ サーバーが短時間停止しても、Azure AD パスワード保護に大きな影響はありません。

## <a name="deployment-requirements"></a>デプロイ要件

ライセンスの詳細については、[Azure AD パスワード保護のライセンスの要件](concept-password-ban-bad.md#license-requirements)に関する記事を参照してください。

次の主要な要件が適用されます。

* ドメイン コントローラーを含め、Azure AD パスワード保護コンポーネントがインストールされているすべてのマシンに、ユニバーサル C ランタイムがインストールされている必要があります。
    * Windows Update からすべての更新プログラムを確実に取得することでランタイムを入手できます。 または、OS 固有の更新プログラム パッケージで入手できます。 詳しくは、「[Update for Universal C Runtime in Windows (Windows のユニバーサル C ランタイムの更新プログラム)](https://support.microsoft.com/help/2999226/update-for-uniersal-c-runtime-in-windows)」をご覧ください。
* Azure AD に Windows Server Active Directory フォレストを登録するために、フォレスト ルート ドメインの Active Directory ドメイン管理者特権を持つアカウントが必要です。
* Windows Server 2012 以降を実行している、ドメイン内のすべてのドメイン コントローラーで、キー配布サービスを有効にする必要があります。 既定では、このサービスは手動トリガーで開始して有効化されます。

* 各ドメイン内の少なくとも 1 つのドメイン コントローラーと、Azure AD パスワード保護用のプロキシ サービスをホストする少なくとも 1 つのサーバーとの間に、ネットワーク接続が存在する必要があります。 この接続では、ドメイン コントローラーがプロキシ サービス上の RPC エンドポイント マッパー ポート 135 および RPC サーバー ポートにアクセスできるようにする必要があります。
    * RPC サーバー ポートは、既定では動的 RPC ポートですが、[静的ポートを使用](#static)するように構成することができます。
* Azure AD のパスワード保護プロキシ サービスがインストールされるすべてのマシンに、次のエンドポイントへのネットワーク アクセスが必要です。

    |**エンドポイント**|**目的**|
    | --- | --- |
    |`https://login.microsoftonline.com`|認証要求|
    |`https://enterpriseregistration.windows.net`|Azure AD パスワード保護機能|

### <a name="azure-ad-password-protection-dc-agent"></a>Azure AD パスワード保護 DC エージェント

Azure AD パスワード保護 DC エージェントには、次の要件が適用されます。

* Azure AD パスワード保護 DC エージェント ソフトウェアがインストールされるすべてのマシンで、Windows Server Core エディションを含め、Windows Server 2012 以降が実行されている必要があります。
    * Active Directory ドメインまたはフォレストは、Windows Server 2012 のドメインの機能レベル (DFL) またはフォレストの機能レベル (FFL) である必要はありません。 「[設計原則](concept-password-ban-bad-on-premises.md#design-principles)」で説明されているように、DC エージェントまたはプロキシ ソフトウェアの実行に必要な最低限の DFL や FFL はありません。
* Azure AD パスワード保護プロキシ サービスがインストールされるすべてのマシンには、.NET 4.7.2 をインストールしておく必要があります。
    * .NET 4.7.2 がまだインストールされていない場合は、[Windows 用の .NET Framework 4.7.2 オフライン インストーラー](https://support.microsoft.com/topic/microsoft-net-framework-4-7-2-offline-installer-for-windows-05a72734-2127-a15d-50cf-daf56d5faec2)に関するページにあるインストーラーをダウンロードして実行してください。
* Azure AD パスワード保護 DC エージェント サービスを実行しているすべての Active Directory ドメインは、sysvol レプリケーションに分散ファイル システム レプリケーション (DFSR) を使用する必要があります。
   * ご利用のドメインでまだ DFSR を使用していない場合、Azure AD パスワード保護をインストールする前に移行する必要があります。 詳細については、次を参照してください。[SYSVOL レプリケーション移行ガイド:FRS レプリケーションから DFS レプリケーションに移行する](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd640019(v=ws.10))

    > [!WARNING]
    > Azure AD パスワード保護 DC エージェント ソフトウェアは現在のところ、sysvol レプリケーションのために依然として FRS (DFSR の前身テクノロジ) を使用しているドメインにあるドメイン コントローラーにインストールされますが、この環境ではソフトウェアは正しく機能しません。
    >
    > その他のマイナスの副作用としては、個々のファイルを複製できない、sysvol 復元処理が成功したように見えたが、一部のファイルの複製に失敗しており、何のエラーも表示されない、などがあります。
    >
    > できるだけ早くドメインを移行して、DFSR に固有のメリットと Azure AD パスワード保護のデプロイのブロック解除のために、DFSR を使用するようにしてください。 このソフトウェアの今後のバージョンは、依然として FRS を使用しているドメインで実行すると、自動的に無効になります。

### <a name="azure-ad-password-protection-proxy-service"></a>Azure AD パスワード保護プロキシ サービス

Azure AD パスワード保護 プロキシ サービスには、次の要件が適用されます。

* Azure AD パスワード保護プロキシー サービスがインストールされるすべてのマシンで、Windows Server Core エディションを含め、Windows Server 2012 R2 以降が実行されている必要があります。

    > [!NOTE]
    > インターネットへの直接の送信接続がドメイン コントローラーにある場合でも、Azure AD パスワード保護のデプロイには、Azure AD パスワード保護プロキシ サービスのデプロイが必須要件です。

* Azure AD パスワード保護プロキシ サービスがインストールされるすべてのマシンには、.NET 4.7.2 をインストールしておく必要があります。
    * .NET 4.7.2 がまだインストールされていない場合は、[Windows 用の .NET Framework 4.7.2 オフライン インストーラー](https://support.microsoft.com/topic/microsoft-net-framework-4-7-2-offline-installer-for-windows-05a72734-2127-a15d-50cf-daf56d5faec2)に関するページにあるインストーラーをダウンロードして実行してください。
* Azure AD パスワード保護プロキシ サービスがホストされているすべてのマシンを、このプロキシ サービスにログオンする機能をドメイン コントローラーに許可するように、構成する必要があります。 この機能は、"ネットワーク経由でコンピューターへアクセス" 特権の割り当てによって制御されます。
* Azure AD パスワード保護プロキシ サービスがホストされているすべてのマシンを、送信 TLS 1.2 HTTP トラフィックを許可するように構成する必要があります。
* Azure AD に Azure AD パスワード保護プロキシ サービスとフォレストを登録する "*グローバル管理者*" または "*セキュリティ管理者*" アカウント。
* [アプリケーション プロキシ環境の設定手順](../manage-apps/application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment)で指定されている一連のポートと URL に対して、ネットワーク アクセスを有効にする必要があります。

### <a name="microsoft-azure-ad-connect-agent-updater-prerequisites"></a>Microsoft Azure AD Connect エージェント アップデーターの前提条件

Microsoft Azure AD Connect エージェント アップデーター サービスは、Azure AD パスワード保護プロキシ サービスとサイド バイ サイドでインストールされます。 Microsoft Azure AD Connect エージェント アップデーター サービスを機能させるには、追加の構成が必要です。

* お使いの環境で HTTP プロキシ サーバーを使用している場合は、「[既存のオンプレミス プロキシ サーバーと連携する](../manage-apps/application-proxy-configure-connectors-with-proxy-servers.md)」に明記されているガイドラインに従ってください。
* Microsoft Azure AD Connect Agent Updater サービスでは、[TLS 要件](../manage-apps/application-proxy-add-on-premises-application.md#tls-requirements)で指定されている TLS 1.2 の手順も必要です。

> [!WARNING]
> Azure AD パスワード保護プロキシと Azure AD アプリケーション プロキシでは、異なるバージョンの Microsoft Azure AD Connect エージェント アップデーター サービスがインストールされます。そのため、説明ではアプリケーション プロキシの内容を示しています。 これらの異なるバージョンは、サイドバイサイドでインストールされた場合に互換性がないため、これを実行するとエージェント アップデーター サービスがソフトウェアの更新のために Azure に接続できなくなります。そのため、Azure AD パスワード保護プロキシとアプリケーション プロキシを同じコンピューター上にインストールしないでください。

## <a name="download-required-software"></a>必要なソフトウェアのダウンロード

オンプレミスの Azure AD パスワード保護のデプロイには 2 つのインストーラーが必要です。

* Azure AD パスワード保護 DC エージェント (*AzureADPasswordProtectionDCAgentSetup.msi*)
* Azure AD パスワード保護プロキシ (*AzureADPasswordProtectionProxySetup.exe*)

[Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=57071)から両方のインストーラーをダウンロードします。

## <a name="install-and-configure-the-proxy-service"></a>プロキシ サービスのインストールと構成

Azure AD パスワード保護プロキシ サービスは、通常、オンプレミスの AD DS 環境のメンバー サーバー上にあります。 インストールが完了すると、Azure AD パスワード保護プロキシ サービスは Azure AD と通信し、Azure AD テナント向けのグローバルおよび顧客の禁止パスワードの一覧のコピーを保持します。

次のセクションでは、オンプレミスの AD DS 環境のドメイン コントローラーに Azure AD パスワード保護 DC エージェントをインストールします。 これらの DC エージェントはプロキシ サービスと通信して、ドメイン内でパスワード変更イベントを処理するときに使用する最新の禁止パスワードの一覧を取得します。

Azure AD パスワード保護プロキシ サービスをホストする 1 つ以上のサーバーを選択します。 このサーバーには、次の考慮事項が適用されます。

* これら各サービスは、単一フォレストのパスワード ポリシーのみを提供できます。 ホスト マシンは、そのフォレスト内の任意のドメインに参加している必要があります。
* ルートまたは子ドメイン、またはそれらの組み合わせのいずれかにプロキシ サービスをインストールできます。
* フォレストの各ドメインの少なくとも 1 つの DC とパスワード保護プロキシ サーバーの間にネットワーク接続が必要です。
* テストのためにドメイン コントローラーで Azure AD パスワード保護プロキシ サービスを実行することはできますが、その場合、ドメイン コントローラーにはインターネット接続が必要です。 この接続は、セキュリティ上の問題になる可能性があります。 この構成はテスト専用としてお勧めします。
* [高可用性に関する考慮事項](#high-availability-considerations)について前のセクションで説明したように、冗長性を確保するためにフォレストあたり少なくとも 2 つの Azure AD パスワード保護プロキシ サーバーを使用することをお勧めします。
* 読み取り専用ドメイン コントローラーでの Azure AD パスワード保護プロキシ サービスの実行はサポートされていません。

Azure AD パスワード保護プロキシ サービスをインストールするには、次の手順を実行します。

1. Azure AD パスワード保護プロキシ サービスをインストールするために、`AzureADPasswordProtectionProxySetup.exe` ソフトウェア インストーラーを実行します。

    このソフトウェアのインストールでは再起動を必要とせず、次の例のように、標準 MSI プロシージャを使用して自動化できます。
    
    ```console
    AzureADPasswordProtectionProxySetup.exe /quiet
    ```
    
    > [!NOTE]
    > インストール エラーを回避するため、`AzureADPasswordProtectionProxySetup.exe` パッケージをインストールする前に Windows ファイアウォール サービスを実行しておく必要があります。
    >
    > Windows Firewall が実行されないように構成されている場合は、回避策として、インストールの際に一時的に Windows Firewall サービスを有効化して実行します。 インストール後はプロキシ ソフトウェアが Windows Firewall に特に依存することはありません。
    >
    > サードパーティ製のファイアウォールを使用している場合も、デプロイ要件を満たすように構成する必要があります。 これらには、ポート 135 およびプロキシ RPC サーバー ポートへの着信アクセスの許可が含まれます。 詳細については、[デプロイ要件](#deployment-requirements)に関する前のセクションを参照してください。

1. Azure AD パスワード保護プロキシ ソフトウェアには、新しい PowerShell モジュール `AzureADPasswordProtection` が含まれています。 この後の手順では、この PowerShell モジュールからさまざまなコマンドレットを実行します。

    このモジュールを使用するには、管理者として PowerShell ウィンドウを開き、次のように新しいモジュールをインポートします。
    
    ```powershell
    Import-Module AzureADPasswordProtection
    ```

1. Azure AD パスワード保護プロキシ サービスが実行されていることを確認するには、次の PowerShell コマンドを使用します。

    ```powershell
    Get-Service AzureADPasswordProtectionProxy | fl
    ```

    結果の **[Status]** が "*Running*" と表示されます。

1. プロキシ サービスはマシンで実行されていますが、Azure AD と通信するための資格情報を保持していません。 `Register-AzureADPasswordProtectionProxy` コマンドレットを使用して、Azure AD に Azure AD パスワード保護プロキシ サーバーを登録します。

    このコマンドレットを使用するには、Azure テナントの "*グローバル管理者*" または "*セキュリティ管理者*" の資格情報が必要です。 また、このコマンドレットは、ローカル管理者特権を持つアカウントを使って実行する必要があります。

    このコマンドが 1 回成功すると、次回以降の呼び出しも成功しますが、これ以上は必要はありません。

    `Register-AzureADPasswordProtectionProxy` コマンドレットでは、以下の 3 つの認証モードがサポートされます。 Azure AD Multi-Factor Authentication は、最初の 2 つのモードではサポートされますが、3 つ目のモードではサポートされません。

    > [!TIP]
    > このコマンドレットを特定の Azure テナントに対して最初に実行するときは、完了するまでにかなり時間がかかることがあります。 エラーが報告されない限り、この遅延については心配しないでください。

     * 対話型認証モード:

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > このモードは、Server Core オペレーティング システムでは機能しません。 代わりに以下の認証モードのいずれかを使用できます。 また、Internet Explorer の強化されたセキュリティ構成が有効になっている場合、このモードは失敗する可能性があります。 回避策としては、その構成を無効にし、プロキシを登録してから、もう一度構成を有効にします。

     * デバイスコード認証モード:

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        ```

        プロンプトが表示されたら、リンクに従って Web ブラウザーを開き、認証コードを入力します。

     * サイレント (パスワードベース) 認証モード:

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionProxy -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > このモードは、お使いのアカウントで Azure AD Multi-Factor Authentication が必要な場合は失敗します。 その場合は、前の 2 つの認証モードのいずれかを使用するか、MFA を必要としない別のアカウントを使用してください。
        >
        > グローバルに MFA を要求するように Azureデバイス登録 (Azure AD のパスワード保護によってバックグラウンドで使用されます) が構成されている場合も、MFA が必要であることが表示されます。 この要件に対処するには、前の 2 つの認証モードのいずれかで、MFA をサポートしている別のアカウントを使用します。または、Azure Device Registration の MFA 要件を一時的に緩めることもできます。
        >
        > この変更を行うには、Azure portal で **[Azure Active Directory]** を検索して選択し、 **[デバイス] > [デバイスの設定]** を選択します。 **[デバイスを参加させるには多要素認証が必要]** を *[いいえ]* に設定します。 登録が完了したら、この設定を必ず *[はい]* に再構成してください。
        >
        > MFA の要件のバイパスは、テスト目的でのみ使用することをお勧めします。

    現時点では、今後提供される機能用に予約された *-ForestCredential* パラメーターを指定する必要はありません。

    Azure AD パスワード保護プロキシ サービスの登録は、サービスの有効期間内に 1 回だけ行う必要があります。 その後、他の必要なメンテナンスは、Azure AD パスワード保護プロキシ サービスによって自動的に実行されます。

1. 次は、`Register-AzureADPasswordProtectionForest` PowerShell コマンドレットを使用して、Azure と通信するために必要な資格情報でオンプレミスの Active Directory フォレストを登録します。

    > [!NOTE]
    > 環境に複数の Azure AD パスワード保護プロキシ サーバーがインストールされている場合、フォレストの登録にどのプロキシ サーバーを使用しても問題はありません。

    このコマンドレットを使用するには、Azure テナントの "*グローバル管理者*" または "*セキュリティ管理者*" の資格情報が必要です。 また、オンプレミスの Active Directory のエンタープライズ管理者特権も必要です。 ローカル管理者特権を持つアカウントを使って、このコマンドレットを実行する必要があります。 フォレストの登録に使用される Azure アカウントは、オンプレミスの Active Directory アカウントとは異なる場合があります。
    
    この手順は、フォレストごとに 1 回実行されます。

    `Register-AzureADPasswordProtectionForest` コマンドレットでは、以下の 3 つの認証モードがサポートされます。 Azure AD Multi-Factor Authentication は、最初の 2 つのモードではサポートされますが、3 つ目のモードではサポートされません。

    > [!TIP]
    > このコマンドレットを特定の Azure テナントに対して最初に実行するときは、完了するまでにかなり時間がかかることがあります。 エラーが報告されない限り、この遅延については心配しないでください。

     * 対話型認証モード:

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > このモードは、Server Core オペレーティング システムでは機能しません。 代わりに以下の 2 つの認証モードのいずれかを使用します。 また、Internet Explorer の強化されたセキュリティ構成が有効になっている場合、このモードは失敗する可能性があります。 回避策としては、その構成を無効にし、フォレストを登録してから、もう一度構成を有効にします。  

     * デバイスコード認証モード:

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        ```

        プロンプトが表示されたら、リンクに従って Web ブラウザーを開き、認証コードを入力します。

     * サイレント (パスワードベース) 認証モード:

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionForest -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > このモードは、お使いのアカウントで Azure AD Multi-Factor Authentication が必要な場合は失敗します。 その場合は、前の 2 つの認証モードのいずれかを使用するか、MFA を必要としない別のアカウントを使用してください。
        >
        > グローバルに MFA を要求するように Azureデバイス登録 (Azure AD のパスワード保護によってバックグラウンドで使用されます) が構成されている場合も、MFA が必要であることが表示されます。 この要件に対処するには、前の 2 つの認証モードのいずれかで、MFA をサポートしている別のアカウントを使用します。または、Azure Device Registration の MFA 要件を一時的に緩めることもできます。
        >
        > この変更を行うには、Azure portal で **[Azure Active Directory]** を検索して選択し、 **[デバイス] > [デバイスの設定]** を選択します。 **[デバイスを参加させるには多要素認証が必要]** を *[いいえ]* に設定します。 登録が完了したら、この設定を必ず *[はい]* に再構成してください。
        >
        > MFA の要件のバイパスは、テスト目的でのみ使用することをお勧めします。

       これらの例は、現在サインインしているユーザーがルート ドメインの Active Directory ドメイン管理者でもある場合にのみ正常に機能します。 これに該当しない場合は、 *-ForestCredential* パラメーターを使用して、代替のドメイン資格情報を指定できます。

    Active Directory フォレストの登録は、フォレストの有効期間中に 1 回だけ必要です。 その後、他の必要なメンテナンスは、フォレスト内の Azure AD パスワード保護 DC エージェントによって自動的に実行されます。 フォレストに対して `Register-AzureADPasswordProtectionForest` が正常に実行された後、それ以降のコマンドレットの呼び出しも成功しますが、必要ありません。
    
    `Register-AzureADPasswordProtectionForest` を成功させるには、Windows Server 2012 以降を実行している少なくとも 1 台の DC が Azure AD パスワード保護プロキシ サーバーのドメイン内で使用できる必要があります。 この手順の前に、Azure AD パスワード保護 DC エージェント ソフトウェアをドメイン コントローラーにインストールしておく必要はありません。

### <a name="configure-the-proxy-service-to-communicate-through-an-http-proxy"></a>HTTP プロキシを通じて通信するようにプロキシ サービスを構成する

ご使用の環境で Azure との通信に特定の HTTP プロキシを使用する必要がある場合は、次の手順を使用して、Azure AD パスワード保護サービスを構成します。

`%ProgramFiles%\Azure AD Password Protection Proxy\Service` フォルダーに *AzureADPasswordProtectionProxy.exe.config* ファイルを作成します。 次の内容を含めます。

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

*AzureADPasswordProtectionProxy.exe.config* ファイルを作成または更新した後は、Azure AD パスワード保護プロキシ サービスを停止して再起動することをお勧めします。

プロキシ サービスでは、HTTP プロキシへの接続に特定の資格情報を使用することはサポートされていません。

### <a name="configure-the-proxy-service-to-listen-on-a-specific-port"></a>特定のポートでリッスンするようにプロキシ サービスを構成する

Azure AD パスワード保護 DC エージェント ソフトウェアは、TCP 経由の RPC を使用してプロキシ サービスと通信します。 既定では、Azure AD パスワード保護プロキシ サービスは、使用可能な動的 RPC エンドポイントでリッスンします。 ご使用の環境のネットワーク トポロジまたはファイアウォールの要件のために必要な場合には、特定の TCP ポート上でリッスンするようにサービスを構成できます。

<a id="static" /></a>静的ポートで実行するようにサービスを構成するには、次のように `Set-AzureADPasswordProtectionProxyConfiguration` コマンドレットを使用します。

```powershell
Set-AzureADPasswordProtectionProxyConfiguration –StaticPort <portnumber>
```

> [!WARNING]
> これらの変更を有効にするには、Azure AD パスワード保護プロキシ サービスを停止して再起動する必要があります。

動的ポートで実行するようにサービスを構成するには、同じ手順を使用しますが、*StaticPort* の設定を 0 に戻します。

```powershell
Set-AzureADPasswordProtectionProxyConfiguration –StaticPort 0
```

> [!WARNING]
> これらの変更を有効にするには、Azure AD パスワード保護プロキシ サービスを停止して再起動する必要があります。

ポート構成の変更後、Azure AD パスワード保護プロキシ サービスを手動で再起動する必要があります。 これらの構成変更の後、ドメイン コントローラー上の Azure AD パスワード保護 DC エージェント サービスを再起動する必要はありません。

サービスの現在の構成を照会するには、次の例に示すように `Get-AzureADPasswordProtectionProxyConfiguration` コマンドレットを使用します

```powershell
Get-AzureADPasswordProtectionProxyConfiguration | fl
```

次の出力例は、Azure AD パスワード保護プロキシ サービスが動的ポートを使用していることを示しています。

```output
ServiceName : AzureADPasswordProtectionProxy
DisplayName : Azure AD password protection Proxy
StaticPort  : 0
```

## <a name="install-the-dc-agent-service"></a>DC エージェント サービスをインストールする

Azure AD パスワード保護 DC エージェント サービスをインストールするには、`AzureADPasswordProtectionDCAgentSetup.msi` パッケージを実行します。

ソフトウェアのインストールは、次の例に示すように、標準 MSI プロシージャを使用して自動化できます。

```console
msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart
```

インストーラーでマシンを自動的に再起動する場合は、`/norestart` フラグを省略できます。

ソフトウェアをインストールまたはアンインストールすると再起動が必要になります。 この要件は、パスワード フィルター DLL は再起動しないとロードまたはアンロードされないためです。

オンプレミスの Azure AD パスワード保護のインストールが完了するのは、DC エージェント ソフトウェアがドメイン コントローラーにインストールされ、そのコンピューターが再起動された後です。 これ以外の構成は必要ないか、可能ではありません。 オンプレミスの DC に対するパスワード変更イベントでは、Azure AD の構成された禁止パスワードの一覧が使用されます。

Azure portal からオンプレミスの Azure AD パスワード保護を有効にしたり、カスタムの禁止パスワードを構成したりするには、[オンプレミスの Azure AD パスワード保護の有効化](howto-password-ban-bad-on-premises-operations.md)に関する記事を参照してください。

> [!TIP]
> Azure AD パスワード保護 DC エージェントは、まだドメイン コントローラーになっていないマシンにインストールできます。 この場合、サービスは開始して実行されますが、マシンがドメイン コントローラーにレベル上げされるまでアクティブになりません。

## <a name="upgrading-the-proxy-service"></a>プロキシ サービスのアップグレード

Azure AD パスワード保護プロキシ サービスでは、自動アップグレードがサポートされています。 自動アップグレードでは、プロキシ サービスとサイド バイ サイドでインストールされる Microsoft Azure AD Connect エージェント アップデーター サービスが使用されます。 自動アップグレードは既定でオンになっており、`Set-AzureADPasswordProtectionProxyConfiguration` コマンドレットを使用して有効または無効にすることができます。

現在の設定は、`Get-AzureADPasswordProtectionProxyConfiguration` コマンドレットを使用して照会できます。 自動アップグレードの設定を常に有効にしておくことをお勧めします。

`Get-AzureADPasswordProtectionProxy` コマンドレットを使用して、フォレストに現在インストールされているすべての Azure AD パスワード保護プロキシ サーバーのソフトウェア バージョンを照会できます。

### <a name="manual-upgrade-process"></a>手動アップグレード プロセス

手動アップグレードを行うには、`AzureADPasswordProtectionProxySetup.exe` ソフトウェア インストーラーの最新バージョンを実行します。 最新バージョンのソフトウェアは、[Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=57071)で入手できます。

現在のバージョンの Azure AD パスワード保護プロキシ サービスをアンインストールする必要はありません。インストーラーによってインプレース アップグレードが実行されます。 プロキシ サービスをアップグレードする際に、再起動は不要です。 ソフトウェアのアップグレードは、標準 MSI プロシージャを使用して自動化できます。たとえば、`AzureADPasswordProtectionProxySetup.exe /quiet` などです。

## <a name="upgrading-the-dc-agent"></a>DC エージェントのアップグレード

新しいバージョンの Azure AD パスワード保護 DC エージェント ソフトウェアを使用できるようになったら、最新バージョンの `AzureADPasswordProtectionDCAgentSetup.msi` ソフトウェア パッケージを実行してアップグレードを行います。 最新バージョンのソフトウェアは、[Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=57071)で入手できます。

現在のバージョンの DC エージェント ソフトウェアをアンインストールする必要はありません。インストーラーによってインプレース アップグレードが実行されます。 DC エージェント ソフトウェアをアップグレードするときは、再起動が常に必要になります。この要件は、Windows のコア動作によるものです。

ソフトウェアのアップグレードは、標準 MSI プロシージャを使用して自動化できます。例えば、`msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart` などです。

インストーラーでコンピューターが自動的に再起動されるようにする場合は、`/norestart` フラグを省略できます。

`Get-AzureADPasswordProtectionDCAgent` コマンドレットを使用して、フォレストに現在インストールされているすべての Azure AD パスワード保護 DC エージェントのソフトウェア バージョンを照会できます。

## <a name="next-steps"></a>次のステップ

Azure AD パスワード保護に必要なサービスがオンプレミス サーバーにインストールされたので、[Azure portal でオンプレミスの Azure AD パスワード保護を有効にして](howto-password-ban-bad-on-premises-operations.md)、デプロイを完了します。
