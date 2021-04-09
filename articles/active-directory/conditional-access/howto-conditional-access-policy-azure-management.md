---
title: 条件付きアクセス - Azure 管理のために MFA を必須にする - Azure Active Directory
description: カスタムの条件付きアクセス ポリシーを作成して、Azure 管理タスクのために多要素認証を必須にします
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2e2b6b3e9a6bdead4e4da7f1a829698d86cfbf52
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92366175"
---
# <a name="conditional-access-require-mfa-for-azure-management"></a>条件付きアクセス:Azure 管理のために MFA を必須にする

組織はさまざまな Azure サービスを使用すると共に、それらを Azure Resource Manager ベースの次のようなツールで管理します。

* Azure portal
* Azure PowerShell
* Azure CLI

これらのツールは、リソースに対する高い特権のアクセスを提供できます。このアクセス権によって、サブスクリプション全体の構成、サービス設定、サブスクリプションの課金を変更できます。 これらの特権リソースを保護するために、Microsoft では、これらのリソースにアクセスするすべてのユーザーに対して多要素認証を必須にすることをお勧めします。

## <a name="user-exclusions"></a>ユーザーの除外

条件付きアクセス ポリシーは強力なツールであり、以下のアカウントはポリシーから除外することをお勧めします。

* **緊急アクセス用** アカウントまたは **非常用** アカウント。テナント全体でアカウントがロックアウトされるのを防ぎます。 発生する可能性は低いシナリオですが、すべての管理者がテナントからロックアウトされた場合に、ご自身の緊急アクセス用管理アカウントを使用してテナントにログインし、アクセスを復旧する手順を実行できます。
   * 詳細については、「[Azure AD で緊急アクセス用アカウントを管理する](../roles/security-emergency-access.md)」を参照してください。
* **サービス アカウント** と **サービス プリンシパル** (Azure AD Connect 同期アカウントなど)。 サービス アカウントは、特定のユーザーに関連付けられていない非対話型のアカウントです。 これらは通常、アプリケーションへのプログラムによるアクセスを可能にするバックエンド サービスによって使用されますが、管理目的でシステムにサインインする場合にも使用されます。 プログラムでは MFA を完了できないため、このようなサービス アカウントは対象外とする必要があります。 サービス プリンシパルによって行われた呼び出しは、条件付きアクセスによってブロックされることはありません。
   * 組織のスクリプトまたはコードでこれらのアカウントが使用されている場合は、それを[マネージド ID](../managed-identities-azure-resources/overview.md) に置き換えることを検討してください。 これらの特定のアカウントは、一時的な回避策として、ベースライン ポリシーの対象外にすることができます。

## <a name="create-a-conditional-access-policy"></a>条件付きアクセス ポリシーを作成する

次の手順では、[Microsoft Azure の管理](concept-conditional-access-cloud-apps.md#microsoft-azure-management)アプリへのアクセス権を持つユーザーに対して、多要素認証の実行を必須にする条件付きアクセス ポリシーを作成します。

1. **Azure portal** にグローバル管理者、セキュリティ管理者、または条件付きアクセス管理者としてサインインします。
1. **[Azure Active Directory]**  >  **[セキュリティ]**  >  **[条件付きアクセス]** の順に移動します。
1. **[新しいポリシー]** を選択します。
1. ポリシーに名前を付けます。 ポリシーの名前に対する意味のある標準を組織で作成することをお勧めします。
1. **[割り当て]** で、 **[ユーザーとグループ]** を選択します。
   1. **[Include]\(含める\)** で、 **[すべてのユーザー]** を選択します。
   1. **[除外]** で、 **[ユーザーとグループ]** を選択し、組織の緊急アクセス用または非常用アカウントを選択します。 
   1. **[Done]** を選択します。
1. **[Cloud apps or actions]\(クラウド アプリまたはアクション\)**  >  **[Include]\(含める\)** で、 **[アプリを選択]** を選択し、 **[Microsoft Azure Management]\(Microsoft Azure の管理\)** を選択し、 **[Select]\(選択する\)** 、 **[Done]\(完了\)** の順に選択します。
1. **[条件]**  >  **[クライアント アプリ (プレビュー)]** の **[このポリシーを適用するクライアント アプリを選択します]** で、すべてを既定値が選択された状態のままにして、 **[完了]** を選択します。
1. **[アクセス制御]**  >  **[許可]** で、 **[アクセス権の付与]** 、 **[Require multi-factor authentication]\(多要素認証を要求する\)** の順に選択し、 **[Select]\(選択する\)** を選択します。
1. 設定を確認し、 **[Enable policy]\(ポリシーの有効化\)** を **[オン]** に設定します。
1. **[作成]** を選択して、ポリシーを作成および有効化します。

## <a name="next-steps"></a>次のステップ

[Conditional Access common policies](concept-conditional-access-policy-common.md) (条件付きアクセスの一般的なポリシー)

[条件付きアクセスのレポート専用モードを使用した影響を判断する](howto-conditional-access-insights-reporting.md)

[Simulate sign in behavior using the Conditional Access What If tool](troubleshoot-conditional-access-what-if.md) (条件付きアクセスの What If ツールを使用したサインイン動作のシミュレート)
