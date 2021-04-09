---
title: IdentitySelector UI 要素
description: Azure portal の Microsoft.ManagedIdentity.IdentitySelector UI 要素について説明します。 リソースにマネージド ID を割り当てるために使用します。
author: tfitzmac
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tomfitz
ms.openlocfilehash: 91e3cd0b99825fd72eb342ce7a8555b046455538
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87063364"
---
# <a name="microsoftmanagedidentityidentityselector-ui-element"></a>Microsoft.ManagedIdentity.IdentitySelector UI 要素

デプロイ内のリソースに対して[マネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) を割り当てるためのコントロールです。

## <a name="ui-sample"></a>UI サンプル

このコントロールは次の要素で構成されます。

![Microsoft.ManagedIdentity.IdentitySelector の 1 番目のステップ](./media/managed-application-elements/microsoft-managedidentity-identityselector-1.png)

ユーザーが **[追加]** 選択すると、次のフォームが開きます。 ユーザーは、リソースに対して 1 つまたは複数のユーザー割り当て ID を選択できます。

![Microsoft.ManagedIdentity.IdentitySelector の 2 番目のステップ](./media/managed-application-elements/microsoft-managedidentity-identityselector-2.png)

選択した ID がテーブルに表示されます。 ユーザーは、このテーブルの項目を追加または削除できます。

![Microsoft.ManagedIdentity.IdentitySelector の 3 番目のステップ](./media/managed-application-elements/microsoft-managedidentity-identityselector-3.png)

## <a name="schema"></a>スキーマ

```json
{
  "name": "identity",
  "type": "Microsoft.ManagedIdentity.IdentitySelector",
  "label": "Managed Identity Configuration",
  "toolTip": {
    "systemAssignedIdentity": "Enable system assigned identity to grant the resource access to other existing resources.",
    "userAssignedIdentity": "Add user assigned identities to grant the resource access to other existing resources."
  },
  "defaultValue": {
    "systemAssignedIdentity": "Off"
  },
  "options": {
    "hideSystemAssignedIdentity": false,
    "hideUserAssignedIdentity": false
  },
  "visible": true
}
```

## <a name="sample-output"></a>サンプル出力

```json
{
  "identity": {
    "value": {
      "type": "UserAssigned",
      "userAssignedIdentities": {
        "/subscriptions/xxxx/resourceGroups/TestResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/TestUserIdentity1": {}
      }
    }
  }
}
```

## <a name="remarks"></a>解説

- システム割り当て ID オプション コントロールに初期値を設定するには、**defaultValue.systemAssignedIdentity** を使用します。 既定値は **Off** です。 次の値を使用できます。
  - **On** – システム割り当ての ID が、リソースに割り当てられます。
  - **Off** – システム割り当ての ID は、リソースに割り当てられません。
  - **OnOnly** – システム割り当ての ID が、リソースに割り当てられます。 ユーザーは、デプロイ中にこの値を編集できません。
  - **OffOnly** – システム割り当ての ID は、リソースに割り当てられません。 ユーザーは、デプロイ中にこの値を編集できません。

- **options.hideSystemAssignedIdentity** が **true** に設定されている場合は、システム割り当て ID を構成するための UI は表示されません。 このオプションの既定値は **false** です。
- **options.hideUserAssignedIdentity** が **true** に設定されている場合は、ユーザー割り当て ID を構成するための UI は表示されません。 リソースには、ユーザー割り当て ID は割り当てられません。 このオプションの既定値は **false** です。

## <a name="next-steps"></a>次のステップ

- UI 定義の作成の概要については、「[CreateUiDefinition の基本概念](create-uidefinition-overview.md)」を参照してください。
- UI 要素の共通プロパティの説明については、「[CreateUiDefinition の要素](create-uidefinition-elements.md)」を参照してください。