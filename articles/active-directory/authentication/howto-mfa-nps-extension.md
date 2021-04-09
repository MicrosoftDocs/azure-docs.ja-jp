---
title: NPS で Azure AD Multi-Factor Authentication を使用する - Azure Active Directory
description: 既存のネットワーク ポリシー サーバー (NPS) 認証インフラストラクチャで Azure AD Multi-Factor Authentication の機能を使用する方法について説明します
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 08/31/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 484dd8313710332660bb20d55f3dac2aa21bbc61
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98232524"
---
# <a name="integrate-your-existing-network-policy-server-nps-infrastructure-with-azure-ad-multi-factor-authentication"></a>Azure AD Multi-Factor Authentication と既存のネットワーク ポリシー サーバー (NPS) インフラストラクチャの統合

Azure AD Multi-Factor Authentication のネットワーク ポリシー サーバー (NPS) 拡張機能は、既存のサーバーを使用してクラウド ベースの MFA 機能を認証インフラストラクチャに追加します。 NPS 拡張機能を使用すると、新しいサーバーをインストール、構成、管理することなく、電話、テキスト メッセージ、またはモバイル アプリによる検証を既存の認証フローに追加できます。

NPS 拡張機能は、RADIUS とクラウド ベース Azure AD Multi-Factor Authentication の間のアダプターとして機能し、認証の 2 番目の要素をフェデレーション ユーザーまたは同期済みユーザーに提供します。

## <a name="how-the-nps-extension-works"></a>NPS 拡張機能のしくみ

Azure AD Multi-Factor Authentication の NPS 拡張機能を使用する場合、認証フローには次のコンポーネントが含まれます。

1. **NAS/VPN サーバー**: VPN クライアントから受信した要求を RADIUS 要求に変換して NPS サーバーに送信します。
2. **NPS サーバー**: Active Directory Domain Service (AD DS) に接続して RADIUS 要求のプライマリ認証を行います。成功したら、インストール済みの任意の拡張機能に要求を渡します。  
3. **NPS 拡張機能**: セカンダリ認証のために Azure AD Multi-Factor Authentication への要求をトリガーします。 応答を受信したら、MFA チャレンジが成功していた場合は、Azure STS が発行した MFA クレームを含むセキュリティ トークンを NPS サーバーに提供して認証要求を完了します。
4. **Azure AD MFA**: Azure Active Directory (Azure AD) と通信してユーザーの詳細を取得し、ユーザーに構成されている検証メソッドを使用してセカンダリ認証を行います。

次の図に、この認証要求フローの概要を示します。

![VPN サーバーを介して NPS サーバーと Azure AD Multi-Factor Authentication の NPS 拡張機能に対して認証を行うユーザーの認証フローの図](./media/howto-mfa-nps-extension/auth-flow.png)

### <a name="radius-protocol-behavior-and-the-nps-extension"></a>RADIUS プロトコルの動作と NPS 拡張機能

RADIUS は UDP プロトコルであるため、送信側はパケット損失を想定し、応答を待機します。 一定の時間が経過すると、接続がタイムアウトする場合があります。その場合、送信側はパケットが送信先に届かなかったと想定してパケットを再送信します。 この記事の認証シナリオでは、VPN サーバーが要求を送信し、応答を待機します。 接続がタイムアウトした場合、VPN サーバーは要求を再度送信します。

![NPS サーバーからの応答のタイムアウト後の RADIUS UDP パケット フローと要求の図](./media/howto-mfa-nps-extension/radius-flow.png)

NPS サーバーは、MFA 要求がまだ処理されている可能性があるため、接続がタイムアウトする前は、VPN サーバーの元の要求に応答できません。 ユーザーが MFA プロンプトに正常に応答しなかった場合もあります。そのため、Azure AD Multi-Factor Authentication の NPS 拡張機能は、そのイベントが完了するのを待機しています。 この場合、NPS サーバーでは、追加の VPN サーバー要求が複製要求と識別されます。 NPS サーバーでは、これらの重複する VPN サーバー要求が破棄されます。

![RADIUS サーバーからの複製要求を破棄する NPS サーバーの図](./media/howto-mfa-nps-extension/discard-duplicate-requests.png)

