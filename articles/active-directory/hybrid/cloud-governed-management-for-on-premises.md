---
title: Azure AD のオンプレミスのワークロードに対するクラウド ガバナンス管理 - Azure
description: このトピックでは、オンプレミスのワークロードに対するクラウド ガバナンス管理について説明します。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 510a5562740260eb2946ded074a5c37804c55375
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67109517"
---
# <a name="how-azure-ad-delivers-cloud-governed-management-for-on-premises-workloads"></a>Azure AD がオンプレミスのワークロードに対してクラウド ガバナンス管理を提供する方法

Azure Active Directory (Azure AD) は、何百万もの組織に使用されている包括的なサービスとしての ID (IDaaS) ソリューションです。その範囲は ID、アクセス管理、セキュリティのあらゆる面に及びます。 Azure AD は 10 億を超えるユーザー ID を保持しており、ユーザーはサインインすると、以下の両方に安全にアクセスすることができます。

* 外部リソース。Microsoft Office 365、Azure portal、その他何千という サービスとしてのソフトウェア (SaaS) アプリケーションなどです。
* 内部リソース。組織の企業ネットワークとイントラネット上のアプリケーションや、自分の組織で開発したクラウド アプリケーションなどです。

組織が "純粋なクラウド" である場合は Azure AD を使用できます。また、オンプレミス ワークロードがある場合は "ハイブリッド" デプロイとして使用できます。 Azure AD のハイブリッド デプロイは、組織が IT 資産をクラウドに移行するため、または既存のオンプレミス インフラストラクチャを新しいクラウド サービスに統合し続けるための戦略の一部になることがあります。

従来、"ハイブリッド" な組織は、Azure AD を既存のオンプレミス インフラストラクチャの拡張機能と見なしてきました。 これらのデプロイでは、オンプレミス ID ガバナンス管理、Windows Server Active Directory、または他の社内ディレクトリ システムが制御ポイントとなり、ユーザーとグループはそれらのシステムから Azure AD などのクラウド ディレクトリに同期されます。 これらの ID は、クラウドに移行されると、Office 365、Azure、およびその他のアプリケーションで利用できるようになります。

