---
title: Azure Lighthouse への顧客のオンボード
description: Azure Lighthouse に顧客をオンボードする方法について説明します。これにより、Azure の委任されたリソース管理を使用して自分のテナントからそれらのリソースにアクセスして管理できるようになります。
ms.date: 08/20/2020
ms.topic: how-to
ms.openlocfilehash: db6a819c72f1ef46f542ed47cad6caae23c0d191
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2020
ms.locfileid: "88719055"
---
# <a name="onboard-a-customer-to-azure-lighthouse"></a>Azure Lighthouse への顧客のオンボード

この記事では、サービス プロバイダーが顧客を Azure Lighthouse にオンボードする方法について説明します。 これを行うとき、[Azure の委任されたリソース管理](../concepts/azure-delegated-resource-management.md)を使用すると、顧客の委任されたリソース (サブスクリプションやリソース グループ) に自分の Azure Active Directory (Azure AD) テナントからアクセスして管理できます。

> [!TIP]
> このトピックではサービス プロバイダーと顧客に言及しますが、[複数のテナントを管理している企業](../concepts/enterprise.md)では、同じプロセスを使用して Azure Lighthouse を設定し、自社の管理エクスペリエンスを強化することができます。

複数の顧客に対して、オンボード プロセスを繰り返すことができます。 適切なアクセス許可を持つユーザーが、管理しているテナントにサインインすると、そのユーザーは、個々の顧客テナントにサインインしなくても管理操作を実行することが顧客のテナント スコープ全体で承認されます。

顧客エンゲージメント全体におけるご自身の影響を追跡して評価を受けるには、オンボードされた各サブスクリプションにアクセスできる少なくとも 1 つのユーザー アカウントに Microsoft Partner Network (MPN) ID を関連付けます。 サービス プロバイダー テナントでこの関連付けを実行する必要があります。 簡略化するために、MPN ID に関連付けられているテナントでサービス プリンシパル アカウントを作成し、オンボードするすべての顧客に対する閲覧者アクセス権をこのアカウントに付与することをお勧めします。 詳しくは、「[Azure アカウントにパートナー ID をリンクする](../../cost-management-billing/manage/link-partner-id.md)」をご覧ください。

> [!NOTE]
> また、[Azure Marketplace に公開](publish-managed-services-offers.md)したマネージド サービス オファー (パブリックまたはプライベート) を顧客が購入したときに、顧客を Azure Lighthouse にオンボードすることもできます。 また、ここで説明されているオンボード プロセスは、Azure Marketplace に公開されているオファーと共に使用できます。

オンボード プロセスでは、サービス プロバイダーのテナントと顧客のテナント両方の中からアクションを実行する必要があります。 これらの手順はすべて、この記事で説明します。

## <a name="gather-tenant-and-subscription-details"></a>テナントとサブスクリプションの詳細を収集する

顧客のテナントをオンボードするには、アクティブな Azure サブスクリプションが必要です。 次のことを知っている必要があります。

- サービス プロバイダーのテナント (顧客のリソースを管理する場所) のテナント ID
- 顧客のテナント (リソースはサービス プロバイダーで管理されます) のテナント ID
- サービス プロバイダーで管理される (またはサービス プロバイダーで管理されるリソース グループを含む) 顧客のテナントにある特定のサブスクリプションそれぞれのサブスクリプション ID

> [!NOTE]
> サブスクリプション内の 1 つ以上のリソース グループのみをオンボードする場合でも、サブスクリプション レベルでデプロイを行う必要があるため、サブスクリプション ID が必要になります。

こうした ID 値がまだない場合は、次のいずれかの方法で取得できます。 デプロイではこれらの正確な値を必ず使用してください。

### <a name="azure-portal"></a>Azure portal

実際のテナント ID は、Azure portal の右上にあるアカウント名をポイントするか、 **[ディレクトリの切り替え]** を選択することで確認できます。 テナント ID を選択してコピーするには、ポータル内から "Azure Active Directory" を検索し、 **[プロパティ]** を選択して、 **[ディレクトリ ID]** フィールドに表示されている値をコピーします。 顧客テナントでのサブスクリプションの ID を見つけるには、"サブスクリプション" を検索して、適切なサブスクリプション ID を選択します。

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

