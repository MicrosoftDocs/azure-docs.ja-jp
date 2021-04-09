---
title: UI 定義参照関数を作成する
description: Azure portal 用に他のオブジェクトを参照する UI 定義を作成するときに使用する機能について説明します。
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: ad21c5b34a58c35b2cef5e430be7cb8cd1296402
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87094697"
---
# <a name="createuidefinition-referencing-functions"></a>CreateUiDefinition 参照関数

CreateUiDefinition ファイルのプロパティまたはコンテキストの出力を参照するときに使用する関数です。

## <a name="basics"></a>basics

[基本](create-uidefinition-overview.md#basics)ステップで定義されている要素の出力値が返されます。 この関数へのパラメーターとして、要素の名前を渡します。

他のステップの要素の出力値を取得するには、[steps()](#steps) 関数を使用します。

次の例は、基本ステップの `clusterName` という名前の要素の出力を返します。

```json
"[basics('clusterName')]"
```

返される値は、取得される要素の型によって異なります。

## <a name="location"></a>location

基本ステップまたは現在のコンテキストで選択されている場所が返されます。

次の例では、`"westus"` のような値が返されます。

```json
"[location()]"
```

## <a name="resourcegroup"></a>resourceGroup

基本ステップまたは現在のコンテキストで選択されている resourceGroup に関する詳細が返されます。

次に例を示します。

```json
"[resourceGroup()]"
```

以下のプロパティが返されます。

```json
{
    "mode": "New" or "Existing",
    "name": "{resourceGroupName}",
    "location": "{resourceGroupLocation}"
}
```

ドット表記を指定して、任意の特定の値を取得できます。

```json
"[resourceGroup().name]"
```

## <a name="steps"></a>steps

指定されたステップの要素が返されます。 この関数へのパラメーターとして、ステップの名前を渡します。 返された要素から、特定のプロパティ値を取得できます。

基本ステップの要素の出力値を取得するには、[basics()](#basics) 関数を使用します。

次の例では、`vmParameters` という名前のステップが返されます。 そのステップにあるのは、`adminUsername` という名前の要素です。

```json
"[steps('vmParameters').adminUsername]"
```

## <a name="subscription"></a>subscription

基本ステップまたは現在のコンテキストで選択されているサブスクリプションのプロパティが返されます。

次に例を示します。

```json
"[subscription()]"
```

以下のプロパティが返されます。

```json
{
    "id": "/subscriptions/{subscription-id}",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}",
    "displayName": "{name-of-subscription}"
}
```

## <a name="next-steps"></a>次のステップ

* ポータル インターフェイスの開発の概要については、「[Azure マネージド アプリケーションの作成エクスペリエンスのための CreateUiDefinition.json](create-uidefinition-overview.md)」を参照してください。
