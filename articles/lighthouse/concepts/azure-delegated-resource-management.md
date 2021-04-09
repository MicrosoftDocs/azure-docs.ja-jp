---
title: Azure の委任されたリソース管理
description: Azure の委任されたリソース管理は Azure Lighthouse の重要な部分であり、サービス プロバイダーは機敏かつ正確に、委任されたリソースを大規模に管理できます。
ms.date: 10/19/2020
ms.topic: conceptual
ms.openlocfilehash: d484e61fc4ab3714eb362b26d64d449890065888
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92203859"
---
# <a name="azure-delegated-resource-management"></a>Azure の委任されたリソース管理

Azure の委任されたリソース管理は、[Azure Lighthouse](../overview.md) の主要なコンポーネントの 1 つです。 Azure の委任されたリソース管理を使用すると、サービス プロバイダーは顧客のエンゲージメントとオンボードのエクスペリエンスを簡略化しながら、機敏かつ正確に委任されたリソースを大規模に管理できます。 お客様は、自社のテナントにアクセスできるサービス プロバイダーの制御を維持できます。お客様は、自社のテナントにアクセスできるユーザー、そのユーザーがアクセスできるリソース、および実行可能な操作の制御を維持できます。

## <a name="what-is-azure-delegated-resource-management"></a>Azure の委任されたリソース管理とは

Azure の委任されたリソース管理を使用すると、あるテナントから別のテナントにリソースを論理的に投影できます。 これにより、1 つの Azure Active Directory (Azure AD) テナントの承認されたユーザーは、顧客に属するさまざまな Azure AD テナントで管理操作を実行できるようになります。 サービス プロバイダーは、独自の Azure AD テナントにサインインし、委任された顧客のサブスクリプションとリソース グループに対して承認を行うことができます。 これにより、個々の顧客テナントにサインインしなくても、顧客に代わって管理操作を実行できます。

> [!TIP]
> Azure の委任されたリソース管理は、[独自の Azure AD テナントを複数所有する企業内](enterprise.md)で、テナント間の管理を簡略化するためにも使用できます。

Azure の委任されたリソース管理を使用すると、承認されたユーザーは、顧客のテナントにアカウントを持っていない場合や、顧客のテナントの共同所有者でない場合でも、顧客のサブスクリプションのコンテキストで直接作業できます。

[テナント間の管理エクスペリエンス](cross-tenant-management-experience.md)により、Azure Policy、Azure Security Center などの Azure 管理サービスで、より効率的に作業を行うことができます。 サービス プロバイダーのすべてのアクティビティがアクティビティ ログに追跡され、顧客のテナントに格納されます (管理テナントのユーザーが表示できます)。 これは、管理側と管理対象のテナントの両方のユーザーが、変更に関連するユーザーを簡単に識別できることを意味します。

[新しいマネージド サービス オファーの種類を Azure Marketplace に発行する](../how-to/publish-managed-services-offers.md)ことで、顧客を Azure Lighthouse に簡単にオンボードできます。 または、[Azure Resource Manager テンプレートをデプロイして、オンボード プロセスを完了する](../how-to/onboard-customer.md)こともできます。

## <a name="how-azure-delegated-resource-management-works"></a>Azure の委任されたリソース管理の動作のしくみ

大まかに言えば、Azure の委任されたリソース管理のしくみは次のとおりです。

1. まず、顧客の Azure リソースを管理するためにグループ、サービス プリンシパル、またはユーザーが必要とするアクセス権 (ロール) を特定します。 アクセスの定義には、管理テナント ID とともに、[組み込みの **roleDefinition** の値](../../role-based-access-control/built-in-roles.md) (共同作成者、VM 共同作成者、閲覧者など) にマップされるテナントの **principalId** ID が含まれています。
2. このアクセスを指定し、次の 2 つの方法のいずれかで Azure Lighthouse に顧客をオンボードします。
   - 顧客が受け入れる [Azure Marketplace の管理されたサービス オファーを発行する](../how-to/publish-managed-services-offers.md) (プライベートまたはパブリック)
   - 1 つ以上の特定のサブスクリプションまたはリソース グループについて、[顧客のテナントに Azure Resource Manager テンプレートをデプロイする](../how-to/onboard-customer.md)

3. 顧客がオンボードされたら、許可されているユーザーは、定義されたアクセス権に基づいて、特定の顧客のスコープで、管理テナントにサインインしてタスクを実行できます。 お客様は、サービス プロバイダーのアクションを確認し、必要に応じてアクセス権を削除することができます。

> [!NOTE]
> 別の[リージョン](../../availability-zones/az-overview.md#regions)にある委任されたリソースを管理することができます。 ただし、[各国のクラウド](../../active-directory/develop/authentication-national-cloud.md)と Azure パブリック クラウドにわたって行われる、または 2 つの独立した国内クラウドにわたって行われるサブスクリプションの委任はサポートされていません。

## <a name="support-for-azure-delegated-resource-management"></a>Azure の委任されたリソース管理のサポート

Azure の委任されたリソース管理に関するヘルプが必要な場合は、Azure portal でサポート リクエストを開くことができます。 **[問題の種類]** では、 **[技術]** を選択します。 サブスクリプションを選択し、 **[Lighthouse]** ( **[管理と監視]** の下) を選択します。

## <a name="next-steps"></a>次のステップ

- [テナント間の管理エクスペリエンス](cross-tenant-management-experience.md)について学習します。
- [Azure Marketplace でのマネージド サービス オファー](managed-services-offers.md)について学習します。