> [!NOTE]
> ここで説明するプロセスを使用してサブスクリプション (またはサブスクリプション内の 1 つまたは複数のリソース グループ) をオンボードすると、そのサブスクリプションに対して **Microsoft.ManagedServices** リソースプロバイダーが登録されます。

## <a name="define-roles-and-permissions"></a>ロールとアクセス許可を定義する

サービス プロバイダーは、1 人の顧客に対して複数のタスクを実行でき、その場合は、スコープごとに異なるアクセスが必要になります。 適切な[ロールベースのアクセス制御 (RBAC) の組み込みロール](../../role-based-access-control/built-in-roles.md)をテナント内のユーザーに割り当てるために、必要な数の承認を定義することができます。

管理しやすくするため、ロールごとに Azure AD ユーザー グループを使用することをお勧めします。 これにより、アクセス権を持つグループに個々のユーザーを柔軟に追加または削除できるようになるため、ユーザー変更を行うためにオンボード プロセスを繰り返す必要がなくなります。 サービス プリンシパルにロールを割り当てることができます。これは、自動化のシナリオで役立ちます。

承認を定義する場合は、ユーザーがジョブの完了に必要なアクセス許可のみを持つように、必ず最小限の特権の原則に従ってください。 サポートされているロールに関するガイドラインと情報については、「[Azure Lighthouse のシナリオにおけるテナント、ロール、ユーザー](../concepts/tenants-users-roles.md)」を参照してください。

> [!IMPORTANT]
> Azure AD グループのアクセス許可を追加するためには、 **[グループの種類]** を **[Office 365]** ではなく、 **[セキュリティ]** にする必要があります。 このオプションは、グループの作成時に選択します。 詳細については、「[Azure Active Directory を使用して基本グループを作成してメンバーを追加する](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)」を参照してください。

承認を定義するために、アクセスを許可するサービス プロバイダー テナントの各ユーザー、各ユーザー グループ、または各サービス プリンシパルの ID 値を知っておく必要があります。 また、割り当てる各組み込みロールのロール定義 ID も必要になります。 それらをまだ持っていない場合は、サービス プロバイダー テナント内から次のコマンドを実行して取得することができます。

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# To retrieve the objectId for an Azure AD group
(Get-AzADGroup -DisplayName '<yourGroupName>').id

# To retrieve the objectId for an Azure AD user
(Get-AzADUser -UserPrincipalName '<yourUPN>').id

# To retrieve the objectId for an SPN
(Get-AzADApplication -DisplayName '<appDisplayName>' | Get-AzADServicePrincipal).Id

# To retrieve role definition IDs
(Get-AzRoleDefinition -Name '<roleName>').id
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# To retrieve the objectId for an Azure AD group
az ad group list --query "[?displayName == '<yourGroupName>'].objectId" --output tsv

# To retrieve the objectId for an Azure AD user
az ad user show --id "<yourUPN>" --query "objectId" --output tsv

# To retrieve the objectId for an SPN
az ad sp list --query "[?displayName == '<spDisplayName>'].objectId" --output tsv

