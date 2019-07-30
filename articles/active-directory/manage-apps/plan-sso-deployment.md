---
title: Microsoft Azure Active Directory シングル サインオンのデプロイ計画
description: 組織内での SSO を計画、デプロイ、管理できるようにガイドします。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: baselden
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5278d504c43688bf064b869982938db52b1b1bf
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/17/2019
ms.locfileid: "67164949"
---
# <a name="plan-a-single-sign-on-deployment"></a>シングル サインオンのデプロイを計画する

シングル サインオン (SSO) とは、1 つのユーザー アカウントを使用して 1 回サインインするだけで、ユーザーが必要なすべてのアプリケーションとリソースにアクセスできる機能です。 SSO を使用すれば、ユーザーは、2 回認証する必要なく、必要なすべてのアプリケーションにアクセスできます。

## <a name="benefits-of-sso"></a>SSO の利点

シングル サインオン (SSO) によって、ユーザーが Azure Active Directory (Azure AD) 内のアプリケーションにサインオンするときのセキュリティと利便性が向上します。 

多くの組織では、エンド ユーザーの生産性向上のため、Office 365、Box、Salesforce などのサービスとしてのソフトウェア (SaaS) アプリケーションに依存しています。 従来は、IT スタッフが各 SaaS アプリケーションのユーザー アカウントを個別に作成して更新する必要があり、ユーザーはそれぞれのパスワードを覚える必要がありました。

Azure Marketplace には、SSO 接続を事前に統合したアプリケーションが 3000 以上あり、お使いのテナントでこれらを簡単に統合できます。

## <a name="licensing"></a>ライセンス

