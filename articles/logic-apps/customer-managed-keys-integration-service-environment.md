---
title: ISE の保存データを暗号化するためにカスタマー マネージド キーを設定する
description: Azure Logic Apps の統合サービス環境 (ISE) の保存データをセキュリティで保護するために、独自の暗号化キーを作成して管理します。
services: logic-apps
ms.suite: integration
ms.reviewer: mijos, rarayudu, logicappspm
ms.topic: conceptual
ms.date: 01/20/2021
ms.openlocfilehash: d31fbd813f0c5d63ee9eddbff5b299209618626b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98629676"
---
# <a name="set-up-customer-managed-keys-to-encrypt-data-at-rest-for-integration-service-environments-ises-in-azure-logic-apps"></a>Azure Logic Apps の統合サービス環境 (ISE) の保存データを暗号化するためにカスタマー マネージド キーを設定する

Azure Logic Apps は Azure Storage を利用して、データを格納し、自動的に[保存データを暗号化](../storage/common/storage-service-encryption.md)します。 この暗号化によってデータが保護され、組織のセキュリティとコンプライアンスの要件を満たすことができます。 既定では、Azure Storage は Microsoft マネージド キーを使用してデータを暗号化します。 Azure Storage の暗号化の仕組みについて詳しくは、「[保存データに対する Azure Storage 暗号化](../storage/common/storage-service-encryption.md)」と「[Azure Data Encryption-at-Rest](../security/fundamentals/encryption-atrest.md)」を参照してください。

ロジック アプリをホストするための[統合サービス環境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) を作成し、Azure Storage で使用される暗号化キーをより詳細に制御したい場合は、[Azure Key Vault](../key-vault/general/overview.md) を使用して、独自のキーを設定、使用、管理することができます。 この機能は "Bring Your Own Key" (BYOK) と呼ばれ、キーは "カスタマー マネージド キー" と呼ばれます。 この機能を使用すると、Azure Storage で [プラットフォーム マネージド キーを使用した二重暗号化または "*インフラストラクチャの暗号化*"](../security/fundamentals/double-encryption.md) が自動的に有効になります。 詳細については、「[インフラストラクチャの暗号化を使用してデータを二重に暗号化する](../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption)」を参照してください。

このトピックでは、Logic Apps REST API を使って ISE を作成するときに使用する、独自の暗号化キーを設定および指定する方法を示します。 Logic Apps REST API で ISE を作成する一般的な手順については、「[Logic Apps REST API を使用して統合サービス環境 (ISE) を作成する](../logic-apps/create-integration-service-environment-rest-api.md)」を参照してください。

## <a name="considerations"></a>考慮事項

* 現時点では、ISE でのカスタマー マネージド キーのサポートは、次の Azure リージョンでのみご利用いただけます:米国西部 2、米国東部、米国中南部

* カスタマー マネージド キーは、 "*ISE を作成するときにのみ*" 指定でき、その後には指定できません。 ISE を作成した後に、このキーを無効にすることはできません。 現時点では、ISE でのカスタマー マネージド キーのローテーションはサポートされていません。

* カスタマー マネージド キーをサポートするには、ISE で[システム割り当てまたはユーザー割り当てのマネージド ID](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) が有効になっている必要があります。 この ID により、Azure 仮想ネットワーク内にある、または仮想ネットワークに接続されている、セキュリティで保護されたリソース (仮想マシンや他のシステムまたはサービスなど) へのアクセスを ISE で認証できます。 このようにすると、資格情報を使用してサインインする必要はありません。

* 現在、カスタマー マネージド キーをサポートし、いずれかのマネージド ID の種類が有効になっている ISE を作成するには、HTTPS PUT 要求を使用して Logic Apps REST API を呼び出す必要があります。

