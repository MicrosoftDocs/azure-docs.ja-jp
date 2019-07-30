---
title: アプリを AD FS から Azure AD に移動する | Microsoft Docs
description: この記事は、アプリケーションを Azure AD に移動する方法の理解に役立つことを目的としています。特に、フェデレーション SaaS アプリケーションに重点を置いています。
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 03/02/2018
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 272d5194b2922e57aca0d63fd62c222e17a29c53
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67108256"
---
# <a name="move-applications-from-ad-fs-to-azure-ad"></a>アプリケーションを AD FS から Azure AD に移動する 

この記事では、アプリケーションを AD FS から Azure Active Directory (Azure AD) に移動する方法を理解するのに役立ちます。 フェデレーション SaaS アプリケーションに重点を置いています。 

この記事では、ステップ バイ ステップ ガイダンスは提供していません。 オンプレミスの構成を Azure AD に変換するしくみを理解して移行を成功させるための概念的なガイダンスを提供しています。 また、一般的なシナリオについても説明します。

## <a name="introduction"></a>はじめに

ユーザー アカウントを含むオンプレミス ディレクトリがある場合は、少なくとも 1 つまたは 2 つのアプリがある可能性があります。 これらのアプリは、ユーザーがこれらの ID でサインオンすることでアクセスできるように構成されています。

多くの組織は、クラウド アプリケーションと ID を採用する過程のいずれかの段階にあるでしょう。 Office 365 と Azure AD Connect で運用を始めているかもしれません。 すべてではなく一部の主要なワークロード用に、クラウド ベースの SaaS アプリケーションをセットアップした場合もあるでしょう。  

多くの組織では、Office 365 や Azure AD ベースのアプリのほかに、SaaS またはカスタムの基幹業務 (LOB) アプリを、Active Directory フェデレーション サービス (AD FS) などのオンプレミスのサインオン サービスに直接フェデレーションしています。 このガイドでは、アプリケーションを Azure AD に移動する理由と方法を説明します。

>[!NOTE]
>SaaS アプリの構成と移行に関する詳細情報と、カスタム LOB アプリの概要情報を提供します。 カスタム LOB アプリの詳細なガイダンスも、今後提供する予定です。

![オンプレミスに直接接続されているアプリ](media/migrate-adfs-apps-to-azure/migrate1.png)

![Azure AD を通じてフェデレーションされているアプリ](media/migrate-adfs-apps-to-azure/migrate2.png)

## <a name="reasons-for-moving-apps-to-azure-ad"></a>アプリを Azure AD に移動する理由

既に AD FS、Ping、またはその他のオンプレミス認証プロバイダーを使用している組織にとって、アプリを Azure AD に移動することには、次のような利点があります。

**より安全なアクセス**
- [Azure AD 条件付きアクセス](../active-directory-conditional-access-azure-portal.md)を使用して、Azure Multi-Factor Authentication を含む、アプリケーションごとの詳細なアクセス制御を構成します。 ポリシーは、SaaS とカスタム アプリに、現在の Office 365 と同じように適用できます。
- 危険なトラフィックを識別する機械学習とヒューリスティックに基づいて、脅威を検出し、サインオンを保護するには、[Azure AD Identity Protection](../active-directory-identityprotection.md) を活用します。

**Azure AD B2B コラボレーション**
- SaaS アプリへのサインオンが Azure AD に基づいている場合は、[Azure AD B2B コラボレーション](../b2b/what-is-b2b.md)を使用して、パートナーにクラウド リソースへのアクセスを許可することができます。

**より簡単な管理エクスペリエンスと Azure AD の追加機能**
- Azure AD は、SaaS アプリの ID プロバイダーとして、次のような追加機能をサポートしています。
  - アプリケーションごとのトークン署名証明書。
  - [構成可能な証明書有効期限日](manage-certificates-for-federated-single-sign-on.md)。
  - Azure AD ID に基づくユーザー アカウントの[自動プロビジョニング](user-provisioning.md) (主要な Azure Marketplace アプリの場合)。

**オンプレミスの ID プロバイダーの利点の維持**
- Azure AD の利点を利用しながら、認証のためのオンプレミスのソリューションを引き続き使用することができます。 そのため、オンプレミスの Multi-Factor Authentication ソリューション、ログ記録、監査などの利点はそのまま維持されます。 

