---
title: Azure Active Directory (Azure AD) Identity Protection のセキュリティの概要 | Microsoft Docs
description: '[セキュリティの概要] で、組織のセキュリティ体制の分析情報を得る方法について説明します。'
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 02/14/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: b894f7020083dd6ca46c394ec2930a3da36bc76d
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335207"
---
# <a name="azure-active-directory-identity-protection---security-overview"></a>Azure Active Directory Identity Protection - セキュリティの概要

この[セキュリティの概要](https://aka.ms/IdentityProtectionRefresh)では、組織のセキュリティ体制の分析情報を得ることができます。 潜在的な攻撃を特定し、ポリシーの有効性を把握するために役立ちます。

[セキュリティの概要] は、大まかに 2 つのセクションに分かれています。

- 左側にある [Trends] には、組織内のリスクのタイムラインが表示されます。
- 右側の [タイル] では、組織内で発生している重要な問題が強調表示され、迅速に対処する方法が提案されます。

![セキュリティの概要](./media/security-overview/01.png)
  
## <a name="trends"></a>Trends

### <a name="new-risky-users-detected"></a>新しい危険なユーザーが検出されました

このグラフは、選択した期間中に検出された新しい危険なユーザーの数を示しています。 このグラフの表示は、ユーザー リスク レベル (低、中、高) でフィルター処理できます。 UTC の日付にマウスを合わせると、その日に検出された危険なユーザーの数が表示されます。 このグラフをクリックすると、[危険なユーザー] レポートに移動します。 危険なユーザーを修復するには、そのユーザーのパスワードを変更することを検討してください。

### <a name="new-risky-sign-ins-detected"></a>新しい危険なサインインが検出されました

このグラフは、選択した期間中に検出された危険なサインインの数を示しています。 このグラフの表示は、サインイン リスクの種類 (リアルタイムまたは集計) とサインイン リスク レベル (低、中、高) でフィルター処理できます。 保護されていないサインインは、MFA チャレンジが実行されていない成功したリアルタイムの危険なサインインです (注: オフラインの検出であるために危険なサインインは、サインイン リスク ポリシーによってリアルタイムで保護することができません)。 UTC の日付にマウスを合わせると、その日に検出された危険なサインインの数が表示されます。 このグラフをクリックすると、[危険なサインイン] レポートに移動します。

## <a name="tiles"></a>タイル
 
### <a name="high-risk-users"></a>危険性が高いユーザー

[危険性が高いユーザー] タイルには、ID が侵害されている可能性が高いユーザーの最新の数が表示されます。 これらは最優先で調査する必要があります。 [危険性が高いユーザー] タイルをクリックすると、リスク レベルが高いユーザーのみが表示される [危険なユーザー] レポートのフィルター処理済みビューにリダイレクトされます。 このレポートでは詳細を確認できます。また、パスワードのリセットを使ってこのようなユーザーを修復することができます。

![セキュリティの概要](./media/security-overview/02.png)

### <a name="medium-risk-users"></a>危険性が中度のユーザー
[危険性が中度のユーザー] タイルには、ID が侵害されている可能性が中度のユーザーの最新の数が表示されます。 [危険性が中度のユーザー] タイルをクリックすると、リスク レベルが中度のユーザーのみが表示される [危険なユーザー] レポートのフィルター処理済みビューにリダイレクトされます。 このレポートを使用すると、このようなユーザーをさらに調査し、修復することができます。

### <a name="unprotected-risky-sign-ins"></a>保護されていない危険なサインイン

[保護されていない危険なサインイン] タイルには、先週の成功したリアルタイムの危険なサインインのうち、条件付きアクセス ポリシー、Identity Protection リスク ポリシー、またはユーザーごとの MFA によってブロックも MFA チャレンジも実行されていないサインインの数が表示されます。 これらは、成功はしましたが MFA チャレンジが実行されていない、侵害された可能性のあるログインです。 今後、このようなサインインを保護するには、サインインのリスク ポリシーを適用してください。 [保護されていない危険なサインイン] タイルをクリックすると、サインインのリスク ポリシー構成ブレードにリダイレクトされます。ここで、指定されたリスク レベルのサインインに対して MFA を要求するようにサインイン リスク ポリシーを構成できます。

### <a name="legacy-authentication"></a>レガシ認証

[レガシ認証] タイルには、組織内のレガシ認証の先週の数が表示されます。 レガシ認証プロトコルは、MFA などの最新のセキュリティ方式をサポートしていません。 レガシ認証を防ぐには、条件付きアクセス ポリシーを適用できます。 [レガシ認証] タイルをクリックすると、[ID セキュリティ スコア] にリダイレクトされます。

### <a name="identity-secure-score"></a>ID セキュリティ スコア

ID セキュリティ スコアでは、セキュリティの状態が測定され、業界のパターンと比較されます。 [Identity Secure Score (プレビュー)] タイルをクリックすると、[Identity Secure Score (プレビュー)] ブレードにリダイレクトされます。ここで、セキュリティ体制の改善について詳細を確認することができます。

## <a name="next-steps"></a>次の手順

- [Channel 9:Azure AD and Identity Show: Identity Protection Preview](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview) (Channel 9: Azure AD および Identity ショー: Identity Protection プレビュー)
- [Azure Active Directory Identity Protection の有効化](enable.md)
