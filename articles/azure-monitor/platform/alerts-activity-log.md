---
title: Azure Monitor でアクティビティ ログ アラートを作成、表示、管理する
description: Azure portal、Azure Resource Manager テンプレート、または Azure PowerShell を使用してアクティビティ ログ アラートを作成する方法。
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: vinagara
ms.openlocfilehash: 8183c7070b5d42e1c7a96fc0d64974658b2ec7d0
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448921"
---
# <a name="create-view-and-manage-activity-log-alerts-using-azure-monitor"></a>Azure Monitor を使用してアクティビティ ログ アラートを作成、表示、管理する  

## <a name="overview"></a>概要
アクティビティ ログ アラートは、アラートで指定した条件と一致する新しいアクティビティ ログ イベントが発生したときにアクティブになるアラートです。

これらのアラートは Azure リソースに対応しており、Azure Resource Manager テンプレートを使用して作成できます。 これらは、Azure Portal で作成、更新、削除することもできます。 通常は、Azure サブスクリプション内のリソースに特定の変更が発生した場合に通知を受け取るためにアクティビティ ログ アラートを作成します。多くの場合、特定のリソース グループまたはリソースを対象とします。 たとえば、**myProductionResourceGroup** (サンプル リソース グループ) 内の任意の仮想マシンが削除されたときに通知を受け取りたい場合やサブスクリプション内のユーザーに新しいロールが割り当てられた場合に通知を受け取ることができます。

> [!IMPORTANT]
> サービス正常性通知に対するアラートは、アクティビティ ログ アラート作成用のインターフェイスでは作成できません。 サービス正常性通知の作成および使用について詳しくは、[サービス正常性通知のアクティビティ ログ アラートの受け取り](alerts-activity-log-service-notifications.md)に関する記事をご覧ください。

アラート ルールを作成する際は、次のことを確認してください。

- スコープ内のサブスクリプションが、アラートが作成されているサブスクリプションと同じである。
- 条件が、アラートが構成されているレベル/状態/呼び出し元/リソース グループ/リソース ID/リソースの種類/イベント カテゴリである必要があります。
- アラートの構成 JSON に "anyOf" 条件または入れ子になった条件がない (基本的に許可されている allOf は 1 つだけで、それ以上 allOf/anyOf は含まれません)。
- カテゴリが "管理" のとき。 アラートでは、上記の条件の少なくとも 1 つを指定する必要があります。 アクティビティ ログ内にイベントが作成されるたびにアクティブ化するアラートを作成することはできません。


## <a name="azure-portal"></a>Azure ポータル

Azure portal を使用して、ユーザーはアクティビティ ログ アラート ルールを作成および変更できます。 また、このエクスペリエンスは Azure アクティビティ ログに統合され、対象とする特定のイベントに対するシームレスなアラート作成が保証されます。

### <a name="create-with-azure-portal"></a>Azure Portal での作成

次の手順に従います。

1. Azure Portal で、 **[モニター]**  >  **[アラート]** の順に選択します。
2. **[アラート]** ウィンドウの上部にある **[新しいアラート ルール]** をクリックします。

     ![新しいアラート ルール](media/alerts-activity-log/AlertsPreviewOption.png)

     **[ルールの作成]** ウィンドウが表示されます。

      ![新しいアラート ルールのオプション](media/alerts-activity-log/create-new-alert-rule-options.png)

