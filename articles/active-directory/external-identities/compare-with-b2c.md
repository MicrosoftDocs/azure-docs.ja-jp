---
title: 外部 ID の比較 - Azure Active Directory |Microsoft Docs
description: Azure AD External Identities を使用すると、組織外のユーザーが自身のアイデンティティを使用して組織のアプリとリソースにアクセスできます。 Azure Active Directory B2B コラボレーションや Azure AD B2C を含めて、外部 ID のためのソリューションを比較します。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: overview
ms.date: 08/05/2020
ms.custom: project-no-code
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 08623e6d69ac4db1790c1e9b46089f0c72c0526d
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87910582"
---
# <a name="what-are-external-identities-in-azure-active-directory"></a>Azure Active Directory の外部 ID とは

Azure AD 内で外部 ID を使用すると、ご自身の組織外のユーザーに組織のアプリとリソースへのアクセスを許可することができ、その際、外部ユーザーは希望する任意の ID を使用してサインインできます。 パートナー、販売代理店、サプライヤー、ベンダー、およびその他のゲスト ユーザーは、"自分のアイデンティティを持ち込む" ことができます。 Azure AD または他の IT 管理システムに含まれている場合でも、Google や Facebook などの管理されていないソーシャル アイデンティティを持っている場合でも、自身の資格情報を使用してサインインできます。 ID プロバイダーによって外部ユーザーのアイデンティティが管理されます。リソースを保護するためには、Azure AD を使用してアプリへのアクセスを管理します。 

## <a name="external-identities-scenarios"></a>外部 ID のシナリオ

Azure AD External Identities は、ユーザーと組織の関係は重視せず、ユーザーが組織のアプリとリソースにサインインしようとする方法を重視します。 Azure AD はこのフレームワークで、企業間 (B2B) コラボレーションから顧客や消費者のためのアプリ開発 (企業-消費者間すなわち B2C) まで、多様なシナリオをサポートします。

- **外部ユーザーとアプリを共有します (B2B コラボレーション)** 。 外部ユーザーをテナントに "ゲスト" ユーザーとして招待し、アクセス許可を割り当てます (認可) が、自身の既存の資格情報を使用してもらいます (認証)。 ユーザーは、職場アカウント、学校アカウント、または電子メール アカウントによる単純な招待と受諾プロセスを使用して、共有リソースにサインインします。 また、セルフサービス サインアップ ユーザー フロー (プレビュー) を使用できるようになったため、共有するアプリケーションを介して外部ユーザー用のサインイン エクスペリエンスを提供することもできます。 ユーザー フロー設定を構成し、アプリケーションにユーザーがサインアップする方法を制御できます。これにより、ユーザーは職場アカウント、学校アカウント、または使用したい任意のソーシャル アイデンティティ (Google や Facebook など) を使用できます。  詳細については、[Azure AD B2B のドキュメント](index.yml)を参照してください。

- **他の Azure AD テナント (シングルテナントまたはマルチテナント) 向けのアプリを開発します**。 Azure AD 用のアプリケーションを開発する場合、1 つの組織のユーザーを対象とすること (シングル テナント) も、または既に Azure AD テナントを持っている組織のユーザーを対象にすること (マルチテナント アプリケーションと呼ばれる) もできます。 このようなマルチテナント アプリケーションをいったん自身の Azure AD に登録すると、その後は追加の作業をしなくても、任意の組織の任意の Azure AD ユーザーが使用できます。

