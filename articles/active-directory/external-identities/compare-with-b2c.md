---
title: 外部 ID の比較 - Azure Active Directory |Microsoft Docs
description: Azure AD External Identities を使用すると、組織外のユーザーが自身のアイデンティティを使用して組織のアプリとリソースにアクセスできます。 Azure Active Directory B2B コラボレーションや Azure AD B2C を含めて、外部 ID のためのソリューションを比較します。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: overview
ms.date: 03/02/2021
ms.custom: project-no-code
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 78fd91e2ba7badb1936b8603c8ed089130b29ffb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101644067"
---
# <a name="what-are-external-identities-in-azure-active-directory"></a>Azure Active Directory の外部 ID とは

Azure AD 内で外部 ID を使用すると、ご自身の組織外のユーザーに組織のアプリとリソースへのアクセスを許可することができ、その際、外部ユーザーは希望する任意の ID を使用してサインインできます。 パートナー、販売代理店、サプライヤー、ベンダー、およびその他のゲスト ユーザーは、"自分のアイデンティティを持ち込む" ことができます。 所有しているのが企業や政府が発行したデジタル ID か、Google や Facebook などの管理されていないソーシャル ID かに関係なく、自身の資格情報を使用してサインインできます。 外部ユーザーの ID プロバイダーによってその ID が管理されます。また、リソースを保護するためには、Azure AD を使用してアプリへのアクセスを管理します。

## <a name="external-identities-scenarios"></a>外部 ID のシナリオ

Azure AD External Identities は、ユーザーと組織の関係はあまり重視せず、ユーザーが組織のアプリやリソースにサインインする方法を重視します。 Azure AD はこのフレームワーク内で、企業間 (B2B) コラボレーションから、消費者や顧客または一般ユーザー向けアプリケーションのアクセス管理 (企業-顧客間すなわち B2C) まで、さまざまなシナリオをサポートします。