3. **[アラートの条件を定義します]** で、次の情報を入力し、 **[完了]** をクリックします。

   - **アラートの対象:** 新しいアラートの対象を表示および選択するには、 **[サブスクリプション別でフィルター]**  /  **[リソースの種類でフィルター]** を使用し、表示された一覧からリソースまたはリソース グループを選択します。

     > [!NOTE]
     > 
     > アクティビティ ログ シグナルについてリソース、リソース グループ、またはサブスクリプション全体を選択できます。

     **アラート対象のサンプル ビュー**
     ![対象の選択](media/alerts-activity-log/select-target.png)

   - **[対象の条件]** の下で **[条件の追加]** をクリックすると、対象で使用可能なすべてのシグナルが表示されます。これには、さまざまなカテゴリの**アクティビティ ログ**のものも含まれ、カテゴリ名は **[サービスの監視]** 名に付加されます。

   - **[アクティビティ ログ]** という種類について使用可能なさまざまな操作の一覧からシグナルを選択します。

     ログ履歴のタイムラインと、この対象シグナルの対応するアラート ロジックを選択できます。

     **[条件の追加] 画面**

     ![条件の追加](media/alerts-activity-log/add-criteria.png)

     **履歴の時間:** 選択した操作で利用できるイベントをプロットできるのは、過去 6 時間/12 時間/24 時間または過去 1 週間です。

     **[アラート ロジック]** :

     - **[イベント レベル]** - イベントの重大度レベル。 "_詳細_"、"_情報_"、"_警告_"、"_エラー_" または "_重大_"。
     - **[状態]** :イベントの状態。 "_開始済み_"、"_失敗_" または "_成功_"。
     - **[イベント開始者]** :呼び出し元とも呼ばれます。操作を実行するユーザーのメール アドレスまたは Azure Active Directory 識別子です。

       アラート ロジックが適用されたシグナル グラフのサンプル:

       ![ 選択済み条件](media/alerts-activity-log/criteria-selected.png)

4. **[アラートの詳細を定義します]** で、以下の詳細を入力します。

    - **[アラート ルール名]** - 新しいアラート ルールの名前
    - **[説明]** - 新しいアラート ルールの説明
    - **[リソース グループにアラートを保存します]** - この新しいルールを保存するリソース グループを選択します。

5. **[アクション グループ]** のドロップダウン メニューから、この新しいアラート ルールに割り当てるアクション グループを指定します。 または、[新しいアクション グループを作成](../../azure-monitor/platform/action-groups.md)し、新しいルールに割り当てます。 新しいグループを作成するには、 **[+ 新しいグループ]** をクリックします。

6. ルールを作成した後に有効にするには、 **[ルールの作成時に有効にする]** オプションの **[はい]** をクリックします。
7. **[アラート ルールの作成]** をクリックします。

    アクティビティ ログの新しいアラート ルールが作成され、ウィンドウの右上に確認メッセージが表示されます。

    ルールは、有効化、無効化、編集、または削除することができます。 アクティビティ ログ ルールの管理の詳細を確認してください。


