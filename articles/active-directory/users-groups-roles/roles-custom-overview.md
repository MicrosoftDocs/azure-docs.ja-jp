---
title: アクセス許可をカスタマイズできる Azure 管理者ロールをプレビューする - Azure Active Directory | Microsoft Docs
description: ID 管理を委任するためのカスタム Azure AD ロールをプレビューします。 Azure portal、PowerShell、または Graph API で Azure ロールを管理します。
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 07/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: eabf29b10814d19e89c21f27ec66fce5355c9bfb
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2019
ms.locfileid: "68880718"
---
# <a name="custom-administrator-roles-in-azure-active-directory-preview"></a>Azure Active Directory でのカスタム管理者ロール (プレビュー)

この記事では、Azure Active Directory (Azure AD) での新しいカスタム ロールベースのアクセス制御 (RBAC) とリソース スコープを理解する方法について説明します。 カスタム RBAC ロールでは、[組み込みロール](directory-assign-admin-roles.md)の基になっているアクセス許可が公開されるので、独自のカスタム ロールを作成して整理することができます。 この方法では、必要に応じて、組み込みのロールよりもきめ細かな方法でアクセスを許可できます。 カスタム RBAC ロールの最初のリリースには、アプリ登録管理用のアクセス許可を割り当てるためにロールを作成する機能が含まれています。 今後、エンタープライズ アプリケーション、ユーザー、デバイスなど、組織のリソースに対するアクセス許可がさらに追加されます。  

さらに、カスタム RBAC ロールでは、従来の組織全体の割り当てに加えて、リソースごとに割り当てがサポートされます。 この方法を利用すると、すべてのリソース (すべてのアプリの登録) にアクセスすることなく、一部のリソース (たとえば 1 つのアプリの登録) を管理するアクセスを許可することができます。

Azure AD のロールベースのアクセス制御は Azure AD のパブリック プレビュー機能であり、すべての有料 Azure AD ライセンス プランで利用できます。 詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="understand-azure-ad-role-based-access-control"></a>Azure AD のロールベースのアクセス制御を理解する

カスタム RBAC ロールを使用してアクセス許可を付与することは、カスタム ロール定義の作成と、ロールの割り当てを使用した割り当てという 2 ステップのプロセスです。 カスタム ロールの定義は、プリセットの一覧から追加するアクセス許可のコレクションです。 これらのアクセス許可は、組み込みロールで使用されるものと同じアクセス許可です。  

ロール定義を作成したら、ロールの割り当てを作成することによって、ロールをユーザーに割り当てます。 ロールの割り当てによって、特定のスコープのロール定義に含まれるアクセス許可が誰かに付与されます。 この 2 ステップのプロセスにより、1 つのロール定義を作成し、異なるスコープで何度もそれを割り当てることができます。 スコープには、ロール メンバーがアクセスできるリソースのセットを定義します。 最も一般的なスコープは、組織全体 (org-wide) のスコープです。 カスタム ロールは、組織全体のスコープで割り当てることができます。つまり、ロール メンバーには組織内のすべてのリソースに対してロールのアクセス許可が付与されます。 カスタム ロールは、オブジェクトのスコープで割り当てることもできます。 オブジェクト スコープの例は単一のアプリケーションです。 これにより、同じロールを、Sally に対しては組織内のすべてのアプリケーションについて割り当て、Naveen に対しては Contoso Expense Reports アプリについてだけ割り当てる、といったことができます。  

Azure AD RBAC は、[Azure のロールベースのアクセス制御](../../role-based-access-control/overview.md)と同様の概念で動作します。 違いは、Azure RBAC では仮想マシンや Web サイトなどの Azure リソースへのアクセスが制御され、Azure AD RBAC では Azure AD へのアクセスが制御されることです。 どちらのシステムにも、ロールの定義とロールの割り当ての概念が利用されています。

### <a name="role-assignments"></a>ロールの割り当て

ロールの割り当ては、アクセスを許可する目的で、特定のスコープのユーザーにロールの定義をアタッチするプロセスです。 アクセスは、ロールの割り当てを作成することによって許可され、ロールの割り当てを削除することによって取り消されます。 ロールの割り当ては、次の 3 つの要素で構成されます。
- User
- ロール定義
- リソースのスコープ

[ロールの割り当ての作成](roles-create-custom.md)は、Azure portal、Azure AD PowerShell、または Graph API を使って行うことができます。 [カスタム ロールの割り当てを表示する](roles-view-assignments.md#view-the-assignments-of-a-role-with-single-application-scope-using-the-azure-ad-portal-preview)こともできます。

次の図では、ロールの割り当ての例を示します。 この例では、Contoso Widget Builder アプリ登録のスコープで、Chris Green にアプリ登録管理者のカスタム ロールが割り当てられています。 この割り当てにより、Chris には、この特定のアプリ登録に対してのみ、アプリ登録管理者ロールのアクセス許可が付与されます。

![ロールの割り当てはアクセス許可を適用する方法であり、3 つの部分で構成される](./media/roles-custom-overview/rbac-overview.png)

### <a name="security-principal"></a>セキュリティ プリンシパル

セキュリティ プリンシパルとは、Azure AD リソースへのアクセスが割り当てられるユーザーまたはサービス プリンシパルを表します。 "*ユーザー*" は、Azure Active Directory 内にプロファイルを持つ個人です。

### <a name="role"></a>Role

ロール定義またはロールは、アクセス許可のコレクションです。 ロール定義には、Azure AD リソースに対して実行できる操作 (作成、読み取り、更新、削除など) が列記されています。 Azure AD には 2 種類のロールがあります。

- Microsoft によって作成されて変更できない組み込みロール。 グローバル管理者の組み込みロールには、すべての Azure AD リソースに対するすべてのアクセス許可があります。
- 組織によって作成および管理されるカスタム ロール。

### <a name="scope"></a>Scope (スコープ)

スコープとは、ロールの割り当ての一部として特定の Azure AD リソースに対して許可されるアクションの制限です。 ロールを割り当てるときに、特定のリソースに対する管理者のアクセスを制限するスコープを指定できます。 たとえば、開発者にカスタム ロールを付与し、特定のアプリケーション登録の管理だけを許可したい場合は、ロールの割り当てでスコープとして特定のアプリケーション登録を含めることができます。

  > [!Note]
  > カスタム ロールは、ディレクトリ スコープとリソース スコープで割り当てることができます。 管理単位のスコープで割り当てることはまだできません。
  > 組み込みロールは、ディレクトリ スコープと、場合によっては管理単位のスコープで、割り当てることができます。 オブジェクト スコープで割り当てることはまだできません。

## <a name="required-license-plan"></a>必要とされるライセンス プラン

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>次の手順

- [Azure portal、Azure AD PowerShell、および Graph API](roles-create-custom.md) を使用して、カスタム ロールの割り当てを作成する
- [カスタム ロールの割り当てを表示する](roles-view-assignments.md#view-the-assignments-of-a-role-with-single-application-scope-using-the-azure-ad-portal-preview)
