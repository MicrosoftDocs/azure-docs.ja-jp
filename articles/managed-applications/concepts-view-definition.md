---
title: Azure Managed Applications のビュー定義の概要 | Microsoft Docs
description: Azure Managed Applications のビュー定義の作成の概念について説明します。
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 06/12/2019
ms.openlocfilehash: 6735787f9b43f98ab611584f3c7191c9f927dbc2
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2019
ms.locfileid: "67479056"
---
# <a name="view-definition-artifact-in-azure-managed-applications"></a>Azure Managed Applications のビュー定義アーティファクト

ビュー定義は Azure Managed Applications のオプションのアーティファクトです。 これを使用すると概要ページをカスタマイズし、メトリックやカスタム リソースなどのビューをさらに追加することができます。

この記事では、ビュー定義アーティファクトとその機能の概要について説明します。

## <a name="view-definition-artifact"></a>ビュー定義アーティファクト

ビュー定義アーティファクトの名前は **viewDefinition.json** とし、マネージド アプリケーション定義を作成する .zip パッケージ内で **createUiDefinition.json** および **mainTemplate.json** と同じレベルに配置する必要があります。 .zip パッケージを作成してマネージド アプリケーション定義を発行する方法については、「[Azure マネージド アプリケーションの定義を発行する](publish-managed-app-definition-quickstart.md)」を参照してください。

## <a name="view-definition-schema"></a>ビュー定義スキーマ

**viewDefinition.json** ファイルにあるのは、最上位レベルの `views` プロパティ 1 つのみです。これはビューの配列です。 各ビューはマネージド アプリケーションのユーザー インターフェイスに、目次の個別のメニュー項目として表示されます。 各ビューには、ビューの種類をセットする `kind` プロパティがあります。 次のいずれかの値を設定する必要があります: [Overview](#overview)、[Metrics](#metrics)、[CustomResources](#custom-resources)。 詳細については、現在の [viewDefinition.json の JSON スキーマ](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#)を参照してください。

ビュー定義のサンプルの JSON:

```json
{
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
                        "displayName": "Custom Test Action",
                        "path": "testAction"
                    },
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

## <a name="metrics"></a>メトリック

`"kind": "Metrics"`

メトリック ビューを使用すると、[Azure Monitor のメトリック](../azure-monitor/platform/data-platform-metrics.md)でマネージド アプリケーション リソースからデータを収集して集計することができます。

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
|metrics|はい|このグラフにプロットするメトリックの配列。 Azure portal でサポートされるメトリックの詳細については、「[Azure Monitor のサポートされるメトリック](../azure-monitor/platform/metrics-supported.md)」を参照してください。|

### <a name="metric"></a>メトリック

|プロパティ|必須|説明|
|---------|---------|---------|
|名前|はい|メトリックの名前。|
|aggregationType|はい|このメトリックに使用する集計の種類。 サポートされている集計の種類: `none, sum, min, max, avg, unique, percentile, count`|
|namespace|いいえ|適切なメトリックのプロバイダーを決定するときに使用する追加情報。|
|resourceTagFilter|いいえ|メトリックが表示されるリソース タグの配列 (単語 `or` で区切られます)。 リソースの種類のフィルターの上に適用されます。|
|resourceType|はい|メトリックが表示されるリソースの種類。|

## <a name="custom-resources"></a>カスタム リソース

`"kind": "CustomResources"`

この種類のビューは複数定義することができます。 各ビューは、**mainTemplate.json** で定義したカスタム プロバイダーからの**一意の**カスタム リソースの種類を表します。 カスタム プロバイダーの概要については、「[Azure Custom Providers プレビューの概要](custom-providers-overview.md)」を参照してください。

このビューでは、カスタム リソースの種類に対して GET、PUT、DELETE、POST 操作を実行できます。 POST 操作は、グローバルなカスタム アクションとすることも、カスタム リソースの種類に関連するカスタム アクションとすることもできます。

```json
{
    "kind": "CustomResources",
    "properties": {
        "displayName": "Test custom resource type",
        "version": "1.0.0",
        "resourceType": "testCustomResource",
        "createUIDefinition": { },
        "commands": [
            {
                "displayName": "Custom Test Action",
                "path": "testAction"
            },
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
|createUIDefinition|いいえ|カスタム リソースの作成コマンド用の UI 定義作成スキーマ。 UI 定義の作成の概要については、[CreateUiDefinition の基本概念](create-uidefinition-overview.md)に関する記事を参照してください。|
|commands|いいえ|CustomResources ビューの追加のツールバーのボタンの配列。「[commands](#commands)」を参照してください。|
|columns|いいえ|カスタム リソースの列の配列。 定義されていない場合、`name` 列が既定で表示されます。 この列には `"key"` と `"displayName"` を含める必要があります。 キーについては、ビューに表示するプロパティのキーを指定します。 入れ子になっている場合は、`"key": "name"` や `"key": "properties.property1"` のように、ドットを区切り記号として使用します。 表示名については、ビューに表示するプロパティの表示名を指定します。 `"optional"` プロパティを指定することもできます。 true に設定すると、列がビュー内で既定で非表示となります。|

## <a name="commands"></a>command

コマンドは、ページ上に表示される追加のツールバーのボタンの配列です。 各コマンドは、**mainTemplate.json** で定義された Azure Custom Provider からの POST アクションを示します。 カスタム プロバイダーの概要については、「[Azure Custom Providers プレビューの概要](custom-providers-overview.md)」を参照してください。

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
|icon|いいえ|コマンド ボタンのアイコン。 サポートされているアイコンの一覧は、[JSON スキーマ](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#)で定義されています。|
|createUIDefinition|いいえ|コマンド用の UI 定義作成スキーマ。 UI 定義の作成の概要については、「[CreateUiDefinition の基本概念](create-uidefinition-overview.md)」を参照してください。|

## <a name="next-steps"></a>次の手順

- マネージド アプリケーションの概要については、「[Azure マネージド アプリケーションの概要](overview.md)」を参照してください。
- カスタム プロバイダーの概要については、「[Azure Custom Providers プレビューの概要](custom-providers-overview.md)」を参照してください。