あるいは、アクティビティ ログに対してアラート ルールを作成する状況を簡単にたとえるとすれば、[Azure portal のアクティビティ ログ](activity-log-view.md#azure-portal)でイベントを探索したりフィルター処理したりするのに似ています。 Azure Monitor のアクティビティ ログでは、必要なイベントを検索またはフィルター処理し、次に **[アクティビティ ログ アラートの追加]** ボタンを使用ことでアラートを作成できます。その後、上記のチュートリアルで説明した 4 つの手順に従います。
    
 ![ アクティビティ ログからのアラートの追加](media/alerts-activity-log/add-activity-log.png)
    

### <a name="view-and-manage-in-azure-portal"></a>Azure portal での表示と管理

1. Azure Portal で、 **[モニター]**  >  **[アラート]** の順にクリックし、ウィンドウの左上にある **[ルールの管理]** をクリックします。

    ![ manage alert rules](media/alerts-activity-log/manage-alert-rules.png)

    使用可能なルールの一覧が表示されます。

2. 変更するアクティビティ ログ ルールを検索します。

    ![ アクティビティ ログ アラート ルールの検索](media/alerts-activity-log/searth-activity-log-rule-to-edit.png)

    編集するアクティビティのルールを検索するには、用意されているフィルターとして _[サブスクリプション]_ 、 _[リソース グループ]_ 、 _[リソース]_ 、 _[シグナルの種類]_ または _[状態]_ を使用できます。

   > [!NOTE]
   > 
   > 編集できるのは、 **[説明]** 、 **[対象の条件]** 、 **[アクション グループ]** のみです。

3. ルールを選択し、ダブルクリックしてルールのオプションを編集します。 必要な変更を行い、 **[保存]** をクリックします。

   ![ manage alert rules](media/alerts-activity-log/activity-log-rule-edit-page.png)

4. ルールは、無効化、有効化、または削除することができます。 手順 2 で説明したようにルールを選択した後、ウィンドウの上部にある適切なオプションを選択します。


## <a name="azure-resource-template"></a>Azure リソース テンプレート
Resource Manager テンプレートを使用してアクティビティ ログ アラートを作成するには、`microsoft.insights/activityLogAlerts` 型のリソースを作成します。 次に、関連するすべてのプロパティを入力します。 アクティビティ ログ アラートを作成するテンプレートを以下に示します。

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
上記のサンプル JSON は、このチュートリアルの目的で (たとえば) sampleActivityLogAlert.json として保存でき、[Azure portal で Azure Resource Manager](../../azure-resource-manager/resource-group-template-deploy-portal.md) を使用してデプロイすることができます。

> [!NOTE]
> 新しいアクティビティ ログ アラート ルールがアクティブになるまで最大 5 分かかる場合があります。

## <a name="rest-api"></a>REST API 
[Azure Monitor - Activity Log Alerts API](https://docs.microsoft.com/rest/api/monitor/activitylogalerts) は REST API であり、Azure Resource Manager REST API との完全な互換性があります。 そのためこれは、Resource Manager コマンドレットだけでなく Azure CLI を使用して、Powershell を介して使用できます。

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="deploy-resource-manager-template-with-powershell"></a>PowerShell を使用して Resource Manager テンプレートをデプロイする
PowerShell を使用して、前のリソース テンプレートのセクションで示したサンプル リソース テンプレートをデプロイするには、次のコマンドを使用します。

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile sampleActivityLogAlert.json -TemplateParameterFile sampleActivityLogAlert.parameters.json
```

ここで、sampleActivityLogAlert.parameters.json には、アラート ルールの作成時に必要とされたパラメーターに指定された値が含まれます。

### <a name="use-activity-log-powershell-cmdlets"></a>アクティビティ ログの PowerShell コマンドレットを使用する

アクティビティ ログ アラートでは、専用の PowerShell コマンドレットを使用できます。

- [Set-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Set-AzActivityLogAlert) :新しいアクティビティ ログ アラートを作成するか、既存のアクティビティ ログ アラートを有効にします。
- [Get-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Get-AzActivityLogAlert) :1 つ以上のアクティビティ ログ アラート リソースを取得します。
- [Enable-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Enable-AzActivityLogAlert) :既存のアクティビティ ログ アラートを有効にし、そのタグを設定します。
- [Disable-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Disable-AzActivityLogAlert) :既存のアクティビティ ログ アラートを無効にし、そのタグを設定します。
- [Remove-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Remove-AzActivityLogAlert)    :アクティビティ ログ アラートを削除します。

## <a name="cli"></a>CLI

アクティビティ ログ アラート ルールを管理する専用の Azure CLI コマンドが [az monitor activity-log alert](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert) セットの下に用意されています。

新しいアクティビティ ログ アラート ルールを作成するには、次のコマンドを次の順序で使用します。

1. [az monitor activity-log alert create](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-create):新規のアクティビティ ログ アラート ルールのリソースを作成します。
1. [az monitor activity-log alert scope](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/scope):作成したアクティビティ ログ アラート ルールのスコープを追加します。
1. [az monitor activity-log alert action-group](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/action-group):アクティビティ ログ アラート ルールにアクション グループを追加します。

1 つのアクティビティ ログ アラート ルールのリソースを取得するには、Azure CLI コマンド [az monitor activity-log alert show](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-show
) を使用します。 リソース グループ内のすべてのアクティビティ ログ アラート ルール のリソースを表示する場合は、[az monitor activity-log alert list](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-list) を使用します。
アクティビティ ログ アラート ルールのリソースを削除するには、Azure CLI コマンド [az monitor activity-log alert delete](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-delete) を使用します。

## <a name="next-steps"></a>次の手順

- [アクティビティ ログの Webhook スキーマ](../../azure-monitor/platform/activity-log-alerts-webhook.md)
- [アクティビティ ログの概要](../../azure-monitor/platform/activity-log-alerts.md) 
- [アクション グループ](../../azure-monitor/platform/action-groups.md)について学習します。  
- [サービス正常性の通知](../../azure-monitor/platform/service-notifications.md)について学習します。
