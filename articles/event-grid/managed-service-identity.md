---
title: イベント配信、マネージド サービス ID、およびプライベート リンク
description: この記事では、Azure イベント グリッド トピックに対してマネージド サービス ID を有効にする方法を説明します。 これを使用して、サポートされている配信先にイベントを転送します。
ms.topic: how-to
ms.date: 01/28/2021
ms.openlocfilehash: 3e643465db7cc918499ca962c4697cb61cb4b594
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100007773"
---
# <a name="event-delivery-with-a-managed-identity"></a>マネージド ID を使用したイベント配信
この記事では、Azure イベント グリッド カスタム トピックまたはドメインに対して、[マネージド サービス ID](../active-directory/managed-identities-azure-resources/overview.md) を有効にする方法を説明します。 これを使用して、Service Bus のキューとトピック、イベント ハブ、ストレージ アカウントなどの、サポートされている配信先にイベントを転送します。

この記事では、次の手順の詳細を説明します。
1. システム割り当て ID を持つカスタム トピックまたはドメインを作成するか、既存のカスタム トピックまたはドメインを更新して ID を有効にする。 
1. 配信先 (Service Bus キューなど) の適切なロール (Service Bus データ送信者など) に ID を追加する。
1. イベント サブスクリプションを作成する際に ID の使用を有効にして、イベントを配信先に配信する。 

