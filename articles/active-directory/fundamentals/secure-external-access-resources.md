---
title: Azure Active Directory での外部コラボレーションのセキュリティ保護
description: 内部リソースへの外部アクセスをセキュリティで保護するためのアーキテクトと IT 管理者向けのガイド
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
ms.openlocfilehash: bc8ec364380fc5cb9f6e33a29cd8dac96ea1fb69
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98219829"
---
# <a name="securing-external-collaboration-in-azure-active-directory-and-microsoft-365"></a>Azure Active Directory と Microsoft 365 での外部コラボレーションのセキュリティ保護

外部パートナーとのコラボレーションをセキュリティで保護することによって、適切な外部パートナーが適切な時間内に内部リソースに適切にアクセスできるようになります。 総合的なガバナンス アプローチにより、セキュリティ リスクを軽減し、コンプライアンス目標を達成し、アクセス権を持つユーザーを把握することができます。

コラボレーションが管理されていないと、アクセスの所有権を明確にすることができなくなり、機密性の高いリソースが公開されてしまう可能性があります。 セキュリティで保護され、管理されたコラボレーションに移行することで、外部ユーザーのアクセスに対する所有権とアカウンタビリティを明確にすることができます。 これには次のものが含まれます

* リソースへのアクセス権を持つ外部組織 (およびその内部のユーザー) を管理する。

* アクセスが適切であり、レビューされており、必要に応じて期限が設定されていることを保証する。

* IT が作成したガード レール内でコラボレーションを管理する権限をビジネス オーナーに付与する。

コンプライアンス フレームワークを満たす必要がある場合は、管理されたコラボレーションによって、アクセスの妥当性を証明することができます。

Microsoft では、外部アクセスをセキュリティで保護するための包括的なツール スイートを提供しています。  Azure Active Directory (Azure AD) B2B Collaboration は、すべての外部コラボレーション プランの中心にあります。 Azure AD B2B は、Azure AD 内の他のツールや Microsoft 365 サービスのツールと統合して、外部アクセスのセキュリティ保護と管理に役立ちます。

このドキュメント セットは、アドホックまたは緩やかに管理された外部コラボレーションから、より安全な状態に移行できるように設計されています。 

## <a name="next-steps"></a>次のステップ

リソースへの外部アクセスをセキュリティで保護する方法については、次の記事を参照してください。 アクションは、表示されている順序で実行することをお勧めします。


1. [外部アクセスに対するセキュリティ体制を決定する](1-secure-access-posture.md)

2. [現在の状態を理解する](2-secure-access-current-state.md)

3. [ガバナンス プランを作成する](3-secure-access-plan.md)

4. [セキュリティにグループを使用する](4-secure-access-groups.md)

5. [Azure AD B2B に移行する](5-secure-access-b2b.md)

6. [エンタイトルメント管理を使用してアクセスをセキュリティで保護する](6-secure-access-entitlement-managment.md)

7. [条件付きアクセス ポリシーを使用してアクセスをセキュリティで保護する](7-secure-access-conditional-access.md)

8. [秘密度ラベルを使用してアクセスをセキュリティで保護する](8-secure-access-sensitivity-labels.md)

9. [Microsoft Teams、OneDrive、SharePoint へのアクセスをセキュリティで保護する](9-secure-access-teams-sharepoint.md)
