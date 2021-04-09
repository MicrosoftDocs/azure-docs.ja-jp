---
title: JSON と Bicep の間でテンプレートを変換する
description: Azure Resource Manager テンプレートを Bicep から JSON に、また JSON から Bicep に変換するためのコマンドについて説明します。
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: 6d242f5846996cd0f5b9510a1a2b9f2bf063a0c7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103422126"
---
# <a name="converting-arm-templates-between-json-and-bicep"></a>JSON と Bicep 間での ARM テンプレートの変換

この記事では、Azure Resource Manager テンプレート (ARM テンプレート) を JSON から Bicep に、また Bicep から JSON に変換する方法について説明します。

変換コマンドを実行するには、[Bicep CLI がインストール](bicep-install.md)されている必要があります。

変換コマンドにより、機能的に同等のテンプレートが生成されます。 ただし、実装がまったく同じであるとは限りません。 テンプレートを JSON から Bicep に変換した後、JSON に再変換すると、元のテンプレートとは構文が異なったテンプレートになる可能性があります。 デプロイすると、変換されたテンプレートは同じ結果を生成します。

## <a name="convert-from-json-to-bicep"></a>JSON から Bicep に変換する

Bicep CLI には、既存の JSON テンプレートを Bicep ファイルに逆コンパイルするコマンドが用意されています。 JSON ファイルをデコンパイルするには、次のように使用します。

```azurecli
bicep decompile mainTemplate.json
```

このコマンドは、Bicep 作成の開始点として使用できます。 このコマンドは、すべてのテンプレートで機能するわけではありません。 現在、入れ子になったテンプレートは、'inner' 式の評価スコープを使用している場合にのみ逆コンパイルできます。 コピー ループを使用するテンプレートは逆コンパイルできません。

## <a name="convert-from-bicep-to-json"></a>Bicep から JSON に変換する

Bicep CLI には、Bicep を JSON に変換するコマンドも用意されています。 JSON ファイルをビルドするには、次のように使用します。

```azurecli
bicep build mainTemplate.bicep
```

## <a name="export-template-and-convert"></a>テンプレートをエクスポートして変換する

リソース グループのテンプレートをエクスポートし、それを decompile コマンドに直接渡すことができます。 次の例は、エクスポートされたテンプレートを逆コンパイルする方法を示しています。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group export --name "your_resource_group_name" > main.json
bicep decompile main.json
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Export-AzResourceGroup -ResourceGroupName "your_resource_group_name" -Path ./main.json
bicep decompile main.json
```

# <a name="portal"></a>[ポータル](#tab/azure-portal)

ポータルを使用して[テンプレートをエクスポート](export-template-portal.md)します。

ダウンロードしたファイルに対して `bicep decompile <filename>` を使用します。

---

## <a name="side-by-side-view"></a>横に並べて表示

[Bicep Playground](https://aka.ms/bicepdemo) を使用すると、対応する JSON と Bicep のファイルを横並びで表示できます。 サンプル テンプレートを選択すると、両方のバージョンを表示できます。 または、`Decompile` を選択して、独自の JSON テンプレートをアップロードし、同等の Bicep ファイルを表示します。

## <a name="next-steps"></a>次のステップ

Bicep の詳細については、[Bicep のチュートリアル](./bicep-tutorial-create-first-bicep.md)を参照してください。