NPS サーバーのログを見ると、これらの追加の要求が破棄されている可能性があります。 この動作は、エンド ユーザーが 1 回の認証試行に対して複数の要求を取得できないようにするための仕様です。 NPS サーバーのイベント ログの破棄された要求は、NPS サーバーまたは Azure AD Multi-Factor Authentication の NPS 拡張機能に問題があることを示すものではありません。

破棄される要求を最小限に抑えるには、VPN サーバーを少なくとも 60 秒のタイムアウトで構成することをお勧めします。 必要に応じて、またはイベント ログの破棄された要求を減らすために、VPN サーバーのタイムアウト値を 90 秒または 120 秒に増やすことができます。

この UDP プロトコルの動作により、NPS サーバーは、ユーザーが最初の要求に応答した後でも複製要求を受信し、別の MFA プロンプトを送信する可能性があります。 このタイミング条件を回避するために、Azure AD Multi-Factor Authentication の NPS 拡張機能は、正常な応答が VPN サーバーに送信されてから最大 10 秒間、複製要求をフィルター処理して破棄し続けます。

![正常な応答が返されてから 10 秒間、VPN サーバーからの複製要求を破棄し続ける NPS サーバーの図](./media/howto-mfa-nps-extension/delay-after-successful-authentication.png)

この場合も、Azure AD Multi-Factor Authentication プロンプトが正常に終了したにも関わらず、NPS サーバーのイベント ログには破棄された要求がある可能性があります。 これは予期される動作であり、NPS サーバーまたは Azure AD Multi-Factor Authentication の NPS 拡張機能に問題があることを示すものではありません。

## <a name="plan-your-deployment"></a>デプロイを計画する

NPS 拡張機能は、自動的に冗長性を処理するため、特別な構成は不要です。

必要な数だけ Azure AD Multi-Factor Authentication 対応の NPS サーバーを作成できます。 複数のサーバーをインストールする場合、サーバーごとに異なるクライアント証明書を使用する必要があります。 サーバーごとに証明書を作成することは、各証明書を個別に更新でき、すべてのサーバー全体でのダウンタイムを心配しなくてもよいことを意味します。

VPN サーバーは認証要求をルーティングするため、新しい Azure AD Multi-Factor Authentication 対応の NPS サーバーを認識する必要があります。

## <a name="prerequisites"></a>前提条件

NPS 拡張機能は、既存のインフラストラクチャで使用します。 開始する前に、以下の前提条件を確認してください。

### <a name="licenses"></a>ライセンス

Azure AD Multi-Factor Authentication の NPS 拡張機能は、[Azure AD Multi-Factor Authentication のライセンス](./concept-mfa-howitworks.md)をお持ちのお客様にご利用いただけます。 使用量ベースの Azure AD Multi-Factor Authentication のライセンス (ユーザーごと、認証ごとのライセンスなど) は、NPS 拡張機能に対応していません。

### <a name="software"></a>ソフトウェア

Windows Server 2012 またはそれ以降。

### <a name="libraries"></a>ライブラリ

次のライブラリを手動でインストールする必要があります。

