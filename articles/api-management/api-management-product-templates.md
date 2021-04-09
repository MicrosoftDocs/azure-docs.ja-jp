---
title: Azure API Management の製品テンプレート | Microsoft Docs
description: Azure API Management で開発者ポータルの製品ページの内容をカスタマイズする方法について説明します。
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 49f9254c-4c5f-4ed4-9c8d-798f44e805ee
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 4c8cd4aa3e91c5d69c40e47683818ed8bc9be338
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86249905"
---
# <a name="product-templates-in-azure-api-management"></a>Azure API Management の製品テンプレート

Azure API Management には、開発者ポータルの各ページの内容を、内容を構成するテンプレート セットを使用してカスタマイズする機能があります。 [DotLiquid](http://dotliquidmarkup.org/) 構文、好みのエディター ([DotLiquid for Designers](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers)など)、および用意されているローカライズされた[文字列リソース](api-management-template-resources.md#strings)、[グリフ リソース](api-management-template-resources.md#glyphs)、および[ページ コントロール](api-management-page-controls.md)のセットをテンプレートで使用して、表示されるページの内容を自由に構成できます。  
  
 このセクションのテンプレートを使用して、開発者ポータルの製品ページの内容をカスタマイズできます。  
  
-   [製品リスト](#ProductList)  
  
-   [Product](#Product)  
  
> [!NOTE]
>  このドキュメントには既定のテンプレートのサンプルが含まれていますが、それらは継続的な改善に伴って変更される可能性があります。 開発者ポータルで目的の個々のテンプレートに移動することで、最新の既定のテンプレートを表示できます。 テンプレートの操作方法の詳細については、[テンプレートを使用して API Management 開発者ポータルをカスタマイズする方法](./api-management-developer-portal-templates.md)に関するページを参照してください。  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="product-list"></a><a name="ProductList"></a> 製品リスト  
 **製品リスト** テンプレートを使用すると、開発者ポータルで製品リスト ページの本文をカスタマイズすることができます。  
  
 ![Product 一覧](./media/api-management-product-templates/APIM_ProductsListTemplatePage.png "APIM_ProductsListTemplatePage")  
  
### <a name="default-template"></a>既定のテンプレート  
  
```xml  
<search-control></search-control>  
<div class="row">  
    <div class="col-md-9">  
        <h2>{% localized "ProductsStrings|PageTitleProducts" %}</h2>  
    </div>  
</div>  
<div class="row">  
    <div class="col-md-12">  
    {% if products.size > 0 %}  
    <ul class="list-unstyled">  
    {% for product in products %}  
        <li>  
            <h3><a href="/products/{{product.id}}">{{product.title}}</a></h3>  
            {{product.description}}  
        </li>     
    {% endfor %}  
    </ul>  
    <paging-control></paging-control>  
    {% else %}  
    {% localized "CommonResources|NoItemsToDisplay" %}  
    {% endif %}  
    </div>  
</div>  
```  
  
### <a name="controls"></a>コントロール  
 `Product list` テンプレートでは、次の[ページ コントロール](api-management-page-controls.md)を使用できます。  
  
-   [paging-control](api-management-page-controls.md#paging-control)  
  
-   [search-control](api-management-page-controls.md#search-control)  
  
### <a name="data-model"></a>データ モデル  
  
|プロパティ|Type|説明|  
|--------------|----------|-----------------|  
|Paging|[Paging](api-management-template-data-model-reference.md#Paging) エンティティ。|製品コレクションのページング情報。|  
|フィルター処理|[フィルター処理](api-management-template-data-model-reference.md#Filtering)エンティティ。|製品リスト ページのフィルター処理情報。|  
|製品|[製品](api-management-template-data-model-reference.md#Product)エンティティのコレクション。|現在のユーザーに表示される製品。|  
  
### <a name="sample-template-data"></a>サンプル テンプレート データ  
  
```json  
{  
    "Paging": {  
        "Page": 1,  
        "PageSize": 10,  
        "TotalItemCount": 2,  
        "ShowAll": false,  
        "PageCount": 1  
    },  
    "Filtering": {  
        "Pattern": null,  
        "Placeholder": "Search products"  
    },  
    "Products": [  
        {  
            "Id": "56f9445ffaf7560049060001",  
            "Title": "Starter",  
            "Description": "Subscribers will be able to run 5 calls/minute up to a maximum of 100 calls/week.",  
            "Terms": "",  
            "ProductState": 1,  
            "AllowMultipleSubscriptions": false,  
            "MultipleSubscriptionsCount": 1  
        },  
        {  
            "Id": "56f9445ffaf7560049060002",  
            "Title": "Unlimited",  
            "Description": "Subscribers have completely unlimited access to the API. Administrator approval is required.",  
            "Terms": null,  
            "ProductState": 1,  
            "AllowMultipleSubscriptions": false,  
            "MultipleSubscriptionsCount": 1  
        }  
    ]  
}  
```  
  
##  <a name="product"></a><a name="Product"></a> Product  
 **Product** テンプレートを使用すると、開発者ポータルで製品 ページの本文をカスタマイズすることができます。  
  
 ![開発者ポータルの製品ページ](./media/api-management-product-templates/APIM_ProductPage.png "APIM_ProductPage")  
  
### <a name="default-template"></a>既定のテンプレート  
  
```xml  
<h2>{{Product.Title}}</h2>  
<p>{{Product.Description}}</p>  
  
{% assign replaceString0 = '{0}' %}  
  
{% if Limits and Limits.size > 0 %}  
<h3>{% localized "ProductDetailsStrings|WebProductsUsageLimitsHeader"%}</h3>  
<ul>  
  {% for limit in Limits %}  
  <li>{{limit.DisplayName}}</li>  
  {% endfor %}  
</ul>  
{% endif %}  
  
{% if apis.size > 0 %}  
<p>  
  <b>  
    {% if apis.size == 1 %}  
    {% capture apisCountText %}{% localized "ProductDetailsStrings|TextblockSingleApisCount" %}{% endcapture %}  
    {% else %}  
    {% capture apisCountText %}{% localized "ProductDetailsStrings|TextblockMultipleApisCount" %}{% endcapture %}  
    {% endif %}  
  
    {% capture apisCount %}{{apis.size}}{% endcapture %}  
    {{ apisCountText | replace : replaceString0, apisCount }}  
  </b>  
</p>  
  
<ul>  
  {% for api in Apis %}  
  <li>  
    <a href="/docs/services/{{api.Id}}">{{api.Name}}</a>  
  </li>  
  {% endfor %}  
</ul>  
{% endif %}  
  
{% if subscriptions.size > 0 %}  
<p>  
  <b>  
    {% if subscriptions.size == 1 %}  
    {% capture subscriptionsCountText %}{% localized "ProductDetailsStrings|TextblockSingleSubscriptionsCount" %}{% endcapture %}  
    {% else %}  
    {% capture subscriptionsCountText %}{% localized "ProductDetailsStrings|TextblockMultipleSubscriptionsCount" %}{% endcapture %}  
    {% endif %}  
  
    {% capture subscriptionsCount %}{{subscriptions.size}}{% endcapture %}  
    {{ subscriptionsCountText | replace : replaceString0, subscriptionsCount }}  
  </b>  
</p>  
  
<ul>  
  {% for subscription in subscriptions %}  
  <li>  
    <a href="/developer#{{subscription.Id}}">{{subscription.DisplayName}}</a>  
  </li>  
  {% endfor %}  
</ul>  
{% endif %}  
{% if CannotAddBecauseSubscriptionNumberLimitReached %}  
<b>{% localized "ProductDetailsStrings|TextblockSubscriptionLimitReached" %}</b>  
{% elsif CannotAddBecauseMultipleSubscriptionsNotAllowed == false %}  
<subscribe-button></subscribe-button>  
{% endif %}  
```  
  
### <a name="controls"></a>コントロール  
 `Product list` テンプレートでは、次の[ページ コントロール](api-management-page-controls.md)を使用できます。  
  
-   [subscribe-button](api-management-page-controls.md#subscribe-button)  
  
### <a name="data-model"></a>データ モデル  
  
|プロパティ|Type|説明|  
|--------------|----------|-----------------|  
|Product|[Product](api-management-template-data-model-reference.md#Product)|指定された製品。|  
|IsDeveloperSubscribed|boolean|現在のユーザーがこの製品をサブスクライブしているかどうか。|  
|SubscriptionState|number|サブスクリプションの状態。 次の状態があります。<br /><br /> -   `0 - suspended` – サブスクリプションがブロックされています。サブスクライバーは製品の API を呼び出すことができません。<br />-   `1 - active` – サブスクリプションがアクティブです。<br />-   `2 - expired` – サブスクリプションの期限になり、サブスクリプションが非アクティブ化されました。<br />-   `3 - submitted` – サブスクリプション要求が開発者によって行われましたが、まだ承認または拒否されていません。<br />-   `4 - rejected` – サブスクリプション要求が管理者によって拒否されました。<br />-   `5 - cancelled` – サブスクリプションが開発者または管理者によって取り消されました。|  
|制限|array|このプロパティは非推奨です。使用しないでください。|  
|DelegatedSubscriptionEnabled|boolean|このサブスクリプションで[委任](./api-management-howto-setup-delegation.md)が有効かどうか。|  
|DelegatedSubscriptionUrl|string|委任が有効になっている場合、委任されたサブスクリプションの URL。|  
|IsAgreed|boolean|製品に条件がある場合、現在のユーザーがその条件に同意したかどうか。|  
|サブスクリプション|[サブスクリプションの概要](api-management-template-data-model-reference.md#SubscriptionSummary)エンティティのコレクション。|製品のサブスクリプション。|  
|Apis|[API](api-management-template-data-model-reference.md#API) エンティティのコレクション。|この製品の API。|  
|CannotAddBecauseSubscriptionNumberLimitReached|boolean|サブスクリプション数制限に関して、現在のユーザーがこの製品をサブスクライブできるかどうか。|  
|CannotAddBecauseMultipleSubscriptionsNotAllowed|boolean|複数サブスクリプションの可否に関して、現在のユーザーがこの製品をサブスクライブできるかどうか。|  
  
### <a name="sample-template-data"></a>サンプル テンプレート データ  
  
```json  
{  
    "Product": {  
        "Id": "56f9445ffaf7560049060001",  
        "Title": "Starter",  
        "Description": "Subscribers will be able to run 5 calls/minute up to a maximum of 100 calls/week.",  
        "Terms": "",  
        "ProductState": 1,  
        "AllowMultipleSubscriptions": false,  
        "MultipleSubscriptionsCount": 1  
    },  
    "IsDeveloperSubscribed": true,  
    "SubscriptionState": 1,  
    "Limits": [],  
    "DelegatedSubscriptionEnabled": false,  
    "DelegatedSubscriptionUrl": null,  
    "IsAgreed": false,  
    "Subscriptions": [  
        {  
            "Id": "56f9445ffaf7560049070001",  
            "DisplayName": "Starter  (default)"  
        }  
    ],  
    "Apis": [  
        {  
            "id": "56f9445ffaf7560049040001",  
            "name": "Echo API",  
            "description": null,  
            "serviceUrl": "http://echoapi.cloudapp.net/api",  
            "path": "echo",  
            "protocols": [  
                2  
            ],  
            "authenticationSettings": null,  
            "subscriptionKeyParameterNames": null  
        }  
    ],  
    "CannotAddBecauseSubscriptionNumberLimitReached": false,  
    "CannotAddBecauseMultipleSubscriptionsNotAllowed": true  
}  
```

## <a name="next-steps"></a>次のステップ
テンプレートの操作方法の詳細については、[テンプレートを使用して API Management 開発者ポータルをカスタマイズする方法](api-management-developer-portal-templates.md)に関するページを参照してください。
