---
title: 従来のサブスクリプション管理者ロール、Azure ロール、および Azure AD ロール
description: Azure のロールである、従来のサブスクリプション管理者ロール、Azure ロール、および Azure Active Directory (Azure AD) ロールのそれぞれについて説明します
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 174f1706-b959-4230-9a75-bf651227ebf6
ms.service: role-based-access-control
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 01/04/2021
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: it-pro;
ms.openlocfilehash: 24ff39fba5ce4b484f23595a9f7d718b12faae10
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102037270"
---
# <a name="classic-subscription-administrator-roles-azure-roles-and-azure-ad-roles"></a>従来のサブスクリプション管理者ロール、Azure ロール、および Azure AD ロール

Azure を初めて使用する場合、Azure のさまざまなロールを理解することは少し難しいかもしれません。 この記事では、以下のロールと、それぞれをどのような場合に使用するかについて説明します。
- 従来のサブスクリプション管理者ロール
- Azure ロール
- Azure Active Directory (Azure AD) ロール

## <a name="how-the-roles-are-related"></a>各ロールの関係

Azure のロールをよりよく理解するには、歴史の一部を知ることが役立ちます。 Azure が最初にリリースされたとき、リソースへのアクセスは次の 3 つの管理者ロールで管理されました:アカウント管理者、サービス管理者、共同管理者。 その後、Azure ロールベースのアクセス制御 (Azure RBAC) が追加されます。 Azure RBAC は、Azure リソースに対するきめ細かいアクセス管理を提供する、より新しい承認システムです。 Azure RBAC には多数の組み込みロールがあり、異なるスコープで割り当てることができます。また、独自のカスタム ロールを作成することができます。 ユーザー、グループ、ドメインなどの、Azure AD のリソースを管理するためには、いくつかの Azure AD ロールがあります。

次の図は、従来のサブスクリプション管理者ロール、Azure ロール、および Azure AD ロールがどのように関連しているかを大まかに示しています。

![Azure の各種のロール](./media/rbac-and-directory-admin-roles/rbac-admin-roles.png)


## <a name="classic-subscription-administrator-roles"></a>従来のサブスクリプション管理者ロール

アカウント管理者、サービス管理者、および共同管理者は、Azure の従来の 3 種類のサブスクリプション管理者ロールです。 従来のサブスクリプション管理者には、Azure サブスクリプションへのフル アクセス権があります。 Azure portal、Azure Resource Manager API、およびクラシック デプロイ モデル API を使用して、リソースを管理することができます。 Azure へのサインアップに使用されたアカウントは、自動的にアカウント管理者とサービス管理者の両方として設定されます。 その後、共同管理者を追加できます。 サービス管理者および共同管理者は、サブスクリプション スコープで所有者ロール (Azure ロール) が割り当てられているユーザーと同等のアクセス権を持ちます。 次の表では、これら 3 つの従来のサブスクリプション管理ロールの違いについて説明します。

