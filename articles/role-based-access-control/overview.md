---
title: Azure リソースのロールベースのアクセス制御 (RBAC) の概要 | Microsoft Docs
description: Azure リソースのロールベースのアクセス制御 (RBAC) の概要について説明します。 ロールの割り当てを使用して、Azure リソースへのアクセスを制御します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8f8aadeb-45c9-4d0e-af87-f1f79373e039
ms.service: role-based-access-control
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/12/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 4a5352e96e522a8bd32c428957b3bbfd62c5fa9b
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2019
ms.locfileid: "67275922"
---
# <a name="what-is-role-based-access-control-rbac-for-azure-resources"></a>Azure リソースのロールベースのアクセス制御 (RBAC) の概要

クラウド リソースに対するアクセスの管理は、クラウドが使用している組織にとって重要な機能です。 ロールベースのアクセス制御 (RBAC) は、Azure のリソースにアクセスできるユーザー、そのユーザーがそれらのリソースに対して実行できること、そのユーザーがアクセスできる領域を管理するのに役立ちます。

Azure RBAC は [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) 上に構築された承認システムであり、Azure リソースに対するアクセスをきめ細かく管理できます。

## <a name="what-can-i-do-with-rbac"></a>RBAC でできること

RBAC でできることの例を次に示します。

- あるユーザーにサブスクリプション内の仮想マシンの管理を許可し、別のユーザーに仮想ネットワークの管理を許可します
- DBA グループにサブスクリプション内の SQL データベースの管理を許可します
- あるユーザーに、仮想マシン、Web サイト、サブネットなど、リソース グループ内のすべてのリソースの管理を許可します
- あるアプリケーションに、リソース グループ内のすべてのリソースへのアクセスを許可します

## <a name="best-practice-for-using-rbac"></a>RBAC を使用するためのベスト プラクティス

RBAC を使用して、チーム内で職務を分離し、職務に必要なアクセス許可のみをユーザーに付与します。 すべてのユーザーに Azure サブスクリプションまたはリソースで無制限のアクセス許可を付与するのではなく、特定のスコープで特定の操作のみを許可することができます。

アクセス制御戦略を計画する場合のベスト プラクティスは、ユーザーの作業を実行できる最低限の特権をユーザーに付与することです。 次の図は、RBAC を使用するための推奨パターンを示しています。

![RBAC と最小限の特権](./media/overview/rbac-least-privilege.png)

## <a name="how-rbac-works"></a>RBAC のしくみ

RBAC を使用してリソースへのアクセスを制御するには、ロールの割り当てを作成します。 これは、アクセス許可が適用される方法であり、理解する必要のある重要な概念です。 ロールの割り当ては、セキュリティ プリンシパル、ロールの定義、スコープの 3 つの要素で構成されています。

### <a name="security-principal"></a>セキュリティ プリンシパル

"*セキュリティ プリンシパル*" は、Azure リソースへのアクセスを要求するユーザー、グループ、サービス プリンシパル、またはマネージド ID を表すオブジェクトです。

![ロール割り当てのセキュリティ プリンシパル](./media/overview/rbac-security-principal.png)

- ユーザー - Azure Active Directory 内にプロファイルを持つ個人です。 他のテナント内のユーザーにロールを割り当てることもできます。 他の組織のユーザーについては、「[Azure Active Directory B2B](../active-directory/b2b/what-is-b2b.md)」をご覧ください。
- グループ - Azure Active Directory 内に作成されたユーザーのセットです。 グループにロールを割り当てると、そのグループ内のすべてのユーザーがそのロールを持つようになります。 
- サービス プリンシパル - 特定の Azure リソースにアクセスするためにアプリケーションまたはサービスによって使用されるセキュリティ ID です。 アプリケーションに対する "*ユーザー ID*" (ユーザー名とパスワード、または証明書) と考えることができます。
- マネージド ID - Azure によって自動的に管理される Azure Active Directory 内の ID。 通常、[マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) は、Azure サービスに対する認証を受けるための資格情報を管理するクラウド アプリケーションを開発するときに使用します。

### <a name="role-definition"></a>ロール定義

*ロール定義*はアクセス許可のコレクションです。 単に*ロール*と呼ばれることもあります。 ロール定義には、実行できる操作 (読み取り、書き込み、削除など) が登録されています。 ロールは、所有者のように高レベルにすることも、仮想マシン リーダーのように限定することもできます。