- **消費者および顧客向けのホワイトラベル アプリを開発します (Azure AD B2C)** 。 顧客向けアプリを作成している企業または開発者は、Azure AD B2C を使用して、消費者、顧客、または一般ユーザーへと範囲を拡張できます。 開発者は、Azure AD をフル機能のアイデンティティ システムとしてアプリケーションで使用できますが、顧客は既に確立しているアイデンティティ (Facebook や Gmail など) を使用してサインインできます。 Azure AD B2C を使用すると、顧客のサインアップ、サインイン、アプリケーション使用時のプロファイルの管理を、完全にカスタマイズして制御できます。 詳細については、[Azure AD B2C のドキュメント](https://docs.microsoft.com/azure/active-directory-b2c/)を参照してください。

## <a name="compare-external-identities-solutions"></a>External Identities ソリューションの比較

以下の表では、Azure AD External Identities で対応できるさまざまなシナリオを詳しく比較しています。

| マルチテナント アプリケーション  | 外部ユーザー コラボレーション (B2B) | 消費者または顧客向けアプリ (B2C)  |
| ---- | --- | --- |
| 主要なシナリオ:エンタープライズ向けのサービスとしてのソフトウェア (SaaS) | 主要なシナリオ:Microsoft アプリケーション (Microsoft 365、Teams、...) または独自のコラボレーション ソフトウェアを使用したコラボレーション。  | 主要なシナリオ:カスタム開発されたアプリケーションを使用するトランザクション アプリケーション。   |
| 対象: 多くの企業顧客にソフトウェアを提供する組織。    | 対象: ID プロバイダーに関係なく、取引先組織のユーザーを認証できるようにする必要のある組織。    | 対象: モバイル アプリや Web アプリの顧客 (個人、教育機関、企業を問わない) の、自身の組織のディレクトリとは切り離された Azure AD ディレクトリへの招待。 |
| サポートされている ID: Azure AD アカウントを持つ従業員。 | サポートされている ID: 会社または学校のアカウントを持つ従業員、会社または学校のアカウントを持つパートナー、または任意のメール アドレス。 近々直接フェデレーションをサポートします。      | サポートされている ID: ローカル アプリケーションのアカウント (任意のメール アドレスまたはユーザー名) を持つコンシューマー ユーザーまたは直接フェデレーションを使用するサポート対象の任意のソーシャル ID。       |
| 外部ユーザーは、アプリケーションが登録されているディレクトリから分離された独自のディレクトリで管理されます。    | 外部ユーザーは、従業員と同じディレクトリで管理されますが、特別な注釈が付与されます。 従業員と同様に管理したり、同じグループに追加したりすることができます。    | 外部ユーザーはアプリケーション ディレクトリで管理されます。 組織の従業員やパートナーのディレクトリ (存在する場合) とは別に管理されます。  |
| シングル サインオン:あらゆる Azure AD 接続アプリへの SSO がサポートされています。          | シングル サインオン:あらゆる Azure AD 接続アプリへの SSO がサポートされています。 たとえば、Microsoft 365 またはオンプレミスのアプリケーションや、Salesforce、Workday などの SaaS アプリへのアクセスを提供できます。    | シングル サインオン:Azure AD B2C テナント内のお客様所有のアプリへの SSO をサポートします。 Microsoft 365 やその他のマイクロソフト SaaS アプリへの SSO はサポートされていません。    |
| お客様のライフサイクル: ユーザーのホーム組織によって管理されます。      | パートナーのライフサイクル: ホスト/招待元の組織によって管理されます。    | お客様のライフサイクル: セルフサービスまたはアプリケーションによって管理されます。      |
| セキュリティ ポリシーとコンプライアンス: ホストまたは招待元の組織によって管理されます ([条件付きアクセス ポリシー](https://docs.microsoft.com/azure/active-directory/b2b/conditional-access)を使用するなど)。           | セキュリティ ポリシーとコンプライアンス: ホストまたは招待元の組織によって管理されます ([条件付きアクセス ポリシー](https://docs.microsoft.com/azure/active-directory/b2b/conditional-access)を使用するなど)。 | セキュリティ ポリシーとコンプライアンス: アプリケーションによって管理されます。        |
| ブランド化: ホスト/招待元の組織のブランドが使用されます。   | ブランド化: ホスト/招待元の組織のブランドが使用されます。    | ブランド化: アプリケーションによって管理されます。 一般的には製品によりブランド化される傾向があり、組織は目立たなくなります。   |
| 詳細情報: [マルチテナント アプリケーションでの ID 管理](https://docs.microsoft.com/azure/architecture/multitenant-identity/)、[ハウツー ガイド](https://docs.microsoft.com/azure/active-directory/develop/howto-convert-app-to-be-multi-tenant) | 詳細情報: [ブログ記事](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/01/azure-ad-b2b-new-updates-make-cross-business-collab-easy/)、[ドキュメント](what-is-b2b.md)                   | 詳細情報: [製品ページ](https://azure.microsoft.com/services/active-directory-b2c/)、[ドキュメント](https://docs.microsoft.com/azure/active-directory-b2c/)       |

Azure AD External Identities を使用して、組織の境界を越えて、顧客とパートナーを保護および管理します。

## <a name="next-steps"></a>次のステップ

- [Azure AD B2B コラボレーションとは](what-is-b2b.md)
- [Azure AD B2C について](https://docs.microsoft.com/azure/active-directory-b2c/overview)
