---
title: ID 保護と B2B ユーザー - Azure Active Directory
description: B2B ユーザーに対して ID 保護を使用する方法と、既知の制限事項について説明します
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: c9ec9e110c3a476c9096ae3e216c9780da0e0f49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88949198"
---
# <a name="identity-protection-and-b2b-users"></a>Identity Protection と B2B ユーザー

Azure AD B2B コラボレーションでは、Identity Protection を使用して、B2B ユーザーに対するリスクベースのポリシーを適用できます。 これらのポリシーは、次の 2 つの方法で構成できます。

- 管理者は、組み込みの Identity Protection によるリスクベースのポリシーを構成し、それをすべてのアプリに適用して、ゲストユーザーを対象に含めることができます。
- 管理者は、サインインリスクを条件として使用し、ゲストユーザーを対象に含めた条件付きアクセスポリシーを構成できます。

## <a name="how-is-risk-evaluated-for-b2b-collaboration-users"></a>B2B コラボレーション ユーザーのリスク評価の方法

B2B コラボレーション ユーザーのユーザー リスクは、ホーム ディレクトリで評価されます。 これらのユーザーのリアルタイムなサインイン リスクは、ユーザーがリソースにアクセスしようとしたときに、リソース ディレクトリで評価されます。

## <a name="limitations-of-identity-protection-for-b2b-collaboration-users"></a>B2B コラボレーション ユーザーの ID 保護に関する制限事項

リソース ディレクトリ内における B2B コラボレーション ユーザーの ID 保護の実装については、複数の制限事項があります。これは、それらのユーザーの ID がホーム ディレクトリに存在するためです。 主な制限事項は次のとおりです。

- ゲスト ユーザーが Identity Protection のユーザー リスク ポリシーをトリガーしてパスワードのリセットを強制した場合、**そのユーザーはブロックされます**。 このブロックは、リソース ディレクトリ内ではパスワードをリセットできないことが原因です。
- **ゲスト ユーザーは、危険なユーザー レポートには表示されません**。 この可視性の低下は、リスク評価が B2B ユーザーのホーム ディレクトリで実行されることに起因します。
- 管理者は、リソース ディレクトリ内の **危険な B2B コラボレーション ユーザーを無視したり、修復したりすることはできません**。 この機能低下は、リソース ディレクトリ内の管理者が B2B ユーザーのホーム ディレクトリにアクセスできないことに起因します。

### <a name="why-cant-i-remediate-risky-b2b-collaboration-users-in-my-directory"></a>ディレクトリ内の危険な B2B コラボレーション ユーザーを修復できないのはなぜですか?

B2B ユーザーのリスク評価と修復は、それらのユーザーのホーム ディレクトリで行われます。 そのため、ゲスト ユーザーはリソース ディレクトリの危険なユーザー レポートには表示されず、リソース ディレクトリ内の管理者は、それらのユーザーに対して、セキュリティで保護されたパスワードのリセットを強制することはできません。

### <a name="what-do-i-do-if-a-b2b-collaboration-user-was-blocked-due-to-a-risk-based-policy-in-my-organization"></a>組織内のリスクベース ポリシーによって B2B コラボレーション ユーザーがブロックされた場合はどうすればよいですか?

ディレクトリ内の危険な B2B ユーザーがリスクベースのポリシーによってブロックされた場合、そのユーザーは自分のホーム ディレクトリでそのリスクを修復する必要があります。 ユーザーは、セキュリティで保護されたパスワードのリセットをホーム ディレクトリで実行することにより、リスクを修復できます。 ユーザーのホーム ディレクトリでセルフサービス パスワード リセット が有効になっていない場合は、そのユーザーが組織の IT スタッフに連絡して、管理者にリスクを手動で無視してもらったり、パスワードをリセットしてもらう必要があります。

### <a name="how-do-i-prevent-b2b-collaboration-users-from-being-impacted-by-risk-based-policies"></a>B2B コラボレーション ユーザーがリスクベース ポリシーの影響を受けないようにするにはどうすればよいですか?

組織のリスクベース条件付きアクセスポリシーから B2B ユーザーを除外すると、B2B ユーザーがリスク評価によって影響を受けたり、ブロックされたりするのを防ぐことができます。 これらの B2B ユーザーを除外するには、組織のすべてのゲストユーザーを含むグループを Azure AD に作成します。 その後、組み込みの Identity Protection ユーザー リスク ポリシーとサインイン リスク ポリシーの除外対象として、そのグループを追加します。また、サインイン リスクを条件として使用している条件付きアクセスポリシーについても、同様の操作を行います。

## <a name="next-steps"></a>次のステップ

Azure AD B2B コラボレーションに関する以下の記事を参照してください。

- [Azure AD B2B コラボレーションとは](../external-identities/what-is-b2b.md)