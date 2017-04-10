---
title: "テナントを制限してクラウド アプリへのアクセスを管理する - Azure | Microsoft Docs"
description: "テナント制限を使用して、Azure AD テナントに基づいてアプリにアクセスできるユーザーを管理する方法について説明します。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: c579c0866387a5eff17b4dbfe25a6cb7d1d47700
ms.openlocfilehash: 4adf15e4767344d450b7411733a5d2f09cb9f06b


---
# <a name="use-tenant-restrictions-to-manage-access-to-saas-cloud-applications"></a>テナント制限を使用して SaaS クラウド アプリケーションへのアクセスを管理する

セキュリティを重視する大規模な組織は、Office 365 などのクラウド サービスへの移行を望んでいますが、ユーザーが未承認のリソースにアクセスできなくなることを認識しておく必要があります。 従来より、企業ではアクセスを管理するときにドメイン名や IP アドレスを制限しています。 この方法は、SaaS アプリがパブリック クラウドでホストされ、outlook.office.com や login.microsoftonline.com などの共有ドメイン名で実行されている世界では失敗します。 これらのアドレスをブロックすると、ユーザーを承認済みの ID やリソースに単に制限するのではなく、ユーザーは Web 上の Outlook にまったくアクセスできなくなります。

この課題を解決する Azure Active Directory のソリューションがテナント制限と呼ばれる機能です。 テナント制限を使用すると、組織はアプリケーションがシングル サインオンに使用する Azure AD テナントに基づいて、SaaS クラウド アプリケーションへのアクセスを制御できます。 たとえば、自分の組織の Office 365 アプリケーションへのアクセスは許可し、これらの同じアプリケーションの他の組織のインスタンスにはアクセスできないようにすることが可能です。  

テナント制限では、組織はユーザーがアクセスを許可されているテナントのリストを指定できます。 Azure AD は、これらの許可されているテナントへのアクセスだけを許可します。

