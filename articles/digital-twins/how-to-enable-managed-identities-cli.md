---
title: イベントをルーティングするためにマネージド ID を有効にする (プレビュー) - CLI
titleSuffix: Azure Digital Twins
description: Azure CLI を使用して、Azure Digital Twins のシステム割り当て ID を有効にし、それを使用してイベントを転送する方法を説明します。
author: baanders
ms.author: baanders
ms.date: 02/09/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: c9ce87584373bd87a8f89ecb4ea692b44d3fab4d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102202962"
---
# <a name="enable-a-managed-identity-for-routing-azure-digital-twins-events-preview-azure-cli"></a>Azure Digital Twins のイベントをルーティングするためにマネージド ID を有効にする (プレビュー)Azure CLI

[!INCLUDE [digital-twins-managed-service-identity-selector.md](../../includes/digital-twins-managed-service-identity-selector.md)]

この記事では、[Azure Digital Twins インスタンスのシステム割り当て ID](concepts-security.md#managed-identity-for-accessing-other-resources-preview) (現時点ではプレビュー段階) を有効にし、[Event Hubs](../event-hubs/event-hubs-about.md)、[Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) 、[Azure Storage コンテナー](../storage/blobs/storage-blobs-introduction.md)などのサポートされている送信先にイベントを転送するときに、その ID を使用します。

この記事では、[**Azure CLI**](/cli/azure/what-is-azure-cli) を使用したプロセスについて説明します。

この記事では、次の手順を説明します。 

1. システム割り当て ID を使用して Azure Digital Twins インスタンスを作成するか、既存の Azure Digital Twins インスタンスでシステム割り当て ID を有効にします。 
1. ID に適切なロール (1 つまたは複数) を追加します。 たとえば、エンドポイントが Event Hubs の場合は ID に **Azure Event Hubs データ送信者** ロールを割り当て、エンドポイントが Service Bus の場合は **Azure Service Bus データ送信者ロール** を割り当てます。
1. 認証にシステム割り当て ID を使用できるエンドポイントを Azure Digital Twins に作成します。

## <a name="enable-system-managed-identities-for-an-instance"></a>インスタンスでシステム マネージド ID を有効にする 

Azure Digital Twins インスタンスでシステム割り当て ID を有効にすると、Azure によって [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) にその ID が自動的に作成されます。 その後、その ID を使用して、イベント転送のために Azure Digital Twins エンドポイントに対する認証を行うことができます。

**インスタンスの初期セットアップの一部として** Azure Digital Twins インスタンスのシステム マネージド ID を有効にすることも、**既に存在するインスタンスで後から有効にする** こともできます。

どちらの作成方法でも、インスタンスに提供される構成オプションと結果は同じです。 このセクションでは、両方を行う方法について説明します。

### <a name="add-a-system-managed-identity-during-instance-creation"></a>インスタンスの作成時にシステム マネージド ID を追加する

このセクションでは、現在作成中の Azure Digital Twins インスタンスで、システム マネージド ID を有効にする方法について説明します。 

これを行うには、インスタンスの作成に使用する `az dt create` コマンドに `--assign-identity` パラメーターを追加します。 (このコマンドの詳細については、その[リファレンス ドキュメント](/cli/azure/ext/azure-iot/dt#ext_azure_iot_az_dt_create)または [Azure Digital Twins インスタンスを設定するための一般的な手順](how-to-set-up-instance-cli.md#create-the-azure-digital-twins-instance)に関する記事を参照してください)。

システム マネージド ID を持つインスタンスを作成するには、このように `--assign-identity` パラメーターを追加します。

```azurecli-interactive
az dt create -n {new_instance_name} -g {resource_group} --assign-identity
```

### <a name="add-a-system-managed-identity-to-an-existing-instance"></a>既存のインスタンスにシステム マネージド ID を追加する

このセクションでは、既に存在する Azure Digital Twins インスタンスにシステム マネージド ID を追加します。

これも `az dt create` コマンドと `--assign-identity` パラメーターを使用して実行します。 作成するインスタンスの新しい名前を指定する代わりに、既に存在するインスタンスの名前を指定して、そのインスタンスの `--assign-identity` の値を更新できます。

マネージド ID を **有効にする** コマンドは、システム マネージド ID を持つインスタンスを作成するコマンドと同じです。 変更されるのは、インスタンス名パラメーターの値だけです。

```azurecli-interactive
az dt create -n {name_of_existing_instance} -g {resource_group} --assign-identity
```

マネージド ID が現在有効になっているインスタンスでそれを **無効にする** には、次のようなコマンドを使用して `--assign-identity` を `false` に設定します。

```azurecli-interactive
az dt create -n {name_of_existing_instance} -g {resource_group} --assign-identity false
```

## <a name="assign-azure-roles-to-the-identity"></a>Azure ロールを ID に割り当てる 

Azure Digital Twins インスタンス用のシステム割り当て ID が作成されたら、サポートされている送信先にイベントを転送するためにさまざまな種類の[エンドポイント](concepts-route-events.md)で認証を行うため、それに適切なロールを割り当てる必要があります。 このセクションでは、ロールのオプションと、それらをシステム割り当て ID に割り当てる方法について説明します。

>[!NOTE]
> これは重要なステップです。行わないと、ID はエンドポイントにアクセスできず、イベントは配信されません。

### <a name="supported-destinations-and-azure-roles"></a>サポートされている配信先と Azure ロール 

送信先の種類に応じて、ID がエンドポイントにアクセスするために必要な最小限のロールを次に示します。 さらに高いアクセス許可を持つロール (データ所有者ロールなどの) も機能します。

| 到着地 | Azure ロール |
| --- | --- |
| Azure Event Hubs | Azure Event Hubs データ送信者 |
| Azure Service Bus | Azure Service Bus データ送信者 |
| [Azure ストレージ コンテナー] | ストレージ BLOB データ共同作成者 |

Azure Digital Twins でのルーティングでサポートされているエンドポイント、ルート、送信先の種類の詳細については、[*イベント ルートの概念*](concepts-route-events.md)に関するページを参照してください。

### <a name="assign-the-role"></a>ロールを割り当てる

[!INCLUDE [digital-twins-permissions-required.md](../../includes/digital-twins-permissions-required.md)]

指定したロールを持つ 1 つ以上のスコープに ID を割り当てるために、`--scopes` パラメーターを `az dt create` コマンドに追加できます。 これは、最初にインスタンスを作成するときに使用することも、既に存在するインスタンスの名前を渡すことで後で使用することもできます。

次の例では、システム マネージド ID を持つインスタンスを作成し、その ID に Event Hubs の `MyCustomRole` という名前のカスタム ロールを割り当てています。

```azurecli-interactive
az dt create -n {instance_name} -g {resource_group} --assign-identity --scopes "/subscriptions/<subscription ID>/resourceGroups/<resource_group>/providers/Microsoft.EventHub/namespaces/<Event_Hubs_namespace>/eventhubs/<event_hub_name>" --role MyCustomRole
```

このコマンドを使用したロールの割り当てのその他の例については、[**az dt create** のリファレンス ドキュメント](/cli/azure/ext/azure-iot/dt#ext_azure_iot_az_dt_create)を参照してください。

また、ロールを作成および管理するために、[**az role assignment**](/cli/azure/role/assignment) コマンド グループも使用できます。 これは、create コマンドでロールの割り当てをグループ化したくない場合の追加のシナリオをサポートするために使用できます。

## <a name="create-an-endpoint-with-identity-based-authentication"></a>ID ベースの認証を使用してエンドポイントを作成する

Azure Digital Twins インスタンスのシステム マネージド ID を設定し、それに適切なロールを割り当てた後は、認証にその ID を使用できる Azure Digital Twins [エンドポイント](how-to-manage-routes-portal.md#create-an-endpoint-for-azure-digital-twins)を作成できます。 このオプションは、種類が Event Hubs と Service Bus のエンドポイントでのみ使用できます (Event Grid ではサポートされていません)。

>[!NOTE]
> キー ベースの ID で既に作成されているエンドポイントを編集して、ID ベースの認証に変更することはできません。 最初にエンドポイントを作成するときに、認証の種類を選択する必要があります。

これを行うには、エンドポイントの作成に使用する `az dt endpoint create` コマンドに `--auth-type` パラメーターを追加します。 (このコマンドの詳細については、その[リファレンス ドキュメント](/cli/azure/ext/azure-iot/dt/endpoint/create)または [Azure Digital Twins エンドポイントを設定するための一般的な手順](how-to-manage-routes-apis-cli.md#create-the-endpoint)に関する記事を参照してください)。

ID ベースの認証を使用するエンドポイントを作成するには、`--auth-type` パラメーターを使用して `IdentityBased` 認証の種類を指定します。 下の例は、Event Hubs エンドポイントについて、これを示しています。

```azurecli-interactive
az dt endpoint create eventhub --endpoint-name {endpoint_name} --eventhub-resource-group {eventhub_resource_group} --eventhub-namespace {eventhub_namespace} --eventhub {eventhub_name} --auth-type IdentityBased -n {instance_name}
```

## <a name="considerations-for-disabling-system-managed-identities"></a>システム マネージド ID を無効にする場合の考慮事項

ID は、それを使用するエンドポイントとは別に管理されているため、ID またはそのロールを変更したときの Azure Digital Twins インスタンスのエンドポイントに対する影響について考慮することが重要です。 ID を無効にした場合、またはエンドポイントに必要なロールを削除した場合、エンドポイントにアクセスできなくなり、イベントのフローが中断される可能性があります。

設定されていたマネージド ID を無効にした後もエンドポイントを引き続き使用するには、エンドポイントを削除し、異なる認証の種類で[再作成する](how-to-manage-routes-apis-cli.md#create-an-endpoint-for-azure-digital-twins)必要があります。 この変更を行った後、エンドポイントへのイベントの配信が再開されるまで、最大で 1 時間かかることがあります。

## <a name="next-steps"></a>次のステップ

Azure AD でのマネージド ID の詳細について確認します。 
* [*Azure リソースのマネージド ID*](../active-directory/managed-identities-azure-resources/overview.md)