- [Visual Studio 2015 の Visual C++ 再頒布可能パッケージ](https://www.microsoft.com/download/details.aspx?id=48145)

拡張機能を含め、次のライブラリが自動的にインストールされます。

- [Visual Studio 2013 (X64) の Visual C++ 再頒布可能パッケージ](https://www.microsoft.com/download/details.aspx?id=40784)
- [Windows PowerShell 用 Microsoft Azure Active Directory モジュール バージョン 1.1.166.0](https://www.powershellgallery.com/packages/MSOnline/1.1.166.0)

Windows PowerShell 用 Microsoft Azure Active Directory モジュールも、セットアップ プロセスの一環として実行する構成スクリプトによってインストールされます (モジュールがまだ存在しない場合)。 このモジュールをまだインストールしていない場合、事前にインストールする必要はありません。

### <a name="azure-active-directory"></a>Azure Active Directory

NPS 拡張機能を使用するすべてのユーザーが、Azure AD Connect を使用して Azure AD に同期され、MFA の対象として登録されている必要があります。

拡張機能をインストールするときは、Azure AD テナントの "*テナント ID*" と管理資格情報が必要です。 テナント ID を取得するには、次の手順のようにします。

1. Azure テナントの全体管理者として [Azure Portal](https://portal.azure.com) にサインインします。
1. **Azure Active Directory** を探して選択します。
1. **[概要]** ページに、"*テナント情報*" が表示されます。 次のスクリーンショットの例に示すように、 *[テナント ID]* の横にある **[コピー]** アイコンを選択します。

   ![Azure portal からのテナント ID の取得](./media/howto-mfa-nps-extension/azure-active-directory-tenant-id-portal.png)

### <a name="network-requirements"></a>ネットワークの要件

NPS サーバーは、ポート 80 および 443 を使って次の URL と通信できる必要があります。

* *https:\//adnotifications.windowsazure.com*
* *https:\//login.microsoftonline.com*
* *https:\//credentials.azure.com*

さらに、[指定された PowerShell スクリプトを使用してアダプターの設定](#run-the-powershell-script)を行うには、次の URL への接続が必要です。

* *https:\//login.microsoftonline.com*
* *https:\//provisioningapi.microsoftonline.com*
* *https:\//aadcdn.msauth.net*
* *https:\//www.powershellgallery.com*
* *https:\//aadcdn.msftauthimages.net*

## <a name="prepare-your-environment"></a>環境を準備する

NPS 拡張機能をインストールする前に、認証トラフィックを処理するように環境を準備してください。

### <a name="enable-the-nps-role-on-a-domain-joined-server"></a>ドメインに参加しているサーバーで NPS 役割を有効にする

NPS サーバーは、Azure AD に接続し、MFA 要求を認証します。 この役割に対して 1 台のサーバーを選択します。 NPS 拡張機能は RADIUS でないすべての要求に対してエラーをスローするため、他のサービスからの要求を処理しないサーバーを選択することをお勧めします。 NPS サーバーを、環境のプライマリおよびセカンダリ認証サーバーとしてセットアップする必要があります。 別のサーバーに RADIUS 要求をプロキシすることはできません。

1. サーバーで、**サーバー マネージャー** を開きます。 *[クイック スタート]* メニューから **[役割と機能の追加ウィザード]** を選択します。
2. インストールの種類として、 **[役割ベースまたは機能ベースのインストール]** を選択します。
3. **[ネットワーク ポリシーとアクセス サービス]** サーバーの役割を選択します。 ウィンドウがポップアップし、この役割を実行するために必要な追加機能が通知される場合があります。
4. *[確認]* ページまでウィザードを続行します。 準備ができたら、 **[インストール]** を選択します。

NPS サーバーの役割のインストールには数分かかる場合があります。 完了したら次のセクションに進み、VPN ソリューションからの受信 RADIUS 要求を処理するようにこのサーバーを構成します。

### <a name="configure-your-vpn-solution-to-communicate-with-the-nps-server"></a>NPS サーバーと通信するように VPN ソリューションを構成する

使用する VPN ソリューションに応じて、RADIUS 認証ポリシーを構成する手順は異なります。 RADIUS NPS サーバーをポイントするように VPN ポリシーを構成します。

### <a name="sync-domain-users-to-the-cloud"></a>クラウドにドメイン ユーザーを同期する

この手順は、テナントで既に完了している可能性がありますが、Azure AD Connect が最近データベースを同期させたことを再確認することをお勧めします。

1. [Azure Portal](https://portal.azure.com) に管理者としてサインインします。
2. **[Azure Active Directory]**  >  **[Azure AD Connect]** を選択します。
3. 同期の状態が **[有効]** であり、最後の同期が 1 時間以内であったことを確認します。

新しい同期を開始する必要がある場合は、「[Azure AD Connect 同期: スケジューラ](../hybrid/how-to-connect-sync-feature-scheduler.md#start-the-scheduler)」の手順を使用します。

### <a name="determine-which-authentication-methods-your-users-can-use"></a>ユーザーが使用できる認証方法を決定する

NPS 拡張機能のデプロイで使用できる認証方法に影響する 2 つの要素があります。

* RADIUS クライアント (VPN、Netscaler サーバーなど) と NPS サーバー間で使用されるパスワードの暗号化アルゴリズム。
   - **PAP** を使うと、クラウドでの Azure AD Multi-Factor Authentication のすべての認証方法 (電話、一方向テキスト メッセージ、モバイル アプリの通知、OATH ハードウェア トークン、モバイル アプリの確認コード) がサポートされます。
   - **CHAPV2** と **EAP** は、電話とモバイル アプリの通知をサポートします。

    > [!NOTE]
    > NPS 拡張機能をデプロイするときに、これらの要素を使用して、ユーザーに使用できる方法を評価します。 RADIUS クライアントは PAP をサポートしているが、クライアント UX に確認コードの入力フィールドがない場合は、サポートされるオプションは電話とモバイル アプリの通知の 2 つになります。
    >
    > また、使用されている認証プロトコル (PAP、CHAP、または EAP) に関係なく、MFA メソッドがテキストベース (SMS、モバイル アプリ確認コード、または OATH ハードウェア トークン) であり、ユーザーが VPN クライアントの UI 入力フィールドにコードまたはテキストを入力する必要がある場合は、認証が成功する可能性があります。 "*ただし*"、ネットワーク アクセス ポリシーで構成されている RADIUS 属性は、RADIUS クライアント (VPN ゲートウェイなどのネットワーク アクセス デバイス) へは転送 "*されません*"。 その結果、VPN クライアントが持つアクセス権は、必要以上に多くなったり、少なくなったり、なくなったりします。

* クライアント アプリケーション (VPN、Netscaler サーバーなど) が処理できる入力方式。 たとえば、VPN クライアントに、ユーザーがテキストまたはモバイル アプリから確認コードを入力できるようにするなんらかの手段があるかどうか。

Azure で[サポートされていない認証方法を無効にする](howto-mfa-mfasettings.md#verification-methods)ことができます。

### <a name="register-users-for-mfa"></a>ユーザーを MFA に登録する

NPS 拡張機能を展開して使用する前に、Azure AD Multi-Factor Authentication を実行する必要があるユーザーを MFA に登録する必要があります。 また、拡張機能をデプロイ時にテストするには、Azure AD Multi-Factor Authentication に対して完全に登録されている少なくとも 1 つのテスト アカウントが必要です。

テスト アカウントを作成して構成する必要がある場合は、次の手順を使用します。

1. テスト アカウントで [https://aka.ms/mfasetup](https://aka.ms/mfasetup) にサインインします。
2. 表示されたメッセージに従って、確認方法を設定します。
3. Azure portal で管理者ユーザーとしてテスト アカウントに対して多要素認証を要求するには、[条件付きアクセス ポリシーを作成](howto-mfa-getstarted.md#create-conditional-access-policy)します。

> [!IMPORTANT]
>
> ユーザーが Azure AD Multi-Factor Authentication に正常に登録していることを確認してください。 ユーザーが以前にセルフサービス パスワード リセット (SSPR) のみに登録している場合は、そのアカウントに対して *StrongAuthenticationMethods* が有効になります。 ユーザーが SSPR のみに登録した場合でも、*StrongAuthenticationMethods* が構成されている場合は、Azure AD Multi-Factor Authentication が適用されます。
>
> SSPR と Azure AD Multi-Factor Authentication を同時に構成する、統合されたセキュリティ登録を有効にすることができます。 詳細については、「[Azure Active Directory での統合されたセキュリティ情報の登録の有効化](howto-registration-mfa-sspr-combined.md)」を参照してください。
>
> 以前に SSPR のみを有効にした場合は、[認証方法を再登録するようユーザーに強制する](howto-mfa-userdevicesettings.md#manage-user-authentication-options)こともできます。

## <a name="install-the-nps-extension"></a>NPS 拡張機能のインストール

> [!IMPORTANT]
> VPN アクセス ポイントとは異なるサーバーに NPS 拡張機能をインストールします。

### <a name="download-and-install-the-nps-extension-for-azure-ad-mfa"></a>Azure AD MFA の NPS 拡張機能をダウンロードしてインストールする

NPS 拡張機能をダウンロードしてインストールするには、次の手順を実行します。

1. Microsoft ダウンロード センターから [NPS 拡張機能をダウンロード](https://aka.ms/npsmfa)します。
1. 構成するネットワーク ポリシー サーバーにバイナリをコピーします。
1. *setup.exe* を実行してインストールの指示に従います。 エラーが発生した場合は、[前提条件のセクションのライブラリ](#libraries)が正常にインストールされていることを確認してください。

#### <a name="upgrade-the-nps-extension"></a>NPS 拡張機能のアップグレード

既存の NPS 拡張機能のインストールを後からアップグレードする場合は、基になるサーバーの再起動を回避するために、次の手順を行います。

1. 既存のバージョンをアンインストールする。
1. 新しいインストーラーを実行する。
1. *ネットワーク ポリシー サーバー (IAS)* サービスを再起動する。

### <a name="run-the-powershell-script"></a>PowerShell スクリプトの実行

インストーラーによって、`C:\Program Files\Microsoft\AzureMfa\Config` (`C:\` はインストール先のドライブ) に PowerShell スクリプトが作成されます。 この PowerShell スクリプトは、実行されるたびに次のアクションを実行します。

* 自己署名証明書を作成する。
* Azure AD のサービス プリンシパルに証明書の公開キーを関連付ける。
* ローカル コンピューターの証明書ストアに証明書を格納する。
* ネットワーク ユーザーに証明書の秘密キーへのアクセスを許可する。
* NPS サービスを再起動する。

(PowerShell スクリプトで生成される自己署名証明書ではなく) 独自の証明書を使用する場合を除き、PowerShell スクリプトを実行して NPS 拡張機能のインストールを完了します。 複数のサーバーに拡張機能をインストールする場合は、それぞれのサーバーに独自の証明書が必要です。

負荷分散機能または冗長性を提供するには、必要に応じて、追加の NPS サーバーで次の手順を繰り返します。

1. 管理者として Windows PowerShell プロンプトを開きます。
1. インストーラーによって PowerShell スクリプトが作成されたディレクトリに移動します。

   ```powershell
   cd "C:\Program Files\Microsoft\AzureMfa\Config"
   ```

1. インストーラーによって作成された PowerShell スクリプトを実行します。

   PowerShell を正常に接続してパッケージをダウンロードできるようにするには、まず TLS 1.2 を有効にする必要がある可能性があります。
   
   `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12`

   > [!IMPORTANT]
   > Azure Government クラウドまたは Azure China 21Vianet クラウドを使用しているお客様の場合は、最初に *AzureMfaNpsExtnConfigSetup.ps1* スクリプトで `Connect-MsolService` コマンドレットを編集して、必要なクラウドの *AzureEnvironment* パラメーターを含めます。 たとえば、 *-AzureEnvironment USGovernment* または *--AzureEnvironment AzureChinaCloud* を指定します。
   >
   > 詳細については、[Connect-MsolService パラメーターのリファレンス](/powershell/module/msonline/connect-msolservice#parameters)を参照してください。

   ```powershell
   .\AzureMfaNpsExtnConfigSetup.ps1
   ```

1. プロンプトが表示されたら、管理者として Azure AD にログインします。
1. PowerShell によって、テナント ID の入力が求められます。 前提条件のセクションで Azure portal からコピーした "*テナント ID*" の GUID を使用します。
1. スクリプトが終了すると、成功メッセージが表示されます。  

以前のコンピューター証明書が期限切れになり、新しい証明書が生成された場合は、期限切れの証明書をすべて削除する必要があります。 期限切れの証明書があると、NPS 拡張機能の起動で問題が生じる可能性があります。

> [!NOTE]
> PowerShell スクリプトを使用して証明書を生成するのではなく独自の証明書を使用する場合は、NPS 名前付け規則に合わせてください。 サブジェクト名は **CN=\<TenantID\>,OU=Microsoft NPS Extension** にする必要があります。

### <a name="microsoft-azure-government-or-azure-china-21vianet-additional-steps"></a>Microsoft Azure Government または Azure China 21Vianet の場合の追加手順

Azure Government クラウドまたは Azure China 21Vianet クラウドを使用しているお客様については、各 NPS サーバーで次の追加の構成手順を実行する必要があります。

> [!IMPORTANT]
> これらのレジストリ設定の構成は、Azure Government または Azure China 21Vianet を利用している場合のみ行ってください。

1. Azure Government または Azure China 21Vianet を利用している場合、NPS サーバーで **レジストリ エディター** を開きます。
1. `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa` に移動します。
1. Azure Government のお客様の場合は、次のキー値を設定します。

    | レジストリ キー       | 値 |
    |--------------------|-----------------------------------|
    | AZURE_MFA_HOSTNAME | adnotifications.windowsazure.us   |
    | STS_URL            | https://login.microsoftonline.us/ |

1. Azure China 21Vianet のお客様の場合は、次のキー値を設定します。

    | レジストリ キー       | 値 |
    |--------------------|-----------------------------------|
    | AZURE_MFA_HOSTNAME | adnotifications.windowsazure.cn   |
    | STS_URL            | https://login.chinacloudapi.cn/   |

1. 前の 2 つの手順を繰り返して、各 NPS サーバーのレジストリ キーの値を設定します。
1. NPS サーバーごとに NPS サービスを再起動します。

    影響を最小限に抑えるには、各 NPS サーバーを 1 つずつ NLB ローテーションから外し、すべての接続がドレインされるのを待ちます。

### <a name="certificate-rollover"></a>証明書のロールオーバー

NPS 拡張機能のリリース *1.0.1.32* では、複数の証明書の読み取りがサポートされるようになりました。 この機能により、証明書の有効期限が切れる前のローリング アップデートが容易になります。 組織で以前のバージョンの NPS 拡張機能を実行している場合は、バージョン *1.0.1.32* 以降にアップグレードしてください。

`AzureMfaNpsExtnConfigSetup.ps1` スクリプトによって作成された証明書は、2 年間有効です。 証明書の有効期限を監視します。 NPS 拡張機能の証明書は、"*ローカル コンピューター*" の証明書ストアの *[個人用]* に配置され、インストール スクリプトに提供されるテナント ID に対して "*発行*" されます。

証明書の有効期限が近づいている場合は、置き換えるための新しい証明書を作成する必要があります。  このプロセスを完了するには、`AzureMfaNpsExtnConfigSetup.ps1` を再度実行し、メッセージが表示されたら同じテナント ID を指定します。 このプロセスは、環境内の各 NPS サーバーで繰り返す必要があります。

## <a name="configure-your-nps-extension"></a>NPS 拡張機能の構成

環境を準備し、必要なサーバーに NPS 拡張機能をインストールしたら、拡張機能を構成できます。

このセクションでは、NPS 拡張機能を正常にデプロイするために必要な設計上の考慮事項と提案を示します。

### <a name="configuration-limitations"></a>構成の制限

- Azure AD Multi-Factor Authentication の NPS 拡張機能には、MFA サーバーからクラウドにユーザーと設定を移行するためのツールは含まれません。 このため、既存のデプロイではなく、新しいデプロイに拡張機能を使用することをお勧めします。 既存のデプロイで拡張機能を使用する場合、ユーザーはクラウドに MFA の詳細を設定するために、再度セキュリティ確認を実行する必要があります。  
- NPS 拡張機能は、オンプレミスの AD DS 環境の UPN を使用して Azure AD Multi-Factor Authentication のユーザーを識別し、セカンダリ認証を行います。代替ログイン ID やカスタム AD DS フィールドなど、UPN 以外の識別子を使用するように NPS 拡張機能を構成することができます。 詳細については、[Microsoft Multi-Factor Authentication のための NPS 拡張機能の詳細構成オプション](howto-mfa-nps-extension-advanced.md)に関するページをご覧ください。
- すべての暗号化プロトコルで、すべての検証メソッドがサポートされるわけではありません。
   - **PAP** は、電話、テキスト メッセージ、モバイル アプリの通知、およびモバイル アプリの確認コードをサポートします。
   - **CHAPV2** と **EAP** は、電話とモバイル アプリの通知をサポートします。

### <a name="control-radius-clients-that-require-mfa"></a>MFA を必須とする RADIUS クライアントの制御

NPS 拡張機能を使用して RADIUS クライアントに対して MFA を有効にすると、このクライアントに対するすべての認証で MFA の実行が必須になります。 一部の RADIUS クライアントに対してのみ MFA を有効にする場合は、2 つの NPS サーバーを構成し、そのうちの一方に拡張機能をインストールします。

MFA を必須とする RADIUS クライアントについては、拡張機能が構成された NPS サーバーに要求を送信するよう構成し、他の RADIUS クライアントについては、拡張機能が構成されていない NPS サーバーに要求を送信するよう構成します。

### <a name="prepare-for-users-that-arent-enrolled-for-mfa"></a>MFA に登録されていないユーザーのための準備

MFA に登録されていないユーザーがいる場合は、そのユーザーが認証しようとしたときの動作を決める必要があります。 この動作を制御するには、レジストリ パス *HKLM\Software\Microsoft\AzureMFA* にある設定 *REQUIRE_USER_MATCH* を使用します。 この設定の構成オプションは 1 つだけです。

| Key | 値 | Default |
| --- | ----- | ------- |
| REQUIRE_USER_MATCH | TRUE または FALSE | 未設定 (TRUE に相当) |

この設定により、ユーザーが MFA に登録されていない場合の処理方法が決まります。 キーが存在しないか、設定されていないか、または *TRUE* に設定されていて、ユーザーが登録されていない場合は、拡張機能による MFA チャレンジが失敗します。

キーが *FALSE* に設定されていて、ユーザーが登録されていない場合は、MFA を実行することがなく認証が行われます。 ユーザーが MFA に登録されている場合、*REQUIRE_USER_MATCH* が *FALSE* に設定されている場合でも、ユーザーは MFA で認証する必要があります。

ユーザーのオンボーディング中、Azure AD Multi-Factor Authentication への登録の一部がまだ完了しないうちに、このキーを作成して *FALSE* に設定できます。 ただし、MFA に登録されていないユーザーのサインインが許可されることになるため、このキーは運用環境に移行する前に削除してください。

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="nps-extension-health-check-script"></a>NPS 拡張機能の正常性チェック スクリプト

次のスクリプトは、NPS 拡張機能のトラブルシューティングを行うときに、基本的な正常性チェックの手順を実行するために使用できます。

[MFA_NPS_Troubleshooter.ps1](/samples/azure-samples/azure-mfa-nps-extension-health-check/azure-mfa-nps-extension-health-check/)

### <a name="how-do-i-verify-that-the-client-cert-is-installed-as-expected"></a>クライアント証明書が正常にインストールされていることを確認するにはどうすればよいですか。

インストーラーによって証明書ストア内に作成された自己署名証明書を探して、ユーザー *NETWORK SERVICE* が秘密キーへのアクセスを許可されていることを確認します。 この証明書のサブジェクト名は **CN \<tenantid\>, OU = Microsoft NPS Extension** になります

`AzureMfaNpsExtnConfigSetup.ps1` スクリプトによって生成された自己署名証明書の有効期間は 2 年間です。 証明書がインストールされていることを確認するときは、証明書の有効期限が切れていないことも確認する必要があります。

### <a name="how-can-i-verify-that-my-client-certificate-is-associated-to-my-tenant-in-azure-ad"></a>クライアント証明書が Azure AD のテナントに関連付けられていることを確認するにはどうすればよいですか。

PowerShell コマンド プロンプトを開き、次のコマンドを実行します。

```powershell
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1
```

このコマンドは、テナントと NPS 拡張機能のインスタンスを関連付けているすべての証明書を PowerShell セッションに出力します。 クライアント証明書を *Base-64 encoded X.509(.cer)* ファイルとして秘密キーなしでエクスポートし、PowerShell が出力したリストと比較します。

次のコマンドによって、名前が *npscertificate*、形式が *.cer* のファイルが *C:* ドライブのルートに作成されます。

```powershell
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1 | select -ExpandProperty "value" | out-file c:\npscertificate.cer
```

このコマンドを実行したら、*C:* ドライブのルートに移動して、そのファイルを見つけてダブルクリックします。 [詳細] に移動して、[サムプリント] までスクロール ダウンします。 サーバーにインストールされている証明書のサムプリントとこれを比較します。 証明書のサムプリントが一致する必要があります。

複数の証明書が返されている場合は、判読できる形式のタイムスタンプ *Valid-From* と *Valid-Until* を使用して、明らかに無関係な証明書を除外することができます。

### <a name="why-cannot-i-sign-in"></a>サインインできないのはなぜですか。

パスワードの有効期限が切れていないことを確認します。 NPS 拡張機能では、サインイン ワークフローの中でパスワードを変更することはできません。 ご自分の組織の IT スタッフに連絡してサポートを依頼してください。

### <a name="why-are-my-requests-failing-with-adal-token-error"></a>要求が ADAL トークン エラーで失敗するのはなぜですか。

このエラーにはいくつかの原因が考えられます。 次の手順に従ってトラブルシューティングを行います。

1. NPS サーバーを再起動します。
2. クライアント証明書が正常にインストールされていることを確認します。
3. 証明書が Azure AD のテナントに関連付けられていることを確認します。
4. 拡張機能を実行しているサーバーから `https://login.microsoftonline.com/` にアクセスできることを確認します。

### <a name="why-does-authentication-fail-with-an-error-in-http-logs-stating-that-the-user-is-not-found"></a>HTTP ログにユーザーが見つからないというエラーが記録され、認証が失敗するのはなぜですか。

AD Connect が実行していること、およびユーザーがオンプレミスの AD DS 環境と Azure AD の両方に存在していることを確認します。

### <a name="why-do-i-see-http-connect-errors-in-logs-with-all-my-authentications-failing"></a>すべての認証が失敗し、ログに HTTP 接続エラーが記録されるのはなぜですか。

NPS 拡張機能を実行しているサーバーから https://adnotifications.windowsazure.com に到達可能であることを確認します。

### <a name="why-is-authentication-not-working-despite-a-valid-certificate-being-present"></a>有効な証明書があるにもかかわらず認証が機能しないのはなぜですか。

以前のコンピューター証明書が期限切れになり、新しい証明書が生成された場合は、期限切れの証明書をすべて削除します。 期限切れの証明書があると、NPS 拡張機能の起動で問題が生じる可能性があります。

有効な証明書があるかどうかを確認するには、MMC を使用してローカルの "*コンピューター アカウントの証明書ストア*" をチェックし、その証明書が有効期限を過ぎていないことを確認してください。 有効な証明書を新しく生成するには、「[PowerShell スクリプトの実行](#run-the-powershell-script)」の手順を再度行います。

### <a name="why-do-i-see-discarded-requests-in-the-nps-server-logs"></a>NPS サーバーのログに破棄された要求があるのはなぜですか。

タイムアウト値が小さすぎる場合、VPN サーバーは NPS サーバーに対して繰り返し要求を送信することがあります。 これらの複製要求は NPS サーバーによって検出され、破棄されます。 この動作は仕様どおりであり、NPS サーバーまたは Azure AD Multi-Factor Authentication の NPS 拡張機能に問題があることを示すものではありません。

NPS サーバーのログに破棄されたパケットがある理由の詳細については、この記事の冒頭にある「[RADIUS プロトコルの動作と NPS 拡張機能](#radius-protocol-behavior-and-the-nps-extension)」を参照してください。

## <a name="managing-the-tlsssl-protocols-and-cipher-suites"></a>TLS/SSL プロトコルと暗号スイートの管理

組織で求められない限り、以前の強度の低い暗号スイートを無効にするか、削除することをお勧めします。 このタスクを完了する方法については、「[AD FS の SSL/TLS プロトコルおよび暗号スイートの管理](/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs)」を参照してください。

### <a name="additional-troubleshooting"></a>その他のトラブルシューティング

その他のトラブルシューティングのガイダンスと可能なソリューションについては、「[Azure AD Multi-Factor Authentication の NPS 拡張機能からのエラー メッセージを解決する](howto-mfa-nps-extension-errors.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

- [Windows Server のネットワーク ポリシー サーバーの概要と構成](/windows-server/networking/technologies/nps/nps-top)

- ログインに別の ID を設定するか、「[Advanced configuration options for the NPS extension for Multi-Factor Authentication](howto-mfa-nps-extension-advanced.md)」 (Multi-Factor Authentication の NPS 拡張機能の高度な構成オプション) の 2 段階認証を実行しない IP の例外リストを設定する

- NPS 拡張機能を使用して[リモート デスクトップ ゲートウェイ](howto-mfa-nps-extension-rdg.md)と [VPN サーバー](howto-mfa-nps-extension-vpn.md)を統合する方法を学習する

- [Azure AD Multi-Factor Authentication の NPS 拡張機能からのエラー メッセージを解決する](howto-mfa-nps-extension-errors.md)