# To retrieve role definition IDs
az role definition list --name "<roleName>" | grep name
```

> [!TIP]
> テナント内のユーザーが必要に応じて後から[委任へのアクセスを削除](remove-delegation.md)できるように、顧客をオンボードするときに、[マネージド サービスの登録割り当ての削除ロール](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role)を割り当てることをお勧めします。 このロールが割り当てられていない場合、委任されたリソースは顧客のテナント内のユーザーによってのみ削除できます。

## <a name="create-an-azure-resource-manager-template"></a>Azure Resource Manager テンプレートの作成

顧客をオンボードするには、プラン用に次の情報を含む [Azure Resource Manager](../../azure-resource-manager/index.yml) テンプレートを作成する必要があります。 **mspOfferName** および **mspOfferDescription** の値は、Azure portal の [[サービス プロバイダー] ページ](view-manage-service-providers.md)で顧客に表示されます。

|フィールド  |定義  |
|---------|---------|
|**mspOfferName**     |この定義を説明する名前。 この値は、オファーのタイトルとして顧客に表示され、一意の値である必要があります。        |
|**mspOfferDescription**     |自分のオファーの簡単な説明 (例: "Contoso VM 管理オファー")。      |
|**managedByTenantId**     |テナント ID。          |
|**authorizations**     |**principalId** 値はテナントのユーザー、グループ、または SPN を表し、それぞれに、顧客が承認の目的を理解するのに役立つ **principalIdDisplayName** が指定されているほか、アクセス レベルを指定するための組み込みの **roleDefinitionId** 値がマップされています。      |

オンボード プロセスには、Azure Resource Manager テンプレート ([サンプル リポジトリ](https://github.com/Azure/Azure-Lighthouse-samples/)に提供されています) と、お使いの構成に合わせるための変更と承認の定義を行う、対応するパラメーター ファイルが必要です。

選択するテンプレートは、オンボードの対象がサブスクリプション全体、リソース グループ、サブスクリプション内の複数のリソース グループのいずれであるかによって異なります。 また、この方法でサブスクリプションをオンボードしたい方のために、Azure Marketplace に公開したマネージド サービス オファーを購入した顧客に使用できるテンプレートも用意されています。

|オンボードの対象  |使用する Azure Resource Manager テンプレート  |変更するパラメーター ファイル |
|---------|---------|---------|
|サブスクリプション   |[delegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management/delegatedResourceManagement.json)  |[delegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management/delegatedResourceManagement.parameters.json)    |
|Resource group   |[rgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)  |[rgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)    |
|サブスクリプション内の複数のリソース グループ   |[multipleRgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.json)  |[multipleRgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.parameters.json)    |
|サブスクリプション (Azure Marketplace に公開されたオファーの使用時)   |[marketplaceDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.json)  |[marketplaceDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.parameters.json)    |

> [!IMPORTANT]
> ここで説明するプロセスでは、同じ顧客テナントにサブスクリプションをオンボードしている場合でも、オンボードするサブスクリプションごとに個別のデプロイが必要です。 また、同じ顧客テナントで異なるサブスクリプション内の複数のリソース グループをオンボードする場合も個別のデプロイが必要です。 ただし、1 つのサブスクリプション内の複数のリソース グループをオンボードする場合は、1 つのデプロイで実行できます。
>
> また、同じサブスクリプション (またはサブスクリプション内のリソース グループ) に適用される複数のプランに対しても個別のデプロイが必要です。 適用される各プランに、それぞれ異なる **mspOfferName** を使用する必要があります。

次の例では、変更した **delegatedResourceManagement.parameters.json** ファイルを示します。これを使用すれば、サブスクリプションをオンボードすることができます。 ([rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/rg-delegated-resource-management) フォルダー内にある) リソース グループのパラメーター ファイルは似ていますが、オンボードの対象となる特定のリソース グループを識別するための **rgName** パラメーターも含まれています。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mspOfferName": {
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

上記の例の最後の承認では、ユーザー アクセス管理者ロール (18d7d88d-d35e-4fb5-a5c3-7773c20a72d9) が設定された **principalId** が追加されます。 このロールを割り当てる際は、**delegatedRoleDefinitionIds** プロパティと 1 つ以上の組み込みロールを含める必要があります。 この承認で作成されたユーザーは、これらの組み込みのロールを顧客テナント内の[マネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) に割り当てることができます。これは、[修復可能なポリシーをデプロイする](deploy-policy-remediation.md)ためには必要なことです。  ユーザーは、サポート インシデントを作成することもできます。  ユーザー アクセス管理者ロールに通常関連付けられている他のアクセス許可は、このユーザーに適用されません。

## <a name="deploy-the-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートをデプロイする

パラメーター ファイルを更新したら、顧客のテナント内のユーザーは、Azure Resource Manager テンプレートを顧客のテナントにデプロイする必要があります。 オンボードするサブスクリプションごと (または、オンボードするリソース グループを含むサブスクリプションごと) に個別のデプロイが必要です。

> [!IMPORTANT]
> このデプロイは、ゲスト以外のアカウントが、オンボード対象のサブスクリプションで[所有者の組み込みロール](../../role-based-access-control/built-in-roles.md#owner)を持っている (またはオンボード対象のリソース グループを含む) 顧客のテナントで実行する必要があります。 サブスクリプションを委任できるすべてのユーザーを表示するには、顧客のテナントのユーザーが Azure portal でサブスクリプションを選択し、 **[アクセス制御 (IAM)]** を開くと、[所有者ロールを持つすべてのユーザーを表示](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription)することができます。 
>
> サブスクリプションが[クラウド ソリューション プロバイダー (CSP) プログラム](../concepts/cloud-solution-provider.md)を使用して作成されている場合、サービス プロバイダー テナントの[管理エージェント](/partner-center/permissions-overview#manage-commercial-transactions-in-partner-center-azure-ad-and-csp-roles) ロールを持つユーザーがデプロイを実行できます。

デプロイは、次に示すように PowerShell または Azure CLI を使用して、Azure portal で行うことができます。

### <a name="azure-portal"></a>Azure portal

1. [GitHub リポジトリ](https://github.com/Azure/Azure-Lighthouse-samples/)で、使用するテンプレートの横に表示される **[Deploy to Azure]\(Azure へのデプロイ\)** ボタンを選択します。 Azure portal でテンプレートが開きます。
1. **[Msp Offer Name]\(Msp オファー名\)** **[Msp Offer Description]\(Msp オファーの説明\)** 、 **[Managed by Tenant Id]\(テナント ID による管理\)** 、および **[Authorizations]\(承認\)** の値を入力します。 必要に応じて、 **[パラメーターの編集]** を選択してパラメーター ファイルに `mspOfferName`、`mspOfferDescription`、`managedbyTenantId`、および `authorizations` の値を直接入力できます。 テンプレートの既定値を使用するのではなく、必ず、これらの値を更新してください。
1. **[確認と作成]** を選択し、次に **[作成]** を選択します。

数分後に、デプロイが完了したという通知が表示されます。

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
New-AzSubscriptionDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateFile <pathToTemplateFile> `
                 -TemplateParameterFile <pathToParameterFile> `
                 -Verbose

# Deploy Azure Resource Manager template that is located externally
New-AzSubscriptionDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateUri <templateUri> `
                 -TemplateParameterUri <parameterUri> `
                 -Verbose
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
az deployment create --name <deploymentName> \
                     --location <AzureRegion> \
                     --template-file <pathToTemplateFile> \
                     --parameters <parameters/parameterFile> \
                     --verbose

# Deploy external Azure Resource Manager template, with local parameter file
az deployment create --name <deploymentName> \
                     --location <AzureRegion> \
                     --template-uri <templateUri> \
                     --parameters <parameterFile> \
                     --verbose
```

