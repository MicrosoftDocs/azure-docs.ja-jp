---
title: Azure Lighthouse のシナリオにおけるテナント、ユーザー、ロール
description: Azure Active Directory のテナント、ユーザー、およびロールの概念と、それらを Azure Lighthouse のシナリオで使用する方法について説明します。
ms.date: 01/14/2021
ms.topic: conceptual
ms.openlocfilehash: d78828cc739030f8e456c64885d77ddf59dd13fb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98233918"
---
# <a name="tenants-users-and-roles-in-azure-lighthouse-scenarios"></a>Azure Lighthouse のシナリオにおけるテナント、ユーザー、ロール

顧客を [Azure Lighthouse](../overview.md) にオンボードする前に、Azure Active Directory (Azure AD) のテナント、ユーザー、およびロールがどのように機能するか、およびそれらを Azure Lighthouse シナリオでどのように使用するかを理解しておくことが重要です。

*テナント* とは、Azure AD の信頼された専用インスタンスです。 通常、各 Azure テナントは 1 つの組織を表します。 [Azure の委任されたリソース管理](azure-delegated-resource-management.md)を使用すると、あるテナントから別のテナントにリソースを論理的に投影できます。 これにより、(サービス プロバイダーに属するユーザーなどの) 管理テナントのユーザーは、顧客のテナント内の委任されたリソースにアクセスできるようになります。また、[複数のテナントを持つ企業が、管理操作を一元的に実行できるようになります。](enterprise.md)

この論理的な投影を実現するには、顧客テナントのサブスクリプション (またはサブスクリプション内の 1 つ以上のリソース グループ) を、Azure Lighthouse に "*オンボード*" する必要があります。 オンボードは、[Azure Resource Manager のテンプレート](../how-to/onboard-customer.md)を使用して行うか、[Azure Marketplace にパブリックまたはプライベート サービスを発行して行います](../how-to/publish-managed-services-offers.md)。

いずれのオンボード方法を選択しても、*承認* を定義する必要があります。 承認ワークフローごとに、委任されたリソースへのアクセス権が与えられる **principalId** と、これらの各ユーザーがこれらのリソースに対して持つアクセス許可を設定する組み込みのロールが指定されます。 この **principalId** により、管理テナントの Azure AD ユーザー、グループ、サービス プリンシパルが定義されます。

> [!NOTE]
> 明示的に指定されていない限り、Azure Lighthouse ドキュメント内での "ユーザー" への参照は、承認に含まれる Azure AD ユーザー、グループ、またはサービス プリンシパルに適用できます。

## <a name="best-practices-for-defining-users-and-roles"></a>ユーザーとロールを定義するためのベスト プラクティス

ご自分の承認を作成するときは、次のベスト プラクティスに従うことをお勧めします。

- ほとんどの場合、一連の個々のユーザー アカウントではなく、Azure AD のユーザー グループまたはサービス プリンシパルにアクセス許可を割り当てます。 これにより、実際のアクセス要件が変更されたときに、プランを更新して再発行することなく、個々のユーザーのアクセス権を追加または削除できます。
- ユーザーがジョブの完了に必要なアクセス許可のみを持ち、不注意によるエラーの可能性が低くなるように、必ず最小限の特権の原則に従ってください。 詳細については、「[推奨セキュリティ プラクティス](../concepts/recommended-security-practices.md)」を参照してください。
- 必要に応じて後で[委任へのアクセスを削除](../how-to/remove-delegation.md)できるように、[マネージド サービスの登録割り当ての削除ロール](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role)を持つユーザーを含めます。 このロールが割り当てられていない場合、委任されたリソースは顧客のテナント内のユーザーによってのみ削除できます。
- [Azure portal の [マイ カスタマー] ページを表示](../how-to/view-manage-customers.md)する必要があるすべてのユーザーには、必ず[閲覧者](../../role-based-access-control/built-in-roles.md#reader)ロール (または閲覧者アクセスを含む別の組み込みロール) を付与します。

> [!IMPORTANT]
> Azure AD グループのアクセス許可を追加するためには、 **[グループの種類]** を **[セキュリティ]** に設定する必要があります。 このオプションは、グループの作成時に選択します。 詳細については、「[Azure Active Directory を使用して基本グループを作成してメンバーを追加する](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)」を参照してください。

## <a name="role-support-for-azure-lighthouse"></a>Azure Lighthouse 用のロールのサポート

承認を定義する場合、各ユーザー アカウントには、[Azure の組み込みロール](../../role-based-access-control/built-in-roles.md)の 1 つを割り当てる必要があります。 カスタム ロールと[従来のサブスクリプション管理者ロール](../../role-based-access-control/classic-administrators.md)はサポートされていません。

現在、Azure Lighthouse では、以下を除く、すべての[組み込みロール](../../role-based-access-control/built-in-roles.md)がサポートされています。

- [所有者](../../role-based-access-control/built-in-roles.md#owner)ロールはサポートされていません。
- [DataActions](../../role-based-access-control/role-definitions.md#dataactions) 権限を持つすべての組み込みロールはサポートされていません。
- 組み込みの[ユーザー アクセス管理者](../../role-based-access-control/built-in-roles.md#user-access-administrator)ロールは、[顧客のテナント](../how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant)のマネージド ID にロールを割り当てる目的限定でサポートされています。 このロールで通常付与されるその他の権限は適用されません。 ユーザーにこのロールを定義する場合は、このユーザーがマネージド ID に割り当てることができる組み込みロールも指定する必要があります。

> [!NOTE]
> 適用可能な新しい組み込みロールが Azure に追加されると、[Azure Resource Manager テンプレートを使用して顧客のオンボードをする](../how-to/onboard-customer.md)際に割り当てることができます。 [マネージド サービス オファーを発行する](../how-to/publish-managed-services-offers.md)際に、新しく追加したロールがパートナー センターで使用できるようになるまでに時間がかかる場合があります。

## <a name="next-steps"></a>次のステップ

- [Azure Lighthouse で推奨されるセキュリティ プラクティス](recommended-security-practices.md)について学習します。
- [Azure Resource Manager テンプレートを使用する](../how-to/onboard-customer.md)か[プライベートまたはパブリックのマネージド サービスのオファーを Azure Marketplace に公開する](../how-to/publish-managed-services-offers.md)ことで、Azure Lighthouse に顧客をオンボードします。