| 従来のサブスクリプション管理者 | 制限 | アクセス許可 | Notes |
| --- | --- | --- | --- |
| アカウント管理者 | 1 Azure アカウントに 1 人 | <ul><li>[Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) で課金を管理する</li><li>アカウントのすべてのサブスクリプションを管理する</li><li>新しいサブスクリプションを作成する</li><li>サブスクリプションを取り消す</li><li>サブスクリプションの課金を変更する</li><li>サービス管理者を変更する</li></ul> | 概念的には、サブスクリプションの課金の所有者です。 |
| サービス管理者 | 1 Azure サブスクリプションに 1 人 | <ul><li>[Azure portal](https://portal.azure.com) でサービスを管理する</li><li>サブスクリプションを取り消す</li><li>共同管理者ロールにユーザーを割り当てる</li></ul> | 既定で、新しいサブスクリプションのアカウント管理者はサービス管理者でもあります。<br>サービス管理者は、サブスクリプション スコープで所有者ロールを割り当てられているユーザーと同等のアクセス権を持ちます。<br>サービス管理者には、Azure portal へのフル アクセス権が与えられます。 |
| 共同管理者 | サブスクリプションあたり 200 人 | <ul><li>サービス管理者と同じアクセス権を持っているものの、サブスクリプションと Azure ディレクトリとの関連付けを変更することはできない</li><li>共同管理者ロールにユーザーを割り当てる。ただし、サービス管理者を変更することはできない</li></ul> | 共同管理者は、サブスクリプション スコープで所有者ロールを割り当てられているユーザーと同等のアクセス権を持ちます。 |

Azure portal では、 **[従来の管理者]** タブを使用して、共同管理者を管理したり、サービス管理者を表示したりできます。

![Azure portal での Azure の従来のサブスクリプション管理者](./media/rbac-and-directory-admin-roles/subscription-view-classic-administrators.png)

Azure portal では、お使いのサブスクリプションのプロパティ ブレードで、サービス管理者を表示または変更したり、アカウント管理者を表示したりすることができます。

![Azure portal でのアカウント管理者とサービス管理者](./media/rbac-and-directory-admin-roles/account-admin.png)

詳しくは、「[Azure の従来のサブスクリプション管理者](classic-administrators.md)」をご覧ください。

### <a name="azure-account-and-azure-subscriptions"></a>Azure アカウントと Azure サブスクリプション

Azure アカウントは、請求関係を表します。 Azure アカウントは、ユーザー ID、1 つ以上の Azure サブスクリプション、および関連する Azure リソースのセットです。 アカウントを作成する人は、そのアカウントで作成されるすべてのサブスクリプションのアカウント管理者です。 その人は、サブスクリプションの既定のサービス管理者でもあります。

Azure サブスクリプションは、Azure リソースへのアクセスを整理するために役立ちます。 さらに、リソースの使用状況の報告、課金、および支払い方法の制御にも役立ちます。 サブスクリプションごとに異なる課金および支払いを設定することができるため、オフィス別、部門別、プロジェクト別などで、異なるサブスクリプションや異なるプランを利用することができます。 すべてのサービスがサブスクリプションに所属し、プログラムによる操作ではサブスクリプション ID が必要になることがあります。

すべてのサブスクリプションは Azure AD ディレクトリと関連付けられています。 サブスクリプションが関連付けられているディレクトリを検索するには、Azure portal で **[サブスクリプション]** を開き、ディレクトリを表示するサブスクリプションを選択します。

アカウントとサブスクリプションは、[Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) で管理されます。

## <a name="azure-roles"></a>Azure ロール

Azure RBAC は、コンピューティングやストレージなどの Azure リソースに対するきめ細かなアクセス管理を提供する、[Azure Resource Manager](../azure-resource-manager/management/overview.md) 上に構築された承認システムです。 Azure RBAC には、70 を超える組み込みロールが含まれています。 基本的な Azure ロールは 4 つです。 最初の 3 つは、すべてのリソースの種類に適用されます。

| Azure ロール | アクセス許可 | Notes |
| --- | --- | --- |
| [所有者](built-in-roles.md#owner) | <ul><li>すべてのリソースへのフル アクセス</li><li>アクセスを他のユーザーに委任する</li></ul> | サービス管理者と共同管理者は、サブスクリプション スコープで所有者ロールを割り当てられます<br>すべてのリソースの種類に適用されます。 |
| [Contributor](built-in-roles.md#contributor) | <ul><li>すべての種類の Azure リソースを作成および管理する</li><li>Azure Active Directory で新しいテナントを作成する</li><li>他のユーザーにアクセスを許可することはできない</li></ul> | すべてのリソースの種類に適用されます。 |
| [Reader](built-in-roles.md#reader) | <ul><li>Azure リソースを表示する</li></ul> | すべてのリソースの種類に適用されます。 |
| [User Access Administrator](built-in-roles.md#user-access-administrator) | <ul><li>Azure リソースに対するユーザー アクセスを管理する</li></ul> |  |

残りの組み込みロールは、特定の Azure リソースの管理を許可します。 たとえば、[仮想マシン共同作成者](built-in-roles.md#virtual-machine-contributor)ロールが割り当てられたユーザーには、仮想マシンの作成と管理が許可されます。 すべての組み込みロールの一覧については、[Azure の組み込みロール](built-in-roles.md)に関するページを参照してください。

Azure RBAC は Azure portal と Azure Resource Manager API の組み合わせのみでサポートされています。 Azure ロールが割り当てられているユーザー、グループ、アプリケーションは、[Azure クラシック デプロイ モデル API](../azure-resource-manager/management/deployment-models.md) を使用できません。

Azure portal では、Azure RBAC を使用したロールの割り当てが **[アクセス制御 (IAM)]** ブレードに表示されます。 このブレードは、管理グループ、サブスクリプション、リソース グループ、各種リソースなど、ポータル全体で表示されます。

![Azure portal のアクセス制御 (IAM) ブレード](./media/rbac-and-directory-admin-roles/access-control-role-assignments.png)

**[ロール]** タブをクリックすると、組み込みロールとカスタム ロールの一覧が表示されます。

![Azure portal での組み込みロール](./media/rbac-and-directory-admin-roles/roles-list.png)

詳細については、[Azure portal を使用して Azure ロールを割り当てる方法](role-assignments-portal.md)に関するページを参照してください。

## <a name="azure-ad-roles"></a>Azure AD ロール

Azure AD ロールは、ディレクトリ内の Azure AD リソースの管理に使用されます。たとえば、ユーザーの作成や編集、他のユーザーへの管理ロールの割り当て、ユーザー パスワードのリセット、ユーザー ライセンスの管理、ドメインの管理などです。 次の表では、より重要な Azure AD ロールのいくつかについて説明します。

| Azure AD ロール | アクセス許可 | Notes |
| --- | --- | --- |
| [全体管理者](../active-directory/roles/permissions-reference.md#global-administrator) | <ul><li>Azure Active Directory のすべての管理機能や、Azure Active Directory に統合されたサービスへのアクセスを管理する</li><li>他のユーザーに管理者ロールを割り当てる</li><li>すべてのユーザーと他のすべての管理者のパスワードをリセットする</li></ul> | Azure Active Directory テナントにサインアップしたユーザーが全体管理者になります。 |
| [ユーザー管理者](../active-directory/roles/permissions-reference.md#user-administrator) | <ul><li>ユーザーとグループのすべての側面を作成および管理する</li><li>サポート チケットの管理</li><li>サービス正常性の監視</li><li>ユーザー、ヘルプデスク管理者、およびその他のユーザー管理者のパスワードを変更する</li></ul> |  |
| [課金管理者](../active-directory/roles/permissions-reference.md#billing-administrator) | <ul><li>購入する</li><li>サブスクリプションの管理</li><li>サポート チケットの管理</li><li>サービスの正常性を監視する</li></ul> |  |

Azure portal では、Azure AD ロールの一覧が **[ロールと管理者]** ブレードに表示されます。 すべての Azure AD ロールの一覧については、「[Azure Active Directory での管理者ロールのアクセス許可](../active-directory/roles/permissions-reference.md)」を参照してください。

![Azure portal での Azure AD ロール](./media/rbac-and-directory-admin-roles/directory-admin-roles.png)

## <a name="differences-between-azure-roles-and-azure-ad-roles"></a>Azure ロールと Azure AD ロールの違い

大まかに言えば、Azure ロールは Azure リソースを管理するアクセス許可を制御し、Azure AD ロールは Azure Active Directory リソースを管理するアクセス許可を制御します。 次の表は、相違点の一部を比較しています。

| Azure ロール | Azure AD ロール |
| --- | --- |
| Azure のリソースへのアクセスの管理 | Azure Active Directory リソースへのアクセスを管理する |
| カスタム ロールをサポートする | カスタム ロールをサポートする |
| スコープを複数のレベル (管理グループ、サブスクリプション、リソース グループ、リソース) で指定できる | スコープはテナント レベルで |
| ロール情報には、Azure portal、Azure CLI、Azure PowerShell、Azure Resource Manager テンプレート、REST API でアクセスできる | ロール情報には、Azure 管理ポータル、Microsoft 365 管理センター、Microsoft Graph、AzureAD PowerShell でアクセスできる |

### <a name="do-azure-roles-and-azure-ad-roles-overlap"></a>Azure ロールと Azure AD ロールは重なり合うか

既定では、Azure ロールと Azure AD ロールは、Azure と Azure AD にまたがっていません。 ただし、全体管理者が Azure portal で **[Azure リソースのアクセス管理]** スイッチを選択して自分のアクセスを昇格させた場合、全体管理者は特定のテナントのすべてのサブスクリプションに対する [ユーザー アクセス管理者](built-in-roles.md#user-access-administrator)ロール (Azure ロール) を許可されます。 ユーザー アクセス管理者ロールを使用すると、ユーザーは他のユーザーに Azure リソースに対するアクセス権を付与できます。 このスイッチは、サブスクリプションへのアクセス権を回復する場合に便利です。 詳細については、「[Azure のすべてのサブスクリプションと管理グループを管理する目的でアクセス権限を昇格させる](elevate-access-global-admin.md)」を参照してください。

いくつかの Azure AD ロール (全体管理者ロールやユーザー管理者ロールなど) は、Azure AD と Microsoft 365 にまたがっています。 たとえば、全体管理者ロールのメンバーであれば、Azure AD および Microsoft 365 における全体管理者機能を持つことになります。たとえば、Microsoft Exchange や Microsoft SharePoint を変更する機能などです。 ただし、既定では、全体管理者は Azure リソースにアクセスできません。

![Azure RBAC ロールと Azure AD ロール](./media/rbac-and-directory-admin-roles/azure-office-roles.png)

## <a name="next-steps"></a>次のステップ

- [Azure ロールベースのアクセス制御 (Azure RBAC) とは](overview.md)
- [Azure Active Directory での管理者ロールのアクセス許可](../active-directory/roles/permissions-reference.md)
- [Azure の従来のサブスクリプション管理者](classic-administrators.md)
