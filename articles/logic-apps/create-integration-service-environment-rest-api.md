---
title: Logic Apps REST API を使用して統合サービス環境 (ISE) を作成する
description: Azure Logic Apps から Azure 仮想ネットワーク (VNET) にアクセスできるように、Logic Apps REST API を使用して統合サービス環境 (ISE) を作成します。
services: logic-apps
ms.suite: integration
ms.reviewer: rarayudu, logicappspm
ms.topic: conceptual
ms.date: 02/03/2021
ms.openlocfilehash: d4500229800fa5d1743779b29927637777647e47
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99550659"
---
# <a name="create-an-integration-service-environment-ise-by-using-the-logic-apps-rest-api"></a>Logic Apps REST API を使用して統合サービス環境 (ISE) を作成する

ロジック アプリと統合アカウントで [Azure 仮想ネットワーク](../virtual-network/virtual-networks-overview.md)にアクセスする必要があるシナリオでは、Logic Apps REST API を使用して "[*統合サービス環境*" (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) を作成できます。 ISE の詳細については、[Azure Logic Apps から Azure Virtual Network リソースへのアクセス](connect-virtual-network-vnet-isolated-environment-overview.md)に関する記事を参照してください。

この記事では、Logic Apps REST API を使用して ISE を作成する方法全般について説明します。 必要に応じて、[システム割り当てまたはユーザー割り当てのマネージド ID](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) を ISE で有効にすることもできますが、この時点では Logic Apps REST API を使用する方法でのみ可能です。 この ID により、Azure 仮想ネットワーク内にある、または仮想ネットワークに接続されている、セキュリティで保護されたリソース (仮想マシンや他のシステムまたはサービスなど) へのアクセスを ISE で認証できます。 このようにすると、資格情報を使用してサインインする必要はありません。

ISE を作成する他の方法の詳細については、次の記事を参照してください。