* [ISE のマネージド ID にキー コンテナーのアクセス権を付与する](#identity-access-to-key-vault)必要がありますが、このタイミングは使用するマネージド ID によって異なります。

  * **システム割り当てマネージド ID**:ISE を作成する HTTPS PUT 要求を送信してから "*30 分*" 以内に、[ISE のマネージド ID にキー コンテナーのアクセス権を付与する](#identity-access-to-key-vault)必要があります。 そうしないと、ISE の作成は失敗し、アクセス許可エラーが発生します。

  * **ユーザー割り当てマネージド ID**:ISE を作成する HTTPS PUT 要求を送信する前に、[ISE のマネージド ID にキー コンテナーのアクセス権を付与する](#identity-access-to-key-vault)必要があります。

## <a name="prerequisites"></a>前提条件

* Azure portal で ISE を作成する場合と同様の、[前提条件](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#prerequisites)および [ISE のアクセスを有効にするための要件](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access)

* **[論理的な削除]** と **[Do Not Purge]\(消去しない\)** プロパティが有効になっている Azure キー コンテナー。

  これらのプロパティの有効化の詳細については、「[Azure Key Vault の論理的な削除の概要](../key-vault/general/soft-delete-overview.md)」と [Azure Key Vault でカスタマー マネージド キーを構成する](../storage/common/customer-managed-keys-configure-key-vault.md)方法に関する記事を参照してください。 [Azure Key Vault](../key-vault/general/overview.md) を初めて使用する場合は、[Azure portal](../key-vault/general/quick-create-portal.md)、[Azure CLI](../key-vault/general/quick-create-cli.md)、または [Azure PowerShell](../key-vault/general/quick-create-powershell.md) を使用して Key Vault を作成する方法を確認してください。

* キー コンテナーで、次のプロパティ値を使用して作成されたキー。

  | プロパティ | 値 |
  |----------|-------|
  | **[キーの種類]** | RSA |
  | **RSA キー サイズ** | 2048 |
  | **有効** | はい |
  |||

  ![カスタマー マネージド暗号化キーの作成](./media/customer-managed-keys-integration-service-environment/create-customer-managed-key-for-encryption.png)

  詳細については、[Azure Key Vault でカスタマー マネージド キーを構成する](../storage/common/customer-managed-keys-configure-key-vault.md)方法に関する記事、または Azure PowerShell コマンドの「[AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey)」を参照してください。

* HTTPS PUT 要求で Logic Apps REST API を呼び出して ISE を作成するために使用できるツール。 たとえば、[Postman](https://www.getpostman.com/downloads/) を使用したり、このタスクを実行するロジック アプリを構築したりできます。

<a name="enable-support-key-managed-identity"></a>

## <a name="create-ise-with-key-vault-and-managed-identity-support"></a>キー コンテナーとマネージド ID のサポートを備えた ISE を作成する

Logic Apps REST API を呼び出して ISE を作成するには、この HTTPS PUT 要求を行います。

`PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

> [!IMPORTANT]
> Logic Apps REST API 2019-05-01 バージョンでは、ISE コネクタに対して独自の HTTPS PUT 要求を行う必要があります。

通常、デプロイは 2 時間以内に完了します。 状況によっては、最大でデプロイに 4 時間かかる場合があります。 デプロイの状態を確認するには、[Azure portal](https://portal.azure.com) の Azure ツール バーで、通知アイコンを選択します。これで通知ペインが開きます。

> [!NOTE]
> デプロイが失敗するか、ISE を削除する場合、サブネットがリリースされるまでに最長 1 時間かかる可能性があります。 この遅延のため、このようなサブネットを他の ISE で再利用できるようになるまで待たなければならない場合があります。
>
> 仮想ネットワークを削除すると、通常、サブネットがリリースされるまでに最長 2 時間かかる可能性があり、この操作にさらに時間がかかる場合があります。 
> 仮想ネットワークを削除する場合は、まだ接続されているリソースがないことを確認してください。 
> 「[仮想ネットワークの削除](../virtual-network/manage-virtual-network.md#delete-a-virtual-network)」を参照してください。

### <a name="request-header"></a>要求ヘッダー

要求ヘッダーに次のプロパティを含めます。

* `Content-type`:このプロパティ値は `application/json` に設定します。

* `Authorization`:このプロパティ値は、使用する Azure サブスクリプションまたはリソース グループにアクセスする顧客のベアラー トークンに設定します。

### <a name="request-body"></a>要求本文

要求本文で、ISE 定義に次の追加項目の情報を入力して、それらのサポートを有効にします。

* ISE がキー コンテナーにアクセスするために使用するマネージド ID
* キー コンテナーと、使用するカスタマー マネージド キー

#### <a name="request-body-syntax"></a>要求本文の構文

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
      "encryptionConfiguration": {
         "encryptionKeyReference": {
            "keyVault": {
               "id": "subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.KeyVault/vaults/{key-vault-name}",
            },
            "keyName": "{customer-managed-key-name}",
            "keyVersion": "{key-version-number}"
         }
      }
   }
}
```

#### <a name="request-body-example"></a>要求本文の例

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
      "encryptionConfiguration": {
         "encryptionKeyReference": {
            "keyVault": {
               "id": "subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.KeyVault/vaults/FabrikamKeyVault",
            },
            "keyName": "Fabrikam-Encryption-Key",
            "keyVersion": "********************"
         }
      }
   }
}
```

<a name="identity-access-to-key-vault"></a>

## <a name="grant-access-to-your-key-vault"></a>キー コンテナーへのアクセス許可を付与する

タイミングは使用するマネージド ID によって異なりますが、[ISE のマネージド ID にキー コンテナーのアクセス権を付与する](#identity-access-to-key-vault)必要があります。

* **システム割り当てマネージド ID**:ISE を作成する HTTPS PUT 要求を送信してから "*30 分*" 以内に、ISE のシステム割り当てマネージド ID 用のキー コンテナーにアクセス ポリシーを追加する必要があります。 そうしないと、ISE の作成に失敗し、アクセス許可エラーが発生します。

* **ユーザー割り当てマネージド ID**:ISE を作成する HTTPS PUT 要求を送信する前に、ISE のユーザー割り当てマネージド ID 用のキー コンテナーにアクセス ポリシーを追加します。

このタスクでは、Azure PowerShell の [AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) コマンドを使用するか、Azure portal で次の手順を実行します。

1. [Azure portal](https://portal.azure.com) で Azure キー コンテナーを開きます。

1. キー コンテナーのメニューで、 **[アクセス ポリシー]**  >  **[アクセス ポリシーの追加]** の順に選択します。次に例を示します。

   ![システム割り当てマネージド ID のアクセス ポリシーを追加する](./media/customer-managed-keys-integration-service-environment/add-ise-access-policy-key-vault.png)

1. **[アクセス ポリシーの追加]** ペインが開いたら、次の手順に従います。

   1. 次のオプションを選択します。

      | 設定 | 値 |
      |---------|--------|
      | **[テンプレートからの構成 (省略可能)] の一覧** | キー管理 |
      | **キーのアクセス許可** | - **キーの管理操作**:Get、List <p><p>- **暗号化操作**:キーの折り返しを解除、キーを折り返す |
      |||

      ![[キーの管理] > [キーのアクセス許可] の選択](./media/customer-managed-keys-integration-service-environment/select-key-permissions.png)

   1. **[プリンシパルの選択]** で、 **[選択されていません]** を選択します。 **[プリンシパル]** ペインが開いたら、検索ボックスで目的の ISE を見つけて選択します。 完了したら、 **[選択]**  >  **[追加]** の順に選択します。

      ![プリンシパルとして使用する ISE の選択](./media/customer-managed-keys-integration-service-environment/select-service-principal-ise.png)

   1. **[アクセス ポリシー]** ペインでの作業が終了したら、 **[保存]** を選択します。

詳細については、[Key Vault で認証を行う方法](../key-vault/general/authentication.md)に関する記事と [Key Vault のアクセス ポリシーの割り当て](../key-vault/general/assign-access-policy-portal.md)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

* [Azure Key Vault](../key-vault/general/overview.md) の詳細を確認する
