---
title: Azure Monitor で Azure AD B2C を監視する
titleSuffix: Azure AD B2C
description: 委任されたリソース管理を使用して、Azure Monitor で Azure AD B2C のイベントをログに記録する方法について説明します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.author: mimart
ms.subservice: B2C
ms.date: 02/10/2020
ms.openlocfilehash: 3106e5a640ed66828558078e6986979ad7195450
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85386217"
---
# <a name="monitor-azure-ad-b2c-with-azure-monitor"></a>Azure Monitor で Azure AD B2C を監視する

Azure Monitor を使用して、Azure Active Directory B2C (Azure AD B2C) のサインインと[監査](view-audit-logs.md)ログをさまざまな監視ソリューションにルーティングします。 そのログを、長期的な使用のために保持したり、サードパーティのセキュリティ情報およびイベント管理 (SIEM) ツールと統合して環境の分析情報を取得したりすることができます。

ログ イベントは次の場所にルーティングできます。

* Azure [ストレージ アカウント](../storage/blobs/storage-blobs-introduction.md)。
* [Log Analytics ワークスペース](../azure-monitor/platform/resource-logs-collect-workspace.md) (データの分析、ダッシュボードの作成、特定のイベントに対するアラートの作成を行う)。
* Azure [イベント ハブ](../event-hubs/event-hubs-about.md) (Splunk および Sumo Logic のインスタンスと統合できます)。

![Azure Monitor](./media/azure-monitor/azure-monitor-flow.png)

## <a name="prerequisites"></a>前提条件

この記事の手順を完了するには、Azure PowerShell モジュールを使用して Azure Resource Manager テンプレートをデプロイします。

