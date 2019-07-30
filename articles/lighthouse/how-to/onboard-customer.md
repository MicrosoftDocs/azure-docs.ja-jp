---
title: Azure の委任されたリソース管理に顧客をオンボードする - Azure Lighthouse
description: Azure の委任されたリソース管理に顧客をオンボードする方法について説明します。これにより、自分のテナントからそれらのリソースにアクセスして管理できるようになります。
author: JnHs
ms.author: jenhayes
ms.service: lighthouse
ms.date: 07/11/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: 1885a6220f14de234710b6980b5d3b6a6172bb7e
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67810856"
---
# <a name="onboard-a-customer-to-azure-delegated-resource-management"></a>Azure の委任されたリソース管理に顧客をオンボードする

この記事では、どうすればサービス プロバイダーが Azure の委任されたリソース管理に顧客をオンボードでき、それらの委任されたリソース (サブスクリプションやリソース グループ) に自分の Azure Active Directory (Azure AD) テナントからアクセスして管理できるかについて説明します。 ここではサービス プロバイダーと顧客に言及しますが、複数のテナントを管理している企業では、同じプロセスを使用して自社の管理エクスペリエンスを強化することができます。

このプロセスは、複数の顧客のリソースを管理している場合に繰り返すことができます。 その後、許可されているユーザーが自分のテナントにサインインすると、そのユーザーは、個々の顧客テナントにサインインしなくても管理操作を実行することが顧客のテナント スコープ全体で承認されます。

