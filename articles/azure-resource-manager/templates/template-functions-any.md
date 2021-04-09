---
title: テンプレート関数 - any
description: 型を変換するために Bicep で使用できる any 関数について説明します。
ms.topic: conceptual
author: tfitzmac
ms.author: tomfitz
ms.service: azure-resource-manager
ms.subservice: templates
ms.date: 03/02/2021
ms.openlocfilehash: b0cb51c9a79d1100de7f1ef32fe326eddcdd6dcc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101743929"
---
# <a name="any-function-for-bicep"></a>Bicep の any 関数

Bicep では、Bicep 型システムの型エラーを解決するために `any()` という関数がサポートされています。 この関数は、指定した値の形式が、型システムで予期されるものと一致しない場合に使用します。 たとえば、プロパティに数値が必要なときに、`'0.5'` のように文字列として指定しなければならない場合があります。 `any()` 関数を使用すると、型システムによって報告されるエラーが抑制されます。

この関数は、Azure Resource Manager テンプレートのランタイムには存在しません。 これは Bicep によってのみ使用され、ビルド済みのテンプレートの JSON には生成されません。

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="any"></a>any

`any(value)`

任意のデータ型に対応した値を返します。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| value | はい | すべての型 | 対応する型に変換する値。 |

### <a name="return-value"></a>戻り値

任意のデータ型に対応する形式の値。

### <a name="examples"></a>例

次のテンプレート例では、`any()` 関数を使用して数値を文字列として指定する方法を示します。

```bicep
resource wpAci 'microsoft.containerInstance/containerGroups@2019-12-01' = {
  name: 'wordpress-containerinstance'
  location: location
  properties: {
    containers: [
      {
        name: 'wordpress'
        properties: {
          ...
          resources: {
            requests: {
              cpu: any('0.5')
              memoryInGB: any('0.7')
            }
          }
        }
      }
    ]
  }
}
```

この関数は、Bicep で代入されたすべての値に対して機能します。 次の例では、`any()` を三項式で引数として使用しています。  

```bicep
publicIPAddress: any((pipId == '') ? null : {
  id: pipId
})
```

## <a name="next-steps"></a>次のステップ

`any()` 関数のより複雑な使用方法については、次の例を参照してください。

* [特定の名前を必要とする子リソース](https://github.com/Azure/bicep/blob/main/docs/examples/201/api-management-create-all-resources/main.bicep#L246)
* [存在するがリソースの型で定義されないリソース プロパティ](https://github.com/Azure/bicep/blob/main/docs/examples/201/log-analytics-with-solutions-and-diagnostics/main.bicep#L26)