* [Azure PowerShell モジュール](https://docs.microsoft.com/powershell/azure/install-az-ps) バージョン 6.13.1 以降

[Azure Cloud Shell](https://shell.azure.com) を使用することもできます。これには、最新バージョンの Azure PowerShell モジュールが含まれます。

## <a name="delegated-resource-management"></a>委任されたリソース管理

Azure AD B2C では、[Azure Active Directory 監視](../active-directory/reports-monitoring/overview-monitoring.md)が利用されます。 Azure AD B2C テナント内の Azure Active Directory で*診断設定*を有効にするには、[委任されたリソース管理](../lighthouse/concepts/azure-delegated-resource-management.md)を使用します。

Azure サブスクリプション (**顧客**) が含まれるテナント内の Azure Monitor インスタンスを構成するには、Azure AD B2C ディレクトリ (**サービス プロバイダー**) でユーザーまたはグループを承認します。 承認を作成するには、サブスクリプションが含まれる Azure AD テナントに [Azure Resource Manager](../azure-resource-manager/index.yml) テンプレートをデプロイします。 以下のセクションでは、そのプロセスの手順を説明します。

## <a name="create-or-choose-resource-group"></a>リソース グループの作成または選択

これは、Azure Monitor からデータを受信するための、送信先の Azure ストレージ アカウント、イベント ハブ、または Log Analytics ワークスペースを含むリソース グループです。 Azure Resource Manager テンプレートをデプロイするときに、リソース グループ名を指定します。

[リソース グループを作成する](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups)か、お使いの Azure サブスクリプションが含まれる Azure Active Directory (Azure AD) テナントで (Azure AD B2C テナントが含まれるディレクトリでは "*ありません*")、既存のリソース グループを選択します。

この例では、"*米国中部*" リージョンの *azure-ad-b2c-monitor* という名前のリソース グループを使用します。

## <a name="delegate-resource-management"></a>リソース管理を委任する

次に、以下の情報を収集します。

お使いの Azure AD B2C ディレクトリの**ディレクトリ ID** (テナント ID とも呼ばれます)。

1. "*ユーザー管理者*" ロール (またはそれ以上) を持つユーザーとして、[Azure portal](https://portal.azure.com/) にサインインします。
1. ポータル ツール バーにある **[ディレクトリ + サブスクリプション]** アイコンを選択し、Azure AD B2C テナントを含むディレクトリを選択します。
1. **[Azure Active Directory]** を選択し、 **[プロパティ]** を選択します。
1. **[ディレクトリ ID]** を記録しておきます。

お使いのサブスクリプションが含まれるディレクトリに先ほど作成したリソース グループに対する "*共同作成者*" アクセス許可を付与する、Azure AD B2C グループまたはユーザーの**オブジェクト ID**。

管理をより簡単にするには、各ロールに Azure AD のユーザー "*グループ*" を使用することをお勧めします。これにより、個々のユーザーに直接アクセス許可を割り当てるのではなく、ユーザーをグループに追加または削除することができます。 このチュートリアルでは、ユーザーを追加します。

1. Azure portal でまだ **[Azure Active Directory]** を選択した状態で、 **[ユーザー]** を選択してから、ユーザーを選択します。
1. ユーザーの **[オブジェクト ID]** を記録しておきます。

### <a name="create-an-azure-resource-manager-template"></a>Azure Resource Manager テンプレートの作成

Azure AD テナント (**顧客**) をオンボードするには、プラン用に次の情報を含む [Azure Resource Manager テンプレート](../lighthouse/how-to/onboard-customer.md)を作成します。 `mspOfferName` と `mspOfferDescription` の値は、Azure portal の[サービス プロバイダーのページ](../lighthouse/how-to/view-manage-service-providers.md)でプランの詳細を表示して確認できます。

| フィールド   | 定義 |
|---------|------------|
| `mspOfferName`                     | この定義を説明する名前。 たとえば、"*Azure AD B2C 管理サービス*" などです。 この値は、プランのタイトルとして顧客に表示されます。 |
| `mspOfferDescription`              | プランの簡単な説明。 たとえば、"*Azure AD B2C で Azure Monitor を有効にする*" などです。|
| `rgName`                           | 前に Azure AD テナントに作成したリソースグループの名前。 たとえば、*azure-ad-b2c-monitor* などです。 |
| `managedByTenantId`                | お使いの Azure AD B2C テナントの**ディレクトリ ID** (テナント ID とも呼ばれます)。 |
| `authorizations.value.principalId` | この Azure サブスクリプション内のリソースにアクセスできる B2C グループまたはユーザーの**オブジェクト ID**。 このチュートリアルでは、前に記録したユーザーのオブジェクト ID を指定します。 |

Azure Resource Manager テンプレートとパラメーター ファイルをダウンロードします。

- [rgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)
- [rgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)

次に、前に記録した値を使用して、パラメーター ファイルを更新します。 次の JSON スニペットでは、Azure Resource Manager テンプレートのパラメーター ファイルの例を示します。 `authorizations.value.roleDefinitionId` には、"*共同作成者ロール*" に対する[組み込みロール](../role-based-access-control/built-in-roles.md)の値 `b24988ac-6180-42a0-ab88-20f7382dd24c` を使用します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mspOfferName": {
            "value": "Azure AD B2C Managed Services"
        },
        "mspOfferDescription": {
            "value": "Enables Azure Monitor in Azure AD B2C"
        },
        "rgName": {
            "value": "azure-ad-b2c-monitor"
        },
        "managedByTenantId": {
            "value": "<Replace with DIRECTORY ID of Azure AD B2C tenant (tenant ID)>"
        },
        "authorizations": {
            "value": [
                {
                    "principalId": "<Replace with user's OBJECT ID>",
                    "principalIdDisplayName": "Azure AD B2C tenant administrator",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                }
            ]
        }
    }
}
```

### <a name="deploy-the-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートをデプロイする

パラメーター ファイルを更新したら、Azure Resource Manager テンプレートをサブスクリプションレベルのデプロイとして Azure テナントにデプロイします。 これはサブスクリプション レベルのデプロイなので、Azure portal 上で開始することはできません。 Azure PowerShell モジュールまたは Azure CLI を使用してデプロイできます。 以下では Azure PowerShell の方法を示します。

[Connect-AzAccount](/powershell/azure/authenticate-azureps) を使用して、サブスクリプションが含まれるディレクトリにサインインします。 `-tenant` フラグを使用して、正しいディレクトリへの認証を強制します。

```PowerShell
Connect-AzAccount -tenant contoso.onmicrosoft.com
```

[Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) コマンドレットを使用して、Azure AD テナントで現在のアカウントがアクセスできるサブスクリプションの一覧を表示します。 Azure AD B2C テナントに追加するサブスクリプションの ID を記録します。

```PowerShell
Get-AzSubscription
```

次に、Azure AD B2C テナントに追加するサブスクリプションに切り替えます。

``` PowerShell
Select-AzSubscription <subscription ID>
```

最後に、先ほどダウンロードして更新した Azure Resource Manager テンプレートとパラメーター ファイルをデプロイします。 `Location`、`TemplateFile`、`TemplateParameterFile` の値は適宜置き換えます。

```PowerShell
New-AzDeployment -Name "AzureADB2C" `
                 -Location "centralus" `
                 -TemplateFile "C:\Users\azureuser\Documents\rgDelegatedResourceManagement.json" `
                 -TemplateParameterFile "C:\Users\azureuser\Documents\rgDelegatedResourceManagement.parameters.json" `
                 -Verbose
