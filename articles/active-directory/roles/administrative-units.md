---
title: Azure Active Directory の管理単位 | Microsoft Docs
description: より細かいレベルでアクセス許可を委任できるように、Azure Active Directory で管理単位を使用します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.topic: overview
ms.subservice: roles
ms.workload: identity
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 49f2c290c69fcadd594d6cbd5879e7d9f5304a42
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102558017"
---
# <a name="administrative-units-in-azure-active-directory"></a>Azure Active Directory の管理単位

この記事では、Azure Active Directory (Azure AD) での管理単位について説明します。 管理単位は 1 つのAzure AD リソースであり、他の Azure AD リソースのコンテナーにすることができます。 管理単位には、ユーザーとグループのみを含めることができます。

管理単位では、ロールのアクセス許可が、組織の任意の定義部分に限定されます。 たとえば、管理単位を使用して、[ヘルプデスク管理者](permissions-reference.md#helpdesk-administrator)のロールを地域のサポート スペシャリストに委任できます。そうすれば、そのスペシャリストが自分のサポートするリージョンのユーザーのみを管理できます。

## <a name="deployment-scenario"></a>デプロイ シナリオ

あらゆる種類の独立した部門で構成される組織では、管理単位を使用して管理スコープを制限することが役立つ可能性があります。 多数の自律的な学部 (経営学部、工学部など) で構成される大きな大学の例を考えてみましょう。 各学部には、アクセスを制御し、ユーザーを管理し、学部のポリシーを設定する IT 管理者のチームがあります。

全体管理者は、以下を行うことができます。

- 経営学部管理単位内の Azure AD ユーザーのみを対象とする管理アクセス許可を含むロールを作成する。
- 経営学部の管理単位を作成する。
- この管理単位に経営学部の学生とスタッフのみを設定する。
- 経営学部の IT チームを、スコープと共にロールに追加する。

## <a name="license-requirements"></a>ライセンスの要件

管理単位を使用するには、各管理単位の管理者に Azure Active Directory Premium ライセンスが必要です。また、管理単位のメンバーに Azure Active Directory Free ライセンスが必要です。 詳細については、「 [Azure AD Premium の概要](../fundamentals/active-directory-get-started-premium.md)」を参照してください。

## <a name="manage-administrative-units"></a>管理単位の管理

管理単位は、Azure portal、PowerShell コマンドレットおよびスクリプト、または Microsoft Graph を使用して管理できます。 詳細については、次を参照してください。

- [ロールを作成、削除、設定し、管理単位に追加する](admin-units-manage.md):詳細な操作方法に関する手順が記載されています。
- [管理単位を使用する](/powershell/azure/active-directory/working-with-administrative-units): PowerShell を使用して管理単位を操作する方法が記載されています。
- [管理単位での Graph のサポート](/graph/api/resources/administrativeunit): Microsoft Graph で管理単位を使用するための詳細なドキュメントが提供されています。

### <a name="plan-your-administrative-units"></a>管理単位を計画する

管理単位を使用して、Azure AD リソースを論理的にグループ化することができます。 IT 部門がグローバルに分散している組織では、適切な地理的境界を定義する管理単位を作成するでしょう。 グローバルな組織に、業務において半自律的なサブ組織がある別のシナリオでは、管理単位でサブ組織を表すことができます。

管理単位を作成する条件は、組織固有の要件によって決まります。 管理単位は、Microsoft 365 サービスをまたがる構造を定義するための一般的な方法です。 Microsoft 365 サービスをまたがる使用を考慮して管理単位を準備することをお勧めします。 管理単位の下にある Microsoft 365 間で共通のリソースを関連付けることができる場合は、管理単位から最大の価値を得ることができます。

次の段階に進むには、組織内の管理単位の作成が予想されます。

1. **初期導入**: 組織は初期条件に基づいて管理単位の作成を開始しますが、条件の適用範囲を絞り込むにつれて管理単位の数が増加していきます。
1. **排除**: 条件を定義すると、不要になった管理単位が削除されます。
1. **安定化**: 組織の構造が定義されており、管理単位の数が短期間で大幅に変更されることはありません。

## <a name="currently-supported-scenarios"></a>現在サポートされているシナリオ

グローバル管理者または特権ロール管理者は、Azure AD ポータルを使用して、次のことを行うことができます。

- 管理単位を管理する
- 管理単位にユーザーとグループをメンバーとして追加する
- 管理単位スコープの管理者ロールに IT スタッフを割り当てる。

管理単位スコープの管理者は、Microsoft 365 管理センターを使用して、管理単位内のユーザーの基本的な管理を行うことができます。 管理単位のスコープであるグループ管理者は、PowerShell、Microsoft Graph、および Microsoft 365 管理センターを使用してグループを管理できます。

>[!Note]
>Microsoft 365 管理センターでは、このセクションで説明されている機能のみを使用できます。 管理単位のスコープである Azure AD ロールでは、組織レベルの機能は使用できません。

以下のセクションでは、さまざまな管理単位シナリオに対する現在のサポート状況について説明しています。

### <a name="administrative-unit-management"></a>管理単位管理

| アクセス許可 |   Graph および PowerShell   | Azure AD ポータル | Microsoft 365 管理センター |
| --- | --- | --- | --- |
| 管理単位の作成と削除   |    サポートされています    |   サポートされています   |    サポートされていません |
| 管理単位のメンバーの個別の追加および削除    |   サポートされています    |   サポートされています   |    サポートされていません |
| CSV ファイルを使用した、管理単位のメンバーの一括での追加および削除   |    サポートされていません     |  サポートされています   |    サポートの計画はありません |
| 管理単位スコープの管理者の割り当て  |     サポートされています    |   サポートされています    |   サポートされていません |
| 属性に基づく、管理単位のメンバーの動的な追加および削除 | サポートされていません | サポートされていません | サポートされていません

### <a name="user-management"></a>[ユーザー管理]

| アクセス許可 |   Graph および PowerShell   | Azure AD ポータル | Microsoft 365 管理センター |
| --- | --- | --- | --- |
| ユーザー プロパティ、パスワード、およびライセンスに対する、管理単位スコープでの管理   |    サポートされています     |  サポートされています   |   サポートされています |
| ユーザー サインインに対する、管理単位スコープでのブロックとブロック解除    |   サポートされています   |    サポートされています   |    サポートされています |
| ユーザーの多要素認証資格情報に対する、管理単位スコープでの管理   |    サポートされています   |   サポートされています   |   サポートされていません |

### <a name="group-management"></a>グループ管理

| アクセス許可 |   Graph および PowerShell   | Azure AD ポータル | Microsoft 365 管理センター |
| --- | --- | --- | --- |
| グループ プロパティおよびメンバーの管理単位スコープでの管理     |  サポートされています   |    サポートされています    |  サポートされていません |
| グループ ライセンスに対する、管理単位スコープでの管理   |    サポートされています  |    サポートされています   |   サポートされていません |

管理単位では、管理アクセス許可にのみスコープが適用されます。 メンバーまたは管理者が[既定のユーザー アクセス許可](../fundamentals/users-default-permissions.md)を使用して管理単位外の他のユーザー、グループ、またはリソースを参照するのを妨げられることはありません。 Microsoft 365 管理センターでは、スコープ管理者の管理単位外のユーザーは除外されます。ただし、Azure AD ポータル、PowerShell、およびその他の Microsoft サービスで他のユーザーを参照することはできます。

## <a name="next-steps"></a>次のステップ

- [管理単位を管理する](admin-units-manage.md)
- [管理単位のユーザーを管理する](admin-units-add-manage-users.md)
- [管理単位のグループを管理する](admin-units-add-manage-groups.md)
- [スコープ付きロールを管理単位に割り当てる](admin-units-assign-roles.md)
