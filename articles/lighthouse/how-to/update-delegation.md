---
title: 委任を更新する
description: Azure Lighthouse に以前オンボードされた顧客の委任を更新する方法について説明します。
ms.date: 02/16/2021
ms.topic: how-to
ms.openlocfilehash: f0ed5222cdbac3d0e4d193941c2a6f233d15938c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100555770"
---
# <a name="update-a-delegation"></a>委任を更新する

サブスクリプション (またはリソース グループ) を Azure Lighthouse にオンボードした後、変更が必要になる場合があります。 たとえば、顧客が別の Azure 組み込みロールを必要とする追加の管理タスクを引き受けるようにお客様に求める場合や、顧客のサブスクリプションの委任先のテナントを変更することが必要になる場合があります。

> [!TIP]
> このトピックではサービス プロバイダーと顧客に言及しますが、[複数のテナントを管理している企業](../concepts/enterprise.md)では、同じプロセスを使用して Azure Lighthouse を設定し、自社の管理エクスペリエンスを強化することができます。

[Azure Resource Manager テンプレート (ARM テンプレート) を使用して顧客をオンボード](onboard-customer.md)した場合、その顧客に対して新しいデプロイを実行する必要があります。 変更内容に応じて、元のオファーを更新するか、元のオファーを削除して新しいものを作成することをお勧めします。

- **認可のみ変更する場合**:ARM テンプレートの **認可** セクションのみを変更することで、委任を更新できます。
- **管理テナントを変更する場合**:前のオファーとは異なる **mspOfferName** を使用して、新しい ARM テンプレートを作成する必要があります。

## <a name="update-your-arm-template"></a>ARM テンプレートを更新する

委任を更新するには、必要な変更を含む ARM テンプレートをデプロイする必要があります。

認可を更新するだけの場合 (以前含めなかったロールを持つ新しいユーザー グループを追加する場合や、既存のユーザーのロールを変更する場合など)、前の委任で使用した [ARM テンプレート](onboard-customer.md#create-an-azure-resource-manager-template)と同じ **mspOfferName** を使用できます。 前のテンプレートを出発点として使用できます。 次に、必要な変更を行います。Azure の組み込みロールを別のものに置き換えたり、完全に新しい認可をテンプレートに追加したりするなどです。

**mspOfferName** を変更すると、これは新しい別のオファーと見なされます。 これは、管理テナントを変更する場合に必要です。

管理テナントが同じままの場合、**mspOfferName** を変更する必要はありません。 ほとんどの場合、同じ顧客と管理テナントによって使用される **mspOfferName** は 1 つだけにすることをお勧めします。 いずれにしてもそれを変更する場合は、新しい委任をデプロイする前に、顧客の前の委任が削除されていることを確認してください。

## <a name="remove-the-previous-delegation"></a>前の委任を削除する

新しいデプロイを実行する前に、[前の委任へのアクセスを削除する](remove-delegation.md)ことをお勧めします。 これにより、前のすべてのアクセス許可が削除され、今後適用する必要のある正確なユーザーまたはグループとロールで、クリーンな状態で開始できます。

> [!IMPORTANT]
> 新しい **mspOfferName** を使用し、同じ **principalId** 値を保持する場合は、新しいオファーをデプロイする前に、前の委任へのアクセスを削除する必要があります。 最初にオファーを削除しないと、前にアクセス許可を付与されたユーザーは、割り当てが競合するため、アクセスを完全に失う可能性があります。

管理テナントを変更するときに、両方のテナントが引き続きアクセスを保持するようにしたい場合は、前のオファーをそのままにしておくことができます。 新しい管理テナントのみがアクセスを保持するようにしたい場合は、前のオファーを削除する必要があります。 これは、新しいオファーをオンボードする前または後に行うことができます。

オファーを更新して認可のみ調整し、同じ **mspOfferName** を保持する場合は、前の委任を削除する必要はありません。 新しいデプロイによって、前の委任が置き換えられ、最新のテンプレートの認可のみが適用されます。

:::image type="content" source="../media/update-delegation.jpg" alt-text="mspOfferName を変更し、前の委任を削除するタイミングを示す図。":::

委任へのアクセスの削除は、元の委任で[マネージド サービスの登録割り当て削除ロール](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role)を付与された管理テナントのすべてのユーザーが実行できます。 管理テナントにこのロールを所有しているユーザーがいない場合は、[Azure portal でオファーへのアクセスを削除する](view-manage-service-providers.md#add-or-remove-service-provider-offers)ことを顧客に依頼できます。

> [!TIP]
> 上記の手順に従って前の委任を削除しても、新しい ARM テンプレートをデプロイできない場合は、[登録定義を完全に削除する](/powershell/module/az.managedservices/remove-azmanagedservicesdefinition)必要がある場合があります。 これは、`Microsoft.Authorization/roleAssignments/write` のアクセス許可を持つ[所有者](../../role-based-access-control/built-in-roles.md#owner)などのロールが割り当てられている、顧客テナント内のすべてのユーザーが実行できます。  

## <a name="deploy-the-arm-template"></a>ARM テンプレートをデプロイする

顧客は、Azure portal、PowerShell、または Azure CLI を使用して、前と同じ方法で[更新されたテンプレートをデプロイ](onboard-customer.md#deploy-the-azure-resource-manager-templates)できます。

デプロイが完了した後、[それが成功したことを確認](onboard-customer.md#confirm-successful-onboarding)します。 更新された認可が、顧客が委任したサブスクリプションまたはリソース グループに対して有効になります。

## <a name="updating-managed-service-offers"></a>マネージド サービス オファーを更新する

Azure Marketplace に発行されたマネージド サービス オファーを通じて顧客をオンボードし、認可を更新する場合は、その顧客のプランで更新された使用する[認可](../../marketplace/plan-managed-service-offer.md)と共に、[オファーの新しいバージョンを発行する](../../marketplace/partner-center-portal/update-existing-offer.md)ことで、委任を更新できます。 その後、顧客は Azure portal で最新バージョンに更新できます。

管理テナントを変更する場合は、顧客が受け入れられるように、[新しいマネージド サービス オファーを作成して発行する](../../marketplace/plan-managed-service-offer.md)必要があります。

> [!TIP]
> 前述のように、同じ顧客と管理テナントの間で、複数の異なるオファーを使用しないことをお勧めします。 同じ管理テナントを使用する同じ顧客に対して新しいオファーを発行する場合は、顧客が新しいオファーを受け入れる前に、前のオファーを必ず削除してください。

## <a name="next-steps"></a>次のステップ

- Azure portal の **[マイ カスタマー]** に移動して、[顧客を表示および管理](view-manage-customers.md)します。
- 以前にオンボードした[委任へのアクセスを削除する](remove-delegation.md)方法について学習します。
