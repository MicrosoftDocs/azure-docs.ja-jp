---
title: デバイスのオンボード
description: Azure カスタム プロバイダーを使用して、他の Azure リソースの種類に管理または構成を適用することで、リソースのオンボードを実行する方法について説明します。
author: jjbfour
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: jobreen
ms.openlocfilehash: 1846b036f12fe7e691021ec0248782cad946d9b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "75649183"
---
# <a name="azure-custom-providers-resource-onboarding-overview"></a>Azure カスタム プロバイダー リソースのオンボードの概要

Azure カスタム プロバイダー リソースのオンボードは、Azure リソースの種類の拡張モデルです。 これにより、既存の Azure リソースに対して大規模に操作や管理を適用できます。 詳細については、[Azure カスタム プロバイダーが Azure を拡張する方法](overview.md)に関するページを参照してください。 この記事では、次の内容について説明します。

- リソースのオンボードでできること。
- リソースのオンボードの基本事項および使用方法。
- 作業を開始するためのガイドとコード サンプルを見つけられる場所

> [!IMPORTANT]
> 現在、Custom Providers はパブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されており、運用環境のワークロードに使用することは推奨されません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="what-can-resource-onboarding-do"></a>リソースのオンボードでできること

[Azure カスタム プロバイダーのカスタム リソース](./custom-providers-resources-endpoint-how-to.md)と同様に、リソースのオンボードはエンドポイントへの "オンボード" 要求をプロキシするコントラクトを定義します。 カスタム リソースとは異なり、リソースのオンボードでは新しいリソースの種類は作成されません。 代わりに、既存のリソースの種類を拡張することができます。 また、リソースのオンボードは Azure Policy と連携するため、リソースの管理と構成を大規模に行うことができます。 リソースのオンボードのワークフローの例を次に示します。

- 仮想マシン拡張機能に対してインストールおよび管理を行う。
- Azure ストレージ アカウントの既定値をアップロードして構成する。
- ベースライン診断設定を大規模に有効にする。

## <a name="resource-onboarding-basics"></a>リソースのオンボードの基本事項

Azure カスタム プロバイダーを使用してリソースのオンボードを構成するには、Microsoft.CustomProviders/resourceProviders および Microsoft.CustomProviders/associations のリソースの種類を使用します。 カスタム プロバイダーのリソースのオンボードを有効にするには、構成プロセス中に、"Extension" を含む **routingType** で、"associations" という **resourceType** を作成します。 Microsoft.CustomProviders/associations と Microsoft.CustomProviders/resourceProviders は、同じリソース グループに属している必要はありません。

Azure カスタム プロバイダーの例を次に示します。

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "associations",
        "routingType": "Proxy,Cache,Extension",
        "endpoint": "https://microsoft.com/"
      }
    ]
  },
  "location": "eastus"
}
```

プロパティ | 必須 | 説明
---|---|---
name | はい | エンドポイント定義の名前。 リソースのオンボードの場合、名前は "associations" である必要があります。
routingType | はい | エンドポイントとのコントラクトの種類を決定します。 リソースのオンボードの場合、有効な **routingTypes** は "Proxy,Cache,Extension" および "Webhook,Cache,Extension" です。
endpoint | はい | 要求をルーティングするエンドポイント。 これにより、応答と、要求の副作用がすべて処理されます。

associations のリソースの種類でカスタム プロバイダーを作成すると、Microsoft.CustomProviders/associations を使用してターゲットを指定できます。 Microsoft.CustomProviders/associations は、他のすべての Azure リソースを拡張できる拡張リソースです。 Microsoft.CustomProviders/associations のインスタンスが作成されると、それは **targetResourceId** プロパティを取得します。これは、有効な Microsoft.CustomProviders/resourceProviders または Microsoft.Solutions/applications リソース ID である必要があります。 このような場合、要求は、作成した Microsoft.CustomProviders/resourceProviders インスタンスの associations のリソースの種類に転送されます。

> [!NOTE]
> Microsoft.Solutions/applications リソース ID が **targetResourceId** として指定されている場合は、"public" という名前の管理対象リソース グループにデプロイされた Microsoft.CustomProviders/resourceProviders が必要です。

Azure カスタム プロバイダーの関連付けの例:

```JSON
{
  "properties": {
    "targetResourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
    ...
  }
}
```

プロパティ | 必須 | 説明
---|---|---
targetResourceId | はい | Microsoft.CustomProviders/resourceProviders または Microsoft.Solutions/applications のリソース ID。

## <a name="how-to-use-resource-onboarding"></a>リソースのオンボードの使用方法

リソースのオンボードは、Microsoft.CustomProviders/associations 拡張リソースで他のリソースを拡張することによって機能します。 次の例では、要求は仮想マシンに対して行われますが、どのリソースも拡張できます。

まず、associations リソースの種類を持つカスタム プロバイダー リソースを作成する必要があります。 これにより、カスタム プロバイダーをターゲットとする、対応する Microsoft.CustomProviders/associations リソースが作成されたときに使用されるコールバック URL が宣言されます。

Microsoft.CustomProviders/resourceProviders の作成要求の例:

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
  "properties": {
    "resourceTypes": [
      {
        "name": "associations",
        "routingType": "Proxy,Cache,Extension",
        "endpoint": "https://{myCustomEndpoint}/"
      }
    ]
  },
  "location": "{location}"
}
```

カスタム プロバイダーを作成したら、他のリソースをターゲットにして、カスタム プロバイダーの副作用をそれらに適用できます。

Microsoft.CustomProviders/associations の作成要求の例:

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}/providers/Microsoft.CustomProviders/associations/{associationName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
  "properties": {
    "targetResourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
  }
}
```

この要求は、作成したカスタム プロバイダーで指定したエンドポイントに転送されます。これは、次の形式で **targetResourceId** によって参照されます。

``` HTTP
PUT https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/associations/{associationName}
X-MS-CustomProviders-ExtensionPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}/providers/Microsoft.CustomProviders/associations/{associationName}
X-MS-CustomProviders-ExtendedResource: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}

{
  "properties": {
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
  }
}
```

エンドポイントは、application/json `Content-Type` と有効な JSON 応答本文で応答する必要があります。 JSON の **properties** オブジェクトの下で返されるフィールドは、関連付けの返される応答に追加されます。

## <a name="getting-help"></a>ヘルプの表示

Azure カスタム リソース プロバイダーの開発に関する質問がある場合、[Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers) で確認してみてください。 同様の質問が既に回答されていることもあるため、投稿する前にまず確認してください。 ```azure-custom-providers``` タグを追加して早く応答を受け取りましょう。

## <a name="next-steps"></a>次のステップ

この記事では、カスタム プロバイダーについて学習しました。 詳細については、次の記事を参照してください。

- [チュートリアル:カスタム プロバイダーを使用したリソースのオンボード](./tutorial-resource-onboarding.md)
- [チュートリアル:Azure でカスタム アクションとカスタム リソースを作成する](./tutorial-get-started-with-custom-providers.md)
- [クイック スタート: カスタム リソース プロバイダーの作成とカスタム リソースのデプロイ](./create-custom-provider.md)
- [方法: カスタム アクションを Azure REST API に追加する](./custom-providers-action-endpoint-how-to.md)
- [方法: カスタム リソースを Azure REST API に追加する](./custom-providers-resources-endpoint-how-to.md)
