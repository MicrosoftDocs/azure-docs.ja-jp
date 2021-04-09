---
title: Azure Monitor でアクティビティ ログ アラートを作成、表示、管理する
description: Azure portal、Azure Resource Manager テンプレート、および Azure PowerShell を使用してアクティビティ ログ アラートを作成します。
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: 26ca755f6675fa19c3b122c3528e05d1e8d76845
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102045532"
---
# <a name="create-view-and-manage-activity-log-alerts-by-using-azure-monitor"></a>Azure Monitor を使用してアクティビティ ログ アラートを作成、表示、管理する  

## <a name="overview"></a>概要

アクティビティ ログ アラートは、アラートで指定した条件と一致する新しいアクティビティ ログ イベントが発生したときにアクティブになるアラートです。

これらのアラートは Azure リソースに対応しており、Azure Resource Manager テンプレートを使用して作成できます。 これらは、Azure Portal で作成、更新、削除することもできます。 通常は、Azure サブスクリプション内のリソースに特定の変更が発生した場合に通知を受け取るためにアクティビティ ログ アラートを作成します。 アラートは、多くの場合、特定のリソース グループまたはリソースにスコープ指定されます。 たとえば、サンプル リソース グループ **myProductionResourceGroup** 内の仮想マシンが削除されたときに通知を受け取ることができます。 または、サブスクリプション内のユーザーに新しいロールが割り当てられた場合に通知を受け取ることもできます。

> [!IMPORTANT]
> サービス正常性通知に対するアラートは、アクティビティ ログ アラート作成用のインターフェイスでは作成できません。 サービス正常性通知の作成および使用の詳細については、[サービス正常性通知のアクティビティ ログ アラートの受け取り](../../service-health/alerts-activity-log-service-notifications-portal.md)に関する記事をご覧ください。

アラート ルールを作成するときには、次のことを確認してください。

- スコープ内のサブスクリプションが、アラートが作成されているサブスクリプションと同じである。
- 条件は、アラートが構成されているレベル/状態/呼び出し元/リソース グループ/リソース ID/リソースの種類/イベント カテゴリでなければならない。
- "allOf" 条件は 1 つだけ許可されます。
- "AnyOf" を使用すると、複数のフィールドに対して複数の条件を許可できます (たとえば、"status" フィールドまたは "subStatus" フィールドのいずれかが特定の値と等しい場合など)。 "AnyOf" の使用は、現時点では ARM テンプレート デプロイを使用したアラート ルールの作成に限定されていることに注意してください。
- "ContainsAny" を使用して、同じフィールドの複数の値を許可できます (たとえば、"operation" が "delete" または "modify" に等しいかどうか)。 "ContainsAny" の使用は、現時点では ARM テンプレート デプロイを使用したアラート ルールの作成に限定されていることに注意してください。
- カテゴリが "管理" の場合、アラートに上記の条件を 1 つ以上指定する必要があります。 アクティビティ ログ内にイベントが作成されるたびにアクティブ化するアラートを作成することはできません。
- アクティビティ ログのアラートのカテゴリに含まれるイベントに対して、アラートを作成することはできません。

## <a name="azure-portal"></a>Azure portal

Azure portal を使用して、アクティビティ ログ アラート ルールを作成および変更できます。 このエクスペリエンスは Azure アクティビティ ログに統合され、対象とする特定のイベントに対するシームレスなアラート作成が保証されます。

### <a name="create-with-the-azure-portal"></a>Azure portal での作成

次の手順に従います。

1. Azure portal で、 **[モニター]**  >  **[アラート]** の順に選択します。
2. **[アラート]** ウィンドウの左上隅にある **[新しいアラート ルール]** を選択します。

     ![新しいアラート ルール](media/alerts-activity-log/AlertsPreviewOption.png)

     **[ルールの作成]** ウィンドウが表示されます。

      ![新しいアラート ルールのオプション](media/alerts-activity-log/create-new-alert-rule-options.png)