**オンプレミスの ID プロバイダーの廃止の支援**
- オンプレミスの認証製品を廃止しようとしている組織では、アプリを Azure AD に移動すると、一部の作業を取り除かれることで簡単に切り替えを行うことができるようになります。 

## <a name="mapping-types-of-apps-on-premises-to-types-of-apps-in-azure-ad"></a>オンプレミスのアプリの種類と Azure AD のアプリの種類のマッピング
ほとんどのアプリは、使用しているサインオンの種類に基づいて、いくつかのカテゴリに分類されます。 これらのカテゴリに応じて、Azure AD でのアプリの表現方法が決まります。

簡単に言えば、SAML 2.0 アプリケーションは、Marketplace の Azure AD アプリケーション ギャラリーを通じて、または非 Marketplace アプリケーションとして、Azure AD と統合することができます。 OAuth 2.0 または OpenID Connect を使用するアプリは、"*アプリの登録*" として同じように Azure AD と統合できます。 詳細については、続く内容をご覧ください。

### <a name="federated-saas-apps-vs-custom-lob-apps"></a>フェデレーション SaaS アプリとカスタム LOB アプリ
フェデレーション アプリには、以下のカテゴリに分類されるアプリが含まれます。

- SaaS アプリ 
    - ユーザーが Salesforce、ServiceNow、Workday などの SaaS アプリにサインオンし、管理者が AD FS や Ping などのオンプレミス ID プロバイダーと統合している場合、管理者は SaaS アプリへのフェデレーション サインオンを使用しています。
    - アプリは、通常、フェデレーション サインオンに SAML 2.0 プロトコルを使用します。
    - このカテゴリに分類されるアプリは、Marketplace から、または非 Marketplace アプリケーションとして、Azure AD にエンタープライズ アプリケーションとして統合できます。
- カスタム LOB アプリ
    - これは、組織内で開発された非 SaaS アプリか、データ センターにインストールされた標準パッケージ製品として利用可能な非 SaaS アプリを指します。 SharePoint アプリと、Windows Identity Foundation 上に構築されたアプリが含まれます。
    - アプリは、フェデレーション サインオンに、SAML 2.0、WS-Federation、OAuth、または OpenID Connect を使用できます。
    - OAuth 2.0、OpenID Connect、または WS-Federation を使用するカスタム アプリは、アプリの登録として Azure AD と統合できます。 SAML 2.0 または WS-Federation を使用するカスタム アプリは、エンタープライズ アプリケーション内の非 Marketplace アプリケーションとして統合できます。