* [Azure portal を使用して ISE を作成する](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* [サンプルの Azure Resource Manager クイックスタート テンプレートを使用して ISE を作成する](https://github.com/Azure/azure-quickstart-templates/tree/master/201-integration-service-environment)
* [保存データを暗号化するためのカスタマー マネージド キーの使用をサポートする ISE を作成する](customer-managed-keys-integration-service-environment.md)

## <a name="prerequisites"></a>前提条件

* Azure portal で ISE を作成する場合と同様の[前提条件](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#prerequisites)および[アクセス要件](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access)

* ISE で使用することで、その情報を ISE 定義に含めることができるその他の追加リソース。たとえば次のものがあります。 

  * 自己署名証明書のサポートを有効にするには、その証明書に関する情報を ISE 定義に含める必要があります。

  * ユーザー割り当てのマネージド ID を有効にするには、事前にその ID を作成し、`objectId`、`principalId`、`clientId` の各プロパティとそれらの値を ISE 定義に含める必要があります。 詳細については、[Azure portal でユーザー割り当てマネージド ID を作成する](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)方法に関する記事を参照してください。

* HTTPS PUT 要求で Logic Apps REST API を呼び出して ISE を作成するために使用できるツール。 たとえば、[Postman](https://www.getpostman.com/downloads/) を使用したり、このタスクを実行するロジック アプリを構築したりできます。

## <a name="create-the-ise"></a>ISE を作成する

Logic Apps REST API を呼び出して ISE を作成するには、この HTTPS PUT 要求を行います。

`PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

> [!IMPORTANT]
> Logic Apps REST API 2019-05-01 バージョンでは、ISE コネクタに対して独自の HTTP PUT 要求を行う必要があります。

通常、デプロイは 2 時間以内に完了します。 状況によっては、最大でデプロイに 4 時間かかる場合があります。 デプロイの状態を確認するには、[Azure portal](https://portal.azure.com) の Azure ツール バーで、通知アイコンを選択します。これで通知ペインが開きます。

> [!NOTE]
> デプロイが失敗するか、ISE を削除する場合、サブネットがリリースされるまでに最長 1 時間かかる可能性があります。 この遅延のため、このようなサブネットを他の ISE で再利用できるようになるまで待たなければならない場合があります。
>
> 仮想ネットワークを削除すると、通常、サブネットがリリースされるまでに最長 2 時間かかる可能性があり、この操作にさらに時間がかかる場合があります。 
> 仮想ネットワークを削除する場合は、まだ接続されているリソースがないことを確認してください。 
> 「[仮想ネットワークの削除](../virtual-network/manage-virtual-network.md#delete-a-virtual-network)」を参照してください。

## <a name="request-header"></a>要求ヘッダー

要求ヘッダーに次のプロパティを含めます。

* `Content-type`:このプロパティ値は `application/json` に設定します。

* `Authorization`:このプロパティ値は、使用する Azure サブスクリプションまたはリソース グループにアクセスする顧客のベアラー トークンに設定します。

<a name="request-body"></a>

## <a name="request-body"></a>要求本文

要求本文には、ISE で有効にする追加機能の情報など、ISE の作成に使用するリソース定義を指定します。たとえば、次のようにします。

* `TrustedRoot` の場所にインストールされている、自己署名証明書とエンタープライズ証明機関によって発行された証明書の使用を許可する ISE を作成するには、この記事で後述するように、ISE 定義の `properties` セクションに `certificates` オブジェクトを含めます。

* システム割り当てまたはユーザー割り当てのマネージド ID を使用する ISE を作成するには、この記事で後述するように、マネージド ID の種類およびその他の必要な情報を含む `identity` オブジェクトを ISE 定義に含めます。

* 保存データを暗号化するためにカスタマー マネージド キーおよび Azure Key Vault を使用する ISE を作成するには、[カスタマー マネージド キー サポートを有効にする情報](customer-managed-keys-integration-service-environment.md)を含めます。 カスタマー マネージド キーを設定できるのは *作成時のみ* であり、その後は不可能です。

### <a name="request-body-syntax"></a>要求本文の構文

ISE の作成時に使用するプロパティを記述する要求本文の構文を次に示します。

```json
{
   "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Logic/integrationServiceEnvironments/{ISE-name}",
   "name": "{ISE-name}",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "{Azure-region}",
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   // Include the `identity` object to enable the system-assigned identity or user-assigned identity
   "identity": {
      "type": <"SystemAssigned" | "UserAssigned">,
      // When type is "UserAssigned", include the following "userAssignedIdentities" object:
      "userAssignedIdentities": {
         "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{user-assigned-managed-identity-object-ID}": {
            "principalId": "{principal-ID}",
            "clientId": "{client-ID}"
         }
      }
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-1}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-2}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-3}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-4}",
            }
         ]
      },
      // Include `certificates` object to enable self-signed certificate and the certificate issued by Enterprise Certificate Authority
      "certificates": {
         "testCertificate": {
            "publicCertificate": "{base64-encoded-certificate}",
            "kind": "TrustedRoot"
         }
      }
   }
}
```

### <a name="request-body-example"></a>要求本文の例

この例の要求本文では、サンプルの値を示しています。

```json
{
   "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Logic/integrationServiceEnvironments/Fabrikam-ISE",
   "name": "Fabrikam-ISE",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "WestUS2",
   "identity": {
      "type": "UserAssigned",
      "userAssignedIdentities": {
         "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/*********************************": {
            "principalId": "*********************************",
            "clientId": "*********************************"
         }
      }
   },
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-1",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-2",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-3",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-4",
            }
         ]
      },
      "certificates": {
         "testCertificate": {
            "publicCertificate": "LS0tLS1CRUdJTiBDRV...",
            "kind": "TrustedRoot"
         }
      }
   }
}
```

## <a name="add-custom-root-certificates"></a>カスタム ルート証明書の追加

ISE を、仮想ネットワーク上またはオンプレミスのカスタム サービスに接続するために使用することがよくあります。 多くの場合、これらのカスタム サービスは、エンタープライズ証明機関などのカスタムのルート証明機関によって発行された証明書、または自己署名証明書によって保護されます。 自己署名証明書の使用の詳細については、[アクセスとデータのセキュリティ保護 - 他のサービスやシステムへの発信呼び出しのアクセス](../logic-apps/logic-apps-securing-a-logic-app.md#secure-outbound-requests)に関するページを参照してください。 ISE がトランスポート層セキュリティ (TLS) を使用してこれらのサービスに正常に接続するためには、ISE がこれらのルート証明書にアクセスできる必要があります。

#### <a name="considerations-for-adding-custom-root-certificates"></a>カスタム ルート証明書の追加に関する考慮事項

カスタムの信頼されたルート証明書を使用して ISE を更新する前に、次の考慮事項を確認してください。

* 必ず、ルート証明書 *および* すべての中間証明書をアップロードしてください。 証明書の最大数 は 20 です。

* ルート証明書のアップロードは、最新のアップロードによって前のアップロードが上書きされる置換操作です。 たとえば、ある証明書をアップロードする要求を送信した後に、別の証明書をアップロードする別の要求を送信した場合、2 番目の証明書のみが ISE によって使用されます。 両方の証明書を使用する必要がある場合は、同じ要求内に一緒に追加します。  

* ルート証明書のアップロードは、時間がかかる可能性がある非同期操作です。 状態または結果を確認するには、同じ URI を使用して `GET` 要求を送信します。 応答メッセージには、アップロード操作がまだ実行中の場合に `InProgress` 値を返す `provisioningState` フィールドがあります。 `provisioningState` 値が `Succeeded` の場合、アップロード操作は完了しています。

#### <a name="request-syntax"></a>要求の構文

カスタムの信頼されたルート証明書を使用して ISE を更新するには、次の HTTPS PATCH 要求を [Azure Resource Manager の URL (お使いの Azure 環境によって異なる)](../azure-resource-manager/management/control-plane-and-data-plane.md#control-plane) に送信します。次に例を示します。

| 環境 | Azure Resource Manager URL |
|-------------|----------------------------|
| Azure グローバル (マルチテナント) | `PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01` |
| Azure Government | `PATCH https://management.usgovcloudapi.net/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01` |
| Microsoft Azure China 21Vianet | `PATCH https://management.chinacloudapi.cn/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01` |
|||

#### <a name="request-body-syntax-for-adding-custom-root-certificates"></a>カスタム ルート証明書を追加するための要求本文の構文

ルート証明書を追加するときに使用するプロパティを記述する要求本文の構文を次に示します。

```json
{
   "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Logic/integrationServiceEnvironments/{ISE-name}",
   "name": "{ISE-name}",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "{Azure-region}",
   "properties": {
      "certificates": {
         "testCertificate1": {
            "publicCertificate": "{base64-encoded-certificate}",
            "kind": "TrustedRoot"
         },
         "testCertificate2": {
            "publicCertificate": "{base64-encoded-certificate}",
            "kind": "TrustedRoot"
         }
      }
   }
}
```

## <a name="next-steps"></a>次のステップ

* [統合サービス環境にリソースを追加する](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [統合サービス環境を管理する](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)