- **外部ユーザーとアプリやリソースを共有する (B2B コラボレーション)** 。 外部ユーザーを自分のテナントに "ゲスト" ユーザーとして招待します。このユーザーには、アクセス許可を割り当てる (承認) と同時に、自分の既存の資格情報を使用させる (認証) ことができます。 ユーザーは、職場、学校などのメール アカウントによる単純な招待と引き換えプロセスを使用して、共有リソースにサインインします。 また、[Azure AD のエンタイトルメント管理](../governance/entitlement-management-overview.md)を使用して、[外部ユーザーのアクセスを管理する](../governance/entitlement-management-external-users.md#how-access-works-for-external-users)ポリシーを構成することもできます。 さらに、[セルフサービス サインアップのユーザー フロー](self-service-sign-up-overview.md)を利用できるようになったので、外部ユーザーが自分でアプリケーションにサインアップできるようにすることができます。 このエクスペリエンスは、職場、学校、またはソーシャル ID (Google や Facebook など) によるサインアップを許可するようにカスタマイズできます。 サインアップ プロセスでユーザーに関する情報を収集することもできます。 詳細については、[Azure AD B2B のドキュメント](index.yml)を参照してください。

- **消費者および顧客向けアプリのホワイトラベルの ID 管理ソリューションでユーザー体験を構築する (Azure AD B2C)** 。 顧客向けアプリを作成している企業または開発者であれば、Azure AD B2C を使用することで、多数の消費者、顧客、または一般ユーザーへと範囲を拡張できます。 開発者は、フル機能を備えた顧客 ID およびアクセス管理 (CIAM) システムとして Azure AD を自身のアプリケーションに使用できます。 顧客は、既に作成している ID (Facebook や Gmail など) を使用してサインインできます。 Azure AD B2C を使用すると、顧客のサインアップ、サインイン、アプリケーション使用時のプロファイルの管理を、完全にカスタマイズして制御できます。 詳細については、[Azure AD B2C のドキュメント](../../active-directory-b2c/index.yml)を参照してください。

## <a name="compare-external-identities-solutions"></a>External Identities ソリューションの比較

次の表では、Azure AD External Identities で対応できるシナリオを詳しく比較しています。

|   | 外部ユーザー コラボレーション (B2B) | 消費者または顧客向けアプリへのアクセス (B2C)  |
| ---- | --- | --- |
| **主要なシナリオ** | Microsoft アプリケーション (Microsoft 365、Teams など) または独自のアプリケーション (SaaS アプリ、独自に開発したアプリなど) を使用したコラボレーション。  | 最新の SaaS または独自に開発したアプリケーション (ファーストパーティの Microsoft アプリ以外) の ID とアクセスの管理。   |
| **対象者**    | サプライヤー、パートナー、ベンダーなどの外部組織のビジネス パートナーとの共同作業。 ユーザーはディレクトリ内でゲスト ユーザーとして表示されます。 これらのユーザーは、IT を管理している場合もあれば、管理していない場合もあります。  | 製品の顧客。 これらのユーザーは、別の Azure AD ディレクトリで管理されています。  |
| **サポートされる ID プロバイダー** | 外部ユーザーは、職場アカウント、学校アカウント、任意のメール アドレス、SAML および WS-Fed ベースの ID プロバイダー、Gmail、Facebook を使用して共同作業を行うことができます。  | ローカル アプリケーションのアカウント (任意のメール アドレスまたはユーザー名) を持つコンシューマー ユーザー、サポートされているさまざまなソーシャル ID、直接フェデレーションを介して企業や政府が発行した ID を所有するユーザー。       |
| **外部ユーザーの管理**   | 外部ユーザーは、従業員と同じディレクトリで管理されますが、通常はゲスト ユーザーとして注釈が付けられます。 ゲスト ユーザーは、従業員と同じように管理したり、同じグループに追加したりできます。    | 外部ユーザーは Azure AD B2C ディレクトリで管理されます。 組織の従業員やパートナーのディレクトリ (存在する場合) とは別に管理されます。  |
| **シングル サインオン (SSO)**      | あらゆる Azure AD 接続アプリへの SSO がサポートされています。 たとえば、Microsoft 365 またはオンプレミスのアプリケーションや、Salesforce、Workday などの SaaS アプリへのアクセスを提供できます。    | Azure AD B2C テナント内のお客様所有のアプリへの SSO をサポートします。 Microsoft 365 やその他の Microsoft SaaS アプリへの SSO はサポートされていません。    |
| **セキュリティ ポリシーとコンプライアンス**        | ホストまたは招待元の組織によって管理されます ([条件付きアクセス ポリシー](conditional-access.md)を使用するなど)。 | 条件付きアクセスと ID 保護を使用して組織によって管理されます。        |
| **ブランド化**  | ホスト/招待元の組織のブランドが使用されます。    | アプリケーションまたは組織ごとの完全にカスタマイズ可能なブランド。   |
| **課金モデル** | 月間アクティブ ユーザー (MAU) に基づいた [External Identities の価格](https://azure.microsoft.com/en-us/pricing/details/active-directory/external-identities/)。 <br>(参照: [B2B の設定の詳細](external-identities-pricing.md)) | 月間アクティブ ユーザー (MAU) に基づいた [External Identities の価格](https://azure.microsoft.com/en-us/pricing/details/active-directory/external-identities/)。 <br>(参照: [B2C の設定の詳細](../../active-directory-b2c/billing.md)) |
| **詳細情報** | [ブログ記事](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/01/azure-ad-b2b-new-updates-make-cross-business-collab-easy/)、[ドキュメント](what-is-b2b.md)                   | [製品ページ](https://azure.microsoft.com/services/active-directory-b2c/)、[ドキュメント](../../active-directory-b2c/index.yml)       |

Azure AD External Identities を使用して、組織の境界を越えて、顧客とパートナーを保護および管理します。

## <a name="about-multitenant-applications"></a>マルチテナント アプリケーションについて

アプリをサービスとして提供していて、顧客のユーザー アカウントを管理したくない場合は、マルチテナント アプリが適切な選択肢になる可能性があります。 他の Azure AD テナントを対象としたアプリケーションを開発する場合、1 つの組織のユーザー (シングル テナント) も、既に Azure AD テナントを所有している組織のユーザー (マルチテナント アプリケーション) も対象にすることができます。 既定では、Azure AD でのアプリの登録はシングル テナントですが、登録をマルチテナントにすることができます。 このマルチテナント アプリケーションは、自身の Azure AD に一度だけ自分で登録します。 その後は追加で作業しなくても、任意の組織の Azure AD ユーザーであればだれでもそのアプリケーションを使用できます。 詳細については、「[マルチテナント アプリケーションでの ID 管理](/azure/architecture/multitenant-identity/)」、[ハウツー ガイド](../develop/howto-convert-app-to-be-multi-tenant.md)を参照してください。

## <a name="next-steps"></a>次のステップ

- [Azure AD B2B コラボレーションとは](what-is-b2b.md)
- [Azure AD B2C について](../../active-directory-b2c/overview.md)