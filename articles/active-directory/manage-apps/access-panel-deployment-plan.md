---
title: Azure Active Directory アクセス パネルのデプロイを計画する
description: Azure AD アクセス パネル機能のデプロイに関するガイダンス
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2019
ms.author: baselden
ms.reviewer: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 448af7075297c2b18df1eceaeaac34d5da762489
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575966"
---
# <a name="plan-an-azure-active-directory-access-panel-deployment"></a>Azure Active Directory アクセス パネルのデプロイを計画する

Azure Active Directory アクセス パネルは、サポート コストを削減し、生産性とセキュリティを向上させ、ユーザーのストレスを軽減する Web ベースのポータルです。 このシステムには、ユーザーがいつシステムにアクセスしたかを追跡し、誤用または悪用を管理者に通知する詳細なレポート機能が含まれています。

Azure Active Directory アクセス パネルを使用して、ユーザーは以下のことができます。

* アプリケーションなど、会社の Azure Active Directory 接続済みリソースをすべて検出し、それらにアクセスします。
* 新しいアプリやグループへのアクセスを要求するか、他のユーザーによるこれらのリソースへのアクセスを管理します。
* セルフサービスによるパスワードのリセットと多要素認証の設定を管理します。
* 各自のデバイスを管理します。

また、管理者が以下を管理できるようにします。

* サービス使用条件
* 組織
* アクセス レビュー


## <a name="benefits-of-azure-active-directory-access-panel-integration"></a>Azure Active Directory アクセス パネル統合のベネフィット

Microsoft Azure Active Directory (Azure AD) アクセス パネルは、以下の面で企業にベネフィットをもたらします。

**直感的なユーザー エクスペリエンスを提供**:アクセス パネルを有効にすると、ユーザーは、すべての Azure シングル サインオン接続済みアプリケーションに対して単一の起動パッドを使用できるようになります。 グループ管理やセルフサービスによるパスワードのリセットなどの機能が追加されても、ユーザーは今までと同様に、統合ポータルからこれらの設定にアクセスできます。 直感的なエクスペリエンスにより、ユーザーはより迅速に作業を再開できるので、生産性が向上し、ストレスが軽減されます。

**生産性の向上**:アクセス パネル内のすべてのユーザー アプリケーションはシングル サインオン (SSO) が有効になっています。 エンタープライズ アプリケーションおよび Office 365 でシングル サインオンを有効にすると、追加のサインイン プロンプトを減らすかなくすことで、より優れたサインイン エクスペリエンスを既存のユーザーに提供できます。 アクセス パネルは、セルフサービスによる動的なメンバーシップを可能にします。また、アプリケーションへのアクセスが適切な担当者によって管理されるのを保証することで、ID システムの全体的なセキュリティを向上させます。 アクセス パネルは、ユーザーがすばやくリソースを見つけて仕事上のタスクを続行するための一貫したランディング ページとして機能します。

**コストの管理**:Azure Active Directory でアクセス パネルを有効にすることで、オンプレミスのインフラストラクチャの処分が可能になる場合があります。 ユーザーは一貫性のあるポータルを使用して、すべてのアプリの検索、リソースへのアクセスの要求、アカウントの管理を実行できるようになるため、サポート コストが削減されます。

**柔軟性とセキュリティの向上**:アクセス パネルを使用すると、クラウド プラットフォームが提供するセキュリティと柔軟性にアクセスできます。 管理者は、ユーザーに影響を与えることなく、アプリケーションやリソースの設定を簡単に変更したり、新しいセキュリティ要件に対応したりできます。

**堅牢な監査と使用状況追跡の実現**:すべてのエンドユーザー機能を監査し、使用状況を追跡することによって、ユーザーが各自のリソースを使用している時間帯を把握し、セキュリティを正確に評価することができます。

### <a name="licensing-considerations"></a>ライセンスに関する考慮事項

アクセス パネルは無料であり、すべてのユーザーがライセンス不要で基本的な機能を使用できます。 ただし、お使いのディレクトリ内のオブジェクト数とお客様がデプロイしたい機能によっては、追加のライセンスが必要になる場合があります。 一般的な Azure AD のシナリオに含まれる以下のセキュリティ機能には、ライセンスの要件があります。

* [Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-how-it-works)