この記事では Office 365 のテナント制限について説明していますが、この機能は Azure AD でのシングル サインオンに最新の認証プロトコルを使用するすべての SaaS クラウド アプリで動作します。 Office 365 で使用する Azure AD テナントとは異なる テナントで SaaS アプリを使用する場合は、必要なすべてのテナントが許可されていることを確認してください。 SaaS クラウド アプリの詳細については、[Active Directory Marketplace](https://azure.microsoft.com/en-us/marketplace/active-directory/) をご覧ください。

## <a name="how-does-it-work"></a>それはどのように機能しますか?

全体的なソリューションは、次のコンポーネントで構成されます。 

1. **Azure AD** - `Restrict-Access-To-Tenants: <permitted tenant list>` が存在する場合、Azure AD は許可されているテナントのセキュリティ トークンのみを発行します。 

2. **オンプレミスのプロキシ サーバー インフラストラクチャ** - 許可されているテナントのリストが含まれたヘッダーを Azure AD へのトラフィックに挿入するように構成された、SSL 検査に対応するプロキシ デバイス。 

3. **クライアント ソフトウェア** - テナント制限をサポートするには、クライアント ソフトウェアが Azure AD から直接トークンを要求する必要があるので、プロキシ インフラストラクチャによってトラフィックをインターセプトできます。 現在、テナント制限は、ブラウザー ベースの Office 365 アプリケーションと、最新の認証 (OAuth 2.0 など) を使用している場合の Office クライアントでサポートされています。 

4. **最新の認証** - テナント制限を使用し、許可されていないすべてのテナントへのアクセスをブロックするには、クラウド サービスで最新の認証を使用する必要があります。 Office 365 クラウド サービスは、既定で最新の認証プロトコルを使用するように構成する必要があります。 Office 365 による最新の認証のサポートに関する最新情報については、「[Updated Office 365 modern authentication (Office 365 の最新の認証の更新)](https://blogs.office.com/2015/11/19/updated-office-365-modern-authentication-public-preview/)」をご覧ください。

次の図は、おおまかなトラフィック フローを示しています。 SSL 検査は Azure AD へのトラフィックでのみ必要であり、Office 365 クラウド サービスへのトラフィックでは不要です。 通常、Azure AD への認証のトラフィック量は、Exchange Online や SharePoint Online などの SaaS アプリケーションへのトラフィック量よりもはるかに少ないため、この区別が重要です。

![テナント制限のトラフィック フロー - 図](./media/active-directory-tenant-restrictions/traffic-flow.png)

## <a name="set-up-tenant-restrictions"></a>テナント制限の設定

テナント制限の使用を開始する場合、2 つの手順があります。 まず、クライアントが適切なアドレスに接続できることを確認します。 次に、プロキシ インフラストラクチャを構成します。

### <a name="urls-and-ip-addresses"></a>URL と IP アドレス

テナント制限を使用するには、クライアントは認証のために、login.microsoftonline.com、login.microsoft.com、login.windows.net の各 Azure AD URL に接続できる必要があります。 さらに、Office 365 にアクセスするために、クライアントは「[Office 365 URL および IP アドレス範囲](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)」で定義されている FQDN/URL と IP アドレスにも接続できる必要があります。 

### <a name="proxy-configuration-and-requirements"></a>プロキシの構成と要件

プロキシ インフラストラクチャを使用したテナント制限を有効にするには、以下の構成が必要となります。 このガイダンスは一般的なものであるため、具体的な実装手順については、プロキシ ベンダーのドキュメントを参照してください。

#### <a name="prerequisites"></a>前提条件

- プロキシは、SSL インターセプト、HTTP ヘッダーの挿入、FQDN/URL を使用した送信先のフィルター処理を実行できる必要があります。 

- クライアントは、SSL 通信でプロキシによって提示される証明書チェーンを信頼する必要があります。 たとえば、内部 PKI の証明書を使用する場合、内部発行のルート証明機関証明書を信頼する必要があります。

- この機能は Office 365 サブスクリプションに含まれていますが、テナント制限を使用して他の SaaS アプリへのアクセスを制御する場合は、Azure AD Premium 1 ライセンスが必要です。

#### <a name="configuration"></a>構成

login.microsoftonline.com、login.microsoft.com、login.windows.net への各受信要求で、*Restrict-Access-To-Tenants* と *Restrict-Access-Context* の&2; つの HTTP ヘッダーを挿入します。

これらのヘッダーには、次の要素を含める必要があります。 
- *Restrict-Access-To-Tenants* には、値として\<許可されているテナント リスト\>を含めます。これは、ユーザーにアクセスを許可するテナントのコンマ区切りリストです。 テナントに登録されているドメインを使用して、このリストのテナントを識別できます。 たとえば、Contoso と Fabrikam の両方のテナントへのアクセスを許可する場合、名前と値のペアは `Restrict-Access-To-Tenants: contoso.onmicrosoft.com,fabrikam.onmicrosoft.com` のようになります。 
- *Restrict-Access-Context* には、値として&1; つのディレクトリ ID を含めます。この ID によって、テナント制限を設定するテナントを宣言します。 たとえば、テナント制限ポリシーを設定するテナントとして Contoso を宣言する場合、名前と値のペアは `Restrict-Access-Context: 456ff232-35l2-5h23-b3b3-3236w0826f3d` のようになります。  

> [!TIP]
> ディレクトリ ID は [Azure Portal](https://portal.azure.com) で確認できます。 管理者としてサインインし、**[Azure Active Directory]** を選択して、**[プロパティ]** を選択します。

ユーザーが未承認のテナントを含む独自の HTTP ヘッダーを挿入するのを防ぐために、プロキシは受信要求に Restrict-Access-To-Tenants ヘッダーが既に存在する場合にそのヘッダーを置き換える必要があります。 

login.microsoftonline.com、login.microsoft.com、login.windows.net へのすべての要求にプロキシを使用することをクライアントに強制する必要があります。 たとえば、PAC ファイルを使用してクライアントにプロキシを使用するよう指示する場合、エンド ユーザーが PAC ファイルを編集したり無効にしたりできないようにする必要があります。

## <a name="the-user-experience"></a>ユーザー エクスペリエンス

ここでは、エンド ユーザーと管理者のエクスペリエンスについて説明します。

### <a name="end-user-experience"></a>エンド ユーザー エクスペリエンス

たとえば、Contoso ネットワーク上のユーザーが、Outlook Online などの共有 SaaS アプリケーションの Fabrikam インスタンスにアクセスしようとしているとします。 Contoso がそのインスタンスの許可されていないテナントの場合、ユーザーには次のページが表示されます。

![許可されていないテナントのユーザーに対してアクセスが拒否されたことを示すページ](./media/active-directory-tenant-restrictions/end-user-denied.png)

### <a name="admin-experience"></a>管理者エクスペリエンス

企業プロキシ インフラストラクチャでテナント制限の構成が完了すると、管理者は Azure Portal でテナント制限レポートに直接アクセスできます。 レポートを表示するには、Azure Active Directory の [概要] ページに移動し、[その他の機能] を確認します。

Restricted-Access-Context テナントとして指定されたテナントの管理者は、このレポートを使用して、テナント制限ポリシーによってブロックされたすべてのサインイン (使用された ID やターゲット ディレクトリ ID など) を確認できます。

![Azure Portal を使用した制限されたサインイン試行の表示](./media/active-directory-tenant-restrictions/portal-report.png)

Azure Portal の他のレポートと同様に、フィルターを使用してレポートの範囲を指定できます。 特定のユーザー、アプリケーション、クライアント、または時間間隔でフィルター処理できます。

## <a name="office-365-support-for-tenant-restrictions"></a>Office 365 によるテナント制限のサポート

テナント制限を完全にサポートするには、Office 365 アプリケーションは次の 2 つの条件を満たす必要があります。

1. 使用されるクライアントが最新の認証をサポートしている。
2. クラウド サービスの既定の認証プロトコルとして最新の認証が有効になっている。

最新の認証を現在サポートしている Office クライアントに関する最新情報については、「[Updated Office 365 modern authentication (Office 365 の最新の認証の更新)](https://blogs.office.com/2015/11/19/updated-office-365-modern-authentication-public-preview/)」をご覧ください。 このページには、特定の Exchange Online テナントと Skype for Business Online テナントで最新の認証を有効にする手順へのリンクも含まれています。 SharePoint Online では、最新の認証が既定で既に有効になっています。

現在、テナント制限はブラウザー ベースの Office 365 アプリケーション (Office Portal、Yammer、SharePoint サイト、Outlook on the Web など) でサポートされています。 シック クライアント (Outlook、Skype for Business、Word、Excel、PowerPoint など) の場合、最新の認証が使用されている場合にのみ、テナント制限を適用できます。  

最新の認証をサポートする Outlook クライアントと Skype for Business クライアントでは、最新の認証が有効になっていないテナントに対して従来のプロトコルを引き続き使用できるため、テナント制限を実質的に迂回します。 Windows 上の Outlook の場合、エンド ユーザーが未承認の電子メール アカウントをプロファイルに追加できないようにする制限を実装することもできます。 例については、[既定以外の Exchange アカウントを追加できないようにする](http://gpsearch.azurewebsites.net/default.aspx?ref=1)グループ ポリシー設定に関するページをご覧ください。 Windows 以外のプラットフォーム上の Outlook と、すべてのプラットフォーム上の Skype for Business では、サービス全体の既定の設定として最新の認証が有効化されたときに、テナント制限が完全にサポートされると予想されます。

## <a name="testing"></a>テスト

テナント制限を組織全体で実装する前にテストする必要がある場合、Fiddler などのツールを使用したホスト ベースのアプローチと、プロキシ設定の段階的なロールアウトの&2; つの方法があります。

### <a name="fiddler-for-a-host-based-approach"></a>Fiddler を使用したホスト ベースのアプローチ

Fiddler は無料の Web デバッグ プロキシです。Fiddler を使用して HTTP/HTTPS トラフィックをキャプチャし、トラフィックを変更 (HTTP ヘッダーの挿入など) できます。 Fiddler を構成してテナント制限をテストするには、次の手順を実行します。

1.  [Fiddler をダウンロードしてインストール](http://www.telerik.com/fiddler)します。
2.  [Fiddler のヘルプ ドキュメント](http://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS)に従って、HTTPS トラフィックを復号化するように Fiddler を構成します。
3.  カスタム ルールを使用して、*Restrict-Access-To-Tenants* と *Restrict-Access-Context* の各ヘッダーを挿入するように Fiddler を構成します。
  1. Fiddler Web Debugger ツールで、**[Rules]** メニューを選択し、**[Customize Rules…]** を選択して CustomRules ファイルを開きます。
  2. *OnBeforeRequest* 関数の先頭に次の行を追加します。 \<tenant domain\> を、テナントに登録されているドメイン (contoso.onmicrosoft.com など) に置き換えます。 \<directory ID\> を、テナントの Azure AD GUID 識別子に置き換えます。

  ```
  if (oSession.HostnameIs("login.microsoftonline.com") || oSession.HostnameIs("login.microsoft.com") || oSession.HostnameIs("login.windows.net")){
      oSession.oRequest["Restrict-Access-To-Tenants"] = "<tenant domain>";
      oSession.oRequest["Restrict-Access-Context"] = "<directory ID>";
  }
  ```
  >[!NOTE]
  > 上記のコード スニペットはすべて&1; 行で記述する必要があります。 右かっこの後まで復帰はありません。

  複数のテナントを許可する必要がある場合は、テナント名をコンマで区切ります。 次に例を示します。

  ```
  oSession.oRequest["Restrict-Access-To-Tenants"] = "contoso.onmicrosoft.com,fabrikam.onmicrosoft.com";
  ```

4. CustomRules ファイルを保存して閉じます。

Fiddler を構成したら、**[File]** メニューに移動し、**[Capture Traffic]** を選択することでトラフィックをキャプチャできます。

### <a name="staged-rollout-of-proxy-settings"></a>プロキシ設定の段階的なロールアウト

プロキシ インフラストラクチャの機能によっては、設定をユーザーに段階的にロールアウトできる場合があります。 考慮すべき高度なオプションは次のとおりです。

1.  PAC ファイルを使用して、テスト ユーザーがテスト用プロキシ インフラストラクチャを使用するようにし、通常のユーザーは運用環境のプロキシ インフラストラクチャを引き続き使用します。
2.  場合によっては、一部のプロキシ サーバーでグループを使用して別の構成をサポートします。

具体的な詳細については、ご使用のプロキシ サーバーのドキュメントを参照してください。

## <a name="next-steps"></a>次のステップ

- [Office 365 の最新の認証の更新](https://blogs.office.com/2015/11/19/updated-office-365-modern-authentication-public-preview/)について確認する

- [Office 365 URL および IP アドレス範囲](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)を確認する



<!--HONumber=Jan17_HO5-->


