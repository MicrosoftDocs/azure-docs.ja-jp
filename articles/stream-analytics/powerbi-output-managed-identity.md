---
title: マネージド ID を使用して、Power BI 出力に対して Azure Stream Analytics ジョブを認証する
description: この記事では、マネージド ID を使用して、Power BI 出力に対して Azure Stream Analytics ジョブを認証する方法について説明します。
ms.service: stream-analytics
author: enkrumah
ms.author: ebnkruma
ms.topic: how-to
ms.date: 3/10/2020
ms.openlocfilehash: 7c1ddbbbd8198cf769e89cfa824de370184a992c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104589686"
---
# <a name="use-managed-identity-to-authenticate-your-azure-stream-analytics-job-to-power-bi"></a>マネージド ID を使用して、Power BI に対して Azure Stream Analytics ジョブを認証する

Power BI への出力の[マネージド ID 認証](../active-directory/managed-identities-azure-resources/overview.md)により、Stream Analytics ジョブは Power BI アカウント内のワークスペースに直接アクセスできるようになります。 この機能を使用すると、Stream Analytics ジョブのデプロイを完全に自動化できます。これは、ユーザーが Azure portal 経由で Power BI に対話形式でログインする必要がなくなったためです。 さらに、ジョブを定期的に再認証する必要がないため、Power BI に書き込む長時間実行ジョブのサポートが強化されました。

この記事では、Azure portal を通じて、および Azure Resource Manager デプロイを通じて、Stream Analytics ジョブの Power BI 出力に対してマネージド ID を有効にする方法を示します。

## <a name="prerequisites"></a>前提条件

この機能を使用するには、次のものが必要です。

- [Pro ライセンス](/power-bi/service-admin-purchasing-power-bi-pro)を持つ Power BI アカウント。

