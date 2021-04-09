---
title: Private Link を使用してプライベート アクセスを有効にする (プレビュー) - CLI
titleSuffix: Azure Digital Twins
description: Private Link を使用した Azure Digital Twins ソリューションのプライベート アクセスを、Azure CLI を使用して有効にする方法について説明します。
author: baanders
ms.author: baanders
ms.date: 02/09/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 5bd7ffda508980a9a56d86037887fc53a0fed640
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102202945"
---
# <a name="enable-private-access-with-private-link-preview-azure-cli"></a>Private Link を使用してプライベート アクセスを有効にする (プレビュー):Azure CLI

[!INCLUDE [digital-twins-private-link-selector.md](../../includes/digital-twins-private-link-selector.md)]

この記事では、[Azure Digital Twins インスタンス用のプライベート エンドポイントを使用して Private Link を有効にする](concepts-security.md#private-network-access-with-azure-private-link-preview)さまざまな方法について説明します (現在プレビュー段階)。 Azure Digital Twins インスタンス用のプライベート エンドポイントを構成することで、Azure Digital Twins インスタンスをセキュリティで保護して公開されないようにすることに加え、[Azure Virtual Network (VNet)](../virtual-network/virtual-networks-overview.md) からのデータ流出を回避できます。

この記事では、[**Azure CLI**](/cli/azure/what-is-azure-cli) を使用したプロセスについて説明します。

この記事では、次の手順を説明します。 
1. Private Link を有効にし、Azure Digital Twins インスタンス用のプライベート エンドポイントを構成します。
1. パブリック ネットワーク アクセス フラグを無効または有効にして、Private Link 接続のみに API アクセスを制限します。

## <a name="prerequisites"></a>前提条件

プライベート エンドポイントをセットアップする前に、エンドポイントをデプロイできる [**Azure Virtual Network (VNet)**](../virtual-network/virtual-networks-overview.md) が必要です。 VNet がまだない場合は、Azure Virtual Network のいずれかの[クイックスタート](../virtual-network/quick-create-portal.md)に従って、これを設定できます。

## <a name="manage-private-endpoints-for-an-azure-digital-twins-instance"></a>Azure Digital Twins インスタンス用のプライベート エンドポイントを管理する 

[Azure CLI](/cli/azure/what-is-azure-cli) を使用する場合、既に存在する Azure Digital Twins インスタンスで Private Link を使用したプライベート エンドポイントを設定できます (インスタンス作成の一部として追加することはできません)。 その後、追加の CLI コマンドを使用して、それらを引き続き表示および管理できます。 

>[!TIP]
> また、Azure Digital Twins インスタンスを使用するのではなく、Private Link サービスを使用して Private Link エンドポイントを設定することもできます。 その場合も、構成オプションと最終的な結果は同じです。
>
> Private Link リソースの設定の詳細については、[Azure portal](../private-link/create-private-endpoint-portal.md)、[Azure CLI](../private-link/create-private-endpoint-cli.md)、[ARM テンプレート](../private-link/create-private-endpoint-template.md)、または [PowerShell](../private-link/create-private-endpoint-powershell.md) の Private Link に関するドキュメントを参照してください。

### <a name="add-a-private-endpoint-to-an-existing-instance"></a>既存のインスタンスにプライベート エンドポイントを追加する

プライベート エンドポイントを作成し、それを Azure Digital Twins インスタンスにリンクするには、[**az network private-endpoint create**](/cli/azure/network/private-endpoint#az_network_private_endpoint_create) コマンドを使用します。 `--private-connection-resource-id` パラメーターで、 Azure Digital Twins インスタンスを、その完全修飾 ID を使用して識別します。

次の例では、必要なパラメーターのみ指定したコマンドを使用して、プライベート エンドポイントを作成しています。

```azurecli-interactive
az network private-endpoint create --connection-name {private_link_service_connection} -n {name_for_private_endpoint} -g {resource_group} --subnet {subnet_ID} --private-connection-resource-id "/subscriptions/{subscription_ID}/resourceGroups/{resource_group}/providers/Microsoft.DigitalTwins/digitalTwinsInstances/{Azure_Digital_Twins_instance_name}" 
```

必須と省略可能な各パラメーターの完全な一覧、およびプライベート エンドポイント作成の追加の例については、[**az network private-endpoint create** のリファレンス ドキュメント](/cli/azure/network/private-endpoint#az_network_private_endpoint_create)を参照してください。

### <a name="manage-private-endpoint-connections-on-the-instance"></a>インスタンスのプライベート エンドポイント接続を管理する

Azure Digital Twins インスタンスに対してプライベート エンドポイントを作成した後、[**az dt network private-endpoint connection**](/cli/azure/ext/azure-iot/dt/network/private-endpoint/connection) コマンドを使用して、インスタンスに対するプライベート エンドポイント **接続** を引き続き管理できます。 操作には以下が含まれます。
* プライベート エンドポイント接続を表示する
* プライベート エンドポイント接続の状態を設定する
* プライベート エンドポイント接続を削除する
* インスタンスのすべてのプライベート エンドポイント接続を一覧表示する

詳細と例については、[**az dt network private-endpoint** のリファレンス ドキュメント](/cli/azure/ext/azure-iot/dt/network/private-endpoint)を参照してください。

### <a name="manage-other-private-link-information-on-an-azure-digital-twins-instance"></a>Azure Digital Twins インスタンスの他の Private Link 情報を管理する

[**az dt network private-link**](/cli/azure/ext/azure-iot/dt/network/private-link) コマンドを使用して、インスタンスの Private Link の状態に関する追加情報を取得できます。 操作には以下が含まれます。
* Azure Digital Twins インスタンスに関連付けられている Private Link を一覧表示する
* インスタンスに関連付けられている Private Link を表示する

詳細と例については、[**az dt network private-link** のリファレンス ドキュメント](/cli/azure/ext/azure-iot/dt/network/private-link)を参照してください。

## <a name="disable--enable-public-network-access-flags"></a>パブリック ネットワーク アクセス フラグを無効または有効にする

Azure Digital Twins インスタンスの構成によって、すべてのパブリック接続を拒否し、プライベート エンドポイント経由の接続のみを許可するようにして、ネットワークのセキュリティを強化できます。 この操作は、**パブリック ネットワーク アクセス フラグ** で行います。 

このポリシーを使用すると、API アクセスを Private Link 接続のみに制限することができます。 パブリック ネットワーク アクセス フラグが "*無効*" に設定されていると、パブリック クラウドから Azure Digital Twins インスタンスのデータ プレーンへのすべての REST API 呼び出しで、`403, Unauthorized` が返されます。 または、ポリシーが "*無効*" に設定されていて、要求がプライベート エンドポイントを通して行われた場合は、API 呼び出しは成功します。

この記事では、[Azure CLI](/cli/azure/) または [ARMClient コマンド ツール](https://github.com/projectkudu/ARMClient)を使用して、ネットワーク フラグの値を更新する方法について説明します。 Azure portal を使用してこれを行う方法については、この記事の「[ポータルのバージョン](how-to-enable-private-link-portal.md)」を参照してください。

### <a name="use-the-azure-cli"></a>Azure CLI の使用

Azure CLI で、`--public-network-access` パラメーターを `az dt create` コマンドに追加することで、パブリック ネットワーク アクセスを無効または有効にできます。 このコマンドは、新しいインスタンスを作成する場合にも使用できますが、既存のインスタンスの名前を指定することで、既存のインスタンスのプロパティを編集する場合にも使用できます。 (このコマンドの詳細については、その[リファレンス ドキュメント](/cli/azure/ext/azure-iot/dt#ext_azure_iot_az_dt_create)または [Azure Digital Twins インスタンスを設定するための一般的な手順](how-to-set-up-instance-cli.md#create-the-azure-digital-twins-instance)に関する記事を参照してください)。

Azure Digital Twins インスタンスのパブリック ネットワーク アクセスを **無効にする** には、このような `--public-network-access` パラメーターを使用します。

```azurecli-interactive
az dt create -n {name_of_existing_instance} -g {resource_group} --public-network-access Disabled
```

パブリック ネットワーク アクセスが現在無効になっているインスタンスでそれを **有効にする** には、次のようなコマンドを使用します。

```azurecli-interactive
az dt create -n {name_of_existing_instance} -g {resource_group} --public-network-access Enabled
```

### <a name="usethe-armclientcommand-tool"></a>ARMClient コマンド ツールを使用する 

[ARMClient コマンド ツール](https://github.com/projectkudu/ARMClient)では、次のコマンドを使用してパブリック ネットワーク アクセスを有効または無効にします。 

パブリック ネットワーク アクセスを **無効にする** には:
  
```cmd/sh
armclient login 

armclient PATCH /subscriptions/<your-Azure-subscription-ID>/resourceGroups/<your-resource-group>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<your-Azure-Digital-Twins-instance>?api-version=2020-12-01 "{ 'properties': { 'publicNetworkAccess': 'disabled' } }"  
```

パブリック ネットワーク アクセスを **有効にする** には:  
  
```cmd/sh
armclient login 

armclient PATCH /subscriptions/<your-Azure-subscription-ID>/resourceGroups/<your-resource-group>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<your-Azure-Digital-Twins-instance>?api-version=2020-12-01 "{ 'properties': { 'publicNetworkAccess': 'enabled' } }"  
``` 

## <a name="next-steps"></a>次のステップ

Azure 用 Private Link の詳細を確認します。 
* [*Azure Private Link サービスとは*](../private-link/private-link-service-overview.md)