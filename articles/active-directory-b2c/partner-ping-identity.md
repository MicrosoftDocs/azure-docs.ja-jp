---
title: Azure Active Directory B2C と Ping Identity を構成するためのチュートリアル
titleSuffix: Azure AD B2C
description: Azure AD B2C 認証を Ping Identity と統合する方法について説明します
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/20/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 94e7ae93d05ae8ee35028882e14d8da74814d833
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101650228"
---
# <a name="tutorial-configure-ping-identity-with-azure-active-directory-b2c-for-secure-hybrid-access"></a>チュートリアル:セキュリティで保護されたハイブリッド アクセスのために Azure Active Directory B2C で Ping Identity を構成する

このサンプル チュートリアルでは、[PingAccess](https://www.pingidentity.com/en/software/pingaccess.html#:~:text=%20Modern%20Access%20Managementfor%20the%20Digital%20Enterprise%20,consistent%20enforcement%20of%20security%20policies%20by...%20More) と [PingFederate](https://www.pingidentity.com/en/software/pingfederate.html) を使用して Azure Active Directory (AD) B2C を拡張し、セキュリティで保護されたハイブリッド アクセスを有効にする方法について説明します。

インターネットに公開されている電子商取引サイトや Web アプリケーションなど、既存の Web プロパティの多くは、プロキシ システム (リバース プロキシ システムと呼ばれることもある) の背後にデプロイされています。 これらのプロキシ システムには、事前認証、ポリシーの適用、トラフィックのルーティングなど、さまざまな機能が用意されています。 ユース ケースの例としては、受信 Web トラフィックから Web アプリケーションを保護したり、分散サーバー デプロイ全体に一貫したセッション管理を提供したりすることが挙げられます。

ほとんどの場合、この構成には、Web アプリケーションから認証を外部化する認証変換レイヤーが含まれています。 そして、リバース プロキシによって、平文またはダイジェスト形式のヘッダー値などの単純な形式で、認証されたユーザーのコンテキストが Web アプリケーションに提供されます。 このような構成の場合、アプリケーションは、Security Assertion Markup Language (SAML)、OAuth、Open ID Connect (OIDC) などの業界標準トークンを使用しません。むしろプロキシに依存して、認証コンテキストを提供し、ブラウザーやネイティブ アプリケーションなどのエンド ユーザー エージェントとのセッションを維持します。 "中間者" で実行されているサービスとして、プロキシは究極のセッション制御を提供できます。 このことはまた、プロキシ サービスが、プロキシ サービスの背後にあるアプリケーションにとってボトルネックや単一障害点にならないように、高い効率と拡張性を備えている必要があることも意味します。 この図は、一般的なリバース プロキシの実装と通信フローを示しています。

![画像はリバース プロキシの実装を示しています](./media/partner-ping/reverse-proxy.png)

このような構成で ID プラットフォームを最新化する必要がある状況の場合は、次の問題が発生します。

- アプリケーションの最新化の取り組みを、ID プラットフォームの最新化から切り離すにはどうすればよいか。

- 最新の ID サービス プロバイダーを利用して、最新およびレガシの認証で共存環境を確立するにはどうすればよいか。

  a. エンド ユーザー エクスペリエンスの一貫性を促進するにはどうすればよいか。

  b. 共存するアプリケーション間でシングル サインイン エクスペリエンスを提供するにはどうすればよいか。

Azure AD B2C を [PingAccess](https://www.pingidentity.com/en/software/pingaccess.html#:~:text=%20Modern%20Access%20Managementfor%20the%20Digital%20Enterprise%20,consistent%20enforcement%20of%20security%20policies%20by...%20More) および [PingFederate](https://www.pingidentity.com/en/software/pingfederate.html) のテクノロジーと統合することでこのような問題を解決する方法について説明します。

## <a name="coexistence-environment"></a>共存環境

技術的に実行可能で、コスト効率も高いシンプルなソリューションは、最新の ID システムを使用して認証を委任するようにリバース プロキシ システムを構成することです。  
この場合、プロキシでは最新の認証プロトコルがサポートされ、ユーザーを新しい ID プロバイダー (IdP) に送信するリダイレクト ベース (パッシブ) の認証が使用されます。

### <a name="azure-ad-b2c-as-an-identity-provider"></a>ID プロバイダーとしての Azure AD B2C

Azure AD B2C には、サーバー側から調整された、さまざまなユーザー エクスペリエンスと動作 (**ユーザー体験** とも呼ばれる) を促進する **ポリシー** を定義する機能があります。 このような各ポリシーでは、IdP のように認証を実行できるプロトコル エンドポイントが公開されます。 特定のポリシーに対してアプリケーション側では特別な処理は必要ありません。 アプリケーションでは、目的のポリシーによって公開されているプロトコル固有の認証エンドポイントに対して業界標準の認証要求が行われるだけです。  
Azure AD B2C は、複数のポリシーで同じ発行者を共有するようにも、ポリシーごとに一意の発行者を使用するようにも構成できます。 各アプリケーションは、プロトコル固有の認証要求を行うことでこれらのポリシーの 1 つまたは複数を参照し、サインイン、サインアップ、プロファイル編集など、目的のユーザー動作を実行させることができます。 この図は、OIDC および SAML アプリケーションのワークフローを示しています。

![画像は OIDC と SAML の実装を示しています](./media/partner-ping/azure-ad-identity-provider.png)

前述のシナリオは最新のアプリケーションではうまく機能しますが、アプリケーションへのアクセス要求にユーザー エクスペリエンスのコンテキストが含まれていないレガシ アプリケーションでは、ユーザーを適切にリダイレクトすることが難しい場合があります。 ほとんどの場合、プロキシ レイヤー、または Web アプリケーション上の統合エージェントによってアクセス要求がインターセプトされます。

### <a name="pingaccess-as-a-reverse-proxy"></a>リバース プロキシとしての PingAccess

多くのお客様が、この記事で前述したように、PingAccess をリバース プロキシとしてデプロイして、1 つまたは多くのロールを実行しています。 PingAccess は、中間者になることで、または Web アプリケーション サーバーで実行されているエージェントからのリダイレクトとして、直接要求をインターセプトできます。

PingAccess は、OIDC、OAuth2、または SAML を使用して構成し、アップストリームの認証プロバイダーに対して認証を実行できます。 この目的のために、PingAccess サーバーに 1 つのアップストリーム IdP を構成できます。 次の図はこの構成を示したものです。

![画像は OIDC を使用した PingAccess の実装を示しています](./media/partner-ping/authorization-flow.png)

複数のポリシーで複数の **IdP** が公開されている一般的な Azure AD B2C のデプロイでは、問題が生じます。 PingAccess は、1 つのアップストリーム IdP でしか構成できないからです。  

### <a name="pingfederate-as-a-federation-proxy"></a>フェデレーション プロキシとしての PingFederate

PingFederate は、認証プロバイダーとして、または必要に応じて他の複数のアップストリーム IdP のプロキシとして完全に構成できるエンタープライズ ID ブリッジです。 次の図はこの機能を示したものです。

![画像は、PingFederate の実装を示しています](./media/partner-ping/pingfederate.png)

この機能を使用すると、文脈的または動的に、あるいは宣言によって、受信要求を特定の Azure AD B2C ポリシーに切り替えることができます。 この構成のプロトコル シーケンス フローを次に示します。

![画像は、PingAccess と PingFederate のワークフローを示しています](./media/partner-ping/pingaccess-pingfederate-workflow.png)

## <a name="prerequisites"></a>前提条件

作業を開始するには、以下が必要です。

- Azure サブスクリプション。 所有していない場合は、[無料アカウント](https://azure.microsoft.com/free/)を入手してください。

- お使いの Azure サブスクリプションにリンクされている [Azure AD B2C テナント](./tutorial-create-tenant.md)。

- Docker コンテナーに、または直接 Azure VM にデプロイされた PingAccess と PingFederate。

## <a name="connectivity"></a>接続

次のものが接続されていることを確認します。

- **PingAccess サーバー** – PingFederate サーバー、クライアント ブラウザー、OIDC、OAuth の well-known、および Azure AD B2C サービスと PingFederate サーバーによって発行されたキー検出と通信できます。

- **PingFederate サーバー** – PingAccess サーバー、クライアント ブラウザー、OIDC、OAuth の well-known、および Azure AD B2C サービスによって発行されたキー検出と通信できます。

- **レガシまたはヘッダーベースの AuthN アプリケーション** – PingAccess サーバーとの間で通信できます。

- **SAML 証明書利用者アプリケーション** – クライアントからブラウザー トラフィックにアクセスできます。 Azure AD B2C サービスによって発行された SAML フェデレーション メタデータにアクセスできます。

- **最新アプリケーション** – クライアントからブラウザー トラフィックにアクセスできます。 OIDC、OAuth の well-known、および Azure AD B2C サービスによって発行されたキー検出にアクセスできます。

- **REST API** – ネイティブまたは Web クライアントからのトラフィックにアクセスできます。 OIDC、OAuth の well-known、および Azure AD B2C サービスによって発行されたキー検出にアクセスできます。

## <a name="configure-azure-ad-b2c"></a>Azure AD B2C を構成する

この目的のために、基本的なユーザー フローまたは高度な Identity Experience Framework (IEF) ポリシーを使用できます。 PingAccess では、[WebFinger](https://tools.ietf.org/html/rfc7033) ベースの検出規則を使用し、**Issuer** の値に基づいてメタデータ エンドポイントが生成されます。
この規則に従うには、ユーザー フローのポリシー プロパティを使用して Azure AD B2C の発行者を更新します。

![画像は、トークンの設定を示しています](./media/partner-ping/token-setting.png)

高度なポリシーでは、これは、[JWT トークン発行者の技術プロファイル](./jwt-issuer-technical-profile.md)で **IssuanceClaimPattern** メタデータ要素を **AuthorityWithTfp** 値に設定することによって構成できます。

## <a name="configure-pingaccesspingfederate"></a>PingAccess または PingFederate を構成する

次のセクションでは、必要な構成について説明します。
この図は、統合の全体的なユーザー フローを示しています。

![画像は、PingAccess と PingFederate の統合を示しています](./media/partner-ping/pingaccess.png)

### <a name="configure-pingfederate-as-the-token-provider"></a>PingFederate をトークン プロバイダーとして構成する

PingFederate を PingAccess のトークン プロバイダーとして構成するには、PingFederate から PingAccess への接続が確立され、その後に PingAccess から PingFederate への接続が行われることを確認します。  
構成手順については、[こちらの記事](https://docs.pingidentity.com/bundle/pingaccess-61/page/zgh1581446287067.html)を参照してください。

### <a name="configure-a-pingaccess-application-for-header-based-authentication"></a>ヘッダーベース認証用に PingAccess アプリケーションを構成する

ヘッダーベース認証のターゲット Web アプリケーション用に PingAccess アプリケーションを作成する必要があります。 次の手順に従います。

#### <a name="step-1--create-a-virtual-host"></a>手順 1 – 仮想ホストを作成する

>[!IMPORTANT]
>このソリューション用に構成するには、すべてのアプリケーションに対して仮想ホストを作成する必要があります。 構成に関する考慮事項とその影響の詳細については、[主要な考慮事項](https://docs.pingidentity.com/bundle/pingaccess-43/page/reference/pa_c_KeyConsiderations.html)に関するページを参照してください。

これらの手順に従って、仮想ホストを作成します。

1. **[設定]**  >  **[アクセス]**  >  **[仮想ホスト]** にアクセスします。

2. **[Add Virtual Host]\(仮想ホストの追加\)** を選択します。

3. [ホスト] フィールドに、アプリケーション URL の FQDN 部分を入力します。

4. [ポート] フィールドに、「**443**」と入力します。

5. **[保存]** を選びます。

#### <a name="step-2--create-a-web-session"></a>手順 2 – Web セッションを作成する

これらの手順に従って、Web セッションを作成します。

1. **[設定]**  >  **[アクセス]**  >  **[Web セッション]** に移動します。

2. **[Add Web Session]\(Web セッションの追加\)** を選択します。

3. Web セッションの **名前** を指定します。

4. **Cookie の種類** として **署名付き JWT** または **暗号化された JWT** を選択します。

5. **対象ユーザー** に対して一意の値を指定します。

6. **[クライアント ID]** フィールドに、**Azure AD アプリケーション ID** を入力します。

7. **[クライアント シークレット]** フィールドに、Azure AD でアプリケーション用に生成した **キー** を入力します。

8. 省略可能 - Microsoft Graph API を使用してカスタム要求を作成して使用することができます。 これを選択した場合は、 **[Advanced]\(拡張\)** を選択し、 **[Request Profile]\(プロファイルの要求\)** と **[Refresh User Attributes]\(ユーザー属性の更新\)** オプションの選択を解除します。 カスタム要求の使用方法について詳しくは、[カスタム要求の使用](../active-directory/manage-apps/application-proxy-configure-single-sign-on-with-headers.md)に関するページ参照してください。

9. **[保存]** を選びます。

#### <a name="step-3--create-identity-mapping"></a>手順 3 – ID マッピングを作成する

>[!NOTE]
>複数のアプリケーションのヘッダー内で同じデータが想定されている場合は、複数のアプリケーションで ID マッピングを使用できます。

これらの手順に従って、ID マッピングを作成します。

1. **[設定]**  >  **[アクセス]**  >  **[ID マッピング]** にアクセスします。

2. **[Add Identity Mapping]\(ID マッピングの追加\)** を選択します。

3. **名前** を指定します。

4. ID マッピングでの **ヘッダー ID マッピングの種類** を選択します。

5. **属性マッピング** のテーブルで、必要なマッピングを指定します。 たとえば、次のように入力します。

   属性名 | ヘッダー名 |
   |-------|--------|
   |upn | x-userprinciplename |
   |email   |    x-email  |
   |oid   | x-oid  |
   |scp   |     x-scope |
   |amr    |    x-amr    |

6. **[保存]** を選びます。

#### <a name="step-4--create-a-site"></a>手順 4 - サイトを作成する

>[!NOTE]
>一部の構成では、1 つのサイトに複数のアプリケーションを含めることができます。 必要に応じて、1 つのサイトを複数のアプリケーションで使用できます。

これらの手順に従って、サイトを作成します。

1. **[メイン]**  >  **[サイト]** にアクセスします。

2. **[サイトの追加]** を選択します。

3. サイトの **名前** を指定します。

4. サイトの **ターゲット** を入力します。 ターゲットは、アプリケーションがホストされているサーバーのホスト名とポートのペアです。 このフィールドには、アプリケーションのパスを入力しないでください。 たとえば、 https://mysite:9999/AppName にあるアプリケーションのターゲット値は次のようになります: mysite: 9999

5. ターゲットで、セキュリティで保護された接続が想定されているかどうかを示します。

6. セキュリティで保護された接続がターゲットで想定されている場合は、[Trusted Certificate Group]\(信頼できる証明書グループ\) を **[Trust Any]\(すべて信頼する\)** に設定します。

7. **[保存]** を選びます。

#### <a name="step-5--create-an-application"></a>手順 5 - アプリケーションを作成する

保護する必要がある Azure 内の各アプリケーションに対して PingAccess でアプリケーションを作成するには、これらの手順に従ってください。

1. **[メイン]**  >  **[アプリケーション]** にアクセスします。

2. **[アプリケーションの追加]** を選択します。

3. アプリケーションの **名前** を指定します。

4. 必要に応じて、アプリケーションの **説明** を入力します。

5. アプリケーションの **コンテキスト ルート** を指定します。 たとえば、 https://mysite:9999/AppName にあるアプリケーションのコンテキスト ルートは /AppName になります。 コンテキスト ルートはスラッシュ (/) で開始する必要がありますが、末尾をスラッシュ (/) にすることはできません。また、/Apps/MyApp. のように、複数のレイヤーの深さを指定できます。

6. 作成した **仮想ホスト** を選択します

   >[!NOTE]
   >仮想ホストとコンテキスト ルートの組み合わせは、PingAccess 内で一意である必要があります。

7. 作成した **Web セッション** を選択します

8. アプリケーションが含まれている、作成した **サイト** を選択します

9. 作成した **ID マッピング** を選択します

10. **[Enabled]\(有効にする\)** を選択して、保存時にサイトを有効にします。

11. **[保存]** を選びます。

### <a name="configure-the-pingfederate-authentication-policy"></a>PingFederate 認証ポリシーを構成する

Azure AD B2C テナントによって提供されている複数の IdP にフェデレーションするように、PingFederate 認証ポリシーを構成します。

1. IdP と SP の間で属性をブリッジするコントラクトを作成します。 詳細については、[フェデレーション ハブと認証ポリシーのコントラクト](https://docs.pingidentity.com/bundle/pingfederate-101/page/ope1564002971971.html)に関するページを参照してください。 SP によって、各 IdP から異なる属性セットが要求されない限り、必要なコントラクトは 1 つだけになります。

2. 各 IdP に対して、IdP と、SP としてのフェデレーション ハブである PingFederate の間に IdP 接続を作成します。

3. **[Target Session Mapping]\(ターゲット セッション マッピング\)** ウィンドウで、該当する認証ポリシーのコントラクトを IdP 接続に追加します。

4. **[Selectors]\(セレクター\)** ウィンドウで、認証セレクターを構成します。 たとえば、各 IdP を認証ポリシー内の対応する IdP 接続にマップするには、 **[Identifier First Adapter]\(ID の最初のアダプター\)** のインスタンスを参照します。

5. IdP としてのフェデレーション ハブである PingFederate と SP の間に SP 接続を作成します。

6. **[Authentication Source Mapping]\(認証ソース マッピング\)** ウィンドウで、対応する認証ポリシーのコントラクトを SP 接続に追加します。

7. 各 IdP を使用して、SP としてのフェデレーション ハブである PingFederate に接続します。

8. SP を使用して、IdP としてのフェデレーション ハブである PingFederate に接続します。

## <a name="next-steps"></a>次のステップ

追加情報については、次の記事を参照してください。

- [Azure AD B2C のカスタム ポリシー](./custom-policy-overview.md)

- [Azure AD B2C のカスタム ポリシーの概要](./custom-policy-get-started.md?tabs=applications)
