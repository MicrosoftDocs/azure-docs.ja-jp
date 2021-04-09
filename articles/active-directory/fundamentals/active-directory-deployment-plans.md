---
title: デプロイ計画 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory の機能を多数デプロイする方法に関するエンド ツー エンドのガイダンスです。
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: baselden
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4185ffd644d54c419f42c78326ca10bf100443c3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99051432"
---
# <a name="azure-active-directory-deployment-plans"></a>Azure Active Directory のデプロイ計画
ここでは、Azure Active Directory (Azure AD) の機能のデプロイについてのエンド ツー エンドのガイダンスを紹介しています。 Azure AD のデプロイ計画では、Azure AD の代表的な機能について、そのビジネス上の価値や計画の考慮事項、正しくデプロイするうえで必要な運用手順をひととおり説明しています。

任意の計画ページから、ブラウザーの PDF への出力機能を使用して、最新のオフライン バージョンのドキュメントを作成します。


## <a name="deploy-authentication"></a>認証のデプロイ

| 機能 | 説明|
| -| -|
| [Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)| Azure AD Multi-Factor Authentication (MFA) は、Microsoft の 2 段階認証ソリューションです。 管理者が承認した認証方式を使用することにより、Azure AD MFA では、シンプルなサインイン プロセスへの要求に応えながら、データやアプリケーションへのアクセスを保護することができます。 [多要素認証を構成してテナントに適用する方法](https://www.youtube.com/watch?v=qNndxl7gqVM)に関するこちらのビデオをご覧ください。|
| [条件付きアクセス](../conditional-access/plan-conditional-access.md)| 条件付きアクセスを使用すると、クラウド アプリへのアクセスをだれに許可するかを各種の条件に基づいて自動的に判断するアクセスの制御を実装できます。 |
| [セルフサービス パスワード リセット](../authentication/howto-sspr-deployment.md)| セルフサービス パスワード リセット により、ユーザーは、必要に応じていつでも、どこでも、管理者の介入なしでパスワードをリセットできます。 |
| [パスワードレス](../authentication/howto-authentication-passwordless-deployment.md) | 組織内の Microsoft Authenticator アプリまたは FIDO2 セキュリティ キーを使用してパスワードレス認証を実装します。 |

## <a name="deploy-application-and-device-management"></a>アプリケーションおよびデバイス管理のデプロイ

| 機能 | 説明|
| -| - |
| [シングル サインオン](../manage-apps/plan-sso-deployment.md)| シングル サインオンは、ユーザーが 1 回サインインするだけで作業に必要なアプリとリソースにアクセスできる機能です。 サインインすると、資格情報をもう一度入力する必要なしに、Microsoft Office から SalesForce、Box、さらには内部アプリケーションにアクセスできるようになります。 |
| [マイ アプリ](../manage-apps/my-apps-deployment-plan.md)| すべてのアプリケーションを検出し、それにアクセスするための単純なハブをユーザーに提供します。 ユーザーが、アプリやグループへのアクセスを要求したり、他のユーザーに代わってリソースへのアクセスを管理したりするなどのセルフサービス機能を使用して生産性を向上できるようにします。 |
| [デバイス](../devices/plan-device-deployment.md) | この記事は、デバイスを Azure AD と統合する方法を評価し、実装計画を選択するのに役立ちます。また、サポートされているデバイス管理ツールへの主要なリンクを提供します。 |


## <a name="deploy-hybrid-scenarios"></a>ハイブリッド シナリオのデプロイ

| 機能 | 説明|
| -| -|
| [ADFS からパスワード ハッシュ同期への移行](../hybrid/plan-migrate-adfs-password-hash-sync.md)| パスワード ハッシュ同期では、ユーザー パスワードのハッシュがオンプレミスの Active Directory から Azure AD に同期されます。これにより Azure AD では、オンプレミスの Active Directory とやり取りすることなくユーザーを認証することができます |
| [ADFS からパススルー認証への移行](../hybrid/plan-migrate-adfs-pass-through-authentication.md)| Azure AD パススルー認証を使用すると、ユーザーは同じパスワードを使用して、オンプレミスのアプリケーションとクラウドベースのアプリケーションの両方にサインインできます。 この機能により、ユーザー エクスペリエンスが向上します。ユーザーは、覚えておくパスワードが 1 つ少なくなり、ユーザーがサインイン方法を忘れる可能性が低くなるため IT ヘルプデスクのコストが削減されます。 この機能により、ユーザーが Azure AD を使用してサインインするとき、ユーザーのパスワードがオンプレミスの Active Directory に対して直接検証されます。 |
| [Azure AD アプリケーション プロキシ](../manage-apps/application-proxy-deployment-plan.md) |現在、従業員は、どこでも、いつでも、どんなデバイスからでも生産的であることを望んでいます。 クラウド内の SaaS アプリとオンプレミスの企業アプリにアクセスする必要があります。 Azure AD アプリケーション プロキシを使用すると、コストが高く複雑な仮想プライベート ネットワーク (VPN) や非武装地帯 (DMZ) を使用することなく、この堅牢なアクセスが可能になります。 |
| [シームレス SSO](../hybrid/how-to-connect-sso-quick-start.md)| Azure Active Directory シームレス シングル サインオン (Azure AD シームレス SSO) では、ユーザーが企業ネットワークに接続される会社のデバイスを使用するときに、自動的にサインインを行います。 この機能を使用すると、ユーザーは Azure AD にサインインするためにパスワードを入力する必要がなくなり、通常はユーザー名の入力も不要です。 この機能により、追加のオンプレミス コンポーネントを必要とせずに、認可されたユーザーはクラウドベースのアプリケーションに簡単にアクセスできるようになります。 |

## <a name="deploy-user-provisioning"></a>ユーザー プロビジョニングのデプロイ

| 機能 | 説明|
| -| -|
| [ユーザー プロビジョニング](../app-provisioning/plan-auto-user-provisioning.md)| Azure AD を使用すると、Dropbox、Salesforce、ServiceNow などのクラウド (SaaS) アプリケーションで、ユーザー ID の作成、保守、削除を自動化できます。 |
| [クラウド HR のプロビジョニング](../app-provisioning/plan-cloud-hr-provision.md)| Active Directory へのクラウド HR ユーザー プロビジョニングによって、継続的な ID ガバナンスのための基礎が作成され、権限のある ID データに依存するビジネス プロセスの品質が向上します。 Workday、Successfactors など、ご利用のクラウド HR 製品でこの機能を使用すると、Joiner-Mover-Leaver プロセス (新規採用、退職、異動など) を IT プロビジョニング アクション (作成、有効化、無効化など) にマッピングする規則を構成することによって、従業員や臨時社員の ID ライフサイクルをシームレスに管理できます |

## <a name="deploy-governance-and-reporting"></a>ガバナンスとレポートのデプロイ

| 機能 | 説明|
| -| -|
| [Privileged Identity Management](../privileged-identity-management/pim-deployment-plan.md)| Azure AD Privileged Identity Management (PIM) は、Azure AD、Azure リソース、およびその他の Microsoft Online Services 全体の特権管理者ロールを管理するのに役立ちます。 PIM では Just-In-Time アクセス、承認要求ワークフロー、完全に統合されたアクセス レビューなどのソリューションが提供されるので、リアルタイムに特権ロールの悪意のあるアクティビティを特定し、明らかにして防ぐことができます。 |
| [レポートと監視](../reports-monitoring/plan-monitoring-and-reporting.md)| Azure AD のレポートおよび監視ソリューションは、法令、セキュリティ、運用の各要件と、既存の環境およびプロセスによって異なります。 この記事では、さまざまな設計オプションを紹介し、正しいデプロイ戦略のガイドを示します。 |
| [アクセス レビュー](../governance/deploy-access-reviews.md) | アクセス レビューは、誰にアクセス許可が付与され、何にアクセスしているのかを把握し、管理することができる、ガバナンス戦略の重要な部分です。 この記事では、アクセス レビューを計画および展開して、必要なセキュリティとコラボレーション体制を実現する方法について説明します。 |

## <a name="include-the-right-stakeholders"></a>適切な関係者を含める

新しい機能のデプロイ計画を開始する場合は、組織全体の主な利害関係者を含めることが重要です。 次の各役割を果たす個人を特定して文書化し、それらの人と協力してプロジェクトへの関与方法を判断することをお勧めします。  

次のような役割があります 

|Role |説明 |
|-|-|
|エンドユーザー|機能を実装する対象となるユーザーの代表的なグループ。 多くの場合、パイロット プログラムの変更をプレビューします。
|IT サポート マネージャー|ヘルプデスクの観点から、この変更のサポート可能性に関する情報を提供できる、IT サポート組織の代表。  
|ID アーキテクトまたは Azure グローバル管理者|この変更をどのように組織内の主要な ID 管理インフラストラクチャに合わせるかを定義する責任がある、ID 管理チームの代表。|
|アプリケーション ビジネス所有者 |影響を受けるアプリケーションの全体的なビジネス所有者。アクセスの管理を含む場合があります。  また、エンドユーザーの観点から、この変更のユーザー エクスペリエンスと有用性に関する入力も提供される可能性があります。
|セキュリティ所有者|計画が組織のセキュリティ要件を満たしていることをサインオフできるセキュリティ チームの代表。|
|Compliance Manager|企業、業界、または政府の要件に確実に準拠する責任を負う組織内の個人。|

**次のような関与のレベルがあります。**

- **R** プロジェクト計画と結果の実施に責任を負う 

- **A** プロジェクト計画と成果の承認 

- **C** プロジェクト計画と成果への貢献者 

- **I** プロジェクト計画と成果を知らされる


## <a name="best-practices-for-a-pilot"></a>パイロットのベスト プラクティス
すべてのユーザーに対して機能を有効にする前に、パイロットを使用して、小規模なグループでテストすることができます。 テストの一部として、組織内の各ユース ケースが十分にテストされていることを確認します。 これを組織全体にロールアウトする前に、パイロット ユーザーの特定のグループを対象にすることをお勧めします。

最初の段階では、テストを受けてフィードバックを提供できる IT、ユーザビリティ、およびその他の適切なユーザーを対象にします。 このフィードバックを使用して、ユーザーに伝える情報と指示をさらに開発し、サポート スタッフが確認する可能性がある問題の種類に関する分析情報を提供します。 

大規模なユーザー グループへのロールアウトの拡大は、対象とするグループのスコープを広げることで実行する必要があります。 これは、[動的グループ メンバーシップ](../enterprise-users/groups-dynamic-membership.md)を使用するか、対象グループにユーザーを手動で追加することで実行できます。
