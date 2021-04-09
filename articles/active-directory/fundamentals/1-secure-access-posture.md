---
title: Azure Active Directory を使用した外部コラボレーションのセキュリティ体制を決定する
description: 外部アクセスのセキュリティ プランを実行する前に、何を達成しようとしているのかを確認する必要があります。
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 37c27e84f15a01a2d8832baae137518685de59a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98725443"
---
# <a name="determine-your-security-posture-for-external-access"></a>外部アクセスに対するセキュリティ体制を決定する 

外部アクセスの管理方法について検討する際には、セキュリティとコラボレーションに関する組織全体のニーズを、シナリオごとに評価する必要があります。 日常的なコラボレーションに対して IT チームがどの程度の管理を行う必要があるかを、組織レベルで検討してください。 規制の厳しい業界の組織では、より多くの IT 管理が必要になる場合があります。 たとえば、各外部ユーザーとそのアクセス、およびアクセスの削除を明確に特定し、文書化することが求められる場合もあるでしょう。 この要件は、すべてのアクセスに対して必要になる場合もありますし、特定のシナリオやワークロードに対して必要になる場合もあります。 また、コンサルティング会社では、特定の IT ガードレール内でコラボレートする必要がある外部ユーザーを、エンドユーザーが決定できるようにすることもできます。 

![IT によるコラボレーション管理とエンドユーザーによるコラボレーション管理](media/secure-external-access/1-overall-control.png)

> [!NOTE]
> コラボレーションを厳密に管理しすぎると、IT 予算の増加、生産性の低下、ビジネス成果の遅延につながる可能性があります。 公式のコラボレーションチャネルが煩雑すぎると感じられる場合、エンドユーザーは、IT から提供されたシステムを使用せずに業務を遂行する傾向があります (たとえば、セキュリティで保護されていないドキュメントを電子メールで送信するなど)。

## <a name="think-in-terms-of-scenarios"></a>シナリオの観点から考える

多くの場合 (少なくとも一部のシナリオでは)、IT はセキュリティのためのガードレールを提供したうえで、パートナーのアクセスを委任できます。 IT ガードレールは、従業員がパートナーとコラボレートして作業できるようにしつつ、知的財産の安全も確保できるようにするうえで役立ちます。

組織内でのシナリオを検討する際には、従業員とビジネスパートナーのそれぞれについて、リソースにアクセスする必要性を評価しましょう。 銀行では、特定のリソース (ユーザー アカウント情報など) へのアクセスを、内部従業員の小規模なグループに制限するというコンプライアンス ニーズが存在する場合もあります。 それとは反対に、同じ銀行において、マーケティング キャンペーンに参加しているパートナーの委任アクセスを有効にするケースも考えられます。

![シナリオごとのガバナンスの連続性](media\secure-external-access\1-scenarios.png)

いずれのシナリオにおいても、以下のことを検討しましょう 

* 情報が危険にさらされた場合の影響度

* パートナー側から参照できる他のユーザーの情報を制限する必要があるかどうか

* セキュリティ侵害の被害コストと、一元管理の負担やエンドユーザーの不便との兼ね合い

 まずはコンプライアンス目標を達成するために一元管理を徹底し、その後、必要に応じて管理をエンドユーザーへ委任していくという方法もあります。 場合によっては、すべてのアクセス管理モデルを組織内で同時に共存させることもできるでしょう。 

[パートナーが管理する資格情報](../external-identities/what-is-b2b.md)を使用すると、外部ユーザーが自分の勤める会社のリソースに対してアクセス権を失った際に、自社のリソースへのアクセスも終了されるようにするための重要なシグナルを得ることができます。

## <a name="goals-of-securing-external-access"></a>外部アクセスのセキュリティ確保に関する目標

IT 管理アクセスと委任アクセスでは、目標がそれぞれ異なります。

**IT 管理アクセスの主な目標は次のとおりです。**

* ガバナンス、規制、コンプライアンス (GRC) の目標を達成する。 

* パートナーのアクセスを厳密に管理し、パートナーが参照できる情報 (メンバー ユーザー、グループ、および他のパートナーの情報) を管理する。

**委任管理アクセスの主な目標は次のとおりです。**

* ビジネス所有者が、コラボレートするユーザーを (IT の制約内で) 管理できるようにする。

* ビジネス パートナーが、ビジネス所有者によって定義されたルールに基づいてアクセスを要求できるようにする。

組織の要件とシナリオに応じていずれを採用するにしても、次のことを行う必要があります。 

* **アプリケーション、データ、およびコンテンツ** へのアクセスを制御する。 これは、[Azure AD](https://azure.microsoft.com/pricing/details/active-directory/) と [Microsoft 365](https://www.microsoft.com/microsoft-365/compare-microsoft-365-enterprise-plans) のバージョンに応じて、さまざまな方法で実現できます。 

* **攻撃対象領域を減らす**。 [Privileged Identity Management](../privileged-identity-management/pim-configure.md)、[データ損失防止 (DLP)](/exchange/security-and-compliance/data-loss-prevention/data-loss-prevention)、および[暗号化](/exchange/security-and-compliance/data-loss-prevention/data-loss-prevention)の機能によって、攻撃対象領域を削減できます。

* **アクティビティと監査ログを定期的にレビューし、コンプライアンスを確認する**。 IT は、エンタイトルメント管理を通じてアクセスに関する意思決定をビジネス所有者に委任できます。アクセス レビューは、継続的なアクセスを定期的に確認するための手段となります。 秘密度ラベルを使用した自動データ分類を導入すれば、機密コンテンツの暗号化を自動化し、エンド ユーザーである従業員がコンプライアンスに簡単に準拠できるようにすることができます。

## <a name="next-steps"></a>次のステップ 

リソースへの外部アクセスをセキュリティで保護する方法については、次の記事を参照してください。 アクションは、表示されている順序で実行することをお勧めします。

1. [外部アクセスに対するセキュリティ体制を決定する](1-secure-access-posture.md) (このページのトピック)

2. [現在の状態を理解する](2-secure-access-current-state.md)

3. [ガバナンス プランを作成する](3-secure-access-plan.md)

4. [セキュリティにグループを使用する](4-secure-access-groups.md)

5. [Azure AD B2B に移行する](5-secure-access-b2b.md)

6. [エンタイトルメント管理を使用してアクセスをセキュリティで保護する](6-secure-access-entitlement-managment.md)

7. [条件付きアクセス ポリシーを使用してアクセスをセキュリティで保護する](7-secure-access-conditional-access.md)

8. [秘密度ラベルを使用してアクセスをセキュリティで保護する](8-secure-access-sensitivity-labels.md)

9. [Microsoft Teams、OneDrive、SharePoint へのアクセスをセキュリティで保護する](9-secure-access-teams-sharepoint.md)
 