![ID ライフサイクル](media/cloud-governed-management-for-on-premises//image1.png)

組織がアプリケーションと共により多くの IT インフラストラクチャをクラウドに移行すると、その多くがサービスとしての ID 管理のセキュリティの向上と管理機能の簡素化を求めるようになります。 Azure AD のクラウドで提供される IDaaS 機能によって、組織が従来のオンプレミス システムから Azure AD へとより多くの ID 管理を迅速に採用して移行できるだけでなく、さらに既存および新規のアプリケーションを引き続きサポートすることができるので、クラウド ガバナンス管理への移行は加速されます。

このドキュメントでは、ハイブリッド IDaaS に関する Microsoft の戦略の概要と、組織が既存のアプリケーションに Azure AD を使用する方法について説明します。

## <a name="the-azure-ad-approach-to-cloud-governed-identity-management"></a>Azure AD からクラウド ガバナンス ID 管理へのアプローチ

組織がクラウドに移行するときには、自動化によってサポートされるアクティビティのセキュリティの強化と可視化の向上、先を見越した分析情報など、全体の環境を管理することを保証する必要があります。 「**クラウド ガバナンス管理**」では、組織がクラウドからユーザー、アプリケーション、グループ、およびデバイスをどのように管理および統制するかについて説明します。

この現代の世界では、SaaS アプリケーションが急増し、コラボレーションと外部の ID の役割が増大しているため、組織は効率的に大規模な環境を管理できる必要があります。 クラウドの新しいリスク環境は、組織が応答性を高める必要があることを意味します。クラウド ユーザーを侵害する悪意のあるアクターが、クラウドおよびオンプレミスのアプリケーションに影響を与える可能性があります。

特に、ハイブリッド組織では、従来は IT が手動で行っていたタスクを委任し、自動化できる必要があります。 タスクを自動化するには、さまざまな ID 関連リソース (ユーザー、グループ、アプリケーション、デバイス) のライフサイクルを調整する API とプロセスが必要です。そうすれば、それらのリソースの日常的な管理をコア IT スタッフ以外のより多くの個人に委任できるようになります。 Azure AD は、オンプレミスの ID インフラストラクチャを必要とせず、ユーザー アカウント管理とユーザーのネイティブ認証を使用してこれらの要件に対処します。 オンプレミスのインフラストラクチャを構築しないことは、オンプレミスのディレクトリに由来しないユーザーの新しいコミュニティ (ビジネス パートナーなど) があり、ビジネス成果の達成にアクセスの管理が不可欠な組織にメリットがあります。

さらに、管理はガバナンスなしには完全にはなりません。また、この新しい世界におけるガバナンスは、アドオンではなく ID システムの統合された部分です。 ID ガバナンスによって、組織は、ID を管理し、従業員、ビジネス パートナーとベンダー、そしてサービスとアプリケーション全体にわたるライフサイクルにアクセスできるようになります。

ID ガバナンスを取り入れることで、組織はクラウド ガバナンス管理への移行が容易になり、IT 担当者はスケール、ゲストとの新たな課題への対処、およびオンプレミス インフラストラクチャの場合よりも深い分析情報と自動化を実現できます。 この新しい世界のガバナンスとは、組織内のリソースへのアクセスに対して、透明性、可視性、および適切な統制力を持つ組織の能力を意味します。 Azure AD を使用すると、セキュリティ運用および監査チームは、組織内の (どのデバイス上の) どのリソースに対してどのユーザーがアクセス権を持っているか (また持つべきか)、それらのユーザーがそのアクセス権を使用して何を実行するか、組織が会社または規制によるポリシーに従ってアクセス権を削除または制限する適切な統制力を持ち、使用しているかどうかを把握できます。

新しい管理モデルは、SaaS と基幹業務 (LOB) アプリケーションの両方を使用している組織にとってメリットがあります。これは、こうしたアプリケーションへのアクセスの管理とセキュリティ保護がより簡単になるためです。 アプリケーションを Azure AD と統合することで、組織はクラウドとオンプレミスが元の ID 両方のアクセスを一貫して使用および管理できるようになります。 アプリケーションのライフサイクル管理はより自動化され、オンプレミスの ID 管理では簡単に達成できなかったアプリケーションの使用状況に関する豊富な分析情報が Azure AD から提供されます。 組織は、Azure AD、Office 365 グループ、および Teams のセルフサービス機能を使用して、アクセス管理とコラボレーションのためのグループを簡単に作成し、クラウド内のユーザーを追加または削除してコラボレーションとアクセス管理の要件を満たすことができます。

クラウド ガバナンス管理のための適切な Azure AD 機能の選択は、使用するアプリケーションと、それらのアプリケーションを Azure AD に統合する方法によって変わります。 以下のセクションでは、AD 統合アプリケーションと、フェデレーション プロトコルを使用するアプリケーション (SAML、OAuth、OpenID Connect など) に採用するアプローチについて概要を説明します。

## <a name="cloud-governed-management-for-ad-integrated-applications"></a>AD 統合アプリケーションのクラウド ガバナンス管理

Azure AD では、これらのアプリケーションへのセキュリティで保護されたリモート アクセスと条件付きアクセスを通して、組織のオンプレミスの Active Directory 統合アプリケーションに対する管理機能を向上させます。 さらに、Azure AD には、ユーザーの既存の AD アカウントに対して、次のようなアカウント ライフサイクル管理と資格情報管理の機能があります。

* **オンプレミスのアプリケーションへのセキュリティで保護されたリモート アクセスと条件付きアクセス**

多くの組織にとって、オンプレミスの AD 統合 Web およびリモート デスクトップベースのアプリケーションでクラウドからのアクセスを管理する最初の手順は、それらのアプリケーションの前に[アプリケーション プロキシ](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy)を配置し、セキュリティで保護されたリモート アクセスを提供することです。

Azure AD にシングル サインオンした後、ユーザーは、外部の URL または内部のアプリケーション ポータルから、クラウド アプリケーションとオンプレミス アプリケーションの両方にアクセスできます。 たとえば、アプリケーション プロキシは、リモート デスクトップ、SharePoint だけでなく、Tableau、Qlik、基幹業務 (LOB) アプリケーションなどのアプリへのリモート アクセスとシングル サインオンを提供します。 さらに、条件付きアクセス ポリシーには、[使用条件](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou)の表示と、アプリケーションにアクセスできるようになる前に[ユーザーがこれらの条件に同意していることの確認](https://docs.microsoft.com/azure/active-directory/conditional-access/require-tou)を含めることができます。

![アプリ プロキシのアーキテクチャ](media/cloud-governed-management-for-on-premises/image2.png)

* **Active Directory アカウントの自動ライフサイクル管理**

ID ガバナンスにより、組織は、"*生産性*" (従業員が組織に加わったときなどに、必要なリソースへのアクセスできるようになるまでの時間) と "*セキュリティ*" (従業員の雇用形態の変更などによって、時間の経過に伴いアクセス権をどのように変更すべきか) とのバランスを取ることができます。 ID ライフサイクル管理は ID 管理の基盤であり、大規模な効果的な管理には、アプリケーションの ID ライフサイクル管理インフラストラクチャの近代化が必要です。

多くの組織では、従業員の ID ライフサイクルは、ヒューマン キャピタル マネジメント (HCM) システムでのそのユーザーの表示に関連付けられています。 HCM システムとして Workday を使用している組織の場合、Azure AD によって、AD のユーザー アカウントが確実に [Workday のワーカーに対して自動的にプロビジョニングおよびプロビジョニング解除される](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial)ようにすることができます。 その結果、ユーザーが役割を変更したり組織を離れたりしたときにアプリケーションのアクセスが確実に自動的に更新されるようになり、生得権アカウントの自動化によってユーザーの生産性が向上し、リスクが管理されるようになります。 Workday 主導のユーザー プロビジョニング [デプロイ計画](https://aka.ms/WorkdayDeploymentPlan)は、Workday から Active Directory へのユーザー プロビジョニング ソリューションのベスト プラクティス実装を 5 段階のプロセスで説明する手順ガイドです。

Azure AD Premium には Microsoft Identity Manager も含まれていて、SAP、Oracle eBusiness、Oracle PeopleSoft などの他のオンプレミスの HCM システムからレコードをインポートすることができます。

企業間のコラボレーションには、組織外のユーザーにアクセスを許可することがますます必要になっています。 組織が [Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/) コラボレーションを使用すると、独自の社内データに対するコントロールを維持した状態で、アプリケーションとサービスをゲスト ユーザーや外部パートナーと安全に共有することができます。

Azure AD では、必要に応じて[ゲスト ユーザー用のアカウントを自動的に AD に作成](https://docs.microsoft.com/azure/active-directory/b2b/hybrid-cloud-to-on-premises)し、ビジネス ゲストが別のパスワードを必要とせずにオンプレミスの AD 統合アプリケーションにアクセスできるようにすることができます。 組織は[ゲスト ユーザーの多要素認証 (MFA) ポリシー](https://docs.microsoft.com/azure/active-directory/b2b/conditional-access)を設定できるので、MFA チェックはアプリケーション プロキシ認証中に行われます。 また、クラウド B2B ユーザーに対して行われた[アクセスのレビュー](https://docs.microsoft.com/azure/active-directory/governance/manage-guest-access-with-access-reviews)は、オンプレミス ユーザーにも適用されます。 たとえば、ライフサイクル管理ポリシーを使用してクラウド ユーザーが削除された場合、オンプレミス ユーザーも削除されます。

**Active Directory アカウントの資格情報管理** Azure AD のセルフサービス パスワード リセットにより、パスワードを忘れたユーザーは再認証され、変更されたパスワードが[オンプレミスの Active Directory に書き込まれます](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback)。 パスワードのリセット プロセスは、オンプレミス Active Directory のパスワード ポリシーも使用できます。ユーザーが自分のパスワードをリセットするとき、パスワードがオンプレミスの Active Directory ポリシーに準拠していることを確認してから、そのディレクトリにコミットします。 セルフサービス パスワード リセットの[デプロイ計画](https://aka.ms/deploymentplans/sspr)では、Web および Windows 統合エクスペリエンスを介してセルフサービス パスワード リセットをユーザーにロールアウトするためのベスト プラクティスの概要を示します。

![Azure AD SSPR のアーキテクチャ](media/cloud-governed-management-for-on-premises/image3.png)

最後に、ユーザーが AD で自分のパスワードを変更することを許可する組織の場合、[Azure AD パスワード保護機能](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises) (現在プレビュー段階) を使用して、組織が Azure AD で使用しているのと同じパスワード ポリシーを使用するように構成できます。

組織が、アプリケーションをホストしているオペレーティング システムを Azure に移動して AD 統合アプリケーションをクラウドに移動する準備ができたら、[Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview) によって AD 互換ドメイン サービス (ドメイン参加、グループ ポリシー、LDAP、Kerberos/NTLM 認証など) が提供されます。 Azure AD Domain Services は組織の既存の Azure AD テナントと統合されるので、ユーザーは企業の資格情報を使用してサインインできるようになります。 さらに、既存のグループおよびユーザー アカウントを使用してリソースへのアクセスをセキュリティで保護することができます。このため、'リフト アンド シフト' 方式でオンプレミスのリソースを Azure インフラストラクチャ サービスに滑らかに移行することができます。

![Azure AD Domain Services](media/cloud-governed-management-for-on-premises/image4.png)

## <a name="cloud-governed-management-for-on-premises-federation-based-applications"></a>オンプレミスのフェデレーションベースのアプリケーションのクラウド ガバナンス管理

オンプレミスの ID プロバイダーを既に使用している組織の場合、アプリケーションを Azure AD に移動することで、フェデレーション管理のアクセスがより安全になり、管理エクスペリエンスが簡単になります。 Azure AD では、Azure AD の条件付きアクセスを使用して、アプリケーションごとのきめ細かなアクセス制御 (Azure Multi-Factor Authentication を含む) を構成できます。 Azure AD は、アプリケーション固有のトークン署名証明書や構成可能な証明書の有効期限など、その他の機能をサポートしています。 組織は、これらの機能、ツール、およびガイダンスを使用して、オンプレミスの ID プロバイダーを廃止することができます。 一例を挙げると、Microsoft の IT 部門は、17,987 個のアプリケーションを Microsoft 社内の Active Directory フェデレーション サービス (AD FS) から Azure AD に移行しました。

![Azure AD の進化](media/cloud-governed-management-for-on-premises/image5.png)

フェデレーション アプリケーションを ID プロバイダーとして Azure AD に移行する作業を始めるには、以下のリンクが含まれる https://aka.ms/migrateapps を参照してください。

* ホワイト ペーパー「[Migrating Your Applications to Azure Active Directory (Azure Active Directory へのアプリケーションの移行)](https://aka.ms/migrateapps/whitepaper)」は、移行のメリットを示し、4 つのおおまかなフェーズ (検出、分類、移行、および継続的な管理) を経て移行するための計画を立案する方法について説明します。 プロセスの考え方とプロジェクトを実行しやすいピースに分割する方法について案内します。 ドキュメント全体に、作業中に役立つ重要なリソースへのリンクがあります。

* ソリューション ガイド「[Migrating Application Authentication from Active Directory Federation Services to Azure Active Directory (Active Directory フェデレーション サービスから Azure Active Directory へのアプリケーション認証の移行)](https://aka.ms/migrateapps/adfssolutionguide)」では、アプリケーション移行プロジェクトの計画と実行の同じ 4 つのフェーズについて詳しく説明しています。 このガイドでは、アプリケーションを Active Directory フェデレーション サービス (AD FS) から Azure AD に移行するための具体的な目標に対してこれらのフェーズを適用する方法について説明します。

* [Active Directory フェデレーション サービスの移行準備スクリプト](https://aka.ms/migrateapps/adfstools)を既存のオンプレミスの Active Directory フェデレーション サービス (AD FS) サーバー上で実行し、Azure AD への移行のためのアプリケーションの準備状況を判断できます。

## <a name="ongoing-access-management-across-cloud-and-on-premises-applications"></a>クラウドとオンプレミスのアプリケーション全体の継続的なアクセス管理

組織には、スケーラブルなアクセス管理プロセスが必要です。 ユーザーがアクセス権を蓄積し続けると、最終的には始めにプロビジョニングされたものを超えます。 さらに、企業組織では、アクセス ポリシーと管理を継続的に開発し適用するように、効率的なスケールが可能でなければなりません。

通常、IT 部門は、アクセス承認の決定を管理職意思決定者に委任します。 さらに、ユーザー自身が IT 部門に関与していることがあります。 たとえば、ヨーロッパで会社のマーケティング アプリケーションで顧客の機密データにアクセスするユーザーは、会社のポリシーを把握している必要があります。 また、ゲスト ユーザーは、招待されている組織のデータの処理要件を理解していない可能性もあります。

組織では、[動的グループ](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)などのテクノロジを [SaaS アプリケーション](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)や、[System for Cross-Domain Identity Management (SCIM) 標準を使用して統合されたアプリケーション](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups)へのユーザー プロビジョニングと組み合わせて使用して、アクセスのライフサイクル プロセスを自動化することができます。 また、組織では、[オンプレミス アプリケーションへのアクセス権を持つゲスト ユーザー](https://docs.microsoft.com/azure/active-directory/b2b/hybrid-cloud-to-on-premises)を制御することもできます。 それにより、[Azure AD アクセス レビュー](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)を定期的に利用して、これらのアクセス権を定期的にレビューすることができます。

## <a name="future-directions"></a>今後の方向性

ハイブリッド環境における Microsoft の戦略は、クラウドが ID の制御プレーンであり **、** Active Directory や他のオンプレミス アプリケーションなどのオンプレミス ディレクトリや他の ID システムが、ユーザーにアクセス権をプロビジョニングするためのターゲットであるデプロイに対応することです。 この戦略は、こうしたアプリケーションとそれらに依存するワークロードの権限、ID、およびアクセスを確保するために今後も継続されます。 この最終段階では、組織はクラウドからエンドユーザーの生産性を全体的に向上できるようになります。

![Azure AD のアーキテクチャ](media/cloud-governed-management-for-on-premises/image6.png)

## <a name="next-steps"></a>次の手順

この体験の始め方の詳細については、<https://aka.ms/deploymentplans> にある Azure AD のデプロイ計画を参照してください。 Azure Active Directory (Azure AD) の機能をデプロイする方法についてのエンド ツー エンドのガイダンスが紹介されています。 各計画では、Azure AD の一般的な機能について、そのビジネス上の価値や、正しく展開するうえで必要な計画の考慮事項、設計、運用手順が説明されています。 Microsoft は、Azure AD を使用したクラウドからの管理に新機能を追加する際は、お客様のデプロイやその他のフィードバックから学んだベスト プラクティスを取り入れてデプロイ計画を継続的に更新します。
