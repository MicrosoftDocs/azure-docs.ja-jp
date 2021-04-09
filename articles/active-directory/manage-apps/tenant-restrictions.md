---
title: テナント制限を使用して SaaS アプリへのアクセスを管理する - Azure AD
description: テナント制限を使用して、どのユーザーが自分の Azure AD テナントに基づいてアプリにアクセスできるかを管理する方法。
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 2/23/2021
ms.author: kenwith
ms.reviewer: hpsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa025f7e21f76b4dde547ccabf675511e9156359
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104589329"
---
# <a name="use-tenant-restrictions-to-manage-access-to-saas-cloud-applications"></a>テナント制限を使用して SaaS クラウド アプリケーションへのアクセスを管理する

セキュリティを重視する大規模な組織は、Microsoft 365 などのクラウド サービスへの移行を望んでいますが、ユーザーが承認済みリソースにしかアクセスできないことを認識しておく必要があります。 従来より、企業ではアクセスを管理するときにドメイン名や IP アドレスを制限しています。 この方法は、SaaS (サービスとしてのソフトウェア) アプリがパブリック クラウドでホストされ、[outlook.office.com](https://outlook.office.com/) や [login.microsoftonline.com](https://login.microsoftonline.com/) などの共有ドメイン名で実行されている環境では失敗します。 これらのアドレスをブロックすると、ユーザーを承認済みの ID やリソースに単に制限するのではなく、ユーザーは Web 上の Outlook にまったくアクセスできなくなります。

この課題を解決する Azure Active Directory (Azure AD) ソリューションが、テナント制限と呼ばれる機能です。 テナント制限を使用すると、組織は、アプリケーションがシングル サインオンに使用する Azure AD テナントに基づいて SaaS クラウド アプリケーションへのアクセスを制御できます。 たとえば、自分の組織の Microsoft 365 アプリケーションへのアクセスは許可し、これらの同じアプリケーションの他の組織のインスタンスにはアクセスできないようにすることが可能です。  

テナント制限では、組織はユーザーがアクセスを許可されているテナントの一覧を指定できます。 Azure AD は、これらの許可されているテナントへのアクセスだけを許可します。

この記事では Microsoft 365 のテナント制限に重点を置いていますが、この機能は、シングル サインオンのためにユーザーを Azure AD に送信するすべてのアプリを保護します。 Microsoft 365 で使用される Azure AD テナントとは異なるテナントで SaaS アプリを使用する場合は、必要なすべてのテナントが許可されていることをご確認ください (たとえば、B2B コラボレーション シナリオ内など)。 SaaS クラウド アプリの詳細については、[Active Directory Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps) をご覧ください。

さらに、テナント制限機能では、OneDrive、Hotmail、Xbox.com などの[すべての Microsoft コンシューマー アプリケーション (MSA アプリ) の使用をブロック](#blocking-consumer-applications-public-preview)できるようになりました。  これは、`login.live.com` エンドポイントに対して別のヘッダーを使用します。これについては、ドキュメントの最後で詳細に説明します。

## <a name="how-it-works"></a>しくみ

全体的なソリューションは、次のコンポーネントで構成されます。

1. **Azure AD**:`Restrict-Access-To-Tenants: <permitted tenant list>` ヘッダーが存在する場合、Azure AD からは許可されているテナントのセキュリティ トークンのみが発行されます。

2. **オンプレミスのプロキシ サーバー インフラストラクチャ**: このインフラストラクチャは、トランスポート層セキュリティ (TLS) 検査に対応したプロキシ デバイスです。 許可されているテナントのリストを含むヘッダーを Azure AD 宛てのトラフィックに挿入するようにプロキシを構成する必要があります。

3. **クライアント ソフトウェア**: テナント制限をサポートするには、プロキシ インフラストラクチャがトラフィックをインターセプトできるように、クライアント ソフトウェアはトークンを直接 Azure AD に要求する必要があります。 先進認証 (OAuth 2.0 など) を使用する Office クライアントと同様に、ブラウザー ベースの Microsoft 365 アプリケーションは現在、テナント制限をサポートしています。

4. **先進認証**: テナント制限を使用し、許可されていないすべてのテナントへのアクセスをブロックするには、クラウド サービスは先進認証を使用する必要があります。 既定で先進認証プロトコルを使用するように Microsoft 365 クラウド サービスを構成する必要があります。 Microsoft 365 による最新の認証のサポートに関する最新情報については、[Office 365 の最新の認証の更新](https://www.microsoft.com/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/)に関するページをご覧ください。

次の図は、おおまかなトラフィック フローを示しています。 テナント制限では、TLS 検査は Microsoft 365 クラウド サービスではなく、Azure AD へのトラフィック上でのみ必要です。 Azure AD への認証のためのトラフィック量は一般に、Exchange Online や SharePoint Online などの SaaS アプリケーションへのトラフィック量よりはるかに少ないため、この区別が重要です。

![テナント制限のトラフィック フロー - 図](./media/tenant-restrictions/traffic-flow.png)

## <a name="set-up-tenant-restrictions"></a>テナント制限を設定する

テナント制限の使用を開始するための手順は 2 つあります。 最初に、クライアントが適切なアドレスに接続できることを確認します。 2 つ目に、プロキシ インフラストラクチャを構成します。

### <a name="urls-and-ip-addresses"></a>URL と IP アドレス

テナント制限を使用するには、クライアントは、認証のために [login.microsoftonline.com](https://login.microsoftonline.com/)、[login.microsoft.com](https://login.microsoft.com/)、および [login.windows.net](https://login.windows.net/) の Azure AD URL に接続できる必要があります。 さらに、Office 365 にアクセスするために、クライアントは「[Office 365 の URL と IP アドレスの範囲](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)」で定義されている完全修飾ドメイン名 (FQDN)、URL、および IP アドレスにも接続できる必要があります。 

### <a name="proxy-configuration-and-requirements"></a>プロキシの構成と要件

プロキシ インフラストラクチャを使用したテナント制限を有効にするには、次の構成が必要です。 このガイダンスは一般的なものであるため、具体的な実装手順については、プロキシ ベンダーのドキュメントを参照してください。

#### <a name="prerequisites"></a>前提条件

- プロキシは、TLS インターセプト、HTTP ヘッダーの挿入、FQDN/URL を使用した送信先のフィルター処理を実行できる必要があります。

- クライアントは、TLS 通信でプロキシによって提示される証明書チェーンを信頼する必要があります。 たとえば、内部[公開キー インフラストラクチャ (PKI)](/windows/desktop/seccertenroll/public-key-infrastructure) からの証明書が使用されている場合は、内部発行のルート証明機関証明書を信頼する必要があります。

- テナント制限を使用するには、Azure AD Premium 1 のライセンスが必要です。

#### <a name="configuration"></a>構成

login.microsoftonline.com、login.microsoft.com、login.windows.net への各送信要求では、*Restrict-Access-To-Tenants* と *Restrict-Access-Context* の 2 つの HTTP ヘッダーを挿入します。

> [!NOTE]
> `*.login.microsoftonline.com` の下のサブドメインをプロキシの構成に含めないでください。 そうすると、device.login.microsoftonline.com が含まれ、デバイスの登録とデバイスベースの条件付きアクセスのシナリオで使用されるクライアント証明書の認証が妨げられます。 TLS の中断と検査およびヘッダーの挿入から device.login.microsoftonline.com を除外するようにプロキシ サーバーを構成します。

これらのヘッダーには、次の要素を含める必要があります。

- *Restrict-Access-To-Tenants* には、ユーザーにアクセスを許可するテナントのコンマ区切りリストである、\<permitted tenant list\> の値を使用します。 テナントに登録されているドメインを使用して、このリストのテナントとディレクトリ ID 自体を識別できます。 テナントを記述する 3 つのすべての方法の例として、Contoso、Fabrikam、および Microsoft を許可する名前と値のペアは、`Restrict-Access-To-Tenants: contoso.com,fabrikam.onmicrosoft.com,72f988bf-86f1-41af-91ab-2d7cd011db47` のようになります。

- *Restrict-Access-Context* には、どのテナントでテナント制限を設定するかを宣言している、1 つのディレクトリ ID の値を使用します。 たとえば、テナント制限ポリシーを設定するテナントとして Contoso を宣言するには、名前と値のペアは `Restrict-Access-Context: 456ff232-35l2-5h23-b3b3-3236w0826f3d` のようになります。  これらの認証のログを取得するには、このスポットで独自のディレクトリ ID を使用する **必要があります**。

> [!TIP]
> ディレクトリ ID は、[Azure Active Directory ポータル](https://aad.portal.azure.com/)で見つけることができます。 管理者としてサインインし、 **[Azure Active Directory]** を選択して、 **[プロパティ]** を選択します。 
>
> ディレクトリ ID またはドメイン名が同じテナントを参照していることを検証するには、URL `https://login.microsoftonline.com/<tenant>/v2.0/.well-known/openid-configuration` で <tenant> の代わりにその ID またはドメインを使用します。  ドメインと ID の結果が同じであれば、同じテナントを参照しています。 

ユーザーが未承認のテナントを含む独自の HTTP ヘッダーを挿入できないようにするために、受信要求に *Restrict-Access-To-Tenants* ヘッダーが既に存在する場合、プロキシはそのヘッダーを置き換える必要があります。

login.microsoftonline.com、login.microsoft.com、login.windows.net へのすべての要求にプロキシを使用することをクライアントに強制する必要があります。 たとえば、クライアントにプロキシの使用を指示するために PAC ファイルが使用されている場合は、エンド ユーザーがその PAC ファイルを編集したり無効にしたりできないようにする必要があります。

## <a name="the-user-experience"></a>ユーザー エクスペリエンス

このセクションでは、エンド ユーザーと管理者の両方のエクスペリエンスについて説明します。

### <a name="end-user-experience"></a>エンド ユーザー エクスペリエンス

たとえば、Contoso ネットワーク上のユーザーが、Outlook Online などの共有 SaaS アプリケーションの Fabrikam インスタンスにアクセスしようとしているとします。 Fabrikam が Contoso インスタンスの許可されていないテナントである場合、ユーザーには、IT 部門の承認のない組織に属するリソースにアクセスしようとしていることを示すアクセス拒否メッセージが表示されます。

### <a name="admin-experience"></a>管理者エクスペリエンス

テナント制限の構成は企業プロキシ インフラストラクチャで実行されますが、管理者は、直接 Azure Portal でテナント制限レポートにアクセスできます。 レポートを表示するには、次の操作を行います。

1. [Azure Active Directory ポータル](https://aad.portal.azure.com/)にサインインします。 **Azure Active Directory 管理センター** ダッシュボードが表示されます。

2. 左ウィンドウで、 **[Azure Active Directory]** を選択します。 [Azure Active Directory] 概要ページが表示されます。

3. [概要] ページで、 **[テナントの制限]** を選択します。

Restricted-Access-Context テナントとして指定されたテナントの管理者は、このレポートを使用して、テナント制限ポリシーのためにブロックされたサインイン (使用された ID やターゲット ディレクトリ ID を含む) を確認できます。 制限を設定するテナントがサインインのユーザー テナントまたはリソース テナントのいずれかである場合は、サインインが含まれます。

Restricted-Access-Context テナント以外のテナントに属するユーザーがサインインする場合、ターゲット ディレクトリ ID などの限られた情報がレポートに含まれる可能性があります。 この場合、名前やユーザー プリンシパル名などのユーザー識別情報はマスクされ、他のテナントのユーザー データは保護されます (必要に応じて、ユーザー名とオブジェクト ID の代わりに "{PII Removed}@domain.com" または 00000000-0000-0000-0000-000000000000)。 

Azure Portal の他のレポートと同様に、フィルターを使用してレポートの範囲を指定できます。 特定の時間間隔、ユーザー、アプリケーション、クライアント、または状態についてフィルター処理できます。 **[列]** ボタンを選択すると、次のフィールドの任意の組み合わせでデータを表示することを選択できます。

- **ユーザー** - このフィールドでは、個人を特定できる情報を削除することができます。これは、`00000000-0000-0000-0000-000000000000` に設定されます。 
- **Application**
- **状態**
- **Date**
- **日付 (UTC)** - UTC は協定世界時
- **IP アドレス**
- **Client**
- **ユーザー名** - このフィールドでは、個人を特定できる情報を削除することができます。これは、`{PII Removed}@domain.com` に設定されます
- **場所**
- **ターゲット テナント ID**

## <a name="microsoft-365-support"></a>Microsoft 365 サポート

テナント制限を完全にサポートするには、Microsoft 365 アプリケーションは次の 2 つの条件を満たす必要があります。

1. 使用されるクライアントが先進認証をサポートしている。
2. クラウド サービスの既定の認証プロトコルとして最新の認証が有効になっている。

最新の認証を現在サポートしている Office クライアントに関する最新情報については、「[Updated Office 365 modern authentication (Office 365 の最新の認証の更新)](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/)」をご覧ください。 このページには、特定の Exchange Online テナントと Skype for Business Online テナントで最新の認証を有効にする手順へのリンクも含まれています。 SharePoint Online では、先進認証が既定で有効になっています。

Microsoft 365 ブラウザー ベースのアプリケーション (Office ポータル、Yammer、SharePoint サイト、Outlook on the Web など) は現在、テナント制限をサポートしています。 シック クライアント (Outlook、Skype for Business、Word、Excel、PowerPoint など) は、先進認証を使用している場合にのみテナント制限を適用できます。  

先進認証をサポートする Outlook および Skype for Business クライアントは、先進認証が有効になっていないテナントに対して従来のプロトコルを引き続き使用できる可能性があるため、テナント制限を実質的に迂回します。 テナント制限では、従来のプロトコルを使用するアプリケーションが認証中に login.microsoftonline.com、login.microsoft.com、または login.windows.net に接続する場合、それらのアプリケーションがブロックされる可能性があります。

Windows 上の Outlook の場合、エンド ユーザーが未承認の電子メール アカウントをプロファイルに追加できないようにする制限を実装することもできます。 例については、[既定以外の Exchange アカウントを追加できないようにする](https://gpsearch.azurewebsites.net/default.aspx?ref=1)グループ ポリシー設定に関するページをご覧ください。

## <a name="testing"></a>テスト

テナント制限を組織全体で実装する前にテストする必要がある場合は、Fiddler などのツールを使用したホスト ベースのアプローチと、プロキシ設定の段階的なロールアウトの 2 つのオプションがあります。

### <a name="fiddler-for-a-host-based-approach"></a>Fiddler を使用したホスト ベースのアプローチ

Fiddler は無料の Web デバッグ プロキシです。Fiddler を使用して HTTP/HTTPS トラフィックをキャプチャし、トラフィックを変更 (HTTP ヘッダーの挿入など) できます。 Fiddler を構成してテナント制限をテストするには、次の手順を実行します。

1. [Fiddler をダウンロードしてインストール](https://www.telerik.com/fiddler)します。

2. [Fiddler のヘルプ ドキュメント](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS)に従って、HTTPS トラフィックを復号化するように Fiddler を構成します。

3. カスタム ルールを使用して、*Restrict-Access-To-Tenants* と *Restrict-Access-Context* の各ヘッダーを挿入するように Fiddler を構成します。

   1. Fiddler Web Debugger ツールで、 **[Rules]** メニューを選択し、 **[Customize Rules…]** を選択して CustomRules ファイルを開きます。

   2. `OnBeforeRequest` 関数の先頭に次の行を追加します。 \<List of tenant identifiers\> をテナントに登録されているドメイン (`contoso.onmicrosoft.com` など) に置き換えます。 \<directory ID\> を、テナントの Azure AD GUID 識別子に置き換えます。  テナントでログ表示するには、正しい GUID 識別子を含める **必要があります**。 

   ```JScript.NET
    // Allows access to the listed tenants.
      if (
          oSession.HostnameIs("login.microsoftonline.com") ||
          oSession.HostnameIs("login.microsoft.com") ||
          oSession.HostnameIs("login.windows.net")
      )
      {
          oSession.oRequest["Restrict-Access-To-Tenants"] = "<List of tenant identifiers>";
          oSession.oRequest["Restrict-Access-Context"] = "<Your directory ID>";
      }

    // Blocks access to consumer apps
      if (
          oSession.HostnameIs("login.live.com")
      )
      {
          oSession.oRequest["sec-Restrict-Tenant-Access-Policy"] = "restrict-msa";
      }
   ```

   複数のテナントを許可する必要がある場合は、テナント名をコンマで区切ります。 次に例を示します。

   `oSession.oRequest["Restrict-Access-To-Tenants"] = "contoso.onmicrosoft.com,fabrikam.onmicrosoft.com";`

4. CustomRules ファイルを保存して閉じます。

Fiddler を構成したら、 **[File]** メニューに移動し、 **[Capture Traffic]** を選択することでトラフィックをキャプチャできます。

### <a name="staged-rollout-of-proxy-settings"></a>プロキシ設定の段階的なロールアウト

プロキシ インフラストラクチャの機能によっては、設定をユーザーに段階的にロールアウトできる場合があります。 考慮すべき高度なオプションは次のとおりです。

1. PAC ファイルを使用して、テスト ユーザーがテスト用プロキシ インフラストラクチャを使用するようにし、通常のユーザーは運用環境のプロキシ インフラストラクチャを引き続き使用します。
2. 場合によっては、一部のプロキシ サーバーでグループを使用して別の構成をサポートします。

具体的な詳細については、ご使用のプロキシ サーバーのドキュメントを参照してください。

## <a name="blocking-consumer-applications-public-preview"></a>コンシューマー アプリケーションのブロック (パブリック プレビュー)

[OneDrive](https://onedrive.live.com/) または [Microsoft Learn](/learn/) など、コンシューマー アカウントと組織アカウントの両方をサポートする Microsoft のアプリケーションは、同じ URL でホストされる場合があります。  これは、仕事の目的でその URL にアクセスする必要があるユーザーは、個人的な使用のためにもアクセスできることを意味しますが、運用上のガイドラインでは、許可されない場合があります。

一部の組織では、個人用アカウントの認証をブロックするために `login.live.com` をブロックして、これを解決しようとします。  これには、いくつかの欠点があります。

1. `login.live.com` をブロックすると、B2B ゲストシナリオでの個人アカウントの使用がブロックされ、訪問者やコラボレーションに侵入する場合があります。
1. 展開するには、[オートパイロットで `login.live.com` を使用する必要があります](/mem/autopilot/networking-requirements)。 `login.live.com` がブロックされると、Intune およびオートパイロットのシナリオは失敗するおそれがあります。
1. デバイス ID を login.live.com サービスに依存する組織のテレメトリと Windows 更新プログラムは[機能しなくなります](/windows/deployment/update/windows-update-troubleshooting#feature-updates-are-not-being-offered-while-other-updates-are)。

### <a name="configuration-for-consumer-apps"></a>コンシューマー アプリの構成

`Restrict-Access-To-Tenants` ヘッダーは許可リストとして機能しますが、Microsoft アカウント (MSA) ブロックは拒否シグナルとして機能し、ユーザーがコンシューマー アプリケーションにサインインできないように Microsoft アカウント プラットフォームに指示します。 このシグナルを送信するために、[上記](#proxy-configuration-and-requirements)と同じ企業プロキシまたはファイアウォールを使用して `login.live.com` にアクセスするトラフィックに `sec-Restrict-Tenant-Access-Policy` ヘッダーが挿入されます。 ヘッダーの値は、`restrict-msa` である必要があります。 このヘッダーが存在し、コンシューマー アプリがユーザーに直接サインインしようとすると、そのサインインはブロックされます。

現時点では、login.live.com は Azure AD とは別にホストされているため、コンシューマー アプリケーションへの認証は[管理ログ](#admin-experience)に表示されません。

### <a name="what-the-header-does-and-does-not-block"></a>ヘッダーでブロックされるものとされないもの

`restrict-msa` ポリシーにより、コンシューマー アプリケーションの使用はブロックされますが、他の一部の種類のトラフィックおよび認証を通じて許可されます。

1. デバイスのユーザーレス トラフィック。  これには、オートパイロット、Windows Update、組織のテレメトリのトラフィックが含まれます。
1. コンシューマー アカウントの B2B 認証。 Microsoft アカウントを持ち、[テナントでのコラボレーションの招待を受けたユーザー](../external-identities/redemption-experience.md#invitation-redemption-flow)は、リソース テナントにアクセスするために login.live.com に対して認証を行います。
    1. そのリソース テナントへのアクセスを許可または拒否するために、このアクセスは `Restrict-Access-To-Tenants` ヘッダーを使用して制御されます。
1. 多くの Azure アプリおよび Office.com で使用される "パススルー" 認証。アプリでは、Azure AD を使用して、コンシューマー コンテキストでコンシューマー ユーザーにサインインします。
    1. 特別な "パススルー" テナントへのアクセスを許可または拒否するために、このアクセスも、`Restrict-Access-To-Tenants` ヘッダーを使用して制御されます (`f8cdef31-a31e-4b4a-93e4-5f571e91255a`)。  このテナントが、自分の許可されたドメインの `Restrict-Access-To-Tenants` リストに表示されない場合、コンシューマー アカウントは、これらのアプリへのサインインを Azure AD によってブロックされます。

## <a name="next-steps"></a>次のステップ

- [Office 365 の最新の認証の更新](https://www.microsoft.com/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/)について確認する
- [Office 365 URL および IP アドレス範囲](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)を確認する