> [!NOTE]
> 現時点では、[プライベート エンドポイント](../private-link/private-endpoint-overview.md)を使用してイベントを配信することはできません。 詳細については、この記事の最後にある「[プライベート エンドポイント](#private-endpoints)」セクションを参照してください。 

## <a name="create-a-custom-topic-or-domain-with-an-identity"></a>ID を持つカスタム トピックまたはドメインを作成する
最初に、システムマネージド ID を持つトピックまたはドメインを作成する方法を説明します。

### <a name="use-the-azure-portal"></a>Azure ポータルの使用
Azure portal でカスタム トピックやドメインを作成する際、それに対してシステム割り当て ID を有効にすることができます。 次の図は、カスタム トピックに対してシステム マネージド ID を有効にする方法を示しています。 基本的には、トピック作成ウィザードの **[詳細設定]** ページで、 **[Enable system assigned identity]\(システム割り当て ID を有効にする\)** オプションを選択します。 このオプションは、ドメイン作成ウィザードの **[詳細設定]** ページにもあります。 

![カスタム トピックを作成する際に ID を有効にする](./media/managed-service-identity/create-topic-identity.png)

### <a name="use-the-azure-cli"></a>Azure CLI の使用
また、Azure CLI を使用して、システム割り当て ID を持つカスタム トピックまたはドメインを作成することもできます。 `--identity` パラメーターを `systemassigned` に設定して、`az eventgrid topic create` コマンドを使用します。 このパラメーターに値を指定しない場合、既定値 `noidentity` が使用されます。 

```azurecli-interactive
# create a custom topic with a system-assigned identity
az eventgrid topic create -g <RESOURCE GROUP NAME> --name <TOPIC NAME> -l <LOCATION>  --identity systemassigned
```

同様に、`az eventgrid domain create` コマンドを使用してシステム マネージド ID を持つドメインを作成することができます。

## <a name="enable-an-identity-for-an-existing-custom-topic-or-domain"></a>既存のカスタム トピックまたはドメインに対して ID を有効にする
前のセクションでは、カスタム トピックまたはドメインを作成する際にシステム マネージド ID を有効にする方法を学びました。 このセクションでは、既存のカスタム トピックまたはドメインに対してシステム マネージド ID を有効にする方法について学びます。 

### <a name="use-the-azure-portal"></a>Azure ポータルの使用
次の手順では、カスタム トピックに対してシステム マネージド ID を有効にする方法を示します。 ドメインに対して ID を有効にする手順と似ています。 

1. [Azure ポータル](https://portal.azure.com)にアクセスします。
2. 上部の検索バーで「**event grid topics (イベント グリッド トピック)** 」を検索します。
3. マネージド ID を有効にする **カスタム トピック** を選択します。 
4. **[ID]** タブに移動します。 
5. スイッチを **オン** にして、ID を有効にします。 
1. ツール バーの **[保存]** を選択して設定を保存します。 

    :::image type="content" source="./media/managed-service-identity/identity-existing-topic.png" alt-text="カスタム トピックの ID ページ"::: 

同様の手順により、イベント グリッド ドメインに対して ID を有効にできます。

### <a name="use-the-azure-cli"></a>Azure CLI の使用
`--identity` に `systemassigned` を設定して `az eventgrid topic update` コマンドを使用し、既存のカスタム トピックに対してシステム割り当て ID を有効にします。 ID を無効にする場合は、値として `noidentity` を指定します。 

```azurecli-interactive
# Update the topic to assign a system-assigned identity. 
az eventgrid topic update -g $rg --name $topicname --identity systemassigned --sku basic 
```

既存のドメインを更新するコマンドも同様です (`az eventgrid domain update`)。

## <a name="supported-destinations-and-azure-roles"></a>サポートされている配信先と Azure ロール
イベント グリッド カスタム トピックまたはドメインに対して ID を有効にすると、Azure によって Azure Active Directory 内で自動的に ID が作成されます。 カスタム トピックまたはドメインによってサポート対象の配信先にイベントを転送できるように、適切な Azure ロールにこの ID を追加します。 たとえば、ある Azure Event Hubs 名前空間の **Azure Event Hubs データ送信者** ロールに ID を追加すると、イベント グリッド カスタム トピックによってその名前空間のイベント ハブにイベントを転送できるようになります。 

現在、Azure イベント グリッドでは、システム割り当てマネージド ID で構成されたカスタム トピックやドメインによって、次の配信先にイベントを転送することをサポートしています。 この表は、カスタム トピックによってイベントを転送できるようにするために、ID を含めるべきロールも示しています。

| 到着地 | Azure ロール | 
| ----------- | --------- | 
| Service Bus のキューとトピック | [Azure Service Bus データ送信者](../service-bus-messaging/authenticate-application.md#azure-built-in-roles-for-azure-service-bus) |
| Azure Event Hubs | [Azure Event Hubs データ送信者](../event-hubs/authorize-access-azure-active-directory.md#azure-built-in-roles-for-azure-event-hubs) | 
| Azure BLOB ストレージ | [ストレージ BLOB データ共同作成者](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues) |
| Azure Queue Storage |[ストレージ キュー データ メッセージ送信者](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues) | 

## <a name="add-an-identity-to-azure-roles-on-destinations"></a>ID を配信先の Azure ロールに追加する
このセクションでは、カスタム トピックまたはドメインの ID を Azure ロールに追加する方法について説明します。 

### <a name="use-the-azure-portal"></a>Azure ポータルの使用
カスタム トピックやドメインによってイベントを配信先に転送できるようにするために、Azure portal を使用して、カスタム トピックやドメインの ID を適切なロールに割り当てることができます。 

次の例では、**msitesttopic** という名称のイベント グリッド カスタム トピックのマネージド ID を、キューまたはトピックのリソースが含まれている Service Bus の名前空間の **Azure Service Bus データ送信者** ロールに追加します。 名前空間レベルでロールに追加すると、イベント グリッド カスタム トピックによりイベントをその名前空間内のすべてのエンティティに転送できるようになります。 

1. [Azure portal](https://portal.azure.com) で、ご利用の **Service Bus 名前空間** に移動します。 
1. 左ペインの **[アクセス制御]** を選択します。 
1. **[ロールの割り当てを追加する]** セクションで **[追加]** を選択します。 
1. **[ロールの割り当ての追加]** ページで、次の手順のようにします。
    1. ロールを選択します。 この例では、**Azure Service Bus データ送信者** です。 
    1. イベント グリッド カスタム トピックまたはドメインの **ID** を選択します。 
    1. **[保存]** を選んで構成を保存します。

表に示されている他のロールへの ID の追加の手順も同様です。 

### <a name="use-the-azure-cli"></a>Azure CLI の使用
このセクションの例では、Azure CLI を使用して ID を Azure ロールに追加する方法を説明します。 サンプル コマンドは、イベント グリッド カスタム トピックのものです。 イベント グリッド ドメインのコマンドも同様です。 

#### <a name="get-the-principal-id-for-the-custom-topics-system-identity"></a>カスタム トピックのシステム ID のプリンシパル ID を取得する 
最初に、カスタム トピックのシステム マネージド ID のプリンシパル ID を取得して、その ID を適切なロールに割り当てます。

```azurecli-interactive
topic_pid=$(az ad sp list --display-name "$<TOPIC NAME>" --query [].objectId -o tsv)
```

#### <a name="create-a-role-assignment-for-event-hubs-at-various-scopes"></a>様々なスコープでイベント ハブへのロールの割り当てを作成する 
次の CLI の例は、カスタム トピックの ID を、名前空間レベルまたはイベント ハブ レベルで **Azure Event Hubs データ送信者** ロールに追加する方法を示しています。 名前空間レベルでロールの割り当てを作成すると、カスタム トピックによりイベントをその名前空間のすべてのイベント ハブに転送できるようになります。 ロールの割り当てをイベント ハブ レベルで作成すると、カスタム トピックによりイベントをその特定のイベント ハブにのみ転送できるようになります。 


```azurecli-interactive
role="Azure Event Hubs Data Sender" 
namespaceresourceid=$(az eventhubs namespace show -n $<EVENT HUBS NAMESPACE NAME> -g <RESOURCE GROUP of EVENT HUB> --query "{I:id}" -o tsv) 
eventhubresourceid=$(az eventhubs eventhub show -n <EVENT HUB NAME> --namespace-name <EVENT HUBS NAMESPACE NAME> -g <RESOURCE GROUP of EVENT HUB> --query "{I:id}" -o tsv) 

# create role assignment for the whole namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$namespaceresourceid" 

# create role assignment scoped to just one event hub inside the namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$eventhubresourceid" 
```

#### <a name="create-a-role-assignment-for-a-service-bus-topic-at-various-scopes"></a>様々なスコープで Service Bus トピックへのロールの割り当てを作成する 
次の CLI の例は、イベント グリッド カスタム トピックの ID を、名前空間レベルまたは Service Bus トピック レベルで **Azure Service Bus データ送信者** ロールに追加する方法を示しています。 名前空間レベルでロールの割り当てを作成すると、イベント グリッド トピックによりイベントをその名前空間内のすべてのエンティティ (Service Bus キューまたはトピック) に転送できるようになります。 ロールの割り当てを Service Bus キューまたはトピックのレベルで作成すると、イベント グリッド カスタム トピックにより、その特定の Service Bus のキューまたはトピックにのみイベントを転送できるようになります。 

```azurecli-interactive
role="Azure Service Bus Data Sender" 
namespaceresourceid=$(az servicebus namespace show -n $RG\SB -g "$RG" --query "{I:id}" -o tsv 
sbustopicresourceid=$(az servicebus topic show -n topic1 --namespace-name $RG\SB -g "$RG" --query "{I:id}" -o tsv) 

# create role assignment for the whole namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$namespaceresourceid" 

# create role assignment scoped to just one hub inside the namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$sbustopicresourceid" 
```

## <a name="create-event-subscriptions-that-use-an-identity"></a>ID を使用するイベント サブスクリプションを作成する
システム マネージド ID を持つイベント グリッド カスタム トピックまたはドメインを作成し、その ID を配信先の適切なロールに追加したら、ID を使用してサブスクリプションを作成することができます。 

### <a name="use-the-azure-portal"></a>Azure ポータルの使用
イベント サブスクリプションを作成する際、 **[エンドポイントの詳細]** セクションにエンドポイントに対するシステム割り当て ID の使用を有効にするオプションが表示されます。 

![Service Bus キューへのイベント サブスクリプションを作成する際に、ID を有効にする](./media/managed-service-identity/service-bus-queue-subscription-identity.png)

また、 **[追加機能]** タブで、配信不能処理でシステム割り当て ID の使用を有効にすることもできます。 

![配信不能処理でシステム割り当て ID を有効にする](./media/managed-service-identity/enable-deadletter-identity.png)

### <a name="use-the-azure-cli---service-bus-queue"></a>Azure CLI の使用 - Service Bus キュー 
このセクションでは、Azure CLI を使用してシステム割り当て ID の使用を有効にし、イベントを Service Bus キューに配信する方法について学びます。 ID は **Azure Service Bus データ送信者** ロールのメンバーである必要があります。 また、配信不能処理に使用されるストレージ アカウントの **ストレージ BLOB データ共同作成者** ロールのメンバーである必要もあります。 

#### <a name="define-variables"></a>変数の定義
最初に、CLI コマンドで使用する次の変数の値を指定します。 

```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID CUSTOM TOPIC>"
topicname = "<EVENT GRID TOPIC NAME>"

# get the service bus queue resource id
queueid=$(az servicebus queue show --namespace-name <SERVICE BUS NAMESPACE NAME> --name <QUEUE NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)
sb_esname = "<Specify a name for the event subscription>" 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery"></a>配信用に、マネージド ID を使用してイベント サブスクリプションを作成する 
このサンプル コマンドでは、エンドポイントの種類を **Service Bus キュー** に設定して、イベント グリッド カスタム トピックのイベント サブスクリプションを作成します。 

```azurecli-interactive
az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname
    --delivery-identity-endpoint-type servicebusqueue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    -n $sb_esname 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery-and-dead-lettering"></a>配信および配信不能処理用に、マネージド ID を使用してイベント サブスクリプションを作成する
このサンプル コマンドでは、エンドポイントの種類を **Service Bus キュー** に設定して、イベント グリッド カスタム トピックのイベント サブスクリプションを作成します。 また、システム マネージド ID を配信不能処理に使用することも指定します。 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type servicebusqueue
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    --deadletter-identity-endpoint $deadletterendpoint 
    --deadletter-identity systemassigned 
    -n $sb_esnameq 
```

### <a name="use-the-azure-cli---event-hubs"></a>Azure CLI の使用 - Event Hubs 
このセクションでは、Azure CLI を使用し、システム割り当て ID の使用を有効にして、イベントをイベント ハブに配信する方法を学びます。 ID は、**Azure Event Hubs データ送信者** ロールのメンバーである必要があります。 また、配信不能処理に使用されるストレージ アカウントの **ストレージ BLOB データ共同作成者** ロールのメンバーである必要もあります。 

#### <a name="define-variables"></a>変数の定義
```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID CUSTOM TOPIC>"
topicname = "<EVENT GRID CUSTOM TOPIC NAME>"

hubid=$(az eventhubs eventhub show --name <EVENT HUB NAME> --namespace-name <NAMESPACE NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)
eh_esname = "<SPECIFY EVENT SUBSCRIPTION NAME>" 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery"></a>配信用に、マネージド ID を使用してイベント サブスクリプションを作成する 
このサンプル コマンドでは、エンドポイントの種類を **Event Hubs** に設定して、イベント グリッド カスタム トピックのイベント サブスクリプションを作成します。 

```azurecli-interactive
az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type eventhub 
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $hubid
    -n $sbq_esname 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery--deadletter"></a>配信用および配信不能にするために、マネージド ID を使用してイベント サブスクリプションを作成する 
このサンプル コマンドでは、エンドポイントの種類を **Event Hubs** に設定して、イベント グリッド カスタム トピックのイベント サブスクリプションを作成します。 また、システム マネージド ID を配信不能処理に使用することも指定します。 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type servicebusqueue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $hubid
    --deadletter-identity-endpoint $eh_deadletterendpoint
    --deadletter-identity systemassigned 
    -n $eh_esname 
```

### <a name="use-the-azure-cli---azure-storage-queue"></a>Azure CLI の使用 - Azure Storage キュー 
このセクションでは、Azure CLI を使用し、システム割り当て ID の使用を有効にして、イベントを Azure Storage キューに配信する方法を学びます。 ID は、ストレージ アカウントの **ストレージ キュー データ メッセージ送信者** ロールのメンバーである必要があります。 また、配信不能処理に使用されるストレージ アカウントの **ストレージ BLOB データ共同作成者** ロールのメンバーである必要もあります。

#### <a name="define-variables"></a>変数の定義  

```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID CUSTOM TOPIC>"
topicname = "<EVENT GRID CUSTOM TOPIC NAME>"

# get the storage account resource id
storageid=$(az storage account show --name <STORAGE ACCOUNT NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)

# build the resource id for the queue
queueid="$storageid/queueservices/default/queues/<QUEUE NAME>" 

sa_esname = "<SPECIFY EVENT SUBSCRIPTION NAME>" 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery"></a>配信用に、マネージド ID を使用してイベント サブスクリプションを作成する 

```azurecli-interactive
az eventgrid event-subscription create 
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type storagequeue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    -n $sa_esname 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery--deadletter"></a>配信用および配信不能にするために、マネージド ID を使用してイベント サブスクリプションを作成する 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type storagequeue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    --deadletter-identity-endpoint $deadletterendpoint 
    --deadletter-identity systemassigned 
    -n $sa_esname 
```

## <a name="private-endpoints"></a>プライベート エンドポイント
現時点では、[プライベート エンドポイント](../private-link/private-endpoint-overview.md)を使用してイベントを配信することはできません。 つまり、配信されたイベント トラフィックがプライベート IP 空間から外に出てはならないという、ネットワークの分離の厳格な要件がある場合はサポートされません。 

ただし、暗号化されたチャネルと、パブリック IP 空間を使用する送信者 (この場合は Event Grid) の既知の ID を使用してイベントを送信する安全な方法の呼び出しが要件で必要とされる場合は、この記事で示したように、システム マネージド ID が構成された Azure イベント グリッド カスタム トピックまたはドメインを使用して、Event Hubs、Service Bus、または Azure Storage サービスにイベントを配信できます。 その後、Azure Functions で構成されたプライベート リンクまたは仮想ネットワークにデプロイされた Webhook を使用して、イベントをプルできます。 次のサンプルを参照してください:「[Azure Functions を使用してプライベート エンドポイントに接続する](/samples/azure-samples/azure-functions-private-endpoints/connect-to-private-endpoints-with-azure-functions/)」。

この構成では、トラフィックはパブリック IP/インターネット経由で Event Grid から Event Hubs、Service Bus、または Azure Storage に送られますが、チャネルを暗号化でき、Event Grid のマネージド ID が使用されます。 Azure Functions、または仮想ネットワークにデプロイされた Webhook を、プライベート リンク経由で Event Hubs、Service Bus、または Azure Storage を使用するように構成すると、トラフィックのそのセクションは確実に Azure 内に留まります。


## <a name="next-steps"></a>次のステップ
マネージド サービス ID の詳細について、「[Azure リソースのマネージド ID とは](../active-directory/managed-identities-azure-resources/overview.md)」を参照してください。 
