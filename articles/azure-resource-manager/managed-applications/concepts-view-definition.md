---
title: ビュー定義の概要
description: Azure Managed Applications のビュー定義の作成の概念について説明します。
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 06/12/2019
ms.openlocfilehash: 7a3d2234a140d1fb2eede50e3fe2eef5575da648
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "81391688"
---
# <a name="view-definition-artifact-in-azure-managed-applications"></a>Azure Managed Applications のビュー定義アーティファクト

ビュー定義は Azure Managed Applications のオプションのアーティファクトです。 これを使用すると概要ページをカスタマイズし、メトリックやカスタム リソースなどのビューをさらに追加することができます。

この記事では、ビュー定義アーティファクトとその機能の概要について説明します。

## <a name="view-definition-artifact"></a>ビュー定義アーティファクト

ビュー定義アーティファクトの名前は **viewDefinition.json** とし、マネージド アプリケーション定義を作成する .zip パッケージ内で **createUiDefinition.json** および **mainTemplate.json** と同じレベルに配置する必要があります。 .zip パッケージを作成してマネージド アプリケーション定義を発行する方法については、「[Azure マネージド アプリケーションの定義を発行する](publish-service-catalog-app.md)」を参照してください。

## <a name="view-definition-schema"></a>ビュー定義スキーマ

