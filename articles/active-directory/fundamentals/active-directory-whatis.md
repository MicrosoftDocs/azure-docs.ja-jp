---
title: Azure Active Directory とは - Azure Active Directory | Microsoft Docs
description: Azure Active Directory の概要と概念情報 (用語、利用可能なライセンス、関連機能の一覧と詳細情報へのリンクなど)。
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.topic: overview
ms.date: 07/31/2019
ms.author: ajburnle
ms.custom: it-pro, seodec18, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: a52b9ee26aac20d95356db6a280c5feb40023fed
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2019
ms.locfileid: "68942805"
---
# <a name="what-is-azure-active-directory"></a>Azure Active Directory とは

Azure Active Directory (Azure AD) は Microsoft が提供するクラウドベースの ID およびアクセス管理サービスであり、次のリソースへのサインインとアクセスを支援します。

- Microsoft Office 365、Azure portal、その他何千という SaaS アプリケーションなど、外部リソース。

- 企業ネットワークとイントラネット上のアプリや、自分の組織で開発したクラウド アプリなどの内部リソース。

[エンタープライズ アーキテクトのための Microsoft Cloud シリーズ](https://docs.microsoft.com/office365/enterprise/microsoft-cloud-it-architecture-resources#identity)の各種ポスターを使用すると、Azure、Azure AD、および Office 365 の中核を成す ID サービスについて、より深く理解することができます。

## <a name="who-uses-azure-ad"></a>Azure AD の利用者

Azure AD は、次の方を対象としています。

- **IT 管理者。** IT 管理者は Azure AD を使用して、ビジネス要件に基づいて自社のアプリやアプリ リソースへのアクセスを制御できます。 たとえば、Azure AD を使用して、重要な組織リソースへのアクセス時に多要素認証を必須にすることができます。 さらに、Azure AD を使用して、既存の Windows Server AD と クラウド アプリ (Office 365 など) の間のユーザー プロビジョニングを自動化できます。 最後に、Azure AD は、ユーザー ID と資格情報を自動的に保護し、アクセス ガバナンス要件を満たすうえで強力なツールとなります。 使用を開始するには、[Azure Active Directory Premium の 30 日間無料試用版](https://azure.microsoft.com/trial/get-started-active-directory/)にサインアップしてください。

- **アプリ開発者。** アプリ開発者は、アプリにシングル サインオン (SSO) を追加するための標準ベースのアプローチとして Azure AD を使用して、ユーザーの既存の資格情報を使用できるようにすることができます。 また Azure AD は、既存の組織データを使用したアプリ エクスペリエンスのパーソナライズに役立つ API も提供します。 使用を開始するには、[Azure Active Directory Premium の 30 日間無料試用版](https://azure.microsoft.com/trial/get-started-active-directory/)にサインアップしてください。 詳細については、[開発者向け Azure Active Directory](../develop/index.yml) に関するページも参照してください。

- **Microsoft 365、Office 365、Azure、または Dynamics CRM Online のサブスクライバー。** サブスクライバーの方であれば、Azure AD を既にお使いです。 Microsoft 365、Office 365、Azure、および Dynamics CRM Online の各テナントは、自動的に Azure AD テナントになります。 統合されたクラウド アプリへのアクセスの管理をすぐに始めることができます。

## <a name="what-are-the-azure-ad-licenses"></a>Azure AD のライセンスとは

Office 365 や Microsoft Azure などの Microsoft Online ビジネス サービスでは、サインインと ID 保護の支援のために Azure AD が必要になります。 Microsoft Online ビジネス サービスにサブスクライブすると、自動的に Azure AD が提供されて、すべての無料機能を利用できるようになります。

Azure AD の実装を強化するために、Azure Active Directory Premium P1 または Premium P2 ライセンスにアップグレードして、有料機能を追加することもできます。 Azure AD の有料ライセンスは、既存の無料ディレクトリをベースに構築され、セルフサービス、拡張された監視機能、セキュリティ レポート、モバイル ユーザーを対象とするセキュリティで保護されたアクセスなどの機能を提供します。

>[!Note]
>これらのライセンスの価格オプションについては、「[Azure Active Directory の価格](https://azure.microsoft.com/pricing/details/active-directory/)」を参照してください。
>
>Azure Active Directory Premium P1 および Premium P2 は、現在、中国ではサポートされていません。 Azure AD の価格の詳細については、[Azure Active Directory フォーラム](https://azure.microsoft.com/support/community/?product=active-directory)にお問い合わせください。

- **Azure Active Directory Free。** ユーザーとグループの管理、オンプレミス ディレクトリ同期、基本レポート、クラウド ユーザー向けのセルフサービスのパスワード変更のほか、Azure、Office 365、および多くの一般的な SaaS アプリ全体のシングル サインオンを提供します。

- **Azure Active Directory Premium P1。** P1 では、Free の機能に加えて、ハイブリッド ユーザーがオンプレミスとクラウドの両方のリソースにアクセスすることもできます。 さらに、動的グループ、セルフサービス グループ管理、Microsoft Identity Manager (オンプレミスの ID およびアクセス管理スイート)、オンプレミス ユーザーによるセルフサービスのパスワード リセットを可能にするクラウドの書き戻し機能など、高度な管理機能もサポートしています。

- **Azure Active Directory Premium P2。** P2 では、Free および P1 の機能に加えて、アプリや重要な企業データへのリスクベースの条件付きアクセスを提供するのに役立つ [Azure Active Directory Identity Protection](../identity-protection/enable.md) のほか、管理者と管理者によるリソースへのアクセスを検出、制限、監視するのに役立ち、必要に応じてジャストインタイム アクセスを提供できる [Privileged Identity Management](../privileged-identity-management/pim-getting-started.md) が提供されます。

- **"従量課金制" の機能ライセンス。** Azure Active Directory Business-to-Customer (B2C) などの機能ライセンスを別途取得することもできます。 B2C は、顧客向けアプリ用の ID およびアクセス管理ソリューションを提供するのに役立つ可能性があります。 詳細については、「[Azure Active Directory B2C のドキュメント](../../active-directory-b2c/index.yml)」を参照してください。

Azure サブスクリプションの Azure AD への関連付けの詳細については「[方法: Azure サブスクリプションを Azure Active Directory に関連付けるまたは追加する](active-directory-how-subscriptions-associated-directory.md)」を、ライセンスのユーザーへの割り当ての詳細については「[方法: Azure Active Directory ライセンスを割り当てる、または削除する](license-users-groups.md)」を参照してください。

## <a name="terminology"></a>用語集

Azure AD とそのドキュメントをより深く理解するために、次の用語を確認しておくことをお勧めします。

|用語または概念|説明|
|---------------|-----------|
|ID| 認証を受けることができるもの。 ID は、ユーザー名とパスワードを持つユーザーの可能性があります。 ID には、秘密キーまたは証明書による認証を必要とする可能性があるアプリケーションまたはその他のサーバーも含まれます。|
|Account| データが関連付けられている ID。 ID なしではアカウントを持つことができません。|
|Azure AD アカウント| Azure AD またはそれ以外の Microsoft クラウド サービス (Office 365 など) を通じて作成される ID です。 ID は Azure AD に保存され、組織のクラウド サービスのサブスクリプションで利用できます。 このアカウントは、職場または学校アカウントと呼ばれることもあります。|
|Azure サブスクリプション| Azure クラウド サービスの支払いに使用されます。 多数のサブスクリプションをご利用いただけます。サブスクリプションはクレジット カードにリンクされます。|
|Azure テナント| Azure AD の信頼された専用インスタンスであり、組織が Microsoft Azure、Microsoft Intune、Office 365 などの Microsoft クラウド サービスのサブスクリプションにサインアップしたときに自動的に作成されます。 1 つの Azure テナントは単一の組織を表します。|
|シングル テナント| 専用の環境で他のサービスにアクセスする Azure テナントは、単一のテナントと見なされます。|
|マルチテナント| 複数の組織の共用環境で他のサービスにアクセスする Azure テナントは、マルチテナントと見なされます。|
|Azure AD ディレクトリ|Azure の各テナントには、信頼された専用の Azure AD ディレクトリが用意されます。 Azure AD ディレクトリは、テナントのユーザー、グループ、およびアプリを含み、テナント リソースに対して ID およびアクセス管理機能を実行するために使用されます。|
|カスタム ドメイン|新しい Azure AD ディレクトリには、必ず domainname.onmicrosoft.com という初期ドメイン名が付けられます。 その初期の名前に加えて、組織のドメイン名をリストに追加することもできます。このドメイン名には、ビジネスを遂行するために使用するユーザー名と、ユーザーが組織のリソースにアクセスするために使用する名前が含まれます。 カスタム ドメイン名を追加すると、alain@contoso.com など、ユーザーになじみのあるユーザー名を作成するのに役立ちます。|
|アカウント管理者|この従来のサブスクリプション管理者ロールは、概念的にはサブスクリプションの課金の所有者です。 このロールは、[Azure アカウント センター](https://account.azure.com/Subscriptions)にアクセスでき、アカウント内の全サブスクリプションの管理を可能にします。 詳細については、「[従来のサブスクリプション管理者ロール、Azure RBAC ロール、および Azure AD 管理者ロール](../../role-based-access-control/rbac-and-directory-admin-roles.md)」を参照してください。|
|サービス管理者|この従来のサブスクリプション管理者ロールでは、アクセスを含め、すべての Azure リソースを管理することができます。 このロールは、サブスクリプション スコープで所有者ロールを割り当てられているユーザーと同等のアクセス権を持ちます。 詳細については、「[従来のサブスクリプション管理者ロール、Azure RBAC ロール、および Azure AD 管理者ロール](../../role-based-access-control/rbac-and-directory-admin-roles.md)」を参照してください。|
|Owner|このロールは、アクセスを含め、すべての Azure リソースを管理するのに役立ちます。 このロールは、Azure リソースへのきめ細かなアクセス管理を提供するロールベース アクセス制御 (RBAC) と呼ばれる新しい認可システムをベースに構築されています。 詳細については、「[従来のサブスクリプション管理者ロール、Azure RBAC ロール、および Azure AD 管理者ロール](../../role-based-access-control/rbac-and-directory-admin-roles.md)」を参照してください。|
|Azure AD 全体管理者|この管理者ロールは、Azure AD テナントを作成したユーザーに自動的に割り当てられます。 全体管理者は、Azure AD と Azure AD にフェデレーションされたすべてのサービス (Exchange Online、SharePoint Online、Skype for Business Online など) に対して、すべての管理機能を実行できます。 全体管理者は複数人配置することができますが、管理者ロールをユーザーに割り当てることができるのは全体管理者に限られます (これには他の全体管理者を割り当てることも含まれます)。<br><br>**注**<br>この管理者ロールは、Azure portal では全体管理者と呼ばれますが、Microsoft Graph API、Azure AD Graph API、Azure AD PowerShell では**会社の管理者**と呼ばれます。<br><br>さまざまな管理者ロールの詳細については、「[Azure Active Directory での管理者ロールのアクセス許可](../users-groups-roles/directory-assign-admin-roles.md)」を参照してください。|
|Microsoft アカウント (別称: MSA)|Outlook、OneDrive、Xbox LIVE、Office 365 など、コンシューマー向けの Microsoft 製品とクラウド サービスへのアクセスを提供する個人アカウントです。 お使いの Microsoft アカウントは、Microsoft が運営する Microsoft コンシューマー ID アカウント システムを使用して作成、保存されます。|

## <a name="which-features-work-in-azure-ad"></a>Azure AD で利用できる機能

Azure AD ライセンスを選択すると、組織向けの次の機能の一部またはすべてにアクセスできるようになります。

|Category|説明|
|-------|-----------|
|アプリケーション管理|アプリケーション プロキシ、シングル サインオン、マイ アプリ ポータル (別称: アクセス パネル)、サービスとしてのソフトウェア (SaaS) アプリを使用して、クラウドおよびオンプレミスのアプリを管理します。 詳細については、「[オンプレミス アプリケーションへの安全なリモート アクセスを実現する方法](../manage-apps/application-proxy.md)」および「[アプリケーション管理のドキュメント](../manage-apps/index.yml)」を参照してください。|
|Authentication|Azure Active Directory のセルフサービスのパスワード リセット、Multi-Factor Authentication、カスタムの禁止パスワード リスト、スマート ロックアウトを管理します。 詳細については、「[Azure AD Authentication のドキュメント](../authentication/index.yml)」を参照してください。|
|企業間 (B2B)|自社データの管理を続けながら、ゲスト ユーザーと外部パートナーを管理します。 詳細については、「[Azure Active Directory B2B のドキュメント](../b2b/index.yml)」を参照してください。|
|企業-消費者間 (B2C)|アプリの使用時にユーザーがサインアップおよびサインインする方法や自分のプロファイルを管理する方法をカスタマイズして制御します。 詳細については、「[Azure Active Directory B2C のドキュメント](../../active-directory-b2c/index.yml)」を参照してください。|
|条件付きアクセス|クラウド アプリへのアクセスを管理します。 詳細については、「[Azure AD の条件付きアクセスのドキュメント](../conditional-access/index.yml)」を参照してください。|
|開発者のための Azure Active Directory|すべての Microsoft ID にサインインし、Microsoft Graph、その他の Microsoft API、またはカスタム API を呼び出すトークンを取得するアプリを構築します。 詳細については、「[Microsoft ID プラットフォーム (開発者向け Azure Active Directory)](../develop/index.yml)」を参照してください。|
|デバイスの管理|クラウドまたはオンプレミスのデバイスが会社のデータにアクセスする方法を管理します。 詳細については、「[Azure AD デバイス管理のドキュメント](../devices/index.yml)」を参照してください。|
|ドメイン サービス|ドメイン コントローラーを使用せずにドメインに Azure 仮想マシンを参加させます。 詳細については、「[Azure AD Domain Services のドキュメント](../../active-directory-domain-services/index.yml)」を参照してください。|
|エンタープライズ ユーザー|グループと管理者のロールを使用して、ライセンスの割り当てとアプリへのアクセスを管理し、委任を設定します。 詳細については、「[Azure Active Directory のユーザー管理のドキュメント](../users-groups-roles/index.yml)」を参照してください。|
|ハイブリッド ID|Azure Active Directory Connect と Connect Health を使用して、場所 (クラウドまたはオンプレミス) に関係なく、すべてのリソースに対する認証と認可のための単一のユーザー ID を提供します。 詳細については、「[ハイブリッド ID のドキュメント](../hybrid/index.yml)」を参照してください。|
|Identity Governance|従業員、ビジネス パートナー、ベンダー、サービス、およびアプリのアクセス制御を通じて、組織の ID を管理します。 アクセス レビューを実行することもできます。 詳細については、「[Azure AD Identity Governance とは](../governance/identity-governance-overview.md)」および [Azure AD アクセス レビュー](../governance/access-reviews-overview.md)に関するページを参照してください。|
|Identity Protection|組織の ID に影響を及ぼす潜在的な脆弱性を検出するほか、疑わしいアクションに対応するようにポリシーを構成し、適切なアクションを行って解決します。 詳細については、[Azure AD Identity Protection](../identity-protection/index.yml) に関するページを参照してください。|
|Azure リソースのマネージド ID|Key Vault を含む、任意の Azure AD でサポートされている認証サービスに対して認証できる、Azure AD の自動管理されたマネージド ID を Azure サービスに提供します。 詳細については、「[Azure リソースのマネージド ID とは](../managed-identities-azure-resources/overview.md)」を参照してください。|
|Privileged Identity Management (PIM)|組織内でのアクセスを管理、制御、および監視します。 この機能には、Azure AD と Azure のリソースへのアクセスと、その他 Microsoft Online Services (Office 365、Intune など) へのアクセスが含まれます。 詳しくは、[Azure AD Privileged Identity Management](../privileged-identity-management/index.yml) に関するページを参照してください。|
|レポートと監視|環境におけるセキュリティや使用パターンに関する分析情報を得ることができます。 詳細については、「[Azure Active Directory のレポートと監視](../reports-monitoring/index.yml)」を参照してください。|

## <a name="next-steps"></a>次の手順

- [Azure Active Directory Premium にサインアップする](active-directory-get-started-premium.md)

- [Azure サブスクリプションを Azure Active Directory に関連付ける](active-directory-how-subscriptions-associated-directory.md)

- [Azure Active Directory にアクセスして新しいテナントを作成する](active-directory-access-create-new-tenant.md)

- [Azure Active Directory Premium P2 機能のデプロイ チェックリスト](active-directory-deployment-checklist-p2.md)