* [グループ ベースのメンバーシップ](https://docs.microsoft.com/azure/active-directory/active-directory-manage-groups)

* [セルフサービスのパスワード要件](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr)

* [Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)

[Azure Active Directory の完全なライセンス ガイド](https://azure.microsoft.com/pricing/details/active-directory/)に関する記事を参照してください。

### <a name="prerequisites-for-deploying-the-azure-ad-access-panel"></a>Azure AD アクセス パネルをデプロイするための前提条件

このプロジェクトを開始する前に、前提条件として以下の整備が完了している必要があります。

* [アプリケーション SSO 統合](https://docs.microsoft.com/azure/active-directory/manage-apps/plan-sso-deployment)

* [Azure AD のユーザーとグループのインフラストラクチャ](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-manage-groups)

## <a name="plan-the-azure-ad-access-panel-deployment"></a>Azure AD アクセス パネルのデプロイを計画する

次の表は、アクセス パネルのデプロイの主なユース ケースの概要です。

| 領域| 説明 |
| - | - |
| Access| 企業ネットワーク内の会社および個人のデバイスからアクセス パネル ポータルにアクセスできます。 |
|Access | 企業ネットワークの外部にある会社のデバイスからアクセス パネル ポータルにアクセスできます。 |
| 監査| 使用状況データは、少なくとも 29 日に一度、企業システムにダウンロードされます。 |
| ガバナンス| Azure AD に接続済みのアプリケーションとグループへのユーザー割り当てのライフサイクルが定義され、監視されます。 |
| セキュリティ| ユーザーとグループの割り当てによって、リソースへのアクセスが制御されます。 許可されているユーザーのみがリソース アクセスを管理できます。 |
| パフォーマンス| アクセス割り当ての伝達タイムラインが文書化され、監視されます。 |
| ユーザー エクスペリエンス| ユーザーはアクセス パネルの機能と使用方法を理解しています。|
| ユーザー エクスペリエンス| ユーザーはアプリケーションとグループへのアクセスを自分で管理できます。|
| ユーザー エクスペリエンス| ユーザーは自分のアカウントを管理できます。 |
| ユーザー エクスペリエンス| ユーザーはブラウザーの互換性を理解しています。 |
| サポート| ユーザーはアクセス パネルの問題のサポートを見つけることができます。 |

### <a name="best-practices-for-deploying-the-azure-ad-access-panel"></a>Azure AD アクセス パネルのデプロイのベスト プラクティス

アクセス パネルの機能は、段階的に有効にすることができます。 以下の順序でデプロイすることをお勧めします。

1. マイ アプリ
   * アプリ起動ツール
   * セルフサービス アプリの管理
   * Microsoft Office 365 統合

1. セルフサービスのアプリ検出
   * セルフサービスのパスワード リセット
   * 多要素認証設定
   * デバイス管理
   * 使用条件
   * 組織の管理

1. 自分のグループ
   * セルフサービスのグループ管理
1. アクセス レビュー
   * アクセス レビュー管理

[マイ アプリ] を出発点に、リソースにアクセスするための共通の場所であるポータルにユーザーを誘導します。 [マイ アプリ] のエクスペリエンスの延長として、セルフサービスによるアプリケーションの検出を追加します。 [マイ グループ] とアクセス レビューは、セルフサービス機能の延長上にあります。

### <a name="plan-configurations-for-the-azure-ad-access-panel"></a>Azure AD アクセス パネルの構成を計画する

次の表は、アクセス パネルの重要な構成と、使用する可能性がある一般的な値の一覧です。

| 構成| 一般的な値 |
| - | - |
| パイロット グループを決定する| 使用する Azure AD セキュリティ グループを特定し、すべてのパイロット メンバーがグループに属していることを確認します。 |
| 運用環境で有効にするグループを決定する| 使用する Azure AD セキュリティ グループ、または Azure AD に同期される AD グループを特定します。 すべてのパイロット メンバーがグループに属していることを確認します。 |
| どの種類のアプリケーションに対してシングル サインオンの使用をユーザーに許可するか| フェデレーション SSO、OAuth、パスワード SSO、アプリ プロキシ |
| セルフサービスによるパスワードのリセットの使用をユーザーに許可する| はい |
| 多要素認証の使用をユーザーに許可する| はい |
| どの種類のグループに対してセルフサービスによるグループの管理の使用をユーザーに許可するか| セキュリティ グループ、Office 365 グループ |
| セルフサービスによるアプリの管理の使用をユーザーに許可する| はい |
| アクセス レビューの使用をユーザーに許可する| はい |

### <a name="plan-consent-strategy"></a>同意を得るための戦略を計画する

アプリケーションの使用条件やプライバシー ポリシーがある場合、ユーザーまたは管理者がそれらに同意する必要があります。 ビジネス ルール上可能であれば、管理者の同意を使用した方がユーザー エクスペリエンスが向上するため、そちらをお勧めします。

管理者の同意を使用するには、テナントのグローバル管理者である必要があり、かつ、アプリケーションが以下の条件を満たしている必要があります。

* ご利用のテナントで登録されているか、

* 別の Azure AD テナントで登録されており、かつ、少なくとも 1 人のエンドユーザーから以前に同意を得ています。

詳細については、「[Azure Active Directory でエンドユーザーがアプリケーションに同意する方法を構成する](configure-user-consent.md)」を参照してください。

### <a name="engage-the-right-stakeholders"></a>適切な関係者を関わらせる

テクノロジ プロジェクトが失敗した場合、その原因は通常、影響、結果、および責任に対する想定の不一致です。 これらの落とし穴を回避するには、[適切な利害関係者が担当していることを確認](../fundamentals/active-directory-deployment-plans.md)し、プロジェクトにおけるその利害関係者の役割がよく理解されていることを確認します。

### <a name="plan-communications"></a>連絡を計画する

コミュニケーションは、新しいサービスの成功に必要不可欠です。 ユーザーのエクスペリエンスがいつ、どのように変化するか、また必要に応じてサポートを受ける方法を事前にユーザーに通知します。

通常、アクセス パネルが原因でユーザー側の問題が発生することはありませんが、準備をすることは重要です。 立ち上げの前に、サポート担当者向けのガイドおよび全リソースの一覧を作成します。

#### <a name="communications-templates"></a>連絡テンプレート

Microsoft は、アクセス パネルに関する[電子メールやその他の連絡用のカスタマイズ可能なテンプレート](https://aka.ms/APTemplates)を提供しています。 これらのアセットは、他の連絡チャネルで使用するために、企業のカルチャに合わせて改変することができます。

## <a name="plan-your-sso-configuration"></a>自分の SSO 構成を計画する

アプリケーションにサインインしたユーザーは、認証プロセスを経て自分の身元を証明する必要があります。 シングル サインオンを使用しない場合、パスワードはアプリケーションに保存され、ユーザーはこのパスワードを知っている必要があります。 シングル サインオン (SSO) を使用する場合、ユーザーの資格情報がアプリケーションに渡されるので、ユーザーはアプリケーションごとにパスワードを再入力する必要はありません。

[マイ アプリ] でアプリケーションを起動するには、アプリケーションに対してシングル サインオン (SSO) が有効になっている必要があります。

Azure AD では、[アプリケーションへのシングル サインオン](what-is-single-sign-on.md)を有効にする方法として次の 3 つをサポートしています。

* **フェデレーション シングル サインオン** 
    * アプリケーションがパスワードの入力を求める代わりに、ユーザー認証のために Azure AD にリダイレクトできるようにします。 
    * SAML 2.0、WS-Federation、OpenID Connect などのプロトコルを使用するアプリケーションでサポートされていて、シングル サインオンのモードのうちで最も多機能です。

* **パスワードベースのシングル サインオン** 
    * セキュリティで保護されたアプリケーションのパスワードの保存と、Web ブラウザーの拡張機能またはモバイル アプリを使用した再生が可能になります。 
    * アプリケーションによって提供される既存のサインイン プロセスが利用されますが、管理者がパスワードを管理できるようになります。 ユーザーがパスワードを知っている必要はありません。

* **既存のシングル サインオン** 
    * アプリケーション用に構成されている既存のシングル サインオンを Azure AD で利用できるようにします。
    * これらのアプリケーションを Office 365 または Azure AD のアクセス パネル ポータルにリンクできるようにします。 
    * アプリケーションがそこで起動されたときの Azure AD の追加レポートを有効にします。 
    * Azure アプリケーション プロキシと、"リンク" のシングル サインオン モードの使用が含まれます。

アプリケーションの SSO モードの構成方法については、次を参照してください: [Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

[マイアプリ] ページで最適なエクスペリエンスを実現するためには、フェデレーション SSO が使用できるクラウド アプリケーションの統合から始めることをお勧めします。 フェデレーション SSO を使用すると、ユーザーはアプリを起動している間に一貫したワンクリック エクスペリエンスが得られ、構成制御がより堅牢になる傾向があります。

パスワード ベースの SSO や ADFS ではなく、この方法をアプリケーションがサポートしている場合は、Azure AD (OpenID Connect/SAML) を用いたフェデレーション SSO を使用します。

SaaS アプリケーションをデプロイおよび構成する方法の詳細については、[SaaS SSO デプロイ計画](https://aka.ms/deploymentplans/sso)に関するページを参照してください。

#### <a name="plan-to-deploy-the-my-apps-browser-extension"></a>[マイ アプリ] ブラウザー拡張機能のデプロイを計画する

パスワードベースの SSO アプリケーションをエンドユーザーが利用できるようにする場合、サインインするには [マイ アプリ] セキュア サインイン拡張機能をインストールする必要があります。 この拡張機能は、アプリケーションのサインイン フォームにパスワードを送信するスクリプトを実行します。 ユーザーは、パスワードベースの SSO アプリケーションを最初に起動するときに、この拡張機能のインストールを求められます。 拡張機能の詳細については、[アクセス パネルのブラウザー拡張機能のインストール](https://docs.microsoft.com/azure/active-directory/application-access-panel-extension-problem-installing?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav)に関するドキュメントを参照してください。

パスワードベースの SSO アプリケーションを統合する必要がある場合、[サポートされているブラウザー](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)を使用して拡張機能を大規模にデプロイするためのメカニズムを定義する必要があります。 次のオプションがあります。

* [Internet Explorer のグループ ポリシー](https://azure.microsoft.com/documentation/articles/active-directory-saas-ie-group-policy/)

* [Internet Explorer 用の System Center Configuration Manager (SCCM)](https://docs.microsoft.com/sccm/core/clients/deploy/deploy-clients-to-windows-computers)

* [Chrome、Firefox、Microsoft Edge、IE 用のユーザー主導のダウンロードおよび構成](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

詳細情報:[パスワード シングル サインオンの構成方法](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-password-sso-non-gallery)に関する記事。

この拡張機能は、パスワードベースの SSO アプリケーションを使用しないユーザーにもベネフィットがあります。 ベネフィットの例には、拡張機能の検索バーから任意のアプリを起動できること、最近使用したアプリケーションへのアクセスの検索、[マイ アプリ] ページへのリンクなどがあります。

パスワードベースの SSO アプリケーションを最初に起動したとき、ユーザーには次のように表示されます。

![[マイ アプリ] ブラウザー拡張機能のインストール画面のスクリーンショット ](media/access-panel-deployment-plan/ap-dp-sign-in.png)

#### <a name="plan-for-mobile-access"></a>モバイル アクセスの計画

パスワードベースの SSO アプリケーションをモバイル ユーザーが起動するには、Intune ポリシーで保護されたブラウザー (Microsoft Edge または Intune Managed Browser) が必要です。 ポリシーで保護されたブラウザーは、アプリケーション用に保存されたパスワードの転送を有効にします。 Microsoft Edge またはマネージド ブラウザーは、一連の Web データ保護機能を提供します。 iOS および Android デバイスで、エンタープライズ シナリオに Microsoft Edge を使用することもできます。 Microsoft Edge は、Intune Managed Browser と同じ管理シナリオをサポートし、エンドユーザーのエクスペリエンスを向上させます。 詳細情報:[ポリシーで保護された Microsoft Intune ブラウザーを使用して Web アクセスを管理する](https://docs.microsoft.com/intune/app-configuration-managed-browser)

## <a name="plan-your-my-apps-deployment"></a>[マイ アプリ] のデプロイを計画する

アクセス パネルの基礎は、ユーザーが [https://myapps.microsoft.com](https://myapps.microsoft.com/) でアクセスするアプリケーション ランチャー [マイ アプリ] です。 [マイアプリ] ページは、作業を開始して必要なアプリケーションにアクセスするための 1 つの場所をユーザーに提供します。 ここでは、ユーザーがシングル サインオンによってアクセスできるすべてのアプリケーションの一覧が表示されます。 

![アプリ パネルのスクリーンショット](media/access-panel-deployment-plan/ap-dp-app-launcher.png)

ユーザーが Office 365 ポータルを使用している場合、同じアプリケーションが Office 365 のアプリ ランチャーに表示されます。

アプリケーションを [マイ アプリ] ランチャーに追加する順序と、アプリケーションを徐々にロールアウトするか、それとも一度にロールアウトするかを計画します。 これを行うには、認証の種類と既存のシングル サインオン (SSO) 統合をアプリケーションごとに列挙するアプリケーション インベントリを作成します。

#### <a name="add-applications-to-the-my-apps-panel"></a>アプリケーションを [マイ アプリ] パネルに追加する

Azure AD SSO に対応した任意のアプリケーションを [マイ アプリ] ランチャーに追加できます。 その他のアプリケーションは "リンク" SSO オプションを使用して追加されます。 既存の Web アプリケーションの URL にリンクするアプリケーション タイルを構成できます。 "リンク" SSO を使用すると、すべてのアプリケーションを Azure Active Directory SSO に移行しなくても、ユーザーを [マイ アプリ] ポータルに誘導開始することができます。 ユーザーのエクスペリエンスを損なうことなく、Azure AD SSO が構成されたアプリケーションに段階的に移行できます。

#### <a name="plan-whether-to-use-my-apps-or-an-existing-portal"></a>[マイ アプリ] と既存のポータルのどちらを使用するかを計画する

[マイ アプリ] 以外のアプリケーションまたはポータルをユーザーが既に利用している場合があります。 その場合、両方のポータルをサポートするか、どちらか一方だけを使用するかを決定します。

既存のポータルがユーザーにとっての出発点として既に使用されている場合、"ユーザー アクセス URL" を使用して [マイ アプリ] 機能を統合することができます。 ユーザー アクセス URL は、[マイ アプリ] ポータルで利用可能なアプリケーションへの直接リンクとして機能します。 これらの URL を既存の Web サイト内に埋め込むことができます。 ユーザーがリンクをクリックすると、[マイ アプリ] ポータルからアプリケーションが起動します。

ユーザー アクセス URL のプロパティは、Azure portal ではアプリケーションの [プロパティ] 領域で確認できます。

![アプリ パネルのスクリーンショット](media/access-panel-deployment-plan/ap-dp-user-access-url.png)


## <a name="plan-self-service-application-discovery-and-access"></a>セルフサービスによるアプリケーションの検出とアクセスを計画する

アプリケーションのコア セットがユーザーの [マイ アプリ] ページにデプロイされたら、セルフサービスによるアプリ管理機能を有効にすることをお勧めします。 セルフサービスのアプリ検出によって、次のことが実現します。
* [マイ アプリ] に追加できる新しいアプリをユーザーが見つけます。 
* セットアップ時に必要性を判断できないオプションのアプリをユーザーが追加します。

承認ワークフローは、アプリケーションにアクセスするための明示的な承認に使用できます。 承認者であるユーザーは、アプリケーションへのアクセス要求が保留中になっていると、[マイ アプリ] ポータル内で通知を受け取ります。

## <a name="plan-self-service-group-membership"></a>セルフサービスによるグループ メンバーシップを計画する 

Azure Active Directory (Azure AD) では、独自のセキュリティ グループまたは Office 365 グループを作成して管理することができます。 グループの所有者は、メンバーシップ要求を承認または拒否できます。また、グループ メンバーシップの制御を委任できます。 セルフサービスによるグループ管理機能は、メールを有効にしたセキュリティ グループまたは配布リストでは使用できません。

セルフサービスによるグループ メンバーシップを計画するには、組織内のすべてのユーザーにグループの作成と管理を許可するか、それとも一部のユーザーのみに許可するかを決定します。 一部のユーザーに限定する場合、許可するユーザーを追加するグループを設定する必要があります。 これらのシナリオを有効にする方法の詳細については、「[Azure Active Directory でのセルフサービス グループ管理の設定](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management)」を参照してください。

## <a name="plan-reporting-and-auditing"></a>レポートと監査を計画する

Azure AD には、[技術やビジネスの分析情報を提供するレポート](https://azure.microsoft.com/documentation/articles/active-directory-view-access-usage-reports/)が用意されています。 ビジネスおよび技術アプリケーションの所有者と協力して、定期的にこれらのレポートの所有権を引き受けて、レポートを使用します。 次の表は、一般的なレポート シナリオの例を示しています。

|   | リスクの管理| 生産性の向上| ガバナンスとコンプライアンス |
|  - |- | - | - |
| レポートの種類|  アプリケーションのアクセス許可と使用状況。| アカウント プロビジョニングのアクティビティ| アプリケーションにアクセス中のユーザーの確認 |
| 潜在的なアクション| アクセスの監査。アクセス許可の取り消し| プロビジョニング エラーの修復| アクセス権の取り消し |

Azure AD は、ほとんどの監査データを 30 日間保持します。 データは、Azure 管理ポータルまたは API を使用して、分析システムにダウンロードできます。

#### <a name="auditing"></a>監査

アプリケーション アクセスの監査ログは 30 日間使用できます。 それよりも長いリテンション期間が企業内のセキュリティ監査に必要な場合、Splunk や ArcSight などのセキュリティ情報イベントおよび管理 (SIEM) ツールにログをエクスポートする必要があります。

監査、レポート、ディザスター リカバリーのバックアップについて、必要なダウンロード頻度、ターゲット システム、各バックアップの管理責任者を文書化します。 監査とレポートのバックアップは分けなくてもよい場合があります。 ディザスター リカバリーのバックアップは独立している必要があります。

## <a name="deploy-applications-to-users-my-apps-panel"></a>アプリケーションをユーザーの [マイ アプリ] パネルにデプロイする

アプリケーションが SSO 用に構成された後、グループにアクセス権が割り当てられます。 割り当てられたグループのユーザーは、[マイ アプリ] および Office 365 アプリ ランチャーでそのアプリケーションを表示してアクセスすることができます。

「[Azure Active Directory でアプリケーションにユーザーとグループを割り当てる](methods-for-assigning-users-and-groups.md)」を参照してください。

テストまたはデプロイ中にグループを追加するが、アプリケーションを [マイ アプリ] にまだ表示させない場合は、「[Azure Active Directory のエンドユーザーに対してアプリケーションを非表示にする](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app)」を参照してください。

### <a name="deploy-microsoft-office-365-applications-to-my-apps"></a>Microsoft Office 365 アプリケーションを [マイ アプリ] にデプロイする

Office 365 アプリケーションの場合、ユーザーは各自に割り当てられたライセンスに基づいて Office のコピーを受け取ります。 Office アプリケーションにアクセスするための前提条件は、Office アプリケーションに紐付けられた正しいライセンスにユーザーを割り当てることです。 ユーザーにライセンスを割り当てると、ライセンスに関連付けられたアプリケーションが、ユーザーの [マイ アプリ] ページと Office 365 アプリ ランチャーに自動的に表示されます。

ユーザーに対して Office アプリケーションのセットを非表示にしたい場合、[マイ アプリ] ポータルではアプリを非表示にしつつ、Office 365 ポータルからのアクセスは引き続き許可するオプションがあります。 これらの設定はアプリケーションの [ユーザー設定] 部分にあります。 詳細情報:[Azure Active Directory でユーザーのエクスペリエンスからアプリケーションを非表示にする](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app)。

![アプリケーションを非表示にする方法の構成のスクリーンショット](media/access-panel-deployment-plan/ap-dp-o365-portal.png)

### <a name="deploy-application-self-service-capabilities"></a>アプリケーションのセルフサービス機能をデプロイする

セルフサービスによるアプリケーション アクセスでは、ユーザーは任意のアプリケーションを自分で検出し、それらへのアクセスを要求することができます。 ユーザーは、アクセスを要求するたびに IT グループを介さなくても、必要なアプリに自由にアクセスできます。 ユーザーがアクセスを要求し、アプリの所有者によって自動または手動で承認されると、ユーザーがバックエンドでグループに追加されます。 アクセスを要求、承認、または削除したユーザーに関するレポートが有効になり、割り当てられたロールの管理を制御できます。

アプリケーションのアクセス要求の承認をビジネス承認者に委任することができます。 ビジネス承認者は、ビジネス承認者の [マイ アプリ] ページから直接、アプリのアクセス パスワードを設定できます。

詳細情報:[アプリケーションのセルフサービス アクセスの使用方法](https://docs.microsoft.com/azure/active-directory/application-access-panel-self-service-applications-how-to)

![セルフサービスによるアプリケーション管理の構成のスクリーンショット](media/access-panel-deployment-plan/ap-dp-salesforce.png)

## <a name="validate-your-deployment"></a>デプロイを検証する

アクセス パネルのデプロイが十分にテストされ、ロールバック計画が策定されていることを確認します。

企業所有のデバイスと個人のデバイスの両方で、以下のテストを実施する必要があります。 これらのテスト ケースには、ビジネスのユース ケースも反映させる必要があります。 以下、このドキュメントのビジネス要件例と一般的な技術シナリオに基づいたケースをいくつか示します。 読者のニーズに固有である他のケースを追加してください。

#### <a name="application-sso-access-test-case-examples"></a>アプリケーションの SSO アクセスのテスト ケース例:


| ビジネス ケース| 予想される結果 |
| - | -|
| ユーザーが [マイ アプリ] ポータルにサインインする| ユーザーはサインインして自分のアプリケーションを表示できる |
| ユーザーがフェデレーション SSO アプリケーションを起動する| ユーザーは自動的にアプリケーションにサインインする |
| ユーザーが初めてパスワード SSO アプリケーションを起動する| ユーザーは [マイ アプリ] 拡張機能をインストールする必要がある |
| ユーザーが後からパスワード SSO アプリケーションを起動する| ユーザーは自動的にアプリケーションにサインインする |
| ユーザーが Office 365 ポータルからアプリを起動する| ユーザーは自動的にアプリケーションにサインインする |
| ユーザーが Managed Browser からアプリを起動する| ユーザーは自動的にアプリケーションにサインインする |


#### <a name="application-self-service-capabilities-test-case-examples"></a>アプリケーションのセルフサービス機能のテスト ケースの例


| ビジネス ケース| 予想される結果 |
| - | - |
| ユーザーはアプリケーションのメンバーシップを管理できる| ユーザーはアプリにアクセスできるメンバーを追加/削除できる |
| ユーザーはアプリケーションを編集できる| ユーザーはパスワード SSO アプリケーションの説明と資格情報を編集できる |

### <a name="rollback-steps"></a>手順をロールバックする

デプロイが想定どおりに行われなかった場合の対処法を計画しておくことが重要です。 デプロイ時に SSO の構成に失敗した場合に、[SSO の問題をトラブルシューティング](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sso)してユーザーへの影響を軽減する方法を理解する必要があります。 極端な状況では、[SSO のロールバック](../manage-apps/plan-sso-deployment.md)が必要になる場合があります。


## <a name="manage-your-implementation"></a>実装を管理する

Microsoft では、Azure Active Directory 内で必要なタスクを実行するために最小限の特権ロールを使用することを推奨しています。 Microsoft では、[使用できるさまざまなロールを確認](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)して、このアプリケーションの各ペルソナのニーズを満たすのに適したものを選択することをお勧めします。 一部のロールは、一時的に適用され、デプロイが完了した後に削除する必要があります。

| ペルソナ| ロール| Azure AD ロール  |
| - | -| -|
| ヘルプ デスク管理者| Tier 1 サポート| なし |
| ID 管理者| Azure AD に影響を与える問題が発生した場合の構成とデバッグ| 全体管理者 |
| アプリケーション管理者| アプリケーションでのユーザーの構成証明、アクセス許可を持つユーザーの構成| なし |
| インフラストラクチャ管理者| 証明書のロールオーバー所有者| 全体管理者 |
| ビジネスの所有者/利害関係者| アプリケーションでのユーザーの構成証明、アクセス許可を持つユーザーの構成| なし |

[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) を使用してロールを管理し、ディレクトリのアクセス許可を持つユーザーに対する追加の監査、制御、およびアクセス レビューを提供することをお勧めします。

### <a name="troubleshoot-access-panel-issues"></a>アクセス パネルに関する問題のトラブルシューティング

一般的なシナリオを使用して、自社のサポート組織向けのトラブルシューティング ガイドを作成し、解決策が載っている Microsoft のドキュメントを参照先として指示します。 組織で採用されている複数の階層でサポートを分担するためのガイドを作成することが必要な場合があります。

リファレンスとして、以下のトラブルシューティング ガイドを参照してください。

[アプリケーションが表示されない](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-application-not-appearing)

[予期しないアプリケーションが表示される](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-unexpected-application)

[ユーザーがアクセス パネルにサインインできない](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-web-sign-in-problem)

[セルフサービスのアプリケーション アクセスの使用に関する問題](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-self-service-access)

[ブラウザー拡張機能に関する問題](https://docs.microsoft.com/azure/active-directory/manage-apps/manage-access-panel-browser-extension)

## <a name="next-steps"></a>次の手順

[Azure Active Directory の多要素認証のデプロイを計画する](https://aka.ms/deploymentplans/mfa)
