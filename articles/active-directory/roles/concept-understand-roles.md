---
title: Azure Active Directory のロールの概念について
description: Azure Active Directory におけるリソースのスコープと併せて Azure Active Directory の組み込みおよびカスタム ロールを理解する方法について説明します。
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: overview
ms.date: 11/20/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71291458eec0aec13542d3e0dfaf04a96f391a23
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103466118"
---
# <a name="understand-roles-in-azure-active-directory"></a>Azure Active Directory のロールについて

Azure Active Directory (Azure AD) には 60 種の組み込みロールがあります。これらは、ロールのアクセス許可がセットとして固定されたロールです。 組み込みロールを補完するため、Azure AD ではカスタム ロールもサポートされています。 カスタム ロールを使用して、必要なアクセス許可をロールに選択できます。 たとえば、アプリケーションやサービス プリンシパルなど、特定の Azure AD リソースを管理するために作成することが可能です。

この記事では、Azure AD ロールの概要と、その使用方法について説明します。

## <a name="how-azure-ad-roles-are-different-from-other-microsoft-365-roles"></a>Azure AD ロールとその他の Microsoft 365 ロールとの違い

Microsoft 365 には、Azure AD や Intune といったさまざまなサービスがあります。 これらのサービスの一部は、独自のロールベースのアクセス制御システムを備えています。具体的には次のとおりです。

- Azure AD
- Exchange
- Intune
- Security Center
- コンプライアンス センター
- Microsoft Cloud App Security
- コマース

Teams、SharePoint、マネージド デスクトップなど、他のサービスには個別のロールベースのアクセス制御システムがありません。 これらでは、管理アクセスに Azure AD ロールが使用されます。 Azure には、Azure リソース (仮想マシンなど) 用の独自のロールベースのアクセス制御システムがあり、このシステムは Azure AD ロールとは異なります。

個別のロールベースのアクセス制御システムと言った場合、 それはロールの定義とロールの割り当てが格納されている別のデータ ストアが存在することを意味します。 同様に、アクセス確認が行われるポリシー決定ポイントも別にあります。 詳細については、[Azure AD での Microsoft 365 サービスのロール](m365-workload-docs.md)に関するページと「[従来のサブスクリプション管理者ロール、Azure ロール、および Azure AD ロール](../../role-based-access-control/rbac-and-directory-admin-roles.md)」を参照してください。

## <a name="why-some-azure-ad-roles-are-for-other-services"></a>その他のサービスで一部の Azure AD ロールが使用される理由

Microsoft 365 には、これまで個別に開発されてきたロールベースのアクセス制御システムが多数あり、それぞれに独自のサービス ポータルが用意されています。 Microsoft 365 全体での ID 管理を Azure AD ポータルから便利に行えるように、Microsoft はサービス固有の組み込みロールをいくつか追加しました。これらでは、それぞれの Microsoft 365 サービスへの管理アクセス権が付与されます。 この追加の一例として、Azure AD の Exchange 管理者ロールがあります。 このロールは Exchange のロールベースのアクセス制御システムにおける[組織管理用ロール グループ](/exchange/organization-management-exchange-2013-help)と同等であり、Exchange のあらゆる側面を管理できます。 同様に、Intune 管理者ロール、Teams 管理者、SharePoint 管理者なども追加されました。 サービス固有のロールは、次のセクションに出てくる Azure AD 組み込みロールのカテゴリの 1 つです。

## <a name="categories-of-azure-ad-roles"></a>Azure AD ロールのカテゴリ

Azure AD 組み込みロールには、使用される場所によってさまざまなものが存在し、次の 3 つのカテゴリに大別されます。