オンボードされたサブスクリプションに自分の Microsoft Partner Network (MPN) ID を関連付けると、顧客エンゲージメント全体での影響を追跡できます。 詳細については、「[Azure アカウントにパートナー ID をリンクする](https://docs.microsoft.com/azure/billing/billing-partner-admin-link-started)」を参照してください。

> [!NOTE]
> 顧客は、Azure Marketplace に公開されたマネージド サービスのオファー (パブリックまたはプライベート) を購入すると自動的にオンボードされます。 詳細については、「[Azure Marketplace にマネージド サービス オファーを公開する](publish-managed-services-offers.md)」を参照してください。 また、ここで説明されているオンボード プロセスは、Azure Marketplace に公開されているオファーでも使用できます。

オンボード プロセスでは、サービス プロバイダーのテナントと顧客のテナント両方の中からアクションを実行する必要があります。 これらの手順はすべて、この記事で説明します。

> [!IMPORTANT]
> 現在、サブスクリプションで Azure Databricks が使用されている場合、Azure の委任されたリソース管理用にそのサブスクリプション (またはサブスクリプション内のリソース グループ) をオンボードすることはできません。 同様に、サブスクリプションがオンボードのために **Microsoft.ManagedServices** リソースプロバイダーに登録されている場合、この時点ではそのサブスクリプション用に Databricks ワークスペースを作成することはできなくなります。

## <a name="gather-tenant-and-subscription-details"></a>テナントとサブスクリプションの詳細を収集する

顧客のテナントをオンボードするには、アクティブな Azure サブスクリプションが必要です。 次のことを知っている必要があります。

- サービス プロバイダーのテナント (顧客のリソースを管理する場所) のテナント ID
- 顧客のテナント (リソースはサービス プロバイダーで管理されます) のテナント ID
- サービス プロバイダーで管理される (またはサービス プロバイダーで管理されるリソース グループを含む) 顧客のテナントにある特定のサブスクリプションそれぞれのサブスクリプション ID

この情報がまだない場合は、次のいずれかの方法で取得できます。

### <a name="azure-portal"></a>Azure ポータル

実際のテナント ID は、Azure portal の右上にあるアカウント名をポイントするか、 **[ディレクトリの切り替え]** を選択することで確認できます。 テナント ID を選択してコピーするには、ポータル内から "Azure Active Directory" を検索し、 **[プロパティ]** を選択して、 **[ディレクトリ ID]** フィールドに表示されている値をコピーします。 サブスクリプションの ID を見つけるには、"サブスクリプション" を検索し、適切なサブスクリプション ID を選択します。

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Select-AzSubscription <subscriptionId>
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account set --subscription <subscriptionId/name>
az account show
```


## <a name="ensure-the-customers-subscription-is-registered-for-onboarding"></a>顧客のサブスクリプションがオンボード用に登録されていることを確認する

各サブスクリプションには、**Microsoft.ManagedServices** リソースプロバイダーを手動で登録することで、オンボードを承認する必要があります。 顧客は、「[Azure リソースプロバイダーと種類](../../azure-resource-manager/resource-manager-supported-services.md)」で説明されている手順に従って、サブスクリプションを登録できます。

顧客は、次のいずれかの方法で、サブスクリプションをオンボードする準備が完了していることを確認できます。

### <a name="azure-portal"></a>Azure ポータル

1. Azure portal で、サブスクリプションを選択します。
1. **[リソース プロバイダー]** を選択します。
1. **Microsoft.ManagedServices** が **[登録済み]** と表示されることを確認します。

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Set-AzContext -Subscription <subscriptionId>
Get-AzResourceProvider -ProviderNameSpace 'Microsoft.ManagedServices'
```

これにより、次のような結果が返されます。

```output
ProviderNamespace : Microsoft.ManagedServices
RegistrationState : Registered
ResourceTypes     : {registrationDefinitions}
Locations         : {}

ProviderNamespace : Microsoft.ManagedServices
RegistrationState : Registered
ResourceTypes     : {registrationAssignments}
Locations         : {}

ProviderNamespace : Microsoft.ManagedServices
RegistrationState : Registered
ResourceTypes     : {operations}
Locations         : {}
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account set –subscription <subscriptionId>
az provider show –namespace "Microsoft.ManagedServices" –-output table
```

これにより、次のような結果が返されます。

```output
Namespace                  RegistrationState
-------------------------  -------------------
Microsoft.ManagedServices  Registered
```

## <a name="define-roles-and-permissions"></a>ロールとアクセス許可を定義する

サービス プロバイダーの場合、1 人の顧客に複数のオファーを使用することができます。この場合、スコープによって異なるアクセスが必要になります。

管理をより簡単にするには、各ロールに Azure AD のユーザー グループを使用することをお勧めします。これにより、個々のユーザーに直接アクセス許可を割り当てるのではなく、ユーザーをグループに追加または削除することができます。 また、サービス プリンシパルにロールをアサインすることもできます。 ユーザーがジョブの完了に必要なアクセス許可のみを持ち、不注意によるエラーの可能性が低くなるように、必ず最小限の特権の原則に従ってください。 詳細については、「[推奨セキュリティ プラクティス](../concepts/recommended-security-practices.md)」を参照してください。

> [!NOTE]
> ロールの割り当てでは、ロールベースのアクセス制御 (RBAC) の[組み込みロール](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)を使用する必要があります。 現在、組み込みロールはすべて、Azure の委任されたリソース管理でサポートされています。ただし、所有者と [DataActions](https://docs.microsoft.com/azure/role-based-access-control/role-definitions#dataactions) アクセス許可を持つ組み込みロールは除きます。 ユーザーアクセス管理者の組み込みロールは、以下で説明するように、限定的な用途でサポートされています。 また、カスタムロールと[従来のサブスクリプション管理者ロール](https://docs.microsoft.com/azure/role-based-access-control/classic-administrators)はサポートされていません。

承認を定義するために、アクセスを許可する各ユーザー、ユーザー グループ、またはサービス プリンシパルの ID 値を知っておく必要があります。 また、割り当てる各組み込みロールのロール定義 ID も必要になります。 それらがまだない場合は、次のいずれかの方法で取得できます。



### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# To retrieve the objectId for an Azure AD group
(Get-AzADGroup -DisplayName '<yourGroupName>').id

# To retrieve the objectId for an Azure AD user
(Get-AzADUser -UserPrincipalName '<yourUPN>').id

# To retrieve the objectId for an SPN
(Get-AzADApplication -DisplayName '<appDisplayName>').objectId

# To retrieve role definition IDs
(Get-AzRoleDefinition -Name '<roleName>').id
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# To retrieve the objectId for an Azure AD group
az ad group list –-query "[?displayName == '<yourGroupName>'].objectId" –-output tsv

# To retrieve the objectId for an Azure AD user
az ad user show –-upn-or-object-id "<yourUPN>" –-query "objectId" –-output tsv

# To retrieve the objectId for an SPN
az ad sp list –-query "[?displayName == '<spDisplayName>'].objectId" –-output tsv

# To retrieve role definition IDs
az role definition list –-name "<roleName>" | grep name
```

## <a name="create-an-azure-resource-manager-template"></a>Azure Resource Manager テンプレートの作成

顧客をオンボードするには、次の内容を含む [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/) テンプレートを作成する必要があります。

|フィールド  |定義  |
|---------|---------|
|**mspName**     |サービス プロバイダーの名前         |
|**mspOfferDescription**     |オファーの簡単な説明 (例: "Contoso VM 管理オファー")      |
|**managedByTenantId**     |テナント ID         |
|**authorizations**     |**principalId** 値はテナントのユーザー、グループ、または SPN を表し、それぞれに、顧客が承認の目的を理解するのに役立つ **principalIdDisplayName** が指定されているほか、アクセス レベルを指定するための組み込みの **roleDefinitionId** 値がマップされています。         |

顧客のサブスクリプションをオンボードするには、[サンプル リポジトリ](https://github.com/Azure/Azure-Lighthouse-samples/)で提供されている適切な Azure Resource Manager テンプレートと共に、対応するパラメーター ファイルを使用します。これは、実際の構成に合わせて承認を定義するように変更します。 オンボードの対象がサブスクリプション全体、リソース グループ、サブスクリプション内の複数のリソース グループのいずれであるかに応じて、個別のテンプレートが用意されています。 また、この方法でサブスクリプションをオンボードしたい方のために、Azure Marketplace に公開したマネージド サービス オファーを購入した顧客に使用できるテンプレートも用意されています。

|**オンボードの対象**  |**使用する Azure Resource Manager テンプレート**  |**変更するパラメーター ファイル** |
|---------|---------|---------|
|Subscription   |[delegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management/delegatedResourceManagement.json)  |[delegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management/delegatedResourceManagement.parameters.json)    |
|Resource group   |[rgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)  |[rgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)    |
|サブスクリプション内の複数のリソース グループ   |[multipleRgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.json)  |[multipleRgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.parameters.json)    |
|サブスクリプション (Azure Marketplace に公開されたオファーの使用時)   |[marketplaceDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.json)  |[marketplaceDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.parameters.json)    |

> [!IMPORTANT]
> ここで説明しているプロセスでは、オンボードの対象となるサブスクリプションごとに個別のデプロイが必要です。
> 
> また、異なるサブスクリプション内のリソース グループを複数オンボードする場合も個別のデプロイが必要です。 ただし、1 つのサブスクリプション内の複数のリソース グループをオンボードする場合は、1 つのデプロイで実行できます。

次の例では、サブスクリプションをオンボードする際に使用される、変更後の **resourceProjection.parameters.json** ファイルを示しています。 ([rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management) フォルダー内にある) リソース グループのパラメーター ファイルは似ていますが、オンボードの対象となる特定のリソース グループを識別するための **rgName** パラメーターも含まれています。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mspName": {
            "value": "Fabrikam Managed Services - Interstellar"
        },
        "mspOfferDescription": {
            "value": "Fabrikam Managed Services - Interstellar"
        },
        "managedByTenantId": {
            "value": "df4602a3-920c-435f-98c4-49ff031b9ef6"
        },
        "authorizations": {
            "value": [
                {
                    "principalId": "0019bcfb-6d35-48c1-a491-a701cf73b419",
                    "principalIdDisplayName": "Tier 1 Support",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                },
                {
                    "principalId": "0019bcfb-6d35-48c1-a491-a701cf73b419",
                    "principalIdDisplayName": "Tier 1 Support",
                    "roleDefinitionId": "36243c78-bf99-498c-9df9-86d9f8d28608"
                },
                {
                    "principalId": "0afd8497-7bff-4873-a7ff-b19a6b7b332c",
                    "principalIdDisplayName": "Tier 2 Support",
                    "roleDefinitionId": "acdd72a7-3385-48ef-bd42-f606fba81ae7"
                },
                {
                    "principalId": "9fe47fff-5655-4779-b726-2cf02b07c7c7",
                    "principalIdDisplayName": "Service Automation Account",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                },
                {
                    "principalId": "3kl47fff-5655-4779-b726-2cf02b05c7c4",
                    "principalIdDisplayName": "Policy Automation Account",
                    "roleDefinitionId": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
                    "delegatedRoleDefinitionIds": [
                        "b24988ac-6180-42a0-ab88-20f7382dd24c",
                        "92aaf0da-9dab-42b6-94a3-d43ce8d16293"
                    ]
                }
            ]
        }
    }
}
```
上記の例の最後の承認では、ユーザー アクセス管理者ロール (18d7d88d-d35e-4fb5-a5c3-7773c20a72d9) が設定された **principalId** が追加されます。 このロールを割り当てる際は、**delegatedRoleDefinitionIds** プロパティと 1 つ以上の組み込みロールを含める必要があります。 この承認で作成されたユーザーは、これらの組み込みロールをマネージド ID に割り当てることができます。 ユーザー アクセス管理者ロールに通常関連付けられている他のアクセス許可はこのユーザーに適用されないことに注意してください。

## <a name="deploy-the-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートをデプロイする

パラメーター ファイルの更新が完了したら、顧客は、Resource Manager テンプレートをサブスクリプションレベルのデプロイとして顧客のテナントにデプロイする必要があります。 Azure の委任されたリソース管理にオンボードするサブスクリプションごと (または、オンボードするリソース グループを含むサブスクリプションごと) に個別のデプロイが必要です。

> [!IMPORTANT]
> このデプロイは、ゲスト以外のアカウントが、オンボード対象のサブスクリプションで[所有者の組み込みコール](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner)を持っている (またはオンボード対象のリソース グループを含む) 顧客のテナントで実行する必要があります。

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
New-AzDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateFile <pathToTemplateFile> `
                 -TemplateParameterFile <pathToParameterFile> `
                 -Verbose

# Deploy Azure Resource Manager template that is located externally
New-AzDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateUri <templateUri> `
                 -TemplateParameterUri <parameterUri> `
                 -Verbose
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
az deployment create –-name <deploymentName> \
                     --location <AzureRegion> \
                     --template-file <pathToTemplateFile> \
                     --parameters <parameters/parameterFile> \
                     --verbose

# Deploy external Azure Resource Manager template, with local parameter file
az deployment create –-name <deploymentName \
                     –-location <AzureRegion> \
                     --template-uri <templateUri> \
                     --parameters <parameterFile> \
                     --verbose
```

## <a name="confirm-successful-onboarding"></a>オンボードが成功したことを確認する

顧客サブスクリプションが Azure の委任されたリソース管理に正常にオンボードされたら、サービス プロバイダーのテナント内のユーザーは、そのサブスクリプションとそのリソースを表示できるようになります (上記のプロセスを通じて、個人として、または適切なアクセス許可を持つ Azure AD グループのメンバーとして、それに対するアクセスが許可されている場合)。 これを確認するには、次のいずれかの方法でサブスクリプションが表示されることを確認します。  

### <a name="azure-portal"></a>Azure ポータル

サービス プロバイダーのテナントで

1. [[マイ カスタマー] ページ](view-manage-customers.md)に移動します。
2. **[顧客]** を選択します。
3. Resource Manager テンプレートで指定したオファー名の付いたサブスクリプションが表示されることを確認します。

顧客のテナントで

1. [[サービス プロバイダー] ページ](view-manage-service-providers.md)に移動します。
2. **[サービス プロバイダーのオファー]** を選択します。
3. Resource Manager テンプレートで指定したオファー名の付いたサブスクリプションが表示されることを確認します。

> [!NOTE]
> デプロイが完了してから Azure portal に更新が反映されるまで数分かかる場合があります。

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Get-AzContext
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account list
```

## <a name="next-steps"></a>次の手順

- [テナント間の管理エクスペリエンス](../concepts/cross-tenant-management-experience.md)について学習します。
- Azure portal の **[マイ カスタマー]** に移動して、[顧客を表示および管理](view-manage-customers.md)します。