```

テンプレートが正常にデプロイされると、次のような出力が生成されます (簡潔にするため出力は切り捨ててあります)。

```Console
PS /usr/csuser/clouddrive> New-AzDeployment -Name "AzureADB2C" `
>>                  -Location "centralus" `
>>                  -TemplateFile "rgDelegatedResourceManagement.json" `
>>                  -TemplateParameterFile "rgDelegatedResourceManagement.parameters.json" `
>>                  -Verbose
WARNING: Breaking changes in the cmdlet 'New-AzDeployment' :
WARNING:  - The cmdlet 'New-AzSubscriptionDeployment' is replacing this cmdlet.


WARNING: NOTE : Go to https://aka.ms/azps-changewarnings for steps to suppress this breaking change warning, and other information on breaking changes in Azure PowerShell.
VERBOSE: 7:25:14 PM - Template is valid.
VERBOSE: 7:25:15 PM - Create template deployment 'AzureADB2C'
VERBOSE: 7:25:15 PM - Checking deployment status in 5 seconds
VERBOSE: 7:25:42 PM - Resource Microsoft.ManagedServices/registrationDefinitions '44444444-4444-4444-4444-444444444444' provisioning status is succeeded
VERBOSE: 7:25:48 PM - Checking deployment status in 5 seconds
VERBOSE: 7:25:53 PM - Resource Microsoft.Resources/deployments 'rgAssignment' provisioning status is running
VERBOSE: 7:25:53 PM - Checking deployment status in 5 seconds
VERBOSE: 7:25:59 PM - Resource Microsoft.ManagedServices/registrationAssignments '11111111-1111-1111-1111-111111111111' provisioning status is running
VERBOSE: 7:26:17 PM - Checking deployment status in 5 seconds
VERBOSE: 7:26:23 PM - Resource Microsoft.ManagedServices/registrationAssignments '11111111-1111-1111-1111-111111111111' provisioning status is succeeded
VERBOSE: 7:26:23 PM - Checking deployment status in 5 seconds
VERBOSE: 7:26:29 PM - Resource Microsoft.Resources/deployments 'rgAssignment' provisioning status is succeeded

DeploymentName          : AzureADB2C
Location                : centralus
ProvisioningState       : Succeeded
Timestamp               : 1/31/20 7:26:24 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name                   Type                       Value
                          =====================  =========================  ==========
                          mspOfferName           String                     Azure AD B2C Managed Services
                          mspOfferDescription    String                     Enables Azure Monitor in Azure AD B2C
