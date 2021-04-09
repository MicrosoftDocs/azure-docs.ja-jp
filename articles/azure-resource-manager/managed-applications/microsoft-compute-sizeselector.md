---
title: SizeSelector UI 要素
description: Azure Portal の Microsoft.Compute.SizeSelector UI 要素について説明します。 仮想マシンのサイズ選択に使用されます。
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: d6408f8c08694ae681d302ae35f5778894091733
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87063651"
---
# <a name="microsoftcomputesizeselector-ui-element"></a>Microsoft.Compute.SizeSelector UI 要素

1 つまたは複数の仮想マシン インスタンスのサイズを選択するコントロールです。

## <a name="ui-sample"></a>UI サンプル

要素定義からのデフォルト値を含むセレクターが表示されます。

![Microsoft.Compute.SizeSelector](./media/managed-application-elements/microsoft-compute-sizeselector.png)

コントロールを選択すると、使用可能なサイズのビューが拡大されて表示されます。

![拡大された Microsoft.Compute.SizeSelector](./media/managed-application-elements/microsoft-compute-sizeselector-expanded.png)

## <a name="schema"></a>スキーマ

```json
{
  "name": "element1",
  "type": "Microsoft.Compute.SizeSelector",
  "label": "Size",
  "toolTip": "",
  "recommendedSizes": [
    "Standard_D1",
    "Standard_D2",
    "Standard_D3"
  ],
  "constraints": {
    "allowedSizes": [],
    "excludedSizes": [],
    "numAvailabilityZonesRequired": 3,
    "zone": "3"
  },
  "options": {
    "hideDiskTypeFilter": false
  },
  "osPlatform": "Windows",
  "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "2012-R2-Datacenter"
  },
  "count": 2,
  "visible": true
}
```

## <a name="sample-output"></a>サンプル出力

```json
"Standard_D1"
```

## <a name="remarks"></a>解説

- `recommendedSizes` には、少なくとも 1 つのサイズが必要です。 最初の推奨サイズが既定値として使用されます。 使用可能なサイズの一覧は、推奨される状態で並べ替えられていません。 その列を選択すると、推奨される状態で並べ替えることができます。
- 推奨されたサイズが選択した場所で使用できない場合、そのサイズは自動的にスキップされます。 代わりに、次の推奨サイズが使用されます。
- `constraints.allowedSizes` と `constraints.excludedSizes` は両方ともオプションとして使用できますが、同時に使用することはできません。 使用可能なサイズの一覧は、[サブスクリプションの使用可能な仮想マシン サイズを一覧表示する操作](/rest/api/compute/virtualmachines/virtualmachines-list-sizes-region)を呼び出すことで決定できます。 `constraints.allowedSizes` で指定されていないサイズはすべて非表示となり、`constraints.excludedSizes` で指定されていないサイズはすべて表示されます。
- `osPlatform` は指定する必要があり、**Windows** と **Linux** のいずれかを使用できます。 これは、仮想マシンのハードウェア コストの決定に使用されます。
- `imageReference` はファーストパーティのイメージでは省略されますが、サードパーティのイメージでは指定されます。 これは、仮想マシンのソフトウェア コストの決定に使用されます。
- `count` は適切な乗数を要素に設定するために使用されます。 **2** などの静的な値のほか、他の要素からの `[steps('step1').vmCount]` などの動的な値もサポートします。 既定値は **1** です。
- `numAvailabilityZonesRequired` には、1、2、または 3 を指定できます。
- 既定では、`hideDiskTypeFilter` は **false** です。 ディスク の種類のフィルターを使用すると、ディスクの種類をすべて表示することも、SSD のみを表示することもできます。

## <a name="next-steps"></a>次のステップ

* UI 定義の作成の概要については、「[CreateUiDefinition の基本概念](create-uidefinition-overview.md)」を参照してください。
* UI 要素の共通プロパティの説明については、「[CreateUiDefinition の要素](create-uidefinition-elements.md)」を参照してください。
