---
title: Azure Monitor ブックと Azure Resource Manager テンプレート
description: Azure Resource Manager テンプレートを介してデプロイされた作成済みのブックやパラメーター化されたカスタム Azure Monitor ブックを使用して、複雑なレポート作成を簡素化します
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: 77190b85da08d09cf05a02dcc5787f0c24229948
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100603955"
---
# <a name="programmatically-manage-workbooks"></a>プログラムでブックを管理する

リソース所有者は、Resource Manager テンプレートを使用して、プログラムでブックを作成および管理することができます。

これは、次のようなシナリオで役に立ちます。
* リソースのデプロイと共に、組織またはドメインに固有の分析レポートをデプロイする。 たとえば、組織に固有のパフォーマンスおよび障害ブックを新しいアプリまたは仮想マシン用にデプロイできます。
* 既存のリソース用のブックを使用して、標準レポートまたはダッシュボードをデプロイする。

ブックは、Resource Manager テンプレートで指定されたコンテンツと共に、ご希望の (サブ) リソース グループに作成されます。

プログラムで管理できるブック リソースには、次の 2 種類があります。
* [ブック テンプレート](#azure-resource-manager-template-for-deploying-a-workbook-template)
* [ブック インスタンス](#azure-resource-manager-template-for-deploying-a-workbook-instance)

## <a name="azure-resource-manager-template-for-deploying-a-workbook-template"></a>ブック テンプレートをデプロイするための Azure Resource Manager テンプレート

1. プログラムでデプロイするブックを開きます。
2. _[編集]_ ツール バー項目をクリックして、ブックを編集モードに切り替えます。
3. ツール バーの _[</>]_ ボタンを使用して "_詳細エディター_" を開きます。
4. _[ギャラリー テンプレート]_ タブが確実に表示されている状態にします。

    ![[ギャラリー テンプレート] タブ](./media/workbooks-automate/gallery-template.png)
1. ギャラリー テンプレート内の JSON をクリップボードにコピーします。
2. Azure Monitor ブック ギャラリーにブック テンプレートをデプロイするサンプル Azure Resource Manager テンプレートを以下に示します。 `<PASTE-COPIED-WORKBOOK_TEMPLATE_HERE>` の代わりにコピーした JSON を貼り付けます。 ブック テンプレートを作成するリファレンス Azure Resource Manager テンプレートについては、[こちら](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Documentation/ARM-template-for-creating-workbook-template)にあります。

    ```json
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "resourceName": {
                "type": "string",
                "defaultValue": "my-workbook-template",
                "metadata": {
                    "description": "The unique name for this workbook template instance"
                }
            }
        },
        "resources": [
            {
                "name": "[parameters('resourceName')]",
                "type": "microsoft.insights/workbooktemplates",
                "location": "[resourceGroup().location]",
                "apiVersion": "2019-10-17-preview",
                "dependsOn": [],
                "properties": {
                    "galleries": [
                        {
                            "name": "A Workbook Template",
                            "category": "Deployed Templates",
                            "order": 100,
                            "type": "workbook",
                            "resourceType": "Azure Monitor"
                        }
                    ],
                    "templateData": <PASTE-COPIED-WORKBOOK_TEMPLATE_HERE>
                }
            }
        ]
    }
    ```
1. `galleries` オブジェクトで、`name` および `category` キーに、使用する値を入力します。 [パラメーター](#parameters)の詳細については、次のセクションを参照してください。
2. この Azure Resource Manager テンプレートは、[Azure portal](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)、[コマンド ライン インターフェイス](../../azure-resource-manager/templates/deploy-cli.md)、[PowerShell](../../azure-resource-manager/templates/deploy-powershell.md) などを使用してデプロイします。
3. Azure portal を開き、Azure Resource Manager テンプレートで選択したブック ギャラリーに移動します。 テンプレートの例で、Azure Monitor ブック ギャラリーに移動します。
    1. Azure portal を開き、Azure Monitor に移動します。
    2. 目次から `Workbooks` を開く
    3. ギャラリー内のカテゴリ `Deployed Templates` (紫色の項目の中の 1 つ) で使用するテンプレートを検索します。

### <a name="parameters"></a>パラメーター

|パラメーター                |説明                                                                                             |
|:-------------------------|:-------------------------------------------------------------------------------------------------------|
| `name`                   | Azure Resource Manager 内のブック テンプレート リソースの名前。                                  |
|`type`                    | 常に microsoft.insights/workbooktemplates                                                            |
| `location`               | ブックが作成される Azure の場所。                                               |
| `apiVersion`             | 2019-10-17 プレビュー                                                                                     |
| `type`                   | 常に microsoft.insights/workbooktemplates                                                            |
| `galleries`              | このブック テンプレートが表示されるギャラリー セット。                                                |
| `gallery.name`           | ギャラリー内のブック テンプレートのフレンドリ名。                                             |
| `gallery.category`       | テンプレートを配置するギャラリー内のグループ。                                                     |
| `gallery.order`          | ギャラリー内のカテゴリにおいてテンプレートの表示順序を決定する数値。 順位が低いほど優先順位が高くなります。 |
| `gallery.resourceType`   | ギャラリーに対応するリソースの種類。 これは、通常、リソースに対応するリソースの種類の文字列です (例: microsoft.operationalinsights/workspaces)。 |
|`gallery.type`            | ブックの種類と呼ばれます。これは、リソースの種類においてギャラリーを区別する一意のキーです。 たとえば、Application Insights には、さまざまなブック ギャラリーに対応する種類として `workbook` と `tsg` があります。 |

### <a name="galleries"></a>ギャラリー

| [ギャラリー]                                        | リソースの種類                                      | ブックの種類 |
| :--------------------------------------------- |:---------------------------------------------------|:--------------|
| Azure Monitor での Workbooks                     | `Azure Monitor`                                    | `workbook`    |
| Azure Monitor での VM Insights                   | `Azure Monitor`                                    | `vm-insights` |
| Log Analytics ワークスペースでの Workbooks           | `microsoft.operationalinsights/workspaces`         | `workbook`    |
| Application Insights での Workbooks              | `microsoft.insights/component`                     | `workbook`    |
| Application Insights のトラブルシューティング ガイド | `microsoft.insights/component`                     | `tsg`         |
| Application Insights の使用                  | `microsoft.insights/component`                     | `usage`       |
| Kubernetes サービスでの Workbooks                | `Microsoft.ContainerService/managedClusters`       | `workbook`    |
| リソース グループでの Workbooks                   | `microsoft.resources/subscriptions/resourcegroups` | `workbook`    |
| Azure Active Directory での Workbooks            | `microsoft.aadiam/tenant`                          | `workbook`    |
| Virtual Machines での VM Insights                | `microsoft.compute/virtualmachines`                | `insights`    |
| 仮想マシン スケール セット内の VM Insights      | `microsoft.compute/virtualmachinescalesets`        | `insights`    |

## <a name="azure-resource-manager-template-for-deploying-a-workbook-instance"></a>ブック インスタンスをデプロイするための Azure Resource Manager テンプレート

1. プログラムでデプロイするブックを開きます。
2. _[編集]_ ツール バー項目をクリックして、ブックを編集モードに切り替えます。
3. ツール バーの _[</>]_ ボタンを使用して "_詳細エディター_" を開きます。
4. エディターで、 _[テンプレートの種類]_ を _[Resource Manager テンプレート]_ に切り替えます。
5. 作成用の Resource Manager テンプレートがエディターに表示されます。 内容をコピーしてそのまま使用するか、ターゲット リソースもデプロイする、より大きなテンプレートとマージします。

    ![ブック UI 内から Resource Manager テンプレートを取得する方法を示す画像](./media/workbooks-automate/programmatic-template.png)

## <a name="sample-azure-resource-manager-template"></a>サンプルの Azure Resource Manager テンプレート
このテンプレートは、"Hello World!" を表示する単純なブックをデプロイする方法を示しています
```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workbookDisplayName":  {             
            "type":"string",
            "defaultValue": "My Workbook",
            "metadata": {
                "description": "The friendly name for the workbook that is used in the Gallery or Saved List. Needs to be unique in the scope of the resource group and source" 
            }
        },
        "workbookType":  {             
            "type":"string",
            "defaultValue": "tsg",
            "metadata": {
                "description": "The gallery that the workbook will been shown under. Supported values include workbook, `tsg`, Azure Monitor, etc." 
            }
        },
        "workbookSourceId":  {             
            "type":"string",
            "defaultValue": "<insert-your-resource-id-here>",
            "metadata": {
                "description": "The id of resource instance to which the workbook will be associated" 
            }
        },
        "workbookId": {
            "type":"string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "The unique guid for this workbook instance" 
            }
        }
    },    
    "resources": [
        {
            "name": "[parameters('workbookId')]",
            "type": "Microsoft.Insights/workbooks",
            "location": "[resourceGroup().location]",
            "kind": "shared",
            "apiVersion": "2018-06-17-preview",
            "dependsOn": [],
            "properties": {
                "displayName": "[parameters('workbookDisplayName')]",
                "serializedData": "{\"version\":\"Notebook/1.0\",\"items\":[{\"type\":1,\"content\":\"{\\\"json\\\":\\\"Hello World!\\\"}\",\"conditionalVisibility\":null}],\"isLocked\":false}",
                "version": "1.0",
                "sourceId": "[parameters('workbookSourceId')]",
                "category": "[parameters('workbookType')]"
            }
        }
    ],
    "outputs": {
        "workbookId": {
            "type": "string",
            "value": "[resourceId( 'Microsoft.Insights/workbooks', parameters('workbookId'))]"
        }
    }
}
```

### <a name="template-parameters"></a>Template parameters

| パラメーター | 説明 |
| :------------- |:-------------|
| `workbookDisplayName` | ギャラリーまたは保存リストで使用されているブックのフレンドリ名。 リソース グループとソースのスコープ内で一意である必要があります |
| `workbookType` | ブックが表示されるギャラリー。 サポートされる値には、workbook、`tsg`、Azure Monitor などがあります。 |
| `workbookSourceId` | ブックを関連付けるリソース インスタンスの ID。 新しいブックは、このリソース インスタンスに関連して表示されます (たとえば、 _[ブック]_ の下にあるリソースの目次内)。 自分のブックを Azure Monitor のブック ギャラリーに表示したい場合は、リソース ID ではなく文字列 _Azure Monitor_ を使用します。 |
| `workbookId` | このブック インスタンスの一意の GUID。 新しい GUID を自動的に作成するには、 _[newGuid()]_ を使用します。 |
| `kind` | 作成されるブックを共有とプライベートのいずれにするかを指定するために使用します。 共有ブックには値 _shared_ を使用し、プライベート ブックには _user_ を使用します。 |
| `location` | ブックの作成先となる Azure の場所。 リソース グループと同じ場所に作成するには、 _[resourceGroup().location]_ を使用します |
| `serializedData` | ブックで使用されるコンテンツまたはペイロードが格納されます。 ブック UI で Resource Manager テンプレートを使用して値を取得します |

### <a name="workbook-types"></a>ブックの種類
ブックの種類では、新しいブック インスタンスが表示されるブック ギャラリーの種類を指定します。 次のオプションがあります。

| Type | ギャラリーの場所 |
| :------------- |:-------------|
| `workbook` | Application Insights、Azure Monitor などのブック ギャラリーを含む、ほとんどのレポートで使用される既定値です。  |
| `tsg` | Application Insights の [トラブルシューティング ガイド] ギャラリー |
| `usage` | Application Insights の _[使用法]_ の下にある _[その他]_ ギャラリー |

### <a name="limitations"></a>制限事項
技術的な理由により、このメカニズムを使用して Application Insights の _[ブック]_ ギャラリーにブック インスタンスを作成することはできません。 この制限については、解決に取り組んでいる最中です。 その間は、[トラブルシューティング ガイド] ギャラリー (workbookType: `tsg`) を使用して Application Insights に関連するブックをデプロイすることをお勧めします。

## <a name="next-steps"></a>次のステップ

新しい [Azure Monitor for Storage エクスペリエンス](../insights/storage-insights-overview.md)を強化するためにブックがどのように使用されているかを確認してください。