3. **[アラートの条件を定義します]** で、次の情報を入力し、 **[完了]** を選択します。

   - **アラートの対象:** 新しいアラートの対象を表示して選択するには、 **[サブスクリプション別でフィルター]**  /  **[リソースの種類でフィルター]** を選択します。 表示された一覧からリソースまたはリソース グループを選択します。

     > [!NOTE]
     > 
     > 選択できるのは、[Azure Resource Manager](../../azure-resource-manager/management/overview.md) の追跡対象のリソース、リソース グループ、またはアクティビティ ログ シグナルのサブスクリプション全体のみになります。 

     **[アラートの対象] のサンプル ビュー**

     ![ターゲット デバイスの選択](media/alerts-activity-log/select-target.png)

   - **[対象の条件]** から、 **[条件の追加]** を選択します。 ターゲットで使用可能なすべてのシグナルが表示されます。これには、**アクティビティ ログ** のさまざまなカテゴリのものが含まれます。 カテゴリ名が **監視サービス** 名に追加されます。

   - **[アクティビティ ログ]** という種類について使用可能なさまざまな操作の一覧からシグナルを選択します。

     ログ履歴のタイムラインと、この対象シグナルの対応するアラート ロジックを選択できます。

     **[条件の追加] 画面**

     ![条件の追加](media/alerts-activity-log/add-criteria.png)
     
     > [!NOTE]
     > 
     >  高品質で効果的なルールを作成するために、Microsoft ではルールに "すべて管理" というシグナルを指定した条件を 1 つ以上追加することをお願いしています。 
     > アラートの定義の一環として、[イベントのレベル]、[状態]、[開始者] のいずれか 1 つのドロップ ダウンを入力してください。これにより、ルールがより具体的になります。

     - **履歴の時間:** 選択した操作で利用できるイベントをプロットできるのは、過去 6 時間/12 時間/24 時間または過去 1 週間です。

     - **[アラート ロジック]** :

       - **イベントのレベル**:イベントの重大度レベル: "_詳細_"、"_情報_"、"_警告_"、"_エラー_" または "_重大_"。
       - **状態**: イベントの状態: "_開始済み_"、"_失敗_" または "_成功_"。
       - **イベント開始者**: "呼び出し元" とも呼ばれます。 操作を実行したユーザーの電子メール アドレスまたは Azure Active Directory 識別子。

       このサンプルのシグナル グラフには、アラート ロジックが適用されています。

       ![選択済み条件](media/alerts-activity-log/criteria-selected.png)

4. **[アラートの詳細を定義します]** で、以下の詳細を入力します。

    - **アラート ルール名**:新しいアラート ルールの名前。
    - **説明**:新しいアラート ルールの説明。
    - **リソース グループにアラートを保存します**:この新しいルールを保存するリソース グループを選択します。

5. **[アクション グループ]** のドロップダウン メニューから、この新しいアラート ルールに割り当てるアクション グループを指定します。 または、[新しいアクション グループを作成](./action-groups.md)して、新しいルールに割り当てます。 新しいグループを作成するには、 **[+ 新しいグループ]** を選択します。

6. ルールを作成後に有効にするには、 **[ルールの作成時に有効にする]** オプションの **[はい]** を選択します。
7. **[アラート ルールの作成]** を選択します。

    アクティビティ ログの新しいアラート ルールが作成され、ウィンドウの右上隅に確認メッセージが表示されます。

    ルールは、有効化、無効化、編集、または削除することができます。 アクティビティ ログ ルールを管理する方法を確認してください。


