---
title: 既存の仮想ネットワークへのデプロイ
description: マネージド アプリケーションのユーザーが既存の仮想ネットワークを選択できるようにする方法について説明します。 仮想ネットワークは、マネージド アプリケーションの外部に配置できます。
author: tfitzmac
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: tomfitz
ms.openlocfilehash: fa5e59b96aada06c2dd486094d9be6a52c79e43e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "84260685"
---
# <a name="use-existing-virtual-network-with-azure-managed-applications"></a>Azure Managed Applications で既存の仮想ネットワークを使用する

この記事では、コンシューマーのサブスクリプション内の既存の仮想ネットワークと統合する Azure マネージド アプリケーションを定義する方法について説明します。 マネージド アプリケーションを使用すると、新しい仮想ネットワークを作成するか、既存の仮想ネットワークを使用するかを、コンシューマーに決定させることができます。 既存の仮想ネットワークは、マネージド リソース グループの外部に配置できます。

## <a name="main-template"></a>Main template

まず、**mainTemplate.json** ファイルを見てみましょう。 仮想マシンとそれに関連付けられているリソースをデプロイするためのテンプレート全体を次に示します。 後で、既存の仮想ネットワークの使用に関連するテンプレートの部分について詳しく調べます。

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/mainTemplate.json":::

仮想ネットワークが[条件付きでデプロイされている](../templates/conditional-resource-deployment.md)ことに注目してください。 コンシューマーは、新しい仮想ネットワークを作成するか、既存の仮想ネットワークを使用するかを示すパラメーター値を渡します。 コンシューマーが新しい仮想ネットワークを選択した場合は、リソースがデプロイされます。 それ以外の場合は、デプロイ時にそのリソースはスキップされます。

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/mainTemplate.json" range="111-132" highlight="2":::

仮想ネットワーク ID の変数には、2 つのプロパティがあります。 1 つのプロパティは、新しい仮想ネットワークがデプロイされるときにリソース ID を返します。 もう 1 つのプロパティは、既存の仮想ネットワークが使用されるときにリソース ID を返します。 既存の仮想ネットワークのリソース ID には、仮想ネットワークを含むリソース グループの名前が含まれます。

サブネット ID は、仮想ネットワーク ID の値から構成されます。 これには、コンシューマーの選択に一致する値が使用されます。

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/mainTemplate.json" range="98-109" highlight="6-10":::

ネットワーク インターフェイスは、サブネット ID 変数に設定されます。

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/mainTemplate.json" range="142-163" highlight="16":::

## <a name="ui-definition"></a>UI 定義

次に、**createUiDefinition.json** ファイルを見てみましょう。 ファイル全体は次のとおりです。

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/createUiDefinition.json":::

このファイルには、仮想ネットワーク要素が含まれています。

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/createUiDefinition.json" range="53-81":::

この要素により、コンシューマーは新規または既存の仮想ネットワークのいずれかを選択できます。

:::image type="content" source="./media/existing-vnet-integration/new-or-existing-vnet.png" alt-text="新規または既存の仮想ネットワーク":::

出力には、コンシューマーが新規または既存の仮想ネットワークを選択したかどうかを示す値を含めます。 マネージド ID 値もあります。

> [!NOTE]
> マネージド ID の出力値には、**managedIdentity** という名前を指定する必要があります。

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/createUiDefinition.json" range="136-148" highlight="6,12":::

## <a name="next-steps"></a>次のステップ

UI 定義ファイルの作成の詳細については、「[Azure マネージド アプリケーションの作成エクスペリエンスのための CreateUiDefinition.json](create-uidefinition-overview.md)」を参照してください。