## <a name="confirm-successful-onboarding"></a>オンボードが成功したことを確認する

顧客サブスクリプションが Azure Lighthouse に正常にオンボードされたら、サービス プロバイダーのテナント内のユーザーは、サブスクリプションとそのリソースを表示できるようになります (上記のプロセスを通じて、個人として、または適切なアクセス許可を持つ Azure AD グループのメンバーとして、それに対するアクセスが許可されている場合)。 これを確認するには、次のいずれかの方法でサブスクリプションが表示されることを確認します。  

### <a name="azure-portal"></a>Azure portal

サービス プロバイダーのテナントで

1. [[マイ カスタマー] ページ](view-manage-customers.md)に移動します。
2. **[顧客]** を選択します。
3. Resource Manager テンプレートで指定したオファー名の付いたサブスクリプションが表示されることを確認します。

> [!IMPORTANT]
> [[マイ カスタマー]](view-manage-customers.md) にある委任されたサブスクリプションを表示するためには、サブスクリプションがオンボードされたときに、サービス プロバイダーのテナント内のユーザーに[閲覧者](../../role-based-access-control/built-in-roles.md#reader)ロール (または、閲覧者アクセスを含む別の組み込みロール) が付与されている必要があります。

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

## <a name="next-steps"></a>次のステップ

- [テナント間の管理エクスペリエンス](../concepts/cross-tenant-management-experience.md)について学習します。
- Azure portal の **[マイ カスタマー]** に移動して、[顧客を表示および管理](view-manage-customers.md)します。
- 以前にオンボードした[委任へのアクセスを削除する](remove-delegation.md)方法について学習します。