アクティビティ ログにアラート ルールを作成できる状況を簡単にたとえるとすれば、[Azure portal のアクティビティ ログ](../essentials/activity-log.md#view-the-activity-log)でイベントを探索したりフィルター処理したりするのに似ています。 **Azure Monitor のアクティビティ ログ** 画面では、必要なイベントをフィルター処理または検索し、次に **[アクティビティ ログ アラートの追加]** ボタンを使用してアラートを作成できます。 次に、前に示した手順 4 から 7 に従います。
    
 ![アクティビティ ログからのアラートの追加](media/alerts-activity-log/add-activity-log.png)
    

### <a name="view-and-manage-in-the-azure-portal"></a>Azure portal での表示と管理

1. Azure portal で、 **[モニター]**  >  **[アラート]** の順に選択します。 ウィンドウの左上隅にある **[アラート ルールの管理]** を選択します。

    ![アクティビティ ログのスクリーンショット。検索ボックスが強調表示されています。](media/alerts-activity-log/manage-alert-rules.png)

    使用可能なルールの一覧が表示されます。

2. 変更するアクティビティ ログ ルールを検索します。

    ![アクティビティ ログ アラート ルールの検索](media/alerts-activity-log/searth-activity-log-rule-to-edit.png)

    編集するアクティビティのルールを検索するには、用意されているフィルターとして _[サブスクリプション]_ 、 _[リソース グループ]_ 、 _[リソース]_ 、 _[シグナルの種類]_ または _[状態]_ を使用できます。

   > [!NOTE]
   > 
   > 編集できるのは、 **[説明]** 、 **[対象の条件]** 、 **[アクション グループ]** のみです。

3. ルールを選択し、ダブルクリックしてルールのオプションを編集します。 必要な変更を行い、 **[保存]** を選択します。

   ![アラート ルールの管理](media/alerts-activity-log/activity-log-rule-edit-page.png)

4. ルールは、有効化、無効化、または削除できます。 手順 2 で説明したようにルールを選択した後、ウィンドウの上部にある適切なオプションを選択します。


## <a name="azure-resource-manager-template"></a>Azure Resource Manager テンプレート
Azure Resource Manager テンプレートを使用してアクティビティ ログ アラート ルールを作成するには、`microsoft.insights/activityLogAlerts` 型のリソースを作成します。 次に、関連するすべてのプロパティを入力します。 アクティビティ ログ アラート ルールを作成するテンプレートを以下に示します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "activityLogAlertName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Activity log alert."
      }
    },
    "activityLogAlertEnabled": {
      "type": "bool",
      "defaultValue": true,
      "metadata": {
        "description": "Indicates whether or not the alert is enabled."
      }
    },
    "actionGroupResourceId": {
      "type": "string",
      "metadata": {
        "description": "Resource Id for the Action group."
      }
    }
  },
  "resources": [   
    {
      "type": "Microsoft.Insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlertName')]",      
      "location": "Global",
      "properties": {
        "enabled": "[parameters('activityLogAlertEnabled')]",
        "scopes": [
            "[subscription().id]"
        ],        
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Administrative"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Resources/deployments/write"
            },
            {
              "field": "resourceType",
              "equals": "Microsoft.Resources/deployments"
            }
          ]
        },
        "actions": {
          "actionGroups":
          [
            {
              "actionGroupId": "[parameters('actionGroupResourceId')]"
            }
          ]
        }
      }
    }
  ]
}
```
前述のサンプル JSON は、このチュートリアルの目的で、たとえば sampleActivityLogAlert.json として保存でき、[Azure portal で Azure Resource Manager](../../azure-resource-manager/templates/deploy-portal.md) を使用してデプロイできます。

  > [!NOTE]
  > 
  > 定義できる最高レベルのアクティビティ ログ アラートはサブスクリプションであることに注意してください。
  > つまり、いくつかのサブスクリプションに対してアラートを定義するオプションがないため、定義はサブスクリプションごとにアラートである必要があります。

次のフィールドは、Azure Resource Manager テンプレートで条件フィールドに対して使用できるオプションです。"Resource Health"、"Advisor"、および "Service Health" には、特別なフィールドに対して追加のプロパティ フィールドがあることに注意してください。 
1. resourceId:アラートを生成する必要があるアクティビティ ログ イベントの影響を受けるリソースのリソース ID。
2. category:アクティビティ ログ イベントのカテゴリ。 次に例を示します。Administrative、ServiceHealth、ResourceHealth、Autoscale、Security、Recommendation、Policy。
3. caller:アクティビティ ログ イベントの操作を実行したユーザーのメール アドレスまたは Azure Active Directory 識別子。
4. level:アラートを生成する必要があるアクティビティ ログ イベントのアクティビティのレベル。 次に例を示します。Critical、Error、Warning、Informational、Verbose。
5. operationName:アクティビティ ログ イベントの操作の名前。 次に例を示します。Microsoft.Resources/deployments/write
6. resourceGroup:アクティビティ ログ イベントの影響を受けるリソースのリソース グループの名前。
7. resourceProvider:[Azure リソース プロバイダーと種類の説明](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-resource-manager%2Fmanagement%2Fresource-providers-and-types&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373543634&sdata=4RjpTkO5jsdOgPdt%2F%2FDOlYjIFE2%2B%2BuoHq5%2F7lHpCwQw%3D&reserved=0)。 リソース プロバイダーを Azure サービスにマップされるリストについては、「[Resource providers for Azure services](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-resource-manager%2Fmanagement%2Fazure-services-resource-providers&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373553639&sdata=0ZgJPK7BYuJsRifBKFytqphMOxMrkfkEwDqgVH1g8lw%3D&reserved=0)」 (Azure サービスのリソースプロバイダー) を参照してください。
8. status:アクティビティ イベントの操作の状態を説明する文字列。 次に例を示します。Started、In Progress、Succeeded、Failed、Active、Resolved
9. subStatus:通常は対応する REST 呼び出しの HTTP 状態コードですが、サブステータスを示す他の文字列が含まれる場合もあります。   次に例を示します。OK (HTTP 状態コード: 200)、作成済み (HTTP 状態コード:201)、受理 (HTTP 状態コード:202)、コンテンツなし (HTTP 状態コード:204)、無効な要求 (HTTP 状態コード:400)、見つかりません (HTTP 状態コード:404)、競合 (HTTP 状態コード:409)、内部サーバー エラー (HTTP 状態コード:500)、サービス利用不可 (HTTP 状態コード:503)、Gateway Timeout (HTTP 状態コード: 504)。
10. resourceType:イベントの影響を受けたリソースの種類。 次に例を示します。Microsoft.Resources/deployments

次に例を示します。

```json
"condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Administrative"
            },
            {
              "field": "resourceType",
              "equals": "Microsoft.Resources/deployments"
            }
          ]
        }

