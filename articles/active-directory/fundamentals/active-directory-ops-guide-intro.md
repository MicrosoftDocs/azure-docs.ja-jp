---
title: Azure Active Directory 運用リファレンス ガイド
description: この運用リファレンス ガイドでは、ID とアクセスの管理、認証、ガバナンス、操作をセキュリティで保護して維持するために実行する必要があるチェックとアクションについて説明します。
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: c815256e60e5ca6582220d247d587a4488c4a55a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "74534771"
---
# <a name="azure-active-directory-operations-reference-guide"></a>Azure Active Directory 運用リファレンス ガイド

この運用リファレンス ガイドでは、次の領域をセキュリティ保護して維持するために実行する必要があるチェックとアクションについて説明します。

- **[ID とアクセスの管理](active-directory-ops-guide-iam.md)** - ID とそのエンタイトルメントのライフサイクルを管理する機能。
- **[認証管理](active-directory-ops-guide-auth.md)** - 資格情報の管理、認証エクスペリエンスの定義、割り当ての委任、使用状況の測定、および企業のセキュリティ体制に基づいたアクセス ポリシーの定義を行う機能。
- **[ガバナンス](active-directory-ops-guide-govern.md)** - アクセス許可されている非特権 ID および特権 ID の評価および証明、監査、および環境に対する変更の管理を行う機能。
- **[運用](active-directory-ops-guide-ops.md)** - Azure Active Directory (Azure AD) の運用を最適化します。

ここに示すいくつかの推奨事項は、一部のお客様の環境には適用されない場合があります。たとえば、組織がパスワード ハッシュ同期を使用する場合、AD FS のベスト プラクティスは適用されないことがあります。

> [!NOTE]
> これらの推奨事項は公開日の時点で最新ですが、時間の経過と共に変化する可能性があります。 Microsoft の製品とサービスは時間の経過と共に進化するため、継続的に ID プラクティスを評価する必要があります。 組織が別の Azure AD Premium ライセンスに登録すると、推奨事項が変わる場合があります。 たとえば、Azure AD Premium P2 には、ガバナンスに関するより多くの推奨事項が含まれます。

## <a name="stakeholders"></a>利害関係者

このリファレンス ガイドの各セクションでは、主要なタスクを正しく計画して実装するために利害関係者を割り当てることを推奨しています。 次の表に、このガイドのすべての利害関係者の一覧を示します。

| 利害関係者 | 説明 |
| :- | :- |
| IAM 運用チーム | このチームは、ID およびアクセス管理システムの日常業務の管理を担当します。 |
| 生産性チーム | このチームは、メール、ファイル共有とコラボレーション、インスタント メッセージング、会議などの生産性アプリケーションを所有し、管理します。 |
| Application Owner | このチームは、ビジネスに関する特定のアプリケーションを、通常は組織の技術的な観点から所有します。 |
| InfoSec アーキテクチャ チーム | このチームは、組織の情報セキュリティ プラクティスを計画および設計します。 |
| InfoSec 運用チーム | このチームは、InfoSec アーキテクチャ チームの実装された情報セキュリティ プラクティスを実行および監視します。 |

## <a name="next-steps"></a>次のステップ

[ID とアクセス管理のチェックとアクション](active-directory-ops-guide-iam.md)を開始します。