### <a name="non-federated-apps"></a>非フェデレーション アプリ
非フェデレーション アプリは、Azure AD アプリケーション プロキシと関連機能を使用して、Azure AD と統合することができます。 非フェデレーション アプリには次のものがあります。
- Active Directory で直接 Windows 統合認証を使用するアプリ。 これらのアプリは、[Azure AD アプリケーション プロキシ](application-proxy-add-on-premises-application.md)を通じて Azure AD に統合できます。
- エージェントを通じてシングル サインオン プロバイダーと統合し、承認のためにヘッダーを使用するアプリ。 サインオンおよびヘッダーベースの承認のために、インストールされたエージェントを使用するオンプレミス アプリは、Azure AD アプリケーション プロキシと [Ping Access for Azure AD](https://blogs.technet.microsoft.com/enterprisemobility/2017/06/15/ping-access-for-azure-ad-is-now-generally-available-ga/) を使用して、Azure AD ベースのサインオン用に構成できます。

## <a name="translating-on-premises-federated-apps-to-azure-ad"></a>オンプレミスのフェデレーション アプリから Azure AD への変換 
AD FS と Azure AD は動作が似ているため、信頼関係の構成、サインオンとサインアウトの URL、および識別子の概念は、両方に当てはまります。 ただし、移行を行う際に理解しておく必要がある、いくつかの小さな違いがあります。

次の表では、変換に役立つように、AD FS、Azure AD、および SaaS アプリで共有されるいくつかの重要な概念をマッピングしています。 

### <a name="representing-the-app-in-azure-ad-or-ad-fs"></a>Azure AD または AD FS でのアプリの表現
移行を開始するには、まず、アプリケーションがオンプレミスでどのように構成されているかを評価し、その構成を Azure AD にマッピングします。 AD FS での証明書利用者の構成要素と、Azure AD での対応する要素とのマッピングを次の表に示します。  
- AD FS の用語:証明書利用者または証明書利用者信頼。
- Azure AD の用語:エンタープライズ アプリケーションまたはアプリの登録 (アプリの種類によって異なります)。

|アプリの構成要素|説明|AD FS 構成内の場所|Azure AD 構成内の対応する場所|SAML トークン要素|
|-----|-----|-----|-----|-----|
|アプリのサインオン URL|このアプリケーションのサインイン ページの URL。 これは、SP によって開始された SAML フローでユーザーがアプリにサインインする場所です。|該当なし|Azure AD では、サインオン URL は Azure Portal 内でアプリケーションの **[シングル サインオン]** プロパティの [サインオン URL] として構成します</br></br>([サインオン URL] を表示するには、 **[詳細な URL 設定の表示]** を選択しなければならない場合があります)。|該当なし|
|アプリの応答 URL|ID プロバイダー (IdP) の観点からの、アプリの URL。 これは、ユーザーが IdP でサインオンした後で、ユーザーとトークンが送信される場所です。</br></br> "SAML アサーション コンシューマー エンドポイント" と呼ばれることもあります。|アプリの AD FS 証明書利用者信頼にあります。 証明書利用者を右クリックし、 **[プロパティ]** を選択し、 **[エンドポイント]** タブをクリックします。|Azure AD では、応答 URL は Azure Portal 内でアプリケーションの **[シングル サインオン]** プロパティの [応答 URL] として構成します</br></br>([応答 URL] を表示するには、 **[詳細な URL 設定の表示]** を選択しなければならない場合があります)。|SAML トークンの **Destination** 要素にマッピングされます。</br></br> 値の例: `https://contoso.my.salesforce.com`|
|アプリのサインアウト URL|ユーザーがアプリからサインアウトするときに、IdP によってユーザーがサインオンした他のすべてのアプリをサインアウトするために、"サインアウト クリーンアップ" 要求が送信される URL。|[AD FS の管理] の **[証明書利用者信頼]** の下にあります。 証明書利用者を右クリックし、 **[プロパティ]** を選択し、 **[エンドポイント]** タブをクリックします。|該当なし。 Azure AD は、すべてのアプリからサインアウトする "シングル ログアウト" をサポートしていません。 単に Azure AD 自体からユーザーをサインアウトします。|該当なし|
|アプリ識別子|IdP の観点からの、アプリの識別子。 多くの場合、サインオン URL 値が識別子に使用されます (そうでない場合もあります)。</br></br> アプリ側でこれを "エンティティ ID" と呼ぶこともあります。|AD FS では、証明書利用者 ID となっています。 証明書利用者を右クリックし、 **[プロパティ]** を選択し、 **[識別子]** タブをクリックします。|Azure AD では、識別子は Azure Portal 内でアプリケーションの **[シングル サインオン]** プロパティの **[ドメインと URL]** の下の識別子として構成します ( **[詳細な URL 設定の表示]** チェック ボックスをオンにしなければならない場合があります)。|SAML トークンの **Audience** 要素に対応します。|
|アプリのフェデレーション メタデータ|アプリのフェデレーション メタデータの場所。 エンドポイントや暗号化証明書などの特定の構成設定を自動更新するために、IdP によって使用されます。|アプリのフェデレーション メタデータ URL は、アプリの AD FS 証明書利用者信頼にあります。 信頼を右クリックし、 **[プロパティ]** を選択し、 **[監視]** タブをクリックします。|該当なし。 Azure AD では、アプリケーション フェデレーション メタデータの使用を直接サポートしていません。|該当なし|
|ユーザー識別子/**NameID**|Azure AD または AD FS のユーザー ID をアプリに一意に示すために使用される属性。</br></br> この属性は、通常、ユーザーの UPN またはメール アドレスです。|AD FS では、証明書利用者の要求規則となっています。 ほとんどの場合、"nameidentifier" で終わる型の要求を発行する要求規則です。|Azure AD では、ユーザー識別子は Azure Portal 内のアプリケーションの **[シングル サインオン]** プロパティの **[ユーザー属性]** ヘッダーの下にあります。</br></br>既定では、UPN が使用されます。|SAML トークンの **NameID** 要素として IdP からアプリに伝達されます。|
|アプリに送信されるその他の要求|ユーザー識別子/**NameID** のほかにも、要求情報が一般的には IdP からアプリに送信されます。 たとえば、名、姓、メール アドレス、ユーザーが所属するグループなどです。|AD FS では、証明書利用者のその他の要求規則となっています。|Azure AD では、Azure Portal のアプリケーションの **[シングル サインオン]** プロパティの **[ユーザー属性]** ヘッダーの下にあります。 **[表示]** を選択し、他のすべてのユーザー属性を編集します。|該当なし|  

### <a name="representing-azure-ad-as-an-identity-provider-in-an-saas-app"></a>SaaS アプリでの ID プロバイダーとしての Azure AD の表現
移行の一環として、オンプレミスの ID プロバイダーではなく Azure AD を使用するようにアプリを構成する必要があります。 このセクションでは、カスタム LOB アプリではなく、SAML プロトコルを使用する SaaS アプリを中心に説明します。 ただし、説明する概念はカスタム LOB アプリにも当てはまります。 

大まかには、SaaS アプリで Azure AD を使用するための重要な点は、次のとおりです。
- ID プロバイダー発行者: https&#58;//sts.windows.net/{tenant-id}/
- ID プロバイダーのログイン URL: https&#58;//login.microsoftonline.com/{tenant-id}/saml2
- ID プロバイダーのログアウト URL: https&#58;//login.microsoftonline.com/{tenant-id}/saml2 
- フェデレーション メタデータの場所: https&#58;//login.windows.net/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={application-id} 

{tenant-id} は、テナント ID に置き換えます。これは、Azure Portal の **[Azure Active Directory]**  >  **[プロパティ]** で **[ディレクトリ ID]** として確認できます。 {application-id} を アプリケーション ID に置き換えます。これは、アプリケーションのプロパティで **[アプリケーション ID]** として確認できます。

次の表では、アプリで SSO 設定を構成するための主要な IdP 構成要素と、その値、または AD FS と Azure AD 内での場所について説明しています。 表の基準となっているのは、SaaS アプリです。SaaS アプリでは、認証要求の送信先と、受信したトークンの検証方法を知っている必要があります。

|構成要素|説明|AD FS|Azure AD|
|---|---|---|---|
|IdP </br>サインオン </br>URL|アプリの観点からの IdP のサインオン URL (ユーザーがログインのためにリダイレクトされる場所)。|AD FS のサインオン URL は、AD FS フェデレーション サービス名に "/adfs/ls/" を付加したものです。 例: https&#58;//fs.contoso.com/adfs/ls/|Azure AD での対応する値は、以下のパターンに従います。{tenant-id} は、テナント ID に置き換えます。 これは、Azure Portal の **[Azure Active Directory]**  >  **[プロパティ]** で **[ディレクトリ ID]** として確認できます。</br></br>SAML-P プロトコルを使用するアプリの場合: https&#58;//login.microsoftonline.com/{tenant-id}/saml2 </br></br>WS-Federation プロトコルを使用するアプリの場合: https&#58;//login.microsoftonline.com/{tenant-id}/wsfed|
|IdP </br>サインアウト </br>URL|アプリの観点からの IdP のサインアウト URL (ユーザーがアプリのサインアウトを選択したときにリダイレクトされる場所)。|AD FS の場合、サインアウト URL はサインオン URL と同じであるか、同じ URL に "wa=wsignout1.0" を付加したものです。 例: https&#58;//fs.contoso.com/adfs/ls/?wa=wsignout1.0|Azure AD での対応する値は、アプリが SAML 2.0 サインアウトをサポートしているかどうかによって異なります。</br></br>アプリが SAML サインアウトをサポートしている場合、値は、以下のパターンに従います。{tenant-id} はテナント ID に置き換えます。 これは、Azure Portal の **[Azure Active Directory]**  >  **[プロパティ]** で **[ディレクトリ ID]** として確認できます (https&#58;//login.microsoftonline.com/{tenant-id}/saml2)。</br></br>アプリが SAML サインアウトをサポートしていない場合: https&#58;//login.microsoftonline.com/common/wsfederation?wa=wsignout1.0|
|トークン </br>署名 </br>証明書|発行されたトークンに署名するために IdP が使用する秘密キーを持つ証明書。 アプリが信頼するように構成されているのと同じ IdP からトークンが来ていることを確認します。|AD FS トークン署名証明書は、[AD FS の管理] の **[証明書]** の下にあります。|Azure AD では、トークン署名証明書は Azure Portal のアプリケーションの **[シングル サインオン]** プロパティの **[SAML 署名証明書]** ヘッダーで確認できます。 ここで、アプリにアップロードするための証明書をダウンロードすることができます。</br></br> アプリケーションに複数の証明書がある場合は、フェデレーション メタデータ XML ファイルですべての証明書を確認することができます。|
|識別子/</br>"発行者"|アプリの観点からの IdP の識別子 ("発行者 ID" と呼ばれる場合もあります)。</br></br>SAML トークンでは、値は **Issuer** 要素として表示されます。|AD FS の識別子は、通常、[AD FS の管理] の **[サービス]**  >  **[フェデレーション サービスのプロパティの編集]** の下にあるフェデレーション サービス識別子です。 例: http&#58;//fs.contoso.com/adfs/services/trust|Azure AD での対応する値は、以下のパターンに従います。{tenant-id} は、テナント ID に置き換えます。 これは、Azure Portal の **[Azure Active Directory]**  >  **[プロパティ]** で **[ディレクトリ ID]** として確認できます (https&#58;//sts.windows.net/{tenant-id}/)。|
|IdP </br>フェデレーション </br>metadata|IdP の一般公開されているフェデレーション メタデータの場所 (一部のアプリでは、管理者によって個別に構成される URL、識別子、およびトークン署名証明書の代わりに、フェデレーション メタデータを使用します)。|AD FS フェデレーション メタデータ URL は、[AD FS の管理] の **[サービス]**  >  **[エンドポイント]**  >  **[メタデータ]**  >  **[種類:フェデレーション メタデータ]** の下で確認できます。 例: https&#58;//fs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml|Azure AD での対応する値は、次のパターンに従います: https&#58;//login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml。 {TenantDomainName} は、"contoso.onmicrosoft.com" という形式のテナントの名前に置き換えます。 </br></br>詳細については、「[フェデレーション メタデータ](../develop/azure-ad-federation-metadata.md)」を参照してください。

## <a name="moving-saas-apps"></a>SaaS アプリの移動
AD FS や他の ID プロバイダーから Azure AD への SaaS アプリの移動は、現時点では手作業で行います。 アプリ固有のガイダンスについては、[Marketplace にある SaaS アプリの統合に関するチュートリアルの一覧](../saas-apps/tutorial-list.md)を参照してください。

統合のチュートリアルでは、統合を一から行うことを前提としています。 アプリの計画、評価、構成、および切り替えの際に知っておく必要がある、移行に固有の主要な概念がいくつかあります。  
- 一部のアプリは簡単に移行することができます。 カスタム要求などのより複雑な要件を持つアプリでは、Azure AD や Azure AD Connect での追加の構成が必要になる場合があります。
- 最も一般的なシナリオでアプリに必要なのは、**NameID** 要求とその他の一般的なユーザー識別子要求だけです。 他にも要求が必要かどうかを判断するには、AD FS またはサード パーティの ID プロバイダーからどの要求が発行されているかを調べます。
- 追加の要求が必要であると判断した場合は、Azure AD でそれが利用可能であることを確認します。 Azure AD Connect の同期構成を調べて、必要な属性 (**samAccountName** など) が Azure AD に同期されていることを確認します。
- 属性が Azure AD で利用可能であれば、Azure AD に要求発行規則を追加して、発行されるトークンに要求としてそれらの属性を含めることができます。 これらの規則は、Azure AD のアプリの **[シングル サインオン]** プロパティで追加します。

### <a name="assess-what-can-be-moved"></a>何を移動できるかの評価
SAML 2.0 アプリケーションは、Marketplace の Azure AD アプリケーション ギャラリーを通じて、または非 Marketplace アプリケーションとして、Azure AD と統合することができます。  

Azure AD で構成するには追加の手順が必要な構成もいくつかあります。また、現時点ではサポートされていない構成もあります。 何を移行できるかを判断するには、各アプリの現在の構成を調べます。 特に、次の項目を確認します。
- 構成されている要求規則 (発行変換規則)。
- SAML **NameID** の形式と属性。
- 発行された SAML トークンのバージョン。
- 発行承認規則やアクセス制御ポリシーなど、その他の構成と、Multi-Factor Authentication (追加認証) 規則。

#### <a name="what-can-be-moved-today"></a>現時点で移動できるもの
現時点で簡単に移動できるのは、構成要素と要求の標準セットを使用する SAML 2.0 アプリなどです。 これらのアプリの構成要素は、次のとおりです。
- ユーザー プリンシパル名。
- 電子メール アドレス。
- 名。
- 姓。
- Azure AD メール属性、メール プレフィックス、従業員 ID、拡張属性 1 から 15、オンプレミスの **SamAccountName** 属性など、SAML **NameID** のような代替属性。 詳細については、「[NameIdentifier 要求の編集](../develop/active-directory-saml-claims-customization.md)」を参照してください。
- カスタム要求。 サポートされている要求のマッピングの詳細については、「[Azure Active Directory での要求のマッピング](../develop/active-directory-claims-mapping.md)」および「[Azure Active Directory のエンタープライズ アプリケーションの SAML トークンで発行された要求のカスタマイズ](../develop/active-directory-saml-claims-customization.md)」を参照してください。

カスタム要求と **NameID** 要素のほかに、移行の一環として Azure AD で追加の構成手順を必要とする構成は、次のとおりです。
- AD FS のカスタム承認または Multi-Factor Authentication 規則。 これらは、[Azure AD 条件付きアクセス](../active-directory-conditional-access-azure-portal.md)機能を使用して構成します。
- 複数の SAML エンドポイントを持つアプリ。 これらは、Azure AD で PowerShell を使用して構成します (この機能は Portal では使用できません)。
- SAML バージョン 1.1 のトークンを必要とする SharePoint アプリなどの WS-Federation アプリ。 これらは、PowerShell を使用して手動で構成する必要があります。

#### <a name="apps-and-configurations-not-supported-in-azure-ad-today"></a>Azure AD で現時点ではサポートされていないアプリと構成
以下の機能を必要とするアプリは、現時点では移行することができません。 これらの機能を必要とするアプリがある場合は、[コメント] セクションからフィードバックを提供してください。
- プロトコル機能:
    - サインインしたすべてのアプリの SAML シングル ログアウト (SLO) のサポート。
    - WS-Trust ActAs パターンのサポート。
    - SAML アーティファクト解決。
    - 署名済み SAML 要求の署名の検証。 署名済み要求は受け入れられますが、署名は検証されないことに注意してください。
- トークン機能: 
    - SAML トークン暗号化。 
    - SAML プロトコル応答内の SAML バージョン 1.1 のトークン。 
- トークン内の要求の機能:
    - 要求としてのオンプレミスのグループ名の発行。
    - Azure AD 以外のストアからの要求。
    - 複雑な要求発行変換規則。 サポートされている要求のマッピングの詳細については、「[Azure Active Directory での要求のマッピング](../develop/active-directory-claims-mapping.md)」および「[Azure Active Directory のエンタープライズ アプリケーションの SAML トークンで発行された要求のカスタマイズ](../develop/active-directory-saml-claims-customization.md)」を参照してください。
    - 要求としてのディレクトリ拡張機能の発行。
    - **NameID** 形式のカスタム仕様。
    - 複数値属性の発行。

>[!NOTE]
>Azure AD は、この領域に機能を追加するために、常に進化し続けています。 Microsoft では、この記事を定期的に更新しています。 

### <a name="configure-azure-ad"></a>Azure AD の構成    
#### <a name="configure-single-sign-on-sso-settings-for-the-saas-app"></a>SaaS アプリのシングル サインオン (SSO) 設定を構成する

Azure AD では、(アプリからの要求に基づく) SAML サインオンの構成は、アプリの **[シングル サインオン]** プロパティの **[ユーザー属性]** の下で行います。

![[シングル サインオン] プロパティの [ユーザー属性] セクション](media/migrate-adfs-apps-to-azure/migrate3.png)

セキュリティ トークン内の要求として送信する属性を表示するには、 **[その他のすべてのユーザー属性を表示および編集する]** を選択します。

![属性の一覧](media/migrate-adfs-apps-to-azure/migrate4.png)

編集する特定の属性行を選択するか、 **[属性の追加]** を選択して新しい属性を追加します。

![[属性の編集] ウィンドウ](media/migrate-adfs-apps-to-azure/migrate5.png)

#### <a name="assign-users-to-the-app"></a>アプリにユーザーを割り当てる
Azure AD のユーザーが SaaS アプリにサインインできるようにするには、そのユーザーにアクセス権を付与する必要があります。

Azure AD ポータルでユーザーを割り当てるには、SaaS アプリのページを参照し、サイドバーの **[ユーザーとグループ]** を選択します。 ユーザーまたはグループを追加するには、ウィンドウ上部の **[ユーザーの追加]** を選択します。 

![[ユーザーとグループ] の [ユーザーの追加] ボタン](media/migrate-adfs-apps-to-azure/migrate6.png) 

![[割り当ての追加] ウィンドウ](media/migrate-adfs-apps-to-azure/migrate7.png)

アクセス権を確認するには、ユーザーがサインインしているときに[アクセス パネル](../user-help/active-directory-saas-access-panel-introduction.md)に SaaS アプリが表示されるかどうかを調べます。 アクセス パネルは https://myapps.microsoft.com にあります。 この例では、ユーザーに Salesforce と ServiceNow の両方へのアクセス権が正常に割り当てられています。

![Salesforce と ServiceNow アプリが表示されているアクセス パネルの例](media/migrate-adfs-apps-to-azure/migrate8.png)

### <a name="configure-the-saas-app"></a>SaaS アプリの構成
オンプレミス フェデレーションから Azure AD への切り替えプロセスは、使用している SaaS アプリが複数の ID プロバイダーをサポートしているかどうかによって異なります。 複数の IdP のサポートに関してよくある質問は、次のとおりです。

   **Q:アプリにとって複数の IdP をサポートすることにはどういう意味がありますか?**
    
   A:複数の IdP をサポートする SaaS アプリでは、サインオンのエクスペリエンスを変更する前に、新しい IdP (ここでは Azure AD) に関するすべての情報を入力することができます。 構成を完了したら、アプリの認証の構成を、Azure AD を使用するように切り替えることができます。

   **Q:SaaS アプリが複数の IdP をサポートしているかどうかが、どうして重要なのですか?**

   A:複数の IdP がサポートされていない場合、管理者はサービスまたはメンテナンスを停止する短い時間枠を確保して、その間に Azure AD をアプリの新しい IdP として構成する必要があります。 この停止中はユーザーが自分のアカウントにサインインできなくなることを、ユーザーに通知する必要があります。

   アプリが複数の IdP をサポートしている場合は、追加の IdP の構成を事前に済ませることができます。 そうすると、管理者が Azure への切り替え時に IdP を変更できます。

   アプリが複数の IdP をサポートしていて、複数の IdP にサインインの認証を同時に処理させるようにした場合、ユーザーは認証を行う IdP をサインイン ページで選択することができます。

#### <a name="example-support-for-multiple-idps"></a>例:複数の IdP のサポート
たとえば、Salesforce では、IDP 構成が **[Settings]\(設定\)**  >  **[Company Settings]\(会社の設定\)**  >  **[My Domain]\(マイ ドメイン\)**  >  **[Authentication Configuration]\(認証の構成\)** にあります。

![Salesforce アプリの [Authentication Configuration]\(認証の構成\) セクション](media/migrate-adfs-apps-to-azure/migrate9.png)

以前に **[ID]**  >  **[シングル サインオンの設定]** で作成した構成があるため、認証構成用の IdP を変更することができます。 たとえば、AD FS から Azure AD に変更できます。 

![認証サービスとしての Azure AD の選択](media/migrate-adfs-apps-to-azure/migrate10.png)

### <a name="optional-configure-user-provisioning-in-azure-ad"></a>省略可能:Azure AD でユーザー プロビジョニングを構成する
Azure AD で SaaS アプリのユーザー プロビジョニングを直接処理するようにする場合は、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](user-provisioning.md)」を参照してください。

## <a name="next-steps"></a>次の手順

- [Azure Active Directory でのアプリケーションの管理](what-is-application-management.md)
- [アプリへのアクセスを管理する](what-is-access-management.md)
- [Azure AD Connect フェデレーション](../hybrid/how-to-connect-fed-whatis.md)