```
アクティビティ ログのフィールドの詳細については、[こちら](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-monitor%2Fplatform%2Factivity-log-schema&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373563632&sdata=6QXLswwZgUHFXCuF%2FgOSowLzA8iOALVgvL3GMVhkYJY%3D&reserved=0)を参照してください。



> [!NOTE]
> 新しいアクティビティ ログ アラート ルールがアクティブになるまで最大 5 分かかる場合があります。

## <a name="rest-api"></a>REST API 
[Azure Monitor Activity Log Alerts API](/rest/api/monitor/activitylogalerts) は REST API です。 これは、Azure Resource Manager REST API と完全に互換性があります。 また、Resource Manager コマンドレットまたは Azure CLI を使用して、Powershell を介して使用できます。

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="deploy-the-resource-manager-template-with-powershell"></a>PowerShell を使用した Resource Manager テンプレートのデプロイ
PowerShell を使用して、前の「[Azure Resource Manager テンプレート](#azure-resource-manager-template)」セクションで示したサンプル Resource Manager テンプレートをデプロイするには、次のコマンドを使用します。

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile sampleActivityLogAlert.json -TemplateParameterFile sampleActivityLogAlert.parameters.json
```

ここで、sampleActivityLogAlert.parameters.json には、アラート ルールの作成時に必要とされたパラメーターに指定された値が含まれます。

### <a name="use-activity-log-powershell-cmdlets"></a>アクティビティ ログの PowerShell コマンドレットを使用する

アクティビティ ログ アラートでは、専用の PowerShell コマンドレットを使用できます。

- [Set-AzActivityLogAlert](/powershell/module/az.monitor/set-azactivitylogalert):新しいアクティビティ ログ アラートを作成するか、既存のアクティビティ ログ アラートを更新します。
- [Get-AzActivityLogAlert](/powershell/module/az.monitor/get-azactivitylogalert):1 つ以上のアクティビティ ログ アラート リソースを取得します。
- [Enable-AzActivityLogAlert](/powershell/module/az.monitor/enable-azactivitylogalert):既存のアクティビティ ログ アラートを有効にし、そのタグを設定します。
- [Disable-AzActivityLogAlert](/powershell/module/az.monitor/disable-azactivitylogalert):既存のアクティビティ ログ アラートを無効にし、そのタグを設定します。
- [Remove-AzActivityLogAlert](/powershell/module/az.monitor/remove-azactivitylogalert):アクティビティ ログ アラートを削除します。

## <a name="azure-cli"></a>Azure CLI

アクティビティ ログ アラート ルールを管理する専用の Azure CLI コマンドが [az monitor activity-log alert](/cli/azure/monitor/activity-log/alert) セットの下に用意されています。

新しいアクティビティ ログ アラート ルールを作成するには、次のコマンドをこの順序で使用します。

1. [az monitor activity-log alert create](/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-create):新規のアクティビティ ログ アラート ルールのリソースを作成します。
1. [az monitor activity-log alert scope](/cli/azure/monitor/activity-log/alert/scope):作成したアクティビティ ログ アラート ルールのスコープを追加します。
1. [az monitor activity-log alert action-group](/cli/azure/monitor/activity-log/alert/action-group):アクティビティ ログ アラート ルールにアクション グループを追加します。

1 つのアクティビティ ログ アラート ルールのリソースを取得するには、Azure CLI コマンド [az monitor activity-log alert show](/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-show
) を使用します。 リソースグループ内のすべてのアクティビティ ログ アラート ルールのリソースを表示するには、[az monitor activity-log alert list](/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-list) を使用します。
アクティビティ ログ アラート ルールのリソースを削除するには、Azure CLI コマンド [az monitor activity-log alert delete](/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-delete) を使用します。

## <a name="next-steps"></a>次のステップ

- [アクティビティ ログの Webhook スキーマ](./activity-log-alerts-webhook.md)について学習します。
- [アクティビティ ログの概要](./activity-log-alerts.md)をお読みください。
- [アクション グループ](./action-groups.md)について学習します。  
- [サービス正常性の通知](../../service-health/service-notifications.md)について学習します。