**viewDefinition.json** ファイルにあるのは、最上位レベルの `views` プロパティ 1 つのみです。これはビューの配列です。 各ビューはマネージド アプリケーションのユーザー インターフェイスに、目次の個別のメニュー項目として表示されます。 各ビューには、ビューの種類をセットする `kind` プロパティがあります。 次のいずれかの値を設定する必要があります: [Overview](#overview)、[Metrics](#metrics)、[CustomResources](#custom-resources)、[Associations](#associations)。 詳細については、現在の [viewDefinition.json の JSON スキーマ](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#)を参照してください。

ビュー定義のサンプルの JSON:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#",
    "contentVersion": "0.0.0.1",
    "views": [
        {
            "kind": "Overview",
            "properties": {
                "header": "Welcome to your Azure Managed Application",
                "description": "This managed application is for demo purposes only.",
                "commands": [
                    {
                        "displayName": "Test Action",
                        "path": "testAction"
                    }
                ]
            }
        },
        {
            "kind": "Metrics",
            "properties": {
                "displayName": "This is my metrics view",
                "version": "1.0.0",
                "charts": [
                    {
                        "displayName": "Sample chart",
                        "chartType": "Bar",
                        "metrics": [
                            {
                                "name": "Availability",
                                "aggregationType": "avg",
                                "resourceTagFilter": [ "tag1" ],
                                "resourceType": "Microsoft.Storage/storageAccounts",
                                "namespace": "Microsoft.Storage/storageAccounts"
                            }
                        ]
                    }
                ]
            }
        },
        {
            "kind": "CustomResources",
            "properties": {
                "displayName": "Test custom resource type",
                "version": "1.0.0",
                "resourceType": "testCustomResource",
                "createUIDefinition": { },
                "commands": [
                    {
                        "displayName": "Custom Context Action",
                        "path": "testCustomResource/testContextAction",
                        "icon": "Stop",
                        "createUIDefinition": { }
                    }
                ],
                "columns": [
                    {"key": "name", "displayName": "Name"},
                    {"key": "properties.myProperty1", "displayName": "Property 1"},
                    {"key": "properties.myProperty2", "displayName": "Property 2", "optional": true}
                ]
            }
        },
        {
            "kind": "Associations",
            "properties": {
                "displayName": "Test association resource type",
                "version": "1.0.0",
                "targetResourceType": "Microsoft.Compute/virtualMachines",
                "createUIDefinition": { }
            }
        }
    ]
}
```

## <a name="overview"></a>概要

`"kind": "Overview"`

**viewDefinition.json** でこのビューを指定すると、マネージド アプリケーションの既定の概要ページがオーバーライドされます。

```json
{
    "kind": "Overview",
    "properties": {
        "header": "Welcome to your Azure Managed Application",
        "description": "This managed application is for demo purposes only.",
        "commands": [
            {
                "displayName": "Test Action",
                "path": "testAction"
            }
        ]
    }
}
```

|プロパティ|必須|説明|
|---------|---------|---------|
|header|いいえ|概要のヘッダー。|
|description|いいえ|マネージド アプリケーションの説明。|
|commands|いいえ|概要ページの追加のツールバーのボタンの配列。「[commands](#commands)」を参照してください。|

![概要](./media/view-definition/overview.png)

## <a name="metrics"></a>メトリック

`"kind": "Metrics"`

メトリック ビューを使用すると、[Azure Monitor のメトリック](../../azure-monitor/platform/data-platform-metrics.md)でマネージド アプリケーション リソースからデータを収集して集計することができます。

```json
{
    "kind": "Metrics",
    "properties": {
        "displayName": "This is my metrics view",
        "version": "1.0.0",
        "charts": [
            {
                "displayName": "Sample chart",
                "chartType": "Bar",
                "metrics": [
                    {
                        "name": "Availability",
                        "aggregationType": "avg",
                        "resourceTagFilter": [ "tag1" ],
                        "resourceType": "Microsoft.Storage/storageAccounts",
                        "namespace": "Microsoft.Storage/storageAccounts"
                    }
                ]
            }
        ]
    }
}
```

|プロパティ|必須|説明|
|---------|---------|---------|
|displayName|いいえ|ビューの表示タイトル。|
|version|いいえ|ビューのレンダリングに使用されるプラットフォームのバージョン。|
|charts|はい|メトリック ページのグラフの配列。|

### <a name="chart"></a>グラフ

|プロパティ|必須|説明|
|---------|---------|---------|
|displayName|はい|グラフの表示タイトル。|
|chartType|いいえ|このグラフに使用する視覚エフェクト。 既定では、折れ線グラフが使用されます。 サポートされるグラフの種類: `Bar, Line, Area, Scatter`。|
|metrics|はい|このグラフにプロットするメトリックの配列。 Azure portal でサポートされるメトリックの詳細については、「[Azure Monitor のサポートされるメトリック](../../azure-monitor/platform/metrics-supported.md)」を参照してください。|

### <a name="metric"></a>メトリック

|プロパティ|必須|説明|
|---------|---------|---------|
|name|はい|メトリックの名前。|
|aggregationType|はい|このメトリックに使用する集計の種類。 サポートされている集計の種類: `none, sum, min, max, avg, unique, percentile, count`|
|namespace|いいえ|適切なメトリックのプロバイダーを決定するときに使用する追加情報。|
|resourceTagFilter|いいえ|メトリックが表示されるリソース タグの配列 (単語 `or` で区切られます)。 リソースの種類のフィルターの上に適用されます。|
|resourceType|はい|メトリックが表示されるリソースの種類。|

![メトリック](./media/view-definition/metrics.png)

## <a name="custom-resources"></a>カスタム リソース

`"kind": "CustomResources"`

この種類のビューは複数定義することができます。 各ビューは、**mainTemplate.json** で定義したカスタム プロバイダーからの**一意の**カスタム リソースの種類を表します。 カスタム プロバイダーの概要については、「[Azure Custom Providers プレビューの概要](../custom-providers/overview.md)」を参照してください。

このビューでは、カスタム リソースの種類に対して GET、PUT、DELETE、POST 操作を実行できます。 POST 操作は、グローバルなカスタム アクションとすることも、カスタム リソースの種類に関連するカスタム アクションとすることもできます。

```json
{
    "kind": "CustomResources",
    "properties": {
        "displayName": "Test custom resource type",
        "version": "1.0.0",
        "resourceType": "testCustomResource",
        "icon": "Polychromatic.ResourceList",
        "createUIDefinition": { },
        "commands": [
            {
                "displayName": "Custom Context Action",
                "path": "testCustomResource/testContextAction",
                "icon": "Stop",
                "createUIDefinition": { },
            }
        ],
        "columns": [
            {"key": "name", "displayName": "Name"},
            {"key": "properties.myProperty1", "displayName": "Property 1"},
            {"key": "properties.myProperty2", "displayName": "Property 2", "optional": true}
        ]
    }
}
```

|プロパティ|必須|説明|
|---------|---------|---------|
|displayName|はい|ビューの表示タイトル。 タイトルは **viewDefinition.json**内の各 CustomResources ビューに対して**一意**である必要があります。|
|version|いいえ|ビューのレンダリングに使用されるプラットフォームのバージョン。|
|resourceType|はい|カスタム リソースの種類。 カスタム プロバイダーの**一意の**カスタム リソースの種類とする必要があります。|
|icon|いいえ|ビューのアイコン。 例のアイコンの一覧は、[JSON スキーマ](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#)で定義されています。|
|createUIDefinition|いいえ|カスタム リソースの作成コマンド用の UI 定義作成スキーマ。 UI 定義の作成の概要については、[CreateUiDefinition の基本概念](create-uidefinition-overview.md)に関する記事を参照してください。|
|commands|いいえ|CustomResources ビューの追加のツールバーのボタンの配列。「[commands](#commands)」を参照してください。|
|列|いいえ|カスタム リソースの列の配列。 定義されていない場合、`name` 列が既定で表示されます。 この列には `"key"` と `"displayName"` を含める必要があります。 キーについては、ビューに表示するプロパティのキーを指定します。 入れ子になっている場合は、`"key": "name"` や `"key": "properties.property1"` のように、ドットを区切り記号として使用します。 表示名については、ビューに表示するプロパティの表示名を指定します。 `"optional"` プロパティを指定することもできます。 true に設定すると、列がビュー内で既定で非表示となります。|

![CustomResources](./media/view-definition/customresources.png)

## <a name="commands"></a>コマンド

コマンドは、ページ上に表示される追加のツールバーのボタンの配列です。 各コマンドは、**mainTemplate.json** で定義された Azure Custom Provider からの POST アクションを示します。 カスタム プロバイダーの概要については、「[Azure Custom Providers プレビューの概要](../custom-providers/overview.md)」を参照してください。

```json
{
    "commands": [
        {
            "displayName": "Start Test Action",
            "path": "testAction",
            "icon": "Start",
            "createUIDefinition": { }
        },
    ]
}
```

|プロパティ|必須|説明|
|---------|---------|---------|
|displayName|はい|コマンド ボタンの表示名。|
|path|はい|カスタム プロバイダーのアクション名。 アクション名は **mainTemplate.json** で定義されている必要があります。|
|icon|いいえ|コマンド ボタンのアイコン。 例のアイコンの一覧は、[JSON スキーマ](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#)で定義されています。|
|createUIDefinition|いいえ|コマンド用の UI 定義作成スキーマ。 UI 定義の作成の概要については、「[CreateUiDefinition の基本概念](create-uidefinition-overview.md)」を参照してください。|

## <a name="associations"></a>Associations

`"kind": "Associations"`

この種類のビューは複数定義することができます。 このビューを使用すると、**mainTemplate.json** で定義したカスタム プロバイダーを使用して、既存のリソースをマネージド アプリケーションにリンクすることができます。 カスタム プロバイダーの概要については、「[Azure Custom Providers プレビューの概要](../custom-providers/overview.md)」を参照してください。

このビューでは、`targetResourceType` に基づいて既存の Azure リソースを拡張できます。 リソースを選択すると、**パブリック** カスタム プロバイダーに対するオンボード要求が作成され、これによりリソースに副作用を適用できます。 

```json
{
    "kind": "Associations",
    "properties": {
        "displayName": "Test association resource type",
        "version": "1.0.0",
        "targetResourceType": "Microsoft.Compute/virtualMachines",
        "createUIDefinition": { }
    }
}
```

|プロパティ|必須|説明|
|---------|---------|---------|
|displayName|はい|ビューの表示タイトル。 タイトルは **viewDefinition.json** 内の各 Associations ビューに対して**一意**である必要があります。|
|version|いいえ|ビューのレンダリングに使用されるプラットフォームのバージョン。|
|targetResourceType|はい|ターゲット リソースの種類。 これは、リソースのオンボードに表示されるリソースの種類です。|
|createUIDefinition|いいえ|関連付けリソース作成コマンド用の UI 定義スキーマを作成します。 UI 定義の作成の概要については、[CreateUiDefinition の基本概念](create-uidefinition-overview.md)に関する記事を参照してください。|

## <a name="looking-for-help"></a>ヘルプを探しています

Azure Managed Applications に関して不明な点がある場合には、[Stack Overflow](https://stackoverflow.com/questions/tagged/azure-managedapps) で質問してみてください。 同様の質問が既に質問され回答されているため、投稿する前にまず確認してください。 `azure-managedapps` タグを追加して早く応答を受け取りましょう。

## <a name="next-steps"></a>次のステップ

- マネージド アプリケーションの概要については、「[Azure マネージド アプリケーションの概要](overview.md)」を参照してください。
- カスタム プロバイダーの概要については、「[Azure Custom Providers プレビューの概要](../custom-providers/overview.md)」を参照してください。
- Azure カスタム プロバイダーを使用した Azure マネージド アプリケーションの作成については、[カスタム プロバイダー アクションとリソースの種類を備えたマネージド アプリケーションを作成する方法に関するチュートリアル](tutorial-create-managed-app-with-custom-provider.md)を参照してください。