- **Azure AD 固有のロール**: これらのロールでは、Azure AD 内のリソースだけを管理するアクセス許可が付与されます。 たとえば、ユーザー管理者、アプリケーション管理者、グループ管理者では、そのどれにおいても Azure AD 内にあるリソースを管理するためのアクセス許可が付与されます。
- **サービス固有のロール**: 主要な Microsoft 365 サービス (Azure AD 以外) では、サービス内のすべての機能を管理するためのアクセス許可を付与するサービス固有のロールが構築してあります。  たとえば、Exchange 管理者、Intune 管理者、SharePoint 管理者、および Teams 管理者ロールでは、それぞれのサービスを使用して機能を管理できます。 Exchange 管理者はメールボックスを管理でき、Intune 管理者はデバイス ポリシーを管理でき、SharePoint 管理者はサイト コレクションを管理でき、Teams 管理者は通話の品質を管理できます。
- **サービス間のロール**: 複数のサービスにわたるロールがいくつかあります。 グローバルなロールとしては、グローバル管理者とグローバル閲覧者の 2 つがあります。 これら 2 つのロールはすべての Microsoft 365 サービスに対して有効です。 また、セキュリティ管理者やセキュリティ閲覧者など、Microsoft 365 内の複数のセキュリティ サービスに対するアクセス権を付与するセキュリティ関連のロールもあります。 たとえば、Azure AD でセキュリティ管理者ロールを使用すると、Microsoft 365 Security Center、Microsoft Defender Advanced Threat Protection、Microsoft Cloud App Security を管理できます。 同様に、コンプライアンス管理者ロールでは、コンプライアンス関連の設定を Microsoft 365 コンプライアンス センターや Exchange などで管理できます。

![Azure AD 組み込みロールの 3 つのカテゴリ](./media/concept-understand-roles/role-overlap-diagram.png)

これらのロール カテゴリの理解に、次の表を役立ててください。 カテゴリの名前は任意で付けられており、[ドキュメントに記載されているロールのアクセス許可](permissions-reference.md)を超える他の機能があることを示すものではありません。

カテゴリ | Role
---- | ----
Azure AD 固有のロール | アプリケーション管理者<br>アプリケーション開発者<br>認証管理者<br>B2C IEF キーセット管理者<br>B2C IEF ポリシー管理者<br>クラウド アプリケーション管理者<br>クラウド デバイス管理者<br>条件付きアクセス管理者<br>デバイス管理者<br>ディレクトリ リーダー<br>ディレクトリ同期アカウント<br>ディレクトリ ライター<br>外部 ID ユーザー フロー管理者<br>外部 ID ユーザー フロー属性管理者<br>外部 ID プロバイダー管理者<br>グループ管理者<br>ゲスト招待元<br>ヘルプデスク管理者<br>ハイブリッド ID の管理者<br>ライセンス管理者<br>パートナー レベル 1 のサポート<br>パートナー レベル 2 のサポート<br>パスワード管理者<br>特権認証管理者<br>特権ロール管理者<br>レポート閲覧者<br>ユーザー アカウント管理者
サービス間のロール | グローバル管理者<br>コンプライアンス管理者<br>コンプライアンス データ管理者<br>グローバル閲覧者<br>セキュリティ管理者<br>セキュリティ オペレーター<br>セキュリティ閲覧者<br>サービス サポート管理者
サービス固有のロール | Azure DevOps 管理者<br>Azure Information Protection 管理者<br>課金管理者<br>CRM サービス管理者<br>カスタマー ロックボックスのアクセス承認者<br>デスクトップ Analytics 管理者<br>Exchange サービス管理者<br>Insights 管理者<br>Insights ビジネス リーダー<br>Intune サービス管理者<br>Kaizala 管理者<br>Lync サービス管理者<br>メッセージ センターのプライバシー閲覧者<br>メッセージ センター閲覧者<br>Modern Commerce ユーザー<br>ネットワーク管理者<br>Office アプリ管理者<br>Power BI サービス管理者<br>Power Platform 管理者<br>プリンター管理者<br>プリンター技術者<br>Search 管理者<br>Search エディター<br>SharePoint サービス管理者<br>Teams 通信管理者<br>Teams 通信サポート エンジニア<br>Teams 通信サポート スペシャリスト<br>Teams デバイス管理者<br>Teams サービス管理者

## <a name="next-steps"></a>次のステップ

- [Azure AD ロールベースのアクセス制御の概要](custom-overview.md)
- [Azure portal、Azure AD PowerShell、および Graph API](custom-create.md) を使用してロールの割り当てを作成する
- [ロールの割り当てを一覧表示する](view-assignments.md)