...
```

テンプレートをデプロイした後、リソースの反映が完了するまでに数分かかることがあります。 次のセクションに進んでサブスクリプションを選択する前に、数分 (通常は 5 分以下) 待つことが必要な場合があります。

## <a name="select-your-subscription"></a>サブスクリプションを選択します。

テンプレートをデプロイし、リソースの反映が完了するまで数分待った後、次の手順でサブスクリプションを Azure AD B2C ディレクトリに関連付けます。

1. 現在サインインしている場合は、**Azure portal** からサインアウトします。 このステップと次のステップで、ポータル セッションの資格情報を更新します。
1. Azure AD B2C の管理者アカウントで [Azure portal](https://portal.azure.com) にサインインします。
1. ポータルツールバーの **[ディレクトリ + サブスクリプション]** アイコンを選択します。
1. ご利用のサブスクリプションが含まれているディレクトリを選択します。

    ![ディレクトリを切り替える](./media/azure-monitor/azure-monitor-portal-03-select-subscription.png)
1. 正しいディレクトリとサブスクリプションを選択してあることを確認します。 この例では、すべてのディレクトリとサブスクリプションが選択されています。

    ![ディレクトリとサブスクリプションのフィルターで選択されたすべてのディレクトリ](./media/azure-monitor/azure-monitor-portal-04-subscriptions-selected.png)

## <a name="configure-diagnostic-settings"></a>診断設定を構成する

診断設定では、リソースのログとメトリックを送信する場所を定義します。 使用できる送信先は次のとおりです。

- [Azure Storage アカウント](../azure-monitor/platform/resource-logs-collect-storage.md)
- [イベント ハブ](../azure-monitor/platform/resource-logs-stream-event-hubs.md) ソリューション。
- [Log Analytics ワークスペース](../azure-monitor/platform/resource-logs-collect-workspace.md)

まだ作成していない場合は、[Azure Resource Manager テンプレート](#create-an-azure-resource-manager-template)で指定したリソース グループに、選択した送信先の種類のインスタンスを作成します。

### <a name="create-diagnostic-settings"></a>診断設定の作成

Azure portal で[診断設定を作成する](../active-directory/reports-monitoring/overview-monitoring.md)準備が整いました。

Azure AD B2C のアクティビティ ログの監視設定を構成するには:

1. [Azure portal](https://portal.azure.com/) にサインインします。
1. ポータル ツール バーにある **[ディレクトリ + サブスクリプション]** アイコンを選択し、Azure AD B2C テナントを含むディレクトリを選択します。
1. **[Azure Active Directory]** を選択します
1. **[監視]** で **[診断設定]** を選択します。
1. リソースに既存の設定が存在する場合は、構成済みの設定の一覧が表示されます。 **[診断設定を追加する]** を選択して新しい設定を追加するか、または **[設定の編集]** を選択して既存の設定を編集します。 各設定には、各送信先の種類を 1 つだけ含めることができます。

    ![Azure portal の [診断設定] ペイン](./media/azure-monitor/azure-monitor-portal-05-diagnostic-settings-pane-enabled.png)

1. 設定にまだ名前がない場合は、名前を付けます。
1. ログを送信する各送信先のチェック ボックスをオンにします。 **[構成]** を選択し、次の表に示すようにそれらの設定を指定します。

    | 設定 | 説明 |
    |:---|:---|
    | ストレージ アカウントへのアーカイブ | ストレージ アカウントの名前。 |
    | イベント ハブへのストリーミング | イベント ハブが作成される名前空間 (ログを初めてストリーミングする場合)、またはストリーミング先の名前空間 (そのログ カテゴリをこの名前空間にストリーミングしているリソースが既に存在する場合)。
    | Log Analytics への送信 | ワークスペースの名前。 |

1. **AuditLogs** と **SignInLogs** を選択します。
1. **[保存]** を選択します。

## <a name="next-steps"></a>次のステップ

Azure Monitor での診断設定の追加と構成の詳細については、「[チュートリアル: Azure リソースからリソース ログを収集して分析する](../azure-monitor/insights/monitor-azure-resource.md)」を参照してください。

イベント ハブへの Azure AD ログのストリーム配信の詳細については、「[チュートリアル: Azure Active Directory ログを Azure イベント ハブにストリーム配信する](../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)」を参照してください。