- **Azure AD のライセンス** - 事前統合された SaaS アプリケーション向けの SSO は無料です。 ただし、お使いのディレクトリ内のオブジェクト数とお客様がデプロイしたい機能によっては、追加のライセンスが必要になる場合があります。 ライセンス要件の詳細一覧については、「[Azure Active Directory の価格](https://azure.microsoft.com/pricing/details/active-directory/)」を参照してください。
- **アプリケーション ライセンス** - お客様のビジネス ニーズを満たすためには、お使いの SaaS アプリケーションに適したライセンスが必要になります。 アプリケーション所有者と連携して、アプリケーションに割り当てられたユーザーに、アプリケーション内での自分のロールに適したライセンスが設定されているかどうかを判断します。 Azure AD で、ロールに基づいた自動プロビジョニングを管理している場合、Azure AD で割り当てられているロールの数は、アプリケーション内で所有されているライセンスの数と揃える必要があります。 アプリケーション内で所有されているライセンスの数が適切でない場合、ユーザーのプロビジョニング/更新時にエラーが発生する可能性があります。

## <a name="plan-your-sso-team"></a>SSO チームを計画する

- **適切な利害関係者が担当する** - テクノロジ プロジェクトが失敗した場合、その原因は通常、影響、結果、および責任に対する想定の不一致です。 これらの落とし穴を回避するには、[適切な利害関係者が担当していることを確認](https://aka.ms/deploymentplans)し、その利害関係者が自分のロールを理解していることを確認します。
- **コミュニケーションの計画** - コミュニケーションは、新しいサービスの成功に必要不可欠です。 ユーザー エクスペリエンスが具体的にどのように変わるのか、問題が発生したときにサポートを受ける方法について、ユーザーに事前に連絡します。 [エンドユーザーが、SSO が有効なアプリケーションにアクセスする方法](end-user-experiences.md)に関する選択肢を確認し、お客様の選択に合わせてコミュニケーションを構築します。 

## <a name="plan-your-sso-protocol"></a>SSO プロトコルを計画する

フェデレーション プロトコルに基づく SSO の実装により、セキュリティ、信頼性、エンド ユーザー エクスペリエンスが向上します。また、この SSO は簡単に実装できます。 [用意されたステップ バイ ステップ ガイド](../saas-apps/tutorial-list.md)を使用して、多くのアプリケーションが Azure ADに事前統合されます。 これらは、[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/)で見つけられます。 SSO の方法ごとの詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](what-is-single-sign-on.md)」の記事で確認できます。

ユーザーがアプリにシングル サインオンできるようにする方法は主に 2 つあります。

- **フェデレーション シングル サインオン**では、Azure AD アカウントを使用して、アプリケーションに対するユーザー認証を Azure AD が行います。 この方法は、SAML 2.0、WS-Federation、OpenID Connect などのプロトコルをサポートするアプリケーションでサポートされていて、シングル サインオンのモードのうちで最も多機能です。 パスワード ベースの SSO や ADFS ではなく、この方法をアプリケーションがサポートしている場合は、Azure AD を用いたフェデレーション SSO を使用することをお勧めします。

- **パスワード ベースのシングル サインオン**では、ユーザーは初回アクセス時にユーザー名とパスワードを使用してアプリケーションにサインインします。 最初のサインイン後は、Azure AD がユーザー名とパスワードをアプリケーションに提供します。 パスワードベースのシングル サインオンでは、セキュリティで保護されたアプリケーションのパスワードの保存と、Web ブラウザーの拡張機能またはモバイル アプリを使用した再生が可能になります。 このオプションでは、アプリケーションによって提供される既存のサインイン プロセスが利用され、管理者がパスワードを管理でき、ユーザーがパスワードを知っている必要はありません。

### <a name="considerations-for-federation-based-sso"></a>フェデレーション ベースの SSO に関する考慮事項

- **OpenID Connect と OAuth の使用** - 接続対象のアプリケーションがこれをサポートしている場合は OIDC/OAuth 2.0 の方法を使用して、そのアプリケーションに対する SSO を有効にします。 この方法では、必要な構成は少なくて済み、ユーザー エクスペリエンスはより豊かなものになります。 詳細については、[OAuth 2.0](../develop/v2-oauth2-auth-code-flow.md) に関する記事、[OpenID Connect 1.0](../develop/v2-protocols-oidc.md) に関する記事、[Azure Active Directory 開発者ガイド](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)を参照してください。
- **SAML ベースの SSO のエンドポイント構成** -SAML を使用する場合、開発者は、アプリケーションを構成する前に特定の情報が必要になります。 詳細については、[基本的な SAML オプションの構成](configure-single-sign-on-portal.md)に関する記事を参照してください。
- **SAML ベースの SSO の証明書の管理** - お使いのアプリケーションのフェデレーション SSO を有効にする場合、既定では 3 年間有効な証明書が Azure AD で作成されます。 必要に応じて、その証明書の有効期限をカスタマイズできます。 有効期限前に証明書を更新するための適切なプロセスがあることを確認します。 詳細については、[Azure AD での証明書管理](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs)に関する記事を参照してください。

### <a name="considerations-for-password-based-sso"></a>パスワード ベースの SSO に関する考慮事項

パスワード ベースの SSO で Azure AD を使用するには、安全に資格情報を取得し、ログイン フォームに入力するブラウザー拡張機能をデプロイする必要があります。 [サポートされているブラウザー](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)を使用して、拡張機能を大規模にデプロイするためのメカニズムを定義します。 次のオプションがあります。

- [Internet Explorer のグループ ポリシー](https://azure.microsoft.com/documentation/articles/active-directory-saas-ie-group-policy/)
- [Internet Explorer 用の System Center Configuration Manager (SCCM)](https://docs.microsoft.com/sccm/core/clients/deploy/deploy-clients-to-windows-computers)
- [Chrome、Firefox、Microsoft Edge、IE 用のユーザー主導のダウンロードおよび構成](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

詳細については、[パスワード シングル サインオンの構成](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-password-sso-non-gallery)に関する記事を参照してください。

#### <a name="capturing-login-forms-metadata-for-applications-that-arent-in-the-gallery"></a>ギャラリーにないアプリケーションのログイン フォーム メタデータの取得

Microsoft では、パスワード保管のための Web アプリケーションでのメタデータの取得 (ユーザー名とパスワードのフィールドの取得) をサポートしています。 フォームのメタデータを取得するようにアプリケーションを構成するプロセス中に、ログイン URL に移動します。 正確なログイン URL については、アプリケーション所有者に問い合わせてください。 この情報はサインオンのプロセス中に使用され、サインオン時に、Azure AD の資格情報をアプリケーションにマッピングします。

詳細については、Azure AD でのアプリケーション アクセスと SSO に関する記事の「[パスワードベースの SSO](https://azure.microsoft.com/documentation/articles/active-directory-appssoaccess-whatis/)」を参照してください。

#### <a name="indications-that-metadata-in-forms-needs-to-be-recaptured"></a>フォームのメタデータを再取得する必要があることが示される場合

アプリケーションで HTML レイアウトが変更されると、その変更に合わせて調整するためにメタデータを再取得しなければならない場合があります。 HTML レイアウトに変更があることを示す一般的な症状には、以下のようなものがあります。

- アプリケーションをクリックすると、ログイン ページで "スタック" することがユーザーによって報告される
- ユーザー名またはパスワードが入力されていないことがユーザーによって報告される

#### <a name="shared-accounts"></a>共有アカウント

サインインの観点からは、共有アカウントを使用しているアプリケーションは、個々のユーザーのパスワード SSO を使用しているギャラリー アプリケーションと違いはありません。 ただし、共有アカウントを使用するようにアプリケーションを計画および構成する場合に必要な追加手順がいくつかあります。

1. アプリケーションのビジネス ユーザーと協力して、以下を文書化します。
   1. アプリケーションを使用する組織の一連のユーザー
   1. 一連のユーザーに関連付けられているアプリケーションの既存の資格情報セット 
1. ユーザーのセットと資格情報の組み合わせごとに、要件に基づいて、クラウドまたはオンプレミスにセキュリティ グループを作成します。
1. 共有の資格情報をリセットします。 Azure AD にアプリがデプロイされると、個々のユーザーには共有アカウントのパスワードが不要になります。 Azure AD にパスワードが保存されるので、パスワードを非常に長く、複雑にすることを検討してください。 
1. アプリケーションがサポートしている場合は、パスワードの自動ロールオーバーを構成します。 この方法では、初期セットアップを行った管理者でさえも、共有アカウントのパスワードを知ることはありません。 

## <a name="plan-your-authentication-method"></a>認証方法を計画する

正しい認証方法の選択は、Azure AD ハイブリッド ID ソリューションのセットアップにおける最初の重要な決定です。 Azure AD Connect (クラウドでのユーザーのプロビジョニングも行います) を使用することで構成される認証方法を実装します。

認証方法を選ぶには、時間、既存のインフラストラクチャ、複雑さ、および選んだ方法の実装にかかるコストを考慮する必要があります。 これらの要因は組織ごとに異なり、時間の経過とともに変化する場合があります。 特定のシナリオに最も合うものを選択する必要があります。 詳細については、「[Azure Active Directory ハイブリッド ID ソリューションの適切な認証方法を選択する](https://docs.microsoft.com/azure/security/azure-ad-choose-authn)」を参照してください。

## <a name="plan-your-security-and-governance"></a>セキュリティとガバナンスを計画する 

BYOD デバイスとクラウド アプリケーションの爆発的な増加に伴い、ネットワーク境界はますます侵入しやすくなり、有効性が低下してきているため、ID は、セキュリティへの関心と投資のための新しい主軸となっています。 

### <a name="plan-access-reviews"></a>アクセス レビューを計画する

[アクセス レビュー](https://docs.microsoft.com/azure/active-directory/governance/create-access-review)を組織で使用することにより、グループ メンバーシップ、エンタープライズ アプリケーションへのアクセス、およびロールの割り当てを効率的に管理できます。 ユーザーのアクセスを定期的にレビューすることを計画し、適切なユーザーのみが継続的なアクセス権を持つようにします。

アクセス レビューの設定中に計画する主要なトピックをいくつかご紹介します。

1. ビジネス ニーズに合ったアクセス レビューの頻度の特定。 この頻度は、週 1 回、月 1 回、年 1 回、またはオンデマンドにできます。

1. アプリのアクセス レポートのレビュー担当者を表すグループの作成。 アプリとその対象ユーザーとユース ケースを最も使い慣れた利害関係者が、アクセス レビューの参加者であることを確認する必要があります

1. アクセス レビューを完了させる作業には、アクセスが不要になったユーザーからアプリのアクセス許可を取り消すことが含まれます。 拒否されたユーザーからの潜在的なサポート要求の処理を計画します。 削除されたユーザーは Azure AD に 30 日間、削除状態で維持されます。その間は、管理者が必要に応じて復元することができます。 30 日後、そのユーザーは完全に削除されます。 その期間に達していなくても、全体管理者は Azure Active Directory ポータルを使用して、最近削除したユーザーを明示的に完全削除することができます。

### <a name="plan-auditing"></a>監査を計画する

Azure AD には、[技術やビジネスのインサイトを含むレポート](https://azure.microsoft.com/documentation/articles/active-directory-view-access-usage-reports/)が用意されています。 

セキュリティとアクティビティの両方のレポートを使用できます。 セキュリティ レポートでは、リスクのフラグ付きユーザーとリスクの高いサインインが示されます。アクティビティ レポートは、サインイン アクティビティを詳細化し、すべてのログインの監査証跡を提供することによって、組織内のユーザーの行動を理解するのに役立ちます。 レポートを使用して、リスクの管理、生産性の向上、およびコンプライアンスの監視を行うことができます。

| レポートの種類 | アクセス レビュー | セキュリティ レポート | サインイン レポート |
|-------------|---------------|------------------|----------------|
| 確認用 | アプリケーションのアクセス許可と使用状況。 | 侵害された可能性があるアカウント | アプリケーションにアクセス中のユーザー |
| 潜在的なアクション | アクセスの監査。アクセス許可の取り消し | アクセス権の取り消し。セキュリティのリセットの強制実行 | アクセス権の取り消し |

Azure AD は、ほとんどの監査データを30 日間保持し、分析システムにダウンロードできるように、Azure 管理ポータルまたは API を通じてデータを使用可能にします。

### <a name="consider-using-microsoft-cloud-application-security"></a>Microsoft Cloud Application Security の使用を検討する

Microsoft Cloud App Security (MCAS) は、Cloud Access Security Broker (CASB) ソリューションです。 このソリューションは、クラウド アプリおよびサービスを可視化し、サイバー脅威を特定して対処するための高度な分析を提供し、データの移動の制御を可能にします。

MCAS をデプロイすると、以下のことができるようになります。

- Cloud Discovery を使用して、組織が使用しているクラウド環境とクラウド アプリをマップして識別する。
- クラウドでのアプリの使用を承認および禁止する
- 接続するアプリの詳細を把握して管理するために、簡単にデプロイでき、プロバイダー API を活用するアプリ コネクタを使用する
- アプリの条件付きアクセス制御の保護を使用して、クラウド アプリにおけるリアルタイムの可視化と、アクセスおよびアクティビティの制御を実現する
- ポリシーを設定して、その後の微調整を行うことで、継続的な制御を可能にする

Microsoft Cloud Application Security (MCAS) のセッション制御は、任意のオペレーティング システムの主要なプラットフォーム上で任意のブラウザーで使用できます。 モバイル アプリとデスクトップ アプリも、ブロックや許可をすることができます。 Azure AD とネイティブに統合することによって、SAML で構成されたアプリまたは Azure AD でのシングル サインオンを使用した Open ID Connect アプリをサポートできます。これには、[こちらでご紹介するいくつかのアプリ](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)が含まれます。

MCAS に関する詳細については、「[Microsoft Cloud App Security の概要](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security)」を参照してください。 MCAS は、ユーザー ベースのサブスクリプション サービスです。 [MCAS ライセンス データシート](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE2NXYO)でライセンスの詳細を確認できます。

### <a name="use-conditional-access"></a>条件付きアクセスを使用する

条件付きアクセスを使用すると、クラウド アプリに対して、条件に基づくアクセス制御の決定を自動化できます。

条件付きアクセス ポリシーは、第 1 段階認証が完了した後で適用されます。 このため、条件付きアクセスはサービス拒否 (DoS) 攻撃などのシナリオに対する防御の最前線を意図したものではありませんが、これらのイベントからのシグナルを使用してアクセスを判別できます。 たとえば、サインインのリスク レベルや要求の場所などを使用できます。 条件付きアクセスの詳細については、[概要](https://docs.microsoft.com/azure/active-directory/conditional-access/plan-conditional-access)に関する記事と[デプロイ計画](https://docs.microsoft.com/azure/active-directory/conditional-access/plan-conditional-access)に関する記事を参照してください。

## <a name="azure-sso-technical-requirements"></a>Azure SSO の技術的な要件

次のセクションでは、必要な環境、エンドポイント、要求のマッピング、必要な属性、証明書、使用するプロトコルなど、特定のアプリケーションを構成するための要件について詳しく説明します。 [Azure AD ポータル](https://portal.azure.com/)で SSO を構成するには、この情報が必要になります。

### <a name="authentication-mechanism-details"></a>認証メカニズムの詳細

事前に統合されたすべての SaaS アプリにはチュートリアルが提供されており、この情報は必要ありません。 アプリケーションがアプリケーション マーケットプレース/ギャラリーにない場合、次のデータを収集する必要があります。

- **アプリケーションが SSO で使用する現在の ID プロバイダー (該当する場合)** - 例:AD FS、PingFederate、Okta
- **対象のアプリケーションがサポートしているプロトコル** -　SAML 2.0、OpenID Connect、OAuth、フォーム ベース認証、WS-Fed、WS-Trust など
- **Azure AD で構成されているプロトコル** - SAML 2.0 または 1.1、OpenID Connect、OAuth、フォーム ベース、WS-Fed など

### <a name="attribute-requirements"></a>属性の要件

Azure AD ユーザー オブジェクトと各 SaaS アプリのユーザー オブジェクトの間には、構成済みの一連の属性と属性マッピングが存在します。 アプリによっては、グループなどの他の種類のオブジェクトを管理するものもあります。 Azure AD からアプリケーションへのユーザー属性のマッピングを計画し、ビジネス ニーズに合わせて[既定の属性マッピングをカスタマイズ](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)します。

### <a name="certificate-requirements"></a>証明書の要件

アプリケーションの証明書を最新の状態にする必要があります。そうでない場合、ユーザーがアプリケーションにアクセスできなくなるおそれがあります。 ほとんどの SaaS アプリケーションの証明書は、36 か月間有効です。 アプリケーション ブレードでその証明書の期間を変更します。 必ず有効期限を記録し、証明書の更新を管理する方法を把握してください。 

証明書を管理する方法は 2 つあります。 

- **証明書の自動ロールオーバー** - Microsoft では、[Azure AD の署名キーのロールオーバー](https://docs.microsoft.com/azure/active-directory/develop/active-directory-signing-key-rollover)をサポートしています。 これは、証明書を管理する上では好ましい方法ですが、すべての ISV がこのシナリオをサポートしているわけではありません。

- **手動による更新** - すべてのアプリケーションには、定義された方法に基づいて有効期限が切れる固有の証明書があります。 アプリケーションの証明書の有効期限が切れる前に、新しい証明書を作成して、ISV に送信します。 この情報は、フェデレーション メタデータから取得できます。 [こちらでフェデレーション メタデータの詳細をお読みください。](https://docs.microsoft.com/azure/active-directory/develop/active-directory-federation-metadata)

## <a name="implement-sso"></a>SSO を実装する

次のフェーズを使用して、ソリューションの計画を立て、組織にデプロイします。

### <a name="user-configuration-for-sso"></a>SSO のユーザー構成

- **テスト ユーザーの特定**

   アプリ所有者に連絡して、アプリケーション内に最低でも 3 人のテスト ユーザーを作成するように依頼します。 プライマリ ID として使用する情報が正しく入力されていて、Azure AD で使用できる属性と合うことを確認します。 ほとんどのケースで、これは、SAML ベースのアプリケーションの "NameID" にマッピングされます。 JWT トークンでは "preferred_username" にあたります。
   
   クラウド ベースのユーザーとして手動で Azure AD にユーザーを作成するか、Azure AD Connect 同期エンジンを使用してオンプレミスからユーザーを同期します。 情報がアプリケーションに送信される要求と一致することを確認します。

- **SSO の構成**

   [アプリケーションの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)からお使いのアプリケーションの SSO チュートリアルを見つけて開き、SaaS アプリケーションを正常に構成するためのチュートリアルの手順に従います。

   お使いのアプリケーションが見つからない場合は、[カスタム アプリケーションに関するドキュメント](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery)を参照してください。 ここでは、Azure AD ギャラリーにないアプリケーションを追加する方法を学びます。

   必要に応じて、[Microsoft のガイダンス ドキュメント](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping)を使用して、エンタープライズ アプリケーションの SAML トークンで発行された要求を使用できます。 これが、お使いのアプリケーションの SAML 応答で受信されると想定されるものにマッピングされていることを確認します。 構成中に問題が発生した場合は、[SSO 統合をデバッグする方法](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)に関するガイダンスを使用します。

カスタム アプリケーションのオンボードは、Azure AD Premium P1 または P2 のライセンス機能です。

### <a name="provide-sso-change-communications-to-end-users"></a>エンドユーザーに SSO の変更を連絡する

コミュニケーションの計画を実装します。 変更が行われる予定であること、変更が行われたときに実行する操作、サポートを要請する方法をエンドユーザーに必ず通知します。

### <a name="verify-end-user-scenarios-for-sso"></a>SSO のエンド ユーザー シナリオを確認する

次のテスト ケースを使用して、会社所有のデバイスと個人デバイスでテストを実行し、SSO 構成が想定通りに動作することを確認できます。 以下のシナリオでは、ユーザーがアプリケーション URL に移動して、サービス プロバイダーによって開始される認証フロー (SP によって開始される認証フロー) を進めていることを前提とします。

| シナリオ | SP によって開始される認証フローでユーザーが想定する結果 |
|----------|---------------------------------------------------|
| 企業ネットワークに接続中に IE を使用してアプリケーションにログインする。 | 追加のプロンプトなしで統合 Windows 認証 (IWA) が実行されます。 |
| 企業ネットワークに未接続の状態で、新しいログインを試行して、IE を使用してアプリケーションにログインする。 | AD FS サーバーでのフォーム ベース プロンプト。 ユーザーは正常にログインし、ブラウザーに MFA のプロンプトが表示されます。 |
| 企業ネットワークに未接続の状態で、IE を使用して現在のセッションでアプリケーションにログインする (MFA は未実行)。 | ユーザーは最初の要素のプロンプトを受け取りません。 ユーザーは MFA のプロンプトを受け取ります。 |
| 企業ネットワークに未接続の状態で、IE を使用して現在のセッションでアプリケーションにログインする (MFA を既に実行済み)。 | ユーザーは最初の要素のプロンプトを受け取りません。 ユーザーは MFA を受け取りません。 ユーザーはアプリケーションに SSO します。 |
| 企業ネットワークに未接続の状態で、Chrome/Firefox/Safari を使用して現在のセッションでアプリケーションにログインする (このセッションで MFA をすでに実行済み)。 | ユーザーは最初の要素のプロンプトを受け取りません。 ユーザーは MFA を受け取りません。 ユーザーはアプリケーションに SSO します。 |
| 企業ネットワークに未接続の状態で、新しいログインを試行して、Chrome/Firefox/Safari を使用してアプリケーションにログインする。 | AD FS サーバーでのフォーム ベース プロンプト。 ユーザーは正常にログインし、ブラウザーに MFA のプロンプトが表示されます。 |
| 企業ネットワークに接続中に、Chrome/Firefox を使用して現在のセッションでアプリケーションにログインする。 | ユーザーは最初の要素のプロンプトを受け取りません。 ユーザーは MFA を受け取りません。 ユーザーはアプリケーションに SSO します。 |
| アプリケーション モバイル アプリで新しいログインを試行してアプリケーションにログインする。 | AD FS サーバーでのフォーム ベース プロンプト。 ユーザーは正常にログインし、ADAL クライアントに MFA のプロンプトが表示されます。 |
| 承認されていないユーザーがログイン URL を使用してアプリケーションへのログインを試行する。 | AD FS サーバーでのフォーム ベース プロンプト。 ユーザーは最初の要素でログインに失敗します。 |
| 承認されたユーザーが、正しくないパスワードを入力してログインを試行する。 | ユーザーはアプリケーション URL に移動し、不正なユーザー名/パスワードによるエラーを受け取ります。 |
| 承認されたユーザーが電子メールのリンクをクリックする (ユーザーは既に認証済み)。 | ユーザーが URL をクリックすると、追加のプロンプトなしでアプリケーションにサインインします。 |
| 承認されたユーザーが電子メールのリンクをクリックする (ユーザーは未認証)。 | ユーザーが URL をクリックすると、最初の要素で認証するためのプロンプトが表示されます。 |
| 承認されたユーザーがアプリケーション モバイル アプリ (SP によって開始) を使用して、新しいログインを試行してアプリケーションにログインする。 | AD FS サーバーでのフォーム ベース プロンプト。 ユーザーは正常にログインし、ADAL クライアントに MFA のプロンプトが表示されます。 |
| 承認されていないユーザーがログイン URL (SP によって開始) を使用してアプリケーションへのログインを試行する。 | AD FS サーバーでのフォーム ベース プロンプト。 ユーザーは最初の要素でログインに失敗します。 |
| 承認されたユーザーが、正しくないパスワードを入力してログインを試行する。| ユーザーはアプリケーション URL に移動し、不正なユーザー名/パスワードによるエラーを受け取ります。 |
| 承認されたユーザーがログアウトした後、再度ログインする。 | サインアウト URL が構成されている場合、ユーザーはすべてのサービスからログアウトし、認証のためのプロンプトが表示されます。 |
| 承認されたユーザーがログアウトした後、再度ログインする。 | サインアウト URL が構成されていない場合、ユーザーは、既存の Azure AD のブラウザー セッションの既存のトークンを使用して自動的に再ログインします。 |
| 承認されたユーザーが電子メールのリンクをクリックする (ユーザーは既に認証済み)。 | ユーザーが URL をクリックすると、追加のプロンプトなしでアプリケーションにサインインします。 |
| 承認されたユーザーが電子メールのリンクをクリックする (ユーザーは未認証)。 | ユーザーが URL をクリックすると、最初の要素で認証するためのプロンプトが表示されます。 |

## <a name="manage-sso"></a>SSO を管理する

このセクションでは、SSO を正常に管理するための要件と推奨事項について説明します。

### <a name="required-administrative-roles"></a>必要な管理者ロール

必ず、Azure Active Directory 内で必要なタスクの実行に使用できる最小限のアクセス許可を持つロールを使用してください。 Microsoft では、[使用できるさまざまなロールを確認](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)して、このアプリケーションの各ペルソナのニーズを満たすのに適したものを選択することをお勧めします。 一部のロールは、一時的に適用され、デプロイが完了した後に削除する必要があります。

| ペルソナ| ロール | Azure AD ロール (必要な場合) |
|--------|-------|-----------------------------|
| ヘルプ デスク管理者 | Tier 1 サポート | なし |
| ID 管理者 | Azure AD に影響を与える問題が発生した場合の構成とデバッグ | 全体管理者 |
| アプリケーション管理者 | アプリケーションでのユーザーの構成証明、アクセス許可を持つユーザーの構成 | なし |
| インフラストラクチャ管理者 | 証明書のロール オーバー所有者 | 全体管理者 |
| ビジネスの所有者/利害関係者 | アプリケーションでのユーザーの構成証明、アクセス許可を持つユーザーの構成 | なし |

[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) (PIM) を使用してロールを管理し、ディレクトリのアクセス許可を持つユーザーに対する追加の監査、制御、およびアクセス レビューを提供することをお勧めします。

### <a name="sso-certificate-lifecycle-management"></a>SSO 証明書のライフサイクル管理

Azure AD とシングル サインオンが構成されているアプリケーションの間で署名証明書のライフ サイクルを管理するための適切なロールと電子メール配布リストを特定することが重要です。 設定することが推奨される主要なロールの一部を以下に示します。

- アプリケーションでユーザーのプロパティを更新する所有者
- アプリケーションの中断/修正サポートに対応する所有者
- 証明書関連の変更通知用の電子メールの配布リスト (常時注意深く監視)

証明書の最長有効期間は、3 年間です。 Azure AD とお使いのアプリケーションの間で証明書の変更を処理する方法について、プロセスを確立することをお勧めします。 これは、証明書の期限切れによる停止を防止または最小限に抑えたり、証明書のロールオーバーを強制的に行ったりするのに役立ちます。

### <a name="rollback-process"></a>ロールバック プロセス

テスト ケースに基づいてテストを完了したら、お使いのアプリケーションを運用環境に移行します。 運用環境への移行は、計画とテストを行った構成を運用環境のテナントに実装して、ユーザーにロールアウトすることを意味します。 ただし、デプロイが想定どおりに行われなかった場合の対処法を計画しておくことが重要です。 デプロイ時に SSO の構成に失敗した場合に、停止を最小限にし、ユーザーへの影響を軽減する方法を把握する必要があります。

アプリケーションでの使用可能な認証方法により、最適な戦略が決まります。 デプロイで問題が発生した場合に、アプリ所有者が元のログイン構成の状態に戻すための正確な方法が記載された詳細なドキュメントを必ず用意しておきます。

- たとえば、LDAP と AD FS と Ping など、**お使いのアプリが複数の ID プロバイダーをサポートしている場合**、ロールアウト中に既存の SSO 構成を削除しないでください。 代わりに、後で戻す必要性が生じた場合に備えて、移行中は無効にします。 

- **お使いのアプリが複数の IDP をサポートしていない**ものの、ユーザーがフォーム ベース認証 (ユーザー名/パスワード) を使用してログインできる場合、新しい SSO 構成のロールアウトに失敗したときはこの手法にフォールバックできるようにします。

### <a name="access-management"></a>アクセス管理

リソースへのアクセスを管理する場合には、拡張された方法を選ぶことをお勧めします。 一般的な方法には、Azure AD Connect 経由で同期したオンプレミス グループの使用、[ユーザー属性に基づいた Azure AD での動的グループの作成](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal)、リソース所有者によって管理される[セルフサービス グループ](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management)の作成があります。

### <a name="monitor-security"></a>セキュリティを監視する

SaaS アプリのセキュリティのさまざまな側面を確認する定期的な頻度を設定して、必要な対策を実施することをお勧めします。

### <a name="troubleshooting"></a>トラブルシューティング

次のリンクでは、トラブルシューティングのシナリオが紹介されています。 必要に応じて、これらのシナリオや修正手順を記載したサポート スタッフ向け専用ガイドを作成することをお勧めします。

#### <a name="consent-issues"></a>同意に関する問題

- [予期しない同意エラー](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-unexpected-user-consent-prompt)

- [ユーザーの同意エラー](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-unexpected-user-consent-error)

#### <a name="sign-in-issues"></a>サインインの問題

- [カスタム ポータルからのサインインに関する問題](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-other-problem-deeplink)

- [アクセス パネルからのサインインに関する問題](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-other-problem-access-panel)

- [アプリケーションのサインイン ページでのエラー](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-application-error)

- [Microsoft アプリケーションへのサインインに関する問題](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-first-party-microsoft)

#### <a name="sso-issues-for-applications-listed-in-the-azure-application-gallery"></a>Azure アプリケーション ギャラリーのアプリケーションの SSO の問題

- [Azure アプリケーション ギャラリーのアプリケーションのパスワード SSO に関する問題](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-gallery) 

- [Azure アプリケーション ギャラリーのアプリケーションのフェデレーション SSO に関する問題](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-federated-sso-gallery)   

#### <a name="sso-issues-for-applications-not-listed-in-the-azure-application-gallery"></a>Azure アプリケーション ギャラリー以外のアプリケーションの SSO の問題

- [Azure アプリケーション ギャラリー以外のアプリケーションのパスワード SSO に関する問題](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-non-gallery) 

- [Azure アプリケーション ギャラリー以外のアプリケーションのフェデレーション SSO に関する問題](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-federated-sso-non-gallery)

## <a name="next-steps"></a>次の手順

[SAML ベースの SSO をデバッグする](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)

[PowerShell 経由でのアプリの要求のマッピング](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping)

[SAML トークンで発行された要求のカスタマイズ](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

[シングル サインオンの SAML プロトコル](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)

[シングル サインアウトの SAML プロトコル](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-out-protocol-reference)

[Azure AD B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b) (パートナーやベンダーなどの外部ユーザー用)

[Azure AD 条件付きアクセス](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)

[Azure Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)

[SSO アクセス](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

[アプリケーション SSO チュートリアル](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)
