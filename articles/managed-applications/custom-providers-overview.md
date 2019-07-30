---
title: Azure カスタム リソース プロバイダーの概要
description: Azure カスタム リソース プロバイダーについて、および自分のワークフローに合うように Azure API プレーンを拡張する方法について学習します。
author: jjbfour
ms.service: managed-applications
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: d25ef00adc307bae57da2c04d4472874f8d67bcd
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67796078"
---
# <a name="azure-custom-resource-providers-overview"></a>Azure カスタム リソース プロバイダーの概要

Azure カスタム リソース プロバイダーは、Azure に対する拡張プラットフォームです。 これによって、既定の Azure エクスペリエンスを強化するために使用できるカスタム API を定義できます。 このドキュメントでは次のことを説明します。

- Azure カスタム リソース プロバイダーを構築してデプロイする方法
- Azure カスタム リソース プロバイダーを使用して既存のワークフローを拡張する方法
- 作業を開始するためのガイドとコード サンプルを見つけられる場所

![カスタム プロバイダーの概要](./media/custom-providers-overview/overview.png)

> [!IMPORTANT]
> 現在、Custom Providers はパブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="what-can-custom-resource-providers-do"></a>カスタム リソース プロバイダーで実行できること

Azure カスタム リソース プロバイダーで実現できるいくつかの例を次に示します。

- Azure Resource Manager REST API を拡張して内部サービスと外部サービスを含める
- 既存の Azure ワークフローの上でカスタム シナリオを有効にする
- Azure Resource Manager テンプレートのコントロールと効果をカスタマイズする

## <a name="what-is-a-custom-resource-provider"></a>カスタム リソース プロバイダーとは

Azure カスタム リソース プロバイダーは、Azure とエンドポイントの間の契約を作成することで作られます。 この契約では、新しいリソース (**Microsoft.CustomProviders/resourceProviders**) から新しいリソースとアクションの一覧を定義します。 その後、カスタム リソース プロバイダーによって、Azure でこれらの新しい API が公開されます。 Azure カスタム リソース プロバイダーは、次の 3 つのパーツで構成されます。カスタム リソース プロバイダー、**エンドポイント**、カスタム リソースです。

## <a name="how-to-build-custom-resource-providers"></a>カスタム リソース プロバイダーを構築する方法

カスタム リソース プロバイダーは、Azure とエンドポイントの間の契約の一覧です。 この契約では、Azure でエンドポイントとやりとりする方法について説明します。 リソース プロバイダーはプロキシのように動作し、指定した**エンドポイント**に対して要求と応答を転送します。 リソース プロバイダーは、2 種類の契約 ([**resourceTypes**](./custom-providers-resources-endpoint-how-to.md) と [**actions**](./custom-providers-action-endpoint-how-to.md)) を指定できます。 これらはエンドポイント定義によって有効にされます。 エンドポイント定義は、次の 3 つのフィールドで構成されます: **name**、**routingType**、**endpoint**。

サンプルのエンドポイント:

```JSON
{
  "name": "{endpointDefinitionName}",
  "routingType": "Proxy",
  "endpoint": "https://{endpointURL}/"
}
```

プロパティ | 必須 | 説明
---|---|---
名前 | *はい* | エンドポイント定義の名前。 Azure では、この名前は '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/<br>resourceProviders/{resourceProviderName}/{endpointDefinitionName}' の下にあるその API によって公開されます。
routingType | *いいえ* | **エンドポイント**で契約の種類を決定します。 指定されていない場合、これは既定で "Proxy" になります。
endpoint | *はい* | 要求をルーティングするエンドポイント。 これにより、応答と要求の副作用がすべて処理されます。

### <a name="building-custom-resources"></a>カスタム リソースを構築する

**ResourceTypes** では、Azure に追加される新しいカスタム リソースについて説明します。 これらにより、基本の RESTful CRUD メソッドが公開されます。 [カスタム リソースの作成の詳細](./custom-providers-resources-endpoint-how-to.md)を確認してください。

**resourceTypes** を含むサンプルのカスタム リソース プロバイダー:

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "myCustomResources",
        "routingType": "Proxy",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus"
}
```

上記のサンプル用の Azure に追加される API:

HttpMethod | サンプル URI | 説明
---|---|---
PUT | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}?api-version=2018-09-01-preview | 新しいリソースを作成するための Azure REST API の呼び出しです。
DELETE | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}?api-version=2018-09-01-preview | 既存のリソースを削除するための Azure REST API の呼び出しです。
GET | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}?api-version=2018-09-01-preview | 既存のリソースを取得するための Azure REST API の呼び出しです。
GET | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources?api-version=2018-09-01-preview | 既存のリソースの一覧を取得するための Azure REST API の呼び出しです。

### <a name="building-custom-actions"></a>カスタム アクションを構築する

**Actions** では Azure に追加される新しいアクションについて説明します。 これらはリソース プロバイダーの上で公開するか、**resourceType** の下で入れ子にすることができます。 [カスタム アクションの作成の詳細](./custom-providers-action-endpoint-how-to.md)を確認してください。

**actions** を含むサンプルのカスタム リソース プロバイダー:

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "myCustomAction",
        "routingType": "Proxy",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus"
}
```

上記のサンプル用の Azure に追加される API:

HttpMethod | サンプル URI | 説明
---|---|---
POST | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomAction?api-version=2018-09-01-preview | アクションをアクティブ化するための Azure REST API の呼び出しです。

## <a name="looking-for-help"></a>ヘルプを探しています

Azure カスタム リソース プロバイダーの開発に関する質問がある場合、[Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers) で確認してみてください。 同様の質問が既に質問され回答されているため、投稿する前にまず確認してください。 ```azure-custom-providers``` タグを追加して早く応答を受け取りましょう。

## <a name="next-steps"></a>次の手順

この記事では、カスタム プロバイダーについて学習しました。 次の記事にアクセスしてカスタム プロバイダーを作成しましょう。

- [クイック スタート:Azure カスタム リソースプロバイダーの作成とカスタム リソースのデプロイ](./create-custom-provider.md)
- [チュートリアル:Azure でカスタム アクションとカスタム リソースを作成する](./tutorial-custom-providers-101.md)
- [方法:カスタム アクションを Azure REST API に追加する](./custom-providers-action-endpoint-how-to.md)
- [方法:カスタム リソースを Azure REST API に追加する](./custom-providers-resources-endpoint-how-to.md)