![ロール割り当てのためのロールの定義](./media/overview/rbac-role-definition.png)

Azure には複数の[組み込みロール](built-in-roles.md)があり、使用することができます。 4 つの基本的な組み込みロールを次に示します。 最初の 3 つは、すべてのリソースの種類に適用されます。

- [所有者](built-in-roles.md#owner) - 他のユーザーへアクセス権を委任する権限を含め、すべてのリソースへのフル アクセス権を持ちます。
- [共同作成者](built-in-roles.md#contributor) - Azure リソースのすべての種類を作成および管理できますが、他のユーザーへアクセス権を付与することはできません。
- [閲覧者](built-in-roles.md#reader) - 既存の Azure リソースを表示できます。
- [ユーザー アクセス管理者](built-in-roles.md#user-access-administrator) - Azure リソースへのユーザー アクセスを管理できます。

残りの組み込みロールは、特定の Azure リソースの管理を許可します。 たとえば、[仮想マシン共同作成者](built-in-roles.md#virtual-machine-contributor)ロールが割り当てられたユーザーには、仮想マシンの作成と管理が許可されます。 組み込みロールが組織の特定のニーズを満たさない場合は、独自に [Azure リソースに対するカスタム ロール](custom-roles.md)を作成することができます。

Azure には、オブジェクト内のデータへのアクセスを許可できるようにするデータ操作が用意されています。 たとえば、ユーザーがあるストレージ アカウントへのデータの読み取りアクセス許可を持っている場合、そのユーザーはそのストレージ アカウント内の BLOB またはメッセージを読み取ることができます。 詳しくは、[Azure リソースのロール定義](role-definitions.md)に関する記事をご覧ください。

### <a name="scope"></a>Scope (スコープ)

"*スコープ*" は、アクセスが適用されるリソースのセットです。 ロールを割り当てるときに、スコープを定義することによって、許可される操作をさらに制限できます。 これは、1 つのリソース グループについてのみ、あるユーザーを [Web サイトの共同作業者](built-in-roles.md#website-contributor)として指定する場合に便利です。

Azure では、複数のレベル ([管理グループ](../governance/management-groups/index.md)、サブスクリプション、リソース グループ、リソース) でスコープを指定できます。 スコープは親子関係で構造化されています。

![ロール割り当てのスコープ](./media/overview/rbac-scope.png)

親スコープでアクセス権を付与すると、それらのアクセス許可が子スコープに継承されます。 例:

- 管理グループ スコープでユーザーに[所有者](built-in-roles.md#owner)ロールを割り当てた場合、そのユーザーは、その管理グループに存在する全サブスクリプションの内容をすべて管理することができます。
- [閲覧者](built-in-roles.md#reader)ロールをサブスクリプション スコープでグループに割り当てた場合、そのグループのメンバーは、サブスクリプション内のすべてのリソース グループとリソースを見ることができます。
- [共同作成者](built-in-roles.md#contributor)ロールをリソース グループ スコープでアプリケーションに割り当てた場合、そのアプリケーションは、そのリソース グループ内のすべての種類のリソースを管理できますが、サブスクリプション内の他のリソース グループは管理できません。

### <a name="role-assignments"></a>ロールの割り当て

"*ロールの割り当て*" は、アクセスの許可を目的として、特定のスコープで、ユーザー、グループ、サービス プリンシパル、またはマネージド ID にロールの定義をアタッチするプロセスです。 アクセスは、ロールの割り当てを作成することによって許可され、ロールの割り当てを削除することによって取り消されます。

次の図では、ロールの割り当ての例を示します。 この例では、Marketing グループには、pharma-sales リソース グループに対する[共同作成者](built-in-roles.md#contributor)ロールが割り当てられています。 つまり、Marketing グループのユーザーは、pharma-sales リソース グループ内の任意の Azure リソースを作成または管理できます。 Marketing ユーザーは、別のロール割り当ての一部になっていない限り、pharma-sales リソース グループに含まれないリソースにはアクセスできません。

![アクセスを制御するためのロールの割り当て](./media/overview/rbac-overview.png)

ロールの割り当ては、Azure portal、Azure CLI、Azure PowerShell、Azure SDK、または REST API を使用して作成できます。 各サブスクリプションには、最大 2,000 個のロールの割り当てを保持できます。 ロールの割り当てを作成および削除するには、`Microsoft.Authorization/roleAssignments/*` アクセス許可が必要です。 このアクセス許可は、[所有者](built-in-roles.md#owner)ロールまたは[ユーザー アクセス管理者](built-in-roles.md#user-access-administrator)ロールを通じて許可されます。

## <a name="multiple-role-assignments"></a>複数のロールの割り当て

複数のロールの割り当てが重複しているとどうなるでしょうか。 RBAC は加算方式のモデルであるため、ロール割り当てを足し算した結果が有効なアクセス許可になります。 ここで、ユーザーにサブスクリプション スコープの共同作成者ロールとリソース グループの閲覧者ロールが付与されている例を考えてみましょう。 共同作成者アクセス許可と閲覧者アクセス許可を足すと、事実上、リソース グループに対する共同作成者ロールになります。 そのため、この場合、閲覧者ロールの割り当ては効果がありません。

![複数のロールの割り当て](./media/overview/rbac-multiple-roles.png)

## <a name="deny-assignments"></a>拒否割り当て

これまでの RBAC は拒否のない許可のみのモデルでしたが、限定的にですが RBAC で拒否の割り当てがサポートされるようになりました。 ロールの割り当てと同様に、"*拒否割り当て*" ではアクセスの拒否を目的として、特定のスコープでユーザー、グループ、サービス プリンシパル、またはマネージド ID に一連の拒否アクションがアタッチされます。 ロールの割り当てでは "*許可される*" アクションのセットを定義しますが、拒否割り当てでは "*許可されない*" アクションのセットを定義します。 つまり、拒否割り当てでは、ロールの割り当てでアクセスを許可されている場合であっても、指定したアクションがユーザーによって実行されるのをブロックします。 ロールの割り当てより拒否割り当ての方が優先されます。 詳しくは、「[Azure リソースの拒否割り当ての概要](deny-assignments.md)」をご覧ください。

## <a name="how-rbac-determines-if-a-user-has-access-to-a-resource"></a>ユーザーがリソースへのアクセス権を持っているどうかを RBAC が特定する方法

管理プレーン上のリソースへのアクセス権をユーザーが持っているかどうかを判断するために RBAC が使用する手順の概要を次に示します。 これは、アクセスの問題のトラブルシューティングを行う場合に理解していると役に立ちます。

1. ユーザー (またはサービス プリンシパル) は、Azure Resource Manager に対するトークンを取得します。

    トークンには、ユーザーのグループ メンバーシップが含まれています (推移的なグループ メンバーシップを含みます)。

1. ユーザーは、トークンを添付して Azure Resource Manager への REST API の呼び出しを行います。

1. Azure Resource Manager では、アクション実行対象リソースに適用されるすべてのロール割り当てと拒否割り当てが取得されます。

1. Azure Resource Manager は、このユーザーまたはユーザーのグループに適用されるロールの割り当てを絞り込み、このリソースに対してユーザーが持っているロールを特定します。

1. Azure Resource Manager は、API 呼び出しでのアクションが、このリソースに対してユーザーが持っているロールに含まれるかどうかを判別します。

1. 要求されたスコープでのアクションを含むロールをユーザーが持っていない場合、アクセスは許可されません。 それ以外の場合、Azure Resource Manager は拒否割り当てが適用されるかどうかを確認します。

1. 拒否割り当てが適用される場合、アクセスはブロックされます。 それ以外の場合、アクセスは許可されます。

## <a name="license-requirements"></a>ライセンスの要件

[!INCLUDE [Azure AD free license](../../includes/active-directory-free-license.md)]

## <a name="next-steps"></a>次の手順

- [クイック スタート:Azure portal を使用して Azure リソースに対するユーザーのアクセス権を表示する](check-access.md)
- [RBAC と Azure portal を使用して Azure リソースへのアクセスを管理する](role-assignments-portal.md)
- [Azure での各種ロールについて](rbac-and-directory-admin-roles.md)
- [エンタープライズ クラウドの導入: Azure でのリソース アクセス管理](/azure/architecture/cloud-adoption/governance/resource-consistency/azure-resource-access)