- Power BI アカウント内のアップグレードされたワークスペース。 詳細については、この機能の [Power BI のお知らせ](https://powerbi.microsoft.com/blog/announcing-new-workspace-experience-general-availability-ga/)に関する記事を参照してください。

## <a name="create-a-stream-analytics-job-using-the-azure-portal"></a>Azure portal を使用して Stream Analytics ジョブを作成する

1. Azure portal で、新しい Stream Analytics ジョブを作成するか、既存のジョブを開きます。 画面の左側にあるメニュー バーで、 **[構成]** の下にある **[マネージド ID]** を選択します。 [システム割り当てマネージド ID を使用] が選択されていることを確認し、画面下部にある **[保存]** ボタンを選択します。

   ![Stream Analytics のマネージド ID の構成](./media/common/stream-analytics-enable-managed-identity.png)

2. 出力を構成する前に、この記事の「[Stream Analytics ジョブに Power BI ワークスペースへのアクセス権を付与する](#give-the-stream-analytics-job-access-to-your-power-bi-workspace)」の手順に従って、Power BI ワークスペースへの Stream Analytics ジョブのアクセス権を付与します。

3. Stream Analytic のジョブの **[出力]** セクションに移動し、 **[+ 追加**] を選択し、 **[Power BI]** を選択します。 次に、 **[承認]** ボタンを選択し、Power BI アカウントでログインします。

   ![Power BI アカウントを承認する](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-authorize-powerbi.png)

4. 承認されると、アクセス権のあるすべてのワークスペースがドロップダウン リストに取り込まれます。 前の手順で承認したワークスペースを選択します。 次に、 **[認証]** で "認証モード" を選択します。 最後に、 **[保存]** ボタンを選択します。

   ![マネージド ID を使用した Power BI 出力の構成](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-configure-powerbi-with-managed-id.png)

## <a name="azure-resource-manager-deployment"></a>Azure Resource Manager デプロイ

Azure Resource Manager を使用すると、Stream Analytics ジョブのデプロイを完全に自動化できます。 Azure PowerShell または [Azure CLI](/cli/azure/) を使用して、Resource Manager テンプレートをデプロイできます。 次の例では、Azure CLI を使用しています。


1. Resource Manager テンプレートのリソース セクションに次のプロパティを含めることで、マネージド ID を持つ **Microsoft.StreamAnalytics/streamingjobs** リソースを作成できます。

    ```json
    "identity": {
        "type": "SystemAssigned",
    }
    ```

   このプロパティにより、Stream Analytics ジョブの ID を作成し、管理するように Azure Resource Manager に通知されます。 次に示すのは、マネージド ID が有効になった Stream Analytics ジョブと、マネージド ID を使用する Power BI 出力シンクをデプロイする Resource Manager テンプレートの例です。

    ```json
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
            {
                "apiVersion": "2017-04-01-preview",
                "name": "pbi_managed_id",
                "location": "[resourceGroup().location]",
                "type": "Microsoft.StreamAnalytics/StreamingJobs",
                "identity": {
                    "type": "systemAssigned"
                },
                "properties": {
                    "sku": {
                        "name": "standard"
                    },
                    "outputs":[
                        {
                            "name":"output",
                            "properties":{
                                "datasource":{
                                    "type":"PowerBI",
                                    "properties":{
                                        "dataset": "dataset_name",
                                        "table": "table_name",
                                        "groupId": "01234567-89ab-cdef-0123-456789abcdef",
                                        "authenticationMode": "Msi"
                                    }
                                }
                            }
                        }
                    ]
                }
            }
        ]
    }
    ```

    次の Azure CLI コマンドを使用して、上記のジョブをリソース グループ **ExampleGroup** にデプロイします。

    ```azurecli
    az deployment group create --resource-group ExampleGroup -template-file StreamingJob.json
    ```

2. ジョブが作成された後、Azure Resource Manager を使用して、そのジョブの完全な定義を取得します。

    ```azurecli
    az resource show --ids /subscriptions/<subsription-id>/resourceGroups/<resource-group>/providers/Microsoft.StreamAnalytics/StreamingJobs/<resource-name>
    ```

    上記のコマンドでは、次のような応答が返されます。

    ```json
    {
        "id": "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.StreamAnalytics/streamingjobs/<resource-name>",
        "identity": {
            "principalId": "<principal-id>",
            "tenantId": "<tenant-id>",
            "type": "SystemAssigned",
            "userAssignedIdentities": null
        },
        "kind": null,
        "location": "West US",
        "managedBy": null,
        "name": "<resource-name>",
        "plan": null,
        "properties": {
            "compatibilityLevel": "1.0",
            "createdDate": "2019-07-12T03:11:30.39Z",
            "dataLocale": "en-US",
            "eventsLateArrivalMaxDelayInSeconds": 5,
            "jobId": "<job-id>",
            "jobState": "Created",
            "jobStorageAccount": null,
            "jobType": "Cloud",
            "outputErrorPolicy": "Stop",
            "package": null,
            "provisioningState": "Succeeded",
            "sku": {
                "name": "Standard"
            }
        },
        "resourceGroup": "<resource-group>",
        "sku": null,
        "tags": null,
        "type": "Microsoft.StreamAnalytics/streamingjobs"
    }
    ```

    Power BI の REST API を使用して Stream Analytics ジョブを Power BI ワークスペースに追加する予定がある場合は、返された "principalId" を書き留めておきます。

3. ジョブが作成されたので、この記事の「[Stream Analytics ジョブに Power BI ワークスペースへのアクセス権を付与する](#give-the-stream-analytics-job-access-to-your-power-bi-workspace)」セクションを参照してください。


## <a name="give-the-stream-analytics-job-access-to-your-power-bi-workspace"></a>Stream Analytics ジョブに Power BI ワークスペースへのアクセス権を付与する

Stream Analytics ジョブが作成されたので、Power BI ワークスペースへのアクセス権を付与することができます。 ジョブにアクセス権を付与したら、ID が伝達されるまで数分待ちます。

### <a name="use-the-power-bi-ui"></a>Power BI UI を使用する

   > [!Note]
   > UI を使用して Stream Analytics ジョブを Power BI ワークスペースに追加するには、Power BI 管理ポータルの **[開発者向け設定]** でサービス プリンシパル アクセスを有効にする必要もあります。 詳細については、「[サービス プリンシパルの使用を始める](/power-bi/developer/embed-service-principal)」を参照してください。

1. ワークスペースのアクセス設定に移動します。 詳細については、次の記事を参照してください。「[ワークスペースへのアクセスを許可する](/power-bi/service-create-the-new-workspaces#give-access-to-your-workspace)」

2. テキストボックスに Stream Analytics ジョブの名前を入力し、アクセス レベルとして **[Contributor]\(共同作成者\)** を選択します。

3. **[追加]** を選択し、ペインを閉じます。

   ![Power BI ワークスペースに Stream Analytics ジョブを追加する](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-add-job-to-powerbi-workspace.png)

### <a name="use-the-power-bi-powershell-cmdlets"></a>Power BI PowerShell コマンドレットを使用する

1. Power BI `MicrosoftPowerBIMgmt` PowerShell コマンドレットをインストールします。

   > [!Important]
   > バージョン 1.0.821 以降のコマンドレットを使用していることを確認してください。

```powershell
Install-Module -Name MicrosoftPowerBIMgmt
```

2. Power BI にログインします。

```powershell
Login-PowerBI
```

3. 共同作成者として Stream Analytics ジョブをワークスペースに追加します。

```powershell
Add-PowerBIWorkspaceUser -WorkspaceId <group-id> -PrincipalId <principal-id> -PrincipalType App -AccessRight Contributor
```

### <a name="use-the-power-bi-rest-api"></a>Power BI REST API を使用する

"グループ ユーザーの追加" REST API を直接使用して、Stream Analytics ジョブを共同作成者としてワークスペースに追加することもできます。 この API の完全なドキュメントは次の場所にあります。[グループ - グループ ユーザーの追加](/rest/api/power-bi/groups/addgroupuser)。

**要求のサンプル**
```http
POST https://api.powerbi.com/v1.0/myorg/groups/{groupId}/users
```
要求本文
```json
{
    "groupUserAccessRight": "Contributor",
    "identifier": "<principal-id>",
    "principalType": "App"
}
```

## <a name="remove-managed-identity"></a>マネージド ID の削除

Stream Analytics ジョブに対して作成されたマネージド ID は、ジョブが削除されたときにのみ削除されます。 ジョブを削除せずにマネージド ID を削除することはできません。 マネージド ID を使用する必要がなくなった場合は、出力の認証方法を変更できます。 マネージド ID は、ジョブが削除されるまで存在し続け、マネージド ID の認証を再度使用する場合に使用されます。

## <a name="limitations"></a>制限事項
この機能の制限は次のとおりです。

- クラシック Power BI ワークスペースはサポートされていません。

- Azure Active Directory なしの Azure アカウント。

- マルチテナント アクセスは、サポートされていません。 特定の Stream Analytics ジョブに対して作成されたサービス プリンシパルは、ジョブが作成された同じ Azure Active Directory テナントに配置する必要があり、別の Azure Active Directory テナントに配置されたリソースでは使用できません。

- [ユーザー割り当て ID](../active-directory/managed-identities-azure-resources/overview.md) はサポートされていません。 つまり、Stream Analytics ジョブで使用される、独自のサービス プリンシパルを入力することはできません。 サービス プリンシパルは、Azure Stream Analytics で生成する必要があります。

## <a name="next-steps"></a>次のステップ

* [Azure Stream Analytics との Power BI ダッシュボードの統合](./stream-analytics-power-bi-dashboard.md)
* [Azure Stream Analytics からの出力を理解する](./stream-analytics-define-outputs.md)
